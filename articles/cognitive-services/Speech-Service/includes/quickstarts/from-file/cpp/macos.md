---
title: 快速入門：辨識來自音訊檔案的語音，C++ (macOS) - 語音服務
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: wolfma61
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 01/14/2020
ms.author: wolfma
ms.openlocfilehash: 4a043c246cc859706e062cdc11d30cbd657bc6b2
ms.sourcegitcommit: dbcc4569fde1bebb9df0a3ab6d4d3ff7f806d486
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/15/2020
ms.locfileid: "76037878"
---
## <a name="prerequisites"></a>Prerequisites

開始之前，請務必：

> [!div class="checklist"]
> * [建立 Azure 語音資源](../../../../get-started.md)
> * [設定開發環境](../../../../quickstarts/setup-platform.md?tabs=macos)
> * [建立空的範例專案](../../../../quickstarts/create-project.md?tabs=macos)

[!INCLUDE [Audio input format](~/articles/cognitive-services/speech-service/includes/audio-input-format-chart.md)]

## <a name="add-sample-code"></a>新增範例程式碼

1. 建立名為 `helloworld.cpp` 的 C++ 原始程式檔，然後在其中貼上下列程式碼。

   ```cpp
    // Creates an instance of a speech config with specified subscription key and service region.
    // Replace with your own subscription key and service region (e.g., "westus").
    auto config = SpeechConfig::FromSubscription("YourSubscriptionKey", "YourServiceRegion");

    // Creates a speech recognizer using a WAV file. The default language is "en-us".
    // Replace with your own audio file name.
    auto audioInput = AudioConfig::FromWavFileInput("whatstheweatherlike.wav");
    auto recognizer = SpeechRecognizer::FromConfig(config, audioInput);
    cout << "Recognizing first result...\n";

    // Starts speech recognition, and returns after a single utterance is recognized. The end of a
    // single utterance is determined by listening for silence at the end or until a maximum of 15
    // seconds of audio is processed.  The task returns the recognition text as result.
    // Note: Since RecognizeOnceAsync() returns only a single utterance, it is suitable only for single
    // shot recognition like command or query.
    // For long-running multi-utterance recognition, use StartContinuousRecognitionAsync() instead.
    auto result = recognizer->RecognizeOnceAsync().get();

    // Checks result.
    switch (result->Reason)
    {
        case ResultReason::RecognizedSpeech:
            cout << "RECOGNIZED: Text=" << result->Text << std::endl;
            break;
        case ResultReason::NoMatch:
            cout << "NOMATCH: Speech could not be recognized." << std::endl;
            break;
        case ResultReason::Canceled:
            auto cancellation = CancellationDetails::FromResult(result);
            cout << "CANCELED: Reason=" << (int)cancellation->Reason << std::endl;

            if (cancellation->Reason == CancellationReason::Error)
            {
                cout << "CANCELED: ErrorCode=" << (int)cancellation->ErrorCode << std::endl;
                cout << "CANCELED: ErrorDetails=" << cancellation->ErrorDetails << std::endl;
                cout << "CANCELED: Did you update the subscription info?" << std::endl;
            }
            break;
    }
   ```

1. 在這個新檔案中，以您的語音服務訂用帳戶金鑰取代 `YourSubscriptionKey` 字串。

1. 以與您的訂用帳戶 (例如，免費試用訂用帳戶的 `westus`) 相關聯的[區域](~/articles/cognitive-services/Speech-Service/regions.md)取代 `YourServiceRegion` 字串。

1. 以您自己的檔案名稱取代字串 `whatstheweatherlike.wav`。

> [!NOTE]
> 語音 SDK 會預設為使用 en-us 來辨識語言，如需選擇來源語言的詳細資訊，請參閱[指定語音轉換文字的來源語言](../../../../how-to-specify-source-language.md)。

## <a name="build-the-app"></a>建置應用程式

> [!NOTE]
> 請務必以「單一命令行」  的形式輸入下面的命令。 若要這麼做，最簡單的方式就是使用每個命令旁邊的 [複製]  按鈕來複製命令，然後將它貼在殼層提示字元。

* 執行下列命令以建置應用程式。

  ```sh
  g++ helloworld.cpp -o helloworld --std=c++14 -F${SPEECHSDK_ROOT} -framework MicrosoftCognitiveServicesSpeech
  ```

## <a name="run-the-app"></a>執行應用程式

1. 將載入程式的程式庫路徑設定為指向語音 SDK 程式庫。

    ```sh
    export DYLD_FRAMEWORK_PATH="$DYLD_FRAMEWORK_PATH:$SPEECHSDK_ROOT"
    ```

1. 執行應用程式。

   ```sh
   ./helloworld
   ```

1. 您的音訊檔案會傳送到語音服務，且檔案中的第一個語句會轉譯為文字，該文字會出現在相同視窗中。

   ```text
   Recognizing first result...
   We recognized: What's the weather like?
   ```

## <a name="next-steps"></a>後續步驟

[!INCLUDE [footer](./footer.md)]
