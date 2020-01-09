---
title: 使用 Azure Log Analytics 代理程式收集記錄資料 | Microsoft Docs
description: 此主題可協助您了解如何使用 Log Analytics 來收集資料，並監視 Azure、內部部署或其他雲端環境中裝載的電腦。
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 12/24/2019
ms.openlocfilehash: 58d6c8d18e03ab248cfbebcf910ae13c5fee439e
ms.sourcegitcommit: ce4a99b493f8cf2d2fd4e29d9ba92f5f942a754c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/28/2019
ms.locfileid: "75530964"
---
# <a name="collect-log-data-with-the-log-analytics-agent"></a>使用 Log Analytics 代理程式收集記錄資料

Azure Log Analytics 代理程式先前稱為 Microsoft Monitoring Agent (MMA) 或 OMS Linux 代理程式，其開發目的，是為了要全方位管理內部部署機器、[System Center Operations Manager](https://docs.microsoft.com/system-center/scom/) 所監視的電腦，和任何雲端中的虛擬機器。 Windows 和 Linux 代理程式會附加至 Azure 監視器，並將收集到的記錄資料從 Log Analytics 工作區中的不同來源儲存，以及監視解決方案中定義的任何唯一記錄或計量。 

本文將詳細說明代理程式的概觀、系統和網路需求以及不同的部署方法。

## <a name="overview"></a>概觀

![Log Analytics 代理程式通訊圖表](./media/log-analytics-agent/log-analytics-agent-01.png)

在分析並處理收集的資料之前，您必須先針對您想要將資料傳送至 Azure 監視器服務的所有電腦，安裝並連接代理程式。 您可以使用安裝程式、命令列或 Azure 自動化中的期望狀態設定 (DSC)，在使用 Windows 和 Linux 的 Azure Log Analytics VM 擴充功能的 Azure VM 上安裝代理程式，以及為混合式環境中的機器安裝。 

適用于 Linux 和 Windows 的代理程式會透過 TCP 埠443輸出到 Azure 監視器服務，而且如果機器連線到防火牆或 proxy 伺服器以透過網際網路通訊，請參閱以下的需求以瞭解網路設定必填。 如果您的 IT 安全性原則不允許網路上的電腦連線到網際網路，您可以設定[Log Analytics 閘道](gateway.md)，然後將代理程式設定為透過閘道連接以 Azure 監視器記錄。 然後，代理程式就可以接收設定資訊，並根據您在工作區中啟用的資料收集規則和監視解決方案來傳送收集到的資料。 

使用 Log Analytics 代理程式收集資料時，您必須瞭解下列各項，才能規劃代理程式部署：

* 若要從 Windows 代理程式收集資料，您可以[設定每個代理程式向一或多個工作區報告](agent-windows.md)，即使它向 System Center Operations Manager 管理群組報告也一樣。 Windows 代理程式最多可以報告四個工作區。
* Linux 代理程式不支援多路連接，而且只能向單一工作區報告。
* Windows 代理程式支援[FIPS 140 標準](https://docs.microsoft.com/windows/security/threat-protection/fips-140-validation)，而 Linux 代理程式不支援它。  

如果您使用 System Center Operations Manager 2012 R2 或更新版本：

* 每個 Operations Manager 管理群組只能[連接到一個工作區](om-agents.md)。
* 向管理群組報告的 Linux 電腦必須設定為直接向 Log Analytics 工作區報告。 如果您的 Linux 電腦已直接回報至工作區，而您想要使用 Operations Manager 來監視它們，請遵循下列步驟向[Operations Manager 管理群組報告](agent-manage.md#configure-agent-to-report-to-an-operations-manager-management-group)。
* 您可以在 Windows 電腦上安裝 Log Analytics Windows 代理程式，並將它報告至與工作區整合的 Operations Manager，以及不同的工作區。

適用于 Linux 和 Windows 的代理程式不僅可用於連線到 Azure 監視器，也支援 Azure 自動化裝載混合式 Runbook 背景工作角色和其他服務，例如[變更追蹤](../../automation/change-tracking.md)、[更新管理](../../automation/automation-update-management.md)和[Azure 資訊安全中心](../../security-center/security-center-intro.md)。 如需有關「混合式 Runbook」背景工作角色的詳細資訊，請參閱 [Azure 自動化混合式 Runbook 背景工作](../../automation/automation-hybrid-runbook-worker.md)。  

## <a name="supported-windows-operating-systems"></a>支援的 Windows 作業系統

Windows 代理程式正式支援下列 Windows 作業系統版本：

* Windows Server 2019
* Windows Server 2008 SP2 （x64）、2008 R2、2012、2012 R2、2016、版本1709和1803
* Windows 7 SP1、Windows 8 企業版和專業版，以及 Windows 10 Enterprise 和 Pro

>[!NOTE]
>雖然適用于 Windows 的 Log Analytics 代理程式是設計來支援伺服器監視案例，但我們瞭解您可能會執行 Windows 用戶端，以支援針對伺服器作業系統所設定和優化的工作負載。 代理程式支援 Windows 用戶端，不過，除非明確陳述，否則我們的監視解決方案不會專注于用戶端監視案例。

## <a name="supported-linux-operating-systems"></a>支援的 Linux 作業系統

本節提供有關所支援 Linux 散發套件的詳細資料。

從 2018 年 8 月之後所發行的版本開始，我們會對支援模型進行下列變更：  

* 只支援伺服器版本，不支援用戶端版本。  
* 一律支援新版的 [Azure Linux 背書散發版本](../../virtual-machines/linux/endorsed-distros.md)。  
* 列出的每個主要版本都支援所有次要版本。
* 不支援製造商結束支援日期已過的版本。  
* 不支援新版 AMI。  
* 只支援預設執行 SSL 1.x 的版本。

>[!NOTE]
>如果您使用的散發版本或版本目前不受支援，且不符合支援模型，建議您為此存放庫建立分支，以認可 Microsoft 支援服務將不會對分支代理程式版本提供協助。

* Amazon Linux 2017.09 (x64)
* CentOS Linux 6 (x86/x64) 和 7 (x64)  
* Oracle Linux 6 和 7 (x86/x64) 
* Red Hat Enterprise Linux Server 6 (x86/x64) 和 7 (x64)
* Debian GNU/Linux 8 和 9 (x86/x64)
* Ubuntu 14.04 LTS (x86/x64)、16.04 LTS (x86/x64) 和 18.04 LTS (x64)
* SUSE Linux Enterprise Server 12 （x64）和15（x64）

>[!NOTE]
>OpenSSL 1.1.0 只支援用於 x86_x64 平台 (64 位元)，1.x 之前的 OpenSSL 則不支援用於任何平台。
>

### <a name="agent-prerequisites"></a>代理程式先決條件

下表將重點放在將安裝代理程式之支援的 Linux 散發版本所需的套件。

|必要的套件 |說明 |最小版本 |
|-----------------|------------|----------------|
|Glibc |    GNU C 程式庫 | 2.5-12 
|Openssl    | OpenSSL 程式庫 | 1.0. x 或 1.1. x |
|Curl | cURL Web 用戶端 | 7.15.5 |
|Python-ctypes | | 
|PAM | 插入式驗證模組 | | 

>[!NOTE]
>需要有 rsyslog 或 syslog-ng，才能收集 syslog 訊息。 Red Hat Enterprise Linux 第 5 版、CentOS 和 Oracle Linux 版本 (sysklog) 不支援預設 syslog 精靈，進行 syslog 事件收集。 若要從此版的這些散發套件收集 syslog 資料，rsyslog 精靈應該安裝和設定為取代 sysklog。

## <a name="tls-12-protocol"></a>TLS 1.2 通訊協定

為了確保傳輸中的資料安全性以 Azure 監視器記錄，我們強烈建議您將代理程式設定為至少使用傳輸層安全性（TLS）1.2。 我們已發現較舊版本的 TLS/安全通訊端層 (SSL) 較易受到攻擊，而且在其目前的運作中仍允許回溯相容性，因此並**不建議使用**這些版本。  如需其它資訊，請檢閱[使用 TLS 1.2 安全地傳送](data-security.md#sending-data-securely-using-tls-12)。 

## <a name="network-firewall-requirements"></a>網路防火牆需求

下列資訊列出 Linux 和 Windows 代理程式用來與 Azure 監視器記錄進行通訊所需的 proxy 和防火牆設定資訊。  

|代理程式資源|連接埠 |方向 |略過 HTTPS 檢查|
|------|---------|--------|--------|   
|*.ods.opinsights.azure.com |連接埠 443 |輸出|是 |  
|*.oms.opinsights.azure.com |連接埠 443 |輸出|是 |  
|*.blob.core.windows.net |連接埠 443 |輸出|是 |  

如需 Azure Government 所需的防火牆資訊，請參閱[Azure Government 管理](../../azure-government/documentation-government-services-monitoringandmanagement.md#azure-monitor-logs)。 

如果您打算使用 Azure 自動化混合式 Runbook 背景工作角色來連線至自動化服務並向其註冊，以便在您的環境中使用 runbook 或管理解決方案，它必須能夠存取[設定混合式 Runbook 背景工作角色的網路](../../automation/automation-hybrid-runbook-worker.md#network-planning)中所述的埠號碼和 url。 

Windows 和 Linux 代理程式支援使用 HTTPS 通訊協定，透過 proxy 伺服器或 Log Analytics 閘道與 Azure 監視器進行通訊。  不支援匿名和基本驗證 (使用者名稱/密碼)。  若是直接連線到服務的 Windows 代理程式，請在安裝期間或[部署後](agent-manage.md#update-proxy-settings)從控制台或使用 PowerShell 來指定 Proxy 設定。  

若是 Linux 代理程式，請在安裝期間或[安裝後](agent-manage.md#update-proxy-settings)透過修改 proxy.conf 設定檔來指定 Proxy 伺服器。  Linux 代理程式 Proxy 設定值的語法如下：

`[protocol://][user:password@]proxyhost[:port]`

> [!NOTE]
> 若您的 Proxy 伺服器不要求您進行驗證，Linux 代理程式仍會要求提供虛擬使用者/密碼。 這可以是任何使用者名稱或密碼。

|屬性| 說明 |
|--------|-------------|
|通訊協定 | https |
|user | 用於驗證 Proxy 的選擇性使用者名稱 |
|密碼 | 用於驗證 Proxy 的選擇性密碼 |
|proxyhost | Proxy 伺服器/Log Analytics 閘道的位址或 FQDN |
|連接埠 | Proxy 伺服器/Log Analytics 閘道的選擇性連接埠號碼 |

例如： `https://user01:password@proxy01.contoso.com:30443`

> [!NOTE]
> 若您在您的密碼中使用特殊字元 (例如 "\@")，您會收到 Proxy 連線錯誤訊息，因為系統無法正確剖析該值。  若要暫時解決此問題，請使用 [URLDecode](https://www.urldecoder.org/) 之類的工具將 URL 中的密碼編碼。  

## <a name="install-and-configure-agent"></a>安裝及設定代理程式

根據您的需求，您可以使用不同的方法，將您 Azure 訂用帳戶或混合式環境中的機器直接與 Azure 監視器記錄連接。 下表說明每個方法，您可以判斷哪個方法最適合您的組織。

|來源 | 方法 | 說明|
|-------|-------------|-------------|
|Azure VM| - 適用於 [Windows](../../virtual-machines/extensions/oms-windows.md) 或 [Linux](../../virtual-machines/extensions/oms-linux.md) 的 Log Analytics VM 擴充功能，使用 Azure CLI 或 Azure Resource Manager 範本<br>[從 Azure 入口網站手動](../../azure-monitor/learn/quick-collect-azurevm.md)- <br>- [Azure 資訊安全中心自動](../../security-center/security-center-enable-data-collection.md)布建| -擴充功能會在 Azure 虛擬機器上安裝 Log Analytics 代理程式，並將其註冊到現有的 Azure 監視器工作區中。<br>-Azure 資訊安全中心可以在所有支援的 Azure Vm 上布建 Log Analytics 代理程式，以及當您啟用它來監視是否有安全性弱點和威脅時所建立的任何新虛擬機器。 若已啟用，則不會布建任何新的或現有的 VM （未安裝代理程式）。|
| 混合式 Windows 電腦|- [手動安裝](agent-windows.md)<br>- [Azure 動化 DSC](agent-windows.md#install-the-agent-using-dsc-in-azure-automation)<br>- [搭配 Azure Stack 的資源管理員範本](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/MicrosoftMonitoringAgent-ext-win) |從命令列或使用自動化方法 (例如 Azure 自動化 DSC、[System Center Configuration Manager](https://docs.microsoft.com/sccm/apps/deploy-use/deploy-applications)) 來安裝 Microsoft Monitoring 代理程式，或者如果您已經在您的資料中心部署 Microsoft Azure Stack，請使用 Azure Resource Manager 範本。| 
| 混合式 Linux 電腦| [手動安裝](agent-linux.md)|安裝適用于 Linux 的代理程式呼叫在 GitHub 上裝載的包裝函式腳本，或手動下載並安裝代理程式。 | 
| System Center Operations Manager|[將 Operations Manager 與 Log Analytics 整合](om-agents.md) | 設定 Operations Manager 與 Azure 監視器記錄之間的整合，以將收集的資料從回報的 Windows 電腦轉送到管理群組。|  

## <a name="next-steps"></a>後續步驟

* 檢閱[資料來源](agent-data-sources.md)以了解可用於從您的 Windows 或 Linux 系統收集資料的資料來源。 

* 了解[記錄查詢](../log-query/log-query-overview.md)，以分析從資料來源和解決方案收集到的資料。 

* 了解可將功能新增至 Azure 監視器，並會將資料收集到 Log Analytics 工作區的[監視解決方案](../insights/solutions.md)。
