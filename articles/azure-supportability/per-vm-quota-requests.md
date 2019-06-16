---
title: 每個 VM 的 azure vCPU 配額增加要求 |Microsoft Docs
description: 每個 VM 的 vCPU 配額增加要求
author: sowmyavenkat86
ms.author: svenkat
ms.date: 06/07/2019
ms.topic: article
ms.service: azure
ms.assetid: ce37c848-ddd9-46ab-978e-6a1445728a3b
ms.openlocfilehash: f921b4a95c1b0cfb29d84c0bacc17d268af6e6c5
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "67082816"
---
# <a name="vm-series-vcpu-limit-increase"></a>增加 VM 系列 vCPU 限制

您現在可以要求透過增加**說明 + 支援**刀鋒視窗或**使用量 + 配額**入口網站刀鋒視窗。 

## <a name="request-per-vm-vcpu-quota-increase-at-subscription-level-using-the-help--support-blade"></a>每在訂用帳戶層級使用的 VM vCPU 配額增加要求**說明 + 支援**刀鋒視窗

請遵循下列指示來建立支援要求，透過 Azure 的 [說明 + 支援] 刀鋒視窗中使用 Azure 入口網站中。 

1. 從 https://portal.azure.com ，選取**說明 + 支援**。

![說明 + 支援](./media/resource-manager-core-quotas-request/helpsupport.png)
 
2.  選取 [新增支援要求]  。 

![新增支援要求](./media/resource-manager-core-quotas-request/newsupportrequest.png)

3. 在 [問題類型] 下拉式清單中，選擇**服務和訂用帳戶的限制 （配額）** 。

![問題類型下拉式清單](./media/resource-manager-core-quotas-request/issuetypedropdown.png)

4. 選取需要增加配額的訂用帳戶。

![選取的訂用帳戶 newSR](./media/resource-manager-core-quotas-request/select-subscription-sr.png)
   
5. 選取 **計算 VM (核心 Vcpu) 訂用帳戶限制就會增加**中**配額類型**下拉式清單。 

![選取 配額類型](./media/resource-manager-core-quotas-request/select-quota-type.png)

6. 在 **問題的詳細資料**，按一下您的要求提供其他資訊來協助處理程序**提供詳細資料，** 。

![提供詳細資料](./media/resource-manager-core-quotas-request/provide-details.png)

7. 在 [**配額的詳細資料**] 面板中，選取部署模型，然後選取位置。

![配額的詳細資料 DM](./media/resource-manager-core-quotas-request/quota-details.png)

8. 選取  **SKU 系列**需要增加。 

![SKU 系列](./media/resource-manager-core-quotas-request/sku-family.png)

9. 輸入您想要對訂用帳戶採取的新限制。 若要移除一行，請從 SKU 系列下拉式清單中取消核取 SKU，或按一下捨棄 [x] 圖示。 之後每個 SKU 系列輸入所需的配額，請按一下**儲存並繼續**上配額詳細資料窗格中，若要繼續建立支援要求。

![新的限制](./media/resource-manager-core-quotas-request/new-limits.png)


## <a name="request-per-vm-vcpu-quota-increase-at-subscription-level-using-usages--quota-blade"></a>依據在訂用帳戶層級使用的 VM vCPU 配額增加要求**使用方式 + 配額**刀鋒視窗

請依照下列指示來建立支援要求，透過 Azure 的 [使用量 + 配額] 使用 Azure 入口網站中可用的刀鋒視窗。 

1. 從 https://portal.azure.com 選取 [訂用帳戶]  。

![Subscriptions](./media/resource-manager-core-quotas-request/subscriptions.png)

2. 選取需要增加配額的訂用帳戶。

![選取訂用帳戶](./media/resource-manager-core-quotas-request/select-subscription.png)

3. 選取 [使用量 + 配額] 

![選取使用量和配額](./media/resource-manager-core-quotas-request/select-usage-quotas.png)

4. 在右上角，選取 [要求增加配額]  。

![要求增加配額](./media/resource-manager-core-quotas-request/request-increase.png)

5. 選取 **計算 VM (核心 Vcpu) 訂用帳戶限制就會增加**為報價類型。 

![填寫表單](./media/resource-manager-core-quotas-request/forms.png)
   
6. 在 [**配額的詳細資料**] 面板中，選取部署模型，然後選取位置。

![配額問題刀鋒視窗](./media/resource-manager-core-quotas-request/problemstep.png)

7. 選取  **SKU 系列**需要增加。

![選取的 SKU 系列](./media/resource-manager-core-quotas-request/sku-family.png)

8. 輸入您想要對訂用帳戶採取的新限制。 若要移除一行，請從 SKU 系列下拉式清單中取消核取 SKU，或按一下捨棄 [x] 圖示。 之後每個 SKU 系列輸入所需的配額，請按一下**儲存並繼續**在問題步驟頁面上，若要繼續建立支援要求。

![SKU 新配額要求](./media/resource-manager-core-quotas-request/new-limits.png)

