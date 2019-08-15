---
title: 安裝 Azure AD Power BI 內容套件 | Microsoft Docs
description: 深入了解如何安裝 Azure AD Power BI 內容套件
services: active-directory
documentationcenter: ''
author: cawrites
manager: daveba
ms.assetid: fd5604eb-1334-4bd8-bfb5-41280883e2b5
ms.service: active-directory
ms.workload: identity
ms.subservice: report-monitor
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 11/13/2018
ms.author: chadam
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: f088c8b49fa6485a21b630738149078b7ff45b7d
ms.sourcegitcommit: 5b76581fa8b5eaebcb06d7604a40672e7b557348
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/13/2019
ms.locfileid: "68988110"
---
# <a name="quickstart-install-azure-active-directory-power-bi-content-pack"></a>快速入門：安裝 Azure Active Directory Power BI 內容套件

|  |
|--|
|目前，Azure AD Power BI 內容套件會使用 Azure AD Graph API 從您的 Azure AD 租用戶擷取資料。 因此，您可能會發現內容套件中的可用資料與使用 [Microsoft Graph API](concept-reporting-api.md) 擷取以用於報告的資料之間有些許差異。 |
|  |

Azure Active Directory (Azure AD) 的 Power BI 內容套件可讓您以視覺化方式檢視來自環境的報告資料。 您可以下載預先建立的內容套件，並且使用它來利用 Power BI 提供的視覺效果體驗，報告目錄中的所有活動。 您也可以建立自己的儀表板，並輕鬆地與組織中所有人共用。 

在本快速入門中，您會深入了解如何安裝內容套件。

## <a name="prerequisites"></a>必要條件

若要完成本快速入門，您需要：

* Power BI 帳戶。 這是與您 O365 或 Azure AD 帳戶相同的帳戶。 
* 您的 Azure AD 租用戶識別碼。 這是從 Azure 入口網站[內容頁面](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Properties)取得的**目錄識別碼**。
* Azure AD Premium (P1/P2) 授權。 請參閱[開始使用 Azure Active Directory Premium](../fundamentals/active-directory-get-started-premium.md) 來升級 Azure Active Directory 版本。

## <a name="install-azure-ad-power-bi-content-pack"></a>安裝 Azure AD Power BI 內容套件 

1. 使用您的 Power BI 帳戶登入 [Power BI](https://app.powerbi.com/groups/me/getdata/services)。 這是與您 O365 或 Azure AD 帳戶相同的帳戶。

2. 在 [應用程式]  頁面中搜尋 **Azure Active Directory 活動記錄**，然後選取 [立即取得]  。 

   ![Azure Active Directory Power BI 內容套件](./media/quickstart-install-power-bi-content-pack/getitnow.png) 
    
3. 在快顯視窗中輸入您的 Azure AD 租用戶識別碼，針對要查詢的天數輸入 **7**，然後選取 [下一步]  。
    
   ![Azure Active Directory Power BI 內容套件](./media/quickstart-install-power-bi-content-pack/connect.png) 

4. 建立好 Azure Active Directory 活動記錄儀表板後，選取該儀表板。

   ![Azure Active Directory Power BI 內容套件](./media/quickstart-install-power-bi-content-pack/dashboard.png) 
    
## <a name="next-steps"></a>後續步驟

* [使用 Power BI 內容套件](howto-power-bi-content-pack.md)。
* [針對內容套件錯誤進行疑難排解](troubleshoot-content-pack.md)。
