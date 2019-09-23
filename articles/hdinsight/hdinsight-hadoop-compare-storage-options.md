---
title: 比較與 Azure HDInsight 叢集搭配使用的儲存體選項
description: 提供儲存體類型以及它們如何與 Azure HDInsight 一起使用的概觀。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 06/17/2019
ms.openlocfilehash: b73810b37020bf01c1088f194bd426e93fd95d2c
ms.sourcegitcommit: a19bee057c57cd2c2cd23126ac862bd8f89f50f5
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/23/2019
ms.locfileid: "71180763"
---
# <a name="compare-storage-options-for-use-with-azure-hdinsight-clusters"></a>比較與 Azure HDInsight 叢集搭配使用的儲存體選項

建立 HDInsight 叢集時，您可以在幾個不同的 Azure 儲存體服務之間進行選擇：

* Azure 儲存體
* Azure Data Lake Storage Gen2
* Azure Data Lake Storage Gen1

本文提供了這些儲存體類型和其獨特功能的概觀。

下表摘要說明不同 HDInsight 版本所支援的 Azure 儲存體服務：

| 儲存體服務 | 帳戶類型 | 命名空間類型 | 支援的服務 | 支援的效能層級 | 支援的存取層 | HDInsight 版本 | 叢集類型 |
|---|---|---|---|---|---|---|---|
|Azure Data Lake Storage Gen2| 一般用途 V2 | 階層式（filesystem） | Blob | 標準 | 經常性存取、非經常性存取、封存 | 3.6+ | 全部 |
|Azure 儲存體| 一般用途 V2 | 物件 | Blob | 標準 | 經常性存取、非經常性存取、封存 | 3.6+ | 全部 |
|Azure 儲存體| 一般用途 V1 | 物件 | Blob | 標準 | N/A | 全部 | 全部 |
|Azure 儲存體| Blob 儲存體 * * | 物件 | 區塊 Blob | 標準 | 經常性存取、非經常性存取、封存 | 全部 | 全部 |
|Azure Data Lake Storage Gen1| N/A | 階層式（filesystem） | N/A | N/A | N/A | 僅限3。6 | HBase 以外的所有 |

\* * 對於 HDInsight 叢集，只有次要儲存體帳戶可以屬於類型 BlobStorage，而分頁 Blob 不是支援的儲存體選項。

如需 Azure 儲存體帳戶類型的詳細資訊，請參閱[Azure 儲存體帳戶總覽](../storage/common/storage-account-overview.md)

如需 Azure 儲存體存取層的詳細資訊， [請參閱 Azure Blob 儲存體：Premium （預覽）、經常性存取、非經常性存取和封存儲存層](../storage/blobs/storage-blob-storage-tiers.md)

您可以針對主要和選擇性的次要儲存體，使用不同的服務組合來建立叢集。 下表摘要說明 HDInsight 目前支援的叢集儲存體設定：

| HDInsight 版本 | 主要儲存體 | 次要儲存體 | 支援 |
|---|---|---|---|
| 3.6 & 4.0 | 一般用途 V1，一般用途 V2 | 一般用途 V1、一般用途 V2、BlobStorage （區塊 Blob） | 是 |
| 3.6 & 4.0 | 一般用途 V1，一般用途 V2 | Data Lake Storage Gen2 | 否 |
| 3.6 & 4.0 | 一般用途 V1，一般用途 V2 | Data Lake Storage Gen1 | 是 |
| 3.6 & 4.0 | Data Lake Storage Gen2 * | Data Lake Storage Gen2 | 是 |
| 3.6 & 4.0 | Data Lake Storage Gen2 * | 一般用途 V1、一般用途 V2、BlobStorage （區塊 Blob） | 是 |
| 3.6 & 4.0 | Data Lake Storage Gen2 | Data Lake Storage Gen1 | 否 |
| 3.6 | Data Lake Storage Gen1 | Data Lake Storage Gen1 | 是 |
| 3.6 | Data Lake Storage Gen1 | 一般用途 V1、一般用途 V2、BlobStorage （區塊 Blob） | 是 |
| 3.6 | Data Lake Storage Gen1 | Data Lake Storage Gen2 | 否 |
| 4.0 | Data Lake Storage Gen1 | Any | 否 |

\* = 這可以是一個或多個 Data Lake Storage Gen2 帳戶，只要它們都設定為使用相同的受控識別來進行叢集存取即可。

## <a name="use-azure-data-lake-storage-gen2-with-apache-hadoop-in-azure-hdinsight"></a>搭配 Azure HDInsight 中的 Apache Hadoop 使用 Azure Data Lake Storage Gen2

Azure Data Lake Storage Gen2 採用 Azure Data Lake Storage Gen1 的核心功能，並將這些功能整合到 Azure Blob 儲存體。 這些功能包括與 Hadoop 相容的檔案系統、Azure Active Directory (Azure AD) 和 POSIX 型存取控制清單 (ACL)。 此組合可讓您擁有 Azure Data Lake Storage Gen1 的效能優勢，同時還能使用 Blob 儲存體的分層功能和資料生命週期管理。

如需 Azure Data Lake Storage Gen2 的詳細資訊，請參閱 [Azure Data Lake Storage Gen2 簡介](../storage/blobs/data-lake-storage-introduction.md)。

### <a name="core-functionality-of-azure-data-lake-storage-gen2"></a>Azure Data Lake Storage Gen2 的核心功能

* **與 Hadoop 相容的存取權：** 在 Azure Data Lake Storage Gen2 中，您可以管理及存取資料，就如同使用 Hadoop 分散式檔案系統 (HDFS) 一樣。 Azure Blob 檔案系統 (ABFS) 驅動程式可在所有 Apache Hadoop 環境中使用，包括 Azure HDInsight 和 Azure Databricks。 使用 ABFS 存取儲存在 Data Lake Storage Gen2 中的資料。

* **POSIX 權限的超集合：** Data Lake Gen2 的安全性模型可支援 ACL 和 POSIX 權限，以及一些 Data Lake Storage Gen2 特有的額外細微性。 這些設定可透過系統管理工具或 Apache Hive 和 Apache Spark 這類架構來配置。

* **符合成本效益：** Data Lake Storage Gen2 提供低成本儲存體容量和異動功能。 Azure Blob 儲存體生命週期這類功能可協助降低成本，因為當資料在整個生命週期中移動時，您可以調整費率。

* **與 Blob 儲存體工具、架構和應用程式相容：** Data Lake Storage Gen2 可繼續搭配使用各種 Blob 儲存體工具、架構及應用程式。

* **最佳化的驅動程式：** ABFS 驅動程式已針對巨量資料分析完成特別最佳化。 對應的 REST API 會透過分散式檔案系統 (DFS) 端點 dfs.core.windows.net 呈現。

### <a name="whats-new-for-azure-data-lake-storage-gen-2"></a>Azure Data Lake Storage Gen 2 的新功能

#### <a name="managed-identities-for-secure-file-access"></a>用於安全存取檔案的受控識別

Azure HDInsight 會使用受控識別來保護叢集對 Azure Data Lake Storage Gen2 中檔案的存取。 受控識別是 Azure Active Directory 的功能，可提供一組自動受控的認證給 Azure 服務。 這些認證可用來向任何支援 Active Directory 驗證的服務進行驗證。 使用受控識別不需要將認證儲存在程式碼或組態檔中。

如需詳細資訊，請參閱[適用於 Azure 資源的受控識別](../active-directory/managed-identities-azure-resources/overview.md)。

#### <a name="azure-blob-file-system-driver"></a>Azure Blob 檔案系統驅動程式

Apache Hadoop 應用程式原本就預期會從本機磁碟儲存體讀取和寫入資料。 Hadoop 檔案系統驅動程式 (例如 ABFS) 會藉由模擬一般 Hadoop 檔案系統作業，讓 Hadoop 應用程式搭配使用雲端儲存體。 驅動程式會將從應用程式接收的命令，轉換為實際雲端儲存空間平台理解的作業。

先前，Hadoop 檔案系統驅動程式已將所有檔案系統作業轉換成用戶端上的 Azure 儲存體 REST API 呼叫，並接著叫用 REST API。 不過，此用戶端轉換會造成單一檔案系統作業 (例如檔案重新命名) 有多個 REST API 呼叫。 ABFS 已將部分 Hadoop 檔案系統邏輯從用戶端移至伺服器端。 Azure Data Lake Storage Gen2 API 現在會與 Blob API 平行執行。 此移轉可改善效能，因為現在的一般 Hadoop 檔案系統作業可透過一個 REST API 呼叫來執行。

如需詳細資訊，請參閱 [Azure Blob 檔案系統驅動程式 (ABFS)：適用於 Hadoop 的專用 Azure 儲存體驅動程式](../storage/blobs/data-lake-storage-abfs-driver.md)。

#### <a name="uri-scheme-for-azure-data-lake-storage-gen-2"></a>Azure Data Lake Storage Gen 2 的 URI 配置 

Azure Data Lake Storage Gen2 會使用新的 URI 配置，從 HDInsight 存取 Azure 儲存體中的檔案：

`abfs://<FILE_SYSTEM_NAME>@<ACCOUNT_NAME>.dfs.core.windows.net/<PATH>`

URI 配置會提供 SSL 加密存取。

`<FILE_SYSTEM_NAME>` 可識別檔案系統 Data Lake Storage Gen2 的路徑。

`<ACCOUNT_NAME>` 可識別 Azure 儲存體帳戶名稱。 需要使用完整網域名稱 (FQDN)。

`<PATH>` 是檔案或目錄 HDFS 路徑名稱。

如果未指定 `<FILE_SYSTEM_NAME>` 和 `<ACCOUNT_NAME>` 的值，則會使用預設檔案系統。 對於預設檔案系統上的檔案，使用相對路徑或絕對路徑。 例如，可使用下列其中一個路徑來參考 HDInsight 叢集隨附的 `hadoop-mapreduce-examples.jar` 檔案：

```
abfs://myfilesystempath@myaccount.dfs.core.windows.net/example/jars/hadoop-mapreduce-examples.jar
abfs:///example/jars/hadoop-mapreduce-examples.jar /example/jars/hadoop-mapreduce-examples.jar
```

> [!Note]
> 在 HDInsight 2.1 和 1.6 版叢集中的檔案名稱是 `hadoop-examples.jar`。 當您使用 HDInsight 外部的檔案時，您會發現大部分的公用程式無法辨識 ABFS 格式，但可預期基本的路徑格式，例如 `example/jars/hadoop-mapreduce-examples.jar`。

如需詳細資訊，請參閱[使用 Azure Data Lake Storage Gen2 URI](../storage/blobs/data-lake-storage-introduction-abfs-uri.md)。

## <a name="azure-storage"></a>Azure 儲存體

Azure 儲存體是強大的一般用途儲存體解決方案，其完美整合了 HDInsight。 HDInsight 可以使用 Azure 儲存體中的 Blob 容器做為叢集的預設檔案系統。 透過 HDFS 介面，HDInsight 中的完整元件集可直接處理儲存為 Blob 的結構化或非結構化資料。

我們建議針對您的預設叢集儲存體和您的商務資料使用個別的儲存體容器，以將 HDInsight 記錄檔和暫存檔案與您自己的商務資料隔離。 我們也建議您刪除預設的 blob 容器，其中包含應用程式和系統記錄檔，然後在每次使用之後，以降低儲存成本。 請務必先擷取記錄再刪除容器。

如果您選擇使用**所選網路**上的**防火牆和虛擬網路**限制來保護您的儲存體帳戶，請務必啟用 [**允許信任的 Microsoft 服務**] 例外狀況，讓 HDInsight 可以存取您的儲存體帳戶.

### <a name="hdinsight-storage-architecture"></a>HDInsight 儲存架構

下圖提供 Azure 儲存體的 HDInsight 架構摘要檢視：

![HDInsight 儲存體架構](./media/hdinsight-hadoop-compare-storage-options/storage-architecture.png "HDInsight 儲存體架構")

HDInsight 可以存取本機連接至計算節點的分散式檔案系統。 可使用完整 URI 來存取此檔案系統，例如：

    hdfs://<namenodehost>/<path>

您也可以透過 HDInsight 存取 Azure 儲存體中的資料。 語法如下所示：

    wasb://<containername>@<accountname>.blob.core.windows.net/<path>

使用 Azure 儲存體帳戶搭配 HDInsight 叢集時，請考量下列原則：

* **儲存體帳戶中連線至叢集的容器：** 因為在建立期間帳戶名稱和金鑰會與叢集相關聯，所以您對這些容器中的 Blob 具有完整存取權。

* **儲存體帳戶中未連線至叢集的公用容器或公用 Blob：** 您對容器中的 Blob 只有唯讀權限。
  
  > [!NOTE]  
  > 公用容器可讓您取得該容器中所有可用的 Blob 清單，並取得容器中繼資料。 公用 Blob 只在您知道確切的 URL 時才可讓您存取 Blob。 如需詳細資訊，請參閱 [管理對容器與 Blob 的匿名讀取權限](../storage/blobs/storage-manage-access-to-resources.md)。

* **儲存體帳戶中未連接至叢集的私人容器：** 除非在提交 WebHCat 工作時定義儲存體帳戶，否則不能存取容器中的 Blob。 

建立程序及其金鑰中定義的儲存體帳戶會儲存在叢集節點的 %HADOOP_HOME%/conf/core-site.xml 中。 根據預設，HDInsight 會使用 core-site.xml 檔案中定義的儲存體帳戶。 您可以使用 [Apache Ambari](./hdinsight-hadoop-manage-ambari.md) 來修改此設定。

多個 WebHCat 工作 (包括 Apache Hive、MapReduce、Apache Hadoop 資料流和 Apache Pig) 可隨身夾帶儲存體帳戶的說明和中繼資料。 (目前適合於含儲存體帳戶的 Pig，但不適合於中繼資料)。如需詳細資訊，請參閱[在其他儲存體帳戶和 Metastores 上使用 HDInsight 叢集](https://social.technet.microsoft.com/wiki/contents/articles/23256.using-an-hdinsight-cluster-with-alternate-storage-accounts-and-metastores.aspx)。

Blob 可使用於結構化和非結構化資料。 Blob 容器會將資料儲存為索引鍵/值組，沒有目錄階層。 但是，索引鍵名稱可以包含斜線字元 ( / )，使檔案變成好像儲存在目錄結構中一樣。 例如，Blob 的機碼可以是 `input/log1.txt`。 實際上，`input` 目錄並不存在，但是因為索引碼名稱中有斜線字元，索引碼才會看起來像是檔案路徑。

### <a id="benefits"></a>Azure 儲存體的優點
未共置的計算叢集和儲存體資源具有隱含的效能成本。 讓所建立的計算叢集靠近 Azure 區域中的儲存體帳戶資源，即可降低這些成本。 在此區域中，計算節點可透過高速網路有效地存取 Azure 儲存體內的資料。

將資料儲存在 Azure 儲存體而非 HDFS 有許多優點：

* **資料重複使用和共用：** HDFS 中的資料位於計算叢集內。 只有可存取計算叢集的應用程式，才能利用 HDFS API 來使用資料。 相較之下，可以透過 HDFS API 或 Blob 儲存體 REST API 存取 Azure 儲存體中的資料。 因為這種安排，許多應用程式 (包括其他 HDInsight 叢集) 和工具都可用來產生和取用資料。

* **資料封存：** 將資料儲存在 Azure 儲存體時，可安全地刪除用於計算的 HDInsight 叢集，而不會遺失使用者資料。

* **資料儲存成本：** 長期將資料儲存在 DFS 中的成本高於將資料儲存在 Azure 儲存體中，因為計算叢集的成本高於 Azure 儲存體的成本。 此外，因為不需要每次產生計算叢集時都重新載入資料，也能節省資料載入成本。

* **彈性向外延展：** 雖然HDFS 提供向外延展的檔案系統，但延展程度取決於您建立給叢集的節點數目。 變更延展程度較為複雜，可改用 Azure 儲存體自動提供的彈性延展功能。

* **異地複寫：** Azure 儲存體可以進行異地複寫。 雖然異地複寫可提供地理位置復原和資料備援性，但容錯移轉至異地複寫的位置會嚴重影響效能，且可能產生額外的成本。 因此，請謹慎選擇異地複寫，且最好在資料的價值大於額外成本時才這樣做。

某些 MapReduce 工作和封裝可能會產生中繼結果，但您不會想要將這些結果儲存在 Azure 儲存體中。 在此情況下，您仍可選擇將資料儲存在本機 HDFS。 在 Hive 工作和其他程序中，HDInsight 會使用 DFS 來儲存許多這些中繼結果。

> [!NOTE]  
> 大部分 HDFS 命令 (例如，`ls`、`copyFromLocal` 和 `mkdir`) 可在 Azure 儲存體中正常運作。 只有原生 HDFS 實作 (稱為 DFS) 的特定命令 (例如 `fschk` 和 `dfsadmin`) 才會在 Azure 儲存體上出現不同的行為。

## <a name="overview-of-azure-data-lake-storage-gen1"></a>Azure Data Lake Storage Gen1 概觀

Azure Data Lake Storage Gen1 是容納巨量資料分析工作負載的企業級超大規模存放庫。 使用 Azure Data Lake，您可以在一個位置擷取任何大小、類型和擷取速度的資料，以便進行運作和探究分析。

使用與 WebHDFS 相容的 REST API，從 Hadoop (HDInsight 叢集所提供) 存取 Data Lake Storage Gen1。 Data Lake Storage Gen1 專為預存資料分析而設計，並針對資料分析案例效能而調整。 根據預設，它包含真實企業使用案例不可或缺的功能。 這些功能包括安全性、管理性、延展性、可靠性和可用性。

如需有關 Azure Data Lake Storage Gen1 的詳細資訊，請參閱詳細的 [Azure Data Lake Storage Gen1 概觀](../data-lake-store/data-lake-store-overview.md)。

Data Lake Storage Gen1 的重要功能包括下列項目。

### <a name="compatibility-with-hadoop"></a>與 Hadoop 相容

Data Lake Storage Gen1 是與 HDFS 相容的 Apache Hadoop 檔案系統，可搭配 Hadoop 生態系統使用。  採用 WebHDFS API 的現有 HDInsight 應用程式或服務可以輕易地與 Data Lake Storage Gen 1 整合。 Data Lake Storage Gen1 也會公開適用於應用程式的 WebHDFS 相容 REST 介面。

使用 Hadoop 分析架構 (例如 MapReduce 或 Hive)，可以輕鬆地分析 Data Lake Storage Gen1 中儲存的資料。 您可以佈建並設定 Azure HDInsight 叢集，以直接存取 Data Lake Storage Gen1 中儲存的資料。

### <a name="unlimited-storage-petabyte-files"></a>無限制的儲存空間、PB 檔案

Data Lake Storage Gen1 提供無限制的儲存空間，適合用來儲存各種資料以供分析。 對於帳戶大小、檔案大小，或資料湖中可儲存的資料量，均無任何限制。 個別檔案的大小可從數 KB 到數 PB，讓 Data Lake Storage Gen1 很適合用來儲存任何類型的資料。 藉由製作多個複本來長期儲存資料，而資料可以儲存在 Data Lake 中的時間沒有限制。

### <a name="performance-tuning-for-big-data-analytics"></a>針對巨量資料分析調整效能

Data Lake Storage Gen1 專為執行大型分析系統而建置，而此類系統需要龐大輸送量才能查詢及分析大量資料。 資料湖會將檔案的各個部分散於數個個別的儲存體伺服器。 當您分析資料時，此設定可改善平行讀取檔案時的讀取輸送量。

### <a name="readiness-for-enterprise-highly-available-and-secure"></a>企業整備：高度可用且安全

Data Lake Storage Gen1 提供符合業界標準的可用性與可靠性。 資料資產可長期儲存：備援複本可防範任何非預期的失敗。 企業可以在其解決方案中使用 Data Lake Storage Gen1，以成為其現有資料平台的重要部分。

Data Lake Storage Gen1 也可對預存資料提供企業級安全性。 如需詳細資訊，請參閱[在 Azure Data Lake Storage Gen1 中保護資料](#DataLakeStoreSecurity)。

### <a name="flexible-data-structures"></a>彈性資料結構

Data Lake Storage Gen1 能以其原生格式 (原樣) 儲存任何資料，而不需要先前轉換。 載入資料之前，Data Lake Storage Gen1 不需要定義結構描述。 個別的分析架構可解譯資料，並且在分析階段定義結構描述。 因為該架構可以儲存任意大小與格式的檔案，所以 Data Lake Storage Gen1 可以處理結構化、半結構化與非結構化資料。

Data Lake Storage Gen1 的資料容器基本上是資料夾與檔案。 您可以使用 SDK、Azure 入口網站和 Azure PowerShell 來操作儲存的資料。 只要您使用這些介面和適當的容器，將資料放入存放區，就可以儲存任何類型的資料。 Data Lake Storage Gen1 不會根據其儲存的資料類型來對資料執行任何特殊處理。

## <a name="DataLakeStoreSecurity"></a>Data Lake Storage Gen1 中的資料安全性
Data Lake Storage Gen1 會使用 Azure Active Directory 進行驗證，並使用存取控制清單 (ACL) 來管理對資料的存取。

| **功能** | **描述** |
| --- | --- |
| 驗證 |Data Lake Storage Gen1 整合了 Azure Active Directory (Azure AD)，可對 Data Lake Storage Gen1 中儲存的所有資料進行身分識別與存取管理。 由於整合的結果，Data Lake Storage Gen1 受惠於所有的 Azure AD 功能。 這些功能包括多重要素驗證、條件式存取、角色型存取控制、應用程式使用方式監視、安全性監視和警示等。 Data Lake Storage Gen1 支援 OAuth 2.0 通訊協定以便在 REST 介面中進行驗證。 請參閱[使用 Azure Active Directory 在 Azure Data Lake Storage Gen1 內驗證](../data-lake-store/data-lakes-store-authentication-using-azure-active-directory.md)|
| 存取控制 |Data Lake Storage Gen1 透過支援 WebHDFS 通訊協定所公開的 POSIX 樣式權限，以提供存取控制。 ACL 可在根資料夾、子資料夾和個別檔案上啟用。 如需 ACL 如何在 Data Lake Storage Gen1 的環境中運作的詳細資訊，請參閱 [Data Lake Storage Gen1 中的存取控制](../data-lake-store/data-lake-store-access-control.md)。 |
| 加密 |Data Lake Storage Gen1 也會為帳戶中儲存的資料提供加密功能。 您會在建立 Data Lake Storage Gen1 帳戶時指定加密設定。 您可以選擇將資料加密，或選擇不使用加密。 如需詳細資訊，請參閱 [Data Lake Storage Gen1 的加密](../data-lake-store/data-lake-store-encryption.md)。 如需如何提供加密相關設定的指示，請參閱[使用 Azure 入口網站開始使用 Azure Data Lake Storage Gen1](../data-lake-store/data-lake-store-get-started-portal.md)。 |

若要深入了解如何在 Data Lake Storage Gen1 中保護資料，請參閱[保護儲存在 Azure Data Lake Storage Gen1 中的資料](../data-lake-store/data-lake-store-secure-data.md)。

## <a name="applications-that-are-compatible-with-data-lake-storage-gen1"></a>與 Data Lake Storage Gen1 相容的應用程式
Data Lake Storage Gen1 與 Hadoop 生態系統中的大部分開放原始碼元件相容。 此外，還與其他 Azure 服務完美整合。  請依照下面的連結，深入了解 Data Lake Storage Gen1 如何搭配開放原始碼元件與其他 Azure 服務使用。

* 如需可與 Data Lake Storage Gen1 交互操作的開放原始碼應用程式清單，請參閱[與 Azure Data Lake Storage Gen1 搭配運作的開放原始碼巨量資料應用程式](../data-lake-store/data-lake-store-compatible-oss-other-applications.md)。
* 請參閱[整合 Azure Data Lake Storage Gen1 與其他 Azure 服務](../data-lake-store/data-lake-store-integrate-with-other-services.md)，以了解如何使用 Data Lake Storage Gen1 搭配其他 Azure 服務，以促成更廣泛的案例。
* 請參閱[使用 Data Lake Storage Gen1 來符合巨量資料需求](../data-lake-store/data-lake-store-data-scenarios.md)，以了解如何在擷取資料、處理資料、下載資料和視覺化資料等案例中使用 Data Lake Storage Gen1。

## <a name="data-lake-storage-gen1-file-system-adl"></a>Data Lake Storage Gen1 檔案系統 (adl://)
在 Hadoop 環境中 (透過 HDInsight 提供)，您可以透過新的檔案系統 (AzureDataLakeFilesystem (adl://)) 存取 Data Lake Storage Gen1。 利用 WebHDFS 中目前無法提供的方式，可以將使用 adl:// 的應用程式和服務效能最佳化。 因此，當您使用 Data Lake Storage Gen1 時，您可取得以下彈性：透過使用建議的 adl:// 獲得最佳效能，或繼續直接使用 WebHDFS API 來維護現有的程式碼。 Azure HDInsight 充分利用 AzureDataLakeFilesystem 來提供 Data Lake Storage Gen1 最佳效能。

使用下列方式，在 Data Lake Storage Gen1 中存取您的資料：

`adl://<data_lake_storage_gen1_name>.azuredatalakestore.net`

如需有關如何在 Data Lake Storage Gen1 中存取資料的詳細資訊，請參閱[預存資料可用的動作](../data-lake-store/data-lake-store-get-started-portal.md#properties)。



## <a name="next-steps"></a>後續步驟

* [Azure Data Lake Storage Gen2 簡介](../storage/blobs/data-lake-storage-introduction.md)
* [Azure 儲存體簡介](../storage/common/storage-introduction.md)
