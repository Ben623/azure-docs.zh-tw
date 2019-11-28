---
title: 使用 Azure 保留容量來節省 SQL 資料倉儲費用的成本
description: 了解如何使用保留容量來節省 SQL 資料倉儲費用的成本以省下資金。
services: billing
author: yashesvi
manager: yashar
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 08/29/2019
ms.author: banders
ms.openlocfilehash: 75e008a6a19baacec40a809530e3a1f624a33f2b
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/20/2019
ms.locfileid: "74223613"
---
# <a name="save-costs-for-sql-data-warehouse-charges-with-reserved-capacity"></a>使用保留容量來節省 SQL 資料倉儲費用的成本

您可以藉由承諾會在一或三年期間內購買保留來獲得 cDWU 使用量，以節省 Azure SQL 資料倉儲的費用。 若要購買 SQL 資料倉儲保留容量，您需要選擇 Azure 區域和期限。 接著，將 SQL 資料倉儲 SKU 新增到您的購物車，並選擇您想要購買的 cDWU 單位數量。

當您購買保留項目時，與保留屬性相符的 SQL 資料倉儲使用量就不會再以隨用隨付費率收費。

保留項目並未涵蓋與 SQL 資料倉儲使用量相關的儲存或網路費用。

當保留的容量到期時，SQL 資料倉儲執行個體會繼續執行，但會按隨用隨附費率收費。 保留不會自動更新。

如需定價資訊，請參閱 [SQL 資料倉儲保留容量供應項目](https://azure.microsoft.com/pricing/details/sql-data-warehouse/gen2/)。

您可以在 [Azure 入口網站](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade)購買 Azure SQL 資料倉儲保留容量。 保留的付款方式可為[預先付款或每月付款](billing-monthly-payments-reservations.md)。 若要購買保留容量：

- 您必須有至少一個企業或隨用隨付訂用帳戶的擁有者角色。
- 針對企業訂用帳戶，必須在 [EA 入口網站](https://ea.azure.com/)中啟用 [新增保留執行個體]  選項。 如果該設定已停用，則您必須是 EA 系統管理員。
- 若為雲端解決方案提供者 (CSP) 方案，則只有系統管理員代理人或銷售人員可以購買 SQL 資料倉儲保留容量。

如需如何向企業客戶和隨用隨付客戶收取購買保留費用的詳細資訊，請參閱[了解 Enterprise 註冊的 Azure 保留使用量](billing-understand-reserved-instance-usage-ea.md)與[了解隨用隨付訂用帳戶的 Azure 保留使用量](billing-understand-reserved-instance-usage.md)。

## <a name="choose-the-right-size-before-purchase"></a>先選擇正確大小再購買

SQL 資料倉儲保留大小應該以您取用的計算資料倉儲單位 (cDWU) 總計為基礎。 購買會以每次增量 100 cDWU 來進行。

例如，假設 SQL 資料倉儲的總取用量是 DW3000c。 您想要為其所有取用量購買保留容量。 因此，您應該購買 30 個單位的 cDWU 保留容量。

## <a name="buy-sql-data-warehouse-reserved-capacity"></a>購買 SQL 資料倉儲保留容量

1. 登入 [Azure 入口網站](https://portal.azure.com/)。
2. 選取 [所有服務]   > [保留]  。
3. 選取一個訂用帳戶。 使用 [訂用帳戶] 清單來選擇用來支付保留容量的訂用帳戶。 訂用帳戶的付款方式為收取保留容量的費用。 訂用帳戶類型必須是 Enterprise 合約 (供應項目號碼：MS-AZR-0017P 或 MS-AZR-0148P) 或隨用隨付 (供應項目號碼：MS-AZR-0003P 或 MS-AZR-0023P)。
   - 針對企業訂用帳戶，費用會從註冊的承諾用量金額餘額扣除或作為超額部分收費。
   - 針對隨用隨付訂用帳戶，費用是透過訂用帳戶的信用卡或發票付款方式收取。
4. 選取範圍。 使用 [範圍] 清單來選擇訂用帳戶範圍。
   - **單一資源群組範圍** - 只會將保留折扣套用至所選資源群組中的相符資源。
   - **單一訂用帳戶範圍** — 會將保留折扣套用至所選訂用帳戶中的相符資源。
   - **共用範圍** — 會將保留折扣套用至計費內容中合格訂用帳戶的相符資源。 針對 Enterprise 合約客戶，計費內容為註冊。 針對使用隨用隨付費率的個別訂用帳戶，計費範圍是帳戶管理員所建立的所有合格訂用帳戶。
   - 針對企業客戶，計費內容為 EA 註冊。
   - 針對隨用隨付客戶，共用範圍是帳戶系統管理員所建立的所有隨用隨付訂用帳戶。
5. 選取 [區域] 以選擇保留容量涵蓋的 Azure 區域。
6. 選擇數量。 輸入您想要購買的 100 個資料倉儲單位 (cDWU) 數量。    
   例如，數量為 30 會每小時提供您 3,000 個 cDWU 的保留容量。
7. 在 [成本]  區段中，檢閱 SQL 資料倉儲保留容量保留成本。
8. 選取 [購買]  。
9. 選取 [檢視此保留]  以查看購買狀態。

## <a name="cancel-exchange-or-refund-reservations"></a>取消、交換保留或進行退款

您可以取消、交換保留或進行退費，但有某些限制。 如需詳細資訊，請參閱 [Azure 保留的自助式交換和退款](billing-azure-reservations-self-service-exchange-and-refund.md)。

保留折扣會自動套用至符合 SQL 資料倉儲保留容量範圍和區域的 SQL 資料倉儲執行個體數目。 您可以透過 [Azure 入口網站](https://portal.azure.com/)、PowerShell、CLI 或 API 來更新 SQL 資料倉儲保留容量的範圍。

## <a name="need-help-contact-us"></a>需要協助嗎？ 與我們連絡

如果您有問題或需要協助，請[建立支援要求](https://portal.azure.com/)。

## <a name="next-steps"></a>後續步驟

- 若要深入了解保留折扣會如何套用至 Azure SQL 資料倉儲，請參閱[保留折扣如何套用至 Azure SQL 資料倉儲](billing-prepay-sql-data-warehouse-charges-with-reserved-capacity.md)。

- 若要深入了解 Azure 保留項目，請參閱下列文章：
  - [什麼是 Azure 保留項目？](billing-save-compute-costs-reservations.md)
  - [管理 Azure 保留項目](billing-manage-reserved-vm-instance.md)
  - [了解 Azure 保留折扣](billing-understand-reservation-charges.md)
  - [了解隨用隨付訂用帳戶的保留使用量](billing-understand-reserved-instance-usage.md)
  - [了解 Enterprise 註冊的保留項目使用量](billing-understand-reserved-instance-usage-ea.md)
