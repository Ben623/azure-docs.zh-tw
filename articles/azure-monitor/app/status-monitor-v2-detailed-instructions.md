---
title: Azure 狀態監視器 v2 詳細指示 |Microsoft Docs
description: 開始使用狀態監視器 v2 的詳細指示。 在不重新部署網站的情況下監視網站效能。 適用于內部部署、Vm 或 Azure 上裝載的 ASP.NET web 應用程式。
services: application-insights
documentationcenter: .net
author: TimothyMothra
manager: alexklim
ms.assetid: 769a5ea4-a8c6-4c18-b46c-657e864e24de
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 04/23/2019
ms.author: tilee
ms.openlocfilehash: 791db3de897231667d184f08ee152705c59a1e35
ms.sourcegitcommit: ca359c0c2dd7a0229f73ba11a690e3384d198f40
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/17/2019
ms.locfileid: "71057858"
---
# <a name="status-monitor-v2-detailed-instructions"></a>狀態監視器 v2:詳細指示

本文說明如何上架至 PowerShell 資源庫並下載 ApplicationMonitor 模組。
其中包含最常見的參數, 讓您開始使用。
如果您沒有網際網路存取, 我們也提供手動下載指示。

## <a name="get-an-instrumentation-key"></a>取得檢測金鑰

若要開始使用, 您需要有檢測金鑰。 如需詳細資訊, 請參閱[建立 Application Insights 資源](create-new-resource.md#copy-the-instrumentation-key)。

## <a name="run-powershell-as-admin-with-an-elevated-execution-policy"></a>使用提高許可權的執行原則以系統管理員身分執行 PowerShell

### <a name="run-as-admin"></a>以系統管理員身分執行

PowerShell 需要系統管理員層級許可權, 才能對您的電腦進行變更。
### <a name="execution-policy"></a>執行原則
- Description:根據預設, 會停用執行中的 PowerShell 腳本。 我們建議只允許目前範圍的 RemoteSigned 腳本。
- 參考：[關於執行原則](https://docs.microsoft.com/powershell/module/microsoft.powershell.core/about/about_execution_policies?view=powershell-6)和[設定-ExecutionPolicy](
https://docs.microsoft.com/powershell/module/microsoft.powershell.security/set-executionpolicy?view=powershell-6
)。
- 命令: `Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope Process`。
- 選擇性參數:
    - `-Force`. 略過確認提示。

**範例錯誤**

```
Install-Module : The 'Install-Module' command was found in the module 'PowerShellGet', but the module could not be
loaded. For more information, run 'Import-Module PowerShellGet'.
    
Import-Module : File C:\Program Files\WindowsPowerShell\Modules\PackageManagement\1.3.1\PackageManagement.psm1 cannot
be loaded because running scripts is disabled on this system. For more information, see about_Execution_Policies at
https:/go.microsoft.com/fwlink/?LinkID=135170.
```


## <a name="prerequisites-for-powershell"></a>PowerShell 的必要條件

執行`$PSVersionTable`命令, 以審核您的 PowerShell 實例。
此命令會產生下列輸出:


```
Name                           Value
----                           -----
PSVersion                      5.1.17763.316
PSEdition                      Desktop
PSCompatibleVersions           {1.0, 2.0, 3.0, 4.0...}
BuildVersion                   10.0.17763.316
CLRVersion                     4.0.30319.42000
WSManStackVersion              3.0
PSRemotingProtocolVersion      2.3
SerializationVersion           1.1.0.1
```

這些指示是在執行 Windows 10 的電腦上撰寫和測試的, 以及以上所列的版本。

## <a name="prerequisites-for-powershell-gallery"></a>PowerShell 資源庫的必要條件

這些步驟會準備您的伺服器, 以從 PowerShell 資源庫下載模組。

> [!NOTE] 
> Windows 10、Windows Server 2016 和 PowerShell 6 都支援 PowerShell 資源庫。
> 如需舊版的詳細資訊, 請參閱[安裝 PowerShellGet](https://docs.microsoft.com/powershell/gallery/installing-psget)。


1. 使用提高許可權的執行原則以系統管理員身分執行 PowerShell。
2. 安裝 NuGet 套件提供者。
    - Description:您需要此提供者, 才能與以 NuGet 為基礎的存放庫 (例如 PowerShell 資源庫) 互動。
    - 參考：[安裝-install-packageprovider](https://docs.microsoft.com/powershell/module/packagemanagement/install-packageprovider?view=powershell-6)。
    - 命令: `Install-PackageProvider -Name NuGet -MinimumVersion 2.8.5.201`。
    - 選擇性參數:
        - `-Proxy`. 指定要求的 proxy 伺服器。
        - `-Force`. 略過確認提示。
    
    如果未設定 NuGet, 您會收到下列提示:
        
        NuGet provider is required to continue
        PowerShellGet requires NuGet provider version '2.8.5.201' or newer to interact with NuGet-based repositories. The NuGet
         provider must be available in 'C:\Program Files\PackageManagement\ProviderAssemblies' or
        'C:\Users\t\AppData\Local\PackageManagement\ProviderAssemblies'. You can also install the NuGet provider by running
        'Install-PackageProvider -Name NuGet -MinimumVersion 2.8.5.201 -Force'. Do you want PowerShellGet to install and import
         the NuGet provider now?
        [Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"):
    
3. 將 PowerShell 資源庫設定為受信任的存放庫。
    - Description:根據預設, PowerShell 資源庫是不受信任的存放庫。
    - 參考：[Register-psrepository](https://docs.microsoft.com/powershell/module/powershellget/set-psrepository?view=powershell-6)。
    - 命令: `Set-PSRepository -Name "PSGallery" -InstallationPolicy Trusted`。
    - 選擇性參數:
        - `-Proxy`. 指定要求的 proxy 伺服器。

    如果 PowerShell 資源庫不受信任, 您會收到此提示:

        Untrusted repository
        You are installing the modules from an untrusted repository. If you trust this repository, change its
        InstallationPolicy value by running the Set-PSRepository cmdlet. Are you sure you want to install the modules from
        'PSGallery'?
        [Y] Yes  [A] Yes to All  [N] No  [L] No to All  [S] Suspend  [?] Help (default is "N"):

    您可以藉由`Get-PSRepository`執行命令來確認此變更並審核所有 psrepository。

4. 安裝最新版的 PowerShellGet。
    - Description:此模組包含用來從 PowerShell 資源庫取得其他模組的工具。 版本1.0.0.1 隨附于 Windows 10 和 Windows Server。 需要1.6.0 或更高版本。 若要判斷已安裝的版本, 請`Get-Command -Module PowerShellGet`執行命令。
    - 參考：[正在安裝 PowerShellGet](https://docs.microsoft.com/powershell/gallery/installing-psget)。
    - 命令: `Install-Module -Name PowerShellGet`。
    - 選擇性參數:
        - `-Proxy`. 指定要求的 proxy 伺服器。
        - `-Force`. 略過「已安裝」警告並安裝最新版本。

    如果您不是使用最新版本的 PowerShellGet, 就會收到此錯誤:
    
        Install-Module : A parameter cannot be found that matches parameter name 'AllowPrerelease'.
        At line:1 char:20
        Install-Module abc -AllowPrerelease
                           ~~~~~~~~~~~~~~~~
            CategoryInfo          : InvalidArgument: (:) [Install-Module], ParameterBindingException
            FullyQualifiedErrorId : NamedParameterNotFound,Install-Module
    
5. 重新開機 PowerShell。 您無法在目前的會話中載入新的版本。 新的 PowerShell 會話會載入最新版的 PowerShellGet。

## <a name="download-and-install-the-module-via-powershell-gallery"></a>透過 PowerShell 資源庫下載並安裝模組

這些步驟將會從 PowerShell 資源庫下載 Az. ApplicationMonitor 模組。

1. 請確定符合 PowerShell 資源庫的所有必要條件。
2. 使用提高許可權的執行原則以系統管理員身分執行 PowerShell。
3. 安裝 Az ApplicationMonitor 模組。
    - 參考：[Install 模組](https://docs.microsoft.com/powershell/module/powershellget/install-module?view=powershell-6)。
    - 命令: `Install-Module -Name Az.ApplicationMonitor`。
    - 選擇性參數:
        - `-Proxy`. 指定要求的 proxy 伺服器。
        - `-AllowPrerelease`. 允許安裝 Alpha 和 Beta 版。
        - `-AcceptLicense`. 略過 [接受授權] 提示
        - `-Force`. 略過「不受信任的存放庫」警告。

## <a name="download-and-install-the-module-manually-offline-option"></a>手動下載並安裝模組 (離線選項)

如果您因為任何原因而無法連接到 PowerShell 模組, 您可以手動下載並安裝 ApplicationMonitor 模組。

### <a name="manually-download-the-latest-nupkg-file"></a>手動下載最新的 nupkg 檔案

1. 移至 https://www.powershellgallery.com/packages/Az.ApplicationMonitor 。
2. 在 [**版本歷程記錄**] 資料表中, 選取檔案的最新版本。
3. 在 [**安裝選項**] 底下, 選取 [**手動下載**]。

### <a name="option-1-install-into-a-powershell-modules-directory"></a>選項 1：安裝至 PowerShell 模組目錄
將手動下載的 PowerShell 模組安裝到 PowerShell 目錄中, 以供 PowerShell 會話探索。
如需詳細資訊, 請參閱[安裝 PowerShell 模組](https://docs.microsoft.com/powershell/developer/module/installing-a-powershell-module)。


#### <a name="unzip-nupkg-as-a-zip-file-by-using-expand-archive-v1010"></a>使用 [展開-封存] 將 nupkg 解壓縮為 zip 檔案 (v 1.0.1.0 版)

- Description:Base 版本的1.0.1.0 版無法將 nupkg 檔案解壓縮。 將檔案重新命名為 .zip 副檔名。
- 參考：[展開](https://docs.microsoft.com/powershell/module/microsoft.powershell.archive/expand-archive?view=powershell-6)[封存]。
- 命令：

    ```
    $pathToNupkg = "C:\az.applicationmonitor.0.3.0-alpha.nupkg"
    $pathToZip = ([io.path]::ChangeExtension($pathToNupkg, "zip"))
    $pathToNupkg | rename-item -newname $pathToZip
    $pathInstalledModule = "$Env:ProgramFiles\WindowsPowerShell\Modules\az.applicationmonitor"
    Expand-Archive -LiteralPath $pathToZip -DestinationPath $pathInstalledModule
    ```

#### <a name="unzip-nupkg-by-using-expand-archive-v1100"></a>使用 Expand-Archive 解壓縮 nupkg (v 1.1.0.0)

- Description:使用目前版本的 [擴充-封存] 來解壓縮 nupkg 檔案, 而不變更延伸模組。
- 參考：[展開](https://docs.microsoft.com/powershell/module/microsoft.powershell.archive/expand-archive?view=powershell-6)[封存] 和 [ [Microsoft](https://www.powershellgallery.com/packages/Microsoft.PowerShell.Archive/1.1.0.0)]。
- 命令：

    ```
    $pathToNupkg = "C:\az.applicationmonitor.0.2.1-alpha.nupkg"
    $pathInstalledModule = "$Env:ProgramFiles\WindowsPowerShell\Modules\az.applicationmonitor"
    Expand-Archive -LiteralPath $pathToNupkg -DestinationPath $pathInstalledModule
    ```

### <a name="option-2-unzip-and-import-nupkg-manually"></a>選項 2：手動解壓縮和匯入 nupkg
將手動下載的 PowerShell 模組安裝到 PowerShell 目錄中, 以供 PowerShell 會話探索。
如需詳細資訊, 請參閱[安裝 PowerShell 模組](https://docs.microsoft.com/powershell/developer/module/installing-a-powershell-module)。

如果您要將模組安裝至其他任何目錄, 請使用[import-module](https://docs.microsoft.com/powershell/module/microsoft.powershell.core/import-module?view=powershell-6)手動匯入模組。

> [!IMPORTANT] 
> Dll 會透過相對路徑來安裝。
> 將封裝的內容儲存在您想要的執行時間目錄中, 並確認存取權限允許讀取但無法寫入。

1. 將延伸模組變更為 ".zip", 並將套件的內容解壓縮到您想要的安裝目錄中。
2. 尋找 Az. ApplicationMonitor. .psd1 的檔案路徑。
3. 使用提高許可權的執行原則以系統管理員身分執行 PowerShell。
4. 使用`Import-Module Az.ApplicationMonitor.psd1`命令載入模組。
    

## <a name="route-traffic-through-a-proxy"></a>透過 proxy 路由傳送流量

當您監視私人內部網路上的電腦時, 您必須透過 proxy 路由傳送 HTTP 流量。

從 PowerShell 資源庫下載並安裝 Az. ApplicationMonitor 的 PowerShell 命令支援`-Proxy`參數。
當您撰寫安裝腳本時, 請參閱上述指示。

Application Insights SDK 必須將您應用程式的遙測傳送至 Microsoft。 我們建議您在 web.config 檔案中設定應用程式的 proxy 設定。 如需詳細資訊, [請參閱 Application Insights 常見問題:Proxy 傳遞](https://docs.microsoft.com/azure/azure-monitor/app/troubleshoot-faq#proxy-passthrough)。


## <a name="enable-monitoring"></a>啟用監視

`Enable-ApplicationInsightsMonitoring`使用命令來啟用監視。

如需如何使用此 Cmdlet 的詳細說明, 請參閱[API 參考](status-monitor-v2-api-enable-monitoring.md)。



## <a name="next-steps"></a>後續步驟

 檢視遙測：

- [探索計量](../../azure-monitor/app/metrics-explorer.md)以監視效能和使用量。
- [搜尋事件和記錄](../../azure-monitor/app/diagnostic-search.md)以診斷問題。
- [流量分析](../../azure-monitor/app/analytics.md)進行更先進的查詢。
- [建立儀表板](../../azure-monitor/app/overview-dashboard.md)。

 新增更多遙測：

- [建立 web 測試](monitor-web-app-availability.md), 以確保您的網站保持上線。
- [新增 web 用戶端遙測](../../azure-monitor/app/javascript.md), 以查看來自網頁程式碼的例外狀況, 並啟用追蹤呼叫。
- [將 APPLICATION INSIGHTS SDK 新增至您的程式碼](../../azure-monitor/app/asp-net.md), 讓您可以插入追蹤和記錄呼叫。

使用狀態監視器 v2 來執行更多動作:

- 使用我們的指南進行狀態監視器 v2 的[疑難排解](status-monitor-v2-troubleshoot.md)。
