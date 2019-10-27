---
title: 在 Azure IoT Central 應用程式中建立和管理事件規則 | Microsoft Docs
description: Azure IoT Central 事件規則可讓您近乎即時地監視裝置，以及在觸發規則時自動叫用動作，例如傳送電子郵件。
author: ankitscribbles
ms.author: ankitgup
ms.date: 06/09/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: f932cb9621e4cfc62e2bb00d11030a7ff03450fc
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/25/2019
ms.locfileid: "72954015"
---
# <a name="create-an-event-rule-and-set-up-notifications-in-your-azure-iot-central-application"></a>在 Azure IoT Central 應用程式中建立事件規則並設定通知

*此文章適用於操作員、建置人員及系統管理員。*

[!INCLUDE [iot-central-original-pnp](../../../includes/iot-central-original-pnp-note.md)]

您可以使用 Azure IoT Central 來遠端監視連網裝置。 Azure IoT Central 規則可讓您近乎即時地監視裝置，以及自動叫用動作，例如傳送電子郵件或觸發 Microsoft Flow。 只要按幾下，就可以定義條件來監視裝置資料以及設定相動應的動作。 本文將說明如何建立規則來監視裝置所傳送的事件。

裝置可以使用事件量測來傳送重要或參考的裝置事件。 當裝置報告所選裝置的事件時，就會觸發事件規則。

## <a name="create-an-event-rule"></a>建立事件規則

若要建立事件規則，裝置範本必須至少定義一個事件量測。 此範例使用會報告風扇馬達錯誤事件的冷飲販賣機裝置。 此規則會監視裝置所報告的事件，並在每次報告事件時傳送電子郵件。

1. 使用 [**裝置範本**] 頁面，流覽至您要為其新增規則的裝置範本。

1. 如果您尚未建立任何規則，您會看到下列畫面：

    ![還沒有規則](media/howto-create-event-rules/rules_landing_page1.png)

1. 在 [**規則**] 索引標籤上，選取 [ **+ 新增規則**] 以查看您可以建立的規則類型。

1. 選擇 [**事件**] 圖格，以建立事件監視規則。

    ![規則類型](media/howto-create-event-rules/rule_types1.png)

1. 輸入名稱來協助您識別此裝置範本中的規則。

1. 若要立即為此範本建立的所有裝置啟用規則，請切換 [**為此範本的所有裝置啟用規則**]。

    ![規則詳細資訊](media/howto-create-event-rules/rule_detail1.png)

    此規則會自動套用到裝置範本下的所有裝置。

### <a name="configure-the-rule-conditions"></a>設定規則條件

條件會定義規則所監視的準則。

1. 選擇 [條件] 旁的 [+] 來新增條件。

1. 從量測下拉式清單中選擇想要監視的事件。 此範例選取了**風扇馬達錯誤**事件。

   ![條件](media/howto-create-event-rules/condition_filled_out1.png)

1. (選擇性) 您也可以將 [計數] 設定為 [彙總]，並提供對應的閾值。

   - 如果沒有彙總，規則會在每個事件資料點符合條件時觸發。 例如，如果您將規則的條件設定為在發生**風扇馬達錯誤**事件時觸發，則此規則幾乎會在裝置報告該事件時立即觸發。
   - 如果使用「計數」作為彙總函式，則必須提供**閾值**，以及評估條件的**彙總時間範圍**。 在此情況下，會匯總事件的計數，而且只有在匯總的事件計數符合閾值時，才會觸發此規則。

     比方說，如果您想要在 5 分鐘內發生三個以上的裝置事件時發出警示，請在選取事件後，將彙總函式設定為 [計數]，將運算子設定為 [大於]，以及將 [閾值] 設定為 3。 將 [彙總期間] 設定為 [5 分鐘]。 如果裝置在 5 分鐘內傳送三個以上的事件時，規則就會觸發。 規則評估頻率與**彙總時間範圍**相同，這表示，在此範例中，此規則會每隔 5 分鐘評估一次。

     ![新增事件條件](media/howto-create-event-rules/aggregate_condition_filled_out1.png)

     >[!NOTE]
     >您可以在**條件**下新增多個事件量測。 若指定多個條件，就必須符合所有條件才會觸發規則。 每個條件都是以隱含方式由 ' AND ' 子句聯結。 使用彙總時，必須彙總每個量測。

### <a name="configure-actions"></a>設定動作

本節示範如何設定要在引發規則時採取的動作。 當規則中指定的所有條件都評估為 True 時，即會叫用動作。

1. 選擇 [動作] 旁的 [+]。 在此，您會看到可用動作的清單。

    ![新增動作](media/howto-create-event-rules/add_action1.png)

1. 選擇 [傳送電子郵件] 動作，在 [收件者] 欄位中輸入有效的電子郵件地址，然後提供當此規則觸發時要在電子郵件本文中出現的附註。

    > [!NOTE]
    > 只有已新增至應用程式且至少已登入一次的使用者才會收到電子郵件。 深入了解 Azure IoT Central 中的[使用者管理](howto-administer.md)。

   ![設定動作](media/howto-create-event-rules/configure_action1.png)

1. 若要儲存規則，請選擇 [儲存]。 幾分鐘內，規則就會生效，並開始監視傳送至應用程式的事件。 當規則中指定的條件相符時，規則就會觸發所設定的電子郵件動作。

您可以將其他動作新增至規則，例如 Microsoft Flow 和 Webhook。 您可以針對每個規則最多新增 5 個動作。

- 觸發規則時，[Microsoft Flow 動作](howto-add-microsoft-flow.md)會啟動 Microsoft Flow 中的工作流程 
- 而 [Webhook 動作](howto-create-webhooks.md)會在觸發規則時，通知其他服務

## <a name="parameterize-the-rule"></a>將規則參數化

您也可以使用 [裝置屬性] 將動作設定為參數。 如果將電子郵件地址儲存為裝置屬性，您就可以在定義 [收件者] 地址時加以使用。

## <a name="delete-a-rule"></a>刪除規則

如果不再需要某個規則，請開啟規則並選擇 [刪除] 來加以刪除。 規則在刪除後就會從裝置範本和所有相關聯的裝置中移除。

## <a name="enable-or-disable-a-rule-for-a-device-template"></a>啟用或停用裝置範本的規則

瀏覽至裝置，然後選擇您要啟用或停用的規則。 切換 [為此範本的所有裝置啟用規則] 按鈕，可對所有與裝置範本相關聯的裝置啟用或停用規則。

## <a name="enable-or-disable-a-rule-for-a-device"></a>啟用或停用裝置的規則

瀏覽至裝置，然後選擇您要啟用或停用的規則。 切換 [啟用此裝置的規則] 按鈕，可對該裝置啟用或停用規則。

## <a name="next-steps"></a>後續步驟

您現在已了解如何在 Azure IoT Central 應用程式中建立規則，以下是一些後續步驟：

- [在規則中新增 Microsoft Flow 動作](howto-add-microsoft-flow.md)
- [在規則中新增 Webhook 動作](howto-create-webhooks.md)
- [從一或多個規則將多個動作分組以執行](howto-use-action-groups.md)
- [如何管理您的裝置](howto-manage-devices.md)
