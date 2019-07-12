---
title: 將 Windows 防火牆資料連接至 Azure 的 Sentinel Preview |Microsoft Docs
description: 了解如何將 Windows 防火牆的資料連接至 Azure 的 Sentinel。
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.assetid: 0e41f896-8521-49b8-a244-71c78d469bc3
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/17/2019
ms.author: rkarlin
ms.openlocfilehash: a863910ee338da5655e9f3b5610b0a8049b8b2a9
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/07/2019
ms.locfileid: "67620768"
---
# <a name="connect-windows-firewall"></a>連線 Windows 防火牆

> [!IMPORTANT]
> Azure Sentinel 目前為公開預覽狀態。
> 此預覽版本是在沒有服務等級協定的情況下提供，不建議用於生產工作負載。 可能不支援特定功能，或可能已經限制功能。 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

如果連線到您 Azure Sentinel 的工作區的 Windows 防火牆連接器可讓您輕鬆地連接您的 Windows 防火牆記錄。 此連線可讓您檢視儀表板、 建立自訂警示，以及改善的調查。 這可讓您更多深入您的組織網路，並改善您的安全性作業功能。 此解決方案會收集 Windows 防火牆事件，從 Log Analytics 代理程式安裝所在的 Windows 機器。 


> [!NOTE]
> 資料會儲存在您在執行 Azure Sentinel 的工作區的地理位置。

## <a name="enable-the-connector"></a>啟用連接器 

1. 在 Azure Sentinel 入口網站中，選取**資料連接器**，然後按一下**Windows 防火牆**圖格。 
1.  如果您的 Windows 機器是在 Azure 中：
    1. 按一下 **在 Azure Windows 虛擬機器上的安裝代理程式**。
    1. 在 **虛擬機器**清單中，選取您想要串流處理至 Azure 的 Sentinel 的 Windows 機器。 請確定這是 Windows VM。
    1. 在視窗中開啟該 vm，按一下**Connect**。  
    1. 按一下 **啟用**中**Windows 防火牆連接器**視窗。 

2. 如果您的 Windows 電腦不是 Azure VM:
    1. 按一下 **非 Azure 電腦上的安裝代理程式**。
    1. 在 [**直接代理程式**] 視窗中，選取**下載 Windows 代理程式 （64 位元）** 或是**下載 Windows 代理程式 （32 位元）** 。
    1. 在 Windows 電腦上安裝代理程式。 複製**工作區識別碼**，**主索引鍵**，並**次要金鑰**和使用它們在安裝期間出現提示時。

4. 選取您要串流處理的資料類型。
5. 按一下 **安裝解決方案**。
6. 若要使用相關的結構描述在 Log Analytics 中的 Windows 防火牆，搜尋**SecurityEvent**。

## <a name="validate-connectivity"></a>驗證連線能力

可能需要多達 20 分鐘，直到您的記錄檔開始出現在 Log Analytics 中。 



## <a name="next-steps"></a>後續步驟
在本文件中，您已了解如何連接到 Azure Sentinel 的 Windows 防火牆。 若要深入了解 Azure Sentinel，請參閱下列文章：
- 了解如何[了解您的資料，與潛在的威脅](quickstart-get-visibility.md)。
- 開始[偵測威脅與 Azure Sentinel](tutorial-detect-threats.md)。

