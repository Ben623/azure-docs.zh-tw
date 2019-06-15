---
title: 如何建立 Kubernetes 叢集，讓您使用 Azure Cloud Shell 的 Azure 開發人員分享空間
titleSuffix: Azure Dev Spaces
services: azure-dev-spaces
ms.service: azure-dev-spaces
author: zr-msft
ms.author: zarhoads
ms.date: 10/04/2018
ms.topic: conceptual
description: 了解如何在不安裝任何項目的情況下，直接從瀏覽器快速建立針對 Azure Dev Spaces 所啟用的 Kubernetes 叢集。
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, 容器, Helm, 服務網格, 服務網格路由傳送, kubectl, k8s
ms.openlocfilehash: cd0c8c3c26feefe3448ada1cf1575706cd17e525
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "66808682"
---
# <a name="create-a-kubernetes-cluster-using-azure-cloud-shell"></a>使用 Azure Cloud Shell 建立 Kubernetes 叢集

您可以使用[Azure Cloud Shell](/azure/cloud-shell)若要利用建立的 Azure Kubernetes 服務叢集**試試**從此頁面的按鈕。 如果您未登入，請遵循提示使用 Azure 帳戶來登入，然後在 Azure Cloud Shell 提示字元出現時，於其中輸入命令。

## <a name="create-the-cluster"></a>建立叢集

首先，在其中建立資源群組[支援 Azure 開發空間的區域][supported-regions]。

```azurecli-interactive
az group create --name MyResourceGroup --location <region>
```

使用下列命令來建立 Kubernetes 叢集：

```azurecli-interactive
az aks create -g MyResourceGroup -n MyAKS --location <region> --disable-rbac --generate-ssh-keys
```

建立叢集需要幾分鐘的時間。  完成時，輸出會以 JSON 格式顯示。 尋找 `provisioningState` 並確認它 `Succeeded`。

## <a name="next-steps"></a>後續步驟

如需完整教學課程的連結，請參閱 [Azure Dev Spaces](/azure/dev-spaces/)。

> [!IMPORTANT]
> 許多的 Azure 開發空間的快速入門和教學課程使用 Azure 開發人員空格 CLI 來執行作業。 您無法安裝 Azure 開發人員空格 CLI 在 Azure Cloud Shell 中。


[supported-regions]: ../about.md#supported-regions-and-configurations