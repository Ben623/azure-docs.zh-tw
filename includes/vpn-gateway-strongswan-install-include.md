---
title: 包含檔案
description: 包含檔案
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 08/14/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: ddab6927044ce638e50ff3ad79aa3c35d046c820
ms.sourcegitcommit: 0e59368513a495af0a93a5b8855fd65ef1c44aac
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/15/2019
ms.locfileid: "69520821"
---
下列設定是針對下列步驟所使用:

  | | |
  |---|---|
  |Computer| Ubuntu Server 18.04|
  |相依性| strongSwan |


使用下列命令來安裝所需的 strongSwan 組態：

```
sudo apt install strongswan
```

```
sudo apt install strongswan-pki
```

```
sudo apt install libstrongswan-extra-plugins
```

使用下列命令來安裝 Azure 命令列介面:

```
curl -sL https://aka.ms/InstallAzureCLIDeb | sudo bash
```

[有關如何安裝 Azure CLI 的其他指示](https://docs.microsoft.com/cli/azure/install-azure-cli-apt?view=azure-cli-latest)
