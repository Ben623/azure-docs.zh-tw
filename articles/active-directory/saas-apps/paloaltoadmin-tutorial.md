---
title: 教學課程：Azure Active Directory 與 Palo Alto Networks - Admin UI 整合 | Microsoft Docs
description: 了解如何設定 Azure Active Directory 與 Palo Alto 網路 - 系統管理 UI 之間的單一登入。
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: a826eaec-15af-4c85-8855-8a3374d1efb9
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/02/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: dd0ca3bb356319f4661e24b192a5f7e776d14cd0
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "67095075"
---
# <a name="tutorial-azure-active-directory-integration-with-palo-alto-networks---admin-ui"></a>教學課程：Azure Active Directory 與 Palo Alto Networks - Admin UI 整合

在本教學課程中，您將了解如何整合 Palo Alto 網路 - 系統管理 UI 與 Azure Active Directory (Azure AD)。
將 Palo Alto 網路 - 系統管理 UI 與 Azure AD 整合，能提供下列優點：

* 您可以在 Azure AD 中控制可存取 Palo Alto 網路 - 系統管理 UI 的人員。
* 您可以讓使用者使用其 Azure AD 帳戶自動登入 Palo Alto Networks - Admin UI (單一登入)。
* 您可以在 Azure 入口網站中集中管理您的帳戶。

若您想了解 SaaS app 與 Azure AD 整合的更多詳細資訊，請參閱 [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)。
如果您沒有 Azure 訂用帳戶，請在開始之前先[建立免費帳戶](https://azure.microsoft.com/free/)。

## <a name="prerequisites"></a>必要條件

若要設定 Azure AD 與 Palo Alto 網路 - 系統管理 UI 整合，您需要下列項目：

* Azure AD 訂用帳戶。 如果您沒有 Azure AD 環境，您可以在[這裡](https://azure.microsoft.com/pricing/free-trial/)取得一個月的試用帳戶
* 已啟用 Palo Alto Networks - Admin UI 單一登入的訂用帳戶

## <a name="scenario-description"></a>案例描述

在本教學課程中，您會在測試環境中設定和測試 Azure AD 單一登入。

* Palo Alto Networks - Admin UI 支援 **SP** 起始的 SSO
* Palo Alto Networks - Admin UI 支援 **Just-In-Time** 使用者佈建

## <a name="adding-palo-alto-networks---admin-ui-from-the-gallery"></a>從資源庫新增 Palo Alto 網路 - 系統管理 UI

若要設定將 Palo Alto 網路 - 系統管理 UI 整合到 Azure AD 中，您需要從資源庫將 Palo Alto 網路 - 系統管理 UI 新增到受控 SaaS 應用程式清單。

**若要從資源庫新增 Palo Alto 網路 - 系統管理 UI，請執行下列步驟：**

1. 在 **[Azure 入口網站](https://portal.azure.com)** 的左方瀏覽窗格中，按一下 [Azure Active Directory]  圖示。

    ![Azure Active Directory 按鈕](common/select-azuread.png)

2. 瀏覽至 [企業應用程式]  ，然後選取 [所有應用程式]  選項。

    ![企業應用程式刀鋒視窗](common/enterprise-applications.png)

3. 若要新增新的應用程式，請按一下對話方塊頂端的 [新增應用程式]  按鈕。

    ![新增應用程式按鈕](common/add-new-app.png)

4. 在搜尋方塊中，輸入 **Palo Alto 網路 - 系統管理 UI**，從結果面板選取 [Palo Alto 網路 - 系統管理 UI]  ，然後按一下 [新增]  按鈕以新增應用程式。

     ![結果清單中的 [Palo Alto 網路 - 系統管理 UI]](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>設定和測試 Azure AD 單一登入

在本節中，您會以名為 **Britta Simon** 的測試使用者身分，設定及測試與 Palo Alto Networks - Admin UI 搭配運作的 Azure AD 單一登入。
若要讓單一登入能夠運作，必須建立 Azure AD 使用者與 Palo Alto Networks - Admin UI 中相關使用者之間的連結關聯性。

若要搭配 Palo Alto 網路 - 系統管理 UI 設定及測試 Azure AD 單一登入，您需要完成下列建置組塊：

1. **[設定 Azure AD 單一登入](#configure-azure-ad-single-sign-on)** - 讓您的使用者能夠使用此功能。
2. **[設定 Palo Alto Networks - Admin UI 單一登入](#configure-palo-alto-networks---admin-ui-single-sign-on)** - 在應用程式端設定單一登入設定。
3. **[建立 Azure AD 測試使用者](#create-an-azure-ad-test-user)** - 使用 Britta Simon 測試 Azure AD 單一登入。
4. **[指派 Azure AD 測試使用者](#assign-the-azure-ad-test-user)** - 讓 Britta Simon 能夠使用 Azure AD 單一登入。
5. **[建立 Palo Alto Networks - Admin UI 測試使用者](#create-palo-alto-networks---admin-ui-test-user)** ：使 Palo Alto Networks - Admin UI 中對應的 Britta Simon 連結到該使用者在 Azure AD 中的代表項目。
6. **[測試單一登入](#test-single-sign-on)** ，驗證組態是否能運作。

### <a name="configure-azure-ad-single-sign-on"></a>設定 Azure AD 單一登入

在本節中，您會在 Azure 入口網站中啟用 Azure AD 單一登入。

若要搭配 Palo Alto Networks - Admin UI 設定 Azure AD 單一登入，請執行下列步驟：

1. 在 [Azure 入口網站](https://portal.azure.com/)中的 [Palo Alto Networks - Admin UI]  應用程式整合頁面上，選取 [單一登入]  。

    ![設定單一登入連結](common/select-sso.png)

2. 在 [選取單一登入方法]  對話方塊中，選取 [SAML/WS-Fed]  模式以啟用單一登入。

    ![單一登入選取模式](common/select-saml-option.png)

3. 在 [以 SAML 設定單一登入] 頁面上，按一下 [編輯] 圖示以開啟 [基本 SAML 設定] 對話方塊。   

    ![編輯基本 SAML 組態](common/edit-urls.png)

4. 在 [基本 SAML 組態]  區段上，執行下列步驟：

    ![[Palo Alto 網路 - 系統管理 UI 網域和 URL] 單一登入資訊](common/sp-identifier-reply.png)

    a. 在 [登入 URL]  文字方塊中，以下列模式輸入 URL︰`https://<Customer Firewall FQDN>/php/login.php`

    b. 在 [識別碼]  方塊中，使用下列模式輸入 URL：`https://<Customer Firewall FQDN>:443/SAML20/SP`

    c. 在 [回覆 URL]  文字方塊中，以下列格式輸入判斷提示取用者服務 (ACS) URL：`https://<Customer Firewall FQDN>:443/SAML20/SP/ACS`

    > [!NOTE]
    > 這些都不是真正的值。 使用實際的「單一登入 URL」、「識別碼」及「回覆 URL」來更新這些值。 請連絡 [Palo Alto 網路 - 系統管理 UI 用戶端支援小組](https://support.paloaltonetworks.com/support) \(英文\) 以取得這些值。 您也可以參考 Azure 入口網站中**基本 SAML 組態**區段所示的模式。

5. Palo Alto 網路 - 系統管理 UI 應用程式需要特定格式的 SAML 判斷提示。 設定此應用程式的下列宣告。 您可以在應用程式整合頁面的 [使用者屬性]  區段中，管理這些屬性的值。 在 [以 SAML 設定單一登入]  頁面上，按一下 [編輯]  按鈕以開啟 [使用者屬性]  對話方塊。

    ![image](common/edit-attribute.png)

   > [!NOTE]
   > 這些屬性值只是範例，因此您必須對應 *username* 和 *adminrole* 的適當值。 此外還有另一個選用屬性 *accessdomain*，可用來限制管理員對防火牆上特定虛擬系統的存取。
   >

6. 在 [使用者屬性]  對話方塊的 [使用者宣告]  區段中，如上圖所示設定 SAML 權杖屬性，然後執行下列步驟：

    | 名稱 |  來源屬性|
    | --- | --- |
    | username | user.userprincipalname |
    | adminrole | customadmin |
    | | |

    a. 按一下 [新增宣告]  以開啟 [管理使用者宣告]  對話方塊。

    ![映像](common/new-save-attribute.png)

    ![映像](common/new-attribute-details.png)

    b. 在 [名稱]  文字方塊中，輸入該資料列所顯示的屬性名稱。

    c. 讓 [命名空間]  保持空白。

    d. 選取 [來源] 作為 [屬性]  。

    e. 在 [來源屬性]  清單中，輸入該資料列所顯示的屬性值。

    f. 按一下 [確定]  。

    g. 按一下 [檔案]  。

    > [!NOTE]
    > 如需關於這些屬性的詳細資訊，請參閱下列文章：
    > * [系統管理 UI (adminrole) 的系統管理角色設定檔](https://www.paloaltonetworks.com/documentation/80/pan-os/pan-os/firewall-administration/manage-firewall-administrators/configure-an-admin-role-profile)
    > * [系統管理 UI (accessdomain) 的裝置存取網域](https://www.paloaltonetworks.com/documentation/80/pan-os/web-interface-help/device/device-access-domain)

7. 在 [以 SAML 設定單一登入]  頁面的 [SAML 簽署憑證]  區段中按一下 [下載]  ，以依據您的需求從指定選項下載**同盟中繼資料 XML**，並儲存在您的電腦上。

    ![憑證下載連結](common/metadataxml.png)

8. 在 [設定 Palo Alto Networks - Admin UI]  區段上，依據您的需求複製適當的 URL。

    ![複製組態 URL](common/copy-configuration-urls.png)

    a. 登入 URL

    b. Azure AD 識別碼

    c. 登出 URL

### <a name="configure-palo-alto-networks---admin-ui-single-sign-on"></a>設定 Palo Alto Networks - Admin UI 單一登入

1. 以系統管理員的身分，在新視窗中開啟 Palo Alto 網路防火牆的系統管理 UI。

2. 選取 [裝置]  索引標籤。

    ![[裝置] 索引標籤](./media/paloaltoadmin-tutorial/tutorial_paloaltoadmin_admin1.png)

3. 在左窗格中選取 [SAML 身分識別提供者]  ，然後選取 [匯入]  以匯入中繼資料檔案。

    ![匯入中繼資料檔案按鈕](./media/paloaltoadmin-tutorial/tutorial_paloaltoadmin_admin2.png)

4. 在 [SAML 身分識別提供者伺服器設定檔匯入]  視窗中，執行下列動作：

    ![[SAML 身分識別提供者伺服器設定檔匯入] 視窗](./media/paloaltoadmin-tutorial/tutorial_paloaltoadmin_idp.png)

    a. 在 [設定檔名稱]  方塊中提供名稱，例如 **AzureAD 系統管理 UI**。
    
    b. 在 [身分識別提供者中繼資料]  下選取 [瀏覽]  ，然後選取您先前從 Azure 入口網站下載的 metadata.xml 檔案。
    
    c. 清除 [驗證身分識別提供者憑證]  核取方塊。
    
    d. 選取 [確定]  。
    
    e. 若要認可防火牆的組態，請選取 [認可]  。

5. 在左窗格中選取 [SAML 身分識別提供者]  ，然後選取您在先前的步驟中建立的 SAML 身分識別提供者設定檔 (例如 **AzureAD 系統管理 UI**)。

    ![SAML 身分識別提供者設定檔](./media/paloaltoadmin-tutorial/tutorial_paloaltoadmin_idp_select.png)

6. 在 [SAML 身分識別提供者伺服器設定檔]  視窗中，執行下列動作：

    ![[SAML 身分識別提供者伺服器設定檔] 視窗](./media/paloaltoadmin-tutorial/tutorial_paloaltoadmin_slo.png)
  
    a. 在 [身分識別提供者 SLO URL]  方塊中，將先前匯入的 SLO URL 取代為下列 URL：`https://login.microsoftonline.com/common/wsfederation?wa=wsignout1.0`
  
    b. 選取 [確定]  。

7. 在 Palo Alto 網路防火牆的系統管理 UI 中選取 [裝置]  ，然後選取 [管理員角色] 

8. 選取 [新增]  按鈕。

9. 在 [管理員角色設定檔]  視窗的 [名稱]  方塊中，提供管理員角色的名稱 (例如 **fwadmin**)。 管理員角色名稱應符合身分識別提供者所傳送的 SAML 管理員角色屬性名稱。 在 Azure 入口網站的 [使用者屬性]  中已建立管理員角色名稱和值。

    ![設定 Palo Alto 網路管理員角色](./media/paloaltoadmin-tutorial/tutorial_paloaltoadmin_adminrole.png)
  
10. 在防火牆的系統管理 UI 上選取 [裝置]  ，然後選取 [驗證設定檔]  。

11. 選取 [新增]  按鈕。

12. 在 [驗證設定檔]  視窗中，執行下列動作： 

    ![[驗證設定檔] 視窗](./media/paloaltoadmin-tutorial/tutorial_paloaltoadmin_authentication_profile.png)

    a. 在 [名稱]  方塊中提供名稱，例如 **AzureSAML_Admin_AuthProfile**。

    b. 選取 [類型]  下拉式清單中，選取 [SAML]  。 

    c. 在 [IdP 伺服器設定檔]  下拉式清單中，選取適當的 SAML 身分識別提供者伺服器設定檔 (例如 **AzureAD 系統管理 UI**)。

    c. 選取 [啟用單一登出]  核取方塊。

    d. 在 [管理員角色屬性]  方塊中輸入屬性名稱，例如 **adminrole**。

    e. 選取 [進階]  索引標籤，然後在 [允許清單]  下選取 [新增]  。

    ![[進階] 索引標籤上的 [新增] 按鈕](./media/paloaltoadmin-tutorial/tutorial_paloaltoadmin_allowlist.png)

    f. 選取 [全部]  核取方塊，或選取可以使用此設定檔進行驗證的特定使用者和群組。  
    當使用者進行驗證時，防火牆會針對此清單中的項目比對相關聯的使用者名稱或群組。 如果您未新增項目，則無法驗證任何使用者。

    g. 選取 [確定]  。

13. 若要使用 Azure 讓管理員能夠使用 SAML SSO，請選取 [裝置]   > [設定]  。 在 [設定]  窗格中選取 [管理]  索引標籤，然後選取 [驗證設定]  下的 [設定]  (「齒輪」) 按鈕。

    ![[設定] 按鈕](./media/paloaltoadmin-tutorial/tutorial_paloaltoadmin_authsetup.png)

14. 選取您在 [驗證設定檔] 視窗中建立的 SAML 驗證設定檔 (例如 **AzureSAML_Admin_AuthProfile**)。

    ![[驗證設定檔] 欄位](./media/paloaltoadmin-tutorial/tutorial_paloaltoadmin_authsettings.png)

15. 選取 [確定]  。

16. 若要認可組態，請選取 [認可]  。

### <a name="create-an-azure-ad-test-user"></a>建立 Azure AD 測試使用者

本節的目標是要在 Azure 入口網站中建立一個名為 Britta Simon 的測試使用者。

1. 在 Azure 入口網站的左窗格中，依序選取 [Azure Active Directory]  、[使用者]  和 [所有使用者]  。

    ![[使用者和群組] 與 [所有使用者] 連結](common/users.png)

2. 在畫面頂端選取 [新增使用者]  。

    ![[新增使用者] 按鈕](common/new-user.png)

3. 在 [使用者] 屬性中，執行下列步驟。

    ![[使用者] 對話方塊](common/user-properties.png)

    a. 在 [名稱]  欄位中，輸入 **BrittaSimon**。
  
    b. 在 [使用者名稱]  欄位中，輸入 **brittasimon\@yourcompanydomain.extension**  
    例如， BrittaSimon@contoso.com

    c. 選取 [顯示密碼]  核取方塊，然後記下 [密碼] 方塊中顯示的值。

    d. 按一下頁面底部的 [新增]  。

### <a name="assign-the-azure-ad-test-user"></a>指派 Azure AD 測試使用者

在本節中，您會將 Palo Alto 網路 - 系統管理 UI 的存取權授與 Britta Simon，讓她能夠使用 Azure 單一登入。

1. 在 Azure 入口網站中，依序選取 [企業應用程式]  、[所有應用程式]  及 [Palo Alto Networks - Admin UI]  。

    ![企業應用程式刀鋒視窗](common/enterprise-applications.png)

2. 在應用程式清單中，選取 [Palo Alto 網路 - 系統管理 UI]  。

    ![應用程式清單中的 [Palo Alto 網路 - 系統管理 UI] 連結](common/all-applications.png)

3. 在左側功能表中，選取 [使用者和群組]  。

    ![[使用者和群組] 連結](common/users-groups-blade.png)

4. 按一下 [新增使用者]  按鈕，然後在 [新增指派]  對話方塊中，選取 [使用者和群組]  。

    ![[新增指派] 窗格](common/add-assign-user.png)

5. 在 [使用者和群組]  對話方塊的 [使用者] 清單中，選取 [Britta Simon]  ，然後按一下畫面底部的 [選取]  按鈕。

6. 如果您預期使用 SAML 判斷提示中的任何角色值，請在 [選取角色]  對話方塊的清單中選取適當使用者角色，然後按一下畫面底部的 [選取]  按鈕。

7. 在 [新增指派]  對話方塊中，按一下 [指派]  按鈕。

### <a name="create-palo-alto-networks---admin-ui-test-user"></a>建立 Palo Alto Networks - Admin UI 測試使用者

Palo Alto 網路 - 系統管理 UI 支援 Just-In-Time 使用者佈建。 如果使用者尚不存在，在成功驗證之後即會在系統中自動建立。 您不需要手動建立使用者。

### <a name="test-single-sign-on"></a>測試單一登入

在本節中，您會使用存取面板來測試您的 Azure AD 單一登入設定。

當您在存取面板中按一下 Palo Alto Networks - Admin UI 圖格時，系統應該會將您自動登入您設定 SSO 的 Palo Alto Networks - Admin UI。 如需「存取面板」的詳細資訊，請參閱[存取面板簡介](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)。

## <a name="additional-resources"></a>其他資源

- [如何與 Azure Active Directory 整合 SaaS 應用程式的教學課程清單](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [什麼是 Azure Active Directory 中的條件式存取？](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
