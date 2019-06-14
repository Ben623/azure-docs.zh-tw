---
title: 使用 Azure Site Recovery 進行 VMware 至 Azure 災害復原的組態伺服器需求 | Microsoft Docs
description: 本文說明部署組態伺服器以便使用 Azure Site Recovery 進行 VMware 至 Azure 的災害復原時的支援和需求
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
services: site-recovery
ms.topic: article
ms.date: 05/30/2019
ms.author: raynew
ms.openlocfilehash: 94f410b7bd3b7c2eb3d7d6a9316323092010338e
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "66418330"
---
# <a name="configuration-server-requirements-for-vmware-disaster-recovery-to-azure"></a>VMware 至 Azure 災害復原的組態伺服器需求

當您使用 [Azure Site Recovery](site-recovery-overview.md) 將 VMware VM 和實體伺服器災害復原到 Azure 時，便會部署內部部署設定伺服器。

- 設定伺服器會協調內部部署 VMware 與 Azure 之間的通訊。 它也會管理資料複寫。
- [深入了解](vmware-azure-architecture.md)組態伺服器元件和程序。

## <a name="configuration-server-deployment"></a>組態伺服器部署

針對 VMware VM 至 Azure 的災害復原，您可以部署組態伺服器作為 VMware VM。

- Site Recovery 會提供您從 Azure 入口網站下載的 OVA 範本，並將該範本匯入至 vCenter Server 來設定組態伺服器 VM。
- 當您使用.OVA 範本部署組態伺服器時，VM 會自動遵從本文所列的需求。
- 我們強烈建議您使用.OVA 範本設定組態伺服器。 不過，如果您要設定 VMware VM 災害復原且無法使用 OVA 範本，您可以使用[所提供的這些指示](physical-azure-set-up-source.md)來部署組態伺服器。
- 如果您要部署組態伺服器，以便進行內部部署實體機器至 Azure 的災害復原，請遵循[這篇文章](physical-azure-set-up-source.md)中的指示。 


## <a name="hardware-requirements"></a>硬體需求

**元件** | **需求** 
--- | ---
CPU 核心 | 8 
RAM | 16 GB
磁碟數量 | 3，包括作業系統磁碟、處理序伺服器快取磁碟和用於容錯回復的保留磁碟機 
可用磁碟空間 (處理序伺服器快取) | 600 GB
可用磁碟空間 (保留磁碟) | 600 GB

## <a name="software-requirements"></a>軟體需求

**元件** | **需求** 
--- | ---
作業系統 | Windows Server 2012 R2 <br> Windows Server 2016
作業系統地區設定 | 英文 (en-us)
Windows Server 角色 | 請勿啟用這些角色： <br> - Active Directory Domain Services <br>- 網際網路資訊服務 <br> - Hyper-V 
群組原則 | 請勿啟用這些群組原則： <br> - 防止存取命令提示字元。 <br> - 防止存取登錄編輯工具。 <br> - 檔案附件的信任邏輯。 <br> - 開啟指令碼執行。 <br> [深入了解](https://technet.microsoft.com/library/gg176671(v=ws.10).aspx)
IIS | - 沒有預先存在的預設網站 <br> - 沒有預先存在的網站/應用程式接聽連接埠 443 <br>- 啟用[匿名驗證](https://technet.microsoft.com/library/cc731244(v=ws.10).aspx) <br> - 啟用 [FastCGI](https://technet.microsoft.com/library/cc753077(v=ws.10).aspx) 設定 

## <a name="network-requirements"></a>網路需求

**元件** | **需求** 
--- | --- 
IP 位址類型 | Static 
網際網路存取 | 伺服器需要存取這些 URL (直接或透過 Proxy)： <br> - \*.accesscontrol.windows.net<br> - \*.backup.windowsazure.com <br>- \*.store.core.windows.net<br> - \*.blob.core.windows.net<br> - \*.hypervrecoverymanager.windowsazure.com  <br> - https:\//management.azure.com <br> - *.services.visualstudio.com <br> - time.nist.gov <br> - time.windows.com <br> OVF 也需要存取下列 URL： <br> - https:\//login.microsoftonline.com <br> - https:\//secure.aadcdn.microsoftonline-p.com <br> - https:\//login.live.com  <br> - https:\//auth.gfx.ms <br> - https:\//graph.windows.net <br> - https:\//login.windows.net <br> - https:\//www.live.com <br> - https:\//www.microsoft.com <br> - https:\//dev.mysql.com/get/Downloads/MySQLInstaller/mysql-installer-community-5.7.20.0.msi 
連接埠 | 443 (控制通道協調流程)<br>9443 (資料傳輸) 
NIC 類型 | VMXNET3 (如果組態伺服器是 VMware 虛擬機器)

## <a name="required-software"></a>必要的軟體

**元件** | **需求** 
--- | ---
VMware vSphere PowerCLI | 如果組態伺服器在 VMware 虛擬機器上執行，則應該安裝 [PowerCLI 6.0 版](https://my.vmware.com/web/vmware/details?productId=491&downloadGroup=PCLI600R1)。
MySQL | 應該安裝 MySQL。 您可以手動安裝，或者 Site Recovery 可以安裝。

## <a name="sizing-and-capacity-requirements"></a>調整大小和容量需求

下表彙總組態伺服器的容量需求。 如果您要複寫多部 VMware VM，您應檢閱[容量規劃考量](site-recovery-plan-capacity-vmware.md)，並執行 [Azure Site Recovery 部署規劃工具](site-recovery-deployment-planner.md)以進行 VMWare 複寫。 

**元件** | **需求** 
--- | ---

| **CPU** | **記憶體** | **快取磁碟** | **資料變更率** | **複寫的機器** |
| --- | --- | --- | --- | --- |
| 8 個 vCPU<br/><br/> 2 個插槽 * 4 個核心 \@ 2.5 GHz | 16 GB | 300 GB | 500 GB 或更少 | 少於 100 部電腦 |
| 12 個 vCPU<br/><br/> 2 個插槽 * 6 個核心 \@ 2.5 GHz | 18 GB | 600 GB | 500 GB-1 TB | 100 到 150 部機器 |
| 16 個 vCPU<br/><br/> 2 個插槽 * 8 個核心 \@ 2.5 GHz | 32 GB | 1 TB | 1-2 TB | 150-200 部電腦 | 



## <a name="next-steps"></a>後續步驟
為 [VMware VM](vmware-azure-tutorial.md) 設定以 Azure 作為目標的災害復原。
