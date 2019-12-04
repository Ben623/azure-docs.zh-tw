---
title: 限制-適用於 MySQL 的 Azure 資料庫
description: 本文說明適用於 MySQL 的 Azure 資料庫中的限制，例如連線數量和儲存引擎選項。
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 12/2/2019
ms.openlocfilehash: 07feb3ebf9720d70da441486fd0b2e6e274b68e4
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/03/2019
ms.locfileid: "74770912"
---
# <a name="limitations-in-azure-database-for-mysql"></a>「適用於 MySQL 的 Azure 資料庫」中的限制
下列各節說明資料庫服務中的容量、儲存引擎支援、權限支援、資料操作陳述式支援，以及功能限制。 另請參閱適用於 MySQL 資料庫引擎的[一般限制](https://dev.mysql.com/doc/mysql-reslimits-excerpt/5.6/en/limits.html) \(英文\)。

## <a name="maximum-connections"></a>最大連線數
每個定價層和 vCores 的連線數目上限如下所示： 

|定價層|**vCore(s)**| **連線數目上限**|
|---|---|---|
|基本| 1| 50|
|基本| 2| 100|
|一般用途| 2| 300|
|一般用途| 4| 625|
|一般用途| 8| 1250|
|一般用途| 16| 2500|
|一般用途| 32| 5000|
|一般用途| 64| 10000|
|記憶體最佳化| 2| 600|
|記憶體最佳化| 4| 1250|
|記憶體最佳化| 8| 2500|
|記憶體最佳化| 16| 5000|
|記憶體最佳化| 32| 10000|

當連線超過限制時，則可能會收到下列錯誤：
> 錯誤 1040 (08004)：太多的連接

## <a name="storage-engine-support"></a>儲存引擎支援

### <a name="supported"></a>支援的
- [InnoDB](https://dev.mysql.com/doc/refman/5.7/en/innodb-introduction.html) \(英文\)
- [MEMORY](https://dev.mysql.com/doc/refman/5.7/en/memory-storage-engine.html) \(英文\)

### <a name="unsupported"></a>不支援
- [MyISAM](https://dev.mysql.com/doc/refman/5.7/en/myisam-storage-engine.html) \(英文\)
- [BLACKHOLE](https://dev.mysql.com/doc/refman/5.7/en/blackhole-storage-engine.html) \(英文\)
- [ARCHIVE](https://dev.mysql.com/doc/refman/5.7/en/archive-storage-engine.html) \(英文\)
- [FEDERATED](https://dev.mysql.com/doc/refman/5.7/en/federated-storage-engine.html) \(英文\)

## <a name="privilege-support"></a>權限支援

### <a name="unsupported"></a>不支援
- DBA 角色：許多伺服器參數與設定可能會在無意中造成伺服器效能降級，或是取消 DBMS 的 ACID 屬性。 因此，為了維護產品層級的服務完整性與 SLA，此服務並不會公開 DBA 角色。 在建立新資料庫行個體時所建構的預設使用者帳戶，可讓使用者在受管理的資料庫執行個體中執行大部分的 DDL 與 DML 陳述式。 
- SUPER 權限：同樣地，[SUPER 權限](https://dev.mysql.com/doc/refman/5.7/en/privileges-provided.html#priv_super)也受到限制。
- DEFINER：需要有超級許可權才能建立且受到限制。 如果使用備份匯入資料，執行 mysqldump 時以手動方式或使用 `--skip-definer` 命令移除 `CREATE DEFINER` 命令。

## <a name="data-manipulation-statement-support"></a>資料操作陳述式支援

### <a name="supported"></a>支援的
- 支援 `LOAD DATA INFILE`，但必須指定 `[LOCAL]` 參數並導向至 UNC 路徑 (透過 SMB 掛接的 Azure 儲存體)。

### <a name="unsupported"></a>不支援
- `SELECT ... INTO OUTFILE`

## <a name="functional-limitations"></a>功能限制：

### <a name="scale-operations"></a>調整作業
- 目前不支援基本定價層的雙向動態調整。
- 不支援減少伺服器儲存體大小。

### <a name="server-version-upgrades"></a>伺服器版本升級
- 目前不支援在主要資料庫引擎版本之間進行自動轉換。 如果您希望升級至下個主要版本，請將資料庫[備份和還原](./concepts-migrate-dump-restore.md)至使用新引擎版本所建立的伺服器。

### <a name="point-in-time-restore"></a>還原時間點
- 使用 PITR 功能時，所建立新伺服器的設定會與作為新伺服器基礎的伺服器設定相同。
- 不支援還原已刪除的伺服器。

### <a name="vnet-service-endpoints"></a>VNet 服務端點
- VNet 服務端點的支援僅適用於一般用途和記憶體最佳化伺服器。

### <a name="storage-size"></a>儲存體大小
- 如需每個定價層的儲存體大小限制，請參閱[定價層](concepts-pricing-tiers.md)。

## <a name="current-known-issues"></a>目前已知問題
- MySQL 伺服器執行個體於建立連線後會顯示錯誤的伺服器版本。 若要取得正確的伺服器執行個體引擎版本，請使用 `select version();` 命令。

## <a name="next-steps"></a>後續步驟
- [每個服務層級中可用的項目](concepts-pricing-tiers.md)
- [支援的 MySQL 資料庫版本](concepts-supported-versions.md)
