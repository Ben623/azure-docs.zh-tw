---
title: 與 Azure Vm 搭配使用的安全性功能
titleSuffix: Azure security
description: 本文對可用於虛擬機器的 Azure 安全性功能提供核心的概觀。
services: security
documentationcenter: na
author: TerryLanfear
manager: rkarlin
editor: TomSh
ms.assetid: 467b2c83-0352-4e9d-9788-c77fb400fe54
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/2/2019
ms.author: terrylan
ms.openlocfilehash: a1726e18ea8c1ba86d77d7b9ca3d50c444620361
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/27/2020
ms.locfileid: "77657158"
---
# <a name="azure-virtual-machines-security-overview"></a>Azure 虛擬機器安全性概觀
本文提供可與虛擬機器搭配使用的核心 Azure 安全性功能的總覽。

您可以使用 Azure 虛擬機器靈活地部署各種運算方案。 此服務支援 Microsoft Windows、Linux、Microsoft SQL Server、Oracle、IBM、SAP 和 Azure BizTalk 服務。 因此，您幾乎可以在所有作業系統上部署任何工作負載和任何語言。

Azure 虛擬機器讓您能夠有彈性地進行虛擬化，而不需購買並維護執行虛擬機器的實體硬體。 您可以建置並部署您的應用程式，並保證您的資料會在高度安全資料中心中受到安全的保護。

您可以使用 Azure 建置安全性已加強且相容的解決方案：

* 保護虛擬機器抵禦病毒和惡意程式碼。
* 將敏感性資料加密。
* 保護網路流量。
* 識別和偵測威脅。
* 符合規範需求。  

## <a name="antimalware"></a>反惡意程式碼

運用 Azure，您可以使用來自安全性廠商 (例如 Microsoft、Symantec、Trend Micro 和 Kaspersky) 的反惡意程式碼軟體。 此軟體可協助保護您的虛擬機器來抵禦惡意檔案、廣告軟體和其他威脅。

適用於 Azure 雲端服務和虛擬機器的 Microsoft Antimalware 是即時保護功能，有助於識別和移除病毒、間諜軟體和其他惡意軟體。  適用於 Azure 的 Microsoft Antimalware 會提供可設定的警示，在已知的惡意或垃圾軟體嘗試自行安裝或在您的 Azure 系統上執行時發出警示。

適用於 Azure 的 Microsoft Antimalware 是針對應用程式和租用戶環境所提供的單一代理程式解決方案。 其設計可於無人為介入的情況下在背景中執行。 您可依據應用程式工作負載需求，選擇預設的基本安全性或進階的自訂組態 (包括反惡意程式碼監視) 來部署保護。

深入瞭解適用[于 Azure 的 Microsoft Antimalware](antimalware.md)和可用的核心功能。

深入了解反惡意程式碼軟體以協助保護虛擬機器︰

* [在 Azure 虛擬機器上部署反惡意程式碼解決方案](https://azure.microsoft.com/blog/deploying-antimalware-solutions-on-azure-virtual-machines/)
* [如何在 Windows VM 上安裝和設定 Trend Micro Deep Security as a Service](/azure/virtual-machines/windows/classic/install-trend)
* [如何在 Windows VM 上安裝和設定 Symantec Endpoint Protection](/azure/virtual-machines/windows/classic/install-symantec)
* [Azure Marketplace 中的安全性解決方案](https://azure.microsoft.com/marketplace/?term=security)

如需更強大的保護，請考慮使用 [Windows Defender 進階威脅防護](https://docs.microsoft.com/windows/security/threat-protection/windows-defender-atp/windows-defender-advanced-threat-protection)。 透過使用 Windows Defender ATP，您將能取得：

* [攻擊面縮減](/windows/security/threat-protection/windows-defender-atp/overview-attack-surface-reduction)  
* [新一代的保護](/windows/security/threat-protection/windows-defender-antivirus/windows-defender-antivirus-in-windows-10)  
* [端點保護和回應](/windows/security/threat-protection/windows-defender-atp/overview-endpoint-detection-response)
* [自動化調查和補救](/windows/security/threat-protection/windows-defender-atp/automated-investigations-windows-defender-advanced-threat-protection)
* [安全分數](/windows/security/threat-protection/microsoft-defender-atp/configuration-score)
* [進階獵狩](/windows/security/threat-protection/windows-defender-atp/overview-hunting-windows-defender-advanced-threat-protection)
* [管理和 API](/windows/security/threat-protection/windows-defender-atp/management-apis)
* [Microsoft 威脅保護](/windows/security/threat-protection/windows-defender-atp/threat-protection-integration)

進一步了解：

* [開始使用 WDATP](/windows/security/threat-protection/microsoft-defender-atp/microsoft-defender-advanced-threat-protection)  
* [WDATP 功能的概觀](/windows/security/threat-protection/windows-defender-atp/overview)  

## <a name="hardware-security-module"></a>硬體安全模組

改善金鑰安全性可增強加密和驗證保護。 您可以藉由將關鍵密碼和金鑰存放在 Azure 金鑰保存庫中來簡化其管理與安全性。

金鑰保存庫讓您能選擇將金鑰存放在通過 FIPS 140-2 Level 2 標準認證的硬體安全性模組 (HSM) 中。 備份或 [透明資料加密](https://msdn.microsoft.com/library/bb934049.aspx) 的 SQL Server 加密金鑰都能與應用程式的任何金鑰或密碼一起存放在金鑰保存庫中。 這些受保護項目的權限和存取權是透過 [Azure Active Directory](https://azure.microsoft.com/documentation/services/active-directory/)來管理。

進一步了解：

* [什麼是 Azure 金鑰保存庫？](/azure/key-vault/key-vault-overview)
* [Azure 金鑰保存庫部落格](https://blogs.technet.microsoft.com/kv/)

## <a name="virtual-machine-disk-encryption"></a>虛擬機器磁碟加密

Azure 磁碟加密是用於加密 Windows 和 Linux 虛擬機器磁碟的新功能。 Azure 磁碟加密使用 Windows 的業界標準 [BitLocker](https://technet.microsoft.com/library/cc732774.aspx) 功能和 Linux 的 [dm-crypt](https://en.wikipedia.org/wiki/Dm-crypt) 功能，為 OS 和資料磁碟提供磁碟區加密。

此解決方案與 Azure Key Vault 整合，協助您控制及管理金鑰保存庫訂用帳戶中的磁碟加密金鑰與祕密。 它可確保虛擬機器磁碟上的所有資料都會在 Azure 儲存體中進行待用加密。

進一步了解：

* [IaaS VM 適用的 Azure 磁碟加密](/azure/security/azure-security-disk-encryption-overview)
* [快速入門：使用 Azure PowerShell 為 Windows IaaS VM 加密](../../virtual-machines/linux/disk-encryption-powershell-quickstart.md)

## <a name="virtual-machine-backup"></a>虛擬機器備份

Azure 備份是可調式解決方案，可以不需成本地協助保護您的應用程式資料，以及將操作成本降到最低。 應用程式錯誤可能導致資料損毀，而人為錯誤可能會將 Bug 導入應用程式中。 使用 Azure 備份，您執行 Windows 與 Linux 的虛擬機器會受到保護。

進一步了解：

* [何謂 Azure 備份？](/azure/backup/backup-introduction-to-azure-backup)
* [Azure 備份服務常見問題集](/azure/backup/backup-azure-backup-faq)

## <a name="azure-site-recovery"></a>Azure Site Recovery

組織的 BCDR 策略的其中一個重要部分是，找出在計劃中和非計劃中的中斷發生時讓企業工作負載和應用程式保持執行的方法。 Azure Site Recovery 有助於協調工作負載和應用程式的複寫、容錯移轉及復原，因此能夠在主要位置發生故障時透過次要位置來提供工作負載和應用程式。

網站復原：

* **簡化 BCDR 策略**：Site Recovery 可讓您從單一位置輕鬆處理多個商務工作負載和應用程式的複寫、容錯移轉及復原。 Site Recovery 會協調複寫和容錯移轉，但不會攔截應用程式資料或擁有任何相關資訊。
* **提供彈性的複寫**：使用 Site Recovery，您就可以複寫 Hyper-V 虛擬機器、VMware 虛擬機器和 Windows/Linux 實體伺服器上執行的工作負載。
* **支援容錯移轉和復原**：Site Recovery 可提供測試用容錯移轉，既能支援災害復原演練，又不會影響生產環境。 您也可以執行計劃性容錯移轉，因為是預期中的中斷，所以不會遺失任何資料；或是執行非計劃性容錯移轉，以在發生非未預期的災害時將資料損失減到最少 (取決於複寫頻率)。 在容錯移轉之後，您可以容錯回復到主要站台。 Site Recovery 提供了包含指令碼和 Azure 自動化作業手冊的復原計畫，以供您自訂多層式應用程式的容錯移轉和復原。
* **消除次要資料中心**：您可以複寫至次要內部部署站台或 Azure。 使用 Azure 做為災害復原目的地，可排除次要站台的維護成本和複雜度。 複寫的資料會儲存在 Azure 儲存體。
* **與現有 BCDR 技術整合**：Site Recovery 能夠與其他應用程式的 BCDR 功能搭配使用。 例如，您可以使用 Site Recovery 來協助保護公司工作負載的 SQL Server 後端。 這包括原生支援 SQL Server Always On 以管理可用性群組的容錯移轉。

進一步了解：

* [什麼是 Azure Site Recovery？](/azure/site-recovery/site-recovery-overview)
* [Azure Site Recovery 如何運作？](/azure/site-recovery/site-recovery-components)
* [Azure Site Recovery 保護哪些工作負載？](/azure/site-recovery/site-recovery-workload)

## <a name="virtual-networking"></a>虛擬網路

虛擬機器需要遠端連線。 為了支援該需求，Azure 需要虛擬機器連接到 Azure 虛擬網路。

Azure 虛擬網路是以實體 Azure 網路網狀架構為基礎所建置的邏輯建構。 每個邏輯 Azure 虛擬網路都會與其他所有 Azure 虛擬網路隔離。 此隔離可協助確保其他 Microsoft Azure 客戶無法存取您部署中的網路流量。

進一步了解：

* [Azure 網路安全性概觀](network-overview.md)
* [虛擬網路概觀](/azure/virtual-network/virtual-networks-overview)
* [網路功能與企業合作夥伴關係的案例](https://azure.microsoft.com/blog/networking-enterprise/)

## <a name="security-policy-management-and-reporting"></a>安全性原則管理和報告

Azure 資訊安全中心可協助您保護、偵測威脅並採取相應的措施。 資訊安全中心可讓您完整檢視並控制 Azure 資源的安全性。 它提供您 Azure 訂用帳戶之間的整合式安全性監視和原則管理。 它有助於偵測可能會被忽視的威脅，並可搭配廣泛的安全性解決方案生態系統使用。

資訊安全中心藉由下列方式來協助您最佳化和監視虛擬機器安全性︰

* 為虛擬機器提供[安全性建議](/azure/security-center/security-center-recommendations)。 建議範例包括：套用系統更新、設定 ACL 端點、啟用反惡意程式碼、啟用網路安全性群組，以及套用磁碟加密。
* 監視您的虛擬機器的狀態。

進一步了解：

* [Azure 資訊安全中心簡介](/azure/security-center/security-center-intro)
* [Azure 資訊安全中心常見問題集](/azure/security-center/security-center-faq)
* [Azure 資訊安全中心規劃和操作](/azure/security-center/security-center-planning-and-operations-guide)

## <a name="compliance"></a>法規遵循

Azure 虛擬機器經過 FISMA、FedRAMP、HIPAA、PCI DSS Level 1 及其他重要規範計劃認證。 此認證可讓您自己的 Azure 應用程式更容易符合規範要求，並讓您的企業解決廣泛的國內與國際法規要求。

進一步了解：

* [Microsoft 信任中心：法規遵循](https://www.microsoft.com/en-us/trustcenter/compliance)
* [受信任的雲端：Microsoft Azure 安全性、隱私權及法規遵循](https://download.microsoft.com/download/1/6/0/160216AA-8445-480B-B60F-5C8EC8067FCA/WindowsAzure-SecurityPrivacyCompliance.pdf)

## <a name="confidential-computing"></a>機密運算

雖然就技術上而言，機密運算並不是虛擬機器安全性的一部分，虛擬機器安全性的主題仍位於「運算」安全性這個更高層級的主題之內。 機密運算位於「運算」安全性這個類別之內。

機密運算能確保資料「暴露在外」(這對於有效進行處理來說是必要的) 時，該資料會被保護在受信任的執行環境 https://en.wikipedia.org/wiki/Trusted_execution_environment (TEE，也稱為保護區) 之內，其範例如下圖所示。  

TEE 能確保沒有任何方法可以從外部檢視資料或內部作業，就算是使用偵錯工具也一樣。 它們甚至能確保只有獲授權的程式碼可以存取該資料。 如果程式碼被修改或竄改，系統就會拒絕作業並停用環境。 TEE 能在位於其中的程式碼執行期間，強制執行這些保護。

進一步了解：

* [Azure 機密運算簡介](https://azure.microsoft.com/blog/introducing-azure-confidential-computing/) \(英文\)  
* [Azure 機密運算](https://azure.microsoft.com/blog/azure-confidential-computing/) \(英文\)  

## <a name="next-steps"></a>後續步驟

瞭解 Vm 和作業系統的[安全性最佳作法](iaas.md)。
