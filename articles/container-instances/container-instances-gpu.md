---
title: 部署啟用 GPU 的 Azure 容器執行個體
description: 了解如何部署 Azure 容器執行個體以在 GPU 資源上執行。
services: container-instances
author: dlepow
manager: jeconnoc
ms.service: container-instances
ms.topic: article
ms.date: 04/17/2019
ms.author: danlep
ms.openlocfilehash: 5073b68f6ef3de330671e3ea25056e0cae976360
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "60583803"
---
# <a name="deploy-container-instances-that-use-gpu-resources"></a>部署使用 GPU 資源的容器執行個體

若要在 Azure 容器執行個體上執行某些計算密集型工作負載，請部署包含「GPU 資源」  的[容器群組](container-instances-container-groups.md)。 群組中的容器執行個體可以存取一或多個 NVIDIA Tesla GPU，同時執行容器工作負載，例如 CUDA 和深度學習應用程式。

這篇文章示範如何加入 GPU 資源，當您使用部署容器群組[YAML 檔案](container-instances-multi-container-yaml.md)或是[Resource Manager 範本](container-instances-multi-container-group.md)。 當您部署容器執行個體使用 Azure 入口網站時，您也可以指定 GPU 資源。

> [!IMPORTANT]
> 此功能目前在預覽階段，但[有某些限制](#preview-limitations)。 若您同意[補充的使用規定][terms-of-use]，即可取得預覽。 在公開上市 (GA) 之前，此功能的某些領域可能會變更。

## <a name="preview-limitations"></a>預覽限制

在預覽中，使用在容器群組中的 GPU 資源時，適用下列限制。 

[!INCLUDE [container-instances-gpu-regions](../../includes/container-instances-gpu-regions.md)]

會隨時間新增其他區域的支援。

**支援的 OS 類型**：僅限 Linux

**其他限制**：當容器群組是部署到[虛擬網路](container-instances-vnet.md)中時，就無法使用 GPU 資源。

## <a name="about-gpu-resources"></a>關於 GPU 資源

### <a name="count-and-sku"></a>計數和 SKU

若要在容器執行個體中使用 GPU，請使用下列資訊來指定「GPU 資源」  ：

* **計數** - GPU 數目：**1**、**2** 或 **4**。
* **SKU** - GPU SKU：**K80**、**P100** 或 **V100**。 每個 SKU 都對應到下列其中一個啟用 GPU 的 Azure VM 系列的 NVIDIA Tesla GPU：

  | SKU | VM 系列 |
  | --- | --- |
  | K80 | [NC](../virtual-machines/linux/sizes-gpu.md#nc-series) |
  | P100 | [NCv2](../virtual-machines/linux/sizes-gpu.md#ncv2-series) |
  | V100 | [NCv3](../virtual-machines/linux/sizes-gpu.md#ncv3-series) |

[!INCLUDE [container-instances-gpu-limits](../../includes/container-instances-gpu-limits.md)]

部署 GPU 資源時，將 CPU 和記憶體資源設定適用於工作負載，最多上表中顯示的最大值。 這些值是目前大於沒有 GPU 資源的容器群組中可用的 CPU 和記憶體資源。  

### <a name="things-to-know"></a>須知事項

* **部署時間** - 建立包含 GPU 資源的容器群組需要最多 **8-10 分鐘**。 這是因為在 Azure 中佈建和設定 GPU VM 的額外時間。 

* **定價** - 類似於沒有 GPU 資源的容器群組，Azure 是以含 GPU 資源的容器群組在其「持續時間」  內所耗用的資源來計費。 持續時間是從提取您第一個容器的映像開始計算，直到容器群組終止。 不包含部署容器群組的時間。

  請參閱[定價詳細資料](https://azure.microsoft.com/pricing/details/container-instances/)。

* **CUDA 驅動程式** - 含 GPU 資源的容器執行個體已預先佈建 NVIDIA CUDA 驅動程式和容器執行階段，因此您可以使用針對 CUDA 工作負載開發的容器映像。

  在這個階段，我們就會支援 CUDA 9.0。 例如，您可以使用下列 Docker 檔案的基底映像：
  * [nvidia/cuda:9.0-base-ubuntu16.04](https://hub.docker.com/r/nvidia/cuda/)
  * [tensorflow/tensorflow:1.12.0-gpu-py3](https://hub.docker.com/r/tensorflow/tensorflow)
    
## <a name="yaml-example"></a>YAML 範例

新增 GPU 資源的方法之一是使用 [YAML 檔案](container-instances-multi-container-yaml.md)部署容器群組。 將下列 YAML 複製到名為 *gpu-deploy-aci.yaml* 的新檔案中，然後儲存檔案。 此 YAML 會建立名為 *gpucontainergroup* 的 容器群組，指定含 K80 GPU 的容器執行個體。 該執行個體執行範例 CUDA 向量加法應用程式。 資源要求足以執行工作負載。

```YAML
additional_properties: {}
apiVersion: '2018-10-01'
name: gpucontainergroup
properties:
  containers:
  - name: gpucontainer
    properties:
      image: k8s-gcrio.azureedge.net/cuda-vector-add:v0.1
      resources:
        requests:
          cpu: 1.0
          memoryInGB: 1.5
          gpu:
            count: 1
            sku: K80
  osType: Linux
  restartPolicy: OnFailure
```

使用 [az container create][az-container-create] 命令來部署容器群組，並為 `--file` 參數指定 YAML 檔案名稱。 您需要提供資源群組的名稱和容器群組的位置，例如支援 GPU 資源的 *eastus*。  

```azurecli
az container create --resource-group myResourceGroup --file gpu-deploy-aci.yaml --location eastus
```

部署需要數分鐘才能完成。 然後，容器會啟動，並執行 CUDA 向量加法運算式。 執行 [az container logs][az-container-logs] 命令以檢視記錄輸出：

```azurecli
az container logs --resource-group myResourceGroup --name gpucontainergroup --container-name gpucontainer
```

輸出：

```Console
[Vector addition of 50000 elements]
Copy input data from the host memory to the CUDA device
CUDA kernel launch with 196 blocks of 256 threads
Copy output data from the CUDA device to the host memory
Test PASSED
Done
```

## <a name="resource-manager-template-example"></a>Resource Manager 範本範例

部署包含 GPU 資源之容器群組的另一個方式是使用 [Resource Manager 範本](container-instances-multi-container-group.md)。 由建立名為 `gpudeploy.json` 的檔案開始，並將下列 JSON 複製到該檔案中。 此範例會部署含 V100 GPU 的容器執行個體，此執行個體對 [MNIST 資料集](http://yann.lecun.com/exdb/mnist/) \(英文\) 執行 [TensorFlow](https://www.tensorflow.org/versions/r1.1/get_started/mnist/beginners) \(英文\) 定型工作。 資源要求足以執行工作負載。

```JSON
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
      "containerGroupName": {
        "type": "string",
        "defaultValue": "gpucontainergrouprm",
        "metadata": {
          "description": "Container Group name."
        }
      }
    },
    "variables": {
      "containername": "gpucontainer",
      "containerimage": "microsoft/samples-tf-mnist-demo:gpu"
    },
    "resources": [
      {
        "name": "[parameters('containerGroupName')]",
        "type": "Microsoft.ContainerInstance/containerGroups",
        "apiVersion": "2018-10-01",
        "location": "[resourceGroup().location]",
        "properties": {
            "containers": [
            {
              "name": "[variables('containername')]",
              "properties": {
                "image": "[variables('containerimage')]",
                "resources": {
                  "requests": {
                    "cpu": 4.0,
                    "memoryInGb": 12.0,
                    "gpu": {
                        "count": 1,
                        "sku": "V100"
                  }
                }
              }
            }
          }
        ],
        "osType": "Linux",
        "restartPolicy": "OnFailure"
        }
      }
    ]
}
```

使用 [az group deployment create][az-group-deployment-create] 命令來部署範本。 您需要提供資源群組的名稱，且該資源群組需要是建立在支援 GPU 資源的區域 (如 *eastus*)。

```azurecli-interactive
az group deployment create --resource-group myResourceGroup --template-file gpudeploy.json
```

部署需要數分鐘才能完成。 然後，容器會啟動並執行 TensorFlow 作業。 執行 [az container logs][az-container-logs] 命令以檢視記錄輸出：

```azurecli
az container logs --resource-group myResourceGroup --name gpucontainergrouprm --container-name gpucontainer
```

輸出：

```Console
2018-10-25 18:31:10.155010: I tensorflow/core/platform/cpu_feature_guard.cc:137] Your CPU supports instructions that this TensorFlow binary was not compiled to use: SSE4.1 SSE4.2 AVX AVX2 FMA
2018-10-25 18:31:10.305937: I tensorflow/core/common_runtime/gpu/gpu_device.cc:1030] Found device 0 with properties:
name: Tesla K80 major: 3 minor: 7 memoryClockRate(GHz): 0.8235
pciBusID: ccb6:00:00.0
totalMemory: 11.92GiB freeMemory: 11.85GiB
2018-10-25 18:31:10.305981: I tensorflow/core/common_runtime/gpu/gpu_device.cc:1120] Creating TensorFlow device (/device:GPU:0) -> (device: 0, name: Tesla K80, pci bus id: ccb6:00:00.0, compute capability: 3.7)
2018-10-25 18:31:14.941723: I tensorflow/stream_executor/dso_loader.cc:139] successfully opened CUDA library libcupti.so.8.0 locally
Successfully downloaded train-images-idx3-ubyte.gz 9912422 bytes.
Extracting /tmp/tensorflow/input_data/train-images-idx3-ubyte.gz
Successfully downloaded train-labels-idx1-ubyte.gz 28881 bytes.
Extracting /tmp/tensorflow/input_data/train-labels-idx1-ubyte.gz
Successfully downloaded t10k-images-idx3-ubyte.gz 1648877 bytes.
Extracting /tmp/tensorflow/input_data/t10k-images-idx3-ubyte.gz
Successfully downloaded t10k-labels-idx1-ubyte.gz 4542 bytes.
Extracting /tmp/tensorflow/input_data/t10k-labels-idx1-ubyte.gz
Accuracy at step 0: 0.097
Accuracy at step 10: 0.6993
Accuracy at step 20: 0.8208
Accuracy at step 30: 0.8594
...
Accuracy at step 990: 0.969
Adding run metadata for 999
```

## <a name="clean-up-resources"></a>清除資源

因為使用 GPU 資源很昂貴，所以請確保您的容器不會非預期地長時間執行。 您可以在 Azure 入口網站中監視您的容器，或使用 [az container show][az-container-show] 命令來檢查容器群組的狀態。 例如:

```azurecli
az container show --resource-group myResourceGroup --name gpucontainergroup --output table
```

當您使用完所建立的容器執行個體後，請使用下列命令將其刪除：

```azurecli
az container delete --resource-group myResourceGroup --name gpucontainergroup -y
az container delete --resource-group myResourceGroup --name gpucontainergrouprm -y
```

## <a name="next-steps"></a>後續步驟

* 深入了解如何使用 [YAML 檔案](container-instances-multi-container-yaml.md)或 [Resource Manager 範本](container-instances-multi-container-group.md)來部署容器群組。
* 深入了解 Azure 中的 [GPU 最佳化 VM 大小](../virtual-machines/linux/sizes-gpu.md)。


<!-- IMAGES -->
[aci-vnet-01]: ./media/container-instances-vnet/aci-vnet-01.png

<!-- LINKS - External -->
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container#az-container-create
[az-container-show]: /cli/azure/container#az-container-show
[az-container-logs]: /cli/azure/container#az-container-logs
[az-container-show]: /cli/azure/container#az-container-show
[az-group-deployment-create]: /cli/azure/group/deployment#az-group-deployment-create
