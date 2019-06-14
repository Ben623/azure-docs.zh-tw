---
title: Azure 計量瀏覽器的進階的功能
description: 深入了解 Azure 監視器計量瀏覽器進階功能
author: vgorbenko
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 01/22/2019
ms.author: vitalyg
ms.subservice: metrics
ms.openlocfilehash: 67e4281b24a7489cf202d82bdddbe99992aac095
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "60256871"
---
# <a name="advanced-features-of-azure-metrics-explorer"></a>Azure 計量瀏覽器的進階的功能

> [!NOTE]
> 本文假設您已熟悉基本的計量瀏覽器的功能。 如果您是新的使用者，而且想要了解如何建立您第一次的計量圖表，請參閱[開始使用 Azure 計量瀏覽器](metrics-getting-started.md)。

## <a name="metrics-in-azure"></a>Azure 中的計量

[Azure 監視器](data-platform-metrics.md)中的計量是隨時間收集並儲存的一系列度量與計數值。 計量包括標準 (或稱為「平台」) 計量與自訂計量。 標準計量是由 Azure 平台本身提供給您使用。 標準計量反映您 Azure 資源的健康情況與使用情形統計資料。 而自訂計量時，會傳送至 Azure 上，使用的應用程式所[自訂事件和度量的 Application Insights API](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics)， [Windows Azure 診斷 (WAD) extension](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostics-extension-overview)，或由[Azure監視 REST API](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-store-custom-rest-api)。

## <a name="create-views-with-multiple-metrics-and-charts"></a>建立具有多個度量和圖表的檢視

您可以建立圖表的繪製多個計量行或一次顯示多個度量的圖表。 這項功能可讓您：

- 在相同的圖形，請參閱如何一個值上相互關聯相關的度量資訊與另一個
- 顯示具有不同非常接近的測量單位的度量
- 以視覺化方式彙總並比較多個資源的計量

例如，如果您有 5 個儲存體帳戶，而且您想要知道多少總空間用完其間，您可以建立 （堆疊） 區域圖會顯示所有值的總和與個人在特定時間點的時間。

### <a name="multiple-metrics-on-the-same-chart"></a>在相同圖表上的多個計量

首先，[建立新的圖表](metrics-getting-started.md#create-your-first-metric-chart)。 按一下 **新增計量**重複步驟，在相同圖表上新增其他計量。

   > [!NOTE]
   > 您一般不會想要有具有不同計量單位的計量 (例如「毫秒」與 “KB”) 或其刻度在單一圖表上會呈現大幅差異的計量。 如果是這樣，您應該考慮使用多個圖表。 按一下 [新增圖表] 按鈕以在計量瀏覽器中建立多個圖表。

### <a name="multiple-charts"></a>多個圖表

按一下 **新增圖表**，並使用不同的計量建立另一個圖表。

### <a name="order-or-delete-multiple-charts"></a>排列或刪除多個圖表

若要排序或刪除多個圖表，請按一下省略符號 ( **...** ) 符號以開啟 [圖表] 功能表，然後選擇適當的功能表項目**向上移動**，**下移**，或**刪除**。

## <a name="apply-filters-to-charts"></a>將篩選條件套用至圖表

您可以將篩選器套用到以多個維度顯示計量的圖表。 例如，若「交易計數」計量有「回應類型」維度 (指出來自交易的回應成功或失敗)，則在此維度上篩選將只為成功 (或失敗) 的交易繪製圖表線條。 

### <a name="to-add-a-filter"></a>加入篩選條件

1. 選取圖表上方的 [新增篩選器] 

2. 選取您要篩選的維度 (屬性)

   ![計量影像](./media/metrics-charts/00006.png)

3. 選取當繪製圖表時要包括的維度值 (此範例會顯示篩選出成功的交易)：

   ![計量影像](./media/metrics-charts/00007.png)

4. 選取篩選器值之後，請按一下 [篩選器選取器] 以外的位置以將它關閉。 現在圖表會顯示失敗的儲存體交易數目：

   ![計量影像](./media/metrics-charts/00008.png)

5. 您可以重複步驟 1-4 以將多個篩選器套用到相同的圖表。



## <a name="apply-splitting-to-a-chart"></a>套用至圖表分割

您可以依維度來分割計量，以視覺化方式顯示維度的各區段與彼此之間的差異，並識別維度中與其他區段差異較大的區段。

### <a name="apply-splitting"></a>套用分割

1. 按一下圖表上方的 [套用分割]  。
 
   > [!NOTE]
   > 分割不能有多個度量的圖表。 此外，您可以有多個篩選器，但只有一個分割的維度，套用至任何單一的圖表。

2. 選擇要據以將圖表分割為不同區段的維度：

   ![計量影像](./media/metrics-charts/00010.png)

   現在圖表會顯示多行，每個維度區段一行：

   ![計量影像](./media/metrics-charts/00012.png)

3. 按一下 [分組選取器]  以外的位置以將它關閉。

   > [!NOTE]
   > 在相同維度上同時使用篩選與分割，以隱藏與您的案例不相關的區段，讓圖表更容易閱讀。

## <a name="lock-boundaries-of-chart-y-axis"></a>鎖定圖表 y 軸的界限

圖表顯示的較大值的小波動時，鎖定 y 軸的範圍相當重要。 

例如，成功要求的數量從 99.99% 下降到 99.5% 時，可能代表服務的品質大幅降低。 不過，或許很難甚至無法從預設圖表設定注意到數值的小波動。 在此情況下，您可以將圖表的最低的界限鎖定為 99%，使得這種小幅度下降變得更明顯。 

另一個範例是可用記憶體的波動，在技術上，值永遠不會變成 0。 將範圍修正為較高的值可以更明顯發現可用記憶體減少。 

若要控制 y 軸範圍，請使用 [...] 圖表功能表，然後選取**編輯圖表**存取進階圖表設定。 修改 [y 軸範圍] 區段中的值，或使用**自動**按鈕還原為預設值。

![計量影像](./media/metrics-charts/00014-manually-set-granularity.png)

> [!WARNING]
> 鎖定圖表的 y 軸界限可追蹤一段時間的各種計算或加總，因此 (使用計數、加總、最小或最大彙總) 通常需要指定固定時間細微性，而不依賴自動預設值。 因為使用者調整瀏覽器視窗的大小，或從一個螢幕解析度變換為另一個螢幕解析度時，時間細微性經過修改，會導致圖表上的值變更，因此這有其必要性。 時間細微性中產生的變更會影響圖表的外觀，導致目前選取的 y 軸範圍無效。

## <a name="pin-charts-to-dashboards"></a>將圖表釘選到儀表板

設定圖表之後，您可以將它加入到儀表板，以便您可以在其他監視遙測情況下重新檢視它，或與您的小組共用。

將已設定的圖表釘選到儀表板：

設定您的圖表之後，按一下圖表右上角的 [圖表動作]  功能表，然後按一下 [釘選到儀表板]  。

![計量影像](./media/metrics-charts/00013.png)

## <a name="create-alert-rules"></a>建立警示規則

您可以使用已設定的準則來將計量視覺化，以作為計量型警示規則的基礎。 新的警示規則將包含來自您圖表的目標資源、計量、分割及篩選維度。 您稍後將能在警示規則建立窗格上修改這些設定。

### <a name="to-create-a-new-alert-rule-click-new-alert-rule"></a>若要建立新的警示規則，請按一下 [新增警示規則] 

![以紅色強調顯示的[新增警示規則] 按鈕](./media/metrics-charts/015.png)

您將會進入警示規則建立窗格，其中已預先填入來自圖表的基礎計量維度，讓您能夠更輕鬆地產生自訂警示規則。

![建立警示規則](./media/metrics-charts/016.png)

若要深入了解如何設定計量警示，請查看這篇[文章](alerts-metric.md)。

## <a name="troubleshooting"></a>疑難排解

*我看不到我的圖表上的任何資料。*

* 篩選會套用至窗格上的所有圖表。 請確定，當您將焦點放在某個圖表時，您未在其他圖表上設定會排除所有資料的篩選。

* 如果您想要在不同的圖表上設定不同的篩選，請在不同的刀鋒視窗中建立圖表，將它們儲存為個別的最愛圖表。 如果想要，您可以將這些圖表釘選到儀表板，以便並排查看兩者。

* 如果您依據計量上未定義的屬性區分圖表，則圖表上不會有任何資料。 請嘗試清除分割 (分割)，或選擇不同的屬性。

## <a name="next-steps"></a>後續步驟

  閱讀[建立自訂 KPI 儀表板](https://docs.microsoft.com/azure/application-insights/app-insights-tutorial-dashboards)以了解使用計量建立可採取動作之儀表板的最佳做法。

