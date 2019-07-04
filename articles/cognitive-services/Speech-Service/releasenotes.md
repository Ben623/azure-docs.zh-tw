---
title: 版本資訊 - 語音服務
titlesuffix: Azure Cognitive Services
description: 請參閱 Azure 語音服務的功能版本、功能改進、Bug 修正和已知問題的執行記錄。
services: cognitive-services
author: wolfma61
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 06/26/2019
ms.author: wolfma
ms.custom: seodec18
ms.openlocfilehash: 609443a4926fabd991846faee4a0a7dffe3a696b
ms.sourcegitcommit: ac1cfe497341429cf62eb934e87f3b5f3c79948e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/01/2019
ms.locfileid: "67490185"
---
# <a name="release-notes"></a>版本資訊

## <a name="speech-sdk-160-2019-june-release"></a>語音 SDK 1.6.0:2019 年 6 月版本

**範例**
*   在 UWP 和 Unity 的文字轉換語音的快速入門範例
*   在 iOS 上的快速入門範例 for Swift
*   語音意圖辨識和轉譯的 unity 範例
*   DialogServiceConnector 的已更新的快速入門範例

**/變更**
* 對話方塊命名空間：
    * 已重新命名為 DialogServiceConnector SpeechBotConnector
    * 已重新命名為 DialogServiceConfig BotConfig
    * 已重新對應至 DialogServiceConfig::FromBotSecret() 的 BotConfig::FromChannelSecret()
    * 所有現有的直接列語音用戶端會繼續支援重新命名之後
* 更新 TTS REST 介面卡，若要支援 proxy，持續連線
* 當傳遞無效的地區時，改善錯誤訊息
* C: Swift/目標
    * 已改進的錯誤報告：可能會導致錯誤的方法現在有兩種版本：公開 （expose） 的其中一個`NSError`錯誤處理和引發例外狀況的其中一個物件。 前者會公開至 Swift。 這項變更需要採用現有的 Swift 程式碼。
    * 改良式的事件處理

**錯誤修正**
*   修正 TTS： 其中 SpeakTextAsync 未來傳回，而不等待，直到完成呈現音訊。
*   修正程式中的字串封送處理C#若要啟用完整的語言支援
*   載入與 net461 範例中的目標 framework 的核心程式庫的.NET core 應用程式問題的修正
*   若要部署至範例的輸出資料夾的原生程式庫的偶發問題修正
*   修正可靠地關閉 web 通訊端
*   可能的損毀時開啟連線，以在 Linux 上的非常繁重負載下的修正程式
*   修正適用於 macOS 的架構組合中的遺漏中繼資料
*   修正問題`pip install --user`在 Windows 上


## <a name="speech-sdk-151"></a>Speech SDK 1.5.1

這是修正程式的版本，只會影響原生 SDK。 它不會影響 JavaScript 版本的 sdk。

**錯誤修正**

* 修正 FromSubscription 交談文字記錄搭配使用時。
* 修正錯誤，找出語音第一個虛擬助理的關鍵字。


## <a name="speech-sdk-150-2019-may-release"></a>語音 SDK 1.5.0:2019 年發行

**新功能**

* 現在適用於 Windows 和 Linux 喚醒字 (關鍵字找出/KWS) 功能。 KWS 功能可能會使用任何麥克風型別，官方 KWS 支援，不過，目前僅限麥克風陣列中找到 Azure Kinect DK 硬體或語音裝置 SDK。
* 透過 SDK 使用片語提示功能。 如需詳細資訊，請參閱[這裡](how-to-phrase-lists.md)。
* 交談文字記錄功能是透過 SDK。 請參閱 [這裡](conversation-transcription-service.md)。
* 新增語音第一個虛擬助理使用直接列語音通道支援。

**範例**

* 加入新功能或支援的 SDK 的新服務的範例。

**/變更**

* 加入各種不同的辨識器屬性，以調整服務行為或服務 （例如遮罩不雅內容等） 的結果。
* 您現在可以設定透過標準設定的屬性，辨識器，即使您建立的辨識器`FromEndpoint`。
* 本節目標 c:`OutputFormat`屬性新增至 SPXSpeechConfiguration。
* SDK 現在支援 Debian 9，作為 Linux 發佈。

**錯誤修正**

* 修正的問題，演講者備忘資源已解構過早在文字轉換語音的位置。
## <a name="speech-sdk-142"></a>Speech SDK 1.4.2

這是修正程式的版本，只會影響原生 SDK。 它不會影響 JavaScript 版本的 sdk。

## <a name="speech-sdk-141"></a>Speech SDK 1.4.1

這是僅限 JavaScript 的版本。 未新增任何功能。 已進行下列修正：

* Web 組件造成妨礙載入 https proxy 代理程式。

## <a name="speech-sdk-140-2019-april-release"></a>語音 SDK 1.4.0:2019 年 4 月版本

**新功能** 

* SDK 會為 beta 版，現在支援文字轉換語音服務。 它支援 Windows 和 Linux 桌面，從C++和C#。 如需詳細資訊，請[文字轉換語音概觀](text-to-speech.md#get-started-with-text-to-speech)。
* SDK 現在支援 MP3 和這種門外漢只/OGG 音訊檔案做為資料流輸入檔。 這項功能是只能在從 Linux 上使用C++和C#，目前在 beta 測試階段 (更多詳細資料[此處](how-to-use-codec-compressed-audio-input-streams.md))。
* 語音 SDK for Java，.NET core C++ ，OBJECTIVE-C 已獲得 macOS 的支援。 適用於 macOS 的 Objective C 支援目前為 beta 版。
* iOS：語音 SDK 適用於 iOS (Objective C) 現在也發行為 CocoaPod。
* JavaScript：做為輸入裝置的非預設麥克風的支援。
* JavaScript：適用於 Node.js 的 proxy 支援。

**範例**

* 適用於使用語音 SDK 與範例C++和已加入在 macOS 上的 Objective C。
* 已加入示範文字轉換語音服務的使用方式的範例。

**/變更**

* Python：現在透過公開的辨識結果的其他屬性`properties`屬性。
* 如需額外的開發和偵錯支援，您可以重新導向到記錄檔的 SDK 的記錄和診斷資訊 (更多詳細資料[此處](how-to-use-logging.md))。
* JavaScript：改善音訊處理效能。

**錯誤修正**

* Mac/iOS:已修正導致長時間的等待，無法建立語音服務的連接時的錯誤。
* Python： 改善錯誤處理中 Python 回呼的引數。
* JavaScript：已修正錯誤的狀態報告語音已經在 RequestSession 結束。

## <a name="speech-sdk-131-2019-february-refresh"></a>語音 SDK 1.3.1:2019 年 2 月重新整理

這是修正程式的版本，只會影響原生 SDK。 它不會影響 JavaScript 版本的 sdk。

**Bug 修复**

* 使用麥克風的輸入時，已修正記憶體流失。 不會影響基礎 Stream 或輸入的檔案。

## <a name="speech-sdk-130-2019-february-release"></a>語音 SDK 1.3.0：2019 年 2 月版本

**新功能**

* 語音 SDK 支援透過 AudioConfig 類別來選取輸入麥克風。 這可讓您將音訊資料串流到語音服務從非預設麥克風。 如需詳細資訊，請參閱文件描述[音訊輸入裝置選取](how-to-select-audio-input-devices.md)。 這項功能尚無法從 JavaScript 使用。
* 語音 SDK 現在支援搶鮮版 (Beta) Unity。 透過在 [問題] 區段提供意見反應[GitHub 範例存放庫](https://aka.ms/csspeech/samples)。 此版本可在 Windows x86 和 x64 (桌面或通用 Windows 平台應用程式) 以及 Android (ARM32/64、x86) 上支援 Unity。 詳情請參閱 [Unity 快速入門](quickstart-csharp-unity.md)。
* 檔案`Microsoft.CognitiveServices.Speech.csharp.bindings.dll`（隨附於之前的版本），則不需要再。 功能現在已整合至核心 SDK。


**範例**

[範例存放庫](https://aka.ms/csspeech/samples)有提供下列新內容：

* 額外的 AudioConfig.FromMicrophoneInput 範例。
* 額外的 Python 範例，適用於意圖辨識和翻譯。
* 在 iOS 中使用連線物件的額外範例。
* 額外的 Java 範例，適用於以音訊輸出進行翻譯。
* [批次轉譯 REST API](batch-transcription.md) 的新使用範例。

**/變更**

* Python
  * 改善了 SpeechConfig 中的參數驗證及錯誤訊息。
  * 新增連線物件支援。
  * 支援在 Windows 上使用 32 位元的 Python (x86)。
  * 適用於 Python 的語音 SDK 已不再是搶鮮版 (Beta)。
* iOS
  * SDK 現在是根據 iOS SDK 12.1 版來建置。
  * SDK 現在支援 iOS 9.2 版和更新版本。
  * 改善參考文件，並修正數個屬性名稱。
* JavaScript
  * 新增連線物件支援。
  * 為配套的 JavaScript 新增了類型定義檔
  * 初次支援和實作片語提示。
  * 傳回含有服務 JSON 的屬性集合以供辨識
* Windows DLL 現在會包含版本資源。
* 如果您建立的辨識器`FromEndpoint`您可以將參數，直接加入的端點 URL。 使用`FromEndpoint`您無法設定辨識器，透過標準的組態屬性。

**錯誤修正**

* 空的 Proxy 使用者名稱和 Proxy 密碼之前不會正確處理。 在此版本中，如果您將 Proxy 使用者名稱和 Proxy 密碼設定為空字串，系統將不會在連線到 Proxy 時提交出去。
* 在某些語言&nbsp;/環境中，SDK 所建立的工作階段識別碼並不一定會真正隨機。 加入亂數產生器初始化，若要修正此問題。
* 改善授權權杖的處理方式。 如果您想要使用授權權杖，請在 SpeechConfig 中指定，並讓訂用帳戶金鑰保持空白。 然後如往常般建立辨識器。
* 在某些情況下，連線物件未正確釋放。 已修正此問題。
* JavaScript 範例已修正，現在也能夠在 Safari 上支援以音訊輸出合成翻譯。

## <a name="speech-sdk-121"></a>語音 SDK 1.2.1

這是僅限 JavaScript 的版本。 未新增任何功能。 已進行下列修正：

* 會在 turn.end 而非 speech.end 引發資料流的結束。
* 修正音訊幫浦中的 BUG，此 BUG 會導致系統未在目前的傳送失敗時排程下一次傳送。
* 修正使用驗證權杖來進行的連續辨識。
* 修正不同辨識器/端點的 BUG。
* 文件改進。

## <a name="speech-sdk-120-2018-december-release"></a>語音 SDK 1.2.0：2018 年 12 月版本

**新功能**

* Python
  * 這一版提供 Python 支援的搶鮮版 (Beta) (3.5 和更新版本)。 如需詳細資訊，請參閱 here](quickstart-python.md)。
* JavaScript
  * 適用於 JavaScript 的語音 SDK 已開放原始碼。 原始程式碼位於 [GitHub](https://github.com/Microsoft/cognitive-services-speech-sdk-js)。
  * 我們現在支援 Node.js，您可以在[這裡](quickstart-js-node.md)找到更多資訊。
  * 音訊工作階段的長度限制已移除，重新連線會自動發生。
* 連線物件
  * 從辨識器，您可以存取連接物件。 此物件可讓您明確地起始服務連線，並訂閱連線和中斷連線事件。
    （這項功能尚無法使用從 JavaScript 和 Python。）
* Ubuntu 18.04 支援。
* Android
  * 已在 APK 產生期間啟用 ProGuard 支援。

**改進**

* 改進內部執行緒使用方式，並減少執行緒、鎖定、Mutex 的數目。
* 已改善錯誤回報 / 資訊。 在許多情況下，錯誤訊息不已經傳播出一路放大。
* 已將 JavaScript 中的開發相依性更新為使用最新的模組。

**錯誤修正**

* 已修正由於 RecognizeAsync 中類型不符所造成的記憶體流失。
* 在某些情況下，例外狀況正在流失。
* 已修正翻譯事件引數的記憶體流失。
* 已修正在長時間執行的工作階段中重新連線的鎖定問題。
* 修正遺漏失敗翻譯的最終結果可能會導致的問題。
* C#: 如果非同步作業未在主執行緒中等候，則在非同步工作完成之前，就可能處置了辨識器。
* Java：已修正導致 Java VM 損毀的問題。
* Objective-C：已修正列舉對應；傳回了 RecognizedIntent 而不是 RecognizingIntent。
* JavaScript：在 SpeechConfig 中將預設輸出格式設定為 'simple'。
* JavaScript：移除 JavaScript 與其他語言中組態物件屬性之間的不一致。

**範例**

* 更新和已修正數個範例 （例如輸出語音翻譯等。）。
* 已在[範例存放庫](https://aka.ms/csspeech/samples)中新增 Node.js 範例。

## <a name="speech-sdk-110"></a>語音 SDK 1.1.0

**新功能**

* 支援 Android x86/x64。
* Proxy 支援：在 SpeechConfig 物件中，您現在可以呼叫的函式來設定 proxy 資訊 （主機名稱、 連接埠、 使用者名稱和密碼）。 此功能尚無法在 iOS 上取得。
* 改善的錯誤碼和訊息。 如果辨識傳回錯誤，系統現在會將 `Reason` (在取消的事件中) 或 `CancellationDetails` (在辨識結果中) 設為 `Error`。 取消的事件現在包含兩個額外的成員，`ErrorCode` 和 `ErrorDetails`。 如果伺服器隨報告的錯誤傳回其他錯誤資訊，現在將可以透過新成員取得這些資訊。

**改進**

* 已在辨識器設定中新增其他驗證，及新增其他錯誤訊息。
* 已改善對音訊檔案中長時間無聲之部分的處理。
* NuGet 套件：針對 .NET Framework 專案，其可防止使用 AnyCPU 設定來建置。

**錯誤修正**

* 已修正在辨識器中找到的幾個例外狀況。 此外，例外狀況會攔截到和轉換成已取消事件。
* 修正屬性管理中的記憶體流失。
* 已修正音訊輸入可能會損毀辨識器的錯誤。
* 已修正工作階段將事件停止之後，仍然可收到事件的錯誤。
* 已修正執行緒中的一些競爭條件。
* 已修正可能會導致當機的 iOS 相容性問題。
* 已改善對 Android 麥克風支援的穩定性。
* 已修正 JavaScript 中的辨識器會忽略辨識語言的錯誤。
* 已修正 JavaScript 中 (在某些情況下) 會防止設定 EndpointId 的錯誤。
* 變更 JavaScript 中 AddIntent 內參數的順序，和新增遺失的 AddIntent JavaScript 簽章。

**範例**

* 已在[範例存放庫](https://aka.ms/csspeech/samples)中新增可供提取和推送資料流使用方式的 C++ 和 C# 範例。

## <a name="speech-sdk-101"></a>語音 SDK 1.0.1

穩定性改進及 Bug 修正：

* 已修正處置辨識器時因競爭條件而可能導致的嚴重錯誤
* 已修正未設定屬性時可能導致的嚴重錯誤。
* 已新增額外的錯誤和參數檢查。
* Objective-C：已修正因 NSString 中的名稱覆寫而可能導致的嚴重錯誤。
* Objective-C：已調整 API 的可見性
* JavaScript：已修正有關事件及其承載的問題。
* 文件改進。

在我們的[範例存放庫](https://aka.ms/csspeech/samples)中，已新增新的 JavaScript 範例。

## <a name="cognitive-services-speech-sdk-100-2018-september-release"></a>認知服務語音 SDK 1.0.0：2018 年 9 月版本

**新功能**

* iOS 上對 Objective-C 的支援。 請參閱我們的[適用於 iOS 的 Objective-C 快速入門](quickstart-objectivec-ios.md)。
* 瀏覽器中對 JavaScript 的支援。 請參閱我們的 [JavaScript 快速入門](quickstart-js-browser.md)。

**重大變更**

* 此版本中，導入了一些重大變更。
  請檢查[本頁](https://aka.ms/csspeech/breakingchanges_1_0_0)如需詳細資訊。

## <a name="cognitive-services-speech-sdk-060-2018-august-release"></a>認知服務語音 SDK 0.6.0：2018 年 8 月版本

**新功能**

* 使用語音 SDK 所建置的 UWP 應用程式現在可以通過 Windows 應用程式認證套件 (WACK) 的認證。
  請參閱 [UWP 快速入門](quickstart-csharp-uwp.md)。
* 在 Linux (Ubuntu 16.04 x64) 上支援 .NET Standard 2.0。
* 實驗性：在 Windows (64 位元) 和 Linux (Ubuntu 16.04 x64) 上支援 Java 8。
  請參閱 [Java 執行階段環境快速入門](quickstart-java-jre.md)。

**功能性變更**

* 公開關於連線錯誤的其他錯誤詳細資訊。

**重大變更**

* 在 Java (Android) 上，`SpeechFactory.configureNativePlatformBindingWithDefaultCertificate` 函式不再需要路徑參數。 系統現在會自動在所有支援平台上偵測路徑。
* Java 和 C# 中 `EndpointUrl` 屬性的 get-accessor 已移除。

**錯誤修正**

* 在 Java 中，現已實作翻譯辨識器上的音訊合成結果。
* 已修正錯誤，此錯誤可能導致非作用中的執行緒，並增加已開啟且未使用的通訊端數目。
* 已修正問題，長時間執行的辨識可能會在傳輸途中終止。
* 已修正辨識器關機中的競爭條件。

## <a name="cognitive-services-speech-sdk-050-2018-july-release"></a>認知服務語音 SDK 0.5.0：2018 年 7 月版本

**新功能**

* 支援 Android 平台 (API 23：Android 6.0 Marshmallow 或更新版本)。 請參閱 [Android 快速入門](quickstart-java-android.md)。
* 在 Windows 上支援 .NET Standard 2.0。 請參閱 [.NET Core 快速入門](quickstart-csharp-dotnetcore-windows.md)。
* 實驗性：在 Windows 上支援 UWP (1709 版或更新版本)。
  * 請參閱 [UWP 快速入門](quickstart-csharp-uwp.md)。
  * 注意：使用語音 SDK 建置的 UWP 應用程式尚未通過 Windows 應用程式認證套件 (WACK) 的認證。
* 使用自動重新連線支援長時間執行辨識。

**功能性變更**

* `StartContinuousRecognitionAsync()` 支援長時間執行辨識。
* 辨識結果包含更多欄位。 它們從已辨識文字的音訊開頭和持續時間 (兩者都以刻度為單位)，以及表示辨識狀態的其他值位移，例如 `InitialSilenceTimeout` 和 `InitialBabbleTimeout`。
* 支援 AuthorizationToken 以建立處理站執行個體。

**重大變更**

* 辨識事件：NoMatch 事件類型已合併到錯誤事件中。
* C# 中的 SpeechOutputFormat 已重新命名為 OutputFormat，以便與 C++ 保持一致。
* `AudioInputStream` 介面部分方法的傳回型別已稍微變更：
   * 在 Java 中，`read` 方法現在會傳回 `long` 而不是 `int`。
   * 在 C# 中，`Read` 方法現在會傳回 `uint` 而不是 `int`。
   * 在 C++ 中，`Read` 和 `GetFormat` 方法現在會傳回 `size_t` 而不是 `int`。
* C++：音訊輸入串流的執行個體現在只能以 `shared_ptr` 的形式傳遞。

**錯誤修正**

* 修正 `RecognizeAsync()` 逾時的時候，結果中不正確的傳回值。
* 已移除 Windows 上媒體基礎程式庫的相依性。 SDK 現在使用核心音訊 API。
* 文件修正：已新增 [[區域]](regions.md) 頁面來說明支援的區域。

**已知問題**

* 適用於 Android 的語音 SDK 不會回報用於翻譯的語音合成結果。 此問題將在下一版中修正。

## <a name="cognitive-services-speech-sdk-040-2018-june-release"></a>認知服務語音 SDK 0.4.0：2018 年 6 月版本

**功能性變更**

- AudioInputStream

  辨識器現在可以取用資料流作為音訊來源。 如需詳細資訊，請參閱相關的[操作說明指南](how-to-use-audio-input-streams.md)。

- 詳細的輸出格式

  建立 `SpeechRecognizer` 時，您可以要求 `Detailed` 或 `Simple` 輸出格式。 `DetailedSpeechRecognitionResult` 包含信賴分數、已辨識的文字、未經處理的語彙形式，以及標準化形式和加上遮罩的粗話。

**重大變更**

- 在 C# 中已從 `SpeechRecognitionResult.RecognizedText` 變更為 `SpeechRecognitionResult.Text`。

**錯誤修正**

- 已修正在關閉期間 USP 層中可能發生的回呼問題。

- 如果辨識器取用音訊輸入檔，其保留檔案控制代碼超過所需的時間。

- 已移除訊息幫浦與辨識器之間的數個死結。

- 如果來自服務的回應逾時，則引發 `NoMatch`。

- Windows 上的媒體基礎程式庫延遲載入。 只有麥克風輸入需要此程式庫。

- 音訊資料的上傳速度受限於大約兩倍的原始音訊速度。

- 在 Windows 上，C# .NET 組件現在使用強式名稱。

- 文件修正：`Region` 是建立辨識器時所需的資訊。

已新增更多範例，且持續更新。 如需最新的範例集，請參閱[語音 SDK 範例 GitHub 存放庫](https://aka.ms/csspeech/samples) \(英文\)。

## <a name="cognitive-services-speech-sdk-0212733-2018-may-release"></a>認知服務語音 SDK 0.2.12733：2018 年 5 月版本

此版本為認知服務語音 SDK 的第一個公開預覽版本。
