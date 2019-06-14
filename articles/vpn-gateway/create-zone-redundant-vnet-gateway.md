---
title: 在 Azure 可用性區域中建立區域備援虛擬網路閘道 | Microsoft Docs
description: 在可用性區域中部署 VPN 閘道和 ExpressRoute 閘道
services: vpn-gateway
author: cherylmc
Customer intent: As someone with a basic network background, I want to understand how to create zone-redundant gateways.
ms.service: vpn-gateway
ms.topic: article
ms.date: 04/26/2019
ms.author: cherylmc
ms.openlocfilehash: 209c4deec2863de21362ab69a7f1d372921ac147
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "64575554"
---
# <a name="create-a-zone-redundant-virtual-network-gateway-in-azure-availability-zones"></a>在 Azure 可用性區域中建立區域備援虛擬網路閘道

您可以在 Azure 可用性區域中部署 VPN 和 ExpressRoute 閘道。 此方式可為虛擬網路閘道帶來復原力、延展性和更高的可用性。 在 Azure 可用性區域中部署閘道可從根本上和邏輯上分隔區域內的閘道，同時還能在發生區域層級的失敗時，保護您內部部署項目與 Azure 的網路連線。 如需詳細資訊，請參閱[關於區域備援虛擬網路閘道](about-zone-redundant-vnet-gateways.md)和[關於 Azure 可用性區域](../availability-zones/az-overview.md)。

## <a name="before-you-begin"></a>開始之前

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

您可以使用安裝在本機電腦的 PowerShell 或 Azure Cloud Shell。 如果您選擇在本機安裝和使用 PowerShell，則需要最新版的 PowerShell 模組，才能使用此功能。

[!INCLUDE [Cloud shell](../../includes/vpn-gateway-cloud-shell-powershell.md)]

### <a name="to-use-powershell-locally"></a>在本機上使用 PowerShell

如果您使用本機電腦上的 PowerShell，而不是使用 Cloud Shell，您必須安裝 PowerShell 模組 1.0.0 版或更新版本。 若要檢查已安裝的 PowerShell 版本，請使用下列命令：

```azurepowershell
Get-Module Az -ListAvailable | Select-Object -Property Name,Version,Path
```

如果您需要升級，請參閱[安裝 Azure PowerShell 模組](/powershell/azure/install-az-ps)。

[!INCLUDE [PowerShell login](../../includes/vpn-gateway-ps-login-include.md)]

## <a name="variables"></a>1.宣告變數

範例步驟所使用的值如下所示。 此外，有部分範例會在步驟中使用已宣告的變數。 如果您在自己的環境中使用這些步驟，請務必以您自己的值取代這些值。 在指定位置時，請確認您指定的區域可受到支援。 如需詳細資訊，請參閱[常見問題集](#faq)。

```azurepowershell-interactive
$RG1         = "TestRG1"
$VNet1       = "VNet1"
$Location1   = "CentralUS"
$FESubnet1   = "FrontEnd"
$BESubnet1   = "Backend"
$GwSubnet1   = "GatewaySubnet"
$VNet1Prefix = "10.1.0.0/16"
$FEPrefix1   = "10.1.0.0/24"
$BEPrefix1   = "10.1.1.0/24"
$GwPrefix1   = "10.1.255.0/27"
$Gw1         = "VNet1GW"
$GwIP1       = "VNet1GWIP"
$GwIPConf1   = "gwipconf1"
```

## <a name="configure"></a>2.建立虛擬網路

建立資源群組。

```azurepowershell-interactive
New-AzResourceGroup -ResourceGroupName $RG1 -Location $Location1
```

建立虛擬網路。

```azurepowershell-interactive
$fesub1 = New-AzVirtualNetworkSubnetConfig -Name $FESubnet1 -AddressPrefix $FEPrefix1
$besub1 = New-AzVirtualNetworkSubnetConfig -Name $BESubnet1 -AddressPrefix $BEPrefix1
$vnet = New-AzVirtualNetwork -Name $VNet1 -ResourceGroupName $RG1 -Location $Location1 -AddressPrefix $VNet1Prefix -Subnet $fesub1,$besub1
```

## <a name="gwsub"></a>3.新增閘道子網路

閘道子網路包含虛擬網路閘道服務所使用的保留 IP 位址。 請使用下列範例來新增和設定閘道子網路：

新增閘道子網路。

```azurepowershell-interactive
$getvnet = Get-AzVirtualNetwork -ResourceGroupName $RG1 -Name VNet1
Add-AzVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -AddressPrefix 10.1.255.0/27 -VirtualNetwork $getvnet
```

設定虛擬網路的閘道子網路設定。

```azurepowershell-interactive
$getvnet | Set-AzVirtualNetwork
```
## <a name="publicip"></a>4.要求公用 IP 位址
 
在此步驟中，針對您要建立的閘道選擇適用的指示。 部署閘道的區域選擇取決於為公用 IP 位址指定的區域。

### <a name="ipzoneredundant"></a>針對區域備援閘道

使用**標準** PublicIpaddress SKU 要求公用 IP 位址，而且不指定任何區域。 在此情況下，建立的標準公用 IP 位址會是區域備援的公用 IP。   

```azurepowershell-interactive
$pip1 = New-AzPublicIpAddress -ResourceGroup $RG1 -Location $Location1 -Name $GwIP1 -AllocationMethod Static -Sku Standard
```

### <a name="ipzonalgw"></a>針對分區閘道

使用**標準** PublicIpaddress SKU 要求公用 IP 位址。 指定區域 (1、2 或 3)。 所有閘道執行個體都會部署在此區域中。

```azurepowershell-interactive
$pip1 = New-AzPublicIpAddress -ResourceGroup $RG1 -Location $Location1 -Name $GwIP1 -AllocationMethod Static -Sku Standard -Zone 1
```

### <a name="ipregionalgw"></a>針對區域閘道

使用**基本** PublicIpaddress SKU 要求公用 IP 位址。 在此情況下，閘道會部署為區域閘道，而且閘道內不會建置任何區域備援。 閘道執行個體會在任何區域中個別建立。

```azurepowershell-interactive
$pip1 = New-AzPublicIpAddress -ResourceGroup $RG1 -Location $Location1 -Name $GwIP1 -AllocationMethod Dynamic -Sku Basic
```
## <a name="gwipconfig"></a>5.建立 IP 組態

```azurepowershell-interactive
$getvnet = Get-AzVirtualNetwork -ResourceGroupName $RG1 -Name $VNet1
$subnet = Get-AzVirtualNetworkSubnetConfig -Name $GwSubnet1 -VirtualNetwork $getvnet
$gwipconf1 = New-AzVirtualNetworkGatewayIpConfig -Name $GwIPConf1 -Subnet $subnet -PublicIpAddress $pip1
```

## <a name="gwconfig"></a>6.建立閘道

建立虛擬網路閘道。

### <a name="for-expressroute"></a>針對 ExpressRoute

```azurepowershell-interactive
New-AzVirtualNetworkGateway -ResourceGroup $RG1 -Location $Location1 -Name $Gw1 -IpConfigurations $GwIPConf1 -GatewayType ExpressRoute -GatewaySku ErGw1AZ
```

### <a name="for-vpn-gateway"></a>針對 VPN 閘道

```azurepowershell-interactive
New-AzVirtualNetworkGateway -ResourceGroup $RG1 -Location $Location1 -Name $Gw1 -IpConfigurations $GwIPConf1 -GatewayType Vpn -VpnType RouteBased -GatewaySku VpnGw1AZ
```

## <a name="faq"></a>常見問題集

### <a name="what-will-change-when-i-deploy-these-new-skus"></a>部署這些新的 SKU 時，將會改變什麼項目？

從您的觀點來看，您可以部署具有區域備援的閘道。 這表示閘道的所有執行個體將會部署到各個 Azure 可用性區域，而且每個可用性區域都是不同的容錯網域和更新網域。 面對區域失敗時，這可讓您的閘道具有更高的可靠性、可用性及復原能力。

### <a name="can-i-use-the-azure-portal"></a>我是否可使用 Azure 入口網站？

是，您可以使用 Azure 入口網站來部署新的 SKU。 不過，您只會在具有 Azure 可用性區域的 Azure 區域中看到這些新的 SKU。

### <a name="what-regions-are-available-for-me-to-use-the-new-skus"></a>我可以在哪些區域中使用新的 SKU？

請參閱[可用性區域](../availability-zones/az-overview.md#services-support-by-region)的可用區域的最新的清單。

### <a name="can-i-changemigrateupgrade-my-existing-virtual-network-gateways-to-zone-redundant-or-zonal-gateways"></a>我可以將現有的虛擬網路閘道變更/遷移/升級為區域備援或分區閘道嗎？

目前不支援將現有的虛擬網路閘道遷移至區域備援或分區閘道。 不過，您可以刪除現有的閘道，然後重新建立區域備援或分區閘道。

### <a name="can-i-deploy-both-vpn-and-express-route-gateways-in-same-virtual-network"></a>可以在相同的虛擬網路中部署 VPN 和 Express Route 閘道嗎？

支援 VPN 和 Express Route 閘道共存於相同的虛擬網路中。 不過，您應該保留閘道子網路的 /27 IP 位址範圍。
