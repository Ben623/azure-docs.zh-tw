---
title: 包含檔案
description: 包含檔案
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: include
ms.date: 10/15/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: d31e30991056cc891e63347a2c88e7fc4caeab28
ms.sourcegitcommit: 9405aad7e39efbd8fef6d0a3c8988c6bf8de94eb
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/05/2019
ms.locfileid: "74875402"
---
### <a name="regions"></a>哪些區域可用？

[!INCLUDE [region](bastion-regions-include.md)]

### <a name="publicip"></a>我的虛擬機器需要使用公用 IP 嗎？

您不需要在與 Azure Bastion 服務連線的 Azure 虛擬機器上使用 公用 IP。 Bastion 服務將會透過虛擬網路中的虛擬機器私人 IP，開啟 RDP/SSH 工作階段/連線至您的虛擬機器。

### <a name="rdpssh"></a>我需要 RDP 或 SSH 用戶端嗎？

您不需要 RDP 或 SSH 用戶端，即可在 Azure 入口網站中對您的 Azure 虛擬機器進行 RDP/SSH 存取。 使用 [Azure 入口網站](https://portal.azure.com)可讓您直接在瀏覽器中對虛擬機器進行 RDP/SSH 存取。

### <a name="agent"></a>我需要在 Azure 虛擬機器中執行代理程式嗎？

您不需要在瀏覽器或 Azure 虛擬機器上安裝代理程式或任何軟體。 Bastion 服務沒有代理程式，不需要任何其他 RDP/SSH 軟體。

### <a name="browsers"></a>支援哪些瀏覽器？

在 Windows 中，請使用 Microsoft Edge 瀏覽器或 Google Chrome。 若為 Apple Mac，請使用 Google Chrome 瀏覽器。 Windows 和 Mac 也支援 Microsoft Edge Chromium。

### <a name="roles"></a>需要哪些角色權限才能存取虛擬機器？

若要建立連線，必須具備下列角色：

* 虛擬機器上的讀取者角色
* 虛擬機器的私人 IP 位址與 NIC 上的讀取者角色
* Azure Bastion 資源上的讀取者角色

### <a name="pricingpage"></a>價格為何？

如需詳細資訊，請參閱[價格頁面](https://aka.ms/BastionHostPricing)。

### <a name="session"></a>為何在 Bastion 工作階段開始之前，收到「您的工作階段已過期」錯誤訊息？

您應該只能從 Azure 入口網站起始工作階段。 登入 Azure 入口網站並再次開始您的工作階段。 如果您直接從另一個瀏覽器工作階段或索引標籤移至 URL，則預計會發生此錯誤。 這有助於確保您的工作階段更加安全，而且只能透過 Azure 入口網站存取工作階段。

### <a name="keyboard"></a>Bastion 遠端工作階段期間支援哪些鍵盤配置？

Azure Bastion 目前在 VM 內支援 en-us-qwerty 鍵盤配置。  鍵盤配置的其他地區設定支援正在進行中。

### <a name="udr"></a>Azure Bastion 子網路是否支援使用者定義的路由 (UDR)？

沒有。 Azure Bastion 子網路不支援 UDR。
若是相同的虛擬網路中同時包含 Azure Bastion 和 Azure 防火牆/網路虛擬設備 (NVA)，您無需強制將 Azure Bastion 子網路的流量導向 Azure 防火牆，因為 Azure Bastion 和您的 VM 間採用私人通訊。 如需詳細資訊，請參閱[透過 Bastion 從 Azure 防火牆後方存取 VM](https://azure.microsoft.com/blog/accessing-virtual-machines-behind-azure-firewall-with-azure-bastion/)。

### <a name="filetransfer"></a>Azure Bastion RDP 工作階段是否支援檔案傳輸？

我們正努力加入新功能。 目前不支援檔案傳輸，但這是我們未來的目標。 請不吝在 [Azure Bastion 意見反應頁面](https://feedback.azure.com/forums/217313-networking?category_id=367303)分享關於新功能的意見反應。
