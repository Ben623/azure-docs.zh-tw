---
title: Azure Cosmos DB 查詢語言中的 GetCurrentDateTime
description: 深入瞭解 Azure Cosmos DB 中的 SQL 系統函數 GetCurrentDateTime。
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 8a2c3dcd3c8ca6dc9d751e50a7862fe98e6de510
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/27/2019
ms.locfileid: "71351016"
---
# <a name="getcurrentdatetime-azure-cosmos-db"></a>GetCurrentDateTime （Azure Cosmos DB）
 傳回目前 UTC （國際標準時間）的日期和時間（以 ISO 8601 字串為限）。
  
## <a name="syntax"></a>語法
  
```sql
GetCurrentDateTime ()
```
  
## <a name="return-types"></a>傳回類型
  
  傳回目前的 UTC 日期和時間 ISO 8601 字串值，格式為 `YYYY-MM-DDThh:mm:ss.sssZ`，其中：
  
  |||
  |-|-|
  |YYYY|四位數的年份|
  |MM|兩位數的月份（01 = 一月等等）|
  |DD|兩位數的月份日期（01到31）|
  |T|時間元素開頭的 signifier|
  |hh|兩位數小時（00到23）|
  |mm|兩位數的分鐘數（00到59）|
  |ss|兩位數的秒數（00到59）|
  |.sss|三位數的十進位數（秒）|
  |Z|UTC （國際標準時間）指示項||
  
  如需 ISO 8601 格式的詳細資訊，請參閱[ISO_8601](https://en.wikipedia.org/wiki/ISO_8601)

## <a name="remarks"></a>備註

  GetCurrentDateTime （）是不具決定性的函數。 
  
  傳回的結果是 UTC。

## <a name="examples"></a>範例
  
  下列範例顯示如何使用 GetCurrentDateTime （）內建函數來取得目前的 UTC 日期時間。
  
```sql
SELECT GetCurrentDateTime() AS currentUtcDateTime
```  
  
 以下是範例結果集。
  
```json
[{
  "currentUtcDateTime": "2019-05-03T20:36:17.784Z"
}]  
```  

## <a name="next-steps"></a>後續步驟

- [日期和時間函數 Azure Cosmos DB](sql-query-date-time-functions.md)
- [系統函數 Azure Cosmos DB](sql-query-system-functions.md)
- [Azure Cosmos DB 簡介](introduction.md)
