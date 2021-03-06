---
title: 針對 B2B 共同作業進行疑難排解-Azure Active Directory |Microsoft Docs
description: Azure Active Directory B2B 共同作業常見問題的補救方式
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: troubleshooting
ms.date: 11/12/2019
tags: active-directory
ms.author: mimart
author: v-miegge
manager: dcscontentpm
ms.reviewer: mal
ms.custom:
- it-pro
- seo-update-azuread-jan"
ms.collection: M365-identity-device-management
ms.openlocfilehash: c7c0a4567da11b10b9a0571656103ef2f17c7da4
ms.sourcegitcommit: 05b36f7e0e4ba1a821bacce53a1e3df7e510c53a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/06/2020
ms.locfileid: "78399051"
---
# <a name="troubleshooting-azure-active-directory-b2b-collaboration"></a>針對 Azure Active Directory B2B 共同作業問題進行疑難排解

以下是 Azure Active Directory (Azure AD) B2B 共同作業常見問題的補救方式。

## <a name="ive-added-an-external-user-but-do-not-see-them-in-my-global-address-book-or-in-the-people-picker"></a>我已新增外部使用者，但在我的全域通訊錄或人員選擇器中未看到他們

若清單中未顯示外部使用者，該物件可能需要一些時間才能複寫。

## <a name="a-b2b-guest-user-is-not-showing-up-in-sharepoint-onlineonedrive-people-picker"></a>B2B 來賓使用者不會顯示在 SharePoint Online/OneDrive 人員選擇器中

在 SharePoint Online (SPO) 人員選擇器中搜尋現有來賓使用者的功能預設為關閉，以符合舊版的行為。

您可以使用租用戶和網站集合層級中的 'ShowPeoplePickerSuggestionsForGuestUsers' 設定來啟用此功能。 您可以使用 Set-SPOTenant 和 Set-SPOSite Cmdlet 來設定此功能，讓成員搜尋目錄中所有的現有來賓使用者。 租用戶範圍中的變更不會影響已佈建的 SPO 網站。

## <a name="invitations-have-been-disabled-for-directory"></a>已針對目錄停用邀請

如果您收到通知，指出您沒有邀請使用者的許可權，請確認您的使用者帳戶已獲授權可在 Azure Active Directory > 使用者設定下邀請外部使用者，> 外部使用者 > 管理外部協同作業設定：

![顯示外部使用者設定的螢幕擷取畫面](media/troubleshoot/external-user-settings.png)

若您最近曾修改這些設定或已獲指派使用者的「來賓邀請者」角色，變更可能需要 15-60 分鐘才會生效。

## <a name="the-user-that-i-invited-is-receiving-an-error-during-redemption"></a>我邀請的使用者在兌換期間遇到錯誤

常見錯誤包括：

### <a name="invitees-admin-has-disallowed-emailverified-users-from-being-created-in-their-tenant"></a>受邀者的管理員不允許在其租用戶中建立 EmailVerified 使用者

當邀請使用者的組織使用 Azure Active Directory，但指定使用者的帳戶不存在 (例如，使用者不存在於 Azure AD contoso.com 中)。 contoso.com 的系統管理員可能已建立防止建立使用者的原則。 使用者必須向管理員確認，以判斷是否允許外部使用者。 外部使用者的管理員需要允許其網域中存在已驗證的電子郵件使用者 (請參閱[本文](/powershell/module/msonline/set-msolcompanysettings?view=azureadps-1.0)，了解允許已驗證的電子郵件使用者)。

![指出租使用者不允許以電子郵件驗證的使用者的錯誤](media/troubleshoot/allow-email-verified-users.png)

### <a name="external-user-does-not-exist-already-in-a-federated-domain"></a>外部使用者不存在於同盟網域中

如果您使用同盟驗證，而使用者目前不在 Azure Active Directory 中，則無法邀請使用者。

若要解決此問題，外部使用者的系統管理員必須將該使用者的帳戶同步到 Azure Active Directory。

## <a name="how-does--which-is-not-normally-a-valid-character-sync-with-azure-ad"></a>‘\#’ (通常是無效的字元) 如何與 Azure AD 同步？

因為受邀帳戶 \# 會成為 user_contoso.com#EXT#user@contoso.com，所以 “@fabrikam.onmicrosoft.com” 是 Azure AD B2B 共同作業或外部使用者之 UPN 中的保留字元。 因此，來自內部部署之 UPN 中的 \# 不可登入 Azure 入口網站。 

## <a name="i-receive-an-error-when-adding-external-users-to-a-synchronized-group"></a>我在將外部使用者新增到已同步的群組時遇到錯誤

您只能將外部使用者新增到「已指派」或「安全性」群組，而無法指派到主控內部部署群組。

## <a name="my-external-user-did-not-receive-an-email-to-redeem"></a>我的外部使用者未收到兌換電子郵件

受邀者應該連絡其 ISP 或檢查其垃圾郵件篩選設定，以確定下列地址在允許清單中：Invites@microsoft.com

## <a name="i-notice-that-the-custom-message-does-not-get-included-with-invitation-messages-at-times"></a>我注意到自訂訊息有時候不會包含在邀請訊息中

為了符合隱私權法律，在下列情況下，我們的 API 不會在電子郵件邀請中包含自訂訊息：

- 邀請者在提出邀請的租用戶中沒有電子郵件地址
- 應用程式服務主體傳送邀請時

如果此案例對您很重要，您可以隱藏我們的 API 邀請電子郵件，然後透過您選擇的電子郵件機制傳送邀請電子郵件。 請諮詢貴組織的法律顧問，以確定透過此方式傳送的任何電子郵件也符合隱私權法律。

## <a name="you-receive-an-aadsts65005-error-when-you-try-to-log-in-to-an-azure-resource"></a>當您嘗試登入 Azure 資源時，收到「AADSTS65005」錯誤

具有來賓帳戶的使用者無法登入，並收到下列錯誤訊息：

    AADSTS65005: Using application 'AppName' is currently not supported for your organization contoso.com because it is in an unmanaged state. An administrator needs to claim ownership of the company by DNS validation of contoso.com before the application AppName can be provisioned.

使用者具有 Azure 使用者帳戶，而且是已被放棄或未受管理的病毒租使用者。 此外，租使用者中沒有全域或公司系統管理員。

若要解決此問題，您必須接管放棄的租使用者。 請參閱[Azure Active Directory 中以系統管理員身分接管非受控目錄](https://docs.microsoft.com/azure/active-directory/users-groups-roles/domains-admin-takeover)。 您也必須針對有問題的網域尾碼存取網際網路對向 DNS，才能提供您控制命名空間的直接辨識項。 當租使用者回到受管理的狀態之後，請與客戶討論是否讓使用者和已驗證的功能變數名稱是其組織的最佳選項。

## <a name="a-guest-user-with-a-just-in-time-or-viral-tenant-is-unable-to-reset-their-password"></a>具有 Just-In-Time (JIT) 或「病毒式」租用戶的來賓使用者無法重設其密碼

如果身分識別租用戶是 Just-In-Time (JIT) 或病毒式租用戶 (表示它是獨立、非受控的 Azure 租用戶)，只有來賓使用者才能重設自己的密碼。 組織有時會[接管病毒式租用戶的管理](https://docs.microsoft.com/azure/active-directory/users-groups-roles/domains-admin-takeover)，這些是員工使用其工作電子郵件地址來註冊服務時所建立的租用戶。 在組織接管病毒式租用戶之後，只有該組織的系統管理員可以重設使用者的密碼或啟用 SSPR。 如有必要，作為發出邀請的組織，您可以從目錄中移除來賓使用者帳戶，並重新傳送邀請。

## <a name="a-guest-user-is-unable-to-use-the-azuread-powershell-v1-module"></a>來賓使用者無法使用 AzureAD PowerShell V1 模組

自2019年11月18日起，您目錄中的來賓使用者（定義為**userType**屬性等於**guest**的使用者帳戶）會遭到封鎖而無法使用 AzureAD PowerShell V1 模組。 接下來，使用者必須是成員使用者（其中**userType**等於**member**）或使用 AzureAD PowerShell V2 模組。

## <a name="in-an-azure-us-government-tenant-i-cant-invite-a-b2b-collaboration-guest-user"></a>在 Azure 美國政府租使用者中，我無法邀請 B2B 共同作業來賓使用者

在 Azure 美國政府雲端中，目前只有在 Azure 美國政府雲端內的租使用者，以及同時支援 B2B 共同作業的租使用者之間，才支援 B2B 共同作業。 如果您邀請的租使用者不屬於 Azure 美國政府雲端的一部分，或是尚未支援 B2B 共同作業，您將會收到錯誤。 如需詳細資訊和限制，請參閱[Azure Active Directory Premium P1 和 P2 變化](https://docs.microsoft.com/azure/azure-government/documentation-government-services-securityandidentity#azure-active-directory-premium-p1-and-p2)。

## <a name="next-steps"></a>後續步驟

[取得 B2B 共同作業的支援](get-support.md)
