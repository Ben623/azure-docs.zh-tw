---
title: 將受控資料磁碟連結到 Windows VM - Azure | Microsoft Docs
description: 如何使用 Azure 入口網站將受控資料磁碟連結到 Windows VM。
services: virtual-machines-windows
documentationcenter: ''
author: roygara
manager: twooley
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 10/08/2018
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: e3641960131d23bf5a8e5b2310a09e7a4dbd70b9
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "64680408"
---
# <a name="attach-a-managed-data-disk-to-a-windows-vm-by-using-the-azure-portal"></a>使用 Azure 入口網站將受控資料磁碟連結到 Windows VM

本文示範如何使用 Azure 入口網站，將新的受控資料磁碟連結到 Windows 虛擬機器 (VM)。 VM 的大小會決定您可以連結的資料磁碟數目。 如需相關資訊，請參閱[虛擬機器的大小](sizes.md)。


## <a name="add-a-data-disk"></a>新增資料磁碟

1. 在 [Azure 入口網站](https://portal.azure.com)中，從左側功能表選取 [虛擬機器]  。
2. 從清單中選取虛擬機器。
3. 在 [虛擬機器]  窗格中，選取 [磁碟]  。
4. 在 [磁碟]  頁面上，選取 [新增資料磁碟]  。
5. 在新磁碟的下拉式清單中，選取 [建立磁碟]  。
6. 在 [建立受控磁碟]  頁面中，輸入磁碟名稱並視需要調整其他設定。 當您完成時，選取 [建立]  。
7. 在 [磁碟]  頁面中，選取 [儲存]  以儲存 VM 的新磁碟設定。
8. 在 Azure 建立磁碟並將其連接至虛擬機器之後，該新磁碟就會列在虛擬機器之磁碟設定中的 [資料磁碟]  底下。


## <a name="initialize-a-new-data-disk"></a>初始化新的資料磁碟

1. 連線至 VM。
1. 選取執行中 VM 內部的 Windows [開始]  功能表，然後在搜尋方塊中輸入 **diskmgmt.msc**。 [磁碟管理]  主控台隨即開啟。
2. 磁碟管理會辨識出您擁有新的、未初始化的磁碟，而且 [初始化磁碟]  視窗隨即出現。
3. 確認已選取新磁碟，然後選取 [確定]  將磁碟初始化。
4. 新的磁碟會顯示為 [未配置]  。 以滑鼠右鍵按一下磁碟上的任何位置，並選取 [新增簡單磁碟區]  。 [新增簡單磁碟區精靈]  視窗隨即開啟。
5. 繼續執行精靈，保留所有預設值，並且在完成時選取 [完成]  。
6. 關閉 [磁碟管理]  。
7. 隨即出現一個快顯視窗，通知您必須先將新的磁碟格式化之後才能使用。 選取 [格式化磁碟]  。
8. 在 [格式化新磁碟]  視窗中，檢查設定，然後選取 [開始]  。
9. 隨即出現警告，通知您格式化磁碟會清除所有資料。 選取 [確定]  。
10. 格式化完成後，請選取 [確定]  。

## <a name="next-steps"></a>後續步驟

- 您也可以[使用 PowerShell 來連結資料磁碟](attach-disk-ps.md)。
- 如果您的應用程式需要使用 D:  磁碟機來儲存資料，您可以[變更 Windows 暫存磁碟的磁碟機代號](change-drive-letter.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)。
