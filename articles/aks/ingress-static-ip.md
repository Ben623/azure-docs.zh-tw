---
title: 在 Azure Kubernetes Service (AKS) 中使用靜態 IP 位址建立 HTTP 輸入控制器
description: 了解如何在 Azure Kubernetes Service (AKS) 叢集中，使用靜態公用 IP 位址來安裝及設定 NGINX 輸入控制器。
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 05/24/2019
ms.author: mlearned
ms.openlocfilehash: 5a4a46b8384da46a95ef148bc9989749535ec811
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/07/2019
ms.locfileid: "67615325"
---
# <a name="create-an-ingress-controller-with-a-static-public-ip-address-in-azure-kubernetes-service-aks"></a>在 Azure Kubernetes Service (AKS) 中使用靜態公用 IP 位址建立輸入控制器

輸入控制器是一項可為 Kubernetes 服務提供反向 Proxy、可設定的流量路由和 TLS 終止的軟體。 Kubernetes 輸入資源可用來設定個別 Kubernetes 服務的輸入規則和路由。 透過輸入控制器和輸入規則，您可以使用單一 IP 位址將流量路由至 Kubernetes 叢集中的多個服務。

本文說明如何部署[NGINX 輸入控制器][nginx-ingress]in an Azure Kubernetes Service (AKS) cluster. The ingress controller is configured with a static public IP address. The [cert-manager][cert-manager]專案用來自動產生和設定[let 's Encrypt][可讓加密]憑證。 最後，會有兩個應用程式在 AKS 叢集中執行，且均可透過單一 IP 位址來存取。

您也可以：

- [建立具有外部網路連線的基本輸入控制器][aks-ingress-basic]
- [啟用 HTTP 應用程式路由附加元件][aks-http-app-routing]
- [建立輸入控制器，會使用您自己的 TLS 憑證][aks-ingress-own-tls]
- [建立輸入控制器用來自動產生的動態公用 IP 位址使用的 TLS 憑證的 let 's Encrypt][aks-ingress-tls]

## <a name="before-you-begin"></a>開始之前

此文章假設您目前具有 AKS 叢集。 如果您需要 AKS 叢集，請參閱 AKS 快速入門[使用 Azure CLI][aks-quickstart-cli] or [using the Azure portal][aks-quickstart-portal]。

本文使用 Helm 來安裝 NGINX 輸入控制器、cert-manager 及範例 Web 應用程式。 您需要在 AKS 叢集內將 Helm 初始化，並使用適用於 Tiller 的服務帳戶。 請確定您使用的是 Helm 的最新版本。 升級的指示，請參閱[Helm 安裝 docs][helm-install]. For more information on configuring and using Helm, see [Install applications with Helm in Azure Kubernetes Service (AKS)][use-helm]。

本文也會要求您執行 Azure CLI 版本 2.0.64 或更新版本。 執行 `az --version` 以尋找版本。 如果您需要安裝或升級，請參閱[安裝 Azure CLI][azure-cli-install]。

## <a name="create-an-ingress-controller"></a>建立輸入控制器

根據預設，NGINX 輸入控制器會使用新的公用 IP 位址指派來建立。 此公用 IP 位址對於輸入控制器的生命週期而言只是靜態的，而且如果刪除並重新建立控制器，則會遺失。 常見的設定需求是為 NGINX 輸入控制器提供現有的靜態公用 IP 位址。 如果刪除了輸入控制器，靜態公用 IP 位址仍會保留。 這種方法可讓您在應用程式的整個生命週期中，以一致的方式使用現有的 DNS 記錄和網路設定。

如果您需要建立靜態公用 IP 位址，請先取得 AKS 叢集中的資源群組名稱[az aks 顯示][az-aks-show]命令：

```azurecli-interactive
az aks show --resource-group myResourceGroup --name myAKSCluster --query nodeResourceGroup -o tsv
```

接下來，建立具有公用 IP 位址*靜態*配置的方法使用[az 網路公用 ip 建立][az-network-public-ip-create]命令。 下列範例會在上一個步驟所取得的 AKS 叢集資源群組中，建立名為 myAKSPublicIP  的公用 IP 位址：

```azurecli-interactive
az network public-ip create --resource-group MC_myResourceGroup_myAKSCluster_eastus --name myAKSPublicIP --allocation-method static --query publicIp.ipAddress -o tsv
```

現在，使用 Helm 部署 nginx-ingress  圖表。 新增 `--set controller.service.loadBalancerIP` 參數，並指定上一個步驟中所建立的自有公用 IP 位址。 為了新增備援，您必須使用 `--set controller.replicaCount` 參數部署兩個 NGINX 輸入控制器複本。 為充分享有執行輸入控制器複本的好處，請確定 AKS 叢集中有多個節點。

輸入控制器也需要將它排程在 Linux 節點上。 Windows Server （目前在 AKS 中的預覽） 的節點不應該執行輸入控制器。 使用指定的節點選取器`--set nodeSelector`告訴 Kubernetes 排程器，在以 Linux 為基礎的節點上執行 NGINX 輸入控制器的參數。

> [!TIP]
> 下列範例會建立名為輸入資源的 Kubernetes 命名空間*輸入 basic*。 視需要請指定您自己的環境的命名空間。 如果您的 AKS 叢集不啟用 RBAC，請新增`--set rbac.create=false`Helm 命令。

> [!TIP]
> 如果您想要啟用[用戶端來源 IP 保留][client-source-ip]針對您的叢集中容器的要求，新增`--set controller.service.externalTrafficPolicy=Local`至 Helm 安裝命令。 IP 會儲存在要求標頭中的用戶端來源*X 轉送的*。 當使用輸入控制器與用戶端啟用的來源 IP 保留時，SSL 傳遞將無法運作。

```console
# Create a namespace for your ingress resources
kubectl create namespace ingress-basic

# Use Helm to deploy an NGINX ingress controller
helm install stable/nginx-ingress \
    --namespace ingress-basic \
    --set controller.replicaCount=2 \
    --set controller.nodeSelector."beta\.kubernetes\.io/os"=linux \
    --set defaultBackend.nodeSelector."beta\.kubernetes\.io/os"=linux \
    --set controller.service.loadBalancerIP="40.121.63.72"
```

為 NGINX 輸入控制器建立 Kubernetes 負載平衡器服務時，系統會指派靜態 IP 位址，如下列輸出範例所示：

```
$ kubectl get service -l app=nginx-ingress --namespace ingress-basic

NAME                                        TYPE           CLUSTER-IP    EXTERNAL-IP    PORT(S)                      AGE
dinky-panda-nginx-ingress-controller        LoadBalancer   10.0.232.56   40.121.63.72   80:31978/TCP,443:32037/TCP   3m
dinky-panda-nginx-ingress-default-backend   ClusterIP      10.0.95.248   <none>         80/TCP                       3m
```

尚未建立任何輸入規則，因此，如果您瀏覽到公用 IP 位址，就會顯示 NGINX 輸入控制器的預設 404 頁面。 輸入規則會於下列步驟中進行設定。

## <a name="configure-a-dns-name"></a>設定 DNS 名稱

對於正常運作的 HTTPS 憑證，設定適用於輸入控制器 IP 位址的 FQDN。 以您輸入控制器的 IP 位址和您要針對 FQDN 使用的名稱來更新下列指令碼：

```azurecli-interactive
#!/bin/bash

# Public IP address of your ingress controller
IP="40.121.63.72"

# Name to associate with public IP address
DNSNAME="demo-aks-ingress"

# Get the resource-id of the public ip
PUBLICIPID=$(az network public-ip list --query "[?ipAddress!=null]|[?contains(ipAddress, '$IP')].[id]" --output tsv)

# Update public ip address with DNS name
az network public-ip update --ids $PUBLICIPID --dns-name $DNSNAME
```

輸入控制器現在已可透過 FQDN 來存取。

## <a name="install-cert-manager"></a>安裝 cert-manager

NGINX 輸入控制器支援 TLS 終止。 有數種方式可擷取和設定 HTTPS 的憑證。 本文示範如何使用[憑證管理員][cert-manager], which provides automatic [Lets Encrypt][lets-encrypt]憑證的產生和管理功能。

> [!NOTE]
> 本文會針對 Let's Encrypt 使用 `staging` 環境。 在生產環境部署中，於資源定義中以及安裝 Helm 圖表時使用 `letsencrypt-prod` 和 `https://acme-v02.api.letsencrypt.org/directory`。

若要在已啟用 RBAC 的叢集中安裝 cert-manager 控制器，請使用下列 `helm install` 命令：

```console
# Install the CustomResourceDefinition resources separately
kubectl apply -f https://raw.githubusercontent.com/jetstack/cert-manager/release-0.8/deploy/manifests/00-crds.yaml

# Create the namespace for cert-manager
kubectl create namespace cert-manager

# Label the cert-manager namespace to disable resource validation
kubectl label namespace cert-manager certmanager.k8s.io/disable-validation=true

# Add the Jetstack Helm repository
helm repo add jetstack https://charts.jetstack.io

# Update your local Helm chart repository cache
helm repo update

# Install the cert-manager Helm chart
helm install \
  --name cert-manager \
  --namespace cert-manager \
  --version v0.8.0 \
  jetstack/cert-manager
```

如需有關憑證管理員設定的詳細資訊，請參閱[憑證管理員專案][cert-manager]。

## <a name="create-a-ca-cluster-issuer"></a>建立 CA 叢集簽發者

憑證管理員發行憑證之前，需要[簽發者][cert-manager-issuer]or [ClusterIssuer][cert-manager-cluster-issuer]資源。 這些 Kubernetes 資源在功能上完全相同，但是 `Issuer` 會在單一命名空間中運作，而 `ClusterIssuer` 會跨所有命名空間運作。 如需詳細資訊，請參閱 <<c0> [ 憑證管理員簽發者][cert-manager-issuer]文件。

使用下列範例資訊清單來建立叢集簽發者，例如 `cluster-issuer.yaml`。 利用您組織中的有效電子郵件地址來更新電子郵件地址：

```yaml
apiVersion: certmanager.k8s.io/v1alpha1
kind: ClusterIssuer
metadata:
  name: letsencrypt-staging
  namespace: ingress-basic
spec:
  acme:
    server: https://acme-staging-v02.api.letsencrypt.org/directory
    email: user@contoso.com
    privateKeySecretRef:
      name: letsencrypt-staging
    http01: {}
```

若要建立簽發者，請使用 `kubectl apply -f cluster-issuer.yaml` 命令。

```
$ kubectl apply -f cluster-issuer.yaml

clusterissuer.certmanager.k8s.io/letsencrypt-staging created
```

## <a name="run-demo-applications"></a>執行示範應用程式

輸入控制器和憑證管理解決方案皆已設定。 現在讓我們在您的 AKS 叢集中執行兩個示範應用程式。 在此範例中，會使用 Helm 來部署簡單 'Hello world' 應用程式的兩個執行個體。

在您可以安裝範例 Helm 圖表之前，先將 Azure 範例存放庫新增至您的 Helm 環境，如下所示：

```console
helm repo add azure-samples https://azure-samples.github.io/helm-charts/
```

使用下列命令，從 Helm 圖表建立第一個示範應用程式：

```console
helm install azure-samples/aks-helloworld --namespace ingress-basic
```

現在，請安裝示範應用程式的第二個執行個體。 對第二個執行個體指定新的標題，以便在視覺上區分這兩個應用程式。 您也要指定唯一的服務名稱：

```console
helm install azure-samples/aks-helloworld \
    --namespace ingress-basic \
    --set title="AKS Ingress Demo" \
    --set serviceName="ingress-demo"
```

## <a name="create-an-ingress-route"></a>建立輸入路由

這兩個應用程式現在都已在您的 Kubernetes 叢集上執行，不過，它們是以 `ClusterIP` 類型的服務設定的。 因此，這些應用程式無法從網際網路存取。 若要使其可公開使用，請建立 Kubernetes 輸入資源。 輸入資源會設定將流量路由至這兩個應用程式之一的規則。

在下列範例中，傳至位址 `https://demo-aks-ingress.eastus.cloudapp.azure.com/` 的流量會路由傳送至名為 `aks-helloworld` 的服務。 傳至位址 `https://demo-aks-ingress.eastus.cloudapp.azure.com/hello-world-two` 的流量會路由至 `ingress-demo` 服務。 將 *hosts* 和 *host* 更新為您在上一個步驟中建立的 DNS 名稱。

建立名為 `hello-world-ingress.yaml` 的檔案，並複製到下列範例 YAML 中。

```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: hello-world-ingress
  namespace: ingress-basic
  annotations:
    kubernetes.io/ingress.class: nginx
    certmanager.k8s.io/cluster-issuer: letsencrypt-staging
    nginx.ingress.kubernetes.io/rewrite-target: /$1
spec:
  tls:
  - hosts:
    - demo-aks-ingress.eastus.cloudapp.azure.com
    secretName: tls-secret
  rules:
  - host: demo-aks-ingress.eastus.cloudapp.azure.com
    http:
      paths:
      - backend:
          serviceName: aks-helloworld
          servicePort: 80
        path: /(.*)
      - backend:
          serviceName: ingress-demo
          servicePort: 80
        path: /hello-world-two(/|$)(.*)
```

使用 `kubectl apply -f hello-world-ingress.yaml` 命令建立輸入資源。

```
$ kubectl apply -f hello-world-ingress.yaml

ingress.extensions/hello-world-ingress created
```

## <a name="create-a-certificate-object"></a>建立憑證物件

接下來，必須建立憑證資源。 憑證資源會定義所需的 X.509 憑證。 如需詳細資訊，請參閱 <<c0> [ 憑證管理員憑證][cert-manager-certificates]。

Cert-manager 有可能已使用 ingress-shim 自動為您建立憑證物件，v0.2.2 之後的 cert-manager 會自動與 ingress-shim 搭配部署。 如需詳細資訊，請參閱 <<c0> [ 填充碼的輸入文件][ingress-shim]。

若要確認已成功建立憑證，請使用 `kubectl describe certificate tls-secret --namespace ingress-basic` 命令。

如果已發出憑證，您將會看到類似下列的輸出：
```
Type    Reason          Age   From          Message
----    ------          ----  ----          -------
  Normal  CreateOrder     11m   cert-manager  Created new ACME order, attempting validation...
  Normal  DomainVerified  10m   cert-manager  Domain "demo-aks-ingress.eastus.cloudapp.azure.com" verified with "http-01" validation
  Normal  IssueCert       10m   cert-manager  Issuing certificate...
  Normal  CertObtained    10m   cert-manager  Obtained certificate from ACME server
  Normal  CertIssued      10m   cert-manager  Certificate issued successfully
```

如果您需要建立額外的憑證資源，可以使用下列範例資訊清單來執行該動作。 將 *dnsNames* 和 *domains* 更新為您在上一個步驟中建立的 DNS 名稱。 如果您使用僅供內部使用的輸入控制器，請為您的服務指定內部 DNS 名稱。

```yaml
apiVersion: certmanager.k8s.io/v1alpha1
kind: Certificate
metadata:
  name: tls-secret
  namespace: ingress-basic
spec:
  secretName: tls-secret
  dnsNames:
  - demo-aks-ingress.eastus.cloudapp.azure.com
  acme:
    config:
    - http01:
        ingressClass: nginx
      domains:
      - demo-aks-ingress.eastus.cloudapp.azure.com
  issuerRef:
    name: letsencrypt-staging
    kind: ClusterIssuer
```

若要建立憑證資源，請使用 `kubectl apply -f certificates.yaml` 命令。

```
$ kubectl apply -f certificates.yaml

certificate.certmanager.k8s.io/tls-secret created
```

## <a name="test-the-ingress-configuration"></a>測試輸入組態

開啟網頁瀏覽器至您 Kubernetes 輸入控制器的 FQDN，例如 *https://demo-aks-ingress.eastus.cloudapp.azure.com* 。

由於這些範例會使用 `letsencrypt-staging`，因此，簽發的 SSL 憑證不會受到瀏覽器信任。 接受警告提示以繼續您的應用程式。 憑證資訊顯示這個 *Fake LE Intermediate X1* 憑證是由 Let's Encrypt 所簽發的。 這個假憑證表示 `cert-manager` 已正確處理要求並接收來自提供者的憑證：

![Let's Encrypt 暫存憑證](media/ingress/staging-certificate.png)

當您變更 Let's Encrypt 來使用 `prod` 而非 `staging` 時，會使用由 Let's Encrypt 所簽發的信任憑證，如下列範例所示：

![Let’s Encrypt 憑證](media/ingress/certificate.png)

示範應用程式會顯示於網頁瀏覽器中：

![應用程式範例一](media/ingress/app-one.png)

現在，將 */hello-world-two* 路徑新增至 FQDN，例如 *https://demo-aks-ingress.eastus.cloudapp.azure.com/hello-world-two* 。 即會顯示第二個具有自訂標題的示範應用程式：

![應用程式範例二](media/ingress/app-two.png)

## <a name="clean-up-resources"></a>清除資源

本文使用 Helm 來安裝輸入元件、憑證及範例應用程式。 部署 Helm 圖表時會建立一些 Kubernetes 資源。 這些資源包含 Pod、部署和服務。 若要清除這些資源，您可以刪除整個範例命名空間或個別資源。

### <a name="delete-the-sample-namespace-and-all-resources"></a>刪除範例命名空間和所有資源

若要刪除整個範例命名空間，請使用`kubectl delete`命令並指定您的命名空間名稱。 會刪除命名空間中的所有資源。

```console
kubectl delete namespace ingress-basic
```

然後，移除 AKS hello world 應用程式的 Helm 存放庫：

```console
helm repo remove azure-samples
```

### <a name="delete-resources-individually"></a>個別地刪除資源

或者，更細微的方法是刪除建立的個別資源。 首先，移除憑證資源：

```console
kubectl delete -f certificates.yaml
kubectl delete -f cluster-issuer.yaml
```

現在使用 `helm list` 命令，列出 Helm 版本。 尋找名為 nginx-ingress  、cert-manager  和 aks-helloworld  的圖表，如下列範例輸出所示：

```
$ helm list

NAME                    REVISION    UPDATED                     STATUS      CHART                   APP VERSION NAMESPACE
waxen-hamster           1           Wed Mar  6 23:16:00 2019    DEPLOYED    nginx-ingress-1.3.1   0.22.0        kube-system
alliterating-peacock    1           Wed Mar  6 23:17:37 2019    DEPLOYED    cert-manager-v0.6.6     v0.6.2      kube-system
mollified-armadillo     1           Wed Mar  6 23:26:04 2019    DEPLOYED    aks-helloworld-0.1.0                default
wondering-clam          1           Wed Mar  6 23:26:07 2019    DEPLOYED    aks-helloworld-0.1.0                default
```

使用 `helm delete` 命令刪除版本。 下列範例會刪除 NGINX 輸入部署、憑證管理員以及兩個範例 AKS hello world 應用程式。

```
$ helm delete waxen-hamster alliterating-peacock mollified-armadillo wondering-clam

release "billowing-kitten" deleted
release "loitering-waterbuffalo" deleted
release "flabby-deer" deleted
release "linting-echidna" deleted
```

接下來，移除 AKS hello world 應用程式的 Helm 存放庫：

```console
helm repo remove azure-samples
```

移除將流量導向範例應用程式的輸入路由：

```console
kubectl delete -f hello-world-ingress.yaml
```

將自己刪除命名空間。 使用`kubectl delete`命令並指定您的命名空間名稱：

```console
kubectl delete namespace ingress-basic
```

最後，移除針對輸入控制器所建立的靜態公用 IP 位址。 提供您在本文第一個步驟中取得的 MC_  叢集資源群組名稱，例如 MC_myResourceGroup_myAKSCluster_eastus  ：

```azurecli-interactive
az network public-ip delete --resource-group MC_myResourceGroup_myAKSCluster_eastus --name myAKSPublicIP
```

## <a name="next-steps"></a>後續步驟

本文包含 AKS 的一些外部元件。 若要深入了解這些元件，請參閱下列專案頁面：

- [Helm CLI][helm-cli]
- [NGINX 輸入控制器][nginx-ingress]
- [cert-manager][cert-manager]

您也可以：

- [建立具有外部網路連線的基本輸入控制器][aks-ingress-basic]
- [啟用 HTTP 應用程式路由附加元件][aks-http-app-routing]
- [建立會使用內部的私人網路和 IP 位址輸入控制器][aks-ingress-internal]
- [建立輸入控制器，會使用您自己的 TLS 憑證][aks-ingress-own-tls]
- [使用動態公用 IP 建立輸入控制器，並設定 let 's Encrypt 來自動產生的 TLS 憑證][aks-ingress-tls]

<!-- LINKS - external -->
[helm-cli]: https://docs.microsoft.com/azure/aks/kubernetes-helm
[cert-manager]: https://github.com/jetstack/cert-manager
[cert-manager-certificates]: https://cert-manager.readthedocs.io/en/latest/reference/certificates.html
[cert-manager-cluster-issuer]: https://cert-manager.readthedocs.io/en/latest/reference/clusterissuers.html
[cert-manager-issuer]: https://cert-manager.readthedocs.io/en/latest/reference/issuers.html
[lets-encrypt]: https://letsencrypt.org/
[nginx-ingress]: https://github.com/kubernetes/ingress-nginx
[helm-install]: https://docs.helm.sh/using_helm/#installing-helm
[ingress-shim]: https://docs.cert-manager.io/en/latest/tasks/issuing-certificates/ingress-shim.html

<!-- LINKS - internal -->
[use-helm]: kubernetes-helm.md
[azure-cli-install]: /cli/azure/install-azure-cli
[az-aks-show]: /cli/azure/aks#az-aks-show
[az-network-public-ip-create]: /cli/azure/network/public-ip#az-network-public-ip-create
[aks-ingress-internal]: ingress-internal-ip.md
[aks-ingress-basic]: ingress-basic.md
[aks-ingress-tls]: ingress-tls.md
[aks-http-app-routing]: http-application-routing.md
[aks-ingress-own-tls]: ingress-own-tls.md
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[client-source-ip]: concepts-network.md#ingress-controllers
[install-azure-cli]: /cli/azure/install-azure-cli
