---
title: OpenID Connect 通訊協定-Microsoft 身分識別平臺 |Azure
description: 使用 OpenID Connect 驗證通訊協定的 Microsoft 身分識別平臺執行來建立 web 應用程式。
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
editor: ''
ms.assetid: a4875997-3aac-4e4c-b7fe-2b4b829151ce
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/12/2019
ms.author: ryanwi
ms.reviewer: hirsin
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: 0ed1cb6a080a35fa81c6a859f88d987020c8504c
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/13/2020
ms.locfileid: "79262291"
---
# <a name="microsoft-identity-platform-and-openid-connect-protocol"></a>Microsoft 身分識別平臺和 OpenID Connect 通訊協定

OpenID Connect 在 OAuth 2.0 上建置的驗證通訊協定，可用來讓使用者安全地登入 Web 應用程式。 當您使用 Microsoft 身分識別平臺端點的 OpenID Connect 執行時，您可以將登入和 API 存取新增至您的 web 應用程式。 本文說明如何不受語言限制地執行此工作，並說明如何在不使用任何 Microsoft 開放原始碼程式庫的情況下，傳送和接收 HTTP 訊息。

> [!NOTE]
> Microsoft 身分識別平臺端點不支援所有 Azure Active Directory （Azure AD）案例和功能。 若要判斷您是否應該使用 Microsoft 身分識別平臺端點，請參閱[microsoft 身分識別平臺限制](active-directory-v2-limitations.md)。

[OpenID Connect](https://openid.net/specs/openid-connect-core-1_0.html) 將 OAuth 2.0「授權」通訊協定延伸來當作「驗證」通訊協定使用，以便讓您能夠使用 OAuth 來執行單一登入。 OpenID Connect 引進了「識別碼權杖」的概念，這是一種安全性權杖，可讓用戶端確認使用者的身分識別。 識別碼權杖也會取得使用者的相關基本設定檔資訊。 由於 OpenID Connect 延伸了 OAuth 2.0，因此應用程式可以安全地取得「存取權杖」，而這些權杖可用來存取受[授權伺服器](active-directory-v2-protocols.md#the-basics)保護的資源。 Microsoft 身分識別平臺端點也允許向 Azure AD 註冊的協力廠商應用程式，針對受保護的資源（例如 Web Api）發出存取權杖。 如需有關如何設定應用程式以發出存取權杖的詳細資訊，請參閱[如何使用 Microsoft 身分識別平臺端點註冊應用](quickstart-register-app.md)程式。 如果您要建置裝載於伺服器上且透過瀏覽器存取的 [Web 應用程式](v2-app-types.md#web-apps)，建議您使用 OpenID Connect。

## <a name="protocol-diagram-sign-in"></a>通訊協定圖表：登入

最基本的登入流程包含下圖中顯示的步驟。 本文有每個步驟的詳細說明。

![OpenID Connect 通訊協定：登入](./media/v2-protocols-oidc/convergence-scenarios-webapp.svg)

## <a name="fetch-the-openid-connect-metadata-document"></a>擷取 OpenID Connect 中繼資料文件

OpenID Connect 會描述元資料檔案，其中包含應用程式執行登入所需的大部分資訊。 這包括要使用的 URL、服務的公開簽署金鑰位置等資訊。 針對 Microsoft 身分識別平臺端點，這是您應該使用的 OpenID Connect 元資料檔案：

```
https://login.microsoftonline.com/{tenant}/v2.0/.well-known/openid-configuration
```
> [!TIP]
> 試試看！ 按一下 [https://login.microsoftonline.com/common/v2.0/.well-known/openid-configuration](https://login.microsoftonline.com/common/v2.0/.well-known/openid-configuration) 以查看 `common` 租用戶設定。

`{tenant}` 可以接受下列四個值的其中一個：

| 值 | 描述 |
| --- | --- |
| `common` |具有個人 Microsoft 帳戶的使用者，以及 Azure AD 的工作或學校帳戶都可以登入應用程式。 |
| `organizations` |只有具有來自 Azure AD 之工作或學校帳戶的使用者可以登入應用程式。 |
| `consumers` |只有具有個人 Microsoft 帳戶的使用者可以登入應用程式。 |
| `8eaef023-2b34-4da1-9baa-8bc8c9d6a490` 或 `contoso.onmicrosoft.com` | 只有來自特定 Azure AD 租使用者的使用者（不論是使用工作或學校帳戶的目錄成員，或是在具有個人 Microsoft 帳戶的目錄中的來賓）都可以登入應用程式。 可以使用 Azure AD 租用戶的易記網域名稱，或是租用戶的 GUID 識別碼。 您也可以使用取用者租使用者（`9188040d-6c67-4c5b-b112-36a304b66dad`）來取代 `consumers` 的租使用者。  |

中繼資料是簡單的「JavaScript 物件標記法」(JSON) 文件。 如需範例，請參閱下列程式碼片段。 [OpenID Connect 規格](https://openid.net/specs/openid-connect-discovery-1_0.html#rfc.section.4.2)中有程式碼片段內容的完整說明。

```
{
  "authorization_endpoint": "https:\/\/login.microsoftonline.com\/{tenant}\/oauth2\/v2.0\/authorize",
  "token_endpoint": "https:\/\/login.microsoftonline.com\/{tenant}\/oauth2\/v2.0\/token",
  "token_endpoint_auth_methods_supported": [
    "client_secret_post",
    "private_key_jwt"
  ],
  "jwks_uri": "https:\/\/login.microsoftonline.com\/{tenant}\/discovery\/v2.0\/keys",

  ...

}
```

如果您的應用程式具有自訂簽署金鑰做為使用[宣告對應](active-directory-claims-mapping.md)功能的結果，您必須附加包含應用程式識別碼的 `appid` 查詢參數，才能取得指向應用程式簽署金鑰資訊的 `jwks_uri`。 例如： `https://login.microsoftonline.com/{tenant}/v2.0/.well-known/openid-configuration?appid=6731de76-14a6-49ae-97bc-6eba6914391e` 包含 `https://login.microsoftonline.com/{tenant}/discovery/v2.0/keys?appid=6731de76-14a6-49ae-97bc-6eba6914391e`的 `jwks_uri`。

一般而言，您可使用此中繼資料文件來設定 OpenID Connect 程式庫或 SDK；程式庫會使用中繼資料來執行其工作。 不過，如果您不是使用預先建立的 OpenID Connect 程式庫，您可以遵循本文其餘部分中的步驟，使用 Microsoft 身分識別平臺端點在 web 應用程式中進行登入。

## <a name="send-the-sign-in-request"></a>傳送登入要求

當您的 Web 應用程式需要驗證使用者時，其可以將使用者導向至 `/authorize` 端點。 這個要求類似於 [OAuth 2.0 授權碼流程](v2-oauth2-auth-code-flow.md)的第一個階段，但有下列重要區別：

* 要求必須在 `openid` 參數中包含 `scope` 範圍。
* `response_type` 參數必須包含 `id_token`。
* 要求必須包含 `nonce` 參數。

> [!IMPORTANT]
> 若要從/authorization 端點成功要求識別碼權杖，[註冊入口網站](https://portal.azure.com)中的應用程式註冊必須已在 [驗證] 索引標籤中啟用 id_tokens 的隱含授與（這會將[應用程式資訊清單](reference-app-manifest.md)中的 `oauth2AllowIdTokenImplicitFlow` 旗標設定為 `true`）。 如果未啟用，則會傳回 `unsupported_response` 錯誤：「此用戶端不允許提供輸入參數 ' response_type ' 的值。 Expected value is 'code'" (此用戶端的 'response_type' 輸入參數不允許使用所提供的值。預期的值為 'code')

例如：

```
// Line breaks are for legibility only.

GET https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&response_type=id_token
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&response_mode=form_post
&scope=openid
&state=12345
&nonce=678910
```

> [!TIP]
> 請按一下以下連結來執行此要求。 登入之後，您的瀏覽器將會重新導向至 `https://localhost/myapp/`，網址列中會有識別碼權杖。 請注意，此要求會使用 `response_mode=fragment` (僅限用於示範)。 建議您使用 `response_mode=form_post`。
> <a href="https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id=6731de76-14a6-49ae-97bc-6eba6914391e&response_type=id_token&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F&scope=openid&response_mode=fragment&state=12345&nonce=678910" target="_blank">https://login.microsoftonline.com/common/oauth2/v2.0/authorize...</a>

| 參數 | 條件 | 描述 |
| --- | --- | --- |
| `tenant` | 必要 | 您可以要求路徑中使用 `{tenant}` 值來控制可登入應用程式的人員。 允許的值為 `common`、`organizations`、`consumers` 及租用戶識別碼。 如需詳細資訊，請參閱[通訊協定基本概念](active-directory-v2-protocols.md#endpoints)。 |
| `client_id` | 必要 | **應用程式（用戶端）識別碼**， [Azure 入口網站](https://go.microsoft.com/fwlink/?linkid=2083908)指派給您應用程式的應用程式註冊體驗。 |
| `response_type` | 必要 | 必須包含 OpenID Connect 登入的 `id_token` 。 它也可能包含其他 `response_type` 值，例如 `code`。 |
| `redirect_uri` | 建議 | 應用程式的重新導向 URI，您的應用程式可在此傳送及接收驗證回應。 它必須與您在入口網站中註冊的其中一個重新導向 URI 完全相符，只是它必須是採用 URL 編碼。 如果不存在，則端點會隨機挑選一個已註冊的 redirect_uri，以將使用者傳送回。 |
| `scope` | 必要 | 範圍的空格分隔清單。 針對 OpenID Connect，即必須包含範圍 `openid`，其會在同意 UI 中轉譯成「讓您登入」權限。 您也可以在此要求中包含其他範圍來要求同意。 |
| `nonce` | 必要 | 一個由應用程式產生且包含在要求中的值，此值會以宣告方式包含在產生的 id_token 中。 應用程式可以確認此值來減輕權杖重新執行攻擊的影響。 此值通常是一個隨機的唯一字串，可用來識別要求的來源。 |
| `response_mode` | 建議 | 指定將產生的授權碼傳回到應用程式所應該使用的方法。 可以是 `form_post` 或 `fragment`。 針對 Web 應用程式，建議使用 `response_mode=form_post`，以確保會以最安全的方式將權杖傳輸至您的應用程式。 |
| `state` | 建議 | 一個包含在要求中而將一併在權杖回應中傳回的值。 它可以是您想要的任何內容的字串。 通常會使用一個隨機產生的唯一值來[防止跨站台偽造要求攻擊](https://tools.ietf.org/html/rfc6749#section-10.12)。 此狀態也用來在驗證要求出現之前，於應用程式中將使用者狀態的相關資訊 (例如使用者所在的網頁或檢視) 編碼。 |
| `prompt` | 選用 | 表示需要的使用者互動類型。 此時唯有 `login`、`none` 及 `consent` 是有效值。 `prompt=login` 宣告會強制使用者在該要求上輸入其認證，亦即取消單一登入。 `prompt=none` 宣告則相反。 此宣告可確保使用者在時不會看到任何互動式提示。 如果要求無法透過單一登入以無訊息方式完成，Microsoft 身分識別平臺端點會傳回錯誤。 `prompt=consent` 宣告會在使用者登入之後觸發 OAuth 同意對話方塊。 該對話方塊會請使用者將權限授與應用程式。 |
| `login_hint` | 選用 | 如果您事先知道使用者名稱，便可使用此參數為使用者預先填入登入頁面的使用者名稱和電子郵件地址欄位。 通常應用程式會在重新驗證期間，在已經使用 `preferred_username` 宣告從稍早的登入中擷取使用者名稱之後，使用此參數。 |
| `domain_hint` | 選用 | 同盟目錄中使用者的領域。  這會略過使用者在登入頁面上經歷的以電子郵件為基礎的探索程式，以提供稍微簡化的使用者體驗。 對於透過內部部署目錄（例如 AD FS）同盟的租使用者，這通常會因為現有的登入會話而導致無縫式登入。 |

此時，系統會要求使用者輸入其認證並完成驗證。 Microsoft 身分識別平臺端點會確認使用者已同意 `scope` 查詢參數中指出的許可權。 如果使用者未同意這些許可權的任何一項，Microsoft 身分識別平臺端點會提示使用者同意所需的許可權。 您可以深入瞭解[許可權、同意及多租使用者應用程式](v2-permissions-and-consent.md)。

在使用者驗證並授與同意之後，Microsoft 身分識別平臺端點會使用 `response_mode` 參數中指定的方法，以指定的重新導向 URI 將回應傳回給您的應用程式。

### <a name="successful-response"></a>成功回應

使用 `response_mode=form_post` 時的成功回應看起來像這樣：

```
POST /myapp/ HTTP/1.1
Host: localhost
Content-Type: application/x-www-form-urlencoded

id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik1uQ19WWmNB...&state=12345
```

| 參數 | 描述 |
| --- | --- |
| `id_token` | 應用程式所要求的識別碼權杖。 您可以使用 `id_token` 參數來確認使用者的身分識別，並開始與使用者的工作階段。 如需識別碼權杖及其內容的詳細資料，請參閱 [`id_tokens` 參考](id-tokens.md)。 |
| `state` | 如果要求中包含 `state` 參數，回應中就應該出現相同的值。 應用程式應該確認要求和回應中的狀態值完全相同。 |

### <a name="error-response"></a>錯誤回應

錯誤回應也可能傳送到重新導向 URI，以便讓應用程式能夠處理它們。 錯誤回應看起來像這樣：

```
POST /myapp/ HTTP/1.1
Host: localhost
Content-Type: application/x-www-form-urlencoded

error=access_denied&error_description=the+user+canceled+the+authentication
```

| 參數 | 描述 |
| --- | --- |
| `error` | 您可用來分類發生的錯誤類型並對錯誤做出反應的錯誤碼字串。 |
| `error_description` | 可協助您識別驗證錯誤根本原因的特定錯誤訊息。 |

### <a name="error-codes-for-authorization-endpoint-errors"></a>授權端點錯誤的錯誤碼

下表說明可能在錯誤回應的 `error` 參數中傳回的錯誤碼：

| 錯誤碼 | 描述 | 用戶端動作 |
| --- | --- | --- |
| `invalid_request` | 通訊協定錯誤，例如遺漏必要的參數。 |修正並重新提交要求。 這是通常在初始測試期間擷取到的開發錯誤。 |
| `unauthorized_client` | 用戶端應用程式無法要求授權碼。 |當用戶端應用程式未在 Azure AD 中註冊，或未新增至使用者的 Azure AD 租使用者時，通常就會發生這種情況。 應用程式可以對使用者提示一些指示，來安裝應用程式並將它新增到 Azure AD。 |
| `access_denied` | 資源擁有者拒絕同意。 |用戶端應用程式可以通知使用者，除非使用者同意，否則無法繼續。 |
| `unsupported_response_type` |授權伺服器不支援要求中的回應類型。 |修正並重新提交要求。 這是通常在初始測試期間擷取到的開發錯誤。 |
| `server_error` | 伺服器發生非預期的錯誤。 |重試要求。 這些錯誤可能是由暫時性狀況所引起。 用戶端應用程式可能會向使用者解釋，其回應因為暫時性錯誤而延遲。 |
| `temporarily_unavailable` | 伺服器暫時過於忙碌而無法處理要求。 |重試要求。 用戶端應用程式可能會向使用者解釋，其回應因為暫時性狀況而延遲。 |
| `invalid_resource` | 目標資源無效，因為它不存在、Azure AD 找不到，或未正確設定。 |這表示資源（如果存在）尚未在租使用者中設定。 應用程式可以對使用者提示一些指示，來安裝應用程式並將它新增到 Azure AD。 |

## <a name="validate-the-id-token"></a>驗證識別碼權杖

只接收 id_token 並不足以驗證使用者;您必須驗證 id_token 的簽章，並根據您的應用程式需求來驗證權杖中的宣告。 Microsoft 身分識別平臺端點會使用[JSON Web 權杖（jwt）](https://self-issued.info/docs/draft-ietf-oauth-json-web-token.html)和公開金鑰加密來簽署權杖，並確認它們是有效的。

您可以選擇驗證用戶端程式代碼中的 `id_token`，但是常見的作法是將 `id_token` 傳送到後端伺服器，並在該處進行驗證。 驗證 id_token 的簽章之後，有幾個宣告需要驗證。 如需詳細資訊，請參閱 [`id_token`參考](id-tokens.md)，其中包括[驗證權杖](id-tokens.md#validating-an-id_token)和[有關簽署金鑰變換的重要資訊](active-directory-signing-key-rollover.md)。 我們建議利用程式庫來剖析和驗證權杖 - 對於大部分語言和平台至少有一個可用。

您可能也希望根據自己的案例驗證其他宣告。 一些常見的驗證包括：

* 確保使用者/組織已註冊應用程式。
* 確保使用者擁有正確的授權/權限
* 確保驗證具有特定強度，例如多重要素驗證。

驗證 id_token 之後，您就可以開始與使用者的會話，並使用 id_token 中的宣告來取得應用程式中使用者的相關資訊。 這項資訊可以用於顯示、記錄、個人化等等。

## <a name="send-a-sign-out-request"></a>傳送登出要求

當您想要將使用者登出應用程式時，只是清除應用程式的 Cookie 或結束使用者的工作階段還是不夠。 您也必須將使用者重新導向至 Microsoft 身分識別平臺端點，才能登出。如果您不這麼做，使用者會就重新驗證您的應用程式，而不需要再次輸入其認證，因為他們將會與 Microsoft 身分識別平臺端點進行有效的單一登入會話。

您可以將使用者重新導向至 OpenID Connect 中繼資料文件中所列出的 `end_session_endpoint`：

```
GET https://login.microsoftonline.com/common/oauth2/v2.0/logout?
post_logout_redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
```

| 參數 | 條件 | 描述 |
| ----------------------- | ------------------------------- | ------------ |
| `post_logout_redirect_uri` | 建議 | 成功登出之後，使用者會重新導向至的 URL。如果未包含參數，使用者會看到由 Microsoft 身分識別平臺端點產生的一般訊息。 此 URL 必須與您在應用程式註冊入口網站中為應用程式註冊的其中一個重新導向 URI 相符。 |

## <a name="single-sign-out"></a>單一登出

當您將使用者重新導向至 `end_session_endpoint`時，Microsoft 身分識別平臺端點會清除瀏覽器中的使用者會話。 不過，使用者可能仍然登入其他使用 Microsoft 帳戶進行驗證的應用程式。 為了讓這些應用程式能夠同時登出使用者，Microsoft 身分識別平臺端點會將 HTTP GET 要求傳送至使用者目前登入之所有應用程式的已註冊 `LogoutUrl`。 應用程式必須藉由清除任何可識別使用者的工作階段並傳回 `200` 回應，以回應此要求。 如果您想要在應用程式中支援單一登出，您必須在應用程式的程式碼中實作這類 `LogoutUrl`。 您可以從應用程式註冊入口網站設定 `LogoutUrl`。

## <a name="protocol-diagram-access-token-acquisition"></a>通訊協定圖表：取得存取權杖

許多 Web 應用程式不僅需要將使用者登入，也需要使用 OAuth 來代表使用者存取 Web 服務。 這個案例結合了 OpenID Connect 來進行使用者驗證，同時又取得您使用 OAuth 授權碼流程時，可用來取得存取權杖的授權碼。

完整的 OpenID Connect 登入和權杖取得流程看起來如下圖。 我們會在本文後續的小節中詳細說明每個步驟。

![OpenID Connect 通訊協定：權杖取得](./media/v2-protocols-oidc/convergence-scenarios-webapp-webapi.svg)

## <a name="get-access-tokens"></a>取得存取權杖
若要取得存取權杖，請修改登入要求：

```
// Line breaks are for legibility only.

GET https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e        // Your registered Application ID
&response_type=id_token%20code
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F       // Your registered redirect URI, URL encoded
&response_mode=form_post                              // 'form_post' or 'fragment'
&scope=openid%20                                      // Include both 'openid' and scopes that your app needs  
offline_access%20                                         
https%3A%2F%2Fgraph.microsoft.com%2Fuser.read
&state=12345                                          // Any value, provided by your app
&nonce=678910                                         // Any value, provided by your app
```

> [!TIP]
> 請按一下以下連結來執行此要求。 登入之後，您的瀏覽器將會重新導向至 `https://localhost/myapp/`，且網址列中會有識別碼權杖和代碼。 請注意，此要求會使用 `response_mode=fragment` (僅限用於示範)。 建議您使用 `response_mode=form_post`。
> <a href="https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id=6731de76-14a6-49ae-97bc-6eba6914391e&response_type=id_token%20code&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F&response_mode=fragment&scope=openid%20offline_access%20https%3A%2F%2Fgraph.microsoft.com%2Fuser.read&state=12345&nonce=678910" target="_blank">https://login.microsoftonline.com/common/oauth2/v2.0/authorize...</a>

藉由在要求中包含許可權範圍，以及藉由使用 `response_type=id_token code`，Microsoft 身分識別平臺端點可確保使用者已同意 `scope` 查詢參數中指出的許可權。 它會將授權碼傳回給到您的應用程式以交換存取權杖。

### <a name="successful-response"></a>成功回應

使用 `response_mode=form_post` 的成功回應看起來像這樣：

```
POST /myapp/ HTTP/1.1
Host: localhost
Content-Type: application/x-www-form-urlencoded

id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik1uQ19WWmNB...&code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...&state=12345
```

| 參數 | 描述 |
| --- | --- |
| `id_token` | 應用程式所要求的識別碼權杖。 您可以使用識別碼權杖來確認使用者的身分識別，然後開始與使用者的工作階段。 如需有關識別碼權杖及其內容的更多詳細資料，請參閱 [`id_tokens` 參考](id-tokens.md)。 |
| `code` | 應用程式所要求的授權碼。 應用程式可以使用授權碼要求目標資源的存取權杖。 授權碼的存留期很短。 授權碼的有效期通常大約是 10 分鐘。 |
| `state` | 如果要求中包含狀態參數，回應中就應該出現相同的值。 應用程式應該確認要求和回應中的狀態值完全相同。 |

### <a name="error-response"></a>錯誤回應

錯誤回應也可能傳送到重新導向 URI，以便讓應用程式能夠適當地處理它們。 錯誤回應看起來像這樣：

```
POST /myapp/ HTTP/1.1
Host: localhost
Content-Type: application/x-www-form-urlencoded

error=access_denied&error_description=the+user+canceled+the+authentication
```

| 參數 | 描述 |
| --- | --- |
| `error` | 您可用來分類發生的錯誤類型並對錯誤做出反應的錯誤碼字串。 |
| `error_description` | 可協助您識別驗證錯誤根本原因的特定錯誤訊息。 |

如需可能的錯誤碼說明及建議的用戶端回應，請參閱[授權端點錯誤的錯誤碼](#error-codes-for-authorization-endpoint-errors)。

在您取得授權碼和識別碼權杖之後，您可以將使用者登入並代表他們取得存取權杖。 若要將使用者登入，您必須[完全依照所述的方式](id-tokens.md#validating-an-id_token)驗證識別碼權杖。 若要取得存取權杖，請依照 [OAuth 程式碼流程文件](v2-oauth2-auth-code-flow.md#request-an-access-token)中所述的步驟操作。
