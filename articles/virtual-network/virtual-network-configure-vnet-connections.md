---
title: 設定及驗證虛擬網路或 VPN 連線
description: 設定及驗證各種 Azure VPN 和虛擬網路部署的逐步指引
services: virtual-network
documentationcenter: na
author: v-miegge
manager: dcscontentpm
editor: ''
ms.assetid: 0433a4f4-b5a0-476d-b398-1506c57eafa2
ms.service: virtual-network
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/28/2019
ms.author: kaushika
ms.openlocfilehash: dddf402455292e19bf0fcda3c50d9ce10d5888d2
ms.sourcegitcommit: cd70273f0845cd39b435bd5978ca0df4ac4d7b2c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/18/2019
ms.locfileid: "71099065"
---
# <a name="configure-and-validate-virtual-network-or-vpn-connections"></a>設定及驗證虛擬網路或 VPN 連線

本逐步解說提供設定和驗證各種 Azure VPN 和虛擬網路部署的逐步指引。 案例包括傳輸路由、網路對網路連線、邊界閘道協定（BGP）、多網站連線和點對站連線。

Azure VPN 閘道可讓您彈性地在 Azure 中安排幾乎任何類型的已連線虛擬網路拓撲。 例如，您可以將虛擬網路連線：

- 跨區域。
- 在虛擬網路類型（Azure Resource Manager 與傳統）之間。
- 在 Azure 中或內部部署的混合式環境內。
- 在不同的訂用帳戶中。 

## <a name="network-to-network-vpn-connection"></a>網路對網路 VPN 連線

透過 VPN 將虛擬網路連線到另一個虛擬網路（網路對網路），類似于將虛擬網路連接到內部部署網站位置。 這兩種連線類型都使用 VPN 閘道，透過 IPsec 和 IKE 提供安全的通道。 虛擬網路可位於相同或不同的區域，以及來自相同或不同的訂用帳戶。

![使用 IPsec 的網路對網路連線](./media/virtual-network-configure-vnet-connections/4034386_en_2.png)
 
如果您的虛擬網路位於相同的區域，您可能會想要考慮使用虛擬網路對等互連來連接它們。 虛擬網路對等互連不會使用 VPN 閘道。 它會增加輸送量並減少延遲。 若要設定虛擬網路對等互連連線，請選取 [**設定並驗證 VNet 對等互連**]。

如果您的虛擬網路是透過 Azure Resource manager 部署模型所建立，請選取 [**設定和驗證 Resource Manager vnet 至 Resource Manager vnet**連線]，以設定 VPN 連線。

如果其中一個虛擬網路是透過 Azure 傳統部署模型建立，而另一個是透過 Resource Manager 建立，請選取 [**設定傳統 vnet 並驗證至 Resource Manager VNet**連線] 以設定 VPN 連線。

### <a name="configure-virtual-network-peering-for-two-virtual-networks-in-the-same-region"></a>為相同區域中的兩個虛擬網路設定虛擬網路對等互連

開始執行和設定 Azure 虛擬網路對等互連之前，請確定您符合下列必要條件：

* 對等互連的虛擬網路必須存在於同一個 Azure 區域中。
* 對等互連的虛擬網路必須有不重迭的 IP 位址空間。
* 虛擬網路對等互連是介於兩個虛擬網路之間。 對等互連之間沒有任何衍生的可轉移關聯性。 例如，如果 VNetA 是以 VNetB 對等互連，而 VNetB 是對等互連 with VNetC，VNetA 就*不*會對等互連 with VNetC。

當您符合需求時，可以遵循[教學課程：使用 Azure 入口網站](https://docs.microsoft.com/azure/virtual-network/virtual-network-create-peering)建立和設定對等互連，將虛擬網路連線到虛擬網路對等互連。

若要檢查對等互連設定，請使用下列方法：

1. 使用具有必要[角色和許可權](virtual-network-manage-peering.md#permissions)的帳戶登入[Azure 入口網站](https://portal.azure.com/)。
2. 在入口網站頂端包含「搜尋資源」文字的方塊中，輸入「虛擬網路」。 當搜尋結果中出現**虛擬網路**時加以選取。
3. 在出現的 [**虛擬網路**] 分頁中，選取您要為其建立對等互連的虛擬網路。
4. 在針對虛擬網路顯示的窗格中，選取 [**設定**] 區段中的 [**對等互連**]。
5. 選取對等互連並查看設定結果。

![檢查虛擬網路對等互連設定的選取專案](./media/virtual-network-configure-vnet-connections/4034496_en_1.png)
 
針對 Azure PowerShell，請執行命令[get-azurermvirtualnetworkpeering](https://docs.microsoft.com/powershell/module/azurerm.network/get-azurermvirtualnetworkpeering?view=azurermps-4.1.0)以取得虛擬網路對等互連。 以下為範例：

```
PS C:\Users\User1> Get-AzureRmVirtualNetworkPeering -VirtualNetworkName Vnet10-01 -ResourceGroupName dev-vnets
Name                             : LinkToVNET10-02
Id                               : /subscriptions/GUID/resourceGroups/dev-vnets/providers/Microsoft.Network/virtualNetworks/VNET10-01/virtualNetworkPeerings/LinkToVNET10-0
2
Etag                             : W/"GUID"
ResourceGroupName                : dev-vnets
VirtualNetworkName               : vnet10-01
ProvisioningState                : Succeeded
RemoteVirtualNetwork             : {
                                  "Id": "/subscriptions/GUID/resourceGroups/DEV-VNET
                                   s/providers/Microsoft.Network/virtualNetworks/VNET10-02"
                                   }
AllowVirtualNetworkAccess        : True
AllowForwardedTraffic            : False
AllowGatewayTransit              : False
UseRemoteGateways                : False
RemoteGateways                   : null
RemoteVirtualNetworkAddressSpace : null
```

### <a name="connect-a-resource-manager-virtual-network-to-another-resource-manager-virtual-network"></a>將 Resource Manager 虛擬網路連線到另一個 Resource Manager 的虛擬網路

您可以設定從一個 Resource Manager 虛擬網路到另一個 Resource Manager 虛擬網路的連接。 或者，您可以使用 IPsec 來設定連接。

### <a name="configure-a-vpn-connection-between-resource-manager-virtual-networks"></a>設定 Resource Manager 虛擬網路之間的 VPN 連線

若要在不含 IPsec 的 Resource Manager 虛擬網路之間設定連線，請參閱[使用 Azure 入口網站設定網路對網路 VPN 閘道聯](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal)機。

若要設定兩個 Resource Manager 虛擬網路之間的 IPsec 連線，請遵循在每個虛擬網路[的 Azure 入口網站中建立站對站](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal)連線中的步驟1到5。

> [!Note]
> 這些步驟只適用于相同訂用帳戶中的虛擬網路。 如果您的虛擬網路位於不同的訂用帳戶中，您必須使用 PowerShell 來進行連線。 請參閱 [PowerShell](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-vnet-vnet-rm-ps) 文件。

### <a name="validate-the-vpn-connection-between-resource-manager-virtual-networks"></a>驗證 Resource Manager 虛擬網路之間的 VPN 連線

![Azure Resource Manager 虛擬網路的傳統虛擬網路連線](./media/virtual-network-configure-vnet-connections/4034493_en_2.png)

若要確認已正確設定您的 VPN 連線，請遵循這些指示。

> [!Note] 
> 在這些步驟中，虛擬網路元件之後的數位會對應到上圖中的數位。

1. 請確定已連線的虛擬網路中沒有重迭的位址空間。
2. 確認已正確地在**連線物件**實例（4）中定義 Azure Resource Manager 虛擬網路（1）的位址範圍。
3. 確認已正確地在**連線物件**實例（3）中定義 Azure Resource Manager 虛擬網路（6）的位址範圍。
4. 確認預先共用金鑰符合連線物件。
5. 確認已正確地在**連線物件**實例（4）中定義 Azure Resource Manager 虛擬網路閘道 VIP （2）。
6. 確認已正確地在**連線物件**實例（3）中定義 Azure Resource Manager 虛擬網路閘道 VIP （5）。

### <a name="connect-a-classic-virtual-network-to-a-resource-manager-virtual-network"></a>將傳統虛擬網路連線至 Resource Manager 虛擬網路

您可以在不同訂用帳戶和不同區域中的虛擬網路之間建立連接。 您也可以將已連線至內部部署網路的虛擬網路連線，只要您已將閘道類型設定為路由型。

若要設定傳統虛擬網路與 Resource Manager 虛擬網路之間的連線，請參閱[使用 Azure 入口網站從不同的部署模型連接虛擬網路](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-connect-different-deployment-models-portal)。

![Azure Resource Manager 虛擬網路的傳統虛擬網路連線](./media/virtual-network-configure-vnet-connections/4034389_en_2.png)

若要在將傳統虛擬網路連線至 Azure Resource Manager 虛擬網路時檢查設定，請遵循這些指示。

> [!Note] 
> 在這些步驟中，虛擬網路元件之後的數位會對應到上圖中的數位。 

1. 請確定已連線的虛擬網路中沒有重迭的位址空間。
2. 確認在傳統區域網路定義（3）中，已正確定義 Azure Resource Manager 虛擬網路（6）的位址範圍。
3. 確認在 Azure Resource Manager**連線物件**實例（4）中，已正確定義傳統虛擬網路的位址範圍（1）。
4. 確認已在 Azure Resource Manager**連線物件**實例（4）中正確定義傳統虛擬網路閘道 VIP （2）。
5. 確認在傳統**區域網路定義**實例（3）中，已正確定義 Azure Resource Manager 虛擬網路閘道（5）。
6. 確認預先共用金鑰同時符合兩個已連線的虛擬網路：
   - 傳統虛擬網路：**區域網路定義**第
   - Azure Resource Manager 虛擬網路：**Connection 物件**4gb

## <a name="create-a-point-to-site-vpn-connection"></a>建立點對站 VPN 連線

點對站（下圖中的*P2S* ）設定可讓您建立從個別用戶端電腦到虛擬網路的安全連線。 當您想要從遠端位置（例如從家中或會議）連線到您的虛擬網路時，點對站連線會很有用。 當您只有幾個需要連線到虛擬網路的用戶端時，它們也很有用。 

點對站 VPN 連線會透過原生 Windows VPN 用戶端從用戶端電腦起始。 連線用戶端會使用憑證進行驗證。

![點對站連線](./media/virtual-network-configure-vnet-connections/4034387_en_3.png)

點對站連線不需要 VPN 裝置。 他們會透過安全通訊端通道通訊協定（SSTP）建立 VPN 連線。 您可以使用各種部署工具和部署模型，將點對站連線連接至虛擬網路：

* [使用 Azure 入口網站設定虛擬網路的點對站連線](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal)
* [使用 Azure 入口網站（傳統）設定虛擬網路的點對站連線](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-point-to-site-classic-azure-portal)
* [使用 PowerShell 設定虛擬網路的點對站連線](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps)

### <a name="validate-your-point-to-site-connection"></a>驗證您的點對站連線

疑難排解文章[：Azure 點對站連線問題](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems)會逐步解說點對站連接的常見問題。

## <a name="create-a-multisite-vpn-connection"></a>建立多網站 VPN 連線

您可以在已有站對站連線、點對站連線或網路對網路連線的虛擬網路上，新增站對站（*S2S* ，在下圖中）連接。 這種連接通常稱為*多*網站設定。 

![多站連接](./media/virtual-network-configure-vnet-connections/4034497_en_2.png)

Azure 目前適用于兩種部署模型：Resource Manager 和傳統。 這兩個模型彼此不完全相容。 若要使用不同的模型來設定多網站連線，請參閱下列文章：

* [將站對站連線新增至具有現有 VPN 閘道連線的虛擬網路](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-multi-site-to-site-resource-manager-portal)
* [將站對站連線新增至具有現有 VPN 閘道連線的虛擬網路（傳統）](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-multi-site)

> [!Note]
> 這些文章中的步驟不適用於 Azure ExpressRoute 和站對站並存連線設定。 如需詳細資訊，請參閱[ExpressRoute 和站對站並存連接](https://docs.microsoft.com/azure/expressroute/expressroute-howto-coexist-resource-manager)。

## <a name="configure-transit-routing"></a>設定傳輸路由

傳輸路由是一種特定的路由案例，可讓您在菊輪鍊拓撲中連接多個網路。 此路由可讓任何鏈結尾的虛擬網路中的資源透過之間的虛擬網路彼此通訊。 如果沒有傳輸路由，透過中樞對等互連的網路或裝置就無法彼此連接。

### <a name="configure-transit-routing-in-a-point-to-site-connection"></a>設定點對站連線中的傳輸路由

想像一下您想要在 VNetA 與 VNetB 之間設定站對站 VPN 連線的案例。 您也想要設定點對站 VPN，讓用戶端連線到 VNetA 的閘道。 然後，您想要啟用「點對站」用戶端的傳輸路由，以連線到 VNetB，而這會通過 VNetA。 

在 VNetA 與 VNetB 之間的站對站 VPN 上啟用 BGP 時，支援此案例。 如需詳細資訊，請參閱[關於點對站 VPN 路由](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-point-to-site-routing)。

### <a name="configure-transit-routing-in-an-expressroute-connection"></a>在 ExpressRoute 連線中設定傳輸路由

Azure ExpressRoute 可讓您透過連線提供者所提供的專用私人連線，將內部部署網路擴充至 Microsoft 雲端。 透過 ExpressRoute，您可以建立 Microsoft 雲端服務的連線，例如 Microsoft Azure、Office 365 和 Dynamics 365。 如需詳細資訊，請參閱[ExpressRoute 總覽](https://docs.microsoft.com/azure/expressroute/expressroute-introduction)。

![Azure 虛擬網路的 ExpressRoute 私人對等互連連線](./media/virtual-network-configure-vnet-connections/4034395_en_1.png)

> [!Note]
> 我們建議，如果 VNetA 和 VNetB 位於相同的地緣政治區域，您可以將[這兩個虛擬網路連結到 ExpressRoute 線路](https://docs.microsoft.com/azure/expressroute/expressroute-howto-linkvnet-arm)，而不是設定傳輸路由。 如果您的虛擬網路位於不同的地緣政治區域，您也可以直接將其連結至您的線路（如果您有[ExpressRoute Premium](https://docs.microsoft.com/azure/expressroute/expressroute-faqs#expressroute-premium)）。 

如果您有 ExpressRoute 和站對站共存，則不支援傳輸路由。 如需詳細資訊，請參閱[使用 PowerShell 設定 ExpressRoute 和站對站](https://docs.microsoft.com/azure/expressroute/expressroute-howto-coexist-resource-manager)。

如果您已啟用 ExpressRoute 將您的區域網路連線到 Azure 虛擬網路，您可以在想要擁有傳輸路由的虛擬網路之間啟用對等互連。 若要允許您的區域網路連線到遠端虛擬網路，您必須設定[虛擬網路對等互連](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview#gateways-and-on-premises-connectivity)。 

> [!Note]
> 虛擬網路對等互連僅適用于相同區域中的虛擬網路。

若要檢查您是否已設定虛擬網路對等互連的傳輸路由，請遵循下列指示：

1. 使用具有必要[角色和許可權](virtual-network-manage-peering.md#permissions)的帳戶登入[Azure 入口網站](https://portal.azure.com/)。
2. [建立 VNetA 與 VNetB 之間的對等互連，](https://docs.microsoft.com/azure/virtual-network/virtual-network-create-peering)如先前的圖表所示。 
3. 在針對虛擬網路顯示的窗格中，選取 [**設定**] 區段中的 [**對等互連**]。
4. 選取您想要查看的對等互連。 然後 **，選取 [設定]，以**驗證您已在連線到 expressroute 線路的 VNetA 網路上啟用 [**允許閘道傳輸**]，並在未連線到 expressroute 的遠端 VNetB 網路上**使用遠端閘道**專線.

### <a name="configure-transit-routing-in-a-virtual-network-peering-connection"></a>設定虛擬網路對等互連連線中的傳輸路由

當虛擬網路已對等互連時，您也可以將對等互連虛擬網路中的閘道設定為內部部署網路的傳輸點。 若要在虛擬網路對等互連中設定傳輸路由，請參閱[網路對網路](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-vnet-vnet-rm-ps?toc=/azure/virtual-network/toc.json)連線。

> [!Note]
> 透過不同部署模型建立的虛擬網路之間的對等互連關聯性不支援閘道傳輸。 對等互連關聯性中的兩個虛擬網路都必須透過 Resource Manager 建立，閘道傳輸才能正常執行。

若要檢查您是否已設定虛擬網路對等互連的傳輸路由，請遵循下列指示：

1. 使用具有必要[角色和許可權](virtual-network-manage-peering.md#permissions)的帳戶登入[Azure 入口網站](https://portal.azure.com/)。
2. 在入口網站頂端包含「搜尋資源」文字的方塊中，輸入「虛擬網路」。 當搜尋結果中出現**虛擬網路**時加以選取。
3. 在出現的 [**虛擬網路**] 分頁中，選取您要檢查對等互連設定的虛擬網路。
4. 在針對您選取的虛擬網路出現的窗格中，選取 [**設定**] 區段中的 [**對等互連**]。
5. 選取您想要查看的對等互連。 驗證您已啟用 [**允許閘道傳輸**] 和 [在**設定下** **使用遠端閘道**]。

![用於檢查您是否已為虛擬網路對等互連設定傳輸路由的選項](./media/virtual-network-configure-vnet-connections/4035414_en_1.png)

### <a name="configure-transit-routing-in-a-network-to-network-connection"></a>設定網路對網路連線中的傳輸路由

若要設定虛擬網路之間的傳輸路由，您必須使用 Resource Manager 部署模型和 PowerShell，在所有中繼網路對網路連線上啟用 BGP。 如需指示，請參閱[如何使用 PowerShell 在 AZURE VPN 閘道上設定 BGP](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-bgp-resource-manager-ps)。

透過 Azure VPN 閘道的傳輸流量可以透過傳統部署模型，但依賴網路設定檔中靜態定義的位址空間。 Azure 虛擬網路和 VPN 閘道尚未透過傳統部署模型支援 BGP。 若沒有 BGP，手動定義傳輸位址空間會造成錯誤，因此不建議您這麼做。

> [!Note]
> 您可以使用 Azure 傳統入口網站，或在傳統入口網站中使用網路設定檔來設定傳統網路對網路連線。 您無法透過 Azure Resource Manager 部署模型或 Azure 入口網站來建立或修改傳統虛擬網路。 如需傳統虛擬網路傳輸路由的詳細資訊，請參閱[Microsoft 開發人員的 blog](https://blogs.msdn.microsoft.com/igorpag/2015/10/01/hubspoke-daisy-chain-and-full-mesh-vnet-topologies-in-azure-arm-using-vpn-v1/)。

### <a name="configure-transit-routing-in-a-site-to-site-connection"></a>設定站對站連線中的傳輸路由

若要在內部部署網路與具有站對站連線的虛擬網路之間設定傳輸路由，您必須使用 Resource Manager 部署模型和 PowerShell，在所有中繼站對站連線上啟用 BGP。 如需相關指示，請參閱[如何使用 PowerShell 在 AZURE VPN 閘道上設定 BGP](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-bgp-resource-manager-ps) 。

透過 Azure VPN 閘道的傳輸流量可以透過傳統部署模型，但依賴網路設定檔中靜態定義的位址空間。 Azure 虛擬網路和 VPN 閘道尚未透過傳統部署模型支援 BGP。 若沒有 BGP，手動定義傳輸位址空間會造成錯誤，因此不建議您這麼做。

> [!Note]
> 您可以使用 Azure 傳統入口網站，或在傳統入口網站中使用網路設定檔來設定傳統站對站連線。 您無法透過 Azure Resource Manager 部署模型或 Azure 入口網站來建立或修改傳統虛擬網路。 如需傳統虛擬網路傳輸路由的詳細資訊，請參閱[Microsoft 開發人員的 blog](https://blogs.msdn.microsoft.com/igorpag/2015/10/01/hubspoke-daisy-chain-and-full-mesh-vnet-topologies-in-azure-arm-using-vpn-v1/)。

## <a name="configure-bgp-for-a-vpn-gateway"></a>設定適用於 VPN 閘道的 BGP

BGP 是網際網路上使用的標準路由通訊協定，可交換兩個或多個網路之間的路由和可連線性資訊。 當 BGP 在 Azure 虛擬網路的內容中使用時，它會啟用 Azure VPN 閘道和您的內部部署 VPN 裝置（稱為 BGP 對等互連或鄰近專案）。 它們會交換「路由」，以通知這兩個閘道的可用性和可連線性，讓這些前置詞通過所涉及的閘道或路由器。 

BGP 也可以藉由將 BGP 閘道從一個 BGP 對等互連學習到的所有其他 BGP 對等互連，來啟用多個網路之間的傳輸路由。 如需詳細資訊，請參閱[BGP 與 Azure VPN 閘道的總覽](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-bgp-overview)。

### <a name="configure-bgp-for-a-vpn-connection"></a>設定 VPN 連線的 BGP

若要設定使用 BGP 的 VPN 連線，請參閱[如何使用 PowerShell 在 AZURE VPN 閘道上設定 BGP](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-bgp-resource-manager-ps)。

藉由為其建立自發系統（AS），在虛擬網路閘道上啟用 BGP。 基本閘道不支援 BGP。 若要檢查閘道的 SKU，請移至 Azure 入口網站中 [ **VPN 閘道**] 分頁的 [**總覽**] 區段。 如果您的 SKU 是**基本**的，您必須變更 sku （請參閱[調整閘道大小](https://docs.microsoft.com/powershell/module/azurerm.network/resize-azurermvirtualnetworkgateway?view=azurermps-4.1.0&viewFallbackFrom=azurermps-4.0.0)）為**VpnGw1**。 

檢查 SKU 會導致20到30分鐘的停機時間。 一旦閘道具有正確的 SKU，您就可以使用[Reset-azurermvirtualnetworkgateway](https://docs.microsoft.com/powershell/module/azurerm.network/set-azurermvirtualnetworkgateway?view=azurermps-3.8.0) PowerShell commandlet 來新增 as 號碼。 在您設定 AS 號碼之後，將會自動提供閘道的 BGP 對等 IP。

您必須使用 AS `LocalNetworkGateway`號碼和 BGP 對等位址來手動提供。 您可以使用`ASN` [set-azurermlocalnetworkgateway](https://docs.microsoft.com/powershell/module/azurerm.network/new-azurermlocalnetworkgateway?view=azurermps-4.1.0)或`-BgpPeeringAddress` [set-azurermlocalnetworkgateway](https://docs.microsoft.com/powershell/module/azurerm.network/set-azurermlocalnetworkgateway?view=azurermps-4.1.0) PowerShell commandlet 來設定和值。 有些 AS 數位會保留給 Azure，而且您無法如[關於 BGP 與 Azure VPN 閘道](../vpn-gateway/vpn-gateway-bgp-overview.md#faq)中所述使用它們。

連線物件必須啟用 BGP。 您可以透過`-EnableBGP` [New-AzureRmVirtualNetworkGatewayConnection](https://docs.microsoft.com/powershell/module/azurerm.network/new-azurermvirtualnetworkgatewayconnection?view=azurermps-4.1.0)或 [Set-AzureRmVirtualNetworkGatewayConnection](https://docs.microsoft.com/powershell/module/azurerm.network/set-azurermvirtualnetworkgatewayconnection?view=azurermps-4.1.0) 將值設定為。`$True`

### <a name="validate-the-bgp-configuration"></a>驗證 BGP 設定

若要檢查是否已正確設定 BGP，您可以執行`get-AzureRmVirtualNetworkGateway`和`get-AzureRmLocalNetworkGateway` commandlet。 然後您會注意到部分中的`BgpSettingsText` BGP 相關輸出。 例如:

```
{

"Asn": AsnNumber,

"BgpPeeringAddress": "IP address",

"PeerWeight": 0

}
```

## <a name="create-a-highly-available-activeactive-vpn-connection"></a>建立高可用性的主動/主動 VPN 連線

主動/主動和主動/待命閘道之間的主要差異如下：

* 您必須建立兩個具有兩個公用 IP 位址的閘道 IP 設定。
* 您必須設定**EnableActiveActiveFeature**旗標。
* 閘道 SKU 必須是**VpnGw1**、 **VpnGw2**或**VpnGw3**。

若要達到跨單位和網路對網路連線的高可用性，您應該部署多個 VPN 閘道，並在您的網路與 Azure 之間建立多個平行連接。 如需連線能力選項和拓撲的總覽，請參閱[高可用性跨單位和網路對網路連線能力](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-highlyavailable)。

若要建立主動/主動跨單位和網路對網路連線，請依照[使用 AZURE vpn 閘道設定主動/主動 S2S VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-activeactive-rm-powershell)連線中的指示，將 azure vpn 閘道設定為主動/主動模式。

> [!Note]  
> * 當您將位址新增到局域網路閘道以進行已啟用 BGP 的主動/主動模式時，請*只新增 bgp 對等的/32 位址*。 如果您新增更多位址，則會將它們視為靜態路由，並優先于 BGP 路由。
> * 您必須針對連線到 Azure 的內部部署網路使用不同的 BGP AS 號碼。 如果您的內部部署 VPN 裝置已經使用 ASN 來與其他 BGP 鄰近專案進行對等互連，則您必須將虛擬網路變更為 [數位]。

## <a name="change-an-azure-vpn-gateway-type-after-deployment"></a>在部署後變更 Azure VPN 閘道類型

您無法將 Azure 虛擬網路閘道類型從以原則為基礎的變更為路由式或其他方式。 您必須先刪除閘道。 之後，就不會保留 IP 位址和預先共用金鑰。 接著，您可以建立所需類型的新閘道。 

若要刪除並建立閘道，請遵循下列步驟：

1. 刪除與原始閘道相關聯的任何連接。
2. 使用 Azure 入口網站、PowerShell 或傳統 PowerShell 來刪除閘道： 
   * [使用 Azure 入口網站刪除虛擬網路閘道](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-delete-vnet-gateway-portal)
   * [使用 PowerShell 刪除虛擬網路閘道](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-delete-vnet-gateway-powershell)
   * [使用 PowerShell 刪除虛擬網路閘道（傳統）](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-delete-vnet-gateway-classic-powershell)
3. 依照[建立 VPN 閘道](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md#VNetGateway)中的步驟來建立所需類型的新閘道，並完成 VPN 設定。

> [!Note]
> 此程式大約需要60分鐘的時間。

## <a name="next-steps"></a>後續步驟

* [為 Azure VM 之間的連線問題疑難排解](https://docs.microsoft.com/azure/virtual-network/virtual-network-troubleshoot-connectivity-problem-between-vms)

