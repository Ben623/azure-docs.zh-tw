---
title: Azure Cosmos 模擬器下載和版本資訊
description: 取得不同版本的 Azure Cosmos 模擬器版本資訊和下載資訊。
ms.service: cosmos-db
ms.topic: tutorial
author: markjbrown
ms.author: mjbrown
ms.date: 06/20/2019
ms.openlocfilehash: eb73551c4226df8bc20428558720c0ebbfff96d9
ms.sourcegitcommit: 9405aad7e39efbd8fef6d0a3c8988c6bf8de94eb
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/05/2019
ms.locfileid: "74873585"
---
# <a name="azure-cosmos-emulator---release-notes-and-download-information"></a>Azure Cosmos 模擬器 - 版本資訊和下載資訊

本文顯示 Azure Cosmos 模擬器版本資訊，以及每個版本中所做的功能更新清單。 它也會列出最新版本的模擬器以供下載和使用。

## <a name="download"></a>下載

| | |
|---------|---------|
|**MSI 下載**|[Microsoft 下載中心](https://aka.ms/cosmosdb-emulator)|
|**開始使用**|[使用 Azure Cosmos 模擬器在本機開發](local-emulator.md)|

## <a name="release-notes"></a>版本資訊

### <a name="270"></a>2.7.0

- 此版本修正了以下迴歸：使用者在使用 .NET Core 或 x86 .NET 型用戶端時，無法從模擬器對 SQL API 帳戶執行查詢。

### <a name="246"></a>2.4.6

- 從 2019 年 7 月起，此版本會提供與 Azure Cosmos 服務中功能同等的內容，而[使用 Azure Cosmos 模擬器在本機進行開發](local-emulator.md)中會敘述例外狀況。 其中也會修正下列狀況的數個相關錯誤 (bug)：透過命令列叫用模擬器時造成模擬器關閉，以及 SDK 用戶端使用直接模式連線時，內部 IP 位址遭到覆寫。

### <a name="243"></a>2.4.3

- 停用依預設啟動 MongoDB 服務。 只有 SQL 端點預設為啟用。 使用者必須使用模擬器的 "/EnableMongoDbEndpoint" 命令列選項，以手動啟動端點。 如同所有其他服務端點，如 Gremlin、Cassandra 和資料表。
- 已修正模擬器中的錯誤 (bug)，當從 “/AllowNetworkAccess” 開頭，其中 Gremlin、Cassandra 和資料表端點未正確處理來自外部用戶端的要求。
- 將直接連線連接埠新增到防火牆規則設定中。

### <a name="240"></a>2.4.0

- 已修正當主機電腦上有網路監視應用程式 (如 Pulse Client) 時，模擬器無法啟動的問題。
