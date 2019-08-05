---
title: 包含檔案
description: 包含檔案
services: functions
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 04/24/2019
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: fbb537c9584c948af37694b3bfc77a7c345e084d
ms.sourcegitcommit: 3877b77e7daae26a5b367a5097b19934eb136350
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/30/2019
ms.locfileid: "68639158"
---
## <a name="deploy-the-function-app-project-to-azure"></a>將函式應用程式專案部署至 Azure

在 Azure 中建立函式應用程式之後，您可以使用 [`func azure functionapp publish`](../articles/azure-functions/functions-run-local.md#project-file-deployment) Core 工具命令來將專案程式碼部署至 Azure。 在下一個命令中，使用上一個步驟的應用程式名稱取代 `<APP_NAME>`。

```bash
func azure functionapp publish <APP_NAME>
```

您會看到類似下列的輸出，該輸出已截短，以提高可讀性：

```output
Getting site publishing info...
...

Preparing archive...
Uploading content...
Upload completed successfully.
Deployment completed successfully.
Syncing triggers...
Functions in myfunctionapp:
    HttpTrigger - [httpTrigger]
        Invoke url: https://myfunctionapp.azurewebsites.net/api/httptrigger?code=cCr8sAxfBiow548FBDLS1....
```

複製 `HttpTrigger` 的叫用 `Invoke url` 值，您現在可以使用它來測試 Azure 中的功能。 該 URL 包含一個 `code` 查詢字串值，它是您的函式金鑰。 此金鑰使其他人很難在 Azure 中呼叫 HTTP 觸發程序端點。