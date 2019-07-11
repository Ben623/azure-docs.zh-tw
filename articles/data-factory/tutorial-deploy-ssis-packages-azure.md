---
title: 佈建 Azure-SSIS 整合執行階段 | Microsoft Docs
description: 了解如何在 Azure Data Factory 中佈建 Azure-SSIS 整合執行階段，讓您可以在 Azure 部署並執行 SSIS 套件。
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: tutorial
ms.date: 06/26/2019
author: swinarko
ms.author: sawinark
ms.reviewer: douglasl
manager: craigg
ms.openlocfilehash: bd2c055d2f7f1e90918cb160cfdebfe88f7f8ea4
ms.sourcegitcommit: 9b80d1e560b02f74d2237489fa1c6eb7eca5ee10
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/01/2019
ms.locfileid: "67484802"
---
# <a name="provision-the-azure-ssis-integration-runtime-in-azure-data-factory"></a>在 Azure Data Factory 中佈建 Azure-SSIS 整合執行階段

本教學課程提供使用 Azure 入口網站在 Azure Data Factory (ADF) 中佈建 Azure-SQL Server Integration Services (SSIS) 整合執行階段 (IR) 的步驟。 套件若部署到 Azure SQL Database 伺服器/受控執行個體 (專案部署模型) 所裝載的 SSIS 目錄 (SSISDB) 以及部署到檔案系統/檔案共用/Azure 檔案 (套件部署模型) 中，Azure-SSIS IR 都可支援執行這些套件。 佈建好 Azure SSIS IR 後，您可以接著使用 SQL Server Data Tools (SSDT)/SQL Server Management Studio (SSMS) 等熟悉的工具和命令列公用程式 (例如 `dtinstall`/`dtutil`/`dtexec`)，在 Azure 中部署並執行您的套件。 如需 Azure-SSIS IR 的概念資訊，請參閱 [Azure-SSIS 整合執行階段概觀](concepts-integration-runtime.md#azure-ssis-integration-runtime)。

在本教學課程中，您會完成下列步驟：

> [!div class="checklist"]
> * 建立資料處理站。
> * 佈建 Azure-SSIS 整合執行階段。

## <a name="prerequisites"></a>必要條件

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

- **Azure 訂用帳戶**。 如果您沒有 Azure 訂用帳戶，請在開始前建立 [免費帳戶](https://azure.microsoft.com/free/) 。
- **Azure SQL Database 伺服器 (選用)** 。 如果您還沒有資料庫伺服器，請在 Azure 入口網站中建立一個，然後再開始。 ADF 接著會在此資料庫伺服器上建立 SSISDB。 建議於整合執行階段所在的相同 Azure 區域中建立資料庫伺服器。 此設定可讓整合執行階段將執行記錄寫入 SSISDB，而不需要跨 Azure 區域。 
    - 根據選取的資料庫伺服器，SSISDB 可代表您建立為單一資料庫、彈性集區的一部分，或建立在受控執行個體中，並且可在公用網路中或透過加入虛擬網路來存取。 如需有關選擇適當資料庫伺服器類型來裝載 SSISDB 的指引，請參閱[比較 Azure SQL Database 單一資料庫/彈性集區/受控執行個體](../data-factory/create-azure-ssis-integration-runtime.md#compare-sql-database-single-databaseelastic-pool-and-sql-database-managed-instance)。 如果您使用虛擬網路中具有虛擬網路服務端點/受控執行個體的 Azure SQL Database 伺服器來裝載 SSISDB，或要求存取內部部署資料，則須將 Azure-SSIS IR 加入虛擬網路，請參閱[在虛擬網路中建立 Azure-SSIS IR](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime)。
    - 確認資料庫伺服器的 [允許存取 Azure 服務]  設定已啟用。 如果您使用虛擬網路中具有虛擬網路服務端點/受控執行個體的 Azure SQL Database 伺服器來裝載 SSISDB，則不適用此項目。 如需詳細資訊，請參閱[保護 Azure SQL 資料庫資料庫](../sql-database/sql-database-security-tutorial.md#create-firewall-rules)。 若要使用 PowerShell 來啟用此設定，請參閱 [New-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule)。
    - 新增用戶端電腦的 IP 位址，或新增 IP 位址範圍，其中包含資料庫伺服器之防火牆設定中用戶端電腦 IP 位址到用戶端 IP 位址清單。 如需詳細資訊，請參閱 [Azure SQL Database 伺服器層級和資料庫層級防火牆規則](../sql-database/sql-database-firewall-configure.md)
    - 若要連線至資料庫伺服器，您可以使用伺服器管理員認證來執行 SQL 驗證，或使用 ADF 受控識別來執行 Azure Active Directory (AAD) 驗證。 對於後者，您需要將 ADF 的受控識別新增到具有資料庫伺服器存取權的 AAD 群組，請參閱[使用 AAD 驗證建立 Azure-SSIS IR](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime)。
    - 請確認您的資料庫伺服器尚未有 SSISDB。 Azure-SSIS IR 的佈建不支援使用現有的 SSISDB。

> [!NOTE]
> - 如需目前可使用 ADF 和 Azure-SSIS IR 的 Azure 區域清單，請參閱[依區域的 ADF + SSIS IR 可用性](https://azure.microsoft.com/global-infrastructure/services/?products=data-factory&regions=all)。 

## <a name="create-a-data-factory"></a>建立 Data Factory

1. 啟動 **Microsoft Edge** 或 **Google Chrome** 網頁瀏覽器。 目前，只有 Microsoft Edge 和 Google Chrome 網頁瀏覽器支援 Data Factory UI。 
1. 登入 [Azure 入口網站](https://portal.azure.com/)。 
1. 選取左側功能表上的 [新增]  、[資料 + 分析]  ，然後選取 [資料處理站]  。 

   ![在 [新增] 窗格中選取資料處理站](./media/tutorial-create-azure-ssis-runtime-portal/new-data-factory-menu.png)

1. 在 [新增資料處理站]  頁面上，於 [名稱]  之下輸入 **MyAzureSsisDataFactory**。 

   ![[新增資料處理站] 頁面](./media/tutorial-create-azure-ssis-runtime-portal/new-azure-data-factory.png)

   Azure Data Factory 的名稱必須是 *全域唯一的*。 如果您收到下列錯誤，請變更資料處理站的名稱 (例如 **&lt;yourname&gt;MyAzureSsisDataFactory**)，然後試著重新建立。 如需 Data Factory 成品的命名規則，請參閱 [Data Factory - 命名規則](naming-rules.md)一文。 

   `Data factory name “MyAzureSsisDataFactory” is not available`

1. 針對 [訂用帳戶]  ，選取您要用來建立資料處理站的 Azure 訂用帳戶。 
1. 針對**資源群組**，請執行下列其中一個步驟︰ 

   - 選取 [使用現有的]  ，然後從清單中選取現有的資源群組。 
   - 選取 [建立新的]  ，然後輸入資源群組的名稱。 

   若要了解資源群組，請參閱 [使用資源群組管理您的 Azure 資源](../azure-resource-manager/resource-group-overview.md)。 
1. 針對 [版本]  ，選取 [V2 (預覽版)]  。 
1. 針對 [位置]  ，選取資料處理站的位置。 此清單只會顯示支援建立資料處理站的位置。 
1. 選取 [釘選到儀表板]  。 
1. 選取 [建立]  。 
1. 在儀表板上，您會看到 [正在部署資料處理站]  狀態的下列圖格︰ 

   ![[部署 Data Factory] 圖格](media/tutorial-create-azure-ssis-runtime-portal/deploying-data-factory.png)

1. 建立完成之後，您會看到 [Data Factory]  頁面。 

   ![資料處理站首頁](./media/tutorial-create-azure-ssis-runtime-portal/data-factory-home-page.png)

1. 選取 [編寫與監視]  ，以在個別索引標籤上開啟 Data Factory 使用者介面 (UI)。 

## <a name="create-an-azure-ssis-integration-runtime"></a>建立 Azure-SSIS 整合執行階段

### <a name="from-the-data-factory-overview"></a>從 Data Factory 概觀

1. 在 [讓我們開始吧]  頁面上，選取 [設定 SSIS 整合執行階段]  圖格。 

   ![[設定 SSIS 整合執行階段] 圖格](./media/tutorial-create-azure-ssis-runtime-portal/configure-ssis-integration-runtime-tile.png)

1. 如需設定 Azure-SSIS IR 的剩餘步驟，請參閱[佈建 Azure-SSIS 整合執行階段](#provision-an-azure-ssis-integration-runtime)一節。 

### <a name="from-the-authoring-ui"></a>從撰寫 UI

1. 在 Azure Data Factory UI 中，切換到 [編輯]  索引標籤，選取 [連線]  ，然後切換到 [整合執行階段]  索引標籤，以檢視資料處理站中的現有整合執行階段。 

   ![檢視現有 IR 的選取項目](./media/tutorial-create-azure-ssis-runtime-portal/view-azure-ssis-integration-runtimes.png)

1. 選取 [新增]  以建立 Azure-SSIS IR。 

   ![整合執行階段功能表](./media/tutorial-create-azure-ssis-runtime-portal/edit-connections-new-integration-runtime-button.png)

1. 在 [整合執行階段設定]  視窗中，選取 [隨即轉移現有的 SSIS 套件以在 Azure 中執行]  ，然後選取 [下一步]  。 

   ![指定整合執行階段的類型](./media/tutorial-create-azure-ssis-runtime-portal/integration-runtime-setup-options.png)

1. 如需設定 Azure-SSIS IR 的剩餘步驟，請參閱[佈建 Azure-SSIS 整合執行階段](#provision-an-azure-ssis-integration-runtime)一節。 

## <a name="provision-an-azure-ssis-integration-runtime"></a>佈建 Azure-SSIS 整合執行階段

1. 在 [整合執行階段設定]  的 [一般設定]  頁面上，完成下列步驟： 

   ![一般設定](./media/tutorial-create-azure-ssis-runtime-portal/general-settings.png)

   a. 針對 [名稱]  ，輸入取您整合執行階段的名稱。 

   b. 針對 [描述]  ，輸入整合執行階段的描述。 

   c. 針對 [位置]  ，選取整合執行階段的位置。 系統只會顯示支援的位置。 我們建議您選取裝載 SSISDB 的資料庫伺服器所在位置。 

   d. 針對 [節點大小]  ，選取整合執行階段叢集中的節點大小。 系統只會顯示支援的節點大小。 如果您想要執行許多計算/記憶體密集套件，請選取大型節點大小 (相應增加)。 

   e. 針對 [節點數目]  ，選取整合執行階段叢集中的節點數目。 系統只會顯示支援的節點數目。 如果您想要平行執行許多套件，請選取具有許多節點的大型叢集 (相應放大)。 

   f. 針對 [版本/授權]  ，選取適合您整合執行階段的 SQL Server 版本/授權：[標準版] 或 [企業版]。 如果您想要在整合執行階段上使用進階/高階功能，請選取 [企業版]。 

   g. 針對 [節省費用]  ，選取適合您整合執行階段的 Azure Hybrid Benefit (AHB) 選項：[是] 或 [否]。 如果您想要自備附有軟體保證的 SQL Server 授權，以混搭使用來享有節省費用的權益，請選擇 [是]。 

   h. 按 [下一步]  。 

1. 在 [SQL 設定]  頁面上，完成下列步驟： 

   ![SQL 設定](./media/tutorial-create-azure-ssis-runtime-portal/sql-settings.png)

   a. 針對要在 Azure-SSIS IR 上執行的套件，在 [建立 SSIS 目錄...]  核取方塊中上選取部署模型：套件部署到您資料庫伺服器所裝載 SSISDB 的專案部署模型，或套件部署到檔案系統/檔案共用/Azure 檔案的套件部署模型。 如果您核取該選項，您必須用自己的資料庫伺服器來裝載 SSISDB，而我們會為您建立及管理 SSISDB。
   
   b. 針對 [訂用帳戶]  ，選取具有資料庫伺服器可裝載 SSISDB 的 Azure 訂用帳戶。 

   c. 針對 [位置]  ，選取裝載 SSISDB 的資料庫伺服器所在位置。 我們建議您選取整合執行階段所在的相同位置。 

   d. 針對 [目錄資料庫伺服器端點]  ，選取裝載 SSISDB 的資料庫伺服器端點。 根據選取的資料庫伺服器，SSISDB 可代表您建立為單一資料庫、彈性集區的一部分，或建立在受控執行個體中，並且可在公用網路中或透過加入虛擬網路來存取。 如需有關選擇適當資料庫伺服器類型來裝載 SSISDB 的指引，請參閱[比較 Azure SQL Database 單一資料庫/彈性集區/受控執行個體](../data-factory/create-azure-ssis-integration-runtime.md#compare-sql-database-single-databaseelastic-pool-and-sql-database-managed-instance)。 如果您選取虛擬網路中具有虛擬網路服務端點/受控執行個體的 Azure SQL Database 伺服器來裝載 SSISDB，或要求存取內部部署資料，則須將 Azure-SSIS IR 加入虛擬網路，請參閱[在虛擬網路中建立 Azure-SSIS IR](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime)。 

   e. 在 [使用 AAD 驗證...]  核取方塊上，選取您資料庫伺服器用來裝載 SSISDB 的驗證方法：使用您 ADF 受控識別的 SQL 驗證 AAD 驗證。 如果加以核取，您需要將 ADF 的受控識別新增到具有資料庫伺服器存取權的 AAD 群組，請參閱[使用 AAD 驗證建立 Azure-SSIS IR](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime)。 

   f. 針對 [管理使用者名稱]  ，為裝載 SSISDB 的資料庫伺服器輸入 SQL 驗證使用者名稱。 

   g. 針對 [管理密碼]  ，為裝載 SSISDB 的資料庫伺服器輸入 SQL 驗證密碼。 

   h. 針對 [目錄資料庫服務層級]  ，選取您資料庫伺服器用來裝載 SSISDB 的服務層級：基本/標準/進階層或彈性集區名稱。 

   i. 按一下 [測試連線]  ，如果成功的話，按 [下一步]  。 

1. 在 [進階設定]  頁面上，完成下列步驟： 

   ![進階設定](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings.png)

   a. 針對 [每節點的平行執行數上限]  ，選取執行階段叢集中每節點上可同時執行的套件數量上限。 系統只會顯示支援的套件數目。 如果您想要使用一個以上的核心，來執行計算/記憶體密集的單一大型/重量套件，請選取較低的數字。 如果您想要在單一核心中執行一個或多個小型/輕量套件，請選取較高的數字。 

   b. 針對 [自訂安裝容器的 SAS URI]  ，選擇性地輸入 Azure 儲存體 Blob 容器的共用存取簽章 (SAS) 統一資源識別項 (URI)，該容器是您安裝指令碼且儲存指令碼相關檔案的地方，請參閱[自訂 Azure-SSIS IR 的安裝](https://docs.microsoft.com/azure/data-factory/how-to-configure-azure-ssis-ir-custom-setup)。 

   c. 在 [選取 VNet...]  核取方塊上，選取是否要將整合執行階段加入虛擬網路。 如果您使用虛擬網路中具有虛擬網路服務端點/受控執行個體的 Azure SQL Database 伺服器來裝載 SSISDB，或要求存取內部部署資料，則須選取此項目，請參閱[在虛擬網路中建立 Azure-SSIS IR](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime)。 

1. 按一下 [完成]  以啟動整合執行階段的建立。 

   > [!NOTE]
   > 若不包含任何自訂的安裝時間，此程序應該會在 5 分鐘內完成。
   >
   > 如果您使用 SSISDB，ADF 服務將會連線到您資料庫伺服器來準備 SSISDB。 
   > 
   > 當您佈建 Azure-SSIS IR 時，也會安裝 Access 可轉散發套件和適用於 SSIS 的 Azure Feature Pack。 除了內建元件已支援的資料來源，這些元件還提供對 Excel/Access 檔案及各種 Azure 資料來源的連線能力。 您也可以安裝其他元件，請參閱 [Azure-SSIS IR 的自訂安裝](how-to-configure-azure-ssis-ir-custom-setup.md)。

1. 如有必要，在 [連線]  索引標籤上，切換到 [整合執行階段]  。 選取 [重新整理]  可重新整理狀態。 

   ![建立狀態，以及 [重新整理] 按鈕](./media/tutorial-create-azure-ssis-runtime-portal/azure-ssis-ir-creation-status.png)

1. 使用 [動作]  資料行中的連結來停止/啟動、編輯或刪除整合執行階段。 使用最後一個連結來檢視整合執行階段的 JSON 程式碼。 只有當 IR 停止時，才會啟用編輯和刪除按鈕。 

   ![[動作] 資料行中的連結](./media/tutorial-create-azure-ssis-runtime-portal/azure-ssis-ir-actions.png) 

## <a name="deploy-ssis-packages"></a>部署 SSIS 套件

如果您使用 SSISDB，您可以使用透過伺服器端點來與資料庫伺服器連線的 SSDT/SSMS 工具，將套件部署到其中，並在 Azure-SSIS IR 上執行這些套件。 對於具有公用端點的 Azure SQL Database 伺服器/受控執行個體，伺服器端點格式分別為 `<server name>.database.windows.net`/`<server name>.public.<dns prefix>.database.windows.net,3342`。 如果您不是使用 SSISDB，您可以使用 `dtinstall`/`dtutil`/`dtexec` 命令列公用程式，將套件部署到檔案系統/檔案共用/Azure 檔案，並在 Azure-SSIS IR 上執行這些套件。 如需詳細資訊，請參閱[部署 SSIS 套件](/sql/integration-services/packages/deploy-integration-services-ssis-projects-and-packages#deploy-packages-to-integration-services-server)。 在這兩種情況下，您也可以使用 ADF 管線中的執行 SSIS 套件活動在 Azure-SSIS IR 上執行已部署的套件，請參閱[以頂級 ADF 活動的形式叫用 SSIS 套件執行](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity)。 

另請參閱 SSIS 文件中的下列文章： 

- [在 Azure 中部署、執行和監視 SSIS 套件](/sql/integration-services/lift-shift/ssis-azure-deploy-run-monitor-tutorial) 
- [連線至 Azure 中的 SSISDB](/sql/integration-services/lift-shift/ssis-azure-connect-to-catalog-database) 
- [在 Azure 中排程套件執行](/sql/integration-services/lift-shift/ssis-azure-schedule-packages) 
- [透過 Windows 驗證連線至內部部署資料來源](/sql/integration-services/lift-shift/ssis-azure-connect-with-windows-auth) 

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已了解如何： 

> [!div class="checklist"]
> * 建立資料處理站。
> * 佈建 Azure-SSIS 整合執行階段。
> * 部署 SSIS 套件

若要了解如何自訂 Azure-SSIS 整合執行階段，請前往下列文章： 

> [!div class="nextstepaction"]
> [自訂 AZURE-SSIS IR](https://docs.microsoft.com/azure/data-factory/how-to-configure-azure-ssis-ir-custom-setup)