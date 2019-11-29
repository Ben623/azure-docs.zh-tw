---
title: 從 Azure 入口網站管理 IoT Central | Microsoft Docs
description: 本文說明如何從 Azure 入口網站建立和管理您的 IoT Central 應用程式。
services: iot-central
ms.service: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 10/02/2019
ms.topic: conceptual
manager: philmea
ms.openlocfilehash: c86df7c50e59309f921c60738870407e74a23219
ms.sourcegitcommit: 428fded8754fa58f20908487a81e2f278f75b5d0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/27/2019
ms.locfileid: "74555208"
---
# <a name="manage-iot-central-from-the-azure-portal"></a>從 Azure 入口網站管理 IoT Central

[!INCLUDE [iot-central-selector-manage](../../../includes/iot-central-selector-manage.md)]

您可以使用[Azure 入口網站](https://portal.azure.com)來管理您的應用程式，而不需要在[Azure IoT Central 應用程式管理員](https://aka.ms/iotcentral)網站上建立和管理 IoT Central 應用程式。

## <a name="create-iot-central-applications"></a>建立 IoT Central 應用程式

若要建立應用程式，請流覽至[Azure 入口網站](https://ms.portal.azure.com)並選取左側主窗格中的 [**建立資源**]。

![管理入口網站：導覽功能表](media/howto-manage-iot-central-from-portal/image0.png)

在搜尋列中輸入 **IoT Central**。

![管理入口網站：搜尋](media/howto-manage-iot-central-from-portal/image0a1.png)

在搜尋結果中選取 [ **IoT Central 應用程式**行] 專案。

![管理入口網站：搜尋結果](media/howto-manage-iot-central-from-portal/image0b1.png)

現在，選取 [建立]。

![管理入口網站：IoT Central 資源](media/howto-manage-iot-central-from-portal/image0c1.png)

填寫表單中的所有欄位。 此表單類似于您在[Azure IoT Central 應用程式管理員](https://aka.ms/iotcentral)網站上填寫來建立應用程式的表單。 如需詳細資訊，請參閱[建立 IoT Central 應用程式](quick-deploy-iot-central.md)快速入門。

您可以選取 [**範例 Contoso**]、[**自訂應用程式**] 和 [**範例 Devkits** ] 作為應用程式範本，以建立具有正式運作功能的 IoT Central 應用程式，所有其他應用程式範本都會使用公開預覽功能。

![建立 IoT Central 表單](media/howto-manage-iot-central-from-portal/image6a.png)

**Location**是您想要在其中建立應用程式的[地理](https://azure.microsoft.com/global-infrastructure/geographies/)位置。 通常，您應該選擇實際最接近裝置的位置，以取得最佳效能。 Azure IoT Central 目前適用于美國、**澳大利亞**、**亞太地區**或**歐洲** **地區**。  選擇位置之後，您便無法在稍後將應用程式移至不同的位置。

> [!NOTE]
> 預覽應用程式範本目前僅適用于**歐洲**和**美國**地區。

![管理入口網站：建立 IoT Central 資源](media/howto-manage-iot-central-from-portal/image1a.png)  

填寫所有欄位之後，請選取 [**建立**]。

## <a name="manage-existing-iot-central-applications"></a>管理現有的 IoT Central 應用程式

如果您已有 Azure IoT Central 應用程式，則可以將其刪除，或移至 Azure 入口網站中的其他訂用帳戶或資源群組。

> [!NOTE]
> 您無法查看 Azure 入口網站中的試用版應用程式，因為這類應用程式並未與您的訂用帳戶相關聯。

若要開始使用，請選取左側主窗格中的 [**所有資源**]。 使用搜尋方塊鍵入您應用程式的名稱，以在您的資源清單中找到該名稱。 然後選取您想要管理的 IoT Central 應用程式。

![管理入口網站：資源管理](media/howto-manage-iot-central-from-portal/image2a.png)

若要流覽至應用程式，請選取 [IoT Central 應用程式 URL]。

![管理入口網站：資源管理](media/howto-manage-iot-central-from-portal/image3.png)

若要將應用程式移至不同的資源群組，請選取資源群組旁的 [**變更**]。 在 [移動資源] 頁面上，挑選要將此應用程式遷移至的資源群組。

![管理入口網站：資源管理](media/howto-manage-iot-central-from-portal/image4a.png)

若要將應用程式移至不同的訂用帳戶，請選取訂用帳戶旁邊的**變更**連結。 在出現的對話方塊中，選擇要將此應用程式遷移至的訂用帳戶。

![管理入口網站：資源管理](media/howto-manage-iot-central-from-portal/image5a.png)

## <a name="next-steps"></a>後續步驟

您現在已了解如何從 Azure 入口網站中管理 Azure IoT Central 應用程式，以下是建議的後續步驟：

> [!div class="nextstepaction"]
> [管理您的應用程式](howto-administer.md)