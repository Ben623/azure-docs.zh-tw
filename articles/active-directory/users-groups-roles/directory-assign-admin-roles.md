---
title: 系統管理員角色說明和權限 - Azure Active Directory | Microsoft Docs
description: 系統管理員角色可以新增使用者、指派系統管理角色、重設使用者密碼、管理使用者授權或管理網域。
services: active-directory
author: curtand
manager: mtillman
search.appverid: MET150
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 07/17/2019
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 24d3da81fabf55bc0c3944f0c03829dee4fcce46
ms.sourcegitcommit: 770b060438122f090ab90d81e3ff2f023455213b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/17/2019
ms.locfileid: "68304401"
---
# <a name="administrator-role-permissions-in-azure-active-directory"></a>Azure Active Directory 中的系統管理員角色權限

使用 Azure Active Directory (Azure AD), 您可以指定有限的系統管理員, 以較低許可權的角色來管理身分識別工作。 指派系統管理員的目的, 是為了新增或變更使用者、指派系統管理角色、重設使用者密碼、管理使用者授權, 以及管理功能變數名稱等。 只能在 Azure AD 中的使用者設定中變更預設使用者權限。

全域管理員可以存取所有系統管理功能。 註冊 Azure 訂用帳戶的人員預設會獲指派目錄的全域管理員角色。 只有全域管理員和特殊權限角色管理員才能委派系統管理員角色。 為了降低業務風險，建議您將此角色僅指派給您公司中的幾個人員。

## <a name="assign-or-remove-administrator-roles"></a>指派或移除系統管理員角色

若要了解如何將系統管理角色指派給 Azure Active Directory 中的使用者，請參閱[在 Azure Active Directory 中檢視和指派系統管理員角色](directory-manage-roles-portal.md)。

## <a name="available-roles"></a>可用的角色

可用的系統管理員角色如下：

* **[應用程式系統管理員](#application-administrator)** ：此角色中的使用者可以建立和管理企業應用程式、應用程式註冊和應用程式 Proxy 設定的所有層面。 此角色也會授與能力來同意委派的權限以及 Microsoft Graph 和 Azure AD Graph 以外的應用程式權限。 在建立新的應用程式註冊或企業應用程式時, 不會將指派給此角色的使用者新增為擁有者。

  <b>重要</b>：此角色授與管理應用程式認證的能力。 獲指派此角色的使用者可以將認證新增至應用程式，並使用這些認證來模擬應用程式的身分識別。 如果應用程式的身分識別已授與 Azure Active Directory 的存取權，例如建立或更新使用者或其他物件的能力，則獲指派這個角色的使用者可以在模擬應用程式時執行這些動作。 模擬應用程式身分識別的這項能力，對於使用者透過 Azure AD 中角色指派所能執行的動作，是一種權限提高。 請務必了解，將應用程式系統管理員角色指派給使用者，會給予他們模擬應用程式身分識別的能力。

* **[應用程式開發人員](#application-developer)** ：將「使用者可以註冊應用程式」設定設為「否」時，此角色中的使用者可以建立應用程式註冊。 當「使用者可以同意應用程式代表自己存取公司資料」設定設為 [否] 時, 此角色也會授與許可權以代表自己的同意。 在建立新的應用程式註冊或企業應用程式時, 指派給此角色的使用者會被新增為擁有者。

* **[驗證系統管理員](#authentication-administrator)** ：具有此角色的使用者可以設定或重設非密碼認證, 並且可以更新所有使用者的密碼。 驗證系統管理員可以要求使用者針對現有的非密碼認證 (例如, MFA 或 FIDO) 重新註冊, 並撤銷**裝置上的 [記住 mfa**], 這會在非系統管理員的使用者登入或僅指派下列角色:
  * 驗證系統管理員
  * 目錄讀取器
  * 來賓邀請者
  * 訊息中心讀取者
  * 報告讀者

  驗證管理員角色目前處於公開預覽狀態。 此預覽版本是在沒有服務等級協定的情況下提供，不建議用於生產工作負載。 可能不支援特定功能，或可能已經限制功能。 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

  <b>重要</b>：對於可存取機密或私人資訊或 Azure Active Directory 內外重要組態的人員，具備此角色的使用者可以變更認證。 變更使用者的認證表示可承擔該使用者身分識別和權限。 例如:

  * 應用程式註冊和企業應用程式擁有者，他們可以管理他們自己的應用程式認證。 這些應用程式在 Azure AD 中可能有特殊權限，而在其他地方未授與驗證系統管理員。 透過此路徑, 驗證系統管理員可以假設應用程式擁有者的身分識別, 然後藉由更新應用程式的認證, 進一步假設特殊許可權應用程式的識別。
  * Azure 訂用帳戶擁有者，他們具有機密或私人資訊或者 Azure 中重要組態的存取權。
  * 安全性群組和 Office 365 群組擁有者，他們可以管理群組成員資格。 這個群組可以存取機密或私人資訊或者 Azure AD 和其他位置中的重要組態。
  * Azure AD 外部其他服務 (例如，Exchange Online、Office 安全性與合規性中心和人力資源系統) 中的系統管理員。
  * 非系統管理員，例如主管、法律顧問和人力資源員工，他們可以存取機密或私人資訊。

* **[Azure 資訊保護系統管理員](#azure-information-protection-administrator)** :具有此角色的使用者在 Azure 資訊保護服務上擁有所有權限。 此角色允許設定「Azure 資訊保護」原則的標籤、管理保護範本，以及啟用保護。 此角色並未授與「Identity Protection 中心」、Privileged Identity Management、「監視 Office 365 服務健康情況」及「Office 365 安全與規範中心」中的任何權限。

* **[B2C 使用者流程管理員](#b2c-user-flow-administrator)** :具有此角色的使用者可以在 Azure 入口網站中建立及管理 B2C 消費者流程 (也稱為「內建」原則)。 藉由建立或編輯使用者流程, 這些使用者可以變更使用者體驗的 html/CSS/javascript 內容、變更每個使用者流程的 MFA 需求、變更權杖中的宣告, 以及調整租使用者中所有原則的會話設定。 另一方面, 此角色並不包括檢查使用者資料的能力, 或對租使用者架構中包含的屬性進行變更。 Identity Experience Framework (也稱為自訂) 原則的變更也不在此角色的範圍內。

* **[B2C 使用者流程屬性管理員](#b2c-user-flow-attribute-administrator)** :具有此角色的使用者可在租使用者中的所有使用者流程中, 新增或刪除自訂屬性。 因此, 具備此角色的使用者可以變更或新增元素至使用者架構, 並影響所有使用者流程的行為, 並間接導致使用者可能會要求哪些資料的變更, 最後以宣告的形式傳送給應用程式。 此角色無法編輯使用者流程。

* **[B2C IEF 金鑰組管理員](#b2c-ief-keyset-administrator)** :  使用者可以建立和管理權杖加密、權杖簽章和宣告加密/解密的原則金鑰和密碼。 藉由將新金鑰新增至現有的金鑰容器, 此有限的系統管理員可以視需要變換秘密, 而不會影響現有的應用程式。 此使用者可以查看這些秘密的完整內容及其到期日, 即使在建立之後也一樣。
    
  <b>重要事項:</b>  這是敏感性角色。 金鑰集系統管理員角色應該仔細地進行審核, 並在生產前和實際執行期間加以指派。

* **[B2C IEF 原則管理員](#b2c-ief-policy-administrator)** :此角色的使用者能夠在 Azure AD B2C 中建立、讀取、更新及刪除所有自訂原則, 因此可以完全控制相關 Azure AD B2C 租使用者中的 Identity Experience Framework。 藉由編輯原則, 此使用者可以與外部身分識別提供者建立直接同盟、變更目錄架構、變更所有使用者面向內容 (HTML、CSS、JavaScript)、變更需求以完成驗證、建立新使用者、傳送使用者資料到外部系統, 包括完整的遷移, 以及編輯所有使用者資訊, 包括密碼和電話號碼等敏感欄位。 相反地, 此角色無法變更加密金鑰, 或編輯用於租使用者中同盟的秘密。

  <b>重要事項：</b>B2 IEF 原則系統管理員是高度敏感的角色, 應針對生產環境中的租使用者以非常有限的基礎加以指派。 這些使用者的活動應該仔細地進行審核, 特別是針對生產環境中的租使用者。

* **[計費管理員](#billing-administrator)** ：進行採購、管理訂用帳戶、管理支援票證，以及監控服務健全狀況。

* **[雲端應用程式系統管理員](#cloud-application-administrator)** ：此角色中的使用者具有與應用程式系統管理員角色相同的權限，但不包括管理應用程式 Proxy 的能力。 此角色會授與能力來建立和管理企業應用程式和應用程式註冊的所有層面。 此角色也會授與能力來同意委派的權限以及 Microsoft Graph 和 Azure AD Graph 以外的應用程式權限。 在建立新的應用程式註冊或企業應用程式時, 不會將指派給此角色的使用者新增為擁有者。

  <b>重要</b>：此角色授與管理應用程式認證的能力。 獲指派此角色的使用者可以將認證新增至應用程式，並使用這些認證來模擬應用程式的身分識別。 如果應用程式的身分識別已授與 Azure Active Directory 的存取權，例如建立或更新使用者或其他物件的能力，則獲指派這個角色的使用者可以在模擬應用程式時執行這些動作。 模擬應用程式身分識別的這項能力，對於使用者透過 Azure AD 中角色指派所能執行的動作，是一種權限提高。 請務必了解，將雲端應用程式系統管理員角色指派給使用者，會給予他們模擬應用程式身分識別的能力。

* **[雲端裝置系統管理員](#cloud-device-administrator)** ：此角色的使用者可以啟用、停用和刪除 Azure AD 中的裝置，並在 Azure 入口網站中讀取 Windows 10 BitLocker 金鑰 (如果有的話)。 此角色不會授與可供管理裝置上任何其他屬性的權限。

* **[規範管理員](#compliance-administrator)** ：具備此角色的使用者有權限管理 Microsoft 365 合規性中心、Microsoft 365 系統管理中心、Azure 和 Office 365 安全性與合規性中心中的合規性相關功能。 使用者也可以管理 Exchange 系統管理中心、Teams 和商務用 Skype 系統管理中心內的所有功能，並建立適用於 Azure 和 Microsoft 365 的支援票證。 如需詳細資訊，請參閱[關於 Office 365 管理員角色](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d)。

  在 | 可以執行
  ----- | ----------
  [Microsoft 365 合規性中心](https://protection.office.com) | 保護和管理您組織在所有 Microsoft 365 服務中的資料<br>管理合規性警示
  [合規性管理員](https://docs.microsoft.com/office365/securitycompliance/meet-data-protection-and-regulatory-reqs-using-microsoft-cloud) | 追蹤、指派和確認您組織的法規合規性活動
  [Office 365 安全性與合規性中心](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d) | 管理資料治理<br>執行法律和資料的調查<br>管理資料主體要求
  [Intune](https://docs.microsoft.com/intune/role-based-access-control) | 檢視所有的 Intune 稽核資料
  [Cloud App Security](https://docs.microsoft.com/cloud-app-security/manage-admins) | 具有唯讀權限，並可管理警示<br>可建立和修改檔案原則，並允許檔案治理動作<br> 可檢視 [資料管理] 下的所有內建報告

* **[合規性資料管理員](#compliance-data-administrator)** :具有此角色的使用者具有在 Microsoft 365 合規性中心、Microsoft 365 系統管理中心和 Azure 中保護和追蹤資料的許可權。 使用者也可以管理 Exchange 系統管理中心、合規性管理員、Teams 和商務用 Skype 系統管理中心內的所有功能，並建立適用於 Azure 和 Microsoft 365 的支援票證。

  在 | 可以執行
  ----- | ----------
  [Microsoft 365 合規性中心](https://protection.office.com) | 監視跨 Microsoft 365 服務的合規性相關原則<br>管理合規性警示
  [合規性管理員](https://docs.microsoft.com/office365/securitycompliance/meet-data-protection-and-regulatory-reqs-using-microsoft-cloud) | 追蹤、指派和確認您組織的法規合規性活動
  [Office 365 安全性與合規性中心](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d) | 管理資料治理<br>執行法律和資料的調查<br>管理資料主體要求
  [Intune](https://docs.microsoft.com/intune/role-based-access-control) | 檢視所有的 Intune 稽核資料
  [Cloud App Security](https://docs.microsoft.com/cloud-app-security/manage-admins) | 具有唯讀權限，並可管理警示<br>可建立和修改檔案原則，並允許檔案治理動作<br> 可檢視 [資料管理] 下的所有內建報告

* **[條件式存取系統管理員](#conditional-access-administrator)** ：具有此角色的使用者能夠管理 Azure Active Directory 的條件式存取設定。
  > [!NOTE]
  > 若要在 Azure 中部署 Exchange ActiveSync 條件式存取原則, 使用者也必須是全域管理員。
  
* **[客戶加密箱存取核准者](#customer-lockbox-access-approver)** ：管理您組織中的[客戶加密箱要求](https://docs.microsoft.com/office365/admin/manage/customer-lockbox-requests)。 他們會收到「客戶加密箱」要求的電子郵件通知，並且可以核准和拒絕來自 Microsoft 365 系統管理中心的要求。 他們也可以開啟或關閉「客戶加密箱」功能。 只有全域管理員可以重設指派給此角色之人員的密碼。
  <!--  This was announced in August of 2018. https://techcommunity.microsoft.com/t5/Security-Privacy-and-Compliance/Customer-Lockbox-Approver-Role-Now-Available/ba-p/223393-->

* **[電腦分析系統管理員](#desktop-analytics-administrator)** :此角色中的使用者可以管理電腦分析和 Office 自訂 & 原則服務。 針對電腦分析, 這包括能夠查看資產清查、建立部署計畫、查看部署和健康狀態。 若為 Office 自訂 & 原則服務, 此角色可讓使用者管理 Office 原則。

* **[裝置系統管理員](#device-administrators)** :此角色是只能指派為[裝置設定](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/DevicesMenuBlade/DeviceSettings/menuId/)中的其他本機系統管理員。 具有此角色的使用者，會在已加入 Azure Active Directory 的所有 Windows 10 裝置上，成為本機電腦系統管理員。 它們並沒有在 Azure Active Directory 中管理裝置物件的能力。 

* **[目錄讀取器](#directory-readers)** ︰此角色只應指派給不支援[同意架構](../develop/quickstart-v1-integrate-apps-with-azure-ad.md)的繼承應用程式。 請勿將它指派給使用者。

* **[目錄同步處理帳戶](#directory-synchronization-accounts)** ：請勿使用。 此角色會自動指派給 Azure AD Connect 服務，不適用於也不支援任何其他用途。

* **[目錄寫入人員](#directory-writers)** ：這是舊版角色，用來指派給不支援[同意架構](../develop/quickstart-v1-integrate-apps-with-azure-ad.md)的應用程式。 不應將它指派給任何使用者。

* **[Dynamics 365 系統管理員/CRM 系統管理員](#crm-service-administrator)** ：此角色的使用者具有 Microsoft Dynamics 365 Online (如其存在) 的全域權限，並能管理支援票證及監視服務的健康情況。 如需詳細資訊，請參閱[使用服務管理員角色管理您的租用戶](https://docs.microsoft.com/dynamics365/customer-engagement/admin/use-service-admin-role-manage-tenant)。
  > [!NOTE] 
  > 在 Microsoft 圖形 API、Azure AD 圖形 API 與 Azure AD PowerShell 中，會將此角色識別為「Dynamics 365 服務管理員」。 在 [Azure 入口網站](https://portal.azure.com)中則是「Dynamics 365 管理員」。

* **[Exchange 系統管理員](#exchange-service-administrator)** ：此角色的使用者具有 Microsoft Exchange Online (如其存在) 的全域權限。 此外，也具備建立和管理所有「Office 365 群組」、管理支援票證，以及監視服務健康情況的能力。 如需詳細資訊，請參閱 [關於 Office 365 管理員角色](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d)。
  > [!NOTE]
  > 在 Microsoft Graph API、Azure AD Graph API 和 Azure AD PowerShell 中，會將此角色識別為「Exchange 服務管理員」。 在 [Azure 入口網站](https://portal.azure.com)中則是「Exchange 管理員」。 在 [Exchange 系統管理中心](https://go.microsoft.com/fwlink/p/?LinkID=529144)中則是「Exchange Online 系統管理員」。 

* **[外部識別提供者系統管理員](#external-identity-provider-administrator)** :此系統管理員會管理 Azure Active Directory 租使用者與外部身分識別提供者之間的同盟。 使用此角色, 使用者可以加入新的身分識別提供者, 並設定所有可用的設定 (例如, 驗證路徑、服務識別碼、指派的金鑰容器)。 此使用者可以讓租使用者信任來自外部識別提供者的驗證。 對終端使用者體驗產生的影響取決於租使用者的類型:
  * Azure Active Directory 員工和合作夥伴的租使用者: 新增同盟 (例如使用 Gmail) 會立即影響尚未兌換的所有來賓邀請。 請參閱[將 Google 新增為 B2B 來賓使用者的身分識別提供者](https://docs.microsoft.com/azure/active-directory/b2b/google-federation)。
  * Azure Active Directory B2C 租使用者:新增同盟 (例如 Facebook 或其他 Azure Active Directory) 並不會立即影響使用者流程, 直到將身分識別提供者新增為使用者流程中的選項 (也稱為內建原則)。 如需範例, 請參閱設定[Microsoft 帳戶做為身分識別提供者](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-setup-msa-app)。 若要變更使用者流程, 需要「B2C 使用者流程管理員」的有限角色。

* **[全域系統管理員/公司系統管理員](#company-administrator)** ：具有此角色的使用者可以存取 Azure Active Directory 中所有的系統管理功能，以及使用 Azure Active Directory 身分識別的服務，例如 Microsoft 365 資訊安全中心、Microsoft 365 合規性中心、Exchange Online、SharePoint Online 和商務用 Skype Online。 註冊 Azure Active Directory 租用戶的人員會變成全域管理員。 只有全域管理員才能指派其他系統管理員角色。 您的公司可以有多位全域管理員。 全域系統管理員可以為任何使用者和所有其他系統管理員重設密碼。

  > [!NOTE]
  > 在 Microsoft Graph API、Azure AD Graph API 及 Azure AD PowerShell 中，是將此角色識別為「公司系統管理員」。 它是 [Azure 入口網站](https://portal.azure.com)中的「全域管理員」。
  >
  >

* **[來賓邀請者](#guest-inviter)** ：當 [成員可邀請]  使用者設定為 [否] 時，此角色中的使用者可以管理 Azure Active Directory B2B 來賓使用者的邀請 在[關於 Azure AD B2B 共同作業](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b)中查看 B2B 共同作業的詳細資訊。 這不包含任何其他權限。

* **[Intune 系統管理員](#intune-service-administrator)** ：此角色的使用者具有 Microsoft Intune Online (如其存在) 的全域權限。 此外，此角色包含管理使用者和裝置的能力，可相關聯原則以及建立和管理群組。 如需詳細資訊，請參閱[將角色型系統管理控制用於 Microsoft Intune](https://docs.microsoft.com/intune/role-based-access-control)
  > [!NOTE]
  > 在 Microsoft Graph API、Azure AD Graph API 和 Azure AD PowerShell 中，會將此角色識別為「Intune 服務管理員」。 在 [Azure 入口網站](https://portal.azure.com)中則是「Intune 管理員」。
  
 * **[Kaizala 系統管理員](#kaizala-administrator)** :具有此角色的使用者具有全域許可權, 可管理 Microsoft Kaizala 中的設定 (當服務存在時), 以及管理支援票證和監控服務健康情況的能力。
此外, 使用者也可以存取與採用 & 使用 Kaizala by 組織成員和使用 Kaizala 動作產生的商務報表相關的報表。 

* **[授權管理員](#license-administrator)** ：此角色中的使用者可新增、移除和更新使用者和群組的授權指派 (使用群組型授權)，以及管理使用者的使用位置。 此角色不會授與購買或管理訂用帳戶、建立或管理群組，或在使用位置以外建立或管理使用者的能力。 這個角色沒有檢視、建立或管理支援票證的存取權。

* **[訊息中心隱私權讀者](#message-center-privacy-reader)** :此角色中的使用者可以監視訊息中心內的所有通知, 包括資料隱私權訊息。 訊息中心隱私權讀者會收到電子郵件通知, 包括資料隱私權的相關資訊, 並可使用訊息中心喜好設定取消訂閱。 只有全域管理員和訊息中心隱私權讀取者可以讀取資料隱私權訊息。 此外, 此角色包含可供您查看群組、網域和訂閱的功能。 此角色沒有任何許可權可查看、建立或管理服務要求。

* **[訊息中心讀者](#message-center-reader)** ：此角色中的使用者可以在 [Office 365 訊息中心](https://support.office.com/article/Message-center-in-Office-365-38FB3333-BFCC-4340-A37B-DEDA509C2093)內，為他們的組織監視所設服務 (例如 Exchange、Intune 和 Microsoft Teams) 的通知和諮詢健康情況更新。 訊息中心讀者每週會收到貼文的電子郵件摘要和更新，並且可以在 Office 365 中分享訊息中心的貼文。 在 Azure AD 中，指派至此角色的使用者只會有 Azure AD 服務的唯讀存取權，與使用者和群組一樣。 這個角色沒有檢視、建立或管理支援票證的存取權。

* **[合作夥伴第 1 層支援](#partner-tier1-support)** ：請勿使用。 此角色已被取代，而且未來將從 Azure AD 中移除。 此角色僅供少數 Microsoft 轉售合作夥伴使用，不適用於一般用途。

* **[合作夥伴第 2 層支援](#partner-tier2-support)** ：請勿使用。 此角色已被取代，而且未來將從 Azure AD 中移除。 此角色僅供少數 Microsoft 轉售合作夥伴使用，不適用於一般用途。

* **[技術服務人員 (密碼) 管理員](#helpdesk-administrator)** :具備此角色的使用者可以變更密碼、讓重新整理權杖失效、管理服務要求，以及監視服務健康情況。 讓重新整理權杖失效會強制使用者重新登入。 技術服務管理員可以重設密碼, 並使非系統管理員的其他使用者重新整理權杖失效, 或只指派下列角色:
  * 目錄讀取器
  * 來賓邀請者
  * 服務台系統管理員
  * 訊息中心讀取者
  * 報告讀者
  
  <b>重要</b>：具備此角色的使用者可以變更可存取機密或私人資訊或 Azure Active Directory 內外重要組態的人員密碼。 變更使用者的密碼表示可承擔該使用者身分識別和權限。 例如:
  * 應用程式註冊和企業應用程式擁有者，他們可以管理他們自己的應用程式認證。 這些應用程式在 Azure AD 中可能有特殊權限，而在其他地方未授與技術支援中心系統管理員。 技術支援中心系統管理員可以透過此路徑承擔應用程式擁有者的身分識別，然後藉由更新應用程式的認證，進一步承擔特殊權限應用程式的身分識別。
  * Azure 訂用帳戶擁有者，他們具有機密或私人資訊或者 Azure 中重要組態的存取權。
  * 安全性群組和 Office 365 群組擁有者，他們可以管理群組成員資格。 這個群組可以存取機密或私人資訊或者 Azure AD 和其他位置中的重要組態。
  * Azure AD 外部其他服務 (例如，Exchange Online、Office 安全性與合規性中心和人力資源系統) 中的系統管理員。
  * 非系統管理員，例如主管、法律顧問和人力資源員工，他們可以存取機密或私人資訊。


  > [!NOTE]
  > 將系統管理許可權委派給使用者子集, 並將原則套用到使用者子集, 可以使用[管理單位 (預覽)](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-administrative-units)。


  > [!NOTE]
  > 此角色先前在[Azure 入口網站](https://portal.azure.com/)中稱為「密碼管理員」。 我們會將其名稱變更為「技術服務管理員」, 以符合其在 Azure AD PowerShell 中的名稱, Azure AD 圖形 API 和 Microsoft Graph API。 在短時間內, 我們會在 Azure 入口網站變更為「技術服務管理員」之前, 將名稱變更為「技術支援中心 (密碼) 管理員」。


* **[Power BI 管理員](#power-bi-service-administrator)** ：此角色的使用者具有 Microsoft Power BI (如其存在) 的全域權限，並能管理支援票證及監視服務的健康情況。 如需詳細資訊，請參閱[了解 Power BI 管理員角色](https://docs.microsoft.com/power-bi/service-admin-role)。
  > [!NOTE]
  > 在 Microsoft Graph API、Azure AD Graph API 和 Azure AD PowerShell 中，會將此角色識別為「Power BI 服務管理員」。 在 [Azure 入口網站](https://portal.azure.com)中則是「Power BI 管理員」。

* 特殊 **[許可權驗證管理員](#privileged-authentication-administrator)** :具有此角色的使用者可以為所有使用者設定或重設非密碼認證, 包括全域管理員, 而且可以更新所有使用者的密碼。 特殊許可權驗證系統管理員可以強制使用者針對現有的非密碼認證 (例如 MFA、FIDO) 重新註冊, 並撤銷「在裝置上記住 MFA」, 在下次登入所有使用者時提示 MFA。 特殊許可權驗證系統管理員可以:
  * 強制使用者重新註冊現有的非密碼認證 (例如 MFA、FIDO)
  * 撤銷「記住裝置上的 MFA」, 在下次登入時提示 MFA

* **[特殊權限角色管理員](#privileged-role-administrator)** ：具備此角色的使用者可以管理 Azure Active Directory 中，以及 Azure AD Privileged Identity Management 內的角色指派。 此外, 此角色可讓您管理 Privileged Identity Management 和管理單位的所有層面。

  <b>重要</b>：此角色會授與管理所有 Azure AD 角色指派的能力, 包括全域管理員角色。 此角色不包含 Azure AD 中的任何其他特殊權限能力，例如建立或更新使用者。 不過，指派給這個角色的使用者可以藉由指派額外的角色，來授與自己或其他人額外權限。

* **[報表讀者](#reports-reader)** ：具有此角色的使用者可以在 Microsoft 365 系統管理中心和 Power BI 中的採用內容套件中, 查看使用方式報告資料和報告儀表板。 此外，此角色還可讓使用者存取 Azure AD 中的登入報告與活動，以及 Microsoft Graph 報告 API 所傳回的資料。 獲指派「報告讀者」角色的使用者只能存取相關的使用情況和採用計量。 他們並不具備任何系統管理權限，因此無法進行設定或存取產品特定的系統管理中心 (例如 Exchange)。 這個角色沒有檢視、建立或管理支援票證的存取權。

* **[搜尋系統管理員](#search-administrator)** :此角色中的使用者具有 Microsoft 365 系統管理中心內所有 Microsoft 搜尋管理功能的完整存取權。 搜尋系統管理員可以將 [搜尋管理員] 和 [搜尋編輯器] 角色委派給使用者, 以及建立和管理內容, 例如書簽、Q & As 和位置。 此外, 這些使用者可以查看訊息中心、監視服務健全狀況, 以及建立服務要求。

* **[搜尋編輯器](#search-editor)** :此角色中的使用者可以在 Microsoft 365 系統管理中心內建立、管理及刪除 Microsoft Search 的內容, 包括書簽、Q & As 和位置。

* **[安全性系統管理員](#security-administrator)** ：具備此角色的使用者有權限管理 Microsoft 365 資訊安全中心、Azure Active Directory Identity Protection、Azure 資訊保護和 Office 365 安全性與合規性中心中的安全性相關功能。 關於 Office 365 權限的詳細資訊可在 [Office 365 安全性與法規遵循中心的權限](https://support.office.com/article/Permissions-in-the-Office-365-Security-Compliance-Center-d10608af-7934-490a-818e-e68f17d0e9c1)中取得。
  
  在 | 可以執行
  --- | ---
  [Microsoft 365 資訊安全中心](https://protection.office.com) | 監視所有 Microsoft 365 服務的安全性相關原則<br>管理安全性威脅和警示<br>檢視報告
  身分識別防護中心 | 「安全性讀取者」角色的所有權限<br>此外，還能夠執行除了重設密碼以外的所有身分識別防護中心作業
  [Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-configure) | 「安全性讀取者」角色的所有權限<br>**無法**管理 Azure AD 角色指派或設定
  [Office 365 安全性與合規性中心](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d) | 管理安全性原則<br>檢視、調查及回應安全性威脅<br>檢視報告
  Azure 進階威脅防護 | 監視及回應可疑的安全性活動
  Windows Defender ATP 和 EDR | 指派角色<br>管理電腦群組<br>設定端點威脅偵測和自動補救<br>檢視、調查及回應警示
  [Intune](https://docs.microsoft.com/intune/role-based-access-control) | 檢視使用者、裝置、註冊、設定及應用程式資訊<br>無法對 Intune 進行變更
  [Cloud App Security](https://docs.microsoft.com/cloud-app-security/manage-admins) | 新增管理員、新增原則和設定、上傳記錄及執行治理動作
  [Azure 資訊安全中心](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles) | 可檢視安全性原則、檢視安全性狀態、編輯安全性原則、檢視警示和建議、關閉警示和建議
  [Office 365 服務健康情況](https://docs.microsoft.com/office365/enterprise/view-service-health) | 檢視 Office 365 服務的健康情況

* **[安全性運算子](#security-operator)** :具有此角色的使用者可以管理警示, 並具有安全性相關功能的全域唯讀存取權, 包括 Microsoft 365 資訊安全中心、Azure Active Directory、身分識別保護、Privileged Identity Management 和 Office 365 中的所有資訊安全性 & 合規性中心。 關於 Office 365 權限的詳細資訊可在 [Office 365 安全性與法規遵循中心的權限](https://docs.microsoft.com/office365/securitycompliance/permissions-in-the-security-and-compliance-center)中取得。

  在 | 可以執行
  --- | ---
  [Microsoft 365 資訊安全中心](https://protection.office.com) | 「安全性讀取者」角色的所有權限<br>查看、調查及回應安全性威脅警示
  身分識別防護中心 | 「安全性讀取者」角色的所有權限<br>此外，還能夠執行除了重設密碼以外的所有身分識別防護中心作業
  [Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-configure) | 「安全性讀取者」角色的所有權限
  [Office 365 安全性與合規性中心](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d) | 「安全性讀取者」角色的所有權限<br>查看、調查及回應安全性警示
  Windows Defender ATP 和 EDR | 「安全性讀取者」角色的所有權限<br>查看、調查及回應安全性警示
  [Intune](https://docs.microsoft.com/intune/role-based-access-control) | 「安全性讀取者」角色的所有權限
  [Cloud App Security](https://docs.microsoft.com/cloud-app-security/manage-admins) | 「安全性讀取者」角色的所有權限
  [Office 365 服務健康情況](https://docs.microsoft.com/office365/enterprise/view-service-health) | 檢視 Office 365 服務的健康情況
<!--* **[Security Operator](#security-operator)**: Users with this role can manage alerts and have global read-only access on security-related feature, including all information in Microsoft 365 security center, Azure Active Directory, Identity Protection, Privileged Identity Management.-->

* **[安全性讀取者](#security-reader)** ：具備此角色的使用者具有安全性相關功能的全域唯讀存取權 (含 Microsoft 365 資訊安全中心、Azure Active Directory、Identity Protection、Privileged Identity Management 中的所有資訊)，並能讀取 Azure Active Directory 登入報告與稽核記錄，且具有 Office 365 安全性與合規性中心的全域唯讀存取權。 關於 Office 365 權限的詳細資訊可在 [Office 365 安全性與法規遵循中心的權限](https://support.office.com/article/Permissions-in-the-Office-365-Security-Compliance-Center-d10608af-7934-490a-818e-e68f17d0e9c1)中取得。

  在 | 可以執行
  --- | ---
  [Microsoft 365 資訊安全中心](https://protection.office.com) | 檢視所有 Microsoft 365 服務的安全性相關原則<br>檢視安全性威脅和警示<br>檢視報告
  身分識別防護中心 | 讀取安全性功能的所有安全性報告和設定資訊<br><ul><li>反垃圾郵件<li>加密<li>資料外洩防護<li>反惡意程式碼<li>進階威脅防護<li>防網路釣魚<li>郵件流程規則
  [Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-configure) | 以唯讀方式存取 Azure AD PIM 中所顯示的一切資訊︰Azure AD 角色指派的原則和報告、安全性檢閱，以及在未來還可透過讀取來存取 Azure AD 角色指派以外案例的原則資料和報告。<br>**無法**註冊 Azure AD PIM 或對它進行任何變更。 在 PIM 入口網站中或透過 PowerShell, 如果使用者符合資格, 此角色中的人員可以啟用其他角色 (例如, 全域管理員或特殊許可權角色管理員)。
  [Office 365 安全性與合規性中心](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d) | 檢視安全性原則<br>檢視及調查安全性威脅<br>檢視報告
  Windows Defender ATP 和 EDR | 檢視和調查警示
  [Intune](https://docs.microsoft.com/intune/role-based-access-control) | 檢視使用者、裝置、註冊、設定及應用程式資訊。 無法對 Intune 進行變更。
  [Cloud App Security](https://docs.microsoft.com/cloud-app-security/manage-admins) | 具有唯讀權限，並可管理警示
  [Azure 資訊安全中心](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles) | 可檢視建議和警示、檢視安全性原則、檢視安全性狀態，但無法進行變更
  [Office 365 服務健康情況](https://docs.microsoft.com/office365/enterprise/view-service-health) | 檢視 Office 365 服務的健康情況

* **[服務支援管理員](#service-support-administrator)** ：具有此角色的使用者可以開啟 Microsoft for Azure 和 Office 365 服務的支援要求, 並在 [ [Azure 入口網站](https://portal.azure.com)] 和 [ [Microsoft 365 系統管理中心](https://admin.microsoft.com)] 中查看服務儀表板和訊息中心。 如需詳細資訊，請參閱 [關於 Office 365 管理員角色](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d)。
  > [!NOTE]
  > 在 Microsoft Graph API、Azure AD Graph API 和 Azure AD PowerShell 中，會將此角色識別為「服務支援管理員」。 這是[Azure 入口網站](https://portal.azure.com)、 [Microsoft 365 系統管理中心](https://admin.microsoft.com)和 Intune 入口網站中的「服務管理員」。

* **[SharePoint 管理員](#sharepoint-service-administrator)** ：具備此角色的使用者在有 Microsoft SharePoint Online 服務時，於該服務內具有全域權限，以及建立和管理所有 Office 365 群組、管理支援票證和監控服務健康情況的能力。 如需詳細資訊，請參閱 [關於 Office 365 管理員角色](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d)。
  > [!NOTE]
  > 在 Microsoft Graph API、Azure AD Graph API 和 Azure AD PowerShell 中，會將此角色識別為「SharePoint 服務管理員」。 在 [Azure 入口網站](https://portal.azure.com)中則是「SharePoint 管理員」。

* **[商務用 Skype/Lync 管理員](#lync-service-administrator)** ︰在有 Microsoft 商務用 Skype 服務時，具備此角色的使用者在該服務內會具有全域權限，以及在 Azure Active Directory 中管理 Skype 特定的使用者屬性。 此外，此角色會授與管理支援票證及監視服務健康情況的能力，以及存取 Microsoft Teams 和商務用 Skype 系統管理中心的能力。 此帳戶也必須獲得 Microsoft Teams 授權，否則就無法執行 Microsoft Teams PowerShell Cmdlet。 如需詳細資訊，請參閱[關於商務用 Skype 系統管理員角色](https://support.office.com/article/about-the-skype-for-business-admin-role-aeb35bda-93fc-49b1-ac2c-c74fbeb737b5)，如需 Microsoft Teams 的授權資訊，請參閱[商務用 Skype 和 Microsoft Teams 附加授權](https://docs.microsoft.com/skypeforbusiness/skype-for-business-and-microsoft-teams-add-on-licensing/skype-for-business-and-microsoft-teams-add-on-licensing)

  > [!NOTE]
  > 在 Microsoft Graph API、Azure AD Graph API 和 Azure AD PowerShell 中，會將此角色識別為「Lync 服務管理員」。 在 [Azure 入口網站](https://portal.azure.com/)中則是「商務用 Skype 管理員」。

* **[Teams 管理員](#teams-service-administrator)** ：此角色的使用者可以透過 Microsoft Teams 和商務用 Skype 系統管理中心以及個別的 PowerShell 模組，管理 Microsoft Teams 工作負載的所有層面。 這包括所有與電話語音、傳訊、會議和小組本身相關的管理工具以及其他領域。 這個角色會額外獲得授與建立和管理所有 Office 365 群組、管理支援票證，以及監視服務健康情況的能力。
  > [!NOTE]
  > 在 Microsoft Graph API、Azure AD Graph API 和 Azure AD PowerShell 中，會將此角色識別為「Microsoft Teams 服務管理員」。 在 [Azure 入口網站](https://portal.azure.com)中則是「Microsoft Teams 管理員」。

* **[Teams 通訊管理員](#teams-communications-administrator)** ：此角色的使用者可以管理 Microsoft Teams 在語音和電話語音相關工作負載的各個層面。 這包括電話號碼指派管理工具、語音和會議原則，以及呼叫分析工具組的完整存取權。

* **[Teams 通訊支援工程師 ](#teams-communications-support-engineer)** ：此角色的使用者可以使用 Microsoft Teams 和商務用 Skype 系統管理中心內的使用者呼叫疑難排解工具，針對 Microsoft Teams 和商務用 Skype 內的通訊問題進行疑難排解。 此角色的使用者可以檢視所有相關參與者的完整呼叫記錄資訊。 這個角色沒有檢視、建立或管理支援票證的存取權。

* **[Teams 通訊支援專家](#teams-communications-support-specialist)** ：此角色的使用者可以使用 Microsoft Teams 和商務用 Skype 系統管理中心內的使用者呼叫疑難排解工具，針對 Microsoft Teams 和商務用 Skype 內的通訊問題進行疑難排解。 此角色的使用者只能檢視其所查閱特定使用者的呼叫中所含有的使用者詳細資料。 這個角色沒有檢視、建立或管理支援票證的存取權。

* **[使用者系統管理員](#user-administrator)** :具有此角色的使用者可以建立使用者, 以及管理具有一些限制的使用者所有層面 (如下所示), 並可更新密碼到期原則。 此外，具有此角色的使用者可以建立與管理所有群組。 此角色也包含建立和管理使用者檢視、管理支援票證，以及監視服務健康情況的能力。

  | | |
  | --- | --- |
  |一般權限|<p>建立 [使用者和群組]</p><p>建立和管理使用者檢視</p><p>建立 Office 支援票證<p>更新密碼到期原則|
  |<p>所有使用者，包括所有管理員</p>|<p>管理授權</p><p>管理使用者主體名稱以外的所有使用者屬性</p>
  |只有非管理員或者下列任何有限管理員角色的使用者：<ul><li>目錄讀取器<li>來賓邀請者<li>服務台系統管理員<li>訊息中心讀取者<li>報告讀者<li>使用者管理員|<p>刪除及還原</p><p>停用和啟用</p><p>使重新整理權杖失效</p><p>管理包含使用者主體名稱的所有使用者屬性</p><p>重設密碼</p><p>更新 (FIDO) 裝置金鑰</p>
  
  <b>重要</b>：具備此角色的使用者可以變更可存取機密或私人資訊或 Azure Active Directory 內外重要組態的人員密碼。 變更使用者的密碼表示可承擔該使用者身分識別和權限。 例如:
  * 應用程式註冊和企業應用程式擁有者，他們可以管理他們自己的應用程式認證。 這些應用程式在 Azure AD 中可能有特殊權限，而在其他地方未授與使用者系統管理員。 使用者系統管理員可以透過此路徑承擔應用程式擁有者的身分識別，然後藉由更新應用程式的認證，進一步承擔特殊權限應用程式的身分識別。
  * Azure 訂用帳戶擁有者，他們具有機密或私人資訊或者 Azure 中重要組態的存取權。
  * 安全性群組和 Office 365 群組擁有者，他們可以管理群組成員資格。 這個群組可以存取機密或私人資訊或者 Azure AD 和其他位置中的重要組態。
  * Azure AD 外部其他服務 (例如，Exchange Online、Office 安全性與合規性中心和人力資源系統) 中的系統管理員。
  * 非系統管理員，例如主管、法律顧問和人力資源員工，他們可以存取機密或私人資訊。

## <a name="role-permissions"></a>角色權限
下表說明 Azure Active Directory 中賦予每個角色的特定權限。 某些角色在 Azure Active Directory 以外的 Microsoft 服務中可能有額外的權限。

### <a name="application-administrator"></a>應用程式系統管理員
能夠建立及管理應用程式註冊與企業應用程式的所有層面。

| **動作** | **說明** |
| --- | --- |
| microsoft.aad.directory/applications/audience/update | 在 Azure Active Directory 中更新 applications.audience 屬性。 |
| microsoft.aad.directory/applications/authentication/update | 在 Azure Active Directory 中更新 applications.authentication 屬性。 |
| microsoft.aad.directory/applications/basic/update | 更新 Azure Active Directory 中 Applications 的基本屬性。 |
| microsoft.aad.directory/applications/create | 在 Azure Active Directory 中建立應用程式。 |
| microsoft.aad.directory/applications/credentials/update | 在 Azure Active Directory 中更新 applications.credentials 屬性。 |
| microsoft.aad.directory/applications/delete | 刪除 Azure Active Directory 中的應用程式。 |
| microsoft.aad.directory/applications/owners/update | 更新 Azure Active Directory 中的 applications.owners 屬性。 |
| microsoft.aad.directory/applications/permissions/update | 在 Azure Active Directory 中更新 applications.permissions 屬性。 |
| microsoft.aad.directory/applications/policies/update | 更新 Azure Active Directory 中的 applications.policies 屬性。 |
| microsoft.aad.directory/appRoleAssignments/create | 在 Azure Active Directory 中建立 appRoleAssignments。 |
| microsoft.aad.directory/appRoleAssignments/read | 讀取 Azure Active Directory 中的 appRoleAssignments。 |
| microsoft.aad.directory/appRoleAssignments/update | 更新在 Azure Active Directory 中的 appRoleAssignments。 |
| microsoft.aad.directory/appRoleAssignments/delete | 刪除 Azure Active Directory 中的 appRoleAssignments。 |
| microsoft.aad.directory/auditLogs/allProperties/read | 讀取 Azure Active Directory 中的 auditLogs 所包含的所有屬性 (包括特殊權限的屬性)。 |
| microsoft.aad.directory/policies/applicationConfiguration/basic/read | 讀取 Azure Active Directory 中的 policies.applicationConfiguration 屬性。 |
| microsoft.aad.directory/policies/applicationConfiguration/basic/update | 更新 Azure Active Directory 中的 policies.applicationConfiguration 屬性。 |
| microsoft.aad.directory/policies/applicationConfiguration/create | 在 Azure Active Directory 中建立原則。 |
| microsoft.aad.directory/policies/applicationConfiguration/delete | 刪除 Azure Active Directory 中的原則。 |
| microsoft.aad.directory/policies/applicationConfiguration/owners/read | 讀取 Azure Active Directory 中的 policies.applicationConfiguration 屬性。 |
| microsoft.aad.directory/policies/applicationConfiguration/owners/update | 更新 Azure Active Directory 中的 policies.applicationConfiguration 屬性。 |
| microsoft.aad.directory/policies/applicationConfiguration/policyAppliedTo/read | 讀取 Azure Active Directory 中的 policies.applicationConfiguration 屬性。 |
| microsoft.aad.directory/servicePrincipals/appRoleAssignedTo/update | 更新 Azure Active Directory 中的 servicePrincipals.appRoleAssignedTo 屬性。 |
| microsoft.aad.directory/servicePrincipals/appRoleAssignments/update | 更新 Azure Active Directory 中的 servicePrincipals.appRoleAssignments 屬性。 |
| microsoft.aad.directory/servicePrincipals/audience/update | 更新 Azure Active Directory 中的 servicePrincipals.audience 屬性。 |
| microsoft.aad.directory/servicePrincipals/authentication/update | 更新 Azure Active Directory 中的 servicePrincipals.authentication 屬性。 |
| microsoft.aad.directory/servicePrincipals/basic/update | 更新 Azure Active Directory 中 servicePrincipals 的基本屬性。 |
| microsoft.aad.directory/servicePrincipals/create | 在 Azure Active Directory 中建立 servicePrincipals。 |
| microsoft.aad.directory/servicePrincipals/credentials/update | 更新 Azure Active Directory 中的 servicePrincipals.credentials 屬性。 |
| microsoft.aad.directory/servicePrincipals/delete | 刪除 Azure Active Directory 中的 servicePrincipals。 |
| microsoft.aad.directory/servicePrincipals/owners/update | 更新 Azure Active Directory 中的 servicePrincipals.owners 屬性。 |
| microsoft.aad.directory/servicePrincipals/permissions/update | 更新 Azure Active Directory 中的 servicePrincipals.permissions 屬性。 |
| microsoft.aad.directory/servicePrincipals/policies/update | 更新 Azure Active Directory 中的 servicePrincipals.policies 屬性。 |
| microsoft.aad.directory/signInReports/allProperties/read | 讀取 Azure Active Directory 中的 signInReports 所包含的所有屬性 (包括特殊權限的屬性)。 |
| microsoft.azure.serviceHealth/allEntities/allTasks | 讀取及設定 Azure 服務健康情況。 |
| microsoft.azure.supportTickets/allEntities/allTasks | 建立和管理 Azure 支援票證。 |
| microsoft.office365.serviceHealth/allEntities/allTasks | 讀取及設定 Office 365 服務健康情況。 |
| microsoft.office365.supportTickets/allEntities/allTasks | 建立和管理 Office 365 支援票證。 |

### <a name="application-developer"></a>應用程式開發人員
可建立與 [使用者可註冊應用程式] 設定不相關的應用程式註冊。

| **動作** | **描述** |
| --- | --- |
| microsoft.aad.directory/applications/createAsOwner | 在 Azure Active Directory 中建立應用程式。 建立者會新增為第一個擁有者，而建立的物件會算在建立者的 250 個建立物件配額中。 |
| microsoft.aad.directory/appRoleAssignments/createAsOwner | 在 Azure Active Directory 中建立 appRoleAssignments。 建立者會新增為第一個擁有者，而建立的物件會算在建立者的 250 個建立物件配額中。 |
| microsoft.aad.directory/oAuth2PermissionGrants/createAsOwner | 在 Azure Active Directory 中建立 oAuth2PermissionGrants。 建立者會新增為第一個擁有者，而建立的物件會算在建立者的 250 個建立物件配額中。 |
| microsoft.aad.directory/servicePrincipals/createAsOwner | 在 Azure Active Directory 中建立 servicePrincipals。 建立者會新增為第一個擁有者，而建立的物件會算在建立者的 250 個建立物件配額中。 |

### <a name="authentication-administrator"></a>驗證系統管理員
可存取以檢視、設定及重設所有非管理員使用者的驗證方法資訊。

| **動作** | **描述** |
| --- | --- |
| microsoft.aad.directory/users/invalidateAllRefreshTokens | 使 Azure Active Directory 中的所有使用者重新整理權杖失效。 |
| microsoft.aad.directory/users/strongAuthentication/update | 更新 MFA 認證資訊等增強式驗證屬性。 |
| microsoft.azure.serviceHealth/allEntities/allTasks | 讀取及設定 Azure 服務健康情況。 |
| microsoft.azure.supportTickets/allEntities/allTasks | 建立和管理 Azure 支援票證。 |
| microsoft.office365.webPortal/allEntities/basic/read | 讀取 microsoft.office365.webPortal 中所有資源的基本屬性。 |
| microsoft.office365.serviceHealth/allEntities/allTasks | 讀取及設定 Office 365 服務健康情況。 |
| microsoft.office365.supportTickets/allEntities/allTasks | 建立和管理 Office 365 支援票證。 |
| microsoft.aad.directory/users/password/update | 更新 Office 365 組織中所有使用者的密碼。 如需詳細資訊，請參閱線上文件。 |

### <a name="azure-information-protection-administrator"></a>Azure 資訊保護系統管理員
可以管理 Azure 資訊保護服務的所有層面。

  > [!NOTE]
  > 此角色具有 Azure Active Directory 以外的其他權限。 如需詳細資訊，請參閱前述角色說明。
  >
  >

| **動作** | **描述** |
| --- | --- |
| microsoft.azure.informationProtection/allEntities/allTasks | 管理 Azure 資訊保護的所有層面。 |
| microsoft.azure.serviceHealth/allEntities/allTasks | 讀取及設定 Azure 服務健康情況。 |
| microsoft.azure.supportTickets/allEntities/allTasks | 建立和管理 Azure 支援票證。 |
| microsoft.office365.serviceHealth/allEntities/allTasks | 讀取及設定 Office 365 服務健康情況。 |
| microsoft.office365.supportTickets/allEntities/allTasks | 建立和管理 Office 365 支援票證。 |

### <a name="b2c-user-flow-administrator"></a>B2C 使用者流程管理員
建立和管理使用者流程的所有層面。

| **動作** | **描述** |
| --- | --- |
| microsoft. aad. b2c/userFlows/allTasks | 在 Azure Active Directory B2C 中讀取和設定使用者流程。 |

### <a name="b2c-user-flow-attribute-administrator"></a>B2C 使用者流程屬性管理員
建立及管理所有使用者流程可用的屬性架構。

| **動作** | **描述** |
| --- | --- |
| microsoft. aad. b2c/userAttributes/allTasks | 在 Azure Active Directory B2C 中讀取和設定使用者屬性。 |

### <a name="b2c-ief-keyset-administrator"></a>B2C IEF 索引鍵集管理員
在 Identity Experience Framework 中管理同盟和加密的秘密。

| **動作** | **描述** |
| --- | --- |
| microsoft.aad.b2c/trustFramework/keySets/allTasks | 在 Azure Active Directory B2C 中讀取和設定金鑰集。 |

### <a name="b2c-ief-policy-administrator"></a>B2C IEF 原則管理員
在 Identity Experience Framework 中建立和管理信任架構原則。

| **動作** | **描述** |
| --- | --- |
| microsoft.aad.b2c/trustFramework/policies/allTasks | 在 Azure Active Directory B2C 中讀取和設定自訂原則。 |

### <a name="billing-administrator"></a>計費管理員
能夠執行一般計費相關工作，例如更新付款資訊。

  > [!NOTE]
  > 此角色具有 Azure Active Directory 以外的其他權限。 如需詳細資訊，請參閱前述角色說明。
  >
  >

| **動作** | **描述** |
| --- | --- |
| microsoft.aad.directory/organization/basic/update | 更新 Azure Active Directory 中 organization 的基本屬性。 |
| microsoft.azure.serviceHealth/allEntities/allTasks | 讀取及設定 Azure 服務健康情況。 |
| microsoft.azure.supportTickets/allEntities/allTasks | 建立和管理 Azure 支援票證。 |
| microsoft.commerce.billing/allEntities/allTasks | 管理 Office 365 帳單的所有層面。 |
| microsoft.office365.webPortal/allEntities/basic/read | 讀取 microsoft.office365.webPortal 中所有資源的基本屬性。 |
| microsoft.office365.serviceHealth/allEntities/allTasks | 讀取及設定 Office 365 服務健康情況。 |
| microsoft.office365.supportTickets/allEntities/allTasks | 建立和管理 Office 365 支援票證。 |


### <a name="cloud-application-administrator"></a>雲端應用程式系統管理員
能夠建立及管理應用程式註冊與企業應用程式的所有層面，但應用程式 Proxy 除外。

| **動作** | **描述** |
| --- | --- |
| microsoft.aad.directory/applications/audience/update | 在 Azure Active Directory 中更新 applications.audience 屬性。 |
| microsoft.aad.directory/applications/authentication/update | 在 Azure Active Directory 中更新 applications.authentication 屬性。 |
| microsoft.aad.directory/applications/basic/update | 更新 Azure Active Directory 中 Applications 的基本屬性。 |
| microsoft.aad.directory/applications/create | 在 Azure Active Directory 中建立應用程式。 |
| microsoft.aad.directory/applications/credentials/update | 在 Azure Active Directory 中更新 applications.credentials 屬性。 |
| microsoft.aad.directory/applications/delete | 刪除 Azure Active Directory 中的應用程式。 |
| microsoft.aad.directory/applications/owners/update | 更新 Azure Active Directory 中的 applications.owners 屬性。 |
| microsoft.aad.directory/applications/permissions/update | 在 Azure Active Directory 中更新 applications.permissions 屬性。 |
| microsoft.aad.directory/applications/policies/update | 更新 Azure Active Directory 中的 applications.policies 屬性。 |
| microsoft.aad.directory/appRoleAssignments/create | 在 Azure Active Directory 中建立 appRoleAssignments。 |
| microsoft.aad.directory/appRoleAssignments/update | 更新在 Azure Active Directory 中的 appRoleAssignments。 |
| microsoft.aad.directory/appRoleAssignments/delete | 刪除 Azure Active Directory 中的 appRoleAssignments。 |
| microsoft.aad.directory/auditLogs/allProperties/read | 讀取 Azure Active Directory 中的 auditLogs 所包含的所有屬性 (包括特殊權限的屬性)。 |
| microsoft.aad.directory/policies/applicationConfiguration/create | 在 Azure Active Directory 中建立原則。 |
| microsoft.aad.directory/policies/applicationConfiguration/basic/read | 讀取 Azure Active Directory 中的 policies.applicationConfiguration 屬性。 |
| microsoft.aad.directory/policies/applicationConfiguration/basic/update | 更新 Azure Active Directory 中的 policies.applicationConfiguration 屬性。 |
| microsoft.aad.directory/policies/applicationConfiguration/delete | 刪除 Azure Active Directory 中的原則。 |
| microsoft.aad.directory/policies/applicationConfiguration/owners/read | 讀取 Azure Active Directory 中的 policies.applicationConfiguration 屬性。 |
| microsoft.aad.directory/policies/applicationConfiguration/owners/update | 更新 Azure Active Directory 中的 policies.applicationConfiguration 屬性。 |
| microsoft.aad.directory/policies/applicationConfiguration/policyAppliedTo/read | 讀取 Azure Active Directory 中的 policies.applicationConfiguration 屬性。 |
| microsoft.aad.directory/servicePrincipals/appRoleAssignedTo/update | 更新 Azure Active Directory 中的 servicePrincipals.appRoleAssignedTo 屬性。 |
| microsoft.aad.directory/servicePrincipals/appRoleAssignments/update | 更新 Azure Active Directory 中的 servicePrincipals.appRoleAssignments 屬性。 |
| microsoft.aad.directory/servicePrincipals/audience/update | 更新 Azure Active Directory 中的 servicePrincipals.audience 屬性。 |
| microsoft.aad.directory/servicePrincipals/authentication/update | 更新 Azure Active Directory 中的 servicePrincipals.authentication 屬性。 |
| microsoft.aad.directory/servicePrincipals/basic/update | 更新 Azure Active Directory 中 servicePrincipals 的基本屬性。 |
| microsoft.aad.directory/servicePrincipals/create | 在 Azure Active Directory 中建立 servicePrincipals。 |
| microsoft.aad.directory/servicePrincipals/credentials/update | 更新 Azure Active Directory 中的 servicePrincipals.credentials 屬性。 |
| microsoft.aad.directory/servicePrincipals/delete | 刪除 Azure Active Directory 中的 servicePrincipals。 |
| microsoft.aad.directory/servicePrincipals/owners/update | 更新 Azure Active Directory 中的 servicePrincipals.owners 屬性。 |
| microsoft.aad.directory/servicePrincipals/permissions/update | 更新 Azure Active Directory 中的 servicePrincipals.permissions 屬性。 |
| microsoft.aad.directory/servicePrincipals/policies/update | 更新 Azure Active Directory 中的 servicePrincipals.policies 屬性。 |
| microsoft.aad.directory/signInReports/allProperties/read | 讀取 Azure Active Directory 中的 signInReports 所包含的所有屬性 (包括特殊權限的屬性)。 |
| microsoft.azure.serviceHealth/allEntities/allTasks | 讀取及設定 Azure 服務健康情況。 |
| microsoft.azure.supportTickets/allEntities/allTasks | 建立和管理 Azure 支援票證。 |
| microsoft.office365.serviceHealth/allEntities/allTasks | 讀取及設定 Office 365 服務健康情況。 |
| microsoft.office365.supportTickets/allEntities/allTasks | 建立和管理 Office 365 支援票證。 |

### <a name="cloud-device-administrator"></a>雲端裝置管理員
在 Azure AD 中管理裝置的完整存取。

| **動作** | **說明** |
| --- | --- |
| microsoft.aad.directory/auditLogs/allProperties/read | 讀取 Azure Active Directory 中的 auditLogs 所包含的所有屬性 (包括特殊權限的屬性)。 |
| microsoft.aad.directory/devices/bitLockerRecoveryKeys/read | 讀取 Azure Active Directory 中的 devices.bitLockerRecoveryKeys 屬性。 |
| microsoft.aad.directory/devices/delete | 刪除 Azure Active Directory 中的 devices。 |
| microsoft.aad.directory/devices/disable | 停用 Azure Active Directory 中的 devices。 |
| microsoft.aad.directory/devices/enable | 啟用 Azure Active Directory 中的裝置。 |
| microsoft.aad.directory/signInReports/allProperties/read | 讀取 Azure Active Directory 中的 signInReports 所包含的所有屬性 (包括特殊權限的屬性)。 |
| microsoft.azure.serviceHealth/allEntities/allTasks | 讀取及設定 Azure 服務健康情況。 |
| microsoft.office365.serviceHealth/allEntities/allTasks | 讀取及設定 Office 365 服務健康情況。 |

### <a name="company-administrator"></a>公司系統管理員
可管理使用 Azure AD 身分識別的 Azure AD 與 Microsoft 服務的所有層面。

  > [!NOTE]
  > 此角色具有 Azure Active Directory 以外的其他權限。 如需詳細資訊，請參閱前述角色說明。
  >
  >

| **動作** | **描述** |
| --- | --- |
| microsoft.aad.cloudAppSecurity/allEntities/allTasks | 建立和刪除所有資源，同時讀取及更新 microsoft.aad.cloudAppSecurity 中的標準屬性。 |
| microsoft.aad.directory/administrativeUnits/allProperties/allTasks | 建立和刪除 administrativeUnits，以及讀取與更新 Azure Active Directory 中的所有屬性。 |
| microsoft.aad.directory/applications/allProperties/allTasks | 建立和刪除應用程式，以及讀取與更新 Azure Active Directory 中的所有屬性。 |
| microsoft.aad.directory/appRoleAssignments/allProperties/allTasks | 建立和刪除 appRoleAssignments，以及讀取與更新 Azure Active Directory 中的所有屬性。 |
| microsoft.aad.directory/auditLogs/allProperties/read | 讀取 Azure Active Directory 中的 auditLogs 所包含的所有屬性 (包括特殊權限的屬性)。 |
| microsoft.aad.directory/contacts/allProperties/allTasks | 建立和刪除合約，以及讀取與更新 Azure Active Directory 中的所有屬性。 |
| microsoft.aad.directory/contracts/allProperties/allTasks | 建立和刪除合約，以及讀取與更新 Azure Active Directory 中的所有屬性。 |
| microsoft.aad.directory/devices/allProperties/allTasks | 建立和刪除裝置，以及在 Azure Active Directory 中讀取和更新所有屬性。 |
| microsoft.aad.directory/directoryRoles/allProperties/allTasks | 建立和刪除 directoryRoles，以及在 Azure Active Directory 中讀取和更新所有屬性。 |
| microsoft.aad.directory/directoryRoleTemplates/allProperties/allTasks | 建立和刪除 directoryRoleTemplates，以及在 Azure Active Directory 中讀取和更新所有屬性。 |
| microsoft.aad.directory/domains/allProperties/allTasks | 建立和刪除 domains，以及在 Azure Active Directory 中讀取和更新所有屬性。 |
| microsoft.aad.directory/groups/allProperties/allTasks | 建立和刪除 groups，以及讀取與更新 Azure Active Directory 中的所有屬性。 |
| microsoft.aad.directory/groupSettings/allProperties/allTasks | 建立和刪除 groupSettings，以及讀取與更新 Azure Active Directory 中的所有屬性。 |
| microsoft.aad.directory/groupSettingTemplates/allProperties/allTasks | 建立和刪除 groupSettingTemplates，以及讀取與更新 Azure Active Directory 中的所有屬性。 |
| microsoft.aad.directory/loginTenantBranding/allProperties/allTasks | 建立和刪除 loginTenantBranding，以及在 Azure Active Directory 中讀取和更新所有屬性。 |
| microsoft.aad.directory/oAuth2PermissionGrants/allProperties/allTasks | 建立和刪除 oAuth2PermissionGrants，以及在 Azure Active Directory 中讀取和更新所有屬性。 |
| microsoft.aad.directory/organization/allProperties/allTasks | 建立和刪除 organization，同時讀取及更新 Azure Active Directory 中的所有屬性。 |
| microsoft.aad.directory/policies/allProperties/allTasks | 建立和刪除 policies，以及在 Azure Active Directory 中讀取和更新所有屬性。 |
| microsoft.aad.directory/roleAssignments/allProperties/allTasks | 建立與刪除 roleAssignments，以及讀取與更新 Azure Active Directory 中的所有屬性。 |
| microsoft.aad.directory/roleDefinitions/allProperties/allTasks | 建立與刪除 roleDefinitions，以及讀取與更新 Azure Active Directory 中的所有屬性。 |
| microsoft.aad.directory/scopedRoleMemberships/allProperties/allTasks | 建立和刪除 scopedRoleMemberships，以及在 Azure Active Directory 中讀取和更新所有屬性。 |
| microsoft.aad.directory/serviceAction/activateService | 可以在 Azure Active Directory 中執行 Activateservice 服務動作 |
| microsoft.aad.directory/serviceAction/disableDirectoryFeature | 可以在 Azure Active Directory 中執行 Disabledirectoryfeature 服務動作 |
| microsoft.aad.directory/serviceAction/enableDirectoryFeature | 可以在 Azure Active Directory 中執行 Enabledirectoryfeature 服務動作 |
| microsoft.aad.directory/serviceAction/getAvailableExtentionProperties | 可以在 Azure Active Directory 中執行 Getavailableextentionproperties 服務動作 |
| microsoft.aad.directory/servicePrincipals/allProperties/allTasks | 建立和刪除 servicePrincipals，以及在 Azure Active Directory 中讀取和更新所有屬性。 |
| microsoft.aad.directory/signInReports/allProperties/read | 讀取 Azure Active Directory 中的 signInReports 所包含的所有屬性 (包括特殊權限的屬性)。 |
| microsoft.aad.directory/subscribedSkus/allProperties/allTasks | 建立和刪除 subscribedSkus，以及在 Azure Active Directory 中讀取和更新所有屬性。 |
| microsoft.aad.directory/users/allProperties/allTasks | 建立和刪除 users，以及在 Azure Active Directory 中讀取和更新所有屬性。 |
| microsoft.aad.directorySync/allEntities/allTasks | 執行 Azure AD Connect 中的所有動作。 |
| microsoft.aad.identityProtection/allEntities/allTasks | 建立和刪除所有資源，以及讀取和更新 microsoft.aad.identityProtection 中的標準屬性。 |
| microsoft.aad.privilegedIdentityManagement/allEntities/read | 讀取 microsoft.aad.privilegedIdentityManagement 中的所有資源。 |
| microsoft.azure.advancedThreatProtection/allEntities/read | 讀取 microsoft.aad.advancedThreatProtection 中的所有資源。 |
| microsoft.azure.informationProtection/allEntities/allTasks | 管理 Azure 資訊保護的所有層面。 |
| microsoft.azure.serviceHealth/allEntities/allTasks | 讀取及設定 Azure 服務健康情況。 |
| microsoft.azure.supportTickets/allEntities/allTasks | 建立和管理 Azure 支援票證。 |
| microsoft.commerce.billing/allEntities/allTasks | 管理 Office 365 帳單的所有層面。 |
| microsoft.intune/allEntities/allTasks | 管理 Intune 的所有層面。 |
| microsoft.office365.complianceManager/allEntities/allTasks | 管理 Office 365 合規性管理員的所有層面 |
| microsoft.office365.desktopAnalytics/allEntities/allTasks | 管理電腦分析的所有層面。 |
| microsoft.office365.exchange/allEntities/allTasks | 管理 Exchange Online 的所有層面。 |
| microsoft.office365.lockbox/allEntities/allTasks | 管理 Office 365 客戶加密箱的所有層面 |
| microsoft.office365.messageCenter/messages/read | 讀取 microsoft.office365.messageCenter 中的訊息。 |
| microsoft.office365.messageCenter/securityMessages/read | 讀取 microsoft.office365.messageCenter 中的 securityMessages。 |
| microsoft.office365.protectionCenter/allEntities/allTasks | 管理 Office 365 防護中心的所有層面。 |
| microsoft.office365.securityComplianceCenter/allEntities/allTasks | 建立和刪除所有資源，以及讀取和更新 microsoft.office365.securityComplianceCenter 中的標準屬性。 |
| microsoft.office365.serviceHealth/allEntities/allTasks | 讀取及設定 Office 365 服務健康情況。 |
| microsoft.office365.sharepoint/allEntities/allTasks | 建立和刪除所有資源，以及讀取和更新 microsoft.office365.sharepoint 中的標準屬性。 |
| microsoft.office365.skypeForBusiness/allEntities/allTasks | 管理商務用 Skype Online 的所有層面。 |
| microsoft.office365.supportTickets/allEntities/allTasks | 建立和管理 Office 365 支援票證。 |
| microsoft.office365.usageReports/allEntities/read | 讀取 Office 365 使用量報告。 |
| microsoft.office365.webPortal/allEntities/basic/read | 讀取 microsoft.office365.webPortal 中所有資源的基本屬性。 |
| microsoft.powerApps.dynamics365/allEntities/allTasks | 管理 Dynamics 365 的所有層面。 |
| microsoft.powerApps.powerBI/allEntities/allTasks | 管理 Power BI 的所有層面。 |
| microsoft.windows.defenderAdvancedThreatProtection/allEntities/read | 讀取 microsoft.aad.defenderAdvancedThreatProtection 中的所有資源。 |

### <a name="compliance-administrator"></a>規範管理員
可讀取和管理合規性設定及 Azure AD 與 Office 365 中的報告。

  > [!NOTE]
  > 此角色具有 Azure Active Directory 以外的其他權限。 如需詳細資訊，請參閱前述角色說明。
  >
  >

| **動作** | **描述** |
| --- | --- |
| microsoft.azure.serviceHealth/allEntities/allTasks | 讀取及設定 Azure 服務健康情況。 |
| microsoft.azure.supportTickets/allEntities/allTasks | 建立和管理 Azure 支援票證。 |
| microsoft.office365.webPortal/allEntities/basic/read | 讀取 microsoft.office365.webPortal 中所有資源的基本屬性。 |
| microsoft.office365.complianceManager/allEntities/allTasks | 管理 Office 365 合規性管理員的所有層面 |
| microsoft.office365.exchange/allEntities/allTasks | 管理 Exchange Online 的所有層面。 |
| microsoft.office365.serviceHealth/allEntities/allTasks | 讀取及設定 Office 365 服務健康情況。 |
| microsoft.office365.sharepoint/allEntities/allTasks | 建立和刪除所有資源，以及讀取和更新 microsoft.office365.sharepoint 中的標準屬性。 |
| microsoft.office365.skypeForBusiness/allEntities/allTasks | 管理商務用 Skype Online 的所有層面。 |
| microsoft.office365.supportTickets/allEntities/allTasks | 建立和管理 Office 365 支援票證。 |

### <a name="compliance-data-administrator"></a>合規性資料管理員
建立和管理合規性內容。

  > [!NOTE]
  > 此角色具有 Azure Active Directory 以外的其他權限。 如需詳細資訊，請參閱前述角色說明。
  >
  >

| **動作** | **描述** |
| --- | --- |
| microsoft.aad.cloudAppSecurity/allEntities/allTasks | 讀取和設定 Microsoft Cloud App Security。 |
| microsoft.azure.informationProtection/allEntities/allTasks | 管理 Azure 資訊保護的所有層面。 |
| microsoft.azure.serviceHealth/allEntities/allTasks | 讀取及設定 Azure 服務健康情況。 |
| microsoft.azure.supportTickets/allEntities/allTasks | 建立和管理 Azure 支援票證。 |
| microsoft.office365.webPortal/allEntities/basic/read | 讀取 microsoft.office365.webPortal 中所有資源的基本屬性。 |
| microsoft.office365.complianceManager/allEntities/allTasks | 管理 Office 365 合規性管理員的所有層面 |
| microsoft.office365.exchange/allEntities/allTasks | 管理 Exchange Online 的所有層面。 |
| microsoft.office365.serviceHealth/allEntities/allTasks | 讀取及設定 Office 365 服務健康情況。 |
| microsoft.office365.sharepoint/allEntities/allTasks | 建立和刪除所有資源，以及讀取和更新 microsoft.office365.sharepoint 中的標準屬性。 |
| microsoft.office365.skypeForBusiness/allEntities/allTasks | 管理商務用 Skype Online 的所有層面。 |
| microsoft.office365.supportTickets/allEntities/allTasks | 建立和管理 Office 365 支援票證。 |

### <a name="conditional-access-administrator"></a>條件式存取系統管理員
可以管理條件式存取功能。

| **動作** | **描述** |
| --- | --- |
| microsoft.aad.directory/policies/conditionalAccess/basic/read | 讀取 Azure Active Directory 中的 policies.conditionalAccess 屬性。 |
| microsoft.aad.directory/policies/conditionalAccess/basic/update | 更新 Azure Active Directory 中的 policies.conditionalAccess 屬性。 |
| microsoft.aad.directory/policies/conditionalAccess/create | 在 Azure Active Directory 中建立原則。 |
| microsoft.aad.directory/policies/conditionalAccess/delete | 刪除 Azure Active Directory 中的原則。 |
| microsoft.aad.directory/policies/conditionalAccess/owners/read | 讀取 Azure Active Directory 中的 policies.conditionalAccess 屬性。 |
| microsoft.aad.directory/policies/conditionalAccess/owners/update | 更新 Azure Active Directory 中的 policies.conditionalAccess 屬性。 |
| microsoft.aad.directory/policies/conditionalAccess/policiesAppliedTo/read | 讀取 Azure Active Directory 中的 policies.conditionalAccess 屬性。 |
| microsoft.aad.directory/policies/conditionalAccess/tenantDefault/update | 更新 Azure Active Directory 中的 policies.conditionalAccess 屬性。 |

### <a name="crm-service-administrator"></a>CRM 服務管理員
可管理 Dynamics 365 產品的所有層面。

  > [!NOTE]
  > 此角色具有 Azure Active Directory 以外的其他權限。 如需詳細資訊，請參閱前述角色說明。
  >
  >

| **動作** | **描述** |
| --- | --- |
| microsoft.azure.serviceHealth/allEntities/allTasks | 讀取及設定 Azure 服務健康情況。 |
| microsoft.azure.supportTickets/allEntities/allTasks | 建立和管理 Azure 支援票證。 |
| microsoft.powerApps.dynamics365/allEntities/allTasks | 管理 Dynamics 365 的所有層面。 |
| microsoft.office365.webPortal/allEntities/basic/read | 讀取 microsoft.office365.webPortal 中所有資源的基本屬性。 |
| microsoft.office365.serviceHealth/allEntities/allTasks | 讀取及設定 Office 365 服務健康情況。 |
| microsoft.office365.supportTickets/allEntities/allTasks | 建立和管理 Office 365 支援票證。 |

### <a name="customer-lockbox-access-approver"></a>客戶 LockBox 存取核准者
可核准 Microsoft 支援要求，以存取客戶組織的資料。

  > [!NOTE]
  > 此角色具有 Azure Active Directory 以外的其他權限。 如需詳細資訊，請參閱前述角色說明。
  >
  >

| **動作** | **描述** |
| --- | --- |
| microsoft.office365.webPortal/allEntities/basic/read | 讀取 microsoft.office365.webPortal 中所有資源的基本屬性。 |
| microsoft.office365.lockbox/allEntities/allTasks | 管理 Office 365 客戶加密箱的所有層面 |

### <a name="desktop-analytics-administrator"></a>電腦分析系統管理員
可以管理電腦分析和 Office 自訂 & 原則服務。 針對電腦分析, 這包括能夠查看資產清查、建立部署計畫、查看部署和健康狀態。 若為 Office 自訂 & 原則服務, 此角色可讓使用者管理 Office 原則。

  > [!NOTE]
  > 此角色具有 Azure Active Directory 以外的其他權限。 如需詳細資訊，請參閱前述角色說明。
  >
  >

| **動作** | **說明** |
| --- | --- |
| microsoft.azure.serviceHealth/allEntities/allTasks | 讀取及設定 Azure 服務健康情況。 |
| microsoft.azure.supportTickets/allEntities/allTasks | 建立和管理 Azure 支援票證。 |
| microsoft.office365.webPortal/allEntities/basic/read | 讀取 microsoft.office365.webPortal 中所有資源的基本屬性。 |
| microsoft.office365.desktopAnalytics/allEntities/allTasks | 管理電腦分析的所有層面。 |
| microsoft.office365.serviceHealth/allEntities/allTasks | 讀取及設定 Office 365 服務健康情況。 |
| microsoft.office365.supportTickets/allEntities/allTasks | 建立和管理 Office 365 支援票證。 |

### <a name="device-administrators"></a>裝置系統管理員
指派給此角色的使用者會新增至已加入 Azure AD 的裝置上的本機系統管理員群組。

| **動作** | **說明** |
| --- | --- |
| microsoft.aad.directory/groupSettings/basic/read | 讀取 Azure Active Directory 中 groupSettings 的基本屬性。 |
| microsoft.aad.directory/groupSettingTemplates/basic/read | 讀取 Azure Active Directory 中 groupSettingTemplates 的基本屬性。 |

### <a name="directory-readers"></a>目錄讀取器
可讀取基本目錄資訊。 用來授與應用程式的存取權，不適用於使用者。

| **動作** | **說明** |
| --- | --- |
| microsoft.aad.directory/administrativeUnits/basic/read | 讀取 Azure Active Directory 中 administrativeUnits 的基本屬性。 |
| microsoft.aad.directory/administrativeUnits/members/read | 讀取 Azure Active Directory 中的 administrativeUnits.members 屬性。 |
| microsoft.aad.directory/applications/basic/read | 讀取 Azure Active Directory 中 Applications 的基本屬性。 |
| microsoft.aad.directory/applications/owners/read | 讀取 Azure Active Directory 中的 applications.owners 屬性。 |
| microsoft.aad.directory/applications/policies/read | 讀取 Azure Active Directory 中的 applications.policies 屬性。 |
| microsoft.aad.directory/contacts/basic/read | 讀取 Azure Active Directory 中 contacts 的基本屬性。 |
| microsoft.aad.directory/contacts/memberOf/read | 讀取 Azure Active Directory 中的 contacts.memberOf 屬性。 |
| microsoft.aad.directory/contracts/basic/read | 讀取 Azure Active Directory 中 contracts 的基本屬性。 |
| microsoft.aad.directory/devices/basic/read | 讀取 Azure Active Directory 中 devices 的基本屬性。 |
| microsoft.aad.directory/devices/memberOf/read | 讀取 Azure Active Directory 中的 devices.memberOf 屬性。 |
| microsoft.aad.directory/devices/registeredOwners/read | 讀取 Azure Active Directory 中的 devices.registeredOwners 屬性。 |
| microsoft.aad.directory/devices/registeredUsers/read | 讀取 Azure Active Directory 中的 devices.registeredUsers 屬性。 |
| microsoft.aad.directory/directoryRoles/basic/read | 讀取 Azure Active Directory 中 directoryRoles 的基本屬性。 |
| microsoft.aad.directory/directoryRoles/eligibleMembers/read | 讀取 Azure Active Directory 中的 directoryRoles.eligibleMembers 屬性。 |
| microsoft.aad.directory/directoryRoles/members/read | 讀取 Azure Active Directory 中的 directoryRoles.members 屬性。 |
| microsoft.aad.directory/domains/basic/read | 讀取 Azure Active Directory 中 domain 的基本屬性。 |
| microsoft.aad.directory/groups/appRoleAssignments/read | 讀取 Azure Active Directory 中的 groups.appRoleAssignments 屬性。 |
| microsoft.aad.directory/groups/basic/read | 讀取 Azure Active Directory 中 groups 的基本屬性。 |
| microsoft.aad.directory/groups/memberOf/read | 讀取 Azure Active Directory 中的 Read groups.memberOf 屬性。 |
| microsoft.aad.directory/groups/members/read | 讀取 Azure Active Directory 中的 groups.members 屬性。 |
| microsoft.aad.directory/groups/owners/read | 讀取 Azure Active Directory 中的 groups.owners 屬性。 |
| microsoft.aad.directory/groups/settings/read | 讀取 Azure Active Directory 中的 groups.settings 屬性。 |
| microsoft.aad.directory/groupSettings/basic/read | 讀取 Azure Active Directory 中 groupSettings 的基本屬性。 |
| microsoft.aad.directory/groupSettingTemplates/basic/read | 讀取 Azure Active Directory 中 groupSettingTemplates 的基本屬性。 |
| microsoft.aad.directory/oAuth2PermissionGrants/basic/read | 讀取 Azure Active Directory 中 oAuth2PermissionGrants 的基本屬性。 |
| microsoft.aad.directory/organization/basic/read | 讀取 Azure Active Directory 中 organization 的基本屬性。 |
| microsoft.aad.directory/organization/trustedCAsForPasswordlessAuth/read | 讀取 Azure Active Directory 中的 organization.trustedCAsForPasswordlessAuth 屬性。 |
| microsoft.aad.directory/roleAssignments/basic/read | 讀取 Azure Active Directory 中 roleAssignments 的基本屬性。 |
| microsoft.aad.directory/roleDefinitions/basic/read | 讀取 Azure Active Directory 中 roleDefinitions 的基本屬性。 |
| microsoft.aad.directory/servicePrincipals/appRoleAssignedTo/read | 讀取 Azure Active Directory 中的 servicePrincipals.appRoleAssignedTo 屬性。 |
| microsoft.aad.directory/servicePrincipals/appRoleAssignments/read | 讀取 Azure Active Directory 中的 servicePrincipals.appRoleAssignments 屬性。 |
| microsoft.aad.directory/servicePrincipals/basic/read | 讀取 Azure Active Directory 中 servicePrincipals 的基本屬性。 |
| microsoft.aad.directory/servicePrincipals/memberOf/read | 讀取 Azure Active Directory 中的 servicePrincipals.memberOf 屬性。 |
| microsoft.aad.directory/servicePrincipals/oAuth2PermissionGrants/basic/read | 讀取 Azure Active Directory 中的 servicePrincipals.oAuth2PermissionGrants 屬性。 |
| microsoft.aad.directory/servicePrincipals/ownedObjects/read | 讀取 Azure Active Directory 中的 servicePrincipals.ownedObjects 屬性。 |
| microsoft.aad.directory/servicePrincipals/owners/read | 讀取 Azure Active Directory 中的 servicePrincipals.owners 屬性。 |
| microsoft.aad.directory/servicePrincipals/policies/read | 讀取 Azure Active Directory 中的 servicePrincipals.policies 屬性。 |
| microsoft.aad.directory/subscribedSkus/basic/read | 讀取 Azure Active Directory 中 subscribedSkus 的基本屬性。 |
| microsoft.aad.directory/users/appRoleAssignments/read | 讀取 Azure Active Directory 中的 users.appRoleAssignments 屬性。 |
| microsoft.aad.directory/users/basic/read | 讀取 Azure Active Directory 中 users 的基本屬性。 |
| microsoft.aad.directory/users/directReports/read | 讀取 Azure Active Directory 中的 users.directReports 屬性。 |
| microsoft.aad.directory/users/manager/read | 讀取 Azure Active Directory 中的 users.manager 屬性。 |
| microsoft.aad.directory/users/memberOf/read | 讀取 Azure Active Directory 中的 users.memberOf 屬性。 |
| microsoft.aad.directory/users/oAuth2PermissionGrants/basic/read | 讀取 Azure Active Directory 中的 users.oAuth2PermissionGrants 屬性。 |
| microsoft.aad.directory/users/ownedDevices/read | 讀取 Azure Active Directory 中的 users.ownedDevices 屬性。 |
| microsoft.aad.directory/users/ownedObjects/read | 讀取 Azure Active Directory 中的 users.ownedObjects 屬性。 |
| microsoft.aad.directory/users/registeredDevices/read | 讀取 Azure Active Directory 中的 users.registeredDevices 屬性。 |

### <a name="directory-synchronization-accounts"></a>目錄同步處理帳戶
僅供 Azure AD Connect 服務使用。

| **動作** | **描述** |
| --- | --- |
| microsoft.aad.directory/organization/dirSync/update | 更新 Azure Active Directory 中的 organization.dirSync 屬性。 |
| microsoft.aad.directory/policies/create | 在 Azure Active Directory 中建立原則。 |
| microsoft.aad.directory/policies/delete | 刪除 Azure Active Directory 中的原則。 |
| microsoft.aad.directory/policies/basic/read | 讀取 Azure Active Directory 中 policies 的基本屬性。 |
| microsoft.aad.directory/policies/basic/update | 更新 Azure Active Directory 中 policies 的基本屬性。 |
| microsoft.aad.directory/policies/owners/read | 讀取 Azure Active Directory 中的 policies.owners 屬性。 |
| microsoft.aad.directory/policies/owners/update | 更新 Azure Active Directory 中的 policies.owners 屬性。 |
| microsoft.aad.directory/policies/policiesAppliedTo/read | 讀取 Azure Active Directory 中的 policies.policiesAppliedTo 屬性。 |
| microsoft.aad.directory/policies/tenantDefault/update | 更新 Azure Active Directory 中的 policies.tenantDefault 屬性。 |
| microsoft.aad.directory/servicePrincipals/appRoleAssignedTo/read | 讀取 Azure Active Directory 中的 servicePrincipals.appRoleAssignedTo 屬性。 |
| microsoft.aad.directory/servicePrincipals/appRoleAssignedTo/update | 更新 Azure Active Directory 中的 servicePrincipals.appRoleAssignedTo 屬性。 |
| microsoft.aad.directory/servicePrincipals/appRoleAssignments/read | 讀取 Azure Active Directory 中的 servicePrincipals.appRoleAssignments 屬性。 |
| microsoft.aad.directory/servicePrincipals/appRoleAssignments/update | 更新 Azure Active Directory 中的 servicePrincipals.appRoleAssignments 屬性。 |
| microsoft.aad.directory/servicePrincipals/audience/update | 更新 Azure Active Directory 中的 servicePrincipals.audience 屬性。 |
| microsoft.aad.directory/servicePrincipals/authentication/update | 更新 Azure Active Directory 中的 servicePrincipals.authentication 屬性。 |
| microsoft.aad.directory/servicePrincipals/basic/read | 讀取 Azure Active Directory 中 servicePrincipals 的基本屬性。 |
| microsoft.aad.directory/servicePrincipals/basic/update | 更新 Azure Active Directory 中 servicePrincipals 的基本屬性。 |
| microsoft.aad.directory/servicePrincipals/create | 在 Azure Active Directory 中建立 servicePrincipals。 |
| microsoft.aad.directory/servicePrincipals/credentials/update | 更新 Azure Active Directory 中的 servicePrincipals.credentials 屬性。 |
| microsoft.aad.directory/servicePrincipals/memberOf/read | 讀取 Azure Active Directory 中的 servicePrincipals.memberOf 屬性。 |
| microsoft.aad.directory/servicePrincipals/oAuth2PermissionGrants/basic/read | 讀取 Azure Active Directory 中的 servicePrincipals.oAuth2PermissionGrants 屬性。 |
| microsoft.aad.directory/servicePrincipals/owners/read | 讀取 Azure Active Directory 中的 servicePrincipals.owners 屬性。 |
| microsoft.aad.directory/servicePrincipals/owners/update | 更新 Azure Active Directory 中的 servicePrincipals.owners 屬性。 |
| microsoft.aad.directory/servicePrincipals/ownedObjects/read | 讀取 Azure Active Directory 中的 servicePrincipals.ownedObjects 屬性。 |
| microsoft.aad.directory/servicePrincipals/permissions/update | 更新 Azure Active Directory 中的 servicePrincipals.permissions 屬性。 |
| microsoft.aad.directory/servicePrincipals/policies/read | 讀取 Azure Active Directory 中的 servicePrincipals.policies 屬性。 |
| microsoft.aad.directory/servicePrincipals/policies/update | 更新 Azure Active Directory 中的 servicePrincipals.policies 屬性。 |
| microsoft.aad.directorySync/allEntities/allTasks | 執行 Azure AD Connect 中的所有動作。 |

### <a name="directory-writers"></a>目錄撰寫者
可讀取和寫入基本目錄資訊。 用來授與應用程式的存取權，不適用於使用者。

| **動作** | **描述** |
| --- | --- |
| microsoft.aad.directory/groups/create | 在 Azure Active Directory 中建立 groups。 |
| microsoft.aad.directory/groups/createAsOwner | 在 Azure Active Directory 中建立 groups。 建立者會新增為第一個擁有者，而建立的物件會算在建立者的 250 個建立物件配額中。 |
| microsoft.aad.directory/groups/appRoleAssignments/update | 更新 Azure Active Directory 中的 groups.appRoleAssignments 屬性。 |
| microsoft.aad.directory/groups/basic/update | 更新 Azure Active Directory 中 groups 的基本屬性。 |
| microsoft.aad.directory/groups/members/update | 更新 Azure Active Directory 中的 groups.members 屬性。 |
| microsoft.aad.directory/groups/owners/update | 更新 Azure Active Directory 中的 groups.owners 屬性。 |
| microsoft.aad.directory/groups/settings/update | 更新 Azure Active Directory 中的 groups.settings 屬性。 |
| microsoft.aad.directory/groupSettings/basic/update | 更新 Azure Active Directory 中 groupSettings 的基本屬性。 |
| microsoft.aad.directory/groupSettings/create | 在 Azure Active Directory 中建立 groupSettings。 |
| microsoft.aad.directory/groupSettings/delete | 在 Azure Active Directory 中刪除 groupSettings。 |
| microsoft.aad.directory/users/appRoleAssignments/update | 更新 Azure Active Directory 中的 users.appRoleAssignments 屬性。 |
| microsoft.aad.directory/users/assignLicense | 管理 Azure Active Directory 中的使用者授權。 |
| microsoft.aad.directory/users/basic/update | 更新 Azure Active Directory 中 users 的基本屬性。 |
| microsoft.aad.directory/users/invalidateAllRefreshTokens | 使 Azure Active Directory 中的所有使用者重新整理權杖失效。 |
| microsoft.aad.directory/users/manager/update | 更新 Azure Active Directory 中的 users.manager 屬性。 |
| microsoft.aad.directory/users/userPrincipalName/update | 更新 Azure Active Directory 中的 users.userPrincipalName 屬性。 |

### <a name="exchange-service-administrator"></a>Exchange 服務管理員
可管理 Exchange 產品的所有層面。

  > [!NOTE]
  > 此角色具有 Azure Active Directory 以外的其他權限。 如需詳細資訊，請參閱前述角色說明。
  >
  >

| **動作** | **描述** |
| --- | --- |
| microsoft.aad.directory/groups/unified/appRoleAssignments/update | 更新 Azure Active Directory 中的 groups.unified 屬性。 |
| microsoft.aad.directory/groups/unified/basic/update | 更新 Office 365 群組的基本屬性。 |
| microsoft.aad.directory/groups/unified/create | 建立 Office 365 群組。 |
| microsoft.aad.directory/groups/unified/delete | 刪除 Office 365 群組。 |
| microsoft.aad.directory/groups/unified/members/update | 更新 Office 365 群組的成員資格。 |
| microsoft.aad.directory/groups/unified/owners/update | 更新 Office 365 群組的擁有權。 |
| microsoft.azure.serviceHealth/allEntities/allTasks | 讀取及設定 Azure 服務健康情況。 |
| microsoft.azure.supportTickets/allEntities/allTasks | 建立和管理 Azure 支援票證。 |
| microsoft.office365.webPortal/allEntities/basic/read | 讀取 microsoft.office365.webPortal 中所有資源的基本屬性。 |
| microsoft.office365.exchange/allEntities/allTasks | 管理 Exchange Online 的所有層面。 |
| microsoft.office365.serviceHealth/allEntities/allTasks | 讀取及設定 Office 365 服務健康情況。 |
| microsoft.office365.supportTickets/allEntities/allTasks | 建立和管理 Office 365 支援票證。 |

### <a name="external-identity-provider-administrator"></a>外部識別提供者系統管理員
設定要在直接同盟中使用的識別提供者。

| **動作** | **描述** |
| --- | --- |
| microsoft. aad. b2c/identityProviders/allTasks | 在 Azure Active Directory B2C 中讀取和設定識別提供者。 |

### <a name="guest-inviter"></a>來賓邀請者
能夠邀請不受 [成員能夠邀請來賓] 設定限制的來賓使用者。

| **動作** | **描述** |
| --- | --- |
| microsoft.aad.directory/users/appRoleAssignments/read | 讀取 Azure Active Directory 中的 users.appRoleAssignments 屬性。 |
| microsoft.aad.directory/users/basic/read | 讀取 Azure Active Directory 中 users 的基本屬性。 |
| microsoft.aad.directory/users/directReports/read | 讀取 Azure Active Directory 中的 users.directReports 屬性。 |
| microsoft.aad.directory/users/inviteGuest | 邀請 Azure Active Directory 中的來賓使用者。 |
| microsoft.aad.directory/users/manager/read | 讀取 Azure Active Directory 中的 users.manager 屬性。 |
| microsoft.aad.directory/users/memberOf/read | 讀取 Azure Active Directory 中的 users.memberOf 屬性。 |
| microsoft.aad.directory/users/oAuth2PermissionGrants/basic/read | 讀取 Azure Active Directory 中的 users.oAuth2PermissionGrants 屬性。 |
| microsoft.aad.directory/users/ownedDevices/read | 讀取 Azure Active Directory 中的 users.ownedDevices 屬性。 |
| microsoft.aad.directory/users/ownedObjects/read | 讀取 Azure Active Directory 中的 users.ownedObjects 屬性。 |
| microsoft.aad.directory/users/registeredDevices/read | 讀取 Azure Active Directory 中的 users.registeredDevices 屬性。 |

### <a name="helpdesk-administrator"></a>服務台系統管理員
能夠為非系統管理員與技術服務人員系統管理員重設密碼。

| **動作** | **描述** |
| --- | --- |
| microsoft.aad.directory/devices/bitLockerRecoveryKeys/read | 讀取 Azure Active Directory 中的 devices.bitLockerRecoveryKeys 屬性。 |
| microsoft.aad.directory/users/invalidateAllRefreshTokens | 使 Azure Active Directory 中的所有使用者重新整理權杖失效。 |
| microsoft.aad.directory/users/password/update | 在 Azure Active Directory 中更新所有使用者的密碼。 如需詳細資訊，請參閱線上文件。 |
| microsoft.azure.serviceHealth/allEntities/allTasks | 讀取及設定 Azure 服務健康情況。 |
| microsoft.azure.supportTickets/allEntities/allTasks | 建立和管理 Azure 支援票證。 |
| microsoft.office365.webPortal/allEntities/basic/read | 讀取 microsoft.office365.webPortal 中所有資源的基本屬性。 |
| microsoft.office365.serviceHealth/allEntities/allTasks | 讀取及設定 Office 365 服務健康情況。 |
| microsoft.office365.supportTickets/allEntities/allTasks | 建立和管理 Office 365 支援票證。 |

### <a name="intune-service-administrator"></a>Intune 服務管理員
可管理 Intune 產品的所有層面。

  > [!NOTE]
  > 此角色具有 Azure Active Directory 以外的其他權限。 如需詳細資訊，請參閱前述角色說明。
  >
  >

| **動作** | **描述** |
| --- | --- |
| microsoft.aad.directory/contacts/basic/update | 更新 Azure Active Directory 中 contacts 的基本屬性。 |
| microsoft.aad.directory/contacts/create | 在 Azure Active Directory 中建立 contacts。 |
| microsoft.aad.directory/contacts/delete | 刪除 Azure Active Directory 中的 contacts。 |
| microsoft.aad.directory/devices/basic/update | 更新 Azure Active Directory 中 devices 的基本屬性。 |
| microsoft.aad.directory/devices/bitLockerRecoveryKeys/read | 讀取 Azure Active Directory 中的 devices.bitLockerRecoveryKeys 屬性。 |
| microsoft.aad.directory/devices/create | 在 Azure Active Directory 中建立 devices。 |
| microsoft.aad.directory/devices/delete | 刪除 Azure Active Directory 中的 devices。 |
| microsoft.aad.directory/devices/registeredOwners/update | 更新 Azure Active Directory 中的 devices.registeredOwners 屬性。 |
| microsoft.aad.directory/devices/registeredUsers/update | 更新 Azure Active Directory 中的 devices.registeredUsers 屬性。 |
| microsoft.aad.directory/groups/appRoleAssignments/update | 更新 Azure Active Directory 中的 groups.appRoleAssignments 屬性。 |
| microsoft.aad.directory/groups/basic/update | 更新 Azure Active Directory 中 groups 的基本屬性。 |
| microsoft.aad.directory/groups/create | 在 Azure Active Directory 中建立 groups。 |
| microsoft.aad.directory/groups/createAsOwner | 在 Azure Active Directory 中建立 groups。 建立者會新增為第一個擁有者，而建立的物件會算在建立者的 250 個建立物件配額中。 |
| microsoft.aad.directory/groups/delete | 刪除 Azure Active Directory 中的 groups。 |
| microsoft.aad.directory/groups/hiddenMembers/read | 讀取 Azure Active Directory 中的 groups.hiddenMembers 屬性。 |
| microsoft.aad.directory/groups/members/update | 更新 Azure Active Directory 中的 groups.members 屬性。 |
| microsoft.aad.directory/groups/owners/update | 更新 Azure Active Directory 中的 groups.owners 屬性。 |
| microsoft.aad.directory/groups/restore | 還原 Azure Active Directory 中的 groups。 |
| microsoft.aad.directory/groups/settings/update | 更新 Azure Active Directory 中的 groups.settings 屬性。 |
| microsoft.aad.directory/users/appRoleAssignments/update | 更新 Azure Active Directory 中的 users.appRoleAssignments 屬性。 |
| microsoft.aad.directory/users/basic/update | 更新 Azure Active Directory 中 users 的基本屬性。 |
| microsoft.aad.directory/users/manager/update | 更新 Azure Active Directory 中的 users.manager 屬性。 |
| microsoft.azure.supportTickets/allEntities/allTasks | 建立和管理 Azure 支援票證。 |
| microsoft.intune/allEntities/allTasks | 管理 Intune 的所有層面。 |
| microsoft.office365.supportTickets/allEntities/allTasks | 建立和管理 Office 365 支援票證。 |
| microsoft.office365.webPortal/allEntities/basic/read | 讀取 microsoft.office365.webPortal 中所有資源的基本屬性。 |

### <a name="kaizala-administrator"></a>Kaizala 系統管理員
可以管理 Microsoft Kaizala 的設定。  

  > [!NOTE]
  > 此角色具有 Azure Active Directory 以外的其他權限。 如需詳細資訊，請參閱前述角色說明。
  >
  >  
  
| **動作** | **說明** |
| --- | --- |
| microsoft.office365.serviceHealth/allEntities/allTasks | 讀取及設定 Office 365 服務健康情況。 |
| microsoft.office365.supportTickets/allEntities/allTasks | 建立和管理 Office 365 支援票證。 |
| microsoft.office365.webPortal/allEntities/basic/read | 閱讀 Office 365 系統管理中心。 |

### <a name="license-administrator"></a>授權管理員
可管理使用者和群組的產品授權。

| **動作** | **說明** |
| --- | --- |
| microsoft.aad.directory/users/assignLicense | 管理 Azure Active Directory 中的使用者授權。 |
| microsoft.aad.directory/users/usageLocation/update | 更新 Azure Active Directory 中的 users.usageLocation 屬性。 |
| microsoft.azure.serviceHealth/allEntities/allTasks | 讀取及設定 Azure 服務健康情況。 |
| microsoft.office365.webPortal/allEntities/basic/read | 讀取 microsoft.office365.webPortal 中所有資源的基本屬性。 |
| microsoft.office365.serviceHealth/allEntities/allTasks | 讀取及設定 Office 365 服務健康情況。 |

### <a name="lync-service-administrator"></a>Lync 服務管理員
可管理商務用 Skype 產品的所有層面。

  > [!NOTE]
  > 此角色具有 Azure Active Directory 以外的其他權限。 如需詳細資訊，請參閱前述角色說明。
  >
  >

| **動作** | **描述** |
| --- | --- |
| microsoft.azure.serviceHealth/allEntities/allTasks | 讀取及設定 Azure 服務健康情況。 |
| microsoft.azure.supportTickets/allEntities/allTasks | 建立和管理 Azure 支援票證。 |
| microsoft.office365.webPortal/allEntities/basic/read | 讀取 microsoft.office365.webPortal 中所有資源的基本屬性。 |
| microsoft.office365.serviceHealth/allEntities/allTasks | 讀取及設定 Office 365 服務健康情況。 |
| microsoft.office365.skypeForBusiness/allEntities/allTasks | 管理商務用 Skype Online 的所有層面。 |
| microsoft.office365.supportTickets/allEntities/allTasks | 建立和管理 Office 365 支援票證。 |

### <a name="message-center-privacy-reader"></a>訊息中心隱私權讀者
可以讀取訊息中心文章、資料隱私權訊息、群組、網域和訂閱。

  > [!NOTE]
  > 此角色具有 Azure Active Directory 以外的其他權限。 如需詳細資訊，請參閱前述角色說明。
  >
  >

| **動作** | **說明** |
| --- | --- |
| microsoft.office365.webPortal/allEntities/basic/read | 讀取 microsoft.office365.webPortal 中所有資源的基本屬性。 |
| microsoft.office365.messageCenter/messages/read | 讀取 microsoft.office365.messageCenter 中的訊息。 |
| microsoft.office365.messageCenter/securityMessages/read | 讀取 microsoft.office365.messageCenter 中的 securityMessages。 |

### <a name="message-center-reader"></a>訊息中心讀取者
只可在 Office 365 訊息中心讀取及更新其組織的訊息。 

  > [!NOTE]
  > 此角色具有 Azure Active Directory 以外的其他權限。 如需詳細資訊，請參閱前述角色說明。
  >
  >

| **動作** | **說明** |
| --- | --- |
| microsoft.office365.webPortal/allEntities/basic/read | 讀取 microsoft.office365.webPortal 中所有資源的基本屬性。 |
| microsoft.office365.messageCenter/messages/read | 讀取 microsoft.office365.messageCenter 中的訊息。 |

### <a name="partner-tier1-support"></a>合作夥伴第 1 層支援
請勿使用 - 不適用於一般用途。

  > [!NOTE]
  > 此角色具有 Azure Active Directory 以外的其他權限。 如需詳細資訊，請參閱前述角色說明。
  >
  >

| **動作** | **說明** |
| --- | --- |
| microsoft.aad.directory/contacts/basic/update | 更新 Azure Active Directory 中 contacts 的基本屬性。 |
| microsoft.aad.directory/contacts/create | 在 Azure Active Directory 中建立 contacts。 |
| microsoft.aad.directory/contacts/delete | 刪除 Azure Active Directory 中的 contacts。 |
| microsoft.aad.directory/groups/create | 在 Azure Active Directory 中建立 groups。 |
| microsoft.aad.directory/groups/createAsOwner | 在 Azure Active Directory 中建立 groups。 建立者會新增為第一個擁有者，而建立的物件會算在建立者的 250 個建立物件配額中。 |
| microsoft.aad.directory/groups/members/update | 更新 Azure Active Directory 中的 groups.members 屬性。 |
| microsoft.aad.directory/groups/owners/update | 更新 Azure Active Directory 中的 groups.owners 屬性。 |
| microsoft.aad.directory/users/appRoleAssignments/update | 更新 Azure Active Directory 中的 users.appRoleAssignments 屬性。 |
| microsoft.aad.directory/users/assignLicense | 管理 Azure Active Directory 中的使用者授權。 |
| microsoft.aad.directory/users/basic/update | 更新 Azure Active Directory 中 users 的基本屬性。 |
| microsoft.aad.directory/users/delete | 刪除 Azure Active Directory 中的 users。 |
| microsoft.aad.directory/users/invalidateAllRefreshTokens | 使 Azure Active Directory 中的所有使用者重新整理權杖失效。 |
| microsoft.aad.directory/users/manager/update | 更新 Azure Active Directory 中的 users.manager 屬性。 |
| microsoft.aad.directory/users/password/update | 在 Azure Active Directory 中更新所有使用者的密碼。 如需詳細資訊，請參閱線上文件。 |
| microsoft.aad.directory/users/restore | 還原 Azure Active Directory 中已刪除的使用者。 |
| microsoft.aad.directory/users/userPrincipalName/update | 更新 Azure Active Directory 中的 users.userPrincipalName 屬性。 |
| microsoft.azure.serviceHealth/allEntities/allTasks | 讀取及設定 Azure 服務健康情況。 |
| microsoft.azure.supportTickets/allEntities/allTasks | 建立和管理 Azure 支援票證。 |
| microsoft.office365.webPortal/allEntities/basic/read | 讀取 microsoft.office365.webPortal 中所有資源的基本屬性。 |
| microsoft.office365.serviceHealth/allEntities/allTasks | 讀取及設定 Office 365 服務健康情況。 |
| microsoft.office365.supportTickets/allEntities/allTasks | 建立和管理 Office 365 支援票證。 |

### <a name="partner-tier2-support"></a>合作夥伴第 2 層支援
請勿使用 - 不適用於一般用途。

  > [!NOTE]
  > 此角色具有 Azure Active Directory 以外的其他權限。 如需詳細資訊，請參閱前述角色說明。
  >
  >

| **動作** | **描述** |
| --- | --- |
| microsoft.aad.directory/contacts/basic/update | 更新 Azure Active Directory 中 contacts 的基本屬性。 |
| microsoft.aad.directory/contacts/create | 在 Azure Active Directory 中建立 contacts。 |
| microsoft.aad.directory/contacts/delete | 刪除 Azure Active Directory 中的 contacts。 |
| microsoft.aad.directory/domains/allTasks | 建立和刪除 domains，以及在 Azure Active Directory 中讀取和更新標準屬性。 |
| microsoft.aad.directory/groups/create | 在 Azure Active Directory 中建立 groups。 |
| microsoft.aad.directory/groups/delete | 刪除 Azure Active Directory 中的 groups。 |
| microsoft.aad.directory/groups/members/update | 更新 Azure Active Directory 中的 groups.members 屬性。 |
| microsoft.aad.directory/groups/restore | 還原 Azure Active Directory 中的 groups。 |
| microsoft.aad.directory/organization/basic/update | 更新 Azure Active Directory 中 organization 的基本屬性。 |
| microsoft.aad.directory/users/appRoleAssignments/update | 更新 Azure Active Directory 中的 users.appRoleAssignments 屬性。 |
| microsoft.aad.directory/users/assignLicense | 管理 Azure Active Directory 中的使用者授權。 |
| microsoft.aad.directory/users/basic/update | 更新 Azure Active Directory 中 users 的基本屬性。 |
| microsoft.aad.directory/users/delete | 刪除 Azure Active Directory 中的 users。 |
| microsoft.aad.directory/users/invalidateAllRefreshTokens | 使 Azure Active Directory 中的所有使用者重新整理權杖失效。 |
| microsoft.aad.directory/users/manager/update | 更新 Azure Active Directory 中的 users.manager 屬性。 |
| microsoft.aad.directory/users/password/update | 在 Azure Active Directory 中更新所有使用者的密碼。 如需詳細資訊，請參閱線上文件。 |
| microsoft.aad.directory/users/restore | 還原 Azure Active Directory 中已刪除的使用者。 |
| microsoft.aad.directory/users/userPrincipalName/update | 更新 Azure Active Directory 中的 users.userPrincipalName 屬性。 |
| microsoft.azure.serviceHealth/allEntities/allTasks | 讀取及設定 Azure 服務健康情況。 |
| microsoft.azure.supportTickets/allEntities/allTasks | 建立和管理 Azure 支援票證。 |
| microsoft.office365.webPortal/allEntities/basic/read | 讀取 microsoft.office365.webPortal 中所有資源的基本屬性。 |
| microsoft.office365.serviceHealth/allEntities/allTasks | 讀取及設定 Office 365 服務健康情況。 |
| microsoft.office365.supportTickets/allEntities/allTasks | 建立和管理 Office 365 支援票證。 |

### <a name="power-bi-service-administrator"></a>Power BI 服務管理員
可管理 Power BI 產品的所有層面。

  > [!NOTE]
  > 此角色具有 Azure Active Directory 以外的其他權限。 如需詳細資訊，請參閱前述角色說明。
  >
  >

| **動作** | **說明** |
| --- | --- |
| microsoft.azure.serviceHealth/allEntities/allTasks | 讀取及設定 Azure 服務健康情況。 |
| microsoft.azure.supportTickets/allEntities/allTasks | 建立和管理 Azure 支援票證。 |
| microsoft.powerApps.powerBI/allEntities/allTasks | 管理 Power BI 的所有層面。 |
| microsoft.office365.webPortal/allEntities/basic/read | 讀取 microsoft.office365.webPortal 中所有資源的基本屬性。 |
| microsoft.office365.serviceHealth/allEntities/allTasks | 讀取及設定 Office 365 服務健康情況。 |
| microsoft.office365.supportTickets/allEntities/allTasks | 建立和管理 Office 365 支援票證。 |

### <a name="privileged-authentication-administrator"></a>特殊許可權驗證管理員
允許針對任何使用者 (管理員或非系統管理員) 來查看、設定及重設驗證方法資訊。

| **動作** | **描述** |
| --- | --- |
| microsoft.aad.directory/users/invalidateAllRefreshTokens | 使 Azure Active Directory 中的所有使用者重新整理權杖失效。 |
| microsoft.aad.directory/users/strongAuthentication/update | 更新 MFA 認證資訊等增強式驗證屬性。 |
| microsoft.azure.serviceHealth/allEntities/allTasks | 讀取及設定 Azure 服務健康情況。 |
| microsoft.azure.supportTickets/allEntities/allTasks | 建立和管理 Azure 支援票證。 |
| microsoft.office365.webPortal/allEntities/basic/read | 讀取 microsoft.office365.webPortal 中所有資源的基本屬性。 |
| microsoft.office365.serviceHealth/allEntities/allTasks | 讀取及設定 Office 365 服務健康情況。 |
| microsoft.office365.supportTickets/allEntities/allTasks | 建立和管理 Office 365 支援票證。 |
| microsoft.aad.directory/users/password/update | 更新 Office 365 組織中所有使用者的密碼。 如需詳細資訊，請參閱線上文件。 |
### <a name="privileged-role-administrator"></a>特殊權限角色管理員
能夠管理 Azure AD 中的角色指派，以及 Privileged Identity Management 的所有層面。

  > [!NOTE]
  > 此角色具有 Azure Active Directory 以外的其他權限。 如需詳細資訊，請參閱前述角色說明。
  >
  >

| **動作** | **說明** |
| --- | --- |
| microsoft.aad.privilegedIdentityManagement/allEntities/allTasks | 建立和刪除所有資源，以及讀取和更新 microsoft.aad.privilegedIdentityManagement 中的標準屬性。 |
| microsoft.aad.directory/servicePrincipals/appRoleAssignedTo/allTasks | 讀取和設定 Azure Active Directory 中的 Serviceprincipals.approleassignedto 屬性。 |
| microsoft.aad.directory/servicePrincipals/oAuth2PermissionGrants/allTasks | 讀取和設定 Azure Active Directory 中的 oAuth2PermissionGrants 屬性。 |
| microsoft.aad.directory/administrativeUnits/allProperties/allTasks | 建立和管理管理單位 (包括成員) |
| microsoft.aad.directory/roleAssignments/allProperties/allTasks | 建立和管理角色指派。 |
| microsoft.aad.directory/roleDefinitions/allProperties/allTasks | 建立和管理角色定義。 |

### <a name="reports-reader"></a>報告讀者
可讀取登入與稽核報告。

  > [!NOTE]
  > 此角色具有 Azure Active Directory 以外的其他權限。 如需詳細資訊，請參閱前述角色說明。
  >
  >

| **動作** | **說明** |
| --- | --- |
| microsoft.aad.directory/auditLogs/allProperties/read | 讀取 Azure Active Directory 中的 auditLogs 所包含的所有屬性 (包括特殊權限的屬性)。 |
| microsoft.aad.directory/signInReports/allProperties/read | 讀取 Azure Active Directory 中的 signInReports 所包含的所有屬性 (包括特殊權限的屬性)。 |
| microsoft.azure.serviceHealth/allEntities/allTasks | 讀取及設定 Azure 服務健康情況。 |
| microsoft.office365.usageReports/allEntities/read | 讀取 Office 365 使用量報告。 |

### <a name="search-administrator"></a>搜尋系統管理員
可以建立及管理 Microsoft 搜尋設定的所有層面。

  > [!NOTE]
  > 此角色具有 Azure Active Directory 以外的其他權限。 如需詳細資訊，請參閱前述角色說明。
  >
  >

| **動作** | **描述** |
| --- | --- |
| microsoft.office365.messageCenter/messages/read | 讀取 microsoft.office365.messageCenter 中的訊息。 |
| microsoft.office365.search/allEntities/allProperties/allTasks | 建立和刪除所有資源, 以及讀取和更新 office365 中的所有屬性。 |
| microsoft.office365.serviceHealth/allEntities/allTasks | 讀取及設定 Office 365 服務健康情況。 |
| microsoft.office365.supportTickets/allEntities/allTasks | 建立和管理 Office 365 支援票證。 |
| microsoft.office365.usageReports/allEntities/read | 讀取 Office 365 使用量報告。 |
| microsoft.office365.webPortal/allEntities/basic/read | 讀取 microsoft.office365.webPortal 中所有資源的基本屬性。 |

### <a name="search-editor"></a>搜尋編輯器
可以建立及管理編輯內容, 例如書簽、Q 和 As、位置、floorplan。

  > [!NOTE]
  > 此角色具有 Azure Active Directory 以外的其他權限。 如需詳細資訊，請參閱前述角色說明。
  >
  >

| **動作** | **說明** |
| --- | --- |
| microsoft.office365.messageCenter/messages/read | 讀取 microsoft.office365.messageCenter 中的訊息。 |
| microsoft.office365.search/content/allProperties/allTasks | 建立和刪除內容, 以及讀取和更新 office365 中的所有屬性。 |
| microsoft.office365.usageReports/allEntities/read | 讀取 Office 365 使用量報告。 |

### <a name="security-administrator"></a>安全性系統管理員
能夠讀取安全性資訊與報表，以及管理 Azure AD 與 Office 365 中的設定。

  > [!NOTE]
  > 此角色具有 Azure Active Directory 以外的其他權限。 如需詳細資訊，請參閱前述角色說明。
  >
  >

| **動作** | **描述** |
| --- | --- |
| microsoft.aad.directory/applications/policies/update | 更新 Azure Active Directory 中的 applications.policies 屬性。 |
| microsoft.aad.directory/auditLogs/allProperties/read | 讀取 Azure Active Directory 中的 auditLogs 所包含的所有屬性 (包括特殊權限的屬性)。 |
| microsoft.aad.directory/devices/bitLockerRecoveryKeys/read | 讀取 Azure Active Directory 中的 devices.bitLockerRecoveryKeys 屬性。 |
| microsoft.aad.directory/policies/basic/update | 更新 Azure Active Directory 中 policies 的基本屬性。 |
| microsoft.aad.directory/policies/create | 在 Azure Active Directory 中建立原則。 |
| microsoft.aad.directory/policies/delete | 刪除 Azure Active Directory 中的原則。 |
| microsoft.aad.directory/policies/owners/update | 更新 Azure Active Directory 中的 policies.owners 屬性。 |
| microsoft.aad.directory/policies/tenantDefault/update | 更新 Azure Active Directory 中的 policies.tenantDefault 屬性。 |
| microsoft.aad.directory/servicePrincipals/policies/update | 更新 Azure Active Directory 中的 servicePrincipals.policies 屬性。 |
| microsoft.aad.directory/signInReports/allProperties/read | 讀取 Azure Active Directory 中的 signInReports 所包含的所有屬性 (包括特殊權限的屬性)。 |
| microsoft.aad.identityProtection/allEntities/read | 讀取 microsoft.aad.identityProtection 中的所有資源。 |
| microsoft.aad.identityProtection/allEntities/update | 更新 microsoft.aad.identityProtection 中的所有資源。 |
| microsoft.aad.privilegedIdentityManagement/allEntities/read | 讀取 microsoft.aad.privilegedIdentityManagement 中的所有資源。 |
| microsoft.azure.serviceHealth/allEntities/allTasks | 讀取及設定 Azure 服務健康情況。 |
| microsoft.office365.webPortal/allEntities/basic/read | 讀取 microsoft.office365.webPortal 中所有資源的基本屬性。 |
| microsoft.office365.protectionCenter/allEntities/read | 讀取 Office 365 防護中心的所有層面。 |
| microsoft.office365.protectionCenter/allEntities/update | 更新 microsoft.office365.protectionCenter 中的所有資源。 |
| microsoft.office365.serviceHealth/allEntities/allTasks | 讀取及設定 Office 365 服務健康情況。 |

### <a name="security-operator"></a>安全性運算子
建立和管理安全性事件。

  > [!NOTE]
  > 此角色具有 Azure Active Directory 以外的其他權限。 如需詳細資訊，請參閱前述角色說明。
  >
  >

| **動作** | **說明** |
| --- | --- |
| microsoft.aad.cloudAppSecurity/allEntities/allTasks | 讀取和設定 Microsoft Cloud App Security。 |
| microsoft.aad.identityProtection/allEntities/read | 讀取 microsoft.aad.identityProtection 中的所有資源。 |
| microsoft.aad.privilegedIdentityManagement/allEntities/read | 讀取 microsoft.aad.privilegedIdentityManagement 中的所有資源。 |
| microsoft.azure.advancedThreatProtection/allEntities/read | 閱讀並設定 Azure AD Advanced 威脅防護。 |
| microsoft.intune/allEntities/allTasks | 管理 Intune 的所有層面。 |
| microsoft.office365.securityComplianceCenter/allEntities/allTasks | 閱讀並設定安全性 & 合規性中心。 |
| microsoft.office365.usageReports/allEntities/read | 讀取 Office 365 使用量報告。 |
| microsoft.windows.defenderAdvancedThreatProtection/allEntities/read | 讀取和設定 Windows Defender Advanced 威脅防護。 |

### <a name="security-reader"></a>安全性讀取者
可讀取安全性資訊及 Azure AD 與 Office 365 中的報告。

  > [!NOTE]
  > 此角色具有 Azure Active Directory 以外的其他權限。 如需詳細資訊，請參閱前述角色說明。
  >
  >

| **動作** | **描述** |
| --- | --- |
| microsoft.aad.directory/auditLogs/allProperties/read | 讀取 Azure Active Directory 中的 auditLogs 所包含的所有屬性 (包括特殊權限的屬性)。 |
| microsoft.aad.directory/devices/bitLockerRecoveryKeys/read | 讀取 Azure Active Directory 中的 devices.bitLockerRecoveryKeys 屬性。 |
| microsoft.aad.directory/signInReports/allProperties/read | 讀取 Azure Active Directory 中的 signInReports 所包含的所有屬性 (包括特殊權限的屬性)。 |
| microsoft.aad.identityProtection/allEntities/read | 讀取 microsoft.aad.identityProtection 中的所有資源。 |
| microsoft.aad.privilegedIdentityManagement/allEntities/read | 讀取 microsoft.aad.privilegedIdentityManagement 中的所有資源。 |
| microsoft.azure.serviceHealth/allEntities/allTasks | 讀取及設定 Azure 服務健康情況。 |
| microsoft.office365.webPortal/allEntities/basic/read | 讀取 microsoft.office365.webPortal 中所有資源的基本屬性。 |
| microsoft.office365.protectionCenter/allEntities/read | 讀取 Office 365 防護中心的所有層面。 |
| microsoft.office365.serviceHealth/allEntities/allTasks | 讀取及設定 Office 365 服務健康情況。 |

### <a name="service-support-administrator"></a>服務支援管理員
可讀取服務健康情況資訊及管理支援票證。

  > [!NOTE]
  > 此角色具有 Azure Active Directory 以外的其他權限。 如需詳細資訊，請參閱前述角色說明。
  >
  >

| **動作** | **說明** |
| --- | --- |
| microsoft.azure.serviceHealth/allEntities/allTasks | 讀取及設定 Azure 服務健康情況。 |
| microsoft.azure.supportTickets/allEntities/allTasks | 建立和管理 Azure 支援票證。 |
| microsoft.office365.webPortal/allEntities/basic/read | 讀取 microsoft.office365.webPortal 中所有資源的基本屬性。 |
| microsoft.office365.serviceHealth/allEntities/allTasks | 讀取及設定 Office 365 服務健康情況。 |
| microsoft.office365.supportTickets/allEntities/allTasks | 建立和管理 Office 365 支援票證。 |

### <a name="sharepoint-service-administrator"></a>SharePoint 服務管理員
可管理 SharePoint 服務的所有層面。

  > [!NOTE]
  > 此角色具有 Azure Active Directory 以外的其他權限。 如需詳細資訊，請參閱前述角色說明。
  >
  >

| **動作** | **描述** |
| --- | --- |
| microsoft.aad.directory/groups/unified/appRoleAssignments/update | 更新 Azure Active Directory 中的 groups.unified 屬性。 |
| microsoft.aad.directory/groups/unified/basic/update | 更新 Office 365 群組的基本屬性。 |
| microsoft.aad.directory/groups/unified/create | 建立 Office 365 群組。 |
| microsoft.aad.directory/groups/unified/delete | 刪除 Office 365 群組。 |
| microsoft.aad.directory/groups/unified/members/update | 更新 Office 365 群組的成員資格。 |
| microsoft.aad.directory/groups/unified/owners/update | 更新 Office 365 群組的擁有權。 |
| microsoft.azure.serviceHealth/allEntities/allTasks | 讀取及設定 Azure 服務健康情況。 |
| microsoft.azure.supportTickets/allEntities/allTasks | 建立和管理 Azure 支援票證。 |
| microsoft.office365.webPortal/allEntities/basic/read | 讀取 microsoft.office365.webPortal 中所有資源的基本屬性。 |
| microsoft.office365.serviceHealth/allEntities/allTasks | 讀取及設定 Office 365 服務健康情況。 |
| microsoft.office365.sharepoint/allEntities/allTasks | 建立和刪除所有資源，以及讀取和更新 microsoft.office365.sharepoint 中的標準屬性。 |
| microsoft.office365.supportTickets/allEntities/allTasks | 建立和管理 Office 365 支援票證。 |

### <a name="teams-communications-administrator"></a>Microsoft Teams 通訊系統管理員
能夠管理 Microsoft Teams 服務內的呼叫和會議功能。

  > [!NOTE]
  > 此角色具有 Azure Active Directory 以外的其他權限。 如需詳細資訊，請參閱前述角色說明。
  >
  >

| **動作** | **說明** |
| --- | --- |
| microsoft.azure.serviceHealth/allEntities/allTasks | 讀取及設定 Azure 服務健康情況。 |
| microsoft.azure.supportTickets/allEntities/allTasks | 建立和管理 Azure 支援票證。 |
| microsoft.office365.webPortal/allEntities/basic/read | 讀取 microsoft.office365.webPortal 中所有資源的基本屬性。 |
| microsoft.office365.serviceHealth/allEntities/allTasks | 讀取及設定 Office 365 服務健康情況。 |
| microsoft.office365.supportTickets/allEntities/allTasks | 建立和管理 Office 365 支援票證。 |
| microsoft.office365.usageReports/allEntities/read | 讀取 Office 365 使用量報告。 |

### <a name="teams-communications-support-engineer"></a>Microsoft Teams 通訊支援工程師
能夠使用進階工具針對 Microsoft Teams 內的通訊問題進行疑難排解。

  > [!NOTE]
  > 此角色具有 Azure Active Directory 以外的其他權限。 如需詳細資訊，請參閱前述角色說明。
  >
  >

| **動作** | **描述** |
| --- | --- |
| microsoft.azure.serviceHealth/allEntities/allTasks | 讀取及設定 Azure 服務健康情況。 |
| microsoft.office365.webPortal/allEntities/basic/read | 讀取 microsoft.office365.webPortal 中所有資源的基本屬性。 |
| microsoft.office365.serviceHealth/allEntities/allTasks | 讀取及設定 Office 365 服務健康情況。 |

### <a name="teams-communications-support-specialist"></a>Microsoft Teams 通訊支援專家
能夠使用基本工具針對 Microsoft Teams 內的通訊問題進行疑難排解。

  > [!NOTE]
  > 此角色具有 Azure Active Directory 以外的其他權限。 如需詳細資訊，請參閱前述角色說明。
  >
  >

| **動作** | **描述** |
| --- | --- |
| microsoft.azure.serviceHealth/allEntities/allTasks | 讀取及設定 Azure 服務健康情況。 |
| microsoft.office365.webPortal/allEntities/basic/read | 讀取 microsoft.office365.webPortal 中所有資源的基本屬性。 |
| microsoft.office365.serviceHealth/allEntities/allTasks | 讀取及設定 Office 365 服務健康情況。 |

### <a name="teams-service-administrator"></a>Microsoft Teams 服務管理員
能夠管理 Microsoft Teams 服務。

  > [!NOTE]
  > 此角色具有 Azure Active Directory 以外的其他權限。 如需詳細資訊，請參閱前述角色說明。
  >
  >

| **動作** | **描述** |
| --- | --- |
| microsoft.aad.directory/groups/hiddenMembers/read | 讀取 Azure Active Directory 中的 groups.hiddenMembers 屬性。 |
| microsoft.aad.directory/groups/unified/appRoleAssignments/update | 更新 Azure Active Directory 中的 groups.unified 屬性。 |
| microsoft.aad.directory/groups/unified/basic/update | 更新 Office 365 群組的基本屬性。 |
| microsoft.aad.directory/groups/unified/create | 建立 Office 365 群組。 |
| microsoft.aad.directory/groups/unified/delete | 刪除 Office 365 群組。 |
| microsoft.aad.directory/groups/unified/members/update | 更新 Office 365 群組的成員資格。 |
| microsoft.aad.directory/groups/unified/owners/update | 更新 Office 365 群組的擁有權。 |
| microsoft.azure.serviceHealth/allEntities/allTasks | 讀取及設定 Azure 服務健康情況。 |
| microsoft.azure.supportTickets/allEntities/allTasks | 建立和管理 Azure 支援票證。 |
| microsoft.office365.webPortal/allEntities/basic/read | 讀取 microsoft.office365.webPortal 中所有資源的基本屬性。 |
| microsoft.office365.serviceHealth/allEntities/allTasks | 讀取及設定 Office 365 服務健康情況。 |
| microsoft.office365.supportTickets/allEntities/allTasks | 建立和管理 Office 365 支援票證。 |
| microsoft.office365.usageReports/allEntities/read | 讀取 Office 365 使用量報告。 |

### <a name="user-administrator"></a>使用者管理員
能夠管理使用者與群組的所有層面，包含為受限制的管理員重設密碼。

| **動作** | **描述** |
| --- | --- |
| microsoft.aad.directory/appRoleAssignments/create | 在 Azure Active Directory 中建立 appRoleAssignments。 |
| microsoft.aad.directory/appRoleAssignments/delete | 刪除 Azure Active Directory 中的 appRoleAssignments。 |
| microsoft.aad.directory/appRoleAssignments/update | 更新在 Azure Active Directory 中的 appRoleAssignments。 |
| microsoft.aad.directory/contacts/basic/update | 更新 Azure Active Directory 中 contacts 的基本屬性。 |
| microsoft.aad.directory/contacts/create | 在 Azure Active Directory 中建立 contacts。 |
| microsoft.aad.directory/contacts/delete | 刪除 Azure Active Directory 中的 contacts。 |
| microsoft.aad.directory/groups/appRoleAssignments/update | 更新 Azure Active Directory 中的 groups.appRoleAssignments 屬性。 |
| microsoft.aad.directory/groups/basic/update | 更新 Azure Active Directory 中 groups 的基本屬性。 |
| microsoft.aad.directory/groups/create | 在 Azure Active Directory 中建立 groups。 |
| microsoft.aad.directory/groups/createAsOwner | 在 Azure Active Directory 中建立 groups。 建立者會新增為第一個擁有者，而建立的物件會算在建立者的 250 個建立物件配額中。 |
| microsoft.aad.directory/groups/delete | 刪除 Azure Active Directory 中的 groups。 |
| microsoft.aad.directory/groups/hiddenMembers/read | 讀取 Azure Active Directory 中的 groups.hiddenMembers 屬性。 |
| microsoft.aad.directory/groups/members/update | 更新 Azure Active Directory 中的 groups.members 屬性。 |
| microsoft.aad.directory/groups/owners/update | 更新 Azure Active Directory 中的 groups.owners 屬性。 |
| microsoft.aad.directory/groups/restore | 還原 Azure Active Directory 中的 groups。 |
| microsoft.aad.directory/groups/settings/update | 更新 Azure Active Directory 中的 groups.settings 屬性。 |
| microsoft.aad.directory/users/appRoleAssignments/update | 更新 Azure Active Directory 中的 users.appRoleAssignments 屬性。 |
| microsoft.aad.directory/users/assignLicense | 管理 Azure Active Directory 中的使用者授權。 |
| microsoft.aad.directory/users/basic/update | 更新 Azure Active Directory 中 users 的基本屬性。 |
| microsoft.aad.directory/users/create | 在 Azure Active Directory 中建立 users。 |
| microsoft.aad.directory/users/delete | 刪除 Azure Active Directory 中的 users。 |
| microsoft.aad.directory/users/invalidateAllRefreshTokens | 使 Azure Active Directory 中的所有使用者重新整理權杖失效。 |
| microsoft.aad.directory/users/manager/update | 更新 Azure Active Directory 中的 users.manager 屬性。 |
| microsoft.aad.directory/users/password/update | 在 Azure Active Directory 中更新所有使用者的密碼。 如需詳細資訊，請參閱線上文件。 |
| microsoft.aad.directory/users/restore | 還原 Azure Active Directory 中已刪除的使用者。 |
| microsoft.aad.directory/users/userPrincipalName/update | 更新 Azure Active Directory 中的 users.userPrincipalName 屬性。 |
| microsoft.azure.serviceHealth/allEntities/allTasks | 讀取及設定 Azure 服務健康情況。 |
| microsoft.azure.supportTickets/allEntities/allTasks | 建立和管理 Azure 支援票證。 |
| microsoft.office365.webPortal/allEntities/basic/read | 讀取 microsoft.office365.webPortal 中所有資源的基本屬性。 |
| microsoft.office365.serviceHealth/allEntities/allTasks | 讀取及設定 Office 365 服務健康情況。 |
| microsoft.office365.supportTickets/allEntities/allTasks | 建立和管理 Office 365 支援票證。 |

## <a name="role-template-ids"></a>角色範本識別碼

角色範本識別碼主要是由圖形 API 或 PowerShell 使用者所使用。

圖表 displayName | Azure 入口網站顯示名稱 | directoryRoleTemplateId
----------------- | ------------------------- | -------------------------
應用程式系統管理員 | 應用程式管理員 | 9B895D92-2CD3-44C7-9D02-A6AC2D5EA5C3
應用程式開發人員 | 應用程式開發人員 | CF1C38E5-3621-4004-A7CB-879624DCED7C
驗證系統管理員 | 驗證管理員 | c4e39bd9-1100-46d3-8c65-fb160da0071f
Azure 資訊保護系統管理員 | Azure 資訊保護系統管理員 | 7495fdc4-34c4-4d15-a289-98788ce399fd
B2C 使用者流程管理員 | B2C 使用者流程管理員 | 6e591065-9bad-43ed-90f3-e9424366d2f0
B2C 使用者流程屬性管理員 | B2C 使用者流程屬性管理員 | 0f971eea-41eb-4569-a71e-57bb8a3eff1e
B2C IEF 索引鍵集管理員 | B2C IEF 索引鍵集管理員 | aaf43236-0c0d-4d5f-883a-6955382ac081
B2C IEF 原則管理員 | B2C IEF 原則管理員 | 3edaf663-341e-4475-9f94-5c398ef6c070
計費管理員 | 計費管理員 | b0f54661-2d74-4c50-afa3-1ec803f12efe
雲端應用程式系統管理員 | 雲端應用程式系統管理員 | 158c047a-c907-4556-b7ef-446551a6b5f7
雲端裝置管理員 | 雲端裝置管理員 | 7698a772-787b-4ac8-901f-60d6b08affd2
公司系統管理員 | 全域管理員 | 62e90394-69f5-4237-9190-012177145e10
規範管理員 | 規範管理員 | 17315797-102d-40b4-93e0-432062caca18
合規性資料管理員 | 合規性資料管理員 | e6d1a23a-da11-4be4-9570-befc86d067a7
條件式存取系統管理員 | 條件式存取系統管理員 | b1be1c3e-b65d-4f19-8427-f6fa0d97feb9
CRM 服務管理員 | Dynamics 365 管理員 | 44367163-eba1-44c3-98af-f5787879f96a
客戶 LockBox 存取核准者 | 客戶加密箱存取核准者 | 5c4f9dcd-47dc-4cf7-8c9a-9e4207cbfc91
電腦分析系統管理員 | 電腦分析系統管理員 | 38a96431-2bdf-4b4c-8b6e-5d3d8abac1a4
裝置系統管理員 | 裝置系統管理員 | 9f06204d-73c1-4d4c-880a-6edb90606fd8
加入裝置 | 裝置加入 | 9c094953-4995-41c8-84c8-3ebb9b32c93f
裝置管理員 | 裝置管理員 | 2b499bcd-da44-4968-8aec-78e1674fa64d
裝置使用者 | 裝置使用者 | d405c6df-0af8-4e3b-95e4-4d06e542189e
目錄讀取器 | 目錄讀取器 | 88d8e3e3-8f55-4a1e-953a-9b9898b8876b
目錄同步處理帳戶 | 目錄同步處理帳戶 | d29b2b05-8046-44ba-8758-1e26182fcf32
目錄撰寫者 | 目錄寫入器 | 9360feb5-f418-4baa-8175-e2a00bac4301
Exchange 服務管理員 | Exchange 系統管理員 | 29232cdf-9323-42fd-ade2-1d097af3e4de
外部識別提供者系統管理員 | 外部識別提供者系統管理員 | be2f45a1-457d-42af-a067-6ec1fa63bc45
來賓邀請者 | 來賓邀請者 | 95e79109-95c0-4d8e-aee3-d01accf2d47b
服務台系統管理員 | 密碼管理員 | 729827e3-9c14-49f7-bb1b-9608f156bbb8
Intune 服務管理員 | Intune 管理員 | 3a2c62db-5318-420d-8d74-23affee5d9d5
Kaizala 系統管理員 | Kaizala 系統管理員 | 74ef975b-6605-40af-a5d2-b9539d836353
授權管理員 | 授權管理員 | 4d6ac14f-3453-41d0-bef9-a3e0c569773a
Lync 服務管理員 | 商務用 Skype 的管理員 | 75941009-915a-4869-abe7-691bff18279e
訊息中心隱私權讀者 | 訊息中心隱私權讀者 | ac16e43d-7b2d-40e0-ac05-243ff356ab5b
訊息中心讀取者 | 訊息中心讀者 | 790c1fb9-7f7d-4f88-86a1-ef1f95c05c1b
合作夥伴第 1 層支援 | 合作夥伴第 1 層支援 | 4ba39ca4-527c-499a-b93d-d9b492c50246
合作夥伴第 2 層支援 | 合作夥伴第 2 層支援 | e00e864a-17c5-4a4b-9c06-f5b95a8d5bd8
Power BI 服務管理員 | Power BI 系統管理員 | a9ea8996-122f-4c74-9520-8edcd192826c
特殊許可權驗證管理員 | 特殊許可權驗證管理員 | 7be44c8a-adaf-4e2a-84d6-ab2649e08a13
特殊權限角色管理員 | 特殊權限角色管理員 | e8611ab8-c189-46e8-94e1-60213ab1f814
報告讀者 | 報表讀者 | 4a5d8f65-41da-4de4-8968-e035b65339cf
搜尋系統管理員 | 搜尋系統管理員 | 0964bb5e-9bdb-4d7b-ac29-58e794862a40
搜尋編輯器 | 搜尋編輯器 | 8835291a-918c-4fd7-a9ce-faa49f0cf7d9
安全性系統管理員 | 安全性系統管理員 | 194ae4cb-b126-40b2-bd5b-6091b380977d
安全性運算子 | 安全性運算子 | 5f2222b1-57c3-48ba-8ad5-d4759f1fde6f
安全性讀取者 | 安全性讀取者 | 5d6b6bb7-de71-4623-b4af-96380a352509
服務支援管理員 | 服務管理員 | f023fd81-a637-4b56-95fd-791ac0226033
SharePoint 服務管理員 | Sharepoint 系統管理員 | f28a1f50-f6e7-4571-818b-6a12f2af6b6c
Microsoft Teams 通訊系統管理員 | Microsoft Teams 通訊系統管理員 | baf37b3a-610e-45da-9e62-d9d1e5e8914b
Microsoft Teams 通訊支援工程師 | Microsoft Teams 通訊支援工程師 | f70938a0-fc10-4177-9e90-2178f8765737
Microsoft Teams 通訊支援專家 | Microsoft Teams 通訊支援專家 | fcf91098-03e3-41a9-b5ba-6f0ec8188a12
Microsoft Teams 服務管理員 | Microsoft Teams 服務管理員 | 69091246-20e8-4a56-aa4d-066075b2a7a8
使用者 | 使用者 | a0b1b346-4d3e-4e8b-98f8-753987be4970
使用者帳戶管理員 | 使用者管理員 | fe930be7-5e62-47db-91af-98c3a49a38b1
加入工作場所裝置 | 工作場所裝置加入 | c34f683f-4d5a-4403-affd-6615e00e3a7f


## <a name="deprecated-roles"></a>已被取代的角色

以下是不應使用的角色。 它們已被取代，而且未來將從 Azure AD 中移除。

* AdHoc 授權管理員
* 加入裝置
* 裝置管理員
* 裝置使用者
* 傳送電子郵件給經過驗證的使用者建立者
* 信箱管理員
* 加入工作場所裝置

## <a name="next-steps"></a>後續步驟

* 若要深入了解如何將使用者指派為 Azure 訂用帳戶的系統管理員，請參閱[使用 RBAC 和 Azure 入口網站管理存取權](../../role-based-access-control/role-assignments-portal.md)
* 若要深入了解如何在 Microsoft Azure 中控制資源存取，請參閱 [了解 Azure 中的資源存取](../../role-based-access-control/rbac-and-directory-admin-roles.md)
* 如需有關 Azure Active Directory 與您的 Azure 訂用帳戶產生關聯之方式的詳細資訊，請參閱 [Azure 訂用帳戶如何與 Azure Active Directory 產生關聯](../fundamentals/active-directory-how-subscriptions-associated-directory.md)
