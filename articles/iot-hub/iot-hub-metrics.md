---
title: 使用計量監視 Azure IoT 中樞 | Microsoft Docs
description: 如何使用 Azure IoT 中樞度量來評估和監視 IoT 中樞的整體健全狀況。
author: jlian
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/24/2019
ms.author: jlian
ms.openlocfilehash: ec8a00460b4a750339f929eb6879ac6eb63cac8e
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/13/2020
ms.locfileid: "79284430"
---
# <a name="understand-iot-hub-metrics"></a>了解 IoT 中樞計量

IoT 中樞度量提供更好的資料，讓您了解 Azure 訂用帳戶中各種 Azure 資源的狀態。 IoT 中樞度量可讓您評估 IoT 中樞服務及其連接之裝置的整體健全狀況。 提供給您的統計資料非常重要，因為它們可以協助您了解其 IoT 中樞的情況，並協助您不需要連絡 Azure 支援人員就解決根本問題。

預設會啟用計量。 您可以從 Azure 入口網站啟用 IoT 中樞計量。

> [!NOTE]
> 您可以使用 IoT 中樞計量來查看連線至 IoT 中樞之 IoT 隨插即用裝置的相關資訊。 IoT 隨插即用裝置是[iot 隨插即用公開預覽](../iot-pnp/overview-iot-plug-and-play.md)的一部分。

## <a name="how-to-view-iot-hub-metrics"></a>如何檢視 IoT 中樞計量

1. 建立 IoT 中樞。 您可以在[將遙測從裝置傳送到 IoT 中樞](quickstart-send-telemetry-dotnet.md)指南中找到有關如何建立 IoT 中樞的指示。

2. 開啟 IoT 中樞的刀鋒視窗。 按一下此處的 [計量]。
   
    ![顯示 [IoT 中樞資源] 頁面中 [度量] 選項之位置的螢幕擷取畫面](./media/iot-hub-metrics/enable-metrics-1.png)

3. 您可以在 [計量] 刀鋒視窗中檢視 IoT 中樞的計量，並建立計量的自訂檢視。 
   
    ![顯示 IoT 中樞 [度量] 頁面的螢幕擷取畫面](./media/iot-hub-metrics/enable-metrics-2.png)
    
4. 您可以藉由按一下 [診斷設定]，然後 [新增診斷設定]，選擇將計量資料傳送到事件中樞端點或 Azure 儲存體帳戶

   ![顯示 [診斷設定] 按鈕位置的螢幕擷取畫面](./media/iot-hub-metrics/enable-metrics-3.png)

## <a name="iot-hub-metrics-and-how-to-use-them"></a>IoT 中樞度量及其使用方式

IoT 中樞提供數個度量，以讓您概略了解中樞的健全狀況和所連線裝置的總數。 您可以結合多個度量的資訊，以便更清楚地了解 IoT 中樞的狀態。 下表描述每個 IoT 中樞所追蹤的度量，以及每個度量與 IoT 中樞整體狀態的關聯。

|計量|計量顯示名稱|單位|彙總類型|描述|維度|
|---|---|---|---|---|---|
|d2c.telemetry.ingress.allProtocol|遙測訊息傳送嘗試|Count|總計|要嘗試傳送至您 IoT 中樞的裝置到雲端遙測訊息數目|None|
|d2c.telemetry.ingress.success|已傳送的遙測訊息|Count|總計|成功傳送至您 IoT 中樞的裝置到雲端遙測訊息數目|None|
|c2d.commands.egress.complete.success|已完成 C2D 訊息傳遞|Count|總計|裝置已成功完成的雲端到裝置訊息傳遞數|None|
|c2d.commands.egress.abandon.success|已放棄的 C2D 訊息|Count|總計|裝置放棄的雲端到裝置訊息數|None|
|c2d.commands.egress.reject.success|拒絕的 C2D 訊息|Count|總計|裝置拒絕的雲端到裝置訊息數目|None|
|C2DMessagesExpired|C2D 郵件已過期（預覽）|Count|總計|已過期的雲端到裝置訊息數目|None|
|devices.totalDevices|裝置總計 (已淘汰)|Count|總計|向 IoT 中樞註冊的裝置數目|None|
|devices.connectedDevices.allProtocol|連接的裝置 (已淘汰) |Count|總計|連接至 IoT 中樞的裝置數目|None|
|d2c.telemetry.egress.success|路由：已傳遞的遙測訊息|Count|總計|使用 IoT 中樞路由成功地將訊息傳遞到所有端點的次數。 如果將訊息路由至多個端點，這個值會為每一次成功傳遞加 1。 如果將訊息多次傳遞到同一個端點，這個值會為每一次成功傳遞加 1。|None|
|d2c.telemetry.egress.dropped|路由：已捨棄的遙測訊息 |Count|總計|IoT 中樞路由因為端點無效而捨棄訊息的次數。 這個值不會計算傳遞到後援路由的訊息，因為捨棄的訊息不會傳遞到那裡。|None|
|d2c.telemetry.egress.orphaned|路由：已遺棄的遙測訊息 |Count|總計|IoT 中樞路由因為訊息不符合任何路由規則 (包括後援規則) 而遺棄訊息的次數。 |None|
|d2c.telemetry.egress.invalid|路由：不相容的遙測訊息|Count|總計|IoT 中樞路由因為與端點不相容而無法傳遞訊息的次數。 這個值不包含重試次數。|None|
|d2c.telemetry.egress.fallback|路由：傳遞至後援的訊息|Count|總計|IoT 中樞路由傳遞訊息至與後援路由相關聯之端點的次數。|None|
|d2c.endpoints.egress.eventHubs|路由：傳遞至事件中樞的訊息|Count|總計|IoT 中樞路由成功將訊息傳遞至事件中樞端點的次數。|None|
|d2c.endpoints.latency.eventHubs|路由：事件中樞的訊息延遲|毫秒|Average|訊息輸入到 IoT 中樞與訊息輸入到事件中樞端點之間的平均延遲 (毫秒)。|None|
|d2c.endpoints.egress.serviceBusQueues|路由：傳遞至服務匯流排佇列的訊息|Count|總計|IoT 中樞路由成功將訊息傳遞至服務匯流排佇列端點的次數。|None|
|d2c.endpoints.latency.serviceBusQueues|路由：服務匯流排佇列的訊息延遲|毫秒|Average|訊息輸入到 IoT 中樞與遙測訊息輸入到服務匯流排佇列端點之間的平均延遲 (毫秒)。|None|
|d2c.endpoints.egress.serviceBusTopics|路由：傳遞至服務匯流排主題的訊息|Count|總計|IoT 中樞路由成功將訊息傳遞至服務匯流排主題端點的次數。|None|
|d2c.endpoints.latency.serviceBusTopics|路由：服務匯流排主題的訊息延遲|毫秒|Average|訊息輸入到 IoT 中樞與遙測訊息輸入到服務匯流排佇列端點之間的平均延遲 (毫秒)。|None|
|d2c.endpoints.egress.builtIn.events|路由：傳遞至訊息/事件的訊息|Count|總計|IoT 中樞路由成功將訊息傳遞至內建端點 (訊息/事件) 的次數。|None|
|d2c.endpoints.latency.builtIn.events|路由：訊息/事件的訊息延遲|毫秒|Average|訊息輸入到 IoT 中樞與遙測訊息輸入到內建端點 (訊息/事件) 之間的平均延遲 (毫秒)。|None|
|d2c.endpoints.egress.storage|路由：傳遞至儲存體的訊息|Count|總計|IoT 中樞路由成功將訊息傳遞至儲存體端點的次數。|None|
|d2c.endpoints.latency.storage|路由：儲存體的訊息延遲|毫秒|Average|訊息輸入到 IoT 中樞與遙測訊息輸入到儲存體端點之間的平均延遲 (毫秒)。|None|
|d2c.endpoints.egress.storage.bytes|路由：傳遞至儲存體的資料|位元組|總計|IoT 中樞路由傳遞至儲存體端點的資料量 (位元組)。|None|
|d2c.endpoints.egress.storage.blobs|路由：傳遞至儲存體的 BLOB|Count|總計|IoT 中樞路由將 BLOB 傳遞至儲存體端點的次數。|None|
|EventGridDeliveries|事件方格傳遞（預覽）|Count|總計|已發佈至事件方格的 IoT 中樞事件數目。 使用 [結果] 維度來取得成功和失敗的要求數目。 [事件種類] 維度會顯示事件的類型（ https://aka.ms/ioteventgrid)。|ResourceId<br/>Result<br/>EventType|
|EventGridLatency|事件方格延遲（預覽）|毫秒|Average|當事件發佈至事件方格時，從 Iot 中樞事件產生的平均延遲（毫秒）。 這個數位是所有事件種類之間的平均值。 使用 [事件種類] 維度來查看特定事件種類的延遲。|ResourceId<br/>EventType|
|d2c.twin.read.success|裝置的成功對應項讀取|Count|總計|裝置起始的所有成功對應項讀取的計數。|None|
|d2c.twin.read.failure|裝置的失敗對應項讀取|Count|總計|裝置起始的所有失敗對應項讀取的計數。|None|
|d2c.twin.read.size|裝置的對應項讀取回應大小|位元組|Average|裝置起始的所有成功對應項讀取的平均值、最小值和最大值。|None|
|d2c.twin.update.success|裝置的成功對應項更新|Count|總計|裝置起始的所有成功對應項更新的計數。|None|
|d2c.twin.update.failure|裝置的失敗對應項更新|Count|總計|裝置起始的所有失敗對應項更新的計數。|None|
|d2c.twin.update.size|裝置的對應項更新大小|位元組|Average|裝置起始的所有成功對應項更新的大小平均值、最小值和最大值。|None|
|c2d.methods.success|成功直接方法叫用|Count|總計|所有成功直接方法呼叫的計數。|None|
|c2d.methods.failure|失敗直接方法叫用|Count|總計|所有失敗直接方法呼叫的計數。|None|
|c2d.methods.requestSize|直接方法叫用的要求大小|位元組|Average|所有成功直接方法要求的平均值、最小值和最大值。|None|
|c2d.methods.responseSize|直接方法叫用的回應大小|位元組|Average|所有成功直接方法回應的平均值、最小值和最大值。|None|
|c2d.twin.read.success|後端的成功對應項讀取|Count|總計|後端起始的所有成功對應項讀取的計數。|None|
|c2d.twin.read.failure|後端的失敗對應項讀取|Count|總計|後端起始的所有失敗對應項讀取的計數。|None|
|c2d.twin.read.size|後端的對應項讀取回應大小|位元組|Average|後端起始的所有成功對應項讀取的平均值、最小值和最大值。|None|
|c2d.twin.update.success|後端的成功對應項更新|Count|總計|後端起始的所有成功對應項更新的計數。|None|
|c2d.twin.update.failure|後端的失敗對應項更新|Count|總計|後端起始的所有失敗對應項更新的計數。|None|
|c2d.twin.update.size|後端的對應項更新大小|位元組|Average|後端起始的所有成功對應項更新的大小平均值、最小值和最大值。|None|
|twinQueries.success|成功對應項查詢|Count|總計|所有成功對應項查詢的計數。|None|
|twinQueries.failure|失敗對應項查詢|Count|總計|所有失敗對應項查詢的計數。|None|
|twinQueries.resultSize|對應項查詢結果大小|位元組|Average|所有成功對應項查詢的結果大小平均值、最小值和最大值。|None|
|jobs.createTwinUpdateJob.success|成功建立的對應項更新作業|Count|總計|所有成功建立的對應項更新作業計數。|None|
|jobs.createTwinUpdateJob.failure|建立失敗的對應項更新作業|Count|總計|所有建立失敗的對應項更新作業計數。|None|
|jobs.createDirectMethodJob.success|成功建立的方法叫用作業|Count|總計|所有成功建立的直接方法叫用作業計數。|None|
|jobs.createDirectMethodJob.failure|建立失敗的方法叫用作業|Count|總計|所有建立失敗的直接方法叫用作業計數。|None|
|jobs.listJobs.success|成功呼叫列出作業|Count|總計|所有成功呼叫列出作業的計數。|None|
|jobs.listJobs.failure|呼叫列出作業失敗|Count|總計|所有呼叫列出作業失敗的計數。|None|
|jobs.cancelJob.success|成功取消作業|Count|總計|所有成功呼叫取消作業的計數。|None|
|jobs.cancelJob.failure|取消作業失敗|Count|總計|所有呼叫取消作業失敗的計數。|None|
|jobs.queryJobs.success|成功作業查詢|Count|總計|所有成功呼叫查詢作業的計數。|None|
|jobs.queryJobs.failure|失敗作業查詢|Count|總計|所有呼叫查詢作業失敗的計數。|None|
|jobs.completed|已完成的工作|Count|總計|所有已完成的作業計數。|None|
|jobs.failed|失敗作業|Count|總計|所有失敗作業計數。|None|
|d2c.telemetry.ingress.sendThrottle|節流錯誤數目|Count|總計|因裝置輸送量節流而導致的節流錯誤數目|None|
|dailyMessageQuotaUsed|已使用的訊息總數|Count|Average|今日已使用的總訊息數。 這是一個累計值，會在每天的 00:00 UTC 重設為零。|None|
|deviceDataUsage|裝置資料使用量總計|位元組|總計|傳輸至任何已連線至 IotHub 之裝置或從該裝置傳出的位元組數|None|
|deviceDataUsageV2|裝置資料使用量總計 (預覽)|位元組|總計|傳輸至任何已連線至 IotHub 之裝置或從該裝置傳出的位元組數|None|
|totalDeviceCount|裝置總計 (預覽)|Count|Average|向 IoT 中樞註冊的裝置數目|None|
|connectedDeviceCount|連接的裝置 (預覽)|Count|Average|連接至 IoT 中樞的裝置數目|None|
|組態|設定計量|Count|總計|設定作業的計量|None|

## <a name="next-steps"></a>後續步驟

現在您已了解 IoT 中樞度量的概觀，請循著下列連結來深入了解如何管理「Azure IoT 中樞」：

* [設定診斷記錄](iot-hub-monitor-resource-health.md)

若要進一步探索 IoT 中樞的功能，請參閱︰

* [IoT 中樞開發人員指南](iot-hub-devguide.md)

* [使用 Azure IoT Edge 將 AI 部署到 Edge 裝置](../iot-edge/tutorial-simulate-device-linux.md)
