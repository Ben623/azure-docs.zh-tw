---
title: 預覽-在 Azure Kubernetes Service (AKS) 叢集上建立 Windows Server 容器
description: 瞭解如何快速建立 Kubernetes 叢集、使用 Azure CLI 在 Azure Kubernetes Service (AKS) 的 Windows Server 容器中部署應用程式。
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 06/17/2019
ms.author: mlearned
ms.openlocfilehash: 879e2831dc099eabe43f1eefb81b1b7373c665dc
ms.sourcegitcommit: d3dced0ff3ba8e78d003060d9dafb56763184d69
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/22/2019
ms.locfileid: "69898718"
---
# <a name="preview---create-a-windows-server-container-on-an-azure-kubernetes-service-aks-cluster-using-the-azure-cli"></a>預覽-使用 Azure CLI 在 Azure Kubernetes Service (AKS) 叢集上建立 Windows Server 容器

Azure Kubernetes Service (AKS) 是受控 Kubernetes 服務，可讓您快速部署及管理叢集。 在本文中, 您會使用 Azure CLI 來部署 AKS 叢集。 您也可以將 Windows Server 容器中的 ASP.NET 範例應用程式部署到叢集。

此功能目前為預覽狀態。

![流覽至 ASP.NET 範例應用程式的影像](media/windows-container/asp-net-sample-app.png)

本文假設您對 Kubernetes 概念有基本瞭解。 如需詳細資訊，請參閱 [Azure Kubernetes Services (AKS) 的 Kubernetes 核心概念][kubernetes-concepts]。

如果您沒有 Azure 訂用帳戶，請在開始前建立 [免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) 。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

如果您選擇在本機安裝和使用 CLI, 本文會要求您執行 Azure CLI 版2.0.61 或更新版本。 執行 `az --version` 以尋找版本。 如果您需要安裝或升級，請參閱[安裝 Azure CLI][azure-cli-install]。

## <a name="before-you-begin"></a>開始之前

建立可執行 Windows Server 容器的叢集之後, 您必須新增額外的節點集區。 稍後的步驟會涵蓋新增額外的節點集區, 但您必須先啟用一些預覽功能。

> [!IMPORTANT]
> AKS 預覽功能是自助加入宣告。 預覽會以「原樣」和「可用」的方式提供, 並從服務等級協定中排除, 並享有有限擔保。 AKS 預覽的部分是由客戶支援, 以最大的方式來涵蓋。 因此, 這些功能並不適用于生產環境使用。 如需其他資訊, 請參閱下列支援文章:
>
> * [AKS 支援原則][aks-support-policies]
> * [Azure 支援常見問題集][aks-faq]

### <a name="install-aks-preview-cli-extension"></a>安裝 aks-preview CLI 擴充功能

若要使用 Windows Server 容器, 您需要*aks-preview* CLI 擴充功能版本0.4.1 或更高版本。 使用[az extension add][az-extension-add]命令來安裝*aks-preview* Azure CLI 擴充功能, 然後使用[az extension update][az-extension-update]命令檢查是否有任何可用的更新::

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

### <a name="register-windows-preview-feature"></a>註冊 Windows preview 功能

若要建立可使用多個節點集區並執行 Windows Server 容器的 AKS 叢集, 請先在您的訂用帳戶上啟用*WindowsPreview*功能旗標。 *WindowsPreview*功能也會使用多節點集區叢集和虛擬機器擴展集來管理 Kubernetes 節點的部署和設定。 使用[az feature register][az-feature-register]命令來註冊*WindowsPreview*功能旗標, 如下列範例所示:

```azurecli-interactive
az feature register --name WindowsPreview --namespace Microsoft.ContainerService
```

> [!NOTE]
> 您在成功註冊*WindowsPreview*功能旗標之後所建立的任何 AKS 叢集都會使用此預覽叢集體驗。 若要繼續建立一般、完全支援的叢集, 請勿在生產訂用帳戶上啟用預覽功能。 使用個別的測試或開發 Azure 訂用帳戶來測試預覽功能。

註冊需要幾分鐘的時間才能完成。 使用[az feature list][az-feature-list]命令來檢查註冊狀態:

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/WindowsPreview')].{Name:name,State:properties.state}"
```

當註冊狀態為`Registered`時, 請按 ctrl-c 停止監視狀態。  然後使用[az provider register][az-provider-register]命令重新整理*microsoft.containerservice*資源提供者的註冊:

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

### <a name="limitations"></a>限制

當您建立和管理支援多個節點集區的 AKS 叢集時, 適用下列限制:

* 當您成功註冊*WindowsPreview*之後, 會有多個節點集區可用於建立的叢集。 如果您為訂用帳戶註冊*MultiAgentpoolPreview*和*VMSSPreview*功能, 也可以使用多個節點集區。 在成功註冊這些功能之前, 您無法新增或管理具有現有 AKS 叢集的節點集區。
* 您無法刪除第一個節點集區。

雖然這項功能處於預覽狀態, 但仍適用下列其他限制:

* AKS 叢集最多可以有八個節點集區。
* AKS 叢集在這八個節點集區中最多可以有400個節點。
* Windows Server 節點集區名稱的限制為6個字元。

## <a name="create-a-resource-group"></a>建立資源群組

Azure 資源群組是在其中部署與管理 Azure 資源的邏輯群組。 建立資源群組時，系統會要求您指定位置。 此位置是儲存資源群組中繼資料的位置，如果您未在資源建立期間指定另一個區域，此位置也會是您在 Azure 中執行資源的位置。 使用 [az group create][az-group-create] 命令來建立資源群組。

下列範例會在 eastus 位置建立名為 myResourceGroup 的資源群組。

> [!NOTE]
> 本文會針對本教學課程中的命令使用 Bash 語法。
> 如果您使用 Azure Cloud Shell, 請確定 [Cloud Shell] 視窗左上角的下拉式清單已設定為 [ **Bash**]。

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

下列範例輸出顯示已成功建立的資源群組：

```json
{
  "id": "/subscriptions/<guid>/resourceGroups/myResourceGroup",
  "location": "eastus",
  "managedBy": null,
  "name": "myResourceGroup",
  "properties": {
    "provisioningState": "Succeeded"
  },
  "tags": null,
  "type": null
}
```

## <a name="create-an-aks-cluster"></a>建立 AKS 叢集

若要執行支援 Windows Server 容器之節點集區的 AKS 叢集, 您的叢集必須使用使用[AZURE CNI][azure-cni-about] (advanced) 網路外掛程式的網路原則。 如需協助規劃必要子網範圍和網路考慮的詳細資訊, 請參閱[設定 AZURE CNI 網路][use-advanced-networking]功能。 使用[az aks create][az-aks-create]命令來建立名為*myAKSCluster*的 aks 叢集。 此命令將會建立所需的網路資源 (如果不存在)。
  * 叢集已設定一個節點
  * *Windows-admin-password*和*windows-admin-username*參數會針對在叢集上建立的任何 windows Server 容器, 設定系統管理員認證。

提供您自己的安全*PASSWORD_WIN* (請記住, 本文中的命令會進入 BASH shell):

```azurecli-interactive
PASSWORD_WIN="P@ssw0rd1234"

az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --node-count 1 \
    --enable-addons monitoring \
    --kubernetes-version 1.14.6 \
    --generate-ssh-keys \
    --windows-admin-password $PASSWORD_WIN \
    --windows-admin-username azureuser \
    --enable-vmss \
    --network-plugin azure
```

> [!Note]
> 如果您收到密碼驗證錯誤, 請嘗試在另一個區域中建立您的資源群組。
> 然後嘗試使用新的資源群組來建立叢集。

在幾分鐘之後，此命令就會完成，並以 JSON 格式傳回叢集的相關資訊。

## <a name="add-a-windows-server-node-pool"></a>新增 Windows Server 節點集區

根據預設, 系統會使用可執行 Linux 容器的節點集區來建立 AKS 叢集。 使用`az aks nodepool add`命令來新增可執行 Windows Server 容器的其他節點集區。

```azurecli
az aks nodepool add \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --os-type Windows \
    --name npwin \
    --node-count 1 \
    --kubernetes-version 1.14.6
```

上述命令會建立名為*npwin*的新節點集區, 並將它新增至*myAKSCluster*。 建立節點集區以執行 Windows Server 容器時, [*節點-vm-大小*] 的預設值是*Standard_D2s_v3*。 如果您選擇設定 [*節點-vm 大小*] 參數, 請檢查[受限制的 vm 大小][restricted-vm-sizes]清單。 建議的最小大小為*Standard_D2s_v3*。 上述命令也會使用執行時`az aks create`所建立之預設 vnet 中的預設子網。

## <a name="connect-to-the-cluster"></a>連接到叢集

若要管理 Kubernetes 叢集，請使用 Kubernetes 命令列用戶端：[kubectl][kubectl]。 如果您使用 Azure Cloud Shell，則 `kubectl` 已安裝。 若要在本機安裝 `kubectl`，請使用 [az aks install-cli][az-aks-install-cli] 命令：

```azurecli
az aks install-cli
```

若要設定 `kubectl` 以連線到 Kubernetes 叢集，請使用 [az aks get-credentials][az-aks-get-credentials] 命令。 此命令會下載憑證並設定 Kubernetes CLI 以供使用。

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

若要驗證叢集的連線，請使用 [kubectl get][kubectl-get] 命令來傳回叢集節點的清單。

```azurecli-interactive
kubectl get nodes
```

下列輸出範例會顯示上一個步驟中建立的單一節點。 請確定節點的狀態為 *Ready*：

```
NAME                                STATUS   ROLES   AGE    VERSION
aks-nodepool1-12345678-vmssfedcba   Ready    agent   13m    v1.14.6
aksnpwin987654                      Ready    agent   108s   v1.14.6
```

## <a name="run-the-application"></a>執行應用程式

Kubernetes 資訊清單檔會定義所需的叢集狀態，例如要執行哪些容器映像。 在本文中, 資訊清單可用來建立在 Windows Server 容器中執行 ASP.NET 範例應用程式所需的所有物件。 此資訊清單包含 ASP.NET 範例應用程式的[Kubernetes 部署][kubernetes-deployment], 以及從網際網路存取應用程式的外部[Kubernetes 服務][kubernetes-service]。

ASP.NET 範例應用程式會當做[.NET Framework 範例][dotnet-samples]的一部分提供, 並在 Windows Server 容器中執行。 AKS 需要 Windows Server 容器以*Windows server 2019*或更新版本的映射為基礎。 Kubernetes 資訊清單檔案也必須定義[節點選取器][node-selector], 以指示您的 AKS 叢集在可執行 Windows Server 容器的節點上執行 ASP.NET 範例應用程式的 pod。

建立名為 `sample.yaml` 的檔案，然後將下列 YAML 定義複製進來。 如果您使用 Azure Cloud Shell，可以使用 `vi` 或 `nano` 建立這個檔案，猶如使用虛擬或實體系統：

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: sample
  labels:
    app: sample
spec:
  replicas: 1
  template:
    metadata:
      name: sample
      labels:
        app: sample
    spec:
      nodeSelector:
        "beta.kubernetes.io/os": windows
      containers:
      - name: sample
        image: mcr.microsoft.com/dotnet/framework/samples:aspnetapp
        resources:
          limits:
            cpu: 1
            memory: 800M
          requests:
            cpu: .1
            memory: 300M
        ports:
          - containerPort: 80
  selector:
    matchLabels:
      app: sample
---
apiVersion: v1
kind: Service
metadata:
  name: sample
spec:
  type: LoadBalancer
  ports:
  - protocol: TCP
    port: 80
  selector:
    app: sample
```

使用 [kubectl apply][kubectl-apply] 命令來部署應用程式並指定 YAML 資訊清單的名稱：

```azurecli-interactive
kubectl apply -f sample.yaml
```

下列範例輸出顯示已成功建立部署和服務:

```
deployment.apps/sample created
service/sample created
```

## <a name="test-the-application"></a>測試應用程式

執行應用程式時，Kubernetes 服務會向網際網路公開前端應用程式。 此程序需要數分鐘的時間完成。

若要監視進度，請使用 [kubectl get service][kubectl-get] 命令搭配 `--watch` 引數。

```azurecli-interactive
kubectl get service sample --watch
```

一開始,*範例*服務的*外部 IP*會顯示為 [*擱置*中]。

```
NAME               TYPE           CLUSTER-IP   EXTERNAL-IP   PORT(S)        AGE
sample             LoadBalancer   10.0.37.27   <pending>     80:30572/TCP   6s
```

當 *EXTERNAL-IP* 位址從 *pending* 變成實際的公用 IP 位址時，請使用 `CTRL-C` 停止 `kubectl` 監看式流程。 下列範例輸出會顯示已指派給服務的有效公用 IP 位址：

```
sample  LoadBalancer   10.0.37.27   52.179.23.131   80:30572/TCP   2m
```

若要查看作用中的範例應用程式, 請將網頁瀏覽器開啟至服務的外部 IP 位址。

![流覽至 ASP.NET 範例應用程式的影像](media/windows-container/asp-net-sample-app.png)

## <a name="delete-cluster"></a>刪除叢集

若不再需要叢集，可使用 [az group delete][az-group-delete] 命令來移除資源群組、容器服務和所有相關資源。

```azurecli-interactive
az group delete --name myResourceGroup --yes --no-wait
```

> [!NOTE]
> 當您刪除叢集時，不會移除 AKS 叢集所使用的 Azure Active Directory 服務主體。 如需有關如何移除服務主體的步驟，請參閱 [AKS 服務主體的考量和刪除][sp-delete]。

## <a name="next-steps"></a>後續步驟

在本文中, 您已部署 Kubernetes 叢集, 並將 Windows Server 容器中的 ASP.NET 範例應用程式部署至該叢集。 存取您剛建立之叢集的[Kubernetes web 儀表板][kubernetes-dashboard]。

若要深入了解 AKS，並逐步完成部署範例的完整程式碼，請繼續 Kubernetes 叢集教學課程。

> [!div class="nextstepaction"]
> [AKS 教學課程][aks-tutorial]

<!-- LINKS - external -->
[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[node-selector]: https://kubernetes.io/docs/concepts/configuration/assign-pod-node/
[dotnet-samples]: https://hub.docker.com/_/microsoft-dotnet-framework-samples/
[azure-cni]: https://github.com/Azure/azure-container-networking/blob/master/docs/cni.md

<!-- LINKS - internal -->
[kubernetes-concepts]: concepts-clusters-workloads.md
[aks-monitor]: https://aka.ms/coingfonboarding
[aks-tutorial]: ./tutorial-kubernetes-prepare-app.md
[az-aks-browse]: /cli/azure/aks?view=azure-cli-latest#az-aks-browse
[az-aks-create]: /cli/azure/aks?view=azure-cli-latest#az-aks-create
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[az-aks-install-cli]: /cli/azure/aks?view=azure-cli-latest#az-aks-install-cli
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-group-create]: /cli/azure/group#az-group-create
[az-group-delete]: /cli/azure/group#az-group-delete
[az-provider-register]: /cli/azure/provider#az-provider-register
[azure-cli-install]: /cli/azure/install-azure-cli
[azure-cni-about]: concepts-network.md#azure-cni-advanced-networking
[sp-delete]: kubernetes-service-principal.md#additional-considerations
[azure-portal]: https://portal.azure.com
[kubernetes-deployment]: concepts-clusters-workloads.md#deployments-and-yaml-manifests
[kubernetes-service]: concepts-network.md#services
[kubernetes-dashboard]: kubernetes-dashboard.md
[restricted-vm-sizes]: quotas-skus-regions.md#restricted-vm-sizes
[use-advanced-networking]: configure-advanced-networking.md
[aks-support-policies]: support-policies.md
[aks-faq]: faq.md
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
