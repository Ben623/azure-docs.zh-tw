---
title: 在訂用帳戶移動之後，變更金鑰保存庫租用戶識別碼 - Azure Key Vault | Microsoft Docs
description: 了解如何在訂用帳戶移到不同租用戶之後，切換金鑰保存庫的租用戶識別碼
services: key-vault
author: amitbapat
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.topic: tutorial
ms.date: 08/12/2019
ms.author: ambapat
ms.openlocfilehash: 2159b5b515e22458edf3ba0eb5b6f23f3f37ce95
ms.sourcegitcommit: 5b76581fa8b5eaebcb06d7604a40672e7b557348
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/13/2019
ms.locfileid: "68990114"
---
# <a name="change-a-key-vault-tenant-id-after-a-subscription-move"></a>在訂用帳戶移動之後變更金鑰保存庫租用戶識別碼

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="q-my-subscription-was-moved-from-tenant-a-to-tenant-b-how-do-i-change-the-tenant-id-for-my-existing-key-vault-and-set-correct-acls-for-principals-in-tenant-b"></a>問：我的訂用帳戶已從租用戶 A 移到租用戶 B。如何變更現有金鑰保存庫的租用戶識別碼並且為租用戶 B 中的主體設定正確的 ACL？

當您在訂用帳戶中建立新的金鑰保存庫時，它會自動繫結到該訂用帳戶的預設 Azure Active Directory 租用戶識別碼。 所有存取原則項目也會繫結到此租用戶識別碼。 當您將 Azure 訂用帳戶從租用戶 A 移到租用戶 B 時，租用戶 B 中的主體 (使用者和應用程式) 無法存取現有的金鑰保存庫。若要修正這個問題，您需要：

* 將此訂用帳戶中與所有現有金鑰保存庫相關聯的租用戶識別碼變更為租用戶 B。
* 移除所有現有的存取原則項目。
* 新增與租用戶 B 相關聯的存取原則項目。

例如，如果您在已從租用戶 A 移到租用戶 B 的訂用帳戶中有金鑰保存庫 'myvault'，以下就是您變更此金鑰保存庫的租用戶識別碼及移除舊存取原則的方式。

<pre>
Select-AzSubscription -SubscriptionId YourSubscriptionID                   # Select your Azure Subscription
$vaultResourceId = (Get-AzKeyVault -VaultName myvault).ResourceId          # Get your Keyvault's Resource ID 
$vault = Get-AzResource –ResourceId $vaultResourceId -ExpandProperties     # Get the properties for your Keyvault
$vault.Properties.TenantId = (Get-AzContext).Tenant.TenantId               # Change the Tenant that your Keyvault resides in
$vault.Properties.AccessPolicies = @()                                     # Accesspolicies can be updated with real
                                                                           # applications/users/rights so that it does not need to be                                                                              # done after this whole activity. Here we are not setting 
                                                                           # any access policies. 
Set-AzResource -ResourceId $vaultResourceId -Properties $vault.Properties  # Modifies the kevault's properties.
</pre>

由於此保存庫在移動前位於租用戶 A 中，所以 **$vault.Properties.TenantId** 的原始值是租用戶 A，而 **(Get-AzContext).Tenant.TenantId** 則是租用戶 B。

現在，您的保存庫已與正確的租用戶識別碼相關聯，而且移除了舊的存取原則項目，請使用 [Set-AzKeyVaultAccessPolicy](https://docs.microsoft.com/powershell/module/az.keyvault/Set-azKeyVaultAccessPolicy)，來設定新的存取原則項目。

## <a name="next-steps"></a>後續步驟

如果您有關於 Azure 金鑰保存庫的問題，請造訪 [Azure 金鑰保存庫論壇](https://social.msdn.microsoft.com/forums/azure/home?forum=AzureKeyVault)。
