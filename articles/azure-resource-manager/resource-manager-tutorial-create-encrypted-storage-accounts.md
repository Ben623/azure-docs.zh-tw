---
title: 利用範本參考
description: 利用 Azure Resource Manager 範本參考來建立範本，以便部署加密的儲存體帳戶。
author: mumian
ms.date: 03/04/2019
ms.topic: tutorial
ms.author: jgao
ms.custom: seodec18
ms.openlocfilehash: 99ec64529b90c7a80aea62090f80c55cf4e23510
ms.sourcegitcommit: b77e97709663c0c9f84d95c1f0578fcfcb3b2a6c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/22/2019
ms.locfileid: "74326488"
---
# <a name="tutorial-utilize-the-azure-resource-manager-template-reference"></a>教學課程：利用 Azure Resource Manager 範本參考

了解如何尋找範本結構描述資訊，以及使用此資訊來建立 Azure Resource Manager 範本。

在本教學課程中，您會使用 Azure 快速入門範本中的基底範本。 您可以使用範本參考文件來自訂範本，用以建立加密的儲存體帳戶。

![Resource Manager 範本參考 - 部署加密的儲存體帳戶](./media/resource-manager-tutorial-create-encrypted-storage-accounts/resource-manager-template-tutorial-deploy-encrypted-storage-account.png)

本教學課程涵蓋下列工作：

> [!div class="checklist"]
> * 開啟快速入門範本
> * 了解範本
> * 尋找範本參考
> * 編輯範本
> * 部署範本

如果您沒有 Azure 訂用帳戶，請在開始之前先[建立免費帳戶](https://azure.microsoft.com/free/)。

## <a name="prerequisites"></a>必要條件

若要完成本文，您需要：

* Visual Studio Code 搭配 Resource Manager Tools 擴充功能。 請參閱[使用 Visual Studio Code 建立 Azure Resource Manager 範本](./resource-manager-tools-vs-code.md)。

## <a name="open-a-quickstart-template"></a>開啟快速入門範本

[Azure 快速入門範本](https://azure.microsoft.com/resources/templates/)是 Resource Manager 範本的存放庫。 您可以尋找範例範本並加以自訂，而不要從頭建立範本。 本快速入門中使用的範本名為[建立標準儲存體帳戶](https://azure.microsoft.com/resources/templates/101-storage-account-create/)。 此範本會定義 Azure 儲存體帳戶資源。

1. 在 Visual Studio Code 中，選取 [檔案]  >[開啟檔案]  。
2. 在 [檔案名稱]  中，貼上下列 URL：

    ```url
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json
    ```
3. 選取 [開啟]  以開啟檔案。
4. 選取 [檔案]  >[另存新檔]  ，在您的本機電腦上將檔案另存為 **azuredeploy.json**。

## <a name="understand-the-schema"></a>了解結構描述

1. 在 VS Code 中，將範本摺疊至根層級。 此時會有最簡單的結構，內含下列元素：

    ![最簡單的 Resource Manager 範本結構](./media/resource-manager-tutorial-create-encrypted-storage-accounts/resource-manager-template-simplest-structure.png)

    * **$schema**：指定說明範本語言版本的 JSON 結構描述檔案所在的位置。
    * **contentVersion**：為此元素指定任何值，用以記錄範本中的重大變更。
    * **parameters**：指定執行部署以自訂資源部署時所提供的值。
    * **variables**：指定範本中作為 JSON 片段以簡化範本語言運算式的值。
    * **resources**：指定在資源群組中部署或更新的資源類型。
    * **outputs**：指定部署後傳回的值。

2. 擴充**資源**。 已定義 `Microsoft.Storage/storageAccounts` 資源。 此範本會建立未加密的儲存體帳戶。

    ![Resource Manager 範本儲存體帳戶定義](./media/resource-manager-tutorial-create-encrypted-storage-accounts/resource-manager-template-encrypted-storage-resource.png)

## <a name="find-the-template-reference"></a>尋找範本參考

1. 瀏覽至[Azure 範本參考](https://docs.microsoft.com/azure/templates/)。
2. 在 [依標題篩選]  方塊中，輸入**儲存體帳戶**。
3. 選取 [參考]/[範本參考]/[儲存體]/[&lt;版本>]/[儲存體帳戶]  ，如以下螢幕擷取畫面所示：

    ![Resource Manager 範本參考儲存體帳戶](./media/resource-manager-tutorial-create-encrypted-storage-accounts/resource-manager-template-resources-reference-storage-accounts.png)

    如果您不知道要選擇哪個版本，請使用最新版本。

4. 尋找加密相關的定義資訊。

    ```json
    "encryption": {
      "services": {
        "blob": {
          "enabled": boolean
        },
        "file": {
          "enabled": boolean
        }
      },
      "keySource": "string",
      "keyvaultproperties": {
        "keyname": "string",
        "keyversion": "string",
        "keyvaulturi": "string"
      }
    },
    ```

    在相同的網頁上，下列描述會確認 `encryption` 物件用來建立加密的儲存體帳戶。

    ![Resource Manager 範本參考儲存體帳戶加密](./media/resource-manager-tutorial-create-encrypted-storage-accounts/resource-manager-template-resources-reference-storage-accounts-encryption.png)

    而且有兩種方式可供管理加密金鑰。 您可以搭配「儲存體服務加密」使用 Microsoft 管理的加密金鑰，或是使用自己的加密金鑰。 若要簡化本教學課程，您可使用 `Microsoft.Storage` 選項，因此不必建立 Azure Key Vault。

    ![Resource Manager 範本參考儲存體帳戶加密物件](./media/resource-manager-tutorial-create-encrypted-storage-accounts/resource-manager-template-resources-reference-storage-accounts-encryption-object.png)

    加密物件應如下所示：

    ```json
    "encryption": {
        "services": {
            "blob": {
                "enabled": true
            },
            "file": {
              "enabled": true
            }
        },
        "keySource": "Microsoft.Storage"
    }
    ```

## <a name="edit-the-template"></a>編輯範本

從 Visual Studio Code 修改範本，使資源元素如下所示：

![Resource Manager 範本加密的儲存體帳戶資源](./media/resource-manager-tutorial-create-encrypted-storage-accounts/resource-manager-template-encrypted-storage-resources.png)

## <a name="deploy-the-template"></a>部署範本

請參閱 Visual Studio Code 快速入門中的[部署範本](./resource-manager-quickstart-create-templates-use-visual-studio-code.md#deploy-the-template)一節，以取得部署程序。

下列螢幕擷取畫面顯示 CLI 命令列出了新建的儲存體帳戶，這表示已為 Blob 儲存體啟用加密。

![Azure Resource Manager 加密的儲存體帳戶](./media/resource-manager-tutorial-create-encrypted-storage-accounts/resource-manager-template-encrypted-storage-account.png)

## <a name="clean-up-resources"></a>清除資源

不再需要 Azure 資源時，可藉由刪除資源群組來清除您所部署的資源。

1. 在 Azure 入口網站中，選取左側功能表中的 [資源群組]  。
2. 在 [依名稱篩選]  欄位中輸入資源群組名稱。
3. 選取資源群組名稱。  您在資源群組中應該會看到共計六個資源。
4. 從頂端功能表中選取 [刪除資源群組]  。

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已了解如何使用範本參考來自訂現有的範本。 若要了解如何建立多個儲存體帳戶執行個體，請參閱：

> [!div class="nextstepaction"]
> [建立多個執行個體](./resource-manager-tutorial-create-multiple-instances.md)
