---
title: 管理 Azure 中 Linux VM 的可用性 | Microsoft Docs
description: 了解如何使用多部虛擬機器，確保 Azure 中 Linux 應用程式的高可用性。
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: gwallace
editor: tysonn
tags: azure-resource-manager,azure-service-management
ms.assetid: 891c852a-84c0-4940-a61e-ada6e185bf37
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.topic: article
ms.date: 03/27/2018
ms.author: cynthn
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: ab269bcd56a5e60fdc8434a58b61163cb8769763
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/28/2019
ms.locfileid: "70082913"
---
# <a name="manage-the-availability-of-linux-virtual-machines"></a>管理 Linux 虛擬機器的可用性

了解如何設定及管理多部虛擬機器，以確保 Azure 中 Linux 應用程式的高可用性。 您也可以[管理 Windows 虛擬機器的可用性](../windows/manage-availability.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)。

如需有關使用 CLI 在 Resource Manger 部署模型中建立可用性設定組的指示，請參閱 [azure availset：用來管理可用性設定組的命令](../azure-cli-arm-commands.md#azure-availset-commands-to-manage-your-availability-sets)。

[!INCLUDE [virtual-machines-common-manage-availability](../../../includes/virtual-machines-common-manage-availability.md)]

## <a name="next-steps"></a>後續步驟
若要深入了解如何對虛擬機器進行負載平衡，請參閱 [對虛擬機器進行負載平衡](../virtual-machines-linux-load-balance.md)。

