---
author: paulbouwer
ms.service: container-service
ms.topic: include
ms.date: 11/15/2019
ms.author: pabouwer
ms.openlocfilehash: d812baa15380b7824deaed081d2857fbc6e0b1b6
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/19/2019
ms.locfileid: "74170855"
---
## <a name="download-and-install-the-istio-istioctl-client-binary"></a>下載並安裝 Istio istioctl 用戶端二進位檔

在 MacOS 上以 bash 為基礎的 shell 中，使用 `curl` 下載 Istio 版本，然後以 `tar` 解壓縮，如下所示：

```bash
# Specify the Istio version that will be leveraged throughout these instructions
ISTIO_VERSION=1.4.0

curl -sL "https://github.com/istio/istio/releases/download/$ISTIO_VERSION/istio-$ISTIO_VERSION-osx.tar.gz" | tar xz
```

`istioctl` 用戶端二進位檔會在您的用戶端電腦上執行，並可讓您與 Istio 服務網格互動。 使用下列命令，將 Istio `istioctl` 用戶端二進位檔安裝在 MacOS 上以 bash 為基礎的 shell 中。 這些命令會將 `istioctl` 用戶端二進位檔複製到 `PATH` 中的標準使用者程式位置。

```bash
cd istio-$ISTIO_VERSION
sudo cp ./bin/istioctl /usr/local/bin/istioctl
sudo chmod +x /usr/local/bin/istioctl
```

如果您想要讓 Istio `istioctl` 用戶端二進位檔完成命令列，請進行如下所示的設定：

```bash
# Generate the bash completion file and source it in your current shell
mkdir -p ~/completions && istioctl collateral --bash -o ~/completions
source ~/completions/istioctl.bash

# Source the bash completion file in your .bashrc so that the command-line completions
# are permanently available in your shell
echo "source ~/completions/istioctl.bash" >> ~/.bashrc
```