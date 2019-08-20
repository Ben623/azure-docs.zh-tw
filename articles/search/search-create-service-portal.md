---
title: 在入口網站中建立 Azure 搜尋服務 - Azure 搜尋服務
description: 在 Azure 入口網站中佈建 Azure 搜尋服務資源。 選擇資源群組、區域、SKU 或定價層。
manager: cgronlun
author: HeidiSteen
services: search
ms.service: search
ms.topic: quickstart
ms.date: 08/09/2019
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: 0649fea0b598ffaaaf2611c9d1324174105ee5d4
ms.sourcegitcommit: 13a289ba57cfae728831e6d38b7f82dae165e59d
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/09/2019
ms.locfileid: "68931529"
---
# <a name="create-an-azure-search-service-in-the-portal"></a>在入口網站中建立 Azure 搜尋服務

Azure 搜尋服務是一項可用來在自訂應用程式中插入搜尋體驗的獨立資源。 雖然 Azure 搜尋服務可與其他 Azure 服務輕易整合，但您也可以將它當做單獨元件使用、將其與網路伺服器上的應用程式整合，或與其他雲端平台上執行的軟體搭配使用。

在本文中，請了解如何在 [Azure 入口網站](https://portal.azure.com/)中建立 Azure 搜尋服務資源。

[![動畫 GIF](./media/search-create-service-portal/AnimatedGif-AzureSearch-small.gif)](./media/search-create-service-portal/AnimatedGif-AzureSearch.gif#lightbox)

是否偏好使用 PowerShell？ 請使用 Azure Resource Manager [服務範本](https://azure.microsoft.com/resources/templates/101-azure-search-create/)。 如需入門說明，請參閱[使用 PowerShell 管理 Azure 搜尋服務](search-manage-powershell.md)。

## <a name="subscribe-free-or-paid"></a>訂閱 (免費或付費)

[開啟免費的 Azure 帳戶](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F)，並使用免費信用額度來試用付費的 Azure 服務。 當您用完信用額度之後，請保留帳戶，並繼續使用免費的 Azure 服務，例如網站。 除非您明確變更您的設定且同意付費，否則我們絕對不會從您的信用卡收取任何費用。

或者，請[啟用 MSDN 訂閱者權益](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F)。 MSDN 訂用帳戶每月會提供您信用額度，讓您可以用於 Azure 付費服務。 

## <a name="find-azure-search"></a>尋找 Azure 搜尋服務

1. 登入 [Azure 入口網站](https://portal.azure.com/)。
2. 按一下左上角的加號 (「+ 建立資源」)。
3. 使用搜尋列尋找「Azure 搜尋服務」，或透過 [Web]   > [Azure 搜尋服務]  瀏覽至資源。

![瀏覽至 Azure 搜尋服務資源](./media/search-create-service-portal/find-search3.png "瀏覽至 Azure 搜尋服務的路徑")

## <a name="select-a-subscription"></a>選取一個訂用帳戶

如果您有一個以上的訂用帳戶，請選擇一個同樣具有資料或檔案儲存體服務的訂用帳戶。 Azure 搜尋服務可以透過[索引子  ](search-indexer-overview.md)自動偵測 Azure 資料表和 Blob 儲存體、SQL Database 和 Azure Cosmos DB 以進行索引編製，但只會針對相同訂用帳戶下的服務。

## <a name="set-a-resource-group"></a>設定資源群組

資源群組是必要的，可用於管理所有資源，包括成本管理。 資源群組可以包含一個服務，或多個一起使用的服務。 例如，如果您使用 Azure 搜尋服務來編製 Azure Cosmos DB 資料庫的索引，您就可以將這兩個服務納入相同的資源群組，以便管理。 

如果您不想將資源結合成單一群組，或現有的資源群組中有許多資源用於不相關的解決方案中，請為您的 Azure 搜尋服務資源建立專屬的新資源群組。 

當您使用此服務時，您可以追蹤目前和預估的成本 (如螢幕擷取畫面所示)，或向下捲動以查看個別資源的費用。

![在資源群組層級上管理成本](./media/search-create-service-portal/resource-group-cost-management.png "在資源群組層級上管理成本")

> [!TIP]
> 刪除資源群組也會刪除其中的服務。 針對使用多個服務的原型專案，將它們全部放入同一個資源群組，在專案結束之後就能更容易清除。

## <a name="name-the-service"></a>為服務命名

在 [執行個體詳細資料] 的 [URL]  欄位中提供服務名稱。 該名稱是 URL 端點的一部分，API 呼叫是根據此端點所發出：`https://your-service-name.search.windows.net`。 例如，如果您所需的端點是 `https://myservice.search.windows.net`，則應輸入 `myservice`。

服務名稱需求：

* 此名稱在 search.windows.net 命名空間中必須是唯一名稱
* 長度為 2 到 60 個字元
* 使用小寫字母、數字或連字號 ("-")
* 前 2 個字元或最後一個字元不可使用連字號 ("-")
* 不能連續使用破折號 ("--")

> [!TIP]
> 如果您認為您將使用多個服務，建議您在服務名稱中包含區域 (或位置) 以作為為命名慣例。 相同區域內的服務可以免費交換資料，因此，如果 Azure 搜尋服務位於美國西部，而您在美國西部也有其他服務，則類似 `mysearchservice-westus` 的名稱可讓您在決定如何結合或連結資源時，為您省去前往 [內容] 頁面的步驟。

## <a name="choose-a-location"></a>選擇位置

做為 Azure 服務，Azure 搜尋服務可以裝載於世界各地的資料中心。 支援的區域清單可在[定價頁面](https://azure.microsoft.com/pricing/details/search/)中找到。 

您可以為多個服務選擇相同的位置，以盡量減少或避免產生頻寬費用。 例如，如果您要依其他 Azure 服務 (Azure 儲存體、Azure Cosmos DB、Azure SQL Database) 提供的資料編製索引，請在相同區域中建立 Azure 搜尋服務，以避免產生頻寬費用 (當服務位於相同區域時，輸出資料不會產生費用)。

此外，如果您使用認知服務 AI 擴充資料，請在認知服務資源所在的區域中建立服務。 *將 Azure 搜尋服務與認知服務共置於相同區域中是 AI 擴充的一項需求*。

> [!Note]
> 新的服務目前無法在印度中部使用。 對於已在印度中部提供的服務，您可以無限制地相應增加，且您的服務在該區域中受到完整的支援。 此區域的限制是暫時的且僅限於新的服務。 當限制不再適用時，我們將移除此注意事項。

## <a name="choose-a-pricing-tier-sku"></a>選擇定價層 (SKU)

[Azure 搜尋服務目前提供多個定價層](https://azure.microsoft.com/pricing/details/search/)︰免費、基本或標準。 每一層都有自己的[容量和限制](search-limits-quotas-capacity.md)。 請參閱[選擇定價層或 SKU](search-sku-tier.md) 以取得指導方針。

一般對於生產工作負載通常會選擇基本和標準服務，但大部分的客戶一開始都會使用免費服務。 層級之間的主要差異在於資料分割大小和速度，以及您可以建立的物件數目限制。

請記住，服務建立之後便無法變更定價層。 如果您稍後需要高或較低的定價層，必須重新建立服務。

## <a name="create-your-service"></a>建立您的服務

在您提供必要的輸入之後，請繼續並建立服務。 

![檢閱並建立服務](./media/search-create-service-portal/new-service3.png "檢閱並建立服務")

您的服務會在幾分鐘內部署，而您可透過 Azure 通知進行監視。 請考慮將服務釘選在儀表板上，以便日後存取。

![監視及釘選服務](./media/search-create-service-portal/monitor-notifications.png "監視及釘選服務")

## <a name="get-a-key-and-url-endpoint"></a>取得金鑰和 URL 端點

除非您使用入口網站，否則以程式設計方式存取新服務時，您需要提供 URL 端點和授權 API 金鑰。

1. 在服務概觀頁面中，找出並複製頁面右側的 URL 端點。

2. 在左側導覽窗格中選取 [金鑰]  ，然後複製任一系統管理員金鑰 (它們是相等的)。 在您的服務上建立、更新及刪除物件時，需要系統管理員 API 金鑰。

   ![包含 URL 端點的服務概觀頁面](./media/search-create-service-portal/get-url-key.png "URL 端點和其他服務詳細資料")

入口網站工作不需要端點和金鑰。 入口網站已透過系統管理員權限連結至您的 Azure 搜尋服務資源。 如需入口網站逐步解說，請從[快速入門：在入口網站中建立 Azure 搜尋服務索引](search-get-started-portal.md)著手。

## <a name="scale-your-service"></a>調整您的服務

佈建完您的服務之後，您可以調整它以符合您的需求。 如果您為 Azure 搜尋服務選擇了「標準」層，即可在兩個維度調整服務︰複本和資料分割。 如果您選擇的是「基本」層，則只能新增複本。 如果您佈建的是免費服務，則無法進行調整。

「資料分割」允許您的服務儲存及搜尋更多文件。

「複本」允許服務來處理更高的搜尋查詢負載。

新增資源會增加每月的帳單。 [定價計算機](https://azure.microsoft.com/pricing/calculator/)可協助您了解新增資源對帳單的影響。 請記住，您可以根據負載來調整資源。 例如，您可增加資源來建立完整的初始索引，並於稍後將資源減少到較適合累加式編製索引的層級。

> [!Important]
> 服務必須具有 [2 個唯讀 SLA 的複本和 3 個讀/寫 SLA 的複本](https://azure.microsoft.com/support/legal/sla/search/v1_0/)。

1. 在 Azure 入口網站中移至您的搜尋服務頁面。
2. 在左導覽窗格中，選取 [設定]   > [調整]  。
3. 您可以使用滑桿來新增任何一種類型的資源。

![新增容量](./media/search-create-service-portal/settings-scale.png "透過複本和分割區新增容量")

> [!Note]
> 每個分割區的儲存體和速度均以更高的層次增加。 如需詳細資訊，請參閱[容量和限制](search-limits-quotas-capacity.md)。

## <a name="when-to-add-a-second-service"></a>新增第二個服務的時機

大部分的客戶都是使用在提供[正確資源平衡](search-sku-tier.md)的層次上佈建的單一服務。 單一服務可以裝載多個索引 (數量受限於[所選層的最大限制](search-capacity-planning.md))，且每個索引都互相隔離。 在 Azure 搜尋服務中，要求只能導向到單一索引，以降低意外或刻意從相同服務的其他索引中擷取資料的機會。

雖然大部分的客戶只使用單一服務，但如果操作需求包含下列項目，則可能需要服務備援：

* 災害復原 (資料中心中斷)。 Azure 搜尋服務在中斷時不會提供即時容錯移轉。 如需建議和指導方針，請參閱[服務管理](search-manage.md)。
* 您的多租用戶模型調查判斷額外服務為最佳化設計。 如需詳細資訊，請參閱[針對多租用戶設計](search-modeling-multitenant-saas-applications.md)。
* 針對全球部署的應用程式，您在多個區域可能都需要 Azure 搜尋服務執行個體，以降低應用程式國際流量的延遲。

> [!NOTE]
> 在 Azure 搜尋服務中，您無法區隔索引和查詢作業，因此您永遠不會針對區隔的工作負載建立多重服務。 一律是在建立索引的服務上查詢該索引 (您無法在某個服務中建立索引，並將它複製到另一個服務)。

不需要第二個服務即可獲得高可用性。 當您在同一個服務中使用 2 個或更多的複本，查詢就會達到高可用性。 複本更新是循序的，這表示當服務更新推出時，至少會有一個複本是可運作的。如需執行時間的詳細資訊，請參閱[服務等級協定](https://azure.microsoft.com/support/legal/sla/search/v1_0/)。

## <a name="next-steps"></a>後續步驟

佈建 Azure 搜尋服務之後，您可以繼續在入口網站中建立第一個索引。

> [!div class="nextstepaction"]
> [快速入門：在入口網站中建立 Azure 搜尋服務索引](search-get-started-portal.md)
