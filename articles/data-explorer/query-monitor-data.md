---
title: 使用 Azure 資料總管查詢 Azure 監視器中的資料（預覽）
description: 在本主題中，您可以使用 Application Insights 和 Log Analytics 建立用於跨產品查詢的 Azure 資料總管 proxy，以在 Azure 監視器中查詢資料。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 07/10/2019
ms.openlocfilehash: 43d91bff6b8b67e79a9549c1524f918166c9adc4
ms.sourcegitcommit: f3f4ec75b74124c2b4e827c29b49ae6b94adbbb7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/12/2019
ms.locfileid: "70934002"
---
# <a name="query-data-in-azure-monitor-using-azure-data-explorer-preview"></a>使用 Azure 資料總管查詢 Azure 監視器中的資料（預覽）

Azure 資料總管 proxy 叢集（ADX Proxy）是一個實體，可讓您在[Azure 監視器](/azure/azure-monitor/)服務中的 Azure 資料總管、 [Application Insights （AI）](/azure/azure-monitor/app/app-insights-overview)和[Log Analytics （LA）](/azure/azure-monitor/platform/data-platform-logs)之間執行跨產品查詢。 您可以將 Azure 監視器 Log Analytics 工作區或 Application Insights apps 對應為 proxy 叢集。 接著，您可以使用 Azure 資料總管工具來查詢 proxy 叢集，並在跨叢集查詢中加以參考。 本文說明如何連線到 proxy 叢集、將 proxy 叢集新增至 Azure 資料總管 Web UI，以及從 Azure 資料總管對您的 AI 應用程式或 LA 工作區執行查詢。

Azure 資料總管 proxy 流程： 

![ADX proxy 流程](media/adx-proxy/adx-proxy-flow.png)

## <a name="prerequisites"></a>必要條件

> [!NOTE]
> ADX Proxy 處於預覽模式。 若要啟用這項功能，請洽詢[ADXProxy](mailto:adxproxy@microsoft.com)小組。

## <a name="connect-to-the-proxy"></a>連接到 proxy

1. *連線*到 Log Analytics 或 Application Insights 叢集之前，請先確認您的 Azure 資料總管原生叢集（例如 [說明叢集]）出現在左側功能表上。

    ![ADX 原生叢集](media/adx-proxy/web-ui-help-cluster.png)

1. 在 Azure 資料總管 UI （ https://dataexplorer.azure.com/clusters) ）中，選取 [**新增**叢集]。

1. 在 [**新增**叢集] 視窗中：

    * 將 URL 新增至 LA 或 AI 叢集。 例如：`https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>`

    * 選取 [新增]。

    ![新增叢集](media/adx-proxy/add-cluster.png)

    如果您將連接新增至多個 proxy 叢集，請為每個叢集提供不同的名稱。 否則，它們會在左窗格中具有相同的名稱。

1. 建立連線之後，您的 LA 或 AI 叢集將會出現在左窗格中，其中包含您的原生 ADX 叢集。 

    ![Log Analytics 和 Azure 資料總管叢集](media/adx-proxy/la-adx-clusters.png)

## <a name="run-queries"></a>執行查詢

您可以使用 Kusto Explorer、ADX web Explorer、Jupyter Kqlmagic 或 REST API 來查詢 proxy 叢集。 

> [!TIP]
> * 資料庫名稱應該與 proxy 叢集中指定的資源名稱相同。 名稱區分大小寫。
> * 在跨叢集查詢中，請確定 Application Insights 應用程式和 Log Analytics 工作區的命名是正確的。
>     * 如果名稱包含特殊字元，則會以 proxy 叢集名稱中的 URL 編碼來取代。 
>     * 如果名稱包含不符合[KQL 識別碼名稱規則](/azure/kusto/query/schema-entities/entity-names)的字元，則會以虛線 **-** 字元來取代它們。

### <a name="query-against-the-native-azure-data-explorer-cluster"></a>針對原生 Azure 資料總管叢集進行查詢 

在您的 Azure 資料總管叢集上執行查詢（例如「說明」叢*集中的* *StormEvents*資料表）。 執行查詢時，請確認已在左窗格中選取您的原生 Azure 資料總管叢集。

```kusto
StormEvents | take 10 // Demonstrate query through the native ADX cluster
```

![查詢 StormEvents 資料表](media/adx-proxy/query-adx.png)

### <a name="query-against-your-la-or-ai-cluster"></a>針對您的 LA 或 AI 叢集進行查詢

當您在 LA 或 AL 叢集上執行查詢時，請確認已在左窗格中選取您的 LA 或 AI 叢集。 

```kusto
Perf | take 10 // Demonstrate query through the proxy on the LA workspace
```

![查詢 LA 工作區](media/adx-proxy/query-la.png)

### <a name="query-your-la-or-ai-cluster-from-the-adx-proxy"></a>從 ADX proxy 查詢您的 LA 或 AI 叢集  

當您從 proxy 在 LA 或 AI 叢集上執行查詢時，請確認已在左窗格中選取您的 ADX 原生叢集。 下列範例示範使用原生 ADX 叢集的 LA 工作區查詢

```kusto
cluster('https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>').database('<workspace-name').Perf
| take 10 
```

![從 Azure 資料總管 proxy 查詢](media/adx-proxy/query-adx-proxy.png)

### <a name="cross-query-of-la-or-ai-cluster-and-the-adx-cluster-from-the-adx-proxy"></a>從 ADX proxy 交叉查詢 LA 或 AI 叢集和 ADX 叢集 

當您從 proxy 執行跨叢集查詢時，請確認已在左窗格中選取您的 ADX native cluster。 下列範例示範如何將 ADX 叢集資料表（使用`union`）與 LA 工作區結合。

```kusto
union StormEvents, cluster('https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>').database('<workspace-name>').Perf
| take 10 
```

```kusto
let CL1 = 'https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>';
union <ADX table>, cluster(CL1).database(<workspace-name>).<table name>
```

![從 Azure 資料總管 proxy 進行交叉查詢](media/adx-proxy/cross-query-adx-proxy.png)

使用運算子（而不是 union）可能需要在[`hint`](/azure/kusto/query/joinoperator#join-hints) Azure 資料總管原生叢集（而不是 proxy）上執行。 [ `join` ](/azure/kusto/query/joinoperator) 

## <a name="additional-syntax-examples"></a>其他語法範例

呼叫 Application Insights （AI）或 Log Analytics （LA）叢集時，可以使用下列語法選項：

|語法描述  |Application Insights  |Log Analytics  |
|----------------|---------|---------|
| 叢集中只包含此訂用帳戶中已定義資源的資料庫（**建議用於跨叢集查詢**） |   cluster （`https://ade.applicationinsights.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.insights/components/<ai-app-name>').database('<ai-app-name>`） | cluster （`https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>').database('<workspace-name>`）     |
| 此訂用帳戶中包含所有應用程式/工作區的叢集    |     cluster （`https://ade.applicationinsights.io/subscriptions/<subscription-id>`）    |    cluster （`https://ade.loganalytics.io/subscriptions/<subscription-id>`）     |
|此叢集包含訂用帳戶中的所有應用程式/工作區，且為此資源群組的成員    |   cluster （`https://ade.applicationinsights.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>`）      |    cluster （`https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>`）      |
|僅包含此訂用帳戶中已定義資源的叢集      |    cluster （`https://ade.applicationinsights.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.insights/components/<ai-app-name>`）    |  cluster （`https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>`）     |

## <a name="next-steps"></a>後續步驟

[撰寫查詢](write-queries.md)
