---
title: 如何在 Azure AD-Azure Active Directory 中禁止弱式密碼
description: 使用 Azure AD 動態禁用密碼在環境中禁用弱式密碼
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 07/11/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: 28201e09a4025c0c8820abc6836a5923e48eb885
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "66742298"
---
# <a name="configuring-the-custom-banned-password-list"></a>設定自訂禁用密碼清單

許多組織發現，其使用者會使用常見的當地用語 (例如，學校、球隊或知名人士) 建立密碼，因此很容易讓人猜到。 當使用者和系統管理員嘗試變更或重設密碼時，Microsoft 的自訂禁用密碼清單可讓組織在全域禁用密碼清單外，再新增要評估和封鎖的字串。

## <a name="add-to-the-custom-list"></a>新增自訂清單

要設定自訂禁用密碼清單，需要有 Azure Active Directory Premium P1 或 P2 授權。 如需詳細的 Azure Active Directory 授權的相關資訊，請參閱[Azure Active Directory 定價頁面](https://azure.microsoft.com/pricing/details/active-directory/)。

1. 登入[Azure 入口網站](https://portal.azure.com)並瀏覽至**Azure Active Directory**，**驗證方法**，然後**密碼保護**。
1. 將 [強制使用自訂清單]  選項設定為 [是]  。
1. 在 [自訂禁用密碼清單]  中新增字串，每行一個字串
   * 自訂禁用密碼清單最多可以包含 1000 個字組。
   * 自訂禁用密碼清單不會區分大小寫。
   * 自訂禁用密碼清單會考量常見字元替代。
      * 範例："o" 和 "0"，或是 "a" 和 "\@"
   * 最小字串長度為 4 個字元，最大長度為 16 個字元。
1. 新增完所有字串時，按一下 [儲存]  。

> [!NOTE]
> 可能需要數小時才會套用自訂禁用密碼清單的更新。

![在 Azure 入口網站中的 [驗證方法] 下修改自訂禁用密碼清單](./media/howto-password-ban-bad/authentication-methods-password-protection.png)

## <a name="how-it-works"></a>運作方式

每次有使用者或系統管理員重設或變更 Azure AD 密碼時，密碼都會流經禁用密碼清單以確認其不在清單上。 使用 Azure AD 所設定或變更的密碼中，都會包含這項檢查。

## <a name="what-do-users-see"></a>使用者看到的內容

當使用者嘗試將密碼重設為會禁用的密碼時，便會看到下列錯誤訊息：

不幸的是，您的密碼包含單字、片語或模式，可輕易猜到您的密碼。 請使用不同的密碼再試一次。

## <a name="next-steps"></a>後續步驟

[禁用密碼清單的概念性概觀](concept-password-ban-bad.md)

[Azure AD 密碼保護的概念性概觀](concept-password-ban-bad-on-premises.md)

[啟用內部部署與禁用密碼清單的整合](howto-password-ban-bad-on-premises.md)
