---
title: 常見問題-Azure VMware 解決方案（AVS）
description: Azure VMware 解決方案（AVS）的常見問題
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/15/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: c3808491c84f6c76a51c914aac6ee5e5ee370970
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/05/2020
ms.locfileid: "77025056"
---
# <a name="frequently-asked-questions-about-vmware-solution-by-avs"></a>以 AVS 的 VMware 解決方案的相關常見問題

## <a name="avs-service"></a>AVS 服務

**什麼是 Azure VMware 解決方案（AVS）？**

Azure VMware 解決方案（AVS）會在短短幾分鐘內，將 VMware 工作負載轉換並延伸至 Azure 上的私人雲端專用雲端。 AVS 會負責布建、管理基礎結構，並協調內部部署與 Azure 之間的工作負載。 由於您的應用程式在內部部署和 Azure 中執行的方式完全相同，因此您可以從雲端的彈性和服務獲益，而不需要重新架構應用程式的複雜性。 AVS 透過雲端取用模型降低您的擁有權總成本，以提供隨選布建、隨用隨付和容量優化。 如需功能、優點和案例，請參閱[Azure 上的 VMware 解決方案（由 AVS](cloudsimple-vmware-solutions-overview.md)提供）。

**什麼是 AVS 私人雲端？**

AVS 私用雲端是私用的專用雲端，其中包含部署在 Azure 位置的 Microsoft Azure 基礎結構（硬體和資料中心空間）上的高效能計算、儲存體和網路環境。 AVS 私用雲端提供原生 VMware 的「平臺即服務」。 在 VMware 方面，每個 AVS 私人雲端都只包含一個 vCenter Server 實例。 此 vCenter Server 會管理一或多個 vSphere 叢集包含的多個 ESXi 節點，以及對應的虛擬 SAN （vSAN）儲存體。 AVS 服務在您的 Azure 訂用帳戶中可以包含多個 AVS 私人雲端。 如需詳細資訊，請參閱[AVS 私用雲端總覽](cloudsimple-private-cloud.md)。

**有哪些 AVS 服務可供使用？**

「美國東部」、「美國西部」和「西歐」區域提供 AVS，即將推出其他區域。

**如何? 為您的 AVS 啟用我的訂用帳戶嗎？**

您可以在[azurevmwaresales@microsoft.com](mailto:azurevmwaresales@microsoft.com)聯繫您的 Microsoft 帳戶代表，以啟用您的 AVS 服務訂用帳戶。 在您要啟用 AVS 服務的電子郵件中，提供您的訂用帳戶識別碼。 

**如何? 存取 AVS 入口網站嗎？**

您可以從 Azure 入口網站存取 AVS 入口網站。 如需詳細資訊，請參閱[從 Azure 入口網站存取 VMware 解決方案（AVS）入口網站](access-cloudsimple-portal.md)。

**如何? 增加了 AVS 私人雲端的容量嗎？**

若要增加容量，請從 Azure 入口網站購買額外的節點，然後使用節點從 AVS 入口網站擴充您的 AVS 私用雲端。 您可以將其他節點新增至現有的 vSphere 叢集，或將它們新增至新的 vSphere 叢集中。 如需詳細資訊，請參閱[擴充 AVS 私用雲端](expand-private-cloud.md)。

**在維護期間，我的 AVS 私人雲端會發生什麼事？**

AVS 會在排定的維護間隔幾天前提供通知。 維護是以非干擾性的方式完成，以確保您的 AVS 私用雲端的可用性。 維護可以是下列類型：

* **AVS 基礎結構**。 AVS 基礎結構設計為高可用性。 在這種維護間隔中，會一次更新一個重複的元件，以避免任何服務中斷。 您可以繼續存取您的 AVS 私用雲端 vCenter、所有虛擬機器、來自您的 AVS 私人雲端的網際網路連線，以及內部部署或 Azure 的連接。
* **AVS 入口網站**。 在這種維護間隔中，可能會停用或無法存取 AVS 入口網站上的某些功能。 維護間隔之前的通知會包含進行維護時的功能限制詳細資料。

## <a name="connectivity"></a>連線能力

**我的 AVS 區域網路的連線選項有哪些？**

AVS 提供下列連線選項，以連線到您的 AVS 區域網路。 可以同時使用多個選項。

* **從您的內部部署資料中心到 AVS 區域網路的 ExpressRoute**連線。 這是一種高速、低延遲、安全的私人連線，可使用全球觸達您的內部部署 ExpressRoute 線路與您的 AVS ExpressRoute 線路。 如需設定連線的指示，請參閱[使用 ExpressRoute 從內部部署連接到 AVS](on-premises-connection.md)。
* **從您的 Azure 虛擬網路到您的 AVS 區域網路的 ExpressRoute**連線。 這是一種高速、低延遲的安全私人連線，其使用虛擬網路閘道，將 Azure 上的虛擬網路與您的 AVS ExpressRoute 線路橋接。 如需設定連線的指示，請參閱[使用 ExpressRoute 將您的 AVS 私用雲端環境連接到 Azure 虛擬網路](azure-expressroute-connection.md)。
* **從內部部署資料中心到您的 AVS 區域網路的站對站 VPN**連線。 這是從您的內部部署 VPN 裝置到您的 AVS 私人雲端區域的安全虛擬私人網路。 如需詳細資訊，請參閱[在 AVS 網路上設定 VPN 閘道](vpn-gateway.md)。

**如何? 連接到 AVS 私人雲端？**

您可以在 AVS 入口網站中查看您的 AVS 私人雲端詳細資料。 若要連接到與您的 AVS 私人雲端對應的 vCenter，請先確認已使用站對站 VPN、點對站 VPN 或 ExpressRoute 來建立網路連線。 然後，從 Azure 入口網站啟動 AVS 入口網站，然後按一下首頁或 AVS 私人雲端詳細資料頁面上的 [**啟動 VSphere 用戶端**]。

**ExpressRoute 線路的優點為何？**

Azure ExpressRoute 線路是高速、低延遲、安全的連線。 AVS 為每個客戶提供每個區域專用的 ExpressRoute 線路。 使用此線路，您可以從內部部署或 Azure 訂用帳戶建立安全的連線。

**連接到 AVS 的網路成本為何？所有的輸出費用都適用于 AVS 和 Azure，或跨區域？**

網路輸出沒有任何 AVS 費用。 Azure 標準費率適用于來自虛擬網路或內部部署 ExpressRoute 線路的任何輸出流量。

## <a name="networking"></a>網路

**我的 AVS 私人雲端有哪些網路功能可供使用？**

您可以布建 Vlan （及其子網）和防火牆資料表，並指派對應至在您的 AVS 私人雲端中執行之虛擬機器的公用 IP 位址。 如需網路功能的詳細資訊，請參閱[vlan 和子網總覽](cloudsimple-vlans-subnets.md)、[防火牆資料表總覽](cloudsimple-firewall-tables.md)和[公用 IP 位址總覽](cloudsimple-public-ip-address.md)。

**如何? 在我的 AVS 私人雲端中為我的應用程式設定不同的子網嗎？**

您可以從 AVS 入口網站，在您的 AVS 私人雲端上建立 Vlan。 建立 VLAN 之後，您可以使用 VLAN 在您的 AVS 私用雲端 vCenter 上建立分散式通訊埠群組，並建立連線到分散式埠群組的虛擬機器。 您可以啟用 VLAN/子網的防火牆資料表，並定義防火牆規則來保護網路流量。

**我的 AVS 私人雲端有哪些防火牆設定？**

您可以設定北南部和東部-西部流量的規則。 這些規則是在防火牆資料表中定義。 防火牆資料表可以連接到您的 AVS 私人雲端上的 Vlan。 如需詳細資訊，請參閱[設定適用于 AVS 私人雲端的防火牆資料表和規則](firewall.md)。

**我可以將公用 IP 位址指派給我的 AVS 私人雲端環境中的 Vm 嗎？**

在 AVS 入口網站中，您可以配置新的公用 IP 位址，並將它與虛擬機器或設備的私人 IP 位址產生關聯。 您也可以建立新的防火牆規則，或套用現有的防火牆規則，以允許來自入口網站中特定埠和 IP 位址的流量。 如需詳細資訊，請參閱[為 AVS 私人雲端環境配置公用 IP 位址](public-ips.md)。

## <a name="security"></a>安全性

**我的 AVS 有哪些安全性選項？**

AVS 提供下列安全性功能來保護您的 AVS 私用雲端環境：

* 待用**資料加密**。 您可以加密位於您的 AVS 私人雲端中 vSAN 儲存體上的待用資料。 vSAN 支援可在您的 Azure vNet 或內部部署環境中部署的外部金鑰管理伺服器。 如需詳細資訊，請參閱[為您的 AVS 私人雲端設定 vSAN 加密](vsan-encryption.md)。
* **網路安全性**。 使用適用于您的 AVS 私用雲端與網際網路、您的 AVS 私用雲端和內部部署環境，或在您的 AVS 私人雲端子網內的防火牆規則，控制網路流量流程。
* **安全的私用連接**。 系統會在您的內部部署網路與 Azure 訂用帳戶之間建立安全的私人連線。

## <a name="compute"></a>計算

**有何種主機可供使用？**

AVS 提供下列主機類型：

* **CS28 節點：** CPU： 2x 2.2 GHz，總計28核心，48 HT。  RAM： 256 GB。  儲存體： 1600 GB NVMe 快取，5760 GB 資料（全部-Flash）。 網路： 4x25Gbe NIC
* **CS36 節點：** CPU 2x 2.3 GHz，總36核心，72 HT。  RAM： 512 GB。  儲存體： 3200 GB NVMe 快取 11520 GB 資料（全部-Flash）。  網路： 4x25Gbe NIC
* **CS36m-節點：** CPU 2x 2.3 GHz，總36核心，72 HT。  RAM： 576 GB。  儲存體： 3200 GB NVMe 快取 13360 GB 資料（全部-Flash）。  網路： 4x25Gbe NIC

**如何處理任何硬體失敗？**

所有的 AVS 基礎結構會持續受到 AVS 平臺和我們的服務營運小組監視。 如果偵測到硬體失敗，則會將新的節點新增至您的 AVS 私人雲端，並移除失敗的節點。

## <a name="storage"></a>儲存體

**AVS 私人雲端支援哪種類型的儲存體？**

AVS 提供所有的每一部 AVS 私人雲端的所有 flash VMware vSAN 儲存空間。 每個 vSphere 都是使用自己的 vSAN 資料存放區所建立。 如需詳細資訊，請參閱[AVS 私用雲端 VMware 元件-vSAN 儲存體](vmware-components.md#vsan-storage)。

**是否支援加密資料？**
可以。 您可以在您的 AVS 私人雲端上設定 vSAN 儲存體，以使用部署在內部部署或 Azure 上的金鑰管理伺服器（KMS）來加密 vSAN 上儲存的資料。

**如何處理失敗的磁片？**

AVS 會持續監視 AVS 私人雲端的所有硬體元件。 如果偵測到磁片失敗或磁片識別為失敗（根據啟發學習法），則會將新的節點自動新增至 AVS 私人雲端。 具有失敗或失敗磁片的節點會從 AVS 私人雲端移除。

## <a name="vmware"></a>VMware

**如何? 在內部部署環境中執行應用程式和資料的大規模上傳或遷移？**

AVS 提供原生 VMware vSphere 解決方案。 所有適用于大量資料移轉的 VMware 工具都可以搭配您的 AVS 私用雲端使用。 選項包括：

* 用於大量遷移資料的 VMware HCX。
* 使用從內部部署到 AVS 的儲存體 vMotion 來進行資料的冷遷移。

**我可以安裝任何 VMware 工具嗎？**

AVS 提供原生 VMware vSphere 解決方案。 所有用來管理內部部署 vSphere 環境的 VMware 工具都可以在 AVS 上使用。 AVS 支援自備授權（BYOL）模型來安裝 VMware 工具。

**更新和升級的管理方式為何？**

AVS 以無干擾性的方式管理及更新您的 AVS 私用雲端的所有基礎結構元件。 由 VMware 或基礎結構廠商發行的所有更新和安全性修補程式，都會在 AVS 合格後立即進行更新。

AVS 不會對安裝在 AVS 私用雲端上的應用程式執行升級或更新。

## <a name="azure-integration"></a>Azure 整合

**支援哪些 Azure 服務？**

AVS 提供 azure ExpressRoute 連線給您在 Azure 上的訂用帳戶。 在您的訂用帳戶中執行的所有服務都可以連接到您的 AVS 私人雲端。 例如：

* **Azure Active Directory** ，做為您的 AVS vCenter 的身分識別來源。
* **Azure 儲存體**，用來儲存您的 AVS 私人雲端中的備份、影像和其他資料。
* **混合式應用程式**，具有跨越公用和 AVS 私人雲端的應用程式架構。 例如，您可以在 Azure 中建立 webservers，以存取您的 AVS 私人雲端上的應用程式和資料庫伺服器。
* **Azure 監視器**和**azure 資訊安全中心**，適用于在 VMware 上執行的工作負載支援記錄、效能計量和安全性管理。

**如何? 將我的 VMware 租使用者對應到 Azure 嗎？**

AVS 提供獨特的功能，從 Azure 入口網站管理您在 AVS 私用雲端上的 VMware Vm。 使用所需資源條件約束設定的 vCenter 資源集區，可由您的全域管理員對應至您的訂用帳戶。 

**我可以使用 Azure 取得哪些授權權益？**

使用 AVS，您可以利用 Azure 混合式使用權益，並省下高達90% 的授權。 這項權益可保留您對 Microsoft 授權的投資，並降低您與其他雲端解決方案相關的 TCO。 您也可以取得 Windows Server 2008 和 Microsoft SQL Server 2008 的擴充安全性更新。 「自備授權」（BYOL）模型可協助您為常見的應用程式（例如 Veeam 和 Zerto）維持低成本。 
