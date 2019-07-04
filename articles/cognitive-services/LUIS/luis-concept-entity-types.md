---
title: 實體類型
titleSuffix: Language Understanding - Azure Cognitive Services
description: 實體會擷取 [utterance] 中的資料。 實體類型可讓您可預測資料擷取。 有兩種類型的實體： 機器學習和非機器學習。 請務必知道您正在使用中的發音的實體的類型。
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 06/12/2019
ms.author: diberry
ms.openlocfilehash: 628a96c4e912341226d67a7ed8f241194e7b7825
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "67080045"
---
# <a name="entity-types-and-their-purposes-in-luis"></a>實體類型和其在 LUIS 中的目的

實體會擷取 [utterance] 中的資料。 實體類型可讓您可預測資料擷取。 有兩種類型的實體： 機器學習和非機器學習。 請務必知道您正在使用中的發音的實體的類型。 

## <a name="entity-compared-to-intent"></a>實體與意圖的比較

實體代表語句中您想要擷取的單字或片語。 一個語句可以包含許多實體，也可以完全不包含實體。 用戶端應用程式可能需要執行其工作，或使用它做為指南的數個選項來呈現給使用者的實體。 

實體：

* 表示類別，包括類似物件 （位置、 項目、 人員、 事件或概念） 的集合。 
* 描述與意圖相關的資訊


例如，「新聞搜尋」應用程式可能包含「主題」、「來源」、「關鍵字」及「發佈日期」等實體，這些都是搜尋新聞的關鍵資料。 在旅遊預訂應用程式中，「地點」、「日期」、「航空公司」、「艙等」及「機票」則是航班預訂 (與「預訂航班」意圖相關) 的關鍵資訊。

相較之下，意圖代表了整個語句的預測。 

## <a name="entities-help-with-data-extraction-only"></a>實體僅有助於資料擷取

在實體上加上標籤或標記只能用於實體擷取，無助於意圖預測。

## <a name="entities-represent-data"></a>實體代表資料

實體是您想要從語句中提取的資料。 這可以是名稱、日期、產品名稱或任何單字的群組。 

|語句|實體|資料|
|--|--|--|
|購買 3 張到紐約的機票|預先建置的號碼<br>Location.Destination|3<br>紐約|
|購買 1 張 3 月 5 日從紐約到倫敦的機票|Location.Origin<br>Location.Destination<br>預先建置的 datetimeV2|紐約<br>倫敦<br>2018 年 3 月 5 日|

## <a name="entities-are-optional-but-highly-recommended"></a>實體是選擇性的，但強烈建議使用

意圖是必要的，實體則為選擇性。 您不需要為應用程式中的每個概念建立實體，只需針對用戶端應用程式執行動作所需的部分建立實體即可。 

如果您的語句中沒有 Bot 繼續工作所需的詳細資料，就無須新增這些資料。 您可以隨著應用程式趨於成熟，稍後再新增這些資料。 

如果您不確定會如何使用該資訊，可以新增一些常見的預先建置實體，例如 [datetimeV2](luis-reference-prebuilt-datetimev2.md)、[序數](luis-reference-prebuilt-ordinal.md)、[電子郵件](luis-reference-prebuilt-email.md)及[電話號碼](luis-reference-prebuilt-phonenumber.md)。

## <a name="label-for-word-meaning"></a>單字意義的標籤

如果單字選擇或單字排列相同，但意義不同，請勿以實體標記它。 

下列語句中的 `fair` 一字是一個同形異義字。 其拼字相同，但意義不同：

|語句|
|--|
|What kind of county fairs are happening in the Seattle area this summer?|
|Is the current rating for the Seattle review fair?|

如果您想要讓事件實體尋找所有事件資料，請標記第一個語句中的 `fair` 一字，但不要標記第二個語句中的該字。

## <a name="entities-are-shared-across-intents"></a>實體會跨意圖共用

實體會在意圖間共用。 它們不屬於任何單一意圖。 意圖和實體可以在語意上相關，但並不具有獨佔關聯性。

在「為我預訂到巴黎的機票」語句中，「巴黎」是代表地點的實體。 LUIS 可透過辨識使用者語句中提到的實體，協助用戶端應用程式選擇要執行以滿足使用者要求的特定動作。

## <a name="mark-entities-in-none-intent"></a>標記 None 意圖中的實體

如果可能，所有意圖 (包括 **None** 意圖) 都應有標記的實體。 這可協助 LUIS 進一步了解實體在語句中的哪裡，以及實體周圍有哪些單字。 

## <a name="entity-status-for-predictions"></a>用於預測的實體狀態

LUIS 入口網站會在範例語句中的實體與標記實體不同時，或太靠近另一個實體以至於不清楚時，讓您知道有此狀況。 這會在範例語句中以紅色底線表示。 

如需詳細資訊，請參閱[實體狀態預測](luis-how-to-add-example-utterances.md#entity-status-predictions)。 

## <a name="types-of-entities"></a>實體類型

LUIS 會提供許多類型的實體。 您可以根據擷取資料的方式和資料在擷取之後的呈現方式，來選擇實體。

實體可以透過機器學習來擷取，如此可讓 LUIS 繼續了解實體在語句中出現的方式。 實體也可不使用機器學習來進行擷取，而是比對確切文字或規則運算式。 模式中的實體可以透過混合式實作來擷取。 

擷取實體後，實體資料就可以表示為單一的資訊單位，或與其他實體結合，以形成用戶端應用程式可使用的資訊單位。

|機器學習|可標記|教學課程|範例<br>Response|實體類型|目的|
|--|--|--|--|--|--|
|✔|✔|[✔](luis-tutorial-composite-entity.md)|[✔](luis-concept-data-extraction.md#composite-entity-data)|[**複合**](#composite-entity)|實體群組，無論何種實體類型。|
|||[✔](luis-quickstart-intent-and-list-entity.md)|[✔](luis-concept-data-extraction.md#list-entity-data)|[**清單**](#list-entity)|透過比對確切文字來擷取的項目和其同義字清單。|
|混合||[✔](luis-tutorial-pattern.md)|[✔](luis-concept-data-extraction.md#patternany-entity-data)|[**Pattern.any**](#patternany-entity)|難以判斷實體結尾的實體。|
|||[✔](luis-tutorial-prebuilt-intents-entities.md)|[✔](luis-concept-data-extraction.md#prebuilt-entity-data)|[**預建**](#prebuilt-entity)|已訓練為擷取各種資料類型。|
|||[✔](luis-quickstart-intents-regex-entity.md)|[✔](luis-concept-data-extraction.md#regular-expression-entity-data)|[**規則運算式**](#regular-expression-entity)|要比對文字的使用者規則運算式。|
|✔|✔|[✔](luis-quickstart-primary-and-secondary-data.md)|[✔](luis-concept-data-extraction.md#simple-entity-data)|[**簡單**](#simple-entity)|在字組或片語中包含單一概念。|

範例談話中標示要唯一的機器學習的實體。 機器學習實體在透過[端點查詢](luis-concept-test.md#endpoint-testing)及[檢閱端點語句](luis-how-to-review-endoint-utt.md)來進行測試時，效果最佳。 

Pattern.any 實體需要在[模式](luis-how-to-model-intent-pattern.md)範本裡的範例 (而不是意圖裡使用者所提供的範例) 中進行標記。 

混合實體會使用實體偵測方法的組合。

## <a name="machine-learned-entities-use-context"></a>機器學習的實體使用內容

在 [utterance] 的內容中，了解機器學習的實體。 如此一來放置的變化中範例的發音顯著。 

## <a name="non-machine-learned-entities-dont-use-context"></a>非機器學習的實體不使用內容

下列非機器已了解實體不要考慮 [utterance] 內容比對實體時： 

* [預先建置實體](#prebuilt-entity)
* [Regex 實體](#regular-expression-entity)
* [清單實體](#list-entity) 

這些實體不需要標記，或定型模型。 一旦您加入或設定實體時，會擷取實體。 缺點是這些實體可以 overmatched，其中內容已納入考量，如果相符項目會有尚未進行。 

這個清單的實體與新的模型上情況經常發生。 您建置和測試您的模型清單中的實體，但當您發行您的模型，並從端點收到查詢，您就會發現您的模型 overmatching 內容的不足。 

如果您想要比對單字或片語，並納入考量的內容，您會有兩個選項。 第一個是使用搭配片語清單的簡單實體。 片語清單不會用於比對，但改為將有助於訊號相對相似字組 （可互換的清單）。 如果您必須有完全相符而不是片語清單的變化，使用清單實體和角色，如下所述。

### <a name="context-with-non-machine-learned-entities"></a>使用非機器學習的實體的內容

如果您想要重要的非機器學習到實體 [utterance] 的內容時，您應該使用[角色](luis-concept-roles.md)。

如果您具有非機器學習的實體，例如[預先建置的實體](#prebuilt-entity)， [regex](#regular-expression-entity)實體或[清單](#list-entity)實體，這比對您想要的執行個體之外，請考慮使用兩個角色中建立一個實體。 一個角色會擷取您要尋找，和一個角色會擷取項目不想要。 範例談話中標示為需要兩個版本。  

## <a name="composite-entity"></a>複合實體

複合實體是組成其他實體，例如預先建置的實體，簡單的規則運算式和清單的實體。 個別實體會構成一個完整的提體。 

當資料有下列特性時，最適用此實體：

* 彼此相關。 
* 在語句的內容中彼此相關。
* 使用各種實體類型。
* 需要由用戶端應用程式當作一個資訊單位進行分組和處理。
* 有各種使用者語句需要使用機器學習。

![複合實體](./media/luis-concept-entities/composite-entity.png)

[教學課程](luis-tutorial-composite-entity.md)<br>
[實體的 JSON 回應範例](luis-concept-data-extraction.md#composite-entity-data)<br>

## <a name="list-entity"></a>清單實體

清單實體代表一組固定的封閉式相關字組及其同義字。 LUIS 並不會探索清單實體的額外值。 使用**建議**功能，以根據目前的清單查看適用於新字組的建議。 如果有多個清單實體具有相同的值，則在端點查詢中會傳回每個實體。 

當文字資料有下列特性時，最適用此實體：

* 是已知的組合。
* 不常變更。 如果您需要經常變更的清單，或想要自行展開的清單，促進式片語清單的簡單實體就會是較好的選擇。 
* 此組合不會超過此實體類型的最大 LUIS [界限](luis-boundaries.md)。
* 語句中的文字是與同義字或正式名稱完全相符的項目。 LUIS 不會將清單用於完全相符之文字項目以外的範圍。 模糊比對、 不區分大小寫、 詞幹分析、 複數和其他變化不會解析與實體清單中。 若要管理變化，請考慮使用[模式](luis-concept-patterns.md#syntax-to-mark-optional-text-in-a-template-utterance)並搭配選擇性的文字語法。

![清單實體](./media/luis-concept-entities/list-entity.png)

[教學課程](luis-quickstart-intent-and-list-entity.md)<br>
[實體的 JSON 回應範例](luis-concept-data-extraction.md#list-entity-data)

## <a name="patternany-entity"></a>Pattern.any 實體

Pattern.any 是僅用於模式範本語句的可變長度預留位置，用來標記實體開始及結束的位置。  

以下時機適用此實體：

* 實體的結尾可能會與語句的其餘文字混淆。 
[教學課程](luis-tutorial-pattern.md)<br>
[實體的 JSON 回應範例](luis-concept-data-extraction.md#patternany-entity-data)

**範例**  
如果用戶端應用程式要根據書名來搜尋書籍，pattern.any 會擷取完整的書名。 為搜尋此書而使用 pattern.any 的範本語句是 `Was {BookTitle} written by an American this year[?]`。 

下表中，每個資料列都有兩個版本的語句。 上層語句是 LUIS 一開始看到語句的樣子，其中書名的開始處和結尾處並不清楚。 下層語句是使用模式進行擷取後，LUIS 了解書名為何的樣子。 

|語句|
|--|
|Was The Man Who Mistook His Wife for a Hat and Other Clinical Tales written by an American this year?<br><br>Was **The Man Who Mistook His Wife for a Hat and Other Clinical Tales** written by an American this year?|
|Was Half Asleep in Frog Pajamas written by an American this year?<br><br>Was **Half Asleep in Frog Pajamas** written by an American this year?|
|Was The Particular Sadness of Lemon Cake:A Novel written by an American this year?<br><br>Was **The Particular Sadness of Lemon Cake:A Novel** written by an American this year?|
|Was There's A Wocket In My Pocket! written by an American this year?<br><br>Was **There's A Wocket In My Pocket!** written by an American this year?|
||

## <a name="prebuilt-entity"></a>預建實體

預建實體是代表一般概念 (例如電子郵件、URL 和電話號碼) 的內建類型。 預先建置的實體名稱為保留名稱。 已新增至應用程式的[所有預建實體](luis-prebuilt-entities.md)都會在端點預測查詢中傳回 (如果有在語句中找到的話)。 

以下時機適用此實體：

* 資料符合預建實體所支援的常見使用案例 (以您的語言文化為基礎)。 

可以隨時新增或移除預建實體。

![Number 預先建置的實體](./media/luis-concept-entities/number-entity.png)

[教學課程](luis-tutorial-prebuilt-intents-entities.md)<br>
[實體的 JSON 回應範例](luis-concept-data-extraction.md#prebuilt-entity-data)

在開放原始碼 [Recognizers-Text](https://github.com/Microsoft/Recognizers-Text) 專案中已定義部分這些預建實體。 如果目前不支援您的特定文化特性或實體，請向專案提出。 

### <a name="troubleshooting-prebuilt-entities"></a>疑難排解預先建置的實體

在 LUIS 入口網站中，如果預先建置的實體標記而不是您的自訂實體中，您可以如何修正此問題幾個的選擇。

預先建置的實體新增至應用程式將會_一律_傳回，即使 [utterance] 應該擷取相同的文字的自訂實體。 

#### <a name="change-tagged-entity-in-example-utterance"></a>變更標記的實體，在範例 [utterance]

如果預先建置的實體是相同的文字或語彙基元，為自訂實體時，選取範例 [utterance] 中的文字，並變更已加上標籤的 [utterance]。 

如果預先建置的實體會標記為更多的文字或語彙基元比您的自訂實體，您會有幾個選項如何修正此問題：

* [移除範例 [utterance]](#remove-example-utterance-to-fix-tagging)方法
* [移除預先建置的實體](#remove-prebuilt-entity-to-fix-tagging)方法

#### <a name="remove-example-utterance-to-fix-tagging"></a>移除範例 [utterance] 若要修正標記 

您的第一個選擇是要移除範例 [utterance]。 

1. 刪除範例 [utterance]。
1. 重新訓練應用程式。 
1. 新增回剛才的單字或片語也就是標示為預先建置的實體，以完整的範例 [utterance] 實體。 單字或片語仍會有預先建置的實體標記。 
1. 在 選取範例 utterance 中的實體**意圖**頁面上，而且變更至您的自訂實體，並重新定型。 這應該防止 LUIS 做為任何使用該文字的範例談話中預先建置的實體標示此的確切文字。 
1. 將整個原始範例 utterance 新增回意圖。 自訂實體應該繼續執行，而不是預先建置的實體標記。 如果未標示的自訂實體，您需要新增更多範例，該文字的談話中。

#### <a name="remove-prebuilt-entity-to-fix-tagging"></a>移除預先建置的實體，若要修正標記

1. 移除應用程式中的預先建置的實體。 
1. 在 **意圖**頁面上，將標記在範例 utterance 中的自訂實體。
1. 將應用程式定型。
1. 將預先建置的實體新增回應用程式，並且訓練應用程式。 此修正程式會假設預先建置的實體不是複合實體的一部分。

## <a name="regular-expression-entity"></a>規則運算式實體 

規則運算式最適用於未經處理的語句文字。 這會忽略大小寫並忽略文化特性變體。  在字元等級而非權杖等級的拼字檢查修改之後，才會套用規則運算式比對。 如果規則運算式太複雜 (例如使用許多方括號)，您便無法將運算式新增到模型中。 使用組件，但不是全部[.NET Regex](https://docs.microsoft.com/dotnet/standard/base-types/regular-expressions)程式庫。 

以下時機適用此實體：

* 以任何一致的變化來一致地格式化的資料。
* 規則運算式不需要 2 個層級以上的巢狀結構。 

![規則運算式實體](./media/luis-concept-entities/regex-entity.png)

[教學課程](luis-quickstart-intents-regex-entity.md)<br>
[實體的 JSON 回應範例](luis-concept-data-extraction.md#regular-expression-entity-data)<br>

可能比您預期符合更多符合規則運算式。 其中一個範例就是比對這類的數值字`one`和`two`。 例如，下列的 regex，用來比對的數字`one`以及其他數字：

```javascript
(plus )?(zero|one|two|three|four|five|six|seven|eight|nine)(\s+(zero|one|two|three|four|five|six|seven|eight|nine))*
``` 

這個 regex 運算式也會比對任何以這些數字，例如結尾的單字`phone`。 若要修正這類問題，請確定 regex 會比對會將帳戶的字詞界限。 此範例中使用的字詞界限 regex 會在下列的 regex:

```javascript
\b(plus )?(zero|one|two|three|four|five|six|seven|eight|nine)(\s+(zero|one|two|three|four|five|six|seven|eight|nine))*\b
```

## <a name="simple-entity"></a>簡單實體 

簡單實體是描述單一概念並從機器學習內容學習到的一般實體。 由於簡單實體通常是名稱，例如公司名稱、產品名稱或其他類別的名稱，因此，使用簡單實體時可加入[片語清單](luis-concept-feature.md)，以提升所用名稱所代表的信號。 

以下時機適用此實體：

* 格式不一致但表示相同內容的資料。 

![簡單實體](./media/luis-concept-entities/simple-entity.png)

[教學課程](luis-quickstart-primary-and-secondary-data.md)<br/>
[實體的範例回應](luis-concept-data-extraction.md#simple-entity-data)<br/>

## <a name="entity-limits"></a>實體限制

請檢閱[限制](luis-boundaries.md#model-boundaries)，以了解您可以將多少個每一種類型的實體新增到模型中。

## <a name="if-you-need-more-than-the-maximum-number-of-entities"></a>如果您所需的實體超出實體數目上限 

您可能需要使用實體的角色搭配複合的實體。

複合實體代表一個整體的多個部分。 例如，名為 PlaneTicketOrder 的複合實體可能會有 Airline、Destination、DepartureCity、DepartureDate 及 PlaneTicketClass 子實體。

LUIS 也提供非機器學習但可讓您的 LUIS 應用程式指定固定值清單的清單實體類型。 請參閱 [LUIS 界限](luis-boundaries.md)參考，以檢閱「清單」實體類型的限制。 

如果您已被視為這些實體，而且仍然需要超過此限制，請連絡支援服務。 若要這樣做，請收集關於您系統的詳細資訊、前往 [LUIS](luis-reference-regions.md#luis-website) 網站，然後選取 [支援]  。 如果您的 Azure 訂用帳戶包含支援服務，請與 [Azure 技術支援人員](https://azure.microsoft.com/support/options/)連絡。 

## <a name="next-steps"></a>後續步驟

了解良好[語句](luis-concept-utterance.md)的相關概念。 

請參閱[新增實體](luis-how-to-add-entities.md)，以深入了解如何將實體新增至 LUIS 應用程式。
