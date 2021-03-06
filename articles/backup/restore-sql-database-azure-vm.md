---
title: 在 Azure VM 上還原 SQL Server 資料庫
description: 本文說明如何還原在 Azure VM 上執行，並使用 Azure 備份備份的 SQL Server 資料庫。
ms.topic: conceptual
ms.date: 05/22/2019
ms.openlocfilehash: 642476c98ca223da01bda5c6eb79ee9b53732468
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/13/2020
ms.locfileid: "79252450"
---
# <a name="restore-sql-server-databases-on-azure-vms"></a>在 Azure VM 上還原 SQL Server 資料庫

本文說明如何還原[Azure 備份](backup-overview.md)服務已備份至 Azure 備份復原服務保存庫的 Azure 虛擬機器（VM）上執行的 SQL Server 資料庫。

本文將說明如何還原 SQL Server 資料庫。 如需詳細資訊，請參閱[備份 Azure vm 上的 SQL Server 資料庫](backup-azure-sql-database.md)。

## <a name="restore-to-a-time-or-a-recovery-point"></a>還原到某個時間或復原點

Azure 備份可以還原在 Azure Vm 上執行 SQL Server 資料庫，如下所示：

- 使用交易記錄備份還原到特定的日期或時間（到第二個）。 Azure 備份會自動決定適當的完整差異備份，以及根據所選時間還原所需的記錄備份鏈。
- 還原特定的完整或差異備份，以還原到特定的復原點。

## <a name="prerequisites"></a>Prerequisites

在還原資料庫之前，請注意下列事項：

- 您可以將資料庫還原至相同 Azure 區域中的 SQL Server 執行個體。
- 目的地伺服器必須註冊到和來源相同的保存庫。
- 若要將 TDE 加密的資料庫還原到另一個 SQL Server，您必須先將[憑證還原至目的地伺服器](https://docs.microsoft.com/sql/relational-databases/security/encryption/move-a-tde-protected-database-to-another-sql-server?view=sql-server-2017)。
- 在還原 "master" 資料庫之前，請使用啟動選項 **-m AzureWorkloadBackup**，在單一使用者模式中啟動 SQL Server 實例。
  - **-M**的值是用戶端的名稱。
  - 只有指定的用戶端名稱可以開啟連接。
- 針對所有系統資料庫（model、master、msdb），請在觸發還原之前，先停止 SQL Server Agent 服務。
- 關閉可能會嘗試連接到這些資料庫的任何應用程式。
- 如果您在伺服器上執行多個實例，則所有實例都應該啟動並執行，否則伺服器不會出現在目的地伺服器清單中，以便您將資料庫還原到其中。

## <a name="restore-a-database"></a>還原資料庫

若要還原，您需要下列許可權：

- 執行還原之保存庫中的**備份操作員**許可權。
- 來源 VM (已備份) 的**參與者 (寫入)** 存取權。
- 目標 VM 的**參與者 (寫入)** 存取權：
  - 如果您要還原至相同的 VM，這就是來源 VM。
  - 如果您要還原至替代位置，這就是新的目標 VM。

以下列方式進行還原：

1. 開啟其中有已註冊 SQL Server VM 的保存庫。
2. 在保存庫儀表板中的 [使用量] 底下，選取 [備份項目]。
3. 在 [備份項目] 中的 [備份管理類型] 底下，選取 **Azure VM 中的 SQL**。

    ![選取 Azure VM 中的 SQL](./media/backup-azure-sql-database/sql-restore-backup-items.png)

4. 選取要還原的資料庫。

    ![選取要還原的資料庫](./media/backup-azure-sql-database/sql-restore-sql-in-vm.png)

5. 檢閱資料庫功能表。 其中有資料庫備份的資訊，包括：

    - 最早和最近的還原點。
    - 過去24小時的記錄備份狀態，適用于處於完整和大量記錄復原模式且已針對異動複寫記錄備份設定的資料庫。

6. 選取 [還原]。

    ![選取 [還原]](./media/backup-azure-sql-database/restore-db.png)

7. 在 [**還原**設定] 中，指定還原資料的位置（或如何）：
   - **替代位置**：將資料庫還原至替代位置，並保留原始源資料庫。
   - **覆寫 DB**：將資料還原至與原始來源相同的 SQL Server 執行個體。 此選項會覆寫原始資料庫。

    > [!IMPORTANT]
    > 如果選取的資料庫屬於 AlwaysOn 可用性群組，SQL Server 不允許覆寫資料庫。 只有 [替代位置] 可用。
    >
   - **還原成**檔案：不是還原為資料庫，而是在稍後使用 SQL Server Management Studio，將可以復原的備份檔案還原為資料庫中存在的任何電腦。
     ![還原設定 功能表](./media/backup-azure-sql-database/restore-configuration.png)

### <a name="restore-to-an-alternate-location"></a>還原至替代位置

1. 在 [**還原**設定] 功能表的 [**要還原的位置**] 底下，選取 [**替代位置**]。
2. 選取您要在其中還原資料庫的目的地 SQL Server 名稱和執行個體。
3. 在 [還原的 DB 名稱] 方塊中，輸入目標資料庫的名稱。
4. 如果適用的話，請選取 [Overwrite if the DB with the same name already exists on selected SQL instance] \(若選取的 SQL 執行個體上已存在相同名稱的 DB 則覆寫\)。
5. 選取 [確定]。

    ![提供 [還原設定] 功能表的值](./media/backup-azure-sql-database/restore-configuration.png)

6. 在 [**選取還原點**] 中，選取要[還原到特定的時間點](#restore-to-a-specific-point-in-time)，還是要[還原到特定的復原點](#restore-to-a-specific-restore-point)。

    > [!NOTE]
    > 還原時間點僅適用于處於完整和大量記錄復原模式之資料庫的記錄備份。

### <a name="restore-and-overwrite"></a>還原並覆寫資料庫

1. 在 [**還原**設定] 功能表的 [**要還原的位置**] 底下，選取 [**覆寫 DB** **] > [確定]** 。

    ![選取 [覆寫 DB]](./media/backup-azure-sql-database/restore-configuration-overwrite-db.png)

2. 在 [**選取還原點**] 中，選取 **[記錄（時間點）** ] 以[還原至特定時間點](#restore-to-a-specific-point-in-time)。 或選取 [**完整 & 差異**]，以還原至[特定復原點](#restore-to-a-specific-restore-point)。

    > [!NOTE]
    > 還原時間點僅適用于處於完整和大量記錄復原模式之資料庫的記錄備份。

### <a name="restore-as-files"></a>還原為檔案

若要將備份資料還原為 .bak 檔案，而不是資料庫，請選擇 [**還原為**檔案]。 一旦檔案傾印到指定的路徑，您就可以將這些檔案放到您想要將它們還原為資料庫的任何電腦上。 由於能夠將這些檔案移到任何電腦上，因此您現在可以在訂用帳戶和區域之間還原資料。

1. 在 [**還原**設定] 功能表的 [**要還原的位置**] 底下，選取 [**還原為**檔案]。
2. 選取您要還原備份檔案的 SQL Server 名稱。
3. 在**伺服器的目的地路徑**中，輸入在步驟2中選取之伺服器上的資料夾路徑。 這是服務將會傾印所有必要備份檔案的位置。 一般而言，當指定為目的地路徑時，已掛接之 Azure 檔案共用的網路共用路徑或路徑，可讓相同網路中的其他電腦或掛接在其上的相同 Azure 檔案共用，更輕鬆地存取這些檔案。<BR>

    >若要在裝載于目標的已註冊 VM 上的 Azure 檔案共用上還原資料庫備份檔案，請確定 NT AUTHORITY\SYSTEM 具有檔案共用的存取權。 您可以執行下列步驟，將讀取/寫入權限授與在 VM 上裝載的 AFS：
    >
    >- 執行 `PsExec -s cmd` 以進入 NT AUTHORITY\SYSTEM shell
    >   - 執行 `cmdkey /add:<storageacct>.file.core.windows.net /user:AZURE\<storageacct> /pass:<storagekey>`
    >   - 使用 `dir \\<storageacct>.file.core.windows.net\<filesharename>` 驗證存取
    >- 從備份保存庫開始還原為檔案，以 `\\<storageacct>.file.core.windows.net\<filesharename>` 做為路徑<BR>
    您可以透過 <https://docs.microsoft.com/sysinternals/downloads/psexec> 下載 Psexec

4. 選取 [確定]。

    ![選取還原為檔案](./media/backup-azure-sql-database/restore-as-files.png)

5. 選取對應至將還原所有可用 .bak 檔案的**還原點**。

    ![選取還原點](./media/backup-azure-sql-database/restore-point.png)

6. 與選取的復原點相關聯的所有備份檔案都會傾印到目的地路徑。 您可以使用 SQL Server Management Studio，將檔案還原為其所在電腦上的資料庫。

    ![已還原目的地路徑中的備份檔案](./media/backup-azure-sql-database/sql-backup-files.png)

### <a name="restore-to-a-specific-point-in-time"></a>還原至特定時間點

如果您已選取 [記錄 (時間點)] 作為還原類型，請執行下列動作：

1. 在 [**還原日期/時間**] 下，開啟行事曆。 在行事曆上，具有復原點的日期會以粗體顯示，而目前的日期則會反白顯示。
1. 選取具有復原點的日期。 您無法選取沒有任何復原點的日期。

    ![開啟行事曆](./media/backup-azure-sql-database/recovery-point-logs-calendar.png)

1. 選取日期之後，時間軸圖表會顯示連續範圍中可用的復原點。
1. 在時間軸圖形上指定復原的時間，或選取時間。 然後選取 [確定]。

    ![選取還原時間](./media/backup-azure-sql-database/recovery-point-logs-graph.png)

1. 在 [ **Advanced Configuration** ] 功能表上，如果您想要在還原後保留資料庫 nonoperational，請啟用 [**使用 NORECOVERY 還原**]。
1. 如果您要變更目的地伺服器上的還原位置，請輸入新的目標路徑。
1. 選取 [確定]。

    ![進階設定功能表](./media/backup-azure-sql-database/restore-point-advanced-configuration.png)

1. 在 [還原] 功能表上，選取 [還原] 以啟動還原作業。
1. 在 [**通知**] 區域中追蹤還原進度，或選取 [資料庫] 功能表上的 [**還原作業**] 加以追蹤。

    ![還原作業進度](./media/backup-azure-sql-database/restore-job-notification.png)

### <a name="restore-to-a-specific-restore-point"></a>還原至特定還原點

如果您已選取 [完整和差異] 作為還原類型，請執行下列動作：

1. 從清單中選取復原點，然後選取 [確定] 以完成還原點程序。

    ![選擇完整復原點](./media/backup-azure-sql-database/choose-fd-recovery-point.png)

    >[!NOTE]
    > 根據預設，會顯示過去30天內的復原點。 您可以按一下 [**篩選**]，然後選取自訂範圍，以顯示30天之前的復原點。

1. 在 [ **Advanced Configuration** ] 功能表上，如果您想要在還原後保留資料庫 nonoperational，請啟用 [**使用 NORECOVERY 還原**]。
1. 如果您要變更目的地伺服器上的還原位置，請輸入新的目標路徑。
1. 選取 [確定]。

    ![進階設定功能表](./media/backup-azure-sql-database/restore-point-advanced-configuration.png)

1. 在 [還原] 功能表上，選取 [還原] 以啟動還原作業。
1. 在 [**通知**] 區域中追蹤還原進度，或選取 [資料庫] 功能表上的 [**還原作業**] 加以追蹤。

    ![還原作業進度](./media/backup-azure-sql-database/restore-job-notification.png)

### <a name="restore-databases-with-large-number-of-files"></a>還原具有大量檔案的資料庫

如果資料庫中檔案的總字串大小大於[特定限制](backup-sql-server-azure-troubleshoot.md#size-limit-for-files)，Azure 備份會將資料庫檔案清單儲存在不同的 pit 元件中，這樣您就無法在還原作業期間設定目標還原路徑。 檔案會改為還原至 SQL 預設路徑。

  ![使用大型檔案還原資料庫](./media/backup-azure-sql-database/restore-large-files.jpg)

## <a name="next-steps"></a>後續步驟

[管理和監視](manage-monitor-sql-database-backup.md)Azure 備份備份的 SQL Server 資料庫。
