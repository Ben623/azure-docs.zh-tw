---
title: 匯出 Azure 活動記錄
description: 將 Azure 活動記錄檔匯出至儲存體，以便在 Azure 外部匯出或 Azure 事件中樞進行封存。
author: bwren
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 05/20/2019
ms.author: bwren
ms.subservice: logs
ms.openlocfilehash: ff8956d942aa54500a08cac4ebd94127b14b0bd4
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/08/2019
ms.locfileid: "74931768"
---
# <a name="export-azure-activity-log-to-storage-or-azure-event-hubs"></a>將 Azure 活動記錄匯出至儲存體或 Azure 事件中樞

> [!WARNING]
> 您現在可以使用與收集資源記錄的方式類似的診斷設定，將活動記錄收集到 Log Analytics 工作區。 請參閱[在 Azure 監視器中收集和分析 Log Analytics 工作區中的 Azure 活動記錄](diagnostic-settings-subscription.md)。

[Azure 活動記錄](activity-logs-overview.md)可讓您深入瞭解 azure 訂用帳戶中所發生的訂用帳戶層級事件。 除了在 Azure 入口網站中查看活動記錄，或將它複製到 Log Analytics 工作區，您可以使用 Azure 監視器收集的其他資料進行分析，您可以建立記錄設定檔，將活動記錄封存至 Azure 儲存體帳戶，或將其串流至 事件中樞。

## <a name="archive-activity-log"></a>封存活動記錄
如果您想要保留記錄資料的時間超過90天（完全控制保留原則）以進行 audit、靜態分析或備份，則將活動記錄封存至儲存體帳戶會很有用。 如果您只需要保留您的事件90天（或更少），則不需要設定封存至儲存體帳戶，因為活動記錄事件會在 Azure 平臺中保留90天。

## <a name="stream-activity-log-to-event-hub"></a>將活動記錄串流至事件中樞
[Azure 事件中樞](/azure/event-hubs/)是一種資料串流平臺和事件內嵌服務，每秒可接收和處理數百萬個事件。 傳送至事件中樞的資料可以透過任何即時分析提供者或批次/儲存體配接器來轉換和儲存。 有兩種方式可以使用活動記錄的串流處理功能：
* **串流至協力廠商記錄與遙測系統** – 隨著時間進展，「Azure 事件中樞」串流會成為將「活動記錄」輸送到協力廠商 SIEM 與記錄分析解決方案的機制。
* **建置自訂遙測與記錄平台** – 如果您已經有自訂建置的遙測平台或正考慮建置一個，「事件中樞」所具備的高度可調整發佈訂閱特質將可讓您靈活擷取活動記錄。 

## <a name="prerequisites"></a>必要條件

### <a name="storage-account"></a>儲存體帳戶
如果您要封存活動記錄檔，您必須[建立儲存體帳戶](../../storage/common/storage-quickstart-create-account.md)（如果您還沒有的話）。 您不應該使用已儲存其他非監視資料的現有儲存體帳戶，讓您可以更有效地控制監視資料的存取。 不過，如果您也將記錄和計量封存到儲存體帳戶，您可以選擇使用相同的儲存體帳戶，將所有監視資料保留在中央位置。

儲存體帳戶不一定要和訂用帳戶發出記錄屬於相同的訂用帳戶，只要使用者有適當的設定可 RBAC 存取這兩個訂用帳戶即可。
> [!NOTE]
>  您目前無法將資料封存到位在安全虛擬網路後面的儲存體帳戶。

### <a name="event-hubs"></a>事件中樞
如果您要將活動記錄傳送至事件中樞，則需要[建立事件中樞](../../event-hubs/event-hubs-create.md)（如果還沒有的話）。 如果您先前已將活動記錄事件串流至此事件中樞命名空間，則會重複使用該事件中樞。

共用存取原則會定義串流機制具有的權限。 串流至事件中樞需要 [管理]、[傳送] 和 [接聽] 許可權。 您可以在事件中樞命名空間的 [設定] 索引標籤下，為 Azure 入口網站中的事件中樞命名空間建立或修改共用存取原則。

若要更新活動記錄檔記錄設定檔以包含串流，您必須具有該事件中樞授權規則的 ListKey 許可權。 事件中樞命名空間不一定要和發出記錄的訂用帳戶屬於相同的訂用帳戶，只要進行設定的使用者有這兩個訂用帳戶的適當 RBAC 存取權，而且這兩個訂用帳戶都屬於同一個 ADD 租用戶。

藉由[建立記錄檔設定檔](#create-a-log-profile)，將活動記錄串流至事件中樞。

## <a name="create-a-log-profile"></a>建立記錄檔設定檔
您可以使用**記錄設定檔**來定義您的 Azure 活動記錄檔的匯出方式。 每個 Azure 訂用帳戶只能有一個記錄檔設定檔。 這些設定可透過入口網站中 [活動記錄] 分頁的 [**匯出**] 選項來設定。 也可以[使用 Azure 監視器 REST API](https://msdn.microsoft.com/library/azure/dn931927.aspx)、PowerShell Cmdlet 或 CLI 以程式設計方式設定。

記錄檔設定檔會定義下列各項。

**活動記錄檔的傳送位置。** 目前可用的選項為 [儲存體帳戶] 或 [事件中樞]。

**應傳送哪些事件類別目錄。** 記錄檔設定檔和活動記錄檔事件中的*類別*意義不同。 在記錄設定檔中，*類別*代表作業類型（Write、Delete、Action）。 在活動記錄事件中，*類別*"* 屬性代表事件的來源或類型（例如，Administration、ServiceHealth 和 Alert）。

**應該匯出哪些區域（位置）。** 您應該包含所有位置，因為活動記錄檔中的許多事件都是全域事件。

**活動記錄應保留在儲存體帳戶中的時間長度。** 保留期為 0 天表示會永遠保留記錄。 否則，此值可以是介於1到365之間的任何天數。

如果已設定保留原則，但已停用儲存體帳戶中的記錄，則保留原則不會有任何作用。 保留原則是每天套用，因此在一天結束時 (UTC)，這一天超過保留原則的記錄會被刪除。 例如，如果您的保留原則為一天，在今天一開始，昨天之前的記錄會被刪除。 刪除程序會從 UTC 午夜開始，但是請注意，可能需要長達 24 小時的時間，記錄才會從您的儲存體帳戶中刪除。


> [!IMPORTANT]
> 如果未註冊 Microsoft Insights 資源提供者，您可能會在建立記錄檔設定檔時收到錯誤。 若要註冊此提供者，請參閱[Azure 資源提供者和類型](../../azure-resource-manager/resource-manager-supported-services.md)。


### <a name="create-log-profile-using-the-azure-portal"></a>使用 Azure 入口網站建立記錄檔設定檔

在 Azure 入口網站中，使用 [**匯出至事件中樞**] 選項來建立或編輯記錄設定檔。

1. 從 Azure 入口網站的 [**監視**] 功能表中，選取 [**匯出至事件中樞**]。

    ![入口網站中的匯出按鈕](media/activity-log-export/portal-export.png)

3. 在出現的分頁中，指定下列各項：
   * 具有要匯出之事件的區域。 您應該選取 [所有區域] 以確保您不會遺漏關鍵事件，因為活動記錄是全域（非區域）記錄檔，因此大部分的事件都沒有與其相關聯的區域。 
   * 如果您想要寫入儲存體帳戶：
       * 您想要儲存事件的儲存體帳戶。
       * 您想要在儲存體中保留這些事件的天數。 如果設定為 0 天會永遠保留記錄。
   * 如果您想要寫入事件中樞：
       * 您想要在其中建立事件中樞以串流處理這些事件的服務匯流排命名空間。

     ![匯出活動記錄檔刀鋒視窗](./media/activity-logs-overview/activity-logs-portal-export-blade.png)


4. 按一下 [儲存] 來儲存這些設定。 您的訂用帳戶時會立即套用設定。


### <a name="configure-log-profile-using-powershell"></a>使用 PowerShell 設定記錄檔設定檔

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

如果記錄檔設定檔已經存在，您必須先移除現有的記錄設定檔，然後再建立一個新的。

1. 使用 `Get-AzLogProfile` 來識別記錄設定檔是否存在。  如果記錄檔設定檔已存在，請注意*name*屬性。

1. 使用 `Remove-AzLogProfile` 透過 name 屬性中的值來移除記錄設定檔。

    ```powershell
    # For example, if the log profile name is 'default'
    Remove-AzLogProfile -Name "default"
    ```

3. 使用 `Add-AzLogProfile` 來建立新的記錄設定檔：

    ```powershell
    Add-AzLogProfile -Name my_log_profile -StorageAccountId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Storage/storageAccounts/my_storage -serviceBusRuleId /subscriptions/s1/resourceGroups/Default-ServiceBus-EastUS/providers/Microsoft.ServiceBus/namespaces/mytestSB/authorizationrules/RootManageSharedAccessKey -Location global,westus,eastus -RetentionInDays 90 -Category Write,Delete,Action
    ```

    | 屬性 | 必要項 | 描述 |
    | --- | --- | --- |
    | Name |是 |記錄檔設定檔的名稱。 |
    | StorageAccountId |否 |應儲存活動記錄之儲存體帳戶的資源識別碼。 |
    | serviceBusRuleId |否 |服務匯流排規則識別碼，您想要在其中建立事件中樞的服務匯流排命名空間。 這是具有下列格式的字串： `{service bus resource ID}/authorizationrules/{key name}`。 |
    | Location |是 |以逗號分隔的區域清單，其中列出您要收集的活動記錄檔事件的區域。 |
    | RetentionInDays |是 |在儲存體帳戶中應保留事件的天數，介於1到365之間。 值為 0 會無限期地儲存記錄。 |
    | 類別 |否 |以逗號分隔的類別清單，其中列出應該收集的事件類別。 可能的值為_Write_、 _Delete_和_Action_。 |

### <a name="example-script"></a>範例指令碼
以下是範例 PowerShell 腳本，用來建立記錄檔設定檔，以將活動記錄寫入至儲存體帳戶和事件中樞。

   ```powershell
   # Settings needed for the new log profile
   $logProfileName = "default"
   $locations = (Get-AzLocation).Location
   $locations += "global"
   $subscriptionId = "<your Azure subscription Id>"
   $resourceGroupName = "<resource group name your event hub belongs to>"
   $eventHubNamespace = "<event hub namespace>"

   # Build the service bus rule Id from the settings above
   $serviceBusRuleId = "/subscriptions/$subscriptionId/resourceGroups/$resourceGroupName/providers/Microsoft.EventHub/namespaces/$eventHubNamespace/authorizationrules/RootManageSharedAccessKey"

   # Build the storage account Id from the settings above
   $storageAccountId = "/subscriptions/$subscriptionId/resourceGroups/$resourceGroupName/providers/Microsoft.Storage/storageAccounts/$storageAccountName"

   Add-AzLogProfile -Name $logProfileName -Location $locations -ServiceBusRuleId $serviceBusRuleId
   ```


### <a name="configure-log-profile-using-azure-cli"></a>使用 Azure CLI 設定記錄檔設定檔

如果記錄設定檔已經存在，您必須先移除現有的記錄設定檔，然後再建立新的記錄設定檔。

1. 使用 `az monitor log-profiles list` 來識別記錄設定檔是否存在。
2. 使用 `az monitor log-profiles delete --name "<log profile name>` 透過 name 屬性中的值來移除記錄設定檔。
3. 使用 `az monitor log-profiles create` 來建立新的記錄設定檔：

   ```azurecli-interactive
   az monitor log-profiles create --name "default" --location null --locations "global" "eastus" "westus" --categories "Delete" "Write" "Action"  --enabled false --days 0 --service-bus-rule-id "/subscriptions/<YOUR SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventHub/namespaces/<EVENT HUB NAME SPACE>/authorizationrules/RootManageSharedAccessKey"
   ```

    | 屬性 | 必要項 | 描述 |
    | --- | --- | --- |
    | 名稱 |是 |記錄檔設定檔的名稱。 |
    | storage-account-id |是 |資源識別碼，活動記錄應該要儲存至此儲存體帳戶。 |
    | 位置 |是 |以空格分隔的區域清單，其中列出您要收集的活動記錄事件的區域。 您可以使用 `az account list-locations --query [].name` 來檢視您訂用帳戶的所有區域清單。 |
    | days |是 |應保留事件的天數，介於1到365之間。 值為 0 會無限期地 (永遠) 儲存記錄。  如果為零，則已啟用的參數應該設定為 false。 |
    |啟用 | 是 |True 或 False。  用來啟用或停用保留原則。  如果為 True，則 days 參數必須是大於 0 的值。
    | categories |是 |以空格分隔的類別清單，其中列出應收集的事件類別。 可能的值有 Write、Delete、Action。 |



## <a name="activity-log-schema"></a>活動記錄架構
無論是傳送至 Azure 儲存體或事件中樞，活動記錄資料將會以下列格式寫入 JSON。


> 寫入儲存體帳戶的活動記錄資料格式，在2018年11月1日變更為 JSON 行。 如需此格式變更的詳細資訊，請參閱[準備將格式變更 Azure 監視器封存到儲存體帳戶的資源記錄](diagnostic-logs-append-blobs.md)。

``` JSON
{
    "records": [
        {
            "time": "2015-01-21T22:14:26.9792776Z",
            "resourceId": "/subscriptions/s1/resourceGroups/MSSupportGroup/providers/microsoft.support/supporttickets/115012112305841",
            "operationName": "microsoft.support/supporttickets/write",
            "category": "Write",
            "resultType": "Success",
            "resultSignature": "Succeeded.Created",
            "durationMs": 2826,
            "callerIpAddress": "111.111.111.11",
            "correlationId": "c776f9f4-36e5-4e0e-809b-c9b3c3fb62a8",
            "identity": {
                "authorization": {
                    "scope": "/subscriptions/s1/resourceGroups/MSSupportGroup/providers/microsoft.support/supporttickets/115012112305841",
                    "action": "microsoft.support/supporttickets/write",
                    "evidence": {
                        "role": "Subscription Admin"
                    }
                },
                "claims": {
                    "aud": "https://management.core.windows.net/",
                    "iss": "https://sts.windows.net/72f988bf-86f1-41af-91ab-2d7cd011db47/",
                    "iat": "1421876371",
                    "nbf": "1421876371",
                    "exp": "1421880271",
                    "ver": "1.0",
                    "http://schemas.microsoft.com/identity/claims/tenantid": "1e8d8218-c5e7-4578-9acc-9abbd5d23315 ",
                    "http://schemas.microsoft.com/claims/authnmethodsreferences": "pwd",
                    "http://schemas.microsoft.com/identity/claims/objectidentifier": "2468adf0-8211-44e3-95xq-85137af64708",
                    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn": "admin@contoso.com",
                    "puid": "20030000801A118C",
                    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier": "9vckmEGF7zDKk1YzIY8k0t1_EAPaXoeHyPRn6f413zM",
                    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname": "John",
                    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname": "Smith",
                    "name": "John Smith",
                    "groups": "cacfe77c-e058-4712-83qw-f9b08849fd60,7f71d11d-4c41-4b23-99d2-d32ce7aa621c,31522864-0578-4ea0-9gdc-e66cc564d18c",
                    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name": " admin@contoso.com",
                    "appid": "c44b4083-3bq0-49c1-b47d-974e53cbdf3c",
                    "appidacr": "2",
                    "http://schemas.microsoft.com/identity/claims/scope": "user_impersonation",
                    "http://schemas.microsoft.com/claims/authnclassreference": "1"
                }
            },
            "level": "Information",
            "location": "global",
            "properties": {
                "statusCode": "Created",
                "serviceRequestId": "50d5cddb-8ca0-47ad-9b80-6cde2207f97c"
            }
        }
    ]
}
```
下表描述此 JSON 中的元素。

| 元素名稱 | 描述 |
| --- | --- |
| time |處理與事件對應之要求的Azure 服務產生事件時的時間戳記。 |
| ResourceId |受影響資源的資源識別碼。 |
| operationName |作業名稱。 |
| category |事件的類別，例如 寫入、讀取、動作。 |
| resultType |結果的類型，例如 成功、失敗、開始 |
| resultSignature |取決於資源類型。 |
| durationMs |作業的持續時間 (以毫秒為單位) |
| callerIpAddress |已執行作業的使用者的 IP 地址，根據可用性的 UPN 宣告或 SPN 宣告。 |
| correlationId |通常是字串格式的 GUID。 具有相同 correlationId、屬於同一 uber 動作的事件。 |
| 身分識別 |描述授權和宣告的 JSON blob。 |
| 授權 |事件的 RBAC 屬性的 blob。 通常包括 action、role 和 scope 屬性。 |
| 層級 |事件的層級。 下列其中一個值：「_重大_」、「_錯誤_」、「_警告_」、「資訊」和「_詳細_ _資訊_」 |
| location |在 location 發生 (或全球) 的區域。 |
| properties |描述事件詳細資料的一組 `<Key, Value>` 配對 (也就是字典)。 |

> [!NOTE]
> 這些屬性的屬性和使用方式會隨著資源而有所不同。



## <a name="next-steps"></a>後續步驟

* [深入了解活動記錄](../../azure-resource-manager/resource-group-audit.md)
* [將活動記錄檔收集到 Azure 監視器記錄檔](activity-log-collect.md)
