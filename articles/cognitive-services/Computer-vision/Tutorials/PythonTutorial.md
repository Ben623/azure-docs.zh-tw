---
title: 執行影像作業 - Python
titlesuffix: Azure Cognitive Services
description: 了解如何透過使用 Jupyter Notebook，將「電腦視覺 API」與 Python 搭配使用。 使用常用的程式庫以視覺方式呈現您的結果。
services: cognitive-services
author: KellyDF
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 07/03/2019
ms.author: kefre
ms.custom: seodec18
ms.openlocfilehash: b18f41db772c1c214bdf48c9bb765b1ce7e6d2c2
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/05/2019
ms.locfileid: "67604164"
---
# <a name="computer-vision-api-jupyter-notebook"></a>電腦視覺 API Jupyter notebook

本指南將說明如何在 Python 中使用電腦視覺 API 以及如何以視覺化方式檢視您使用熱門的程式庫的結果。 您將使用 Jupyter 來執行教學課程。 若要了解如何開始使用互動式 Jupyter Notebook，請參閱 [Jupyter 文件](https://jupyter.readthedocs.io/en/latest/index.html)。

## <a name="prerequisites"></a>必要條件

- [Python 2.7+ 或 3.5+](https://www.python.org/downloads/)
- [pip](https://pip.pypa.io/en/stable/installing/) 工具
- 已安裝 [Jupyter Notebook](https://jupyter.org/install)

## <a name="open-the-notebook-in-jupyter"></a>在 Jupyter 中開啟 notebook 

1. 移至[認知視覺 Python](https://github.com/Microsoft/Cognitive-Vision-Python) GitHub 存放庫。 
2. 按一下綠色按鈕以複製或下載存放庫。 
3. 開啟命令提示字元，然後瀏覽至 **Cognitive-Vision-Python\Jupyter Notebook** 資料夾。
1. 從命令提示字元執行 `pip install requests opencv-python numpy matplotlib` 命令，以確定您已具備所有必要的程式庫。
1. 從命令提示字元執行 `jupyter notebook` 命令，以啟動 Jupyter。
1. 在 Jupyter 視窗中按一下 [電腦視覺 API Example.ipynb]  ，以開啟教學課程 Notebook。

## <a name="run-the-notebook"></a>執行 Notebook

若要使用這個 Notebook，您將需要有「電腦視覺 API」的訂用帳戶金鑰。 請造訪[訂用帳戶頁面](https://azure.microsoft.com/try/cognitive-services/)來註冊。 在 [登入]  頁面上，使用您的 Microsoft 帳戶登入，便能訂閱並取得免費的金鑰。 完成註冊程序之後，將金鑰貼到 Notebook 的 `Variables` 區段中 (重新產生於下方)。 不論是主要金鑰還是次要金鑰都可運作。 請務必以引號括住金鑰使其成為字串。

您也必須確定 `_region` 欄位符合與您的訂用帳戶相對應的區域。

```python
# Variables
_region = 'westcentralus'  # Here you enter the region of your subscription
_url = 'https://{}.api.cognitive.microsoft.com/vision/v2.0/analyze'.format(
    _region)
_key = None  # Here you have to paste your primary key
_maxNumRetries = 10
```

當您執行本教學課程時，您將能夠從 URL 和本機儲存體新增要分析的影像。 指令碼會在 Notebook 中顯示影像和分析資訊。
