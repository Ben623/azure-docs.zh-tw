---
title: Azure Active Directory 中條件式存取的最佳做法 |Microsoft Docs
description: 了解您應該知道的事項，以及它是設定條件式存取原則時應避免的動作。
services: active-directory
keywords: 條件式存取應用程式，與 Azure AD，安全地存取公司資源，條件式存取原則的條件式存取
documentationcenter: ''
author: MicrosoftGuyJFlo
manager: daveba
editor: ''
ms.assetid: 8c1d978f-e80b-420e-853a-8bbddc4bcdad
ms.service: active-directory
ms.subservice: conditional-access
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 01/25/2019
ms.author: joflore
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8e7b77376989031dc1697d155cccf59954233a85
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "67112663"
---
# <a name="best-practices-for-conditional-access-in-azure-active-directory"></a>Azure Active Directory 中條件式存取的最佳作法

具有[Azure Active Directory (Azure AD) 條件式存取](../active-directory-conditional-access-azure-portal.md)，您可以控制授權的使用者如何存取您雲端應用程式。 本文提供以下相關資訊：

- 您應該知道的事情 
- 什麼是您應該避免進行設定條件式存取原則時。 

本文假設，您很熟悉的概念與術語中所述[什麼是 Azure Active Directory 中的條件式存取？](../active-directory-conditional-access-azure-portal.md)



## <a name="whats-required-to-make-a-policy-work"></a>讓原則運作的必要條件？

當您建立新的原則時，未選取任何使用者、群組、應用程式或存取控制項。

![雲端應用程式](./media/best-practices/02.png)


若要讓您的原則運作，您必須設定：


| 何事           | 方式                                  | 理由 |
| :--            | :--                                  | :-- |
| **雲端應用程式** |選取一或多個應用程式。  | 條件式存取原則的目標是要讓您控制授權的使用者如何存取雲端應用程式。|
| **使用者和群組** | 選取至少一個已獲授權存取您所選雲端應用程式的使用者或群組。 | 沒有任何使用者和群組指派，條件式存取原則永遠不會觸發。 |
| **存取控制** | 選取至少一個存取控制。 | 如果滿足您的條件，您的原則處理器需要知道該怎麼辦。 |




## <a name="what-you-should-know"></a>您應該知道的事情



### <a name="how-are-conditional-access-policies-applied"></a>如何套用條件式存取原則？

當您在存取雲端應用程式時，可能會套用一個以上的條件式存取原則。 在此情況下，您必須滿足所有套用的原則。 例如，如果其中一個原則需要 MFA，而第二個原則需要符合規範的裝置，則您必須通過 MFA，並使用符合規範的裝置。 

所有的原則都會在兩個階段強制執行：

- 在**第一個**階段中，系統會評估所有原則，並收集不符合的所有存取控制。 

- 在**第二個**階段中，系統會提示您滿足您還沒有符合的需求。 如果其中一個原則會封鎖存取，則您會遭到封鎖，而且不會提示您滿足其他原則控制。 如果沒有任何原則會封鎖您，系統會提示您以下列順序滿足其他原則控制：

    ![順序](./media/best-practices/06.png)
    
    外部的 MFA 提供者及使用規定來自下一步。



### <a name="how-are-assignments-evaluated"></a>如何評估指派？

所有指派邏輯上都會使用 **AND** 運算子。 如果您已設定多個指派，必須滿足所有的指派才能觸發原則。  

如果您需要設定一個位置條件，以套用至從您的組織網路外部進行的所有連線︰

- 包含**所有位置**
- 排除**所有信任的 IP**


### <a name="what-to-do-if-you-are-locked-out-of-the-azure-ad-admin-portal"></a>如果被鎖定在 Azure AD 管理入口網站之外該怎麼辦？

如果您遭到鎖定而無法 Azure AD 入口網站，因為不正確的設定中的條件式存取原則：

- 確認您的組織中是否有其他系統管理員尚未遭到封鎖。 具有 Azure 入口網站存取權的系統管理員可以停用會影響您的登入的原則。 

- 如果您的組織中沒有任何系統管理員可以更新原則，您需要提交支援要求。 Microsoft 支援服務可以檢閱，並更新導致您無法存取的條件式存取原則。


### <a name="what-happens-if-you-have-policies-in-the-azure-classic-portal-and-azure-portal-configured"></a>如果您已在 Azure 傳統入口網站和 Azure 入口網站中設定一些原則，則會發生什麼情況？  

Azure Active Directory 會強制執行這兩個原則，而且只有在符合所有需求時，使用者才可取得存取權。

### <a name="what-happens-if-you-have-policies-in-the-intune-silverlight-portal-and-the-azure-portal"></a>如果您已在 Intune Silverlight 入口網站和 Azure 入口網站中設定一些原則，則會發生什麼情況？

Azure Active Directory 會強制執行這兩個原則，而且只有在符合所有需求時，使用者才可取得存取權。

### <a name="what-happens-if-i-have-multiple-policies-for-the-same-user-configured"></a>如果我已針對同一個使用者設定多個原則，則會發生什麼情況？  

針對每次登入，Azure Active Directory 會評估所有的原則，並確保在授與使用者存取權之前已符合所有的需求。 封鎖存取的優先順序高過所有其他組態設定。 


### <a name="does-conditional-access-work-with-exchange-activesync"></a>沒有適用於 Exchange ActiveSync 條件式存取？

是，您可以使用一些條件式存取原則中的 Exchange ActiveSync[限制](https://docs.microsoft.com/azure/active-directory/conditional-access/conditional-access-for-exo-and-spo)。 

### <a name="how-should-you-configure-conditional-access-with-office-365-apps"></a>您應該使用 Office 365 應用程式如何設定條件式存取？

由於 Office 365 應用程式都互相連線，我們建議通常指派應用程式一起使用時建立原則。

常見的互連應用程式包括 Microsoft Flow、 Microsoft Planner、 Microsoft Teams、 Office 365 Exchange Online、 Office 365 SharePoint Online 和 Office 365 Yammer。

請務必針對存取控制的工作階段或工作開始時，需要使用者互動，例如多重要素驗證的原則。 如果沒有，使用者將無法完成應用程式內的某些工作。 比方說，如果您需要多因素驗證未受管理的裝置存取 SharePoint 而非電子郵件時，使用其電子郵件中的使用者無法再將 SharePoint 檔案附加至訊息。 可以在本文中，找到更多資訊[什麼是 Azure Active Directory 條件式存取中的服務相依性？](service-dependencies.md)。





## <a name="what-you-should-avoid-doing"></a>您應該避免做的事

條件式存取架構為您提供絕佳的組態彈性。 不過，絕佳的彈性也意謂著您應該先仔細地檢閱每個設定原則，然後才發行它，以避免產生不想要的結果。 在此情況下，您應該特別注意影響整個集合的指派，例如 **all users / groups / cloud apps**。

在您的環境中，應該避免使用下列組態：


**針對所有使用者、所有雲端應用程式：**

- **封鎖存取** - 此組態會封鎖您整個組織，這絕對不是一個好方法。

- **需要符合規範的裝置** - 針對尚未註冊其裝置的使用者，此原則會封鎖所有存取，包括對 Intune 入口網站的存取。 如果您是沒有已註冊裝置的系統管理員，此原則會阻擋您回到 Azure 入口網站來變更此原則。

- **需要加入網域** - 如果您還沒有已加入網域的裝置，此原則也可能封鎖您組織中所有使用者的存取。

- **需要應用程式保護原則**-這個原則區塊存取也有可能會封鎖您的組織中的所有使用者的存取，如果您沒有 Intune 原則。 如果您是系統管理員，而不需要有 Intune 應用程式保護原則的用戶端應用程式時，此原則會阻擋您回到入口網站，例如 Intune 和 Azure。

**針對所有使用者、所有雲端應用程式、所有裝置平台：**

- **封鎖存取** - 此組態會封鎖您整個組織，這絕對不是一個好方法。


## <a name="how-should-you-deploy-a-new-policy"></a>您應該如何部署新的原則？

第一步，您應該使用[該怎麼辦工具](what-if-tool.md)評估您的原則。

為您的環境準備好新原則時，請分階段部署它們：

1. 將原則套用至較少的使用者，確認它如預期般運作。 

2.  當您展開原則，以納入更多使用者時。 繼續排除原則中的所有系統管理員，以確保他們仍然擁有存取權，而且可以在需要變更時，更新原則。

3. 必要時，將原則套用到所有使用者。 

最佳做法是建立這一個使用者帳戶：

- 專門用來管理原則 
- 從您的原則中排除


## <a name="policy-migration"></a>原則移轉

請考慮移轉尚未在 Azure 入口網站中建立的原則，因為：

- 您現在可以處理之前無法處理的案例。

- 您可以合併它們以減少必須管理的原則數目。   

- 您可以管理所有的條件式存取原則，在單一中央位置。

- Azure 傳統入口網站將被淘汰。   


如需詳細資訊，請參閱[在 Azure 入口網站中移轉傳統原則](policy-migration.md)。


## <a name="next-steps"></a>後續步驟

如果您想要知道：

- 如何設定條件式存取原則，請參閱[需要 MFA 的特定應用程式與 Azure Active Directory 條件式存取](app-based-mfa.md)。
- 規劃您的條件式存取原則，請參閱[如何規劃 Azure Active Directory 中的條件式存取部署](plan-conditional-access.md)。
