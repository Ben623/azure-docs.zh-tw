---
title: Azure 服務匯流排訊息計數 | Microsoft Docs
description: 擷取 Azure 服務匯流排訊息的計數。
services: service-bus-messaging
documentationcenter: ''
author: axisc
manager: timlt
editor: spelluru
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2019
ms.author: aschhab
ms.openlocfilehash: adfd8c5849cfee69805715378a3f56ec9f685b00
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "60403953"
---
# <a name="message-counters"></a>訊息計數器

您可以使用 Azure Resource Manager 和 .NET Framework SDK 中的服務匯流排 [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) API，擷取佇列和訂用帳戶中保留的訊息計數。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

您可以透過 PowerShell 取得計數，如下所示：

```powershell
(Get-AzServiceBusQueue -ResourceGroup mygrp -NamespaceName myns -QueueName myqueue).CountDetails
```

## <a name="message-count-details"></a>訊息計數詳細資料

得知作用中的訊息計數，有助於判斷佇列建置之待處理項目所需處理的資源是否比目前部署的資源還多。 [MessageCountDetails](/dotnet/api/microsoft.servicebus.messaging.messagecountdetails) 類別提供以下計數器詳細資料：

-   [ActiveMessageCount](/dotnet/api/microsoft.servicebus.messaging.messagecountdetails.activemessagecount#Microsoft_ServiceBus_Messaging_MessageCountDetails_ActiveMessageCount)：佇列或訂用帳戶中處於作用中狀態，而且可供傳遞的訊息。
-   [DeadLetterMessageCount](/dotnet/api/microsoft.servicebus.messaging.messagecountdetails.deadlettermessagecount#Microsoft_ServiceBus_Messaging_MessageCountDetails_DeadLetterMessageCount)：無效信件佇列中的訊息。
-   [ScheduledMessageCount](/dotnet/api/microsoft.servicebus.messaging.messagecountdetails.scheduledmessagecount#Microsoft_ServiceBus_Messaging_MessageCountDetails_ScheduledMessageCount)：已排程的狀態中的訊息。
-   [TransferDeadLetterMessageCount](/dotnet/api/microsoft.servicebus.messaging.messagecountdetails.transferdeadlettermessagecount#Microsoft_ServiceBus_Messaging_MessageCountDetails_TransferDeadLetterMessageCount)：無法傳送到另一個佇列或主題，而且已移動到傳送無效信件佇列的訊息。
-   [TransferMessageCount](/dotnet/api/microsoft.servicebus.messaging.messagecountdetails.transfermessagecount#Microsoft_ServiceBus_Messaging_MessageCountDetails_TransferMessageCount)：等待傳送到另一個佇列或主題的訊息。

如果應用程式想要依據佇列長度調整資源，它應該採用循序漸進的方式來進行。 取得訊息計數是在訊息代理程式中是項昂貴的作業，頻繁執行會直接對實體效能造成不良影響。

## <a name="next-steps"></a>後續步驟

若要深入了解服務匯流排傳訊，請參閱下列主題：

* [服務匯流排佇列、主題和訂用帳戶](service-bus-queues-topics-subscriptions.md)
* [開始使用服務匯流排佇列](service-bus-dotnet-get-started-with-queues.md)
* [如何使用服務匯流排主題和訂用帳戶](service-bus-dotnet-how-to-use-topics-subscriptions.md)
