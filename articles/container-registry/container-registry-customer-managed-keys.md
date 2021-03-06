---
title: 靜態加密與客戶管理的金鑰
description: 瞭解 Azure container registry 的待用加密，以及如何使用儲存在 Azure Key Vault 中客戶管理的金鑰來加密您的登錄
ms.topic: article
ms.date: 03/10/2020
ms.custom: ''
ms.openlocfilehash: 7bfc4e9a73280ab330efbeeba51a5dcb0a80da10
ms.sourcegitcommit: 512d4d56660f37d5d4c896b2e9666ddcdbaf0c35
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/14/2020
ms.locfileid: "79365335"
---
# <a name="encryption-using-customer-managed-keys"></a>使用客戶管理的金鑰進行加密

當您在 Azure container registry 中儲存映射和其他成品時，Azure 會使用[服務管理的金鑰](../security/fundamentals/encryption-atrest.md#data-encryption-models)，自動將待用的登錄內容加密。 您可以使用您在 Azure Key Vault 中建立和管理的金鑰，以額外的加密層補充預設加密。 本文會逐步引導您使用 Azure CLI 和 Azure 入口網站的步驟。

透過與[Azure Key Vault](../key-vault/key-vault-overview.md)的整合，可支援使用客戶管理的金鑰進行伺服器端加密。 您可以建立自己的加密金鑰，然後將其儲存在金鑰保存庫中，或是使用 Azure Key Vault 的 API 來產生加密金鑰。 您也可以使用 Azure Key Vault 來審核金鑰使用方式。

這項功能適用于**Premium** container registry 服務層級。 如需登錄服務層和限制的相關資訊，請參閱[Azure Container Registry sku](container-registry-skus.md)。

> [!IMPORTANT]
> 這項功能目前為預覽狀態，並適用一些[限制](#preview-limitations)。 若您同意[補充的使用規定][terms-of-use]即可取得預覽。 在公開上市 (GA) 之前，此功能的某些領域可能會變更。
>
   
## <a name="preview-limitations"></a>預覽限制 

* 您目前只能在建立登錄時啟用此功能。
* 在登錄上啟用客戶管理的金鑰之後，您就無法將它停用。
* 以客戶管理的金鑰加密的登錄中，目前不支援[內容信任](container-registry-content-trust.md)。
* 在以客戶管理的金鑰加密的登錄中，執行目前僅保留24小時[ACR 工作](container-registry-tasks-overview.md)的記錄。 如果您需要保留記錄較長的時間，請參閱[匯出和儲存工作執行記錄](container-registry-tasks-logs.md#alternative-log-storage)的指引。

## <a name="prerequisites"></a>Prerequisites

若要使用本文中的 Azure CLI 步驟，您需要 Azure CLI 版本2.2.0 或更新版本。 如果您需要安裝或升級，請參閱[安裝 Azure CLI](/cli/azure/install-azure-cli)。

## <a name="enable-customer-managed-key---cli"></a>啟用客戶管理的金鑰-CLI

### <a name="create-a-resource-group"></a>建立資源群組

如有需要，請執行[az group create][az-group-create]命令來建立用來建立金鑰保存庫、container registry 和其他必要資源的資源群組。

```azurecli
az group create --name <resource-group-name> --location <location>
```

### <a name="create-a-user-assigned-managed-identity"></a>建立使用者指派的受控識別

使用[az identity create][az-identity-create]命令，[為 Azure 資源](../active-directory/managed-identities-azure-resources/overview.md)建立使用者指派的受控識別。 您的登錄將使用此身分識別來存取 Key Vault 服務。

```azurecli
az identity create \
  --resource-group <resource-group-name> \
  --name <managed-identity-name> 
```

在命令輸出中，記下下列值： `id` 和 `principalId`。 您在稍後的步驟中需要這些值，才能設定金鑰保存庫的登錄存取權。

```JSON
{
  "clientId": "xxxx2bac-xxxx-xxxx-xxxx-192cxxxx6273",
  "clientSecretUrl": "https://control-eastus.identity.azure.net/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/myresourcegroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myidentityname/credentials?tid=xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx&oid=xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx&aid=xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/myresourcegroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myresourcegroup",
  "location": "eastus",
  "name": "myidentityname",
  "principalId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "resourceGroup": "myresourcegroup",
  "tags": {},
  "tenantId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "type": "Microsoft.ManagedIdentity/userAssignedIdentities"
}
```

為了方便起見，請將這些值儲存在環境變數中：

```azurecli
identityID=$(az identity show --resource-group <resource-group-name> --name <managed-identity-name> --query 'id' --output tsv)

identityPrincipalID=$(az identity show --resource-group <resource-group-name> --name <managed-identity-name> --query 'principalId' --output tsv)
```

### <a name="create-a-key-vault"></a>建立金鑰保存庫

使用[az keyvault create][az-keyvault-create]建立金鑰保存庫，以儲存用於登錄加密的客戶管理金鑰。 

若要防止意外刪除金鑰或金鑰保存庫而造成資料遺失，您必須啟用下列設定： [虛**刪除**] 和 [**清除保護**]。 下列範例包含這些設定的參數： 

```azurecli
az keyvault create --name <key-vault-name> \
  --resource-group <resource-group-name> \
  --enable-soft-delete \
  --enable-purge-protection
```

### <a name="add-key-vault-access-policy"></a>新增金鑰保存庫存取原則

設定金鑰保存庫的原則，讓身分識別可以存取它。 在下列[az keyvault set-policy][az-keyvault-set-policy]命令中，您會傳遞先前建立之受控識別的主體識別碼，並將其儲存在環境變數中。 將索引鍵許可權設定為**get**、 **unwrapKey**和**wrapKey**。  

```azurecli
az keyvault set-policy \
  --resource-group <resource-group-name> \
  --name <key-vault-name> \
  --object-id $identityPrincipalID \
  --key-permissions get unwrapKey wrapKey 
```

### <a name="create-key-and-get-key-id"></a>建立金鑰並取得金鑰識別碼

執行[az keyvault key create][az-keyvault-key-create]命令，在金鑰保存庫中建立金鑰。

```azurecli
az keyvault key create \
  --name <key-name> \
  --vault-name <key-vault-name>
```

在 命令輸出 中，記下金鑰的識別碼，`kid`。 您會在下一個步驟中使用此識別碼：

```JSON
[...]
  "key": {
    "crv": null,
    "d": null,
    "dp": null,
    "dq": null,
    "e": "AQAB",
    "k": null,
    "keyOps": [
      "encrypt",
      "decrypt",
      "sign",
      "verify",
      "wrapKey",
      "unwrapKey"
    ],
    "kid": "https://mykeyvault.vault.azure.net/keys/mykey/xxxxxxxxxxxxxxxxxxxxxxxx",
    "kty": "RSA",
[...]
```
為了方便起見，請將此值儲存在環境變數中：

```azurecli
keyID=$(az keyvault key show --name <keyname> --vault-name <key-vault-name> --query 'key.kid' --output tsv)
```

### <a name="create-a-registry-with-customer-managed-key"></a>使用客戶管理的金鑰建立登錄

執行[az acr create][az-acr-create]命令來建立登錄，並啟用客戶管理的金鑰。 傳遞先前儲存在環境變數中的受控識別主體識別碼和金鑰識別碼：

```azurecli
az acr create \
  --resource-group <resource-group-name> \
  --name <container-registry-name> \
  --identity $identityID \
  --key-encryption-key $keyID \
  --sku Premium
```

### <a name="show-encryption-status"></a>顯示加密狀態

若要顯示是否已啟用使用客戶管理金鑰的登錄加密，請執行[az acr encryption show][az-acr-encryption-show]命令：

```azurecli
az acr encryption show --name <registry-name> 
```

## <a name="enable-customer-managed-key---portal"></a>啟用客戶管理的金鑰-入口網站

### <a name="create-a-managed-identity"></a>建立受控識別

在 Azure 入口網站中建立[適用于 Azure 資源](../active-directory/managed-identities-azure-resources/overview.md)的使用者指派受控識別。 如需相關步驟，請參閱[建立使用者指派的身分識別](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md#create-a-user-assigned-managed-identity)。

記下受控識別的**資源名稱**。 您在稍後的步驟中需要此名稱。

![在 Azure 入口網站中建立使用者指派的受控識別](./media/container-registry-customer-managed-keys/create-managed-identity.png)

### <a name="create-a-key-vault"></a>建立金鑰保存庫

如需建立金鑰保存庫的步驟，請參閱[快速入門：使用 Azure 入口網站從 Azure Key Vault 設定和取出秘密](../key-vault/quick-create-portal.md)。

在為客戶管理的金鑰建立金鑰保存庫時，您必須在 [**基本**] 索引標籤中啟用下列保護設定： [虛**刪除**] 和 [**清除保護**]。 這些設定有助於防止意外刪除金鑰或金鑰保存庫而造成資料遺失。

![在 Azure 入口網站中建立金鑰保存庫](./media/container-registry-customer-managed-keys/create-key-vault.png)

### <a name="add-key-vault-access-policy"></a>新增金鑰保存庫存取原則

設定金鑰保存庫的原則，讓身分識別可以存取它。

1. 流覽至您的金鑰保存庫。
1. 選取 [**設定**] > [**存取原則] > [+ 新增存取原則**]。
1. 選取 [**金鑰許可權**]，然後選取 [**取得**]、[解除包裝**金鑰**] 和 [**包裝金鑰**]。
1. 選取 [**選取主體**]，然後選取使用者指派受控識別的資源名稱。  
1. 選取 [**新增**]，然後選取 [**儲存**]。

![建立金鑰保存庫存取原則](./media/container-registry-customer-managed-keys/add-key-vault-access-policy.png)

### <a name="create-key"></a>建立金鑰

1. 流覽至您的金鑰保存庫。
1. 選取 [設定] > [金鑰]。
1. 選取 [ **+ 產生/匯入**]，並輸入金鑰的唯一名稱。
1. 接受其餘的預設值，然後選取 [**建立**]。
1. 建立之後，請選取金鑰，並記下目前的金鑰版本。

### <a name="create-azure-container-registry"></a>建立 Azure Container Registry

1. 選取 [建立資源] > [容器] > [容器登錄]。
1. 在 [**基本**] 索引標籤中，選取或建立資源群組，然後輸入登錄名稱。 在 [ **SKU**] 中，選取 [ **Premium**]。
1. 在 [**加密**] 索引標籤的 [**客戶管理的金鑰**] 中，選取 [**已啟用**]。
1. 在 [身分**識別**] 中，選取您建立的受控識別。
1. 在 [**加密金鑰**] 中，選取 [**從 Key Vault 選取**]。
1. 在 [**從 Azure Key Vault 選取金鑰**] 視窗中，選取您在上一節中建立的金鑰保存庫、金鑰和版本。
1. 在 [**加密**] 索引標籤中，選取 [**審查 + 建立**]。
1. 選取 [**建立**] 以部署登錄實例。

![在 Azure 入口網站中建立容器登錄](./media/container-registry-customer-managed-keys/create-encrypted-registry.png)

若要在入口網站中查看登錄的加密狀態，請流覽至您的登錄。 在 [**設定**] 下，選取 **[加密（預覽）** ]。

## <a name="enable-customer-managed-key---template"></a>啟用客戶管理的金鑰-範本

您也可以使用 Resource Manager 的範本來建立登錄，並使用客戶管理的金鑰來啟用加密。 

下列範本會建立新的容器登錄和使用者指派的受控識別。 將下列內容複寫到新的檔案，並使用檔案名（例如 `CMKtemplate.json`）儲存檔案。

```JSON
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "vault_name": {
      "defaultValue": "",
      "type": "String"
    },
    "registry_name": {
      "defaultValue": "",
      "type": "String"
    },
    "identity_name": {
      "defaultValue": "",
      "type": "String"
    },
    "kek_id": {
      "type": "String"
    }
  },
  "variables": {},
  "resources": [
    {
      "type": "Microsoft.ContainerRegistry/registries",
      "apiVersion": "2019-12-01-preview",
      "name": "[parameters('registry_name')]",
      "location": "[resourceGroup().location]",
      "sku": {
        "name": "Premium",
        "tier": "Premium"
      },
      "identity": {
        "type": "UserAssigned",
        "userAssignedIdentities": {
          "[resourceID('Microsoft.ManagedIdentity/userAssignedIdentities', parameters('identity_name'))]": {}
        }
      },
      "dependsOn": [
        "[resourceId('Microsoft.ManagedIdentity/userAssignedIdentities', parameters('identity_name'))]"
      ],
      "properties": {
        "adminUserEnabled": false,
        "encryption": {
          "status": "enabled",
          "keyVaultProperties": {
            "identity": "[reference(resourceId('Microsoft.ManagedIdentity/userAssignedIdentities', parameters('identity_name')), '2018-11-30').clientId]",
            "KeyIdentifier": "[parameters('kek_id')]"
          }
        },
        "networkRuleSet": {
          "defaultAction": "Allow",
          "virtualNetworkRules": [],
          "ipRules": []
        },
        "policies": {
          "quarantinePolicy": {
            "status": "disabled"
          },
          "trustPolicy": {
            "type": "Notary",
            "status": "disabled"
          },
          "retentionPolicy": {
            "days": 7,
            "status": "disabled"
          }
        }
      }
    },
    {
      "type": "Microsoft.KeyVault/vaults/accessPolicies",
      "apiVersion": "2018-02-14",
      "name": "[concat(parameters('vault_name'), '/add')]",
      "dependsOn": [
        "[resourceId('Microsoft.ManagedIdentity/userAssignedIdentities', parameters('identity_name'))]"
      ],
      "properties": {
        "accessPolicies": [
          {
            "tenantId": "[subscription().tenantId]",
            "objectId": "[reference(resourceId('Microsoft.ManagedIdentity/userAssignedIdentities', parameters('identity_name')), '2018-11-30').principalId]",
            "permissions": {
              "keys": [
                "get",
                "unwrapKey",
                "wrapKey"
              ]
            }
          }
        ]
      }
    },
    {
      "type": "Microsoft.ManagedIdentity/userAssignedIdentities",
      "apiVersion": "2018-11-30",
      "name": "[parameters('identity_name')]",
      "location": "[resourceGroup().location]"
    }
  ]
}

```

依照上一節中的步驟來建立下列資源：

* 金鑰保存庫，依名稱識別
* 金鑰保存庫金鑰，以金鑰識別碼識別

執行下列[az group deployment create][az-group-deployment-create]命令，以使用先前的範本檔案來建立登錄。 在指示的位置，提供新的登錄名稱和受控識別名稱，以及您所建立的金鑰保存庫名稱和金鑰識別碼。 

```bash
az group deployment create \
  --resource-group <resource-group-name> \
  --template-file CMKtemplate.json \
  --parameters \
    registry_name=<registry-name> \
    identity_name=<managed-identity> \
    vault_name=<key-vault-name> \
    kek_id=<key-vault-key-id>
```

### <a name="show-encryption-status"></a>顯示加密狀態

若要顯示登錄加密的狀態，請執行 [az acr encryption show-status] [az-acr-encryption-show-status] 命令：

```azurecli
az acr encryption show-status --name <registry-name> 
```

## <a name="use-the-registry"></a>使用登錄

當您使用客戶管理的金鑰啟用登錄來加密資料之後，您可以執行與在未使用客戶管理的金鑰加密之登錄中執行的相同登錄作業。 例如，您可以使用登錄進行驗證，並推送 Docker 映射。 請參閱[推送和提取映射](container-registry-get-started-docker-cli.md)中的範例命令。

## <a name="rotate-key"></a>旋轉鍵

您可以根據您的相容性原則，在 Azure Key Vault 中旋轉客戶管理的金鑰。 建立新的金鑰，然後更新登錄以使用新的金鑰來加密資料。 您可以使用 Azure CLI 或在入口網站中執行這些步驟。

例如，執行[az keyvault key create][az-keyvault-key-create]命令來建立新的機碼：

```azurecli
az keyvault key create –-name <new-key-name> --vault-name <key-vault-name> 
```

然後執行[az acr encryption 輪替-key][az-acr-encryption-rotate-key]命令，傳遞新的金鑰識別碼和您先前設定之受控識別的主體識別碼：

```azurecli
az acr encryption rotatekey \
  --name <registry-name> \
  --key-encryption-key <new-key-id> \
  --identity $identityPrincipalID
```

## <a name="revoke-key"></a>撤銷金鑰

藉由變更金鑰保存庫上的存取原則，或藉由刪除金鑰，撤銷客戶管理的加密金鑰。 例如，使用[az keyvault delete-policy][az-keyvault-delete-policy]命令來變更您的登錄所使用之受控識別的存取原則。 例如：

```azurecli
az keyvault delete-policy \
  --resource-group <resource-group-name> \
  --name <key-vault-name> \
  --object-id $identityPrincipalID
```

撤銷金鑰會有效封鎖對所有登錄資料的存取，因為登錄無法存取加密金鑰。 如果已啟用金鑰的存取權，或還原已刪除的金鑰，您的登錄將會挑選金鑰，讓您可以再次存取加密的登錄資料。

## <a name="next-steps"></a>後續步驟

* 深入瞭解[Azure 中](../security/fundamentals/encryption-atrest.md)的待用加密。
* 深入瞭解存取原則，以及如何[保護金鑰保存庫的存取權](../key-vault/key-vault-secure-your-key-vault.md)。
* 若要針對 Azure Container Registry 提供客戶管理金鑰的意見反應，請造訪[ACR GitHub 網站](https://aka.ms/acr/issues)。


<!-- LINKS - external -->
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms

<!-- LINKS - internal -->

[az-feature-register]: /cli/azure/feature#az-feature-register
[az-feature-show]: /cli/azure/feature#az-feature-show
[az-group-create]: /cli/azure/group#az-group-create
[az-identity-create]: /cli/azure/identity#az-identity-create
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-group-deployment-create]: /cli/azure/group/deployment#az-group-deployment-create
[az-keyvault-create]: /cli/azure/keyvault#az-keyvault-create
[az-keyvault-key-create]: /cli/azure/keyvault/keyt#az-keyvault-key-create
[az-keyvault-set-policy]: /cli/azure/keyvault/keyt#az-keyvault-set-policy
[az-keyvault-delete-policy]: /cli/azure/keyvault/keyt#az-keyvault-delete-policy
[az-resource-show]: /cli/azure/resource#az-resource-show
[az-acr-create]: /cli/azure/acr#az-acr-create
[az-acr-show]: /cli/azure/acr#az-acr-show
[az-acr-encryption-rotate-key]: /cli/azure/acr/encryption#az-acr-encryption-rotate-key
[az-acr-encryption-show]: /cli/azure/acr/encryption#az-acr-encryption-show
