---
title: EdgeAgent 和 EdgeHub 預期屬性參考 - Azure IoT Edge | Microsoft Docs
description: 檢閱 edgeAgent 和 edgeHub 模組對應項的特定屬性及其值
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 06/17/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 1ab45a6bde9ead69a7ea23dd095de84b8ff01334
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/24/2019
ms.locfileid: "74456698"
---
# <a name="properties-of-the-iot-edge-agent-and-iot-edge-hub-module-twins"></a>IoT Edge 代理程式和 IoT Edge 中樞模組對應項的屬性

IoT Edge 代理程式和 IoT Edge 中樞是構成 IoT Edge 執行階段的兩個模組。 如需模組各自執行哪些工作的相關資訊，請參閱[了解 Azure IoT Edge 執行階段及其架構](iot-edge-runtime.md)。 

本文提供執行階段模組對應項的所需屬性和報告屬性。 For more information on how to deploy modules on IoT Edge devices, see [Learn how to deploy modules and establish routes in IoT Edge](module-composition.md).

A module twin includes: 

* **所需屬性**。 The solution backend can set desired properties, and the module can read them. The module can also receive notifications of changes in the desired properties. Desired properties are used along with reported properties to synchronize module configuration or conditions.

* **報告屬性**。 The module can set reported properties, and the solution backend can read and query them. Reported properties are used along with desired properties to synchronize module configuration or conditions. 

## <a name="edgeagent-desired-properties"></a>EdgeAgent 的所需屬性

IoT Edge 代理程式的模組對應項稱為 `$edgeAgent`，並且會協調裝置與 IoT 中樞上執行之 IoT Edge 代理程式之間的通訊。 在單一裝置或規模部署時，在特定裝置上套用部署資訊清單時，會設定預期屬性。 

| 屬性 | 描述 | 必要項 |
| -------- | ----------- | -------- |
| schemaVersion | 必須為「1.0」 | 是 |
| runtime.type | 必須為「docker」 | 是 |
| runtime.settings.minDockerVersion | 此部署資訊清單需要設定為最小 Docker 版本 | 是 |
| runtime.settings.loggingOptions | stringified JSON，包含 IoT Edge 代理程式容器的記錄選項。 [Docker 記錄選項](https://docs.docker.com/engine/admin/logging/overview/) | 否 |
| runtime.settings.registryCredentials<br>.{registryId}.username | 容器登錄的使用者名稱。 Azure Container Registry 的使用者名稱通常是登錄名稱。<br><br> 非公用的任何模組映像都需要登錄認證。 | 否 |
| runtime.settings.registryCredentials<br>.{registryId}.password | 容器登錄的密碼。 | 否 |
| runtime.settings.registryCredentials<br>.{registryId}.address | 容器登錄的位址。 Azure Container Registry 的位址通常是 {登錄名稱}.azurecr.io。 | 否 |  
| systemModules.edgeAgent.type | 必須為「docker」 | 是 |
| systemModules.edgeAgent.settings.image | IoT Edge 代理程式映像的 URI。 目前，IoT Edge 代理程式無法自行更新。 | 是 |
| systemModules.edgeAgent.settings<br>.createOptions | stringified JSON，包含 IoT Edge 代理程式容器的建立選項。 [Docker 建立選項](https://docs.docker.com/engine/api/v1.32/#operation/ContainerCreate) | 否 |
| systemModules.edgeAgent.configuration.id | 部署此模組之部署的識別碼。 | 使用部署來套用資訊清單時，IoT 中樞會設定這個屬性。 非部署資訊清單的一部分。 |
| systemModules.edgeHub.type | 必須為「docker」 | 是 |
| systemModules.edgeHub.status | 必須為「執行中」 | 是 |
| systemModules.edgeHub.restartPolicy | 必須為「永遠」 | 是 |
| systemModules.edgeHub.settings.image | IoT Edge 中樞映像的 URI。 | 是 |
| systemModules.edgeHub.settings<br>.createOptions | stringified JSON，包含 IoT Edge 中樞容器的建立選項。 [Docker 建立選項](https://docs.docker.com/engine/api/v1.32/#operation/ContainerCreate) | 否 |
| systemModules.edgeHub.configuration.id | 部署此模組之部署的識別碼。 | 使用部署來套用資訊清單時，IoT 中樞會設定這個屬性。 非部署資訊清單的一部分。 |
| modules.{moduleId}.version | 使用者定義的字串，表示此模組的版本。 | 是 |
| modules.{moduleId}.type | 必須為「docker」 | 是 |
| modules.{moduleId}.status | {"running" \| "stopped"} | 是 |
| modules.{moduleId}.restartPolicy | {"never" \| "on-failure" \| "on-unhealthy" \| "always"} | 是 |
| modules.{moduleId}.imagePullPolicy | {"on-create" \| "never"} | 否 |
| modules.{moduleId}.settings.image | 模組映像的 URI。 | 是 |
| modules.{moduleId}.settings.createOptions | stringified JSON，包含模組容器的建立選項。 [Docker 建立選項](https://docs.docker.com/engine/api/v1.32/#operation/ContainerCreate) | 否 |
| modules.{moduleId}.configuration.id | 部署此模組之部署的識別碼。 | 使用部署來套用資訊清單時，IoT 中樞會設定這個屬性。 非部署資訊清單的一部分。 |

## <a name="edgeagent-reported-properties"></a>EdgeAgent 的報告屬性

IoT Edge 代理程式報告屬性包含三個主要部分資訊：

1. 上次出現預期屬性之應用程式的狀態；
2. 目前在裝置上執行之模組的狀態；由 IoT Edge 代理程式報告；以及
3. 目前在裝置上執行之預期屬性的複本。

This last piece of information, a copy of the current desired properties, is useful to tell whether the device has applied the latest desired properties or is still running a previous deployment manifest.

> [!NOTE]
> 可以使用 [IoT 中樞查詢語言](../iot-hub/iot-hub-devguide-query-language.md)查詢，以調查大規模部署的狀態時，IoT Edge 代理程式的報告屬性相當有用。 如需如何將 IoT Edge 代理程式屬性用於狀態的詳細資訊，請參閱[了解單一裝置或大規模的 IoT Edge 部署](module-deployment-monitoring.md)。

下表不包含從預期屬性複製的資訊。

| 屬性 | 描述 |
| -------- | ----------- |
| lastDesiredVersion | 此整數代表 IoT Edge 代理程式處理之所需屬性的最後版本。 |
| lastDesiredStatus.code | This status code refers to the last desired properties seen by the IoT Edge agent. 允許的值：`200` 成功、`400` 無效的設定、`412` 無效的結構描述版本、`417` 預期屬性是空的、`500` 失敗 |
| lastDesiredStatus.description | 狀態的文字描述 |
| deviceHealth | 如果所有模組的執行階段狀態為 `running`、`stopped` 時，則為 `healthy`，否則為 `unhealthy` |
| configurationHealth.{deploymentId}.health | 如果部署 {deploymentId} 設定之所有模組的執行階段狀態為 `running` 或 `stopped` 時，則為 `healthy`，否則為 `unhealthy` |
| runtime.platform.OS | 報告裝置上執行的作業系統 |
| runtime.platform.architecture | 報告裝置上的 CPU 架構 |
| systemModules.edgeAgent.runtimeStatus | IoT Edge 代理程式的報告狀態：{"running" \| "unhealthy"} |
| systemModules.edgeAgent.statusDescription | IoT Edge 代理程式之報告狀態的文字描述。 |
| systemModules.edgeHub.runtimeStatus | IoT Edge 中樞的狀態：{ "running" \| "stopped" \| "failed" \| "backoff" \| "unhealthy" } |
| systemModules.edgeHub.statusDescription | 狀況不良時，IoT Edge 中樞狀態的文字描述。 |
| systemModules.edgeHub.exitCode | The exit code reported by the IoT Edge hub container if the container exits |
| systemModules.edgeHub.startTimeUtc | IoT Edge 中樞的上次啟動時間 |
| systemModules.edgeHub.lastExitTimeUtc | IoT Edge 中樞的上次結束時間 |
| systemModules.edgeHub.lastRestartTimeUtc | IoT Edge 中樞的上次重新啟動時間 |
| systemModules.edgeHub.restartCount | 此模組重新啟動的次數，作為重新啟動原則的一部分。 |
| modules.{moduleId}.runtimeStatus | 模組的狀態：{ "running" \| "stopped" \| "failed" \| "backoff" \| "unhealthy" } |
| modules.{moduleId}.statusDescription | 狀況不良時，模組狀態的文字描述。 |
| modules.{moduleId}.exitCode | The exit code reported by the module container if the container exits |
| modules.{moduleId}.startTimeUtc | 模組的上次啟動時間 |
| modules.{moduleId}.lastExitTimeUtc | 模組的上次結束時間 |
| modules.{moduleId}.lastRestartTimeUtc | 模組的上次重新啟動時間 |
| modules.{moduleId}.restartCount | 此模組重新啟動的次數，作為重新啟動原則的一部分。 |

## <a name="edgehub-desired-properties"></a>EdgeHub 的所需屬性

IoT Edge 中樞的模組對應項稱為 `$edgeHub`，並且會協調裝置與 IoT 中樞上執行之 IoT Edge 中樞之間的通訊。 在單一裝置或規模部署時，在特定裝置上套用部署資訊清單時，會設定預期屬性。 

| 屬性 | 描述 | 部署資訊清單中的必要項目 |
| -------- | ----------- | -------- |
| schemaVersion | 必須為「1.0」 | 是 |
| routes.{routeName} | 字串，表示 IoT Edge 中樞路由。 For more information, see [Declare routes](module-composition.md#declare-routes). | `routes` 元素可以存在但為空白。 |
| storeAndForwardConfiguration.timeToLiveSecs | The time in seconds that IoT Edge hub keeps messages if disconnected from routing endpoints, whether IoT Hub or a local module. The value can be any positive integer. | 是 |

## <a name="edgehub-reported-properties"></a>EdgeHub 的報告屬性

| 屬性 | 描述 |
| -------- | ----------- |
| lastDesiredVersion | 此整數代表 IoT Edge 中樞處理之所需屬性的最後版本。 |
| lastDesiredStatus.code | The status code referring to last desired properties seen by the IoT Edge hub. 允許的值：`200` 成功、`400` 無效的設定、`500` 失敗 |
| lastDesiredStatus.description | Text description of the status. |
| clients.{device or moduleId}.status | 此裝置或模組的連線狀態。 可能的值 {"connected" \| "disconnected"}。 只有模組身分識別可以處於中斷連線狀態。 連線到 IoT Edge 中樞的下游裝置只會在連線時顯示。 |
| clients.{device or moduleId}.lastConnectTime | Last time the device or module connected. |
| clients.{device or moduleId}.lastDisconnectTime | Last time the device or module disconnected. |

## <a name="next-steps"></a>後續步驟

若要了解如何使用這些屬性建置部署資訊清單，請參閱[了解如何使用、設定以及重複使用 IoT Edge 模組](module-composition.md)。
