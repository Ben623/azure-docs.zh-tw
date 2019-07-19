---
title: 建立具有區域備援前端的 Load Balancer - Azure PowerShell
titlesuffix: Azure Load Balancer
description: 了解如何使用 PowerShell 建立具有區域備援公用 IP 位址前端的公用 Standard Load Balancer
services: load-balancer
documentationcenter: na
author: asudbring
manager: twooley
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/21/2019
ms.author: allensu
ms.openlocfilehash: 3ce14a0d9d5b9d5ef7ca7be34faad3fa60795ab9
ms.sourcegitcommit: 9a699d7408023d3736961745c753ca3cec708f23
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/16/2019
ms.locfileid: "68275094"
---
#  <a name="create-a-standard-load-balancer-with-zone-redundant-frontend-using-azure-powershell"></a>使用 Azure PowerShell 來建立具有區域備援前端的 Standard Load Balancer

本文會逐步說明如何使用公用 IP 標準位址，來建立具有區域備援前端的公用 [Standard Load Balancer](https://aka.ms/azureloadbalancerstandard)。

如果您沒有 Azure 訂用帳戶，請在開始前建立 [免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) 。

> [!NOTE]
>  針對精選的 Azure 資源和區域及 VM 大小系列有提供「可用性區域」支援。 如需如何開始使用，以及有哪些 Azure 資源、區域和 VM 大小系列可供用來試用可用性區域，請參閱[可用性區域概觀](https://docs.microsoft.com/azure/availability-zones/az-overview)。 如需支援，您可以透過 [StackOverflow](https://stackoverflow.com/questions/tagged/azure-availability-zones) 與我們聯繫或[開啟 Azure 支援票證](../azure-supportability/how-to-create-azure-support-request.md?toc=%2fazure%2fvirtual-network%2ftoc.json)。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="log-in-to-azure"></a>登入 Azure

使用 `Connect-AzAccount` 命令登入 Azure 訂用帳戶並遵循畫面上的指示。

```azurepowershell-interactive
Connect-AzAccount
```

## <a name="create-resource-group"></a>建立資源群組

使用下列命令建立資源群組：

```azurepowershell-interactive
New-AzResourceGroup -Name myResourceGroup -Location westeurope
```

## <a name="create-a-public-ip-standard"></a>建立 Public IP Standard 
使用下列命令建立 Public IP Standard：

```azurepowershell-interactive
$publicIp = New-AzPublicIpAddress -ResourceGroupName myResourceGroup -Name 'myPublicIP' `
  -Location westeurope -AllocationMethod Static -Sku Standard
```

## <a name="create-a-front-end-ip-configuration-for-the-website"></a>為網站建立前端 IP 組態

使用下列命令來建立前端 IP 組態：

```azurepowershell-interactive
$feip = New-AzLoadBalancerFrontendIpConfig -Name 'myFrontEndPool' -PublicIpAddress $publicIp
```

## <a name="create-the-back-end-address-pool"></a>建立後端位址集區

使用下列命令來建立後端位址集區：

```azurepowershell-interactive
$bepool = New-AzLoadBalancerBackendAddressPoolConfig -Name 'myBackEndPool'
```

## <a name="create-a-load-balancer-probe-on-port-80"></a>在連接埠 80 上建立負載平衡器探查

使用下列命令在連接埠 80 上建立負載平衡器地健康情況探查：

```azurepowershell-interactive
$probe = New-AzLoadBalancerProbeConfig -Name 'myHealthProbe' -Protocol Http -Port 80 `
  -RequestPath / -IntervalInSeconds 360 -ProbeCount 5
```

## <a name="create-a-load-balancer-rule"></a>建立負載平衡器規則
 使用下列命令建立負載平衡器規則：

```azurepowershell-interactive
   $rule = New-AzLoadBalancerRuleConfig -Name HTTP -FrontendIpConfiguration $feip -BackendAddressPool  $bepool -Probe $probe -Protocol Tcp -FrontendPort 80 -BackendPort 80
```

## <a name="create-a-load-balancer"></a>建立負載平衡器
使用下列命令建立 Standard Load Balancer：

```azurepowershell-interactive
$lb = New-AzLoadBalancer -ResourceGroupName myResourceGroup -Name 'MyLoadBalancer' -Location westeurope `
  -FrontendIpConfiguration $feip -BackendAddressPool $bepool `
  -Probe $probe -LoadBalancingRule $rule -Sku Standard
```

## <a name="next-steps"></a>後續步驟
- 深入了解[標準 Load Balancer 和可用性區域](load-balancer-standard-availability-zones.md)。