---
title: Azure Functions 執行階段版本概觀
description: Azure Functions 支援多個執行階段版本。 了解其間的差異以及如何選擇最適合您的版本。
ms.topic: conceptual
ms.date: 10/10/2019
ms.openlocfilehash: 53da5869b4768c95fd225fb15db60f4301e537d4
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/20/2019
ms.locfileid: "74226543"
---
# <a name="azure-functions-runtime-versions-overview"></a>Azure Functions 執行階段版本概觀

Azure Functions 執行時間的主要版本與執行時間所依據的 .NET 版本相關。 下表指出執行時間的目前版本、發行層級，以及相關的 .NET 版本。 

| 執行階段版本 | 發行層級<sup>1</sup> | .NET 版本 | 
| --------------- | ------------- | ------------ |
| 3.x  | preview | .NET Core 3。x | 
| 2.x | GA | .NET Core 2.2 |
| 1.x | GA<sup>2</sup> | .NET Framework 4.6<sup>3</sup> |

<sup>1</sup>GA 版本支援生產案例。   
<sup>2</sup>1.x 版處於維護模式。 只有在較新的版本中才會提供增強功能。   
<sup>3</sup>僅支援在 Azure 入口網站中或在 Windows 電腦本機上進行開發。

>[!NOTE]  
> 2\.x 版的函式執行時間處於預覽階段，不支援生產環境。 如需有關嘗試3.x 版的詳細資訊，請參閱[此公告](https://dev.to/azure/develop-azure-functions-using-net-core-3-0-gcm)。

本文會詳細說明各種版本之間的一些差異、如何建立每個版本，以及如何變更版本。

## <a name="languages"></a>語言

從2.x 版開始，執行時間會使用語言擴充性模型，而且函數應用程式中的所有函式都必須共用相同的語言。 建立應用程式時，會選擇函式應用程式中的函式語言，並在[\_WORKER\_運行](functions-app-settings.md#functions_worker_runtime)時間設定的函式中維護。 

Azure Functions 1.x 實驗語言無法使用新模型，因此在2.x 中不支援。 下表指出每個執行階段版本目前支援的程式設計語言。

[!INCLUDE [functions-supported-languages](../../includes/functions-supported-languages.md)]

如需詳細資訊，請參閱[支援的語言](supported-languages.md)。

## <a name="creating-1x-apps"></a>在特定版本上執行

根據預設，在 Azure 入口網站和 Azure CLI 中建立的函數應用程式會設定為2.x 版。 可能的話，您應該使用這個執行階段版本。 如有需要，您仍然可以在 1.x 版執行階段上執行函數應用程式。 您只能在建立函數應用程式之後，但在新增任何函式之前，變更執行階段版本。 若要了解如何將執行階段版本固定在 1.x，請參閱[檢視及更新目前的執行階段版本](set-runtime-version.md#view-and-update-the-current-runtime-version)。

您也可以升級至2.x 版的執行時間（預覽版）。 如果您需要能夠在 .NET Core 3.x 上執行函數，請執行此動作。 若要瞭解如何升級至3.x，請參閱[查看及更新目前的執行階段版本](set-runtime-version.md#view-and-update-the-current-runtime-version)。

## <a name="migrating-from-1x-to-later-versions"></a>從1.x 遷移至更新版本

您可以選擇將撰寫成使用 1.x 版執行階段的現有應用程式移轉成改用 2.x 版。 您所需進行的大多數變更都與語言執行階段有關，例如 .NET Framework 4.7 與 .NET Core 2 之間的 C# API 變更。 您也必須確認您的程式碼和程式庫與所選語言執行階段相容。 最後，請務必記下下面所強調觸發程序、繫結及功能方面的所有變更。 為獲得最佳移轉結果，您應該為 2.x 版建立新函數應用程式，然後將現有的 1.x 版函式程式碼移植到新應用程式。  

### <a name="changes-in-triggers-and-bindings"></a>觸發程序與繫結中的變更

2\.x 版需要您針對應用程式中函式所使用的特定觸發程序和繫結安裝延伸模組。 唯一的例外是 HTTP 和計時器觸發程序，這兩者不需要延伸模組。  如需詳細資訊，請參閱[註冊及安裝繫結延伸模組](./functions-bindings-register.md)。

`function.json` 或函式的屬性在版本之間也有一些變更。 例如，「事件中樞」的 `path` 屬性現在是 `eventHubName`。 如需每個繫結的文件連結，請參閱[現有的繫結表格](#bindings)。

### <a name="changes-in-features-and-functionality"></a>功能方面的變更

在新版本中已移除、更新或取代一些功能。 此節詳細說明在您使用 1.x 版之後，在 2.x 版中會看到的變更。

2\.x 版中做了下列變更：

* 用於呼叫 HTTP 端點的金鑰一律會以加密形式儲存在 Azure Blob 儲存體中。 在 1.x 中，金鑰預設是儲存在 Azure 檔案儲存體中。 將應用程式從 1.x 版升級至 2.x 版時，會重設檔案儲存體中的現有秘密。

* 2\.x 版執行階段並未內建對 Webhook 提供者的支援。 進行此變更是為了提升效能。 您仍然可以使用 HTTP 觸發程序作為 Webhook 的端點。

* 主機設定檔 (host.json) 應該空白或含有 `"version": "2.0"` 字串。

* 為了改進監視功能，已將入口網站中使用 [`AzureWebJobsDashboard`](functions-app-settings.md#azurewebjobsdashboard) 設定的 WebJobs 儀表板，取代成使用 [`APPINSIGHTS_INSTRUMENTATIONKEY`](functions-app-settings.md#appinsights_instrumentationkey) 設定的 Azure Application Insights。 如需詳細資訊，請參閱[監視 Azure Functions](functions-monitoring.md)。

* 函數應用程式中的所有函式都必須共用相同語言。 當您建立函數應用程式時，必須為應用程式選擇執行階段堆疊。 執行階段堆疊會由應用程式設定中的 [`FUNCTIONS_WORKER_RUNTIME`](functions-app-settings.md#functions_worker_runtime) 值指定。 新增此需求是為了改善使用量和啟動時間。 在本機進行開發時，您必須在 [local.settings.json 檔案](functions-run-local.md#local-settings-file)中也包含此設定。

* App Service 方案中函式的預設逾時已變更為 30 分鐘。 您可以藉由在 host.json 中使用 [functionTimeout](functions-host-json.md#functiontimeout) 設定，將逾時手動變更回無限制。

* 預設會針對使用情況方案函式實作 HTTP 並行節流，預設值為每一執行個體 100 個並行要求。 您可以在 host.json 檔案中的 [`maxConcurrentRequests`](functions-host-json.md#http) 設定中變更此值。

* 由於 [.NET Core 限制](https://github.com/Azure/azure-functions-host/issues/3414)的緣故，已移除對 F# 指令碼 (.fsx) 函式的支援。 仍然支援已編譯的 F# 函式 (.fs)。

* 「事件方格」觸發程序 Webhook 的 URL 格式已變更為 `https://{app}/runtime/webhooks/{triggerName}`。

### <a name="migrating-a-locally-developed-application"></a>移轉在本機開發的應用程式

您可能有使用 1.x 版執行階段在本機開發的現有函數應用程式專案。 若要升級至 2.x 版，您應該針對 2.x 版建立一個本機函數應用程式專案，然後將現有的程式碼移植到新應用程式。 您可以手動更新現有的專案和程式碼，就像是一種「就地」升級。 不過，1.x 版與 2.x 版之間仍有一些其他您需要進行的改進。 例如，在 C# 中，偵錯物件已從 `TraceWriter` 變更為 `ILogger`。 藉由建立新的 2.x 版專案，您將從以最新 2.x 版範本為基礎的已更新函式開始著手。

#### <a name="visual-studio-runtime-versions"></a>Visual Studio 執行階段版本

在 Visual Studio 中，您會在建立專案時選取執行階段版本。 適用於 Visual Studio 的 Azure Functions 工具同時支援這兩個主要的執行階段版本。 根據專案設定進行偵錯和發佈時，會使用正確的版本。 版本設定會在 `.csproj` 檔案中的下列屬性中定義：

##### <a name="version-1x"></a>1\.x 版

```xml
<TargetFramework>net461</TargetFramework>
<AzureFunctionsVersion>v1</AzureFunctionsVersion>
```

##### <a name="version-2x"></a>2\.x 版

```xml
<TargetFramework>netcoreapp2.2</TargetFramework>
<AzureFunctionsVersion>v2</AzureFunctionsVersion>
```

當您進行專案偵錯或發佈時，會使用正確的執行階段版本。

#### <a name="vs-code-and-azure-functions-core-tools"></a>VS Code 與 Azure Functions Core Tools

[Azure Functions Core Tools](functions-run-local.md) 除了用於命令列開發之外，也會供適用於 Visual Studio Code 的 [Azure Functions 延伸模組](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions)使用。 若要針對 2.x 版進行開發，請安裝 2.x 版的 Core Tools。 1\.x 版開發需要 1.x 版的 Core Tools。 如需詳細資訊，請參閱[安裝 Azure Functions Core Tools](functions-run-local.md#install-the-azure-functions-core-tools)。

針對 Visual Studio Code 開發，您可能必須一併更新 `azureFunctions.projectRuntime` 的使用者設定，以符合所安裝工具的版本。  此設定也會更新函數應用程式建立期間所使用的範本和語言。

### <a name="changing-version-of-apps-in-azure"></a>在 Azure 中變更應用程式版本

[`FUNCTIONS_EXTENSION_VERSION`](functions-app-settings.md#functions_extension_version) 應用程式設定會控制 Azure 中所發佈應用程式所使用的 Functions 執行階段版本。 值為 `~2` 時，會以 2.x 版執行階段為目標，值為 `~1` 時，會以 1.x 版執行階段為目標。 請勿任意變更此設定，因為可能會需要變更您函式中的其他應用程式設定和程式碼。 若要了解將函數應用程式移轉至不同執行階段版本的建議方式，請參閱[如何設定 Azure Functions 執行階段目標版本](set-runtime-version.md)。

## <a name="bindings"></a>繫結

從2.x 版開始，執行時間會使用新的系結擴充性[模型](https://github.com/Azure/azure-webjobs-sdk-extensions/wiki/Binding-Extensions-Overview)，以提供下列優點：

* 支援第三方繫結延伸模組。

* 分開處理執行階段和繫結。 此變更可讓繫結延伸模組個別建立版本和發行。 舉例來說，您可以選擇升級至依賴較新版基礎 SDK 的延伸模組版本。

* 較輕便的執行環境，執行階段只會知道及載入使用中的繫結。

除了 HTTP 和計時器觸發程序之後，所有繫結都必須以明確方式新增至函數應用程式專案，或在入口網站中註冊。 如需詳細資訊，請參閱[註冊繫結延伸模組](./functions-bindings-expressions-patterns.md)。

下表顯示每個執行階段版本所支援的繫結。

[!INCLUDE [Full bindings table](../../includes/functions-bindings.md)]

[!INCLUDE [Timeout Duration section](../../includes/functions-timeout-duration.md)]

## <a name="next-steps"></a>後續步驟

如需詳細資訊，請參閱下列資源：

* [撰寫 Azure Functions 並在本機進行測試](functions-run-local.md)
* [如何設定 Azure Functions 執行階段目標版本](set-runtime-version.md)
* [版本資訊](https://github.com/Azure/azure-functions-host/releases)
