---
title: 遷移條件式存取原則-Azure Active Directory
description: 了解如何在 Azure 入口網站中移轉傳統原則中。
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: article
ms.date: 11/21/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: nigu
ms.collection: M365-identity-device-management
ms.openlocfilehash: 75d664f6e61dbbaaf0b8ab74c392596a206ff644
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/22/2019
ms.locfileid: "74380546"
---
# <a name="what-is-a-policy-migration-in-azure-active-directory-conditional-access"></a>什麼是 Azure Active Directory 條件式存取中的原則遷移？ 

[條件式存取](../active-directory-conditional-access-azure-portal.md)是 Azure Active directory （Azure AD）的功能，可讓您控制授權使用者存取雲端應用程式的方式。 雖然目的仍然相同，但新 Azure 入口網站的發行已引進條件式存取如何運作的重大改善。

請考慮移轉尚未在 Azure 入口網站中建立的原則，因為：

- 您現在可以處理之前無法處理的案例。
- 您可以合併它們以減少必須管理的原則數目。   
- 您可以在一個集中位置管理所有條件式存取原則。
- Azure 傳統入口網站將被淘汰。   

本文說明將現有的條件式存取原則遷移至新架構時需要知道的事項。
 
## <a name="classic-policies"></a>傳統原則

在[Azure 入口網站](https://portal.azure.com)中，[[條件式存取-原則](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ConditionalAccessBlade/Policies)] 頁面是您的條件式存取原則的進入點。 不過，在您的環境中，您可能也有未使用此頁面建立的條件式存取原則。 這些原則也稱為「傳統原則」。 傳統原則是您在中建立的條件式存取原則：

- Azure 傳統入口網站
- Intune 傳統入口網站
- Intune 應用程式防護入口網站

在 [**條件式存取**] 頁面上，按一下 [**管理**] 區段中的 [[**傳統原則（預覽）** ](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ConditionalAccessBlade/ClassicPolicies) ]，即可存取您的傳統原則。 

![Azure Active Directory](./media/policy-migration/71.png)

**傳統原則**檢視會提供選項以便：

- 篩選傳統原則。
 
   ![Azure Active Directory](./media/policy-migration/72.png)

- 停用傳統原則。

   ![Azure Active Directory](./media/policy-migration/73.png)
   
- 檢查傳統原則的設定（並將其停用）。

   ![Azure Active Directory](./media/policy-migration/74.png)

如果您已停用傳統原則，再也無法還原此步驟。 這就是為什麼您可以使用 [詳細資料] 檢視來修改傳統原則中的群組成員資格。 

![Azure Active Directory](./media/policy-migration/75.png)

藉由變更所選的群組或排除特定群組，即可先測試一些測試使用者的已停用傳統原則效果，然後再針對所有包含的使用者和群組停用此原則。 

## <a name="azure-ad-conditional-access-policies"></a>Azure AD 條件式存取原則

透過 Azure 入口網站中的條件式存取，您可以在一個集中位置管理所有的原則。 由於條件式存取的執行方式已有所改變，因此您應該先熟悉基本概念，再遷移傳統原則。

請參閱：

- [什麼是 Azure Active Directory 中的條件式存取](../active-directory-conditional-access-azure-portal.md)，以瞭解基本概念和術語。
- [Azure Active Directory 中的條件式存取最佳做法](best-practices.md)，以取得在您的組織中部署條件式存取的一些指引。
- [使用 Azure Active Directory 條件式存取來要求特定應用程式的 MFA](app-based-mfa.md) ，以熟悉 Azure 入口網站中的使用者介面。
 
## <a name="migration-considerations"></a>移轉考量

在本文中，Azure AD 條件式存取原則也稱為「*新原則*」。
您的傳統原則會繼續與新原則並存運作，直到您停用或刪除它們為止。 

下列是原則彙總內容中的重要層面：

- 雖然傳統原則會繫結至特定雲端應用程式，但您可以在一個新原則中選取您所需要數量的雲端應用程式。
- 雲端應用程式的傳統原則與新原則的控制項要求滿足所有的控制項 (*AND*)。 
- 在新原則中，您可以：
   - 結合多個條件 (如果您的案例所需)。 
   - 選取數個授與需求作為存取控制，並將它們與邏輯 *OR* (需要其中一個選取的控制項) 或與邏輯*AND* (需要所有選取的控制項)結合。

   ![Azure Active Directory](./media/policy-migration/25.png)

### <a name="office-365-exchange-online"></a>Office 365 Exchange Online

如果您想要移轉的 **Office 365 Exchange Online** 傳統原則將 **Exchange Active Sync** 納入為用戶端應用程式條件，您可能無法將其彙總成一個新原則。 

例如，如果您想要支援所有的用戶端應用程式類型，就是這種情況。 在以 **Exchange Active Sync** 作為用戶端應用程式條件的新原則中，您無法選取其他用戶端應用程式。

![Azure Active Directory](./media/policy-migration/64.png)

如果傳統原則包含數個條件，也不可能彙總成一個新的原則。 以 **Exchange Active Sync** 作為用戶端應用程式條件的新原則不支援其他條件：   

![Azure Active Directory](./media/policy-migration/08.png)

如果您已設定以 **Exchange Active Sync** 作為用戶端應用程式條件的新原則，您必須確定並未設定其他條件。 

![Azure Active Directory](./media/policy-migration/16.png)
 
Office 365 Exchange Online [以應用程式為基礎](technical-reference.md#approved-client-app-requirement)並將 **Exchange Active Sync** 納入為用戶端應用程式條件的傳統原則可允許**支援**和**不支援**[的裝置平台](technical-reference.md#device-platform-condition)。 雖然您無法在相關的新原則中設定個別裝置平台，但您可以讓支援僅限於[支援的裝置平台](technical-reference.md#device-platform-condition)。 

![Azure Active Directory](./media/policy-migration/65.png)

您可以彙總多個將 **Exchange Active Sync** 納入為用戶端應用程式條件的傳統原則，前提是您：

- 只有 **Exchange Active Sync** 作為條件 
- 已設定授與存取的幾項需求

常見的案例之一：

- 從 Azure 傳統入口網站彙總以裝置為基礎的傳統原則 
- 在 Intune 應用程式防護入口網站中彙總以應用程式為基礎的傳統原則 
 
在此情況下，您可以將傳統原則彙總成一個已選取兩項需求的新原則。

![Azure Active Directory](./media/policy-migration/62.png)

### <a name="device-platforms"></a>裝置平台

具有[以應用程式為基礎的控制項](technical-reference.md#approved-client-app-requirement)的傳統原則會將 iOS 和 Android 預先設定為[裝置平台條件](technical-reference.md#device-platform-condition)。 

在新的原則中，您必須選取想要個別支援的[裝置平台](technical-reference.md#device-platform-condition)。

![Azure Active Directory](./media/policy-migration/41.png)

## <a name="next-steps"></a>後續步驟

- 如果您想要知道如何設定條件式存取原則，請參閱[利用 Azure Active Directory 條件式存取來取得特定應用程式的 MFA](app-based-mfa.md)。
- 如果您已準備好設定您環境的條件式存取原則，請參閱 [Azure Active Directory 中條件式存取的最佳做法](best-practices.md)。 
