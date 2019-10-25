---
title: 初始化用戶端應用程式（適用于 JavaScript 的 Microsoft 驗證程式庫）
titleSuffix: Microsoft identity platform
description: 瞭解如何使用適用于 JavaScript 的 Microsoft 驗證程式庫（MSAL）初始化用戶端應用程式。
services: active-directory
documentationcenter: dev-center-name
author: TylerMSFT
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/12/2019
ms.author: twhitney
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: b24d9d79bf34325ec033b6ae6847579fa51769ac
ms.sourcegitcommit: be8e2e0a3eb2ad49ed5b996461d4bff7cba8a837
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/23/2019
ms.locfileid: "72803094"
---
# <a name="initialize-client-applications-using-msaljs"></a>使用 MSAL 初始化用戶端應用程式
本文說明如何將 Microsoft Authentication Library for JavaScript （MSAL）與使用者代理程式應用程式的實例進行初始化。 使用者代理程式應用程式是一種公用用戶端應用程式，其中用戶端程式代碼會在使用者代理程式（例如網頁瀏覽器）中執行。 這些用戶端不會儲存秘密，因為瀏覽器內容可公開存取。 若要深入瞭解用戶端應用程式類型和應用程式設定選項，請參閱[總覽](msal-client-applications.md)。

## <a name="prerequisites"></a>必要條件
在初始化應用程式之前，您必須先向[Azure 入口網站註冊](scenario-spa-app-registration.md)，讓您的應用程式可以與 Microsoft 身分識別平臺整合。 註冊之後，您可能需要下列資訊（可在 Azure 入口網站中找到）：

- 用戶端識別碼（代表應用程式 GUID 的字串）
- 應用程式的身分識別提供者 URL （名為實例）和登入物件。 這兩個參數統稱為授權單位。
- 租使用者識別碼，如果您要撰寫僅供貴組織使用的企業營運應用程式（也稱為單一租使用者應用程式）。
- 對於 web 應用程式，您也必須設定 redirectUri，其中身分識別提供者會使用安全性權杖傳回您的應用程式。

## <a name="initializing-applications"></a>初始化應用程式

您可以在一般 JavaScript/Typescript 應用程式中使用 MSAL，如下所示。 藉由具現化 `UserAgentApplication` 與設定物件，初始化 MSAL 驗證內容。 初始化 MSAL 所需的最小設定是您應用程式的 clientID，您應該從應用程式註冊入口網站取得該設定。

針對具有重新導向流程（`loginRedirect` 和 `acquireTokenRedirect`）的驗證方法，您必須透過 `handleRedirectCallback()` 方法，明確註冊成功或錯誤的回呼。 這是必要的，因為重新導向流程不會傳回承諾，因為方法具有快顯體驗。

```javascript
// Configuration object constructed
const config = {
    auth: {
        clientId: “abcd-ef12-gh34-ikkl-ashdjhlhsdg”
    }
}

// create UserAgentApplication instance
const myMSALObj = new UserAgentApplication(config);

function authCallback(error, response) {
    //handle redirect response
}

// (optional when using redirect methods) register redirect call back for Success or Error
myMSALObj.handleRedirectCallback(authCallback);
```

MSAL 的設計目的，是要有單一實例和設定 `UserAgentApplication` 來代表單一驗證內容。 不建議使用多個實例，因為它們會在瀏覽器中造成衝突的快取專案和行為。

## <a name="configuration-options"></a>組態選項

MSAL 具有如下所示的設定物件，可提供可供建立 `UserAgentApplication`實例的可設定選項群組。

```javascript
type storage = "localStorage" | "sessionStorage";

// Protocol Support
export type AuthOptions = {
    clientId: string;
    authority?: string;
    validateAuthority?: boolean;
    redirectUri?: string | (() => string);
    postLogoutRedirectUri?: string | (() => string);
    navigateToLoginRequestUrl?: boolean;
};

// Cache Support
export type CacheOptions = {
    cacheLocation?: CacheLocation;
    storeAuthStateInCookie?: boolean;
};

// Library support
export type SystemOptions = {
    logger?: Logger;
    loadFrameTimeout?: number;
    tokenRenewalOffsetSeconds?: number;
};

// Developer App Environment Support
export type FrameworkOptions = {
    isAngular?: boolean;
    unprotectedResources?: Array<string>;
    protectedResourceMap?: Map<string, Array<string>>;
};

// Configuration Object
export type Configuration = {
    auth: AuthOptions,
    cache?: CacheOptions,
    system?: SystemOptions,
    framework?: FrameworkOptions
};
```

以下是目前在 config 物件中支援的可設定選項總數：

- **clientID**：必要。 您的應用程式的 clientID 應該會從應用程式註冊入口網站取得。

- **授權**單位：選擇性。 URL，指出 MSAL 可向要求權杖的目錄。 預設值為：`https://login.microsoftonline.com/common`。
    * 在 Azure AD 中，其格式為 HTTPs://&lt;實例&gt;/&lt;物件&gt;，其中 &lt;實例&gt; 是身分識別提供者網域（例如 `https://login.microsoftonline.com`），而 &lt;物件&gt; 是識別碼代表登入物件。 這可以是下列值：
        * `https://login.microsoftonline.com/<tenant>`租使用者是與租使用者相關聯的網域（例如 contoso.onmicrosoft.com），或是代表目錄之 `TenantID` 屬性的 GUID，僅用於登入特定組織的使用者。
        * `https://login.microsoftonline.com/common`-用來登入具有公司和學校帳戶或 Microsoft 個人帳戶的使用者。
        * `https://login.microsoftonline.com/organizations/`-用來以公司和學校帳戶登入使用者。
        * `https://login.microsoftonline.com/consumers/`-用來登入僅具有個人 Microsoft 帳戶（live）的使用者。
    * 在 Azure AD B2C 中，其格式為 `https://<instance>/tfp/<tenant>/<policyName>/`，其中 instance 是 Azure AD B2C 網域，租使用者是 Azure AD B2C 租使用者的名稱，policyName 是要套用的 B2C 原則名稱。


- **validateAuthority**：選擇性。  驗證權杖的簽發者。 預設值為 `true`。 針對 B2C 應用程式，由於授權值是已知的，而且每個原則可能有所不同，因此授權單位驗證將無法使用，且必須設定為 `false`。

- **redirectUri**：選擇性。  應用程式的重新導向 URI，您的應用程式可在此傳送及接收驗證回應。 其必須完全符合您在入口網站中註冊的其中一個重新導向 Uri。 預設為 `window.location.href`。

- **postLogoutRedirectUri**：選擇性。  將使用者重新導向至登出後的 `postLogoutRedirectUri`。預設值為 `redirectUri`。

- **navigateToLoginRequestUrl**：選擇性。 在登入之後關閉預設導覽至起始頁的功能。 預設值為 true。 這僅用於重新導向流程。

- **cacheLocation**：選擇性。  將瀏覽器儲存設定為 `localStorage` 或 `sessionStorage`。 預設值為 `sessionStorage`。

- **storeAuthStateInCookie**：選擇性。  此旗標是在 MSAL 中引進，以修正 Microsoft Internet Explorer 和 Microsoft Edge 上的[驗證迴圈問題](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Known-issues-on-IE-and-Edge-Browser#1-issues-due-to-security-zones)。 啟用旗標 `storeAuthStateInCookie` 為 true，即可利用此修正程式。 當此功能啟用時，MSAL 會將驗證要求狀態儲存在瀏覽器 cookie 中，以驗證授權流程。 根據預設，此旗標會設定為 `false`。

- **記錄器**：選擇性。  具有回呼實例的記錄器物件，可由開發人員提供，以自訂的方式取用和發行記錄。 如需傳遞記錄器物件的詳細資訊，請參閱[使用 msal 進行記錄](msal-logging.md)。

- **loadFrameTimeout**：選擇性。  在 Azure AD 的權杖更新回應應該被視為超時之前的非使用狀態毫秒數。預設值為6秒。

- **tokenRenewalOffsetSeconds**：選擇性。 毫秒數，設定在到期前更新權杖所需的位移時間範圍。 預設值為300毫秒。

這些僅適用于從 MSAL 角包裝函式程式庫傳遞：
- **unprotectedResources**：選擇性。  未受保護資源的 Uri 陣列。 MSAL 不會將權杖附加至具有這些 URI 的連出要求。 預設為 `null`。

- **protectedResourceMap**：選擇性。  這是將資源對應到 MSAL 用來在 Web API 呼叫中自動附加存取權杖的範圍。 會取得資源的單一存取權杖。 因此，您可以如下所示對應特定的資源路徑： {"https://graph.microsoft.com/v1.0/me"，["user. read"]}，或資源的應用程式 URL： {"https://graph.microsoft.com/"，["user. read"，"mail. send"]}。 這是 CORS 呼叫的必要參數。 預設為 `null`。
