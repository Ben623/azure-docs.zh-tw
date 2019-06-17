---
title: Azure Active Directory 裝置管理常見問題集 | Microsoft Docs
description: Azure Active Directory 裝置管理常見問題集。
services: active-directory
documentationcenter: ''
author: MicrosoftGuyJFlo
manager: daveba
ms.assetid: cdc25576-37f2-4afb-a786-f59ba4c284c2
ms.service: active-directory
ms.subservice: devices
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/22/2019
ms.author: joflore
ms.reviewer: jairoc
ms.collection: M365-identity-device-management
ms.openlocfilehash: e29c58c0e9a31b2eb3e3d7e237a3db8173214faf
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "67110656"
---
# <a name="azure-active-directory-device-management-faq"></a>Azure Active Directory 裝置管理常見問題集

### <a name="q-i-registered-the-device-recently-why-cant-i-see-the-device-under-my-user-info-in-the-azure-portal-or-why-is-the-device-owner-marked-as-na-for-hybrid-azure-active-directory-azure-ad-joined-devices"></a>問：我最近註冊了裝置。 為什麼在 Azure 入口網站中我的使用者資訊底下看不到該裝置？ 或是為何要將裝置擁有者標示為不適用於混合式 Azure Active Directory (Azure AD) 已加入裝置嗎？

**答：** 已加入混合式 Azure AD 的 Windows 10 裝置不會顯示在 [使用者裝置]  底下。
請使用 Azure 入口網站中的 [所有裝置]  檢視。 您也可以使用 PowerShell [Get-MsolDevice](https://docs.microsoft.com/powershell/module/msonline/get-msoldevice?view=azureadps-1.0) Cmdlet。

只有下列裝置會列在 [使用者裝置]  底下：

- 所有未加入混合式 Azure AD 的個人裝置。 
- 所有非 Windows 10 或 Windows Server 2016 裝置。
- 所有非 Windows 裝置。 

---

### <a name="q-how-do-i-know-what-the-device-registration-state-of-the-client-is"></a>問：我如何才能知道用戶端的裝置註冊狀態為何？

**答：** 在 Azure 入口網站中，移至 [所有裝置]  。 使用裝置識別碼來搜尋裝置。 檢查加入類型資料行中的值。 有時，可能會將裝置重設或重新安裝映像。 因此，檢查裝置上的裝置註冊狀態也很重要：

- 針對 Windows 10 和 Windows Server 2016 或更新的裝置，請執行 `dsregcmd.exe /status`。
- 針對舊版 OS 版本，請執行`%programFiles%\Microsoft Workplace Join\autoworkplace.exe`。

---

### <a name="q-i-see-the-device-record-under-the-user-info-in-the-azure-portal-and-i-see-the-state-as-registered-on-the-device-am-i-set-up-correctly-to-use-conditional-access"></a>問：我在 Azure 入口網站中的 [使用者資訊] 底下看到裝置記錄。 並且看到該裝置上的狀態為已註冊。 要設定正確使用條件式存取？

**答：** 裝置的聯結狀態，以顯示**deviceID**必須符合 Azure AD 上的狀態並滿足條件式存取的所有評估準則。 如需詳細資訊，請參閱 <<c0> [ 需要受管理的裝置存取雲端應用程式使用條件式存取](../conditional-access/require-managed-devices.md)。

---

### <a name="q-i-deleted-my-device-in-the-azure-portal-or-by-using-windows-powershell-but-the-local-state-on-the-device-says-its-still-registered"></a>問：我已在 Azure 入口網站中或藉由使用 Windows PowerShell 刪除我的裝置。 但在裝置上的本機狀態表示它仍註冊。

**答：** 這是刻意安排的作業。 該裝置並無法存取雲端中的資源。 

如果您想要重新註冊，必須對該裝置採取手動動作。 

若要從已加入內部部署 Active Directory 網域的 Windows 10 和 Windows Server 2016 清除加入狀態，請執行下列步驟：

1.  以系統管理員身分開啟命令提示字元。

2.  輸入 `dsregcmd.exe /debug /leave` 。

3.  登出後再登入，以觸發向 Azure AD 重新註冊裝置的排定工作。 

針對已加入內部部署 Active Directory 網域的舊版 Windows OS 版本，請執行下列步驟：

1.  以系統管理員身分開啟命令提示字元。
2.  輸入 `"%programFiles%\Microsoft Workplace Join\autoworkplace.exe /l"` 。
3.  輸入 `"%programFiles%\Microsoft Workplace Join\autoworkplace.exe /j"` 。

---

### <a name="q-why-do-i-see-duplicate-device-entries-in-the-azure-portal"></a>問：為什麼看到重複的裝置在 Azure 入口網站中的項目？

**答：**

-   就 Windows 10 和 Windows Server 2016 而言，如果重複嘗試將同一個裝置取消加入再重新加入，就可能導致產生重複的項目。 

-   每個使用 [新增工作或學校帳戶]  的 Windows 使用者都會以相同的裝置名稱建立一個新裝置記錄。

-   針對已加入內部部署 Active Directory 網域的舊版 Windows OS 版本，自動註冊會為每個登入裝置的網域使用者，以相同的裝置名稱建立一個新裝置記錄。 

-   已加入 Azure AD 的機器如果經過抹除、重新安裝，然後再以相同名稱重新加入，就會顯示成另一個具有相同裝置名稱的記錄。

---

### <a name="q-does-windows-10-device-registration-in-azure-ad-support-tpms-in-fips-mode"></a>問：在 Azure AD 中的 Windows 10 裝置註冊並在 FIPS 模式下支援 Tpm？

**答：** 否，目前在 Windows 10 上所有的裝置狀態-混合式 Azure AD join、 加入 Azure AD 和 Azure AD 註冊-裝置註冊不支援 Tpm 在 FIPS 模式中。 已成功加入，或註冊至 Azure AD，FIPS 模式需要在這些裝置上的 tpm 已關閉

---

**问：為什麼使用者仍然能夠從我已在 Azure 入口網站中停用的裝置存取資源？**

**答：** 套用撤銷最多需要一小時的時間才能完成。

>[!NOTE] 
>針對已註冊的裝置，建議您將裝置抹除，以確保使用者無法存取資源。 如需詳細資訊，請參閱[什麼是裝置註冊？](https://docs.microsoft.com/intune/deploy-use/enroll-devices-in-microsoft-intune)。 

---

## <a name="azure-ad-join-faq"></a>Azure AD Join 常見問題集

### <a name="q-how-do-i-unjoin-an-azure-ad-joined-device-locally-on-the-device"></a>問：我如何退出 Azure AD 聯結的裝置在本機裝置上？

**答：** 
- 針對已加入混合式 Azure AD 的裝置，請務必關閉自動註冊。 如此一來，排定的工作就不會重新註冊該裝置。 接著，以系統管理員身分開啟命令提示字元，然後輸入 `dsregcmd.exe /debug /leave`。 或者，若要進行大量退出，請以指令碼方式跨多個裝置執行此命令。

- 針對純粹的已加入 Azure AD 裝置，請確定您具有離線的本機系統管理員帳戶，或建立一個這樣的帳戶。 您無法使用任何 Azure AD 使用者認證來登入。 接下來，移至 [設定]   > [帳戶]   > [存取公司或學校資源]  。 選取您的帳戶，然後選取 [中斷連線]  。 遵循提示，然後在系統提示您時提供本機系統管理員認證。 重新啟動裝置以完成退出程序。

---

### <a name="q-can-my-users-sign-in-to-azure-ad-joined-devices-that-are-deleted-or-disabled-in-azure-ad"></a>問：我的使用者登入已被刪除或停用 Azure AD 中的 Azure AD 已加入裝置嗎？

**答：** 是。 Windows 具有快取使用者名稱和密碼的功能，可讓先前曾登入的使用者即使沒有網路連線，也能快速存取桌面。 

當裝置在 Azure AD 中被刪除或停用時，Windows 裝置並不知道此狀況。 所以先前曾登入的使用者能夠繼續以快取的使用者名稱和密碼存取桌面。 但是，刪除或停用裝置時，使用者無法存取受裝置型條件式存取的任何資源。 

先前未曾登入的使用者無法存取裝置。 沒有任何已為他們啟用的快取使用者名稱和密碼。 

---

### <a name="q-can-disabled-or-deleted-users-sign-in-to-azure-ad-joined-devices"></a>問：可以停用或已刪除的使用者登入 Azure AD 加入裝置？

**答：** 是，但時間有限。 當使用者在 Azure AD 中被刪除或停用時，Windows 裝置並不會立即得知此狀況。 所以先前曾登入的使用者能夠以快取的使用者名稱和密碼存取桌面。 

一般而言，裝置會在四小時內得知使用者狀態。 然後 Windows 就會封鎖這些使用者對桌面的存取。 由於使用者已在 Azure AD 中被刪除或停用，因此系統會撤銷他們的所有權杖。 所以他們無法存取任何資源。 

已被刪除或停用的使用者如果先前未曾登入，便無法存取裝置。 沒有任何已為他們啟用的快取使用者名稱和密碼。 

---

### <a name="q-why-do-my-users-have-issues-on-azure-ad-joined-devices-after-changing-their-upn"></a>問：為什麼我的使用者有問題已加入 Azure AD 的裝置上變更其 UPN 之後？

**答：** 目前，已加入 Azure AD 的裝置並未完全支援 UPN 變更。 因此，他們在變更其 UPN 之後，會在向 Azure AD 驗證時失敗。 所以使用者會在其裝置上遇到 SSO 和條件式存取的問題。 目前，使用者必須透過 [其他使用者] 圖格以其新的 UPN 登入 Windows 來解決此問題。 我們目前正努力解決此問題。 不過，使用 Windows Hello 企業版來登入的使用者不會遇到這個問題。 

---

### <a name="q-my-users-cant-search-printers-from-azure-ad-joined-devices-how-can-i-enable-printing-from-those-devices"></a>問：我的使用者無法從已加入 Azure AD 的裝置搜尋印表機。 如何啟用從這些裝置列印？

**答：** 若要為已加入 Azure AD 的裝置部署印表機，請參閱[使用預先驗證來部署 Windows Server 混合式雲端列印](https://docs.microsoft.com/windows-server/administration/hybrid-cloud-print/hybrid-cloud-print-deploy) \(英文\)。 您需要有內部部署的 Windows Server，才能部署混合式雲端列印。 目前尚無法使用雲端式列印服務。 

---

### <a name="q-how-do-i-connect-to-a-remote-azure-ad-joined-device"></a>問：如何連線到遠端的 Azure AD 已加入的裝置？

**答：** 請參閱[連線到遠端加入 Azure Active Directory 的電腦](https://docs.microsoft.com/windows/client-management/connect-to-remote-aadj-pc)。

---

### <a name="q-why-do-my-users-see-you-cant-get-there-from-here"></a>問：為什麼我的使用者會看到*您無法從這裡前往該處*？

**答：** 您是否有設定特定的條件式存取規則，來要求特定的裝置狀態？ 如果裝置不符合準則，使用者就會被封鎖而看到該訊息。 評估條件式存取原則規則。 確定裝置符合準則，以避免收到該訊息。

---

### <a name="q-why-dont-some-of-my-users-get-azure-multi-factor-authentication-prompts-on-azure-ad-joined-devices"></a>問：為什麼不一些我的使用者取得 Azure Multi-factor Authentication 提示裝置上的已加入 Azure AD？

**答：** 使用者可以使用 Multi-Factor Authentication 向 Azure AD 註冊裝置。 如此一來，裝置本身就會變成該使用者的第二受信任要素。 每當同一個使用者登入裝置並存取應用程式時，Azure AD 都會將該裝置視為第二個要素。 它可讓該使用者順暢地存取應用程式，而不會出現額外的 Multi-Factor Authentication 提示。 

此行為：

- 適用於 Azure AD 已加入和 Azure AD 已註冊裝置，但不適用於混合式 Azure AD 已加入裝置。

- 它不適用於任何其他登入該裝置的使用者。 所以所有其他存取該裝置的使用者都會收到 Multi-Factor Authentication 挑戰。 然後，他們便可存取要求 Multi-Factor Authentication 的應用程式。

---

### <a name="q-why-do-i-get-a-username-or-password-is-incorrect-message-for-a-device-i-just-joined-to-azure-ad"></a>問：為什麼我會收到*使用者名稱或密碼不正確*我剛加入至 Azure AD 裝置的訊息？

**答：** 此情況的常見原因如下：

- 您的使用者認證已經無效。

- 您的電腦無法與 Azure Active Directory 進行通訊。 請檢查是否有任何網路連線問題。

- 同盟登入會要求同盟伺服器支援已啟用並可供存取的 WS-Trust 端點。 

- 您已啟用傳遞驗證。 所以當您登入時，必須變更暫時密碼。

---

### <a name="q-why-do-i-see-the-oops-an-error-occurred-dialog-when-i-try-to-azure-ad-join-my-pc"></a>問：為什麼看到*糟糕...發生錯誤 ！* 當我嘗試將 Azure AD 將電腦加入嗎？

**答：** 當您使用 Intune 來設定 Azure Active Directory 註冊時，就會發生此錯誤。 請確定嘗試加入 Azure AD 的使用者已獲指派正確的 Intune 授權。 如需詳細資訊，請參閱[設定 Windows 裝置的註冊](https://docs.microsoft.com/intune/windows-enroll)。  

---

### <a name="q-why-did-my-attempt-to-azure-ad-join-a-pc-fail-although-i-didnt-get-any-error-information"></a>問：為什麼我嘗試將 Azure AD 加入電腦故障時，雖然我沒有收到任何錯誤訊息？

**答：** 可能是因為您使用本機內建的系統管理員帳戶來登入裝置的緣故。 請在使用 Azure Active Directory Join 來完成設定之前，先建立一個不同的本機帳戶。 

---

### <a name="qwhat-are-the-ms-organization-p2p-access-certificates-present-on-our-windows-10-devices"></a>問︰ 什麼是 MS-組織-P2P-存取憑證出現在我們的 Windows 10 裝置上嗎？

**答：** MS-Organization-P2P-Access 憑證是由 Azure AD 簽發給已加入 Azure AD 之裝置和已加入混合式 Azure AD 之裝置的憑證。 這些憑證可用來啟用相同租用戶中裝置間的信任，以進行遠端桌面存取。 一個憑證簽發給裝置，另一個憑證則簽發給使用者。 裝置憑證位於 `Local Computer\Personal\Certificates` 中，且有效期為一天。 如果裝置在 Azure AD 中仍處於作用中，系統就會更新此憑證 (透過簽發新憑證的方式)。 使用者憑證位於 `Current User\Personal\Certificates` 中，此憑證的有效期也是一天，但它是在使用者嘗試對另一個已加入 Azure AD 的裝置建立遠端桌面工作階段時視需要簽發的憑證。 系統並不會在其到期時予以更新。 這兩種憑證都是使用 `Local Computer\AAD Token Issuer\Certificates` 中的 MS-Organization-P2P-Access 憑證來簽發的。 而此憑證則是由 Azure AD 在裝置註冊期間所簽發的。 

---

### <a name="qwhy-do-i-see-multiple-expired-certificates-issued-by-ms-organization-p2p-access-on-our-windows-10-devices-how-can-i-delete-them"></a>Q:Why 我會看到多個公布在我們的 Windows 10 裝置上的 MS-組織-P2P-存取過期的憑證嗎？ 如何刪除它們？

**答：** 在 Windows 10 1709 版和更舊的版本上已發現一個問題，就是因為密碼編譯問題，導致過期的 MS-Organization-P2P-Access 憑證繼續存在電腦存放區上。 如果您使用任何無法處理大量過期憑證的 VPN 用戶端 (例如 Cisco AnyConnect)，使用者就可能遇到網路連線問題。 此問題在 Windows 10 1803 版中已修正，可自動刪除任何這類過期的 MS-Organization-P2P-Access 憑證。 您可以將裝置更新至 Windows 10 1803 來解決此問題。 如果無法更新，您可以刪除這些憑證，而不會產生任何負面影響。  

---


## <a name="hybrid-azure-ad-join-faq"></a>混合式 Azure AD Join 常見問題集

### <a name="q-where-can-i-find-troubleshooting-information-to-diagnose-hybrid-azure-ad-join-failures"></a>問：哪裡可以找到疑難排解診斷混合式 Azure AD 聯結失敗的資訊？

**答：** 如需疑難排解資訊，請參閱下列文章：

- [針對已加入混合式 Azure Active Directory 的 Windows 10 和 Windows Server 2016 裝置進行疑難排解](troubleshoot-hybrid-join-windows-current.md)

- [針對已加入混合式 Azure Active Directory 的下層裝置進行疑難排解](troubleshoot-hybrid-join-windows-legacy.md)
 
### <a name="q-why-do-i-see-a-duplicate-azure-ad-registered-record-for-my-windows-10-hybrid-azure-ad-joined-device-in-the-azure-ad-devices-list"></a>問：為什麼看到重複的 Azure AD 註冊的記錄我的 Windows 10 混合式 Azure AD 已加入 Azure AD 的 [裝置] 清單中的裝置？

**答：** 當使用者在已加入網域的裝置上將其帳戶新增至應用程式時，系統可能會對他們顯示「將帳戶新增至 Windows？」  提示。 如果他們在出現提示時輸入**是**，裝置就會向 Azure AD 註冊。 信任類型會標示為 [Azure AD 已註冊]。 在您於組織中啟用混合式 Azure AD Join 之後，裝置也會加入混合式 Azure AD。 如此一來，針對同一個裝置就會顯示兩個裝置狀態。 

混合式 Azure AD Join 的優先順序會高於 Azure AD 已註冊狀態。 因此您的裝置會被視為加入混合式 Azure AD 進行任何驗證和條件式存取評估。 您可以從 Azure AD 入口網站中放心地刪除 Azure AD 已註冊裝置記錄。 了解如何[在 Windows 10 機器上避免或清除此雙重狀態](https://docs.microsoft.com/azure/active-directory/devices/hybrid-azuread-join-plan#review-things-you-should-know)。 


---

### <a name="q-why-do-my-users-have-issues-on-windows-10-hybrid-azure-ad-joined-devices-after-changing-their-upn"></a>問：為什麼我的使用者有問題在混合式 Azure AD 已加入的 Windows 10 裝置上變更其 UPN 之後？

**答：** 目前，已加入混合式 Azure AD 的裝置並未完全支援 UPN 變更。 雖然使用者可以登入裝置，並存取其內部部署應用程式，但在變更 UPN 之後，向 Azure AD 進行驗證會失敗。 所以使用者會在其裝置上遇到 SSO 和條件式存取的問題。 目前，您必須先讓裝置取消加入 Azure AD (使用較高的權限執行 "dsregcmd /leave") 再重新加入 (會自動發生)，以解決此問題。 我們目前正努力解決此問題。 不過，使用 Windows Hello 企業版來登入的使用者不會遇到這個問題。 

---

### <a name="q-do-windows-10-hybrid-azure-ad-joined-devices-require-line-of-sight-to-the-domain-controller-to-get-access-to-cloud-resources"></a>問：混合式 Azure AD 已加入的 Windows 10 裝置需要直視網域控制站，以取得雲端資源的存取權嗎？

**答：** 通常不會，但使用者的密碼變更時。 Windows 10 混合式 Azure AD 聯結完成而且使用者至少登入一次後，裝置不需要網域控制站的視線，即可存取雲端資源。 除了密碼變更時，Windows 10 可以透過網際網路連線在任何地方取得 Azure AD 應用程式的單一登入。 使用 Windows hello 企業版登入繼續取得單一使用者登入 Azure AD 應用程式即使在密碼變更，即使他們沒有其網域控制站的視野。 

---

### <a name="q-what-happens-if-a-user-changes-their-password-and-tries-to-login-to-their-windows-10-hybrid-azure-ad-joined-device-outside-the-corporate-network"></a>問：如果使用者變更其密碼，並嘗試登入其 Windows 10 的混合式 Azure AD 已加入公司網路外部的裝置？

**答：** 如果公司網路外部變更密碼 （例如，藉由使用 Azure AD SSPR），然後以新密碼的使用者登入將會失敗。 對於混合式 Azure AD 已加入裝置，在內部部署 Active Directory 會是主要的授權單位。 當裝置不需要直視網域控制站時，就無法驗證新的密碼。 因此，使用者就必須建立與網域控制站 （無論是透過 VPN 或公司網路中） 連線他們能夠登入的裝置有新密碼之前。 否則，他們用來登入舊密碼因為 Windows 中的快取登入功能。 不過，舊的密碼都無效的權杖要求期間的 Azure AD 和因此，防止單一登入和任何裝置型條件式存取原則就會失敗。 如果您使用 Windows Hello 企業版，則不會發生此問題。 

---


## <a name="azure-ad-register-faq"></a>Azure AD 註冊常見問題集

### <a name="q-can-i-register-android-or-ios-byod-devices"></a>問：我是否可以註冊 Android 或 iOS 的 BYOD 裝置？

**答：** 可以，但只能使用 Azure 裝置註冊服務，且僅適用於混合式客戶。 不支援使用「Active Directory 同盟服務」(AD FS) 中的內部部署裝置註冊服務。

### <a name="q-how-can-i-register-a-macos-device"></a>問：如何註冊 macOS 裝置？

**答：** 請執行下列步驟：

1.  [建立裝置相容性原則](https://docs.microsoft.com/intune/compliance-policy-create-mac-os)
2.  [定義 macOS 裝置的條件式存取原則](../active-directory-conditional-access-azure-portal.md) 

**備註：**

- 包含在您的條件式存取原則需求的使用者[支援適用於 macOS 的 Office 版本](../conditional-access/technical-reference.md#client-apps-condition)來存取資源。 

- 在第一次嘗試存取時，系統會提示使用者使用公司入口網站來註冊裝置。

