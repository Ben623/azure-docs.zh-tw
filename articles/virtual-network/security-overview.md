---
title: Azure 安全性群組概觀
titlesuffix: Azure Virtual Network
description: 了解網路和應用程式安全性群組。 安全性群組可協助您篩選 Azure 資源之間的網路流量。
services: virtual-network
documentationcenter: na
author: malopMSFT
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/26/2018
ms.author: malop;kumud
ms.openlocfilehash: 99a55d0cd06e6f1a92a70b20447d300dbc05eee1
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/09/2019
ms.locfileid: "67709545"
---
# <a name="security-groups"></a>安全性群組
<a name="network-security-groups"></a>

您可以透過網路安全性群組，篩選在 Azure [虛擬網路](virtual-networks-overview.md)中進出 Azure 資源的網路流量。 網路安全性群組包含[安全性規則](#security-rules)，用來允許或拒絕進出多種 Azure 資源類型的輸入和輸出網路流量。 若要了解虛擬網路中可部署哪些 Azure 資源，並使這些資源與網路安全性群組產生關聯，請參閱 [Azure 服務的虛擬網路整合](virtual-network-for-azure-services.md)。 您可以為每個規則指定來源和目的地、連接埠及通訊協定。

本文將說明網路安全性群組的概念，協助您有效地使用此功能。 如果您從未建立過網路安全性群組，可以完成快速[教學課程](tutorial-filter-network-traffic.md)，以取得一些建立體驗。 如果您熟悉網路安全性群組，並且有管理需求，請參閱[管理網路安全性群組](manage-network-security-group.md)。 如果您有通訊問題，因而需要對網路安全性群組進行疑難排解，請參閱[診斷虛擬機器網路流量篩選問題](diagnose-network-traffic-filter-problem.md)。 您可以啟用[網路安全性群組流程記錄](../network-watcher/network-watcher-nsg-flow-logging-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)，針對與網路安全性群組相關聯的資源，分析進出其中的[網路流量](../network-watcher/traffic-analytics.md?toc=%2fazure%2fvirtual-network%2ftoc.json)。

## <a name="security-rules"></a>安全性規則

視 Azure 訂用帳戶[限制](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits)而定，網路安全性群組可包含零個或多個規則。 每個規則都會指定下列屬性：

|屬性  |說明  |
|---------|---------|
|名稱|網路安全性群組中的唯一名稱。|
|優先順序 | 100 和 4096 之間的數字。 系統會依照優先權順序處理規則，較低的數字會在較高的數字之前處理，因為較低的數字具有較高的優先順序。 一旦流量符合規則，處理就會停止。 因此，如果存在較低優先順序 (較高數字) 的規則具有與較高優先順序之規則相同的屬性，則不會進行處理。|
|來源或目的地| 任何或個別的 IP 位址、無類別網域間路由 (CIDR) 區塊 (例如 10.0.0.0/24)、[服務標籤](#service-tags)或[應用程式安全性群組](#application-security-groups)。 當您指定 Azure 資源的位址時，可以指定指派給資源的私人 IP 位址。 在 Azure 針對輸入流量將公用 IP 位址轉譯為私人 IP 位址之後，和 Azure 針對輸出流量將私人 IP 位址轉譯為公用 IP 位址之前，網路安全性群組會進行處理。 深入了解 Azure [IP 位址](virtual-network-ip-addresses-overview-arm.md)。 指定範圍、服務標籤或應用程式安全性群組，可讓您建立較少的安全性規則。 在規則中指定多個個別 IP 位址和範圍 (您無法指定多個服務標籤或應用程式群組) 的功能也稱為[增強型安全性規則](#augmented-security-rules)。 增強型安全性規則只可以在透過 Resource Manager 部署模型建立的網路安全性群組中建立。 您無法在透過傳統部署模型建立的網路安全性群組中指定多個 IP 位址與 IP 位址範圍。 深入了解 Azure [部署模型](../azure-resource-manager/resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json)。|
|Protocol     | TCP、UDP 或 [任何]，其中包括 (但不限於) TCP、 UDP 和 ICMP。 您無法單獨指定 ICMP，如果您需要 ICMP，則必須使用 [任何]。 |
|Direction| 規則是否套用至輸入或輸出流量。|
|連接埠範圍     |您可以指定個別連接埠或連接埠範圍。 例如，您可以指定 80 或 10000-10005。 指定範圍可讓您建立較少的安全性規則。 增強型安全性規則只可以在透過 Resource Manager 部署模型建立的網路安全性群組中建立。 您無法在透過傳統部署模型建立之網路安全性群組的相同安全性規則中指定多個連接埠與連接埠範圍。   |
|動作     | 允許或拒絕        |

系統會依優先順序使用 5 項 Tuple 資訊 (來源、來源連接埠、目的地、目的地連接埠和通訊協定) 來評估網路安全性群組的安全性規則，以允許或拒絕流量。 系統會為現有連線建立流程記錄。 允許或拒絕通訊都會以此流程記錄的連線狀態為依據。 流程記錄可讓網路安全性群組成為具狀態的形式。 如果您將輸出安全性規則指定至任何透過連接埠 80 (舉例來說) 的位址，則不必為了回應輸出流量來指定輸入安全性規則。 如果在外部起始通訊，您只需要指定輸入安全性規則。 反之亦然。 如果允許透過連接埠傳送輸入流量，則不必指定輸出安全性規則來透過連接埠回應流量。
當您移除啟用流量的安全性規則時，現有的連線不會中斷。 當連線停止，且兩個方向至少有數分鐘都沒有流量時，流量即會中斷。

您可以在網路安全性群組中建立的安全性規則數量會有所限制。 如需詳細資訊，請參閱 [Azure 限制](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits)。

## <a name="augmented-security-rules"></a>增強型安全性規則

增強型安全性規則可簡化虛擬網路的安全性定義，讓您定義更大且複雜的網路安全性原則 (但規則比較少)。 您可以將多個連接埠和多個明確 IP 位址與範圍，合併成易於了解的單一安全性規則。 在規則的來源、目的地和連接埠欄位中使用增強型規則。 若要簡化安全性規則定義的維護，請合併增強型安全性規則與[服務標籤](#service-tags)或[應用程式安全性群組](#application-security-groups)。 您可以在規則中指定的位址、範圍和連接埠數量會有所限制。 如需詳細資訊，請參閱 [Azure 限制](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits)。

## <a name="service-tags"></a>服務標籤

服務標籤表示一組 IP 位址前置詞，有助於降低建立安全性規則的複雜性。 您無法建立自己的服務標籤，或指定標籤中包含哪些 IP 位址。 Microsoft 會管理服務標籤包含的位址前置詞，並隨著位址變更自動更新服務標籤。 建立安全性規則時，您可以使用服務標籤取代特定的 IP 位址。 

下列服務標籤可供使用，在[網路安全性群組規則](https://docs.microsoft.com/azure/virtual-network/security-overview#security-rules)。 以星號結尾 （也就是 AzureCloud *） 的服務標籤也會用於[網路的 Azure 防火牆規則](https://docs.microsoft.com/azure/firewall/service-tags)。 

* **VirtualNetwork** (Resource Manager) (在傳統模型為 **VIRTUAL_NETWORK**)：這個標籤包含虛擬網路位址空間 （定義的所有 CIDR 範圍的虛擬網路），所有已連線的內部部署位址空間[對等互連](virtual-network-peering-overview.md)虛擬網路或虛擬網路連線至[虛擬網路閘道](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json)並處理上使用的前置詞[使用者定義的路由](virtual-networks-udr-overview.md)。 請注意，此標記可能會包含預設路由。 
* **AzureLoadBalancer** (Resource Manager) (在傳統模型為 **AZURE_LOADBALANCER**)：此標記代表 Azure 基礎結構的負載平衡器。 此標記會轉譯成作為 Azure 健康情況探查來源的[主機虛擬 IP 位址](security-overview.md#azure-platform-considerations) (168.63.129.16)。 如果您未使用 Azure 負載平衡器，則可以覆寫此規則。
* **Internet** (Resource Manager) (在傳統模型為 **INTERNET**)：此標記代表虛擬網路以外且可以透過公用網際網路進行存取的 IP 位址空間。 此位址範圍也包括 [Azure 擁有的公用 IP 位址空間](https://www.microsoft.com/download/details.aspx?id=41653)。
* **AzureCloud*** (僅限 Resource Manager):此標記代表包含所有[資料中心公用 IP 位址](https://www.microsoft.com/download/details.aspx?id=41653)的 Azure IP 位址空間。 如果您指定 *AzureCloud* 作為值，就會允許或拒絕 Azure 公用 IP 位址的流量。 如果您只是要允許存取特定的 AzureCloud[地區](https://azure.microsoft.com/regions)，您可以指定地區，以下列格式 AzureCloud。 [區域名稱]。 輸出安全性規則，建議您使用此標記。 
* **AzureTrafficManager*** (僅限 Resource Manager):此標記代表 Azure 流量管理員探查 IP 位址的 IP 位址空間。 如需流量管理員探查 IP 位址的詳細資訊，請參閱 [Azure 流量管理員常見問題集](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs)。 此標籤建議的輸入的安全性規則。  
* **儲存體*** (僅限 Resource Manager):此標記代表 Azure 儲存體服務的 IP 位址空間。 如果您指定 *Storage* 值，則會允許或拒絕儲存體的流量。 如果您只想要允許存取儲存體中特定[地區](https://azure.microsoft.com/regions)，您可以指定地區，以下列格式儲存。 [區域名稱]。 標籤代表服務，但不代表服務的特定執行個體。 例如，標籤代表 Azure 儲存體服務，但不代表特定的 Azure 儲存體帳戶。 輸出安全性規則，建議您使用此標記。 
* **Sql*** (僅限 Resource Manager):這個標籤代表 Azure SQL Database、 適用於 MySQL 的 Azure 資料庫、 適用於 PostgreSQL 的 Azure 資料庫的位址首碼和 Azure SQL 資料倉儲服務。 如果您指定 Sql  作為值，就會允許或拒絕 Sql 的流量。 如果您只是要允許存取 Sql 中特殊[地區](https://azure.microsoft.com/regions)，您可以以下列格式中指定的區域。 Sql。 [區域名稱] 標籤代表服務，但不代表服務的特定執行個體。 例如，標籤代表 SQL Database 或伺服器服務，但不代表特定的 Azure SQL Database。 輸出安全性規則，建議您使用此標記。 
* **AzureCosmosDB*** (僅限 Resource Manager):此標記代表 Azure Cosmos Database 服務的位址前置詞。 如果您指定 *AzureCosmosDB* 作為值，就會允許或拒絕 AzureCosmosDB 的流量。 如果您只要在特定[區域](https://azure.microsoft.com/regions)中允許存取 AzureCosmosDB，您可以用 AzureCosmosDB.[region name] 的格式指定區域。 輸出安全性規則，建議您使用此標記。 
* **AzureKeyVault*** (僅限 Resource Manager):此標記代表 Azure KeyVault 服務的位址前置詞。 如果您指定 *AzureKeyVault* 作為值，就會允許或拒絕 AzureKeyVault 的流量。 如果您只要在特定[區域](https://azure.microsoft.com/regions)中允許存取 AzureKeyVault，您可以用 AzureKeyVault.[region name] 的格式指定區域。 此標記有相依性**AzureActiveDirectory**標記。 輸出安全性規則，建議您使用此標記。  
* **EventHub*** (僅限 Resource Manager):此標記代表 Azure EventHub 服務的位址前置詞。 如果您指定 EventHub  作為值，就會允許或拒絕 EventHub 的流量。 如果您只想要在特定[區域](https://azure.microsoft.com/regions)中允許存取 EventHub，您可以用 EventHub.[region name] 的格式指定區域。 輸出安全性規則，建議您使用此標記。 
* **服務匯流排*** (僅限 Resource Manager):這個標籤代表使用 Premium 服務層的 Azure 服務匯流排服務的位址前置詞。 如果您指定 ServiceBus  作為值，就會允許或拒絕 ServiceBus 的流量。 如果您只想要在特定[區域](https://azure.microsoft.com/regions)中允許存取 ServiceBus，您可以用 ServiceBus.[region name] 的格式指定區域。 輸出安全性規則，建議您使用此標記。 
* **MicrosoftContainerRegistry*** (僅限 Resource Manager):此標記代表 Microsoft Container Registry 服務的位址前置詞。 如果您指定 MicrosoftContainerRegistry  作為值，就會允許或拒絕 MicrosoftContainerRegistry 的流量。 如果您只想要在特定[區域](https://azure.microsoft.com/regions)中允許存取 MicrosoftContainerRegistry，您可以用 MicrosoftContainerRegistry.[region name] 的格式指定區域。 輸出安全性規則，建議您使用此標記。 
* **AzureContainerRegistry*** (僅限 Resource Manager):此標記代表 Azure Container Registry 服務的位址前置詞。 如果您指定 AzureContainerRegistry  作為值，就會允許或拒絕 AzureContainerRegistry 的流量。 如果您只想要在特定[區域](https://azure.microsoft.com/regions)中允許存取 AzureContainerRegistry，您可以用 AzureContainerRegistry.[region name] 的格式指定區域。 輸出安全性規則，建議您使用此標記。 
* **AppService*** (僅限 Resource Manager):此標記代表 Azure AppService 服務的位址前置詞。 如果您指定 AppService  作為值，就會允許或拒絕 AppService 的流量。 如果您只想要在特定[區域](https://azure.microsoft.com/regions)中允許存取 AppService，您可以用 AppService.[region name] 的格式指定區域。 此標記的 web 應用程式前端建議輸出安全性規則。  
* **AppServiceManagement*** (僅限 Resource Manager):這個標籤代表專用的 App Service Environment 部署的管理流量的位址首碼。 如果您指定 AppServiceManagement  作為值，就會允許或拒絕 AppServiceManagement 的流量。 此標籤建議的輸入/輸出安全性規則。 
* **ApiManagement*** (僅限 Resource Manager):這個標籤代表 APIM 的管理流量的位址首碼專用的部署。 如果您指定 ApiManagement  作為值，就會允許或拒絕 ApiManagement 的流量。 此標籤建議的輸入/輸出安全性規則。 
* **AzureConnectors*** (僅限 Resource Manager):這個標籤代表探查/後端連線的 Logic Apps 連接器的位址首碼。 如果您指定 AzureConnectors  作為值，就會允許或拒絕 AzureConnectors 的流量。 如果您只要在特定[區域](https://azure.microsoft.com/regions)中允許存取 AzureConnectors，您可以用 AzureConnectors.[region name] 的格式指定區域。 此標籤建議的輸入的安全性規則。 
* **GatewayManager** (僅限 Resource Manager)：這個標籤代表專用的 VPN/應用程式閘道部署的管理流量的位址首碼。 如果您指定 GatewayManager  作為值，就會允許或拒絕 GatewayManager 的流量。 此標籤建議的輸入的安全性規則。 
* **AzureDataLake*** (僅限 Resource Manager):此標記代表 Azure Data Lake 服務的位址前置詞。 如果您指定 AzureDataLake  作為值，就會允許或拒絕 AzureDataLake 的流量。 輸出安全性規則，建議您使用此標記。 
* **AzureActiveDirectory*** (僅限 Resource Manager):此標記代表 AzureActiveDirectory 服務的位址前置詞。 如果您指定 AzureActiveDirectory  作為值，就會允許或拒絕 AzureActiveDirectory 的流量。 輸出安全性規則，建議您使用此標記。
* **AzureMonitor*** (僅限 Resource Manager):這個標籤代表的 Log Analytics、 應用程式深入解析、 AzMon，及自訂的度量 （Gb 端點） 的位址前置詞。 如果您指定 AzureMonitor  作為值，就會允許或拒絕 AzureMonitor 的流量。 Log analytics，此標記會有相依性**儲存體**標記。 輸出安全性規則，建議您使用此標記。
* **ServiceFabric*** (僅限 Resource Manager):此標記代表 ServiceFabric 服務的位址前置詞。 如果您指定 ServiceFabric  作為值，就會允許或拒絕 ServiceFabric 的流量。 輸出安全性規則，建議您使用此標記。 
* **AzureMachineLearning*** (僅限 Resource Manager):此標記代表 AzureMachineLearning 服務的位址前置詞。 如果您指定 AzureMachineLearning  作為值，就會允許或拒絕 AzureMachineLearning 的流量。 輸出安全性規則，建議您使用此標記。 
* **BatchNodeManagement*** (僅限 Resource Manager):這個標籤代表專用的 Azure 批次部署的管理流量的位址首碼。 如果您指定*BatchNodeManagement*的值，是允許或拒絕流量從 Batch 服務至計算節點。 此標籤建議的輸入/輸出安全性規則。 
* **AzureBackup*** (僅限 Resource Manager):這個標籤代表 AzureBackup 服務的位址首碼。 如果您指定*AzureBackup*的值，允許或拒絕 AzureBackup 流量。 此標記有相依性**儲存體**並**AzureActiveDirectory**標記。輸出安全性規則，建議您使用此標記。 
* **AzureActiveDirectoryDomainServices*** (僅限 Resource Manager):這個標籤代表 Azure Active Directory Domain Services 專用部署的管理流量的位址首碼。 如果您指定*AzureActiveDirectoryDomainServices*的值，允許或拒絕 AzureActiveDirectoryDomainServices 流量。 此標籤建議的輸入/輸出安全性規則。  
* **SqlManagement*** (僅限 Resource Manager):這個標籤代表 sql 管理流量的位址首碼專用的部署。 如果您指定*SqlManagement*的值，允許或拒絕 SqlManagement 流量。 此標籤建議的輸入/輸出安全性規則。 
* **CognitiveServicesManagement** (僅限 Resource Manager):這個標籤代表認知服務的位址首碼的流量。 如果您指定*CognitiveServicesManagement*的值，允許或拒絕 CognitiveServicesManagement 流量。 輸出安全性規則，建議您使用此標記。  
* **Dynamics365ForMarketingEmail** (僅限 Resource Manager):這個標籤代表 Dynamics 365 的 「 行銷電子郵件服務的位址首碼。 如果您指定*Dynamics365ForMarketingEmail*的值，允許或拒絕 Dynamics365ForMarketingEmail 流量。 如果您只想允許特定存取 Dynamics365ForMarketingEmail[地區](https://azure.microsoft.com/regions)，您可以以下列格式 Dynamics365ForMarketingEmail 指定地區。 [區域名稱]。
* **AzurePlatformDNS** (僅限 Resource Manager):這個標籤代表 DNS，這是基本的基礎結構服務。 如果您指定*AzurePlatformDNS*的值，您可以停用預設[Azure 平台考量](https://docs.microsoft.com/azure/virtual-network/security-overview#azure-platform-considerations)dns。 請採取謹慎使用這個標記。 使用此標記之前，建議您測試。 
* **AzurePlatformIMDS** (僅限 Resource Manager):這個標籤代表 IMDS 的基本基礎結構服務。 如果您指定*AzurePlatformIMDS*的值，您可以停用預設[Azure 平台考量](https://docs.microsoft.com/azure/virtual-network/security-overview#azure-platform-considerations)IMDS 的。 請採取謹慎使用這個標記。 使用此標記之前，建議您測試。 
* **AzurePlatformLKM** (僅限 Resource Manager):這個標籤代表 Windows 授權或金鑰管理服務。 如果您指定*AzurePlatformLKM*的值，您可以停用預設[Azure 平台考量](https://docs.microsoft.com/azure/virtual-network/security-overview#azure-platform-considerations)授權。 請採取謹慎使用這個標記。 使用此標記之前，建議您測試。 

> [!NOTE]
> Azure 服務的服務標記代表所使用之特定雲端中的位址前置詞。 

> [!NOTE]
> 如果您對服務 (例如 Azure 儲存體或 Azure SQL Database) 實作[虛擬網路服務端點](virtual-network-service-endpoints-overview.md)，Azure 會將[路由](virtual-networks-udr-overview.md#optional-default-routes)新增至服務的虛擬網路子網路。 路由中的位址前置詞為相同的位址前置詞，或 CIDR 範圍，以及對應的服務標籤。

### <a name="service-tags-in-on-premises"></a>在內部部署的服務標籤  
您可以下載並整合內部部署防火牆清單中的前置詞的詳細資料適用於 Azure 的下列每週發行集上的服務標籤[公開金鑰](https://www.microsoft.com/download/details.aspx?id=56519)，[美國政府](https://www.microsoft.com/download/details.aspx?id=57063)，[中國](https://www.microsoft.com/download/details.aspx?id=57062)，並[德國](https://www.microsoft.com/download/details.aspx?id=57064)雲端。

您可以也以程式設計方式擷取此資訊使用**服務標記探索 API** （公開預覽）- [REST](https://aka.ms/discoveryapi_rest)， [Azure PowerShell](https://aka.ms/discoveryapi_powershell)，和[Azure CLI](https://aka.ms/discoveryapi_cli)。 

> [!NOTE]
> 遵循每週的發行集 （舊版） azure[公開金鑰](https://www.microsoft.com/en-us/download/details.aspx?id=41653)，[中國](https://www.microsoft.com/en-us/download/details.aspx?id=42064)，並[德國](https://www.microsoft.com/en-us/download/details.aspx?id=54770)雲端會在 2020 年 6 月 30 日淘汰。 請開始使用更新的發行集，如上面所述。 

## <a name="default-security-rules"></a>預設安全性規則

Azure 會在您建立的每個網路安全性群組中，建立下列預設規則：

### <a name="inbound"></a>輸入

#### <a name="allowvnetinbound"></a>AllowVNetInBound

|優先順序|Source|來源連接埠|目的地|目的地連接埠|Protocol|Access|
|---|---|---|---|---|---|---|
|65000|VirtualNetwork|0-65535|VirtualNetwork|0-65535|全部|允許|

#### <a name="allowazureloadbalancerinbound"></a>AllowAzureLoadBalancerInBound

|優先順序|Source|來源連接埠|目的地|目的地連接埠|Protocol|Access|
|---|---|---|---|---|---|---|
|65001|AzureLoadBalancer|0-65535|0.0.0.0/0|0-65535|全部|允許|

#### <a name="denyallinbound"></a>DenyAllInbound

|優先順序|Source|來源連接埠|目的地|目的地連接埠|Protocol|Access|
|---|---|---|---|---|---|---|
|65500|0.0.0.0/0|0-65535|0.0.0.0/0|0-65535|全部|拒絕|

### <a name="outbound"></a>輸出

#### <a name="allowvnetoutbound"></a>AllowVnetOutBound

|優先順序|Source|來源連接埠| 目的地 | 目的地連接埠 | Protocol | Access |
|---|---|---|---|---|---|---|
| 65000 | VirtualNetwork | 0-65535 | VirtualNetwork | 0-65535 | 全部 | 允許 |

#### <a name="allowinternetoutbound"></a>AllowInternetOutBound

|優先順序|Source|來源連接埠| 目的地 | 目的地連接埠 | Protocol | Access |
|---|---|---|---|---|---|---|
| 65001 | 0.0.0.0/0 | 0-65535 | Internet | 0-65535 | 全部 | 允許 |

#### <a name="denyalloutbound"></a>DenyAllOutBound

|優先順序|Source|來源連接埠| 目的地 | 目的地連接埠 | Protocol | Access |
|---|---|---|---|---|---|---|
| 65500 | 0.0.0.0/0 | 0-65535 | 0.0.0.0/0 | 0-65535 | 全部 | 拒絕 |

在 [來源]  和 [目的地]  欄中，*VirtualNetwork*、*AzureLoadBalancer*和 *Internet* 都是[服務標籤](#service-tags)，而不是 IP 位址。 在 [通訊協定] 欄中，[全部]  包含 TCP、 UDP 和 ICMP。 建立規則時，您可以指定 TCP、UDP 或 [全部]，但不能單獨指定 ICMP。 因此，如果您的規則需要 ICMP，請針對通訊協定選取 [所有]  。 [來源]  和 [目的地]  欄中的 *0.0.0.0/0* 代表所有位址。 用戶端讓 Azure 入口網站中，Azure CLI 或 Powershell 可以使用 * 或 any 代表此運算式。
 
您無法移除預設規則，但可以建立較高優先順序的規則來覆寫預設規則。

## <a name="application-security-groups"></a>應用程式安全性群組

應用程式安全性群組可讓您將網路安全性設定為應用程式結構的自然擴充功能，讓您將虛擬機器分組，並定義以這些群組為基礎的網路安全性原則。 您可以大規模重複使用您的安全性原則，而不需進行明確 IP 位址的手動維護。 此平台可處理明確 IP 位址和多個規則集的複雜性，讓您專注於商務邏輯。 若要進一步了解應用程式安全性群組，請考慮下列範例：

![應用程式安全性群組](./media/security-groups/application-security-groups.png)

在上圖中，NIC1  和 NIC2  都是 AsgWeb  應用程式安全性群組的成員。 NIC3  是 AsgLogic  應用程式安全性群組的成員。 NIC4  是 AsgDb  應用程式安全性群組的成員。 雖然在此範例中的每個網路介面都只是一個應用程式安全性群組的成員，但網路介面可以是多個應用程式安全性群組的成員，數量上限可參照 [Azure 限制](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits)。 這些網路介面都沒有相關聯的網路安全性群組。 NSG1  與這兩個子網路相關聯且包含下列規則：

### <a name="allow-http-inbound-internet"></a>Allow-HTTP-Inbound-Internet

需要此規則才能讓流量從網際網路流向 Web 伺服器。 因為來自網際網路的輸入流量會遭到 [DenyAllInbound](#denyallinbound) 預設安全性規則拒絕，而 AsgLogic  或 AsgDb  應用程式安全性群組則不需要其他規則。

|優先順序|Source|來源連接埠| 目的地 | 目的地連接埠 | Protocol | Access |
|---|---|---|---|---|---|---|
| 100 | Internet | * | AsgWeb | 80 | TCP | 允許 |

### <a name="deny-database-all"></a>Deny-Database-All

由於 [AllowVNetInBound](#allowvnetinbound) 預設安全性規則允許相同虛擬網路中各資源之間的所有通訊，因此需要此規則才能拒絕來自所有資源的流量。

|優先順序|Source|來源連接埠| 目的地 | 目的地連接埠 | Protocol | Access |
|---|---|---|---|---|---|---|
| 120 | * | * | AsgDb | 1433 | 全部 | 拒絕 |

### <a name="allow-database-businesslogic"></a>Allow-Database-BusinessLogic

此規則會允許流量從 AsgLogic  應用程式安全性群組流向 AsgDb  應用程式安全性群組。 此規則的優先順序高於 Deny-Database-All  規則的優先順序。 如此一來，此規則會在 Deny-Database-All  規則之前進行處理，因此系統會允許來自 AsgLogic  應用程式安全性群組的流量，但所有其他流量仍會遭到封鎖。

|優先順序|Source|來源連接埠| 目的地 | 目的地連接埠 | Protocol | Access |
|---|---|---|---|---|---|---|
| 110 | AsgLogic | * | AsgDb | 1433 | TCP | 允許 |

用於將應用程式安全性群組指定為來源或目的地的規則，只會套用至屬於此應用程式安全性群組成員的網路介面。 如果網路介面不是應用程式安全性群組的成員，則此規則不適用於此網路介面，即使子網路與網路安全性群組相關聯也一樣。

應用程式安全性群組具有下列條件約束：

-   您可以在訂用帳戶中擁有的應用程式安全性群組數量會有所限制，而且還有其他與應用程式安全性群組相關的限制。 如需詳細資訊，請參閱 [Azure 限制](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits)。
- 您可以將一個應用程式安全性群組指定為安全性規則中的來源和目的地。 您無法在來源或目的地中指定多個應用程式安全性群組。
- 指派給應用程式安全性群組的所有網路介面，都必須與指派給應用程式安全性群組的第一個網路介面位於相同虛擬網路中。 例如，如果指派給應用程式安全性群組 AsgWeb  的第一個網路介面位於名為 VNet1  的虛擬網路中，則後續所有指派給 ASGWeb  的網路介面都必須存在於 VNet1  中。 您無法將不同虛擬網路的網路介面新增至相同的應用程式安全性群組。
- 如果您指定安全性群組作為安全性規則中的來源和目的地，兩個應用程式安全性群組中的網路介面都必須在相同的虛擬網路中。 例如，如果 AsgLogic  包含來自 VNet1  的網路介面，而 AsgDb  包含來自 VNet2  的網路介面，您無法在規則中將 AsgLogic  指派為來源，將 AsgDb  指派為目的地。 來源和目的地應用程式安全性群組的所有網路介面都必須位在相同的虛擬網路中。

> [!TIP]
> 若要將所需的安全性規則數量及規則變更需求降到最低，請規劃好您需要的應用程式安全性群組，並盡可能使用服務標籤或應用程式安全性群組來建立規則，而不是使用個別的 IP 位址或 IP 位址範圍。

## <a name="how-traffic-is-evaluated"></a>評估流量的方式

您可以將數個 Azure 服務的資源部署到 Azure 虛擬網路。 如需完整清單，請參閱[可以部署至虛擬網路的服務](virtual-network-for-azure-services.md#services-that-can-be-deployed-into-a-virtual-network)。 您可以將零個或一個網路安全性群組關聯至每個虛擬網路[子網路](virtual-network-manage-subnet.md#change-subnet-settings)，以及虛擬機器中的[網路介面](virtual-network-network-interface.md#associate-or-dissociate-a-network-security-group)。 您可以將相同的網路安全性群組關聯至所需數量的子網路和網路介面。

下圖以不同案例說明網路安全性群組可如何部署，以允許網路流量透過 TCP 連接埠 80 來進出網際網路：

![NSG 處理](./media/security-groups/nsg-interaction.png)

請參考上圖及下列文字，以了解 Azure 如何處理網路安全性群組的輸入和輸出規則：

### <a name="inbound-traffic"></a>輸入流量

針對輸入流量，Azure 會先針對與子網路相關聯的網路安全性群組，處理其中的規則 (如果有的話)，然後再針對與網路介面相關聯的網路安全性群組，處理其中的規則 (如果有的話)。

- **VM1**：NSG1  中的安全性規則會進行處理，因為它與 Subnet1  和 VM1  相關聯，並且位於 Subnet1  中。 除非您已建立一個規則來允許連接埠 80 的輸入，否則流量會遭到 [DenyAllInbound](#denyallinbound) 預設安全性規則拒絕，並永遠不會由 NSG2  進行評估，因為 NSG2  與網路介面相關聯。 如果 NSG1  具有允許連接埠 80 的安全性規則，流量接著會由 NSG2  進行處理。 若要允許流量從連接埠 80 輸入虛擬機器，則 NSG1  和 NSG2  都必須有規則來允許從網際網路輸入流量的連接埠 80。
- **VM2**：在 NSG1  中的規則會進行處理，因為VM2  也位於 Subnet1  中。 由於 VM2  沒有與其網路介面相關聯的網路安全性群組，因此會接收允許通過 NSG1  的所有流量，或拒絕所有 NSG1  拒絕的流量。 如果網路安全性群組與子網路相關聯，則相同子網路中的所有資源會一起接收或拒絕流量。
- **VM3**：由於沒有任何網路安全性群組與 Subnet2  相關聯，流量會允許進入子網路並由NSG2  處理流量，因為 NSG2  與連結至 VM3  的網路介面相關聯。
- **VM4**：流量會允許進入 VM4  ，因為網路安全性群組未與 Subnet3  或虛擬機器中的網路介面相關聯。 如果沒有任何網路安全性群組與子網路和網路介面相關聯，則所有網路流量都可以通過子網路和網路介面。

### <a name="outbound-traffic"></a>輸出流量

針對輸出流量，Azure 會先針對與網路介面相關聯的網路安全性群組，處理其中的規則 (如果有的話)，然後再針對與子網路相關聯的網路安全性群組，處理其中的規則 (如果有的話)。

- **VM1**：NSG2  中的安全性規則會進行處理。 除非您建立安全性規則來拒絕向網際網路輸出流量的連接埠 80，否則 NSG1  和 NSG2  中的 [AllowInternetOutbound](#allowinternetoutbound) 預設安全性規則會允許流量通過。 如果 NSG2  具有拒絕連接埠 80 的安全性規則，則流量會遭到拒絕，且永遠不會由 NSG1  進行評估。 若要拒絕流量從連接埠 80 輸出虛擬機器，其中一個網路安全性群組或兩個網路安全性群組必須有規則來拒絕將流量流向網際網路的連接埠 80。
- **VM2**：所有流量都會通過網路介面流向子網路，因為連結到 VM2  的網路介面沒有與網路安全性群組相關聯。 NSG1  中的規則會進行處理。
- **VM3**：如果 NSG2  具有拒絕連接埠 80 的安全性規則，則流量會遭到拒絕。 如果 NSG2  有允許連接埠 80 的安全性規則，則連接埠 80 允許輸出流量到網際網路，因為沒有與 Subnet2  相關聯的網路安全性群組。
- **VM4**：所有網路流量會允許從 VM4  輸出，因為網路安全性群組未與連結至虛擬機器的網路介面或 Subnet3  相關聯。

藉由檢視網路介面的[有效安全性規則](virtual-network-network-interface.md#view-effective-security-rules)，可以輕鬆地檢視套用至網路介面的彙總規則。 您也可以使用 Azure 網路監看員中的 [IP 流量確認](../network-watcher/diagnose-vm-network-traffic-filtering-problem.md?toc=%2fazure%2fvirtual-network%2ftoc.json)功能來判斷是否允許網路介面的雙向通訊。 IP 流量確認會告訴您已允許會拒絕通訊，以及哪個網路安全性規則允許或拒絕流量。

> [!NOTE]
> 網路安全性群組會關聯至子網路或虛擬機器和雲端服務部署在傳統部署模型中，以及子網路或 Resource Manager 部署模型中的網路介面。 若要深入了解 Azure 部署模型，請參閱[了解 Azure 部署模型](../azure-resource-manager/resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json)。

> [!TIP]
> 除非您有特殊原因要這麼做，否則我們建議您讓網路安全性群組與子網路或網路介面的其中一個建立關聯，而非同時與這兩者建立關聯。 因為如果與子網路相關聯的網路安全性群組中，以及與網路介面相關聯的網路安全性群組中都存在規則，則這兩個規則可能會發生衝突，您可能會遇到需要進行疑難排解的非預期通訊問題。

## <a name="azure-platform-considerations"></a>Azure 平台的考量

- **主機節點的虛擬 IP**：基本的基礎結構服務，例如 DHCP、DNS、IMDS 和健康情況監控是透過虛擬化主機 IP 位址 168.63.129.16 和 169.254.169.254 所提供。 這些 IP 位址屬於 Microsoft，而且是針對此目的唯一用於所有地區的虛擬 IP。
- **授權 (金鑰管理服務)** ：必須授權在虛擬機器中執行的 Windows 映像。 若要確保授權，授權要求會傳送至處理此類查詢的金鑰管理服務主機伺服器。 此要求是透過連接埠 1688 輸出。 若為使用[預設路由 0.0.0.0/0](virtual-networks-udr-overview.md#default-route)組態的部署，將會停用此平台規則。
- **負載平衡集區中的虛擬機器**：套用的來源連接埠和位址範圍是來自原始電腦，而不是負載平衡器。 目的地連接埠和位址範圍屬於目的地電腦，而不是負載平衡器。
- **Azure 服務執行個體**：虛擬網路子網路中會部署數個 Azure 服務的執行個體，例如 HDInsight、應用程式服務環境及虛擬機器擴展集。 如需您可以部署到虛擬網路的完整服務清單，請參閱 [Azure 服務的虛擬網路](virtual-network-for-azure-services.md#services-that-can-be-deployed-into-a-virtual-network)。 將網路安全性群組套用至部署資源的子網路之前，請先確定您熟悉每個服務的連接埠需求。 如果您拒絕服務所需要的連接埠，服務就無法正常運作。
- **傳送外寄電子郵件**：Microsoft 建議您利用已驗證的 SMTP 轉送服務 (通常透過 TCP 連接埠 587 連線，但也可透過其他連接埠連線)，從 Azure 虛擬機器傳送電子郵件。 SMTP 轉送服務是專為寄件者信譽所設計，可將第三方電子郵件提供者拒絕訊息的可能性降到最低。 這類 SMTP 轉送服務包括但不限於 Exchange Online Protection 和 SendGrid。 不論您的訂用帳戶類型為何，在 Azure 中使用 SMTP 轉送服務不受限制。 

  如果您在 2017 年 11 月 15 日前建立了 Azure 訂用帳戶，除了能夠使用 SMTP 轉送服務，您還可以直接透過 TCP 連接埠 25 傳送電子郵件。 如果您在 2017 年 11 月 15 日後建立了訂用帳戶，您可能無法直接透過連接埠 25 傳送電子郵件。 透過連接埠 25 的連出通訊行為取決於您擁有的訂用帳戶類型，如下所示：

     - **Enterprise 合約**：允許輸出通訊埠 25 通訊。 您能夠從虛擬機器將外寄電子郵件直接傳送到外部電子郵件提供者 (Azure 平台沒有限制)。 
     - **隨用隨付**：所有資源的輸出連接埠 25 通訊都遭到封鎖。 如果您需要將電子郵件從虛擬機器直接傳送給外部電子郵件提供者 (不使用已驗證的 SMTP 轉送)，可以提出移除限制的要求。 要求是在 Microsoft 的斟酌之下審查與核准，而且只會在執行反詐騙檢查之後授權。 若要提出要求，請開啟問題類型為 [技術]  、[虛擬網路連線]  、[無法傳送電子郵件 (SMTP/連接埠 25)]  的支援案例。 在您的支援案例中，請包含訂用帳戶需要將電子郵件直接傳送到郵件提供者，而不需經過已驗證 SMTP 轉送之原因的詳細資料。 如果您的訂用帳戶獲得豁免，則只有在豁免日期之後建立的虛擬機器能夠透過連接埠 25 對外通訊。
     - **MSDN、Azure Pass、Azure in Open、Education、BizSpark 及免費試用**：所有資源的輸出連接埠 25 通訊都遭到封鎖。 無法進行任何移除限制的要求，因為要求未獲授權。 如果您必須從虛擬機器傳送電子郵件，就必須使用 SMTP 轉送服務。
     - **雲端服務提供者**：透過雲端服務提供者使用 Azure 資源的客戶，可以建立其雲端服務提供者的支援案例，以及在安全的 SMTP 轉送無法使用時，要求提供者代表他們建立解除封鎖案例。

  如果 Azure 允許您透過連接埠 25 傳送電子郵件，Microsoft 無法保證電子郵件提供者會接受您虛擬機器所發出的內送電子郵件。 如果特定提供者拒絕來自虛擬機器的郵件，請直接與提供者合作以解決任何訊息傳遞或垃圾郵件篩選問題，或使用已驗證的 SMTP 轉送服務。

## <a name="next-steps"></a>後續步驟

* 了解如何[建立網路安全性群組](tutorial-filter-network-traffic.md)。
