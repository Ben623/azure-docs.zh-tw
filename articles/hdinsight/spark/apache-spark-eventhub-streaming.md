---
title: 教學課程：使用 HDInsight 中的 Apache Spark 處理來自 Azure 事件中樞的資料
description: 教學課程 - 將 Azure HDInsight 中的 Apache Spark 連線至 Azure 事件中樞，並處理串流資料。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,mvc
ms.topic: tutorial
ms.date: 05/24/2019
ms.openlocfilehash: be21b809272a132ee6e63582036c36ad5dcdf4ad
ms.sourcegitcommit: 0576bcb894031eb9e7ddb919e241e2e3c42f291d
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/15/2019
ms.locfileid: "71266201"
---
# <a name="tutorial-process-tweets-using-azure-event-hubs-and-apache-spark-in-hdinsight"></a>教學課程：使用 Azure 事件中樞與 HDInsight 中的 Apache Spark 處理推文

在本教學課程中，您會了解如何建立 [Apache Spark](https://spark.apache.org/) \(英文\) 串流應用程式以將推文傳送至 Azure 事件中樞，並建立另一個應用程式以便從事件中樞讀取推文。 如需 Spark 串流的詳細說明，請參閱 [Apache Spark 串流概觀](https://spark.apache.org/docs/latest/streaming-programming-guide.html#overview)。 HDInsight 會將相同的串流功能帶入 Azure 上的 Spark 叢集。

在本教學課程中，您會了解如何：
> [!div class="checklist"]
> * 將訊息傳送至 Azure 事件中樞
> * 從 Azure 事件中樞讀取訊息

如果您沒有 Azure 訂用帳戶，請在開始之前先[建立免費帳戶](https://azure.microsoft.com/free/)。

## <a name="prerequisites"></a>必要條件

* HDInsight 上的 Apache Spark 叢集。 請參閱[建立 Apache Spark 叢集](./apache-spark-jupyter-spark-sql-use-portal.md)。

* 熟悉如何搭配使用 Jupyter Notebook 和 HDInsight 上的 Spark。 如需詳細資訊，請參閱[使用 HDInsight 上的 Apache Spark 載入資料及執行查詢](./apache-spark-load-data-run-query.md)。

* [Twitter 帳戶](https://twitter.com/i/flow/signup)。

## <a name="create-a-twitter-application"></a>建立 Twitter 應用程式

若要收到推文的串流，您必須在 Twitter 中建立應用程式。 依照下列指示建立 Twitter 應用程式，並寫下完成本教學課程所需的值。

1. 瀏覽至 [Twitter 應用程式管理](https://apps.twitter.com/) \(英文\)。

1. 選取 [Create New App]  \(建立新的應用程式\)。

1. 提供下列值：

    |屬性 |值 |
    |---|---|
    |名稱|提供應用程式名稱。 針對本教學課程所使用的值是 **HDISparkStreamApp0423**。 此名稱必須是唯一的名稱。|
    |說明|提供應用程式的簡短描述。 針對本教學課程所使用的值是**簡單的 HDInsight Spark 串流應用程式**。|
    |網站|提供應用程式的網站。 不需是有效的網站。  本教學課程使用的值為 `http://www.contoso.com`。|
    |回呼 URL|您可以將其保留為空白。|

1. 選取 [Yes, I have read and agree to the Twitter Developer Agreement]  \(是，我已閱讀並同意 Twitter 開發人員合約\)，然後選取 [Create your Twitter application]  \(建立 Twitter 應用程式\)。

1. 選取 [ **金鑰和存取權杖** ] 索引標籤。

1. 選取頁面底部的 [Create my access token]  \(建立我的存取權杖\)。

1. 寫下頁面上的下列值。  在本教學課程後續的內容中，您會需要這些值：

    - **取用者金鑰 (API 金鑰)**    
    - **取用者祕密 (API 祕密)**  
    - **存取權杖**
    - **存取權杖祕密**   

## <a name="create-an-azure-event-hubs-namespace"></a>建立 Azure 事件中樞命名空間

您會使用此事件中樞來儲存推文。

1. 登入 [Azure 入口網站](https://portal.azure.com)。 

2. 從左側功能表中選取 [所有服務]  。  

3. 在 [物聯網]  底下選取 [事件中樞]  。 

    ![建立 Spark 串流範例的事件中樞](./media/apache-spark-eventhub-streaming/hdinsight-create-event-hub-for-spark-streaming.png "建立 Spark 串流範例的事件中樞")

4. 選取 [+ 新增]  。

5. 針對新的事件中樞命名空間，輸入下列值：

    |屬性 |值 |
    |---|---|
    |名稱|輸入事件中樞的名稱。  針對本教學課程所使用的值是 **myeventhubns20180403**。|
    |定價層|選取 [標準]  。|
    |訂用帳戶|選取適當的訂用帳戶。|
    |資源群組|從下拉式清單中選取現有的資源群組，或選取 [新建]  來建立新的資源群組。|
    |位置|選取與 HDInsight 中 Apache Spark 叢集相同的**位置**，以降低延遲和成本。|
    |啟用自動擴充 (選用) |自動擴充會在您流量超過指派給事件中樞命名空間的輸送量單位容量時，自動調整指派的輸送量單位數目。  |
    |自動擴充輸送量單位上限 (選用)|此滑桿只會在您核取 [啟用自動擴充]  時出現。  |

    ![提供 Spark 串流範例的事件中樞名稱](./media/apache-spark-eventhub-streaming/hdinsight-provide-event-hub-name-for-spark-streaming.png "提供 Spark 串流範例的事件中樞名稱")

6. 選取 [建立]  來建立命名空間。  部署將會在幾分鐘內完成。

## <a name="create-an-azure-event-hub"></a>建立 Azure 事件中樞
部署好事件中樞命名空間後，請建立事件中樞。  從入口網站：

1. 從左側功能表中選取 [所有服務]  。  

1. 在 [物聯網]  底下選取 [事件中樞]  。  

1. 從清單中選取您的事件中樞命名空間。  

1. 在 [事件中樞命名空間]  頁面中，選取 [+ 事件中樞]  。  
1. 在 [建立事件中樞]  頁面中輸入下列值：

    - **名稱**：提供事件中樞的名稱。 
 
    - **分割區計數**：10.  

    - **訊息保留期**：1.   
   
      ![提供 Spark 串流範例的事件中樞詳細資料](./media/apache-spark-eventhub-streaming/hdinsight-provide-event-hub-details-for-spark-streaming-example.png "提供 Spark 串流範例的事件中樞詳細資料")

1. 選取 [建立]  。  部署應該會在幾秒鐘內完成，而您將返回 [事件中樞命名空間] 頁面。

1. 在 [設定]  底下，選取 [共用存取原則]  。

1. 選取 [RootManageSharedAccessKey]  。
    
     ![設定 Spark 串流範例的事件中樞原則](./media/apache-spark-eventhub-streaming/hdinsight-set-event-hub-policies-for-spark-streaming-example.png "設定 Spark 串流範例的事件中樞原則")

1. 儲存**主索引鍵**和**連接字串-主索引鍵**值，以便稍後在本教學課程中使用。

     ![檢視 Spark 串流範例的事件中樞原則金鑰](./media/apache-spark-eventhub-streaming/hdinsight-view-event-hub-policy-keys.png "檢視 Spark 串流範例的事件中樞原則金鑰")


## <a name="send-tweets-to-the-event-hub"></a>將推文傳送至事件中樞

建立一個 Jupyter Notebook，並將其命名為 **SendTweetsToEventHub**。 

1. 執行下列程式碼，以新增外部 Apache Maven 程式庫：

    ```
    %%configure
    {"conf":{"spark.jars.packages":"com.microsoft.azure:azure-eventhubs-spark_2.11:2.3.13,org.twitter4j:twitter4j-core:4.0.6"}}
    ```

2. 編輯下列程式碼，將 `<Event hub name>`、`<Event hub namespace connection string>`、`<CONSUMER KEY>`、`<CONSUMER SECRET>`、`<ACCESS TOKEN>` 和 `<TOKEN SECRET>` 取代為適當的值。 執行編輯後的程式碼，將推文傳送到您的事件中樞：

    ```scala
    import java.util._
    import scala.collection.JavaConverters._
    import java.util.concurrent._
    
    import org.apache.spark._
    import org.apache.spark.streaming._
    import org.apache.spark.eventhubs.ConnectionStringBuilder

    // Event hub configurations
    // Replace values below with yours        
    val eventHubName = "<Event hub name>"
    val eventHubNSConnStr = "<Event hub namespace connection string>"
    val connStr = ConnectionStringBuilder(eventHubNSConnStr).setEventHubName(eventHubName).build 
    
    import com.microsoft.azure.eventhubs._
    val pool = Executors.newFixedThreadPool(1)
    val eventHubClient = EventHubClient.create(connStr.toString(), pool)
    
    def sendEvent(message: String) = {
          val messageData = EventData.create(message.getBytes("UTF-8"))
          eventHubClient.get().send(messageData)
          println("Sent event: " + message + "\n")
    }
    
    import twitter4j._
    import twitter4j.TwitterFactory
    import twitter4j.Twitter
    import twitter4j.conf.ConfigurationBuilder

    // Twitter application configurations
    // Replace values below with yours   
    val twitterConsumerKey = "<CONSUMER KEY>"
    val twitterConsumerSecret = "<CONSUMER SECRET>"
    val twitterOauthAccessToken = "<ACCESS TOKEN>"
    val twitterOauthTokenSecret = "<TOKEN SECRET>"
    
    val cb = new ConfigurationBuilder()
    cb.setDebugEnabled(true).setOAuthConsumerKey(twitterConsumerKey).setOAuthConsumerSecret(twitterConsumerSecret).setOAuthAccessToken(twitterOauthAccessToken).setOAuthAccessTokenSecret(twitterOauthTokenSecret)
    
    val twitterFactory = new TwitterFactory(cb.build())
    val twitter = twitterFactory.getInstance()

    // Getting tweets with keyword "Azure" and sending them to the Event Hub in realtime!
    
    val query = new Query(" #Azure ")
    query.setCount(100)
    query.lang("en")
    var finished = false
    while (!finished) {
      val result = twitter.search(query)
      val statuses = result.getTweets()
      var lowestStatusId = Long.MaxValue
      for (status <- statuses.asScala) {
        if(!status.isRetweet()){
          sendEvent(status.getText())
        }
        lowestStatusId = Math.min(status.getId(), lowestStatusId)
        Thread.sleep(2000)
      }
      query.setMaxId(lowestStatusId - 1)
    }
    
    // Closing connection to the Event Hub
    eventHubClient.get().close()
    ```

3. 在 Azure 入口網站中開啟事件中樞。  在 [概觀]  上，您應該會看到某些圖表顯示傳送至事件中樞的訊息。

## <a name="read-tweets-from-the-event-hub"></a>從事件中樞讀取推文

建立另一個 Jupyter Notebook，並將其命名為 **ReadTweetsFromEventHub**。 

1. 執行下列程式碼，以新增外部 Apache Maven 程式庫：

    ```
    %%configure -f
    {"conf":{"spark.jars.packages":"com.microsoft.azure:azure-eventhubs-spark_2.11:2.3.13"}}
    ```

2. 編輯下列程式碼，將 `<Event hub name>` 和 `<Event hub namespace connection string>` 取代為適當的值。 執行編輯後的程式碼，從您的事件中樞讀取推文：

    ```scala
    import org.apache.spark.eventhubs._
    // Event hub configurations
    // Replace values below with yours        
    val eventHubName = "<Event hub name>"
    val eventHubNSConnStr = "<Event hub namespace connection string>"
    val connStr = ConnectionStringBuilder(eventHubNSConnStr).setEventHubName(eventHubName).build
    
    val customEventhubParameters = EventHubsConf(connStr).setMaxEventsPerTrigger(5)
    val incomingStream = spark.readStream.format("eventhubs").options(customEventhubParameters.toMap).load()
    //incomingStream.printSchema    
    
    import org.apache.spark.sql.types._
    import org.apache.spark.sql.functions._
    
    // Event Hub message format is JSON and contains "body" field
    // Body is binary, so you cast it to string to see the actual content of the message
    val messages = incomingStream.withColumn("Offset", $"offset".cast(LongType)).withColumn("Time (readable)", $"enqueuedTime".cast(TimestampType)).withColumn("Timestamp", $"enqueuedTime".cast(LongType)).withColumn("Body", $"body".cast(StringType)).select("Offset", "Time (readable)", "Timestamp", "Body")
    
    messages.printSchema
    
    messages.writeStream.outputMode("append").format("console").option("truncate", false).start().awaitTermination()
    ```

## <a name="clean-up-resources"></a>清除資源

利用 HDInsight，會將您的資料儲存於 Azure 儲存體或 Azure Data Lake Storage 中，以便您在未使用叢集時安全地進行刪除。 您也需支付 HDInsight 叢集的費用 (即使未使用)。 如果您打算立即進行下一個教學課程，則可能想要保留叢集，否則可直接刪除叢集。

在 Azure 入口網站中開啟叢集，然後選取 [刪除]  。

![HDInsight Azure 入口網站刪除叢集](./media/apache-spark-load-data-run-query/hdinsight-azure-portal-delete-cluster.png "刪除 HDInsight 叢集")

您也可以選取資源群組名稱來開啟資源群組頁面，然後選取 [刪除資源群組]  。 刪除資源群組時，會同時刪除 HDInsight Spark 叢集及預設儲存體帳戶。

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已了解如何建立 Apache Spark 串流應用程式以將推文傳送至 Azure 事件中樞，並建立另一個應用程式從事件中樞讀取推文。  前進到下一篇文章，以查看您如何建立機器學習應用程式。

> [!div class="nextstepaction"]
> [建立機器學習應用程式](./apache-spark-ipython-notebook-machine-learning.md)
