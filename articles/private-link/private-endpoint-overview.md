---
title: 什麼是 Azure 私人端點？
description: 瞭解 Azure 私用端點
services: private-link
author: KumudD
ms.service: private-link
ms.topic: conceptual
ms.date: 09/16/2019
ms.author: kumud
ms.openlocfilehash: 7394a9e1b1fa90741bc26fc0e9b6b7e6284d75e0
ms.sourcegitcommit: 1c9858eef5557a864a769c0a386d3c36ffc93ce4
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/18/2019
ms.locfileid: "71104688"
---
# <a name="what-is-azure-private-endpoint"></a>什麼是 Azure 私用端點？

Azure 私用端點是一種網路介面，可讓您私下且安全地連線到 Azure 私用連結所支援的服務。 私人端點會使用您 VNet 中的私人 IP 位址，有效地將服務帶入您的 VNet。 服務可以是 Azure 服務，例如 Azure 儲存體、SQL 等，或您自己的[私人連結服務](private-link-service-overview.md)。
  
## <a name="private-endpoint-properties"></a>私用端點屬性 
 私用端點會指定下列屬性： 


|屬性  |Description |
|---------|---------|
|Name    |    資源群組內的唯一名稱。      |
|Subnet    |  要從虛擬網路部署和配置私人 IP 位址的子網。 如需子網需求，請參閱本文的限制一節。         |
|私人連結資源    |   要使用資源識別碼或別名來連接的私用連結資源，從可用類型的清單中。 系統將會為傳送至此資源的所有流量產生唯一的網路識別碼。       |
|目標 subresource   |      要連接的 subresource。 每個私人連結資源類型都有不同的選項，可根據喜好設定來選取。    |
|連接核准方法    |  自動或手動。 根據角色型存取控制（RBAC）許可權，您的私用端點可以自動核准。 如果您嘗試連接到不具 RBAC 的私人連結資源，請使用手動方法，讓資源的擁有者核准連接。        |
|要求訊息     |  您可以指定要以手動方式核准要求連接的訊息。 此訊息可用於識別特定的要求。        |
|連線狀態   |   唯讀屬性，指定私用端點是否作用中。 只有處於核准狀態的私人端點可以用來傳送流量。 其他可用的狀態： <br>-**已核准**：連線已自動或手動核准，並已準備好可供使用。</br><br>-**暫**止：已手動建立連線，且正在等待私用連結資源擁有者核准。</br><br>-已**拒絕**：私人連結資源擁有者拒絕連線。</br><br>-已**中斷**連線：私人連結資源擁有者已移除連接。 私用端點會變成資訊，而且應該刪除以進行清除。 </br>|

以下是私人端點的一些重要詳細資料： 
- 私人端點可讓相同 VNet、區域內對等互連 Vnet、全域對等互連 Vnet 和內部部署之間的取用者，使用[VPN](https://azure.microsoft.com/services/vpn-gateway/)或[Express Route](https://azure.microsoft.com/services/expressroute/) ，以及由私用連結提供技術支援的服務之間進行連線。
 
- 建立私用端點時，也會針對資源的生命週期建立網路介面。 介面會從對應至私人連結服務的子網指派私人 IP 位址。
 
- 私人端點必須部署在與虛擬網路相同的區域中。 
 
- 私人連結資源可以部署在與虛擬網路和私人端點不同的區域中。
 
- 您可以使用相同的私人連結資源來建立多個私人端點。 針對使用一般 DNS 伺服器設定的單一網路，建議的作法是針對指定的私人連結資源使用單一私人端點，以避免 DNS 解析中出現重複的專案或衝突。 
 
- 可以在相同的虛擬網路內，于相同或不同的子網上建立多個私人端點。 您可以在訂用帳戶中建立的私人端點數目有所限制。 如需詳細資訊，請參閱 [Azure 限制](https://docs.microsoft.com/azure/azure-subscription-service-limits.md#networking-limits)。


 
## <a name="private-link-resource"></a>私人連結資源 
私用連結資源是指定私用端點的目的地目標。 以下是可用的私人連結資源類型清單： 
 
|私人連結資源名稱  |資源類型   |子資源  |
|---------|---------|---------|
|**私人連結服務**（您自己的服務）   |  Microsoft 網路/privateLinkServices       | 空的 |
|**Azure SQL Database** | Microsoft.Sql/servers    |  Sql Server （sqlServer）        |
|**Azure SQL 資料倉儲** | Microsoft.Sql/servers    |  Sql Server （sqlServer）        |
|**Azure 儲存體**  | Microsoft.Storage/storageAccounts    |  Blob （blob、blob_secondary）<BR> Table （table，table_secondary）<BR> Queue （queue，queue_secondary）<BR> File （file，file_secondary）<BR> Web （web、web_secondary）        |
|**Azure Data Lake Storage Gen2**  | Microsoft.Storage/storageAccounts    |  Blob （blob、blob_secondary）       |
 
 
## <a name="network-security-of-private-endpoints"></a>私人端點的網路安全性 
使用 Azure 服務的私人端點時，流量會受到特定私人連結資源的保護。 平臺會執行存取控制，以驗證僅到達指定私人連結資源的網路連接。 若要存取相同 Azure 服務中的其他資源，則需要額外的私用端點。 
 
您可以完全鎖定您的工作負載，使其無法存取公用端點，以連線到支援的 Azure 服務。 此控制項提供內建的外泄保護，以防止存取相同 Azure 服務上裝載的其他資源，為您的資源提供額外的網路安全性層級。 
 
## <a name="access-to-a-private-link-resource-using-approval-workflow"></a>使用核准工作流程存取私人連結資源 
您可以使用下列連接核准方法來連線到私人連結資源：
- 當您擁有或具有特定私人連結資源的許可權時，**會自動**核准。 所需的許可權是以下列格式的私用連結資源類型為基礎：Microsoft.\<提供者 >/< resource_type >/privateEndpointConnectionApproval/action
- 當您沒有必要的許可權，而且想要要求存取權時，**手動**要求。 將會起始核准工作流程。 私用端點和後續的私用端點連線將會以「擱置」狀態建立。 私用連結資源擁有者負責核准連接。 核准後，私用端點會啟用以正常傳送流量，如下列核准工作流程圖表所示。  

![工作流程核准](media/private-endpoint-overview/private-link-paas-workflow.png)
 
私人連結資源擁有者可以透過私人端點連線執行下列動作： 
- 查看所有私人端點連線詳細資料。 
- 核准私用端點連接。 將會啟用對應的私人端點，將流量傳送至私人連結資源。 
- 拒絕私人端點連接。 對應的私用端點會更新以反映狀態。
- 刪除處於任何狀態的私用端點連接。 對應的私人端點將會更新為 [中斷連線] 狀態，以反映動作，私人端點擁有者只能在此時刪除資源。 
 
> [!NOTE]
> 只有處於已核准狀態的私用端點可以將流量傳送到指定的私人連結資源。 

### <a name="connecting-using-alias"></a>使用別名連接
別名是當服務擁有者在標準負載平衡器後方建立私人連結服務時，所產生的唯一標記。 服務擁有者可以離線共用此別名。 取用者可以使用資源 URI 或別名來要求私人連結服務的連接。 如果您想要使用別名進行連線，您必須使用手動連接核准方法來建立私人端點。 若要使用手動連接核准方法，請在私人端點建立流程期間，將手動要求參數設定為 true。 如需詳細資訊，請參閱[AzPrivateEndpoint](https://docs.microsoft.com/en-us/powershell/module/az.network/new-azprivateendpoint?view=azps-2.6.0)和[az network 私用端點建立](https://docs.microsoft.com/en-us/cli/azure/network/private-endpoint?view=azure-cli-latest#az-network-private-endpoint-create)。 

## <a name="dns-configuration"></a>DNS 組態 
使用完整功能變數名稱（FQDN）連接到私人連結資源做為連接字串的一部分時，請務必正確地設定您的 DNS 設定，以解析為配置的私人 IP 位址。 現有的 Azure 服務在透過公用端點連線時，可能已經有 DNS 設定可供使用。 這需要覆寫，才能使用您的私用端點進行連接。 
 
與私人端點相關聯的網路介面包含設定 DNS 所需的一組完整資訊，包括為指定的私人連結資源配置的 FQDN 和私人 IP 位址。 
 
您可以使用下列選項來設定私人端點的 DNS 設定： 
- **使用主機檔案（僅建議用於測試）** 。 您可以使用虛擬機器上的主機檔案來覆寫 DNS。  
- **使用私人 DNS 區域**。 您可以使用私人 DNS 區域來覆寫指定私用端點的 DNS 解析。 私人 DNS 區域可以連結至您的虛擬網路，以解析特定網域。
- **使用您的自訂 DNS 伺服器**。 您可以使用自己的 DNS 伺服器來覆寫指定私人連結資源的 DNS 解析。 如果您的 DNS 伺服器裝載于虛擬網路上，您可以建立 DNS 轉送規則來使用私人 DNS 區域，以簡化所有私人連結資源的設定。
 
> [!IMPORTANT]
> 不建議您覆寫主動使用的區域來解析公用端點。 若未將 DNS 轉送至公用 DNS，則無法正確解析資源的連線。 為避免發生問題，請建立不同的功能變數名稱，或遵循下列每項服務的建議名稱。 
 
針對 Azure 服務，請使用下表中所述的建議區功能變數名稱稱：

|私人連結資源類型   |Subresource  |區功能變數名稱稱  |
|---------|---------|---------|
|SQL DB/DW （Microsoft .Sql/servers）    |  Sql Server （sqlServer）        |   privatelink.database.windows.net       |
|儲存體帳戶（Microsoft 儲存體/storageAccounts）    |  Blob （blob、blob_secondary）        |    privatelink.blob.core.windows.net      |
|儲存體帳戶（Microsoft 儲存體/storageAccounts）    |    Table （table，table_secondary）      |   privatelink.table.core.windows.net       |
|儲存體帳戶（Microsoft 儲存體/storageAccounts）    |    Queue （queue，queue_secondary）     |   privatelink.queue.core.windows.net       |
|儲存體帳戶（Microsoft 儲存體/storageAccounts）   |    File （file，file_secondary）      |    privatelink.file.core.windows.net      |
|儲存體帳戶（Microsoft 儲存體/storageAccounts）     |  Web （web、web_secondary）        |    privatelink.web.core.windows.net      |
|Data Lake 檔案系統 Gen2 （Microsoft. Storage/storageAccounts）  |  Data Lake 檔案系統 Gen2 （dfs、dfs_secondary）        |     privatelink.dfs.core.windows.net     |
||||
 

Azure 會在公用 DNS 上建立正式名稱 DNS 記錄（CNAME），以將解析重新導向至建議的功能變數名稱。 您將能夠使用私人端點的私人 IP 位址來覆寫解析。 
 
您的應用程式不需要變更連接 URL。 嘗試使用公用 DNS 進行解析時，DNS 伺服器現在會解析為您的私用端點。 此程式不會影響您的應用程式。 
 
## <a name="limitations"></a>限制
 
下表包含使用私用端點時的已知限制清單： 


|限制 |描述 |風險降低  |
|---------|---------|---------|
|網路安全性群組（NSG）規則不適用於私人端點    |私用端點上不支援 NSG。 雖然包含私用端點的子網可以有相關聯的 NSG，但這些規則不會對私用端點所處理的流量生效。 您必須[停用網路原則強制執行](disable-private-endpoint-network-policy.md)，才能在子網中部署私人端點。 在相同子網上裝載的其他工作負載上仍會強制執行 NSG。   | 針對來源用戶端上的輸出流量使用 NSG 規則，以控制流量。        |
|無法在已啟用服務端點或特定工作負載的子網中建立私人端點    |私人端點無法部署在啟用服務端點或子網的子網，以供委派至特定工作負載|  建立個別的子網以部署私人端點。        |
|私人端點只能對應至相同區域中的私用連結服務（客戶擁有）    |   不支援從不同的區域連接到私人連結服務（您自己的）       |  在預覽期間，您必須在相同的區域中部署私用連結服務。        |
|特製化工作負載無法存取私人端點    |   下列部署到虛擬網路的服務無法使用私人端點來存取任何私人連結資源：<br>App Service 方案</br>Azure 容器執行個體</br>Azure NetApp Files</br>Azure 專用 HSM<br>       |   預覽期間不會緩和風險。       |
|  入口網站不支援使用別名建立私人端點  |   入口網站只允許使用資源 URI 建立私人端點      | 使用資源 URI 來要求私人端點連接        |

## <a name="next-steps"></a>後續步驟
- [使用入口網站建立 SQL Database 伺服器的私用端點](create-private-endpoint-portal.md)
- [使用 PowerShell 建立 SQL Database 伺服器的私用端點](create-private-endpoint-powershell.md)
- [使用 CLI 建立 SQL Database 伺服器的私用端點](create-private-endpoint-cli.md)
- [使用入口網站建立儲存體帳戶的私用端點](create-private-endpoint-storage-portal.md)
- [使用 Azure PowerShell 建立您自己的私人連結服務](create-private-link-service-powershell.md)
