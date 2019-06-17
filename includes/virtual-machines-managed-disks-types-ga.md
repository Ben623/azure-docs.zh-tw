---
title: 包含檔案
description: 包含檔案
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 05/14/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 2bc5602011ed64b11b1b8c96b7e69a8d5ee9bf32
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "67133225"
---
## <a name="premium-ssd"></a>進階 SSD

針對輸入/輸出 (IO) 工作負載大的虛擬機器 (VM)，Azure 進階 SSD 可提供高效能和低延遲的磁碟支援。 您可以將現有 VM 磁碟遷移到進階 SSD，以利用進階儲存體磁碟的速度和效能。 進階 SSD 適用於任務關鍵性的生產應用程式。 進階 Ssd 只能是進階儲存體 」 相容的 VM 系列。

若要深入了解個別 VM 類型和大小，請在 Azure 的 Windows，包括哪種大小進階儲存體相容，請參閱[Windows VM 大小](../articles/virtual-machines/windows/sizes.md)。 若要深入了解個別 VM 類型和大小，在 Azure 中的針對 Linux，包括哪種大小進階儲存體相容，請參閱[Linux VM 大小](../articles/virtual-machines/linux/sizes.md)。

### <a name="disk-size"></a>磁碟大小
[!INCLUDE [disk-storage-premium-ssd-sizes](disk-storage-premium-ssd-sizes.md)]

當您佈建進階儲存體磁碟時，不同於標準儲存體的是，您可獲得該磁碟的容量、IOPS 和輸送量保證。 例如，如果您建立 P50 磁碟，Azure 會為該磁碟佈建 4,095 GB 儲存體容量、7,500 IOPS 和 250 MB/秒的輸送量。 您的應用程式可以使用全部或部分的容量和效能。 進階 SSD 磁碟被設計來提供低的個位數毫秒延遲，並鎖定 IOPS 和輸送量上述的資料表有 99.9%的時間中所述。

### <a name="transactions"></a>交易

進階 Ssd，每個 I/O 作業小於或等於 256 KiB 的輸送量會被視為單一 I/O 作業。 大於 256 KiB 的輸送量的 I/O 作業會被視為多個 I/o 大小 256 KiB。

## <a name="standard-ssd"></a>標準 SSD

Azure 標準 SSD 是符合成本效益的儲存體選項，已針對在較低 IOPS 層級上需要一致效能的工作負載進行最佳化。 「標準 SSD」為想要移至雲端的使用者，尤其是在於內部部署 HDD 解決方案上執行之工作負載的變異方面遇到問題的使用者，提供一個良好的入門級體驗。 相較於標準的 Hdd，標準的 Ssd 提供更好的可用性、 一致性、 可靠性和延遲。 標準 SSD 適用於網頁伺服器、低 IOPS 應用程式伺服器、不常使用的企業應用程式及開發/測試工作負載。 標準的 Hdd，例如標準 Ssd 可在所有 Azure Vm 上。

### <a name="disk-size"></a>磁碟大小
[!INCLUDE [disk-storage-standard-ssd-sizes](disk-storage-standard-ssd-sizes.md)]

標準的 Ssd 專為提供單一數字毫秒延遲的 IOPS 和輸送量數目達到上述表格 99%的時間中所述的限制。 實際的 IOPS 和輸送量會有所不同有時流量模式。 與 HDD 磁碟相比，「標準 SSD」將可提供延遲更低的更一致效能。

### <a name="transactions"></a>交易

標準的 Ssd，每個 I/O 作業小於或等於 256 KiB 的輸送量會被視為單一 I/O 作業。 大於 256 KiB 的輸送量的 I/O 作業會被視為多個 I/o 大小 256 KiB。 這些交易有計費影響。

## <a name="standard-hdd"></a>標準 HDD

如果 VM 執行的工作負載不在乎延遲，Azure 標準 HDD 可以提供可靠、低成本的磁碟支援。 使用標準儲存體時，資料會儲存在硬碟 (HDD)。 延遲、 IOPS 和標準 HDD 輸送量的磁碟可能會有所不同更廣泛地相較於以 SSD 為基礎的磁碟。 不過的實際效能可能會有所不同的 IO 大小和工作負載模式時，標準的 HDD 磁碟專為提供下 10 毫秒的寫入延遲，並讀取在大部分的 IO 作業的 20 毫秒的延遲。 當使用 Vm，您可以使用標準的 HDD 磁碟，較重要的工作負載和開發/測試案例。 標準的 Hdd 可用於所有 Azure 區域，和適用於所有 Azure Vm。

### <a name="disk-size"></a>磁碟大小
[!INCLUDE [disk-storage-standard-hdd-sizes](disk-storage-standard-hdd-sizes.md)]

### <a name="transactions"></a>交易

針對標準的 Hdd，每個的 IO 作業都會被視為單一交易，不論的 I/O 大小。 這些交易有計費影響。

## <a name="billing"></a>計費

使用受控磁碟時，需考量下列計費資訊：

- 磁碟類型
- 受控磁碟大小
- 快照集
- 輸出資料傳輸
- 交易數

**受控磁碟大小**：受控磁碟會依佈建大小計費。 Azure 會將佈建大小對應 (無條件進位) 至供應項目中最接近的磁碟大小。 若要深入了解提供的磁碟大小，請參閱之前的資料表。 每一個磁碟會對應至供應項目中支援的佈建磁碟大小，並據此計費。 例如，如果您佈建了 200 GiB 的「標準 SSD」，它將會與 E15 (256 GiB) 的磁碟大小供應項目對應。 任何已佈建的磁碟都是使用每月的進階儲存體供應項目價格，以每小時的方式計費。 例如，如果您佈建了 E10 磁碟並在 20 小時後刪除它，便會按 20 小時的比例向您收取 E10 供應項目的費用。 這與寫入磁碟的實際資料量無關。

**快照集**：快照集會根據使用的大小來計費。 例如，如果建立佈建容量為 64 GiB 的受控磁碟快照集，而實際使用資料大小為 10 GiB，則只會對已使用的 10 GiB 資料大小收取快照集費用。
