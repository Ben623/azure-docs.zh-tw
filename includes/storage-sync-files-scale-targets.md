---
title: 包含檔案
description: 包含檔案
services: storage
author: wmgries
ms.service: storage
ms.topic: include
ms.date: 05/05/2019
ms.author: wgries
ms.custom: include file
ms.openlocfilehash: 2614c9290bf31813d59ee753a31622bccf0682b8
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "66114539"
---
| 資源 | 目標 | 固定限制 |
|----------|--------------|------------|
| 每個區域的儲存體同步服務數目 | 20 的儲存體同步服務 | 是 |
| 每個儲存體同步服務的同步群組 | 100 個同步群組 | 是 |
| 每個儲存體同步服務的已註冊伺服器 | 99 部伺服器 | 是 |
| 每個同步群組的雲端端點 | 1 個雲端端點 | 是 |
| 每個同步群組的伺服器端點 | 50 個伺服器端點 | 否 |
| 每部伺服器的伺服器端點 | 30 個伺服器端點 | 是 |
| 每個同步群組的檔案系統物件 (目錄和檔案) | 2500 萬個物件 | 否 |
| 目錄中的檔案系統物件 (目錄和檔案) 數目上限 | 1 百萬個物件 | 是 |
| 物件 (目錄和檔案) 安全性描述元大小上限 | 64 KiB | 是 |
| 檔案大小 | 100 GiB | 否 |
| 要分層之檔案的檔案大小下限 | 64 KiB | 是 |
| 並行同步處理工作階段 | V4 代理程式和更新版本：可用系統資源限制而有所不同。 <BR> V3 代理程式：每個處理器或最多八個作用中的同步處理工作階段，每一部伺服器的兩個使用中的同步處理工作階段。 | 是

> [!Note]  
> Azure 檔案共用的大小，Azure 檔案同步端點可以相應增加。 如果達到 Azure 檔案共用大小限制時，同步處理將會無法運作。