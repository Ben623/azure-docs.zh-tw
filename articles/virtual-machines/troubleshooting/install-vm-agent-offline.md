---
title: 在離線模式安裝 Azure VM 代理程式 | Microsoft Docs
description: 了解如何在離線模式安裝 Azure VM 代理程式。
services: virtual-machines-windows
documentationcenter: ''
author: genlin
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 10/31/2018
ms.author: genli
ms.openlocfilehash: 8ea85b560f35c79b3d5066d794f587345810b5d0
ms.sourcegitcommit: 3c925b84b5144f3be0a9cd3256d0886df9fa9dc0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/28/2020
ms.locfileid: "77920853"
---
# <a name="install-the-azure-virtual-machine-agent-in-offline-mode"></a>在離線模式安裝 Azure 虛擬機器代理程式 

Azure 虛擬機器代理程式 (VM 代理程式) 提供有用的功能，例如本機系統管理員密碼重設與指令碼推送。 本文說明如何為離線的 Windows 虛擬機器 (VM) 安裝 VM 代理程式。 

## <a name="when-to-use-the-vm-agent-in-offline-mode"></a>何時應以離線模式使用 VM 代理程式

在下列情況下，請以離線模式安裝 VM 代理程式：

- 已部署的 Azure VM 未安裝 VM 代理程式或代理程式無法運作。
- 您忘記 VM 的系統管理員密碼，或您無法存取 VM。

## <a name="how-to-install-the-vm-agent-in-offline-mode"></a>以離線模式安裝 VM 代理程式

請使用下列步驟，以離線模式安裝 VM 代理程式。

### <a name="step-1-attach-the-os-disk-of-the-vm-to-another-vm-as-a-data-disk"></a>步驟 1：將 VM 的 OS 磁碟附加至另一個 VM 當做資料磁碟

1. 取得受影響 VM 的 OS 磁片快照集、從快照集建立磁片，然後將磁片連結至疑難排解 VM。 如需詳細資訊，請參閱[使用 Azure 入口網站將 OS 磁片連接至復原 VM，以針對 WINDOWS VM 進行疑難排解](troubleshoot-recovery-disks-portal-windows.md)。 若為傳統 VM，請刪除 VM 並保留 OS 磁片，然後將 OS 磁片連結至疑難排解 VM。

2.  連接至疑難排解 VM。 開啟 [電腦管理] > [磁碟管理]。 確認 OS 磁碟在線上，且已指派磁碟分割的磁碟機代號。

### <a name="step-2-modify-the-os-disk-to-install-the-azure-vm-agent"></a>步驟 2：修改 OS 磁碟以安裝 Azure VM 代理程式

1.  建立與虛擬機器 VM 的遠端桌面連線。

2.  在疑難排解員 VM 中，流覽至您連接的 OS 磁片，開啟 [\windows\system32\config] 資料夾。 複製此資料夾中的所有檔案作為備份，以在需要復原時使用。

3.  啟動 [登錄編輯器] (regedit.exe)。

4.  選取 [HKEY_LOCAL_MACHINE] 機碼。 在功能表上，選取 [檔案] >  **[載入 Hive]** ：

    ![載入 Hive](./media/install-vm-agent-offline/load-hive.png)

5.  在您連接的 OS 磁碟上，瀏覽至 \windows\system32\config\SYSTEM 資料夾。 針對 Hive 的名稱，輸入 **BROKENSYSTEM**。 新的 Hive 會顯示在 [HKEY_LOCAL_MACHINE] 機碼下方。

6.  在您連接的 OS 磁碟上，瀏覽至 \windows\system32\config\SOFTWARE 資料夾。 針對 Hive 軟體的名稱，輸入 **BROKENSOFTWARE**。

7. 如果連結的作業系統磁碟已安裝 VM 代理程式，請執行目前組態的備份。 如果並未安裝 VM 代理程式，請跳至下一個步驟。
      
    1. 將 \windowsazure 資料夾重新命名為 \windowsazure.old。

    2. 匯出下列登錄：
        - HKEY_LOCAL_MACHINE\BROKENSYSTEM\ControlSet001\Services\WindowsAzureGuestAgent
        - HKEY_LOCAL_MACHINE\BROKENSYSTEM\\ControlSet001\Services\WindowsAzureTelemetryService
        - HKEY_LOCAL_MACHINE\BROKENSYSTEM\ControlSet001\Services\RdAgent

8.  使用疑難排解 VM 上現有的檔案作為 VM 代理程式安裝的儲存機制。 完成下列步驟：

    1. 從疑難排解 VM 中，以登錄格式 (.reg) 匯出下列子機碼： 
        - HKEY_LOCAL_MACHINE  \SYSTEM\ControlSet001\Services\WindowsAzureGuestAgent
        - HKEY_LOCAL_MACHINE  \SYSTEM\ControlSet001\Services\WindowsAzureTelemetryService
        - HKEY_LOCAL_MACHINE  \SYSTEM\ControlSet001\Services\RdAgent

          ![匯出登錄子機碼](./media/install-vm-agent-offline/backup-reg.png)

    2. 編輯登錄檔。 在每個檔案中，將項目值 **SYSTEM** 變更為 **BROKENSYSTEM** (如下列影像所示)，然後儲存檔案。 請記住目前 VM 代理程式的 **ImagePath**。 我們後續必須將對應的資料夾複製到連結的作業系統磁碟。 

        ![變更登錄子機碼值](./media/install-vm-agent-offline/change-reg.png)

    3. 按兩下每個登錄檔，將登錄檔匯入到儲存機制。

    4. 確認下列三個子機碼已成功匯入到 **BROKENSYSTEM** Hive：
        - WindowsAzureGuestAgent
        - WindowsAzureTelemetryService
        - RdAgent

    5. 將目前 VM 代理程式的安裝資料夾複製到連結的作業系統磁碟： 

        1.  在您所連結的作業系統磁碟上，在根路徑中建立名為 WindowsAzure 的資料夾。

        2.  移至疑難排解 VM 的 C:\WindowsAzure，並尋找任何名為 C:\WindowsAzure\GuestAgent_X.X.XXXX.XXX 的資料夾。 將具有最新版本號碼的 GuestAgent 資料夾從 C:\WindowsAzure 複製到已連結的作業系統磁碟中的 WindowsAzure 資料夾。 如果您不確定應複製哪個資料夾，請複製所有 GuestAgent 資料夾。 下圖顯示複製到已連結作業系統磁碟的 GuestAgent 資料夾範例。

             ![複製 GuestAgent 資料夾](./media/install-vm-agent-offline/copy-files.png)

9.  選取 [BROKENSYSTEM]。 在功能表上，選取 [檔案] >  **[上傳 Hive]** 。

10.  選取 [BROKENSOFTWARE]。 在功能表上，選取 [檔案] >  **[上傳 Hive]** 。

11.  卸離 OS 磁片，然後[變更受影響 VM 的 os 磁片](troubleshoot-recovery-disks-portal-windows.md#swap-the-os-disk-for-the-vm)。 若為傳統 VM，請使用已修復的 OS 磁片來建立新的 VM。

12.  存取 VM。 請注意 RdAgent 正在執行，並且正在產生記錄。

如果您已使用資源管理員部署模型建立 VM，則作業已完成。

### <a name="use-the-provisionguestagent-property-for-classic-vms"></a>針對傳統 VM 使用 ProvisionGuestAgent 屬性

[!INCLUDE [classic-vm-deprecation](../../../includes/classic-vm-deprecation.md)]

如果您使用傳統模型建立 VM，請使用 Azure PowerShell 模組來更新 **ProvisionGuestAgent** 屬性。 屬性會通知 Azure VM 已安裝 VM 代理程式。

若要設定 **ProvisionGuestAgent** 屬性，在 Azure PowerShell 中執行下列命令：

   ```powershell
   $vm = Get-AzureVM –ServiceName <cloud service name> –Name <VM name>
   $vm.VM.ProvisionGuestAgent = $true
   Update-AzureVM –Name <VM name> –VM $vm.VM –ServiceName <cloud service name>
   ```

然後執行 `Get-AzureVM` 命令。 請注意，**GuestAgentStatus** 屬性現已填入資料：

   ```powershell
   Get-AzureVM –ServiceName <cloud service name> –Name <VM name>
   GuestAgentStatus:Microsoft.WindowsAzure.Commands.ServiceManagement.Model.PersistentVMModel.GuestAgentStatus
   ```

## <a name="next-steps"></a>後續步驟

- [Azure 虛擬機器代理程式概觀](../extensions/agent-windows.md)
- [適用於 Windows 的虛擬機器擴充功能和功能](../extensions/features-windows.md)
