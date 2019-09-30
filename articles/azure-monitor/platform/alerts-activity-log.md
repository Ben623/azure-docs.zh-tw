---
title: 在 Azure 監視器中建立、查看和管理活動記錄警示
description: 使用 Azure 入口網站、Azure Resource Manager 範本和 Azure PowerShell 來建立活動記錄警示。
author: rboucher
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 06/25/2019
ms.author: robb
ms.openlocfilehash: e93b6f9c74bb44cb7341f5763261ecb699432ae2
ms.sourcegitcommit: 5f0f1accf4b03629fcb5a371d9355a99d54c5a7e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/30/2019
ms.locfileid: "71675267"
---
# <a name="create-view-and-manage-activity-log-alerts-by-using-azure-monitor"></a>使用 Azure 監視器來建立、查看和管理活動記錄警示  

## <a name="overview"></a>總覽
活動記錄警示是發生符合警示中指定條件的新活動記錄事件時所啟動的警示。

這些警示適用于 Azure 資源, 並可使用 Azure Resource Manager 範本來建立。 也可以在 Azure 入口網站中將它們建立、更新或刪除。 一般來說, 您會建立活動記錄警示, 以便在 Azure 訂用帳戶中的資源發生特定變更時收到通知。 警示的範圍通常是特定資源群組或資源。 例如, 您可能想要在範例資源群組**myProductionResourceGroup**中的任何虛擬機器遭到刪除時收到通知。 或者, 您可能想要在有任何新角色指派給訂用帳戶中的使用者時收到通知。

> [!IMPORTANT]
> 服務健康情況通知的警示無法透過建立活動記錄警示的介面來建立。 若要深入瞭解如何建立和使用服務健康情況通知, 請參閱[在服務健康情況通知上接收活動記錄警示](alerts-activity-log-service-notifications.md)。

當您建立警示規則時, 請確定下列事項:

- 範圍中的訂用帳戶與建立警示的訂用帳戶不同。
- 準則必須是設定警示的 [層級]、[狀態]、[呼叫者]、[資源群組]、[資源識別碼] 或 [資源類型] 事件類別目錄。
- 在警示設定 JSON 中, 沒有任何「anyOf」條件或 nested 條件。 基本上, 只允許一個「allOf」條件, 而不會有進一步的「allOf」或「anyOf」條件。
- 當類別為「系統管理」時, 您必須在警示中指定至少一個上述準則。 您不能建立會在每次活動記錄中建立事件時即啟動的警示。


## <a name="azure-portal"></a>Azure 入口網站

您可以使用 Azure 入口網站來建立和修改活動記錄警示規則。 此體驗與 Azure 活動記錄整合, 以確保能針對感針對性的特定事件建立順暢的警示。

### <a name="create-with-the-azure-portal"></a>使用 Azure 入口網站建立

請使用下列程式。

1. 在 Azure 入口網站中, 選取 **監視** >  **警示**。
2. 在 [**警示**] 視窗的左上角, 選取 [**新增警示規則**]。

     ![新增警示規則](media/alerts-activity-log/AlertsPreviewOption.png)

     [建立規則] 視窗隨即出現。

      ![新增警示規則選項](media/alerts-activity-log/create-new-alert-rule-options.png)

3. 在 [**定義警示條件**] 底下, 提供下列資訊, 然後選取 [**完成**]:

   - **警示目標:** 若要查看並選取新警示的目標, 請使用 [依**訂** / 用帳戶篩選依據**資源類型**]。 從顯示的清單中選取資源或資源群組。

     > [!NOTE]
     > 
     > 您只能針對活動記錄信號選取[Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md)追蹤的資源、資源群組或整個訂用帳戶。 

     **警示目標範例檢視**

     ![選取目標](media/alerts-activity-log/select-target.png)

   - 在 [**目標準則**] 底下, 選取 [**新增準則**]。 系統會顯示目標的所有可用信號, 其中包括來自不同類別的**活動記錄**。 類別目錄名稱會附加至**監視器服務**名稱。

   - 從類型 [活動記錄] 的各種可能作業顯示的清單中選取訊號。

     您可以選取此目標訊號的記錄歷程記錄時間軸和對應的警示邏輯：

     **新增準則畫面**

     ![新增準則](media/alerts-activity-log/add-criteria.png)

     - **歷程記錄時間**：所選取作業的可用事件可以在過去6、12或24小時或過去一周內繪製。

     - **警示邏輯**：

       - **事件層級**:事件的嚴重性層級:詳細資訊_資訊_ _警告_ _錯誤_或_嚴重_。
       - **狀態**：事件的狀態:已啟動_失敗_或_成功_。
       - **事件起始者**：也稱為呼叫端。 執行作業之使用者的電子郵件地址或 Azure Active Directory 識別碼。

       這個範例信號圖表已套用警示邏輯:

       ![選取的準則](media/alerts-activity-log/criteria-selected.png)

4. 在 [**定義警示詳細資料**] 底下, 提供下列詳細資料:

    - **警示規則名稱**:新警示規則的名稱。
    - **描述**：新警示規則的描述。
    - **將警示儲存到資源群組**:選取您要儲存此新規則的資源群組。

5. 在 [動作群組] 下，從下拉式功能表中指定您想要指派給此新警示規則的動作群組。 或者,[建立新的動作群組](../../azure-monitor/platform/action-groups.md), 並將它指派給新的規則。 若要建立新的群組, 請選取 [ **+ 新增群組**]。

6. 若要在建立規則後加以啟用, 請針對 [在**建立時啟用規則**] 選項選取 **[是]** 。
7. 選取 [**建立警示規則**]。

    隨即會建立活動記錄的新警示規則, 並在視窗的右上角顯示確認訊息。

    您可以啟用、停用、編輯或刪除規則。 深入瞭解如何管理活動記錄規則。


若要瞭解在活動記錄中可建立警示規則的條件, 簡單的比喻是透過[Azure 入口網站中的活動記錄](activity-log-view.md#azure-portal)來探索或篩選事件。 在 [ **Azure 監視器-活動記錄**檔] 畫面中, 您可以篩選或尋找必要事件, 然後使用 [**新增活動記錄警示**] 按鈕來建立警示。 然後依照前面所示的步驟4到7進行。
    
 ![從活動記錄新增警示](media/alerts-activity-log/add-activity-log.png)
    

### <a name="view-and-manage-in-the-azure-portal"></a>在 Azure 入口網站中查看和管理

1. 在 Azure 入口網站中, 選取 **監視** >  **警示**。 選取視窗左上角的 [**管理警示規則**]。

    ![管理警示規則](media/alerts-activity-log/manage-alert-rules.png)

    可用規則清單隨即出現。

2. 搜尋要修改的活動記錄規則。

    ![搜尋活動記錄警示規則](media/alerts-activity-log/searth-activity-log-rule-to-edit.png)

    您可以使用可用的篩選器、_訂_用帳戶、_資源群組_、_資源_、_信號類型_或_狀態_, 來尋找您想要編輯的活動規則。

   > [!NOTE]
   > 
   > 您只能編輯 [**描述**]、[**目標條件**] 和 [**動作群組**]。

3. 選取規則, 然後按兩下以編輯規則選項。 進行必要的變更, 然後選取 [**儲存**]。

   ![管理警示規則](media/alerts-activity-log/activity-log-rule-edit-page.png)

4. 您可以啟用、停用或刪除規則。 選取規則 (如步驟2中所述) 之後, 請在視窗頂端選取適當的選項。


## <a name="azure-resource-manager-template"></a>Azure Resource Manager 範本
若要使用 Azure Resource Manager 範本建立活動記錄警示, 您要建立類型`microsoft.insights/activityLogAlerts`的資源。 然後要填入所有相關的屬性。 以下是建立活動記錄警示的範本:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "activityLogAlertName": {
      "type": "string",
      "metadata": {
        "description": "Unique name (within the Resource Group) for the Activity log alert."
      }
    },
    "activityLogAlertEnabled": {
      "type": "bool",
      "defaultValue": true,
      "metadata": {
        "description": "Indicates whether or not the alert is enabled."
      }
    },
    "actionGroupResourceId": {
      "type": "string",
      "metadata": {
        "description": "Resource Id for the Action group."
      }
    }
  },
  "resources": [   
    {
      "type": "Microsoft.Insights/activityLogAlerts",
      "apiVersion": "2017-04-01",
      "name": "[parameters('activityLogAlertName')]",      
      "location": "Global",
      "properties": {
        "enabled": "[parameters('activityLogAlertEnabled')]",
        "scopes": [
            "[subscription().id]"
        ],        
        "condition": {
          "allOf": [
            {
              "field": "category",
              "equals": "Administrative"
            },
            {
              "field": "operationName",
              "equals": "Microsoft.Resources/deployments/write"
            },
            {
              "field": "resourceType",
              "equals": "Microsoft.Resources/deployments"
            }
          ]
        },
        "actions": {
          "actionGroups":
          [
            {
              "actionGroupId": "[parameters('actionGroupResourceId')]"
            }
          ]
        }
      }
    }
  ]
}
```
先前的範例 JSON 可儲存為 (例如 Sampleactivitylogalert.json), 以供本逐步解說之用, 而且可以使用[Azure 入口網站中的 Azure Resource Manager](../../azure-resource-manager/resource-group-template-deploy-portal.md)來進行部署。

> [!NOTE]
> 最多可能需要5分鐘的時間, 新的活動記錄警示規則才會變成作用中狀態。

## <a name="rest-api"></a>REST API 
[Azure 監視器活動記錄警示 API](https://docs.microsoft.com/rest/api/monitor/activitylogalerts)是一個 REST API。 它與 Azure Resource Manager REST API 完全相容。 您可以使用 Resource Manager Cmdlet 或 Azure CLI, 透過 PowerShell 來使用它。

## <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

### <a name="deploy-the-resource-manager-template-with-powershell"></a>使用 PowerShell 部署 Resource Manager 範本
若要使用 PowerShell 來部署先前[Azure Resource Manager 範本](#azure-resource-manager-template)一節中所示的範例 Resource Manager 範本, 請使用下列命令:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName "myRG" -TemplateFile sampleActivityLogAlert.json -TemplateParameterFile sampleActivityLogAlert.parameters.json
```

其中 Sampleactivitylogalert.json 包含針對建立警示規則所需參數所提供的值。

### <a name="use-activity-log-powershell-cmdlets"></a>使用活動記錄 PowerShell Cmdlet

活動記錄警示有專用的 PowerShell Cmdlet 可供使用：

- [AzActivityLogAlert](https://docs.microsoft.com/powershell/module/az.monitor/Set-AzActivityLogAlert):建立新的活動記錄警示, 或更新現有的活動記錄警示。
- [Get-AzActivityLogAlert](https://docs.microsoft.com/powershell/module/az.monitor/Get-AzActivityLogAlert):取得一或多個活動記錄警示資源。
- [啟用-AzActivityLogAlert](https://docs.microsoft.com/powershell/module/az.monitor/Enable-AzActivityLogAlert):啟用現有的活動記錄警示, 並設定其標記。
- [停用-AzActivityLogAlert](https://docs.microsoft.com/powershell/module/az.monitor/Disable-AzActivityLogAlert):停用現有的活動記錄警示, 並設定其標記。
- [移除-AzActivityLogAlert](https://docs.microsoft.com/powershell/module/az.monitor/Remove-AzActivityLogAlert):移除活動記錄警示。

## <a name="azure-cli"></a>Azure CLI

[az monitor activity-log alert](https://docs.microsoft.com/cli/azure/monitor/activity-log/alert) 集合底下的專用 Azure CLI 命令可用來管理活動記錄警示規則。

若要建立新的活動記錄警示規則, 請依此順序使用下列命令:

1. [az monitor activity-log alert create](https://docs.microsoft.com/cli/azure/monitor/activity-log/alert#az-monitor-activity-log-alert-create)：建立新的活動記錄警示規則資源。
1. [az monitor activity-log alert scope](https://docs.microsoft.com/cli/azure/monitor/activity-log/alert/scope)：新增所建立活動記錄警示規則的範圍。
1. [az monitor activity-log alert action-group](https://docs.microsoft.com/cli/azure/monitor/activity-log/alert/action-group)：將動作群組新增至活動記錄警示規則。

若要取出一個活動記錄警示規則資源, 請使用 Azure CLI [命令 az monitor activity-log alert](https://docs.microsoft.com/cli/azure/monitor/activity-log/alert#az-monitor-activity-log-alert-show
)show。 若要查看資源群組中的所有活動記錄警示規則資源, 請使用[az monitor activity-log alert list](https://docs.microsoft.com/cli/azure/monitor/activity-log/alert#az-monitor-activity-log-alert-list)。
活動記錄警示規則資源可以藉由使用 Azure CLI 命令[az monitor activity-log alert delete](https://docs.microsoft.com/cli/azure/monitor/activity-log/alert#az-monitor-activity-log-alert-delete)來移除。

## <a name="next-steps"></a>後續步驟

- 深入瞭解[適用于活動記錄的 webhook 架構](../../azure-monitor/platform/activity-log-alerts-webhook.md)。
- 閱讀[活動記錄的總覽](../../azure-monitor/platform/activity-log-alerts.md)。
- 深入了解[動作群組](../../azure-monitor/platform/action-groups.md)。  
- 深入了解[服務健康狀態通知](../../azure-monitor/platform/service-notifications.md)。
