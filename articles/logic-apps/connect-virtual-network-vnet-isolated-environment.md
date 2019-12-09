---
title: 使用 ISE 連接到 Azure 虛擬網路
description: 建立可從 Azure Logic Apps 存取 Azure 虛擬網路（Vnet）的整合服務環境（ISE）
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 11/27/2019
ms.openlocfilehash: 27c83bffe40fd80f87542ee4486ef90e684bd5a6
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/08/2019
ms.locfileid: "74931859"
---
# <a name="connect-to-azure-virtual-networks-from-azure-logic-apps-by-using-an-integration-service-environment-ise"></a>透過使用整合服務環境 (ISE) 從 Azure Logic Apps 連線至 Azure 虛擬網路

針對您邏輯應用程式與整合帳戶需存取 [Azure 虛擬網路](../virtual-network/virtual-networks-overview.md)的案例，建立[整合服務的環境 (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)。 ISE 是私用和隔離的環境，使用與公用或「全域」 Logic Apps 服務分開的專用儲存體和其他資源。 這種區隔也可減少任何其他 Azure 租用戶可能對您應用程式效能造成的影響。

當您建立 ISE 時，Azure 會將 ISE 插入*您的 azure*虛擬網路，然後將 Logic Apps 服務部署到您的虛擬網路。 當您建立邏輯應用程式或整合帳戶時，請選取您的 ISE 作為其位置。 然後，您的邏輯應用程式或整合帳戶就可以直接存取虛擬網路中的資源，例如：虛擬機器 (VM)、伺服器、系統及服務。

![選取整合服務環境](./media/connect-virtual-network-vnet-isolated-environment/select-logic-app-integration-service-environment.png)

> [!IMPORTANT]
> 若要讓邏輯應用程式和整合帳戶在 ISE 中一起工作，兩者都必須使用與其位置*相同的 ISE* 。

ISE 已增加執行持續時間、儲存體保留期、輸送量、HTTP 要求和回應超時、訊息大小，以及自訂連接器要求的限制。 如需詳細資訊，請參閱[Azure Logic Apps 的限制和](logic-apps-limits-and-config.md)設定。 若要深入瞭解 Ise，請參閱[Azure Logic Apps 的 Azure 虛擬網路資源存取權](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)。

本文說明如何完成這些工作：

* 啟用 ISE 的存取權。
* 建立您的 ISE。
* 將額外的容量新增至您的 ISE。

> [!IMPORTANT]
> 邏輯應用程式、內建觸發程式、內建動作，以及在您 ISE 中執行的連接器會使用與以耗用量為基礎的定價方案不同的定價方案。 若要瞭解 Ise 的定價和計費方式，請參閱[Logic Apps 定價模式](../logic-apps/logic-apps-pricing.md#fixed-pricing)。 如需定價費率，請參閱[Logic Apps 定價](../logic-apps/logic-apps-pricing.md)。

## <a name="prerequisites"></a>必要條件

* Azure 訂用帳戶。 如果您沒有 Azure 訂用帳戶，請先[註冊一個免費的 Azure 帳戶](https://azure.microsoft.com/free/)。

* [Azure 虛擬網路](../virtual-network/virtual-networks-overview.md)。 如果您沒有虛擬網路，請了解如何[建立 Azure 虛擬網路](../virtual-network/quick-create-portal.md)。

  * 您的虛擬網路必須有四個*空白*子網，才能在 ISE 中建立及部署資源。 您可以事先建立這些子網，也可以等到建立 ISE，讓您可以同時建立子網。 深入瞭解[子網需求](#create-subnet)。

  * 子網名稱的開頭必須是字母字元或底線，而且不能使用下列字元： `<`、`>`、`%`、`&`、`\\`、`?`、`/`。 
  
  * 如果您想要透過 Azure Resource Manager 範本部署 ISE，請先確定您將一個空白子網委派給 Microsoft. 邏輯/integrationServiceEnvironment。 當您透過 Azure 入口網站部署時，不需要執行此委派。

  * 請確定您的虛擬網路[可存取您的 ise](#enable-access) ，讓您的 ise 能夠正常運作並保持可存取。

  * 如果您使用[ExpressRoute](../expressroute/expressroute-introduction.md)（提供 Microsoft 雲端服務的私人連線），您必須建立具有下列路由的[路由表](../virtual-network/manage-route-table.md)，並將該資料表連結到 ISE 所使用的每個子網：

    **名稱**： <*路由名稱*><br>
    **位址首碼**： 0.0.0.0/0<br>
    **下一個躍點**：網際網路

* 如果您想要針對您的 Azure 虛擬網路使用自訂 DNS 伺服器，請在將 ISE 部署至虛擬網路之前，[遵循下列步驟來設定這些伺服器](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md)。 否則，每次變更 DNS 伺服器時，您也必須重新開機 ISE。

  > [!IMPORTANT]
  > 如果您在建立 ISE 之後變更 DNS 伺服器設定，請務必重新開機您的 ISE。 如需管理 DNS 伺服器設定的詳細資訊，請參閱[建立、變更或刪除虛擬網路](../virtual-network/manage-virtual-network.md#change-dns-servers)。

<a name="enable-access"></a>

## <a name="enable-access-for-ise"></a>啟用 ISE 的存取權

當您搭配 Azure 虛擬網路使用 ISE 時，常見的設定問題是有一或多個已封鎖的埠。 您用來在 ISE 與目的地系統之間建立連線的連接器，可能也會有自己的埠需求。 例如，如果您使用 FTP 連接器與 FTP 系統通訊，就必須提供您在 FTP 系統上使用的埠，例如，用於傳送命令的埠21。

若要確定您的 ISE 可供存取，且該 ISE 中的邏輯應用程式可以跨虛擬網路中的子網進行通訊，請[開啟此表格中的埠](#network-ports-for-ise)。 如果有任何必要的埠無法使用，您的 ISE 將無法正確運作。

* 如果您有多個 Ise，且您的虛擬網路使用[Azure 防火牆](../firewall/overview.md)或[網路虛擬裝置](../virtual-network/virtual-networks-overview.md#filter-network-traffic)，您可以[設定單一、輸出、公用和可預測的 IP 位址](connect-virtual-network-vnet-set-up-single-ip-address.md)，以便與目的地系統進行通訊。 如此一來，您就不需要為目的地上的每個 ISE 設定額外的防火牆。

* 如果您建立新的 Azure 虛擬網路和子網，但沒有任何限制，您就不需要在虛擬網路中設定[網路安全性群組（nsg）](../virtual-network/security-overview.md#network-security-groups)來控制子網之間的流量。

* 在現有的虛擬網路上，您可以藉由[篩選跨子網的網路流量](../virtual-network/tutorial-filter-network-traffic.md)，*選擇性地*設定 nsg。 如果您選擇 [此路由]，請在您要設定 Nsg 的虛擬網路上，確定您已[開啟此資料表中的埠](#network-ports-for-ise)。 如果您使用[NSG 安全性規則](../virtual-network/security-overview.md#security-rules)，則需要 TCP 和 UDP 通訊協定。

* 如果您先前已有 Nsg，請確定您已[在此資料表中開啟埠](#network-ports-for-ise)。 如果您使用[NSG 安全性規則](../virtual-network/security-overview.md#security-rules)，則需要 TCP 和 UDP 通訊協定。

<a name="network-ports-for-ise"></a>

### <a name="network-ports-used-by-your-ise"></a>ISE 使用的網路埠

下表描述您的 ISE 使用的 Azure 虛擬網路中的埠，以及這些埠的使用位置。 [Resource Manager 服務](../virtual-network/security-overview.md#service-tags)標籤代表一組 IP 位址首碼，可在建立安全性規則時協助將複雜度降到最低。

> [!IMPORTANT]
> 來源埠是暫時的，因此請確定您已將其設定為所有規則的 `*`。
> 針對子網內的內部通訊，ISE 會要求您在這些子網內開啟所有埠。

| 目的 | 方向 | 目的地連接埠 | 來源服務標籤 | 目的地服務標記 | 注意 |
|---------|-----------|-------------------|--------------------|-------------------------|-------|
| 來自 Azure Logic Apps 的通訊 | 輸出 | 80、443 | VirtualNetwork | Internet | 埠取決於 Logic Apps 服務所通訊的外部服務 |
| Azure Active Directory | 輸出 | 80、443 | VirtualNetwork | AzureActiveDirectory | |
| Azure 儲存體相依性 | 輸出 | 80、443 | VirtualNetwork | 儲存體 | |
| Intersubnet 通訊 | 輸入和輸出 | 80、443 | VirtualNetwork | VirtualNetwork | 用於子網之間的通訊 |
| 對 Azure Logic Apps 的通訊 | 輸入 | 443 | 內部存取端點： <br>VirtualNetwork <p><p>外部存取端點： <br>Internet <p><p>**注意**：這些端點會參考[在 ISE 建立時選取](connect-virtual-network-vnet-isolated-environment.md#create-environment)的端點設定。 如需詳細資訊，請參閱[端點存取](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#endpoint-access)。 | VirtualNetwork | 電腦或服務的 IP 位址，其會呼叫存在於邏輯應用程式中的任何要求觸發程式或 webhook。 關閉或封鎖此埠可防止對邏輯應用程式的 HTTP 呼叫使用要求觸發程式。 |
| 邏輯應用程式執行歷程記錄 | 輸入 | 443 | 內部存取端點： <br>VirtualNetwork <p><p>外部存取端點： <br>Internet <p><p>**注意**：這些端點會參考[在 ISE 建立時選取](../logic-apps/connect-virtual-network-vnet-isolated-environment.md#create-environment)的端點設定。 如需詳細資訊，請參閱[端點存取](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#endpoint-access)。 | VirtualNetwork | 您用來查看邏輯應用程式執行歷程記錄之電腦的 IP 位址。 雖然關閉或封鎖此埠並不會讓您無法查看執行歷程記錄，但您無法在該執行歷程記錄中查看每個步驟的輸入和輸出。 |
| 連線管理 | 輸出 | 443 | VirtualNetwork  | AppService | |
| 發佈診斷記錄和計量 | 輸出 | 443 | VirtualNetwork  | AzureMonitor | |
| 從 Azure 流量管理員的通訊 | 輸入 | 443 | AzureTrafficManager | VirtualNetwork | |
| Logic Apps 設計工具 - 動態屬性 | 輸入 | 454 | 如需允許的 IP 位址，請參閱附注資料行 | VirtualNetwork | 要求來自該區域的 Logic Apps 存取端點[輸入](../logic-apps/logic-apps-limits-and-config.md#inbound)IP 位址。 |
| 網路健全狀況檢查 | 輸入 | 454 | 如需允許的 IP 位址，請參閱附注資料行 | VirtualNetwork | 要求來自該區域的[輸入](../logic-apps/logic-apps-limits-and-config.md#inbound)和[輸出](../logic-apps/logic-apps-limits-and-config.md#outbound)IP 位址的 Logic Apps 存取端點。 |
| App Service 管理相依性 | 輸入 | 454、455 | AppServiceManagement | VirtualNetwork | |
| 連接器部署 | 輸入 | 454 | AzureConnectors | VirtualNetwork | 部署和更新連接器所需。 關閉或封鎖此埠會導致 ISE 部署失敗，並防止連接器更新或修正。 |
| 連接器原則部署 | 輸入 | 3443 | Internet | VirtualNetwork | 部署和更新連接器所需。 關閉或封鎖此埠會導致 ISE 部署失敗，並防止連接器更新或修正。 |
| Azure SQL 相依性 | 輸出 | 1433 | VirtualNetwork | SQL | |
| Azure 資源健康狀態 | 輸出 | 1886 | VirtualNetwork | AzureMonitor | 將健全狀況狀態發佈至資源健康狀態 |
| API 管理 - 管理端點 | 輸入 | 3443 | APIManagement | VirtualNetwork | |
| 「記錄到事件中樞」原則和監視代理程式的相依性 | 輸出 | 5672 | VirtualNetwork | EventHub | |
| 針對角色執行個體之間的 Redis 執行個體存取 Azure 快取 | 輸入 <br>輸出 | 6379-6383 | VirtualNetwork | VirtualNetwork | 此外，若要讓 ISE 使用 Azure Cache for Redis，您必須開啟[Azure cache For REDIS 常見問題中所述的這些輸出和輸入埠](../azure-cache-for-redis/cache-how-to-premium-vnet.md#outbound-port-requirements)。 |
| Azure Load Balancer | 輸入 | * | AzureLoadBalancer | VirtualNetwork | |
||||||

<a name="create-environment"></a>

## <a name="create-your-ise"></a>建立您的 ISE

若要建立您的整合服務環境 (ISE)，請遵循下列步驟：

1. 在 [Azure 入口網站](https://portal.azure.com)中，於 Azure 主功能表上選取 [建立資源]。
在搜尋方塊中，輸入「整合服務環境」作為篩選條件。

   ![建立新資源](./media/connect-virtual-network-vnet-isolated-environment/find-integration-service-environment.png)

1. 在 [整合服務環境] 建立窗格中，選擇 [**建立**]。

   ![選擇 [建立]](./media/connect-virtual-network-vnet-isolated-environment/create-integration-service-environment.png)

1. 為您的環境提供這些詳細資料，然後選擇 [檢閱 + 建立]，例如：

   ![提供環境詳細資料](./media/connect-virtual-network-vnet-isolated-environment/integration-service-environment-details.png)

   | 屬性 | 必要項 | Value | 描述 |
   |----------|----------|-------|-------------|
   | **訂用帳戶** | 是 | <*Azure-subscription-name*> | 要用於環境的 Azure 訂用帳戶 |
   | **資源群組** | 是 | <*Azure-resource-group-name*> | 您要用來建立環境的 Azure 資源群組 |
   | **整合服務環境名稱** | 是 | <*environment-name*> | 您的 ISE 名稱，其中只能包含字母、數位、連字號（`-`）、底線（`_`）和句點（`.`）。 |
   | **位置** | 是 | <*Azure-datacenter-region*> | 要用來部署環境的 Azure 資料中心區域 |
   | **SKU** | 是 | **Premium**或**DEVELOPER （無 SLA）** | 要建立及使用的 ISE SKU。 如需這些 Sku 之間的差異，請參閱[ISE sku](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#ise-level)。 <p><p>**重要**事項：只有在 ISE 建立時才可使用此選項，且稍後無法變更。 |
   | **額外容量** | 優質： <br>是 <p><p>開發人員： <br>不適用 | 優質： <br>0到10 <p><p>開發人員： <br>不適用 | 要用於此 ISE 資源的額外處理單位數。 若要在建立後新增容量，請參閱[新增 ISE 容量](#add-capacity)。 |
   | **存取端點** | 是 | **內部**或**外部** | 用於 ISE 的存取端點類型，可決定 ISE 中的邏輯應用程式上的要求或 webhook 觸發程式是否可以接收來自虛擬網路外部的呼叫。 端點類型也會影響您的邏輯應用程式執行歷程記錄中的輸入和輸出存取。 如需詳細資訊，請參閱[端點存取](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#endpoint-access)。 <p><p>**重要**事項：只有在 ISE 建立時才可使用此選項，且稍後無法變更。 |
   | **虛擬網路** | 是 | <*Azure-virtual-network-name*> | 要插入環境的 Azure 虛擬網路，讓該環境中的邏輯應用程式可以存取虛擬網路。 如果您沒有網路，請[先建立 Azure 虛擬網路](../virtual-network/quick-create-portal.md)。 <p>**重要**：您*只能*在建立 ISE 時執行此插入。 |
   | **子網路** | 是 | <*subnet-resource-list*> | ISE 需要四個*空*的子網，才能在您的環境中建立及部署資源。 若要建立每個子網路，[請遵循此表格底下的步驟](#create-subnet)。 |
   |||||

   <a name="create-subnet"></a>

   **建立子網路**

   若要在您的環境中建立及部署資源，您的 ISE 需要四個未委派給任何服務的*空白*子網。 建立環境之後，您就*無法*變更這些子網位址。

   > [!IMPORTANT]
   > 
   > 子網名稱的開頭必須是字母字元或底線（沒有數位），而且不會使用下列字元： `<`、`>`、`%`、`&`、`\\`、`?`、`/`。

   此外，每個子網都必須符合下列需求：

   * 使用無[類別網域間路由（CIDR）格式](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing)和類別 B 位址空間。

   * 在位址空間中使用至少 `/27`，因為每個子*網至少必須有 32*個*位址。* 例如：

     * `10.0.0.0/27` 有32個位址，因為 2<sup>（32-27）</sup>是 2<sup>5</sup>或32。

     * `10.0.0.0/24` 有256個位址，因為 2<sup>（32-24）</sup>是 2<sup>8</sup>或256。

     * `10.0.0.0/28` 只有16個位址，而且太小，因為 2<sup>（32-28）</sup>是 2<sup>4</sup>或16。

     若要深入瞭解如何計算位址，請參閱[IPV4 CIDR 區塊](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing#IPv4_CIDR_blocks)。

   * 如果您使用[ExpressRoute](../expressroute/expressroute-introduction.md)，您必須建立具有下列路由的[路由表](../virtual-network/manage-route-table.md)，並連結該資料表與您的 ISE 所使用的每個子網：

     **名稱**： <*路由名稱*><br>
     **位址首碼**： 0.0.0.0/0<br>
     **下一個躍點**：網際網路

   1. 在 [子網路] 清單底下，選擇 [管理子網路設定]。

      ![管理子網路設定](./media/connect-virtual-network-vnet-isolated-environment/manage-subnet.png)

   1. 在 [子網路] 窗格中，選擇 [子網路]。

      ![新增子網路](./media/connect-virtual-network-vnet-isolated-environment/add-subnet.png)

   1. 在 [新增子網路] 窗格中，提供這項資訊。

      * **名稱**：子網的名稱
      * **位址範圍（CIDR 區塊）** ：您的虛擬網路中的子網範圍和 CIDR 格式

      ![新增子網路詳細資料](./media/connect-virtual-network-vnet-isolated-environment/subnet-details.png)

   1. 完成時，選擇 [確定]。

   1. 針對其他三個子網路重複這些步驟。

      > [!NOTE]
      > 如果您嘗試建立的子網無效，Azure 入口網站會顯示一則訊息，但不會封鎖您的進度。

   如需有關建立子網的詳細資訊，請參閱[新增虛擬網路子網](../virtual-network/virtual-network-manage-subnet.md)。

1. 在 Azure 成功驗證您的 ISE 資訊之後，選擇 [建立]，例如：

   ![驗證成功之後，選擇 [建立]](./media/connect-virtual-network-vnet-isolated-environment/ise-validation-success.png)

   Azure 會開始部署您的環境，這通常會在兩個小時內完成。 有時候，部署可能需要多達四小時。 若要檢查部署狀態，請在 Azure 工具列上選擇通知圖示，這會開啟 [通知] 窗格。

   ![檢查部署狀態](./media/connect-virtual-network-vnet-isolated-environment/environment-deployment-status.png)

   成功完成部署時，Azure 就會顯示此通知：

   ![部署成功](./media/connect-virtual-network-vnet-isolated-environment/deployment-success.png)

   否則，請遵循針對部署進行疑難排解的 Azure 入口網站指示。

   > [!NOTE]
   > 如果部署失敗或刪除您的 ISE，Azure 可能需要一小時的時間，才能釋出您的子網。 這種延遲表示您可能必須等待，才能在另一個 ISE 中重複使用這些子網。
   >
   > 如果您刪除虛擬網路，Azure 通常需要兩小時的時間，才能釋出您的子網，但此作業可能需要較長的時間。 
   > 刪除虛擬網路時，請確定沒有任何資源仍處於線上狀態。 
   > 請參閱[刪除虛擬網路](../virtual-network/manage-virtual-network.md#delete-a-virtual-network)。

1. 如果 Azure 在部署完成之後不會自動移至您的環境，且若要檢視您的環境，請選擇 [移至資源]。

1. 若要檢查 ISE 的網路健康情況，請參閱[管理您的整合服務環境](../logic-apps/ise-manage-integration-service-environment.md#check-network-health)。

1. 若要開始在 ISE 中建立邏輯應用程式和其他成品，請參閱[將成品新增至整合服務環境](../logic-apps/add-artifacts-integration-service-environment-ise.md)。

<a name="add-capacity"></a>

## <a name="add-ise-capacity"></a>新增 ISE 容量

Premium ISE 基礎單位具有固定容量，因此如果您需要更多輸送量，可以在建立期間或之後新增更多縮放單位。 您可以根據效能計量，或根據一些額外的處理單位來自動調整。 如果您選擇 [根據計量自動調整]，您可以從各種條件中選擇，並指定符合該準則的閾值條件。 開發人員 SKU 不包含新增縮放單位的功能。

1. 在 Azure 入口網站中，尋找您的 ISE。

1. 若要查看 ISE 的使用量和效能計量，請在 ISE 的主功能表上，選取 **[總覽**]。

   ![查看 ISE 的使用方式](./media/connect-virtual-network-vnet-isolated-environment/integration-service-environment-usage.png)

1. 若要設定自動調整，請在 [**設定**] 下選取 [**相應**放大]。在 [**設定**] 索引標籤上，選擇 [**啟用自動**調整]。

   ![開啟自動調整](./media/connect-virtual-network-vnet-isolated-environment/scale-out.png)

1. 針對 [**自動調整設定名稱**]，提供您設定的名稱。

1. 在 [**預設**] 區段中，選擇 [**根據度量調整規模**] 或 [**調整為特定實例計數**]。

   * 如果您選擇 [實例型]，請輸入介於0到10（含）之間的處理單位數。

   * 如果您選擇 [以計量為基礎]，請遵循下列步驟：

     1. 在 [**規則**] 區段中，選擇 [**新增規則**]。

     1. 在 [**調整規則**] 窗格上，設定規則引發時要採取的準則和動作。

     1. 當您完成時，請選擇 [**新增**]。

1. 當您完成自動調整規模設定後，請儲存您的變更。

## <a name="next-steps"></a>後續步驟

* [將構件新增至整合服務環境](../logic-apps/add-artifacts-integration-service-environment-ise.md)
* [檢查整合服務環境的網路健全狀況](../logic-apps/ise-manage-integration-service-environment.md#check-network-health)
* 深入了解 [Azure 虛擬網路](../virtual-network/virtual-networks-overview.md)
* 了解 [Azure 服務的虛擬網路整合](../virtual-network/virtual-network-for-azure-services.md)
