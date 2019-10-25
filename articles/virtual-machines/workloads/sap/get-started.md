---
title: 開始使用 Azure 上的 SAP Vm |Microsoft Docs
description: 瞭解在 Microsoft Azure 的虛擬機器（Vm）上執行的 SAP 解決方案
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: ad8e5c75-0cf6-4564-ae62-ea1246b4e5f2
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 10/21/2019
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 9b857ea5380fb13f8c8b1ff30b9dd9d8b6c723c2
ms.sourcegitcommit: ec2b75b1fc667c4e893686dbd8e119e7c757333a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/23/2019
ms.locfileid: "72809688"
---
# <a name="use-azure-to-host-and-run-sap-workload-scenarios"></a>使用 Azure 來裝載和執行 SAP 工作負載案例

當您使用 Microsoft Azure 時，您可以在可調整、符合規範且經企業證明的平臺上，可靠地執行您的任務關鍵性 SAP 工作負載和案例。 您可以獲得 Azure 的擴充性、彈性和成本節約。 透過 Microsoft 與 SAP 的擴充合作關係，您可以在 Azure 中的開發和測試和生產案例中執行 SAP 應用程式，並受到完整支援。 從 SAP NetWeaver 到 SAP S/4HANA、從 Linux 到 Windows 的 SAP BI，以及 SAP Hana 到 SQL，我們都已涵蓋在內。

除了在 Azure 上裝載具有不同 DBMS 的 SAP NetWeaver 案例，您可以裝載其他 SAP 工作負載案例，例如 Azure 上的 SAP BI。 

適用于 SAP Hana 的 Azure 唯一性是設定 Azure 的供應專案。 為了能夠裝載包含 SAP Hana 的記憶體和 CPU 資源需求較高的 SAP 案例，Azure 提供了客戶專用的裸機硬體使用。 使用此解決方案可針對 S/4HANA 或其他 SAP Hana 的工作負載，執行需要最多 24 TB （120 TB 向外延展）記憶體的 SAP Hana 部署。 

在 Azure 中裝載 SAP 工作負載案例也可以建立身分識別整合和單一登入的需求。 當您使用 Azure Active Directory （Azure AD）連接不同的 SAP 元件和 SAP 軟體即服務（SaaS）或平臺即服務（PaaS）供應專案時，就會發生這種情況。 「AAD SAP 身分識別整合和單一登入」一節會描述並記載一份這類整合和單一登入案例的清單，其中包含 Azure AD 和 SAP 實體。

## <a name="changes-to-the-sap-workload-section"></a>SAP 工作負載區段的變更
Azure 上的 SAP 工作負載區段中的檔變更會列在本文結尾。


## <a name="sap-hana-on-azure-large-instances"></a>Azure 上的 SAP HANA (大型執行個體)

一系列的檔會引導您完成 Azure （大型實例）或簡短的 HANA 大型實例的 SAP Hana。 如需有關「HANA 大型實例」的下欄區域的資訊，請參閱：

- [SAP HANA on Azure (大型執行個體) 的概觀](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture)
- [SAP HANA on Azure (大型執行個體) 的架構](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-architecture)
- [Azure （大型實例）上 SAP Hana 的基礎結構和連線能力](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-infrastructure-connectivity)
- [在 Azure （大型實例）上安裝 SAP Hana](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-installation)
- [Azure （大型實例）上 SAP Hana 的高可用性和嚴重損壞修復](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-high-availability-disaster-recovery)
- [針對 Azure （大型實例）上的 SAP Hana 進行疑難排解和監視](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/troubleshooting-monitoring)

接下來的步驟：

- 瞭解[Azure （大型實例）上 SAP Hana 的總覽和架構](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture)



## <a name="sap-hana-on-azure-virtual-machines"></a>Azure 虛擬機器上的 SAP Hana
文件的本節涵蓋 SAP HANA 的不同層面。 必要條件是，您應該熟悉提供 Azure IaaS 基本服務的 Azure 主要服務。 因此，您需要 Azure 計算、儲存體和網路的知識。 這些主題中有許多都是在 SAP NetWeaver 相關的[Azure 規劃指南](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide)中處理。 

如需有關 Azure 上 HANA 的詳細資訊，請參閱下列文章及其 subarticles：

- [快速入門：在 Azure VM 上手動安裝單一執行個體 SAP HANA](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-get-started)
- [在 Azure 上部署 SAP S/4HANA 或 BW/4HANA](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/cal-s4h)
- [SAP HANA 在 Azure 上的基礎結構組態和作業](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations)
- [Azure 虛擬機器的 SAP Hana 高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-availability-overview)
- [單一 Azure 區域中的 SAP HANA 可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-availability-one-region)
- [跨 Azure 區域的 SAP HANA 可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-availability-across-regions)
- [Azure 虛擬機器上 SAP HANA 的高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-high-availability)
- [Azure 虛擬機器上的 SAP Hana 備份指南](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-guide)
- [檔案層級的 SAP HANA Azure 備份](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-file-level)
- [以儲存體快照集為基礎的 SAP HANA 備份](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-storage-snapshots)

 

## <a name="sap-netweaver-deployed-on-azure-virtual-machines"></a>部署在 Azure 虛擬機器上的 SAP NetWeaver
本節列出 Azure 上 SAP NetWeaver 和 Business One 的規劃和部署檔。 本檔著重于在 Azure 上使用非 HANA 資料庫與 SAP 工作負載的基本概念。 適用于高可用性的檔和文章也是 Azure 中 HANA 高可用性的基礎，例如：

- [Azure 虛擬機器上的 SAP Business One](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/business-one-azure)
- [在 Azure 上部署適用於 SAP ERP 6.0 的 SAP IDES EHP7 SP3](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/cal-ides-erp6-erp7-sp3-sql)
- [在 Microsoft Azure SUSE Linux Vm 上執行 SAP NetWeaver](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/suse-quickstart)
- [SAP NetWeaver 的 Azure 虛擬機器規劃和實作指南](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide)
- [適用於 SAP NetWeaver 的 Azure 虛擬機器部署](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/deployment-guide)
- [使用 Site Recovery 保護多層式 SAP NetWeaver 應用程式部署](https://docs.microsoft.com/azure/site-recovery/site-recovery-sap)
- [適用於 Azure 的 SAP LaMa 連接器](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/lama-installation)

如需 Azure 上 SAP 工作負載下非 HANA 資料庫的詳細資訊，請參閱：

- [適用於 SAP 工作負載的 Azure 虛擬機器 DBMS 部署考量](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_general)
- [適用於 SAP NetWeaver 的 SQL Server Azure 虛擬機器 DBMS 部署](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_sqlserver)
- [適用於 SAP 工作負載的 Oracle Azure 虛擬機器 DBMS 部署](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_oracle)
- [適用於 SAP 工作負載的 IBM DB2 Azure 虛擬機器 DBMS 部署](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_ibm)
- [適用於 SAP 工作負載的 SAP ASE Azure 虛擬機器 DBMS 部署](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_sapase)
- [Azure Vm 上的 SAP MaxDB、Live Cache 和 Content Server 部署](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_maxdb)

如需有關 Azure 上 SAP Hana 資料庫的詳細資訊，請參閱 Azure 虛擬機器上的「SAP Hana」一節。

如需有關 Azure 上 SAP 工作負載的高可用性資訊，請參閱：

- [SAP NetWeaver 的 Azure 虛擬機器高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-guide-start)

本檔指向各種不同的架構和案例檔。 在稍後的案例檔中，會提供詳細技術檔的連結，說明如何部署和設定不同的高可用性方法。 說明如何為 SAP NetWeaver 工作負載建立和設定高可用性的不同檔涵蓋 Linux 和 Windows 作業系統。


如需 Azure Active Directory （Azure AD）與 SAP 服務與單一登入之間整合的相關資訊，請參閱：

- [教學課程：Azure Active Directory 與 SAP Cloud for Customer 整合](https://docs.microsoft.com/azure/active-directory/saas-apps/sap-customer-cloud-tutorial?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [教學課程：Azure Active Directory 與 SAP Cloud Platform Identity Authentication 整合](https://docs.microsoft.com/azure/active-directory/saas-apps/sap-hana-cloud-platform-identity-authentication-tutorial?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [教學課程：Azure Active Directory 與 SAP 雲端平台整合](https://docs.microsoft.com/azure/active-directory/saas-apps/sap-hana-cloud-platform-tutorial?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [教學課程：Azure Active Directory 與 SAP NetWeaver 整合](https://docs.microsoft.com/azure/active-directory/saas-apps/sap-netweaver-tutorial?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [教學課程：Azure Active Directory 與 SAP Business ByDesign 整合](https://docs.microsoft.com/azure/active-directory/saas-apps/sapbusinessbydesign-tutorial?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [教學課程：Azure Active Directory 與 SAP HANA 整合](https://docs.microsoft.com/azure/active-directory/saas-apps/saphana-tutorial?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [您的 S/4HANA 環境：使用 Azure AD 的 Fiori 啟動列 SAML 單一登入](https://blogs.sap.com/2017/02/20/your-s4hana-environment-part-7-fiori-launchpad-saml-single-sing-on-with-azure-ad/)

如需將 Azure 服務整合到 SAP 元件的相關資訊，請參閱：

- [在 Power BI Desktop 中使用 SAP HANA](https://docs.microsoft.com/power-bi/desktop-sap-hana)
- [DirectQuery 和 SAP HANA](https://docs.microsoft.com/power-bi/desktop-directquery-sap-hana)
- [在 Power BI Desktop 中使用 SAP BW 連接器](https://docs.microsoft.com/power-bi/desktop-sap-bw-connector) 
- [Azure Data Factory 提供 SAP HANA 和 Business Warehouse 資料整合](https://azure.microsoft.com/blog/azure-data-factory-offer-sap-hana-and-business-warehouse-data-integration)


## <a name="change-log"></a>變更記錄檔
- 10/22/2019：在適用于 sap 應用程式的[SUSE Linux Enterprise Server 上的 Azure vm](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse)上，sap NetWeaver 的高可用性變更、azure vm 上的 sap [NetWeaver 高可用性（含適用于 sap 應用程式的 azure NetApp Files） SUSE Linux Enterprise Server](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-netapp-files)、 [SUSE Linux Enterprise Server 上的 azure vm 上的 NFS 高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-nfs)、在[azure 的 SUSE Linux Enterprise Server 上設定 Pacemaker](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker)、在[azure VM SUSE Linux Enterprise Server 上使用 Pacemaker 的 IBM Db2 LUW 的高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms-guide-ha-ibm)、[SUSE Linux Enterprise Server 上的 Azure vm 上 SAP Hana 的高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-high-availability)，適用于 Azure 負載平衡器偵測強化
- 變更[SAP Hana Azure 虛擬機器儲存體](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations-storage)設定中的及區段和標頭區段
- 10/21/2019：使用 SLES 上的[Azure NetApp Files 在 Azure vm 上以待命節點發行 SAP Hana 相應](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-scale-out-standby-netapp-files-suse)放大
- 10/16/2019：修正[備份和還原](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-backup-restore)中中斷的連結
- 10/16/2019：在 SUSE Linux Enterprise Server 上使用 Pacemaker，將 SLES 12 SP3 的最低建議 OS 變更為[Azure vm 上的](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms-guide-ha-ibm)SLES 12 SP4
- 10/11/2019： [SAP Hana Azure 虛擬機器儲存體](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations-storage)設定中，對 Ultra 磁片儲存體設定和引入及的變更
- 10/01/2019：變更[Azure 鄰近放置群組的圖形以取得最佳的網路延遲，讓 SAP 應用程式](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-proximity-placement-scenarios)更清楚
- 10/01/2019：變更[Azure 上 SAP Hana 的基礎結構設定和作業](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations)，以更正適用于/hana/shared 的高可用性 NFS 共用的語句 
- 09/28/2019：在 RHEL 叢集上不支援在[Azure 中的 Red Hat Enterprise Linux 上設定 Pacemaker](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-pacemaker) ，以將 SBD 澄清為隔離機制  
- 09/17/2019： NetWeaver 規劃與部署指南中的變更，以整合適用于 SAP 的 VM 延伸模組術語  
- 08/22/2019：在[Azure 中的 SUSE Linux Enterprise Server 上設定 Pacemaker](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker)的變更，以更新自訂角色建立的 url  
- 08/16/2019：在[azure Red Hat Enterprise Linux 上設定 Pacemaker](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-pacemaker)的變更，以在更新至新版本的 azure 隔離代理程式時，提醒客戶更新自訂角色中的動作  
- 08/15/2019： [SAP Hana Azure 虛擬機器儲存體](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations-storage)設定的變更，以反映 ultra （舊稱 ultra SSD）的正式運作
- 08/01/2019：[在 Azure 中的 SUSE Linux Enterprise Server 上設定 Pacemaker 的](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker)變更，以整合特別針對 SLES 15 的變更 
- 07/23/2019：在[Azure 中使用檔案共用在 Windows 容錯移轉叢集上對 SAP ASCS/SCS 實例](sap-high-availability-guide-wsfc-file-share.md)進行叢集變更，以反映 Azure Site Recovery 服務的儲存空間直接存取支援
- 07/14/2019：[使用 SAP 應用程式的 Azure 鄰近放置群組版本，以獲得最佳的網路延遲](sap-proximity-placement-scenarios.md)
- 07/11/2019：涵蓋 HANA 大型實例之各種檔中的變更涵蓋 HANA 大型實例的修訂4
- 07/09/2019： [Red Hat Enterprise Server 中的 IBM DB2 HADR](high-availability-guide-rhel-ibm-db2-luw.md)新指南版本
- 06/13/2019：以[適用于 sap 應用程式的 Azure NetApp Files Red Hat Enterprise Linux 的 Sap NetWeaver 的高可用性](high-availability-guide-rhel-netapp-files.md)發行


