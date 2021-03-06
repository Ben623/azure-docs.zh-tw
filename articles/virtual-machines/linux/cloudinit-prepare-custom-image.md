---
title: 準備要與雲端 init 搭配使用的 Azure VM 映射
description: 如何準備現有的 Azure 虛擬機器映像，以便使用 cloud-init 進行部署
author: danis
ms.service: virtual-machines-linux
ms.topic: article
ms.date: 06/24/2019
ms.author: danis
ms.openlocfilehash: 73df3a12ebea3b94563d02eda8f1211401d1ae3f
ms.sourcegitcommit: 5f39f60c4ae33b20156529a765b8f8c04f181143
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/10/2020
ms.locfileid: "78969184"
---
# <a name="prepare-an-existing-linux-azure-vm-image-for-use-with-cloud-init"></a>準備現有的 Linux Azure 虛擬機器映像以搭配 cloud-init 使用
本文會示範如何讓現有的 Azure 虛擬機器準備好進行重新部署，並且可使用 cloud-init。 所產生的映像可用來部署新的虛擬機器或虛擬機器擴展集 - 之後這兩者都可進一步使用 cloud-init 在部署期間進行自訂。  一旦 Azure 佈建資源，這些 cloud-init 指令碼就會在初次開機時執行。 如需深入了解 cloud-init 如何以原生方式在 Azure 和支援的 Linux 散發版本中運作，請參閱 [cloud-init 概觀](using-cloud-init.md)

## <a name="prerequisites"></a>Prerequisites
本文件會假設您已經有正在執行的 Azure 虛擬機器，且執行著支援版本的 Linux 作業系統。 您已設定符合您需求的電腦、已安裝所有必要模組、已處理所有所需更新並且已經過測試，確定其符合您的需求。 

## <a name="preparing-rhel-76--centos-76"></a>準備 RHEL 7.6/CentOS 7。6
您需要使用 SSH 連線至 Linux VM 並執行下列命令，才能安裝 cloud-init。

```bash
sudo yum makecache fast
sudo yum install -y gdisk cloud-utils-growpart
sudo yum install - y cloud-init 
```

更新 `cloud_init_modules` 中的 `/etc/cloud/cloud.cfg` 區段以包含下列模組：
```bash
- disk_setup
- mounts
```

以下範例是一般用途的 `cloud_init_modules` 區段看起來的樣子。
```bash
cloud_init_modules:
 - migrator
 - bootcmd
 - write-files
 - growpart
 - resizefs
 - disk_setup
 - mounts
 - set_hostname
 - update_hostname
 - update_etc_hosts
 - rsyslog
 - users-groups
 - ssh
```
與佈建和處理暫時磁碟相關的許多工作都需要在 `/etc/waagent.conf` 中進行更新。 執行下列命令來更新適當設定。 
```bash
sed -i 's/Provisioning.Enabled=y/Provisioning.Enabled=n/g' /etc/waagent.conf
sed -i 's/Provisioning.UseCloudInit=n/Provisioning.UseCloudInit=y/g' /etc/waagent.conf
sed -i 's/ResourceDisk.Format=y/ResourceDisk.Format=n/g' /etc/waagent.conf
sed -i 's/ResourceDisk.EnableSwap=y/ResourceDisk.EnableSwap=n/g' /etc/waagent.conf
cloud-init clean
```

使用您選擇的編輯器來建立 `/etc/cloud/cloud.cfg.d/91-azure_datasource.cfg` 新的檔案，以僅允許 Azure 做為 Azure Linux 代理程式的資料來源，如下所示：

```bash
# Azure Data Source config
datasource_list: [ Azure ]
```

如果您現有的 Azure 映像已設定分頁檔，但您想要使用 cloud-init 來變更新映像的分頁檔設定，那麼就必須移除現有的分頁檔。

針對以 Red Hat 為基礎的映像 - 遵循下列 Red Hat 文件中的指示，其中會說明如何[移除分頁檔](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/6/html/storage_administration_guide/swap-removing-file)。

針對已啟用分頁檔的 CentOS 映像，您可以執行下列命令來關閉分頁檔：
```bash
sudo swapoff /mnt/resource/swapfile
```

確認分頁檔參考已從 `/etc/fstab` 中移除 - 看起來應該會類似下列輸出：
```text
# /etc/fstab
# Accessible filesystems, by reference, are maintained under '/dev/disk'
# See man pages fstab(5), findfs(8), mount(8) and/or blkid(8) for more info
#
UUID=99cf66df-2fef-4aad-b226-382883643a1c / xfs defaults 0 0
UUID=7c473048-a4e7-4908-bad3-a9be22e9d37d /boot xfs defaults 0 0
```

若要節省空間並移除分頁檔，您可以執行下列命令：
```bash
rm /mnt/resource/swapfile
```
## <a name="extra-step-for-cloud-init-prepared-image"></a>額外步驟 (適用於為使用 cloud-init 準備的映像)
> [!NOTE]
> 如果您的映像之前就是為使用 **cloud-init** 而準備及設定的映像，您需要執行下列步驟。

如果您要自訂為新的特製化來源映像的虛擬機器，先前已透過 cloud-init 進行佈建，您才需要使用下列三個命令。  如果您的映像已設定為使用 Azure Linux 代理程式，則不需要執行這些命令。

```bash
sudo cloud-init clean --logs
sudo waagent -deprovision+user -force
```

## <a name="finalizing-linux-agent-setting"></a>完成 Linux 代理程式設定 
所有 Azure 平台映像皆已安裝 Azure Linux 代理程式，無論是否由 cloud-init 進行設定。  執行下列命令來完成從 Linux 機器取消佈建使用者。 

```bash
sudo waagent -deprovision+user -force
```

如需有關 Azure Linux 代理程式取消佈建命令的詳細資訊，請參閱 [Azure Linux 代理程式](../extensions/agent-linux.md)。

結束 SSH 工作階段，然後從您 Bash 殼層中，執行下列 AzureCLI 命令來解除配置、一般化和建立新的 Azure 虛擬機器映像。  使用代表您來源虛擬機器的適當資訊取代 `myResourceGroup` 和 `sourceVmName`。

```bash
az vm deallocate --resource-group myResourceGroup --name sourceVmName
az vm generalize --resource-group myResourceGroup --name sourceVmName
az image create --resource-group myResourceGroup --name myCloudInitImage --source sourceVmName
```

## <a name="next-steps"></a>後續步驟
如需其他設定變更的 cloud-init 範例，請參閱下列文件：
 
- [將其他 Linux 使用者新增至虛擬機器](cloudinit-add-user.md)
- [執行套件管理員以便在初次開機時更新現有的套件](cloudinit-update-vm.md)
- [變更虛擬機器本機的主機名稱](cloudinit-update-vm-hostname.md) 
- [安裝應用程式套件、更新組態檔案，以及插入金鑰](tutorial-automate-vm-deployment.md)
