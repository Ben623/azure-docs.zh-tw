---
title: 授與 Azure 事件中樞的存取權
description: 本文提供有關授權存取 Azure 事件中樞資源的不同選項的資訊。
services: event-hubs
ms.service: event-hubs
documentationcenter: ''
author: spelluru
ms.topic: conceptual
ms.date: 08/22/2019
ms.author: spelluru
ms.openlocfilehash: b5a037eaf310aa28c76d831dc9fe56eefaddbe56
ms.sourcegitcommit: 5a8c65d7420daee9667660d560be9d77fa93e9c9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/15/2019
ms.locfileid: "74123476"
---
# <a name="authorize-access-to-azure-event-hubs"></a>授與 Azure 事件中樞的存取權
每次您發佈或使用事件中樞的事件/資料時，您的用戶端都會嘗試存取事件中樞資源。 每個對安全資源的要求都必須經過授權，服務才能確保用戶端具有發佈/取用資料的必要許可權。 

Azure 事件中樞提供下列選項來授權安全資源的存取：

## <a name="azure-active-directory"></a>Azure Active Directory
事件中樞資源的 Azure Active Directory （Azure AD）整合提供角色型存取控制（RBAC），以精細控制用戶端對資源的存取。 您可以使用角色型存取控制（RBAC），將許可權授與安全性主體，這可能是使用者、群組或應用程式服務主體。 安全性主體會由 Azure AD 進行驗證，以傳回 OAuth 2.0 權杖。 權杖可以用來授權存取事件中樞資源的要求。

如需使用 Azure AD 進行驗證的詳細資訊，請參閱下列文章：

- [使用 Azure Active Directory 驗證 Azure 事件中樞的要求](authenticate-application.md)
- [使用 Azure Active Directory 授權事件中樞資源的存取權](authorize-access-azure-active-directory.md)。

## <a name="shared-access-signatures"></a>共用存取簽章 
事件中樞資源的共用存取簽章（SAS）提供有限的委派存取權給事件中樞資源。 在簽章有效的時間間隔或其授與的許可權上加入條件約束，可提供管理資源的彈性。 如需詳細資訊，請參閱[使用共用存取簽章（SAS）進行驗證](authenticate-shared-access-signature.md)。 

使用 Azure AD 所傳回的 OAuth 2.0 權杖來授權使用者或應用程式，可透過共用存取簽章（SAS）提供更優異的安全性和易用性。 有了 Azure AD，就不需要在您的程式碼中儲存存取權杖，而且可能會有潛在的安全性弱點。 雖然您可以繼續使用共用存取簽章（SAS）來授與更細緻的事件中樞資源存取權，但 Azure AD 提供類似的功能，而不需要管理 SAS 權杖或擔心撤銷遭盜用的 SAS。 

根據預設，所有事件中樞資源都會受到保護，而且僅供帳戶擁有者使用。 雖然您可以使用上面所述的任何授權策略來授與用戶端存取事件中樞資源的許可權。 只有使用 Azure Resource Manager 部署模型所建立事件中樞資源支援 Azure AD 授權。 Microsoft 建議盡可能使用 Azure AD，以獲得最高的安全性和使用便利性。

如需使用 SAS 進行授權的詳細資訊，請參閱[使用共用存取簽章授權事件中樞資源的存取權](authorize-access-shared-access-signature.md)。

## <a name="next-steps"></a>後續步驟
- 查看 GitHub 存放庫中發佈的[RBAC 範例](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/Rbac)。 
- 請參閱下列文章：
    - [使用 Azure Active Directory 驗證從應用程式 Azure 事件中樞的要求](authenticate-application.md)
    - [使用 Azure Active Directory 來驗證受控識別，以存取事件中樞資源](authenticate-managed-identity.md)
    - [使用共用存取簽章驗證 Azure 事件中樞的要求](authenticate-shared-access-signature.md)
    - [使用 Azure Active Directory 授權事件中樞資源的存取權](authorize-access-azure-active-directory.md)
    - [使用共用存取簽章授權事件中樞資源的存取權](authorize-access-shared-access-signature.md)

