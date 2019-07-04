---
author: craigktreasure
ms.service: azure-spatial-anchors
ms.topic: include
ms.date: 12/13/2018
ms.author: crtreasu
ms.openlocfilehash: 32f4545a45eda8acddd7c93cc4917dbadca9ad4d
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/23/2019
ms.locfileid: "67134990"
---
## <a name="create-a-spatial-anchors-resource"></a>建立 Spatial Anchors 資源

移至 <a href="https://portal.azure.com" target="_blank">Azure 入口網站</a>。

在 Azure 入口網站的左側導覽窗格中，選取 [建立資源]  。

使用搜尋方塊搜尋 **Spatial Anchors**。

   ![搜尋 Spatial Anchors](./media/spatial-anchors-get-started-create-resource/portal-search.png)

選取 [Spatial Anchors]  。 在對話方塊中，選取 [建立]  。

在 [Spatial Anchors 帳戶]  對話方塊中：

- 使用規則英數字元，輸入唯一的資源名稱。
- 選取您要連結資源的訂用帳戶。
- 選取 [新建]  來建立資源群組。 將其命名為 **myResourceGroup**，然後選取 [確定]  。
      [!INCLUDE [resource group intro text](resource-group.md)]
- 選取要在其中放置資源的位置 (區域)。
- 選取 [新增]  開始建立資源。

   ![建立資源](./media/spatial-anchors-get-started-create-resource/create-resource-form.png)

建立資源之後，Azure 入口網站會顯示您的部署已完成。 按一下 [前往資源]  。

![部署完成](./media/spatial-anchors-get-started-create-resource/deployment-complete.png)

然後，您即可檢視資源屬性。 將資源的 [帳戶識別碼]  值複製到文字編輯器，因為您稍後需要用到它。

   ![資源屬性](./media/spatial-anchors-get-started-create-resource/view-resource-properties.png)

在 [設定]  底下，選取 [金鑰]  。 將 [主要金鑰]  值複製到文字編輯器中。 此值為 `Account Key`。 稍後您將會用到此資訊。

   ![帳戶金鑰](./media/spatial-anchors-get-started-create-resource/view-account-key.png)
