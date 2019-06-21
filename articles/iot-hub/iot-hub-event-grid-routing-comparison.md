---
title: 比較事件格線、IoT 中樞的路由 | Microsoft Docs
description: 「IoT 中樞」提供自己的訊息路由服務，但也與「事件格線」整合來發佈事件。 比較兩個功能。
author: kgremban
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 02/20/2019
ms.author: kgremban
ms.openlocfilehash: c6d395d878d38ab48b95ca82d94d2a222428bae1
ms.sourcegitcommit: a52d48238d00161be5d1ed5d04132db4de43e076
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/20/2019
ms.locfileid: "67274936"
---
# <a name="compare-message-routing-and-event-grid-for-iot-hub"></a>比較 IoT 中樞的訊息路由和事件格線

Azure IoT 中樞提供串流處理來自連接裝置資料及將該資料整合到商務應用程式的功能。 「IoT 中樞」提供兩種方法來將 IoT 事件整合至其他 Azure 服務或商務應用程式。 本文將探討提供此能力的兩個功能，以便您選擇最適合您案例的選項。

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

**[IoT 中樞訊息路由](iot-hub-devguide-messages-d2c.md)** :此 IoT 中樞功能可讓使用者將裝置到雲端訊息路由至服務端點，例如 Azure 儲存體容器、 事件中樞、 服務匯流排佇列和服務匯流排主題。 路由也會提供查詢功能，在將資料路由傳送到端點前先篩選資料。 除了裝置遙測資料外，您也可以傳送可用來觸發動作的[非遙測事件](iot-hub-devguide-messages-d2c.md#non-telemetry-events)。 

**IoT 中樞與 Event Grid 的整合**:Azure 事件方格是一個完全受控的事件路由服務，它使用發佈-訂閱模型。 「IoT 中樞」與「事件格線」合作即可以近乎即時的方式，將 [IoT 中樞事件整合至 Azure 和非 Azure 服務](iot-hub-event-grid.md)。 IoT 中樞 」 會發佈[裝置事件](iot-hub-event-grid.md#event-types)，已公開推出，以及現在也會發佈遙測事件，這是公開預覽狀態。

## <a name="differences"></a>差異

雖然訊息路由和「事件格線」都可啟用警示設定功能，但兩者之間有一些重要的差異。 如需詳細資訊，請參閱下表︰

| 功能 | IoT 中樞訊息路由 | IoT 中樞與事件格線的整合 |
| ------- | --------------- | ---------- |
| **裝置的訊息和事件** | 是，訊息路由可以使用遙測資料和報表的對應項變更和裝置生命週期事件。 | 是，Event Grid 可用於遙測資料，但也可以回報裝置會建立、 刪除、 連線，並與 IoT 中樞中斷連線時 |
| **排序** | 是，會維持事件的排序。  | 否，不保證維持事件的順序。 | 
| **篩選** | 對訊息應用程式屬性、訊息系統屬性、訊息內文、裝置對應項標籤及裝置對應項屬性所進行的豐富篩選功能。 如需範例，請參閱[訊息路由查詢語法](iot-hub-devguide-routing-query-syntax.md)。 | 根據篩選事件類型、 主體類型和每個事件中的屬性。 如需範例，請參閱[了解事件格線訂用帳戶中的篩選事件](../event-grid/event-filtering.md)。 訂閱時遙測事件，可以將其他篩選器上的資料套用至訊息屬性上的篩選器，訊息內文和裝置對應項，在 IoT 中樞，然後再發佈到 Event Grid。 請參閱[如何篩選事件](../iot-hub/iot-hub-event-grid.md#filter-events)。 |
| **端點** | <ul><li>事件中樞</li> <li>Azure Blob 儲存體</li> <li>服務匯流排佇列</li> <li>服務匯流排主題</li></ul><br>付費的「IoT 中樞」SKU (S1、S2 及 S3) 僅限有 10 個自訂端點。 可以為每個「IoT 中樞」建立 100 個路由。 | <ul><li>Azure Functions</li> <li>Azure 自動化</li> <li>事件中樞</li> <li>Logic Apps</li> <li>儲存體 Blob</li> <li>自訂主題</li> <li>佇列儲存體</li> <li>Microsoft Flow</li> <li>透過 WebHook 提供的協力廠商服務</li></ul><br>支援 500 的端點，每個 IoT 中樞。 如需最新的端點清單，請參閱[事件格線處理常式](../event-grid/overview.md#event-handlers)。 |
| **成本** | 針對訊息路由並不另外收費。 僅針對將遙測資料輸入「IoT 中樞」收費。 例如，如果您將一則訊息路由傳送至三個不同的端點，將只需支付一則訊息的費用。 | 沒有任何來自「IoT 中樞」的收費。 Event Grid，免費提供每個月的前 100,000 個作業 $0.60 美元每百萬個作業之後。 |

## <a name="similarities"></a>相似之處

「IoT 中樞訊息路由」與「事件格線」也有相似之處，下表詳細說明一些相似之處：

| 功能 | IoT 中樞訊息路由 | IoT 中樞與事件格線的整合 |
| ------- | --------------- | ---------- |
| **訊息大小上限** | 256 KB (裝置到雲端) | 256 KB (裝置到雲端) |
| **可靠性** | 高：每個訊息傳遞至每個路由至少一次的端點。 所有未在 1 小時內傳遞的訊息都會變成過期訊息。 | 高：每個訊息傳遞至 webhook 至少一次為每個訂用帳戶。 所有未在 24 小時內傳遞的事件都會變成過期事件。 | 
| **延展性** | 高：最佳化，以支援數百萬部同時連線的裝置傳送數十億則訊息。 | 高：支援的每個區域每秒的路由 10,000,000 個事件。 |
| **延遲** | 低：近乎即時。 | 低：近乎即時。 |
| **傳送至多個端點** | 是，將單一訊息傳送至多個端點。 | 是，將單一訊息傳送至多個端點。  
| **安全性** | 「IoT 中樞」提供個別裝置身分識別與可撤銷的存取控制。 如需詳細資訊，請參閱 [IoT 中樞存取控制](iot-hub-devguide-security.md)。 | 「事件格線」提供三個時間點的驗證：事件訂閱、事件發佈，以及 Webhook 事件傳遞。 如需詳細資訊，請參閱 [Event Grid 安全性和驗證](../event-grid/security-authentication.md)。 |

## <a name="how-to-choose"></a>如何選擇

「IoT 中樞訊息路由」和「IoT 中樞與事件格線的整合」執行不同的動作來達到類似的結果。 兩者都會從您的「IoT 中樞」解決方案取得資訊，然後將該資訊傳遞下去，以便其他服務能夠做出反應。 那麼，要如何決定要使用哪一個？ 請考慮下列問題來協助引導您的決策： 

* **您要傳送給端點的資料是哪一種？**

   當您必須將遙測資料傳送給其他服務時，請使用「IoT 中樞訊息路由」。 訊息路由也能夠查詢訊息應用程式和系統屬性、訊息內文、裝置對應項標籤及裝置對應項屬性。

   「IoT 中樞與事件格線的整合」可處理「IoT 中樞」服務中發生的事件。 這些 「 IoT 中樞 」 事件包含遙測資料，建立、 刪除、 連線，並中斷連線的裝置。 訂閱時遙測事件，可以將其他篩選器上的資料套用至訊息屬性上的篩選器，訊息內文和裝置對應項，在 IoT 中樞，然後再發佈到 Event Grid。 請參閱[如何篩選事件](../iot-hub/iot-hub-event-grid.md#filter-events)。

* **哪些端點需要接收此資訊？**

   IoT 中樞訊息路由支援有限的數目的唯一端點與端點類型，但您可以建置連接器，以重新路由傳送的資料和其他端點的事件。 如需支援的端點完整清單，請參閱上一節中的表格。 

   IoT 中樞與 Event Grid 的整合支援 500 的端點，每個 IoT 中樞和較大的各種端點類型。 它以原生方式整合了 Azure Functions、 Logic Apps、 儲存體和服務匯流排佇列，也適用於擴充傳送的資料以外的 Azure 服務生態系統和協力廠商商務應用程式的 webhook。

* **如果資料有抵達順序，是否有影響？**

   「IoT 中樞訊息路由」會維持訊息的傳送順序，因此這些訊息會依照相同的方式抵達。

   「事件格線」則不保證端點收到事件的順序會與事件發生順序相同。 在其中的訊息絕對順序很重要和/或消費者中訊息需要可靠的唯一識別碼的情況下，我們建議使用訊息路由。 

## <a name="next-steps"></a>後續步驟

* 深入了解 [IoT 中樞訊息路由](iot-hub-devguide-messages-d2c.md)和 [IoT 中樞端點](iot-hub-devguide-endpoints.md)。
* 深入了解 [Azure Event Grid](../event-grid/overview.md)。
* 如需了解如何建立訊息路由，請參閱[使用路由處理 IoT 中樞的裝置到雲端訊息](../iot-hub/tutorial-routing.md)教學課程。
* 試試看 「 事件格線 」 整合，藉由[傳送電子郵件通知，使用 Logic Apps 的 Azure IoT 中樞事件的相關](../event-grid/publish-iot-hub-events-to-logic-apps.md)。
