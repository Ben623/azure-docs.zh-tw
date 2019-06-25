---
title: Azure Resource Manager 個人資料 | Microsoft Docs
description: 了解如何管理與 Azure 資源管理員作業相關聯的個人資料。
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 05/14/2018
ms.author: tomfitz
ms.openlocfilehash: cc8400a3b6d51bacd55d3c711700a1d07266f528
ms.sourcegitcommit: b7a44709a0f82974578126f25abee27399f0887f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/18/2019
ms.locfileid: "67206284"
---
# <a name="manage-personal-data-associated-with-azure-resource-manager"></a>管理與 Azure 資源管理員相關聯的個人資料

若要避免洩漏敏感資訊，請刪除任何您可能已提供在部署、資源群組或標籤中的個人資訊。 Azure Resource Manager 提供的作業可讓您管理可能已提供在部署、資源群組或標籤中的個人資料。

[!INCLUDE [Handle personal data](../../includes/gdpr-intro-sentence.md)]

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="delete-personal-data-in-deployment-history"></a>刪除部署記錄中的個人資料

針對部署，資源管理員會在部署記錄中保留參數值和狀態訊息。 這些值會保存到您從記錄中刪除部署為止。 若要查看您是否在這些值中提供了個人資料，請列出部署。 如果您發現個人資料，請從記錄中刪除部署。

若要在記錄中列出**部署**，請使用：

* [依資源群組列示](/rest/api/resources/deployments/listbyresourcegroup)
* [Get-AzResourceGroupDeployment](/powershell/module/az.resources/Get-AzResourceGroupDeployment)
* [az group deployment list](/cli/azure/group/deployment#az-group-deployment-list)

若要從記錄中刪除**部署**，請使用：

* [刪除](/rest/api/resources/deployments/delete)
* [Remove-AzResourceGroupDeployment](/powershell/module/az.resources/Remove-AzResourceGroupDeployment)
* [az group deployment delete](/cli/azure/group/deployment#az-group-deployment-delete)

## <a name="delete-personal-data-in-resource-group-names"></a>刪除資源群組名稱中的個人資料

資源群組的名稱會保存到您刪除資源群組為止。 若要查看您是否在名稱中提供了個人資料，請列出資源群組。 如果您發現個人資料，請[將資源移到](resource-group-move-resources.md)新的資源群組，並刪除名稱中含有個人資料的資源群組。

若要列出**資源群組**，請使用：

* [清單](/rest/api/resources/resourcegroups/list)
* [Get-AzResourceGroup](/powershell/module/az.resources/Get-AzResourceGroup)
* [az group list](/cli/azure/group#az-group-list)

若要刪除**資源群組**，請使用：

* [刪除](/rest/api/resources/resourcegroups/delete)
* [Remove-AzResourceGroup](/powershell/module/az.resources/Remove-AzResourceGroup)
* [az group delete](/cli/azure/group#az-group-delete)

## <a name="delete-personal-data-in-tags"></a>在標籤中刪除個人資料

標籤名稱和值會保存到您刪除或修改標籤為止。 若要查看您是否在標籤中提供了個人資料，請列出標籤。 如果您發現個人資料，請刪除標籤。

若要列出**標籤**，請使用：

* [清單](/rest/api/resources/tags/list)
* [Get-AzTag](/powershell/module/az.resources/Get-AzTag)
* [az tag list](/cli/azure/tag#az-tag-list)

若要刪除**標籤**，請使用：

* [刪除](/rest/api/resources/tags/delete)
* [Remove-AzTag](/powershell/module/az.resources/Remove-AzTag)
* [az tag delete](/cli/azure/tag#az-tag-delete)

## <a name="next-steps"></a>後續步驟
* 如需 Azure Resource Manager 的概觀，請參閱[什麼是資源管理員？](resource-group-overview.md)