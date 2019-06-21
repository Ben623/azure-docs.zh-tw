---
title: 在 Azure Active Directory 條件式存取的存取控制有哪些？ | Microsoft Docs
description: 了解在 Azure Active Directory 條件式存取的工作中的存取控制如何。
services: active-directory
keywords: 條件式存取應用程式，與 Azure AD，安全地存取公司資源，條件式存取原則的條件式存取
documentationcenter: ''
author: MicrosoftGuyJFlo
manager: daveba
editor: ''
ms.assetid: 8c1d978f-e80b-420e-853a-8bbddc4bcdad
ms.service: active-directory
ms.subservice: conditional-access
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 06/15/2019
ms.author: joflore
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 206b5fef3e4e686bd237fe0f45cfb91dccd4626f
ms.sourcegitcommit: e5dcf12763af358f24e73b9f89ff4088ac63c6cb
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/14/2019
ms.locfileid: "67136499"
---
# <a name="what-are-access-controls-in-azure-active-directory-conditional-access"></a>在 Azure Active Directory 條件式存取的存取控制有哪些？

具有[Azure Active Directory (Azure AD) 條件式存取](../active-directory-conditional-access-azure-portal.md)，您可以控制授權的使用者如何存取您雲端應用程式。 在條件式存取原則中，您可以定義 ("do this") 的回應 ("when this happens")，觸發您原則的原因。

![控制](./media/controls/10.png)

條件式存取的內容中

- "**When this happens**" 稱為**條件**

- "**Then do this**" 稱為**存取控制**

條件陳述式與控制項的組合即代表條件式存取原則。

![控制](./media/controls/61.png)

每種控制項都是必須由登入之使用者或系統履行的要求，或是規範使用者在登入後之行為的限制。

控制項分為兩種：

- **授與控制項** - 限制存取權限

- **工作階段控制項** - 限制工作階段內的存取權限

本主題說明 Azure AD 條件式存取中可用的各種控制項。 

## <a name="grant-controls"></a>授與控制

透過授與控制項，您可以藉由選取需要的控制項來全面封鎖存取權限，或允許在滿足其他要求的情況下授與存取權限。 若要實施多個控制項，您可以要求：

- 滿足所有選取的控制項 (*AND*)
- 滿足一項選取的控制項 (*OR*)

![控制](./media/controls/18.png)

### <a name="multi-factor-authentication"></a>Multi-Factor Authentication

您可以使用這個控制項來要求使用者通過 Multi-Factor Authentication 後，才能存取指定的雲端應用程式。 這個控制項支援以下多重要素提供者：

- Azure Multi-Factor Authentication

- 結合 Active Directory Federation Services (AD FS) 的內部部署 Multi-Factor Authentication 提供者。

對於未獲授權但已取得有效使用者之主要認證存取權的使用者，使用 Multi-Factor Authentication 有助於防止其存取資源。

### <a name="compliant-device"></a>符合規範的裝置

您可以設定以裝置作為基礎的條件式存取原則。 裝置型條件式存取原則的目標是只存取權授與選取的雲端應用程式，從[受管理的裝置](require-managed-devices.md)。 您必須限制對受控裝置的存取的其中一個選項，是要求將裝置標示為符合規範。 Intune (適用於任何裝置作業系統) 或您的協力廠商 MDM 系統 (適用於 Windows 10 裝置) 可以將裝置標示為符合規範。 不支援針對 Windows 10 以外的裝置 OS 類型使用的第三方 MDM 系統。 

您的裝置必須向 Azure AD 註冊，才能標示為符合規範。 若要註冊裝置，您會有三個選項： 

- [Azure AD 註冊裝置](../devices/overview.md#azure-ad-registered-devices)
- [Azure AD 已加入裝置](../devices/overview.md#azure-ad-joined-devices)  
- [混合式 Azure AD 已加入裝置](../devices/overview.md#hybrid-azure-ad-joined-devices)

如需詳細資訊，請參閱 <<c0> [ 如何要求使用條件式存取的雲端應用程式存取的受管理的裝置](require-managed-devices.md)。

### <a name="hybrid-azure-ad-joined-device"></a>已加入混合式 Azure AD 的裝置

需要混合式 Azure AD 已加入的裝置是您不必設定裝置型條件式存取原則的另一個選項。 這項需求是指加入內部部署 Active Directory 的 Windows 桌上型電腦、膝上型電腦和企業平板電腦。 如果選取此選項時，您的條件式存取原則會授與連線才能存取嘗試與您的內部部署 Active Directory 與 Azure Active Directory 已加入的裝置。  

如需詳細資訊，請參閱 <<c0> [ 設定 Azure Active Directory 裝置型條件式存取原則](require-managed-devices.md)。

### <a name="approved-client-app"></a>已核准的用戶端應用程式

由於員工使用行動裝置來處理個人和工作事務，因此即使您不負責管理公司資料，可能也會想要在員工使用裝置存取公司資料時保護資料。
您可以使用 [Intune 應用程式保護原則](https://docs.microsoft.com/intune/app-protection-policy)來保護公司的資料，並且不受任何行動裝置管理 (MDM) 解決方案影響。

藉由核准的用戶端應用程式，您可以要求嘗試存取雲端應用程式的用戶端應用程式支援 [Intune 應用程式保護原則](https://docs.microsoft.com/intune/app-protection-policy)。 例如，您可以限制唯有 Outlook 應用程式能存取 Exchange Online。 需要核准的用戶端應用程式的條件式存取原則就是所謂[應用程式型條件式存取原則](app-based-conditional-access.md)。 如需支援的核准用戶端應用程式清單，請參閱[核准的用戶端應用程式需求](technical-reference.md#approved-client-app-requirement)。

### <a name="app-protection-policy-preview"></a>應用程式保護原則 （預覽）

由於員工使用行動裝置來處理個人和工作事務，因此即使您不負責管理公司資料，可能也會想要在員工使用裝置存取公司資料時保護資料。
您可以使用 [Intune 應用程式保護原則](https://docs.microsoft.com/intune/app-protection-policy)來保護公司的資料，並且不受任何行動裝置管理 (MDM) 解決方案影響。

使用應用程式保護原則，您可以限制已向 Azure 回報的用戶端應用程式的存取 AD 有接收[Intune 應用程式保護原則](https://docs.microsoft.com/intune/app-protection-policy)。 比方說，您可以限制 Exchange online 的存取，Outlook 應用程式具有 Intune 應用程式保護原則。 需要應用程式保護原則的條件式存取原則就是所謂[應用程式保護為基礎的條件式存取原則](app-protection-based-conditional-access.md)。 

您的裝置必須向 Azure AD 應用程式可以標示為受保護的原則。

如需支援的原則的受保護的用戶端應用程式，請參閱[應用程式保護原則需求](technical-reference.md#app-protection-policy-requirement)。


### <a name="terms-of-use"></a>使用規定

您可以要求租用戶中的使用者先同意使用條款，再授與他們存取資源的權利。 身為系統管理員，您可以上傳 PDF 文件來設定及自訂使用條款。 使用者如果落在此控制項的控制範圍內，就必須同意使用條款才會獲得應用程式的存取權。

### <a name="custom-controls-preview"></a>自訂控制項 (預覽)

您可以將使用者重新導向至相容的服務，以進一步滿足需求，Azure Active Directory 之外的條件式存取中新增自訂控制項。 這可讓您使用某些外部的多重要素驗證和驗證提供者來強制執行條件式存取需求。 為了滿足此控制項，系統會將使用者的瀏覽器重新導向至外部服務，執行任何必要的驗證或確認活動，再將瀏覽器重新導向回到 Azure Active Directory。 使用者如果成功通過驗證或確認，就會繼續進行條件式存取流程。 

## <a name="custom-controls"></a>自訂控制項

自訂控制項是 Azure Active Directory Premium P1 版本的一項功能。 使用自訂控制項時，系統會將使用者重新導向到相容的服務，以滿足 Azure Active Directory 之外的進一步需求。 為了滿足此控制項，系統會將使用者的瀏覽器重新導向至外部服務，執行任何必要的驗證或確認活動，再將瀏覽器重新導向回到 Azure Active Directory。 Azure Active Directory 確認回應，而且，如果使用者已成功驗證或驗證，使用者會繼續進行條件式存取流程。

這些控制項允許使用某些外部或自訂的服務，做為條件式存取控制項，而且一般都能擴充條件式存取的功能。

目前提供相容服務的提供者包括：

- [Duo Security](https://duo.com/docs/azure-ca)
- [委託 Datacard](https://www.entrustdatacard.com/products/authentication/intellitrust)
- [GSMA](https://mobileconnect.io/azure/)
- [Ping 身分識別](https://documentation.pingidentity.com/pingid/pingidAdminGuide/index.shtml#pid_c_AzureADIntegration.html)
- RSA
- [SecureAuth](https://docs.secureauth.com/pages/viewpage.action?pageId=47238992#)
- [Silverfort](https://www.silverfort.io/company/using-silverfort-mfa-with-azure-active-directory/)
- [Symantec VIP](https://help.symantec.com/home/VIP_Integrate_with_Azure_AD)
- [Thales (Gemalto)](https://resources.eu.safenetid.com/help/AzureMFA/Azure_Help/Index.htm)
- [Trusona](https://www.trusona.com/docs/azure-ad-integration-guide)

如需這些服務的詳細資訊，請直接連絡相關提供者。

### <a name="creating-custom-controls"></a>建立自訂控制項

若要建立自訂控制項，請先連絡您想要使用的提供者。 每個非 Microsoft 提供者都有自己的處理程序和註冊、 訂閱，或成為服務的一部分，並指出您想要整合使用條件式存取的需求。 屆時，提供者會提供您 JSON 格式的資料區塊。 這項資料可讓提供者和條件式存取一起運作，您的租用戶、 建立新的控制項和定義條件式存取如何分辨如果您的使用者是否已成功執行與提供者的驗證。

自訂控制項不能使用 Identity Protection 的自動化需要 multi-factor authentication，或提高權限角色 Privileged Identity Manager (PIM) 中。

複製 JSON 資料並貼到相關文字方塊中。 除非您明確地了解您要進行的變更，否則請勿變更 JSON。 進行變更可能會讓提供者與 Microsoft 之間的連線中斷，進而可能將您的帳戶鎖定，讓您和您的使用者無法使用。

建立自訂控制項的選項位於**管理**一節**條件式存取**頁面。

![控制](./media/controls/82.png)

按一下 [新增自訂控制項]  隨即會開啟刀鋒視窗，裡面有文字方塊可供輸入控制項的 JSON 資料。  

![控制](./media/controls/81.png)

### <a name="deleting-custom-controls"></a>刪除自訂控制項

若要刪除自訂控制項，您必須先確定它不在任何條件式存取原則中所使用。 完成之後：

1. 移至 [自訂控制項] 清單

2. 按一下 [...]  

3. 選取 [刪除]  。

### <a name="editing-custom-controls"></a>編輯自訂控制項

若要編輯自訂控制項，您必須先刪除目前的控制項，然後使用更新後的資訊建立新的控制項。

## <a name="session-controls"></a>工作階段控制項

工作階段控制項可讓您限制雲端應用程式內的體驗。 工作階段控制項是由雲端應用程式強制執行，並依賴 Azure AD 對應用程式提供有關工作階段的其他資訊。

![控制](./media/controls/31.png)

### <a name="use-app-enforced-restrictions"></a>使用應用程式強制執行限制

您可以使用這個控制項，要求 Azure AD 將裝置資訊傳遞至所選的雲端應用程式。 裝置資訊可讓雲端應用程式知道連線是否從符合規範或已加入網域的裝置起始。 此控制項僅支援 SharePoint Online 和 Exchange Online 作為選取的雲端應用程式。 選取後，雲端應用程式會使用裝置資訊來提供使用者有限或完整的體驗 (視裝置狀態而定)。

若要深入了解，請參閱：

- [啟用 SharePoint Online 的有限存取](https://aka.ms/spolimitedaccessdocs)

- [啟用 Exchange Online 的有限存取](https://aka.ms/owalimitedaccess)

## <a name="next-steps"></a>後續步驟

- 如果您想要了解如何設定條件式存取原則，請參閱[需要 MFA 的特定應用程式與 Azure Active Directory 條件式存取](app-based-mfa.md)。

- 如果您已準備好設定您的環境的條件式存取原則，請參閱[Azure Active Directory 中的條件式存取的最佳作法](best-practices.md)。
