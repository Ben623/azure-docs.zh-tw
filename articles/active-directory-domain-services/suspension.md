---
title: Azure AD Domain Services 中的擱置網域 |Microsoft Docs
description: 瞭解 Azure AD DS 受控網域的不同健全狀況狀態，以及如何還原已擱置的網域。
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 95e1d8da-60c7-4fc1-987d-f48fde56a8cb
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 09/27/2019
ms.author: iainfou
ms.openlocfilehash: 8a82d2ad3e79633bb930348c6162996e961c4306
ms.sourcegitcommit: f15f548aaead27b76f64d73224e8f6a1a0fc2262
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/26/2020
ms.locfileid: "77612957"
---
# <a name="understand-the-health-states-and-resolve-suspended-domains-in-azure-active-directory-domain-services"></a>瞭解健全狀況狀態，並在 Azure Active Directory Domain Services 中解決已擱置的網域

當 Azure Active Directory Domain Services (Azure AD DS) 有很長的一段時間無法服務受控網域時，其會使該受控網域進入暫時停權狀態。 如果受控網域仍處於暫停狀態，則會自動刪除。 若要讓您的 Azure AD DS 受控網域保持良好狀態，並避免暫停，請儘快解決所有警示。

本文說明受控網域的暫止原因，以及如何復原已暫停的網域。

## <a name="overview-of-managed-domain-states"></a>受控網域狀態的總覽

透過 Azure AD DS 受控網域的生命週期，會有不同的狀態指出其健康狀態。 如果受控網域回報問題，請快速解決根本原因，讓狀態停止，使其無法繼續下降。

![Azure AD DS 受控網域所需的狀態進展](media/active-directory-domain-services-suspension/suspension-timeline.PNG)

Azure AD DS 受控網域可能處於下列其中一種狀態：

* [執行中](#running-state)
* [需要注意](#needs-attention-state)
* [已暫停](#suspended-state)
* [已刪除](#deleted-state)

## <a name="running-state"></a>執行狀態

已正確設定並在沒有問題的情況下執行的 Azure AD DS 受控網域會處於 [*正在*執行] 狀態。 這是受控網域所需的狀態。

### <a name="what-to-expect"></a>未來展望

* Azure 平臺可以定期監視受控網域的健全狀況。
* 受控網域的網域控制站會定期進行修補和更新。
* Azure Active Directory 的變更會定期同步處理至受控網域。
* 系統會針對受控網域執行定期備份。

## <a name="needs-attention-state"></a>需要注意狀態

具有一或多個需要修正之問題的 Azure AD DS 受控網域會處於*需要注意*狀態。 受控網域的健康情況頁面會列出警示，並指出發生問題的位置。 某些警示是暫時性的，而且會由 Azure 平臺自動解決。 針對其他警示，您可以遵循所提供的解決步驟來修正問題。 這是重大警示，請[開啟 Azure 支援要求][azure-support]以取得額外的疑難排解協助。

警示的其中一個範例是有限制的網路安全性群組。 在此設定中，Azure 平臺可能無法更新及監視受控網域。 系統會產生警示，且狀態會變更為 [*需要注意*]。

如需詳細資訊，請參閱[如何針對 AZURE AD DS 受控網域的警示進行疑難排解][resolve-alerts]。

### <a name="what-to-expect"></a>未來展望

當 Azure AD DS 受控網域處於「*需要注意*」狀態時，Azure 平臺可能無法定期監視、修補、更新或備份資料。 在某些情況下（例如，使用不正確網路設定），可能無法連線到受控網域的網域控制站。

* 受控網域處於狀況不良的狀態，而持續的健康情況監視可能會停止，直到警示解決為止。
* 無法修補或更新受控網域的網域控制站。
* Azure Active Directory 的變更可能不會同步處理至受控網域。
* 可能不會採用受控網域的備份。
* 如果您解決影響受控網域的非重大警示，健康情況應會回到執行*中狀態。*
* 針對 Azure 平臺無法連線到網域控制站的設定問題，會觸發重大警示。 如果這些重大警示未在15天內解決，受控網域就會進入「已*暫停*」狀態。

## <a name="suspended-state"></a>暫止狀態

基於下列其中一個原因，Azure AD DS 受控網域進入**暫停**狀態：

* 有一或多個重大警示未在 15 天內解決。
    * 重大警示可能是因為設定錯誤而使 Azure AD DS 所需資源的存取遭到封鎖而導致的。 例如，受控網域中的 [AADDS104：網路錯誤][alert-nsg]警示並未在 15 天內解決。
* Azure 訂用帳戶有帳單問題，或 Azure 訂用帳戶已過期。

當 Azure 平臺無法管理、監視、修補或備份網域時，受控網域就會暫停。 受控網域會維持在*暫停*狀態15天。 若要維護受控網域的存取權，請立即解決重大警示。

### <a name="what-to-expect"></a>未來展望

當 Azure AD DS 受控網域處於「已*暫停*」狀態時，會發生下列行為：

* 受控網域的網域控制站已解除布建，且無法在虛擬網路內連線。
* 安全 LDAP 透過網際網路存取受控網域（如果啟用）會停止運作。
* 對受控網域進行驗證、登入已加入網域的 Vm，或透過 LDAP/LDAPS 進行連線時發生失敗。
* 已不再採用受控網域的備份。
* 與 Azure AD 的同步處理會停止。

### <a name="how-do-you-know-if-your-managed-domain-is-suspended"></a>如何得知您的受控網域已被暫時停權？

您會在 Azure 入口網站中的 [Azure AD DS 健全狀況] 頁面上看到[警示][resolve-alerts]，指出網域已暫止。 網域的狀態也會顯示 [已*暫停*]。

### <a name="restore-a-suspended-domain"></a>還原暫時停權的網域

若要還原處于「已*暫停*」狀態之 Azure AD DS 受控網域的健康情況，請完成下列步驟：

1. 在 Azure 入口網站中，搜尋並選取 **網域服務**。
1. 從清單中選擇您的 Azure AD DS 受控網域（例如*aaddscontoso.com*），然後選取 [**健康**情況]。
1. 視暫停的原因而定，選取警示，例如*AADDS503*或*AADDS504*。
1. 選擇警示中提供的解決連結，並遵循步驟來解決此問題。

受控網域只能還原到上次備份的日期。 上次備份的日期會顯示在受控網域的 [**健全狀況**] 頁面上。 在上次備份之後發生的任何變更都將無法還原。 受控網域的備份最多儲存 30 天。 超過 30 天的備份會被刪除。

當您在受控網域處於「已*暫停*」狀態時解決警示之後，請[開啟 Azure 支援要求][azure-support]回到狀況良好狀態。 如果備份時間少於30天，Azure 支援可以還原受控網域。

## <a name="deleted-state"></a>已刪除狀態

如果 Azure AD DS 受控網域處於*暫停*狀態15天，則會被刪除。 此進程無法復原。

### <a name="what-to-expect"></a>未來展望

當 Azure AD DS 受控網域進入*已刪除*狀態時，會看到下列行為：

* 該受控網域的所有資源和備份都會被刪除。
* 您無法還原受控網域，且必須建立替代的受控網域，才能重複使用 Azure AD DS。
* 將它刪除之後，您就不需繼續支付該受控網域的費用。

## <a name="next-steps"></a>後續步驟

若要讓您的 Azure AD DS 受控網域保持良好狀態，並將它暫停的風險降到最低，請瞭解如何[解決受控網域的警示][resolve-alerts]。

<!-- INTERNAL LINKS -->
[alert-nsg]: alert-nsg.md
[azure-support]: ../active-directory/fundamentals/active-directory-troubleshooting-support-howto.md
[resolve-alerts]: troubleshoot-alerts.md
