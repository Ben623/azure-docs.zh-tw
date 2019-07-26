---
title: 在 Azure SQL 資料倉儲中使用動態 SQL 檢視 | Microsoft Docs
description: 使用 Azure SQL 資料倉儲中的動態 SQL 以開發解決方案的秘訣。
services: sql-data-warehouse
author: XiaoyuMSFT
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: query
ms.date: 04/17/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.openlocfilehash: 4454b1d44d0be61dca8571e86c73e09a9527d1eb
ms.sourcegitcommit: 75a56915dce1c538dc7a921beb4a5305e79d3c7a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/24/2019
ms.locfileid: "68479651"
---
# <a name="dynamic-sql-in-sql-data-warehouse"></a>SQL 資料倉儲中的動態 SQL
使用 Azure SQL 資料倉儲中的動態 SQL 以開發解決方案的秘訣。

## <a name="dynamic-sql-example"></a>動態 SQL 範例

開發 SQL 資料倉儲的應用程式程式碼時，您可能須使用動態 SQL，以協助提供有彈性的泛型模組化解決方案。 不過，SQL 資料倉儲目前不支援 Blob 資料類型。 不支援 Blob 資料類型可能會限制字串大小，因為 Blob 資料類型包括 varchar(max) 與 nvarchar(max) 類型。 如果您在曾在應用程式的程式碼中使用這些類型建立大型字串，便必須將這些程式碼分成許多區塊，並以 EXEC 陳述式取代。

以下是簡單的範例：

```sql
DECLARE @sql_fragment1 VARCHAR(8000)=' SELECT name '
,       @sql_fragment2 VARCHAR(8000)=' FROM sys.system_views '
,       @sql_fragment3 VARCHAR(8000)=' WHERE name like ''%table%''';

EXEC( @sql_fragment1 + @sql_fragment2 + @sql_fragment3);
```

如果字串簡短，您可以像平常一樣使用 [sp_executesql](/sql/relational-databases/system-stored-procedures/sp-executesql-transact-sql)。

> [!NOTE]
> 以動態 SQL 執行的陳述式仍會受限於所有的 TSQL 驗證規則。
> 
> 

## <a name="next-steps"></a>後續步驟
如需更多開發秘訣，請參閱[開發概觀](sql-data-warehouse-overview-develop.md)。

