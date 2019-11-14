---
title: 從 Windows VM 卸離資料磁片-Azure
description: 使用 Resource Manger 部署模型，將資料磁碟與 Azure 中的虛擬機器中斷連結。
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: gwallace
editor: ''
tags: azure-service-management
ms.assetid: 13180343-ac49-4a3a-85d8-0ead95e2028c
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 07/17/2018
ms.author: cynthn
ms.subservice: disks
ms.openlocfilehash: 93db2935fdc41787bb1820d1f8ce85ac05ef0863
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/13/2019
ms.locfileid: "74033350"
---
# <a name="how-to-detach-a-data-disk-from-a-windows-virtual-machine"></a>如何從 Windows 虛擬機器卸離資料磁碟

當不再需要某個連接至虛擬機器的資料磁碟時，卸離此資料磁碟很簡單。 這會將磁碟從虛擬機器中卸離，但這不會將它從儲存體中移除。

> [!WARNING]
> 將磁碟中斷連結時，並不會自動將它刪除。 如果您已訂閱「進階」儲存體，您將會繼續因該磁碟而導致產生儲存體費用。 如需詳細資訊，請參閱[使用進階儲存體時的價格和計費](disks-types.md#billing)。

如果您想要再次使用磁碟上現有的資料，您可以將磁碟重新連接至相同或其他虛擬機器。

 

## <a name="detach-a-data-disk-using-powershell"></a>使用 PowerShell 來中斷資料磁碟連結

您可以使用 PowerShell 來「熱」移除資料磁碟，但是在從 VM 卸離磁碟之前，請確定沒有任何項目正在使用該磁碟。

在此範例中，我們會從 **myResourceGroup** 資源群組中的 **myVM** 移除名為 **myDisk** 的磁碟。 首先使用 [Remove-AzVMDataDisk](https://docs.microsoft.com/powershell/module/az.compute/remove-azvmdatadisk) Cmdlet 來移除磁碟。 接著，您會使用 [Update-AzVM](https://docs.microsoft.com/powershell/module/az.compute/update-azvm) Cmdlet 來更新虛擬機器的狀態，以完成移除資料磁碟的程序。

```azurepowershell-interactive
$VirtualMachine = Get-AzVM -ResourceGroupName "myResourceGroup" -Name "myVM"
Remove-AzVMDataDisk -VM $VirtualMachine -Name "myDisk"
Update-AzVM -ResourceGroupName "myResourceGroup" -VM $VirtualMachine
```

磁碟仍留在儲存體中，但不再連結至虛擬機器。

## <a name="detach-a-data-disk-using-the-portal"></a>使用入口網站來中斷資料磁碟連結

1. 在左窗格中，選取 [虛擬機器]。
2. 選取含有您想要中斷連結之資料磁碟的虛擬機器，然後按一下 [停止] 以將該 VM 解除配置。
3. 在 [虛擬機器] 窗格中，選取 [磁碟]。
4. 在 [磁碟] 窗格頂端，選取 [編輯]。
5. 在 [磁碟] 窗格中，在您想要中斷連結的資料磁碟最右側，按一下 [中斷連結按鈕影像]![](./media/detach-disk/detach.png) 中斷連結按鈕。
5. 移除磁碟之後，按一下窗格頂端的 [儲存]。
6. 在 [虛擬機器] 窗格中，按一下 [概觀]，然後按一下窗格頂端的 [啟動] 按鈕以重新啟動 VM。

磁碟仍留在儲存體中，但不再連結至虛擬機器。

## <a name="next-steps"></a>後續步驟

如果您想要重複使用該資料磁碟，只要 [將它連結至另一個 VM](attach-managed-disk-portal.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)