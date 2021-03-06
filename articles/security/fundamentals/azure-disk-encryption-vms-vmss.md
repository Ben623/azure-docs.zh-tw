---
title: 虛擬機器和虛擬機器擴展集的 Azure 磁碟加密
description: 本文提供 Azure 磁碟加密的總覽
author: msmbaldwin
ms.service: security
ms.topic: article
ms.author: mbaldwin
ms.date: 10/15/2019
ms.custom: seodec18
ms.openlocfilehash: 239bfb4f5a0e9115c113bb2d5ddbc931f41c0f3d
ms.sourcegitcommit: b4f201a633775fee96c7e13e176946f6e0e5dd85
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/18/2019
ms.locfileid: "72599951"
---
# <a name="azure-disk-encryption-for-virtual-machines-and-virtual-machine-scale-sets"></a>虛擬機器和虛擬機器擴展集的 Azure 磁碟加密

Azure 磁片加密可以同時套用至 Linux 和 Windows 虛擬機器，以及虛擬機器擴展集。 

## <a name="linux-virtual-machines"></a>Linux 虛擬機器

下列文章提供加密 Linux 虛擬機器的指導方針。

### <a name="current-version-of-azure-disk-encryption"></a>目前的 Azure 磁碟加密版本

- [Linux 虛擬機器的 Azure 磁碟加密總覽](../../virtual-machines/linux/disk-encryption-overview.md)
- [Linux Vm 上的 Azure 磁碟加密案例](../../virtual-machines/linux/disk-encryption-linux.md)
- [使用 Azure CLI 建立和加密 Linux VM](../../virtual-machines/linux/disk-encryption-cli-quickstart.md)
- [使用 Azure PowerShell 建立和加密 Linux VM](../../virtual-machines/linux/disk-encryption-powershell-quickstart.md)
- [使用 Azure 入口網站建立和加密 Linux VM](../../virtual-machines/linux/disk-encryption-portal-quickstart.md)
- [建立和設定 Azure 磁碟加密的金鑰保存庫](../../virtual-machines/linux/disk-encryption-key-vault.md)
- [Azure 磁碟加密範例腳本](../../virtual-machines/linux/disk-encryption-sample-scripts.md)
- [Azure 磁碟加密疑難排解](../../virtual-machines/linux/disk-encryption-troubleshooting.md)
- [Azure 磁碟加密常見問題](../../virtual-machines/linux/disk-encryption-faq.md)

### <a name="azure-disk-encryption-with-azure-ad-previous-version"></a>使用 Azure AD 的 Azure 磁片加密（舊版）

- [Linux 虛擬機器 Azure AD 的 Azure 磁碟加密總覽](../../virtual-machines/linux/disk-encryption-overview-aad.md)
- [在 Linux Vm 上使用 Azure AD 案例的 Azure 磁碟加密](../../virtual-machines/linux/disk-encryption-linux.md)
- [使用 Azure AD 建立和設定 Azure 磁碟加密的金鑰保存庫（舊版）](../../virtual-machines/linux/disk-encryption-key-vault-aad.md)

## <a name="windows-virtual-machines"></a>Windows 虛擬機器

下列文章提供加密 Windows 虛擬機器的指引。

### <a name="current-version-of-azure-disk-encryption"></a>目前的 Azure 磁碟加密版本

- [Windows 虛擬機器的 Azure 磁碟加密總覽](../../virtual-machines/windows/disk-encryption-overview.md)
- [Windows Vm 上的 Azure 磁碟加密案例](../../virtual-machines/windows/disk-encryption-windows.md)
- [使用 Azure CLI 建立和加密 Windows VM](../../virtual-machines/windows/disk-encryption-cli-quickstart.md)
- [使用 Azure PowerShell 建立和加密 Windows VM](../../virtual-machines/windows/disk-encryption-powershell-quickstart.md)
- [使用 Azure 入口網站建立和加密 Windows VM](../../virtual-machines/windows/disk-encryption-portal-quickstart.md)
- [建立和設定 Azure 磁碟加密的金鑰保存庫](../../virtual-machines/windows/disk-encryption-key-vault.md)
- [Azure 磁碟加密範例腳本](../../virtual-machines/windows/disk-encryption-sample-scripts.md)
- [Azure 磁碟加密疑難排解](../../virtual-machines/windows/disk-encryption-troubleshooting.md)
- [Azure 磁碟加密常見問題](../../virtual-machines/windows/disk-encryption-faq.md)

### <a name="azure-disk-encryption-with-azure-ad-previous-version"></a>使用 Azure AD 的 Azure 磁片加密（舊版）

- [Windows 虛擬機器 Azure AD 的 Azure 磁碟加密總覽](../../virtual-machines/windows/disk-encryption-overview-aad.md)
- [在 Windows Vm 上使用 Azure AD 案例的 Azure 磁碟加密](../../virtual-machines/windows/disk-encryption-windows.md)
- [使用 Azure AD 建立和設定 Azure 磁碟加密的金鑰保存庫（舊版）](../../virtual-machines/windows/disk-encryption-key-vault-aad.md)

## <a name="virtual-machine-scale-sets"></a>虛擬機器擴展集

下列文章提供加密虛擬機器擴展集的指引。

- [虛擬機器擴展集的 Azure 磁碟加密總覽](../../virtual-machine-scale-sets/disk-encryption-overview.md) 
- [使用 Azure CLI 加密虛擬機器擴展集](../../virtual-machine-scale-sets/disk-encryption-cli.md) 
- [使用 Azure Powershell 加密虛擬機器擴展集](../../virtual-machine-scale-sets/disk-encryption-powershell.md)。
- [使用 Azure Resource Manager 加密虛擬機器擴展集](../../virtual-machine-scale-sets/disk-encryption-azure-resource-manager.md)
- [為 Azure 磁碟加密建立及設定金鑰保存庫](../../virtual-machine-scale-sets/disk-encryption-key-vault.md)
- [使用 Azure 磁碟加密搭配虛擬機器擴展集擴充功能排序](../../virtual-machine-scale-sets/disk-encryption-extension-sequencing.md)

## <a name="next-steps"></a>後續步驟

- [Azure 加密概觀](encryption-overview.md)
- [待用資料加密](encryption-atrest.md)
- [資料安全性與加密的最佳做法](data-encryption-best-practices.md)
