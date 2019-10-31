---
title: 在您的 Azure IoT Central 應用程式中使用裝置群組 |Microsoft Docs
description: 身為操作員，請瞭解如何使用裝置群組來分析 Azure IoT Central 應用程式中來自裝置的遙測資料。
author: dominicbetts
ms.author: dobett
ms.date: 10/29/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpfr
ms.openlocfilehash: 0f222cf9907dbf768a5f93842ff3df3fddf224a4
ms.sourcegitcommit: 98ce5583e376943aaa9773bf8efe0b324a55e58c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/30/2019
ms.locfileid: "73180864"
---
# <a name="tutorial-use-device-groups-to-analyze-device-telemetry-preview-features"></a>教學課程：使用裝置群組來分析裝置遙測（預覽功能）

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

本文說明如何以操作員的身分，使用裝置群組來分析 Azure IoT Central 應用程式中的裝置遙測。

裝置群組是群組在一起的裝置清單，因為它們符合某些指定的準則。 裝置群組可透過將裝置分組為較小的邏輯群組，協助您大規模管理、視覺化和分析裝置。 例如，您可以建立裝置群組來列出西雅圖的所有空中空調裝置，讓技術人員能夠尋找其負責的裝置。

在本教學課程中，您會了解如何：

> [!div class="checklist"]
> * 建立裝置群組
> * 使用裝置群組來分析裝置遙測

## <a name="prerequisites"></a>必要條件

開始之前，您應該先完成[建立 Azure IoT Central 應用程式](./quick-deploy-iot-central-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json)，並[將模擬裝置新增至您的 IoT Central 應用程式](./quick-create-pnp-device-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json)快速入門，以建立要使用的**環境感應器**裝置範本。

## <a name="create-simulated-devices"></a>建立模擬的裝置

建立裝置群組之前，請先從**環境感應器**裝置範本新增至少五個模擬裝置，以在本教學課程中使用：

![五個模擬的環境感應器裝置](./media/tutorial-use-device-groups-pnp/simulated-devices.png)

對於四個環境感應器裝置，請使用**環境感應器屬性**視圖將客戶名稱設定為**Contoso**：

![將客戶名稱設定為 Contoso](./media/tutorial-use-device-groups-pnp/customer-name.png)

## <a name="create-a-device-group"></a>建立裝置群組

若要建立裝置群組：

1. 選擇左窗格中的 [**裝置群組**]。

1. 選取 [ **+ 新增**]。

    ![新增裝置群組](media/tutorial-use-device-groups-pnp/image1.png)

1. 為您的裝置群組提供一個名稱，例如**Contoso 裝置**。 您也可以新增描述。 裝置群組只能包含來自單一裝置範本的裝置。 選擇要用於此群組的**環境感應器**裝置範本。

1. 藉由選取 [ **Customer Name** ] 屬性、[ **Equals** ] 比較運算子和 [ **contoso** ] 做為值，建立查詢以識別屬於**Contoso**的裝置群組裝置。 您可以新增多個查詢，且符合**所有**條件的裝置會放在裝置群組中。 可存取應用程式的任何人都能存取您所建立的裝置群組，因此任何人都可以查看、修改或刪除裝置群組。

    ![裝置群組查詢](media/tutorial-use-device-groups-pnp/image2.png)

    > [!NOTE]
    > 裝置群組是動態查詢。 您每次檢視裝置清單時所看到的裝置可能會不同。 清單內容取決於哪些裝置目前符合查詢準則。

1. 選擇 [儲存]。

> [!NOTE]
> 針對 Azure IoT Edge 裝置，請選取 [Azure IoT Edge 範本] 來建立裝置群組。

## <a name="analytics"></a>分析

您可以使用**分析**與裝置群組，從群組中的裝置分析遙測資料。 例如，您可以繪製所有 Contoso 環境感應器所報告的平均溫度。

若要分析裝置群組的遙測：

1. 選擇左窗格中的 [**分析**]。

1. 選取您建立的 [ **Contoso 裝置**] 裝置群組。 然後，新增**溫度**和**濕度**遙測類型：

    ![建立分析](./media/tutorial-use-device-groups-pnp/create-analysis.png)

    使用 [遙測類型] 旁的齒輪圖示來選取匯總類型。 預設值為 [**平均值**]。 使用 [**分割依據**] 來變更匯總資料的顯示方式。 例如，如果您依裝置識別碼分割，當您選取 [**分析**] 時，就會看到每個裝置的繪圖。

1. 選取 [**分析**] 以查看平均遙測值：

    ![View 分析](./media/tutorial-use-device-groups-pnp/view-analysis.png)

    您可以自訂此視圖、變更所顯示的時間週期，以及匯出資料。

## <a name="next-steps"></a>後續步驟

既然您已瞭解如何在 Azure IoT Central 應用程式中使用裝置群組，以下是建議的後續步驟：

> [!div class="nextstepaction"]
> [如何建立遙測規則](tutorial-create-telemetry-rules-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json)
