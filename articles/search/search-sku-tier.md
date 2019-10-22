---
title: 選擇 Azure 搜尋服務的定價層或 SKU - Azure 搜尋服務
description: 您可以在這些 Sku 中布建 Azure 搜尋服務：免費、基本和標準，而標準則適用于各種資源設定和容量層級。
services: search
author: HeidiSteen
manager: nitinme
tags: ''
ms.service: search
ms.topic: conceptual
ms.date: 08/15/2019
ms.author: heidist
ms.openlocfilehash: 32ec97ce923c1cffd92fa6522f30abf7ea87fff7
ms.sourcegitcommit: e0e6663a2d6672a9d916d64d14d63633934d2952
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/21/2019
ms.locfileid: "72331197"
---
# <a name="choose-a-pricing-tier-for-azure-search"></a>選擇 Azure 搜尋服務的定價層

當您建立 Azure 搜尋服務服務時，[資源會](search-create-service-portal.md)在服務存留期內固定的定價層（或 SKU）上建立。 層包括免費、基本、標準和儲存體優化。 標準和儲存體優化提供數個設定和容量。

大部分的客戶都是從免費層開始，讓他們可以評估服務。 評估後，通常會在開發和生產部署的其中一個較高層級建立第二個服務。

雖然所有層（包括免費層）通常都會提供功能同位，但較大的工作負載可能會要求較高的層級。 例如，[具有認知服務的 AI 擴充](cognitive-search-concept-intro.md)具有長時間執行的技能，這會在免費的服務上運作，除非資料集很小。

> [!NOTE] 
> 功能同位的例外狀況是無法在 S3 HD 上使用的[索引子](search-indexer-overview.md)。
>

<!-- For Basic tier and up, you can [adjust replica and partition resources](search-capacity-planning.md) to increase or decrease scale. You could start with one or two of each and then temporarily raise your computational power for a heavy indexing workload. The ability to tune resource levels within a tier adds flexibility, but also slightly complicates your analysis. You might have to experiment to see whether a lower tier with more resources/replicas offers better value and performance than a higher tier with fewer resources. To learn more about when and why you would adjust capacity, see [Performance and optimization considerations](search-performance-optimization.md). -->

## <a name="available-tiers"></a>可用的層級

層級會反映裝載服務之硬體的特性 (而非功能)，並會依下列方式來加以區分：

+ 您可以建立的索引和索引子數量
+ 分割區的大小和速度 (實體儲存體)

您選取的層級會決定可計費的費率。 下列來自 Azure 入口網站的螢幕擷取畫面顯示可用的層，減去定價（您可以在入口網站和[定價頁面](https://azure.microsoft.com/pricing/details/search/)上找到）。 「**免費**」、「**基本**」和「**標準**」是最常見的層級。

[**免費**] 會在叢集上建立受限的搜尋服務，與其他訂閱者共用。 您可以完成小型專案，包括快速入門和教學課程，但無法調整服務或執行重要的工作負載。 [**基本**] 和 [**標準**] 是最常用的可計費層，預設值是 [**標準**]。

![Azure 搜尋服務的定價層](media/search-sku-tier/tiers.png "Azure 搜尋服務的定價層")

某些層級已針對特定類型的工作進行優化。 例如，**標準3高密度（S3 HD）** 是 S3 的*主控模式*，其中基礎硬體已針對大量較小的索引進行優化，適用于多租使用者案例。 S3 HD 與 S3 具有相同的每單位費用，但是硬體已針對大量較小索引的快速檔案讀取優化。

**儲存體優化**層以比標準層低的價格提供更大的儲存體容量。 主要的取捨是較高的查詢延遲，您應該針對特定的應用程式需求進行驗證。  若要深入瞭解此層的效能考慮，請參閱[效能和優化考慮](search-performance-optimization.md)。

您可以在[定價頁面](https://azure.microsoft.com/pricing/details/search/)上找到有關各層的詳細資訊，請參閱[Azure 搜尋服務文章的服務限制](search-limits-quotas-capacity.md)一文，以及在布建服務時的入口網站頁面。

## <a name="billable-events"></a>可計費事件

以 Azure 搜尋服務為基礎的解決方案會以下列方式產生成本：

+ 最低設定服務的基本成本（建立服務）
+ 相應增加時的累加成本（新增複本或資料分割）
+ 頻寬費用（輸出資料傳輸） 
+ 認知搜尋（附加認知服務 AI 擴充、適用于知識存放區的 Azure 儲存體）

### <a name="service-costs"></a>服務成本

不同于可「暫停」以避免費用的虛擬機器或其他資源，Azure 搜尋服務服務一律可供您專屬使用的硬體使用。 因此，建立服務是一種可計費事件，會在您建立服務時啟動，並在您刪除服務時結束。 

最低費用是依計費費率的第一個搜尋單位（一個複本 x 一個資料分割）。 此最小值在服務的存留期間內已修正，因為服務無法在此設定的任何專案上執行。 除了最小值之外，您還可以單獨新增複本和分割區。 透過複本和分割區增量增加的容量，將會根據下列公式來增加您的帳單： [（複本 x 分割區 x 速率）](#search-units)，而您需支付的費用取決於您選取的定價層。

當您要預估搜尋解決方案的成本時，請記住，定價和容量不是線性的。 （容量加倍大於將成本加倍）。如需公式運作方式的範例，請參閱[如何配置複本和](search-capacity-planning.md#how-to-allocate-replicas-and-partitions)分割區。

### <a name="bandwidth-charges"></a>頻寬費用

根據服務的位置而定，使用[Azure 搜尋服務索引子](search-indexer-overview.md)可能會影響計費。 如果您在與資料相同的區域中建立 Azure 搜尋服務服務，則可以完全排除資料輸出費用。 以下是[頻寬定價頁面](https://azure.microsoft.com/pricing/details/bandwidth/)的一些資訊：

+ Microsoft 不會針對 Azure 上的任何服務或來自 Azure 搜尋服務的任何輸出資料，收取任何輸入資料的費用。
+ 在 multiservice 解決方案中，當所有服務都位於相同的區域時，就不會對跨越網路的資料收費。

如果服務位於不同的區域，則會收取輸出資料的費用。 這些費用並不是您 Azure 搜尋服務帳單的一部分。 這裡有提到，因為如果您使用資料或 AI 擴充的索引子，從不同的區域提取資料，您會看到整體帳單中反映的成本。

### <a name="cognitive-search-ai-enrichment-with-cognitive-services"></a>認知服務的認知搜尋 AI 擴充

針對[具有認知服務的 AI 擴充](cognitive-search-concept-intro.md)，您應該規劃[將可計費的 Azure 認知服務資源](cognitive-search-attach-cognitive-services.md)（位於與 Azure 搜尋服務相同的區域）連結到隨用隨付處理的 S0 定價層。 附加認知服務並沒有相關聯的固定成本。 您只需要支付所需的處理費用。

| 作業 | 計費影響 |
|-----------|----------------|
| 檔破解，文字解壓縮 | 免費 |
| 檔破解，影像解壓縮 | 根據從您的檔中解壓縮的影像數目來計費。 在[索引子](https://docs.microsoft.com/rest/api/searchservice/create-indexer#indexer-parameters)設定中， **imageAction**是用來觸發影像解壓縮的參數。 如果 [ **imageAction** ] 設定為 [無] （預設值），則不會向您收取影像解壓縮的費用。 影像解壓縮的速率會記錄在 Azure 搜尋服務的 [[定價詳細資料](https://azure.microsoft.com/pricing/details/search/)] 頁面上。|
| [預先建立的認知技能](cognitive-search-predefined-skills.md) | 以與您直接使用認知服務執行工作時相同的費率來計費。 |
| 自訂技能 | 自訂技能是您所提供的功能。 使用自訂技能的成本完全取決於自訂程式碼是否呼叫其他計量付費服務。 |

<a name="search-units"></a>

## <a name="billing-formula-r-x-p--su"></a>計費公式（R x P = SU）

若要瞭解 Azure 搜尋服務作業，最重要的計費概念是*搜尋單位*（SU）。 因為 Azure 搜尋服務需要複本和分割區才能編製索引和查詢，因此僅依據其中之一來計費沒有意義。 相反地，會將兩者合併來計費。

SU 是服務*所使用*之*複本*和資料分割的乘積： **（R x P = SU）** 。

每個服務都以一個 SU (一個複本乘以一個分割區) 作為最小值。 任何服務的最大值為 36 su。 您可以透過多種方式來達到此上限：6個數據分割 x 6 個複本，或3個分割區 x 12 個複本，例如。 通常會使用小於總容量（例如，3個複本、3個數據分割服務，以9個 su 計費）。 如需有效的組合，請參閱資料[分割和複本組合](search-capacity-planning.md#chart)圖表。

計費費率為每 SU 每小時。 每一層都有漸進較高的速率。 較高的層級隨附較大且 speedier 的資料分割，而這對於該階層的整體每小時費率也會增加。 您可以在 [[定價詳細資料](https://azure.microsoft.com/pricing/details/search/)] 頁面上，查看每一層的費率。

大多數客戶只能線上提供總容量的一部分，其餘部分則保留。 針對計費，您所上線的資料分割和複本數目（由 SU 公式計算）會決定您以小時為單位支付的費用。

## <a name="how-to-manage-and-reduce-costs"></a>如何管理和降低成本

除了下列建議以外，請造訪[計費和成本管理](https://docs.microsoft.com/azure/billing/billing-getting-started)。

- 在相同區域中建立所有資源，或盡可能在較少的區域中，以最小化或排除頻寬費用。

- 將所有服務合併成一個資源群組，例如 Azure 搜尋服務、認知服務，以及解決方案中使用的任何其他 Azure 服務。 在 Azure 入口網站中，尋找資源群組，並使用**成本管理**命令，以深入瞭解實際和預估的支出。

- 請考慮適用于您前端應用程式的 Azure Web 應用程式，讓要求和回應保留在資料中心界限內。

- 針對需要大量資源的作業（例如索引編制）進行相應增加，然後針對一般查詢工作負載重新調整。 從 Azure 搜尋服務的最小設定（一個由一個資料分割和一個複本組成的 SU）開始，然後監視使用者活動，以識別可能表示需要更多容量的使用模式。 如果有可預測的模式，您可以同步處理調整與活動（您需要撰寫程式碼來自動化這項作業）。

您無法關閉搜尋服務來減少帳單。 專用資源一律可運作，並配置給您的服務存留期專屬使用。 就服務本身而言，降低帳單的唯一方法，是將複本和分割區減少到仍然提供可接受的效能和[SLA 合規性](https://azure.microsoft.com/support/legal/sla/search/v1_0/)的層級，或在較低層建立服務（S1 每小時費率低於 S2 或 S3 速率）。 假設您在負載預測的低端布建服務，則您可以建立第二個較大的階層式服務，在第二個服務上重建索引，然後刪除第一個服務。

## <a name="how-to-evaluate-capacity-requirements"></a>如何評估容量需求

在 Azure 搜尋服務中，容量是由「複本」和「分割區」所構成。

+ 複本是搜尋服務的實例。 每個複本主控一個索引的負載平衡複本。 例如，具有六個複本的服務在服務中載入的每個索引都有6個複本。

+ 分割區會儲存索引，並自動分割可搜尋的資料。 兩個分割區會將索引分成一半、三個分割區分成三分之二，依此類推。 就容量而言，*分割區大小*是各層間主要的區分功能。

> [!NOTE]
> 所有標準和儲存體優化層都支援[彈性的複本和](search-capacity-planning.md#chart)分割區組合，讓您可以藉由變更餘額來[優化系統的速度或儲存](search-performance-optimization.md)。 基本層最多可提供三個複本以提供高可用性，但只有一個資料分割。 免費層不會提供專用資源：多個訂閱者會共用計算資源。

<!-- ## Consumption patterns

On the low and high ends, Basic and S3 HD are for important but atypical consumption patterns. Basic is for small production workloads. It offers SLAs, dedicated resources, and high availability, but it provides modest storage, topping out at 2 GB total. This tier was engineered for customers that consistently underutilize available capacity. At the high end, S3 HD is for workloads typical of ISVs, partners, [multitenant solutions](search-modeling-multitenant-saas-applications.md), or any configuration that calls for a large number of small indexes. It's often clear when Basic or S3 HD is the right tier. If you want confirmation, you can post to [StackOverflow](https://stackoverflow.com/questions/tagged/azure-search) or [contact Azure support](https://azure.microsoft.com/support/options/) for guidance.

The more commonly used standard tiers, S1 through S3, make up a progression of increasing levels of capacity. There are inflection points on partition size and limits on numbers of indexes, indexers, and corollary resources:

|  | S1 | S2 | S3 |  |  |  |  |
|--|----|----|----|--|--|--|--|
| Partition size|  25 GB | 100 GB | 200 GB |  |  |  |  |
| Index and indexer limits| 50 | 200 | 200 |  |  |  |  |

S1 is a common choice for customers that need dedicated resources and multiple partitions. S1 offers partitions of 25 GB and up to 12 partitions, providing a per-service limit of 300 GB if you maximize partitions over replicas. (See [Allocate partitions and replicas](search-capacity-planning.md#chart) for more balanced allocations.)

The portal and pricing pages put the focus on partition size and storage, but, for each tier, all compute capabilities (disk capacity, speed, CPUs) generally increase linearly with price. An S2 replica is faster than S1, and S3 is faster than S2. S3 tiers break from the linear compute-pricing pattern with disproportionately faster I/O. If you expect I/O to be the bottleneck, keep in mind that you can get much more IOPS with S3 than you can get with lower tiers.

S3 and S3 HD are backed by identical high-capacity infrastructure, but they reach their maximum limits in different ways. S3 targets a smaller number of very large indexes, so its maximum limit is resource-bound (2.4 TB for each service). S3 HD targets a large number of very small indexes. At 1,000 indexes, S3 HD reaches its limits in the form of index constraints. If you're an S3 HD customer and you need more than 1,000 indexes, contact Microsoft Support for information about how to proceed.

> [!NOTE]
> Document limits were a consideration at one time, but they're no longer applicable for new services. For information about conditions in which document limits still apply, see [Document limits](search-limits-quotas-capacity.md#document-limits).
>

Storage Optimized tiers, L1 and L2, are ideal for applications with large data requirements but a relatively low number of end users, when minimizing query latency isn't the top priority.  

|  | L1 | L2 |  |  |  |  |  |
|--|----|----|--|--|--|--|--|
| Partition size|  1 TB | 2 TB |  |  |  |  |  |
| Index and indexer limits| 10 | 10 |  |  |  |  |  |

L2 offers twice the overall storage capacity of L1.  Choose your tier based on the maximum amount of data that you think your index needs. The L1 tier partitions scale up in 1-TB increments to a maximum of 12 TB. The L2 partitions increase by 2 TBs per partition up to a maximum of 24 TB. -->

### <a name="evaluating-capacity"></a>評估容量

產能和執行服務的成本。 階層會限制兩個層級：儲存體和資源。 您應該考慮這兩個原因，因為您第一次達到的限制是有效的限制。

商務需求通常會決定您需要的索引數目。 例如，您可能需要大型檔存放庫的全域索引。 或者，您可能需要以區域、應用程式或業務的小型為基礎的多個索引。

若要判斷索引的大小，您必須[建置一個](search-create-index-portal.md)索引。 Azure 搜尋服務中的資料結構主要是[反向索引](https://en.wikipedia.org/wiki/Inverted_index)結構，其具有與來源資料不同的特性。 針對反向索引，大小和複雜度取決於內容，而不一定是您饋送至其中的資料量。 具有高冗余的大型資料來源可能會產生比包含高度變數內容的較小資料集更小的索引。 因此，很少可以根據原始資料集的大小來推斷索引大小。

> [!NOTE] 
> 即使估計索引和儲存空間的未來需求也可以像猜測一樣，但還是值得一試。 如果某一層的容量變得太低，您必須在較高的層級布建新的服務，然後[重載您的索引](search-howto-reindex.md)。 服務無法就地升級至另一個 SKU。
>

### <a name="estimate-with-the-free-tier"></a>使用免費層來預估

估計容量的一種方法是從免費層開始。 請記住，免費服務最多可提供三個索引、50 MB 的儲存空間，以及2分鐘的編制索引時間。 使用這些條件約束估計預計的索引大小可能會很困難，但這些步驟如下：

+ [建立免費服務](search-create-service-portal.md)。
+ 準備一個小型、具代表性的資料集。
+ [在入口網站中建立初始索引](search-create-index-portal.md)，並記下其大小。 功能和屬性會影響存放裝置。 例如，新增建議工具（自動提示）將會增加儲存需求。 使用相同的資料集，您可能會嘗試建立多個版本的索引，每個欄位各有不同的屬性，以查看儲存體需求的差異。 如需詳細資訊，請參閱[建立基本索引中的「儲存體影響](search-what-is-an-index.md#storage-implications)」。

使用粗略的估計值，您可以將兩個索引（開發和生產）的預算加倍，然後據以選擇您的定價層。

### <a name="estimate-with-a-billable-tier"></a>使用可計費層來預估

專用資源可以容納較大的取樣和處理時間，以在開發期間取得索引數量、大小和查詢磁片區的更實際估計。 有些客戶會直接進入可計費的層，然後在開發專案成熟時重新評估。

1. 請[參閱每一層的服務限制](https://docs.microsoft.com/azure/search/search-limits-quotas-capacity#index-limits)，以判斷較低層是否可以支援您所需的索引數目。 在基本、S1 和 S2 層中，索引限制分別為15、50和200。 儲存體優化層的限制為10個索引，因為它是設計來支援非常大的非常大型索引。

1. [在可計費的定價層建立服務](search-create-service-portal.md)：

    + 如果您不確定預計的負載，請從 [基本] 或 [S1] 開始。
    + 如果您知道您要進行大規模的索引編制和查詢負載，請從最高的 S2 開始，甚至是 S3。
    + 從儲存體優化（L1 或 L2）開始，如果您要編制大量資料的索引，而且查詢負載相對較低，就像內部商務應用程式一樣。

1. [建置初始索引](search-create-index-portal.md)，以判斷來源資料轉譯為索引的情形。 這是唯一能預估索引大小的方式。

1. 在入口網站中[監視儲存體、服務限制、查詢量和延遲](search-monitor-usage.md)。 入口網站會顯示每秒查詢數、節流查詢，以及搜尋延遲。 所有這些值都可協助您決定是否選取正確的層級。 

索引編號和大小對您的分析同樣重要。 這是因為最大限制是透過儲存體（資料分割）的完整使用量，或資源（索引、索引子等等）的最大限制來達到，視何者先發生。 入口網站可協助您追蹤這兩項，在 [概觀] 頁面上並排顯示目前的使用量和最大限制。

> [!NOTE]
> 如果檔包含無關的資料，則儲存需求可能會擴大。 在理想的情況下，檔只包含搜尋體驗所需的資料。 二進位資料無法搜尋，且應分別儲存（可能是在 Azure 資料表或 blob 儲存體中）。 接著，您應該在索引中加入一個欄位，以保存外部資料的 URL 參考。 個別檔的大小上限為 16 MB （如果您要在一個要求中大量上傳多個檔，則會較少）。 如需詳細資訊，請參閱 [Azure 搜尋服務中的服務限制](search-limits-quotas-capacity.md)。
>

**查詢量的考量**

在效能調整期間，每秒查詢數（QPS）是一項重要的計量，但如果您預計會在一開始就有高查詢量，通常只會有一層考慮。

標準層可以提供複本和分割區的平衡。 您可以加入負載平衡的複本，或加入分割區以進行平行處理，藉此增加查詢週期。 接著，您可以在布建服務之後調整效能。

如果您預計從一開始就有很大的查詢量，您應該考慮較高的標準層，並以更強大的硬體做為後盾。 接著，您可以讓分割區和複本離線，或甚至切換至較低層的服務（如果未發生這些查詢磁片區）。 如需如何計算查詢輸送量的詳細資訊，請參閱 [Azure 搜尋服務效能和最佳化](search-performance-optimization.md)。

儲存體優化層適用于大型資料工作負載，在查詢延遲需求較不重要時，支援更高的整體可用索引儲存體。 您仍然應該使用額外的複本來進行負載平衡和其他分割區，以進行平行處理。 接著，您可以在布建服務之後調整效能。

**服務等級協定**

免費層和預覽功能不提供[服務等級協定（sla）](https://azure.microsoft.com/support/legal/sla/search/v1_0/)。 在所有可計費層中，SLA 會在您為您的服務佈建足夠的備援性時生效。 您需要有兩個或以上的複本，才能進行查詢（讀取） Sla。 您需要有三個或更多複本，才能進行查詢和索引（讀寫） Sla。 磁碟分割數目不會影響 Sla。

## <a name="tips-for-tier-evaluation"></a>定價層評估秘訣

+ 允許以查詢為依據的計量，並收集使用模式的資料（工作時間內的查詢，在離峰時段編制索引）。 使用此資料來通知服務布建決策。 雖然這不是每小時或每日的頻率，但是您可以動態調整資料分割和資源，以配合查詢磁片區中的計畫變更。 如果層級夠長，而足以保證採取動作，您也可以納入未規劃但持續的變更。

+ 請記住，underprovisioning 的唯一缺點是，如果實際需求大於預測，您可能必須卸載服務。 為避免服務中斷，建議您在相同訂用帳戶中使用更高的定價層建立一個新服務，並讓此服務並行執行，直到所有應用程式和要求都以新端點為目標才停止。

## <a name="next-steps"></a>後續步驟

從免費層開始，並使用您資料的子集來建立初始索引，以瞭解其特性。 Azure 搜尋服務中的資料結構是反向的索引結構。 反向索引的大小和複雜性取決於內容。 請記住，重複性高的內容所產生的索引，往往會比差異性高的內容所產生的索引小。 因此，內容特性，而不是資料集的大小，會決定索引儲存需求。

在您有索引大小的初始估計之後，請在本文所討論的其中一個層級上布建可[計費服務](search-create-service-portal.md)：基本、標準或儲存體優化。 放寬資料大小的任何人工條件約束，並[重建您的索引](search-howto-reindex.md)，以包含您想要搜尋的所有資料。

視需要[配置資料分割和複本](search-capacity-planning.md)，以取得所需的效能和規模。

如果效能和容量都沒問題，您就大功告成了。 否則，請在更符合需求的不同定價層重新建立搜尋服務。

> [!NOTE]
> 如有任何疑問，請張貼至[StackOverflow](https://stackoverflow.com/questions/tagged/azure-search)或[連絡人 Azure 支援](https://azure.microsoft.com/support/options/)。
