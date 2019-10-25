---
title: 用戶端應用程式（Microsoft 驗證程式庫）
titleSuffix: Microsoft identity platform
description: 深入瞭解 Microsoft 驗證程式庫（MSAL）中的公用用戶端和機密用戶端應用程式。
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
ms.date: 04/25/2019
ms.author: twhitney
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 76c57d3a13f9f03fa635c6c9425e2049e2a2c885
ms.sourcegitcommit: be8e2e0a3eb2ad49ed5b996461d4bff7cba8a837
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/23/2019
ms.locfileid: "72803208"
---
# <a name="public-client-and-confidential-client-applications"></a>公用用戶端和機密用戶端應用程式
Microsoft 驗證程式庫（MSAL）定義兩種類型的用戶端：公用用戶端和機密用戶端。 這兩種用戶端類型的區別在於能夠安全地向授權伺服器進行驗證，並維護其用戶端認證的機密性。 相較之下，Azure AD 驗證程式庫（ADAL）會使用所謂的*驗證*內容（這是與 Azure AD 的連線）。

- **機密用戶端應用程式**是在伺服器（web 應用程式、web API 應用程式，甚至是服務/daemon 應用程式）上執行的應用程式。 這會被視為很容易存取，因此能夠保存應用程式密碼。 機密用戶端可以保存設定時間秘密。 用戶端的每個實例都有不同的設定（包括用戶端識別碼和用戶端密碼）。 這些值很容易讓使用者進行解壓縮。 Web 應用程式是最常見的機密用戶端。 用戶端識別碼是透過網頁瀏覽器公開的，但密碼只會在後端通道中傳遞，而且永遠不會直接公開。

    機密用戶端應用程式： <BR>
    ![Web 應用程式](media/msal-client-applications/web-app.png) ![Web API](media/msal-client-applications/web-api.png) ![Daemon/服務](media/msal-client-applications/daemon-service.png)

- **公用用戶端應用程式**是在裝置或桌上型電腦上或網頁瀏覽器中執行的應用程式。 它們不受信任，無法安全地保存應用程式密碼，因此他們只會代表使用者存取 Web Api。 （它們僅支援公用用戶端流程）。公用用戶端無法保存設定時間秘密，因此它們沒有用戶端密碼。

    公用用戶端應用程式： <BR>
    ![桌面應用程式](media/msal-client-applications/desktop-app.png) ![Browserless API](media/msal-client-applications/browserless-app.png) ![行動應用程式](media/msal-client-applications/mobile-app.png)

> [!NOTE]
> 在 MSAL 中，不會區分公用和機密用戶端應用程式。  MSAL 會將用戶端應用程式表示為以使用者代理程式為基礎的應用程式，也就是在使用者代理程式（例如網頁瀏覽器）中執行用戶端程式代碼的公用用戶端。 這些用戶端不會儲存秘密，因為瀏覽器內容可公開存取。

## <a name="comparing-the-client-types"></a>比較用戶端類型
以下是公用用戶端和機密用戶端應用程式之間的一些相似之處：

- 這兩種應用程式都會維護使用者權杖快取，並且可以無訊息方式取得權杖（當令牌已在權杖快取中）。 機密用戶端應用程式也會有應用程式本身適用之權杖的應用程式權杖快取。
- 這兩種類型的應用程式都會管理使用者帳戶，而且可以從使用者權杖快取取得帳戶、從其識別碼取得帳戶，或移除帳戶。
- 公用用戶端應用程式有四種方式可取得權杖（四個驗證流程）。 機密用戶端應用程式有三種方式可取得權杖（以及一個計算識別提供者授權端點 URL 的方式）。 如需詳細資訊，請參閱取得[權杖](msal-acquire-cache-tokens.md)。

如果您已經使用 ADAL，您可能會注意到，與 ADAL 的驗證內容不同的是，在 MSAL 中，用戶端識別碼（也稱為*應用程式識別碼*或*應用程式識別碼*）會在應用程式的結構上傳遞一次。 當應用程式取得權杖時，不需要再次傳遞。 這適用于公用和機密用戶端應用程式。 機密用戶端應用程式的「函式」也會傳遞用戶端認證：與識別提供者共用的密碼。

## <a name="next-steps"></a>後續步驟
了解：
- [用戶端應用程式設定選項](msal-client-application-configuration.md)
- [使用 MSAL.NET 具現化用戶端應用程式](msal-net-initializing-client-applications.md)
- [使用 MSAL 具現化用戶端應用程式](msal-js-initializing-client-applications.md)
