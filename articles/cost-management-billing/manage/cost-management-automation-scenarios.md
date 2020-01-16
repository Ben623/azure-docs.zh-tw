---
title: Azure 計費和成本管理的自動化案例 | Microsoft Docs
description: 了解常見的計費和成本管理案例如何對應至不同的 API。
services: billing
documentationcenter: ''
author: bandersmsft
manager: dougeby
tags: billing
ms.service: cost-management-billing
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: billing
ms.date: 10/01/2019
ms.author: banders
ms.openlocfilehash: c9de7d5f7661e4083d3a2f5b53368616d0e6655a
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/15/2020
ms.locfileid: "75992812"
---
# <a name="automation-scenarios-for-billing-and-cost-management"></a>計費和成本管理的自動化案例

本文列出 Azure 計費與成本管理的常見案例。 文中會將案例對應至您可使用的 API。 對於每個案例與 API 對應，您可以找到 API 的摘要和它們提供的功能。

## <a name="common-scenarios"></a>一般狀況

您可以在數個案例中，使用計費和成本管理 API 來回應成本相關及使用量相關的問題。 以下是常見案例的概要：

- **發票對帳**： Microsoft 向我收取的金額是否正確？  我的帳單內容為何，我是否可以自行計算？

- **交叉收費**：現在我知道要支付多少費用，我的組織中誰需要支付費用？

- **成本優化**：我知道已支付多少費用。 如何才能充分利用花費在 Azure 上的每分每毫？

- **成本追蹤**：我想要查看我在一段時間內所花費的費用，以及使用 Azure。 趨勢為何？ 如何做得更好？

- **當月的 Azure 費用**：我目前當月的日期花費有多大？ 我是否需要對 Azure 費用及/或使用量進行任何變更？ 我的 Azure 使用量在當月何時最高？

- **警示**：如何設定以資源為基礎的耗用量或以貨幣為基礎的警示？

## <a name="scenario-to-api-mapping"></a>案例與 API 對應

|         API        | 發票對帳    | 交叉收費    | 成本最佳化    | 成本追蹤    | 月中費用    | 警示    |
|:---------------------------:|:-------------------------:|:----------------:|:--------------------:|:----------------:|:------------------:|:---------:|
| 預算                     |                           |                  |           X          |                  |                    |     X     |
| Marketplace 費用                |             X             |         X        |           X          |         X        |          X         |     X     |
| 價位表                 |             X             |         X        |           X          |         X        |          X         |           |
| 保留建議 |                           |                  |           X          |                  |                    |           |
| 保留詳細資料         |                           |                  |           X          |         X        |                    |           |
| 保留摘要       |                           |                  |           X          |         X        |                    |           |
| 使用量詳細資料               |             X             |         X        |           X          |         X        |          X         |     X     |
| 計費期間             |             X             |         X        |           X          |         X        |                    |           |
| 發票                    |             X             |         X        |           X          |         X        |                    |           |
| RateCard                    |             X             |                  |           X          |         X        |          X         |           |
| 未分級的使用量               |             X             |                  |           X          |                  |          X         |           |

> [!NOTE]
> 案例與 API 對應不包含企業使用量 API。 可能的話，請針對新的開發案例使用一般使用量 API。

## <a name="api-summaries"></a>API 摘要

### <a name="consumption"></a>消費
Web Direct 和企業客戶可以使用所有下列 API (除了註明的以外)：

-   [預算 API](https://docs.microsoft.com/rest/api/consumption/budgets) （*僅限企業客戶*）：建立資源、資源群組或計費計量的成本或使用量預算。 當您建立預算之後，您可以設定警示，以在您超出定義的預算閾值時通知您。 您也可以設定當到達預算量時會發生的動作。

-   [Marketplace 費用 API](https://docs.microsoft.com/rest/api/consumption/marketplaces)：取得所有 Azure Marketplace 資源（Azure 合作夥伴供應專案）的費用和使用量資料。 您可以使用此資料來加總所有 Marketplace 資源的成本，或調查特定資源的成本/使用量。

-   [價位表 API](https://docs.microsoft.com/rest/api/consumption/pricesheet) （*僅限企業客戶*）：取得所有計量的自訂定價。 企業可以搭配使用此資料與使用量詳細資料和 Marketplace 使用量資訊，並使用使用量和 Marketplace 資料來計算成本。

-   [保留建議 API](https://docs.microsoft.com/rest/api/consumption/reservationrecommendations)：取得購買保留的 VM 實例的建議。 協助您分析預期節省成本和購買數量的建議。 如需詳細資訊，請參閱[適用於 Azure 保留自動化的 API ](../reservations/reservation-apis.md)。

-   [保留詳細資料 API](https://docs.microsoft.com/rest/api/consumption/reservationsdetails)：請參閱先前購買之 VM 保留的資訊，例如保留的耗用量與使用的數量。 您可以查看每個 VM 層級的詳細資料。 如需詳細資訊，請參閱[適用於 Azure 保留自動化的 API ](../reservations/reservation-apis.md)。

-   [保留摘要 API](https://docs.microsoft.com/rest/api/consumption/reservationssummaries)：查看您的組織所購買 VM 保留的匯總資訊，例如保留的耗用量與匯總中使用的數量。 如需詳細資訊，請參閱[適用於 Azure 保留自動化的 API ](../reservations/reservation-apis.md)。

-   [使用量詳細資料 API](https://docs.microsoft.com/rest/api/consumption/usagedetails)：取得 Microsoft 所有 Azure 資源的費用和使用量資訊。 此資訊採用使用量詳細資料記錄格式，目前每天會針對每個計量發出一次。 您可以使用此資訊來加總所有資源的成本，或調查特定資源的成本/使用量。

-   [RATECARD API](/previous-versions/azure/reference/mt219005(v=azure.100))：如果您是 Web Direct 客戶，請取得計量費率。 然後，您可以使用傳回的資訊搭配其資源使用量資訊來手動計算預期的帳單。

-   未[分級的使用量 API](/previous-versions/azure/reference/mt219003(v=azure.100))：在 Azure 進行任何計量/收費之前，取得原始使用量資訊。

### <a name="billing"></a>計費
-   [計費週期 API](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-billing-periods)：決定要分析的計費期間，以及該期間的發票識別碼。 您可以搭配使用發票識別碼和發票 API。

-   [發票 API](/rest/api/billing/2019-10-01-preview/invoices)：取得以 PDF 格式計費期間的發票下載 URL。

### <a name="enterprise-consumption"></a>企業使用量
下列 API 僅適用於企業：

-   [餘額摘要 API](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-balance-summary)：取得餘額、新購買、Azure Marketplace 服務費用、調整和超額費用的每月摘要資訊。 您可以取得目前計費期間或任何過去期間的這項資訊。 企業可以使用此資料與手動計算之摘要費用比較。 此 API 不提供資源特定資訊或成本的彙總檢視。

-   [使用量詳細資料 API](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-usage-detail)：取得目前月份、特定計費期間或自訂日期期間的 Azure 使用量資訊（Microsoft 供應專案）。 企業可以使用此資料來根據速率和耗用量手動計算帳單。 企業可以使用部門/組織資訊，計算成本在組織中的來源。 此資料提供使用量/成本的資源特定檢視。

-   [Marketplace 商店費用 API](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-marketplace-storecharge)：取得目前月份、特定計費期間或自訂日期期間的 Azure 使用量（合作夥伴供應專案）的相關資訊。 企業可以使用此資料來根據速率和耗用量手動計算帳單。 企業可以使用部門/組織資訊，計算成本在組織中的來源。 此 API 提供使用量/成本的資源特定檢視。

-   [價位表 API](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-pricesheet)：針對指定註冊和計費期間的每個計量取得適用的費率。 您可以搭配使用此費率資訊與使用量詳細資料和 Marketplace 使用量資訊，來手動計算預期的帳單。

-   [計費週期 API](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-billing-periods)：取得計費週期清單。 此 API 也會提供一個屬性，指向與計費期間相關的四組企業 API 資料的 API 路由： BalanceSummary、UsageDetails、Marketplace 費用和 PriceSheet。

-   [保留實例建議 API](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation)：查看7天、30天或60天的虛擬機器使用量，並取得單一和共用購買建議。 您可以使用此 API 來分析預期的成本節省和建議的購買數量。 如需詳細資訊，請參閱[適用於 Azure 保留自動化的 API ](../reservations/reservation-apis.md)。

## <a name="frequently-asked-questions"></a>常見問題集

### <a name="whats-the-difference-between-the-enterprise-reporting-apis-and-the-consumption-apis-when-should-i-use-each"></a>企業報告 API 與使用量 API 之間有何差異？ 各自的使用時機為何？
這些 API 有一組類似的功能，並且可以回答同一組計費與成本管理空間相關問題。 但它們的目標對象不同：

- 企業報告 API 這類 API 適用於已經與 Microsoft 簽署 Enterprise 合約的客戶，該合約會授與他們存取協商承諾用量和自訂定價的權限。 這類 API 需要您可從[企業版入口網站](https://ea.azure.com)取得的金鑰。 如需這些 API 的說明，請參閱[適用於企業客戶的報告 API 概觀](enterprise-api.md)。

- 所有客戶皆可取得使用量 API，但有一些例外狀況。 如需詳細資訊，請參閱 [Azure 使用量 API 概觀](consumption-api-overview.md)和[ Azure 使用量 API 參考](https://docs.microsoft.com/rest/api/consumption/)。 我們建議將所提供的 API 作為最新開發案例的解決方案。

### <a name="whats-the-difference-between-the-usage-details-api-and-the-usage-api"></a>使用量詳細資料 API 與使用量 API 之間有何差異？
這些 API 基本上提供不同的資料：

- [使用量詳細資料 API](https://docs.microsoft.com/rest/api/consumption/usagedetails)可提供每個計量實例的 Azure 使用量和成本資訊。 提供的資料已通過 Azure 中的成本計量系統，而且已隨著其他可能的變更套用成本：

   - 因為使用預付承諾用量而造成的帳戶變更
   - 因為 Azure 發現使用量差異而造成的帳戶變更

- [使用量 API](/previous-versions/azure/reference/mt219003(v=azure.100)) \(英文\) 會在未經處理的 Azure 使用量資訊通過 Azure 中的成本計量系統之前提供該資訊。 此資料可能與在 Azure 費用計量系統之後看到的使用量或費用金額沒有任何相互關聯。

### <a name="whats-the-difference-between-the-invoice-api-and-the-usage-details-api"></a>發票 API 與使用量詳細資料 API 之間有何差異？
這些 API 可提供相同資料的不同檢視：

- [發票 API](/rest/api/billing/2019-10-01-preview/invoices) 僅適用於 Web Direct 客戶。 它根據每個計量類型的彙總收費，提供您帳單的每月彙總。

- [使用量詳細資料 API](https://docs.microsoft.com/rest/api/consumption/usagedetails) \(英文\) 提供每天的細微使用量/成本記錄檢視。 企業和 Web Direct 客戶都可以使用。

### <a name="whats-the-difference-between-the-price-sheet-api-and-the-ratecard-api"></a>價位表 API 與 RateCard API 之間有何差異？
這些 API 提供類似的資料集，但有不同的對象：

- [價位表 API](https://docs.microsoft.com/rest/api/consumption/pricesheet) 可提供已針對企業客戶協商的自訂定價。

- [RateCard API](/previous-versions/azure/reference/mt219005(v=azure.100)) \(英文\) 提供適用於 Web Direct 客戶的公開定價。

## <a name="next-steps"></a>後續步驟

- 如需使用 Azure API 以程式設計方式取得 Azure 使用量見解的資訊，請參閱 [Azure 使用量 API 概觀](consumption-api-overview.md)和 [Azure 計費 API 概觀](usage-rate-card-overview.md)。

- 若要比較包含每日詳細使用量檔案的發票與 Azure 入口網站中的成本管理報告，請參閱[了解 Microsoft Azure 帳單](../understand/review-individual-bill.md)。

- 如有問題或需要協助，請[建立支援要求](https://go.microsoft.com/fwlink/?linkid=2083458)。
