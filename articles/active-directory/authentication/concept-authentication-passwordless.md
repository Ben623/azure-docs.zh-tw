---
title: Azure Active Directory 無密碼登入（預覽）
description: 無密碼使用 FIDO2 安全性金鑰或 Microsoft Authenticator 應用程式（預覽）登入 Azure AD
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 10/08/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: librown
ms.collection: M365-identity-device-management
ms.openlocfilehash: 28d4dd3f0d4432930d62bb499fe72533b79d2a08
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/05/2019
ms.locfileid: "74848726"
---
# <a name="passwordless-authentication-options"></a>無密碼驗證選項

多重要素驗證（MFA）是保護組織安全的絕佳方式，但使用者在需要記住其密碼的同時，也能讓您感到沮喪。 無密碼的驗證方法更方便，因為密碼會被移除，並以您所擁有的東西和您知道的東西取代。

|   | 您擁有的內容 | 您或知道的東西 |
| --- | --- | --- |
| 無密碼 | Windows 10 裝置、電話或安全性金鑰 | 生物識別或 PIN |

在驗證方面，每個組織都有不同的需求。 Microsoft 提供三種無密碼的驗證選項：

- Windows Hello 企業版
- Microsoft Authenticator 應用程式
- FIDO2 安全性金鑰

![驗證：安全性與便利性](./media/concept-authentication-passwordless/passwordless-convenience-security.png)

## <a name="windows-hello-for-business"></a>Windows Hello 企業版

Windows Hello 企業版適用于擁有自己指定 Windows 電腦的資訊工作者。 生物識別和 PIN 會直接系結至使用者的電腦，以防止擁有者以外的任何人進行存取。 透過 PKI 整合和單一登入（SSO）的內建支援，Windows Hello 企業版提供簡單且便利的方法，讓您無縫存取內部部署和雲端中的公司資源。

Windows Hello 企業版[規劃指南](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-planning-guide)可用來協助您決定要使用的 Windows Hello 企業版部署類型，以及您需要考慮的選項。

## <a name="microsoft-authenticator-app"></a>Microsoft Authenticator 應用程式

允許員工的電話成為無密碼的驗證方法。 除了密碼以外，您可能已經使用 Microsoft Authenticator 應用程式作為便利的多重要素驗證選項。 但現在它是以無密碼選項的形式提供。

![使用 Microsoft Authenticator 應用程式登入 Microsoft Edge](./media/concept-authentication-passwordless/concept-web-sign-in-microsoft-authenticator-app.png)

它藉由讓使用者能夠登入任何平臺或瀏覽器，將任何 iOS 或 Android 手機轉換成強式的無密碼認證，方法是藉由取得電話的通知、比對螢幕上顯示的數位與電話上的號碼，然後使用其生物識別（觸控或臉部），或釘選以確認。

## <a name="fido2-security-keys"></a>FIDO2 安全性金鑰

FIDO2 安全性金鑰是一種以 unphishable 標準為基礎的無密碼驗證方法，可採用任何形式的規格。 快速身分識別線上（FIDO）是無密碼 authentication 的開放標準。 它可讓使用者和組織利用外部安全性金鑰或裝置內建的平臺金鑰，使用標準來登入其資源，而不需要使用者名稱或密碼。

對於公開預覽，員工可以使用安全性金鑰登入其 Azure AD 加入的 Windows 10 裝置，並取得單一登入其雲端和內部部署資源。 他們也可以登入支援的瀏覽器。

![使用安全性金鑰登入 Microsoft Edge](./media/concept-authentication-passwordless/concept-web-sign-in-security-key.png)

雖然 FIDO 聯盟有許多 FIDO2 認證的金鑰，但 Microsoft 還是需要由廠商實行的 FIDO2 用戶端對驗證器通訊協定（CTAP）規格的一些選用延伸模組，以確保最高的安全性和最佳遇到.

安全性金鑰**必須**將下列功能和延伸模組從 FIDO2 CTAP 通訊協定實作為 Microsoft 相容：

| # | 功能/延伸模組信任 | 為什麼需要這項功能或延伸模組？ |
| --- | --- | --- |
| 1 | 常駐金鑰 | 這項功能可讓安全性金鑰成為可移植的，您的認證會儲存在安全性金鑰上。 |
| 2 | 用戶端 pin | 這項功能可讓您使用第二個因素來保護您的認證，並套用至沒有使用者介面的安全性金鑰。 |
| 3 | hmac-secret | 此延伸模組可確保您可以在裝置離線或在飛機模式時，登入您的裝置。 |
| 4 | 每個 RP 的多個帳戶 | 這項功能可確保您可以在多個服務（例如 Microsoft 帳戶和 Azure Active Directory）上使用相同的安全性金鑰。 |

下列提供者提供 FIDO2 的安全性金鑰，這些是已知與無密碼體驗相容的不同外型規格。 Microsoft 鼓勵客戶透過聯繫廠商以及 FIDO 聯盟，來評估這些金鑰的安全性屬性。

| 提供者 | 連絡人 |
| --- | --- |
| Yubico | [https://www.yubico.com/support/contact/](https://www.yubico.com/support/contact/) |
| Feitian | [https://www.ftsafe.com/about/Contact_Us](https://www.ftsafe.com/about/Contact_Us) |
| HID | [https://www.hidglobal.com/contact-us](https://www.hidglobal.com/contact-us) |
| Ensurity | [https://www.ensurity.com/contact](https://www.ensurity.com/contact) |
| eWBM | [https://www.ewbm.com/support](https://www.ewbm.com/support) |
| AuthenTrend | [https://authentrend.com/about-us/#pg-35-3](https://authentrend.com/about-us/#pg-35-3) |

> [!NOTE]
> 如果您購買並計畫使用 NFC 型安全性金鑰，您將需要支援的 NFC 讀取器。

如果您是廠商，而且想要在這份清單中取得您的裝置，請聯絡[Fido2Request@Microsoft.com](mailto:Fido2Request@Microsoft.com)。

對於安全性敏感的企業，或有不願意或無法使用其電話作為第二個因素的案例，FIDO2 安全性金鑰是很好的選擇。

## <a name="what-scenarios-work-with-the-preview"></a>哪些案例適用于預覽版？

- 系統管理員可以為其租使用者啟用無密碼 authentication 方法
- 系統管理員可以將所有使用者設為目標，或選取其租使用者中每個方法的使用者/群組
- 使用者可以在其帳戶入口網站中註冊和管理這些無密碼驗證方法
- 終端使用者可以使用這些無密碼的驗證方法登入
   - Microsoft Authenticator 應用程式：適用于使用 Azure AD 驗證的案例，包括跨所有瀏覽器、在 Windows 10 全新（OOBE）安裝期間，以及任何作業系統上的整合式行動應用程式。
   - 安全性金鑰：適用于 Windows 10 的鎖定畫面和 web 上支援的瀏覽器，例如 Microsoft Edge。

## <a name="next-steps"></a>後續步驟

[在您的組織中啟用 FIDO2 安全性金鑰 passwordlesss 選項](howto-authentication-passwordless-security-key.md)

[在您的組織中啟用以電話為基礎的無密碼選項](howto-authentication-passwordless-phone.md)

### <a name="external-links"></a>外部連結

[FIDO 聯盟](https://fidoalliance.org/)

[FIDO2 CTAP 規格](https://fidoalliance.org/specs/fido-v2.0-id-20180227/fido-client-to-authenticator-protocol-v2.0-id-20180227.html)
