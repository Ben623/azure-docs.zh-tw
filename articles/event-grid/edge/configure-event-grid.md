---
title: 設定-Azure Event Grid IoT Edge |Microsoft Docs
description: IoT Edge 上事件方格中的設定。
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 10/03/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: 841b5092775353bbe3340dbbd55610026f998a15
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/29/2020
ms.locfileid: "76846465"
---
# <a name="event-grid-configuration"></a>事件方格設定

事件方格提供許多可在每個環境中修改的設定。 下一節是所有可用選項及其預設值的參考。

## <a name="tls-configuration"></a>TLS 設定

若要深入瞭解用戶端驗證的一般資訊，請參閱[安全性和驗證](security-authentication.md)。 您可以在[這篇文章](configure-api-protocol.md)中找到其使用方式的範例。

| 屬性名稱 | 說明 |
| ---------------- | ------------ |
|`inbound__serverAuth__tlsPolicy`| 事件方格模組的 TLS 原則。 預設值為 [僅限 HTTPS]。
|`inbound__serverAuth__serverCert__source`| 事件方格模組用來進行 TLS 設定的伺服器憑證來源。 預設值為 IoT Edge。

## <a name="incoming-client-authentication"></a>傳入用戶端驗證

若要深入瞭解用戶端驗證的一般資訊，請參閱[安全性和驗證](security-authentication.md)。 您可以在[這篇文章](configure-client-auth.md)中找到範例。

| 屬性名稱 | 說明 |
| ---------------- | ------------ |
|`inbound__clientAuth__clientCert__enabled`| 開啟/關閉以憑證為基礎的用戶端驗證。 預設值為 true。
|`inbound__clientAuth__clientCert__source`| 驗證用戶端憑證的來源。 預設值為 IoT Edge。
|`inbound__clientAuth__clientCert__allowUnknownCA`| 允許自我簽署用戶端憑證的原則。 預設值為 true。
|`inbound__clientAuth__sasKeys__enabled`| 若要開啟/關閉以 SAS 金鑰為基礎的用戶端驗證。 預設值為 off。
|`inbound__clientAuth__sasKeys__key1`| 其中一個值，用來驗證傳入的要求。
|`inbound__clientAuth__sasKeys__key2`| 選擇性的第二個值，用來驗證傳入的要求。

## <a name="outgoing-client-authentication"></a>外寄用戶端驗證
若要深入瞭解用戶端驗證的一般資訊，請參閱[安全性和驗證](security-authentication.md)。 您可以在[這篇文章](configure-identity-auth.md)中找到範例。

| 屬性名稱 | 說明 |
| ---------------- | ------------ |
|`outbound__clientAuth__clientCert__enabled`| 若要開啟/關閉附加連出要求的身分識別憑證。 預設值為 true。
|`outbound__clientAuth__clientCert__source`| 用來抓取事件方格模組外寄憑證的來源。 預設值為 IoT Edge。

## <a name="webhook-event-handlers"></a>Webhook 事件處理常式

若要深入瞭解用戶端驗證的一般資訊，請參閱[安全性和驗證](security-authentication.md)。 您可以在[這篇文章](configure-webhook-subscriber-auth.md)中找到範例。

| 屬性名稱 | 說明 |
| ---------------- | ------------ |
|`outbound__webhook__httpsOnly`| 控制是否只允許 HTTPS 訂閱者的原則。 預設值為 true （僅限 HTTPS）。
|`outbound__webhook__skipServerCertValidation`| 用來控制是否要驗證訂閱者憑證的旗標。 預設值為 true。
|`outbound__webhook__allowUnknownCA`| 控制訂閱者是否可以出示自我簽署憑證的原則。 預設值為 true。 

## <a name="delivery-and-retry"></a>傳遞和重試

若要深入瞭解這項功能的一般資訊，請參閱[傳遞和重試](delivery-retry.md)。

| 屬性名稱 | 說明 |
| ---------------- | ------------ |
| `broker__defaultMaxDeliveryAttempts` | 傳遞事件的嘗試次數上限。 預設值為 30。
| `broker__defaultEventTimeToLiveInSeconds` | 存留時間（TTL），以秒為單位，如果未傳遞，則會捨棄事件。 預設值為**7200**秒

## <a name="output-batching"></a>輸出批次處理

若要深入瞭解這項功能的一般資訊，請參閱[傳遞和輸出批次處理](delivery-output-batching.md)。

| 屬性名稱 | 說明 |
| ---------------- | ------------ |
| `api__deliveryPolicyLimits__maxBatchSizeInBytes` | `ApproxBatchSizeInBytes` 旋鈕所允許的最大值。 預設值為 `1_058_576`。
| `api__deliveryPolicyLimits__maxEventsPerBatch` | `MaxEventsPerBatch` 旋鈕所允許的最大值。 預設值為 `50`。
| `broker__defaultMaxBatchSizeInBytes` | 只有在指定 `MaxEventsPerBatch` 時，傳遞要求大小上限。 預設值為 `1_058_576`。
| `broker__defaultMaxEventsPerBatch` | 只有在指定 `MaxBatchSizeInBytes` 時，要新增至批次的最大事件數目。 預設值為 `10`。

## <a name="metrics"></a>計量

若要瞭解如何在 IoT Edge 上搭配使用計量與事件方格，請參閱[監視主題和](monitor-topics-subscriptions.md)訂用帳戶

| 屬性名稱 | 說明 |
| ---------------- | ------------ |
| `metrics__reporterType` | 計量端點的報告類型。 預設值為 `none` 並停用計量。 將設定為 `prometheus` 會啟用 Prometheus 展示格式的計量。