---
title: Azure 備份：使用 Azure 入口網站還原虛擬機器
description: 使用 Azure 入口網站從復原點還原 Azure 虛擬機器
ms.reviewer: geg
author: dcurwin
manager: carmonm
keywords: 還原備份；如何還原；復原點；
ms.service: backup
ms.topic: conceptual
ms.date: 09/17/2019
ms.author: dacurwin
ms.openlocfilehash: 759be3691ba44c92033ec71fd031f9c6e47d6cb4
ms.sourcegitcommit: 9dec0358e5da3ceb0d0e9e234615456c850550f6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/14/2019
ms.locfileid: "72311907"
---
# <a name="how-to-restore-azure-vm-data-in-azure-portal"></a>如何在 Azure 入口網站中還原 Azure VM 資料

本文說明如何從儲存在 [Azure 備份](backup-overview.md)復原服務保存庫的復原點還原 Azure VM 資料。



## <a name="restore-options"></a>還原選項

Azure 備份提供數種方法來還原 VM。

**還原選項** | **詳細資料**
--- | ---
**建立新的 VM** | 從還原點快速建立及啟動基本 VM 並加以執行。<br/><br/> 您可以指定 VM 的名稱，並選取將在其中放置資源群組和虛擬網路（VNet），並為還原的 VM 指定儲存體帳戶。 必須在與來源 VM 相同的區域中建立新的 VM。
**還原磁碟** | 還原 VM 磁片，然後可以用來建立新的 VM。<br/><br/> Azure 備份提供一個範本，協助您自訂和建立 VM。 <br/><br> 還原作業會產生範本，您可以下載並使用該範本來指定自訂 VM 設定，並建立 VM。<br/><br/> 磁片會複製到您指定的儲存體帳戶。<br/><br/> 或者，您可以將磁碟連結至現有 VM，或使用 PowerShell 建立新的 VM。<br/><br/> 此選項十分適用於自訂 VM、新增備份時沒有的組態設定，或新增必須使用範本或 PowerShell 來配置的設定。
**取代現有的** | 您可以還原磁碟，然後使用該磁碟來取代現有 VM 上的磁碟。<br/><br/> 目前的 VM 必須存在。 如果已刪除，則無法使用此選項。<br/><br/> Azure 備份會在更換磁片之前取得現有 VM 的快照，並將其儲存在您指定的暫存位置。 已連線至 VM 的現有磁片會取代為選取的還原點。<br/><br/> 快照集會複製到保存庫，並根據保留原則加以保留。 <br/><br/> 針對未加密的受控 VM 支援取代現有的項目。 不支援非受控磁碟、[一般化的 VM](https://docs.microsoft.com/azure/virtual-machines/windows/capture-image-resource) 或[使用自訂映像建立的](https://azure.microsoft.com/resources/videos/create-a-custom-virtual-machine-image-in-azure-resource-manager-with-powershell/) \(英文\) VM。<br/><br/> 如果還原點中的磁碟數目多於或少於目前的 VM，則還原點中的磁碟數目只會反映該 VM 組態。<br/><br/>


> [!NOTE]
> 您也可以復原 Azure VM 上的特定檔案和資料夾。 [深入了解](backup-azure-restore-files-from-vm.md)。
>
> 如果您執行[最新版](backup-instant-restore-capability.md)適用於 Azure VM 的 Azure 備份 (又稱為立即還原)，則快照集可保留最多七天，而且您可以在備份資料傳送至保存庫之前，從快照集還原 VM。 如果您想要從過去七天的備份還原 VM，從快照集還原會比從保存庫還原還快。

## <a name="storage-accounts"></a>儲存體帳戶

儲存體帳戶的一些詳細資料：

- **建立 VM**：當您建立新的 VM 時，VM 將會放在您指定的儲存體帳戶中。
- **復原磁碟**：當您復原磁碟時，磁片會複製到您指定的儲存體帳戶。 還原作業會產生範本，供您下載並用來指定自訂 VM 設定。 此範本會放在指定的儲存體帳戶中。
- **取代磁片**：當您更換現有 VM 上的磁片時，Azure 備份會先取得現有 VM 的快照集，然後再更換磁片。 快照集會儲存在您指定的預備位置（儲存體帳戶）中。 此儲存體帳戶是用來在還原過程中暫時儲存快照集，我們建議您建立新的帳戶來執行此作業，之後就可以輕鬆地移除。
- **儲存體帳戶位置**：儲存體帳戶必須位於與保存庫相同的區域中。 只會顯示這些帳戶。 如果位置中沒有儲存體帳戶，您必須建立一個。
- **儲存體類型**：不支援 Blob 儲存體。
- **儲存體冗余**：不支援區域備援儲存體 (ZRS)。 帳戶名稱後面會以括弧括住帳戶的複寫和重複資訊。 
- **Premium 儲存體**：
    - 還原非 premium Vm 時，不支援 premium 儲存體帳戶。
    - 還原受控 Vm 時，不支援使用網路規則設定的 premium 儲存體帳戶。


## <a name="before-you-start"></a>開始之前

若要還原 VM （建立新的 VM），請確定您具有還原 VM 作業的正確角色型存取控制（RBAC）[許可權](backup-rbac-rs-vault.md#mapping-backup-built-in-roles-to-backup-management-actions)。

如果您沒有許可權，您可以[復原磁碟](#restore-disks)，然後在復原磁碟之後，使用在還原作業中產生的[範本](#use-templates-to-customize-a-restored-vm)來建立新的 VM。



## <a name="select-a-restore-point"></a>選取還原點

1. 在所要還原 VM 的相關保存庫中，按一下 [備份項目] > [Azure 虛擬機器]。
2. 建立 VM。 根據預設，VM 儀表板上會顯示過去 30 天內的復原點。 您可以根據日期、時間範圍和不同類型的快照集一致性來顯示或篩選出超過 30 天的復原點。
3. 若要還原 VM，請按一下 [還原 VM]。

    ![還原點](./media/backup-azure-arm-restore-vms/restore-point.png)

4. 選取要用於復原的還原點。

## <a name="choose-a-vm-restore-configuration"></a>選擇 VM 還原組態

1. 在 [還原設定] 中，選取還原選項：
    - **建立新項目**：如果您想要建立新的 VM，請使用此選項。 您可以使用簡單的設定來建立 VM，或還原磁碟並建立自訂 VM。
    - **取代現有的**：如果您想要取代現有 VM 上的磁碟，請使用此選項。

        ![還原組態精靈](./media/backup-azure-arm-restore-vms/restore-configuration.png)

2. 為您所選的還原選項指定設定。

## <a name="create-a-vm"></a>建立 VM

其中一個[還原選項](#restore-options)可讓您從還原點快速建立具有基本設定的 VM。

1. 在 [還原設定] > [新建] > [還原類型] 中，選取 [建立虛擬機器]。
2. 在 [**虛擬機器名稱**] 中，指定訂用帳戶中不存在的 VM。
3. 在 [資源群組] 中，選取新 VM 的現有資源群組，或使用全域的唯一名稱來建立新的資源群組。 如果您指派已經存在的名稱，Azure 就會為群組指派與 VM 相同的名稱。
4. 在 [虛擬網路] 中，選取將放置 VM 的 VNet。 與訂用帳戶相關聯的所有 VNet 均會顯示。 選取子網路。 預設會選取第一個子網路。
5. 在 [**儲存位置**] 中，指定 VM 的儲存體帳戶。 [深入了解](#storage-accounts)。

    ![還原組態精靈](./media/backup-azure-arm-restore-vms/recovery-configuration-wizard1.png)

6. 在 [還原設定] 中，選取 [確定]。 在 [還原] 中，按一下 [還原] 以觸發還原作業。


## <a name="restore-disks"></a>還原磁碟

其中一個[還原選項](#restore-options)可讓您從還原點建立磁碟。 然後，您可以使用該磁碟來執行下列其中一項操作：

- 使用還原作業期間所產生的範本來自訂設定，並觸發 VM 部署。 您會編輯預設範本設定，並提交範本以進行 VM 部署。
- [將還原的磁碟](https://docs.microsoft.com/azure/virtual-machines/windows/attach-managed-disk-portal)連結至現有 VM。
- 使用 PowerShell 從還原的磁片[建立新的 VM](https://docs.microsoft.com/azure/backup/backup-azure-vms-automation#create-a-vm-from-restored-disks) 。

1. 在 [還原設定] > [新建] > [還原類型] 中，選取 [還原磁碟]。
2. 在 [資源群組] 中，選取已還原磁碟的現有資源群組，或使用全域的唯一名稱來建立新的資源群組。
3. 在 [儲存體帳戶] 中，指定要將 VHD 複製到其中的帳戶。 [深入了解](#storage-accounts)。

    ![已完成復原設定](./media/backup-azure-arm-restore-vms/trigger-restore-operation1.png)

4. 在 [還原設定] 中，選取 [確定]。 在 [還原] 中，按一下 [還原] 以觸發還原作業。

當您的虛擬機器使用受控磁片，並選取 [**建立虛擬機器**] 選項時，Azure 備份不會使用指定的儲存體帳戶。 在**復原磁碟**和**立即還原**的案例中，儲存體帳戶僅用於儲存範本。 受控磁片會建立在指定的資源群組中。
當您的虛擬機器使用非受控磁片時，它們會以 blob 的形式還原至儲存體帳戶。

### <a name="use-templates-to-customize-a-restored-vm"></a>使用範本自訂還原的 VM

還原磁碟後，使用還原作業期間所產生的範本來自訂和建立新 VM：

1. 開啟相關作業的**還原作業詳細資料**。

2. 在 [還原作業詳細資料] 中，選取 [部署範本] 來起始範本部署。

    ![還原作業向下鑽研](./media/backup-azure-arm-restore-vms/restore-job-drill-down1.png)

3. 若要自訂範本中提供的 VM 設定，請按一下 [編輯範本]。 如果您想要新增更多自訂，請按一下 [編輯參數]。
    - [深入了解](../azure-resource-manager/resource-group-template-deploy-portal.md#deploy-resources-from-custom-template)自訂範本中的部署資源。
    - [深入了解](../azure-resource-manager/resource-group-authoring-templates.md)編寫範本。

   ![載入範本部署](./media/backup-azure-arm-restore-vms/edit-template1.png)

4. 輸入 VM 的自訂值，接受 [條款和條件]，然後按一下 [購買]。

   ![提交範本部署](./media/backup-azure-arm-restore-vms/submitting-template1.png)


## <a name="replace-existing-disks"></a>取代現有的磁碟

其中一個[還原選項](#restore-options)可讓您使用選取的還原點取代現有 VM 磁碟。 [檢閱](#restore-options)所有還原選項。

1. 在 [還原設定] 中，按一下 [取代現有的]。
2. 在 [還原類型] 中，選取 [取代磁碟]。 這是將用來取代現有 VM 磁碟的還原點。
3. 在 [**預備位置**] 中，指定在還原過程中應該儲存目前受控磁片的快照集。 [深入了解](#storage-accounts)。

   ![還原組態精靈取代現有的](./media/backup-azure-arm-restore-vms/restore-configuration-replace-existing.png)


## <a name="restore-vms-with-special-configurations"></a>還原具有特殊設定的 Vm

有幾個可能需要還原 VM 的常見案例。

**案例** | **指引**
--- | ---
**使用 Hybrid Use Benefit 還原 VM** | 如果 Windows VM 使用 [Hybrid Use Benefit (HUB) 授權](../virtual-machines/windows/hybrid-use-benefit-licensing.md)，請使用提供的範本 (將**授權類型**設定為 **Windows_Server**) 或 PowerShell 來還原磁碟並建立新 VM。  此設定也可以在建立 VM 之後套用。
**在 Azure 資料中心發生災害時還原 VM** | 如果保存庫使用 GRS 和主要資料中心來因應 VM 的停機狀況，則 Azure 備份可支援將備份的 VM 還原至配對的資料中心。 您可以在配對的資料中心內選取儲存體帳戶，然後依正常程序進行還原。 Azure 備份會使用配對區域中的計算服務來建立還原的 VM。 [深入了解](../resiliency/resiliency-technical-guidance-recovery-loss-azure-region.md)資料中心復原。
**還原單一網域中的單一網域控制站 VM** | 像任何其他 VM 一樣地還原 VM。 請注意：<br/><br/> 從 Active Directory 的觀點來看，Azure VM 就像任何其他 VM 一樣。<br/><br/> 我們也提供了目錄服務還原模式 (DSRM)，因此，您可以進行所有的 Active Directory 復原案例。 [深入了解](https://docs.microsoft.com/windows-server/identity/ad-ds/get-started/virtual-dc/virtualized-domain-controllers-hyper-v)虛擬化網域控制站的備份與還原考量。
**還原單一網域中的多個網域控制站 VM** | 如果可以透過網路連線到相同網域中的其他網域控制站，則可以像任何 VM 一樣還原網域控制站。 如果該網域控制站是網域內剩餘的最後一個網域控制站，或者您是在隔離的網路中進行復原，請使用[樹系復原](https://docs.microsoft.com/windows-server/identity/ad-ds/manage/ad-forest-recovery-single-domain-in-multidomain-recovery)。
**還原單一樹系中的多個網域** | 我們建議使用[樹系復原](https://docs.microsoft.com/windows-server/identity/ad-ds/manage/ad-forest-recovery-single-domain-in-multidomain-recovery)。
**裸機還原** | Azure VM 與內部部署 Hypervisor 的主要差異是，Azure 沒有提供 VM 主控台。 在某些情況下，您必須使用主控台，例如使用裸機復原 (BMR) 類型的備份進行復原。 不過，從保存庫來還原 VM 可完整取代 BMR。
**還原具有特殊網路組態的 VM** | 特殊網路組態包含使用內部或外部負載平衡、使用多個 NIC 或多個保留 IP 位址的 VM。 您可以使用[還原磁碟選項](#restore-disks)來還原這些 VM。 此選項會將 Vhd 複製到指定的儲存體帳戶，然後您可以建立具有[內部](https://azure.microsoft.com/documentation/articles/load-balancer-internal-getstarted/)或[外部](https://azure.microsoft.com/documentation/articles/load-balancer-internet-getstarted/)負載平衡器、[多個 NIC](../virtual-machines/windows/multiple-nics.md)或[多個保留 IP 位址](../virtual-network/virtual-network-multiple-ip-addresses-powershell.md)的 VM，並根據您的配置.
**NIC/子網上的網路安全性群組（NSG）** | Azure VM 備份支援在 vnet、子網和 NIC 層級備份和還原 NSG 資訊。
**區域釘選的 Vm** | Azure 備份支援備份和還原已分區的固定 Vm。 [深入了解](https://azure.microsoft.com/global-infrastructure/availability-zones/)

## <a name="track-the-restore-operation"></a>追蹤還原作業
在觸發還原作業之後，備份服務會建立用於追蹤的作業。 Azure 備份會在入口網站中顯示作業的相關通知。 如果看不到它們，請選取 [**通知**] 符號，然後選取 [**查看所有作業**] 以查看還原程式狀態。

![已觸發還原](./media/backup-azure-arm-restore-vms/restore-notification1.png)

 透過以下方式來追蹤還原：

1. 若要檢視作業的運作，請按一下 [通知] 超連結。 或者，在保存庫中按一下 [備份作業]，然後按一下相關的 VM。

    ![保存庫中的 VM 清單](./media/backup-azure-arm-restore-vms/restore-job-in-progress1.png)

2. 若要監視還原進度，請按一下狀態為 [進行中] 的任何還原作業。 這會顯示進度列，其中顯示還原進度的相關資訊：

    - **預估的還原時間**：一開始會提供完成還原作業所花費的時間。 作業進行時，所花費的時間會減少，並在還原作業完成時到達零。
    - **還原的百分比**。 顯示已完成的還原作業百分比。
    - **已傳輸的位元組數**：如果您是藉由建立新 VM 來進行還原，這會顯示已傳輸的位元組數和要傳輸的位元組總數。

## <a name="post-restore-steps"></a>還原後的步驟

還原 VM 之後有些要注意的事項：

- 系統會安裝出現在備份組態期間的擴充功能，但不會加以啟用。 如果您發現問題，請重新安裝擴充功能。
- 如果備份的 VM 具有靜態 IP 位址，則還原的 VM 會有動態 IP 位址，這是為了避免發生衝突。 您可以[將靜態 IP 位址新增至已還原的 VM](../virtual-network/virtual-networks-reserved-private-ip.md#how-to-add-a-static-internal-ip-to-an-existing-vm)。
- 還原的 VM 不會有可用性設定值組。 如果您使用 [復原磁碟] 選項，則當您使用提供的範本或 PowerShell 從磁片建立 VM 時，可以[指定可用性設定組](../virtual-machines/windows/tutorial-availability-sets.md)。
- 如果您使用 cloud-init 型 Linux 散發套件 (例如 Ubuntu)，基於安全理由，還原後會封鎖密碼。 請在還原的 VM 上使用 VMAccess 擴充功能[重設密碼](../virtual-machines/linux/reset-password.md)。 我們建議您在這些散發套件中使用 SSH 金鑰，如此一來，您就不需要在還原後重設密碼。
- 如果您因為 VM 與網域控制站中斷關聯性而無法存取 VM，請遵循下列步驟來啟動 VM：
    - 將 OS 磁片做為資料磁片連結到復原的 VM。
    - 如果找不到 Azure 代理程式，請遵循此[連結](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/install-vm-agent-offline)，手動安裝 VM 代理程式。
    - 在 VM 上啟用序列主控台存取，以允許對 VM 的命令列存取
    
  ```
    bcdedit /store <drive letter>:\boot\bcd /enum
    bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /set {bootmgr} displaybootmenu yes
    bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /set {bootmgr} timeout 5
    bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /set {bootmgr} bootems yes
    bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /ems {<<BOOT LOADER IDENTIFIER>>} ON
    bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /emssettings EMSPORT:1 EMSBAUDRATE:115200
    ```
    - 重建 VM 時，請使用 Azure 入口網站來重設本機系統管理員帳戶和密碼
    - 使用序列主控台存取和 CMD 從網域退出後 VM

    ```
    cmd /c "netdom remove <<MachineName>> /domain:<<DomainName>> /userD:<<DomainAdminhere>> /passwordD:<<PasswordHere>> /reboot:10 /Force" 
    ```

- 一旦 VM 脫離並重新啟動之後，您就可以使用本機系統管理員認證成功地 RDP 至 VM，並成功將 VM 重新加入網域。

## <a name="backing-up-restored-vms"></a>備份已還原的 VM

- 如果您將 VM 還原至相同的資源群組，並使用與原始備份 VM 相同的名稱，則會在還原後於 VM 上繼續進行備份。
- 如果您將 VM 還原到不同的資源群組，或為還原的 VM 指定了不同的名稱，則您必須為還原的 VM 設定備份。

## <a name="next-steps"></a>後續步驟

- 如果您在還原過程中遇到困難，[請檢閱](backup-azure-vms-troubleshoot.md#restore)常見問題和錯誤。
- 還原 VM 之後，深入了解[管理虛擬機器](backup-azure-manage-vms.md)
