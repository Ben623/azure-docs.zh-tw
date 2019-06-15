---
title: Azure 網路監看員的網路安全性群組流量記錄簡介 | Microsoft Docs
description: 本文說明如何使用 Azure 網路監看員的 NSG 流量記錄功能。
services: network-watcher
documentationcenter: na
author: KumudD
manager: twooley
editor: ''
ms.assetid: 47d91341-16f1-45ac-85a5-e5a640f5d59e
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: kumud
ms.openlocfilehash: 1ec7fd4116aa848a9c431df386997cb23f405f1b
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "64925418"
---
# <a name="introduction-to-flow-logging-for-network-security-groups"></a>網路安全性群組流量記錄簡介

網路安全性群組 (NSG) 流量記錄是網路監看員的一項功能，可讓您檢視透過 NSG 輸入和輸出 IP 流量的相關資訊。 流量記錄是以 JSON 格式撰寫，會顯示每一規則的輸出和輸入流量、套用流量的網路介面 (NIC)、關於流量的 5 個 Tuple 資訊 (來源/目的地 IP、來源/目的地連接埠和通訊協定)、流量受到允許或拒絕，以及第 2 版中的輸送量資訊 (位元組和封包)。


![流量記錄概觀](./media/network-watcher-nsg-flow-logging-overview/figure1.png)

雖然流量記錄是以 NSG 為目標，但其顯示方式與其他記錄不同。 流量記錄只會儲存在儲存體帳戶內，並且會採用以下範例所示的記錄路徑：

```
https://{storageAccountName}.blob.core.windows.net/insights-logs-networksecuritygroupflowevent/resourceId=/SUBSCRIPTIONS/{subscriptionID}/RESOURCEGROUPS/{resourceGroupName}/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/{nsgName}/y={year}/m={month}/d={day}/h={hour}/m=00/macAddress={macAddress}/PT1H.json
```
您可以使用[流量分析](traffic-analytics.md)來分析流量記錄，並取得網路流量的見解。

在其他記錄上看到的保留原則也同樣適用於流量記錄。 您可以設定 1 天到 2147483647 天的記錄保留原則。 如果未設定保留原則，則會永遠保留記錄。

> [!NOTE] 
> 透過 NSG 流量記錄使用保留原則功能可能會產生大量的儲存體作業和相關成本。 如果您不需要保留原則功能，建議您將此值設為 0。


## <a name="log-file"></a>記錄檔

流量記錄包含下列屬性：

* **time** - 事件的記錄時間
* **systemId** - 網路安全性群組資源識別碼。
* **類別** - 事件的類別。 類別一律為 **NetworkSecurityGroupFlowEvent**
* **resourceid** - NSG 的資源識別碼
* **operationName** - 一律是 NetworkSecurityGroupFlowEvents
* **properties** - 流量屬性的集合
    * **Version** - 流量記錄事件結構描述的版本號碼
    * **flows** - 流量的集合。 這個屬性有多個適用於不同規則的項目
        * **rule** - 做為流量列出依據的規則
            * **flows** - 流量的集合
                * **mac** - 流量收集所在 VM 之 NIC 的 MAC 位址
                * **flowTuples** - 包含多個流量 tuple 屬性的逗號分隔格式字串
                    * **時間戳記** - 這個值是流量發生時的時間戳記，格式為 UNIX EPOCH
                    * **來源 IP** - 來源 IP
                    * **目的地 IP** - 目的地 IP
                    * **來源連接埠** - 來源連接埠
                    * **目的地連接埠** - 目的地連接埠
                    * **通訊協定** - 流量的通訊協定。 有效值為 **T** (若為 TCP) 和 **U** (若為 UDP)
                    * **流量流動** - 流量的流動方向。 有效值為 **I** (若為輸入) 和 **O** (若為輸出)。
                    * **流量判定** - 流量受到允許或拒絕。 有效值為 **A** (若允許) 和 **D** (若拒絕)。
                    * **流量狀態 - 僅限第 2 版** - 擷取流程的狀態。 可能的狀態為 **B**：開始，當流量建立時。 不提供統計資料。 **C**：繼續進行中的流量。 提供 5 分鐘間隔的統計資料。 **E**：結束，當流量終止時。 提供統計資料。
                    * **封包 - 來源到目的地 - 僅限第 2 版** 上次更新之後從來源傳送到目的地的 TCP 或 UDP 封包總數。
                    * **傳送的位元組 - 來源到目的地 - 僅限第 2 版** 上次更新之後從來源傳送到目的地的 TCP 或 UDP 封包位元組總數。 封包位元組包括封包標頭與承載。
                    * **封包 - 目的地到來源 - 僅限第 2 版** 上次更新之後從目的地傳送到來源的 TCP 或 UDP 封包總數。
                    * **傳送的位元組 - 目的地到來源 - 僅限第 2 版** 上次更新之後從目的地傳送到來源的 TCP 與 UDP 封包位元組總數。 封包位元組包括封包標頭與承載。

## <a name="nsg-flow-logs-version-2"></a>NSG 流量記錄第 2 版

第 2 版的記錄引進流量狀態。 您可以設定您所收到的流量記錄版本。 若要了解如何啟用流量記錄，請參閱[啟用 NSG 流量記錄](network-watcher-nsg-flow-logging-portal.md)。

當起始流量時，會記錄流量狀態 *B*。 流量狀態 *C* 與流量狀態 *E* 是分別標記繼續傳送流量與流量終止的狀態。 *C* 與 *E* 狀態包含流量頻寬資訊。

針對繼續 *C* 與結束 *E* 流量狀態，位元組與封包計數是從上一個流量 Tuple 記錄開始時的彙總計數。 參考上一個範例對話，已傳輸的封包總數是 1021+52+8005+47 = 9125。 已傳輸的位元組總數是 588096+29952+4610880+27072 = 5256000。

**範例**：來自 185.170.185.105:35370 與 10.2.0.4:23 間之 TCP 對話的流量 Tuple：

"1493763938,185.170.185.105,10.2.0.4,35370,23,T,I,A,B,,,," "1493695838,185.170.185.105,10.2.0.4,35370,23,T,I,A,C,1021,588096,8005,4610880" "1493696138,185.170.185.105,10.2.0.4,35370,23,T,I,A,E,52,29952,47,27072"

針對繼續 *C* 與結束 *E* 流量狀態，位元組與封包計數是從上一個流量 Tuple 記錄開始時的彙總計數。 參考上一個範例對話，已傳輸的封包總數是 1021+52+8005+47 = 9125。 已傳輸的位元組總數是 588096+29952+4610880+27072 = 5256000。

之後的文字是流量記錄範例。 如您所見，有多筆記錄遵循上一節所述的屬性清單。

## <a name="nsg-flow-logging-considerations"></a>NSG 流量記錄考量

**在所有附加到資源的 NSG 上啟用 NSG 流量記錄**：Azure 中的流量記錄是在 NSG 資源上設定的。 流量只會與一個 NSG 規則相關聯。 在利用多個 NSG 的情況下，建議在適用於資源子網路或網路介面的所有 NSG 上啟用 NSG 流量記錄，以確保會記錄所有的流量。 如需網路安全性群組的詳細資訊，請參閱[如何評估流量](../virtual-network/security-overview.md#how-traffic-is-evaluated)。 

**流量記錄成本**：NSG 流量記錄是以產生的記錄量來計費。 高流量可能會產生大量流量記錄和相關費用。 NSG 流量記錄價格不包括儲存體的基礎結構費用。 透過 NSG 流量記錄使用保留原則功能可能會產生大量的儲存體作業和相關成本。 如果您不需要保留原則功能，建議您將此值設為 0。 如需詳細資訊，請參閱[網路監看員價格](https://azure.microsoft.com/pricing/details/network-watcher/)和 [Azure 儲存體定價](https://azure.microsoft.com/pricing/details/storage/)。

**輸入記錄來自網際網路的 Ip，而不需要公用 Ip 的 vm 的流程**:Vm 未透過與作為執行的執行個體層級公用 IP，NIC 相關聯的公用 IP 位址指派的公用 IP 位址或屬於基本負載平衡器後端集區，使用[預設 SNAT](../load-balancer/load-balancer-outbound-connections.md#defaultsnat)和已指派的 IP 位址Azure，以進行輸出連線。 如此一來，您可能會看到流程的流程記錄項目從網際網路 IP 位址，如果流程的目的指派之 SNAT 連接埠的範圍中的連接埠。 Azure 不會讓這些流量到 VM，並同時嘗試會記錄，並依設計會出現在 網路監看員的 NSG 流量記錄。 我們建議您不想要的輸入的網際網路流量與 NSG 會明確遭到封鎖。

## <a name="sample-log-records"></a>範例記錄的記錄

之後的文字是流量記錄範例。 如您所見，有多筆記錄遵循上一節所述的屬性清單。


> [!NOTE]
> \*flowTuples  屬性中的值是逗號分隔清單。
 
### <a name="version-1-nsg-flow-log-format-sample"></a>第 1 版 NSG 流量記錄格式範例
```json
{
    "records": [
        {
            "time": "2017-02-16T22:00:32.8950000Z",
            "systemId": "2c002c16-72f3-4dc5-b391-3444c3527434",
            "category": "NetworkSecurityGroupFlowEvent",
            "resourceId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-000000000000/RESOURCEGROUPS/FABRIKAMRG/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/FABRIAKMVM1-NSG",
            "operationName": "NetworkSecurityGroupFlowEvents",
            "properties": {
                "Version": 1,
                "flows": [
                    {
                        "rule": "DefaultRule_DenyAllInBound",
                        "flows": [
                            {
                                "mac": "000D3AF8801A",
                                "flowTuples": [
                                    "1487282421,42.119.146.95,10.1.0.4,51529,5358,T,I,D"
                                ]
                            }
                        ]
                    },
                    {
                        "rule": "UserRule_default-allow-rdp",
                        "flows": [
                            {
                                "mac": "000D3AF8801A",
                                "flowTuples": [
                                    "1487282370,163.28.66.17,10.1.0.4,61771,3389,T,I,A",
                                    "1487282393,5.39.218.34,10.1.0.4,58596,3389,T,I,A",
                                    "1487282393,91.224.160.154,10.1.0.4,61540,3389,T,I,A",
                                    "1487282423,13.76.89.229,10.1.0.4,53163,3389,T,I,A"
                                ]
                            }
                        ]
                    }
                ]
            }
        },
        {
            "time": "2017-02-16T22:01:32.8960000Z",
            "systemId": "2c002c16-72f3-4dc5-b391-3444c3527434",
            "category": "NetworkSecurityGroupFlowEvent",
            "resourceId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-000000000000/RESOURCEGROUPS/FABRIKAMRG/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/FABRIAKMVM1-NSG",
            "operationName": "NetworkSecurityGroupFlowEvents",
            "properties": {
                "Version": 1,
                "flows": [
                    {
                        "rule": "DefaultRule_DenyAllInBound",
                        "flows": [
                            {
                                "mac": "000D3AF8801A",
                                "flowTuples": [
                                    "1487282481,195.78.210.194,10.1.0.4,53,1732,U,I,D"
                                ]
                            }
                        ]
                    },
                    {
                        "rule": "UserRule_default-allow-rdp",
                        "flows": [
                            {
                                "mac": "000D3AF8801A",
                                "flowTuples": [
                                    "1487282435,61.129.251.68,10.1.0.4,57776,3389,T,I,A",
                                    "1487282454,84.25.174.170,10.1.0.4,59085,3389,T,I,A",
                                    "1487282477,77.68.9.50,10.1.0.4,65078,3389,T,I,A"
                                ]
                            }
                        ]
                    }
                ]
            }
        },
    "records":
    [
        
        {
             "time": "2017-02-16T22:00:32.8950000Z",
             "systemId": "2c002c16-72f3-4dc5-b391-3444c3527434",
             "category": "NetworkSecurityGroupFlowEvent",
             "resourceId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-000000000000/RESOURCEGROUPS/FABRIKAMRG/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/FABRIAKMVM1-NSG",
             "operationName": "NetworkSecurityGroupFlowEvents",
             "properties": {"Version":1,"flows":[{"rule":"DefaultRule_DenyAllInBound","flows":[{"mac":"000D3AF8801A","flowTuples":["1487282421,42.119.146.95,10.1.0.4,51529,5358,T,I,D"]}]},{"rule":"UserRule_default-allow-rdp","flows":[{"mac":"000D3AF8801A","flowTuples":["1487282370,163.28.66.17,10.1.0.4,61771,3389,T,I,A","1487282393,5.39.218.34,10.1.0.4,58596,3389,T,I,A","1487282393,91.224.160.154,10.1.0.4,61540,3389,T,I,A","1487282423,13.76.89.229,10.1.0.4,53163,3389,T,I,A"]}]}]}
        }
        ,
        {
             "time": "2017-02-16T22:01:32.8960000Z",
             "systemId": "2c002c16-72f3-4dc5-b391-3444c3527434",
             "category": "NetworkSecurityGroupFlowEvent",
             "resourceId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-000000000000/RESOURCEGROUPS/FABRIKAMRG/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/FABRIAKMVM1-NSG",
             "operationName": "NetworkSecurityGroupFlowEvents",
             "properties": {"Version":1,"flows":[{"rule":"DefaultRule_DenyAllInBound","flows":[{"mac":"000D3AF8801A","flowTuples":["1487282481,195.78.210.194,10.1.0.4,53,1732,U,I,D"]}]},{"rule":"UserRule_default-allow-rdp","flows":[{"mac":"000D3AF8801A","flowTuples":["1487282435,61.129.251.68,10.1.0.4,57776,3389,T,I,A","1487282454,84.25.174.170,10.1.0.4,59085,3389,T,I,A","1487282477,77.68.9.50,10.1.0.4,65078,3389,T,I,A"]}]}]}
        }
        ,
        {
             "time": "2017-02-16T22:02:32.9040000Z",
             "systemId": "2c002c16-72f3-4dc5-b391-3444c3527434",
             "category": "NetworkSecurityGroupFlowEvent",
             "resourceId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-000000000000/RESOURCEGROUPS/FABRIKAMRG/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/FABRIAKMVM1-NSG",
             "operationName": "NetworkSecurityGroupFlowEvents",
             "properties": {"Version":1,"flows":[{"rule":"DefaultRule_DenyAllInBound","flows":[{"mac":"000D3AF8801A","flowTuples":["1487282492,175.182.69.29,10.1.0.4,28918,5358,T,I,D","1487282505,71.6.216.55,10.1.0.4,8080,8080,T,I,D"]}]},{"rule":"UserRule_default-allow-rdp","flows":[{"mac":"000D3AF8801A","flowTuples":["1487282512,91.224.160.154,10.1.0.4,59046,3389,T,I,A"]}]}]}
        }
        ,
        ...
```
### <a name="version-2-nsg-flow-log-format-sample"></a>第 2 版 NSG 流量記錄格式範例
```json
 {
    "records": [
        {
            "time": "2018-11-13T12:00:35.3899262Z",
            "systemId": "a0fca5ce-022c-47b1-9735-89943b42f2fa",
            "category": "NetworkSecurityGroupFlowEvent",
            "resourceId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-000000000000/RESOURCEGROUPS/FABRIKAMRG/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/FABRIAKMVM1-NSG",
            "operationName": "NetworkSecurityGroupFlowEvents",
            "properties": {
                "Version": 2,
                "flows": [
                    {
                        "rule": "DefaultRule_DenyAllInBound",
                        "flows": [
                            {
                                "mac": "000D3AF87856",
                                "flowTuples": [
                                    "1542110402,94.102.49.190,10.5.16.4,28746,443,U,I,D,B,,,,",
                                    "1542110424,176.119.4.10,10.5.16.4,56509,59336,T,I,D,B,,,,",
                                    "1542110432,167.99.86.8,10.5.16.4,48495,8088,T,I,D,B,,,,"
                                ]
                            }
                        ]
                    },
                    {
                        "rule": "DefaultRule_AllowInternetOutBound",
                        "flows": [
                            {
                                "mac": "000D3AF87856",
                                "flowTuples": [
                                    "1542110377,10.5.16.4,13.67.143.118,59831,443,T,O,A,B,,,,",
                                    "1542110379,10.5.16.4,13.67.143.117,59932,443,T,O,A,E,1,66,1,66",
                                    "1542110379,10.5.16.4,13.67.143.115,44931,443,T,O,A,C,30,16978,24,14008",
                                    "1542110406,10.5.16.4,40.71.12.225,59929,443,T,O,A,E,15,8489,12,7054"
                                ]
                            }
                        ]
                    }
                ]
            }
        },
        {
            "time": "2018-11-13T12:01:35.3918317Z",
            "systemId": "a0fca5ce-022c-47b1-9735-89943b42f2fa",
            "category": "NetworkSecurityGroupFlowEvent",
            "resourceId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-000000000000/RESOURCEGROUPS/FABRIKAMRG/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/FABRIAKMVM1-NSG",
            "operationName": "NetworkSecurityGroupFlowEvents",
            "properties": {
                "Version": 2,
                "flows": [
                    {
                        "rule": "DefaultRule_DenyAllInBound",
                        "flows": [
                            {
                                "mac": "000D3AF87856",
                                "flowTuples": [
                                    "1542110437,125.64.94.197,10.5.16.4,59752,18264,T,I,D,B,,,,",
                                    "1542110475,80.211.72.221,10.5.16.4,37433,8088,T,I,D,B,,,,",
                                    "1542110487,46.101.199.124,10.5.16.4,60577,8088,T,I,D,B,,,,",
                                    "1542110490,176.119.4.30,10.5.16.4,57067,52801,T,I,D,B,,,,"
                                ]
                            }
                        ]
                    }
                ]
            }
        },
        ...
```

## <a name="next-steps"></a>後續步驟

- 若要了解如何啟用流量記錄，請參閱[啟用 NSG 流量記錄](network-watcher-nsg-flow-logging-portal.md)。
- 若要了解如何讀取流量記錄，請參閱[讀取 NSG 流量記錄](network-watcher-read-nsg-flow-logs.md)。
- 若要深入了解 NSG 記錄，請參閱[Azure 監視器記錄的網路安全性群組 (Nsg)](../virtual-network/virtual-network-nsg-manage-log.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json)。
- 若要判斷是否允許或拒絕流量進出 VM，請參閱[診斷 VM 網路流量篩選問題](diagnose-vm-network-traffic-filtering-problem.md)
