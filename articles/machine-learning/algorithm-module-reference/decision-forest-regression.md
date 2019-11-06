---
title: 決策樹系回歸：模組參考
titleSuffix: Azure Machine Learning
description: 瞭解如何在 Azure Machine Learning 中使用 [決策樹系回歸] 模組，以根據決策樹的集團建立回歸模型。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 10/22/2019
ms.openlocfilehash: d930a6d856c6608e7792ce8ef3204b39aba0028a
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/04/2019
ms.locfileid: "73497973"
---
# <a name="decision-forest-regression-module"></a>決策樹系回歸模組

本文說明 Azure Machine Learning 設計工具（預覽）中的模組。

使用此模組來建立以決策樹集團為基礎的回歸模型。

設定模型之後，您必須使用已加上標籤的資料集和 [[訓練模型](./train-model.md)] 模組來定型模型。 定型的模型接著就可以用來進行預測。 

## <a name="how-it-works"></a>運作方式

決策樹是非參數化模型，可針對每個實例執行一系列簡單的測試，並在到達分葉節點（決策）之前，先遍歷二進位樹狀結構的資料結構。

決策樹有下列優點：

- 在定型和預測期間，它們都能有效率地進行計算和記憶體使用量。

- 它們可以代表非線性決策界限。

- 它們會執行整合式特徵選取和分類，並在有雜訊功能的情況中具有彈性。

這個回歸模型是由決策樹的集團所組成。 回歸決策樹系中的每個樹狀結構都會以預測的形式輸出高斯分佈。 匯總會在樹狀結構的集團上執行，以找出最接近模型中所有樹狀結構之合併分佈的高斯分佈。

如需此演算法和其實現理論架構的詳細資訊，請參閱這篇文章：決策樹系[：適用于分類、回歸、密度估計、各種方式學習和半監督式學習的統一架構](https://www.microsoft.com/en-us/research/publication/decision-forests-a-unified-framework-for-classification-regression-density-estimation-manifold-learning-and-semi-supervised-learning/?from=http%3A%2F%2Fresearch.microsoft.com%2Fapps%2Fpubs%2Fdefault.aspx%3Fid%3D158806#)

## <a name="how-to-configure-decision-forest-regression-model"></a>如何設定決策樹系回歸模型

1. 將 [**決策樹系回歸**] 模組加入至管線。 您可以在設計工具中的 [ **Machine Learning**]、[**初始化模型**] 和 [**回歸**] 下找到模組。

2. 開啟模組屬性，並針對 [重新**取樣方法**] 選擇用來建立個別樹狀結構的方法。  您可以選擇 [**封袋**]**或 [** 複寫]。

    - **封袋**：封袋也稱為「*啟動*程式匯總」。 回歸決策樹系中的每個樹狀結構都會透過預測來輸出高斯分佈。 匯總是要找出一個高斯，其中的前兩個時間會結合個別樹狀結構所傳回的所有散發，使高斯分佈的混合時間符合。

         如需詳細資訊，請參閱適用于[啟動](https://wikipedia.org/wiki/Bootstrap_aggregating)程式匯總的維琪百科專案。

    - 複寫 **：在**複寫中，每個樹狀結構都是以完全相同的輸入資料進行定型。 判斷每個樹狀節點所使用的分割述詞會維持隨機，而且樹狀結構會不同。

         如需有關使用 [複寫 **] 選項進行定型程式的詳細**資訊，請參閱[電腦視覺和醫學影像分析的決策樹系。Criminisi 和 J. Shotton。Springer link 2013.](https://research.microsoft.com/projects/decisionforests/)。

3. 藉由設定 [**建立定型模式]** 選項，指定您要如何訓練模型。

    - **單一參數**

      如果您知道要如何設定模型，可以提供一組特定值做為引數。 您可能已透過實驗學習這些值，或將它們當做指引來接收。



4. 針對 [**決策樹數目**]，指出要在集團中建立的決策樹總數。 藉由建立更多決策樹，您可能會獲得更好的涵蓋範圍，但訓練時間將會增加。

    > [!TIP]
    > 這個值也會控制視覺化定型模型時所顯示的樹狀結構數目。 如果您想要查看或列印單一樹狀結構，您可以將值設定為1。不過，這表示只會產生一個樹狀結構（具有初始參數集的樹狀結構），而且不會執行任何進一步的反覆運算。

5. 如需**決策樹的最大深度**，請輸入數位來限制任何決策樹的最大深度。 增加樹狀結構的深度可能會提高精確度，但會有一些過度學習並增加定型時間的風險。

6. 針對 [**每個節點的隨機分割數目**]，輸入建立樹狀結構的每個節點時所要使用的分割數目。 「*分割*」（split）表示每個樹狀結構層級（節點）的功能會隨機分割。

7. 針對 [**每個分葉節點的最小樣本數**]，表示在樹狀結構中建立任何終端節點（分葉）所需的最小案例數目。

     藉由增加此值，您可以增加建立新規則的閾值。 例如，預設值為1時，即使是單一案例，也可能會導致建立新的規則。 如果您將此值增加為5，訓練資料必須包含至少五個符合相同條件的案例。


9. 連接已加上標籤的資料集，選取包含不超過兩個結果的單一標籤資料行，然後連接到 [[定型模型](./train-model.md)]。

    - 如果您將 [**建立定型模式]** 選項設定為 [**單一參數**]，請使用 [[定型模型](./train-model.md)] 模組來定型模型。

   

10. 執行管道。

### <a name="results"></a>結果

定型完成後：

+ 若要查看在每個反復專案上建立的樹狀結構，請以滑鼠右鍵按一下定型模組的輸出，然後選取 [**視覺化**]。

+ 若要查看每個節點的規則，請按一下每個樹狀結構，並向下切入至分割。

+ 若要儲存定型模型的快照集，請以滑鼠右鍵按一下定型模組的輸出，然後選取 [**另存為定型模型**]。 管線的這個複本不會在後續的管線執行時更新。 

## <a name="next-steps"></a>後續步驟

請參閱可用來 Azure Machine Learning 的[模組集合](module-reference.md)。 