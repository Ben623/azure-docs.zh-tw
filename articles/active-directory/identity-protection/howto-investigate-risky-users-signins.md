---
title: 如何在 Azure Active Directory Identity Protection 中調查有風險的使用者和登入記錄 (已重新整理) | Microsoft Docs
description: 了解如何在 Azure Active Directory Identity Protection 中調查有風險的使用者和登入記錄 (已重新整理)。
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: conceptual
ms.date: 01/25/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: c89658e962654f005eaee5ceff220d5fb343e86e
ms.sourcegitcommit: c71306fb197b433f7b7d23662d013eaae269dc9c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/22/2019
ms.locfileid: "68370317"
---
# <a name="how-to-investigate-risky-users-and-sign-ins"></a>如何：調查有風險的使用者和登入記錄 

使用有風險的登入和具風險的使用者報告, 您可以調查並深入瞭解環境中的風險。 透過對具風險登入和使用者進行篩選及排序，您可以深入了解組織中可能的入侵。 

## <a name="risky-users-report"></a>具風險使用者報告

使用具風險使用者報告提供的資訊，您可以找到下列問題的解答：

- 哪些使用者有高風險？
- 哪些使用者具有補救的風險狀態？

此報告的第一個進入點是安全性頁面上的 [調查] 區段。

![具風險使用者報告](./media/howto-investigate-risky-users-signins/01.png)

具風險的使用者報告有預設檢視，顯示：

- 名稱
- 風險狀態
- 風險層級
- 風險詳細資料
- 上次更新的風險
- Type
- 狀態

![具風險使用者報告](./media/howto-investigate-risky-users-signins/03.png)

您可以按一下工具列中的 [資料行] 來自訂清單檢視。

![具風險使用者報告](./media/howto-investigate-risky-users-signins/04.png)

此資料行對話方塊可讓您顯示其他欄位，或移除已顯示的欄位。

按一下清單檢視中的項目，即可取得所有可用的詳細資料 (水平檢視)。

![具風險使用者報告](./media/howto-investigate-risky-users-signins/05.png)

詳細資料檢視會顯示：

- 基本資訊
- 最近的具風險登入
- 未連結至登入的風險事件
- 風險記錄

此外，您可以：

![具風險使用者報告](./media/howto-investigate-risky-users-signins/08.png)

- 檢視所有的登入捷徑，以檢視該使用者的登入報告。
- 檢視所有具風險登入，以檢視已標記為具風險使用者的所有登入。
- 如果您認為使用者的身分識別已遭入侵，請重設使用者密碼。
- 如果您認為使用者的作用中風險事件是誤判，請解除使用者風險。 如需詳細資訊, 請參閱在[Azure AD Identity Protection 中提供風險事件的意見](howto-provide-risk-event-feedback.md)反應一文。

### <a name="filter-risky-users"></a>篩選具風險的使用者

若要將報告的資料縮小至您適用的層級，您可以使用下列預設欄位篩選具風險的使用者資料︰

- 名稱
- 使用者名稱
- 風險狀態
- 風險層級
- Type
- 狀態

![具風險使用者報告](./media/howto-investigate-risky-users-signins/06.png)

[名稱] 篩選條件可讓您指定您想知道的使用者名稱或使用者主體名稱 (UPN)。

[風險狀態] 篩選條件可讓您選取︰

- 有風險
- 已補救
- 已解除

[風險層級] 篩選條件可讓您選取︰

- 高
- 中等
- 低

[類型] 篩選條件可讓您選取︰

- 來賓
- 成員

[狀態] 篩選條件可讓您選取︰

- 已刪除
- 有效

### <a name="download-risky-users-data"></a>下載具風險使用者的資料

如果您想要在 Azure 入口網站之外使用, 您可以下載具風險使用者資料。 按一下 [下載], 會建立最新2500記錄的 CSV 檔案。 

![具風險使用者報告](./media/howto-investigate-risky-users-signins/07.png)

您可以按一下工具列中的 [資料行]，以自訂清單檢視。
 
這可讓您顯示其他欄位，或移除已顯示的欄位。
 
若要深入了解具風險的使用者，請按一下 [詳細資料] 隱藏式選單以展開資料

## <a name="risky-sign-ins-report"></a>有風險的登入報告

使用具風險登入報告提供的資訊，您可以找到下列問題的解答：

- 上週有多少成功登入具有匿名 IP 位址風險事件？
- 上個月有哪些使用者確認遭受入侵？
- 哪些使用者對於 Office 365 入口網站屬於風險登入？

此報告的第一個進入點是安全性頁面上的 [調查] 區段。

![有風險的登入報告](./media/howto-investigate-risky-users-signins/02.png)

具風險的登入報告有預設檢視，顯示：

- Date
- 使用者
- 應用程式
- 登入狀態
- 風險狀態
- 風險層級 (彙總)
- 風險層級 (即時)
- 條件式存取
- 需要 MFA  

![有風險的登入報告](./media/howto-investigate-risky-users-signins/09.png)

您可以按一下工具列中的 [資料行] 來自訂清單檢視。

![具風險使用者報告](./media/howto-investigate-risky-users-signins/11.png)

此資料行對話方塊可讓您顯示其他欄位，或移除已顯示的欄位。

按一下清單檢視中的項目，即可取得所有可用的詳細資料 (水平檢視)。

![具風險使用者報告](./media/howto-investigate-risky-users-signins/12.png)

詳細資料檢視會顯示：

- 基本資訊
- 裝置資訊
- 風險資訊
- MFA 資訊
- 條件式存取

此外，您可以：

![具風險使用者報告](./media/howto-investigate-risky-users-signins/13.png)

- 確認遭入侵 
- 確認安全

如需詳細資訊, 請參閱在[Azure AD Identity Protection 中提供風險事件的意見](howto-provide-risk-event-feedback.md)反應一文。

### <a name="filter-risky-sign-ins"></a>篩選具風險登入

若要將報告的資料縮小至您適用的層級，您可以使用下列預設欄位篩選具風險的使用者資料︰

- 使用者
- 應用程式
- 登入狀態
- 風險狀態
- 風險層級 (彙總)
- 風險層級 (即時)
- 條件式存取
- Date
- 風險層級類型

![有風險的登入報告](./media/howto-investigate-risky-users-signins/14.png)

[名稱] 篩選條件可讓您指定您想知道的使用者名稱或使用者主體名稱 (UPN)。

[應用程式] 篩選條件可讓您指定使用者嘗試存取的雲端應用程式。

[登入狀態] 篩選條件可讓您選取︰

- 所有
- 成功
- 失敗

[風險狀態] 篩選條件可讓您選取︰

- 有風險
- 已確認遭入侵
- 已確認安全
- 已解除
- 已補救

[風險層級 (彙總)] 篩選條件可讓您選取︰

- 高
- 中等
- 低

[風險層級 (即時)] 篩選條件可讓您選取︰

- 高
- 中等
- 低

**條件式存取**篩選器可讓您選取:

- 所有
- 不適用
- 成功
- 失敗

[日期] 篩選條件可讓您定義傳回資料的時間範圍。
可能的值包括：

- 前 1 個月
- 最近 7 天
- 最近 24 小時
- 自訂時間間隔

### <a name="download-risky-sign-ins-data"></a>下載具風險的登入資料

如果您想要在 Azure 入口網站外部使用有風險的登入資料, 您可以下載該資料。 按一下 [下載], 會建立最新2500記錄的 CSV 檔案。 

![具風險使用者報告](./media/howto-investigate-risky-users-signins/15.png)

## <a name="next-steps"></a>後續步驟

若要取得 Azure AD Identity Protection 的概觀，請參閱 [Azure AD Identity Protection 概觀](overview-v2.md)。
