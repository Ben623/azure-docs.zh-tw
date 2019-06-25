---
title: 透過 Azure IoT 中樞裝置串流與使用 C 的裝置應用程式進行通訊 (預覽) | Microsoft Docs
description: 在本快速入門中，您會執行透過裝置串流與 IoT 裝置進行通訊的 C 裝置端應用程式。
author: rezasherafat
manager: briz
ms.service: iot-hub
services: iot-hub
ms.devlang: c
ms.topic: quickstart
ms.custom: mvc
ms.date: 03/14/2019
ms.author: rezas
ms.openlocfilehash: 6a2fe71a1086559d90adf5c464323f353be431aa
ms.sourcegitcommit: 4cdd4b65ddbd3261967cdcd6bc4adf46b4b49b01
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/06/2019
ms.locfileid: "66733316"
---
# <a name="quickstart-communicate-to-a-device-application-in-c-via-iot-hub-device-streams-preview"></a>快速入門：透過 IoT 中樞裝置串流與使用 C 的裝置應用程式進行通訊 (預覽)

[!INCLUDE [iot-hub-quickstarts-3-selector](../../includes/iot-hub-quickstarts-3-selector.md)]

Azure IoT 中樞目前支援裝置串流作為[預覽功能](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

[IoT 中樞裝置串流](iot-hub-device-streams-overview.md)可讓服務和裝置應用程式以安全且便於設定防火牆的方式進行通訊。 在公開預覽期間，C SDK 僅支援裝置端上的裝置串流。 因此，本快速入門僅提供執行裝置端應用程式的指示。 若要執行隨附的服務端應用程式，請參閱：
 
   * [透過 IoT 中樞裝置串流與使用 C# 的裝置應用程式進行通訊](./quickstart-device-streams-echo-csharp.md)
   * [透過 IoT 中樞裝置串流與使用 Node.js 的裝置應用程式進行通訊](./quickstart-device-streams-echo-nodejs.md)

本快速入門中的裝置端 C 應用程式具有下列功能：

* 建立對 IoT 裝置的裝置串流。
* 接收從服務端應用程式傳來的資料並加以回應。

程式碼會示範裝置串流的起始程序，以及如何用它來傳送和接收資料。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) 。

## <a name="prerequisites"></a>必要條件

* 裝置串流的預覽版目前僅支援在下列區域建立的 IoT 中樞：

  * 美國中部
  * 美國中部 EUAP

* 安裝 [Visual Studio 2017](https://www.visualstudio.com/vs/) 並啟用[使用 C++ 的桌面開發](https://www.visualstudio.com/vs/support/selecting-workloads-visual-studio-2017/)工作負載。

* 安裝最新版的 [Git](https://git-scm.com/download/)。

* 執行下列命令，將適用於 Azure CLI 的 Azure IoT 擴充功能新增至您的 Cloud Shell 執行個體。 IoT 擴充功能可將 IoT 中樞、IoT Edge 和 IoT 裝置佈建服務的特定命令新增至 Azure CLI。

   ```azurecli-interactive
   az extension add --name azure-cli-iot-ext
   ```

## <a name="prepare-the-development-environment"></a>準備開發環境

針對此快速入門，您會使用[適用於 C 的 Azure IoT 裝置 SDK](iot-hub-device-sdk-c-intro.md)。您會準備用來從 GitHub 複製並建置 [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) \(英文\) 的開發環境。 GitHub 上的 SDK 包括此快速入門中使用的範例程式碼。

1. 下載 [CMake 建置系統](https://cmake.org/download/)。

    在開始安裝 CMake 之前，請務必將 Visual Studio 必要條件 (Visual Studio 和「使用 C++ 的桌面開發」  工作負載) 安裝在您的機器上。 在符合先決條件並驗證過下載項目之後，您可以安裝 CMake 建置系統。

2. 開啟命令提示字元或 Git Bash 殼層。 執行下列命令以複製 [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) GitHub 存放庫：

    ```
    git clone https://github.com/Azure/azure-iot-sdk-c.git --recursive -b public-preview
    ```

    這項作業應該需要幾分鐘的時間。

3. 在 Git 存放庫的根目錄中建立 cmake  子目錄 (如下列命令所示)，然後前往該資料夾。

    ```
    cd azure-iot-sdk-c
    mkdir cmake
    cd cmake
    ```

4. 從 cmake  目錄執行下列命令，以建置您開發用戶端平台特有的 SDK 版本。

   * 在 Linux 中：

      ```bash
      cmake ..
      make -j
      ```

   * 在 Windows 中，請在 Visual Studio 2015 或 2017 的開發人員命令提示字元中執行下列命令。 cmake  目錄中會產生模擬裝置的 Visual Studio 解決方案。

      ```cmd
      rem For VS2015
      cmake .. -G "Visual Studio 14 2015"

      rem Or for VS2017
      cmake .. -G "Visual Studio 15 2017"

      rem Then build the project
      cmake --build . -- /m /p:Configuration=Release
      ```

## <a name="create-an-iot-hub"></a>建立 IoT 中樞

[!INCLUDE [iot-hub-include-create-hub-device-streams](../../includes/iot-hub-include-create-hub-device-streams.md)]

## <a name="register-a-device"></a>註冊裝置

您必須向 IoT 中樞註冊裝置，才能進行連線。 在本節中，您會使用 Azure Cloud Shell 搭配 [IoT 擴充功能](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot?view=azure-cli-latest)來註冊模擬裝置。

1. 若要建立裝置身分識別，請在 Cloud Shell 中執行下列命令：

   > [!NOTE]
   > * 以您為 IoT 中樞選擇的名稱取代 YourIoTHubName  預留位置。
   > * 使用所示的 MyCDevice  。 這是為已註冊裝置指定的名稱。 如果您為裝置選擇不同的名稱，請在本文中使用該名稱，並先在應用程式範例中更新該裝置名稱，再執行應用程式。

    ```azurecli-interactive
    az iot hub device-identity create --hub-name YourIoTHubName --device-id MyDevice
    ```

2. 若要針對您剛註冊的裝置取得「裝置連接字串」  ，請在 Azure Cloud Shell 中執行下列命令：

   > [!NOTE]
   > 以您為 IoT 中樞選擇的名稱取代 YourIoTHubName  預留位置。

    ```azurecli-interactive
    az iot hub device-identity show-connection-string --hub-name YourIoTHubName --device-id MyDevice --output table
    ```

    請記下儲存連接字串，以供稍後在本快速入門中使用。 看起來會像下列範例：

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyDevice;SharedAccessKey={YourSharedAccessKey}`

## <a name="communicate-between-the-device-and-the-service-via-device-streams"></a>透過裝置串流在裝置與服務之間進行通訊

在本節中，您會執行裝置端應用程式和服務端應用程式，並在兩者之間進行通訊。

### <a name="run-the-device-side-application"></a>執行裝置端應用程式

若要執行裝置端應用程式，請執行下列動作：

1. 編輯 iothub_client/samples/iothub_client_c2d_streaming_sample  資料夾中的 iothub_client_c2d_streaming_sample.c  來源檔案，然後提供您的裝置連接字串，以提供您的裝置認證。

   ```C
   /* Paste in your iothub connection string  */
   static const char* connectionString = "[device connection string]";
   ```

2. 依照下列方式編譯程式碼：

   ```bash
   # In Linux
   # Go to the sample's folder cmake/iothub_client/samples/iothub_client_c2d_streaming_sample
   make -j
   ```

   ```cmd
   rem In Windows
   rem Go to the cmake folder at the root of repo
   cmake --build . -- /m /p:Configuration=Release
   ```

3. 執行已編譯的程式：

   ```bash
   # In Linux
   # Go to the sample's folder cmake/iothub_client/samples/iothub_client_c2d_streaming_sample
   ./iothub_client_c2d_streaming_sample
   ```

   ```cmd
   rem In Windows
   rem Go to the sample's release folder cmake\iothub_client\samples\iothub_client_c2d_streaming_sample\Release
   iothub_client_c2d_streaming_sample.exe
   ```

### <a name="run-the-service-side-application"></a>執行服務端應用程式

如先前所述，IoT 中樞 C SDK 僅支援裝置端上的裝置串流。 若要建置及執行服務端應用程式，請遵循下列其中一個快速入門中的指示：

* [透過 IoT 中樞裝置串流與使用 C# 的裝置應用程式進行通訊](./quickstart-device-streams-echo-csharp.md)
* [透過 IoT 中樞裝置串流與使用 Node.js 的裝置應用程式進行通訊](./quickstart-device-streams-echo-nodejs.md)

## <a name="clean-up-resources"></a>清除資源

[!INCLUDE [iot-hub-quickstarts-clean-up-resources-device-streams](../../includes/iot-hub-quickstarts-clean-up-resources-device-streams.md)]

## <a name="next-steps"></a>後續步驟

在本快速入門中，您已設定 IoT 中樞、註冊裝置、在裝置和服務端上的其他應用程式建立 C 應用程式之間的裝置串流，以及使用串流在應用程式之間來回傳送資料。

若要深入了解裝置串流，請參閱：

> [!div class="nextstepaction"]
> [裝置串流概觀](./iot-hub-device-streams-overview.md)
