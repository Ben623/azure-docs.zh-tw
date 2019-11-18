---
title: 將 Azure 網路資源移到新的訂用帳戶或資源群組
description: 使用 Azure Resource Manager 將虛擬網路和其他網路資源移到新的資源群組或訂用帳戶。
ms.topic: conceptual
ms.date: 10/16/2019
ms.openlocfilehash: c4e6acb52f6342c57fb1db9fc3e83d90d6d01285
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/17/2019
ms.locfileid: "74150833"
---
# <a name="move-guidance-for-networking-resources"></a>移動網路資源的指導方針

本文說明如何在特定案例中移動虛擬網路和其他網路資源。

## <a name="dependent-resources"></a>相依資源

當您移動虛擬網路時，也必須移動其相依資源。 針對 VPN 閘道，您必須移動 IP 位址、虛擬網路閘道和所有相關聯的連線資源。 區域網路閘道可位於不同的資源群組。

若要將具有網路介面卡的虛擬機器移至新的訂用帳戶，您必須移動所有相依資源。 移動網路介面卡的虛擬網路、虛擬網路的所有其他網路介面卡，以及 VPN 閘道。

如需詳細資訊，請參閱跨訂用帳戶[移動的案例](../resource-group-move-resources.md#scenario-for-move-across-subscriptions)。

## <a name="peered-virtual-network"></a>對等互連虛擬網路

若要移動對等虛擬網路，您必須先停用虛擬網路對等互連。 停用之後，您可以移動虛擬網路。 移動之後，重新啟用虛擬網路對等互連。

## <a name="subnet-links"></a>子網連結

如果虛擬網路包含有資源導覽連結的子網路，則無法將虛擬網路移動到其他訂用帳戶。 例如，如果 Azure Cache for Redis 資源部署到子網路，該子網路具有資源導覽連結。

## <a name="next-steps"></a>後續步驟

如需用來移動資源的命令，請參閱[將資源移動到新的資源群組或訂用帳戶](../resource-group-move-resources.md)。
