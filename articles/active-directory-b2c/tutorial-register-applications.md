---
title: 教學課程-註冊應用程式-Azure Active Directory B2C
description: 了解如何使用 Azure 入口網站在 Azure Active Directory B2C 中註冊 Web 應用程式。
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 06/07/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 5c46d3153bdc5768836bce198af115f82e8469f3
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "67056289"
---
# <a name="tutorial-register-an-application-in-azure-active-directory-b2c"></a>教學課程：在 Azure Active Directory B2C 中註冊應用程式

[應用程式](active-directory-b2c-apps.md)必須先在您管理的租用戶中完成註冊，才可以與 Azure Active Directory (Azure AD) B2C 互動。 本教學課程示範如何使用 Azure 入口網站註冊 Web 應用程式。

在本文中，您將了解：

> [!div class="checklist"]
> * 註冊 Web 應用程式
> * 建立用戶端密碼

如果您沒有 Azure 訂用帳戶，請在開始前建立 [免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) 。

## <a name="prerequisites"></a>必要條件

如果您尚未建立自己的 [Azure AD B2C 租用戶](tutorial-create-tenant.md)，請立即建立一個。 您可以使用現有的 Azure AD B2C 租用戶。

## <a name="register-a-web-application"></a>註冊 Web 應用程式

1. 按一下頂端功能表中的 [目錄和訂用帳戶]  篩選，然後選擇包含您租用戶的目錄，以確定您使用的是包含 Azure AD B2C 租用戶的目錄。
2. 選擇 Azure 入口網站左上角的 [所有服務]  ，然後搜尋並選取 [Azure AD B2C]  。
3. 選取 [應用程式]  ，然後選取 [新增]  。
4. 輸入應用程式的名稱。 例如，*webapp1*。
5. 針對 [包含 Web 應用程式/Web API]  和 [允許隱含流程]  ，選取 [是]  。
6. 針對**回覆 URL**，請輸入 Azure AD B2C 應傳回您的應用程式所要求任何權杖的端點。 比方說，您可以將它設定為在本機接聽`https://localhost:44316`。 如果還不知道連接埠號碼，您可以輸入預留位置值，之後再變更。

    基於測試目的，例如本教學課程中您可以將它設定為`https://jwt.ms`顯示進行檢查的權杖內容。 本教學課程中，將**回覆 URL**至`https://jwt.ms`。

    回覆 URL 必須以配置 `https` 開頭，而且所有回覆 URL 值必須共用單一 DNS 網域。 例如，如果應用程式的回覆 URL 為 `https://login.contoso.com`，您可以新增它，如這個 URL 所示：`https://login.contoso.com/new`。 或者，您可以參照 `login.contoso.com` 的 DNS 子網域，例如 `https://new.login.contoso.com`。 如果您想要有以 `login-east.contoso.com` 和 `login-west.contoso.com` 作為回覆 URL 的應用程式，您必須依照以下順序新增這些回覆 URL：`https://contoso.com`、`https://login-east.contoso.com`、`https://login-west.contoso.com`。 您可以新增後面兩個，因為它們是第一個回覆 URL (`contoso.com`) 的子網域。

7. 按一下 [建立]  。

## <a name="create-a-client-secret"></a>建立用戶端密碼

如果您的應用程式交換權杖的程式碼，您需要建立應用程式祕密。

1. 在  **Azure AD B2C 應用程式**頁面上，選取您建立應用程式，例如*webapp1*。
2. 選取 **按鍵**，然後選取**產生金鑰**。
3. 選取 [儲存]  以檢視金鑰。 請記下 [應用程式金鑰]  值。 您可以使用此值作為您的應用程式程式碼中的應用程式密碼。

## <a name="next-steps"></a>後續步驟

在本文中，您已了解如何：

> [!div class="checklist"]
> * 註冊 Web 應用程式
> * 建立用戶端密碼

接下來，了解如何建立使用者流程，以讓使用者註冊、 登入，並管理其設定檔。

> [!div class="nextstepaction"]
> [在 Azure Active Directory B2C 中建立使用者流程 >](tutorial-create-user-flows.md)
