---
title: Azure CLI 指令碼範例
description: Azure CLI 指令碼範例可以建立和管理 Azure SQL Database 伺服器、彈性集區、資料庫和防火牆。
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: overview-samples, mvc
ms.devlang: azurecli
ms.topic: sample
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 02/03/2019
ms.openlocfilehash: 023651d61f891c5a73eee234a14306add838bf98
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/08/2019
ms.locfileid: "73821834"
---
# <a name="azure-cli-samples-for-azure-sql-database"></a>Azure SQL Database 的 Azure CLI 範例

您可以使用 <a href="/cli/azure">Azure CLI</a> 來設定 Azure SQL Database。

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

如果您選擇在本機安裝和使用 CLI，本主題會要求您執行 Azure CLI 2.0 版或更新版本。 執行 `az --version` 以尋找版本。 如果您需要安裝或升級，請參閱[安裝 Azure CLI]( /cli/azure/install-azure-cli)。

## <a name="single-database--elastic-poolstabsingle-database"></a>[單一資料庫與彈性集區](#tab/single-database)

下表包含適用於 Azure SQL Database 之 Azure CLI 指令碼範例的連結。

| |  |
|---|---|
|**建立單一資料庫和彈性集區**||
| [建立單一資料庫並設定防火牆規則](scripts/sql-database-create-and-configure-database-cli.md?toc=%2fcli%2fazure%2ftoc.json) | 此 CLI 指令碼範例會建立單一 Azure SQL 資料庫，並設定伺服器層級防火牆規則。 |
| [建立彈性集區並移動集區資料庫](scripts/sql-database-move-database-between-pools-cli.md?toc=%2fcli%2fazure%2ftoc.json) | 此CLI 指令碼範例會建立 SQL 彈性集區，並移動集區 Azure SQL 資料庫，然後變更計算大小。|
|**調整單一資料庫和彈性集區**||
| [調整單一資料庫](scripts/sql-database-monitor-and-scale-database-cli.md?toc=%2fcli%2fazure%2ftoc.json) | 此 CLI 指令碼範例會在查詢單一 Azure SQL 資料庫的大小資訊後，將其調整為不同的計算大小。 |
| [調整彈性集區](scripts/sql-database-scale-pool-cli.md?toc=%2fcli%2fazure%2ftoc.json) | 此 CLI 指令碼範例會將 SQL 彈性集區調整為不同的計算大小。  |
|**容錯移轉群組**||
| [將單一資料庫新增至容錯移轉群組](scripts/sql-database-add-single-db-to-failover-group-cli.md?toc=%2fcli%2fazure%2ftoc.json)| 此 CLI 指令碼會建立資料庫和容錯移轉群組，將資料庫新增至容錯移轉群組，並測試容錯移轉至次要伺服器。|
|||

深入了解[單一資料庫 Azure CLI API](sql-database-single-databases-manage.md#azure-cli-manage-sql-database-servers-and-single-databases)。

## <a name="managed-instancetabmanaged-instance"></a>[受控執行個體](#tab/managed-instance)

下表包含適用於 Azure SQL Database 之 Azure CLI 指令碼範例的連結 - 受控執行個體。

| |  |
|---|---|
| [建立受控執行個體](https://blogs.msdn.microsoft.com/sqlserverstorageengine/20../../create-azure-sql-managed-instance-using-azure-cli/) | 此 CLI 指令碼示範如何建立受控執行個體。 |
| [更新受控執行個體](https://blogs.msdn.microsoft.com/sqlserverstorageengine/20../../modify-azure-sql-database-managed-instance-using-azure-cli/) | 此 CLI 指令碼示範如何更新受控執行個體。 |
| [將資料庫移到另一個受控執行個體](https://blogs.msdn.microsoft.com/sqlserverstorageengine/20../../cross-instance-point-in-time-restore-in-azure-sql-database-managed-instance/) | 此 CLI 指令碼示範如何將資料庫的備份從一個執行個體還原到另一個執行個體。 |
|||

深入了解[受控執行個體 Azure CLI API](sql-database-managed-instance-create-manage.md#azure-cli-create-and-manage-managed-instances) 並尋找[其他範例](https://medium.com/azure-sqldb-managed-instance/working-with-sql-managed-instance-using-azure-cli-611795fe0b44)。

---
