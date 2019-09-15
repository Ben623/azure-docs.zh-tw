---
title: 設定 Linux VM 的 Azure Key Vault | Microsoft Docs
description: 如何使用 Azure CLI 設定要與 Azure Resource Manager 虛擬機器搭配使用的 Key Vault。
services: virtual-machines-linux
documentationcenter: ''
author: singhkays
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: bccdd5ab-5ccf-4760-9039-92c6eafb15bd
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.topic: article
ms.date: 02/24/2017
ms.author: kasing
ms.openlocfilehash: cbc8b6be09fcf4232636b580dc0c62482b83bd60
ms.sourcegitcommit: e97a0b4ffcb529691942fc75e7de919bc02b06ff
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/15/2019
ms.locfileid: "71002167"
---
# <a name="how-to-set-up-key-vault-for-virtual-machines-with-the-azure-cli"></a>如何使用 Azure CLI 設定虛擬機器的 Key Vault

在 Azure Resource Manager 堆疊中，密碼/憑證會被塑造成 Key Vault 所提供的資源。 若要深入了解「Azure 金鑰保存庫」，請參閱 [什麼是 Azure 金鑰保存庫？](../../key-vault/key-vault-overview.md) 為了讓 Key Vault 能與 Azure Resource Manager VM 搭配使用，必須將「金鑰保存庫」上的 *EnabledForDeployment* 屬性設定為 true。 此文章說明如何使用 Azure CLI 設定要與 Azure 虛擬機器 (VM) 搭配使用的 Key Vault。 

若要執行這些步驟，您需要安裝最新的 [Azure CLI](/cli/azure/install-az-cli2)，並且使用 [az login](/cli/azure/reference-index) 來登入 Azure 帳戶。

## <a name="create-a-key-vault"></a>建立金鑰保存庫
使用 [az keyvault create](/cli/azure/keyvault) 建立金鑰保存庫並指派部署原則。 下列範例會在 `myResourceGroup` 資源群組中建立名為 `myKeyVault` 的金鑰保存庫：

```azurecli
az keyvault create -l westus -n myKeyVault -g myResourceGroup --enabled-for-deployment true
```

## <a name="update-a-key-vault-for-use-with-vms"></a>更新要與 VM 搭配使用的 Key Vault
使用 [az keyvault update](/cli/azure/keyvault) 對現有的金鑰保存庫設定部署原則。 下列範例會更新 `myResourceGroup` 資源群組中名為 `myKeyVault` 的金鑰保存庫：

```azurecli
az keyvault update -n myKeyVault -g myResourceGroup --set properties.enabledForDeployment=true
```

## <a name="use-templates-to-set-up-key-vault"></a>使用範本來設定金鑰保存庫
當您使用範本時，您需要針對 Key Vault 資源將 `enabledForDeployment` 屬性設定為 `true`，如下所示︰

```json
{
    "type": "Microsoft.KeyVault/vaults",
    "name": "ContosoKeyVault",
    "apiVersion": "2015-06-01",
    "location": "<location-of-key-vault>",
    "properties": {
    "enabledForDeployment": "true",
    ....
    ....
    }
}
```

## <a name="next-steps"></a>後續步驟
如需使用範本來建立 Key Vault 時可以設定的其他選項，請參閱[建立金鑰保存庫](https://azure.microsoft.com/documentation/templates/101-key-vault-create/)。
