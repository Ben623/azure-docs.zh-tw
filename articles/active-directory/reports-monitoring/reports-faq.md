---
title: Azure Active Directory 報告常見問題集 | Microsoft Docs
description: 關於 Azure Active Directory 報表的常見問題。
services: active-directory
documentationcenter: ''
author: cawrites
manager: MarkusVi
ms.assetid: 534da0b1-7858-4167-9986-7a62fbd10439
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: report-monitor
ms.date: 11/13/2018
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 273fdb80475defb0576bcd29d1944c5f6c595cfc
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/13/2020
ms.locfileid: "79266503"
---
# <a name="frequently-asked-questions-around-azure-active-directory-reports"></a>關於 Azure Active Directory 報告的常見問題集

本文會回答有關 Azure Active Directory (Azure AD) 報告的常見問題。 如需詳細資訊，請參閱 [Azure Active Directory 報告](overview-reports.md)。 

## <a name="getting-started"></a>開始使用 

**問：我目前使用 `https://graph.windows.net/<tenant-name>/reports/` 端點 Api，以程式設計方式將 Azure AD 審核和整合的應用程式使用方式報告提取到我們的報告系統中。我該切換到什麼？**

**答：** 請查閱 [API 參考](https://developer.microsoft.com/graph/)，查看您可以如何使用 API 來存取[活動報告](concept-reporting-api.md)。 此端點有兩個報告 (**稽核**和**登入**)，提供您在舊有 API 端點中取得的所有資料。 這個新端點也有 Azure AD Premium 授權的登入報告，您可以用它來取得應用程式使用方式、裝置使用方式，以及使用者登入資訊。

---

**問：我目前使用 `https://graph.windows.net/<tenant-name>/reports/` 端點 Api，以程式設計方式將 Azure AD 的安全性報告（例如洩漏的認證或從匿名 IP 位址登入等特定類型的偵測）提取到我們的報告系統中。我該切換到什麼？**

**答：** 您可以使用身分 [識別保護風險偵測 API](../identity-protection/graph-get-started.md) ，透過 Microsoft Graph 存取安全性偵測。 這種新格式可讓您以更大的彈性來查詢資料、使用先進的篩選、欄位選取等等，以及將風險偵測標準化成一種類型，以便更輕鬆地整合到 Siem 和其他資料收集工具。 由於資料的格式不同，您無法以新查詢替換舊查詢。 不過，[新 API 會使用 Microsoft Graph](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/identityriskevent)，這是 O365 或 Azure AD 這類 API 的 Microsoft 標準格式。 因此，所需的工作可以擴充您目前的 Microsoft Graph 投資，或協助您開始轉換到這個新的標準平臺。

---

**問：我要如何取得進階授權？**

**答：** 請參閱[開始使用 Azure Active Directory Premium](../fundamentals/active-directory-get-started-premium.md) 來升級 Azure Active Directory 版本。

---

**問：在取得進階授權之後，我應該多快能看見活動資料？**

**答：** 如果您在使用免費授權時即已經有活動資料，您便可以立即看見該資料。 如果您沒有任何資料，則必須花一或兩天的時間，資料才會顯示在報告中。

---

**問：在取得 Azure AD 進階授權之後，我能否看見上個月的資料？**

**答：** 如果您是最近切換到進階版 (包括試用版)，一開始最多可以看見 7 天的資料。 當資料累積起來時，您便可以看到過去 30 天的資料。

---

**問：我是否必須是全域系統管理員，才能看到登入 Azure 入口網站的活動，或是透過 API 取得資料？**

**答：** 不必，如果您是租用戶的**安全性讀取者**或**安全性系統管理員**，則也可以透過入口網站或透過 API 存取報告資料。 當然，**全域系統管理員**也可存取此資料。

---


## <a name="activity-logs"></a>活動記錄


**問：Azure 入口網站中活動記錄 (稽核和登入) 的資料保留期是多長？** 

**答：** 下表列出活動記錄的資料保留期間。 如需詳細資訊，請參閱 [Azure AD 報告的資料保留原則](reference-reports-data-retention.md)。

| Report                 | Azure AD Free | Azure AD Premium P1 | Azure AD Premium P2 |
| :--                    | :--           | :--                 | :--                 |
| 稽核記錄             | 7 天        | 30 天             | 30 天             |
| 登入               | N/A           | 30 天             | 30 天             |
| Azure MFA 使用        | 30 天       | 30 天             | 30 天             |

---

**問：在我完成工作之後，要多久的時間才能看見活動資料？**

**答：** 稽核記錄的延遲時間為 15 分鐘到 1 小時。 針對某些記錄，登入活動記錄可能需要 15 分鐘到 2 小時。

---

**問：我是否可以透過 Azure 入口網站取得 Office 365 活動記錄資訊？**

**答：** 雖然 Office 365 活動和 Azure AD 活動記錄共用許多目錄資源，但如果您想要完整看到 Office 365 活動記錄，您應該移至[Microsoft 365 系統管理中心](https://admin.microsoft.com)，取得 Office 365 活動記錄資訊。

---

**問：我應該使用哪些 API 來取得 Office 365 活動記錄的相關資訊？**

**答：** 請使用「Office 365 管理 API」來存取 [Office 365 活動記錄 (透過 API)](https://docs.microsoft.com/office/office-365-management-api/office-365-management-apis-overview)。

---

**問：我可以從 Azure 入口網站下載多少記錄？**

**答：** 您可以從 Azure 入口網站最多下載 5000 筆記錄。 這些記錄會依「時間上最近」方式來排序，而根據預設，您會取得最近的 5000 筆記錄。

---

## <a name="risky-sign-ins"></a>有風險的登入

**問： Identity Protection 有風險偵測，但我在登入報告中看不到對應的登入。這是預期的嗎？**

**答：** 是，Identity Protection 會評估所有驗證流程的風險，無論是互動式或非互動式。 不過，所有登入只會報告顯示互動式登入。

---

**問：如何知道為何在 Azure 入口網站中登入或使用者會標示為風險？**

**答：** 如果您有**Azure AD Premium**訂用帳戶，您可以在**標示為有風險的使用者**中選取使用者，或選取有風險的登**入**報告中的記錄，以深入瞭解基礎風險偵測。 如果您有**免費**或**基本**的訂用帳戶，則可以查看有風險的使用者和有風險的登入報告，但您看不到基礎風險偵測資訊。

---

**問：如何在登入和有風險的登入報告中計算 IP 位址？**

**答：** IP 位址的發出方式如下：IP 位址與該位址實際所在的電腦之間沒有任何明確的連線。 對應 IP 位址會因為某些因素而變得更加復雜，例如行動提供者和從中央集區發出 IP 位址的 VPN 通常距離實際使用用戶端裝置的位置非常遠。 目前在 Azure AD 報告中，根據追蹤、登錄資料、反向查詢和其他資訊，將 IP 位址轉換為實體位置的效果最佳。 

---

**問 . 風險偵測「登入時偵測到其他風險」會代表嗎？**

**答：** 為讓您深入了解您環境中所有具有風險的登入，「登入時偵測到其他風險」可作為 Azure AD Identity Protection 訂閱者專屬偵測的登入預留位置。

---

## <a name="conditional-access"></a>條件式存取

**問：這項功能有什麼新增功能？**

**答：** 客戶現在可以透過所有登入報告來針對條件式存取原則進行疑難排解。 客戶可以檢查條件式存取狀態，並深入瞭解適用于登入的原則詳細資料，以及每個原則的結果。

**問：如何開始使用？**

**答：** 開始進行之前：

* 巡覽至 [Azure 入口網站](https://portal.azure.com)中的登入報告。
* 按一下您想要進行疑難排解的登入。
* 流覽至 [**條件式存取**] 索引標籤。在這裡，您可以查看所有影響登入的原則，以及每個原則的結果。 
    
**問：條件式存取狀態的所有可能值為何？**

**答：** 條件式存取狀態可以有下列值：

* **不適用**：這表示沒有任何 CA 原則包含範圍中的使用者和應用程式。 
* **成功**：這表示有一個 CA 原則包含範圍中的使用者和應用程式，且成功地符合 CA 原則。 
* **失敗**：這表示有一個 CA 原則包含範圍中的使用者和應用程式，且不符合 CA 原則。 
    
**問：條件式存取原則結果的所有可能值為何？**

**答：** 條件式存取原則可能會產生下列結果：

* **成功**：已成功地符合原則。
* **失敗**：不符合原則。
* **不適用**：這可能是因為不符合原則條件。
* **未啟用**：這是因為原則處於停用狀態。 
    
**問：所有登入報告中的原則名稱與 CA 中的原則名稱不符。因此?**

**答：** 所有登入報告中的原則名稱是以登入時的 CA 原則名稱為基礎。 如果您稍後 (也就是登入之後) 更新原則名稱，這可能會與 CA 中的原則名稱不一致。

**問：我的登入因條件式存取原則而遭到封鎖，但登入活動報告顯示登入成功。因此?**

**答：** 當套用條件式存取時，目前登入報告可能不會顯示 Exchange ActiveSync 案例的準確結果。 在某些情況下，報告中的登入結果會顯示成功的登入，但登入因為條件式存取原則而實際失敗。 
