---
title: 管理 Machine Learning Studio 工作區
titleSuffix: Azure Machine Learning Studio
description: 管理 to Azure Machine Learning Studio 工作區的存取權，並部署及管理 Machine Learning API Web 服務
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: xiaoharper
ms.author: amlstudiodocs
ms.custom: previous-author=heatherbshapiro, previous-ms.author=hshapiro
ms.date: 02/27/2017
ms.openlocfilehash: a947f9a94dd4ceed624e6b04a671b21b8926d25e
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "60322782"
---
# <a name="manage-an-azure-machine-learning-studio-workspace"></a>管理 Azure Machine Learning Studio 工作區

> [!NOTE]
> 如需在 Machine Learning Web 服務入口網站中管理 Web 服務的相關資訊，請參閱[使用 Azure Machine Learning Web 服務入口網站管理 Web 服務](manage-new-webservice.md)。
> 
> 

您可以在 Azure 入口網站中管理 Machine Learning Studio 工作區。



## <a name="use-the-azure-portal"></a>使用 Azure 入口網站

若要在 Azure 入口網站中管理 Studio 工作區︰

1. 使用 Azure 訂用帳戶系統管理員帳戶登入 [Azure 入口網站](https://portal.azure.com/)。
2. 在頁面頂端的搜尋方塊中輸入「Machine Learning Studio 工作區」，然後選取 [Machine Learning Studio 工作區]  。
3. 按一下您想要管理的工作區。

除了標準的資源管理資訊和可用的選項，您還可以︰

- 檢視**屬性** - 此頁面會顯示工作區和資源的資訊，而且您可以變更這個工作區所連線的訂用帳戶和資源群組。
- **重新同步儲存體金鑰** - 工作區會保有儲存體帳戶的金鑰。 如果儲存體帳戶變更了金鑰，則您可以按一下 [重新同步金鑰]  來與工作區同步處理金鑰。

若要管理與此 Studio 工作區相關聯的 Web 服務，請使用 Machine Learning Web 服務入口網站。 如需詳細資訊，請參閱[使用 Azure Machine Learning Web 服務入口網站管理 Web 服務](manage-new-webservice.md)。

> [!NOTE]
> 若要部署或管理新的 Web 服務，您必須獲得下列角色的指派：要部署 Web 服務之訂用帳戶上的參與者或系統管理員角色。 如果您邀請另一個使用者到 Machine Learning Studio 工作區，就必須為他們指派訂用帳戶上的參與者或管理員角色，他們才能部署或管理 Web 服務。 
> 
>如需如何設定存取權限的詳細資訊，請參閱[使用 RBAC 和 Azure 入口網站來管理存取權](../../role-based-access-control/role-assignments-portal.md)。

## <a name="next-steps"></a>後續步驟
* 深入了解[使用Azure Resource Manager 範本部署 Machine Learning](deploy-with-resource-manager-template.md)。 
