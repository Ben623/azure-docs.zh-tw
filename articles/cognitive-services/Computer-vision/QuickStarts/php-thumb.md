---
title: 快速入門：產生縮圖 - REST、PHP
titleSuffix: Azure Cognitive Services
description: 在本快速入門中，您將搭配使用電腦視覺 API 與 PHP，從影像產生縮圖。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: quickstart
ms.date: 07/03/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 69a7438f2daf1eb16efff7f0b4f503064afac29d
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/05/2019
ms.locfileid: "67603587"
---
# <a name="quickstart-generate-a-thumbnail-using-the-computer-vision-rest-api-and-php"></a>快速入門：使用電腦視覺 REST API 和 PHP 產生縮圖

在此快速入門中，您會使用電腦視覺的 REST API 從影像產生縮圖。 您可以使用[取得縮圖](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fb)方法產生影像的縮圖。 您可指定高度和寬度，可以與輸入影像的外觀比例不同。 「電腦視覺」會使用智慧型裁剪，以智慧方式識別關注區域，並根據該區域產生裁剪座標。

如果您沒有 Azure 訂用帳戶，請在開始前建立 [免費帳戶](https://azure.microsoft.com/free/ai/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=cognitive-services) 。

## <a name="prerequisites"></a>必要條件

- 您必須安裝 [PHP](https://secure.php.net/downloads.php)。
- 您必須安裝 [Pear](https://pear.php.net)。
- 您必須有電腦視覺的訂用帳戶金鑰。 您可以從[試用認知服務](https://azure.microsoft.com/try/cognitive-services/?api=computer-vision)取得免費試用的金鑰。 或者，依照[建立認知服務帳戶](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account)中的指示訂閱電腦視覺並取得金鑰。

## <a name="create-and-run-the-sample"></a>建立並執行範例

若要建立並執行範例，請執行下列步驟：

1. 安裝 PHP5 [`HTTP_Request2`](https://pear.php.net/package/HTTP_Request2) 套件。
   1. 以系統管理員身分開啟 [命令提示字元] 視窗。
   1. 執行以下命令：

      ```console
      pear install HTTP_Request2
      ```

   1. 成功安裝套件之後，關閉命令提示字元視窗。

1. 將下列程式碼複製到文字編輯器。
1. 視需要在程式碼中進行下列變更：
    1. 將 `subscriptionKey` 的值取代為您的訂用帳戶金鑰。
    1. 如有需要，請從您取得訂用帳戶金鑰的 Azure 區域，將 `uriBase` 的值取代為 [Get Thumbnail](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fb) 方法的端點 URL。
    1. (選擇性) 將 `imageUrl` 的值取代為要產生縮圖之不同影像的 URL。
1. 將程式碼儲存為副檔名為 `.php` 的檔案。 例如： `get-thumbnail.php` 。
1. 開啟具有 PHP 支援的瀏覽器視窗。
1. 將檔案拖放到瀏覽器視窗中。

```php
<html>
<head>
    <title>Get Thumbnail Sample</title>
</head>
<body>
<?php
// Replace <Subscription Key> with a valid subscription key.
$ocpApimSubscriptionKey = '<Subscription Key>';

// You must use the same location in your REST call as you used to obtain
// your subscription keys. For example, if you obtained your subscription keys
// from westus, replace "westcentralus" in the URL below with "westus".
$uriBase = 'https://westcentralus.api.cognitive.microsoft.com/vision/v2.0/';

$imageUrl =
    'https://upload.wikimedia.org/wikipedia/commons/9/94/Bloodhound_Puppy.jpg';

require_once 'HTTP/Request2.php';

$request = new Http_Request2($uriBase . 'generateThumbnail');
$url = $request->getUrl();

$headers = array(
    // Request headers
    'Content-Type' => 'application/json',
    'Ocp-Apim-Subscription-Key' => $ocpApimSubscriptionKey
);
$request->setHeader($headers);

$parameters = array(
    // Request parameters
    'width' => '100',  // Width of the thumbnail.
    'height' => '100', // Height of the thumbnail.
    'smartCropping' => 'true',
);
$url->setQueryVariables($parameters);

$request->setMethod(HTTP_Request2::METHOD_POST);

// Request body parameters
$body = json_encode(array('url' => $imageUrl));

// Request body
$request->setBody($body);

try
{
    $response = $request->send();
    echo "<pre>" .
        json_encode(json_decode($response->getBody()), JSON_PRETTY_PRINT) . "</pre>";
}
catch (HttpException $ex)
{
    echo "<pre>" . $ex . "</pre>";
}
?>
</body>
</html>
```

## <a name="examine-the-response"></a>檢查回應

成功的回應會以二進位資料的形式傳回，代表縮圖的影像資料。 如果要求失敗，則回應會顯示在瀏覽器視窗中。 如果要求失敗，回應會包含錯誤碼和訊息以協助判斷問題所在。

## <a name="clean-up-resources"></a>清除資源

不再需要檔案時請將它刪除，然後再解除安裝 PHP5 `HTTP_Request2` 套件。 若要解除安裝套件，請執行下列步驟：

1. 以系統管理員身分開啟 [命令提示字元] 視窗。
2. 執行以下命令：

   ```console
   pear uninstall HTTP_Request2
   ```

3. 將套件成功解除安裝之後，關閉命令提示字元視窗。

## <a name="next-steps"></a>後續步驟

探索可分析影像、偵測名人與地標、建立縮圖，以及擷取印刷與手寫文字的電腦視覺 API。 若要快速地試驗電腦視覺 API，請嘗試 [Open API 測試主控台](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa/console)。

> [!div class="nextstepaction"]
> [探索電腦視覺 API](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44)
