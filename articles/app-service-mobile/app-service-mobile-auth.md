---
title: Azure App Service 中行動裝置應用程式的驗證和授權 | Microsoft Docs
description: Azure App Service 驗證/授權功能的概念性參考和概觀，特別是針對行動裝置應用程式
services: app-service
documentationcenter: ''
author: mattchenderson
manager: erikre
editor: ''
ms.service: app-service
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 10/01/2016
ms.author: mahender
ms.openlocfilehash: 6017d9bc8b9a791e46fd1562660c9049ca0f72e5
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/16/2019
ms.locfileid: "72388899"
---
# <a name="authentication-and-authorization-in-azure-app-service-for-mobile-apps"></a>Azure App Service 中行動裝置應用程式的驗證和授權

> [!NOTE]
> Visual Studio App Center 支援行動應用程式開發的端對端和整合式服務中心。 開發人員可以使用**組建**、**測試**和**散發**服務來設定持續整合和傳遞管線。 部署應用程式之後，開發人員可以使用**分析**和**診斷**服務來監視其應用程式的狀態和使用，並與使用**推**播服務的使用者互動。 開發人員也可以利用**驗證**來驗證其使用者和**資料**服務，以保存及同步雲端中的應用程式資料。
>
> 如果您想要在您的行動應用程式中整合雲端服務，請立即註冊[App Center](https://appcenter.ms/?utm_source=zumo&utm_medium=Azure&utm_campaign=zumo%20doc) 。

本文說明使用 App Service 後端開發原生行動裝置應用程式時，驗證和授權的運作方式。 App Service 會提供整合式驗證和授權，讓您的行動裝置應用程式可以將使用者登入，而不需要在 App Service 中變更任何程式碼。 它提供簡單的方法來保護您的應用程式，以及使用每位使用者的資料。 

本文著重於行動裝置應用程式開發。 若要快速開始使用行動應用程式的 App Service 驗證和授權，請參閱下列其中一個教學課程[將驗證新增至您的 iOS 應用程式][iOS]（或[android]、 [Windows]、 [Xamarin.iOS]、 [Xamarin.Android]、 [Xamarin.Forms]或[Cordova]）。 

如需 App Service 中驗證和授權運作方式的詳細資訊，請參閱 [Azure App Service 中的驗證和授權](../app-service/overview-authentication-authorization.md)。

## <a name="authentication-with-provider-sdk"></a>使用提供者 SDK 的驗證

在 App Service 完成一切設定後，您就可以修改讓行動用戶端使用 App Service 進行登入。 有以下兩種方法：

* 使用指定的識別提供者發佈的 SDK 來建立身分識別，然後取得 App Service 的存取權。
* 使用一行程式碼讓 Mobile Apps 用戶端 SDK 能夠將使用者登入。

> [!TIP]
> 大部分應用程式都應該使用提供者 SDK，讓使用者登入時有更一致的體驗、使用權杖重新整理支援，以及享受提供者指定的其他好處。
> 
> 

當您使用提供者 SDK 時，使用者的登入體驗可以與執行應用程式的作業系統更緊密地整合。 這個方法也會給予您一個提供者權杖以及用戶端上的一些使用者資訊，讓它更容易取用圖表 API 和自訂使用者體驗。 在部落格和論壇上，這稱為「用戶端流程」或「用戶端導向流程」，因為用戶端的程式碼會將使用者登入，而且用戶端程式碼可以存取提供者權杖。

取得提供者權杖之後，必須傳送給 App Service 進行驗證。 當 App Service 驗證權杖之後，App Service 會建立新的 App Service 權杖來傳回給用戶端。 Mobile Apps 用戶端 SDK 提供 Helper 方法來管理此交換，並自動將權杖附加至應用程式後端的所有要求。 開發人員也可以持續參考提供者權杖。

如需驗證流程的詳細資訊，請參閱 [App Service 驗證流程](../app-service/overview-authentication-authorization.md#authentication-flow)。 

## <a name="authentication-without-provider-sdk"></a>不使用提供者 SDK 的驗證

如果您不想設定提供者 SDK，您可以允許 Azure App Service 的 Mobile Apps 功能替您登入。 Mobile Apps 用戶端 SDK 將為您選擇的提供者開啟網頁檢視，並將使用者登入。 在部落格和論壇上，這稱為「伺服器流程」或「伺服器導向流程」，因為伺服器會管理使用者登入程序，而用戶端 SDK 永遠不會收到提供者權杖。

每個平台的驗證教學課程都包含啟動此流程的程式碼。 在流程結束時，用戶端 SDK 會擁有 App Service 權杖，而該權杖會自動附加至對應用程式後端的所有要求。

如需驗證流程的詳細資訊，請參閱 [App Service 驗證流程](../app-service/overview-authentication-authorization.md#authentication-flow)。 
## <a name="more-resources"></a>更多資源

下列教學課程示範如何使用[伺服器導向流程](../app-service/overview-authentication-authorization.md#authentication-flow)，將驗證新增至您的行動用戶端︰

* [將驗證新增至您的 iOS 應用程式][iOS]
* [將驗證新增至 Android 應用程式][Android]
* [將驗證新增至您的 Windows App][Windows]
* [將驗證新增至 Xamarin.iOS 應用程式][Xamarin.iOS]
* [將驗證新增至 Xamarin.Android 應用程式][Xamarin.Android]
* [將驗證新增至您的 Xamarin. Forms 應用程式][Xamarin.Forms]
* [將驗證新增至您的 Cordova 應用程式][Cordova]

如果您想要使用適用於 Azure Active Directory 的[用戶端導向流程](../app-service/overview-authentication-authorization.md#authentication-flow)，請使用下列資源︰

* [使用適用于 iOS 的 Active Directory 驗證程式庫][ADAL-iOS]
* [使用適用于 Android 的 Active Directory 驗證程式庫][ADAL-Android]
* [使用適用于 Windows 和 Xamarin 的 Active Directory 驗證程式庫][ADAL-dotnet]

如果您想要使用適用於 Facebook 的[用戶端導向流程](../app-service/overview-authentication-authorization.md#authentication-flow)，請使用下列資源︰

* [使用 Facebook SDK for iOS](../app-service-mobile/app-service-mobile-ios-how-to-use-client-library.md#facebook-sdk)

如果您想要使用適用於 Twitter 的[用戶端導向流程](../app-service/overview-authentication-authorization.md#authentication-flow)，請使用下列資源︰

* [使用 Twitter Fabric for iOS](../app-service-mobile/app-service-mobile-ios-how-to-use-client-library.md#twitter-fabric)

如果您想要使用適用於 Google 的[用戶端導向流程](../app-service/overview-authentication-authorization.md#authentication-flow)，請使用下列資源︰

* [使用 Google Sign-In SDK for iOS](../app-service-mobile/app-service-mobile-ios-how-to-use-client-library.md#google-sdk)

[iOS]: ../app-service-mobile/app-service-mobile-ios-get-started-users.md
[Android]: ../app-service-mobile/app-service-mobile-android-get-started-users.md
[Xamarin.iOS]: ../app-service-mobile/app-service-mobile-xamarin-ios-get-started-users.md
[Xamarin.Android]: ../app-service-mobile/app-service-mobile-xamarin-android-get-started-users.md
[Xamarin.Forms]: ../app-service-mobile/app-service-mobile-xamarin-forms-get-started-users.md
[Windows]: ../app-service-mobile/app-service-mobile-windows-store-dotnet-get-started-users.md
[Cordova]: ../app-service-mobile/app-service-mobile-cordova-get-started-users.md

[AAD]: ../app-service/configure-authentication-provider-aad.md
[Facebook]: ../app-service/configure-authentication-provider-facebook.md
[Google]: configure-authentication-provider-google.md
[MSA]: ../app-service/configure-authentication-provider-microsoft.md
[Twitter]: ../app-service/configure-authentication-provider-twitter.md

[custom-auth]: ../app-service-mobile/app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#custom-auth

[ADAL-Android]: ../app-service-mobile/app-service-mobile-android-how-to-use-client-library.md#adal
[ADAL-iOS]: ../app-service-mobile/app-service-mobile-ios-how-to-use-client-library.md#adal
[ADAL-dotnet]: ../app-service-mobile/app-service-mobile-dotnet-how-to-use-client-library.md#adal
