---
author: diberry
ms.author: diberry
ms.service: cognitive-services
ms.topic: include
ms.date: 01/02/2019
ms.openlocfilehash: 2f5b03dd0170da9a9869183d7a412688509525ef
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/18/2019
ms.locfileid: "67174247"
---
`ApplicationInsights` 設定可讓您將 [Azure Application Insights](https://docs.microsoft.com/azure/application-insights) 遙測支援新增至容器。 Application Insights 可提供深入容器監視。 您可輕鬆監視容器的可用性、效能和使用情形。 您也可以快速識別並診斷容器中的錯誤。

下表說明 `ApplicationInsights` 區段下所支援的組態設定。

|必要項| 名稱 | 数据类型 | 描述 |
|--|------|-----------|-------------|
|否| `InstrumentationKey` | String | Application Insights 執行個體的檢測金鑰，容器的遙測資料會傳送到這裡。 如需詳細資訊，請參閱 [ASP.NET Core 的 Application Insights](https://docs.microsoft.com/azure/application-insights/app-insights-asp-net-core)。 <br><br>範例：<br>`InstrumentationKey=123456789`|

