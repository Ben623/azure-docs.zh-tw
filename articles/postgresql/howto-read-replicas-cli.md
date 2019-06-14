---
title: 管理 Azure Database for PostgreSQL-從 Azure CLI 的單一伺服器讀取的複本
description: 了解如何管理適用於 PostgreSQL 的 Azure CLI 從單一伺服器的 Azure 資料庫中讀取的複本。
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 05/28/2019
ms.openlocfilehash: 9a6a1a744a8441d2f082d4d14a3aba8aa1cfc09e
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "66306027"
---
# <a name="create-and-manage-read-replicas-from-the-azure-cli"></a>建立和管理讀取的複本，從 Azure CLI

在本文中，您會學習如何建立和管理適用於 PostgreSQL 的 Azure CLI 的 Azure 資料庫中讀取的複本。 若要深入了解讀取複本，請參閱[概觀](concepts-read-replicas.md)。

> [!IMPORTANT]
> 在與您的主要伺服器相同的區域，或您選擇的任何其他 Azure 區域中，您可以建立一個讀取的複本。 跨區域複寫目前為公開預覽狀態。

## <a name="prerequisites"></a>必要條件
- 使用[適用於 PostgreSQL 的 Azure 資料庫伺服器](quickstart-create-server-up-azure-cli.md)作為主要伺服器。

[!INCLUDE [cloud-shell-try-it](../../includes/cloud-shell-try-it.md)]

如果您選擇在本機安裝和使用 CLI，本文會要求您執行 Azure CLI 2.0 版或更新版本。 若要查看所安裝的版本，請執行 `az --version` 命令。 如果您需要安裝或升級，請參閱[安裝 Azure CLI]( /cli/azure/install-azure-cli)。 


## <a name="prepare-the-master-server"></a>準備主要伺服器
若要在一般用途和記憶體最佳化層中準備主要伺服器，這些是必要步驟。

`azure.replication_support` 參數必須在主要伺服器上設定為 **REPLICA**。 靜態參數變更時，重新啟動伺服器時需要，變更才會生效。

1. 設定`azure.replication_support`至複本。

   ```azurecli-interactive
   az postgres server configuration set --resource-group myresourcegroup --server-name mydemoserver --name azure.replication_support --value REPLICA
   ```

2. 重新啟動以套用變更到伺服器。

   ```azurecli-interactive
   az postgres server restart --name mydemoserver --resource-group myresourcegroup
   ```

## <a name="create-a-read-replica"></a>建立讀取複本

[Az postgres server 複本建立](/cli/azure/postgres/server/replica?view=azure-cli-latest#az-postgres-server-replica-create)命令需要下列參數：

| 設定 | 範例值 | 描述  |
| --- | --- | --- |
| resource-group | myresourcegroup |  複本伺服器將會建立資源群組。  |
| name | mydemoserver-replica | 所建立的新複本伺服器名稱。 |
| source-server | mydemoserver | 名稱或資源識別碼的現有的主要伺服器複寫。 |

在下列 CLI 範例中，為主機相同的區域中建立複本。

```azurecli-interactive
az postgres server replica create --name mydemoserver-replica --source-server mydemoserver --resource-group myresourcegroup
```

若要建立跨區域讀取複本，請使用`--location`參數。 下列 CLI 範例會建立複本，在美國西部。

```azurecli-interactive
az postgres server replica create --name mydemoserver-replica --source-server mydemoserver --resource-group myresourcegroup --location westus
```

如果您尚未設定`azure.replication_support`參數來**複本**上一般用途或記憶體最佳化的主要伺服器，並重新啟動伺服器，您會收到錯誤。 建立複本之前，請完成這兩個步驟。

系統會使用與主要伺服器相同的伺服器設定來建立複本。 建立複本之後，以下設定可以個別地從主要伺服器進行變更：計算世代、虛擬核心、儲存體及備份保留期間。 定價層也可以個別變更，但不能變更為基本層，或從基本層變更為別的層。

> [!IMPORTANT]
> 在將主要伺服器設定更新為新值之前，應將複本的設定更新為相等或更大的值。 此動作可確保複本可以跟上主要伺服器上所做的變更。

## <a name="list-replicas"></a>清單複本
您可以使用檢視的主要伺服器的複本清單[az postgres server 複本清單](/cli/azure/postgres/server/replica?view=azure-cli-latest#az-postgres-server-replica-list)命令。

```azurecli-interactive
az postgres server replica list --server-name mydemoserver --resource-group myresourcegroup 
```

## <a name="stop-replication-to-a-replica-server"></a>停止複寫至複本伺服器
您可以使用，以停止主要伺服器與讀取的複本之間的複寫[az postgres server 複本停止](/cli/azure/postgres/server/replica?view=azure-cli-latest#az-postgres-server-replica-stop)命令。

停止主要伺服器和讀取複本的複寫之後，即無法再復原。 讀取複本會變成支援讀取和寫入的獨立伺服器。 獨立伺服器無法再次設定為複本。

```azurecli-interactive
az postgres server replica stop --name mydemoserver-replica --resource-group myresourcegroup 
```

## <a name="delete-a-master-or-replica-server"></a>刪除主要或複本伺服器
若要刪除的主要或複本伺服器，您使用[az postgres server delete](/cli/azure/postgres/server?view=azure-cli-latest#az-postgres-server-delete)命令。

刪除主要伺服器時，所有讀取複本上的複寫都會停止。 讀取複本會變成獨立伺服器，進而支援讀取和寫入。

```azurecli-interactive
az postgres server delete --name myserver --resource-group myresourcegroup
```

## <a name="next-steps"></a>後續步驟
深入了解[適用於 PostgreSQL 的 Azure 資料庫中的讀取複本](concepts-read-replicas.md)。