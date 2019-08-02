---
title: 探索 Azure SQL Database 受控執行個體內建防火牆 | Microsoft Docs
description: 了解如何驗證 Azure SQL Database 受控執行個體的內建防火牆保護。
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein, carlrab
ms.date: 12/04/2018
ms.openlocfilehash: c98b0fd5669140559b4840e157394c2e8c6086ae
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/26/2019
ms.locfileid: "68567433"
---
# <a name="verifying-the-managed-instance-built-in-firewall"></a>驗證受控執行個體的內建防火牆

受控執行個體的[必要輸入安全性規則](sql-database-managed-instance-connectivity-architecture.md#mandatory-inbound-security-rules)要求用來保護受控執行個體的網路安全性群組 (NSG) 上的**任何來源**均需開啟管理連接埠 9000、9003、1438、1440、1452。 雖然這些連接埠是於 NSG 層級開啟，不過會在網路層級受到內建防火牆保護。

## <a name="verify-firewall"></a>驗證防火牆

若要確認這些連接埠，請使用任何安全性掃描程式工具對這些連接埠進行測試。 下列螢幕擷取畫面顯示了使用其中一套工具的使用方式。

![驗證內建防火牆](./media/sql-database-managed-instance-management-endpoint/03_verify_firewall.png)

## <a name="next-steps"></a>後續步驟

如需受控執行個體和連線的詳細資訊，請參閱 [Azure SQL Database 受控執行個體連線架構](sql-database-managed-instance-connectivity-architecture.md)。