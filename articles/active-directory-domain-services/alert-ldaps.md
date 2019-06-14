---
title: Azure Active Directory Domain Services：針對安全 LDAP 組態進行疑難排解 | Microsoft Docs
description: 針對 Azure AD Domain Services 的安全 LDAP 進行疑難排解
services: active-directory-ds
documentationcenter: ''
author: MikeStephens-MS
manager: ''
editor: ''
ms.assetid: 81208c0b-8d41-4f65-be15-42119b1b5957
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/22/2019
ms.author: mstephen
ms.openlocfilehash: dde4a02e5be32d5549ba499742d1ab650fa146c0
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "66246580"
---
# <a name="azure-ad-domain-services---troubleshooting-secure-ldap-configuration"></a>Azure AD Domain Services - 針對安全 LDAP 設定進行疑難排解

本文會針對[設定 Azure AD Domain Services 的安全 LDAP](configure-ldaps.md) 時的常見問題提供解決方案。

## <a name="aadds101-secure-ldap-network-security-group-configuration"></a>AADDS101：安全 LDAP 網路安全性群組組態

**警示訊息：**

*受控網域已啟用透過網際網路的安全 LDAP。不過，卻未使用網路安全性群組鎖定連接埠 636 的存取。這可能會使受控網域上的使用者帳戶暴露於暴力密碼破解攻擊的威脅之下。*

### <a name="secure-ldap-port"></a>安全 LDAP 連接埠

當安全 LDAP 啟用時，建議您建立其他規則，僅允許來自特定 IP 位址的輸入 LDAPS 存取。 這些規則能保護網域，不讓其遭受可能會造成安全性威脅的暴力攻擊。 連接埠 636 允許存取受控網域。 更新 NSG 使其允許存取安全 LDAP 的方法如下：

1. 在 Azure 入口網站中，瀏覽至 [網路安全性群組](https://portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.Network%2FNetworkSecurityGroups) 索引標籤
2. 從資料表選擇與網域相關聯的 NSG。
3. 按一下 **輸入安全性規則**
4. 建立連接埠 636 規則
   1. 按一下頂端導覽列上的 [新增]  。
   2. 選擇來源的 [IP 位址]  。
   3. 指定此規則的來源連接埠範圍。
   4. 輸入 "636" 作為目的地連接埠範圍。
   5. 通訊協定為 **TCP**。
   6. 為規則提供適當的名稱、描述和優先順序。 此規則的優先順序應高於「全部拒絕」規則的優先順序 (如果有的話)。
   7. 按一下 [確定]  。
5. 確認規則已建立。
6. 請在兩個小時後檢查網域的健康情況，以確保您已正確完成所有步驟。

> [!TIP]
> 連接埠 636 不是 Azure AD Domain Services 順暢執行所需的唯一規則。 若要深入了解，請瀏覽[網路指導方針](network-considerations.md)或[針對 NSG 設定進行疑難排解](alert-nsg.md)文章。
>

## <a name="aadds502-secure-ldap-certificate-expiring"></a>AADDS502：安全 LDAP 憑證即將到期

**警示訊息：**

*受控網域的安全 LDAP 憑證將於 [date] 到期。*

**解決方案：**

依照[設定安全 LDAP](configure-ldaps.md) 一文中所述的步驟，建立新的安全 LDAP 憑證。

## <a name="contact-us"></a>與我們連絡
請連絡 Azure Active Directory Domain Services 產品小組， [分享意見或尋求支援](contact-us.md)。
