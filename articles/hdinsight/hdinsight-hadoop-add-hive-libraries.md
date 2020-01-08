---
title: 在叢集建立期間 Apache Hive 程式庫-Azure HDInsight
description: 瞭解如何在叢集建立期間，將 Apache Hive 程式庫（jar 檔案）新增至 HDInsight 叢集。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: H1Hack27Feb2017,hdinsightactive
ms.date: 12/23/2019
ms.openlocfilehash: 57b4440a29dde470f91bbaae091bf65a0d2a1b51
ms.sourcegitcommit: ec2eacbe5d3ac7878515092290722c41143f151d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/31/2019
ms.locfileid: "75552265"
---
# <a name="add-custom-apache-hive-libraries-when-creating-your-hdinsight-cluster"></a>建立 HDInsight 叢集時新增自訂 Apache Hive 程式庫

了解如何預先載入 HDInsight 上的 [Apache Hive](https://hive.apache.org/) 程式庫。 本文件包含如何在叢集建立期間使用「指令碼動作」預先載入程式庫的詳細資訊。 使用本檔中的步驟新增的程式庫在 Hive 中是全域可用的，不需要使用 [[新增 JAR](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+Cli) ] 來載入它們。

## <a name="how-it-works"></a>運作方式

建立叢集時，您可以使用腳本動作，在叢集節點建立時加以修改。 本文件中的指令碼會接受單一參數，也就是程式庫的位置。 這個位置必須在 Azure 儲存體帳戶中，且將程式庫必須儲存為 jar 檔案。

叢集建立期間，指令碼會列舉檔案、將它們複製到前端和背景工作節點上的 `/usr/lib/customhivelibs/` 目錄，然後將它們加入至 `core-site.xml` 檔案中的 `hive.aux.jars.path` 屬性。 在以 Linux 為基礎的叢集上，它也會以檔案的位置來更新 `hive-env.sh` 檔案。

使用本文中的腳本動作，可讓您在使用 Hive 用戶端進行**WebHCat**和**HiveServer2**時使用程式庫。

## <a name="the-script"></a>指令碼

**指令碼位置**

[https://hdiconfigactions.blob.core.windows.net/setupcustomhivelibsv01/setup-customhivelibs-v01.ps1](https://hdiconfigactions.blob.core.windows.net/setupcustomhivelibsv01/setup-customhivelibs-v01.ps1)

**需求**

* 指令碼必須同時套用至**前端節點**和**背景工作節點**。

* 您想要安裝的 jar 必須儲存在**單一容器**中的 Azure Blob 儲存體。

* 包含 jar 檔案程式庫的儲存體帳戶**必須**在建立期間連結至 HDInsight 叢集。 它必須是預設儲存體帳戶，或透過__儲存體帳戶設定__新增的帳戶。

* 必須指定容器的 WASB 路徑做為指令碼動作的參數。 例如，如果 jar 儲存在名為**mystorage**的儲存體帳戶**上名**為「程式庫」的容器中，則參數會是 `wasbs://libs@mystorage.blob.core.windows.net/`。

  > [!NOTE]  
  > 本文件假設您已建立儲存體帳戶、blob 容器，也已將檔案上傳給它。
  >
  > 如果您尚未建立儲存體帳戶，您可以透過 [Azure 入口網站](https://portal.azure.com)來建立。 接著，您可以使用 [Azure 儲存體 Explorer](https://storageexplorer.com/) 之類的公用程式，在帳戶中建立容器，並將檔案上傳至其中。

## <a name="create-a-cluster-using-the-script"></a>使用指令碼建立叢集

1. 使用在[Linux 上](hdinsight-hadoop-provision-linux-clusters.md)布建 HDInsight 叢集中的步驟開始布建叢集，但不完成布建。 您也可以使用 Azure PowerShell 或 HDInsight .NET SDK，以使用此指令碼建立叢集。 如需使用這些方法的詳細資訊，請參閱 [使用指令碼動作自訂 HDInsight 叢集](hdinsight-hadoop-customize-cluster-linux.md)。 針對 Azure 入口網站，您必須選取 [**移至傳統建立體驗**] 選項，然後選取 [**自訂（大小、設定、應用程式）** ]。

1. 針對**儲存體**，如果包含 jar 檔案程式庫的儲存體帳戶會與用於叢集的帳戶不同，請完成**其他儲存體帳戶**。

1. 針對**腳本動作**，請提供下列資訊：

    |屬性 |值 |
    |---|---|
    |指令碼類型|- 自訂|
    |名稱|程式庫 |
    |Bash 指令碼 URI|`https://hdiconfigactions.blob.core.windows.net/linuxsetupcustomhivelibsv01/setup-customhivelibs-v01.sh`|
    |節點類型|Head、Worker|
    |參數|輸入包含 jar 之容器和儲存體帳戶的 WASB 位址。 例如： `wasbs://libs@mystorage.blob.core.windows.net/` 。|

1. 繼續如[在 Linux 上佈建 HDInsight 叢集](hdinsight-hadoop-provision-linux-clusters.md)中所述佈建叢集。

叢集建立完成後，您就能夠從 Hive 使用透過此腳本加入的 jar，而不需要使用 `ADD JAR` 語句。

## <a name="next-steps"></a>後續步驟

如需使用 Hive 的詳細資訊，請參閱 [搭配使用 Apache Hive 與 HDInsight](hadoop/hdinsight-use-hive.md)
