---
title: 將 Azure API 管理服務部署到多個 Azure 區域 | Microsoft Docs
description: 瞭解如何將 Azure API 管理服務執行個體部署到多個 Azure 區域。
services: api-management
documentationcenter: ''
author: mikebudzynski
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/04/2019
ms.author: apimpm
ms.openlocfilehash: d22da92355616c208c7616b4b0e8c26b7f9e7006
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "60658130"
---
# <a name="how-to-deploy-an-azure-api-management-service-instance-to-multiple-azure-regions"></a>如何將 Azure API 管理服務執行個體部署到多個 Azure 區域

Azure API 管理支援多區域部署，可讓 API 發行者跨所需的任意數量 Azure 區域發佈單一 Azure API 管理服務。 這有助於降低地理上分散的 API 取用者感受到的要求延遲，並且可以改善某個區域離線時服務的可用性。

新的 Azure API 管理服務一開始只會在單一 Azure 區域 (主要區域) 中包含一個[單位][unit]。 您可以透過 Azure 入口網站輕鬆新增其他區域。 API 管理閘道 」 伺服器會部署到每個區域，並呼叫流量會路由到延遲方面最接近的閘道。 如果區域離線，流量會自動重新導向到下一個最近的閘道。

> [!NOTE]
> Azure API 管理只可跨區域複寫 API 閘道元件。 服務管理元件只裝載在主要區域中。 如果主要區域發生中斷狀況，則不可能將組態變更套用至 Azure API 管理服務執行個體 (包括設定或原則更新)。

[!INCLUDE [premium.md](../../includes/api-management-availability-premium.md)]

## <a name="add-region"> </a>部署 API 管理服務執行個體到新區域

> [!NOTE]
> 如果您尚未建立 API 管理服務執行個體，請參閱[建立 API 管理服務執行個體][Create an API Management service instance]。

在 Azure 入口網站中，瀏覽至「API 管理」服務執行個體的 [級別與價格]  頁面。 

![調整索引標籤][api-management-scale-service]

若要部署新區域，請從工具列中按一下 [+加入區域]  。

![加入區域][api-management-add-region]

從下拉式清單中選取位置，然後使用滑桿設定單位數量。

![指定單位][api-management-select-location-units]

按一下 [加入]  以將您的選項放入 [位置] 表格中。 

重複此程序，直到設定好所有位置為止，然後從工具列中按一下 [儲存]  來開始部署程序。

## <a name="remove-region"> </a>從區域中刪除 API 管理服務執行個體

在 Azure 入口網站中，瀏覽至「API 管理」服務執行個體的 [級別與價格]  頁面。 

![調整索引標籤][api-management-scale-service]

針對您想要移除的位置，使用表格最右邊的 [...]  按鈕來開啟操作功能表。 選取 [刪除]  選項。

確認刪除，然後按一下 [儲存]  來套用變更。

## <a name="route-backend"> </a>將 API 呼叫路由至區域後端服務

Azure API 管理只有一個後端服務 URL。 即使不同區域中有多個 Azure API 管理執行個體，API 閘道仍會將要求轉送到只部署在單一區域中的相同後端服務。 在此案例中，效能的提升只在於將回應快取到要求專屬區域中的 Azure API 管理內，但在連絡全球各地的後端時，可能仍會造成高延遲。

若要完全利用您系統的地理分佈，您應該將後端服務部署在 Azure API 管理執行個體所在的區域中。 然後，藉由使用原則和 `@(context.Deployment.Region)` 屬性，您可以將流量路由至您後端的本機執行個體。

1. 瀏覽至您的 Azure API 管理執行個體，然後按一下左側功能表中的 [API]  。
2. 選取想要的 API。
3. 從 [輸入處理]  中的箭號下拉式清單中，按一下 [程式碼編輯器]  。

    ![API 程式碼編輯器](./media/api-management-howto-deploy-multi-region/api-management-api-code-editor.png)

4. 使用 `set-backend` 結合條件式 `choose` 原則，在檔案的 `<inbound> </inbound>` 區段中建構適當的路由原則。

    例如，下列 XML 檔案將用於美國西部和東亞區域：

    ```xml
    <policies>
        <inbound>
            <base />
            <choose>
                <when condition="@("West US".Equals(context.Deployment.Region, StringComparison.OrdinalIgnoreCase))">
                    <set-backend-service base-url="http://contoso-us.com/" />
                </when>
                <when condition="@("East Asia".Equals(context.Deployment.Region, StringComparison.OrdinalIgnoreCase))">
                    <set-backend-service base-url="http://contoso-asia.com/" />
                </when>
                <otherwise>
                    <set-backend-service base-url="http://contoso-other.com/" />
                </otherwise>
            </choose>
        </inbound>
        <backend>
            <base />
        </backend>
        <outbound>
            <base />
        </outbound>
        <on-error>
            <base />
        </on-error>
    </policies>
    ```

> [!TIP]
> 您也可能前端與後端服務[Azure 流量管理員](https://azure.microsoft.com/services/traffic-manager/)，請直接 API 呼叫至流量管理員中，並讓它解決自動路由。

## <a name="custom-routing"> </a>使用 API 管理區域閘道的自訂路由

API 管理會將要求路由到區域性*閘道*根據[最低延遲](../traffic-manager/traffic-manager-routing-methods.md#performance)。 雖然您不可以覆寫此設定，在 API 管理中的，您可以使用流量管理員自訂的路由規則。

1. 建立您自己[Azure 流量管理員](https://azure.microsoft.com/services/traffic-manager/)。
1. 如果您使用自訂網域時，[使用它使用流量管理員](../traffic-manager/traffic-manager-point-internet-domain.md)而不是 API 管理服務。
1. [流量管理員中設定 API 管理區域端點](../traffic-manager/traffic-manager-manage-endpoints.md)。 區域的端點，請依照下列的 URL 模式`https://<service-name>-<region>-01.regional.azure-api.net`，例如`https://contoso-westus2-01.regional.azure-api.net`。
1. [流量管理員中設定 API 管理區域狀態的端點](../traffic-manager/traffic-manager-monitoring.md)。 區域狀態的端點，請依照下列的 URL 模式`https://<service-name>-<region>-01.regional.azure-api.net/status-0123456789abcdef`，例如`https://contoso-westus2-01.regional.azure-api.net/status-0123456789abcdef`。
1. 指定[路由方法](../traffic-manager/traffic-manager-routing-methods.md)的流量管理員。


[api-management-management-console]: ./media/api-management-howto-deploy-multi-region/api-management-management-console.png

[api-management-scale-service]: ./media/api-management-howto-deploy-multi-region/api-management-scale-service.png
[api-management-add-region]: ./media/api-management-howto-deploy-multi-region/api-management-add-region.png
[api-management-select-location-units]: ./media/api-management-howto-deploy-multi-region/api-management-select-location-units.png
[api-management-remove-region]: ./media/api-management-howto-deploy-multi-region/api-management-remove-region.png

[Create an API Management service instance]: get-started-create-service-instance.md
[Get started with Azure API Management]: get-started-create-service-instance.md

[Deploy an API Management service instance to a new region]: #add-region
[Delete an API Management service instance from a region]: #remove-region

[unit]: https://azure.microsoft.com/pricing/details/api-management/
[Premium]: https://azure.microsoft.com/pricing/details/api-management/
