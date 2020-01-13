---
title: 將圖格圖層新增至地圖 |Microsoft Azure 對應
description: 在本文中，您將瞭解如何使用 Microsoft Azure Maps Web SDK 來覆迭地圖上的圖格圖層。 圖格圖層可讓您在地圖上呈現影像。
author: rbrundritt
ms.author: richbrun
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 83e8f6d684d6d39102fd682653cd19816a9f7b10
ms.sourcegitcommit: f9601bbccddfccddb6f577d6febf7b2b12988911
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/12/2020
ms.locfileid: "75911099"
---
# <a name="add-a-tile-layer-to-a-map"></a>將圖格圖層新增至地圖

本文會示範如何在地圖上覆疊圖格圖層。 圖格圖層可讓您在 Azure 地圖服務的地圖底圖上覆蓋影像。 您可以在[縮放層級和圖格格線](zoom-levels-and-tile-grid.md)文件中找到有關 Azure 地圖服務圖格顯示系統的詳細資訊。

磚圖層會從伺服器載入磚。 這些影像可以像伺服器上其他影像一樣，使用圖格圖層了解的命名慣例來預先轉譯及儲存，或是作為可動態產生影像的動態服務。 Azure 地圖服務[TileLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.tilelayer?view=azure-iot-typescript-latest)類別支援三種不同的磚服務命名慣例： 

* X、Y、縮放標記法 - 以縮放層級為基礎，在圖格格線中的圖格上，x 是資料行位置，而 y 是資料列位置。
* Quadkey 標記法 - 將 x、y、縮放資訊結合成單一字串值，以作為圖格的唯一識別碼。
* 週框方塊 - 週框方塊座標可用來以 `{west},{south},{east},{north}` 格式指定影像，[Web 地圖服務 (WMS)](https://www.opengeospatial.org/standards/wms) 常使用此格式。

> [!TIP]
> [TileLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.tilelayer?view=azure-iot-typescript-latest) 是視覺化地圖上大型資料集的好方法。 不只可以從影像產生圖格圖層，向量資料也可轉譯為圖格圖層。 藉由將向量資料轉譯為圖格圖層，地圖控制項就只需載入圖層，而這可能比向量資料所表示的檔案大小小很多。 許多人因為需要轉譯地圖上數百萬個資料列，而選擇使用這項技術。

傳遞至圖格圖層的圖格 URL 必須是 TileJSON 資源的 http/https URL，或是使用下列參數的圖格 URL 範本： 

* `{x}` - 圖格的 X 位置。 也需要 `{y}` 和 `{z}`。
* `{y}` - 圖格的 Y 位置。 也需要 `{x}` 和 `{z}`。
* `{z}` 圖格的縮放層級。 也需要 `{x}` 和 `{y}`。
* `{quadkey}` -圖格 quadkey 識別碼，以 Bing Maps 圖格系統的命名慣例為基礎。
* `{bbox-epsg-3857}` - 使用 `{west},{south},{east},{north}` 格式的週框方塊字串，位在 EPSG 3857 空間參考系統中。
* `{subdomain}` - 用來加入子網域值 (如果有指定) 的預留位置。

## <a name="add-a-tile-layer"></a>新增圖格圖層

 此範例示範如何建立圖格圖層，而此圖格圖層會指向一組使用 x、y、縮放圖格系統的圖格。 此圖格圖層的來源是天氣雷達覆疊圖，資料來源：[愛荷華州立大學的愛荷華州環境氣象網 (Iowa Environmental Mesonet of Iowa State University)](https://mesonet.agron.iastate.edu/ogc/)。 當觀看雷達圖資料時，理想的使用者可以在流覽地圖時清楚地看到城市的標籤，這可以藉由將圖格圖層插入 `labels` 圖層來完成。

```javascript
//Create a tile layer and add it to the map below the label layer.
//Weather radar tiles from Iowa Environmental Mesonet of Iowa State University.
map.layers.add(new atlas.layer.TileLayer({
    tileUrl: 'https://mesonet.agron.iastate.edu/cache/tile.py/1.0.0/nexrad-n0q-900913/{z}/{x}/{y}.png',
    opacity: 0.8,
    tileSize: 256
}), 'labels');
```

以下是上述功能的完整執行程式碼範例。

<br/>

<iframe height='500' scrolling='no' title='底圖圖層使用 X、Y 和 Z' src='//codepen.io/azuremaps/embed/BGEQjG/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>請參閱 <a href='https://codepen.io'>CodePen</a> 上由 Azure 地圖服務 (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) 建立的 Pen：<a href='https://codepen.io/azuremaps/pen/BGEQjG/'>使用 X、Y 和 Z 的圖格圖層</a>。
</iframe>

## <a name="customize-a-tile-layer"></a>自訂圖格圖層

圖格圖層類別有許多樣式選項。 以下是可讓您試用這些選項的工具。

<br/>

<iframe height='700' scrolling='no' title='圖格圖層選項' src='//codepen.io/azuremaps/embed/xQeRWX/?height=700&theme-id=0&default-tab=result' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>請參閱 <a href='https://codepen.io'>CodePen</a> 上由 Azure 地圖服務 (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) 建立的 Pen：<a href='https://codepen.io/azuremaps/pen/xQeRWX/'>圖格圖層選項</a>。
</iframe>

## <a name="next-steps"></a>後續步驟

深入了解本文使用的類別和方法：

> [!div class="nextstepaction"]
> [TileLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.tilelayer?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [TileLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.tilelayeroptions?view=azure-iot-typescript-latest)

請參閱下列文章，以取得更多可新增至地圖的程式碼範例：

> [!div class="nextstepaction"]
> [新增影像圖層](./map-add-image-layer.md)
