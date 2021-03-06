---
title: 搭配 HDInsight 的 Hadoop 上的 MapReduce 使用 C# - Azure
description: 了解如何搭配 Azure HDInsight 的 Apache Hadoop 使用 C# 以建立 MapReduce 解決方案。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 11/22/2019
ms.openlocfilehash: 025b5c5c1e3b8543111e112202906ef6f1fdb482
ms.sourcegitcommit: c31dbf646682c0f9d731f8df8cfd43d36a041f85
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/27/2019
ms.locfileid: "74561792"
---
# <a name="use-c-with-mapreduce-streaming-on-apache-hadoop-in-hdinsight"></a>搭配 HDInsight 的 Apache Hadoop 上的 MapReduce 串流使用 C#

了解如何使用 C# 在 HDInsight 上建立 MapReduce 方案。

Apache Hadoop 串流是一個公用程式，可讓您使用指令碼或可執行檔執行 MapReduce 作業。 在此範例中，.NET 用來實作字數統計方案的對應工具和歸納器。

## <a name="net-on-hdinsight"></a>HDInsight 上的 .NET

HDInsight 叢集使用[Mono （ https://mono-project.com)](https://mono-project.com)執行 .net 應用程式。 4\.2.1 版的 Mono 隨附於 3.6 版的 HDInsight。 如需 HDInsight 隨附之 Mono 版本的詳細資訊，請參閱[不同 hdinsight 版本提供的 Apache Hadoop 元件](../hdinsight-component-versioning.md#apache-hadoop-components-available-with-different-hdinsight-versions)。

如需 Mono 與 .NET Framework 版本之相容性的詳細資訊，請參閱 [Mono 相容性 (英文)](https://www.mono-project.com/docs/about-mono/compatibility/)。

## <a name="how-hadoop-streaming-works"></a>Hadoop 串流的運作方式

本文件中使用於串流的基本程序如下︰

1. Hadoop 會以 STDIN 將資料傳遞給對應*程式（在*此範例中為對應工具）。
2. 對應工具會處理資料，然後發出以 Tab 分隔的機碼值組到 STDOUT。
3. Hadoop 會讀取輸出，然後在 STDIN 上傳遞至歸納器（在此範例中為*歸納器*）。
4. 歸納器會讀取以 Tab 分隔的機碼值組、處理資料，然後在 STDOUT 發出以 Tab 分隔的機碼值組格式結果。
5. Hadoop 會讀取輸出，接著輸出會寫入至輸出目錄。

如需有關串流的詳細資訊，請參閱 [Hadoop 串流](https://hadoop.apache.org/docs/r2.7.1/hadoop-streaming/HadoopStreaming.html) \(英文\)。

## <a name="prerequisites"></a>必要條件

* Visual Studio。

* 熟悉如何撰寫和建置以 .NET Framework 4.5 為目標的 C# 程式碼。

* 將 .exe 檔案上傳至叢集的方式。 本文件中的步驟使用 Data Lake Tools for Visual Studio 將檔案上傳至叢集的主要儲存體。

* 如果使用 PowerShell，您將需要[Az 模組](https://docs.microsoft.com/powershell/azure/overview)。

* SSH 用戶端（選擇性）。 如需詳細資訊，請參閱[使用 SSH 連線至 HDInsight (Apache Hadoop)](../hdinsight-hadoop-linux-use-ssh-unix.md)。

* HDInsight 上的 Apache Hadoop 叢集。 請參閱[開始在 Linux 上使用 HDInsight](../hadoop/apache-hadoop-linux-tutorial-get-started.md)。

* 您叢集主要儲存體的 [URI 配置](../hdinsight-hadoop-linux-information.md#URI-and-scheme)。 `wasb://` 適用於 Azure 儲存體，`abfs://` 適用於 Azure Data Lake Storage Gen2 或 `adl://` 適用於 Azure Data Lake Storage Gen1。 如果針對 Azure 儲存體或 Data Lake Storage Gen2 已啟用安全傳輸，則 URI 分別會是 `wasbs://` 或 `abfss://`。另請參閱[安全傳輸](../../storage/common/storage-require-secure-transfer.md)。


## <a name="create-the-mapper"></a>建立對應工具

在 Visual Studio 中，*建立名為*[對應工具] 的新 .NET Framework 主控台應用程式。 針對此應用程式使用下列程式碼：

```csharp
using System;
using System.Text.RegularExpressions;

namespace mapper
{
    class Program
    {
        static void Main(string[] args)
        {
            string line;
            //Hadoop passes data to the mapper on STDIN
            while((line = Console.ReadLine()) != null)
            {
                // We only want words, so strip out punctuation, numbers, etc.
                var onlyText = Regex.Replace(line, @"\.|;|:|,|[0-9]|'", "");
                // Split at whitespace.
                var words = Regex.Matches(onlyText, @"[\w]+");
                // Loop over the words
                foreach(var word in words)
                {
                    //Emit tab-delimited key/value pairs.
                    //In this case, a word and a count of 1.
                    Console.WriteLine("{0}\t1",word);
                }
            }
        }
    }
}
```

在您建立應用程式之後，建立它以在專案目錄中產生 */bin/Debug/mapper.exe*檔案。

## <a name="create-the-reducer"></a>建立歸納器

在 Visual Studio 中，建立名為*歸納器*的新 .NET Framework 主控台應用程式。 針對此應用程式使用下列程式碼：

```csharp
using System;
using System.Collections.Generic;

namespace reducer
{
    class Program
    {
        static void Main(string[] args)
        {
            //Dictionary for holding a count of words
            Dictionary<string, int> words = new Dictionary<string, int>();

            string line;
            //Read from STDIN
            while ((line = Console.ReadLine()) != null)
            {
                // Data from Hadoop is tab-delimited key/value pairs
                var sArr = line.Split('\t');
                // Get the word
                string word = sArr[0];
                // Get the count
                int count = Convert.ToInt32(sArr[1]);

                //Do we already have a count for the word?
                if(words.ContainsKey(word))
                {
                    //If so, increment the count
                    words[word] += count;
                } else
                {
                    //Add the key to the collection
                    words.Add(word, count);
                }
            }
            //Finally, emit each word and count
            foreach (var word in words)
            {
                //Emit tab-delimited key/value pairs.
                //In this case, a word and a count of 1.
                Console.WriteLine("{0}\t{1}", word.Key, word.Value);
            }
        }
    }
}
```

在您建立應用程式之後，建立它以在專案目錄中產生 */bin/Debug/reducer.exe*檔案。

## <a name="upload-to-storage"></a>上傳至儲存體

接下來，您*必須將對應工具*和*歸納器*應用程式上傳至 HDInsight 儲存體。

1. 在 Visual Studio 中，選取 [ **View** > **伺服器總管**]。

1. 以滑鼠右鍵按一下 [ **Azure**]，選取 **[連線至 Microsoft Azure 訂用帳戶 ...]** ，然後完成登入程式。

1. 展開您要部署此應用程式的 HDInsight 叢集。 就會列出含有文字 **(預設儲存體帳戶)** 的項目。

   ![儲存體帳戶、HDInsight 叢集、伺服器總管、Visual Studio](./media/apache-hadoop-dotnet-csharp-mapreduce-streaming/hdinsight-storage-account.png)

   * 如果 [ **（預設儲存體帳戶）** ] 專案可以展開，您就會使用**Azure 儲存體帳戶**作為叢集的預設儲存體。 若要在叢集的預設儲存體上查看檔案，請展開該專案，然後按兩下 [ **（預設容器）** ]。

   * 如果 [ **（預設儲存體帳戶）** ] 專案無法展開，表示您是使用**Azure Data Lake Storage**做為叢集的預設儲存體。 若要檢視叢集之預設儲存體上的檔案，請按兩下 [(預設儲存體帳戶)] 項目。

1. 若要上傳 .exe 檔案，請使用下列其中一種方法：

    * 如果您使用的是**Azure 儲存體帳戶**，請選取 [**上傳 Blob** ] 圖示。

        ![對應工具的 HDInsight 上傳圖示，Visual Studio](./media/apache-hadoop-dotnet-csharp-mapreduce-streaming/hdinsight-upload-icon.png)

        在 [**上傳新**檔案] 對話方塊的 [**檔案名**] 底下，選取 **[流覽]** 。 在 [**上傳 Blob** ] 對話方塊中，移至對應*工具專案的 [* *bin\debug* ] 資料夾，然後*選擇 [對應*工具] 檔案。 最後，依序選取 [**開啟** **] 和 [確定]** 以完成上傳。

    * 在 [ **Azure Data Lake Storage**] 中，以滑鼠右鍵按一下檔案清單中的空白區域，然後選取 **[上傳**]。 最後，選取對應工具 *.exe*檔案，然後選取 [**開啟**]。

    *mapper.exe* 上傳完成後，請針對 *reducer.exe* 檔案重複上傳程序。

## <a name="run-a-job-using-an-ssh-session"></a>執行工作︰使用 SSH 工作階段

下列程式描述如何使用 SSH 會話來執行 MapReduce 工作：

1. 使用[ssh 命令](../hdinsight-hadoop-linux-use-ssh-unix.md)連接到您的叢集。 以您叢集的名稱取代 CLUSTERNAME，然後輸入命令，以編輯下面的命令：

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. 使用下列其中一個命令來啟動 MapReduce 作業：

   * 如果預設儲存體為**Azure 儲存體**：

        ```bash
        yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-streaming.jar \
            -files wasbs:///mapper.exe,wasbs:///reducer.exe \
            -mapper mapper.exe \
            -reducer reducer.exe \
            -input /example/data/gutenberg/davinci.txt \
            -output /example/wordcountout
        ```

    * 如果預設儲存體為**Data Lake Storage Gen1**：

        ```bash
        yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-streaming.jar \
            -files adl:///mapper.exe,adl:///reducer.exe \
            -mapper mapper.exe \
            -reducer reducer.exe \
            -input /example/data/gutenberg/davinci.txt \
            -output /example/wordcountout
        ```

   * 如果預設儲存體為**Data Lake Storage Gen2**：

        ```bash
        yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-streaming.jar \
            -files abfs:///mapper.exe,abfs:///reducer.exe \
            -mapper mapper.exe \
            -reducer reducer.exe \
            -input /example/data/gutenberg/davinci.txt \
            -output /example/wordcountout
        ```

   下列清單說明每個參數和選項所代表的意義：

   * *hadoop-streaming.jar*：指定包含串流處理 MapReduce 功能的 jar 檔案。
   * `-files`：指定此作業的對應程式 *.exe*和*歸納器*檔。 在每個檔案之前的 `wasbs:///`、`adl:///`或 `abfs:///` 通訊協定宣告，都是叢集之預設儲存體根目錄的路徑。
   * `-mapper`：指定執行對應工具的檔案。
   * `-reducer`：指定用來執行歸納器的檔案。
   * `-input`：指定輸入資料。
   * `-output`：指定輸出目錄。

3. MapReduce 作業完成後，請使用下列命令來查看結果：

   ```bash
   hdfs dfs -text /example/wordcountout/part-00000
   ```

   下列文字是此命令所傳回資料的範例︰

   ```output
   you     1128
   young   38
   younger 1
   youngest        1
   your    338
   yours   4
   yourself        34
   yourselves      3
   youth   17
   ```

## <a name="run-a-job-using-powershell"></a>執行作業：使用 PowerShell

使用下列 PowerShell 指令碼來執行 MapReduce 作業並下載結果。

[!code-powershell[main](../../../powershell_scripts/hdinsight/use-csharp-mapreduce/use-csharp-mapreduce.ps1?range=5-87)]

此指令碼會提示您輸入叢集登入帳戶名稱和密碼，以及 HDInsight 叢集名稱。 一旦作業完成，輸出就會下載到名為*output .txt*的檔案。 以下文字是 `output.txt` 檔案中的資料範例：

```output
you     1128
young   38
younger 1
youngest        1
your    338
yours   4
yourself        34
yourselves      3
youth   17
```

## <a name="next-steps"></a>後續步驟

如需搭配 HDInsight 使用 MapReduce 的詳細資訊，請參閱在[hdinsight 上的 Apache Hadoop 中使用 mapreduce](hdinsight-use-mapreduce.md)。

如需有關使用 C# 搭配 Hive 和 Pig 的詳細資訊，請參閱[使用 C# 使用者定義函式搭配 Apache Hive 和 Apache Pig](apache-hadoop-hive-pig-udf-dotnet-csharp.md)。

如需搭配 HDInsight 上的 Storm 使用 C# 的詳細資訊，請參閱[開發適用於 Apache Storm on HDInsight 的 C# 拓撲](../storm/apache-storm-develop-csharp-visual-studio-topology.md)。
