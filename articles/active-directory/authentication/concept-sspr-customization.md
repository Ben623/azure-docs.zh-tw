---
title: 自訂自助式密碼重設-Azure Active Directory
description: Azure AD 自助式密碼重設的自訂選項
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: sahenry
ms.collection: M365-identity-device-management
ms.openlocfilehash: 039b514fe70da0e300e74bbc98a3a0f4e9ea342c
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/05/2019
ms.locfileid: "74848590"
---
# <a name="customize-the-azure-ad-functionality-for-self-service-password-reset"></a>自訂 Azure AD 的自助式密碼重設功能

想要在 Azure Active Directory (Azure AD) 中部署自助式密碼重設 (SSPR) 的 IT 專業人員可以自訂體驗以符合其使用者需求。

## <a name="customize-the-contact-your-administrator-link"></a>自訂 [請連絡您的系統管理員] 連結

自助密碼重設使用者在密碼重設入口網站中有提供他們「聯絡您的系統管理員」連結。 如果使用者選取此連結，則會執行兩個動作的其中一項：

* 如果處於預設狀態：
   * 電子郵件會傳送給您的系統管理員，並要求他們提供變更使用者密碼的協助。 請參閱下面的[範例電子郵件](#sample-email)。
* 若自訂：
   * 將您的使用者傳送至系統管理員所指定的網頁或電子郵件地址以取得協助。

> [!TIP]
> 如果您自訂此專案，建議您將此設定為已熟悉支援的使用者

> [!WARNING]
> 如果您使用需要密碼重設的電子郵件地址和帳戶來自訂此設定，使用者可能會無法尋求協助。

### <a name="sample-email"></a>範例電子郵件

![重設傳送給系統管理員之電子郵件的範例要求][Contact]

這封連絡人電子郵件會依下列順序傳送給下列收件者︰

1. 如果已指派**密碼管理員**角色，就會通知具備此角色的系統管理員。
2. 如果未指派任何密碼管理員，就會通知具備**使用者管理員**角色的系統管理員。
3. 如果上述角色都未指派，則會通知**全域管理員**。

在所有情況下，最多 100 位收件者會收到通知。

若要深入了解不同的系統管理員角色，以及如何指派這些角色，請參閱[在 Azure Active Directory 中指派系統管理員角色](../users-groups-roles/directory-assign-admin-roles.md)。

### <a name="disable-contact-your-administrator-emails"></a>停用 [請連絡您的系統管理員] 電子郵件

如果您的組織不想傳送密碼重設要求通知給系統管理員，則您可以啟用下列設定：

* 對所有使用者啟用自助式密碼重設。 此選項位於 [密碼重設] > [屬性] 底下。 如果您不想讓使用者重設其自己的密碼，可以將存取範圍設定為空群組。 不建議使用此選項。
* 自訂技術服務人員連結，以提供可讓使用者取得協助的 Web URL 或 mailto︰位址。 此選項位於 [密碼重設] > [自訂] > [自訂的技術服務人員電子郵件或 URL] 底下。

## <a name="customize-the-ad-fs-sign-in-page-for-sspr"></a>自訂 SSPR 的 AD FS 登入頁面

「Active Directory 同盟服務」(AD FS) 系統管理員可以使用[新增登入頁面描述](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/add-sign-in-page-description) \(機器翻譯\) 一文中所提供的指引，新增其登入頁面的連結。

若要新增 AD FS 登入頁面的連結，請在您的 AD FS 伺服器上使用下列命令。 使用者可以使用此頁面來進入 SSPR 工作流程。

``` powershell
Set-ADFSGlobalWebContent -SigninPageDescriptionText "<p><A href='https://passwordreset.microsoftonline.com' target='_blank'>Can’t access your account?</A></p>"
```

## <a name="customize-the-sign-in-page-and-access-panel-look-and-feel"></a>自訂登入頁面和存取面板的外觀與風格

您可以自訂登入頁面。 您可以新增與符合您公司商標的影像一起顯示的標誌。

您選擇的圖形會在下列情況下顯示：

* 在使用者輸入其使用者名稱之後
* 如果使用者存取自訂的 URL，則會透過下列方式：
   * 藉由將 `whr` 參數傳遞至密碼重設頁面，例如 `https://login.microsoftonline.com/?whr=contoso.com`
   * 藉由將 `username` 參數傳遞至密碼重設頁面，例如 `https://login.microsoftonline.com/?username=admin@contoso.com`

如需有關如何設定公司商標的詳細資料，請參閱[將公司商標新增至 Azure AD 中的登入頁面](../fundamentals/customize-branding.md)一文。

### <a name="directory-name"></a>目錄名稱

您可以在 [Azure Active Directory] > [屬性] 底下變更目錄名稱屬性。 您可以顯示一個會在入口網站及自動化通訊中顯示的易記組織名稱。 這個選項最常以下列形式出現在自動化電子郵件中：

* 電子郵件中的易記名稱，例如「Microsoft 代表 CONTOSO 示範」
* 電子郵件中的主旨列，例如「CONTOSO 示範帳戶電子郵件驗證碼」

## <a name="next-steps"></a>後續步驟

* [如何完成 SSPR 成功首度發行？](howto-sspr-deployment.md)
* [重設或變更您的密碼](../user-help/active-directory-passwords-update-your-own-password.md)
* [註冊自助式密碼重設](../user-help/active-directory-passwords-reset-register.md)
* [您有授權問題嗎？](concept-sspr-licensing.md)
* [SSPR 使用哪些資料，以及您應該為使用者填入哪些資料？](howto-sspr-authenticationdata.md)
* [哪些驗證方法可供使用者使用？](concept-sspr-howitworks.md#authentication-methods)
* [使用 SSPR 的原則選項有哪些？](concept-sspr-policy.md)
* [什麼是密碼回寫，且為什麼我需要了解它？](howto-sspr-writeback.md)
* [如何回報 SSPR 中的活動？](howto-sspr-reporting.md)
* [SSPR 中的所有選項有哪些，以及它們有何意義？](concept-sspr-howitworks.md)
* [我認為有些東西已中斷。如何? SSPR 疑難排解？](active-directory-passwords-troubleshoot.md)
* [在其他某處並未涵蓋我的問題](active-directory-passwords-faq.md)

[Contact]: ./media/concept-sspr-customization/sspr-contact-admin.png "請洽詢您的系統管理員，以取得重設密碼電子郵件範例的協助"
