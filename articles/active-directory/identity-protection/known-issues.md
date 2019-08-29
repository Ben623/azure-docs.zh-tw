---
title: Azure Active Directory 的常見問題集與 Identity Protection (已重新整理) 的已知問題 | Microsoft Docs
description: Azure Active Directory 的常見問題集與 Identity Protection (已重新整理) 的已知問題。
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: troubleshooting
ms.date: 01/24/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: fe7125174129752e6d6dbe0e00d01d4f32755333
ms.sourcegitcommit: 07700392dd52071f31f0571ec847925e467d6795
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/28/2019
ms.locfileid: "70126098"
---
# <a name="faqs-and-known-issues-with-identity-protection-refreshed-in-azure-active-directory"></a>Azure Active Directory 的常見問題集與 Identity Protection (已重新整理) 的已知問題

## <a name="dismiss-user-risk-known-issues"></a>解除使用者風險的已知問題

傳統 Identity Protection 中的 [解除使用者風險]會將 Identity Protection (已重新整理) 中使用者風險歷程記錄內的執行者設定為 **Azure AD**。

Identity Protection 中的 [解除使用者風險] 會將 Identity Protection (已重新整理) 中使用者風險歷程記錄內的執行者設定為 **\<管理員名稱並含指向使用者刀鋒視窗的超連結\>** 。

目前有已知問題會造成關閉使用者風險流程延遲。 如果您有「使用者風險原則」，按一下 [解除使用者風險] 的幾分鐘內，此原則將會停止將套用至已解除的使用者。 不過，已知解除使用者的 [風險狀態] 有 UX 重新整理延遲。 因應措施是在瀏覽器層級重新整理頁面，以查看最新的使用者 [風險狀態]。

## <a name="risky-users-report-known-issues"></a>有風險的使用者報告已知問題

[使用者名稱] 欄位上的查詢區分大小寫，[名稱] 欄位上的查詢則為大小寫無從驗證。

切換 [顯示日期為] 會隱藏 [RISK LAST UPDATED] 資料行。 若要讀取資料行，請按一下 [具風險的使用者] 刀鋒視窗頂端的 [資料行]。

關閉 [傳統身分識別保護] 中的**所有事件**, 將風險偵測的狀態設定為 [**已關閉 (已解決)** ]。

## <a name="risky-sign-ins-report-known-issues"></a>有風險的登入報告已知問題

**解決**風險偵測會將狀態設定為已**通過以風險為基礎之原則的 MFA 驅動的使用者**。

## <a name="frequently-asked-questions"></a>常見問題集

### <a name="why-cant-i-set-my-own-risk-levels-for-each-risk-detection"></a>為什麼我無法針對每個風險偵測設定自己的風險層級？

Identity Protection 中的風險層級是以偵測的精確度為基礎，並由我們所監督的機器學習系統技術支援。 若要自訂向使用者呈現哪些體驗，管理員可以在使用者風險原則與登入風險原則中包含/排除特定的使用者/群組。

### <a name="why-does-the-location-of-a-sign-in-not-match-where-the-user-truly-signed-in-from"></a>登入的位置為何與使用者真正登入自的位置不符合？

IP 地理位置對應對整個產業而言是項挑戰。 如果您認為登入報告中所列的位置與實際位置不符，請與支援人員連絡。 

### <a name="how-do-the-feedback-mechanisms-in-identity-protection-work"></a>Identity Protection 中的意見反應機制如何運作？

**確認遭盜用** (登入時) – 告知 Azure AD Identity Protection：登入不是由身分擁有者所執行的，並表示遭到盜用。

- 收到此意見反應時，我們會將登入和使用者風險狀態改為 [確認遭盜用]，並將風險層級改為 [高]。

- 此外，我們會將資訊提供給我們的機器學習系統，以供未來改進風險評定之用。

    > [!NOTE]
    > 如果已補救使用者，請不要按一下 [確認遭盜用]，因為它會將登入與使用者風險狀態改為 [確認遭盜用]，並將風險層級改為 [高]。

**確認安全** (登入時) – 告知 Azure AD Identity Protection：登入是由身分擁有者執行的，不會表示遭到盜用。

- 收到此意見反應時，我們會將登入 (非使用者) 風險狀態改為 [確認安全]，並將風險層級改為 **-** 。

- 此外，我們會將資訊提供給我們的機器學習系統，以供未來改進風險評定之用。

    > [!NOTE]
    > 如果您認為使用者未遭到盜用，請使用使用者層級上的 [解除使用者風險]，而不是使用登入層級上的 [確認安全]。 使用者層級的「**解除使用者」風險**會關閉使用者風險以及所有過去具風險的登入和風險偵測。

### <a name="why-am-i-seeing-a-user-with-a-low-or-above-risk-score-even-if-no-risky-sign-ins-or-risk-detections-are-shown-in-identity-protection"></a>為什麼在 Identity Protection 中未顯示有風險的登入或風險偵測時, 我會看到具有低 (或以上) 風險分數的使用者？

假設使用者的風險是累積的, 而且不會過期, 即使在 Identity Protection 中未顯示最新的具風險登入或風險偵測, 使用者也可能會有低或高於的使用者風險。 如果使用者的唯一惡意活動只是在我們儲存具風險登入和風險偵測詳細資料的時間範圍內, 就會發生這種情況。 我們不會讓使用者風險過期，因為已經知道不良執行者會以盜用的身分識別停留在客戶的環境中超過 140 天，再大肆攻擊。 客戶可以移至 `Azure Portal > Azure Active Directory > Risky users’ report > Click on an at-risk user > Details’ drawer > Risk history tab` 檢閱使用者的風險時間軸，以了解為何使用者會有風險

### <a name="why-does-a-sign-in-have-a-sign-in-risk-aggregate-score-of-high-when-the-detections-associated-with-it-are-of-low-or-medium-risk"></a>當與登入相關聯的偵測其風險為低或中時，為何該登入的「登入風險 (彙總)」分數為「高」？

高彙總風險分數是根據登入的其他特徵而定，或根據為該登入引發了多個偵測的這項事實而定。 相反地，即使與該登入相關聯的偵測為「高」風險，登入的「登入風險 (彙總)」也有可能為「中」。 
