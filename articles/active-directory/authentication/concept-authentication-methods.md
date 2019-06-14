---
title: 驗證方法-Azure Active Directory
description: 在 Azure AD 中有哪些驗證方法可供 MFA 和 SSPR 使用
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 02/20/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahenry, michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: f0bcaf356108984baf473cdef8c18c5561343cd9
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "66119356"
---
# <a name="what-are-authentication-methods"></a>驗證方法有哪些？

身為管理員，請針對 Azure Multi-factor Authentication 與自助式密碼重設 (SSPR) 建議您要求使用者註冊多個驗證方法選擇驗證方法。 無法使用使用者驗證方法時，他們可以選擇使用另一個方法進行驗證。

系統管理員可以定義在原則中可用於 SSPR 和 MFA 使用者的驗證方法。 某些驗證方法可能不適用於所有功能。 如需有關設定您的原則請參閱文章[如何成功推出自助式密碼重設](howto-sspr-deployment.md)和[規劃雲端架構的 Azure Multi-factor Authentication](howto-mfa-getstarted.md)

Microsoft 強烈建議系統管理員讓使用者可選取多於必要驗證方法數目下限，以免使用者無法存取其中一個。

|驗證方法|使用量|
| --- | --- |
| 密碼 | MFA 和 SSPR |
| 安全性問題 | 僅 SSPR |
| 電子郵件地址 | 僅 SSPR |
| Microsoft Authenticator 應用程式 | MFA 和 SSPR 的公開預覽版 |
| OATH 硬體權杖 | MFA 和 SSPR 的公開預覽版 |
| sms | MFA 和 SSPR |
| 語音通話 | MFA 和 SSPR |
| 應用程式密碼 | 只有在某些情況下的 MFA |

![在 [登入] 畫面使用中的驗證方法](media/concept-authentication-methods/overview-login.png)

|     |
| --- |
| 適用於 MFA 及 SSPR 的 OATH 硬體權杖，以及以行動應用程式通知或行動應用程式程式碼作為 Azure AD 自助式密碼重設的方法，是 Azure Active Directory 的公開預覽功能。 如需有關預覽版的詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)|
|     |

## <a name="password"></a>密碼

您的 Azure AD 密碼會視為一種驗證方法。 它是一種**無法停用**的方法。

## <a name="security-questions"></a>安全性問題

安全性問題**只可在 Azure AD 自助式密碼重設中**用於非系統管理員帳戶。

如果您使用安全性問題，建議結合另一個方法共同使用。 安全性問題可能會比其他方法更不安全，因為有些人可能會知道其他使用者問題的答案。

> [!NOTE]
> 安全性問題會私密且安全地儲存在目錄中的使用者物件上，只能在註冊期間由使用者回答。 系統管理員無法讀取或修改使用者問題或答案。
>

### <a name="predefined-questions"></a>預先定義的問題

* 您在哪個城市遇見第一個配偶/伴侶？
* 您的父母在哪個城市相遇？
* 您最親近的手足住在哪個城市？
* 您的父親在哪個城市出生？
* 您的第一份工作是在哪個城市？
* 您的母親在哪個城市出生？
* 您在哪個城市渡過 2000 年的新年？
* 您最喜愛的高中老師姓什麼？
* 您已申請但未就讀的大專名稱為何？
* 您舉辦第一次婚宴的地點名稱為何？
* 您父親的中間名是什麼？
* 您最愛的食物是什麼？
* 您外婆的姓名為何？
* 您母親的中間名是什麼？
* 您最年長手足的生日月份和年度為何？ (例如 1985 年 11 月)
* 您最年長手足的中間名是什麼？
* 您祖父的姓名為何？
* 您最年幼手足的中間名稱是什麼？
* 您六年級時就讀哪間學校？
* 您童年最要好朋友的姓名為何？
* 您第一個密友的姓名為何？
* 您最喜愛的小學老師姓什麼？
* 第一輛汽車或機車的製造商和型號為何？
* 您就讀的第一所學校名稱為何？
* 您出生的醫院名稱為何？
* 您兒時第一個家的街道名稱為何？
* 您的童年英雄名稱為何？
* 您最喜愛的娃娃名稱為何？
* 您第一隻寵物的名稱為何？
* 您童年時期的綽號是什麼？
* 您中學最喜愛的運動是什麼？
* 您的第一份工作是什麼？
* 您兒時電話號碼的末四碼是什麼？
* 在您年輕時，您長大想做什麼？
* 您遇過最有名的人物是誰？

所有預先定義的安全性問題會依據使用者的瀏覽器地區設定，翻譯並當地語系化成完整的 O365 語言集。

### <a name="custom-security-questions"></a>自訂安全性問題

自訂安全性問題不會進行當地語系化。 所有自訂問題會以您在系統管理使用者介面中輸入它們的語言顯示，即使使用者的瀏覽器地區設定不同。 如果您需要已當地語系化的問題，請使用預先定義的問題。

自訂安全性問題的長度上限為 200 個字元。

### <a name="security-question-requirements"></a>安全性問題需求

* 答案字元限制下限為三個字元。
* 答案字元限制上限為 40 個字元。
* 使用者不能回答相同的問題一次以上。
* 使用者不能對一個以上的問題提供相同的答案。
* 可以使用任何字元集來定義問題和答案 (包括 Unicode 字元)。
* 定義的問題數目必須大於或等於註冊所需的問題數目。

## <a name="email-address"></a>電子郵件地址

電子郵件地址只可**在 Azure AD 自助式密碼重設**中使用。

Microsoft 建議使用不需要使用者的 Azure AD 密碼進行存取的電子郵件帳戶。

## <a name="microsoft-authenticator-app"></a>Microsoft Authenticator 應用程式

Microsoft Authenticator 應用程式可以為您的 Azure AD 公司或學校帳戶或 Microsoft 帳戶提供額外一層安全性。

Microsoft Authenticator 應用程式適用於 [Android](https://go.microsoft.com/fwlink/?linkid=866594)、[iOS](https://go.microsoft.com/fwlink/?linkid=866594) 和 [Windows Phone](https://go.microsoft.com/fwlink/?Linkid=825071)。

> [!NOTE]
> 使用者在註冊自助式密碼重設時，不可選擇註冊其行動應用程式。 相反地，使用者可以在 [https://aka.ms/mfasetup](https://aka.ms/mfasetup)，或在 [https://aka.ms/setupsecurityinfo](https://aka.ms/setupsecurityinfo) 的安全性資訊註冊預覽版中，註冊其行動應用程式。
>

### <a name="notification-through-mobile-app"></a>行動應用程式的通知

Microsoft Authenticator 應用程式可協助防止未經授權即存取帳戶，並藉由推播通知到您的手機或平板電腦來停止詐騙交易。 使用者需檢視通知，如果合法，則選取 [驗證]。 否則，他們可以選取 [拒絕]。

> [!WARNING]
> 對於自助式密碼重設，當重設只需要一個方法時，驗證程式碼是使用者可**確保最高層級安全性**的唯一可用選項。
>
> 需要兩種方法時，使用者除了任何其他已啟用的方法之外，將能夠使用  通知**或**驗證碼。
>

如果您允許使用透過行動裝置應用程式的通知和來自行動裝置應用程式的驗證碼，使用通知註冊 Microsoft Authenticator 應用程式的使用者，就能使用通知和驗證碼來確認其身分識別。

> [!NOTE]
> 如果您的組織有人員在運作，或用於中國，進而**行動應用程式的通知**方法**Android 裝置**不適用於該國家/地區。 替代方法應該可供這些使用者。

### <a name="verification-code-from-mobile-app"></a>行動應用程式傳回的驗證碼

Microsoft Authenticator 應用程式或其他第三方應用程式可以作為軟體權杖來產生 OATH 驗證碼。 在輸入您的使用者名稱和密碼後，必須在登入畫面出現提示時輸入應用程式提供的驗證碼。 驗證碼提供第二種形式的驗證。

> [!WARNING]
> 對於自助式密碼重設，當重設只需要一個方法時，驗證程式碼是使用者可**確保最高層級安全性**的唯一可用選項。
>

使用者可能會有最多五個 OATH 硬體權杖或驗證器應用程式，例如設定要在任何時候使用的 Microsoft Authenticator 應用程式的組合。

## <a name="oath-hardware-tokens-public-preview"></a>OATH 硬體權杖 (公開預覽)

OATH 是一項開放標準，可指定單次密碼 (OTP) 程式碼的產生方式。 Azure AD 將會支援使用每 30 秒或 60 秒變換一次的 OATH-TOTP SHA-1 權杖。 客戶可以從他們選擇的廠商購買這些權杖。 祕密金鑰，僅限於 128 個字元，可能會不相容的所有權杖。

![將 OATH 權杖上傳至 MFA Server OATH 權杖 刀鋒視窗](media/concept-authentication-methods/oath-tokens-azure-ad.png)

OATH 硬體權杖已支援作為公開預覽的一部分。 如需有關預覽版的詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)

取得權杖後，必須使用逗號分隔值 (CSV) 檔案格式加以上傳，包括 UPN、序號、祕密金鑰、時間間隔、製造商和模型，如下列範例所示。

```csv
upn,serial number,secret key,time interval,manufacturer,model
Helga@contoso.com,1234567,1234567890abcdef1234567890abcdef,60,Contoso,HardwareKey
```

> [!NOTE]
> 請確定您在 CSV 檔案中包含標頭資料列，如上所示。

一旦將格式正確設為 CSV 檔案後，系統管理員接著可以登入 Azure 入口網站，然後瀏覽至 [Azure Active Directory]  、[MFA 伺服器]  、[OATH 權杖]  ，並上傳所產生的 CSV 檔案。

視 CSV 檔案的大小而定，可能需要數分鐘的時間來處理。 按一下 [重新整理]  按鈕來取得目前的狀態。 如果檔案中有任何錯誤，您可以選擇下載 CSV 檔案，當中會列出需要您解決的任何錯誤。

一旦已解決任何錯誤後，系統管理員可以接著按一下 [啟動]  來啟動每個金鑰，以便啟動權杖及輸入權杖上顯示的 OTP。

使用者可能會有最多五個 OATH 硬體權杖或驗證器應用程式，例如設定要在任何時候使用的 Microsoft Authenticator 應用程式的組合。

## <a name="mobile-phone"></a>行動電話

有兩個選項可供使用行動電話的使用者使用。

如果使用者不想在目錄中顯示其行動電話號碼，但仍想要用於密碼重設，管理員便不應將該號碼填入目錄。 使用者應該透過[密碼重設註冊入口網站](https://aka.ms/ssprsetup)，填妥其**驗證電話**屬性。 管理員可以在使用者的設定檔中看到此資訊，但該資訊不會發佈在其他地方。

為了正確運作，電話號碼的格式必須是：+國碼 電話號碼  ，例如 +1 4255551234。

> [!NOTE]
> 國碼 (地區碼) 和電話號碼之間需要空格。
>
> 密碼重設不支援電話分機。 即使是 +1 4255551234X12345 格式，撥號之前都會移除分機號碼。

### <a name="text-message"></a>簡訊

包含驗證碼的簡訊會傳送到行動電話號碼。 輸入登入介面中提供的驗證碼以繼續。

### <a name="phone-call"></a>撥打電話

撥打自動語音電話給您所提供的電話號碼。 接聽電話並按電話鍵盤上的 # 進行驗證

> [!IMPORTANT]
> 從於 2019 年 3 月開始撥打電話選項將無法使用免費/試用 Azure AD 租用戶中的 MFA 和 SSPR 的使用者。 這項變更不會影響簡訊。 通話會繼續在使用者可使用付費 Azure AD 租用戶。 這項變更只會影響免費/試用 Azure AD 租用戶。

## <a name="office-phone"></a>辦公室電話

撥打自動語音電話給您所提供的電話號碼。 接聽電話並按電話鍵盤上的 # 進行驗證。

為了正確運作，電話號碼的格式必須是：+國碼 電話號碼  ，例如 +1 4255551234。

辦公室電話屬性是由您的系統管理員管理。

> [!IMPORTANT]
> 從於 2019 年 3 月開始撥打電話選項將無法使用免費/試用 Azure AD 租用戶中的 MFA 和 SSPR 的使用者。 這項變更不會影響簡訊。 通話會繼續在使用者可使用付費 Azure AD 租用戶。 這項變更只會影響免費/試用 Azure AD 租用戶。

> [!NOTE]
> 國碼 (地區碼) 和電話號碼之間需要空格。
>
> 密碼重設不支援電話分機。 即使是 +1 4255551234X12345 格式，撥號之前都會移除分機號碼。

## <a name="app-passwords"></a>應用程式密碼

某些非瀏覽器應用程式不支援多重要素驗證，如果使用者已啟用多重要素驗證，並嘗試使用非瀏覽器應用程式，則無法進行驗證。 應用程式密碼可讓使用者繼續進行驗證

如果您透過條件式存取原則強制執行 Multi-Factor Authentication (而不是透過每個使用者的 MFA)，則無法建立應用程式密碼。 使用條件式存取原則來控制存取權的應用程式不需要應用程式密碼。

如果您的組織使用 SSO 與 Azure AD 同盟，而且您想要使用 Azure MFA 時，請注意下列細節：

* 應用程式密碼由 Azure AD 驗證，因此會略過同盟。 唯有在設定應用程式密碼時才會使用同盟。 對於同盟 (SSO) 使用者，密碼會儲存在組織識別碼中。 如果使用者離開公司，這些資訊必須使用 DirSync 流向組織識別碼。 停用/刪除帳戶可能需要長達三個小時才能完成同步處理，導致 Azure AD 中停用/刪除應用程式密碼時延遲。
* 應用程式密碼不會遵守內部部署用戶端存取控制設定。
* 應用程式密碼不適用內部部署驗證記錄/稽核功能。
* 某些進階架構設計在使用雙步驟驗證時，可能需要搭配使用組織使用者名稱和密碼及應用程式密碼，需視驗證的位置而定。 對於根據內部部署基礎結構進行驗證的用戶端，您可以使用組織使用者名稱和密碼。 對於根據 Azure AD 進行驗證的用戶端，您需要使用應用程式密碼。
* 根據預設，使用者無法建立應用程式密碼。 如果您需要允許使用者建立應用程式密碼，請選取服務設定下的 [允許使用者建立應用程式密碼以登入非瀏覽器應用程式]  選項。

## <a name="next-steps"></a>後續步驟

[啟用貴組織的自助密碼重設](quickstart-sspr.md)

[啟用貴組織的 Azure 多重要素驗證](howto-mfa-getstarted.md)

[在您的租用戶中的啟用結合註冊](howto-registration-mfa-sspr-combined.md)

[使用者驗證方法設定文件](https://aka.ms/securityinfoguide)
