---
title: 依 Azure 資源類型區分的移動作業支援
description: 列出可移至新資源群組或訂用帳戶的 Azure 資源類型。
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: reference
ms.date: 6/6/2019
ms.author: tomfitz
ms.openlocfilehash: 9ab8fbd8fa0453ca6c89f3e7ad91bea95b0b9096
ms.sourcegitcommit: 08138eab740c12bf68c787062b101a4333292075
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/22/2019
ms.locfileid: "67331995"
---
# <a name="move-operation-support-for-resources"></a>資源的移動作業支援
此文章列出 Azure 資源類型是否支援移動作業。 雖然資源類型支援作業，但可能會有導致無法移動資源的情況。 如需有關影響移動作業之情況的詳細資料，請參閱[將資源移動到新的資源群組或訂用帳戶](resource-group-move-resources.md)。

若要以逗號區隔值檔案的形式取得相同資料，請下載 [move-support-resources.csv](https://github.com/tfitzmac/resource-capabilities/blob/master/move-support-resources.csv)。

## <a name="microsoftaad"></a>Microsoft.AAD
| 資源類型 | 資源群組 | 訂用帳戶 |
| ------------- | ----------- | ---------- |
| domainservices | 否 | 否 |

## <a name="microsoftaadiam"></a>microsoft.aadiam
| 資源類型 | 資源群組 | 訂用帳戶 |
| ------------- | ----------- | ---------- |
| tenants | 否 | 否 |

## <a name="microsoftalertsmanagement"></a>Microsoft.AlertsManagement
| 資源類型 | 資源群組 | 訂用帳戶 |
| ------------- | ----------- | ---------- |
| actionrules | 是 | 是 |

## <a name="microsoftanalysisservices"></a>Microsoft.AnalysisServices
| 資源類型 | 資源群組 | 訂用帳戶 |
| ------------- | ----------- | ---------- |
| servers | 是 | 是 |

## <a name="microsoftapimanagement"></a>Microsoft.ApiManagement
| 資源類型 | 資源群組 | 訂用帳戶 |
| ------------- | ----------- | ---------- |
| service | 是 | 是 |

## <a name="microsoftappconfiguration"></a>Microsoft.AppConfiguration
| 資源類型 | 資源群組 | 訂用帳戶 |
| ------------- | ----------- | ---------- |
| configurationstores | 是 | 是 |

## <a name="microsoftappservice"></a>Microsoft.AppService
| 資源類型 | 資源群組 | 訂用帳戶 |
| ------------- | ----------- | ---------- |
| apiapps | 否 | 否 |
| appidentities | 否 | 否 |
| gateways | 否 | 否 |

## <a name="microsoftauthorization"></a>Microsoft.Authorization
| 資源類型 | 資源群組 | 訂用帳戶 |
| ------------- | ----------- | ---------- |
| policyassignments | 否 | 否 |

## <a name="microsoftautomation"></a>Microsoft.Automation
| 資源類型 | 資源群組 | 訂用帳戶 |
| ------------- | ----------- | ---------- |
| automationaccounts | 是 | 是 |
| automationaccounts/configurations | 是 | 是 |
| automationaccounts/runbooks | 是 | 是 |

## <a name="microsoftazureactivedirectory"></a>Microsoft.AzureActiveDirectory
| 資源類型 | 資源群組 | 訂用帳戶 |
| ------------- | ----------- | ---------- |
| b2cdirectories | 是 | 是 |

## <a name="microsoftazurestack"></a>Microsoft.AzureStack
| 資源類型 | 資源群組 | 訂用帳戶 |
| ------------- | ----------- | ---------- |
| registrations | 是 | 是 |

## <a name="microsoftbackup"></a>Microsoft.Backup
| 資源類型 | 資源群組 | 訂用帳戶 |
| ------------- | ----------- | ---------- |
| backupvault | 否 | 否 |

## <a name="microsoftbatch"></a>Microsoft.Batch
| 資源類型 | 資源群組 | 訂用帳戶 |
| ------------- | ----------- | ---------- |
| batchaccounts | 是 | 是 |

## <a name="microsoftbatchai"></a>Microsoft.BatchAI
| 資源類型 | 資源群組 | 訂用帳戶 |
| ------------- | ----------- | ---------- |
| clusters | 否 | 否 |
| fileservers | 否 | 否 |
| jobs | 否 | 否 |
| workspaces | 否 | 否 |

## <a name="microsoftbingmaps"></a>Microsoft.BingMaps
| 資源類型 | 資源群組 | 訂用帳戶 |
| ------------- | ----------- | ---------- |
| mapapis | 否 | 否 |

## <a name="microsoftbiztalkservices"></a>Microsoft.BizTalkServices
| 資源類型 | 資源群組 | 訂用帳戶 |
| ------------- | ----------- | ---------- |
| biztalk | 是 | 是 |

## <a name="microsoftblockchain"></a>Microsoft.Blockchain
| 資源類型 | 資源群組 | 訂用帳戶 |
| ------------- | ----------- | ---------- |
| blockchainmembers | 是 | 是 |

## <a name="microsoftblueprint"></a>Microsoft.Blueprint
| 資源類型 | 資源群組 | 訂用帳戶 |
| ------------- | ----------- | ---------- |
| blueprintassignments | 否 | 否 |

## <a name="microsoftbotservice"></a>Microsoft.BotService
| 資源類型 | 資源群組 | 訂用帳戶 |
| ------------- | ----------- | ---------- |
| botservices | 是 | 是 |

## <a name="microsoftcache"></a>Microsoft.Cache
| 資源類型 | 資源群組 | 訂用帳戶 |
| ------------- | ----------- | ---------- |
| redis | 是 | 是 |

## <a name="microsoftcdn"></a>Microsoft.Cdn
| 資源類型 | 資源群組 | 訂用帳戶 |
| ------------- | ----------- | ---------- |
| 設定檔 | 是 | 是 |
| profiles/endpoints | 是 | 是 |

## <a name="microsoftcertificateregistration"></a>Microsoft.CertificateRegistration
| 資源類型 | 資源群組 | 訂用帳戶 |
| ------------- | ----------- | ---------- |
| certificateorders | 是 | 是 |

## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute
| 資源類型 | 資源群組 | 訂用帳戶 |
| ------------- | ----------- | ---------- |
| domainnames | 是 | 否 |
| virtualmachines | 是 | 否 |

## <a name="microsoftclassicnetwork"></a>Microsoft.ClassicNetwork
| 資源類型 | 資源群組 | 訂用帳戶 |
| ------------- | ----------- | ---------- |
| networksecuritygroups | 否 | 否 |
| reservedips | 否 | 否 |
| virtualnetworks | 否 | 否 |

## <a name="microsoftclassicstorage"></a>Microsoft.ClassicStorage
| 資源類型 | 資源群組 | 訂用帳戶 |
| ------------- | ----------- | ---------- |
| storageaccounts | 是 | 否 |

## <a name="microsoftcognitiveservices"></a>Microsoft.CognitiveServices
| 資源類型 | 資源群組 | 訂用帳戶 |
| ------------- | ----------- | ---------- |
| accounts | 是 | 是 |

## <a name="microsoftcompute"></a>Microsoft.Compute
| 資源類型 | 資源群組 | 訂用帳戶 |
| ------------- | ----------- | ---------- |
| availabilitysets | 是 | 是 |
| disks | 是 | 是 |
| galleries | 否 | 否 |
| galleries/images | 否 | 否 |
| galleries/images/versions | 否 | 否 |
| hostgroups | 否 | 否 |
| hostgroups/主機 | 否 | 否 |
| images | 是 | 是 |
| proximityplacementgroups | 否 | 否 |
| restorepointcollections | 否 | 否 |
| sharedvmimages | 否 | 否 |
| sharedvmimages/versions | 否 | 否 |
| snapshots | 是 | 是 |
| virtualmachines | 是 | 是 |
| virtualmachines/extensions | 是 | 是 |
| virtualmachinescalesets | 是 | 是 |

## <a name="microsoftcontainer"></a>Microsoft.Container
| 資源類型 | 資源群組 | 訂用帳戶 |
| ------------- | ----------- | ---------- |
| containergroups | 否 | 否 |

## <a name="microsoftcontainerinstance"></a>Microsoft.ContainerInstance
| 資源類型 | 資源群組 | 訂用帳戶 |
| ------------- | ----------- | ---------- |
| containergroups | 否 | 否 |

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry
| 資源類型 | 資源群組 | 訂用帳戶 |
| ------------- | ----------- | ---------- |
| registries | 是 | 是 |
| registries/buildtasks | 是 | 是 |
| registries/replications | 是 | 是 |
| registries/tasks | 是 | 是 |
| registries/webhooks | 是 | 是 |

## <a name="microsoftcontainerservice"></a>Microsoft.ContainerService
| 資源類型 | 資源群組 | 訂用帳戶 |
| ------------- | ----------- | ---------- |
| containerservices | 否 | 否 |
| managedclusters | 否 | 否 |
| openshiftmanagedclusters | 否 | 否 |

## <a name="microsoftcontentmoderator"></a>Microsoft.ContentModerator
| 資源類型 | 資源群組 | 訂用帳戶 |
| ------------- | ----------- | ---------- |
| 應用程式所需 | 是 | 是 |

## <a name="microsoftcortanaanalytics"></a>Microsoft.CortanaAnalytics
| 資源類型 | 資源群組 | 訂用帳戶 |
| ------------- | ----------- | ---------- |
| accounts | 否 | 否 |

## <a name="microsoftcostmanagement"></a>Microsoft.CostManagement
| 資源類型 | 資源群組 | 訂用帳戶 |
| ------------- | ----------- | ---------- |
| 連接器 | 是 | 是 |

## <a name="microsoftcustomerinsights"></a>Microsoft.CustomerInsights
| 資源類型 | 資源群組 | 訂用帳戶 |
| ------------- | ----------- | ---------- |
| hubs | 是 | 是 |

## <a name="microsoftdatabox"></a>Microsoft.DataBox
| 資源類型 | 資源群組 | 訂用帳戶 |
| ------------- | ----------- | ---------- |
| jobs | 否 | 否 |

## <a name="microsoftdataboxedge"></a>Microsoft.DataBoxEdge
| 資源類型 | 資源群組 | 訂用帳戶 |
| ------------- | ----------- | ---------- |
| databoxedgedevices | 否 | 否 |

## <a name="microsoftdatabricks"></a>Microsoft.Databricks
| 資源類型 | 資源群組 | 訂用帳戶 |
| ------------- | ----------- | ---------- |
| workspaces | 否 | 否 |

## <a name="microsoftdatacatalog"></a>Microsoft.DataCatalog
| 資源類型 | 資源群組 | 訂用帳戶 |
| ------------- | ----------- | ---------- |
| catalogs | 是 | 是 |
| datacatalogs | 否 | 否 |

## <a name="microsoftdataconnect"></a>Microsoft.DataConnect
| 資源類型 | 資源群組 | 訂用帳戶 |
| ------------- | ----------- | ---------- |
| connectionmanagers | 否 | 否 |

## <a name="microsoftdataexchange"></a>Microsoft.DataExchange
| 資源類型 | 資源群組 | 訂用帳戶 |
| ------------- | ----------- | ---------- |
| packages | 否 | 否 |
| plans | 否 | 否 |

## <a name="microsoftdatafactory"></a>Microsoft.DataFactory
| 資源類型 | 資源群組 | 訂用帳戶 |
| ------------- | ----------- | ---------- |
| datafactories | 是 | 是 |
| factories | 是 | 是 |

## <a name="microsoftdatalake"></a>Microsoft.DataLake
| 資源類型 | 資源群組 | 訂用帳戶 |
| ------------- | ----------- | ---------- |
| datalakeaccounts | 否 | 否 |

## <a name="microsoftdatalakeanalytics"></a>Microsoft.DataLakeAnalytics
| 資源類型 | 資源群組 | 訂用帳戶 |
| ------------- | ----------- | ---------- |
| accounts | 是 | 是 |

## <a name="microsoftdatalakestore"></a>Microsoft.DataLakeStore
| 資源類型 | 資源群組 | 訂用帳戶 |
| ------------- | ----------- | ---------- |
| accounts | 是 | 是 |

## <a name="microsoftdatamigration"></a>Microsoft.DataMigration
| 資源類型 | 資源群組 | 訂用帳戶 |
| ------------- | ----------- | ---------- |
| 服務 | 否 | 否 |
| services/projects | 否 | 否 |
| slots | 否 | 否 |

## <a name="microsoftdbformariadb"></a>Microsoft.DBforMariaDB
| 資源類型 | 資源群組 | 訂用帳戶 |
| ------------- | ----------- | ---------- |
| servers | 是 | 是 |

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL
| 資源類型 | 資源群組 | 訂用帳戶 |
| ------------- | ----------- | ---------- |
| servers | 是 | 是 |

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL
| 資源類型 | 資源群組 | 訂用帳戶 |
| ------------- | ----------- | ---------- |
| servergroups | 否 | 否 |
| servers | 是 | 是 |
| serversv2 | 是 | 是 |

## <a name="microsoftdeploymentmanager"></a>Microsoft.DeploymentManager
| 資源類型 | 資源群組 | 訂用帳戶 |
| ------------- | ----------- | ---------- |
| artifactsources | 是 | 是 |
| rollouts | 是 | 是 |
| servicetopologies | 是 | 是 |
| servicetopologies/services | 是 | 是 |
| servicetopologies/services/serviceunits | 是 | 是 |
| steps | 是 | 是 |

## <a name="microsoftdevices"></a>Microsoft.Devices
| 資源類型 | 資源群組 | 訂用帳戶 |
| ------------- | ----------- | ---------- |
| elasticpools | 否 | 否 |
| elasticpools/iothubtenants | 否 | 否 |
| iothubs | 是 | 是 |
| provisioningservices | 是 | 是 |

## <a name="microsoftdevspaces"></a>Microsoft.DevSpaces
| 資源類型 | 資源群組 | 訂用帳戶 |
| ------------- | ----------- | ---------- |
| controllers | 否 | 否 |

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab
| 資源類型 | 資源群組 | 訂用帳戶 |
| ------------- | ----------- | ---------- |
| labcenters | 否 | 否 |
| labs | 是 | 否 |
| 實驗室/環境 | 是 | 是 |
| labs/servicerunners | 是 | 是 |
| labs/virtualmachines | 是 | 否 |
| schedules | 是 | 是 |

## <a name="microsoftdns"></a>microsoft.dns
| 資源類型 | 資源群組 | 訂用帳戶 |
| ------------- | ----------- | ---------- |
| dnszones | 否 | 否 |
| dnszones/a | 否 | 否 |
| dnszones/aaaa | 否 | 否 |
| dnszones/cname | 否 | 否 |
| dnszones/mx | 否 | 否 |
| dnszones/ptr | 否 | 否 |
| dnszones/srv | 否 | 否 |
| dnszones/txt | 否 | 否 |
| trafficmanagerprofiles | 否 | 否 |

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB
| 資源類型 | 資源群組 | 訂用帳戶 |
| ------------- | ----------- | ---------- |
| databaseaccounts | 是 | 是 |

## <a name="microsoftdomainregistration"></a>Microsoft.DomainRegistration
| 資源類型 | 資源群組 | 訂用帳戶 |
| ------------- | ----------- | ---------- |
| domains | 是 | 是 |

## <a name="microsoftenterpriseknowledgegraph"></a>Microsoft.EnterpriseKnowledgeGraph
| 資源類型 | 資源群組 | 訂用帳戶 |
| ------------- | ----------- | ---------- |
| 服務 | 是 | 是 |

## <a name="microsofteventgrid"></a>Microsoft.EventGrid
| 資源類型 | 資源群組 | 訂用帳戶 |
| ------------- | ----------- | ---------- |
| domains | 是 | 是 |
| topics | 是 | 是 |

## <a name="microsofteventhub"></a>Microsoft.EventHub
| 資源類型 | 資源群組 | 訂用帳戶 |
| ------------- | ----------- | ---------- |
| clusters | 是 | 是 |
| namespaces | 是 | 是 |

## <a name="microsoftgenomics"></a>Microsoft.Genomics
| 資源類型 | 資源群組 | 訂用帳戶 |
| ------------- | ----------- | ---------- |
| accounts | 否 | 否 |

## <a name="microsofthanaonazure"></a>Microsoft.HanaOnAzure
| 資源類型 | 資源群組 | 訂用帳戶 |
| ------------- | ----------- | ---------- |
| hanainstances | 是 | 是 |

## <a name="microsofthdinsight"></a>Microsoft.HDInsight
| 資源類型 | 資源群組 | 訂用帳戶 |
| ------------- | ----------- | ---------- |
| clusters | 是 | 是 |

## <a name="microsofthealthcareapis"></a>Microsoft.HealthcareApis
| 資源類型 | 資源群組 | 訂用帳戶 |
| ------------- | ----------- | ---------- |
| 服務 | 是 | 是 |

## <a name="microsofthybridcompute"></a>Microsoft.HybridCompute
| 資源類型 | 資源群組 | 訂用帳戶 |
| ------------- | ----------- | ---------- |
| 機器 | 否 | 否 |

## <a name="microsofthybriddata"></a>Microsoft.HybridData
| 資源類型 | 資源群組 | 訂用帳戶 |
| ------------- | ----------- | ---------- |
| datamanagers | 是 | 是 |

## <a name="microsoftimportexport"></a>Microsoft.ImportExport
| 資源類型 | 資源群組 | 訂用帳戶 |
| ------------- | ----------- | ---------- |
| jobs | 是 | 是 |

## <a name="microsoftinsights"></a>microsoft.insights
| 資源類型 | 資源群組 | 訂用帳戶 |
| ------------- | ----------- | ---------- |
| accounts | 否 | 否 |
| actiongroups | 是 | 是 |
| activitylogalerts | 否 | 否 |
| alertrules | 是 | 是 |
| autoscalesettings | 是 | 是 |
| components | 是 | 是 |
| guestdiagnosticsettings | 否 | 否 |
| metricalerts | 否 | 否 |
| notificationgroups | 否 | 否 |
| notificationrules | 否 | 否 |
| scheduledqueryrules | 是 | 是 |
| webtests | 是 | 是 |
| workbooks | 是 | 是 |

## <a name="microsoftiotcentral"></a>Microsoft.IoTCentral
| 資源類型 | 資源群組 | 訂用帳戶 |
| ------------- | ----------- | ---------- |
| iotapps | 是 | 是 |

## <a name="microsoftiotspaces"></a>Microsoft.IoTSpaces
| 資源類型 | 資源群組 | 訂用帳戶 |
| ------------- | ----------- | ---------- |
| checknameavailability | 是 | 是 |
| graph | 是 | 是 |

## <a name="microsoftkeyvault"></a>Microsoft.KeyVault
| 資源類型 | 資源群組 | 訂用帳戶 |
| ------------- | ----------- | ---------- |
| hsmpools | 否 | 否 |
| vaults | 是 | 是 |

## <a name="microsoftkusto"></a>Microsoft.Kusto
| 資源類型 | 資源群組 | 訂用帳戶 |
| ------------- | ----------- | ---------- |
| clusters | 是 | 是 |

## <a name="microsoftlabservices"></a>Microsoft.LabServices
| 資源類型 | 資源群組 | 訂用帳戶 |
| ------------- | ----------- | ---------- |
| labaccounts | 是 | 是 |

## <a name="microsoftlocationbasedservices"></a>Microsoft.LocationBasedServices
| 資源類型 | 資源群組 | 訂用帳戶 |
| ------------- | ----------- | ---------- |
| accounts | 是 | 是 |

## <a name="microsoftlocationservices"></a>Microsoft.LocationServices
| 資源類型 | 資源群組 | 訂用帳戶 |
| ------------- | ----------- | ---------- |
| accounts | 否 | 否 |

## <a name="microsoftlogic"></a>Microsoft.Logic
| 資源類型 | 資源群組 | 訂用帳戶 |
| ------------- | ----------- | ---------- |
| hostingenvironments | 否 | 否 |
| integrationaccounts | 是 | 是 |
| integrationserviceenvironments | 否 | 否 |
| isolatedenvironments | 否 | 否 |
| workflows | 是 | 是 |

## <a name="microsoftmachinelearning"></a>Microsoft.MachineLearning
| 資源類型 | 資源群組 | 訂用帳戶 |
| ------------- | ----------- | ---------- |
| commitmentplans | 是 | 是 |
| webservices | 是 | 否 |
| workspaces | 是 | 是 |

## <a name="microsoftmachinelearningcompute"></a>Microsoft.MachineLearningCompute
| 資源類型 | 資源群組 | 訂用帳戶 |
| ------------- | ----------- | ---------- |
| operationalizationclusters | 是 | 是 |

## <a name="microsoftmachinelearningexperimentation"></a>Microsoft.MachineLearningExperimentation
| 資源類型 | 資源群組 | 訂用帳戶 |
| ------------- | ----------- | ---------- |
| accounts | 否 | 否 |
| accounts/workspaces | 否 | 否 |
| accounts/workspaces/projects | 否 | 否 |
| teamaccounts | 否 | 否 |
| teamaccounts/workspaces | 否 | 否 |
| teamaccounts/workspaces/projects | 否 | 否 |

## <a name="microsoftmachinelearningmodelmanagement"></a>Microsoft.MachineLearningModelManagement
| 資源類型 | 資源群組 | 訂用帳戶 |
| ------------- | ----------- | ---------- |
| accounts | 是 | 是 |

## <a name="microsoftmachinelearningoperationalization"></a>Microsoft.MachineLearningOperationalization
| 資源類型 | 資源群組 | 訂用帳戶 |
| ------------- | ----------- | ---------- |
| hostingaccounts | 否 | 否 |

## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices
| 資源類型 | 資源群組 | 訂用帳戶 |
| ------------- | ----------- | ---------- |
| workspaces | 否 | 否 |

## <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity
| 資源類型 | 資源群組 | 訂用帳戶 |
| ------------- | ----------- | ---------- |
| userassignedidentities | 否 | 否 |

## <a name="microsoftmaps"></a>Microsoft.Maps
| 資源類型 | 資源群組 | 訂用帳戶 |
| ------------- | ----------- | ---------- |
| accounts | 是 | 是 |

## <a name="microsoftmarketplaceapps"></a>Microsoft.MarketplaceApps
| 資源類型 | 資源群組 | 訂用帳戶 |
| ------------- | ----------- | ---------- |
| classicdevservices | 否 | 否 |

## <a name="microsoftmedia"></a>Microsoft.Media
| 資源類型 | 資源群組 | 訂用帳戶 |
| ------------- | ----------- | ---------- |
| mediaservices | 是 | 是 |
| mediaservices/liveevents | 是 | 是 |
| mediaservices/streamingendpoints | 是 | 是 |

## <a name="microsoftmigrate"></a>Microsoft.Migrate
| 資源類型 | 資源群組 | 訂用帳戶 |
| ------------- | ----------- | ---------- |
| assessmentprojects | 否 | 否 |
| migrateprojects | 否 | 否 |
| projects | 否 | 否 |

## <a name="microsoftnetapp"></a>Microsoft.NetApp
| 資源類型 | 資源群組 | 訂用帳戶 |
| ------------- | ----------- | ---------- |
| netappaccounts | 否 | 否 |
| netappaccounts/capacitypools | 否 | 否 |
| netappaccounts/capacitypools/volumes | 否 | 否 |
| netappaccounts/capacitypools/volumes/mounttargets | 否 | 否 |
| netappaccounts/capacitypools/volumes/snapshots | 否 | 否 |

## <a name="microsoftnetwork"></a>Microsoft.Network
| 資源類型 | 資源群組 | 訂用帳戶 |
| ------------- | ----------- | ---------- |
| applicationgateways | 否 | 否 |
| applicationgatewaywebapplicationfirewallpolicies | 否 | 否 |
| applicationsecuritygroups | 是 | 是 |
| azurefirewalls | 是 | 是 |
| bastionhosts | 否 | 否 |
| connections | 是 | 是 |
| ddoscustompolicies | 是 | 是 |
| ddosprotectionplans | 否 | 否 |
| dnszones | 是 | 是 |
| expressroutecircuits | 否 | 否 |
| expressroutecrossconnections | 否 | 否 |
| expressroutegateways | 否 | 否 |
| expressrouteports | 否 | 否 |
| frontdoors | 否 | 否 |
| frontdoorwebapplicationfirewallpolicies | 否 | 否 |
| loadbalancers | 是 | 是 |
| localnetworkgateways | 是 | 是 |
| natgateways | 是 | 是 |
| networkintentpolicies | 是 | 是 |
| networkinterfaces | 是 | 是 |
| networkprofiles | 否 | 否 |
| networksecuritygroups | 是 | 是 |
| networkwatchers | 是 | 是 |
| networkwatchers/connectionmonitors | 是 | 是 |
| networkwatchers/lenses | 是 | 是 |
| networkwatchers/pingmeshes | 是 | 是 |
| p2svpngateways | 否 | 否 |
| privatednszones | 是 | 是 |
| privatednszones/virtualnetworklinks | 是 | 是 |
| privateendpoints | 否 | 否 |
| privatelinkservices | 否 | 否 |
| publicipaddresses | 是 | 是 |
| publicipprefixes | 是 | 是 |
| routefilters | 否 | 否 |
| routetables | 是 | 是 |
| securegateways | 是 | 是 |
| serviceendpointpolicies | 是 | 是 |
| trafficmanagerprofiles | 是 | 是 |
| virtualhubs | 否 | 否 |
| virtualnetworkgateways | 是 | 是 |
| virtualnetworks | 是 | 是 |
| virtualnetworktaps | 否 | 否 |
| virtualwans | 否 | 否 |
| vpngateways | 否 | 否 |
| vpnsites | 否 | 否 |
| webapplicationfirewallpolicies | 是 | 是 |

## <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs
| 資源類型 | 資源群組 | 訂用帳戶 |
| ------------- | ----------- | ---------- |
| namespaces | 是 | 是 |
| namespaces/notificationhubs | 是 | 是 |

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights
| 資源類型 | 資源群組 | 訂用帳戶 |
| ------------- | ----------- | ---------- |
| workspaces | 是 | 是 |

## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement
| 資源類型 | 資源群組 | 訂用帳戶 |
| ------------- | ----------- | ---------- |
| managementconfigurations | 是 | 是 |
| solutions | 是 | 是 |
| 檢視 | 是 | 是 |

## <a name="microsoftpeering"></a>Microsoft.Peering
| 資源類型 | 資源群組 | 訂用帳戶 |
| ------------- | ----------- | ---------- |
| 對等互連 | 否 | 否 |

## <a name="microsoftportal"></a>Microsoft.Portal
| 資源類型 | 資源群組 | 訂用帳戶 |
| ------------- | ----------- | ---------- |
| dashboards | 是 | 是 |

## <a name="microsoftportalsdk"></a>Microsoft.PortalSdk
| 資源類型 | 資源群組 | 訂用帳戶 |
| ------------- | ----------- | ---------- |
| rootresources | 否 | 否 |

## <a name="microsoftpowerbi"></a>Microsoft.PowerBI
| 資源類型 | 資源群組 | 訂用帳戶 |
| ------------- | ----------- | ---------- |
| workspacecollections | 是 | 是 |

## <a name="microsoftpowerbidedicated"></a>Microsoft.PowerBIDedicated
| 資源類型 | 資源群組 | 訂用帳戶 |
| ------------- | ----------- | ---------- |
| capacities | 是 | 是 |

## <a name="microsoftprojectoxford"></a>Microsoft.ProjectOxford
| 資源類型 | 資源群組 | 訂用帳戶 |
| ------------- | ----------- | ---------- |
| accounts | 否 | 否 |

## <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices
| 資源類型 | 資源群組 | 訂用帳戶 |
| ------------- | ----------- | ---------- |
| vaults | 是 | 是 |

## <a name="microsoftrelay"></a>Microsoft.Relay
| 資源類型 | 資源群組 | 訂用帳戶 |
| ------------- | ----------- | ---------- |
| namespaces | 是 | 是 |

## <a name="microsoftsaas"></a>Microsoft.SaaS
| 資源類型 | 資源群組 | 訂用帳戶 |
| ------------- | ----------- | ---------- |
| 應用程式所需 | 是 | 否 |

## <a name="microsoftscheduler"></a>Microsoft.Scheduler
| 資源類型 | 資源群組 | 訂用帳戶 |
| ------------- | ----------- | ---------- |
| flows | 是 | 是 |
| jobcollections | 是 | 是 |

## <a name="microsoftsearch"></a>Microsoft.Search
| 資源類型 | 資源群組 | 訂用帳戶 |
| ------------- | ----------- | ---------- |
| searchservices | 是 | 是 |

## <a name="microsoftsecurity"></a>Microsoft.Security
| 資源類型 | 資源群組 | 訂用帳戶 |
| ------------- | ----------- | ---------- |
| iotsecuritysolutions | 是 | 是 |

## <a name="microsoftservermanagement"></a>Microsoft.ServerManagement
| 資源類型 | 資源群組 | 訂用帳戶 |
| ------------- | ----------- | ---------- |
| gateways | 否 | 否 |
| nodes | 否 | 否 |

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus
| 資源類型 | 資源群組 | 訂用帳戶 |
| ------------- | ----------- | ---------- |
| namespaces | 是 | 是 |

## <a name="microsoftservicefabric"></a>Microsoft.ServiceFabric
| 資源類型 | 資源群組 | 訂用帳戶 |
| ------------- | ----------- | ---------- |
| 應用程式所需 | 否 | 否 |
| clusters | 是 | 是 |
| containergroups | 否 | 否 |
| containergroupsets | 否 | 否 |
| edgeclusters | 否 | 否 |
| networks | 否 | 否 |
| secretstores | 否 | 否 |
| 磁碟區 | 否 | 否 |

## <a name="microsoftservicefabricmesh"></a>Microsoft.ServiceFabricMesh
| 資源類型 | 資源群組 | 訂用帳戶 |
| ------------- | ----------- | ---------- |
| 應用程式所需 | 是 | 是 |
| containergroups | 否 | 否 |
| gateways | 是 | 是 |
| networks | 是 | 是 |
| 密碼 | 是 | 是 |
| 磁碟區 | 是 | 是 |

## <a name="microsoftsignalrservice"></a>Microsoft.SignalRService
| 資源類型 | 資源群組 | 訂用帳戶 |
| ------------- | ----------- | ---------- |
| signalr | 是 | 是 |

## <a name="microsoftsiterecovery"></a>Microsoft.SiteRecovery
| 資源類型 | 資源群組 | 訂用帳戶 |
| ------------- | ----------- | ---------- |
| siterecoveryvault | 否 | 否 |

## <a name="microsoftsolutions"></a>Microsoft.Solutions
| 資源類型 | 資源群組 | 訂用帳戶 |
| ------------- | ----------- | ---------- |
| appliancedefinitions | 否 | 否 |
| appliances | 否 | 否 |
| applicationdefinitions | 否 | 否 |
| 應用程式所需 | 否 | 否 |
| jitrequests | 否 | 否 |

## <a name="microsoftsql"></a>Microsoft.Sql
| 資源類型 | 資源群組 | 訂用帳戶 |
| ------------- | ----------- | ---------- |
| instancepools | 是 | 是 |
| managedinstances | 是 | 是 |
| managedinstances/databases | 是 | 是 |
| servers | 是 | 是 |
| servers/databases | 是 | 是 |
| servers/elasticpools | 是 | 是 |
| virtualclusters | 是 | 是 |

## <a name="microsoftsqlvirtualmachine"></a>Microsoft.SqlVirtualMachine
| 資源類型 | 資源群組 | 訂用帳戶 |
| ------------- | ----------- | ---------- |
| sqlvirtualmachinegroups | 是 | 是 |
| sqlvirtualmachines | 是 | 是 |

## <a name="microsoftsqlvm"></a>Microsoft.SqlVM
| 資源類型 | 資源群組 | 訂用帳戶 |
| ------------- | ----------- | ---------- |
| dwvm | 否 | 否 |

## <a name="microsoftstorage"></a>Microsoft.Storage
| 資源類型 | 資源群組 | 訂用帳戶 |
| ------------- | ----------- | ---------- |
| storageaccounts | 是 | 是 |

## <a name="microsoftstoragecache"></a>Microsoft.StorageCache
| 資源類型 | 資源群組 | 訂用帳戶 |
| ------------- | ----------- | ---------- |
| 快取 | 否 | 否 |

## <a name="microsoftstoragesync"></a>Microsoft.StorageSync
| 資源類型 | 資源群組 | 訂用帳戶 |
| ------------- | ----------- | ---------- |
| storagesyncservices | 是 | 是 |

## <a name="microsoftstoragesyncdev"></a>Microsoft.StorageSyncDev
| 資源類型 | 資源群組 | 訂用帳戶 |
| ------------- | ----------- | ---------- |
| storagesyncservices | 否 | 否 |

## <a name="microsoftstoragesyncint"></a>Microsoft.StorageSyncInt
| 資源類型 | 資源群組 | 訂用帳戶 |
| ------------- | ----------- | ---------- |
| storagesyncservices | 否 | 否 |

## <a name="microsoftstorsimple"></a>Microsoft.StorSimple
| 資源類型 | 資源群組 | 訂用帳戶 |
| ------------- | ----------- | ---------- |
| managers | 否 | 否 |

## <a name="microsoftstreamanalytics"></a>Microsoft.StreamAnalytics
| 資源類型 | 資源群組 | 訂用帳戶 |
| ------------- | ----------- | ---------- |
| streamingjobs | 是 | 是 |

## <a name="microsoftstreamanalyticsexplorer"></a>Microsoft.StreamAnalyticsExplorer
| 資源類型 | 資源群組 | 訂用帳戶 |
| ------------- | ----------- | ---------- |
| environments | 否 | 否 |
| environments/eventsources | 否 | 否 |
| 執行個體 | 否 | 否 |
| instances/environments | 否 | 否 |
| instances/environments/eventsources | 否 | 否 |

## <a name="microsoftterraformoss"></a>Microsoft.TerraformOSS
| 資源類型 | 資源群組 | 訂用帳戶 |
| ------------- | ----------- | ---------- |
| providerregistrations | 否 | 否 |
| resources | 否 | 否 |

## <a name="microsofttimeseriesinsights"></a>Microsoft.TimeSeriesInsights
| 資源類型 | 資源群組 | 訂用帳戶 |
| ------------- | ----------- | ---------- |
| environments | 是 | 是 |
| environments/eventsources | 是 | 是 |
| environments/referencedatasets | 是 | 是 |

## <a name="microsofttoken"></a>Microsoft.Token
| 資源類型 | 資源群組 | 訂用帳戶 |
| ------------- | ----------- | ---------- |
| 存放區 | 否 | 否 |

## <a name="microsoftvirtualmachineimages"></a>Microsoft.VirtualMachineImages
| 資源類型 | 資源群組 | 訂用帳戶 |
| ------------- | ----------- | ---------- |
| imagetemplates | 否 | 否 |

## <a name="microsoftvisualstudio"></a>microsoft.visualstudio
| 資源類型 | 資源群組 | 訂用帳戶 |
| ------------- | ----------- | ---------- |
| 帳戶 | 是 | 是 |
| account/extension | 是 | 是 |
| account/project | 是 | 是 |

## <a name="microsoftvmwarecloudsimple"></a>Microsoft.VMwareCloudSimple
| 資源類型 | 資源群組 | 訂用帳戶 |
| ------------- | ----------- | ---------- |
| dedicatedcloudnodes | 是 | 是 |
| dedicatedcloudservices | 是 | 是 |
| virtualmachines | 是 | 是 |

## <a name="microsoftweb"></a>Microsoft.Web
| 資源類型 | 資源群組 | 訂用帳戶 |
| ------------- | ----------- | ---------- |
| certificates | 否 | 是 |
| connectiongateways | 是 | 是 |
| connections | 是 | 是 |
| customapis | 是 | 是 |
| hostingenvironments | 否 | 否 |
| serverfarms | 是 | 是 |
| sites | 是 | 是 |
| sites/premieraddons | 是 | 是 |
| sites/slots | 是 | 是 |

## <a name="microsoftwindowsiot"></a>Microsoft.WindowsIoT
| 資源類型 | 資源群組 | 訂用帳戶 |
| ------------- | ----------- | ---------- |
| deviceservices | 否 | 否 |

## <a name="microsoftwindowsvirtualdesktop"></a>Microsoft.WindowsVirtualDesktop
| 資源類型 | 資源群組 | 訂用帳戶 |
| ------------- | ----------- | ---------- |
| applicationgroups | 否 | 否 |
| hostpools | 否 | 否 |
| workspaces | 否 | 否 |

## <a name="third-party-services"></a>協力廠商服務

協力廠商服務目前不支援移動作業。

## <a name="next-steps"></a>後續步驟
如需用來移動資源的命令，請參閱[將資源移動到新的資源群組或訂用帳戶](resource-group-move-resources.md)。
