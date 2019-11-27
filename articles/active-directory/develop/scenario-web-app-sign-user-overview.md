---
title: 登入使用者的 Web 應用程式（總覽）-Microsoft 身分識別平臺
description: 瞭解如何建立可登入使用者的 web 應用程式（總覽）
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/17/2019
ms.author: jmprieur
ms.custom: aaddev, identityplatformtop40
ms.collection: M365-identity-device-management
ms.openlocfilehash: 316ab055a077b251e88421ab26997f8556a6e31f
ms.sourcegitcommit: 8cf199fbb3d7f36478a54700740eb2e9edb823e8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/25/2019
ms.locfileid: "74482502"
---
# <a name="scenario-web-app-that-signs-in-users"></a>案例：登入使用者的 Web 應用程式

瞭解您所需的一切，以建立使用 Microsoft 身分識別平臺來登入使用者的 web 應用程式。

## <a name="prerequisites"></a>先決條件

[!INCLUDE [Prerequisites](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

## <a name="getting-started"></a>快速入門

# <a name="aspnet-coretabaspnetcore"></a>[ASP.NET Core](#tab/aspnetcore)

如果您想要建立登入使用者的第一個可攜（ASP.NET Core） web 應用程式，請遵循此快速入門：

> [!div class="nextstepaction"]
> [快速入門： ASP.NET Core 登入使用者的 web 應用程式](quickstart-v2-aspnet-core-webapp.md)

# <a name="aspnettabaspnet"></a>[ASP.NET](#tab/aspnet)

如果您想要瞭解如何將登入新增至現有的 ASP.NET web 應用程式，請嘗試下列快速入門：

> [!div class="nextstepaction"]
> [快速入門： ASP.NET 可登入使用者的 web 應用程式](quickstart-v2-aspnet-webapp.md)

# <a name="javatabjava"></a>[Java](#tab/java)

如果您是 JAVA 開發人員，請嘗試下列快速入門：

> [!div class="nextstepaction"]
> [快速入門：使用 Microsoft 將登入新增至 JAVA web 應用程式](quickstart-v2-java-webapp.md)

# <a name="pythontabpython"></a>[Python](#tab/python)

如果您使用 Python 進行開發，請嘗試下列快速入門：

> [!div class="nextstepaction"]
> [快速入門：使用 Microsoft 將登入新增至 Python web 應用程式](quickstart-v2-python-webapp.md)

---

## <a name="overview"></a>Overview

您可以將驗證新增至 web 應用程式，讓它可以登入使用者。 新增驗證可讓您的 web 應用程式存取有限的設定檔資訊，以自訂使用者的體驗。 

Web apps 會在網頁瀏覽器中驗證使用者。 在此案例中，web 應用程式會引導使用者的瀏覽器登入，以 Azure Active Directory （Azure AD）。 Azure AD 會透過使用者的瀏覽器傳回登入回應，其中包含有關安全性權杖中使用者的宣告。 登入使用者會利用[OPEN ID Connect](./v2-protocols-oidc.md)標準通訊協定，並透過使用中介軟體連結[庫](scenario-web-app-sign-user-app-configuration.md#libraries-for-protecting-web-apps)來簡化。

![登入使用者的 Web 應用程式](./media/scenario-webapp/scenario-webapp-signs-in-users.svg)

在第二個階段中，您可以讓應用程式代表登入的使用者呼叫 web Api。 下一個階段是不同的案例，您可以在[呼叫 Web api 的 web 應用程式](scenario-web-app-call-api-overview.md)中找到。

> [!NOTE]
> 將登入新增至 web 應用程式時，是關於保護 web 應用程式和驗證使用者權杖，這是**中介軟體**程式庫的功能。 在 .NET 中，此案例尚未需要 Microsoft 驗證程式庫（MSAL），這是關於取得權杖以呼叫受保護的 Api。 當 web 應用程式需要呼叫 web Api 時，會在後續的案例中引進驗證程式庫。

## <a name="specifics"></a>瞭解

- 在應用程式註冊期間，您必須提供一或多個（如果您將應用程式部署至數個位置）回復 Uri。 在某些情況下（ASP.NET 和 ASP.NET Core），您必須啟用 ID 權杖。 最後，您會想要設定登出 URI，讓您的應用程式對登出使用者做出回應。
- 在應用程式的程式碼中，您必須提供 web 應用程式委派登入的許可權。 您可能想要自訂權杖驗證（尤其是在合作夥伴案例中）。
- Web 應用程式支援任何帳戶類型。 如需詳細資訊，請參閱[支援的帳戶類型](v2-supported-account-types.md)。

## <a name="next-steps"></a>後續步驟

# <a name="aspnet-coretabaspnetcore"></a>[ASP.NET Core](#tab/aspnetcore)

> [!div class="nextstepaction"]
> [應用程式註冊](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-sign-user-app-registration?tabs=aspnetcore)

# <a name="aspnettabaspnet"></a>[ASP.NET](#tab/aspnet)

> [!div class="nextstepaction"]
> [應用程式註冊](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-sign-user-app-registration?tabs=aspnet)

# <a name="javatabjava"></a>[Java](#tab/java)

> [!div class="nextstepaction"]
> [應用程式註冊](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-sign-user-app-registration?tabs=java)

# <a name="pythontabpython"></a>[Python](#tab/python)

> [!div class="nextstepaction"]
> [應用程式註冊](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-sign-user-app-registration?tabs=python)

---
