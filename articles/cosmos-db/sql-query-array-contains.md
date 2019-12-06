---
title: Azure Cosmos DB 查詢語言中的 ARRAY_CONTAINS
description: 深入瞭解陣列在 Azure Cosmos DB 中如何包含 SQL 系統函數會傳回一個布林值，指出陣列是否包含指定的值。
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 115667c93bbb832926a7939cf250a02b6fef8baa
ms.sourcegitcommit: 9405aad7e39efbd8fef6d0a3c8988c6bf8de94eb
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/05/2019
ms.locfileid: "74873483"
---
# <a name="array_contains-azure-cosmos-db"></a>ARRAY_CONTAINS （Azure Cosmos DB）
傳回布林值，表示陣列是否包含指定值。 您可以在命令中使用布林值運算式，以檢查物件為部分相符或完全相符。 

## <a name="syntax"></a>語法
  
```sql
ARRAY_CONTAINS (<arr_expr>, <expr> [, bool_expr])  
```  
  
## <a name="arguments"></a>引數
  
*arr_expr*  
   這是要搜尋的陣列運算式。  
  
*expr*  
   這是要尋找的運算式。  

*bool_expr*  
   這是布林運算式。 如果評估為 ' true '，且指定的搜尋值為物件，則命令會檢查部分相符（搜尋物件是否為其中一個物件的子集）。 如果評估為 ' false '，此命令會檢查陣列中所有物件的完全相符。 若未指定，則預設值為 false。 
  
## <a name="return-types"></a>傳回類型
  
  傳回布林值。  
  
## <a name="examples"></a>範例
  
  下列範例說明如何使用 `ARRAY_CONTAINS`檢查陣列中的成員資格。  
  
```sql
SELECT   
           ARRAY_CONTAINS(["apples", "strawberries", "bananas"], "apples") AS b1,  
           ARRAY_CONTAINS(["apples", "strawberries", "bananas"], "mangoes") AS b2  
```  
  
 以下為結果集。  
  
```json
[{"b1": true, "b2": false}]  
```  

下列範例說明如何使用 ARRAY_CONTAINS 檢查陣列中的 JSON 部份相符。  
  
```sql
SELECT  
    ARRAY_CONTAINS([{"name": "apples", "fresh": true}, {"name": "strawberries", "fresh": true}], {"name": "apples"}, true) AS b1, 
    ARRAY_CONTAINS([{"name": "apples", "fresh": true}, {"name": "strawberries", "fresh": true}], {"name": "apples"}) AS b2,
    ARRAY_CONTAINS([{"name": "apples", "fresh": true}, {"name": "strawberries", "fresh": true}], {"name": "mangoes"}, true) AS b3 
```  
  
 以下為結果集。  
  
```json
[{
  "b1": true,
  "b2": false,
  "b3": false
}] 
```  
  

## <a name="next-steps"></a>後續步驟

- [陣列函數 Azure Cosmos DB](sql-query-array-functions.md)
- [系統函數 Azure Cosmos DB](sql-query-system-functions.md)
- [Azure Cosmos DB 簡介](introduction.md)
