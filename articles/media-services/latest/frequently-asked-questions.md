---
title: Azure 媒體服務 v3 常見問題集 | Microsoft Docs
description: 本文提供 Azure 媒體服務 v3 常見問題集的解答。
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 06/21/2019
ms.author: juliako
ms.openlocfilehash: 766208c01f27d2024025b7a202bc3724b4fc9fff
ms.sourcegitcommit: f5075cffb60128360a9e2e0a538a29652b409af9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/18/2019
ms.locfileid: "68311844"
---
# <a name="media-services-v3-frequently-asked-questions"></a>媒體服務 v3 常見問題

本文提供 Azure 媒體服務 (AMS) v3 常見問題集的解答。

## <a name="general"></a>一般

### <a name="what-azure-roles-can-perform-actions-on-azure-media-services-resources"></a>哪些 Azure 角色可以對 Azure 媒體服務資源執行動作？ 

請參閱[角色型存取控制 (RBAC) 以取得媒體服務帳戶](rbac-overview.md)。

### <a name="how-do-i-configure-media-reserved-units"></a>如何設定編碼保留單元？

針對由媒體服務 v3 或影片索引子觸發的音訊分析和影片分析作業，強烈建議您使用 10 個 S3 MRU 佈建帳戶。 如果您需要 10 個以上的 S3 MRU，請使用 [Azure 入口網站](https://portal.azure.com/)開立支援票證。

如需詳細資訊，請參閱[使用 CLI 調整媒體處理的規模](media-reserved-units-cli-how-to.md)。

### <a name="what-is-the-recommended-method-to-process-videos"></a>建議用來處理影片的方法為何？

使用[轉換](https://docs.microsoft.com/rest/api/media/transforms)可設定視訊編碼或分析的一般工作。 每個**轉換**都會描述配方或工作流程，以便處理您的視訊或音訊檔案。 「[作業](https://docs.microsoft.com/rest/api/media/jobs)」 (Job) 是媒體服務將**轉換**套用到指定輸入影片或音訊內容的實際要求。 一旦建立轉換，您就可以使用媒體服務 API 或使用任何已發佈的 SDK 提交作業。 如需詳細資訊，請參閱[轉換和作業](transforms-jobs-concept.md)。

### <a name="how-does-pagination-work"></a>分頁的運作方式為何？

使用分頁時，您應一律使用下一頁連結來列舉集合，而不應依存於特定頁面大小。 如需詳細資訊和範例，請參閱[篩選、排序、分頁](entities-overview.md)。

### <a name="what-features-are-not-yet-available-in-azure-media-services-v3"></a>Azure 媒體服務 v3 中尚無法使用哪些功能？

如需詳細資訊, 請參閱[V2 api 相關的功能差距](migrate-from-v2-to-v3.md#feature-gaps-with-respect-to-v2-apis)。

### <a name="what-is-the-process-of-moving-a-media-services-account-between-subscriptions"></a>在訂閱之間移動媒體服務帳戶的程式為何？  

如需詳細資訊, 請參閱[在訂閱之間移動媒體服務帳戶](media-services-account-concept.md)。

## <a name="live-streaming"></a>即時串流 

###  <a name="how-to-insert-breaksvideos-and-image-slates-during-live-stream"></a>如何在即時資料流進行期間插入中斷/影片和影像靜態圖像？

媒體服務 v3 即時編碼尚未支援在即時資料流進行期間插入影片或影像靜態圖像。 

您可以使用[即時內部部署編碼器](recommended-on-premises-live-encoders.md)來切換來源影片。 許多應用程式都可讓您切換來源，包括 Telestream Wirecast、Switcher Studio (在 iOS 上)、OBS Studio (免費應用程式) 等等。

## <a name="content-protection"></a>內容保護

### <a name="should-i-use-an-aes-128-clear-key-encryption-or-a-drm-system"></a>我應該使用 AES-128 清除金鑰加密或 DRM 系統嗎？

客戶通常不知道他們應該使用 AES 加密，還是 DRM 系統。 這兩個系統之間的主要差異是, 使用 AES 加密時, 會透過 TLS 將內容金鑰傳輸至用戶端, 這樣金鑰就會在傳輸中加密, 但不需要任何額外的加密 (「明文」)。 因此, 用來解密內容的金鑰可供用戶端播放人員存取, 而且可以在用戶端的網路追蹤中以純文字方式來查看。 AES-128 清除金鑰加密適用于檢視器是受信任的合作物件的使用案例 (例如, 將公司內散佈的公司影片加密, 以供員工查看)。

PlayReady、Widevine 和 FairPlay 等 DRM 系統全都在用來解密內容的金鑰上提供額外的加密層級, 相較于 AES-128 清除金鑰。 內容金鑰會加密為受 DRM 執行時間保護的金鑰, 以供 TLS 所提供的任何傳輸層級加密使用。 此外，解密會在安全環境中，惡意使用者更難攻擊的作業系統層級上進行處理。 針對檢視者可能不是受信任的合作對象，而且您需要最高層級安全性的使用案例，建議使用 DRM。

### <a name="how-and-where-to-get-jwt-token-before-using-it-to-request-license-or-key"></a>如何以及在何處取得 JWT 權杖，再用來要求授權或金鑰？

1. 針對生產環境, 您需要有一個安全權杖服務 (STS) (web 服務), 它會在 HTTPS 要求發出 JWT 權杖。 用於測試時，您可以使用 [Program.cs](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithDRM/Program.cs) 中定義的 **GetTokenAsync** 方法所示的程式碼。
2. 播放器必須在使用者經過驗證之後，向 STS 提出此類權杖的要求，並將它指派為權杖的值。 您可以使用 [Azure 媒體播放器 API](https://amp.azure.net/libs/amp/latest/docs/)。

* 如需執行 STS (使用對稱和非對稱金鑰) 的範例，請參閱 [https://aka.ms/jwt](https://aka.ms/jwt)。 
* 如需以使用這類 JWT 權杖的 Azure 媒體播放器為基礎的播放器範例，請參閱 [https://aka.ms/amtest](https://aka.ms/amtest) (展開 "player_settings" 連結以查看權杖輸入)。

### <a name="how-do-you-authorize-requests-to-stream-videos-with-aes-encryption"></a>如何授權要求以使用 AES 加密來串流處理影片？

正確的方法是利用 STS (Secure Token Service)：

在 STS 中，根據使用者設定檔，新增不同的宣告 (例如「進階使用者」、「基本使用者」、「免費試用使用者」)。 在 JWT 中使用不同的宣告，使用者可以看見不同的內容。 當然，對於不同的內容/資產，ContentKeyPolicyRestriction 會有對應的 RequiredClaims。

使用 Azure 媒體服務 Api 來設定授權/金鑰傳遞和加密您的資產 (如[本範例](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithAES/Program.cs)所示)。

如需詳細資訊，請參閱：

- [內容保護概觀](content-protection-overview.md)
- [設計具有存取控制的多重 DRM 內容保護系統](design-multi-drm-system-with-access-control.md)

### <a name="http-or-https"></a>HTTP 或 HTTPS？
ASP.NET MVC 播放器應用程式必須支援下列功能：

* 透過 Azure AD (位於 HTTPS 底下) 的使用者驗證。
* 用戶端與 Azure AD (位於 HTTPS 底下) 之間的 JWT 交換。
* 用戶端 (必須在 HTTPS 底下) 的 DRM 授權取得，如果授權傳遞是由媒體服務提供。 PlayReady 產品套件不會針對授權傳遞來授權 HTTPS。 如果您的 PlayReady 授權伺服器是位於媒體服務以外的地方，可以使用 HTTP 或 HTTPS。

ASP.NET 播放器應用程式的最佳做法是使用 HTTPS，因此媒體播放器是在 HTTPS 的頁面上。 不過，HTTP 十分適用於串流處理，因此您必須考量混合內容的問題。

* 瀏覽器不允許混合的內容。 但是諸如 Silverlight 等外掛程式以及適用於 Smooth 和 DASH 的 OSMF 外掛程式則允許。 混合的內容有安全性考量，因為能夠插入惡意 JavaScript 的威脅，從而造成客戶資料處於風險之中。 瀏覽器預設會封鎖這項功能。 目前唯一的解決之道是在伺服器端 (來源) 允許所有網域 (不論 HTTPS 或 HTTP)。 這可能也不是個好主意。
* 避免混合的內容。 播放器應用程式和媒體播放器都應該使用 HTTP 或 HTTPS。 播放混合的內容時，silverlightSS 技術需要清除混合內容警告。 flashSS 技術會處理混合的內容，而沒有混合內容警告。
* 如果您的串流端點是在 2014 年 8 月之前建立的，將不會支援 HTTPS。 在此情況下，請針對 HTTPS 建立並使用新的串流端點。

### <a name="what-about-live-streaming"></a>即時串流呢？

您可以使用完全相同的設計和實作，藉由將與程式相關聯的資產當成「VOD 資產」，來保護媒體服務中的即時串流。 若要提供即時內容的多重 DRM 保護, 請在將資產與即時輸出產生關聯之前, 將相同的設定/處理套用至資產, 如同它是 VOD 資產。

### <a name="what-about-license-servers-outside-media-services"></a>在媒體服務外部的授權伺服器呢？

通常，客戶可能會在他們自己的資料中心或由 DRM 服務提供者裝載的位置，投資授權伺服器陣列。 利用媒體服務內容保護，您就能在混合模式中運作。 您可以在媒體服務中裝載內容並以動態方式保護內容，同時伺服器會在媒體服務外部傳遞 DRM 授權。 在此情況下，請考量下列變更：

* STS 必須發出權杖，讓授權伺服器陣列可接受且可驗證這類權杖。 例如，Axinom 所提供的 Widevine 授權伺服器要求包含權利訊息的特定 JWT。 因此，您需要有 STS 才能發出這類的 JWT。 
* 您不再需要在媒體服務中設定授權傳遞服務。 當您設定 ContentKeyPolicies 時，必須提供授權取得 URL (針對 PlayReady、Widevine 和 FairPlay)。

### <a name="what-if-i-want-to-use-a-custom-sts"></a>如果我想要使用自訂 STS？

客戶可能會選擇使用自訂 STS 來提供 JWT。 原因包括：

* 客戶使用的 IDP 不支援 STS。 在此情況下，自訂 STS 可能是一個選項。
* 客戶在整合 STS 與客戶的訂閱者計費系統時，可能需要更多彈性或更緊密的控制。 例如，MVPD 業者可能會提供多個 OTT 訂閱者套件，例如高階、基本和運動。 業者可能需要讓權杖中的宣告與訂閱者套件相符，如此一來，只有正確套件中的內容可供使用。 在此情況下，自訂 STS 提供所需的彈性和控制。

當您使用自訂的 STS 時，必須進行兩項變更：

* 為資產設定授權傳遞服務時，您必須指定自訂 STS 用來驗證的安全性金鑰，而不是 Azure AD 的目前金鑰。 (更多詳細資料如下)。 
* 產生 JTW 權杖時，會指定安全性金鑰，而不是 Azure AD 中目前 x509 憑證的私密金鑰。

有兩種類型的安全性金鑰：

* 對稱金鑰：使用相同的金鑰來產生及驗證 JWT。
* 非對稱金鑰：搭配使用 x509 憑證中的私密-公開金鑰組，私密金鑰用來加密/產生 JWT，公開金鑰則用來驗證權杖。

> [!NOTE]
> 如果您使用 .NET Framework/C# 作為開發平台，用於非對稱安全性金鑰的 x509 憑證之金鑰長度必須至少為 2048。 這是 .NET Framework 中的 System.IdentityModel.Tokens.X509AsymmetricSecurityKey 類別的需求。 否則，會擲回下列例外狀況：
> 
> IDX10630：用於簽署的 'System.IdentityModel.Tokens.X509AsymmetricSecurityKey' 不能小於 '2048' 位元。

## <a name="media-services-v2-vs-v3"></a>媒體服務 v2 和 v3 

### <a name="can-i-use-the-azure-portal-to-manage-v3-resources"></a>是否可以使用 Azure 入口網站來管理 v3 資源？

目前您無法使用 Azure 入口網站管理 v3 資源。 請使用 [REST API](https://aka.ms/ams-v3-rest-ref)、[CLI](https://aka.ms/ams-v3-cli-ref) 或其中一個支援的 [SDK](media-services-apis-overview.md#sdks)。

### <a name="is-there-an-assetfile-concept-in-v3"></a>v3 中是否有 AssetFile 概念？

AssetFile 已從 AMS API 中移除，以便讓媒體服務與儲存體 SDK 相依性分開。 現在是由儲存體 (而非媒體服務) 保留屬於儲存體的資訊。 

如需詳細資訊，請參閱[遷移至媒體服務 v3](migrate-from-v2-to-v3.md)。

### <a name="where-did-client-side-storage-encryption-go"></a>用戶端儲存體加密怎麼不見了？

現在會建議您使用伺服器端儲存體加密 (預設為開啟)。 如需詳細資訊，請參閱[待用資料的 Azure 儲存體服務加密](https://docs.microsoft.com/azure/storage/common/storage-service-encryption)。

## <a name="next-steps"></a>後續步驟

[媒體服務 v3 概觀](media-services-overview.md)
