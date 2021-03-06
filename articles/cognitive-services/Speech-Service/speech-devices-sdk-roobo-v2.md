---
title: 語音裝置 SDK Roobo 智慧型音訊開發工具組 v2-語音服務
titleSuffix: Azure Cognitive Services
description: 開始使用語音裝置 SDK Roobo 智慧型音訊開發工具組 v2 的必要條件和指示。
services: cognitive-services
author: anushapatnala
manager: wellsi
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 02/20/2020
ms.author: v-anusp
ms.openlocfilehash: 5cf851bc9333004c0e14713cde44f470fb8c0c02
ms.sourcegitcommit: f915d8b43a3cefe532062ca7d7dbbf569d2583d8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/05/2020
ms.locfileid: "78304314"
---
# <a name="device-roobo-smart-audio-dev-kit-v2"></a>裝置： Roobo 智慧型音訊開發工具組 v2

本文提供 Roobo 智慧型音訊開發 Kit2 的裝置特定資訊。

## <a name="set-up-the-development-kit"></a>設定開發套件

1. 開發套件有兩個 micro USB 連接器。 左側的連接器用來對開發套件供電，在下圖中會以 Power 來醒目提示。 右邊的連接器則用來控制開發套件，在圖中會標示為 Debug。 
    ![連接開發工具組](media/speech-devices-sdk/roobo-v2-connections.png)
1. 使用 micro USB 纜線將電源連接埠連接至電腦或電源配接器，以對開發套件供電。 上方面板底下會亮起綠色電源指示燈。
1. 若要控制開發工具組，請使用第二個微型 USB 纜線，將 debug 埠連接到電腦。 使用高品質的纜線以確保可靠的通訊是不可或缺的。
1. 以迴圈方式向您的開發工具組加上垂直方向，如上所示的麥克風


## <a name="development-information"></a>開發資訊

如需更多開發資訊，請參閱[Roobo 開發指南](http://dwn.roo.bo/server_upload/ddk/ROOBO%20Dev%20Kit-User%20Guide.pdf)。

## <a name="audio-recordplay"></a>音訊錄製/播放

DDK2 音訊作業可以透過下列方式執行：
* 使用 ALSA 的開放原始碼程式庫及其應用程式。
* 使用 appmainprog 介面來執行應用程式開發。 DDK2 音訊相關軟體架構使用標準的 ALSA 架構，您可以使用 libasound。 因此，直接開發軟體。 因此，您可以直接使用 ALSA 的 arecord 和 aplay 來錄製和播放音訊。
