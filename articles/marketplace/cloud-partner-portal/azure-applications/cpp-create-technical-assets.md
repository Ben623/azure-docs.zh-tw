---
title: 建立 Azure 應用程式的技術資產 |Azure Marketplace
description: 建立 Azure 應用程式供應項目的技術資產。
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.topic: article
ms.date: 12/13/2018
ms.author: pabutler
ms.openlocfilehash: cbe1b8c8f1159d90fbf97eeae272c1c50ec9b9bb
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "64942993"
---
# <a name="prepare-your-azure-application-technical-assets"></a>準備 Azure 應用程式技術資產

本文會介紹可用來準備 Azure 應用程式供應項目技術資產的資源。

## <a name="before-you-begin"></a>開始之前

請觀看下列影片[建置 Azure Marketplace 的解決方案範本和受控應用程式](https://channel9.msdn.com/Events/Build/2018/BRK3603)，其會概述如何撰寫 Azure Resource Manager 範本以定義 Azure 應用程式解決方案，以及之後如何將應用程式供應項目發佈至 Azure Marketplace。

>[!VIDEO https://channel9.msdn.com/Events/Build/2018/BRK3603/player]


請參閱以下 Azure 應用程式文件，其中提供快速入門、教學課程和範例。

- [了解 Azure Resource Manager 範本](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authoring-templates)
- 快速入門：

  - [Azure 快速入門範本](https://azure.microsoft.com/documentation/templates/)
  - [GitHub Azure 快速入門範例](https://github.com/azure/azure-quickstart-templates) \(英文\)
  - [發佈應用程式定義](https://docs.microsoft.com/azure/managed-applications/publish-managed-app-definition-quickstart)
  - [部署服務目錄應用程式](https://docs.microsoft.com/azure/managed-applications/deploy-service-catalog-quickstart)

  
- 教學課程：

  - [建立定義檔案](https://docs.microsoft.com/azure/managed-applications/publish-service-catalog-app)
  - [發佈 Marketplace 應用程式](https://docs.microsoft.com/azure/managed-applications/publish-marketplace-app)

  - 範例：

    - [Azure CLI](https://docs.microsoft.com/azure/managed-applications/cli-samples)
    - [Azure PowerShell](https://docs.microsoft.com/azure/managed-applications/powershell-samples)
    - [受控應用程式解決方案](https://docs.microsoft.com/azure/managed-applications/sample-projects)

## <a name="fundamental-technical-knowledge"></a>基本技術知識

設計、建置和測試這些資產需要時間，且需要具備關於 Azure 平台與建置供應項目所用技術的技術知識。

您的工程小組應具備下列 Microsoft 技術的知識：

- 對於 [Azure 服務](https://azure.microsoft.com/services/)的基本了解
- 如何[設計和架構 Azure 應用程式](https://azure.microsoft.com/solutions/architecture/)
- 具備 [Azure 虛擬機器](https://azure.microsoft.com/services/virtual-machines/)、[Azure 儲存體](https://azure.microsoft.com/services/?filter=storage)和 [Azure 網路](https://azure.microsoft.com/services/?filter=networking)的運用知識
- 具備 [Azure Resource Manager](https://azure.microsoft.com/features/resource-manager/) 的運用知識
- 具備 [JSON](https://www.json.org/) 的運用知識

## <a name="suggested-tools"></a>建議的工具

選擇下列其中一種指令碼環境，或兩者均使用，以協助管理 Azure 應用程式：

- [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview)
- [Azure CLI](https://docs.microsoft.com/cli/azure)

建議您將下列工具新增至開發環境：

- [Azure 儲存體總管](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer)
- 具有下列擴充功能的 [Visual Studio Code](https://code.visualstudio.com/) \(英文\)：

  - 擴充功能：[Azure Resource Manager](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools) \(英文\)
  - 擴充功能：[Beautify](https://marketplace.visualstudio.com/items?itemName=HookyQR.beautify) \(英文\)
  - 擴充功能：[Prettify JSON](https://marketplace.visualstudio.com/items?itemName=mohsen1.prettify-json)

也建議您至 [Azure 開發人員工具](https://azure.microsoft.com/tools/) 頁面查看可用的工具，若您使用 Visual Studio，也請查看 [Visual Studio Marketplace](https://marketplace.visualstudio.com/)。

## <a name="next-steps"></a>後續步驟

[建立 Azure 應用程式供應項目](./cpp-create-offer.md)

