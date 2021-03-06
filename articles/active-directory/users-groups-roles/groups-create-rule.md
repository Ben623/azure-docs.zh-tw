---
title: 建立或編輯動態群組並取得狀態-Azure AD |Microsoft Docs
description: 如何建立或更新 Azure 入口網站中的群組成員資格規則，並檢查其處理狀態。
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 01/07/2020
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: d2ed7f27e2145f666f38eec5ddc6c985a4d32138
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/13/2020
ms.locfileid: "79266373"
---
# <a name="create-or-update-a-dynamic-group-in-azure-active-directory"></a>在 Azure Active Directory 中建立或更新動態群組

在 Azure Active Directory （Azure AD）中，您可以使用規則根據使用者或裝置屬性來判斷群組成員資格。 本文說明如何在 Azure 入口網站中設定動態群組的規則。
安全性群組或 Office 365 群組支援動態成員資格。 套用群組成員資格規則時，會評估使用者和裝置屬性是否符合成員資格規則。 當使用者或裝置的屬性變更時，會處理組織中的所有動態群組規則，以進行成員資格變更。 如果使用者和裝置符合群組的條件，則會加以新增或移除。 安全性群組可以用於裝置或使用者，但 Office 365 群組只能是使用者群組。

## <a name="rule-builder-in-the-azure-portal"></a>Azure 入口網站中的規則產生器

Azure AD 提供規則產生器，更快速地建立及更新您的重要規則。 規則產生器支援最多五個運算式的結構。 規則產生器可讓您更輕鬆地使用一些簡單的運算式來形成規則，不過，它不能用來重現每個規則。 如果規則產生器不支援您想要建立的規則，您可以使用文字方塊。

以下是一些我們建議您使用文字方塊來建立的先進規則或語法範例：

- 具有五個以上運算式的規則
- 直接報告規則
- 設定[運算子優先順序](groups-dynamic-membership.md#operator-precedence)
- [具有複雜運算式的規則](groups-dynamic-membership.md#rules-with-complex-expressions);例如 `(user.proxyAddresses -any (_ -contains "contoso"))`

> [!NOTE]
> 「規則產生器」可能無法顯示在文字方塊中所建立的某些規則。 當規則產生器無法顯示規則時，您可能會看到一則訊息。 規則產生器不會以任何方式變更支援的語法、驗證或處理動態群組規則。

![新增動態群組的成員資格規則](./media/groups-create-rule/update-dynamic-group-rule.png)

如需語法、支援的屬性、運算子和成員資格規則值的範例，請參閱[Azure Active Directory 中群組的動態成員資格規則](groups-dynamic-membership.md)。

## <a name="to-create-a-group-membership-rule"></a>建立群組成員資格規則

1. 使用租使用者中全域管理員、Intune 管理員或使用者系統管理員角色的帳戶登入[Azure AD 系統管理中心](https://aad.portal.azure.com)。
1. 搜尋並選取 [**群組**]。
1. 選取 [所有群組]，然後選取 [新增群組]。

   ![選取命令以新增群組](./media/groups-create-rule/create-new-group-azure-active-directory.png)

1. 在 [**群組**] 頁面上，輸入新群組的名稱和描述。 選取使用者或裝置的**成員資格類型**，然後選取 [**新增動態查詢**]。 「規則產生器」最多支援五個運算式。 若要加入五個以上的運算式，您必須使用文字方塊。

   ![新增動態群組的成員資格規則](./media/groups-create-rule/add-dynamic-group-rule.png)

1. 若要查看您的成員資格查詢可用的自訂延伸模組屬性：
   1. 選取 [**取得自訂擴充屬性**]
   1. 輸入 [應用程式識別碼]，然後選取 [重新整理**屬性**]。
1. 建立規則之後，請選取 [**儲存**]。
1. 選取 [**新增群組**] 頁面上的 [**建立**] 來建立群組。

如果您輸入的規則無效，說明無法處理規則的原因會顯示在入口網站的 Azure 通知中。 請仔細閱讀，以瞭解如何修正規則。

## <a name="to-update-an-existing-rule"></a>若要更新現有規則

1. 使用位於租使用者中全域管理員、群組系統管理員、Intune 管理員或使用者系統管理員角色的帳戶登入[Azure AD 系統管理中心](https://aad.portal.azure.com)。
1. 選取 [**群組**] > [**所有群組**]。
1. 選取群組以開啟其設定檔。
1. 在群組的 [設定檔] 頁面上，選取 [**動態成員資格規則**]。 「規則產生器」最多支援五個運算式。 若要加入五個以上的運算式，您必須使用文字方塊。

   ![新增動態群組的成員資格規則](./media/groups-create-rule/update-dynamic-group-rule.png)

1. 若要查看您的成員資格規則可用的自訂延伸模組屬性：
   1. 選取 [**取得自訂擴充屬性**]
   1. 輸入 [應用程式識別碼]，然後選取 [重新整理**屬性**]。
1. 更新規則之後，請選取 [**儲存**]。

## <a name="turn-on-or-off-welcome-email"></a>開啟或關閉歡迎電子郵件

當建立新的 Office 365 群組時，會將新增至群組的使用者傳送歡迎電子郵件通知。 之後，如果使用者或裝置的任何屬性變更，則會處理組織中的所有動態群組規則，以進行成員資格變更。 新增的使用者隨後也會收到歡迎通知。 您可以在[Exchange PowerShell](https://docs.microsoft.com/powershell/module/exchange/users-and-groups/Set-UnifiedGroup?view=exchange-ps)中關閉此行為。

## <a name="check-processing-status-for-a-rule"></a>檢查規則的處理狀態

您可以在群組的 [概觀] 頁面上看到成員資格處理狀態和上次更新日期。
  
  ![動態群組狀態的顯示](./media/groups-create-rule/group-status.png)

可能會針對**成員資格處理**狀態顯示下列狀態訊息：

- **評估中**：已收到群組變更，而且正在評估更新。
- **處理中**：正在處理更新。
- **更新完成**：處理已完成，並已建立所有適用的更新。
- **處理錯誤**：無法完成處理，因為評估成員資格規則時發生錯誤。
- **已暫停更新**：系統管理員已暫停動態成員資格規則更新。 MembershipRuleProcessingState 設定為「已暫停」。

可能會針對**成員資格上次更新**狀態顯示下列狀態訊息：

- &lt;**日期和時間**&gt;：上次更新成員資格的時間。
- **進行中**：目前正在更新。
- **未知**：無法抓取上次更新時間。 群組可能是新的。

如果處理特定群組的成員資格規則時發生錯誤，則會在群組的 [概觀] 頁面頂端顯示警示。 如果租使用者中的所有群組都無法處理擱置中的動態成員資格更新超過24小時，則會在 [**所有群組**] 的頂端顯示警示。

![處理錯誤訊息警示](./media/groups-create-rule/processing-error.png)

這些文章提供有關 Azure Active Directory 中群組的其他資訊。

- [查看現有的群組](../fundamentals/active-directory-groups-view-azure-portal.md)
- [建立新群組並新增成員](../fundamentals/active-directory-groups-create-azure-portal.md)
- [管理群組的設定](../fundamentals/active-directory-groups-settings-azure-portal.md)
- [管理群組的成員資格](../fundamentals/active-directory-groups-membership-azure-portal.md)
- [管理群組中使用者的動態規則](groups-dynamic-membership.md)
