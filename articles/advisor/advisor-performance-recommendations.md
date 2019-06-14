---
title: 利用 Azure Advisor 改善 Azure 應用程式的效能 | Microsoft Docs
description: 使用 Advisor 將 Azure 部署的效能最佳化。
services: advisor
documentationcenter: NA
author: kasparks
ms.service: advisor
ms.topic: article
ms.date: 01/29/2019
ms.author: kasparks
ms.openlocfilehash: 8fdae1e12e56dcbcb56941726b0c089ad59b8fc8
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "66254658"
---
# <a name="improve-performance-of-azure-applications-with-azure-advisor"></a>利用 Azure Advisor 改善 Azure 應用程式的效能

Advisor 效能建議有助於提升業務關鍵應用程式的速度和回應能力。 您可以在 Advisor 儀表板的 [效能]  索引標籤上，取得 Advisor 的效能建議。

## <a name="reduce-dns-time-to-live-on-your-traffic-manager-profile-to-fail-over-to-healthy-endpoints-faster"></a>減少 DNS 在流量管理員設定檔上的存留時間，以更快速容錯移轉至健康情況良好的端點上

流量管理員設定檔上的[存留時間 (TTL) 設定](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-performance-considerations)可讓您在指定端點停止回應查詢時，指定切換至端點的速度。 減少 TTL 值表示用戶端將會更快速路由傳送至運作的端點。

Azure Advisor 會識別設定較長 TTL 的流量管理員設定檔，並且建議將 TTL 設定為 20 秒或 60 秒，取決於是否設定[快速容錯移轉](https://azure.microsoft.com/roadmap/fast-failover-and-tcp-probing-in-azure-traffic-manager/)的設定檔。

## <a name="improve-database-performance-with-sql-db-advisor"></a>使用 SQL DB Advisor 來改善資料庫效能

建議程式可針對所有的 Azure 資源提供一致的合併建議檢視。 它會與 SQL Database 建議程式整合，以提供改善 SQL Azure 資料庫效能的相關建議。 SQL Database 建議程式會藉由分析您的使用歷程記錄來評估 SQL Azure 資料庫的效能。 接著會提供最適合用於執行資料庫之一般工作負載的建議事項。

> [!NOTE]
> 若要取得建議，資料庫必須持續使用一週，而且那一週之內必須有一些一致的活動。 相較於隨機蹦出的活動，一致的查詢模式更有利於 SQL Database Advisor 最佳化。

如需 SQL Database Advisor 的詳細資訊，請參閱 [SQL Database Advisor](https://azure.microsoft.com/documentation/articles/sql-database-advisor/)。

## <a name="improve-app-service-performance-and-reliability"></a>改善 App Service 的效能和可靠性

Azure 建議程式整合了最佳作法建議，以供提升應用程式服務體驗和探索相關的平台功能。 應用程式服務建議的範例如下︰
* 偵測應用程式執行階段與緩和選項已耗盡記憶體或 CPU 資源的執行個體。
* 偵測共置資源 (如 Web 應用程式和資料庫) 可改善效能並降低成本的執行個體。

如需應用程式服務建議的詳細資訊，請參閱 [Azure App Service 的最佳作法](https://azure.microsoft.com/documentation/articles/app-service-best-practices/)。

## <a name="use-managed-disks-to-prevent-disk-io-throttling"></a>使用受控磁碟可避免磁碟 I/O 節流

Advisor 會識別即將達到其延展性目標的儲存體帳戶所包含的虛擬機器。 這種情況讓這些 VM 很可能會執行 I/O 節流。 Advisor 會建議它們使用受控磁碟，以避免效能降低。

## <a name="improve-the-performance-and-reliability-of-virtual-machine-disks-by-using-premium-storage"></a>使用進階儲存體以改善虛擬機器磁碟的效能和可靠性

虛擬機器若使用標準磁碟，且在您的儲存體帳戶上有大量交易，Advisor 會加以識別，並建議升級為進階磁碟。 

針對執行時需要大量 I/O 之工作負載的虛擬機器，「Azure 進階儲存體」可提供高效能、低延遲的磁碟支援。 使用進階儲存體帳戶的虛擬機器磁碟會將資料儲存在固態硬碟 (SSD) 上。 為了讓應用程式發揮最佳效能，建議您將任何需要高 IOPS 的虛擬機器磁碟移轉至進階儲存體。

## <a name="remove-data-skew-on-your-sql-data-warehouse-table-to-increase-query-performance"></a>在 SQL 資料倉儲資料表上移除資料扭曲以提升查詢效能

執行工作負載時，資料扭曲可能會造成不必要的資料移動或資源瓶頸。 Advisor 將偵測到大於 15% 的散發資料扭曲，且會建議您重新散發資料，並重新檢視您的資料表散發金鑰選取項目。 若要深入了解如何識別和移除扭曲，請參閱[針對扭曲進行疑難排解](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-tables-distribute#how-to-tell-if-your-distribution-column-is-a-good-choice)。

## <a name="create-or-update-outdated-table-statistics-on-your-sql-data-warehouse-table-to-increase-query-performance"></a>在 SQL 資料倉儲資料表上建立或更新過期的資料表統計資料以提升查詢效能

Advisor 會識別不含最新[資料表統計資料](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-tables-statistics)的資料表，以及建立或更新資料表統計資料的建議。 SQL 資料倉儲查詢最佳化工具會使用最新的統計資料，來估計基數或查詢結果中的資料列數目，以利其建立高品質的查詢計劃來取得更快速的效能。

## <a name="scale-up-to-optimize-cache-utilization-on-your-sql-data-warehouse-tables-to-increase-query-performance"></a>藉由相應增加將 SQL 資料倉儲資料表的快取使用率最佳化，以提升查詢效能

Azure Advisor 會偵測您的 SQL 資料倉儲是否有快取使用百分比偏高、命中百分比偏低的情形。 這種情況表示快取收回率偏高，而可能會影響到您 SQL 資料倉儲的效能。 Advisor 會建議您相應增加 SQL 資料倉儲，以確保能配置足夠的快取容量供工作負載使用。

## <a name="convert-sql-data-warehouse-tables-to-replicated-tables-to-increase-query-performance"></a>將 SQL 資料倉儲資料表轉換為複寫資料表，以提升查詢效能

Advisor 會識別不是複寫資料表、但可因轉換而受益的資料表，並建議您轉換這些資料表。 提供的建議取決於複寫的資料表大小、資料行數目、資料表散發類型，以及 SQL 資料倉儲資料表的分割區數目。 此外也可能在內容的建議中提供啟發學習法。 若要深入了解這項建議的權衡方式，請參閱 [SQL 資料倉儲建議](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-concept-recommendations#replicate-tables)。 

## <a name="migrate-your-storage-account-to-azure-resource-manager-to-get-all-of-the-latest-azure-features"></a>將儲存體帳戶移轉至 Azure Resource Manager 以取得所有最新的 Azure 功能

將儲存體帳戶部署模型遷移到 Azure Resource Manager (Resource Manager)，可使用範本部署、其他安全性選項，以及可升級至 GPv2 帳戶，以利用 Azure 儲存體的最新功能。 Advisor 會識別任何使用傳統部署模型的獨立儲存體帳戶，並建議遷移到 Resource Manager 部署模型。

> [!NOTE]
> Azure 監視器中的傳統警示排定在 2019 年 6 月淘汰。 建議您將傳統儲存體帳戶升級為使用 Resource Manager，以在新平台上保留警示功能。 如需詳細資訊，請參閱[傳統警示洶汰](https://azure.microsoft.com/updates/classic-alerting-monitoring-retirement/)。

## <a name="design-your-storage-accounts-to-prevent-hitting-the-maximum-subscription-limit"></a>設計您的儲存體帳戶，以避免達到最大訂用帳戶限制

Azure 區域可以支援最多 250 每訂用帳戶的儲存體帳戶。 一旦達到限制時，您將無法在該區域/訂用帳戶組合中建立任何更多的儲存體帳戶。 Advisor 會檢查您的訂用帳戶和介面的建議，讓您設計較少的儲存體帳戶，即將達到最大限制。

## <a name="optimize-the-performance-of-your-azure-mysql-azure-postgresql-and-azure-mariadb-servers"></a>您的 Azure MySQL、 Azure PostgreSQL 和 Azure MariaDB 伺服器的效能最佳化 

### <a name="fix-the-cpu-pressure-of-your-azure-mysql-azure-postgresql-and-azure-mariadb-servers-with-cpu-bottlenecks"></a>修正您的 Azure MySQL、 Azure PostgreSQL 和 Azure MariaDB 伺服器的 CPU 壓力與 CPU 瓶頸
非常高使用率的 CPU，經過一段長，可能會導致查詢效能緩慢工作負載。 增加 CPU 大小將會協助最佳化資料庫查詢的執行階段，並改善整體效能。 Azure Advisor 會識別伺服器，以高 CPU 使用率可能執行 CPU 限制的工作負載，建議您調整您的計算。

### <a name="reduce-memory-constraints-on-your-azure-mysql-azure-postgresql-and-azure-mariadb-servers-or-move-to-a-memory-optimized-sku"></a>減少您的 Azure MySQL、 Azure PostgreSQL 和 Azure MariaDB 伺服器上的記憶體條件約束，或移至記憶體最佳化的 SKU
的低的快取點擊的率可能會導致較慢的查詢效能和更高的 IOPS。 這可能是因為不正確的查詢計劃或執行記憶體密集工作負載。 修正查詢計劃或 [增加記憶體](https://docs.microsoft.com/azure/postgresql/concepts-pricing-tiers)PostgreSQL 資料庫伺服器、 Azure MySQL 資料庫伺服器或 Azure MariaDB 的 Azure 資料庫伺服器有助於最佳化執行資料庫工作負載。 Azure Advisor 會識別由於此高緩衝區集區變換所影響的伺服器，並建議修正的查詢計劃中，移至 更多的記憶體，較高的 SKU，或增加以取得更多 IOPS 的儲存體大小。

### <a name="use-a-azure-mysql-or-azure-postgresql-read-replica-to-scale-out-reads-for-read-intensive-workloads"></a>使用 Azure MySQL 或 Azure PostgreSQL 讀取複本，來相應放大讀取的讀取密集的工作負載
Azure Advisor 會利用工作負載為基礎啟發學習法例如讀取與寫入伺服器上識別為大量讀取工作負載在過去七天的比例。 您的資源用於 PostgreSQL 的 Azure 資料庫或具有極高的讀取/寫入比率的 MySQL 資源的 Azure 資料庫可能會導致 CPU 和/或記憶體爭用導致查詢效能變慢。 新增 [複本](https://docs.microsoft.com/azure/postgresql/howto-read-replicas-portal)有助於您的相應放大到複本伺服器，以防止在主要伺服器上的 CPU 和/或記憶體條件約束的讀取。 Advisor 會識別這類高為大量讀取工作負載的伺服器，並建議您新增 [讀取複本](https://docs.microsoft.com/azure/postgresql/concepts-read-replicas) 來卸載部分讀取的工作負載。


### <a name="scale-your-azure-mysql-azure-postgresql-or-azure-mariadb-server-to-a-higher-sku-to-prevent-connection-constraints"></a>調整您的 Azure MySQL、 Azure PostgreSQL 或 Azure MariaDB 伺服器，以較高的 SKU，以防止連接條件約束
每個新連線到您的資料庫伺服器會佔一些記憶體。 如果因為無法連線到您的伺服器，就會降低資料庫伺服器的效能 [上限](https://docs.microsoft.com/azure/postgresql/concepts-limits)在記憶體中。 Azure Advisor 會識別伺服器執行具有許多連接失敗，並建議升級您的伺服器連線限制，藉由相應增加計算，或使用記憶體最佳化的 Sku，具有更多的運算，每個核心，提供更多記憶體到您的伺服器。

## <a name="scale-your-cache-to-a-different-size-or-sku-to-improve-cache-and-application-performance"></a>調整您的快取為不同大小或 SKU，以改善快取與應用程式效能

當不高的記憶體不足的壓力、 高的伺服器負載或高網路頻寬可能會讓其變成沒有回應、 發生資料遺失，或變成無法使用其下執行時，快取執行個體的表現最好。 Advisor 會識別在這些情況的快取執行個體，並建議套用最佳作法，以減少記憶體不足的壓力、 伺服器負載或網路頻寬，或使用更多容量調整到不同的大小或 SKU。

## <a name="add-regions-with-traffic-to-your-azure-cosmos-db-account"></a>將流量的區域新增至您的 Azure Cosmos DB 帳戶

Advisor 會偵測目前未設定的地區有流量的 Azure Cosmos DB 帳戶，並建議您新增該區域。 這會改善來自該區域之要求的延遲，並可確保發生區域中斷時的可用性。 [深入了解使用 Azure Cosmos DB 的全域資料發佈](https://aka.ms/cosmos/globaldistribution)

## <a name="configure-your-azure-cosmos-db-indexing-policy-with-customer-included-or-excluded-paths"></a>設定您的 Azure Cosmos DB 與客戶編製索引原則中包含或排除路徑

Azure Advisor 會識別使用的預設檢索原則，但無法受益於工作負載模式為基礎的自訂索引編製原則的 Cosmos DB 容器。 編製索引原則的預設編製索引的所有屬性，但用於查詢篩選條件的明確包含或排除路徑中使用自訂的編製索引原則，可以減少 Ru 及編製索引所耗用的儲存體。 [深入了解修改索引原則](https://aka.ms/cosmosdb/modify-index-policy)

## <a name="configure-your-azure-cosmos-db-query-page-size-maxitemcount-to--1"></a>將 Azure Cosmos DB 查詢頁面大小 (MaxItemCount) 設定為 -1 

Azure Advisor 會識別 Azure Cosmos DB 容器會使用查詢頁面大小為 100，建議使用的頁面大小為-1 的快速掃描。 [深入了解最大項目計數](https://aka.ms/cosmosdb/sql-api-query-metrics-max-item-count)

## <a name="how-to-access-performance-recommendations-in-advisor"></a>如何在建議程式中存取效能建議

1. 登入 [Azure 入口網站](https://portal.azure.com)，然後開啟 [Advisor](https://aka.ms/azureadvisordashboard)。

2.  在 Advisor 儀表板上，按一下 [效能]  索引標籤。

## <a name="next-steps"></a>後續步驟

若要深入了解 Advisor 建議，請參閱：

* [Advisor 簡介](advisor-overview.md)
* [開始使用 Advisor](advisor-get-started.md)
* [Advisor 成本建議](advisor-performance-recommendations.md)
* [Advisor 高可用性建議](advisor-high-availability-recommendations.md)
* [Advisor 安全性建議](advisor-security-recommendations.md)
