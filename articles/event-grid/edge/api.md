---
title: REST API-Azure 事件方格 IoT Edge |Microsoft Docs
description: IoT Edge 上的事件方格上 REST API。
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 10/03/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: ee2b3a35b6f1817b89541a31d0bde4adf00ade2a
ms.sourcegitcommit: 92d42c04e0585a353668067910b1a6afaf07c709
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/28/2019
ms.locfileid: "72992531"
---
# <a name="rest-api"></a>REST API
本文說明 Azure 事件方格在 IoT Edge 上的 REST Api

## <a name="common-api-behavior"></a>通用 API 行為

### <a name="base-url"></a>基底 URL
IoT Edge 上的事件方格具有透過 HTTP （埠5888）和 HTTPS （埠4438）公開的下列 Api。

* HTTP： http://eventgridmodule:5888 的基底 URL
* HTTPS 的基底 URL： https://eventgridmodule:4438

### <a name="request-query-string"></a>要求查詢字串
所有 API 要求都需要下列查詢字串參數：

```?api-version=2019-01-01-preview```

### <a name="request-content-type"></a>要求內容類型
所有 API 要求都必須具有**content-type**。

如果是**EventGridSchema**或**CustomSchema**，content-type 的值可以是下列其中一個值：

```Content-Type: application/json```

```Content-Type: application/json; charset=utf-8```

如果是結構化模式的**CloudEventSchemaV1_0** ，content-type 的值可以是下列其中一個值：

```Content-Type: application/cloudevents+json```
    
```Content-Type: application/cloudevents+json; charset=utf-8```
    
```Content-Type: application/cloudevents-batch+json```
    
```Content-Type: application/cloudevents-batch+json; charset=utf-8```

如果是二進位模式的**CloudEventSchemaV1_0** ，請參閱[檔](https://github.com/cloudevents/spec/blob/master/http-protocol-binding.md)以取得詳細資料。

### <a name="error-response"></a>錯誤回應
所有 Api 都會傳回具有下列承載的錯誤：

```json
{
    "error":
    {
        "code": "<HTTP STATUS CODE>",
        "details": 
        {
            "code": "<Detailed Error Code>",
            "message": "..."
        }
    }
}
```

## <a name="manage-topics"></a>管理主題

### <a name="put-topic-create--update"></a>Put 主題（建立/更新）

**要求**： ``` PUT /topics/<topic_name>?api-version=2019-01-01-preview ```

裝載：

```json
    {
        "name": "<topic_name>", // optional, inferred from URL. If specified must match URL topic_name
        "properties":
        {
            "inputSchema": "EventGridSchema | CustomEventSchema | CloudEventSchemaV1_0" // optional
        }
    }
```

**回應**： HTTP 200

裝載：

```json
{
    "id": "/iotHubs/<iot_hub_name>/devices/<iot_edge_device_id>/modules/<eventgrid_module_name>/topics/<topic_name>",
    "name": "<topic_name>",
    "type": "Microsoft.EventGrid/topics",
    "properties":
    {
        "endpoint": "<get_request_base_url>/topics/<topic_name>/events?api-version=2019-01-01-preview",
        "inputSchema": "EventGridSchema | CustomEventSchema | CloudEventSchemaV1_0" // populated with EventGridSchema if not explicitly specified in PUT request
    }
}
```

### <a name="get-topic"></a>取得主題

**要求**： ``` GET /topics/<topic_name>?api-version=2019-01-01-preview ```

**回應**： HTTP 200

裝載：
```json
{
    "id": "/iotHubs/<iot_hub_name>/devices/<iot_edge_device_id>/modules/<eventgrid_module_name>/topics/<topic_name>",
    "name": "<topic_name>",
    "type": "Microsoft.EventGrid/topics",
    "properties":
    {
        "endpoint": "<request_base_url>/topics/<topic_name>/events?api-version=2019-01-01-preview",
        "inputSchema": "EventGridSchema | CustomEventSchema | CloudEventSchemaV1_0"
    }
}
```

### <a name="get-all-topics"></a>取得所有主題

**要求**： ``` GET /topics?api-version=2019-01-01-preview ```

**回應**： HTTP 200

裝載：
```json
[
    {
        "id": "/iotHubs/<iot_hub_name>/devices/<iot_edge_device_id>/modules/<eventgrid_module_name>/topics/<topic_name>",
        "name": "<topic_name>",
        "type": "Microsoft.EventGrid/topics",
        "properties":
        {
            "endpoint": "<request_base_url>/topics/<topic_name>/events?api-version=2019-01-01-preview",
            "inputSchema": "EventGridSchema | CustomEventSchema | CloudEventSchemaV1_0"
        }
    },
    {
        "id": "/iotHubs/<iot_hub_name>/devices/<iot_edge_device_id>/modules/<eventgrid_module_name>/topics/<topic_name>",
        "name": "<topic_name>",
        "type": "Microsoft.EventGrid/topics",
        "properties":
        {
            "endpoint": "<request_base_url>/topics/<topic_name>/events?api-version=2019-01-01-preview",
            "inputSchema": "EventGridSchema | CustomEventSchema | CloudEventSchemaV1_0"
        }
    }
]
```

### <a name="delete-topic"></a>刪除主題

**要求**： ``` DELETE /topics/<topic_name>?api-version=2019-01-01-preview ```

**回應**： HTTP 200，空白承載

## <a name="manage-event-subscriptions"></a>管理事件訂閱
本節中的範例使用 `EndpointType=Webhook;`。 下一節中 `EndpointType=EdgeHub / EndpointType=EventGrid` 的 json 範例。 

### <a name="put-event-subscription-create--update"></a>Put 事件訂用帳戶（建立/更新）

**要求**： ``` PUT /topics/<topic_name>/eventSubscriptions/<subscription_name>?api-version=2019-01-01-preview ```

裝載：
```json
{
    "name": "<subscription_name>", // optional, inferred from URL. If specified must match URL subscription_name
    "properties":
    {
        "topicName": "<topic_name>", // optional, inferred from URL. If specified must match URL topic_name
        "eventDeliverySchema": "EventGridSchema | CustomEventSchema | CloudEventSchemaV1_0", // optional
        "retryPolicy":  //optional
        {
            "eventExpiryInMinutes": 120,
            "maxDeliveryAttempts": 50
        },
        "destination":
        {
            "endpointType": "WebHook",
            "properties":
            {
                "endpointUrl": "<webhook_url>",
                "maxEventsPerBatch": 10, // optional
                "preferredBatchSizeInKilobytes": 1033 // optional
            }
        },
        "filter": // optional
        {
            "subjectBeginsWith": "...",
            "subjectEndsWith": "...",
            "isSubjectCaseSensitive": true|false,
            "includedEventTypes": ["...", "..."],
            "advancedFilters":
            [
                {
                    "OperatorType": "BoolEquals",
                    "Key": "...",
                    "Value": "..."
                },
                {
                    "OperatorType": "NumberLessThan",
                    "Key": "...",
                    "Value": <number>
                },
                {
                    "OperatorType": "NumberGreaterThan",
                    "Key": "...",
                    "Value": <number>
                },
                {
                    "OperatorType": "NumberLessThanOrEquals",
                    "Key": "...",
                    "Value": <number>
                },
                {
                    "OperatorType": "NumberGreaterThanOrEquals",
                    "Key": "...",
                    "Value": <number>
                },
                {
                    "OperatorType": "NumberIn",
                    "Key": "...",
                    "Values": [<number>, <number>, <number>]
                },
                {
                    "OperatorType": "NumberNotIn",
                    "Key": "...",
                    "Values": [<number>, <number>, <number>]
                },
                {
                    "OperatorType": "StringIn",
                    "Key": "...",
                    "Values": ["...", "...", "..."]
                },
                {
                    "OperatorType": "StringNotIn",
                    "Key": "...",
                    "Values": ["...", "...", "..."]
                },
                {
                    "OperatorType": "StringBeginsWith",
                    "Key": "...",
                    "Values": ["...", "...", "..."]
                },
                {
                    "OperatorType": "StringEndsWith",
                    "Key": "...",
                    "Values": ["...", "...", "..."]
                },
                {
                    "OperatorType": "StringContains",
                    "Key": "...",
                    "Values": ["...", "...", "..."]
                }
            ]
        }
    }
}
```

**回應**： HTTP 200

裝載：

```json
{
    "id": "/iotHubs/<iot_hub_name>/devices/<iot_edge_device_id>/modules/<eventgrid_module_name>/topics/<topic_name>/eventSubscriptions/<subscription_name>",
    "name": "<subscription_name>",
    "type": "Microsoft.EventGrid/eventSubscriptions",
    "properties":
    {
        "topicName": "<topic_name>",
        "eventDeliverySchema": "EventGridSchema | CustomEventSchema  | CloudEventSchemaV1_0", // populated with EventGridSchema if not explicitly specified in PUT request
        "retryPolicy":  // only populated if specified in the PUT request
        {
            "eventExpiryInMinutes": 120,
            "maxDeliveryAttempts": 50
        },
        "destination":
        {
            "endpointType": "WebHook",
            "properties":
            {
                "endpointUrl": "<webhook_url>",
                "maxEventsPerBatch": 10, // optional
                "preferredBatchSizeInKilobytes": 1033 // optional
            }
        },
        "filter": // only populated if specified in the PUT request 
        {
            "subjectBeginsWith": "...",
            "subjectEndsWith": "...",
            "isSubjectCaseSensitive": true|false,
            "includedEventTypes": ["...", "..."],
            "advancedFilters":
            [
                {
                    "OperatorType": "BoolEquals",
                    "Key": "...",
                    "Value": "..."
                },
                {
                    "OperatorType": "NumberLessThan",
                    "Key": "...",
                    "Value": <number>
                },
                {
                    "OperatorType": "NumberGreaterThan",
                    "Key": "...",
                    "Value": <number>
                },
                {
                    "OperatorType": "NumberLessThanOrEquals",
                    "Key": "...",
                    "Value": <number>
                },
                {
                    "OperatorType": "NumberGreaterThanOrEquals",
                    "Key": "...",
                    "Value": <number>
                },
                {
                    "OperatorType": "NumberIn",
                    "Key": "...",
                    "Values": [<number>, <number>, <number>]
                },
                {
                    "OperatorType": "NumberNotIn",
                    "Key": "...",
                    "Values": [<number>, <number>, <number>]
                },
                {
                    "OperatorType": "StringIn",
                    "Key": "...",
                    "Values": ["...", "...", "..."]
                },
                {
                    "OperatorType": "StringNotIn",
                    "Key": "...",
                    "Values": ["...", "...", "..."]
                },
                {
                    "OperatorType": "StringBeginsWith",
                    "Key": "...",
                    "Values": ["...", "...", "..."]
                },
                {
                    "OperatorType": "StringEndsWith",
                    "Key": "...",
                    "Values": ["...", "...", "..."]
                },
                {
                    "OperatorType": "StringContains",
                    "Key": "...",
                    "Values": ["...", "...", "..."]
                }
            ]
        }
    }
}
```


### <a name="get-event-subscription"></a>取得事件訂用帳戶

**要求**： ``` GET /topics/<topic_name>/eventSubscriptions/<subscription_name>?api-version=2019-01-01-preview ```

**回應**： HTTP 200

裝載：
```json
{
    "id": "/iotHubs/<iot_hub_name>/devices/<iot_edge_device_id>/modules/<eventgrid_module_name>/topics/<topic_name>/eventSubscriptions/<subscription_name>",
    "name": "<subscription_name>",
    "type": "Microsoft.EventGrid/eventSubscriptions",
    "properties":
    {
        "topicName": "<topic_name>",
        "eventDeliverySchema": "EventGridSchema | CustomEventSchema  | CloudEventSchemaV1_0", // populated with EventGridSchema if not explicitly specified in PUT request
        "retryPolicy":  // only populated if specified in the PUT request
        {
            "eventExpiryInMinutes": 120,
            "maxDeliveryAttempts": 50
        },
        "destination":
        {
            "endpointType": "WebHook",
            "properties":
            {
                "endpointUrl": "<webhook_url>",
                "maxEventsPerBatch": 10, // optional
                "preferredBatchSizeInKilobytes": 1033 // optional
            }
        },
        "filter": // only populated if specified in the PUT request 
        {
            "subjectBeginsWith": "...",
            "subjectEndsWith": "...",
            "isSubjectCaseSensitive": true|false,
            "includedEventTypes": ["...", "..."],
            "advancedFilters":
            [
                {
                    "OperatorType": "BoolEquals",
                    "Key": "...",
                    "Value": "..."
                },
                {
                    "OperatorType": "NumberLessThan",
                    "Key": "...",
                    "Value": <number>
                },
                {
                    "OperatorType": "NumberGreaterThan",
                    "Key": "...",
                    "Value": <number>
                },
                {
                    "OperatorType": "NumberLessThanOrEquals",
                    "Key": "...",
                    "Value": <number>
                },
                {
                    "OperatorType": "NumberGreaterThanOrEquals",
                    "Key": "...",
                    "Value": <number>
                },
                {
                    "OperatorType": "NumberIn",
                    "Key": "...",
                    "Values": [<number>, <number>, <number>]
                },
                {
                    "OperatorType": "NumberNotIn",
                    "Key": "...",
                    "Values": [<number>, <number>, <number>]
                },
                {
                    "OperatorType": "StringIn",
                    "Key": "...",
                    "Values": ["...", "...", "..."]
                },
                {
                    "OperatorType": "StringNotIn",
                    "Key": "...",
                    "Values": ["...", "...", "..."]
                },
                {
                    "OperatorType": "StringBeginsWith",
                    "Key": "...",
                    "Values": ["...", "...", "..."]
                },
                {
                    "OperatorType": "StringEndsWith",
                    "Key": "...",
                    "Values": ["...", "...", "..."]
                },
                {
                    "OperatorType": "StringContains",
                    "Key": "...",
                    "Values": ["...", "...", "..."]
                }
            ]
        }
    }
}
```

### <a name="get-event-subscriptions"></a>取得事件訂閱

**要求**： ``` GET /topics/<topic_name>/eventSubscriptions?api-version=2019-01-01-preview ```

**回應**： HTTP 200

裝載：
```json
[
    {
        // same event-subscription json as that returned from Get-EventSubscription above
    },
    {
    },
    ...
]
```

### <a name="delete-event-subscription"></a>刪除事件訂用帳戶

**要求**： ``` DELETE /topics/<topic_name>/eventSubscriptions/<subscription_name>?api-version=2019-01-01-preview ```

**回應**： HTTP 200，沒有承載


## <a name="publish-events-api"></a>發行事件 API

### <a name="send-batch-of-events-in-event-grid-schema"></a>傳送事件批次（在事件方格架構中）

**要求**： ``` POST /topics/<topic_name>/events?api-version=2019-01-01-preview ```

```json
[
    {
        "id": "<user-defined-event-id>",
        "topic": "<topic_name>",
        "subject": "",
        "eventType": "",
        "eventTime": ""
        "dataVersion": "",
        "metadataVersion": "1",
        "data": 
            ...
    }
]
```

**回應**： HTTP 200，空白承載


**裝載欄位描述**
- ```Id``` 是強制的。 它可以是由呼叫者填入的任何字串值。 事件方格不會執行任何重複的偵測，或在此欄位上強制執行任何語義。
- ```Topic``` 是選擇性的，但如果指定的必須符合要求 URL 中的 topic_name
- ```Subject``` 是必要的，可以是任何字串值
- ```EventType``` 是必要的，可以是任何字串值
- ```EventTime``` 是必要的，但不會進行驗證，但應該是適當的日期時間。
- ```DataVersion``` 是必要的
- ```MetadataVersion``` 是選擇性的（如果指定的話）必須是值為的字串 ```"1"```
- ```Data``` 是選擇性的，而且可以是任何 JSON 權杖（數位、字串、布林值、陣列、物件）

### <a name="send-batch-of-events-in-custom-schema"></a>傳送事件批次（在自訂架構中）

**要求**： ``` POST /topics/<topic_name>/events?api-version=2019-01-01-preview ```

```json
[
    {
        ...
    }
]
```

**回應**： HTTP 200，空白承載


**裝載限制**
- 必須是事件陣列。
- 每個陣列專案都必須是 JSON 物件。
- 沒有其他條件約束（裝載大小除外）。

## <a name="examples"></a>範例

### <a name="set-up-topic-with-eventgrid-schema"></a>使用 EventGrid 架構設定主題
設定一個主題，要求在**eventgridschema**中發行事件。

```json
    {
        "name": "myeventgridtopic",
        "properties":
        {
            "inputSchema": "EventGridSchema"
        }
    }
```

### <a name="set-up-topic-with-custom-schema"></a>使用自訂架構設定主題
設定一個主題，要求在 `customschema`中發佈事件。

```json
    {
        "name": "mycustomschematopic",
        "properties":
        {
            "inputSchema": "CustomSchema"
        }
    }
```

### <a name="set-up-topic-with-cloud-event-schema"></a>使用雲端事件架構設定主題
設定一個主題，要求在 `cloudeventschema`中發佈事件。

```json
    {
        "name": "mycloudeventschematopic",
        "properties":
        {
            "inputSchema": "CloudEventSchemaV1_0"
        }
    }
```

### <a name="set-up-webhook-as-destination-events-to-be-delivered-in-eventgridschema"></a>將 WebHook 設定為目的地，要在 eventgridschema 中傳遞的事件
使用此目的地類型，將事件傳送至任何其他模組（裝載 HTTP 端點）或網路/網際網路上的任何 HTTP 可定址端點。

```json
{
    "properties":
    {
        "destination":
        {
            "endpointType": "WebHook",
            "properties":
            {
                "endpointUrl": "<webhook_url>",
                "eventDeliverySchema": "eventgridschema",
            }
        }
    }
}
```

`endpointUrl` 屬性的條件約束：
- 必須為非 null。
- 它必須是絕對 URL。
- 如果 EventGridModule 設定中的 outbound__webhook__HTTPsOnly 設定為 true，則其必須為僅限 HTTPS。
- 如果 outbound__webhook__HTTPsOnly 設為 false，則可以是 HTTP 或 HTTPS。

`eventDeliverySchema` 屬性的條件約束：
- 它必須符合訂閱主題的輸入架構。
- 它可以是 null。 預設為主題的輸入架構。

### <a name="set-up-iot-edge-as-destination"></a>將 IoT Edge 設定為目的地

使用此目的地將事件傳送至 IoT Edge 中樞，並受限於 Edge 中樞的路由/篩選/轉送子系統。

```json
{
    "properties":
    {
        "destination":
        {
            "endpointType": "EdgeHub",
            "properties":
            {
                "outputName": "<eventgridmodule_output_port_name>"
            }
        }
    }
}
```

### <a name="set-up-event-grid-cloud-as-destination"></a>將事件方格雲端設定為目的地

使用此目的地將事件傳送至雲端（Azure）中的事件方格。 在 edge 上建立事件訂閱之前，您必須先在雲端中設定應傳送事件的使用者主題。

```json
{
    "properties":
    {
        "destination":
        {
            "endpointType": "EventGrid",
            "properties":
            {
                "endpointUrl": "<eventgrid_user_topic_url>",
                "sasKey": "<user_topic_sas_key>",
                "topicName": "<new value to populate in forwarded EventGridEvent.Topic>" // if not specified, the Topic field on every event gets nulled out before being sent to Azure Event Grid
            }
        }
    }
}
```

EndpointUrl
- 必須為非 null。
- 它必須是絕對 URL。
- 必須在要求 URL 路徑中定義路徑 `/api/events`。
- 查詢字串中必須有 `api-version=2018-01-01`。
- 如果 outbound__eventgrid__HTTPsOnly 在 EventGridModule 設定中設定為 true （預設值為 true），則其必須為僅限 HTTPS。
- 如果 outbound__eventgrid__HTTPsOnly 設定為 false，則可以是 HTTP 或 HTTPS。
- 如果 outbound__eventgrid__allowInvalidHostnames 設定為 false （預設為 false），它必須以下列其中一個端點為目標：
   - `eventgrid.azure.net`
   - `eventgrid.azure.us`
   - `eventgrid.azure.cn`

SasKey:
- 必須為非 null。

TopicName:
- 如果 EventDeliverySchema 設定為 EventGridSchema，則此欄位的值會放入每個事件的 [主題] 欄位，然後再轉送到雲端中的事件方格。
- 如果 EventDeliverySchema 設定為 CustomEventSchema，則會忽略這個屬性，而自訂事件裝載就會完全依照收到的方式轉送。
