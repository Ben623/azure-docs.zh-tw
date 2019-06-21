---
title: 什麼是 Azure Active Directory 條件式存取的位置條件？ | Microsoft Docs
description: 了解如何使用位置條件，以根據使用者的網路位置來控制雲端應用程式的存取。
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: article
ms.workload: identity
ms.date: 04/12/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: bd62cda209a8ac95a41fa271ce3a96001a3b4811
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/17/2019
ms.locfileid: "67164783"
---
# <a name="what-is-the-location-condition-in-azure-active-directory-conditional-access"></a>什麼是 Azure Active Directory 條件式存取的位置條件？ 

具有[Azure Active Directory (Azure AD) 條件式存取](../active-directory-conditional-access-azure-portal.md)，您可以控制如何授權的使用者可以存取您雲端應用程式。 條件式存取原則的位置條件可讓您將繫結至您的使用者的網路位置的存取控制設定。

本文提供設定位置條件所需的資訊。

## <a name="locations"></a>位置

Azure AD 可讓單一登入裝置、 應用程式，並從任何位置服務在公用網際網路上。 使用位置條件，您可以根據使用者的網路位置來控制雲端應用程式的存取。 位置條件的常見使用案例如下：

- 針對從公司網路外存取服務的使用者要求使用多重要素驗證。
- 封鎖從特定國家或地區存取服務之使用者的存取權。

位置是其中一個代表具名的位置或多重要素驗證信任 Ip 的網路位置的標籤。

## <a name="named-locations"></a>具名位置

使用具名位置，您可以建立 IP 位址範圍或國家/地區和區域的邏輯群組。

您可以存取您在中的具名的位置**管理**條件式存取 頁面的區段。

![條件式存取中的具名的位置](./media/location-condition/02.png)

具名位置具有下列元件：

![建立新的具名位置](./media/location-condition/42.png)

- **名稱** - 具名位置的顯示名稱。
- **IP 範圍** - CIDR 格式的一或多個 IPv4 位址範圍。 不支援指定的 IPv6 位址範圍。

   > [!NOTE]
   > IPv6 位址 rangess 目前不能包含具名位置中。 無法從 條件式存取原則中排除此 measn IPv6 範圍。

- **標記為信任位置** - 您可以為具名位置設定旗標，以指出信任的位置。 一般而言，信任的位置是由您的 IT 部門所控制的網路區域。 除了條件式存取，受信任的具名的位置也會使用 Azure Identity Protection 與 Azure AD 安全性報告來減少[誤判](../reports-monitoring/concept-risk-events.md#impossible-travel-to-atypical-locations-1)。
- **國家/地區** - 此選項可讓您選取一或多個國家或地區，以定義具名位置。
- **包括未知的區域**-部分 IP 位址未對應至特定國家或地區。 此選項可讓您選擇這些 IP 位址是否應包含在具名位置中。 當使用具名位置的原則應套用到未知位置時，請使用此設定。

您可以設定的具名位置數目受到 Azure AD 中相關物件大小的限制。 您可以設定位置根據下列限制：

- 一個具名位置最多有 1200 個 IP 範圍。
- 最多 90 個具名位置，每個位置皆指派一個 IP 範圍。

條件式存取原則適用於 IPv4 和 IPv6 流量。 目前具名的位置不允許設定的 IPv6 範圍。 這項限制會導致下列情況：

- 條件式存取原則不能指向特定的 IPv6 範圍
- 條件式存取原則不能排除特定的 IPV6 範圍

如果原則設定將套用至 「 任何位置 」，它會套用到 IPv4 和 IPv6 流量。 具名的位置設定為指定的國家/地區和區域僅支援 IPv4 位址。 IPv6 流量才包含如果選取 [包含未知的區域] 的選項。

## <a name="trusted-ips"></a>信任的 IP

您也可以在[多重要素驗證服務設定](https://account.activedirectory.windowsazure.com/usermanagement/mfasettings.aspx)中設定代表您組織的近端內部網路的 IP 位址範圍。 這項功能可讓您設定最多 50 個 IP 位址範圍。 IP 位址範圍是 CIDR 格式。 如需詳細資訊，請參閱 <<c0> [ 信任的 Ip](../authentication/howto-mfa-mfasettings.md#trusted-ips)。  

如果您有信任的 Ip 設定，它們會顯示為**MFA 信任的 IP**中的位置條件的位置清單。

### <a name="skipping-multi-factor-authentication"></a>略過多重要素驗證

在多重要素驗證服務設定頁面中，您可以透過選取**針對來自內部網路同盟使用者的要求略過多重要素驗證**來識別公司內部網路使用者。 此設定表示 AD FS 所發出的內部公司網路宣告應受信任，並且應用來識別使用者位於公司網路上。 如需詳細資訊，請參閱 <<c0> [ 使用條件式存取啟用信任的 Ip 功能](../authentication/howto-mfa-mfasettings.md#enable-the-trusted-ips-feature-by-using-conditional-access)。

之後核取此選項，包括 具名的位置**MFA 信任的 IP**選取此選項會套用任何原則。

對於行動裝置和桌上型電腦的應用程式，具有長工作階段存留期，條件式存取會定期重新評估。 預設值是一小時一次。 當只有在初始驗證才會發出位於公司網路內宣告時，Azure AD 可能不會有可信任 IP 範圍清單。 在此情況下，較難判斷使用者是否仍在公司網路上：

1. 檢查使用者的 IP 位址是否在其中一個信任的 IP 範圍內。
2. 檢查使用者的 IP 位址的前三個八位元是否符合初始驗證的 IP 位址的前三個八位元。 IP 位址相較於初始驗證時的內部公司網路宣告原來所發出，且已驗證使用者的位置。

如果這兩個步驟均失敗，會將使用者視為不再位於信任的 IP。

## <a name="location-condition-configuration"></a>位置條件組態

當您設定位置條件時，您可以區別：

- 任何位置
- 所有信任的位置
- 選取的位置

![位置條件組態](./media/location-condition/01.png)

### <a name="any-location"></a>任何位置

根據預設，選取 [任何位置]  會將原則套用到所有 IP 位址，而這意味著網際網路上的所有位址。 這項設定不限於您已設為具名位置的 IP 位址。 當您選取 [任何位置]  時，您仍然可以從原則中排除特定位置。 例如，您可以將原則套用到受信任位置以外的所有位置，以將範圍設為公司網路以外的所有位置。

### <a name="all-trusted-locations"></a>所有信任的位置

此選項適用範圍：

- 已標示為信任的位置的所有位置
- **MFA 信任的 IP** (若已設定)

### <a name="selected-locations"></a>選取的位置

使用此選項，您可以選取一或多個具名位置。 將套用的原則若有此設定，使用者必須從任一選取的位置連線。 當您按一下 [選取]  時，顯示具名網路清單的具名網路選取控制項隨即開啟。 此清單也會顯示網路位置是否已標示為受信任。 稱為 [MFA 信任的 IP]  的具名位置是用來包含可在多重要素驗證服務設定頁面中設定的 IP 設定。

## <a name="what-you-should-know"></a>您應該知道的事情

### <a name="when-is-a-location-evaluated"></a>何時會評估位置？

評估條件式存取原則時：

- 使用者初次登入 Web 應用程式、行動或桌面應用程式。
- 使用新式驗證的行動或桌面應用程式使用重新整理權杖來取得新的存取權杖。 依預設這項檢查一次是一小時。

此核取記號表示適用於行動裝置和桌面應用程式使用新式驗證的位置中的變更會偵測到的網路位置變更一小時內。 對於不使用新式驗證的行動和桌面應用程式，此原則會套用至每個權杖要求。 要求頻率會隨應用程式而異。 同樣地，針對 Web 應用程式，此原則會在初始登入時套用，且適用於 Web 應用程式工作階段的存留期。 由於跨應用程式之工作階段存留期的差異，所以原則評估之間的時間也會不同。 每次應用程式要求新的登入權杖時，就會套用此原則。

根據預設，Azure AD 每小時會發出一個權杖。 在移出公司網路後，一小時內就會使用新式驗證針對應用程式強制執行原則。

### <a name="user-ip-address"></a>使用者 IP 位址

使用於原則評估的 IP 位址是使用者的公用 IP 位址。 如在私人網路上的裝置，此 IP 位址不是內部網路上的使用者裝置的用戶端 IP，它是用來連接到公用網際網路的網路位址。

> [!WARNING]
> 如果您的裝置有只具有 IPv6 位址，不支援設定位置條件。

### <a name="bulk-uploading-and-downloading-of-named-locations"></a>大量上傳與下載具名位置

當您建立或更新具名位置時，您可以上傳或下載包含 IP 範圍的 CSV 檔案來進行大量更新。 上傳會使用檔案中的 IP 範圍取代清單中的 IP 範圍。 檔案的每個資料列均包含一個 CIDR 格式的 IP 位址範圍。

### <a name="cloud-proxies-and-vpns"></a>雲端 Proxy 和 VPN

當您使用雲端託管 Proxy 或 VPN 解決方案時，Azure AD 在評估原則時使用的 IP 位址為 Proxy 的 IP 位址。 不會使用包含使用者公用 IP 位址的 X-Forwarded-For (XFF) 標頭，因為無法驗證它來自受信任的來源，故會提供偽造 IP 位址的方法。

當雲端 Proxy 已就緒時，就能使用用來要求已加入網域之裝置的原則或來自 AD FS 的內部公司網路宣告。

### <a name="api-support-and-powershell"></a>API 支援與 PowerShell

API 和 PowerShell 尚未支援具名位置，或條件式存取原則。

## <a name="next-steps"></a>後續步驟

- 如果您想要了解如何設定條件式存取原則，請參閱[需要 MFA 的特定應用程式與 Azure Active Directory 條件式存取](app-based-mfa.md)。
- 如果您已準備好設定您的環境的條件式存取原則，請參閱[Azure Active Directory 中的條件式存取的最佳作法](best-practices.md)。
