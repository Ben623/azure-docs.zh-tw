---
title: 如何使用 Python-Azure 儲存體中的佇列儲存體
description: 了解如何使用 Python 的 Azure 佇列服務來建立和刪除佇列，以及插入、取得和刪除訊息。
services: storage
author: mhopkins-msft
ms.service: storage
ms.devlang: python
ms.topic: article
ms.date: 12/14/2018
ms.author: mhopkins
ms.reviewer: cbrooks
ms.subservice: queues
ms.openlocfilehash: 75f04893067d92813207bd656fc3368239ae9303
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "65142797"
---
# <a name="how-to-use-queue-storage-from-python"></a>如何使用 Python 的佇列儲存體
[!INCLUDE [storage-selector-queue-include](../../../includes/storage-selector-queue-include.md)]

[!INCLUDE [storage-try-azure-tools-queues](../../../includes/storage-try-azure-tools-queues.md)]

## <a name="overview"></a>總覽
本指南說明如何使用 Azure 佇列儲存體服務執行一般案例。 這些範例是以 Python 所撰寫，並使用 [Microsoft Azure Storage SDK for Python]。 所涵蓋的案例包括「插入」  、「查看」  、「取得」  和「刪除」  佇列訊息，以及「建立和刪除佇列」  。 如需佇列的詳細資訊，請參閱 [後續步驟] 一節。

[!INCLUDE [storage-queue-concepts-include](../../../includes/storage-queue-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

## <a name="download-and-install-azure-storage-sdk-for-python"></a>下載並安裝 Azure Storage SDK for Python

[Azure Storage SDK for Python](https://github.com/azure/azure-storage-python) 需要 Python 2.7、3.3、3.4、3.5 或 3.6。
 
### <a name="install-via-pypi"></a>透過 PyPi 安裝

若要透過 Python Package Index (PyPI) 安裝，請輸入：

```bash
pip install azure-storage-queue
```

> [!NOTE]
> 如果您要從 Azure Storage SDK for Python 版本 0.36 或更早版本升級，請先使用 `pip uninstall azure-storage` 解除安裝舊版的 SDK，再安裝最新的封裝。

如需替代安裝方法，請參閱 [Azure Storage SDK for Python](https://github.com/Azure/azure-storage-python/) \(英文\)。

## <a name="view-the-sample-application"></a>檢視範例應用程式

若要檢視和執行示範如何使用 Python 與 Azure 佇列的範例應用程式，請參閱 [Azure 儲存體：在 Python 中開始使用 Azure Queues](https://github.com/Azure-Samples/storage-queue-python-getting-started)。 

若要執行範例應用程式，請確定您已安裝 `azure-storage-queue` 和 `azure-storage-common` 封裝。

## <a name="how-to-create-a-queue"></a>如何：建立佇列

**QueueService** 物件可讓您操作佇列。 下列程式碼將建立 **QueueService** 物件。 將下列內容新增至您想要在其中以程式設計方式存取 Azure 儲存體之任何 Python 檔案內的頂端附近：

```python
from azure.storage.queue import QueueService
```

下列程式碼會使用儲存體帳戶名稱和帳戶金鑰來建立 **QueueService** 物件。 將 'myaccount' 和 'mykey' 取代為您的帳戶名稱和金鑰。

```python
queue_service = QueueService(account_name='myaccount', account_key='mykey')

queue_service.create_queue('taskqueue')
```

## <a name="how-to-insert-a-message-into-a-queue"></a>作法：將訊息插入佇列
若要將訊息插入佇列，請使用 **put\_message** 方法建立新訊息，並將它新增至佇列。

```python
queue_service.put_message('taskqueue', u'Hello World')
```

## <a name="how-to-peek-at-the-next-message"></a>作法：查看下一個訊息
透過呼叫 **peek\_messages** 方法，您可以在佇列前面查看訊息，而無需將它從佇列中移除。 **peek\_messages** 預設會查看單一訊息。

```python
messages = queue_service.peek_messages('taskqueue')
for message in messages:
    print(message.content)
```

## <a name="how-to-dequeue-messages"></a>作法：清除佇列訊息
您的程式碼可以使用兩個步驟來將訊息從佇列中移除。 呼叫 **get\_messages** 時，您預設會取得佇列中的下一個訊息。 從 **get\_messages** 傳回的訊息，對於從此佇列讀取訊息的任何其他程式碼而言將會是不可見的。 依預設，此訊息會維持 30 秒的不可見狀態。 若要完成從佇列中移除訊息，您還必須呼叫 **delete\_message**。 這個移除訊息的兩步驟程序可確保您的程式碼因為硬體或軟體故障而無法處理訊息時，另一個程式碼的執行個體可以取得相同訊息並再試一次。 您的程式碼會在處理完訊息之後立即呼叫 **delete\_message**。

```python
messages = queue_service.get_messages('taskqueue')
for message in messages:
    print(message.content)
    queue_service.delete_message('taskqueue', message.id, message.pop_receipt)
```

自訂從佇列中擷取訊息的方法有兩種。
首先，您可以取得一批訊息 (最多 32 個)。 其次，您可以設定較長或較短的可見度逾時，讓您的程式碼有較長或較短的時間可以完全處理每個訊息。 下列程式碼範例將使用 **get\_messages** 方法，在一次呼叫中取得 16 個訊息。 接著它會使用 for 迴圈處理每個訊息。 它也會將可見度逾時設定為每個訊息五分鐘。

```python
messages = queue_service.get_messages('taskqueue', num_messages=16, visibility_timeout=5*60)
for message in messages:
    print(message.content)
    queue_service.delete_message('taskqueue', message.id, message.pop_receipt)        
```

## <a name="how-to-change-the-contents-of-a-queued-message"></a>作法：變更佇列訊息的內容
您可以在佇列中就地變更訊息內容。 如果訊息代表工作作業，則您可以使用此功能來更新工作作業的狀態。 下方的程式碼使用 **update\_message** 方法來更新訊息。 可見度逾時設定為 0，表示會立即顯示訊息，並且會更新內容。

```python
messages = queue_service.get_messages('taskqueue')
for message in messages:
    queue_service.update_message('taskqueue', message.id, message.pop_receipt, 0, u'Hello World Again')
```

## <a name="how-to-get-the-queue-length"></a>作法：取得佇列長度
您可以取得佇列中的估計訊息數目。 **get\_queue\_metadata** 方法會要求佇列服務傳回佇列的中繼資料和 **approximate_message_count**。 由於佇列服務在回應您的要求之後可以新增或移除訊息，此結果僅為近似值。

```python
metadata = queue_service.get_queue_metadata('taskqueue')
count = metadata.approximate_message_count
```

## <a name="how-to-delete-a-queue"></a>作法：刪除佇列
若要刪除佇列及其內含的所有訊息，請呼叫 **delete\_queue** 方法。

```python
queue_service.delete_queue('taskqueue')
```

## <a name="next-steps"></a>後續步驟
了解佇列儲存體的基本概念之後，請使用下列連結深入了解。

* [Python 開發人員中心](https://azure.microsoft.com/develop/python/)
* [Azure 儲存體服務 REST API](https://msdn.microsoft.com/library/azure/dd179355)

[Azure Storage Team Blog]: https://blogs.msdn.com/b/windowsazurestorage/
[Microsoft Azure Storage SDK for Python]: https://github.com/Azure/azure-storage-python
