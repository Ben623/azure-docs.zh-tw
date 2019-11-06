---
title: 包含檔案
description: 包含檔案
services: azure-policy
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 09/18/2018
ms.author: dacoulte
ms.custom: include file
ms.openlocfilehash: dabadf3a4175947f09ba20b0e644f1d2b485ee38
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/04/2019
ms.locfileid: "73590516"
---
### <a name="sql-servers"></a>SQL Server

|  |  |
|---------|---------|
| [在沒有 Azure Active Directory 系統管理員時稽核](../articles/governance/policy/samples/audit-no-aad-admin.md) | 在 SQL Server 沒有指派的 Azure Active Directory 系統管理員時稽核。 |
| [稽核伺服器層級威脅偵測設定](../articles/governance/policy/samples/audit-sql-server-threat-detection-setting.md) | 稽核 SQL 資料庫安全性警示原則是否未設定成指定的狀態。 您需指定一個指出已啟用或停用威脅偵測的值。  |
| [稽核 SQL Server 稽核設定](../articles/governance/policy/samples/sql-server-audit.md) | 根據是否已啟用稽核設定，來稽核 SQL 伺服器。 |
| [稽核 SQL Server 層級稽核設定](../articles/governance/policy/samples/audit-sql-server-audit-setting.md) | 稽核 SQL Server 稽核設定是否與指定的設定不符。 您需指定一個指出應啟用或停用稽核設定的值。 |