---
title: Azure 儲存體簡介 - Azure 中的雲端儲存體 | Microsoft Docs
description: Azure 儲存體是 Microsoft 的雲端儲存體解決方案。 Azure 儲存體提供了高可用性、安全、持久、可大幅調整且具有備援的資料物件儲存體。
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 01/16/2020
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: 5bab70b6b023a4e6510e32368d407a38388cde2f
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/13/2020
ms.locfileid: "79256064"
---
# <a name="introduction-to-azure-storage"></a>Azure 儲存體簡介

Azure 儲存體是 Microsoft 針對最新資料儲存體環境推出的雲端儲存體解決方案。 Azure 儲存體提供可大幅調整的資料物件存放區、雲端檔案系統服務、能可靠傳訊的訊息存放區，以及 NoSQL 存放區。 Azure 儲存體的特色：

- **持久與高可用性。** 備援功能可在發生暫時性硬體失敗時，確保您的資料安全無虞。 您也可以選擇在資料中心或地理區域間複寫資料，以便在發生地方性災難或天然災害時獲得額外保護。 以此方式複寫資料，可在發生未預期的中斷事件時保持高可用性。
- **安全。** 所有寫入 Azure 儲存體的資料皆會由服務進行加密。 Azure 儲存體在存取您資料的人員控管上，提供更細微的控制。
- **可調整。** Azure 儲存體設計為可大幅調整，以符合現今應用程式的資料儲存和效能需求。
- **受控。** Microsoft Azure 會為您處理硬體維護、更新和重大問題。
- **可存取。** 您可以從世界各地透過 HTTP 或 HTTPS 存取 Azure 儲存體中的資料。 Microsoft 提供各種語言的 Azure 儲存體用戶端程式庫，包括 .NET、JAVA、node.js、Python、PHP、Ruby、Go 等，以及成熟的 REST API。 Azure 儲存體支援在 Azure PowerShell 或 Azure CLI 中使用指令碼。 而且在使用資料方面，Azure 入口網站和 Azure 儲存體總管提供簡易的視覺式解決方案。  

## <a name="azure-storage-services"></a>Azure 儲存體服務

Azure 儲存體包含以下資料服務：

- [Azure Blob](../blobs/storage-blobs-introduction.md)：適用於文字和二進位資料且可大幅調整的物件存放區。
- [Azure 檔案服務](../files/storage-files-introduction.md)：雲端或內部部署的受控檔案共用。
- [Azure 佇列](../queues/storage-queues-introduction.md)：可在應用程式元件之間可靠傳訊的訊息存放區。
- [Azure 資料表](../tables/table-storage-overview.md)：以無結構描述方式儲存結構化資料的 NoSQL 存放區。

每個服務都會透過儲存體帳戶存取。 若要開始使用，請參閱[建立儲存體帳戶](storage-account-create.md)。

## <a name="blob-storage"></a>Blob 儲存體

Azure Blob 儲存體是 Microsoft 針對雲端推出的物件儲存體解決方案。 Blob 儲存體已針對儲存大量非結構化物件資料 (例如文字或二進位資料) 最佳化。

Blob 儲存體是適合用於：

- 直接提供映像或文件給瀏覽器。
- 儲存檔案供分散式存取。
- 串流傳輸視訊和音訊。
- 儲存備份和還原、災害復原和封存資料。
- 儲存資料供內部部署或 Azure 託管服務進行分析。

使用者可從世界各地透過 HTTP 或 HTTPS 存取 Blob 儲存體中的物件。 使用者或用戶端應用程式可以透過 URL、[Azure 儲存體 REST API](https://docs.microsoft.com/rest/api/storageservices/blob-service-rest-api)、[Azure PowerShell](https://docs.microsoft.com/powershell/module/azure.storage)、[Azure CLI](https://docs.microsoft.com/cli/azure/storage) 或 Azure 儲存體用戶端程式庫存取 Blob。 儲存體用戶端程式庫提供多種語言，包括 [.NET](/dotnet/api/overview/azure/storage?view=azure-dotnet)、[Java](https://docs.microsoft.com/java/api/overview/azure/storage)、[Node.js](https://azure.github.io/azure-storage-node)、[Python](https://azure-storage.readthedocs.io/)、[PHP](https://azure.github.io/azure-storage-php/) 和 [Ruby](https://azure.github.io/azure-storage-ruby)。

如需有關 Blob 儲存體的詳細資訊，請參閱 [Blob 儲存體簡介](../blobs/storage-blobs-introduction.md)。

## <a name="azure-files"></a>Azure 檔案

[Azure 檔案服務](../files/storage-files-introduction.md)可讓您設定高可用性網路檔案共用，其可使用標準伺服器訊息區塊 (SMB) 通訊協定來存取。 這表示多個 VM 可以透過讀取和寫入權限共用相同的檔案。 您也可以使用 REST 介面或儲存體用戶端程式庫來讀取檔案。

區分 Azure 檔案服務與公司檔案共用上的檔案的方法之一，就是您可以使用指向檔案並包含共用存取簽章 (SAS) 權杖的 URL，從世界各地存取檔案。 您可以產生 SAS 權杖；SAS 權杖可允許特定一段時間內私人資產的特定存取。

檔案共用可以用於許多常見案例：

- 許多內部部署應用程式會使用檔案共用。 這項功能可讓您更輕鬆地將共用資料的應用程式移轉至 Azure。 如果您將檔案共用掛接至內部部署應用程式使用的相同磁碟機代號，則存取檔案共用的應用程式部分應會在變動最小 (如果有的話) 的情況下運作。

- 組態檔可以儲存在檔案共用上並從多個 VM 進行存取。 由群組中多個開發人員所用的工具和公用程式可以儲存於檔案共用，確保所有人都可以找到它們並使用相同的版本。

- 可以寫入檔案共用且稍後進行處理或分析的資料範例有三個：診斷記錄、計量和損毀傾印。

這個階段不支援以 Active Directory 為基礎的驗證和存取控制清單 (ACL)，但未來將會提供支援。 儲存體帳戶認證用來提供檔案共用存取權的驗證。 這表示任何掛接共用的人員都具有共用的完整讀取/寫入權限。

如需 Azure 檔案服務的詳細資訊，請參閱 [Azure 檔案服務簡介](../files/storage-files-introduction.md)。

## <a name="queue-storage"></a>佇列儲存體

Azure 佇列服務用來儲存及擷取訊息。 佇列訊息的大小上限為 64 KB，而一個佇列可以包含數百萬則訊息。 佇列通常用來儲存要以非同步方式處理的訊息清單。

例如，假設您希望客戶能夠上傳圖片，而且要建立每張圖片的縮圖。 您可以讓客戶在上傳圖片時等候您建立縮圖。 另外，也可以使用佇列。 當客戶完成上傳時，將訊息寫入佇列。 然後讓 Azure Function 從佇列擷取訊息並建立縮圖。 這項處理的每個部分都可以個別調整，讓您在針對您的使用量進行微調時有更多控制權。

如需 Azure 佇列的詳細資訊，請參閱[佇列簡介](../queues/storage-queues-introduction.md)。

## <a name="table-storage"></a>表格儲存體

Azure 資料表儲存體現在屬於 Azure Cosmos DB。 若要查看 Azure 資料表儲存體文件，請參閱 [Azure 資料表儲存體概觀](../tables/table-storage-overview.md)。 除了現有的 Azure 資料表儲存體服務，有新的 Azure Cosmos DB 資料表 API 供應項目，可提供輸送量最佳化的資料表、全域發佈，以及自動次要索引。 若要深入了解並試用新的進階體驗，請查看 [Azure Cosmos DB 資料表 API](https://aka.ms/premiumtables)。

如需資料表儲存體的詳細資訊，請參閱 [Azure 資料表儲存體概觀](../tables/table-storage-overview.md)。

## <a name="disk-storage"></a>磁碟儲存體

Azure 受控磁碟是虛擬硬碟 (VHD)。 您可以將它視為內部部署伺服器中虛擬化的實體磁碟。 Azure 受控磁碟會儲存為分頁 Blob，它是 Azure 中的隨機 IO 儲存物件。 我們會呼叫受控磁片「受控」，因為它是對分頁 blob、blob 容器和 Azure 儲存體帳戶的抽象概念。 使用受控磁碟，您所要做的就是佈建磁碟，Azure 會負責執行剩餘的部分。

如需受控磁片的詳細資訊，請參閱[Azure 受控磁片簡介](../../virtual-machines/windows/managed-disks-overview.md)。

## <a name="types-of-storage-accounts"></a>儲存體帳戶類型

[!INCLUDE [storage-account-types-include](../../../includes/storage-account-types-include.md)]

如需有關儲存體帳戶類型的詳細資訊，請參閱 [Azure 儲存體帳戶概觀](storage-account-overview.md)。

## <a name="securing-access-to-storage-accounts"></a>保護對儲存體帳戶的存取

Azure 儲存體的每個要求都必須獲得授權。 Azure 儲存體支援下列授權方法：

- **適用于 blob 和佇列資料的 Azure Active Directory （Azure AD）整合。** Azure 儲存體透過角色型存取控制（RBAC），支援透過 Blob 和佇列服務的 Azure AD 進行驗證和授權。 建議使用 Azure AD 授權要求，以獲得更佳的安全性和易用性。 如需詳細資訊，請參閱[使用 Azure Active Directory 授權存取 Azure blob 和佇列](storage-auth-aad.md)。
- **針對 Azure 檔案儲存體（預覽） Azure AD 透過 SMB 的授權。** Azure 檔案儲存體支援透過 Azure Active Directory Domain Services 以身分識別為基礎的 SMB （伺服器訊息區）授權。 已加入網域的 Windows 虛擬機器（Vm）可以使用 Azure AD 認證來存取 Azure 檔案共用。 如需詳細資訊，請參閱[Azure 檔案儲存體（預覽）透過 SMB 進行 Azure Active Directory 授權的總覽](../files/storage-files-active-directory-overview.md)。
- **使用共用金鑰進行授權。** Azure 儲存體 Blob、佇列和表格服務和 Azure 檔案儲存體支援使用共用金鑰進行授權。使用共用金鑰授權的用戶端會傳遞標頭，其中每個要求都使用儲存體帳戶存取金鑰進行簽署。 如需詳細資訊，請參閱[使用共用金鑰進行授權](https://docs.microsoft.com/rest/api/storageservices/authorize-with-shared-key)。
- **使用共用存取簽章（SAS）的授權。** 共用存取簽章（SAS）是一個字串，其中包含可附加至儲存體資源 URI 的安全性權杖。 安全性權杖會封裝條件約束，例如許可權和存取的間隔。 如需詳細資訊，請參閱[使用共用存取簽章（SAS）](storage-sas-overview.md)。
- **對容器和 blob 的匿名存取。** 容器及其 blob 可能可公開使用。 當您指定容器或 blob 為公用時，任何人都可以匿名讀取它;不需要驗證。 如需詳細資訊，請參閱[管理對容器與 Blob 的匿名讀取權限](../blobs/storage-manage-access-to-resources.md)

## <a name="encryption"></a>加密

有兩個基本加密類型可用於儲存體服務。 如需安全性和加密的詳細資訊，請參閱 [Azure 儲存體安全性指南](../blobs/security-recommendations.md)。

### <a name="encryption-at-rest"></a>待用加密

Azure 儲存體加密可保護您的資料，以符合您組織的安全性和合規性承諾。 Azure 儲存體會在保存到儲存體帳戶之前自動加密所有資料，並在抓取之前將其解密。 加密、解密和金鑰管理程式對使用者而言是完全透明的。 客戶也可以選擇使用 Azure Key Vault 來管理自己的金鑰。 如需詳細資訊，請參閱待用[資料的加密 Azure 儲存體](storage-service-encryption.md)。

### <a name="client-side-encryption"></a>用戶端加密

Azure 儲存體用戶端程式庫會提供方法，以在透過網路傳送資料並將回應解密之前，從用戶端程式庫加密資料。 透過用戶端加密加密的資料也會在待用時 Azure 儲存體進行加密。 如需用戶端加密的詳細資訊，請參閱[使用 .net 進行 Azure 儲存體的用戶端加密](storage-client-side-encryption.md)。

## <a name="redundancy"></a>備援性

為了確保您的資料持久，Azure 儲存體會儲存資料的多個複本。 當您設定儲存體帳戶時，您可選取備援選項。

[!INCLUDE [storage-common-redundancy-options](../../../includes/storage-common-redundancy-options.md)]

## <a name="transferring-data-to-and-from-azure-storage"></a>從 Azure 儲存體來回傳輸資料

您可以透過數個選項將資料移入或移出 Azure 儲存體。 應選擇哪個選項，取決於您的資料集大小和網路頻寬。 如需詳細資訊，請參閱[選擇適合資料轉送的 Azure 解決方案](storage-choose-data-transfer-solution.md)。

## <a name="pricing"></a>價格

如需 Azure 儲存體價格的詳細資訊，請參閱[價格頁面](https://azure.microsoft.com/pricing/details/storage/blobs/)。

## <a name="storage-apis-libraries-and-tools"></a>儲存體 API、程式庫和工具

任何可提出 HTTP/HTTPS 要求的語言皆可存取 Azure 儲存體資源。 另外，Azure 儲存體還提供了幾種熱門語言的程式設計程式庫。 這些程式庫可透過處理詳細資料 (例如同步和非同步叫用、進行批次作業、例外狀況管理、自動重試、運作方式等等) 來簡化使用 Azure 儲存體的許多項目。 程式庫目前適用於下列語言和平台，以及正在研發的其他語言和平台：

### <a name="azure-storage-data-api-and-library-references"></a>Azure 儲存體資料 API 和程式庫參考

- [Azure 儲存體 REST API](https://docs.microsoft.com/rest/api/storageservices/)
- [適用于 .NET 的 Azure 儲存體用戶端程式庫](https://docs.microsoft.com/dotnet/api/overview/azure/storage)
- [適用于 JAVA/Android 的 Azure 儲存體用戶端程式庫](https://docs.microsoft.com/java/api/overview/azure/storage)
- [適用于 node.js 的 Azure 儲存體用戶端程式庫](https://docs.microsoft.com/javascript/api/overview/azure/storage-overview)
- [適用于 Python 的 Azure 儲存體用戶端程式庫](https://github.com/Azure/azure-storage-python)
- [適用于 PHP 的 Azure 儲存體用戶端程式庫](https://github.com/Azure/azure-storage-php)
- [適用于 Ruby 的 Azure 儲存體用戶端程式庫](https://github.com/Azure/azure-storage-ruby)
- [Azure 儲存體的用戶端程式庫C++](https://github.com/Azure/azure-storage-cpp)

### <a name="azure-storage-management-api-and-library-references"></a>Azure 儲存體管理 API 和程式庫參考

- [儲存體資源提供者 REST API](https://docs.microsoft.com/rest/api/storagerp/)
- [適用於 .NET 的儲存體資源提供者用戶端程式庫](https://docs.microsoft.com/dotnet/api/overview/azure/storage/management)
- [儲存體服務管理 REST API (傳統)](https://msdn.microsoft.com/library/azure/ee460790.aspx)

### <a name="azure-storage-data-movement-api-and-library-references"></a>Azure 儲存體資料移動 API 和程式庫參考

- [儲存體匯入/匯出服務 REST API](https://docs.microsoft.com/rest/api/storageimportexport/)
- [適用於 .NET 的儲存體資料移動 用戶端程式庫](/dotnet/api/microsoft.azure.storage.datamovement)

### <a name="tools-and-utilities"></a>工具和公用程式

- [儲存體的 Azure PowerShell Cmdlet](https://docs.microsoft.com/powershell/module/az.storage)
- [儲存體的 Azure CLI Cmdlet](https://docs.microsoft.com/cli/azure/storage)
- [AzCopy 命令列公用程式](https://aka.ms/downloadazcopy)
- [Azure 儲存體總管](https://azure.microsoft.com/features/storage-explorer/) 是一個免費的獨立應用程式，可讓您在 Windows、MacOS 和 Linux 上以視覺化方式處理 Azure 儲存體資料。
- [Azure 儲存體用戶端工具](../storage-explorers.md)
- [Azure 開發人員工具](https://azure.microsoft.com/tools/)

## <a name="next-steps"></a>後續步驟

若要讓 Azure 儲存體啟動並執行，請參閱[建立儲存體帳戶](storage-account-create.md)。
