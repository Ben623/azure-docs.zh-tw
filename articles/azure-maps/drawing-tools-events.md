---
title: 將繪圖工具欄新增至地圖 |Microsoft Azure 對應
description: 在本文中，您將瞭解如何使用 Microsoft Azure Maps Web SDK 將繪製工具列新增至地圖
author: rbrundritt
ms.author: richbrun
ms.date: 12/05/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.openlocfilehash: fd235f3f39d67f86c8387add79ca0dbf17dc5906
ms.sourcegitcommit: f9601bbccddfccddb6f577d6febf7b2b12988911
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/12/2020
ms.locfileid: "75911672"
---
# <a name="drawing-tool-events"></a>繪製工具事件

在地圖上使用繪圖工具時，當使用者在地圖上繪製時，回應某些事件通常會很有用。 下表列出 `DrawingManager` 類別支援的所有事件。

| 活動 | 說明 |
|-------|-------------|
| `drawingchanged` | 在圖形中的任何座標已加入或變更時引發。 | 
| `drawingchanging` | 在顯示圖形的任何預覽座標時引發。 例如，會在拖曳座標時引發多次。 | 
| `drawingcomplete` | 當圖形已完成繪製或離開編輯模式時引發。 |
| `drawingmodechanged` | 繪製模式變更時引發。 新的繪圖模式會傳遞至事件處理常式。 |
| `drawingstarted` | 當使用者開始繪製圖形或將圖形置於編輯模式時引發。  |

下列程式碼顯示「繪圖工具」模組中的事件如何正常執行。 在地圖上繪製圖形，並在事件引發時監看。

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="繪製工具事件" src="https://codepen.io/azuremaps/embed/dyPMRWo?height=500&theme-id=default&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
請參閱<a href='https://codepen.io'>CodePen</a>上的以 Azure 地圖服務（<a href='https://codepen.io/azuremaps'>@azuremaps</a>）繪製的畫筆<a href='https://codepen.io/azuremaps/pen/dyPMRWo'>工具事件</a>。
</iframe>

<br/>

## <a name="examples"></a>範例

以下是一些使用繪製工具事件的常見案例範例。

### <a name="select-points-in-polygon-area"></a>選取多邊形區域中的點

下列程式碼示範如何監視代表多邊形區域（多邊形、矩形和圓形）的圖形繪圖，並判斷地圖上的哪些資料點是在繪製的區域內。 `drawingcomplete` 事件是用來觸發選取邏輯。 在選取邏輯中，地圖上的所有資料點都會透過進行迴圈，並測試與所繪製圖形之多邊形區域的交集。 這個範例會使用開放原始碼[Turf](https://turfjs.org/)程式庫來執行空間交集計算。

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="選取繪製多邊形區域中的資料" src="https://codepen.io/azuremaps/embed/XWJdeja?height=500&theme-id=default&default-tab=result" frameborder="no" allowtransparency="true" allowfullscreen="true">
在<a href='https://codepen.io'>CodePen</a>上 Azure 地圖服務（<a href='https://codepen.io/azuremaps'>@azuremaps</a>），請參閱在<a href='https://codepen.io/azuremaps/pen/XWJdeja'>繪製的多邊形區域中選取資料</a>。
</iframe>

<br/>

### <a name="draw-and-search-in-polygon-area"></a>在多邊形區域中繪製和搜尋

下列程式碼示範如何在使用者完成圖形繪製之後，搜尋繪圖區域內的感興趣點。 `drawingcomplete` 事件是用來觸發搜尋邏輯。 如果使用者繪製矩形或多邊形，則會執行幾何內的搜尋。 若已繪製圓形，則會使用半徑和中心位置來執行感對點的搜尋。 `drawingmodechanged` 事件是用來判斷使用者何時切換為繪圖模式，並清除繪圖畫布。

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="在多邊形區域中繪製和搜尋" src="https://codepen.io/azuremaps/embed/eYmZGNv?height=500&theme-id=default&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
請參閱<a href='https://codepen.io'>CodePen</a>上的 Azure 地圖服務（<a href='https://codepen.io/azuremaps'>@azuremaps</a>），<a href='https://codepen.io/azuremaps/pen/eYmZGNv'>以在多邊形區域中繪製和搜尋</a>畫筆。
</iframe>

<br/>

### <a name="create-a-measuring-tool"></a>建立測量工具

下列程式碼會示範如何使用繪製事件來建立測量工具。 `drawingchanging` 是用來在繪製圖形時進行監視。 當使用者移動滑鼠時，會計算圖形的維度。 `drawingcomplete` 事件是用來在繪製圖形之後，對其執行最後的計算。 `drawingmodechanged` 事件是用來判斷使用者何時切換成繪圖模式，並清除繪圖畫布和舊的度量資訊。

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="測量工具" src="https://codepen.io/azuremaps/embed/RwNaZXe?height=500&theme-id=default&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
請參閱<a href='https://codepen.io'>CodePen</a>上的 Azure 地圖服務（<a href='https://codepen.io/azuremaps'>@azuremaps</a>）的「畫筆<a href='https://codepen.io/azuremaps/pen/RwNaZXe'>測量工具</a>」。
</iframe>

<br/>

## <a name="next-steps"></a>後續步驟

瞭解如何使用 [繪圖工具] 模組的其他功能：

> [!div class="nextstepaction"]
> [取得圖形資料](map-get-shape-data.md)

> [!div class="nextstepaction"]
> [互動類型和鍵盤快速鍵](drawing-tools-interactions-keyboard-shortcuts.md)

深入瞭解服務模組：

> [!div class="nextstepaction"]
> [服務模組](how-to-use-services-module.md)

查看更多程式碼範例：

> [!div class="nextstepaction"]
> [程式碼範例頁面](https://aka.ms/AzureMapsSamples) \(英文\)
