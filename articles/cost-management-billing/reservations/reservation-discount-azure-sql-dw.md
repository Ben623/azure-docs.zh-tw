---
title: 保留折扣如何套用至 Azure SQL 資料倉儲 | Microsoft Docs
description: 了解保留折扣如何套用至 Azure SQL 資料倉儲，以協助您節省成本。
services: billing
author: yashesvi
manager: yashar
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 01/21/2020
ms.author: banders
ms.openlocfilehash: 24a673dc7949d5ce05aa1a701cc8b939a766fd99
ms.sourcegitcommit: a9b1f7d5111cb07e3462973eb607ff1e512bc407
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/22/2020
ms.locfileid: "76313921"
---
# <a name="how-reservation-discounts-apply-to-azure-sql-data-warehouse"></a>保留折扣如何套用至 Azure SQL 資料倉儲

購買 Azure SQL 資料倉儲保留容量之後，保留折扣就會自動套用至該區域中存在的資料倉儲。 保留折扣會套用至 SQL 資料倉儲 cDWU 計量所發出的使用量。 儲存體和網路則會以隨用隨付費率計費。

## <a name="reservation-discount-application"></a>保留折扣的套用

SQL 資料倉儲保留容量折扣會套用至執行中的倉儲 (以小時計算)。 如果您所部署的倉儲未達一小時，則那一小時的保留容量便浪費掉了。 未用完的保留容量無法延續。

購買之後，您購買的保留會與在任何時間點執行倉儲所發出的 SQL 資料倉儲使用量進行比對。 如果您關閉某些倉儲，則保留折扣會自動套用至任何其他相符的倉儲。

針對未執行滿一小時的倉儲，保留會自動套用至該小時內的其他相符執行個體。

## <a name="discount-examples"></a>折扣範例

下列範例會根據部署來顯示 SQL 資料倉儲保留容量折扣的套用方式。

- **範例 1**：購買5個單位的 100 cDWU 保留容量。 您執行一小時的 DW1500c SQL 資料倉儲執行個體。 在此案例中，系統會發出 15 個單位的 100 cDWU 使用量。 保留折扣會套用至您已使用的 5 個單位。 針對您已使用的其餘 10 個單位 100 cDWU 使用量，您會以隨用隨付費率來支付費用。 換句話說，部分涵蓋範圍適用于多個保留。

- **範例 2**：您購買5個單位的 100 cDWU 保留容量。 您執行兩個 DW100c SQL 資料倉儲執行個體，為期一小時。 在此案例中，系統會針對 1 個單位的 100 cDWU 使用量發出兩個使用量事件。 這兩個使用量事件都會獲得保留容量折扣。 其餘 3 個單位的 100 cDWU 保留容量則會浪費掉，而無法延續到未來使用。 換句話說，單一保留區可以符合多個 SQL 資料倉儲實例。

- **範例 3**：您購買1個100單位的 cDWU 保留容量。 您執行兩個 DW100c SQL 資料倉儲執行個體。 每個都執行 30 分鐘。 在此案例中，這兩個使用量事件都會獲得保留容量折扣。 不會以隨用隨付費率來對任何使用量收取費用。

## <a name="need-help-contact-us"></a>需要協助嗎？ 連絡我們

- 如果您有問題或需要協助，請[建立支援要求](https://go.microsoft.com/fwlink/?linkid=2083458)。

## <a name="next-steps"></a>後續步驟

若要深入了解 Azure 保留項目，請參閱下列文章：

- [什麼是 Azure 保留項目？](save-compute-costs-reservations.md)
- [檢視保留交易](view-reservations.md)
- [透過 API 取得保留交易和使用率](reservation-apis.md)
- [管理保留](manage-reserved-vm-instance.md)
