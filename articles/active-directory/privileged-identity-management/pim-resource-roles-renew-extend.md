---
title: 在 PIM 中延長或更新 Azure 資源角色指派-Azure Active Directory |Microsoft Docs
description: 了解如何在 Azure AD Privileged Identity Management (PIM) 中延長或更新 Azure 資源角色指派。
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: pim
ms.date: 04/02/2018
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9eeba148945f7aa52dd32edc0fec4c45e2ab0748
ms.sourcegitcommit: 95b180c92673507ccaa06f5d4afe9568b38a92fb
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/08/2019
ms.locfileid: "70804128"
---
# <a name="extend-or-renew-azure-resource-role-assignments-in-pim"></a>在 PIM 中延長或更新 Azure 資源角色指派

Azure Active Directory （Azure AD） Privileged Identity Management （PIM）引進新的控制項來管理 Azure 資源的存取和指派生命週期。 管理員可以使用開始和結束日期/時間屬性來指派成員資格。 當指派的結束日期接近時，PIM 會傳送電子郵件通知給受影響的使用者或群組。 也會傳送電子郵件通知給資源的系統管理員，以確保能維護正確的存取權。 如果存取權未延長，指派可能會進行更新，而且仍會以過期狀態顯示長達 30 天。

## <a name="who-can-extend-and-renew"></a>誰可以延長和更新？

只有資源的管理員可以延長或更新角色指派。 受影響的成員可以要求延長即將過期的角色，以及要求更新已經過期的角色。

## <a name="when-are-notifications-sent"></a>何時傳送通知？

PIM 會在 14 天內和到期的前一天，將電子郵件通知傳送給角色的管理員及受影響的成員。 當指派正式到期時，會傳送額外的電子郵件。 

當即將過期或已過期角色的成員要求延長或更新時，管理員就會收到通知。 當特定管理員解決要求時，所有其他管理員會獲知解決決策 (核准或拒絕)。 接著，要求成員也會獲知決策。 

## <a name="extend-role-assignments"></a>延長角色指派

下列步驟會概述要求程序、解決或管理角色指派延長或更新。 

### <a name="member-extend"></a>成員延長

角色指派的成員可以直接從資源的 [我的角色] 頁面上的 [合格] 或 [有效] 索引標籤以及從 PIM 入口網站的最上層 [我的角色] 頁面，延長即將過期的角色指派。 成員可以要求延長在 14 天後過期的合格和有效 (已指派) 角色。

![Azure 資源-[我的角色] 頁面，列出具有 [動作] 資料行的合格角色](media/pim-resource-roles-renew-extend/aadpim-rbac-extend-ui.png)

如果指派結束日期/時間在 14 天內，則 [延長] 按鈕會變成使用者介面中的有效連結。 在下列範例中，假設目前日期為 3 月 27 日。

![具有要啟用或擴充之連結的動作資料行](media/pim-resource-roles-renew-extend/aadpim-rbac-extend-within-14.png)

若要要求延長此角色指派，請選取 [延長] 來開啟要求表單。

![使用原因方塊擴充角色指派窗格](media/pim-resource-roles-renew-extend/aadpim-rbac-extend-role-assignment-request.png)

若要檢視原始指派相關資訊，請展開 [指派詳細資料]。 輸入延長要求的原因，然後選取 [延長]。

>[!Note]
>建議包含為何需要延長的詳細資料，以及應該給予多久的延長 (如果您有此資訊的話)。

![以擴充的指派詳細資料擴充角色指派窗格](media/pim-resource-roles-renew-extend/aadpim-rbac-extend-form-complete.png)

過了一會兒，資源管理員會收到電子郵件通知，要求他們檢閱延長要求。 如果已經提交延長要求，則 Azure 入口網站的頂端會出現快顯通知來說明錯誤。

![通知，說明已經有現有的暫止角色指派延伸模組](media/pim-resource-roles-renew-extend/aadpim-rbac-extend-failed-existing-request.png)

移至左窗格中的 [**擱置要求**] 頁面，以查看要求的狀態或將其取消。

![Azure 資源-[擱置要求] 頁面，其中列出任何擱置的要求和要取消的連結](media/pim-resource-roles-renew-extend/aadpim-rbac-extend-cancel-request.png)

### <a name="admin-approve"></a>管理員核准

當成員提交延長角色指派的要求時，資源管理員會收到電子郵件通知，其中包含原始指派的詳細資訊以及要求的原因。 通知包含可供管理員核准或拒絕要求的直接連結。 

除了遵循電子郵件中的連結，管理員可以前往 PIM 管理入口網站並從左側窗格中選取 [核准要求]，以核准或拒絕要求。

![Azure 資源-核准要求頁面，列出要核准或拒絕的要求和連結](media/pim-resource-roles-renew-extend/aadpim-rbac-extend-admin-approve-grid.png)

當管理員選取 [核准] 或 [拒絕] 時，要求的詳細資料會顯示，連同一個可提供理由作為稽核記錄的欄位。

![以要求者原因、指派類型、開始時間、結束時間和原因核准角色指派要求](media/pim-resource-roles-renew-extend/aadpim-rbac-extend-admin-approve-blade.png)

資源管理員在核准延長角色指派的要求時，可以選擇新的開始日期、結束日期和指派類型。 如果管理員想要提供有限的存取權來完成特定工作 (例如一天)，則可能需要變更指派類型。 在此範例中，管理員可將指派從 [合格] 變更為 [有效]。 這表示他們可以提供存取權給要求者，而不用要求他們啟用。

### <a name="admin-extend"></a>管理員延長

如果角色成員忘記或無法要求角色成員資格延長，則管理員可以代表成員延長指派。 角色成員資格的管理延長不需要核准，但系統會在角色延長時，傳送通知給所有其他管理員。

若要延長角色成員資格，請瀏覽至 PIM 中的資源角色或成員檢視。 尋找需要延長的成員。 然後在動作欄中選取 [延長]。

![Azure 資源-[成員] 頁面，其中列出具有要擴充連結的合格角色](media/pim-resource-roles-renew-extend/aadpim-rbac-extend-admin-extend.png)

## <a name="renew-role-assignments"></a>更新角色指派

雖然在概念上類似要求延長的程序，但更新已過期角色指派的程序其實不同。 使用下列步驟，成員和管理員即可視需要更新已過期角色的存取權。

### <a name="member-renew"></a>成員更新

無法再存取資源的成員可以存取到期的指派歷程記錄達 30 天。 若要這樣做，請瀏覽至左側窗格中的 [我的角色]，然後選取 Azure 資源角色區段中的 [到期的角色]。

![[我的角色] 頁面-過期的角色索引標籤](media/pim-resource-roles-renew-extend/aadpim-rbac-renew-from-myroles.png)

顯示的角色清單會預設為 [合格角色]。 使用下拉式功能表來切換 [合格] 與 [有效] 指派的角色。

若要要求更新清單中的任何角色指派，請選取 [更新] 動作。 然後提供要求的原因。 除了將協助資源管理員決定核准或拒絕的任何其他內容，提供持續時間也很有用。

![[更新角色指派] 窗格顯示原因方塊](media/pim-resource-roles-renew-extend/aadpim-rbac-renew-request-form.png)

提交要求後，資源管理員會獲知更新角色指派的要求擱置。

### <a name="admin-approves"></a>管理員核准

資源管理員可以從電子郵件通知中的連結，或藉由從 Azure 入口網站存取 PIM 並從左側窗格中選取 [核准要求]，進而存取更新要求。

![Azure 資源-核准要求頁面，列出要核准或拒絕的要求和連結](media/pim-resource-roles-renew-extend/aadpim-rbac-extend-admin-approve-grid.png)

當管理員選取 [核准] 或 [拒絕] 時，要求的詳細資料會顯示，連同一個可提供理由作為稽核記錄的欄位。

![以要求者原因、指派類型、開始時間、結束時間和原因核准角色指派要求](media/pim-resource-roles-renew-extend/aadpim-rbac-extend-admin-approve-blade.png)

資源管理員在核准更新角色指派的要求時，必須輸入新的開始日期、結束日期和指派類型。 

### <a name="admin-renew"></a>管理員更新

資源管理員可以從資源左側導覽功能表中的 [成員] 索引標籤，更新已過期的角色指派。 他們也可從資源角色的 [到期的角色] 索引標籤內，更新已過期的角色指派。

若要檢視所有已過期的角色指派清單，請從 [成員] 畫面中選取 [到期的角色]。

![Azure 資源-[成員] 頁面列出已過期的角色，並提供更新的連結](media/pim-resource-roles-renew-extend/aadpim-rbac-renew-from-member-blade.png)

## <a name="next-steps"></a>後續步驟

- [在 PIM 中核准或拒絕 Azure 資源角色的要求](pim-resource-roles-approval-workflow.md)
- [在 PIM 中設定 Azure 資源角色設定](pim-resource-roles-configure-role-settings.md)
