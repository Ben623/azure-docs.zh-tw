---
title: 將 Azure AD Identity Protection 資料連線到 Azure Sentinel |Microsoft Docs
description: 瞭解如何將 Azure AD Identity Protection 資料連線到 Azure Sentinel。
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.assetid: 91c870e5-2669-437f-9896-ee6c7fe1d51d
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/17/2019
ms.author: rkarlin
ms.openlocfilehash: 5d73ce12d509b2ccc534e19a27124980f3b60044
ms.sourcegitcommit: 28688c6ec606ddb7ae97f4d0ac0ec8e0cd622889
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/18/2019
ms.locfileid: "74158894"
---
# <a name="connect-data-from-azure-ad-identity-protection"></a>從 Azure AD Identity Protection 連接資料



您可以將[Azure AD Identity Protection](https://docs.microsoft.com/azure/information-protection/reports-aip)的記錄串流至 Azure Sentinel，以將警示串流至 Azure Sentinel 以查看儀表板、建立自訂警示，以及改善調查。 Azure Active Directory Identity Protection 提供有風險的使用者、風險偵測和弱點的匯總視圖，並能夠立即補救風險，以及設定原則來自動補救未來的事件。 這項服務是以 Microsoft 的經驗為基礎來保護取用者身分識別，並從一天超過13000000000個登入的信號獲得極高的精確度。 


## <a name="prerequisites"></a>先決條件

- 您必須擁有[Azure Active Directory Premium P1 或 P2 授權](https://azure.microsoft.com/pricing/details/active-directory/)
- 具有全域管理員或安全性系統管理員許可權的使用者


## <a name="connect-to-azure-ad-identity-protection"></a>連接到 Azure AD Identity Protection

如果您已經有 Azure AD Identity Protection，請確定已[在您的網路上啟用該功能](../active-directory/identity-protection/overview-identity-protection.md)。
如果 Azure AD Identity Protection 已部署並取得資料，則可以輕鬆地將警示資料串流至 Azure Sentinel。


1. 在 Azure Sentinel 中，選取 [**資料連線器**]，然後按一下 [ **Azure AD Identity Protection** ] 磚。

2. 按一下 **[連接]** 開始將 Azure AD Identity Protection 事件串流至 Azure Sentinel。


6. 若要在 Log Analytics 中針對 Azure AD Identity Protection 警示使用相關的架構，請搜尋**SecurityAlert**。

## <a name="next-steps"></a>後續步驟
在本檔中，您已瞭解如何將 Azure AD Identity Protection 連接到 Azure Sentinel。 若要深入了解 Azure Sentinel，請參閱下列文章：
- 瞭解如何[查看您的資料和潛在威脅](quickstart-get-visibility.md)。
- 開始[使用 Azure Sentinel 偵測威脅](tutorial-detect-threats-built-in.md)。
