---
title: 建立、變更或刪除 Azure 網路安全性群組
titlesuffix: Azure Virtual Network
description: 了解如何建立、變更或刪除網路安全性群組。
services: virtual-network
documentationcenter: na
author: KumudD
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/05/2018
ms.author: kumud
ms.openlocfilehash: 1c00f23570c3f8d80e39f3fe3901f866e40dc2ea
ms.sourcegitcommit: 770b060438122f090ab90d81e3ff2f023455213b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/17/2019
ms.locfileid: "68305691"
---
# <a name="create-change-or-delete-a-network-security-group"></a>建立、變更或刪除網路安全性群組

網路安全性群組中的安全性規則能讓您篩選可在虛擬網路子網路及網路介面中流入和流出的網路流量類型。 如果您不熟悉網路安全性群組，請參閱[網路安全性群組概觀](security-overview.md)以深入了解安全性群組，並完成[篩選網路流量](tutorial-filter-network-traffic.md)教學課程以獲得一些網路安全性群組相關經驗。

## <a name="before-you-begin"></a>開始之前

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

在完成本文任一節的步驟之前，請先完成下列工作︰

- 如果您還沒有 Azure 帳戶，請註冊[免費試用帳戶](https://azure.microsoft.com/free)。
- 如果使用入口網站，請開啟 https://portal.azure.com ，並使用您的 Azure 帳戶來登入。
- 如果使用 PowerShell 命令來完成這篇文章中的工作，請在 [Azure Cloud Shell](https://shell.azure.com/powershell) \(英文\) 中執行命令，或從您的電腦執行 PowerShell。 Azure Cloud Shell 是免費的互動式 Shell，可讓您用來執行本文中的步驟。 它具有預先安裝和設定的共用 Azure 工具，可與您的帳戶搭配使用。 本教學課程需要 Azure PowerShell 模組 1.0.0 版或更新版本。 執行 `Get-Module -ListAvailable Az` 來了解安裝的版本。 如果您需要升級，請參閱[安裝 Azure PowerShell 模組](/powershell/azure/install-az-ps)。 如果您在本機執行 PowerShell，則也需要執行 `Connect-AzAccount` 以建立與 Azure 的連線。
- 如果使用命令列介面 (CLI) 命令來完成這篇文章中的工作，請在 [Azure Cloud Shell](https://shell.azure.com/bash) \(英文\) 中執行命令，或從您的電腦執行 CLI。 本教學課程需要 Azure CLI 2.0.28 版或更新版本。 執行 `az --version` 來了解安裝的版本。 如果您需要安裝或升級，請參閱[安裝 Azure CLI](/cli/azure/install-azure-cli)。 如果您在本機執行 Azure CLI，則也需要執行 `az login` 以建立與 Azure 的連線。

您登入或連線到 Azure 的帳戶必須指派為[網路參與者](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)角色，或為已指派[權限](#permissions)中所列適當動作的[自訂角色](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json)。

## <a name="work-with-network-security-groups"></a>使用網路安全性群組

您可以建立網路安全性群組、[檢視所有網路安全性群組](#view-all-network-security-groups)[檢視網路安全性群組的詳細資料](#view-details-of-a-network-security-group)，以及[變更](#change-a-network-security-group)和[刪除](#delete-a-network-security-group)網路安全性群組。 您也可以讓網路安全性群組與網路介面或子網路[建立關聯或中斷關聯](#associate-or-dissociate-a-network-security-group-to-or-from-a-subnet-or-network-interface)。

### <a name="create-a-network-security-group"></a>建立網路安全性群組

每個 Azure 位置和訂用帳戶可以建立的網路安全性群組數目有所限制。 如需詳細資訊，請參閱 [Azure 限制](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits)。

1. 在入口網站的左上角，選取 [+ 建立資源]  。
2. 選取 [網路]  ，然後選取 [網路安全性群組]  。
3. 輸入網路安全性群組的 [名稱]  選取您的 [訂用帳戶]  建立新的 [資源群組]  或選取現有的資源群組、選取 [位置]  ，然後選取 [建立]  。

**命令**

- Azure CLI：[az network nsg create](/cli/azure/network/nsg#az-network-nsg-create)
- PowerShell：[New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup)

### <a name="view-all-network-security-groups"></a>檢視所有網路安全性群組

在入口網站頂端的搜尋方塊中，輸入「網路安全性群組」  。 當**網路安全性群組**出現在搜尋結果中時，請選取它。 列出的是存在於您訂用帳戶中的網路安全性群組。

**命令**

- Azure CLI：[az network nsg list](/cli/azure/network/nsg#az-network-nsg-list)
- PowerShell：[Get-AzNetworkSecurityGroup](/powershell/module/az.network/get-aznetworksecuritygroup)

### <a name="view-details-of-a-network-security-group"></a>檢視網路安全性群組的詳細資料

1. 在入口網站頂端的搜尋方塊中，輸入「網路安全性群組」  。 當**網路安全性群組**出現在搜尋結果中時，請選取它。
2. 選取清單中您想要檢視其詳細資料的網路安全性群組。 在 [設定]  底下，您可以檢視網路安全性群組所關聯的 [輸入安全性規則]  和 [輸出安全性規則]  、[網路介面]  和 [子網路]  。 您也可以啟用或停用 [診斷記錄]  ，以及檢視 [有效的安全性規則]  。 若要深入了解，請參閱[診斷記錄](virtual-network-nsg-manage-log.md)和[檢視有效的安全性規則](diagnose-network-traffic-filter-problem.md)。
3. 若要深入了解列出的一般 Azure 設定，請參閱下列文章：
    *   [活動記錄檔](../azure-monitor/platform/activity-logs-overview.md)
    *   [存取控制 (IAM)](../role-based-access-control/overview.md)
    *   [標記](../azure-resource-manager/resource-group-using-tags.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
    *   [鎖定](../azure-resource-manager/resource-group-lock-resources.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
    *   [自動化指令碼](../azure-resource-manager/manage-resource-groups-portal.md#export-resource-groups-to-templates)

**命令**

- Azure CLI：[az network nsg show](/cli/azure/network/nsg#az-network-nsg-show)
- PowerShell：[Get-AzNetworkSecurityGroup](/powershell/module/az.network/get-aznetworksecuritygroup)

### <a name="change-a-network-security-group"></a>變更網路安全性群組

1. 在入口網站頂端的搜尋方塊中，輸入「網路安全性群組」  。 當**網路安全性群組**出現在搜尋結果中時，請選取它。
2. 選取您想要變更的網路安全性群組。 最常見的變更是[新增](#create-a-security-rule)或[移除](#delete-a-security-rule)安全性規則，以及[讓網路安全性群組與子網路或網路介面建立關聯或中斷關聯](#associate-or-dissociate-a-network-security-group-to-or-from-a-subnet-or-network-interface)。

**命令**

- Azure CLI：[az network nsg update](/cli/azure/network/nsg#az-network-nsg-update)
- PowerShell：[Set-AzNetworkSecurityGroup](/powershell/module/az.network/set-aznetworksecuritygroup)

### <a name="associate-or-dissociate-a-network-security-group-to-or-from-a-subnet-or-network-interface"></a>讓網路安全性群組與子網路或網路介面建立關聯或中斷關聯

若要讓網路安全性群組與網路介面建立關聯或中斷關聯，請參閱[讓網路安全性群組與網路介面建立關聯或中斷關聯](virtual-network-network-interface.md#associate-or-dissociate-a-network-security-group)。 若要讓網路安全性群組與子網路建立關聯或中斷關聯，請參閱[變更子網路設定](virtual-network-manage-subnet.md#change-subnet-settings)。

### <a name="delete-a-network-security-group"></a>刪除網路安全性群組

如果網路安全性群組與任何子網路或網路介面關聯，便無法刪除它。 請先將網路安全性群組與所有子網路和網路介面中斷關聯，再嘗試刪除它。

1. 在入口網站頂端的搜尋方塊中，輸入「網路安全性群組」  。 當**網路安全性群組**出現在搜尋結果中時，請選取它。
2. 從清單中選取您想要刪除的網路安全性群組。
3. 選取 [刪除]  ，然後選取 [是]  。

**命令**

- Azure CLI：[az network nsg delete](/cli/azure/network/nsg#az-network-nsg-delete)
- PowerShell：[Remove-AzNetworkSecurityGroup](/powershell/module/az.network/remove-aznetworksecuritygroup)

## <a name="work-with-security-rules"></a>使用安全性規則

網路安全性群組包含零個或多個安全性規則。 您可以建立安全性規則、[檢視所有安全性規則](#view-all-security-rules)[檢視安全性規則的詳細資料](#view-details-of-a-security-rule)，以及[變更](#change-a-security-rule)和[刪除](#delete-a-security-rule)安全性規則。

### <a name="create-a-security-rule"></a>建立安全性規則

每個 Azure 位置和訂用帳戶的每個網路安全性群組可以建立的規則數目有所限制。 如需詳細資訊，請參閱 [Azure 限制](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits)。

1. 在入口網站頂端的搜尋方塊中，輸入「網路安全性群組」  。 當**網路安全性群組**出現在搜尋結果中時，請選取它。
2. 從清單中選取您想要為其新增安全性規則的網路安全性群組。
3. 在 [設定]  底下，選取 [輸入安全性規則]  。 這會列出數個現有的規則。 有些規則可能您並未新增。 建立網路安全性群組時，會在該群組中建立數個預設安全性規則。 若要深入了解，請參閱[預設安全性規則](security-overview.md#default-security-rules)。  您無法刪除預設安全性規則，但是可以使用優先順序較高的規則來覆寫它們。
4. <a name = "security-rule-settings"></a>選取 [+ 新增]  。  選取或新增下列設定的值，然後選取 [確定]  ：
    
    |設定  |值  |詳細資料  |
    |---------|---------|---------|
    |Source     | 針對輸入安全性規則，選取 [任何]  、[應用程式安全性群組]  、[IP 位址]  或 [服務標籤]  。 如果您建立輸出安全性規則，則選項會與針對 [目的地]  所列的選項相同。       | 如果您選取 [應用程式安全性群組]  ，請選取與網路介面相同之區域中的一或多個現有應用程式安全性群組。 了解如何[建立應用程式安全性群組](#create-an-application-security-group)。 如果您針對 [來源]  和 [目的地]  選取 [應用程式安全性群組]  ，則兩個應用程式安全性群組內的網路介面都必須在相同的虛擬網路中。 如果您選取 [IP 位址]  ，請指定 [來源 IP 位址/CIDR 範圍]  。 您可以指定單一值或以逗號分隔的多值清單。 多值範例：10.0.0.0/16, 192.188.1.1。 您可以指定的值數目有所限制。 如需詳細資訊，請參閱 [Azure 限制](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits)。 如果您選取 [服務標籤]  ，請選取一個服務標籤。 服務標籤是為 IP 位址類別預先定義的識別碼。 若要深入了解可用的服務標籤，以及每個標籤所代表的意義，請參閱[服務標籤](security-overview.md#service-tags)。 如果您將指定的 IP 位址指派給 Azure 虛擬機器，請確定您指定私人 IP 位址，而不是指派給虛擬機器的公用 IP 位址。 在 Azure 針對輸入安全性規則將公用 IP 位址轉譯為私人 IP 位址之後，和 Azure 針對輸出規則將私人 IP 位址轉譯為公用 IP 位址之前，安全性規則會進行處理。 若要深入了解 Azure 中的公用和私人 IP 位址，請參閱 [IP 位址類型](virtual-network-ip-addresses-overview-arm.md)。        |
    |Source port ranges     | 指定單一連接埠 (例如 80)、連接埠範圍 (例如 1024-65535)，或是單一連接埠和/或連接埠範圍的逗號分隔清單 (例如 80, 1024-65535)。 輸入星號可以允許任何連接埠上的流量。 | 連接埠和範圍指定規則將允許或拒絕哪些連接埠流量。 您可以指定的連接埠數目有所限制。 如需詳細資訊，請參閱 [Azure 限制](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits)。  |
    |目的地     | 針對輸出安全性規則選取 [**任何**]、[**應用程式安全性群組**]、[ **IP 位址**] 或 [**虛擬網路**]。 如果您要建立輸入安全性規則, 選項會與針對 [**來源**] 所列的選項相同。        | 如果您選取 [應用程式安全性群組]  ，則必須選取與網路介面相同之區域中的一或多個現有應用程式安全性群組。 了解如何[建立應用程式安全性群組](#create-an-application-security-group)。 如果您選取 [應用程式安全性群組]  ，請選取與網路介面相同之區域中的一個現有應用程式安全性群組。 如果您選取 [IP 位址]  ，請指定 [目的地 IP 位址/CIDR 範圍]  。 與 [來源]  和 [來源 IP 位址/CIDR 範圍]  類似，您可以指定單一或多個位址或範圍，且您可以指定的數目有所限制。 選取 [虛擬網路]  (服務標籤) 即表示允許流量連至虛擬網路位址空間內的所有 IP 位址。 如果您將指定的 IP 位址指派給 Azure 虛擬機器，請確定您指定私人 IP 位址，而不是指派給虛擬機器的公用 IP 位址。 在 Azure 針對輸入安全性規則將公用 IP 位址轉譯為私人 IP 位址之後，和 Azure 針對輸出規則將私人 IP 位址轉譯為公用 IP 位址之前，安全性規則會進行處理。 若要深入了解 Azure 中的公用和私人 IP 位址，請參閱 [IP 位址類型](virtual-network-ip-addresses-overview-arm.md)。        |
    |目的地連接埠範圍     | 指定單一值或以逗號分隔的值清單。 | 與 [來源連接埠範圍]  類似，您可以指定單一或多個位址和範圍，且您可以指定的數目有所限制。 |
    |Protocol     | 選取 [**任何**]、[ **TCP**]、[ **UDP** ] 或 [ **ICMP**]。        |         |
    |Action     | 選取 [允許]  或 [拒絕]  。        |         |
    |Priority     | 輸入一個介於 100 到 4096 且對網路安全性群組內的所有安全性規則而言具唯一性的值。 |規則會依照優先順序進行處理。 編號愈低，優先順序愈高。 建議您在建立規則時，於優先順序編號之間保留間距，例如 100、200、300。 保留間距可方便您未來新增比現有規則優先順序更高或更低的規則。         |
    |名稱     | 網路安全性群組內規則的唯一名稱。        |  此名稱最多可有 80 個字元。 它必須以字母或數字為開頭、以字母、數字或底線為結尾，且只能包含字母、數字、底線、句點或連字號。       |
    |描述     | 選擇性的描述。        |         |

**命令**

- Azure CLI：[az network nsg rule create](/cli/azure/network/nsg/rule#az-network-nsg-rule-create)
- PowerShell：[New-AzNetworkSecurityRuleConfig](/powershell/module/az.network/new-aznetworksecurityruleconfig)

### <a name="view-all-security-rules"></a>檢視所有安全性規則

網路安全性群組包含零個或多個規則。 若要深入了解檢視規則時列出的資訊，請參閱[網路安全性群組概觀](security-overview.md)。

1. 在入口網站頂端的搜尋方塊中，輸入「網路安全性群組」  。 當**網路安全性群組**出現在搜尋結果中時，請選取它。
2. 從清單中選取您想要檢視其規則的網路安全性群組。
3. 在 [設定]  底下，選取 [輸入安全性規則]  或 [輸出安全性規則]  。

此清單包含您已建立的所有規則，以及網路安全性群組[預設安全性規則](security-overview.md#default-security-rules)。

**命令**

- Azure CLI：[az network nsg rule list](/cli/azure/network/nsg/rule#az-network-nsg-rule-list)
- PowerShell：[Get-AzNetworkSecurityRuleConfig](/powershell/module/az.network/get-aznetworksecurityruleconfig)

### <a name="view-details-of-a-security-rule"></a>檢視安全性規則的詳細資料

1. 在入口網站頂端的搜尋方塊中，輸入「網路安全性群組」  。 當**網路安全性群組**出現在搜尋結果中時，請選取它。
2. 選取您想要檢視其安全性規則詳細資料的網路安全性群組。
3. 在 [設定]  底下，選取 [輸入安全性規則]  或 [輸出安全性規則]  。
4. 選取您想要檢視其詳細資料的規則。 如需所有設定的詳細說明，請參閱[安全性規則設定](#security-rule-settings)。

**命令**

- Azure CLI：[az network nsg rule show](/cli/azure/network/nsg/rule#az-network-nsg-rule-show)
- PowerShell：[Get-AzNetworkSecurityRuleConfig](/powershell/module/az.network/get-aznetworksecurityruleconfig)

### <a name="change-a-security-rule"></a>變更安全性規則

1. 完成[檢視安全性規則的詳細資料](#view-details-of-a-security-rule)中的步驟。
2. 視需要變更設定，然後選取 [儲存]  。 如需所有設定的詳細說明，請參閱[安全性規則設定](#security-rule-settings)。

**命令**

- Azure CLI：[az network nsg rule update](/cli/azure/network/nsg/rule#az-network-nsg-rule-update)
- PowerShell：[Set-AzNetworkSecurityRuleConfig](/powershell/module/az.network/set-aznetworksecurityruleconfig)

### <a name="delete-a-security-rule"></a>刪除安全性規則

1. 完成[檢視安全性規則的詳細資料](#view-details-of-a-security-rule)中的步驟。
2. 選取 [刪除]  ，然後選取 [是]  。

**命令**

- Azure CLI：[az network nsg rule delete](/cli/azure/network/nsg/rule#az-network-nsg-rule-delete)
- PowerShell：[Remove-AzNetworkSecurityRuleConfig](/powershell/module/az.network/remove-aznetworksecurityruleconfig)

## <a name="work-with-application-security-groups"></a>使用應用程式安全性群組

應用程式安全性群組包含零個或多個網路介面。 若要深入了解，請參閱[應用程式安全性群組](security-overview.md#application-security-groups)。 應用程式安全性群組內的所有網路介面都必須存在於相同的虛擬網路中。 若要了解如何將網路介面新增至應用程式安全性群組，請參閱[將網路介面新增至應用程式安全性群組](virtual-network-network-interface.md#add-to-or-remove-from-application-security-groups)。

### <a name="create-an-application-security-group"></a>建立應用程式安全性群組

1. 選取 Azure 入口網站左上角的 [+ 建立資源]  。
2. 在 [搜尋 Marketplace]  方塊中，輸入「應用程式安全性群組」  。 當搜尋結果中出現 [應用程式安全性群組]  時，請加以選取，在 [所有項目]  下再次選取 [應用程式安全性群組]  ，然後選取 [建立]  。
3. 輸入或選取下列資訊，然後選取 [建立]  ︰

    | 設定        | 值                                                   |
    | ---            | ---                                                     |
    | 名稱           | 名稱在資源群組內必須是唯一的。        |
    | Subscription   | 選取您的訂用帳戶。                               |
    | Resource group | 選取現有資源群組或建立新群組。 |
    | Location       | 選取位置                                       |

**命令**

- Azure CLI：[az network asg create](/cli/azure/network/asg#az-network-asg-create)
- PowerShell：[New-AzApplicationSecurityGroup](/powershell/module/az.network/new-azapplicationsecuritygroup)

### <a name="view-all-application-security-groups"></a>檢視所有應用程式安全性群組

1. 在 Azure 入口網站的左上角，選取 [所有服務]  。
2. 在 [所有服務篩選]  方塊中輸入「應用程式安全性群組」  ，然後當 [應用程式安全性群組]  出現在搜尋結果時加以選取。

**命令**

- Azure CLI：[az network asg list](/cli/azure/network/asg#az-network-asg-list)
- PowerShell：[Get-AzApplicationSecurityGroup](/powershell/module/az.network/get-azapplicationsecuritygroup)

### <a name="view-details-of-a-specific-application-security-group"></a>檢視特定應用程式安全性群組的詳細資料

1. 在 Azure 入口網站的左上角，選取 [所有服務]  。
2. 在 [所有服務篩選]  方塊中輸入「應用程式安全性群組」  ，然後當 [應用程式安全性群組]  出現在搜尋結果時加以選取。
3. 選取您想要檢視其詳細資料的應用程式安全性群組。

**命令**

- Azure CLI：[az network asg show](/cli/azure/network/asg#az-network-asg-show)
- PowerShell：[Get-AzApplicationSecurityGroup](/powershell/module/az.network/get-azapplicationsecuritygroup)

### <a name="change-an-application-security-group"></a>變更應用程式安全性群組

1. 在 Azure 入口網站的左上角，選取 [所有服務]  。
2. 在 [所有服務篩選]  方塊中輸入「應用程式安全性群組」  ，然後當 [應用程式安全性群組]  出現在搜尋結果時加以選取。
3. 選取您想要變更其設定的應用程式安全性群組。 您可以新增或移除標記，或是指派或移除應用程式安全性群組的權限。

- Azure CLI：[az network asg update](/cli/azure/network/asg#az-network-asg-update)
- PowerShell：沒有 PowerShell Cmdlet。

### <a name="delete-an-application-security-group"></a>刪除應用程式安全性群組

如果應用程式安全性群組內有網路介面，您便無法刪除該群組。 藉由變更網路介面設定或刪除網路介面，從應用程式安全性群組移除所有網路介面。 如需詳細資料，請參閱[在應用程式安全性群組新增或移除網路介面](virtual-network-network-interface.md#add-to-or-remove-from-application-security-groups)或[刪除網路介面](virtual-network-network-interface.md#delete-a-network-interface)。

1. 在 Azure 入口網站的左上角，選取 [所有服務]  。
2. 在 [所有服務篩選]  方塊中輸入「應用程式安全性群組」  ，然後當 [應用程式安全性群組]  出現在搜尋結果時加以選取。
3. 選取您想要刪除的應用程式安全性群組。
4. 選取 [刪除]  ，然後選取 [是]  刪除應用程式安全性群組。

**命令**

- Azure CLI：[az network asg delete](/cli/azure/network/asg#az-network-asg-delete)
- PowerShell：[Remove-AzApplicationSecurityGroup](/powershell/module/az.network/remove-azapplicationsecuritygroup)

## <a name="permissions"></a>Permissions

若要針對網路安全性群組、安全性規則及應用程式安全性群組執行工作，您的帳戶必須指派為[網路參與者](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)角色，或為已指派下表所列適當權限的[自訂角色](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json)：

### <a name="network-security-group"></a>網路安全性群組

| Action                                                        |   名稱                                                                |
|-------------------------------------------------------------- |   -------------------------------------------                         |
| Microsoft.Network/networkSecurityGroups/read                  |   取得網路安全性群組                                          |
| Microsoft.Network/networkSecurityGroups/write                 |   建立或更新網路安全性群組                             |
| Microsoft.Network/networkSecurityGroups/delete                |   刪除網路安全性群組                                       |
| Microsoft.Network/networkSecurityGroups/join/action           |   將網路安全性群組與子網路或網路介面建立關聯 


### <a name="network-security-group-rule"></a>網路安全性群組規則

| Action                                                        |   名稱                                                                |
|-------------------------------------------------------------- |   -------------------------------------------                         |
| Microsoft.Network/networkSecurityGroups/rules/read            |   取得規則                                                            |
| Microsoft.Network/networkSecurityGroups/rules/write           |   建立或更新規則                                               |
| Microsoft.Network/networkSecurityGroups/rules/delete          |   刪除規則                                                         |

### <a name="application-security-group"></a>應用程式安全性群組

| Action                                                                     | 名稱                                                     |
| --------------------------------------------------------------             | -------------------------------------------              |
| Microsoft.Network/applicationSecurityGroups/joinIpConfiguration/action     | 將 IP 設定加入至應用程式安全性群組|
| Microsoft.Network/applicationSecurityGroups/joinNetworkSecurityRule/action | 將安全性規則加入至應用程式安全性群組    |
| Microsoft.Network/applicationSecurityGroups/read                           | 取得應用程式安全性群組                        |
| Microsoft.Network/applicationSecurityGroups/write                          | 建立或更新應用程式安全性群組           |
| Microsoft.Network/applicationSecurityGroups/delete                         | 刪除應用程式安全性群組                     |

## <a name="next-steps"></a>後續步驟

- 使用 [PowerShell](powershell-samples.md) 或 [Azure CLI](cli-samples.md) 範例指令碼，或使用 Azure [Resource Manager 範本](template-samples.md)建立網路或應用程式安全性群組
- 為虛擬網路建立及套用 [Azure 原則](policy-samples.md)
