---
title: 使用 Azure 實驗室服務設定教室實驗室 | Microsoft Docs
description: 在本教學課程中，您會設定在教室中使用的實驗室。
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
ms.date: 06/11/2019
ms.author: spelluru
ms.openlocfilehash: 964ecca015e440439885bbbd85cb720a3abd10a9
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/09/2019
ms.locfileid: "68883521"
---
# <a name="tutorial-set-up-a-classroom-lab"></a>教學課程：設定教室實驗室 
您將在本教學課程中設定教室實驗室，且其中包含教室中學生所使用的虛擬機器。  

在本教學課程中，您會執行下列動作：

> [!div class="checklist"]
> * 建立教室實驗室
> * 將使用者新增至實驗室
> * 將註冊連結傳送給學生

## <a name="prerequisites"></a>必要條件
若要在實驗室帳戶中設定教室實驗室，您必須是實驗室帳戶中其中一個角色的成員：擁有者、實驗室建立者或參與者。 您用來建立實驗室帳戶的帳戶會自動新增至擁有者角色。

實驗室擁有者可以將其他使用者新增至 [實驗室建立者]  角色。 例如，實驗室擁有者可將教授新增至實驗室建立者角色。 然後，教授會為其班級建立包含 VM 的實驗室。 學生會使用教授提供的註冊連結向實驗室註冊。 註冊後，他們就可以使用實驗室中的 VM 來進行班級工作和在家工作。 如需將使用者新增至實驗室建立者角色的詳細步驟，請參閱[將使用者新增至實驗室建立者角色](tutorial-setup-lab-account.md#add-a-user-to-the-lab-creator-role)。


## <a name="create-a-classroom-lab"></a>建立教室實驗室

1. 瀏覽至 [Azure 實驗室服務網站](https://labs.azure.com)。 請注意，目前不支援 Internet Explorer 11。 
2. 選取 [登入]  ，然後輸入您的認證。 Azure 實驗室服務支援組織帳戶和 Microsoft 帳戶。 
3. 在 [新增實驗室]  視窗中，執行下列動作： 
    1. 指定實驗室的**名稱**。 
    2. 指定實驗室中的**虛擬機器數目**上限。 建立實驗室之後或在現有的實驗室中，您可以增加或減少 VM 數目。 如需詳細資訊，請參閱[更新實驗室中的 VM 數目](how-to-configure-student-usage.md#update-number-of-virtual-machines-in-lab)。
    6. 選取 [ **儲存**]。

        ![建立教室實驗室](../media/tutorial-setup-classroom-lab/new-lab-window.png)
4. 在 [選取虛擬機器規格]  頁面上，執行下列步驟：
    1. 選取在實驗室中建立的虛擬機器 (VM) [大小]  。 目前只允許 [小]  、[中]  、[中 (虛擬化)]  、[大]  及 [GPU]  大小。
    3. 選取要用來在實驗室中建立 VM 的 [VM 映像]  。 如果您選取 Linux 映像，您會看到可啟用遠端桌面連接的選項。 如需詳細資料，請參閱[啟用 Linux 遠端桌面連線](how-to-enable-remote-desktop-linux.md)。
    4. 選取 [下一步]  。

        ![指定 VM 規格](../media/tutorial-setup-classroom-lab/select-vm-specifications.png)    
5. 在 [設定認證]  頁面上，指定實驗室中所有 VM 的預設認證。 
    1. 指定實驗室中所有 VM 的 [使用者名稱]  。
    2. 指定使用者的 [密碼]  。 

        > [!IMPORTANT]
        > 請記下使用者名稱和密碼。 它們將不會再次顯示。
    3. 選取 [建立]  。 

        ![設定認證](../media/tutorial-setup-classroom-lab/set-credentials.png)
6. 在 [設定範本]  頁面上，您會看到實驗室建立程序的狀態。 在實驗室中建立範本最多需要 20 分鐘。 

    ![設定範本](../media/tutorial-setup-classroom-lab/configure-template.png)
7. 範本的設定完成後，您會看到下列頁面： 

    ![在範本頁面完成後加以設定](../media/tutorial-setup-classroom-lab/configure-template-after-complete.png)
8. 在 [設定範本]  頁面上，執行下列步驟：這些都是本教學課程的**選擇性**步驟。
    2. 選取 [連線]  以連線至範本 VM。 如果是 Linux 範本 VM，您可選擇是否要使用 SSH 或 RDP 連線 (若已啟用 RDP)。
    1. 選取 [重設密碼]  以重設 VM 的密碼。 
    1. 在您的範本 VM 上安裝並設定軟體。 
    1. **停止** VM。  
    1. 輸入範本的 [描述] 
9. 選取 [範本] 頁面上的 [下一步]  。 
10. 在 [發佈範本]  頁面上，執行下列動作。 
    1. 若要立即發佈範本，請選取 [發佈]  。  

        > [!WARNING]
        > 在您發佈時，即無法取消發佈。 
    2. 若要稍後再發佈，請選取 [儲存以便稍後使用]  。 您可以在精靈完成之後發佈範本 VM。 如需如何在精靈完成之後設定和發佈的詳細資訊，請參閱[如何管理教室實驗室](how-to-manage-classroom-labs.md)一文中的[發佈範本](how-to-create-manage-template.md#publish-the-template-vm)小節。

        ![發佈範本](../media/tutorial-setup-classroom-lab/publish-template.png)
11. 您會看到範本的**發佈進度**。 此程序最多可能需要一小時。 

    ![發佈範本 - 進度](../media/tutorial-setup-classroom-lab/publish-template-progress.png)
12. 範本成功發佈後，您會看到下列頁面。 選取 [完成]  。

    ![發佈範本 - 成功](../media/tutorial-setup-classroom-lab/publish-success.png)
1. 您會看到實驗室的**儀表板**。 
    
    ![教室實驗室儀表板](../media/tutorial-setup-classroom-lab/classroom-lab-home-page.png)
4. 選取左側功能表上的 [虛擬機器] 或選取 [虛擬機器] 圖格，以切換到 [虛擬機器]  頁面。 確認您看到處於 [未指派]  狀態的虛擬機器。 這些虛擬機器尚未指派給任何學生。 它們應處於 [已停止]  狀態。 您可以在此頁面上啟動學生 VM、連線到 VM、停止 VM，以及刪除 VM。 您可以在此頁面啟動 VM，或是讓學生啟動 VM。 

    ![已停止狀態的虛擬機器](../media/tutorial-setup-classroom-lab/virtual-machines-stopped.png)

## <a name="add-users-to-the-lab"></a>將使用者新增至實驗室

1. 選取左側功能表上的 [使用者]  。 預設會啟用 [限制存取]  選項。 當此設定為開啟時，即使使用者有註冊連結，除非使用者位於使用者清單中，否則也無法向實驗室註冊。 只有清單中的使用者可以使用您傳送的註冊連結，向實驗室註冊。 在此程序中，您會在清單中新增使用者。 或者，您可以關閉 [限制存取]  ，讓使用者能向實驗室註冊 (只要他們有註冊連結)。 
2. 選取工具列上的 [新增使用者]  。 

    ![[新增使用者] 按鈕](../media/how-to-configure-student-usage/add-users-button.png)
1. 在 [新增使用者]  頁面上的不同行或以分號隔開的單一行中，輸入使用者的電子郵件地址。 

    ![新增使用者電子郵件地址](../media/how-to-configure-student-usage/add-users-email-addresses.png)
4. 選取 [ **儲存**]。 您會在清單中看到使用者的電子郵件地址及其狀態 (是否已註冊)。 

    ![使用者清單](../media/how-to-configure-student-usage/users-list-new.png)

## <a name="set-quotas-for-users"></a>設定使用者的配額
您可以使用下列步驟來設定每位使用者的配額： 

1. 如果頁面還不是在作用中，請選取左側功能表上的 [使用者]  。 
2. 選取 **[每位使用者的配額：10 小時]** (位於工具列上)。 
3. 在 [每位使用者的配額]  頁面上，指定您想要給予每位使用者 (學生) 的時數： 
    1. **每位使用者的實驗室總時數**。 使用者**除了排定的時間之外**，還可以在設定的時數 (針對此欄位指定) 內使用其 VM。 如果選取此選項，請在文字方塊中輸入**時數**。 

        ![每位使用者的時數](../media/how-to-configure-student-usage/number-of-hours-per-user.png)上也提供本文中使用的原始碼。 
    1. **0 小時 (僅排程)** 。 使用者只能在排定的時間期間或是當身為試驗室擁有者的您為他們開啟 VM 時，才能使用他們的 VM。

        ![零小時 - 僅排定的時間](../media/how-to-configure-student-usage/zero-hours.png)
    4. 選取 [ **儲存**]。 
5. 您會立即在工具列上看到變更的值：**每位使用者的配額：&lt;時數&gt;** 。 

    ![每位使用者的配額](../media/how-to-configure-student-usage/quota-per-user.png)

## <a name="set-a-schedule-for-the-lab"></a>設定實驗室的排程
如果您將配額設定設為 [0 小時 (僅排程)]  ，您必須為實驗室設定排程。 在本教學課程中，您可以將排程設為週期性每週排程。

1. 切換至 [排程]  頁面，然後選取工具列上的 [新增排程]  。 

    ![[排程] 頁面上的 [新增排程] 按鈕](../media/how-to-create-schedules/add-schedule-button.png)
2. 在 [新增排程]  頁面上，切換至頂端的 [每週]  。 
3. 針對 [排程日期 (必要)]  ，選取您要讓排程生效的日期。 下列範例中選取了星期一至星期五。 
4. 針對 [開始]  欄位，請輸入 [排程開始日期]  ，或選取 [行事曆]  按鈕以挑選日期。 這是必填欄位。 
5. 針對 [排程結束日期]  ，輸入或選取要關閉 VM 的結束日期。 
6. 針對 [開始時間]  ，選取您要啟動 VM 的時間。 若未設定停止時間，則必須要有開始時間。 如果您只要指定停止時間，請選取 [移除開始事件]  。 如果 [開始時間]  停用，請選取下拉式清單旁的 [新增開始事件]  加以啟用。 
7. 針對 [停止時間]  ，選取您要關閉 VM 的時間。 若未設定開始時間，則必須要有停止時間。 如果您只要指定開始時間，請選取 [移除停止事件]  。 如果 [停止時間]  停用，請選取下拉式清單旁的 [新增停止事件]  加以啟用。
8. 針對 [時區 (必要)]  ，為您指定的開始和停止時間選取時區。  
9. 針對 [記事]  ，輸入排程的任何描述或記事。 
10. 選取 [ **儲存**]。 

    ![每週排程](../media/how-to-create-schedules/add-schedule-page-weekly.png)

## <a name="send-an-email-with-the-registration-link"></a>傳送含有註冊連結的電子郵件

1. 如果您尚未在 [使用者]  頁面上，請切換至該檢視。 
2. 在清單中選取特定或所有使用者。 若要選取特定使用者，選取清單第一個資料行中的核取方塊。 若要選取所有使用者，則選取第一個資料行標題 (**名稱**) 前面的核取方塊，或全部選取清單中所有使用者的核取方塊。 您可以在此清單中查看**邀請狀態**的狀態。  在下圖中，所有學員的邀請狀態會設為 [邀請未送出]  。 

    ![選取學員](../media/tutorial-setup-classroom-lab/select-students.png)
1. 選取其中一個資料列中的 [電子郵件圖示 (信封)]  ，或選取工具列上的 [傳送邀請]  。 您也可以將滑鼠停留在清單中的學生名稱上，即可看到電子郵件圖示。 

    ![透過電子郵件傳送註冊連結](../media/tutorial-setup-classroom-lab/send-email.png)
4. 在 [透過電子郵件傳送註冊連結]  頁面上，遵循下列步驟： 
    1. 輸入您想要傳送給學生的**選擇性訊息**。 電子郵件會自動包含註冊連結。 
    2. 在 [透過電子郵件傳送註冊連結]  頁面上，選取 [傳送]  。 您會看到邀請狀態變更為 [正在傳送邀請]  ，然後變更為 [邀請已送出]  。 
        
        ![邀請已送出](../media/tutorial-setup-classroom-lab/invitations-sent.png)

## <a name="next-steps"></a>後續步驟
在本教學課程中，您已建立教室實驗室並已設定實驗室。 若要了解學生可以如何使用註冊連結，來存取實驗室中的 VM，請前往下一個教學課程：

> [!div class="nextstepaction"]
> [連線到教室實驗室中的 VM](tutorial-connect-virtual-machine-classroom-lab.md)

