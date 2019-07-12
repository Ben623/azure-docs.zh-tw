---
title: 將 Azure 活動資料連接至 Azure 的 Sentinel Preview |Microsoft Docs
description: 了解如何將 Azure 活動資料連接至 Azure 的 Sentinel。
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.assetid: 8c25baa8-b93b-41da-9e6c-15bb7b5c5511
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/07/2019
ms.author: rkarlin
ms.openlocfilehash: e329c8efd9b0e89f5f5eae41952cda9a45a95969
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/07/2019
ms.locfileid: "67620660"
---
# <a name="connect-data-from-azure-activity-log"></a>連接 Azure 活動記錄檔資料

> [!IMPORTANT]
> Azure Sentinel 目前為公開預覽狀態。
> 此預覽版本是在沒有服務等級協定的情況下提供，不建議用於生產工作負載。 可能不支援特定功能，或可能已經限制功能。 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

您可以從串流記錄[Azure 活動記錄檔](../azure-monitor/platform/activity-logs-overview.md)到 Azure Sentinel 只要按一下。 活動記錄是訂用帳戶記錄，可深入了解 Azure 中發生的訂用帳戶層級事件的進度。 所涵蓋的資料範圍從 Azure Resource Manager 作業資料到服務健康情況事件的更新。 您可以使用活動記錄檔，判斷 '內容、 對象和時間' 的任何寫入作業 (PUT、 POST、 DELETE)，您的訂用帳戶中的資源上。 您也可以了解作業的狀態和其他相關屬性。 活動記錄不包含讀取 (GET) 作業或作業之資源的使用傳統 /"RDFE"模型。 


## <a name="prerequisites"></a>必要條件

- 以全域系統管理員或安全性系統管理員權限的使用者


## <a name="connect-to-azure-activity-log"></a>連接到 Azure 活動記錄檔

1. 在 Azure Sentinel，選取**資料連接器**，然後按一下**Azure 活動記錄檔**圖格。

2. 在 [Azure 活動記錄檔] 窗格中，選取您想要串流處理至 Azure 的 Sentinel 的訂用帳戶。 

3. 按一下 **[連接]** 。

4. 若要使用 Log Analytics 中的 Azure 活動警示相關的結構描述，搜尋**AzureActivity**。


 

## <a name="next-steps"></a>後續步驟
在本文件中，您已了解如何連接到 Azure Sentinel 的 Azure 活動記錄檔。 若要深入了解 Azure Sentinel，請參閱下列文章：
- 了解如何[了解您的資料，與潛在的威脅](quickstart-get-visibility.md)。
- 開始[偵測威脅與 Azure Sentinel](tutorial-detect-threats.md)。
