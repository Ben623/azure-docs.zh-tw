---
title: 快速入門：使用 Node.js 來呼叫文字分析 API
titleSuffix: Azure Cognitive Services
description: 取得資訊和程式碼範例，以協助您快速開始使文字分析 API。
services: cognitive-services
author: raymondl
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: quickstart
ms.date: 06/11/2019
ms.author: shthowse
ms.openlocfilehash: 7e43d53c0916cf7fdc684c9e044e632015662c3b
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "67068994"
---
# <a name="quickstart-using-nodejs-to-call-the-text-analytics-cognitive-service"></a>快速入門：使用 Node.js 來呼叫文字分析認知服務
<a name="HOLTop"></a>

透過此快速入門，開始使用適用於 Node.js 的文字分析 SDK 來分析語言。 雖然 [Text Analytics](//go.microsoft.com/fwlink/?LinkID=759711) REST API 與大部分程式設計語言相容，但 SDK 會提供簡單的方法，將服務整合到您的應用程式。 此範例的原始程式碼可以在 [GitHub](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples/blob/master/Samples/textAnalytics.js) 上找到。

如需 API 的技術文件，請參閱 [API 定義](//go.microsoft.com/fwlink/?LinkID=759346)。

## <a name="prerequisites"></a>必要條件

* [Node.js](https://nodejs.org/)
* [適用於 Node.js 的文字分析 SDK](https://www.npmjs.com/package/azure-cognitiveservices-textanalytics) 您可以使用下列內容安裝 SDK：

    `npm install azure-cognitiveservices-textanalytics`

[!INCLUDE [cognitive-services-text-analytics-signup-requirements](../../../../includes/cognitive-services-text-analytics-signup-requirements.md)]

您也必須具備註冊時產生的[端點和存取金鑰](../How-tos/text-analytics-how-to-access-key.md)。

## <a name="create-a-nodejs-application-and-install-the-sdk"></a>建立 Node.js 應用程式並安裝 SDK

安裝 Node.js 之後，建立 Node 專案。 為您的應用程式建立新目錄，並瀏覽至其目錄。

```mkdir myapp && cd myapp```

執行 ```npm init``` 來建立具有 package.json 檔案的 Node 應用程式。 安裝 `ms-rest-azure` 和 `azure-cognitiveservices-textanalytics` NPM 套件：

```npm install azure-cognitiveservices-textanalytics ms-rest-azure```

您應用程式的 package.json 檔案將會隨著相依項目更新。

## <a name="authenticate-your-credentials"></a>驗證您的認證

在專案根目錄中建立新的檔案 `index.js`並匯入已安裝的程式庫

```javascript
const CognitiveServicesCredentials = require("ms-rest-azure").CognitiveServicesCredentials;
const TextAnalyticsAPIClient = require("azure-cognitiveservices-textanalytics");
```

建立文字分析訂用帳戶金鑰的變數。

```javascript
let credentials = new CognitiveServicesCredentials(
    "enter-your-key-here"
);
```

> [!Tip]
> 為了在生產系統中安全部署祕密，建議使用 [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/quick-create-net)。
>

## <a name="create-a-text-analytics-client"></a>建立文字分析用戶端

以 `credentials` 作為參數來建立新的 `TextAnalyticsClient` 物件。 針對您的文字分析訂用帳戶，使用正確的 Azure 區域。

```javascript
//Replace 'westus' with the correct region for your Text Analytics subscription
let client = new TextAnalyticsAPIClient(
    credentials,
    "https://westus.api.cognitive.microsoft.com/"
);
```

## <a name="sentiment-analysis"></a>情感分析

建立物件清單，其中包含您要分析的文件。 對 API 的酬載是由 `documents` 清單所組成，其中包含 `id`、`language` 和 `text` 屬性。 `text` 屬性會儲存要分析的文字，`language` 是文件語言，而 `id` 可以是任何值。 

```javascript
const inputDocuments = {documents:[
    {language:"en", id:"1", text:"I had the best day of my life."},
    {language:"en", id:"2", text:"This was a waste of my time. The speaker put me to sleep."},
    {language:"es", id:"3", text:"No tengo dinero ni nada que dar..."},
    {language:"it", id:"4", text:"L'hotel veneziano era meraviglioso. È un bellissimo pezzo di architettura."}
]}
```

呼叫 `client.sentiment` 並取得結果。 接著逐一查看結果，並列印每個文件的識別碼和人氣分數。 接近 0 的分數表示負面人氣，而接近 1 的分數則表示正面人氣。

```javascript
const operation = client.sentiment({multiLanguageBatchInput: inputDocuments})
operation
.then(result => {
    console.log(result.documents);
})
.catch(err => {
    throw err;
});
```

在主控台視窗中透過 `node index.js` 執行您的程式碼。

### <a name="output"></a>輸出

```console
[ { id: '1', score: 0.8723785877227783 },
  { id: '2', score: 0.1059873104095459 },
  { id: '3', score: 0.43635445833206177 },
  { id: '4', score: 1 } ]
```

## <a name="language-detection"></a>語言偵測

建立物件清單，其中包含您的文件。 對 API 的酬載是由 `documents` 清單所組成，其中包含 `id` 和 `text` 屬性。 `text` 屬性會儲存要分析的文字，而 `id` 可以是任何值。

```javascript
// The documents to be submitted for language detection. The ID can be any value.
const inputDocuments = {
    documents: [
        { id: "1", text: "This is a document written in English." },
        { id: "2", text: "Este es un document escrito en Español." },
        { id: "3", text: "这是一个用中文写的文件" }
    ]
    };
```

呼叫 `client.detectLanguage()` 並取得結果。 接著逐一查看結果，並列印每個文件的識別碼以及第一個傳回的語言。

```javascript
const operation = client.detectLanguage({
    languageBatchInput: inputDocuments
});
operation
    .then(result => {
    result.documents.forEach(document => {
        console.log(`ID: ${document.id}`);
        document.detectedLanguages.forEach(language =>
        console.log(`\tLanguage: ${language.name}`)
        );
    });
    })
    .catch(err => {
    throw err;
    });
```

在主控台視窗中透過 `node index.js` 執行您的程式碼。

### <a name="output"></a>輸出

```console
===== LANGUAGE EXTRACTION ======
ID: 1 Language English
ID: 2 Language Spanish
ID: 3 Language Chinese_Simplified
```

## <a name="entity-recognition"></a>實體辨識

建立物件清單，其中包含您的文件。 對 API 的酬載是由 `documents` 清單所組成，其中包含 `id`、`language` 和 `text` 屬性。 `text` 屬性會儲存要分析的文字，`language` 是文件語言，而 `id` 可以是任何值。

```javascript

    const inputDocuments = {documents:[
        {language:"en", id:"1", text:"Microsoft was founded by Bill Gates and Paul Allen on April 4, 1975, to develop and sell BASIC interpreters for the Altair 8800"},
        {language:"es", id:"2", text:"La sede principal de Microsoft se encuentra en la ciudad de Redmond, a 21 kilómetros de Seattle."},
        ]}

}
```

呼叫 `client.entities()` 並取得結果。 接著逐一查看結果，並列印每個文件的識別碼。 對於每個偵測到的實體，列印其維基百科名稱、類型和子類型 (如果存在) 以及原始文字中的位置。

```javascript
const operation = client.entities({
    multiLanguageBatchInput: inputDocuments
});
operation
    .then(result => {
    result.documents.forEach(document => {
        console.log(`Document ID: ${document.id}`)
        document.entities.forEach(e =>{
        console.log(`\tName: ${e.name} Type: ${e.type} Sub Type: ${e.type}`)
        e.matches.forEach(match => (
            console.log(`\t\tOffset: ${match.offset} Length: ${match.length} Score: ${match.entityTypeScore}`)
        ))
        })
    });
    })
    .catch(err => {
    throw err;
    });
```

在主控台視窗中透過 `node index.js` 執行您的程式碼。

### <a name="output"></a>輸出

```console
Document ID: 1
    Name: Microsoft Type: Organization Sub Type: Organization
            Offset: 0 Length: 9 Score: 1
    Name: Bill Gates Type: Person Sub Type: Person
            Offset: 25 Length: 10 Score: 0.999786376953125
    Name: Paul Allen Type: Person Sub Type: Person
            Offset: 40 Length: 10 Score: 0.9988105297088623
    Name: April 4 Type: Other Sub Type: Other
            Offset: 54 Length: 7 Score: 0.8
    Name: April 4, 1975 Type: DateTime Sub Type: DateTime
            Offset: 54 Length: 13 Score: 0.8
    Name: BASIC Type: Other Sub Type: Other
            Offset: 89 Length: 5 Score: 0.8
    Name: Altair 8800 Type: Other Sub Type: Other
            Offset: 116 Length: 11 Score: 0.8
Document ID: 2
    Name: Microsoft Type: Organization Sub Type: Organization
            Offset: 21 Length: 9 Score: 0.999755859375
    Name: Redmond (Washington) Type: Location Sub Type: Location
            Offset: 60 Length: 7 Score: 0.9911284446716309
    Name: 21 kilómetros Type: Quantity Sub Type: Quantity
            Offset: 71 Length: 13 Score: 0.8
    Name: Seattle Type: Location Sub Type: Location
            Offset: 88 Length: 7 Score: 0.9998779296875
```

## <a name="key-phrase-extraction"></a>關鍵片語擷取

建立物件清單，其中包含您的文件。 對 API 的酬載是由 `documents` 清單所組成，其中包含 `id`、`language` 和 `text` 屬性。 `text` 屬性會儲存要分析的文字，`language` 是文件語言，而 `id` 可以是任何值。

```javascript
    let inputLanguage = {
    documents: [
        {language:"ja", id:"1", text:"猫は幸せ"},
        {language:"de", id:"2", text:"Fahrt nach Stuttgart und dann zum Hotel zu Fu."},
        {language:"en", id:"3", text:"My cat might need to see a veterinarian."},
        {language:"es", id:"4", text:"A mi me encanta el fútbol!"}
    ]
    };
```

呼叫 `client.keyPhrases()` 並取得結果。 然後逐一查看結果，並列印每個文件的識別碼以及偵測到的任何主要片語。

```javascript
    let operation = client.keyPhrases({
    multiLanguageBatchInput: inputLanguage
    });
    operation
    .then(result => {
        console.log(result.documents);
    })
    .catch(err => {
        throw err;
    });
```

在主控台視窗中透過 `node index.js` 執行您的程式碼。

### <a name="output"></a>輸出

```console
[ 
    { id: '1', keyPhrases: [ '幸せ' ] },
    { id: '2', keyPhrases: [ 'Stuttgart', 'Hotel', 'Fahrt', 'Fu' ] },
    { id: '3', keyPhrases: [ 'cat', 'veterinarian' ] },
    { id: '4', keyPhrases: [ 'fútbol' ] } 
]
```

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [文字分析與 Power BI](../tutorials/tutorial-power-bi-key-phrases.md)

## <a name="see-also"></a>另請參閱

 [文字分析概觀](../overview.md)[常見問題集 (FAQ)](../text-analytics-resource-faq.md)
