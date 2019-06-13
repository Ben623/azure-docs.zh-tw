---
title: Apache Hadoop 元件和版本 - Azure HDInsight
description: 了解 HDInsight 中的 Apache Hadoop 元件和版本，以及此 Hortonworks Data Platform 雲端發佈的可用服務層級。
keywords: hadoop 版本, hadoop 生態系統元件, hadoop 元件, 如何檢查 hadoop 版本
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: overview
ms.date: 03/26/2019
ms.openlocfilehash: 16a168f69c635ca86f40a08ba38a26d68c95954c
ms.sourcegitcommit: 600d5b140dae979f029c43c033757652cddc2029
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/04/2019
ms.locfileid: "66495769"
---
# <a name="what-are-the-apache-hadoop-components-and-versions-available-with-hdinsight"></a>可以搭配 HDInsight 使用的 Apache Hadoop 元件和版本有哪些？

了解 Microsoft Azure HDInsight 中的 [Apache Hadoop](https://hadoop.apache.org/) 生態系統元件和版本，以及企業安全性套件。 此外，了解如何檢查 HDInsight 中的 Hadoop 元件版本。

每個 HDInsight 版本都是 Hortonworks Data Platform (HDP) 版本的雲端發佈。

## <a name="apache-hadoop-components-available-with-different-hdinsight-versions"></a>可以搭配不同 HDInsight 版本使用的 Apache Hadoop 元件

Azure HDInsight 支援多個可隨時部署的 Hadoop 叢集版本。 每一個版本選擇都會建立特定版本的 HDP 發佈，以及該發佈內包含的一組元件。 截至 2017 年 4 月 4 日為止，Azure HDInsight 預設使用的叢集版本為 3.6 版，且以 HDP 2.6 為基礎。

下表列出與 HDInsight 叢集版本相關聯的元件版本： 

> [!NOTE]  
> HDInsight 服務的預設版本可能會變更，恕不另行通知。 如果您有版本相依性，請在使用 .NET SDK 搭配 Azure PowerShell 和 Azure 傳統 CLI 建立叢集時指定 HDInsight 版本。

| 元件 | HDInsight 4.0 | HDInsight 3.6 (預設) | HDInsight 3.5 | HDInsight 3.4 | HDInsight 3.3 | HDInsight 3.2 |
|---------------------------|---------------|-----------------------------|---------------|---------------|---------------|----------------------|
| Hortonworks Data Platform | 3.0 | 2.6 | 2.5 | 2.4 | 2.3 | 2.2 |
| Apache Hadoop 和 YARN | 3.1.1 | 2.7.3 | 2.7.3 | 2.7.1 | 2.7.1 | 2.6.0 |
| Apache Tez | 0.9.1 | 0.7.0 | 0.7.0 | 0.7.0 | 0.7.0 | 0.5.2 |
| Apache Pig | 0.16.0 | 0.16.0 | 0.16.0 | 0.15.0 | 0.15.0 | 0.14.0 |
| Apache Hive 和 HCatalog | - | 1.2.1 | 1.2.1 | 1.2.1 | 1.2.1 | 0.14.0 |
| Apache Hive | 3.1.0 | 2.1.0 | - | - | - | - |
| Apache Tez Hive2 | - | 0.8.4 | - | - | - | - |
| Apache Ranger | 1.1.0 | 0.7.0 | 0.6.0 | - | - | - |
| Apache HBase (英文) | 2.0.1 | 1.1.2 | 1.1.2 | 1.1.2 | 1.1.1 | 0.98.4 |
| Apache Sqoop | 1.4.7 | 1.4.6 | 1.4.6 | 1.4.6 | 1.4.6 | 1.4.5 |
| Apache Oozie | 4.3.1 | 4.2.0 | 4.2.0 | 4.2.0 | 4.2.0 | 4.1.0 |
| Apache Zookeeper | 3.4.6 | 3.4.6 | 3.4.6 | 3.4.6 | 3.4.6 | 3.4.6 |
| Apache Storm | - | 1.1.0 | 1.0.1 | 0.10.0 | 0.10.0 | 0.9.3 |
| Apache Mahout | - | 0.9.0+ | 0.9.0+ | 0.9.0+ | 0.9.0+ | 0.9.0 |
| Apache Phoenix | 5 | 4.7.0 | 4.7.0 | 4.4.0 | 4.4.0 | 4.2.0 |
| Apache Spark | 2.3.2 | 2.3.0, 2.2.0, 2.1.0 | 1.6.2、2.0 | 1.6.0 | 1.5.2 | 1.3.1 (僅限 Windows) |
| Apache Livy | 0.5 | 0.4 | 0.3 | 0.3 | 0.2 | - |
| Apache Kafka | 1.1.1 | 1.1、1.0 *(請參閱下方注意事項) | 0.10.0 | 0.9.0 | - | - |
| Apache Ambari | 2.7.0 | 2.6.0 | 2.4.0 | 2.2.1 | 2.1.0 | - |
| Apache Zeppelin | 0.8.0 | 0.7.0 | - | - | - | - |
| Mono | 4.2.1 | 4.2.1 | 4.2.1 | 3.2.8 | - | - |

> [!NOTE]
> 基於系統效能考量，Kafka 0.10 版的支援已在 2019 年 3 月過期。

## <a name="check-for-current-hadoop-component-version-information"></a>檢查目前的 Hadoop 元件版本資訊

在 HDInsight 的更新中，可能會變更與 HDInsight 叢集版本相關聯的 Hadoop 生態系統元件版本。 若要檢查 Hadoop 元件和驗證叢集所使用的版本，請使用 Ambari REST API。 **GetComponentInformation** 命令會擷取服務元件的相關資訊。 如需詳細資訊，請參閱 [Apache Ambari 文件][ambari-docs]。

> [!IMPORTANT]    
> Linux 是 HDInsight 版本 3.4 或更新版本上唯一使用的作業系統。 如需詳細資訊，請參閱 [HDInsight 上的 Windows 停用項目](#hdinsight-windows-retirement)。

### <a name="release-notes"></a>版本資訊

請參閱 [HDInsight 版本資訊](hdinsight-release-notes.md) ，以取得 HDInsight 最新版本的其他版本資訊。

## <a name="supported-hdinsight-versions"></a>支援的 HDInsight 辦本

下表列出 HDInsight 的版本。 其中會列出對應至每個 HDInsight 版本的 HDP 版本以及產品發行日期。 如果已知支援到期日與停用日期，也會一併提供。

### <a name="available-versions"></a>可用的版本

下表列出 Azure 入口網站中可用的 HDInsight 版本，以及諸如 PowerShell 和 .NET SDK 等其他部署方法。

| HDInsight 版本 | HDP 版本 | VM OS | 發行日期 | 支援到期日 | 停用日期 | 高可用性 |  Azure 入口網站上的可用性 | 
| --- | --- | --- | --- | --- | --- | --- | --- |
| HDInsight 4.0 |HDP 3.0 |Ubuntu 16.0.4 LTS |2018 年 9 月 24 日 | | |yes |yes |
| HDInsight 3.6 |HDP 2.6 |Ubuntu 16.0.4 LTS |2017 年 4 月 4 日 | 2020 年 6 月 30日 |2020 年 12 月 31 日 |yes |yes |


> [!NOTE]  
> 當版本的支援到期之後，可能就無法透過 Microsoft Azure 入口網站取得它。 不過，您可透過 Windows PowerShell [New-AzHDInsightCluster](https://docs.microsoft.com/powershell/module/az.hdinsight/new-azhdinsightcluster) 命令中的 `Version` 參數和 .NET SDK 持續取得叢集版本，直到版本停用日期為止。
>

### <a name="retired-versions"></a>已淘汰的版本

下表列出**不**可在 Azure 入口網站中取得的 HDInsight 版本。

| HDInsight 版本 | HDP 版本 | VM OS | 發行日期 | 支援到期日 | 停用日期 | 高可用性 |  Azure 入口網站上的可用性 | 
| --- | --- | --- | --- | --- | --- | --- | --- |
| HDInsight 3.5 <br> (非 Spark) |HDP 2.5 |Ubuntu 16.0.4 LTS |2016 年 9 月 30 日 |2017 年 9 月 5 日 |2018 年 6 月 28 日 |yes |否 |
| HDInsight 3.4 |HDP 2.4 |Ubuntu 14.0.4 LTS |2016 年 3 月 29 日 |2016 年 12 月 29 日 |2018 年 1 月 9 日 |yes |否 |
| HDInsight 3.3 |HDP 2.3 |Windows Server 2012 R2 |2015 年 12 月 2 日 |2016 年 6 月 27 日 |2018 年 7 月 31 日 |yes |否 |
| HDInsight 3.3 |HDP 2.3 |Ubuntu 14.0.4 LTS |2015 年 12 月 2 日 |2016 年 6 月 27 日 |2017 年 7 月 31 日 |yes |否 |
| HDInsight 3.2 |HDP 2.2 |Ubuntu 12.04 LTS 或 Windows Server 2012 R2 |2015 年 2 月 18 日 |2016 年 3 月 1 日 |2017 年 4 月 1 日 |yes |否 |
| HDInsight 3.1 |HDP 2.1 |Windows Server 2012 R2 |2014 年 6 月 24 日 |2015 年 5 月 18 日 |2016 年 6 月 30 日 |yes |否 |
| HDInsight 3.0 |HDP 2.0 |Windows Server 2012 R2 |2014 年 2 月 11 日 |2014 年 9 月 17 日 |2015 年 6 月 30 日 |yes |否 |
| HDInsight 2.1 |HDP 1.3 |Windows Server 2012 R2 |2013 年 10 月 28 日 |2014 年 5 月 12 日 |2015 年 5 月 31 日 |yes |否 |
| HDInsight 1.6 |HDP 1.1 | |2013 年 10 月 28 日 |2014 年 4 月 26 日 |2015 年 5 月 31 日 |否 |否 |

> [!NOTE]  
> 依預設，系統會為 HDInsight 2.1 和更新版本部署具有兩個前端節點的高可用性叢集。 它們不適用於 HDInsight 版本 1.6 叢集。

## <a name="enterprise-security-package-for-hdinsight"></a>適用於 HDInsight 的企業安全性套件

企業安全性是選擇性的套件，您可以在建立叢集工作流程時，新增於 HDInsight 叢集上。 企業安全性套件支援：

- 與 Active Directory 整合以進行驗證。

    在過去，您只能使用本機系統管理員使用者和本機 SSH 使用者來建立 HDInsight 叢集。 本機系統管理員使用者可以存取所有的檔案、資料夾、資料表和資料行。  您可以利用企業安全性套件來啟用角色型存取控制，方法是將 HDInsight 叢集與您自己的 Active Directory 整合，叢集中會包含 IaaS 虛擬機器上的內部部署 Active Directory、Azure Active Directory Domain Services 或 Active Directory。 叢集上的網域系統管理員可以授與使用者使用自己公司 (網域) 的使用者名稱和密碼來存取叢集。 

    如需詳細資訊，請參閱

    - [已加入網域之 HDInsight 叢集的 Apache Hadoop 安全性簡介](./domain-joined/apache-domain-joined-introduction.md)
    - [規劃 HDInsight 中已加入網域的 Azure Apache Hadoop 叢集](./domain-joined/apache-domain-joined-architecture.md)
    - [設定已加入網域的沙箱環境](./domain-joined/apache-domain-joined-configure.md)
    - [使用 Azure Active Directory Domain Services 設定已加入網域的 HDInsight 叢集](./domain-joined/apache-domain-joined-configure-using-azure-adds.md)

- 資料的授權

  - 與 Apache Ranger 整合可取得 Hive、Spark SQL 和 Yarn 佇列的授權。
  - 您可以設定檔案和資料夾的存取控制。

    如需詳細資訊，請參閱

  - [在已加入網域的 HDInsight 中設定 Apache Hive 原則](./domain-joined/apache-domain-joined-run-hive.md)

- 檢視稽核記錄可監視存取和設定的原則。 

### <a name="supported-cluster-types"></a>支援的叢集類型

目前，只有下列叢集類型支援企業安全性套件：

- Hadoop (僅限 HDInsight 3.6)
- Spark
- 互動式查詢

### <a name="support-for-azure-data-lake-storage"></a>支援 Azure Data Lake Storage

企業安全性套件支援使用 Azure Data Lake Storage 同時作為主要儲存體和附加元件儲存體。

### <a name="pricing-and-sla"></a>價格和 SLA

如需企業安全性套件之定價和 SLA 的詳細資訊，請參閱 [HDInsight 定價](https://azure.microsoft.com/pricing/details/hdinsight/)。

## <a name="hdinsight-windows-retirement"></a>HDInsight Windows 停用項目

Microsoft Azure HDInsight 版本 3.3 是 Windows 上的最後一個 HDInsight 版本。 Windows 上 HDInsight 的停用日期是 2018 年 7 月 31 日。 如果您在 Windows 上有任何 3.3 版或更早版本的 HDInsight 叢集，就必須在 2018 年 7 月 31 日之前移轉至 Linux 上的 HDInsight 叢集 (HDInsight 版本 3.5 或更新版本)。 移轉至 Linux OS，可讓您保留建立或調整 HDInsight 叢集的能力。 對於 Windows 上 HDInsight 版本 3.3 的支援已在 2016 年 6 月 27 日到期。

從 HDInsight 版本 3.4 開始，Microsoft 只在 Linux OS 上發行 HDInsight。 因此，HDInsight 內的部分元件僅適用於 Linux。 這些包含 [Apache Ranger](https://ranger.apache.org/)、[Apache Kafka](https://kafka.apache.org/)、互動式查詢、[Apache Spark](https://spark.apache.org/)、HDInsight 應用程式，以及作為主要檔案系統的 Azure Data Lake Storage。 未來的 HDInsight 版本僅適用於 Linux OS。 未來不會在 Windows 上推出任何 HDInsight 版本。 

## <a name="faqs"></a>常見問題集

### <a name="what-is-the-timeline-for-retiring-hdinsight-on-windows"></a>停用 Windows 上 HDInsight 的時間表為何？

Windows 上 HDInsight 的停用日期是 2018 年 7 月 31 日。 如果您的區域與規劃的停用日期不同，就會個別通知您。 

### <a name="what-is-the-impact-of-retiring-hdinsight-on-windows-for-existing-customers"></a>停用 Windows 上的 HDInsight 會對現有客戶產生何種影響？

停用 Windows 上的 HDInsight 之後，您便無法建立新的 HDInsight Windows 叢集，或調整現有的 HDInsight Windows 叢集。 對於 HDInsight 版本 3.3 的支援已在 2016 年 6 月 27 日到期。 因此，不會對 HDInsight 3.3 或更早版本提供任何支援或錯誤修正。 未來的 HDInsight 版本僅適用於 Linux OS。 未來不會在 Windows 上推出任何 HDInsight 版本。
 
### <a name="which-versions-of-hdinsight-on-windows-are-affected"></a>哪些 Windows 上的 HDInsight 版本會受到影響？

Azure HDInsight 版本 3.3 是最後一個適用於 Windows 的 HDInsight 版本。 您必須在 Windows 上的 HDInsight 停用之前，將所有的 HDInsight Windows 叢集版本 3.3 或更早版本移轉至 Linux 上的 HDInsight 版本 3.5 或更新版本。 將您的叢集移轉至 Linux 上的 HDInsight，可讓您保留建立新叢集或調整現有叢集的能力。 

### <a name="what-do-i-need-to-do"></a>我需要做什麼事嗎？

在 2018 年 7 月 31 日之前，將您的 HDInsight Windows 叢集移轉到支援的 HDInsight Linux 叢集。 深入了解 [HDInsight 移轉文件](hdinsight-migrate-from-windows-to-linux.md)。 如需 Azure HDInsight 版本的詳細資訊，請參閱[支援的版本](hdinsight-component-versioning.md#supported-hdinsight-versions)清單。 

### <a name="where-do-i-find-the-cluster-os-type"></a>哪裡可以找到叢集 OS 類型？

在 Azure 入口網站中，移至 HDInsight 叢集的概觀頁面，然後在 [基本資訊]  下方找到 [叢集類型]  。 叢集 OS 類型會列在該頁面上。 

### <a name="i-cant-migrate-to-an-hdinsight-linux-cluster-by-july-31-2018-what-is-the-impact-to-my-hdinsight-windows-cluster"></a>我無法在 2018 年 7 月 31 日之前移轉至 HDInsight Linux 叢集。 這樣會對我的 HDInsight Windows 叢集產生何種影響？

HDInsight Windows 叢集會依原樣執行，但您無法建立新的 HDInsight Windows 叢集，或調整現有的 HDInsight Windows 叢集。 

### <a name="my-cluster-has-a-net-dependency-how-do-i-resolve-this-dependency-on-linux"></a>我的叢集具有 .NET 相依性。 如何在 Linux 上解析此相依性？

您可以使用 [Mono 專案](https://www.mono-project.com/) \(英文\) 來解析 Linux 叢集相依性。 這個 .NET 開放原始碼實作適用於 HDInsight Linux 叢集。 深入了解 [HDInsight 移轉文件](hdinsight-migrate-from-windows-to-linux.md)。 

### <a name="im-a-new-customer-for-hdinsight-on-windows-how-can-i-create-an-hdinsight-windows-cluster"></a>我是 Windows 上 HDInsight 的新客戶。 該如何建立 HDInsight Windows 叢集？

從 2017 年 7 月 3 日開始，只有現有的 HDInsight Windows 客戶可以建立新的 HDInsight Windows 叢集。 新客戶無法在 Azure 入口網站中，使用 PowerShell 或 SDK 來建立 HDInsight Windows 叢集。 我們建議新客戶建立 Linux HDInsight 叢集。 現有的客戶可以在 Windows上 HDInsight 的停用日期之前，建立新的 HDInsight Windows 叢集。 

### <a name="is-there-a-pricing-impact-associated-with-moving-from-hdinsight-on-windows-to-hdinsight-on-linux"></a>從 Windows 上的 HDInsight 移至 Linux 上的 HDInsight 是否會有相關聯的價格影響？

否，此價格對於任一 OS 上的 HDInsight 都一樣。 

### <a name="what-are-the-customer-advantages-associated-with-the-move-to-only-using-hdinsight-on-linux"></a>移至僅使用 Linux 上的 HDInsight，對客戶有哪些相關聯的優點？

* 透過 HDInsight 服務，可對開放原始碼巨量資料技術提供快速的上市時間
* 支援大型社群和生態系統
* 能夠讓開放原始碼社群練習積極開發 Hadoop 和較新的巨量資料技術

### <a name="does-hdinsight-on-linux-provide-additional-functionality-beyond-what-is-available-in-hdinsight-on-windows"></a>Linux 上的 HDInsight 是否提供 Windows 上的 HDInsight 中未提供的額外功能？

從 HDInsight 版本 3.4 開始，Microsoft 只在 Linux OS 上發行 HDInsight。 因此，HDInsight 內的部分元件僅適用於 Linux。 這些包含 Apache Ranger、Kafka、「互動式查詢」、Spark、HDInsight 應用程式，以及作為主要檔案系統的 Azure Data Lake Storage。 

## <a name="service-level-agreement-for-hdinsight-cluster-versions"></a>HDInsight 叢集版本的服務等級協定

服務等級協定 (SLA) 是根據「支援期間」  來定義。 支援期間是 Microsoft 客戶服務與支援中心支援 HDInsight 叢集版本的一段時間。 如果已超過版本的「支援到期日」  ，則表示該 HDInsight 叢集不在支援期間內。 如需支援版本的詳細資訊，請參閱[支援的 HDInsight 叢集版本](hdinsight-migrate-from-windows-to-linux.md)清單。 特定 HDInsight 版本 X (在較新的 X+1 版本推出後) 的支援到期日計算方式會以下列較晚的時間為準：  

* 公式 1：將 HDInsight 叢集版本 X 的發行日期加上 180 天。
* 公式 2：將 Azure 入口網站中推出 HDInsight 叢集版本 X+1 的日期加上 90 天。

「停用日期」  是在此日期之後便無法在 HDInsight 上建立叢集版本。 從 2017 年 7 月 31 日開始，您就無法在 HDInsight 叢集的停用日期之後調整叢集。 

> [!NOTE]  
> HDInsight Windows 叢集 (包括版本 2.1、3.0、3.1、3.2 和 3.3) 均可在 Azure 客體 OS 系列第 4 版上執行，此系列使用 64 位元版的 Windows Server 2012 R2。 Azure 客體 OS 系列第 4 版支援 .NET Framework 版本 4.0、4.5、4.5.1 和 4.5.2。

## <a name="hortonworks-release-notes-associated-with-hdinsight-versions"></a>與 HDInsight 版本相關聯的 Hortonworks 版本資訊

本節提供 Hortonworks Data Platform 發佈的版本資訊，以及與 HDInsight 搭配使用的 Apache 元件。
* HDInsight 叢集 4.0 版採用以 [Hortonworks Data Platform 3.0](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.0.0/release-notes/content/relnotes.html) 為基礎的 Hadoop 散發
* HDInsight 叢集 3.6 版採用以 [Hortonworks Data Platform 2.6](https://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.6.0/bk_release-notes/content/ch_relnotes.html) 為基礎的 Hadoop 散發套件。
* HDInsight 叢集 3.5 版採用以 [Hortonworks Data Platform 2.5](https://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.5.0/bk_release-notes/content/ch_relnotes_v250.html) 為基礎的 Hadoop 散發套件。 HDInsight 叢集版本 3.5 是在 Azure 入口網站中建立的「預設」  Hadoop 叢集。
* HDInsight 叢集 3.4 版採用以 [Hortonworks Data Platform 2.4](https://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.4.0/bk_HDP_RelNotes/content/ch_relnotes_v240.html)為基礎的 Hadoop 散發套件。
* HDInsight 叢集 3.3 版採用以 [Hortonworks Data Platform 2.3](https://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.3.0/bk_HDP_RelNotes/content/ch_relnotes_v230.html)為基礎的 Hadoop 散發。

  * [Apache Storm 版本資訊](https://storm.apache.org/2015/11/05/storm0100-released.html) \(英文\) 可從 Apache 網站上取得。
  * [Apache Hive 版本資訊](https://issues.apache.org/jira/secure/ReleaseNote.jspa?version=12332384&styleName=Text&projectId=12310843) \(英文\) 可從 Apache 網站上取得。
* HDInsight 叢集 3.2 版採用以 [Hortonworks Data Platform 2.2][hdp-2-2] 為基礎的 Hadoop 散發套件。

  * 以下提供特定 Apache 元件的版本資訊：[Hive 0.14](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12310843&version=12326450)、[Pig 0.14](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12310730&version=12326954)、[HBase 0.98.4](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12310753&version=12326810)、[Phoenix 4.2.0](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12315120&version=12327581)、[M/R 2.6](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12310941&version=12327180)、[HDFS 2.6](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12310942&version=12327181)、[YARN 2.6](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12313722&version=12327197)、[Common](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12310240&version=12327179)、[Tez 0.5.2](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12314426&version=12328742)、[Ambari 2.0](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12312020&version=12327486)、[Storm 0.9.3](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12314820&version=12327112) 及 [Oozie 4.1.0](https://issues.apache.org/jira/secure/ReleaseNote.jspa?version=12324960&projectId=12311620)。
* HDInsight 叢集版本 3.1 使用以 [Hortonworks Data Platform 2.1.7][hdp-2-1-7] 為基礎的 Hadoop 散發套件。 在 2014 年 11 月 7 日之前建立的 HDInsight 3.1 叢集是以 [Hortonworks Data Platform 2.1.1][hdp-2-1-1] 為基礎。
* HDInsight 叢集 3.0 版採用以 [Hortonworks Data Platform 2.0][hdp-2-0-8] 為基礎的 Hadoop 散發套件。
* HDInsight 叢集 2.1 版採用以 [Hortonworks Data Platform 1.3][hdp-1-3-0] 為基礎的 Hadoop 散發套件。
* HDInsight 叢集 1.6 版採用以 [Hortonworks Data Platform 1.1][hdp-1-1-0] 為基礎的 Hadoop 散發套件。

## <a name="default-node-configuration-and-virtual-machine-sizes-for-clusters"></a>適用於叢集的預設節點設定和虛擬機器大小

下表列出 HDInsight 叢集的預設虛擬機器 (VM) 大小。  此圖表提供必要資訊，可協助您了解建立 PowerShell 或 Azure CLI 指令碼以部署 HDInsight 叢集時，所要使用的 VM 大小。

> [!IMPORTANT]  
> 如果您的叢集需要 32 個以上的背景工作角色節點，則必須選取具有至少 8 個核心和 14 GB RAM 的前端節點大小。

* 所有支援的區域，巴西南部和日本西部除外︰

|叢集類型|Hadoop|hbase|互動式查詢|Storm|Spark|ML Server|Kafka|
|---|---|---|---|---|---|---|---|
|前端：預設 VM 大小|D12 v2|D12 v2|D13 v2|A3|D12 v2|D12 v2|D3v2|
|前端：建議的 VM 大小|D3 v2|D3 v2|D13|A4 v2|D12 v2|D12 v2|A2M v2|
||D4 v2|D4 v2|D14|A8 v2|D13 v2|D13 v2|D3 v2|
||D12 v2|D12 v2|E16 v3|A2m v2|D14 v2|D14 v2|D4 v2|
||E4 v3|E4 v3|E32 v3|E4 v3|E4 v3|E4 v3|D12 v2|
|背景工作：預設 VM 大小|D4 v2|D4 v2|D14 v2|D3 v2|D13 v2|D4 v2|4 D12v2 (每個訊息代理程式有 2 個 S30 磁碟)|
|背景工作：建議的 VM 大小|D3 v2|D3 v2|D13|D3 v2|D4 v2|D4 v2|D13 v2|
||D4 v2|D4 v2|D14|D4 v2|D12 v2|D12 v2|DS12 v2|
||D12 v2|D12 v2|E16 v3|D12 v2|D13 v2|D13 v2|DS13 v2|
||E4 v3|E4 v3|E20 v3|E4 v3|D14 v2|D14 v2|E4 v3|
||||E32 v3||E16 v3|E16 v3|ES4 v3|
||||E64 v3||E20 v3|E20 v3|E8 v3|
||||||E32 v3|E32 v3|ES8 v3|
||||||E64 v3|E64 v3||
|ZooKeeper：預設 VM 大小||A4 v2|A4 v2|A4 v2||A2 v2|D3v2|
|ZooKeeper：建議的 VM 大小||A4 v2||A2 v2|||A2M v2|
|||A8 v2||A4 v2|||D3 v2|
|||A2m v2||A8 v2|||E8 v3|
|邊緣：預設 VM 大小||||||D4 v2||
|邊緣：建議的 VM 大小||||||D4 v2||
|||||||D12 v2||
|||||||D13 v2||
|||||||D14 v2||
|||||||E16 v3||
|||||||E20 v3||
|||||||E32 v3||
|||||||E64 v3||

* 僅限巴西南部和日本西部 (沒有 v2 大小)：

  | 叢集類型 | Hadoop | hbase | 互動式查詢 |Storm | Spark | ML 服務 |
  | --- | --- | --- | --- | --- | --- | --- |
  | 前端：預設 VM 大小 |D12 |D12  | D13 |A3 |D12 |D12 |
  | 前端：建議的 VM 大小 |D3、<br/> D4、<br/> D12 |D3、<br/> D4、<br/> D12  | D13、<br/> D14 |A3、<br/> A4、<br/> A5 |D12、<br/> D13、<br/> D14 |D12、<br/> D13、<br/> D14 |
  | 背景工作：預設 VM 大小 |D4 |D4  |  D14 |D3 |D13 |D4 |
  | 背景工作：建議的 VM 大小 |D3、<br/> D4、<br/> D12 |D3、<br/> D4、<br/> D12  | D13、<br/> D14 |D3、<br/> D4、<br/> D12 |D4、<br/> D12、<br/> D13、<br/> D14 | D4、<br/> D12、<br/> D13、<br/> D14 |
  | ZooKeeper：預設 VM 大小 | |A4 v2 | A4 v2| A4 v2 | | A2 v2|
  | ZooKeeper：建議的 VM 大小 | |A2、<br/> A3、<br/> A4 | |A2、<br/> A3、<br/> A4 | | |
  | 邊緣：預設 VM 大小 | | | | | |D4 |
  | 邊緣：建議的 VM 大小 | | | | | |D4、<br/> D12、<br/> D13、<br/> D14 |

> [!NOTE]
> - 前端稱為 Storm 叢集類型的 Nimbus  。
> - 背景工作角色稱為 Storm 叢集類型的「監督員」  。
> - 背景工作角色稱為 HBase 叢集類型的「區域」  。

## <a name="next-steps"></a>後續步驟
- [使用 Hadoop、Spark 及 Kafka 等在 HDInsight 中設定叢集](hdinsight-hadoop-provision-linux-clusters.md)
- [從 Windows PC 在 HDInsight 上的 Hadoop 生態系統中作業](hdinsight-hadoop-windows-tools.md)

[Supported HDInsight versions]:(#supported-hdinsight-versions)

[image-hdi-versioning-versionscreen]: ./media/hdinsight-component-versioning/hdi-versioning-version-screen.png

[wa-forums]: https://azure.microsoft.com/support/forums/

[connect-excel-with-hive-ODBC]: hdinsight-connect-excel-hive-ODBC-driver.md

[hdp-2-2]: https://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.2.9/bk_HDP_RelNotes/content/ch_relnotes_v229.html

[hdp-2-1-7]: https://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.1.7-Win/bk_releasenotes_HDP-Win/content/ch_relnotes-HDP-2.1.7.html

[hdp-2-1-1]: https://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.1.1/bk_releasenotes_hdp_2.1/content/ch_relnotes-hdp-2.1.1.html

[hdp-2-0-8]: https://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.0.8.0/bk_releasenotes_hdp_2.0/content/ch_relnotes-hdp2.0.8.0.html

[hdp-1-3-0]: https://docs.hortonworks.com/HDPDocuments/HDP1/HDP-1.3.0/bk_releasenotes_hdp_1.x/content/ch_relnotes-hdp1.3.0_1.html

[hdp-1-1-0]: https://docs.hortonworks.com/HDPDocuments/HDP1/HDP-1.3.0/bk_releasenotes_hdp_1.x/content/ch_relnotes-hdp1.1.1.16_1.html

[ambari-docs]: https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md

[zookeeper]: https://zookeeper.apache.org/
