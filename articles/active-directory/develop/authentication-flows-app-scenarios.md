---
title: Microsoft 身分識別平台驗證流程和應用程式案例 | Azure
description: 瞭解 Microsoft 身分識別平臺的應用程式案例，包括驗證身分識別、取得權杖，以及呼叫受保護的 Api。
services: active-directory
author: jmprieur
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 03/03/2020
ms.author: jmprieur
ms.custom: aaddev, identityplatformtop40, scenarios:getting-started
ms.openlocfilehash: 89bafeb077fc83f4f3165d591006831bf8287875
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/13/2020
ms.locfileid: "79263019"
---
# <a name="authentication-flows-and-application-scenarios"></a>驗證流程和應用程式情節

Microsoft 身分識別平台 (v2.0) 端點支援各種新式應用程式架構的驗證。 所有架構都是以業界標準通訊協定 [OAuth 2.0 和 OpenID Connect](active-directory-v2-protocols.md) 為基礎。  使用[Microsoft 身分識別平臺驗證程式庫](reference-v2-libraries.md)，應用程式會驗證識別，並取得權杖以存取受保護的 api。

本文說明驗證流程，以及它們在中使用的應用程式案例：

- [應用程式案例與支援的驗證流程](#scenarios-and-supported-authentication-flows)。
- [應用程式案例與支援的平台和語言](#scenarios-and-supported-platforms-and-languages)。

## <a name="application-categories"></a>應用程式類別

您可以從數種類型的應用程式取得權杖，包括：

- Web 應用程式
- 行動應用程式
- 傳統型應用程式
- Web API

您也可以從沒有瀏覽器或在 IoT 上執行的裝置上執行的應用程式取得權杖。

應用程式的分類如下列清單所示：

- [受保護的資源與用戶端應用程式](#protected-resources-vs-client-applications)：某些案例是關於保護資源（例如 web 應用程式或 web api）。 其他案例則是關於取得安全性權杖來呼叫受保護的 Web API。
- [使用者或不使用使用者](#with-users-or-without-users)：某些案例牽涉到登入的使用者，但其他情況（例如背景程式案例）則不會牽涉到使用者。
- [單一頁面、公用用戶端和機密用戶端應用程式](#single-page-public-client-and-confidential-client-applications)：這些類型是三個大型的應用程式類別。 每種都會搭配不同的程式庫和物件使用。
- 登[入物件](v2-supported-account-types.md#certain-authentication-flows-dont-support-all-the-account-types)：可用的驗證流程會根據登入物件而有所不同。 有些流程僅適用於公司或學校帳戶。 而有些則同時適用於公司或學校帳戶以及個人 Microsoft 帳戶。 允許的對象取決於驗證流程。
- [支援的 OAuth 2.0 流程](#scenarios-and-supported-authentication-flows)：驗證流程是用來執行要求權杖的應用程式案例。 應用程式案例與驗證流程之間不會有一對一的對應。
- [支援的平臺](#scenarios-and-supported-platforms-and-languages)：並非所有應用程式案例都適用于每個平臺。

### <a name="protected-resources-vs-client-applications"></a>受保護的資源與用戶端應用程式

驗證情節牽涉到兩個活動：

- 取得**受保護 Web API 的安全性權杖**：建議您使用[microsoft 支援的用戶端程式庫](reference-v2-libraries.md#microsoft-supported-client-libraries)來取得權杖，特別是 microsoft 驗證程式庫（MSAL）系列。
- **保護 Web API 或 web 應用程式**：保護 Web API 或 web 應用程式資源的一項挑戰是驗證安全性權杖。 在某些平台上，Microsoft 提供會[中介軟體程式庫](reference-v2-libraries.md#microsoft-supported-server-middleware-libraries)。

### <a name="with-users-or-without-users"></a>有使用者或沒有使用者

大部分的驗證案例都會代表登入的使用者取得權杖。

![有使用者的案例](media/scenarios/scenarios-with-users.svg)

不過，也有一些精靈應用程式案例，其中應用程式會代表其本身 (不含使用者) 取得權杖。

![有精靈應用程式的案例](media/scenarios/daemon-app.svg)

### <a name="single-page-public-client-and-confidential-client-applications"></a>單頁、公用用戶端和機密用戶端應用程式

您可以從多種應用程式類型取得安全性權杖。 這些應用程式通常分成三種類別：

- **單頁應用程式**（也稱為 spa）：這些應用程式是 web 應用程式，其中會從在瀏覽器中執行的 JavaScript 或 TypeScript 應用程式取得權杖。 許多新式應用程式都有一個單頁應用程式前端，主要是以 JavaScript 撰寫。 此應用程式通常使用 Angular、React 或 Vue 之類的架構。 MSAL.js 是唯一支援單頁應用程式的 Microsoft 驗證程式庫。

- **公用用戶端應用程式**：這些應用程式一律會登入使用者：
  - 代表已登入使用者呼叫 Web API 的傳統型應用程式
  - 行動應用程式
  - 在沒有瀏覽器的裝置上執行的應用程式，例如在 iOT 上執行的應用程式

  這些應用程式是以 MSAL [PublicClientApplication](/dotnet/api/microsoft.identity.client.publicclientapplication) 類別表示。 如需詳細資訊，請參閱[公用用戶端和機密用戶端應用程式](msal-client-applications.md)。

- **機密用戶端應用程式**：
  - 呼叫 Web API 的 Web 應用程式
  - 呼叫 Web API 的 Web API
  - 精靈應用程式 (即使以 Linux 精靈或 Windows 服務之類的主控台服務形式實作也是如此)

  這些類型的應用程式會使用 [ConfidentialClientApplication](/dotnet/api/microsoft.identity.client.confidentialclientapplication) 類別。 如需詳細資訊，請參閱[公用用戶端和機密用戶端應用程式](msal-client-applications.md)。

## <a name="application-scenarios"></a>應用程式案例

Microsoft 身分識別平臺端點支援不同應用程式架構的驗證：

- 單一頁面應用程式
- Web 應用程式
- Web API
- 行動應用程式
- 原生應用程式
- 精靈應用程式
- 伺服器端應用程式

應用程式會使用不同驗證流程來登入使用者，並取得權杖以呼叫受保護的 API。

### <a name="a-single-page-application"></a>單頁應用程式

許多新式 web 應用程式都是以用戶端單一頁面應用程式的形式建立。 這些應用程式使用 JavaScript 或單一頁面應用程式架構，例如角度、Vue 和回應 .js。 這些應用程式會在網頁瀏覽器中執行。

其驗證特性與傳統的伺服器端 web 應用程式不同。 使用 Microsoft 身分識別平台，單頁應用程式即可登入使用者，並取得權杖來存取後端服務或 Web API。

![單頁應用程式](media/scenarios/spa-app.svg)

如需詳細資訊，請參閱[單頁應用程式](scenario-spa-overview.md)。

### <a name="a-web-app-that-is-signing-in-a-user"></a>正在登入使用者的 Web 應用程式

![登入使用者的 Web 應用程式](media/scenarios/scenario-webapp-signs-in-users.svg)

若要保護正在登入使用者的 Web 應用程式：

- 如果您在 .NET 中開發，您可使用 ASP.NET 或 ASP.NET Core 搭配 ASP.NET Open ID Connect 中介軟體使用。 保護資源牽涉到驗證安全性權杖，這是由[適用於 .NET 的 IdentityModel 擴充功能](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet/wiki)程式庫 (而非 MSAL 程式庫) 所完成。

- 如果您以 .Node.js 進行開發，您會使用 Passport.js。

如需詳細資訊，請參閱[登入使用者的 Web 應用程式](scenario-web-app-sign-user-overview.md)。

### <a name="a-web-app-that-signs-in-a-user-and-calling-a-web-api-on-behalf-of-the-user"></a>登入使用者並代表使用者呼叫 Web API 的 Web 應用程式

![呼叫 Web API 的 Web 應用程式](media/scenarios/web-app.svg)

若要代表使用者從 Web 應用程式呼叫 Web API，請使用 MSAL **ConfidentialClientApplication** 類別。 您可使用授權碼流程，將取得的權杖儲存在權杖快取中。 如有需要，MSAL 會重新整理權杖，而控制器會以無訊息方式從快取中取得權杖。

如需詳細資訊，請參閱[呼叫 Web API 的 Web 應用程式](scenario-web-app-call-api-overview.md)。

### <a name="a-desktop-app-calling-a-web-api-on-behalf-of-a-signed-in-user"></a>代表登入的使用者呼叫 Web API 的傳統型應用程式

若要讓傳統型應用程式呼叫可登入使用者的 Web API，請使用 MSAL **PublicClientApplication** 類別的互動式權杖取得方法。 您可以使用這些互動式方法來控制登入 UI 體驗。 MSAL 會在此互動中使用網頁瀏覽器。

![呼叫 Web API 的傳統型應用程式](media/scenarios/desktop-app.svg)

在加入 Windows 網域或 Azure Active Directory (Azure AD) 的電腦上，Windows 裝載應用程式還有另一種可能性。 這些應用程式可透過使用[整合式 Windows 驗證](https://aka.ms/msal-net-iwa)，以無訊息方式取得權杖。

在沒有瀏覽器的裝置上執行的應用程式，仍然可以代表使用者呼叫 API。 若要進行驗證，使用者必須登入具有網頁瀏覽器的其他裝置。 此案例需要您使用裝置程式[代碼流程](https://aka.ms/msal-net-device-code-flow)。

![裝置程式碼流程](media/scenarios/device-code-flow-app.svg)

雖然我們不建議您使用它，但是在公用用戶端應用程式中可以使用[使用者名稱/密碼流程](https://aka.ms/msal-net-up)。 在某些案例 (例如 DevOps) 中，仍然需要此流程。

但使用此流程會限制您的應用程式。 例如，應用程式無法登入需要使用多重要素驗證或條件式存取的使用者。 您的應用程式也不會受益於單一登入。

以使用者名稱/密碼流程進行驗證違反了新式驗證的原則，僅供舊版的理由之用。

在桌面應用程式中，如果您想要保留權杖快取，請自訂權杖快取[序列化](https://aka.ms/msal-net-token-cache-serialization)。 藉由執行[雙重權杖](https://aka.ms/msal-net-dual-cache-serialization)快取序列化，您可以使用與舊版相容和向前相容的權杖快取。 這些權杖支援先前的驗證程式庫層代。 特定程式庫包括適用於 .NET 的 Azure AD 驗證程式庫 (ADAL.NET) 第 3 版和第 4 版。

如需詳細資訊，請參閱[呼叫 Web API 的傳統型應用程式](scenario-desktop-overview.md)。

### <a name="a-mobile-app-calling-a-web-api-on-behalf-of-an-interactive-user"></a>代表互動式使用者呼叫 Web API 的行動應用程式

類似於傳統型應用程式，行動應用程式會呼叫 MSAL **PublicClientApplication** 類別的互動式權杖取得方法，以取得用於呼叫 Web API 的權杖。

![呼叫 Web API 的行動應用程式](media/scenarios/mobile-app.svg)

MSAL iOS 和 MSAL Android 預設會使用系統網頁瀏覽器。 不過，您也可以指示它們使用內嵌的網頁檢視。 Specificities 相依于行動平臺：通用 Windows 平臺（UWP）、iOS 或 Android。

某些案例（例如牽涉到與裝置識別碼或裝置註冊相關的條件式存取），需要在裝置上安裝訊息代理程式。 訊息代理程式的範例包括 Android 上的 Microsoft 公司入口網站以及 Android 與 iOS 上的 Microsoft Authenticator。 MSAL 現在可以與訊息代理程式互動。 如需詳細資訊，請參閱[在 Android 和 iOS 上運用代理](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/leveraging-brokers-on-Android-and-iOS)程式。

如需詳細資訊，請參閱[呼叫 Web API 的行動裝置應用程式](scenario-mobile-overview.md)。

> [!NOTE]
> 在 Xamarin 上使用 MSAL.iOS、MSAL.Android 或 MSAL.NET 的行動應用程式，可以套用應用程式保護原則。 比方說，這些原則可能讓使用者無法複製受保護的文字。 行動裝置應用程式由 Intune 管理，並由 Intune 辨識為受管理的應用程式。 如需詳細資訊，請參閱[Microsoft Intune APP SDK 總覽](https://docs.microsoft.com/intune/app-sdk)。
>
> [Intune 應用程式 SDK](https://docs.microsoft.com/intune/app-sdk-get-started) 獨立於 MSAL 程式庫之外，而且會自行與 AAD 互動。

### <a name="a-protected-web-api"></a>受保護的 Web API

您可以使用 Microsoft 身分識別平臺端點來保護 web 服務，例如應用程式的 RESTful Web API。 使用存取權杖呼叫受保護的 Web API。 權杖會保護 API 的資料，並驗證傳入的要求。 Web API 的呼叫者會在 HTTP 要求的授權標頭尾部附加存取權杖。

如果您要保護 ASP.NET 或 ASP.NET Core Web API，則需要驗證存取權杖。 在此驗證中，您會使用 ASP.NET JWT 中介軟體。 驗證是由[適用於 .NET 的 IdentityModel 擴充功能](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet/wiki)程式庫 (而不是 MSAL.NET) 完成。

如需詳細資訊，請參閱[受保護的 Web API](scenario-protected-web-api-overview.md)。

### <a name="a-web-api-calling-another-web-api-on-behalf-of-a-user"></a>代表使用者呼叫另一個 Web API 的 Web API

若要讓 ASP.NET 或 ASP.NET Core 受保護的 Web API 代表使用者呼叫另一個 Web API，您的應用程式需要取得下游 Web API 的權杖。 若要取得權杖，您的應用程式會呼叫**ConfidentialClientApplication**類別的[AcquireTokenOnBehalfOf](https://aka.ms/msal-net-on-behalf-of)方法。 這類呼叫也稱為「*服務對服務*呼叫」。 呼叫其他 Web API 的 Web API 需要提供自訂快取序列化。

  ![呼叫另一個 Web API 的 Web API](media/scenarios/web-api.svg)

如需詳細資訊，請參閱[呼叫 Web API 的 Web API](scenario-web-api-call-api-overview.md)。

### <a name="a-daemon-app-calling-a-web-api-in-the-daemons-name"></a>以精靈的名稱呼叫 Web API 的精靈應用程式

應用程式如果具有長時間執行的程序，或其運作方式不需要使用者互動，就也需要一個存取受保護的 Web API 的方法。 這類應用程式可以使用應用程式的身分識別來驗證和取得權杖。 應用程式會使用用戶端秘密或憑證來證明其身分識別。

您可以使用 MSAL **ConfidentialClientApplication** 類別的[用戶端認證](https://aka.ms/msal-net-client-credentials)取得方法撰寫此類精靈應用程式，以取得呼叫應用程式的權杖。 這些方法要求呼叫應用程式已向 Azure AD 註冊秘密。 然後應用程式會與所呼叫的精靈共用此秘密。 這類秘密的範例包括應用程式密碼、憑證判斷提示或用戶端判斷提示。

![其他應用程式和 API 所呼叫的精靈應用程式](media/scenarios/daemon-app.svg)

如需詳細資訊，請參閱[呼叫 Web API 的精靈應用程式](scenario-daemon-overview.md)。

## <a name="scenarios-and-supported-authentication-flows"></a>情節與支援的驗證流程

涉及取得權杖的案例也會對應到 OAuth 2.0 驗證流程。 如需詳細資訊，請參閱[Microsoft 身分識別平臺上的 OAuth 2.0 和 OpenID connect 通訊協定](active-directory-v2-protocols.md)。

<table>
 <thead>
  <tr><th>狀況</th> <th>詳細案例逐步解說</th> <th>OAuth 2.0 流程和授與</th> <th>適用對象</th></tr>
 </thead>
 <tbody>
  <tr>
   <td><a href="scenario-spa-overview.md"><img alt="Single-Page App" src="media/scenarios/spa-app.svg"></a></td>
   <td><a href="scenario-spa-overview.md">單一頁面應用程式</a></td>
   <td><a href="v2-oauth2-implicit-grant-flow.md">隱含</a></td>
   <td>公司或學校帳戶、個人帳戶和 Microsoft Azure Active Directory B2C (Azure AD B2C)</td>
 </tr>

  <tr>
   <td><a href="scenario-web-app-sign-user-overview.md"><img alt="Web App that signs in users" src="media/scenarios/scenario-webapp-signs-in-users.svg"></a></td>
   <td><a href="scenario-web-app-sign-user-overview.md">登入使用者的 Web 應用程式</a></td>
   <td><a href="v2-oauth2-auth-code-flow.md">授權碼</a></td>
   <td>公司或學校帳戶、個人帳戶和 Azure AD B2C</td>
 </tr>

  <tr>
   <td><a href="scenario-web-app-call-api-overview.md"><img alt="Web App that signs in users" src="media/scenarios/web-app.svg"></a></td>
   <td><a href="scenario-web-app-call-api-overview.md">呼叫 Web API 的 Web 應用程式</a></td>
   <td><a href="v2-oauth2-auth-code-flow.md">授權碼</a></td>
   <td>公司或學校帳戶、個人帳戶和 Azure AD B2C</td>
 </tr>

  <tr>
   <td rowspan="3"><a href="scenario-desktop-overview.md"><img alt=Desktop app that calls web APIs" src="media/scenarios/desktop-app.svg"></a></td>
   <td rowspan="4"><a href="scenario-desktop-overview.md">呼叫 Web API 的傳統型應用程式</a></td>
   <td>互動式 (使用<a href="v2-oauth2-auth-code-flow.md">授權碼</a>搭配 PKCE)</td>
   <td>公司或學校帳戶、個人帳戶和 Azure AD B2C</td>
 </tr>

  <tr>
   <td>整合式 Windows 驗證</td>
   <td>工作或學校帳戶</td>
 </tr>

  <tr>
   <td><a href="v2-oauth-ropc.md">資源擁有者密碼</a></td>
   <td>公司或學校帳戶及 Azure AD B2C</td>
 </tr>

  <tr>
   <td><a href="scenario-desktop-acquire-token.md#command-line-tool-without-a-web-browser"><img alt="Browserless application" src="media/scenarios/device-code-flow-app.svg"></a></td>
   <td><a href="v2-oauth2-device-code.md">裝置程式碼</a></td>
   <td>工作或學校帳戶</td>
 </tr>

 <tr>
   <td rowspan="2"><a href="scenario-mobile-overview.md"><img alt="Mobile app that calls web APIs" src="media/scenarios/mobile-app.svg"></a></td>
   <td rowspan="2"><a href="scenario-mobile-overview.md">呼叫 Web API 的行動應用程式</a></td>
   <td>互動式 (使用<a href="v2-oauth2-auth-code-flow.md">授權碼</a>搭配 PKCE)</td>
   <td>公司或學校帳戶、個人帳戶和 Azure AD B2C</td>
 </tr>

  <tr>
   <td><a href="v2-oauth-ropc.md">資源擁有者密碼</a></td>
   <td>公司或學校帳戶及 Azure AD B2C</td>
 </tr>

  <tr>
   <td><a href="scenario-daemon-overview.md"><img alt="Daemon app that calls web APIs" src="media/scenarios/daemon-app.svg"></a></td>
   <td><a href="scenario-daemon-overview.md">呼叫 Web API 的精靈應用程式</a></td>
   <td><a href="v2-oauth2-client-creds-grant-flow.md">用戶端認證</a></td>
   <td>僅限應用程式的權限 (不含使用者且只能在 Azure AD 組織中使用)</td>
 </tr>

  <tr>
   <td><a href="scenario-web-api-call-api-overview.md"><img alt="Web API that calls web APIs" src="media/scenarios/web-api.svg"></a></td>
   <td><a href="scenario-web-api-call-api-overview.md">呼叫 Web API 的 Web API</a></td>
   <td><a href="v2-oauth2-on-behalf-of-flow.md">代表</a></td>
   <td>公司或學校帳戶與個人帳戶</td>
 </tr>

 </tbody>
</table>

## <a name="scenarios-and-supported-platforms-and-languages"></a>情節與支援的平台與語言

Microsoft 驗證程式庫支援多個平台：

- JavaScript
- .NET Framework
- .NET Core
- Windows 10/UWP
- Xamarin.iOS
- Xamarin.Android
- 原生 iOS
- macOS
- 原生 Android
- Java
- Python

您也可以使用各種語言來建置應用程式。

> [!NOTE]
> 某些應用程式類型無法在每個平臺上使用。

在下表的 Windows 欄中，每次提及 .NET Core 時，也可能是 .NET Framework。 省略後者可避免表格雜亂。

|狀況  | Windows | Linux | Mac | iOS | Android
|--|--|--|--|--|--|--|
| [單一頁面應用程式](scenario-spa-overview.md) <br/>[![單頁應用程式](media/scenarios/spa-app.svg)](scenario-spa-overview.md) | ![MSAL.js](media/sample-v2-code/small_logo_js.png)<br/>MSAL.js | ![MSAL.js](media/sample-v2-code/small_logo_js.png)<br/>MSAL.js | ![MSAL.js](media/sample-v2-code/small_logo_js.png)<br/>MSAL.js | ![MSAL.js](media/sample-v2-code/small_logo_js.png) MSAL.js | ![MSAL.js](media/sample-v2-code/small_logo_js.png)<br/>MSAL.js
| [登入使用者的 Web 應用程式](scenario-web-app-sign-user-overview.md) <br/>[![登入使用者的 Web 應用程式](media/scenarios/scenario-webapp-signs-in-users.svg)](scenario-web-app-sign-user-overview.md) | ![ASP.NET Core](media/sample-v2-code/small_logo_NETcore.png)<br/>ASP.NET Core | ![ASP.NET Core](media/sample-v2-code/small_logo_NETcore.png)<br/>ASP.NET Core | ![ASP.NET Core](media/sample-v2-code/small_logo_NETcore.png)<br/>ASP.NET Core
| [呼叫 Web API 的 Web 應用程式](scenario-web-app-call-api-overview.md) <br/> <br/>[![呼叫 Web API 的 Web 應用程式](media/scenarios/web-app.svg)](scenario-web-app-call-api-overview.md) | ![ASP.NET Core](media/sample-v2-code/small_logo_NETcore.png)<br/>ASP.NET Core + MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png) <br/>MSAL Java<br/>![MSAL Python](media/sample-v2-code/small_logo_python.png)<br/>Flask + MSAL Python| ![ASP.NET Core](media/sample-v2-code/small_logo_NETcore.png)<br/>ASP.NET Core + MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png)<br/>MSAL Java<br/>![MSAL Python](media/sample-v2-code/small_logo_python.png)<br/>Flask + MSAL Python| ![ASP.NET Core](media/sample-v2-code/small_logo_NETcore.png)<br/>ASP.NET Core + MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png)<br/>MSAL Java<br/> ![MSAL Python](media/sample-v2-code/small_logo_python.png)<br/>Flask + MSAL Python
| [呼叫 Web API 的傳統型應用程式](scenario-desktop-overview.md) <br/> <br/>[![呼叫 Web API 的傳統型應用程式](media/scenarios/desktop-app.svg)](scenario-desktop-overview.md) ![裝置程式碼流程](media/scenarios/device-code-flow-app.svg) | ![.NET Core](media/sample-v2-code/small_logo_NETcore.png)MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png)<br/>MSAL Java<br/> ![MSAL Python](media/sample-v2-code/small_logo_python.png)<br/>MSAL Python| ![.NET Core](media/sample-v2-code/small_logo_NETcore.png)MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png)<br/>MSAL Java<br/>![MSAL Python](media/sample-v2-code/small_logo_python.png)<br/>MSAL Python| ![.NET Core](media/sample-v2-code/small_logo_NETcore.png)MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png)<br/>MSAL Java<br/>![MSAL Python](media/sample-v2-code/small_logo_python.png)<br/>MSAL Python <br/> ![iOS/Objective C 或 swift](media/sample-v2-code/small_logo_iOS.png) MSAL.objc |
| [呼叫 Web API 的行動裝置應用程式](scenario-mobile-overview.md) <br/> [![呼叫 Web API 的行動裝置應用程式](media/scenarios/mobile-app.svg)](scenario-mobile-overview.md) | ![UWP](media/sample-v2-code/small_logo_windows.png) MSAL.NET ![Xamarin](media/sample-v2-code/small_logo_xamarin.png) MSAL.NET | | | ![iOS/Objective C 或 swift](media/sample-v2-code/small_logo_iOS.png) MSAL.objc | ![Android](media/sample-v2-code/small_logo_Android.png) MSAL.Android
| [精靈應用程式](scenario-daemon-overview.md) <br/> [![精靈應用程式](media/scenarios/daemon-app.svg)](scenario-daemon-overview.md) | ![.NET Core](media/sample-v2-code/small_logo_NETcore.png)MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png)<br/>MSAL Java<br/>![MSAL Python](media/sample-v2-code/small_logo_python.png)<br/>MSAL Python| ![.NET Core](media/sample-v2-code/small_logo_NETcore.png) MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png)<br/>MSAL Java<br/>![MSAL Python](media/sample-v2-code/small_logo_python.png)<br/>MSAL Python| ![.NET Core](media/sample-v2-code/small_logo_NETcore.png)MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png)<br/>MSAL Java<br/>![MSAL Python](media/sample-v2-code/small_logo_python.png)<br/>MSAL Python
| [呼叫 Web API 的 Web API](scenario-web-api-call-api-overview.md) <br/><br/> [![呼叫 Web API 的 Web API](media/scenarios/web-api.svg)](scenario-web-api-call-api-overview.md) | ![ASP.NET Core](media/sample-v2-code/small_logo_NETcore.png)<br/>ASP.NET Core + MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png)<br/>MSAL Java<br/>![MSAL Python](media/sample-v2-code/small_logo_python.png)<br/>MSAL Python| ![.NET Core](media/sample-v2-code/small_logo_NETcore.png)<br/>ASP.NET Core + MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png)<br/>MSAL Java<br/>![MSAL Python](media/sample-v2-code/small_logo_python.png)<br/>MSAL Python| ![.NET Core](media/sample-v2-code/small_logo_NETcore.png)<br/>ASP.NET Core + MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png)<br/>MSAL Java<br/>![MSAL Python](media/sample-v2-code/small_logo_python.png)<br/>MSAL Python

如需詳細資訊，請參閱[Microsoft 支援的程式庫（依 OS/語言](reference-v2-libraries.md#microsoft-supported-libraries-by-os--language)）。

## <a name="next-steps"></a>後續步驟

深入瞭解[驗證基本概念](authentication-scenarios.md)和[Microsoft 身分識別平臺存取權杖](access-tokens.md)。
