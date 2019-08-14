---
title: 將重新導向 URL 設定為 b2clogin.com - Azure Active Directory B2C | Microsoft Docs
description: 了解如何在 Azure Active Directory B2C 的重新導向 URL 中使用 b2clogin.com。
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 01/28/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 080c1933f88d9e824969a42212de2eacd0f62e14
ms.sourcegitcommit: 13a289ba57cfae728831e6d38b7f82dae165e59d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/09/2019
ms.locfileid: "68927275"
---
# <a name="set-redirect-urls-to-b2clogincom-for-azure-active-directory-b2c"></a>將 Azure Active Directory B2C 的重新導向 URL 設定為 b2clogin.com

當您為 Azure Active Directory (Azure AD) B2C 應用程式中的註冊和登入設定識別提供者時，必須指定重新導向 URL。 過去是使用 login.microsoftonline.com，現在您應該使用 b2clogin.com。

> [!NOTE]
> 您可以使用 b2clogin.com 中的 JavaScript 用戶端程式代碼 (目前處於預覽狀態)。 如果您使用 login.microsoftonline.com, 您的 JavaScript 程式碼將會從您的自訂頁面中移除。 額外的安全性限制也適用于 login.microsoftonline.com, 例如從您的自訂頁面移除 HTML 表單元素。 

使用 b2clogin.com 可提供額外的優點，例如：

- Microsoft 服務在 Cookie 標頭中所耗用的空間會縮小。
- 您的 URL 不再包含對 Microsoft 的參考。 例如： `https://your-tenant-name.b2clogin.com/tenant-id/oauth2/authresp` 。

> [!NOTE]
> 您可以使用租使用者名稱和租使用者 GUID, 如下所示:
> * `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com`(它仍然是指`onmicrosoft.com`)
> * `https://your-tenant-name.b2clogin.com/your-tenant-guid`(在這種情況下, 完全沒有 Microsoft 的參考)
>
> 不過, 您無法將_自訂網域_用於 Azure Active Directory B2C 租使用者, 例如`https://your-tenant-name.b2clogin.com/your-custom-domain-name` _無法_使用。

請考慮這些設定在使用 b2clogin.com 時可能需要變更：

- 請將您識別提供者應用程式中的重新導向 URL 設定為使用 b2clogin.com。 
- 設定讓 Azure AD B2C 應用程式將 b2clogin.com 用於使用者流程參考和權杖端點。 
- 如果您使用 MSAL，則必須將 **ValidateAuthority** 屬性設定為 `false`。
- 請確定您變更在 CORS 設定中針對[使用者介面自訂](active-directory-b2c-ui-customization-custom-dynamic.md)定義的任何**允許的來源**。  

## <a name="change-redirect-urls"></a>變更重新導向 URL

若要使用 b2clogin.com，請在識別提供者應用程式的設定中，尋找受信任的 URL 清單，並將其變更為重新導向回 Azure AD B2C。  目前您可能將它設定為重新導向回某個 login.microsoftonline.com 網站。 

您將必須變更重新導向 URL，以便授權 `your-tenant-name.b2clogin.com`。 請務必以您的 Azure AD B2C 租用戶名稱取代 `your-tenant-name`，且如果 URL 中有 `/te`，便將其移除。 每個識別提供者的這個 URL 都略有差異，因此請查看對應的頁面以取得確切的 URL。

您可以在下列文章中找到識別提供者的設定資訊：

- [Microsoft 帳戶](active-directory-b2c-setup-msa-app.md)
- [Facebook](active-directory-b2c-setup-fb-app.md)
- [Google](active-directory-b2c-setup-goog-app.md)
- [Amazon](active-directory-b2c-setup-amzn-app.md)
- [LinkedIn](active-directory-b2c-setup-li-app.md)
- [Twitter](active-directory-b2c-setup-twitter-app.md)
- [GitHub](active-directory-b2c-setup-github-app.md)
- [微博](active-directory-b2c-setup-weibo-app.md)
- [QQ](active-directory-b2c-setup-qq-app.md)
- [微信](active-directory-b2c-setup-wechat-app.md)
- [Azure AD](active-directory-b2c-setup-oidc-azure-active-directory.md)
- [自訂 OIDC](active-directory-b2c-setup-oidc-idp.md)

## <a name="update-your-application"></a>更新您的應用程式

您 Azure AD B2C 應用程式可能有數個地方 (例如使用者流程參考和權杖端點) 都會參考 `login.microsoftonline.com`。  請確定您的授權端點、權杖端點及簽發者都已更新成使用 `your-tenant-name.b2clogin.com`。  

## <a name="set-the-validateauthority-property"></a>設定 ValidateAuthority 屬性

如果您使用 MSAL，請將 **ValidateAuthority** 屬性設定為 `false`。 當 **ValidateAuthority** 設定為 `false` 時，可允許重新導向到 b2clogin.com。 

下列範例示範如何設定該屬性：

在[適用於 .NET 的 MSAL](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet)：

```CSharp
 ConfidentialClientApplication client = new ConfidentialClientApplication(...); // can also be PublicClientApplication
 client.ValidateAuthority = false;
```

和在[適用於 JavaScript 的 MSAL](https://github.com/AzureAD/microsoft-authentication-library-for-js) 中：

```Javascript
this.clientApplication = new UserAgentApplication(
  env.auth.clientId,
  env.auth.loginAuthority,
  this.authCallback.bind(this),
  {
    validateAuthority: false
  }
);
```
