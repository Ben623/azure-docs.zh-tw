---
title: 新增範例語句
titleSuffix: Language Understanding - Azure Cognitive Services
description: 範例語句是使用者問題或命令的文字範例。 若要教導 Language Understanding (LUIS)，您需要將範例語句新增至意圖。
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 04/01/2019
ms.author: diberry
ms.openlocfilehash: af43697dc864019c6fd86b041e231a54d8aa8b40
ms.sourcegitcommit: 441e59b8657a1eb1538c848b9b78c2e9e1b6cfd5
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/11/2019
ms.locfileid: "67827482"
---
# <a name="add-an-entity-to-example-utterances"></a>將實體新增至範例語句 

範例語句是使用者問題或命令的文字範例。 若要教導 Language Understanding (LUIS)，您需要將[範例語句](luis-concept-utterance.md)新增至[意圖](luis-concept-intent.md)。

通常, 您會先將範例語句新增至意圖, 然後在 [**意圖**] 頁面上建立實體和標籤語句。 若您要先建立實體，請參閱[新增實體](luis-how-to-add-entities.md)。

## <a name="marking-entities-in-example-utterances"></a>在範例語句中標記實體

當您在範例語句中選取文字以標記為實體時，將會就地出現快顯功能表。 使用此功能表來建立或選取實體。 

某些實體類型 (例如預先建置的實體和規則運算式實體) 並無法在範例語句中標記，因為系統會自動標記它們。 

## <a name="add-a-simple-entity"></a>新增簡單實體

在下列程式中, 您會在 [**意圖**] 頁面的下列語句中建立及標記自訂實體:

```text
Are there any SQL server jobs?
```

1. 在語句中選取 `SQL server`，來將它標示為簡單實體。 在出現的實體下拉式清單方塊中，您可以選取現有實體，或新增實體。 若要加入新的實體, 請在`Job`文字方塊中輸入其名稱, 然後選取 [**建立新實體**]。

    ![輸入實體名稱的螢幕擷取畫面](./media/luis-how-to-add-example-utterances/create-simple-entity.png)

    > [!NOTE]
    > 選取單字以將其標記為實體時：
    > * 對於單一字組，只需要選取它。 
    > * 針對一組兩個或多個單字, 選取第一個單字, 然後選取最後一個單字。

1. 在 [What type of entity do you want to create?]  \(您要建立哪種類型的實體?\) 快顯方塊中驗證實體名稱，並選取 [Simple]  \(簡單\) 實體類型，然後選取 [Done]  \(完成\)。

    [片語清單](luis-concept-feature.md)常被用來提升簡單實體的訊號。

## <a name="add-a-list-entity"></a>新增清單實體

清單實體代表您系統中相關單字的一組完全相符的文字。 

針對公司的部門清單，您可能會有標準化的值：`Accounting` 和 `Human Resources`。 每個標準化名稱都會有同義字。 針對部門，這些同義字可能包含任何部門首字母縮略字、數字或俚語。 當您建立實體時，不需要知道所有值。 檢閱包含同義字的實際使用者語句之後，即可新增其他語句。

1. 在 [**意圖**] 頁面上的範例語句中, 選取您想要在新清單中的單字或片語。 當 [實體] 下拉式清單出現時, 在頂端的文字方塊中輸入新清單實體的名稱, 然後選取 [**建立新實體**]。   

1. 在 [What type of entity do you want to create?]  \(您要建立哪種類型的實體?\) 快顯方塊中為實體命名，並選取 [List]  \(清單\) 作為類型。 新增此清單項目的同義字，然後選取 [Done]  \(完成\)。 

    ![輸入清單實體同義字的螢幕擷取畫面](./media/luis-how-to-add-example-utterances/hr-create-list-2.png)

    您可以透過標示其他語句，或從左導覽的 [Entities]  \(實體\) 中編輯實體，來新增更多清單項目或項目同義字。 [編輯](luis-how-to-add-entities.md#add-list-entities)實體可讓您選擇輸入具有對應同義字的其他項目，或匯入清單。 

## <a name="add-a-composite-entity"></a>新增複合實體

複合實體會從現有的**實體**建立, 以形成父實體。 

假設語句, `Does John Smith work in Seattle?`複合陳述式可以傳回員工名稱`John Smith`的實體資訊, 以及複合實體中的位置`Seattle` 。 子實體必須已經存在於應用程式中, 並在範例語句中標示為在建立複合實體之前。

1. 若要將子實體包裝到複合實體中, 請選取複合實體語句中的**第一個**標記實體 (最左邊)。 下拉式清單隨即出現, 以顯示此選取專案的選項。

1. 從下拉式清單中選取 [**換行複合實體**]。 

1. 選取複合實體的最後一個單字 (最右邊)。 注意到有一條綠色線條會跟隨複合實體。 這是複合實體的視覺指標, 而且應該在最左邊子實體到最右邊子實體的複合實體中的所有字之下。

1. 在下拉式清單中輸入複合實體名稱。

    當您正確地包裝實體時，整個片語下方會有綠色線條。

1. 在 [What type of entity do you want to create?]  \(您要建立哪種類型的實體?\) 快顯方塊中驗證複合實體的詳細資料，然後選取 [Done]  \(完成\)。

    ![實體詳細資料快顯方塊的螢幕擷取畫面](./media/luis-how-to-add-example-utterances/hr-create-composite-3.png)

1. 複合實體會同時顯示針對個別實體的藍色醒目提示，以及針對整個複合實體的綠色底線。 

    ![意圖詳細資料頁面的螢幕擷取畫面，其中具有複合實體](./media/luis-how-to-add-example-utterances/hr-create-composite-4.png)

## <a name="add-entitys-role-to-utterance"></a>將實體的角色新增至語句

角色是實體的命名子型別, 由語句的內容所決定。 您可以將語句內的實體標示為實體, 或選取該實體內的角色。 任何實體都可以有角色, 包括機器學習的自訂實體 (簡單實體和複合實體), 而不是機器學習 (預先建立的實體、正則運算式實體、清單實體)。 

瞭解如何從實際操作教學課程中,[以實體角色標記語句](tutorial-entity-roles.md)。 

## <a name="entity-status-predictions"></a>實體狀態預測

當您在 LUIS 入口網站中輸入新的語句時，語句可能有實體預測誤差。 預測誤差是如何標示實體和 LUIS 如何預測實體之間的差異。 

語句中的差異會顯示在 LUIS 入口網站中，以紅色底線標示。 紅色底線可能會出現在實體括號內或括號外部。 

![實體狀態預測差異的螢幕擷取畫面](./media/luis-how-to-add-example-utterances/entity-prediction-error.png)

選取語句中以紅色底線標示的字組。 

如果有預測差異，實體方塊會顯示具有紅色驚嘆號的 [實體狀態]  。 若要查看實體狀態和有關標示實體和預測實體之間的差異資訊，請選取 [實體狀態]  ，然後選取右邊的項目。

![實體狀態選取專案的螢幕擷取畫面](./media/luis-how-to-add-example-utterances/entity-prediction-error-correction.png)

可能出現紅線的時機如下：

   * 在實體未標示時輸入語句
   * 套用實體標籤時
   * 移除實體標籤時
   * 對該文字預測多個實體標籤時 

下列解決方案可協助您解決實體預測差異：

|實體|視覺指標|預測|方案|
|--|--|--|--|
|已輸入語句，但實體尚未標示。|紅色底線|預測是正確的。|以預測值標示實體。|
|未標示的文字|紅色底線|不正確的預測|目前語句使用了這個不正確的實體，必須透過所有意圖進行檢閱。 目前語句已使 LUIS 誤以為此文字是預測實體。
|已正確標示的文字|以藍色醒目提示實體，有紅色底線|不正確的預測|以各種位置和用法提供更多實體標示正確的語句。 目前語句不足以教導 LUIS 此實體是 (或類似) 相同內容中出現的實體。 類似的實體應該合併成單一實體，才不會讓 LUIS 混淆。 另一個解決方案是新增片語清單，以大幅提升字組的精確度。 |
|未正確標示的文字|以藍色醒目提示實體，有紅色底線|正確的預測| 以各種位置和用法提供更多實體標示正確的語句。 

## <a name="other-actions"></a>其他動作

您能以選取群組或個別項目的形式對範例語句執行動作。 選取範例語句群組會變更清單上的內容功能表。 單一項目可以使用清單上的內容功能表，也可以使用位於每個語句列末端的個別內容相關省略符號。 

### <a name="remove-entity-labels-from-utterances"></a>從語句中移除實體標籤

您可以從 [意圖] 頁面上的語句中移除機器學習的實體標籤。 若實體不是機器學習實體，則無法從語句中予以移除。 若您需要從語句中移除非機器學習實體，則需要刪除整個應用程式中的實體。 

若要從語句中移除機器學習實體標籤，請選取語句中的實體。 然後選取所出現實體下拉式方塊中的 [移除標籤]  。

### <a name="add-a-prebuilt-entity-label"></a>新增預先建立的實體標籤

當您您將預先建置的實體新增至 LUIS 應用程式時，您並不需要標記具有這些實體的語句。 若要深入了解預先建置的實體和其新增方式，請參閱[新增實體](luis-how-to-add-entities.md#add-a-prebuilt-entity-to-your-app)。

### <a name="add-a-regular-expression-entity-label"></a>新增正則運算式實體標籤

若您將規則運算式實體新增至 LUIS 應用程式，則不需要標記具有這些實體的語句。 若要深入了解規則運算式實體和其新增方式，請參閱[新增實體](luis-how-to-add-entities.md#add-regular-expression-entities-for-highly-structured-concepts)。


### <a name="create-a-pattern-from-an-utterance"></a>從語句建立模式

請參閱[在意圖或實體頁面上從現有的語句新增模式](luis-how-to-model-intent-pattern.md#add-pattern-from-existing-utterance-on-intent-or-entity-page)。


### <a name="add-a-patternany-entity"></a>新增模式。任何實體

若您將 pattern.any 實體新增至 LUIS 應用程式，則無法標示具有這些實體的語句。 它們只有在模式中有效。 若要深入了解 pattern.any 實體和其新增方式，請參閱[新增實體](luis-how-to-add-entities.md#add-patternany-entities-to-capture-free-form-entities)。

## <a name="train-your-app-after-changing-model-with-utterances"></a>在利用語句變更模型後訓練您的應用程式

在您新增、編輯或移除語句之後，[訓練](luis-how-to-train.md)並[發佈](luis-how-to-publish-app.md)您的應用程式，您的變更才會影響端點查詢。 

## <a name="next-steps"></a>後續步驟

在您的**意圖**中標記語句之後, 您現在可以建立[複合實體](luis-how-to-add-entities.md)。
