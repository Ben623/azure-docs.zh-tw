---
title: 快速入門：適用於 .NET 的個人化工具用戶端程式庫
titleSuffix: Azure Cognitive Services
description: 此快速入門示範如何使用學習迴圈，開始使用適用於 .NET 的個人化工具用戶端程式庫。
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: quickstart
ms.date: 10/24/2019
ms.author: diberry
ms.openlocfilehash: c17bf54d89e3a98ca667eeba40f2d2b166550833
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/25/2019
ms.locfileid: "75446381"
---
# <a name="quickstart-personalizer-client-library-for-net"></a>快速入門：適用於 .NET 的個人化工具用戶端程式庫

使用個人化工具服務顯示此 C# 快速入門中的個人化內容。

開始使用適用於 .NET 的個人化工具用戶端程式庫。 請遵循下列步驟來安裝套件，並試用基本工作的程式碼範例。

 * 為個人化的動作清單排名。
 * 回報獎勵分數，指出已成功排列出名次最高的動作。

[參考文件](https://docs.microsoft.com/dotnet/api/Microsoft.Azure.CognitiveServices.Personalizer?view=azure-dotnet-preview) | [程式庫來源程式碼](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Personalizer) | [套件 (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Personalizer/) | [範例](https://github.com/Azure-Samples/cognitive-services-personalizer-samples)

## <a name="prerequisites"></a>Prerequisites

* Azure 訂用帳戶 - [建立免費帳戶](https://azure.microsoft.com/free/)
* 最新版 [.NET Core](https://dotnet.microsoft.com/download/dotnet-core)。

## <a name="using-this-quickstart"></a>使用此快速入門

使用本快速入門有幾個步驟：

* 在 Azure 入口網站中，建立個人化工具資源
* 在 Azure 入口網站中，於個人化工具資源的 [設定]  頁面上，變更模型更新頻率
* 在程式碼編輯器中，建立程式碼檔案並編輯程式碼檔案
* 在命令列或終端機中，從命令列安裝 SDK
* 在命令列或終端機中，執行程式碼檔案

## <a name="create-a-personalizer-azure-resource"></a>建立個人化工具 Azure 資源

請使用 [Azure 入口網站](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account)或 [Azure CLI](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli) 在本機電腦上建立個人化工具的資源。 您也可以：

* 取得可免費使用 7 天的[試用版金鑰](https://azure.microsoft.com/try/cognitive-services)。 註冊之後，即可在 [Azure 網站](https://azure.microsoft.com/try/cognitive-services/my-apis/)上取得該金鑰。  
* 在 [Azure 入口網站](https://portal.azure.com/)上檢視您的資源。

從試用版訂用帳戶或資源取得金鑰後，請建立兩個[環境變數](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication)：

* `PERSONALIZER_RESOURCE_KEY` 用於資源金鑰。
* `PERSONALIZER_RESOURCE_ENDPOINT` 用於資源端點。

在 Azure 入口網站中，可以從 [快速入門]  頁面取得金鑰和端點值。

## <a name="change-the-model-update-frequency"></a>變更模型更新頻率

在 Azure 入口網站中，於個人化工具資源的 [設定]  頁面上，將 [模型更新頻率]  變更為 10 秒。 這個較短的時間將會快速定型服務，讓您可以查看名次最高的動作會如何針對每個反覆項目變更。

![變更模型更新頻率](./media/settings/configure-model-update-frequency-settings.png)

第一次具現化個人化工具迴圈時，因為已經沒有可進行訓練的獎勵 API 呼叫，所以沒有模型。 排名呼叫會針對每個項目傳回相等的機率。 您的應用程式應該仍會一直使用 RewardActionId 的輸出來排名內容。

## <a name="create-a-new-c-application"></a>建立新的 C# 應用程式

在您慣用的編輯器或 IDE 中，建立新的 .NET Core 應用程式。 

在主控台視窗中 (例如 cmd、PowerShell 或 Bash)，使用 dotnet `new` 命令建立名為 `personalizer-quickstart` 的新主控台應用程式。 此命令會建立簡單的 "Hello World" C# 專案，內含單一原始程式檔：`Program.cs`。 

```console
dotnet new console -n personalizer-quickstart
```

將目錄變更為新建立的應用程式資料夾。 您可以使用下列命令來建置應用程式：

```console
dotnet build
```

建置輸出應該不會有警告或錯誤。 

```console
...
Build succeeded.
 0 Warning(s)
 0 Error(s)
...
```

## <a name="install-the-sdk"></a>安裝 SDK

在應用程式目錄中，使用下列命令安裝適用於 .NET 的個人化工具用戶端程式庫：

```console
dotnet add package Microsoft.Azure.CognitiveServices.Personalizer --version 0.8.0-preview
```

如果您使用 Visual Studio IDE，則可以取得可下載 NuGet 套件形式的用戶端程式庫。

## <a name="object-model"></a>物件模型

個人化工具用戶端是一種 [PersonalizerClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.personalizer.personalizerclient?view=azure-dotnet) 物件，會使用含有金鑰的 Microsoft.Rest.ServiceClientCredentials 向 Azure 進行驗證。

若要要求內容的排名，請建立 [RankRequest](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.personalizer.models.rankrequest?view=azure-dotnet-preview)，然後將其傳遞給 [client.Rank](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.personalizer.personalizerclientextensions.rank?view=azure-dotnet-preview) 方法。 Rank 方法會傳回包含排名內容的 RankResponse。 

若要將獎勵傳送給個人化工具，請建立 [RewardRequest](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.personalizer.models.rewardrequest?view=azure-dotnet-preview)，然後將其傳遞至 [client.Reward](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.personalizer.personalizerclientextensions.reward?view=azure-dotnet-preview) 方法。 

在本快速入門中，決定獎勵是很簡單的。 在生產系統中，判斷影響[獎勵分數](concept-rewards.md)的因素及影響程度可能是複雜的程序，您可能會隨著時間做出變更決定。 此設計決策應該是您個人化工具架構中的其中一個主要決策。 

## <a name="code-examples"></a>程式碼範例

這些程式碼片段會示範如何使用適用於 .NET 的個人化工具用戶端程式庫來執行下列工作：

* [建立個人化工具用戶端](#create-a-personalizer-client)
* [要求排名](#request-a-rank)
* [傳送獎勵](#send-a-reward)

## <a name="add-the-dependencies"></a>新增相依性

從專案目錄，在慣用的編輯器或 IDE 中開啟 **Program.cs** 檔案。 將現有的 `using` 程式碼取代為下列 `using` 指示詞：

[!code-csharp[Using statements](~/samples-personalizer/quickstarts/csharp/PersonalizerExample/Program.cs?name=Dependencies)]

## <a name="add-personalizer-resource-information"></a>新增個人化工具資源資訊

在 [程式]  類別中，針對從環境變數中提取的資源 Azure 金鑰和端點 (名稱為 `PERSONALIZER_RESOURCE_KEY` 和 `PERSONALIZER_RESOURCE_ENDPOINT`) 建立變數。 如果您在應用程式啟動後才建立環境變數，則執行該應用程式的編輯器、IDE 或殼層必須先關閉再重新載入後，才能存取該變數。 這些方法稍後會在本快速入門中建立。

[!code-csharp[Create variables to hold the Personalizer resource key and endpoint values found in the Azure portal.](~/samples-personalizer/quickstarts/csharp/PersonalizerExample/Program.cs?name=classVariables)]

## <a name="create-a-personalizer-client"></a>建立個人化工具用戶端

接下來，建立可傳回個人化工具用戶端的方法。 方法的參數是 `PERSONALIZER_RESOURCE_ENDPOINT`，而 ApiKey 是 `PERSONALIZER_RESOURCE_KEY`。

[!code-csharp[Create the Personalizer client](~/samples-personalizer/quickstarts/csharp/PersonalizerExample/Program.cs?name=authorization)]

## <a name="get-food-items-as-rankable-actions"></a>取得食物項目作為可排名的動作

動作代表您想要讓個人化工具進行排名的內容選擇。 將下列方法新增至 Program 類別，以代表要排名的一組動作。

[!code-csharp[Food items as actions](~/samples-personalizer/quickstarts/csharp/PersonalizerExample/Program.cs?name=createAction)]

## <a name="get-user-preferences-for-context"></a>取得內容的使用者喜好設定

將下列方法新增至 [程式] 類別，以從命令列取得一天時間和目前食物喜好的使用者輸入。 在為動作排名時，這些方法會作為內容。

[!code-csharp[Present time out day preference to the user](~/samples-personalizer/quickstarts/csharp/PersonalizerExample/Program.cs?name=createUserFeatureTimeOfDay)]

[!code-csharp[Present food taste preference to the user](~/samples-personalizer/quickstarts/csharp/PersonalizerExample/Program.cs?name=createUserFeatureTastePreference)]

這兩個方法都會使用 `GetKey` 方法從命令列讀取使用者的選取。 

[!code-csharp[Read user's choice from the command line](~/samples-personalizer/quickstarts/csharp/PersonalizerExample/Program.cs?name=readCommandLine)]

## <a name="create-the-learning-loop"></a>建立學習迴圈

個人化工具學習迴圈是排名和獎勵呼叫的循環。 在本快速入門中，用於個人化內容的每個排名呼叫後面都會接著獎勵呼叫，以告訴個人化工具該服務在內容排名上的成效。 

在程式的 `main` 方法中，下列程式碼會在命令列上進行詢問使用者喜好的循環迴圈，並將該資訊傳送至個人化工具以進行排名，然後向客戶顯示已排名的選取項目，讓他們從清單中選擇，接著傳送獎勵給個人化工具，告知服務在排名選取項目上的成效為何。

[!code-csharp[Learning loop](~/samples-personalizer/quickstarts/csharp/PersonalizerExample/Program.cs?name=mainLoop)]

新增下列方法，以在執行程式碼檔案之前，[取得內容選項](#get-food-items-as-rankable-actions)：

* `GetActions`
* `GetUsersTimeOfDay`
* `GetUsersTastePreference`
* `GetKey`

## <a name="request-a-rank"></a>要求排名

為了完成排名要求，程式會詢問使用者的喜好，以建立內容選擇的 `currentContent`。 程式可以建立從排名中排除的內容，如 `excludeActions` 所示。 排名要求需要 actions、currentCoNtext、excludeActions 和唯一排名事件識別碼 (作為 GUID)，才能接收排名的回應。 

本快速入門有一天時間和使用者食物喜好的簡單關係特性。 在生產系統中，判斷和[評估](concept-feature-evaluation.md)[動作和特性](concepts-features.md)可能不是簡單的事。  

[!code-csharp[The Personalizer learning loop ranks the request.](~/samples-personalizer/quickstarts/csharp/PersonalizerExample/Program.cs?name=rank)]

## <a name="send-a-reward"></a>傳送獎勵

若要完成獎勵要求，程式會從命令列取得使用者的選取項目，將數值指派給每個選取項目，然後將唯一的排名事件識別碼和數值傳送給獎勵方法。

本快速入門會指派簡單的數字作為獎勵，也就是零或 1。 在生產系統中，視您的特定需求而定，判斷要傳送給[獎勵](concept-rewards.md)呼叫的時機和內容可能不是簡單的事。 

[!code-csharp[The Personalizer learning loop ranks the request.](~/samples-personalizer/quickstarts/csharp/PersonalizerExample/Program.cs?name=reward)]

## <a name="run-the-program"></a>執行程式

從應用程式目錄使用 dotnet `run` 命令來執行應用程式。

```console
dotnet run
```

![快速入門程式會詢問幾個問題以收集使用者偏好 (稱為特性)，然後提供最佳的動作。](media/csharp-quickstart-commandline-feedback-loop/quickstart-program-feedback-loop-example.png)

[本快速入門的原始程式碼](https://github.com/Azure-Samples/cognitive-services-personalizer-samples/blob/master/quickstarts/csharp/PersonalizerExample/Program.cs)可在個人化工具範例 GitHub 存放庫中取得。

## <a name="clean-up-resources"></a>清除資源

如果您想要清除和移除認知服務訂用帳戶，則可以刪除資源或資源群組。 刪除資源群組也會刪除其關聯的任何其他資源。

* [入口網站](../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
>[個人化工具的運作方式](how-personalizer-works.md)

* [什麼是個人化工具？](what-is-personalizer.md)
* [個人化工具可以應用在何處？](where-can-you-use-personalizer.md)
* [疑難排解](troubleshooting.md)
