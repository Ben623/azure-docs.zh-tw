---
title: Azure Windows VM 大小-GPU
description: 列出 Azure 中可用的不同 Windows 虛擬機器 GPU 最佳化大小。 列出 vCPU 數目、資料磁碟和 NIC 的相關資訊，以及此服務中各種大小之儲存體輸送量和網路頻寬的相關資訊。
services: virtual-machines-windows
documentationcenter: ''
author: jonbeck7
manager: gwallace
editor: ''
tags: azure-resource-manager,azure-service-management
ms.assetid: ''
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 06/11/2019
ms.author: jonbeck
ms.openlocfilehash: 5b2da3076f3970d2f11301b3ea412ce52c257111
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/14/2019
ms.locfileid: "74075359"
---
# <a name="gpu-optimized-virtual-machine-sizes"></a>GPU 最佳化的虛擬機器大小

[!INCLUDE [virtual-machines-common-sizes-gpu](../../../includes/virtual-machines-common-sizes-gpu.md)]

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../../includes/virtual-machines-common-sizes-table-defs.md)]

## <a name="supported-operating-systems-and-drivers"></a>支援的作業系統和驅動程式

若要利用 Azure N 系列 VM (執行 Windows) 的 GPU 功能，您必須安裝 NVIDIA GPU 驅動程式。 [NVIDIA GPU 驅動程式擴充功能](../extensions/hpccompute-gpu-windows.md)會在 N 系列 VM 上安裝適當的 NVIDIA CUDA 或 GRID 驅動程式。 使用 Azure 入口網站或者 Azure PowerShell 或 Azure Resource Manager 範本之類的工具，安裝或管理擴充功能。 如需支援的作業系統和部署步驟，請參閱 [NVIDIA GPU 驅動程式擴充功能文件](../extensions/hpccompute-gpu-windows.md)。 如需有關虛擬機器擴充功能的一般資訊，請參閱 [Azure 虛擬機器擴充功能和功能](../extensions/overview.md)。

如果您選擇手動安裝 NVIDIA GPU 驅動程式，請參閱[適用於 Windows 的 N 系列 GPU 驅動程式設定](n-series-driver-setup.md)，以了解支援的作業系統、驅動程式，以及安裝和驗證步驟。

[!INCLUDE [virtual-machines-n-series-considerations](../../../includes/virtual-machines-n-series-considerations.md)]

## <a name="other-sizes"></a>其他大小
- [一般用途](sizes-general.md)
- [計算最佳化](sizes-compute.md)
- [高效能計算](sizes-hpc.md)
- [記憶體最佳化](sizes-memory.md)
- [儲存體最佳化](sizes-storage.md)
- [前幾代](sizes-previous-gen.md)

## <a name="next-steps"></a>後續步驟
深入了解 [Azure 計算單位 (ACU)](acu.md) 如何協助您比較各個 Azure SKU 的計算效能。

