---
title: 使用適用於 VS Code 的 Azure IoT Tools 建立 Azure IoT 中樞 | Microsoft Docs
description: 瞭解如何使用適用于 Visual Studio Code 的 Azure IoT 工具，在資源群組中建立 Azure IoT 中樞。
author: formulahendry
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 01/04/2019
ms.author: junhan
ms.openlocfilehash: 3fe4dfae2e86d549056805e87c0fd3927697fe24
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/09/2019
ms.locfileid: "73890558"
---
# <a name="create-an-iot-hub-using-the-azure-iot-tools-for-visual-studio-code"></a>使用適用於 Visual Studio Code 的 Azure IoT Tools 建立 IoT 中樞

[!INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

這篇文章為您示範如何使用[適用於 Visual Studio Code 的 Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) (英文)，建立 Azure IoT 中樞。 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

若要完成本文，您需要下列項目：

- Azure 訂閱。 如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

- [Visual Studio Code](https://code.visualstudio.com/)

- 適用於 Visual Studio Code 的 [Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)。

## <a name="create-an-iot-hub"></a>建立 IoT 中樞

1. 在 Visual Studio Code 中，開啟 [總管] 檢視。

2. 在總管底部，展開 [Azure IoT 中樞裝置] 區段。 

   ![展開 Azure IoT 中樞裝置](./media/iot-hub-create-use-iot-toolkit/azure-iot-hub-devices.png)

3. 按一下 [Azure IoT 中樞裝置] 區段標頭中的 [...]。 如果您沒有看到省略符號，將滑鼠停留在標頭上方。 

4. 選擇 [建立 IoT 中樞]。

5. 快顯視窗會顯示在右下角，讓您第一次登入 Azure。

6. 選取 Azure 訂用帳戶。 

7. 選取資源群組。

8. 選取位置。

9. 選取定價層。

10. 輸入 IoT 中樞的全域唯一名稱。

11. 等候幾分鐘，直到 IoT 中樞建立。

## <a name="next-steps"></a>後續步驟

現在您已使用適用於 Visual Studio Code 的 Azure IoT Tools 部署了 IoT 中樞。 若要進一步探索，請查看下列文章：

* [使用適用於 Visual Studio Code 的 Azure IoT Tools 來傳送和接收裝置與 IoT 中樞之間的訊息](iot-hub-vscode-iot-toolkit-cloud-device-messaging.md)。

* [使用適用於 Visual Studio Code 的 Azure IoT Tools 來管理 Azure IoT 中樞裝置](iot-hub-device-management-iot-toolkit.md)

* [請參閱 Azure IoT 中樞工具組 Wiki 頁面](https://github.com/microsoft/vscode-azure-iot-toolkit/wiki)。
