---
title: Azure 儲存體的安全性控制項
description: 評估 Azure 儲存體的安全性控制檢查清單
services: storage
documentationcenter: ''
author: msmbaldwin
manager: rkarlin
ms.service: storage
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: mbaldwin
ms.openlocfilehash: 8cb14c19e8816d53c7d9385563f916bee5d4a6af
ms.sourcegitcommit: 7c5a2a3068e5330b77f3c6738d6de1e03d3c3b7d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/11/2019
ms.locfileid: "70886572"
---
# <a name="security-controls-for-azure-storage"></a>Azure 儲存體的安全性控制項

本文記載內建在 Azure 儲存體中的安全性控制項。 

[!INCLUDE [Security controls Header](../../../includes/security-controls-header.md)]

## <a name="data-protection"></a>資料保護

| 安全性控制 | 是/否 | 注意 |
|---|---|--|
| 待用的伺服器端加密：Microsoft 管理的金鑰 | 是 |  |
| 待用的伺服器端加密：客戶管理的金鑰（BYOK） | 是 | 請參閱[儲存體服務加密在 Azure Key Vault 中使用客戶管理的金鑰](storage-service-encryption-customer-managed-keys.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)。|
| 資料行層級加密 (Azure 資料服務)| N/A |  |
| 傳輸中的加密（例如 ExpressRoute 加密、VNet 加密中和 VNet VNet 加密）| 是 | 支援標準 HTTPS/TLS 機制。  使用者也可以在將資料傳送至服務之前, 將其加密。 |
| API 呼叫加密| 是 |  |

## <a name="network"></a>網路

| 安全性控制 | 是/否 | 注意 |
|---|---|--|
| 服務端點支援| 是 |  |
| VNet 插入支援| N/A |  |
| 網路隔離和防火牆支援| 是 | |
| 強制通道支援| N/A |  |

## <a name="monitoring--logging"></a>監視 & 記錄

| 安全性控制 | 是/否 | 注意|
|---|---|--|
| Azure 監視支援 (Log analytics、App insights 等)| 是 | 現在已可使用 Azure 監視器計量, 記錄開始預覽 |
| 控制和管理平面記錄和審核 | 是 | Azure Resource Manager 活動記錄 |
| 資料平面記錄和審核| 是 | 服務診斷記錄, 以及開始預覽 Azure 監視器記錄  |

## <a name="identity"></a>身分識別

| 安全性控制 | 是/否 | 注意|
|---|---|--|
| 驗證| 是 | Azure Active Directory、共用金鑰、共用存取權杖。 |
| Authorization| 是 | 透過 RBAC、POSIX Acl 和 SAS 權杖支援授權 |

## <a name="configuration-management"></a>設定管理

| 安全性控制 | 是/否 | 注意|
|---|---|--|
| 設定管理支援 (設定的版本設定等)| 是 | 透過 Azure Resource Manager Api 支援資源提供者版本設定 |

## <a name="next-steps"></a>後續步驟

- 深入瞭解[跨 Azure 服務的內建安全性控制](../../security/fundamentals/security-controls.md)。