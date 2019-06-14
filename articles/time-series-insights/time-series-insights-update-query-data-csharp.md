---
title: 查詢資料從 Azure 時間序列深入解析預覽環境使用C#程式碼 |Microsoft Docs
description: 本文說明如何編製以 C# (c-sharp) .NET 語言撰寫的自訂應用程式，以從 Azure 時間序列深入解析環境查詢資料。
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: dpalled
manager: cshankar
reviewer: jasonwhowell, kfile, tsidocs
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 05/09/2019
ms.custom: seodec18
ms.openlocfilehash: fc5f35aedd52e206433afb0f556bc1cde8296232
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "66237484"
---
# <a name="query-data-from-the-azure-time-series-insights-preview-environment-using-c"></a>從 Azure 時間序列深入解析預覽環境使用的查詢資料C#

這個C#範例示範如何從 Azure 時間序列深入解析預覽環境查詢資料。

範例會顯示查詢 API 使用方式的數個基本範例︰

1. 在準備步驟中，透過 Azure Active Directory API 取得存取權杖。 在每個「查詢」API 要求的 `Authorization` 標頭中傳遞此權杖。 若要了解如何設定非互動式應用程式，請參閱[驗證與授權](time-series-insights-authentication-and-authorization.md)。 此外，請務必正確設定在此範例開頭定義的所有常數。
1. 取得使用者可存取的環境清單。 挑選其中一個環境作為使用環境，並針對此環境查詢進一步資料。
1. 在 HTTPS 要求的範例中，要求感興趣環境的可用性資料。
1. 在 Web 通訊端要求的範例中，要求感興趣環境的事件彙總資料。 要求整個可用性時間範圍內的資料。

> [!NOTE]
> 此程式碼範例也會提供在[ https://github.com/Azure-Samples/Azure-Time-Series-Insights ](https://github.com/Azure-Samples/Azure-Time-Series-Insights/tree/master/csharp-tsi-preview-sample)。

## <a name="c-example"></a>C# 範例

[!code-csharp[csharpquery-example](~/samples-tsi/csharp-tsi-preview-sample/DataPlaneClientSampleApp/Program.cs)]

> [!NOTE]
> 上述程式碼範例可以執行而不需變更預設環境的值。

## <a name="next-steps"></a>後續步驟

- 若要深入了解查詢，請閱讀[查詢 API 參考](/rest/api/time-series-insights/preview-query)。

- 如何讀取到[JavaScript 單一頁面應用程式連線](tutorial-create-tsi-sample-spa.md)至時間序列深入解析。