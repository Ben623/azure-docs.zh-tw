---
title: 從 Linux 上的 Azure 儲存體提供內容 - App Service
description: 如何從 Azure 儲存體設定，並在 Linux 上的 App Service 中提供內容。
author: msangapu
manager: jeconnoc
ms.service: app-service
ms.workload: web
ms.topic: article
ms.date: 2/04/2019
ms.author: msangapu
ms.openlocfilehash: 15cb31a3157b034089b1518a4e70eeb93ecc449e
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/07/2019
ms.locfileid: "67617104"
---
# <a name="serve-content-from-azure-storage-in-app-service-on-linux"></a>從 Azure 儲存體在 Linux 上的 App Service 中提供內容

本指南說明如何使用 [Azure 儲存體](/azure/storage/common/storage-introduction) 在 Linux 上的 App Service 中提供靜態內容。 好處包括受保護的內容、內容可攜性、可存取多個應用程式，以及多個傳輸方法。

## <a name="prerequisites"></a>先決條件

- 現有的 Web 應用程式 (Linux 上的 App Service 或適用於容器的 Web 應用程式)。
- [Azure CLI](/cli/azure/install-azure-cli) (2.0.46 或更新版本)。

## <a name="create-azure-storage"></a>建立 Azure 儲存體

> [!NOTE]
> Azure 儲存體為非預設儲存體，並且會分開計費，不包含在 Web 應用程式中。
>
> 攜帶您自己的儲存體不支援使用儲存體防火牆組態中，由於基礎結構的限制。
>

建立 [Azure 儲存體帳戶](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account?tabs=azure-cli)。

```azurecli
#Create Storage Account
az storage account create --name <storage_account_name> --resource-group myResourceGroup

#Create Storage Container
az storage container create --name <storage_container_name> --account-name <storage_account_name>
```

## <a name="upload-files-to-azure-storage"></a>將檔案上傳至 Azure 儲存體

若要將本機目錄上傳至儲存體帳戶，可以使用 [`az storage blob upload-batch`](https://docs.microsoft.com/cli/azure/storage/blob?view=azure-cli-latest#az-storage-blob-upload-batch) 命令，如以下範例所示：

```azurecli
az storage blob upload-batch -d <full_path_to_local_directory> --account-name <storage_account_name> --account-key "<access_key>" -s <source_location_name>
```

## <a name="link-storage-to-your-web-app-preview"></a>將儲存體連結到您的 Web 應用程式 (預覽)

> [!CAUTION]
> 將 Web 應用程式中的現有目錄連結至儲存體帳戶將會刪除目錄內容。 如果您是移轉現有應用程式的檔案，請先備份您的應用程式和其內容再開始移轉。
>

若要將儲存體帳戶掛接至您 App Service 應用程式中的目錄，可以使用 [`az webapp config storage-account add`](https://docs.microsoft.com/cli/azure/webapp/config/storage-account?view=azure-cli-latest#az-webapp-config-storage-account-add) 命令。 儲存體類型可以是 AzureBlob 或 AzureFiles。 您可以針對此容器使用 AzureBlob。

```azurecli
az webapp config storage-account add --resource-group <group_name> --name <app_name> --custom-id <custom_id> --storage-type AzureBlob --share-name <share_name> --account-name <storage_account_name> --access-key "<access_key>" --mount-path <mount_path_directory>
```

您應該對想要連結到儲存體帳戶的任何其他目錄執行此作業。

## <a name="verify"></a>驗證

一旦儲存體容器連結至 Web 應用程式，您就可以執行以下命令來確認：

```azurecli
az webapp config storage-account list --resource-group <resource_group> --name <app_name>
```

## <a name="use-custom-storage-in-docker-compose"></a>使用自訂的儲存體中 Docker Compose

Azure 儲存體可以裝載多容器應用程式使用的自訂 id。若要檢視自訂識別碼名稱，請執行[ `az webapp config storage-account list --name <app_name> --resource-group <resource_group>` ](/cli/azure/webapp/config/storage-account?view=azure-cli-latest#az-webapp-config-storage-account-list)。

在您*docker compose.yml*檔案中，對應`volumes`選項設定為`custom-id`。 例如:

```yaml
wordpress:
  image: wordpress:latest
  volumes:
  - <custom-id>:<path_in_container>
```

## <a name="next-steps"></a>後續步驟

- [在 Azure App Service 中設定 Web 應用程式](../configure-common.md)。
