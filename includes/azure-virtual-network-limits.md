---
title: 包含檔案
description: 包含檔案
services: networking
author: anavinahar
ms.service: networking
ms.topic: include
ms.date: 01/13/2020
ms.author: anavin
ms.custom: include file
ms.openlocfilehash: 97ee10e0b286f1b007f2e807351301f2037d1754
ms.sourcegitcommit: 014e916305e0225512f040543366711e466a9495
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/14/2020
ms.locfileid: "75942825"
---
<a name="azure-resource-manager-virtual-networking-limits"></a>網路限制-Azure Resource Manager 下列限制僅適用于透過每個訂用帳戶的每個區域**Azure Resource Manager**管理的網路資源。 深入了解如何[根據您的訂用帳戶限制檢視目前資源使用量](../articles/networking/check-usage-against-limits.md)。

> [!NOTE]
> 我們最近已將所有預設限制提升至其最大限制。 如果沒有上限資料行，則資源沒有可調整的限制。 如果您過去的支援增加了這些限制，而且在下表中看不到更新的限制，請免費[開啟線上客戶支援要求](../articles/azure-resource-manager/resource-manager-quota-errors.md)

| 資源 | 預設/最大限制 | 
| --- | --- |
| 虛擬網路 |1,000 |
| 每一虛擬網路的子網路 |3,000 |
| 每個虛擬網路的虛擬網路對等互連 |500 |
| [每個虛擬網路的虛擬網路閘道（VPN 閘道）](../articles/vpn-gateway/vpn-gateway-about-vpngateways.md#gwsku) |30 |
| 每個虛擬網路的 DNS 伺服器 |20 |
| 每個虛擬網路的私人 IP 位址 |65,536 |
| 每個網路介面的私人 IP 位址 |256 |
| 每個虛擬機器的私人 IP 位址 |256 |
| 每個網路介面的公用 IP 位址 |256 |
| 每個虛擬機器的公用 IP 位址 |256 |
| [虛擬機器或角色實例每個 NIC 的並行 TCP 或 UDP 流量](../articles/virtual-network/virtual-machine-network-throughput.md#flow-limits-and-recommendations) |500,000 |
| 網路介面卡 |65,536 |
| 網路安全性群組 |5,000 |
| 每一 NSG 的 NSG 規則 |1,000 |
| 針對安全性群組中的來源或目的地所指定的 IP 位址和範圍 |4,000 |
| 應用程式安全性群組 |3,000 |
| 每個 NIC、每個 IP 組態的應用程式安全性群組 |20 |
| 每個應用程式安全性群組的 IP 組態 |4,000 |
| 可在網路安全性群組的所有安全性規則內指定的應用程式安全性群組 |100 |
| 使用者定義的路由表 |200 |
| 每個路由表的使用者定義路由 |400 |
| 每個 Azure VPN 閘道的點對站根憑證 |20 |
| 虛擬網路 TAP |100 |
| 每個虛擬網路 TAP 的網路介面 TAP 設定 |100 |

#### <a name="publicip-address"></a>公用 IP 位址限制
| 資源 | 預設限制 | 上限 |
| --- | --- | --- |
| 公用 IP 位址 - 動態 | 適用于 Basic 的1000。 |請連絡支援人員。 |
| 公用 IP 位址 - 靜態 | 適用于 Basic 的1000。 |請連絡支援人員。 |
| 公用 IP 位址 - 靜態 | 適用于 Standard 的1000。|請連絡支援人員。 |
| 公用 IP 前置長度 | /28 | 請連絡支援人員。 |

#### <a name="load-balancer"></a>負載平衡器限制
下列限制僅適用於透過每個訂用帳戶每一區域的 Azure Resource Manager 所管理的網路資源。 深入了解如何[根據您的訂用帳戶限制檢視目前資源使用量](../articles/networking/check-usage-against-limits.md)。

**Standard Load Balancer**

| 資源                                | 預設/最大限制         |
|-----------------------------------------|-------------------------------|
| 負載平衡器                          | 1,000                         |
| 每個資源的規則                      | 1,500                         |
| 每個 NIC 的規則（在 NIC 上的所有 Ip） | 300                           |
| 前端 IP 設定              | 600                           |
| 後端集區大小                       | 1000 IP 設定，單一虛擬網路 |
| 高可用性埠                 | 每個內部前端 1 個       |
| 每個 Load Balancer 的輸出規則         | 20                            |


**基本 Load Balancer**

| 資源                                | 預設/最大限制        |
|-----------------------------------------|------------------------------|
| 負載平衡器                          | 1,000                        |
| 每個資源的規則                      | 250                          |
| 每個 NIC 的規則（在 NIC 上的所有 Ip） | 300                          |
| 前端 IP 設定              | 200                          |
| 後端集區大小                       | 300 IP 設定，單一可用性設定組 |
| 每個 Load Balancer 的可用性設定組     | 150                          |

#### <a name="virtual-networking-limits-classic"></a>下列限制僅適用于透過每個訂用帳戶的**傳統**部署模型所管理的網路資源。 深入了解如何[根據您的訂用帳戶限制檢視目前資源使用量](../articles/networking/check-usage-against-limits.md)。

| 資源 | 預設限制 | 上限 |
| --- | --- | --- |
| 虛擬網路 |100 |100 |
| 區域網路網站 |20 |50 |
| 每個虛擬網路的 DNS 伺服器 |20 |20 |
| 每個虛擬網路的私人 IP 位址 |4,096 |4,096 |
| 虛擬機器或角色執行個體之每個 NIC 的並行 TCP 或 UDP 流程 |500000，最多1000000個，適用于兩個以上的 Nic。 |500000，最多1000000個，適用于兩個以上的 Nic。 |
| 網路安全性群組 (NSG) |200 |200 |
| 每一 NSG 的 NSG 規則 |1,000 |1,000 |
| 使用者定義的路由表 |200 |200 |
| 每個路由表的使用者定義路由 |400 |400 |
| 公用 IP 位址 (動態) |500 |500 |
| 保留的公用 IP 位址 |500 |500 |
| 每個部署的公用 VIP |5 |連絡客戶支援 |
| 每個部署的私人 VIP （內部負載平衡） |1 |1 |
| 端點存取控制清單（Acl） |50 |50 |
