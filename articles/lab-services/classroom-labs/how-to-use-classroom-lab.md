---
title: 如何存取 Azure 實驗室服務中的教室實驗室 | Microsoft Docs
description: 在本教學課程中，您會在由教師設定的教室實驗室中存取虛擬機器。
services: devtest-lab, lab-services, virtual-machines
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.custom: mvc
ms.date: 09/19/2019
ms.author: spelluru
ms.openlocfilehash: 2ac9e8b8d0635eceb7d4f85ad867b102f7d064f5
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/04/2019
ms.locfileid: "73585115"
---
# <a name="how-to-access-a-classroom-lab-in-azure-lab-services"></a>如何存取 Azure 實驗室服務中的教室實驗室
本文說明如何向教室實驗室註冊、檢視您可以存取的所有實驗室、啟動/停止實驗室中的 VM，以及連線至 VM。 

## <a name="register-to-the-lab"></a>向實驗室註冊

1. 瀏覽至教師/授課者提供給您的**註冊 URL**。 完成註冊之後，您不需要使用註冊 URL。 請改用 URL：[https://labs.azure.com](https://labs.azure.com)。 目前尚未支援 Internet Explorer 11。 
1. 使用學校帳戶登入服務，以完成註冊。 

    > [!NOTE]
    > 使用 Azure 實驗室服務時需要 Microsoft 帳戶。 如果您嘗試使用非 Microsoft 帳戶 (例如 Yahoo 或 Google 帳戶) 來登入入口網站，請依照指示來建立將連結至非 Microsoft 帳戶的 Microsoft 帳戶。 然後遵循步驟來完成註冊程序。 
1. 註冊之後，請確認您有看到可存取的實驗室虛擬機器。 
1. 請等候虛擬機器準備就緒。 在 [VM] 圖格上，請注意下欄欄位：
    1. 在圖格頂端，您會看到**實驗室的名稱**。
    1. 在圖格右邊，您會看到代表 VM **作業系統 (OS)** 的圖示。 在此範例中為 Windows OS。 
    1. 您會在圖格底部看到用來啟動/停止 VM 和連線至 VM 的圖示/按鈕。 
    1. 在按鈕右邊，您會看到 VM 的狀態。 確認您看到的 VM 狀態為 [已停止]  。

        ![處於已停止狀態的 VM](../media/tutorial-connect-vm-in-classroom-lab/vm-in-stopped-state.png)

## <a name="start-or-stop-the-vm"></a>啟動或停止 VM
1. 選取第一個按鈕來**啟動** VM，如下圖所示。 這個程序需要一些時間。  

    ![啟動 VM](../media/tutorial-connect-vm-in-classroom-lab/start-vm.png)
4. 確認 VM 的狀態已設定為 [執行中]  。 

    ![處於執行中狀態的 VM](../media/tutorial-connect-vm-in-classroom-lab/vm-running.png)

    請注意，第一個按鈕的圖示會變更為代表**停止**作業。 您可以選取此按鈕來停止 VM。 

## <a name="connect-to-the-vm"></a>連接至 VM

1. 選取第二個按鈕 (如下圖所示)，以**連線**至實驗室的 VM。 

    ![連接到 VM](../media/tutorial-connect-vm-in-classroom-lab/connect-vm.png)
2. 執行下列其中一個步驟： 
    1. 針對 **Windows** 虛擬機器，將 **RDP** 檔案儲存至硬碟。 開啟 RDP 檔案以連線至虛擬機器。 使用授課者/教授提供給您的**使用者名稱**和**密碼**來登入機器。 
    3. 對於 **Linux** 虛擬機器，您可以使用 **SSH** 或 **RDP** (若已啟用) 進行連線。 如需詳細資訊，請參閱[啟用 Linux 機器的遠端桌面連線](how-to-enable-remote-desktop-linux.md)。 
    1. 如果您使用 **Mac** 連線至實驗室 VM，請依照下一節中的指示操作。 

## <a name="connect-to-a-vm-using-rdp-on-a-mac"></a>在 Mac 上使用 RDP 連線至 VM
本節說明學生如何使用 RDP 從 Mac 連線至 VM。

### <a name="step-1-install-microsoft-remote-desktop-on-a-mac"></a>步驟 1：在 Mac 上安裝 Microsoft 遠端桌面
1. 在您的 Mac 上開啟 App Store，然後搜尋 **Microsoft 遠端桌面**。

    ![Microsoft 遠端桌面](../media/how-to-use-classroom-lab/install-ms-remote-desktop.png)
1. 安裝最新版的 Microsoft 遠端桌面。 

### <a name="step-2-access-the-vm-from-your-mac-using-rdp"></a>步驟 2：使用 RDP 從 Mac 存取 VM
1. 在已安裝 **Microsoft 遠端桌面**的電腦上，開啟已下載的 **RDP** 檔案。 它應該會開始連線至 VM。 

    ![連接到 VM](../media/how-to-use-classroom-lab/connect-linux-vm.png)
1. 如果出現下列警告，請選取 [繼續]  。 

    ![憑證警告](../media/how-to-use-classroom-lab/certificate-error.png)
1. 您應該會看到 VM。 

    > [!NOTE]
    > 下列範例適用於 CentOS Linux VM。 

    ![VM](../media/how-to-use-classroom-lab/vm-ui.png)

## <a name="progress-bar"></a>進度列 
圖格上的進度列會顯示針對指派給您的[配額時數](how-to-configure-student-usage.md#set-quotas-for-users)所使用的時數。 此時間是除了實驗室的排程時間外額外分配給您的時間。 進度列的色彩和進度列底下的文字會依下列案例而有所不同：

- 如果某個課程正在進行中 (在課程的排程內)，進度列會呈現灰色，表示並未使用配額時數。 

    ![灰色的進度列](../media/tutorial-connect-vm-in-classroom-lab/progress-bar-class-in-progress.png)
- 如果未指派配額 (零小時)，則會顯示**僅在課程期間可用**的文字來取代進度列。 
    
    ![未設定配額時的狀態](../media/tutorial-connect-vm-in-classroom-lab/available-during-class.png)
- 如果您**用完配額**，進度列的色彩會變成**紅色**。 

    ![紅色的進度列](../media/tutorial-connect-vm-in-classroom-lab/progress-bar-red-color.png)
- 超出實驗室的排程時間，而且已使用一些配額時間時，進度列色彩會變成**藍色**。 

    ![藍色的進度列](../media/tutorial-connect-vm-in-classroom-lab/progress-bar-blue-color.png)


## <a name="view-all-the-classroom-labs"></a>檢視所有教室實驗室
向實驗室註冊之後，您可以採取下列步驟來檢視所有教室實驗室： 

1. 瀏覽至 [https://labs.azure.com](https://labs.azure.com)。 目前尚未支援 Internet Explorer 11。 
2. 使用您用來向實驗室註冊的使用者帳戶登入服務。 
3. 確認您看到您有權存取的所有實驗室。 

    ![檢視所有連結](../media/how-to-manage-classroom-labs/all-labs.png)


## <a name="next-steps"></a>後續步驟
請參閱下列文章：

- [以管理員身分建立及管理實驗室帳戶](how-to-manage-lab-accounts.md)
- [以實驗室擁有者身分建立及管理實驗室](how-to-manage-classroom-labs.md)
- [以實驗室擁有者身分設定及發佈範本](how-to-create-manage-template.md)
- [以實驗室擁有者身分設定及控制實驗室的使用方式](how-to-configure-student-usage.md)
 