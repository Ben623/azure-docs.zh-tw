---
title: 從 Azure 媒體編碼器遷移至媒體編碼器標準 |Microsoft Docs
description: 本主題討論如何從 Azure 媒體編碼器遷移至媒體編碼器標準媒體處理器。
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/21/2019
ms.author: juliako
ms.openlocfilehash: 7bbebe71f6a3278d70767ac9f9dbb9d55e6d481a
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/25/2019
ms.locfileid: "75453387"
---
# <a name="migrate-from-azure-media-encoder-to-media-encoder-standard"></a>從 Azure 媒體編碼器遷移至媒體編碼器標準

本文討論從舊版 Azure 媒體編碼器（AME）媒體處理器（即將于2020年3月31日淘汰）到媒體編碼器標準媒體處理器的升級步驟。  

以 AME 編碼檔案時，客戶通常會使用名為的預設字串，例如 `H264 Adaptive Bitrate MP4 Set 1080p`。 為了進行遷移，您的程式碼必須更新為使用**媒體編碼器標準**媒體處理器，而不是 [AME]，以及其中一個對等`H264 Multiple Bitrate 1080p`[系統預設值](media-services-mes-presets-overview.md)（例如）。 

## <a name="migrating-to-media-encoder-standard"></a>遷移至媒體編碼器標準

以下是使用舊版C#媒體處理器的一般程式碼範例。 

```csharp
// Declare a new job. 
IJob job = _context.Jobs.Create("AME Job"); 
// Get a media processor reference, and pass to it the name of the  
// processor to use for the specific task. 
IMediaProcessor processor = GetLatestMediaProcessorByName("Azure Media Encoder"); 

// Create a task with the encoding details, using a string preset. 
// In this case " H264 Adaptive Bitrate MP4 Set 1080p" preset is used. 
ITask task = job.Tasks.AddNew("My encoding task", 
    processor, 
    " H264 Adaptive Bitrate MP4 Set 1080p", 
    TaskOptions.None); 
```

以下是使用媒體編碼器標準的更新版本。

```csharp
// Declare a new job. 
IJob job = _context.Jobs.Create("Media Encoder Standard Job"); 
// Get a media processor reference, and pass to it the name of the  
// processor to use for the specific task. 
IMediaProcessor processor = GetLatestMediaProcessorByName("Media Encoder Standard"); 

// Create a task with the encoding details, using a string preset. 
// In this case " H264 Multiple Bitrate 1080p" preset is used. 
ITask task = job.Tasks.AddNew("My encoding task", 
    processor, 
    "H264 Multiple Bitrate 1080p", 
    TaskOptions.None); 
```

### <a name="advanced-scenarios"></a>進階案例 

如果您已使用其架構為 AME 建立自己的編碼預設值，則會有媒體編碼器標準的對[等架構](media-services-mes-schema.md)。 如果您有關于如何將較舊的設定對應到新編碼器的問題, 請透過 mailto:amshelp@microsoft.com  
## <a name="known-differences"></a>已知的差異 

媒體編碼器標準更健全、可靠、具有較佳的效能，並產生比舊版 AME 編碼器更佳的品質輸出。 此外： 

* 媒體編碼器標準會產生不同命名慣例的輸出檔，而不是 AME。
* 媒體編碼器標準會產生成品，例如包含[輸入檔案中繼資料](media-services-input-metadata-schema.md)和[輸出檔案中繼資料](media-services-output-metadata-schema.md)的檔案。

## <a name="next-steps"></a>後續步驟

* [舊版元件](legacy-components.md)
* [定價頁面](https://azure.microsoft.com/pricing/details/media-services/#encoding)
