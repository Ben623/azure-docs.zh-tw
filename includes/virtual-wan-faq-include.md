---
title: 包含檔案
description: 包含檔案
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: include
ms.date: 10/17/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 0ba69715998f110d88015bdba2fbf340a6b64089
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/04/2019
ms.locfileid: "73491648"
---
### <a name="what-is-the-difference-between-an-azure-virtual-network-gateway-vpn-gateway-and-an-azure-virtual-wan-vpn-gateway"></a>Azure 虛擬網路閘道 (VPN 閘道) 與 Azure 虛擬 WAN VPN 閘道之間有何差異？

虛擬 WAN 提供大規模站對站連線，而且是針對輸送量、延展性和易用性而建置的。 當您將網站連線到虛擬 WAN VPN 閘道時，它與使用閘道類型「VPN」的一般虛擬網路閘道不同。 同樣地，當您將 ExpressRoute 線路連線到虛擬 WAN 中樞時，它會使用與使用閘道類型「ExpressRoute」的一般虛擬網路閘道不同的 ExpressRoute 閘道資源。 虛擬 WAN 為 VPN 和 ExpressRoute 最多支援 20 Gbps 的彙總輸送量。 虛擬 WAN 也有自動化功能，可與 CPE 分支裝置夥伴的生態系統建立連線。 CPE 分支裝置有內建的自動化功能，可自動佈建和連線至 Azure 虛擬 WAN。 這些裝置都可以從 SD-WAN 和 VPN 合作夥伴日益成長的生態系統中取用。 請參閱[慣用的夥伴清單](../articles/virtual-wan/virtual-wan-locations-partners.md)。

### <a name="how-is-virtual-wan-different-from-an-azure-virtual-network-gateway"></a>虛擬 WAN 與 Azure 虛擬網路閘道有何不同？

虛擬網路閘道 VPN 限定為 30 個通道。 對於連線，您應將虛擬 WAN 用於大規模的 VPN。 每個區域最多可連線 1000 個分支連線 (虛擬中樞)，每個中樞有 20 Gbps 的彙總。 連線是從內部部署 VPN 裝置到虛擬中樞的主動-主動通道。 您可以在每個區域都有一個中樞，這表示您可以跨中樞連線到超過 1,000 個分支。

### <a name="which-device-providers-virtual-wan-partners-are-supported"></a>啟動時支援哪些裝置提供者 (虛擬 WAN 合作夥伴)？

目前有許多合作夥伴支援完全自動化的虛擬 WAN 體驗。 如需詳細資訊，請參閱[虛擬 WAN 合作夥伴](../articles/virtual-wan/virtual-wan-locations-partners.md)。 

### <a name="what-are-the-virtual-wan-partner-automation-steps"></a>虛擬 WAN 合作夥伴自動化步驟是什麼？

若要了解合作夥伴自動化步驟，請參閱[虛擬 WAN 合作夥伴自動化](../articles/virtual-wan/virtual-wan-configure-automation-providers.md)。

### <a name="am-i-required-to-use-a-preferred-partner-device"></a>我是否必須使用偏好的合作夥伴裝置？

沒有。 您可以使用任何支援 VPN 且符合 IKEv2/IKEv1 IPsec 支援需求的裝置。

### <a name="how-do-virtual-wan-partners-automate-connectivity-with-azure-virtual-wan"></a>虛擬 WAN 合作夥伴如何將與 Azure 虛擬 WAN 的連線自動化？

軟體定義的連線解決方案通常會使用控制器或裝置佈建中心，來管理它們的分支裝置。 控制器可以使用 Azure API，將與 Azure 虛擬 WAN 的連線自動化。 如需詳細資訊，請參閱虛擬 WAN 夥伴自動化。

軟體定義的連線解決方案通常會使用控制器或裝置佈建中心，來管理它們的分支裝置。 控制器可以使用 Azure API，將與 Azure 虛擬 WAN 的連線自動化。 自動化包括上傳分支資訊、下載 Azure 設定、將 IPSec 通道設定到 Azure 虛擬中樞，以及自動設定從分支裝置到 Azure 虛擬 WAN 的連線。 當您有數百個分支時，使用虛擬 WAN CPE 夥伴進行連線很方便，因為上線體驗可讓您不需要安裝、設定及管理大規模的 IPsec 連線。 如需詳細資訊，請參閱[虛擬 WAN 合作夥伴自動化](../articles/virtual-wan/virtual-wan-configure-automation-providers.md)。

### <a name="am-i-required-to-use-a-preferred-partner-device"></a>我是否必須使用偏好的合作夥伴裝置？

沒有。 您可以使用任何支援 VPN 且符合 IKEv2/IKEv1 IPsec 支援需求的裝置。

### <a name="how-is-virtual-wan-supporting-sd-wan-devices"></a>虛擬 WAN 如何支援 SD-WAN 裝置？

虛擬 WAN 合作夥伴會將 IPsec 連線到 Azure VPN 端點自動化。 如果虛擬 WAN 合作夥伴是 SD WAN 提供者，那就表示 SD-WAN 控制器會管理自動化和 IPsec 與 Azure VPN 端點的連線。 如果 SD-WAN 裝置的任何專用 SD-WAN 功能需要它自己的端點，而不是 Azure VPN，您可以在 Azure VNet 中部署 SD-WAN 端點，並與 Azure 虛擬 WAN 並存。

### <a name="does-virtual-wan-change-any-existing-connectivity-features"></a>虛擬 WAN 是否會變更任何現有的連線功能？

現有的 Azure 連線功能不會有任何變更。

### <a name="are-there-new-resource-manager-resources-available-for-virtual-wan"></a>虛擬 WAN 是否有任何新的 Resource Manager 資源可用？
  
是，虛擬 WAN 導入了新的 Resource Manager 資源。 如需詳細資訊，請參閱[概觀](../articles/virtual-wan/virtual-wan-about.md)。

### <a name="how-many-vpn-devices-can-connect-to-a-single-hub"></a>有多少部 VPN 裝置可以連線到單一中樞？

每個虛擬中樞支援多達 1,000 個連線。 每個連線是由四個連結組成，每個連結連線支援兩個其設定為主動-主動的通道。 通道會在 Azure 虛擬中樞 vpngateway 內終止。

### <a name="can-the-on-premises-vpn-device-connect-to-multiple-hubs"></a>內部部署 VPN 裝置是否可以連線到多個中樞？

是。 開始時的流量會從內部部署裝置流至最近的 Microsoft 網路邊緣，然後流至虛擬中樞。

### <a name="can-i-deploy-and-use-my-favorite-network-virtual-appliance-in-an-nva-vnet-with-azure-virtual-wan"></a>是否可以使用 Azure 虛擬 WAN 部署及使用我的最愛網路虛擬設備 (在 NVA VNet 中)？

是，您可以將您最愛的網路虛擬設備 (NVA) VNet 連線到 Azure 虛擬 WAN。 首先，使用中樞虛擬網路連線將網路虛擬設備 VNet 連線到中樞。 接著，建立包含指向虛擬設備之下一個躍點的虛擬中樞路由。 您可以將多個路由套用到虛擬中樞路由表。 連線到 NVA VNet 的任何輪輻還必須連線到虛擬中樞，以確保輪輻 VNet 路由會傳播到內部部署系統。

### <a name="can-i-create-a-network-virtual-appliance-inside-the-virtual-hub"></a>我可以在虛擬中樞內建立網路虛擬設備嗎？

無法在虛擬中樞內部署網路虛擬設備 (NVA)。 但是，您可以在連線到虛擬中樞的輪輻 VNet 中建立該設備，並啟用中樞內的路由，以透過 (NIC 的) NVA IP 位址為目的地 VNet 導入流量。

### <a name="can-a-spoke-vnet-have-a-virtual-network-gateway"></a>輪幅 VNet 是否可以有虛擬網路閘道？

沒有。 如果輪幅 VNet 連線到虛擬中樞，則不能有虛擬網路閘道。

### <a name="is-there-support-for-bgp"></a>是否支援 BGP？

是，有支援 BGP。 當您建立 VPN 網站時，可以在其中提供 BGP 參數。 這表示為在 Azure 中該網站建立的任何連線將會針對 BGP 啟用。 此外，如果您有具備 NVA 的 Vnet，而且如果此 NVA VNet 已連結至虛擬 WAN 中樞，為了確保來自 NVA VNet 的路由會適當地公告，連結至 NVA VNet 的支點必須停用 BGP。 此外，請將這些支點 VNet 連線到虛擬中樞 VNet，以確保支點 VNet 路由會傳播到內部部署系統。

### <a name="can-i-direct-traffic-using-udr-in-the-virtual-hub"></a>是否可以使用虛擬中樞的 UDR 來引導流量？

是，您可以使用虛擬中樞路由表將流量導向 VNet。 這可讓您透過特定的 IP 位址 (通常是 NVA NIC 的 IP 位址)，為 Azure 中的目的地 VNet 設定路由。

### <a name="is-there-any-licensing-or-pricing-information-for-virtual-wan"></a>虛擬 WAN 是否有任何授權或價格資訊？

是。 請參閱[定價](https://azure.microsoft.com/pricing/details/virtual-wan/)頁面。

### <a name="how-do-i-calculate-price-of-a-hub"></a>如何計算中樞的價格？

* 您會為中樞內的服務支付費用。 例如，假設您有 10 個分支或內部部署裝置需要連線至 Azure 虛擬 WAN，表示連線至中樞內的 VPN 端點。 假設這是 1 縮放單位 = 500 Mbps 的 VPN，則會以每小時 0.361 美元計費。 每個連線以每小時 0.05 美元計費。 針對 10 個連線，每小時的服務總費用是每小時 0.361 + 5 美元。 適用離開 Azure 的流量資料費用。

* 中樞費用另計。 請參閱[定價](https://azure.microsoft.com/pricing/details/virtual-wan/)頁面。

* 如果由於 ExpressRoute 線路連線到虛擬中樞，而擁有 ExpressRoute 閘道，則您需要支付縮放單位的費用。 ER 中的每個縮放單位均為 2 Gbps，每個連線單位的費用與 VPN 連線單位相同。

### <a name="how-do-new-partners-that-are-not-listed-in-your-launch-partner-list-get-onboarded"></a>未列在產品發佈合作夥伴清單中的新合作夥伴要如何上線？

所有虛擬 WAN API 都是開放 API。 您可以查看說明文件以評估技術可行性。 如果有任何問題，請傳送電子郵件至 azurevirtualwan@microsoft.com。 理想的合作夥伴是有裝置可用來佈建 IKEv1 或 IKEv2 IPSec 連線的合作夥伴。

### <a name="what-if-a-device-i-am-using-is-not-in-the-virtual-wan-partner-list-can-i-still-use-it-to-connect-to-azure-virtual-wan-vpn"></a>如果我使用的裝置不在虛擬 WAN 合作夥伴清單中又該如何？ 我仍可用它來連線到 Azure 虛擬 WAN VPN 嗎？

是的，只要裝置支援 IPsec IKEv1 或 IKEv2 即可。 虛擬 WAN 合作夥伴協力會將裝置到 Azure VPN 端點的連線自動化。 這表示自動化的步驟，例如「分支資訊上傳」、「IPsec 與組態」和「連線」。由於您的裝置不是來自虛擬 WAN 合作夥伴生態系統，因此您必須執行繁重的工作，以手動方式取得 Azure 組態，並更新您的裝置以設定 IPsec 連線。

### <a name="is-it-possible-to-construct-azure-virtual-wan-with-a-resource-manager-template"></a>是否可以使用 Resource Manager 範本來建構 Azure 虛擬 WAN？

可以使用[快速入門範本](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Network)來建立一個簡易設定的虛擬 WAN，其中包含一個中樞和一個 vpnsite。 虛擬 WAN 主要是 REST 或入口網站驅動的服務。

### <a name="is-global-vnet-peering-supported-with-azure-virtual-wan"></a>Azure 虛擬 WAN 是否支援全域 VNet 對等互連？ 

您可以在與虛擬 WAN 不同的區域中連線 VNet。

### <a name="can-spoke-vnets-connected-to-a-virtual-hub-communicate-with-each-other"></a>連線到虛擬中樞的輪輻 VNet 之間，是否可以彼此通訊？

是。 輪輻 VNet 可以直接透過虛擬網路對等互連進行通訊。 如需詳細資訊，請參閱[虛擬網路對等互連](../articles/virtual-network/virtual-network-peering-overview.md)。 我們也支援 VNet 透過中樞以轉移方式進行通訊。

### <a name="what-is-a-branch-connection-to-azure-virtual-wan"></a>什麼是 Azure 虛擬 WAN 的分支連線？

從分支裝置到 Azure 虛擬 WAN 的連線最多支援四個連結。 連結是分支位置的實體連線連結 (例如：ATT、Verizon 等)。 每個連結連線都是由兩個主動/主動 IPsec 通道所組成。

### <a name="is-branch-to-branch-connectivity-allowed-in-virtual-wan"></a>虛擬 WAN 中是否允許分支對分支連線？

是，在 VPN 的虛擬 WAN 與 VPN 對 ExpressRoute 中可以使用分支對分支連線。

### <a name="does-branch-to-branch-traffic-traverse-through-the-azure-virtual-wan"></a>分支對分支流量是否會透過 Azure 虛擬 WAN 而周遊？

是。

### <a name="does-virtual-wan-require-expressroute-from-each-site"></a>虛擬 WAN 是否需要來自每個網站的 ExpressRoute？

否，此虛擬 WAN 不需要來自每個網站的 ExpressRoute。 它會透過從裝置到 Azure 虛擬 WAN 中樞的網際網路連結，使用標準 IPsec 站對站連線。 您的網站可以使用 ExpressRoute 線路連線到提供者網路。 對於虛擬中樞內使用 ExpressRoute 來連線的網站，它們可以具有分支，以在 VPN 與 ExpressRoute 之間將流量分流。

### <a name="is-there-a-network-throughput-limit-when-using-azure-virtual-wan"></a>使用 Azure 虛擬 WAN 時是否有網路輸送量限制？

每個中樞/區域的分支數目限制為 1000 個連線，且中樞內的合併連線速度限制為 20 Gbps。 每個區域可以有 1 個中樞。

### <a name="how-many-vpn-connections-does-a-virtual-wan-hub-support"></a>虛擬 WAN 中樞支援多少個 VPN 連線？

Azure 虛擬 WAN 中樞最多可同時支援 1,000 個 S2S 連線、10,000 個 P2S 連線，和 4 個 ExpressRoute 連線。

### <a name="what-is-the-total-vpn-throughput-of-a-vpn-tunnel-and-a-connection"></a>一個 VPN 通道和一個連線的 VPN 輸送量總計是多少？

根據所選的縮放單位，一個中樞的 VPN 輸送量總計最多可達 20 Gbps。 輸送量會由所有現有的連線共用。 連線中的每個通道最多可支援 1 Gbps。

### <a name="i-dont-see-the-20-gbps-setting-for-the-virtual-hub-in-the-portal-how-do-i-configure-that"></a>我在入口網站中看不到虛擬中樞的 20 Gbps 設定。 我要如何設定？

在入口網站中，導覽至中樞內的 VPN 閘道，並按一下縮放單位，以將其變更為適當的設定。

### <a name="does-virtual-wan-allow-the-on-premises-device-to-utilize-multiple-isps-in-parallel-or-is-it-always-a-single-vpn-tunnel"></a>虛擬 WAN 是否允許內部部署裝置以平行方式使用多個 ISP，還是一律為單一 VPN 通道？

使用分支的可用連結進入虛擬 WAN VPN 的連線，一律為主動-主動通道 (適用於相同中樞/區域內的復原)。 此連結可能是在內部部署分支的 ISP 連結。 虛擬 WAN「VPNSite」提供將連結資訊新增至網站的功能。 如果您在分支有多個 ISP，而且每個 ISP 提供一個連結，則可在 Azure 中的 VPN 網站資訊中設定該資訊。 不過，在分支中跨 ISP 管理容錯移轉完全是以分支為中心的路由作業。

### <a name="what-is-global-transit-architecture"></a>什麼是全域傳輸架構？

如需全域傳輸架構的詳細資訊，請參閱[全域傳輸網路架構和虛擬 WAN](../articles/virtual-wan/virtual-wan-global-transit-network-architecture.md)。

### <a name="how-is-traffic-routed-on-the-azure-backbone"></a>流量在 Azure 骨幹上的路由傳送方式為何？

流量會依循下列模式：分支裝置->ISP->Microsoft 網路邊緣->Microsoft DC (中樞 VNet)->Microsoft 網路邊緣->ISP->分支裝置

### <a name="in-this-model-what-do-you-need-at-each-site-just-an-internet-connection"></a>在此模型中，每個站台需要什麼？ 有網際網路連線就可以嗎？

是。 支援 IPsec 的網際網路連線與實體裝置，最好是我們的整合式[虛擬 WAN 夥伴](../articles/virtual-wan/virtual-wan-locations-partners.md)所提供的。 或者，您可以從慣用的裝置手動管理 Azure 的設定與連線。

### <a name="how-do-i-enable-default-route-00000-in-a-connection-vpn-expressroute-or-virtual-network"></a>如何在連線 (VPN、ExpressRoute 或虛擬網路) 中啟用預設路由 (0.0.0.0/0)：

如果連線上的旗標為「啟用」，則虛擬中樞可以將學習到的預設路由傳播到虛擬網路/站對站 VPN/ExpressRoute 連線。 當使用者編輯虛擬網路連線、VPN 連線或 ExpressRoute 連線時，此旗標為可見。 根據預設，當網站或 ExpressRoute 線路連線至中樞時，會停用此旗標。 新增虛擬網路連線以將 VNet 連線到虛擬中樞時，依預設會啟用旗標。 預設路由不是源自於虛擬 WAN 中樞；如果虛擬 WAN 中樞由於在中樞內部署防火牆而學習預設路由，或其他連線的網站已啟用強制通道，則會傳播預設路由。

### <a name="what-are-the-differences-between-the-virtual-wan-types-basic-and-standard"></a>虛擬 WAN 類型 (基本和標準) 之間有何差異？

「基本」WAN 類型可讓您建立基本中樞 (SKU = 基本)。 「標準」WAN 類型可讓您建立標準中樞 (SKU = 標準)。 基本中樞僅限於站對站 VPN 功能。 標準中樞可讓您透過中樞擁有 ExpressRoute、使用者 VPN (P2S)、完整網狀中樞和 VNet 對 VNet 的傳輸。 您必須為標準中樞支付每小時 0.25 美元的基本費用，以及在 VNet 對 VNet 連線期間，透過中樞傳輸的資料處理費用，以及中樞對中樞流量的資料處理費用。 如需詳細資訊，請參閱[基本和標準虛擬 WAN](../articles/virtual-wan/virtual-wan-about.md#basicstandard)。 如需價格資訊，請參閱 [價格](https://azure.microsoft.com/pricing/details/virtual-wan/) 頁面。
