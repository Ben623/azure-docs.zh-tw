---
title: 使用 Azure Active Directory 來授權開發人員帳戶 - Azure API 管理 | Microsoft Docs
description: 了解如何在 API 管理中使用 Azure Active Directory 來授權使用者。
services: api-management
documentationcenter: API Management
author: miaojiang
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 11/04/2019
ms.author: apimpm
ms.openlocfilehash: 067d4488b064ede572a4b3ad94c94fb1552c827d
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/24/2019
ms.locfileid: "74454464"
---
# <a name="authorize-developer-accounts-by-using-azure-active-directory-in-azure-api-management"></a>在 Azure API 管理中使用 Azure Active Directory 來授權開發人員帳戶

本文說明如何讓使用者能夠從 Azure Active Directory (Azure AD) 來存取開發人員入口網站。 本指南也說明如何新增包含 Azure AD 使用者的外部群組來管理使用者。

## <a name="prerequisites"></a>必要條件

- 完成下列快速入門：[建立 Azure API 管理執行個體](get-started-create-service-instance.md)。
- 匯入並發佈 Azure API 管理執行個體。 如需詳細資訊，請參閱[匯入和發佈](import-and-publish.md)。

[!INCLUDE [premium-dev-standard.md](../../includes/api-management-availability-premium-dev-standard.md)]

## <a name="authorize-developer-accounts-by-using-azure-ad"></a>使用 Azure AD 來授權開發人員帳戶

1. 登入 [Azure 入口網站](https://portal.azure.com)。 
2. 選取 ![箭號](./media/api-management-howto-aad/arrow.png)。
3. 在搜尋方塊中，輸入 **api**。
4. 選取 [API 管理服務]。
5. 選取 API 管理服務執行個體。
6. Under **Security**, select **Identities**.
7. 從頂端選取 [+新增]。

    [新增識別提供者] 窗格隨即出現在右邊。
8. 在 [提供者類型] 下選取 [Azure Active Directory]。

    窗格中會出現可讓您輸入其他必要資訊的控制項。 控制項包括 [用戶端識別碼] 與 [用戶端密碼]。 (您可在本文稍後取得這些控制項的相關資訊)。
9. Make a note of the content of **Redirect URL**.
    
   ![在 Azure 入口網站中新增識別提供者的步驟](./media/api-management-howto-aad/api-management-with-aad001.png)  
10. 在瀏覽器中開啟其他索引標籤。 
11. Navigate to the [Azure portal - App registrations](https://go.microsoft.com/fwlink/?linkid=2083908) to register an app in Active Directory.
12. Under **Manage**, select **App registrations**.
13. 選取 [新增註冊]。 On the **Register an application** page, set the values as follows:
    
* Set **Name** to a meaningful name. e.g., *developer-portal*
* Set **Supported account types** to **Accounts in this organizational directory only**. 
* Set **Redirect URI** to the value you got from step 9. 
* Choose **Register**. 

14.  After the application is registered, copy the **Application (client) ID** from the **Overview** page. 
15. Go back to your API Management instance. In the **Add identity provider** window, paste the **Application (client) ID** value into the **Client ID** box.
16. Switch back to the Azure AD configuration, Select **Certificates & secrets** under **Manage**. Select the **New client secret** button. Enter a value in **Description**, select any option for **Expires** and choose **Add**. Copy the client secret value before leaving the page. 您在下一個步驟將會用到這些資料。 
17. Under **Manage**, select **Authentication** and then select **ID tokens** under **Implicit Grant**
18. Go back to your API Management instance, paste the secret into the **Client secret** box.

    > [!IMPORTANT]
    > 請務必在金鑰到期之前，更新 [用戶端密碼]。 
    >  
    >

19. [新增識別提供者] 視窗也包含 [允許的租用戶] 文字方塊。 請在該處指定 Azure AD 執行個體的網域，您將會對它授與 API 管理服務執行個體的 API 存取權。 您可以使用換行符號、空格或逗號來分隔多個網域。

> [!NOTE]
> 您可以在 [允許的租用戶] 區段中指定多個網域。 在使用者可透過與註冊應用程式之原始網域不同的網域登入前，不同網域的全域管理員必須授與權限，應用程式才能存取目錄資料。 To grant permission, the global administrator should: a. 移至 `https://<URL of your developer portal>/aadadminconsent` (例如， https://contoso.portal.azure-api.net/aadadminconsent) 。
> b.這是另一個 C# 主控台應用程式。 輸入想要授與存取權的 Azure AD 租用戶網域名稱。
> c. 選取 [提交]。 

20.  在您指定所需的設定之後，請選取 [新增]。

儲存變更之後，所指定 Azure AD 執行個體中的使用者即可依照[使用 Azure AD 帳戶來登入開發人員入口網站](#log_in_to_dev_portal)中的步驟來登入開發人員入口網站。

## <a name="add-an-external-azure-ad-group"></a>新增外部 Azure AD 群組

After you enable access for users in an Azure AD tenant, you can add Azure AD groups into API Management. As a result, you can control product visibility using Azure AD groups.

To add an external Azure AD group into APIM, you must first complete the previous section. Additionally, the application you registered must be granted access to the Azure Active Directory Graph API with `Directory.ReadAll` permission by following below steps: 

1. Go back to your App Registration that was created in the previous section
2. Click on the **API Permissions** tab, then click **+Add a permission** button 
3. In the **Request API Permissions** pane, select the **Microsoft APIs** tab, and scroll to the bottom to find the **Azure Active Directory Graph** tile under the Supported Legacy APIs section and click it. Then click **APPLICATION Permissions** button, and select **Directory.ReadAll** permission and then add that permission using button at the bottom. 
4. Click the **Grant admin consent for {tenantname}** button so that you grant access for all users in this directory. 

Now you can add external Azure AD groups from the **Groups** tab of your API Management instance.

1. 選取 [群組] 索引標籤。
2. 選取 [新增 AAD 群組] 按鈕。
   ![[新增 AAD 群組] 按鈕](./media/api-management-howto-aad/api-management-with-aad008.png)
3. 選取您想要新增的群組。
4. 按 [選取] 按鈕。

新增外部 Azure AD 群組之後，您就可以檢閱並設定其屬性。 Select the name of the group from the **Groups** tab. From here, you can edit **Name** and **Description** information for the group.
 
所設定 Azure AD 執行個體的使用者現在可以登入開發人員入口網站。 這些使用者也可以檢視和訂閱所能看見的任何群組。

## <a name="a-idlog_in_to_dev_portal-developer-portal---add-azure-ad-account-authentication"></a><a id="log_in_to_dev_portal"/> Developer portal - add Azure AD account authentication

In the developer portal, sign-in with AAD is possible with the **OAuth buttons** widget. The widget is already included on the sign-in page of the default developer portal content.

![AAD buttons widget](./media/api-management-howto-aad/portal-oauth-widget.png)

Although a new account will be automatically created whenever a new user signs in with AAD, you may consider adding the same widget to the sign-up page.

> [!IMPORTANT]
> You need to [republish the portal](api-management-howto-developer-portal-customize.md#publish) for the AAD changes to take effect.

## <a name="legacy-developer-portal---how-to-sign-in-with-azure-ad"></a>Legacy developer portal - how to sign in with Azure AD

[!INCLUDE [api-management-portal-legacy.md](../../includes/api-management-portal-legacy.md)]

使用您在先前小節中設定的 Azure AD 帳戶來登入開發人員入口網站：

1. 使用 Active Directory 應用程式設定中的登入 URL 來開啟新的瀏覽器視窗，然後選取 [Azure Active Directory]。

   ![登入頁面][api-management-dev-portal-signin]

1. 輸入 Azure AD 中其中一位使用者的認證，然後選取 [登入]。

   ![以使用者名稱和密碼來登入][api-management-aad-signin]

1. 如果需要其他資訊，可能會出現註冊表單的提示。 完成註冊表單，然後選取 [註冊]。

   ![註冊表單上的 [註冊] 按鈕][api-management-complete-registration]

您的使用者現在已登入 API 管理服務執行個體的開發人員入口網站。

![註冊完成之後的開發人員入口網站][api-management-registration-complete]

[api-management-dev-portal-signin]: ./media/api-management-howto-aad/api-management-dev-portal-signin.png
[api-management-aad-signin]: ./media/api-management-howto-aad/api-management-aad-signin.png
[api-management-complete-registration]: ./media/api-management-howto-aad/api-management-complete-registration.png
[api-management-registration-complete]: ./media/api-management-howto-aad/api-management-registration-complete.png

[How to add operations to an API]: api-management-howto-add-operations.md
[How to add and publish a product]: api-management-howto-add-products.md
[Monitoring and analytics]: api-management-monitoring.md
[Add APIs to a product]: api-management-howto-add-products.md#add-apis
[Publish a product]: api-management-howto-add-products.md#publish-product
[Get started with Azure API Management]: get-started-create-service-instance.md
[API Management policy reference]: api-management-policy-reference.md
[Caching policies]: api-management-policy-reference.md#caching-policies
[Create an API Management service instance]: get-started-create-service-instance.md

[https://oauth.net/2/]: https://oauth.net/2/
[WebApp-GraphAPI-DotNet]: https://github.com/AzureADSamples/WebApp-GraphAPI-DotNet
[Accessing the Graph API]: https://msdn.microsoft.com/library/azure/dn132599.aspx#BKMK_Graph

[Prerequisites]: #prerequisites
[Configure an OAuth 2.0 authorization server in API Management]: #step1
[Configure an API to use OAuth 2.0 user authorization]: #step2
[Test the OAuth 2.0 user authorization in the Developer Portal]: #step3
[Next steps]: #next-steps

[Sign in to the developer portal by using an Azure AD account]: #Sign-in-to-the-developer-portal-by-using-an-Azure-AD-account
