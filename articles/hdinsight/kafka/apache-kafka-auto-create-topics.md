---
title: 在 Apache Kafka 中啟用自動建立主題 - Azure HDInsight
description: 了解如何將 HDInsight 上的 Apache Kafka 設定為自動建立主題。 您可以透過 Ambari 或在透過 PowerShell 或 Resource Manager 範本建立叢集期間，將 auto.create.topics.enable 設為 true，藉以設定 Kafka。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 04/18/2018
ms.openlocfilehash: 5d990a1c39495090fed1c78f1ddf5e879490e6c4
ms.sourcegitcommit: dd69b3cda2d722b7aecce5b9bd3eb9b7fbf9dc0a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/12/2019
ms.locfileid: "70960677"
---
# <a name="how-to-configure-apache-kafka-on-hdinsight-to-automatically-create-topics"></a>如何將 HDInsight 上的 Apache Kafka 設定為自動建立主題

根據預設，HDInsight 上的 [Apache Kafka](https://kafka.apache.org/) 不會啟用自動建立主題。 您可以使用 [Apache Ambari](https://ambari.apache.org/)，針對現有的叢集啟用自動建立主題。 您也可以在使用 Azure Resource Manager 範本建立新的 Kafka 叢集時，啟用自動建立主題。

## <a name="apache-ambari-web-ui"></a>Apache Ambari Web UI

若要透過 Ambari Web UI 針對現有的叢集啟用自動建立主題，請使用下列步驟：

1. 從 [Azure 入口網站](https://portal.azure.com)，選取 Kafka 叢集。

2. 從 [叢集概觀]，選取 [叢集儀表板]。 

    ![已選取叢集儀表板的入口網站影像](./media/apache-kafka-auto-create-topics/kafka-cluster-overview.png)

3. 然後選取 [HDInsight 叢集儀表板]。 出現提示時，使用叢集的登入 (系統管理員) 認證進行驗證。

    ![HDInsight 叢集儀表板項目的影像](./media/apache-kafka-auto-create-topics/hdinsight-cluster-dashboard.png)

3. 從頁面左邊的清單中，選取 [Kafka] 服務。

    ![服務清單](./media/apache-kafka-auto-create-topics/hdinsight-service-list.png)

4. 選取頁面中間的 [設定]。

    ![服務設定索引標籤](./media/apache-kafka-auto-create-topics/hdinsight-service-config.png)

5. 在 [篩選] 欄位中，輸入 `auto.create` 的值。 

    ![篩選欄位的影像](./media/apache-kafka-auto-create-topics/hdinsight-filter-field.png)

    這會篩選屬性清單並顯示 `auto.create.topics.enable` 設定。

6. 將 `auto.create.topics.enable` 的值變更為 `true`，然後選取 [儲存]。 新增附註，然後再次選取 [儲存]。

    ![auto.create.topics.enable 項目的影像](./media/apache-kafka-auto-create-topics/auto-create-topics-enable.png)

7. 依序選取 [Kafka] 服務、[重新啟動] 和 [重新啟動所有受影響的]。 出現提示時，選取 [確認全部重新啟動]。

    ![重新啟動選取項目的影像](./media/apache-kafka-auto-create-topics/restart-all-affected.png)

> [!NOTE]  
> 您也可以透過 Ambari REST API 設定 Ambari 值。 這通常更困難，因為您必須進行多個 REST 呼叫以擷取目前的設定、修改它等。如需詳細資訊，請參閱[使用 Apache Ambari REST API 管理 HDInsight 叢集](../hdinsight-hadoop-manage-ambari-rest-api.md)文件。

## <a name="resource-manager-templates"></a>Resource Manager 範本

使用 Azure Resource Manager 範本建立 Kafka 叢集時，您可以藉由將 `auto.create.topics.enable` 加入至 `kafka-broker` 來直接設定它。 下列 JSON 程式碼片段示範如何將此值設定為 `true`：

```json
"clusterDefinition": {
    "kind": "kafka",
    "configurations": {
    "gateway": {
        "restAuthCredential.isEnabled": true,
        "restAuthCredential.username": "[parameters('clusterLoginUserName')]",
        "restAuthCredential.password": "[parameters('clusterLoginPassword')]"
    },
    "kafka-broker": {
        "auto.create.topics.enable": "true"
    }
}
```

## <a name="next-steps"></a>後續步驟

在本文件中，您已了解如何針對 HDInsight 上的 Apache Kafka 啟用自動建立主題。 若要深入了解 Kafka 的使用方式，請參閱下列連結：

* [分析 Apache Kafka 記錄](apache-kafka-log-analytics-operations-management.md)
* [在 Apache Kafka 叢集之間複寫資料](apache-kafka-mirroring.md)
