---
title: 什麼是 Azure 可用性區域？ | Microsoft Docs
description: 若要在 Azure 中建立高度可用且具有恢復功能的應用程式，可用性區域可提供可讓您用來執行資源的實體不同位置。
services: ''
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: ''
ms.assetid: ''
ms.service: azure
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/20/2019
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 048a4dd9f35bd62886876f98bcbc5e6267cca6c8
ms.sourcegitcommit: a874064e903f845d755abffdb5eac4868b390de7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/24/2019
ms.locfileid: "68442224"
---
# <a name="what-are-availability-zones-in-azure"></a>什麼是 Azure 中的可用性區域？
「可用性區域」是高可用性供應項目，可保護您的應用程式和資料不受資料中心故障影響。 「可用性區域」是 Azure 地區內獨特的實體位置。 每個區域皆由一或多個配備獨立電力、冷卻系統及網路的資料中心所組成。 若要確保復原能力，在所有已啟用的地區中都至少要有三個個別的區域。 地區內「可用性區域」的實體區隔可保護應用程式和資料不受資料中心故障影響。 區域備援服務會將應用程式和資料複寫至所有「可用性區域」，以防出現單一失敗點。 使用「可用性區域」時，Azure 可提供業界最佳的 99.99% VM 執行時間 SLA。 完整 [Azure SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/) 說明保證的 Azure 整體可用性。

Azure 區域中的可用性區域是由容錯網域和更新網域組成。 例如，如果您在 Azure 區域中建立橫跨三個區域的三個 (或更多) VM，您的 VM 會有效地分散到三個容錯網域和三個更新網域。 Azure 平台會從更新網域中辨識此分佈，以確定不會同時更新不同區域中的 VM。

藉由將運算、儲存體、網路及資料資源共置於某個區域內並複寫至其他區域，即可讓您的應用程式架構內建高可用性。 支援「可用性區域」的 Azure 服務分成兩個類別：

- **區域服務** – 您需將資源釘選至特定區域 (例如虛擬機器、受控磁碟、IP 位址) 或
- **區域備援服務** – 平台會自動跨區域複寫 (例如區域備援儲存體、SQL Database)。

若要在 Azure 上達到全面性的商務持續性，請使用「可用性區域」與 Azure 地區配對的組合來建置您的應用程式架構。 您可以使用 Azure 地區內的「可用性區域」來同步複寫應用程式和資料以提供高可用性，並以非同步方式跨 Azure 地區複寫以提供災害復原保護。
 
![地區中細分至一個區域的概念性檢視](./media/az-overview/az-graphic-two.png)

## <a name="services-support-by-region"></a>依區域的服務支援

支援可用性區域的 Azure 服務和區域的組合包括:


|                                 |美洲 |              |           |           | 歐洲 |              |          |              | 亞太地區 |                 |
|----------------------------|----------|----------|---------|---------|--------------|------------|--------|----------|----------|-------------|
|          |美國中部|East US|美國東部 2|美國西部 2|法國中部|北歐|英國南部|西歐|日本東部|東南亞|
| **計算**                         |            |              |           |           |                |              |          |             |            |                |
| Linux 虛擬機器          | &#10003;   | &#10003;     | &#10003;  | &#10003;  | &#10003;       | &#10003;     | &#10003; | &#10003;    | &#10003;   | &#10003;       |
| Windows 虛擬機器        | &#10003;   | &#10003;     | &#10003;  | &#10003;  | &#10003;       | &#10003;     | &#10003; | &#10003;    | &#10003;   | &#10003;       |
| 虛擬機器擴展集      | &#10003;   | &#10003;     | &#10003;  | &#10003;  | &#10003;       | &#10003;     | &#10003; | &#10003;    | &#10003;   | &#10003;       |
| **儲存體**   |            |              |           |           |                |              |          |             |            |                |
| 受控磁碟                   | &#10003;   | &#10003;     | &#10003;  | &#10003;  | &#10003;       | &#10003;     | &#10003; | &#10003;    | &#10003;   | &#10003;       |
| 區域-多餘儲存體          | &#10003;   | &#10003;     | &#10003;  | &#10003;  | &#10003;       | &#10003;     | &#10003; | &#10003;    | &#10003;   | &#10003;       |
| **網路功能**                     |            |              |           |           |                |              |          |             |            |                |
| 標準 IP 位址        | &#10003;   | &#10003;     | &#10003;  | &#10003;  | &#10003;       | &#10003;     | &#10003; | &#10003;    | &#10003;   | &#10003;       |
| Standard Load Balancer     | &#10003;   | &#10003;     | &#10003;  | &#10003;  | &#10003;       | &#10003;     | &#10003; | &#10003;    | &#10003;   | &#10003;       |
| VPN 閘道   | &#10003;   |  &#10003;    | &#10003;  | &#10003;  | &#10003;  | &#10003;     |   &#10003;  | &#10003;    |  &#10003;   | &#10003;       |
| ExpressRoute 閘道   | &#10003;   |  &#10003;   | &#10003;  | &#10003;  | &#10003;       | &#10003;     |  &#10003;  | &#10003;    |   &#10003;  | &#10003;       |
| 應用程式閘道   | &#10003;   | &#10003;     | &#10003;  | &#10003;  | &#10003;       | &#10003;     |          | &#10003;    | &#10003;       | &#10003;       |
| Azure 防火牆           | &#10003;   | &#10003;     | &#10003;  | &#10003;  | &#10003;       | &#10003;     | &#10003; | &#10003;    |  &#10003;       | &#10003;       |
| **資料庫**                     |            |              |           |           |                |              |          |             |            |                |
| Azure 資料總管                   | &#10003;   | &#10003;     | &#10003;  | &#10003;  | &#10003;       | &#10003;     | &#10003; | &#10003;    | &#10003;        | &#10003;       |
| SQL 資料庫                    | &#10003;   | &#10003;     | &#10003;  | &#10003;  | &#10003;       | &#10003;     | &#10003; | &#10003;    |            | &#10003;       |
| Azure Cache for Redis           | &#10003;   | &#10003;     | &#10003;  | &#10003;  | &#10003;       | &#10003;     | &#10003; | &#10003;    |  &#10003;       | &#10003;       |
| Azure Cosmos DB                    | &#10003;   |  &#10003;  |  &#10003; |  |       |     | &#10003; |     |            | &#10003;       |
| **分析**                       |            |              |           |           |                |              |          |             |            |                |
| 事件中樞                      | &#10003;   |              | &#10003;  | &#10003;  | &#10003;       | &#10003;     |          | &#10003;    |            | &#10003;       |
| **整合**                     |            |              |           |           |                |              |          |             |            |                |
| 服務匯流排 (僅限進階層) | &#10003;   |              | &#10003;  | &#10003;  | &#10003;       | &#10003;     |          | &#10003;    |            | &#10003;       |



## <a name="services-resiliency"></a>服務復原
所有 Azure 管理服務的架構都可以從區域層級失敗中復原。 在失敗的範圍中, 區域內的一個或多個可用性區域失敗, 與整個區域失敗相比, 其失敗半徑較小。 Azure 可以從區域內或從另一個 Azure 區域中的管理服務區域層級失敗中復原。 Azure 會在區域內一次執行一個區域的重要維護, 以避免影響在某個區域內可用性區域部署之客戶資源的任何失敗。

## <a name="pricing"></a>定價
針對部署在「可用性區域」中的虛擬機器並不會產生額外費用。 有兩部 (或以上) VM 部署在 Azure 區域內兩個以上的「可用性區域」中時，即可提供 99.99% VM 執行時間 SLA。 在可用性區域之間將額外產生 VM 對 VM 的資料傳輸費用。 如需詳細資訊，請檢閱[頻寬定價](https://azure.microsoft.com/pricing/details/bandwidth/)頁面。


## <a name="get-started-with-availability-zones"></a>開始使用可用性區域
- [建立虛擬機器](../virtual-machines/windows/create-portal-availability-zone.md)
- [使用 PowerShell 來新增受控磁碟](../virtual-machines/windows/attach-disk-ps.md#add-an-empty-data-disk-to-a-virtual-machine)
- [建立區域備援虛擬機器擴展集](../virtual-machine-scale-sets/virtual-machine-scale-sets-use-availability-zones.md)
- [使用具有區域備援前端的標準 Load Balancer 來進行跨區域的 VM 負載平衡](../load-balancer/load-balancer-standard-public-zone-redundant-cli.md)
- [使用具有區域前端的標準 Load Balancer 來進行區域內的 VM 負載平衡](../load-balancer/load-balancer-standard-public-zonal-cli.md)
- [區域備援儲存體](../storage/common/storage-redundancy-zrs.md)
- [SQL Database](../sql-database/sql-database-high-availability.md#zone-redundant-configuration)
- [事件中樞地理災害復原](../event-hubs/event-hubs-geo-dr.md#availability-zones)
- [服務匯流排地理災害復原](../service-bus-messaging/service-bus-geo-dr.md#availability-zones)
- [建立區域備援虛擬網路閘道](../vpn-gateway/create-zone-redundant-vnet-gateway.md)
- [為 Azure Cosmos DB 新增區域多餘區域](../cosmos-db/high-availability.md#availability-zone-support)
- [消費者入門 Azure Cache for Redis 可用性區域](https://aka.ms/redis/az/getstarted)

## <a name="next-steps"></a>後續步驟
- [快速入門範本](https://aka.ms/azqs)
