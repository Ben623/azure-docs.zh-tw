---
title: Azure 防火牆的基礎結構 FQDN
description: 深入了解 Azure 防火牆中的基礎結構 FQDN
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 9/24/2018
ms.author: victorh
ms.openlocfilehash: 34201a0eb4139de64261f77f285096a2aa2dd3aa
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "61066316"
---
# <a name="infrastructure-fqdns"></a>基礎結構 FQDN

Azure 防火牆包含內建的規則集合，適用於依預設允許的基礎結構 FQDN。 這些 FQDN 特定於平台，且無法用於其他用途。 

下列服務包含在內建規則集合中：

- 儲存體平台映像存放庫 (PIR) 的計算存取權
- 受控磁碟狀態儲存體存取權
- Azure 診斷和記錄 (MDS)
- Azure Active Directory

## <a name="overriding"></a>覆寫 

您可以建立最後處理的「全部拒絕」應用程式規則集合，來覆寫此內建的基礎結構規則集合。 它一律會在基礎結構規則集合之前進行處理。 依預設會拒絕不在基礎結構規則集合中的任何項目。

## <a name="next-steps"></a>後續步驟

- 深入了解如何[部署和設定 Azure 防火牆](tutorial-firewall-deploy-portal.md)。