---
title: Windows 適用的 Azure 磁碟加密
description: 使用虛擬機器擴充功能將 Azure 磁碟加密部署至 Windows 虛擬機器。
services: virtual-machines-windows
documentationcenter: ''
author: ejarvi
manager: gwallace
editor: ''
ms.assetid: ''
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 06/12/2018
ms.author: ejarvi
ms.openlocfilehash: 8435663dcf92e2617ea2fe9218649e94243272d2
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/13/2020
ms.locfileid: "79250643"
---
# <a name="azure-disk-encryption-for-windows-microsoftazuresecurityazurediskencryption"></a>適用於 Windows 的 Azure 磁碟加密 (Microsoft.Azure.Security.AzureDiskEncryption)

## <a name="overview"></a>概觀

Azure 磁碟加密會利用 BitLocker 在執行 Windows 的 Azure 虛擬機器上提供完整的磁碟加密。  此解決方案與 Azure Key Vault 整合，可讓您管理金鑰保存庫訂用帳戶中的磁碟加密金鑰與祕密。 

## <a name="prerequisites"></a>Prerequisites

如需必要條件的完整清單，請參閱[適用于 Linux vm 的 Azure 磁碟加密](../linux/disk-encryption-overview.md)，特別是下列各節：

- [適用于 Linux Vm 的 Azure 磁碟加密](../windows/disk-encryption-overview.md#supported-vms-and-operating-systems)
- [網路需求](../windows/disk-encryption-overview.md#networking-requirements)
- [群組原則需求](../windows/disk-encryption-overview.md#group-policy-requirements)

## <a name="extension-schemata"></a>延伸模組架構

Windows AzureDiskEncryption 擴充功能有兩個架構：2.2 版，這是較新的建議架構，不會使用 Azure Active Directory （AAD）屬性和 v1.1，這是需要 AAD 屬性的較舊架構。 您必須使用對應于您所使用之延伸模組的架構版本： AzureDiskEncryption 延伸模組版本2.2 的架構 v4.0，適用于 AzureDiskEncryption 擴充功能1.1 版的架構 v1.1。

### <a name="schema-v22-no-aad-recommended"></a>架構2.2：無 AAD （建議）

建議所有新的 Vm 使用2.2 架構，而且不需要 Azure Active Directory 屬性。

```json
{
  "type": "extensions",
  "name": "[name]",
  "apiVersion": "2015-06-15",
  "location": "[location]",
  "properties": {
    "publisher": "Microsoft.Azure.Security",
    "settings": {
      "EncryptionOperation": "[encryptionOperation]",
      "KeyEncryptionAlgorithm": "[keyEncryptionAlgorithm]",
      "KeyEncryptionKeyURL": "[keyEncryptionKeyURL]",
      "KekVaultResourceId": "[keyVaultResourceID]",
      "KeyVaultURL": "[keyVaultURL]",
      "KeyVaultResourceId": "[keyVaultResourceID]",
      "SequenceVersion": "sequenceVersion]",
      "VolumeType": "[volumeType]"
    },
  "type": "AzureDiskEncryption",
  "typeHandlerVersion": "[extensionVersion]"
  }
}
```


### <a name="schema-v11-with-aad"></a>架構 v1.1：使用 AAD 

1\.1 架構需要 `aadClientID`，而且 `aadClientSecret` 或 `AADClientCertificate`，不建議用於新的 Vm。

使用 `aadClientSecret`：

```json
{
  "type": "extensions",
  "name": "[name]",
  "apiVersion": "2015-06-15",
  "location": "[location]",
  "properties": {
    "protectedSettings": {
      "AADClientSecret": "[aadClientSecret]"
    },    
    "publisher": "Microsoft.Azure.Security",
    "settings": {
      "AADClientID": "[aadClientID]",
      "EncryptionOperation": "[encryptionOperation]",
      "KeyEncryptionAlgorithm": "[keyEncryptionAlgorithm]",
      "KeyEncryptionKeyURL": "[keyEncryptionKeyURL]",
      "KekVaultResourceId": "[keyVaultResourceID]",
      "KeyVaultURL": "[keyVaultURL]",
      "KeyVaultResourceId": "[keyVaultResourceID]",
      "SequenceVersion": "sequenceVersion]",
      "VolumeType": "[volumeType]"
    },
  "type": "AzureDiskEncryption",
  "typeHandlerVersion": "[extensionVersion]"
  }
}
```

使用 `AADClientCertificate`：

```json
{
  "type": "extensions",
  "name": "[name]",
  "apiVersion": "2015-06-15",
  "location": "[location]",
  "properties": {
    "protectedSettings": {
      "AADClientCertificate": "[aadClientCertificate]"
    },    
    "publisher": "Microsoft.Azure.Security",
    "settings": {
      "AADClientID": "[aadClientID]",
      "EncryptionOperation": "[encryptionOperation]",
      "KeyEncryptionAlgorithm": "[keyEncryptionAlgorithm]",
      "KeyEncryptionKeyURL": "[keyEncryptionKeyURL]",
      "KekVaultResourceId": "[keyVaultResourceID]",
      "KeyVaultURL": "[keyVaultURL]",
      "KeyVaultResourceId": "[keyVaultResourceID]",
      "SequenceVersion": "sequenceVersion]",
      "VolumeType": "[volumeType]"
    },
  "type": "AzureDiskEncryption",
  "typeHandlerVersion": "[extensionVersion]"
  }
}
```


### <a name="property-values"></a>屬性值

| 名稱 | 值 / 範例 | 資料類型 |
| ---- | ---- | ---- |
| apiVersion | 2015-06-15 | date |
| publisher | Microsoft.Azure.Security | 字串 |
| type | AzureDiskEncryptionForLinux | 字串 |
| typeHandlerVersion | 1.1、2。2 | 字串 |
| （1.1 架構）AADClientID | xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx | guid | 
| （1.1 架構）AADClientSecret | 密碼 | 字串 |
| （1.1 架構）AADClientCertificate | thumbprint | 字串 |
| DiskFormatQuery | {"dev_path":"","name":"","file_system":""} | JSON 字典 |
| EncryptionOperation | EnableEncryption、EnableEncryptionFormatAll | 字串 | 
| KeyEncryptionAlgorithm | 'RSA-OAEP'、'RSA-OAEP-256'、'RSA1_5' | 字串 |
| KeyEncryptionKeyURL | url | 字串 |
| KeyVaultURL | url | 字串 |
| (選擇性) Passphrase | 密碼 | 字串 | 
| SequenceVersion | UNIQUEIDENTIFIER | 字串 |
| VolumeType | 作業系統、資料、全部 | 字串 |

## <a name="template-deployment"></a>範本部署
如需範本部署的範例，請參閱[從資源庫映像建立新的加密 Windows VM](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-create-new-vm-gallery-image)。

## <a name="azure-cli-deployment"></a>Azure CLI 部署

您可以在最新的 [Azure CLI 文件](/cli/azure/vm/encryption?view=azure-cli-latest)中找到相關指示。 

## <a name="troubleshoot-and-support"></a>疑難排解與支援

### <a name="troubleshoot"></a>疑難排解

請參閱 [Azure 磁碟加密疑難排解指南](../../security/azure-security-disk-encryption-tsg.md)。

### <a name="support"></a>支援

如果您在本文中有任何需要協助的地方，您可以連絡 [MSDN Azure 和 Stack Overflow 論壇](https://azure.microsoft.com/support/community/)上的 Azure 專家。 或者，您可以提出 Azure 支援事件。 請移至 [Azure 支援網站](https://azure.microsoft.com/support/options/)，然後選取 [取得支援]。 如需使用 Azure 支援的資訊，請參閱 [Microsoft Azure 支援常見問題集](https://azure.microsoft.com/support/faq/)。

## <a name="next-steps"></a>後續步驟
如需擴充功能的詳細資訊，請參閱[虛擬機器擴充功能和 Windows 功能](features-windows.md)。
