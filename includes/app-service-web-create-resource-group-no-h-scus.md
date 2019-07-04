---
title: 包含檔案
description: 包含檔案
services: app-service
author: msangapu
ms.service: app-service
ms.topic: include
ms.date: 09/18/2018
ms.author: msangapu
ms.custom: include file
ms.openlocfilehash: 458782011623721bb44771d42caa32130e23bbc9
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/18/2019
ms.locfileid: "67174466"
---
[!INCLUDE [resource group intro text](resource-group.md)]

在 Cloud Shell 中，使用 [`az group create`](/cli/azure/group?view=azure-cli-latest#az-group-create) 命令來建立資源群組。 下列範例會在*美國中南部*位置中建立名為 *myResourceGroup* 的資源群組。 若要查看**免類**層中 App Service 的所有支援位置，請執行 [`az appservice list-locations --sku FREE`](/cli/azure/appservice?view=azure-cli-latest#az-appservice-list-locations) 命令。

```azurecli-interactive
az group create --name myResourceGroup --location "South Central US"
```

您通常會在附近的區域中建立資源群組和資源。 

當命令完成時，JSON 輸出會顯示資源群組屬性。