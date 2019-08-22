---
title: 在 Azure IoT Central 應用程式中使用裝置集合 | Microsoft Docs
description: 如何以操作員的身分在 Azure IoT Central 應用程式中使用裝置集合。
author: ellenfosborne
ms.author: elfarber
ms.date: 06/09/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpfr
ms.openlocfilehash: 9576711c33979cef7e043c18ac3b56251dd8a806
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/21/2019
ms.locfileid: "69877332"
---
# <a name="use-device-sets-in-your-azure-iot-central-application"></a>在 Azure IoT Central 應用程式中使用裝置集合

[!INCLUDE [iot-central-original-pnp](../../includes/iot-central-original-pnp-note.md)]

本文說明如何以操作員的身分在 Azure IoT Central 應用程式中使用裝置集合。

裝置集合是群組在一起的裝置清單, 因為它們符合某些指定的準則。 裝置集合可藉由將裝置分組到較小的邏輯群組，來協助您大規模地對裝置進行管理、視覺化和分析。 例如, 您可以建立裝置集來列出西雅圖的所有空中空調裝置, 讓技術人員能夠尋找其負責的裝置。 本文說明如何建立和設定裝置集合。

## <a name="create-a-device-set"></a>建立裝置集合

若要建立裝置集合：

1. 在左側導覽功能表上選擇 [裝置集合]。

1. 選取 [ **+ 新增**]。

    ![新增裝置集合](media/howto-use-device-sets/image1.png)

1. 為裝置集合提供在整個應用程式中獨一無二的名稱。 您也可以新增描述。 裝置集合只能包含來自單一裝置範本的裝置。 選擇要用於此集合的裝置範本。

1. 建立查詢，藉由選取屬性、比較運算子和值來識別裝置集合的裝置。 您可以新增多個查詢，而符合**所有**準則的裝置則會放置在裝置集合中。 能存取應用程式的任何人都能存取您所建立的裝置集合，因此任何人都能檢視、修改或刪除裝置集合。

    ![裝置集合查詢](media/howto-use-device-sets/image2.png)

    > [!NOTE]
    > 裝置集合是動態的查詢。 您每次檢視裝置清單時所看到的裝置可能會不同。 清單內容取決於哪些裝置目前符合查詢準則。

1. 選擇 [儲存]。

## <a name="configure-the-dashboard-for-your-device-set"></a>設定裝置集的儀表板

建立裝置集合後，就可以設定其 [儀表板]。 **儀表板**是您放置影像和連結的首頁。 您也可以新增會列出裝置集合所含裝置的格線。

1. 在左側導覽功能表上選擇 [裝置集合]。

1. 選取您的裝置集。

1. 選取 [ **儀表板** ] 索引標籤。

1. 選取 [編輯]。

    ![設計模式開啟](media/howto-use-device-sets/image3.png)

1. 如需如何新增影像的資訊，請參閱[準備及上傳影像至您的 Azure IoT Central 應用程式](howto-prepare-images.md)。

1. 新增連結圖格：
    1. 選擇右窗格上的 [連結]。
    1. 為連結提供**標題**。
    1. 選擇要在選取連結時開啟的 URL。
    1. 為連結提供描述，此描述會顯示在**標題**下面。
    1. 選擇 [儲存]。

        ![儲存連結](media/howto-use-device-sets/image7.png)

    1. 您可以在 [儀表板] 上移動連結圖格或調整其大小。

1. 新增格線。 格線是一份資料表，內含裝置集合中的裝置以及您所選擇的資料行。
    1. 選擇右窗格上的 [格線]。
    1. 為格線提供**標題**。
    1. 選擇 [新增/移除] 來選取要顯示的資料行。 在跳出的面板中選擇您想要顯示的資料行，並選擇向右箭號來加以選取。
    1. 選擇 [確定]。
    1. 選擇 [儲存]。

        ![儲存格線](media/howto-use-device-sets/image9.png)

    1. 拖放格線以將其放在 [儀表板] 上。

        > [!NOTE]
        > 您可以新增多個影像、連結和格線。
  
    1. 選取 [完成]。

若要深入瞭解如何在 Azure IoT Central 中使用磚, 請參閱[使用儀表板磚](howto-use-tiles.md)。

### <a name="configure-a-location-map-in-your-device-sets-dashboard"></a>在裝置集合儀表板中設定位置圖

您可以新增對應, 以視覺化方式呈現裝置集合中的裝置位置。

若要將地圖新增至您的裝置集合儀表板, 您必須已在裝置範本中設定位置測量或位置屬性。 若要深入瞭解, 請參閱[建立位置量測](howto-set-up-template.md)或[建立位置屬性](howto-set-up-template.md)。

1. 在您的裝置集合**儀表板**上, 從程式庫選取 [**對應**]。
2. 新增標題, 然後選擇您先前設定的位置量測或屬性。
3. 選取 [**儲存**], [地圖] 磚會顯示裝置集合中裝置的最後已知位置。
4. 當操作員查看裝置集合儀表板時, 操作員會看到您已設定的所有磚, 包括位置地圖。

您可以調整儀表板上地圖底圖的大小。 選取地圖上的 pin 會顯示裝置資訊、名稱和位置。 選取快顯視窗, 以移至 [裝置] 屬性頁。

## <a name="configure-the-list-for-your-device-set"></a>設定裝置集合的清單

建立裝置集合後，就可以設定 [清單]。 [清單] 會在資料表中顯示裝置集合中的所有裝置以及您所選擇的資料行。

1. 在左側導覽功能表上選擇 [裝置集合]。

1. 選擇 [清單] 索引標籤。

1. 選擇 [資料行選項]。

    ![資料行選項](media/howto-use-device-sets/image11.png)

1. 藉由選取您想要顯示的資料行，然後選擇向右箭號來加以選取，以選擇所要顯示的資料行。

    ![選擇資料行](media/howto-use-device-sets/image12.png)

1. 選擇 [確定]。

## <a name="analytics"></a>分析

裝置集合中的分析等同於左側導覽功能表中的 [主要分析] 索引標籤。 您可以在關於[如何建立分析](howto-use-device-sets.md)的文章中深入了解分析。

## <a name="next-steps"></a>後續步驟

您現在已了解如何在 Azure IoT Central 應用程式中使用裝置集合，以下是建議的後續步驟：

> [!div class="nextstepaction"]
> [如何建立遙測規則](howto-create-telemetry-rules.md)
