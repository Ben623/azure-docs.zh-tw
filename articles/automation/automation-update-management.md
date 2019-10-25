---
title: Azure 中的更新管理解決方案
description: 本文說明如何使用 Azure 更新管理解決方案來管理 Windows 和 Linux 電腦的更新。
services: automation
ms.service: automation
ms.subservice: update-management
author: bobbytreed
ms.author: robreed
ms.date: 05/22/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 66acb1284f0814eec91715284259272a065dbae2
ms.sourcegitcommit: e0e6663a2d6672a9d916d64d14d63633934d2952
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/21/2019
ms.locfileid: "72690902"
---
# <a name="update-management-solution-in-azure"></a>Azure 中的更新管理解決方案

您可以使用 Azure 自動化中的更新管理解決方案，在 Azure、內部部署環境及其他雲端提供者中管理 Windows 和 Linux 電腦的作業系統更新。 您可以快速評估所有代理程式電腦上可用更新的狀態，並管理為伺服器安裝必要更新的程序。

您可以直接從您的 Azure 自動化帳戶啟用虛擬機器（Vm）的更新管理。 若要瞭解作法，請參閱[管理多部虛擬機器的更新](manage-update-multi.md)。 您也可以從 Azure 入口網站的 [虛擬機器] 頁面中，啟用 VM 的更新管理。 此案例適用于[Linux](../virtual-machines/linux/tutorial-config-management.md#enable-update-management)和[Windows](../virtual-machines/windows/tutorial-config-management.md#enable-update-management) vm。

> [!NOTE]
> 更新管理解決方案需要將 Log Analytics 工作區連結至您的自動化帳戶。 如需所支援區域的確切清單，請參閱[Azure 工作區](./how-to/region-mappings.md)對應。 區域對應並不會影響從您的自動化帳戶在不同的區域中管理 Vm 的能力。

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="solution-overview"></a>解決方案概觀

受更新管理管理的電腦會使用下列設定來執行評估和更新部署：

* 適用於 Windows 或 Linux 的 Microsoft Monitoring Agent (MMA)
* 適用於 Linux 的 PowerShell 預期狀態組態 (DSC)
* 自動化 Hybrid Runbook Worker
* 適用於 Windows 電腦的 Microsoft Update 或 Windows Server Update Services (WSUS)

下圖說明解決方案如何評估並將安全性更新套用至工作區中所有已連線的 Windows Server 和 Linux 電腦：

![更新管理程序流程](./media/automation-update-management/update-mgmt-updateworkflow.png)

您可以使用更新管理在相同租用戶中的多個訂用帳戶中以原生方式上架機器。

發行套件之後，需要2到3小時的時間，才會針對 Linux 電腦顯示修補程式以供評量。 對於 Windows 機器，需要12到15小時的時間，修補程式才會在發行後顯示進行評估。

在電腦完成更新相容性掃描之後，代理程式會將大量資訊轉送至 Azure 監視器記錄。 在 Windows 電腦上，合規性掃描預設會每 12 小時執行一次。

除了掃描排程，如果在更新安裝之前與更新安裝之後重新啟動 MMA，則會在 15 分鐘內起始更新合規性掃描。

針對 Linux 電腦，預設會每小時執行合規性掃描。 若 MMA 代理程式重新啟動，則會在 15 分鐘內起始合規性掃描。

解決方案會根據您設定要同步處理的來源，回報電腦的最新狀態。 如果 Windows 電腦設定為向 WSUS 報告，視 WSUS 上次與 Microsoft Update 同步處理的時間而定，結果可能會與 Microsoft Update 所顯示的不同。 針對設定為向本機存放庫 (而非公用存放庫) 回報的 Linux 電腦，此行為也相同。

> [!NOTE]
> 為了能正確地向服務回報，「更新管理」需要啟用特定 URL 和連接埠。 若要深入了解這些需求，請參閱[混合式背景工作角色的網路規劃](automation-hybrid-runbook-worker.md#network-planning)。

您可以藉由建立排定的部署，在需要更新的電腦上部署和安裝軟體更新。 歸類為「選擇性」的更新不會包含在 Windows 電腦的部署範圍內。 部署範圍中僅包含必要更新。

排定的部署會定義哪些目的電腦會收到適用的更新。 其方式是明確指定特定電腦，或選取以一組特定電腦的記錄搜尋為基礎的[電腦群組](../azure-monitor/platform/computer-groups.md)（或在根據指定準則動態選取 azure Vm 的[azure 查詢](automation-update-management-query-logs.md)上）。 這些群組與[範圍](../azure-monitor/insights/solution-targeting.md)設定不同，僅用於判斷哪些機器取得可啟用解決方案的管理元件。

您也可以指定要核准的排程，並設定可安裝更新的時段。 這段期間稱為維護視窗。 維護期間的20分鐘範圍會保留重新開機，前提是需要一個，而且您已選取適當的重新開機選項。 如果修補所花費的時間超出預期，且維護期間內的時間少於20分鐘，就不會進行重新開機。

在 Azure 自動化中，會由 Runbook 安裝更新。 您無法查看這些 runbook，而且它們不需要任何設定。 建立更新部署時，它會建立一個排程，以在指定的時間為所包含的電腦啟動主要更新 runbook。 主要 Runbook 會在每個代理程式上啟動子 Runbook，以安裝必要的更新。

到了在更新部署中指定的日期和時間時，目標電腦會以平行方式執行部署。 在安裝之前，系統會先執行掃描，以確認仍然需要更新。 對於 WSUS 用戶端電腦而言，如果未在 WSUS 中核准更新，則更新部署會失敗。

不支援在一個以上的 Log Analytics 工作區（多路連接）中註冊更新管理的機器。

## <a name="clients"></a>用戶端

### <a name="supported-client-types"></a>支援的用戶端類型

下表列出更新評估支援的作業系統。 修補需要混合式 Runbook 背景工作角色。 如需混合式 Runbook 背景工作角色需求的詳細資訊，請參閱安裝[Windows 混合式 runbook 背景工作角色](automation-windows-hrw-install.md#installing-the-windows-hybrid-runbook-worker)和[Linux 混合式 Runbook 背景工作角色](automation-linux-hrw-install.md#installing-a-linux-hybrid-runbook-worker)的安裝指南。

|作業系統  |注意事項  |
|---------|---------|
|Windows Server 2019 （Datacenter/Datacenter Core/Standard）<br><br>Windows Server 2016 （Datacenter/Datacenter Core/Standard）<br><br>Windows Server 2012 R2 （Datacenter/Standard）<br><br>Windows Server 2012<br><br>Windows Server 2008 R2 （RTM 和 SP1 標準）||
|CentOS 6 (x86/x64) 和 7 (x64)      | Linux 代理程式必須能夠存取更新存放庫。 以分類為基礎的修補需要 `yum` 傳回其 RTM 版本中 CentOS 沒有的安全性資料。 如需 CentOS 上以分類為基礎之修補的詳細資訊，請參閱[Linux 上的更新分類](#linux-2)。          |
|Red Hat Enterprise 6 (x86/x64) 和 7 (x64)     | Linux 代理程式必須能夠存取更新存放庫。        |
|SUSE Linux Enterprise Server 11 (x86/x64) 和 12 (x64)     | Linux 代理程式必須能夠存取更新存放庫。        |
|Ubuntu 14.04 LTS、16.04 LTS 和 18.04 (x86/x64)      |Linux 代理程式必須能夠存取更新存放庫。         |

> [!NOTE]
> 您可以透過更新管理來管理 Azure 虛擬機器擴展集。 更新管理適用于實例本身，而不是基底映射。 您需要以累加方式排程更新，如此一來，就不會一次更新所有 VM 實例。
> 您可以遵循 [在[非 Azure 機器上架](automation-tutorial-installed-software.md#onboard-a-non-azure-machine)] 底下的步驟來新增虛擬機器擴展集的節點。

### <a name="unsupported-client-types"></a>不支援的用戶端類型

下表列出不支援的作業系統：

|作業系統  |注意事項  |
|---------|---------|
|Windows 用戶端     | 不支援用戶端作業系統 (例如 Windows 7 和 Windows 10)。        |
|Windows Server 2016 Nano Server     | 不支援。       |
|Azure Kubernetes Service 節點 | 不支援。 使用在[Azure Kubernetes Service 中將安全性和核心更新套用至 Linux 節點](../aks/node-updates-kured.md)中所述的修補程式（AKS）|

### <a name="client-requirements"></a>用戶端需求

下列資訊描述 OS 特定的用戶端需求。 如需其他指引，請參閱[網路規劃](#ports)。

#### <a name="windows"></a>Windows

Windows 代理程式必須設定為與 WSUS 伺服器通訊，或必須具有 Microsoft Update 的存取權。

您可以搭配 System Center Configuration Manager 使用「更新管理」。 若要深入了解整合案例，請參閱[整合 System Center Configuration Manager 與更新管理](oms-solution-updatemgmt-sccmintegration.md#configuration)。 需要 [Windows 代理程式](../azure-monitor/platform/agent-windows.md)。 如果您要將 Azure VM 上架，則會自動安裝代理程式。

根據預設，從 Azure Marketplace 部署的 Windows Vm 會設定為從 Windows Update 服務接收自動更新。 當您新增此解決方案或將 Windows Vm 新增至工作區時，此行為不會變更。 如果您未使用這個解決方案來主動管理更新，則會套用預設行為 (自動套用更新)。

> [!NOTE]
> 使用者可以修改群組原則，讓電腦重新開機只能由使用者執行，而不是由系統執行。 如果更新管理沒有重新開機電腦的許可權，而沒有使用者的手動互動，則受管理的機器可能會停滯。
>
> 如需詳細資訊，請參閱[設定自動更新的群組原則設定](https://docs.microsoft.com/windows-server/administration/windows-server-update-services/deploy/4-configure-group-policy-settings-for-automatic-updates)。

#### <a name="linux"></a>Linux

針對 Linux，機器必須能夠存取更新存放庫。 更新存放庫可以是私人或公用。 必須使用 TLS 1.1 或 TLS 1.2，才能與更新管理互動。 此解決方案不支援適用于 Linux 且設定為向多個 Log Analytics 工作區報告的 Log Analytics 代理程式。 電腦也必須安裝 Python 2.x。

如需有關如何安裝適用于 Linux 的 Log Analytics 代理程式及下載最新版本的詳細資訊，請參閱[適用于 linux 的 Log Analytics 代理程式](https://github.com/microsoft/oms-agent-for-linux)。 如需有關如何安裝適用于 Windows 的 Log Analytics 代理程式的詳細資訊，請參閱[將 Windows 電腦連線到 Azure 監視器](../log-analytics/log-analytics-windows-agent.md)。

從 Azure Marketplace 中提供的隨選 Red Hat Enterprise Linux （RHEL）映射所建立的 Vm，會進行註冊以存取部署在 Azure 中的[Red Hat 更新基礎結構（RHUI）](../virtual-machines/virtual-machines-linux-update-infrastructure-redhat.md) 。 任何其他 Linux 發行版本都必須使用發佈的支援方法，從發佈的線上檔案存放庫更新。

## <a name="permissions"></a>使用權限

若要建立及管理更新部署，您必須具有特定權限。 若要瞭解這些許可權，請參閱以[角色為基礎的存取–更新管理](automation-role-based-access-control.md#update-management)。

## <a name="solution-components"></a>解決方案元件

解決方案包含下列資源。 資源會新增到您的自動化帳戶。 它們是直接連線的代理程式或是位於連線到 Operations Manager 的管理群組。

### <a name="hybrid-worker-groups"></a>混合式背景工作群組

啟用此解決方案之後，任何直接連線到 Log Analytics 工作區的 Windows 電腦都會自動設定為「混合式 Runbook 背景工作角色」，以支援此解決方案中所包含的 Runbook。

此解決方案管理的每部 Windows 電腦都會列在自動化帳戶的 [混合式背景工作角色群組] 窗格中，作為自動化帳戶的**系統混合式背景工作角色群組**。 解決方案會使用*HOSTNAME FQDN_GUID*命名慣例。 您不能讓這些群組以您帳戶中的 Runbook 為目標。 如果您嘗試，則嘗試會失敗。 這些群組只支援管理解決方案。

您可以將 Windows 電腦新增到自動化帳戶中的「混合式 Runbook 背景工作角色」群組來支援自動化 Runbook，只要解決方案和「混合式 Runbook 背景工作角色」群組成員資格兩者所用的帳戶相同即可。 此功能已新增至混合式 Runbook 背景工作角色 7.2.12024.0 版。

### <a name="management-packs"></a>管理組件

如果 System Center Operations Manager 管理群組已連線到 Log Analytics 工作區，則下列管理組件會安裝在 Operations Manager 中。 新增此解決方案之後，這些管理組件也會安裝在直接連線的 Windows 電腦上。 您不需要設定或管理這些管理組件。

* Microsoft System Center Advisor 更新評估智慧套件 (Microsoft.IntelligencePacks.UpdateAssessment)
* Microsoft.IntelligencePack.UpdateAssessment.Configuration (Microsoft.IntelligencePack.UpdateAssessment.Configuration)
* 更新部署 MP

> [!NOTE]
> 假設您有一個 Operations Manager 1807 或2019管理群組，並在管理群組層級設定代理程式，以將它們與工作區產生關聯。 要讓它們顯示的目前因應措施是在**microsoft.intelligencepacks.applicationdependencymonitor. AzureAutomation. microsoft.enterprisemanagement.healthservice.azureautomation.hybridagent 事件**中，將**IsAutoRegistrationEnabled**覆寫為**True** 。

如需有關方案管理元件如何更新的詳細資訊，請參閱[將 Operations Manager 連接到 Azure 監視器記錄](../azure-monitor/platform/om-agents.md)。

> [!NOTE]
> 針對具有 Operations manager 代理程式的系統：若要讓代理程式完全受更新管理管理，必須將代理程式更新為 MMA。 若要深入了解如何更新代理程式，請參閱[如何升級 Operations Manager 代理程式](https://docs.microsoft.com/system-center/scom/deploy-upgrade-agents)。 在使用 Operations Manager 的環境中，您必須執行 System Center Operations Manager 2012 R2 UR 14 或更新版本。

## <a name="data-collection"></a>資料收集

### <a name="supported-agents"></a>支援的代理程式

下表描述本解決方案支援的連線來源：

| 連線的來源 | 支援 | 說明 |
| --- | --- | --- |
| Windows 代理程式 |是 |解決方案會從 Windows 代理程式收集系統更新的相關資訊，然後起始必要更新的安裝。 |
| Linux 代理程式 |是 |解決方案會從 Linux 代理程式收集系統更新的相關資訊，然後在支援的發行版本上起始必要更新的安裝。 |
| Operations Manager 管理群組 |是 |方案會從所連線之管理群組中的代理程式收集系統更新的相關資訊。<br/><br/>不需要從 Operations Manager 代理程式直接連接到 Azure 監視器記錄。 資料會從管理群組轉送至 Log Analytics 工作區。 |

### <a name="collection-frequency"></a>收集頻率

針對每部受控 Windows 電腦，每天會掃描兩次。 系統會每隔 15 分鐘呼叫一次 Windows API 來查詢上次更新時間，以判斷狀態是否已變更。 如果狀態已變更，則會起始合規性掃描。

每隔一小時就會針對每部受管理的 Linux 電腦執行掃描。

儀表板可能需要 30 分鐘到 6 小時，才能顯示來自受控電腦的已更新資料。

使用更新管理 Azure 監視器電腦記錄的平均資料使用量大約是每月 25 mb。 這個值只是近似值，視您的環境而定，可能會變更。 我們建議您監視您的環境，以追蹤確切的使用方式。

## <a name="ports"></a>網路規劃

以下為「更新管理」特別需求的位址。 與這些位址的通訊皆經由連接埠 443 進行。

|Azure 公用  |Azure Government  |
|---------|---------|
|*.ods.opinsights.azure.com     |*.ods.opinsights.azure.us         |
|*.oms.opinsights.azure.com     | *.oms.opinsights.azure.us        |
|*.blob.core.windows.net|*.blob.core.usgovcloudapi.net|
|*.azure-automation.net|*.azure-automation.us|

對於 Windows 機器，您也必須允許流量傳送至 Windows Update 所需的任何端點。 您可以在[與 HTTP/Proxy 相關的問題](/windows/deployment/update/windows-update-troubleshooting#issues-related-to-httpproxy)中，找到所需端點的更新清單。 如果您有本機[Windows Update 伺服器](/windows-server/administration/windows-server-update-services/plan/plan-your-wsus-deployment)，您也必須允許流向[WSUS 金鑰](/windows/deployment/update/waas-wu-settings#configuring-automatic-updates-by-editing-the-registry)中所指定伺服器的流量。

對於 Red Hat Linux 電腦，請參閱必要端點[的 RHUI 內容傳遞伺服器的 ip](../virtual-machines/linux/update-infrastructure-redhat.md#the-ips-for-the-rhui-content-delivery-servers) 。 如需其他 Linux 發行版本，請參閱您的提供者檔。

如需混合式 Runbook 背景工作角色所需連接埠的詳細資訊，請參閱[混合式背景工作角色連接埠](automation-hybrid-runbook-worker.md#hybrid-worker-role)。

我們建議您在定義例外狀況時使用所列的位址。 針對 IP 位址，您可以下載[Microsoft Azure 資料中心 IP 範圍](https://www.microsoft.com/download/details.aspx?id=41653)。 此檔案會每週更新，並反映目前已部署的範圍及任何即將進行的 IP 範圍變更。

依照[連接沒有網際網路存取的電腦](../azure-monitor/platform/gateway.md)中的指示，設定無法存取網際網路的電腦。

## <a name="view-update-assessments"></a>查看更新評量

在您的自動化帳戶中，選取 [更新管理] 來檢視機器的狀態。

此檢視會提供您的機器、缺少的更新、更新部署以及已排定之更新部署的相關資訊。 在 [**相容性**] 欄中，您可以看到上次評估電腦的時間。 在 [**更新代理程式準備就緒**] 欄位中，您可以檢查更新代理程式的健全狀況。 如果發生問題，請選取 [移至疑難排解檔] 連結，以協助您更正問題。

若要執行會傳回機器、更新或部署相關資訊的記錄檔搜尋，請選取清單中的對應專案。 [記錄搜尋] 窗格隨即開啟，並顯示所選項目的查詢：

![更新管理的預設檢視](media/automation-update-management/update-management-view.png)

## <a name="view-missing-updates"></a>檢視缺少的更新

選取 [缺少的更新] 以檢視機器缺少的更新清單。 會列出每個更新，而且您可以選取更新。 畫面上會顯示需要更新的機器數目、作業系統以及一個能提供詳細資訊的連結。 [記錄搜尋] 窗格會顯示與更新有關的詳細資訊。

![缺少更新](./media/automation-view-update-assessments/automation-view-update-assessments-missing-updates.png)

## <a name="update-classifications"></a>更新分類

下表列出「更新管理」中的更新分類清單，以及每個分類的定義。

### <a name="windows"></a>Windows

|分類  |說明  |
|---------|---------|
|重大更新     | 特定問題的更新，負責處理與安全性無關的重大錯誤。        |
|安全性更新     | 特定產品的安全性相關更新。        |
|更新彙總套件     | 一組累計的 Hotfix，封裝在一起以便於部署。        |
|Feature Pack     | 在產品版本之外散發的新產品功能。        |
|Service Pack     | 一組套用到應用程式的累計 Hotfix。        |
|定義更新     | 病毒或其他定義檔案的更新。        |
|工具     | 有助於完成一或多個工作的公用程式或功能。        |
|更新     | 目前安裝之應用程式或檔案的更新。        |

### <a name="linux-2"></a>Linux

|分類  |說明  |
|---------|---------|
|重大更新和安全性更新     | 特定問題或特定產品的安全性相關問題的更新，         |
|其他更新     | 本質上不重要或不是安全性更新的所有其他更新。        |

針對 Linux，更新管理可以區別雲端中的重大更新和安全性更新，同時顯示因雲端中的資料擴充而產生的評量資料。 針對修補，「更新管理」仰賴機器上可用的分類資料。 與其他散發套件不同的是，CentOS 不會在 RTM 版本中提供這項資訊。 如果您已將 CentOS 機器設定為傳回下列命令的安全性資料，更新管理可以根據分類進行修補。

```bash
sudo yum -q --security check-update
```

目前沒有支援的方法可在 CentOS 上啟用原生分類資料可用性。 此時，只有可能已自行啟用此功能的客戶才會提供最佳支援。

## <a name="integrate-with-system-center-configuration-manager"></a>與 System Center Configuration Manager 進行整合

投資了 System Center Configuration Manager 來管理電腦、伺服器和行動裝置的客戶也需仰賴 Configuration Manager 的強度和成熟度，以協助他們管理軟體更新。 Configuration Manager 是其軟體更新管理 (SUM) 週期的一部分。

若要了解如何將管理解決方案與 Sytem Center Configuration Manager 整合，請參閱[將 System Center Configuration Manager 與更新管理整合](oms-solution-updatemgmt-sccmintegration.md)。

### <a name="third-party-patches-on-windows"></a>Windows 上的協力廠商修補程式

更新管理依賴本機設定的更新存放庫來修補支援的 Windows 系統。 這可能是 WSUS 或 Windows Update。 像 [System Center Updates Publisher](/sccm/sum/tools/updates-publisher) (Updates Publisher) 這樣的工具，允許您將自訂更新發佈至 WSUS。 此案例可讓更新管理利用協力廠商軟體，修補使用 System Center Configuration Manager 作為其更新存放庫的機器。 若要了解如何設定 Updates Publisher，請參閱[安裝Updates Publisher](/sccm/sum/tools/install-updates-publisher)。

## <a name="patch-linux-machines"></a>修補 Linux 機器

以下各節說明使用 Linux 修補的潛在問題。

### <a name="unexpected-os-level-upgrades"></a>未預期的 OS 層級升級

在某些 Linux 變體（例如 Red Hat Enterprise Linux）上，可能會透過套件進行 OS 層級的升級。 這可能會導致「更新管理」在 OS 版本號碼變更時執行。 因為「更新管理」會使用與系統管理員在本機 Linux 電腦上相同的方式來更新套件，因此這個行為是刻意的。

若要避免透過更新管理執行來更新 OS 版本，請使用**排除**功能。

在 Red Hat Enterprise Linux 中，要排除的套件名稱將是：redhat-release-server.x86_64。

![要為 Linux 排除的套件](./media/automation-update-management/linuxpatches.png)

### <a name="criticalsecurity-patches-arent-applied"></a>不適用重大/安全性修補程式

當您將更新部署至 Linux 機器時，可以選取更新分類。 此選項會篩選套用至符合指定準則之電腦的更新。 此篩選會在更新部署時套用至本機電腦上。

由於更新管理會在雲端中執行更新擴充，因此有些更新可能會在更新管理中標示為有安全性影響，即使本機電腦沒有該資訊也一樣。 因此，如果您將重大更新套用至 Linux 電腦，可能會有未標示為對該電腦造成安全性影響的更新，因此不會套用更新。 不過，更新管理可能仍會將該機器回報為不符合規範，因為它有相關更新的其他資訊。

藉由更新分類來部署更新，不會在 CentOS 的 RTM 版本上生效。 若要正確部署 CentOS 的更新，請選取 [所有分類] 以確定已套用更新。 若是 SUSE，*只*選取**其他更新**做為分類時，如果必須先 zypper （套件管理員）或其相依性相關的安全性更新，也可能會造成某些安全性更新。 此行為是 zypper 的限制。 在某些情況下，您可能需要重新執行更新部署。 若要驗證，請檢查更新記錄。

### <a name="multi-tenant"></a>跨租使用者更新部署

如果您有另一個 Azure 租使用者中的電腦向您需要修補的更新管理報告，則必須使用下列因應措施來進行排程。 您可以使用[new-azurermautomationschedule](/powershell/module/azurerm.automation/new-azurermautomationschedule) Cmdlet 搭配 `-ForUpdate` 參數來建立排程，並使用[new-azurermautomationsoftwareupdateconfiguration](/powershell/module/azurerm.automation/new-azurermautomationsoftwareupdateconfiguration
) Cmdlet，並將另一個租使用者中的電腦傳遞給 `-NonAzureComputer` 參數. 下列範例示範如何執行：

```azurepowershell-interactive
$nonAzurecomputers = @("server-01", "server-02")

$startTime = ([DateTime]::Now).AddMinutes(10)

$sched = New-AzureRmAutomationSchedule -ResourceGroupName mygroup -AutomationAccountName myaccount -Name myupdateconfig -Description test-OneTime -OneTime -StartTime $startTime -ForUpdate

New-AzureRmAutomationSoftwareUpdateConfiguration  -ResourceGroupName $rg -AutomationAccountName <automationAccountName> -Schedule $sched -Windows -NonAzureComputer $nonAzurecomputers -Duration (New-TimeSpan -Hours 2) -IncludedUpdateClassification Security,UpdateRollup -ExcludedKbNumber KB01,KB02 -IncludedKbNumber KB100
```

## <a name="onboard"></a>啟用更新管理

若要開始修補系統，您需要啟用更新管理解決方案。 有許多方法可讓機器上線至更新管理。 下列是使解決方案上線的建議和支援方式：

* [從虛擬機器](automation-onboard-solutions-from-vm.md)
* [從瀏覽多部電腦](automation-onboard-solutions-from-browse.md)
* [從自動化帳戶](automation-onboard-solutions-from-automation-account.md)
* [使用 Azure 自動化 Runbook](automation-onboard-solutions.md)

## <a name="next-steps"></a>後續步驟

使用下列教學課程來瞭解如何管理 Windows Vm 的更新：

> [!div class="nextstepaction"]
> [管理 Azure Windows VM 的更新和修補程式](automation-tutorial-update-management.md)

* 使用[Azure 監視器記錄](../log-analytics/log-analytics-log-searches.md)檔中的記錄檔搜尋，以查看詳細的更新資料。
* 針對更新部署狀態[建立警示](automation-tutorial-update-management.md#configure-alerts)。

* 若要瞭解如何透過 REST API 與更新管理互動，請參閱[軟體更新](/rest/api/automation/softwareupdateconfigurations)設定。
* 若要瞭解如何針對更新管理進行疑難排解，請參閱[疑難排解更新管理](troubleshoot/update-management.md)。
