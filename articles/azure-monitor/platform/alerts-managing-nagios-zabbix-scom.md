---
title: 在 Azure 監視器中管理來自 System Center Operations Manager、Zabbix 和 Nagios 的警示
description: 在 Azure 監視器中管理來自 System Center Operations Manager、Zabbix 和 Nagios 的警示
ms.service: azure-monitor
ms.subservice: alerts
ms.topic: conceptual
author: anantr
ms.author: robb
ms.date: 09/24/2018
ms.openlocfilehash: 0e774633542f9b7f8b7de9f8f187821bfbe6b9cf
ms.sourcegitcommit: ae461c90cada1231f496bf442ee0c4dcdb6396bc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/17/2019
ms.locfileid: "72555610"
---
# <a name="manage-alerts-from-system-center-operations-manager-zabbix-and-nagios-in-azure-monitor"></a>在 Azure 監視器中管理來自 System Center Operations Manager、Zabbix 和 Nagios 的警示

您現在可以在 [Azure 監視器](https://aka.ms/azure-alerts-overview)中檢視來自 Nagios、Zabbix 和 System Center Operations Manager 的警示。 這些警示來自 Nagios/Zabbix 伺服器或 System Center Operations Manager 整合到 Log Analytics 中。 

## <a name="prerequisites"></a>必要條件
Log Analytics 存放庫中具有警示類型的任何記錄，將匯入到 Azure 監視器，所以您必須執行必要組態以收集這些記錄。
1. 針對 **Nagios** 和 **Zabbix** 警示，[設定這些伺服器](https://docs.microsoft.com/azure/log-analytics/log-analytics-linux-agents)以[傳送警示](https://docs.microsoft.com/azure/azure-monitor/platform/data-sources-alerts-nagios-zabbix?toc=%2Fazure%2Fazure-monitor%2Ftoc.json)給 Log Analytics。
1. 針對 **System Center Operations Manager** 警示，[將 Operations Manager 管理群組連線到 Log Analytics 工作區](https://docs.microsoft.com/azure/log-analytics/log-analytics-om-agents)。 在此之後，從 Azure 解決方案市集部署[警示管理](https://docs.microsoft.com/azure/azure-monitor/platform/alert-management-solution)解決方案。 完成後，在 System Center Operations Manager 中建立的任何警示會匯入至記錄分析。

## <a name="view-your-alert-instances"></a>檢視警示執行個體
在設定將警示匯入到 Log Analytics 之後，您便可以開始在 [Azure 監視器](https://aka.ms/azure-alerts-overview)中檢視來自這些監視服務的警示執行個體。 一旦這些警示出現在 Azure 監視器中，您即可[管理您的警示執行個體](https://aka.ms/managing-alert-instances)、[管理在這些警示上建立的智慧群組](https://aka.ms/managing-smart-groups)以及[變更警示和智慧群組的狀態](https://aka.ms/managing-alert-smart-group-states)。

> [!NOTE]
>  1. 此方案只允許您在 Azure 監視器中查看 System Center Operations Manager/Zabbix/Nagios 引發的警示實例。 警示規則設定則只能在個別的監視工具中檢視/編輯。 
>  1. 所有引發的警示執行個體均可在 Azure 監視器和 Azure Log Analytics 中看到。 目前沒有任何方法可供您在兩者之間做選擇，也無法僅擷取所引發的特定警示。
>  1. 來自 System Center Operations Manager、Zabbix 和 Nagios 的所有警示都具有信號類型 "Unknown"，因為無法使用基礎遙測類型。
>  1. Nagios 警示不具狀態 – 例如，警示的[監視條件](https://aka.ms/azure-alerts-overview)將不會從「已引發」變更為「已解決」。 相反地，「已引發」和「已解決」兩者會顯示為個別的警示執行個體。 

