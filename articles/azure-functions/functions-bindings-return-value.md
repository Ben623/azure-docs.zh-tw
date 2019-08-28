---
title: 使用來自 Azure 函式的傳回值
description: 瞭解如何管理 Azure Functions 的傳回值
services: functions
documentationcenter: na
author: craigshoemaker
manager: gwallace
ms.service: azure-functions
ms.topic: reference
ms.date: 01/14/2019
ms.author: cshoe
ms.openlocfilehash: 1ea7ec0444ba80d3494afba77ad9d7fdabd5f982
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/28/2019
ms.locfileid: "70086416"
---
# <a name="using-the-azure-function-return-value"></a>使用 Azure 函數傳回值

本文說明傳回值在函式內的運作方式。

在具有傳回值的語言中, 您可以將函式[輸出](./functions-triggers-bindings.md#binding-direction)系結系結至傳回值:

* 在 C# 類別庫中，將輸出繫結屬性套用至方法傳回值。
* 在其他語言中，將 function.json 中的 `name` 屬性設定為 `$return`。

如果有多個輸出繫結，請只對其中一個使用傳回值。

在 C# 和 C# 指令碼中，傳送資料到輸出繫結的方式可以是 `out` 參數和[收集器物件](functions-reference-csharp.md#writing-multiple-output-values)。

請參閱示範傳回值用法的特定語言範例：

* [C#](#c-example)
* [C# 指令碼 (.csx)](#c-script-example)
* [F#](#f-example)
* [JavaScript](#javascript-example)
* [Python](#python-example)

## <a name="c-example"></a>C# 範例

以下是使用輸出繫結傳回值的 C# 程式碼，接著則是非同步範例：

```cs
[FunctionName("QueueTrigger")]
[return: Blob("output-container/{id}")]
public static string Run([QueueTrigger("inputqueue")]WorkItem input, ILogger log)
{
    string json = string.Format("{{ \"id\": \"{0}\" }}", input.Id);
    log.LogInformation($"C# script processed queue message. Item={json}");
    return json;
}
```

```cs
[FunctionName("QueueTrigger")]
[return: Blob("output-container/{id}")]
public static Task<string> Run([QueueTrigger("inputqueue")]WorkItem input, ILogger log)
{
    string json = string.Format("{{ \"id\": \"{0}\" }}", input.Id);
    log.LogInformation($"C# script processed queue message. Item={json}");
    return Task.FromResult(json);
}
```

## <a name="c-script-example"></a>C# 指令碼範例

以下是 function.json 檔案中的輸出繫結：

```json
{
    "name": "$return",
    "type": "blob",
    "direction": "out",
    "path": "output-container/{id}"
}
```

以下是 C# 指令程式碼，接著則是非同步範例：

```cs
public static string Run(WorkItem input, ILogger log)
{
    string json = string.Format("{{ \"id\": \"{0}\" }}", input.Id);
    log.LogInformation($"C# script processed queue message. Item={json}");
    return json;
}
```

```cs
public static Task<string> Run(WorkItem input, ILogger log)
{
    string json = string.Format("{{ \"id\": \"{0}\" }}", input.Id);
    log.LogInformation($"C# script processed queue message. Item={json}");
    return Task.FromResult(json);
}
```

## <a name="f-example"></a>F# 範例

以下是 function.json 檔案中的輸出繫結：

```json
{
    "name": "$return",
    "type": "blob",
    "direction": "out",
    "path": "output-container/{id}"
}
```

以下是 F# 程式碼：

```fsharp
let Run(input: WorkItem, log: ILogger) =
    let json = String.Format("{{ \"id\": \"{0}\" }}", input.Id)   
    log.LogInformation(sprintf "F# script processed queue message '%s'" json)
    json
```

## <a name="javascript-example"></a>JavaScript 範例

以下是 function.json 檔案中的輸出繫結：

```json
{
    "name": "$return",
    "type": "blob",
    "direction": "out",
    "path": "output-container/{id}"
}
```

在 JavaScript 中，傳回值是放在 `context.done` 的第二個參數中：

```javascript
module.exports = function (context, input) {
    var json = JSON.stringify(input);
    context.log('Node.js script processed queue message', json);
    context.done(null, json);
}
```

## <a name="python-example"></a>Python 範例

以下是 function.json 檔案中的輸出繫結：

```json
{
    "name": "$return",
    "type": "blob",
    "direction": "out",
    "path": "output-container/{id}"
}
```
以下是 Python 程式碼：

```python
def main(input: azure.functions.InputStream) -> str:
    return json.dumps({
        'name': input.name,
        'length': input.length,
        'content': input.read().decode('utf-8')
    })
```

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [處理 Azure Functions 系結錯誤](./functions-bindings-errors.md)
