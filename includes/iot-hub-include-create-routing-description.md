---
title: 包含檔案
description: 包含檔案
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: include
ms.date: 03/05/2019
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: b640a3cb9382ad72bb48e06c6a7074c96409e2e4
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/18/2019
ms.locfileid: "67174600"
---
<!-- description of message routing used in the Azure CLI, PowerShell, and RM routing articles.-->

您將根據由模擬裝置附加至訊息的屬性，將訊息路由至不同的資源。 沒有自訂路由的訊息會傳送至預設的端點 (訊息/事件)。 在下一個教學課程中，您會將訊息傳送至 IoT 中樞，並看看這些訊息如何路由傳送至不同的目的地。

|value |結果|
|------|------|
|level="storage" |寫入至 Azure 儲存體。|
|level="critical" |寫入至服務匯流排佇列。 邏輯應用程式會從佇列擷取訊息，並使用 Office 365 以電子郵件傳送訊息。|
|預設值 |使用 Power BI 顯示此資料。|

第一個步驟是設定要作為資料路由傳送目的地的端點。 第二個步驟是設定會使用該端點的訊息路由。 設定好路由之後，您便可以在入口網站中檢視端點和訊息路由。