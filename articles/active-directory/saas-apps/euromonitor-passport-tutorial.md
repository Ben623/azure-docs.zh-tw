---
title: 教學課程：Azure Active Directory 單一登入 (SSO) 與 Euromonitor Passport 整合 | Microsoft Docs
description: 了解如何設定 Azure Active Directory 與 Euromonitor Passport 之間的單一登入。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 7740905c-04c4-4d8c-ad90-523a6cd1e206
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/31/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1748c6945dc50554c84fa8c178cece02baf9d840
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/14/2019
ms.locfileid: "74075252"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-euromonitor-passport"></a>教學課程：Azure Active Directory 單一登入 (SSO) 與 Euromonitor Passport 整合

在本教學課程中，您會了解如何將 Euromonitor Passport 與 Azure Active Directory (Azure AD) 整合。 整合 Euromonitor Passport 與 Azure AD 後，您可以：

* 在 Azure AD 中控制可存取 Euromonitor Passport 的人員。
* 讓使用者使用其 Azure AD 帳戶自動登入 Euromonitor Passport。
* 在 Azure 入口網站集中管理您的帳戶。

若要深入了解 SaaS 應用程式與 Azure AD 整合，請參閱[什麼是搭配 Azure Active Directory 的應用程式存取和單一登入](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)。

## <a name="prerequisites"></a>必要條件

若要開始，您需要下列項目：

* Azure AD 訂用帳戶。 如果沒有訂用帳戶，您可以取得[免費帳戶](https://azure.microsoft.com/free/)。
* 已啟用 Euromonitor Passport 單一登入 (SSO) 的訂用帳戶。

## <a name="scenario-description"></a>案例描述

在本教學課程中，您會在測試環境中設定和測試 Azure AD SSO。

* Euromonitor Passport 支援由 **SP 和 IDP** 起始的 SSO

> [!NOTE]
> 此應用程式的識別碼是固定的字串值，因此一個租用戶中只能設定一個執行個體。

## <a name="adding-euromonitor-passport-from-the-gallery"></a>從資源庫新增 Euromonitor Passport

若要設定將 Euromonitor Passport 整合到 Azure AD 中，您需要從資源庫將 Euromonitor Passport 新增到受控 SaaS 應用程式清單。

1. 使用公司或學校帳戶或個人的 Microsoft 帳戶登入 [Azure 入口網站](https://portal.azure.com)。
1. 在左方瀏覽窗格上，選取 [Azure Active Directory]  服務。
1. 巡覽至 [企業應用程式]  ，然後選取 [所有應用程式]  。
1. 若要新增應用程式，請選取 [新增應用程式]  。
1. 在 [從資源庫新增]  區段的搜尋方塊中輸入 **Euromonitor Passport**。
1. 從結果面板選取 [Euromonitor Passport]  ，然後新增應用程式。 當應用程式新增至您的租用戶時，請等候幾秒鐘。

## <a name="configure-and-test-azure-ad-single-sign-on-for-euromonitor-passport"></a>設定及測試 Euromonitor Passport 的 Azure AD 單一登入

以名為 **B. Simon** 的測試使用者，設定及測試與 Euromonitor Passport 搭配運作的 Azure AD SSO。 若要讓 SSO 能夠運作，您必須建立 Azure AD 使用者與 Euromonitor Passport 中相關使用者之間的連結關聯性。

若要設定及測試與 Euromonitor Passport 搭配運作的 Azure AD SSO，請完成下列構成要素：

1. **[設定 Azure AD SSO](#configure-azure-ad-sso)** - 讓您的使用者能夠使用此功能。
    * **[建立 Azure AD 測試使用者](#create-an-azure-ad-test-user)** - 使用 B.Simon 測試 Azure AD 單一登入。
    * **[指派 Azure AD 測試使用者](#assign-the-azure-ad-test-user)** - 讓 B.Simon 能夠使用 Azure AD 單一登入。
1. **[設定 Euromonitor Passport SSO](#configure-euromonitor-passport-sso)** - 在應用程式端設定單一登入設定。
    * **[建立 Euromonitor Passport 測試使用者](#create-euromonitor-passport-test-user)** - 使 Euromonitor Passport 中對應的 B.Simon 連結到該使用者在 Azure AD 中的代表項目。
1. **[測試 SSO](#test-sso)** - 驗證組態是否能運作。

## <a name="configure-azure-ad-sso"></a>設定 Azure AD SSO

依照下列步驟在 Azure 入口網站中啟用 Azure AD SSO。

1. 在 [Azure 入口網站](https://portal.azure.com/)的 [Euromonitor Passport]  應用程式整合頁面上，尋找 [管理]  區段並選取 [單一登入]  。
1. 在 [**選取單一登入方法**] 頁面上，選取 [**SAML**]。
1. 在 [以 SAML 設定單一登入]  頁面上，按一下 [基本 SAML 設定]  的編輯/畫筆圖示，以編輯設定。

   ![編輯基本 SAML 組態](common/edit-urls.png)

1. 在 [基本 SAML 組態]  區段中，使用者不需要執行任何步驟，因為應用程式已預先與 Azure 整合。

1. 如果您想要以 **SP** 起始模式設定應用程式，您必須向 [Euromonitor Passport 支援小組](mailto:passport.support@euromonitor.com)索取登入 URL。 取得 Euromonitor Passport 支援小組所提供的登入 URL 後，請按一下 [設定其他 URL]  ，然後執行下列步驟：

    將 Euromonitor Passport 支援小組提供的 [登入 URL] 值貼到 [登入 URL] 文字方塊中。

1. 按一下 [檔案]  。

1. Euromonitor Passport 應用程式需要特定格式的 SAML 判斷提示，需要您將自訂屬性對應新增到您的 SAML 權杖屬性設定。 以下螢幕擷取畫面顯示預設屬性清單。

    ![image](common/default-attributes.png)

1. 除了上述屬性外，Euromonitor Passport 應用程式還需要在 SAML 回應中多傳回幾個屬性，如下所示。 這些屬性也會預先填入，但您可以根據您的需求來檢閱這些屬性。

    | 名稱 | 來源屬性|
    | ---------------| --------- |
    | 名稱識別碼值 | user.userprincipalname |

    > [!NOTE]
    > 用戶端系統管理員可以依據其需求新增/變更屬性。

1. 在 [以 SAML 設定單一登入]  頁面的 [SAML 簽署憑證]  區段中，按一下 [複製] 按鈕以複製 [應用程式同盟中繼資料 URL]  ，並將資料儲存在您的電腦上。

    ![憑證下載連結](common/copy-metadataurl.png)

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

在本節中，您會將 Euromonitor Passport 的存取權授與 B.Simon，使其能夠使用 Azure 單一登入。

1. 在 Azure 入口網站中，選取 [企業應用程式]  ，然後選取 [所有應用程式]  。
1. 在應用程式清單中，選取 [Euromonitor Passport]  。
1. 在應用程式的概觀頁面中尋找 [管理]  區段，然後選取 [使用者和群組]  。

   ![[使用者和群組] 連結](common/users-groups-blade.png)

1. 選取 [新增使用者]  ，然後在 [新增指派]  對話方塊中選取 [使用者和群組]  。

    ![[新增使用者] 連結](common/add-assign-user.png)

1. 在 [使用者和群組]  對話方塊的 [使用者] 清單中選取 [B.Simon]  ，然後按一下畫面底部的 [選取]  按鈕。
1. 如果您在 SAML 判斷提示中需要任何角色值，請在 [選取角色]  對話方塊的清單中為使用者選取適當的角色，然後按一下畫面底部的 [選取]  按鈕。
1. 在 [新增指派]  對話方塊中，按一下 [指派]  按鈕。

## <a name="configure-euromonitor-passport-sso"></a>設定 Euromonitor Passport SSO

若要在 **Euromonitor Passport** 端設定單一登入，您必須將 [應用程式同盟中繼資料 URL]  傳送給 [Euromonitor Passport 支援小組](mailto:passport.support@euromonitor.com)。 他們會進行此設定，讓兩端的 SAML SSO 連線都設定正確。

### <a name="create-euromonitor-passport-test-user"></a>建立 Euromonitor Passport 測試使用者

在本節中，您會在 Euromonitor Passport 中建立名為 B.Simon 的使用者。 請與 [Euromonitor Passport 支援小組](mailto:passport.support@euromonitor.com)合作，在 Euromonitor Passport 平台中新增使用者。 您必須先建立和啟動使用者，然後才能使用單一登入。

## <a name="test-sso"></a>測試 SSO 

在本節中，您會使用存取面板來測試您的 Azure AD 單一登入設定。

當您在「存取面板」中按一下 [Euromonitor Passport] 圖格時，應該會自動登入您已設定 SSO 的 Euromonitor Passport。 如需「存取面板」的詳細資訊，請參閱[存取面板簡介](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)。

## <a name="additional-resources"></a>其他資源

- [如何與 Azure Active Directory 整合 SaaS 應用程式的教學課程清單](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [什麼是 Azure Active Directory 中的條件式存取？](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [嘗試搭配 Azure AD 使用 Euromonitor Passport](https://aad.portal.azure.com/)