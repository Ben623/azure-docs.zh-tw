---
title: 文字翻譯認知技能（預覽）
titleSuffix: Azure Cognitive Search
description: 評估文字，並針對每一筆記錄，傳回在 Azure 認知搜尋的 AI 擴充管線中轉譯為指定目的語言的文字。 此技能目前為公開預覽狀態。
manager: nitinme
author: careyjmac
ms.author: chalton
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 7c42c9033fac057c12426726a96ae6079f3080da
ms.sourcegitcommit: bc7725874a1502aa4c069fc1804f1f249f4fa5f7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/07/2019
ms.locfileid: "73715409"
---
#   <a name="text-translation-cognitive-skill"></a>文字翻譯認知技能

> [!IMPORTANT] 
> 此技能目前為公開預覽狀態。 預覽功能會在沒有服務等級協定的情況下提供，不建議用於生產工作負載。 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。 [REST API 版本 2019-05-06-preview](search-api-preview.md)提供預覽功能。 目前有有限的入口網站支援，而且沒有 .NET SDK 支援。

**文字翻譯**技能會評估文字，而針對每筆記錄，會傳回轉譯為指定目的語言的文字。 這項技能使用認知服務中提供的[翻譯工具文字 API v3.0](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-translate) 。

如果您預期檔可能並非全部都使用一種語言，這項功能就很有用，在這種情況下，您可以將文字標準化為單一語言，然後再進行搜尋的索引。  這也適用于當地語系化使用案例，您可能會想要有多種語言的相同文字複本。

[翻譯工具文字 API v3.0](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference)是非區域認知服務，這表示您的資料不一定會與您的 Azure 認知搜尋或附加認知服務資源保持在相同的區域。

> [!NOTE]
> 當您透過增加處理頻率、新增更多文件或新增更多 AI 演算法來擴展範圍時，您必須[連結可計費的認知服務資源](cognitive-search-attach-cognitive-services.md)。 在認知服務中呼叫 Api 時，會產生費用，並在 Azure 認知搜尋中以檔破解階段的形式進行映射解壓縮。 從文件中擷取文字不會產生費用。
>
> 內建技能的執行會依現有的[認知服務預付型方案價格](https://azure.microsoft.com/pricing/details/cognitive-services/)收費。 [Azure 認知搜尋定價頁面](https://go.microsoft.com/fwlink/?linkid=2042400)會說明影像提取定價。

## <a name="odatatype"></a>@odata.type  
TranslationSkill。

## <a name="data-limits"></a>資料限制
記錄的大小上限應為50000個字元，如[`String.Length`](https://docs.microsoft.com/dotnet/api/system.string.length)所測量。 如果您需要在將資料傳送到文字翻譯技能之前先將其分解，請考慮使用[文字分割技能](cognitive-search-skill-textsplit.md)。

## <a name="skill-parameters"></a>技能參數

這些參數會區分大小寫。

| 輸入                | 說明 |
|---------------------|-------------|
| defaultToLanguageCode | 具備將檔翻譯成的語言代碼，適用于未明確指定為語言的檔。 <br/> 請參閱[支援語言的完整清單](https://docs.microsoft.com/azure/cognitive-services/translator/language-support)。 |
| defaultFromLanguageCode | 選擇性針對未明確指定 from 語言的檔，用來轉譯檔的語言代碼。  如果未指定 defaultFromLanguageCode，則會使用翻譯工具文字 API 所提供的自動語言偵測來判斷 from 語言。 <br/> 請參閱[支援語言的完整清單](https://docs.microsoft.com/azure/cognitive-services/translator/language-support)。 |
| suggestedFrom | 選擇性當未提供 fromLanguageCode 輸入或 defaultFromLanguageCode 參數，且自動語言偵測不成功時，用來轉譯檔的語言代碼。  如果未指定 suggestedFrom 語言，則會使用英文（en）作為 suggestedFrom 語言。 <br/> 請參閱[支援語言的完整清單](https://docs.microsoft.com/azure/cognitive-services/translator/language-support)。 |

## <a name="skill-inputs"></a>技能輸入

| 輸入名稱     | 說明 |
|--------------------|-------------|
| 文字 | 要轉譯的文字。|
| toLanguageCode    | 字串，表示文字應轉譯成的語言。 如果未指定此輸入，將會使用 defaultToLanguageCode 來轉譯文字。 <br/>請參閱[支援語言的完整清單](https://docs.microsoft.com/azure/cognitive-services/translator/language-support)|
| fromLanguageCode  | 字串，表示文字的目前語言。 如果未指定此參數，則會使用 defaultFromLanguageCode （如果未提供 defaultFromLanguageCode，則會自動偵測語言）將會用來轉譯文字。 <br/>請參閱[支援語言的完整清單](https://docs.microsoft.com/azure/cognitive-services/translator/language-support)|

## <a name="skill-outputs"></a>技能輸出

| 輸出名稱    | 說明 |
|--------------------|-------------|
| translatedText | 從 translatedFromLanguageCode 到 translatedToLanguageCode 之文字轉譯的字串結果。|
| translatedToLanguageCode  | 表示文字轉譯成之語言代碼的字串。 如果您要翻譯成多種語言，而且想要能夠追蹤哪些文字是哪種語言，就很有用。|
| translatedFromLanguageCode    | 表示文字轉譯來來源語言代碼的字串。 如果您選擇了自動語言偵測選項，此輸出將會提供該偵測的結果，這會很有用。|

##  <a name="sample-definition"></a>範例定義

```json
 {
    "@odata.type": "#Microsoft.Skills.Text.TranslationSkill",
    "defaultToLanguageCode": "fr",
    "suggestedFrom": "en",
    "context": "/document",
    "inputs": [
      {
        "name": "text",
        "source": "/document/text"
      }
    ],
    "outputs": [
      {
        "name": "translatedText",
        "targetName": "translatedText"
      },
      {
        "name": "translatedFromLanguageCode",
        "targetName": "translatedFromLanguageCode"
      },
      {
        "name": "translatedToLanguageCode",
        "targetName": "translatedToLanguageCode"
      }
    ]
  }
```

##  <a name="sample-input"></a>範例輸入

```json
{
  "values": [
    {
      "recordId": "1",
      "data":
        {
          "text": "We hold these truths to be self-evident, that all men are created equal."
        }
    },
    {
      "recordId": "2",
      "data":
        {
          "text": "Estamos muy felices de estar con ustedes."
        }
    }
  ]
}
```


##  <a name="sample-output"></a>範例輸出

```json
{
  "values": [
    {
      "recordId": "1",
      "data":
        {
          "translatedText": "Nous tenons ces vérités pour évidentes, que tous les hommes sont créés égaux.",
          "translatedFromLanguageCode": "en",
          "translatedToLanguageCode": "fr"
        }
    },
    {
      "recordId": "2",
      "data":
        {
          "translatedText": "Nous sommes très heureux d'être avec vous.",
          "translatedFromLanguageCode": "es",
          "translatedToLanguageCode": "fr"
        }
    }
  ]
}
```


## <a name="errors-and-warnings"></a>錯誤和警告
如果您為 from 或 to 語言提供不支援的語言代碼，則會產生錯誤，而且不會轉譯文字。
如果您的文字是空白的，則會產生警告。
如果您的文字大於50000個字元，則只會轉譯前50000個字元，併發出警告。

## <a name="see-also"></a>另請參閱

+ [內建技能](cognitive-search-predefined-skills.md)
+ [如何定義技能集](cognitive-search-defining-skillset.md) (英文)
