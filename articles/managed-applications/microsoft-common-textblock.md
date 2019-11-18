---
title: Azure TextBlock UI 元素 | Microsoft Docs
description: 描述 Azure 入口網站的 Microsoft.Common.TextBlock UI 元素。 用來將文字加入至介面。
services: managed-applications
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.service: managed-applications
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/27/2018
ms.author: tomfitz
ms.openlocfilehash: 1dbef1e43d9f970665a31f0e4e9270d3193edaba
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/17/2019
ms.locfileid: "74151727"
---
# <a name="microsoftcommontextblock-ui-element"></a>Microsoft.Common.TextBlock UI 元素

可用來將文字新增至入口網站介面的控制項。

## <a name="ui-sample"></a>UI 範例

![Microsoft.Common.TextBox](./media/managed-application-elements/microsoft.common.textblock.png)

## <a name="schema"></a>結構描述

```json
{
  "name": "text1",
  "type": "Microsoft.Common.TextBlock",
  "visible": true,
  "options": {
    "text": "Please provide the configuration values for your application.",
    "link": {
      "label": "Learn more",
      "uri": "https://www.microsoft.com"
    }
  }
}
```

## <a name="sample-output"></a>範例輸出

```json
"Please provide the configuration values for your application. Learn more"
```

## <a name="next-steps"></a>後續步驟

* 如需建立 UI 定義的簡介，請參閱[開始使用 CreateUiDefinition](create-uidefinition-overview.md)。
* 如需 UI 元素中通用屬性的說明，請參閱 [CreateUiDefinition 元素](create-uidefinition-elements.md)。
