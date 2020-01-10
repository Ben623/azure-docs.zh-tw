---
title: 在 Azure 資源中檢視活動記錄是否有 RBAC 變更 | Microsoft Docs
description: 檢視活動記錄在過去 90 天是否有 Azure 資源之角色型存取控制 (RBAC) 的變更。
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.assetid: 2bc68595-145e-4de3-8b71-3a21890d13d9
ms.service: role-based-access-control
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/02/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 753c626fe44193b83cbd992f225fe01c2ff67f89
ms.sourcegitcommit: 380e3c893dfeed631b4d8f5983c02f978f3188bf
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/08/2020
ms.locfileid: "75744814"
---
# <a name="view-activity-logs-for-rbac-changes-to-azure-resources"></a>檢視活動記錄中 Azure 資源的各種 RBAC 變更

有時，您會需要 Azure 資源之角色型存取控制 (RBAC) 變更的相關資訊，例如用來進行稽核或疑難排解。 每當有人對您訂用帳戶內的角色指派或角色定義進行變更時，這些變更都會記錄在 [Azure 活動記錄](../azure-monitor/platform/platform-logs-overview.md)中。 您可以檢視活動記錄來查看過去 90 天的所有 RBAC 變更。

## <a name="operations-that-are-logged"></a>記錄的作業

以下是在活動記錄中記錄的 RBAC 相關作業：

- 建立角色指派
- 刪除角色指派
- 建立或更新自訂角色定義
- 刪除自訂角色定義

## <a name="azure-portal"></a>Azure Portal

開始使用最簡單的方式是使用 Azure 入口網站檢視活動記錄。 下列螢幕擷取畫面顯示已篩選成顯示角色指派和角色定義作業的活動記錄範例。 它也包含將記錄下載為 CSV 檔案的連結。

![使用入口網站的活動記錄 - 螢幕擷取畫面](./media/change-history-report/activity-log-portal.png)

入口網站中的活動記錄有數個篩選條件。 以下是 RBAC 相關的篩選條件：

|篩選  |值  |
|---------|---------|
|事件類別目錄     | <ul><li>管理</li></ul>         |
|作業     | <ul><li>建立角色指派</li> <li>刪除角色指派</li> <li>建立或更新自訂角色定義</li> <li>刪除自訂角色定義</li></ul>      |


如需有關活動記錄的詳細資訊，請參閱[檢視活動記錄中的事件](/azure/azure-resource-manager/resource-group-audit?toc=%2fazure%2fmonitoring-and-diagnostics%2ftoc.json)。

## <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [az-powershell-update](../../includes/updated-for-az.md)]

若要使用 Azure PowerShell 檢視活動記錄，請使用 [Get-AzLog](/powershell/module/Az.Monitor/Get-AzLog) 命令。

此命令列出過去 7 天在訂用帳戶中的所有角色指派變更：

```azurepowershell
Get-AzLog -StartTime (Get-Date).AddDays(-7) | Where-Object {$_.Authorization.Action -like 'Microsoft.Authorization/roleAssignments/*'}
```

此命令列出過去 7 天在資源群組中的所有角色定義變更：

```azurepowershell
Get-AzLog -ResourceGroupName pharma-sales -StartTime (Get-Date).AddDays(-7) | Where-Object {$_.Authorization.Action -like 'Microsoft.Authorization/roleDefinitions/*'}
```

此命令列出過去 7 天在訂用帳戶中的所有角色指派及角色定義變更，並且以清單顯示結果：

```azurepowershell
Get-AzLog -StartTime (Get-Date).AddDays(-7) | Where-Object {$_.Authorization.Action -like 'Microsoft.Authorization/role*'} | Format-List Caller,EventTimestamp,{$_.Authorization.Action},Properties
```

```Example
Caller                  : alain@example.com
EventTimestamp          : 4/20/2018 9:18:07 PM
$_.Authorization.Action : Microsoft.Authorization/roleAssignments/write
Properties              :
                          statusCode     : Created
                          serviceRequestId: 11111111-1111-1111-1111-111111111111

Caller                  : alain@example.com
EventTimestamp          : 4/20/2018 9:18:05 PM
$_.Authorization.Action : Microsoft.Authorization/roleAssignments/write
Properties              :
                          requestbody    : {"Id":"22222222-2222-2222-2222-222222222222","Properties":{"PrincipalId":"33333333-3333-3333-3333-333333333333","RoleDefinitionId":"/subscriptions/00000000-0000-0000-0000-000000000000/providers
                          /Microsoft.Authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c","Scope":"/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales"}}

```

## <a name="azure-cli"></a>Azure CLI

若要使用 Azure CLI 檢視活動記錄，請使用 [az monitor activity-log list](/cli/azure/monitor/activity-log#az-monitor-activity-log-list) 命令。

此命令列出自開始時間起，在資源群組中的活動記錄：

```azurecli
az monitor activity-log list --resource-group pharma-sales --start-time 2018-04-20T00:00:00Z
```

此命令列出自開始時間起，[授權] 資源提供者的活動記錄：

```azurecli
az monitor activity-log list --resource-provider "Microsoft.Authorization" --start-time 2018-04-20T00:00:00Z
```

## <a name="azure-monitor-logs"></a>Azure 監視器記錄

[Azure 監視器記錄](../log-analytics/log-analytics-overview.md)是您可以用來收集並分析所有 Azure 資源之 RBAC 變更的另一種工具。 Azure 監視器記錄具有下列優點：

- 撰寫複雜的查詢和邏輯
- 整合警示、Power BI 和其他工具
- 儲存更長保留期限的資料
- 交互參考其他記錄，例如安全性、虛擬機器和自訂

開始使用的基本步驟如下：

1. [建立 Log Analytics 工作區](../azure-monitor/learn/quick-create-workspace.md)。

1. 為您的工作區[設定活動記錄分析解決方案](../azure-monitor/platform/activity-log-collect.md#activity-logs-analytics-monitoring-solution)。

1. [檢視活動記錄](../azure-monitor/platform/activity-log-collect.md#activity-logs-analytics-monitoring-solution)。 流覽至 [活動記錄分析解決方案] [總覽] 頁面的快速方式是按一下 [ **Log Analytics** ] 選項。

   ![入口網站中的 Azure 監視器記錄 選項](./media/change-history-report/azure-log-analytics-option.png)

1. 選擇性使用 [記錄搜尋](../log-analytics/log-analytics-log-search.md) 頁面或[進階分析入口網站](../azure-monitor/log-query/get-started-portal.md)來查詢和檢視記錄。 如需有關這兩個選項的詳細資訊，請參閱[記錄搜尋頁面或進階分析入口網站](../azure-monitor/log-query/portals.md)。

以下查詢會傳回由目標資源提供者所組織的新角色指派：

```
AzureActivity
| where TimeGenerated > ago(60d) and OperationNameValue startswith "Microsoft.Authorization/roleAssignments/write" and ActivityStatus == "Succeeded"
| parse ResourceId with * "/providers/" TargetResourceAuthProvider "/" *
| summarize count(), makeset(Caller) by TargetResourceAuthProvider
```

以下查詢會傳回以圖表顯示的角色指派變更：

```
AzureActivity
| where TimeGenerated > ago(60d) and OperationNameValue startswith "Microsoft.Authorization/roleAssignments"
| summarize count() by bin(TimeGenerated, 1d), OperationNameValue
| render timechart
```

![使用進階分析入口網站的活動記錄 - 螢幕擷取畫面](./media/change-history-report/azure-log-analytics.png)

## <a name="next-steps"></a>後續步驟
* [檢視活動記錄中的事件](/azure/azure-resource-manager/resource-group-audit?toc=%2fazure%2fmonitoring-and-diagnostics%2ftoc.json)
* [使用 Azure 活動記錄監視訂用帳戶活動](/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs)
