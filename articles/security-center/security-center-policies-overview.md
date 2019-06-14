---
title: Azure 資訊安全中心設定 | Microsoft Docs
description: 設定 Azure 資訊安全中心設定。
services: security-center
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: f24b1e4a-cc36-4542-b21e-041453cdfcd8
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/3/2018
ms.author: rkarlin
ms.openlocfilehash: ec674641991a1b5a1e0ca92c133be235dd91dfae
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "60703518"
---
# <a name="security-center-settings"></a>資訊安全中心設定
本文提供資訊安全中心的設定概觀。

您可以在 [安全性原則] 下方找到下列設定：

- **資料收集**：決定代理程式佈建和[資料收集](https://docs.microsoft.com/azure/security-center/security-center-enable-data-collection)設定。
- **安全性原則**：決定何者控制資訊安全中心監視和建議。 您可以編輯資訊安全中心的[安全性原則](tutorial-security-policy.md)。 您也可以使用 [Azure 原則](tutorial-security-policy.md)建立新的定義、定義額外的原則，並在管理群組指派原則。 
- **電子郵件通知**：決定安全性連絡人和[電子郵件通知](security-center-provide-security-contact-details.md)設定。
- **定價層**：定義免費或標準[定價選取項目](security-center-pricing.md)。 您選擇的一層將決定範圍中的資源可用的資訊安全中心功能。 您可以指定訂用帳戶及工作區的層。

> [!NOTE]
> 您可以對每個訂用帳戶設定所有這些內容。 針對工作區，您只能設定資料收集和定價層。
>


## <a name="who-can-edit-security-policies"></a>誰可以編輯安全性原則？
資訊安全中心會使用角色型存取控制 (RBAC)，以提供可在 Azure 中指派給使用者、群組與服務的內建角色。 使用者開啟資訊安全中心時，只能看到與自己能夠存取之資源相關的資訊。 這表示使用者獲指派的角色*擁有者*，*參與者*，或*讀取器*資源所屬訂用帳戶。 除了這些角色，有兩個特定的資訊安全中心角色：

- **安全性讀取者**：擁有資訊安全中心的檢視權限 (包括建議、警示、原則和健康情況)，但無法進行變更。
- **安全性管理員**：擁有與*安全性讀取者*相同的檢視權限，而且還可以更新安全性原則，並解除建議和警示。


## <a name="next-steps"></a>後續步驟
在本文中，您已了解 Azure 資訊安全中心安全性原則的相關資訊。 若要深入了解「Azure 資訊安全中心」，請參閱下列文章：

* [在 Azure 資訊安全中心設定安全性原則](tutorial-security-policy.md)：了解如何設定您的 Azure 訂用帳戶的安全性原則。
* [管理 Azure 資訊安全中心的安全性建議](security-center-recommendations.md)：了解資訊安全中心建議如何協助保護您的 Azure 資源。
* [Azure 資訊安全中心的安全性健全狀況監視](security-center-monitoring.md)：了解如何監視 Azure 資源的健全狀況。
* [管理及回應 Azure 資訊安全中心的安全性警示](security-center-managing-and-responding-alerts.md)：了解如何管理和回應安全性警示。
* [使用 Azure 資訊安全中心監視合作夥伴解決方案](security-center-partner-solutions.md)：了解如何監視合作夥伴解決方案的健全狀態。
* [Azure 資訊安全中心資料安全性](security-center-data-security.md)：了解資訊安全中心如何管理和保護資料。
* [Azure 資訊安全中心常見問題](security-center-faq.md)：取得有關使用服務常見問題的答案。
* [Azure 安全性部落格](https://blogs.msdn.com/b/azuresecurity/)：取得最新的 Azure 安全性新聞和資訊。
