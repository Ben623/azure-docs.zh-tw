---
title: Microsoft 身分識別平臺開發人員詞彙 |Azure
description: 常用 Microsoft 身分識別平臺開發人員概念和功能的字詞清單。
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 12/13/2019
ms.author: ryanwi
ms.custom: aaddev
ms.reviewer: jmprieur, saeeda, jesakowi, nacanuma
ms.openlocfilehash: ce98d2db86c87ac6aa8fa4872bc076714467d32f
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/13/2020
ms.locfileid: "79263045"
---
# <a name="microsoft-identity-platform-developer-glossary"></a>Microsoft 身分識別平臺開發人員詞彙

本文包含一些核心開發人員概念和術語的定義，當您瞭解使用 Microsoft 身分識別平臺的應用程式開發時，這會很有説明。

## <a name="access-token"></a>存取權杖

由[授權伺服器](#security-token)所簽發的一種[安全性權杖](#authorization-server)，可供[用戶端應用程式](#client-application)用來存取[受保護的資源伺服器](#resource-server)。 權杖通常以[JSON Web 權杖（JWT）][JWT]的形式組成，由[資源擁有](#resource-owner)者授與用戶端的授權，以取得所要求的存取層級。 此權杖中會包含所有適用的主體相關 [宣告](#claim) ，可讓用戶端應用程式以它做為某種形式的認證以存取給定的資源。 這也可讓資源擁有者不必對用戶端公開認證。

根據所提供的認證而定，存取權杖有時會稱為「使用者 + 應用程式」或「僅限應用程式」。 例如，當用戶端應用程式使用：

* [「授權程式碼」授權授與](#authorization-grant)，使用者會先驗證為資源擁有者，將授權委派給用戶端來存取資源。 之後，用戶端會在取得存取權杖時進行驗證。 權杖有時可以更明確地稱為「使用者 + 應用程式」權杖，因為它同時代表授權用戶端應用程式的使用者，以及應用程式。
* [「用戶端認證」授權授與](#authorization-grant)，用戶端會提供唯一的驗證，在沒有資源擁有者驗證/授權的情況下運作，因此這個權杖有時可以稱為「僅限應用程式」權杖。

如需詳細資訊，請參閱[Microsoft 身分識別平臺權杖參考][AAD-Tokens-Claims]。

## <a name="application-id-client-id"></a>應用程式識別碼（用戶端識別碼）

唯一識別碼 Azure AD 會核發給應用程式註冊，它會識別特定應用程式和相關聯的設定。 執行驗證要求時，會使用此應用程式識別碼（[用戶端識別碼](https://tools.ietf.org/html/rfc6749#page-15)），並在開發期間提供給驗證程式庫。 應用程式識別碼（用戶端識別碼）不是秘密。

## <a name="application-manifest"></a>應用程式資訊清單

[Azure 入口網站][AZURE-portal]所提供的功能，它會產生應用程式身分識別設定的 JSON 標記法，用來做為更新其相關聯[應用程式][Graph-App-Resource]和[ServicePrincipal][Graph-Sp-Resource]實體的機制。 如需詳細資訊，請參閱[瞭解 Azure Active Directory 應用程式資訊清單][AAD-App-Manifest]。

## <a name="application-object"></a>應用程式物件

當您在[Azure 入口網站][AZURE-portal]中註冊/更新應用程式時，入口網站會針對該租使用者建立/更新應用程式物件和對應的[服務主體物件](#service-principal-object)。 應用程式物件可全域 (在其能夠存取的所有租用戶中)「定義」應用程式的身分識別組態，並提供範本來「衍生」出其對應的服務主體物件，以在執行階段於本機 (在特定租用戶) 使用。

如需詳細資訊，請參閱[應用程式和服務主體物件][AAD-App-SP-Objects]。

## <a name="application-registration"></a>應用程式註冊

為了讓應用程式能夠整合身分識別和存取管理功能，並將這些功能委派給 Azure AD，您必須向 Azure AD [租用戶](#tenant)註冊應用程式。 當您向 Azure AD 註冊應用程式時，您必須提供應用程式的身分識別組態，以允許它與 Azure AD 整合，並使用如下功能︰

* 使用 Azure AD 身分識別管理和[OpenID connect][OpenIDConnect]通訊協定執行功能來健全地管理單一登入
* [用戶端應用程式](#client-application)透過 OAuth 2.0[授權伺服器](#authorization-server)，對[受保護資源](#resource-server)的代理存取
* [同意架構](#consent) ，根據資源擁有者授權來管理用戶端對受保護資源的存取權。

如需詳細資訊，請參閱[整合應用程式與 Azure Active Directory][AAD-Integrating-Apps] 。

## <a name="authentication"></a>驗證 (authentication)

對憑證者查問合法認證的動作，此動作可做為基礎來建立用於身分識別和存取控制的安全性主體。 例如，在 [OAuth2 授權授與](#authorization-grant)期間，憑證者驗證會根據所使用的授與，填入[資源擁有者](#resource-owner)或[用戶端應用程式](#client-application)的角色。

## <a name="authorization"></a>授權

授與已驗證的安全性主體權限以執行某些工作的動作。 在 Azure AD 程式設計模型中有兩大使用案例︰

* 在 [OAuth2 授權授與](#authorization-grant)流程期間︰當[資源擁有者](#resource-owner)授與授權給[用戶端應用程式](#client-application)，以允許用戶端存取資源擁有者的資源。
* 在用戶端存取資源期間︰和[資源伺服器](#resource-server)所實作的一樣，使用[存取權杖](#claim)所提供的[宣告](#access-token)值來據以做出存取控制決定。

## <a name="authorization-code"></a>授權碼

在四個 OAuth2 [授權授與](#client-application)之一的「授權碼」流程中，由[授權端點](#authorization-endpoint)提供給[用戶端應用程式](#authorization-grant)的短暫「權杖」。 為回應 [資源擁有者](#resource-owner)的驗證，會將授權碼傳回給用戶端應用程式，以指出資源擁有者已委派存取所要求資源的授權。 在流程進行期間稍後的時候，會將授權碼兌換為 [存取權杖](#access-token)。

## <a name="authorization-endpoint"></a>授權端點

[授權伺服器](#authorization-server)所實作的其中一個端點，可用來與[資源擁有者](#resource-owner)互動，以在 OAuth2 授權授與流程期間提供[授權授與](#authorization-grant)。 根據所使用的授權授與流程而定，實際提供的授與會不一樣，這包括[授權碼](#authorization-code)或[安全性權杖](#security-token)。

如需詳細資訊，請參閱 OAuth2 規格的[授權授與類型][OAuth2-AuthZ-Grant-Types]和[授權端點][OAuth2-AuthZ-Endpoint]章節和[OpenIDConnect 規格][OpenIDConnect-AuthZ-Endpoint]。

## <a name="authorization-grant"></a>授權授與

認證，代表[資源擁有](#resource-owner)者存取其受保護資源的[授權](#authorization)，並授與[用戶端應用程式](#client-application)。 用戶端應用程式可以使用[OAuth2 授權架構所定義的四種授與類型][OAuth2-AuthZ-Grant-Types]之一來取得授與，視用戶端類型/需求而定：「授權碼授與」、「用戶端認證授與」、「隱含授與」和「資源擁有者密碼認證授與」。 視所使用的授權授與類型而定，傳回給用戶端的認證會是[存取權杖](#access-token)或[授權碼](#authorization-code) (稍後換成存取權杖)。

## <a name="authorization-server"></a>受保護的資源

如[OAuth2 授權架構][OAuth2-Role-Def]所定義，負責在成功驗證[資源擁有](#resource-owner)者並取得其授權之後，將存取權杖發行至[用戶端](#client-application)的伺服器。 [用戶端應用程式](#client-application)會在執行階段根據 OAuth2 所定義的[授權授與](#authorization-endpoint)，透過其[授權](#token-endpoint)和[權杖](#authorization-grant)端點與授權伺服器互動。

在 Microsoft 身分識別平臺應用程式整合的案例中，Microsoft 識別平臺會為 Azure AD 應用程式和 Microsoft 服務 Api （例如[Microsoft Graph api][Microsoft-Graph]）實行授權伺服器角色。

## <a name="claim"></a>宣告

[安全性權杖](#security-token)中包含宣告，而宣告可提供關於某一實體 (例如[用戶端應用程式](#client-application)或[資源擁有者](#resource-owner)) 的判斷提示給另一個實體 (例如[資源伺服器](#resource-server))。 宣告是轉送權杖主體 (例如，由 [授權伺服器](#authorization-server)驗證的安全性主體) 相關事實的名稱/值組。 給定權杖所提供的宣告取決於幾項變數，包括權杖類型、用來驗證主體的認證類型，以及應用程式組態等。

如需詳細資訊，請參閱[Microsoft 身分識別平臺權杖參考][AAD-Tokens-Claims]。

## <a name="client-application"></a>用戶端應用程式 (client application)

如[OAuth2 授權架構][OAuth2-Role-Def]所定義，這是代表[資源擁有](#resource-owner)者提出受保護資源要求的應用程式。 「用戶端」一詞並不代表任何特定的硬體實作特性 (例如，應用程式執行於伺服器、桌面還是其他裝置)。

用戶端應用程式會向資源擁有者要求[授權](#authorization)，以參與 [OAuth2 授權授與](#authorization-grant)流程，並可代表資源擁有者存取 API/資料。 OAuth2 授權架構會根據用戶端維護其認證機密性的能力，[定義兩種類型的用戶端][OAuth2-Client-Types]：「機密」和「公用」。 應用程式可實作在 Web 伺服器上執行的 [Web 用戶端 (機密)](#web-client)、安裝在裝置上的[原生用戶端 (公用)](#native-client)，或在裝置的瀏覽器中執行的[使用者代理程式型用戶端 (公用)](#user-agent-based-client)。

## <a name="consent"></a>同意

[資源擁有者](#resource-owner)授與授權給[用戶端應用程式](#client-application)，以讓使用特定[權限](#permissions)來代表資源擁有者存取受保護資源的程序。 根據用戶端所要求的權限，會要求系統管理員或使用者同意分別允許其組織/個人資料的存取權。 請注意，在[多租用戶](#multi-tenant-application)案例中，應用程式的[服務主體](#service-principal-object)也會記錄在同意方使用者的租用戶中。

如需詳細資訊，請參閱[同意架構](consent-framework.md)。

## <a name="id-token"></a>識別碼權杖

[授權伺服器的](#authorization-server)[授權端點](#authorization-endpoint)所提供的[OpenID connect][OpenIDConnect-ID-Token] [安全性權杖](#security-token)，其中包含與使用者[資源擁有](#resource-owner)者的驗證有關的[宣告](#claim)。 和存取權杖一樣，識別碼權杖也會以數位簽署的[JSON Web 權杖（JWT）][JWT]來表示。 但識別碼權杖的宣告則不同於存取權杖，它並不會用來進行與資源存取相關的用途，具體來說也就是存取控制。

如需詳細資訊，請參閱[Microsoft 身分識別平臺權杖參考][AAD-Tokens-Claims]。

## <a name="microsoft-identity-platform"></a>Microsoft 身分識別平台

Microsoft 身分識別平台是 Azure Active Directory (Azure AD) 身分識別服務與開發人員平台的演化。 它可讓開發人員建置應用程式以登入所有 Microsoft 身分識別、取得權杖以呼叫 Microsoft Graph、其他 Microsoft API 或開發人員所建置的 API。 它是具有完整功能的平台，由驗證服務、程式庫、應用程式註冊與設定、完整開發人員文件、程式碼範例與其他開發人員內容所組成。 Microsoft 身分識別平台支援業界標準通訊協定，例如 OAuth 2.0 與 OpenID Connect。 如需詳細資訊，請參閱[關於 Microsoft 身分識別平台](about-microsoft-identity-platform.md)。

## <a name="multi-tenant-application"></a>多租用戶應用程式

一種應用程式類別，能讓在任何 Azure AD [租用戶](#consent) (包括用戶端註冊所在之租用戶以外的租用戶) 中佈建的使用者登入和[同意](#tenant)。 [原生用戶端](#native-client)應用程式預設是多租用戶，而 [Web 用戶端](#web-client)和 [Web 資源/API](#resource-server) 應用程式則可以在單一或多租用戶之間做選擇。 相反地，若 Web 應用程式註冊為單一租用戶，則只會允許來自應用程式註冊所在相同租用戶中所佈建之使用者帳戶的登入。

如需詳細資訊，請參閱[如何使用多租使用者應用程式模式登入任何 Azure AD 使用者][AAD-Multi-Tenant-Overview]。

## <a name="native-client"></a>原生用戶端

裝置上原生安裝的 [用戶端應用程式](#client-application) 類型。 所有程式碼都會在裝置上執行，因此裝置會因為無法私下/祕密地儲存認證而被視為「公用」用戶端。 如需詳細資訊，請參閱[OAuth2 用戶端類型和設定檔][OAuth2-Client-Types]。

## <a name="permissions"></a>權限

透過宣告權限要求來取得[資源伺服器](#client-application)存取權的[用戶端應用程式](#resource-server)。 其可用類型有兩種︰

* 「委派」權限，其可使用登入的[資源擁有者](#scopes)的委派授權指定[範圍型](#resource-owner)存取，而在執行階段會以用戶端[存取權杖](#claim)中的 ["scp" 宣告](#access-token)形式顯示給資源。
* 「應用程式」權限，其可使用用戶端應用程式的認證/身分識別指定[角色型](#roles)存取，而在執行階段會以用戶端存取權杖中的[「角色」宣告](#claim)形式顯示給資源。

權限也會在 [同意](#consent) 程序期間出現，讓系統管理員或資源擁有者有機會允許/拒絕用戶端對其租用戶中的資源進行存取。

許可權要求是在[Azure 入口網站][AZURE-portal]中應用程式的 [ **API 許可權**] 頁面上設定，方法是選取所需的 [委派的許可權] 和 [應用程式許可權] （後者需要全域管理員角色的成員資格）。 [公用用戶端](#client-application)無法安全地維護認證，因此它只能要求委派的權限，而[機密用戶端](#client-application)則能夠要求委派的權限和應用程式權限。 用戶端的[應用程式物件](#application-object)會將宣告的許可權儲存在其[requiredResourceAccess 屬性][Graph-App-Resource]中。

## <a name="resource-owner"></a>資源擁有者

如[OAuth2 授權架構][OAuth2-Role-Def]所定義，實體能夠授與受保護資源的存取權。 個人資源擁有者就是指使用者。 例如，當[用戶端應用程式](#client-application)想要透過[Microsoft Graph API][Microsoft-Graph]存取使用者的信箱時，它需要信箱的資源擁有者所擁有的許可權。

## <a name="resource-server"></a>資源伺服器

如[OAuth2 授權架構][OAuth2-Role-Def]所定義，這是裝載受保護資源的伺服器，能夠接受並回應出示[存取權杖](#access-token)的[用戶端應用程式](#client-application)所提出的受保護資源要求。 它也稱為「受保護的資源伺服器」或「資源應用程式」。

資源伺服器會使用 OAuth 2.0 授權架構公開 API，並透過[範圍](#scopes)和[角色](#roles)強制執行其受保護資源的存取權。 範例包括[MICROSOFT GRAPH API][Microsoft-Graph] ，可提供 Azure AD 租使用者資料的存取權，以及提供存取資料（例如郵件和行事曆）的 Office 365 api。 

和用戶端應用程式一樣，資源應用程式的身分識別組態是透過 Azure AD 租用戶中的 [註冊](#application-registration) 程序來建立，可提供應用程式和服務主體物件。 某些 Microsoft 提供的 Api （例如 Microsoft Graph API）在布建期間，會在所有租使用者中提供預先註冊的服務主體。

## <a name="roles"></a>角色

和[範圍](#scopes)一樣，角色會提供方法讓[資源伺服器](#resource-server)控管其受保護資源的存取權。 角色有兩種類型：「使用者」角色會為需要資源存取權的使用者/群組實作角色型存取控制，「應用程式」角色則會為需要存取權的 [用戶端應用程式](#client-application) 實作相同的存取控制。

角色是資源定義的字串（例如「支出核准者」、「唯讀」、「目錄. ReadWrite」），會透過資源的[應用程式資訊清單](#application-manifest)在[Azure 入口網站][AZURE-portal]中管理，並儲存在資源的[appRoles 屬性][Graph-Sp-Resource]中。 Azure 入口網站也可用來將使用者指派給「使用者」角色，並設定用戶端[應用程式權限](#permissions)以存取「應用程式」角色。

如需 Microsoft Graph API 所公開之應用程式角色的詳細討論，請參閱[圖形 API 許可權範圍][Graph-Perm-Scopes]。 如需逐步執行的範例，請參閱[使用 RBAC 和 Azure 入口網站來管理存取權][AAD-RBAC]。

## <a name="scopes"></a>範圍

和[角色](#roles)一樣，範圍會提供方法讓[資源伺服器](#resource-server)控管其受保護資源的存取權。 範圍是用來針對已由其擁有者提供資源委派存取權的[用戶端應用程式](#client-application)，執行[範圍型][OAuth2-Access-Token-Scopes]存取控制。

範圍是資源定義的字串（例如 "Mail. Read"、"Directory. ReadWrite. All"），會透過資源的[應用程式資訊清單](#application-manifest)在[Azure 入口網站][AZURE-portal]中管理，並儲存在資源的[oauth2Permissions 屬性][Graph-Sp-Resource]中。 Azure 入口網站也可用來將用戶端應用程式[委派的權限](#permissions)設定為存取某個範圍。

命名慣例的最佳作法是使用「resource.operation.constraint」格式。 如需 Microsoft Graph API 所公開之範圍的詳細討論，請參閱[圖形 API 許可權範圍][Graph-Perm-Scopes]。 如需 Office 365 服務所公開的範圍，請參閱[office 365 API 許可權參考][O365-Perm-Ref]。

## <a name="security-token"></a>安全性權杖

包含 OAuth2 權杖或 SAML 2.0 判斷提示等宣告的已簽署文件。 對於 OAuth2[授權授](#authorization-grant)與而言，[存取權杖](#access-token)（OAuth2）和[識別碼權杖](https://openid.net/specs/openid-connect-core-1_0.html#IDToken)是安全性權杖的類型，兩者都實作為[JSON Web 權杖（JWT）][JWT]。

## <a name="service-principal-object"></a>服務主體物件

當您在[Azure 入口網站][AZURE-portal]中註冊/更新應用程式時，入口網站會針對該租使用者建立/更新[應用程式物件](#application-object)和對應的服務主體物件。 應用程式物件可全域 (在相關聯的應用程式已獲授與存取權的所有租用戶中)「定義」應用程式的身分識別組態，並可做為範本來「衍生」出其對應的服務主體物件，以在執行階段於本機 (在特定租用戶) 使用。

如需詳細資訊，請參閱[應用程式和服務主體物件][AAD-App-SP-Objects]。

## <a name="sign-in"></a>登入

[用戶端應用程式](#client-application)會透過此程序起始使用者驗證並擷取相關狀態，以便取得[安全性權杖](#security-token)並將應用程式工作階段侷限在該狀態。 狀態中可包含使用者設定檔資訊之類的構件，以及衍生自權杖宣告的資訊。

應用程式的登入功能通常會用來實作單一登入 (SSO)。 也可能會在這之前加上「註冊」功能，以做為進入點來讓使用者取得應用程式的存取權 (在第一次登入時)。 註冊功能可用來收集並保存使用者專屬的其他狀態，而且可能需要 [使用者同意](#consent)。

## <a name="sign-out"></a>登出

讓使用者變成未驗證狀態的程序，以便解除使用者在[登入](#client-application)期間與[用戶端應用程式](#sign-in)工作階段相關聯的狀態。

## <a name="tenant"></a>tenant

Azure AD 目錄的執行個體會稱為 Azure AD 租用戶。 它提供數個功能，包括︰

* 整合式應用程式的登錄服務
* 驗證使用者帳戶和已註冊的應用程式
* 支援各種通訊協定 (包括 OAuth2 和 SAML) 所需的 REST 端點包括[授權端點](#authorization-endpoint)、[權杖端點](#token-endpoint)以及[多租用戶應用程](#multi-tenant-application)所使用的「通用」端點。

Azure AD 租用戶會在註冊期間建立/與 Azure 和 Office 365 訂用帳戶產生關聯，藉此提供訂用帳戶的「身分識別與存取權管理」功能。 Azure 訂用帳戶管理員也可以透過 Azure 入口網站，建立其他 Azure AD 租用戶。 如需可存取租使用者之各種方式的詳細資訊，請參閱[如何取得 Azure Active Directory 的租][AAD-How-To-Tenant]使用者。 如需訂用帳戶與 Azure AD 租使用者之間關聯性的詳細資訊，請參閱[Azure 訂用帳戶如何與 Azure Active Directory 相關聯][AAD-How-Subscriptions-Assoc]。

## <a name="token-endpoint"></a>權杖端點

[授權伺服器](#authorization-server)為了支援 OAuth2 [授權授與](#authorization-grant)所實作的其中一個端點。 視授與而定，它可以在與[OpenID connect][OpenIDConnect]通訊協定搭配使用時，用來取得[用戶端](#client-application)的[存取權杖](#access-token)（和相關的「重新整理」權杖）或[識別碼權杖](#id-token)。

## <a name="user-agent-based-client"></a>使用者代理程式型用戶端

一種[用戶端應用程式](#client-application)，可從 Web 伺服器下載程式碼並在使用者代理程式 (例如，網頁瀏覽器) 中執行，例如單一頁面應用程式 (SPA)。 所有程式碼都會在裝置上執行，因此裝置會因為無法私下/祕密地儲存認證而被視為「公用」用戶端。 如需詳細資訊，請參閱[OAuth2 用戶端類型和設定檔][OAuth2-Client-Types]。

## <a name="user-principal"></a>使用者主體

和服務主體物件用來表示應用程式執行個體的方式一樣，使用者主體物件是另一種類型的安全性主體，它所代表的是使用者。 Microsoft Graph[使用者資源類型][Graph-User-Resource]會定義使用者物件的架構，包括使用者相關屬性，例如名字和姓氏、使用者主體名稱、目錄角色成員資格等。這會提供使用者身分識別設定，讓 Azure AD 在執行時間建立使用者主體。 使用者主體可用來代表單一登入的已驗證使用者、記錄[同意](#consent)委派，以做出存取控制決策等。

## <a name="web-client"></a>Web 用戶端

一種 [用戶端應用程式](#client-application) ，它會在 Web 伺服器上執行所有程式碼，並且能夠藉由在伺服器上安全地儲存其認證，而當作「機密」用戶端運作。 如需詳細資訊，請參閱[OAuth2 用戶端類型和設定檔][OAuth2-Client-Types]。

## <a name="next-steps"></a>後續步驟

[Microsoft 身分識別平臺開發人員指南][AAD-Dev-Guide]是適用于所有 Microsoft 身分識別平臺開發相關主題的登陸頁面，其中包含[應用程式整合][AAD-How-To-Integrate]的總覽，以及 Microsoft 身分[識別平臺驗證和支援的驗證案例][AAD-Auth-Scenarios]的基本概念。 您也可以在 [GitHub](https://github.com/azure-samples?utf8=%E2%9C%93&q=active%20directory&type=&language=) 上找到如何快速啟動及執行的程式碼範例和教學課程。

使用下列留言區段提供意見反應，並協助改善與設計此內容，包括要求新定義或更新現有定義！

<!--Image references-->

<!--Reference style links -->
[AAD-App-Manifest]:reference-app-manifest.md
[AAD-App-SP-Objects]:app-objects-and-service-principals.md
[AAD-Auth-Scenarios]:authentication-scenarios.md
[AAD-Dev-Guide]:azure-ad-developers-guide.md
[Graph-Perm-Scopes]: /graph/permissions-reference
[Graph-App-Resource]: /graph/api/resources/application
[Graph-Sp-Resource]: /graph/api/resources/serviceprincipal?view=graph-rest-beta
[Graph-User-Resource]: /graph/api/resources/user
[AAD-How-Subscriptions-Assoc]:../fundamentals/active-directory-how-subscriptions-associated-directory.md
[AAD-How-To-Integrate]: ./active-directory-how-to-integrate.md
[AAD-How-To-Tenant]:quickstart-create-new-tenant.md
[AAD-Integrating-Apps]:quickstart-v1-integrate-apps-with-azure-ad.md
[AAD-Multi-Tenant-Overview]:howto-convert-app-to-be-multi-tenant.md
[AAD-Security-Token-Claims]: ./active-directory-authentication-scenarios/#claims-in-azure-ad-security-tokens
[AAD-Tokens-Claims]:access-tokens.md
[AZURE-portal]: https://portal.azure.com
[AAD-RBAC]: ../../role-based-access-control/role-assignments-portal.md
[JWT]: https://tools.ietf.org/html/draft-ietf-oauth-json-web-token-32
[Microsoft-Graph]: https://developer.microsoft.com/graph
[O365-Perm-Ref]: https://msdn.microsoft.com/office/office365/howto/application-manifest
[OAuth2-Access-Token-Scopes]: https://tools.ietf.org/html/rfc6749#section-3.3
[OAuth2-AuthZ-Endpoint]: https://tools.ietf.org/html/rfc6749#section-3.1
[OAuth2-AuthZ-Grant-Types]: https://tools.ietf.org/html/rfc6749#section-1.3
[OAuth2-Client-Types]: https://tools.ietf.org/html/rfc6749#section-2.1
[OAuth2-Role-Def]: https://tools.ietf.org/html/rfc6749#page-6
[OpenIDConnect]: https://openid.net/specs/openid-connect-core-1_0.html
[OpenIDConnect-AuthZ-Endpoint]: https://openid.net/specs/openid-connect-core-1_0.html#AuthorizationEndpoint
[OpenIDConnect-ID-Token]: https://openid.net/specs/openid-connect-core-1_0.html#IDToken
