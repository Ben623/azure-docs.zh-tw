---
title: Azure HPC 快取必要條件
description: 使用 Azure HPC 快取的必要條件
author: ekpgh
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 10/30/2019
ms.author: rohogue
ms.openlocfilehash: ca7a12f45f8d907ee65df85e349883e4c14af47a
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/04/2019
ms.locfileid: "73582157"
---
# <a name="prerequisites-for-azure-hpc-cache"></a>Azure HPC 快取的必要條件

在使用 Azure 入口網站建立新的 Azure HPC 快取之前，請確定您的環境符合這些需求。

## <a name="azure-subscription"></a>Azure 訂閱

建議使用付費訂用帳戶。

> [!NOTE]
> 在 GA 版本的前幾個月期間，Azure HPC 快取小組必須先將您的訂用帳戶新增至存取清單，才能用來建立快取實例。 此程式有助於確保每個客戶從其快取取得高品質的回應能力。 填寫[此表單](https://aka.ms/onboard-hpc-cache)以要求存取權。

## <a name="network-infrastructure"></a>網路基礎結構

您必須先設定兩個網路相關的必要條件，才能使用您的快取：

* Azure HPC Cache 實例的專用子網
* DNS 支援，讓快取可以存取儲存體和其他資源

### <a name="cache-subnet"></a>快取子網

Azure HPC 快取需要具有下列品質的專用子網：

* 子網必須至少有64個可用的 IP 位址。
* 子網無法裝載任何其他 Vm，即使是適用于用戶端電腦的相關服務也一樣。
* 如果您使用多個 Azure HPC 快取實例，每個實例都需要自己的子網。

最佳做法是為每個快取建立新的子網。 您可以建立新的虛擬網路和子網，做為建立快取的一部分。

### <a name="dns-access"></a>DNS 存取

快取需要 DNS 才能存取其虛擬網路以外的資源。 視您使用的資源而定，您可能需要設定自訂的 DNS 伺服器，並設定該伺服器與 Azure DNS 伺服器之間的轉送：

* 若要存取 Azure Blob 儲存體端點和其他內部資源，您需要以 Azure 為基礎的 DNS 伺服器。
* 若要存取內部部署存放裝置，您必須設定可解析儲存體主機名稱的自訂 DNS 伺服器。

如果您只需要存取 Blob 儲存體，您可以針對您的快取使用預設的 Azure 提供的 DNS 伺服器。 不過，如果您需要存取其他資源，您應該建立自訂 DNS 伺服器，並將其設定為將任何 Azure 特定的解析要求轉送至 Azure DNS 伺服器。 （您也可以使用簡單 DNS 伺服器，對所有可用的快取掛接點之間的用戶端連線進行負載平衡）。

[針對 azure 虛擬網路中的資源](https://docs.microsoft.com/azure/virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances)，深入瞭解 azure 虛擬網路和 DNS 伺服器設定的名稱解析。

## <a name="permissions"></a>使用權限

開始建立快取之前，請先檢查這些許可權相關的必要條件。

* 快取實例必須能夠建立虛擬網路介面（Nic）。 建立快取的使用者在訂用帳戶中必須具有足夠的許可權，才能建立 Nic。

* 如果使用 Blob 儲存體，Azure HPC Cache 需要授權才能存取您的儲存體帳戶。 您可以使用角色型存取控制（RBAC），為您的 Blob 儲存體提供快取存取權。 需要兩個角色：儲存體帳戶參與者和儲存體 Blob 資料參與者。 遵循[新增儲存體目標](hpc-cache-add-storage.md#add-the-access-control-roles-to-your-account)中的指示來新增角色。

## <a name="storage-infrastructure"></a>儲存體基礎結構

快取支援 Azure Blob 容器或 NFS 硬體儲存體匯出。 建立快取之後，新增儲存體目標。

每種儲存體類型都有特定的必要條件。

### <a name="nfs-storage-requirements"></a>NFS 儲存體需求

如果使用內部部署硬體儲存體，快取必須具有從其子網到資料中心的高頻寬網路存取權。 建議使用[ExpressRoute](https://docs.microsoft.com/azure/expressroute/)或類似的存取權。

NFS 後端存放裝置必須是相容的硬體/軟體平臺。 如需詳細資訊，請洽詢 Azure HPC 快取小組。

### <a name="blob-storage-requirements"></a>Blob 儲存體需求

如果您想要在快取中使用 Azure Blob 儲存體，您需要相容的儲存體帳戶，以及使用 Azure HPC 快取格式化資料填入的空白 Blob 容器或容器，如[將資料移至 Azure Blob 儲存體](hpc-cache-ingest.md)中所述。

請先建立帳戶和容器，再嘗試將它新增為儲存體目標。

若要建立相容的儲存體帳戶，請使用下列設定：

* 效能：**標準**
* 帳戶種類： **StorageV2 （一般用途 v2）**
* 複寫：**本地備援儲存體 (LRS)**
* 存取層（預設）：**熱**

最佳做法是在與快取相同的位置中使用儲存體帳戶。
<!-- clarify location - same region or same resource group or same virtual network? -->

您也必須為快取應用程式提供您 Azure 儲存體帳戶的存取權。 遵循[新增儲存體目標](hpc-cache-add-storage.md#add-the-access-control-roles-to-your-account)中的說明，為快取授與存取角色儲存體帳戶參與者和儲存體 Blob 資料參與者。 如果您不是儲存體帳戶擁有者，請讓擁有者執行此步驟。

## <a name="next-steps"></a>後續步驟

* 從 Azure 入口網站[建立 AZURE HPC](hpc-cache-create.md)快取實例
