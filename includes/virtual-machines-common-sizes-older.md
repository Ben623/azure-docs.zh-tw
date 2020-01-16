---
title: 包含檔案
description: 包含檔案
services: virtual-machines-windows, virtual-machines-linux
author: laurenhughes
ms.service: multiple
ms.topic: include
ms.date: 08/15/2019
ms.author: lahugh
ms.custom: include file
ms.openlocfilehash: 1867164954a3f9dff7a8a8c04e249a13edccb84a
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/15/2020
ms.locfileid: "76020876"
---
本節提供舊版虛擬機器大小的相關資訊。 這些大小仍然受支援，但不會收到額外的容量。 有較新或替代的大小已正式推出。 請參閱[azure 中的 Windows 虛擬機器大小](../articles/virtual-machines/windows/sizes.md)或[azure 中 Linux 虛擬機器](../articles/virtual-machines/linux/sizes.md)的大小，以選擇最適合您需求的 VM 大小。  

如需調整 Linux VM 大小的詳細資訊，請參閱[使用 Azure CLI 調整 linux 虛擬機器的大小](../articles/virtual-machines/linux/change-vm-size.md)。 如果您使用的是 Windows Vm，且偏好使用 PowerShell，請參閱[調整 WINDOWS VM 的大小](../articles/virtual-machines/windows/resize-vm.md)。  

<br>

### <a name="basic-a"></a>基本 A  

**較新的大小建議**： [Av2 系列](../articles/virtual-machines/windows/sizes-general.md#av2-series)

進階儲存體：不支援

進階儲存體快取：不支援

基本層大小主要適用於開發工作負載，以及其他不需要負載平衡、自動調整或記憶體高用量虛擬機器的應用程式。

|大小 - 大小\名稱 | vCPU |記憶體|NIC (最大)|暫存磁碟大小上限 |最大 資料磁碟 (每個 1023 GB)|最大 IOPS (每個磁碟 300)|
|---|---|---|---|---|---|---|
|A0\Basic_A0|1|768 MB|2| 20GB|1|1x300|
|A1\Basic_A1|1|1.75 GB|2| 40GB |2|2x300|
|A2\Basic_A2|2|3.5 GB|2| 60 GB|4|4x300|
|A3\Basic_A3|4|7 GB|2| 120 GB |8|8x300|
|A4\Basic_A4|8|14 GB|2| 240 GB |16|16x300|

<br>

### <a name="standard-a0---a4-using-cli-and-powershell"></a>使用 CLI 和 PowerShell 的標準 A0 - A4

在傳統部署模型中，部分 VM 大小名稱會與 CLI 和 PowerShell 中的稍有不同：

* Standard_A0 是「特小型」
* Standard_A1 是「小型」
* Standard_A2 是「中型」
* Standard_A3 是「大型」
* Standard_A4 是「特大型」

### <a name="a-series"></a>A 系列  

**較新的大小建議**： [Av2 系列](../articles/virtual-machines/windows/sizes-general.md#av2-series)

ACU：50 - 100

進階儲存體：不支援

進階儲存體快取：不支援

| 大小 | vCPU | 記憶體：GiB | 暫存儲存體 (HDD)：GiB | 最大資料磁碟 | 最大資料磁碟輸送量︰IOPS | 最大 NIC/預期的網路頻寬 (Mbps)  |
| --- | --- | --- | --- | --- | --- | --- |
| Standard_A0&nbsp;<sup>1</sup> |1 |0.768 |20 |1 |1x500 |2 / 100 |
| Standard_A1 |1 |1.75 |70 |2 |2x500 |2 / 500  |
| Standard_A2 |2 |3.5 |135 |4 |4x500 |2 / 500 |
| Standard_A3 |4 |7 |285 |8 |8x500 |2 / 1000 |
| Standard_A4 |8 |14 |605 |16 |16x500 |4 / 2000 |
| Standard_A5 |2 |14 |135 |4 |4x500 |2 / 500 |
| Standard_A6 |4 |28 |285 |8 |8x500 |2 / 1000 |
| Standard_A7 |8 |56 |605 |16 |16x500 |4 / 2000 |

<sup>1</sup> A0 大小已在實體硬體上過度訂閱。 僅針對這個特定大小，其他客戶部署可能會影響您正在執行的工作負載的效能。 以下概述的相對效能為預期的基準，受限於近似變化性的 15%。

<br>

### <a name="a-series---compute-intensive-instances"></a>A 系列 - 大量計算執行個體  

**較新的大小建議**： [Av2 系列](../articles/virtual-machines/windows/sizes-general.md#av2-series)

ACU：225

進階儲存體：不支援

進階儲存體快取：不支援

A8-A11 和 H 系列大小也稱為 *計算密集型執行個體*。 執行這些大小的硬體是針對計算密集型和網路密集型應用程式 (包括高效能運算 (HPC) 叢集應用程式)、模型化及模擬而設計及最佳化的。 A8-A11 系列使用 Intel Xeon E5-2670 @ 2.6 GHZ，而 H 系列使用 Intel Xeon E5-2667 v3 @ 3.2 GHz。  

| 大小 | vCPU | 記憶體：GiB | 暫存儲存體 (HDD)：GiB | 最大資料磁碟 | 最大資料磁碟輸送量︰IOPS | 最大 NIC|
| --- | --- | --- | --- | --- | --- | --- |
| Standard_A8&nbsp;<sup>1</sup> |8 |56 |382 |32 |32x500 |2 |
| Standard_A9&nbsp;<sup>1</sup> |16 |112 |382 |64 |64x500 |4 |
| Standard_A10 |8 |56 |382 |32 |32x500 |2  |
| Standard_A11 |16 |112 |382 |64 |64x500 |4 |

<sup>1</sup>針對 MPI 應用程式，專用的 RDMA 後端網路是由 QDR 的未受支援網路所啟用，它會提供超低延遲和高頻寬。  

<br>

### <a name="d-series"></a>D 系列  

**較新的大小建議**： [Dv3 系列](../articles/virtual-machines/windows/sizes-general.md#dv3-series-1)

ACU： 160-250 <sup>1</sup>

進階儲存體：不支援

進階儲存體快取：不支援

| 大小         | vCPU | 記憶體：GiB | 暫存儲存體 (SSD) GiB | 最大暫存儲存體輸送量：IOPS / 讀取 MBps / 寫入 MBps | 最大資料磁碟 / 輸送量︰IOPS | 最大 NIC/預期的網路頻寬 (Mbps) |
|--------------|-----------|-------------|----------------|----------------------------------------------------------|-----------------------------------|------------------------------|
| 標準_D1  | 1         | 3.5         | 50             | 3000 / 46 / 23                                           | 4 / 4x500                         | 2 / 500                 |
| 標準_D2  | 2         | 7           | 100            | 6000 / 93 / 46                                           | 8 / 8x500                         | 2 / 1000                     |
| Standard_D3  | 4         | 14          | 200            | 12000 / 187 / 93                                         | 16 / 16x500                         | 4 / 2000                     |
| 標準_D4  | 8         | 28          | 400            | 24000 / 375 / 187                                        | 32 / 32x500                       | 8 / 4000                     |

<sup>1</sup>個 VM 系列可以在下列其中一個 CPU 上執行： 2.2 GHz Intel® E5-2660 v2、2.4 GHz intel Haswell® e5-2673 V3 （）或 2.3 GHz intel® E5-2673 V4 （Broadwell）  

<br>

### <a name="d-series---memory-optimized"></a>D 系列 - 記憶體已最佳化  

**較新的大小建議**： [Dv3 系列](../articles/virtual-machines/windows/sizes-general.md#dv3-series-1)

ACU： 160-250 <sup>1</sup>

進階儲存體：不支援

進階儲存體快取：不支援

| 大小         | vCPU | 記憶體：GiB | 暫存儲存體 (SSD) GiB | 最大暫存儲存體輸送量：IOPS / 讀取 MBps / 寫入 MBps | 最大資料磁碟 / 輸送量︰IOPS | 最大 NIC/預期的網路頻寬 (Mbps) |
|--------------|-----------|-------------|----------------|----------------------------------------------------------|-----------------------------------|------------------------------|
| 標準_D11 | 2         | 14          | 100            | 6000 / 93 / 46                                           | 8 / 8x500                         | 2 / 1000                     |
| 標準_D12 | 4         | 28          | 200            | 12000 / 187 / 93                                         | 16 / 16x500                         | 4 / 2000                     |
| 標準_D13 | 8         | 56          | 400            | 24000 / 375 / 187                                        | 32 / 32x500                       | 8 / 4000                     |
| 標準_D14 | 16        | 112         | 800            | 48000 / 750 / 375                                        | 64 / 64x500                       | 8 / 8000                |

<sup>1</sup>個 VM 系列可以在下列其中一個 CPU 上執行： 2.2 GHz Intel® E5-2660 v2、2.4 GHz intel Haswell® e5-2673 V3 （）或 2.3 GHz intel® E5-2673 V4 （Broadwell）  

<br>

### <a name="ds-series"></a>DS 系列  

**較新的大小建議**： [DSv3 系列](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-general#dsv3-series-1)

ACU： 160-250 <sup>1</sup>

進階儲存體：支援

進階儲存體快取：支援

| 大小 | vCPU | 記憶體：GiB | 暫存儲存體 (SSD) GiB | 最大資料磁碟 | 最大快取和暫存儲存體輸送量︰IOPS / MBps (以 GiB 為單位的快取大小) | 最大取消快取的磁碟輸送量︰IOPS / MBps | 最大 NIC/預期的網路頻寬 (Mbps) |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_DS1 |1 |3.5 |7 |4 |4,000 / 32 (43) |3,200 / 32 |2 / 500 |
| Standard_DS2 |2 |7 |14 |8 |8,000 / 64 (86) |6,400 / 64 |2 / 1000 |
| Standard_DS3 |4 |14 |28 |16 |16,000 / 128 (172) |12,800 / 128 |4 / 2000 |
| Standard_DS4 |8 |28 |56 |32 |32,000 / 256 (344) |25,600 / 256 |8 / 4000 |

<sup>1</sup>個 VM 系列可以在下列其中一個 CPU 上執行： 2.2 GHz Intel® E5-2660 v2、2.4 GHz intel Haswell® e5-2673 V3 （）或 2.3 GHz intel® E5-2673 V4 （Broadwell）  

<br>

### <a name="ds-series---memory-optimized"></a>DS 系列 - 記憶體已最佳化  

**較新的大小建議**： [DSv3 系列](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-general#dsv3-series-1)

ACU： 160-250 <sup>1、2</sup>

進階儲存體：支援

進階儲存體快取：支援

| 大小 | vCPU | 記憶體：GiB | 暫存儲存體 (SSD) GiB | 最大資料磁碟 | 最大快取和暫存儲存體輸送量︰IOPS / MBps (以 GiB 為單位的快取大小) | 最大取消快取的磁碟輸送量︰IOPS / MBps | 最大 NIC/預期的網路頻寬 (Mbps) |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_DS11 |2 |14 |28 |8 |8,000 / 64 (72) |6,400 / 64 |2 / 1000 |
| Standard_DS12 |4 |28 |56 |16 |16,000 / 128 (144) |12,800 / 128 |4 / 2000 |
| Standard_DS13 |8 |56 |112 |32 |32,000 / 256 (288) |25,600 / 256 |8 / 4000 |
| Standard_DS14 |16 |112 |224 |64 |64,000 / 512 (576) |51,200 / 512 |8 / 8000 |

<sup>1</sup> DS 系列 VM 的最大磁碟輸送量 (IOPS 或 MBps)，可能會受到所連接磁碟的數量、大小和串接所限制。  如需詳細資訊，請參閱[為高效能而設計](../articles/virtual-machines/windows/premium-storage-performance.md) \(英文\)。   
<sup>2</sup> VM 系列可以在下列其中一個 CPU 上執行： 2.2 GHz Intel® E5-2660 v2、2.4 GHz intel Haswell® e5-2673 V3 （）或 2.3 GHz intel® E5-2673 V4 （Broadwell）  

<br>

### <a name="ls-series"></a>Ls 系列

Ls 系列使用[Intel® Xeon® 處理器 E5 v3 系列](https://www.intel.com/content/www/us/en/processors/xeon/xeon-e5-solutions.html)，提供最多 32 個 vCPU。 Ls 系列會取得與 G/GS 系列相同的 CPU 效能，而且每個 vCPU 會有 8 GiB 的記憶體。

Ls 系列不支援建立本機快取，來增加可由耐久資料磁碟達成的 IOPS。 本機磁片的高輸送量和 IOPS 讓 Ls 系列 Vm 適用于 NoSQL 存放區（例如 Apache Cassandra 和 MongoDB），其會將資料複寫到多個 Vm，以在單一 VM 失敗時達到持續性。

ACU：180 - 240

進階儲存體：支援

進階儲存體快取：不支援
 
| 大小          | vCPU | 記憶體 (GiB) | 暫存儲存體 (GiB) | 最大資料磁碟 | 最大暫存儲存體輸送量 (IOPS / MBps) | 最大未快取磁碟輸送量 (IOPS / MBps) | 最大 NIC/預期的網路頻寬 (Mbps) | 
|----------------|-----------|-------------|--------------------------|----------------|-------------------------------------------------------------|-------------------------------------------|------------------------------| 
| Standard_L4s   | 4  | 32  | 678   | 16 | 20000/200 | 5000/125  | 2 / 4000  | 
| Standard_L8s   | 8  | 64  | 1388 | 32 | 40000/400 | 10000/250 | 4 / 8000  | 
| Standard_L16s  | 16 | 128 | 2807 | 64 | 80000/800 | 20000/500 | 8 / 16000 | 
| Standard_L32s&nbsp;<sup>1</sup> | 32   | 256  | 5630 | 64   | 160000/1600   | 40000/1000     | 8 / 20000 | 

Ls 系列 VM 的最大磁碟輸送量，可能會受到任何連結磁碟的數量、大小和串接所限制。 如需詳細資訊，請參閱[為高效能而設計](../articles/virtual-machines/windows/premium-storage-performance.md) \(英文\)。

<sup>1</sup> 執行個體會隔離至單一客戶專用的硬體。

### <a name="gs-series"></a>GS 系列 

ACU：180 - 240 <sup>1</sup>

進階儲存體：支援

進階儲存體快取：支援

| 大小 | vCPU | 記憶體：GiB | 暫存儲存體 (SSD) GiB | 最大資料磁碟 | 最大快取和暫存儲存體輸送量︰IOPS / MBps (以 GiB 為單位的快取大小) | 最大取消快取的磁碟輸送量︰IOPS / MBps | 最大 NIC/預期的網路頻寬 (Mbps) |
|---|---|---|---|---|---|---|---|
| Standard_GS1 |2 |28 |56 |8 |10,000 / 100 (264) |5,000 / 125 |2 / 2000 |
| Standard_GS2 |4 |56 |112 |16 |20,000 / 200 (528) |10,000 / 250 |2 / 4000 |
| Standard_GS3 |8 |112 |224 |32 |40,000 / 400 (1,056) |20,000 / 500 |4 / 8000 |
| Standard_GS4&nbsp;<sup>3</sup> |16 |224 |448 |64 |80,000 / 800 (2,112) |40,000 / 1,000 |8 / 16000 |
| Standard_GS5&nbsp;<sup>2,&nbsp;3</sup> |32 |448 |896 |64 |160,000 / 1,600 (4,224) |80,000 / 2,000 |8 / 20000 |

<sup>1</sup> GS 系列 VM 的最大磁碟輸送量 (IOPS 或 MBps)，可能會受到所連接磁碟的數量、大小和串接所限制。 如需詳細資訊，請參閱[為高效能而設計](../articles/virtual-machines/windows/premium-storage-performance.md) \(英文\)。

<sup>2</sup> 執行個體會隔離至單一客戶專用的硬體。

<sup>3</sup> 可用限制核心大小。

<br>

### <a name="g-series"></a>G 系列

ACU：180 - 240

進階儲存體：不支援

進階儲存體快取：不支援

| 大小         | vCPU | 記憶體：GiB | 暫存儲存體 (SSD) GiB | 最大暫存儲存體輸送量：IOPS / 讀取 MBps / 寫入 MBps | 最大資料磁碟 / 輸送量︰IOPS | 最大 NIC/預期的網路頻寬 (Mbps) |
|--------------|-----------|-------------|----------------|----------------------------------------------------------|-----------------------------------|------------------------------|
| Standard_G1  | 2         | 28          | 384            | 6000 / 93 / 46                                           | 8 / 8 x 500                       | 2 / 2000                     |
| Standard_G2  | 4         | 56          | 768            | 12000 / 187 / 93                                         | 16 / 16 x 500                       | 2 / 4000                     |
| Standard_G3  | 8         | 112         | 1,536          | 24000 / 375 / 187                                        | 32 / 32 x 500                     | 4 / 8000                |
| Standard_G4  | 16        | 224         | 3,072          | 48000 / 750 / 375                                        | 64 / 64 x 500                     | 8 / 16000          |
| Standard_G5&nbsp;<sup>1</sup> | 32        | 448         | 6,144          | 96000 / 1500 / 750                                       | 64 / 64 x 500                     | 8 / 20000           |

<sup>1</sup> 執行個體會隔離至單一客戶專用的硬體。
<br>
