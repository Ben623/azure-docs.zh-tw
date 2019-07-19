---
title: Azure 狀態監視器 v2 API 參考:啟用檢測引擎 |Microsoft Docs
description: 狀態監視器 v2 API 參考。 啟用-InstrumentationEngine。 在不重新部署網站的情況下監視網站效能。 適用于內部部署、Vm 或 Azure 上裝載的 ASP.NET web 應用程式。
services: application-insights
documentationcenter: .net
author: MS-TimothyMothra
manager: alexklim
ms.assetid: 769a5ea4-a8c6-4c18-b46c-657e864e24de
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 04/23/2019
ms.author: tilee
ms.openlocfilehash: d4683a1cad5172f7104e745433bd141bcf36d56f
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/18/2019
ms.locfileid: "68326380"
---
# <a name="status-monitor-v2-api-enable-instrumentationengine"></a>狀態監視器 v2 API:啟用-InstrumentationEngine

本文說明的 Cmdlet 是[ApplicationMonitor PowerShell 模組](https://www.powershellgallery.com/packages/Az.ApplicationMonitor/)的成員。

## <a name="description"></a>描述

藉由設定一些登錄機碼來啟用檢測引擎。
重新開機 IIS, 變更才會生效。

檢測引擎可以補充 .NET Sdk 所收集的資料。
它會收集描述 managed 進程執行的事件和訊息。 這些事件和訊息包括相依性結果碼、HTTP 指令動詞和[SQL 命令文字](asp-net-dependencies.md#advanced-sql-tracking-to-get-full-sql-query)。

啟用檢測引擎:
- 您已經使用 Enable Cmdlet 啟用監視功能, 但未啟用檢測引擎。
- 您已使用 .NET Sdk 手動檢測應用程式, 並想要收集其他遙測。

> [!IMPORTANT] 
> 此 Cmdlet 需要具有系統管理員許可權的 PowerShell 會話。

> [!NOTE] 
> - 此 Cmdlet 會要求您審查並接受我們的授權及隱私權聲明。
> - 檢測引擎會增加額外的負荷, 並預設為關閉。

## <a name="examples"></a>範例

```powershell
PS C:\> Enable-InstrumentationEngine
```

## <a name="parameters"></a>參數

### <a name="-acceptlicense"></a>-AcceptLicense
**選用。** 使用此參數可接受無周邊安裝中的授權和隱私權聲明。

### <a name="-verbose"></a>-Verbose
**一般參數。** 使用此參數來輸出詳細記錄。

## <a name="output"></a>Output


#### <a name="example-output-from-successfully-enabling-the-instrumentation-engine"></a>成功啟用檢測引擎的範例輸出

```
Configuring IIS Environment for instrumentation engine...
Configuring registry for instrumentation engine...
```

## <a name="next-steps"></a>後續步驟

  檢視遙測：
 - [探索計量](../../azure-monitor/app/metrics-explorer.md)以監視效能和使用量。
- [搜尋事件和記錄](../../azure-monitor/app/diagnostic-search.md)以診斷問題。
- 使用[分析](../../azure-monitor/app/analytics.md)進行更先進的查詢。
- [建立儀表板](../../azure-monitor/app/overview-dashboard.md)。
 
 新增更多遙測：
 - [建立 web 測試](monitor-web-app-availability.md), 以確保您的網站保持上線。
- [新增 web 用戶端遙測](../../azure-monitor/app/javascript.md), 以查看來自網頁程式碼的例外狀況, 並啟用追蹤呼叫。
- [將 APPLICATION INSIGHTS SDK 新增至您的程式碼](../../azure-monitor/app/asp-net.md), 讓您可以插入追蹤和記錄呼叫。
 
 使用狀態監視器 v2 來執行更多動作:
 - 使用我們的指南進行狀態監視器 v2 的[疑難排解](status-monitor-v2-troubleshoot.md)。
 - [取得](status-monitor-v2-api-get-config.md)設定, 以確認已正確記錄您的設定。
 - [取得狀態](status-monitor-v2-api-get-status.md)以檢查監視。
