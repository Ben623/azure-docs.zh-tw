---
title: 常見的安全性屬性適用於 Azure Resource Manager
description: 常見的安全性屬性，來評估 Azure Resource Manager 的檢查清單
services: azure-resource-manager
author: msmbaldwin
manager: barbkess
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 04/25/2019
ms.author: mbaldwin
ms.openlocfilehash: a771d4c2ae22b7bf149c13c80fe5286ef52a4545
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "66002264"
---
# <a name="security-attributes-for-azure-resource-manager"></a>適用於 Azure Resource Manager 的安全性屬性

這篇文章說明建置到 Azure Resource Manager 的安全性屬性。

[!INCLUDE [Security Attributes Header](../../includes/security-attributes-header.md)]

## <a name="preventative"></a>預防

| 安全性屬性 | 是/否 | 注意 |
|---|---|--|
| 待用加密：<ul><li>伺服器端加密</li><li>使用客戶管理的金鑰進行伺服器端加密</li><li>其他加密功能 (例如用戶端、一律加密等)</ul>| 是 |  |
| 傳輸中加密：<ul><li>Express Route 加密</li><li>在 VNet 加密</li><li>VNet-VNet 加密</ul>| 是 | HTTPS/TLS。 |
| 加密金鑰處理 （CMK、 BYOK）| N/A | Azure Resource Manager 會儲存任何客戶內容，只是控制資料。 |
| 資料行層級加密 (Azure Data Services)| 是 | |
| API 呼叫加密| 是 | |

## <a name="network-segmentation"></a>網路分割

| 安全性屬性 | 是/否 | 注意 |
|---|---|--|
| 服務端點支援| 否 | |
| VNet 插入支援| 是 | |
| 網路隔離，而且防火牆支援| 否 |  |
| 強制通道的支援| 否 |  |

## <a name="detection"></a>偵測

| 安全性屬性 | 是/否 | 注意|
|---|---|--|
| Azure 監視支援 （Log analytics、 App insights）| 否 | |

## <a name="identity-and-access-management"></a>身分識別和存取管理

| 安全性屬性 | 是/否 | 注意|
|---|---|--|
| Authentication| 是 | [Azure Active Directory](/azure/active-directory)基礎。|
| 授權| 是 | |


## <a name="audit-trail"></a>稽核線索

| 安全性屬性 | 是/否 | 注意|
|---|---|--|
| 控制和管理平面記錄與稽核| 是 | 活動記錄所有寫入您的資源; 上執行的作業 (PUT、 POST、 DELETE) 的公開請參閱[檢視活動記錄以稽核對資源的動作](resource-group-audit.md)。 |
| 資料平面記錄與稽核| N/A | |

## <a name="configuration-management"></a>設定管理

| 安全性屬性 | 是/否 | 注意|
|---|---|--|
| 組態管理支援 （版本設定的組態等）。| 是 |  |
