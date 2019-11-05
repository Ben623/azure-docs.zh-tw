---
title: 包含檔案
description: 包含檔案
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 08/02/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 19b8a73835e8ac5ecaac7b42793140325964d17c
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/04/2019
ms.locfileid: "73522434"
---
若要使用 Azure 入口網站在 Resource Manager 部署模型中建立 VNet，請遵循下列步驟。 如果您使用這些步驟作為教學課程，請使用**範例值**。 如果您不執行這些步驟作為教學課程，請務必以自己的值取代這些值。 如需使用虛擬網路的詳細資訊，請參閱 [虛擬網路概觀](../articles/virtual-network/virtual-networks-overview.md)。

>[!NOTE]
>為了讓此 VNet 連線到內部部署位置，您需要與內部部署網路系統管理員協調，以切割出此虛擬網路專用的 IP 位址範圍。 如果 VPN 連線的兩端存在重複的位址範圍，流量就不會如預期的方式進行路由。 此外，如果您想要將此 VNet 連線到另一個 VNet，則位址空間不能與其他 VNet 重疊。 因此，請謹慎規劃您的網路組態。
>

1. 從 [ [Azure 入口網站](https://portal.azure.com)] 功能表中，選取 [**建立資源**]。 

   ![在 Azure 入口網站中建立資源](./media/vpn-gateway-create-virtual-network-portal-include/azure-portal-create-resource.png)
2. 在 [搜尋 Marketplace] 欄位中，輸入「虛擬網路」。 在傳回的清單中找到 [虛擬網路]，並按一下以開啟 [虛擬網路] 頁面。
3. 按一下 [建立]。 這會開啟 [建立虛擬網路] 頁面。
4. 在 [建立虛擬網路] 頁面上進行 VNet 設定。 當您填寫欄位時，若欄位中輸入的字元有效，紅色驚嘆號就會變成綠色核取記號。 輸入下列值：

   - **名稱**： VNet1
   - **位址空間**：10.1.0.0/16
   - **訂用帳戶**：確認列出的訂用帳戶是您想要使用的訂用帳戶。 您可以使用下拉式清單變更訂用帳戶。
   - **資源群組**： TestRG1 **（按一下 [新建]** 以建立新群組）
   - **位置**：美國東部
   - **子網路**：Frontend
   - **位址範圍**：10.1.0.0/24

   ![建立虛擬網路頁面](./media/vpn-gateway-create-virtual-network-portal-include/create-virtual-network1.png)
5. 將 [DDoS] 保留為 [基本]、[已停用服務端點] 和 [已停用]
6. 按一下 [建立] 來建立 VNet。