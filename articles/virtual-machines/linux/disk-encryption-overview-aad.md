---
title: 具有 Azure AD App 必要條件的 Azure 磁碟加密 (舊版)
description: 本文提供要對 IaaS VM 使用 Microsoft Azure 磁碟加密所需滿足的先決條件。
author: msmbaldwin
ms.service: security
ms.topic: article
ms.author: mbaldwin
ms.date: 03/15/2019
ms.custom: seodec18
ms.openlocfilehash: 5f3de0f877186daa8f6add7fcd1546f91d6ce3d2
ms.sourcegitcommit: 827248fa609243839aac3ff01ff40200c8c46966
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/07/2019
ms.locfileid: "73748926"
---
# <a name="azure-disk-encryption-with-azure-ad-previous-release"></a>Azure AD 的 Azure 磁碟加密（上一個版本）

**新版本的 Azure 磁碟加密不需要提供 Azure AD 的應用程式參數，即可啟用 VM 磁片加密。在新版本中，您不再需要在啟用加密步驟期間提供 Azure AD 認證。所有新的 Vm 都必須使用新版本的 Azure AD 應用程式參數進行加密。若要使用新版本來查看啟用 VM 磁片加密的指示，請參閱[Linux vm 的 Azure 磁碟加密](disk-encryption-overview.md)。已使用 Azure AD 應用程式參數進行加密的 Vm 仍然受支援，應該繼續使用 AAD 語法進行維護。**

本文提供[Linux vm](disk-encryption-overview.md)的補充 Azure 磁碟加密，以及 Azure AD （舊版） Azure 磁碟加密的額外需求和必要條件。

這些章節中的資訊保持不變：

- [支援的 Vm 和作業系統](disk-encryption-overview.md#supported-vms-and-operating-systems)
- [其他 VM 需求](disk-encryption-overview.md#additional-vm-requirements)


 

## <a name="networking-and-group-policy"></a>網路和群組原則

**若要使用較舊的 AAD 參數語法啟用 Azure 磁碟加密功能，IaaS VM 必須符合下列網路端點組態需求：** 
  - 若要取得用來連線至金鑰保存庫的權杖，IaaS VM 必須能連線至 Azure Active Directory 端點 \[login.microsoftonline.com\]。
  - 若要將加密金鑰寫入至您的金鑰保存庫，IaaS VM 必須能連接至金鑰保存庫端點。
  - IaaS VM 必須能連接至託管 Azure 擴充儲存機制的 Azure 儲存體端點，和託管 VHD 檔案的 Azure 儲存體帳戶。
  -  如果您的安全性原則會限制從 Azure VM 至網際網路的存取，您可以解析前述的 URI，並設定特定的規則以允許和這些 IP 的輸出連線。 如需詳細資訊，請參閱[防火牆後方的 Azure Key Vault](../../key-vault/key-vault-access-behind-firewall.md)。
  - 在 Windows 中，如果 TLS 1.0 已明確停用，且 .NET 版本尚未更新到 4.6 版或更新版本，則下列登錄變更會啟用 ADE 來選取較新的 TLS 版本：
    
        [HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\.NETFramework\v4.0.30319]
        "SystemDefaultTlsVersions"=dword:00000001
        "SchUseStrongCrypto"=dword:00000001

        [HKEY_LOCAL_MACHINE\SOFTWARE\WOW6432Node\Microsoft\.NETFramework\v4.0.30319]
        "SystemDefaultTlsVersions"=dword:00000001
        "SchUseStrongCrypto"=dword:00000001` 
     

**群組原則：**
 - Azure 磁碟加密解決方案對 Windows IaaS VM 使用 BitLocker 外部金鑰保護裝置。 對於加入網域的 VM，請勿推送任何會強制使用 TPM 保護裝置的群組原則。 如需關於「在不含相容 TPM 的情形下允許使用 BitLocker」的群組原則相關資訊，請參閱 [BitLocker 群組原則參考文件](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-group-policy-settings#bkmk-unlockpol1)。

-  已加入網域的虛擬機器上具有自訂群組策略的 BitLocker 原則必須包含下列設定：設定[BitLocker 修復資訊的使用者儲存體-> 允許256位的修復金鑰](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-group-policy-settings)。 當 BitLocker 的自訂群組原則設定不相容時，Azure 磁碟加密將會失敗。 在沒有正確原則設定的電腦上，您可能必須套用新的原則、強制新的原則進行更新 (gpupdate.exe /force)，然後重新啟動。  

## <a name="encryption-key-storage-requirements"></a>加密金鑰儲存需求  

Azure 磁碟加密需要 Azure Key Vault 來控制及管理磁片加密金鑰和密碼。 您的金鑰保存庫和 Vm 必須位於相同的 Azure 區域和訂用帳戶中。

如需詳細資訊，請參閱[使用 Azure AD 為 Azure 磁碟加密建立和設定金鑰保存庫（舊版）](disk-encryption-key-vault-aad.md)。
 
## <a name="next-steps"></a>後續步驟

- [使用 Azure AD 建立和設定 Azure 磁碟加密的金鑰保存庫（舊版）](disk-encryption-key-vault-aad.md)
- [在 Linux Vm 上使用 Azure AD 啟用 Azure 磁碟加密（舊版）](disk-encryption-linux-aad.md)
- [Azure 磁碟加密必要條件 CLI 腳本](https://github.com/ejarvi/ade-cli-getting-started)
- [Azure 磁碟加密必要條件 PowerShell 腳本](https://github.com/Azure/azure-powershell/tree/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts)