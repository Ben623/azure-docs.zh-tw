---
title: Azure AD B2C （適用于 Android 的 Microsoft 驗證程式庫）
titleSuffix: Microsoft identity platform
description: 瞭解使用 Azure AD B2C 搭配適用于 Android 的 Microsoft 驗證程式庫（MSAL）時的特定考慮。面向
services: active-directory
documentationcenter: dev-center-name
author: brianmel
manager: omkrishn
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 9/18/2019
ms.author: brianmel
ms.reviewer: rapong
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8b5061f1ab341e5872dfa82c9f5c5b133ae40bdf
ms.sourcegitcommit: be8e2e0a3eb2ad49ed5b996461d4bff7cba8a837
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/23/2019
ms.locfileid: "72803248"
---
# <a name="use-msal-for-android-with-b2c"></a>搭配 B2C 使用適用于 Android 的 MSAL

Microsoft 驗證程式庫 (MSAL) 可讓應用程式開發人員使用 [Azure Active Directory B2C (Azure AD B2C)](https://docs.microsoft.com/azure/active-directory-b2c/) \(部分機器翻譯\)，以社交和本機身分識別驗證使用者。 Azure AD B2C 是身分識別管理服務。 用來自訂和控制客戶在使用您的應用程式時，如何註冊、登入及管理其設定檔。

## <a name="configure-known-authorities-and-redirect-uri"></a>設定已知的授權單位和重新導向 URI

在 MSAL for Android 中，B2C 原則（使用者旅程）會設定為個別的授權單位。

假設有一個具有兩個原則的 B2C 應用程式：
- 註冊/登入
    * 呼叫 `B2C_1_SISOPolicy`
- 編輯設定檔
    * 呼叫 `B2C_1_EditProfile`

應用程式的設定檔會宣告兩個 `authorities`。 每個原則各一個。 每個授權單位的 `type` 屬性 `B2C`。

### `app/src/main/res/raw/msal_config.json`
```json
{
    "client_id": "<your_client_id_here>",
    "redirect_uri": "<your_redirect_uri_here>",
    "authorities": [{
            "type": "B2C",
            "authority_url": "https://contoso.b2clogin.com/tfp/contoso.onmicrosoft.com/B2C_1_SISOPolicy/",
            "default": true
        },
        {
            "type": "B2C",
            "authority_url": "https://contoso.b2clogin.com/tfp/contoso.onmicrosoft.com/B2C_1_EditProfile/"
        }
    ]
}
```

`redirect_uri` 必須在應用程式設定中註冊，也可以在 `AndroidManifest.xml` 中，以在[授權碼授與流程](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-reference-oauth-code)期間支援重新導向。

## <a name="initialize-ipublicclientapplication"></a>初始化 IPublicClientApplication

`IPublicClientApplication` 是由 factory 方法所建立，可允許以非同步方式剖析應用程式設定。

```java
PublicClientApplication.createMultipleAccountPublicClientApplication(
    context, // Your application Context
    R.raw.msal_config, // Id of app JSON config
    new IPublicClientApplication.ApplicationCreatedListener() {
        @Override
        public void onCreated(IMultipleAccountPublicClientApplication pca) {
            // Application has been initialized.
        }

        @Override
        public void onError(MsalException exception) {
            // Application could not be created.
            // Check Exception message for details.
        }
    }
);
```

## <a name="interactively-acquire-a-token"></a>以互動方式取得權杖

若要以互動方式使用 MSAL 取得權杖，請建立 `AcquireTokenParameters` 實例，並將其提供給 `acquireToken` 方法。 下面的權杖要求會使用 `default` 授權單位。

```java
IMultipleAccountPublicClientApplication pca = ...; // Initialization not shown

AcquireTokenParameters parameters = new AcquireTokenParameters.Builder()
    .startAuthorizationFromActivity(activity)
    .withScopes(Arrays.asList("https://contoso.onmicrosoft.com/contosob2c/read")) // Provide your registered scope here
    .withPrompt(Prompt.LOGIN)
    .callback(new AuthenticationCallback() {
        @Override
        public void onSuccess(IAuthenticationResult authenticationResult) {
            // Token request was successful, inspect the result
        }

        @Override
        public void onError(MsalException exception) {
            // Token request was unsuccessful, inspect the exception
        }

        @Override
        public void onCancel() {
            // The user cancelled the flow
        }
    }).build();

pca.acquireToken(parameters);
```

## <a name="silently-renew-a-token"></a>以無訊息方式更新權杖

若要使用 MSAL 以無訊息方式取得權杖，請建立 `AcquireTokenSilentParameters` 實例，並將其提供給 `acquireTokenSilentAsync` 方法。 不同于 `acquireToken` 方法，必須指定 `authority` 以無訊息方式取得權杖。

```java
IMultilpeAccountPublicClientApplication pca = ...; // Initialization not shown
AcquireTokenSilentParameters parameters = new AcquireTokenSilentParameters.Builder()
    .withScopes(Arrays.asList("https://contoso.onmicrosoft.com/contosob2c/read")) // Provide your registered scope here
    .forAccount(account)
    // Select a configured authority (policy), mandatory for silent auth requests
    .fromAuthority("https://contoso.b2clogin.com/tfp/contoso.onmicrosoft.com/B2C_1_SISOPolicy/")
    .callback(new SilentAuthenticationCallback() {
        @Override
        public void onSuccess(IAuthenticationResult authenticationResult) {
            // Token request was successful, inspect the result
        }

        @Override
        public void onError(MsalException exception) {
            // Token request was unsuccesful, inspect the exception
        }
    })
    .build();

pca.acquireTokenSilentAsync(parameters);
```

## <a name="specify-a-policy"></a>指定原則

因為 B2C 中的原則是以個別的授權單位來表示，所以當您在建立 `acquireToken` 或 `acquireTokenSilent` 參數時指定 `fromAuthority` 子句，就可以叫用預設值以外的原則。  例如：

```java
AcquireTokenParameters parameters = new AcquireTokenParameters.Builder()
    .startAuthorizationFromActivity(activity)
    .withScopes(Arrays.asList("https://contoso.onmicrosoft.com/contosob2c/read")) // Provide your registered scope here
    .withPrompt(Prompt.LOGIN)
    .callback(...) // provide callback here
    .fromAuthority("<url_of_policy_defined_in_configuration_json>")
    .build();
```

## <a name="handle-password-change-policies"></a>處理密碼變更原則

本機帳戶註冊或登入使用者流程顯示「**忘記密碼？** 」 連結。 按一下此連結並不會自動觸發密碼重設使用者流程。

相反地，錯誤碼 `AADB2C90118` 會傳回給您的應用程式。 您的應用程式應該藉由執行可重設密碼的特定使用者流程來處理此錯誤碼。

若要攔截密碼重設錯誤碼，可以在 `AuthenticationCallback`內使用下列執行：

```java
new AuthenticationCallback() {

    @Override
    public void onSuccess(IAuthenticationResult authenticationResult) {
        // ..
    }

    @Override
    public void onError(MsalException exception) {
        final String B2C_PASSWORD_CHANGE = "AADB2C90118";

        if (exception.getMessage().contains(B2C_PASSWORD_CHANGE)) {
            // invoke password reset flow
        }
    }

    @Override
    public void onCancel() {
        // ..
    }
}
```

## <a name="use-iauthenticationresult"></a>使用 IAuthenticationResult

成功取得權杖會產生 `IAuthenticationResult` 物件。 其中包含存取權杖、使用者宣告和中繼資料。

### <a name="get-the-access-token-and-related-properties"></a>取得存取權杖和相關屬性

```java
// Get the raw bearer token
String accessToken = authenticationResult.getAccessToken();

// Get the scopes included in the access token
String[] accessTokenScopes = authenticationResult.getScope();

// Gets the access token's expiry
Date expiry = authenticationResult.getExpiresOn();

// Get the tenant for which this access token was issued
String tenantId = authenticationResult.getTenantId();
```

### <a name="get-the-authorized-account"></a>取得授權的帳戶

```java
// Get the account from the result
IAccount account = authenticationResult.getAccount();

// Get the id of this account - note for B2C, the policy name is a part of the id
String id = account.getId();

// Get the IdToken Claims
//
// For more information about B2C token claims, see reference documentation
// https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-reference-tokens
Map<String, ?> claims = account.getClaims();

// Get the 'preferred_username' claim through a convenience function
String username = account.getUsername();

// Get the tenant id (tid) claim through a convenience function
String tenantId = account.getTenantId();
```

### <a name="idtoken-claims"></a>IdToken 宣告

IdToken 中傳回的宣告會由安全性權杖服務（STS）填入，而不是由 MSAL。 視所使用的識別提供者（IdP）而定，某些宣告可能不存在。 某些 Idp 目前未提供 `preferred_username` 宣告。 由於 MSAL 會使用此宣告來進行快取，因此會在其位置使用預留位置值 `MISSING FROM THE TOKEN RESPONSE`。 如需有關 B2C IdToken 宣告的詳細資訊，請參閱[Azure Active Directory B2C 中的權杖總覽](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-reference-tokens#claims)。

## <a name="managing-accounts-and-policies"></a>管理帳戶和原則

B2C 會將每個原則視為個別的授權單位。 因此，從每個原則傳回的存取權杖、重新整理權杖和識別碼權杖無法互換。 這表示每個原則都會傳回不同的 `IAccount` 物件，其權杖無法用來叫用其他原則。

每個原則都會將 `IAccount` 新增至每個使用者的快取。 如果使用者登入應用程式，並叫用兩個原則，則會有兩個 `IAccount`s。 若要從快取中移除此使用者，您必須針對每個原則呼叫 `removeAccount()`。

當您使用 `acquireTokenSilent`來更新原則的權杖時，請提供從先前的原則調用傳回給 `AcquireTokenSilentParameters`的相同 `IAccount`。 提供另一個原則所傳回的帳戶會導致錯誤。

## <a name="next-steps"></a>後續步驟

深入瞭解[Azure Active Directory B2C 的](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-overview)Azure Active Directory B2C （Azure AD B2C）？
