---
title: 設定 Azure 實驗室服務的教室實驗室中的使用方式設定 | Microsoft Docs
description: 了解如何設定實驗室的使用者數目、讓他們向實驗室註冊、控制他們可以使用 VM 的時數等等。
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
ms.date: 06/11/2019
ms.author: spelluru
ms.openlocfilehash: 86f22864c416ad2a90bea09c02675d6eb3322308
ms.sourcegitcommit: 04ec7b5fa7a92a4eb72fca6c6cb617be35d30d0c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/22/2019
ms.locfileid: "68385650"
---
# <a name="configure-usage-settings-and-policies"></a>設定使用方式設定和原則
本文說明如何將使用者新增至實驗室、讓他們向實驗室註冊、控制他們可以使用 VM 的時數等等。 


## <a name="add-users-to-the-lab"></a>將使用者新增至實驗室
如果您已啟用 [限制存取]，請將使用者 (電子郵件地址) 新增至清單。

1. 選取左側功能表上的 [使用者]。
2. 選取工具列上的 [新增使用者]。 

    ![[新增使用者] 按鈕](../media/how-to-configure-student-usage/add-users-button.png)
1. 在 [新增使用者] 頁面上的不同行或以分號隔開的單一行中，輸入使用者的電子郵件地址。 

    ![新增使用者電子郵件地址](../media/how-to-configure-student-usage/add-users-email-addresses.png)
4. 選取 [ **儲存**]。 您會在清單中看到使用者的電子郵件地址及其狀態 (是否已註冊)。 

    ![使用者清單](../media/how-to-configure-student-usage/users-list-new.png)

## <a name="share-registration-link-with-students"></a>與學生分享註冊連結
若要將註冊連結傳送給學生, 請使用下列其中一種方法。 第一種方法會示範如何使用註冊連結和選擇性訊息, 將電子郵件傳送給學生。 第二種方法會向您示範如何取得註冊連結, 以您想要的方式與其他人共用。 

如果實驗室啟用 [限制存取]，則只有使用者清單中的使用者可以使用註冊連結向實驗室註冊。 預設會啟用此選項。 

### <a name="send-email-to-users"></a>傳送電子郵件給使用者
Azure 實驗室服務可讓教師將實驗室邀請電子郵件傳送給所有或選取的學生, 而不需要使用另一個電子郵件用戶端。 教師可以將滑鼠停留在清單中的個別學生上, 以查看每個學生的電子郵件圖示, 或選取一或多個學生, 並使用工具列上的 [**傳送邀請**]。 這項功能會傳送電子郵件, 其中包含註冊連結和老師新增的訊息 (如果有的話)。 邀請傳送之後, 邀請狀態會變更為 [**已傳送邀請**], 讓老師能夠追蹤哪些學生已收到註冊連結和傳送日期。

1. 如果您尚未在 [使用者] 頁面上，請切換至該檢視。 
2. 在清單中選取特定或所有使用者。 若要選取特定使用者，選取清單第一個資料行中的核取方塊。 若要選取所有使用者，則選取第一個資料行標題 (**名稱**) 前面的核取方塊，或全部選取清單中所有使用者的核取方塊。 您可以在此清單中查看**邀請狀態**的狀態。  在下圖中，所有學員的邀請狀態會設為 [邀請未送出]。 

    ![選取學員](../media/tutorial-setup-classroom-lab/select-students.png)
1. 選取其中一個資料列中的 [電子郵件圖示 (信封)]，或選取工具列上的 [傳送邀請]。 您也可以將滑鼠停留在清單中的學生名稱上，即可看到電子郵件圖示。 

    ![透過電子郵件傳送註冊連結](../media/tutorial-setup-classroom-lab/send-email.png)
4. 在 [透過電子郵件傳送註冊連結] 頁面上，遵循下列步驟： 
    1. 輸入您想要傳送給學生的**選擇性訊息**。 電子郵件會自動包含註冊連結。 
    2. 在 [透過電子郵件傳送註冊連結] 頁面上，選取 [傳送]。 您會看到邀請狀態變更為 [正在傳送邀請]，然後變更為 [邀請已送出]。 
        
        ![邀請已送出](../media/tutorial-setup-classroom-lab/invitations-sent.png)

## <a name="get-registration-link"></a>取得註冊連結
1. 選取左側功能表上的 [使用者]，以切換至 [使用者] 檢視。 
2. 選取 [取得註冊連結] 圖格。

    ![學生註冊連結](../media/tutorial-setup-classroom-lab/dashboard-user-registration-link.png)
1. 在 [使用者註冊] 對話方塊中，選取 [複製] 按鈕。 連結會複製到剪貼簿中。 將連結貼到電子郵件編輯器中，然後將電子郵件傳送給學生。 

    ![學生註冊連結](../media/tutorial-setup-classroom-lab/registration-link.png)
2. 在 [使用者註冊] 對話方塊上，選取 [關閉]。 
4. 與學生共用**註冊連結**, 讓學生可以註冊該類別。 

## <a name="view-users-registered-with-the-lab"></a>檢視已向實驗室註冊的使用者

選取左側功能表上的 [使用者]，查看已向實驗室註冊的使用者清單。 

![已向實驗室註冊的使用者清單](../media/how-to-configure-student-usage/users-list-new.png)

## <a name="set-quotas-for-users"></a>設定使用者的配額
您可以使用下列步驟來設定每位使用者的配額： 

1. 如果頁面還不是在作用中，請選取左側功能表上的 [使用者]。 
2. 選取**每位使用者的配額:工具列上**有10小時。 
3. 在 [每位使用者的配額] 頁面上，指定您想要給予每位使用者 (學生) 的時數： 
    1. **每位使用者的實驗室總時數**。 使用者**除了排定的時間之外**，還可以在設定的時數 (針對此欄位指定) 內使用其 VM。 如果選取此選項，請在文字方塊中輸入**時數**。 

        ![每位使用者的時數](../media/how-to-configure-student-usage/number-of-hours-per-user.png). 
    1. **0 小時 (僅排程)** 。 使用者只能在排定的時間期間或是當身為試驗室擁有者的您為他們開啟 VM 時，才能使用他們的 VM。

        ![零小時 - 僅排定的時間](../media/how-to-configure-student-usage/zero-hours.png)
    4. 選取 [ **儲存**]。 
5. 您會立即在工具列上看到變更的值：**每位使用者的配額：&lt;時數&gt;** 。 

    ![每位使用者的配額](../media/how-to-configure-student-usage/quota-per-user.png)



> [!IMPORTANT]
> 將註冊連結傳送給學生之前, 老師必須設定類別的排程 (如果他們選擇0個配額時數, 或指定實驗室的配額時數)。
>
> VM 的排程執行時間不會計入[分配給使用者的配額](how-to-create-schedules.md)中。 該配額用於學生在排程時間以外花費於 VM 上的時間。 

### <a name="add-users-by-uploading-a-csv-file"></a>上傳 CSV 檔案以新增使用者
您也可以上傳具有使用者電子郵件地址的 CSV 檔案，藉此新增使用者。

1. 建立將使用者的電子郵件地址都放在一個資料行中的 CSV 檔案。

    ![每位使用者的配額](../media/how-to-configure-student-usage/csv-file-with-users.png)
2. 在實驗室的 [使用者] 頁面上，選取工具列上的 [上傳 CSV]。

    ![[上傳 CSV] 按鈕](../media/how-to-configure-student-usage/upload-csv-button.png)
3. 選取具有使用者電子郵件地址的 CSV 檔案。 當您在選取 CSV 檔案之後選取 [開啟] 時，會看到以下的 [新增使用者] 視窗。 電子郵件地址清單中會填入來自 CSV 檔案的電子郵件地址。 

    ![[新增使用者] 視窗中填入來自 CSV 檔案的電子郵件地址](../media/how-to-configure-student-usage/add-users-window.png)
4. 在 [新增使用者] 視窗中選取 [儲存]。 
5. 確認您在使用者清單中看到使用者。 

    ![已新增的使用者清單](../media/how-to-configure-student-usage/list-of-added-users.png)

## <a name="manage-user-vms"></a>管理使用者 VM
一旦學生使用您提供的連結向 Azure Lab Services 註冊，您就會在 [虛擬機器] 索引標籤上看見已指派給學生的 VM。 

![已指派給學生的虛擬機器](../media/how-to-manage-classroom-labs/virtual-machines-students.png)

您可以在學生 VM 上執行下列工作： 

- 如果 VM 正在執行，請停止 VM。 
- 如果 VM 已停止，請啟動 VM。 
- 連線至 VM。 
- 刪除 VM。 
- 檢視使用者使用虛擬機器的時數。 

## <a name="update-number-of-virtual-machines-in-lab"></a>更新實驗室中的虛擬機數目
若要更新實驗室中的虛擬機器數目，請執行下列 [虛擬機器] 頁面中的步驟：

1. 在左功能表上，選取 [虛擬機器]。 
2. 在工具列上選取 [實驗室容量： **&lt;數字&gt; 台機器**]。 
3. 輸入虛擬機器的**數字**。
4. 選取 [ **儲存**]。

    ![實驗室中的虛擬機器](../media/how-to-configure-student-usage/number-virtual-machines.png)


## <a name="next-steps"></a>後續步驟
請參閱下列文章：

- [以管理員身分建立及管理實驗室帳戶](how-to-manage-lab-accounts.md)
- [以實驗室擁有者身分建立及管理實驗室](how-to-manage-classroom-labs.md)
- [以實驗室擁有者身分設定及發佈範本](how-to-create-manage-template.md)
- [以實驗室使用者的身分存取教室實驗室](how-to-use-classroom-lab.md)
