---
title: 在 Azure API 管理中使用備份和還原實作災害復原 | Microsoft Docs
description: 了解如何在 Azure API 管理中使用備份和還原來執行災難復原。
services: api-management
documentationcenter: ''
author: mikebudzynski
manager: erikre
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/26/2019
ms.author: apimpm
ms.openlocfilehash: 6507c39faecfa0e56fc19597e414e9d25d368567
ms.sourcegitcommit: aa66898338a8f8c2eb7c952a8629e6d5c99d1468
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/28/2019
ms.locfileid: "67460868"
---
# <a name="how-to-implement-disaster-recovery-using-service-backup-and-restore-in-azure-api-management"></a>如何在 Azure API 管理中使用服務備份和還原實作災害復原

透過 Azure API 管理來發佈及管理 API，您將能夠利用非 Azure API 管理使用者要另行手動設計、實作及管理的容錯和基礎結構功能。 Azure 平台可緩和絕大部分可能的失敗後果，且成本低廉。

若要從影響裝載您「API 管理」服務的區域可用性問題復原，請隨時準備好在另一個區域中重新建構您的服務。 根據您的復原時間目標，您可以將一或多個區域中的待命資料庫的服務。 您也可能會嘗試維持組態和內容與作用中的服務根據您的復原點目標的同步處理。 服務備份和還原功能會提供必要的建置組塊，以實作災害復原策略。

備份和還原作業也可用來操作的環境，例如 「 開發 」 和 「 預備環境之間複寫 API 管理服務組態。 請注意，該執行階段資料，例如使用者和訂用帳戶將會複製，這不一定需要。

本指南說明如何自動化備份和還原作業以及如何確保成功驗證的備份和還原要求由 Azure Resource Manager。

> [!IMPORTANT]
> 還原作業不會變更的目標服務的自訂主機名稱設定。 我們建議使用相同的自訂主機名稱和 TLS 憑證的作用中和待命資料庫服務，使流量還原作業完成之後，可以重新導向到待命執行個體的簡單的 DNS CNAME 變更。
>
> 備份作業不會擷取顯示在 Azure 入口網站中的 [Analytics] 刀鋒視窗上的報表中使用預先彙總的記錄資料。

> [!WARNING]
> 每個備份會在 30 天後到期。 如果您在過了 30 天的到期時間後嘗試還原備份，還原會失敗並傳回 `Cannot restore: backup expired` 訊息。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="authenticating-azure-resource-manager-requests"></a>驗證 Azure 資源管理員要求

> [!IMPORTANT]
> 備份和還原的 REST API 會使用 Azure 資源管理員，並有不同的驗證機制的 REST API 來管理您的 API 管理實體。 本節中的步驟描述如何驗證 Azure 資源管理員的要求。 如需詳細資訊，請參閱 [驗證 Azure Resource Manager 要求](/rest/api/index)。

所有您使用 Azure Resource Manager 對資源執行的工作，都必須使用下列步驟向 Azure Active Directory 進行驗證：

-   新增應用程式到 Azure Active Directory 租用戶。
-   設定您所新增之應用程式的權限。
-   取得向 Azure 資源管理員驗證要求的權杖。

### <a name="create-an-azure-active-directory-application"></a>建立 Azure Active Directory 應用程式

1. 登入 [Azure 入口網站](https://portal.azure.com)。
2. 使用含 API 管理服務執行個體的訂用帳戶，瀏覽至 **Azure Active Directory** 的 [應用程式註冊]  索引標籤 (Azure Active Directory > 管理/應用程式註冊)。

    > [!NOTE]
    > 如果您的帳戶看不到 Azure Active Directory 預設目錄，請連絡 Azure 訂用帳戶的系統管理員，以授與您的帳戶必要權限。

3. 按一下 [新增應用程式註冊]  。

    [建立]  視窗會出現在右邊。 您可以在這裡輸入 AAD 應用程式的相關資訊。

4. 輸入應用程式的名稱。
5. 針對應用程式類型，選取 [原生]  。
6. 輸入 [重新導向 URI]  的預留位置 URL，例如 `http://resources`，因為它是必要的欄位，但稍後不會使用這個值。 按一下核取方塊以儲存應用程式。
7. 按一下頁面底部的 [新增]  。

### <a name="add-an-application"></a>新增應用程式

1. 在建立應用程式之後，按一下 [設定]  。
2. 按一下 [必要權限]  。
3. 按一下 [+新增]  。
4. 按 [選取 API]  。
5. 選擇 [Windows Azure 服務管理 API]   。
6. 按 [選取]  。

    ![新增權限](./media/api-management-howto-disaster-recovery-backup-restore/add-app.png)

7. 按一下剛新增之應用程式旁邊的 [委派的權限]  ，選取 [存取 Azure 服務管理 (預覽)]  方塊。
8. 按 [選取]  。
9. 按一下 [授與權限]  。

### <a name="configuring-your-app"></a>設定您的應用程式

在呼叫產生備份並將其還原的 API 之前，您必須先取得權杖。 以下範例會使用 [Microsoft.IdentityModel.Clients.ActiveDirectory](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory) NuGet 套件來擷取權杖。

```csharp
using Microsoft.IdentityModel.Clients.ActiveDirectory;
using System;

namespace GetTokenResourceManagerRequests
{
    class Program
    {
        static void Main(string[] args)
        {
            var authenticationContext = new AuthenticationContext("https://login.microsoftonline.com/{tenant id}");
            var result = authenticationContext.AcquireTokenAsync("https://management.azure.com/", "{application id}", new Uri("{redirect uri}"), new PlatformParameters(PromptBehavior.Auto)).Result;

            if (result == null) {
                throw new InvalidOperationException("Failed to obtain the JWT token");
            }

            Console.WriteLine(result.AccessToken);

            Console.ReadLine();
        }
    }
}
```

請使用下列指示來取代 `{tenant id}`、`{application id}` 和 `{redirect uri}`：

1. 使用您所建立 Azure Active Directory 應用程式的租用戶識別碼來取代 `{tenant id}`。 您可以按一下 [應用程式註冊]   -> [端點]  來存取識別碼。

    ![端點][api-management-endpoint]

2. 使用您瀏覽至 [設定]  頁面取得的值來取代 `{application id}`。
3. 以來自您 Azure Active Directory 應用程式 [重新導向 URI]  索引標籤的值取代 `{redirect uri}`。

    指定值之後，程式碼範例應該會傳回類似以下範例的權杖：

    ![token][api-management-arm-token]

    > [!NOTE]
    > 權杖可能會在一段時間之後過期。 再次執行程式碼範例即可產生新的權杖。

## <a name="calling-the-backup-and-restore-operations"></a>呼叫備份與還原作業

REST API 是 [API 管理服務 - 備份](/rest/api/apimanagement/2019-01-01/apimanagementservice/backup)和 [API 管理服務 - 還原](/rest/api/apimanagement/2019-01-01/apimanagementservice/restore)。

在呼叫接下來小節中所述的「備份與還原」作業之前，請先為您的 REST 呼叫設定授權要求標頭。

```csharp
request.Headers.Add(HttpRequestHeader.Authorization, "Bearer " + token);
```

### <a name="step1"> </a>備份 API 管理服務

若要備份 API 管理服務，請發出以下 HTTP 要求：

```http
POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.ApiManagement/service/{serviceName}/backup?api-version={api-version}
```

其中：

-   `subscriptionId` - 訂用帳戶的識別碼，此訂用帳戶會保留您嘗試備份的「API 管理」服務
-   `resourceGroupName` - 您 Azure API 管理服務的資源群組名稱
-   `serviceName` - 所要備份 API 管理服務的名稱，該名稱是在服務建立時所指定
-   `api-version` -取代 `2018-06-01-preview`

在要求的本文中指定目標 Azure 儲存體帳戶名稱、存取金鑰、Blob 容器名稱和備份名稱：

```json
{
    "storageAccount": "{storage account name for the backup}",
    "accessKey": "{access key for the account}",
    "containerName": "{backup container name}",
    "backupName": "{backup blob name}"
}
```

將 `Content-Type` 要求標頭的值設定為 `application/json`。

備份作業的執行時間較長，因此可能需要數分鐘的時間才能完成。 如果要求成功並已開始備份程序，您就會收到含有 `Location` 標頭的 `202 Accepted` 回應狀態碼。 請向 `Location` 標頭中的 URL 發出 'GET' 要求，以查明作業的狀態。 在備份進行時，您會持續收到「202 已接受」狀態碼。 回應碼 `200 OK` 代表備份作業已成功完成。

進行備份要求時，請注意下列條件約束：

-   在要求本文中指定的**容器** **必須存在**。
-   備份在進行時，**請避免變更服務管理** (例如 SKU 升級或降級)、避免變更網域名稱等等。
-   備份還原的**保證僅限建立後的 30 天內**。
-   備份**不包含**用來建立分析報告的**使用量資料**。 請使用 [Azure API 管理 REST API][azure api management rest api] 來定期擷取分析報告，以利妥善保存。
-   執行服務備份的頻率會影響您的復原點目標。 為了盡可能縮小，建議您實作定期備份，並在針對 API 管理服務進行變更後執行隨選備份。
-   在備份作業進行時針對服務組態 (例如 API、原則及開發人員入口網站外觀) 所做的**變更** **可能會從備份中排除，因此可能會遺失**。

### <a name="step2"> </a>還原 API 管理服務

若要從先前建立的備份還原 API 管理服務，請發出以下 HTTP 要求：

```http
POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.ApiManagement/service/{serviceName}/restore?api-version={api-version}
```

其中：

-   `subscriptionId` - 訂用帳戶的識別碼，此訂用帳戶會保留您要還原備份的目標 API 管理服務
-   `resourceGroupName` - 資源群組的名稱，該資源群組會保留您要還原備份的目標 Azure API 管理服務
-   `serviceName` - 所要還原目標 API 管理服務的名稱，該名稱是在服務建立時所指定
-   `api-version` -取代 `2018-06-01-preview`

在要求的本文中，指定備份檔案位置。 也就是新增 Azure 儲存體帳戶名稱、存取金鑰、Blob 容器名稱和備份名稱：

```json
{
    "storageAccount": "{storage account name for the backup}",
    "accessKey": "{access key for the account}",
    "containerName": "{backup container name}",
    "backupName": "{backup blob name}"
}
```

將 `Content-Type` 要求標頭的值設定為 `application/json`。

還原作業的執行時間較長，因此可能需要 30 分鐘以上的時間才能完成。 如果要求成功並已開始還原程序，您就會收到含有 `Location` 標頭的 `202 Accepted` 回應狀態碼。 請向 `Location` 標頭中的 URL 發出 'GET' 要求，以查明作業的狀態。 在還原進行時，您會持續收到「202 已接受」狀態碼。 回應碼 `200 OK` 代表還原作業已成功完成。

> [!IMPORTANT]
> 作為還原目的地之服務的 **SKU** **必須符合**所要還原之已備份服務的 SKU。
>
> 在還原作業進行時針對服務組態 (例如 API、原則、開發人員入口網站外觀) 所做的**變更** **可能會遭到覆寫**。

<!-- Dummy comment added to suppress markdown lint warning -->

> [!NOTE]
> 您也可以使用 PowerShell 執行備份和還原作業_備份 AzApiManagement_並_還原 AzApiManagement_分別命令。

## <a name="next-steps"></a>後續步驟

請參閱下列資源，以取得不同的備份/還原程序逐步解說。

-   [複寫 Azure API 管理帳戶 (英文)](https://www.returngis.net/en/2015/06/replicate-azure-api-management-accounts/)
-   [使用 Logic Apps 將 API 管理備份與還原自動化](https://github.com/Azure/api-management-samples/tree/master/tutorials/automating-apim-backup-restore-with-logic-apps) \(英文\)
-   [Azure APIM：備份與還原設定](https://blogs.msdn.com/b/stuartleeks/archive/2015/04/29/azure-api-management-backing-up-and-restoring-configuration.aspx) \(英文\)
    Stuart 詳細說明的方法與正式指南不同，但相當有意思。 

[backup an api management service]: #step1
[restore an api management service]: #step2
[azure api management rest api]: https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/api-management-rest
[api-management-add-aad-application]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-add-aad-application.png
[api-management-aad-permissions]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-aad-permissions.png
[api-management-aad-permissions-add]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-aad-permissions-add.png
[api-management-aad-delegated-permissions]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-aad-delegated-permissions.png
[api-management-aad-default-directory]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-aad-default-directory.png
[api-management-aad-resources]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-aad-resources.png
[api-management-arm-token]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-arm-token.png
[api-management-endpoint]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-endpoint.png
