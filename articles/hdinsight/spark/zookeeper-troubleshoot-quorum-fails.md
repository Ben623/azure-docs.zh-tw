---
title: Apache ZooKeeper server 無法在 Azure HDInsight 中形成仲裁
description: Apache ZooKeeper server 無法在 Azure HDInsight 中形成仲裁
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/20/2019
ms.openlocfilehash: a0874826529b5c9ca5d6d4107fe820cd522d81d0
ms.sourcegitcommit: 8e9a6972196c5a752e9a0d021b715ca3b20a928f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2020
ms.locfileid: "75894040"
---
# <a name="apache-zookeeper-server-fails-to-form-a-quorum-in-azure-hdinsight"></a>Apache ZooKeeper server 無法在 Azure HDInsight 中形成仲裁

本文說明與 Azure HDInsight 叢集互動時，問題的疑難排解步驟和可能的解決方法。

## <a name="issue"></a>問題

Apache ZooKeeper 伺服器狀況不良，徵兆可能包括：資源管理員/名稱節點處於待命模式、簡單 HDFS 作業無法運作、`zkFailoverController` 已停止且無法啟動，因為 Zookeeper 錯誤，Yarn/Spark/Livy 作業會失敗。 您可能會看到類似下列的錯誤訊息：

```
19/06/19 08:27:08 ERROR ZooKeeperStateStore: Fatal Zookeeper error. Shutting down Livy server.
19/06/19 08:27:08 INFO LivyServer: Shutting down Livy server.
```

## <a name="cause"></a>原因

當快照集檔案的數量很大或快照集檔案損毀時，ZooKeeper 伺服器將無法形成仲裁，這會導致 ZooKeeper 相關的服務狀況不良。 ZooKeeper 伺服器不會從其資料目錄中移除舊的快照集檔案，而是定期執行的工作，供使用者維護 ZooKeeper 的資料軌。 如需詳細資訊，請參閱[ZooKeeper 的優點與限制](https://zookeeper.apache.org/doc/r3.3.5/zookeeperAdmin.html#sc_strengthsAndLimitations)。

## <a name="resolution"></a>解析度

檢查 ZooKeeper 資料目錄 `/hadoop/zookeeper/version-2`，並 `/hadoop/hdinsight-zookeepe/version-2` 以瞭解快照集檔案大小是否龐大。 如果有大型快照集，請採取下列步驟：

1. 備份 `/hadoop/zookeeper/version-2` 和 `/hadoop/hdinsight-zookeepe/version-2`中的快照集。

1. 清除 `/hadoop/zookeeper/version-2` 和 `/hadoop/hdinsight-zookeepe/version-2`中的快照集。

1. 從 Apache Ambari UI 重新開機所有 ZooKeeper 伺服器。

## <a name="next-steps"></a>後續步驟

如果您沒有看到您的問題，或無法解決您的問題，請瀏覽下列其中一個管道以取得更多支援：

- 透過[Azure 社區支援](https://azure.microsoft.com/support/community/)取得 azure 專家的解答。

- 與[@AzureSupport](https://twitter.com/azuresupport)進行連接-官方 Microsoft Azure 帳戶，以改善客戶體驗。 將 Azure 社區連接到正確的資源：解答、支援和專家。

- 如果您需要更多協助，您可以從[Azure 入口網站](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)提交支援要求。 從功能表列選取 [**支援**]，或開啟 [說明 **+ 支援**] 中樞。 如需詳細資訊，請參閱[如何建立 Azure 支援要求](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)。 您的 Microsoft Azure 訂用帳戶包含訂用帳戶管理和帳單支援的存取權，而技術支援則透過其中一項[Azure 支援方案](https://azure.microsoft.com/support/plans/)提供。
