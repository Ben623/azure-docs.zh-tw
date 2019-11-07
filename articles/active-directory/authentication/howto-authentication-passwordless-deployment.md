---
title: 使用 Azure AD 完成無密碼 authentication 部署
description: 完成 Azure Active Directory 無密碼 authentication 部署
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 10/08/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: baselden, librown
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9b6f07e1dd8e9252d2b6e00b85a47ba2e19f8bd8
ms.sourcegitcommit: c62a68ed80289d0daada860b837c31625b0fa0f0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/05/2019
ms.locfileid: "73603459"
---
# <a name="complete-a-passwordless-authentication-deployment"></a>完成無密碼 authentication 部署

大部分的網路攻擊都是以使用者名稱和密碼從組織中的某人竊取而來。 

組織會藉由要求使用者使用來嘗試對抗威脅：

- 長密碼
- 複雜密碼
- 經常變更密碼
- 多重要素驗證 (MFA)

Microsoft 的[研究顯示](https://aka.ms/passwordguidance)，這些工作會對使用者帶來煩惱，並提升支援成本。 如需詳細資訊，請參閱[您的 Pa $ $word 不重要](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Your-Pa-word-doesn-t-matter/ba-p/731984)。

部署無密碼 authentication 提供下列優點：

- 加強安全性。 藉由移除密碼作為受攻擊面，降低網路釣魚和密碼噴灑攻擊的風險。
- 更好的使用者體驗。 讓使用者可以輕鬆地從任何地方存取資料，並可讓您在行動裝置上輕鬆存取 Outlook、OneDrive、office 等等。
- 穩固的見解。 透過健全的記錄和審核，取得使用者無密碼活動的深入解析。

密碼會取代為您所擁有的內容，以及您所知的東西。 例如，在 Windows Hello 企業版中，生物識別手勢（例如臉部或指紋）或不是透過網路傳輸的裝置特定 PIN。

Microsoft 提供三種無密碼的驗證選項，涵蓋許多案例。 這些方法可以串聯使用。 

- [Windows Hello 企業版](https://docs.microsoft.com/azure/security/fundamentals/ad-passwordless)最適合其專用 windows 電腦上的使用者。
- 具有[FIDO2 安全性](https://docs.microsoft.com/azure/security/fundamentals/ad-passwordless)金鑰的安全性金鑰登入，特別適用于登入 kiosk 等共用電腦的使用者、限制使用電話的情況，以及高特殊許可權的身分識別。
- 使用[Microsoft Authenticator 應用程式](https://docs.microsoft.com/azure/security/fundamentals/ad-passwordless)的手機登入，適用于為具有行動裝置的使用者提供無密碼選項。 它會允許使用者登入任何平臺或瀏覽器，將任何 iOS 或 Android 手機轉換成強式的無密碼認證。 使用者可以藉由取得電話的通知來登入，將畫面上顯示的數位與電話上的號碼進行比對，然後使用其生物識別資料或 PIN 來確認。

## <a name="compare-passwordless-authentication-methods"></a>比較無密碼的驗證方法

Microsoft 的無密碼 authentication 方法可啟用不同的案例。 請考慮您的組織需求、必要條件，以及每個驗證方法的功能，以選取您的無密碼 authentication 策略。 我們建議使用 Windows 10 裝置的每個組織都使用 Windows Hello 企業版。 然後，在其他案例中新增電話登入（使用 Microsoft Authenticator 應用程式）或安全性金鑰。

### <a name="passwordless-authentication-scenarios"></a>無密碼驗證案例

| 案例 | 電話驗證 | 安全性金鑰 | Windows Hello 企業版 |
| --- | --- | --- | --- |
| **電腦登入**： <br> 從指派的 Windows 10 裝置 | **否** | **是** <br> 使用生物特徵辨識，PIN | **是**<br>使用生物識別辨識和或 PIN |
| **電腦登入**： <br> 從共用的 Windows 10 裝置 | **否** | **是** <br> 使用生物特徵辨識，PIN  | **否** |
| **Web 應用程式登入**： <br>從使用者專用的電腦 | **是** | **是** <br> 提供給應用程式的單一登入已由電腦登入啟用 | **是**<br> 提供給應用程式的單一登入已由電腦登入啟用 |
| **Web 應用程式登入**： <br> 從行動或非 windows 裝置 | **是** | **否** | **否** |
| **電腦登入**： <br> 非 Windows 電腦 | **否** | **否** | **否** |

### <a name="technical-considerations-for-the-microsoft-authenticator-app"></a>Microsoft Authenticator 應用程式的技術考慮

**AD FS 整合**-當使用者啟用 Microsoft Authenticator 無密碼認證時，該使用者的驗證會預設為傳送通知以供核准。 除非在混合式租使用者中選取 [改用您的密碼]，否則無法將其導向至 ADFS 進行登入。 此程式也會略過任何內部部署的條件式存取原則，以及傳遞驗證流程。 不過，如果指定了 login_hint，使用者將會轉送到 ADFS，並略過使用無密碼認證的選項。

**AZURE mfa server** -透過組織的內部部署 azure mfa SERVER 啟用 MFA 的使用者，仍然可以建立並使用單一無密碼電話登入認證。 如果使用者嘗試使用認證升級 Microsoft Authenticator 的多個安裝 (5+)，這項變更可能會導致錯誤。

**裝置註冊**-若要使用驗證器應用程式進行無密碼驗證，裝置必須在 Azure AD 租使用者中註冊，而且不能是共用裝置。 裝置只能在單一租使用者中註冊。 此限制表示，使用驗證器應用程式時，電話登入只支援一個公司或學校帳戶。

## <a name="prerequisites"></a>必要條件

組織必須符合下列必要條件，才能開始進行無密碼部署。

| 必要條件 | 驗證器應用程式 | FIDO2 安全性金鑰 |
| --- | --- | --- |
| 已啟用[AZURE MFA 和自助式密碼重設（SSPR）的合併註冊](howto-registration-mfa-sspr-combined.md)（預覽功能） | √ | √ |
| [使用者可以執行 Azure MFA](howto-mfa-getstarted.md) | √ | √ |
| [使用者已註冊 Azure MFA 和 SSPR](howto-registration-mfa-sspr-combined.md) | √ | √ |
| [使用者已註冊其行動裝置以 Azure Active Directory](../devices/overview.md) | √ |   |
| 使用支援的瀏覽器（例如 Microsoft Edge 或 Mozilla Firefox）的 Windows 10 1809 版或更高版本 <br> （67版或更高版本）。 <br> *Microsoft 建議採用1903版或更高版本來提供原生支援*。 |   | √ |
| 相容的 FIDO2 安全性金鑰。 請確定您使用的是[Microsoft 測試和驗證](howto-authentication-passwordless-enable.md)的 FIDO2 安全性裝置，或其他相容的 FIDO2 安全性裝置。 |   | √ |

### <a name="prerequisites-for-windows-hello-for-business"></a>Windows Hello 企業版的必要條件

Windows Hello 的必要條件非常依賴您是部署在內部部署、混合式或僅限雲端的設定中。 如需詳細資訊，請參閱[Windows Hello 企業版的必要條件完整清單](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-identity-verification)。

### <a name="azure-multi-factor-authentication"></a>Azure Multi-Factor Authentication

使用者會在 Azure MFA 註冊流程中註冊其無密碼方法。 使用使用者名稱和密碼的多重要素驗證以及另一個已註冊的方法，可以在某些情況下使用其電話或安全性金鑰做為回復。

### <a name="security-key-lifecycle"></a>安全性金鑰生命週期

安全性金鑰可讓您存取您的資源，而且您應該規劃這些實體裝置的管理。

1. 金鑰散發：規劃如何將金鑰布建到您的組織。 您可能有集中的布建程式，或允許終端使用者購買 FIDO 2.0 相容的金鑰。
1. 金鑰啟用：終端使用者必須自行啟動安全性金鑰。 終端使用者在[https://aka.ms/mysecurityinfo](https://aka.ms/mysecurityinfo)註冊其安全性金鑰，並在第一次使用時啟用第二個要素（PIN 或生物識別）。
1. 停用金鑰：雖然安全性金鑰功能是在預覽階段，但系統管理員無法從使用者帳戶中移除金鑰。 使用者必須將它移除。 如果遺失或遭竊金鑰：
   1. 從啟用無密碼 authentication 的任何群組中移除使用者。
   1. 確認他們已移除金鑰做為驗證方法。
   1. 發出新的金鑰。
1. 金鑰取代：使用者可以同時啟用兩個安全性金鑰。 取代安全性金鑰時，請確定使用者也已移除要取代的金鑰。

### <a name="enable-windows-10-support"></a>啟用 Windows 10 支援

若要啟用 Windows 10 登入，請使用 FIDO2 安全性金鑰，必須啟用 Windows 10 中的認證提供者功能。 以下列兩種方式之一加以啟用：

- [使用 Intune 啟用認證提供者](howto-authentication-passwordless-security-key-windows.md#enable-with-intune)
   - Intune 部署是建議的選項。
- [啟用具有布建套件的認證提供者](howto-authentication-passwordless-security-key-windows.md#enable-with-a-provisioning-package)
   - 如果無法部署 Intune，系統管理員必須在每部電腦上部署套件，以啟用認證提供者功能。 您可以使用下列其中一個選項來執行封裝安裝：
      - 群組原則或 System Center Configuration Manager （SCCM）
      - 在 Windows 10 電腦上進行本機安裝

### <a name="register-security-keys"></a>註冊安全性金鑰

使用者必須在每個已加入 Windows 10 電腦的 Azure Active Directory 上註冊其安全性金鑰。

如需詳細資訊，請參閱[FIDO2 安全性金鑰的使用者註冊與管理](howto-authentication-passwordless-security-key.md#user-registration-and-management-of-fido2-security-keys)。

### <a name="licensing-for-passwordless-authentication"></a>無密碼 authentication 的授權

無密碼 authentication 沒有額外的成本，不過某些必要條件可能需要 premium 訂用帳戶。 請參閱[Azure Active Directory 授權頁面](https://azure.microsoft.com/pricing/details/active-directory/)中的詳細功能與授權資訊。

## <a name="develop-a-plan"></a>開發方案

請考慮您的商務需求，以及每個驗證方法的使用案例。 然後選取最符合您需求的方法。

### <a name="use-cases"></a>使用案例

下表概述此專案期間要執行的使用案例。

| 領域 | 說明 |
| --- | --- |
| **存取** | 無密碼登入可從公司網路內部或外部的公司或個人裝置取得。 |
| **稽核** | 使用方式資料可供系統管理員近乎即時地進行審核。 <br> 使用量資料會至少每隔29天下載到公司系統，或使用 SIEM 工具。 |
| **條例** | 定義和監視適當驗證方法和相關聯群組之使用者指派的生命週期。 |
| **安全性** | 存取適當的驗證方法是透過使用者和群組指派來控制。 <br> 只有授權的使用者可以使用無密碼登入。 |
| **效能** | 已記載並監視存取指派傳播時間軸。 <br> 登入時間是為了方便使用而測量。 |
| **使用者體驗** | 使用者瞭解行動相容性。 <br> 使用者可以設定驗證器應用程式無密碼登入。 |
| **支援** | 使用者知道如何尋找無密碼登入問題的支援。 |

### <a name="engage-the-right-stakeholders"></a>參與適當的專案關係人

當技術專案失敗時，通常是因為對影響、結果和責任的預期不相符。 若要避免這些錯誤，請[確定您正在吸引正確的專案關係人](../fundamentals/active-directory-deployment-plans.md#include-the-right-stakeholders)，而且已充分瞭解專案中的專案關係人角色。

### <a name="organization-communications"></a>組織通訊

通訊對於任何新服務的成功非常重要。 主動溝通使用者的經驗如何改變、何時會變更，以及如何在遇到問題時取得支援。

您對終端使用者的通訊必須包括：

- [啟用結合的安全性註冊體驗](howto-authentication-passwordless-phone.md)
- [下載 Microsoft Authenticator 應用程式](../user-help/user-help-auth-app-download-install.md)
- [在 Microsoft Authenticator 應用程式中註冊](howto-authentication-passwordless-phone.md)
- [使用您的電話登入](../user-help/user-help-auth-app-sign-in.md)

Microsoft 提供 MFA[通訊範本](https://aka.ms/mfatemplates)、自助式密碼重設（SSPR）[通訊範本](https://www.microsoft.com/download/details.aspx?id=56768)以及[使用者檔](../user-help/security-info-setup-signin.md)，以協助草擬您的通訊。 您可以藉由選取該頁面上的安全性資訊連結，將使用者傳送至[https://myprofile.microsoft.com](https://myprofile.microsoft.com/)直接註冊。

### <a name="testing-passwordless"></a>測試無密碼

在您部署的每個階段，請確定您正在測試結果是否如預期般運作。

#### <a name="testing-the-microsoft-authenticator-app"></a>測試 Microsoft Authenticator 應用程式

以下是使用 Microsoft Authenticator 應用程式進行無密碼驗證的範例測試案例

| 案例 | 預期的結果 |
| --- | --- |
| 使用者可以註冊 Microsoft Authenticator 應用程式 | 使用者可以從 aka.ms/mysecurityinfo 註冊應用程式 |
| 使用者可以啟用電話登入 | 已針對公司帳戶設定電話登入 |
| 使用者可以使用電話登入來存取應用程式 | 使用者經歷了電話登入流程，並到達指定的應用程式。 |
| 在 Azure Active Directory 入口網站的 [驗證方法] 畫面中關閉 Microsoft Authenticator 無密碼登入，藉以測試回復電話登入 | 先前啟用的使用者無法從 Microsoft Authenticator 使用無密碼登入。 |
| 從 Microsoft Authenticator 應用程式移除手機登入 | Microsoft Authenticator 上不再提供工作帳戶 |

#### <a name="testing-security-keys"></a>測試安全性金鑰

以下是使用安全性金鑰進行無密碼驗證的範例測試案例。

**無密碼 FIDO 登入 Azure Active Directory 聯結的 Windows 10 裝置**

| 案例 | 預期的結果 |
| --- | --- |
| 使用者可以註冊 FIDO2 裝置（1809） | 使用者可以在 設定 > 帳戶 > 登入選項 > 安全性金鑰）註冊 FIDO2 裝置 |
| 使用者可以重設 FIDO2 裝置（1809） | 使用者可以使用製造商軟體重設 FIDO2 裝置 |
| 使用者可以使用 FIDO2 裝置登入（1809） | 使用者可以從 [登入] 視窗中選取 [安全性金鑰]，並成功登入。 |
| 使用者可以註冊 FIDO2 裝置（1903） | 使用者可以在設定 > 帳戶 > 登入選項 > 安全性金鑰）註冊 FIDO2 裝置 |
| 使用者可以重設 FIDO2 裝置（1903） | 使用者可以在設定 > 帳戶 > 登入選項 > 安全性金鑰）重設 FIDO2 裝置 |
| 使用者可以使用 FIDO2 裝置登入（1903） | 使用者可以從 [登入] 視窗中選取 [安全性金鑰]，並成功登入。 |

**無密碼 FIDO 登入以 Azure AD web 應用程式**

| 案例 | 預期的結果 |
| --- | --- |
| 使用者可以使用 Microsoft Edge 在 aka.ms/mysecurityinfo 上註冊 FIDO2 裝置 | 註冊應會成功 |
| 使用者可以使用 Firefox 在 aka.ms/mysecurityinfo 上註冊 FIDO2 裝置 | 註冊應會成功 |
| 使用者可以使用 Microsoft Edge，透過 FIDO2 裝置登入 OneDrive online | 登入應會成功 |
| 使用者可以使用 Firefox 以 FIDO2 裝置登入 OneDrive online | 登入應會成功 |
| 在 Azure Active Directory 入口網站的 [驗證方法] 分頁中關閉 FIDO2 安全性金鑰，以測試回復 FIDO2 裝置註冊 | 系統會提示使用者使用其安全性金鑰登入，它會成功地將其記錄在中，並且會顯示錯誤：「您的公司原則要求您使用不同的方法來登入」。 然後，使用者應該能夠選取不同的方法並成功登入。 關閉視窗並再次登入，以確認它們沒有看到相同的錯誤訊息。 |

### <a name="auditing-passwordless"></a>審核無密碼

Azure AD 具有可提供技術和商務深入解析的報表。 讓您的商務和技術應用程式擁有者取得的擁有權，並根據貴組織的需求來取用這些報告。

Azure Active Directory 入口網站中的 [驗證方法] 區段，系統管理員可以在其中啟用和管理無密碼認證的設定。

Azure AD 會在下列情況將專案新增至 audit 記錄檔：

- 系統管理員會在 [驗證方法] 區段中進行變更。
- 使用者會對其在 Azure Active Directory 內的認證進行任何變更。

下表提供一般報告案例的一些範例。

|   | 管理風險 | 提高生產力 | 治理和合規性 |
| --- | --- | --- | --- |
| **報表類型** | 驗證方法-已註冊結合安全性註冊的使用者 | 驗證方法–已註冊代理程式更新的使用者 | 登入：審查誰正在存取租使用者，以及如何 |
| **可能的動作** | 目標使用者尚未註冊 | 推動採用 Microsoft Authenticator 應用程式或安全性金鑰 | 撤銷存取權或強制執行系統管理員的其他安全性原則 |

**Azure AD 會保留最多30天的審核資料**，並透過 Azure 系統管理員入口網站或 API 提供資料，以供您下載至您的分析系統。 如果您的組織需要更長的保留期，則必須將記錄匯出並取用至 SIEM 工具，例如[Azure Sentinel](../../sentinel/connect-azure-active-directory.md)、Splunk 或 Sumo 邏輯。 [深入瞭解如何查看您的存取和使用方式報表](../reports-monitoring/overview-reports.md)。

使用者可以藉由流覽至[https://aka.ms/mysecurityinfo](https://aka.ms/mysecurityinfo)來註冊和管理其認證。 此連結會將使用者引導至透過結合的 SSPR/MFA 註冊體驗來啟用的使用者認證管理體驗。 FIDO2 安全性裝置的任何註冊或使用者對驗證方法所做的變更都會記錄在 Azure Active Directory audit 記錄中。

當使用者啟用或停用安全性金鑰上的帳戶，或在其 Windows 10 電腦上重設安全性金鑰的第二個因素時，會將專案新增至安全性記錄檔，並在下列事件識別碼底下：4670、5382。

### <a name="plan-for-rollback"></a>規劃復原作業

雖然無密碼 authentication 是輕量的功能，對終端使用者的影響最小，但可能需要復原。

復原時，系統管理員必須登入 Azure Active Directory 入口網站、選取所需的增強式驗證方法，並將 [啟用] 選項變更為 [否]。 此程式會關閉所有使用者的無密碼功能。

已註冊 FIDO2 安全性裝置的使用者，會在下次登入時提示他們使用安全性裝置，然後會看到下列錯誤：

![選擇不同的登入方式](./media/howto-authentication-passwordless-deployment/passwordless-choose-sign-in.png)

### <a name="plan-to-pilot"></a>規劃試驗

當您部署無密碼 authentication 時，您應該先啟用一或多個試驗群組。 您可以特別針對此用途[建立群組](../fundamentals/active-directory-groups-create-azure-portal.md)。 將參與試驗的使用者新增至群組。 然後，針對選取的群組啟用新的無密碼驗證方法。

群組可以從內部部署目錄或從 Azure AD 進行同步處理。 當您滿意試驗結果之後，就可以針對所有使用者切換無密碼 authentication。

請參閱部署計畫頁面上[試驗的最佳做法](https://aka.ms/deploymentplans)。

## <a name="deploy-passwordless-authentication"></a>部署無密碼 authentication

遵循符合您選擇之方法的步驟。

### <a name="required-administrative-roles"></a>必要的系統管理角色

| Azure AD 角色 | 說明 |
| --- | --- |
| 驗證系統管理員 | 最低特殊許可權角色能夠執行及管理驗證方法 |
| User | 在裝置上設定驗證器應用程式，或註冊 web 或 Windows 10 登入的安全性金鑰裝置的最低特殊許可權角色。 |

### <a name="deploy-phone-sign-in-with-the-microsoft-authenticator-app"></a>使用 Microsoft Authenticator 應用程式部署電話登入

遵循使用[Microsoft Authenticator 應用程式啟用無密碼登入](howto-authentication-passwordless-phone.md)一文中的步驟，讓 Microsoft Authenticator 應用程式成為貴組織中的無密碼驗證方法。

### <a name="deploy-fido2-security-key-sign-in"></a>部署 FIDO2 安全性金鑰登入

依照[Azure AD 的啟用無密碼安全性金鑰登入](howto-authentication-passwordless-security-key.md)一文中的步驟，在您的組織中啟用 FIDO2 安全性金鑰做為無密碼驗證方法。

### <a name="troubleshoot-phone-sign-in"></a>針對手機登入進行疑難排解

| 案例 | 方案 |
| --- | --- |
| 使用者無法執行合併的註冊 | 請確定已啟用[合併註冊](concept-registration-mfa-sspr-combined.md)。 |
| 使用者無法在驗證器應用程式中啟用電話登入 | 確保使用者在部署範圍內 |
| 使用者不在無密碼 authentication 的範圍內，但會以無密碼登入選項呈現，其無法完成。 | 當使用者在建立原則之前，已在應用程式中啟用電話登入時，就會發生這種情況。 <br> 若要啟用登入：將使用者新增至已啟用無密碼登入的使用者範圍。 <br> 若要封鎖登入：讓使用者從應用程式移除其認證。 |

### <a name="troubleshoot-security-key-sign-in"></a>對安全性金鑰登入進行疑難排解

| 案例 | 方案 |
| --- | --- |
| 使用者無法執行合併的註冊 | 請確定已啟用[合併註冊](concept-registration-mfa-sspr-combined.md)。 |
| 使用者無法在其[安全性設定](https://aka.ms/mysecurityinfo)中新增安全性金鑰 | 請確定已啟用[安全性金鑰](howto-authentication-passwordless-security-key.md)。 |
| 使用者無法在 Windows 10 登入選項中新增安全性金鑰 | [確定 Windows 登入的安全性金鑰](howto-authentication-passwordless-enable.md) |
| **錯誤訊息**：我們偵測到此瀏覽器或作業系統不支援 FIDO2 安全性金鑰。 | 無密碼 FIDO2 安全性裝置只能在 Windows 10 1809 版或更新版本上的支援瀏覽器（Microsoft Edge、Firefox 67 版）中註冊。 |
| **錯誤訊息**：您的公司原則要求您使用不同的方法來登入。 | 不確定已在租使用者中啟用安全性金鑰。 |
| 使用者無法在 Windows 10 版本1809上管理我的安全性金鑰 | 1809版需要您使用 FIDO2 金鑰廠商所提供的安全性金鑰管理軟體。 請洽詢廠商尋求支援。 |
| 我認為我的 FIDO2 安全性金鑰可能會有問題，我該如何進行測試 | 流覽至 [ [https://webauthntest.azurewebsites.net/](https://webauthntest.azurewebsites.net/)]，輸入測試帳戶的認證，插入可疑的安全性金鑰，按一下畫面右上方的 [+] 按鈕，按一下 [建立]，然後完成建立程式。 如果此案例失敗，您的裝置可能會有問題。 |

## <a name="next-steps"></a>後續步驟

- [啟用 Azure AD 登入的無密碼安全性金鑰](howto-authentication-passwordless-security-key.md)
- [啟用使用 Microsoft Authenticator 應用程式的無密碼登入](howto-authentication-passwordless-phone.md)
- [深入瞭解如何使用 & 深入解析的驗證方法](howto-authentication-methods-usage-insights.md)
