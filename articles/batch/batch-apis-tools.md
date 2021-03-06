---
title: 適用於開發人員的 API 和工具 - Azure Batch | Microsoft Docs
description: 了解可搭配 Azure Batch 服務用來開發解決方案的 API 和工具。
services: batch
author: LauraBrenner
manager: evansma
ms.service: batch
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: labrenne
ms.custom: seodec18
ms.openlocfilehash: 00d2a74946957f690979eec1d3a03a9b766299d8
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/13/2020
ms.locfileid: "79252320"
---
# <a name="overview-of-batch-apis-and-tools"></a>Batch API 和工具的概觀

使用 Azure Batch 處理平行工作負載通常會透過其中一個 Batch API，以程式設計方式進行。 用戶端應用程式或服務可使用 Batch API 來與 Batch 服務進行通訊。 Batch API 可讓您建立和管理計算節點 (虛擬機器或雲端服務) 集區。 您可以接著排程要在這些節點上執行的作業及工作。 

您可以為組織有效率地處理大量工作負載，或提供前端服務給客戶，讓他們可以在一個、數百個或甚至數千個節點上，依需要或依排程執行作業和工作。 您也可以在 [Azure Data Factory](../data-factory/transform-data-using-dotnet-custom-activity.md?toc=%2fazure%2fbatch%2ftoc.json)之類的工具所管理的大型工作流程中使用 Azure Batch。

> [!TIP]
> 當您準備鑽研 Batch API 以深入了解它所提供的功能時，請參閱 [適用於開發人員的 Batch 功能概觀](batch-api-basics.md)。
> 
> 

## <a name="azure-accounts-for-batch-development"></a>用於 Batch 開發的 Azure 帳戶
當您開發 Batch 解決方案時，您將在 Azure 訂用帳戶中使用下列帳戶：

* **Batch 帳戶** - Azure Batch 資源 (包括集區、計算節點、作業和工作) 都與 Azure [Batch 帳戶](batch-api-basics.md#account)相關聯。 當您的應用程式對 Batch 服務提出要求時，可以使用 Azure Batch 帳戶名稱、帳戶的 URL 及存取金鑰或 Azure Active Directory 權杖，來驗證此要求。 您可以在 Azure 入口網站中或以程式設計方式[建立 Batch 帳戶](batch-account-create-portal.md)。
* **儲存體帳戶**-Batch 包含內建支援，可在[Azure 儲存體][azure_storage]中使用檔案。 幾乎每個 Batch 案例都會使用 Azure Blob 儲存體，來預備工作執行的程式及其處理的資料，以及儲存其產生的輸出資料。 如需 Batch 中的儲存體帳戶選項，請參閱 [Batch 功能概觀](batch-api-basics.md#azure-storage-account)。

## <a name="batch-service-apis"></a>Batch 服務 API

應用程式和服務可以發出直接的 REST API 呼叫，或使用一或多個下列用戶端程式庫來執行和管理 Azure Batch 工作負載。

| API | API 參考資料 | 下載 | 教學課程 | 程式碼範例 | 其他資訊 |
| --- | --- | --- | --- | --- | --- |
| **Batch REST** |[docs.microsoft.com][batch_rest] |N/A |- |- | [支援的版本](/rest/api/batchservice/batch-service-rest-api-versioning) |
| **Batch .NET** |[docs.microsoft.com][api_net] |[NuGet][api_net_nuget] |[教學課程](tutorial-parallel-dotnet.md) |[GitHub][api_sample_net] | [版本資訊](https://aka.ms/batch-net-dataplane-changelog) |
| **Batch Python** |[docs.microsoft.com][api_python] |[PyPI][api_python_pypi] |[教學課程](tutorial-parallel-python.md)|[GitHub][api_sample_python] | [讀我檔案](https://github.com/Azure/azure-sdk-for-python/blob/master/doc/batch.rst) |
| **Batch Node.js** |[docs.microsoft.com][api_nodejs] |[npm][api_nodejs_npm] |[教學課程](batch-nodejs-get-started.md) |- | [讀我檔案](https://github.com/Azure/azure-sdk-for-node/tree/master/lib/services/batch) |
| **Batch Java** |[docs.microsoft.com][api_java] |[Maven][api_java_jar] |- |[讀我檔案][api_sample_java] | [讀我檔案](https://github.com/Azure/azure-batch-sdk-for-java)|

## <a name="batch-management-apis"></a>Batch 管理 API

適用於 Batch 的 Azure Resource Manager API 會提供 Batch 帳戶的程式設計存取方式。 使用這些 API，您可以透過 Microsoft.Batch 提供者，以程式設計方式管理 Batch 帳戶、配額、應用程式套件及其他資雲。  

| API | API 參考資料 | 下載 | 教學課程 | 程式碼範例 |
| --- | --- | --- | --- | --- |
| **Batch 管理 REST** |[docs.microsoft.com][api_rest_mgmt] |N/A |- |[GitHub](https://github.com/Azure-Samples/batch-dotnet-manage-batch-accounts) |
| **Batch Management .NET** |[docs.microsoft.com][api_net_mgmt] |[NuGet][api_net_mgmt_nuget] | [教學課程](batch-management-dotnet.md) |[GitHub][api_sample_net] |
| **Batch 管理 Python** |[docs.microsoft.com][api_python_mgmt] |[PyPI][api_python_mgmt_pypi] |- |- |
| **Batch 管理 Node.js** |[docs.microsoft.com][api_nodejs_mgmt] |[npm][api_nodejs_mgmt_npm] |- |- | 
| **Batch 管理 Java** |- |[Maven][api_java_mgmt_jar] |- |- |
## <a name="batch-command-line-tools"></a>Batch 命令列工具

這些命令列工具可提供與 Batch 服務和 Batch 管理 API 相同的功能︰ 

* [Batch PowerShell Cmdlet][batch_ps]： [Azure PowerShell](/powershell/azure/overview)模組中的 Azure Batch Cmdlet 可讓您使用 PowerShell 來管理 Batch 資源。
* [Azure CLI](/cli/azure)：Azure CLI 是跨平台工具組，可提供用來與許多 Azure 服務 (包括 Batch 服務和 Batch 管理服務) 互動的殼層命令。 如需搭配使用 Azure CLI 與 Batch 的詳細資訊，請參閱[使用 Azure CLI 管理 Batch 資源](batch-cli-get-started.md)。

## <a name="other-tools-for-application-development"></a>其他用於應用程式開發的工具

以下是其他一些可能有助於建置及偵錯 Batch 應用程式和服務的工具︰

* [Azure 入口網站][portal]：您可以在 Azure 入口網站中建立、監視和刪除 Batch 集區、作業和工作。 您可以在執行作業時檢視上述和其他資源的狀態資訊，甚至從您集區中的計算節點下載檔案。 例如，您可以在進行疑難排解時下載失敗的工作 `stderr.txt`。 您也可以下載可用來登入計算節點的遠端桌面 (RDP) 檔案。
* [Azure Batch Explorer][batch_labs]： Batch Explorer （之前稱為 BatchLabs）是免費、功能豐富、獨立用戶端的工具，可協助建立、偵測和監視 Azure Batch 應用程式。 下載適用於 Mac、Linux 或 Windows 的[安裝套件](https://azure.github.io/BatchExplorer/)。
* [Azure Batch Shipyard](https://github.com/Azure/batch-shipyard)： Batch Shipyard 是一種工具，可協助您在 Azure Batch 上布建、執行和監視以容器為基礎的批次處理和 HPC 工作負載。
* [Azure 儲存體總管][storage_explorer]：雖然不是嚴格的 Azure Batch 工具，但儲存體總管是您在開發和偵測 Batch 解決方案時的另一個重要工具。

## <a name="additional-resources"></a>其他資源

- 若要了解如何記錄來自 Batch 應用程式的事件，請參閱[記錄事件以便對 Batch 解決方案進行診斷評估和監視](batch-diagnostics.md)。 如需 Batch 服務所引發之事件的參考，請參閱 [Batch Analytics](batch-analytics.md)。
- 如需計算節點環境變數的詳細資訊，請參閱 [Azure Batch 計算節點環境變數](batch-compute-node-environment-variables.md)。

## <a name="next-steps"></a>後續步驟

* 請參閱 [適用於開發人員的 Batch 功能概觀](batch-api-basics.md)，這是任何準備使用 Batch 的人員不可或缺的資訊。 本文包含 Batch 服務資源 (例如集區、節點、作業和工作) 的詳細資訊，以及在建置 Batch 應用程式時可使用的許多 API 功能。
* [開始使用適用於 .NET 的 Azure Batch 程式庫](tutorial-parallel-dotnet.md) ，了解如何使用 C# 和 Batch .NET 程式庫，透過一般的批次工作流程來執行簡單的工作負載。 也會提供 [Python 版本](tutorial-parallel-python.md)和 [Node.js 教學課程](batch-nodejs-get-started.md)。
* 下載[GitHub 上的程式碼範例][github_samples]，瞭解C#和 Python 如何與 Batch 進行互動，以排程和處理範例工作負載。

[azure_storage]: https://azure.microsoft.com/services/storage/
[api_java]: /java/api/overview/azure/batch
[api_java_mgmt]: /java/api/overview/azure/batch/managementapi
[api_java_jar]: https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-batch%22
[api_java_mgmt_jar]: https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-batch%22
[api_net]: /dotnet/api/overview/azure/batch/
[api_net_nuget]: https://www.nuget.org/packages/Microsoft.Azure.Batch/
[api_rest_mgmt]: /rest/api/batchmanagement/
[api_net_mgmt]: /dotnet/api/overview/azure/batch/management
[api_net_mgmt_nuget]: https://www.nuget.org/packages/Microsoft.Azure.Management.Batch/
[api_nodejs]: /javascript/api/overview/azure/batch/client
[api_nodejs_mgmt]: /javascript/api/overview/azure/batch/management
[api_nodejs_npm]: https://www.npmjs.com/package/azure-batch
[api_nodejs_mgmt_npm]: https://www.npmjs.com/package/azure-arm-batch
[api_python]: /python/api/overview/azure/batch/client
[api_python_mgmt]: /python/api/overview/azure/batch/management
[api_python_pypi]: https://pypi.python.org/pypi/azure-batch
[api_python_mgmt_pypi]: https://pypi.python.org/pypi/azure-mgmt-batch
[api_sample_net]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp
[api_sample_python]: https://github.com/Azure/azure-batch-samples/tree/master/Python/Batch
[api_sample_java]: https://github.com/Azure/azure-batch-samples/tree/master/Java/
[batch_ps]: /powershell/module/az.batch/
[batch_rest]: /rest/api/batchservice/
[free_account]: https://azure.microsoft.com/free/
[github_samples]: https://github.com/Azure/azure-batch-samples
[msdn_benefits]: https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/
[batch_labs]: https://azure.github.io/BatchExplorer/
[storage_explorer]: https://storageexplorer.com/
[portal]: https://portal.azure.com
