---
title: 建立資料標記專案
titleSuffix: Azure Machine Learning
description: 了解如何建立及執行加上標籤專案，以標記用於機器學習的資料。
author: lobrien
ms.author: laobri
ms.service: machine-learning
ms.topic: tutorial
ms.date: 11/04/2019
ms.openlocfilehash: 864cccc4629140754a326823cbaebd7ad8933d3d
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/09/2020
ms.locfileid: "75765064"
---
# <a name="create-a-data-labeling-project-and-export-labels"></a>建立資料標記專案和匯出標籤 

為機器學習專案中的大量資料加上標籤，常是一項麻煩的工作。 具有電腦視覺元件 (例如影像分類或物體偵測) 的專案，通常需要數千個影像的標籤。
 
[Azure Machine Learning](https://ml.azure.com/) 可讓您在集中的位置建立、管理及監視標籤專案。 您可以用它來協調資料、標籤和小組成員，以有效管理標籤工作。 Machine Learning 支援影像分類 (多標籤或多類別)，以及搭配使用週框方塊的物體識別。

Machine Learning 會追蹤進度，並維護未完成標籤工作的佇列。 標籤者不需要擁有 Azure 帳戶就能參與工作。 在使用您的 Microsoft 帳戶或 [Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-whatis) 加以驗證後，標籤者即可在時間允許的範圍內加上相應數量的標籤。

在 Machine Learning 中，您可以啟動和停止專案、新增和移除人員與小組，以及監視進度。 您可以匯出已加上標籤的資料，無論是以 COCO 格式或 Azure ML 資料集的形式均可。

> [!Important]
> 目前僅支援影像分類和物件識別標記專案。 此外，資料影像必須可於 Azure Blob 資料存放區中取得。 (如果您沒有現有的資料存放區，則可以在建立專案期間上傳影像)。 

在本文中，您將學會如何：

> [!div class="checklist"]
> * 建立專案
> * 指定專案的資料和結構
> * 管理處理專案的小組和人員
> * 執行和監視專案
> * 匯出標籤


## <a name="prerequisites"></a>Prerequisites

* 您要加上標籤的資料，無論是在本機檔案中還是在 Azure 儲存體中。
* 您要套用的標籤集合。
* 加上標籤的指示。
* Azure 訂用帳戶。 如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://aka.ms/AMLFree) 。
* Machine Learning 工作區。 請參閱[建立 Azure Machine Learning 工作區](how-to-manage-workspace.md)。

## <a name="create-a-labeling-project"></a>建立加上標籤專案

標籤專案可從 Azure Machine Learning 進行管理。 您可以使用 [標籤專案]  頁面來管理專案和人員。 專案中會指派一或多個小組，而小組中會指派一或多個人員。

如果您的資料已儲存在 Azure Blob 儲存體中，您應先使其成為可用的資料存放區，然後才建立標籤專案。 如需詳細資訊，請參閱[建立和註冊資料存放區](https://docs.microsoft.com/azure/machine-learning/how-to-access-data#create-and-register-datastores)。

若要建立專案，請選取 [新增專案]  。 請為專案指定適當名稱，然後選取 [標籤工作類型]  。

![加上標籤專案建立精靈](./media/how-to-create-labeling-projects/labeling-creation-wizard.png)

* 如果要對影像套用某組類別中的「一或多個」  標籤，請為專案選擇 [影像分類多重標籤]  。 例如，狗狗的相片可同時加上「狗」  和「白天」  標籤。
* 如果只要對影像套用某組類別中的「單一類別」  ，請為專案選擇 [影像分類多重類別]  。
* 如果要將類別和週框方塊指派給影像中的每個物件，請為專案選擇 [物體識別 (週框方塊)]  。

準備好要繼續操作時，請選取 [下一步]  。

## <a name="specify-the-data-to-label"></a>指定要加上標籤的資料

如果您已建立包含資料的資料集，請從 [選取現有的資料集]  下拉式清單中加以選取。 或者，您也可以選取 [建立資料集]  以使用現有 Azure 資料存放區，或上傳本機檔案。

### <a name="create-a-dataset-from-an-azure-datastore"></a>從 Azure 資料存放區建立資料集

在許多情況下，只需上傳本機檔案即可。 但 [Azure 儲存體總管](https://azure.microsoft.com/features/storage-explorer/)可提供更快速且更穩健的方式來傳輸大量資料。 建議您使用儲存體總管作為移動檔案的預設方式。

若要從您已儲存在 Azure Blob 儲存體中的資料建立資料集：

1. 選取 [建立資料集]   > [從資料存放區]  。
1. 為您的資料集指派 [名稱]  。
1. 選擇 [檔案]  作為 [資料集類型]  。  
1. 選取資料存放區。
1. 如果您的資料位於 Blob 儲存體內的子資料夾中，請選擇 [瀏覽]  以選取路徑。
    * 為路徑附加 "/**"，可包含所選路徑的子資料夾內的所有檔案。
    * 附加 "* */* .*" 可包含目前容器及其子資料夾內的所有資料。
1. 提供資料集的描述。
1. 選取 [下一步]  。
1. 確認詳細資料。 選取 [上一步]  以修改設定，或選取 [建立]  以建立資料集。

### <a name="create-a-dataset-from-uploaded-data"></a>從上傳的資料建立資料集

若要直接上傳資料：

1. 選取 [建立資料集]   > [從本機檔案]  。
1. 為您的資料集指派 [名稱]  。
1. 選擇 [檔案] 作為 [資料集類型]  。
1. *選擇性：* 選取 [進階設定]  ，可自訂資料存放區、容器和資料的路徑。
1. 選取 [瀏覽]  可選取要上傳的本機檔案。
1. 提供資料集的描述。
1. 選取 [下一步]  。
1. 確認詳細資料。 選取 [上一步]  以修改設定，或選取 [建立]  以建立資料集。

資料會上傳至 Machine Learning 工作區的預設 Blob 存放區 ("workspaceblobstore")。

## <a name="specify-label-classes"></a>指定標籤類別

在 [標籤類別]  頁面上，指定用來將資料分類的一組類別。 請謹慎執行此動作，因為標籤者的正確性和速度會受其類別選擇能力的影響。 例如，與其完整拼出動植物的完整屬名和種名，不如使用界碼或屬名的縮寫。

每個標籤應輸入於個別的資料列中。 使用 **+** 按鈕可新增資料列。 如果您有 3 或 4 個以上、10 個以下的標籤，您可以在名稱前面加上數字 ("1: "、"2: ")，讓標籤者可利用數字鍵快速執行工作。

## <a name="describe-the-labeling-task"></a>描述加上標籤工作

請務必清楚說明標籤工作。 在 [標籤指示]  頁面上，您可以為標籤指示新增外部網站的連結。 請對受眾提供以工作為導向的適當指示。 請考量下列問題：

* 他們會看到什麼標籤，以及要如何從中選擇？ 是否有參考文字可供參考？
* 如果看起來沒有合適的標籤，該怎麼辦？
* 如果有多個看起來合適的標籤，該怎麼辦？
* 應該對標籤套用怎樣的信賴度臨界值？ 是否要他們在不確定時「盡其所能地猜測」？
* 若關注的物體有局部遮蔽或重疊的情形，該怎麼辦？
* 若關注的物體在影像邊緣遭到裁剪，該怎麼辦？
* 如果他們在提交標籤後發現作業有誤，應怎麼做？

週框方塊的重要問題包括：

* 此工作的週框方塊是如何定義的？ 方塊應完全位於物件內部，還是位於外部？ 方塊應盡可能緊貼地裁切，還是可容許些許間隙？
* 您希望標籤者在定義週框方塊時達到何種程度的嚴謹度和一致性？

>[!NOTE]
> 請務必註明標籤者可以使用數字鍵 1 到 9 來選取前 9 個標籤。

## <a name="initialize-the-labeling-project"></a>初始化加上標籤專案

在標籤專案初始化後，專案的某些層面將是不可變的。 您無法變更工作類型或資料集。 您*可以*修改標籤，以及工作描述的 URL。 在建立專案之前，請仔細檢閱設定。 提交專案之後，您就會回到 [標籤]  首頁，此處會將專案顯示為 [正在初始化]  。 此頁面不會自動重新整理。 因此，在暫停之後，請手動重新整理頁面，以確認專案的狀態是否為 [已建立]  。

## <a name="manage-teams-and-people"></a>管理小組和人員

根據預設，您所建立的每個標籤專案都會有將您納入為成員的新小組。 但小組也可在專案之間共用。 而且，專案可能會有多個小組。 若要建立小組，請在 [小組]  頁面上選取 [新增小組]  。

您可以在 [人員]  頁面上管理人員。 您可以透過電子郵件地址來新增和移除人員。 如果您使用 Microsoft 帳戶或 Azure Active Directory，則每個標籤者都必須透過該途徑進行驗證。  

新增人員後，您可以將其指派給一或多個小組：移至 [小組]  頁面，選取小組，然後選取 [指派人員]  或 [移除人員]  。

若要傳送電子郵件給小組，請選取小組以檢視 [小組詳細資料]  頁面。 在此頁面上，選取 [傳送電子郵件給小組]  以開啟電子郵件草稿，其中包含小組中每個人的地址。

## <a name="run-and-monitor-the-project"></a>執行和監視專案

在您初始化專案後，Azure 就會開始執行該專案。 選取主要 [標籤]  頁面上的專案，以移至 [專案詳細資料]  。 [儀表板]  索引標籤會顯示標籤工作的進度。

在 [資料]  索引標籤上，您可以查看資料集並檢閱已加上標籤的資料。 如果您看到標籤不正確的資料，請選取該資料，然後選擇 [拒絕]  ，以移除標籤並將資料放回未加上標籤的佇列中。

使用 [小組]  索引標籤，對專案指派或取消指派小組。

若要暫停或重新啟動專案，請選取 [暫停]  /[啟動]  按鈕。 只有在專案執行時，您才可以為資料加上標籤。

您可以從 [專案詳細資料]  頁面選取 [為資料加上標籤]  ，來直接為資料加上標籤。

## <a name="export-the-labels"></a>匯出標籤

您可以隨時匯出 Machine Learning 實驗的標籤資料。 影像標籤可以匯出為 [COCO 格式](http://cocodataset.org/#format-data)或匯出為 Azure Machine Learning 資料集。 在標籤專案的 [專案詳細資料]  頁面上使用 [匯出]  按鈕。

COCO 檔案會建立在 Azure Machine Learning 工作區的預設 Blob 存放區中，而其所在的資料夾位於 *export/coco* 內。 您可以在 Machine Learning 的 [資料集]  區段中，存取已匯出的 Azure Machine Learning 資料集。 資料集詳細資料頁面也會提供從 Python 存取標籤的程式碼範例。

![所匯出的資料集](./media/how-to-create-labeling-projects/exported-dataset.png)

## <a name="next-steps"></a>後續步驟

* 為影像加上標籤以進行[影像分類或物體偵測](how-to-label-images.md)
* 深入了解 [Azure Machine Learning 和 Machine Learning Studio (傳統)](compare-azure-ml-to-studio-classic.md)
