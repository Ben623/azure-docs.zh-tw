---
title: 在 Kubernetes 上使用 Azure Dev Spaces 以 Visual Studio Code 和 Java 進行偵錯和逐一查看
titleSuffix: Azure Dev Spaces
author: zr-msft
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.author: zarhoads
ms.date: 07/08/2019
ms.topic: quickstart
description: 在 Azure 上使用容器、微服務和 Java 快速進行 Kubernetes 開發
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, 容器, Java, Helm, 服務網格, 服務網格路由傳送, kubectl, k8s
manager: gwallace
ms.openlocfilehash: 2154ec3ae99ae816b970c96ffde435f1a3366e99
ms.sourcegitcommit: 80da36d4df7991628fd5a3df4b3aa92d55cc5ade
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/02/2019
ms.locfileid: "71815867"
---
# <a name="quickstart-debug-and-iterate-with-visual-studio-code-and-java-on-kubernetes-using-azure-dev-spaces"></a>快速入門：在 Kubernetes 上使用 Azure Dev Spaces 以 Visual Studio Code 和 Java 進行偵錯和逐一查看

在本指南中，您將了解如何：

- 使用 Azure 中受管理的 Kubernetes 叢集，設定 Azure Dev Spaces。
- 使用 Visual Studio Code 在容器中反覆開發程式碼。
- 從 Visual Studio Code 對開發人員空間中的程式碼進行偵錯。

Azure Dev Spaces 也可讓您使用下列工具進行偵錯和逐一查看：
- [Node.js 和 Visual Studio Code](quickstart-nodejs.md)
- [.NET Core 和 Visual Studio Code](quickstart-netcore.md)
- [.NET Core 和 Visual Studio](quickstart-netcore-visualstudio.md)

## <a name="prerequisites"></a>必要條件

- Azure 訂用帳戶。 如果您沒有帳戶，您可以建立[免費帳戶](https://azure.microsoft.com/free)。
- [已安裝 Visual Studio Code](https://code.visualstudio.com/download)。
- 已安裝適用於 Visual Studio Code 的 [Azure Dev Spaces](https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds) 和[適用於 Azure Dev Spaces 的 Java 偵錯工具](https://marketplace.visualstudio.com/items?itemName=vscjava.vscode-java-debugger-azds)擴充功能。
- [已安裝 Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest)。
- [已安裝並設定 Maven](https://maven.apache.org)。

## <a name="create-an-azure-kubernetes-service-cluster"></a>建立 Azure Kubernetes Service 叢集

您必須在[支援的區域][supported-regions]中建立 AKS 叢集。 下列命令會建立名為 MyResourceGroup  的資源群組與名為 MyAKS  的 AKS 叢集。

```cmd
az group create --name MyResourceGroup --location eastus
az aks create -g MyResourceGroup -n MyAKS --location eastus --disable-rbac --generate-ssh-keys
```

## <a name="enable-azure-dev-spaces-on-your-aks-cluster"></a>在 AKS 叢集上啟用 Azure Dev Spaces

使用 `use-dev-spaces` 命令在 AKS 叢集上啟用 Dev Spaces，並遵循提示來進行。 下列命令會在 MyResourceGroup  群組中的 MyAKS  叢集上啟用 Dev Spaces，並建立「預設」  開發空間。

> [!NOTE]
> `use-dev-spaces` 命令也會安裝 Azure Dev Spaces CLI (如果尚未安裝)。 您無法在 Azure Cloud Shell 中安裝 Azure Dev Spaces CLI。

```cmd
$ az aks use-dev-spaces -g MyResourceGroup -n MyAKS

'An Azure Dev Spaces Controller' will be created that targets resource 'MyAKS' in resource group 'MyResourceGroup'. Continue? (y/N): y

Creating and selecting Azure Dev Spaces Controller 'MyAKS' in resource group 'MyResourceGroup' that targets resource 'MyAKS' in resource group 'MyResourceGroup'...2m 24s

Select a dev space or Kubernetes namespace to use as a dev space.
 [1] default
Type a number or a new name: 1

Kubernetes namespace 'default' will be configured as a dev space. This will enable Azure Dev Spaces instrumentation for new workloads in the namespace. Continue? (Y/n): Y

Configuring and selecting dev space 'default'...3s

Managed Kubernetes cluster 'MyAKS' in resource group 'MyResourceGroup' is ready for development in dev space 'default'. Type `azds prep` to prepare a source directory for use with Azure Dev Spaces and `azds up` to run.
```

## <a name="get-sample-application-code"></a>取得應用程式範例的程式碼

在本文中，您會使用 [Azure Dev Spaces 應用程式範例](https://github.com/Azure/dev-spaces)來示範如何使用 Azure Dev Spaces。

從 GitHub 複製應用程式。

```cmd
git clone https://github.com/Azure/dev-spaces
```

## <a name="prepare-the-sample-application-in-visual-studio-code"></a>在 Visual Studio Code 中準備範例應用程式

開啟 Visual Studio Code，依序按一下 [檔案]  和 [開啟...]  ，瀏覽至 dev-spaces/samples/java/getting-started/webfrontend  目錄，然後按一下[開啟]  。

您現在已在 Visual Studio Code 中開啟 *webfrontend* 專案。 若要在您的開發空間中執行應用程式，請在命令選擇區中使用 Azure Dev Spaces 擴充功能產生 Docker 和 Helm 圖表資產。

若要在 Visual Studio Code 中開啟命令選擇區，請依序按一下 [檢視]  和 [命令選擇區]  。 開始輸入 `Azure Dev Spaces`，然後按一下 `Azure Dev Spaces: Prepare configuration files for Azure Dev Spaces`。

![準備 Azure Dev Spaces 的組態檔](./media/common/command-palette.png)

當 Visual Studio Code 也提示您設定基礎映像、公開的連接埠和公用端點時，請選擇 `Azul Zulu OpenJDK for Azure (Free LTS)` 作為基礎映像，選擇 `8080` 作為公開的連接埠，以及選擇 `Yes` 來啟用公用端點。

![選取基礎映像](media/get-started-java/select-base-image.png)

![選取公開的連接埠](media/get-started-java/select-exposed-port.png)

![選取公用端點](media/get-started-java/select-public-endpoint.png)

此命令會產生 Dockerfile 和 Helm 圖表，讓您的專案準備好執行 Azure Dev Spaces。 其也會產生 .vscode  目錄，且專案的根目錄中會有偵錯設定。

## <a name="build-and-run-code-in-kubernetes-from-visual-studio"></a>從 Visual Studio 在 Kubernetes 中建置和執行程式碼

按一下左側的 [偵錯]  圖示，然後按一下頂端的 [啟動 Java 程式 (AZDS)]  。

![啟動 Java 程式](media/get-started-java/debug-configuration.png)

此命令會在 Azure Dev Spaces 中建置和執行您的服務。 底部的 [終端機]  視窗會顯示您在 Azure Dev Spaces 中所執行服務的建置輸出和 URL。 [偵錯主控台]  會顯示記錄輸出。

> [!Note]
> 如果您未在 [命令選擇區]  中看到任何 Azure Dev Spaces 命令，請確定您已安裝[適用於 Azure Dev Spaces 的 Visual Studio Code 擴充功能](https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds)。 也請確認您已在 Visual Studio Code 中開啟 dev-spaces/samples/java/getting-started/webfrontend  目錄。

您可以開啟公用 URL，查看執行的服務。

依序按一下 [偵錯]  和 [停止偵錯]  來停止偵錯工具。

## <a name="update-code"></a>更新程式碼

若要部署更新過的服務版本，您可以在專案中更新任何檔案，然後重新執行「啟動 Java 程式 (AZDS)」  。 例如︰

1. 如果您的應用程式仍在執行中，請按一下 [偵錯]  ，然後按遺下 [停止偵錯]  來停止它。
1. 將 [`src/main/java/com/ms/sample/webfrontend/Application.java` 中的第 19 行](https://github.com/Azure/dev-spaces/blob/master/samples/java/getting-started/webfrontend/src/main/java/com/ms/sample/webfrontend/Application.java#L19) \(英文\) 更新為：
    
    ```java
    return "Hello from webfrontend in Azure!";
    ```

1. 儲存您的變更。
1. 重新執行 [啟動 Java 程式 (AZDS)]  。
1. 瀏覽至執行中的服務，然後觀察您的變更。
1. 依序按一下 [偵錯]  和 [停止偵錯]  來停止您的應用程式。

## <a name="setting-and-using-breakpoints-for-debugging"></a>設定和使用偵錯的中斷點

使用 [啟動 Java 程式 (AZDS)]  啟動您的服務。 這也會在偵錯模式中執行您的服務。

依序按一下 [檢視]  和 [總管]  ，來瀏覽回到 [總管]  檢視。 開啟 `src/main/java/com/ms/sample/webfrontend/Application.java`，然後在第 19 行的某處按一下來將游標放在該處。 若要設定中斷點，請按 F9  ，或依序按一下 [偵錯]  和 [切換中斷點]  。

在瀏覽器中開啟您的服務，並注意其中並未顯示任何訊息。 返回 Visual Studio Code，您會看到第 19 行已醒目提示。 您所設定的中斷點已讓服務在第 19 行暫停。 若要讓服務繼續，請按 F5  ，或依序按一下 [偵錯]  和 [繼續]  。 返回您的瀏覽器，並注意現在會顯示訊息。

在連結了偵錯工具的 Kubernetes 中執行您的服務時，您可以完整地存取偵錯資訊，例如呼叫堆疊、區域變數和例外狀況資訊。

藉由將游標放在 `src/main/java/com/ms/sample/webfrontend/Application.java` 中的第 19 行上並按下 *F9* 來移除中斷點。

## <a name="update-code-from-visual-studio-code"></a>從 Visual Studio Code 更新程式碼

在服務以偵錯模式執行時，更新 `src/main/java/com/ms/sample/webfrontend/Application.java` 中的第 19 行。 例如︰
```java
return "Hello from webfrontend in Azure while debugging!";
```

儲存檔案。 依序按一下 [偵錯]  和 [重新啟動偵錯]  ，或在 [偵錯工具列]  中按一下 [重新啟動偵錯]  按鈕。

![重新整理偵錯](media/common/debug-action-refresh.png)

在瀏覽器中開啟您的服務，並注意您已更新的訊息會顯示出來。

Azure Dev Spaces 會以累加方式重新編譯現有容器中的程式碼，以提供更快的編輯/偵錯迴圈，而不是在每次進行程式碼編輯時重新建置及重新部署新的容器映像。

## <a name="clean-up-your-azure-resources"></a>清除 Azure 資源

```cmd
az group delete --name MyResourceGroup --yes --no-wait
```

## <a name="next-steps"></a>後續步驟

了解 Azure Dev Spaces 如何協助您跨多個容器開發更複雜的應用程式，以及如何藉由在不同的空間中使用不同的程式碼版本或分支，來簡化共同開發。

> [!div class="nextstepaction"]
> [使用多個容器和小組開發](multi-service-java.md)


[supported-regions]: about.md#supported-regions-and-configurations
