---
title: 什麼是 Azure web 應用程式防火牆的 Azure 大門？ (預覽)
description: 針對 Azure 前端服務會將您的 web 應用程式防止惡意的攻擊，請了解如何在 Azure web 應用程式防火牆。
services: frontdoor
documentationcenter: ''
author: KumudD
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/31/2019
ms.author: kumud;tyao
ms.openlocfilehash: 122e9687ee313edff34e5a4fd9a44b1026a63811
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "66478764"
---
# <a name="what-is-azure-web-application-firewall-for-azure-front-door"></a>什麼是 Azure web 應用程式防火牆的 Azure 大門？

Azure Web 應用程式防火牆 (WAF) 會以集中保護的方式，保護透過使用 Azure Front Door 向全球提供的 Web 應用程式。 其作用是協助 Web 服務抵禦常見的惡意攻擊和弱點，並為您的使用者維持服務的高可用性，以及協助您符合法規需求。


Web 應用程式已逐漸成為惡意的攻擊，例如阻絕服務洪水攻擊、 SQL 插入式攻擊和跨網站指令碼攻擊的目標。 這些惡意的攻擊可能會導致服務中斷和資料遺失，造成重大的威脅，web 應用程式擁有者。

想要防止應用程式的程式碼受到這類攻擊會非常困難，而且可能需要對多層次的應用程式拓撲執行嚴格的維護、修補和監視工作。 集中式 Web 應用程式防火牆有助於簡化安全性管理作業，且更加確保應用程式管理員能夠對抗威脅或入侵。 此外，WAF 方案可因應安全性威脅，更快速的修補已知的弱點在集中位置，而不是保護每個個別的 web 應用程式。

WAF 大門是全域和集中式解決方案。 它部署在世界各地的 Azure 網路邊緣位置上，並由前端的 web 應用程式已啟用 WAF 的每個傳入要求會檢查在網路邊緣。 這允許 WAF，防止惡意攻擊的攻擊來源、 接近，才能進入您的虛擬網路，並提供大規模的整體保護，而不會犧牲效能。 WAF 原則可以輕易地連結到您的訂用帳戶中任何前端設定檔和新的規則可以部署分鐘內，可讓您因應瞬息萬變的威脅模式。

![Azure web 應用程式防火牆](./media/waf-overview/web-application-firewall-overview2.png)

Azure WAF 也能夠與應用程式閘道。 如需詳細資訊，請參閱 < [Web 應用程式防火牆](../application-gateway/waf-overview.md)。

## <a name="waf-policy-and-rules"></a>WAF 原則和規則

您可以設定 WAF 原則，並建立一或多個前端前端來保護該原則的關聯。 WAF 原則是由兩種類型的安全性規則所組成：

- 由客戶所撰寫的自訂規則。

- 是的 Azure 受控組預先設定的規則集合的受管理的規則集。

當兩者同時存在時，會處理中的受管理的規則集的規則之前，先處理自訂的規則。 規則是由比對條件、 優先權和動作。 支援的動作類型包括：允許、 封鎖、 LOG 和重新導向。 您可以建立完全自訂的原則，藉由結合受管理和自訂規則符合您特定的應用程式的保護需求。

原則的規則處理以優先順序排列優先順序的定義的規則處理順序的唯一整數。 較小的整數值表示較高的優先順序，這些會更高版本的整數值的規則優先評估。 一旦符合規則時，會在規則中定義的對應動作套用到要求。 一旦處理這類相符項目之後，較低優先順序的規則都不會進一步處理。

前門所傳遞的 web 應用程式可以有與其相關聯，一次只能有一個 WAF 原則。 不過，您可以在沒有任何與其相關聯的 WAF 原則有前端組態。 如果 WAF 原則存在，它會複寫到所有我們以確保安全性原則中的一致性在世界各地的邊緣位置。

## <a name="waf-modes"></a>WAF 模式

在下列兩種模式中執行，就可以設定 WAF 原則：

- **偵測模式：** 偵測模式中執行時，WAF 不會監視之外的任何其他動作和 WAF 記錄檔會記錄要求和其相符的 WAF 規則。 您可以開啟記錄診斷的大門 (當使用入口網站，這可藉由移至**診斷**在 Azure 入口網站中的一節)。

- **防止模式：** 設定為在防止模式中執行時，WAF 會採用指定的動作，如果要求符合規則，而且如果找到相符項目，則會評估任何其他規則，以較低的優先順序。 WAF 記錄也會記錄任何相符的要求。

## <a name="waf-actions"></a>WAF 動作

WAF 客戶可以選擇要求符合規則條件時執行的其中一個動作：

- **允許：** 要求通過 WAF，然後轉送到後端。 沒有進一步較低的優先順序規則可以封鎖這項要求。
- **封鎖：** 要求被封鎖，WAF 會傳送回應給用戶端，而不將要求轉送到後端。
- **記錄檔：** 要求會記錄在 WAF 記錄，WAF 會繼續評估較低優先順序的規則。
- **重新導向：** WAF 會將要求重新導向至指定的 URI。 指定的 URI 是原則層級設定。 設定後，所有的要求符合**重新導向**動作將會傳送至該 URI。

## <a name="waf-rules"></a>WAF 規則

WAF 原則可以包含兩種類型的安全性規則-自訂規則，依客戶及受管理的 ruleset，撰寫 Azure 受管理預先設定的規則集。

### <a name="custom-authored-rules"></a>撰寫的自訂規則

您可以設定 WAF 的自訂規則，如下所示：

- **IP 允許清單和封鎖清單：** 您可以設定自訂規則來控制存取權的用戶端 IP 位址或 IP 位址範圍清單為基礎的 web 應用程式。 支援 IPv4 和 IPv6 位址類型。 這份清單可以設定為封鎖或允許這些要求的來源 IP 符合 IP 清單中的位置。

- **地理型的存取控制：** 您可以設定自訂的規則，以控制存取您的 web 應用程式，根據用戶端的 IP 位址相關聯的國家/地區碼。

- **HTTP 參數為基礎的存取控制：** 您可以設定自訂規則依據比對 HTTP/HTTPS 要求參數，例如查詢字串、 POST 引數，要求 URI、 要求標頭和要求主體的字串。

- **要求方法為基礎的存取控制：** 您可以設定自訂規則，例如 GET、 PUT 或 HEAD 要求的 HTTP 要求方法為基礎。

- **大小條件約束：** 您可以設定自訂的規則為基礎的查詢字串的 Uri，例如要求特定部分的長度，或要求主體。

- **速率限制規則：** 速率控制規則是要限制來自任何用戶端 IP 的異常高流量。 您可以設定臨界值一分鐘期間允許從用戶端 IP 的 web 要求的數目。 這是不同的 IP 清單架構允許/封鎖自訂規則，是讓所有要求從用戶端 IP 的區塊。 速率限制可以結合其他的比對條件，例如 HTTP (S) 的細微速率控制相符的參數。

### <a name="azure-managed-rule-sets"></a>Azure 受管理的規則集

Azure 受管理的規則集提供簡單的方式部署一組常見的安全性威脅的防護。 因為這類的 ruleset，並由 Azure 管理，視需要以防止新的攻擊簽章，會更新規則。 在公開預覽期間，Azure 受控預設規則集包含針對下列的威脅類別的規則：

- 跨網站指令碼
- Java 攻擊
- 本機檔案包含
- PHP 插入式攻擊
- 遠端命令執行
- 遠端檔案引入
- 工作階段 fixation
- SQL 插入式攻擊保護
- 通訊協定攻擊者

新的攻擊簽章新增至規則集時，預設規則集的版本號碼會遞增。
在您的 WAF 原則中的偵測模式中的預設會啟用預設規則集。 您可以停用或啟用預設規則設定為符合您的應用程式內的個別規則。 您也可以設定每個規則的特定動作 （允許/封鎖/重新導向/記錄）。 預設動作是區塊。 此外，自訂規則會設定位於相同的 WAF 原則上，如果您想略過任何預先設定的規則，在 預設規則集。
之前在預設規則集的規則會進行評估，一律會套用自訂規則。 如果要求符合自訂規則，套用相對應的規則動作，並要求封鎖或傳遞到後端，而不需要任何進一步的自訂規則或規則在預設規則集的引動過程。 此外，您可以選擇從您的 WAF 原則中移除 預設規則集。


### <a name="bot-protection-rule-preview"></a>Bot 保護規則 （預覽）

WAF 擔任來自已知惡意 IP 位址的要求中的自訂動作時，可以啟用受管理的 Bot 保護規則集。 IP 位址被來自 Microsoft 威脅情報摘要。 [Intelligent Security Graph](https://www.microsoft.com/security/operations/intelligence)提供 Microsoft 威脅情報，並由多個服務，包括 Azure 資訊安全中心。

![Bot 保護規則集](./media/waf-front-door-configure-bot-protection/BotProtect2.png)

> [!IMPORTANT]
> Bot 保護規則集目前處於公開預覽狀態，並提供預覽服務等級協定。 可能不支援特定功能，或可能已經限制功能。  如需詳細資訊，請參閱 [Microsoft Azure 預覽專用的補充使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

如果已啟用 Bot 保護，符合惡意 Bot 用戶端 Ip 的連入要求都會記錄在 FrontdoorWebApplicationFirewallLog 記錄檔中。 您可以從儲存體帳戶存取 WAF 記錄事件中樞或 log analytics。 

## <a name="configuration"></a>組態

設定和部署所有的 WAF 規則型別完全支援使用 Azure 入口網站、 REST Api、 Azure Resource Manager 範本和 Azure PowerShell。

## <a name="monitoring"></a>監視

監視在前門 waf 整合 Azure 監視器來追蹤警示並輕鬆地監視流量趨勢。

## <a name="next-steps"></a>後續步驟

- 了解如何[為使用 Azure 入口網站的前端設定 WAF 原則](waf-front-door-create-portal.md)