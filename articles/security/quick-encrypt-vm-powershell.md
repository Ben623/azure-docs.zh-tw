---
title: 快速入門 - 使用 Azure PowerShell 為 Windows IaaS VM 加密 | Microsoft Docs
description: 在本快速入門中，可了解如何使用 Azure PowerShell 為 Azure 中的 Windows IaaS VM 加密。
services: security
documentationcenter: na
author: msmbaldwin
manager: MBaldwin
ms.assetid: c8abd340-5ed4-42ec-b83f-4d679b61494d
ms.service: security
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/14/2019
ms.author: mbaldwin
ms.custom: seodec18
ms.openlocfilehash: c36f9506665f573a2e7990102af81a9de088e95b
ms.sourcegitcommit: 837dfd2c84a810c75b009d5813ecb67237aaf6b8
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2019
ms.locfileid: "67501636"
---
# <a name="quickstart-encrypt-a-windows-iaas-vm-with-azure-powershell"></a>快速入門：使用 Azure PowerShell 為 Windows IaaS VM 加密

Azure 磁碟加密可協助您為 Windows 和 Linux IaaS 虛擬機器磁碟加密。 此解決方案可與 Azure Key Vault 整合，協助您控制及管理磁碟加密金鑰和祕密。 使用 Azure 磁碟加密時，您將可確保 VM 會透過業界標準的加密技術受到保護。 在本快速入門中，您將建立 Windows Server 2016 VM，並為 OS 磁碟加密。

如果您沒有 Azure 訂用帳戶，請在開始前建立 [免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) 。

## <a name="prerequisites"></a>必要條件

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

- Windows PowerShell ISE
- 安裝或更新至[最新版的 Azure PowerShell 模組](/powershell/azure/install-az-ps)
    - Az 模組版本必須是 1.0.0 或更新版本。 使用 `Get-Module Az -ListAvailable | Select-Object -Property Name,Version,Path` 來檢查版本。
- [Azure 磁碟加密先決條件指令碼](https://raw.githubusercontent.com/Azure/azure-powershell/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts/AzureDiskEncryptionPreRequisiteSetup.ps1)的複本。
    - 如果您已有此指令碼，請下載新複本，因為近期已有所變更。 
    - 使用 **CTRL-A** 選取所有文字，然後使用 **CTRL-C** 將所有文字複製到 [記事本] 中。
    - 將檔案儲存為 **ADEPrereqScript.ps1**


## <a name="sign-in-to-azure"></a>登入 Azure

1. 以滑鼠右鍵按一下 [Windows PowerShell ISE]  ，然後按一下 [以系統管理員身分執行]  。
1. 在**系統管理員：Windows PowerShell ISE** 視窗中，依序按一下 [檢視]  和 [顯示指令碼窗格]  。
1. 在指令碼窗格中，輸入下列 Cmdlet： 

     ```azurepowershell
      Connect-AzAccount
     ```

1. 按一下 [執行指令碼]  的綠色箭號，或使用 F5。 
2. 使用互動式登入，完成連線至 Azure 帳戶的作業。
3. 複製傳回的**訂用帳戶識別碼**，以在執行下一個 PowerShell 指令碼時使用。 

## <a name="bkmk_PrereqScript"></a>執行 Azure 磁碟加密先決條件指令碼
 **ADEPrereqScript.ps1** 將會建立資源群組、金鑰保存庫，並設定金鑰保存庫的存取原則。 此指令碼也會建立金鑰保存庫的資源鎖定，以防止保存庫意外遭到刪除。  

1. 在**系統管理員：Windows PowerShell ISE** 視窗中，按一下 [檔案]  ，然後按一下 [開啟]  。 瀏覽至 **ADEPrereqScript.ps1** 檔案並按兩下。 此指令碼會在指令碼窗格中開啟。
2. 按一下 [執行指令碼]  的綠色箭號，或使用 F5 執行指令碼。 
3. 輸入新的**資源群組**和新**金鑰保存庫**的名稱。 在本快速入門請勿使用現有的資源群組或金鑰保存庫，因為我們稍後將會刪除資源群組。 
4. 輸入要建立資源的位置，例如 **EastUS**。 使用 `Get-AzLocation` 取得位置清單。
5. 在其中複製您的**訂用帳戶識別碼**。 您可以使用 `Get-AzSubscription` 取得您的訂用帳戶識別碼。  
6. 按一下 [執行指令碼]  的綠色箭號。 
7. 複製傳回的 **DiskEncryptionKeyVaultUrl** 和 **DiskEncryptionKeyVaultId**，以供後續使用。

![在 PowerShell ISE 中執行的 Azure 磁碟加密先決條件指令碼](media/azure-security-disk-encryption/ade-prereq-script.PNG)


## <a name="create-a-virtual-machine"></a>建立虛擬機器 
現在，您必須建立虛擬機器，以加密其磁碟。 您將使用的指令碼會建立一個 Windows Server 2016 VM，具有 8 GB 的 RAM 與 30 GB 的 OS 磁碟。 

1. 將指令碼複製到**系統管理員：Windows PowerShell ISE** 指令碼窗格中，並變更最上方的三個變數。 資源群組和位置必須與您先前在[先決條件指令碼](#bkmk_PrereqScript)中使用的相同。  

   ```azurepowershell
    # Variables for common values
    $resourceGroup = "MySecureRG"
    $location = "EastUS"
    $vmName = "MySecureVM"
    
    # Create user object
    $cred = Get-Credential -Message "Enter a username and password for the virtual machine."
    
    # Create a resource group
    New-AzResourceGroup -Name $resourceGroup -Location $location
    
    # Create a subnet configuration
    $subnetConfig = New-AzVirtualNetworkSubnetConfig -Name mySubnet -AddressPrefix 192.168.1.0/24
    
    # Create a virtual network
    $vnet = New-AzVirtualNetwork -ResourceGroupName $resourceGroup -Location $location `
      -Name MYvNET -AddressPrefix 192.168.0.0/16 -Subnet $subnetConfig
    
    # Create a public IP address and specify a DNS name
    $pip = New-AzPublicIpAddress -ResourceGroupName $resourceGroup -Location $location `
      -Name "mypublicdns$(Get-Random)" -AllocationMethod Static -IdleTimeoutInMinutes 4
    
    # Create an inbound network security group rule for port 3389
    $nsgRuleRDP = New-AzNetworkSecurityRuleConfig -Name myNetworkSecurityGroupRuleRDP  -Protocol Tcp `
      -Direction Inbound -Priority 1000 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * `
      -DestinationPortRange 3389 -Access Allow
    
    # Create a network security group
    $nsg = New-AzNetworkSecurityGroup -ResourceGroupName $resourceGroup -Location $location `
      -Name myNetworkSecurityGroup -SecurityRules $nsgRuleRDP
    
    # Create a virtual network card and associate with public IP address and NSG
    $nic = New-AzNetworkInterface -Name myNic -ResourceGroupName $resourceGroup -Location $location `
      -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id -NetworkSecurityGroupId $nsg.Id
    
    # Create a virtual machine configuration
    $vmConfig = New-AzVMConfig -VMName $vmName -VMSize Standard_D2_v3 | `
    Set-AzVMOperatingSystem -Windows -ComputerName $vmName -Credential $cred | `
    Set-AzVMSourceImage -PublisherName MicrosoftWindowsServer -Offer WindowsServer -Skus 2016-Datacenter-smalldisk -Version latest | `
    Add-AzVMNetworkInterface -Id $nic.Id
    
    # Create a virtual machine
    New-AzVM -ResourceGroupName $resourceGroup -Location $location -VM $vmConfig
   ```

2. 按一下 [執行指令碼]  的綠色箭號，以建置 VM。  


## <a name="encrypt-the-disk-of-the-vm"></a>為 VM 的磁碟加密
現在您已建立並設定金鑰保存庫和 VM，接下來您可以使用 **Set-AzVmDiskEncryptionExtension** Cmdlet 為磁碟加密。 
 
1. 執行下列 Cmdlet，為 VM 的磁碟加密：

    ```azurepowershell
     Set-AzVmDiskEncryptionExtension -ResourceGroupName "MySecureRG" -VMName "MySecureVM" `
     -DiskEncryptionKeyVaultId "<Returned by the prerequisites script>" -DiskEncryptionKeyVaultUrl "<Returned by the prerequisites script>"
     ```


1. 加密完成後，您可以使用下列 Cmdlet 確認磁碟是否已加密： 

     ```azurepowershell
     Get-AzVmDiskEncryptionStatus -ResourceGroupName "MySecureRG" -VMName "MySecureVM"
     ```
    ![Get-AzVmDiskEncryptionStatus output](media/azure-security-disk-encryption/ade-get-encryption-status.PNG)
    
## <a name="clean-up-resources"></a>清除資源
 **ADEPrereqScript.ps1** 會建立金鑰保存庫的資源鎖定。 若要清除本快速入門產生的資源，您必須先移除資源鎖定，然後再刪除資源群組。 

1. 從金鑰保存庫移除資源鎖定

     ```azurepowershell
     $LockId =(Get-AzResourceLock -ResourceGroupName "MySecureRG" -ResourceName "MySecureVault" -ResourceType "Microsoft.KeyVault/vaults").LockID 
     Remove-AzResourceLock -LockID $LockId
      ```
    
2. 移除資源群組。 這將會同時刪除群組中的所有資源。 
     ```azurepowershell
      Remove-AzResourceGroup -Name "MySecureRG"
      ```

## <a name="next-steps"></a>後續步驟
繼續閱讀下一篇文章，以深入了解 IaaS VM 的 Azure 磁碟加密先決條件。

> [!div class="nextstepaction"]
> [Azure 磁碟加密的先決條件](azure-security-disk-encryption-prerequisites.md)
