---
title: Azure Migrate appliance architecture
description: Provides an overview of the Azure Migrate appliance used in server assessment and migration.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 11/19/2019
ms.author: raynew
ms.openlocfilehash: 49545ca6c43c272c3fd84f8bee59b8617aae136d
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/20/2019
ms.locfileid: "74232560"
---
# <a name="azure-migrate-appliance"></a>Azure Migrate 設備

This article describes the Azure Migrate appliance. You deploy the appliance when you use Azure Migrate Assessment and Migration tools to discover, assess and migrate apps, infrastructure, and workloads to Microsoft Azure. 

[Azure Migrate](migrate-services-overview.md) provides a central hub to track discovery, assessment and migration of your on-premises apps and workloads, and private/public cloud VMs, to Azure. 該中樞能提供 Azure Migrate 工具以進行評估和移轉，也提供協力廠商獨立軟體廠商 (ISV) 的供應項目。



## <a name="appliance-overview"></a>Appliance overview

The Azure Migrate appliance types and usage are as follows.

**Deployed as** | **用於** | **詳細資料**
--- | --- |  ---
VMware VM | VMware VM assessment with the Azure Migrate Assessment tool.<br/><br/> VMware VM agentless migration with the Azure Migrate Server Migration tool | Download OVA template and import to vCenter Server to create the appliance VM.
Hyper-V VM | Hyper-V VM assessment with the Azure Migrate Assessment tool. | Download zipped VHD and import to Hyper-V to create the appliance VM.

## <a name="appliance-access"></a>Appliance access

After you have configured the appliance, you can remotely access the appliance VM through TCP port 3389. You can also remotely access the web management app for the appliance, on port 44368 with URL: `https://<appliance-ip-or-name>:44368`.

## <a name="appliance-license"></a>Appliance license
The appliance comes with a Windows Server 2016 evaluation license, which is valid for 180 days. If the evaluation period is close to expiry, we recommend that you download and deploy a new appliance, or that you activate the operating system license of the appliance VM.

## <a name="appliance-agents"></a>Appliance agents
The appliance has these agents installed.

**代理程式** | **詳細資料**
--- | ---
Discovery agent | Gathers configuration data of on-premises virtual machines
評量代理程式 | Profiles the on-premises environment to collect VM performance data.
Migration adapter | Orchestrates VM replication, and coordinates communication between VMs and Azure.
Migration gateway | Sends replicated VM data to Azure.


## <a name="appliance-deployment-requirements"></a>Appliance deployment requirements

- [Review](migrate-support-matrix-vmware.md#assessment-appliance-requirements) the deployment requirements for a VMware appliance, and the URLs that the appliance needs to access.
- [Review](migrate-support-matrix-hyper-v.md#assessment-appliance-requirements) the deployment requirements for a Hyper-V appliance, and the URLs that the appliance  needs to access.


## <a name="collected-performance-data-vmware"></a>Collected performance data-VMware

Here's the VMware VM performance data that the appliance collects and sends to Azure.

**資料** | **計數器** | **Assessment impact**
--- | --- | ---
CPU 使用率 | cpu.usage.average | Recommended VM size/cost
記憶體使用量 | mem.usage.average | Recommended VM size/cost
Disk read throughput (MB per second) | virtualDisk.read.average | Calculation for disk size, storage cost, VM size
Disk write throughput (MB per second) | virtualDisk.write.average | Calculation for disk size, storage cost, VM size
Disk read operations per second | virtualDisk.numberReadAveraged.average | Calculation for disk size, storage cost, VM size
Disk write operations per second | virtualDisk.numberReadAveraged.average  | Calculation for disk size, storage cost, VM size
NIC read throughput (MB per second) | net.received.average | Calculation for VM size
NIC write throughput (MB per second) | net.transmitted.average  |Calculation for VM size


## <a name="collected-metadata-vmware"></a>Collected metadata-VMware

> [!NOTE]
> Metadata discovered by the Azure Migrate appliance is used to help you right-size your applications as you migrate them to Azure, perform Azure suitability analysis, application dependency analysis, and cost planning. Microsoft does not use this data in relation to any license compliance audit.

Here's the full list of VMware VM metadata that the appliance collects and sends to Azure.

**資料** | **計數器**
--- | --- 
**Machine details** | 
VM 識別碼 | vm.Config.InstanceUuid 
VM 名稱 | vm.Config.Name
vCenter Server 識別碼 | VMwareClient.Instance.Uuid
VM 描述 | vm.Summary.Config.Annotation
授權產品名稱 | vm.Client.ServiceContent.About.LicenseProductName
作業系統類型 | vm.SummaryConfig.GuestFullName
開機類型 | vm.Config.Firmware
核心數 | vm.Config.Hardware.NumCPU
Memory (MB) | vm.Config.Hardware.MemoryMB
磁碟數目 | vm.Config.Hardware.Device.ToList().FindAll(x => is VirtualDisk).count
磁碟大小清單 | vm.Config.Hardware.Device.ToList().FindAll(x => is VirtualDisk)
網路介面卡清單 | vm.Config.Hardware.Device.ToList().FindAll(x => is VirtualEthernet).count
CPU 使用率 | cpu.usage.average
記憶體使用量 |mem.usage.average
**Per disk details** | 
磁碟機碼值 | disk.Key
Dikunit number | disk.UnitNumber
磁碟控制器機碼值 | disk.ControllerKey.Value
已佈建的 GB 數 | virtualDisk.DeviceInfo.Summary
Disk name | Value generated using disk.UnitNumber, disk.Key, disk.ControllerKey.VAlue
每秒的讀取作業數 | virtualDisk.numberReadAveraged.average
每秒的寫入作業數 | virtualDisk.numberReadAveraged.average
Read throughput (MB per second) | virtualDisk.read.average
Write throughput (MB per second) | virtualDisk.write.average
**Per NIC details** | 
網路介面卡名稱 | nic.Key
MAC 位址 | ((VirtualEthernetCard)nic).MacAddress
IPv4 addresses | vm.Guest.Net
IPv6 位址 | vm.Guest.Net
Read throughput (MB per second) | net.received.average
Write throughput (MB per second) | net.transmitted.average
**Inventory path details** | 
Name | container.GetType().Name
子物件的類型 | container.ChildType
參考詳細資料 | container.MoRef
父系詳細資料 | Container.Parent
Folder details per VM | ((Folder)container).ChildEntity.Type
Datacenter details per VM | ((Datacenter)container).VmFolder
Datacenter details per host folder | ((Datacenter)container).HostFolder
Cluster details per host | ((ClusterComputeResource)container).Host
Host details per VM | ((HostSystem)container).VM



## <a name="collected-performance-data-hyper-v"></a>Collected performance data-Hyper-V

> [!NOTE]
> Metadata discovered by the Azure Migrate appliance is used to help you right-size your applications as you migrate them to Azure, perform Azure suitability analysis, application dependency analysis, and cost planning. Microsoft does not use this data in relation to any license compliance audit.

Here's the Hyper VM performance data that the appliance collects and sends to Azure.

**Performance counter class** | **計數器** | **Assessment impact**
--- | --- | ---
Hyper-V Hypervisor Virtual Processor | % Guest Run Time | Recommended VM size/cost
Hyper-V Dynamic Memory VM | Current Pressure (%)<br/> Guest Visible Physical Memory (MB) | Recommended VM size/cost
Hyper-V Virtual Storage Device | Read Bytes/Second | Calculation for disk size, storage cost, VM size
Hyper-V Virtual Storage Device | Write Bytes/Second | Calculation for disk size, storage cost, VM size
Hyper-V Virtual Network Adapter | Bytes Received/Second | Calculation for VM size
Hyper-V Virtual Network Adapter | Bytes Sent/Second | Calculation for VM size

- CPU utilization is the sum of all usage, for all virtual processors attached to a VM.
- Memory utilization is (Current Pressure * Guest Visible Physical Memory) / 100.
- Disk and network utilization values are collected from the listed Hyper-V performance counters.

## <a name="collected-metadata-hyper-v"></a>Collected metadata-Hyper-V

Here's the full list of Hyper-V VM metadata that the appliance collects and sends to Azure.

**資料** | **WMI class** | **WMI class property**
--- | --- | ---
**Machine details** | 
Serial number of BIOS _ Msvm_BIOSElement | BIOSSerialNumber
VM type (Gen 1 or 2) | Msvm_VirtualSystemSettingData | VirtualSystemSubType
VM display name | Msvm_VirtualSystemSettingData | ElementName
VM version | Msvm_ProcessorSettingData | VirtualQuantity
Memory (bytes) | Msvm_MemorySettingData | VirtualQuantity
Maximum memory that can be consumed by VM | Msvm_MemorySettingData | 限制
Dynamic memory enabled | Msvm_MemorySettingData | DynamicMemoryEnabled
Operating system name/version/FQDN | Msvm_KvpExchangeComponent | GuestIntrinsicExchangeItems Name Data
VM power status | Msvm_ComputerSystem | EnabledState
**Per disk details** | 
Disk identifier | Msvm_VirtualHardDiskSettingData | VirtualDiskId
Virtual hard disk type | Msvm_VirtualHardDiskSettingData | Type
Virtual hard disk size | Msvm_VirtualHardDiskSettingData | MaxInternalSize
Virtual hard disk parent | Msvm_VirtualHardDiskSettingData | ParentPath
**Per NIC details** | 
IP addresses (synthetic NICs) | Msvm_GuestNetworkAdapterConfiguration | IPAddresses
DHCP enabled (synthetic NICs) | Msvm_GuestNetworkAdapterConfiguration | DHCPEnabled
NIC ID (synthetic NICs) | Msvm_SyntheticEthernetPortSettingData | InstanceID
NIC MAC address (synthetic NICs) | Msvm_SyntheticEthernetPortSettingData | 位址
NIC ID (legacy NICs) | MsvmEmulatedEthernetPortSetting Data | InstanceID
NIC MAC ID (legacy NICs) | MsvmEmulatedEthernetPortSetting Data | 位址




## <a name="discovery-and-collection-process"></a>Discovery and collection process

The appliance communicates with vCenter Servers and Hyper-V hosts/cluster using the following process.


1. **Start discovery**:
    - When you start the discovery on the Hyper-V appliance, it communicates with the Hyper-V hosts on WinRM ports 5985 (HTTP) and 5986 (HTTPS).
    - When you start discovery on the VMware appliance, it communicates with the vCenter server on TCP port 443 by default. IF the vCenter server listens on a different port, you can configure it in the appliance web app.
2. **Gather metadata and performance data**:
    - The appliance uses a Common Information Model (CIM) session to gather Hyper-V VM data from the Hyper-V host on ports 5985 and 5986.
    - The appliance communicates with port 443 by default, to gather VMware VM data from the vCenter Server.
3. **Send data**: The appliance sends the collected data to Azure Migrate Server Assessment and Azure Migrate Server Migration over SSL port 443.
    - For performance data, the appliance collects real-time utilization data.
        - Performance data is collected every 20 seconds for VMware, and every 30 seconds for Hyper-V, for each performance metric.
        - The collected data is rolled up to create a single data point for ten minutes.
        - The peak utilization value is selected from all of the 20/30 second data points, and sent to Azure for assessment calculation.
        - Based on the percentile value specified in the assessment properties (50th/90th/95th/99th), the ten-minute points are sorted in ascending order, and the appropriate percentile value is used to compute the assessment
    - For Server Migration, the appliance starts collecting VM data, and replicates it to Azure.
4. **Assess and migrate**: You can now create assessments from the metadata collected by the appliance using Azure Migrate Server Assessment. In addition, you can also start migrating VMware VMs using Azure Migrate Server Migration to orchestrate agentless VM replication.


![架構](./media/migrate-appliance/architecture.png)


## <a name="appliance-upgrades"></a>Appliance upgrades

The appliance is upgraded as the Azure Migrate agents running on the appliance are updated.

- This happens automatically because the auto-update is enabled on the appliance by default.
- You can change this default setting to update the agents manually.
- To disable the auto-update, go to the Registry Editor>HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\AzureAppliance and set the registry key- "AutoUpdate" to 0 (DWORD).
 
### <a name="set-agent-updates-to-manual"></a>Set agent updates to manual

For manual updates, make sure that you update all the agents on the appliance at the same time, using the **Update** button for each outdated agent on the appliance. You can switch the update setting back to automatic updates at any time.

## <a name="next-steps"></a>後續步驟

[Learn how](tutorial-assess-vmware.md#set-up-the-appliance-vm) to set up the appliance for VMware.
[Learn how](tutorial-assess-hyper-v.md#set-up-the-appliance-vm) to set up the appliance for Hyper-V.

