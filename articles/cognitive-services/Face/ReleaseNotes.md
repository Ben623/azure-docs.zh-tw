---
title: 版本資訊 - 臉部 API 服務
titleSuffix: Azure Cognitive Services
description: 臉部 API 服務的版本資訊包括各種版本的發行變更歷程記錄。
services: cognitive-services
author: yluiu
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 06/06/2019
ms.author: yluiu
ms.openlocfilehash: a7667f94d3f4dea2901c4b4b0e2b2c893b9f535e
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "67074086"
---
# <a name="face-api-release-notes"></a>臉部 API 版本資訊

本文適用於臉部 API 服務 1.0 版。

### <a name="release-changes-in-june-2019"></a>在 2019 年 6 月發行的變更

* 改善的精確度，小型、 側邊檢視、 阻擋和模糊的表面上加入新的臉部偵測模型。 使用透過[面臨-偵測](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)， [FaceList-新增臉部](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395250)， [LargeFaceList-新增臉部](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a158c10d2de3616c086f2d3)， [PersonGroup 個人新增臉部](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b)及[LargePersonGroup 個人新增臉部](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599adf2a3a7b9412a4d53f42)藉由指定新的臉部偵測模型名稱`detection_02`在`detectionModel`參數。 在 更多詳細資料[如何指定偵測模型](Face-API-How-to-Topics/specify-detection-model.md)。

### <a name="release-changes-in-april-2019"></a>在 2019 年 4 月發行的變更

* 改善整體精確度`age`和`headPose`屬性。 `headPose`屬性也會更新與`pitch`現在已啟用的值。 藉由指定在使用這些屬性`returnFaceAttributes`的參數[面臨-偵測](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)`returnFaceAttributes`參數。 

* 改善的速度[面臨-偵測](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)， [FaceList-新增臉部](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395250)， [LargeFaceList-新增臉部](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a158c10d2de3616c086f2d3)， [PersonGroup 個人新增臉部](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b)和[LargePersonGroup 個人新增臉部](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599adf2a3a7b9412a4d53f42)。

### <a name="release-changes-in-march-2019"></a>在 2019 年 3 月發行的變更

* 加入新的臉部辨識模型以改善精確度。 使用透過[面臨-偵測](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)， [FaceList-建立](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524b)， [LargeFaceList-建立](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a157b68d2de3616c086f2cc)， [PersonGroup-建立](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244)和[LargePersonGroup-建立](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acdee6ac60f11b48b5a9d)藉由指定新的臉部辨識模型名稱`recognition_02`在`recognitionModel`參數。 在 更多詳細資料[如何指定辨識模型](Face-API-How-to-Topics/specify-recognition-model.md)。

### <a name="release-changes-in-january-2019"></a>2019 年 1 月的發行變更

* 已新增快照集功能，以支援訂用帳戶間的資料移轉：[快照集](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/snapshot-get)。 在 更多詳細資料[如何將臉部資料移轉至不同的臉部訂用帳戶](Face-API-How-to-Topics/how-to-migrate-face-data.md)。

### <a name="release-changes-in-october-2018"></a>2018 年 10 月的發行變更

* [PersonGroup - 取得訓練狀態](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395247)、[LargePersonGroup - 取得訓練狀態](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599ae32c6ac60f11b48b5aa5) 及 [LargeFaceList - 取得訓練狀態](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a1582f8d2de3616c086f2cf) 中 `status`、`createdDateTime`、`lastActionDateTime` 和 `lastSuccessfulTrainingDateTime` 的精簡描述。

### <a name="release-changes-in-may-2018"></a>2018 年 5 月的發行變更

* 已大幅改善 `gender` 屬性，並已改善 `age`、`glasses`、`facialHair`、`hair`、`makeup` 屬性。 您可以透過 [Face - Detect](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) (臉部 - 偵測) 的 `returnFaceAttributes` 參數來使用這些屬性。 

* 在 [Face - Detect](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) (臉部 - 偵測)、[FaceList - Add Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395250) (FaceList - 新增臉部)、[LargeFaceList - Add Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a158c10d2de3616c086f2d3) (LargeFaceList - 新增臉部)、[PersonGroup Person - Add Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b) (PersonGroup Person - 新增臉部) 和 [LargePersonGroup Person - Add Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599adf2a3a7b9412a4d53f42) (LargePersonGroup Person - 新增臉部) 中，輸入影像檔大小限制從 4 MB 提高到 6 MB。

### <a name="release-changes-in-march-2018"></a>2018 年 3 月的發行變更

* 新增的百萬規模容器：[LargeFaceList](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a157b68d2de3616c086f2cc) 和 [LargePersonGroup](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acdee6ac60f11b48b5a9d)。 如需詳細資料，請參閱[如何使用大規模功能](Face-API-How-to-Topics/how-to-use-large-scale.md)。

* 將 [Face - Identify](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239) (臉部 - 識別) 的 `maxNumOfCandidatesReturned` 參數從 [1, 5] 提高到 [1, 100]，預設為 10。

### <a name="release-changes-in-may-2017"></a>2017 年 5 月的發行變更

* 在 [Face - Detect](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) (臉部 - 偵測) 的 `returnFaceAttributes` 參數中新增 `hair`、`makeup`、`accessory`、`occlusion`、`blur`、`exposure` 和 `noise` 屬性。

* 在 PersonGroup 和 [Face - Identify](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239) (臉部 - 識別) 中支援 一萬人。

* [PersonGroup Person - List](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395241) (PersonGroup Person - 列出) 支援分頁，其選擇性參數為：`start` 和 `top`。

* 支援同時對不同的 FaceList 和 PersonGroup 中的不同人員新增/刪除臉部。

### <a name="release-changes-in-march-2017"></a>2017 年 3 月的發行變更
* 在 [Face - Detect](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) (臉部 - 偵測) 的 `returnFaceAttributes` 參數中新增 `emotion` 屬性。

* 修正無法使用從 [Face - Detect](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) (臉部 - 偵測) 傳回的矩形，重新偵測臉部作為 [FaceList - Add Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395250) (FaceList - 新增臉部) 和 [PersonGroup Person - Add Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b) (PersonGroup Person - 新增臉部) 中的 `targetFace`。

* 修正可偵測的臉部大小，確保它完全介於 36x36 到 4096x4096 像素之間。

### <a name="release-changes-in-november-2016"></a>2016 年 11 月的發行變更
* 新增臉部儲存體標準訂用帳戶，在使用 [PersonGroup Person - Add Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b) (PersonGroup Person - 新增臉部) 或 [FaceList - Add Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395250) (FaceList - 新增臉部) 時，持續儲存更多的臉部，供比對識別及相似度之用。 儲存影像的計費方式為每 1000 張人臉美金 $0.5，並會依使用天數按比例計算。 免費層訂用帳戶的總人數會繼續僅限 1,000 名。

### <a name="release-changes-in-october-2016"></a>2016 年 10 月的發行變更
* 在 [FaceList - Add Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395250) (FaceList - 新增臉部) 和 [PersonGroup Person - Add Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b) (PersonGroup Person - 新增臉部) 中，將 targetFace 中有多個臉部的錯誤訊息，從 'There are more than one face in the image' 變更為 'There is more than one face in the image'。

### <a name="release-changes-in-july-2016"></a>2016 年 7 月的發行變更
* 在 [Face - Verify](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a) (臉部 - 驗證) 中支援 Face 對 Person 物件的驗證。

* 在 [Face - Find Similar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237) (臉部 - 尋找類似項目) 中新增選擇性 `mode` 參數，讓您可以選取兩個工作模式：`matchPerson` 和 `matchFace`，預設為 `matchPerson`。

* 在 [Face - Identify](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239) (臉部 - 識別) 中新增選擇性 `confidenceThreshold` 參數，讓使用者可以設定某個臉部是否屬於 Person 物件的閾值。

* 在 [PersonGroup - List](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395248) (PersonGroup - 列出) 中新增選擇性 `start` 和 `top` 參數，讓使用者可以為清單指定起點和 PersonGroup 總數。

### <a name="v10-changes-from-v0"></a>從 V0 變更為 V1.0
* 將服務根端點從 ```https://westus.api.cognitive.microsoft.com/face/v0/``` 更新為 ```https://westus.api.cognitive.microsoft.com/face/v1.0/```。 變更適用於：[Face - Detect](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) (臉部 - 偵測)、[Face - Identify](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239) (臉部 - 識別)、[Face - Find Similar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237) (臉部 - 尋找類似項目) 和 [Face - Group](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395238) (臉部 - 分組)。

* 將可偵測的臉部大小下限更新為 36x36 像素。 小於 36x36 像素的臉部將無法被偵測。

* 臉部 V0 中的 PersonGroup 和 Person 資料已淘汰。 無法使用臉部 V1.0 服務存取這些資料。

* 臉部 API 的 V0 端點已於 2016 年 6 月 30 日淘汰。
