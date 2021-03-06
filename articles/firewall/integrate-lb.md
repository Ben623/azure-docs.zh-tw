---
title: 整合 Azure 防火牆與 Azure Standard Load Balancer
description: 您可以將 Azure 防火牆整合到具有 Azure Standard Load Balancer (公用或內部) 的虛擬網路。
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 02/28/2020
ms.author: victorh
ms.openlocfilehash: ab9a500d9535b55702b8baff15f8cc47e6ac2c86
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/29/2020
ms.locfileid: "78196689"
---
# <a name="integrate-azure-firewall-with-azure-standard-load-balancer"></a>整合 Azure 防火牆與 Azure Standard Load Balancer

您可以將 Azure 防火牆整合到具有 Azure Standard Load Balancer (公用或內部) 的虛擬網路。 

慣用的設計是將內部負載平衡器與您的 Azure 防火牆整合，因為這是更簡單的設計。 如果您已經部署了公用負載平衡器，而且想要將它保留在原處，您可以使用它。 不過您必須了解在公用負載平衡器案例中可能會中斷功能的非對稱式路由問題。

如需 Azure Load Balancer 的詳細資訊，請參閱[什麼是 Azure Load Balancer？](../load-balancer/load-balancer-overview.md)

## <a name="public-load-balancer"></a>公用 Load Balancer

使用公用負載平衡器時，該負載平衡器會以公用的前端 IP 位址進行部署。

### <a name="asymmetric-routing"></a>非對稱式路由

在非對稱式路由的情況下，封包會採用某個路徑前往目的地，並採用另一個路徑返回來源。 在子網路具有前往防火牆私人 IP 位址的預設路由，且您是使用公用負載平衡器的情況下，就會發生此問題。 在此情況下，系統會透過傳入負載平衡器流量的公用 IP 位址接收它，但傳回路徑則會通過防火牆的私人 IP 位址。 由於防火牆是具狀態的，因此它會捨棄傳回的封包，因為防火牆並不知道這類已建立的會話。

### <a name="fix-the-routing-issue"></a>修正路由問題

當您將 Azure 防火牆部署到子網路時，其中一個步驟是為子網路建立預設路由，以引導封包通過防火牆位於 AzureFirewallSubnet 上的私人 IP 位址。 如需詳細資訊，請參閱[教學課程：使用 Azure 入口網站部署和設定 Azure 防火牆](tutorial-firewall-deploy-portal.md#create-a-default-route)。

當您將防火牆導入負載平衡器案例時，您通常會讓傳入的網際網路流量通過防火牆的公用 IP 位址。 從那裡，防火牆便會套用其防火牆規則，並將封包網路位址轉譯到您負載平衡器的公用 IP 位址。 而問題就是在此處發生。 封包會抵達防火牆的公用 IP 位址，但會透過私人 IP 位址 (使用預設路由) 返回防火牆。
若要避免此問題，請為防火牆的公用 IP 位址建立額外的主機路由。 前往防火牆公用 IP 位址的封包會透過網際網路進行路由。 這可避免採用預設路由來前往防火牆的私人 IP 位址。

![非對稱式路由](media/integrate-lb/Firewall-LB-asymmetric.png)

### <a name="route-table-example"></a>路由表範例

例如，下列路由適用于公用 IP 位址20.185.97.136 的防火牆和私人 IP 位址10.0.1.4。

> [!div class="mx-imgBorder"]
> ![路由表](media/integrate-lb/route-table.png)

### <a name="nat-rule-example"></a>NAT 規則範例

在下列範例中，NAT 規則會將 RDP 流量轉換成20.185.97.136 到負載平衡器的防火牆，20.42.98.220：

> [!div class="mx-imgBorder"]
> ![NAT 規則](media/integrate-lb/nat-rule-02.png)

### <a name="health-probes"></a>健康狀態探查

請記住，如果您使用 TCP 健康情況探查來埠80或 HTTP/HTTPS 探查，則需要在負載平衡器集區的主機上執行 web 服務。

## <a name="internal-load-balancer"></a>內部負載平衡器

使用內部負載平衡器時，該負載平衡器會以私人的前端 IP 位址進行部署。

此案例並不會有非對稱式路由問題。 傳入封包會抵達防火牆的公用 IP 位址，被轉譯到負載平衡器的私人 IP 位址，然後使用相同的傳回路徑返回防火牆的私人 IP 位址。

因此，您可以用與公用負載平衡器案例類似的方式部署此案例，但不需要建立防火牆公用 IP 位址主機路由。

## <a name="additional-security"></a>額外的安全性

若要進一步加強負載平衡案例的安全性，您可以使用網路安全性群組 (NSG)。

例如，您可以在已負載平衡的虛擬機器所位於的後端子網路上建立 NSG。 允許源自防火牆 IP 位址/連接埠的傳入流量。

![網路安全性群組](media/integrate-lb/nsg-01.png)

如需有關 NSG 的詳細資訊，請參閱[安全性群組](../virtual-network/security-overview.md)。

## <a name="next-steps"></a>後續步驟

- 深入了解如何[部署和設定 Azure 防火牆](tutorial-firewall-deploy-portal.md)。