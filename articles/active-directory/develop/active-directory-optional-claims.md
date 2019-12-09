---
title: 提供 Azure AD 應用程式的選擇性宣告 |Azure
titleSuffix: Microsoft identity platform
description: 如何將自訂或額外的宣告新增至 Azure Active Directory 所簽發的 SAML 2.0 和 JSON Web 權杖（JWT）權杖。
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 07/03/2019
ms.author: ryanwi
ms.reviewer: paulgarn, hirsin
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 323415c18497f19b4c8f29a303b6ec59dfda1885
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/08/2019
ms.locfileid: "74918265"
---
# <a name="how-to-provide-optional-claims-to-your-azure-ad-app"></a>如何：為您的 Azure AD 應用程式提供選擇性宣告

應用程式開發人員可以在其 Azure AD 應用程式中使用選擇性宣告，在傳送至其應用程式的權杖中指定他們想要的宣告。 

您可以使用選擇性宣告來：

- 選取要包含在應用程式之權杖中的額外宣告。
- 變更 Azure AD 在權杖中傳回之特定宣告的行為。
- 新增和存取應用程式的自訂宣告。

如需標準宣告的清單，請參閱[存取權杖](access-tokens.md)和[id_token](id-tokens.md)宣告檔。 

雖然 v1.0 和 v2.0 格式權杖都支援選擇性宣告，以及 SAML 權杖，但它們會在從 v1.0 移至 v2.0 時提供大部分的值。 V2.0 [Microsoft 身分識別平臺端點](active-directory-appmodel-v2-overview.md)的其中一個目標是較小的權杖大小，以確保用戶端能獲得最佳效能。 因此，數個先前包含在存取和識別碼權杖中的宣告在 v2.0 權杖中已不再提供，而必須依據個別應用程式明確提出要求才會提供。

**表 1：適用性**

| 帳戶類型 | v1.0 權杖 | v2.0 權杖  |
|--------------|---------------|----------------|
| 個人 Microsoft 帳戶  | N/A  | 支援的 |
| Azure AD 帳戶      | 支援的 | 支援的 |

## <a name="v10-and-v20-optional-claims-set"></a>v1.0 和 v2.0 選擇性宣告集

以下列出預設可供應用程式使用的一組選擇性宣告。 若要為您的應用程式新增自訂選擇性宣告，請參閱下方的[目錄延伸模組](#configuring-directory-extension-optional-claims)。 將宣告新增至**存取權杖**時，這會套用到應用程式要求*的*存取權杖（Web API），而*不是應用*程式所要求的權杖。 如此可確保無論用戶端存取您的 API，用於驗證 API 的存取權杖都具有正確的資料。

> [!NOTE]
> 這些宣告中大多數都可包含在 v1.0 和 v2.0 權杖的 JWT 中，但不可包含在 SAML 權杖中 (「權杖類型」欄中已註明者除外)。 取用者帳戶支援這些宣告的子集，並在 [使用者類型] 資料行中標示。  列出的許多宣告不會套用至取用者使用者（沒有租使用者，因此 `tenant_ctry` 沒有值）。  

**表2： v1.0 和 v2.0 選用宣告集**

| Name                       |  描述   | 權杖類型 | 使用者類型 | 注意  |
|----------------------------|----------------|------------|-----------|--------|
| `auth_time`                | 上次驗證使用者的時間。 請參閱 OpenID Connect 規格。| JWT        |           |  |
| `tenant_region_scope`      | 資源租用戶的區域 | JWT        |           | |
| `home_oid`                 | 就來賓使用者而言，是使用者主租用戶中的使用者物件識別碼。| JWT        |           | |
| `sid`                      | 會話識別碼，用於每個會話的使用者登出。 | JWT        |  個人和 Azure AD 帳戶。   |         |
| `platf`                    | 裝置平台    | JWT        |           | 限制為可驗證裝置類型的受控裝置。|
| `verified_primary_email`   | 以使用者的 PrimaryAuthoritativeEmail 為來源      | JWT        |           |         |
| `verified_secondary_email` | 以使用者的 SecondaryAuthoritativeEmail 為來源   | JWT        |           |        |
| `enfpolids`                | 強制執行的原則識別碼。 已針對目前使用者評估的原則識別碼清單。 | JWT |  |  |
| `vnet`                     | VNET 規範資訊。 | JWT        |           |      |
| `fwd`                      | IP 位址。| JWT    |   | 新增發出要求之用戶端的原始 IPv4 位址 (位於 VNET 內部時) |
| `ctry`                     | 使用者的國家/地區 | JWT |  | 如果出現且宣告值是兩個字母的國家/地區碼 (例如 FR、JP、SZ 等等)，則 Azure AD 會傳回 `ctry` 選擇性宣告。 |
| `tenant_ctry`              | 資源租用戶的國家/地區 | JWT | | |
| `xms_pdl`          | 慣用資料位置   | JWT | | 針對多地理位置租使用者，這是3個字母的代碼，用來顯示使用者所在的地理區域。 如需詳細資訊，請參閱[關於慣用資料位置的 Azure AD Connect 檔](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsync-feature-preferreddatalocation)。<br/>例如：`APC` 是指亞太地區。 |
| `xms_pl`                   | 使用者慣用語言  | JWT ||如果設定，則為使用者的慣用語言。 在來賓存取案例中，來源是其主租用戶。 格式化 LL-CC (“en-us”)。 |
| `xms_tpl`                  | 租用戶慣用語言| JWT | | 如果設定，則為資源租用戶的慣用語言。 格式化 LL (“en”)。 |
| `ztdid`                    | 全自動部署識別碼 | JWT | | 裝置身分識別，用於 [Windows AutoPilot](https://docs.microsoft.com/windows/deployment/windows-autopilot/windows-10-autopilot) |
| `email`                    | 此使用者可定址的電子郵件 (如果使用者有的話)。  | JWT、SAML | MSA，Azure AD | 如果使用者是租用戶中的來賓，則預設會包含此值。  若為受管理的使用者 (在租用戶中)，則必須透過此選擇性宣告，或使用 OpenID 範圍 (僅限 v2.0) 要求此值。  若為受管理的使用者，電子郵件地址必須設定於 [Office 管理入口網站](https://portal.office.com/adminportal/home#/users)。| 
| `groups`| 群組宣告的選擇性格式 |JWT、SAML| |與[應用程式資訊清單](reference-app-manifest.md)中的 GroupMembershipClaims 設定搭配使用，這也必須一併設定。 如需詳細資訊，請參閱下面的[群組宣告](#Configuring-group-optional claims)。 如需群組宣告的詳細資訊，請參閱[如何設定群組宣告](../hybrid/how-to-connect-fed-group-claims.md)
| `acct`             | 租用戶中的使用者帳戶狀態。 | JWT、SAML | | 如果使用者是租用戶的成員，則值為 `0`。 如果是來賓使用者，則值為 `1`。 |
| `upn`                      | UserPrincipalName 宣告。 | JWT、SAML  |           | 雖然會自動包含此宣告，但在來賓使用者案例中，您可以將它指定為選擇性宣告來附加額外屬性，以修改其行為。  |

### <a name="v20-optional-claims"></a>V2.0 選擇性宣告

這些宣告一律包含在 v1.0 Azure AD 權杖中，但不包含在 v2.0 權杖中（除非有要求）。 這些宣告僅適用于 Jwt （識別碼權杖和存取權杖）。 

**表3：僅限 v2.0 的選擇性宣告**

| JWT 宣告     | Name                            | 描述                                | 注意 |
|---------------|---------------------------------|-------------|-------|
| `ipaddr`      | IP 位址                      | 用戶端的登入來源 IP 位址。   |       |
| `onprem_sid`  | 內部部署安全性識別碼 |                                             |       |
| `pwd_exp`     | 密碼到期時間        | 密碼到時的日期時間。 |       |
| `pwd_url`     | 變更密碼 URL             | 使用者可以瀏覽來變更其密碼的 URL。   |   |
| `in_corp`     | 公司網路內部        | 指出用戶端是否是從公司網路登入的。 如果不是，則不包含宣告。   |  根據 MFA 中的[可信任 IP](../authentication/howto-mfa-mfasettings.md#trusted-ips) 設定。    |
| `nickname`    | 暱稱                        | 使用者的額外名稱 (有別於名字或姓氏)。 | 
| `family_name` | 姓氏                       | 提供使用者物件中所定義之使用者的姓氏、姓氏或家庭名稱。 <br>"family_name":"Miller" | MSA 和 Azure AD 中支援   |
| `given_name`  | 名字                      | 提供使用者的第一個或「指定」名稱，如使用者物件上所設定。<br>"given_name": "Frank"                   | MSA 和 Azure AD 中支援  |
| `upn`         | 使用者主體名稱 | 可與 username_hint 參數搭配使用的使用者識別碼。  不是使用者的持久識別碼，且不應該用於金鑰資料。 | 如需了解宣告的設定，請參閱下方的[額外屬性](#additional-properties-of-optional-claims)。 |

### <a name="additional-properties-of-optional-claims"></a>選擇性宣告的額外屬性

有些選擇性宣告可經由設定來變更傳回宣告的方式。 這些額外的屬性大多數用來協助移轉對資料有不同要求的內部部署應用程式 (例如 `include_externally_authenticated_upn_without_hash` 可協助無法處理 UPN 中井號 (`#`) 的用戶端)

**表4：用於設定選擇性宣告的值**

| 屬性名稱  | 額外屬性名稱 | 描述 |
|----------------|--------------------------|-------------|
| `upn`          |                          | 可同時用於 SAML 和 JWT 回應，以及用於 v1.0 和 v2.0 權杖。 |
|                | `include_externally_authenticated_upn`  | 包含儲存在資源租用戶中的來賓 UPN。 例如，`foo_hometenant.com#EXT#@resourcetenant.com` |             
|                | `include_externally_authenticated_upn_without_hash` | 同上，只是將井號 (`#`) 取代成底線 (`_`)，例如 `foo_hometenant.com_EXT_@resourcetenant.com` |

#### <a name="additional-properties-example"></a>額外屬性範例

```json
 "optionalClaims": 
   {
       "idToken": [ 
             { 
                "name": "upn", 
            "essential": false,
                "additionalProperties": [ "include_externally_authenticated_upn"]  
              }
        ]
}
```

這個 OptionalClaims 物件會導致傳回給用戶端的識別碼權杖包含另一個 UPN，該 UPN 含有額外的主租用戶和資源租用戶資訊。 如果使用者是租用戶中的來賓 (使用不同的 IDP 進行驗證)，則這樣只會變更權杖中的 `upn` 宣告。 

## <a name="configuring-optional-claims"></a>設定選擇性宣告

您可以藉由修改應用程式資訊清單，來為應用程式設定選擇性宣告 (請參閱以下範例)。 如需詳細資訊，請參閱[瞭解 Azure AD 應用程式資訊清單一文](reference-app-manifest.md)。

> [!IMPORTANT]
> 存取權杖**一律**會使用資源的資訊清單（而非用戶端）產生。  因此，在要求中 `...scope=https://graph.microsoft.com/user.read...` 資源是 Graph。  因此，會使用圖形資訊清單（而不是用戶端的資訊清單）來建立存取權杖。  變更應用程式的資訊清單永遠不會導致圖形的權杖看起來不同。  若要驗證您的 `accessToken` 變更是否有效，請為您的應用程式要求權杖，而不是另一個應用程式。  

**範例結構描述：**

```json
"optionalClaims":  
   {
      "idToken": [
            {
                  "name": "auth_time", 
                  "essential": false
             }
      ],
      "accessToken": [
             {
                    "name": "ipaddr", 
                    "essential": false
              }
      ],
      "saml2Token": [
              {
                    "name": "upn", 
                    "essential": false
               },
               {
                    "name": "extension_ab603c56068041afb2f6832e2a17e237_skypeId",
                    "source": "user", 
                    "essential": false
               }
       ]
   }
```

### <a name="optionalclaims-type"></a>OptionalClaims 類型

宣告應用程式所要求的選擇性宣告。 應用程式可以針對它可從安全性權杖服務接收的三種權杖 (識別碼權杖、存取權杖、SAML 2 權杖)，設定要在這每一種權杖中傳回的選擇性宣告。 應用程式可以設定一組要在每個權杖類型中傳回的不同選擇性宣告。 「應用程式」實體的 OptionalClaims 屬性是一個 OptionalClaims 物件。

**表5： OptionalClaims 類型屬性**

| Name        | Type                       | 描述                                           |
|-------------|----------------------------|-------------------------------------------------------|
| `idToken`     | 集合 (OptionalClaim) | 在 JWT 識別碼權杖中傳回的選擇性宣告。 |
| `accessToken` | 集合 (OptionalClaim) | 在 JWT 存取權杖中傳回的選擇性宣告。 |
| `saml2Token`  | 集合 (OptionalClaim) | 在 SAML 權杖中傳回的選擇性宣告。   |

### <a name="optionalclaim-type"></a>OptionalClaim 類型

包含與應用程式或服務主體關聯的選擇性宣告。 [OptionalClaims](https://msdn.microsoft.com/library/azure/ad/graph/api/entity-and-complex-type-reference#optionalclaims-type) 類型的 idToken、accessToken 及 saml2Token 屬性是 OptionalClaim 的集合。
如果特定宣告可支援，您也可以使用 AdditionalProperties 欄位來修改 OptionalClaim 的行為。

**表6： OptionalClaim 類型屬性**

| Name                 | Type                    | 描述                                                                                                                                                                                                                                                                                                   |
|----------------------|-------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `name`                 | Edm.String              | 選擇性宣告的名稱。                                                                                                                                                                                                                                                                           |
| `source`               | Edm.String              | 宣告的來源 (目錄物件)。 有來自延伸模組屬性的預先定義宣告和使用者定義宣告。 如果來源值為 null，宣告便是預先定義的選擇性宣告。 如果來源值為 user，名稱屬性中的值即為來自使用者物件的延伸模組屬性。 |
| `essential`            | Edm.Boolean             | 如果值為 true，就必須要有用戶端指定的宣告，才能確保使用者所要求之特定工作的授權體驗順暢。 預設值為 False。                                                                                                             |
| `additionalProperties` | 集合 (Edm.String) | 宣告的額外屬性。 如果屬性存在於此集合中，它就會修改名稱屬性中所指定之選擇性宣告的行為。                                                                                                                                               |
## <a name="configuring-directory-extension-optional-claims"></a>設定目錄擴充功能的選擇性宣告

除了標準的選擇性宣告集之外，您也可以設定權杖以包含目錄架構延伸。 如需詳細資訊，請參閱[目錄架構延伸](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-directory-schema-extensions)。 此功能可用來附加應用程式可使用的額外使用者資訊 – 例如，使用者已設定的額外識別碼或重要設定選項。 

> [!Note]
> - 目錄架構延伸是一個僅限 Azure AD 的功能，因此如果您的應用程式資訊清單要求自訂延伸模組，而 MSA 使用者登入您的應用程式，則不會傳回這些延伸模組。
> - Azure AD 選擇性宣告僅適用于 Azure AD 延伸模組，不適用於 Microsoft Graph 目錄延伸模組。 這兩個 Api 都需要「`Directory.ReadWriteAll`」許可權，這只能由系統管理員同意。

### <a name="directory-extension-formatting"></a>目錄擴充功能格式

針對延伸模組屬性，請在應用程式資訊清單中使用延伸模組的全名 (格式為：`extension_<appid>_<attributename>`)。 `<appid>` 必須符合要求宣告之應用程式的識別碼。 

在 JWT 內，這些宣告將會以下列名稱格式發出：`extn.<attributename>`。

在 SAML 權杖內，這些宣告則會以下列 URI 格式發出：`http://schemas.microsoft.com/identity/claims/extn.<attributename>`

## <a name="configuring-group-optional-claims"></a>設定群組選擇性宣告

   > [!NOTE]
   > 針對從內部部署同步處理的使用者和群組發出組名的功能是公開預覽。

本節涵蓋 [選擇性宣告] 底下的設定選項，用於將群組宣告中所使用的群組屬性從預設群組 objectID 變更為從內部部署 Windows Active Directory 同步處理的屬性。

> [!IMPORTANT]
> 如需詳細資訊，請參閱[使用 Azure AD 設定應用程式的群組宣告](../hybrid/how-to-connect-fed-group-claims.md)，包括從內部部署屬性對群組宣告公開預覽的重要注意事項。

1. 在入口網站中 > Azure Active Directory-> 應用程式註冊-> 選取 [應用程式-> 資訊清單]

2. 藉由變更 groupMembershipClaim 來啟用群組成員資格宣告

   有效的值為：

   - "All"
   - SecurityGroup
   - DistributionList
   - DirectoryRole

   例如：

   ```json
   "groupMembershipClaims": "SecurityGroup"
   ```

   根據預設，會在群組宣告值中發出群組 Objectid。  若要將宣告值修改為包含內部部署群組屬性，或將宣告類型變更為角色，請使用 OptionalClaims 設定，如下所示：

3. 設定組名 configuration 選擇性宣告。

   如果您想要將權杖中的群組分組以包含 [選擇性宣告] 區段中的內部部署 AD 群組屬性，請指定應套用哪一個權杖類型、所要求的選擇性宣告，以及所需的任何其他屬性。  可以列出多個權杖類型：

   - OIDC 識別碼權杖的 idToken
   - OAuth/OIDC 存取權杖的 accessToken
   - SAML 權杖的 Saml2Token。

   > [!NOTE]
   > Saml2Token 類型同時適用于 SAML 1.1 和 SAML 2.0 格式權杖

   針對每個相關的權杖類型，修改群組宣告以使用資訊清單中的 OptionalClaims 區段。 OptionalClaims 架構如下所示：

   ```json
   {
   "name": "groups",
   "source": null,
   "essential": false,
   "additionalProperties": []
   }
   ```

   | 選擇性宣告架構 | Value |
   |----------|-------------|
   | **檔案名** | 必須是「群組」 |
   | **來源** | 不使用。 省略或指定 null |
   | **基本** | 不使用。 省略或指定 false |
   | **AdditionalProperties** | 其他屬性的清單。  有效的選項為「sam_account_name」、「dns_domain_and_sam_account_name」、「netbios_domain_and_sam_account_name」、「emit_as_roles」 |

   在 additionalProperties 中，只需要「sam_account_name」、「dns_domain_and_sam_account_name」、「netbios_domain_and_sam_account_name」其中一個。  如果有多個，則會使用第一個，並忽略其他任何專案。

   某些應用程式需要角色宣告中的使用者群組資訊。  若要將宣告類型從群組宣告變更為角色宣告，請將 "emit_as_roles" 新增至其他屬性。  群組值會在角色宣告中發出。

   > [!NOTE]
   > 如果使用「emit_as_roles」，則設定為使用者指派的任何應用程式角色都不會出現在角色宣告中。

**範例：** 以 dnsDomainName\sAMAccountName 格式在 OAuth 存取權杖中以組名的形式發出群組

```json
"optionalClaims": {
    "accessToken": [{
        "name": "groups",
        "additionalProperties": ["dns_domain_and_sam_account_name"]
    }]
}
 ```

若要以 netbiosDomain\sAMAccountName 格式發出要傳回的組名作為 SAML 和 OIDC 識別碼權杖中的角色宣告：

```json
"optionalClaims": {
    "saml2Token": [{
        "name": "groups",
        "additionalProperties": ["netbios_name_and_sam_account_name", "emit_as_roles"]
    }],

    "idToken": [{
        "name": "groups",
        "additionalProperties": ["netbios_name_and_sam_account_name", "emit_as_roles"]
    }]
 }

 ```

## <a name="optional-claims-example"></a>選擇性宣告範例

在本節中，您可以逐步進行案例，以了解如何針對應用程式使用選擇性宣告功能。
有多個選項可用來更新應用程式身分識別設定上的屬性，以啟用和設定選擇性宣告：
-   您可以修改應用程式資訊清單。 下列範例會使用這個方法來執行設定。 請先閱讀介紹資訊清單的[了解 Azure AD 應用程式資訊清單](https://docs.microsoft.com/azure/active-directory/develop/active-directory-application-manifest)文件。
-   您也可以撰寫使用[圖形 API](https://docs.microsoft.com/azure/active-directory/develop/active-directory-graph-api)來更新您應用程式的應用程式。 「圖形 API」參考指南中的[實體和複雜類型參考](https://msdn.microsoft.com/library/azure/ad/graph/api/entity-and-complex-type-reference#optionalclaims-type) \(英文\) 可協助您設定選擇性宣告。

**範例：** 在以下範例中，您將修改應用程式的資訊清單，以將宣告新增至要用於應用程式的存取權杖、識別碼權杖及 SAML 權杖中。

1. 登入 [Azure 入口網站](https://portal.azure.com)。
1. 驗證後，在頁面右上角選取您的 Azure AD 租用戶。
1. 從左側選取 [應用程式註冊]。
1. 從清單中找出您要為其設定選擇性宣告的應用程式，然後按一下該應用程式。
1. 按一下應用程式頁面中的 [資訊清單]，以開啟內嵌資訊清單編輯器。 
1. 您可以使用此編輯器直接編輯資訊清單。 資訊清單會依循[應用程式實體](https://docs.microsoft.com/azure/active-directory/develop/reference-app-manifest)的結構描述，並在儲存後會自動設定資訊清單的格式。 新元素會新增至 `OptionalClaims` 屬性。

    ```json
      "optionalClaims": 
      {
            "idToken": [ 
                  { 
                        "name": "upn", 
                        "essential": false, 
                        "additionalProperties": [ "include_externally_authenticated_upn"]  
                  }
            ],
            "accessToken": [ 
                  {
                        "name": "auth_time", 
                        "essential": false
                  }
            ],
            "saml2Token": [ 
                  { 
                        "name": "extension_ab603c56068041afb2f6832e2a17e237_skypeId",
                        "source": "user", 
                        "essential": true
                  }
            ]
      }

    ```

    在此案例中，已將不同的選擇性宣告新增至應用程式可接收的每一種權杖。 識別碼權杖現在將會在完整格式 (`<upn>_<homedomain>#EXT#@<resourcedomain>`) 中包含同盟使用者的 UPN。 其他用戶端為這個應用程式要求的存取權杖，現在將包含 auth_time 宣告。 SAML 權杖現在將會包含 skypeId 目錄結構描述延伸模組 (在此範例中，此應用程式的應用程式識別碼是 ab603c56068041afb2f6832e2a17e237)。 SAML 權杖會將 Skype 識別碼公開為 `extension_skypeId`。

1. 完成資訊清單更新時，按一下 [儲存] 以儲存資訊清單

## <a name="next-steps"></a>後續步驟

深入了解 Azure AD 所提供的標準宣告。

- [識別碼權杖](id-tokens.md)
- [存取權杖](access-tokens.md)
