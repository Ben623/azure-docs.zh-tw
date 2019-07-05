---
title: Azure 自動化中的認證資產
description: Azure 自動化中的認證資產包含可用來驗證由 Runbook 或 DSC 設定存取資源的安全性認證。 本文說明如何建立認證資產和在 Runbook 或 DSC 設定中使用它們。
services: automation
ms.service: automation
ms.subservice: shared-capabilities
author: bobbytreed
ms.author: robreed
ms.date: 04/12/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 44bc49d10c492822c1b5d30ad5794ac2522cb918
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/29/2019
ms.locfileid: "67478144"
---
# <a name="credential-assets-in-azure-automation"></a>Azure 自動化中的認證資產

自動化認證資產會保存物件，其中包含安全性認證，例如使用者名稱和密碼。 Runbook 和 DSC 設定可以使用可接受 PSCredential 物件進行驗證的 Cmdlet，否則可能會擷取 PSCredential 物件的使用者名稱和密碼，以對需要驗證的一些應用程式或服務提供。 認證的屬性會安全地儲存在 Azure 自動化中，並且可在 Runbook 或 DSC 設定中透過 [Get-AutomationPSCredential](#activities) 活動存取。

[!INCLUDE [gdpr-dsr-and-stp-note.md](../../../includes/gdpr-dsr-and-stp-note.md)]

> [!NOTE]
> Azure 自動化中的安全資產包括認證、憑證、連接和加密的變數。 這些資產都會經過加密，並使用為每個自動化帳戶產生的唯一金鑰儲存在 Azure 自動化中。 此金鑰會儲存在 Key Vault 中。 在儲存安全資產之前，系統會從 Key Vault 載入金鑰，然後用來加密資產。

## <a name="azure-classic-powershell-cmdlets"></a>Azure 傳統 PowerShell Cmdlet

下表中的 Cmdlet 是用來透過 Windows PowerShell 建立和管理自動化認證資產。  它們附屬於[Azure PowerShell 模組](/powershell/azure/overview)，這是可用於自動化 runbook 和 DSC 設定。

| Cmdlet | 描述 |
|:--- |:--- |
| [Get-AzureAutomationCredential](/powershell/module/servicemanagement/azure/get-azureautomationcredential) |擷取認證資產的相關資訊。 您只能從 **Get-AutomationPSCredential** 活動擷取認證本身。 |
| [New-AzureAutomationCredential](/powershell/module/servicemanagement/azure/new-azureautomationcredential) |建立新的自動化認證。 |
| [Remove-AzureAutomationCredential](/powershell/module/servicemanagement/azure/new-azureautomationcredential) |移除自動化認證。 |
| [Set-AzureAutomationCredential](/powershell/module/servicemanagement/azure/new-azureautomationcredential) |設定現有自動化認證的屬性。 |

## <a name="azurerm-powershell-cmdlets"></a>AzureRM PowerShell Cmdlet

針對 AzureRM，下表中的 Cmdlet 可透過 Windows PowerShell 來建立和管理自動化認證資產。  它們附屬於[AzureRM.Automation 模組](/powershell/azure/overview)，這是可用於自動化 runbook 和 DSC 設定。

| Cmdlet | 描述 |
|:--- |:--- |
| [Get-AzureRmAutomationCredential](/powershell/module/azurerm.automation/get-azurermautomationcredential) |擷取認證資產的相關資訊。 這不會傳回的 PSCredential 物件。  |
| [New-AzureRmAutomationCredential](/powershell/module/azurerm.automation/new-azurermautomationcredential) |建立新的自動化認證。 |
| [Remove-AzureRmAutomationCredential](/powershell/module/azurerm.automation/remove-azurermautomationcredential) |移除自動化認證。 |
| [Set-AzureRmAutomationCredential](/powershell/module/azurerm.automation/set-azurermautomationcredential) |設定現有自動化認證的屬性。 |

## <a name="activities"></a>activities

下表中的活動用來存取中 Runbook 和 DSC 設定的認證。

| activities | 描述 |
|:--- |:--- |
| Get-AutomationPSCredential |取得要在 Runbook 或 DSC 設定中使用的認證。 傳回 [System.Management.Automation.PSCredential](/dotnet/api/system.management.automation.pscredential) 物件。 |

> [!NOTE]
> 您應該避免在 Get-AutomationPSCredential 的 -Name 參數中使用變數，因為這可能會使在設計階段中探索 Runbook 或 DSC 設定與認證資產之間的相依性變得複雜。

## <a name="python2-functions"></a>Python2 函式

下表中的函式用於存取 Python2 Runbook 中的認證。

| 函式 | 描述 |
|:---|:---|
| automationassets.get_automation_credential | 擷取認證資產的相關資訊。 |

> [!NOTE]
> 您必須在 Python Runbook 的頂端匯入「automationassets」模組，才能存取資產函式。

## <a name="creating-a-new-credential-asset"></a>建立新的認證資產

### <a name="to-create-a-new-credential-asset-with-the-azure-portal"></a>使用 Azure 入口網站建立新的認證資產

1. 從您的自動化帳戶中，選取 [共用資源]  下的 [認證]  。
1. 按一下 [+ 新增認證]  。
1. 完成表單，然後按一下 [ **建立** ] 以儲存新認證。

> [!NOTE]
> 「Azure 自動化」中不支援使用多重要素驗證的使用者帳戶。

### <a name="to-create-a-new-credential-asset-with-windows-powershell"></a>使用 Windows PowerShell 建立新的認證資產

下列範例命令顯示如何建立新的自動化認證。 PSCredential 物件是先建立了名稱和密碼，然後用來建立認證資產。 您也可以使用 **Get-Credential** Cmdlet 來提示您輸入名稱和密碼。

```powershell
$user = "MyDomain\MyUser"
$pw = ConvertTo-SecureString "PassWord!" -AsPlainText -Force
$cred = New-Object –TypeName System.Management.Automation.PSCredential –ArgumentList $user, $pw
New-AzureAutomationCredential -AutomationAccountName "MyAutomationAccount" -Name "MyCredential" -Value $cred
```

## <a name="using-a-powershell-credential"></a>使用 PowerShell 認證

您會使用 **Get-AutomationPSCredential** 活動在 Runbook 或 DSC 設定中擷取認證資產。 這會傳回 [PSCredential 物件](/dotnet/api/system.management.automation.pscredential) ，您可以對需要 PSCredential 參數的活動或 Cmdlet 搭配使用此物件。 您也可以擷取要個別使用的認證物件的屬性。 物件具備使用者名稱和安全密碼的屬性，或是您可以使用 **GetNetworkCredential** 方法來傳回將提供不安全版本的密碼的 [NetworkCredential](/dotnet/api/system.net.networkcredential) 物件。

> [!NOTE]
> **Get-azurermautomationcredential**不會傳回**PSCredential** ，可用來驗證。 此外，它只會提供認證的相關資訊。 如果您要在 runbook 中使用的認證必須使用**Get-automationpscredential**來擷取**PSCredential**物件。

### <a name="textual-runbook-sample"></a>文字式 Runbook 範例

下列範例命令顯示如何在 Runbook 中使用 PowerShell 認證。 在此範例中，會擷取認證及指派給變數的使用者名稱和密碼。

```azurepowershell
$myCredential = Get-AutomationPSCredential -Name 'MyCredential'
$userName = $myCredential.UserName
$securePassword = $myCredential.Password
$password = $myCredential.GetNetworkCredential().Password
```

您也可以使用認證來向 Azure 中使用[Connect-azurermaccount](/powershell/module/azurerm.profile/connect-azurermaccount)。 在大部分情況下，您應該使用[執行身分帳戶](../manage-runas-account.md)，並擷取與其[Get-automationconnection](../automation-connections.md)。

```azurepowershell
$myCred = Get-AutomationPSCredential -Name 'MyCredential'
$userName = $myCred.UserName
$securePassword = $myCred.Password
$password = $myCred.GetNetworkCredential().Password

$myPsCred = New-Object System.Management.Automation.PSCredential ($userName,$password)

Connect-AzureRmAccount -Credential $myPsCred
```

### <a name="graphical-runbook-sample"></a>圖形化 Runbook 範例

透過在圖形化編輯器 [文件庫] 窗格的認證上按一下滑鼠右鍵，然後選取 [加入至畫布]  ，即可將 **Get-AutomationPSCredential** 活動加入至圖形化 Runbook。

![加入認證至畫布](../media/credentials/credential-add-canvas.png)

下圖顯示在圖形化 Runbook 中使用認證的範例。  在此情況下，它用，提供對 Azure 資源的 runbook 的驗證中所述[使用 Azure AD 使用者帳戶驗證 Runbook](../automation-create-aduser-account.md)。  第一個活動會擷取可存取 Azure 訂用帳戶的認證。  然後 **Add-AzureAccount** 活動會使用這個認證來為隨後的任何活動提供驗證。  因為 [Get-AutomationPSCredential](../automation-graphical-authoring-intro.md#links-and-workflow) 需要單一物件，因此推出了 **管線連結** 。  

![加入認證至畫布](../media/credentials/get-credential.png)

## <a name="using-a-powershell-credential-in-dsc"></a>在 DSC 中使用 PowerShell 認證

雖然 Azure 自動化中的 DSC 組態可以參考使用的認證資產**Get-automationpscredential**，傳入認證資產可以也是透過參數，如果想。 如需詳細資訊，請參閱 [編譯 Azure Automation DSC 中的設定](../automation-dsc-compile.md#credential-assets)。

## <a name="using-credentials-in-python2"></a>使用 Python2 中的認證

下列範例顯示存取 Python2 Runbook 中認證的範例。

```python
import automationassets
from automationassets import AutomationAssetNotFound

# get a credential
cred = automationassets.get_automation_credential("credtest")
print cred["username"]
print cred["password"]
```

## <a name="next-steps"></a>後續步驟

* 若要深入了解圖形化編寫中的連結，請參閱 [圖形化編寫中的連結](../automation-graphical-authoring-intro.md#links-and-workflow)
* 若要了解使用自動化的不同驗證方法，請參閱 [Azure 自動化安全性](../automation-security-overview.md)
* 若要開始使用圖形化 Runbook，請參閱 [我的第一個圖形化 Runbook](../automation-first-runbook-graphical.md)
* 若要开始使用 PowerShell 工作流 Runbook，请参阅 [我的第一个 PowerShell 工作流 Runbook](../automation-first-runbook-textual.md)
* 若要開始使用 Python2 Runbook，請參閱[我的第一個 Python2 Runbook](../automation-first-runbook-textual-python2.md) 
