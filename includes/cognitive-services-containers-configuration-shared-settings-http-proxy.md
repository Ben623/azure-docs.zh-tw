---
author: IEvangelist
ms.author: dapine
ms.date: 06/25/2019
ms.service: cognitive-services
ms.topic: include
ms.openlocfilehash: 664cea26f910fa5b3354e2879a33de50eb13a7f3
ms.sourcegitcommit: a6873b710ca07eb956d45596d4ec2c1d5dc57353
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/16/2019
ms.locfileid: "68286224"
---
如果您需要設定 HTTP Proxy 以進行輸出要求，請使用以下兩個引數：

| 名稱 | 資料類型 | 描述 |
|--|--|--|
|HTTP_PROXY|string|要使用的 Proxy，例如 `http://proxy:8888`<br>`<proxy-url>`|
|HTTP_PROXY_CREDS|string|對 Proxy 進行驗證時所需的任何認證，例如 username:password。|
|`<proxy-user>`|string|Proxy 的使用者。|
|`proxy-password`|string|對於 Proxy 與 `<proxy-user>` 相關聯的密碼。|
||||


```bash
docker run --rm -it -p 5000:5000 \
--memory 2g --cpus 1 \
--mount type=bind,src=/home/azureuser/output,target=/output \
<registry-location>/<image-name> \
Eula=accept \
Billing=<billing-endpoint> \
ApiKey=<api-key> \
HTTP_PROXY=<proxy-url> \
HTTP_PROXY_CREDS=<proxy-user>:<proxy-password> \
```
