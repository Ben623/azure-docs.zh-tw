---
title: 自訂 Azure-SSIS 整合執行階段的安裝
description: 本文說明如何使用 Azure-SSIS 整合執行階段 (IR) 的自訂安裝介面來安裝其他元件或變更設定
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
author: swinarko
ms.author: sawinark
manager: mflasko
ms.reviewer: douglasl
ms.custom: seo-lt-2019
ms.date: 1/25/2019
ms.openlocfilehash: d80ff102648deebf63cc0752b2980274cb90aeb9
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/08/2019
ms.locfileid: "74922887"
---
# <a name="customize-setup-for-the-azure-ssis-integration-runtime"></a>自訂 Azure-SSIS 整合執行階段的安裝

Azure-SSIS 整合執行階段的自訂安裝介面所提供的介面，可讓您自行新增在佈建或重新設定 Azure-SSIS IR 期間的安裝步驟。 自訂安裝可讓您變更預設作業組態或環境 (例如，用以啟動其他 Windows 服務或保存檔案共用的存取認證)，或是在 Azure-SSIS IR 的每個節點上安裝其他元件 (例如組件、驅動程式或擴充功能)。

您可以準備指令碼及其相關聯的檔案，並將其上傳至您 Azure 儲存體帳戶中的 Blob 容器中，以設定您的自訂安裝。 當您佈建或重新設定 Azure-SSIS IR 時，您可以提供容器的共用存取簽章 (SAS) 統一資源識別項 (URI)。 隨後，Azure-SSIS IR 的每個節點會從您的容器下載指令碼及其相關聯的檔案，並使用提高的權限執行您的自訂安裝。 自訂安裝完成後，每個節點會將執行的標準輸出和其他記錄上傳到您的容器中。

您可以安裝免費或未授權的元件，和付費或授權的元件。 如果您是 ISV，請參閱[如何開發 Azure-SSIS IR 的付費或授權元件](how-to-develop-azure-ssis-ir-licensed-components.md)。

> [!IMPORTANT]
> Azure-SSIS IR 的 v2 系列節點不適用於自訂安裝，因此請改用 v3 系列節點。  如果您已經使用 v2 系列節點，請儘快切換成使用 v3 系列節點。

## <a name="current-limitations"></a>目前的限制

-   如果您想要使用 `gacutil.exe` 在全域組件快取 (GAC) 中安裝組件，您必須在自訂安裝的過程中提供`gacutil.exe`，或者使用公開預覽容器中提供的副本。

-   如果您想要參考指令碼中的子資料夾，`msiexec.exe` 不支援以 `.\` 標記法參考根資料夾。 使用類似 `msiexec /i "MySubfolder\MyInstallerx64.msi" ...` 的命令，而不是 `msiexec /i ".\MySubfolder\MyInstallerx64.msi" ...`。

-   如果您需要將使用自訂安裝的 Azure-SSIS IR 加入虛擬網路，目前僅支援 Azure Resource Manager 虛擬網路。 傳統虛擬網路不受支援。

-   Azure-SSIS IR 上目前不支援系統管理共用。

-   Azure SSIS IR 不支援 IBM iSeries Access ODBC 驅動程式。 在自訂安裝期間，您可能會看到安裝錯誤。 請洽詢 IBM 支援以取得協助。

## <a name="prerequisites"></a>必要條件

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

若要自訂 Azure-SSIS IR，您必須具備下列條件：

-   [Azure 訂用帳戶](https://azure.microsoft.com/)

-   [Azure SQL Database 或受控執行個體伺服器](https://ms.portal.azure.com/#create/Microsoft.SQLServer)

-   [佈建您的 Azure-SSIS IR](https://docs.microsoft.com/azure/data-factory/tutorial-deploy-ssis-packages-azure)

-   [Azure 儲存體帳戶](https://azure.microsoft.com/services/storage/)。 進行自訂安裝時，您可以將自訂安裝指令碼及其相關聯的檔案上傳並儲存在 Blob 容器中。 自訂安裝程序也會將其執行記錄上傳至相同的 Blob 容器。

## <a name="instructions"></a>範例的指示

1. 下載並安裝 [Azure PowerShell](/powershell/azure/install-az-ps)。

1. 準備您的自訂安裝指令碼和及相關聯的檔案 (例如 .bat、.cmd、.exe、.dll、.msi 或 .ps1 檔案)。

   1.  您必須具有名為 `main.cmd` 的指令碼檔案，這是您自訂安裝的進入點。

   1.  您必須確定腳本可以無訊息的方式執行，建議您先在本機電腦上測試腳本。

   1.  如果您想要將其他工具 (例如 `msiexec.exe`) 所產生的其他記錄上傳到您的容器中，請將預先定義的環境變數 `CUSTOM_SETUP_SCRIPT_LOG_DIR` 指定為指令碼中的記錄資料夾 (例如 `msiexec /i xxx.msi /quiet /lv %CUSTOM_SETUP_SCRIPT_LOG_DIR%\install.log`)。

1. 下載、安裝並啟動 [Azure 儲存體總管](https://storageexplorer.com/)。

   1. 在 [(本機與已連結)] 下方，以滑鼠右鍵選取 [儲存體帳戶]，然後選取 [連線到 Azure 儲存體]。

      ![連線到 Azure 儲存體](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image1.png)

   1. 選取 [使用儲存體帳戶名稱和金鑰]，然後選取 [下一步]。

      ![使用儲存體帳戶名稱和金鑰](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image2.png)

   1. 輸入您的 Azure 儲存體帳戶名稱和金鑰，選取 [下一步]，然後選取 [連線]。

      ![提供儲存體帳戶名稱和金鑰](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image3.png)

   1. 在已連線的 Azure 儲存體帳戶下，以滑鼠右鍵按一下 [Blob 容器]，選取 [建立 Blob 容器]，然後為新容器命名。

      ![建立 Blob 容器](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image4.png)

   1. 選取新容器，並上傳您的自訂安裝指令碼及其相關聯的檔案。 請務必將 `main.cmd` 上傳至容器的最上層，而非任何資料夾中。 此外，也請確定您的容器只包含必要的自訂安裝檔，如此一來，稍後將它們下載到您的 Azure-SSIS IR 時，才不會花費很長的時間。 自訂安裝程式的最長期間目前設定為45分鐘的時間，這包括從您的容器下載所有檔案，並將它們安裝在 Azure SSIS IR 的時間。 如果需要較長的時間，請提出支援票證。

      ![將檔案上傳至 Blob 容器](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image5.png)

   1. 以滑鼠右鍵按一下容器，然後選取 [取得共用存取簽章]。

      ![取得容器的共用存取簽章](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image6.png)

   1. 為您的容器建立具有夠長的到期時間和讀取 + 寫入 + 列出權限的 SAS URI。 您必須具有 SAS URI，才能在 Azure-SSIS IR 的任何節點重新安裝映像/重新啟動時，下載並執行您的自訂安裝指令碼，及其相關聯的檔案。 您必須具備寫入權限才能上傳安裝執行記錄。

      > [!IMPORTANT]
      > 如果您會在這段時間內定期停止和啟動 Azure-SSIS IR，請確定在 Azure-SSIS IR 的整個生命週期內 (從建立到刪除)，SAS URI 不會到期，且自訂安裝資源永遠可供使用。

      ![產生容器的共用存取簽章](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image7.png)

   1. 複製並儲存容器 SAS URI。

      ![複製並儲存共用存取簽章](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image8.png)

   1. 當您使用 Data Factory UI 佈建或重新設定 Azure-SSIS IR 時，請在 [進階設定] 面板上於適當的欄位中輸入 SAS URI：

      ![輸入共用存取簽章](media/tutorial-create-azure-ssis-runtime-portal/advanced-settings.png)

      當您使用 PowerShell 佈建或重新設定 Azure-SSIS IR 時，在啟動 Azure-SSIS IR 之前，請先執行 `Set-AzDataFactoryV2IntegrationRuntime` Cmdlet，並以容器的 SAS URI 作為新 `SetupScriptContainerSasUri` 參數的值。 例如：

      ```powershell
      Set-AzDataFactoryV2IntegrationRuntime -DataFactoryName $MyDataFactoryName `
                                                -Name $MyAzureSsisIrName `
                                                -ResourceGroupName $MyResourceGroupName `
                                                -SetupScriptContainerSasUri $MySetupScriptContainerSasUri

      Start-AzDataFactoryV2IntegrationRuntime -DataFactoryName $MyDataFactoryName `
                                                  -Name $MyAzureSsisIrName `
                                                  -ResourceGroupName $MyResourceGroupName
      ```

   1. 在自訂安裝完成且 Azure-SSIS IR 啟動後，您可以在儲存體容器的 `main.cmd.log` 資料夾中找到 `main.cmd` 的標準輸出和其他執行記錄。

1. 若要查看其他自訂安裝範例，請透過 Azure 儲存體總管連線至公用預覽容器。

   a.  在 [(本機與已連結)] 下方，以滑鼠右鍵按一下 [儲存體帳戶]，然後依序選取 [連線到 Azure 儲存體]、[使用連接字串或共用存取簽章 URI] 和 [下一步]。

      ![使用共用存取簽章連線至 Azure 儲存體](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image9.png)

   b.這是另一個 C# 主控台應用程式。  選取 [使用 SAS URI]，並為公用預覽容器輸入下列 SAS URI。 選取 [下一步]，然後選取 [連線]。

      `https://ssisazurefileshare.blob.core.windows.net/publicpreview?sp=rl&st=2018-04-08T14%3A10%3A00Z&se=2020-04-10T14%3A10%3A00Z&sv=2017-04-17&sig=mFxBSnaYoIlMmWfxu9iMlgKIvydn85moOnOch6%2F%2BheE%3D&sr=c`

      ![提供容器的共用存取簽章](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image10.png)

   c. 選取已連線的公用預覽容器，然後按兩下 `CustomSetupScript` 資料夾。 此資料夾中包含下列項目：

      1. 一個 `Sample` 資料夾，其中包含自訂安裝程式，用以在 Azure-SSIS IR 的每個節點上安裝基本工作。 此工作不會執行任何動作，而會休眠數秒鐘。 此資料夾也包含 `gacutil` 資料夾，其中的整個內容 (`gacutil.exe`、`gacutil.exe.config` 和 `1033\gacutlrc.dll`) 可以原封不動地複製到您的容器。 此外，`main.cmd` 還會包含註解以供保存檔案共用的存取認證。

      1. 一個 `UserScenarios` 資料夾，其中包含實際使用者案例的數個自訂安裝。

   ![公開預覽容器的內容](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image11.png)

   d. 按兩下 `UserScenarios` 資料夾。 此資料夾中包含下列項目：

      1. 一個 `.NET FRAMEWORK 3.5` 資料夾，其中包含要安裝舊版 .NET Framework (Azure-SSIS IR 每個節點上的自訂元件可能需要它) 的自訂安裝。

      1. 一個 `BCP` 資料夾，其中包含自訂安裝程式，用以在 Azure-SSIS IR 的每個節點上安裝 SQL Server 命令列公用程式 (`MsSqlCmdLnUtils.msi`)，包括大量複製程式 (`bcp`)。

      1. 一個 `EXCEL` 資料夾，其中包含自訂安裝程式，用以在 Azure-SSIS IR 的每個節點上安裝開放原始碼組件 (`DocumentFormat.OpenXml.dll`、`ExcelDataReader.DataSet.dll` 和 `ExcelDataReader.dll`)。

      1. `ORACLE ENTERPRISE` 資料夾，其中包含自訂安裝指令碼 (`main.cmd`) 和無訊息安裝組態檔 (`client.rsp`)，用以在 Azure-SSIS IR Enterprise Edition 的每個節點上安裝 Oracle 連接器和 OCI 驅動程式。 此安裝可讓您使用 Oracle 連線管理員、來源和目的地。 首先，從 [Microsoft Download Center](https://www.microsoft.com/en-us/download/details.aspx?id=55179) 下載 Microsoft Connectors v5.0 for Oracle (`AttunitySSISOraAdaptersSetup.msi` 和 `AttunitySSISOraAdaptersSetup64.msi`)，並從 [Oracle](https://www.oracle.com/technetwork/database/enterprise-edition/downloads/database12c-win64-download-2297732.html)，下載最新的 Oracle 用戶端 (例如，`winx64_12102_client.zip`)，然後將它們全部一起與 `main.cmd` 和 `client.rsp` 上傳到您的容器。 如果您使用 TNS 連線至 Oracle，則也必須下載 `tnsnames.ora`、加以編輯，然後將其上傳到您的容器中，使其能夠在安裝期間複製到 Oracle 安裝資料夾中。

      1. 一個 `ORACLE STANDARD ADO.NET` 資料夾，其中包含自訂安裝指令碼 (`main.cmd`)，用以在 Azure-SSIS IR 的每個節點上安裝 Oracle ODP.NET 驅動程式。 此安裝可讓您使用 ADO.NET 連線管理員、來源和目的地。 首先，請從 [Oracle](https://www.oracle.com/technetwork/database/windows/downloads/index-090165.html) 下載最新的 Oracle ODP.NET 驅動程式，例如 `ODP.NET_Managed_ODAC122cR1.zip`，然後與 `main.cmd` 一起將其上傳至您的容器。
       
      1. 一個 `ORACLE STANDARD ODBC` 資料夾，其中包含自訂安裝指令碼 (`main.cmd`)，用以在 Azure-SSIS IR 的每個節點上安裝 Oracle ODBC 驅動程式及設定 DSN。 此安裝可讓您使用 ODBC 連線管理員/來源/目的地或 Power Query 連線管理員/來源搭配 ODBC 資料來源種類，來連線到 Oracle 伺服器。 首先，請下載最新的 Oracle Instant Client (基本套件或基本精簡套件) - 例如，從[這裡](https://www.oracle.com/technetwork/topics/winx64soft-089540.html)下載 64 位元套件 (基本套件：`instantclient-basic-windows.x64-18.3.0.0.0dbru.zip`、基本精簡套件：`instantclient-basiclite-windows.x64-18.3.0.0.0dbru.zip`、ODBC 套件：`instantclient-odbc-windows.x64-18.3.0.0.0dbru.zip`) 或從[這裡](https://www.oracle.com/technetwork/topics/winsoft-085727.html)下載 32 位元套件 (基本套件 ：`instantclient-basic-nt-18.3.0.0.0dbru.zip`、基本精簡套件：`instantclient-basiclite-nt-18.3.0.0.0dbru.zip`、ODBC 套件：`instantclient-odbc-nt-18.3.0.0.0dbru.zip`)，然後將它們與 `main.cmd` 一起上傳到您的容器中。

      1. 一個 `SAP BW` 資料夾，其中包含自訂安裝指令碼 (`main.cmd`)，用以在 Azure-SSIS IR Enterprise Edition 的每個節點上安裝 SAP .NET 連接器組件 (`librfc32.dll`)。 此安裝可讓您使用 SAP BW 連線管理員、來源和目的地。 首先，從 SAP 安裝資料夾將 64 位元或 32 位元版的 `librfc32.dll` 連同 `main.cmd` 上傳到您的容器中。 接著，此指令碼會在安裝期間將 SAP 組件複製到 `%windir%\SysWow64` 或 `%windir%\System32` 資料夾中。

      1. 一個 `STORAGE` 資料夾，其中包含自訂安裝程式，用以在 Azure-SSIS IR 的每個節點上安裝 Azure PowerShell。 此安裝可讓您部署和執行 SSIS 套件，以執行 [PowerShell 指令碼來管理您的 Azure 儲存體帳戶](https://docs.microsoft.com/azure/storage/blobs/storage-how-to-use-blobs-powershell)。 將 `main.cmd` (一個範例 `AzurePowerShell.msi`) 和 `storage.ps1` 複製到您的容器 (或安裝最新版本)。 使用 PowerShell.dtsx 作為您套件的範本。 此套件範本結合了 [Azure Blob 下載工作](https://docs.microsoft.com/sql/integration-services/control-flow/azure-blob-download-task) (會下載 `storage.ps1` 作為可修改的 PowerShell 指令碼) 和[執行程序工作](https://blogs.msdn.microsoft.com/ssis/2017/01/26/run-powershell-scripts-in-ssis/) (會在每個節點上執行指令碼)。

      1. `TERADATA` 資料夾，其中包含自訂安裝指令碼 (`main.cmd`) 及其相關聯的檔案 (`install.cmd`)，和安裝程式套件 (`.msi`)。 這些檔案會在 Azure-SSIS IR Enterprise Edition 的每個節點上安裝 Teradata 連接器、TPT API 和 ODBC 驅動程式。 此安裝可讓您使用 Teradata 連線管理員、來源和目的地。 首先，從 [Teradata](http://partnerintelligence.teradata.com) 下載 Teradata Tools and Utilities (TTU) 15.x zip 檔案 (例如 `TeradataToolsAndUtilitiesBase__windows_indep.15.10.22.00.zip`)，然後將其連同上述的 `.cmd` 和 `.msi` 檔案一起上傳到您的容器中。

   ![使用者案例資料夾中的資料夾](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image12.png)

   e. 若要嘗試使用這些自訂安裝範例，請複製所選資料夾中的內容，並貼到您的容器中。 當您使用 PowerShell 佈建或重新設定 Azure-SSIS IR 時，請執行 `Set-AzDataFactoryV2IntegrationRuntime` Cmdlet，並以容器的 SAS URI 作為新 `SetupScriptContainerSasUri` 參數的值。

## <a name="next-steps"></a>後續步驟

-   [Azure-SSIS 整合執行階段的 Enterprise Edition](how-to-configure-azure-ssis-ir-enterprise-edition.md)

-   [如何開發 Azure-SSIS 整合執行階段的付費或授權自訂元件](how-to-develop-azure-ssis-ir-licensed-components.md)
