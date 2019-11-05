---
title: 安裝語音容器
titleSuffix: Azure Cognitive Services
description: 詳細說明語音傘 helm 圖表設定選項。
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 06/26/2019
ms.author: dapine
ms.openlocfilehash: ed64412ccf9d192506fafe546b1ccee7941aa43a
ms.sourcegitcommit: 3f8017692169bd75483eefa96c225d45cd497f06
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/04/2019
ms.locfileid: "73522501"
---
### <a name="speech-umbrella-chart"></a>語音（傘圖）

最上層「傘」圖表中的值會覆寫對應的子圖表值。 因此，您應該在這裡新增所有內部部署的自訂值。

|參數|說明|預設值|
| -- | -- | -- | -- |
| `speechToText.enabled` | **語音轉換文字**服務是否已啟用。 | `true` |
| `speechToText.verification.enabled` | **語音轉換文字**服務的 `helm test` 功能是否已啟用。 | `true` |
| `speechToText.verification.image.registry` | `helm test` 用來測試**語音轉換文字**服務的 docker 映射存放庫。 Helm 會在叢集內建立個別的 pod 來進行測試，並從這個登錄提取*測試用*的映射。 | `docker.io` |
| `speechToText.verification.image.repository` | `helm test` 用來測試**語音轉換文字**服務的 docker 映射存放庫。 Helm 測試 pod 會使用此存放庫來提取*測試用的*映射。 | `antsu/on-prem-client` |
| `speechToText.verification.image.tag` | 用於**語音轉換文字**服務 `helm test` 的 docker 映射標記。 Helm 測試 pod 會使用此標記來提取*測試用的*影像。 | `latest` |
| `speechToText.verification.image.pullByHash` | 是否由雜湊提取*測試用*的 docker 映射。 如果 `true`，則應使用有效的影像雜湊值來加入 `speechToText.verification.image.hash`。 | `false` |
| `speechToText.verification.image.arguments` | 用來執行*測試用*docker 映射的引數。 Helm 測試 pod 會在執行 `helm test`時，將這些引數傳遞至容器。 | `"./speech-to-text-client"`<br/> `"./audio/whatstheweatherlike.wav"` <br/> `"--expect=What's the weather like"`<br/>`"--host=$(SPEECH_TO_TEXT_HOST)"`<br/>`"--port=$(SPEECH_TO_TEXT_PORT)"` |
| `textToSpeech.enabled` | **文字轉換語音**服務是否已啟用。 | `true` |
| `textToSpeech.verification.enabled` | **語音轉換文字**服務的 `helm test` 功能是否已啟用。 | `true` |
| `textToSpeech.verification.image.registry` | `helm test` 用來測試**語音轉換文字**服務的 docker 映射存放庫。 Helm 會在叢集內建立個別的 pod 來進行測試，並從這個登錄提取*測試用*的映射。 | `docker.io` |
| `textToSpeech.verification.image.repository` | `helm test` 用來測試**語音轉換文字**服務的 docker 映射存放庫。 Helm 測試 pod 會使用此存放庫來提取*測試用的*映射。 | `antsu/on-prem-client` |
| `textToSpeech.verification.image.tag` | 用於**語音轉換文字**服務 `helm test` 的 docker 映射標記。 Helm 測試 pod 會使用此標記來提取*測試用的*影像。 | `latest` |
| `textToSpeech.verification.image.pullByHash` | 是否由雜湊提取*測試用*的 docker 映射。 如果 `true`，則應使用有效的影像雜湊值來加入 `textToSpeech.verification.image.hash`。 | `false` |
| `textToSpeech.verification.image.arguments` | 要與*測試用*docker 映射一起執行的引數。 Helm 測試 pod 會在執行 `helm test`時，將這些引數傳遞至容器。 | `"./text-to-speech-client"`<br/> `"--input='What's the weather like'"` <br/> `"--host=$(TEXT_TO_SPEECH_HOST)"`<br/>`"--port=$(TEXT_TO_SPEECH_PORT)"` |