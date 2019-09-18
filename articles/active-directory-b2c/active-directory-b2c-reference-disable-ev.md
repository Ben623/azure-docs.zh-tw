---
title: 在 Azure Active Directory B2C 中的取用者註冊期間停用電子郵件驗證 | Microsoft Docs
description: 示範如何在 Azure Active Directory B2C 中的取用者註冊期間停用電子郵件驗證的主題。
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/30/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: d827843bdbaff1ad95dce3318f39867abe8b2d0f
ms.sourcegitcommit: f209d0dd13f533aadab8e15ac66389de802c581b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/17/2019
ms.locfileid: "71065629"
---
# <a name="disable-email-verification-during-consumer-sign-up-in-azure-active-directory-b2c"></a>在取用者於 Azure Active Directory B2C 中註冊期間停用電子郵件驗證
啟用時，Azure Active Directory B2C （Azure AD B2C）會提供電子郵件地址和建立本機帳戶，讓取用者能夠註冊應用程式。 Azure AD B2C 可透過要求取用者在註冊程序期間驗證其身分以確保電子郵件地址的有效性。 它也會防止惡意自動化程序為應用程式產生假帳戶。

某些應用程式開發人員偏好在註冊程序期間跳過電子郵件驗證，改為讓取用者稍後在掙電子郵件地址。 為支援此方式，您可以將 Azure AD B2C 設定為停用電子郵件驗證。 這樣做可建立較為順暢的註冊程序，並提供區分已驗證電子郵件地址與未驗證電子郵件地址之取用者的彈性。

根據預設值，註冊使用者流程會開啟電子郵件驗證。 使用下列步驟將它關閉：

1. 按一下 [使用者流程]。
2. 按一下您的使用者流程 (例如 "B2C_1_SiUp") 以將它開啟。
3. 按一下 [頁面配置]。
4. 按一下 [本機帳戶註冊頁面]。
5. 按一下 [使用者屬性] 區段下 [名稱] 欄中的 [電子郵件地址]。
6. 在 [需要驗證] 之下，選取 [否]。
7. 按一下刀鋒視窗頂端的 [儲存] 。 大功告成！

> [!NOTE]
> 在註冊程序期間停用電子郵件驗證可能會導致收到垃圾郵件。 若停用預設的項目，建議您新增自己的驗證系統。
>
>
