---
title: Azure Active Directory Identity Protection | Microsoft Docs
description: 了解 Azure AD Identity Protection 如何讓您限制攻擊者利用遭入侵的身分識別或裝置的能力，以及保護先前疑似或已知遭到入侵的身分識別或裝置。
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: overview
ms.date: 01/29/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8b89cab41061376fc1d8b4cbffc8fe87b9677688
ms.sourcegitcommit: 07700392dd52071f31f0571ec847925e467d6795
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/28/2019
ms.locfileid: "70125682"
---
# <a name="what-is-azure-active-directory-identity-protection"></a>Azure Active Directory Identity Protection 是什麼？

Azure Active Directory [Identity](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-whatis#terminology) Protection 可讓組織設定自動回應，以偵測與使用者身分識別相關的可疑動作。

## <a name="get-started"></a>開始使用

Microsoft 保護雲端身分識別已經超過十多年。 在您的環境中使用 Azure Active Directory Identity Protection，即可使用與 Microsoft 用來保護身份識別一樣的保護系統。

大部分的安全性缺口出現於當攻擊者藉由竊取使用者的身分識別來取得環境的存取權時。 這些年來，攻擊者變得越來越擅於利用第三方缺口，以及使用複雜的網路釣魚攻擊。 只要攻擊者取得更低權限的使用者帳戶的存取權，他們即可相對容易地透過橫向移動存取重要的公司資源。

如此一來，您必須：

- 保護所有的識別身分，不論其權限層級
- 主動防止遭入侵的身分識別被濫用

探索遭入侵的身分識別並不容易。 Azure Active Directory 使用調適性機器學習運算法和啟發學習法來偵測表示可能遭入侵身份識別的異常與可疑事件。 Identity Protection 會使用此資料來產生報告和警示，讓您評估偵測到的問題並採取適當的緩和或補救動作。

Azure Active Directory Identity Protection 不只是監視和報告工具而已。 若要保護您組織的身分識別，您可以設定以風險為基礎的原則，當達到指定風險層級時自動回應偵測到的問題。 除了 Azure Active Directory 與 [Enterprise Mobility + Security](https://docs.microsoft.com/enterprise-mobility-security/) (EMS) 所提供的其他條件式存取控制以外，這些的原則可以自動封鎖或起始調適性補救動作，包括重設密碼以及強制 Multi-Factor Authentication。

### <a name="identity-protection-capabilities"></a>Identity Protection 功能

**偵測弱點和風險帳戶：**  

- 提供自訂建議，藉由反白顯示弱點來改善整體安全性狀態
- 計算登入風險層級
- 計算使用者風險層級

**調查風險偵測：**

- 傳送風險偵測的通知
- 使用相關和內容資訊來調查風險偵測
- 提供基本工作流程來追蹤調查
- 讓您輕鬆存取補救動作，例如重設密碼

**風險條件式存取原則：**

- 此原則可藉由封鎖登入或要求多重要素驗證挑戰，以儘量阻止高風險登入
- 此原則會封鎖或保護有風險的使用者帳戶
- 此原則會要求使用者註冊以便進行 Multi-Factor Authentication

## <a name="identity-protection-roles"></a>Identity Protection 角色

若要讓 Identity Protection 實作方面的管理活動達到負載平衡，您可以指派數個角色。 Azure AD Identity Protection 支援 3 種目錄角色：

| 角色 | 可以執行 | 無法執行 |
| :-- | --- | --- |
| 全域管理員 | 完整存取 Identity Protection、將 Identity Protection 上架| |
| 安全性系統管理員 | 完整存取 Identity Protection | 將 Identity Protection 上架、重設使用者密碼 |
| 安全性讀取者 | 唯讀存取 Identity Protection | 讓 Identity Protection 上線、修復使用者、設定原則、重設密碼 |

如需詳細資訊，請參閱[在 Azure Active Directory 中指派系統管理員角色](../users-groups-roles/directory-assign-admin-roles.md)

## <a name="detection"></a>偵測

### <a name="vulnerabilities"></a>弱點

Azure Active Directory Identity Protection 會分析您的組態，並偵測可能影響您使用者身份識別的弱點。 如需詳細資訊，請參閱 [Azure Active Directory Identity Protection 偵測到的弱點](vulnerabilities.md)。

### <a name="risk-detections"></a>風險偵測

Azure Active Directory 使用調適性機器學習運算法和啟發學習法來偵測與您使用者身份識別有關的可疑動作。 系統會針對每個偵測到的可疑動作建立記錄。 這些記錄又名風險偵測。  
如需詳細資訊，請參閱 [Azure Active 風險偵測](../active-directory-identity-protection-risk-events.md)。

## <a name="investigation"></a>調查

您通常會從 Identity Protection 儀表板開始使用 Identity Protection。

![補救](./media/overview/1000.png "補救")

儀表板可讓您存取：

- 報告，例如**標示有風險的使用者**、**風險偵測**和**弱點**
- 設定，例如 [安全性原則]  、[通知]  和 [Multi-Factor Authentication 註冊]  的組態

這通常是調查的起點，而在調查過程中會檢閱風險偵測相關活動、記錄和其他相關資訊，以決定是否需要採取補救或緩和步驟，了解身分識別如何遭到入侵，以及了解遭到入侵的身分識別如何被利用。

您可以將調查活動繫結至 Azure Active Directory Protection 傳送的每封電子郵件 [通知](notifications.md) 。

## <a name="policies"></a>原則

為了實作自動化回應，Azure Active Directory Identity Protection 提供三個原則：

- [多重要素驗證註冊原則](howto-mfa-policy.md)
- [使用者風險原則](howto-user-risk-policy.md)
- [登入風險原則](howto-sign-in-risk-policy.md)

## <a name="license-requirements"></a>授權需求

[!INCLUDE [Active Directory P2 license](../../../includes/active-directory-p2-license.md)]

## <a name="next-steps"></a>後續步驟

- [第 9 頻道：Azure AD 和身分識別示範：身分識別保護預覽版](https://channel9.msdn.com/Series/Azure-AD-Identity/Azure-AD-and-Identity-Show-Identity-Protection-Preview)
- [啟用 Azure Active Directory Identity Protection](enable.md)
