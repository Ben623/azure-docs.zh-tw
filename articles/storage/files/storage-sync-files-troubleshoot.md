---
title: 對 Azure 檔案同步進行疑難排解 | Microsoft Docs
description: 針對 Azure 檔案同步常見問題進行疑難排解。
services: storage
author: jeffpatt24
ms.service: storage
ms.topic: article
ms.date: 01/31/2019
ms.author: jeffpatt
ms.subservice: files
ms.openlocfilehash: 9cd1be26f6832fffb86dfefd0d93d9dbb393c0f0
ms.sourcegitcommit: 82efacfaffbb051ab6dc73d9fe78c74f96f549c2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/20/2019
ms.locfileid: "67303884"
---
# <a name="troubleshoot-azure-file-sync"></a>針對 Azure 檔案同步進行移難排解
使用 Azure 檔案同步，將組織的檔案共用集中在 Azure 檔案服務中，同時保有內部部署檔案伺服器的彈性、效能及相容性。 Azure 檔案同步會將 Windows Server 轉換成 Azure 檔案共用的快速快取。 您可以使用 Windows Server 上可用的任何通訊協定來從本機存取資料，包括 SMB、NFS 和 FTPS。 您可以視需要存取多個散佈於世界各地的快取。

本文旨在協助您針對使用 Azure 檔案同步部署時所發生的問題進行疑難排解，並解決這些問題。 文中也說明如果需要更深入調查問題，如何從系統收集重要的記錄。 如果您找不到問題的答案，可透過下列管道 (依先後順序) 和我們連絡：

1. [Azure 儲存體論壇](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazuredata)。
2. [Azure 檔案服務 UserVoice](https://feedback.azure.com/forums/217298-storage/category/180670-files) \(英文\)。
3. Microsoft 支援服務。 若要建立新的支援要求，在 Azure 入口網站的 [說明]  索引標籤上，選取 [說明 + 支援]  按鈕，然後選取 [新增支援要求]  。

## <a name="im-having-an-issue-with-azure-file-sync-on-my-server-sync-cloud-tiering-etc-should-i-remove-and-recreate-my-server-endpoint"></a>我在伺服器上的 Azure 檔案同步有問題 (同步、雲端分層等)。 我是否應移除並重新建立伺服器端點？
[!INCLUDE [storage-sync-files-remove-server-endpoint](../../../includes/storage-sync-files-remove-server-endpoint.md)]

## <a name="agent-installation-and-server-registration"></a>代理程式安裝和伺服器註冊
<a id="agent-installation-failures"></a>**針對代理程式安裝失敗進行疑難排解**  
如果 Azure 檔案同步代理程式安裝失敗，使用提升權限的命令提示字元，執行下列命令，以啟用代理程式安裝期間的記錄：

```
StorageSyncAgent.msi /l*v AFSInstaller.log
```

檢閱 installer.log 判斷安裝失敗的原因。

<a id="agent-installation-on-DC"></a>**在 Active Directory 網域控制站上安裝代理程式失敗**  
如果您嘗試在 PDC 角色擁有者執行 Windows Server 2008 R2 或更低 OS 版本的 Active Directory 網域控制站上安裝同步代理程式，則可能會遇到無法安裝同步代理程式的問題。

若要解決此問題，請將 PDC 角色轉移到另一個執行 Windows Server 2012 R2 或更新版本的網域控制站，然後安裝同步代理程式。

<a id="server-registration-missing"></a>**伺服器未列在 Azure 入口網站的 [已註冊的伺服器] 下**  
如果伺服器未列在儲存體同步服務的 [已註冊的伺服器]  下：
1. 登入您要註冊的伺服器。
2. 開啟 [檔案總管]，然後移至儲存體同步代理程式的安裝目錄 (預設位置是 C:\Program Files\Azure\StorageSyncAgent)。 
3. 執行 ServerRegistration.exe，然後完成精靈的指示向儲存體同步服務註冊伺服器。

<a id="server-already-registered"></a>**Azure 檔案同步代理程式安裝期間，伺服器註冊顯示下列訊息：「此伺服器已註冊」** 

![[伺服器註冊] 對話方塊的螢幕擷取畫面顯示 [此伺服器已註冊] 錯誤訊息](media/storage-sync-files-troubleshoot/server-registration-1.png)

如果先前已向儲存體同步服務註冊該伺服器，就會出現此訊息。 若要向目前的儲存體同步服務取消註冊伺服器，再向新的儲存體同步服務註冊，請完成[向 Azure 檔案同步取消註冊伺服器](storage-sync-files-server-registration.md#unregister-the-server-with-storage-sync-service)中所述的步驟。

如果伺服器未列在儲存體同步服務的 [已註冊的伺服器]  底下，請在您想要取消註冊的伺服器上執行下列 PowerShell 命令：

```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
Reset-StorageSyncServer
```

> [!Note]  
> 如果伺服器是叢集的一部分，您可以使用選擇性的 *Reset-StorageSyncServer -CleanClusterRegistration* 參數，一併移除叢集的註冊。

<a id="web-site-not-trusted"></a>**我在註冊伺服器時收到多個「網站不受信任」回應，為什麼？**  
如果在伺服器註冊期間啟用了**增強的 Internet Explorer 安全性**原則，便會發生此問題。 若想進一步了解如何正確停用**增強的 Internet Explorer 安全性**原則，請參閱[準備 Windows Server 以搭配 Azure 檔案同步使用](storage-sync-files-deployment-guide.md#prepare-windows-server-to-use-with-azure-file-sync)和[如何部署 Azure 檔案同步](storage-sync-files-deployment-guide.md)。

## <a name="sync-group-management"></a>同步群組管理
<a id="cloud-endpoint-using-share"></a>**雲端端點建立失敗，發生此錯誤：「指定的 Azure 檔案共用已由不同雲端端點使用中」**  
如果 Azure 檔案共用已由另一個雲端端點使用中，就會發生這個問題。 

如果您看到這個訊息，而 Azure 檔案共用目前未由雲端端點使用中，請執行下列步驟，以清除 Azure 檔案共用上的 Azure 檔案同步中繼資料：

> [!Warning]  
> 刪除 Azure 檔案共用上目前正由雲端端點使用中的中繼資料時，會導致 Azure 檔案同步作業失敗。 

1. 在 Azure 入口網站中，移至您的 Azure 檔案共用。  
2. 以滑鼠右鍵按一下 Azure 檔案共用，然後選取 [編輯中繼資料]  。
3. 以滑鼠右鍵按一下 [SyncService]  ，然後選取 [刪除]  。

<a id="cloud-endpoint-authfailed"></a>**雲端端點建立失敗，發生此錯誤："AuthorizationFailed"**  
如果您的使用者帳戶沒有足夠權限可建立雲端端點，就會發生此問題。 

若要建立雲端端點，您的使用者帳戶必須具有下列 Microsoft 授權權限：  
* 讀取：取得角色定義
* 寫入：建立或更新自訂角色定義
* 讀取：取得角色指派
* 寫入：建立角色指派

下列內建角色具有所需的 Microsoft 授權權限：  
* 擁有者
* 使用者存取系統管理員

判斷您的使用者帳戶角色是否具有所需的權限：  
1. 在 Azure 入口網站中，按一下 [資源群組]  。
2. 選取儲存體帳戶所在的資源群組，然後選取 [存取控制 (IAM)]  。
3. 選取 [角色指派]  索引標籤。
4. 選取您的使用者帳戶的 [角色] (例如，[擁有者]、[參與者])。 
5. 在 [資源提供者]  清單中，選取 [Microsoft 授權]  。 
    * [角色指派]  應具有 [讀取]  和 [寫入]  權限。
    * [角色定義]  應具有 [讀取]  和 [寫入]  權限。

<a id="server-endpoint-createjobfailed"></a>**伺服器端點建立失敗，發生此錯誤："MgmtServerJobFailed" (錯誤碼：-2134375898)**  
如果伺服器端點路徑位於系統磁碟區上，而且已啟用雲端階層處理，就會發生此問題。 系統磁碟區上不支援雲端階層。 若要在系統磁碟區上建立伺服器端點，請於建立伺服器端點時，停用雲端階層處理。

<a id="server-endpoint-deletejobexpired"></a>**伺服器端點刪除失敗，發生此錯誤："MgmtServerJobExpired"**                 
如果伺服器離線或沒有網路連線能力，就會發生此問題。 如果伺服器已無法再使用，請在入口網站中取消註冊伺服器，從而刪除伺服器端點。 若要刪除伺服器端點，請依照[向 Azure 檔案同步取消註冊伺服器](storage-sync-files-server-registration.md#unregister-the-server-with-storage-sync-service)中所述的步驟進行。

<a id="server-endpoint-provisioningfailed"></a>**無法開啟伺服器端點屬性頁面，或更新雲端階層處理原則**  
如果伺服器端點上的管理作業失敗，就會發生此問題。 如果伺服器端點屬性頁面未在 Azure 入口網站中開啟，則從伺服器使用 PowerShell 命令來更新伺服器端點可修正此問題。 

```powershell
# Get the server endpoint id based on the server endpoint DisplayName property
Get-AzStorageSyncServerEndpoint `
    -ResourceGroupName myrgname `
    -StorageSyncServiceName storagesvcname `
    -SyncGroupName mysyncgroup | `
Tee-Object -Variable serverEndpoint

# Update the free space percent policy for the server endpoint
Set-AzStorageSyncServerEndpoint `
    -InputObject $serverEndpoint
    -CloudTiering `
    -VolumeFreeSpacePercent 60
```
<a id="server-endpoint-noactivity"></a>**伺服器端點的健康狀態為 [無活動] 或 [擱置中]，且 [已註冊的伺服器] 刀鋒視窗上的伺服器狀態為 [顯示為離線]**  

如果儲存體同步監視器程序未執行，或伺服器因 Proxy 或防火牆而無法與 Azure 檔案同步服務進行通訊，即可能發生此問題。

若要解決此問題，請執行下列步驟：

1. 在伺服器上開啟工作管理員，並確認儲存體同步監視器 (AzureStorageSyncMonitor.exe) 程序正在執行。 如果此程序未執行，先嘗試重新啟動伺服器。 如果重新啟動伺服器無法解決此問題，請升級至最新版 Azure 檔案同步[代理程式版本](https://docs.microsoft.com/azure/storage/files/storage-files-release-notes)。
2. 確認防火牆和 Proxy 設定已正確設定：
    - 如果伺服器位於防火牆後方，請確認允許連接埠 443 輸出。 如果防火牆限制僅允許對特定網域的流量，請確認您可以存取防火牆[文件](https://docs.microsoft.com/azure/storage/files/storage-sync-files-firewall-and-proxy#firewall)中列出的網域。
    - 如果伺服器位於 Proxy 後方，請依照 Proxy [文件](https://docs.microsoft.com/azure/storage/files/storage-sync-files-firewall-and-proxy#proxy)中的步驟設定整部電腦或應用程式專屬的 Proxy 設定。

<a id="endpoint-noactivity-sync"></a>**伺服器端點的健康狀態為 [無活動]，且 [已註冊的伺服器] 刀鋒視窗上的伺服器狀態為 [離線]**  

伺服器端點健康狀態為 [無活動] 時，表示伺服器端點在過去兩小時內未記錄任何同步活動。

伺服器端點未記錄同步活動的可能原因如下：

- 已安裝代理程式版本 4.3.0.0 或較舊版本，且伺服器具有作用中的 VSS 同步工作階段 (SnapshotSync)。 一個伺服器端點有作用中的 VSS 同步工作階段時，相同磁碟區上的其他伺服器端點在 VSS 同步工作階段完成之前，將無法啟動開始同步工作階段。 若要解決此問題，請安裝代理程式版本 5.0.2.0 或更新版本，以便在 VSS 同步工作階段作用中時，支援在磁碟區上同步處理多個伺服器端點。

    若要查看伺服器上目前的同步活動，請參閱[如何監視目前同步工作階段的進度？](#how-do-i-monitor-the-progress-of-a-current-sync-session)。

- 伺服器已達到並行同步工作階段的數目上限。 
    - 代理程式版本 4.x 和更新版本：限制隨可用的系統資源而不同。
    - 代理程式版本 3.x：每個處理器 2 個作用中的同步處理工作階段，或每個伺服器最多 8 個作用中的同步處理工作階段。

> [!Note]  
> 如果 [已註冊的伺服器] 刀鋒視窗上的伺服器狀態為 [顯示為離線]，請執行[伺服器端點的健康狀態為 [無活動] 或 [擱置中]，且 [已註冊的伺服器] 刀鋒視窗上的伺服器狀態為 [顯示為離線]](#server-endpoint-noactivity) 一節所列的步驟。

## <a name="sync"></a>Sync
<a id="afs-change-detection"></a>**如果我直接在我的 Azure 檔案共用中透過 SMB 建立檔案，或是透過入口網站建立檔案，要等多久檔案才會同步至同步群組中的伺服器？**  
[!INCLUDE [storage-sync-files-change-detection](../../../includes/storage-sync-files-change-detection.md)]

<a id="serverendpoint-pending"></a>**伺服器端點健康情況處於擱置狀態達數小時**  
如果您建立雲端端點並使用包含資料的 Azure 檔案共用，便會發生此問題。 在 Azure 檔案共用中掃描變更的變更列舉作業必須先完成，才能在雲端與伺服器端點之間同步檔案。 完成此作業的所需時間，取決於 Azure 檔案共用中的命名空間大小。 在變更列舉作業完成後，伺服器端點健康情況應會隨即更新。

### <a id="broken-sync"></a>如何監視同步健康情況？
# <a name="portaltabportal1"></a>[入口網站](#tab/portal1)
在每個同步群組內，您可以向下切入至其個別的伺服器端點，以查看上次完成的同步工作階段所處的狀態。 若 [健康情況] 資料行為綠色，且 [檔案無法同步] 的值為 0，表示同步運作正常。 若非如此，請查看下方的常見同步錯誤清單，並了解如何處理未同步的檔案。 

![Azure 入口網站的螢幕擷取畫面](media/storage-sync-files-troubleshoot/portal-sync-health.png)

# <a name="servertabserver"></a>[伺服器](#tab/server)
移至伺服器的遙測記錄 (位於事件檢視器的 `Applications and Services Logs\Microsoft\FileSync\Agent\Telemetry` 上)。 事件 9102 對應於已完成的同步工作階段；如需最新同步狀態，請尋找識別碼為 9102 的最新事件。 SyncDirection 會指出此工作階段是否已上傳或下載。 HResult 為 0，表示同步工作階段成功。 非零的 HResult 表示在同步期間發生錯誤；請查看下方的常見錯誤清單。 如果 PerItemErrorCount 大於 0，表示某些檔案或資料夾未正確同步。 HResult 為 0，但 PerItemErrorCount 大於 0，是有可能的。

以下是成功上傳的範例。 為了方便說明，以下僅列出每個 9102 事件所包含的部分值。 

```
Replica Sync session completed.
SyncDirection: Upload,
HResult: 0, 
SyncFileCount: 2, SyncDirectoryCount: 0,
AppliedFileCount: 2, AppliedDirCount: 0, AppliedTombstoneCount 0, AppliedSizeBytes: 0.
PerItemErrorCount: 0,
TransferredFiles: 2, TransferredBytes: 0, FailedToTransferFiles: 0, FailedToTransferBytes: 0.
```

相反地，不成功的上傳可能如下所示：

```
Replica Sync session completed.
SyncDirection: Upload,
HResult: -2134364065,
SyncFileCount: 0, SyncDirectoryCount: 0, 
AppliedFileCount: 0, AppliedDirCount: 0, AppliedTombstoneCount 0, AppliedSizeBytes: 0.
PerItemErrorCount: 0, 
TransferredFiles: 0, TransferredBytes: 0, FailedToTransferFiles: 0, FailedToTransferBytes: 0.
```

有時候，同步工作階段會完全失敗，或是有非零的 PerItemErrorCount 但仍繼續進行，而有部分檔案成功同步。 這可以從 [已套用]* 欄位中看出 (AppliedFileCount、AppliedDirCount、AppliedTombstoneCount 和 AppliedSizeBytes)，在此處您可以了解工作階段成功執行的程度。 如果您發現資料列中有多個同步工作階段雖然失敗，但 [已套用]* 計數卻逐漸增加，您即應在建立支援票證之前，保留足夠的時間來重新嘗試同步。

---

### <a name="how-do-i-monitor-the-progress-of-a-current-sync-session"></a>如何監視目前同步工作階段的進度？
# <a name="portaltabportal1"></a>[入口網站](#tab/portal1)
在您的同步群組中，移至有問題的伺服器端點，並查看 [同步活動] 區段以確認目前的同步工作階段中已上傳或下載的檔案計數。 請注意，此狀態大約會延遲 5 分鐘才顯示，因此如果同步工作階段較小而可在這段期間內完成，就可能不會報告在入口網站中。 

# <a name="servertabserver"></a>[伺服器](#tab/server)
在伺服器的遙測記錄中查看最新的 9302 事件 (在 [事件檢視器] 中，移至 Applications and Services Logs\Microsoft\FileSync\Agent\Telemetry)。 此事件表示目前同步工作階段的狀態。 TotalItemCount 代表要同步的檔案數目、AppliedItemCount 是目前已同步的檔案數目，而 PerItemErrorCount 則是無法同步的檔案數目 (請參閱下方的處置方式)。

```
Replica Sync Progress. 
ServerEndpointName: <CI>sename</CI>, SyncGroupName: <CI>sgname</CI>, ReplicaName: <CI>rname</CI>, 
SyncDirection: Upload, CorrelationId: {AB4BA07D-5B5C-461D-AAE6-4ED724762B65}. 
AppliedItemCount: 172473, TotalItemCount: 624196. AppliedBytes: 51473711577, 
TotalBytes: 293363829906. 
AreTotalCountsFinal: true. 
PerItemErrorCount: 1006.
```
---

### <a name="how-do-i-know-if-my-servers-are-in-sync-with-each-other"></a>如何得知我的伺服器是否彼此保持同步？
# <a name="portaltabportal1"></a>[入口網站](#tab/portal1)
針對給定同步群組中的每個伺服器，請確定：
- 上傳和下載的「上次嘗試的同步」時間戳記都是最新的。
- 上傳和下載的狀態都是綠色的。
- [同步活動] 欄位中僅顯示少許或沒有待同步的檔案。
- 上傳和下載的 [檔案無法同步] 欄位皆為 0。

# <a name="servertabserver"></a>[伺服器](#tab/server)
查看已完成的同步工作階段，這在每個伺服器的遙測事件記錄中會以 9102 事件標示 (在 [事件檢視器] 中，移至 `Applications and Services Logs\Microsoft\FileSync\Agent\Telemetry`)。 

1. 在任何給定的伺服器上，您都會想要確定最新的上傳和下載工作階段已順利完成。 若要這樣做，請確認上傳和下載的 HResult 和 PerItemErrorCount 皆為 0 (SyncDirection 欄位會指出給定的工作階段是上傳還是下載工作階段)。 請注意，若未看到最近完成的同步工作階段，有可能是同步工作階段正在進行中，這是您剛剛新增或修改大量資料之後的正常情況。
2. 如果伺服器與雲端之間的狀態是最新的，且雙向都沒有需要同步的變更，您將會看到空的同步工作階段。 如果上傳和下載中的所有 [同步]* 欄位 (SyncFileCount、SyncDirCount、SyncTombstoneCount 和 SyncSizeBytes) 皆為零 (表示沒有可同步的項目)，即表示此狀態。請注意，這些空的同步工作階段不會出現在高變換伺服器上，因為此處總是會有需要同步的新項目。如果沒有同步活動，此類工作階段應該每 30 分鐘出現一次。 
3. 如果所有伺服器與雲端之間的狀態都是最新的，這表示其最近的上傳和下載工作階段都是空的同步工作階段，而您可以合理斷定整體系統皆已同步。 
    
請注意，如果您直接在 Azure 檔案共用中進行變更，在每 24 小時發生一次的變更列舉執行之前，Azure 檔案同步將不會偵測到此變更。 當伺服器實際上遺漏直接在 Azure 檔案共用中所做的最新變更時，它仍有可能認定本身與雲端之間的狀態是最新的。 

---

### <a name="how-do-i-see-if-there-are-specific-files-or-folders-that-are-not-syncing"></a>如何確認是否有特定的檔案或資料夾並未同步？
就任何給定的同步工作階段而言，如果伺服器上的 PerItemErrorCount 或入口網站中的「檔案無法同步」計數大於 0，則表示有部分項目無法同步。檔案和資料夾可能有某些特性使其無法同步。 特性可能是持續性的，且您必須執行明確的動作才能繼續同步，例如，從檔案或資料夾名稱中移除不支援的字元。 特性也有可能是暫時性的，這表示檔案或資料夾將會自動繼續同步；例如，具有開啟控制代碼的檔案，將會檔案關閉時自動繼續同步。 當 Azure 檔案同步引擎偵測到此類問題時，將會產生錯誤記錄，而此記錄可經由剖析列出目前未正確同步的項目。

若要查看這些錯誤，請執行 **FileSyncErrorsReport.ps1** PowerShell 指令碼 (位於 Azure 檔案同步代理程式的代理程式安裝目錄中)，以識別因開啟的控制代碼、不支援的字元或其他問題而無法同步的檔案。 ItemPath 欄位會指出檔案與根同步目錄的相對位置。 請參閱下方的常見同步錯誤清單，以取得補救步驟。

#### <a name="troubleshooting-per-filedirectory-sync-errors"></a>個別檔案/目錄同步錯誤的疑難排解
**ItemResults 記錄 - 個別項目同步錯誤**  

| HRESULT | HRESULT (十進位) | 錯誤字串 | 問題 | 補救 |
|---------|-------------------|--------------|-------|-------------|
| 0x80c80207 | -2134375929 | ECS_E_SYNC_CONSTRAINT_CONFLICT | 尚無法同步某個檔案或目錄變更，因為尚未同步相依的資料夾。 將在同步相依的變更之後同步此項目。 | 不需要任何動作。 |
| 0x8007007b | -2147024773 | ERROR_INVALID_NAME | 檔案或目錄名稱無效。 | 請重新命名有問題的檔案或目錄。 如需詳細資訊，請參閱[處理不支援的字元](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#handling-unsupported-characters)。 |
| 0x80c80018 | -2134376424 | ECS_E_SYNC_FILE_IN_USE | 檔案正在使用中，無法同步。 檔案不再處於使用中狀態時即會同步。 | 不需要任何動作。 Azure 檔案同步每天會在伺服器上建立一個暫時的 VSS 快照集，用以同步具有開啟控制代碼的檔案。 |
| 0x80c8031d | -2134375651 | ECS_E_CONCURRENCY_CHECK_FAILED | 檔案已變更，但同步尚未偵測到變更。偵測到這項變更之後，即會恢復同步作業。 | 不需要任何動作。 |
| 0x80c8603e | -2134351810 | ECS_E_AZURE_STORAGE_SHARE_SIZE_LIMIT_REACHED | 無法同步檔案，因為已達 Azure 檔案共用限制。 | 若要解決此問題，請參閱疑難排解指南中的[您已達到 Azure 檔案共用儲存體限制](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#-2134351810)一節。 |
| 0x80070005 | -2147024891 | E_ACCESSDENIED | 基於下列原因可能會發生此錯誤： 不支援的解決方案 （例如 NTFS EFS) 加密檔案、 檔案有暫止狀態的刪除或檔案位在 DFS-R 唯讀複寫資料夾 | 如果檔案已加密不支援的解決方案，解密檔案，並使用支援的加密解決方案。 如需支援解決方案的清單，請參閱計劃指南中的[加密解決方案](https://docs.microsoft.com/azure/storage/files/storage-sync-files-planning#encryption-solutions)一節。 如果檔案處於刪除擱置狀態，則會在關閉所有開啟檔案控制代碼後，刪除該檔案。 如果該檔案位於 DFS-R 唯讀複寫資料夾中，Azure 檔案同步不支援伺服器端點上 DFS-R 唯讀複寫資料夾。 請參閱[規劃指南](https://docs.microsoft.com/azure/storage/files/storage-sync-files-planning#distributed-file-system-dfs)如需詳細資訊。
| 0x80070020 | -2147024864 | ERROR_SHARING_VIOLATION | 檔案正在使用中，無法同步。 檔案不再處於使用中狀態時即會同步。 | 不需要任何動作。 |
| 0x80c80017 | -2134376425 | ECS_E_SYNC_OPLOCK_BROKEN | 檔案在同步期間有所變更，因此需要再次同步。 | 不需要任何動作。 |

#### <a name="handling-unsupported-characters"></a>處理不支援的字元
如果**FileSyncErrorsReport.ps1** PowerShell 指令碼會示範不支援的字元 （錯誤碼 0x8007007b） 所造成的失敗，您應該移除或重新命名錯誤從個別的檔案名稱的字元。 由於這些字元大多沒有標準的視覺編碼，PowerShell 可能會將這些字元列印為問號或空的矩形。 [評估工具](storage-sync-files-planning.md#evaluation-tool)可用來識別不受支援的字元。

下表列出 Azure 檔案同步尚不支援的所有 Unicode 字元。

| 字元集 | 字元計數 |
|---------------|-----------------|
| <ul><li>0x0000009D (osc 作業系統命令)</li><li>0x00000090 (dcs 裝置控制字串)</li><li>0x0000008F (ss3 單一移位三)</li><li>0x00000081 (高八位元組前置)</li><li>0x0000007F (del 刪除)</li><li>0x0000008D (ri 反向換行)</li></ul> | 6 |
| 0x0000FDD0 - 0x0000FDEF (阿拉伯文表現形式-a) | 32 |
| 0x0000FFF0 - 0x0000FFFF (特殊符號) | 16 |
| <ul><li>0x0001FFFE - 0x0001FFFF = 2 (非字元)</li><li>0x0002FFFE - 0x0002FFFF = 2 (非字元)</li><li>0x0003FFFE - 0x0003FFFF = 2 (非字元)</li><li>0x0004FFFE - 0x0004FFFF = 2 (非字元)</li><li>0x0005FFFE - 0x0005FFFF = 2 (非字元)</li><li>0x0006FFFE - 0x0006FFFF = 2 (非字元)</li><li>0x0007FFFE - 0x0007FFFF = 2 (非字元)</li><li>0x0008FFFE - 0x0008FFFF = 2 (非字元)</li><li>0x0009FFFE - 0x0009FFFF = 2 (非字元)</li><li>0x000AFFFE - 0x000AFFFF = 2 (非字元)</li><li>0x000BFFFE - 0x000BFFFF = 2 (非字元)</li><li>0x000CFFFE - 0x000CFFFF = 2 (非字元)</li><li>0x000DFFFE - 0x000DFFFF = 2 (非字元)</li><li>0x000EFFFE - 0x000EFFFF = 2 (未定義)</li><li>0x000FFFFE - 0x000FFFFF = 2 (補充專用區)</li></ul> | 30 |
| 0x0010FFFE、0x0010FFFF | 2 |

### <a name="common-sync-errors"></a>常見同步錯誤
<a id="-2147023673"></a>**同步工作階段已取消。**  

| | |
|-|-|
| **HRESULT** | 0x800704c7 |
| **HRESULT (十進位)** | -2147023673 | 
| **錯誤字串** | ERROR_CANCELLED |
| **需要補救** | 否 |

同步工作階段可能因多種不同原因而失敗，包括伺服器正在重新啟動或更新、VSS 快照集等。雖然此錯誤看似需要後續追蹤，但除非錯誤持續存在達數小時，否則皆可安全地加以忽略。

<a id="-2147012889"></a>**無法建立與此服務的連線。**    

| | |
|-|-|
| **HRESULT** | 0x80072ee7 |
| **HRESULT (十進位)** | -2147012889 | 
| **錯誤字串** | WININET_E_NAME_NOT_RESOLVED |
| **需要補救** | 是 |

[!INCLUDE [storage-sync-files-bad-connection](../../../includes/storage-sync-files-bad-connection.md)]

<a id="-2134376372"></a>**使用者要求已由服務進行節流。**  

| | |
|-|-|
| **HRESULT** | 0x80c8004c |
| **HRESULT (十進位)** | -2134376372 |
| **錯誤字串** | ECS_E_USER_REQUEST_THROTTLED |
| **需要補救** | 否 |

不需要任何動作；伺服器會再試一次。 如果此錯誤持續存在超過兩小時，請建立支援要求。

<a id="-2134364043"></a>**同步處理會被封鎖，直到還原後的變更偵測完成**  

| | |
|-|-|
| **HRESULT** | 0x80c83075 |
| **HRESULT (十進位)** | -2134364043 |
| **錯誤字串** | ECS_E_SYNC_BLOCKED_ON_CHANGE_DETECTION_POST_RESTORE |
| **需要補救** | 否 |

不需採取任何動作。 當檔案共用 （雲端端點） 會使用 Azure 備份來還原、 同步處理會被封鎖，直到變更偵測完成 Azure 檔案共用上。 變更偵測會在還原完成，且持續時間為基礎的檔案共用中的檔案數目之後，立即執行。

<a id="-2134364065"></a>**同步作業無法存取雲端端點中指定的 Azure 檔案共用。**  

| | |
|-|-|
| **HRESULT** | 0x80c8305f |
| **HRESULT (十進位)** | -2134364065 |
| **錯誤字串** | ECS_E_CANNOT_ACCESS_EXTERNAL_STORAGE_ACCOUNT |
| **需要補救** | 是 |

之所以發生此錯誤，是因為 Azure 檔案同步代理程式無法存取 Azure 檔案共用，而這可能是因為 Azure 檔案共用或加以裝載的儲存體帳戶已不存在。 您可以透過下列步驟，對此錯誤進行疑難排解：

1. [確認儲存體帳戶確實存在。](#troubleshoot-storage-account)
2. [檢查並確定儲存體帳戶未包含任何網路規則。](#troubleshoot-network-rules)
3. [確定 Azure 檔案共用確實存在。](#troubleshoot-azure-file-share)
4. [確定 Azure 檔案同步具有儲存體帳戶的存取權。](#troubleshoot-rbac)

<a id="-2134364064"></a><a id="cannot-resolve-storage"></a>**無法解析使用的儲存體帳戶名稱。**  

| | |
|-|-|
| **HRESULT** | 0x80C83060 |
| **HRESULT (十進位)** | -2134364064 |
| **錯誤字串** | ECS_E_STORAGE_ACCOUNT_NAME_UNRESOLVED |
| **需要補救** | 是 |

1. 確認您可以解析來自伺服器的儲存體 DNS 名稱。

    ```powershell
    Test-NetConnection -ComputerName <storage-account-name>.file.core.windows.net -Port 443
    ```
2. [確認儲存體帳戶確實存在。](#troubleshoot-storage-account)
3. [檢查並確定儲存體帳戶未包含任何網路規則。](#troubleshoot-network-rules)

<a id="-1906441138"></a>**同步因同步資料庫發生問題而失敗。**  

| | |
|-|-|
| **HRESULT** | 0x8e5e044e |
| **HRESULT (十進位)** | -1906441138 |
| **錯誤字串** | JET_errWriteConflict |
| **需要補救** | 是 |

當 Azure 檔案同步所使用的內部資料庫有問題時，就會發生此錯誤。發生此問題時，請建立支援要求，我們會與您連絡，協助您解決這個問題。

<a id="-2134364053"></a>**不支援安裝在伺服器上的 Azure 檔案同步代理程式版本。**  

| | |
|-|-|
| **HRESULT** | 0x80C8306B |
| **HRESULT (十進位)** | -2134364053 |
| **錯誤字串** | ECS_E_AGENT_VERSION_BLOCKED |
| **需要補救** | 是 |

如果不支援安裝在伺服器上的 Azure 檔案同步代理程式版本，就會發生此錯誤。 若要解決此問題，請[升級]( https://docs.microsoft.com/azure/storage/files/storage-files-release-notes#upgrade-paths)至[支援的代理程式版本]( https://docs.microsoft.com/azure/storage/files/storage-files-release-notes#supported-versions)。

<a id="-2134351810"></a>**已達到 Azure 檔案共用儲存空間限制。**  

| | |
|-|-|
| **HRESULT** | 0x80c8603e |
| **HRESULT (十進位)** | -2134351810 |
| **錯誤字串** | ECS_E_AZURE_STORAGE_SHARE_SIZE_LIMIT_REACHED |
| **需要補救** | 是 |

在達到 Azure 檔案共用儲存空間限制時 (可能在 Azure 檔案共用套用了配額，或 Azure 檔案共用的使用量超出限制時發生)，就會發生此錯誤。 如需詳細資訊，請參閱 [Azure 檔案共用目前的限制](storage-files-scale-targets.md)。

1. 瀏覽至儲存體同步服務中的同步群組。
2. 選取同步群組內的雲端端點。
3. 記下開啟的窗格中顯示的 Azure 檔案共用名稱。
4. 選取連結的儲存體帳戶。 如果此連結失敗，表示參考的儲存體帳戶已移除。

    ![此螢幕擷取畫面顯示有連結通往儲存體帳戶的雲端端點詳細資料窗格。](media/storage-sync-files-troubleshoot/file-share-inaccessible-1.png)

5. 選取 [檔案]  ，以檢視檔案共用清單。
6. 按一下資料列結尾處的三個點，以顯示雲端端點所參考的 Azure 檔案共用。
7. 確認 [使用量]  低於 [配額]  。 請注意，除非已指定替代配額，否則配額將會符合 [Azure 檔案共用的大小上限](storage-files-scale-targets.md)。

    ![Azure 檔案共用屬性的螢幕擷取畫面。](media/storage-sync-files-troubleshoot/file-share-limit-reached-1.png)

如果共用已滿，且未設定配額，修正此問題的可行方式之一，是使目前伺服器端點的每個子資料夾，成為其各自所屬同步群組內的專屬伺服器端點。 如此一來，每個子資料夾將會同步至個別的 Azure 檔案共用。

<a id="-2134351824"></a>**找不到 Azure 檔案共用。**  

| | |
|-|-|
| **HRESULT** | 0x80c86030 |
| **HRESULT (十進位)** | -2134351824 |
| **錯誤字串** | ECS_E_AZURE_FILE_SHARE_NOT_FOUND |
| **需要補救** | 是 |

無法存取 Azure 檔案共用時，就會發生此錯誤。 若要進行疑難排解：

1. [確認儲存體帳戶確實存在。](#troubleshoot-storage-account)
2. [確定 Azure 檔案共用確實存在。](#troubleshoot-azure-file-share)

如果 Azure 檔案共用已刪除，您必須建立新的檔案共用，並重新建立同步群組。 

<a id="-2134364042"></a>**此 Azure 訂用帳戶暫止期間，會暫停同步。**  

| | |
|-|-|
| **HRESULT** | 0x80C83076 |
| **HRESULT (十進位)** | -2134364042 |
| **錯誤字串** | ECS_E_SYNC_BLOCKED_ON_SUSPENDED_SUBSCRIPTION |
| **需要補救** | 是 |

當 Azure 訂用帳戶暫止時，就會發生此錯誤。 同步將在 Azure 訂用帳戶恢復時重新啟用。 如需詳細資訊，請參閱[我的 Azure 訂用帳戶為何停用，以及如何重新啟動它？](../../billing/billing-subscription-become-disable.md)。

<a id="-2134364052"></a>**儲存體帳戶已設定了防火牆或虛擬網路。**  

| | |
|-|-|
| **HRESULT** | 0x80c8306c |
| **HRESULT (十進位)** | -2134364052 |
| **錯誤字串** | ECS_E_MGMT_STORAGEACLSNOTSUPPORTED |
| **需要補救** | 是 |

因為儲存體帳戶有防火牆，或因為儲存體帳戶屬於虛擬網路，而無法存取 Azure 檔案共用時，就會發生此錯誤。 Azure 檔案同步尚不支援這項功能。 若要進行疑難排解：

1. [確認儲存體帳戶確實存在。](#troubleshoot-storage-account)
2. [檢查並確定儲存體帳戶未包含任何網路規則。](#troubleshoot-network-rules)

請移除這些規則以修正此問題。 

<a id="-2134375911"></a>**同步因同步資料庫發生問題而失敗。**  

| | |
|-|-|
| **HRESULT** | 0x80c80219 |
| **HRESULT (十進位)** | -2134375911 |
| **錯誤字串** | ECS_E_SYNC_METADATA_WRITE_LOCK_TIMEOUT |
| **需要補救** | 否 |

此錯誤通常會自行解決；若有下列情況，即可能發生：

* 在同步群組的多個伺服器之間發生大量檔案變更。
* 在個別檔案和目錄上發生大量錯誤。

如果此錯誤持續存在超過數小時，請建立支援要求，我們會與您連絡，協助您解決這個問題。

<a id="-2146762487"></a>**伺服器無法建立安全連線。雲端服務接收到非預期的憑證。**  

| | |
|-|-|
| **HRESULT** | 0x800b0109 |
| **HRESULT (十進位)** | -2146762487 |
| **錯誤字串** | CERT_E_UNTRUSTEDROOT |
| **需要補救** | 是 |

如果您的組織目前使用 SSL 終止 Proxy 或惡意實體正在攔截您的伺服器與 Azure 檔案同步服務之間的流量，即可能發生此錯誤。 若您確定這是預期中的情況 (因為您的組織使用 SSL 終止 Proxy)，您可以透過登錄覆寫略過憑證驗證。

1. 建立 SkipVerifyingPinnedRootCertificate 登錄值。

    ```powershell
    New-ItemProperty -Path HKLM:\SOFTWARE\Microsoft\Azure\StorageSync -Name SkipVerifyingPinnedRootCertificate -PropertyType DWORD -Value 1
    ```

2. 在已註冊的伺服器上重新啟動同步服務。

    ```powershell
    Restart-Service -Name FileSyncSvc -Force
    ```

設定此登錄值之後，在伺服器與雲端服務之間傳送資料時，Azure 檔案同步代理程式即會接受所有本機信任的 SSL 憑證。

<a id="-2147012894"></a>**無法建立與此服務的連線。**  

| | |
|-|-|
| **HRESULT** | 0x80072ee2 |
| **HRESULT (十進位)** | -2147012894 |
| **錯誤字串** | WININET_E_TIMEOUT |
| **需要補救** | 是 |

[!INCLUDE [storage-sync-files-bad-connection](../../../includes/storage-sync-files-bad-connection.md)]

<a id="-2134375680"></a>**同步因驗證有問題而失敗。**  

| | |
|-|-|
| **HRESULT** | 0x80c80300 |
| **HRESULT (十進位)** | -2134375680 |
| **錯誤字串** | ECS_E_SERVER_CREDENTIAL_NEEDED |
| **需要補救** | 是 |

造成此錯誤的原因可能是：

- 伺服器時間不正確
- 伺服器端點刪除失敗
- 用於驗證的憑證已過期。 
    若要檢查憑證是否已過期，請執行下列步驟：  
    1. 開啟 [憑證 MMC 嵌入式管理單元]，選取 [電腦帳戶]，並瀏覽至 [憑證 (本機電腦)\個人\憑證]。
    2. 檢查用戶端驗證憑證是否已過期。

如果伺服器時間正確無誤，請執行下列步驟來解決問題：

1. 確認已安裝 Azure 檔案同步代理程式版本 4.0.1.0 或更新版本。
2. 在伺服器上執行下列 PowerShell 命令：

    ```powershell
    Reset-AzStorageSyncServerCertificate -ResourceGroupName <string> -StorageSyncServiceName <string>
    ```

<a id="-1906441711"></a><a id="-2134375654"></a><a id="doesnt-have-enough-free-space"></a>**伺服器端點所在的磁碟區沒有足夠的磁碟空間。**  

| | |
|-|-|
| **HRESULT** | 0x8e5e0211 |
| **HRESULT (十進位)** | -1906441711 |
| **錯誤字串** | JET_errLogDiskFull |
| **需要補救** | 是 |
| | |
| **HRESULT** | 0x80c8031a |
| **HRESULT (十進位)** | -2134375654 |
| **錯誤字串** | ECS_E_NOT_ENOUGH_LOCAL_STORAGE |
| **需要補救** | 是 |

發生此錯誤的原因是磁碟區已滿。 之所以發生此錯誤，常是因為伺服器端點以外的檔案即將用盡磁碟區的空間。 請新增額外的伺服器端點、將檔案移至不同的磁碟區，或增加伺服器端點所在磁碟區的大小，以釋出磁碟區的空間。

<a id="-2134364145"></a><a id="replica-not-ready"></a>**服務尚未準備好要與此伺服器端點同步。**  

| | |
|-|-|
| **HRESULT** | 0x80c8300f |
| **HRESULT (十進位)** | -2134364145 |
| **錯誤字串** | ECS_E_REPLICA_NOT_READY |
| **需要補救** | 否 |

之所以發生此錯誤，是因為 Azure 檔案共用有直接的變更，且變更偵測正在進行中。 變更偵測完成後，就會開始進行同步。

[!INCLUDE [storage-sync-files-change-detection](../../../includes/storage-sync-files-change-detection.md)]

<a id="-2134375877"></a><a id="-2134375908"></a><a id="-2134375853"></a>**同步因許多個別檔案有問題而失敗。**  

| | |
|-|-|
| **HRESULT** | 0x80c8023b |
| **HRESULT (十進位)** | -2134364145 |
| **錯誤字串** | ECS_E_SYNC_METADATA_KNOWLEDGE_SOFT_LIMIT_REACHED |
| **需要補救** | 是 |
| | |
| **HRESULT** | 0x80c8021c |
| **HRESULT (十進位)** | -2134375908 |
| **錯誤字串** | ECS_E_SYNC_METADATA_KNOWLEDGE_LIMIT_REACHED |
| **需要補救** | 是 |
| | |
| **HRESULT** | 0x80c80253 |
| **HRESULT (十進位)** | -2134375853 |
| **錯誤字串** | ECS_E_TOO_MANY_PER_ITEM_ERRORS |
| **需要補救** | 是 |

如果有許多個別檔案同步錯誤，同步工作階段就可能開始失敗。 <!-- To troubleshoot this state, see [Troubleshooting per file/directory sync errors]().-->

> [!NOTE]
> Azure 檔案同步每天會在伺服器上建立一個暫時的 VSS 快照集，用以同步具有開啟控制代碼的檔案。

<a id="-2134376423"></a>**同步因伺服器端點路徑有問題而失敗。**  

| | |
|-|-|
| **HRESULT** | 0x80c80019 |
| **HRESULT (十進位)** | -2134376423 |
| **錯誤字串** | ECS_E_SYNC_INVALID_PATH |
| **需要補救** | 是 |

請確定路徑存在、位於本機 NTFS 磁碟區尚，且不是重新分析點或現有的伺服器端點。

<a id="-2134375817"></a>**同步處理失敗，因為篩選器驅動程式版本與代理程式版本不相容**  

| | |
|-|-|
| **HRESULT** | 0x80C80277 |
| **HRESULT (十進位)** | -2134375817 |
| **錯誤字串** | ECS_E_INCOMPATIBLE_FILTER_VERSION |
| **需要補救** | 是 |

發生此錯誤的原因是，載入的雲端階層處理篩選器驅動程式 (StorageSync.sys) 版本與儲存體同步代理程式 (FileSyncSvc) 服務不相容。 如果 Azure 檔案同步代理程式已升級，請重新啟動伺服器以完成安裝。 如果錯誤持續發生，請解除安裝代理程式、重新啟動伺服器，並重新安裝 Azure 檔案同步代理程式。

<a id="-2134376373"></a>**服務目前無法使用。**  

| | |
|-|-|
| **HRESULT** | 0x80c8004b |
| **HRESULT (十進位)** | -2134376373 |
| **錯誤字串** | ECS_E_SERVICE_UNAVAILABLE |
| **需要補救** | 否 |

之所以發生此錯誤，是因為 Azure 檔案同步服務無法使用。 此錯誤會在 Azure 檔案同步服務恢復使用時自動解決。

<a id="-2134375922"></a>**同步因同步資料庫發生暫時性問題而失敗。**  

| | |
|-|-|
| **HRESULT** | 0x80c8020e |
| **HRESULT (十進位)** | -2134375922 |
| **錯誤字串** | ECS_E_SYNC_METADATA_WRITE_LEASE_LOST |
| **需要補救** | 否 |

之所以發生此錯誤，是因為同步資料庫發生內部錯誤。 此錯誤會在 Azure 檔案同步重試時自動解決。 如果此錯誤持續存在一段時間，請建立支援要求，我們會與您連絡，協助您解決這個問題。

### <a name="common-troubleshooting-steps"></a>常用的疑難排解步驟
<a id="troubleshoot-storage-account"></a>**確認儲存體帳戶確實存在。**  
# <a name="portaltabazure-portal"></a>[入口網站](#tab/azure-portal)
1. 瀏覽至儲存體同步服務中的同步群組。
2. 選取同步群組內的雲端端點。
3. 記下開啟的窗格中顯示的 Azure 檔案共用名稱。
4. 選取連結的儲存體帳戶。 如果此連結失敗，表示參考的儲存體帳戶已移除。
    ![此螢幕擷取畫面顯示有連結通往儲存體帳戶的雲端端點詳細資料窗格。](media/storage-sync-files-troubleshoot/file-share-inaccessible-1.png)

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)
```powershell
# Variables for you to populate based on your configuration
$region = "<Az_Region>"
$resourceGroup = "<RG_Name>"
$syncService = "<storage-sync-service>"
$syncGroup = "<sync-group>"

# Log into the Azure account
Connect-AzAccount

# Check to ensure Azure File Sync is available in the selected Azure
# region.
$regions = [System.String[]]@()
Get-AzLocation | ForEach-Object { 
    if ($_.Providers -contains "Microsoft.StorageSync") { 
        $regions += $_.Location 
    } 
}

if ($regions -notcontains $region) {
    throw [System.Exception]::new("Azure File Sync is either not available in the " + `
        " selected Azure Region or the region is mistyped.")
}

# Check to ensure resource group exists
$resourceGroups = [System.String[]]@()
Get-AzResourceGroup | ForEach-Object { 
    $resourceGroups += $_.ResourceGroupName 
}

if ($resourceGroups -notcontains $resourceGroup) {
    throw [System.Exception]::new("The provided resource group $resourceGroup does not exist.")
}

# Check to make sure the provided Storage Sync Service
# exists.
$syncServices = [System.String[]]@()

Get-AzStorageSyncService -ResourceGroupName $resourceGroup | ForEach-Object {
    $syncServices += $_.StorageSyncServiceName
}

if ($syncServices -notcontains $syncService) {
    throw [System.Exception]::new("The provided Storage Sync Service $syncService does not exist.")
}

# Check to make sure the provided Sync Group exists
$syncGroups = [System.String[]]@()

Get-AzStorageSyncGroup -ResourceGroupName $resourceGroup -StorageSyncServiceName $syncService | ForEach-Object {
    $syncGroups += $_.SyncGroupName
}

if ($syncGroups -notcontains $syncGroup) {
    throw [System.Exception]::new("The provided sync group $syncGroup does not exist.")
}

# Get reference to cloud endpoint
$cloudEndpoint = Get-AzStorageSyncCloudEndpoint `
    -ResourceGroupName $resourceGroup `
    -StorageSyncServiceName $syncService `
    -SyncGroupName $syncGroup

# Get reference to storage account
$storageAccount = Get-AzStorageAccount | Where-Object { 
    $_.Id -eq $cloudEndpoint.StorageAccountResourceId
}

if ($storageAccount -eq $null) {
    throw [System.Exception]::new("The storage account referenced in the cloud endpoint does not exist.")
}
```
---

<a id="troubleshoot-network-rules"></a>**檢查並確定儲存體帳戶未包含任何網路規則。**  
# <a name="portaltabazure-portal"></a>[入口網站](#tab/azure-portal)
1. 在進入儲存體帳戶後，選取位於儲存體帳戶左側的 [防火牆和虛擬網路]  。
2. 在儲存體帳戶中，應選取 [允許從所有網路存取]  選項按鈕。
    ![顯示已停用儲存體帳戶防火牆和網路規則的螢幕擷取畫面。](media/storage-sync-files-troubleshoot/file-share-inaccessible-2.png)

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)
```powershell
if ($storageAccount.NetworkRuleSet.DefaultAction -ne 
    [Microsoft.Azure.Commands.Management.Storage.Models.PSNetWorkRuleDefaultActionEnum]::Allow) {
    throw [System.Exception]::new("The storage account referenced contains network " + `
        "rules which are not currently supported by Azure File Sync.")
}
```
---

<a id="troubleshoot-azure-file-share"></a>**確定 Azure 檔案共用確實存在。**  
# <a name="portaltabazure-portal"></a>[入口網站](#tab/azure-portal)
1. 按一下位於目錄左側的 [概觀]  ，以返回主要儲存體帳戶頁面。
2. 選取 [檔案]  ，以檢視檔案共用清單。
3. 確認雲端端點所參考的檔案共用出現在檔案共用清單中 (您應已先前的步驟 1 中記下這項資料)。

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)
```powershell
$fileShare = Get-AzStorageShare -Context $storageAccount.Context | Where-Object {
    $_.Name -eq $cloudEndpoint.AzureFileShareName -and
    $_.IsSnapshot -eq $false
}

if ($fileShare -eq $null) {
    throw [System.Exception]::new("The Azure file share referenced by the cloud endpoint does not exist")
}
```
---

<a id="troubleshoot-rbac"></a>**確定 Azure 檔案同步具有儲存體帳戶的存取權。**  
# <a name="portaltabazure-portal"></a>[入口網站](#tab/azure-portal)
1. 按一下左側目錄的 [存取控制 (IAM)]  。
1. 按一下 [角色指派]  索引標籤，列出可存取儲存體帳戶的使用者和應用程式 (*服務主體*)。
1. 確認 [混合式檔案同步服務]  連同 [讀取者及資料存取]  角色出現在清單中。 

    ![混合式檔案同步服務的服務主體，在儲存體帳戶的存取控制 索引標籤的螢幕擷取畫面](media/storage-sync-files-troubleshoot/file-share-inaccessible-3.png)

    如果清單中未顯示 [混合式檔案同步服務]  ，請執行下列步驟：

    - 按一下 [新增]  。
    - 在 [角色]  欄位中，選取 [讀取者及資料存取]  。
    - 在 [選取]  欄位中，輸入 [混合式檔案同步服務]  ，選取角色並按一下 [儲存]  。

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)
```powershell    
$role = Get-AzRoleAssignment -Scope $storageAccount.Id | Where-Object { $_.DisplayName -eq "Hybrid File Sync Service" }

if ($role -eq $null) {
    throw [System.Exception]::new("The storage account does not have the Azure File Sync " + `
                "service principal authorized to access the data within the " + ` 
                "referenced Azure file share.")
}
```
---

### <a name="how-do-i-prevent-users-from-creating-files-containing-unsupported-characters-on-the-server"></a>如何防止使用者在伺服器上建立所含的字元不受支援的檔案？
您可以使用[檔案伺服器資源管理員 (FSRM) 的檔案檢測](https://docs.microsoft.com/windows-server/storage/fsrm/file-screening-management)，阻止檔案名稱中包含非支援字元的檔案建立於伺服器上。 您可能必須使用 PowerShell 來執行此作業，因為不支援的字元大多是無法列印的，因此您必須先將其十六進位表示法轉換為字元。

請先使用 [New-FsrmFileGroup Cmdlet](https://docs.microsoft.com/powershell/module/fileserverresourcemanager/new-fsrmfilegroup) 建立 FSRM 檔案群組。 此範例會定義僅包含兩個非支援字元的群組，但您可以視需要在檔案群組中包含任意數目的字元。

```powershell
New-FsrmFileGroup -Name "Unsupported characters" -IncludePattern @(("*"+[char]0x00000090+"*"),("*"+[char]0x0000008F+"*"))
```

在定義 FSRM 檔案群組後，您可以使用 New-FsrmFileScreen Cmdlet 建立 FSRM 檔案檢測。

```powershell
New-FsrmFileScreen -Path "E:\AFSdataset" -Description "Filter unsupported characters" -IncludeGroup "Unsupported characters"
```

> [!Important]  
> 請注意，檔案檢測應僅用來防止建立 Azure 檔案同步不支援的字元。如果將檔案檢測用於其他案例，同步作業仍會持續嘗試從 Azure 檔案共用將檔案下載至伺服器，並因為檔案檢測而遭到封鎖，導致大量資料輸出。 

## <a name="cloud-tiering"></a>雲端階層處理 
在雲端階層中有以下兩種失敗：

- 檔案無法分層，表示 Azure 檔案同步嘗試將檔案分層至 Azure 檔案但不成功。
- 檔案無法回收，表示當使用者嘗試存取已分層的檔案時，Azure 檔案同步檔案系統篩選器 (StorageSync.sys) 無法下載資料。

上述任一種失敗會導致兩種主要類型的失敗：

- 雲端儲存體失敗
    -  暫時性儲存體服務可用性問題。 如需詳細資訊，請參閱 [Azure 儲存體服務等級協定 (SLA)](https://azure.microsoft.com/support/legal/sla/storage/v1_2/)。
    -  無法存取 Azure 檔案共用。 此失敗通常發生在您刪除的 Azure 檔案共用仍是同步群組中的雲端端點時。
    -  無法存取儲存體帳戶。 當您刪除的儲存體帳戶仍有 Azure 檔案共用是同步處理群組中的雲端端點時，通常會發生此失敗。 
- 伺服器失敗 
  -  未載入 Azure 檔案同步處理檔案系統篩選器 (StorageSync.sys)。 一定要載入 Azure 檔案同步處理檔案系統篩選器，才能回應分層/回收要求。 發生無法載入篩選器的情況可能有幾個原因，但最常見的是系統管理員手動將它卸載。 Azure 檔案同步處理檔案系統篩選器不論何時都必須保持載入，Azure 檔案同步才能正常運作。
  -  遺失、損毀或中斷的重新剖析點。 重新剖析點是檔案上的特殊資料結構，由以下兩部分所組成：
    1. 重新剖析標記。它會向作業系統指示，Azure 檔案同步處理檔案系統篩選器 (StorageSync.sys) 可能需要對檔案的 IO 採取一些動作。 
    2. 重新剖析資料。它會向檔案系統篩選器指示相關聯雲端端點 (Azure 檔案共用) 上檔案的 URI。 
        
       造成重新剖析點損毀的最常見狀況是系統管理員嘗試修改標記或其資料。 
  -  網路連線問題。 為了分層或回收檔案，伺服器必須有網際網路連線。

下列各節將說明如何針對雲端階層問題進行疑難排解，以及如何判別雲端儲存體問題或伺服器問題。

<a id="monitor-tiering-activity"></a>**如何監視伺服器上的分層活動**  
若要監視伺服器上的分層活動，請在遙測事件記錄中使用事件識別碼 9003、9016 和 9029 (位於事件檢視器中的 Applications and Services\Microsoft\FileSync\Agent 下)。

- 事件識別碼 9003 會提供伺服器端點的錯誤分布。 例如錯誤總數、錯誤碼等。請注意，系統會為每個錯誤碼記錄一個事件。
- 事件識別碼 9016 會提供磁碟區的建立映像結果。 例如可用空間百分比、在工作階段中建立映像的檔案數目、無法建立映像的檔案數目等。
- 事件識別碼 9029 會提供伺服器端點的虛像化工作階段資訊。 例如，在工作階段中嘗試的檔案數目、在工作階段中分層的檔案數目、已分層的檔案數目等。

<a id="monitor-recall-activity"></a>**如何監視伺服器上的回收活動**  
若要監視伺服器上的回收活動，請在遙測事件記錄中使用事件識別碼 9005、9006、9009 和 9059 (位於事件檢視器中的 Applications and Services\Microsoft\FileSync\Agent 下)。

- 事件識別碼 9005 會提供伺服器端點的回收可靠性。 例如，存取的唯一檔案總數、存取失敗的唯一檔案總數等。
- 事件識別碼 9006 會提供伺服器端點的回收錯誤分布。 例如失敗的要求總數、錯誤碼等。請注意，系統會為每個錯誤碼記錄一個事件。
- 事件識別碼 9009 會提供伺服器端點的回收工作階段資訊。 例如，DurationSeconds、CountFilesRecallSucceeded、CountFilesRecallFailed 等。
- 事件識別碼 9059 會提供伺服器端點的應用程式回收分布。 例如，ShareId、應用程式名稱和 TotalEgressNetworkBytes。

<a id="files-fail-tiering"></a>**針對檔案無法分層的問題進行疑難排解**  
如果無法在 Azure 檔案中將檔案分層：

1. 在 [事件檢視器] 中，檢閱 Applications and Services\Microsoft\FileSync\Agent 下的遙測、作業和診斷事件記錄。 
   1. 確認檔案存在於 Azure 檔案共用中。

      > [!NOTE]
      > 檔案必須同步到 Azure 檔案共用，才能將它分層。

   2. 確認伺服器具有網際網路連線。 
   3. 確認 Azure 檔案同步篩選器驅動程式 (StorageSync.sys 與 StorageSyncGuard.sys) 正在執行：
       - 在提高權限的命令提示字元，執行 `fltmc`。 確認已列出 StorageSync.sys 和 StorageSyncGuard.sys 檔案系統篩選器驅動程式。

> [!NOTE]
> 如果檔案無法分層，則會每小時在遙測事件記錄中記錄一次事件識別碼 9003 (對於每個錯誤碼會記錄一個事件)。 如果在診斷問題時需要額外的資訊，則應使用作業和診斷事件記錄。

<a id="files-fail-recall"></a>**針對無法重新叫用檔案的問題進行疑難排解**  
如果無法重新叫用檔案：
1. 在 [事件檢視器] 中，檢閱 Applications and Services\Microsoft\FileSync\Agent 下的遙測、作業和診斷事件記錄。
    1. 確認檔案存在於 Azure 檔案共用中。
    2. 確認伺服器具有網際網路連線。 
    3. 開啟 [服務 MMC 嵌入式管理單元]，並確認儲存體同步代理程式服務 (FileSyncSvc) 正在執行。
    4. 確認 Azure 檔案同步篩選器驅動程式 (StorageSync.sys 與 StorageSyncGuard.sys) 正在執行：
        - 在提高權限的命令提示字元，執行 `fltmc`。 確認已列出 StorageSync.sys 和 StorageSyncGuard.sys 檔案系統篩選器驅動程式。

> [!NOTE]
> 如果檔案無法回收，則會每小時在遙測事件記錄中記錄一次事件識別碼 9006 (對於每個錯誤碼會記錄一個事件)。 如果在診斷問題時需要額外的資訊，則應使用作業和診斷事件記錄。

<a id="files-unexpectedly-recalled"></a>**針對在伺服器上意外重新叫用檔案的問題進行疑難排解**  
防毒程式、備份及其他讀取大量檔案的應用程式，除非採用離線屬性並略過讀取這些檔案的內容，否則將會導致非預期的重新叫用。 略過支援此選項之產品的離線檔案，有助於在執行防毒掃描、備份等作業時，避免發生非預期的重新叫用。

請洽詢您的軟體廠商，了解如何設定其解決方案以略過讀取離線檔案。

非預期的重新叫用也會在其他情況下發生，例如在 [檔案總管] 中瀏覽檔案時。 在伺服器的 [檔案總管]中開啟包含雲端層檔案的資料夾，可能會導致非預期的重新叫用。 特別是伺服器上已啟用防毒程式時，更容易發生。

> [!NOTE]
>使用遙測事件記錄中的事件識別碼 9059 來判斷哪些應用程式造成回收。 此事件會提供伺服器端點的應用程式回收分布，且一小時記錄一次。

## <a name="general-troubleshooting"></a>一般疑難排解
如果在伺服器上使用 Azure 檔案同步時發生問題，請完成以下步驟開始疑難排解：
1. 在 [事件檢視器] 中檢閱遙測、作業和診斷事件記錄。
    - 系統會將同步、分層和回收問題記錄在 Applications and Services\Microsoft\FileSync\Agent 下的遙測、診斷和作業事件記錄中。
    - 與管理伺服器相關的問題 (例如組態設定) 則記錄在 Applications and Services\Microsoft\FileSync\Management 下的作業和診斷事件記錄中。
2. 確認 Azure 檔案同步服務正在伺服器上執行：
    - 開啟 [服務] MMC 嵌入式管理單元，並確認儲存體同步代理程式服務 (FileSyncSvc) 正在執行。
3. 確認 Azure 檔案同步篩選器驅動程式 (StorageSync.sys 與 StorageSyncGuard.sys) 正在執行：
    - 在提高權限的命令提示字元，執行 `fltmc`。 確認已列出 StorageSync.sys 和 StorageSyncGuard.sys 檔案系統篩選器驅動程式。

如果問題沒有解決，執行 AFSDiag 工具：
1. 建立將用來儲存 AFSDiag 輸出的目錄 (例如 C:\Output)。
    > [!NOTE]
    >AFSDiag 會刪除之前收集記錄檔的輸出目錄中的所有內容。 指定輸出位置不包含資料。
2. 開啟提升權限的 PowerShell 視窗，然後執行下列命令 (每個命令後要按 Enter 鍵)：

    ```powershell
    cd "c:\Program Files\Azure\StorageSyncAgent"
    Import-Module .\afsdiag.ps1
    Debug-Afs c:\output # Note: Use the path created in step 1.
    ```

3. 針對 Azure 檔案同步核心模式追蹤層級，輸入 **1** (除非為了建立更詳細的追蹤而指定其他值)，然後按 Enter。
4. 針對 Azure 檔案同步使用者模式追蹤層級，輸入 **1** (除非為了建立更詳細的追蹤而指定其他值)，然後按 Enter。
5. 重現問題。 完成時，輸入 **D**。
6. 含有記錄和追蹤檔案的 .zip 檔案將會儲存在指定的輸出目錄中。

## <a name="see-also"></a>請參閱
- [監視 Azure 檔案同步](storage-sync-files-monitoring.md)
- [Azure 檔案服務常見問題集](storage-files-faq.md)
- [針對 Windows 中的 Azure 檔案服務問題進行疑難排解](storage-troubleshoot-windows-file-connection-problems.md)
- [針對 Linux 中的 Azure 檔案服務問題進行疑難排解](storage-troubleshoot-linux-file-connection-problems.md)
