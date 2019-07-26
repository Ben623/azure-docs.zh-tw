---
title: 還原現有的 Azure SQL 資料倉儲 |Microsoft Docs
description: 還原現有 Azure SQL 資料倉儲的操作指南。
services: sql-data-warehouse
author: anumjs
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: manage
ms.date: 08/29/2018
ms.author: anjangsh
ms.reviewer: igorstan
ms.openlocfilehash: 8593378dd35a6aaa31aaefca41dd921c6e65d336
ms.sourcegitcommit: 198c3a585dd2d6f6809a1a25b9a732c0ad4a704f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/23/2019
ms.locfileid: "68426742"
---
# <a name="restore-an-existing-azure-sql-data-warehouse"></a>還原現有的 Azure SQL 資料倉儲

在本文中, 您將瞭解如何透過 Azure 入口網站和 PowerShell 還原現有的 SQL 資料倉儲:

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

**請驗證您的 DTU 容量。** 每個 SQL 資料倉儲都是由具有預設 DTU 配額的 SQL server (例如, myserver.database.windows.net) 所主控。 確認 SQL server 有足夠的剩餘 DTU 配額可供要還原的資料庫。 若要了解如何計算所需 DTU 或要求更多 DTU，請參閱 [要求 DTU 配額變更][Request a DTU quota change]。

## <a name="before-you-begin"></a>開始之前

1. 請務必[安裝 Azure PowerShell][Install Azure PowerShell]。
2. 擁有您想要從中還原的現有還原點。 如果您想要建立新的還原, 請參閱教學課程,[以建立新的使用者定義還原點][the tutorial to create a new user-defined restore point]。

## <a name="restore-an-existing-data-warehouse-through-powershell"></a>透過 PowerShell 還原現有的資料倉儲

若要從還原點還原現有的資料倉儲, 請使用[Set-azsqldatabase 搭配][Restore-AzSqlDatabase]PowerShell Cmdlet。

1. 開啟 PowerShell。

2. 連接到您的 Azure 帳戶，然後列出與您帳戶關聯的所有訂用帳戶。

3. 選取包含要還原之資料庫的訂用帳戶。

4. 列出資料倉儲的還原點。

5. 使用 RestorePointCreationDate 挑選出想要的還原點。

6. 使用[Set-azsqldatabase 搭配][Restore-AzSqlDatabase]PowerShell Cmdlet, 將資料倉儲還原到所需的還原點。
        1. 若要將 SQL 資料倉儲還原到不同的邏輯伺服器, 請務必指定其他邏輯伺服器名稱。  此邏輯伺服器也可以位於不同的資源群組和區域中。
        2. 若要還原至不同的訂用帳戶, 請使用 [移動] 按鈕將邏輯伺服器移至另一個訂用帳戶。

7. 確認還原的資料倉儲已上線。

8. 還原完成之後, 您可以遵循在復原[之後設定資料庫][Configure your database after recovery]來設定已復原的資料倉儲。

```Powershell

$SubscriptionName="<YourSubscriptionName>"
$ResourceGroupName="<YourResourceGroupName>"
$ServerName="<YourServerNameWithoutURLSuffixSeeNote>"  # Without database.windows.net
#$TargetResourceGroupName="<YourTargetResourceGroupName>" # uncomment to restore to a different logical server.
#$TargetServerName="<YourtargetServerNameWithoutURLSuffixSeeNote>"  
$DatabaseName="<YourDatabaseName>"
$NewDatabaseName="<YourDatabaseName>"

Connect-AzAccount
Get-AzSubscription
Select-AzSubscription -SubscriptionName $SubscriptionName

# Or list all restore points
Get-AzSqlDatabaseRestorePoints -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName $DatabaseName

# Get the specific database to restore
$Database = Get-AzSqlDatabase -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName $DatabaseName

# Pick desired restore point using RestorePointCreationDate "xx/xx/xxxx xx:xx:xx xx"
$PointInTime="<RestorePointCreationDate>"  

# Restore database from a restore point
$RestoredDatabase = Restore-AzSqlDatabase –FromPointInTimeBackup –PointInTime $PointInTime -ResourceGroupName $Database.ResourceGroupName -ServerName $Database.ServerName -TargetDatabaseName $NewDatabaseName –ResourceId $Database.ResourceID

# Use the following command to restore to a different logical server
#$RestoredDatabase = Restore-AzSqlDatabase –FromPointInTimeBackup –PointInTime $PointInTime -ResourceGroupName $Database.ResourceTargetGroupName -ServerName $TargetServerName -TargetDatabaseName $NewDatabaseName –ResourceId $Database.ResourceID

# Verify the status of restored database
$RestoredDatabase.status

```

## <a name="restore-an-existing-data-warehouse-through-the-azure-portal"></a>透過 Azure 入口網站還原現有的資料倉儲

1. 登入 [Azure 入口網站][Azure portal]。
2. 瀏覽至您想要還原的 SQL 資料倉儲。
3. 在 [概觀] 刀鋒視窗頂端，選取 [還原]。

    ![ 還原概觀](./media/sql-data-warehouse-restore-active-paused-dw/restoring-01.png)

4. 選取 [自動還原點] 或 [使用者定義的還原點]。 如果資料倉儲沒有任何自動還原點, 請等待幾個小時, 或先建立使用者定義的還原點, 再進行還原。 若為使用者定義的還原點, 請選取現有的還原點, 或建立一個新的。 針對 [**伺服器**], 您可以在不同的資源群組和區域中挑選邏輯伺服器, 或建立一個新的。 提供所有參數之後, 請按一下 [**審核 + 還原**]。

    ![自動還原點](./media/sql-data-warehouse-restore-active-paused-dw/restoring-11.png)

## <a name="next-steps"></a>後續步驟
- [還原已刪除的資料倉儲][Restore a deleted data warehouse]
- [從異地備份資料倉儲還原][Restore from a geo-backup data warehouse]
 
<!--Image references-->

<!--Article references-->
[Azure SQL Database business continuity overview]: ../sql-database/sql-database-business-continuity.md
[Request a DTU quota change]: ./sql-data-warehouse-get-started-create-support-ticket.md
[Configure your database after recovery]: ../sql-database/sql-database-disaster-recovery.md#configure-your-database-after-recovery
[How to install and configure Azure PowerShell]: /powershell/azureps-cmdlets-docs
[Overview]: ./sql-data-warehouse-restore-database-overview.md
[Portal]: ./sql-data-warehouse-restore-database-portal.md
[PowerShell]: ./sql-data-warehouse-restore-database-powershell.md
[REST]: ./sql-data-warehouse-restore-database-rest-api.md
[Configure your database after recovery]: ../sql-database/sql-database-disaster-recovery.md#configure-your-database-after-recovery
[the tutorial to create a new user-defined restore point]:../sql-data-warehouse/sql-data-warehouse-restore-points.md
[Install Azure PowerShell]: https://docs.microsoft.com/powershell/azure/overview
[Restore an existing data warehouse]:./sql-data-warehouse-restore-active-paused-dw.md
[Restore a deleted data warehouse]:./sql-data-warehouse-restore-deleted-dw.md
[Restore from a geo-backup data warehouse]:./sql-data-warehouse-restore-from-geo-backup.md

<!--MSDN references-->
[Restore-AzSqlDatabase]: https://docs.microsoft.com/powershell/module/az.sql/restore-azsqldatabase

<!--Other Web references-->
[Azure Portal]: https://portal.azure.com/
