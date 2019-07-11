---
title: 教學課程：Azure Active Directory 與 LinkedIn Sales Navigator 整合 | Microsoft Docs
description: 了解如何設定 Azure Active Directory 與 LinkedIn Sales Navigator 之間的單一登入。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 7a9fa8f3-d611-4ffe-8d50-04e9586b24da
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/19/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 38ce83df4d4de6d7816ee969a4fee153ad5457d8
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "67098004"
---
# <a name="tutorial-azure-active-directory-integration-with-linkedin-sales-navigator"></a>教學課程：Azure Active Directory 與 LinkedIn Sales Navigator 整合

在本教學課程中，您將了解如何整合 LinkedIn Sales Navigator 與 Azure Active Directory (Azure AD)。
LinkedIn Sales Navigator 與 Azure AD 整合提供下列優點：

* 您可以在 Azure AD 中控制可存取 LinkedIn Sales Navigator 的人員。
* 您可以讓使用者使用其 Azure AD 帳戶自動登入 LinkedIn Sales Navigator (單一登入)。
* 您可以在 Azure 入口網站中集中管理您的帳戶。

若您想了解 SaaS app 與 Azure AD 整合的更多詳細資訊，請參閱 [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)。
如果您沒有 Azure 訂用帳戶，請在開始之前先[建立免費帳戶](https://azure.microsoft.com/free/)。

## <a name="prerequisites"></a>必要條件

如要設定 Azure AD 與 LinkedIn Sales Navigator 的整合，您需要下列項目：

* Azure AD 訂用帳戶。 如果您沒有 Azure AD 環境，您可以申請[免費帳戶](https://azure.microsoft.com/free/)
* 已啟用 LinkedIn Sales Navigator 單一登入的訂用帳戶

## <a name="scenario-description"></a>案例描述

在本教學課程中，您會在測試環境中設定和測試 Azure AD 單一登入。

* LinkedIn Sales Navigator 支援 **SP 和 IDP** 起始的 SSO

* LinkedIn Sales Navigator 支援 **Just In Time** 使用者佈建

* LinkedIn Sales Navigator 支援 [**自動化**使用者佈建](linkedinsalesnavigator-provisioning-tutorial.md)

## <a name="adding-linkedin-sales-navigator-from-the-gallery"></a>從資源庫新增 LinkedIn Sales Navigator

如要設定將 LinkedIn Sales Navigator 整合到 Azure AD 中，您需要從資源庫中將 LinkedIn Sales Navigator 新增到受控 SaaS 應用程式清單。

**若要從資源庫新增 LinkedIn Sales Navigator，請執行下列步驟：**

1. 在 **[Azure 入口網站](https://portal.azure.com)** 的左方瀏覽窗格中，按一下 [Azure Active Directory]  圖示。

    ![Azure Active Directory 按鈕](common/select-azuread.png)

2. 瀏覽至 [企業應用程式]  ，然後選取 [所有應用程式]  選項。

    ![企業應用程式刀鋒視窗](common/enterprise-applications.png)

3. 若要新增新的應用程式，請按一下對話方塊頂端的 [新增應用程式]  按鈕。

    ![新增應用程式按鈕](common/add-new-app.png)

4. 在搜尋方塊中，輸入 **LinkedIn Sales Navigator**，從結果面板中選取 [LinkedIn Sales Navigator]  ，然後按一下 [新增]  按鈕以新增應用程式。

    ![結果清單中的 LinkedIn Sales Navigator](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>設定和測試 Azure AD 單一登入

在本節中，您會以名為 **Britta Simon** 的測試使用者身分，使用 LinkedIn Sales Navigator 設定及測試 Azure AD 單一登入。
若要讓單一登入能夠運作，必須建立 Azure AD 使用者與 LinkedIn Sales Navigator 中相關使用者之間的連結關聯性。

若要設定及測試與 LinkedIn Sales Navigator 搭配運作的 Azure AD 單一登入，您需要完成下列建置組塊：

1. **[設定 Azure AD 單一登入](#configure-azure-ad-single-sign-on)** - 讓您的使用者能夠使用此功能。
2. **[設定 LinkedIn Sales Navigator 單一登入](#configure-linkedin-sales-navigator-single-sign-on)** - 在應用程式端設定單一登入設定。
3. **[建立 Azure AD 測試使用者](#create-an-azure-ad-test-user)** - 使用 Britta Simon 測試 Azure AD 單一登入。
4. **[指派 Azure AD 測試使用者](#assign-the-azure-ad-test-user)** - 讓 Britta Simon 能夠使用 Azure AD 單一登入。
5. **[建立 LinkedIn Sales Navigator 測試使用者](#create-linkedin-sales-navigator-test-user)** - 使 LinkedIn Sales Navigator 中對應的 Britta Simon 連結到該使用者在 Azure AD 中的代表項目。
6. **[測試單一登入](#test-single-sign-on)** ，驗證組態是否能運作。

### <a name="configure-azure-ad-single-sign-on"></a>設定 Azure AD 單一登入

在本節中，您會在 Azure 入口網站中啟用 Azure AD 單一登入。

如要設定搭配 LinkedIn Sales Navigator 的 Azure AD 單一登入，請執行下列步驟：

1. 在 [Azure 入口網站](https://portal.azure.com/)的 [LinkedIn Sales Navigator]  應用程式整合頁面上，選取 [單一登入]  。

    ![設定單一登入連結](common/select-sso.png)

2. 在 [選取單一登入方法]  對話方塊中，選取 [SAML/WS-Fed]  模式以啟用單一登入。

    ![單一登入選取模式](common/select-saml-option.png)

3. 在 [以 SAML 設定單一登入] 頁面上，按一下 [編輯] 圖示以開啟 [基本 SAML 設定] 對話方塊。   

    ![編輯基本 SAML 組態](common/edit-urls.png)

4. 在 [基本 SAML 設定]  區段上，如果您想要以 **IDP** 起始模式設定應用程式，請執行下列步驟：

    ![LinkedIn Sales Navigator 網域及 URL 單一登入資訊](common/idp-intiated.png)

    a. 在 [識別碼]  文字方塊中，輸入 [實體識別碼]  值，您將會從 Linkedin 入口網站複製本教學課程稍後說明的實體識別碼值。

    b. 在 [回覆 URL]  文字方塊中，輸入 [判斷提示取用者存取 (ACS) URL]  值，您將會從 Linkedin 入口網站複製本教學課程稍後說明的判斷提示取用者存取 (ACS) URL 值。

5. 如果您想要以 **SP** 起始模式設定應用程式，請按一下 [設定其他 URL]  ，然後執行下列步驟：

    ![LinkedIn Sales Navigator 網域及 URL 單一登入資訊](common/metadata-upload-additional-signon.png)

    在 [登入 URL]  文字方塊中，以下列模式輸入 URL︰`https://www.linkedin.com/checkpoint/enterprise/login/<account id>?application=salesNavigator`

6. LinkedIn Sales Navigator 應用程式需要特定格式的 SAML 判斷提示，這會需要您將自訂屬性對應新增至您的 SAML 權杖屬性組態。 下列螢幕擷取畫面顯示預設屬性清單，其中的 **nameidentifier** 與 **user.userprincipalname** 相對應。 LinkedIn Sales Navigator 應用程式要求 nameidentifier 需與 **user.mail** 相對應，因此您必須按一下 [編輯] 圖示以編輯屬性對應，並變更屬性對應。

    ![image](common/edit-attribute.png)

7. 除了以上屬性外，LinkedIn Sales Navigator 應用程式還需要在 SAML 回應中傳回更多屬性。 在 [使用者屬性]  對話方塊的 [使用者宣告] 區段中，執行下列步驟以設定 SAML 權杖屬性，如下表所示：

    | Name | 來源屬性|
    | --- | --- |
    | 電子郵件| user.mail |
    | department| user.department |
    | firstname| user.givenname |
    | lastname| user.surname |

    a. 按一下 [新增宣告]  以開啟 [管理使用者宣告]  對話方塊。

    ![映像](common/new-save-attribute.png)

    ![映像](common/new-attribute-details.png)

    b. 在 [名稱]  文字方塊中，輸入該資料列所顯示的屬性名稱。

    c. 讓 [命名空間]  保持空白。

    d. 選取 [來源] 作為 [屬性]  。

    e. 在 [來源屬性]  清單中，輸入該資料列所顯示的屬性值。

    f. 按一下 [確定]  。

    g. 按一下 [檔案]  。

8. 在 [以 SAML 設定單一登入]  頁面的 [SAML 簽署憑證]  區段中，按一下 [下載]  以依據您的需求從指定選項下載**同盟中繼資料 XML**，並儲存在您的電腦上。

    ![憑證下載連結](common/metadataxml.png)

9. 在 [設定 LinkedIn Sales Navigator]  區段上，依據您的需求複製適當的 URL。

    ![複製組態 URL](common/copy-configuration-urls.png)

    a. 登入 URL

    b. Azure AD 識別碼

    c. 登出 URL

### <a name="configure-linkedin-sales-navigator-single-sign-on"></a>設定 LinkedIn Sales Navigator 單一登入

1. 在不同的網頁瀏覽器視窗中，以管理員身分登入您的 **LinkedIn Sales Navigator** 網站。

1. 在 [帳戶中心]  中，按一下 [設定]  底下的 [全域設定]  。 此外，請從下拉式清單中選取 [Sales Navigator]  。

    ![設定單一登入](./media/linkedinsalesnavigator-tutorial/tutorial_linkedin_admin_01.png)

1. 按一下**或按一下這裡以從表單載入和複製個別欄位**，然後執行下列步驟：

    ![設定單一登入](./media/linkedinsalesnavigator-tutorial/tutorial_linkedin_admin_031.png)

    a. 複製 [實體識別碼]  並將其貼到 Azure 入口網站中 [基本 SAML 組態]  中的 [識別碼]  文字方塊內。

    b. 複製 [判斷提示取用者存取 (ACS) Url]  並將其貼到 Azure 入口網站中 [基本 SAML 組態]  中的 [回覆 URL]  文字方塊內。

1. 移至 [LinkedIn 系統管理員設定]  區段。 按一下 [上傳 XML 檔案]  選項，將您從 Azure 入口網站下載的 XML 檔案上傳。

    ![設定單一登入](./media/linkedinsalesnavigator-tutorial/tutorial_linkedin_metadata_03.png)

1. 按一下 [開啟]  以啟用 SSO。 SSO 狀態會從 [未連線]  變更為 [已連線] 

    ![設定單一登入](./media/linkedinsalesnavigator-tutorial/tutorial_linkedin_admin_05.png)

### <a name="create-an-azure-ad-test-user"></a>建立 Azure AD 測試使用者 

本節的目標是要在 Azure 入口網站中建立一個名為 Britta Simon 的測試使用者。

1. 在 Azure 入口網站的左窗格中，依序選取 [Azure Active Directory]  、[使用者]  和 [所有使用者]  。

    ![[使用者和群組] 與 [所有使用者] 連結](common/users.png)

2. 在畫面頂端選取 [新增使用者]  。

    ![[新增使用者] 按鈕](common/new-user.png)

3. 在 [使用者] 屬性中，執行下列步驟。

    ![[使用者] 對話方塊](common/user-properties.png)

    a. 在 [名稱]  欄位中，輸入 **BrittaSimon**。
  
    b. 在 [使用者名稱]  欄位中，輸入 `brittasimon@yourcompanydomain.extension`。 例如， BrittaSimon@contoso.com

    c. 選取 [顯示密碼]  核取方塊，然後記下 [密碼] 方塊中顯示的值。

    d. 按一下頁面底部的 [新增]  。

### <a name="assign-the-azure-ad-test-user"></a>指派 Azure AD 測試使用者

在本節中，您會將 LinkedIn Sales Navigator 的存取權授與 Britta Simon，讓她能夠使用 Azure 單一登入。

1. 在 Azure 入口網站中，依序選取 [企業應用程式]  、[所有應用程式]  及 [LinkedIn Sales Navigator]  。

    ![企業應用程式刀鋒視窗](common/enterprise-applications.png)

2. 在應用程式清單中，選取 [LinkedIn Sales Navigator]  。

    ![應用程式清單中的 LinkedIn Sales Navigator 連結](common/all-applications.png)

3. 在左側功能表中，選取 [使用者和群組]  。

    ![[使用者和群組] 連結](common/users-groups-blade.png)

4. 按一下 [新增使用者]  按鈕，然後在 [新增指派]  對話方塊中，選取 [使用者和群組]  。

    ![[新增指派] 窗格](common/add-assign-user.png)

5. 在 [使用者和群組]  對話方塊的 [使用者] 清單中，選取 [Britta Simon]  ，然後按一下畫面底部的 [選取]  按鈕。

6. 如果您預期使用 SAML 判斷提示中的任何角色值，請在 [選取角色]  對話方塊的清單中選取適當使用者角色，然後按一下畫面底部的 [選取]  按鈕。

7. 在 [新增指派]  對話方塊中，按一下 [指派]  按鈕。

### <a name="create-linkedin-sales-navigator-test-user"></a>建立 LinkedIn Sales Navigator 測試使用者

LinkedIn Sales Navigator 應用程式支援及時 (JIT) 使用者佈建，而在驗證之後，應用程式會自動建立使用者。 啟用 [自動指派授權]  以指派授權給使用者。

   ![建立 Azure AD 測試使用者](./media/linkedinsalesnavigator-tutorial/LinkedinUserprovswitch.png)

### <a name="test-single-sign-on"></a>測試單一登入

在本節中，您會使用存取面板來測試您的 Azure AD 單一登入設定。

當您在存取面板中按一下 LinkedIn Sales Navigator 圖格時，應該會自動登入您設定 SSO 的 LinkedIn Sales Navigator。 如需「存取面板」的詳細資訊，請參閱[存取面板簡介](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)。

## <a name="additional-resources"></a>其他資源

- [如何與 Azure Active Directory 整合 SaaS 應用程式的教學課程清單](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [什麼是 Azure Active Directory 中的條件式存取？](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

