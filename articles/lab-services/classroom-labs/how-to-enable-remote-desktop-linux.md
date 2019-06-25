---
title: 適用於 Linux Azure 實驗室服務中啟用遠端桌面 |Microsoft Docs
description: 了解如何啟用遠端桌面的 Linux 虛擬機器的實驗室中 Azure 實驗室服務。
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
ms.date: 05/24/2019
ms.author: spelluru
ms.openlocfilehash: 389d467bd9672743d4a086e8a1c505fb0366dba7
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "66237063"
---
# <a name="enable-and-use-remote-desktop-for-linux-virtual-machines-in-a-lab-in-azure-lab-services"></a>啟用及使用 Azure 實驗室服務中的實驗室中的 Linux 虛擬機器的遠端桌面
這篇文章會示範如何執行下列工作：

- 針對 Linux VM 啟用遠端桌面
- 如何老師可以連線到 VM 透過遠端桌面連線 (RDP) 的範本。
- 學生要如何連接到 VM via RDP 的學生

## <a name="enable-remote-desktop-for-linux-vm"></a>針對 Linux VM 啟用遠端桌面
在實驗室建立時，可以讓老師**遠端桌面連線**for **Linux**映像。 **啟用遠端桌面連線**選項會顯示當選取的範本在 Linux 映像。 啟用此選項時，老師可以連接到範本中，VM 和學生 Vm 透過 RDP （遠端桌面）。 

![啟用遠端桌面連接 Linux 映像](../media/how-to-enable-remote-desktop-linux/enable-rdp-option.png)

在 **啟用遠端桌面連線**訊息方塊中，選取**繼續使用遠端桌面**。 

![啟用遠端桌面連接 Linux 映像](../media/how-to-enable-remote-desktop-linux/enabling-remote-desktop-connection-dialog.png)

> [!IMPORTANT] 
> 啟用**遠端桌面連線**只會開啟**RDP** Linux 機器上的連接埠。 您身為老師，連線到第一次，使用 SSH 的 Linux 機器，並安裝 RDP 和 GUI 的套件，以便您可以連線到使用 RDP 的 Linux 機器。 然後，您要**發佈**映像，讓學生可以中的 RDP 至學生的 Linux Vm。 

## <a name="supported-operating-systems"></a>受支援的作業系統
目前，下列作業系統支援遠端桌面連線：

- openSUSE Leap 42.3
- CentOS 型 7.5
- Debian 9 "Stretch"
- Ubuntu Server 16.04 LTS

## <a name="teachers-connecting-to-the-template-vm-using-rdp"></a>連接至 VM 範本的老師使用 RDP
教師必須連接至 VM 範本前，使用 SSH 和在其上安裝 RDP 和 GUI 的封裝。 然後，老師可以使用下列步驟來連線到使用 RDP 的 Linux Vm: 

您會看到**遠端桌面**在建立實驗室連接至 VM 範本的選項。 

![在建立時連接至透過 RDP 的範本](../media/how-to-enable-remote-desktop-linux/connect-at-creation.png)

您會看到**遠端桌面**開始建立實驗室之後，在實驗室的首頁頁面和範本的 VM 上的選項。 如果未啟動，請啟動 VM 的範本。 

![建立實驗室之後，連線到透過 RDP 的範本](../media/how-to-enable-remote-desktop-linux/rdp-after-lab-creation.png) 

如需有關如何使用 SSH 或 RDP 的 vm 連接的詳細資訊，請參閱 [使用 SSH 或 RDP]((#connect-using-ssh-or-rdp) 的連線。 

## <a name="teachers-connecting-to-a-student-vm-using-rdp"></a>連接到 VM 的學生的老師使用 RDP
老師/教授可以連線到 VM 的學生藉由切換至**虛擬機器**檢視，然後選取**連線**圖示。 之前，必須老師**發佈**搭配 RDP 和 GUI 的封裝，在其上安裝的範本映像。 

![連接到 VM 的學生的教師方案](../media/how-to-enable-remote-desktop-linux/teacher-connect-to-student-vm.png)

如需有關如何使用 SSH 或 RDP 的 vm 連接的詳細資訊，請參閱 [使用 SSH 或 RDP]((#connect-using-ssh-or-rdp) 的連線。 

## <a name="students-connecting-to-the-student-vm"></a>連接到 VM 的學生的學生
學生在實驗室擁有者 （老師/教授） 之後可以在其 Linux vm 的 RDP**發佈**電腦上安裝的範本 VM 使用 RDP 和 GUI 的套件。 步驟如下： 

1. 當學生登入實驗室入口網站直接 (`https://labs.azure.com`) 或使用的註冊連結 (`https://labs.azure.com/register/<registrationCode>`)，顯示圖格的每個實驗室學生具有存取權。 
2. 在 [] 磚中，選取**啟動**如果 VM 已停止。 
3. 選取 [ **連接**]。 您會看到兩個選項可以連線到 VM:**SSH**並**遠端桌面**。

    ![學生 VM-連接選項](../media/how-to-enable-remote-desktop-linux/student-vm-connect-options.png)

## <a name="connect-using-ssh-or-rdp"></a>使用 SSH 或 RDP 連線
如果您選取**SSH**選項，您會看到下列**連線到您的虛擬機器** 對話方塊中：  

![SSH 連接字串](../media/how-to-enable-remote-desktop-linux/ssh-connection-string.png)

選取 **複製**按鈕的文字方塊中，將它複製到剪貼簿。 將儲存的 SSH 連接字串。 使用此連接字串，從 SSH 終端機 (例如 [Putty](https://www.putty.org/)) 連線至虛擬機器。

如果您選取**RDP**選項，RDP 檔案下載到您的電腦。 將它儲存，並將它開啟以連接至電腦。 

## <a name="next-steps"></a>後續步驟
請參閱下列文章：

- [以管理員身分建立及管理實驗室帳戶](how-to-manage-lab-accounts.md)
- [以實驗室擁有者身分建立及管理實驗室](how-to-manage-classroom-labs.md)
- [以實驗室擁有者身分設定及發佈範本](how-to-create-manage-template.md)
- [以實驗室使用者的身分存取教室實驗室](how-to-use-classroom-lab.md)

