---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/04/2018
ms.author: glenga
ms.openlocfilehash: ac6b86b4ad8830bd08c9db28ac0027a5f048c3dd
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/08/2019
ms.locfileid: "74935925"
---
下表顯示 Azure Functions 執行時間的主要版本所支援的系結：

| Type | 1.x | 2.x 和更新版本<sup>1</sup> | 觸發程序 | 輸入 | 輸出 |
| ---- | :-: | :-: | :------: | :---: | :----: |
| [Blob 儲存體](../articles/azure-functions/functions-bindings-storage-blob.md)          |✔|✔|✔|✔|✔|
| [Cosmos DB](../articles/azure-functions/functions-bindings-documentdb.md)               |✔|✔|✔|✔|✔|
| [Event Grid](../articles/azure-functions/functions-bindings-event-grid.md)              |✔|✔|✔| | |
| [事件中樞](../articles/azure-functions/functions-bindings-event-hubs.md)              |✔|✔|✔| |✔|
| [HTTP & webhook](../articles/azure-functions/functions-bindings-http-webhook.md)             |✔|✔|✔| |✔|
| [IoT 中心](../articles/azure-functions/functions-bindings-event-iot.md)             |✔|✔|✔| |✔|
| [Microsoft Graph<br/>Excel 資料表](../articles/azure-functions/functions-bindings-microsoft-graph.md)   ||✔| |✔|✔|
| [Microsoft Graph<br/>OneDrive 檔案](../articles/azure-functions/functions-bindings-microsoft-graph.md) ||✔| |✔|✔|
| [Microsoft Graph<br/>Outlook 電子郵件](../articles/azure-functions/functions-bindings-microsoft-graph.md)  ||✔| | |✔|
| [Microsoft Graph<br/>事件](../articles/azure-functions/functions-bindings-microsoft-graph.md)         ||✔|✔|✔|✔|
| [Microsoft Graph<br/>驗證權杖](../articles/azure-functions/functions-bindings-microsoft-graph.md)    ||✔| |✔| |
| [行動應用程式](../articles/azure-functions/functions-bindings-mobile-apps.md)             |✔| | |✔|✔|
| [通知中樞](../articles/azure-functions/functions-bindings-notification-hubs.md) |✔|| | |✔|
| [佇列儲存體](../articles/azure-functions/functions-bindings-storage-queue.md)         |✔|✔|✔| |✔|
| [SendGrid](../articles/azure-functions/functions-bindings-sendgrid.md)                   |✔|✔| | |✔|
| [服務匯流排](../articles/azure-functions/functions-bindings-service-bus.md)             |✔|✔|✔| |✔|
| [SignalR](../articles/azure-functions/functions-bindings-signalr-service.md)             | |✔| |✔|✔|
| [資料表儲存體](../articles/azure-functions/functions-bindings-storage-table.md)         |✔|✔| |✔|✔|
| [計時器](../articles/azure-functions/functions-bindings-timer.md)                         |✔|✔|✔| | |
| [Twilio](../articles/azure-functions/functions-bindings-twilio.md)                       |✔|✔| | |✔|

<sup>1</sup>從版本2.x 執行時間開始，必須註冊 HTTP 和計時器以外的所有系結。 請參閱[註冊繫結延伸模組](../articles/azure-functions/functions-bindings-register.md)。
