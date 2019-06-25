---
title: 適用於 MySQL 的 Azure 資料庫中的伺服器記錄
description: 描述 for MySQL，並啟用不同的記錄層級的可用參數的 Azure 資料庫中可用的慢速查詢記錄。
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 05/29/2019
ms.openlocfilehash: 1a8956d40ef30e8d52fbdded3448019e14ab16a5
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "67062411"
---
# <a name="slow-query-logs-in-azure-database-for-mysql"></a>適用於 MySQL 慢速查詢記錄中的 Azure 資料庫
在適用於 MySQL 的 Azure 資料庫中，使用者可以使用慢速查詢記錄。 不支援存取交易記錄。 慢速查詢記錄檔可以用來找出效能瓶頸，以進行疑難排解。

如需 MySQL 慢速查詢記錄的詳細資訊，請參閱 MySQL 參考手冊的[慢速查詢記錄章節](https://dev.mysql.com/doc/refman/5.7/en/slow-query-log.html)。

## <a name="access-slow-query-logs"></a>存取慢速查詢記錄
您可以列出和下載 Azure Database for MySQL 慢速查詢記錄，使用 Azure 入口網站和 Azure CLI。

在 Azure 入口網站中，選取適用於 MySQL 的 Azure 資料庫伺服器。 在 [監視]  標題下方，選取 [伺服器記錄]  頁面。

如需 Azure CLI 的詳細資訊，請參閱[使用 Azure CLI 設定和存取伺服器記錄](howto-configure-server-logs-in-cli.md)。

## <a name="log-retention"></a>記錄保留
記錄最多可以從建立開始算起保留七天。 如果可用記錄的大小總計超過 7 GB，則除非有空間可用，否則會刪除最舊檔案。 

記錄會每隔 24 小時或 7 GB 旋轉一次，先到者先用。

## <a name="configure-slow-query-logging"></a>設定慢速查詢記錄 
預設會停用慢速查詢記錄。 若要啟用它，請將 slow_query_log 設為 ON。

您可以調整的其他參數包含：

- **long_query_time**：如果查詢所需的時間比記錄該查詢的 long_query_time (秒) 還要久。 預設值為 10 秒。
- **log_slow_admin_statements**：如果 ON 在寫入至 slow_query_log 的陳述式中包含 ALTER_TABLE 和 ANALYZE_TABLE 這類管理陳述式。
- **log_queries_not_using_indexes**：決定是否將未使用索引的查詢記錄至 slow_query_log
- **log_throttle_queries_not_using_indexes**：這個參數會限制可寫入至慢速查詢記錄的非索引查詢次數。 log_queries_not_using_indexes 設為 ON 時，這個參數會生效。

如需慢速查詢記錄參數的完整描述，請參閱 MySQL [慢速查詢記錄文件](https://dev.mysql.com/doc/refman/5.7/en/slow-query-log.html)。

## <a name="diagnostic-logs"></a>診斷記錄
適用於 MySQL 的 Azure 資料庫會與 Azure 監視器診斷記錄整合。 在 MySQL 服务器上启用慢查询日志后，可以选择将它们发送到 Azure Monitor 日志、事件中心或 Azure 存储。 若要深入了解如何啟用診斷記錄，請參閱[診斷記錄文件](../azure-monitor/platform/diagnostic-logs-overview.md)的操作說明一節。

> [!IMPORTANT]
> 服务器日志的此诊断功能仅适用于“常规用途”和“内存优化”的[定价层](concepts-pricing-tiers.md)。

下表描述每個記錄的內容。 視輸出方法而定，包含的欄位及其出現的順序可能有所不同。

| **屬性** | **說明** |
|---|---|
| `TenantId` | 您的租用戶識別碼 |
| `SourceSystem` | `Azure` |
| `TimeGenerated` [UTC] | 以 UTC 記錄記錄時的時間戳記 |
| `Type` | 記錄的類型。 一律為 `AzureDiagnostics` |
| `SubscriptionId` | 伺服器所屬訂用帳戶的 GUID |
| `ResourceGroup` | 伺服器所屬資源群組的名稱 |
| `ResourceProvider` | 資源提供者名稱。 一律為 `MICROSOFT.DBFORMYSQL` |
| `ResourceType` | `Servers` |
| `ResourceId` | 資源 URI |
| `Resource` | 伺服器的名稱 |
| `Category` | `MySqlSlowLogs` |
| `OperationName` | `LogEvent` |
| `Logical_server_name_s` | 伺服器的名稱 |
| `start_time_t` [UTC] | 查詢開始時間 |
| `query_time_s` | 執行查詢所花費的總時間 |
| `lock_time_s` | 查詢遭到鎖定的總時間 |
| `user_host_s` | 使用者名稱 |
| `rows_sent_s` | 傳送的資料列數目 |
| `rows_examined_s` | 檢查的資料列數目 |
| `last_insert_id_s` | [last_insert_id](https://dev.mysql.com/doc/refman/8.0/en/information-functions.html#function_last-insert-id) |
| `insert_id_s` | 插入識別碼 |
| `sql_text_s` | 完整查詢 |
| `server_id_s` | 伺服器的識別碼 |
| `thread_id_s` | 執行緒識別碼 |
| `\_ResourceId` | 資源 URI |

## <a name="next-steps"></a>後續步驟
- [如何從 Azure CLI 設定和存取伺服器記錄](howto-configure-server-logs-in-cli.md)。
