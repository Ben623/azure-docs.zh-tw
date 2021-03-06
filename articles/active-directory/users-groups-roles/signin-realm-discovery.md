---
title: 登入期間的使用者名稱查閱-Azure Active Directory |Microsoft Docs
description: 螢幕上的訊息在登入期間如何反映使用者名稱查閱 Azure Active Directory
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 11/08/2019
ms.author: curtand
ms.reviewer: kexia
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: c8b6a65a964016f702fcf75aa4cbdab33a952e3b
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/13/2019
ms.locfileid: "74024257"
---
# <a name="home-realm-discovery-for-azure-active-directory-sign-in-pages"></a>Azure Active Directory 登入頁面的主領域探索

我們正在變更 Azure Active Directory (Azure AD) 的登入行為，來為新的驗證方法騰出空間並改善可用性。 登入期間，Azure AD 會判斷使用者應在何處進行驗證。 Azure AD 會讀取組織和使用者設定，來針對於登入頁面輸入的使用者名稱做出最佳的決策。 這讓我們離採用 FIDO 2.0 等其他認證的免用密碼未來更進了一步。

## <a name="home-realm-discovery-behavior"></a>主領域探索行為

在過去，主領域探索是由在登入時提供的網域，或由一些繼承應用程式的主領域探索原則所控管。 例如，在我們的探索行為中，Azure Active Directory 使用者可能會錯誤地輸入使用者名稱，但仍會抵達組織的認證集合畫面。 當使用者正確提供組織的功能變數名稱 "contoso.com" 時，就會發生這種情況。 這項行為並不允許個別使用者自訂體驗的細微性。

為了支援更廣泛的認證並增加可用性，在登入程式期間，Azure Active Directory 的使用者名稱查閱行為會立即更新。 新的行為會根據登入頁面上輸入的使用者名稱，來讀取租使用者和使用者層級設定，以做出明智的決策。 若要這麼做，Azure Active Directory 將會檢查登入頁面上所輸入的使用者名稱是否存在於其指定的網域中，或將使用者重新導向以提供其認證。

這項工作的另一個優點是改善了錯誤訊息。 以下是在登入僅支援 Azure Active Directory 使用者的應用程式時，改善的錯誤訊息範例。

- 使用者名稱不正確，或使用者名稱尚未同步到 Azure AD：
  
    ![使用者名稱不是輸入錯誤，或找不到](./media/signin-realm-discovery/typo-username.png)
  
- 功能變數名稱輸入錯誤：
  
    ![功能變數名稱輸入錯誤或找不到](./media/signin-realm-discovery/typo-domain.png)
  
- 使用者嘗試使用已知的取用者網域登入：
  
    ![使用已知的取用者網域進行登入](./media/signin-realm-discovery/consumer-domain.png)
  
- 密碼輸入錯誤，但使用者名稱是正確的：  
  
    ![密碼輸入正確的使用者名稱](./media/signin-realm-discovery/incorrect-password.png)
  
> [!IMPORTANT]
> 這項功能可能會影響依賴舊網域層級主領域探索來強制執行同盟的聯盟網域。 如需新增同盟網域支援時的更新，請參閱[Microsoft 365 服務登入期間的主領域探索](https://azure.microsoft.com/updates/signin-hrd/)。 在此同時，某些組織會使用不存在於 Azure Active Directory 中的使用者名稱來進行登入，但包含適當的功能變數名稱，因為功能變數名稱會將目前的使用者路由傳送到其組織的網域端點。 新的登入行為不允許這種情況。 系統會通知使用者，以更正使用者名稱，而且不允許使用 Azure Active Directory 中不存在的 username 登入。
>
> 如果您或您的組織具有相依于舊行為的作法，組織系統管理員必須更新員工登入和驗證檔，並訓練員工使用他們的 Azure Active Directory 使用者名稱來登入。
  
如果您對新行為有疑慮，請在本文的**意見**反應一節中留下您的意見。  

## <a name="next-steps"></a>後續步驟

[自訂您的登入商標](../fundamentals/add-custom-domain.md)
