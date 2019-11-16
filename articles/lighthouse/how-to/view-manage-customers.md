---
title: 檢視及管理客戶與委派的資源
description: 身為使用 Azure 委派的資源管理的服務提供者，您可以移至 Azure 入口網站中的 [我的客戶] 來檢視所有委派的客戶資源與訂用帳戶。
ms.date: 10/23/2019
ms.topic: overview
ms.openlocfilehash: ed1f2e5044fe5058acc4f8b155b346dd46c51b28
ms.sourcegitcommit: 2d3740e2670ff193f3e031c1e22dcd9e072d3ad9
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/16/2019
ms.locfileid: "74131234"
---
# <a name="view-and-manage-customers-and-delegated-resources"></a>檢視及管理客戶與委派的資源

使用 [Azure 委派的資源管理](../concepts/azure-delegated-resource-management.md)的服務提供者，可以使用 [Azure 入口網站](https://portal.azure.com)中的 [我的客戶]  頁面來檢視委派的客戶資源與訂用帳戶。 雖然我們在此所述的是服務提供者與其客戶，但管理多個租用戶的企業也可以使用相同的程序來合併其管理體驗。

若要存取 Azure 入口網站中的 [我的客戶]  頁面，請選取 [所有服務]  ，然後搜尋**我的客戶**並選取它。 您也可以在靠近 Azure 入口網站頂端的搜尋方塊中輸入「我的客戶」來找到它。

請記住，[我的客戶]  頁面最上方的區段只會顯示委派的訂用帳戶或資源群組之客戶的資訊。 如果您還有其他客戶 (例如透過[雲端解決方案提供者計畫](https://docs.microsoft.com/partner-center/csp-overview)所取得的客戶)，您將無法在此看見那些客戶的資訊，除非您將其資源上線以進行委派的資源管理。 在此頁面下方，[雲端解決方案提供者 (預覽)]  區段會針對已[簽署 Microsoft 客戶合約 (MCA)](https://docs.microsoft.com/partner-center/confirm-customer-agreement) 且加入 Azure 方案的 CSP 客戶，顯示帳單資訊和資源 (不論他們是否已經上線以進行 Azure 委派的資源管理)。 如需詳細資訊，請參閱[開始使用您的 Microsoft 合作夥伴合約計費帳戶](https://docs.microsoft.com/azure/billing/mpa-overview)。

> [!NOTE]
> 您的客戶可以在 Azure 入口網站中瀏覽至 [服務提供者]  ，來檢視服務提供者的相關資訊。 如需詳細資訊，請參閱[檢視和管理服務提供者](view-manage-service-providers.md)。

## <a name="view-and-manage-customer-details"></a>檢視和管理客戶詳細資料

若要檢視客戶詳細資料，請選取位於 [我的客戶]  頁面左側的 [客戶]  。

針對每個客戶，您將會看見該客戶的名稱、客戶識別碼 (租用戶識別碼)，以及與此業務開發相關聯的供應項目。 在 [委派]  欄中，您將會看見委派的訂用帳戶和/或資源群組的數目。

> [!IMPORTANT]
> 若要查看委派，使用者必須在上線程序中，獲授與[讀取者](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#reader)角色 (或包含讀取者存取權的其他內建角色)。

頁面頂端的篩選可讓您對客戶資訊進行排序及分組，或是依特定客戶、供應項目或關鍵字進行篩選。

您可以從此頁面檢視下列資訊：

- 若要查看與某個客戶相關聯的所有訂用帳戶、供應項目與委派，請選取該客戶的名稱。
- 若要查看某個供應項目與其委派的更多詳細資料，請選取該供應項目的名稱。
- 若要檢視所委派訂用帳戶或資源群組的角色指派詳細資料，請在 [委派]  欄中選取該項目。

## <a name="view-delegations"></a>檢視委派

[委派] 會顯示已委派的訂用帳戶/資源群組，以及能夠存取它的使用者與權限。 若要檢視此資訊，請選取位於 [我的客戶]  頁面左側的 [委派]  。

頁面頂端的篩選可讓您對存取指派進行排序及分組，或是依特定客戶、供應項目或關鍵字進行篩選。

與每個委派相關聯的使用者和權限會顯示在 [角色指派]  欄中。 您可以選取每個項目來檢視已被授與該訂用帳戶或資源群組存取權的使用者、群組與服務主體完整清單。 從那裡，您可以選取特定的使用者、群組或服務主體名稱來取得更多詳細資料。

## <a name="work-in-the-context-of-a-delegated-subscription"></a>在委派之訂用帳戶的內容中工作

在 Azure 入口網站內，您可以直接於委派之訂用帳戶的內容中工作，而不需要切換目前正在使用的目錄。 若要這樣做：

1. 選取靠近 Azure 入口網站頂端的 [目錄 + 訂用帳戶]  圖示。
2. 在 [全域訂用帳戶]  篩選中，確保僅選取適用於該委派之訂用帳戶的方塊。 您可以使用 [目前 + 委派的目錄]  下拉式方塊來僅顯示位於特定目錄內的訂用帳戶。 (不要使用 [切換目錄]  選項，因為那會將目錄變更為您目前所登入的目錄。)

如果您接著存取支援[跨租用戶管理體驗](../concepts/cross-tenant-management-experience.md)的服務，該服務將會預設為您所選取之委派訂用帳戶的內容。 您可以依照上述步驟執行並選取 [全選]  方塊 (或是改為選取一或多個額外的訂用帳戶來進行工作) 來變更此情況。

> [!NOTE]
> 如果您被授與一或多個資源群組的存取權，與其存取整個訂用帳戶，您可以選取該資源群組所屬的訂用帳戶。 您接著將能在該訂用帳戶的內容中工作，但是只能存取指定的資源群組。

您也可以從支援跨租用戶管理體驗的服務內，存取與委派的訂用帳戶或資源群組相關聯的功能，方法是從該服務內選取訂用帳戶或資源群組。

## <a name="next-steps"></a>後續步驟

- 了解[跨租用戶管理體驗](../concepts/cross-tenant-management-experience.md)。
- 了解客戶如何能透過移至 Azure 入口網站中的 [服務提供者]  來[檢視和管理服務提供者](view-manage-service-providers.md)。
