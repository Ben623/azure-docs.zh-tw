---
title: 利用存留時間讓 Azure Cosmos DB 中的資料過期
description: Microsoft Azure Cosmos DB 可讓您利用 TTL 在一段時間後自動從系統清除文件。
author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/21/2019
ms.author: rimman
ms.reviewer: sngun
ms.openlocfilehash: 0b32665b09eb02c337a12ac3cfc2b474fa82711a
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/28/2019
ms.locfileid: "67447236"
---
# <a name="time-to-live-ttl-in-azure-cosmos-db"></a>Azure Cosmos DB 中的存留時間 (TTL) 

使用**存留時間** ( TTL)，Azure Cosmos DB 提供在一段特定時間後自動從容器刪除項目的功能。 根據預設，您可以在容器層級設定存留時間，並覆寫每個項目的值。 在容器或項目層級設定 TTL 之後，Azure Cosmos DB 會在自從上次修改以來的時間週期後自動移除這些項目。 存留時間值是以秒為單位來設定的。 設定 TTL 之後，系統會根據 TTL 值自動刪除已過期的項，不需要用戶端應用程序明確地發出刪除作業。

## <a name="time-to-live-for-containers-and-items"></a>容器和項目的存留時間

存留時間值是以秒為單位來設定的的，解釋為自上次修改項目時間之後的增量。 您可以在容器或容器內項目上設定存留時間：

1. **容器的存留時間** (使用 `DefaultTimeToLive` 設定)：

   - 如果遺失 (或設為 null)，項目便不會自動過期。

   - 如果存在且值設定為"-1"，則為無限，和依預設不會過期的項目。

   - 如果存在且值設定為某個數字 *"n 名"* – 項目將過期 *"n"* 之後其上次修改時間的秒數。

2. **項目的存留時間** (使用 `ttl` 設定)：

   - 僅在父容器有 `DefaultTimeToLive` 且未設定為 DefaultTTL 時，才適用此屬性。

   - 如果有的話，它會覆寫父容器的 `DefaultTimeToLive` 值。

## <a name="time-to-live-configurations"></a>存留時間組態

* 如果將某個容器的 TTL 設定為 “n”，該容器中的項目將在 n 秒之後過期。    如果同一容器中的項目有自己的存留時間且已設定為 -1 (表示不會過期)，或某些項目已使用不同的值來覆寫存留時間設定，則系統會根據這些項目自己的已設定 TTL 值設定為過期。 

* 如果未設定容器的 TTL，則此容器中項目的存留時間沒有任何作用。 

* 如果容器的 TTL 設定為 -1，則此容器中存留時間設為 n 的項目會在 n 秒後過期，而其餘的項目則不會過期。 

刪除以 TTL 為基礎的項目是免費的。 因為 TTL 過期而刪除項目時，沒有任何額外的成本 (也就是不會使用任何額外的 RU)。

## <a name="examples"></a>範例

此區段會顯示不同存留時間值指派給容器和項目使用的一些範例：

### <a name="example-1"></a>範例 1

在容器上的 TTL 設定為 null (DefaultTimeToLive = null)

|項目上的 TTL| 結果|
|---|---|
|ttl = null|    已停用 TTL。 項目永遠不會過期 （預設值）。|
|ttl = -1   |已停用 TTL。 項目永遠不會到期。|
|ttl = 2000年 |已停用 TTL。 項目永遠不會到期。|


### <a name="example-2"></a>範例 2

在容器上的 TTL 設定為-1 (DefaultTimeToLive =-1)

|項目上的 TTL| 結果|
|---|---|
|ttl = null |啟用 TTL 為止。 項目永遠不會過期 （預設值）。|
|ttl = -1   |啟用 TTL 為止。 項目永遠不會到期。|
|ttl = 2000年 |啟用 TTL 為止。 在 2000 秒後逾期項目。|


### <a name="example-3"></a>範例 3

在容器上的 TTL 設為 1000年 (DefaultTimeToLive = 1000年)

|項目上的 TTL| 結果|
|---|---|
|ttl = null|    啟用 TTL 為止。 項目將到期之後 1000 秒 （預設值）。|
|ttl = -1   |啟用 TTL 為止。 項目永遠不會到期。|
|ttl = 2000年 |啟用 TTL 為止。 在 2000 秒後逾期項目。|

## <a name="next-steps"></a>後續步驟

透過下列文章來了解如何設定存留時間：

* [如何設定存留時間](how-to-time-to-live.md)
