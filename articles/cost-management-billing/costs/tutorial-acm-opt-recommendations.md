---
title: 教學課程-使用建議來降低 Azure 成本
description: 本教學課程可在您針對最佳化建議採取行動時協助您降低 Azure 成本。
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 10/24/2019
ms.topic: conceptual
ms.service: cost-management-billing
manager: dougeby
ms.custom: seodec18
ms.openlocfilehash: 37253bb4c6001afe436e22597e75e2bc869fbbc8
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/15/2020
ms.locfileid: "75990290"
---
# <a name="tutorial-optimize-costs-from-recommendations"></a>教學課程：透過建議最佳化成本

Azure 成本管理可搭配 Azure Advisor，提供成本最佳化建議。 Azure Advisor 可協助您透過識別閒置及使用量過低的資源來最佳化及改善效率。 本教學課程會引導您完成範例，識別使用量過低的 Azure 資源，並讓您採取動作以降低成本。

在本教學課程中，您會了解如何：

> [!div class="checklist"]
> * 檢視成本最佳化建議以檢視潛在的使用效率不足
> * 針對建議採取行動，將虛擬機器大小調整至更符合成本效益的選項
> * 驗證動作，確保虛擬機器的大小已成功調整

## <a name="prerequisites"></a>必要條件
建議適用于各種範圍和 Azure 帳戶類型。 若要檢視所支援帳戶類型的完整清單，請參閱[了解成本管理資料](understand-cost-mgt-data.md)。 您必須至少具備一或多個下列範圍的讀取存取，才能檢視成本資料。 如需有關範圍的詳細資訊，請參閱[了解並使用範圍](understand-work-scopes.md)。

- 訂閱
- 資源群組

您必須擁有使用中的虛擬機器，且其至少需要活動 14 天。

## <a name="sign-in-to-azure"></a>登入 Azure
登入 Azure 入口網站：[https://portal.azure.com](https://portal.azure.com/)。

## <a name="view-cost-optimization-recommendations"></a>檢視成本最佳化建議

若要查看訂用帳戶的成本優化建議，請在 Azure 入口網站中開啟所需的範圍，然後選取 [ **Advisor 建議**]。

若要查看管理群組的建議，請在 Azure 入口網站中開啟所需的範圍，然後選取功能表中的 [**成本分析**]。 使用 [**範圍**] 膠囊按鈕切換至不同的範圍，例如管理群組。 選取功能表中的 [ **Advisor 建議**]。 如需有關範圍的詳細資訊，請參閱[了解並使用範圍](understand-work-scopes.md)。

![顯示在 Azure 入口網站中的成本管理 Advisor 建議](./media/tutorial-acm-opt-recommendations/advisor-recommendations.png)

建議清單會識別使用效率不足的情況，或是顯示能協助您節省額外成本的購買建議。 合計 [潛在每年節省成本] 會顯示關機或解除配置您所有符合建議規則的 VM 時，所能節省的總金額。 若您不想要將它們關機，建議您考慮將其大小調整至較不昂貴的 VM SKU。

[影響] 類別與 [潛在每年節省成本] 旨在協助您識別具有潛力，可以盡量節省成本的建議。

高影響力建議事項包括：
- [購買保留的虛擬機器實例以節省隨用隨付費用的費用](../../advisor/advisor-cost-recommendations.md#buy-reserved-virtual-machine-instances-to-save-money-over-pay-as-you-go-costs)
- [藉由調整大小或關閉使用量過低的實例，將虛擬機器花費優化](../../advisor/advisor-cost-recommendations.md#optimize-virtual-machine-spend-by-resizing-or-shutting-down-underutilized-instances)
- [使用標準儲存體儲存受控磁碟快照集](../../advisor/advisor-cost-recommendations.md#use-standard-snapshots-for-managed-disks)

中度影響建議包括：
- [刪除失敗 Azure Data Factory 管線](../../advisor/advisor-cost-recommendations.md#delete-azure-data-factory-pipelines-that-are-failing)
- [藉由排除未布建的 ExpressRoute 線路來降低成本](../../advisor/advisor-cost-recommendations.md#reduce-costs-by-eliminating-unprovisioned-expressroute-circuits)
- [藉由刪除或重新設定閒置虛擬網路閘道來降低成本](../../advisor/advisor-cost-recommendations.md#reduce-costs-by-deleting-or-reconfiguring-idle-virtual-network-gateways)

## <a name="act-on-a-recommendation"></a>針對建議採取行動

Azure Advisor 會監視您的虛擬機器使用七天，然後找出使用量過低的虛擬機器。 CPU 使用量小於 (含) 5% 且網路使用量小於 (含) 7 MB 長達 4 天 (含) 以上的虛擬機器，將會視為低使用量虛擬機器。

5% 以下 (含) 的 CPU 使用量設定為預設，但您可以調整設定。 如需調整設定的詳細資訊，請參閱[針對低使用量虛擬機器建議設定平均 CPU 使用量規則](../../advisor/advisor-get-started.md#configure-low-usage-vm-recommendation)。

雖然根據設計，某些案例可能會導致低使用量，但您通常可以藉由將虛擬機器的大小變更為較不昂貴的大小，來節省費用。 若您選擇調整大小動作，則您實際節省的成本可能會有所不同。 讓我們逐步完成調整虛擬機器大小的範例。

在建議清單中，按一下 [調整大小或關閉使用量過低的虛擬機器] 建議。 在虛擬機器候選項目清單中，選擇要調整大小的虛擬機器，然後按一下虛擬機器。 隨即顯示虛擬機器的詳細資料，讓您可以確認使用量計量。 [潛在每年節省成本] 值是關機或移除 VM 時您所能節省的金額。 調整 VM 的大小可能可以節省成本，但是您將無法節省 [潛在每年節省成本] 的完整金額。

![建議詳細資料的範例](./media/tutorial-acm-opt-recommendations/recommendation-details.png)

在 VM 詳細資料中，檢查虛擬機器的使用量，確認其為適合的調整大小候選項目。

![顯示歷史使用率的範例 VM 詳細資料](./media/tutorial-acm-opt-recommendations/vm-details.png)

請注意目前虛擬機器的大小。 在確認虛擬機器的大小應該進行調整之後，請關閉 VM 詳細資料，讓您可以查看虛擬機器清單。

在要關閉或調整大小的候選清單中，選取 * * [調整 *&lt;FromVirtualMachineSKU*的大小]&gt;為 *&lt;ToVirtualMachineSKU&gt;* * *。
![使用選項調整虛擬機器大小的範例建議](./media/tutorial-acm-opt-recommendations/resize-vm.png)

接下來，您會看到一份可用的調整大小選項清單。 選擇可為您帶來最佳效能，並且可讓案例最符合成本效益的選項。 在下列範例中，選擇的選項會從**Standard_D8s_v3**調整大小為**Standard_D2s_v3**。

![可供您選擇大小的可用 VM 大小範例清單](./media/tutorial-acm-opt-recommendations/choose-size.png)

選擇適當的大小之後，請按一下 [重**設**大小] 來開始調整大小動作。

調整大小需要重新開機正在使用中的執行中虛擬機器。 若虛擬機器位於生產環境，我們建議您在上班時間結束之後再執行調整大小作業。 排程重新開機可減少因暫時無法使用而導致的中斷。

## <a name="verify-the-action"></a>驗證動作

當 VM 成功完成調整大小之後，隨即顯示 Azure 通知。

![已成功調整大小的虛擬機器通知](./media/tutorial-acm-opt-recommendations/resized-notification.png)

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已了解如何：

> [!div class="checklist"]
> * 檢視成本最佳化建議以檢視潛在的使用效率不足
> * 針對建議採取行動，將虛擬機器大小調整至更符合成本效益的選項
> * 驗證動作，確保虛擬機器的大小已成功調整

若您尚未閱讀成本管理最佳做法一文，則它可提供高層級指導及協助管理成本時需要考慮的準則。

> [!div class="nextstepaction"]
> [成本管理最佳做法](cost-mgt-best-practices.md)
