---
title: 建置可供認證的 IoT 隨插即用預覽版裝置 | Microsoft Docs
description: 身為裝置開發人員，請了解如何建置可供認證的 IoT 隨插即用預覽版裝置。
author: tbhagwat3
ms.author: tanmayb
ms.date: 06/28/2019
ms.topic: tutorial
ms.custom: mvc
ms.service: iot-pnp
services: iot-pnp
manager: philmea
ms.openlocfilehash: e4dd5215812f0fd1a43afe0923601417bc8e6916
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/04/2019
ms.locfileid: "73569629"
---
# <a name="build-an-iot-plug-and-play-preview-device-thats-ready-for-certification"></a>建置可供認證的 IoT 隨插即用預覽版裝置

本教學課程說明身為裝置開發人員的您如何建置可供認證的 IoT 隨插即用預覽版裝置。

認證測試會檢查：

- 您的 IoT 隨插即用裝置程式碼已安裝在裝置上。
- 您的 IoT 隨插即用裝置程式碼是使用 Azure IoT SDK 建置的。
- 您的裝置程式碼支援 [Azure IoT 中樞裝置佈建服務](../iot-dps/about-iot-dps.md)。
- 您的裝置程式碼實作裝置資訊介面。
- 功能模型和裝置程式碼可與 IoT Central 搭配運作。

## <a name="prerequisites"></a>必要條件

若要完成本教學課程，您需要：

- [Visual Studio Code](https://code.visualstudio.com/download)
- [適用於 VS Code 的 Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) \(英文\) 擴充套件

您也需要在下列快速入門中建立的 IoT 隨插即用裝置：[使用裝置功能模型來建立裝置](quickstart-create-pnp-device.md)。

## <a name="store-a-capability-model-and-interfaces"></a>儲存功能模型和介面

針對 IoT 隨插即用裝置，您必須製作將裝置的功能定義為 JSON 檔案的功能模型和介面。

您可以將這些 JSON 檔案儲存在三個不同的位置：

- 公用模型存放庫。
- 您的公司模型存放庫。
- 您的裝置上。

目前，若要認證您的裝置，必須將這些檔案儲存在您的公司模型存放庫，或公用模型存放庫中。

## <a name="include-the-required-interfaces"></a>包含必要的介面

若要通過認證程序，您必須在功能模型中包含並執行**裝置資訊**介面。 此介面具有下列識別：

```json
"@id": "urn:azureiot:DeviceManagement:DeviceInformation:1"
```

> [!NOTE]
> 如果您已完成[快速入門：使用裝置功能模型來建立裝置](quickstart-create-pnp-device.md)，則您的模型中已包含**裝置資訊**介面。

若要在裝置模型中包含**裝置資訊**介面，請將介面識別碼新增至功能模型的 `implements` 屬性：

```json
{
  "@id": "urn:yourcompanyname:sample:ThermostatDevice:1",
  "@type": "CapabilityModel",
  "displayName": "Thermostat T-1000",
  "implements": [
    "urn:yourcompanyname:sample:Thermostat:1",
    "urn:azureiot:DeviceManagement:DeviceInformation:1"
  ],
  "@context": "http://azureiot.com/v1/contexts/CapabilityModel.json"
}
```

若要在 VS Code 中檢視**裝置資訊**介面：

1. 使用 **Ctrl+Shift+P** 開啟命令選擇區。

1. 輸入**隨插即用**，然後選取 [IoT 隨插即用開啟模型存放庫]  命令。 選擇 [開啟公用模型存放庫]  。 公用模型存放庫會在 VS Code 中開啟。

1. 在公用模型存放庫中選取 [介面]  索引標籤，選取篩選圖示，然後在 [篩選] 欄位中輸入**裝置資訊**。

1. 若要建立**裝置資訊**介面的本機複本，請在篩選過的清單中加以選取，然後選取 [下載]  。 VS Code 會顯示介面檔案。

若要使用 Azure CLI 檢視 [裝置資訊]  介面：

1. [安裝 Azure IoT CLI 擴充功能](howto-install-pnp-cli.md)。

1. 使用下列 Azure CLI 命令來顯示具有裝置資訊介面識別碼的介面：

    ```cmd/sh
    az iot pnp interface show --interface urn:azureiot:DeviceManagement:DeviceInformation:1
    ```

如需詳細資訊，請參閱[安裝和使用適用於 Azure CLI 的 Azure IoT 擴充功能](howto-install-pnp-cli.md)。

## <a name="update-device-code"></a>更新裝置程式碼

### <a name="enable-device-provisioning-through-the-azure-iot-device-provisioning-service-dps"></a>透過 Azure IoT 裝置佈建服務 (DPS) 啟用裝置佈建

若要認證裝置，該裝置必須透過 [Azure IoT 裝置佈建服務 (DPS)](https://docs.microsoft.com/azure/iot-dps/about-iot-dps) 啟用佈建。 若要新增使用 DPS 的功能，您可以在 VS Code 中產生 C 程式碼 Stub。 請遵循下列步驟：

1. 在 VS Code 中開啟含有 DCM 檔案的資料夾，使用 **Ctrl+Shift+P** 開啟命令選擇區，輸入 **IoT 隨插即用**，然後選取 [產生裝置程式碼 Stub]  。

1. 選擇您要用來產生裝置程式碼 Stub 的 DCM 檔案。

1. 輸入專案名稱，這是您的裝置應用程式名稱。

1. 選擇 [ANSI C]  作為語言。

1. 選擇 [透過 DPS (裝置佈建服務) 對稱金鑰]  作為連線方法。

1. 根據您的裝置作業系統，選擇 **Windows 上的 CMake 專案**或 **Linux 上的 CMake 專案**作為專案範本。

1. VS Code 會開啟新視窗，其中包含產生的裝置程式碼 Stub 檔案。

1. 建置程式碼之後，請輸入 DPS 認證 (**DPS 識別碼範圍**、**DPS 對稱金鑰**、**裝置識別碼**) 作為應用程式的參數。 若要從憑證入口網站取得認證，請參閱[連線並測試您的 IoT 隨插即用裝置](tutorial-certification-test.md#connect-and-discover-interfaces)。

    ```cmd/sh
    .\your_pnp_app.exe [DPS ID Scope] [DPS symmetric key] [device ID]
    ```

### <a name="implement-standard-interfaces"></a>實作標準介面

#### <a name="implement-the-model-information-and-sdk-information-interfaces"></a>實作模型資訊和 SDK 資訊介面

Azure IoT 裝置 SDK 會實作模型資訊和 SDK 資訊介面。 如果您在 VS Code 中使用程式碼產生功能，則裝置程式碼會使用 IoT 隨插即用裝置 SDK。

如果您選擇不使用 Azure IoT 裝置 SDK，您可以使用 SDK 原始程式碼作為您本身實作的參考。

#### <a name="implement-the-device-information-interface"></a>實作裝置資訊介面

在您的裝置上實作**裝置資訊**介面，並在執行時從裝置提供裝置特定資訊。

您可以將適用於 [Linux](https://github.com/Azure/azure-iot-sdk-c/tree/public-preview) 的**裝置資訊**介面的範例實作當作參考。

### <a name="implement-all-the-capabilities-defined-in-your-model"></a>實作模型中定義的所有功能

在認證期間，您的裝置會以程式設計方式進行測試，以確保它會實作其介面中定義的功能。 如果您的裝置未實作讀取/寫入屬性和命令要求，請使用 HTTP 狀態碼 501 加以回應。

## <a name="next-steps"></a>後續步驟

您已建置可供認證的 IoT 隨插即用裝置，建議執行的下一個步驟是：

> [!div class="nextstepaction"]
> [了解如何認證您的裝置](tutorial-certification-test.md)
