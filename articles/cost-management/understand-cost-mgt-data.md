---
title: 了解 Azure 成本管理資料 | Microsoft Docs
description: 本文將協助您深入了解 Azure 成本管理所包含的資料，及其處理、收集、顯示和關閉的頻率。
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 05/20/2019
ms.topic: conceptual
ms.service: cost-management
manager: micflan
ms.custom: ''
ms.openlocfilehash: f4c296aeefacc9516303ad75dd8b7d67325e38ee
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "65969067"
---
# <a name="understand-cost-management-data"></a>了解成本管理資料

本文將協助您深入了解 Azure 成本管理所包含的資料。 此外也說明處理、收集、顯示和關閉資料的頻率。 您須按月支付使用 Azure 的費用。 不過，您的 Azure 訂用帳戶類型會決定您的計費月份於何時結束。 「成本管理」接收使用量資料的頻率會隨著不同的因素而改變。 這類因素包括處理資料所需的時間，以及 Azure 服務向計費系統發出使用量資料的頻率。

成本的管理會包含所有的使用方式與購買項目，包括保留項目和 Enterprise 合約 (EA) 帳戶的第三方供應項目。 Microsoft 客戶協議 (MCA) 帳戶和隨用隨付訂用帳戶只會包含從 Azure 與 Marketplace 服務的使用方式。 支援和其他成本不包含在內。 成本估計，直到產生發票並不會納入信用額度。

## <a name="supported-microsoft-offers"></a>支援的 Microsoft 供應項目

下列資訊顯示 Azure 成本管理中目前支援的 [Microsoft Azure 供應項目](https://azure.microsoft.com/support/legal/offer-details/)。  Azure 供應項目是您 Azure 訂用帳戶的類型。

| Category  | **供應項目名稱** | **Quota ID** | **供應項目號碼** |
| --- | --- | --- | --- |
| **Azure Germany** | [Azure 德國預付型方案](https://azure.microsoft.com/offers/ms-azr-de-0003p)      | PayAsYouGo_2014-09-01 | MS-AZR-DE-0003P |
| **Azure Government** | Azure Government Enterprise                                                         | EnterpriseAgreement_2014-09-01 | MS-AZR-USGOV-0017P |
| **Enterprise 合約 (EA)** | Enterprise 開發/測試                                                        | MSDNDevTest_2014-09-01 | MS-AZR-0148P |
| **Enterprise 合約 (EA)** | [Microsoft Azure Enterprise](https://azure.microsoft.com/offers/enterprise-agreement-support-upgrade) | EnterpriseAgreement_2014-09-01 | MS-AZR-0017P |
| **Microsoft 客戶合約** | [Microsoft Azure Plan](https://azure.microsoft.com/offers/ms-azr-0017g) | EnterpriseAgreement_2014-09-01 | N/A |
| **Microsoft 客戶合約** | [Microsoft Azure 開發/測試的計劃](https://azure.microsoft.com/offers/ms-azr-0148g)  | MSDNDevTest_2014-09-01 | N/A |
| **Microsoft Developer Network (MSDN)** | [MSDN 平台](https://azure.microsoft.com/offers/ms-azr-0062p) | MSDN_2014-09-01 | MS-AZR-0062P |
| **隨用隨付** | [隨用隨付](https://azure.microsoft.com/offers/ms-azr-0003p)                       | PayAsYouGo_2014-09-01 | MS-AZR-0003P |
| **隨用隨付** | [隨用隨付開發/測試](https://azure.microsoft.com/offers/ms-azr-0023p)              | MSDNDevTest_2014-09-01 | MS-AZR-0023P |
| **隨用隨付** | [Microsoft 合作夥伴網路](https://azure.microsoft.com/offers/ms-azr-0025p)           | MPN_2014-09-01 | MS-AZR-0025P |
| **隨用隨付** | [免費試用版](https://azure.microsoft.com/offers/ms-azr-0044p)                          | FreeTrial_2014-09-01 | MS-AZR-0044P |
| **隨用隨付** | [Azure in Open](https://azure.microsoft.com/offers/ms-azr-0111p)                       | AzureInOpen_2014-09-01 | MS-AZR-0111P |
| **隨用隨付** | [Azure 學生版](https://azure.microsoft.com/offers/ms-azr-0170p)                  | AzureForStudents_2018-01-01 | MS-AZR-0170P |
| **隨用隨付** | Azure Pass                                                                             | AzurePass_2014-09-01 | MS-AZR-0120P、MS-AZR-0122P - MS-AZR-0125P、MS-AZR-0128P - MS-AZR-0130P |
| **Visual Studio** | [Visual Studio Enterprise – MPN](https://azure.microsoft.com/offers/ms-azr-0029p)      | MPN_2014-09-01 | MS-AZR-0029P |
| **Visual Studio** | [Visual Studio Professional](https://azure.microsoft.com/offers/ms-azr-0059p)          | MSDN_2014-09-01 | MS-AZR-0059P |
| **Visual Studio** | [Visual Studio Test Professional](https://azure.microsoft.com/offers/ms-azr-0060p)     | MSDNDevTest_2014-09-01 | MS-AZR-0060P |
| **Visual Studio** | [Visual Studio 企業版](https://azure.microsoft.com/offers/ms-azr-0063p)            | MSDN_2014-09-01 | MS-AZR-0063P |
| **Visual Studio** | [Visual Studio Enterprise：BizSpark](https://azure.microsoft.com/offers/ms-azr-0064p)  | MSDN_2014-09-01 | MS-AZR-0064P |

下表顯示不支援的供應項目。

| Category  | **供應項目名稱** | **Quota ID** | **供應項目號碼** |
| --- | --- | --- | --- |
| **雲端解決方案提供者 (CSP)** | Microsoft Azure                                    | CSP_2015-05-01 | MS-AZR-0145P |
| **雲端解決方案提供者 (CSP)** | Azure Government CSP                               | CSP_2015-05-01 | MS-AZR-USGOV-0145P |
| **雲端解決方案提供者 (CSP)** | CSP 中適用於 Microsoft Cloud Germany 的 Azure Germany   | CSP_2015-05-01 | MS-AZR-DE-0145P |
| **隨用隨付**                 | Azure for Students Starter | DreamSpark_2015-02-01 | MS-AZR-0144P |
| **隨用隨付**                 | [Microsoft Azure 贊助](https://azure.microsoft.com/offers/ms-azr-0036p/) | Sponsored_2016-01-01 | MS-AZR-0036P |
| **支援方案** | 標準支援                    | Default_2014-09-01 | MS-AZR-0041P |
| **支援方案** | 專業指導支援         | Default_2014-09-01 | MS-AZR-0042P |
| **支援方案** | 開發人員支援                   | Default_2014-09-01 | MS-AZR-0043P |
| **支援方案** | 德國支援方案                | Default_2014-09-01 | MS-AZR-DE-0043P |
| **支援方案** | Azure Government 標準支援   | Default_2014-09-01 | MS-AZR-USGOV-0041P |
| **支援方案** | Azure Government 專業指導支援 | Default_2014-09-01 | MS-AZR-USGOV-0042P |
| **支援方案** | Azure Government 開發人員支援  | Default_2014-09-01 | MS-AZR-USGOV-0043P |

使用隨用隨付、MSDN 和 Visual Studio 供應項目類別的客戶，自 2018/10/02 起可在「成本管理」中取得資料。 若要存取您的訂用帳戶在 2018 年 10 月 02 日之前的資料，您可以使用[Azure 帳戶中心](https://account.azure.com/subscriptions)若要下載您的使用量詳細資料的 CSV 檔案中，或者您可以使用[使用情況詳細資料 API](/rest/api/consumption/usagedetails)。

## <a name="determine-your-offer-type"></a>判斷您的供應項目類型
如果您未看到訂用帳戶的資料，而您想要確認訂用帳戶是否屬於支援的供應項目，您可以驗證您的訂用帳戶是否受支援。 若要驗證 Azure 訂用帳戶是否受支援，請登入 [Azure 入口網站](https://portal.azure.com)。 然後，在左側功能表窗格中選取 [所有服務]  。 在服務清單中，選取 [訂用帳戶]  。 在訂用帳戶清單功能表中，按一下您要確認的的訂用帳戶。 您的訂用帳戶會顯示在 [概觀] 索引標籤上，您會看到 [供應項目]  和 [供應項目識別碼]  。 下圖說明一個範例。

![顯示供應項目和供應項目識別碼的訂用帳戶概觀範例](./media/understand-cost-mgt-data/offer-and-offer-id.png)

## <a name="costs-included-in-cost-management"></a>包含在成本管理中的成本

下表顯示「成本管理」中包含或未包含的資料。 所有成本被都估計，直到產生發票。 顯示的成本不包含免費及預付點數。

**成本和使用量資料**

| **包含** | **未包含** |
| --- | --- |
| Azure 服務使用量<sup>1</sup> | 保留購買 – 如需詳細資訊，請參閱[適用於 Azure 保留自動化的 API](../billing/billing-reservation-apis.md)。 |
| Marketplace 供應項目使用方式<sup>2</sup> | Marketplace 購買 – 如需詳細資訊，請參閱[第三方服務費用](../billing/billing-understand-your-azure-marketplace-charges.md)。 |
|   | 支援費用 - 如需詳細資訊，請參閱[發票條款說明](../billing/billing-understand-your-invoice.md)。 |
|   | 稅金 - 如需詳細資訊，請參閱[發票條款說明](../billing/billing-understand-your-invoice.md)。 |
|   | 點數 - 如需詳細資訊，請參閱[發票條款說明](../billing/billing-understand-your-invoice.md)。 |

<sup>1</sup> Azure 服務使用量以保留和議定價格為基礎。

<sup>2</sup> marketplace 供應項目使用方式不適用於隨用隨付、 MSDN、 與 Visual Studio 提供這一次。

**Metadata**

| **包含** | **未包含** |
| --- | --- |
| 資源標記<sup>3</sup> | 資源群組標記 |

<sup>3</sup>資源標記使用方式，就會發出每個服務會套用，而且無法追溯至歷程記錄的使用方式。

## <a name="rated-usage-data-refresh-schedule"></a>評比使用量資料重新整理排程

成本與使用量資料可從 Azure 入口網站和[支援 API](index.yml) 的 [成本管理 + 計費] 中取得。 檢閱成本時，請記住以下幾點：

- 目前計費期間的預估費用每天會更新 6 次。
- 目前計費期間的預估費用可能隨著您產生更多使用量而變更。
- 每次更新將持續累積，包含前次更新的所有明細項目和資訊。
- Azure 會在計費期間結束後的 72 小時 (三個日曆天) 內終止或_關閉_目前計費期間。

下列範例說明計費期間的結束方式。

Enterprise 合約 (EA) 訂用帳戶 – 如果計費月份在 3 月 31 日結束，預估費用將會在 72 個小時內更新。 在此範例中，即 4 月 4 日午夜 (UTC)。

隨用隨付訂用帳戶 – 如果計費月份在 5 月 15 日結束，預估費用將可在 72 個小時內更新。 在此範例中，即 5 月 19 日午夜 (UTC)。

### <a name="rerated-data"></a>重新評估的資料

您是否使用[成本管理 Api](https://aka.ms/costmgmt/docs)，Power BI 或 Azure 入口網站來擷取資料，預期取得重新分級，並因此變更，直到關閉的發票目前計費週期的費用。

## <a name="usage-data-update-frequency-varies"></a>使用量資料更新頻率不相同

您產生的使用量資料在成本管理中的可用性取決於某些因素，包括：

- Azure 服務 (例如儲存體、計算、CDN 和 SQL) 發出使用量資料的頻率。
- 透過評比引擎和成本管理管線處理使用量資料所花費的時間。

某些服務會比其他服務更頻繁地發出使用量資料。 因此，您在「成本管理」中可能會看到某些服務的資料比其他發出資料頻率較低的服務更快出現。 服務的使用量通常需要 8-24 小時才會出現在「成本管理」中。 請記住，在尚未結算的月份中，該資料會隨著您產生更多使用量而重新整理，因為更新會持續累積。

## <a name="historical-data-might-not-match-invoice"></a>歷程記錄資料可能不符合發票

歷程記錄資料點數為基礎和預先付費供應項目可能不符合您的發票。 Azure 信用額度與套用到發票的進階的付款，可以有一些 Azure 隨用隨付、 MSDN 及 Visual Studio 供應項目。 不過，在成本管理中所顯示的歷程記錄資料根據您的預估的耗用量費用只。 成本的管理歷程記錄資料不包含付款與信用額度。 如此一來，使用下列供應項目所顯示的歷程記錄資料可能不符合完全與您的發票。

-   MS-AZR-0029P
-   MS-AZR-0064P
-   MS-AZR-0170P
-   MS-AZR-0062P
-   MS-AZR-0123P
-   MS-AZR-0129P
-   MS-AZR-0044P
-   MS-AZR-0128P
-   MS-AZR-0120P
-   MS-AZR-0125P
-   MS-AZR-0059P
-   MS-AZR-0063P
-   MS-AZR-0060P
-   MS-AZR-0111P

## <a name="see-also"></a>請參閱

- 如果您尚未完成成本管理的第一個快速入門，請在[開始分析成本](quick-acm-cost-analysis.md)閱讀它。
