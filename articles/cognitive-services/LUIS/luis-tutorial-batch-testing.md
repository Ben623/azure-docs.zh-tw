---
title: 教學課程：使用批次測試找出問題 - LUIS
titleSuffix: Azure Cognitive Services
description: 本教學課程示範如何使用批次測試來找出應用程式中的語句預測問題，並加以修正。
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 10/14/2019
ms.author: diberry
ms.openlocfilehash: 68a0016e034f4642c4e4ff166a1456f7ecf1ee3c
ms.sourcegitcommit: bc193bc4df4b85d3f05538b5e7274df2138a4574
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/10/2019
ms.locfileid: "73904240"
---
# <a name="tutorial-batch-test-data-sets"></a>教學課程：批次測試資料集

本教學課程示範如何使用批次測試來找出應用程式中的語句預測問題，並加以修正。  

批次測試可讓您使用一組已標示的已知語句和實體來驗證作用中且已定型的模型狀態。 在 JSON 格式的批次檔中新增語句，並在語句內設定您需要預測的實體標籤。 

批次測試的需求：

* 每個測試最多 1000 個語句。 
* 沒有重複項目。 
* 允許的實體類型：僅限簡單及複合的機器學習實體。 批次測試僅適用於機器學習的意圖和實體。

使用本教學課程以外的應用程式時，請「不要」  使用已新增到某個意圖的範例語句。 

[!INCLUDE [Waiting for LUIS portal refresh](./includes/wait-v3-upgrade.md)]

**在本教學課程中，您將了解如何：**

<!-- green checkmark -->
> [!div class="checklist"]
> * 匯入範例應用程式
> * 建立批次測試檔案 
> * 執行批次測試
> * 檢閱測試結果
> * 修正錯誤 
> * 重新測試批次

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="import-example-app"></a>匯入範例應用程式

以上一個教學課程中建立的應用程式繼續進行，其名稱為 **HumanResources**。 

請使用下列步驟：

1.  下載並儲存[應用程式的 JSON 檔案](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/documentation-samples/tutorials/custom-domain-review-HumanResources.json)。

2. 將 JSON 匯入新的應用程式中。

3. 從 [管理]  區段的 [版本]  索引標籤上，複製版本並將它命名為 `batchtest`。 複製是一個既可測試各種 LUIS 功能又不影響原始版本的絕佳方式。 因為版本名稱會作為 URL 路由的一部分，所以此名稱不能包含任何在 URL 中無效的字元。 

4. 將應用程式定型。

## <a name="batch-file"></a>批次檔

1. 在文字編輯器中建立 `HumanResources-jobs-batch.json`，或[下載此項目](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/documentation-samples/tutorials/HumanResources-jobs-batch.json)。 

2. 在 JSON 格式的批次檔中，新增具有您想要在測試中預測之**意圖**的語句。 

   [!code-json[Add the intents to the batch test file](~/samples-luis/documentation-samples/tutorials/HumanResources-jobs-batch.json "Add the intents to the batch test file")]

## <a name="run-the-batch"></a>執行批次

1. 選取頂端導覽列中的 [Test] \(測試\)  。 

2. 選取右側面板中的 [Batch testing panel] \(批次測試面板\)  

    [![已醒目提示 [批次測試] 面板的 LUIS 應用程式螢幕擷取畫面](./media/luis-tutorial-batch-testing/hr-batch-testing-panel-link.png)](./media/luis-tutorial-batch-testing/hr-batch-testing-panel-link.png#lightbox)

3. 選取 [Import dataset] \(匯入資料集\)  。

    [![已醒目提示 [匯入資料集] 的 LUIS 應用程式螢幕擷取畫面](./media/luis-tutorial-batch-testing/hr-import-dataset-button.png)](./media/luis-tutorial-batch-testing/hr-import-dataset-button.png#lightbox)

4. 選擇 `HumanResources-jobs-batch.json` 檔案的檔案位置。

5. 將資料集命名為 `intents only`，然後選取 [完成]  。

    ![選取檔案](./media/luis-tutorial-batch-testing/hr-import-new-dataset-ddl.png)

6. 選取 [執行]  按鈕。 

7. 選取 [See results] \(查看結果\)  。

8. 檢閱圖表和圖例中的結果。

    [![含有批次測試結果的 LUIS 應用程式螢幕擷取畫面](./media/luis-tutorial-batch-testing/hr-intents-only-results-1.png)](./media/luis-tutorial-batch-testing/hr-intents-only-results-1.png#lightbox)

## <a name="review-batch-results"></a>檢閱批次結果

批次圖表會顯示四個象限的結果。 圖表的右邊是一個篩選條件。 篩選包含意圖和實體。 當您選取某個[圖表區段](luis-concept-batch-test.md#batch-test-results)或圖表內的某一點時，相關聯的語句即會顯示於圖表下方。 

將滑鼠停留在圖表上方時，滑鼠滾輪可以放大或縮小圖表中的顯示。 當圖表上有許多點緊密聚集在一起時，這非常有用。 

此圖表分成四個象限，其中兩個區段會以紅色顯示。 **這些是要關注的區段**。 

### <a name="getjobinformation-test-results"></a>GetJobInformation 測試結果

篩選條件中顯示的 **GetJobInformation** 測試結果顯示四個預測中有 2 個成功。 在左下方象限選取 [誤判]  名稱，以在圖表下方查看語句。 

使用鍵盤 (Ctrl + E) 切換至標籤檢視，以查看使用者語句的確切文字。 

語句 `Is there a database position open in Los Colinas?` 標示為 _GetJobInformation_，但目前的模型將語句預測為 _ApplyForJob_。 

適用於 **ApplyForJob** 的範例幾乎是 **GetJobInformation** 的三倍。 範例語句的不均等，使系統傾向預測為 **ApplyForJob** 意圖，因此造成錯誤預測。 

請注意，這兩個意圖具有相同的錯誤計數。 一個意圖中不正確的預測也會影響另一個意圖。 這兩者都有錯誤，因為針對一個意圖不正確地預測了語句，而且也不會針對另一個意圖進行不正確的預測。 

<a name="fix-the-app"></a>

## <a name="how-to-fix-the-app"></a>如何修正應用程式

本節目標是藉由修正應用程式，針對 **GetJobInformation** 正確預測所有語句。 

有個看似快速的修正是將這些批次檔語句新增到正確的意圖。 那不是您想要做的。 您想要 LUIS 正確預測這些語句，而不需將它們新增為範例。 

您可能也會想要從 **ApplyForJob** 移除語句，直到語句數量與 **GetJobInformation** 一樣為止。 那樣做可能會修正測試結果，但會妨礙 LUIS 下一次準確預測該意圖。 

修正方法是將更多語句新增到 **GetJobInformation**。 請記得改變語句長度、單字選擇和文字排列，同時仍以尋找工作資訊為目標意圖，而「不是」  應徵工作。

### <a name="add-more-utterances"></a>新增更多語句

1. 選取上方瀏覽面板中的 [測試]  按鈕來關閉批次測試面板。 

2. 從意圖清單中選取 [GetJobInformation]  。 

3. 新增更多會隨著長度、選字及字組排列而變動的語句，確定會包含 `resume`、`c.v.` 和 `apply` 等字詞：

    |適用於 **GetJobInformation** 意圖的範例語句|
    |--|
    |Does the new job in the warehouse for a stocker require that I apply with a resume? (我需要使用履歷表來申請倉庫中適合補貨員的新工作嗎？)|
    |Where are the roofing jobs today? (今天要在哪裡進行蓋屋頂的工作？)|
    |I heard there was a medical coding job that requires a resume. (我聽說有個需要履歷表的醫療編碼工作。)|
    |I would like a job helping college kids write their c.v.s. (我想要找一份可協助大學生撰寫其履歷表的工作。) |
    |Here is my resume, looking for a new post at the community college using computers. (這是我的履歷表，在社區大學中使用電腦尋找新職位。)|
    |What positions are available in child and home care? (有哪些關於孩童與居家照護的職位？)|
    |Is there an intern desk at the newspaper? (該報社有實習生編輯部嗎？)|
    |My C.v. shows I'm good at analyzing procurement, budgets, and lost money. (我在履歷表上表明我擅長分析採購、預算及財富上的損益。) Is there anything for this type of work? (有任何適合這種類型工作的職位嗎？)|
    |Where are the earth drilling jobs right now? (現在哪裡有地質鑽探的工作？)|
    |I've worked 8 years as an EMS driver. (我已經做了 8 年的 EMS 司機。) Any new jobs? (有任何新工作嗎？)|
    |New food handling jobs require application? (新的食物處理工作需要申請嗎？)|
    |How many new yard work jobs are available? (目前有多少個新的庭園工作職位？)|
    |Is there a new HR post for labor relations and negotiations? (有任何關於勞資關係和協商的新人力資源職位嗎？)|
    |I have a masters in library and archive management. (我具備有關圖書館與檔案管理的碩士學位。) Any new positions? (有任何新職務嗎？)|
    |Are there any babysitting jobs for 13 year olds in the city today? (今天在這個城市中有任何照顧 13 歲孩童的保姆工作嗎？)|

    不要在語句中標示 **Job** 實體。 教學課程的這一節只會將重點放在意圖預測。

4. 藉由在右上方瀏覽列中選取 [定型]  來將應用程式定型。

## <a name="verify-the-new-model"></a>確認新的模型

為了確認會正確預測批次測試中的語句，請重新執行批次測試。

1. 選取頂端導覽列中的 [Test] \(測試\)  。 如果批次結果仍處於開放狀態，請選取 [返回清單]  。  

1. 選取批次名稱右邊的省略符號 (***...***) 按鈕，然後選取 [執行]  。 等候批次測試完成。 請注意，[查看結果]  按鈕現在是綠色。 這表示整個批次已成功執行。

1. 選取 [See results] \(查看結果\)  。 這些意圖的意圖名稱左邊應該都會有綠色圖示。 

## <a name="create-batch-file-with-entities"></a>建立含有實體的批次檔 

為了在批次測試中確認實體，必須在批次 JSON 檔案中標示實體。 

適用於文字 ([語彙基元](luis-glossary.md#token)) 總計數的實體變化可能會影響預測品質。 若已將定型資料提供給含有已標示語句的意圖，請確定這類資料包括各種實體長度。 

第一次撰寫和測試批次檔時，最好從您熟悉的一些語句和實體以及您認為可能不會正確預測的一些語句和實體開始。 這可協助您快速專注於問題領域。 使用數個無法預測的不同作業名稱來測試 **GetJobInformation** 和 **ApplyForJob** 意圖之後，開發了這個批次測試檔來查看是否有任何包含 **Job** 實體之特定值的預測問題。 

測試語句中提供的 **Job** 實體值，通常是一或兩個文字，以及一些有更多文字的範例。 如果「您自己」  的人力資源應用程式的作業名稱通常會有許多文字，則應用程式中已使用 **Job** 實體標示的範例語句就無法正常運作。

1. 在文字編輯器 (例如 `HumanResources-entities-batch.json`VSCode[) 中建立 ](https://code.visualstudio.com/)，或[下載此項目](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/documentation-samples/tutorials/HumanResources-entities-batch.json)。

2. 在 JSON 格式的批次檔中，新增一個物件陣列，其中包含語句和您想要在測試中預測的**意圖**，以及語句中任何實體的位置。 由於實體會以語彙基元為基礎，因此，請確定會在字元上啟動和停止每個實體。 不要以空格開始或結束語句。 這會在批次檔匯入期間造成錯誤。  

   [!code-json[Add the intents and entities to the batch test file](~/samples-luis/documentation-samples/tutorials/HumanResources-entities-batch.json "Add the intents and entities to the batch test file")]


## <a name="run-the-batch-with-entities"></a>使用實體執行批次

1. 選取頂端導覽列中的 [Test] \(測試\)  。 

2. 選取右側面板中的 [Batch testing panel] \(批次測試面板\)  

3. 選取 [Import dataset] \(匯入資料集\)  。

4. 選擇 `HumanResources-entities-batch.json` 檔案的檔案系統位置。

5. 將資料集命名為 `entities`，然後選取 [完成]  。

6. 選取 [執行]  按鈕。 等候測試完成。

7. 選取 [See results] \(查看結果\)  。

## <a name="review-entity-batch-results"></a>檢閱實體批次結果

圖表隨即開啟，並顯示已正確預測的所有意圖。 在右側篩選條件中向下捲動，以尋找有錯誤的實體預測。 

1. 在篩選條件中選取 [作業]  實體。

    ![篩選條件中的錯誤實體預測](./media/luis-tutorial-batch-testing/hr-entities-filter-errors.png)

    此圖表會變更以顯示實體預測。 

2. 在圖表的左下象限中，選取 [誤判]  。 然後，使用鍵盤組合 Ctrl + E 鍵來切換至語彙基元檢視。 

    [![實體預測的語彙基元檢視](./media/luis-tutorial-batch-testing/token-view-entities.png)](./media/luis-tutorial-batch-testing/token-view-entities.png#lightbox)
    
    檢閱圖表下方的語句，會在 Job 名稱包含 `SQL` 時顯示一致的錯誤。 檢閱範例語句和 Job 片語清單時，只會使用 SQL 一次，而且只會作為較長作業名稱 `sql/oracle database administrator` 的一部分。

## <a name="fix-the-app-based-on-entity-batch-results"></a>根據實體批次結果修正應用程式

修正應用程式，需要 LUIS 正確判斷 SQL 作業的變化。 您有數個適用於該修正的選項。 

* 明確地新增更多範例語句，它會使用 SQL 並將那些文字標示為 Job 實體。 
* 明確地將更多 SQL 作業新增到片語清單

這些工作會留給您來做。

在正確預測實體之前新增[模式](luis-concept-patterns.md)，將不會修正此問題。 這是因為在偵測到模式中的所有實體之前，模式將不會比對。 

## <a name="clean-up-resources"></a>清除資源

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="next-steps"></a>後續步驟

本教學課程已使用批次測試來尋找目前模型的問題。 模型已修正，並使用批次檔進行重新測試，以確認變更正確。

> [!div class="nextstepaction"]
> [了解模式](luis-tutorial-pattern.md)

