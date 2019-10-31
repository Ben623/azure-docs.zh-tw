---
title: 將服務主體新增至 Azure Analysis Services 伺服器管理員角色 | Microsoft Docs
description: 了解如何將自動化服務主體新增至伺服器管理員角色
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 10/29/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: c141bcea030f5afcc3cb33adba32f4a96c335eec
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/30/2019
ms.locfileid: "73147393"
---
# <a name="add-a-service-principal-to-the-server-administrator-role"></a>將服務主體新增至伺服器管理員角色 

 若要將無人看管的 PowerShell 工作自動化，服務主體必須擁有受控 Analysis Services 伺服器的**伺服器管理員**權限。 本文說明如何將服務主體新增至 Azure AS 伺服器上的伺服器管理員角色。

## <a name="before-you-begin"></a>開始之前
完成這項工作前，您必須在 Azure Active Directory 中註冊服務主體。

[建立服務主體 - Azure 入口網站](../active-directory/develop/howto-create-service-principal-portal.md)   
[建立服務主體 - PowerShell](../active-directory/develop/howto-authenticate-service-principal-powershell.md)

## <a name="required-permissions"></a>所需的權限
若要完成這項工作，您必須擁有 Azure AS 伺服器的[伺服器管理員](analysis-services-server-admins.md)權限。 

## <a name="add-service-principal-to-server-administrators-role"></a>將服務主體加入至伺服器管理員角色

1. 在 SSMS 中，連線至 Azure AS 伺服器。
2. 在 [伺服器屬性]  >  [安全性] 中，按一下 [新增]。
3. 在 [選取使用者或群組] 中，以名稱搜尋您註冊的應用程式，選取它，然後按一下 [新增]。

    ![搜尋服務主體帳戶](./media/analysis-services-addservprinc-admins/aas-add-sp-ssms-picker.png)

4. 確認服務主體帳戶識別碼，然後按一下 [確定]。
    
    ![搜尋服務主體帳戶](./media/analysis-services-addservprinc-admins/aas-add-sp-ssms-add.png)


> [!NOTE]
> 針對使用 Azure PowerShell Cmdlet 的伺服器作業，執行排程器的服務主體也必須屬於[Azure 角色型存取控制（RBAC）](../role-based-access-control/overview.md)中資源的**擁有**者角色。 

## <a name="related-information"></a>相關資訊

* [下載 SQL Server PowerShell 模組](https://docs.microsoft.com/sql/ssms/download-sql-server-ps-module)   
* [下載 SSMS](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms)   


