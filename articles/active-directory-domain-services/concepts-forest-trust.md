---
title: Azure AD Domain Services 的信任如何使用 |Microsoft Docs
description: 深入瞭解樹系信任如何使用 Azure AD Domain Services
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 11/19/2019
ms.author: iainfou
ms.openlocfilehash: 8b79e0fb24c15d2e9f16640e90d62f7df5c21f32
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/20/2019
ms.locfileid: "74233697"
---
# <a name="how-trust-relationships-work-for-resource-forests-in-azure-active-directory-domain-services"></a>Azure Active Directory Domain Services 中資源樹系的信任關係如何運作

Active Directory Domain Services （AD DS）透過網域和樹系信任關係，提供跨多個網域或樹系的安全性。 Windows 必須先檢查使用者、電腦或服務所要求的網域是否與要求帳戶的網域有信任關係，才可以跨信任進行驗證。

若要檢查此信任關係，Windows 安全性系統會針對接收要求的伺服器和要求帳戶網域中的 DC，計算網域控制站（DC）之間的信任路徑。

AD DS 和 Windows 分散式安全性模型所提供的存取控制機制，提供網域和樹系信任的操作環境。 為了讓這些信任能夠正常運作，每個資源或電腦都必須擁有其所在網域中 DC 的直接信任路徑。

使用已驗證的遠端程序呼叫（RPC）連線到受信任的網域授權單位時，Net Logon 服務會執行信任路徑。 受保護的通道也會透過區域間的信任關係，延伸至其他 AD DS 網域。 此安全通道可用來取得及驗證安全性資訊，包括使用者和群組的安全識別碼（Sid）。

## <a name="trust-relationship-flows"></a>信任關係流程

透過信任的安全通訊流程會決定信任的彈性。 建立或設定信任的方式會決定在樹系內或跨樹系進行通訊的程度。

透過信任的通訊流程是由信任的方向決定。 信任可以是單向或雙向，而且可以是可轉移或不可轉移的。

下圖顯示*樹狀目錄 1*和*樹狀結構 2*中的所有網域預設都具有可轉移的信任關係。 如此一來，當您在資源上指派適當的許可權時，*樹狀 1*中的使用者可以存取樹狀*2*中的網域中的資源，而*樹狀結構*中的使用者可以存取*樹狀 2*中的資源。

![兩個樹系之間的信任關係圖表](./media/concepts-forest-trust/trust-relationships.png)

### <a name="one-way-and-two-way-trusts"></a>單向和雙向信任

信任關聯性可讓您存取資源的方式為單向或雙向。

單向信任是在兩個網域之間建立的單向驗證路徑。 在*網域 a*和*網域 b*之間的單向信任中，*網域 a*中的使用者可以存取*網域 B*中的資源。不過，*網域 B*中的使用者無法存取*網域 A*中的資源。

某些單向信任可能是不可轉移或可轉移，視所建立的信任類型而定。

在雙向信任中，*網域 a*信任*網域 b* ，而*網域 b*信任*網域 a*。這項設定表示驗證要求可以在兩個網域之間傳遞雙向。 根據所建立的信任類型而定，某些雙向關聯性可以是不可轉移或可轉移的。

AD DS 樹系中的所有網域信任都是雙向、可轉移的信任。 建立新的子域時，會自動在新的子域和父系網域之間建立雙向、可轉移的信任。

### <a name="transitive-and-non-transitive-trusts"></a>可轉移和不可轉移的信任

轉移性決定信任是否可延伸到其形成所在的兩個網域之外。

* 可以使用可轉移的信任來延伸與其他網域的信任關係。
* 不可轉移的信任可用於拒絕與其他網域的信任關係。

每次您在樹系中建立新網域時，會自動在新的網域和其父系網域之間建立雙向、可轉移的信任關係。 如果將子域新增至新的網域，則信任路徑會沿著延伸新網域和其父系網域之間所建立之初始信任路徑的網域階層向上流動。 可轉移的信任關係會在形成網域樹狀結構時向上流動，並在域樹中的所有網域之間建立可轉移的信任。

驗證要求會遵循這些信任路徑，因此樹系中任何網域的帳戶都可以由樹系中的任何其他網域進行驗證。 使用單一登入精靈，具有適當許可權的帳戶可以存取樹系中任何網域內的資源。

## <a name="forest-trusts"></a>樹系信任

樹系信任可協助您管理分段的 AD DS 基礎結構，並支援跨多個樹系存取資源和其他物件。 樹系信任適用于服務提供者、正在進行合併或收購的公司、共同作業的商業外部網路，以及尋求管理自主性解決方案的公司。

使用樹系信任，您可以將兩個不同的樹系連結，形成單向或雙向可轉移的信任關係。 樹系信任可讓系統管理員連接兩個具有單一信任關係的 AD DS 樹系，以提供跨樹系的順暢驗證和授權體驗。

樹系信任只能在一個樹系中的樹系根域與另一個樹系中的樹系根域之間建立。 樹系信任只能在兩個樹系之間建立，而且無法隱含擴充到第三個樹系。 此行為表示，如果樹系*1*和*樹系 2*之間建立樹系信任，而且*樹系 2*與樹系*3*之間建立了另一個樹系信任，*樹系 1*與樹系*3*之間並沒有隱含信任。

下圖顯示在單一組織中，三個 AD DS 樹系之間的兩個不同樹系信任關係。

![單一組織內樹系信任關係的圖表](./media/concepts-forest-trust/forest-trusts.png)

此範例設定提供下列存取權：

* 樹系*2*中的使用者可以存取*樹系 1*或*樹系 3*中任何網域內的資源
* 樹系*3*中的使用者可以存取*樹系 2*中任何網域內的資源
* 樹系*1*中的使用者可以存取*樹系 2*中任何網域內的資源

此設定不允許樹系*1*中的使用者存取*樹系 3*中的資源，反之亦然。 為了讓*樹系 1*和樹系*3*中的使用者共用資源，必須在這兩個樹系之間建立雙向轉移信任。

如果在兩個樹系之間建立單向樹系信任，受信任樹系的成員就可以利用位於信任樹系中的資源。 不過，信任只會在一個方向操作。

例如，在*樹系 1* （信任的樹系）和*樹系 2* （信任樹系）之間建立單向樹系信任時：

* 樹系*1*的成員可以存取位於*樹系 2*中的資源。
* 樹系*2*的成員無法使用相同的信任來存取位於*樹系 1*中的資源。

> [!IMPORTANT]
> Azure AD Domain Services 資源樹系僅支援對內部部署 Active Directory 的單向樹系信任。

### <a name="forest-trust-requirements"></a>樹系信任需求

您必須先確認正確的網域名稱系統（DNS）基礎結構已就緒，才可以建立樹系信任。 只有在下列其中一個 DNS 設定可供使用時，才能建立樹系信任：

* 單一根 DNS 伺服器是這兩個樹系 DNS 命名空間的根 DNS 伺服器-根區域包含每個 DNS 命名空間的委派，以及所有 DNS 伺服器的根提示，包括根 DNS 伺服器。
* 如果沒有共用的根 DNS 伺服器，且每個樹系 DNS 命名空間的根 DNS 伺服器會針對每個 DNS 命名空間使用 DNS 條件轉寄站，以路由傳送另一個命名空間中的名稱查詢。

    > [!IMPORTANT]
    > Azure AD Domain Services 資源樹系必須使用此 DNS 設定。 裝載資源樹系 DNS 命名空間以外的 DNS 命名空間不是 Azure AD Domain Services 的功能。 條件式轉寄站是正確的設定。

* 如果沒有共用的根 DNS 伺服器，且每個樹系 DNS 命名空間的根 DNS 伺服器都是使用 DNS 次要區域，則會在每個 DNS 命名空間中設定，以路由傳送另一個命名空間中的名稱查詢。

若要建立樹系信任，您必須是 Domain Admins 群組（位於樹系根域中）或 Enterprise Admins 群組的成員，Active Directory。 每個信任都會被指派一個密碼，這兩個樹系的系統管理員都必須知道。 這兩個樹系中的 Enterprise Admins 成員可以同時在這兩個樹系中建立信任，而在此案例中，系統會自動為這兩個樹系產生和寫入密碼，並為其隨機加密。

Azure AD Domain Services 的輸出樹系信任是在 Azure 入口網站中建立。 您不會手動建立與受控網域本身的信任。 連入樹系信任必須由具有先前在內部部署 Active Directory 中所記下之許可權的使用者進行設定。

## <a name="trust-processes-and-interactions"></a>信任進程和互動

許多網域間和樹系間交易相依于網域或樹系信任，才能完成各種工作。 本節說明在透過信任和驗證參照來存取資源時所發生的處理常式和互動。

### <a name="overview-of-authentication-referral-processing"></a>驗證參考處理的總覽

當驗證要求被參照網域時，該網域中的網域控制站必須判斷是否有與該要求來自的網域之間的信任關係。 信任的方向，以及信任是否為可轉移或非轉移，也必須在驗證使用者以存取網域中的資源之前進行判斷。 在受信任網域之間發生的驗證程式會根據使用中的驗證通訊協定而有所不同。 Kerberos V5 和 NTLM 通訊協定會以不同方式處理向網域驗證的參考

### <a name="kerberos-v5-referral-processing"></a>Kerberos V5 參考處理

Kerberos V5 驗證通訊協定取決於網域控制站上的 Net Logon 服務，以取得用戶端驗證和授權資訊。 Kerberos 通訊協定會連接到線上金鑰發佈中心（KDC）和會話票證的 Active Directory 帳戶存放區。

Kerberos 通訊協定也會使用跨領域票證授與服務（TGS）的信任，並在受保護的通道上驗證許可權屬性憑證（Pac）。 Kerberos 通訊協定只會使用非 Windows 品牌的作業系統 Kerberos 領域（例如 MIT Kerberos 領域）來執行跨領域驗證，而且不需要與 Net Logon 服務互動。

如果用戶端使用 Kerberos V5 進行驗證，它會從其帳戶網域中的網域控制站向目標網域中的伺服器要求票證。 Kerberos KDC 會做為用戶端與伺服器之間的信任媒介，並提供工作階段金鑰讓雙方彼此驗證。 如果目標網域與目前的網域不同，KDC 會遵循邏輯程式來判斷是否可以參考驗證要求：

1. 目前的網域是否由所要求之伺服器的網域直接信任？
    * 如果是，請將參考傳送給用戶端，以要求的網域。
    * 若為否，請移至下一個步驟。

2. 目前網域與信任路徑上的下一個網域之間是否有可轉移的信任關係？
    * 如果是，請將參考傳送至信任路徑上的下一個網域。
    * 若為 [否]，則傳送拒絕登入的訊息給用戶端。

### <a name="ntlm-referral-processing"></a>NTLM 參考處理

NTLM 驗證通訊協定取決於網域控制站上的 Net Logon 服務，以取得用戶端驗證和授權資訊。 此通訊協定會驗證不使用 Kerberos 驗證的用戶端。 NTLM 使用信任來傳遞網域之間的驗證要求。

如果用戶端使用 NTLM 進行驗證，則驗證的初始要求會直接從用戶端移至目標網域中的資源伺服器。 此伺服器會建立用戶端回應的挑戰。 接著，伺服器會將使用者的回應傳送到其電腦帳戶網域中的網域控制站。 此網域控制站會根據其安全性帳戶資料庫來檢查使用者帳戶。

如果帳戶不存在於資料庫中，網域控制站會決定是否要執行傳遞驗證、轉送要求，或使用下列邏輯拒絕要求：

1. 目前的網域是否與使用者的網域具有直接信任關係？
    * 如果是，網域控制站會將用戶端的認證傳送至使用者網域中的網域控制站，以進行傳遞驗證。
    * 若為否，請移至下一個步驟。

2. 目前的網域是否與使用者的網域具有可轉移的信任關係？
    * 如果是，請將驗證要求傳遞至信任路徑中的下一個網域。 此網域控制站會針對自己的安全性帳戶資料庫檢查使用者的認證，以重複此程式。
    * 若為 [否]，則傳送拒絕登入的訊息給用戶端。

### <a name="kerberos-based-processing-of-authentication-requests-over-forest-trusts"></a>透過樹系信任驗證要求的 Kerberos 型處理

當兩個樹系透過樹系信任進行連線時，使用 Kerberos V5 或 NTLM 通訊協定所提出的驗證要求可以在樹系之間路由傳送，以提供兩個樹系中資源的存取權。

第一次建立樹系信任時，每個樹系會收集其夥伴樹系中的所有受信任命名空間，並將資訊儲存在[受信任的網域物件](#trusted-domain-object)中。 受信任的命名空間包括網域樹系名稱、使用者主體名稱（UPN）尾碼、服務主體名稱（SPN）尾碼，以及在另一個樹系中使用的安全識別碼（SID）命名空間。 TDO 物件已複寫至通用類別目錄。

在驗證通訊協定可以遵循樹系信任路徑之前，資源電腦的服務主體名稱（SPN）必須解析成另一個樹系中的位置。 SPN 可以是下列其中一項：

* 主機的 DNS 名稱。
* 網域的 DNS 名稱。
* 服務連接點物件的辨別名稱。

當某個樹系中的工作站嘗試存取另一個樹系中資源電腦上的資料時，Kerberos 驗證程式會將服務票證的網域控制站連線到資源電腦的 SPN。 一旦網域控制站查詢通用類別目錄，並判斷 SPN 與網域控制站不在相同的樹系中，網域控制站就會將其父系網域的參照傳送回工作站。 此時，工作站會查詢服務票證的父系網域，並繼續遵循參照鏈，直到到達資源所在的網域為止。

下圖和步驟提供 Kerberos 驗證程式的詳細描述，當執行 Windows 的電腦嘗試從位於另一個樹系中的電腦存取資源時，就會使用該進程。

![透過樹系信任的 Kerberos 進程圖表](media/concepts-forest-trust/kerberos-over-forest-trust-process.png)

1. *User1*使用來自*europe.tailspintoys.com*網域的認證登入*Workstation1* 。 然後，使用者會嘗試存取位於*usa.wingtiptoys.com*樹系中*包含 fileserver1*上的共用資源。

2. *Workstation1*會在其網域中的網域控制站上聯絡 Kerberos KDC， *ChildDC1*，並要求*包含 fileserver1* SPN 的服務票證。

3. *ChildDC1*在其網域資料庫中找不到 SPN，並查詢通用類別目錄，以查看*tailspintoys.com*樹系中的任何網域是否包含此 spn。 因為通用類別目錄僅限於它自己的樹系，所以找不到 SPN。

    通用類別目錄接著會檢查其資料庫，以取得與其樹系建立之任何樹系信任的相關資訊。 如果找到，它會將樹系信任受信任的網域物件（TDO）中所列的名稱尾碼，與目標 SPN 的尾碼進行比較，以尋找相符的。 一旦找到相符的，通用類別目錄就會將路由提示提供給*ChildDC1*。

    路由提示可協助導向目的地樹系的直接驗證要求。 只有在所有傳統驗證通道（例如本機網域控制站和通用類別目錄）找不到 SPN 時，才會使用提示。

4. *ChildDC1*會將其父系網域的參考傳回*Workstation1*。

5. *Workstation1*會將*ForestRootDC1* （其父網域）中的網域控制站與*wingtiptoys.com*樹系的樹系根域中的網域控制站（*ForestRootDC2*）的參考聯繫在一起。

6. *Workstation1* *ForestRootDC2*在*wingtiptoys.com*樹系中的連絡人，以取得要求服務的服務票證。

7. *ForestRootDC2*會聯絡其通用類別目錄以尋找 spn，而通用類別目錄會尋找與 spn 相符的內容，並將其傳回至*ForestRootDC2*。

8. 然後， *ForestRootDC2*會將*usa.wingtiptoys.com*的參考傳回給*Workstation1*。

9. *Workstation1*會聯繫*CHILDDC2*上的 KDC，並協商*User1*的票證，以取得*包含 fileserver1*的存取權。

10. 一旦*Workstation1*有服務票證，它就會將服務票證傳送至*包含 fileserver1*，它會讀取*User1*的安全性認證，並據此來建立存取權杖。

## <a name="trusted-domain-object"></a>受信任的網域物件

組織內的每個網域或樹系信任，都是由儲存在其網域內的*系統*容器中的受信任的網域物件（TDO）來表示。

### <a name="tdo-contents"></a>TDO 內容

TDO 中包含的資訊會根據是否由網域信任或樹系信任所建立的 TDO 而有所不同。

建立網域信任時，會在 TDO 中表示 DNS 功能變數名稱、網域 SID、信任類型、信任傳遞和交互功能變數名稱等屬性。 樹系信任 Tdo 會儲存額外的屬性，以識別來自夥伴樹系的所有受信任命名空間。 這些屬性包括網域樹系名稱、使用者主體名稱（UPN）尾碼、服務主體名稱（SPN）尾碼，以及安全識別碼（SID）命名空間。

因為信任是以 Tdo 的形式儲存在 Active Directory 中，所以樹系中的所有網域都知道整個樹系中的信任關係。 同樣地，當兩個或多個樹系透過樹系信任聯結在一起時，每個樹系中的樹系根域都會知道已在受信任樹系中所有網域的信任關係。

### <a name="tdo-password-changes"></a>TDO 密碼變更

信任關係中的兩個網域都共用一個密碼，儲存在 Active Directory 的 TDO 物件中。 在帳戶維護程式中，每隔30天，信任網域控制站會變更儲存在 TDO 中的密碼。 因為所有雙向信任實際上都是以相反方向進行的 2 1 雙向信任，所以雙向信任的程式會發生兩次。

信任具有信任和信任的端。 在信任的端，任何可寫入的網域控制站都可以用於此程式。 在信任端，PDC 模擬器會執行密碼變更。

若要變更密碼，網域控制站會完成下列程式：

1. 信任網域中的主域控制站（PDC）模擬器會建立新的密碼。 受信任網域中的網域控制站永遠不會起始密碼變更。 它一律由信任網域 PDC 模擬器起始。

2. 信任網域中的 PDC 模擬器會將 TDO 物件的*OldPassword*欄位設定為目前的*NewPassword*欄位。

3. 信任網域中的 PDC 模擬器會將 TDO 物件的*NewPassword*欄位設定為新密碼。 如果受信任網域中的網域控制站無法接收變更，或在使用新的信任密碼提出要求之前未複寫變更，則保留先前密碼的複本可以還原成舊密碼。

4. 信任網域中的 PDC 模擬器會對受信任網域中的網域控制站進行遠端呼叫，要求它將信任帳戶的密碼設定為新的密碼。

5. 受信任網域中的網域控制站會將信任密碼變更為新密碼。

6. 在信任的每一端，更新會複寫到網域中的其他網域控制站。 在信任網域中，此變更會觸發受信任網域物件的緊急複寫。

這兩個網域控制站上的密碼現在已變更。 一般複寫會將 TDO 物件散發至網域中的其他網域控制站。 不過，信任網域中的網域控制站可能會變更密碼，而不會成功更新受信任網域中的網域控制站。 發生此情況的原因可能是無法建立處理密碼變更所需的安全通道。 此外，受信任網域中的網域控制站也可能在程式中的某個時間點無法使用，而且可能不會收到更新過的密碼。

若要處理密碼變更未成功通訊的情況，信任網域中的網域控制站永遠不會變更新密碼，除非已使用新密碼成功驗證（設定安全通道）。 此行為是為什麼舊密碼和新密碼會保留在信任網域的 TDO 物件中。

密碼變更不會完成，直到使用密碼驗證成功為止。 舊的預存密碼可以透過安全的通道使用，直到受信任網域中的網域控制站收到新的密碼，因此可啟用不中斷的服務。

如果使用新密碼進行驗證失敗，因為密碼無效，信任的網域控制站會嘗試使用舊密碼進行驗證。 如果它使用舊密碼進行驗證成功，則會在15分鐘內繼續執行密碼變更程式。

信任密碼更新必須在30天內複寫到信任雙方的網域控制站。 如果在30天后變更信任密碼，且網域控制站只有 N-2 密碼，則不能使用信任端的信任，也無法在受信任的端建立安全通道。

## <a name="network-ports-used-by-trusts"></a>信任所使用的網路埠

因為信任必須部署在不同的網路界限，所以可能必須跨越一或多個防火牆。 當發生這種情況時，您可以在防火牆之間建立通道信任流量，或在防火牆中開啟特定埠，以允許流量通過。

> [!IMPORTANT]
> Active Directory Domain Services 不支援對特定埠限制 Active Directory RPC 流量。

閱讀 Microsoft 支援服務文章[如何設定 Active Directory 網域和信任的防火牆](https://support.microsoft.com/help/179442/how-to-configure-a-firewall-for-domains-and-trusts)中的**Windows Server 2008 和更新版本**一節，以瞭解樹系信任所需的埠。

## <a name="supporting-services-and-tools"></a>支援的服務和工具

為了支援信任和驗證，會使用一些額外的功能和管理工具。

### <a name="net-logon"></a>Net Logon

Net Logon 服務會維護從 Windows 電腦到 DC 的安全通道。 它也會在下列信任相關的程式中使用：

* 信任設定和管理-Net Logon 可協助維護信任密碼、收集信任資訊，以及與 LSA 程式和 TDO 互動來驗證信任。

    對於樹系信任，信任資訊包括樹系信任資訊（*FTInfo*）記錄，其中包括信任的樹系所宣告要管理的一組命名空間，加上一個欄位，其中指出信任樹系是否信任每個宣告。

* 驗證–透過受保護的通道向網域控制站提供使用者認證，並傳回使用者的網域 Sid 和使用者權限。

* 網域控制站位置–協助尋找或尋找網域中或跨網域的網域控制站。

* 傳遞驗證– Net Logon 會處理其他網域中的使用者認證。 當信任網域需要驗證使用者的身分識別時，它會透過 Net Logon 將使用者的認證傳遞至受信任的網域，以進行驗證。

* 許可權屬性憑證（PAC）驗證–當使用 Kerberos 通訊協定進行驗證的伺服器需要驗證服務票證中的 PAC 時，它會透過安全通道將 PAC 傳送至其網域控制站進行驗證。

### <a name="local-security-authority"></a>本地安全機構

「本地安全機構」（LSA）是一個受保護的子系統，會維護系統上本機安全性所有層面的相關資訊。 LSA 統稱為本機安全性原則，可提供各種服務，以便在名稱和識別碼之間進行轉譯。

LSA 安全性子系統提供核心模式和使用者模式的服務，以驗證對物件的存取、檢查使用者權限，以及產生 audit 訊息。 LSA 負責檢查受信任或不信任網域中的服務所呈現的所有會話票證的有效性。

### <a name="management-tools"></a>管理工具

系統管理員可以使用*Active Directory 網域和信任*、 *Netdom*和*Nltest*來公開、建立、移除或修改信任。

* *Active Directory 網域和信任*是 Microsoft Management CONSOLE （MMC），可用來管理網域信任、網域和樹系功能等級，以及使用者主要名稱尾碼。
* *Netdom*和*Nltest*命令列工具可以用來尋找、顯示、建立和管理信任。 這些工具會直接與網域控制站上的 LSA 授權單位進行通訊。

## <a name="next-steps"></a>後續步驟

若要深入瞭解資源樹系，請參閱[樹系信任在 AZURE AD DS 中的運作方式？][concepts-trust]

若要開始建立具有資源樹系的 Azure AD DS 受控網域，請參閱[建立和設定 AZURE AD ds 受控網域][tutorial-create-advanced]。 接著，您可以對內部[部署網域（預覽）建立輸出樹系信任][create-forest-trust]。

<!-- LINKS - INTERNAL -->
[concepts-trust]: concepts-forest-trust.md
[tutorial-create-advanced]: tutorial-create-instance-advanced.md
[create-forest-trust]: tutorial-create-forest-trust.md
