---
title: 在 Azure Data Factory 中建立自我裝載整合執行階段 | Microsoft Docs | Microsoft Docs
description: 了解如何在 Azure Data Factory 中建立自我裝載整合執行階段，它可讓資料處理站存取私人網路中的資料存放區。
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/15/2019
author: nabhishek
ms.author: abnarain
manager: craigg
ms.openlocfilehash: 90e43ab0448646650067dbf151702132f434c01e
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "65967955"
---
# <a name="create-and-configure-a-self-hosted-integration-runtime"></a>建立和設定自我裝載整合執行階段
整合執行階段 (IR) 是 Azure Data Factory 所使用的計算基礎結構，可提供跨不同網路環境的資料整合功能。 如需 IR 的詳細資訊，請參閱[整合執行階段概觀](concepts-integration-runtime.md)。

自我裝載整合執行階段可以在雲端資料存放區以及私人網路的資料存放區之間執行複製活動，並且可以在內部部署或 Azure 虛擬網路中針對計算資源分派轉換活動。 自我裝載整合執行階段必須安裝在內部部署電腦上，或安裝在位於私人網路內的虛擬機器 (VM) 上。  

本文件會說明了如何建立和設定自我裝載 IR。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="high-level-steps-to-install-a-self-hosted-ir"></a>安裝自我裝載 IR 的概略步驟
1. 建立自我裝載整合執行階段。 您可以使用 Azure Data Factory UI 來執行這項工作。 以下是 PowerShell 範例︰

    ```powershell
    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $resourceGroupName -DataFactoryName $dataFactoryName -Name $selfHostedIntegrationRuntimeName -Type SelfHosted -Description "selfhosted IR description"
    ```
  
2. [下載](https://www.microsoft.com/download/details.aspx?id=39717)並在本機電腦上安裝自我裝載整合執行階段。

3. 擷取驗證金鑰，並使用該金鑰註冊自我裝載整合執行階段。 以下是 PowerShell 範例︰

    ```powershell

    Get-AzDataFactoryV2IntegrationRuntimeKey -ResourceGroupName $resourceGroupName -DataFactoryName $dataFactoryName -Name $selfHostedIntegrationRuntimeName  

    ```

## <a name="setting-up-a-self-hosted-ir-on-an-azure-vm-by-using-an-azure-resource-manager-template-automation"></a>使用 Azure Resource Manager 範本 (自動化) 在 Azure VM 上設定自我裝載 IR
您可以使用[此 Azure Resource Manager 範本](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vms-with-selfhost-integration-runtime)在 Azure 虛擬機器上自動進行自我裝載 IR 設定。 此範本可讓您在 Azure 虛擬網路內，輕鬆擁有具高可用性和延展性功能的全功能自我裝載 IR (只要您設定的節點計數等於或高於 2)。

## <a name="command-flow-and-data-flow"></a>命令流程和資料流程
當您在內部部署和雲端之間移動資料時，活動會使用自我裝載整合執行階段將資料從內部部署資料來源傳輸到雲端，反之亦然。

利用自我裝載 IR 進行複製步驟的概略資料流程如下：

![高階概述](media/create-self-hosted-integration-runtime/high-level-overview.png)

1. 資料開發人員使用 PowerShell Cmdlet 在 Azure Data Factory 內建立自我裝載整合執行階段。 目前，Azure 入口網站不支援此功能。
2. 資料開發人員建立內部部署資料存放區的連結服務，方法是指定應用來連結資料存放區的自我裝載整合執行階段執行個體。
3. 自我裝載整合執行階段節點會使用 Windows 資料保護應用程式開發介面 (DPAPI) 將加密，並將認證儲存在本機上。 如果有多個節點設定為高可用性，則該認證會進一步同步處理到其他節點。 每個節點都會使用 DPAPI 來加密認證，並將其儲存在本機上。 認證同步處理無需資料開發人員介入，並且由自我裝載 IR 處理。    
4. Data Factory 服務與自我裝載的整合執行階段通訊進行排程和管理作業，透過*控制通道*使用共用[Azure 服務匯流排轉送](https://docs.microsoft.com/azure/service-bus-relay/relay-what-is-it#wcf-relay)。 當需要執行活動作業時，Data Factory 會將要求與任何認證資訊排入佇列 (如果認證尚未儲存在自我裝載整合執行階段上)。 輪詢佇列之後，自我裝載整合執行階段便會開始作業。
5. 自我裝載整合執行階段會根據複製活動在資料管線中的設定方式，將資料從內部部署存放區複製到雲端儲存體，反之亦然。 針對這個步驟，自我裝載整合執行階段會透過安全的 (HTTPS) 通道，直接與雲端式儲存體服務 (例如 Azure Blob 儲存體) 進行通訊。

## <a name="considerations-for-using-a-self-hosted-ir"></a>使用自我裝載 IR 的考量

- 單一的自我裝載整合執行階段可用於多個內部部署資料來源。 單一的自我裝載整合執行階段可以與同一個 Azure Active Directory 租用戶內的另一個 資料處理站共用。 如需詳細資訊，請參閱[共用自我裝載整合執行階段](#sharing-the-self-hosted-integration-runtime-with-multiple-data-factories)。
- 單一電腦上只能安裝一個自我裝載整合執行階段執行個體。 如果您有兩個 data factory 需要存取內部部署資料來源，請使用[自我裝載 IR 共用功能](#sharing-the-self-hosted-integration-runtime-with-multiple-data-factories)共用自我裝載的整合執行階段，或對兩個安裝自我裝載的整合執行階段在內部部署電腦，一個用於每個資料處理站。  
- 自我裝載整合執行階段不一定要在與資料來源相同的電腦上。 不過，讓自我裝載整合執行階段更靠近資料來源，可以減少自我裝載整合執行階段連線到資料來源花費的時間。 建議您將自我裝載整合執行階段安裝在與裝載內部部署資料來源不同的電腦上。 當自我裝載整合執行階段和資料來源位於不同的電腦上時，自我裝載整合執行階段才不會與資料來源爭奪資源。
- 您可以在不同電腦上有多個自我裝載整合執行階段，但它們皆連接至相同的內部部署資料來源。 例如，您可能有兩個用於為兩個資料處理站提供服務的自我裝載整合執行階段，但相同的內部部署資料來源都會向這兩個資料處理站註冊。
- 若您已在電腦上安裝用於 Power BI 案例的閘道，請在另一部電腦上安裝另一個用於 Azure Data Factory 的自我裝載整合執行階段。
- 自我裝載整合執行階段必須用於支援 Azure 虛擬網路內的資料整合。
- 即使您使用 Azure ExpressRoute，也應該將資料來源視為在防火牆後的內部部署資料來源。 請使用自我裝載整合執行階段來建立服務與資料來源之間的連線。
- 即使資料儲存在雲端中的 Azure IaaS 虛擬機器上，您也必須使用自我裝載整合執行階段。
- Windows 伺服器若啟用了符合 FIPS 規範的加密，則在安裝於伺服器上的自我裝載整合執行階段中，工作可能會失敗。 若要解決此問題，請在伺服器上停用符合 FIPS 規範的加密。 若要停用符合 FIPS 規範的加密，請將下列登錄值從 1 (已啟用) 變更為 0 (已停用)：`HKLM\System\CurrentControlSet\Control\Lsa\FIPSAlgorithmPolicy\Enabled`。

## <a name="prerequisites"></a>必要條件

- 支援的作業系統版本包括 Windows 7 Service Pack 1、Windows 8.1、Windows 10、Windows Server 2008 R2 SP1、Windows Server 2012、Windows Server 2012 R2、Windows Server 2016。 不支援在網域控制站上安裝自我裝載整合執行階段。
- 必須要有 .NET Framework 4.6.1 或更新版本。 如果您要在 Windows 7 電腦上安裝自我裝載整合執行階段，必須安裝 .NET Framework 4.6.1 或更新版本。 如需詳細資訊，請參閱 [.NET Framework 系統需求](/dotnet/framework/get-started/system-requirements) 。
- 建議的自我裝載整合執行階段電腦組態為至少 2 GHz、4 核心、8 GB RAM 和 80 GB 磁碟。
- 如果主機電腦休眠，自我裝載整合執行階段不會回應資料要求。 因此，安裝自我裝載整合執行階段之前，請先在電腦上設定適當的電源計劃。 如果電腦已設定為休眠，安裝自我裝載整合執行階段時會提示訊息。
- 您必須是電腦上的系統管理員，才能成功安裝和設定自我裝載整合執行階段。
- 複製活動流程會在特定頻率下出現。 電腦上的資源使用方式 (CPU、記憶體) 會與尖峰和閒置時間的模式相同。 資源使用率也仰賴要移動的資料量。 如果有多個複製作業正在進行，您會看到資源使用量在尖峰時段增加。

## <a name="installation-best-practices"></a>安裝最佳做法
您可以從 [Microsoft 下載中心](https://www.microsoft.com/download/details.aspx?id=39717)下載 MSI 安裝套件來安裝自我裝載整合執行階段。 如需逐步指示，請參閱[在內部部署與雲端之間移動資料一文](tutorial-hybrid-copy-powershell.md)。

- 為自我裝載整合執行階段設定主機電腦上的電源計劃，使電腦不休眠。 如果主機電腦休眠，自我裝載整合執行階段就會離線。
- 請定期備份與自我裝載整合執行階段相關聯的認證。

## <a name="install-and-register-self-hosted-ir-from-the-download-center"></a>從下載中心安裝和註冊自我裝載 IR

1. 瀏覽至 [Microsoft 整合執行階段下載頁面](https://www.microsoft.com/download/details.aspx?id=39717)。
2. 選取 [下載]  ，選取 64 位元版本 (不支援 32 位元)，然後選取[下一步]  。
3. 直接執行 MSI 檔案或將它儲存至您的硬碟並執行。
4. 在 [歡迎]  頁面上選取一個語言，然後按 [下一步]  。
5. 接受「Microsoft 軟體授權條款」，然後選取 [下一步]  。
6. 選取 [資料夾]  來安裝自我裝載整合執行階段，接著按一下 [下一步]  。
7. 在 [準備安裝]  頁面上，選取 [安裝]  。
8. 按一下 [完成]  來完成安裝。
9. 使用 Azure PowerShell 取得驗證金鑰。 擷取驗證金鑰的 PowerShell 範例：

    ```powershell
    Get-AzDataFactoryV2IntegrationRuntimeKey -ResourceGroupName $resourceGroupName -DataFactoryName $dataFactoryName -Name $selfHostedIntegrationRuntime
    ```
11. 在您電腦上執行的 Microsoft Integration Runtime 管理員 [Microsoft Integration Runtime (自我裝載)]  頁面上，執行下列步驟：

    a. 將驗證金鑰貼到文字區域。

    b. (選擇性) 選取 [顯示驗證金鑰]  以查看金鑰文字。

    c. 選取 [註冊]  。


## <a name="high-availability-and-scalability"></a>高可用性和延展性
自我裝載的整合執行階段可以與多個內部部署機器或在 Azure 中的虛擬機器相關聯。 這些電腦稱為節點。 一個自我裝載整合執行階段最多可與四個節點建立關聯。 讓邏輯閘道擁有多個節點 (安裝了閘道的內部部署機器) 的優點如下：
* 自我裝載整合執行階段最高可與四個節點建立關聯，提供高可用性並確保持續性，如此一來它就不再是您的巨量資料解決方案或 Azure Data Factory 雲端資料整合的單一失敗點。
* 提升在內部部署和雲端資料存放區之間移動資料時的效能和輸送量。 如需詳細資訊，請參閱[效能比較](copy-activity-performance.md)。

您可以從[下載中心](https://www.microsoft.com/download/details.aspx?id=39717)下載自我裝載整合執行階段軟體，以針對多個節點建立關聯。 接著，註冊它使用其中一種驗證金鑰取自**新增 AzDataFactoryV2IntegrationRuntimeKey** cmdlet，如中所述[教學課程](tutorial-hybrid-copy-powershell.md)。

> [!NOTE]
> 您不需要建立新的自我裝載整合執行階段來關聯每個節點。 您可以將自我裝載整合執行階段安裝在另一部電腦上，並使用相同的驗證金鑰進行註冊。 

> [!NOTE]
> 在新增其他節點來實現高可用性和延展性之前，請確認第一個節點上的 [遠端存取內部網路]  選項已啟用 ([Microsoft Integration Runtime 組態管理員]   >  **[設定]**  >  **[遠端存取內部網路]** )。 

### <a name="scale-considerations"></a>調整考量

#### <a name="scale-out"></a>相應放大

當自我裝載 IR 上可用的記憶體偏低，而 CPU 使用率偏高時，新增節點有助於相應放大電腦所能承受的負載。 如果活動因為逾時或自我裝載 IR 節點離線而失敗，對閘道新增節點將有所助益。

#### <a name="scale-up"></a>相應增加

當可用的記憶體和 CPU 並未充分使用時，但目前的並行作業數目即將達到限制時，您應該增加節點上可執行的並行作業數目來進行相應增加。 當自我裝載 IR 超載而導致活動逾時，您也可以相應增加。 您可以增加一個節點的容量上限，如下圖所示：  

![增加可在節點上執行的並行作業](media/create-self-hosted-integration-runtime/scale-up-self-hosted-IR.png)

### <a name="tlsssl-certificate-requirements"></a>TLS/SSL 憑證需求

以下是用來保護整合執行階段節點間通訊之 TLS/SSL 憑證的需求：

- 憑證必須是受信任的 X509 v3 公開憑證。 建議您使用公開 (夥伴) 憑證授權單位 (CA) 所發出的憑證。
- 每個整合執行階段節點都必須信任此憑證。
- 由於系統僅會使用主體別名 (SAN) 的最後一個項目，其他所有項目則會因目前的限制而遭到忽略，因此不建議使用 SAN 憑證。 舉例來說，若您具有 SAN 憑證，且其 SAN 為 **node1.domain.contoso.com** 和 **node2.domain.contoso.com**，則您僅可在 FQDN 為 **node2.domain.contoso.com** 的電腦上使用此憑證。
- 此憑證支援 Windows Server 2012 R2 所支援的任何 SSL 憑證金鑰大小。
- 不支援使用 CNG 金鑰的憑證。  

> [!NOTE]
> 此憑證用來加密自我裝載 IR 在節點上，使用的連接埠**節點對節點通訊**（適用於狀態同步處理，其中包含連結的服務認證同步處理，跨節點） 和 while **使用 PowerShell cmdlet，針對已連結服務認證設定**從區域網路內。 如果您的私人網路環境不安全，或者您也想要保護您私人網路內節點之間的通訊，我們建議使用此憑證。 不論是否設定此憑證，從自我裝載 IR 移到其他資料存放區的傳輸中資料移動一律會使用已加密的通道進行。 

## <a name="sharing-the-self-hosted-integration-runtime-with-multiple-data-factories"></a>與多個資料處理站共用自我裝載整合執行階段

您可以重複使用您已經在資料處理站中設定的現有自我裝載整合執行階段基礎結構。 這可讓您藉由參考現有的自我裝載 IR (共用)，在不同的資料處理站中建立*連結的自我裝載整合執行階段*。

若要使用 PowerShell 共用自我裝載整合執行階段，請參閱[使用 PowerShell 在 Azure Data Factory 中建立共用的自我裝載整合執行階段](create-shared-self-hosted-integration-runtime-powershell.md)。

如需此功能的簡介與示範，請觀看下列 12 分鐘長的影片：

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Hybrid-data-movement-across-multiple-Azure-Data-Factories/player]

### <a name="terminology"></a>術語

- **共用 IR**：在實體基礎結構上執行的原始自我裝載 IR。  
- **連結的 IR**：參考另一個共用 IR 的 IR。 這是邏輯整合執行階段並使用另一個自我裝載整合執行階段 (共用) 的基礎結構。

### <a name="high-level-steps-for-creating-a-linked-self-hosted-ir"></a>用於建立連結自我裝載 IR 的概略步驟

1. 在要共用的自我裝載 IR 中，將權限授與您要建立連結 IR 的資料處理站。 

   ![在 [共用] 索引標籤上的權限授與按鈕](media/create-self-hosted-integration-runtime/grant-permissions-IR-sharing.png)

   ![指派權限的選取項目](media/create-self-hosted-integration-runtime/3_rbac_permissions.png)

2. 請記下要共用的自我裝載 IR 的資源識別碼。

   ![資源識別碼的位置](media/create-self-hosted-integration-runtime/4_ResourceID_self-hostedIR.png)

3. 在已授與權限的資料處理站中，建立新的自我裝載 IR (已連結) 並輸入資源識別碼。

   ![建立已連結自我裝載整合執行階段的按鈕](media/create-self-hosted-integration-runtime/6_create-linkedIR_2.png)

   ![名稱和資源識別碼方塊](media/create-self-hosted-integration-runtime/6_create-linkedIR_3.png)

### <a name="monitoring"></a>監視 

- **共用 IR**

  ![尋找共用整合執行階段的選取項目](media/create-self-hosted-integration-runtime/Contoso-shared-IR.png)

  ![監視索引標籤](media/create-self-hosted-integration-runtime/contoso-shared-ir-monitoring.png)

- **連結 IR**

  ![尋找連結整合執行階段的選取項目](media/create-self-hosted-integration-runtime/Contoso-linked-ir.png)

  ![監視索引標籤](media/create-self-hosted-integration-runtime/Contoso-linked-ir-monitoring.png)

### <a name="known-limitations-of-self-hosted-ir-sharing"></a>自我裝載整合執行階段共用的已知限制

* 將要建立已連結 IR 的資料處理站必須具備 [MSI](https://docs.microsoft.com/azure/active-directory/managed-service-identity/overview)。 根據預設，在 Azure 入口網站或 PowerShell Cmdlet 中建立的資料處理站會隱含建立 MSI。 不過，使用 Azure Resorce Manager 範本或 SDK 建立資料處理站的情況下，必須明確設定 [身分識別]  屬性，以確保 Azure Resorce Manager 會建立包含 MSI 的資料處理站。 

* Azure Data Factory .NET SDK 1.1.0 版或更新版本支援這項功能。

* 為了授與權限，使用者需要在已共用 IR 所在的資料處理站中具備「擁有者」角色或繼承「擁有者」角色。

* 共用功能只適用於相同 Azure Active Directory 租用戶內的 Data Factory。

* Active Directory 的[來賓使用者](https://docs.microsoft.com/azure/active-directory/governance/manage-guest-access-with-access-reviews)[無法使用](https://msdn.microsoft.com/library/azure/ad/graph/howto/azure-ad-graph-api-permission-scopes#SearchLimits) UI 中的搜尋功能 (使用搜尋關鍵字列出所有資料處理站)。 但只要來賓使用者是資料處理站的「擁有者」，則可以在共用 IR 而無需搜尋功能，方法是在 [指派權限]  文字方塊中直接輸入 IR 須共用的資料處理站的 MSI，然後在 Azure Data Factory UI 中選取 [新增]  。 

  > [!NOTE]
  > 這項功能僅適用於 Azure Data Factory 第 2 版。 

## <a name="notification-area-icons-and-notifications"></a>通知區域圖示和通知

如果您將游標放在通知區域的圖示/訊息上，您會看到自我裝載整合執行階段狀態的詳細資料。

![通知區域中的通知](media/create-self-hosted-integration-runtime/system-tray-notifications.png)

## <a name="ports-and-firewall"></a>連接埠和防火牆
有兩個您需要考量的防火牆：在組織的中央路由器上執行的*公司防火牆*，以及在已安裝自我裝載整合執行階段的本機電腦上設定為精靈的 *Windows 防火牆*。

![防火牆](media/create-self-hosted-integration-runtime/firewall.png)

在*公司防火牆*層級，您需要設定下列網域和輸出連接埠：

網域名稱 | 連接埠 | 描述
------------ | ----- | ------------
*.servicebus.windows.net | 443 | 用於與後端資料移動服務進行通訊
*.core.windows.net | 443 | 用於透過 Azure Blob 儲存體進行的分段複製 (若已設定)
*.frontend.clouddatahub.net | 443 | 用於與後端資料移動服務進行通訊
download.microsoft.com | 443 | 用於下載更新

*Windows 防火牆*層級 (電腦層級) 通常會啟用這些輸出連接埠。 如果沒有，您可以在自我裝載整合執行階段的電腦上相應地設定網域和連接埠。

> [!NOTE]
> 視您的來源和接收器而定，您可能需要將額外的網域和輸出連接埠加到您公司或 Windows 防火牆的白名單中。
>
> 對於某些雲端資料庫 (例如：Azure SQL Database 和 Azure Data Lake)，您可能需要將自我裝載整合執行階段電腦的 IP 位址加到其防火牆設定的允許清單中。

### <a name="copy-data-from-a-source-to-a-sink"></a>將資料從來源複製到接收器
請確定在公司防火牆、自我裝載整合執行階段電腦上的 Windows 防火牆，及資料存放區本身都已正確啟用防火牆規則。 啟用這些規則可讓自我裝載整合執行階段成功連線到來源和接收器。 請為複製作業所涉及的每個資料存放區啟用規則。

例如，若要從內部部署資料存放區複製到 Azure SQL Database 接收器或 Azure SQL 資料倉儲接收器，請執行下列步驟︰

1. 在 Windows 防火牆和公司防火牆的通訊埠 1433 上都允許進行輸出 TCP 通訊。
2. 設定 Azure SQL 資料庫的防火牆設定，將自我裝載整合執行階段電腦的 IP 位址新增到允許的 IP 位址清單。

> [!NOTE]
> 如果您的防火牆不允許使用輸出連接埠 1433，自我裝載整合執行階段將無法直接存取 Azure SQL 資料庫。 在此情況下，您可以使用[分段複製](copy-activity-performance.md)到 Azure SQL Database 和 Azure SQL 資料倉儲。 在此案例中，您只需要 HTTPS (連接埠 443) 即可進行資料移動。


## <a name="proxy-server-considerations"></a>Proxy 伺服器考量
如果您的公司網路環境使用 Proxy 伺服器來存取網際網路，請將自我裝載整合執行階段設定為使用適當的 Proxy 設定。 您可以在初始註冊階段期間設定 Proxy。

![指定 Proxy](media/create-self-hosted-integration-runtime/specify-proxy.png)

設定，自我裝載的整合執行階段會使用 proxy 伺服器來連線到雲端服務、 來源 / 目的地 (那些使用 HTTP / HTTPS 通訊協定)。 這是選取**變更連結**初始設定期間。 您會看到 [Proxy 設定] 對話方塊。

![設定 Proxy](media/create-self-hosted-integration-runtime/set-http-proxy.png)

有三個組態選項：

- **不使用 Proxy**︰自我裝載整合執行階段不會明確地使用任何 Proxy 來連線到雲端服務。
- **使用系統 Proxy**：自我裝載整合執行階段會使用 diahost.exe.config 和 diawp.exe.config 中設定的 Proxy 設定。如果 diahost.exe.config 和 diawp.exe.config 中未設定任何 Proxy，自我裝載整合執行階段就會直接連線到雲端服務而不經由 Proxy。
- **使用自訂 Proxy**：設定要用於自我裝載整合執行階段的 HTTP Proxy 設定，而不使用 diahost.exe.config 和 diawp.exe.config 中的設定。必須指定 [IP 位址]  和 [連接埠]  。 [使用者名稱]  和 [密碼]  則為選擇性使用，需視您的 Proxy 驗證設定而定。 自我裝載整合執行階段上的所有設定都會使用 Windows DPAPI 加密，並儲存在本機電腦上。

在您儲存已更新的 Proxy 設定之後，自我裝載整合執行階段主機服務會自動重新啟動。

在成功註冊自我裝載整合執行階段之後，如果您想要檢視或更新 Proxy 設定，請使用「整合執行階段組態管理員」。

1. 開啟 [Microsoft Integration Runtime 管理員]  。
2. 切換到 [設定]  索引標籤。
3. 選取 [HTTP Proxy]  區段中的 [變更]  連結，以開啟 [設定 HTTP Proxy]  對話方塊。
4. 選取 [下一步]  。 您會看到一個警告對話方塊，此對話方塊會向您請求權限來儲存 Proxy 設定及重新啟動「整合執行階段主機服務」。

您可以使用「組態管理員」工具來更新 HTTP Proxy。

![檢視 Proxy](media/create-self-hosted-integration-runtime/view-proxy.png)

> [!NOTE]
> 如果您為 Proxy 伺服器設定了 NTLM 驗證，「整合執行階段主機服務」就會以網域帳戶執行。 如果您稍後變更網域帳戶的密碼，請記得更新服務的組態設定並相應地將它重新啟動。 基於這項需求，建議您使用不需要經常更新密碼的專用網域帳戶來存取 Proxy 伺服器。

### <a name="configure-proxy-server-settings"></a>設定 Proxy 伺服器設定

如果您為 HTTP Proxy 選取 [使用系統 Proxy]  設定，自我裝載整合執行階段就會使用 diahost.exe.config 和 diawp.exe.config 中的 Proxy 設定。如果 diahost.exe.config 和 diawp.exe.config 中未指定任何 Proxy，自我裝載整合執行階段就會直接連線到雲端服務而不經由 Proxy。 下列程序說明如何更新 diahost.exe.config 檔案：

1. 在「檔案總管」中，建立一份 C:\Program Files\Microsoft Integration Runtime\3.0\Shared\diahost.exe.config 的安全複本來備份原始檔案。
2. 以系統管理員身分啟動 Notepad.exe，並開啟文字檔 C:\Program Files\Microsoft Integration Runtime\3.0\Shared\diahost.exe.config。您會在以下程式碼中找到 system.net 的預設標籤：

    ```xml
    <system.net>
        <defaultProxy useDefaultCredentials="true" />
    </system.net>
    ```
    接著，您可以新增 Proxy 伺服器詳細資料，如以下範例所示：

    ```xml
    <system.net>
        <defaultProxy enabled="true">
              <proxy bypassonlocal="true" proxyaddress="http://proxy.domain.org:8888/" />
        </defaultProxy>
    </system.net>
    ```

    在 Proxy 標籤內可以有其他屬性，用以指定必要的設定，例如 `scriptLocation`。 請參閱 [Proxy 項目 (網路設定)](https://msdn.microsoft.com/library/sa91de1e.aspx) 以了解語法。

    ```xml
    <proxy autoDetect="true|false|unspecified" bypassonlocal="true|false|unspecified" proxyaddress="uriString" scriptLocation="uriString" usesystemdefault="true|false|unspecified "/>
    ```
3. 將組態檔儲存在原始位置， 然後重新啟動「自我裝載整合執行階段主機服務」以套用變更。 

   若要重新啟動服務，請使用控制台中的服務小程式。 或從「整合執行階段組態管理員」中，選取 [停止服務]  按鈕，然後再選取 [啟動服務]  。 
   
   如果服務未啟動，可能因為在已編輯的應用程式組態檔中加入了不正確的 XML 標記語法。

> [!IMPORTANT]
> 別忘了同時更新 diahost.exe.config 和 diawp.exe.config。

您也必須確定 Microsoft Azure 包含在公司的白名單中。 如需有效的 Microsoft Azure IP 位址清單，可從 [Microsoft 下載中心](https://www.microsoft.com/download/details.aspx?id=41653)下載。

### <a name="possible-symptoms-for-firewall-and-proxy-server-related-issues"></a>防火牆和 Proxy 伺服器相關問題的可能徵兆
如果發生如下錯誤，有可能是因為防火牆或 Proxy 伺服器的設定不正確，使得自我裝載整合執行階段無法連線到 Data Factory 來進行自我驗證。 請參閱上一節，以確保您的防火牆和 Proxy 伺服器的設定皆正確。

* 當您嘗試註冊自我裝載整合執行階段時，您會收到下列錯誤：「無法註冊此整合執行階段節點！ 請確認驗證金鑰有效，且「整合執行階段主機服務」正在這部電腦上執行。
* 當您開啟「Integration Runtime 組態管理員」時，您會看到「已中斷連線」  或「正在連線」  狀態。 檢視 Windows 事件記錄時，在 [事件檢視器]   >  **[應用程式和服務記錄]**  >  **[Microsoft Integration Runtime]** 下，您會看到如下錯誤訊息：

    ```
    Unable to connect to the remote server
    A component of Integration Runtime has become unresponsive and restarts automatically. Component name: Integration Runtime (Self-hosted).
    ```

### <a name="enabling-remote-access-from-an-intranet"></a>啟用來自內部網路的遠端存取  
如果您使用 PowerShell 來加密認證 （在網路中） 以外的自我裝載的整合執行階段安裝所在的另一部電腦時，您可以啟用**來自內部網路的遠端存取**選項。 如果您執行 PowerShell，以加密認證，在相同電腦上的安裝自我裝載的整合執行階段，您無法啟用**來自內部網路的遠端存取**。

在新增其他節點來實現高可用性和延展性之前，您應啟用 [來自內部網路的遠端存取]  。  

在自我裝載整合執行階段安裝期間 (3.3.xxxx.x 版之後)，根據預設，自我裝載整合執行階段安裝作業會停用自我裝載整合執行階段電腦上的 [來自內部網路的遠端存取]  。

如果您使用協力廠商的防火牆，可以手動開啟連接埠 8060 (或使用者設定的連接埠)。 如果您在設定自我裝載整合執行階段時遇到防火牆問題，您可以嘗試使用下列命令來安裝自我裝載整合執行階段，而不設定防火牆：

```
msiexec /q /i IntegrationRuntime.msi NOFIREWALL=1
``` 

如果您選擇不開啟自我裝載整合執行階段電腦上的連接埠 8060，則請使用設定認證應用程式以外的機制來設定資料存放區認證。 例如，您可以使用**新增 AzDataFactoryV2LinkedServiceEncryptCredential** PowerShell cmdlet。


## <a name="next-steps"></a>後續步驟
如需逐步指示，請參閱下列教學課程：[教學課程：將內部部署資料複製到雲端](tutorial-hybrid-copy-powershell.md)。
