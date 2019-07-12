---
title: 規劃 Azure 檔案同步部署 | Microsoft Docs
description: 了解規劃 Azure 檔案服務部署時的考量事項。
services: storage
author: roygara
ms.service: storage
ms.topic: article
ms.date: 2/7/2019
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: e9e790ac8ac67478a0e7b5143a5b2f1fdd9c790c
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/11/2019
ms.locfileid: "67798673"
---
# <a name="planning-for-an-azure-file-sync-deployment"></a>規劃 Azure 檔案同步部署
使用 Azure 檔案同步，將組織的檔案共用集中在 Azure 檔案服務中，同時保有內部部署檔案伺服器的彈性、效能及相容性。 Azure 檔案同步會將 Windows Server 轉換成 Azure 檔案共用的快速快取。 您可以使用 Windows Server 上可用的任何通訊協定來從本機存取資料，包括 SMB、NFS 和 FTPS。 您可以視需要存取多個散佈於世界各地的快取。

本文章說明 Azure 檔案同步部署的重要考量。 建議您另行閱讀[規劃 Azure 檔案服務部署](storage-files-planning.md)。 

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="azure-file-sync-terminology"></a>Azure 檔案同步術語
在了解規劃 Azure 檔案同步部署的細節之前，請務必先了解其術語。

### <a name="storage-sync-service"></a>儲存體同步服務
儲存體同步服務是 Azure 檔案同步的最上層 Azure 資源。儲存體同步服務資源是儲存體帳戶資源的對等，同樣可以部署到 Azure 資源群組中。 由於儲存體同步服務可以透過多個同步群組與多個儲存體帳戶建立同步關係，因此最上層資源必須與儲存體帳戶資源不同。 一個訂用帳戶可以部署多個儲存體同步服務資源。

### <a name="sync-group"></a>同步群組
同步群組定義一組檔案的同步拓撲。 同步群組內的端點會與彼此保持同步。 例如，如果您有兩組不同的檔案需要透過 Azure 檔案同步管理，您會建立兩個同步群組，並將不同的端點個別新增至這兩個同步群組。 儲存體同步服務可以視需要裝載許多同步群組。  

### <a name="registered-server"></a>已註冊的伺服器
已註冊的伺服器物件代表您的伺服器 (或叢集) 與儲存體同步服務之間的信任關係。 您可以視需要向儲存體同步服務執行個體註冊多個伺服器。 不過，一次只能向單一儲存體同步服務註冊單一伺服器 (或叢集)。

### <a name="azure-file-sync-agent"></a>Azure 檔案同步代理程式
Azure 檔案同步代理程式是可下載的套件，可讓 Windows Server 能夠和 Azure 檔案共用進行同步處理。 Azure 檔案同步代理程式有三個主要元件： 
- **FileSyncSvc.exe**：負責監視伺服器端點上的變更，並起始同步至 Azure 之工作階段的背景 Windows 服務。
- **StorageSync.sys**：負責將檔案分層處理到 Azure 檔案服務 (啟用雲端階層處理時) 的 Azure 檔案同步檔案系統篩選。
- **PowerShell 管理 Cmdlet**：用來與 Microsoft.StorageSync Azure 資源提供者互動的 PowerShell Cmdlet。 您可以在下列 (預設) 位置找到這些 Cmdlet：
    - C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.PowerShell.Cmdlets.dll
    - C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll

### <a name="server-endpoint"></a>伺服器端點
伺服器端點代表已註冊伺服器上的特定位置，例如伺服器磁碟區上的資料夾。 如果伺服器端點的命名空間沒有重疊 (例如 `F:\sync1` 和 `F:\sync2`)，則相同磁碟區中可以存在多個伺服器端點。 您可以為每個伺服器端點個別設定雲端階層原則。 

您可以透過掛接點建立伺服器端點。 請注意，伺服器端點內的掛接點會略過。  

您可以在系統磁碟區上建立伺服器端點，但這樣做有兩個限制：
* 無法啟用雲端階層處理。
* 不會執行快速命名空間還原 (其中系統會快速中斷連線整個命名空間，然後開始重新叫用內容)。


> [!Note]  
> 僅支援非卸除式磁碟區。  不支援使用從遠端共用對應的磁碟機作為伺服器端點路徑。  此外，伺服器端點可以位於 Windows 系統磁碟區，但系統磁碟區上不支援雲端階層。

如果您將具有一組現有檔案的伺服器位置作為伺服器端點新增至同步群組，那些檔案會與同步群組中其他端點上已存在的任何其他檔案合併。

### <a name="cloud-endpoint"></a>雲端端點
雲端端點是屬於同步群組之一部分的 Azure 檔案共用。 整個 Azure 檔案共用都會同步，而且 Azure 檔案共用只能是單一雲端端點的成員。 因此，Azure 檔案共用只能是單一同步處理群組的成員。 如果您將內含一組現有檔案的 Azure 檔案共用作為雲端端點新增至同步群組，現有的檔案會與同步群組中其他端點上已存在的任何其他檔案合併。

> [!Important]  
> Azure 檔案同步支援直接對 Azure 檔案共用進行變更。 不過，在 Azure 檔案共用上所做的任何變更，都必須先由 Azure 檔案同步變更偵測作業做出探索。 針對雲端端點的變更偵測作業，每隔 24 小時才會起始一次。 此外，透過 REST 通訊協定對 Azure 檔案共用所做的變更，將不會更新 SMB 上次修改時間，而且將不會同步顯示為變更。如需詳細資訊，請參閱 [Azure 檔案服務常見問題集](storage-files-faq.md#afs-change-detection)。

### <a name="cloud-tiering"></a>雲端階層處理 
雲端階層處理是 Azure 檔案同步的一個選用功能，其中經常存取的檔案會快取到伺服器本機上，而其他的檔案會依原則設定分層處理至 Azure 檔案服務。 如需詳細資訊，請參閱[了解雲端階層處理](storage-sync-cloud-tiering.md)。

## <a name="azure-file-sync-system-requirements-and-interoperability"></a>Azure 檔案同步系統需求和互通性 
本節涵蓋 Azure 檔案同步代理程式與 Windows Server 功能和角色以及第三方解決方案的系統需求和互通性。

### <a name="evaluation-cmdlet"></a>評估 cmdlet
在部署 Azure 檔案同步，您應該評估是否與您使用 Azure 檔案同步評估 cmdlet 的系統相容。 此 cmdlet 會檢查潛在的問題，與您的檔案系統和資料集，例如不支援的字元或不受支援的作業系統版本。 請注意，此工具會檢查下列提到的大部分功能 (但不是全部)；我們建議您仔細閱讀本節的其餘部分，以確保您的部署可順利進行。 

評估指令程式可以安裝所安裝的 Az PowerShell 模組，可依照此處的指示進行安裝：[安裝和設定 Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-Az-ps)。

#### <a name="usage"></a>使用量  
您可以使用幾個不同的方式來叫用評估工具：您可以執行系統檢查、資料集檢查，或兩者都執行。 若要執行系統和資料集的檢查： 

```powershell
    Invoke-AzStorageSyncCompatibilityCheck -Path <path>
```

若要只要測試資料集：
```powershell
    Invoke-AzStorageSyncCompatibilityCheck -Path <path> -SkipSystemChecks
```
 
若只要測試系統需求：
```powershell
    Invoke-AzStorageSyncCompatibilityCheck -ComputerName <computer name>
```
 
若要以 CSV 格式顯示結果：
```powershell
    $errors = Invoke-AzStorageSyncCompatibilityCheck […]
    $errors | Select-Object -Property Type, Path, Level, Description | Export-Csv -Path <csv path>
```

### <a name="system-requirements"></a>系統需求
- 執行 Windows Server 2012 R2、Windows Server 2016 或 Windows Server 2019 的伺服器：

    | Version | 支援的 SKU | 支援的部署選項 |
    |---------|----------------|------------------------------|
    | Windows Server 2019 | Datacenter 和 Standard | 完整和核心 |
    | Windows Server 2016 | Datacenter 和 Standard | 完整和核心 |
    | Windows Server 2012 R2 | Datacenter 和 Standard | 完整和核心 |

    Windows Server 的未來版本將會於發佈時加入支援清單。

    > [!Important]  
    > 建議您透過 Windows Update 的最新更新，將搭配 Azure 檔案同步使用的所有伺服器保持在最新狀態。 

- 至少有 2 GiB 記憶體的伺服器。

    > [!Important]  
    > 如果伺服器在啟用動態記憶體的虛擬機器中執行，則 VM 的記憶體應最少設定為 2048 MiB。
    
- 本機連結的磁碟區會以 NTFS 檔案系統進行格式化。

### <a name="file-system-features"></a>檔案系統功能

| 功能 | 支援狀態 | 注意 |
|---------|----------------|-------|
| 存取控制清單 (ACL) | 完全支援 | Azure 檔案同步會保留 Windows ACL，並由 Windows Server 在伺服器端點上強制執行。 如果檔案是直接在雲端中存取，則 Azure 檔案服務尚未支援 Windows ACL。 |
| 永久連結 | Skipped | |
| 符號連結 | Skipped | |
| 掛接點 | 部分支援 | 掛接點可能是伺服器端點的根目錄，但如果伺服器端點的命名空間中包含它們，系統會予以略過。 |
| 接合 | Skipped | 例如，分散式檔案系統 DfrsrPrivate 和 DFSRoots 資料夾。 |
| 重新分析點 | Skipped | |
| NTFS 壓縮 | 完全支援 | |
| 疏鬆檔案 | 完全支援 | 疏鬆檔案會同步 (不會封鎖)，但它們會以完整檔案的形式同步至雲端。 如果檔案內容在雲端中 (或另一部伺服器上) 有所變更，該檔案在變更下載之後就不再是疏鬆檔案。 |
| 替代資料流 (ADS) | 已保留，但未同步 | 例如，不會同步「檔案分類基礎結構」所建立的分類標籤。 每個伺服器端點上檔案的現有分類標籤會原封不動。 |

> [!Note]  
> 僅支援 NTFS 磁碟區。 不支援 ReFS、FAT、FAT32 及其他檔案系統。

### <a name="files-skipped"></a>跳過的檔案

| 檔案/資料夾 | 附註 |
|-|-|
| Desktop.ini | 系統專用檔案 |
| ethumbs.db$ | 暫存檔案的縮圖 |
| ~$\*.\* | Office 暫存檔 |
| \*.tmp | 暫存檔 |
| \*.laccdb | Access DB 鎖定檔|
| 635D02A9D91C401B97884B82B3BCDAEA.* | 內部同步檔案|
| \\系統磁碟區資訊 | 特定磁碟區的資料夾 |
| $RECYCLE.BIN| 資料夾 |
| \\SyncShareState | 同步處理的資料夾 |

### <a name="failover-clustering"></a>容錯移轉叢集
Azure 檔案同步的 [一般用途的檔案伺服器] 部署選項支援 Windows Server 容錯移轉叢集。 「適用於應用程式資料的向外延展檔案伺服器」(SOFS) 或叢集共用磁碟區 (CSV) 上並不支援容錯移轉叢集。

> [!Note]  
> 必須在容錯移轉叢集中的每個節點上安裝 Azure 檔案同步代理程式，同步才能正確運作。

### <a name="data-deduplication"></a>重複資料刪除
**代理程式版本 5.0.2.0 或更新版本**   
重複資料刪除會在 Windows Server 2016 和 Windows Server 2019 中，已啟用雲端階層處理的磁碟區上受到支援。 啟用重複資料刪除磁碟區上，以啟用雲端階層處理，可讓您快取多個檔案內部，不需要佈建更多儲存空間。 

重複資料刪除磁碟區上啟用以啟用雲端階層處理時，重複資料刪除最佳化檔案內的伺服器端點的位置會分層類似於一般的檔案為基礎的雲端階層處理原則設定。 一次最佳化的檔案已分層儲存重複資料刪除，重複資料刪除記憶體回收工作會自動執行不必要區塊移除，不再參考回收磁碟空間的磁碟區上的其他檔案。

請注意省下的磁碟區只會套用到伺服器;Azure 檔案共用中的資料將不會重複資料刪除。

**Windows Server 2012 R2 或舊版代理程式**  
針對未啟用雲端階層處理的磁碟區，Azure 檔案同步支援在磁碟區上啟用 Windows Server 重複資料刪除。

**注意事項**
- 如果在安裝 Azure 檔案同步代理程式之前安裝重複資料刪除，則重新啟動，才支援重複資料刪除和雲端階層處理相同的磁碟區上。
- 如果重複資料刪除磁碟區上啟用後雲端階層是否啟用，初始的重複資料刪除最佳化工作將會最佳化磁碟區上，不已分層，並會在雲端上有下列影響的檔案分層：
    - 使用熱度圖，可用空間原則將會繼續根據可用空間的磁碟區上的層檔案。
    - 日期原則會略過的可能已否則適合因為重複資料刪除最佳化工作存取的檔案分層的檔案階層處理。
- 進行中的重複資料刪除最佳化工作，針對雲端階層處理日期原則將取得延遲，重複資料刪除[MinimumFileAgeDays](https://docs.microsoft.com/powershell/module/deduplication/set-dedupvolume?view=win10-ps)設定，如果檔案已經不會分層。 
    - 範例：如果將 MinimumFileAgeDays 設定為 7 天雲端階層處理日期原則是 30 天，原則將 37 天後層檔案的日期。
    - 注意:一旦檔案已分層的 Azure 檔案同步，重複資料刪除最佳化工作會略過檔案。
- 如果已安裝 Azure 檔案同步代理程式執行 Windows Server 2012 R2 的伺服器已升級至 Windows Server 2016 或 Windows Server 2019，則必須以支援重複資料刪除和雲端階層處理相同的磁碟區上執行下列步驟：  
    - 解除安裝 Windows Server 2012 R2 的 Azure 檔案同步代理程式，並重新啟動伺服器。
    - 下載新的伺服器作業系統版本 （Windows Server 2016 或 Windows Server 2019） 的 Azure 檔案同步代理程式。
    - 安裝 Azure 檔案同步代理程式，並重新啟動伺服器。  
    
    注意:解除安裝並重新安裝代理程式時，會保留在伺服器上的 Azure 檔案同步組態設定。

### <a name="distributed-file-system-dfs"></a>分散式檔案系統 (DFS)
Azure 檔案同步支援與 DFS 命名空間 (DFS-N) 和 DFS 複寫 (DFS-R) 互通。

**DFS 命名空間 (DFS-N)** ：DFS-N 伺服器上完全支援 Azure 檔案同步。 您可以將 Azure 檔案同步代理程式安裝在一或多個 DFS-N 成員上，同步伺服器端點與雲端端點之間的資料。 如需詳細資訊，請參閱 [DFS 命名空間概觀](https://docs.microsoft.com/windows-server/storage/dfs-namespaces/dfs-overview)。
 
**DFS 複寫 (DFS-R)** ：因為 DFS-R 與 Azure 檔案同步都是複寫解決方案，所以建議您在大部分情況下，以 Azure 檔案同步取代 DFS-R。不過有幾個案例，您會想要一起使用 DFS-R 和 Azure 檔案同步：

- 您正要從 DFS-R 部署移轉至 Azure 檔案同步部署。 如需詳細資訊，請參閱[將 DFS 複寫 (DFS-R) 部署移轉至 Azure 檔案同步](storage-sync-files-deployment-guide.md#migrate-a-dfs-replication-dfs-r-deployment-to-azure-file-sync)。
- 並非需要檔案資料複本的每個內部部署伺服器都能直接連線到網際網路。
- 分支伺服器將資料合併到您要使用 Azure 檔案同步的單一中樞伺服器。

Azure 檔案同步和 DFS-R 如需並存使用：

1. 務必在具有 DFS-R 複寫資料夾的磁碟區上停用 Azure 檔案同步雲端階層。
2. 不應在 DFS-R 唯讀複寫資料夾上設定伺服器端點。

如需詳細資訊，請參閱 [DFS 複寫概觀](https://technet.microsoft.com/library/jj127250)。

### <a name="sysprep"></a>Sysprep
不支援在已安裝 Azure 檔案同步代理程式的伺服器上使用 sysprep，而且此舉可能會導致非預期的結果。 請在部署伺服器映像和完成 sysprep 迷你設定之後，再進行代理程式安裝與伺服器註冊。

### <a name="windows-search"></a>Windows 搜尋
如果在伺服器端點上啟用雲端階層處理，則系統會略過階層式檔案，且 Windows 搜尋不會將這些檔案編製索引。 非階層式檔案則會正確編製索引。

### <a name="antivirus-solutions"></a>防毒解決方案
因為防毒程式的運作方式是掃描檔案中的已知惡意程式碼，所以防毒產品可能會導致階層式檔案的重新叫用。 在 4.0 版和更新版本的 Azure 檔案同步代理程式中，階層式檔案已設定安全的 Windows 屬性 FILE_ATTRIBUTE_RECALL_ON_DATA_ACCESS。 建議您洽詢您的軟體廠商，以了解如何設定其解決方案來略過讀取已設定此屬性的檔案 (很多軟體會自動這麼做)。 

作為 Microsoft 內部防毒解決方案的 Windows Defender 和 System Center Endpoint Protection (SCEP)，皆會自動略過讀取已設定此屬性的檔案。 我們已經測試這兩個解決方案並找到一個小問題：當您將伺服器新增至現有同步群組時，會在新的伺服器上重新叫用 (下載) 小於 800 個位元組的檔案。 這些檔案會保留在新的伺服器上，而且不會分層，因為這些檔案不符合階層處理大小需求 (> 64 kb)。

> [!Note]  
> 防毒軟體廠商可以檢查其產品和 Azure 檔案同步使用之間的相容性[Azure 檔案同步防毒軟體相容性測試套件](https://www.microsoft.com/download/details.aspx?id=58322)，適用於 Microsoft Download Center 下載。

### <a name="backup-solutions"></a>備份解決方案
備份解決方案類似防毒解決方案，可能會導致階層式檔案的重新叫用。 建議使用雲端備份解決方案來備份 Azure 檔案共用，而不要使用內部部署備份產品。

如果您要使用內部部署備份解決方案，則應該在已停用雲端階層處理的同步群組中，對其中的某個伺服器執行備份。 執行還原時，請使用磁碟區層級或檔案層級的還原選項。 使用檔案層級還原選項進行還原的檔案會同步至同步群組中的所有端點，並使用從備份還原過來的版本取代現有檔案。  磁碟區層級還原將不會取代 Azure 檔案共用或其他伺服器端點中的較新檔案版本。

> [!Note]  
> 裸機 (BMR) 還原可能會導致非預期的結果，且目前不受支援。

> [!Note]  
> 已啟用雲端階層處理的磁碟區目前不支援 VSS 快照集 (包括 [舊版] 索引標籤)。 如果啟用了雲端階層處理，請使用 Azure 檔案共用快照集從備份還原檔案。

### <a name="encryption-solutions"></a>加密解決方案
加密解決方案的支援取決於其實作方式。 Azure 檔案共用已知可用於：

- BitLocker 加密
- Azure 資訊保護、Azure Rights Management Services (Azure RMS) 及 Active Directory RMS

Azure 檔案共用已知無法用於：

- NTFS 加密檔案系統 (EFS)

一般來說，Azure 檔案同步應該會支援與位於檔案系統下的加密解決方案 (例如 BitLocker)，以及實作於檔案格式中的解決方案 (例如 Azure 資訊保護) 之間的互通性。 對於位於檔案系統之上的解決方案 (例如 NTFS EFS)，並沒有做出任何特殊的互通性。

### <a name="other-hierarchical-storage-management-hsm-solutions"></a>其他階層式存放裝置管理 (HSM) 解決方案
不應該搭配 Azure 檔案同步使用其他 HSM 解決方案。

## <a name="region-availability"></a>區域可用性
Azure 檔案同步僅於下列區域提供：

| 區域 | 資料中心位置 |
|--------|---------------------|
| 澳洲東部 | 新南威爾斯 |
| 澳大利亞東南部 | 維多利亞 |
| 巴西南部 | 聖保羅 Paolo 狀態 |
| 加拿大中部 | 多倫多 |
| 加拿大東部 | 魁北克市 |
| 印度中部 | 浦那 |
| 美國中部 | 愛荷華州 |
| 東亞 | 香港特別行政區 |
| East US | 維吉尼亞州 |
| 美國東部 2 | 維吉尼亞州 |
| 法國中部 | 巴黎 |
| 南韓中部| 首爾 |
| 南韓南部| 斧山 |
| 日本東部 | 東京，埼玉 |
| 日本西部 | 大阪 |
| 美國中北部 | 伊利諾州 |
| 北歐 | 愛爾蘭 |
| 美國中南部 | Texas |
| 印度南部 | 辰內 |
| 東南亞 | 新加坡 |
| 英國南部 | 倫敦 |
| 英國西部 | 卡地夫 |
| 美國政府亞利桑那州 | 亞利桑那州 |
| 美國政府德克薩斯州 | Texas |
| 美國政府維吉尼亞州 | 維吉尼亞州 |
| 西歐 | 荷蘭 |
| 美國中西部 | 懷俄明州 |
| 美國西部 | California |
| 美國西部 2 | Washington |

Azure 檔案同步僅支援與位於和儲存體同步服務相同之區域中的 Azure 檔案共用進行同步。

### <a name="azure-disaster-recovery"></a>Azure 災害復原
為防範遺失 Azure 區域，Azure 檔案同步會與[異地備援儲存體備援](../common/storage-redundancy-grs.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json) (GRS) 選項整合。 GRS 儲存體的運作方式是在主要區域中您通常與其互動的儲存體，與配對的次要區域中的儲存體之間，使用非同步區塊複寫。 發生導致 Azure 區域暫時或永久離線的災害時，Microsoft 會將儲存體容錯移轉到配對的區域。 

> [!Warning]  
> 若您使用 Azure 檔案共用作為 GRS 儲存體帳戶中的雲端端點，您不應該啟動儲存體帳戶的容錯移轉。 這麼做將導致同步停止運作，且可能會在新分層的檔案中產生未預期的資料遺失。 若發生 Azure 區域遺失，Microsoft 將會觸發與 Azure 檔案同步相容的儲存體帳戶容錯移轉。

為支援異地備援儲存體和 Azure 檔案同步之間的容錯移轉整合，所有 Azure 檔案同步區域都會與符合儲存體所使用之次要區域的次要區域配對。 這些配對如下：

| 主要區域      | 配對的區域      |
|---------------------|--------------------|
| 澳洲東部      | 澳大利亞東南部|
| 澳大利亞東南部 | 澳洲東部     |
| 巴西南部        | 美國中南部   |
| 加拿大中部      | 加拿大東部        |
| 加拿大東部         | 加拿大中部     |
| 印度中部       | 印度南部        |
| 美國中部          | 美國東部 2          |
| 東亞           | 東南亞     |
| East US             | 美國西部            |
| 美國東部 2           | 美國中部         |
| 法國中部      | 法國南部       |
| 日本東部          | 日本西部         |
| 日本西部          | 日本東部         |
| 南韓中部       | 南韓南部        |
| 南韓南部         | 南韓中部      |
| 北歐        | 西歐        |
| 美國中北部    | 美國中南部   |
| 美國中南部    | 美國中北部   |
| 印度南部         | 印度中部      |
| 東南亞      | 東亞          |
| 英國南部            | 英國西部            |
| 英國西部             | 英國南部           |
| 美國政府亞利桑那州      | 美國政府德克薩斯州       |
| US Gov 愛荷華州         | 美國政府維吉尼亞州    |
| 美國政府維吉尼亞州      | 美國政府德克薩斯州       |
| 西歐         | 北歐       |
| 美國中西部     | 美國西部 2          |
| 美國西部             | East US            |
| 美國西部 2           | 美國中西部    |

## <a name="azure-file-sync-agent-update-policy"></a>Azure 檔案同步代理程式更新原則
[!INCLUDE [storage-sync-files-agent-update-policy](../../../includes/storage-sync-files-agent-update-policy.md)]

## <a name="next-steps"></a>後續步驟
* [考量防火牆和 Proxy 設定](storage-sync-files-firewall-and-proxy.md)
* [規劃 Azure 檔案部署](storage-files-planning.md)
* [部署 Azure 檔案服務](storage-files-deployment-guide.md)
* [部署 Azure 檔案同步](storage-sync-files-deployment-guide.md)
* [監視 Azure 檔案同步](storage-sync-files-monitoring.md)
