---
title: 有關 Azure Data Lake Storage Gen2 的資料案例 | Microsoft Docs
description: 了解可在 Data Lake Storage Gen2 (先前稱為 Azure Data Lake Store) 中用來內嵌、處理、下載及視覺化資料的各種案例和工具
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 10/11/2019
ms.author: normesta
ms.reviewer: stewu
ms.openlocfilehash: eba0c6a8932a8c6d50bd98d94712c95516519274
ms.sourcegitcommit: e0e6663a2d6672a9d916d64d14d63633934d2952
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/21/2019
ms.locfileid: "72300324"
---
# <a name="using-azure-data-lake-storage-gen2-for-big-data-requirements"></a>使用 Azure Data Lake Storage Gen2 處理巨量資料需求

巨量資料處理有四個主要階段︰

> [!div class="checklist"]
> * 即時或以批次形式將大量資料內嵌到存放區
> * 處理資料
> * 下載資料
> * 將資料視覺化

一開始先建立儲存體帳戶和容器。 然後，授與資料的存取權。 本文的前幾節可協助您完成這些工作。 在其餘各節中，我們會特別說明每個處理階段的選項與工具。

如需可與 Azure Data Lake Storage Gen2 搭配使用的 Azure 服務完整清單，請參閱[整合 Azure Data Lake Storage 與 azure 服務](data-lake-store-integrate-with-azure-services.md)

## <a name="create-a-data-lake-storage-gen2-account"></a>建立 Data Lake Storage Gen2 帳戶

Data Lake Storage Gen2 帳戶是具有階層命名空間的儲存體帳戶。 

若要建立一個，請參閱[快速入門：建立 Azure Data Lake Storage Gen2 儲存體帳戶](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-quickstart-create-account?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)。

## <a name="create-a-container"></a>建立容器

以下是您可以用來建立檔案容器的工具清單。

|工具 | 指引 |
|---|--|
|Azure 儲存體總管 | [使用儲存體總管建立容器](data-lake-storage-explorer.md#create-a-container) |
|AzCopy | [使用 AzCopyV10 建立 Blob 容器或檔案共用](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-v10#transfer-files)|
|搭配 HDInsight 的 Hadoop 容器（HDFS）命令列介面（CLI） |[使用 HDFS 搭配 HDInsight 來建立容器](data-lake-storage-use-hdfs-data-lake-storage.md#create-a-container) |
|Azure Databricks Notebook 中的程式碼|[建立儲存體帳戶容器（Scala）](data-lake-storage-quickstart-create-databricks-account.md#create-storage-account-container) <br><br> [建立容器並加以掛接（Python）](data-lake-storage-use-databricks-spark.md#create-a-container-and-mount-it)|

使用儲存體總管或 AzCopy 來建立檔案系統是最簡單的方式。 使用 HDInsight 與 Databricks 來建立檔案系統比較複雜一點。 不過，如果您打算使用 HDInsight 或 Databricks 叢集來處理您的資料，則可先建立您的叢集，然後使用 HDFS CLI 來建立檔案系統。  

## <a name="grant-access-to-the-data"></a>授與資料的存取權

開始擷取資料之前，對您的帳戶以及您帳戶中的資料，設定適當的存取權限。

有兩種方式可授與存取權：

* 將其中一個角色指派給使用者、群組、使用者管理的身分識別或服務主體：

  [儲存體 Blob 資料讀者](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-reader)

  [儲存體 Blob 資料參與者](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-queue-data-contributor)

  [儲存體 Blob 資料擁有者](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-owner)

* 使用共用存取簽章 (SAS) 權杖。

* 使用儲存體帳戶金鑰。

下表顯示如何授與每個 Azure 服務或工具的存取權。

|工具 | 授與存取權 | 指引 |
|---|--|---|
|儲存體總管| 將角色指派給使用者和群組 | [使用 Azure Active Directory 將系統管理員和非系統管理員角色指派給使用者](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-users-assign-role-azure-portal) |
|AzCopy| 將角色指派給使用者和群組 <br>**or**<br> 使用 SAS 權杖| [使用 Azure Active Directory 將系統管理員和非系統管理員角色指派給使用者](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-users-assign-role-azure-portal)<br><br>[輕鬆地建立 SAS 以從 Azure 儲存體下載檔案 – 使用 Azure 儲存體總管](https://blogs.msdn.microsoft.com/jpsanders/2017/10/12/easily-create-a-sas-to-download-a-file-from-azure-storage-using-azure-storage-explorer/)|
|Apache DistCp | 對使用者指派的受控識別指派角色 | [建立搭配 Data Lake Storage Gen2 的 HDInsight 叢集](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2) |
|Azure Data Factory| 對使用者指派的受控識別指派角色<br>**or**<br> 將角色指派給服務主體<br>**or**<br> 使用儲存體帳戶金鑰 | [連結服務屬性](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage#linked-service-properties) |
|Azure HDInsight| 對使用者指派的受控識別指派角色 | [建立搭配 Data Lake Storage Gen2 的 HDInsight 叢集](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2)|
|Azure Databricks| 將角色指派給服務主體 | [如何：使用入口網站建立可存取資源的 Azure AD 應用程式和服務主體](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal)|

若要授與特定檔案和資料夾的存取權，請參閱下列文章。

* [搭配 Azure Data Lake Storage Gen2 使用 Azure 儲存體總管設定檔案和目錄等級使用權限](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-how-to-set-permissions-storage-explorer)

* [檔案和目錄的存取控制清單](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control#access-control-lists-on-files-and-directories)

若要了解如何設定其他層面的安全性，請參閱 [Azure Data Lake Storage Gen2 安全性指南](https://docs.microsoft.com/azure/storage/common/storage-data-lake-storage-security-guide?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)。

## <a name="ingest-the-data"></a>內嵌資料

此節強調不同的資料來源，以及將資料內嵌到 Data Lake Storage Gen2 帳戶的各種方式。

![將資料內嵌至 Data Lake Storage Gen2](./media/data-lake-storage-data-scenarios/ingest-data.png "將資料內嵌到 Data Lake Storage Gen2")

### <a name="ad-hoc-data"></a>臨機操作資料

代表用來建立巨量資料應用程式原型的較小型資料集。 內嵌臨機操作資料的方式會因資料來源不同而有所差異。 

以下是您可用來擷取臨機操作資料的工具清單。

| 資料來源 | 內嵌方式 |
| --- | --- |
| 本機電腦 |[儲存體總管](https://azure.microsoft.com/features/storage-explorer/)<br><br>[AzCopy 工具](../common/storage-use-azcopy-v10.md)|
| Azure 儲存體 Blob |[Azure Data Factory](../../data-factory/connector-azure-data-lake-store.md)<br><br>[AzCopy 工具](../common/storage-use-azcopy-v10.md)<br><br>[HDInsight 叢集上執行的 DistCp](data-lake-storage-use-distcp.md)|

### <a name="streamed-data"></a>串流資料

這表示可由各種來源（例如應用程式、裝置、感應器等）產生的資料。這項資料可由各種不同的工具內嵌到 Data Lake Storage Gen2。 這些工具通常能以個別事件為基礎即時擷取及處理資料，然後再以批次將事件寫入 Data Lake Storage Gen2，以供進一步處理。

以下是您可用來擷取串流資料的工具清單。

|工具 | 指引 |
|---|--|
|Azure HDInsight Storm | [從 HDInsight 上的 Apache Storm 寫入 Apache Hadoop HDFS](https://docs.microsoft.com/azure/hdinsight/storm/apache-storm-write-data-lake-store) |

### <a name="relational-data"></a>關聯式資料

您也可以從關聯式資料庫取得資料。 每經過一段時間，關聯式資料庫就會收集大量資料，在經過巨量資料管線處理後，這些資料將可提供重要情資。 您可以使用下列工具，將此類資料移動到 Data Lake Storage Gen2。

以下是您可用來擷取關聯式資料的工具清單。

|工具 | 指引 |
|---|--|
|Azure Data Factory | [Azure Data Factory 中的複製活動](https://docs.microsoft.com/azure/data-factory/copy-activity-overview) |

### <a name="web-server-log-data-upload-using-custom-applications"></a>Web 伺服器記錄資料 (使用自訂應用程式上傳)

我們會特別強調此類資料集的原因在於，因為 Web 伺服器記錄資料的分析是巨量資料應用程式的常見使用案例，且需要將大量記錄檔上傳到 Data Lake Storage Gen2。 您可以使用以下任何工具來撰寫自己的指令碼或應用程式，以便上傳這類資料。

以下是您可用來擷取網頁伺服器記錄資料的工具清單。

|工具 | 指引 |
|---|--|
|Azure Data Factory | [Azure Data Factory 中的複製活動](https://docs.microsoft.com/azure/data-factory/copy-activity-overview)  |

若要上傳 Web 伺服器記錄資料及上傳其他類型的資料 (如社交情緒資料)，撰寫自己的自訂指令碼/應用程式是個不錯方法，因為您可以彈性地將自己的資料上傳元件納入較大型的巨量資料應用程式中。 在某些情況下，這段程式碼可能會採用指令碼或簡易命令列公用程式的形式。 在其他情況下，程式碼可用來將巨量資料處理整合到商務應用程式或解決方案中。

### <a name="data-associated-with-azure-hdinsight-clusters"></a>與 Azure HDInsight 叢集相關聯的資料

大部分的 HDInsight 叢集類型 (Hadoop、HBase、Storm) 能以資料儲存存放庫的形式支援 Data Lake Storage Gen2。 HDInsight 叢集能從 Azure 儲存體 Blob (WASB) 存取資料。 為了提高效能，您可以將資料從 WASB 複製到與叢集相關聯的 Data Lake Storage Gen2 帳戶。 您可以使用下列工具來複製資料。

以下是您可用來擷取 HDInsight 叢集相關資料的工具清單。

|工具 | 指引 |
|---|--|
|Apache DistCp | [使用 DistCp 在 Azure 儲存體 Blob 與 Azure Data Lake Storage Gen2 之間複製資料](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-use-distcp) |
|AzCopy 工具 | [使用 AzCopy 轉送資料](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-v10) |
|Azure Data Factory | [使用 Azure Data Factory 從 Azure Data Lake Storage Gen2 複製資料](https://docs.microsoft.com/azure/data-factory/load-azure-data-lake-storage-gen2) |

### <a name="data-stored-in-on-premises-or-iaas-hadoop-clusters"></a>儲存於內部部署環境或 IaaS Hadoop 叢集中的資料

您可能會使用 HDFS，在本機電腦上將大量資料儲存於現有的 Hadoop 叢集中。 Hadoop 叢集可能位於內部部署環境中，也可能位於 Azure 上的 IaaS 叢集內。 可能有一些要以一次性方法或週期性方式將此類資料複製到 Azure Data Lake Storage Gen2 的需求。 有各種不同的選項可用來達到此目的。 以下是替代項目和相關考量的清單。

| 方法 | 詳細資料 | 優點 | 考量 |
| --- | --- | --- | --- |
| 使用 Azure Data Factory (ADF)，將資料從 Hadoop 叢集直接複製到 Azure Data Lake Storage Gen2 |[ADF 支援 HDFS 做為資料來源](../../data-factory/connector-hdfs.md) |ADF 針對 HDFS 提供全新支援，以及一流的端對端管理與監視 |需要將「資料管理閘道」部署在內部部署環境或 IaaS 叢集中 |
| 使用 Distcp，將資料從 Hadoop 複製到 Azure 儲存體。 接著，使用適當的機制將資料從 Azure 儲存體複製到 Data Lake Storage Gen2。 |您可以使用下列工具，將資料從 Azure 儲存體複製到 Data Lake Storage Gen2︰ <ul><li>[Azure Data Factory](../../data-factory/copy-activity-overview.md)</li><li>[AzCopy 工具](../common/storage-use-azcopy-v10.md)</li><li>[HDInsight 叢集上執行的 Apache DistCp](data-lake-storage-use-distcp.md)</li></ul> |您可以使用開放原始碼工具。 |牽涉到多種技術的多步驟程序 |

### <a name="really-large-datasets"></a>大型資料集

若要上傳動輒數 TB 的資料集，使用上述方法有時候可能會過於緩慢且昂貴。 在這種情況下，您可以使用 Azure ExpressRoute。  

Azure ExpressRoute 可讓您在 Azure 資料中心與內部部署的基礎結構之間建立私人連線。 這是傳輸大量資料的可靠選項。 若要深入了解，請參閱 [Azure ExpressRoute 文件](../../expressroute/expressroute-introduction.md)。

## <a name="process-the-data"></a>處理資料

一旦 Data Lake Storage Gen2 中的資料可以使用，您就可以使用支援的巨量資料應用程式來針對這些資料執行分析。 

![分析 Data Lake Storage Gen2 中的資料](./media/data-lake-storage-data-scenarios/analyze-data.png "分析 Data Lake Storage Gen2 中的資料")

以下是您可用來對儲存在 Data Lake Storage Gen2 中的資料執行資料分析工作的工具清單。

|工具 | 指引 |
|---|--|
|Azure HDInsight | [搭配 Azure HDInsight 叢集使用 Data Lake Storage Gen2](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2) |
|Azure Databricks | [Azure Data Lake Storage Gen2](https://docs.azuredatabricks.net/spark/latest/data-sources/azure/azure-datalake-gen2.html)<br><br>[快速入門：使用 Azure Databricks 分析 Azure Data Lake Storage Gen2 中的資料](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-quickstart-create-databricks-account?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)<br><br>[教學課程：使用 Azure Databricks 解壓縮、轉換和載入資料](https://docs.microsoft.com/azure/azure-databricks/databricks-extract-load-sql-data-warehouse?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|

## <a name="visualize-the-data"></a>將資料視覺化

您可以混合使用多種服務，利用視覺化的方式呈現儲存在 Data Lake Storage Gen2 中的資料。

![在 Data Lake Storage Gen2 中將資料視覺化](./media/data-lake-storage-data-scenarios/visualize-data.png "視覺化 Data Lake Storage Gen2 中的資料")

* 您可以從使用 [Azure Data Factory 將資料從 Data Lake Storage Gen2 移到 Azure SQL 資料倉儲](../../data-factory/copy-activity-overview.md)開始
* 之後，您可以 [將 Power BI 與 Azure SQL 資料倉儲整合](../../sql-data-warehouse/sql-data-warehouse-get-started-visualize-with-power-bi.md) ，以視覺化方式呈現資料。

## <a name="download-the-data"></a>下載資料

在以下案例中，您可能也會想要從 Azure Data Lake Storage Gen2 下載資料或移動資料：

* 將資料移動到其他儲存機制，以便與現有的資料處理管線連結。 例如，您可能會想要將資料從 Data Lake Storage Gen2 移動到 Azure SQL Database 或內部部署 SQL Server。

* 在建置應用程式原型時，將資料下載到本機電腦，以便在 IDE 環境中處理。

![來自 Data Lake Storage Gen2 的輸出資料](./media/data-lake-storage-data-scenarios/egress-data.png "來自 Data Lake Storage Gen2 的輸出資料")

以下是您可用來從 Data Lake Storage Gen2 下載資料的工具清單。

|工具 | 指引 |
|---|--|
|Azure Data Factory | [Azure Data Factory 中的複製活動](https://docs.microsoft.com/azure/data-factory/copy-activity-overview) |
|Apache DistCp | [使用 DistCp 在 Azure 儲存體 Blob 與 Azure Data Lake Storage Gen2 之間複製資料](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-use-distcp) |
