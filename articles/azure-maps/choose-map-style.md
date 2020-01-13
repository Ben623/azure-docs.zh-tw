---
title: 地圖樣式功能 |Microsoft Azure 對應
description: 在本文中，您將瞭解 Microsoft Azure Maps web SDK 中可用的樣式相關功能。
author: walsehgal
ms.author: v-musehg
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: b92e03c4e5346dd39eaba84cfeeedb93e418678c
ms.sourcegitcommit: f9601bbccddfccddb6f577d6febf7b2b12988911
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/12/2020
ms.locfileid: "75911785"
---
# <a name="choose-a-map-style-in-azure-maps"></a>選擇 Azure 地圖服務中的地圖樣式

在 Azure 地圖服務中，有許多[支援的地圖樣式](./supported-map-styles.md)可在 Web SDK 中取得。 本文說明如何使用與樣式相關的功能在地圖負載上設定樣式、設定新樣式，以及使用樣式選擇器控制項。

## <a name="set-style-on-map-load"></a>設定載入地圖時的樣式

在下列程式碼中，對應的 `style` 選項會設定為在初始化時 `grayscale_dark`。

<br/>

<iframe height='500' scrolling='no' title='設定地圖負載上的樣式' src='//codepen.io/azuremaps/embed/WKOQRq/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>查看畫筆<a href='https://codepen.io/azuremaps/pen/WKOQRq/'>在地圖負載上設定樣式</a>，發佈者：Azure 地圖服務 (<a href='https://codepen.io/azuremaps'>@azuremaps</a>)，發佈位置：<a href='https://codepen.io'>CodePen</a>。
</iframe>

## <a name="update-the-style"></a>更新樣式

在下列程式碼中，載入對應實例之後，會使用對應的[setStyle](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)函式，將地圖樣式從 `road` 更新為 `satellite`。

<br/>

<iframe height='500' scrolling='no' title='更新樣式' src='//codepen.io/azuremaps/embed/yqXYzY/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>查看畫筆<a href='https://codepen.io/azuremaps/pen/yqXYzY/'>更新樣式</a>，發佈者：Azure 地圖服務 (<a href='https://codepen.io/azuremaps'>@azuremaps</a>)，發佈位置：<a href='https://codepen.io'>CodePen</a>。
</iframe>

## <a name="add-the-style-picker"></a>新增樣式選擇器

下列程式碼會將[StyleControl](/javascript/api/azure-maps-control/atlas.control.stylecontrol)新增至地圖，讓使用者可以輕鬆地在不同的地圖樣式之間切換。 

<br/>

<iframe height='500' scrolling='no' title='新增樣式選擇器' src='//codepen.io/azuremaps/embed/OwgyvG/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>查看畫筆<a href='https://codepen.io/azuremaps/pen/OwgyvG/'>新增樣式選擇器</a>，發佈者：Azure 地圖服務 (<a href='https://codepen.io/azuremaps'>@azuremaps</a>)，發佈位置：<a href='https://codepen.io'>CodePen</a>。
</iframe>

> [!TIP]
> 根據預設，樣式選擇器控制項會列出使用 Azure 地圖服務的 S0 定價層時可用的所有樣式。 如果您想要減少此清單中的樣式數目，請將您想要顯示在清單中的樣式陣列，傳遞至樣式選擇器的 [`mapStyle`] 選項。 如果您使用 S1，而且想要顯示所有可用的樣式，請將樣式選擇器的 [`mapStyles`] 選項設定為 [`"all"`]。

## <a name="next-steps"></a>後續步驟

深入了解此文章中使用的類別與方法：

> [!div class="nextstepaction"]
> [地圖](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)

將控制項新增至您的對應：

> [!div class="nextstepaction"]
> [新增地圖控制項](map-add-controls.md)

> [!div class="nextstepaction"]
> [新增圖釘](map-add-pin.md)
