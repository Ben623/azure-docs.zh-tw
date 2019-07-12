---
title: 在 Azure Kubernetes Service (AKS) 中使用網路原則來保護 Pod
description: 了解如何保護 Azure Kubernetes Service (AKS) 中使用 Kubernetes 網路原則流入和流出 pod 流動的流量
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 05/06/2019
ms.author: mlearned
ms.openlocfilehash: c9bf2c2c459999813c7fc30f95be653168d270ad
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/07/2019
ms.locfileid: "67613959"
---
# <a name="secure-traffic-between-pods-using-network-policies-in-azure-kubernetes-service-aks"></a>在 Azure Kubernetes Service (AKS) 中使用網路原則來保護 Pod 之間的流量

當您在 Kubernetes 中執行新式微服務架構的應用程式時，您通常想要控制哪些元件可以彼此通訊。 最低權限原則應該套用至流量可以在 Azure Kubernetes Service (AKS) 叢集中的 pod 之間流動的方式。 例如，假設您可能想要封鎖直接流向後端應用程式。 *網路原則*在 Kubernetes 中的功能可讓您定義規則，以便在叢集中的 pod 之間的輸入和輸出流量。

這篇文章會示範如何安裝網路原則引擎，並建立 Kubernetes 網路原則來控制在 AKS 中的 pod 之間的流量流程。 網路原則應該只用於以 Linux 為基礎的節點和 AKS 中的 pod。

## <a name="before-you-begin"></a>開始之前

您需要 Azure CLI 2.0.61 版或更新版本安裝並設定。 執行  `az --version` 以尋找版本。 如果您需要安裝或升級，請參閱 [安裝 Azure CLI][install-azure-cli]。

> [!TIP]
> 如果您使用的網路原則功能在預覽期間，我們建議您[建立新的叢集](#create-an-aks-cluster-and-enable-network-policy)。
> 
> 如果您想要繼續使用現有的測試叢集使用網路原則，在預覽期間，將叢集升級到新的 Kubernetes 版本 「 最新的 GA 版本，然後將下列 YAML 資訊清單，以修正損毀的計量伺服器和 Kubernetes 部署儀表板。 此修正程式，才需要使用 Calico 網路原則引擎的叢集。
>
> 安全性最佳作法[檢閱此 YAML 資訊清單的內容][calico-aks-cleanup]若要了解什麼部署到 AKS 叢集。
>
> `kubectl delete -f https://raw.githubusercontent.com/Azure/aks-engine/master/docs/topics/calico-3.3.1-cleanup-after-upgrade.yaml`

## <a name="overview-of-network-policy"></a>網路原則概觀

在 AKS 叢集中的所有 pod 可以傳送和接收流量而不會限制，預設值。 為了提升安全性，您可以定義可控制流量流程的規則。 後端應用程式通常才會顯示必要的前端服務，例如。 或者，資料庫元件才能夠連接到這些應用程式層。

網路原則是 Kubernetes 規格會定義 Pod 之間進行通訊的存取原則。 使用網路原則，您會定義規則來傳送和接收流量，並將它們套用至的 pod 符合一或多個標籤選取器集合的已排序的集合。

這些網路原則規則會定義為 YAML 資訊清單。 網路原則可以包含更多的資訊清單，也會建立部署或服務的過程。

### <a name="network-policy-options-in-aks"></a>在 AKS 中的網路原則選項

Azure 提供兩種方式來實作網路原則。 當您建立 AKS 叢集時，您可以選擇網路原則選項。 在建立叢集之後，就無法變更原則選項：

* Azure 本身的實作，稱為*Azure 網路原則*。
* *Calico 網路原則*，開放原始碼網路和網路安全性解決方案，由[Tigera][tigera]。

這兩個實作會使用 Linux *iptables 相關自定義*來強制執行指定的原則。 原則會轉譯成允許和不允許的 IP 組之集合。 這些組再設計方式為 IPTable 篩選規則。

網路原則僅適用於 Azure CNI （進階） 選項。 實作是不同的兩個選項：

* *Azure 的網路原則*-Azure CNI 設定 VM 主機的內部節點的網路中的橋接器。 封包通過橋接器時，會套用篩選規則。
* *Calico 網路原則*-Azure CNI 設定局部核心之內部節點流量路由。 Pod 的網路介面上所套用的原則。

### <a name="differences-between-azure-and-calico-policies-and-their-capabilities"></a>Azure 和 Calico 原則和其功能之間的差異

| 功能                               | Azure                      | Calico                      |
|------------------------------------------|----------------------------|-----------------------------|
| 支援的平台                      | Linux                      | Linux                       |
| 支援網路功能選項             | Azure CNI                  | Azure CNI                   |
| Kubernetes 規格的合規性 | 支援的所有原則類型 |  支援的所有原則類型 |
| 其他功能                      | 無                       | 擴充原則模型，其中包含全域網路原則、 全域網路設定，以及主應用程式端點。 如需有關使用`calicoctl`CLI 來管理這些擴充功能，請參閱[calicoctl 使用者參考][calicoctl]。 |
| 支援                                  | Azure 支援和工程小組支援 | Calico 社群支援。 如需有關其他的付費支援的詳細資訊，請參閱[專案 Calico 支援選項][calico-support]。 |
| 記錄                                  | 規則已新增 / 刪除 iptables 相關自定義中會記錄下每個主機上 */var/log/azure-npm.log* | 如需詳細資訊，請參閱[Calico 元件記錄檔][calico-logs] |

## <a name="create-an-aks-cluster-and-enable-network-policy"></a>建立 AKS 叢集並啟用網路原則

若要讓我們查看動作中的網路原則建立，然後再展開定義流量的原則：

* 拒絕流向 Pod 的所有流量。
* 允許以 Pod 標籤為基礎的流量。
* 允許以命名空間為基礎的流量。

首先，讓我們建立的 AKS 叢集，支援網路原則。 只有在叢集建立時，才可以啟用網路原則功能。 您無法在現有的 AKS 叢集上啟用網路原則。

若要使用網路原則與 AKS 叢集，您必須使用[Azure CNI 外掛程式][azure-cni] and define your own virtual network and subnets. For more detailed information on how to plan out the required subnet ranges, see [configure advanced networking][use-advanced-networking]。

下列範例指令碼：

* 建立虛擬網路和子網路。
* 建立 Azure Active Directory (Azure AD) 使用的服務主體與 AKS 叢集。
* 針對虛擬網路上的 AKS 叢集服務主體指派「參與者」  權限。
* 在定義的虛擬網路中建立 AKS 叢集，並讓網路原則。
    * *Azure*使用網路原則選項。 若要改為使用 Calico 做為網路原則選項中，使用`--network-policy calico`參數。

提供您自己的安全 *SP_PASSWORD*。 您可以取代*RESOURCE_GROUP_NAME*並*CLUSTER_NAME*變數：

```azurecli-interactive
SP_PASSWORD=mySecurePassword
RESOURCE_GROUP_NAME=myResourceGroup-NP
CLUSTER_NAME=myAKSCluster
LOCATION=canadaeast

# Create a resource group
az group create --name $RESOURCE_GROUP_NAME --location $LOCATION

# Create a virtual network and subnet
az network vnet create \
    --resource-group $RESOURCE_GROUP_NAME \
    --name myVnet \
    --address-prefixes 10.0.0.0/8 \
    --subnet-name myAKSSubnet \
    --subnet-prefix 10.240.0.0/16

# Create a service principal and read in the application ID
SP_ID=$(az ad sp create-for-rbac --password $SP_PASSWORD --skip-assignment --query [appId] -o tsv)

# Wait 15 seconds to make sure that service principal has propagated
echo "Waiting for service principal to propagate..."
sleep 15

# Get the virtual network resource ID
VNET_ID=$(az network vnet show --resource-group $RESOURCE_GROUP_NAME --name myVnet --query id -o tsv)

# Assign the service principal Contributor permissions to the virtual network resource
az role assignment create --assignee $SP_ID --scope $VNET_ID --role Contributor

# Get the virtual network subnet resource ID
SUBNET_ID=$(az network vnet subnet show --resource-group $RESOURCE_GROUP_NAME --vnet-name myVnet --name myAKSSubnet --query id -o tsv)

# Create the AKS cluster and specify the virtual network and service principal information
# Enable network policy by using the `--network-policy` parameter
az aks create \
    --resource-group $RESOURCE_GROUP_NAME \
    --name $CLUSTER_NAME \
    --node-count 1 \
    --generate-ssh-keys \
    --network-plugin azure \
    --service-cidr 10.0.0.0/16 \
    --dns-service-ip 10.0.0.10 \
    --docker-bridge-address 172.17.0.1/16 \
    --vnet-subnet-id $SUBNET_ID \
    --service-principal $SP_ID \
    --client-secret $SP_PASSWORD \
    --network-policy azure
```

建立叢集需要幾分鐘的時間。 備妥叢集時，設定`kubectl`若要使用連線到 Kubernetes 叢集[az aks get-credentials 來取得認證][az-aks-get-credentials]命令。 此命令會下載憑證並設定 Kubernetes CLI 以供使用：

```azurecli-interactive
az aks get-credentials --resource-group $RESOURCE_GROUP_NAME --name $CLUSTER_NAME
```

## <a name="deny-all-inbound-traffic-to-a-pod"></a>拒絕流向 Pod 的所有輸入流量

在您定義規則以允許特定網路流量之前，先建立網路原則以拒絕所有流量。 此原則可讓您只想要的流量開始白名單的起點。 您可以也清楚地看到套用網路原則時會捨棄該流量。

在範例應用程式環境和流量規則，讓我們先建立命名空間稱為*開發*執行範例 pod:

```console
kubectl create namespace development
kubectl label namespace/development purpose=development
```

建立執行 NGINX 範例後端 pod。 這個後端 pod 可以用於模擬的範例後端 web 應用程式。 在 *development* 命名空間中建立這個 Pod，然後開啟連接埠 *80* 來處理 Web 流量。 替 Pod 加上 *app=webapp,role=backend* 標籤，以便我們在下一節中使用網路原則以其作為目標：

```console
kubectl run backend --image=nginx --labels app=webapp,role=backend --namespace development --expose --port 80 --generator=run-pod/v1
```

建立另一個 pod，並附加到測試，您可以成功連線到預設 NGINX 網頁的終端機工作階段：

```console
kubectl run --rm -it --image=alpine network-policy --namespace development --generator=run-pod/v1
```

在殼層提示字元中，使用`wget`以確認您是否可以存取預設 NGINX 網頁：

```console
wget -qO- http://backend
```

下列範例輸出會顯示預設 NGINX 網頁中傳回：

```
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
[...]
```

結束連結的終端機工作階段。 測試 pod 會自動刪除。

```console
exit
```

### <a name="create-and-apply-a-network-policy"></a>建立並套用網路原則

既然您已確認您可以使用基本的 NGINX 網頁上的範例後端 pod，請建立網路原則以拒絕所有流量。 建立名為 `backend-policy.yaml` 的檔案，並貼上下列 YAML 資訊清單。 使用此資訊清單*podSelector*若要將原則附加至有的 pod *app:webapp，角色： 後端*標籤，例如範例 NGINX pod。 *ingress* 之下未定義任何規則，因此會拒絕流向 Pod 的所有輸入流量：

```yaml
kind: NetworkPolicy
apiVersion: networking.k8s.io/v1
metadata:
  name: backend-policy
  namespace: development
spec:
  podSelector:
    matchLabels:
      app: webapp
      role: backend
  ingress: []
```

使用網路原則套用所[kubectl 套用][kubectl-apply]命令並指定您的 YAML 資訊清單的名稱：

```azurecli-interactive
kubectl apply -f backend-policy.yaml
```

### <a name="test-the-network-policy"></a>測試網路原則


您可以使用後端 pod 上的 NGINX 網頁一次，讓我們來看。 建立另一個測試 Pod 並連結終端機工作階段：

```console
kubectl run --rm -it --image=alpine network-policy --namespace development --generator=run-pod/v1
```

在殼層提示字元中，使用`wget`如果您可以存取預設 NGINX 網頁。 此時，將逾時值設定為 2  秒。 網路原則現在封鎖所有輸入的流量，因此無法載入此頁面，如下列範例所示：

```console
$ wget -qO- --timeout=2 http://backend

wget: download timed out
```

結束連結的終端機工作階段。 測試 pod 會自動刪除。

```console
exit
```

## <a name="allow-inbound-traffic-based-on-a-pod-label"></a>允許以 Pod 標籤為基礎的輸入流量

在上一節中，已排程的後端 NGINX pod，並建立網路原則，以拒絕所有流量。 讓我們建立前端的 pod，並更新網路原則，以允許流量從前端的 pod。

更新網路原則，以允許來自任何命名空間中具有 *app:webapp,role:frontend* 標籤的 Pod 流量。 編輯先前*後端 policy.yaml*檔案，並新增*matchLabels*輸入規則，因此您的資訊清單看起來如下列範例所示：

```yaml
kind: NetworkPolicy
apiVersion: networking.k8s.io/v1
metadata:
  name: backend-policy
  namespace: development
spec:
  podSelector:
    matchLabels:
      app: webapp
      role: backend
  ingress:
  - from:
    - namespaceSelector: {}
      podSelector:
        matchLabels:
          app: webapp
          role: frontend
```

> [!NOTE]
> 此網路原則針對輸入規則使用 *namespaceSelector* 和 *podSelector* 元素。 YAML 語法，請務必輸入規則是加總。 在此範例中，兩個元素都必須與要套用的輸入規則相符。 Kubernetes 版本早於*1.12*未正確解譯這些項目及限制的網路流量，如您所預期。 如需有關此行為的詳細資訊，請參閱[行為的選取器來回][policy-rules]。

使用更新的網路原則套用所[kubectl 套用][kubectl-apply]命令並指定您的 YAML 資訊清單的名稱：

```azurecli-interactive
kubectl apply -f backend-policy.yaml
```

排程會標示為 pod*應用程式 = webapp，角色 = frontend*並附加終端機工作階段：

```console
kubectl run --rm -it frontend --image=alpine --labels app=webapp,role=frontend --namespace development --generator=run-pod/v1
```

在殼層提示字元中，使用`wget`如果您可以存取預設 NGINX 網頁：

```console
wget -qO- http://backend
```

因為輸入規則允許流量： 其標籤的 pod*應用程式： webapp，角色： 前端*，允許流量從前端的 pod。 下列範例輸出顯示預設 NGINX 網頁傳回：

```
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
[...]
```

結束連結的終端機工作階段。 Pod 會自動刪除。

```console
exit
```

### <a name="test-a-pod-without-a-matching-label"></a>測試沒有相符標籤的 Pod

網路原則允許來自標籤為 *app: webapp,role: frontend* 的 Pod 流量，但應該拒絕所有其他流量。 讓我們測試看看另一個 pod，而不需要這些標籤是否可存取後端 NGINX pod。 建立另一個測試 Pod 並連結終端機工作階段：

```console
kubectl run --rm -it --image=alpine network-policy --namespace development --generator=run-pod/v1
```

在殼層提示字元中，使用`wget`如果您可以存取預設 NGINX 網頁。 網路原則會封鎖輸入的流量，因此無法載入此頁面，如下列範例所示：

```console
$ wget -qO- --timeout=2 http://backend

wget: download timed out
```

結束連結的終端機工作階段。 測試 pod 會自動刪除。

```console
exit
```

## <a name="allow-traffic-only-from-within-a-defined-namespace"></a>只允許來自已定義命名空間的流量

在上述範例中，您可以建立的網路原則，拒絕所有流量，並稍後更新原則，以允許流量從 pod 特定標籤。 另一個常見的需求是限制流量只在指定的命名空間內。 如果先前的範例中的流量*開發*命名空間中，建立網路原則會防止流量從另一個命名空間，例如*生產*，使其無法到達的 pod。

首先，建立新的命名空間，以模擬生產命名空間：

```console
kubectl create namespace production
kubectl label namespace/production purpose=production
```

在標籤為 *app=webapp,role=frontend* 的 *production* 命名空間中排程測試 Pod。 連結終端機工作階段：

```console
kubectl run --rm -it frontend --image=alpine --labels app=webapp,role=frontend --namespace production --generator=run-pod/v1
```

在殼層提示字元中，使用`wget`以確認您是否可以存取預設 NGINX 網頁：

```console
wget -qO- http://backend.development
```

因為 pod 的標籤符合目前所允許的網路原則中，允許流量。 網路原則不會查看命名空間，只會查看 Pod 標籤。 下列範例輸出顯示預設 NGINX 網頁傳回：

```
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
[...]
```

結束連結的終端機工作階段。 測試 pod 會自動刪除。

```console
exit
```

### <a name="update-the-network-policy"></a>更新網路原則

讓我們更新 輸入規則*namespaceSelector*一節，以只允許流量從*開發*命名空間。 編輯 *backend-policy.yaml* 資訊清單檔，如下列範例所示：

```yaml
kind: NetworkPolicy
apiVersion: networking.k8s.io/v1
metadata:
  name: backend-policy
  namespace: development
spec:
  podSelector:
    matchLabels:
      app: webapp
      role: backend
  ingress:
  - from:
    - namespaceSelector:
        matchLabels:
          purpose: development
      podSelector:
        matchLabels:
          app: webapp
          role: frontend
```

在更複雜的範例中，您可以像定義多個輸入規則， *namespaceSelector* ，然後*podSelector*。

使用更新的網路原則套用所[kubectl 套用][kubectl-apply]命令並指定您的 YAML 資訊清單的名稱：

```azurecli-interactive
kubectl apply -f backend-policy.yaml
```

### <a name="test-the-updated-network-policy"></a>測試已更新的網路原則

排程中的其他 pod*生產*命名空間，並將附加的終端機工作階段：

```console
kubectl run --rm -it frontend --image=alpine --labels app=webapp,role=frontend --namespace production --generator=run-pod/v1
```

在殼層提示字元中，使用`wget`以查看 網路原則會立即拒絕的流量：

```console
$ wget -qO- --timeout=2 http://backend.development

wget: download timed out
```

結束測試 Pod：

```console
exit
```

拒絕的流量*生產*命名空間、 排程的測試 pod 回到*開發*命名空間，並將附加的終端機工作階段：

```console
kubectl run --rm -it frontend --image=alpine --labels app=webapp,role=frontend --namespace development --generator=run-pod/v1
```

在殼層提示字元中，使用`wget`以查看 網路原則允許的流量：

```console
wget -qO- http://backend
```

允許流量，因為 pod 會排定在命名空間，相符項目所允許的網路原則中。 下列範例輸出顯示預設 NGINX 網頁傳回：

```
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
[...]
```

結束連結的終端機工作階段。 測試 pod 會自動刪除。

```console
exit
```

## <a name="clean-up-resources"></a>清除資源

在本文中，我們會建立兩個命名空間，並套用的網路原則。 若要清除這些資源，請使用[kubectl 刪除][kubectl-delete]命令並指定資源名稱：

```console
kubectl delete namespace production
kubectl delete namespace development
```

## <a name="next-steps"></a>後續步驟

如需有關網路資源的詳細資訊，請參閱[網路的 Azure Kubernetes Service (AKS) 中的應用程式概念][concepts-network]。

若要深入了解原則，請參閱[Kubernetes 網路原則][kubernetes-network-policies]。

<!-- LINKS - external -->
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-delete]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#delete
[kubernetes-network-policies]: https://kubernetes.io/docs/concepts/services-networking/network-policies/
[azure-cni]: https://github.com/Azure/azure-container-networking/blob/master/docs/cni.md
[policy-rules]: https://kubernetes.io/docs/concepts/services-networking/network-policies/#behavior-of-to-and-from-selectors
[aks-github]: https://github.com/azure/aks/issues
[tigera]: https://www.tigera.io/
[calicoctl]: https://docs.projectcalico.org/v3.6/reference/calicoctl/
[calico-support]: https://www.projectcalico.org/support
[calico-logs]: https://docs.projectcalico.org/v3.6/maintenance/component-logs
[calico-aks-cleanup]: https://github.com/Azure/aks-engine/blob/master/docs/topics/calico-3.3.1-cleanup-after-upgrade.yaml

<!-- LINKS - internal -->
[install-azure-cli]: /cli/azure/install-azure-cli
[use-advanced-networking]: configure-advanced-networking.md
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[concepts-network]: concepts-network.md
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-provider-register]: /cli/azure/provider#az-provider-register
