---
title: 互動式要求提示行為（MSAL .js） |Azure
titleSuffix: Microsoft identity platform
description: 瞭解如何使用適用于 JavaScript 的 Microsoft 驗證程式庫（MSAL），在互動式呼叫中自訂提示行為。
services: active-directory
author: navyasric
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 04/24/2019
ms.author: nacanuma
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6654b523cef4623a55bf9f857fd5a723fae2dc47
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/08/2019
ms.locfileid: "74921958"
---
# <a name="prompt-behavior-in-msaljs-interactive-requests"></a>MSAL 中的提示行為互動式要求

當使用者使用多個使用者帳戶建立 active Azure AD 會話時，Azure AD 登入 頁面預設會提示使用者選取帳戶，然後再繼續登入。 如果只有一個已驗證的會話與 Azure AD，則使用者不會看到帳戶選取體驗。

MSAL 程式庫（從 v 0.2.4 開始）不會在互動式要求（`loginRedirect`、`loginPopup`、`acquireTokenRedirect` 和 `acquireTokenPopup`）期間傳送提示參數，因此不會強制執行任何提示行為。 針對使用 `acquireTokenSilent` 方法的無訊息 token 要求，MSAL 會將提示參數設定為 `none`。

根據您的應用程式案例，您可以在傳遞給方法的要求參數中設定 prompt 參數，以控制互動式要求的提示行為。 例如，如果您想要叫用帳戶選取體驗：

```javascript
var request = {
    scopes: ["user.read"],
    prompt: 'select_account',
}

userAgentApplication.loginRedirect(request);
```


使用 Azure AD 進行驗證時，可以傳遞下列提示值：

**登入：** 此值會強制使用者輸入驗證要求的認證。

**select_account：** 這個值會提供使用者一個帳戶選取體驗，列出會話中的所有帳戶。

**同意：** 此值將會叫用 OAuth 同意對話方塊，讓使用者授與許可權給應用程式。

**無：** 此值可確保使用者看不到任何互動式提示。 建議您不要將此值傳遞至 MSAL 中的互動式方法，因為它可能會有未預期的行為。 相反地，請使用 `acquireTokenSilent` 方法來達到無訊息呼叫。

## <a name="next-steps"></a>後續步驟

深入瞭解 MSAL 的[OAuth 2.0 隱含授](v2-oauth2-implicit-grant-flow.md)與通訊協定中的 `prompt` 參數，其使用。
