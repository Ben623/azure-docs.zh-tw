---
title: 單一頁面應用程式 (應用程式的程式碼設定)-Microsoft 身分識別平臺
description: 瞭解如何建立單一頁面應用程式 (應用程式的程式碼設定)
services: active-directory
documentationcenter: dev-center-name
author: navyasric
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: nacanuma
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: a7b4fba03f9edf8a3f4e42b23c6a1b5e06518863
ms.sourcegitcommit: a3a40ad60b8ecd8dbaf7f756091a419b1fe3208e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/21/2019
ms.locfileid: "69891523"
---
# <a name="single-page-application---code-configuration"></a>單一頁面應用程式代碼設定

瞭解如何為您的單一頁面應用程式 (SPA) 設定程式碼。

## <a name="msal-libraries-supporting-implicit-flow"></a>支援隱含流程的 MSAL 程式庫

Microsoft 身分識別平臺提供 MSAL 程式庫, 以支援使用業界建議的安全作法的隱含流程。  

支援隱含流程的程式庫包括:

| MSAL 程式庫 | 描述 |
|--------------|--------------|
| ![MSAL.js](media/sample-v2-code/logo_js.png) <br/> [MSAL.js](https://github.com/AzureAD/microsoft-authentication-library-for-js)  | 一般的 JavaScript 程式庫, 用於使用 JavaScript 或 SPA 架構 (例如, 角度、Vue、回應 .js 等) 所建立的任何用戶端 web 應用程式。 |
| ![MSAL 角度](media/sample-v2-code/logo_angular.png) <br/> [MSAL 角度](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angular/README.md) | 核心 MSAL 程式庫的包裝函式, 可簡化以角度架構建立的單一頁面應用程式中使用。 此程式庫目前為預覽狀態, 並具有某些角度版本與瀏覽器的[已知問題](https://github.com/AzureAD/microsoft-authentication-library-for-js/issues?q=is%3Aopen+is%3Aissue+label%3Aangular)。 |

## <a name="application-code-configuration"></a>應用程式代碼設定

在 MSAL 程式庫中, 應用程式註冊資訊會在程式庫初始化期間以設定的形式傳遞。

### <a name="javascript"></a>JavaScript

```javascript
// Configuration object constructed.
const config = {
    auth: {
        clientId: 'your_app_id',
        redirectUri: "your_app_redirect_uri" //defaults to application start page
    }
}

// create UserAgentApplication instance
const userAgentApplication = new UserAgentApplication(config);
```
如需可用可設定選項的詳細資訊, 請參閱[使用 MSAL 初始化應用程式](msal-js-initializing-client-applications.md)。

### <a name="angular"></a>Angular

```javascript
//In app.module.ts
import { MsalModule } from '@azure/msal-angular';

@NgModule({
  imports: [ MsalModule.forRoot({
                clientID: 'your_app_id'
            })]
         })

  export class AppModule { }
```

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [登入和登出](scenario-spa-sign-in.md)
