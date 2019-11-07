---
title: 複製 Azure SQL 資料庫
description: 在同個伺服器或不同伺服器上，建立現有 Azure SQL 資料庫的交易一致性複本。
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sashan
ms.reviewer: carlrab
ms.date: 09/04/2019
ms.openlocfilehash: d49896d8088ae1352cb2785d061cde6c8647cb89
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/06/2019
ms.locfileid: "73690813"
---
# <a name="copy-a-transactionally-consistent-copy-of-an-azure-sql-database"></a>複製 Azure SQL 資料庫的交易一致性複本

Azure SQL Database 提供數種方法，可在相同伺服器或不同的伺服器上，建立現有 Azure SQL 資料庫（[單一資料庫](sql-database-single-database.md)）的交易一致複本。 若要複製 SQL Database，您可使用 Azure 入口網站、PowerShell 或 T-SQL。 

## <a name="overview"></a>概觀

資料庫複本是發生複製要求時的來源資料庫快照集。 您可以選取相同的伺服器或不同的伺服器。 此外，您也可以選擇保留其服務層級和計算大小，或在相同的服務層級（版本）中使用不同的計算大小。 複製完成之後，複本會變成功能完整的獨立資料庫。 此時，您可以將它升級或降級成任何版本。 可以個別管理登入、使用者和權限。 此複本是使用異地複寫技術所建立，一旦植入完成，異地複寫連結就會自動終止。 使用異地複寫的所有需求都適用于資料庫複製作業。 如需詳細資訊，請參閱[主動式異地複寫總覽](sql-database-active-geo-replication.md)。

> [!NOTE]
> 當您建立資料庫複本時，會使用[自動資料庫備份](sql-database-automated-backups.md)。

## <a name="logins-in-the-database-copy"></a>資料庫複本中的登入

當您將資料庫複製到相同的 SQL Database 伺服器時，可以在這兩個資料庫上使用相同的登入。 您用來複製資料庫的安全性主體會變成新資料庫的資料庫擁有者。 所有資料庫使用者、其權限及其安全性識別碼 (SID) 都會複製到資料庫副本。  

當您將資料庫複製到不同的 SQL Database 伺服器時，新伺服器上的安全性主體就會變成新資料庫上的資料庫擁有者。 如果您使用[自主資料庫使用者](sql-database-manage-logins.md)來進行資料存取，請確保主要和次要資料庫一律具有相同的使用者認證，以便在複製完成時，您可以使用相同的認證立即存取它。 

如果您使用 [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md)，則可以完全不需管理副本中的認證。 不過，當您將資料庫複製到新的伺服器時，以登入為基礎的存取可能無法運作，因為登入不存在於新的伺服器上。 若要了解如何在將資料庫複製到不同的 SQL Database 伺服器時管理登入，請參閱[如何管理災害復原後的 Azure SQL 資料庫安全性](sql-database-geo-replication-security-config.md)。 

在複製成功之後，重新對應其他使用者之前，只有起始複製的登入 (也就是資料庫擁有者) 可以登入新的資料庫。 若要在複製作業完成之後解析登入，請參閱 [解析登入](#resolve-logins)。

## <a name="copy-a-database-by-using-the-azure-portal"></a>使用 Azure 入口網站來複製資料庫

若要使用 Azure 入口網站來複製資料庫，請開啟資料庫頁面，然後按一下 [複製]。 

   ![資料庫複本](./media/sql-database-copy/database-copy.png)

## <a name="copy-a-database-by-using-powershell"></a>使用 PowerShell 來複製資料庫

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

若要使用 PowerShell 來複製資料庫，請使用[AzSqlDatabaseCopy](/powershell/module/az.sql/new-azsqldatabasecopy) Cmdlet。 

```powershell
New-AzSqlDatabaseCopy -ResourceGroupName "myResourceGroup" `
    -ServerName $sourceserver `
    -DatabaseName "MySampleDatabase" `
    -CopyResourceGroupName "myResourceGroup" `
    -CopyServerName $targetserver `
    -CopyDatabaseName "CopyOfMySampleDatabase"
```

如需完整範例指令碼，請參閱[將資料庫複製到新伺服器](scripts/sql-database-copy-database-to-new-server-powershell.md)。

資料庫複本是非同步作業，但是在接受要求之後，就會立即建立目標資料庫。 如果您需要在仍在進行時取消複製作業，請使用[set-azsqldatabase 搭配 Cmdlet 卸載](/powershell/module/az.sql/new-azsqldatabase)目標資料庫。  

## <a name="rbac-roles-to-manage-database-copy"></a>用來管理資料庫複製的 RBAC 角色

若要建立資料庫複本，您必須在下列角色中

- 訂用帳戶擁有者，或是
- SQL Server 參與者角色或
- 來源和目標資料庫上具有下列許可權的自訂角色：

   Microsoft.Sql/servers/databases/read   
   Microsoft.Sql/servers/databases/write   

若要取消資料庫複本，您必須在下列角色中

- 訂用帳戶擁有者，或是
- SQL Server 參與者角色或
- 來源和目標資料庫上具有下列許可權的自訂角色：

   Microsoft.Sql/servers/databases/read   
   Microsoft.Sql/servers/databases/write   
   
若要使用 Azure 入口網站來管理資料庫複製，您也需要下列許可權：

&nbsp; &nbsp; &nbsp; Microsoft .Resources/訂用帳戶/資源/讀取   
&nbsp; &nbsp; &nbsp; Microsoft .Resources/訂用帳戶/資源/寫入   
&nbsp; &nbsp; &nbsp; Microsoft .Resources/部署/讀取   
&nbsp; &nbsp; &nbsp; Microsoft .Resources/部署/寫入   
&nbsp; &nbsp; &nbsp; Microsoft .Resources/部署/operationstatuses/讀取    

如果您想要查看入口網站上資源群組中的 [部署] 底下的作業，跨多個資源提供者的作業（包括 SQL 作業），您將需要這些額外的 RBAC 角色： 

&nbsp; &nbsp; &nbsp; Microsoft .Resources/訂用帳戶/resourcegroups/部署/作業/讀取   
&nbsp; &nbsp; &nbsp; Microsoft .Resources/訂用帳戶/resourcegroups/部署/operationstatuses/讀取



## <a name="copy-a-database-by-using-transact-sql"></a>使用 Transact-SQL 來複製資料庫

使用伺服器層級主體登入或建立您要複製之資料庫的登入來登入 master 資料庫。 若要成功複製資料庫，不是伺服器層級主體的登入必須是 dbmanager 角色的成員。 如需登入與連接到伺服器的詳細資訊，請參閱 [管理登入](sql-database-manage-logins.md)。

使用 [CREATE DATABASE](https://msdn.microsoft.com/library/ms176061.aspx) 陳述式，開始複製來源資料庫。 執行此陳述式會起始資料庫複製程序。 因為複製資料庫是非同步程序，所以 CREATE DATABASE 陳述式會在資料庫完成複製之前傳回。

### <a name="copy-a-sql-database-to-the-same-server"></a>將 SQL Database 複製到相同伺服器

使用伺服器層級主體登入或建立您要複製之資料庫的登入來登入 master 資料庫。 若要成功複製資料庫，不是伺服器層級主體的登入必須是 dbmanager 角色的成員。

此命令會將 Database1 複製到相同伺服器上名為 Database2 的新資料庫。 視資料庫大小而定，複製作業可能需要一些時間才能完成。

    -- Execute on the master database.
    -- Start copying.
    CREATE DATABASE Database2 AS COPY OF Database1;

### <a name="copy-a-sql-database-to-a-different-server"></a>將 SQL Database 複製到不同伺服器

登入目的地伺服器的 master 資料庫，也就是即將建立新資料庫的 SQL Database 伺服器。 使用具有與來源 SQL Database 伺服器上來源資料庫的資料庫擁有者相同之名稱和密碼的登入。 目的地伺服器上的登入必須也是 dbmanager 角色的成員或是伺服器層級主體登入。

此命令會將 server1 上的 Database1 複製到 server2 上名為 Database2 的新資料庫。 視資料庫大小而定，複製作業可能需要一些時間才能完成。

    -- Execute on the master database of the target server (server2)
    -- Start copying from Server1 to Server2
    CREATE DATABASE Database2 AS COPY OF server1.Database1;
    
> [!IMPORTANT]
> 這兩部伺服器的防火牆都必須設定為允許來自發出 T-sql COPY 命令之用戶端 IP 的輸入連接。

### <a name="copy-a-sql-database-to-a-different-subscription"></a>將 SQL 資料庫複製到不同的訂用帳戶

您可以使用上一節所述的步驟，將您的資料庫複製到不同訂用帳戶中的 SQL Database 伺服器。 請確定您使用的登入與源資料庫的資料庫擁有者具有相同的名稱和密碼，而且它是 dbmanager 角色的成員，或者是伺服器層級主體登入。 

> [!NOTE]
> [Azure 入口網站](https://portal.azure.com)不支援複製到不同的訂用帳戶，因為入口網站會呼叫 ARM API，並使用訂用帳戶憑證來存取與異地複寫相關的兩部伺服器。  

### <a name="monitor-the-progress-of-the-copying-operation"></a>監視複製作業的進度

藉由查詢 sys.databases 和 sys.dm_database_copies 檢視來監視複製程序。 當複製正在進行時，新資料庫的 sys.databases 檢視的 **state_desc** 資料行會設定為 **COPYING**。

* 如果複製失敗，新資料庫的 sys.databases 檢視的 **state_desc** 資料行會設定為 **SUSPECT**。 在新的資料庫上執行 DROP 陳述式，稍後再試一次。
* 如果複製成功，新資料庫的 sys.databases 檢視的 **state_desc** 資料行會設定為 **ONLINE**。 複製已完成且新資料庫是一般資料庫，能夠與來源資料庫分開進行變更。

> [!NOTE]
> 如果您決定在進行複製時予以取消，請在新資料庫上執行 [DROP DATABASE](https://msdn.microsoft.com/library/ms178613.aspx) 陳述式。 或者，在來源資料庫上執行 DROP DATABASE 陳述式也會取消複製程序。

> [!IMPORTANT]
> 如果您需要使用比來源更小的 SLO 來建立複本，目標資料庫可能沒有足夠的資源來完成植入程式，而且可能會導致複製 operaion 失敗。 在此案例中，請使用異地還原要求，在不同的伺服器和/或不同的區域中建立複本。 如需詳細需，請參閱[使用資料庫備份復原 AZURE SQL 資料庫](sql-database-recovery-using-backups.md#geo-restore)。


## <a name="resolve-logins"></a>解析登入

在新資料庫於目的地伺服器上線之後，使用 [ALTER USER](https://msdn.microsoft.com/library/ms176060.aspx) 陳述式將使用者從新的資料庫重新對應至目的地伺服器上的登入。 若要解析被遺棄的使用者，請參閱 [被遺棄使用者疑難排解](https://msdn.microsoft.com/library/ms175475.aspx)。 另請參閱 [如何管理災害復原後的 Azure SQL 資料庫安全性](sql-database-geo-replication-security-config.md)。

新資料庫中的所有使用者都保有其在來源資料庫中原有的權限。 起始資料庫複製的使用者會變成新資料庫的資料庫擁有者，並且被指派新的安全性識別碼 (SID)。 在複製成功之後，重新對應其他使用者之前，只有起始複製的登入 (也就是資料庫擁有者) 可以登入新的資料庫。

若要了解將資料庫複製到不同的邏輯 SQL Database 伺服器時如何管理使用者與登入，請參閱[如何管理災害復原後的 Azure SQL 資料庫安全性](sql-database-geo-replication-security-config.md)。

## <a name="next-steps"></a>後續步驟

* 如需登入相關資訊，請參閱[管理登入](sql-database-manage-logins.md)以及[如何管理災害復原後的 Azure SQL 資料庫安全性](sql-database-geo-replication-security-config.md)。
* 若要匯出資料庫，請參閱[將資料庫匯出至 BACPAC](sql-database-export.md)。
