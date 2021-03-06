---
title: 診斷 Azure 中已捨棄的通知通知中樞
description: 了解如何透過 Azure 通知中樞卸除的通知診斷常見問題。
services: notification-hubs
documentationcenter: Mobile
author: sethmanheim
manager: femila
editor: jwargo
ms.assetid: b5c89a2a-63b8-46d2-bbed-924f5a4cce61
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: NA
ms.devlang: multiple
ms.topic: article
ms.date: 02/25/2020
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 04/04/2019
ms.openlocfilehash: 1f3c16e6fe1855cf7882d83e620c70d15ce3cb92
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/27/2020
ms.locfileid: "77657505"
---
# <a name="diagnose-dropped-notifications-in-azure-notification-hubs"></a>診斷 Azure 中已捨棄的通知通知中樞

關於 Azure 通知中樞的常見問題，就是如何針對來自應用程式的通知不會出現在用戶端裝置上進行疑難排解。 客戶想要知道捨棄通知的位置和原因，以及如何修正此問題。 本文介紹卸除通知的可能原因，或裝置未收到通知的可能原因。 它也會說明如何判斷根本原因。

請務必先了解通知中樞如何推播通知至裝置。

![通知中樞架構][0]

一般傳送通知流程中，訊息會從*應用程式後端*傳送至通知中樞。 通知中樞會處理所有註冊。 它會將已設定的標記和標記運算式納入考慮，以決定目標。 目標是需要接收推播通知的註冊。 這些註冊可以跨越我們支援的任何平臺： Android、百度（中國的 Android 裝置）、引發 OS （Amazon） iOS、Windows 和 Windows Phone。

透過建立目標，通知中樞會將推播通知到裝置平台的*推播通知服務*。 範例包括適用于 iOS 和 macOS 的 Apple Push Notification service （APNs），以及適用于 Android 裝置的 Firebase 雲端通訊（FCM）。 通知中樞跨多個批次註冊推播通知。 它會根據您在 設定**通知中樞** 下的 Azure 入口網站中設定的認證，向個別推播通知服務進行驗證。 隨後，推播通知服務會將通知轉送到各個*用戶端裝置*。

通知傳遞的最終階段是在平臺的推播通知服務和裝置之間。 通知傳遞可能會在推播通知程式（用戶端、應用程式後端、通知中樞和平臺的推播通知服務）的四個階段中失敗。 如需更多通知中樞架構的相關資訊，請參閱[通知中樞概觀]。

在初始測試/暫存階段，可能會發生無法傳遞通知的情況。 在此階段卸除的通知可能表示設定有問題。 如果在生產環境中發生無法傳遞通知的情況，可能會捨棄部分或全部的通知。 在此情況下，會指出更深入的應用程式或訊息模式問題。

下一節將探討可能會捨棄通知的案例，範圍從通用到罕見。

## <a name="notification-hubs-misconfiguration"></a>通知中樞設定錯誤

若要將通知傳送給個別的推播通知服務，通知中樞必須在應用程式的內容中自行驗證。 您必須使用目標平臺的通知服務（Microsoft、Apple、Google 等）來建立開發人員帳戶。 然後，您必須向作業系統註冊應用程式，以取得您用來處理目標 PNS 的權杖或金鑰。

您必須將平台認證新增至 Azure 入口網站。 如果沒有通知到達裝置，第一個步驟是確保在通知中樞中設定正確的認證。 認證必須符合平臺特定開發人員帳戶所建立的應用程式。

若要完成此程序的逐步指示，請參閱[開始使用 Azure 通知中樞]。

以下是一些常見的錯誤設定檢查：

### <a name="notification-hub-name-location"></a>通知中樞名稱位置

確認通知中樞名稱與以下各項位置相同 (無錯別字)：

* 從用戶端註冊的位置
* 從後端傳送通知的位置
* 您設定推播通知服務認證的位置

請確定您在用戶端和應用程式後端上使用正確的共用存取簽章設定字串。 一般來說，您必須在用戶端上使用**DefaultListenSharedAccessSignature** ，並在應用程式後端上**DefaultFullSharedAccessSignature** 。 這會授與將通知傳送至通知中樞的許可權。

### <a name="apn-configuration"></a>APN 設定

您必須維護兩個不同的中樞：一個用於生產環境，另一個用於測試。 您必須將您在沙箱環境中使用的憑證上傳至不同的中樞，而不是要用於生產環境的憑證/中樞。 請勿嘗試將不同類型的憑證上傳至相同的中樞。 這會導致通知失敗。

如果您不小心將不同類型的憑證上傳至相同的中樞，您應該刪除中樞，並使用新的中樞重新開始。 如果您因為某些原因無法刪除中樞，至少必須刪除中樞的所有現有註冊。

### <a name="fcm-configuration"></a>FCM 設定

1. 請確定您從 Firebase 取得的*伺服器金鑰*符合您在 Azure 入口網站中註冊的伺服器金鑰。

   ![Firebase 伺服器金鑰][3]

2. 確認您已在用戶端上設定**專案識別碼**。 您可以從 Firebase 儀表板取得**專案識別碼**的值。

   ![Firebase 專案識別碼][1]

## <a name="application-issues"></a>應用程式問題

### <a name="tags-and-tag-expressions"></a>標記和標記運算式

如果您使用標記或標記運算式來區隔物件，您可能會在傳送通知時找不到任何目標。 此錯誤是以您的傳送呼叫中指定的標記或標記運算式為基礎。

檢查您的註冊，以確保當您傳送通知時，標記會相符。 然後，只驗證具有這些註冊的用戶端的通知收據。

例如，假設您使用通知中樞的所有註冊都使用「政治」標記。 如果您接著傳送具有「體育」標記的通知，則不會將通知傳送到任何裝置。 複雜的案例可能牽涉到使用「標記 A」*或*「標記 b」註冊的標記運算式，但您的目標是「標記 & & 標記 b」。 本文稍後的自我診斷秘訣一節將說明如何檢查您的註冊和其標記。

### <a name="template-issues"></a>範本問題

如果您使用範本，請確定遵循[範本]中所述的指導方針。

### <a name="invalid-registrations"></a>不正確註冊

如果已正確設定通知中樞，且已正確使用標記或標記運算式，則會找到有效的目標。 通知應該傳送給這些目標。 然後，通知中樞會以並行的方式一連串地傳送數個處理批次。 每個批次會將訊息傳送至一組註冊。

> [!NOTE]
> 由於通知中樞會以平行方式處理批次，因此不保證傳遞通知的順序。

通知中樞已針對「最多一次」訊息傳遞模型優化。 我們會嘗試刪除重複的項目，避免系統將通知多次傳遞給同一部裝置。 系統會檢查註冊，以確保每個裝置的識別碼只會傳送一則訊息，然後才傳送到推播通知服務。

每個批次都會傳送至推播通知服務，而這會接著接受並驗證註冊。 在此過程中，推播通知服務可能會偵測到批次中有一或多個註冊的錯誤。 推播通知服務接著會將錯誤傳回至通知中樞，並停止進程。 推播通知服務會完全卸除該批次。 這特別適用于使用 TCP 串流通訊協定的 APNs。

在此情況下，錯誤註冊會從資料庫移除。 然後，我們會針對該批次中的其餘裝置，重試通知傳遞。

若要取得針對註冊失敗傳遞嘗試的更多錯誤資訊，您可以使用[每個訊息遙測通知中樞 REST api：取得通知訊息遙測](https://docs.microsoft.com/rest/api/notificationhubs/get-notification-message-telemetry)和[PNS 意見](https://msdn.microsoft.com/library/azure/mt705560.aspx)反應。 如需範例程式碼，請參閱[傳送 REST 範例](https://github.com/Azure/azure-notificationhubs-dotnet/tree/master/Samples/SendRestExample/)。

## <a name="push-notification-service-issues"></a>推播通知服務問題

推播通知服務收到通知之後，會將通知傳遞給裝置。 此時，通知中樞無法控制通知傳遞至裝置。

由於平臺通知服務是健全的，因此通知通常會在幾秒鐘內到達裝置。 如果推播通知服務正在進行節流，通知中樞會套用指數退避策略。 如果推播通知服務在30分鐘內仍無法連線，則會有一個原則可以過期並永久捨棄訊息。

如果推播通知服務嘗試傳遞通知，但裝置已離線，推播通知服務就會儲存通知。 它只會儲存一段有限的時間。 並且在可使用裝置時，再行傳遞。

每個應用程式只會儲存一個最近的通知。 如果在裝置離線時傳送多個通知，每個新通知都會導致最後一個通知遭到捨棄。 只保留最新通知，在 FCM*的 APNs 和折*迭*中稱為聯合*。 （FCM 使用折迭索引鍵）。當裝置長時間保持離線狀態時，系統會捨棄針對該裝置儲存的通知。 如需詳細資訊，請參閱[APNs 概觀]和[關於 FCM 訊息]。

使用通知中樞，您可以使用泛型 SendNotification API，透過 HTTP 標頭傳遞聯合索引鍵。 例如，對於 .NET SDK，您會使用 `SendNotificationAsync`。 SendNotification API 也會採用依原樣傳遞至個別推播通知服務的 HTTP 標頭。

## <a name="self-diagnosis-tips"></a>自我診斷秘訣

以下是在通知中樞中診斷已捨棄之通知根本原因的路徑。

### <a name="verify-credentials"></a>驗證認證

#### <a name="push-notification-service-developer-portal"></a>推播通知服務開發人員入口網站

確認個別推播通知服務開發人員入口網站 (APNs、FCM、Windows 通知服務等等) 中的認證。 如需詳細資訊，請參閱[教學課程：使用 Azure 通知中樞將通知傳送至通用 Windows 平臺應用程式](https://docs.microsoft.com/azure/notification-hubs/notification-hubs-windows-store-dotnet-get-started-wns-push-notification)。

#### <a name="azure-portal"></a>Azure 入口網站

若要檢查並比對您從推播通知服務開發人員入口網站取得的認證，請移至 Azure 入口網站中的 [**存取原則**] 索引標籤。

![Azure 入口網站存取原則][4]

### <a name="verify-registrations"></a>驗證註冊

#### <a name="visual-studio"></a>Visual Studio

在 Visual Studio 中，您可以透過伺服器總管來連接到 Azure，以查看及管理多個 Azure 服務，包括通知中樞。 此快捷方式主要適用于您的開發/測試環境。

![Visual Studio 伺服器總管][9]

![伺服器總管](media/notification-hubs-push-notification-fixer/vsserverexplorer2.png)

您可以查看和管理中樞內的所有註冊。 註冊可以依平臺、原生或範本註冊、標記、推播通知服務識別碼、註冊識別碼和到期日來分類。 您也可以在此頁面上編輯註冊。 這對於編輯標記特別有用。

在**伺服器總管**中，以滑鼠右鍵按一下您的通知中樞，然後選取 [**診斷**]。 

![Visual Studio 伺服器總管：診斷功能表](./media/notification-hubs-push-notification-fixer/diagnose-menu.png)

您會看到下列頁面：

![Visual Studio：診斷頁面](./media/notification-hubs-push-notification-fixer/diagnose-page.png)

切換至 [**裝置註冊**] 頁面：

![Visual Studio：裝置註冊](./media/notification-hubs-push-notification-fixer/VSRegistrations.png)

您可以使用 [**測試傳送**] 頁面來傳送測試通知訊息：

![Visual Studio：測試傳送](./media/notification-hubs-push-notification-fixer/test-send-vs.png)

> [!NOTE]
> 僅使用 Visual Studio 在開發/測試期間和有限的註冊數目中編輯註冊。 如果您需要大量編輯註冊，請考慮使用[如何：匯出和修改大量註冊中](https://msdn.microsoft.com/library/dn790624.aspx)所述的匯出和匯入註冊功能。

#### <a name="service-bus-explorer"></a>服務匯流排總管

許多客戶會使用[服務匯流排 Explorer](https://github.com/paolosalvatori/ServiceBusExplorer)來查看和管理其通知中樞。 服務匯流排總管是開放原始碼專案。 

### <a name="verify-message-notifications"></a>驗證訊息通知

#### <a name="azure-portal"></a>Azure 入口網站

若要將測試通知傳送到您的用戶端，而不需要運作後端服務，請在 [支援 + 疑難排解] 下，選取 [測試傳送]。

![測試 Azure 中的傳送功能][7]

#### <a name="visual-studio"></a>Visual Studio

您也可以從 Visual Studio 傳送測試通知。

![測試 Visual Studio 中的傳送功能][10]

如需更多使用通知中樞與 Visual Studio 伺服器總管的相關資訊，請參閱下列文章：

* [如何查看通知中樞的裝置註冊](https://docs.microsoft.com/previous-versions/windows/apps/dn792122(v=win.10))
* [深入探討：Visual Studio 2013 Update 2 RC 和 Azure SDK 2.3]
* [宣佈發行 Visual Studio 2013 Update 3 和 Azure SDK 2.4]

### <a name="debug-failed-notifications-and-review-notification-outcome"></a>偵錯失敗的通知並檢閱通知結果

#### <a name="enabletestsend-property"></a>EnableTestSend 屬性

當您透過通知中樞傳送通知時，一開始會將通知排入佇列。 通知中樞會確定正確的目標，然後將通知傳送至推播通知服務。 如果您使用的是 REST API 或任何用戶端 Sdk，則傳送呼叫的傳回僅表示訊息會以通知中樞排入佇列。 它無法讓您深入瞭解通知中樞最後將通知傳送至推播通知服務時所發生的狀況。

如果您的通知未抵達用戶端裝置，當通知中樞嘗試將它傳遞給推播通知服務時，可能會發生錯誤。 例如，承載大小可能會超過推播通知服務允許的上限，或通知中樞中設定的認證可能無效。

若要深入了解推播通知服務的錯誤，您可以使用 [EnableTestSend] 屬性。 當您從入口網站或 Visual Studio 用戶端傳送測試訊息時，會自動啟用該屬性。 您可以使用此屬性來查看詳細的調試資訊，以及透過 Api。 目前，您可以在 .NET SDK 中使用該屬性。 最後會將它新增至所有用戶端 Sdk。

若要搭配使用 `EnableTestSend` 屬性與 REST 呼叫，請在傳送呼叫結尾附加名為 test 的查詢字串參數。 例如，

```text
https://mynamespace.servicebus.windows.net/mynotificationhub/messages?api-version=2013-10&test
```

#### <a name="net-sdk-example"></a>.NET SDK 範例

以下是使用 .NET SDK 傳送原生快顯視窗 (快顯) 通知的範例：

```csharp
NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString(connString, hubName);
var result = await hub.SendWindowsNativeNotificationAsync(toast);
Console.WriteLine(result.State);
```

在執行的結尾處，`result.State` 只會指出`Enqueued`。 結果不會對您的推播通知發生什麼事提供深入解析。

接著，您可以使用 `EnableTestSend` 布林值屬性。 初始化 `EnableTestSend` 時，請使用 `NotificationHubClient` 屬性，以取得關於傳送通知時發生的推播通知服務錯誤詳細狀態。 傳送呼叫需要額外的時間才能傳回，因為它會先需要通知中樞，才能將通知傳遞給推播通知服務。

```csharp
    bool enableTestSend = true;
    NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString(connString, hubName, enableTestSend);

    var outcome = await hub.SendWindowsNativeNotificationAsync(toast);
    Console.WriteLine(outcome.State);

    foreach (RegistrationResult result in outcome.Results)
    {
        Console.WriteLine(result.ApplicationPlatform + "\n" + result.RegistrationId + "\n" + result.Outcome);
    }
```

#### <a name="sample-output"></a>範例輸出

```text
DetailedStateAvailable
windows
7619785862101227384-7840974832647865618-3
The Token obtained from the Token Provider is wrong
```

此訊息表示在通知中樞中設定的認證無效，或中樞的註冊發生問題。 刪除此註冊，並讓用戶端在傳送訊息之前重新建立註冊。

> [!NOTE]
> 使用 `EnableTestSend` 屬性會進行大幅度的節流處理。 只能在開發/測試環境中使用此選項，並以一組有限的註冊。 Debug 通知只會傳送到10部裝置。 處理 debug 傳送的限制也是每分鐘10次。

### <a name="review-telemetry"></a>檢閱遙測

#### <a name="azure-portal"></a>Azure 入口網站

在入口網站中，您可以取得通知中樞上所有活動的簡要概觀。

1. 在 [概觀] 索引標籤上，您可以依平台查看註冊、通知和錯誤的彙總檢視。

   ![通知中樞概觀儀表板][5]

2. 在 [監視器] 索引標籤上，您可以新增許多其他平台特定度量以獲得更深入的了解。 當通知中樞嘗試將通知傳送至推播通知服務時，您可以特別查看傳回的錯誤。

   ![Azure 入口網站活動記錄][6]

3. 首先，檢閱**內送郵件**、**註冊作業**，以及**成功通知**。 然後，移至每個平台的索引標籤，以檢閱特定於推播通知服務的錯誤。

4. 如果您通知中樞的驗證設定不正確，會出現 **PNS 驗證錯誤**訊息。 檢查推播通知服務認證是很好的指示。

#### <a name="programmatic-access"></a>以程式設計方式存取

如需以程式設計方式存取的詳細資訊，請參閱程式[設計存取](https://docs.microsoft.com/previous-versions/azure/azure-services/dn458823(v=azure.100))。

> [!NOTE]
> 幾個與遙測有關的功能，例如匯出和匯入註冊，以及透過 API 進行遙測存取，只能在標準服務層級上使用。 如果您嘗試從「免費」或「基本」服務層級使用這些功能，當您使用 SDK 時，將會收到例外狀況訊息。 如果您直接從 REST Api 使用這些功能，則會收到 HTTP 403 （禁止）錯誤。
>
> 若要使用遙測相關功能，請先在 Azure 入口網站中確認您使用的是標準服務層級。  

<!-- IMAGES -->
[0]: ./media/notification-hubs-push-notification-fixer/Architecture.png
[1]: ./media/notification-hubs-push-notification-fixer/FCMConfigure.png
[3]: ./media/notification-hubs-push-notification-fixer/FCMServerKey.png
[4]: ../../includes/media/notification-hubs-portal-create-new-hub/notification-hubs-connection-strings-portal.png
[5]: ./media/notification-hubs-push-notification-fixer/PortalDashboard.png
[6]: ./media/notification-hubs-push-notification-fixer/PortalAnalytics.png
[7]: ./media/notification-hubs-ios-get-started/notification-hubs-test-send.png
[8]: ./media/notification-hubs-push-notification-fixer/VSRegistrations.png
[9]: ./media/notification-hubs-push-notification-fixer/vsserverexplorer.png
[10]: ./media/notification-hubs-push-notification-fixer/VSTestNotification.png

<!-- LINKS -->
[通知中樞概觀]: notification-hubs-push-notification-overview.md
[開始使用 Azure 通知中樞]: notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md
[範本]: https://msdn.microsoft.com/library/dn530748.aspx
[APNs 概觀]: https://developer.apple.com/library/content/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/APNSOverview.html
[關於 FCM 訊息]: https://firebase.google.com/docs/cloud-messaging/concept-options
[Export and modify registrations in bulk]: https://msdn.microsoft.com/library/dn790624.aspx
[Service Bus Explorer code]: https://code.msdn.microsoft.com/windowsazure/Service-Bus-Explorer-f2abca5a
[View device registrations for notification hubs]: https://msdn.microsoft.com/library/windows/apps/xaml/dn792122.aspx
[深入探討：Visual Studio 2013 Update 2 RC 和 Azure SDK 2.3]: https://azure.microsoft.com/blog/2014/04/09/deep-dive-visual-studio-2013-update-2-rc-and-azure-sdk-2-3/#NotificationHubs
[宣佈發行 Visual Studio 2013 Update 3 和 Azure SDK 2.4]: https://azure.microsoft.com/blog/2014/08/04/announcing-release-of-visual-studio-2013-update-3-and-azure-sdk-2-4/
[EnableTestSend]: https://docs.microsoft.com/dotnet/api/microsoft.azure.notificationhubs.notificationhubclient.enabletestsend?view=azure-dotnet
[Programmatic telemetry access]: https://msdn.microsoft.com/library/azure/dn458823.aspx
