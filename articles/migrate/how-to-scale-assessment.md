---
title: 使用 Azure Migrate 進行規模探索和評定 | Microsoft Docs
description: 說明如何使用 Azure Migrate 服務評定大量的內部部署機器。
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 04/04/2019
ms.author: raynew
ms.openlocfilehash: fe86c758dbf05f91d53cb918b7794c12ab3f39bc
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "65518765"
---
# <a name="discover-and-assess-a-large-vmware-environment"></a>探索及評定大型 VMware 環境

Azure Migrate 具有每個專案 1500 部機器的限制，本文說明如何使用 [Azure Migrate](migrate-overview.md) 來評定大量內部部署虛擬機器 (VM)。

> [!NOTE]
> 我們有提供預覽版本，可允許在使用單一設備的單一專案中探索多達 10,000 個 VMware VM，如果您有興趣試用看看，請登入[這裡。](https://aka.ms/migratefuture)

## <a name="prerequisites"></a>先決條件

- **VMware**：您計劃移轉的虛擬機器必須透過 vCenter Server 5.5、6.0、6.5 或 6.7 版來管理。 此外，您還需要一部執行 5.5 版或更新版本的 ESXi 主機來部署收集器虛擬機器。
- **vCenter 帳戶**：您需要一個唯讀帳戶來存取 vCenter Server。 Azure Migrate 會使用此帳戶來探索內部部署 VM。
- **權限**：在 vCenter Server 中，您需要權限，方可藉由匯入 OVA 格式的檔案來建立 VM。
- **統計資料設定**：此需求僅適用於[單次探索模型](https://docs.microsoft.com/azure/migrate/concepts-collector) (現在已被取代)。 對於單次探索模型，vCenter Server 的統計資料設定應該先設為層級 3，再開始部署。 日、週和月收集間隔的統計資料層級都會設定為 3。 如果任一收集間隔的層級低於 3，還是能進行評定，但不會收集儲存體和網路的效能資料。 將會根據 CPU 和記憶體的效能資料，以及磁碟和網路介面卡的組態資料來提出大小建議。

> [!NOTE]
> 一次性探索設備現在已被取代，因為這個方法依賴 vCenter Server 的統計資料設定來取得效能資料點可用性，而且收集到的平均效能計數器會導致 VM 大小不足，而無法遷移至 Azure。

### <a name="set-up-permissions"></a>設定權限

Azure Migrate 需要存取 VMware 伺服器，才能自動探索 VM 以進行評量。 VMware 帳戶需要下列權限：

- 使用者類型：至少是唯讀使用者
- 權限：資料中心物件 –> 傳播至子物件、role=Read-only
- 詳細資料：在資料中心層級指派的使用者，且能夠存取資料中心內的所有物件。
- 如果要限制存取權，請將具備 [傳播至子物件] 權限的 [沒有存取權] 角色指派給子物件 (vSphere 主機、資料存放區、VM 與網路)。

如果您在多租用戶環境中進行部署，並想要針對單一租用戶的範圍由 Vm 的資料夾，您無法直接選取 [VM] 資料夾，當範圍在 Azure Migrate 中的集合。 以下是如何由資料夾的領域探索的 Vm 的指示：

1. 建立每個租用戶使用者，並將唯讀權限指派給屬於特定的租用戶的所有 Vm。 
2. 授與此使用者唯讀存取權裝載 Vm 之所有父物件。 所要包含的資料中心到階層中所有的父物件-主機、 主機、 叢集、 叢集資料夾的資料夾。 您不需要傳播到所有子物件的權限。
3. 針對選取的資料中心的探索使用的認證*集合範圍*。 設定的 RBAC 可確保對應的 vCenter 使用者可存取只會將租用戶專屬的 Vm。

## <a name="plan-your-migration-projects-and-discoveries"></a>規劃您的移轉專案及探索

根據您打算探索的 VM 數目，可以建立多個專案，並在環境中部署多個設備。 設備可以連線到單一 vCenter 伺服器和單一專案 (除非您停止探索並重新開始)。

在進行單次探索 (現在已被取代) 時，探索能以「射後不理」(Fire and Forget) 模式運作；一旦探索完成後，您可以使用相同收集器從不同的 vCenter Server 收集資料，或將資料傳送到不同的移轉專案。

> [!NOTE]
> 一次性探索設備現在已被取代，因為這個方法依賴 vCenter Server 的統計資料設定來取得效能資料點可用性，而且收集到的平均效能計數器會導致 VM 大小不足，而無法遷移至 Azure。 建議您移至持續探索設備。

根據下列限制來規劃探索和評定：

| **實體** | **機器限制** |
| ---------- | ----------------- |
| 隨附此逐步解說的專案    | 1,500             |
| 探索  | 1,500             |
| 評量 | 1,500             |

請記住下列規劃考量：

- 當您使用 Azure Migrate 收集器進行探索時，可以將探索範圍設定為 vCenter Server 資料夾、資料中心、叢集或主機。
- 若要從相同的 vCenter Server 執行一次以上的探索，請在 vCenter Server 中確認您要探索的虛擬機器是位於支援 1500 部機器限制的資料夾、資料中心、叢集或主機內。
- 基於評定目的，我們建議您將相依的機器保留在同個專案和同次評定內。 在 vCenter Server 中，確認相依的機器位在同一個資料夾、資料中心或叢集內，以供評定。

根據您的案例，您可以將探索分割，如下所述：

### <a name="multiple-vcenter-servers-with-less-than-1500-vms"></a>多部 vCenter Server 包含小於 1500 部虛擬機器
如果您的環境中有多部 vCenter Server，且虛擬機器總數小於 1500，您可以根據本身的案例使用下列方法：

**連續探索：** 進行連續探索時，一個設備只能連線到單一專案。 因此，您必須為每個 vCenter Server 部署一個設備，然後為每個設備建立一個專案，再據以觸發探索。

**單次探索 (現在已被取代)：** 您可使用單一收集器和單一移轉專案來探索所有 vCenter Server 間的所有虛擬機器。 由於單次探索收集器一次會探索一部 vCenter Server，所以您可以針對所有 vCenter Server 執行相同的收集器，並將收集器指向相同的移轉專案。 所有探索完成後，您可以接著建立機器的評估。


### <a name="multiple-vcenter-servers-with-more-than-1500-vms"></a>多部 vCenter Server 包含大於 1500 部虛擬機器

如果您有多部 vCenter Server，且每部 vCenter Server 的虛擬機器小於 1500 部，但所有 vCenter Server 中有超過 1500 部虛擬機器，則必須建立多個移轉專案 (一個移轉專案只能包含 1500 部虛擬機器)。 若要達到這個目的，您可以按 vCenter Server 建立移轉專案，並分割探索。

**連續探索：** 您必須建立多個收集器設備 (每個 vCenter Server 一個)，並將每個設備連線到專案，再據以觸發探索。

**單次探索 (現在已被取代)：** 您可以使用單一收集器來探索每部 vCenter Server (依序)。 如果您希望探索同時間開始，也可以部署多部設備，並以平行方式執行探索。

### <a name="more-than-1500-machines-in-a-single-vcenter-server"></a>在單一 vCenter Server 中超過 1500 部機器

如果您在單一 vCenter Server 中有超過 1500 部虛擬機器，則必須將探索分割為多個移轉專案。 若要分割探索，您可以利用設備中的 [領域] 欄位，並指定主機、 叢集、 資料夾的主機、 叢集或您想要探索的資料中心內的資料夾。 例如，如果您在 vCenter Server 中有兩個資料夾，一個包含 1000 個 VM (Folder1)，另一個包含 800 個 VM (Folder2)，則您可以使用範圍欄位來分割這些資料夾之間的探索。

**連續探索：** 在此案例中，您必須建立兩個收集器設備，並且為第一個收集器指定 Folder1 作為範圍，並將其連線到第一個移轉專案。 您可以使用第二個收集器設備平行啟動 Folder2 的探索，並將其連線到第二個移轉專案。

**單次探索 (現在已被取代)：** 您可以使用相同的收集器來觸發兩個探索。 在第一個探索中，您可以指定 Folder1 作為範圍，並將其指向第一個移轉專案，一旦第一個探索完成之後，您可以使用相同的收集器、將其範圍變更為 Folder2，並將移轉專案詳細資料變更為第二個移轉專案，然後執行第二個探索。

### <a name="multi-tenant-environment"></a>多租用戶環境

如果您有一個在租用戶之間共用的環境，而且不想在一個租用戶的訂用帳戶中探索另一個租用戶的虛擬機器，您可以使用收集器設備中的 [範圍] 欄位來設定探索的範圍。 如果租用戶間要共用主機，請只針對屬於特定租用戶的虛擬機器，建立具有唯讀存取權的認證，然後在收集器設備中使用此認證，並將範圍指定為要進行探索的主機。

## <a name="discover-on-premises-environment"></a>探索內部部署環境

當您準備好計劃時，可以接著開始探索內部部署虛擬機器：

### <a name="create-a-project"></a>建立專案

根據需求建立 Azure Migrate 專案：

1. 在 Azure 入口網站中，選取 [建立資源]  。
2. 搜尋 **Azure Migrate**，然後在搜尋結果中選取 [Azure Migrate]  服務。 然後選取 [建立]  。
3. 指定專案名稱，以及專案的 Azure 訂用帳戶。
4. 建立新的資源群組。
5. 指定您要建立專案的位置，然後選取 [建立]  。 請注意，您仍可針對不同目標位置評定虛擬機器。 為專案指定的位置用於儲存從內部部署虛擬機器收集的中繼資料。

### <a name="set-up-the-collector-appliance"></a>設定收集器設備

Azure Migrate 會建立稱為「收集器設備」的內部部署 VM。 此虛擬機器會探索內部部署 VMware 虛擬機器，並將其相關中繼資料傳送至 Azure Migrate 服務。 若要設定收集器設備，您可以下載 .OVA 檔案，並將其匯入內部部署 vCenter Server 執行個體。

#### <a name="download-the-collector-appliance"></a>下載收集器設備

如果您有多個專案，只需要將收集器設備下載到 vCenter Server 一次。 下載和設定設備之後，您可以針對每個專案執行設備，並指定唯一的專案識別碼和索引鍵。

1. 在 Azure Migrate 專案中，按一下 [開始使用]   > [探索及評定]   > [探索機器]  。
2. 在 [探索機器]  中，按一下 [下載]  以下載設備。

    Azure Migrate 設備會 vCenter Server 與通訊，並持續剖析內部部署環境，以收集每部 VM 的即時使用量資料。 它會收集每個計量 (CPU 使用量、記憶體使用量等) 的尖峰計數器。 此模型並不需仰賴 vCenter Server 的統計資料設定來收集效能資料。 您可以隨時從設備停止持續性的剖析。

    > [!NOTE]
    > 一次性探索設備現在已被取代，因為這個方法依賴 vCenter Server 的統計資料設定來取得效能資料點可用性，而且收集到的平均效能計數器會導致 VM 大小不足，而無法遷移至 Azure。

    **立即滿足：** 透過持續探索設備，探索完成後 (視 VM 數目而定，需要幾個小時)，您可以立即建立評量。 由於效能資料收集會在您開始探索時開始進行，如果您要尋求立即滿足，則應將評量中的調整大小準則選取為「內部部署」  。 對於以效能為基礎的評量，建議等待至少一天後開始探索，以取得可靠的大小建議。

    請注意，設備只會持續收集效能資料，不會偵測內部部署環境中的任何組態變更 (也就是新增、刪除 VM 或新增磁碟等)。 如果內部部署環境中有組態變更，您可以執行下列動作，以在入口網站中反映變更：

    - 新增項目 (VM、磁碟、核心等)：若要在 Azure 入口網站中反映這些變更，您可以從設備停止探索，然後重新啟動。 這可確保所做的變更會在 Azure Migrate 專案中更新。

    - 刪除 VM：基於設備的設計方式，刪除 VM 並不會有所反映，即使您停止探索後再重新啟動也一樣。 這是因為後續探索中的資料會附加至較舊的探索，而且不會受到覆寫。 在此情況下，您可以藉由從群組中移除 VM 並重新計算評定，以直接忽略入口網站中的 VM。

3. 在 [複製專案認證]  中，複製專案識別碼和索引鍵。 在設定收集器時，您會需要這些資料。


#### <a name="verify-the-collector-appliance"></a>確認收集器設備

請先確認 OVA 檔案是安全的，再進行部署：

1. 在存放下載檔案的目標電腦上，開啟系統管理員命令視窗。

2. 執行下列命令以產生 OVA 的雜湊：

   ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```

   使用方式範例：```C:\>CertUtil -HashFile C:\AzureMigrate\AzureMigrate.ova SHA256```

3. 確定所產生的雜湊符合下列設定。

#### <a name="continuous-discovery"></a>連續探索

OVA 1.0.10.4 版

**演算法** | **雜湊值**
--- | ---
MD5 | 2ca5b1b93ee0675ca794dd3fd216e13d
SHA1 | 8c46a52b18d36e91daeae62f412f5cb2a8198ee5
SHA256 | 3b3dec0f995b3dd3c6ba218d436be003a687710abab9fcd17d4bdc90a11276be

#### <a name="one-time-discovery-deprecated-now"></a>單次探索 (現在已被取代)

OVA 1.0.9.15 版 (2018 年 10 月 23 日發行)

**演算法** | **雜湊值**
--- | ---
MD5 | e9ef16b0c837638c506b5fc0ef75ebfa
SHA1 | 37b4b1e92b3c6ac2782ff5258450df6686c89864
SHA256 | 8a86fc17f69b69968eb20a5c4c288c194cdcffb4ee6568d85ae5ba96835559ba

OVA 1.0.9.14 版 (2018 年 8 月 24 日發行)

**演算法** | **雜湊值**
--- | ---
MD5 | 6d8446c0eeba3de3ecc9bc3713f9c8bd
SHA1 | e9f5bdfdd1a746c11910ed917511b5d91b9f939f
SHA256 | 7f7636d0959379502dfbda19b8e3f47f3a4744ee9453fc9ce548e6682a66f13c

OVA 1.0.9.12 版

**演算法** | **雜湊值**
--- | ---
MD5 | d0363e5d1b377a8eb08843cf034ac28a
SHA1 | df4a0ada64bfa59c37acf521d15dcabe7f3f716b
SHA256 | f677b6c255e3d4d529315a31b5947edfe46f45e4eb4dbc8019d68d1d1b337c2e

OVA 1.0.9.8 版

**演算法** | **雜湊值**
--- | ---
MD5 | b5d9f0caf15ca357ac0563468c2e6251
SHA1 | d6179b5bfe84e123fabd37f8a1e4930839eeb0e5
SHA256 | 09c68b168719cb93bd439ea6a5fe21a3b01beec0e15b84204857061ca5b116ff

若為 OVA 1.0.9.7 版

**演算法** | **雜湊值**
--- | ---
MD5 | d5b6a03701203ff556fa78694d6d7c35
SHA1 | f039feaa10dccd811c3d22d9a59fb83d0b01151e
SHA256 | e5e997c003e29036f62bf3fdce96acd4a271799211a84b34b35dfd290e9bea9c

### <a name="create-the-collector-vm"></a>建立收集器 VM

將下載的檔案匯入 vCenter Server：

1. 在 vSphere 用戶端主控台中，選取 [檔案]   > [部署 OVF 範本]  。

    ![部署 OVF](./media/how-to-scale-assessment/vcenter-wizard.png)

2. 在 [部署 OVF 範本精靈] > [來源]  中，指定 OVA 檔案的位置。
3. 在 [名稱]  和 [位置]  中，指定收集器 VM 的易記名稱，以及要裝載 VM 的所在清查物件。
4. 在 [主機/叢集]  中，指定收集器 VM 的執行所在主機或叢集。
5. 在儲存體中，指定收集器 VM 的儲存目的地。
6. 在 [磁碟格式]  中，指定磁碟類型和大小。
7. 在 [網路對應]  中，指定收集器 VM 所要連線的網路。 此網路必須能夠連線到網際網路，以將中繼資料傳送至 Azure。
8. 檢閱並確認設定，然後選取 [完成]  。

### <a name="identify-the-id-and-key-for-each-project"></a>找出每個專案的識別碼和索引鍵

如果您有多個專案，請務必找出每個專案的識別碼和索引鍵。 當您執行收集器來探索 VM 時，將會需要索引鍵。

1. 在專案中，選取 [開始使用]   > [探索及評定]   > [探索機器]  。
2. 在 [複製專案認證]  中，複製專案識別碼和索引鍵。
    ![複製專案認證](./media/how-to-scale-assessment/copy-project-credentials.png)

### <a name="run-the-collector-to-discover-vms"></a>執行收集器來探索 VM

針對每個需要執行的探索，您要執行收集器來探索所需範圍內的虛擬機器。 請逐一執行探索。 系統不支援並行探索，且每個探索的範圍不得相同。

1.  在 vSphere 用戶端主控台中，以滑鼠右鍵按一下 [VM] > [開啟主控台]  。
2.  提供設備的語言、時區和密碼喜好設定。
3.  在桌面上，選取 [執行收集器]  捷徑。
4.  在 Azure Migrate 收集器中，開啟 [設定必要條件]  ，然後：

    a. 接受授權條款，並閱讀第三方資訊。

    收集器會確認 VM 是否能夠存取網際網路。

    b. 如果虛擬機器能夠透過 Proxy 存取網際網路，請選取 [Proxy 設定]  ，然後指定 Proxy 位址和接聽連接埠。 如果 Proxy 需要驗證，請指定認證。

    收集器會檢查收集器服務是否正在執行。 根據預設，收集器 VM 上會安裝此服務。

    c. 下載並安裝 VMware PowerCLI。

5.  在 [指定 vCenter Server 詳細資料]  中，執行下列動作：
    - 指定 vCenter Server 的名稱 (FQDN) 或 IP 位址。
    - 在 [使用者名稱]  和 [密碼]  中，指定收集器要用來探索 vCenter Server 中虛擬機器的唯讀帳戶認證。
    - 在 [選取範圍]  中，選取虛擬機器探索的範圍。 收集器只能探索指定範圍內的虛擬機器。 範圍可以設定為特定資料夾、資料中心或叢集。 其不應包含超過 1000 個虛擬機器。

6.  在 [指定移轉專案]  中，指定專案的識別碼和索引鍵。 如果您之前未複製，請從收集器虛擬機器開啟 Azure 入口網站。 在專案的 [概觀]  頁面上，選取 [探索機器]  ，然後複製值。  
7.  在 [檢視收集進度]  中監視探索程序，然後確認從虛擬機器收集到的中繼資料位於範圍內。 收集器會提供概略的探索時間。

#### <a name="verify-vms-in-the-portal"></a>在入口網站中確認 VM

收集器將會持續對內部部署環境進行剖析，並會持續以一小時為間隔傳送效能資料。 在開始探索的一小時後，您便可以在入口網站中檢閱電腦。 強烈建議您至少先等候一天的時間，再針對 VM 建立以效能為基礎的評量。

1. 在移轉專案中，按一下 [管理]   > [機器]  。
2. 確認您想要探索的 VM 出現在入口網站內。

### <a name="data-collected-from-on-premises-environment"></a>從內部部署環境收集的資料

收集器設備會探索下列關於所選虛擬機器的組態資料。

1. VM 顯示名稱 (在 vCenter 上)
2. VM 的清查路徑 (vCenter 中的主機/資料夾)
3. IP 位址
4. MAC 位址
5. 作業系統
5. 核心、磁碟、NIC 數目
6. 記憶體大小、磁碟大小
7. 以及 VM、磁碟及網路的效能計數器，如下表所列。

收集器設備會以 20 秒的間隔，從 ESXi 主機收集下列每個 VM 的效能計數器。 這些計數器是 vCenter 計數器，雖然術語說明是平均計數器，但 20 秒範例是即時計數器。 然後，設備會彙總 20 秒的樣本，並從 20 秒的樣本中選取尖峰值，建立以 15 分鐘為間隔的單一資料點，並將其傳送至 Azure。 啟動探索兩小時之後，就可以在入口網站上取得 VM 效能資料。 強烈建議您至少先等候一天的時間，再建立以效能為基礎的評量，以取得正確的適當大小建議。 如果您要尋求立即滿足，則可以使用*內部部署*作為調整大小準則來建立評量，這不會考慮適當大小的效能資料。

**計數器** |  **對評量的影響**
--- | ---
cpu.usage.average | 建議的虛擬機器大小和成本  
mem.usage.average | 建議的虛擬機器大小和成本  
virtualDisk.read.average | 計算磁碟大小、儲存成本、VM 大小
virtualDisk.write.average | 計算磁碟大小、儲存成本、VM 大小
virtualDisk.numberReadAveraged.average | 計算磁碟大小、儲存成本、VM 大小
virtualDisk.numberReadAveraged.average | 計算磁碟大小、儲存成本、VM 大小
net.received.average | 計算 VM 大小                          
net.transmitted.average | 計算 VM 大小     

> [!WARNING]
> 依賴 vCenter Server 的統計資料設定來收集效能資料的單次探索現在已被取代。

## <a name="next-steps"></a>後續步驟

- 了解如何[建立評定群組](how-to-create-a-group.md)。
- [深入了解](concepts-assessment-calculation.md)評定的計算方式。
