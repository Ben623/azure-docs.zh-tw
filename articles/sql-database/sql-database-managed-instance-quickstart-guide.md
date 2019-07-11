---
title: 快速入門 - Azure SQL Database 受控執行個體 | Microsoft Docs
description: 了解如何快速地開始使用 Azure SQL Database - 受控執行個體
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: quickstart
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein, carlr
manager: craigg
ms.date: 03/25/2019
ms.openlocfilehash: ec0007e2d53a3fd3cae158375b696379d923b4b3
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/28/2019
ms.locfileid: "67447777"
---
# <a name="getting-started-with-azure-sql-database-managed-instance"></a>開始使用 Azure SQL Database 受控執行個體

[受控執行個體](sql-database-managed-instance-index.yml)是建立資料庫的部署選項，幾乎可與最新的 SQL Server 內部部署環境 (Enterprise Edition) 資料庫引擎 100% 相容，並提供原生[虛擬網路 (VNet)](../virtual-network/virtual-networks-overview.md) 實作可因應常見的安全性考量，以及提供有利於內部部署 SQL Server 客戶的[商務模型](https://azure.microsoft.com/pricing/details/sql-database/)。 在本文中，您將了解如何快速地設定和建立受控執行個體，並移轉您的資料庫。

## <a name="quickstart-overview"></a>快速入門概觀

下列快速入門可讓您快速建立受控執行個體、為用戶端應用程式設定虛擬機器或點對站 VPN 連線，以及使用 `.bak` 檔案將資料庫還原至新的受控執行個體。

### <a name="configure-environment"></a>設定環境

首先，您必須建立第一個受控執行個體及其放置所在的網路環境，並啟用從您執行查詢的電腦或虛擬機器到受控執行個體的連線。 您可以使用下列指南：

- [使用 Azure 入口網站建立受控執行個體](sql-database-managed-instance-get-started.md)。 在 Azure 入口網站中，您可以設定必要參數 (使用者名稱/密碼、核心數目和儲存體數量上限)，並自動建立 Azure 網路環境，而無需了解網路詳細資料和基礎結構需求。 您只需確定自己具有目前允許用來建立受控執行個體的[訂用帳戶類型](sql-database-managed-instance-resource-limits.md#supported-subscription-types)。 如果您想要使用自己的網路，或想要自訂網路，請參閱[針對 Azure SQL Database 受控執行個體設定現有虛擬網路](sql-database-managed-instance-configure-vnet-subnet.md)或[建立 Azure SQL Database 受控執行個體的虛擬網路](sql-database-managed-instance-create-vnet-subnet.md)。
- 受控執行個體會建立在沒有公用端點的自有 VNet 中。 針對用戶端應用程式的存取，您可以**在相同 VNet (不同子網路) 中建立 VM**，或使用以下其中一個快速入門來**建立用戶端電腦到 VNet 的點對站 VPN 連線**：

  - [在受控執行個體 VNet 中建立 Azure 虛擬機器](sql-database-managed-instance-configure-vm.md)來進行用戶端應用程式連線，包括 SQL Server Management Studio。
  - 從具有 SQL Server Management Studio 和其他用戶端連線應用程式的用戶端電腦中，[對您的受控執行個體設定點對站 VPN 連線](sql-database-managed-instance-configure-p2s.md)。 這是其他兩個可連線到您受控執行個體和其 VNet 的選項。

  > [!NOTE]
  > 您也可以從區域網路使用 ExpressRoute 或站對站連線，但這些方法並不在這些快速入門所涵蓋的範圍內。

### <a name="migrate-your-databases"></a>移轉資料庫

在建立受控執行個體並設定存取後，您即可開始從 SQL Server 內部部署或 Azure VM 移轉資料庫。 如果您想要遷移的來源資料庫具有不支援的功能，移轉將會失敗。 若要避免失敗並檢查相容性，您可以安裝 [Data Migration Assistant (DMA)](https://www.microsoft.com/download/details.aspx?id=53595) \(英文\)，其會分析您位於 SQL Server 上的資料庫，並找出可能會防止移轉到受控執行個體的所有問題 (例如存在 [Filestream](https://docs.microsoft.com/sql/relational-databases/blob/filestream-sql-server) 或多個記錄檔)。 如果您解決這些問題，您的資料庫便已準備好遷移至受控執行個體。 [資料庫測試助理](https://blogs.msdn.microsoft.com/datamigration/2018/08/06/release-database-experimentation-assistant-dea-v2-6/) \(英文\) 是另一個有用的工具，其可記錄您在 SQL Server 上的工作負載，並在受控執行個體上重新播放它，讓您可以判斷在遷移到受控執行個體之後是否會發生任何效能問題。

一旦您確定可以將資料庫遷移至受控執行個體後，即可使用原生的 SQL Server 還原功能，從 `.bak` 檔案將資料庫還原到受控執行個體。 您可以使用此方法，從安裝內部部署或 Azure VM 的 SQL Server 資料庫引擎移轉資料庫。 如需快速入門，請參閱[從備份還原至受控執行個體](sql-database-managed-instance-get-started-restore.md)。 在此快速入門中，您將使用 `RESTORE` Transact-SQL 命令，從儲存在 Azure Blob 儲存體中的 `.bak` 檔案進行還原。

> [!TIP]
> 若要使用 `BACKUP` TRANSACT-SQL 命令來為 Azure Blob 儲存體中的資料庫建立備份，請參閱 [SQL Server 備份至 URL](https://docs.microsoft.com/sql/relational-databases/backup-restore/sql-server-backup-to-url)。

這些快速入門可讓您快速地對受控執行個體建立、設定及還原資料庫備份。 在某些情況下，您必須對受控執行個體和必要網路環境的部署進行自訂或自動化。 這些案例將於下方詳述。

## <a name="customize-network-environment"></a>自訂網路環境

雖然 VNet/子網路可以在使用 [Azure 入口網站](sql-database-managed-instance-get-started.md)建立執行個體時自動設定，您應該在開始建立受控執行個體之前建立它，以設定 VNet 和子網路的參數。 建立並設定網路環境最簡單的方式，便是使用 [Azure 資源部署](sql-database-managed-instance-create-vnet-subnet.md)範本，其會建立要放置執行個體的網路和子網路。 您只需要按下 Azure Resource Manager 部署按鈕，並將參數填入表單中。

或者，您也可以使用 [PowerShell 指令碼](https://www.powershellmagazine.com/20../../configuring-azure-environment-to-set-up-azure-sql-database-managed-instance-preview/) \(英文\) 來將網路的建立自動化。

或者，您也可以使用此 [PowerShell 指令碼](https://www.powershellmagazine.com/2018/07/23/configuring-azure-environment-to-set-up-azure-sql-database-managed-instance-preview/) \(英文\) 來將網路的建立自動化。

如果您已經有要部署受控執行個體的 VNet 和子網路，您必須確定您的 VNet 和子網路皆能滿足[網路需求](sql-database-managed-instance-connectivity-architecture.md#network-requirements)。 使用這個 [PowerShell 指令碼來確認子網路已正確設定](sql-database-managed-instance-configure-vnet-subnet.md)。 此指令碼會確認網路並回報問題，還會告訴您應該變更的項目，然後在 VNet/子網路中做出必要的變更。 如果您不想要手動設定 VNet/子網路，請執行此指令碼。 您也可以在大幅地重新設定網路基礎結構後，執行此指令碼。 如果您要建立及設定您自己的網路，請參閱[連線架構](sql-database-managed-instance-connectivity-architecture.md)，以及這篇[建立及設定受控執行個體環境的終極指南](https://medium.com/azure-sqldb-managed-instance/the-ultimate-guide-for-creating-and-configuring-azure-sql-managed-instance-environment-91ff58c0be01)。

## <a name="automating-creation-of-a-managed-instance"></a>將受控執行個體的建立自動化

 如果您尚未依照上個步驟中的指示建立網路環境，Azure 入口網站可以代您進行。此作法唯一的缺點在於系統會以預設參數設定網路環境，且您無法於稍後變更這些參數。 或者，您可以使用：

- [PowerShell](scripts/sql-database-create-configure-managed-instance-powershell.md)
- [搭配 Resource Manager 範本使用 PowerShell](scripts/sql-managed-instance-create-powershell-azure-resource-manager-template.md)
- [Azure CLI](https://blogs.msdn.microsoft.com/sqlserverstorageengine/2018/11/14/create-azure-sql-managed-instance-using-azure-cli/)。
- [Resource Manager 範本](sql-database-single-database-get-started-template.md)

## <a name="migrating-to-a-managed-instance-with-minimal-downtime"></a>以最短停機時間遷移到受控執行個體

這些快速入門中的文章可讓您快速地設定受控執行個體，並使用原生的 `RESTORE` 功能來移動您的資料庫。 不過，若使用原生的 `RESTORE`，您必須等待資料庫進行還原並複製到 Azure Blob 儲存體 (如果資料庫尚未儲存在該處)。 這會導致您的應用程式有停機時間，特別是處理較大型資料庫的時候。 若要移動您的生產資料庫，請使用[資料移轉服務 (DMS)](https://docs.microsoft.com/azure/dms/tutorial-sql-server-to-managed-instance?toc=/azure/sql-database/toc.json)，才能以最短的停機時間來遷移資料庫。 DMS 會以累加方式將來源資料庫中的變更推送到正在還原的受控執行個體資料庫，藉此來完成移轉。 透過此方法，您可以在最短停機時間的前提下，快速地將應用程式從來源資料庫切換到目標資料庫。

## <a name="next-steps"></a>後續步驟

- 在[這裡取得受控執行個體中已支援功能的高階清單](sql-database-features.md)，並在[這裡取得詳細資料和已知問題](sql-database-managed-instance-transact-sql-information.md)。
- 了解[受控執行個體的技術特性](sql-database-managed-instance-resource-limits.md#instance-level-resource-limits)。
- 如需更進階的操作說明，請參閱[如何在 Azure SQL Database 中使用受控執行個體](sql-database-howto-managed-instance.md)。
- [為您的內部部署資料庫識別正確的 Azure SQL Database/受控執行個體 SKU](/sql/dma/dma-sku-recommend-sql-db/)。