---
title: Azure Service Fabric 監視合作夥伴 | Microsoft Docs
description: 了解如何使用合作夥伴監視解決方案監視 Azure Service Fabric
services: service-fabric
documentationcenter: .net
author: srrengar
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 10/16/2018
ms.author: srrengar
ms.openlocfilehash: c2f953c98e41291951f07556bd0cd441d2793d1d
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "60946587"
---
# <a name="azure-service-fabric-monitoring-partners"></a>Azure Service Fabric 監視合作夥伴

本文說明使用者如何使用少量合作夥伴解決方案，監視其 Service Fabric 應用程式、叢集和基礎結構。 我們已經與下列每個合作夥伴合作，建立適用於 Service Fabric 的整合式供應項目。

## <a name="dynatrace"></a>Dynatrace

我們與 Dynatrace 整合之後，可以提供許多現成的功能來監視 Service Fabric 叢集。 在 VMSS 執行個體上安裝 Dynatrace OneAgent 可為您提供效能計數器，並將 Service Fabric 的拓撲向下部署至應用程式層級。 Dynatrace 也是內部部署監視的絕佳選擇。 查看更多所列的功能[公告](https://www.dynatrace.com/news/blog/automatic-end-to-end-service-fabric-monitoring-with-dynatrace/)並[指示](https://www.dynatrace.com/news/blog/automatic-end-to-end-service-fabric-monitoring-with-dynatrace/)到您的叢集上啟用 Dynatrace。 

## <a name="datadog"></a>Datadog

Datadog 針對 Windows 和 Linux 執行個體，提供適用於 VMSS 的擴充功能。 您可以使用 Datadog 收集 Windows 事件記錄，並藉此收集 Windows 上的 Service Fabric 平台事件。 請在[這裡](https://www.datadoghq.com/blog/azure-monitoring-enhancements/#integrate-with-azure-service-fabric)查看如何將診斷資料傳送至 Datadog 的指示。

## <a name="appdynamics"></a>AppDynamics

Service Fabric 與 AppDynamics 是在應用程式層級整合的。 您可以更新環境變數，並使用 App Dynamics NuGet，將應用程式遙測傳送到 AppDynamics。 請參閱這些[指示](https://docs.appdynamics.com/display/AZURE/Install+AppDynamics+for+Azure+Service+Fabric)，以了解如何整合 .NET Service Fabric 應用程式與 AppDynamics。

## <a name="new-relic"></a>New Relic

New Relic 是另一個應用程式效能管理工具，可充分整合 Service Fabric 應用程式。 您可以安裝 New Relic NuGet 封裝，並在您的資訊清單檔案中新增特定的環境變數，以便將應用程式遙測傳送至 New Relic。 請查看這些[指示](https://docs.newrelic.com/docs/agents/net-agent/azure-installation/install-net-agent-azure-service-fabric)，以便為 .NET Service Fabric 應用程式啟用 New Relic 遙測。

## <a name="elk"></a>ELK 

ELK 堆疊是一系列開放原始碼技術：Elasticsearch、Logstash 和 Kibana。 您可以組合使用這些技術來收集、儲存及分析 Service Fabric 監視和診斷資料。 我們在[這裡](service-fabric-tutorial-java-elk.md)有如何使用 Service Fabric 原生 Java 應用程式執行此動作的教學課程。 

## <a name="humio"></a>Humio

Humio 是可從您的應用程式和從 Service Fabric 在雲端或內部部署即時的事件收集記錄檔的記錄檔收集服務。 除了即時的可檢視性，Humio 會提供先進的分析和視覺效果功能，可檢視，並從您的診斷收集深入解析。 Humio 有符合成本效益的定價方案，並內建至小數位數，同時保留它是閃電快的速度。 它直接與 Service Fabric 平台事件和應用程式遙測整合。 您可以深入了解 Humio 和 Service Fabric 的整合[此處](https://github.com/humio/service-fabric-humio)。

## <a name="next-steps"></a>後續步驟

* [了解 Service Fabric 中的監視與診斷](service-fabric-diagnostics-overview.md)
* 了解如何使用我們第一方工具[診斷常見案例](service-fabric-diagnostics-common-scenarios.md)
