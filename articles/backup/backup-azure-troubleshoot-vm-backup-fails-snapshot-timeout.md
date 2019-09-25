---
title: 針對 Azure 備份失敗進行疑難排解：無法使用客體代理程式狀態
description: 與代理程式、延伸模組及磁碟相關之 Azure 備份失敗的徵狀、原因和解決方案。
ms.reviewer: saurse
author: dcurwin
manager: carmonm
keywords: Azure 備份; VM 代理程式; 網路連線;
ms.service: backup
ms.topic: troubleshooting
ms.date: 07/05/2019
ms.author: dacurwin
ms.openlocfilehash: ab03056557c7c67c5b75d701c9995c9ad500caae
ms.sourcegitcommit: 263a69b70949099457620037c988dc590d7c7854
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/25/2019
ms.locfileid: "71268766"
---
# <a name="troubleshoot-azure-backup-failure-issues-with-the-agent-or-extension"></a>針對 Azure 備份失敗進行疑難排解：與代理程式或擴充功能相關的問題

本文提供疑難排解步驟，協助您解決與 VM 代理程式和延伸模組通訊相關的 Azure 備份錯誤。

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="UserErrorGuestAgentStatusUnavailable-vm-agent-unable-to-communicate-with-azure-backup"></a>UserErrorGuestAgentStatusUnavailable - VM 代理程式無法與 Azure 備份通訊

**錯誤碼**：UserErrorGuestAgentStatusUnavailable <br>
**錯誤訊息**：VM 代理程式無法與 Azure 備份通訊<br>

Azure VM 代理程式可能已停止、過期、處於不一致的狀態，或未安裝，並導致 Azure 備份服務觸發快照集。  

- 如果 VM 代理程式已停止或處於不一致的狀態，請**重新開機代理程式**，然後重試備份作業（嘗試臨機操作備份）。 如需重新開機代理程式的步驟，請參閱[Windows vm](https://docs.microsoft.com/azure/backup/backup-azure-troubleshoot-vm-backup-fails-snapshot-timeout#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms)或[Linux vm](https://docs.microsoft.com/azure/virtual-machines/linux/update-agent)。
- 如果 VM 代理程式未安裝或已過時，請安裝/更新 VM 代理程式，然後重試備份操作。 如需安裝/更新代理程式的步驟，請參閱[Windows vm](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-windows)或[Linux vm](https://docs.microsoft.com/azure/virtual-machines/linux/update-agent)。  

## <a name="guestagentsnapshottaskstatuserror---could-not-communicate-with-the-vm-agent-for-snapshot-status"></a>GuestAgentSnapshotTaskStatusError - 無法與 VM 代理程式通訊來取得快照集狀態

**錯誤碼**：GuestAgentSnapshotTaskStatusError<br>
**錯誤訊息**：無法與 VM 代理程式通訊來取得快照集狀態 <br>

在註冊及排程 Azure 備份服務的 VM 之後，備份就會藉由與 VM 備份擴充功能通訊以取得時間點快照，來起始作業。 下列任一種狀況都可能會阻止觸發快照集。 如果未觸發快照集，可能會發生備份失敗。 請依照列出的順序完成下列疑難排解步驟，然後重試作業：  

**原因 1：[代理程式已安裝在 VM 中，但沒有回應 (適用於 Windows VM)](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms)**  

**原因 2：[VM 中安裝的代理程式已過時 (適用於 Linux VM)](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)**

**原因 3：[無法擷取快照集狀態或無法取得快照集](#the-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-taken)**

**原因 4︰[備份擴充功能無法更新或載入](#the-backup-extension-fails-to-update-or-load)**

## <a name="usererrorrpcollectionlimitreached---the-restore-point-collection-max-limit-has-reached"></a>UserErrorRpCollectionLimitReached - 已達到還原點集合上限

**錯誤碼**：UserErrorRpCollectionLimitReached <br>
**錯誤訊息**：已達到還原點集合上限。 <br>

- 如果復原點資源群組上有鎖定導致無法自動清除復原點，就會發生此問題。
- 如果每日觸發多個備份，也會發生此問題。 目前建議每天只進行一次備份，因為在每個設定的快照集保留期間，立即還原點會保留1-5 天，而且在任何指定時間內，只有18個立即 Rp 可以與 VM 相關聯。 <br>

建議的動作：<br>
若要解決此問題，請移除 VM 資源群組的鎖定，並重試此作業以觸發清除動作。
> [!NOTE]
> 備份服務會建立與 VM 資源群組不同的資源群組，來儲存還原點集合。 建議客戶請勿鎖定建立給備份服務使用的資源群組。 備份服務建立的資源群組命名格式為：AzureBackupRG_`<Geo>`_`<number>` 例如：AzureBackupRG_northeurope_1

**步驟 1：[從還原點資源群組中移除鎖定](#remove_lock_from_the_recovery_point_resource_group)** <br>
**步驟 2：[清除還原點集合](#clean_up_restore_point_collection)**<br>

## <a name="usererrorkeyvaultpermissionsnotconfigured---backup-doesnt-have-sufficient-permissions-to-the-key-vault-for-backup-of-encrypted-vms"></a>UserErrorKeyvaultPermissionsNotConfigured - 備份沒有足夠的金鑰保存庫權限來備份加密的 VM

**錯誤碼**：UserErrorKeyvaultPermissionsNotConfigured <br>
**錯誤訊息**：備份沒有足夠的金鑰保存庫權限可以進行加密 VM 的備份。 <br>

若要在加密的 Vm 上成功執行備份作業，它必須具有存取金鑰保存庫的許可權。 這可以使用[Azure 入口網站](https://docs.microsoft.com/azure/backup/backup-azure-vms-encryption)或透過[PowerShell](https://docs.microsoft.com/azure/backup/backup-azure-vms-automation#enable-protection)來完成。

## <a name="ExtensionSnapshotFailedNoNetwork-snapshot-operation-failed-due-to-no-network-connectivity-on-the-virtual-machine"></a>ExtensionSnapshotFailedNoNetwork - 因為虛擬機器沒有網路連線，所以快照集作業失敗

**錯誤碼**：ExtensionSnapshotFailedNoNetwork<br>
**錯誤訊息**：因為虛擬機器沒有網路連線，所以快照集作業失敗<br>

在註冊及排程 Azure 備份服務的 VM 之後，備份就會藉由與 VM 備份擴充功能通訊以取得時間點快照，來起始作業。 下列任一種狀況都可能會阻止觸發快照集。 如果未觸發快照集，可能會發生備份失敗。 請依照列出的順序完成下列疑難排解步驟，然後重試作業：

**原因 1：[無法擷取快照集狀態或無法取得快照集](#the-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-taken)**  
**原因 2：[備份擴充功能無法更新或載入](#the-backup-extension-fails-to-update-or-load)**  
**原因 3：[VM 沒有網際網路存取](#the-vm-has-no-internet-access)**

## <a name="ExtensionOperationFailed-vmsnapshot-extension-operation-failed"></a>ExtensionOperationFailedForManagedDisks-VMSnapshot 延伸模組作業失敗

**錯誤碼**：ExtensionOperationFailedForManagedDisks <br>
**錯誤訊息**：VMSnapshot 擴充作業失敗<br>

在註冊及排程 Azure 備份服務的 VM 之後，備份就會藉由與 VM 備份擴充功能通訊以取得時間點快照，來起始作業。 下列任一種狀況都可能會阻止觸發快照集。 如果未觸發快照集，可能會發生備份失敗。 請依照列出的順序完成下列疑難排解步驟，然後重試作業：  
**原因 1：[無法擷取快照集狀態或無法取得快照集](#the-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-taken)**  
**原因 2：[備份擴充功能無法更新或載入](#the-backup-extension-fails-to-update-or-load)**  
**原因 3：[代理程式已安裝在 VM 中，但沒有回應 (適用於 Windows VM)](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms)**  
**原因 4︰[VM 中安裝的代理程式已過時 (適用於 Linux VM)](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)**

## <a name="backupoperationfailed--backupoperationfailedv2---backup-fails-with-an-internal-error"></a>BackUpOperationFailed / BackUpOperationFailedV2 - 備份失敗，發生內部錯誤

**錯誤碼**：BackUpOperationFailed / BackUpOperationFailedV2 <br>
**錯誤訊息**：備份因為內部錯誤而失敗 - 請在幾分鐘內重試此作業 <br>

在註冊及排程 Azure 備份服務的 VM 之後，備份就會藉由與 VM 備份擴充功能通訊以取得時間點快照，來起始作業。 下列任一種狀況都可能會阻止觸發快照集。 如果未觸發快照集，可能會發生備份失敗。 請依照列出的順序完成下列疑難排解步驟，然後重試作業：  
**原因 1：[代理程式已安裝到 VM 中，但沒有回應 (適用於 Windows VM)](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms)**  
**原因 2：[VM 中安裝的代理程式已過時 (適用於 Linux VM)](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)**  
**原因 3：[無法擷取快照集狀態或無法取得快照集](#the-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-taken)**  
**原因 4︰[備份擴充功能無法更新或載入](#the-backup-extension-fails-to-update-or-load)**  
**原因 5：備份服務因資源群組鎖定而沒有刪除舊還原點的權限** <br>
**原因 6：[VM 沒有網際網路存取](#the-vm-has-no-internet-access)**

## <a name="usererrorunsupporteddisksize---the-configured-disk-sizes-is-currently-not-supported-by-azure-backup"></a>UserErrorUnsupportedDiskSize-Azure 備份目前不支援設定的磁片大小。

**錯誤碼**：UserErrorUnsupportedDiskSize <br>
**錯誤訊息**：Azure 備份目前不支援已設定的磁片大小。 <br>

當備份磁片大小大於 30 TB 的 VM 時，您的備份操作可能會失敗。 此外，目前不支援備份大小大於 4 TB 的加密磁片。 藉由分割磁片，確定磁片大小小於或等於支援的限制值。

## <a name="usererrorbackupoperationinprogress---unable-to-initiate-backup-as-another-backup-operation-is-currently-in-progress"></a>UserErrorBackupOperationInProgress - 無法起始備份，因為另一個備份作業正在進行中

**錯誤碼**：UserErrorBackupOperationInProgress <br>
**錯誤訊息**：無法起始備份，因為另一個備份作業正在進行中<br>

您最近的備份作業失敗，因為現有備份作業正在進行中。 必須等到目前的作業完成，才能啟動新的備份作業。 請確定目前正在進行的備份作業已完成，再觸發或排定其他備份作業。 若要檢查備份作業狀態，請執行下列步驟：

1. 登入 Azure 入口網站，按一下 [**所有服務**]。 輸入「復原服務」，然後按一下 [復原服務保存庫]。 復原服務保存庫清單隨即出現。
2. 在復原服務保存庫清單中，選取其中已設定備份的保存庫。
3. 在 [保存庫儀表板] 功能表中，按一下 [備份作業] 以顯示所有備份作業。
   - 如果有正在進行中的備份作業，請等到該作業完成或取消備份作業。
     - 若要取消備份作業，請以滑鼠右鍵按一下備份作業，然後按一下 [**取消**] 或使用[PowerShell](https://docs.microsoft.com/powershell/module/az.recoveryservices/stop-azrecoveryservicesbackupjob?view=azps-1.4.0)。
   - 如果您已在不同的保存庫中重新設定備份，請確定舊的保存庫中沒有正在執行的備份作業。 如果存在，則取消備份作業。
     - 若要取消備份作業，請以滑鼠右鍵按一下備份作業，然後按一下 [取消]，或使用 [PowerShell](https://docs.microsoft.com/powershell/module/az.recoveryservices/stop-azrecoveryservicesbackupjob?view=azps-1.4.0)
4. 請重試備份作業。

如果排定的備份作業花費較長的時間，與下一個備份設定發生衝突，則請參閱[最佳作法](backup-azure-vms-introduction.md#best-practices)、[備份效能](backup-azure-vms-introduction.md#backup-performance)和[還原考慮](backup-azure-vms-introduction.md#backup-and-restore-considerations)。

## <a name="causes-and-solutions"></a>原因和解決方案

### <a name="the-vm-has-no-internet-access"></a>VM 沒有網際網路存取

根據部署需求，VM 無法存取網際網路。 或者，它可能會有防止存取 Azure 基礎結構的限制。

備份延伸模組需要連線到 Azure 公用 IP 位址，才能正確運作。 延伸模組會將命令傳送至 Azure 儲存體端點 (HTTPS URL) 來管理 VM 的快照集。 如果延伸模組無法存取公用網際網路，則備份最終會失敗。

#### <a name="solution"></a>方案

若要解決網路問題，請參閱[建立網路連線](backup-azure-arm-vms-prepare.md#establish-network-connectivity)。

### <a name="the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms"></a>代理程式已安裝在 VM 中，但沒有回應 (適用於 Windows VM)

#### <a name="solution"></a>方案

VM 代理程式可能已損毀，或服務可能已停止。 重新安裝 VM 代理程式有助於取得最新版本。 也有助於重新開始與服務通訊。

1. 判斷 Windows Azure 客體代理程式服務是否在 VM 服務 (services.msc) 中執行。 請嘗試重新啟動 Windows Azure 客體代理程式服務並啟動備份。
2. 如果 Windows Azure 客體代理程式服務未顯示在控制台的服務中，請移至 [程式和功能] 來判斷是否已安裝 Windows Azure 客體代理程式服務。
3. 如果 Windows Azure 客體代理程式顯示在 [程式和功能] 中，請將 Windows Azure 客體代理程式解除安裝。
4. 下載並安裝[最新版的代理程式 MSI](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409)。 您必須擁有系統管理員權限，才能完成安裝。
5. 確認 Windows Azure 客體代理程式服務顯示在服務中。
6. 執行隨選備份：
   - 在入口網站中，選取 [立即備份]。

此外，確認 VM 中[已安裝 Microsoft .NET 4.5](https://docs.microsoft.com/dotnet/framework/migration-guide/how-to-determine-which-versions-are-installed)。 VM 代理程式需有 .NET 4.5 才能與服務通訊。

### <a name="the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms"></a>VM 中安裝的代理程式已過時 (適用於 Linux VM)

#### <a name="solution"></a>方案

針對 Linux VM，與代理程式或擴充功能相關的多數失敗是由於會影響過時 VM 代理程式的問題所造成。 若要對此問題進行疑難排解，請遵循下列一般方針：

1. 請遵循[更新 Linux VM 代理程式](../virtual-machines/linux/update-agent.md)的指示。

   > [!NOTE]
   > 我們強烈建議您只透過散發套件存放庫更新代理程式。 我們不建議直接從 GitHub 下載代理程式程式碼，並加以更新。 如果最新的代理程式不適用於您的散發套件，請連絡散發套件支援以取得如何進行安裝的指示。 若要檢查最新的代理程式，請移至 GitHub 儲存機制中的 [Microsoft Azure Linux 代理程式 (英文)](https://github.com/Azure/WALinuxAgent/releases) 頁面。

2. 執行下列命令，確定 Azure 代理程式正在 VM 上執行：`ps -e`

   如果此程序不在執行中，請使用下列命令來重新啟動它：

   - 針對 Ubuntu：`service walinuxagent start`
   - 針對其他散發套件︰`service waagent start`

3. [設定自動重新啟動代理程式](https://github.com/Azure/WALinuxAgent/wiki/Known-Issues#mitigate_agent_crash)。
4. 執行新的測試備份。 如果失敗持續發生，請從 VM 收集下列記錄：

   - /var/lib/waagent/*.xml
   - /var/log/waagent.log
   - /var/log/azure/*

如果您需要 waagent 的詳細資訊記錄，請遵循下列步驟：

1. 在 /etc/waagent.conf 檔案中，找出下一行︰**Enable verbose logging (y|n)**
2. 將 **Logs.Verbose** 值從 *n* 變更為 *y*。
3. 儲存變更，然後完成本節前面所述的步驟來重新啟動 waagent。

### <a name="the-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-taken"></a>無法擷取快照集狀態或無法取得快照集

VM 備份仰賴發給底層儲存體帳戶的快照命令。 備份可能會失敗，因為它無權存取儲存體帳戶，或是因為快照集工作延遲執行。

#### <a name="solution"></a>方案

下列狀況可能導致快照集工作失敗：

| 原因 | 方案 |
| --- | --- |
| 因為遠端桌面通訊協定 (RDP) 中的 VM 關機，而導致報告的 VM 狀態不正確。 | 如果您關閉 RDP 中的 VM，請檢查入口網站，以判斷 VM 狀態是否正確。 如果不正確，可使用 VM 儀表板上的 [關閉] 選項來關閉入口網站中的 VM。 |
| VM 無法從 DHCP 取得主機或網狀架構位址。 | 必須在來賓內啟用 DHCP，IaaS VM 備份才能運作。 如果 VM 無法從 DHCP 回應 245 取得主機或網狀架構位址，則無法下載或執行任何延伸模組。 如果您需要靜態私人 IP，則應透過**Azure 入口網站**或**PowerShell**加以設定，並確定已啟用 VM 內的 DHCP 選項。 [深入瞭解](../virtual-network/virtual-networks-static-private-ip-arm-ps.md#change-the-allocation-method-for-a-private-ip-address-assigned-to-a-network-interface)如何使用 PowerShell 設定靜態 IP 位址。

### <a name="the-backup-extension-fails-to-update-or-load"></a>備份擴充功能無法更新或載入

如果無法載入延伸模組，備份就會因為無法取得快照集而失敗。

#### <a name="solution"></a>方案

將擴充功能解除安裝，以強制重新載入 VMSnapshot 擴充功能。 下一次備份嘗試會重新載入解除安裝。

若要將解除安裝解除安裝：

1. 在 [Azure 入口網站](https://portal.azure.com/)中，移至發生備份失敗的 VM。
2. 選取 [Settings] \(設定)。
3. 選取 [擴充功能]。
4. 選取 [Vmsnapshot 解除安裝]。
5. 選取 [解除安裝]。

針對 Linux VM，如果 VMSnapshot 延伸模組未顯示在 Azure 入口網站中，[更新 Azure Linux 代理程式](../virtual-machines/linux/update-agent.md)，然後再執行備份。

完成這些步驟之後，下一次備份期間會重新安裝延伸模組。

### <a name="remove_lock_from_the_recovery_point_resource_group"></a>從還原點資源群組中移除鎖定

1. 登入 [Azure 入口網站](https://portal.azure.com/)。
2. 移至 [所有資源] 選項，選取下列格式的還原點集合資源群組：AzureBackupRG_`<Geo>`_`<number>`。
3. 在 [設定] 區段中，選取 [鎖定] 來顯示鎖定項目。
4. 若要移除鎖定，請選取省略符號，然後按一下 [刪除]。

    ![刪除鎖定](./media/backup-azure-arm-vms-prepare/delete-lock.png)

### <a name="clean_up_restore_point_collection"></a> 清除還原點集合

移除鎖定之後，必須清除還原點。

如果您刪除 VM 的資源群組或 VM 本身，受控磁片的立即還原快照集會維持作用中狀態，並會根據保留集而過期。 若要刪除儲存在還原點集合中的立即還原快照集（如果不需要的話），請根據以下所述的步驟來清除還原點集合。

若要清除還原點，請遵循下列任一方法：<br>

- [藉由執行臨機操作備份來清除還原點集合](#clean-up-restore-point-collection-by-running-ad-hoc-backup)<br>
- [從 Azure 入口網站清除還原點集合](#clean-up-restore-point-collection-from-azure-portal)<br>

#### <a name="clean-up-restore-point-collection-by-running-ad-hoc-backup"></a>藉由執行臨機操作備份來清除還原點集合

移除鎖定之後，請觸發臨機操作/手動備份。 這可確保還原點會自動清除。 此臨機操作/手動作業會第一次失敗;不過，它會確保自動清除，而不是手動刪除還原點。 完成清除作業之後，下一個排定的備份應該會成功。

> [!NOTE]
> 在觸發臨機操作/手動備份的幾個小時後，將會自動清除。 如果您排定的備份仍然失敗，請使用[此處](#clean-up-restore-point-collection-from-azure-portal)列出的步驟，嘗試手動刪除還原點集合。

#### <a name="clean-up-restore-point-collection-from-azure-portal"></a>從 Azure 入口網站清除還原點集合 <br>

若要手動清除還原點集合（因為資源群組的鎖定而未清除），請嘗試下列步驟：

1. 登入 [Azure 入口網站](https://portal.azure.com/)。
2. 在 [中樞] 功能表上按一下 [所有資源]，選取下列格式的資源群組：AzureBackupRG_`<Geo>`_`<number>`，也就是您 VM 所在的位置。

    ![刪除鎖定](./media/backup-azure-arm-vms-prepare/resource-group.png)

3. 按一下 [資源群組]，[概觀] 刀鋒視窗會隨即出現。
4. 選取 [顯示隱藏的類型] 選項，以顯示所有隱藏的資源。 選取下列格式的還原點集合：AzureBackupRG_`<VMName>`_`<number>`。

    ![刪除鎖定](./media/backup-azure-arm-vms-prepare/restore-point-collection.png)

5. 按一下 [**刪除**] 以清除還原點集合。
6. 重試備份作業。

> [!NOTE]
 >如果資源（RP 集合）有大量的還原點，則從入口網站刪除它們可能會超時並失敗。 這是已知的 CRP 問題，其中所有還原點不會在規定的時間刪除而作業會逾時；不過，刪除作業通常在 2 或 3 次重試之後就會成功。
