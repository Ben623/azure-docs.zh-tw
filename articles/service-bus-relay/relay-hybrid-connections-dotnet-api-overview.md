---
title: Azure 轉送 .NET Standard API 概觀 | Microsoft Docs
description: 本文摘要說明 Azure 轉送混合式連線 .NET Standard API 的一些重要概述。
services: service-bus-relay
documentationcenter: na
author: spelluru
manager: timlt
editor: ''
ms.assetid: b1da9ac1-811b-4df7-a22c-ccd013405c40
ms.service: service-bus-relay
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/23/2018
ms.author: spelluru
ms.openlocfilehash: 18eaf2d2daae817107be6cdb0da9359bb5f9b4e9
ms.sourcegitcommit: 38b11501526a7997cfe1c7980d57e772b1f3169b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/22/2020
ms.locfileid: "76514530"
---
# <a name="azure-relay-hybrid-connections-net-standard-api-overview"></a>Azure 轉送混合式連線 .NET Standard API 概觀

本文將摘要列出一些主要 Azure 轉送混合式連線 .NET Standard [用戶端 API](/dotnet/api/microsoft.azure.relay)。
  
## <a name="relay-connection-string-builder-class"></a>轉送連接字串產生器類別

[RelayConnectionStringBuilder][RelayConnectionStringBuilder]類別會格式化轉送混合式連接特定的連接字串。 您可以使用它來驗證連接字串的格式，或從頭開始建置連接字串。 如需範例，請參閱下列程式碼：

```csharp
var endpoint = "[Relay namespace]";
var entityPath = "[Name of the Hybrid Connection]";
var sharedAccessKeyName = "[SAS key name]";
var sharedAccessKey = "[SAS key value]";

var connectionStringBuilder = new RelayConnectionStringBuilder()
{
    Endpoint = endpoint,
    EntityPath = entityPath,
    SharedAccessKeyName = sasKeyName,
    SharedAccessKey = sasKeyValue
};
```

您也可以直接將連接字串傳遞到 `RelayConnectionStringBuilder` 方法。 這項作業可讓您確認連接字串格式有效。 如果有任何參數無效，建構函式會產生 `ArgumentException`。

```csharp
var myConnectionString = "[RelayConnectionString]";
// Declare the connectionStringBuilder so that it can be used outside of the loop if needed
RelayConnectionStringBuilder connectionStringBuilder;
try
{
    // Create the connectionStringBuilder using the supplied connection string
    connectionStringBuilder = new RelayConnectionStringBuilder(myConnectionString);
}
catch (ArgumentException ae)
{
    // Perform some error handling
}
```

## <a name="hybrid-connection-stream"></a>混合式連線串流

[HybridConnectionStream][HCStream]類別是主要物件，用來傳送和接收來自 Azure 轉送端點的資料，不論您是使用[HybridConnectionClient][HCClient]或[HybridConnectionListener][HCListener]。

### <a name="getting-a-hybrid-connection-stream"></a>取得混合式連線串流

#### <a name="listener"></a>接聽程式

使用[HybridConnectionListener][HCListener]物件，您可以取得 `HybridConnectionStream` 物件，如下所示：

```csharp
// Use the RelayConnectionStringBuilder to get a valid connection string
var listener = new HybridConnectionListener(csb.ToString());
// Open a connection to the Relay endpoint
await listener.OpenAsync();
// Get a `HybridConnectionStream`
var hybridConnectionStream = await listener.AcceptConnectionAsync();
```

#### <a name="client"></a>Client

使用[HybridConnectionClient][HCClient]物件，您可以取得 `HybridConnectionStream` 物件，如下所示：

```csharp
// Use the RelayConnectionStringBuilder to get a valid connection string
var client = new HybridConnectionClient(csb.ToString());
// Open a connection to the Relay endpoint and get a `HybridConnectionStream`
var hybridConnectionStream = await client.CreateConnectionAsync();
```

### <a name="receiving-data"></a>接收資料

[HybridConnectionStream][HCStream]類別可啟用雙向通訊。 在大部分的案例中，您會持續接收來自串流的資料。 如果您從串流讀取文字，建議您使用 [StreamReader](https://msdn.microsoft.com/library/system.io.streamreader(v=vs.110).aspx) 物件，這可讓您更容易剖析資料。 例如，您可以讀取文字 (而非`byte[]`) 格式的資料。

下列程式碼會從串流讀取個別的文字行，直到要求取消：

```csharp
// Create a CancellationToken, so that we can cancel the while loop
var cancellationToken = new CancellationToken();
// Create a StreamReader from the 'hybridConnectionStream`
var streamReader = new StreamReader(hybridConnectionStream);

while (!cancellationToken.IsCancellationRequested)
{
    // Read a line of input until a newline is encountered
    var line = await streamReader.ReadLineAsync();
    if (string.IsNullOrEmpty(line))
    {
        // If there's no input data, we will signal that 
        // we will no longer send data on this connection
        // and then break out of the processing loop.
        await hybridConnectionStream.ShutdownAsync(cancellationToken);
        break;
    }
}
```

### <a name="sending-data"></a>傳送資料

一旦建立連線，您可以傳送訊息到轉送端點。 因為連線物件會繼承[串流](https://msdn.microsoft.com/library/system.io.stream(v=vs.110).aspx)，請傳送 `byte[]` 格式的資料。 下列範例示範如何執行：

```csharp
var data = Encoding.UTF8.GetBytes("hello");
await clientConnection.WriteAsync(data, 0, data.Length);
```

不過，如果您想要直接傳送文字，不需每次都將字串編碼，您可以使用 [StreamWriter](https://msdn.microsoft.com/library/system.io.streamwriter(v=vs.110).aspx) 物件來包裝 `hybridConnectionStream` 物件。

```csharp
// The StreamWriter object only needs to be created once
var textWriter = new StreamWriter(hybridConnectionStream);
await textWriter.WriteLineAsync("hello");
```

## <a name="next-steps"></a>後續步驟

若要深入了解 Azure 轉送，請造訪下列連結：

* [Microsoft.Azure.Relay reference](/dotnet/api/microsoft.azure.relay)
* [什麼是 Azure 轉送？](relay-what-is-it.md)
* [可用的轉送 API](relay-api-overview.md)

[RelayConnectionStringBuilder]: /dotnet/api/microsoft.azure.relay.relayconnectionstringbuilder
[HCStream]: /dotnet/api/microsoft.azure.relay.hybridconnectionstream
[HCClient]: /dotnet/api/microsoft.azure.relay.hybridconnectionclient
[HCListener]: /dotnet/api/microsoft.azure.relay.hybridconnectionlistener