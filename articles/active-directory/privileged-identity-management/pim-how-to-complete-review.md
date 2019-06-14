---
title: 完成 Azure AD PIM-Azure Active Directory 中的角色的存取權檢閱 |Microsoft Docs
description: 了解如何完成在 Azure AD Privileged Identity Management (PIM) 中的 Azure AD 角色的存取權檢閱，並檢視結果
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 06/06/2017
ms.author: rolyon
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0a7fa3bfe159620130bc0962b470cea8e7422646
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "65602152"
---
# <a name="complete-an-access-review-of-azure-ad-roles-in-pim"></a>完成 Azure AD PIM 中的角色的存取權檢閱
在[開始存取權檢閱](pim-how-to-start-security-review.md)之後，特殊權限角色管理員就可以檢閱特殊權限存取權。 Azure Active Directory (Azure AD) Privileged Identity Management (PIM) 會自動傳送電子郵件，提示使用者檢閱其存取權。 如果使用者未收到電子郵件，您可以將[如何執行存取權檢閱](pim-how-to-perform-security-review.md)中的指示傳送給他們。

存取權檢閱期間結束後，或所有使用者都已完成其自我檢閱後，請遵循本文的步驟來管理檢閱並查看結果。

## <a name="manage-access-reviews"></a>管理存取權檢閱
1. 移至 [Azure 入口網站](https://portal.azure.com/)，然後在儀表板上選取 [Azure AD Privileged Identity Management]  應用程式。
2. 選取儀表板的 [存取權檢閱]  區段。
3. 選取您想要管理的存取權檢閱。

在存取權檢閱的詳細資料刀鋒視窗上，有一些可管理該檢閱的選項。

![PIM 存取權檢閱按鈕 - 螢幕擷取畫面](./media/pim-how-to-complete-review/review-buttons.png)

### <a name="remind"></a>提醒
如果將存取權檢閱設定成讓使用者自我檢閱，[提醒]  按鈕就會傳送通知。 

### <a name="stop"></a>Stop
所有的存取權檢閱都有結束日期，但是您可以使用 [停止]  按鈕來提早結束檢閱。 如果此時有任何使用者尚未受到檢閱，在您停止檢閱之後，他們將無法受到檢閱。 在停止檢閱之後，即無法重新開始該檢閱。

### <a name="apply"></a>套用
在存取權檢閱因您已達到結束日期或手動停止它而完成之後，[套用]  按鈕就會實作該檢閱的結果。 如果使用者的存取權在檢閱中被拒絕，則在這個步驟就會將其角色指派移除。  

### <a name="export"></a>匯出
如果想要手動套用存取權檢閱的結果，您可以匯出檢閱。 [匯出]  按鈕會開始下載 CSV 檔案。 您可以在 Excel 或開啟 CSV 檔案的其他程式中管理結果。

### <a name="delete"></a>Delete
如果對檢閱不再有任何興趣，請刪除它。 [刪除]  按鈕會將檢閱從 PIM 應用程式中移除。

> [!IMPORTANT]
> 刪除執行前，您不會收到警告，因此請務必確定您想要刪除該檢閱。 

## <a name="next-steps"></a>後續步驟

- [在 PIM 中啟動 Azure AD 角色的存取權檢閱](pim-how-to-start-security-review.md)
- [在 PIM 中執行我的 Azure AD 角色的存取權檢閱](pim-how-to-perform-security-review.md)
