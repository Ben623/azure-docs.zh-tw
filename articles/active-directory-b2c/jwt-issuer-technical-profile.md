---
title: 在自訂原則中定義 JWT 簽發者的技術設定檔
titleSuffix: Azure AD B2C
description: 在 Azure Active Directory B2C 的自訂原則中定義 JSON web token （JWT）簽發者的技術設定檔。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 03/06/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: c23648d70192607b2a5b977dcdd445931e995154
ms.sourcegitcommit: bc792d0525d83f00d2329bea054ac45b2495315d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/06/2020
ms.locfileid: "78671794"
---
# <a name="define-a-technical-profile-for-a-jwt-token-issuer-in-an-azure-active-directory-b2c-custom-policy"></a>在 Azure Active Directory B2C 自訂原則中定義 JWT 權杖簽發者的技術設定檔

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure Active Directory B2C （Azure AD B2C）會在處理每個驗證流程時發出數種類型的安全性權杖。 JWT 權杖簽發者的技術設定檔會發出將傳回至信賴憑證者應用程式的 JWT 權杖。 此技術設定檔通常是使用者旅程圖中的最後一個協調流程步驟。

## <a name="protocol"></a>通訊協定

**Protocol** 元素的 **Name** 屬性必須設定為 `None`。 請將 **OutputTokenFormat** 元素設定為 `JWT`。

下列範例顯示 `JwtIssuer` 的技術設定檔：

```XML
<TechnicalProfile Id="JwtIssuer">
  <DisplayName>JWT Issuer</DisplayName>
  <Protocol Name="None" />
  <OutputTokenFormat>JWT</OutputTokenFormat>
  ...
</TechnicalProfile>
```

## <a name="input-output-and-persist-claims"></a>輸入、輸出和保存宣告

**InputClaims**、**OutputClaims** 和 **PersistClaims** 元素是空的或不存在。 **InutputClaimsTransformations** 和 **OutputClaimsTransformations** 元素也不存在。

## <a name="metadata"></a>中繼資料

| 屬性 | 必要項 | 描述 |
| --------- | -------- | ----------- |
| issuer_refresh_token_user_identity_claim_type | 是 | 應在 OAuth2 授權碼和重新整理權杖內作為使用者識別宣告的宣告。 根據預設，您應將其設定為 `objectId`，除非您指定不同的 SubjectNamingInfo 宣告類型。 |
| SendTokenResponseBodyWithJsonNumbers | 否 | 一律設定為 `true`。 針對以字串形式指定數值 (而非 JSON 數字) 的舊版格式，請設定為 `false`。 用戶端只要相依於以字串形式傳回這類屬性的舊有實作，就需要此屬性。 |
| token_lifetime_secs | 否 | 存取權杖存留期。 用來取得受保護資源之存取權的 OAuth 2.0 持有人權杖的存留期。 預設值為 3,600 秒 (1 小時)。 最小值為 300 秒 (5 分鐘，含此值)。 最大值為 86,400 秒 (24 小時，含此值)。 |
| id_token_lifetime_secs | 否 | 識別碼權杖存留期。 預設值為 3,600 秒 (1 小時)。 最小值為 300 秒 (5 分鐘，含此值)。 最大值為 86,400 秒 (24 小時，含此值)。 |
| refresh_token_lifetime_secs | 否 | 重新整理權杖存留期。 重新整理權杖可用來取得新的存取權杖 (如果您的應用程式已獲得 offline_access 範圍的授權) 的最大期間。 預設值為 120,9600 秒 (14 天)。 最小值為 86,400 秒 (24 小時，含此值)。 最大值為 7,776,000 秒 (90 天，含此值)。 |
| rolling_refresh_token_lifetime_secs | 否 | 重新整理權杖滑動時間範圍存留期。 經過此時段之後，不管應用程式所獲得的最新重新整理權杖有效期間為何，都會強制重新驗證使用者。 如果您不想要強制執行滑動時間範圍存留期，請 allow_infinite_rolling_refresh_token 的值設定為 `true`。 預設值為 7,776,000 秒 (90 天)。 最小值為 86,400 秒 (24 小時，含此值)。 最大值為 31,536,000 秒 (365 天，含此值)。 |
| allow_infinite_rolling_refresh_token | 否 | 如果設定為 `true`，則重新整理權杖滑動時間範圍存留期永不過期。 |
| IssuanceClaimPattern | 否 | 控制簽發者 (iss) 宣告。 下列其中一個值：<ul><li>AuthorityAndTenantGuid-iss 宣告包含您的功能變數名稱，例如 `login.microsoftonline` 或 `tenant-name.b2clogin.com`，以及您的租使用者識別碼 HTTPs：\//login.microsoftonline.com/00000000-0000-0000-0000-000000000000/v2.0/</li><li>AuthorityWithTfp - iss 宣告包含您的網域名稱，例如 `login.microsoftonline` 或 `tenant-name.b2clogin.com`、您的租用戶識別碼，以及您的信賴憑證者原則名稱。 HTTPs：\//login.microsoftonline.com/tfp/00000000-0000-0000-0000-000000000000/b2c_1a_tp_sign-up-or-sign-in/v2.0/</li></ul> 預設值： AuthorityAndTenantGuid |
| AuthenticationContextReferenceClaimPattern | 否 | 控制 `acr` 宣告值。<ul><li>無 - Azure AD B2C 不會發出 acr 宣告</li><li>PolicyId - `acr` 宣告包含原則名稱</li></ul>用來設定此值的選項為 TFP (信任架構原則) 和 ACR (驗證內容參考)。 建議將此值設定為 TFP；若要設定此值，請確定有 `<Item>` 的 `Key="AuthenticationContextReferenceClaimPattern"` 存在，且值為 `None`。 在您的信賴憑證者原則新增 `<OutputClaims>` 項目，並新增下列元素：`<OutputClaim ClaimTypeReferenceId="trustFrameworkPolicy" Required="true" DefaultValue="{policy}" />`。 同時請確定您的原則包含宣告類型 `<ClaimType Id="trustFrameworkPolicy">   <DisplayName>trustFrameworkPolicy</DisplayName>     <DataType>string</DataType> </ClaimType>` |
|RefreshTokenUserJourneyId| 否 | 在 `/token` 端點的重新整理[存取權杖](authorization-code-flow.md#4-refresh-the-token)POST 要求期間，應執行之使用者旅程圖的識別碼。 |

## <a name="cryptographic-keys"></a>密碼編譯金鑰

CryptographicKeys 元素包含下列屬性：

| 屬性 | 必要項 | 描述 |
| --------- | -------- | ----------- |
| issuer_secret | 是 | 用來簽署 JWT 權杖的 X509 憑證 (RSA 金鑰組)。 這是您在`B2C_1A_TokenSigningKeyContainer`開始使用自訂原則[中設定的 ](custom-policy-get-started.md) 金鑰。 |
| issuer_refresh_token_key | 是 | 用來加密重新整理權杖的 X509 憑證 (RSA 金鑰組)。 您已在`B2C_1A_TokenEncryptionKeyContainer`開始使用自訂原則[中設定 ](custom-policy-get-started.md) 金鑰 |














