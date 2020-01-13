---
title: 建立具有 Azure 地圖服務的對應 |Microsoft Azure 對應
description: 在本文中，您將瞭解如何使用 Microsoft Azure Maps Web SDK，在網頁上呈現地圖。
author: jingjing-z
ms.author: jinzh
ms.date: 07/26/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 49c86f3e6c654ecbfcd07809f42a1b038ca3f8ab
ms.sourcegitcommit: f9601bbccddfccddb6f577d6febf7b2b12988911
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/12/2020
ms.locfileid: "75911115"
---
# <a name="create-a-map"></a>建立地圖

本文將說明建立地圖及以動畫顯示地圖的方法。  

## <a name="loading-a-map"></a>載入對應

若要載入對應，請建立[map 類別](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)的新實例。 在初始化對應時，會傳入用來轉譯對應的 DIV 專案識別碼，以及要在載入對應時使用的一組選項。 如果未在 `atlas` 命名空間上指定預設驗證資訊，載入對應時，就必須在對應選項中指定這項資訊。 對應會以非同步方式載入數個資源，以提高效能。 因此，建立 map 實例之後，請將 `ready` 或 `load` 事件附加至對應，然後在該事件處理常式中加入與對應互動的任何額外程式碼。 當對應的資源載入足以與程式互動時，`ready` 事件就會引發。 在初始對應視圖完全載入完成後，就會引發 `load` 事件。 

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="基本地圖載入" src="//codepen.io/azuremaps/embed/rXdBXx/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
請參閱<a href='https://codepen.io'>CodePen</a>上的 Azure 地圖服務（<a href='https://codepen.io/azuremaps'>@azuremaps</a>）的畫筆<a href='https://codepen.io/azuremaps/pen/rXdBXx/'>基本地圖載入</a>。
</iframe>

> [!TIP]
> 可以在同一個頁面上載入多個對應，而且每個對應都可以使用相同或不同的驗證和語言設定。

## <a name="show-a-single-copy-of-the-world"></a>顯示單一的世界複本

當地圖在寬螢幕上放大時，會以水準方式顯示多個世界複本。 這適用于大部分的案例，但某些應用程式可能只會想要看到一份全球的複本。 這可以藉由將 [對應] `renderWorldCopies` 選項設定為 [`false`] 來完成。

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="renderWorldCopies = false" src="//codepen.io/azuremaps/embed/eqMYpZ/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
請參閱<a href='https://codepen.io'>CodePen</a>上 Azure 地圖服務（<a href='https://codepen.io/azuremaps'>@azuremaps</a>）的 Pen <a href='https://codepen.io/azuremaps/pen/eqMYpZ/'>renderWorldCopies = false</a> 。
</iframe>

## <a name="controlling-the-map-camera"></a>控制地圖攝影機

有兩種方式可以使用相機來設定地圖的顯示區域。 您可以在載入對應時設定相機選項（例如置中和縮放），或在對應載入後隨時呼叫 `setCamera` 選項，以程式設計方式更新地圖視圖。  

<a id="setCameraOptions"></a>

### <a name="set-the-camera"></a>設定觀景窗

在下列程式碼中，會建立[地圖物件](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)，並設定置中和縮放選項。 [置中] 和 [縮放] 層級等地圖屬性是[CameraOptions](/javascript/api/azure-maps-control/atlas.cameraoptions)的一部分。

<br/>

<iframe height='500' scrolling='no' title='透過 CameraOptions 建立地圖' src='//codepen.io/azuremaps/embed/qxKBMN/?height=543&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>請參閱<a href='https://codepen.io'>CodePen</a>上的 Azure 位置服務（<a href='https://codepen.io/azuremaps'>@azuremaps</a>） `CameraOptions` 的畫筆<a href='https://codepen.io/azuremaps/pen/qxKBMN/'>建立地圖</a>。
</iframe>

<a id="setCameraBoundsOptions"></a>

### <a name="set-the-camera-bounds"></a>設定觀景窗界限

在下列程式碼中， [Map 物件](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)是透過 `new atlas.Map()`來建立。 `CameraBoundsOptions` 等地圖屬性可透過 Map 類別的 [setCamera](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest) 函式來定義。 邊界和邊框間距屬性會使用 `setCamera` 來設定。

<br/>

<iframe height='500' scrolling='no' title='透過 CameraBoundsOptions 建立地圖' src='//codepen.io/azuremaps/embed/ZrRbPg/?height=543&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>請參閱<a href='https://codepen.io'>CodePen</a>上的 Azure 地圖服務（<a href='https://codepen.io/azuremaps'>@azuremaps</a>），透過 `CameraBoundsOptions` 的畫筆<a href='https://codepen.io/azuremaps/pen/ZrRbPg/'>建立地圖</a>。
</iframe>

### <a name="animate-map-view"></a>以動畫方式呈現地圖的檢視

在下列程式碼中，第一個程式碼區塊會建立地圖，並設定地圖樣式、置中和縮放值。 在第二個程式碼區塊中，會建立 [動畫] 按鈕的 click 事件處理常式。 按一下此按鈕時，會使用[CameraOptions](/javascript/api/azure-maps-control/atlas.cameraoptions)， [AnimationOptions](/javascript/api/azure-maps-control/atlas.animationoptions)的一些隨機值來呼叫 setCamera 函數。

<br/>

<iframe height='500' scrolling='no' title='以動畫方式呈現地圖的檢視' src='//codepen.io/azuremaps/embed/WayvbO/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>查看 Pen <a href='https://codepen.io/azuremaps/pen/WayvbO/'>以動畫方式呈現地圖的檢視</a>，發佈者：Azure 地圖服務 (<a href='https://codepen.io/azuremaps'>@azuremaps</a>)，發佈位置：<a href='https://codepen.io'>CodePen</a>。
</iframe>

## <a name="try-out-the-code"></a>試用程式碼

看一下上述範例程式碼。 您可以在左側的 [JS] 索引標籤上編輯 JavaScript 程式碼，並在右側的 [結果] 索引標籤上看到地圖檢視變更。 您也可以按一下 [在 CodePen 上編輯] 按鈕，並在 CodePen 中編輯程式碼。

<a id="relatedReference"></a>

## <a name="next-steps"></a>後續步驟

深入了解本文使用的類別和方法：

> [!div class="nextstepaction"]
> [地圖](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [CameraOptions](/javascript/api/azure-maps-control/atlas.cameraoptions)

> [!div class="nextstepaction"]
> [AnimationOptions](/javascript/api/azure-maps-control/atlas.animationoptions)

請參閱程式碼範例，將功能新增至您的應用程式：

> [!div class="nextstepaction"]
> [變更地圖樣式](choose-map-style.md)

> [!div class="nextstepaction"]
> [將控制項新增至對應](map-add-controls.md)

> [!div class="nextstepaction"]
> [程式碼範例](https://docs.microsoft.com/samples/browse/?products=azure-maps)