---
title: 向 Azure 地圖服務驗證 | Microsoft Docs
description: 使用 Microsoft Azure 對應服務的 Azure Active Directory （Azure AD）或共用金鑰驗證。 瞭解如何取得 Azure 地圖服務訂用帳戶金鑰。
author: walsehgal
ms.author: v-musehg
ms.date: 12/30/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc
ms.openlocfilehash: a58436063009b732a15e74c8a3fc3f95b8df29cf
ms.sourcegitcommit: f53cd24ca41e878b411d7787bd8aa911da4bc4ec
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/10/2020
ms.locfileid: "75834189"
---
# <a name="authentication-with-azure-maps"></a>向 Azure 地圖服務驗證

Azure 地圖服務支援兩種驗證要求的方式：共用金鑰和 Azure Active Directory （Azure AD）。 本文說明這些驗證方法，協助指導您的實作。

## <a name="shared-key-authentication"></a>共用金鑰驗證

共用金鑰驗證會將 Azure 地圖服務帳戶所產生的金鑰連同每個要求傳送給 Azure 地圖服務。 對於 Azure 地圖服務服務的每個要求，必須將訂用帳戶*金鑰*新增為 URL 的參數。 建立 Azure 地圖服務帳戶之後，會產生主要和次要金鑰。 當您使用共用金鑰驗證呼叫 Azure 地圖服務時，建議您使用主要金鑰做為訂用帳戶金鑰。 次要金鑰可用於輪流金鑰變更之類的案例中。  

如需在 Azure 入口網站中查看金鑰的詳細資訊，請參閱[管理驗證](https://aka.ms/amauthdetails)。

> [!Tip]
> 建議您定期重新產生金鑰。 您會收到兩個金鑰，以便使用一個金鑰維持連線，同時重新產生另一個金鑰。 在重新產生金鑰時，必須對所有存取此帳戶的應用程式進行更新，使其使用新的金鑰。



## <a name="authentication-with-azure-active-directory-preview"></a>使用 Azure Active Directory 進行驗證 (預覽)

Azure 地圖服務現在提供 [Azure Active Directory (Azure AD)](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-whatis) 整合，以驗證 Azure 地圖服務的要求。 Azure AD 提供以身分識別為基礎的驗證（包括[角色型存取控制（RBAC））](https://docs.microsoft.com/azure/role-based-access-control/overview)，以將使用者層級、群組層級和應用層級的存取權授與 Azure 地圖服務資源。 後續幾節可協助您了解 Azure 地圖服務與 Azure AD 整合的概念和元件。

## <a name="authentication-with-oauth-access-tokens"></a>使用 OAuth 存取權杖進行驗證

針對與包含 Azure 地圖服務帳戶的 Azure 訂用帳戶相關聯的 Azure AD 租用戶，Azure 地圖服務可接受 **OAuth 2.0** 存取權杖。 Azure 地圖服務可接受下列項目的權杖：

* Azure AD 使用者。 
* 使用使用者所委派權限的夥伴應用程式。
* 適用於 Azure 資源的受控識別。

Azure 地圖服務會為每個 Azure 地圖服務帳戶產生「唯一識別碼 (用戶端識別碼)」。 當您將此用戶端識別碼與其他參數結合時，您可以根據您的 Azure 環境，指定下表中的值，以要求來自 Azure AD 的權杖。

| Azure 環境   | Azure AD token 端點 |
| --------------------|-------------------------|
| Azure 公用        | https://login.microsoftonline.com |
| Azure 政府機構    | https://login.microsoftonline.us |


如需如何為 Azure 地圖服務設定 Azure AD 和要求權杖的詳細資訊，請參閱[管理 Azure 地圖服務中的驗證](https://docs.microsoft.com/azure/azure-maps/how-to-manage-authentication)。

如需如何向 Azure AD 要求權杖的一般資訊，請參閱[何謂驗證？](https://docs.microsoft.com/azure/active-directory/develop/authentication-scenarios)。

## <a name="request-azure-map-resources-with-oauth-tokens"></a>使用 OAuth 權杖要求 Azure 地圖服務資源

從 Azure AD 接收權杖後，便可將設定了下列兩個必要要求標頭的要求傳送至 Azure 地圖服務：

| 要求標頭    |    值    |
|:------------------|:------------|
| x-ms-client-id    | 30d7cc….9f55|
| 授權     | Bearer eyJ0e….HNIVN |

> [!Note]
> `x-ms-client-id` 是以 Azure 地圖服務帳戶為基礎的 GUID，其顯示在 Azure 地圖服務的驗證頁面上。

以下是使用 OAuth 權杖的 Azure 地圖服務路由要求範例：

```
GET /route/directions/json?api-version=1.0&query=52.50931,13.42936:52.50274,13.43872 
Host: atlas.microsoft.com 
x-ms-client-id: 30d7cc….9f55 
Authorization: Bearer eyJ0e….HNIVN 
```

如需檢視用戶端識別碼的相關資訊，請參閱[檢視驗證詳細資料](https://aka.ms/amauthdetails)。

## <a name="control-access-with-rbac"></a>使用 RBAC 控制存取權

Azure AD 可讓您使用 RBAC 來控制安全資源的存取權。 建立 Azure 地圖服務帳戶並在您的 Azure AD 租使用者中註冊您的 Azure 地圖服務 Azure AD 應用程式之後，您可以在 Azure 地圖服務帳戶入口網站頁面上設定使用者、群組、應用程式或 Azure 資源的 RBAC。

Azure 地圖服務可透過 Azure 資源的受控識別，支援個別 Azure AD 使用者、群組、應用程式和 Azure 服務的讀取存取控制。

![Azure 地圖服務資料讀者 (預覽)](./media/azure-maps-authentication/concept.png)

如需檢視 RBAC 設定的相關資訊，請參閱[如何設定 Azure 地圖服務的 RBAC](https://aka.ms/amrbac)。

## <a name="managed-identities-for-azure-resources-and-azure-maps"></a>Azure 資源和 Azure 地圖服務的受控識別

[Azure 資源的受控識別](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)可為 Azure 服務 (Azure App Service、Azure Functions、Azure 虛擬機器等等) 提供自動的受控識別，以便獲得授權來存取 Azure 地圖服務。  

## <a name="next-steps"></a>後續步驟

* 若要深入了解如何向 Azure AD 和 Azure 地圖服務驗證應用程式，請參閱[管理 Azure 地圖服務中的驗證](https://docs.microsoft.com/azure/azure-maps/how-to-manage-authentication)。

* 若要深入了解如何驗證 Azure 地圖服務、地圖控制項和 Azure AD，請參閱[使用 Azure 地圖服務的地圖控制項](https://aka.ms/amaadmc)。
