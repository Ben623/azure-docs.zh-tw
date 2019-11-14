---
title: 在 Azure 中移動 Windows VM 資源
description: 在 Resource Manager 部署模型中將 Windows VM 移至另一個 Azure 訂用帳戶或資源群組。
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: 4e383427-4aff-4bf3-a0f4-dbff5c6f0c81
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.topic: article
ms.date: 07/03/2019
ms.author: cynthn
ms.openlocfilehash: 9264e36922cb88c541ba9fb2fe54a9606f371b72
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/13/2019
ms.locfileid: "74033183"
---
# <a name="move-a-windows-vm-to-another-azure-subscription-or-resource-group"></a>將 Windows VM 移至另一個 Azure 訂用帳戶或資源群組
本文將逐步引導您了解如何在資源群組或訂用帳戶之間移動 Windows 虛擬機器 (VM)。 如果您原本在個人訂用帳戶中建立 VM，而現在想要將它移至您的公司訂用帳戶以繼續工作，在訂用帳戶之間移動會很方便。 您不需要啟動 VM 就能移動它，而且它應該會在移動期間繼續執行。

> [!IMPORTANT]
>移動過程中會建立新的資源識別碼。 移動 VM 之後，您必須更新工具和指令碼以使用新的資源識別碼。 
> 
> 

[!INCLUDE [virtual-machines-common-move-vm](../../../includes/virtual-machines-common-move-vm.md)]

## <a name="use-powershell-to-move-a-vm"></a>使用 PowerShell 移動 VM

若要將虛擬機器移至另一個資源群組，您必須確定也要移動所有的相依資源。 若要取得清單 (其中具有每個資源的資源識別碼)，請使用 [Get-AzResource](https://docs.microsoft.com/powershell/module/az.resources/get-azresource) Cmdlet。

```azurepowershell-interactive
 Get-AzResource -ResourceGroupName <sourceResourceGroupName> | Format-list -wrap -Property ResourceId 
```

您可以使用上述命令的輸出作為 [Move-AzResource](https://docs.microsoft.com/powershell/module/az.resources/move-azresource) 以逗號分隔的資源識別碼清單，以將每個資源移到目的地。 

```azurepowershell-interactive
Move-AzResource -DestinationResourceGroupName "<myDestinationResourceGroup>" `
    -ResourceId <myResourceId,myResourceId,myResourceId>
```
    
若要將資源移到不同的訂用帳戶，請納入 **DestinationSubscriptionId** 參數。 

```azurepowershell-interactive
Move-AzResource -DestinationSubscriptionId "<myDestinationSubscriptionID>" `
    -DestinationResourceGroupName "<myDestinationResourceGroup>" `
    -ResourceId <myResourceId,myResourceId,myResourceId>
```


系統要求您確認您想要移動指定的資源時，請輸入 **Y** 來確認。

## <a name="next-steps"></a>後續步驟
您可以在資源群組和訂用帳戶之間移動許多不同類型的資源。 如需詳細資訊，請參閱[將資源移動到新的資源群組或訂用帳戶](../../resource-group-move-resources.md)。    

