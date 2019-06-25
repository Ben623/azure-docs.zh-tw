---
title: 了解如何建立 Azure 受控應用程式的 UI 定義 | Microsoft Docs
description: 描述如何建立 Azure 受控應用程式的 UI 定義
services: managed-applications
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.service: managed-applications
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/26/2019
ms.author: tomfitz
ms.openlocfilehash: 3d0a6d97440404904c041369a4631fdd3fb618b4
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "66257555"
---
# <a name="create-azure-portal-user-interface-for-your-managed-application"></a>為您的受控應用程式建立 Azure 入口網站使用者介面
本文件介紹 createUiDefinition.json 檔案的核心概念。 Azure 入口網站會使用這個檔案，產生用來建立受控應用程式的使用者介面。

```json
{
   "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
   "handler": "Microsoft.Compute.MultiVm",
   "version": "0.1.2-preview",
   "parameters": {
      "basics": [ ],
      "steps": [ ],
      "outputs": { }
   }
}
```

CreateUiDefinition 一律會包含三個屬性︰ 

* 處理常式
* 版本
* parameters

針對受控應用程式，處理常式應該一律為 `Microsoft.Compute.MultiVm`，而最新的支援版本是 `0.1.2-preview`。

parameters 屬性的結構描述取決於指定的處理常式和版本之組合。 針對受控應用程式，支援的屬性為 `basics`、`steps` 和 `outputs`。 basics 和 steps屬性包含要在 Azure 入口網站中顯示的_元素_ - 如同文字方塊和下拉式清單。 outputs 屬性可用來將指定元素的輸出值對應至 Azure Resource Manager 部署範本的參數。

建議包含 `$schema`，但是為選用。 如果指定，`version` 的值必須符合 `$schema` URI 內的版本。

您可以使用 JSON 編輯器來建立 UI 定義，或您可以使用 UI 定義沙箱，以建立並預覽其中的 UI 定義。 如需有關沙箱的詳細資訊，請參閱[Azure 受控應用程式中測試您的入口網站介面](test-createuidefinition.md)。

## <a name="basics"></a>基本概念
當 Azure 入口網站剖析檔案時，所產生之精靈的第一個步驟一律為「基本」步驟。 除了顯示 `basics` 中指定的元素之外，入口網站會插入元素，以供使用者選擇部署的訂用帳戶、資源群組和位置。 一般而言，查詢整個部署參數的元素 (例如叢集的名稱或管理員認證) 都應在此步驟中。

如果元素的行為取決於使用者的訂用帳戶、資源群組或位置，就不能在 basics 中使用該元素。 例如，**Microsoft.Compute.SizeSelector** 取決於使用者的訂用帳戶和位置，來判斷可用大小的清單。 因此，**Microsoft.Compute.SizeSelector** 只能用於步驟中。 一般而言，basics 中只能使用 **Microsoft.Common** 命名空間中的元素。 儘管其他命名空間中的某些元素 (例如 **Microsoft.Compute.Credentials**) 並非取決於使用者的內容，但仍可允許使用。

## <a name="steps"></a>步驟
steps 屬性可以包含零個或多個要在 basics 之後顯示的額外步驟，其中都各包含一個或多個元素。 請考慮針對每個角色或要進行部署的應用程式層新增步驟。 例如，針對主要節點的輸入新增一個步驟，以及針對叢集中的背景工作節點新增一個步驟。

## <a name="outputs"></a>輸出
Azure 入口網站會使用 `outputs` 屬性，將 `basics` 和 `steps` 的屬性對應至 Azure Resource Manager 部署範本的參數。 這個字典的金鑰是範本參數的名稱，而值則是所參照元素的輸出物件之屬性。

若要設定受控應用程式資源名稱，您必須在輸出屬性中包含名為 `applicationResourceName` 的值。 如果您未設定此值，應用程式會指派名稱的 GUID。 您可以在使用者介面中包含文字方塊，向使用者要求名稱。

```json
"outputs": {
    "vmName": "[steps('appSettings').vmName]",
    "trialOrProduction": "[steps('appSettings').trialOrProd]",
    "userName": "[steps('vmCredentials').adminUsername]",
    "pwd": "[steps('vmCredentials').vmPwd.password]",
    "applicationResourceName": "[steps('appSettings').vmName]"
}
```

## <a name="functions"></a>函式
類似於範本函式在 Azure Resource Manager （包括語法和功能），CreateUiDefinition 提供函式使用項目的輸入和輸出以及條件等功能。

## <a name="next-steps"></a>後續步驟
createUiDefinition.json 檔案本身有簡單的結構描述。 它的實際深度來自於所有支援的元素和函式。 這些項目會在以下位置更詳細地描述：

- [元素](create-uidefinition-elements.md)
- [函式](create-uidefinition-functions.md)

下列位置會提供 createUiDefinition 的目前 JSON 結構描述： https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json 。

如需使用者介面檔案範例，請參閱 [createUiDefinition.json](https://github.com/Azure/azure-managedapp-samples/blob/master/samples/201-managed-app-using-existing-vnet/createUiDefinition.json)。
