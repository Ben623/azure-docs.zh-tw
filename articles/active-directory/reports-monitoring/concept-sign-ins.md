---
title: Azure Active Directory 入口網站中的登入活動報告 | Microsoft Docs
description: 介紹 Azure Active Directory 入口網站中的登入活動報告
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 4b18127b-d1d0-4bdc-8f9c-6a4c991c5f75
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 07/17/2019
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6121ca6c1636c8839110712310a1b94fe7fada49
ms.sourcegitcommit: 08d3a5827065d04a2dc62371e605d4d89cf6564f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/29/2019
ms.locfileid: "68619226"
---
# <a name="sign-in-activity-reports-in-the-azure-active-directory-portal"></a>Azure Active Directory 入口網站中的登入活動報告

Azure Active Directory (Azure AD) 中的報告架構包含下列元件：

- **活動** 
    - **登入** – 受控應用程式和使用者登入活動的使用情況相關資訊。
    - **稽核記錄** - [稽核記錄](concept-audit-logs.md)可提供使用者和群組管理、受控應用程式和目錄活動的相關系統活動資訊。
- **安全性** 
    - **有風險的登入** - [有風險的登入](concept-risky-sign-ins.md)表示非使用者帳戶合法擁有者的某人嘗試登入。
    - **標幟為有風險的使用者** - [有風險的使用者](concept-user-at-risk.md)表示可能被盜用的使用者帳戶。

本主題提供登入報告的概觀。

## <a name="prerequisites"></a>必要條件

### <a name="who-can-access-the-data"></a>誰可以存取資料？
* 具有安全性系統管理員、安全性讀取者和報告讀取者角色的使用者
* 全域系統管理員
* 此外，任何使用者 (非系統管理員) 都可以存取自己的登入 

### <a name="what-azure-ad-license-do-you-need-to-access-sign-in-activity"></a>您需要哪項 Azure AD 授權才能存取登入活動？
* 租用戶必須要有相關聯的 Azure AD Premium 授權，才能查看活動報告中的所有登入。 請參閱[開始使用 Azure Active Directory Premium](../fundamentals/active-directory-get-started-premium.md) 來升級 Azure Active Directory 版本。 請注意，如果您在升級前沒有任何活動資料，則在升級至進階授權之後，報告需要幾天的時間才會顯示出資料。

## <a name="sign-ins-report"></a>登入報告

使用者登入報告可回答下列問題：

* 使用者的登入模式為何？
* 一週內有多少使用者登入？
* 這些登入的狀態為何？

您可以藉由在 [Azure入口網站](https://portal.azure.com)的 [Azure Active Directory] 刀鋒視窗中，選取 [活動] 區段的 [登入]，來存取登入報告。 請注意，最多可能需要兩個小時，入口網站中才會出現一些登入記錄。

![登入活動](./media/concept-sign-ins/61.png "登入活動")

> [!IMPORTANT]
> 登入報告只會顯示使用者透過使用者名稱和密碼以手動方式登入的**互動式**登入。 非互動式登入 (例如服務對服務驗證) 不會顯示在登入報告中。 

登入記錄的預設清單檢視會顯示︰

- 登入日期
- 相關的使用者
- 使用者已登入的應用程式
- 登入狀態
- 風險偵測的狀態
- 多重要素驗證 (MFA) 需求的狀態

![登入活動](./media/concept-sign-ins/01.png "登入活動")

您可以按一下工具列中的 [資料行] 來自訂清單檢視。

![登入活動](./media/concept-sign-ins/19.png "登入活動")

這可讓您顯示其他欄位，或移除已顯示的欄位。

![登入活動](./media/concept-sign-ins/02.png "登入活動")

選取清單檢視中的項目，即可取得更詳細的資訊。

![登入活動](./media/concept-sign-ins/03.png "登入活動")

> [!NOTE]
> 客戶現在可以透過所有登入報告來針對條件式存取原則進行疑難排解。 藉由按一下登入記錄的 [**條件式存取**] 索引標籤, 客戶可以檢查條件式存取狀態, 並深入探索套用至每個原則登入和結果的原則詳細資料。
> 如需詳細資訊，請參閱[所有登入中的 CA 資訊相關常見問題集](reports-faq.md#conditional-access)。



## <a name="filter-sign-in-activities"></a>篩選登入活動

若要將報告的資料縮小至適合您的層級, 您可以使用 [日期] 欄位作為 [預設篩選] 來篩選登入資料。 此外, Azure AD 提供您可設定的各種額外篩選準則。

![登入活動](./media/concept-sign-ins/04.png "登入活動")

[使用者] 篩選條件可讓您指定您關心的使用者名稱或使用者主體名稱 (UPN)。

[應用程式] 篩選條件可讓您指定您關心的應用程式名稱。

[登入狀態] 篩選條件可讓您選取︰

- 全部
- 成功
- 失敗

**條件式存取**篩選條件可讓您選取登入的 CA 原則狀態：

- 全部
- 未套用
- 成功
- 失敗

[日期] 篩選條件可讓您定義傳回資料的時間範圍。  
可能的值包括：

- 1 個月
- 7 天
- 24 小時
- 自訂時間間隔

當您選取自訂時間範圍時，可以設定開始時間和結束時間。

如果您將其他欄位新增至您的登入檢視，這些欄位就會自動新增至篩選條件清單。 例如，藉由將 [用戶端應用程式] 欄位新增至您的清單，同時也會取得其他篩選選項，可讓您設定下列篩選條件：  
![登入活動](./media/concept-sign-ins/12.png "登入活動")

- **[瀏覽器]**  
    此篩選器會顯示使用瀏覽器流程執行登入嘗試的所有事件。
- **Exchange ActiveSync (支援)**  
    此篩選器會顯示從 iOS、Android 和 Windows Phone 等支援的平臺嘗試 Exchange ActiveSync (EAS) 通訊協定的所有登入嘗試。
- **Exchange ActiveSync (不支援)**  
    此篩選器會顯示從 Linux 散發版本等不支援的平臺嘗試 EAS 通訊協定的所有登入嘗試。
- **Mobile Apps 和桌面用戶端**此篩選器會顯示所有未使用瀏覽器流程的登入嘗試。 這可以是使用任何通訊協定或桌面用戶端應用程式 (例如 Office on Windows 或 MacOS) 的行動應用程式。
  
- **其他用戶端**
    - **IMAP**  
        使用 IMAP 抓取電子郵件的舊版郵件用戶端。
    - **MAPI**  
        Office 2013, 其中 ADAL 已啟用且使用 MAPI。
    - **舊版 Office 用戶端**  
        未啟用 ADAL 且使用 MAPI 的預設設定中的 office 2013, 或已停用 ADAL 的 Office 2016。
    - **提示**  
        使用 POP3 抓取電子郵件的舊版郵件用戶端。
    - **SMTP**  
        使用 SMTP 傳送電子郵件的舊版郵件用戶端。

## <a name="download-sign-in-activities"></a>下載登入活動

如果您想要在 Azure 入口網站以外使用登入活動資料，您可以[下載登入資料](quickstart-download-sign-in-report.md)。 按一下 [**下載**] 可讓您選擇建立最新250000記錄的 CSV 或 JSON 檔案。  

![下載](./media/concept-sign-ins/71.png "下載")

> [!IMPORTANT]
> 您可以下載的記錄數目會受限於 [Azure Active Directory 報告保留原則](reference-reports-data-retention.md)。  


## <a name="sign-ins-data-shortcuts"></a>登入資料捷徑

除了 Azure AD 之外，Azure 入口網站還可提供您登入資料的額外進入點︰

- 身分識別安全性保護概觀
- 使用者人數
- 群組
- 企業應用程式

### <a name="users-sign-ins-data-in-identity-security-protection"></a>身分識別安全性保護中的使用者登入資料

[身分**識別安全性保護**] 總覽頁面中的使用者登入圖會顯示在指定的時段內, 所有使用者的每週登入匯總。 時間週期的預設值是 30 天。

![登入活動](./media/concept-sign-ins/06.png "登入活動")

當您按一下登入圖中的某一天時，會取得當日登入活動的概觀。

登入活動清單中的每一列會顯示：

* 誰已登入？
* 哪個應用程式是登入的目標？
* 登入狀態為何？
* 登入的 MFA 狀態為何？

按一下項目，即可取得有關登入作業的更多詳細資料：

- 使用者 ID
- 使用者
- 使用者名稱
- 應用程式識別碼
- 應用程式
- 用戶端
- Location
- IP 位址
- Date
- 需要 MFA
- 登入狀態

> [!NOTE]
> IP 位址的發出方式如下：IP 位址與該位址實際所在的電腦之間沒有任何明確的連線。 對應 IP 位址之所以複雜，是因為行動提供者和 VPN 會從中央集區發出 IP 位址，而中央集區通常距離用戶端裝置的實際使用位置非常遠。 目前在 Azure AD 報告中，根據追蹤、登錄資料、反向查詢和其他資訊，將 IP 位址轉換為實體位置的效果最佳。

在 [使用者] 頁面上，按一下 [活動] 區段中的 [登入]，即可取得所有使用者登入的完整概觀。

![登入活動](./media/concept-sign-ins/08.png "登入活動")

## <a name="usage-of-managed-applications"></a>受控應用程式的使用情況

利用登入資料以應用程式為主的檢視，您可以回答下列問題︰

* 誰在使用我的應用程式？
* 您的組織中排名前 3 個應用程式為何？
* 我最近已推出一個應用程式。 它的情況為何？

此資料的進入點是在 [企業應用程式] 之下 [概觀] 區段中的最近 30 天報告內您的組織中排名前 3 個應用程式。

![登入活動](./media/concept-sign-ins/10.png "登入活動")

在指定的時段內, 應用程式使用量圖表每週針對前3名應用程式的登入匯總。 時間週期的預設值是 30 天。

![登入活動](./media/concept-sign-ins/47.png "登入活動")

如果您想要，您可以將焦點設在特定的應用程式。

![報告](./media/concept-sign-ins/single_spp_usage_graph.png "報告")

當您按一下應用程式使用圖中的某一天時，您會取得登入活動的詳細清單。

[登入] 選項會提供您的應用程式的所有登入事件的完整概觀。

![登入活動](./media/concept-sign-ins/11.png "登入活動")

## <a name="office-365-activity-logs"></a>Office 365 活動記錄

您可以從[Microsoft 365 系統管理中心](https://docs.microsoft.com/office365/admin/admin-overview/about-the-admin-center)觀看 Office 365 活動記錄。 雖然 Office 365 活動和 Azure AD 活動記錄共用許多目錄資源, 但只有 Microsoft 365 系統管理中心才會提供 Office 365 活動記錄的完整觀點。 

您也可以使用[office 365 管理 api](https://docs.microsoft.com/office/office-365-management-api/office-365-management-apis-overview), 以程式設計方式存取 office 365 活動記錄。

## <a name="next-steps"></a>後續步驟

* [登入活動報告錯誤碼](reference-sign-ins-error-codes.md)
* [Azure AD 資料保留原則](reference-reports-data-retention.md)
* [Azure AD 報告延遲](reference-reports-latencies.md)

