---
title: Azure 應用程式 Insights 資料模型-追蹤遙測
description: 追蹤遙測的 Application Insights 資料模型
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 04/25/2017
ms.reviewer: sergkanz
ms.openlocfilehash: 6e188039a86f4c655df3098be1d769668dcf3571
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/25/2019
ms.locfileid: "75407127"
---
# <a name="trace-telemetry-application-insights-data-model"></a>追蹤遙測：Application Insights 資料模型

追蹤遙測 (在 [Application Insights](../../azure-monitor/app/app-insights-overview.md) 中) 代表以文字搜尋的 `printf` 樣式追蹤陳述式。 `Log4Net`、`NLog`和其他以文字為基礎的記錄檔項目會轉譯成此類型的執行個體。 追蹤沒有作為擴充性的度量。

## <a name="message"></a>訊息

追蹤訊息。

最大長度︰32768 個字元

## <a name="severity-level"></a>嚴重性等級

追蹤嚴重性層級。 值可為 `Verbose`、`Information`、`Warning`、`Error`、`Critical`。

## <a name="custom-properties"></a>自訂屬性

[!INCLUDE [application-insights-data-model-properties](../../../includes/application-insights-data-model-properties.md)]

## <a name="next-steps"></a>後續步驟

- [在 Application Insights 中探索 .NET 追蹤記錄](../../azure-monitor/app/asp-net-trace-logs.md)。
- [在 Application Insights 中探索 Java 追蹤記錄](../../azure-monitor/app/java-trace-logs.md)。
- 如需 Application Insights 類型和資料模型，請參閱[資料模型](data-model.md)。
- [撰寫自訂追蹤遙測](../../azure-monitor/app/api-custom-events-metrics.md#tracktrace)
- 查看 Application Insights 支援的[平台](../../azure-monitor/app/platforms.md)。
