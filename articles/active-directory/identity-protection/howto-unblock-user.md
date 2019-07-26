---
title: 如何使用 Azure Active Directory Identity Protection 解除封鎖使用者 | Microsoft Docs
description: 了解如何解鎖由 Azure Active Directory Identity Protection 原則所封鎖的使用者。
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: c28e30b8e44b6888cdb7416b9c7b563b955a68ce
ms.sourcegitcommit: e9c866e9dad4588f3a361ca6e2888aeef208fc35
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/19/2019
ms.locfileid: "68335372"
---
# <a name="how-to-unblock-users"></a>如何：解除封鎖使用者

使用 Azure Active Directory Identity Protection 時，如果符合設定的條件，您就可以設定原則來封鎖使用者。 一般而言，遭封鎖的使用者會聯絡服務台以解除封鎖。 本文說明將遭封鎖使用者解除封鎖的執行步驟。

## <a name="determine-the-reason-for-blocking"></a>判斷封鎖的原因

解鎖使用者的第一個步驟，您需要判斷封鎖使用者的原則類型為何，以決定後續步驟。
使用 Azure Active Directory Identity Protection 時，使用者可能是因登入風險原則或使用者風險原則而遭封鎖。

您可以從使用者嘗試登入期間所出現的對話方塊標題取得封鎖使用者的原則類型︰

| 原則 | 使用者對話方塊 |
| --- | --- |
| 登入風險 |![封鎖的登入](./media/howto-unblock-user/02.png) |
| 使用者風險 |![封鎖的帳戶](./media/howto-unblock-user/104.png) |

封鎖使用者的類型為︰

* 登入風險原則，也就是可疑的登入
* 使用者風險原則，也就是有風險的帳戶

## <a name="unblocking-suspicious-sign-ins"></a>解鎖可疑的登入

若要解鎖可疑的登入，您有下列選擇︰

1. **從熟悉的位置或裝置登入** - 可疑的登入會遭封鎖通常是因為使用者嘗試從不熟悉的位置或裝置登入。 使用者可以嘗試從熟悉的位置或裝置登入，以迅速判斷這是否是遭封鎖的原因。
2. **從原則中排除** - 如果您認為目前的登入原則設定對特定使用者造成問題，您可以排除這些使用者。 如需詳細資訊，請參閱 [Azure Active Directory Identity Protection](../active-directory-identityprotection.md)。
3. **停用原則** - 如果您認為您的原則設定對所有使用者造成問題，您可以停用原則。 如需詳細資訊，請參閱 [Azure Active Directory Identity Protection](../active-directory-identityprotection.md)。

## <a name="unblocking-accounts-at-risk"></a>解鎖有風險的帳戶

若要解鎖有風險的帳戶，您有下列選擇︰

1. **重設密碼** - 您可以重設使用者的密碼。 
2. **關閉所有風險事件** - 如果已達到設定的封鎖存取權限之使用者風險層級，使用者風險原則就會封鎖使用者。 您可以手動關閉已報告的風險事件來降低使用者的風險層級。 
3. **從原則中排除** - 如果您認為目前的登入原則設定對特定使用者造成問題，您可以排除這些使用者。 如需詳細資訊，請參閱 [Azure Active Directory Identity Protection](../active-directory-identityprotection.md)。
4. **停用原則** - 如果您認為您的原則設定對所有使用者造成問題，您可以停用原則。 如需詳細資訊，請參閱 [Azure Active Directory Identity Protection](../active-directory-identityprotection.md)。

## <a name="next-steps"></a>後續步驟
 
您想要深入了解 Azure AD Identity Protection？ 查看 [Azure Active Directory Identity Protection](../active-directory-identityprotection.md)。
