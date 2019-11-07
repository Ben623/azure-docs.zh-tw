---
title: 在 Azure 中開發資料倉儲的資源
description: SQL 資料倉儲的開發概念、設計決策、建議和程式碼撰寫技巧。
services: sql-data-warehouse
author: XiaoyuMSFT
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: development
ms.date: 08/29/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.openlocfilehash: a6cb7937bdd5dea9eb1a48b2b350db9077431fe0
ms.sourcegitcommit: 359930a9387dd3d15d39abd97ad2b8cb69b8c18b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/06/2019
ms.locfileid: "73645607"
---
# <a name="design-decisions-and-coding-techniques-for-sql-data-warehouse"></a>SQL 資料倉儲的設計決策和程式碼撰寫技術
若要進一步了解 SQL 資料倉儲的重要的設計決策、建議和程式碼撰寫技術，請參閱這些開發文章。

## <a name="key-design-decisions"></a>主要的設計決策
下列文章醒目提示使用 SQL 資料倉儲開發分散式資料倉儲的概念和設計決策：

* [連線][connections]
* [並行][concurrency]
* [交易][transactions]
* [使用者定義的結構描述][user-defined schemas]
* [資料表散發][table distribution]
* [資料表索引][table indexes]
* [資料表分割][table partitions]
* [CTAS][CTAS]
* [統計資料][statistics]

## <a name="development-recommendations-and-coding-techniques"></a>開發建議和程式碼撰寫技術
這些文章會強調特定的程式碼撰寫技術、秘訣和建議，用於開發您的 SQL 資料倉儲：

* [預存程序][stored procedures]
* [標籤][labels]
* [檢視][views]
* [暫存資料表][temporary tables]
* [動態 SQL][dynamic SQL]
* [迴圈][looping]
* [依據選項分組][group by options]
* [變數指派][variable assignment]

## <a name="next-steps"></a>後續步驟
如需更多的參考資訊，請參閱 [SQL 資料倉儲 T-SQL 陳述式](sql-data-warehouse-reference-tsql-statements.md)。

<!--Image references-->

<!--Article references-->
[concurrency]: ./resource-classes-for-workload-management.md
[connections]: ./sql-data-warehouse-connect-overview.md
[CTAS]: ./sql-data-warehouse-develop-ctas.md
[dynamic SQL]: ./sql-data-warehouse-develop-dynamic-sql.md
[group by options]: ./sql-data-warehouse-develop-group-by-options.md
[labels]: ./sql-data-warehouse-develop-label.md
[looping]: ./sql-data-warehouse-develop-loops.md
[statistics]: ./sql-data-warehouse-tables-statistics.md
[stored procedures]: ./sql-data-warehouse-develop-stored-procedures.md
[table distribution]: ./sql-data-warehouse-tables-distribute.md
[table indexes]: ./sql-data-warehouse-tables-index.md
[table partitions]: ./sql-data-warehouse-tables-partition.md
[temporary tables]: ./sql-data-warehouse-tables-temporary.md
[transactions]: ./sql-data-warehouse-develop-transactions.md
[user-defined schemas]: ./sql-data-warehouse-develop-user-defined-schemas.md
[variable assignment]: ./sql-data-warehouse-develop-variable-assignment.md
[views]: ./sql-data-warehouse-develop-views.md


<!--MSDN references-->
[renaming objects]: https://msdn.microsoft.com/library/mt631611.aspx

<!--Other Web references-->
