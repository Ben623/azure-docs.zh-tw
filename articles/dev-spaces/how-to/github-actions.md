---
title: GitHub 動作 & Azure Kubernetes Service
titleSuffix: Azure Dev Spaces
author: zr-msft
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.author: zarhoads
ms.date: 10/24/2019
ms.topic: conceptual
description: 使用 GitHub 動作和 Azure Dev Spaces，直接在 Azure Kubernetes Service 中檢查並測試提取要求的變更。
keywords: Docker，Kubernetes，Azure，AKS，Azure Kubernetes Service，容器，GitHub 動作，Helm，服務網格，服務網格路由，kubectl，k8s
manager: gwallace
ms.openlocfilehash: 2638fe2cd12407e43d3b3b698cdfca5231362db4
ms.sourcegitcommit: f7f70c9bd6c2253860e346245d6e2d8a85e8a91b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/30/2019
ms.locfileid: "73065934"
---
# <a name="github-actions--azure-kubernetes-service-preview"></a>GitHub 動作 & Azure Kubernetes Service （預覽）

Azure Dev Spaces 提供使用 GitHub 動作的工作流程，可讓您在提取要求合併至存放庫的主要分支之前，直接在 AKS 中測試提取要求的變更。 擁有執行中的應用程式來審查提取要求的變更，可以提高開發人員和小組成員的信心。 這個執行中的應用程式也可以協助小組成員（例如，產品經理和設計人員）在開發初期階段成為審核程式的一部分。

在本指南中，您將了解如何：

- 在 Azure 中於受控 Kubernetes 叢集上設定 Azure Dev Spaces。
- 將具有多個微服務的大型應用程式部署至開發人員空間。
- 使用 GitHub 動作來設定 CI/CD。
- 在獨立的開發人員空間中以完整應用程式的內容測試單一微服務。

> [!IMPORTANT]
> 此功能目前為預覽狀態。 若您同意[補充的使用規定](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)即可取得預覽。 在公開上市 (GA) 之前，此功能的某些領域可能會變更。

## <a name="prerequisites"></a>必要條件

- Azure 訂用帳戶。 如果您沒有 Azure 訂用帳戶，您可以建立[免費帳戶](https://azure.microsoft.com/free)。
- [已安裝 Azure CLI][azure-cli-installed]。
- [已安裝 Helm 2.13 或更新版本][helm-installed] \(英文\)。
- [已啟用 Github 動作][github-actions-beta-signup]的 github 帳戶。

## <a name="create-an-azure-kubernetes-service-cluster"></a>建立 Azure Kubernetes Service 叢集

您必須在[支援的區域][supported-regions]中建立 AKS 叢集。 下列命令會建立名為 MyResourceGroup 的資源群組與名為 MyAKS 的 AKS 叢集。

```cmd
az group create --name MyResourceGroup --location eastus
az aks create -g MyResourceGroup -n MyAKS --location eastus --disable-rbac --generate-ssh-keys
```

## <a name="enable-azure-dev-spaces-on-your-aks-cluster"></a>在 AKS 叢集上啟用 Azure Dev Spaces

使用 `use-dev-spaces` 命令在 AKS 叢集上啟用 Dev Spaces，並遵循提示來進行。 下列命令會在 *MyResourceGroup* 群組中的 *MyAKS* 叢集上啟用 Dev Spaces，並建立稱為 *dev* 的開發空間。

> [!NOTE]
> `use-dev-spaces` 命令也會安裝 Azure Dev Spaces CLI (如果尚未安裝)。 您無法在 Azure Cloud Shell 中安裝 Azure Dev Spaces CLI。

```cmd
az aks use-dev-spaces -g MyResourceGroup -n MyAKS --space dev --yes
```

## <a name="create-an-azure-container-registry"></a>建立 Azure Container Registry

建立 Azure Container Registry （ACR）：

```cmd
az acr create --resource-group MyResourceGroup --name <acrName> --sku Basic
```

> [!IMPORTANT]
> 您的 ACR 在 Azure 內必須是唯一的名稱，而且包含5-50 個英數位元。 您使用的任何字母都必須是小寫。

儲存輸出中的*loginServer*值，因為在稍後的步驟中會用到它。

## <a name="create-a-service-principal-for-authentication"></a>建立用於驗證的服務主體

使用[az ad sp create for-rbac][az-ad-sp-create-for-rbac]來建立服務主體。 例如：

```cmd
az ad sp create-for-rbac --sdk-auth --skip-assignment
```

儲存 JSON 輸出，因為在稍後的步驟中會用到它。


使用[az aks show][az-aks-show]顯示 aks 叢集的*識別碼*：

```cmd
az aks show -g MyResourceGroup -n MyAKS  --query id
```

使用[az acr show][az-acr-show]顯示 acr 的*識別碼*：

```cmd
az acr show --name <acrName> --query id
```

使用[az role 指派 create][az-role-assignment-create]來授與 AKS 叢集的*參與者*存取權，以及*AcrPush*對您 ACR 的存取權。

```cmd
az role assignment create --assignee <ClientId> --scope <AKSId> --role Contributor
az role assignment create --assignee <ClientId>  --scope <ACRId> --role AcrPush
```

> [!IMPORTANT]
> 您必須是 AKS 叢集和 ACR 的擁有者，才能讓您的服務主體存取這些資源。

## <a name="get-sample-application-code"></a>取得應用程式範例的程式碼

在本文中，您會使用[Azure Dev Spaces 自行車分享範例應用程式][bike-sharing-gh]，示範如何使用 Azure Dev Spaces 搭配 GitHub 動作。

將 Azure Dev Spaces 範例存放庫派生，然後流覽至您的分支存放庫。 按一下 [*動作*] 索引標籤，然後選擇啟用此存放庫的動作。

複製您的分支存放庫，並流覽至其目錄：

```cmd
git clone https://github.com/USERNAME/dev-spaces
cd dev-spaces/samples/BikeSharingApp/
```

## <a name="retrieve-the-hostsuffix-for-dev"></a>擷取適用於 *dev* 的 HostSuffix

使用 `azds show-context` 命令來顯示適用於 *dev* 的 HostSuffix。

```cmd
$ azds show-context

Name                ResourceGroup     DevSpace  HostSuffix
------------------  ----------------  --------  -----------------------
MyAKS               MyResourceGroup   dev       fedcab0987.eus.azds.io
```

## <a name="update-the-helm-chart-with-your-hostsuffix"></a>搭配您的 HostSuffix 更新 Helm 圖表

開啟 [charts/values.yaml][bike-sharing-values-yaml] \(英文\) 並將所有 `<REPLACE_ME_WITH_HOST_SUFFIX>` 項目取代為您先前所擷取的 HostSuffix 值。 儲存變更並關閉該檔案。

## <a name="run-the-sample-application-in-kubernetes"></a>在 Kubernetes 中執行範例應用程式

在 Kubernetes 執行範例應用程式的命令為現有處理程序的一部分，且不具任何與 Azure Dev Spaces 工具的相依性。 在此情況下，Helm 是用來執行此範例應用程式的工具，但您可以使用其他工具來在叢集內的命名空間中執行您的整個應用程式。 Helm 命令是以您先前所建立的 *dev* 開發空間作為目標，但此開發空間也是 Kubernetes 命名空間。 因此，開發空間和其他命名空間相同，可以被其他工具作為目標。

在應用程式于叢集內執行之後，您可以使用 Azure Dev Spaces 進行開發，而不論用來部署它的工具為何。

使用 `helm init` 和 `helm install` 命令來在叢集上設定並安裝範例應用程式。

```cmd
cd charts/
helm init --wait
helm install -n bikesharing . --dep-up --namespace dev --atomic
```

> [!Note]
> **如果您使用已啟用 RBAC 的叢集**，請務必設定 [Tiller 的服務帳戶][tiller-rbac]。 否則，`helm` 命令將會失敗。

`helm install` 命令可能需要幾分鐘的時間才能完成。 命令的輸出會顯示其於完成時部署至叢集之所有伺服器的狀態：

```cmd
$ cd charts/
$ helm init --wait
...
Happy Helming!

$ helm install -n bikesharing . --dep-up --namespace dev --atomic

Hang tight while we grab the latest from your chart repositories...
...
NAME               READY  UP-TO-DATE  AVAILABLE  AGE
bikes              1/1    1           1          4m32s
bikesharingweb     1/1    1           1          4m32s
billing            1/1    1           1          4m32s
gateway            1/1    1           1          4m32s
reservation        1/1    1           1          4m32s
reservationengine  1/1    1           1          4m32s
users              1/1    1           1          4m32s
```

在範例應用程式已安裝至叢集上，且基於您已在叢集上啟用 Dev Spaces 的原因，請使用 `azds list-uris` 命令來顯示目前所選取 *dev* 中之範例應用程式的 URL。

```cmd
$ azds list-uris
Uri                                                 Status
--------------------------------------------------  ---------
http://dev.bikesharingweb.fedcab0987.eus.azds.io/  Available
http://dev.gateway.fedcab0987.eus.azds.io/         Available
```

開啟來自 `azds list-uris` 命令的公用 URL，來瀏覽至 *bikesharingweb* 服務。 在上述範例中，*bikesharingweb* 服務的公用 URL 是 `http://dev.bikesharingweb.fedcab0987.eus.azds.io/`。 選取 [ *Aurelia Briggs （customer）* ] 作為使用者，然後選取要出租的自行車。 確認您看到自行車的預留位置影像。

## <a name="configure-your-github-action"></a>設定您的 GitHub 動作

流覽至您的分支存放庫，然後按一下 [*設定*]。 按一下左側邊欄中的 [*秘密*]。 按一下 [新增*新密碼*]，在下方新增每個新密碼：

1. *AZURE_CREDENTIALS*：從服務主體建立的整個輸出。
1. *RESOURCE_GROUP*： AKS 叢集的資源群組，在此範例中為*MyResourceGroup*。
1. *CLUSTER_NAME*： AKS 叢集的名稱，在此範例中為*MyAKS*。
1. *CONTAINER_REGISTRY*： ACR 的*loginServer* 。
1. *主機*：您的開發人員空間的主機，其採用 *< MASTER_SPACE > 的格式。 < APP_NAME >。 < HOST_SUFFIX >* ，在此範例中為*dev.bikesharingweb.fedcab0987.eus.azds.io*。
1. *IMAGE_PULL_SECRET*：您想要使用的秘密名稱，例如「*示範秘密*」。
1. *MASTER_SPACE*：您的父系開發人員空間的名稱，在此範例中為*dev*。
1. *REGISTRY_USERNAME*：從服務主體建立的 JSON 輸出中的*clientId* 。
1. *REGISTRY_PASSWORD*：從服務主體建立的 JSON 輸出*clientSecret* 。

> [!NOTE]
> 所有這些秘密都是由 GitHub 動作使用，並設定于[github/workflow/自行車. yml][github-action-yaml]中。

## <a name="create-a-new-branch-for-code-changes"></a>建立程式碼變更的新分支

流覽回到 `BikeSharingApp/` 並建立名為*自行車-images*的新分支。

```cmd
cd ..
git checkout -b bike-images
```

編輯[自行車/server .js][bikes-server-js]以移除232和233行：

```javascript
    // Hard code image url *FIX ME*
    theBike.imageUrl = "/static/logo.svg";
```

區段現在看起來應該像這樣：

```javascript
    var theBike = result;
    theBike.id = theBike._id;
    delete theBike._id;
```

儲存檔案，然後使用 `git add` 和 `git commit` 來暫存您的變更。

```cmd
git add Bikes/server.js 
git commit -m "Removing hard coded imageUrl from /bikes/:id route"
```

## <a name="push-your-changes"></a>推送您的變更

使用 `git push` 將您的新分支推送至分叉存放庫：

```cmd
git push origin bike-images
```

推播完成後，在 GitHub 上流覽至您的分支存放庫在分支存放庫中建立具有*主要*的提取要求，做為與*自行車影像*分支相較之下的基底分支。

開啟提取要求之後，流覽至 [*動作*] 索引標籤。確認已啟動新的動作，並正在建立*自行車*服務。

## <a name="view-the-child-space-with-your-changes"></a>使用您的變更來查看子空間

動作完成後，您會看到批註，其中包含以提取要求中的變更為基礎的新子空間 URL。

> [!div class="mx-imgBorder"]
> ![GitHub 動作 Url](../media/github-actions/github-action-url.png)

從批註開啟 URL，以流覽至*bikesharingweb*服務。 選取 [ *Aurelia Briggs （customer）* ] 作為使用者，然後選取要出租的自行車。 確認您不會再看到自行車的預留位置影像。

## <a name="clean-up-your-azure-resources"></a>清除 Azure 資源

```cmd
az group delete --name MyResourceGroup --yes --no-wait
```

## <a name="next-steps"></a>後續步驟

了解 Azure Dev Spaces 如何協助您跨多個容器開發更複雜的應用程式，以及如何藉由在不同的空間中使用不同的程式碼版本或分支，來簡化共同開發。

> [!div class="nextstepaction"]
> [在 Azure Dev Spaces 中進行小組開發][team-quickstart]

[azure-cli-installed]: /cli/azure/install-azure-cli?view=azure-cli-latest
[az-ad-sp-create-for-rbac]: /cli/azure/ad/sp#az-ad-sp-create-for-rbac
[az-acr-show]: /cli/azure/acr#az-acr-show
[az-aks-show]: /cli/azure/aks?view=azure-cli-latest#az-aks-show
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
[bikes-server-js]: https://github.com/Azure/dev-spaces/blob/master/samples/BikeSharingApp/Bikes/server.js#L232-L233
[bike-sharing-gh]: https://github.com/Azure/dev-spaces/
[bike-sharing-values-yaml]: https://github.com/Azure/dev-spaces/blob/master/samples/BikeSharingApp/charts/values.yaml
[github-actions-beta-signup]: https://github.com/features/actions
[github-action-yaml]: https://github.com/Azure/dev-spaces/blob/master/.github/workflows/bikes.yml
[github-action-bikesharing-yaml]: https://github.com/Azure/dev-spaces/blob/master/.github/workflows/bikesharing.yml
[helm-installed]: https://helm.sh/docs/using_helm/#installing-helm
[tiller-rbac]: https://helm.sh/docs/using_helm/#role-based-access-control
[supported-regions]: ../about.md#supported-regions-and-configurations
[sp-acr]: ../../container-registry/container-registry-auth-service-principal.md
[sp-aks]: ../../aks/kubernetes-service-principal.md
[team-quickstart]: ../quickstart-team-development.md