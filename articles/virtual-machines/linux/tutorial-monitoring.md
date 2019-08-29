---
title: 教學課程 - 在 Azure 中監視和更新 Linux 虛擬機器 | Microsoft Docs
description: 在本教學課程中，您會了解如何監視 Linux 虛擬機器上的開機診斷和效能計量及管理套件更新
services: virtual-machines-linux
documentationcenter: virtual-machines
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: tutorial
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 01/26/2019
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 64000f42798b321c937dc3d01bf22e594f2e6176
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/28/2019
ms.locfileid: "70081695"
---
# <a name="tutorial-monitor-and-update-a-linux-virtual-machine-in-azure"></a>教學課程：在 Azure 中監視和更新 Linux 虛擬機器

為了確保您的虛擬機器 (VM) 在 Azure 中正確執行，您可以檢閱開機診斷、效能計量及管理套件更新。 在本教學課程中，您了解如何：

> [!div class="checklist"]
> * 啟用 VM 上的開機診斷
> * 檢視開機診斷
> * 檢視主機計量
> * 啟用 VM 上的診斷擴充功能
> * 檢視 VM 計量
> * 依據診斷計量建立警示
> * 管理套件更新
> * 監視變更和清查
> * 設定進階監視

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

如果您選擇在本機安裝和使用 CLI，本教學課程會要求您執行 Azure CLI 2.0.30 版或更新版本。 執行 `az --version` 以尋找版本。 如果您需要安裝或升級，請參閱[安裝 Azure CLI]( /cli/azure/install-azure-cli)。

## <a name="create-vm"></a>建立 VM

若要查看作用中的診斷和計量，您需要 VM。 首先，使用 [az group create](/cli/azure/group#az-group-create) 建立資源群組。 下列範例會在 eastus  位置建立名為 myResourceGroupMonitor  的資源群組。

```azurecli-interactive
az group create --name myResourceGroupMonitor --location eastus
```

現在，使用 [az vm create](/cli/azure/vm#az-vm-create) 建立 VM。 下列範例會建立名為 myVM  的 VM，並產生 SSH 金鑰 (如果 ~/.ssh/  中沒有這些金鑰的話)︰

```azurecli-interactive
az vm create \
  --resource-group myResourceGroupMonitor \
  --name myVM \
  --image UbuntuLTS \
  --admin-username azureuser \
  --generate-ssh-keys
```

## <a name="enable-boot-diagnostics"></a>啟用開機診斷

當 Linux VM 開機，開機診斷擴充功能會擷取開機輸出，並儲存在 Azure 儲存體。 這項資料可以用於 VM 開機問題的疑難排解。 當您使用 Azure CLI 建立 Linux VM 時，開機診斷不會自動啟用。

啟用開機診斷之前，需要建立儲存體帳戶用來儲存開機記錄。 儲存體帳戶必須具有全域唯一的名稱，介於 3 到 24 個字元的長度，而且只能包含數字和小寫字母。 使用 [az storage account create](/cli/azure/storage/account#az-storage-account-create) 建立儲存體帳戶。 在此範例中，使用隨機字串來建立唯一的儲存體帳戶名稱。

```azurecli-interactive
storageacct=mydiagdata$RANDOM

az storage account create \
  --resource-group myResourceGroupMonitor \
  --name $storageacct \
  --sku Standard_LRS \
  --location eastus
```

當啟用開機診斷時，需要 blob 儲存體容器的 URI。 以下命令會查詢儲存體帳戶傳回此 URI。 URI 值儲存在名為 bloburi  的變數，下一個步驟會使用此變數。

```azurecli-interactive
bloburi=$(az storage account show --resource-group myResourceGroupMonitor --name $storageacct --query 'primaryEndpoints.blob' -o tsv)
```

現在，使用 [az vm boot-diagnostics enable](https://docs.microsoft.com/cli/azure/vm/boot-diagnostics#az-vm-boot-diagnostics-enable) 啟用開機診斷。 `--storage` 值是在上一個步驟收集到的 blob URI。

```azurecli-interactive
az vm boot-diagnostics enable \
  --resource-group myResourceGroupMonitor \
  --name myVM \
  --storage $bloburi
```

## <a name="view-boot-diagnostics"></a>檢視開機診斷

開機診斷啟用後，每次您停止並啟動 VM 時，開機程序的相關資訊便會寫入記錄檔。 在此範例中，先使用 [az vm deallocate](/cli/azure/vm#az-vm-deallocate) 命令將 VM 解除配置，如下所示：

```azurecli-interactive
az vm deallocate --resource-group myResourceGroupMonitor --name myVM
```

現在，使用 [az vm start]( /cli/azure/vm#az-vm-stop) 命令啟動 VM，如下所示：

```azurecli-interactive
az vm start --resource-group myResourceGroupMonitor --name myVM
```

您可以使用 [az vm boot-diagnostics get-boot-log](https://docs.microsoft.com/cli/azure/vm/boot-diagnostics#az-vm-boot-diagnostics-get-boot-log) 命令取得 myVM  的開機診斷資料，如下所示︰

```azurecli-interactive
az vm boot-diagnostics get-boot-log --resource-group myResourceGroupMonitor --name myVM
```

## <a name="view-host-metrics"></a>檢視主機計量

在 Azure 中有 Linux VM 專用的主機與它互動。 系統會自動收集主機的計量，而且可以在 Azure 入口網站中檢視這些計量，如下所示︰

1. 從 Azure 入口網站中，選取 [資源群組]  ，選擇 [myResourceGroupMonitor]  ，然後選取資源清單中的 [myVM]  。
1. 若要查看主機 VM 的執行狀況，選取 VM 視窗上的 [計量]  ，然後選擇 [可用的計量]  下的任何 [主機]  計量。

    ![檢視主機計量](./media/tutorial-monitoring/monitor-host-metrics.png)

## <a name="install-diagnostics-extension"></a>安裝診斷擴充功能

系統提供基本的主機計量，但若要查看更細微或 VM 特定的計量，則需要在 VM 上安裝 Azure 診斷延伸模組。 Azure 診斷擴充功能可額外提供從 VM 擷取的監視和診斷資料。 您可以檢視這些效能計量，並依據 VM 的執行狀況建立警示。 診斷擴充功能可透過 Azure 入口網站安裝，如下所示︰

1. 從 Azure 入口網站中，選擇 [資源群組]  ，選取 [myResourceGroupMonitor]  ，然後選取資源清單中的 [myVM]  。
1. 選取 [診斷設定]  。 在 [選擇儲存體帳戶]  下拉式選單中選擇前一節中所建立的 mydiagdata [1234]  帳戶 (如果尚未選取的話)。
1. 選取 [啟用來賓層級監視]  按鈕。

    ![檢視診斷計量](./media/tutorial-monitoring/enable-diagnostics-extension.png)

## <a name="view-vm-metrics"></a>檢視 VM 計量

您可以檢視 VM 計量，和您檢視主機 VM 計量資訊的方式相同︰

1. 從 Azure 入口網站中，選擇 [資源群組]  ，選取 [myResourceGroupMonitor]  ，然後選取資源清單中的 [myVM]  。
1. 若要查看 VM 的執行狀況，選取 VM 視窗上的 [計量]  ，然後選取 [可用的計量]  下的任何 [來賓]  診斷計量。

    ![檢視 VM 計量](./media/tutorial-monitoring/monitor-vm-metrics.png)

## <a name="create-alerts"></a>建立警示

您可以根據特定效能計量來建立警示。 舉例來說，可使用警示來通知您平均 CPU 使用量超過特定臨界值、或是可用磁碟空間降到低於某個量。 警示會顯示在 Azure 入口網站，或是可以透過電子郵件傳送。 您也可以觸發 Azure 自動化 Runbook 或 Azure Logic Apps 以回應產生的警示。

以下範例會建立平均 CPU 使用量的警示。

1. 從 Azure 入口網站中，選取 [資源群組]  ，選取 [myResourceGroupMonitor]  ，然後選取資源清單中的 [myVM]  。
2. 選取 [警示 (傳統)]  ，然後從警示視窗的頂端選擇 [新增計量警示 (傳統)]  。
3. 提供警示的 [名稱]  ，例如 myAlertRule  。
4. 若要在 CPU 百分比連續五分鐘超過 1.0 時觸發警示，保留所有其他已選取的預設值。
5. (選擇性) 選取 [電子郵件的擁有者、參與者及讀者]  核取方塊以傳送電子郵件通知。 預設動作是在入口網站中顯示通知。
6. 選取 [確定]  按鈕。

## <a name="manage-software-updates"></a>管理軟體更新

更新管理可讓您管理 Azure Linux VM 的更新和修補程式。
您可以直接從 VM 快速評估可用更新的狀態、排定何時安裝必要的更新，並檢閱部署結果來確認更新已成功套用至 VM。

如需價格資訊，請參閱[更新管理的自動化價格](https://azure.microsoft.com/pricing/details/automation/)

### <a name="enable-update-management"></a>啟用更新管理

啟用 VM 的更新管理：

1. 在畫面左邊，選取 [虛擬機器]  。
2. 從清單中選取 VM。
3. 在 [VM] 畫面的 [作業]  區段中，選取 [更新管理]  。 [啟用更新管理]  畫面隨即開啟。

將會執行驗證來判斷此 VM 是否已啟用更新管理。
驗證包括檢查 Log Analytics 工作區及連結的自動化帳戶，以及解決方法是否在工作區中。

[Log Analytics](../../log-analytics/log-analytics-overview.md) 工作區可用來收集功能和服務 (例如更新管理) 所產生的資料。
工作區提供單一位置來檢閱和分析來自多個來源的資料。
若要在需要更新的 VM 上執行其他動作，Azure 自動化可讓您對 VM 執行 Runbook，例如下載和套用更新。

驗證程序也會檢查 VM 是否以 Log Analytics 代理程式和自動化混合式 Runbook 背景工作角色佈建。 此代理程式用來與 VM 通訊，並取得更新狀態的相關資訊。

選擇 Log Analytics 工作區與自動化帳戶，然後選取 [啟用]  來啟用解決方案。 啟用解決方案最多需要 15 分鐘。

如果在上線期間遺漏下列任何必要條件，就會自動新增：

* [Log Analytics](../../log-analytics/log-analytics-overview.md) 工作區
* [自動化帳戶](../../automation/automation-offering-get-started.md)
* VM 上已啟用 [Hybrid Runbook 背景工作角色](../../automation/automation-hybrid-runbook-worker.md)

[更新管理]  畫面隨即開啟。 設定位置、Log Analytics 工作區以及要使用的自動化帳戶，然後選取 [啟用]  。 如果欄位呈現灰色，就表示已啟用 VM 的另一個自動化解決方案，且必須使用相同的工作區和自動化帳戶。

![啟用更新管理解決方案](./media/tutorial-monitoring/manage-updates-update-enable.png)

啟用解決方案可能需要 15 分鐘。 在此期間，請勿關閉瀏覽器視窗。 啟用解決方案之後，有關在 VM 上遺漏更新的相關資訊會流向 Azure 監視器記錄。 可能需要 30 分鐘到 6 小時，資料才可供分析。

### <a name="view-update-assessment"></a>檢視更新評量

啟用 [更新管理]  之後，隨即會顯示 [更新管理]  畫面。 完成更新評估之後，您會在 [遺失更新]  索引標籤上看到遺失的更新清單。

 ![檢視更新狀態](./media/tutorial-monitoring/manage-updates-view-status-linux.png)

### <a name="schedule-an-update-deployment"></a>排定更新部署

若要安裝更新，請將部署排定在發行排程和服務時段之後。 您可以選擇要在部署中包含的更新類型。 例如，您可以包含重大更新或安全性更新，並排除更新彙總套件。

按一下 [更新管理]  畫面頂端的 [排程更新部署]  ，以針對 VM 來排程新的更新部署。 在 [新增更新部署]  畫面上，指定下列資訊：

若要建立新的更新部署，請選取 [排程更新部署]  。 [新增更新部署]  窗格隨即開啟。 為下表描述的屬性輸入相關的值，然後按一下 [建立]  ：

| 屬性 | 說明 |
| --- | --- |
| Name |用以識別更新部署的唯一名稱。 |
|作業系統| Linux 或 Windows|
| 要更新的群組 |對於 Azure 機器，根據訂用帳戶、資源群組、位置及標記的組合來定義查詢，以建置要包含在您部署中的動態 Azure VM 群組。 </br></br>對於非 Azure 機器，選取現有的已儲存搜尋，以選取要包含在部署中的非 Azure 機器群組。 </br></br>若要深入了解，請參閱[動態群組](../../automation/automation-update-management.md#using-dynamic-groups)|
| 要更新的機器 |選取已儲存的搜尋、已匯入的群組，或從下拉式清單中選擇 [機器]，然後選取個別的機器。 如果您選擇 [機器]  ，機器的整備程度會顯示於 [更新代理程式整備程度]  欄中。</br> 若要深入了解在 Azure 監視器記錄中建立電腦群組的不同方法，請參閱 [Azure 監視器記錄中的電腦群組](../../azure-monitor/platform/computer-groups.md) |
|更新分類|選取您需要的所有更新分類|
|包含/排除更新|這會開啟 [包含]/[排除]  頁面。 要包含或排除的更新會在個別的索引標籤上。 如需有關如何處理包含的詳細資訊，請參閱[包含行為](../../automation/automation-update-management.md#inclusion-behavior) |
|排程設定|選取開始時間，並選取 [一次] 或 [週期性] 以定期執行|
| 前置指令碼 + 後置指令碼|選取要在部署前和部署後執行的指令碼|
| 維護時間範圍 |為更新設定的分鐘數。 此值不可小於 30 分鐘，且不可超過 6 小時 |
| 重新開機控制| 決定應該如何處理重新開機。 可用選項包括：</br>在必要時重新開機 (預設值)</br>一律重新開機</br>永不重新開機</br>僅重新開機 - 將不會安裝更新|

您也可以透過程式設計方式建立「更新部署」。 若要了解如何使用 REST API 來建立「更新部署」，請參閱[軟體更新設定 - 建立](/rest/api/automation/softwareupdateconfigurations/create)。 此外，也有可用來建立每週「更新部署」的範例 Runbook。 若要深入了解此 Runbook，請參閱[為資源群組中的一或多個 VM 建立每週更新部署](https://gallery.technet.microsoft.com/scriptcenter/Create-a-weekly-update-2ad359a1) \(英文\)。

排程設定完成之後，請按一下 [建立]  按鈕，您就會返回狀態儀表板。
請注意，[已排程]  表格會顯示您已建立的部署排程。

### <a name="view-results-of-an-update-deployment"></a>檢視更新部署的結果

已排程的部署開始之後，您就可以在 [更新管理]  畫面的 [更新部署]  索引標籤上看到該部署的狀態。
如果該部署目前正在執行，其狀態會顯示為 [進行中]  。 當它完成時，如果成功，狀態就會變更為 [成功]  。
如果該部署中的一或多個更新失敗，則狀態會是 [部分失敗]  。
選取已完成的更新部署，即可查看該更新部署的儀表板。

![特定部署的更新部署狀態儀表板](./media/tutorial-monitoring/manage-updates-view-results.png)

[更新結果]  磚包含 VM 上的更新總數和部署結果的摘要。
右邊表格是每個更新和安裝結果的詳細解析，可能是下列其中一個值：

* **未嘗試** - 未安裝更新，因為在已定義的維護時段內，沒有足夠的時間可用。
* **成功** - 更新已順利完成
* **失敗** - 更新失敗

若要查看部署已建立的所有記錄項目，請選取 [所有記錄]  。

選取 [輸出]  磚，以查看負責在目標 VM 上管理更新部署的 Runbook 作業串流。

若要查看部署所傳回的任何錯誤詳細資訊，請選取 [錯誤]  。

## <a name="monitor-changes-and-inventory"></a>監視變更和清查

您可以在電腦上收集並檢視軟體、檔案、Linux 精靈、Windows 服務和 Windows 登錄機碼的清查。 追蹤您電腦的設定可協助您找出環境中的操作問題，並進一步了解您電腦的狀態。

### <a name="enable-change-and-inventory-management"></a>啟用變更和清查管理

為您的 VM 啟用「變更」和「清查」管理：

1. 在畫面左邊，選取 [虛擬機器]  。
2. 從清單中選取 VM。
3. 在 VM 畫面上，選取 [作業]  區段中的 [清查]  或 [變更追蹤]  。 [啟用變更追蹤與詳細目錄]  畫面隨即開啟。

設定位置、Log Analytics 工作區以及要使用的自動化帳戶，然後選取 [啟用]  。 如果欄位呈現灰色，就表示已啟用 VM 的另一個自動化解決方案，且必須使用相同的工作區和自動化帳戶。 儘管這些解決方案在功能表上是分開的，但它們是相同的解決方案。 啟用其中一個會為您的 VM 同時啟用兩個。

![啟用變更與清查追蹤](./media/tutorial-monitoring/manage-inventory-enable.png)

啟用解決方案之後，可能需要一些時間在 VM 上收集清查，然後才會顯示資料。

### <a name="track-changes"></a>追蹤變更

在您的 VM 上，選取 [作業]  底下的 [變更追蹤]  。 選取 [編輯設定]  ，就會顯示 [變更追蹤]  頁面。 選取您想要追蹤的設定類型，然後選取 [+ 新增]  來進行設定。 可用的 Linux 選項為 [Linux 檔案] 

如需有關「變更追蹤」的詳細資訊，請參閱 [針對 VM 上的變更進行疑難排解](../../automation/automation-tutorial-troubleshoot-changes.md)

### <a name="view-inventory"></a>檢視清查

在您的 VM 上，選取 [作業]  底下的 [清查]  。 在 [軟體]  索引標籤上，有一份資料表列出已找到的軟體。 每一筆軟體記錄的高階詳細資料都可在資料表中進行檢視。 這些詳細資料包括軟體名稱、版本、發行者、上次重新整理時間。

![檢視清查](./media/tutorial-monitoring/inventory-view-results.png)

### <a name="monitor-activity-logs-and-changes"></a>監視活動記錄和變更

從您 VM 上的 [變更追蹤]  頁面，選取 [管理活動記錄連線]  。 此工作會開啟 [Azure 活動記錄]  頁面。 選取 [連線]  可將變更追蹤連線 VM 的 Azure 活動記錄。

此設定啟用時，瀏覽至您 VM 的 [概觀]  頁面，並選取 [停止]  來將您的 VM 停止。 出現提示時，選取 [是]  可停止 VM。 當它解除配置時，選取 [啟動]  可將您的 VM 重新啟動。

將 VM 記錄停止和啟動，可在其活動記錄中記錄事件。 瀏覽回 [變更追蹤]  頁面。 選取頁面底部的 [事件]  索引標籤。 一段時間之後，就會在圖表和資料表中顯示事件。 您可以選取每一個事件來檢視該事件的相關詳細資訊。

![檢視活動記錄中的變更](./media/tutorial-monitoring/manage-activitylog-view-results.png)

圖表會顯示一段時間內已發生的變更。 在您新增活動記錄連線之後，頂端的線條圖表會顯示 Azure 活動記錄事件。 長條圖中的每個資料列都代表不同的可追蹤變更類型。 這些類型是 Linux 精靈、檔案和軟體。 [變更] 索引標籤會以發生變更時間的遞減順序 (最新的優先)，顯示視覺效果中所顯示變更的詳細資料。

## <a name="advanced-monitoring"></a>進階監視

您可使用[適用於 VM 的 Azure 監視器](../../azure-monitor/insights/vminsights-overview.md)進行更進階的 VM 監視，該監視器可藉由分析 Windows 和 Linux VM 的效能和健康情況 (包括其不同的程序以及其他資源和外部程序的互連相依性)，大規模監視 Azure 虛擬機器 (VM)。 Azure VM 的組態管理示透過 [Azure 自動化](../../automation/automation-intro.md)變更追蹤和清查解決方案提供，可輕鬆地識別您環境中的變更。 管理更新合規性是透過 Azure 自動化更新管理解決方案提供。   

從 VM 連線至的 Log Analytics 工作區，您也可以利用[豐富2查詢語言](../../azure-monitor/log-query/log-query-overview.md)來擷取、彙總及分析所收集的資料。 

![Log Analytics 工作區](./media/tutorial-monitoring/tutorial-monitor-oms.png)

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已設定、檢閱和管理 VM 的更新。 您已了解如何︰

> [!div class="checklist"]
> * 啟用 VM 上的開機診斷
> * 檢視開機診斷
> * 檢視主機計量
> * 啟用 VM 上的診斷擴充功能
> * 檢視 VM 計量
> * 依據診斷計量建立警示
> * 管理套件更新
> * 監視變更和清查
> * 設定進階監視

請前進到下一個教學課程，以了解 Azure 資訊安全中心。

> [!div class="nextstepaction"]
> [管理 VM 安全性](../../security/fundamentals/overview.md)
