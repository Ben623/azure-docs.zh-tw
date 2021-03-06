---
title: 使用 Azure HPC Cache 進列區域冗余和容錯移轉復原
description: 為使用 Azure HPC 快取的嚴重損壞修復提供容錯移轉功能的技術
author: ekpgh
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 10/30/2019
ms.author: rohogue
ms.openlocfilehash: 4eb203915c8fedbef6af0e5a3bc14eff1835a92b
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/15/2020
ms.locfileid: "75982167"
---
# <a name="use-multiple-caches-for-regional-failover-recovery"></a>使用多個快取進列區域容錯移轉復原

每個 Azure HPC 快取實例都是在特定訂用帳戶和一個區域中執行。 這表示如果區域發生完全中斷，您的快取工作流程可能會中斷。

本文說明使用第二個區域進行快取容錯移轉，以降低工作中斷風險的策略。

金鑰是使用可從多個區域存取的後端儲存體。 此儲存體可以是具有適當 DNS 支援的內部部署 NAS 系統，或是位於與快取不同區域中的 Azure Blob 儲存體。

當您的工作流程在主要區域中繼續進行時，資料會儲存在區域外的長期儲存體中。 如果快取區域變得無法使用，您可以在次要區域中建立重複的 Azure HPC 快取實例、連接到相同的儲存體，然後從新的快取繼續工作。

## <a name="planning-for-regional-failover"></a>規劃區域性容錯移轉

若要設定已準備好進行容錯移轉的快取，請遵循下列步驟：

1. 請確定您的後端儲存體可在第二個區域中存取。
1. 在規劃建立主要快取實例時，您也應該準備在第二個區域中複寫此安裝程式。 包含下列專案：

   1. 虛擬網路和子網結構
   1. 快取容量
   1. 儲存體目標詳細資料、名稱和命名空間路徑
   1. 用戶端電腦的詳細資料（如果它們位於與快取相同的區域）
   1. 供快取用戶端使用的掛接命令

   > [!NOTE]
   > 您可以透過[Azure Resource Manager 範本](../azure-resource-manager/templates/overview.md)或直接存取其 API，以程式設計方式建立 Azure HPC 快取。 如需詳細資訊，請洽詢 Azure HPC 快取小組。

## <a name="failover-example"></a>容錯移轉範例

例如，假設您想要在 Azure 的美國東部區域中找出您的 Azure HPC 快取。 它會存取儲存在您內部部署資料中心的資料。

您可以使用美國西部2區域中的快取作為容錯移轉備份。

在美國東部建立快取時，請在美國西部2中準備第二個快取以進行部署。 您可以使用腳本或範本，將此準備作業自動化。

在美國東部發生全區域失敗的情況下，建立您在美國西部2區域中準備的快取。

建立快取之後，新增指向相同內部部署資料存放區的儲存體目標，並使用與舊快取儲存體目標相同的匯總命名空間路徑。

如果原始的用戶端受到影響，請在美國西部2區域中建立新的用戶端，以與新的快取搭配使用。

所有用戶端都必須掛接新的快取，即使用戶端不會受到區域中斷的影響。 新的快取與舊的快取有不同的掛接位址。

## <a name="learn-more"></a>了解更多

Azure 應用程式架構指南包含如何[從全區域服務中斷復原](<https://docs.microsoft.com/azure/architecture/resiliency/recovery-loss-azure-region>)的詳細資訊。
<!-- this should be an internal link instead of a URL but I can't find the tree  -->
