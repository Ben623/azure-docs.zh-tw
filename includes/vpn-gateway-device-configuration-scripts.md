---
title: 包含檔案
description: 包含檔案
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 01/09/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 78dfd57fba6365f9c8937b30b5cf96b840749c68
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "66157543"
---
| **廠商** | **裝置系列** | **韌體版本** |
| --- | --- | --- |
|Cisco | ISR| IOS 15.1 (預覽)|
|Cisco | ASA | 適用於版本低於 9.8 之 ASA 的 ASA ( * ) RouteBased (IKEv2 - 沒有 BGP) |
|Cisco | ASA | 適用於 9.8 版以上之 ASA 的 ASA RouteBased (IKEv2 - 沒有 BGP) |
|Juniper | SRX_GA | 12.x|
|Juniper | SSG_GA | ScreenOS 6.2.x|
|Juniper | JSeries_GA | JunOS 12.x|
|Juniper | SRX | JunOS 12.x RouteBased BGP |
|Ubiquiti| EdgeRouter| EdgeOS v1.10x RouteBased VTI|
|Ubiquiti| EdgeRouter| EdgeOS v1.10x RouteBased BGP|

> [!NOTE]
> ( * ) 必要：NarrowAzureTrafficSelectors (啟用 UsePolicyBasedTrafficSelectors 選項) 和 CustomAzurePolicies (IKE/IPsec)
>
