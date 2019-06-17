---
title: 包含檔案
description: 包含檔案
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 11/04/2018
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: d25efa8c666fa91c7bd652a7cf931ea6ca379aa2
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "67133596"
---
區域備援儲存體 (ZRS) 會以同步方式，將您的資料複寫到單一區域中的三個儲存體叢集。 每個儲存體叢集實際上都與其他叢集分隔且位在自己的可用性區域 (AZ) 中。 每個可用性區域&mdash;及其中的 ZRS 叢集&mdash;都是自發的，且包含個別的工具和網路功能。 將資料寫入三個叢集之間的所有複本時，才會成功傳回 ZRS 儲存體帳戶的寫入要求。

當您使用 ZRS 複寫將資料儲存在儲存體帳戶中時，如果有可用性區域變得無法使用，您仍然可以繼續存取和管理您的資料。 ZRS 的效能優異且延遲性低。 ZRS 提供與[本地備援儲存體 (LRS)](../articles/storage/common/storage-redundancy-lrs.md) 相同的[延展性目標](../articles/storage/common/storage-scalability-targets.md)。

針對需要一致性、耐久性和高可用性的案例，請考慮使用 ZRS。 在給定的一年中，即使發生中斷或自然災害使可用性區域變得無法取得，ZRS 仍提供至少 99.9999999999% (12 個 9) 的儲存體物件耐久性。

如需可用性區域的詳細資訊，請參閱[可用性區域概觀](https://docs.microsoft.com/azure/availability-zones/az-overview)。