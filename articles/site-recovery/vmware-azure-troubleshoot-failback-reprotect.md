---
title: 對使用 Azure Site Recovery 從 VMWare VM 災害復原至 Azure 期間所進行的內部部署環境容錯回復進行疑難排解 | Microsoft Docs
description: 本文說明對使用 Azure Site Recovery 從 VMWare VM 災害復原至 Azure 期間所發生的容錯回復和重新保護問題進行疑難排解的方式。
author: rajani-janaki-ram
manager: gauravd
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/27/2018
ms.author: rajanaki
ms.openlocfilehash: 20cb7a446befb1d31f0e069d91d0230fc4a2a901
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "60565594"
---
# <a name="troubleshoot-failback-to-on-premises-from-azure"></a>針對從 Azure 至內部部署環境的容錯回復進行疑難排解

本文說明如何排解您在使用 [Azure Site Recovery](site-recovery-overview.md) 執行容錯移轉後從 Azure VM 容錯回復至內部部署 VMware 基礎結構時可能遇到的問題。

容錯回復基本上涉及兩個主要步驟。 第一個步驟是容錯移轉之後，您必須重新保護移轉至內部部署的 Azure VM，使其能夠開始進行複寫。 第二個步驟是從 Azure 執行容錯移轉，以容錯回復到您的內部部署網站。

## <a name="troubleshoot-reprotection-errors"></a>對重新保護錯誤進行疑難排解

本節詳述常見的重新保護錯誤及其更正方式。

### <a name="error-code-95226"></a>錯誤碼 95226

重新保護失敗，因為 Azure 虛擬機器無法觸達內部部署組態伺服器。 

此錯誤的發生條件如下：

* Azure VM 無法連線到內部部署組態伺服器。 無法探索到 VM 並將其註冊至組態伺服器。
* 在容錯移轉之後，InMage Scout 應用程式服務未執行於 Azure VM 上。 必須有此服務，才能與內部部署組態伺服器進行通訊。

若要解決此問題︰

* 確認 Azure VM 網路允許 Azure VM 與內部部署組態設定伺服器進行通訊。 您可以將站對站 VPN 設為您的內部部署資料中心，或是使用 Azure VM 之虛擬網路上的私人對等互連來設定 Azure ExpressRoute 連線。
* 如果 VM 可以使用內部部署組態伺服器通訊，則請登入 VM。 然後檢查 InMage Scout 應用程式服務。 如果您看到其未執行，請手動啟動服務。 檢查服務啟動類型是否設定為 [自動]  。

### <a name="error-code-78052"></a>錯誤碼 78052

**無法為虛擬機器完成保護。**

在您要容錯回復的主要目標伺服器上已有同名的 VM 存在，就可能發生此問題。

若要解決此問題︰

* 在不同的主機上選取不同的主要目標伺服器，使重新保護可在不同的主機上建立機器，而其中的名稱不會衝突。
* 您也可以使用 vMotion 將主要目標移到不同的主機，而其中的名稱不會發生衝突。 如果現有的 VM 是偏離機器，您可以將其重新命名，以在相同的 ESXi 主機上建立新的 VM。


### <a name="error-code-78093"></a>錯誤碼 78093

**VM 未執行，處於停止回應狀態或無法存取。**

若要解決此問題︰

若要重新保護已容錯移轉的 VM，Azure VM 必須執行，如此一來，行動服務就會向組態伺服器內部部署進行註冊，並可與流程伺服器通訊，開始進行複寫。 如果電腦位於不正確的網路，或未執行 （未回應或關閉），組態伺服器無法聯繫以開始重新保護 VM 上的行動服務。

* 請重新啟動 VM，讓它能夠開始通訊回內部部署。
* 啟動 Azure 虛擬機器之後，重新啟動重新保護作業。

### <a name="error-code-8061"></a>錯誤碼 8061

**無法從 ESXi 主機存取資料存放區。**

查看容錯回復的[主要目標必要條件和支援的資料存放區](vmware-azure-reprotect.md#deploy-a-separate-master-target-server)。


## <a name="troubleshoot-failback-errors"></a>對容錯回復錯誤進行疑難排解

本節說明您在容錯回復期間可能會遇到的常見錯誤。

### <a name="error-code-8038"></a>錯誤碼 8038

**由於發生錯誤而無法啟動內部部署的虛擬機器。**

此問題可能會在未佈建足夠記憶體的主機上啟動內部部署 VM 時發生。 

若要解決此問題︰

* 在 ESXi 主機上佈建更多記憶體。
* 此外，您可以使用 vMotion 將虛擬機器移到另一部有足夠記憶體可啟動 VM 的 ESXi 主機。
