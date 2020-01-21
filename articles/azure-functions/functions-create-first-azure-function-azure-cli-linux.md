---
title: 在 Azure 中的 Linux 上建立您的第一個函式
description: 了解如何使用命令列工具、Azure Functions Core Tools 和 Azure CLI 在 Azure 中建立第一個 Linux 上裝載的函式。
ms.date: 03/12/2019
ms.topic: quickstart
ms.custom: mvc, fasttrack-edit
ms.openlocfilehash: 972feedf880ed55210c8422094d5b26a85b31d5e
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/09/2020
ms.locfileid: "75769399"
---
# <a name="quickstart-create-your-first-function-hosted-on-linux-using-command-line-tools"></a>快速入門：使用命令列工具建立第一個在 Linux 上裝載的函式

Azure Functions 可讓您在[無伺服器](https://azure.com/serverless) Linux 環境中執行程式碼，而不需要先建立 VM 或發佈 Web 應用程式。 Linux 裝載功能需要 [Functions 2.x 和更新版本的執行階段](functions-versions.md)。 無伺服器函式會在[取用方案](functions-scale.md#consumption-plan)中執行。

本快速入門文章會逐步解說如何使用 Azure CLI 建立第一個在 Linux 上執行的函式應用程式。 您可以使用 [Azure Functions Core Tools](functions-run-local.md) 在本機建立函式程式碼，然後將其部署至 Azure。

下列步驟適用於 Mac、Windows 或 Linux 電腦。 本文說明如何以 JavaScript 或 C# 建立函式。 若要了解如何建立 Python 函式，請參閱[使用 Core Tools 和 Azure CLI 建立第一個 Python 函式](functions-create-first-function-python.md)。

## <a name="prerequisites"></a>Prerequisites

在執行此範例之前，您必須具備下列項目︰

+ 安裝 [Azure Functions Core Tools](./functions-run-local.md#v2) 2.6.666 版或更新版本。

+ 安裝 [Azure CLI]( /cli/azure/install-azure-cli)。 此文章需要 Azure CLI 2.0 版或更新版本。 執行 `az --version` 以尋找您擁有的版本。 您也可以使用 [Azure Cloud Shell](https://shell.azure.com/bash)。

+ 有效的 Azure 訂用帳戶。

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [functions-create-function-app-cli](../../includes/functions-create-function-app-cli.md)]

## <a name="enable-extension-bundles"></a>啟用延伸模組搭售方案

[!INCLUDE [functions-extension-bundles](../../includes/functions-extension-bundles.md)]

[!INCLUDE [functions-create-function-core-tools](../../includes/functions-create-function-core-tools.md)]

[!INCLUDE [functions-run-function-test-local](../../includes/functions-run-function-test-local.md)]

[!INCLUDE [functions-create-resource-group](../../includes/functions-create-resource-group.md)]

[!INCLUDE [functions-create-storage-account](../../includes/functions-create-storage-account.md)]

## <a name="create-a-linux-function-app-in-azure"></a>在 Azure 中建立 Linux 函式應用程式

您必須擁有函式應用程式以便在 Linux 上主控函式的執行。 函式應用程式可提供無伺服器環境讓您執行函式程式碼。 其可讓您將多個函式群組為邏輯單位，以方便您管理、部署、調整和共用資源。 使用 [az functionapp create](/cli/azure/functionapp#az-functionapp-create) 命令，建立在 Linux 上執行的函式應用程式。

在下列命令中，請使用唯一函式應用程式名稱來替代您看到的 `<app_name>` 預留位置，並使用儲存體帳戶名稱來替代 `<storage_name>`。 `<app_name>` 也是函式應用程式的預設 DNS 網域。 此名稱在 Azure 中的所有應用程式之間必須是唯一的。 您也應該從 `dotnet` (C#)、`node` (JavaScript/TypeScript) 或 `python`，設定函數應用程式的 `<language>` 執行階段。

```azurecli-interactive
az functionapp create --resource-group myResourceGroup --consumption-plan-location westus --os-type Linux \
--name <app_name> --storage-account  <storage_name> --runtime <language>
```

建立函式應用程式之後，您會看到下列訊息：

```output
Your serverless Linux function app 'myfunctionapp' has been successfully created.
To active this function app, publish your app content using Azure Functions Core Tools or the Azure portal.
```

現在，您可以將專案發佈至 Azure 中的新函式應用程式。

[!INCLUDE [functions-publish-project](../../includes/functions-publish-project.md)]

[!INCLUDE [functions-test-function-code](../../includes/functions-test-function-code.md)]

[!INCLUDE [functions-cleanup-resources](../../includes/functions-cleanup-resources.md)]

[!INCLUDE [functions-quickstart-next-steps-cli](../../includes/functions-quickstart-next-steps-cli.md)]