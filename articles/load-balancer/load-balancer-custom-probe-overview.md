---
title: 使用 Azure Load Balancer 健康情況探查來調整服務並讓服務達到高可用性
titlesuffix: Azure Load Balancer
description: 了解如何使用健康情況探查來監視 Load Balancer 後方的執行個體
services: load-balancer
documentationcenter: na
author: KumudD
manager: twooley
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/07/2019
ms.author: kumud
ms.openlocfilehash: e488a4a6438279270f3d86dafa16c45eda184059
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "65415717"
---
# <a name="load-balancer-health-probes"></a>Load Balancer 健康情況探查

Azure Load Balancer 具有搭配負載平衡規則使用的健康情況探查功能。  健康情況的探查組態和探查回應會決定由哪個後端集區執行個體接收新流程。 您可以使用健康情況探查來偵測後端執行個體上的應用程式失敗。 您也可以對健康情況探查產生自訂回應，並運用健康情況探查來控制流量，藉此管理負載或排定的停機時間。 當健康情況探查失敗時，Load Balancer 就會停止傳送新流量給狀況不良的個別執行個體。

健康情況探查支援多個通訊協定。 某種類型的健康情況探查是否可支援特定的通訊協定，則視 Load Balancer SKU 而定。  此外，服務行為也會因 Load Balancer SKU 而異。

| | 標準 SKU | 基本 SKU |
| --- | --- | --- |
| [探查類型](#types) | TCP, HTTP, HTTPS | TCP, HTTP |
| [探查關閉行為](#probedown) | 關閉所有探查、繼續所有 TCP 流程。 | 向下的所有探查，所有的 TCP 流量都過期。 | 

> [!IMPORTANT]
> Load Balancer 健康情況探查源自於 IP 位址 168.63.129.16，而且不得封鎖探查以將您的執行個體標示為已啟動。  檢閱[探查來源 IP 位址](#probesource)，以取得詳細資料。

## <a name="types"></a>探查類型

您可以使用下列三種通訊協定為接聽程式設定健康情況探查：

- [接聽程式](#tcpprobe)
- [HTTP 端點](#httpprobe)
- [HTTPS 端點](#httpsprobe)

可用的健康情況探查類型會因為選取的 Load Balancer SKU 不同而有所差異：

|| TCP | HTTP | HTTPS |
| --- | --- | --- | --- |
| 標準 SKU |    &#9989; |   &#9989; |   &#9989; |
| 基本 SKU |   &#9989; |   &#9989; | &#10060; |

無論您選擇的探查類型為何，健康情況探查可以觀察後端執行個體上的任何連接埠，包括在其上提供實際服務的連接埠。

### <a name="tcpprobe"></a> TCP 探查

TCP 探查會利用定義的連接埠執行三向開放 TCP 交握，藉此初始化連線。  TCP 探查會終止與四向 TCP 交握的連線。

最小探查間隔為 5 秒，狀況不良的回應數目下限為 2。  所有間隔的總持續時間不能超過 120 秒。

在以下情況，TCP 探查會失敗：
* 執行個體上的 TCP 接聽程式在逾時期間完全沒有回應。  系統會根據失敗探查要求數目將探查標示為已關閉，而將探查標示為已關閉之前，這些要求會設定為未獲得回應。
* 探查會從執行個體接收 TCP 重設。

#### <a name="resource-manager-template"></a>Resource Manager 範本

```json
    {
      "name": "tcp",
      "properties": {
        "protocol": "Tcp",
        "port": 1234,
        "intervalInSeconds": 5,
        "numberOfProbes": 2
      },
```

### <a name="httpprobe"></a> <a name="httpsprobe"></a> HTTP / HTTPS 探查

> [!NOTE]
> HTTPS 探查僅適用於 [Standard Load Balancer](load-balancer-standard-overview.md)。

HTTP 和 HTTPS 探查建立在 TCP 探查的基礎上，並會發出含有指定路徑的 HTTP GET。 這兩個探查皆支援 HTTP GET 的相對路徑。 HTTPS 探查就是加入傳輸層安全性 (TLS，之前稱為 SSL) 包裝函式的 HTTP 探查。 當執行個體在逾時期限內以 HTTP 狀態 200 回應時，健康情況探查會標示為已啟動。  依預設，健康情況探查會每隔 15 秒嘗試檢查一次已設定的健康情況探查連接埠。 最小探查間隔為 5 秒。 所有間隔的總持續時間不能超過 120 秒。

如果您想要表達健康情況探查，HTTP / HTTPS 探查也很重要。  如果探查連接埠也是服務本身的接聽程式，請實作您自己的邏輯，將執行個體從負載平衡循環中移出。 例如，如果執行個體使用超過 90% CPU，並傳回非 200 HTTP 狀態，您可以決定移除執行個體。 

如果您使用雲端服務而且有使用 w3wp.exe 的 Web 角色，也能讓網站的監視自動化。 網站程式碼中的失敗，會將非 200 的狀態傳回給負載平衡器探查。

在以下情況，HTTP / HTTPS 探查會失敗：
* 探查端點會傳回 200 以外的 HTTP 回應碼 (例如，403、404 或 500)。 這會立即將健康情況探查標記為關閉。 
* 探查端點在 31 秒的逾時期間內完全沒有回應。 在探查被標示為非執行中之前，直到達到所有逾時時間間隔總和為止，這段時間內已有多個探查要求並未獲得回應。
* 探查端點會透過 TCP 重設關閉連線。

#### <a name="resource-manager-templates"></a>Resource Manager 範本

```json
    {
      "name": "http",
      "properties": {
        "protocol": "Http",
        "port": 80,
        "requestPath": "/",
        "intervalInSeconds": 5,
        "numberOfProbes": 2
      },
```

```json
    {
      "name": "https",
      "properties": {
        "protocol": "Https",
        "port": 443,
        "requestPath": "/",
        "intervalInSeconds": 5,
        "numberOfProbes": 2
      },
```

### <a name="guestagent"></a>客體代理程式探查 (僅限傳統)

依預設，雲端服務角色 (背景工作角色和 Web 角色) 會使用客體代理程式進行探查監視。  客體代理程式探查設定是最後的手段。  請一律明確地將健康情況探查搭配 TCP 或 HTTP 探查使用。 在大部分的應用程式案例中，客體代理程式探查的效果不如明確定義的探查。

客體代理程式探查是虛擬機器內客體代理程式的檢查。 然後，只有在執行個體處於就緒狀態時，它才會接聽並以「HTTP 200 確定」回應。 (其他狀態為忙碌、正在回收或正在停止。)

如需詳細資訊，請查看[設定適用於健全狀況探查的服務定義檔案 (csdef)](https://msdn.microsoft.com/library/azure/ee758710.aspx) 或[開始為雲端服務建立公用負載平衡器](load-balancer-get-started-internet-classic-cloud.md#check-load-balancer-health-status-for-cloud-services)。

如果客體代理程式無法以「HTTP 200 確定」回應，則負載平衡器會將執行個體標示為沒有回應。 然後，它會停止將流量傳送到該執行個體。 負載平衡器會繼續檢查執行個體。 

如果客體代理程式以 HTTP 200 回應，則負載平衡器會再次將新流量傳送到該執行個體。

使用 Web 角色時，網站程式碼通常會在不受 Azure 網狀架構或客體代理程式監視的 w3wp.exe 中執行。 在 w3wp.exe 中的失敗 (例如 HTTP 500 回應) 不會向客體代理程式報告。 因此，負載平衡器不會將該執行個體從循環中剔除。

<a name="health"></a>
## <a name="probehealth"></a>探查健康情況

TCP、HTTP 和 HTTPS 健康情況探查於下列狀況時會視為狀況良好，並將角色執行個體標示為狀況良好：

* 健康情況探查在 VM 開機時就成功。
* 已到達將角色執行個體標示為健康所需的指定探查數目。

> [!NOTE]
> 如果健康情況探查出現變動，負載平衡器會先等候較長的時間，才將角色執行個體恢復為良好狀態。 此額外等候時間可保護使用者和基礎結構，且為刻意設計的原則。

## <a name="probe-count-and-timeout"></a>探查計數及逾時

探查行為取決於︰

* 允許執行個體標示為已啟動的成功探查數目。
* 導致執行個體標示為已關閉的失敗探查數目。

指定的逾時和間隔值會決定要將執行個體標示為已啟動或已關閉。

## <a name="probedown"></a>探查關閉行為

### <a name="tcp-connections"></a>TCP 連線

新的 TCP 連線會在剩餘的良好後端執行個體上繼續。

如果後端執行個體的健康情況探查失敗，則這個後端執行個體的已建立 TCP 連線會繼續。

如果後端集區中所有執行個體的所有探查都失敗，則不會有任何新流量傳送至後端集區。 Standard Load Balancer 將允許已建立的 TCP 流量繼續。  Basic Load Balancer 會終止後端集區的所有現有 TCP 流量。
 
Load Balancer 是一種穿透服務 (不會終止 TCP 連線)，且流程一律介於用戶端和虛擬機器的客體作業系統與應用程式之間。 如果沒有狀況良好的後端執行個體可接收流量並以 SYN-ACK 回應，則所有探查都已關閉的集區會導致前端不回應 TCP 連線開啟嘗試 (SYN)。

### <a name="udp-datagrams"></a>UDP 資料包

UDP 資料包會傳遞至狀況良好的後端執行個體。

UDP 是不需連線的，因此 UDP 沒有流程狀態追蹤。 如有任何後端執行個體的健康情況探查失敗，則現有的 UDP 流量可能會移到後端集區中另一個狀況良好的執行個體。

如果後端集區中所有執行個體的所有探查都失敗，則 Basic 和 Standard Load Balancer 的現有 UDP 流量將會終止。

<a name="source"></a>
## <a name="probesource"></a>探查來源 IP 位址

Load Balancer 會為其內部健康情況模型使用分散式探查服務。 探查服務駐留在 VM 所在的每個主機上，可以視需要透過程式設計方式來為每個客戶的組態產生健康情況探查。 健康情況探查流量會直接在產生健康情況探查的探查服務和客戶虛擬機器之間產生。 所有 Load Balancer 健康情況探查都源自作為其來源的 IP 位址 168.63.129.16。  您可以在非 RFC1918 空間的 VNet 內部使用 IP 位址空間。  使用全域保留且為 Microsoft 擁有的 IP 位址，會減少 IP 位址與您在 VNet 內所用的 IP 位址空間發生衝突的機會。  此 IP 位址在所有區域內都是相同的，不會改變，而且因為只有內部 Azure 平台元件會從此 IP 位址獲得封包，所以此位址並不會有安全性風險。 

AzureLoadBalancer 服務標籤會在您的[網路安全性群組](../virtual-network/security-overview.md)中識別此來源 IP 位址，且預設即會許可健康情況探查的流量。

負載平衡器健康情況探查，除了[下列作業會使用此 IP 位址](../virtual-network/what-is-ip-address-168-63-129-16.md):

- 啟用 VM 代理程式來與平台通訊，藉此表示它處於「就緒」狀態
- 啟用與 DNS 虛擬伺服器的通訊，以提供篩選後的名稱解析給未定義自訂 DNS 伺服器的客戶。  此篩選可確保客戶只可以解析其部署的主機名稱。
- 允許 VM 從 Azure 中的 DHCP 服務取得動態 IP 位址。

## <a name="design"></a> 設計指引

健康情況探查用於讓您的服務具有復原性，並讓服務得以調整級別。 錯誤的設定或不良的設計模式，都可能會影響您服務的可用性和延展性。 請詳讀整份文件，想一想當這此探查被標示為開啟或關閉時，會對您的案例造成何種影響，又會如何影響您應用程式的可用性。

在設計應用程式的健康情況模型時，請探查後端執行個體上會反映該執行個體__和__您所提供應用程式服務之健康情況的連接埠。  應用程式連接埠和探查連接埠不一定要相同。  在某些情況下，探查連接埠可能需要與您應用程式提供服務的連接埠不同。  

有時候對您的應用程式而言，產生健康情況探查回應不僅會偵測到應用程式的健康情況，且無論您的執行個體是否應接收新流量，還能將訊號直接發送給 Load Balancer，這點相當有用。  您可以運用探查回應讓應用程式得以建立背壓，並透過讓健康情況探查失敗，或是準備好對應用程式進行維護並開始清空案例的作法，將傳遞到執行個體的新流量加以節流。  使用 Standard Load Balancer 時，[探查失敗](#probedown)訊號一律會允許 TCP 流量繼續，直到閒置逾時或連線關閉為止。 

至於 UDP 負載平衡方面，請從後端執行個體產生自訂健康情況探查訊號，並使用 TCP、HTTP 或 HTTPS 健康情況探查，這些探查會以對應的接聽程式為目標，可反映 UDP 應用程式的健康情況。

使用 [HA 連接埠負載平衡規則](load-balancer-ha-ports-overview.md)搭配 [Standard Load Balancer](load-balancer-standard-overview.md) 時，所有連接埠都會進行負載平衡，而單一健康情況探查的回應則必須反映出整個執行個體的狀態。

請勿透過接受健康情況探查的執行個體，將健康情況探查轉化或通過 Proxy 處理至 VNet 中的另一個執行個體，因為此設定可能導致您的案例發生連鎖性失敗。  請設想一下以下狀況：有一組第三方設備部署在 Load Balancer 資源的後端集區中，藉以提供設備所需的規模和備援能力，且健康情況探查會設定為負責探查第三方設備通過 Proxy 處理或轉化至設備背後的其他虛擬機器時所使用的連接埠。  如果您探查的連接埠相同於您要用來將要求轉化或通過 Proxy 處理至設備背後其他虛擬機器的連接埠，任何來自設備背後單一虛擬機器的探查回應都會將設備本身標記為無作用。 此設定也會因為設備背後的單一後端執行個體的緣故，導致整個應用程式案例發生連鎖性失敗。  觸發程序可能會是間歇性探查失敗，而會造成 Load Balancer 將原始目的地 (應用程式執行個體) 標示為關閉，接著會停用您整個應用程式案例。 請改為探查設備本身的健康情況。 挑選要以何種探查來判定健康情況訊號，對網路虛擬設備 (NVA) 案例而言是一大考量，請務必諮詢您的應用程式供應商，確定什麼健康情況訊號適合此類案例。

如果您不允許防火牆原則中有此探查的[來源 IP](#probesource)，則健康情況探查將會失敗，因為它無法接觸您的執行個體。  接著，Load Balancer 會因為健康情況探查失敗而將您的執行個體標示為已關閉。  此種設定會造成負載已平衡的應用程式案例失敗。

為了讓 Load Balancer 的健康情況探查將您的執行個體標示為已開啟，您**必須**在任何 Azure [網路安全性群組](../virtual-network/security-overview.md)和本機防火牆原則中允許此 IP 位址。  預設中，每個網路安全性群組皆含有[服務標籤](../virtual-network/security-overview.md#service-tags) AzureLoadBalancer，以許可健康情況探查流量。

如果您想要測試健康情況探查的失敗，或將個別的執行個體標示為已關閉，您可以使用[網路安全性群組](../virtual-network/security-overview.md)明確封鎖健康情況探查 (目的地連接埠或[來源 IP](#probesource))，並模擬探查失敗。

請勿使用 Microsoft 所擁有的 IP 位址範圍 (含 168.63.129.16 在內) 來設定您的 VNet。  這種設定會與健康情況探查的 IP 位址相衝突，而可能導致您的案例失敗。

如果您的 VM 上有多個介面，您必須確保在您接收探查的介面上進行回應。  您可能需要在每個介面上將來源網路位址在虛擬機器中轉換成此位址。

請勿啟用 [TCP 時間戳記](https://tools.ietf.org/html/rfc1323)。  啟用 TCP 時間戳記會造成健康情況探查失敗，因為虛擬機器的客體作業系統 TCP 堆疊會不斷卸除 TCP 封包，導致 Load Balancer 將個別的端點標記為已關閉。  TCP 時間戳記依預設會在安全性強化的虛擬機器映像上定期啟用，而必須停用。

## <a name="monitoring"></a>監視

公用和內部 [Standard Load Balancer](load-balancer-standard-overview.md) 會透過 Azure 監視器將每個端點和後端執行個體的健康情況探查狀態公開為多維度計量。 這些計量可供其他 Azure 服務或協力廠商應用程式。 

基本的公用 Load Balancer 會公開每個後端集區，透過 Azure 監視器記錄檔彙總的健康情況探查狀態。  Azure 監視器記錄不適用於內部基本負載平衡器。  您可以使用[Azure 監視器記錄](load-balancer-monitor-log.md)檢查公用負載平衡器探查健全狀況狀態和探查計數。 記錄可以與 Power BI 或 Azure Operation Insights 搭配使用，以提供負載平衡器健康狀態。

## <a name="limitations"></a>限制

- HTTPS 探查不支援使用用戶端憑證進行相互驗證。
- 當 TCP 時間戳記啟用時，健康情況探查會失敗。

## <a name="next-steps"></a>後續步驟

- 深入了解[標準 Load Balancer](load-balancer-standard-overview.md)
- [開始使用 PowerShell 在資源管理員中建立公用負載平衡器](load-balancer-get-started-internet-arm-ps.md)
- [適用於健康情況探查的 REST API](https://docs.microsoft.com/rest/api/load-balancer/loadbalancerprobes/)
- 透過 [Load Balancer 的 Uservoice](https://aka.ms/lbuservoice) 要求新的健康情況探查功能
