---
title: Deploy Azure Blockchain Workbench Preview
description: How to deploy Azure Blockchain Workbench Preview
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 11/19/2019
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: brendal
manager: femila
ms.openlocfilehash: 61d47709df2d1bc41bfbf61a7f71f1d73fe27389
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/20/2019
ms.locfileid: "74222881"
---
# <a name="deploy-azure-blockchain-workbench-preview"></a>Deploy Azure Blockchain Workbench Preview

Azure Blockchain Workbench Preview is deployed using a solution template in the Azure Marketplace. 該範本可讓您輕鬆部署建立區塊鏈應用程式所需的元件。 部署完成後，Blockchain Workbench 將可讓您存取用戶端應用程式，以建立及管理使用者和區塊鏈應用程式。

如需關於 Blockchain Workbench 元件的詳細資訊，請參閱 [Azure Blockchain Workbench 架構](architecture.md)。

[!INCLUDE [Preview note](./includes/preview.md)]

## <a name="prepare-for-deployment"></a>準備部署

Blockchain Workbench 可讓您部署區塊鏈總帳與一組相關的 Azure 服務，這些 Azure 服務最常用來建置以區塊鏈為基礎的應用程式。 部署 Blockchain Workbench 會使下列 Azure 服務佈建在您 Azure 訂用帳戶中的資源群組內。

* App Service Plan (Standard)
* Application Insights
* 事件格線
* Azure 金鑰保存庫
* 服務匯流排
* SQL Database (Standard S0) + SQL Logical Server
* Azure Storage account (Standard LRS)
* Virtual machine scale set with capacity of 1
* Virtual Network resource group (with Load Balancer, Network Security Group, Public IP Address, Virtual Network)
* Azure Blockchain Service. If you are using a previous Blockchain Workbench deployment, consider redeploying Azure Blockchain Workbench to use Azure Blockchain Service.

以下是在 **myblockchain** 資源群組中建立的部署範例。

![部署範例](media/deploy/example-deployment.png)

Blockchain Workbench 的成本是彙總基礎 Azure 服務的成本。 Azure 服務的定價資訊可使用[定價計算機](https://azure.microsoft.com/pricing/calculator/)來計算。

## <a name="prerequisites"></a>必要條件

Azure Blockchain Workbench 需要 Azure AD 設定和應用程式註冊。 您可以選擇先進行 Azure AD[手動設定](#azure-ad-configuration)，然後再部署或執行指令碼後部署。 如果您想要重新部署 Blockchain Workbench，請參閱 [Azure AD 設定](#azure-ad-configuration)以驗證您的 Azure AD 設定。

> [!IMPORTANT]
> Workbench 所要部署到的租用戶，不必和用來註冊 Azure AD 應用程式的租用戶相同。 Workbench 所要部署到的租用戶，必須是您在其中有足夠權限可部署資源的租用戶。 如需 Azure AD 租用戶的詳細資訊，請參閱[如何取得 Active Directory 租用戶](../../active-directory/develop/quickstart-create-new-tenant.md)和[整合應用程式與 Azure Active Directory](../../active-directory/develop/quickstart-v1-integrate-apps-with-azure-ad.md)。

## <a name="deploy-blockchain-workbench"></a>部署 Blockchain Workbench

在必要條件步驟完成後，您即可部署 Blockchain Workbench。 以下幾節將概述如何部署架構。

1. 登入 [Azure 入口網站](https://portal.azure.com)。
1. 在右上角選取帳戶，並切換至要部署 Azure Blockchain Workbench 的適當 Azure AD 租用戶。
1. 選取 Azure 入口網站左上角的 [建立資源]。
1. Select **Blockchain** > **Azure Blockchain Workbench (preview)** .

    ![建立 Azure Blockchain Workbench](media/deploy/blockchain-workbench-settings-basic.png)

    | 設定 | 描述  |
    |---------|--------------|
    | 資源前置詞 | 部署的簡短唯一識別項。 此值會作為命名資源的基礎。 |
    | VM 使用者名稱 | 此使用者名稱會作為所有虛擬機器 (VM) 的管理員。 |
    | 驗證類型 | 選取您要使用密碼還是金鑰連線至 VM。 |
    | 密碼 | 此密碼會用來連線至 VM。 |
    | SSH | 使用開頭為 **ssh-rsa** 的單行格式 RSA 公開金鑰，或使用多行 PEM 格式。 您可以在 Linux 和 OS X 上使用 `ssh-keygen` 來產生 SSH 金鑰，或在 Windows 上使用 PuTTYGen 產生。 如需 SSH 金鑰的詳細資訊，請參閱[如何在 Azure 上對 Windows 使用 SSH 金鑰](../../virtual-machines/linux/ssh-from-windows.md)。 |
    | Database and Blockchain password | 指定要用來存取在部署過程中建立之資料庫的密碼。 The password must meet three of the following four requirements: length needs to be between 12 & 72 characters, 1 lower case character, 1 upper case character, 1 number, and 1 special character that is not number sign(#), percent(%), comma(,), star(*), back quote(\`), double quote("), single quote('), dash(-) and semicolumn(;) |
    | 部署區域 | 指定 Blockchain Workbench 資源的部署位置。 為獲得最佳可用性，此位置應符合**位置**設定。 |
    | Subscription | 指定要用於部署的 Azure 訂用帳戶。 |
    | 資源群組 | 選取 [新建] 以建立新的資源群組，並指定唯一的資源群組名稱。 |
    | Location | 指定要部署架構的區域。 |

1. 選取 [確定]，以完成基本設定組態區段。

1. 在 [進階設定] 中，選擇您是否要建立新的區塊鏈網路，或使用現有的權威證明區塊鏈網路。

    **新建**：

    The *create new* option deploys an Azure Blockchain Service Quorum ledger with the default basic sku.

    ![適用於新區塊鏈網路的進階設定](media/deploy/advanced-blockchain-settings-new.png)

    | 設定 | 描述  |
    |---------|--------------|
    | Azure Blockchain Service pricing tier | Choose **Basic** or **Standard** Azure Blockchain Service tier that is used for Blockchain Workbench |
    | Azure Active Directory 設定 | 選擇 [稍後再新增]。</br>注意：如果您選擇了 [預先設定 Azure AD](#azure-ad-configuration) 或重新部署，請選擇 *立即新增*。 |
    | VM 選取項目 | Select preferred storage performance and VM size for your blockchain network. 如果您使用服務額度較低的訂用帳戶 (例如，Azure 免費層)，請選擇較小規模的 VM (例如，標準 DS1 v2)。 |

    **使用現有項目**：

    [使用現有項目] 選項可讓您指定以太坊權威證明 (PoA) 區塊鏈網路。 端點具有下列需求。

   * 端點必須是以太坊權威證明 (PoA) 區塊鏈網路。
   * 端點必須是可透過網路公開存取的。
   * 在 PoA 區塊鏈網路的設定中，應將 Gas Price 設為零。

     > [!NOTE]
     > Blockchain Workbench 帳戶沒有資金。 如果需要資金，交易即會失敗。

     ![適用於現有區塊鏈網路的進階設定](media/deploy/advanced-blockchain-settings-existing.png)

     | 設定 | 描述  |
     |---------|--------------|
     | 以太坊 RPC 端點 | 提供現有 PoA 區塊鏈網路的 RPC 端點。 端點會以 https:// 或 http:// 開頭，並以連接埠號碼結尾。 例如，`http<s>://<network-url>:<port>` |
     | Azure Active Directory 設定 | 選擇 [稍後再新增]。</br>注意：如果您選擇了 [預先設定 Azure AD](#azure-ad-configuration) 或重新部署，請選擇 *立即新增*。 |
     | VM 選取項目 | Select preferred storage performance and VM size for your blockchain network. 如果您使用服務額度較低的訂用帳戶 (例如，Azure 免費層)，請選擇較小規模的 VM (例如，標準 DS1 v2)。 |

1. 選取 [確定] 以完成進階設定。

1. 檢閱摘要，以確認您的參數正確無誤。

    ![總結](media/deploy/blockchain-workbench-summary.png)

1. 選取 [建立]，以同意條款並部署您的 Azure Blockchain Workbench。

部署最多需要 90 分鐘的時間。 您可以使用 Azure 入口網站來監視進度。 在新建立的資源群組中選取 [部署] > [概觀]，以查看已部署的構件狀態。

> [!IMPORTANT]
> 部署後，您需要完成 Active Directory 設定。 如果您選擇了 [稍後再新增]，就需要執行 [Azure AD 設定指令碼](#azure-ad-configuration-script)。  如果您選擇了 [立即新增]，則需要[設定回覆 URL](#configuring-the-reply-url)。

## <a name="blockchain-workbench-web-url"></a>Blockchain Workbench web URL

Blockchain Workbench 部署完成後，新的資源群組即會包含您的 Blockchain Workbench 資源。 Blockchain Workbench 服務可透過 Web URL 來存取。 下列步驟說明如何擷取已部署之架構的 Web URL。

1. 登入 [Azure 入口網站](https://portal.azure.com)。
1. In the left-hand navigation pane, select **Resource groups**.
1. 選擇您在部署 Blockchain Workbench 時指定的資源群組名稱。
1. 選取 [類型] 資料行標題，依類型按字母順序來排序清單。
1. 有兩項屬於 **App Service** 類型的資源。 選取屬於 **App Service** 類型、且*沒有* "-api" 尾碼的資源。

    ![應用程式服務清單](media/deploy/resource-group-list.png)

1. In the App Service **Overview**, copy the **URL** value, which represents the web URL to your deployed Blockchain Workbench.

    ![應用程式服務基本資訊](media/deploy/app-service.png)

若要將自訂網域名稱與 Blockchain Workbench 產生關聯，請參閱[在使用流量管理員的 Azure App Service 中設定 Web 應用程式的自訂網域名稱](../../app-service/web-sites-traffic-manager-custom-domain-name.md)。

## <a name="azure-ad-configuration-script"></a>Azure AD 設定指令碼

您必須設定 Azure AD 以完成 Blockchain Workbench 部署。 您將使用 PowerShell 指令碼來進行設定。

1. 在瀏覽器中，瀏覽至 [Blockchain Workbench Web URL](#blockchain-workbench-web-url)。
1. 您會看到如何使用 Cloud Shell 設定 Azure AD 的指示。 複製命令並啟動 Cloud Shell。

    ![啟動 AAD 指令碼](media/deploy/launch-aad-script.png)

1. 選擇您部署 Blockchain Workbench 所在的 Azure AD 租用戶。
1. 在 Cloud Shell 中，貼上並執行命令。
1. 出現提示時，輸入您想要針對 Blockchain Workbench 使用的 Azure AD 租用戶。 這將是包含適用於 Blockchain Workbench 之使用者的租用戶。

    > [!IMPORTANT]
    > 已驗證的使用者需要權限來建立 Azure AD 應用程式註冊，並在租用戶中授與委派的應用程式權限。 您可能必須要求租用戶的系統管理員執行 Azure AD 設定指令碼，或建立新的租用戶。

    ![輸入 Azure AD 租用戶](media/deploy/choose-tenant.png)

1. 系統將提示您使用瀏覽器來向 Azure AD 租用戶進行驗證。 在瀏覽器中開啟 Web URL、輸入程式碼，然後進行驗證。

    ![使用程式碼進行驗證](media/deploy/authenticate.png)

1. 指令碼會輸出數個狀態訊息。 如果已成功佈建租用戶，您就會收到**成功**狀態訊息。
1. 瀏覽至 Blockchain Workbench URL。 系統會要求您同意授與目錄的讀取權限。 這可讓 Blockchain Workbench Web 應用程式存取租用戶中的使用者。 如果您是租用戶系統管理員，則可為整個組織選擇同意。 這個選項會接受租用戶中所有使用者的同意。 否則，會在第一次使用 Blockchain Workbench Web 應用程式時提示每位使用者同意。
1. 選取 [接受] 來同意。

     ![同意讀取使用者設定檔](media/deploy/graph-permission-consent.png)

1. 同意之後，就能使用 Blockchain Workbench Web 應用程式。

## <a name="azure-ad-configuration"></a>Azure AD 設定

如果您選擇在部署之前手動設定或驗證 Azure AD 設定，請完成本節的所有步驟。 如果您想要自動設定 Azure AD 設定，在部署 Blockchain Workbench 之後，使用[Azure AD 設定指令碼](#azure-ad-configuration-script)。

### <a name="blockchain-workbench-api-app-registration"></a>Blockchain Workbench API 應用程式註冊

要部署 Blockchain Workbench，必須要註冊 Azure AD 應用程式。 您必須要有 Azure Active Directory (Azure AD) 租用戶才能註冊應用程式。 您可以使用現有的租用戶，或建立新的租用戶。 如果您使用現有的 Azure AD 租用戶，則必須有足夠的權限，才能在 Azure AD 租用戶中註冊應用程式、授與圖形 API 權限，以及允許來賓存取。 如果現有的 Azure AD 租用戶中沒有足夠權限，請建立新的租用戶。


1. 登入 [Azure 入口網站](https://portal.azure.com)。
1. 在右上角選取帳戶，並切換至所需的 Azure AD 租用戶。 The tenant should be the subscription admin's tenant of the subscription where Azure Blockchain Workbench is deployed and you have sufficient permissions to register applications.
1. 在左側導覽窗格中，選取 [Azure Active Directory] 服務。 Select **App registrations** > **New registration**.

    ![應用程式註冊](media/deploy/app-registration.png)

1. Provide a display **Name** and choose **Accounts in this organizational directory only**.

    ![建立應用程式註冊](media/deploy/app-registration-create.png)

1. Select **Register** to register the Azure AD application.

### <a name="modify-manifest"></a>修改資訊清單

接著，您必須修改資訊清單，以使用 Azure AD 中的應用程式角色來指定 Blockchain Workbench 管理員。  如需應用程式資訊清單的詳細資訊，請參閱 [Azure Active Directory 應用程式資訊清單](../../active-directory/develop/reference-app-manifest.md)。


1. You need to generate a GUID for the manifest. You can generate a GUID using the PowerShell command `[guid]::NewGuid()` or `New-GUID` cmdlet. 另一個選項是使用 GUID 產生器網站。
1. For the application you registered, select **Manifest** in the **Manage** section.
1. Next, update the **appRoles** section of the manifest. Replace `"appRoles": []` with the provided JSON. 請務必將 [識別碼] 欄位的值取代為您所產生的 GUID。 

    ![編輯資訊清單](media/deploy/edit-manifest.png)

    ``` json
    "appRoles": [
         {
           "allowedMemberTypes": [
             "User",
             "Application"
           ],
           "displayName": "Administrator",
           "id": "<A unique GUID>",
           "isEnabled": true,
           "description": "Blockchain Workbench administrator role allows creation of applications, user to role assignments, etc.",
           "value": "Administrator"
         }
       ],
    ```

    > [!IMPORTANT]
    > 您必須以 [管理員] 值識別 Blockchain Workbench 管理員。

1. 在資訊清單中，也會將 **Oauth2AllowImplictFlow** 值變更為 **true**。

    ``` json
    "oauth2AllowImplicitFlow": true,
    ```

1. 選取 [儲存] 以儲存資訊清單變更。

### <a name="add-graph-api-required-permissions"></a>新增圖形 API 的必要權限

API 應用程式必須向使用者要求存取目錄的權限。 請為 API 應用程式設定下列必要權限：

1. In the *Blockchain API* app registration, select **API permissions**. By default, the Graph API **User.Read** permission is added.

1. In **Grant consent**, select **Grant admin consent** for the domain then select **Yes** for the verification prompt.

   ![授與權限](media/deploy/client-app-grant-permissions.png)

   授與權限後，Blockchain Workbench 即可存取目錄中的使用者。 必須具有讀取權限，才能搜尋成員，以及將成員新增至 Blockchain Workbench。

### <a name="get-application-id"></a>取得應用程式識別碼

部署時必須要有應用程式識別碼和租用戶資訊。 請收集並儲存這些資訊，以供部署期間使用。

1. For the application you registered, select **Overview**.
1. Copy and store the **Application ID** value for later use during deployment.

    ![API 應用程式屬性](media/deploy/app-properties.png)

    | 設定以儲存  | 用於部署 |
    |------------------|-------------------|
    | Application (client) ID | Azure Active Directory 設定 > 應用程式識別碼 |

### <a name="get-tenant-domain-name"></a>取得租用戶網域名稱

收集並儲存應用程式註冊所在的 Active Directory 租用戶網域名稱。 

在左側導覽窗格中，選取 [Azure Active Directory] 服務。 選取 [自訂網域名稱]。 複製並儲存網域名稱。

![網域名稱](media/deploy/domain-name.png)

### <a name="guest-user-settings"></a>來賓使用者設定

如果您的 Azure AD 租用戶中有來賓使用者，請遵循下列額外步驟，以確保 Blockchain Workbench 使用者指派和管理可正常運作。

1. 切換您的 Azure AD 租用戶，然後選取 [Azure Active Directory] > [使用者設定] > [管理外部共同作業設定]。
1. 將 [來賓使用者權限受限] 設定為 [否]。
    ![外部共同作業設定](media/deploy/user-collaboration-settings.png)

## <a name="configuring-the-reply-url"></a>Configuring the reply URL

部署 Azure Blockchain Workbench 之後，您必須設定已部署 Blockchain Workbench Web URL 的 Azure Active Directory (Azure AD) 用戶端應用程式**回覆 URL**。

1. 登入 [Azure 入口網站](https://portal.azure.com)。
1. 確認您在用來註冊 Azure AD 用戶端應用程式的租用戶中。
1. 在左側導覽窗格中，選取 [Azure Active Directory] 服務。 選取 [應用程式註冊]。
1. 選取您在 [必要條件] 區段中註冊的 Azure AD 用戶端應用程式。
1. Select **Authentication**.
1. Specify the main web URL of the Azure Blockchain Workbench deployment you retrieved in the [Blockchain Workbench web URL](#blockchain-workbench-web-url) section. 回覆 URL 前面會加上 `https://`。 例如，`https://myblockchain2-7v75.azurewebsites.net`

    ![Authentication reply URLs](media/deploy/configure-reply-url.png)

1. In the **Advanced setting** section, check **Access tokens** and **ID tokens**.

    ![Authentication advanced settings](media/deploy/authentication-advanced-settings.png)

1. 選取 [儲存] 以更新用戶端註冊。

## <a name="remove-a-deployment"></a>移除部署

當部署不再需要時，您可以藉由刪除 Blockchain Workbench 資源群組來移除部署。

1. 在 Azure 入口網站中，瀏覽至左側瀏覽窗格中的 [資源群組]，然後選取您想要刪除的資源群組。 
1. 選取 [刪除資源群組]。 輸入資源群組名稱並選取 [刪除]，以確定進行刪除。

    ![刪除資源群組](media/deploy/delete-resource-group.png)

## <a name="next-steps"></a>後續步驟

在這篇操作說明文章中，您已部署了 Azure Blockchain Workbench。 若要了解如何建立區塊鏈應用程式，請繼續閱讀下一篇操作說明文章。

> [!div class="nextstepaction"]
> [在 Azure Blockchain Workbench 中建立區塊鏈應用程式](create-app.md)
