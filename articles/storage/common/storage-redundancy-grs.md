---
title: 適用於 Azure 儲存體中跨區域持久性的異地備援儲存體 (GRS) | Microsoft Docs
description: 異地備援儲存體 (GRS) 會在相距數百英哩的兩個區域之間複寫資料。 GRS 可以針對資料中心內的硬體故障提供防護，也可以針對區域性災害提供防護。
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 10/20/2018
ms.author: tamram
ms.reviewer: artek
ms.subservice: common
ms.openlocfilehash: 16a5f214495025d16d10ee01a7b2a40b78f7a17a
ms.sourcegitcommit: 2d9a9079dd0a701b4bbe7289e8126a167cfcb450
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/29/2019
ms.locfileid: "71670812"
---
# <a name="geo-redundant-storage-grs-cross-regional-replication-for-azure-storage"></a>異地備援儲存體 (GRS)：適用於 Azure 儲存體的跨區域複寫
[!INCLUDE [storage-common-redundancy-GRS](../../../includes/storage-common-redundancy-grs.md)]

## <a name="read-access-geo-redundant-storage"></a>讀取權限異地備援儲存體
讀取權限異地備援儲存體 (RA-GRS) 可為儲存體帳戶提供最大的可用性。 除了跨兩個區域異地複寫之外，RA-GRS 還提供對次要位置中資料的唯讀存取權。

啟用次要區域資料的唯讀權限時，您的資料能在儲存體帳戶的次要端點以及主要端點中使用。 次要端點與主要端點類似，但會在帳戶名稱中附加尾碼 `–secondary` 。 例如，如果 Blob 服務的主要端點是 `myaccount.blob.core.windows.net`，則次要端點會是 `myaccount-secondary.blob.core.windows.net`。 主要和次要端點會有相同的儲存體帳戶存取金鑰。

使用 RA-GRS 時要記得考量：

* 您的應用程式必須管理在使用 RA-GRS 時，與哪一個端點進行互動。
* 由於非同步複寫會涉及延遲，因此如果無法從主要區域復原資料，則尚未複寫到次要區域的變更可能會遺失。
* 您可以查看儲存體帳戶的上次同步處理時間。 上次同步處理時間是 GMT 日期/時間值。 在上次同步處理時間之前完成的所有主要位置寫入都已成功寫入到次要位置，這表示現在已經可以從次要位置讀取這些資料。 在上次同步處理時間之後完成的主要位置寫入可能已可讀取，也可能無法讀取。 您可以使用 [Azure 入口網站](https://portal.azure.com/)、[Azure PowerShell](storage-powershell-guide-full.md)、或 Azure 儲存體用戶端程式庫之一查詢這個值。
* 如果您將 GRS 或 RA-GRS 帳戶進行帳戶容錯移轉 (預覽) 至次要區域，該帳戶的寫入權限則會在容錯移轉完成後還原。 如需詳細資訊，請參閱[災害復原和儲存體帳戶容錯移轉 (預覽)](storage-disaster-recovery-guidance.md)。
* RA-GRS 適用於高可用性目的。 如需延展性方面的指引，請檢閱[效能檢查清單](storage-performance-checklist.md)。
* 如需有關 RA-GRS 高可用性的設計建議，請參閱[使用 RA-GRS 儲存體設計高可用性應用程式](storage-designing-ha-apps-with-ragrs.md)。

## <a name="what-is-the-rpo-and-rto-with-grs"></a>RPO 和使用 GRS 的 RTO 為何？

**復原點目標 (RPO)：** 在 GRS 和 RA-GRS 中，儲存體服務會以非同步方式將資料從主要位置異地複寫到次要位置。 主要區域無法使用時，您可以執行次要區域的帳戶容錯移轉 (預覽)。 進行容錯移轉時，尚未異地複寫的最近變更可能會遺失。 可能的遺失資料分鐘數稱為 RPO。 RPO 表示可復原資料的時間點。 「Azure 儲存體」的 RPO 通常低於 15 分鐘，但目前並沒有關於異地複寫時間長短的 SLA。

**復原時間目標 (RTO)：** RTO 是執行容錯移轉並使儲存體帳戶恢復上線的測量標準。 執行容錯移轉的時間包括下列動作：

   * 客戶對於儲存體帳戶從主要區域到次要區域進行容錯移轉之前的時間。
   * Azure 變更主要 DNS 項目以指向次要位置來執行容錯移轉所需的時間。

## <a name="paired-regions"></a>配對的區域 
建立儲存體帳戶時，您可以為帳戶選取主要區域。 配對的次要區域會視主要區域而定，且無法變更。 如需有關 Azure 所支援區域的最新資訊，請參閱[商務持續性和災害復原 (BCDR)：Azure 配對區域](../../best-practices-availability-paired-regions.md)。

## <a name="see-also"></a>另請參閱
- [Azure 儲存體複寫](storage-redundancy.md)
- [本地備援儲存體 (LRS)：適用於 Azure 儲存體的低成本資料備援](storage-redundancy-lrs.md)
- [區域備援儲存體 (ZRS)：高可用性 Azure 儲存體應用程式](storage-redundancy-zrs.md)
