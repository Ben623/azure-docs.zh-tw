---
title: 在 PIM 中查看 Azure 資源角色的審核報表-Azure AD |Microsoft Docs
description: 在 Azure AD Privileged Identity Management (PIM) 中檢視 Azure 資源角色的活動和稽核記錄。
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.assetid: ''
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: pim
ms.date: 01/10/2020
ms.author: curtand
ms.collection: M365-identity-device-management
ms.openlocfilehash: 905acd206ba574e092f41707c9a5625bcaed7f8d
ms.sourcegitcommit: 014e916305e0225512f040543366711e466a9495
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/14/2020
ms.locfileid: "75932366"
---
# <a name="view-activity-and-audit-history-for-azure-resource-roles-in-privileged-identity-management"></a>在 Privileged Identity Management 中查看 Azure 資源角色的活動和審核歷程記錄

您可以使用 Azure Active Directory (Azure AD) Privileged Identity Management (PIM)，檢視組織內 Azure 資源角色的活動、啟用及稽核記錄。 適用範圍包括訂用帳戶、資源群組甚至是虛擬機器。 Azure 入口網站中任何利用 Azure 角色型存取控制（RBAC）功能的資源，都可以利用 Privileged Identity Management 中的安全性和生命週期管理功能。

> [!NOTE]
> 如果您的組織具有外包管理功能給使用[Azure 委派資源管理](../../lighthouse/concepts/azure-delegated-resource-management.md)的服務提供者，該服務提供者所授權的角色指派將不會顯示在這裡。

## <a name="view-activity-and-activations"></a>檢視活動和啟用

若要查看各種資源中特定使用者採取了哪些動作，您可以檢視與指定之啟用期間相關聯的 Azure 資源活動。

1. 開啟 **Azure AD Privileged Identity Management**。

1. 按一下 [Azure 資源]。

1. 按一下您想要檢視其活動及啟用的資源。

1. 按一下 [角色] 或 [成員]。

1. 按一下某個使用者。

    您會依日期看到使用者在 Azure 資源中所採取動作的圖形化檢視。 它也會顯示同一段時間週期內最新的角色啟用。

    ![具有資源活動摘要和角色啟用的使用者詳細資料](media/azure-pim-resource-rbac/rbac-user-details.png)

1. 按一下特定的角色啟用可查看詳細資料，以及該使用者作用中時所發生的對應 Azure 資源活動。

    ![已選取角色啟用和依日期顯示的活動詳細資料](media/azure-pim-resource-rbac/rbac-user-resource-activity.png)

## <a name="export-role-assignments-with-children"></a>匯出具有子系的角色指派

您的合規性需求可能會要求您必須提供完整的角色指派清單給稽核員。 Privileged Identity Management 可讓您查詢特定資源的角色指派，其中包含所有子資源的角色指派。 之前系統管理員很難取得訂用帳戶的角色指派完整清單，而且他們必須針對每個特定資源匯出角色指派。 您可以使用 Privileged Identity Management 來查詢訂用帳戶中所有作用中和合格的角色指派，包括所有資源群組和資源的角色指派。

1. 開啟 **Azure AD Privileged Identity Management**。

1. 按一下 [Azure 資源]。

1. 按一下您想要匯出其角色指派的資源，例如訂用帳戶。

1. 按一下 [成員]。

1. 按一下 [匯出] 來開啟 [匯出成員資格] 窗格。

    ![匯出成員資格窗格以匯出所有成員](media/azure-pim-resource-rbac/export-membership.png)

1. 按一下 [匯出所有成員]，匯出 CSV 檔案中的所有角色指派。

    ![已匯出 CSV 檔案中的角色指派，做為 Excel 中的顯示](media/azure-pim-resource-rbac/export-csv.png)

## <a name="view-resource-audit-history"></a>檢視資源稽核記錄

資源稽核可讓您檢視資源的所有角色活動。

1. 開啟 **Azure AD Privileged Identity Management**。

1. 按一下 [Azure 資源]。

1. 按一下您想要檢視其稽核記錄的資源。

1. 按一下 [資源稽核]。

1. 使用預先定義的日期或自訂範圍篩選記錄。

    ![具有篩選的資源審核清單](media/azure-pim-resource-rbac/rbac-resource-audit.png)

1. 在 [稽核類型] 中，選取 [啟動 (已指派 + 已啟動)]。

    ![依啟用 audit 類型篩選的資源審核清單](media/azure-pim-resource-rbac/rbac-audit-activity.png)

1. 在 [動作] 底下，按一下使用者的 [(活動)] 以查看該使用者在 Azure 資源中的活動詳細資料。

    ![特定動作的使用者活動詳細資料](media/azure-pim-resource-rbac/rbac-audit-activity-details.png)

## <a name="view-my-audit"></a>檢視我的稽核

我的稽核可讓您檢視您的個人角色活動。

1. 開啟 **Azure AD Privileged Identity Management**。

1. 按一下 [Azure 資源]。

1. 按一下您想要檢視其稽核記錄的資源。

1. 按一下 [我的稽核]。

1. 使用預先定義的日期或自訂範圍篩選記錄。

    ![目前使用者的審核清單](media/azure-pim-resource-rbac/my-audit-time.png)

## <a name="next-steps"></a>後續步驟

- [在 Privileged Identity Management 中指派 Azure 資源角色](pim-resource-roles-assign-roles.md)
- [在 Privileged Identity Management 中核准或拒絕 Azure 資源角色的要求](pim-resource-roles-approval-workflow.md)
- [查看 Privileged Identity Management 中 Azure AD 角色的審核歷程記錄](pim-how-to-use-audit-log.md)
