---
title: 使用適用於 VM 的 Azure 監視器 (預覽) 來監視虛擬機器健康情況 | Microsoft Docs
description: 本文說明您如何使用適用於 VM 的 Azure 監視器，來了解虛擬機器和基礎作業系統的健康情況。
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: azure-monitor
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/22/2019
ms.author: magoedte
ms.openlocfilehash: 9fa76c9637a6dcdca48bf45e8ee2aa9305a4f64f
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "66130446"
---
# <a name="understand-the-health-of-your-azure-virtual-machines"></a>了解 Azure 虛擬機器的健全狀況

Azure 包含多項服務個別在監視空間中，執行特定角色或工作，但還沒有 Azure 虛擬機器上提供一種代管的作業系統的觀點深入的健全狀況。 雖然您無法針對使用 Azure 監視器的不同條件進行監視，它不被設計模型，並代表的核心元件的健康情況或虛擬機器的整體健全狀況。 透過適用於 VM 的 Azure 監視器健康情況功能，它會利用一個模型主動監視 Windows 或 Linux 客體 OS 的可用性和效能，該模型代表重要元件及其關聯性、指定如何測量那些元件健康情況的準則，並在偵測到狀況不良的情況時向您發出警示。  

檢視 Azure VM 和基礎作業系統的整體健康狀態，可以從含有適用於 VM 的 Azure 監視器健康情況的兩個檢視方塊、直接從虛擬機器，或者跨 Azure 監視器中某個資源群組的所有 VM 來觀測。

本文將協助您了解如何快速地評估、調查和解決所偵測到的健康情況問題。

如需設定適用於 VM 的 Azure 監視器相關資訊，請參閱[啟用適用於 VM 的 Azure 監視器](vminsights-enable-overview.md)。

## <a name="monitoring-configuration-details"></a>監視設定詳細資料

本節將概述定義來監視 Azure Windows 和 Linux 虛擬機器的預設健康情況準則。 所有健康情況準則均預先設定為在符合狀況不良的條件時發出警示。 

### <a name="windows-vms"></a>Windows VM

- 記憶體的可用 MB 
- 每次寫入 (邏輯磁碟) 的平均磁碟秒數
- 每次寫入 (磁碟) 的平均磁碟秒數
- 每次讀取的平均邏輯磁碟秒數
- 每次傳輸的平均邏輯磁碟秒數
- 每次讀取的平均磁碟秒數
- 每次傳輸的平均磁碟秒數
- 目前磁碟佇列長度 (邏輯磁碟)
- 目前磁碟佇列長度 (磁碟)
- 磁碟閒置時間百分比
- 檔案系統錯誤或損毀
- 邏輯磁碟可用空間 (%) 低
- 邏輯磁碟可用空間 (MB) 低
- 邏輯磁碟閒置時間百分比
- 每秒記憶體分頁數
- 已用來讀取的頻寬百分比
- 已使用的頻寬百分比總計
- 已用來寫入的頻寬百分比
- 使用中認可的記憶體百分比
- 磁碟閒置時間百分比
- DHCP 用戶端服務健康狀態
- DNS 用戶端服務健康狀態
- RPC 服務健康狀態
- 伺服器服務健康狀態
- CPU 使用率百分比總計
- Windows 事件記錄服務健康狀態
- Windows 防火牆服務健康狀態
- Windows 遠端管理服務健康狀態

### <a name="linux-vms"></a>Linux VM
- 磁碟平均Disk sec/Transfer 
- 磁碟平均Disk sec/Read 
- 磁碟平均Disk sec/Write 
- 磁碟健康情況
- 邏輯磁碟可用空間
- 邏輯磁碟 % 可用空間
- 邏輯磁碟 % 可用閒置空間
- 網路介面卡健康情況
- 處理器時間百分比總計
- 作業系統記憶體的可用 MB

## <a name="sign-in-to-the-azure-portal"></a>登入 Azure 入口網站

登入 [Azure 入口網站](https://portal.azure.com)。 

## <a name="introduction-to-health-experience"></a>健康情況體驗簡介

深入了解如何針對單一虛擬機器或 VM 群組使用健康情況功能之前，重要的是我們將提供一段簡介，讓您能夠了解資訊的呈現方式，以及視覺效果所呈現的內容。  

### <a name="view-health-directly-from-a-virtual-machine"></a>直接從虛擬機器檢視健康情況 

若要檢視 Azure VM 的健康情況，從虛擬機器的左側窗格選取 [Insights (預覽)]  。 在 [VM Insights] 頁面中，預設會開啟 [健康情況]  並顯示 VM 的健康情況檢視。  

![所選取 Azure 虛擬機器之適用於 VM 的 Azure 監視器健康情況概觀](./media/vminsights-health/vminsights-directvm-health.png)

在 [健康情況]  索引標籤的 [客體 VM 健康情況]  區段下方，有個表格會顯示您虛擬機器目前的健康狀態，以及由狀況不良元件所引發的 VM 健康情況警示總數。 如需詳細資訊，請參閱 <<c0> [ 警示](#alerts)的其他詳細資料的警示體驗。  

為 VM 定義的健全狀態如下表所述： 

|圖示 |健康情況狀態 |意義 |
|-----|-------------|---------------|
| |Healthy |如果健全狀態在定義的健康狀況內，則健全狀態狀況良好，表示沒有偵測到 VM 的問題，而且其正依要求運作。 以父彙總套件監視，健全狀況彙總並反映的最佳或最差狀況狀態的子系。|
| |重要 |如果健全狀態不在定義的健康狀況內，則健全狀態屬重大，表示偵測到一或多個重大問題，需要解決這些問題才能恢復正常功能。 以父彙總套件監視，健全狀況彙總並反映的最佳或最差狀況狀態的子系。|
| |警告 |如果健全狀態介於定義之健康狀況的兩個閾值之間，其中一個表示「警告」  狀態，另一個表示「重大」  狀態 (可設定三個健全狀態閾值)，或是偵測到非重大問題，但不解決該問題可能會進一步導致嚴重問題時，在以上兩種情況，健全狀態都會發出警告。 父系彙總套件監視，如果有一個以上的子系處於警告狀態，則會反映出父*警告*狀態。 如果有一個子系處於「重大」  狀態且另一個子系處於「警告」  狀態，則父代彙總將顯示「重大」  的健康狀態。|
| |不明 |健全狀況狀態是*未知*時它無法計算有幾個原因。 請參閱下列的註腳<sup>1</sup>的詳細資訊和可能的解決方案來解決這些問題。 |

<sup>1</sup>不明的健全狀況狀態因下列問題：

- 已重新設定代理程式和工作區的報表不會再指定 Vm 的 Azure 監視器啟用時。 若要設定代理程式工作區，請參閱以報告[新增或移除工作區](../platform/agent-manage.md#adding-or-removing-a-workspace)。
- 已刪除 VM。
- Azure 監視器與 Vm 相關聯的工作區會被刪除。 若要復原的工作區中，如果您有頂級支援的權益，您可以開啟支援要求[頂級](https://premier.microsoft.com/)。
- 已刪除方案相依性。 若要重新啟用 ServiceMap 和 InfrastructureInsights 解決方案在您的 Log Analytics 工作區中，您可以重新安裝使用[Azure Resource Manager 範本](vminsights-enable-at-scale-powershell.md#install-the-servicemap-and-infrastructureinsights-solutions)已提供或使用 [設定] 工作區] 選項，顯示上找到。取得已啟動] 索引標籤。
- VM 已關閉。
- Azure VM 服務無法使用或正在執行維護。
- 工作區[每日資料或保留限制](../platform/manage-cost-storage.md)成立。

選取 [檢視健康情況診斷]  ，即會開啟一個頁面來顯示 VM 的所有元件、相關聯的健康情況準則、狀態變更，以及與 VM 相關之監視元件所遇到的其他重大問題。 如需詳細資訊，請參閱[健康情況診斷](#health-diagnostics)。 

在 [元件健康情況]  區段下方，有個表格會顯示適用於那些區域之健康情況準則所監視的主要效能分類健康情況彙總狀態，特別是 **CPU**、**記憶體**、**磁碟**及**網路**。  選取其中任一個元件，即會開啟一個頁面，列出該元件的所有個別健康情況準則監視層面，以及每個層面各自的健康狀態。  

當存取從執行 Windows 作業系統的 Azure VM 的健全狀況，頂端的健全狀況狀態五個核心的 Windows 服務則會顯示在下一節**核心服務健全狀況**。  選取其中任一個服務，即會開啟一個頁面，列出監視該元件的健康情況準則及其健康狀態。  按一下健康情況準則的名稱將會開啟屬性窗格，而您可以從這裡檢閱設定詳細資料，包括健康情況準則是否已定義對應的 Azure 監視器警示。 若要深入了解，請參閱[健康情況診斷和健康情況的使用方式](#health-diagnostics)。  

### <a name="aggregate-virtual-machine-perspective"></a>彙總虛擬機器檢視方塊

若要檢視資源群組中所有虛擬機器的健康情況集合，從入口網站的瀏覽清單中選取 [Azure 監視器]  ，然後選取 [虛擬機器 (預覽)]  。  

![Azure 監視器中的 VM Insights 監視檢視](./media/vminsights-health/vminsights-aggregate-health.png)

在 [訂用帳戶]  和 [資源群組]  下拉式清單中，選取包含群組相關 VM 的適當資源群組，以檢視系統為其報告的健康狀態。  您的選取只會套用至「健康情況」功能，而不會擴及「效能」或「對應」。

在 [健康情況]  索引標籤上，您可以了解下列內容：

* 有多少部 VM 處於重大或狀況不良狀態，以及有多少部 VM 的狀況良好或未提交資料 (稱為未知狀態)？
* 作業系統 (OS) 回報哪些 VM 處於狀況不良的狀態，且數量有多少？
* 有多少部 VM 因為處理器、磁碟、記憶體或網路介面卡偵測到問題 (依健康狀態分類) 而處於狀況不良的狀態？  
* 有多少部 VM 因為核心作業系統服務偵測到問題 (依健康狀態分類) 而處於狀況不良的狀態？

您可以在這裡快速地識別由主動監視 VM 的健康情況準則所偵測到的前幾個重大問題，並檢閱 VM 健康情況警示的詳細資料，以及旨在協助診斷並修復問題的相關知識文章。  選取任何嚴重性以開啟依照該嚴重性篩選的 [所有警示](../../azure-monitor/platform/alerts-overview.md#all-alerts-page) 頁面。

[依作業系統的 VM 分佈]  清單會顯示依 Windows 版本或 Linux 發行版本列出的 VM 及其版本。 在每個作業系統分類中，都會根據 VM 的健康情況進一步細分 VM。 

![VM Insights 虛擬機器分佈檢視方塊](./media/vminsights-health/vminsights-vmdistribution-by-os.png)

您可以按一下任一個資料行項目：[VM 計數]  、[重大]  、[警告]  、[狀況良好]  或 [未知]  ，向下切入到 [虛擬機器]  頁面以查看符合所選取資料行的篩選結果清單。 例如，如果我們想要檢閱執行 **Red Hat Enterprise Linux 7.5 版**的所有 VM，請按一下該 OS 的 [VM 計數]  值，它將會開啟下列頁面，列出符合該篩選條件的虛擬機器及其目前已知的健康狀態。  

![Red Hat Linux VM 的範例彙總](./media/vminsights-health/vminsights-rollup-vm-rehl-01.png)
 
在 [虛擬機器]  頁面上，如果您選取 [VM 名稱]  資料行下方的 VM 名稱，系統就會將您導向到 VM 執行個體頁面，其中包含更多警示的詳細資料，以及已識別出會影響所選取 VM 的健康情況準則問題。  您可以從這裡按一下頁面左上角的 [健康狀態]  圖示來篩選健康狀態詳細資料，以查看哪些元件狀況不良，或者您可以檢視由依警示嚴重性分類且狀況不良之元件所引發的 VM 健康情況警示。    

從 VM 清單檢視中按一下 VM 的名稱，即會開啟所選取 VM 的 [健康情況]  頁面，就像您直接從 VM 選取 [Insights (預覽)]  一樣。

![所選取 Azure 虛擬機器的 VM Insights](./media/vminsights-health/vminsights-directvm-health.png)

這裡會顯示虛擬機器的彙總**健康狀態**和**警示** (依嚴重性分類)，其會呈現當健康情況準則的健康狀態從狀況良好變更為狀況不良時所引發的 VM 健康情況警示。  選取 [處於重大情況的 VM]  ，將會開啟一個頁面，其中具有一或多部處於重大健康情況狀態的 VM 清單。  在清單中按一下其中一部 VM 的健康狀態，將會顯示該 VM 的 [健康情況診斷]  檢視。  您可以在這裡找出哪些健康情況準則會反映健康狀態問題。 在 [健康情況診斷]  頁面開啟時，它會顯示 VM 的所有元件，以及其具有目前健康狀態的相關聯健康情況準則。 如需詳細資訊，請參閱 <<c0> [ 健全狀況診斷](#health-diagnostics)。  

選取 [檢視所有健康情況準則]  ，即會開啟一個頁面，顯示適用於此功能的所有健康情況準則清單。  您可以根據下列選項進一步篩選資訊：

* **類型**：有三種健康情況準則類型可用來評估情況，並彙總受監視 VM 的整體健康狀態。  
    a. **單位**：測量虛擬機器的某些層面。 這個健康情況準則類型可能會檢查效能計數器來判斷元件的效能、執行指令碼來執行綜合交易，或監看表示發生錯誤的事件。  預設會將篩選條件設為「單位」。  
    b. **相依性**：提供不同實體之間的健康情況彙總。 這個健康情況準則可讓實體的健康情況相依於另一種相依於成功作業之實體的健康情況。  
    c. **彙總**：提供類似健康情況準則的健康狀態組合。 單位和相依性健康情況準則通常將設定於彙總健康情況準則下方。 除了可針對目標設為實體的許多不同健康情況準則提供更好的一般組織方式，彙總健康情況準則還能針對不同的實體分類提供唯一的健康狀態。

* **分類**：健康情況準則的類型，可基於報告目的用來群組類似類型的準則。  它們可以是**可用性**或**效能**。

您可以按一下 [狀況不良的元件]  資料行下方的值，進一步向下切入來查看哪些執行個體的狀況不良。  在頁面上，有個表格會列出處於重大健康情況狀態的元件。    

## <a name="health-diagnostics"></a>健康情況診斷

**健康情況診斷** 頁面可讓您以視覺化方式檢視健康情況模型的 vm 列出 VM 的所有元件相關聯的健全狀況準則、 狀態變更，且可由其他重大問題監視相關的元件VM。

![適用於 VM 的健康情況診斷頁面範例](./media/vminsights-health/health-diagnostics-page-01.png)

您可以利用下列方式啟動健康情況診斷。

* 在 Azure 監視器中來自彙總 VM 檢視方塊之所有 VM 的彙總健康狀態。  在 [健康情況]  頁面的 [客體 VM 健康情況]  區段下方，按一下 [重大]  、[警告]  、[狀況良好]  或 [未知]  健康狀態，並向下切入至會列出所有符合已篩選分類之 VM 的頁面。  按一下 [健康狀態]  資料行，將會開啟已將範圍設為該特定 VM 的健康情況診斷。      

* 來自 Azure 監視器中彙總 VM 檢視方塊的作業系統。 在 [VM 分佈]  下方，選取任一個資料行值將會開啟 [虛擬機器]  頁面，並在表格中傳回符合已篩選分類的清單。  按一下 [健全狀態]  資料行下方的值，即會開啟所選取 VM 的健康情況診斷。    
 
* 從客體 VM 中，在適用於 VM 的 Azure 監視器 [健康情況]  索引標籤上，選取 [檢視健康情況診斷]  

健康情況診斷會將健康情況資訊組織成下列分類： 

* 可用性
* 效能
 
定義特定的元件，例如邏輯磁碟的所有健全狀況準則 CPU 等可檢視不含篩選條件 （也就是所有準則的全面檢視） 的兩個分類，或選取時，篩選的結果依任一類別**可用性**或是**效能**頁面上的選項。 此外，準則的類別可以看到它在旁邊**健全狀況準則**資料行。 如果條件不符合選取的類別，它會顯示訊息**沒有可供所選類別的健全狀況準則**中**健全狀況準則**資料行。  

適用於健全準則的狀態會由下列四種狀態之一來定義：「重大」  、「警告」  、「狀況良好」  及「未知」  。 前三個是可設定的這表示您可以修改直接從 [健全狀況準則組態] 窗格的監視器的臨界值，或使用 Azure 監視器 REST API[更新監視作業](https://docs.microsoft.com/rest/api/monitor/microsoft.workloadmonitor/monitors/update)。 「未知」  狀態無法設定，並保留供特定情況使用。  

[健康情況診斷] 頁面具有三個主要區段：

* 元件模型 
* 健康情況準則
* 狀態變更 

![[健康情況診斷] 頁面的區段](./media/vminsights-health/health-diagnostics-page-02.png)

### <a name="component-model"></a>元件模型

[健康情況診斷] 頁面中最左邊的資料行是元件模型。 所有元件 (與 VM 相關聯) 均會顯示於此資料行及其目前的健全狀態中。 

在下列範例中，探索到的元件為磁碟、邏輯磁碟、處理器、記憶體和作業系統。 將在此資料行中探索及顯示這些元件的多個執行個體。 例如，下圖顯示 VM 具有邏輯磁碟 (C: 和 D:) 的兩個執行個體，皆處於狀況良好的狀態。  

![[健康情況診斷] 中所呈現的範例元件模型](./media/vminsights-health/health-diagnostics-page-component.png)

### <a name="health-criteria"></a>健康情況準則

[健康情況診斷] 頁面上的中間資料行為 [健全準則]  資料行。 針對 VM 定義的健康情況模型會以階層樹狀結構來顯示。 VM 的健康情況模型會由單位和彙總健全準則所組成。  

![[健康情況診斷] 中所呈現的範例健康情況準則](./media/vminsights-health/health-diagnostics-page-healthcriteria.png)

健全準則會使用一些準則來測量受監視執行個體的健康情況，準則可以是閾值、實體狀態等。如先前所述，健全準則具有兩或三個可設定的健全狀態閾值。 在任何指定的時間點，健康情況準則都只能處於它的其中一個可能狀態。 

目標的整體健康情況會從其定義於健康情況模型中每個健全準則的健康情況來判斷。 它是健全狀況準則目標對象為目標，作為目標的彙總至彙總的健康狀態準則透過目標元件的健全狀況準則的組合。 [健康情況診斷] 頁面的 [健康情況準則]  區段中會說明這個階層。 健康情況彙總原則是彙總健全準則設定的一部分 (預設值設為*最差*)。 您可以找到一份預設集的一部分 區段下的這項功能執行的健全狀況準則[監視組態詳細資料](#monitoring-configuration-details)，而且您可以使用 Azure 監視器 REST API[監視執行個體-依資源的清單作業](https://docs.microsoft.com/rest/api/monitor/microsoft.workloadmonitor/monitorinstances/listbyresource)取得一份所有健全狀況準則和 Azure VM 資源上執行其詳細的組態。  

**單位**健全準則類型可藉由按一下最右邊的省略符號連結，然後選取 [顯示詳細資料]  以開啟設定窗格，來修改它們的設定。 

![設定健康情況準則範例](./media/vminsights-health/health-diagnostics-vm-example-02.png)

在選定健全準則的 [設定] 窗格中，使用範例**每次寫入的平均磁碟秒數**，其閾值可以設定不同的數值。 這是雙狀態監視器，亦即狀態只會從「狀況良好」變更為「警告」。 其他健全準則可能是三個狀態，您可以在其中設定警告和重大健全狀態閾值的值。 您也可以修改使用 Azure 監視器 REST API 的臨界值[更新監視作業](https://docs.microsoft.com/rest/api/monitor/microsoft.workloadmonitor/monitors/update)。

>[!NOTE]
>將健全準則設定變更套用到一個執行個體後，亦適用於所有受監視的執行個體。  例如，如果您選取**磁碟 -1 D:** ，並修改**每次寫入的平均磁碟秒數**閾值，它不只會套用到該執行個體，還會套用到所有在 VM 上探索到且受監視的其他磁碟執行個體。
>

![設定單位監視器範例的健康情況準則](./media/vminsights-health/health-diagnostics-criteria-config-01.png)

如果您想要深入了解健康情況指標，有一些知識文章可協助您找出問題、原因和解決方式。 按一下頁面上的 [檢視資訊]  連結可在瀏覽器中開啟新的索引標籤，其中顯示特定的知識文章。 您可以在[這裡](https://docs.microsoft.com/azure/monitoring/infrastructure-health/)隨時檢閱適用於 VM 的 Azure 監視器健康情況功能隨附的所有健康情況準則知識文章。
  
### <a name="state-changes"></a>狀態變更

[健康情況診斷] 頁面中最右邊的資料行是 [狀態變更]  。 它會列出與在 [健康情況準則]  區段中所選取健康狀態準則相關聯的所有狀態變更，或者，如果 VM 選取自資料表的 [元件模型]  或 [健康情況準則]  資料行，則會列出該 VM 的狀態變更。 

![[健康情況診斷] 中所呈現的範例狀態變更](./media/vminsights-health/health-diagnostics-page-statechanges.png)

此區段會由健康情況準則狀態，以及由最上方之最新狀態所儲存的相關聯時間所組成。   

### <a name="association-of-component-model-health-criteria-and-state-change-columns"></a>關聯的元件模型、 健全狀況準則和狀態變更的資料行 

這三個資料行彼此交互連結。 當您在 [元件模型]  區段中選取探索到的執行個體時，會將 [健全準則]  區段篩選到該元件檢視，並根據所選取的健全準則相應更新 [狀態變更]  區段。 

![選取受監視的執行個體和結果範例](./media/vminsights-health/health-diagnostics-vm-example-01.png)

在上述範例中，當您選取**磁碟 - 1 D:** 時，即會將 [健全準則] 樹狀目錄篩選為**磁碟 - 1D:** 。 [狀態變更]  資料行會根據**磁碟 - 1 D:** 的可用性顯示狀態變更。 

若要查看更新的健康狀態，您可以按一下 [重新整理]  連結，重新整理 [健康情況診斷] 頁面。  如果要基於預先定義的輪詢間隔來更新健康情況準則的健康狀態，此工作可讓您避免等候，並反映最新的健康狀態。  [健全準則狀態]  是一個篩選條件，可讓您根據所選取的健全狀態 ([狀況良好]  、[警告]  、[重大]  、[未知]  ，以及 [全部]  ) 來設定結果範圍。  右上角的**上次更新**時間表示上次重新整理 [健康情況診斷] 頁面時的時間。  

## <a name="alerts"></a>警示

適用於 VM 的 Azure 監視器健康情況功能會與 [Azure 警示](../../azure-monitor/platform/alerts-overview.md)整合，並在預先定義的健康情況準則於偵測到狀況時從狀況良好變更為狀況不良狀態時引發警示。 警示會依嚴重性 (嚴重性 0 到 4，嚴重性 0 表示最高的嚴重性層級) 分類。 

無法在觸發警示時通知您的動作群組相關聯的警示。 訂用帳戶擁有者必須設定通知的步驟[本主題稍後的](#configure-alerts)。   

依嚴重性分類的 VM 健康情況警示總數可在 [健康情況]  儀表板的 [警示]  區段下方取得。 當您選取警示總數或對應到嚴重性層級的數字時，[警示]  頁面隨即開啟並列出所有符合您選取項目的警示。  例如，如果您選取了對應至**嚴重性層級 1** 的資料列，則會看到下列檢視：

![所有嚴重性層級 1 的警示範例](./media/vminsights-health/vminsights-sev1-alerts-01.png)

[警示]  頁面上不僅可顯示與您的選取項目相符的警示，也可藉由**資源類型**的篩選而僅顯示虛擬機器資源所引發的健康情況警示。  它會反映在警示的清單，資料行底下**目標資源**，它會顯示符合特定的健全狀況準則的狀況不良的條件時引發警示的 Azure VM。  

從其他的資源類型或服務的警示不適合包含在此檢視，例如記錄檔查詢為基礎的記錄警示，或計量警示，您通常會檢視從預設 Azure 監視器[所有警示](../../azure-monitor/platform/alerts-overview.md#all-alerts-page)頁面。 

您可以選取頁面頂端下拉式功能表中的值來篩選此檢視。

|欄 |描述 | 
|-------|------------| 
|訂用帳戶 |選取 Azure 訂用帳戶。 檢視僅會包含所選訂用帳戶中出現的警示。 | 
|資源群組 |選取單一資源群組。 檢視僅會包含所選資源群組中具有目標的警示。 | 
|資源類型 |選取一個或多個資源類型。 依預設只會選取目標**虛擬機器**的警示，並將其包含在此檢視中。 指定資源群組之後，才可使用此欄。 | 
|資源 |選取資源。 只有以該資源作為目標的警示才會包含在檢視中。 指定資源類型之後，才可使用此欄。 | 
|Severity |選取警示嚴重性，或選取 [全部]  以包含所有嚴重性的警示。 | 
|監視條件 |選取監視條件來篩選警示，但前提是系統已「引發」  它們，或者如果該條件不再使用中，則系統已「解決」  它們。 或者，選取 [全部]  來包含所有條件的警示。 | 
|警示狀態 |選取警示狀態 (「新的」  、「認可」  、「已關閉」  )，或選取 [全部]  以包含所有狀態的警示。 | 
|監視器服務 |選取服務，或選取 [所有]  以包含所有服務。 此功能僅支援來自 VM Insights  的警示。| 
|時間範圍| 只有在所選時間範圍內引發的警示才會包含在檢視中。 支援的值為過去 1 小時、過去 24 小時、過去 7 天和過去 30 天。 | 

[警示詳細資料]  頁面會在您選取警示時顯示，以提供警示的詳細資料並讓您變更其狀態。 若要深入了解如何管理警示，請參閱[使用 Azure 監視器來建立、檢視及管理警示](../../azure-monitor/platform/alerts-metric.md)。  

>[!NOTE]
>目前並不支援根據健全準則建立新的警示，或從入口網站修改 Azure 監視器中現有的健康情況警示規則。  
>

![所選取警示的 [警示詳細資料] 窗格](./media/vminsights-health/alert-details-pane-01.png)

您也可以藉由選取警示，然後從 [所有警示]  頁面的左上角選取 [變更狀態]  ，針對一或多個警示變更警示狀態。 在 [變更警示狀態]  窗格中，您會選取其中一種狀態、在 [註解]  欄位中新增變更的說明，然後按一下 [確定]  來認可變更。 在驗證資訊並套用變更之後，您可以在功能表的 [通知]  底下追蹤其進度。  

### <a name="configure-alerts"></a>設定警示
特定的工作無法從 Azure 入口網站進行管理，而且必須使用執行警示管理[Azure 監視器 REST API](https://docs.microsoft.com/rest/api/monitor/microsoft.workloadmonitor/components)。 具體而言：

- 啟用或停用健全狀況準則的警示 
- 設定健全狀況準則的警示通知 

每個範例中使用的方法使用[ARMClient](https://github.com/projectkudu/armclient) Windows 電腦上。 如果您不熟悉此方法，請參閱[使用 ARMClient](../platform/rest-api-walkthrough.md#use-armclient)。  

#### <a name="enable-or-disable-alert-rule"></a>啟用或停用警示規則

啟用或停用特定的健全狀況準則，健全狀況準則屬性的警示*alertGeneration*需要使用的值修改**停用**或**Enabled**. 若要找出*monitorId*特定的健全狀況準則的下列範例將示範如何查詢準則該值**LogicalDisk\Avg Disk 秒 Per Transfer**。

1. 在終端機視窗中，輸入 armclient.exe login  。 如此一來，會提示您登入 Azure。

2. 輸入下列命令來擷取特定的虛擬機器上使用所有健康狀態準則，並識別的值*monitorId*屬性。 

    ```
    armclient GET "subscriptions/subscriptionId/resourceGroups/resourcegroupName/providers/Microsoft.Compute/virtualMachines/vmName/providers/Microsoft.WorkloadMonitor/monitors?api-version=2018-08-31-preview”
    ```

    下列範例會顯示該命令的輸出。 請記下的值*MonitorId*。 這是必要值的下一步我們要指定健康條件的識別碼，並修改其屬性，來建立警示。

    ```
    "id": "/subscriptions/a7f23fdb-e626-4f95-89aa-3a360a90861e/resourcegroups/Lab/providers/Microsoft.Compute/virtualMachines/SVR01/providers/Microsoft.WorkloadMonitor/monitors/ComponentTypeId='LogicalDisk',MonitorId='Microsoft_LogicalDisk_AvgDiskSecPerRead'",
      "name": "ComponentTypeId='LogicalDisk',MonitorId='Microsoft_LogicalDisk_AvgDiskSecPerRead'",
      "type": "Microsoft.WorkloadMonitor/virtualMachines/monitors"
    },
    {
      "properties": {
        "description": "Monitor the performance counter LogicalDisk\\Avg Disk Sec Per Transfer",
        "monitorId": "Microsoft_LogicalDisk_AvgDiskSecPerTransfer",
        "monitorName": "Microsoft.LogicalDisk.AvgDiskSecPerTransfer",
        "monitorDisplayName": "Average Logical Disk Seconds Per Transfer",
        "parentMonitorName": null,
        "parentMonitorDisplayName": null,
        "monitorType": "Unit",
        "monitorCategory": "PerformanceHealth",
        "componentTypeId": "LogicalDisk",
        "componentTypeName": "LogicalDisk",
        "componentTypeDisplayName": "Logical Disk",
        "monitorState": "Enabled",
        "criteria": [
          {
            "healthState": "Warning",
            "comparisonOperator": "GreaterThan",
            "threshold": 0.1
          }
        ],
        "alertGeneration": "Enabled",
        "frequency": 1,
        "lookbackDuration": 17,
        "documentationURL": "https://aka.ms/Ahcs1r",
        "configurable": true,
        "signalType": "Metrics",
        "signalName": "VMHealth_Avg. Logical Disk sec/Transfer"
      },
      "etag": null,
    ```

3. 輸入下列命令來修改*alertGeneration*屬性。

    ```
    armclient patch subscriptions/subscriptionId/resourceGroups/resourcegroupName/providers/Microsoft.Compute/virtualMachines/vmName/providers/Microsoft.WorkloadMonitor/monitors/Microsoft_LogicalDisk_AvgDiskSecPerTransfer?api-version=2018-08-31-preview "{'properties':{'alertGeneration':'Disabled'}}"
    ```   

4. 輸入在步驟 2 中用於驗證之屬性的值設定為 GET 命令**已停用**。  

#### <a name="associate-action-group-with-health-criteria"></a>健全狀況準則相關聯動作群組

Azure Vm 的健全狀況監視器就會產生警示時，支援 SMS 和電子郵件通知時健康狀態準則會變為狀況不良。 若要設定通知，您需要記下已傳送簡訊或電子郵件通知的動作群組的名稱。 

>[!NOTE]
>這個動作需要執行對監視每個 VM，您想要接收的通知，它不會套用至資源群組中的所有 Vm。  

1. 在終端機視窗中，輸入 armclient.exe login  。 如此一來，會提示您登入 Azure。

2. 輸入下列命令來建立動作群組關聯的警示規則。
 
    ```
    $payload = "{'properties':{'ActionGroupResourceIds':['/subscriptions/subscriptionId/resourceGroups/resourcegroupName/providers/microsoft.insights/actionGroups/actiongroupName']}}" 
    armclient PUT https://management.azure.com/subscriptions/subscriptionId/resourceGroups/resourcegroupName/providers/Microsoft.Compute/virtualMachines/vmName/providers/Microsoft.WorkloadMonitor/notificationSettings/default?api-version=2018-08-31-preview $payload
    ```

3. 若要確認屬性的值**actionGroupResourceIds**已成功更新，輸入下列命令。

    ```
    armclient GET "subscriptions/subscriptionName/resourceGroups/resourcegroupName/providers/Microsoft.Compute/virtualMachines/vmName/providers/Microsoft.WorkloadMonitor/notificationSettings?api-version=2018-08-31-preview"
    ```

    輸出應該如以下所示：
    
    ```
    {
      "value": [
        {
          "properties": {
            "actionGroupResourceIds": [
              "/subscriptions/a7f23fdb-e626-4f95-89aa-3a360a90861e/resourceGroups/Lab/providers/microsoft.insights/actionGroups/Lab-IT%20Ops%20Notify"
            ]
          },
          "etag": null,
          "id": "/subscriptions/a7f23fdb-e626-4f95-89aa-3a360a90861e/resourcegroups/Lab/providers/Microsoft.Compute/virtualMachines/SVR01/providers/Microsoft.WorkloadMonitor/notificationSettings/default",
          "name": "notificationSettings/default",
          "type": "Microsoft.WorkloadMonitor/virtualMachines/notificationSettings"
        }
      ],
      "nextLink": null
    }
    ```

## <a name="next-steps"></a>後續步驟

若要找出 VM 效能的瓶頸和整體使用率，請參閱[檢視 Azure VM 效能](vminsights-performance.md)，或者，若要檢視探索到的應用程式相依性，請參閱[檢視適用於 VM 的 Azure 監視器對應](vminsights-maps.md)。 
