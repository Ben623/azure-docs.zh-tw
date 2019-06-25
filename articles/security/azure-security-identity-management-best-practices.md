---
title: Azure 身分識別和存取安全性的最佳做法 | Microsoft Docs
description: 本文提供使用內建 Azure 功能的一些身分識別管理和存取控制最佳作法。
services: security
documentationcenter: na
author: barclayn
manager: barbkess
editor: TomSh
ms.assetid: 07d8e8a8-47e8-447c-9c06-3a88d2713bc1
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/03/2019
ms.author: barclayn
ms.openlocfilehash: 2b57ec7727e8f5b648bcb97e5fae26c63724411c
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "67127200"
---
# <a name="azure-identity-management-and-access-control-security-best-practices"></a>Azure 身分識別管理和存取控制安全性最佳作法
本文會討論一系列的 Azure 身分識別管理和存取控制安全性最佳做法。 這些最佳作法衍生自我們的 [Azure AD](../active-directory/fundamentals/active-directory-whatis.md) 經驗和客戶的經驗。

針對每個最佳做法，我們會說明︰

* 最佳作法是什麼
* 您為何想要啟用該最佳作法
* 如果無法啟用最佳作法，結果可能為何
* 最佳作法的可能替代方案
* 如何學習啟用最佳作法

這篇「Azure 身分識別管理和存取控制安全性最佳作法」是以共識意見及 Azure 平台功能和特性集 (因為在撰寫本文時已存在) 為基礎。 意見和技術會隨著時間改變，這篇文章將會定期進行更新以反映這些變更。

本文討論的 Azure 身分識別管理和存取控制安全性最佳作法包括：

* 將身分識別視為主要安全性周邊
* 集中管理身分識別
* 管理已連線的租用戶
* 啟用單一登入
* 開啟 條件式存取
* 啟用密碼管理
* 對使用者強制執行多重要素驗證
* 使用角色型存取控制
* 降低特殊權限帳戶的暴露風險
* 控制資源所在的位置
* 使用 Azure AD 進行驗證儲存體

## <a name="treat-identity-as-the-primary-security-perimeter"></a>將身分識別視為主要安全性周邊

許多人認為身分識別是安全性的主要周邊。 這種看法擺脫了傳統以網路安全性為主的觀點。 網路周邊持續變得更容易滲透，而且周邊防禦已不如 [BYOD](https://aka.ms/byodcg) 裝置和雲端應用程式遽增之前那麼有效。

[Azure Active Directory (Azure AD)](../active-directory/active-directory-whatis.md) 是用於管理身分識別和存取的 Azure 解決方案。 Azure AD 是 Microsoft 提供的多租用戶雲端式目錄和身分識別管理服務。 它將核心目錄服務、應用程式存取管理及身分識別保護結合到單個解決方案。

下列各節列出使用 Azure AD 的身分識別和存取安全性最佳做法。

## <a name="centralize-identity-management"></a>集中管理身分識別

在[混合式身分識別](https://resources.office.com/ww-landing-M365E-EMS-IDAM-Hybrid-Identity-WhitePaper.html?)案例中，建議您整合內部部署與雲端目錄。 整合可讓您的 IT 團隊管理帳戶從一個位置，不論會建立一個帳戶。 整合也可協助您的使用者更具生產力提供常見的身分識別來存取雲端及內部部署資源。

**最佳做法**：建立單一 Azure AD 執行個體。 一致性和單一的權威來源會增加清楚起見，並從人為錯誤和組態複雜度降低安全性風險。
**詳細資料**：指定單一的 Azure AD 目錄視為公司和組織帳戶的授權來源。

**最佳做法**：整合您的內部部署目錄與 Azure AD。  
**詳細資料**：使用 [Azure AD Connect](../active-directory/connect/active-directory-aadconnect.md) 同步處理內部部署目錄與雲端目錄。

> [!Note]
> 有[因素會影響效能的 Azure AD Connect](../active-directory/hybrid/plan-connect-performance-factors.md)。 請確定 Azure AD Connect 有足夠的容量來保留表現不佳的系統妨礙安全性與生產力。 大型或複雜的組織 （組織佈建物件超過 100,000 個） 應該遵循[建議](../active-directory/hybrid/whatis-hybrid-identity.md)最佳化其 Azure AD Connect 實作。

**最佳做法**：不要同步處理至 Azure AD 中現有的 Active Directory 執行個體擁有較高權限的帳戶。
**詳細資料**：不要變更預設值[Azure AD Connect 設定](../active-directory/hybrid/how-to-connect-sync-configure-filtering.md)，篩選掉這些帳戶。 此設定可降低進行樞紐分析從雲端至內部部署資產 （這可能造成重大的事件） 的敵人的風險。

**最佳做法**：開啟密碼雜湊同步處理。  
**詳細資料**：密碼雜湊同步處理是一項功能可用來同步處理使用者密碼雜湊從內部部署 Active Directory 執行個體至雲端的 Azure AD 執行個體。 此同步處理有助於防止外洩的認證，在重新執行先前的攻擊。

即使您選擇使用與 Active Directory 同盟服務 (AD FS) 或其他身分識別提供者的同盟，仍可選擇性地設定密碼雜湊同步處理作為備用方式，以防內部部署伺服器失敗或暫時無法使用。 此同步處理可讓使用者使用他們用來登入其內部部署 Active Directory 執行個體的相同密碼登入服務。 您也可以藉由比較已同步處理的密碼雜湊與已知遭到入侵，如果使用者已在其他未連接到 Azure AD 的服務上使用相同的電子郵件地址和密碼的密碼來偵測認證遭入侵的身分識別保護。

如需詳細資訊，請參閱[使用 Azure AD Connect 同步實作密碼雜湊同步處理](../active-directory/connect/active-directory-aadconnectsync-implement-password-hash-synchronization.md)。

**最佳做法**：新的應用程式開發，使用 Azure AD 進行驗證。
**詳細資料**：您可以使用正確的功能來支援驗證：

  - Azure AD 的員工
  - [Azure AD B2B](https://docs.microsoft.com/azure/active-directory/b2b/)來賓使用者與外部合作夥伴
  - [Azure AD B2C](https://docs.microsoft.com/azure/active-directory-b2c/)來控制如何客戶註冊時，登入，然後使用您的應用程式時，管理其設定檔

未整合內部部署身分識別與雲端身分識別的組織，可能會有更多管理帳戶的額外負荷。 此額外負荷提高錯誤和安全性缺口的可能性。

> [!Note]
> 您必須選擇哪些重大帳戶位於和處理使用的系統管理工作站是否由新的雲端服務或現有的目錄。 使用現有的管理和佈建程序的身分識別可以減少一些風險，但也可以建立攻擊者危害內部部署帳戶和到雲端進行樞紐分析的風險。 您可能想要針對不同的角色 （例如，IT 系統管理員與業務單位的系統管理員） 使用不同的策略。 您有兩個選擇。 第一個選項是建立未與您的內部部署 Active Directory 執行個體同步的 Azure AD 帳戶。 加入 Azure AD 中，您可以管理和修補程式以使用 Microsoft Intune 系統管理工作站。 第二個選項是使用現有的系統管理員帳戶同步處理至您的內部部署 Active Directory 執行個體。 使用 Active Directory 網域中的現有的工作站，管理和安全性。

## <a name="manage-connected-tenants"></a>管理已連線的租用戶
組織的安全性必須評估風險，並且判斷是否已遵循任何法規需求，您的組織，以及原則的可見性。 您應該確定組織的安全性已連線到您的生產環境和網路的所有訂用帳戶的可見性 (透過[Azure ExpressRoute](../expressroute/expressroute-introduction.md)或是[站台對站 VPN](../vpn-gateway/vpn-gateway-howto-multi-site-to-site-resource-manager-portal.md))。 A[全域管理員/公司系統管理員](../active-directory/users-groups-roles/directory-assign-admin-roles.md#company-administrator)在 Azure AD 中可以提升其存取權[使用者存取系統管理員](../role-based-access-control/built-in-roles.md#user-access-administrator)角色，並查看所有訂用帳戶和受管理的群組連線到您的環境。

請參閱[提高管理所有 Azure 訂用帳戶和管理群組的存取權限](../role-based-access-control/elevate-access-global-admin.md)若要確保您和您的安全性群組，可以檢視所有訂用帳戶或管理群組連線到您的環境。 在評估風險之後，您應該移除此提高權限的存取權。

## <a name="enable-single-sign-on"></a>啟用單一登入

在行動第一、雲端第一的世界中，無論是從什麼地方，都要讓使用者能單一登入 (SSO) 至裝置、應用程式和服務，他們才能隨時隨地保有生產力。 當您有多個身分識別解決方案要管理時，這不只會成為 IT 的系統管理問題，對於必須記住多組密碼的使用者而言也是個問題。

將相同的身分識別解決方案使用於您所有的應用程式和資源，即可達成 SSO。 而不論資源位於內部部署或雲端，使用者都可以使用同一組認證來登入及存取他們所需的資源。

**最佳做法**：啟用 SSO。  
**詳細資料**：Azure AD 會[將內部部署 Active Directory 延伸](../active-directory/connect/active-directory-aadconnect.md)至雲端。 使用者可以使用其主要公司或學校帳戶來登入已加入網域的裝置、公司資源，也能登入完成其作業所需的所有 Web 和 SaaS 應用程式。 使用者不需要記住多組使用者名稱和密碼，而且可根據其組織群組成員資格及其身為員工的狀態，自動佈建 (或解除佈建) 其應用程式存取權。 而且，您可以透過 [Azure AD 應用程式 Proxy](../active-directory/active-directory-application-proxy-get-started.md) 控制資源庫應用程式或您已開發並發佈之自有內部部署應用程式的存取權。

使用 SSO 讓使用者根據其在 Azure AD 中的公司或學校帳戶存取其 [SaaS 應用程式](../active-directory/active-directory-appssoaccess-whatis.md)。 這不只適用於 Microsoft SaaS 應用程式，也適用於其他應用程式，例如 [Google Apps](../active-directory/active-directory-saas-google-apps-tutorial.md) 和 [Salesforce](../active-directory/active-directory-saas-salesforce-tutorial.md)。 您可以將應用程式設定為使用 Azure AD 作為 [SAML 型身分識別](../active-directory/fundamentals-identity.md)提供者。 為了控制安全性，Azure AD 不會核發允許使用者登入應用程式的權杖，除非他們已透過 Azure AD 獲得存取權。 您可以直接授與存取權，或透過使用者所屬的群組授與。

未建立通用身分識別來對使用者和應用程式建立 SSO 的組織，更容易遭遇使用者有多組密碼的情況。 這些情況會提高使用者重複使用密碼或使用弱式密碼的可能性。

## <a name="turn-on-conditional-access"></a>開啟 條件式存取

使用者可以使用各種裝置和應用程式，從任何位置存取您組織的資源。 身為 IT 系統管理員，您會想要確保這些裝置符合安全性與合規性標準。 只將焦點放在誰可以存取資源，已不再足夠。

安全性與生產力之間取得平衡，您需要思考才能進行存取控制的相關決定要如何存取資源。 使用 Azure AD 條件式存取，您可以解決這項需求。 使用條件式存取，您可以進行以存取您雲端應用程式的條件為基礎的自動化的存取控制決定。

**最佳做法**：管理和控制公司資源的存取權。  
**詳細資料**：設定 Azure AD[條件式存取](../active-directory/active-directory-conditional-access-azure-portal.md)根據群組、 位置和 SaaS 應用程式與 Azure AD 連線應用程式的應用程式敏感性。

**最佳做法**：封鎖舊版驗證通訊協定。
**詳細資料**：攻擊者利用舊的通訊協定中的弱點每一天，特別是針對噴灑防範密碼攻擊。 設定條件式存取封鎖舊版通訊協定。 請觀看影片[Azure AD:建議與禁忌](https://www.youtube.com/watch?v=wGk0J4z90GI)如需詳細資訊。

## <a name="enable-password-management"></a>啟用密碼管理

如果您有多個租用戶或想要讓使用者[重設其密碼](../active-directory/active-directory-passwords-update-your-own-password.md)，請務必使用適當的安全性原則來防止不當使用。

**最佳做法**：為使用者設定自助式密碼重設 (SSPR)。  
**詳細資料**：使用 Azure AD [自助式密碼重設](../active-directory-b2c/active-directory-b2c-reference-sspr.md)功能。

**最佳做法**：監視 SSPR 的使用方式或是否真的正在使用它。  
**詳細資料**：使用 Azure AD [密碼重設註冊活動報告](../active-directory/active-directory-passwords-get-insights.md)，監視正在註冊的使用者。 Azure AD 提供的報告功能可協助您使用預先建立的報告來回答問題。 如果您已適當地取得授權，則也可以建立自訂查詢。

**最佳做法**：擴充您的內部部署基礎結構的雲端密碼原則。
**詳細資料**：加強您組織中的密碼原則的雲端架構的密碼變更一樣，執行相同的檢查內部部署密碼變更。 安裝[Azure AD 密碼保護](../active-directory/authentication/concept-password-ban-bad.md)以 Windows Server Active Directory 代理程式-從內部部署擴充到您現有的基礎結構遭到禁用的密碼清單。 使用者和系統管理員變更，請設定，或重設內部部署所需遵守相同的密碼原則，為僅限雲端使用者的密碼。

## <a name="enforce-multi-factor-verification-for-users"></a>對使用者強制執行多重要素驗證

建議您要求所有使用者都使用雙步驟驗證。 這包括系統管理員，以及組織中帳戶遭到入侵時會造成重大影響的其他人員 (例如財務人員)。

有很多選項可供您要求使用雙步驟驗證。 最適合您的選擇取決於您的目標、您正在執行的 Azure AD 版本，以及您的授權方案。 請參閱[如何要求使用者使用雙步驟驗證](../active-directory/authentication/howto-mfa-userstates.md)，以判斷最適合您的選項。 如需有關授權和定價的詳細資訊，請參閱 [Azure AD](https://azure.microsoft.com/pricing/details/active-directory/) 和 [Azure Multi-Factor Authentication](https://azure.microsoft.com/pricing/details/multi-factor-authentication/) 定價頁面。

以下是啟用雙步驟驗證的選項和優點：

**選項 1**：[藉由變更使用者狀態來啟用 Multi-Factor Authentication](../active-directory/authentication/howto-mfa-userstates.md)。   
**優點**：這是要求使用雙步驟驗證的傳統方法。 同時適用於[雲端與 Azure Multi-Factor Authentication Server 中的 Azure Multi-Factor Authentication](../active-directory/authentication/concept-mfa-whichversion.md)。 使用此方法需要每次登入時執行雙步驟驗證的使用者，並覆寫條件式存取原則。

若要判斷何處啟用 Multi-factor Authentication，請參閱[哪個版本的 Azure MFA 是最適合我的組織？](../active-directory/authentication/concept-mfa-whichversion.md)。

**選項 2**：[使用條件式存取原則中啟用 Multi-factor Authentication](../active-directory/authentication/howto-mfa-getstarted.md)。
**優點**：此選項可讓您能夠提示進行雙步驟驗證，在特定情況下，使用[條件式存取](../active-directory/active-directory-conditional-access-azure-portal.md)。 特定條件可以是使用者從不同的位置、不受信任的裝置，或您認為有危險的應用程式登入。 定義您要求使用雙步驟驗證的特定條件，可讓您避免要持續提示使用者，這可能會帶來不愉快的使用者體驗。

這是最具彈性的方法，可為您的使用者啟用雙步驟驗證。 啟用條件式存取原則只適用於在雲端中的 Azure Multi-factor Authentication，並為 Azure AD premium 功能。 您可以在[部署雲端式 Azure Multi-Factor Authentication](../active-directory/authentication/howto-mfa-getstarted.md)中找到這個方法的詳細資訊。

**選項 3**：啟用 Multi-factor Authentication 條件式存取原則，藉由評估的使用者和登入風險[Azure AD Identity Protection](../active-directory/authentication/tutorial-risk-based-sspr-mfa.md)。   
**優點**：此選項可讓您：

- 偵測會影響貴組織身分識別的潛在弱點。
- 針對偵測到的與您組織的身分識別有關的可疑動作，設定自動回應。
- 調查可疑事件並採取適當動作以解決它們。

這個方法使用 Azure AD Identity Protection 風險評估，根據所有雲端應用程式的使用者和登入風險來判斷是否需要雙步驟驗證。 這個方法需要 Azure Active Directory P2 授權。 您可以在 [Azure Active Directory Identity Protection](../active-directory/identity-protection/overview.md) 中找到這個方法的詳細資訊。

> [!Note]
> 藉由變更使用者狀態來啟用 Multi-factor Authentication 的選項 1，會覆寫條件式存取原則。 選項 2 和 3 會使用條件式存取原則，因為您無法使用選項 1 與它們。

未新增額外身分識別保護層 (例如雙步驟驗證) 的組織比較容易遭受認證竊取攻擊。 認證竊取攻擊可能會導致資料洩漏。

## <a name="use-role-based-access-control"></a>使用角色型存取控制
雲端資源的存取管理的公司會使用雲端，至關重要。 [角色型存取控制 (RBAC)](../role-based-access-control/overview.md)可協助您管理誰可以存取 Azure 資源，這些資源，可以做什麼，以及哪些地方他們擁有存取權。

指定群組或個別角色負責在 Azure 中的特定函式，可協助避免混淆，可能會導致人力和自動化錯誤，會產生安全性風險。 對於想要強制執行資料存取安全性原則的組織，根據[需要知道](https://en.wikipedia.org/wiki/Need_to_know)和[最低權限](https://en.wikipedia.org/wiki/Principle_of_least_privilege)安全性原則限制存取權限是必須做的事。

安全性小組需要您的 Azure 資源，以評估及補救風險的可見度。 如果安全性小組有操作的責任，他們會需要額外的權限執行其工作。

您可以使用[RBAC](../role-based-access-control/overview.md)權限指派給使用者、 群組和應用程式在特定範圍。 角色指派的範圍可以是訂用帳戶、資源群組或單一資源。

**最佳做法**：區隔小組內的職責，僅授與的存取權執行其工作所需的使用者。 而不是讓所有人都不受限制的權限，在您的 Azure 訂用帳戶或資源，只允許特定的動作在特定範圍。
**詳細資料**：使用[內建 RBAC 角色](../role-based-access-control/built-in-roles.md)在 Azure 中指派權限給使用者。

> [!Note]
> 特定權限建立不必要的複雜性和混淆，累積成很難修正不用擔心中斷發生的 「 舊版 」 設定。 避免資源特定權限。 相反地，為整個企業的權限及訂用帳戶內的權限的資源群組中使用管理群組。 避免使用者特定的權限。 相反地，指派給群組存取 Azure AD 中。

**最佳做法**：授與安全性小組，讓他們可以評估和補救風險，請參閱 Azure 資源的存取 Azure 的責任。
**詳細資料**：授與安全性小組的 RBAC[安全性讀取者](../role-based-access-control/built-in-roles.md#security-reader)角色。 您可以使用其根管理群組或區段管理群組中，視責任範圍而定：

- **根管理群組**小組負責所有的企業資源
- **區段管理群組**團隊限定範圍 （通常是因為法規或其他組織的界限）

**最佳做法**：授與安全性小組的直接操作的適當權限。
**詳細資料**：檢閱適當的角色指派的 RBAC 內建角色。 如果內建角色不符合您組織的特定需求，您可以建立[適用於 Azure 資源的自訂角色](../role-based-access-control/custom-roles.md)。 如有內建角色，您可以將自訂角色指派給使用者、 群組和訂用帳戶、 資源群組和資源範圍的服務主體。

**最佳做法**：需要的安全性角色授與 Azure 資訊安全中心存取。 資訊安全中心可讓安全性小組快速地找出與補救風險。
**詳細資料**：這些需求與安全性小組加入 RBAC[安全性系統管理員](../role-based-access-control/built-in-roles.md#security-admin)角色，讓他們可以檢視安全性原則、 檢視安全性狀態、 編輯安全性原則、 檢視警示和建議，並關閉警示和建議。 您可以使用其根管理群組或區段管理群組中，視責任範圍而定。

未強制執行資料存取控制，使用功能，例如 RBAC 可能會提供更多的權限，比使用者所需的組織。 這可能會導致資料洩漏藉由允許使用者存取他們不應具備的資料 （例如，高度業務衝擊） 的類型。

## <a name="lower-exposure-of-privileged-accounts"></a>降低特殊權限帳戶的暴露風險

保護特殊權限存取是保護企業資產很重要的第一個步驟。 將能夠存取安全資訊或資源的人數降到最低，可以降低惡意使用者取得該存取權，或者授權使用者無意中影響到敏感資源的機率。

特殊權限帳戶是可管理 IT 系統的帳戶。 網路攻擊者會以這些帳戶為目標，來取得組織資料和系統的存取權。 為了保護特殊權限存取，您應該讓帳戶和系統遠離遭遇惡意使用者的風險。

我們建議您擬定並遵循適當計劃以保護特殊權限存取，使網路攻擊者無法取得。 如需有關如何擬定詳細的藍圖，以保護 Azure AD、Microsoft Azure、Office 365 和其他雲端服務所管理或報告的身分識別和存取權，請檢閱[在 Azure AD 中保護混合式部署和雲端部署的特殊權限存取](../active-directory/users-groups-roles/directory-admin-roles-secure.md)。

以下摘要說明[在 Azure AD 中保護混合式部署和雲端部署的特殊權限存取](../active-directory/users-groups-roles/directory-admin-roles-secure.md)中找到的最佳做法：

**最佳做法**：管理、控制及監視特殊權限帳戶的存取權。   
**詳細資料**：開啟 [Azure AD Privileged Identity Management](../active-directory/privileged-identity-management/active-directory-securing-privileged-access.md)。 開啟 Privileged Identity Management 之後，您會收到有關於特殊權限存取角色有所變更的通知電子郵件訊息。 您目錄中的高特殊權限角色新增了其他使用者時，這些通知將會提供早期警告。

**最佳做法**：請確定所有重要的系統管理員帳戶所管理的 Azure AD 帳戶。
**詳細資料**：重要的系統管理員角色 （例如，hotmail.com、 live.com 和 outlook.com 等的 Microsoft 帳戶） 中移除任何取用者的帳戶。

**最佳做法**：請確定所有重要的系統管理員角色有不同的帳戶系統管理工作，才能避免網路釣魚和其他攻擊方式來侵害系統管理權限。
**詳細資料**：建立個別的系統管理員帳戶已指派執行的系統管理工作所需的權限。 封鎖這些系統管理帳戶用於每日的產能工具，例如 Microsoft Office 365 電子郵件或任意的網頁瀏覽。

**最佳做法**：識別及分類具備高特殊權限角色的帳戶。   
**詳細資料**：在開啟 Azure AD Privileged Identity Management 之後，檢視具備全域管理員、特殊權限角色管理員和其他較高特殊權限角色的使用者。 請移除這些角色中不再需要的任何帳戶，並將指派給管理員角色的其餘帳戶分類：

- 個別指派給系統管理使用者，並且可用於非系統管理用途 (例如個人電子郵件)
- 個別指派給系統管理使用者，且指定為僅供系統管理之用
- 在多個使用者之間共用
- 用於緊急存取案例
- 用於自動化指令碼
- 用於外部使用者

**最佳做法**：實作 Just-In-Time (JIT) 存取，以進一步降低權限的暴露時間，並提升使用特殊權限帳戶對您的能見度。   
**詳細資料**：Azure AD Privileged Identity Management 可讓您：

- 限制使用者只能 JIT 取用其權限。
- 指派縮短持續時間的角色，而且有信心會自動撤銷權限。

**最佳做法**：定義至少兩個緊急存取帳戶。   
**詳細資料**：緊急存取帳戶可協助組織限制現有 Azure Active Directory 環境內的特殊權限存取。 這些帳戶具有高特殊權限，不會指派給特定個人。 緊急存取帳戶僅限用於無法使用一般系統管理帳戶的情況。 組織必須將緊急帳戶的使用量限制於僅只必要的時間量。

請評估已指派或適用於全域管理員角色的帳戶。 如果使用 `*.onmicrosoft.com` 網域 (供緊急存取使用)，並未看到任何僅限雲端的帳戶，請加以建立。 如需詳細資訊，請參閱[在 Azure AD 中管理緊急存取系統管理帳戶](../active-directory/users-groups-roles/directory-emergency-access.md)。

**最佳做法**：備妥，萬一發生緊急狀況中的 「 急用 」 的程序。
**詳細資料**：請依照下列中的步驟[保護特殊權限存取 Azure AD 中的 混合式部署和雲端部署](../active-directory/users-groups-roles/directory-admin-roles-secure.md)。

**最佳做法**：需要為無密碼的所有重要的系統管理員帳戶 （慣用） 或要求多重要素驗證。
**詳細資料**：使用[Microsoft Authenticator 應用程式](../active-directory/authentication/howto-authentication-phone-sign-in.md)無須使用密碼登入任何 Azure AD 帳戶。 像是[Windows hello 企業版](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-identity-verification)，Microsoft 驗證器會使用金鑰型驗證，以便繫結至裝置，並使用生物識別驗證或 PIN 的使用者認證。

在 所有個別使用者永久指派給一或多個 Azure AD 管理員角色的登入需要 Azure Multi-factor Authentication:全域管理員、 特殊權限角色管理員、 Exchange Online 系統管理員和 SharePoint Online 系統管理員。 啟用[為您的系統管理員帳戶的 Multi-factor Authentication](../active-directory/authentication/howto-mfa-userstates.md) ，並確保系統管理員帳戶的使用者已註冊。

**最佳做法**：為重要的系統管理員帳戶，具有系統管理工作站，實際執行工作不允許 （適用於範例中，瀏覽和電子郵件） 的位置。 這會保護您的系統管理員帳戶從攻擊媒介，使用 瀏覽和電子郵件，並大幅降低您的主要事件的風險。
**詳細資料**：使用系統管理工作站。 選擇工作站的安全性層的級：

- 高度安全的產能的裝置提供進階的安全性，來瀏覽和其他產能的工作。
- [特殊權限存取工作站 (Paw)](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations)提供會來抵禦網際網路攻擊和威脅載體敏感性工作的專用的作業系統。

**最佳做法**：當員工離開組織時，取消佈建系統管理員帳戶。
**詳細資料**：備妥停用或刪除系統管理員帳戶，當員工離開組織中的處理程序。

**最佳做法**：使用目前的攻擊技巧，定期測試系統管理員帳戶。
**詳細資料**：使用 Office 365 攻擊模擬器或協力廠商供應項目在您的組織中執行實際的攻擊案例。 這可協助您尋找實際的攻擊發生前的易受攻擊的使用者。

**最佳做法**：採取步驟來減輕由最常被使用的攻擊技巧所造成的損害。  
**詳細資料**：[識別系統管理角色中需要切換至公司或學校帳戶的 Microsoft 帳戶](../active-directory/users-groups-roles/directory-admin-roles-secure.md#identify-microsoft-accounts-in-administrative-roles-that-need-to-be-switched-to-work-or-school-accounts)  

[確認全域系統管理員帳戶的個別使用者帳戶和郵件轉寄](../active-directory/users-groups-roles/directory-admin-roles-secure.md)  

[確訂系統管理帳戶的密碼近期做過變更](../active-directory/users-groups-roles/directory-admin-roles-secure.md#ensure-the-passwords-of-administrative-accounts-have-recently-changed)  

[開啟密碼雜湊同步處理](../active-directory/users-groups-roles/directory-admin-roles-secure.md#turn-on-password-hash-synchronization)  

[所有具有特殊權限角色的使用者和公開的使用者，都必須進行多重要素驗證](../active-directory/users-groups-roles/directory-admin-roles-secure.md#require-multi-factor-authentication-mfa-for-users-in-all-privileged-roles-as-well-as-exposed-users)  

[取得您的 Office 365 安全分數 (如果使用 Office 365)](../active-directory/users-groups-roles/directory-admin-roles-secure.md#obtain-your-office-365-secure-score-if-using-office-365)  

[檢閱 Office 365 安全性與合規性指引 (如果使用 Office 365)](../active-directory/users-groups-roles/directory-admin-roles-secure.md#review-the-office-365-security-and-compliance-guidance-if-using-office-365)  

[設定 Office 365 活動監視 (如果使用 Office 365)](../active-directory/users-groups-roles/directory-admin-roles-secure.md#configure-office-365-activity-monitoring-if-using-office-365)  

[建立事件/緊急回應計劃擁有者](../active-directory/users-groups-roles/directory-admin-roles-secure.md#establish-incidentemergency-response-plan-owners)  

[保護內部部署的特殊權限系統管理帳戶](../active-directory/users-groups-roles/directory-admin-roles-secure.md#turn-on-password-hash-synchronization)

如果您不保護特殊權限的存取，則可能發現您有太多具備較高特殊權限角色的使用者，而且比較容易遭受攻擊。 包括網路攻擊者在內的惡意人士通常會以管理帳戶和特殊權限存取的其他元素為目標，以利用認證竊取來取得敏感性資料和系統的存取權。

## <a name="control-locations-where-resources-are-created"></a>控制建立資源的位置

讓雲端操作者能夠執行工作，同時防止他們違反管理組織資源所需的慣例極為重要。 想要控制資源建立位置的組織應將這些位置硬式編碼。

您可以使用 [Azure Resource Manager](../azure-resource-manager/resource-group-overview.md) 來建立安全性原則，其定義會描述明確拒絕的動作或資源。 在所需範圍內指派那些原則定義，例如訂用帳戶、資源群組或是個別的資源。

> [!NOTE]
> 安全性原則與 RBAC 不同。 這類原則實際上使用 RBAC 來授權使用者建立這些資源。
>
>

不控制資源建立方式的組織，比較容易遇到使用者因建立超過所需資源而濫用服務的情況。 強化資源建立程序是保護多租用戶案例的重要步驟。

## <a name="actively-monitor-for-suspicious-activities"></a>主動監視可疑的活動

主動身分識別監視系統可快速偵測可疑行為並觸發警示，以便進一步調查。 下表列出兩項可協助組織監視其身分識別的 Azure AD 功能：

**最佳做法**：想辦法識別：

- 嘗試在[不被追蹤](../active-directory/active-directory-reporting-sign-ins-from-unknown-sources.md)的情況下登入。
- 針對特定帳戶的[暴力密碼破解](../active-directory/active-directory-reporting-sign-ins-after-multiple-failures.md)攻擊。
- 嘗試從多個位置登入。
- 從 [受感染的裝置登入](../active-directory/active-directory-reporting-sign-ins-from-possibly-infected-devices.md)。
- 可疑的 IP 位址。

**詳細資料**：使用 Azure AD Premium 的[異常報告](../active-directory/active-directory-view-access-usage-reports.md)。 備妥相關處理和程序，以便 IT 系統管理員每天或依需求執行這些報告 (通常出現在事件回應案例)。

**最佳做法**：採用主動監視系統，該系統會將風險通知您，並可針對您的業務需求調整風險層級 (高、中或低)。   
**詳細資料**：使用[Azure AD Identity Protection](../active-directory/active-directory-identityprotection.md)，它會在自己的儀表板上標示目前的風險，並透過電子郵件傳送每日摘要通知。 若要協助保護貴組織的身分識別，您可設定以風險為基礎的原則，以在達到指定的風險層級時自動回應偵測到的問題。

未主動監視其身分識別系統的組織有洩漏使用者認證的風險。 若不知道有透過這些認證進行的可疑活動，組織便無法減輕這類型的威脅。

## <a name="use-azure-ad-for-storage-authentication"></a>使用 Azure AD 進行驗證儲存體
[Azure 儲存體](../storage/common/storage-auth-aad.md)支援 Blob 儲存體和佇列儲存體的驗證和使用 Azure AD 進行授權。 使用 Azure AD 驗證，您可以使用 Azure 角色型存取控制以特定的權限授與使用者、 群組和個別的 blob 容器或佇列的範圍下的應用程式。

我們建議您改用[Azure AD 驗證存取儲存體](https://azure.microsoft.com/blog/azure-storage-support-for-azure-ad-based-access-control-now-generally-available/)。

## <a name="next-step"></a>後續步驟

如需更多安全性最佳做法，請參閱 [Azure 安全性最佳做法與模式](security-best-practices-and-patterns.md)，以便在使用 Azure 設計、部署和管理雲端解決方案時使用。
