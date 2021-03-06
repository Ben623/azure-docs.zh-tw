---
title: 使用 Azure 備份伺服器來還原 VMware VM
description: 使用 Azure 備份伺服器（MABS）來還原 VMware vCenter/ESXi 伺服器上執行的 VMware Vm。
ms.topic: conceptual
ms.date: 08/18/2019
ms.openlocfilehash: ab2fb4f8f79fa5a664f5cb0ba1bb537c1df658c2
ms.sourcegitcommit: 0eb0673e7dd9ca21525001a1cab6ad1c54f2e929
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/14/2020
ms.locfileid: "77212329"
---
# <a name="restore-vmware-virtual-machines"></a>還原 VMware 虛擬機器

本文說明如何使用 Microsoft Azure 備份 Server （MABS）來還原 VMware VM 復原點。 如需使用 MABS 來復原資料的總覽，請參閱[復原受保護的資料](https://docs.microsoft.com/azure/backup/backup-azure-alternate-dpm-server)。 在 MABS 系統管理員主控台中，有兩種方式可尋找可復原的資料-搜尋或流覽。 復原資料時，您可能會想要將資料或 VM 還原至相同的位置。 基於這個理由，MABS 支援 VMware VM 備份的三個修復選項：

* **原始位置復原（OLR）** -使用 OLR 將受保護的 VM 還原至其原始位置。 您只能將 VM 還原到其原始位置（若未新增或刪除任何磁片），因為備份發生。 如果已新增或刪除磁片，您必須使用替代位置復原。

* **替代位置復原（ALR）** -當原始 vm 遺失，或您不想要打擾原始 vm 時，請將 VM 復原到替代位置。 若要將 VM 復原到替代位置，您必須提供 ESXi 主機、資源集區、資料夾和儲存體資料存放區和路徑的位置。 為了協助區分已還原的 VM 與原始 VM，MABS 會將「-已復原」附加至 VM 的名稱。

* **個別檔案位置復原（ILR）** -如果受保護的 Vm 是 WINDOWS Server VM，則可以使用 MABS 的 ILR 功能來復原 vm 內的個別檔案/資料夾。 若要復原個別檔案，請參閱本文稍後的程式。

## <a name="restore-a-recovery-point"></a>還原復原點

1. 在 MABS 系統管理員主控台中，按一下 復原視圖。

2. 使用 [流覽] 窗格，流覽或篩選以尋找您想要復原的 VM。 一旦您選取 VM 或資料夾，窗格的 [復原點] 就會顯示可用的復原點。

    ![可用的復原點](./media/restore-azure-backup-server-vmware/recovery-points.png)

3. 在 [**適用于的復原點**] 欄位中，使用 [行事曆] 和下拉式功能表來選取取得復原點的日期。 以粗體顯示的行事曆日期具有可用的復原點。

4. 在工具功能區上，按一下 [**復原**] 以開啟 [**修復嚮導]** 。

    ![復原嚮導，審查復原選項](./media/restore-azure-backup-server-vmware/recovery-wizard.png)

5. 按 **[下一步]** 前進至 [**指定復原選項**] 畫面。

6. 在 [**指定復原選項**] 畫面上，如果您想要啟用網路頻寬節流設定，請按一下 [**修改**]。 若要停用網路節流，請按 **[下一步]** 。 此 wizard 畫面上沒有可供 VMware Vm 使用的其他選項。 如果您選擇修改網路頻寬節流設定，請在 [節流處理] 對話方塊中，選取 [**啟用網路頻寬使用節流**設定] 以開啟它。 啟用之後，設定**設定**和**工作排程**。

7. 在 [**選取復原類型**] 畫面上，選擇要復原到原始實例或新位置，然後按 **[下一步]** 。

     * 如果您選擇 [**復原到原始實例**]，則不需要在嚮導中進行任何其他選擇。 會使用原始實例的資料。

     * 如果您選擇 [**復原為任何主機上的虛擬機器**]，請在 [**指定目的地**] 畫面上，提供**ESXi 主機、資源集區、資料夾**和**路徑**的資訊。

      ![選擇復原類型](./media/restore-azure-backup-server-vmware/recovery-type.png)

8. 在 [**摘要**] 畫面上，檢查您的設定，然後按一下 [**復原**] 以開始修復程式。 [復原**狀態**] 畫面會顯示覆原作業的進度。

## <a name="restore-an-individual-file-from-a-vm"></a>從 VM 還原個別檔案

您可以從受保護的 VM 復原點還原個別檔案。 這項功能僅適用于 Windows Server Vm。 還原個別檔案類似于還原整個 VM，但您必須先流覽 VMDK 並尋找所需的檔案，然後再開始復原程式。 若要復原個別檔案或從 Windows Server VM 選取檔案：

>[!NOTE]
>從 VM 還原個別檔案僅適用于 Windows VM 和磁片復原點。

1. 在 MABS 系統管理員主控台中，**按一下** 復原視圖。

2. 使用 [**流覽**] 窗格，流覽或篩選以尋找您想要復原的 VM。 一旦您選取 VM 或資料夾，窗格的 [復原點] 就會顯示可用的復原點。

    ![可用的復原點](./media/restore-azure-backup-server-vmware/vmware-rp-disk.png)

3. 在 [**下列的復原點：** ] 窗格中，使用行事曆選取包含所需復原點的日期。 視備份原則的設定方式而定，日期可以有一個以上的復原點。 一旦您選取了復原點的建立日期，請確定您已選擇正確的復原**時間**。 如果選取的日期有多個復原點，請在 [復原時間] 下拉式功能表中選取您的復原點。 一旦您選擇復原點之後，[**路徑：** ] 窗格中就會顯示可復原的專案清單。

4. 若要尋找您要復原的檔案，請在 [**路徑**] 窗格中，按兩下 [可復原的**專案**] 資料行中的專案，將它開啟。 選取您想要復原的檔案、檔案或資料夾。 若要選取多個專案，請在選取每個專案時按**Ctrl**鍵。 使用 [**路徑**] 窗格，即可搜尋出現在 [可復原的**專案**] 資料行中的檔案或資料夾清單。 **下列搜尋清單**不會搜尋子資料夾。 若要搜尋子資料夾，請按兩下該資料夾。 使用 [**向上**] 按鈕，從子資料夾移至上層資料夾。 您可以選取多個專案（檔案和資料夾），但它們必須位於相同的父資料夾中。 您無法在相同的復原工作中，從多個資料夾復原專案。

    ![檢閱復原選項](./media/restore-azure-backup-server-vmware/vmware-rp-disk-ilr-2.png)

5. 當您選取要復原的專案時，請在 [系統管理員主控台工具] 功能區中按一下 [**復原**]，以開啟 [**修復嚮導]** 。 在 [復原嚮導] 中，[**審核修復選項**] 畫面會顯示所選取要復原的專案。

6. 在 [**指定復原選項**] 畫面上，如果您想要啟用網路頻寬節流設定，請按一下 [**修改**]。 若要停用網路節流，請按 **[下一步]** 。 此 wizard 畫面上沒有可供 VMware Vm 使用的其他選項。 如果您選擇修改網路頻寬節流設定，請在 [節流處理] 對話方塊中，選取 [**啟用網路頻寬使用節流**設定] 以開啟它。 啟用之後，設定**設定**和**工作排程**。
7. 在 [**選取復原類型**] 畫面上，按 **[下一步]** 。 您只能將您的檔案或資料夾復原到網路資料夾。
8. 在 [**指定目的地**] 畫面上，按一下 **[流覽]** 以尋找檔案或資料夾的網路位置。 MABS 會建立一個資料夾，其中會複製所有已復原的專案。 資料夾名稱的前置詞為 MABS_day-month-year。 當您選取復原檔案或資料夾的位置時，會提供該位置的詳細資料（[目的地]、[目的地路徑] 和 [可用空間]）。

    ![指定復原檔案的位置](./media/restore-azure-backup-server-vmware/specify-destination.png)

9. 在 [**指定復原選項**] 畫面上，選擇要套用的安全性設定。 您可以選擇修改網路頻寬使用節流設定，但預設會停用節流。 此外，也不會啟用**SAN**復原和**通知**。
10. 在 [**摘要**] 畫面上，檢查您的設定，然後按一下 [**復原**] 以開始修復程式。 [復原**狀態**] 畫面會顯示覆原作業的進度。

## <a name="next-steps"></a>後續步驟

如需使用 Azure 備份伺服器時的疑難排解問題，請參閱[Azure 備份伺服器的疑難排解指南](./backup-azure-mabs-troubleshoot.md)。
