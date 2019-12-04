---
title: 第2代 Vm 的 Azure 支援
description: 第2代 Vm 的 Azure 支援總覽
services: virtual-machines-linux
documentationcenter: ''
author: laurenhughes
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.topic: article
ms.date: 12/03/2019
ms.author: lahugh
ms.openlocfilehash: cfa8d28a41bb5551277bca29c118698ecaa8d112
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/03/2019
ms.locfileid: "74791728"
---
# <a name="support-for-generation-2-vms-on-azure"></a>Azure 上第2代 Vm 的支援

第2代虛擬機器（Vm）的支援現已在 Azure 上提供。 建立虛擬機器之後，您就無法變更其層代，因此請先參閱此頁面上的考慮，再選擇世代。

第2代 Vm 支援第1代 Vm 中不支援的重要功能。 這些功能包括增加的記憶體、Intel 軟體防護延伸模組（Intel SGX）和虛擬化的持續性記憶體（vPMEM）。 第2代 Vm 執行于內部部署，但在 Azure 中尚不支援某些功能。 如需詳細資訊，請參閱[特性和功能](#features-and-capabilities)一節。

第2代 Vm 使用新的 UEFI 型開機架構，而不是第1代 Vm 所使用的 BIOS 架構。 相較于第1代 Vm，第2代 Vm 可能已改善開機和安裝時間。 如需第2代 Vm 的總覽，以及第1代與第2代的差異，請參閱[我應該在 hyper-v 中建立第1代或第2代虛擬機器嗎？](https://docs.microsoft.com/windows-server/virtualization/hyper-v/plan/should-i-create-a-generation-1-or-2-virtual-machine-in-hyper-v)。

## <a name="generation-2-vm-sizes"></a>第2代 VM 大小

Azure 中的所有 VM 大小都支援第1代 Vm。 Azure 現在為下列選取的 VM 系列提供第2代支援：

* [B 系列](https://docs.microsoft.com/azure/virtual-machines/linux/b-series-burstable)
* [DC 系列](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-general#dc-series)
* [Dsv2 系列](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-general#dsv2-series)和[Dsv3 系列](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-general#dsv3-series-1)
* [Esv3 系列](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-memory#esv3-series)
* [Fsv2 系列](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-compute#fsv2-series-1)
* [GS 系列](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-previous-gen#gs-series)
* [HB 系列](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-hpc#hb-series)
* [HC 系列](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-hpc#hc-series)
* [Ls 系列](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-previous-gen#ls-series)和[Lsv2 系列](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-storage#lsv2-series)
* [Mv2 系列](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-memory#mv2-series)
* [NCv2 系列](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-gpu#ncv2-series)和[NCv3 系列](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-gpu#ncv3-series)
* [ND 系列](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-gpu#nd-series)
* [NVv3 系列](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-gpu#nvv3-series--1)

> [!NOTE]
> 適用于 Mv2 系列 Vm 的第2代 VM 映射使用已正式運作，因為 Mv2 系列僅適用于第2代 VM 映射。 Mv2 系列 Vm 上不支援第1代 VM 映射。 

## <a name="generation-2-vm-images-in-azure-marketplace"></a>Azure Marketplace 中的第2代 VM 映射

第2代 Vm 支援下列 Marketplace 映射：

* Windows Server 2019、2016、2012 R2、2012
* Windows 10
* SUSE Linux Enterprise Server 15 SP1
* SUSE Linux Enterprise Server 12 SP4
* Ubuntu Server 16.04、18.04、19.04、19.10 
* RHEL 8。0

## <a name="on-premises-vs-azure-generation-2-vms"></a>內部部署與 Azure 第2代 Vm

Azure 目前不支援內部部署 Hyper-v 針對第2代 Vm 所支援的部分功能。

| 第2代功能                | 內部部署 Hyper-v | Azure |
|-------------------------------------|---------------------|-------|
| 安全開機                         | :heavy_check_mark:  | :x:   |
| 受防護的 VM                         | :heavy_check_mark:  | :x:   |
| vTPM                                | :heavy_check_mark:  | :x:   |
| 以虛擬化為基礎的安全性（VBS） | :heavy_check_mark:  | :x:   |
| VHDX 格式                         | :heavy_check_mark:  | :x:   |

## <a name="features-and-capabilities"></a>功能

### <a name="generation-1-vs-generation-2-features"></a>層代1與第2代功能

| 功能 | 第 1 代 | 第2代 |
|---------|--------------|--------------|
| 開機             | PCAT         | UEFI |
| 磁碟控制卡 | IDE          | SCSI |
| VM 大小         | 所有 VM 大小 | 只有支援 premium 儲存體的 Vm |

### <a name="generation-1-vs-generation-2-capabilities"></a>層代1和層代2功能

| 功能 | 第 1 代 | 第2代 |
|------------|--------------|--------------|
| OS 磁片 > 2 TB                    | :x:                | :heavy_check_mark: |
| 自訂磁片/映射/交換 OS         | :heavy_check_mark: | :heavy_check_mark: |
| 虛擬機器擴展集支援 | :heavy_check_mark: | :heavy_check_mark: |
| Azure Site Recovery               | :heavy_check_mark: | :heavy_check_mark: |
| 備份/還原                    | :heavy_check_mark: | :heavy_check_mark: |
| 共用映射資源庫              | :heavy_check_mark: | :heavy_check_mark: |
| Azure 磁碟加密             | :heavy_check_mark: | :x:                |

## <a name="creating-a-generation-2-vm"></a>建立第2代 VM

### <a name="marketplace-image"></a>Marketplace 映射

在 [Azure 入口網站] 或 [Azure CLI] 中，您可以從支援 UEFI 開機的 Marketplace 映射建立第2代 Vm。

#### <a name="azure-portal"></a>Azure Portal

Windows 和 SLES 的第2代映射包含在與 Gen1 映射相同的伺服器供應專案中。 從流程的觀點來看，這表示您會從 VM 的入口網站中選取供應專案和 SKU。 如果 SKU 同時支援第1代和第2代映射，您可以從 VM 建立流程的 [ *Advanced* ] 索引標籤中選擇建立第2代 VM。

目前，下列 Sku 支援第1代和第2代映射：

* Windows Server 2012
* Windows Server 2012 R2
* Windows Server 2016
* Windows Server 2019

當您選取 Windows Server SKU 做為供應專案時，您可以在 [ **Advanced** ] 索引標籤中，選擇建立**gen 1** （BIOS）或**GEN 2** （UEFI） VM。 如果您選取 [ **Gen 2**]，請確定[第2代 Vm 支援](#generation-2-vm-sizes)[**基本**] 索引標籤中所選取的 VM 大小。

![選取第1代或第2代 VM](./media/generation-2/gen1-gen2-select.png)

#### <a name="powershell"></a>PowerShell

您也可以使用 PowerShell 直接參考第1代或第2代 SKU 來建立 VM。

例如，使用下列 PowerShell Cmdlet 來取得 `WindowsServer` 供應專案中的 Sku 清單。

```powershell
Get-AzVMImageSku -Location westus2 -PublisherName MicrosoftWindowsServer -Offer WindowsServer
```

如果您使用 Windows Server 2012 做為作業系統來建立 VM，則會選取第1代（BIOS）或第2代（UEFI） VM SKU，如下所示：

```powershell
2012-Datacenter
2012-datacenter-gensecond
```

如需目前支援的 Marketplace 映射清單，請參閱[特性和功能](#features-and-capabilities)一節。

### <a name="managed-image-or-managed-disk"></a>受控映射或受控磁片

您可以使用與建立第1代 VM 相同的方式，從受控映射或受控磁片建立第2代 VM。

### <a name="virtual-machine-scale-sets"></a>虛擬機器擴展集

您也可以使用虛擬機器擴展集來建立第2代 Vm。 在 Azure CLI 中，使用 Azure 擴展集來建立第2代 Vm。

## <a name="frequently-asked-questions"></a>常見問題集

* **第2代 Vm 可以在所有 Azure 區域中使用嗎？**  
    可以。 但並非所有層[代 2 VM 大小](#generation-2-vm-sizes)都可以在每個區域中使用。 第2代 VM 的可用性取決於 VM 大小的可用性。

* **第1代和第2代 Vm 之間是否有價格差異？**  
    不會。

* **我有來自內部部署第2代 VM 的 .vhd 檔案。我可以在 Azure 中使用該 .vhd 檔案來建立第2代 VM 嗎？**
  是，您可以將第2代 .vhd 檔案帶入 Azure，並使用它來建立第2代 VM。 請使用下列步驟來執行這項操作：
    1. 將 .vhd 上傳至您要在其中建立 VM 的相同區域中的儲存體帳戶。
    1. 從 .vhd 檔案建立受控磁片。 將 [Hyper-v 產生] 屬性設為 V2。 下列 PowerShell 命令會在建立受控磁片時設定 Hyper-v 產生屬性。

        ```powershell
        $sourceUri = 'https://xyzstorage.blob.core.windows.net/vhd/abcd.vhd'. #<Provide location to your uploaded .vhd file>
        $osDiskName = 'gen2Diskfrmgenvhd'  #<Provide a name for your disk>
        $diskconfig = New-AzDiskConfig -Location '<location>' -DiskSizeGB 127 -AccountType Standard_LRS -OsType Windows -HyperVGeneration "V2" -SourceUri $sourceUri -CreateOption 'Import'
        New-AzDisk -DiskName $osDiskName -ResourceGroupName '<Your Resource Group>' -Disk $diskconfig
        ```

    1. 當磁片可供使用之後，請連接此磁片來建立 VM。 建立的 VM 將會是第2代 VM。
    建立第2代 VM 時，您可以選擇性地將此 VM 的映射一般化。 藉由將映射一般化，您就可以用它來建立多個 Vm。

* **如何? 增加 OS 磁片大小嗎？**  
  大於 2 TB 的 OS 磁片是第2代 Vm 的新手。 根據預設，第2代 Vm 的 OS 磁片小於 2 TB。 您可以將磁片大小增加到最多 4 TB 的建議。 使用 Azure CLI 或 Azure 入口網站來增加 OS 磁片大小。 如需如何以程式設計方式擴充磁片的相關資訊，請參閱[調整磁片大小](expand-disks.md)。

  若要增加 Azure 入口網站的 OS 磁片大小：

  1. 在 Azure 入口網站中，移至 [VM 屬性] 頁面。
  1. 若要關閉和解除配置 VM，請選取 [**停止**] 按鈕。
  1. 在 [**磁片**] 區段中，選取您想要增加的 OS 磁片。
  1. 在 [**磁片**] 區段中 **，選取 [** 設定]，並將**大小**更新為您想要的值。
  1. 回到 VM 的 [屬性] 頁面並**啟動**vm。

  對於大於 2 TB 的 OS 磁片，您可能會看到警告。 此警告不適用於第2代 Vm。 不過，*不建議*大於 4 TB 的 OS 磁片大小。

* **第2代 Vm 是否支援加速網路？**  
    可以。 如需詳細資訊，請參閱[建立具有加速網路的 VM](../../virtual-network/create-vm-accelerated-networking-cli.md)。

* **第2代支援 VHDX 嗎？**  
    否，第2代 Vm 僅支援 VHD。

* **第2代 Vm 是否支援 Azure Ultra 磁碟儲存體？**  
    可以。

* **我可以將 VM 從第1代遷移到第2代嗎？**  
    否，您無法在建立 VM 之後變更其產生。 如果您需要在 VM 層代之間切換，請建立不同世代的新 VM。

## <a name="next-steps"></a>後續步驟

* 瞭解[hyper-v 中的第2代虛擬機器](https://docs.microsoft.com/windows-server/virtualization/hyper-v/plan/should-i-create-a-generation-1-or-2-virtual-machine-in-hyper-v)。
