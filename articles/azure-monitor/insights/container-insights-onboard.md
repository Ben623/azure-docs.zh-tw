---
title: 如何啟用適用於容器的 Azure 監視器 |Microsoft Docs
description: 這篇文章說明如何啟用和設定適用於容器的 Azure 監視器，因此您可以了解如何執行您的容器，以及目前已確認哪些效能相關問題。
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: azure-monitor
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/25/2019
ms.author: magoedte
ms.openlocfilehash: 5e149fa96e0a62656804906b52adf10273321d17
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "65521905"
---
# <a name="how-to-enable-azure-monitor-for-containers"></a>如何啟用適用於容器的 Azure 監視器  

本文提供可用來設定適用於容器的 Azure 監視器來監視部署至 Kubernetes 環境和裝載於工作負載的效能選項的概觀[Azure Kubernetes Service](https://docs.microsoft.com/azure/aks/)。

使用下列受支援的方法，即可為全新、一或多個現有的 AKS 部署啟用適用於容器的 Azure 監視器：

* 從 Azure 入口網站中，Azure PowerShell，或使用 Azure CLI
* 使用 [Terraform 和 AKS](../../terraform/terraform-create-k8s-cluster-with-tf-and-aks.md)

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>必要條件 
開始之前，請確定您有下列項目：

- **Log Analytics 工作區中。** 您可以在啟用新 AKS 叢集的監視時建立它，或是讓上線體驗在 AKS 叢集訂用帳戶的預設資源群組中建立預設工作區。 若選擇自行建立它 ，您可以透過 [Azure Resource Manager](../platform/template-workspace-configuration.md)、透過 [PowerShell](../scripts/powershell-sample-create-workspace.md?toc=%2fpowershell%2fmodule%2ftoc.json)，或是在 [Azure 入口網站](../learn/quick-create-workspace.md)中建立它。
- 您所隸屬**Log Analytics 參與者 」 角色**啟用容器監視。 如需有關如何控制 Log Analytics 工作區存取的詳細資訊，請參閱[管理工作區](../platform/manage-access.md)。
- 您所隸屬 **[擁有者](../../role-based-access-control/built-in-roles.md#owner)** AKS 叢集資源上的角色。 

[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)]

## <a name="components"></a>元件 

監視效能的能力依賴適用於 Linux 的容器化 Log Analytics 代理程式，此代理程式特別為適用於容器的 Azure 監視器所開發。 這個特製化的代理程式會收集叢集中所有節點的效能和事件資料，且會自動部署並在部署期間向指定的 Log Analytics 工作區進行註冊。 代理程式版本是 microsoft/oms:ciprod04202018 或更新版本，且會以下列格式的日期呈現：*mmddyyyy*。 

>[!NOTE]
>AKS 的 Windows Server 支援的預覽版本，與 AKS 叢集與 Windows 伺服器節點，不需要代理程式來收集資料並轉寄給 Azure 監視器。 相反地，自動為標準部署的一部分部署在叢集中的 Linux 節點會收集和轉送資料至 Azure 監視器，代表在叢集中的所有 Windows 節點。  
>

發行代理程式的新版本時，代理程式會在您裝載於 Azure Kubernetes Service (AKS) 的受控 Kubernetes 叢集上自動升級。 若要遵循所發行的版本，請參閱[代理程式發行公告](https://github.com/microsoft/docker-provider/tree/ci_feature_prod) (英文)。 

>[!NOTE] 
>如果您已部署 AKS 叢集，則可以使用 Azure CLI 或提供的 Azure Resource Manager 範本來啟用監視，如此文章稍後所示。 您無法使用 `kubectl` 來生集、刪除、重新部署或部署此代理程式。 範本必須部署在叢集所在的資源群組。

您啟用適用於容器的 Azure 監視器使用下表所述的下列方法之一。

| 部署狀態 | 方法 | 描述 | 
|------------------|--------|-------------| 
| 新的 AKS 叢集 | [使用 Azure CLI 建立叢集](../../aks/kubernetes-walkthrough.md#create-aks-cluster)| 您可以啟用新的 AKS 叢集，您使用 Azure CLI 建立監視。 | 
| | [使用 Terraform 建立叢集](container-insights-enable-new-cluster.md#enable-using-terraform)| 您可以啟用新的 AKS 叢集，您使用開放原始碼工具 Terraform 所建立的監視。 | 
| 現有的 AKS 叢集 | [啟用使用 Azure CLI](container-insights-enable-existing-clusters.md#enable-using-azure-cli) | 您可以啟用監視的已使用 Azure CLI 來部署 AKS 叢集。 | 
| |[使用 Terraform 啟用](container-insights-enable-existing-clusters.md#enable-using-terraform) | 您可以啟用監視的已使用的開放原始碼工具 Terraform 來部署 AKS 叢集。 | 
| | [啟用從 Azure 監視器](container-insights-enable-existing-clusters.md#enable-from-azure-monitor-in-the-portal)| 您可以啟用監視已部署 AKS 多的叢集頁面上，在 Azure 監視器中的一或多個 AKS 叢集。 | 
| | [啟用從 AKS 叢集](container-insights-enable-existing-clusters.md#enable-directly-from-aks-cluster-in-the-portal)| 您可以啟用監視直接從 Azure 入口網站中的 AKS 叢集。 | 
| | [啟用使用 Azure Resource Manager 範本](container-insights-enable-existing-clusters.md#enable-using-an-azure-resource-manager-template)| 您可以啟用監視的預先設定的 Azure Resource Manager 範本與 AKS 叢集。 | 

## <a name="next-steps"></a>後續步驟

* 藉由啟用監視來擷取 AKS 叢集節點和 Pod 的健康情況計量，這些健康情況計量都可在 Azure 入口網站中取得。 若要了解如何使用適用於容器的 Azure 監視器，請參閱[檢視 Azure Kubernetes Service 健康情況](container-insights-analyze.md)。
