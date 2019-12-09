---
title: 在 Azure Kubernetes Service (AKS) 中建立適用於多個 Pod 的靜態磁碟區
description: 了解如何透過 Azure 檔案服務手動建立磁碟區，以搭配 Azure Kubernetes Service (AKS) 中的多個平行 Pod 使用
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 03/01/2019
ms.author: mlearned
ms.openlocfilehash: 7b5f7c25cd1627475d8e37a539956f01ae6151ab
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/08/2019
ms.locfileid: "74914027"
---
# <a name="manually-create-and-use-a-volume-with-azure-files-share-in-azure-kubernetes-service-aks"></a>在 Azure Kubernetes Service (AKS) 中手動建立和使用 Azure 檔案共用的磁碟區

容器型應用程式常常需要存取和保存外部資料磁碟區中的資料。 如果多個 pod 需要平行存取相同的存放磁片區，您可以使用 Azure 檔案儲存體，以使用[伺服器訊息區（SMB）通訊協定][smb-overview]進行連接。 本文會示範如何手動建立 Azure 檔案共用，並將其連結到 AKS 中的 Pod。

如需有關 Kubernetes 磁片區的詳細資訊，請參閱[AKS 中應用程式的儲存體選項][concepts-storage]。

## <a name="before-you-begin"></a>開始之前

此文章假設您目前具有 AKS 叢集。 如果您需要 AKS 叢集，請參閱[使用 Azure CLI][aks-quickstart-cli]或[使用 Azure 入口網站][aks-quickstart-portal]的 AKS 快速入門。

您也需要安裝並設定 Azure CLI 版本2.0.59 或更新版本。 執行  `az --version` 以尋找版本。 如果您需要安裝或升級，請參閱 [安裝 Azure CLI][install-azure-cli]。

## <a name="create-an-azure-file-share"></a>建立 Azure 檔案共用

在使用 Azure 檔案服務作為 Kubernetes 磁碟區之前，您必須建立 Azure 儲存體帳戶與檔案共用。 下列命令會建立名為*myAKSShare*的資源群組、儲存體帳戶，以及名為*aksshare*的檔案共用：

```azurecli-interactive
# Change these four parameters as needed for your own environment
AKS_PERS_STORAGE_ACCOUNT_NAME=mystorageaccount$RANDOM
AKS_PERS_RESOURCE_GROUP=myAKSShare
AKS_PERS_LOCATION=eastus
AKS_PERS_SHARE_NAME=aksshare

# Create a resource group
az group create --name $AKS_PERS_RESOURCE_GROUP --location $AKS_PERS_LOCATION

# Create a storage account
az storage account create -n $AKS_PERS_STORAGE_ACCOUNT_NAME -g $AKS_PERS_RESOURCE_GROUP -l $AKS_PERS_LOCATION --sku Standard_LRS

# Export the connection string as an environment variable, this is used when creating the Azure file share
export AZURE_STORAGE_CONNECTION_STRING=$(az storage account show-connection-string -n $AKS_PERS_STORAGE_ACCOUNT_NAME -g $AKS_PERS_RESOURCE_GROUP -o tsv)

# Create the file share
az storage share create -n $AKS_PERS_SHARE_NAME --connection-string $AZURE_STORAGE_CONNECTION_STRING

# Get storage account key
STORAGE_KEY=$(az storage account keys list --resource-group $AKS_PERS_RESOURCE_GROUP --account-name $AKS_PERS_STORAGE_ACCOUNT_NAME --query "[0].value" -o tsv)

# Echo storage account name and key
echo Storage account name: $AKS_PERS_STORAGE_ACCOUNT_NAME
echo Storage account key: $STORAGE_KEY
```

請記下指令碼輸出末端所示的儲存體帳戶名稱和金鑰。 當您在下列其中一個步驟內建立 Kubernetes 磁碟區時，會需要這些值。

## <a name="create-a-kubernetes-secret"></a>建立 Kubernetes 祕密

Kubernetes 必須要有認證才能存取前面步驟中建立的檔案共用。 這些認證會儲存在[Kubernetes 秘密][kubernetes-secret]中，這會在您建立 Kubernetes pod 時參考。

使用 `kubectl create secret` 命令建立秘密。 下列範例會建立名為 azure-secret 的共用，並填入前面步驟中的 azurestorageaccountname 和 azurestorageaccountkey。 若要使用現有的 Azure 儲存體帳戶，請提供帳戶名稱和金鑰。

```console
kubectl create secret generic azure-secret --from-literal=azurestorageaccountname=$AKS_PERS_STORAGE_ACCOUNT_NAME --from-literal=azurestorageaccountkey=$STORAGE_KEY
```

## <a name="mount-the-file-share-as-a-volume"></a>將檔案共用掛接為磁碟區

若要將 Azure 檔案儲存體共用掛接至您的 pod，請在容器規格中設定磁片區。請使用下列內容建立名為 `azure-files-pod.yaml` 的新檔案。 如果您變更了檔案共用或祕密的名稱，請更新 shareName 和 secretName。 若有需要，請更新 `mountPath`，這是 Pod 中檔案共用掛接所在的路徑。 若是 Windows Server 容器（目前在 AKS 中處於預覽狀態），請使用 Windows 路徑慣例指定*mountPath* ，例如「 *d：* 」。

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: mypod
spec:
  containers:
  - image: nginx:1.15.5
    name: mypod
    resources:
      requests:
        cpu: 100m
        memory: 128Mi
      limits:
        cpu: 250m
        memory: 256Mi
    volumeMounts:
      - name: azure
        mountPath: /mnt/azure
  volumes:
  - name: azure
    azureFile:
      secretName: azure-secret
      shareName: aksshare
      readOnly: false
```

使用 `kubectl` 命令建立 Pod。

```console
kubectl apply -f azure-files-pod.yaml
```

您現在已有一個 Azure 檔案共用掛接在 /mnt/azure 的執行中 Pod。 您可以使用 `kubectl describe pod mypod` 來驗證是否已成功掛接共用。 下列扼要範例輸出顯示容器中掛接的磁碟區：

```
Containers:
  mypod:
    Container ID:   docker://86d244cfc7c4822401e88f55fd75217d213aa9c3c6a3df169e76e8e25ed28166
    Image:          nginx:1.15.5
    Image ID:       docker-pullable://nginx@sha256:9ad0746d8f2ea6df3a17ba89eca40b48c47066dfab55a75e08e2b70fc80d929e
    State:          Running
      Started:      Sat, 02 Mar 2019 00:05:47 +0000
    Ready:          True
    Mounts:
      /mnt/azure from azure (rw)
      /var/run/secrets/kubernetes.io/serviceaccount from default-token-z5sd7 (ro)
[...]
Volumes:
  azure:
    Type:        AzureFile (an Azure File Service mount on the host and bind mount to the pod)
    SecretName:  azure-secret
    ShareName:   aksshare
    ReadOnly:    false
  default-token-z5sd7:
    Type:        Secret (a volume populated by a Secret)
    SecretName:  default-token-z5sd7
[...]
```

## <a name="mount-options"></a>掛接選項

*適用于*Kubernetes 版本1.9.1 和以上的*dirMode*的預設值為*0755* 。 如果使用 Kuberetes version 1.8.5 版或更高版本的叢集，並以靜態方式建立持續性磁片區物件，則必須在*PersistentVolume*物件上指定掛接選項。 下列範例會設定 0777：

```yaml
apiVersion: v1
kind: PersistentVolume
metadata:
  name: azurefile
spec:
  capacity:
    storage: 5Gi
  accessModes:
    - ReadWriteMany
  storageClassName: azurefile
  azureFile:
    secretName: azure-secret
    shareName: aksshare
    readOnly: false
  mountOptions:
  - dir_mode=0777
  - file_mode=0777
  - uid=1000
  - gid=1000
  - mfsymlinks
  - nobrl
```

如果您是使用 1.8.0-1.8.4 版的叢集，可將 runAsUser 值設定為 0 來指定資訊安全內容。 如需 Pod 安全性內容的詳細資訊，請參閱[設定安全性內容][kubernetes-security-context]。

若要更新您的掛接選項，請使用*PersistentVolume*建立*azurefile-掛接-選項-yaml*檔案。 例如：

```yaml
apiVersion: v1
kind: PersistentVolume
metadata:
  name: azurefile
spec:
  capacity:
    storage: 5Gi
  accessModes:
    - ReadWriteMany
  storageClassName: azurefile
  azureFile:
    secretName: azure-secret
    shareName: aksshare
    readOnly: false
  mountOptions:
  - dir_mode=0777
  - file_mode=0777
  - uid=1000
  - gid=1000
  - mfsymlinks
  - nobrl
```

建立*azurefile yaml*檔案，其中包含使用*PersistentVolume*的*PersistentVolumeClaim* 。 例如：

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: azurefile
spec:
  accessModes:
    - ReadWriteMany
  storageClassName: azurefile
  resources:
    requests:
      storage: 5Gi
```

使用 `kubectl` 命令來建立*PersistentVolume*和*PersistentVolumeClaim*。

```console
kubectl apply -f azurefile-mount-options-pv.yaml
kubectl apply -f azurefile-mount-options-pvc.yaml
```

確認您的*PersistentVolumeClaim*已建立並系結至*PersistentVolume*。

```console
$ kubectl get pvc azurefile

NAME        STATUS   VOLUME      CAPACITY   ACCESS MODES   STORAGECLASS   AGE
azurefile   Bound    azurefile   5Gi        RWX            azurefile      5s
```

更新您的容器規格以參考您的*PersistentVolumeClaim* ，並更新您的 pod。 例如：

```yaml
...
  volumes:
  - name: azure
    persistentVolumeClaim:
      claimName: azurefile
```

## <a name="next-steps"></a>後續步驟

如需相關的最佳作法，請參閱[AKS 中儲存和備份的最佳作法][operator-best-practices-storage]。

如需 AKS 叢集與 Azure 檔案儲存體互動的詳細資訊，請參閱[Azure 檔案儲存體的 Kubernetes 外掛程式][kubernetes-files]。

<!-- LINKS - external -->
[kubectl-create]: https://kubernetes.io/docs/user-guide/kubectl/v1.8/#create
[kubernetes-files]: https://github.com/kubernetes/examples/blob/master/staging/volumes/azure_file/README.md
[kubernetes-secret]: https://kubernetes.io/docs/concepts/configuration/secret/
[kubernetes-volumes]: https://kubernetes.io/docs/concepts/storage/volumes/
[smb-overview]: /windows/desktop/FileIO/microsoft-smb-protocol-and-cifs-protocol-overview
[kubernetes-security-context]: https://kubernetes.io/docs/tasks/configure-pod-container/security-context/

<!-- LINKS - internal -->
[az-group-create]: /cli/azure/group#az-group-create
[az-storage-create]: /cli/azure/storage/account#az-storage-account-create
[az-storage-key-list]: /cli/azure/storage/account/keys#az-storage-account-keys-list
[az-storage-share-create]: /cli/azure/storage/share#az-storage-share-create
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[install-azure-cli]: /cli/azure/install-azure-cli
[operator-best-practices-storage]: operator-best-practices-storage.md
[concepts-storage]: concepts-storage.md
