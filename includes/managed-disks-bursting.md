---
title: 包含檔案
description: 包含檔案
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 02/28/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: a04df7ed283a17ddad6af87cf8215ff8d39a5079
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/29/2020
ms.locfileid: "78202517"
---
磁片高載目前是 premium Ssd 的預覽功能。 任何 premium SSD 磁片大小都支援高載，< = 512 GiB （P20 或以下）。 這些磁片大小支援高載，並利用信用系統來管理負載平衡。 當磁片流量低於已布建的效能目標時，信用額度會累積在高載值區中，而且當流量高載超過目標時，就會耗用點數。 磁片流量會針對布建目標中的 IOPS 和頻寬進行追蹤。 磁片高載不會略過虛擬機器（VM）對 IOPS 或輸送量的大小限制。

預設會在支援的磁片大小的新部署上啟用磁片負載平衡。 現有的磁片大小如果支援磁片高載，可以透過下列其中一種方法來啟用高載：

- 卸離並重新附加磁片。
- 停止並啟動 VM。

## <a name="burst-states"></a>高載狀態

當磁片連接至虛擬機器時，所有高載適用的磁片大小將從完整的高載點數值區開始。 高載的最大持續時間取決於高載點數值區的大小。 您只能累積未使用的信用額度，直到點數值區的大小為止。 在任何時間點，您的磁片高載點數值區可以是下列三種狀態之一： 

- 當磁片流量使用低於布建的效能目標時，就會產生這種情況。 如果磁片流量超出 IOPS 或頻寬目標，您可以累積點數。 當您使用完整磁片頻寬時，您仍然可以累積 IO 點數，反之亦然。  

- 當磁片流量使用超過布建的效能目標時，會拒絕。 高載流量會獨立使用 IOPS 或頻寬的點數。 

- 當磁片流量完全位於已布建的效能目標時，剩餘的常數。 

下表摘要說明提供負載平衡支援的磁片大小以及高載規格。

## <a name="regional-availability"></a>區域可用性

目前，磁片高載僅適用于美國中西部區域。

## <a name="disk-sizes"></a>磁碟大小

[!INCLUDE [disk-storage-premium-ssd-sizes](disk-storage-premium-ssd-sizes.md)]

## <a name="example-scenarios"></a>範例案例

為了讓您更瞭解其運作方式，以下是一些範例案例：

- 有一個可受益于磁片高載的常見案例，是在 OS 磁片上加快 VM 開機和應用程式啟動的速度。 採用具有8個 GiB OS 映射的 Linux VM 作為範例。 如果我們使用 P2 磁片做為 OS 磁片，則布建的目標為 120 IOPS 和 25 MBps。 當 VM 啟動時，會有作業系統磁片載入開機檔案的讀取尖峰。 隨著負載平衡的推出，您可以讀取 3500 IOPS 和 170 MBps 的最大高載速度，以至少6x 的方式加速載入時間。 在 VM 開機後，OS 磁片上的流量層級通常很低，因為應用程式的大部分資料作業都是針對連接的資料磁片。 如果流量低於布建的目標，您將會累積點數。

- 如果您裝載的是遠端虛擬桌面環境，當作用中的使用者啟動 AutoCAD 之類的應用程式時，對 OS 磁片的讀取流量會大幅增加。 在此情況下，高載流量會耗用累積的信用額度，讓您超越布建的目標，並更快啟動應用程式。

- P1 磁片的布建目標為 120 IOPS 和 25 MBps。 如果磁片上的實際流量為 100 IOPS，而過去1秒間隔為 20 MBps，則未使用的 20 Io 和 5 MB 會貸到磁片的高載 bucket。 當流量超過已布建的目標（最大高載限制）時，可于稍後使用高載值區中的信用額度。 「最大高載限制」會定義磁片流量的上限，即使您有要使用的高載點數也一樣。 在此情況下，即使點數值區中有 10000 IOs，P1 磁片也無法發出超過每秒 3500 IO 的最大高載。  
