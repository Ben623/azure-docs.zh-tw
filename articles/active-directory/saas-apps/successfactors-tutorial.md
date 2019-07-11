---
title: 教學課程：Azure Active Directory 與 SuccessFactors 整合 | Microsoft Docs
description: 了解如何設定 Azure Active Directory 與 SuccessFactors 之間的單一登入。
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 32bd8898-c2d2-4aa7-8c46-f1f5c2aa05f1
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 1/3/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: a9526cde92fe2f504207de188fa8f0567ffa580d
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "67089817"
---
# <a name="tutorial-azure-active-directory-integration-with-successfactors"></a>教學課程：Azure Active Directory 與 SuccessFactors 整合

在本教學課程中，您將了解如何整合 SuccessFactors 與 Azure Active Directory (Azure AD)。
SuccessFactors 與 Azure AD 整合提供下列優點：

* 您可以在 Azure AD 中控制可存取 SuccessFactors 的人員。
* 您可以讓使用者使用其 Azure AD 帳戶自動登入 SuccessFactor (單一登入)。
* 您可以在 Azure 入口網站中集中管理您的帳戶。

若您想了解 SaaS app 與 Azure AD 整合的更多詳細資訊，請參閱 [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)。
如果您沒有 Azure 訂用帳戶，請在開始之前先[建立免費帳戶](https://azure.microsoft.com/free/)。

## <a name="prerequisites"></a>必要條件

若要設定 Azure AD 與 SuccessFactors 的整合，您需要下列項目：

* Azure AD 訂用帳戶。 如果您沒有 Azure AD 環境，您可以在[這裡](https://azure.microsoft.com/pricing/free-trial/)取得一個月的試用帳戶
* 已啟用 SuccessFactors 單一登入的訂用帳戶

## <a name="scenario-description"></a>案例描述

在本教學課程中，您會在測試環境中設定和測試 Azure AD 單一登入。

* SuccessFactors 支援 **SP** 起始的 SSO

## <a name="adding-successfactors-from-the-gallery"></a>從資源庫新增 SuccessFactors

若要設定 SuccessFactors 與 Azure AD 的整合，您需要從資源庫將 SuccessFactors 新增到受控 SaaS 應用程式清單。

**若要從資源庫新增 SuccessFactors，請執行下列步驟：**

1. 在 **[Azure 入口網站](https://portal.azure.com)** 的左方瀏覽窗格中，按一下 [Azure Active Directory]  圖示。

    ![Azure Active Directory 按鈕](common/select-azuread.png)

2. 瀏覽至 [企業應用程式]  ，然後選取 [所有應用程式]  選項。

    ![企業應用程式刀鋒視窗](common/enterprise-applications.png)

3. 若要新增新的應用程式，請按一下對話方塊頂端的 [新增應用程式]  按鈕。

    ![新增應用程式按鈕](common/add-new-app.png)

4. 在搜尋方塊中，輸入 **SuccessFactors**，從結果面板中選取 [SuccessFactors]  ，然後按一下 [新增]  按鈕以新增應用程式。

     ![結果清單中的 SuccessFactors](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>設定和測試 Azure AD 單一登入

在本節中，您會以名為 **Britta Simon** 的測試使用者為基礎，設定及測試與 SuccessFactors 搭配運作的 Azure AD 單一登入。
若要讓單一登入能夠運作，必須建立 Azure AD 使用者與 SuccessFactors 中相關使用者之間的連結關聯性。

若要設定及測試對 SuccessFactors 的 Azure AD 單一登入，您需要完成下列建置組塊：

1. **[設定 Azure AD 單一登入](#configure-azure-ad-single-sign-on)** - 讓您的使用者能夠使用此功能。
2. **[設定 SuccessFactors 單一登入](#configure-successfactors-single-sign-on)** - 在應用程式端設定單一登入設定。
3. **[建立 Azure AD 測試使用者](#create-an-azure-ad-test-user)** - 使用 Britta Simon 測試 Azure AD 單一登入。
4. **[指派 Azure AD 測試使用者](#assign-the-azure-ad-test-user)** - 讓 Britta Simon 能夠使用 Azure AD 單一登入。
5. **[建立 SuccessFactors 測試使用者](#create-successfactors-test-user)** - 在 SuccessFactors 中建立一個與 Azure AD 中代表 Britta Simon 之項目連結的對應項目。
6. **[測試單一登入](#test-single-sign-on)** ，驗證組態是否能運作。

### <a name="configure-azure-ad-single-sign-on"></a>設定 Azure AD 單一登入

在本節中，您會在 Azure 入口網站中啟用 Azure AD 單一登入。

若要使用 SuccessFactors 設定 Azure AD 單一登入，請執行下列步驟：

1. 在 [Azure 入口網站](https://portal.azure.com/)的 [SuccessFactors]  應用程式整合頁面上，選取 [單一登入]  。

    ![設定單一登入連結](common/select-sso.png)

2. 在 [選取單一登入方法]  對話方塊中，選取 [SAML/WS-Fed]  模式以啟用單一登入。

    ![單一登入選取模式](common/select-saml-option.png)

3. 在 [以 SAML 設定單一登入] 頁面上，按一下 [編輯] 圖示以開啟 [基本 SAML 設定] 對話方塊。   

    ![編輯基本 SAML 組態](common/edit-urls.png)

4. 在 [基本 SAML 組態]  區段上，執行下列步驟：

    ![SuccessFactors 網域及 URL 單一登入資訊](common/sp-identifier-reply.png)

    a. 在 [登入 URL]  文字方塊中，使用下列模式輸入 URL︰

    | |
    |--|
    | `https://<companyname>.successfactors.com/<companyname>`|
    | `https://<companyname>.sapsf.com/<companyname>`|
    | `https://<companyname>.successfactors.eu/<companyname>`|
    | `https://<companyname>.sapsf.eu`|

    b. 在 [識別碼]  文字方塊中，使用下列模式輸入 URL：

    | |
    |--|
    | `https://www.successfactors.com/<companyname>`|
    | `https://www.successfactors.com`|
    | `https://<companyname>.successfactors.eu`|
    | `https://www.successfactors.eu/<companyname>`|
    | `https://<companyname>.sapsf.com`|
    | `https://hcm4preview.sapsf.com/<companyname>`|
    | `https://<companyname>.sapsf.eu`|
    | `https://www.successfactors.cn`|
    | `https://www.successfactors.cn/<companyname>`|

    c. 在 [回覆 URL]  文字方塊中，以下列模式輸入 URL：

    | |
    |--|
    | `https://<companyname>.successfactors.com/<companyname>`|
    | `https://<companyname>.successfactors.com`|
    | `https://<companyname>.sapsf.com/<companyname>`|
    | `https://<companyname>.sapsf.com`|
    | `https://<companyname>.successfactors.eu/<companyname>`|
    | `https://<companyname>.successfactors.eu`|
    | `https://<companyname>.sapsf.eu`|
    | `https://<companyname>.sapsf.eu/<companyname>`|
    | `https://<companyname>.sapsf.cn`|
    | `https://<companyname>.sapsf.cn/<companyname>`|

    > [!NOTE]
    > 這些都不是真正的值。 使用實際的「單一登入 URL」、「識別碼」及「回覆 URL」來更新這些值。 請連絡 [SuccessFactors 用戶端支援小組](https://www.successfactors.com/content/ssf-site/en/support.html) \(英文\) 以取得這些值。

5. 在 [以 SAML 設定單一登入]  頁面的 [SAML 簽署憑證]  區段中，按一下 [下載]  ，以依據您的需求從指定選項下載 [憑證 (Base64)]  ，並儲存在您的電腦上。

    ![憑證下載連結](common/certificatebase64.png)

6. 在 [設定 SuccessFactors]  區段上，依據您的需求複製適當的 URL。

    ![複製組態 URL](common/copy-configuration-urls.png)

    a. 登入 URL

    b. Azure AD 識別碼

    c. 登出 URL

### <a name="configure-successfactors-single-sign-on"></a>設定 SuccessFactors 單一登入

1. 在不同的網頁瀏覽器視窗中，以系統管理員身分登入您的 **SuccessFactors 管理入口網站**。

2. 造訪 [應用程式安全性]  和原生 [單一登入功能]  。

3. 在 [重設權杖]  中放入任何值，然後按一下 [儲存權杖]  以啟用 SAML SSO。

    ![在應用程式端設定單一登入][11]

    > [!NOTE]
    > 此值是用來作為 on/off 開關。 如果儲存了任何值，SAML SSO 為 ON。 如果儲存了空白值，SAML SSO 為 OFF。

4. 瀏覽至以下螢幕擷取畫面，然後執行下列動作：

    ![在應用程式端設定單一登入][12]
  
    a. 選取 [SAML v2 SSO]  選項按鈕
  
    b. 設定 [SAML Asserting Party Name] \(SAML 判斷提示方名稱\)  (例如 SAML 簽發者 + 公司名稱)。

    c. 在 [簽發者 URL]  文字方塊中，貼上您從 Azure 入口網站複製的 **Azure AD 識別碼**值。

    d. 選取 [判斷提示]  作為 [需要必要簽章]  。

    e. 選取 [啟用]  做為 [啟用 SAML 旗標]  。

    f. 選取 [否]  做為 [登入要求簽章 (SF 產生的/SP/RP)]  。

    g. 選取 [瀏覽器/後置設定檔]  做為 [SAML 設定檔]  。

    h. 選取 [否]  做為 [強制執行憑證有效期間]  。

    i. 複製從 Azure 入口網站下載的憑證檔案內容，然後將它貼到 [SAML Verifying Certificate] \(SAML 驗證憑證\)  文字方塊中。

    > [!NOTE] 
    > 憑證內容必須有開始憑證和結束憑證標籤。

5. 瀏覽至 [SAML V2]，然後執行下列步驟：

    ![在應用程式端設定單一登入][13]

    a. 選取 [是]  做為 [支援 SP 起始的全域登出]  。

    b. 在 [Global Logout Service URL (LogoutRequest destination)] \(全域登出服務 URL (LogoutRequest 目的地)\)  文字方塊中，貼上您從 Azure 入口網站複製的 [登出 URL]  值。

    c. 選取 [否]  做為 [要求 SP 必須加密所有 NameID 元素]  。

    d. 選取 [未指定]  做為 [NameID 格式]  。

    e. 選取 [是]  做為 [啟用 SP 起始的登入 (AuthnRequest)]  。

    f. 在 [Send request as Company-Wide issuer] \(以全公司簽發者身分傳送要求\)  文字方塊中，貼上您從 Azure 入口網站複製的 [登入 URL]  值。

6. 如果您想要讓登入使用者名稱不區分大小寫，請執行下列步驟。

    ![設定單一登入][29]

    a. 造訪 [公司設定]  \(靠近底部)。

    b. 選取 [啟用不區分大小寫使用者名稱]  附近的核取方塊。

    c. 按一下 [檔案]  。

    > [!NOTE]
    > 如果您嘗試啟用此功能，系統就會檢查它建立的 SAML 登入名稱是否重複。 例如，如果客戶有 User1 和 user1 的使用者名稱。 取消區分大小寫功能將會讓這些名稱重複。 系統會提供您錯誤訊息，且將不會啟用此功能。 客戶必須變更其中一個使用者名稱，使其拼寫方式不同。

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

在本節中，您會將 SuccessFactors 的存取權授與 Britta Simon，讓她能夠使用 Azure 單一登入。

1. 在 Azure 入口網站中，依序選取 [企業應用程式]  、[所有應用程式]  及 [SuccessFactors]  。

    ![企業應用程式刀鋒視窗](common/enterprise-applications.png)

2. 在應用程式清單中，選取 [SuccessFactors]  。

    ![應用程式清單中的 [SuccessFactors] 連結](common/all-applications.png)

3. 在左側功能表中，選取 [使用者和群組]  。

    ![[使用者和群組] 連結](common/users-groups-blade.png)

4. 按一下 [新增使用者]  按鈕，然後在 [新增指派]  對話方塊中，選取 [使用者和群組]  。

    ![[新增指派] 窗格](common/add-assign-user.png)

5. 在 [使用者和群組]  對話方塊的 [使用者] 清單中，選取 [Britta Simon]  ，然後按一下畫面底部的 [選取]  按鈕。

6. 如果您預期使用 SAML 判斷提示中的任何角色值，請在 [選取角色]  對話方塊的清單中選取適當使用者角色，然後按一下畫面底部的 [選取]  按鈕。

7. 在 [新增指派]  對話方塊中，按一下 [指派]  按鈕。

### <a name="create-successfactors-test-user"></a>建立 SuccessFactors 測試使用者

若要讓 Azure AD 使用者能夠登入 SuccessFactors，必須將他們佈建到 SuccessFactors。 SuccessFactors 需以手動方式佈建。

若要在 SuccessFactors 建立使用者，您需要連絡 [SuccessFactors 支援小組](https://www.successfactors.com/content/ssf-site/en/support.html)。

### <a name="test-single-sign-on"></a>測試單一登入

在本節中，您會使用存取面板來測試您的 Azure AD 單一登入設定。

當您在存取面板中按一下 [SuccessFactors] 圖格時，應該會自動登入您設定 SSO 的 SuccessFactors。 如需「存取面板」的詳細資訊，請參閱[存取面板簡介](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)。

## <a name="additional-resources"></a>其他資源

- [如何與 Azure Active Directory 整合 SaaS 應用程式的教學課程清單](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [什麼是 Azure Active Directory 中的條件式存取？](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

<!--Image references-->

[11]: ./media/successfactors-tutorial/tutorial_successfactors_07.png
[12]: ./media/successfactors-tutorial/tutorial_successfactors_08.png
[13]: ./media/successfactors-tutorial/tutorial_successfactors_09.png
[29]: ./media/successfactors-tutorial/tutorial_successfactors_10.png
