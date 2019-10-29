---
title: 追蹤 Azure 實驗室服務中的實驗室的使用情況 | Microsoft Docs
description: 在本教學課程中，身為實驗室建立者/擁有者的您可以追蹤實驗室的使用情況。
services: lab-services
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
ms.date: 10/18/2019
ms.author: spelluru
ms.openlocfilehash: 842392ab425628a1c82a39e25a65066064747211
ms.sourcegitcommit: 9a4296c56beca63430fcc8f92e453b2ab068cc62
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/20/2019
ms.locfileid: "72675774"
---
# <a name="tutorial-track-usage-of-a-lab-in-azure-lab-service"></a>教學課程：追蹤實驗室在 Azure 實驗室服務中的使用情形
本教學課程會說明實驗室建立者/擁有者如何追蹤實驗室的使用情況。

在本教學課程中，您會執行下列動作：

> [!div class="checklist"]
> * 檢視已向實驗室註冊的使用者
> * 檢視實驗室中 VM 的使用情況
> * 管理學生 VM 


## <a name="view-users-registered-with-the-lab"></a>檢視已向實驗室註冊的使用者

1. 瀏覽至 [Azure 實驗室服務網站](https://labs.azure.com)。 
2. 選取 [登入]  ，然後輸入您的認證。 Azure 實驗室服務支援組織帳戶和 Microsoft 帳戶。
3. 在 [我的實驗室]  頁面上，選取您要追蹤其使用情況的實驗室。 
4. 選取左側功能表或 [使用者]  圖格上的 [使用者]  。 您會看到已向實驗室註冊的學生。 選取 [註冊連結]  、複製該連結，並將連結傳送給任何尚未向實驗室完成註冊的新學生。 

    ![已註冊的使用者](../media/tutorial-track-usage/registered-users.png)

## <a name="view-the-usage-of-vms-in-the-lab"></a>檢視實驗室中 VM 的使用情況 

1. 在左側的功能表上選取 [虛擬機器]  。 
2. 確認您有看到 VM 的狀態和 VM 已執行的時數。 實驗室擁有者花在學生 VM 的時間，不會計入最後一個資料行所示的使用時間中。 

    ![VM 使用方式](../media/tutorial-track-usage/vm-usage.png)

## <a name="manage-student-vms"></a>管理學生 VM 
在此頁面上，您可以使用 [狀態]  資料行中的下拉式清單或工作列上的按鈕來啟動、停止或重設學生 VM。 

![VM 控制項](../media/tutorial-track-usage/vm-controls.png)

您也可以使用工具列按鈕來啟動、停止或刪除 VM。 


## <a name="next-steps"></a>後續步驟
若要深入了解教室實驗室，請參閱[操作指南](how-to-manage-lab-accounts.md)底下的文章。
