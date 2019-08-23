---
title: 部署方案 - Azure Active Directory | Microsoft Docs
description: 如何部署眾多 Azure Active Directory 功能的相關端對端指引。
services: active-directory
author: msaburnley
manager: daveba
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 08/20/2019
ms.author: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6a335bcbb45b1aac48a3aaea282ffeffe1696f4c
ms.sourcegitcommit: d3dced0ff3ba8e78d003060d9dafb56763184d69
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/22/2019
ms.locfileid: "69900074"
---
# <a name="azure-active-directory-deployment-plans"></a>Azure Active Directory 部署方案
正在尋找部署 Azure Active Directory (Azure AD) 功能的端對端指引嗎？ Azure AD 部署計畫會引導您瞭解成功部署常見 Azure AD 功能所需的商業價值、規劃考慮和操作程式。

從任何計畫頁面, 使用瀏覽器的 [列印至 PDF] 功能來建立檔的最新離線版本。
## <a name="include-the-right-stakeholders"></a>包含正確的專案關係人

當您開始規劃新功能的部署時, 請務必在組織中包含重要的專案關係人。 我們建議您識別並記載符合下列每個角色的人員或人員, 並與他們合作以判斷他們在專案中的參與程度。  

角色可能包括下列各項 

|Role |描述 |
|-|-|
|終端使用者|將會實作為功能的代表性使用者群組。 通常會預覽試驗計畫中的變更。
|IT 支援經理|IT 支援組織代表誰可以從技術支援人員的觀點來提供這項變更的可支援性。  
|身分識別架構設計人員或 Azure 全域管理員|身分識別管理小組代表負責定義這項變更如何與組織中的核心身分識別管理基礎結構一致。|
|應用程式商務擁有者 |受影響應用程式的整體商務擁有者, 可能包括管理存取權。  也可以提供使用者體驗的輸入, 以及從使用者的觀點來看這項變更的實用性。
|安全性擁有者|安全性小組的代表, 可以將方案登出, 以符合組織的安全性需求。|
|合規性管理員|貴組織內負責確保符合公司、產業或政府需求的人員。|

**參與程度可能包括:**

- 用於執行專案計劃和結果的**R**esponsible 

- 專案計劃和結果的 pproval 

- **C**ontributor 至專案計劃和結果 

- 專案計劃和結果 nformed


## <a name="deploy-authentication"></a>部署驗證

| 功能 | 描述|
| -| -|
| [Multi-Factor Authentication](https://docs.microsoft.com/en-us/azure/active-directory/authentication/howto-mfa-getstarted)| Azure Multi-Factor Authentication (MFA) 是 Microsoft 的雙步驟驗證解決方案。 Azure MFA 使用系統管理員核准的驗證方法, 協助保護對您的資料和應用程式的存取, 同時滿足簡單登入流程的需求。 |
| [條件式存取](https://aka.ms/deploymentplans/ca)| 透過條件式存取, 您可以根據條件, 針對可存取雲端應用程式的人員執行自動化存取控制決策。 |
| [自助式密碼重設](https://aka.ms/deploymentplans/sspr)| 自助式密碼重設可協助您的使用者在不需要系統管理員介入的情況下, 重設其密碼。 |

## <a name="deploy-application-management"></a>部署應用程式管理

| 功能 | 描述|
| -| - |
| [單一登入](https://aka.ms/deploymentplans/sso)| 單一登入可協助您的使用者在只登入一次時, 存取所需的應用程式和資源來執行業務。 登入之後, 他們可以從 Microsoft Office 到 SalesForce 到 Box, 到內部應用程式, 而不需要再次輸入認證。 |
| [存取面板](https://aka.ms/deploymentplans/accesspanel)| 為使用者提供可探索及存取其所有應用程式的簡單中樞。 使用自助功能讓它們更具生產力, 例如要求應用程式和群組的存取權, 或代表其他人管理資源的存取權。 |


## <a name="deploy-hybrid-scenarios"></a>部署混合式案例

| 功能 | 描述|
| -| -|
| [以 ADFS 進行密碼雜湊同步處理](https://aka.ms/deploymentplans/adfs2phs)| 使用密碼雜湊同步處理時, 使用者密碼的雜湊會從內部部署 Active Directory 同步到 Azure AD, 讓 Azure AD 驗證使用者, 而不需要與內部部署互動 Active Directory |
| [以 ADFS 通過驗證](https://aka.ms/deploymentplans/adfs2pta)| Azure AD 傳遞驗證可協助您的使用者使用相同的密碼登入內部部署和雲端式應用程式。 這項功能可為使用者提供更好的體驗, 也就是要記住的密碼較少, 並降低 IT 技術服務人員的成本, 因為使用者較不可能忘記登入的方式。 當使用者使用 Azure AD 登入時，此功能會向您的內部部署 Active Directory 直接驗證使用者的密碼。 |
| [Azure AD 應用程式 Proxy](https://aka.ms/deploymentplans/appproxy)| 現今的員工想要隨時隨地都能在任何裝置發揮生產力。 他們需要存取雲端中的 SaaS 應用程式和內部部署的公司應用程式。 Azure AD 應用程式 proxy 可讓這種健全的存取不需要昂貴且複雜的虛擬私人網路 (Vpn) 或非軍事區域 (Dmz)。 |
| [無縫 SSO](https://aka.ms/SeamlessSSODPDownload)| 使用者位於連線到公司網路的公司裝置時，Azure Active Directory 無縫單一登入 (Azure AD 無縫 SSO) 就會自動將他們登入。 有了這項功能, 使用者就不需要輸入密碼就能登入 Azure AD, 而且通常不需要輸入他們的使用者名稱。 這項功能可讓授權使用者輕鬆存取您的雲端應用程式, 而不需要任何額外的內部部署元件。 |

## <a name="deploy-user-provisioning"></a>部署使用者布建

| 功能 | 描述|
| -| -|
| [使用者佈建](https://aka.ms/UserProvisioningDPDownload)| Azure AD 可協助您自動化在雲端 (SaaS) 應用程式 (例如 Dropbox、Salesforce、ServiceNow 等等) 中建立、維護和移除使用者身分識別的作業。 |
| [Workday 導向的輸入使用者佈建](https://aka.ms/WorkdayDeploymentPlan)| 對 Active Directory 進行 Workday 導向的輸入使用者佈建，可為進行中的身分識別治理建立基底，並為依賴權威性身分識別資料的商務程序加強品質。 使用這項功能, 您可以設定規則, 將 Leaver 的程式 (例如新的員工、終止、轉移) 對應到 IT 布建動作 (例如建立、啟用、啟用 |

## <a name="deploy-governance-and-reporting"></a>部署治理和報告

| 功能 | 描述|
| -| -|
| [Privileged Identity Management](https://aka.ms/deploymentplans/pim)| Azure AD Privileged Identity Management (PIM) 可協助您管理遍及 Azure AD、Azure 資源及其他 Microsoft Online Services 的特殊權限系統管理角色。 PIM 提供 Just-In-Time 存取、要求核准工作流程，以及完全整合的存取權檢閱等解決方案，讓您能夠即時識別、發現和防止特殊權限角色的惡意活動。 |
| [報告和監視](https://aka.ms/deploymentplans/reporting)| Azure AD 報告和監視解決方案的設計取決於您的法律、安全性和營運需求, 以及現有的環境和程式。 本文提供各種不同的設計選項, 並引導您進行適當的部署策略。 |
