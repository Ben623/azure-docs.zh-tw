---
title: 如何規劃 Azure Active Directory (Azure AD) 中的混合式 Azure Active Directory Join 實作 | Microsoft Docs
description: 了解如何設定混合式 Azure Active Directory 已加入的裝置。
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: conceptual
ms.date: 06/28/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sandeo
ms.collection: M365-identity-device-management
ms.openlocfilehash: ee3309bdd3629057d174866dde58ffd95e9e5ca8
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/26/2019
ms.locfileid: "68562132"
---
# <a name="how-to-plan-your-hybrid-azure-active-directory-join-implementation"></a>如何：規劃混合式 Azure Active Directory Join 實作

以類似的使用方式，裝置是您想要保護的另一個核心身分識別，可用來隨時隨地保護您的資源。 您可以使用下列其中一種方法，將裝置身分識別導入 Azure AD 中進行管理，以達到此目標：

- Azure AD Join
- 混合式 Azure AD Join
- Azure AD 註冊

將您的裝置導入 Azure AD 中，您將可透過跨雲端和內部部署資源的單一登入 (SSO)，將使用者的生產力最大化。 同時, 您可以使用[條件式存取](../active-directory-conditional-access-azure-portal.md)來保護對雲端和內部部署資源的存取。

如果您有內部部署 Active Directory (AD) 環境, 而且想要將已加入 AD 網域的電腦加入 Azure AD, 您可以執行混合式 Azure AD 聯結來完成這項作業。 本文提供在您的環境中實作混合式 Azure AD Join 的相關步驟。 

## <a name="prerequisites"></a>先決條件

本文假設您已熟悉[Azure Active Directory 中的裝置身分識別管理簡介](../device-management-introduction.md)。

> [!NOTE]
> Windows 10 混合式 Azure AD 加入所需的最低網域功能和樹系功能等級為 Windows Server 2008 R2。

## <a name="plan-your-implementation"></a>計劃您的實作

若要規劃您的混合式 Azure AD 實作，您應該熟悉：

|   |   |
| --- | --- |
| ![檢查][1] | 檢閱支援的裝置 |
| ![檢查][1] | 檢閱您應該知道的事情 |
| ![檢查][1] | 審查混合式 Azure AD 聯結的受控制驗證 |
| ![檢查][1] | 根據您的身分識別基礎結構來選取您的案例 |
| ![檢查][1] | 檢查混合式 Azure AD 聯結的內部部署 AD UPN 支援 |

## <a name="review-supported-devices"></a>檢閱支援的裝置

混合式 Azure AD Join 支援廣泛的 Windows 裝置。 因為執行舊版 Windows 的裝置設定需要其他或不同的步驟，所以支援的裝置會分為兩類：

### <a name="windows-current-devices"></a>現行 Windows 裝置

- Windows 10
- Windows Server 2016
- Windows Server 2019

對於執行 Windows 桌面作業系統的裝置, 支援的版本會列在這篇文章中的[windows 10 版本資訊](https://docs.microsoft.com/windows/release-information/)。 最佳作法是 Microsoft 建議您升級至最新版本的 Windows 10。

### <a name="windows-down-level-devices"></a>舊版 Windows 裝置

- Windows 8.1
- Windows 7。 如需 Windows 7 的支援資訊, 請參閱這篇文章[的 windows 7 支援即將結束](https://www.microsoft.com/windowsforbusiness/end-of-windows-7-support)
- Windows Server 2012 R2
- Windows Server 2012
- Windows Server 2008 R2

在第一個規劃步驟中，您應該檢閱您的環境，並判斷是否需要支援 Windows 舊版裝置。

## <a name="review-things-you-should-know"></a>檢閱您應該知道的事情

如果您的環境包含將識別資料同步處理至多個 Azure AD 租使用者的單一 AD 樹系, 則目前不支援混合式 Azure AD 聯結。

使用虛擬桌面基礎結構 (VDI) 時, 目前不支援加入混合式 Azure AD。

混合式 Azure AD 聯結不支援符合 FIPS 標準的 Tpm。 如果您的裝置具有 FIPS 相容的 Tpm, 您必須先停用它們, 才能繼續進行混合式 Azure AD 聯結。 Microsoft 不會提供任何工具來停用 Tpm 的 FIPS 模式, 因為它相依于 TPM 製造商。 請洽詢您的硬體 OEM 以取得支援。

執行網域控制站 (DC) 角色的 Windows Server 不支援混合式 Azure AD 聯結。

使用認證漫遊或使用者設定檔漫遊時, 舊版 Windows 裝置不支援混合式 Azure AD 聯結。

如果您依賴系統準備工具 (Sysprep), 而且您使用**Windows 前 10 1809**映射來進行安裝, 請確定映射不是來自已向 Azure AD 註冊的裝置混合式 Azure AD 聯結。

如果您依賴虛擬機器 (VM) 快照集來建立其他 Vm, 請確定快照集不是來自已向 Azure AD 註冊的 VM, 因為混合式 Azure AD 聯結。

如果您已加入網域的 Windows 10 裝置已向租用戶[註冊 Azure AD](overview.md#getting-devices-in-azure-ad)，我們強烈建議您先移除該狀態，再啟用混合式 Azure AD Join。 自 Windows 10 1809 版起，我們已進行下列變更以避免這種雙重狀態：

- 在裝置加入混合式 Azure AD 之後，就會自動移除任何現有的 Azure AD 註冊狀態。
- 您可以新增下列登錄機碼-HKLM\SOFTWARE\Policies\Microsoft\Windows\WorkplaceJoin、"BlockAADWorkplaceJoin" = dword: 00000001, 以防止加入網域的裝置 Azure AD 註冊。
- 此變更現在適用于已套用 KB4489894 的 Windows 10 1803 版本。 不過, 如果您已設定 Windows Hello 企業版, 則使用者必須在雙重狀態清除之後重新設定 Windows Hello 企業版。

## <a name="review-controlled-validation-of-hybrid-azure-ad-join"></a>審查混合式 Azure AD 聯結的受控制驗證

當所有必要條件都已就緒時, Windows 裝置會自動在您的 Azure AD 租使用者中註冊為裝置。 這些裝置身分識別在 Azure AD 中的狀態稱為混合式 Azure AD 聯結。 如需本文中所涵蓋概念的詳細資訊, 請參閱[Azure Active Directory 中的裝置身分識別管理簡介](overview.md)和[規劃混合式 Azure Active Directory 聯結執行](hybrid-azuread-join-plan.md)。

組織可能會想要對混合式 Azure AD 聯結進行受控制的驗證, 然後一次在整個組織中啟用它。 請參閱[受控制的混合式 Azure AD 聯結驗證一](hybrid-azuread-join-control.md)文, 以瞭解如何完成。

## <a name="select-your-scenario-based-on-your-identity-infrastructure"></a>根據您的身分識別基礎結構來選取您的案例

混合式 Azure AD join 同時適用于、受控和同盟環境。  

### <a name="managed-environment"></a>受控環境

受控環境可使用[無縫單一登入](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sso)透過[密碼雜湊同步 (PHS)](https://docs.microsoft.com/azure/active-directory/hybrid/whatis-phs) 或[傳遞驗證 (PTA)](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-pta) 進行部署。

在這些案例中，您不需要設定同盟伺服器以進行驗證。

### <a name="federated-environment"></a>同盟環境

同盟環境應具有支援下列需求的識別提供者:

- **WS-TRUST 通訊協定:** 必須使用此通訊協定, 才能向 Azure AD 驗證已加入 Windows 的混合式 Azure AD 裝置。
- **WIAORMULTIAUTHN 宣告:** 需要此宣告, 才能執行適用于舊版 Windows 裝置的混合式 Azure AD 聯結。

如果您的同盟環境使用 Active Directory 同盟服務 (AD FS), 則已支援上述需求。

> [!NOTE]
> Azure AD 不支援受控網域中的智慧卡或憑證。

從 1.1.819.0 版開始，Azure AD Connect 即為您提供用來設定混合式 Azure AD Join 的精靈。 此精靈可讓您大幅簡化設定程序。 如果安裝必要的 Azure AD Connect 版本不適合您，請參閱[如何手動設定裝置註冊](hybrid-azuread-join-manual.md)。 

根據符合您身分識別基礎結構的案例, 請參閱:

- [設定聯合環境的混合式 Azure Active Directory 聯結](hybrid-azuread-join-federated-domains.md)
- [設定受管理環境的混合式 Azure Active Directory 聯結](hybrid-azuread-join-managed-domains.md)

## <a name="review-on-premises-ad-upn-support-for-hybrid-azure-ad-join"></a>檢查混合式 Azure AD join 的內部部署 AD UPN 支援

有時候，您的內部部署 AD UPN 可能與 Azure AD UPN 不同。 在此情況下，Windows 10 混合式 Azure AD Join 會根據[驗證方法](https://docs.microsoft.com/azure/security/azure-ad-choose-authn)、網域類型和 Windows 10 版本，提供有限的內部部署 AD UPN 支援。 您的環境中可以有兩種類型的內部部署 AD UPN：

- 可路由的 UPN：可路由的 UPN 具有有效的已驗證網域，該網域已向網域註冊機構註冊。 例如，如果 Azure AD 中的主要網域為 contoso.com，則 contoso.org 在 Contoso 所擁有且[已在 Azure AD 中驗證](https://docs.microsoft.com/azure/active-directory/fundamentals/add-custom-domain)的內部部署 AD 中是主要網域
- 不可路由的 UPN：不可路由的 UPN 沒有已驗證的網域。 它僅適用於組織的私人網路內。 例如，如果 Azure AD 中的主要網域為 contoso.com，則 contoso.local 是內部部署 AD 中的主要網域，但不是網際網路中可驗證的網域，而且只能在 Contoso 的網路內使用。

下表提供有關在 Windows 10 混合式 Azure AD Join 中支援這些內部部署 AD UPN 的詳細資料

| 內部部署 AD UPN 的類型 | 網域類型 | Windows 10 版本 | 描述 |
| ----- | ----- | ----- | ----- |
| 路由式 | 同盟 | 自 1703 版起 | 正式推出 |
| 非可路由傳送 | 同盟 | 自 1803 版起 | 正式推出 |
| 路由式 | 受控 | 不支援 | |
| 非可路由傳送 | 受控 | 不支援 | |

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [設定同盟環境](hybrid-azuread-join-federated-domains.md)
> 的混合式 Azure Active Directory 聯結[設定受管理環境的混合式 Azure Active Directory 聯結](hybrid-azuread-join-managed-domains.md)

<!--Image references-->
[1]: ./media/hybrid-azuread-join-plan/12.png
