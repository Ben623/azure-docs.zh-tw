---
title: Azure 監視器中的簡單記錄體驗（預覽） |Microsoft Docs
description: 簡單的記錄體驗可讓您在 Azure 監視器中建立基本查詢，而不需要直接與 KQL 互動。
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 09/12/2019
ms.author: bwren
ms.openlocfilehash: 323267dd47735ca54b84e47e6a55d1f2d14a0b06
ms.sourcegitcommit: 55f7fc8fe5f6d874d5e886cb014e2070f49f3b94
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/25/2019
ms.locfileid: "71262179"
---
# <a name="simple-logs-experience-in-azure-monitor-preview"></a>Azure 監視器中的簡單記錄體驗（預覽）
Azure 監視器提供使用 KQL 語言建立[記錄查詢](log-query-overview.md)的[豐富體驗](get-started-portal.md)。 您可能不需要 KQL 的完整功能，而且偏好簡單的基本查詢需求體驗。 簡單的記錄體驗可讓您建立基本查詢，而不需要直接與 KQL 互動。 您也可以使用簡單記錄做為 KQL 的學習工具，因為您需要更複雜的查詢。

> [!NOTE]
> 簡單的記錄檔目前只會實作為 Cosmos DB 和金鑰保存庫的測試。 請透過[User Voice](https://feedback.azure.com/forums/913690-azure-monitor)與 Microsoft 分享您的體驗，以協助我們判斷是否要擴充併發行這項功能。


## <a name="scope"></a>`Scope`
簡單的記錄體驗會從所選資源的*AzureDiagnostics*、 *AzureMetrics*和*AzureActivity*資料表中抓取資料。 

## <a name="using-simple-logs"></a>使用簡單記錄
使用[設定為在 Log Analytics 工作區中收集記錄的診斷設定](../platform/resource-logs-collect-storage.md)，流覽至您的 Azure 訂用帳戶中的任何 Cosmos DB 或 Key Vault。 按一下 [**監視**] 功能表中的 [**記錄**]，開啟簡單的記錄體驗。

![功能表](media/simple-logs/menu.png)

選取**欄位**和**運算子**，並指定比較的**值**。 按一下 **+** 並指定**和（或**）以新增其他準則。

![條件](media/simple-logs/criteria.png)

按一下 [**執行**] 以查看查詢結果。

## <a name="view-and-edit-kql"></a>查看和編輯 KQL
選取 [**查詢編輯器**]，以在完整的 Log Analytics 體驗中開啟簡單記錄查詢所產生的 KQL。 

![查詢編輯器](media/simple-logs/query-editor.png)

您可以直接編輯 KQL，並使用 Log Analytics 中的其他功能（例如篩選器）來進一步精簡您的結果。

![編輯 KQL](media/simple-logs/edit-kql.png)


## <a name="next-steps"></a>後續步驟

- 完成在[Azure 入口網站中使用 Log Analytics](get-started-portal.md)的教學課程。
- 完成[撰寫記錄查詢](get-started-portal.md)的教學課程。
