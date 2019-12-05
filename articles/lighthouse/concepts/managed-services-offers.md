---
title: Azure Marketplace 中的受控服務供應項目
description: 受控服務供應項目能允許服務提供者在 Azure Marketplace 中向客戶銷售資源管理供應項目。
ms.date: 09/19/2019
ms.topic: conceptual
ms.openlocfilehash: f7dcaa34b1fb471823acd74a7d9b05e42dd44161
ms.sourcegitcommit: 95931aa19a9a2f208dedc9733b22c4cdff38addc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/25/2019
ms.locfileid: "74463993"
---
# <a name="managed-services-offers-in-azure-marketplace"></a>Azure Marketplace 中的受控服務供應項目

此文章說明 **Azure Marketplace** 中的新 [受控服務][](https://azuremarketplace.microsoft.com) 供應項目類型。 受控服務供應項目可讓您搭配 Azure 委派的資源管理向客戶提供資源管理服務。 您可以向所有潛在客戶提供這些供應項目，或僅向一或多個特定客戶提供。 由於您會直接向客戶收取與這些受控服務相關的費用，因此 Microsoft 不會向您收取任何費用。

## <a name="understand-managed-services-offers"></a>了解受控服務供應項目

受控服務能簡化針對 Azure 委派的資源管理將客戶上線的程序。 當客戶在 Azure Marketplace 中購買供應項目時，他們將能夠指定應上架的訂用帳戶和 (或) 資源群組。 請注意，必須手動註冊 **Microsoft.ManagedServices** 資源提供者，訂用帳戶才能先獲得上線授權。

之後，貴組織中的使用者能夠根據您在 [Cloud Partner 入口網站](https://cloudpartner.azure.com/)中建立供應項目時所定義的存取權，在貴組織的租用戶中執行這些資源的管理工作。 這是透過資訊清單完成，該資訊清單可指定能使用 Azure 委派資源管理來存取客戶資源的 Azure AD 使用者、群組及服務主體，以及能定義其存取層級的角色。 透過將權限指派給 Azure AD 群組，而非一系列個別使用者或應用程式帳戶，您便可以在需要變更存取權時個別地加入或移除使用者。

## <a name="public-and-private-offers"></a>公用和私人供應項目

每個受控服務供應項目都包含一或多個方案。 這些方案可以是私人或公用。

如果您想要將供應項目限制為僅提供特定客戶使用，則可以發佈私人方案。 當您這麼做時，該方案只能針對由您提供的特定訂用帳戶識別碼購買。 如需詳細資訊，請參閱[私人供應項目](https://docs.microsoft.com/azure/marketplace/private-offers) \(部分機器翻譯\)。

公用方案能讓您將服務推廣給新的客戶。 當您僅需要客戶租用戶的有限存取權時，這通常是較為適合的選擇。 當您與客戶建立關聯性之後，如果他們決定將額外存取權授與您的組織，您可以僅為該客戶發佈新的私人方案，或是[使用 Azure Resource Manager 範本將他們上線以取得進一步存取](../how-to/onboard-customer.md)。

請注意，當您將某個方案發佈為公用之後，便無法再將它變更為私人。 此外，您無法將公用方案的可用性限制為僅供某個客戶甚至是特定數目的客戶使用，不過您可以選擇完全停止銷售該方案。

如有需要，您可以在相同的供應項目中同時包含公用與私人方案。

## <a name="publish-managed-service-offers"></a>發佈受控服務供應項目

若要了解如何發佈受控服務供應項目，請參閱[將受控服務供應項目發佈到 Azure Marketplace](../how-to/publish-managed-services-offers.md)。 如需使用 Cloud Partner 入口網站發佈至 Azure Marketplace 的一般資訊，請參閱 [Azure Marketplace 和 AppSource 發行指南](https://docs.microsoft.com/azure/marketplace/marketplace-publishers-guide) \(部分機器翻譯\) 和[管理 Azure 與 AppSource Marketplace 供應項目](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/manage-offers/cpp-manage-offers) \(部分機器翻譯\)。

## <a name="next-steps"></a>後續步驟

- 了解 [Azure 委派的資源管理](azure-delegated-resource-management.md)與[跨租用戶管理體驗](cross-tenant-management-experience.md)。
- [將受控服務供應項目發佈](../how-to/publish-managed-services-offers.md)到 Azure Marketplace。
