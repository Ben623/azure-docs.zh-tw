---
title: 設定呼叫 web Api 的傳統型應用程式-Microsoft 身分識別平臺 |Azure
description: 瞭解如何設定呼叫 web Api 的傳統型應用程式代碼
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
ms.date: 10/30/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 33b1724c25ef2d85aa8f838811864104b49576a3
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/25/2019
ms.locfileid: "75423903"
---
# <a name="desktop-app-that-calls-web-apis---code-configuration"></a>呼叫 web Api 的桌面應用程式-程式碼設定

既然您已建立應用程式，您將瞭解如何使用應用程式的座標來設定程式碼。

## <a name="msal-libraries"></a>MSAL 程式庫

支援桌面應用程式的 Microsoft 程式庫包括：

  MSAL 程式庫 | 說明
  ------------ | ----------
  ![MSAL.NET](media/sample-v2-code/logo_NET.png) <br/> MSAL.NET  | 支援在多個平臺（Linux、Windows 和 MacOS）中建立桌面應用程式
  ![Python](media/sample-v2-code/logo_python.png) <br/> MSAL Python | 支援在多個平臺中建立桌面應用程式。
  ![Java](media/sample-v2-code/logo_java.png) <br/> MSAL Java | 支援在多個平臺中建立桌面應用程式。
  ![MSAL iOS](media/sample-v2-code/logo_iOS.png) <br/> MSAL iOS | 僅支援在 macOS 上執行的桌面應用程式

## <a name="public-client-application"></a>公用用戶端應用程式

就程式碼的觀點而言，桌面應用程式是公用用戶端應用程式。 根據您是否使用互動式驗證，設定會有點不同。

# <a name="nettabdotnet"></a>[.NET](#tab/dotnet)

您將需要建立和操作 MSAL.NET `IPublicClientApplication`。

![IPublicClientApplication](media/scenarios/public-client-application.png)

### <a name="exclusively-by-code"></a>由程式碼獨佔

下列程式碼會具現化公用用戶端應用程式、Microsoft Azure 公用雲端中的登入使用者、工作和學校帳戶，或個人 Microsoft 帳戶。

```csharp
IPublicClientApplication app = PublicClientApplicationBuilder.Create(clientId)
    .Build();
```

如果您想要使用互動式驗證或裝置程式碼流程（如上所示），您會想要使用 `.WithRedirectUri` 修飾詞：

```csharp
IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
        .WithDefaultRedirectUri()
        .Build();
```

### <a name="using-configuration-files"></a>使用設定檔

下列程式碼會從設定物件具現化公用用戶端應用程式，這可以用程式設計方式填入或從設定檔讀取

```csharp
PublicClientApplicationOptions options = GetOptions(); // your own method
IPublicClientApplication app = PublicClientApplicationBuilder.CreateWithApplicationOptions(options)
        .WithDefaultRedirectUri()
        .Build();
```

### <a name="more-elaborated-configuration"></a>更複雜的設定

您可以藉由加入數個修飾詞來更精細地建立應用程式。 比方說，如果您想要讓應用程式成為國家雲端中的多租使用者應用程式（在美國政府），您可以撰寫：

```csharp
IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
        .WithDefaultRedirectUri()
        .WithAadAuthority(AzureCloudInstance.AzureUsGovernment,
                         AadAuthorityAudience.AzureAdMultipleOrgs)
        .Build();
```

MSAL.NET 也包含 ADFS 2019 的修飾詞：

```csharp
IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
        .WithAdfsAuthority("https://consoso.com/adfs")
        .Build();
```

最後，如果您想要取得 Azure AD B2C 租使用者的權杖，可以指定您的租使用者，如下列程式碼片段所示：

```csharp
IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
        .WithB2CAuthority("https://fabrikamb2c.b2clogin.com/tfp/{tenant}/{PolicySignInSignUp}")
        .Build();
```

### <a name="learn-more"></a>了解更多

若要深入瞭解如何設定 MSAL.NET 桌面應用程式：

- 如需 `PublicClientApplicationBuilder`上可用的所有修飾詞清單，請參閱參考檔[PublicClientApplicationBuilder](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.publicclientapplicationbuilder#methods)
- 如需 `PublicClientApplicationOptions` 中公開之所有選項的說明，請參閱參考檔中的[PublicClientApplicationOptions](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.publicclientapplicationoptions)。

### <a name="complete-example-with-configuration-options"></a>含設定選項的完整範例

想像一下具有下列 `appsettings.json` 設定檔的 .NET Core 主控台應用程式：

```JSon
{
  "Authentication": {
    "AzureCloudInstance": "AzurePublic",
    "AadAuthorityAudience": "AzureAdMultipleOrgs",
    "ClientId": "ebe2ab4d-12b3-4446-8480-5c3828d04c50"
  },

  "WebAPI": {
    "MicrosoftGraphBaseEndpoint": "https://graph.microsoft.com"
  }
}
```

您有少段程式碼可以使用 .NET 提供的設定架構來讀取此檔案;

```csharp
public class SampleConfiguration
{
 /// <summary>
 /// Authentication options
 /// </summary>
 public PublicClientApplicationOptions PublicClientApplicationOptions { get; set; }

 /// <summary>
 /// Base URL for Microsoft Graph (it varies depending on whether the application is ran
 /// in Microsoft Azure public clouds or national / sovereign clouds
 /// </summary>
 public string MicrosoftGraphBaseEndpoint { get; set; }

 /// <summary>
 /// Reads the configuration from a json file
 /// </summary>
 /// <param name="path">Path to the configuration json file</param>
 /// <returns>SampleConfiguration as read from the json file</returns>
 public static SampleConfiguration ReadFromJsonFile(string path)
 {
  // .NET configuration
  IConfigurationRoot Configuration;
  var builder = new ConfigurationBuilder()
                    .SetBasePath(Directory.GetCurrentDirectory())
                    .AddJsonFile(path);
  Configuration = builder.Build();

  // Read the auth and graph endpoint config
  SampleConfiguration config = new SampleConfiguration()
  {
   PublicClientApplicationOptions = new PublicClientApplicationOptions()
  };
  Configuration.Bind("Authentication", config.PublicClientApplicationOptions);
  config.MicrosoftGraphBaseEndpoint =
  Configuration.GetValue<string>("WebAPI:MicrosoftGraphBaseEndpoint");
  return config;
 }
}
```

現在，若要建立您的應用程式，您只需要撰寫下列程式碼：

```csharp
SampleConfiguration config = SampleConfiguration.ReadFromJsonFile("appsettings.json");
var app = PublicClientApplicationBuilder.CreateWithApplicationOptions(config.PublicClientApplicationOptions)
           .WithDefaultRedirectUri()
           .Build();
```

在呼叫 `.Build()` 方法之前，您可以使用先前所見 `.WithXXX` 方法的呼叫來覆寫設定。

# <a name="javatabjava"></a>[Java](#tab/java)

以下是 MSAL JAVA dev 範例中用來設定範例的類別： [TestData](https://github.com/AzureAD/microsoft-authentication-library-for-java/blob/dev/src/samples/public-client/TestData.java)。

```Java
PublicClientApplication app = PublicClientApplication.builder(TestData.PUBLIC_CLIENT_ID)
        .authority(TestData.AUTHORITY_COMMON)
        .build();
```

# <a name="pythontabpython"></a>[Python](#tab/python)

```Python
config = json.load(open(sys.argv[1]))

app = msal.PublicClientApplication(
    config["client_id"], authority=config["authority"],
    # token_cache=...  # Default cache is in memory only.
                       # You can learn how to use SerializableTokenCache from
                       # https://msal-python.rtfd.io/en/latest/#msal.SerializableTokenCache
    )
```

# <a name="macostabmacos"></a>[MacOS](#tab/macOS)

下列程式碼會具現化公用用戶端應用程式、Microsoft Azure 公用雲端中的登入使用者、工作和學校帳戶，或個人 Microsoft 帳戶。

### <a name="quick-configuration"></a>快速設定

Objective-C：

```objc
NSError *msalError = nil;

MSALPublicClientApplicationConfig *config = [[MSALPublicClientApplicationConfig alloc] initWithClientId:@"<your-client-id-here>"];    
MSALPublicClientApplication *application = [[MSALPublicClientApplication alloc] initWithConfiguration:config error:&msalError];
```

Swift：
```swift
let config = MSALPublicClientApplicationConfig(clientId: "<your-client-id-here>")
if let application = try? MSALPublicClientApplication(configuration: config){ /* Use application */}
```

### <a name="more-elaborated-configuration"></a>更複雜的設定

您可以藉由加入數個修飾詞來更精細地建立應用程式。 比方說，如果您想要讓應用程式成為國家雲端中的多租使用者應用程式（在美國政府），您可以撰寫：

Objective-C：

```objc
MSALAADAuthority *aadAuthority =
                [[MSALAADAuthority alloc] initWithCloudInstance:MSALAzureUsGovernmentCloudInstance
                                                   audienceType:MSALAzureADMultipleOrgsAudience
                                                      rawTenant:nil
                                                          error:nil];

MSALPublicClientApplicationConfig *config =
                [[MSALPublicClientApplicationConfig alloc] initWithClientId:@"<your-client-id-here>"
                                                                redirectUri:@"<your-redirect-uri-here>"
                                                                  authority:aadAuthority];

NSError *applicationError = nil;
MSALPublicClientApplication *application =
                [[MSALPublicClientApplication alloc] initWithConfiguration:config error:&applicationError];
```

Swift：

```swift
let authority = try? MSALAADAuthority(cloudInstance: .usGovernmentCloudInstance, audienceType: .azureADMultipleOrgsAudience, rawTenant: nil)

let config = MSALPublicClientApplicationConfig(clientId: "<your-client-id-here>", redirectUri: "<your-redirect-uri-here>", authority: authority)
if let application = try? MSALPublicClientApplication(configuration: config) { /* Use application */}
```
---

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [取得傳統型應用程式的權杖](scenario-desktop-acquire-token.md)
