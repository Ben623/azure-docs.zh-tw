---
title: 虛擬 WAN：建立虛擬中樞路由表以 NVA： Azure 入口網站
description: 使用入口網站將流量引導至網路虛擬裝置的虛擬 WAN 虛擬中樞路由表。
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 03/05/2020
ms.author: cherylmc
Customer intent: As someone with a networking background, I want to create a route table using the portal.
ms.openlocfilehash: 0807b535adc45093b439dba5ab8a0ea26b2a0721
ms.sourcegitcommit: 05b36f7e0e4ba1a821bacce53a1e3df7e510c53a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/06/2020
ms.locfileid: "78402941"
---
# <a name="create-a-virtual-wan-hub-route-table-for-nvas-azure-portal"></a>建立 Nva 的虛擬 WAN 中樞路由表： Azure 入口網站

本文說明如何透過網路虛擬裝置（NVA），將流量從連線至虛擬 WAN 中樞的分支（內部部署網站）轉移到輪輻虛擬網路（VNet）。

![虛擬 WAN 的圖表](./media/virtual-wan-route-table/vwanroute.png)

## <a name="before-you-begin"></a>開始之前

請確認您已符合下列條件：

*  您有網路虛擬裝置（NVA）。 網路虛擬裝置是您選擇的協力廠商軟體，通常會從虛擬網路中的 Azure Marketplace 布建。

    * 必須將私人 IP 位址指派給 NVA 網路介面。

    * NVA 不會部署在虛擬中樞內。 它必須部署在不同的虛擬網路中。

    *  NVA 虛擬網路可能會有一或多個連線到它的虛擬網路。 在本文中，我們將 NVA 虛擬網路稱為「間接輪輻 VNet」。 這些虛擬網路可以使用 VNet 對等互連連線到 NVA VNet。 Vnet 對等互連連結是由上圖中的黑色箭號（vnet 1、VNet 2 和 NVA VNet）所描述。
*  您已建立兩個虛擬網路。 它們將用來做為輪輻 Vnet。

    * VNet 輪輻位址空間包括： VNet1： 10.0.2.0/24 和 VNet2： 10.0.3.0/24。 如果您需要如何建立虛擬網路的資訊，請參閱[建立虛擬網路](../virtual-network/quick-create-portal.md)。

    * 確定任何 Vnet 中都沒有虛擬網路閘道。

    * Vnet 不需要閘道子網。

## <a name="signin"></a>1. 登入

透過瀏覽器瀏覽至 [Azure 入口網站](https://portal.azure.com) ，並使用您的 Azure 帳戶登入。

## <a name="vwan"></a>2. 建立虛擬 WAN

建立虛擬 WAN。 使用下列範例值：

* **虛擬 WAN 名稱：** myVirtualWAN
* **資源群組：** testRG
* **位置：** 美國西部

[!INCLUDE [Create a virtual WAN](../../includes/virtual-wan-tutorial-vwan-include.md)]

## <a name="hub"></a>3. 建立中樞

建立中樞。 使用下列範例值：

* **位置：** 美國西部
* **名稱：** westushub
* **中樞私人位址空間：** 10.0.1.0/24

[!INCLUDE [Create a hub](../../includes/virtual-wan-tutorial-hub-include.md)]

## <a name="route"></a>4. 建立和套用中樞路由表

使用中樞路由表來更新中樞。 使用下列範例值：

* **輪輻 VNet 位址空間：** （VNet1 和 VNet2） 10.0.2.0/24 和 10.0.3.0/24
* **DMZ NVA 網路介面私人 IP 位址：** 10.0.4。5

1. 流覽至您的虛擬 WAN。
2. 按一下您要建立路由表的中樞。
3. 按一下 [ **...** ]，然後按一下 [**編輯虛擬中樞**]。
4. 在 [**編輯虛擬中樞**] 頁面上，向下卷並選取 [**使用資料表路由**] 核取方塊。
5. 在 [**如果目的地前置詞為**] 資料行中，新增位址空間。 在 [**傳送到下一個躍點]** 資料行中，新增 DMZ NVA 網路介面私人 IP 位址。
6. 按一下 [**確認**] 以使用路由表設定來更新中樞資源。

## <a name="connections"></a>5. 建立 VNet 連線

建立從每個間接輪輻 VNet （VNet1 和 VNet2）到中樞的虛擬網路連線。 這些虛擬網路連線是由上圖中的藍色箭號所描述。 然後，從 NVA VNet 建立 VNet 連線至中樞（圖中的黑色箭號）。

 在此步驟中，您可以使用下列值：

| 虛擬網路名稱| 連接名稱|
| --- | --- |
| VNet1 | testconnection1 |
| VNet2 | testconnection2 |
| NVAVNet | testconnection3 |

針對您想要連線的每個虛擬網路，重複執行下列程式。

1. 在虛擬 WAN 頁面上，按一下 [虛擬網路連線]。
2. 在 [虛擬網路連線] 頁面上，按一下 [+ 新增連線]。
3. 在 [新增連線] 頁面上，填寫下列欄位︰

    * **名稱** - 為您的連線命名。
    * **中樞** - 選取要與此連線產生關聯的中樞。
    * **訂用帳戶** - 請確認訂用帳戶。
    * **虛擬網路** - 選取要與此中樞連線的虛擬網路。 虛擬網路不能有現有的虛擬網路閘道。
4. 按一下 [確定] 來建立連線。

## <a name="next-steps"></a>後續步驟

若要深入了解虛擬 WAN，請參閱[虛擬 WAN 概觀](virtual-wan-about.md)頁面。
