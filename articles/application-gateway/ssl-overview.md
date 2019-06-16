---
title: 在 Azure 應用程式閘道上啟用端對端 SSL
description: 本文將簡介應用程式閘道端對端 SSL 支援。
services: application-gateway
author: amsriva
ms.service: application-gateway
ms.topic: article
ms.date: 3/19/2019
ms.author: victorh
ms.openlocfilehash: ee901fdcae9717cc6d03d7653bcaacc0c32518e0
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "66254311"
---
# <a name="overview-of-ssl-termination-and-end-to-end-ssl-with-application-gateway"></a>SSL 終止和端對端 SSL，與應用程式閘道概觀

安全通訊端層 (SSL) 是建立網頁伺服器和瀏覽器之間的加密的連結的標準安全性技術。 此連結可確保 web 伺服器和瀏覽器之間傳遞所有的資料保持私用和加密。 應用程式閘道支援在閘道，以及端對端 SSL 加密這兩種 SSL 終止。

## <a name="ssl-termination"></a>SSL 終止

應用程式閘道支援在閘道上終止 SSL，之後流量通常會以未加密狀態流至後端伺服器。 有許多種應用程式閘道的 SSL 終止的優點：

- **更佳的效能**– 最大效能的衝擊進行 SSL 解密時是初始信號交換。 若要改善效能，伺服器進行解密會快取 SSL 工作階段識別碼，並管理 TLS 工作階段票證。 如果這是在應用程式閘道，所有來自相同用戶端要求可以使用快取的值。 如果它進行設定後端伺服器上，移至另一部伺服器的用戶端的用戶端的要求每次對 re‑authenticate。 使用 TLS 票證有助於減輕此問題，但不是支援的所有用戶端，且可能難以設定和管理。
- **更好的後端伺服器的使用率**– SSL/TLS 處理是非常 CPU 密集，並且成為更密集的索引鍵的大小增加時。 從後端伺服器中移除此工作可讓它們將焦點放在它們是最有效率，請在傳遞內容。
- **智慧型路由**– 解密流量，應用程式閘道具有存取權的要求內容，例如標頭、 URI，並依此類推，而且可以使用將要求路由傳送此資料。
- **憑證管理**– 憑證只需要購買並安裝應用程式閘道和並非所有的後端伺服器上。 這樣可以節省時間和金錢。

若要設定 SSL 終止，SSL 憑證，才能加入至接聽程式，以啟用應用程式閘道，以衍生根據 SSL 通訊協定規格的對稱金鑰。 對稱金鑰則用來加密和解密流量傳送至閘道中。 SSL 憑證必須採用 「 個人資訊交換 (PFX) 格式。 此檔案格式可允許將私密金鑰匯出，而應用程式閘道需要這個匯出的金鑰來執行流量的加密和解密。

> [!NOTE] 
>
> 應用程式閘道不提供任何功能可建立新的憑證或憑證要求傳送至憑證授權單位。

為 SSL 連接，無法運作，您必須確定 SSL 憑證符合下列條件：

- 目前的日期和時間是 「 有效起始 」 和 「 有效期到 」 憑證上的日期範圍內。
- 憑證的「一般名稱」(CN) 符合要求中的主機標頭。 例如，如果用戶端提出 `https://www.contoso.com/` 要求，則 CN 必須是 `www.contoso.com`。

### <a name="certificates-supported-for-ssl-termination"></a>支援 SSL 終止的憑證

應用程式閘道支援下列類型的憑證：

- CA （憑證授權單位） 憑證：CA 憑證是由憑證授權單位 (CA) 發出的數位認證
- 環境變數 （延伸的驗證） 的憑證：EV 憑證是業界標準憑證指導方針。 這會在瀏覽器定位器列會變成綠色，並發佈以及公司名稱。
- 萬用字元憑證：此憑證支援任意數目的子網域為基礎 *。 site.com，您的子網域會取代其中 *。 不過，它不是，支援 site.com，因此如果使用者會存取您的網站，而不需要輸入前置的"www"，萬用字元憑證將不討論這個部分。
- 自我簽署的憑證：用戶端瀏覽器不會信任這些憑證，並將警告使用者虛擬服務的憑證不是信任鏈結的一部分。 自我簽署的憑證適合用於測試或環境系統管理員控制用戶端，可以放心地略過瀏覽器的安全性警示。 生產工作負載絕對不應該使用自我簽署的憑證。

如需詳細資訊，請參閱 <<c0> [ 使用應用程式閘道設定 SSL 終止](https://docs.microsoft.com/azure/application-gateway/create-ssl-portal)。

### <a name="size-of-the-certificate"></a>憑證的大小
請檢查[應用程式閘道限制](https://docs.microsoft.com/azure/azure-subscription-service-limits#application-gateway-limits)一節，以了解最大的 SSL 憑證支援的大小。

## <a name="end-to-end-ssl-encryption"></a>端對端 SSL 加密

有些客戶可能不需要後端伺服器未加密的通訊。 可能是因為安全性需求、合規性需求，或應用程式可能只接受安全連線。 對於這類應用程式，應用程式閘道可支援端對端 SSL 加密。

端對端 SSL 可讓您將機密資料以加密方式安全地傳輸到後端，同時又享有應用程式閘道所提供第 7 層負載平衡功能的好處。 部分功能為 cookie 型工作階段親和性、URL 型路由、支援根據站台或能注入 X-Forwarded-* 標頭的路由。

當設定為端對端 SSL 通訊模式時，應用程式閘道會在閘道上終止 SSL 工作階段，並解密使用者流量。 然後，它會套用所設定的規則來選取要將流量路由傳送到的適當後端集區執行個體。 應用程式閘道接著再起始連往後端伺服器的新 SSL 連線，並先使　用後端伺服器的公開金鑰憑證重新加密資料，再將要求傳輸至後端。 任何來自 Web 伺服器的回應都會經歷相同的程序而回到使用者端。 藉由設定通訊協定設定啟用端對端 SSL[後端 HTTP 設定](https://docs.microsoft.com/azure/application-gateway/configuration-overview#http-settings)為 HTTPS，然後套用至後端集區。

SSL 原則適用於 frontend 和 backend 的流量。 後端，應用程式閘道會作為伺服器，並強制執行原則。 在後端，應用程式閘道會做為用戶端，以及 SSL 交握期間，將通訊協定/密碼資訊傳送為偏好瀏覽。

應用程式閘道只會與通訊有任一列入允許清單中的後端執行個體與應用程式閘道，或其憑證由其中憑證 CN 必須符合在 HTTP 主機名稱的已知 CA 授權單位簽署憑證後端設定。 這些包括受信任的 Azure 服務，例如 Azure App service web apps 和 Azure API 管理。

由知名 CA 授權單位未簽署的憑證後端集區中的成員，如果已啟用的端對端 SSL 的後端集區中的每個執行個體必須設定以允許安全通訊的憑證。 新增憑證可確保應用程式閘道只會與已知的後端執行個體通訊。 這會進而保護端對端通訊。

> [!NOTE] 
>
> 不受信任的 Azure 服務，例如 Azure App service web apps 和 Azure API 管理需要驗證的憑證安裝程式。

> [!NOTE] 
>
> 若要加入的憑證**後端 HTTP 設定**向後端伺服器可以加入的憑證相同**接聽程式**在應用程式閘道或不同的 SSL 終止增強式的安全性。

![端對端 SSL 案例][1]

在此範例中，使用端對端 SSL，將使用 TLS1.2 的要求路由傳送至 Pool1 中的後端伺服器。

## <a name="end-to-end-ssl-and-whitelisting-of-certificates"></a>端對端 SSL 和憑證允許清單

應用程式閘道只會與已知的後端執行個體通訊，後者已將其憑證加入到應用程式閘道的允許清單。 若要啟用憑證允許清單，您必須將後端伺服器憑證的公開金鑰上傳至應用程式閘道 (不是根憑證)。 於是，只允許連接至已知和允許清單中的後端。 其餘的後端會導致閘道錯誤。 自签名证书仅用于测试目的，不建议用于生产工作负荷。 這類憑證必須如先前步驟所述，加入應用程式閘道的允許清單之中，才能使用。

> [!NOTE]
> 受信任的 Azure 服務 (例如 Azure App Service) 不需進行驗證憑證設定。

## <a name="end-to-end-ssl-with-the-v2-sku"></a>v2 SKU 的端對端 SSL

在「應用程式閘道 v2 SKU」中，「驗證憑證」已被淘汰且被「受信任的根憑證」取代。 它們的功能與「驗證憑證」類似，但有幾個主要差異：

- 憑證如果是由 CN 與 HTTP 後端設定中主機名稱相符的已知 CA 授權單位所簽署，則無須執行任何其他步驟，端對端 SSL 就能運作。 

   例如，如果後端憑證是由已知 CA 所簽發且 CN 為 contoso.com，而後端 HTTP 設定的主機欄位也設定為 contoso.com，便無須執行任何其他步驟。 您可以將後端 HTTP 設定通訊協定設定為 HTTPS，健康狀態探查和資料路徑就會都啟用 SSL。 如果您使用 Azure App Service 或其他 Azure web 服務作為後端，則這些也是以隱含方式信任和任何進一步的步驟所需的端對端 SSL。
- 如果憑證是自我簽署的，或由未知的媒介所簽署的，則若要在 v2 SKU 中啟用端對端 SSL，就必須定義受信任的根憑證。 「應用程式閘道」只會與符合下列條件的後端進行通訊：其伺服器憑證的根憑證符合與集區相關之後端 HTTP 設定中受信任根憑證清單內的其中一個憑證。
- 除了根憑證相符之外，「應用程式閘道」也會驗證後端 HTTP 設定中所指定的「主機」設定是否符合後端伺服器 SSL 憑證所出示的通用名稱 (CN)。 嘗試與後端建立 SSL 連線時，「應用程式閘道」會將「伺服器名稱指示」(SNI) 延伸模組設定為後端 HTTP 設定中所指定的「主機」。
- 如果選擇**從後端位址挑選主機名稱**，而不是從後端 HTTP 設定中的 [主機] 欄位挑選，則 SNI 標頭會一律設定為後端集區 FQDN，而後端伺服器 SSL 憑證上的 CN 必須符合其 FQDN。 在此案例中，不支援與 Ip 的後端集區成員。
- 根憑證是一個來自後端伺服器憑證的 Base64 編碼根憑證。

## <a name="next-steps"></a>後續步驟

了解端對端 SSL 之後，請移至[使用 PowerShell 以應用程式閘道設定端對端 SSL](application-gateway-end-to-end-ssl-powershell.md)，以使用端對端 SSL 來建立應用程式閘道。

<!--Image references-->

[1]: ./media/ssl-overview/scenario.png
