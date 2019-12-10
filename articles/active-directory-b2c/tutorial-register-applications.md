---
title: 教學課程：註冊應用程式
titleSuffix: Azure AD B2C
description: 了解如何使用 Azure 入口網站在 Azure Active Directory B2C 中註冊 Web 應用程式。
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 10/16/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 76d38e4b35111cd2c09685653fb4948f98387132
ms.sourcegitcommit: 5b9287976617f51d7ff9f8693c30f468b47c2141
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/09/2019
ms.locfileid: "74950777"
---
# <a name="tutorial-register-an-application-in-azure-active-directory-b2c"></a>教學課程：在 Azure Active Directory B2C 中註冊應用程式

您的[應用程式](active-directory-b2c-apps.md)必須先在您管理的租使用者中註冊，才可以與 Azure Active Directory B2C （Azure AD B2C）互動。 本教學課程示範如何使用 Azure 入口網站註冊 Web 應用程式。

在本文中，您將了解：

> [!div class="checklist"]
> * 註冊 Web 應用程式
> * 建立用戶端密碼

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="prerequisites"></a>必要條件

如果您尚未建立自己的 [Azure AD B2C 租用戶](tutorial-create-tenant.md)，請立即建立一個。 您可以使用現有的 Azure AD B2C 租用戶。

## <a name="register-a-web-application"></a>註冊 Web 應用程式

若要在您的 Azure AD B2C 租用戶中註冊應用程式，您可以使用目前的**應用程式**體驗，或使用新整合的**應用程式註冊 (預覽)** 體驗。 [深入了解新的體驗](https://aka.ms/b2cappregintro)。

#### <a name="applicationstabapplications"></a>[應用程式](#tab/applications/)

1. 選取頂端功能表中的 [目錄 + 訂用帳戶] 篩選，然後選擇包含您租用戶的目錄，以確定您使用的是包含 Azure AD B2C 租用戶的目錄。
1. 選擇 Azure 入口網站左上角的**所有服務**，然後搜尋並選取 **Azure AD B2C**。
1. 選取 [應用程式]，然後選取 [新增]。
1. 輸入應用程式的名稱。 例如，*webapp1*。
1. 針對 [包含 Web 應用程式/Web API] 和 [允許隱含流程]，選取 [是]。
1. 針對**回覆 URL**，請輸入 Azure AD B2C 應傳回您的應用程式所要求任何權杖的端點。 例如，您可以將它設定為在本機接聽 `https://localhost:44316`。 如果還不知道連接埠號碼，您可以輸入預留位置值，之後再變更。

    基於此教學課程的測試目的，您可以將它設定為 `https://jwt.ms`，以顯示要檢查之權杖的內容。 在本教學課程中，請將 [**回復 URL** ] 設定為 `https://jwt.ms`。

    下列限制適用于回復 Url：

    * 回復 URL 的開頭必須是配置 `https`。
    * 回復 URL 會區分大小寫。 其大小寫必須符合您執行中應用程式之 URL 路徑的大小寫。 例如，如果您的應用程式包含作為其路徑 `.../abc/response-oidc`的一部分，請勿在回復 URL 中指定 `.../ABC/response-oidc`。 由於網頁瀏覽器會將路徑視為區分大小寫，因此如果將與 `.../abc/response-oidc` 相關聯的 cookie 重新導向至不相符的 `.../ABC/response-oidc` URL，可能會予以排除。

1. 按一下 [**建立**] 以完成應用程式註冊。

#### <a name="app-registrations-previewtabapp-reg-preview"></a>[應用程式註冊 (預覽)](#tab/app-reg-preview/)

1. 登入 [Azure 入口網站](https://portal.azure.com)。
1. 在頂端功能表中選取 [目錄 + 訂用帳戶] 篩選，然後選取包含您 Azure AD B2C 租用戶的目錄。
1. 在左側功能表中，選取 [Azure AD B2C]。 或者，選取 [所有服務]，然後搜尋並選取 [Azure AD B2C]。
1. 選取 [應用程式註冊 (預覽)]，然後選取 [新增註冊]。
1. 輸入應用程式的 [名稱]。 例如，*webapp1*。
1. 選取**任何組織目錄中的帳戶或任何識別提供者**。
1. 在 [重新**導向 URI**] 底下，選取 [ **Web**]，然後在 [URL] 文字方塊中輸入 `https://jwt.ms`。

    重新導向 URI 是由授權伺服器（在此案例中為 Azure AD B2C，在此範例中為）完成與使用者的互動之後，以及在成功授權時傳送存取權杖或授權碼給使用者的端點。 在生產應用程式中，它通常是您的應用程式執行所在的可公開存取端點，例如 `https://contoso.com/auth-response`。 基於此教學課程的測試目的，您可以將它設定為 [`https://jwt.ms`]，這是 Microsoft 所擁有的 web 應用程式，它會顯示權杖的已解碼內容（權杖的內容永遠不會離開您的瀏覽器）。 在應用程式開發期間，您可以新增應用程式在本機接聽的端點，例如 `https://localhost:5000`。 您可以隨時在已註冊的應用程式中新增及修改重新導向 Uri。

    下列限制適用于重新導向 Uri：

    * 回復 URL 的開頭必須是配置 `https`。
    * 回復 URL 會區分大小寫。 其大小寫必須符合您執行中應用程式之 URL 路徑的大小寫。 例如，如果您的應用程式包含作為其路徑 `.../abc/response-oidc`的一部分，請勿在回復 URL 中指定 `.../ABC/response-oidc`。 由於網頁瀏覽器會將路徑視為區分大小寫，因此如果將與 `.../abc/response-oidc` 相關聯的 cookie 重新導向至不相符的 `.../ABC/response-oidc` URL，可能會予以排除。

1. 在 [權限] 下，選取 [對 openid 與 offline_access 權限授與管理員同意] 核取方塊。
1. 選取 [註冊]。

應用程式註冊完成後，請啟用隱含授與流程：

1. 在 [管理] 底下，選取 [驗證]。
1. 選取 [試用全新體驗] (若顯示的話)。
1. 在 [隱含授與] 底下，同時選取 [存取權杖] 和 [識別碼權杖] 核取方塊。
1. 選取 [儲存]。

* * *

## <a name="create-a-client-secret"></a>建立用戶端密碼

如果您的應用程式會交換權杖的程式碼，您必須建立應用程式密碼。

#### <a name="applicationstabapplications"></a>[應用程式](#tab/applications/)

1. 在 [ **Azure AD B2C 應用**程式] 頁面中，選取您建立的應用程式，例如*webapp1*。
1. 選取 [**金鑰**]，然後選取 [**產生金鑰**]。
1. 選取 [儲存] 以檢視金鑰。 請記下 [應用程式金鑰] 值。 您會使用此值做為應用程式代碼中的應用程式密碼。

#### <a name="app-registrations-previewtabapp-reg-preview"></a>[應用程式註冊 (預覽)](#tab/app-reg-preview/)

1. 在 [ **Azure AD B2C-應用程式註冊（預覽）** ] 頁面中，選取您建立的應用程式，例如*webapp1*。
1. 在 [管理] 下，選取 [憑證和密碼]。
1. 選取 [新增用戶端密碼]。
1. 在 [描述] 方塊中，輸入用戶端密碼的描述。 例如，*clientsecret1*。
1. 在 [到期] 下，選取密碼有效的持續時間，然後選取 [新增]。
1. 記錄密碼的**值**。 您會使用此值做為應用程式代碼中的應用程式密碼。

* * *

## <a name="next-steps"></a>後續步驟

在本文中，您已了解如何：

> [!div class="checklist"]
> * 註冊 Web 應用程式
> * 建立用戶端密碼

接下來，瞭解如何建立使用者流程，讓您的使用者註冊、登入及管理其設定檔。

> [!div class="nextstepaction"]
> [在 Azure Active Directory B2C > 中建立使用者流程](tutorial-create-user-flows.md)
