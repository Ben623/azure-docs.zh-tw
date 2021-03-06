---
title: 教學課程：將 Azure Active Directory 與 Kiteworks 整合 | Microsoft Docs
description: 了解如何設定 Azure Active Directory 與 Kiteworks 之間的單一登入功能。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: f7984aaf-ab1f-4a85-9646-a9523f5275d9
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 07/11/2019
ms.author: jeedes
ms.openlocfilehash: 52b113d92fa83795e94d5179ea47ed5d9d9e9a26
ms.sourcegitcommit: a6873b710ca07eb956d45596d4ec2c1d5dc57353
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/16/2019
ms.locfileid: "68248908"
---
# <a name="tutorial-integrate-kiteworks-with-azure-active-directory"></a>教學課程：整合 Kiteworks 與 Azure Active Directory

在本教學課程中，您將了解如何整合 Kiteworks 與 Azure Active Directory (Azure AD)。 在整合 Kiteworks 與 Azure AD 時，您可以︰

* 在 Azure AD 中控制可存取 Kiteworks 的人員。
* 讓使用者使用其 Azure AD 帳戶自動登入 Kiteworks。
* 在 Azure 入口網站集中管理您的帳戶。

若要深入了解 SaaS 應用程式與 Azure AD 整合，請參閱[什麼是搭配 Azure Active Directory 的應用程式存取和單一登入](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)。

## <a name="prerequisites"></a>必要條件

若要開始，您需要下列項目：

* Azure AD 訂用帳戶。 如果沒有訂用帳戶，您可以在[這裡](https://azure.microsoft.com/pricing/free-trial/)取得一個月的免費試用。
* 已啟用 Kiteworks 單一登入 (SSO) 的訂用帳戶。

## <a name="scenario-description"></a>案例描述

在本教學課程中，您會在測試環境中設定和測試 Azure AD SSO。

* Kiteworks 支援 **SP** 起始的 SSO
* Kiteworks 支援 **Just In Time** 使用者佈建

## <a name="adding-kiteworks-from-the-gallery"></a>從資源庫新增 Kiteworks

若要設定將 Kiteworks 整合到 Azure AD 中，您需要從資源庫將 Kiteworks 新增到受控 SaaS 應用程式清單。

1. 使用公司或學校帳戶或個人的 Microsoft 帳戶登入 [Azure 入口網站](https://portal.azure.com)。
1. 在左方瀏覽窗格上，選取 [Azure Active Directory]  服務。
1. 巡覽至 [企業應用程式]  ，然後選取 [所有應用程式]  。
1. 若要新增應用程式，請選取 [新增應用程式]  。
1. 在 [從資源庫新增]  區段的搜尋方塊中，輸入 **Kiteworks**。
1. 從結果面板中選取 [Kiteworks]  ，然後新增應用程式。 當應用程式新增至您的租用戶時，請等候幾秒鐘。


## <a name="configure-and-test-azure-ad-single-sign-on"></a>設定和測試 Azure AD 單一登入

以名為 **B.Simon** 的測試使用者，設定及測試與 Kiteworks 搭配運作的 Azure AD SSO。 若要讓 SSO 能夠運作，您必須建立 Azure AD 使用者與 Kiteworks 中相關使用者之間的連結關聯性。

若要設定及測試與 Kiteworks 搭配運作的 Azure AD SSO，請完成下列建置組塊：

1. **[設定 Azure AD SSO](#configure-azure-ad-sso)** - 讓您的使用者能夠使用此功能。
2. **[設定 Kiteworks SSO](#configure-kiteworks-sso)** - 在應用程式端設定單一登入設定。
3. **[建立 Azure AD 測試使用者](#create-an-azure-ad-test-user)** - 使用 Britta Simon 測試 Azure AD 單一登入。
4. **[指派 Azure AD 測試使用者](#assign-the-azure-ad-test-user)** - 讓 Britta Simon 能夠使用 Azure AD 單一登入。
5. **[建立 Kiteworks 測試使用者](#create-kiteworks-test-user)** - 讓 Kiteworks 中對應的 Britta Simon 連結到使用者在 Azure AD 中的代表項目。
6. **[測試 SSO](#test-sso)** - 驗證組態是否能運作。

### <a name="configure-azure-ad-sso"></a>設定 Azure AD SSO

依照下列步驟在 Azure 入口網站中啟用 Azure AD SSO。

1. 在 [Azure 入口網站](https://portal.azure.com/)的 [Kiteworks]  應用程式整合頁面上，尋找 [管理]  區段並選取 [單一登入]  。
1. 在 [選取單一登入方法]  頁面上，選取 [SAML]  。
1. 在 [以 SAML 設定單一登入]  頁面上，按一下 [基本 SAML 設定]  的編輯/畫筆圖示，以編輯設定。

   ![編輯基本 SAML 組態](common/edit-urls.png)

1. 在 [基本 SAML 組態]  區段上，輸入下列欄位的值：

    a. 在 [登入 URL]  文字方塊中，使用下列模式輸入 URL：`https://<kiteworksURL>.kiteworks.com`

    b. 在 [識別碼 (實體識別碼)]  文字方塊中，使用下列模式輸入 URL：`https://<kiteworksURL>/sp/module.php/saml/sp/saml2-acs.php/sp-sso`

    > [!NOTE]
    > 這些都不是真正的值。 請使用實際的「登入 URL」及「識別碼」來更新這些值。 請連絡 [Kiteworks 用戶端支援小組](https://accellion.com/support)以取得這些值。 您也可以參考 Azure 入口網站中**基本 SAML 組態**區段所示的模式。

1. 在 [以 SAML 設定單一登入]  頁面的 [SAML 簽署憑證]  區段中，尋找 [憑證 (Base64)]  並選取 [下載]  ，以下載憑證並將其儲存在電腦上。

    ![憑證下載連結](common/certificatebase64.png)

1. 在 [設定 Kiteworks]  區段上，依據您的需求複製適當的 URL。

    ![複製組態 URL](common/copy-configuration-urls.png)

### <a name="configure-kiteworks-sso"></a>設定 Kiteworks SSO

1. 以系統管理員身分登入 Kiteworks 公司網站。

1. 在頂端的工具列中，按一下 [設定]  。

    ![設定單一登入](./media/kiteworks-tutorial/tutorial_kiteworks_06.png)

1. 在 [驗證和授權]  區段中，按一下 [SSO 設定]  。

    ![設定單一登入](./media/kiteworks-tutorial/tutorial_kiteworks_07.png)

1. 在 [SSO 設定] 頁面上，執行下列步驟：

    ![設定單一登入](./media/kiteworks-tutorial/tutorial_kiteworks_09.png)

    a. 選取 [透過 SSO 驗證]  。

    b. 選取 [起始 AuthnRequest]  。

    c. 在 [IDP 實體識別碼]  文字方塊中，貼上您從 Azure 入口網站複製的 [Azure AD 識別碼]  值。

    d. 在 [單一登入服務 URL]  文字方塊中，貼上您從 Azure 入口網站複製的 [登入 URL]  值。

    e. 在 [單一登出服務 URL]  文字方塊中，貼上您從 Azure 入口網站複製的 [登出 URL]  值。

    f. 在 [記事本] 中開啟下載的憑證，複製其內容，然後貼到 [RSA 公開金鑰憑證]  文字方塊中。

    g. 按一下 [檔案]  。

### <a name="create-an-azure-ad-test-user"></a>建立 Azure AD 測試使用者

在本節中，您將在 Azure 入口網站中建立名為 B.Simon 的測試使用者。

1. 在 Azure 入口網站的左窗格中，依序選取 [Azure Active Directory]  、[使用者]  和 [所有使用者]  。
1. 在畫面頂端選取 [新增使用者]  。
1. 在 [使用者]  屬性中，執行下列步驟：
   1. 在 [名稱]  欄位中，輸入 `B.Simon`。
   1. 在 [使用者名稱]  欄位中，輸入 username@companydomain.extension。 例如： `B.Simon@contoso.com` 。
   1. 選取 [顯示密碼]  核取方塊，然後記下 [密碼]  方塊中顯示的值。
   1. 按一下頁面底部的 [新增]  。

### <a name="assign-the-azure-ad-test-user"></a>指派 Azure AD 測試使用者

在本節中，您會將 Kiteworks 的存取權授與 B.Simon，讓她能夠使用 Azure 單一登入。

1. 在 Azure 入口網站中，選取 [企業應用程式]  ，然後選取 [所有應用程式]  。
1. 在應用程式清單中，選取 [Kiteworks]  。
1. 在應用程式的概觀頁面中尋找 [管理]  區段，然後選取 [使用者和群組]  。

   ![[使用者和群組] 連結](common/users-groups-blade.png)

1. 選取 [新增使用者]  ，然後在 [新增指派]  對話方塊中選取 [使用者和群組]  。

    ![[新增使用者] 連結](common/add-assign-user.png)

1. 在 [使用者和群組]  對話方塊的 [使用者] 清單中選取 [B.Simon]  ，然後按一下畫面底部的 [選取]  按鈕。
1. 如果您在 SAML 判斷提示中需要任何角色值，請在 [選取角色]  對話方塊的清單中為使用者選取適當的角色，然後按一下畫面底部的 [選取]  按鈕。
1. 在 [新增指派]  對話方塊中，按一下 [指派]  按鈕。

### <a name="create-kiteworks-test-user"></a>建立 Kiteworks 測試使用者

本節的目標是要在 Kiteworks 中建立一個名為 Britta Simon 的使用者。

Kiteworks 支援預設啟用的 Just-in-Time 佈建。 在這一節沒有您需要進行的動作項目。 嘗試存取 Kiteworks 時，如果使用者還不存在，就會建立新使用者。

> [!NOTE]
> 如果您需要手動建立使用者，您需要連絡 [Kiteworks 支援小組](https://accellion.com/support)。

### <a name="test-sso"></a>測試 SSO

在本節中，您會使用存取面板來測試您的 Azure AD 單一登入設定。

當您在存取面板中按一下 Kiteworks 圖格時，應該會自動登入您已設定 SSO 的 Kiteworks。 如需「存取面板」的詳細資訊，請參閱[存取面板簡介](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)。

## <a name="additional-resources"></a>其他資源

- [如何與 Azure Active Directory 整合 SaaS 應用程式的教學課程清單](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [什麼是 Azure Active Directory 中的條件式存取？](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
