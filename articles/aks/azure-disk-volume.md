---
title: 在 Azure Kubernetes Service (AKS) 中建立適用於 Pod 的靜態磁碟區
description: 了解如何透過 Azure 磁碟手動建立磁碟區，以搭配 Azure Kubernetes Service (AKS) 中的 Pod 使用
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 03/01/2019
ms.author: mlearned
ms.openlocfilehash: 9017c8cf721fbb9c493dc18da769b9d6e83ddf05
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/07/2019
ms.locfileid: "67616126"
---
# <a name="manually-create-and-use-a-volume-with-azure-disks-in-azure-kubernetes-service-aks"></a>在 Azure Kubernetes Service (AKS) 中手動建立和使用 Azure 磁碟的磁碟區

容器型應用程式常常需要存取和保存外部資料磁碟區中的資料。 如果單一 Pod 需要存取儲存體，您可以使用 Azure 磁碟來呈現原生磁碟區給應用程式使用。 本文會示範如何手動建立 Azure 磁碟，並將其連結到 AKS 中的 Pod。

> [!NOTE]
> Azure 磁碟一次只能掛接到一個 Pod。 如果您需要多個 pod 之間共用永續性磁碟區，使用[Azure 檔案][azure-files-volume]。

如需有關 Kubernetes 磁碟區的詳細資訊，請參閱 < [AKS 中的應用程式的儲存體選項][concepts-storage]。

## <a name="before-you-begin"></a>開始之前

此文章假設您目前具有 AKS 叢集。 如果您需要 AKS 叢集，請參閱 AKS 快速入門[使用 Azure CLI][aks-quickstart-cli] or [using the Azure portal][aks-quickstart-portal]。

您也需要 Azure CLI 2.0.59 版或更新版本安裝並設定。 執行  `az --version` 以尋找版本。 如果您需要安裝或升級，請參閱 [安裝 Azure CLI][install-azure-cli]。

## <a name="create-an-azure-disk"></a>建立 Azure 磁碟

當您建立與 AKS 搭配使用的 Azure 磁碟時，您可以在**節點**資源群組中建立磁碟資源。 此方法可讓 AKS 叢集存取和管理磁碟資源。 如果您在個別的資源群組中建立磁碟，則必須將磁碟資源群組的 `Contributor` 角色授與叢集的 Azure Kubernetes Service (AKS) 服務主體。

在本文中，我們會在節點資源群組中建立磁碟。 首先，取得資源群組名稱，與[az aks show][az-aks-show]命令，並新增`--query nodeResourceGroup`查詢參數。 下列範例會在 *myResourceGroup* 資源群組名稱中，取得 AKS 叢集名稱 *myAKSCluster* 的節點資源群組：

```azurecli-interactive
$ az aks show --resource-group myResourceGroup --name myAKSCluster --query nodeResourceGroup -o tsv

MC_myResourceGroup_myAKSCluster_eastus
```

現在，建立磁碟，使用[az 磁碟建立][az-disk-create]命令。 指定在上一個命令中所取得的節點資源群組名稱，然後指定磁碟資源的名稱，例如 myAKSDisk  。 下列範例會建立*20*GiB 的磁碟，並一次建立磁碟的輸出識別碼。 如果您需要以 Windows Server 容器 （目前在 AKS 中的預覽） 建立使用磁碟，新增`--os-type windows`參數來正確格式化磁碟。

```azurecli-interactive
az disk create \
  --resource-group MC_myResourceGroup_myAKSCluster_eastus \
  --name myAKSDisk \
  --size-gb 20 \
  --query id --output tsv
```

> [!NOTE]
> Azure 磁碟是按 SKU 的特定大小計費。 這些 Sku 範圍 32GiB S4 P4 磁碟到 32TiB S80 或 P80 磁碟 （處於預覽狀態）。 進階受控磁碟的輸送量和 IOPS 效能，取決於 SKU 和 AKS 叢集中節點的執行個體大小。 請參閱[價格和效能的受控磁碟][managed-disk-pricing-performance]。

當命令成功完成之後，磁碟資源識別碼就會隨即顯示，如下列輸出範例所示。 在下一個步驟中，此磁碟識別碼會用來掛接磁碟。

```console
/subscriptions/<subscriptionID>/resourceGroups/MC_myAKSCluster_myAKSCluster_eastus/providers/Microsoft.Compute/disks/myAKSDisk
```

## <a name="mount-disk-as-volume"></a>將磁碟掛接為磁碟區

若要將 Azure 磁碟掛接至您的 Pod，請在容器規格中設定磁碟區。建立一個名為 `azure-disk-pod.yaml` 且含有下列內容的新檔案。 以上一個步驟中建立的磁碟名稱更新 `diskName`，並以磁碟建立命令輸出中顯示的磁碟識別碼更新 `diskURI`。 若有需要，請更新 `mountPath`，這是 Pod 中 Azure 磁碟掛接所在的路徑。 適用於 Windows Server 容器 （目前處於預覽 AKS 中） 指定*mountPath*使用 Windows 路徑慣例，例如 *'d ':* 。

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
        azureDisk:
          kind: Managed
          diskName: myAKSDisk
          diskURI: /subscriptions/<subscriptionID>/resourceGroups/MC_myAKSCluster_myAKSCluster_eastus/providers/Microsoft.Compute/disks/myAKSDisk
```

使用 `kubectl` 命令建立 Pod。

```console
kubectl apply -f azure-disk-pod.yaml
```

您現在已有一個 Azure 磁碟掛接在 `/mnt/azure` 的執行中 Pod。 您可以使用 `kubectl describe pod mypod` 來驗證是否已成功掛接磁碟。 下列扼要範例輸出顯示容器中掛接的磁碟區：

```
[...]
Volumes:
  azure:
    Type:         AzureDisk (an Azure Data Disk mount on the host and bind mount to the pod)
    DiskName:     myAKSDisk
    DiskURI:      /subscriptions/<subscriptionID/resourceGroups/MC_myResourceGroupAKS_myAKSCluster_eastus/providers/Microsoft.Compute/disks/myAKSDisk
    Kind:         Managed
    FSType:       ext4
    CachingMode:  ReadWrite
    ReadOnly:     false
  default-token-z5sd7:
    Type:        Secret (a volume populated by a Secret)
    SecretName:  default-token-z5sd7
    Optional:    false
[...]
Events:
  Type    Reason                 Age   From                               Message
  ----    ------                 ----  ----                               -------
  Normal  Scheduled              1m    default-scheduler                  Successfully assigned mypod to aks-nodepool1-79590246-0
  Normal  SuccessfulMountVolume  1m    kubelet, aks-nodepool1-79590246-0  MountVolume.SetUp succeeded for volume "default-token-z5sd7"
  Normal  SuccessfulMountVolume  41s   kubelet, aks-nodepool1-79590246-0  MountVolume.SetUp succeeded for volume "azure"
[...]
```

## <a name="next-steps"></a>後續步驟

如需相關聯的最佳作法，請參閱[儲存體和 AKS 中的備份的最佳做法][operator-best-practices-storage]。

如需有關 AKS 叢集與 Azure 磁碟進行互動，請參閱 <<c0> [ 適用於 Azure 磁碟的 Kubernetes 外掛程式][kubernetes-disks]。

<!-- LINKS - external -->
[kubernetes-disks]: https://github.com/kubernetes/examples/blob/master/staging/volumes/azure_disk/README.md
[kubernetes-volumes]: https://kubernetes.io/docs/concepts/storage/volumes/
[managed-disk-pricing-performance]: https://azure.microsoft.com/pricing/details/managed-disks/

<!-- LINKS - internal -->
[az-disk-list]: /cli/azure/disk#az-disk-list
[az-disk-create]: /cli/azure/disk#az-disk-create
[az-group-list]: /cli/azure/group#az-group-list
[az-resource-show]: /cli/azure/resource#az-resource-show
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[az-aks-show]: /cli/azure/aks#az-aks-show
[install-azure-cli]: /cli/azure/install-azure-cli
[azure-files-volume]: azure-files-volume.md
[operator-best-practices-storage]: operator-best-practices-storage.md
[concepts-storage]: concepts-storage.md
