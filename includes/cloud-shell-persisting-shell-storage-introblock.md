---
author: jluk
ms.service: cloud-shell
ms.topic: persist-storage
ms.date: 9/7/2018
ms.author: juluk
ms.openlocfilehash: c28441b6fe25b3480a55b79682d5067b19e3023a
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/18/2019
ms.locfileid: "67174317"
---
# <a name="persist-files-in-azure-cloud-shell"></a>在 Azure Cloud Shell 中保存檔案
Cloud Shell 會運用 Azure 檔案儲存體在工作階段間保存檔案。 在初始啟動時，Cloud Shell 會提示您關聯新的或現有的檔案共用，以在工作階段間保存檔案。

> [!NOTE]
> Bash 和 PowerShell 會共用相同的檔案共用。 只有一個檔案共用可以與 Cloud Shell 中的自動掛接相關聯。

## <a name="create-new-storage"></a>建立新的儲存體

當您使用基本設定並只選取訂用帳戶時，Cloud Shell 會代表您在距離您最近的支援區域中建立三個資源：
* 資源群組：`cloud-shell-storage-<region>`
* 儲存體帳戶：`cs<uniqueGuid>`
* 檔案共用：`cs-<user>-<domain>-com-<uniqueGuid>`

![訂用帳戶設定](../articles/cloud-shell/media/persisting-shell-storage/basic-storage.png)

檔案共用會在您的 `$Home` 目錄中掛接為 `clouddrive`。 這是一次性的動作，後續的工作階段會自動掛接檔案共用。 

> [!NOTE]
> 為了安全起見，每位使用者都應該佈建自己的儲存體帳戶。  若要進行角色型存取控制 (RBAC)，使用者在儲存體帳戶層級必須具備參與者權限或更高的存取權限。

檔案共用也包含為您建立的 5 GB 映像，此映像會自動保存 `$Home` 目錄中的資料。 這適用於 Bash 和 PowerShell。

## <a name="use-existing-resources"></a>使用現有的資源

您可以使用進階選項來建立與現有資源的關聯。 選取 Cloud Shell 區域時，您也必須選取在同一個區域中共置的備份儲存體帳戶。 例如，如果您指派的區域是美國西部，則您也必須與位於美國西部的檔案共用建立關聯。

當儲存體設定提示出現時，請選取 [顯示進階設定]  以檢視其他選項。 已填入的儲存體選項會篩選本地備援儲存體 (LRS)、異地備援儲存體 (GRS) 和區域備援儲存體 (ZRS) 帳戶。 

> [!NOTE]
> 建議使用 GRS 或 ZRS 儲存體帳戶，如此您的備份檔案共用能獲得更多復原能力。 備援類型取決於您的目標和偏好的價格。 [深入了解 Azure 儲存體帳戶的複寫選項](https://docs.microsoft.com/azure/storage/common/storage-redundancy)。

![資源群組設定](../articles/cloud-shell/media/persisting-shell-storage/advanced-storage.png)

### <a name="supported-storage-regions"></a>支援的儲存體區域
相關聯的 Azure 儲存體帳戶必須位於與所掛接之目標 Cloud Shell 電腦相同的區域中。 若要尋找您目前的區域，您可以在 Bash 中執行 `env`，以找出 `ACC_LOCATION` 變數。 檔案共用會收到為您建立以便保存 `$Home` 目錄的 5 GB 映像。

Cloud Shell 電腦存在於下列區域：

|領域|區域|
|---|---|
|美洲|美國東部、美國中南部、美國西部|
|歐洲|北歐、西歐|
|亞太地區|印度中部、東南亞|

## <a name="restrict-resource-creation-with-an-azure-resource-policy"></a>使用 Azure 資源原則限制資源建立
您在 Cloud Shell 中建立的儲存體帳戶都會標記 `ms-resource-usage:azure-cloud-shell`。 如果您想要禁止使用者在 Cloud Shell 中建立儲存體帳戶，請建立這個特定標籤所觸發之[標籤的 Azure 資源原則](../articles/azure-policy/json-samples.md)。
