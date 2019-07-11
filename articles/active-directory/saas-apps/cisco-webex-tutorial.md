---
title: 教學課程：Azure Active Directory 與 Cisco Webex Meetings 整合 | Microsoft Docs
description: 了解如何設定 Azure Active Directory 與 Cisco Webex Meetings 之間的單一登入。
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 26704ca7-13ed-4261-bf24-fd6252e2072b
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/01/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: ec8c2fc2c2dbbfd2c98184215f696aaf18a87193
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "67105541"
---
# <a name="tutorial-azure-active-directory-integration-with-cisco-webex-meetings"></a>教學課程：Azure Active Directory 與 Cisco Webex Meetings 整合

在本教學課程中，您會了解如何整合 Cisco Webex Meetings 與 Azure Active Directory (Azure AD)。
將 Cisco Webex Meetings 與 Azure AD 整合可提供下列優點：

* 您可以在 Azure AD 中控制可存取 Cisco Webex Meetings 的人員。
* 您可以讓使用者使用其 Azure AD 帳戶自動登入 Cisco Webex Meetings (單一登入)。
* 您可以在 Azure 入口網站中集中管理您的帳戶。

若您想了解 SaaS app 與 Azure AD 整合的更多詳細資訊，請參閱 [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)。
如果您沒有 Azure 訂用帳戶，請在開始之前先[建立免費帳戶](https://azure.microsoft.com/free/)。

## <a name="prerequisites"></a>必要條件

若要設定 Azure AD 與 Cisco Webex Meetings 的整合，您需要下列項目：

* Azure AD 訂用帳戶。 如果您沒有 Azure AD 環境，您可以在[這裡](https://azure.microsoft.com/pricing/free-trial/)取得一個月的試用帳戶
* 已啟用 Cisco Webex Meetings 單一登入的訂用帳戶

## <a name="scenario-description"></a>案例描述

在本教學課程中，您會在測試環境中設定和測試 Azure AD 單一登入。

* Cisco Webex Meetings 支援由 **SP** 起始的 SSO

* Cisco Webex Meetings 支援 **Just In Time** 使用者佈建

## <a name="adding-cisco-webex-meetings-from-the-gallery"></a>從資源庫新增 Cisco Webex Meetings

若要設定將 Cisco Webex Meetings 整合到 Azure AD 中，您必須從資源庫將 Cisco Webex Meetings 新增到受控 SaaS 應用程式清單。

**若要從資源庫新增 Cisco Webex Meetings，請執行下列步驟：**

1. 在 **[Azure 入口網站](https://portal.azure.com)** 的左方瀏覽窗格中，按一下 [Azure Active Directory]  圖示。

    ![Azure Active Directory 按鈕](common/select-azuread.png)

2. 瀏覽至 [企業應用程式]  ，然後選取 [所有應用程式]  選項。

    ![企業應用程式刀鋒視窗](common/enterprise-applications.png)

3. 若要新增新的應用程式，請按一下對話方塊頂端的 [新增應用程式]  按鈕。

    ![新增應用程式按鈕](common/add-new-app.png)

4. 在搜尋方塊中，輸入 **Cisco Webex Meetings**，從結果面板中選取 [Cisco Webex Meetings]  ，然後按一下 [新增]  按鈕以新增應用程式。

     ![結果清單中的 Cisco Webex Meetings](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>設定和測試 Azure AD 單一登入

在本節中，您會以名為 **Britta Simon** 的測試使用者為基礎，設定及測試與 Cisco Webex Meetings 搭配運作的 Azure AD 單一登入。
若要讓單一登入能夠運作，必須建立 Azure AD 使用者與 Cisco Webex Meetings 中相關使用者之間的連結關聯性。

若要設定及測試與 Cisco Webex Meetings 搭配運作的 Azure AD 單一登入，您需要完成下列構成要素：

1. **[設定 Azure AD 單一登入](#configure-azure-ad-single-sign-on)** - 讓您的使用者能夠使用此功能。
2. **[設定 Cisco Webex Meetings 單一登入](#configure-cisco-webex-meetings-single-sign-on)** - 在應用程式端設定單一登入設定。
3. **[建立 Azure AD 測試使用者](#create-an-azure-ad-test-user)** - 使用 Britta Simon 測試 Azure AD 單一登入。
4. **[指派 Azure AD 測試使用者](#assign-the-azure-ad-test-user)** - 讓 Britta Simon 能夠使用 Azure AD 單一登入。
5. **[建立 Cisco Webex Meetings 測試使用者](#create-cisco-webex-meetings-test-user)** - 在 Cisco Webex Meetings 中建立一個與 Azure AD 中代表 Britta Simon 之使用者連結的 Britta Simon 對應項目。
6. **[測試單一登入](#test-single-sign-on)** ，驗證組態是否能運作。

### <a name="configure-azure-ad-single-sign-on"></a>設定 Azure AD 單一登入

在本節中，您會在 Azure 入口網站中啟用 Azure AD 單一登入。

若要設定與 Cisco Webex Meetings 搭配運作的 Azure AD 單一登入，請執行下列步驟：

1. 在 [Azure 入口網站](https://portal.azure.com/)的 [Cisco Webex Meetings]  應用程式整合頁面上，選取 [單一登入]  。

    ![設定單一登入連結](common/select-sso.png)

2. 在 [選取單一登入方法]  對話方塊中，選取 [SAML/WS-Fed]  模式以啟用單一登入。

    ![單一登入選取模式](common/select-saml-option.png)

3. 在 Azure 入口網站的 [以 SAML 設定單一登入]  頁面上，按一下 [編輯]  圖示以開啟 [基本 SAML 設定]  對話方塊。

    ![編輯基本 SAML 組態](common/edit-urls.png)

4. 在 [基本 SAML 組態]  區段上，執行下列步驟，以上傳已下載的**服務提供者中繼資料**檔案並設定應用程式：

    >[!Note]
    >您會取得「服務提供者中繼資料」檔案，稍後在本教學課程的＜設定 Cisco Webex Meetings 單一登入＞  一節中會有說明。 

    a. 按一下 [上傳中繼資料檔案]  。

    ![上傳中繼資料檔案](common/upload-metadata.png)

    b. 按一下**資料夾圖示**以選取中繼資料檔案，然後按一下 [上傳]  。

    ![選擇中繼資料檔案](common/browse-upload-metadata.png)

    c. 成功完成上傳服務提供者中繼資料檔案之後，[基本 SAML 組態]  區段中會自動填入 [識別碼]  和 [回覆 URL]  值：

    ![Cisco Webex Meetings 網域及 URL 單一登入資訊](common/sp-identifier-reply.png)

    在 [登入 URL]  文字方塊中，貼上透過 SP 中繼資料檔案上傳而自動填入的 [回覆 URL]  值。

5. Cisco Webex Meetings 應用程式會預期要有特定格式的 SAML 判斷提示，因此您必須將自訂屬性對應新增至 SAML 權杖屬性設定。 以下螢幕擷取畫面顯示預設屬性清單。 按一下 [編輯] ****   圖示以新增屬性。

    ![image](common/edit-attribute.png)

6. 從 [使用者宣告]  區段中刪除預設屬性，而 Cisco Webex Meetings 則會預期 SAML 回應中會再多傳回幾個屬性。 在 [使用者屬性]  對話方塊的 [使用者宣告]  區段中，執行下列步驟以設定 SAML 權杖屬性，如下表所示：
    
    | Name | 來源屬性|
    | ---------------|  --------- |
    |   firstname    | user.givenname |
    |   lastname    | user.surname |
    |   電子郵件       | user.mail |
    |   UID    | user.mail |

    a. 按一下 [新增宣告]  以開啟 [管理使用者宣告]  對話方塊。

    ![映像](./media/cisco-webex-tutorial/tutorial-cisco-webex-addnewclaim.png)

    ![映像](common/new-attribute-details.png)

    b. 在 [名稱]  文字方塊中，輸入該資料列所顯示的屬性名稱。

    c. 讓 [命名空間]  保持空白。

    d. 選取 [來源] 作為 [屬性]  。

    e. 在 [來源屬性]  清單中，輸入該資料列所顯示的屬性值。

    f. 按一下 [確定]  。

    g. 按一下 [檔案]  。

7. 在 [以 SAML 設定單一登入]  頁面的 [SAML 簽署憑證]  區段中，按一下 [下載]  以依據您的需求從指定選項下載**同盟中繼資料 XML**，並儲存在您的電腦上。

    ![憑證下載連結](common/metadataxml.png)

8. 在 [安裝 Cisco Webex Meetings]  區段上，依據您的需求複製適當的 URL。

    ![複製組態 URL](common/copy-configuration-urls.png)

    a. 登入 URL

    b. Azure AD 識別碼

    c. 登出 URL

### <a name="configure-cisco-webex-meetings-single-sign-on"></a>設定 Cisco Webex Meetings 單一登入

1. 使用您的系統管理員認證來前往 [Cisco Cloud Collaboration Management](https://www.webex.com/go/connectadmin)。

2. 移至 [Security Settings] \(安全性設定\)  ，然後瀏覽至 [Federated Web SSO Configuration] \(同盟 Web SSO 設定\)  。
 
    ![設定單一登入](./media/cisco-webex-tutorial/tutorial-cisco-webex-10.png)

3. 在 [Federated Web SSO Configuration] \(同盟 Web SSO 設定\)  區段中，執行下列步驟：

    ![設定單一登入](./media/cisco-webex-tutorial/tutorial-cisco-webex-11.png)

    a. 在 [Federation Protocol] \(同盟通訊協定\) 文字方塊中，輸入您通訊協定的名稱。

    b. 按一下 [Import SAML Metadata] \(匯入 SAML 中繼資料\)  連結，以上傳從 Azure 入口網站下載的中繼資料檔案。

    c. 按一下 [Export] \(匯出\)  按鈕以下載「服務提供者中繼資料」檔案，然後在 Azure 入口網站的 [基本 SAML 設定]  區段中上傳此檔案。

    d. 在 [AuthContextClassRef]  文字方塊中，輸入 `urn:oasis:names:tc:SAML:2.0:ac:classes:unspecified`，如果您想要使用 Azure AD 來啟用 MFA，請輸入像 `urn:oasis:names:tc:SAML:2.0:ac:classes:PasswordProtectedTransport;urn:oasis:names:tc:SAML:2.0:ac:classes:X509` 這兩個值

    e. 選取 [Auto Account Creation] \(自動建立帳戶\)  。

    >[!NOTE]
    >若要啟用 **Just-In-Time** 使用者佈建，您必須勾選 [Auto Account Creation] \(自動建立帳戶\)  。 此外，還必須在 SAML 回應中傳遞 SAML 權杖屬性。

    f. 按一下 [檔案]  。 

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

在本節中，您會將 Cisco Webex Meetings 的存取權授與 Britta Simon，讓她能夠使用 Azure 單一登入。

1. 在 Azure 入口網站中，依序選取 [企業應用程式]  、[所有應用程式]  及 [Cisco Webex Meetings]  。

    ![企業應用程式刀鋒視窗](common/enterprise-applications.png)

2. 在應用程式清單中，選取 [Cisco Webex Meetings]  。

    ![應用程式清單中的 [Cisco Webex Meetings] 連結](common/all-applications.png)

3. 在左側功能表中，選取 [使用者和群組]  。

    ![[使用者和群組] 連結](common/users-groups-blade.png)

4. 按一下 [新增使用者]  按鈕，然後在 [新增指派]  對話方塊中，選取 [使用者和群組]  。

    ![[新增指派] 窗格](common/add-assign-user.png)

5. 在 [使用者和群組]  對話方塊的 [使用者] 清單中，選取 [Britta Simon]  ，然後按一下畫面底部的 [選取]  按鈕。

6. 如果您預期使用 SAML 判斷提示中的任何角色值，請在 [選取角色]  對話方塊的清單中選取適當使用者角色，然後按一下畫面底部的 [選取]  按鈕。

7. 在 [新增指派]  對話方塊中，按一下 [指派]  按鈕。

### <a name="create-cisco-webex-meetings-test-user"></a>建立 Cisco Webex Meetings 測試使用者

本節的目標是要在 Cisco Webex Meetings 中建立一個名為 Britta Simon 的使用者。 Cisco Webex Meetings 支援預設會啟用的 **Just-In-Time** 佈建。 在這一節沒有您需要進行的動作項目。 如果 Cisco Webex Meetings 中還沒有任何使用者存在，在您嘗試存取 Cisco Webex Meetings 時就會建立新的使用者。

### <a name="test-single-sign-on"></a>測試單一登入

在本節中，您會使用存取面板來測試您的 Azure AD 單一登入設定。

當您在存取面板中按一下 [Cisco Webex Meetings] 圖格時，應該會自動登入您已設定 SSO 的 Cisco Webex Meetings。 如需「存取面板」的詳細資訊，請參閱[存取面板簡介](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)。

## <a name="additional-resources"></a>其他資源

- [如何與 Azure Active Directory 整合 SaaS 應用程式的教學課程清單](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [什麼是 Azure Active Directory 中的條件式存取？](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

