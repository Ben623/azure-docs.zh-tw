---
title: 包含檔案
description: 包含檔案
services: active-directory
documentationcenter: dev-center-name
author: navyasric
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/17/2018
ms.author: nacanuma
ms.custom: include file
ms.openlocfilehash: 7a6fa45d0f4ea09fbc84d3185aa0e58db165ac19
ms.sourcegitcommit: b7a44709a0f82974578126f25abee27399f0887f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/18/2019
ms.locfileid: "67205874"
---
## <a name="add-the-applications-registration-information-to-your-app"></a>將應用程式的註冊資訊新增到您的應用程式

在此步驟中，您需要設定應用程式註冊資訊的重新導向 URL，然後將應用程式識別碼新增至 JavaScript SPA 應用程式。

### <a name="configure-redirect-url"></a>設定重新導向 URL

使用以您的 Web 伺服器為基礎的 index.html 網頁 URL 設定 `Redirect URL` 欄位，然後按一下 [更新]  。

> #### <a name="visual-studio-instructions-for-obtaining-the-redirect-url"></a>取得重新導向 URL 的 Visual Studio 指示
> 請遵循下列步驟以取得重新導向 URL：
> 1. 在 [方案總管]  中，選取專案並查看 [屬性]  視窗。 如果您沒有看到 [屬性]  視窗，請按 **F4**。
> 2. 將此值從 **URL** 複製到剪貼簿：<br/> ![專案屬性](media/active-directory-develop-guidedsetup-javascriptspa-configure/vs-project-properties-screenshot.png)<br />
> 3. 將此值貼在此頁面的頂端作為 [重新導向 URL]  ，然後選取 [更新]  。

<p>

> #### <a name="setting-redirect-url-for-node"></a>設定 Node 的重新導向 URL
> 對於 Node.js，您可以在 server.js  檔案中設定網頁伺服器連接埠。 此教學課程會使用連接埠 30662 作為參考，但您可以使用任何其他可用的連接埠。 請遵循下列指示以在應用程式註冊資訊中設定重新導向 URL：<br/>
> 設定 `http://localhost:30662/` 作為此頁面頂端的 [重新導向 URL]  ，或者如果您使用自訂 TCP 連接埠，則使用 `http://localhost:[port]/` (其中 [port]  是自訂 TCP 連接埠號碼)，然後按一下 [更新] 

### <a name="configure-your-javascript-spa-application"></a>設定您的 JavaScript SPA 應用程式

1. 在於專案設定期間建立的 `index.html` 檔案中，新增應用程式註冊資訊。 在 `index.html` 檔案主體頂端的 `<script></script>` 標記內，新增下列程式碼：

```javascript
var applicationConfig = {
    clientID: "Enter_the_Application_Id_here",
    authority: "https://login.microsoftonline.com/common",
    graphScopes: ["user.read"],
    graphEndpoint: "https://graph.microsoft.com/v1.0/me"
};
```
