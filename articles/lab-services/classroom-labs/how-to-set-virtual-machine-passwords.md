---
title: 在 Azure 實驗室服務中設定 Vm 的密碼 |Microsoft Docs
description: 瞭解如何在 Azure 實驗室服務的教室實驗室中設定和重設虛擬機器（Vm）的密碼。
services: lab-services
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/12/2019
ms.author: spelluru
ms.openlocfilehash: 40cdd0adf7bf100e1dbca64dbba68db3bc59a4fe
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/15/2019
ms.locfileid: "72331490"
---
# <a name="set-up-and-manage-virtual-machine-pool"></a>設定和管理虛擬機器集區 
本文說明如何執行下列工作：

- 增加實驗室中的虛擬機器（Vm）數目
- 啟動所有 Vm 或選取的 Vm 
- 重設 Vm

## <a name="update-the-lab-capacity"></a>更新實驗室容量
若要增加或減少實驗室容量（實驗室中的虛擬機器數目），請執行下列步驟：

1. 在 [**虛擬機器集**區] 頁面上，選取 [**實驗室容量： &lt;number @ no__t-3 部電腦**]。
2. 在實驗室中輸入您想要的新**vm 數目**。 此數位必須大於或等於實驗室中註冊的使用者數目。 
3. 然後選取 [儲存]。 

    ![[全部啟動] 按鈕](../media/how-to-set-virtual-machine-passwords/number-of-vms-in-lab.png)
4. 如果您增加了容量，您可以看到 VM 或正在建立的 Vm。 

    ![正在建立 VM](../media/how-to-set-virtual-machine-passwords/vm-being-created.png)

## <a name="start-vms"></a>啟動 VM

### <a name="start-ot-stop-all-vms"></a>開始停止所有 Vm
1. 切換至 [**虛擬機器集**區] 頁面。 
2. 從工具列中選取 [**全部啟動**]。 

    ![[全部啟動] 按鈕](../media/how-to-set-virtual-machine-passwords/start-all-vms-button.png)
3. 啟動所有 Vm 之後，您可以選取工具列上的 [全部**停止**] 按鈕來停止所有 vm。 

    ![[全部停止] 按鈕](../media/how-to-set-virtual-machine-passwords/stop-all-vms-button.png)

### <a name="start-selected-vms"></a>啟動選取的 Vm
有兩種方式可以啟動選取的 Vm （一或多個）。 第一種方式是選取清單中的 VM 或 vm，然後選取工具列上的 [**啟動**]。 第二種方式是選取清單中的 VM 或 vm，在其中一個資料列的 [**狀態**] 資料行中選取 [dropdown]，然後選取 [**啟動**]。 

![啟動選取的 Vm](../media/how-to-set-virtual-machine-passwords/start-selected-vms.png)

同樣地，您可以使用 [**狀態**] 資料行中的下拉式清單或工具列上的 [**停止**]，來停止一或多個 vm。 

## <a name="reset-vms"></a>重設 Vm
若要重設一或多個 Vm，請在清單中選取它們，然後選取工具列上的 [**重設**]。 

![重設選取的 Vm](../media/how-to-set-virtual-machine-passwords/reset-vm-button.png)

在 [**重設虛擬機器**] 對話方塊中，選取 [**重設**]。 

![重設 VM 對話方塊](../media/how-to-set-virtual-machine-passwords/reset-vms-dialog.png)



## <a name="set-password-for-vms"></a>設定 Vm 的密碼
實驗室擁有者（老師）可以在建立實驗室（lab 建立嚮導）時，或在 [**範本**] 頁面上建立實驗室之後，設定/重設 vm 的密碼。 

### <a name="set-password-at-the-time-of-lab-creation"></a>在建立實驗室時設定密碼
實驗室擁有者（老師）可以在實驗室建立嚮導的 [**虛擬機器認證**] 頁面上，為實驗室中的 vm 設定密碼。

![新增實驗室視窗](../media/tutorial-setup-classroom-lab/virtual-machine-credentials.png)

藉由啟用/停用此頁面上**所有虛擬機器的 [使用相同的密碼**] 選項，老師可以選擇針對實驗室中的所有 vm 使用相同的密碼，或允許學生設定其 vm 的密碼。 根據預設，除了 Ubuntu 以外，所有 Windows 和 Linux 作業系統映射都會啟用此設定。 當此設定停用時，學生會在嘗試第一次連線到 VM 時，提示您設定密碼。 

### <a name="reset-password-later"></a>稍後重設密碼

1. 在實驗室的 [**範本**] 頁面上，選取工具列上的 [**重設密碼**]。 

    ![首頁上的重設密碼功能表](../media/how-to-set-virtual-machine-passwords/reset-password-menu-dashboard.png)
1. 在 [**重設密碼**] 對話方塊中，輸入密碼，然後選取 [**重設密碼**]。
    
    ![[設定密碼] 對話方塊](../media/how-to-set-virtual-machine-passwords/set-password.png)

## <a name="next-steps"></a>後續步驟
若要瞭解您（身為實驗室擁有者）可以設定的其他學生使用選項，請參閱下列文章：[設定學生使用](how-to-configure-student-usage.md)方式。

若要瞭解學生如何重設其 Vm 的密碼，請參閱[在教室實驗室（學生）中設定或重設虛擬機器的密碼](how-to-set-virtual-machine-passwords-student.md)。