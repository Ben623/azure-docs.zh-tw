---
title: 管理 Azure 資訊安全中心中的安全性事件 |Microsoft Docs
description: 本檔可協助您使用 Azure 資訊安全中心來管理安全性事件。
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 09/09/2019
ms.author: memildin
ms.openlocfilehash: 1a6dbaeac5355d50edb93a7f215d7f8e88231e98
ms.sourcegitcommit: f15f548aaead27b76f64d73224e8f6a1a0fc2262
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/26/2020
ms.locfileid: "77615976"
---
# <a name="manage-security-incidents-in-azure-security-center"></a>管理 Azure 資訊安全中心中的安全性事件

即使是最熟練的安全性分析師，對安全性警示進行分級和調查也可能相當耗時，而對許多人來說，很難知道要從何處著手。 透過使用[分析](security-center-detection-capabilities.md)來連結不同[安全性警示](security-center-managing-and-responding-alerts.md)之間的資訊，資訊安全中心可以提供關於攻擊活動和所有相關警示的單一檢視，讓您快速了解攻擊者所採取的動作以及受到影響的資源。

本主題說明資訊安全中心中的事件，以及如何使用修復其警示。

## <a name="what-is-a-security-incident"></a>什麼是安全性事件？

在資訊安全中心內，安全性事件是符合 [攻擊鏈](https://blogs.technet.microsoft.com/office365security/addressing-your-cxos-top-five-cloud-security-concerns/) 模式之資源的所有警示彙總。 事件會出現在 [[安全性警示](security-center-managing-and-responding-alerts.md)] 清單中。 按一下事件以查看相關的警示，讓您取得每次發生的詳細資訊。

## <a name="managing-security-incidents"></a>管理安全性事件

1. 在 [資訊安全中心] 儀表板上，按一下 [**安全性警示**] 圖格。 事件和警示會列出。 請注意，安全性事件描述具有不同於其他警示的圖示。

    ![查看安全性事件](./media/security-center-managing-and-responding-alerts/security-center-manage-alerts.png)

1. 若要查看詳細資料，請按一下事件。 [偵測**到安全性事件**] 分頁會顯示進一步的詳細資料。 [**一般資訊**] 區段可以提供觸發安全性警示的深入解析。 它會顯示如目標資源、來源 IP 位址（如果適用的話）、警示是否仍在作用中的資訊，以及有關如何補救的建議。  

    ![回應 Azure 資訊安全中心中的安全性事件](./media/security-center-managing-and-responding-alerts/security-center-alert-incident.png)

1. 若要取得每個警示的詳細資訊，請按一下警示。 資訊安全中心會根據安全性警示，建議您不同的補救方法。

   > [!NOTE]
   > 相同的警示可以做為事件的一部分存在，也能以獨立警示的形式顯示。

    ![警示詳細資料](./media/security-center-incident/security-center-incident-alert.png)

1. 請遵循針對每個警示所提供的補救步驟。

如需警示的詳細資訊，請[管理及回應安全性警示](security-center-managing-and-responding-alerts.md)。

下列主題會根據資源類型，引導您完成不同的警示：

* [IaaS Windows 機器的警示](threat-protection.md#windows-machines)
* [IaaS Linux 機器的警示](threat-protection.md#linux-machines)
* [Azure App Service 的警示](threat-protection.md#app-services)
* [Azure 容器的警示](threat-protection.md#azure-containers)
* [SQL Database 和 SQL 資料倉儲的警示](threat-protection.md#data-sql)
* [Azure 儲存體的警示](threat-protection.md#azure-storage)
* [Cosmos DB 的警示](threat-protection.md#cosmos-db)

下列主題說明資訊安全中心如何使用它從與 Azure 基礎結構整合所收集的不同遙測資料，以針對部署在 Azure 上的資源套用額外的保護層級：

* [Azure 管理層（Azure Resource Manager）的警示（預覽）](threat-protection.md#management-layer)
* [Azure Key Vault 的警示（預覽）](threat-protection.md#azure-keyvault)
* [Azure 網路層的警示](threat-protection.md#network-layer)
* [來自其他服務的警示](threat-protection.md#alerts-other)

## <a name="see-also"></a>另請參閱
在本文件中，您已了解如何使用資訊安全中心的安全性事件功能。 如要深入了解資訊安全中心，請參閱下列主題：

* [Azure 資訊安全中心中的安全性警示](security-center-alerts-overview.md)。
* [管理安全性警示](security-center-managing-and-responding-alerts.md)
* [Azure 資訊安全中心規劃和操作指南](security-center-planning-and-operations-guide.md)
