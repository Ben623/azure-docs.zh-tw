---
title: Azure 虛擬網路的 IPv6 總覽（預覽）
titlesuffix: Azure Virtual Network
description: Ipv6 端點的 IPv6 描述和 Azure 虛擬網路中的資料路徑。
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.workload: infrastructure-services
ms.date: 12/19/2019
ms.author: kumud
ms.openlocfilehash: 9214886f468a4a052328a99289845361a059b650
ms.sourcegitcommit: 5b073caafebaf80dc1774b66483136ac342f7808
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/09/2020
ms.locfileid: "75780074"
---
# <a name="what-is-ipv6-for-azure-virtual-network-preview"></a>什麼是適用于 Azure 虛擬網路的 IPv6？ (預覽)

適用于 Azure 虛擬網路（VNet）的 IPv6 可讓您在 Azure 中裝載應用程式，並在虛擬網路內和網際網路之間使用 IPv6 和 IPv4 連線能力。 由於公用 IPv4 位址耗盡，行動性和物聯網（IoT）的新網路通常是以 IPv6 為基礎。 即使是長期建立的 ISP 和行動網路也會轉換為 IPv6。 僅適用于 IPv4 的服務在現有和新興市場中都可以真正的缺點尋找自己。 雙重堆疊 IPv4/IPv6 連線能力可讓 Azure 託管的服務利用全球可用的雙堆疊服務，輕鬆地與現有的 IPv4 和這些新的 IPv6 裝置和網路連線，以跨越這項技術缺口。

Azure 的原始 IPv6 連線能力可讓您輕鬆地為裝載于 Azure 中的應用程式提供雙重堆疊（IPv4/IPv6）網際網路連線能力。 它可讓您使用負載平衡的 IPv6 連線，針對輸入和輸出起始的連線進行簡單的 Vm 部署。 這項功能仍可供使用，並可在[這裡](../load-balancer/load-balancer-ipv6-overview.md)取得詳細資訊。
Azure 虛擬網路的 IPv6 有更完整的功能，可讓您在 Azure 中部署完整的 IPv6 解決方案架構。

> [!Important]
> 適用于 Azure 虛擬網路的 IPv6 目前為公開預覽版。 此預覽版是在沒有服務等級協定的情況下提供，不建議用於生產工作負載。 可能不支援特定功能，或可能已經限制功能。 如需詳細資訊，請參閱 [Microsoft Azure 預覽專用的補充使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

下圖說明 Azure 中的簡單雙重堆疊（IPv4/IPv6）部署：

![IPv6 網路部署圖表](./media/ipv6-support-overview/ipv6-sample-diagram.png)

## <a name="benefits"></a>優勢

適用于 Azure VNET 的 IPv6 權益：

- 協助將 Azure 託管應用程式的範圍擴充到不斷成長的行動和物聯網市場。
- 雙堆疊的 IPv4/IPv6 Vm 提供最大的服務部署彈性。 單一服務實例可以與支援 IPv4 和 IPv6 的網際網路用戶端連接。
- 建置於長時間建立、穩定的 Azure VM 對網際網路 IPv6 連線能力。
- 預設是安全的，因為只有在您的部署中明確要求時，才會建立網際網路的 IPv6 連線能力。

## <a name="capabilities"></a>容量

適用于 Azure VNet 的 IPv6 包含下列功能：

- Azure 客戶可以定義自己的 IPv6 虛擬網路位址空間，以符合其應用程式、客戶或緊密整合到其內部部署 IP 空間的需求。
- 雙重堆疊（IPv4 和 IPv6）具有雙重堆疊子網的虛擬網路，可讓應用程式與虛擬網路或網際網路中的 IPv4 和 IPv6 資源連接。
    > [!IMPORTANT]
    > IPv6 的子網的大小必須完全為/64。  這可確保在您決定啟用子網到內部部署網路的路由時，未來的相容性，因為某些路由器只能接受/64 IPv6 路由。  
- 使用網路安全性群組的 IPv6 規則來保護您的資源。
    - Azure 平臺的分散式阻斷服務（DDoS）保護會延伸到網際網路對向的公用 IP
- 使用使用者定義的路由來自訂虛擬網路中的 IPv6 流量路由，特別是在利用網路虛擬裝置來擴充您的應用程式時。
- Linux 和 Windows 虛擬機器都可以使用適用于 Azure VNET 的 IPv6
- [標準 IPv6 公用 Load Balancer](virtual-network-ipv4-ipv6-dual-stack-standard-load-balancer-powershell.md)支援來建立具有彈性、可擴充的應用程式，包括：
    - 選擇性的 IPv6 健全狀況探查，用來判斷哪個後端集區實例是健全狀況，因此可以接收新的流程。
    - 選擇性輸出規則，可提供對輸出連線能力的完整宣告式控制，以調整並微調此功能以滿足您的特定需求。
    - 選擇性多個前端設定，可讓單一負載平衡器使用多個 IPv6 公用 IP 位址-相同的前端通訊協定和埠可以跨前端位址重複使用。
    - 選擇性 IPv6 埠可以使用負載平衡規則的*浮動 IP*功能，在後端實例上重複使用 
- [標準 IPv6 內部 Load Balancer](ipv6-dual-stack-standard-internal-load-balancer-powershell.md)支援，可在 Azure vnet 中建立復原的多層式應用程式。  
- 基本 IPv6 公用 Load Balancer 與舊版部署的相容性支援
- [保留的 Ipv6 公用 IP 位址和位址範圍](ipv6-public-ip-address-prefix.md)提供穩定且可預測的 ipv6 位址，可讓您的公司和客戶輕鬆允許清單您的 azure 託管應用程式。
- 實例層級的公用 IP 可直接對個別 Vm 提供 IPv6 網際網路連線能力。
- [將 Ipv6 新增至現有的僅 ipv4 部署](ipv6-add-to-existing-vnet-powershell.md)-這項功能可讓您輕鬆地將 ipv6 連線新增至現有的僅 ipv4 部署，而不需要重新建立部署。  在此程式中，IPv4 網路流量不會受到影響，因此，視您的應用程式和作業系統而定，您可以將 IPv6 新增到 live 服務。    
- 讓網際網路用戶端使用其選擇的通訊協定，順暢地存取雙重堆疊應用程式，並 Azure DNS IPv6 （AAAA）記錄的支援。 
- 建立雙重堆疊應用程式，以使用 IPv6 的虛擬機器擴展集自動調整為您的負載。
- [虛擬網路（VNET）對等互連](virtual-network-peering-overview.md)-區域內和全域對等互連-可讓您完美地 connect 雙重堆疊 vnet-對等互連網路中 vm 上的 IPv4 和 IPv6 端點都能夠彼此通訊。 當您將部署轉換成雙重堆疊時，甚至可以使用僅限 IPv4 的 Vnet 對等互連雙重堆疊。 
- IPv6 疑難排解和診斷適用于負載平衡器計量/警示和網路監看員功能，例如封包捕獲、NSG 流量記錄、連線疑難排解和連線監視。   

## <a name="scope"></a>範圍
適用于 Azure VNET 的 IPv6 是基礎功能集，可讓客戶在 Azure 中裝載雙重堆疊（IPv4 + IPv6）應用程式。  我們想要在一段時間後將 IPv6 支援新增至更多的 Azure 網路功能，並最終提供 Azure PaaS 服務的雙堆疊版本，但在此同時，所有 Azure PaaS 服務都可透過雙重堆疊虛擬機器的 IPv4 端點來存取。   

## <a name="limitations"></a>限制
目前的 IPv6 for Azure 虛擬網路版本具有下列限制：
- 適用于 Azure 虛擬網路的 IPv6 （預覽）可在所有全球 Azure 區域中使用，但僅限於全球 Azure （不在政府雲端中）。
- ExpressRoute 和 VPN 閘道不能在啟用 IPv6 的 VNET 中使用，不論是直接或使用 "Useremotegateway 不得" 對等互連。 
- Azure 平臺（AKS 等）不支援適用于容器的 IPv6 通訊。  

## <a name="pricing"></a>定價

IPv6 Azure 資源和頻寬的收費方式與 IPv4 的費率相同。 IPv6 沒有額外或不同的費用。 您可以在 [[公用 IP 位址](https://azure.microsoft.com/pricing/details/ip-addresses/)]、[[網路頻寬](https://azure.microsoft.com/pricing/details/bandwidth/)] 或 [ [Load Balancer](https://azure.microsoft.com/pricing/details/load-balancer/)] 中找到定價的詳細資料。

## <a name="next-steps"></a>後續步驟

- 瞭解如何[使用 Azure PowerShell 部署 IPv6 雙重堆疊應用程式](virtual-network-ipv4-ipv6-dual-stack-standard-load-balancer-powershell.md)。
- 瞭解如何[使用 Azure CLI 部署 IPv6 雙重堆疊應用程式](virtual-network-ipv4-ipv6-dual-stack-standard-load-balancer-cli.md)。
- 瞭解如何[使用 Resource Manager 範本（JSON）部署 IPv6 雙重堆疊應用程式](ipv6-configure-standard-load-balancer-template-json.md)
