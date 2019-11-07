---
title: 版本資訊
description: Azure SQL 資料倉儲的版本資訊。
services: sql-data-warehouse
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: ''
ms.date: 09/18/2019
author: anumjs
ms.author: anjangsh
ms.reviewer: jrasnick
manager: craigg
ms.custom: seo-lt-2019
ms.openlocfilehash: bb9aa1cf547c37670732665a8d207f8c9f01eee0
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/06/2019
ms.locfileid: "73692917"
---
# <a name="azure-sql-data-warehouse-release-notes"></a>Azure SQL 資料倉儲版本資訊

本文摘要說明最新版本的[Azure SQL 資料倉儲](sql-data-warehouse-overview-what-is.md)（AZURE SQL DW）中的新功能和改進。 本文也會列出與發行不直接相關，但在相同時間範圍內發行的值得注意的內容更新。 如需其他 Azure 服務的改進功能，請參閱[服務更新](https://azure.microsoft.com/updates)。

## <a name="check-your-azure-sql-data-warehouse-version"></a>檢查您的 Azure SQL 資料倉儲版本

當新功能推出至所有區域時，請檢查部署至您實例的版本，以及最新的 Azure SQL DW 版本資訊，以取得功能可用性。 若要檢查您的 Azure SQL DW 版本，請透過 SQL Server Management Studio （SSMS）連線到您的資料倉儲，然後執行 `SELECT @@VERSION;` 以傳回目前版本的 Azure SQL DW。

範例輸出︰

![SQL 資料倉儲版本](./media/release-notes/sql_data_warehouse_version.png)

使用識別的日期來確認哪個發行已套用至您的 Azure SQL DW。

## <a name="september-2019"></a>2019年9月

| 服務改進功能 | 詳細資料 |
| --- | --- |
|**Azure 私人連結（預覽）**|使用[Azure 私人連結](https://azure.microsoft.com/blog/announcing-azure-private-link/)，您可以在虛擬網路（VNet）中建立私人端點，並將其對應至您的 AZURE SQL DW。 接著，您可以透過 VNet 中的私人 IP 位址來存取這些資源，以啟用從內部部署到 Azure ExpressRoute 私用對等互連和（或） VPN 閘道的連線。 整體來說，這可簡化網路設定，而不需要您將它開啟到公用 IP 位址。 這也可以保護資料外泄風險。 如需詳細資訊，請參閱[總覽](/azure/private-link/private-link-overview)和[SQL DW 檔](/azure/sql-database/sql-database-private-endpoint-overview)。|
|**資料探索 & 分類（GA）**|[資料探索和分類](/azure/sql-database/sql-database-data-discovery-and-classification?toc=/azure/sql-data-warehouse/toc.json)功能現已正式推出。 這項功能提供了先進的功能，可用於**探索、分類、標記 & 保護**資料庫中的敏感性資料。|
|**Azure Advisor 單鍵整合**|SQL 資料倉儲現在直接與 [總覽] 分頁中的 Azure Advisor 建議整合，並提供單鍵體驗。 您現在可以在 [總覽] 分頁中探索建議，而不是流覽至 [Azure advisor] 分頁。 [在這裡](sql-data-warehouse-concept-recommendations.md)瞭解更多建議。|
|**讀取認可的快照集隔離（預覽）**|您可以使用 ALTER database 來啟用或停用使用者資料庫的快照集隔離。  若要避免影響目前的工作負載，您可能會想要在資料庫維護期間設定此選項，或等到沒有其他作用中的資料庫連接。 如需詳細資訊，請參閱[Alter database set 選項](/sql/t-sql/statements/alter-database-transact-sql-set-options?view=azure-sqldw-latest)。|
|**EXECUTE AS （Transact-sql）**| [執行](/sql/t-sql/statements/execute-as-transact-sql?view=azure-sqldw-latest)身分T-sql 支援現已提供 SQL 資料倉儲，讓客戶能夠將會話的執行內容設定為指定的使用者。|
|**其他 T-sql 支援**|SQL 資料倉儲的 T-sql 語言介面區已擴充為包含的支援： </br> - [格式（transact-sql）](/sql/t-sql/functions/format-transact-sql?view=azure-sqldw-latest)</br> - [TRY_PARSE （transact-sql）](/sql/t-sql/functions/try-parse-transact-sql?view=azure-sqldw-latest)</br> - [TRY_CAST （transact-sql）](/sql/t-sql/functions/try-cast-transact-sql?view=azure-sqldw-latest)</br> - [TRY_CONVERT （transact-sql）](/sql/t-sql/functions/try-convert-transact-sql?view=azure-sqldw-latest)</br> - [sys.databases user_token （transact-sql）](/sql//relational-databases/system-catalog-views/sys-user-token-transact-sql?view=azure-sqldw-latest)|

## <a name="july-2019"></a>2019年7月

| 服務改進功能 | 詳細資料 |
| --- | --- |
|**具體化視圖（預覽）**|具體化視圖會保存從 View definition 查詢傳回的資料，並在基礎資料表中的資料變更時自動更新。 它可以改善複雜查詢（通常是具有聯結和匯總的查詢）的效能，同時提供簡單的維護作業。 如需詳細資訊，請參閱： </br> - [建立具體化 VIEW 做為&#40;SELECT transact-sql&#41; ](/sql/t-sql/statements/create-materialized-view-as-select-transact-sql?view=azure-sqldw-latest)</br> - [ALTER 具體化 VIEW &#40;transact-sql&#41; ](/sql/t-sql/statements/alter-materialized-view-transact-sql?view=azure-sqldw-latest) </br> [Azure SQL 資料倉儲支援 - t-sql 語句](/azure/sql-data-warehouse/sql-data-warehouse-reference-tsql-statements)|
|**其他 T-sql 支援**|SQL 資料倉儲的 T-sql 語言介面區已擴充為包含的支援： </br> [在時區 - （transact-sql）](/sql/t-sql/queries/at-time-zone-transact-sql?view=azure-sqldw-latest)</br> - [STRING_AGG （transact-sql）](/sql/t-sql/functions/string-agg-transact-sql?view=azure-sqldw-latest)|
|**結果集快取（預覽）**|新增的 DBCC 命令，用來管理先前宣佈的結果集快取。 如需詳細資訊，請參閱： </br> - [DBCC DROPRESULTSETCACHE &#40;Transact-SQL&#41;](/sql/t-sql/database-console-commands/dbcc-dropresultsetcache-transact-sql?view=azure-sqldw-latest)  </br> - [DBCC SHOWRESULTCACHESPACEUSED &#40;Transact-SQL&#41;](/sql/t-sql/database-console-commands/dbcc-showresultcachespaceused-transact-sql?view=azure-sqldw-latest) </br></br> 另請參閱 sys.databases 中的新 result_set_cache 資料行，該資料行會顯示執行中的查詢使用結果集快取的時間[dm_pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql?view=azure-sqldw-latest) 。|
|**已排序的叢集資料行存放區索引（預覽）**|新增至 sys.databases 的新資料行 column_store_order_ordinal， [index_columns](/sql/relational-databases/system-catalog-views/sys-index-columns-transact-sql?view=azure-sqldw-latest)識別已排序的叢集資料行存放區索引中的資料行順序。|

## <a name="may-2019"></a>2019 年 5 月

| 服務改進功能 | 詳細資料 |
| --- | --- |
|**動態資料遮罩（預覽）**|動態資料遮罩 (DDM) 可防止其他人未經授權存取您資料倉儲中的敏感性資料，做法是根據您定義的遮罩規則，即時在查詢結果中混淆這項資料。 如需詳細資訊，請參閱[SQL Database 動態資料遮罩](/azure/sql-database/sql-database-dynamic-data-masking-get-started)。|
|**工作負載重要性現已正式推出**|工作負載管理分類和重要性可讓您影響查詢的執行順序。 如需工作負載重要性的詳細資訊，請參閱檔中的[分類](sql-data-warehouse-workload-classification.md)和[重要性](sql-data-warehouse-workload-importance.md)總覽文章。 也請參閱[建立工作負載分類器](/sql/t-sql/statements/create-workload-classifier-transact-sql?view=azure-sqldw-latest)檔。<br/><br/>請參閱下列影片中的動作中的工作負載重要性：<br/> -[工作負載管理概念](https://www.youtube.com/embed/QcCRBAhoXpM)<br/> -[工作負載管理案例](https://www.youtube.com/embed/_2rLMljOjw8)|
|**其他 T-sql 支援**|SQL 資料倉儲的 T-sql 語言介面區已擴充為包含的支援： </br> - [修剪](/sql/t-sql/functions/trim-transact-sql?view=azure-sqldw-latest)|
|**JSON 函式**|商務分析師現在可以使用熟悉的 T-sql 語言，利用 Azure 資料倉儲中的下列新 JSON 函數來查詢和操作格式化為 JSON 資料的檔：</br> - [ISJSON](/sql/t-sql/functions/isjson-transact-sql?view=azure-sqldw-latest)</br> - [JSON_VALUE](/sql/t-sql/functions/json-value-transact-sql?view=azure-sqldw-latest)</br> -  [JSON_QUERY](/sql/t-sql/functions/json-query-transact-sql?view=azure-sqldw-latest)</br> -  [JSON_MODIFY](/sql/t-sql/functions/json-modify-transact-sql?view=azure-sqldw-latest)</br> - [OPENJSON](/sql/t-sql/functions/openjson-transact-sql?view=azure-sqldw-latest)|
|**結果集快取（預覽）**|結果集快取可啟用即時查詢回應時間，同時減少商務分析師和報告使用者的時間深入解析。 如需詳細資訊，請參閱：</br> - [ALTER database （transact-sql）](/sql/t-sql/statements/alter-database-transact-sql?view=azure-sqldw-latest)</br> - [ALTER DATABASE SET 選項（transact-sql）](/sql/t-sql/statements/alter-database-transact-sql-set-options?view=azure-sqldw-latest)</br> - [設定結果集快取（transact-sql）](/sql/t-sql/statements/set-result-set-caching-transact-sql?view=azure-sqldw-latest)</br> - [SET 語句（transact-sql）](/sql/t-sql/statements/set-statements-transact-sql)</br> - [sys.databases （transact-sql）](/sql/relational-databases/system-catalog-views/sys-databases-transact-sql?view=azure-sqldw-latest)|
|**已排序的叢集資料行存放區索引（預覽）**|資料行存放區對於儲存及有效率地查詢大量資料至關重要。 針對每個資料表，它會將傳入的資料分割成資料列群組，而且資料列群組的每個資料行會構成磁碟上的一個分割。  已排序叢集資料行存放區索引透過有效率地刪除分割，進一步將查詢執行最佳化。   如需詳細資訊，請參閱：</br> -  [CREATE TABLE （Azure SQL 資料倉儲）](/sql/t-sql/statements/create-table-azure-sql-data-warehouse?view=azure-sqldw-latest)</br> -  建立資料行存放區[索引（transact-sql）](/sql/t-sql/statements/create-columnstore-index-transact-sql?view=azure-sqldw-latest)。|

## <a name="march-2019"></a>2019 年 3 月

| 服務改進功能 | 詳細資料 |
| --- | --- |
|**資料探索與分類**|Azure SQL 資料倉儲的資料探索與分類現在提供公開預覽。 保護敏感性資料和客戶隱私權是不可或缺的。 隨著您的業務和客戶資料資產的成長，探索、分類和保護您的資料會變得難以管理。 我們以原生方式介紹的資料探索和分類功能 Azure SQL 資料倉儲可協助保護您的資料，使其更容易管理。 這個功能的整體優點包括：<br/>&bull; &nbsp; 符合資料隱私權標準和法規合規性需求。<br/>&bull; &nbsp; 限制存取和強化包含高度敏感性資料之資料倉儲的安全性。<br/>&bull; &nbsp; 對敏感性資料的異常存取進行監視和警示。<br/>&bull; &nbsp; Azure 入口網站上中央儀表板中敏感性資料的視覺效果。 </br></br>資料探索 & 分類適用于所有 Azure 區域中的 Azure SQL 資料倉儲，它屬於先進的資料安全性，包括弱點評定和威脅偵測。 如需有關資料探索 & 分類的詳細資訊，請參閱[blog 文章](https://azure.microsoft.com/blog/announcing-public-preview-of-data-discovery-classification-for-microsoft-azure-sql-data-warehouse/)和我們的線上[檔](/azure/sql-database/sql-database-data-discovery-and-classification)。|
|**依匯總套件分組**|匯總現在是 Azure 資料倉儲中支援的 GROUP BY 選項。   GROUP BY ROLLUP 會針對每個資料行運算式的組合建立群組。 GROUP BY 也會將結果「匯總」為小計和總計。 GROUP BY 函數會由右至左處理，減少建立群組和匯總所依據的資料行運算式數目。  資料行順序會影響匯總輸出，而且可能會影響結果集中的資料列數目。<br/><br/>如需群組依據匯總的詳細資訊，請參閱[GROUP by （transact-sql）](/sql/t-sql/queries/select-group-by-transact-sql?view=azure-sqldw-latest)
|**已改善 DWU 使用和 CPU 入口網站計量的精確度**|SQL 資料倉儲大幅增強 Azure 入口網站中的計量準確度。  此版本包含 CPU 和 DWU 使用的計量定義的修正，可正確反映所有計算節點上的工作負載。 在此修正之前，會短報度量值。 預期會在 Azure 入口網站中看到 [已使用的 DWU] 和 [CPU 計量] 的增加。 |
|**資料列層級安全性**|我們在2017年11月引進了資料列層級安全性功能。 我們現在也將這種支援延伸到外部資料表。 此外，我們也新增了在定義安全性篩選述詞所需的內嵌資料表值函式（內嵌 Tvf）中呼叫不具決定性函數的支援。 這項新增功能可讓您在安全性篩選器述詞中指定 IS_ROLEMEMBER （）、USER_NAME （）等。 如需詳細資訊，請參閱資料[列層級安全性檔案](/sql/relational-databases/security/row-level-security)中的範例。|
|**其他 T-sql 支援**|SQL 資料倉儲的 T-sql 語言介面區已擴充為包含對[STRING_SPLIT （transact-sql）](/sql/t-sql/functions/string-split-transact-sql)的支援。
|**查詢最佳化工具增強功能** |查詢優化是任何資料庫的重要元件。 對如何最佳執行查詢做出最佳選擇，會產生顯著的改善。  在分散式環境中執行複雜的分析查詢時，執行的作業數目很重要。 藉由產生較佳的品質計畫，已增強了查詢效能。 這些計畫會將昂貴的資料傳輸作業和重複的計算（例如，重複的子查詢）降到最低。 如需詳細資訊，請參閱這 Azure SQL 資料倉儲的[blog 文章](https://azure.microsoft.com/blog/smarter-faster-safer-azure-sql-data-warehouse-is-simply-unmatched/)。|
| | |

### <a name="documentation-improvements"></a>文件改進功能

| 文件改進功能 | 詳細資料 |
| --- | --- |
| | |

## <a name="january-2019"></a>2019 年 1 月

### <a name="service-improvements"></a>服務改進功能

| 服務改進功能 | 詳細資料 |
| --- | --- |
|**傳回 Order By 最佳化**|SELECT…ORDER BY 查詢會在此版本中獲得效能提升。   現在，所有計算節點都會將其結果傳送至單一計算節點。 此節點會合並並排序結果，並將其傳回給使用者。  當查詢結果集包含大量資料列時，透過單一計算節點進行合併會讓效能顯著提升。 過去，查詢執行引擎會在每個計算節點上排序結果。 結果會將它們串流處理至控制節點。 然後，控制節點會合並結果。|
|**適用於 PartitionMove 和 BroadcastMove 的資料移動增強功能**|在 Azure SQL 資料倉儲 Gen2 中，ShuffleMove 類型的資料移動步驟會使用即時資料移動技術。  如需詳細資訊，請參閱[效能增強功能的 blog](https://azure.microsoft.com/blog/lightning-fast-query-performance-with-azure-sql-data-warehouse/)。 在此版本中，PartitionMove 和 BroadcastMove 現在已由相同的即時資料移動技術提供支援。 使用這類資料移動步驟的使用者查詢會以改善的效能執行。 您無須變更任何程式碼，即可享有這些改進的效能。|
|**值得注意的錯誤 (bug)**|不正確的 Azure SQL 資料倉儲版本 `SELECT @@VERSION` 可能會傳回不正確的版本10.0.9999.0。 目前所發行的正確版本是 10.0.10106.0。 此錯誤 (bug) 已經被回報並正在進行檢閱。
| | |

### <a name="documentation-improvements"></a>文件改進功能

| 文件改進功能 | 詳細資料 |
| --- | --- |
|None | |
| | |

## <a name="december-2018"></a>2018 年 12 月

### <a name="service-improvements"></a>服務改進功能

| 服務改進功能 | 詳細資料 |
| --- | --- |
|**虛擬網路服務端點已正式運作**|此版本具有所有 Azure 區域內 Azure SQL 資料倉儲虛擬網路 (VNet) 服務端點的一般可用性。 VNet 服務端點讓您可隔離連線，從虛擬網路內的指定子網路或一組子網路連線到邏輯伺服器。 從 VNet 傳送至 Azure SQL 資料倉儲的流量一律會保留在 Azure 骨幹網路中。 這會優先使用此直接路由，而不是透過虛擬設備或內部部署傳送網際網路流量的任何特定路由。 透過服務端點存取虛擬網路並不會額外收費。 [Azure SQL 資料倉儲](https://azure.microsoft.com/pricing/details/sql-data-warehouse/gen2/)的目前定價模型照常適用。<br/><br/>透過此版本，我們也讓 PolyBase 能夠透過 [Azure Blob File System](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-introduction) (ABFS) 驅動程式連線到 [Azure Data Lake Storage Gen2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-abfs-driver) (ADLS)。 Azure Data Lake Storage Gen2 所具備的品質，足以滿足 Azure 儲存體分析資料完整生命週期的一切所需。 兩個現有 Azure 儲存體服務 (Azure Blob 儲存體與 Azure Data Lake Storage Gen1) 的功能均涵蓋在內。 [Azure Data Lake Storage Gen1](https://docs.microsoft.com/azure/data-lake-store/index) 的功能 (例如檔案系統語意、檔案層級安全性和級別) 結合了 [Azure Blob 儲存體](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-introduction)的低成本、分層式儲存體、高可用性/災害復原功能。<br/><br/>透過使用 Polybase，您也可將資料從固定於 VNet 的 Azure 儲存體匯入到 Azure SQL 資料倉儲。 同樣的，也支援經由 PolyBase 將資料從 Azure SQL 資料倉儲匯出至固定於 VNet 的 Azure 儲存體。<br/><br/>如需 Azure SQL 資料倉儲中 VNet 服務端點的詳細資訊，請參閱完整的[部落格文章](https://azure.microsoft.com/blog/general-availability-of-vnet-service-endpoints-for-azure-sql-data-warehouse/) (英文) 或[文件](https://docs.microsoft.com/azure/sql-database/sql-database-vnet-service-endpoint-rule-overview?toc=/azure/sql-data-warehouse/toc.json)。|
|**自動效能監視（預覽）**|[查詢存放區](https://docs.microsoft.com/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store?view=sql-server-2017)目前已可在 Azure SQL 資料倉儲預覽版中使用。 查詢存放區的設計目的是透過追蹤查詢、查詢計劃、執行階段統計資料以及查詢記錄等方式，協助您監視資料倉儲的活動與效能，藉此排解查詢效能的問題。 查詢存放區是一組內容存放區與「動態管理檢視」(DMV)，具備以下功能：<br/><br/>&bull; &nbsp; 識別並調整熱門資源取用查詢<br/>&bull; &nbsp; 識別並改善非計畫的工作負載<br/>&bull; &nbsp; 依據統計資料、索引或系統大小（DWU 設定）中的變更，評估計畫的查詢效能和影響<br/>&bull; &nbsp; 查看所有已執行查詢的完整查詢文字<br/><br/>查詢存放區包含三個實際存放區：<br/>&bull; &nbsp; 計畫存放區以保存執行計畫資訊<br/>&bull; &nbsp; 執行時間統計資料存放區，以保存執行統計資料資訊<br/>&bull; &nbsp; 等候統計資料存放區以保存等候統計資料資訊。<br/><br/>SQL 資料倉儲會自動管理這些存放區，並在過去七天內無需額外費用，即可 storied 不限數目的查詢。 啟用查詢存放區很簡單，只要執行 ALTER DATABASE T-SQL 陳述式即可： <br/>sql----ALTER DATABASE [DatabaseName] 設定 QUERY_STORE = ON;-------如需 Azure SQL 資料倉儲中查詢存放區的詳細資訊，請參閱[使用查詢存放區監視效能](/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store)和查詢存放區 dmv （例如 SYS）一文[。 query_store_查詢](/sql/relational-databases/system-catalog-views/sys-query-store-query-transact-sql)。 以下是版本發佈的[部落格文章](https://azure.microsoft.com/blog/automatic-performance-monitoring-in-azure-sql-data-warehouse-with-query-store/) (英文)。|
|**Azure SQL 資料倉儲 Gen2 的較低計算層級**|Azure SQL 資料倉儲 Gen2 現可支援較低的支援層級。 客戶可從 100 cDWU ([資料倉儲單位](what-is-a-data-warehouse-unit-dwu-cdwu.md)) 開始使用，在數分鐘內調整為 30,000 cDWU，體驗 Azure SQL 資料倉儲卓越的效能、彈性及安全性功能。 自 2018 年 12 月中起，這些[地區](gen2-migration-schedule.md#automated-schedule-and-region-availability-table)的客戶即可在較低的運算層級享有 Gen2 的效能和彈性，其餘地區則會在 2019 年跟進。<br/><br/>注重價值的客戶希望能全面評估安全的高效能資料倉儲的所有益處，且不想要猜測決定最適合的試用環境，而 Microsoft 降低了次世代資料倉儲服務的入門門檻，對這些客戶敞開了大門。 客戶最低可從 100 cDWU 開始，比目前 500 cDWU 的入門門檻更低。 SQL 資料倉儲 Gen2 會繼續支援暫停和繼續作業，超出只是計算的彈性。 Gen2 也支援無限制資料行存放區的儲存體容量，每個查詢的記憶體增加 2.5 倍，最多可達 128 個並行查詢，還具備[調適性快取](https://azure.microsoft.com/blog/adaptive-caching-powers-azure-sql-data-warehouse-performance-gains/)功能。 與同價格的 Gen1 一樣的資料倉儲單位相比，上述功能的效能平均高出五倍。 異地備援備份是內建保證資料保護之 Gen2 的標準功能。 Azure SQL Data Warehouse Gen2 時刻做好充足準備，只待您開始調整級別。|
|**資料行存放區背景合併**|根據預設，Azure SQL 資料倉儲 (Azure SQL DW) 會使用稱為[資料列群組](sql-data-warehouse-memory-optimizations-for-columnstore-compression.md)的微型分割，以資料行格式儲存資料。 有時候，因為在建置索引或載入資料時記憶體受到限制，資料列群組可能會經過壓縮，且使用的壓縮大小低於最佳的 1 百萬個資料列。 資料列群組也可能會因為刪除操作而變得零碎。 小型或零碎的資料列群組會耗用更高的記憶體，執行查詢時的效率也不好。 使用這一版的 Azure SQL DW 時，資料行存放區的背景維護工作會合併經過壓縮的小型資料列群組，以建立較大型的資料列群組，從而更加充分地利用記憶體並加快查詢的執行速度。
| | |

## <a name="october-2018"></a>2018 年 10 月

### <a name="service-improvements"></a>服務改進功能

| 服務改進功能 | 詳細資料 |
| --- | --- |
|**適用于資料倉儲的 DevOps**|SQL 資料倉儲 (SQL DW) 高度要求的功能目前為預覽狀態，Visual Studio 中支援資料工具 (SSDT)！ 開發人員小組現在可以透過單一的版本控制程式碼基底執行協同作業，並快速地將變更部署到全球任何執行個體。 有興趣加入嗎？ 此功能目前可供預覽！ 您可以透過造訪 [SQL 資料倉儲 Visual Studio SQL Server Data Tools (SSDT) - 預覽註冊表單](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR4-brmKy3TZOjoktwuHd7S1UODkwQ1lVMEw1NDBGRjNLRDNWOFlQRUpIRi4u)進行註冊。 鑑於需求量很大，我們正在對預覽進行驗收，以確保為客戶提供最佳體驗。 註冊後，我們的目標是在七個工作日內確認您的狀態。|
|**資料列層級安全性正式推出**|Azure SQL 資料倉儲 (SQL DW) 現在支援資料列層級安全性 (RLS)，增加了保護機密資料的強大功能。 藉由引進 RLS，您可以實作安全性原則來控制對資料表中資料列的存取，例如誰可以存取哪些資料列。 RLS 支援這種更細緻的存取控制，而不必重新設計您的資料倉儲。 RLS 簡化了整體安全性模型，因為存取限制邏輯位於資料庫層本身，而不是遠離另一個應用程式中的資料。 RLS 還消除了導入檢視以篩選掉用於存取控制管理之資料列的需要。 我們為所有客戶提供此企業級安全性功能，無需任何額外的費用。|
|**先進的顧問**|Azure SQL 資料倉儲 (SQL DW) 的進階微調透過額外的資料倉儲相關建議和計量變得更加簡單。 Azure Advisor 還提供其他進階的效能建議，包括：<br/><br/>1. 彈性快取–建議何時調整以優化快取使用率。<br/>2. 資料表散發–判斷何時複寫資料表以減少資料移動並提升工作負載效能。<br/>3. Tempdb –瞭解何時要調整和設定資源類別，以減少 Tempdb 競爭。<br/><br/>資料倉儲計量與 [Azure 監視器](https://azure.microsoft.com/blog/enhanced-capabilities-to-monitor-manage-and-integrate-sql-data-warehouse-in-the-azure-portal/)的整合更加深入，包括功能更強的可自訂監視圖表，能在概觀刀鋒視窗中提供近乎即時的計量。 當您監視使用量，或是驗證並套用資料倉儲建議時，不再需要離開資料倉儲概觀刀鋒視窗，即可存取 Azure 監視器計量。 此外，也提供新的計量 (例如 tempdb 及自適性快取使用率)，作為您效能建議的補充。|
|**整合式顧問的先進調整**|Azure SQL 資料倉儲 (SQL DW) 的進階微調透過額外的資料倉儲相關建議和計量以及入口網站概觀刀鋒視窗的重新設計變得更加簡單，該刀鋒視窗提供了 Azure Advisor 和 Azure 監視器的整合式體驗。|
|**加速資料庫復原（ADR）**|Azure SQL 資料倉儲加速資料庫復原 (ADR) 現在處於公開預覽狀態。 ADR 是新的 SQL 資料庫引擎功能，藉由完整重新設計目前的復原程序，來大幅改善資料庫可用性 (特別是在有長時間執行的交易時)。 ADR 的主要優點是快速且一致的資料庫復原，以及即時的交易回復。|
|**Azure 監視器診斷記錄**|藉由直接與 Azure 監視器診斷記錄整合，SQL 資料倉儲 (SQL DW) 現在提供更強的分析工作負載見解。 這項新功能可讓開發人員分析更長一段時間內的工作負載行為，並在充分了解資訊的情況下，做出查詢最佳化或容量管理決策。 我們現在透過 [Azure 監視器診斷記錄](https://docs.microsoft.com/azure/monitoring/monitoring-data-collection?toc=/azure/azure-monitor/toc.json#logs)引進了外部的記錄處理程序，這些記錄提供了有關資料倉儲工作負載的額外見解。 只要按一下按鈕，您就可以使用 [Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-queries) 為歷程查詢效能疑難排解功能設定診斷記錄。 Azure 監視器診斷記錄透過將記錄儲存至儲存體帳戶以供稽核，來支援可自訂的保留期間；也支援將記錄串流至事件中樞，以獲得近乎即時的遙測深入解析功能；以及使用具有記錄查詢之 Log Analytics 來分析記錄的功能。 診斷記錄是由您資料倉儲的遙測檢視所組成，其等同於 SQL 資料倉儲最常使用的效能疑難排解 DMV。 我們已為此初始版本啟用下列系統動態管理檢視的檢視：<br/><br/>&bull; &nbsp; [sys. dm_pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql)<br/>&bull; &nbsp; [sys. dm_pdw_request_steps](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-request-steps-transact-sql)<br/>&bull; &nbsp; [sys. dm_pdw_dms_workers](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-dms-workers-transact-sql)<br/>&bull; &nbsp; [sys. dm_pdw_waits](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-waits-transact-sql)<br/>&bull; &nbsp; [sys. dm_pdw_sql_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-sql-requests-transact-sql)|
|**資料行存放區記憶體管理**|隨著壓縮的資料行存放區資料列群組數目增加，管理這些資料列群組的內部資料行區段中繼資料所需的記憶體會隨之增加。  因此，查詢效能和針對某些資料行存放區動態管理檢視 (Dmv) 所執行的查詢可能會降級。  此版本已進行改善，讓這些情況的內部中繼資料大小達到最佳化，進而改善這類查詢的體驗和效能。|
|**Azure Data Lake Storage Gen2 整合（GA）**|Azure SQL 資料倉儲 (SQL DW) 現在已與 Azure Data Lake Storage Gen2 原生整合。 客戶現在可以使用 ABFS 中的外部資料表將資料載入至 SQL DW。 這項功能可讓客戶與其 Data Lake Storage Gen2 中的 Data Lake 整合。|
|**值得注意的錯誤 (bug)**|CETAS 至 Parquet DW2000 和更多資料倉儲上小型資源類別中的失敗-此修正會在 [建立外部資料表] 中將 null 參考正確地識別為 Parquet 程式碼路徑。<br/><br/>在某些 CTAS 作業中，識別資料行值可能會遺失-在 CTASed 至另一個資料表時，可能不會保留識別資料行的值。 在[blog](https://blog.westmonroepartners.com/azure-sql-dw-identity-column-bugs/)中回報。<br/><br/>當查詢仍在執行時，在某些情況下會發生內部失敗-如果在查詢仍在執行時終止會話，此修正會觸發 InvalidOperationException。<br/><br/>（部署于2018年11月）客戶在嘗試使用 Polybase 從 ADLS （Gen1）載入多個小型檔案時，遇到了效能不佳的情況。 -系統效能在 AAD 安全性權杖驗證期間發生瓶頸。 效能問題已藉由啟用安全性權杖的快取來降低影響。 |
| | |

## <a name="next-steps"></a>後續步驟

- [建立 SQL 資料倉儲](./create-data-warehouse-portal.md)

## <a name="more-information"></a>詳細資訊

- [部落格 - Azure SQL 資料倉儲](https://azure.microsoft.com/blog/tag/azure-sql-data-warehouse/) \(英文\)
- [客戶諮詢小組部落格](https://blogs.msdn.microsoft.com/sqlcat/tag/sql-dw/)
- [客戶成功案例](https://azure.microsoft.com/case-studies/?service=sql-data-warehouse)
- [Stack Overflow 論壇](https://stackoverflow.com/questions/tagged/azure-sqldw)
- [Twitter](https://twitter.com/hashtag/SQLDW)
- [影片](https://azure.microsoft.com/documentation/videos/index/?services=sql-data-warehouse)
- [Azure 詞彙](../azure-glossary-cloud-terminology.md)
