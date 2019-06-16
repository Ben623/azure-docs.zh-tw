---
title: Azure 虛擬機器網路輸送量 | Microsoft Docs
description: 了解 Azure 虛擬機器網路輸送量。
services: virtual-network
documentationcenter: na
author: steveesp
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 4/26/2019
ms.author: kumud,steveesp, mareat
ms.openlocfilehash: 9d74e53c754367ecfa63642514db93354fcadf25
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "65153755"
---
# <a name="virtual-machine-network-bandwidth"></a>虛擬機器網路頻寬

Azure 提供各種 VM 大小和類型，各有不同的效能功能組合。 其中一個功能是網路輸送量 (或頻寬)，測量單位是 MB/秒 (Mbps)。 因為虛擬機器裝載於共用硬體，網路容量必須平均分給共用相同硬體的虛擬機器。 相對於較小的虛擬機器，大型的虛擬機器會配置較多的頻寬。
 
配置給每個虛擬機器的網路頻寬是依據從虛擬機器的流出 (輸出) 流量來計量。 所有離開虛擬機器的網路流量都會計算到配置的限制，不會因為目的地不同而有差異。 例如，如果虛擬機器有 1,000 Mbps 的限制，則無論輸出流量的目的地是相同虛擬網路中的另一部虛擬機器，或是 Azure 之外的虛擬機器，皆會套用該限制。
 
輸入則不會直接計量或限制。 不過，還有其他因素，例如 CPU 和儲存體限制可能會影響虛擬機器處理內送資料的能力。

加速網路是設計來改善網路效能 (包括延遲、輸送量和 CPU 使用率) 的功能。 雖然加速網路可提升虛擬機器的輸送量，但不能超過虛擬機器的配置頻寬。 若要深入了解加速網路，請參閱適用於 [Windows](create-vm-accelerated-networking-powershell.md) 或 [Linux](create-vm-accelerated-networking-cli.md) 虛擬機器的＜加速網路＞。
 
Azure 虛擬機器必須連結一個 (但可以有數個) 網路介面。 配置給虛擬機器的頻寬，是虛擬機器上所有網路介面的所有輸出流量總和。 換句話說，配置的頻寬是以每個虛擬機器為依據，跟連結多少個網路介面到虛擬機器無關。 若要了解不同 Azure 虛擬機器大小支援的網路介面有多少，請參閱 Azure [Windows](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 和 [Linux](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 虛擬機器大小。 

## <a name="expected-network-throughput"></a>預期的網路輸送量

預期的輸出輸送量和每個虛擬機器大小支援的網路介面數目，皆詳細列在 Azure [Windows](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 和 [Linux](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 虛擬機器大小中。 選取類型，例如一般用途，然後在產生的頁面上選取大小的系列，例如 Dv2 系列。 每個數列都有資料表，且最後一個資料行包含網路規格，標題為**最大 NIC 數 / 預期的網路效能 (Mbps)** 。 

輸送量限制會套用至虛擬機器。 輸送量不會受到下列因素影響：
- **網路介面數目**:頻寬限制，都會累積所有的輸出流量從虛擬機器。
- **加速網路**:此功能，能幫助您達到已發佈的限制，但它不會變更限制。
- **流量目的地**:所有目的地都會計入輸出限制。
- **通訊協定**：透過所有通訊協定的所有輸出流量都會計入限制。

## <a name="network-flow-limits"></a>網路流量限制

除了頻寬，在任何時候存在 VM 上的網路連線的數目可能會影響其網路效能。 Azure 的網路堆疊會維護每個方向中呼叫 '流程' 的資料結構的 TCP/UDP 連接的狀態。 一般的 TCP/UDP 連接將會有 2 個另一個用於輸入和輸出方向的另一個建立的流程。 

端點之間的資料傳輸需要建立數個流程以外執行資料傳輸。 建立用於 DNS 解析的流程和流程建立負載平衡器健康情況探查的一些範例。 也請注意，網路虛擬設備 (Nva)，例如閘道、 proxy、 防火牆，會看到正在建立設備在終止與來源應用裝置所連線的流程。 

![TCP 透過轉送設備的交談流程計數](media/virtual-machine-network-throughput/flow-count-through-network-virtual-appliance.png)

## <a name="flow-limits-and-recommendations"></a>Flow 限制和建議

現在，Azure 的網路堆疊會有超過 8 顆 CPU 核心和 100 k 總流量具備良好效能的 Vm 具有少於 8 個 CPU 核心的 Vm 支援 250k 個總計的網路流量，具備良好效能。 超過此限制的網路效能降低依正常程序的硬上限是 1 百萬個其他流程總流程中，輸入和 500 的 500k K 輸出之後會卸除哪些額外的流程。

||Vm 有 < 8 顆 CPU 核心|具有 8 + 個 CPU 核心的 Vm|
|---|---|---|
|<b>良好的效能</b>|100k 流程 |250k 個流程|
|<b>效能降低</b>|高於 100 k 流程|250k 個以上的流程|
|<b>Flow 限制</b>|1 百萬個流程|1 百萬個流程|

計量可用於[Azure 監視器](../azure-monitor/platform/metrics-supported.md#microsoftcomputevirtualmachines)追蹤 VM 或 VMSS 執行個體上的網路流量數目和流程建立速率。

![azure-monitor-flow-metrics.png](media/virtual-machine-network-throughput/azure-monitor-flow-metrics.png)

連線的建立和終止的比率也會影響網路效能，為連接的建立和終止共用 CPU 以封包處理常式。 我們建議，您建立基準針對預期的流量模式和向外延展工作負載的工作負載適當地以符合您效能需求。 

## <a name="next-steps"></a>後續步驟

- [最佳化虛擬機器作業系統的網路輸送量](virtual-network-optimize-network-bandwidth.md)
- 針對虛擬機器，[測試網路輸送量](virtual-network-bandwidth-testing.md)。
