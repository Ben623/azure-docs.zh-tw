---
title: 設定應用程式的使用者同意-Azure Active Directory
description: 了解如何管理使用者同意應用程式權限的方式。 您可以授與管理員同意，以簡化使用者體驗。 這些方法適用於 Azure Active Directory (Azure AD) 租用戶中的所有使用者。
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 10/22/2018
ms.author: mimart
ms.reviewer: arvindh
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4a187ea9d22efa40c482ea8930be0271d84a899a
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/21/2019
ms.locfileid: "74273979"
---
# <a name="configure-the-way-end-users-consent-to-an-application-in-azure-active-directory"></a>在 Azure Active Directory 設定使用者同意應用程式的方式
了解如何設定使用者同意應用程式權限的方式。 您可以授與管理員同意，以簡化使用者體驗。 本文提供不同的方式供您設定使用者同意。 這些方法適用於 Azure Active Directory (Azure AD) 租用戶中的所有使用者。 

如需同意應用程式的詳細資訊，請參閱 [Azure Active Directory 同意架構](../develop/consent-framework.md)。

## <a name="prerequisites"></a>先決條件

授與管理員同意時，您需要以全域管理員、應用程式管理員或雲端應用程式管理員的身分登入。

若要限制應用程式的存取權，您必須要求指派使用者，然後將使用者或群組指派給應用程式。  如需詳細資訊，請參閱[指派使用者和群組的方法](methods-for-assigning-users-and-groups.md)。

## <a name="grant-admin-consent-to-enterprise-apps-in-the-azure-portal"></a>在 Azure 入口網站中授與管理員同意企業應用程式

若要授與管理員同意企業應用程式：

1. 以全域管理員、應用程式管理員或雲端應用程式管理員的身分登入 [Azure 入口網站](https://portal.azure.com)。
2. 按一下左側導覽功能表頂端的 [所有服務]。 [Azure Active Directory 擴充功能] 隨即開啟。
3. 在篩選搜尋方塊中，輸入[Azure Active Directory]，然後選取 [Azure Active Directory] 項目。
4. 從導覽功能表中，按一下 [企業應用程式]。
5. 選取應用程式以取得同意。
6. 選取 [**許可權**]，然後按一下 **[授與系統管理員同意**]。 系統會提示您登入以管理應用程式。
7. 使用有權限授與管理員同意應用程式的帳戶登入。 
8. 同意應用程式權限。

此選項僅適用於下列情況的應用程式： 

- 已在您的租用戶中註冊，或
- 已在另一部的 Azure AD 租用戶中註冊，並由至少一個使用者同意。 在使用者已同意應用程式之後，Azure AD 會在 在 Azure 入口網站中的 [企業應用程式] 之下列出該應用程式。

## <a name="grant-admin-consent-when-registering-an-app-in-the-azure-portal"></a>在 Azure 入口網站中應用程式應用程式時授與管理員同意

若要在註冊應用程式時授與管理員同意： 

1. 以系統管理員身分登入 [Azure 入口網站](https://portal.azure.com)。
2. 瀏覽至 [應用程式註冊] 刀鋒視窗。
3. 選取同意的應用程式。
4. 選取 [API 權限]。
5. 按一下 **[授與系統管理員同意**]。


## <a name="grant-admin-consent-through-a-url-request"></a>透過 URL 要求授與管理員同意

若要透過 URL 要求授與管理員同意：

1. 利用您的應用程式組態來建構對 *login.microsoftonline.com* 的要求，並附加於 `&prompt=admin_consent` 上。 此 URL 看起來會像這樣： `https://login.microsoftonline.com/<tenant-id>/oauth2/authorize?client_id=<client id>&response_type=code&redirect_uri=<Your-Redirect-URI-Https-Encoded>&nonce=1234&resource=<your-resource-Https-encoded>&prompt=admin_consent`
2. 使用系統管理員認證登入之後，就已同意所有使用者使用應用程式。


## <a name="force-user-consent-through-a-url-request"></a>透過 URL 要求強制使用者同意

若要要求使用者在每次驗證時同意應用程式，請將 `&prompt=consent` 附加至驗證要求 URL。
此 URL 看起來會像這樣： `https://login.microsoftonline.com/<tenant-id>/oauth2/authorize?client_id=<client id>&response_type=code&redirect_uri=<Your-Redirect-URI-Https-Encoded>&nonce=1234&resource=<your-resource-Https-encoded>&prompt=consent`

## <a name="next-steps"></a>後續步驟

[同意並將應用程式整合到 AzureAD](../develop/quickstart-v1-integrate-apps-with-azure-ad.md)

[適用於 AzureAD v2.0 交集應用程式的同意與權限](../develop/active-directory-v2-scopes.md)

[AzureAD StackOverflow (英文)](https://stackoverflow.com/questions/tagged/azure-active-directory)
