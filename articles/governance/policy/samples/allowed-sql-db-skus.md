---
title: 範例 - 允許的 SQL DB SKU
description: 此原則定義範例會要求 SQL 資料庫使用已核准的 SKU。 不符合需求的新資源會遭到拒絕。
ms.date: 01/23/2019
ms.topic: sample
ms.openlocfilehash: 0365de32cd2b6d52b236f2105912e0969f9b7a18
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/14/2019
ms.locfileid: "74076381"
---
# <a name="sample---allowed-sql-database-skus"></a>範例 - 允許的 SQL 資料庫 SKU

此原則會要求 SQL 資料庫使用已核准的 SKU。 您需指定允許的 SKU 識別碼陣列或允許的 SKU 名稱陣列。

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-template"></a>範例範本

[!code-json[main](../../../../policy-templates/samples/SQL/sql-db-skus/azurepolicy.json "Allowed SQL DB SKUs")]

您可以使用 [Azure 入口網站](#deploy-with-the-portal) [PowerShell](#deploy-with-powershell) 或 [Azure CLI](#deploy-with-azure-cli) 來部署此範本。

## <a name="deploy-with-the-portal"></a>使用入口網站部署

[![將原則範例部署至 Azure](https://azuredeploy.net/deploybutton.png)](https://portal.azure.com/?feature.customportal=false&microsoft_azure_policy=true&microsoft_azure_policy_policyinsights=true&feature.microsoft_azure_security_policy=true&microsoft_azure_marketplace_policy=true#blade/Microsoft_Azure_Policy/CreatePolicyDefinitionBlade/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-policy%2Fmaster%2Fsamples%2FSQL%2Fsql-db-skus%2Fazurepolicy.json)

## <a name="deploy-with-powershell"></a>使用 PowerShell 部署

[!INCLUDE [sample-powershell-install](../../../../includes/sample-powershell-install-no-ssh-az.md)]

```azurepowershell-interactive
$definition = New-AzPolicyDefinition -Name "sql-db-skus" -DisplayName "Allowed SQL DB SKUs" -description "This policy enables you to specify a set of SQL DB SKUs" -Policy 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/SQL/sql-db-skus/azurepolicy.rules.json' -Parameter 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/SQL/sql-db-skus/azurepolicy.parameters.json' -Mode All
$definition
$assignment = New-AzPolicyAssignment -Name <assignmentname> -Scope <scope>  -listOfSKUId <Allowed SKU IDs> -listOfSKUName <Allowed SKU Names> -PolicyDefinition $definition
$assignment
```

### <a name="clean-up-powershell-deployment"></a>清除 PowerShell 部署

執行下列命令來移除資源群組、VM 和所有相關資源。

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup
```

## <a name="deploy-with-azure-cli"></a>使用 Azure CLI 進行部署

[!INCLUDE [sample-cli-install](../../../../includes/sample-cli-install.md)]

```azurecli-interactive
az policy definition create --name 'sql-db-skus' --display-name 'Allowed SQL DB SKUs' --description 'This policy enables you to specify a set of SQL DB SKUs' --rules 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/SQL/sql-db-skus/azurepolicy.rules.json' --params 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/SQL/sql-db-skus/azurepolicy.parameters.json' --mode All

az policy assignment create --name <assignmentname> --scope <scope> --policy "sql-db-skus"
```

### <a name="clean-up-azure-cli-deployment"></a>清除 Azure CLI 部署

執行下列命令來移除資源群組、VM 和所有相關資源。

```azurecli-interactive
az group delete --name myResourceGroup --yes
```

## <a name="next-steps"></a>後續步驟

- 在 [Azure 原則範例](index.md)中檢閱更多範例