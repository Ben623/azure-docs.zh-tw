---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 10/26/2018
ms.author: tamram
ms.custom: seo-python-october2019
ms.openlocfilehash: 24e755edbb18e85bcaa97435dee27418c1457221
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/19/2019
ms.locfileid: "74174931"
---
## <a name="what-is-queue-storage"></a>什麼是佇列儲存體？

Azure 佇列儲存體是一項儲存大量訊息的服務，全球任何地方都可利用 HTTP 或 HTTPS 並透過驗證的呼叫來存取這些訊息。 單一佇列訊息的大小上限為 64 KB，而一個佇列可以包含數百萬個訊息，以儲存體帳戶的總容量為限。 佇列儲存體通常用來建立要以非同步方式處理的待處理專案（backlog）。

## <a name="queue-service-concepts"></a>佇列服務概念

Azure 佇列服務包含下列元件：

![Azure 佇列服務元件](./media/storage-queue-concepts-include/azure-queue-service-components.png)

* **URL 格式：** 可利用下列 URL 格式來定址佇列：   
    http://`<storage account>`.queue.core.windows.net/`<queue>` 
  
    下列 URL 可定址圖中的佇列：  
  
    `http://myaccount.queue.core.windows.net/images-to-download`

* **儲存體帳戶：** 一律透過儲存體帳戶來存取 Azure 儲存體。 如需關於儲存體帳戶容量的詳細資訊，請參閱＜ [Azure 儲存體延展性和效能目標](../articles/storage/common/storage-scalability-targets.md) ＞(英文)。
* **佇列：** 佇列包含一組訊息。 所有訊息都必須放在佇列中。 請注意，佇列名稱必須是小寫。 如需為佇列命名的詳細資訊，請參閱 [為佇列和中繼資料命名](https://msdn.microsoft.com/library/azure/dd179349.aspx)。
* **訊息：** 大小上限為 64 KB 的訊息 (任何格式)。 訊息可保留在佇列中的時間上限為 7 天。 如需版本 2017-07-29 或更新版本，最大存留時間可以是任何正數，或是表示訊息未過期的 -1。 如果省略此參數，則預設存留時間為 7 天。

