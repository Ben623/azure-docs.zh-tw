---
title: 在 Azure 中準備 Debian Linux VHD
description: 了解如何建立 Debian VHD，以便部署於 Azure 中。
services: virtual-machines-linux
documentationcenter: ''
author: szarkos
manager: gwallace
editor: ''
tags: azure-resource-manager,azure-service-management
ms.assetid: a6de7a7c-cc70-44e7-aed0-2ae6884d401a
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.topic: article
ms.date: 11/13/2018
ms.author: szark
ms.openlocfilehash: 7f371e2681ecc2b3bb4bcb3e1a8b642061dc2449
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/13/2019
ms.locfileid: "74036480"
---
# <a name="prepare-a-debian-vhd-for-azure"></a>準備適用於 Azure 的 Debian VHD
## <a name="prerequisites"></a>先決條件
本節假設您已使用從 [Debian 網站](https://www.debian.org/distrib/) 下載的 .iso 檔案將 Debian Linux 作業系統安裝至虛擬硬碟。 可建立 .vhd 檔案的工具有多種，Hyper-V 只是其中一個範例。 如需 Hyper-V 的使用指示，請參閱 [安裝 Hyper-V 角色及設定虛擬機器](https://technet.microsoft.com/library/hh846766.aspx)。

## <a name="installation-notes"></a>安裝注意事項
* 如需有關針對 Azure 準備 Linux 的更多秘訣，另請參閱[一般 Linux 安裝注意事項](create-upload-generic.md#general-linux-installation-notes)。
* Azure 不支援較新的 VHDX 格式。 您可以使用 Hyper-V 管理員或 **convert-vhd** Cmdlet，將磁碟轉換為 VHD 格式。
* 安裝 Linux 系統時，建議您使用標準磁碟分割而不是 LVM (常是許多安裝的預設設定)。 這可避免 LVM 與複製之虛擬機器的名稱衝突，特別是為了疑難排解而需要將作業系統磁碟連接至其他虛擬機器時。 如果願意，您可以在資料磁碟上使用 [LVM](configure-lvm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) 或 [RAID](configure-raid.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。
* 請勿在作業系統磁碟上設定交換磁碟分割。 您可以設定 Azure Linux 代理程式，以在暫存資源磁碟上建立交換檔。 如需更多詳細資料，請參閱以下步驟。
* Azure 上的所有 VHD 必須具有與 1 MB 對應的虛擬大小。 從未經處理的磁碟轉換成 VHD 時，您必須確定未經處理的磁碟大小在轉換前是 1 MB 的倍數。 如需詳細資訊，請參閱 [Linux 安裝注意事項](create-upload-generic.md#general-linux-installation-notes)。

## <a name="use-azure-manage-to-create-debian-vhds"></a>使用 Azure-Manage 建立 Debian VHD
有一些工具可用來產生適用於 Azure 的 Debian VHD，例如 [Credativ](https://github.com/credativ/azure-manage) 中的 [azure-manage](https://www.credativ.com/) 指令碼。 若不想從頭建立映像，建議採用此方法。 例如，若要建立 Debian 8 VHD，請執行下列命令以下載 `azure-manage` 公用程式 (和相依性)，並執行 `azure_build_image` 指令碼︰

    # sudo apt-get update
    # sudo apt-get install git qemu-utils mbr kpartx debootstrap

    # sudo apt-get install python3-pip python3-dateutil python3-cryptography
    # sudo pip3 install azure-storage azure-servicemanagement-legacy azure-common pytest pyyaml
    # git clone https://github.com/credativ/azure-manage.git
    # cd azure-manage
    # sudo pip3 install .

    # sudo azure_build_image --option release=jessie --option image_size_gb=30 --option image_prefix=debian-jessie-azure section


## <a name="manually-prepare-a-debian-vhd"></a>手動準備 Debian VHD
1. 在 Hyper-V 管理員中，選取虛擬機器。
2. 按一下 [連接] ，以開啟虛擬機器的主控台視窗。
3. 如果您使用 ISO 安裝作業系統，則在 `deb cdrom` 中註解排除與 "`/etc/apt/source.list`" 相關的任一行。

4. 依照以下方法編輯 `/etc/default/grub` 檔案及修改 **GRUB_CMDLINE_LINUX** 參數，以納入用於 Azure 的其他核心參數。
   
        GRUB_CMDLINE_LINUX="console=tty0 console=ttyS0,115200n8 earlyprintk=ttyS0,115200"

5. 重建 grub，然後執行：

        # sudo update-grub

6. 將 Debian 的 Azure 存放庫加入 Debian 8 或 9 的 /etc/apt/sources.list 中：

    **Debian 8.x "Jessie"**

        deb http://debian-archive.trafficmanager.net/debian jessie main
        deb-src http://debian-archive.trafficmanager.net/debian jessie main
        deb http://debian-archive.trafficmanager.net/debian-security jessie/updates main
        deb-src http://debian-archive.trafficmanager.net/debian-security jessie/updates
        deb http://debian-archive.trafficmanager.net/debian jessie-updates main
        deb-src http://debian-archive.trafficmanager.net/debian jessie-updates main
        deb http://debian-archive.trafficmanager.net/debian jessie-backports main
        deb-src http://debian-archive.trafficmanager.net/debian jessie-backports main

    **Debian 9.x "Stretch"**

        deb http://debian-archive.trafficmanager.net/debian stretch main
        deb-src http://debian-archive.trafficmanager.net/debian stretch main
        deb http://debian-archive.trafficmanager.net/debian-security stretch/updates main
        deb-src http://debian-archive.trafficmanager.net/debian-security stretch/updates main
        deb http://debian-archive.trafficmanager.net/debian stretch-updates main
        deb-src http://debian-archive.trafficmanager.net/debian stretch-updates main
        deb http://debian-archive.trafficmanager.net/debian stretch-backports main
        deb-src http://debian-archive.trafficmanager.net/debian stretch-backports main


7. 安裝 Azure Linux 代理程式：
   
        # sudo apt-get update
        # sudo apt-get install waagent

8. 針對 Debian 9+，建議使用新的 Debian Cloud 核心來搭配 Azure 中的 VM 使用。 若要安裝這個新核心，請先建立稱為 /etc/apt/preferences.d/linux.pref 的檔案，其內容如下：
   
        Package: linux-* initramfs-tools
        Pin: release n=stretch-backports
        Pin-Priority: 500
   
    然後執行 "sudo apt-get install linux-image-cloud-amd64" 安裝新的 Debian Cloud 核心。

9. 取消佈建虛擬機器並準備將其佈建於 Azure 上，然後執行：
   
        # sudo waagent –force -deprovision
        # export HISTSIZE=0
        # logout

10. 在 Hyper-V 管理員中，依序按一下 [動作] -> [關閉]。 您現在可以將 Linux VHD 上傳至 Azure。

## <a name="next-steps"></a>後續步驟
您現在可以開始使用您的 Debian 虛擬硬碟在 Azure 建立新的虛擬機器。 如果您是第一次將 .vhd 檔案上傳至 Azure，請參閱[從自訂磁碟建立 Linux VM](upload-vhd.md#option-1-upload-a-vhd)。

