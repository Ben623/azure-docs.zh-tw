---
title: 從通用內部部署 VHD 建立受控 Azure VM | Microsoft Docs
description: 在 Resource Manager 部署模型中，將一般化 VHD 上傳至 Azure 並使用它來建立新 VM。
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 09/25/2018
ms.author: cynthn
ms.openlocfilehash: 9846bf7b28f1205f98eb59671553d309fe754d30
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/09/2019
ms.locfileid: "67707945"
---
# <a name="upload-a-generalized-vhd-and-use-it-to-create-new-vms-in-azure"></a>將一般化 VHD 上傳，並使用它在 Azure 中建立新的 VM

本文會逐步引導您使用 PowerShell 將一般化 VM 的 VHD 上傳至 Azure，從 VHD 建立映像和從該映像建立新的 VM。 您可以上傳從內部部署虛擬化工具或另一個雲端匯出的 VHD。 針對新的 VM 使用[受控磁碟](managed-disks-overview.md)可簡化 VM 管理，當 VM 中包含可用性設定組時，還可提供更高的可用性。 

如需範例指令碼，請參閱[用來將 VHD 上傳至 Azure 並建立新 VM 的範例指令碼](../scripts/virtual-machines-windows-powershell-upload-generalized-script.md)。

## <a name="before-you-begin"></a>開始之前

- 將任何 VHD 上傳至 Azure 之前，您應該遵循[準備 Windows VHD 或 VHDX 以上傳至 Azure](prepare-for-upload-vhd-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)。
- 請先檢閱[規劃移轉至受控磁碟](on-prem-to-azure.md#plan-for-the-migration-to-managed-disks)，再開始移轉至[受控磁碟](managed-disks-overview.md)。

[!INCLUDE [updated-for-az.md](../../../includes/updated-for-az.md)]


## <a name="generalize-the-source-vm-by-using-sysprep"></a>使用 Sysprep 將來源 VM 一般化

Sysprep 會移除您的所有個人帳戶資訊以及其他項目，並準備電腦以做為映像。 如需 Sysprep 的詳細資訊，請參閱 [Sysprep 概觀](https://docs.microsoft.com/windows-hardware/manufacture/desktop/sysprep--system-preparation--overview)。

請確定 Sysprep 支援電腦上執行的伺服器角色。 如需詳細資訊，請參閱 [Sysprep Support for Server Roles (伺服器角色的 Sysprep 支援)](https://msdn.microsoft.com/windows/hardware/commercialize/manufacture/desktop/sysprep-support-for-server-roles)。

> [!IMPORTANT]
> 如果您打算在第一次將 VHD 上傳至 Azure 之前，先執行 Sysprep，請確定您已[準備好 VM](prepare-for-upload-vhd-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)。 
> 
> 

1. 登入 Windows 虛擬機器。
2. 以系統管理員身分開啟 [命令提示字元] 視窗。 將目錄變更到 %windir%\system32\sysprep，然後執行 `sysprep.exe`。
3. 在 [系統準備工具]  對話方塊中，選取 [進入系統全新體驗 (OOBE)]  ，並確認已啟用 [一般化]  核取方塊。
4. 針對 [關機選項]  ，選取 [關機]  。
5. 選取 [確定]  。
   
    ![啟動 Sysprep](./media/upload-generalized-managed/sysprepgeneral.png)
6. Sysprep 完成時，會關閉虛擬機器。 不要重新啟動 VM。


## <a name="get-a-storage-account"></a>取得儲存體帳戶

您需要一個 Azure 中的儲存體帳戶來裝載上傳的 VM 映像。 您可以使用現有的儲存體帳戶或建立新帳戶。 

如果您要使用 VHD 為 VM 建立受控磁碟，儲存體帳戶位置與您將建立 VM 的位置必須相同。

若要顯示可用的儲存體帳戶，請輸入︰

```azurepowershell
Get-AzStorageAccount | Format-Table
```

## <a name="upload-the-vhd-to-your-storage-account"></a>將 VHD 上傳至儲存體帳戶

使用 [Add-AzVhd](https://docs.microsoft.com/powershell/module/az.compute/add-azvhd) Cmdlet，將 VHD 上傳至儲存體帳戶中的容器。 這個範例會將 myVHD.vhd  檔案從 *C:\Users\Public\Documents\Virtual hard disks\\* 上傳至 myResourceGroup  資源群組中名為 mystorageaccount  的儲存體帳戶。 檔案會放入名為 *mycontainer* 的容器，新的檔案名稱會是 *myUploadedVHD.vhd*。

```powershell
$rgName = "myResourceGroup"
$urlOfUploadedImageVhd = "https://mystorageaccount.blob.core.windows.net/mycontainer/myUploadedVHD.vhd"
Add-AzVhd -ResourceGroupName $rgName -Destination $urlOfUploadedImageVhd `
    -LocalFilePath "C:\Users\Public\Documents\Virtual hard disks\myVHD.vhd"
```


如果成功，您會得到看起來如以下的回應：

```powershell
MD5 hash is being calculated for the file C:\Users\Public\Documents\Virtual hard disks\myVHD.vhd.
MD5 hash calculation is completed.
Elapsed time for the operation: 00:03:35
Creating new page blob of size 53687091712...
Elapsed time for upload: 01:12:49

LocalFilePath           DestinationUri
-------------           --------------
C:\Users\Public\Doc...  https://mystorageaccount.blob.core.windows.net/mycontainer/myUploadedVHD.vhd
```

視您的網路連線和 VHD 檔案大小而定，此命令可能需要一些時間才能完成。

### <a name="other-options-for-uploading-a-vhd"></a>上傳 VHD 的其他選項
 
您也可以使用下列其中一種方法將 VHD 上傳至儲存體帳戶：

- [AzCopy](https://aka.ms/downloadazcopy)
- [Azure 儲存體複製 Blob API](https://msdn.microsoft.com/library/azure/dd894037.aspx)
- [Azure 儲存體總管上傳 Blob](https://azurestorageexplorer.codeplex.com/)
- [儲存體匯入/匯出服務 REST API 參考](https://msdn.microsoft.com/library/dn529096.aspx)
-   如果預估的上傳時間長度超過 7 天，我們建議使用匯入/匯出服務。 您可以使用 [DataTransferSpeedCalculator](https://github.com/Azure-Samples/storage-dotnet-import-export-job-management/blob/master/DataTransferSpeedCalculator.html) 從資料大小和傳輸單位來評估時間。 
    匯入/匯出可用來複製到標準儲存體帳戶。 您必須使用 AzCopy 之類的工具，從 Standard 儲存體帳戶複製到進階儲存體帳戶。

> [!IMPORTANT]
> 如果您是使用 AzCopy 來將 VHD 上傳至 Azure，請確定您在執行上傳指令碼之前已設定 [ **/BlobType:page**](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-blobs#upload-a-file)。 如果目的地是 Blob 且未指定此選項，則 AzCopy 預設會建立區塊 Blob。
> 
> 



## <a name="create-a-managed-image-from-the-uploaded-vhd"></a>從上傳的 VHD 建立受控映像 

從一般化 OS VHD 建立受控映像。 使用您自己的資訊取代下列值。


首先，設定部分參數：

```powershell
$location = "East US" 
$imageName = "myImage"
```

使用一般化 OS VHD 建立映像。

```powershell
$imageConfig = New-AzImageConfig `
   -Location $location
$imageConfig = Set-AzImageOsDisk `
   -Image $imageConfig `
   -OsType Windows `
   -OsState Generalized `
   -BlobUri $urlOfUploadedImageVhd `
   -DiskSizeGB 20
New-AzImage `
   -ImageName $imageName `
   -ResourceGroupName $rgName `
   -Image $imageConfig
```


## <a name="create-the-vm"></a>建立 VM

現在您已有映像，您可以從映像建立一個或多個新的 VM。 該範例會根據 *myResourceGroup* 中的 *myImage* 建立名為 *myVM* 的 VM。


```powershell
New-AzVm `
    -ResourceGroupName $rgName `
    -Name "myVM" `
    -ImageName $imageName `
    -Location $location `
    -VirtualNetworkName "myVnet" `
    -SubnetName "mySubnet" `
    -SecurityGroupName "myNSG" `
    -PublicIpAddressName "myPIP" `
    -OpenPorts 3389
```


## <a name="next-steps"></a>後續步驟

登入至新的虛擬機器。 如需詳細資訊，請參閱 [如何連接和登入執行 Windows 的 Azure 虛擬機器](connect-logon.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)。 

