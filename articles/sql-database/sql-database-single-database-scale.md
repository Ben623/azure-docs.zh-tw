---
title: 調整單一資料庫資源
description: 本文說明如何在 Azure SQL Database 中調整單一資料庫可用的計算和儲存資源。
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: carlrab
ms.date: 04/26/2019
ms.openlocfilehash: 8d4917bb8956185e0cb557368fbb0c64343c0ac6
ms.sourcegitcommit: 4c831e768bb43e232de9738b363063590faa0472
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/23/2019
ms.locfileid: "74422553"
---
# <a name="scale-single-database-resources-in-azure-sql-database"></a>在 Azure SQL Database 中調整單一資料庫資源

本文說明如何在布建的計算層級調整 Azure SQL Database 的計算和儲存體資源。 或者，[無伺服器計算層](sql-database-serverless.md)會為使用的計算提供計算自動調整和每秒計費。

## <a name="change-compute-size-vcores-or-dtus"></a>變更計算大小（虛擬核心或 Dtu）

一開始挑選虛擬核心或 Dtu 數之後，您可以根據使用[Azure 入口網站](sql-database-single-databases-manage.md#manage-an-existing-sql-database-server)、 [transact-sql](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-current#examples-1)、 [PowerShell](/powershell/module/az.sql/set-azsqldatabase)、 [Azure CLI](/cli/azure/sql/db#az-sql-db-update)或[REST API](https://docs.microsoft.com/rest/api/sql/databases/update)的實際經驗，以動態方式相應增加或相應減少單一資料庫。

下列影片示範如何動態變更服務層級與計算大小，以提高單一資料庫的可用 DTU。

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-SQL-Database-dynamically-scale-up-or-scale-down/player]

> [!IMPORTANT]
> 在某些情況下，您可能需要壓縮資料庫來回收未使用的空間。 如需詳細資訊，請參閱[管理 Azure SQL Database 中的檔案空間](sql-database-file-space-management.md)。

### <a name="impact-of-changing-service-tier-or-rescaling-compute-size"></a>變更服務層級或重新調整計算大小的影響

變更服務層級或計算大小主要涉及服務執行下列步驟：

1. 為資料庫建立新的計算實例  

    使用要求的服務層級和計算大小建立新的計算實例。 針對某些服務層級和計算大小變更的組合，必須在新的計算實例中建立資料庫的複本，這牽涉到複製資料，而且可能會強烈影響整體延遲。 不論是哪一種情況，在此步驟期間，資料庫都會保持上線，而且連接會繼續導向至原始計算實例中的資料庫。

2. 將連接的路由切換至新的計算實例

    原始計算實例中的資料庫現有連接會被捨棄。 系統會在新的計算實例中，為資料庫建立任何新的連接。 針對某些服務層級和計算大小變更的組合，資料庫檔案會在切換期間卸離並重新附加。  無論如何，在資料庫無法使用時，此參數可能會導致短暫的服務中斷，通常只有幾秒鐘的時間。 當連接中斷時，如果有長時間執行的交易在執行中，則此步驟可能需要較長的時間才能復原中止的交易。 [加速資料庫](sql-database-accelerated-database-recovery.md)復原可以降低中止長時間執行之交易的影響。

> [!IMPORTANT]
> 在工作流程中的任何步驟期間，都不會遺失任何資料。 請確定您已在服務層級變更時，使用 Azure SQL Database 的應用程式和元件中，執行了一些[重試邏輯](sql-database-connectivity-issues.md)。

### <a name="latency-of-changing-service-tier-or-rescaling-compute-size"></a>變更服務層級或重新調整計算大小的延遲

變更服務層級或重新調整單一資料庫或彈性集區之計算大小的預估延遲會參數化，如下所示：

|服務層|基本單一資料庫、</br>標準（S0-S1）|基本彈性集區，</br>標準（S2-S12）、 </br>超大規模資料庫 </br>一般用途單一資料庫或彈性集區|Premium 或 Business Critical 單一資料庫或彈性集區|
|:---|:---|:---|:---|
|**基本單一資料庫，</br> 標準（S0-S1）**|&bull; &nbsp;與所用空間無關的固定時間延遲</br>&bull; &nbsp;通常不到5分鐘|&bull; 與由於資料複製而使用的資料庫空間成正比 &nbsp;延遲</br>&bull; &nbsp;通常是每 GB 使用的空間少於1分鐘|&bull; 與由於資料複製而使用的資料庫空間成正比 &nbsp;延遲</br>&bull; &nbsp;通常是每 GB 使用的空間少於1分鐘|
|**基本彈性集區、</br>標準（S2-S12）、</br>超大規模資料庫、</br>一般用途單一資料庫或彈性集區**|&bull; 與由於資料複製而使用的資料庫空間成正比 &nbsp;延遲</br>&bull; &nbsp;通常是每 GB 使用的空間少於1分鐘|&bull; &nbsp;與所用空間無關的固定時間延遲</br>&bull; &nbsp;通常不到5分鐘|&bull; 與由於資料複製而使用的資料庫空間成正比 &nbsp;延遲</br>&bull; &nbsp;通常是每 GB 使用的空間少於1分鐘|
|**Premium 或 Business Critical 單一資料庫或彈性集區**|&bull; 與由於資料複製而使用的資料庫空間成正比 &nbsp;延遲</br>&bull; &nbsp;通常是每 GB 使用的空間少於1分鐘|&bull; 與由於資料複製而使用的資料庫空間成正比 &nbsp;延遲</br>&bull; &nbsp;通常是每 GB 使用的空間少於1分鐘|&bull; 與由於資料複製而使用的資料庫空間成正比 &nbsp;延遲</br>&bull; &nbsp;通常是每 GB 使用的空間少於1分鐘|

> [!TIP]
> 若要監視進行中的作業，請參閱：[使用 SQL REST API 管理作業](https://docs.microsoft.com/rest/api/sql/operations/list)、[使用 CLI 管理](/cli/azure/sql/db/op)作業、[使用 t-sql 監視作業](/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database)和這兩個 PowerShell 命令： [get-azsqldatabaseactivity](/powershell/module/az.sql/get-azsqldatabaseactivity)和[Get-azsqldatabaseactivity](/powershell/module/az.sql/stop-azsqldatabaseactivity)。

### <a name="cancelling-service-tier-changes-or-compute-rescaling-operations"></a>取消服務層級變更或計算重新調整作業

服務層級變更或計算重新調整作業可以取消。

#### <a name="azure-portal"></a>Azure 入口網站

在 [資料庫總覽] 分頁中，流覽至 [**通知**]，然後按一下表示有進行中操作的磚：

![進行中的操作](media/sql-database-single-database-scale/ongoing-operations.png)

接下來，按一下標示為 [**取消此操作**] 的按鈕。

![取消進行中的操作](media/sql-database-single-database-scale/cancel-ongoing-operation.png)

#### <a name="powershell"></a>PowerShell

從 PowerShell 命令提示字元中，設定 `$resourceGroupName`、`$serverName`和 `$databaseName`，然後執行下列命令：

```powershell
$operationName = (az sql db op list --resource-group $resourceGroupName --server $serverName --database $databaseName --query "[?state=='InProgress'].name" --out tsv)
if (-not [string]::IsNullOrEmpty($operationName)) {
    (az sql db op cancel --resource-group $resourceGroupName --server $serverName --database $databaseName --name $operationName)
        "Operation " + $operationName + " has been canceled"
}
else {
    "No service tier change or compute rescaling operation found"
}
```

### <a name="additional-considerations-when-changing-service-tier-or-rescaling-compute-size"></a>變更服務層級或重新調整計算大小時的其他考慮

- 如果您要升級到較高服務層級或計算大小，除非明確指定較大的大小 (大小上限)，否則資料庫大小上限不會增加。
- 若要將資料庫降級，資料庫已用的空間必須小於目標服務層級和計算大小允許的大小上限。
- 從**進階**降級至**標準**層時，如果發生下列情況，將會產生額外的儲存體成本：(1) 以目標計算大小支援資料庫的大小上限，而且 (2) 大小上限超過目標計算大小的內含儲存體數量。 例如，如果大小上限為 500 GB 的 P1 資料庫縮減為 S3，則會有額外的儲存成本，因為 S3 支援的大小上限為 1 TB，而其內含的儲存體數量只有 250 GB。 因此，額外的儲存體數量為 500 GB – 250 GB = 250 GB。 如需額外儲存體的價格詳細資訊，請參閱 [SQL Database 定價](https://azure.microsoft.com/pricing/details/sql-database/)。 如果實際的使用空間量小於內含的儲存體數量，則可將資料庫大小上限降低至內含量，以避免造成額外成本。
- 升級資料庫時，若已啟用[異地複寫](sql-database-geo-replication-portal.md)，您必須先將其次要資料庫升級為所需的服務層級與計算大小，然後再升級主要資料庫 (最佳效能的一般指引)。 升級至不同的版本時，必須先升級次要資料庫。
- 當您在啟用[異地複寫](sql-database-geo-replication-portal.md)的情況下將資料庫降級時，必須先將其主要資料庫降級為所需的服務層級與計算大小，然後再將次要資料庫降級 (最佳效能的一般指引)。 降級至不同的版本時，必須先降級主要資料庫。
- 還原服務會針對各種服務層級提供不同的供應項目。 如果降級至**基本**層，會有較短的備份保留期。 請參閱 [Azure SQL Database 備份](sql-database-automated-backups.md)。
- 完成變更之前，不會將新屬性套用至資料庫。

### <a name="billing-during-compute-rescaling"></a>計算重新調整期間的計費

您需要支付使用最高服務層級資料庫存在的時數 + 在該小時適用的計算大小，不論使用狀況或資料庫是否在作用中少於一小時。 例如，假設您建立了單一資料庫並在五分鐘後刪除，您的帳單就會反映一個資料庫時數的費用。

## <a name="change-storage-size"></a>變更儲存體大小

### <a name="vcore-based-purchasing-model"></a>以虛擬核心為基礎的購買模型

- 可以使用 1GB 為增量單位，將儲存體佈建到大小上限。 資料儲存體的最小可設定值是 5 GB
- 藉由使用 [Azure 入口網站](https://portal.azure.com)、[Transact-SQL](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-current#examples-1)、[PowerShell](/powershell/module/az.sql/set-azsqldatabase)、[Azure CLI](/cli/azure/sql/db#az-sql-db-update) 或 [REST API](https://docs.microsoft.com/rest/api/sql/databases/update) 增加或減少其大小上限，即可佈建單一資料庫的儲存體。
- SQL Database 會自動為記錄檔配置 30% 的額外儲存體，為 TempDB 的每個虛擬核心配置 32GB 儲存體，但不超過 384GB。 TempDB 位於所有服務層級中的已連結 SSD 上。
- 單一資料庫的儲存體價格為資料儲存體和記錄儲存體數量的總和乘以服務層的儲存體單價。 TempDB 的成本包含在虛擬核心價格內。 如需有關額外儲存體的價格詳細資訊，請參閱 [SQL Database 定價](https://azure.microsoft.com/pricing/details/sql-database/)。

> [!IMPORTANT]
> 在某些情況下，您可能需要壓縮資料庫來回收未使用的空間。 如需詳細資訊，請參閱[管理 Azure SQL Database 中的檔案空間](sql-database-file-space-management.md)。

### <a name="dtu-based-purchasing-model"></a>以 DTU 為基礎的購買模型

- 單一資料庫的 DTU 價格包含一定數量不額外收費的儲存體。 佈建超過內含量的額外儲存體會產生額外費用，以 250 GB 為單位最多增加到大小上限 1 TB，超過 1 TB 則以 256 GB 為單位增加。 如需了解內含儲存體數量與大小上限，請參閱[單一資料庫：儲存體大小與計算大小](sql-database-dtu-resource-limits-single-databases.md#single-database-storage-sizes-and-compute-sizes)。
- 可以透過使用 Azure 入口網站、[Transact-SQL](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-current#examples-1)、[PowerShell](/powershell/module/az.sql/set-azsqldatabase)、[Azure CLI](/cli/azure/sql/db#az-sql-db-update) 或 [REST API](https://docs.microsoft.com/rest/api/sql/databases/update) 增加其大小上限，以佈建單一資料庫的額外儲存體。
- 單一資料庫之額外儲存體的價格為額外儲存體數量乘以服務層的額外儲存體單價。 如需有關額外儲存體的價格詳細資訊，請參閱 [SQL Database 定價](https://azure.microsoft.com/pricing/details/sql-database/)。

> [!IMPORTANT]
> 在某些情況下，您可能需要壓縮資料庫來回收未使用的空間。 如需詳細資訊，請參閱[管理 Azure SQL Database 中的檔案空間](sql-database-file-space-management.md)。

## <a name="p11-and-p15-constraints-when-max-size-greater-than-1-tb"></a>當大小上限大於 1 TB 時的 P11 和 P15 條件約束

所有區域目前均可使用進階層中超過 1 TB 的儲存體，但下列地區除外：中國東部、中國北部、德國中部、德國東北部、美國中西部、美國 DoD 地區和美國政府中部。 在這些區域中，進階層中的儲存空間上限為 1 TB。 下列考量與限制適用於大小上限大於 1 TB 的 P11 和 P15 資料庫：

- 如果 P11 或 P15 資料庫的大小上限已設定為大於 1 TB 的值，則只能還原或複製到 P11 或 P15 資料庫。  之後，您可以將資料庫重新調整至不同的計算大小，但前提是在重新調整作業時所配置的空間量，並不會超過新計算大小的大小上限。
- 若是作用中異地複寫案例：
  - 設定異地複寫關聯性：如果 P11 或 P15 為主要資料庫，則次要資料庫也必須是 P11 或 P15；系統會拒絕使用較低的計算大小作為次要資料庫，因為這些資料庫無法支援 1 TB 以上的大小。
  - 在異地複寫關聯性中升級主要資料庫：將主要資料庫的大小上限變更為 1 TB 以上會在次要資料庫中觸發相同的變更。 這兩種升級方式都必須成功，在主要資料庫中的變更才會生效。 適用於大於 1-TB 選項的區域限制。 如果次要資料庫位於不支援大於 1 TB 的區域，就無法升級主要資料庫。
- 不支援使用匯入/匯出服務載入具有 1 TB 以上之 P11/P15 資料庫的功能。 使用 SqlPackage.exe 來[匯入](sql-database-import.md)和[匯出](sql-database-export.md)資料。

## <a name="next-steps"></a>後續步驟

如需整體資源限制，請參閱[SQL Database 以虛擬核心為基礎的資源限制 - 單一資料庫](sql-database-vcore-resource-limits-single-databases.md)和[SQL Database 以 DTU 為基礎的資源限制 - 彈性集區](sql-database-dtu-resource-limits-single-databases.md)。
