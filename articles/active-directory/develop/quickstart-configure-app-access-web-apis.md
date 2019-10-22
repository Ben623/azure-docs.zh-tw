---
title: 設定應用程式以存取 Web API - Microsoft 身分識別平台
description: 了解如何設定使用 Microsoft 身分識別平台所註冊的應用程式，以納入用來存取 Web API 的重新導向 URI、認證或權限。
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 08/07/2019
ms.author: ryanwi
ms.custom: aaddev
ms.reviewer: lenalepa, aragra, sureshja
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5100af99046a03345230ed0468071766aae1c77b
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/16/2019
ms.locfileid: "72389617"
---
# <a name="quickstart-configure-a-client-application-to-access-web-apis"></a>快速入門：設定用戶端應用程式以存取 Web API

為了讓 Web/機密用戶端應用程式能夠參與需要驗證的授權授與流程 (及取得存取權杖)，用戶端必須建立安全的認證。 Azure 入口網站支援的預設驗證方法為用戶端識別碼 + 祕密金鑰。

此外，同意架構要先確保用戶端取得必要的權限授與 (根據所要求的權限)，用戶端才可以存取資源應用程式所公開的 Web API (例如 Microsoft Graph API)。 根據預設，所有應用程式都可以從 Microsoft Graph API 選擇權限。 依預設會選取[圖形 API「登入及讀取使用者設定檔」權限](https://developer.microsoft.com/graph/docs/concepts/permissions_reference#user-permissions)。 針對每一個所要的 Web API，您可以從[兩種權限類型](developer-glossary.md#permissions)中做選擇：

* **應用程式權限** - 您的用戶端應用程式本身需要直接存取 Web API (沒有使用者內容)。 這種類型的權限需要系統管理員的同意，而且無法供公用 (傳統型和行動) 用戶端應用程式使用。
* **委派權限** - 您的用戶端應用程式需要存取 Web API 作為已登入的使用者，但其存取權受到選取權限的限制。 這種類型的權限可由使用者授與，除非權限需要系統管理員的同意。

  > [!NOTE]
  > 新增對應用程式的委派權限並不會自動將同意授與租用戶內的使用者。 使用者仍必須手動同意在執行階段新增的委派權限，除非者系統管理員代表所有使用者授與同意。

在本快速入門中，我們會說明如何設定應用程式，以便：

* [將重新導向 URL 新增至應用程式](#add-redirect-uris-to-your-application)
* [設定應用程式的進階設定](#configure-advanced-settings-for-your-application)
* [修改支援的帳戶類型](#modify-supported-account-types)
* [將認證新增至 Web 應用程式](#add-credentials-to-your-web-application)
* [新增用來存取 Web API 的權限](#add-permissions-to-access-web-apis)

## <a name="prerequisites"></a>必要條件

開始之前，請先確定您已完成以下先決條件：

* 了解所支援的[權限和同意](v2-permissions-and-consent.md)，在建置其他使用者或應用程式需要使用的應用程式時，這些是務必了解的重要資訊。
* 擁有已有應用程式在其中註冊的租用戶。
  * 如果您尚未註冊應用程式，請[了解如何使用 Microsoft 身分識別平台來註冊應用程式](quickstart-register-app.md)。

## <a name="sign-in-to-the-azure-portal-and-select-the-app"></a>登入 Azure 入口網站，然後選取應用程式

請先遵循下列步驟，然後才能設定應用程式：

1. 使用公司或學校帳戶或個人的 Microsoft 帳戶登入 [Azure 入口網站](https://portal.azure.com)。
1. 如果您的帳戶可讓您存取多個租用戶，請在右上角選取帳戶，然後將您的入口網站工作階段設定為想要的 Azure AD 租用戶。
1. 在左側導覽窗格中，選取 [Azure Active Directory]  服務，然後選取 [應用程式註冊]  。
1. 尋找並選取您要設定的應用程式。 在選取應用程式後，您會看到應用程式的 [概觀]  或主要註冊頁面。
1. 遵循下列步驟來設定應用程式以存取 Web API：
    * [將重新導向 URL 新增至應用程式](#add-redirect-uris-to-your-application)
    * [設定應用程式的進階設定](#configure-advanced-settings-for-your-application)
    * [修改支援的帳戶類型](#modify-supported-account-types)
    * [將認證新增至 Web 應用程式](#add-credentials-to-your-web-application)
    * [新增用來存取 Web API 的權限](#add-permissions-to-access-web-apis)

## <a name="add-redirect-uris-to-your-application"></a>將重新導向 URL 新增至應用程式

若要將重新導向 URL 新增至應用程式：

1. 從應用程式的 [概觀]  頁面，選取 [驗證]  區段。
1. 若要新增 Web 和公用用戶端應用程式的自訂重新導向 URI，請遵循下列步驟：
   1. 找到 [重新導向 URI]  區段。
   1. 選取您要建置的應用程式類型：**Web** 還是**公用用戶端 (行動和傳統型)** 。
   1. 輸入應用程式的重新導向 URI。
      * 若為 Web 應用程式，請提供應用程式的基底 URL。 例如，`http://localhost:31544` 可能是本機電腦上所執行 Web 應用程式的 URL。 使用者會使用此 URL 來登入 Web 用戶端應用程式。
      * 若為公用應用程式，請提供 Azure AD 用來傳回權杖回應的 URI。 輸入應用程式特定的值，例如 `https://MyFirstApp`。

1. 若要選擇建議的公用用戶端 (行動、傳統型) 重新導向 URI，請遵循下列步驟：
    1. 找出建議的 [公用用戶端 (行動、傳統型) 的重新導向 URI]  區段。
    1. 使用核取方塊選取應用程式適用的重新導向 URI。 您也可以輸入自訂重新導向 URI。 如果您不確定要使用的內容，請查看程式庫文件。

重新導向 URI 適用一些限制。 深入了解[重新導向 URI 限制](https://docs.microsoft.com/azure/active-directory/develop/reply-url)。
> [!NOTE]
> 試用新的**驗證**設定體驗，您可以在其中根據您要設為目標的平台或裝置來設定應用程式的設定。
>
> 若要查看此檢視，請從預設的 [驗證]  網頁檢視中選取 [試用新的體驗]  。
>
> ![按一下 [試用新的體驗] 以查看平台設定檢視](./media/quickstart-update-azure-ad-app-preview/authentication-try-new-experience-cropped.png)
>
> 這會帶您前往[新的 [平臺設定]  頁面](#configure-platform-settings-for-your-application)。

### <a name="configure-advanced-settings-for-your-application"></a>設定應用程式的進階設定

根據您所註冊的應用程式，您可能需要設定一些額外的設定，例如：

* **登出 URL**
* 針對單一頁面應用程式，您可以啟用**隱含授與**，並選取您想要授權端點發出的權杖。
* 針對在**預設用戶端類型**區段中使用整合式 Windows 驗證、裝置程式碼流程或使用者名稱/密碼取得權杖的桌面應用程式，請將 [將應用程式視為公用用戶端]  設定為 [是]  。
* 針對使用 Live SDK 與 Microsoft 帳戶服務整合的繼承應用程式，請設定 **Live SDK 支援**。 新的應用程式不需要此設定。
* **預設用戶端類型**

### <a name="modify-supported-account-types"></a>修改支援的帳戶類型

**支援的帳戶類型**會指定誰可以使用應用程式或存取 API。

當您一開始註冊應用程式時，一旦[設定了支援的帳戶類型](quickstart-register-app.md)，只有在下列情況下，才可以使用應用程式資訊清單編輯器來變更此設定：

* 您可以將帳戶類型從 **AzureADMyOrg** 或 **AzureADMultipleOrgs** 變更為 **AzureADandPersonalMicrosoftAccount**，反之亦然。
* 您可以將帳戶類型從 **AzureADMyOrg** 變更為 **AzureADMultipleOrgs**，反之亦然。

若要變更現有應用程式註冊所支援的帳戶類型：

* 請參閱[設定應用程式資訊清單](reference-app-manifest.md)並更新 `signInAudience` 金鑰。

## <a name="configure-platform-settings-for-your-application"></a>設定應用程式的平台設定

[![根據平台或裝置設定應用程式的設定](./media/quickstart-update-azure-ad-app-preview/authentication-new-platform-configurations-expanded.png)](./media/quickstart-update-azure-ad-app-preview/authentication-new-platform-configurations-small.png#lightbox)

若要根據平台或裝置來設定應用程式設定，您的目標是：

1. 在 [平台設定]  頁面中，選取 [新增平台]  ，然後從可用的選項中選擇。

   ![顯示 [設定平台] 頁面](./media/quickstart-update-azure-ad-app-preview/authentication-platform-configurations-configure-platforms.png)

1. 根據您選取的平台輸入設定資訊。

   | 平台                | 選擇              | 組態設定            |
   |-------------------------|----------------------|-----------------------------------|
   | **Web 應用程式**    | **Web**              | 輸入應用程式的**重新導向 URI**。 |
   | **行動裝置應用程式** | **iOS**              | 輸入應用程式的 [套件組合識別碼]  ，您可以在 XCode 中的 Info.plist 或 [組建設定] 中找到它。 新增套件組合識別碼會自動建立應用程式的重新導向 URI。 |
   |                         | **Android**          | * 提供應用程式的**套件名稱**，您可以在 androidmanifest.xml 檔案中找到。<br/>* 產生並輸入**簽章雜湊**。 新增簽章雜湊會自動建立應用程式的重新導向 URI。  |
   | **桌面 + 裝置**   | **桌面 + 裝置** | * 選擇性。 如果您要建立桌面和裝置的應用程式，請選取其中一個**建議的重新導向 URI**。<br/>* 選擇性。 輸入**自訂重新導向 URI**，作為回應驗證要求時，Azure AD 將會重新導向使用者的位置。 例如，針對您想要進行互動的 .NET Core 應用程式，請使用 `https://localhost`。 |

   > [!IMPORTANT]
   > 對於未使用最新 MSAL 程式庫或未使用訊息代理程式的行動裝置應用程式，您必須在 [桌面 + 裝置]  中設定這些應用程式的重新導向 URI。

1. 視您選擇的平台而定，可能會有您可以設定的其他設定。 針對 **Web** 應用程式，您可以：
    * 新增更多重新導向 URI
    * 設定[隱含授與]  以選取您想要由授權端點發出的權杖：
        * 針對單一頁面應用程式，請選取 [存取權杖]  和 [識別碼權杖] 
        * 針對 Web 應用程式，選取 [識別碼權杖] 

## <a name="add-credentials-to-your-web-application"></a>將認證新增至 Web 應用程式

若要將認證新增至 Web 應用程式：

1. 從應用程式的 [概觀]  頁面，選取 [憑證和祕密]  區段。

1. 若要新增憑證，請遵循下列步驟：

    1. 選取 [上傳憑證]  。
    1. 選取您想要上傳的檔案。 檔案必須是下列其中一種檔案類型：.cer、.pem、.crt。
    1. 選取 [新增]  。

1. 若要新增用戶端密碼，請遵循下列步驟：

    1. 選取 [新增用戶端密碼]  。
    1. 新增用戶端密碼的描述。
    1. 選取持續時間。
    1. 選取 [新增]  。

> [!NOTE]
> 儲存組態變更後，最右側的資料行就會包含用戶端密碼值。 **請務必複製此值**，以供在用戶端應用程式的程式碼中使用，因為您一旦離開此頁面就無法再存取此金鑰。

## <a name="add-permissions-to-access-web-apis"></a>新增用來存取 Web API 的權限

若要新增權限以從用戶端存取資源 API：

1. 從應用程式的 [概觀]  頁面，選取 [API 權限]  。
1. 選取 [新增權限]  按鈕。
1. 根據預設，此檢視可讓您選取 **Microsoft API**。 選取您感興趣的 API 區段：
    * **Microsoft API** - 可讓您選取 Microsoft API (例如 Microsoft Graph) 的權限。
    * **我的組織使用的 API** - 可讓您針對組織已公開的 API 或組織已整合的 API 選取其權限。
    * **我的 API** - 可讓您選取您所公開 API 的權限。
1. 在選取 API 後，您就會看到 [要求 API 權限]  頁面。 如果 API 同時公開了委派權限和應用程式權限，請選取應用程式所需的權限類型。
1. 完成時，選取 [新增權限]  。 您會回到 [API 權限]  頁面，權限已儲存在其中並新增至資料表。

## <a name="next-steps"></a>後續步驟

了解與應用程式相關的下列其他應用程式管理快速入門：

* [使用 Microsoft 身分識別平台來註冊應用程式](quickstart-register-app.md)
* [設定應用程式以公開 Web API](quickstart-configure-app-expose-web-apis.md)
* [修改應用程式所支援的帳戶](quickstart-modify-supported-accounts.md)
* [移除使用 Microsoft 身分識別平台所註冊的應用程式](quickstart-remove-app.md)

若要了解代表已註冊的應用程式和它們之間關係的兩個 Azure AD 物件，請參閱[應用程式物件和服務主體物件](app-objects-and-service-principals.md)。

若要了解使用 Azure Active Directory 開發應用程式時應使用的商標指導方針，請參閱[應用程式的商標指導方針](howto-add-branding-in-azure-ad-apps.md)。
