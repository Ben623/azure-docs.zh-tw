---
author: paulbouwer
ms.topic: include
ms.date: 10/09/2019
ms.author: pabouwer
ms.openlocfilehash: a6b915e2735b45ac876ca98d279a15e4a371fbfd
ms.sourcegitcommit: 99ac4a0150898ce9d3c6905cbd8b3a5537dd097e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/25/2020
ms.locfileid: "77594231"
---
在 Linux 上的 bash 型 shell 中，適用于 Linux 或 MacOS 的[Windows 子系統][install-wsl]，請使用 `curl` 下載 Consul Helm 圖表版本，如下所示：

```bash
# Specify the Consul Helm chart version that will be leveraged throughout these instructions
CONSUL_HELM_VERSION=0.10.0

curl -sL "https://github.com/hashicorp/consul-helm/archive/v$CONSUL_HELM_VERSION.tar.gz" | tar xz
mv consul-helm-$CONSUL_HELM_VERSION consul-helm
```

<!-- LINKS - external -->
[install-wsl]: https://docs.microsoft.com/windows/wsl/install-win10