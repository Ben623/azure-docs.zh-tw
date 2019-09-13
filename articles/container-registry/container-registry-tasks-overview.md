---
title: 使用 Azure Container Registry 工作 (ACR 工作) 自動建立及修補容器映射
description: ACR 工作簡介, 這是 Azure Container Registry 中的一套功能, 可在雲端中提供安全、自動化的容器映射組建、管理和修補。
services: container-registry
author: dlepow
manager: gwallace
ms.service: container-registry
ms.topic: article
ms.date: 09/05/2019
ms.author: danlep
ms.openlocfilehash: c62987031a73aa4840c1d036689a3c52fb4dc4a0
ms.sourcegitcommit: 083aa7cc8fc958fc75365462aed542f1b5409623
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/11/2019
ms.locfileid: "70914669"
---
# <a name="automate-container-image-builds-and-maintenance-with-acr-tasks"></a>使用 ACR 工作自動化容器映射組建和維護

容器提供新的虛擬化層級，並且隔離應用程式和開發人員相依性與基礎結構和作業需求。 不過, 仍然需要解決此應用程式虛擬化如何透過容器生命週期進行管理和修補。

## <a name="what-is-acr-tasks"></a>什麼是 ACR 工作？

「ACR 工作」是 Azure Container Registry 內的一套功能。 它提供以雲端為基礎的容器映射，適用于包含 Linux、Windows 和 ARM 的[平臺](#image-platforms)，並可自動為您的 Docker 容器進行[OS 和架構修補](#automate-os-and-framework-patching)。 ACR 工作不僅會使用隨選容器映射組建將您的「內部迴圈」開發週期延伸到雲端，還可讓原始程式碼更新所觸發的自動化組建、容器基底映射或計時器的更新。 例如，透過基底映射更新觸發程式，您可以將作業系統和應用程式架構修補工作流程自動化，維護安全環境，同時遵守不可變容器的原則。

## <a name="task-scenarios"></a>工作案例

ACR 工作支援數種建立和維護容器映射和其他成品的案例。 如需詳細資訊，請參閱本文中的下列各節。

* **[快速](#quick-task)** 工作-在 Azure 中建立單一容器映射並隨選推送至容器登錄，而不需要本機 Docker 引擎安裝。 請思考一下雲端中的 `docker build`、`docker push`。
* **自動觸發**的工作-啟用一或多個*觸發*程式來建立映射：
  * **[原始程式碼更新的觸發程式](#trigger-task-on-source-code-update)** 
  * **[基底映射更新的觸發程式](#automate-os-and-framework-patching)** 
  * **[依排程觸發](#schedule-a-task)** 
* **[多步驟](#multi-step-tasks)** 工作-使用多步驟、多容器型工作流程，擴充 ACR 工作的單一映射組建和推送功能。 

每個 ACR 工作都有相關聯的[原始程式碼內容](#context-locations)，也就是用來建立容器映射或其他成品的一組原始程式檔的位置。 範例內容包含 Git 存放庫或本機檔案系統。

工作也可以利用[執行變數](container-registry-tasks-reference-yaml.md#run-variables)，因此您可以重複使用工作定義，並將影像和成品的標記標準化。

## <a name="quick-task"></a>快速工作

內部迴圈開發週期 (在認可至原始檔控制前的撰寫程式碼、建置及測試應用程式的反覆程序) 是容器生命週期管理的開端。

「ACR 工作」的[快速工作](container-registry-tutorial-quick-task.md)功能可在您認可第一行程式碼之前，藉由將您的容器映像建置卸交給 Azure，提供一個整合式開發體驗。 使用快速工作時，您可以在認可程式碼之前，先確認您的自動化建置定義並攔截可能的問題。

使用熟悉`docker build`的格式時, Azure CLI 中的[az acr build][az-acr-build]命令會採用[內容](#context-locations)(要建立的一組檔案), 將它傳送 ACR 工作, 而且根據預設, 會在完成時將建立的映射推送至其登錄。

如需簡介, 請參閱在 Azure Container Registry 中[建立和執行容器映射](container-registry-quickstart-task-cli.md)的快速入門。  

「ACR 工作」已設計為容器生命週期原始物件。 例如，您可以將「ACR 工作」整合到 CI/CD 解決方案中。 藉由執行[az login][az-login]與[服務主體][az-login-service-principal]，您的 CI/CD 解決方案就可以發出[az acr build][az-acr-build]命令來啟動映射組建。

若要了解如何使用快速工作，請參閱第一個「ACR 工作」教學課程：[使用 Azure Container Registry 工作在雲端中建置容器映像](container-registry-tutorial-quick-task.md)。

> [!TIP]
> 如果您想要直接從原始程式碼建立和推送映射，但沒有 Dockerfile，Azure Container Registry 提供[az acr pack build][az-acr-pack-build]命令（preview）。 此工具會使用[雲端原生 Buildpacks](https://buildpacks.io/)，從應用程式原始程式碼建立和推送映射。

## <a name="trigger-task-on-source-code-update"></a>原始程式碼更新的觸發程式工作

在 GitHub 或 Azure DevOps 中將程式碼認可或提取要求或更新至 Git 存放庫時，觸發容器映射組建或多步驟工作。 例如，藉由指定 Git 存放庫和選擇性的分支和 Dockerfile，使用 Azure CLI 命令[az acr task create][az-acr-task-create]來設定組建工作。 當您的小組更新儲存機制中的程式碼時，ACR 工作建立的 webhook 會觸發存放庫中所定義之容器映射的組建。 

當您將 Git 存放庫設定為工作的內容時，ACR 工作支援下列觸發程式：

| 觸發程序 | 預設為啟用 |
| ------- | ------------------ |
| 認可 | 是 |
| 提取要求 | 否 |

若要設定觸發程式，請提供工作個人存取權杖（PAT），以在 GitHub 或 Azure DevOps 存放庫中設定 webhook。

若要了解如何在認可原始程式碼時觸發建置，請參閱第二個「ACR 工作」教學課程：[使用 Azure Container Registry 工作自動執行容器映像建置](container-registry-tutorial-build-task.md)。

## <a name="automate-os-and-framework-patching"></a>自動進行作業系統和架構修補

「ACR 工作」之所以能夠真正增強您的容器建置工作流程，是因為它能夠偵測基底映像的更新。 當更新的基底映射推送至您的登錄, 或在公用儲存機制 (例如 Docker Hub) 中更新基底映射時, ACR 工作可以根據它自動建立任何應用程式映射。

容器映像可概括地分類為「基底」映像和「應用程式」映像。 您的基底映像通常包含您的應用程式建置所在的作業系統和應用程式架構，以及其他自訂項目。 這些基底映像本身通常是以公用上游映像為基礎，例如：[Alpine Linux][base-alpine]、 [Windows][base-windows]、 [.net][base-dotnet]或[node.js][base-node]。 您有數個應用程式映像可能會共用一個通用基底映像。

當上游維護程式 (例如重要 OS 安全性修補程式) 更新作業系統或應用程式架構映像時，您也必須更新您的基底映像以包含重要修正。 接著，還必須重建每個應用程式映像，以包含現在包含在基底映像中的這些上游修正。

由於「ACR 工作」會在建置容器映像時動態地探索基底映像相依性，因此它可以偵測到應用程式映像的基底映像何時更新。 「ACR 工作」會接著使用一個預先設定的[建置工作](container-registry-tutorial-base-image-update.md#create-a-task)，為您**自動重建每個應用程式映像**。 透過這個自動偵測和重建功能，「ACR 工作」便可讓您針對參考已更新之基底映像的每個應用程式映像，省下手動追蹤及更新通常所需的時間與精力。

針對來自 Dockerfile 的映射組建，當基底映射位於下列其中一個位置時，ACR 工作會追蹤基底映射更新：

* 執行工作所在的相同 Azure 容器登錄
* 相同區域中的其他 Azure 容器登錄 
* Docker Hub 中的公用存放庫
* Microsoft 容器登錄中的公用存放庫

> [!NOTE]
> * 預設會在 ACR 工作中啟用基底映射更新觸發程式。 
> * 目前，ACR 工作只會追蹤應用程式（*運行*時間）映射的基底映射更新。 ACR 工作不會追蹤用於多階段 Dockerfile 之中繼（*buildtime*）映射的基底映射更新。 

在第三個 ACR 工作教學課程中深入瞭解 OS 和架構修補、[使用 Azure Container Registry 工作自動化基底映射更新上的映射組建](container-registry-tutorial-base-image-update.md)。

## <a name="schedule-a-task"></a>排程工作

當您建立或更新工作時，藉由設定一或多個*計時器觸發*程式，選擇性地排程工作。 排程工作適用于依照定義的排程執行容器工作負載，或對定期推送至您的登錄的映射執行維護作業或測試。 如需詳細資訊，請參閱依[定義的排程執行 ACR](container-registry-tasks-scheduled.md)工作。

## <a name="multi-step-tasks"></a>多步驟工作

多步驟工作提供以步驟為基礎的工作定義和執行, 以便在雲端中建立、測試及修補容器映射。 在[YAML](container-registry-tasks-reference-yaml.md)檔中定義的工作步驟會指定容器映射或其他成品的個別組建和推送作業。 它們也可以定義一或多個容器的執行，其中每個步驟都使用容器作為其執行環境。

例如，您可以建立一個自動執行下列操作的多步驟工作：

1. 建置 Web 應用程式映像
1. 執行 Web 應用程式容器
1. 建置 Web 應用程式測試映像
1. 執行 web 應用程式測試容器，它會對執行中的應用程式容器執行測試
1. 如果測試通過，便建置 Helm 圖表封存套件
1. 使用新的 Helm 圖表封存套件來執行 `helm upgrade`

多步驟工作可讓您將映像的建置、執行及測試，分割成更多可組合且具有步驟間相依性支援的步驟。 藉由「ACR 工作」中的多步驟工作，您可以更細微地控制映像建置、測試及 OS 和架構修補工作流程。

如需了解多步驟工作，請參閱[執行 ACR 工作中的多步驟建置、測試及修補工作](container-registry-tasks-multi-step.md)。

## <a name="context-locations"></a>內容位置

下表顯示「ACR 工作」的幾個所支援內容位置範例：

| 內容位置 | 描述 | 範例 |
| ---------------- | ----------- | ------- |
| 本機檔案系統 | 本機檔案系統上目錄內的檔案。 | `/home/user/projects/myapp` |
| GitHub 主要分支 | GitHub 存放庫之主要 (或其他預設) 分支內的檔案。  | `https://github.com/gituser/myapp-repo.git` |
| GitHub 分支 | GitHub 存放庫的特定分支。| `https://github.com/gituser/myapp-repo.git#mybranch` |
| GitHub 子資料夾 | GitHub 存放庫中子資料夾內的檔案。 範例會顯示分支和子資料夾規格的組合。 | `https://github.com/gituser/myapp-repo.git#mybranch:myfolder` |
| 遠端 Tarball | 遠端 Web 伺服器上壓縮封存中的檔案。 | `http://remoteserver/myapp.tar.gz` |

## <a name="image-platforms"></a>映射平臺

根據預設，ACR 工作會建立 Linux OS 和 amd64 架構的映射。 `--platform`指定標記以建立其他架構的 Windows 映像或 Linux 映射。 指定 os/架構格式（例如， `--platform Linux/arm`）的作業系統和選擇性的支援架構。 針對 ARM 架構，選擇性地指定 OS/架構/變異格式的 variant （例如， `--platform Linux/arm64/v8`）：

| OS | 架構|
| --- | ------- | 
| Linux | amd64<br/>arm<br/>arm64<br/>386 |
| Windows | amd64 |

## <a name="view-task-logs"></a>查看工作記錄

每個工作執行都會產生可供您檢查的記錄輸出, 以判斷工作步驟是否已順利執行。 如果您使用[az acr build](/cli/azure/acr#az-acr-build)、 [az acr run](/cli/azure/acr#az-acr-run)或[az acr task run](/cli/azure/acr/task#az-acr-task-run)命令來觸發工作, 則會將工作執行的記錄輸出串流處理到主控台, 並儲存以供日後抓取。 當工作自動觸發時（例如，由原始程式碼認可或基底映射更新），只會儲存工作記錄。 查看在 Azure 入口網站中執行之工作的記錄, 或使用[az acr task logs](/cli/azure/acr/task#az-acr-task-logs)命令。

根據預設，在登錄中執行工作的資料和記錄會保留30天，然後自動清除。 如果您想要封存工作執行的資料, 請使用[az acr task update-run](/cli/azure/acr/task#az-acr-task-update-run)命令來啟用保存。 下列範例會在登錄*myregistry*中啟用 [工作執行*cf11* ] 的封存。

```azurecli
az acr task update-run --registry myregistry --run-id cf11 --no-archive false
```

## <a name="next-steps"></a>後續步驟

當您準備好在雲端中自動執行容器映射組建和維護時，請參閱[ACR 工作教學課程系列](container-registry-tutorial-quick-task.md)。

您可以選擇性地安裝[適用於 Visual Studio Code 的 Docker 擴充功能](https://code.visualstudio.com/docs/azure/docker)和 [Azure 帳戶](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account)擴充功能，來搭配 Azure 容器登錄使用。 向 Azure 容器登錄提取及推送映像，或是執行 ACR 工作，都可以在 Visual Studio Code 內完成。

<!-- LINKS - External -->
[base-alpine]: https://hub.docker.com/_/alpine/
[base-dotnet]: https://hub.docker.com/r/microsoft/dotnet/
[base-node]: https://hub.docker.com/_/node/
[base-windows]: https://hub.docker.com/r/microsoft/nanoserver/
[sample-archive]: https://github.com/Azure-Samples/acr-build-helloworld-node/archive/master.zip
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

<!-- LINKS - Internal -->
[azure-cli]: /cli/azure/install-azure-cli
[az-acr-build]: /cli/azure/acr#az-acr-build
[az-acr-pack-build]: /cli/azure/acr/pack#az-acr-pack-build
[az-acr-task]: /cli/azure/acr/task
[az-acr-task-create]: /cli/azure/acr/task#az-acr-task-create
[az-login]: /cli/azure/reference-index#az-login
[az-login-service-principal]: /cli/azure/authenticate-azure-cli

<!-- IMAGES -->
[quick-build-01-fork]: ./media/container-registry-tutorial-quick-build/quick-build-01-fork.png
[quick-build-02-browser]: ./media/container-registry-tutorial-quick-build/quick-build-02-browser.png
