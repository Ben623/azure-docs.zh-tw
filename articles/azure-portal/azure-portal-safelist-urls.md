---
title: Azure 入口網站 Url 的安全Microsoft Docs
description: 將這些 Url 新增至 proxy 伺服器略過，以與 Azure 入口網站及其服務進行通訊
services: azure-portal
keywords: ''
author: mgblythe
ms.author: mblythe
ms.date: 09/13/2019
ms.topic: conceptual
ms.service: azure-portal
manager: mtillman
ms.openlocfilehash: 3f81d41bc6d8ce07ea4e7b11c7c48f9b68d70466
ms.sourcegitcommit: a9b1f7d5111cb07e3462973eb607ff1e512bc407
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/22/2020
ms.locfileid: "76310558"
---
# <a name="safelist-the-azure-portal-urls-on-your-firewall-or-proxy-server"></a>將防火牆或 proxy 伺服器上的 Azure 入口網站 Url

若要在您的本機或廣域網路與 Azure 雲端之間取得良好的效能和連線，請設定內部部署安全性裝置略過 Azure 入口網站 Url 的安全性限制。 網路系統管理員通常會部署 proxy 伺服器、防火牆或其他裝置，以協助保護及控制使用者存取網際網路的方式。 不過，設計來保護使用者的規則有時可以封鎖或減緩合法的商務相關網際網路流量，包括您與 Azure 之間的通訊。 若要優化您的網路與 Azure 入口網站及其服務之間的連線，建議您將 Azure 入口網站的 Url 新增至您的安全。

## <a name="azure-portal-urls-for-proxy-bypass"></a>Azure 入口網站 proxy 略過的 Url

Azure 入口網站的安全的 URL 端點是您組織部署所在的 Azure 雲端特有的。 選取您的雲端，然後將 Url 清單新增至您的 proxy 伺服器或防火牆，以允許這些端點的網路流量略過限制。

#### <a name="public-cloudtabpublic-cloud"></a>[公用雲端](#tab/public-cloud)
```
*.aadcdn.microsoftonline-p.com
*.aka.ms
*.applicationinsights.io
*.azure.com
*.azure.net
*.azureafd.net
*.azure-api.net
*.azuredatalakestore.net
*.azureedge.net
*.loganalytics.io
*.microsoft.com
*.microsoftonline.com
*.microsoftonline-p.com
*.msauth.net
*.msftauth.net
*.trafficmanager.net
*.visualstudio.com
*.windows.net
*.windows-int.net
```

#### <a name="us-government-cloudtabus-government-cloud"></a>[美國政府雲端](#tab/us-government-cloud)
```
*.azure.us
*.loganalytics.us
*.microsoft.us
*.microsoftonline.us
*.msauth.net
*.usgovcloudapi.net
*.usgovtrafficmanager.net
*.windowsazure.us
```

#### <a name="china-government-cloudtabchina-government-cloud"></a>[中國政府雲端](#tab/china-government-cloud)
```
*.azure.cn
*.microsoft.cn
*.microsoftonline.cn
*.chinacloudapi.cn
*.trafficmanager.cn
*.chinacloudsites.cn
*.windowsazure.cn
```
---

> [!NOTE]
> 這些端點的流量會使用適用于 HTTP （80）和 HTTPS （443）的標準 TCP 埠。
>
>
## <a name="next-steps"></a>後續步驟

需要將 IP 位址加入至安全的名稱嗎？ 下載您雲端的 Microsoft Azure datacenter IP 範圍清單：

* [國際](https://www.microsoft.com/download/details.aspx?id=56519)
* [美國政府](https://www.microsoft.com/download/details.aspx?id=57063)
* [德國](https://www.microsoft.com/download/details.aspx?id=57064)
* [中國](https://www.microsoft.com/download/details.aspx?id=57062)

其他 Microsoft 服務會使用額外的 Url 和 IP 位址來進行連線。 若要將 Microsoft 365 服務的網路連線優化，請參閱[設定您的網路以進行 Office 365](/office365/enterprise/set-up-network-for-office-365)。
