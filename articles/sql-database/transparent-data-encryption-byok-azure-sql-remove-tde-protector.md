---
title: 移除 TDE 保護裝置-PowerShell
description: 本操作指南說明 Azure SQL Database 或資料倉儲若是使用具有「攜帶您自己的金鑰」(BYOK) 支援的 TDE，在其 TDE 保護裝置可能遭破壞時，應如何因應。
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: seo-lt-2019
ms.devlang: ''
ms.topic: conceptual
author: jaszymas
ms.author: jaszymas
ms.reviewer: vanto
ms.date: 03/12/2019
ms.openlocfilehash: ba591872f4f8af93e5f7e13e0fb69d0679fd1a0c
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/15/2020
ms.locfileid: "75965756"
---
# <a name="remove-a-transparent-data-encryption-tde-protector-using-powershell"></a>使用 PowerShell 移除透明資料加密 (TDE) 保護裝置

## <a name="prerequisites"></a>必要條件

- 您必須具有 Azure 訂用帳戶，並且是該訂用帳戶的系統管理員
- 您必須已安裝且正在執行 Azure PowerShell。
- 本操作指南假設您已使用 Azure Key Vault 中的金鑰作為 Azure SQL Database 或資料倉儲的 TDE 保護裝置。 若要深入了解，請參閱[具有 BYOK 支援的透明資料加密](transparent-data-encryption-byok-azure-sql.md)。

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

 如需 Az 模組安裝指示，請參閱[安裝 Azure PowerShell](/powershell/azure/install-az-ps)。 如需特定的 Cmdlet，請參閱[AzureRM](https://docs.microsoft.com/powershell/module/AzureRM.Sql/)。

> [!IMPORTANT]
> Azure SQL Database 仍然支援 PowerShell Azure Resource Manager （RM）模組，但所有未來的開發都是針對 Az .Sql 模組。 AzureRM 模組會繼續收到錯誤修正，直到2020年12月為止。  Az 模組和 AzureRm 模組中命令的引數本質上完全相同。 如需其相容性的詳細資訊，請參閱[新的 Azure PowerShell Az 模組簡介](/powershell/azure/new-azureps-module-az)。

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

如需安裝，請參閱[安裝 Azure CLI](/cli/azure/install-azure-cli)。

* * *

## <a name="overview"></a>概觀

本操作指南說明 Azure SQL Database 或資料倉儲若是在 Azure Key Vault 中搭配由客戶管理的金鑰來使用 TDE (攜帶您自己的金鑰 (BYOK) 支援)，在其 TDE 保護裝置可能遭破壞時，應如何因應。 若要深入了解 TDE 的 BYOK 支援，請參閱[概觀頁面](transparent-data-encryption-byok-azure-sql.md)。

只有在極端情況下或測試環境中，才需要執行下列程序。 請仔細檢閱本操作指南，因為從 Azure Key Vault 中刪除正在使用的 TDE 保護裝置，可能會導致**資料遺失**。

如果金鑰疑似遭到破壞，而使服務或使用者可在未經授權的情況下存取金鑰，則最好將金鑰刪除。

請切記，在 Key Vault 中刪除 TDE 保護裝置後，**系統會封鎖該伺服器下所有與已加密資料庫的連線；這些資料庫會離線，並在 24 小時內遭到捨棄**。 以遭破壞的金鑰加密的舊備份，將不再可供存取。

下列步驟概述如何檢查指定資料庫的虛擬記錄檔（VLF）仍在使用的 TDE 保護裝置指紋。
資料庫目前 TDE 保護裝置的指紋，而且可以藉由執行下列程式來找到資料庫識別碼：

```sql
SELECT [database_id],
       [encryption_state],
       [encryptor_type], /*asymmetric key means AKV, certificate means service-managed keys*/
       [encryptor_thumbprint],
 FROM [sys].[dm_database_encryption_keys]
```

下列查詢會傳回使用中的 Vlf 和加密器的個別指紋。 每個不同的指紋會參照 Azure Key Vault 中的不同金鑰（AKV）：

```sql
SELECT * FROM sys.dm_db_log_info (database_id)
```

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

PowerShell 命令**add-azurermsqlserverkeyvaultkey** 會提供用於查詢的 TDE 保護裝置指紋，讓您可以查看要保留哪些金鑰，以及要在 AKV 中刪除哪些金鑰。 只有資料庫不再使用的金鑰可以安全地從 Azure Key Vault 中刪除。

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

PowerShell 命令**az sql server key show** 提供查詢中所使用之 TDE 保護裝置的指紋，讓您可以查看要保留哪些金鑰，以及要在 AKV 中刪除哪些金鑰。 只有資料庫不再使用的金鑰可以安全地從 Azure Key Vault 中刪除。

* * *

本操作指南會根據事件回應之後的所需結果討論兩種方法：

- 將 Azure SQL 資料庫/資料倉儲保持為**可供存取**
- 使 Azure SQL 資料庫/資料倉儲變成**無法存取**

## <a name="to-keep-the-encrypted-resources-accessible"></a>將加密資源保持為可供存取

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

1. 建立 [Key Vault 中的新金鑰](/powershell/module/az.keyvault/add-azkeyvaultkey)。 請務必將此新金鑰建立在與可能遭破壞的 TDE 保護裝置不同的金鑰保存庫中，因為存取控制佈建於保存庫層級上。

2. 使用[AzSqlServerKeyVaultKey](/powershell/module/az.sql/add-azsqlserverkeyvaultkey)和[AzSqlServerTransparentDataEncryptionProtector](/powershell/module/az.sql/set-azsqlservertransparentdataencryptionprotector) Cmdlet 將新的金鑰新增至伺服器，並將其更新為伺服器的新 TDE 保護裝置。

   ```powershell
   # add the key from Key Vault to the server  
   Add-AzSqlServerKeyVaultKey -ResourceGroupName <SQLDatabaseResourceGroupName> -ServerName <LogicalServerName> -KeyId <KeyVaultKeyId>

   # set the key as the TDE protector for all resources under the server
   Set-AzSqlServerTransparentDataEncryptionProtector -ResourceGroupName <SQLDatabaseResourceGroupName> `
       -ServerName <LogicalServerName> -Type AzureKeyVault -KeyId <KeyVaultKeyId>
   ```

3. 請使用[AzSqlServerTransparentDataEncryptionProtector](/powershell/module/az.sql/get-azsqlservertransparentdataencryptionprotector) Cmdlet，確定伺服器和任何複本已更新為新的 TDE 保護裝置。

   > [!NOTE]
   > 新的 TDE 保護裝置可能需要幾分鐘的時間才能傳播至伺服器下的所有資料庫和次要資料庫。

   ```powershell
   Get-AzSqlServerTransparentDataEncryptionProtector -ServerName <LogicalServerName> -ResourceGroupName <SQLDatabaseResourceGroupName>
   ```

4. 在 Key Vault 中建立[新金鑰的備份](/powershell/module/az.keyvault/backup-azkeyvaultkey)。

   ```powershell
   # -OutputFile parameter is optional; if removed, a file name is automatically generated.
   Backup-AzKeyVaultKey -VaultName <KeyVaultName> -Name <KeyVaultKeyName> -OutputFile <DesiredBackupFilePath>
   ```

5. 使用[AzKeyVaultKey](/powershell/module/az.keyvault/remove-azkeyvaultkey) Cmdlet，從 Key Vault 刪除遭盜用的金鑰。

   ```powershell
   Remove-AzKeyVaultKey -VaultName <KeyVaultName> -Name <KeyVaultKeyName>
   ```

6. 若要在未來使用[AzKeyVaultKey](/powershell/module/az.keyvault/restore-azkeyvaultkey) Cmdlet 將金鑰還原至 Key Vault：

   ```powershell
   Restore-AzKeyVaultKey -VaultName <KeyVaultName> -InputFile <BackupFilePath>
   ```

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

如需命令參考，請參閱[Azure CLI keyvault](/cli/azure/keyvault/key)。

1. 建立 [Key Vault 中的新金鑰](/cli/azure/keyvault/key#az-keyvault-key-create)。 請務必將此新金鑰建立在與可能遭破壞的 TDE 保護裝置不同的金鑰保存庫中，因為存取控制佈建於保存庫層級上。

2. 將新的金鑰新增至伺服器，並將它更新為伺服器的新 TDE 保護裝置。

   ```powershell
   # add the key from Key Vault to the server  
   az sql server key create --kid <KeyVaultKeyId> --resource-group <SQLDatabaseResourceGroupName> --server <LogicalServerName>

   # set the key as the TDE protector for all resources under the server
   az sql server tde-key set --server-key-type AzureKeyVault --kid <KeyVaultKeyId> --resource-group <SQLDatabaseResourceGroupName> --server <LogicalServerName>
   ```

3. 請確定伺服器和任何複本已更新為新的 TDE 保護裝置。

   > [!NOTE]
   > 新的 TDE 保護裝置可能需要幾分鐘的時間才能傳播至伺服器下的所有資料庫和次要資料庫。

   ```powershell
   az sql server tde-key show --resource-group <SQLDatabaseResourceGroupName> --server <LogicalServerName>
   ```

4. 請在 Key Vault 中備份新的金鑰。

   ```powershell
   # --file parameter is optional; if removed, a file name is automatically generated.
   az keyvault key backup --file <DesiredBackupFilePath> --name <KeyVaultKeyName> --vault-name <KeyVaultName>
   ```

5. 從 Key Vault 刪除遭盜用的金鑰。

   ```powershell
   az keyvault key delete --name <KeyVaultKeyName> --vault-name <KeyVaultName>
   ```

6. 將金鑰還原至未來 Key Vault。

   ```powershell
   az keyvault key restore --file <BackupFilePath> --vault-name <KeyVaultName>
   ```

* * *

## <a name="to-make-the-encrypted-resources-inaccessible"></a>使加密資源變得無法存取

1. 捨棄以可能遭破壞的金鑰加密的資料庫。

   資料庫和記錄檔會自動備份，因此資料庫的時間點還原可以在任何時間點執行 (只要您提供金鑰)。 資料庫必須在作用中的 TDE 保護裝置刪除之前捨棄，否則最多可能會遺失最新交易 10 分鐘內的資料。

2. 在 Key Vault 中備份 TDE 保護裝置的金鑰內容。
3. 從 Key Vault 中移除可能遭破壞的金鑰

## <a name="next-steps"></a>後續步驟

- 了解如何輪替伺服器的 TDE 保護裝置以符合安全需求：[使用 PowerShell 輪替透明資料加密保護裝置](transparent-data-encryption-byok-azure-sql-key-rotation.md)
- 開始將「攜帶您自己的金鑰支援」用於 TDE：[透過 PowerShell 從 Key Vault 使用您自己的金鑰開啟 TDE](transparent-data-encryption-byok-azure-sql-configure.md)
