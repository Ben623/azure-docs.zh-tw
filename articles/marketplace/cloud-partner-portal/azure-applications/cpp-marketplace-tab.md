---
title: Azure 應用程式供應專案 Marketplace 索引標籤
description: 使用 [Marketplace] 索引標籤來識別 Azure 應用程式供應項目的行銷資產。
services: Azure, Marketplace, Cloud Partner Portal,
author: dan-wesley
ms.service: marketplace
ms.topic: conceptual
ms.date: 04/23/2019
ms.author: pabutler
ms.openlocfilehash: b5d54e4c6744675da0dea5b095590b71df6d2641
ms.sourcegitcommit: dd69b3cda2d722b7aecce5b9bd3eb9b7fbf9dc0a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/12/2019
ms.locfileid: "70962869"
---
# <a name="azure-application-marketplace-tab"></a>Azure 應用程式 Marketplace 索引標籤

使用 [Marketplace] 索引標籤來說明 Azure 應用程式並提供行銷資產。 此索引標籤包含下列表單：概觀、行銷成品、潛在客戶管理以及法律。

## <a name="overview-form"></a>概觀表單

「概觀」表單中包含下一個螢幕擷取畫面所示的必要欄位和選擇性欄位。 必要欄位由星號 (*) 標示。

![概觀表單](./media/azureapp-marketplace-overview.png)

下表說明要用於建立供應項目店面的設定。   以星號附加的欄位是必要的。

|      欄位         |    描述    |
|  ---------------   |  ---------------  |
| **主題\***        | 供應項目的標題。 此標題會在 Marketplace 中醒目顯示。 長度上限是 50 個字元。 |
| **總計\***      | 供應項目的簡短摘要。 長度上限是 100 個字元。           |
| **完整摘要\*** | 供應項目的完整摘要 (雖然可能與摘要相同)。 長度上限是 256 個字元。           |
| **說明\***  | 供應項目的說明。 長度上限是 3000 個字元。 允許簡單的 HTML 格式，包括 &lt;p&gt;、&lt;em&gt;、&lt;ul&gt;、&lt;li&gt;&lt;ol&gt; 和標題標記。  |
| **行銷識別碼\*** | 與本供應項目相關聯的唯一不重複網址，通常會包含您的組織與解決方案名稱，長度上限是 50 個字元。 為您的服務選擇簡短、易讀的行銷識別碼。 這將用於此供應項目的市集 URL。 例如，如果您的發行者識別碼為 "contoso"，而行銷識別碼是 "sampleApp"，則 Azure Marketplace 中供應項目的 URL 會是 https://azuremarketplace.microsoft.com/marketplace/apps/contoso.sampleApp  
| **預覽訂用帳戶識別碼\*** | 可新增從 1 到 100 個預覽使用者的訂閱帳戶識別碼。 這些列入允許清單的訂閱帳戶，待供應項目發佈後可供預覽時，即能在供應項目上線前先行存取。          |
| **實用連結**    | （選擇性）您可以為供應專案的使用者提供各種資源的連結，例如支援、檔、論壇等。建議您至少在檔中加入一個連結。            |
| **建議的類別（最多5個）\*** | 選取一到五個類別。 選取的類別係用來將您提供的供應項目對應至 Azure Marketplace 和 Azure 入口網站中提供的產品類別。 這些類別會顯示在瀏覽頁面和您的產品詳細資料頁面上。 |
|  |  |


## <a name="marketing-artifacts"></a>行銷成品

「行銷成品」表單中包含下一個螢幕擷取畫面所示的必要欄位和選擇性欄位。 必要欄位由星號 (*) 標示。

![行銷成品表單](./media/azureapp-marketplace-artifacts.png)

下表說明行銷成品。

|      欄位         |    描述    |
|  ---------------   |  ---------------  |
| **小型\***        | 小型標誌：40x40 像素 (PNG 格式)     |
| **中型\***       | 中標誌：以 PNG 格式90x90 圖元    |
| **大型\***        | 大型標誌：以 PNG 格式115x115 圖元   |
| **廣\***         | 寬標誌：以 PNG 格式255x115 圖元    |
| **主圖**           | 選擇性主圖示志：以 PNG 格式815x290 圖元。 **注意：** 主圖圖示上傳之後，便無法刪除。 |
| **螢幕擷取畫面 (最多 5 個)** |        螢幕擷取畫面會顯示在您的產品詳細資料頁面上， 相當適合用來以視覺方式傳達應用程式的功能及其運作方式。 例如，可以顯示架構圖表或是使用案例圖例。 螢幕擷取畫面是選擇性的，每個 SKU 限制為 5 個。 若要新增螢幕擷取畫面：<ul><li>選取 [+ 新增螢幕擷取畫面] 以開啟 [螢幕擷取畫面] 視窗</li><li>**名稱** - 輸入名稱/標題 (長度上限是 100 個字元)。</li><li>**上傳** - 上傳映像。 它必須是 PNG 格式，而大小為 533x324 像素。</li></ul>           |
| **新增影片**      | （選擇性）影片會顯示在您的產品詳細資料頁面上。 相當適合用來以視覺方式傳達應用程式的功能及其運作方式。 若要新增影片： <ul><li>選取 [+ 新增影片] 以開啟 [影片] 視窗</li><li>**名稱** - 輸入名稱/標題 (長度上限是 100 個字元)。</li><li>**連結** - 輸入裝載影片的站台 URL (YouTube 或 Vimeo)</li><li>**縮圖** – 上傳縮圖。 它必須是 PNG 格式，而大小為 533x324 像素。</li></ul>          |
|  |  |


### <a name="artifact-examples-in-azure-marketplace"></a>Azure Marketplace 中的成品範例

下一個螢幕擷取畫面會顯示 Marketplace 搜尋結果的範例。

![Marketplace 供應項目搜尋結果](./media/azureapp-marketplace-example-browse.png)

下圖顯示當客戶按一下搜尋結果中的供應項目圖格後，供應項目在 Marketplace 中的顯示方式。

![Marketplace 供應項目搜尋結果詳細資料](./media/azureapp-marketplace-example-details.png)


### <a name="artifact-examples-in-azure-portal"></a>Azure 入口網站中的成品範例

下列螢幕擷取畫面顯示供應項目在 Azure 入口網站中的顯示方式。 您可以瀏覽至 **Marketplace>Everything>Dev + Test>Jenkins**，即可找到此範例中的應用程式供應項目。 Jenkins 供應項目會顯示標誌、標題和發行者顯示名稱。

![瀏覽 Azure 入口網站中的供應項目](./media/azureapp-portalbrowse-artifacts-jenkins.png)

當使用者選取 Jenkins 時，下一個螢幕擷取畫面會顯示應用程式的詳細資訊。

![Azure 入口網站中的供應項目詳細資料](./media/azureapp-portal-artifacts-jenkins-details.png)


### <a name="logo-guidelines"></a>標誌指導方針

Cloud Partner 入口網站中所上傳的所有標誌都應該遵循指導方針︰

- Azure 設計具有簡單的調色盤。 請保持標誌上具有最少的主要和次要色彩數目。
- Azure 入口網站的佈景主題色彩是白色與黑色。 請避免使用這些色彩做為您標誌的背景色彩。 請使用可在 Azure 入口網站突顯您的標誌的色彩。 建議您使用簡單的主要色彩。 如果您使用透明背景，請確定標誌/文字不是白色、黑色或藍色。
- 不要在標誌上使用漸層背景。
- 避免在標誌上放置文字 (甚至是公司或品牌名稱)。 您標誌的外觀與風格應該很「平面」，而且應避免使用漸層。
- 不要延展標誌。


#### <a name="hero-logo"></a>主圖標誌

主圖標誌為選用項。

>[!IMPORTANT]
>主圖圖示上傳之後，便無法刪除。

請針對主圖標誌使用下列指引：

- 不允許使用黑色、白色與透明背景。
- 避免在背景中使用任何淺色的標誌。 發行者顯示名稱、方案標題和供應項目完整摘要會以白色字型色彩顯示，而且必會和背景有明顯對比。
- 設計標誌時，請避免以文字為主體。 供應項目刊出之後，會透過程式設計方式，將發行者名稱、方案標題、供應項目完整摘要以及 [建立] 按鈕內嵌在標誌內。
- 請在主圖標誌的右側加入一個未使用的矩形空間。 此空白區域需為 415x100 像素，並從左側位移 370 像素。


## <a name="lead-management"></a>潛在客戶管理

潛在客戶管理表單有選擇性的欄位可以設定潛在客戶管理。 若要設定潛在客戶管理，請從下拉式清單中選取潛在客戶目的地。 下一個螢幕擷取畫面顯示可用的目的地。

![選取潛在客戶管理目的地](./media/azureapp-marketplace-leadmgmt.png)

>[!TIP]
>選取資訊圖示，即可看到此訊息：「選取將儲存您潛在客戶的系統。 在[這裡](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-get-customer-leads)了解如何連線 CRM 系統。」

如需詳細資訊，請參閱[設定潛在客戶](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-get-customer-leads)。


## <a name="legal"></a>法律聲明

使用法律形式為每個供應項目提供所需的法律文件。

請提供下列資訊：

- **隱私權原則 URL\***  –輸入應用程式隱私權原則的連結。
- **使用規定\***  –輸入應用程式的使用條款。 客戶必須先接受這些條款，才能試用您的應用程式。

![法律形式](./media/azureapp-marketplace-legal.png)


## <a name="next-steps"></a>後續步驟

[支援索引標籤](./cpp-support-tab.md)
