---
author: PatrickFarley
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: include
ms.date: 06/12/2019
ms.author: pafarley
ms.openlocfilehash: 14740e780f6143f19f843891c16b25df761d0b4f
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/25/2019
ms.locfileid: "72931263"
---
當您獲得使用表單辨識器的存取權時，您將會收到「歡迎使用」電子郵件，內含數個連結和資源。 請使用該訊息中的 [Azure 入口網站] 連結開啟 Azure 入口網站，並建立表單辨識器資源。 在 [建立]  窗格中，提供下列資訊：

|    |    |
|--|--|
| **名稱** | 資源的描述性名稱。 建議您使用描述性名稱，例如 *MyNameFormRecognizer*。 |
| **訂用帳戶** | 選取已獲存取權的 Azure 訂用帳戶。 |
| **位置** | 您的認知服務執行個體的位置。 位置不同可能會造成延遲，但不會影響您資源執行階段的可用性。 |
| **定價層** | 資源的成本取決於您選擇的定價層和您的使用量。 如需詳細資訊，請參閱 API [定價詳細資料](https://azure.microsoft.com/pricing/details/cognitive-services/)。
| **資源群組** | 將包含您的資源的 [Azure 資源群組](https://docs.microsoft.com/azure/cloud-adoption-framework/govern/resource-consistency/resource-access-management#what-is-an-azure-resource-group)。 您可以建立新的群組，或將群組新增到既有的群組。 |

> [!IMPORTANT]
> 一般而言，當您在 Azure 入口網站中建立認知服務資源時，您可以選擇建立多服務的訂用帳戶金鑰 (可跨多個認知服務使用) 或單一服務的訂用帳戶金鑰 (僅用於特定認知服務)。 不過，由於表單辨識器是預覽版本，而並未納入多服務的訂用帳戶中，因此您無法建立單一服務的訂用帳戶，除非您使用「歡迎使用」電子郵件中提供的連結。

當您的表單辨識器資源完成部署後，您可以從入口網站中的 [所有資源]  清單尋找並選取該項資源。 然後，選取 [快速入門]  索引標籤，以檢視您的訂用帳戶資料。 將 **Key1** 和**端點**的值儲存至暫存位置。 在下列步驟中，您將使用這些資料。