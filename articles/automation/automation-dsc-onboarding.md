---
title: 將機器上架交由 Azure Automation State Configuration 管理
description: 如何設定機器交由 Azure Automation State Configuration 管理
services: automation
ms.service: automation
ms.subservice: dsc
author: bobbytreed
ms.author: robreed
ms.topic: conceptual
ms.date: 08/08/2018
manager: carmonm
ms.openlocfilehash: ca53d85a09727b75f68da8d049ac3fcd6723a041
ms.sourcegitcommit: af58483a9c574a10edc546f2737939a93af87b73
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/17/2019
ms.locfileid: "68302260"
---
# <a name="onboarding-machines-for-management-by-azure-automation-state-configuration"></a>將機器上架交由 Azure Automation State Configuration 管理

## <a name="why-manage-machines-with-azure-automation-state-configuration"></a>為什麼要使用 Azure Automation State Configuration 管理機器？

Azure 自動化狀態設定是任何雲端或內部部署資料中心內 DSC 節點的設定管理服務。
它可讓您從中央、安全的位置快速且輕鬆地延展性到數千部電腦。
您可以輕鬆地上架機器、指派它們宣告式組態和檢視顯示每個電腦的符合性報告 (達您指定的所需狀態)。
Azure 自動化狀態設定服務是 DSC Azure 自動化 runbook 與 PowerShell 腳本。
換句話說，「Azure 自動化」會以協助您管理 Powershell 指令碼的相同方式，同樣協助您管理 DSC 組態。
若要深入了解使用 Azure Automation State Configuration 的優點，請參閱 [Azure Automation State Configuration 概觀](automation-dsc-overview.md)。

Azure Automation State Configuration 可以用來管理各種不同的機器：

- Azure 虛擬機器
- Azure 虛擬機器 (傳統)
- Amazon Web Services (AWS) EC2 執行個體
- 位於內部部署或 Azure/AWS 以外之雲端中的實體/虛擬 Windows 電腦
- 位於內部部署、Azure 或 Azure 以外之雲端中的實體/虛擬 Linux 機器

此外，如果您不準備從雲端管理機器組態，Azure Automation State Configuration 也可用來當作報告專用端點。
這可讓您透過 DSC 設定 (推送) 設定, 並在 Azure 自動化中查看報告詳細資料。

> [!NOTE]
> 如果安裝的虛擬機器 DSC 擴充功能大於 2.70，則使用 State Configuration 管理 Azure VM 是免費隨附的。 如需詳細資料，請參閱[**自動化定價分頁**](https://azure.microsoft.com/pricing/details/automation/)。

下列各節概述如何將每個類型的機器上架到 Azure Automation State Configuration。

## <a name="azure-virtual-machines"></a>Azure 虛擬機器

Azure Automation State Configuration 可讓您使用 Azure 入口網站、Azure 資源管理員範本或 PowerShell，輕鬆上架 Azure 虛擬機器以進行組態管理。 在幕後並且不需要系統管理員遠端連至 VM 的情況下，Azure VM Desired State Configuration 擴充功能會向 Azure Automation State Configuration 註冊 VM。
因為 Azure VM 預期狀態設定延伸模組是以非同步方式執行，以下的[**疑難排解 Azure 虛擬機器上架**](#troubleshooting-azure-virtual-machine-onboarding)一節會提供追蹤其進度或疑難排解的步驟。

### <a name="azure-portal"></a>Azure 入口網站

在 [Azure 入口網站](https://portal.azure.com/)中，瀏覽至您想要佈建虛擬機器的「Azure 自動化」帳戶。 在 State Configuration 頁面和 [節點]  索引標籤上，按一下 [+ 新增]  。

選取要上架的 Azure 虛擬機器。

如果電腦沒有 PowerShell 預期安裝的狀態延伸模組，並且電源狀態為執行中，請按一下 [連接]  。

在 [註冊]  下，輸入您的使用情況所需的 [PowerShell DSC 本機 Configuration Manager 值](/powershell/dsc/managing-nodes/metaconfig)，並選擇性地輸入要指派給 VM 的節點組態。

![上架](./media/automation-dsc-onboarding/DSC_Onboarding_6.png)

### <a name="azure-resource-manager-templates"></a>Azure 資源管理員範本

您可以透過 Azure Resource Manager 範本部署 Azure 虛擬機器和上架到 Azure Automation State Configuration。 如需將上線現有 VM 以 Azure 自動化狀態設定的範例範本, 請參閱[Desired State Configuration 服務管理的伺服器](https://azure.microsoft.com/resources/templates/101-automation-configuration/)。
如果您要管理虛擬機器擴展集, 請參閱 Azure 自動化管理的範例範本[VM 擴展集設定](https://azure.microsoft.com/resources/templates/201-vmss-automation-dsc/)。

### <a name="powershell"></a>PowerShell

您可以透過 PowerShell 使用 [Register-AzureRmAutomationDscNode](/powershell/module/azurerm.automation/register-azurermautomationdscnode) Cmdlet 在 Azure 入口網站中佈建虛擬機器。

### <a name="registering-virtual-machines-across-azure-subscriptions"></a>跨 Azure 訂用帳戶註冊虛擬機器

從其他 Azure 訂用帳戶註冊虛擬機器的最佳方式, 是在 Azure Resource Manager 部署範本中使用 DSC 延伸模組。
[Azure Resource Manager 範本 Desired State Configuration 擴充](https://docs.microsoft.com/azure/virtual-machines/extensions/dsc-template)功能中提供範例。
若要在範本中尋找用來作為參數的註冊金鑰和註冊 URL, 請參閱下列[**安全註冊**](#secure-registration)一節。

## <a name="amazon-web-services-aws-virtual-machines"></a>Amazon Web Services (AWS) 虛擬機器

您可以使用 AWS DSC Toolkit 輕鬆地將 Amazon Web Services 虛擬機器上架，以便 Azure Automation State Configuration 管理組態。 您可以在 [這裡](https://blogs.msdn.microsoft.com/powershell/2016/04/20/aws-dsc-toolkit/)深入了解工具組。

## <a name="physicalvirtual-windows-machines-on-premises-or-in-a-cloud-other-than-azureaws"></a>位於內部部署或 Azure/AWS 以外之雲端中的實體/虛擬 Windows 電腦

在內部部署或其他雲端環境中執行的 Windows 伺服器, 也可以上架至 Azure 自動化狀態設定, 只要它們具有[Azure 的輸出存取權](automation-dsc-overview.md#network-planning):

1. 確定在您想要上架到 Azure Automation State Configuration 的電腦上已安裝最新版的 [WMF 5](https://aka.ms/wmf5latest)。
1. 請依照下列[**產生 DSC 中繼設定**](#generating-dsc-metaconfigurations)一節中的指示，來產生包含所需 DSC 中繼設定的資料夾。
1. 從遠端將 PowerShell DSC 中繼設定套用至您想要上架的電腦。 **執行此命令的電腦必須安裝最新版的 [WMF 5](https://aka.ms/wmf5latest)** ：

   ```powershell
   Set-DscLocalConfigurationManager -Path C:\Users\joe\Desktop\DscMetaConfigs -ComputerName MyServer1, MyServer2
   ```

1. 如果您無法從遠端套用 PowerShell DSC 中繼設定，請將步驟 2 中繼設定的資料夾複製到每一部要上架的電腦。 然後在要上架的每台電腦本機上呼叫 **Set-DscLocalConfigurationManager** 。
1. 使用 Azure 入口網站或 Cmdlet，檢查要上架的電腦現在在您的 Azure 自動化帳戶中是否顯示為已註冊的 State Configuration 節點。

## <a name="physicalvirtual-linux-machines-on-premises-or-in-a-cloud-other-than-azure"></a>內部部署或 Azure 以外之雲端中的實體/虛擬 Linux 機器

在內部部署或其他雲端環境中執行的 Linux 伺服器也可以上架至 Azure 自動化狀態設定, 只要它們有 Azure 的[輸出存取權](automation-dsc-overview.md#network-planning)即可:

1. 確定您想要上架到 Azure Automation State Configuration 的電腦上已安裝最新版的 [PowerShell Desired State Configuration for Linux](https://github.com/Microsoft/PowerShell-DSC-for-Linux)。
1. 如果 [PowerShell DSC 本機組態管理員的預設值](/powershell/dsc/metaconfig4)符合您的使用案例，而且您想要將電腦上架，使其**同時**從 Azure Automation State Configuration 提取並報告：

   - 在要於「Azure 自動化狀態設定」上線的每部 Linux 機器上，使用 `Register.py` 以運用「PowerShell DSC 本機設定管理員」預設值來上線：

     `/opt/microsoft/dsc/Scripts/Register.py <Automation account registration key> <Automation account registration URL>`

   - 若要尋找您的自動化帳戶的註冊金鑰和註冊 URL，請參閱以下的[**安全註冊**](#secure-registration)一節會提供追蹤其進度或疑難排解的步驟。

     如果 PowerShell DSC 本機 Configuration Manager 預設值**不**符合您的使用案例, 或者您想要將電腦上架, 使其只向 Azure 自動化狀態設定報告, 請遵循步驟 3-6。 否則，請直接跳到步驟 6。

1. 請依照下列[**產生 DSC 中繼設定**](#generating-dsc-metaconfigurations)一節中的指示，來產生包含所需 DSC 中繼設定的資料夾。
1. 從遠端將 PowerShell DSC metaconfiguration 套用至您想要上架的電腦：

    ```powershell
    $SecurePass = ConvertTo-SecureString -String '<root password>' -AsPlainText -Force
    $Cred = New-Object System.Management.Automation.PSCredential 'root', $SecurePass
    $Opt = New-CimSessionOption -UseSsl -SkipCACheck -SkipCNCheck -SkipRevocationCheck

    # need a CimSession for each Linux machine to onboard
    $Session = New-CimSession -Credential $Cred -ComputerName <your Linux machine> -Port 5986 -Authentication basic -SessionOption $Opt

    Set-DscLocalConfigurationManager -CimSession $Session -Path C:\Users\joe\Desktop\DscMetaConfigs
    ```

執行此命令的電腦必須安裝最新版的 [WMF 5](https://aka.ms/wmf5latest) 。

1. 如果您無法從遠端套用 PowerShell DSC 中繼設定, 請從步驟5中的資料夾將對應至該電腦的中繼功能複製到 Linux 電腦。 然後在您要上架到 Azure Automation State Configuration 的每部 Linux 電腦本機上呼叫 `SetDscLocalConfigurationManager.py`：

   `/opt/microsoft/dsc/Scripts/SetDscLocalConfigurationManager.py -configurationmof <path to metaconfiguration file>`

1. 使用 Azure 入口網站或 Cmdlet，檢查要上架的電腦現在在您的 Azure 自動化帳戶中顯示為已註冊的 DSC 節點。

## <a name="generating-dsc-metaconfigurations"></a>產生 DSC 中繼設定

若要以一般方式將任何電腦上架至 Azure 自動化狀態設定, 可以產生[dsc](/powershell/dsc/metaconfig)中繼中繼, 告知 dsc 代理程式從和/或報表提取到 Azure 自動化狀態設定。 Azure Automation State Configuration 的 DSC 中繼組態可以使用 PowerShell DSC 組態或 Azure 自動化 PowerShell Cmdlet 產生。

> [!NOTE]
> DSC 中繼設定包含將電腦上架至進行管理之自動化帳戶的機密資料。 請務必適當地保護您所建立的任何 DSC 中繼設定，或在使用後將它們刪除。

### <a name="using-a-dsc-configuration"></a>使用 DSC 設定

1. 在您本機環境的電腦中，以系統管理員身分開啟 VSCode (或您喜愛的編輯器)。 電腦必須安裝最新版本的 [WMF 5](https://aka.ms/wmf5latest) 。
1. 在本機複製下列指令碼。 此指令碼包含用來建立中繼設定的 PowerShell DSC 設定，以及開始執行中繼設定建立作業的命令。

> [!NOTE]
> Azure Automation State Configuration 名稱在入口網站會區分大小寫。 如果大小寫不相符，則該節點不會在 [節點]  索引標籤下顯示。

   ```powershell
   # The DSC configuration that will generate metaconfigurations
   [DscLocalConfigurationManager()]
   Configuration DscMetaConfigs
   {
        param
        (
            [Parameter(Mandatory=$True)]
            [String]$RegistrationUrl,

            [Parameter(Mandatory=$True)]
            [String]$RegistrationKey,

            [Parameter(Mandatory=$True)]
            [String[]]$ComputerName,

            [Int]$RefreshFrequencyMins = 30,

            [Int]$ConfigurationModeFrequencyMins = 15,

            [String]$ConfigurationMode = 'ApplyAndMonitor',

            [String]$NodeConfigurationName,

            [Boolean]$RebootNodeIfNeeded= $False,

            [String]$ActionAfterReboot = 'ContinueConfiguration',

            [Boolean]$AllowModuleOverwrite = $False,

            [Boolean]$ReportOnly
        )

        if(!$NodeConfigurationName -or $NodeConfigurationName -eq '')
        {
            $ConfigurationNames = $null
        }
        else
        {
            $ConfigurationNames = @($NodeConfigurationName)
        }

        if($ReportOnly)
        {
            $RefreshMode = 'PUSH'
        }
        else
        {
            $RefreshMode = 'PULL'
        }

        Node $ComputerName
        {
            Settings
            {
                RefreshFrequencyMins           = $RefreshFrequencyMins
                RefreshMode                    = $RefreshMode
                ConfigurationMode              = $ConfigurationMode
                AllowModuleOverwrite           = $AllowModuleOverwrite
                RebootNodeIfNeeded             = $RebootNodeIfNeeded
                ActionAfterReboot              = $ActionAfterReboot
                ConfigurationModeFrequencyMins = $ConfigurationModeFrequencyMins
            }

            if(!$ReportOnly)
            {
            ConfigurationRepositoryWeb AzureAutomationStateConfiguration
                {
                    ServerUrl          = $RegistrationUrl
                    RegistrationKey    = $RegistrationKey
                    ConfigurationNames = $ConfigurationNames
                }

                ResourceRepositoryWeb AzureAutomationStateConfiguration
                {
                ServerUrl       = $RegistrationUrl
                RegistrationKey = $RegistrationKey
                }
            }

            ReportServerWeb AzureAutomationStateConfiguration
            {
                ServerUrl       = $RegistrationUrl
                RegistrationKey = $RegistrationKey
            }
        }
   }

    # Create the metaconfigurations
    # NOTE: DSC Node Configuration names are case sensitive in the portal.
    # TODO: edit the below as needed for your use case
   $Params = @{
        RegistrationUrl = '<fill me in>';
        RegistrationKey = '<fill me in>';
        ComputerName = @('<some VM to onboard>', '<some other VM to onboard>');
        NodeConfigurationName = 'SimpleConfig.webserver';
        RefreshFrequencyMins = 30;
        ConfigurationModeFrequencyMins = 15;
        RebootNodeIfNeeded = $False;
        AllowModuleOverwrite = $False;
        ConfigurationMode = 'ApplyAndMonitor';
        ActionAfterReboot = 'ContinueConfiguration';
        ReportOnly = $False;  # Set to $True to have machines only report to AA DSC but not pull from it
   }

   # Use PowerShell splatting to pass parameters to the DSC configuration being invoked
   # For more info about splatting, run: Get-Help -Name about_Splatting
   DscMetaConfigs @Params
   ```

1. 填寫您自動化帳戶的註冊金鑰和 URL，以及要上架的電腦名稱。 所有其他參數都是選擇性的。 若要尋找您的自動化帳戶的註冊金鑰和註冊 URL，請參閱以下的[**安全註冊**](#secure-registration)一節會提供追蹤其進度或疑難排解的步驟。
1. 如果您希望電腦向 Azure Automation State Configuration 報告 DSC 狀態資訊，但不提取組態或 PowerShell 模組，請將 **ReportOnly** 參數設定為 true。
1. 執行指令碼。 您現在工作目錄中應該有一個名為 **DscMetaConfigs** 的資料夾，其中包含要上架之電腦的 PowerShell DSC 中繼設定 (以系統管理員身分)：

    ```powershell
    Set-DscLocalConfigurationManager -Path ./DscMetaConfigs
    ```

### <a name="using-the-azure-automation-cmdlets"></a>使用 Azure 自動化 Cmdlet

如果 PowerShell DSC 本機組態管理員的預設值符合您的使用案例，而且您想要將電腦上架，使其同時從 Azure Automation State Configuration 提取資訊並向 Azure Automation State Configuration 提供報告，Azure 自動化 Cmdlet 會提供簡單的方法來產生所需的 DSC 中繼組態：

1. 在您本機環境的電腦中，以系統管理員身分開啟 PowerShell 主控台或 VSCode。
2. 使用 `Connect-AzureRmAccount` 連線到 Azure Resource Manager
3. 從您要上架節點的目標自動化帳戶下載您想要上架之電腦的 PowerShell DSC 中繼設定：

   ```powershell
   # Define the parameters for Get-AzureRmAutomationDscOnboardingMetaconfig using PowerShell Splatting
   $Params = @{
       ResourceGroupName = 'ContosoResources'; # The name of the Resource Group that contains your Azure Automation Account
       AutomationAccountName = 'ContosoAutomation'; # The name of the Azure Automation Account where you want a node on-boarded to
       ComputerName = @('web01', 'web02', 'sql01'); # The names of the computers that the meta configuration will be generated for
       OutputFolder = "$env:UserProfile\Desktop\";
   }
   # Use PowerShell splatting to pass parameters to the Azure Automation cmdlet being invoked
   # For more info about splatting, run: Get-Help -Name about_Splatting
   Get-AzureRmAutomationDscOnboardingMetaconfig @Params
   ```

1. 您現在應該有一個名為 ***DscMetaConfigs*** 的資料夾，其中包含要上架之電腦的 PowerShell DSC 中繼設定 (以系統管理員身分)：

    ```powershell
    Set-DscLocalConfigurationManager -Path $env:UserProfile\Desktop\DscMetaConfigs
    ```

## <a name="secure-registration"></a>安全註冊

機器可以透過 WMF 5 DSC 註冊通訊協定安全地上架到 Azure 自動化帳戶，如此可讓 DSC 節點向 PowerShell DSC 提取或報告伺服器 (包括 Azure Automation State Configuration) 進行驗證。 節點會在**註冊 URL** 時向伺服器註冊，並使用**註冊金鑰**進行驗證。 在註冊期間，DSC 節點和 DSC 提取/報告伺服器會交涉獨特的憑證，讓此節點在註冊伺服器用於進行驗證。 此程序可避免上架的節點彼此模擬，例如當節點遭到入侵並且具有惡意行為。 註冊之後，註冊金鑰不會再次用於驗證，並且會從節點中刪除。

您可以從 Azure 入口網站中 [帳戶設定]  底下的 [金鑰]  取得 State Configuration 註冊通訊協定所需的資訊。 在自動化帳戶的 [基本功能]  面板按一下金鑰圖示，可開啟此刀鋒視窗。

![Azure 自動化金鑰和 URL](./media/automation-dsc-onboarding/DSC_Onboarding_4.png)

- 「註冊 URL」是 [管理金鑰] 刀鋒視窗中的 [URL] 欄位。
- 「註冊金鑰」是 [管理金鑰] 刀鋒視窗中的主要存取金鑰或次要存取金鑰。 可以使用這兩個金鑰。

為了提高安全性，自動化帳戶的主要和次要存取金鑰可以隨時重新產生 (在 [管理金鑰]  頁面上)，以避免未來的節點使用先前的金鑰註冊。

## <a name="troubleshooting-azure-virtual-machine-onboarding"></a>疑難排解 Azure 虛擬機器上架

Azure Automation State Configuration 可讓您輕鬆地將 Azure Windows VM 上架以進行組態管理。 在幕後，Azure VM Desired State Configuration 擴充功能是用來向 Azure Automation State Configuration 註冊 VM。 因為 Azure VM 期望的狀態組態延伸模組是以非同步方式執行，追蹤其進度和疑難排解其執行可能很重要。

> [!NOTE]
> 任何可將 Azure Windows VM 上架到使用 Azure VM Desired State Configuration 擴充功能的 Azure Automation State Configuration 的方法，最多可能需要一小時的時間，節點才會顯示為已在 Azure 自動化中註冊。 這是因為 VM 上憑藉著 Azure VM DSC 擴充功能的 Windows Management Framework 5.0 安裝，需要它才能將 VM 上架到 Azure Automation State Configuration。

若要對「Azure VM 預期狀態設定」延伸模組的狀態進行疑難排解或檢視，請在 Azure 入口網站中，瀏覽至要上架的 VM，然後按一下 [設定]  底下的 [延伸模組]  。 然後視作業系統而定，按一下 [DSC]  或 [DSCForLinux]  。 如需詳細資訊，您可以按一下 [檢視詳細狀態]  。

## <a name="certificate-expiration-and-reregistration"></a>憑證到期日和重新註冊

在將機器註冊為 Azure Automation State Configuration 中的 DSC 節點之後，有數種原因讓您可能需要在未來重新註冊該節點：

- 在註冊之後，每個節點會自動交涉唯一的驗證憑證，該憑證於一年之後到期。 目前，當憑證即將過期時，PowerShell DSC 註冊通訊協定便無法自動更新憑證，因此您必須在一年之後重新註冊這些節點。 在重新登錄之前，請確定每個節點都正在執行 Windows Management Framework 5.0 RTM。 如果節點的驗證憑證過期，而且該節點尚未註冊，則該節點將無法與 Azure 自動化通訊，而且會被標示為「未回應」。 與憑證到期時間相距 90 天或更短時間內執行的註冊，或是憑證到期時間之後任何時間點執行的註冊，將會產生新的憑證並予以使用。
- 變更在節點初始註冊期間設定的任何 [PowerShell DSC 本機組態管理員值](/powershell/dsc/metaconfig4) ，例如 ConfigurationMode。 目前，這些 DSC 代理程式值只可以透過重新註冊變更。 其中一個例外是指派給節點的節點組態 - 它可以在 Azure Automation DSC 中直接變更。

重新註冊可以用您初始註冊節點的相同方法執行，使用這份文件中所述的任何上架方法。 重新註冊節點之前，您不需要從 Azure Automation State Configuration 取消註冊節點。

## <a name="next-steps"></a>後續步驟

- 若要開始使用，請參閱[開始使用 Azure 自動化狀態設定](automation-dsc-getting-started.md)
- 若要了解如何編譯 DSC 組態，以將它們指派給目標節點，請參閱[編譯 Azure Automation State Configuration 中的組態](automation-dsc-compile.md)
- 如需 PowerShell Cmdlet 參考，請參閱 [Azure 自動化狀態設定 Cmdlet](/powershell/module/azurerm.automation/#automation)
- 如需定價資訊，請參閱 [Azure 自動化狀態設定的定價](https://azure.microsoft.com/pricing/details/automation/)
- 若要查看在持續部署管線中使用 Azure 自動化狀態設定的範例，請參閱[使用 Azure 自動化狀態設定和 Chocolatey 的持續部署](automation-dsc-cd-chocolatey.md)
