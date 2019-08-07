---
title: 使用 Azure 監視器記錄來監視 Azure HDInsight 叢集
description: 瞭解如何使用 Azure 監視器記錄來監視在 HDInsight 叢集中執行的作業。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 08/05/2019
ms.openlocfilehash: 7d015f485a51ae1f929e2ecaf1a05811d21594a2
ms.sourcegitcommit: c8a102b9f76f355556b03b62f3c79dc5e3bae305
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/06/2019
ms.locfileid: "68816025"
---
# <a name="use-azure-monitor-logs-to-monitor-hdinsight-clusters"></a>使用 Azure 監視器記錄來監視 HDInsight 叢集

瞭解如何啟用 Azure 監視器記錄來監視 HDInsight 中的 Hadoop 叢集作業, 以及如何新增 HDInsight 監視解決方案。

[Azure 監視器記錄](../log-analytics/log-analytics-overview.md)是 Azure 監視器中的一項服務, 可監視您的雲端和內部部署環境, 以維護其可用性和效能。 它會收集您的雲端和內部部署環境中的資源所產生的資料，以及從其他監視工具提供橫跨多個來源的分析。

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

如果您沒有 Azure 訂用帳戶，請在開始之前先[建立免費帳戶](https://azure.microsoft.com/free/)。

## <a name="prerequisites"></a>必要條件

* **Log Analytics 工作區**。 您可以將此工作區視為唯一的 Azure 監視器記錄環境, 其具有自己的資料存放庫、資料來源和解決方案。 如需指示，請參閱[建立 Log Analytics 工作區](../azure-monitor/learn/quick-collect-azurevm.md#create-a-workspace)。

* **Azure HDInsight 叢集**。 目前, 您可以使用 Azure 監視器記錄搭配下列 HDInsight 叢集類型:

  * Hadoop
  * hbase
  * 互動式查詢
  * Kafka
  * Spark
  * Storm

  如需如何建立 HDInsight 叢集的指示，請參閱[開始使用 Azure HDInsight](hadoop/apache-hadoop-linux-tutorial-get-started.md)。  

* **Azure PowerShell Az 模組**。  請參閱[新的 Azure PowerShell Az 模組簡介](https://docs.microsoft.com/powershell/azure/new-azureps-module-az)。

> [!NOTE]  
> 建議您將 HDInsight 叢集和 Log Analytics 工作區放在相同的區域中，以提升效能。 在所有 Azure 區域中都無法使用 Azure 監視器記錄。

## <a name="enable-azure-monitor-logs-by-using-the-portal"></a>使用入口網站啟用 Azure 監視器記錄

在本節中，您會設定現有 HDInsight Hadoop 叢集，以使用 Azure Log Analytics 工作區來監視作業、偵錯記錄等等。

1. 從 [ [Azure 入口網站](https://portal.azure.com/)] 中, 選取您的叢集。  請參閱[列出和顯示叢集](./hdinsight-administer-use-portal-linux.md#showClusters)以取得指示。 叢集會在新的入口網站頁面中開啟。

1. 在左邊的 [監視]下方，選取 [Operations Management Suite]。

1. 從主要檢視中的 [OMS 監視] 下方，選取 [啟用]。

1. 從 [選取工作區] 下拉式清單中，選取現有的 Log Analytics 工作區。

1. 選取 [ **儲存**]。  需要一些時間來儲存設定。

    ![針對 HDInsight 叢集啟用監視](./media/hdinsight-hadoop-oms-log-analytics-tutorial/hdinsight-enable-monitoring.png "針對 HDInsight 叢集啟用監視")

## <a name="enable-azure-monitor-logs-by-using-azure-powershell"></a>使用 Azure PowerShell 啟用 Azure 監視器記錄

您可以使用 Azure PowerShell Az module [AzHDInsightOperationsManagementSuite](https://docs.microsoft.com/powershell/module/az.hdinsight/enable-azhdinsightoperationsmanagementsuite) Cmdlet 來啟用 Azure 監視器記錄。

```powershell
# Enter user information
$resourceGroup = "<your-resource-group>"
$cluster = "<your-cluster>"
$LAW = "<your-Log-Analytics-workspace>"
# End of user input

# obtain workspace id for defined Log Analytics workspace
$WorkspaceId = (Get-AzOperationalInsightsWorkspace -ResourceGroupName $resourceGroup -Name $LAW).CustomerId

# obtain primary key for defined Log Analytics workspace
$PrimaryKey = (Get-AzOperationalInsightsWorkspace -ResourceGroupName $resourceGroup -Name $LAW | Get-AzOperationalInsightsWorkspaceSharedKeys).PrimarySharedKey

# Enables Operations Management Suite
Enable-AzHDInsightOperationsManagementSuite -ResourceGroupName $resourceGroup -Name $cluster -WorkspaceId $WorkspaceId -PrimaryKey $PrimaryKey
```

若要停用, 請使用[AzHDInsightOperationsManagementSuite](https://docs.microsoft.com/powershell/module/az.hdinsight/disable-azhdinsightoperationsmanagementsuite) Cmdlet:

```powershell
Disable-AzHDInsightOperationsManagementSuite -Name "<your-cluster>"
```

## <a name="install-hdinsight-cluster-management-solutions"></a>安裝 HDInsight 叢集管理解決方案

HDInsight 提供叢集特定的管理解決方案, 可讓您針對 Azure 監視器記錄進行新增。 [管理解決方案](../log-analytics/log-analytics-add-solutions.md)可將功能新增至 Azure 監視器記錄, 以提供其他資料和分析工具。 這些解決方案會從您的 HDInsight 叢集收集重要效能計量，並且提供工具以搜尋計量。 這些解決方案也會針對 HDInsight 中支援的大部分叢集類型，提供視覺效果和儀表板。 藉由使用您以解決方案收集的計量，您可以建立自訂的監視規則和警示。

可用的 HDInsight 解決方案如下：

* HDInsight Hadoop 監視
* HDInsight HBase 監控
* HDInsight 互動式查詢監視
* HDInsight Kafka 監視
* HDInsight Spark 監視
* HDInsight Storm 監視

如需安裝管理解決方案的指示，請參閱[在 Azure 中的管理解決方案](../azure-monitor/insights/solutions.md#install-a-monitoring-solution)。 若要進行實驗, 請安裝 HDInsight Hadoop 監視解決方案。 完成後，您會看到 **HDInsightHadoop** 圖格列示於 [摘要] 下方。 選取 **HDInsightHadoop** 圖格。 HDInsightHadoop 解決方案顯示如下：

![HDInsight 監視解決方案檢視](media/hdinsight-hadoop-oms-log-analytics-tutorial/hdinsight-oms-hdinsight-hadoop-monitoring-solution.png)

由於叢集是全新的叢集，因此報表不會顯示任何活動。

## <a name="configuring-performance-counters"></a>正在設定效能計數器

Azure 監視器也支援收集和分析叢集中節點的效能計量。 如需啟用和設定這項功能的詳細資訊, 請參閱[Azure 監視器中的 Linux 效能資料來源](https://docs.microsoft.com/azure/azure-monitor/platform/data-sources-performance-counters#linux-performance-counters)。

## <a name="next-steps"></a>後續步驟

* [查詢 Azure 監視器記錄以監視 HDInsight 叢集](hdinsight-hadoop-oms-log-analytics-use-queries.md)
