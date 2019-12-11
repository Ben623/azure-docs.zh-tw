---
title: 在入口網站中建立 Azure Machine Learning 工作區
titleSuffix: Azure Machine Learning
description: 瞭解如何在 Azure 入口網站中建立、查看和刪除 Azure Machine Learning 的工作區。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sgilley
author: sdgilley
ms.date: 11/04/2019
ms.custom: seodec18
ms.openlocfilehash: 80b554b7dd4f37e1a215892962d8ec5622d8ae5c
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/10/2019
ms.locfileid: "74974031"
---
# <a name="create-and-manage-azure-machine-learning-workspaces-in-the-azure-portal"></a>建立和管理 Azure 入口網站中的 Azure Machine Learning 工作區
[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-basic-enterprise-sku.md)]

在本文中，您將會在[Azure Machine Learning](overview-what-is-azure-ml.md)的 Azure 入口網站中建立、查看和刪除[**Azure Machine Learning 的工作區**](concept-workspace.md)。  入口網站是開始使用工作區的最簡單方式，但隨著您的需求變更或自動化的需求增加，您也可以[使用 CLI](reference-azure-machine-learning-cli.md)、Python 程式[代碼](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)或透過[VS Code 延伸](how-to-vscode-tools.md#get-started-with-azure-machine-learning-for-visual-studio-code)模組來建立和刪除工作區。

## <a name="create-a-workspace"></a>建立工作區

若要建立工作區，您將需要 Azure 訂用帳戶。 如果您沒有 Azure 訂用帳戶，請在開始前建立一個免費帳戶。 立即試用[免費或付費版本的 Azure Machine Learning](https://aka.ms/AMLFree)。

1. 使用您 Azure 訂閱的認證來登入 [Azure 入口網站](https://portal.azure.com/)。 

1. 在 Azure 入口網站的左上角，選取 [+建立資源]。

      ![建立新的資源](../../../includes/media/aml-create-in-portal/create-workspace.gif)

1. 使用搜尋列尋找 **Machine Learning**。

1. 選取 [Machine Learning]。

1. 在 [Machine Learning] 窗格中選取 [建立] 來開始操作。

1. 提供下列資訊來設定新的工作區：

   欄位|描述 
   ---|---
   工作區名稱 |輸入可識別您工作區的唯一名稱。 在此範例中，我們使用 **docs-ws**。 名稱必須是整個資源群組中唯一的。 請使用可輕鬆回想並且與其他人建立的工作區有所區別的名稱。  
   Subscription |選取您要使用的 Azure 訂用帳戶。
   Resource group | 在您的訂用帳戶中使用現有的資源群組，或輸入名稱來建立新的資源群組。 資源群組會保留 Azure 方案的相關資源。 在此範例中，我們使用 **docs-aml**。 
   Location | 選取最接近您的使用者與資料資源的位置，以建立工作區。
   工作區版本 | 選取 [**基本**] 或 [**企業**]。  此工作區版本會決定您可以存取和定價的功能。 深入瞭解[基本和企業版供應](overview-what-is-azure-ml.md#sku)專案。 

    ![設定您的工作區](media/how-to-manage-workspace/select-edition.png)

1. 在完成工作區的設定後，選取 [建立]。 

   > [!Warning] 
   > 在雲端中建立工作區可能需要數分鐘的時間。

   程序完成後，會出現部署成功訊息。 
 
 1. 若要檢視新的工作區，選取 [前往資源]。

### <a name="download-a-configuration-file"></a>下載設定檔

1. 如果您將建立[筆記本 VM](tutorial-1st-experiment-sdk-setup.md#azure)，請略過此步驟。

1. 如果您打算在參考此工作區的本機環境上使用程式碼， 請從工作區的 [概觀] 區段中，選取 [下載 config.xml]。  

   ![下載 config.json](./media/how-to-manage-workspace/configure.png)
   
   使用 Python 指令碼或 Jupyter Notebook 將文件置於目錄結構中。 可以位於相同的目錄，名為 *aml_config* 的子目錄，或位於父目錄。 當您建立筆記本 VM 時，會為您將此檔案新增至 VM 上的正確目錄。

## <a name="upgrade"></a>升級至 Enterprise edition

您可以將工作區從基本版本升級至 Enterprise edition，以利用增強功能，例如低程式碼體驗和增強的安全性功能。

1. 登入 [Azure Machine Learning Studio](https://ml.azure.com)。

1. 選取您想要升級的工作區。

1. 選取頁面右上方的 [**深入瞭解**]。

   [![升級工作區](media/how-to-manage-workspace/upgrade.png)](media/how-to-manage-workspace/upgrade.png#lightbox)

1. 在出現的視窗中選取 [**升級**]。


> [!IMPORTANT]
> 您無法將 Enterprise edition 工作區降級為基本版本工作區。 

## <a name="view"></a>尋找工作區

1. 在 [頂端搜尋] 欄位中，輸入**Machine Learning**。  

1. 選取 [Machine Learning]。

   ![搜尋 Azure Machine Learning 工作區](media/how-to-manage-workspace/find-workspaces.png)

1. 查看找到的工作區清單。 您可以根據訂用帳戶、資源群組和位置來篩選。  

1. 選取工作區以顯示其屬性。

## <a name="delete-a-workspace"></a>刪除工作區

在您要刪除的工作區頂端，使用 [刪除] 按鈕。

  ![刪除按鈕](media/how-to-manage-workspace/delete-workspace.png)


## <a name="clean-up-resources"></a>清除資源

[!INCLUDE [aml-delete-resource-group](../../../includes/aml-delete-resource-group.md)]

## <a name="next-steps"></a>後續步驟

遵循完整的教學課程，瞭解如何使用工作區來建立、定型和部署具有 Azure Machine Learning 的模型。

> [!div class="nextstepaction"]
> [教學課程：訓練模型](tutorial-train-models-with-aml.md)
