---
title: 使用 OpenFaaS 搭配 Azure Kubernetes Service (AKS)
description: 部署及使用 OpenFaaS 搭配 Azure Kubernetes Service (AKS)
author: justindavies
ms.topic: conceptual
ms.date: 03/05/2018
ms.author: juda
ms.custom: mvc
ms.openlocfilehash: e684aee1469f855ec651567b805262c71aaf32e5
ms.sourcegitcommit: 99ac4a0150898ce9d3c6905cbd8b3a5537dd097e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/25/2020
ms.locfileid: "77594918"
---
# <a name="using-openfaas-on-aks"></a>在 AKS 上使用 OpenFaaS

[OpenFaaS][open-faas]是一種架構，可讓您透過使用容器來建立無伺服器函式。 由於是開放原始碼專案，它在社群內被廣泛採用。 本文件詳述在 Azure Kubernetes Service (AKS) 叢集上安裝和使用 OpenFaas 的做法。

## <a name="prerequisites"></a>Prerequisites

為了要完成本文中的步驟，您需要下列項目。

* 對 Kubernetes 的基本了解。
* Azure Kubernetes Service (AKS) 叢集，並在您的開發系統上設定好 AKS 認證。
* 在您的開發系統上安裝 Azure CLI。
* 在您的系統上安裝 Git 命令列工具。

## <a name="add-the-openfaas-helm-chart-repo"></a>新增 OpenFaaS helm 圖表存放庫

OpenFaaS 會維護它自己的 helm 圖，以隨時掌握最新的變更。

```azurecli-interactive
helm repo add openfaas https://openfaas.github.io/faas-netes/
helm repo update
```

## <a name="deploy-openfaas"></a>部署 OpenFaaS

好的做法是 OpenFaaS 和 OpenFaaS 函式應該儲存在自己的 Kubernetes 命名空間中。

建立 OpenFaaS 系統和函式的命名空間：

```azurecli-interactive
kubectl apply -f https://raw.githubusercontent.com/openfaas/faas-netes/master/namespaces.yml
```

產生 OpenFaaS UI 入口網站和 REST API 的密碼：

```azurecli-interactive
# generate a random password
PASSWORD=$(head -c 12 /dev/urandom | shasum| cut -d' ' -f1)

kubectl -n openfaas create secret generic basic-auth \
--from-literal=basic-auth-user=admin \
--from-literal=basic-auth-password="$PASSWORD"
```

您可以使用 `echo $PASSWORD`取得密碼的值。

Helm 圖表會使用我們在此處建立的密碼來啟用 OpenFaaS 閘道上的基本驗證，這是透過雲端 LoadBalancer 向網際網路公開。

複製的存放庫中包含適用於 OpenFaaS 的 Helm 圖表。 使用此圖表來將 OpenFaaS 部署至 AKS 叢集。

```azurecli-interactive
helm upgrade openfaas --install openfaas/openfaas \
    --namespace openfaas  \
    --set basic_auth=true \
    --set functionNamespace=openfaas-fn \
    --set serviceType=LoadBalancer
```

輸出：

```
NAME:   openfaas
LAST DEPLOYED: Wed Feb 28 08:26:11 2018
NAMESPACE: openfaas
STATUS: DEPLOYED

RESOURCES:
==> v1/ConfigMap
NAME                 DATA  AGE
prometheus-config    2     20s
alertmanager-config  1     20s

{snip}

NOTES:
To verify that openfaas has started, run:

  kubectl --namespace=openfaas get deployments -l "release=openfaas, app=openfaas"
```

建立了用於存取 OpenFaaS 閘道的公用 IP 位址。 若要取出此 IP 位址，請使用[kubectl get service][kubectl-get]命令。 將 IP 位址指派給服務可能需要一些時間。

```console
kubectl get service -l component=gateway --namespace openfaas
```

輸出。

```console
NAME               TYPE           CLUSTER-IP     EXTERNAL-IP    PORT(S)          AGE
gateway            ClusterIP      10.0.156.194   <none>         8080/TCP         7m
gateway-external   LoadBalancer   10.0.28.18     52.186.64.52   8080:30800/TCP   7m
```

若要測試 OpenFaaS 系統，瀏覽至外部 IP 位址的 8080 連接埠，在此範例中是 `http://52.186.64.52:8080`。 系統會提示您登入。 若要提取您的密碼，請輸入 `echo $PASSWORD`。

![OpenFaaS 使用者介面](media/container-service-serverless/openfaas.png)

最後，安裝 OpenFaaS CLI。 這個範例使用 brew，如需更多選項，請參閱[OPENFAAS CLI 檔][open-faas-cli]。

```console
brew install faas-cli
```

將 `$OPENFAAS_URL` 設定為上面找到的公用 IP。

使用 Azure CLI 登入：

```azurecli-interactive
export OPENFAAS_URL=http://52.186.64.52:8080
echo -n $PASSWORD | ./faas-cli login -g $OPENFAAS_URL -u admin --password-stdin
```

## <a name="create-first-function"></a>建立第一個函式

現在，OpenFaaS 已可正常運作，請使用 OpenFaas 入口網站建立函式。

按一下 [部署新函式]，並搜尋 **Figlet**。 選取 Figlet 函式，然後按一下 [部署]。

![Figlet](media/container-service-serverless/figlet.png)

使用 curl 叫用此函式。 將下列範例中的 IP 位址取代為您的 OpenFaas 閘道。

```azurecli-interactive
curl -X POST http://52.186.64.52:8080/function/figlet -d "Hello Azure"
```

輸出：

```console
 _   _      _ _            _
| | | | ___| | | ___      / \    _____   _ _ __ ___
| |_| |/ _ \ | |/ _ \    / _ \  |_  / | | | '__/ _ \
|  _  |  __/ | | (_) |  / ___ \  / /| |_| | | |  __/
|_| |_|\___|_|_|\___/  /_/   \_\/___|\__,_|_|  \___|

```

## <a name="create-second-function"></a>建立第二個函式

現在，建立第二個函式。 這個範例會使用 OpenFaaS CLI 進行部署，並包含自訂容器映像，以及從 Cosmos DB 擷取資料。 建立函式之前必須先設定數個項目。

首先，為 Cosmos DB 建立新資源群組。

```azurecli-interactive
az group create --name serverless-backing --location eastus
```

部署 `MongoDB` 類型的 CosmosDB 執行個體。 此執行個體需有唯一名稱，將 `openfaas-cosmos` 更新為您環境中的特有項目。

```azurecli-interactive
az cosmosdb create --resource-group serverless-backing --name openfaas-cosmos --kind MongoDB
```

取得 Cosmos 資料庫連接字串，並儲存在變數中。

將 `--resource-group` 引數的值更新為您的資源群組名稱，將 `--name` 引數更新為您的 Cosmos DB 名稱。

```azurecli-interactive
COSMOS=$(az cosmosdb list-connection-strings \
  --resource-group serverless-backing \
  --name openfaas-cosmos \
  --query connectionStrings[0].connectionString \
  --output tsv)
```

現在替 Cosmos DB 填入測試資料。 建立名為 `plans.json` 的檔案，並複製下列 json。

```json
{
    "name" : "two_person",
    "friendlyName" : "Two Person Plan",
    "portionSize" : "1-2 Person",
    "mealsPerWeek" : "3 Unique meals per week",
    "price" : 72,
    "description" : "Our basic plan, delivering 3 meals per week, which will feed 1-2 people.",
    "__v" : 0
}
```

使用 *mongoimport* 工具載入包含資料的 CosmosDB 執行個體。

視需要安裝 MongoDB 工具。 下列範例會使用 brew 來安裝這些工具，如需其他選項，請參閱[MongoDB 檔][install-mongo]。

```azurecli-interactive
brew install mongodb
```

將資料載入資料庫。

```azurecli-interactive
mongoimport --uri=$COSMOS -c plans < plans.json
```

輸出：

```console
2018-02-19T14:42:14.313+0000    connected to: localhost
2018-02-19T14:42:14.918+0000    imported 1 document
```

執行下列命令建立函式。 將 `-g` 引數的值更新成您的 OpenFaaS 閘道位址。

```azurecli-interctive
faas-cli deploy -g http://52.186.64.52:8080 --image=shanepeckham/openfaascosmos --name=cosmos-query --env=NODE_ENV=$COSMOS
```

部署完成之後，您應該會看到您剛為函式建立的 OpenFaaS 端點。

```console
Deployed. 202 Accepted.
URL: http://52.186.64.52:8080/function/cosmos-query
```

使用 curl 測試函式。 將 IP 位址更新為 OpenFaaS 閘道位址。

```console
curl -s http://52.186.64.52:8080/function/cosmos-query
```

輸出：

```json
[{"ID":"","Name":"two_person","FriendlyName":"","PortionSize":"","MealsPerWeek":"","Price":72,"Description":"Our basic plan, delivering 3 meals per week, which will feed 1-2 people."}]
```

您也可以在 OpenFaaS 使用者介面中測試函式。

![替代文字](media/container-service-serverless/OpenFaaSUI.png)

## <a name="next-steps"></a>後續步驟

您可以透過一組實際操作實驗室繼續學習 OpenFaaS 研討會，其中涵蓋的主題包括如何建立您自己的 GitHub bot、取用秘密、查看計量和自動調整。

<!-- LINKS - external -->
[install-mongo]: https://docs.mongodb.com/manual/installation/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[open-faas]: https://www.openfaas.com/
[open-faas-cli]: https://github.com/openfaas/faas-cli
[openfaas-workshop]: https://github.com/openfaas/workshop
