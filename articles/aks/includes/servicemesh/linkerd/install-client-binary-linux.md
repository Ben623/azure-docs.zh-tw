---
author: paulbouwer
ms.service: container-service
ms.topic: include
ms.date: 10/09/2019
ms.author: pabouwer
ms.openlocfilehash: 1729eabca75ed7c7a3a43ea2c0b1617efd337f2c
ms.sourcegitcommit: f29fec8ec945921cc3a89a6e7086127cc1bc1759
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/17/2019
ms.locfileid: "72530104"
---
## <a name="download-and-install-the-linkerd-linkerd-client-binary"></a>下載並安裝 Linkerd Linkerd 用戶端二進位檔

在 Linux 或[適用于 linux 的 Windows 子系統][install-wsl]上以 bash 為基礎的 shell 中，使用 `curl` 下載 Linkerd 版本，如下所示：

```bash
# Specify the Linkerd version that will be leveraged throughout these instructions
LINKERD_VERSION=stable-2.6.0

curl -sLO "https://github.com/linkerd/linkerd2/releases/download/$LINKERD_VERSION/linkerd2-cli-$LINKERD_VERSION-linux"
```

@No__t_0 用戶端二進位檔會在您的用戶端電腦上執行，並可讓您與 Linkerd 服務網格互動。 使用下列命令，在 linux 或[適用于 linux 的 Windows 子系統][install-wsl]上以 bash 為基礎的 shell 中安裝 Linkerd `linkerd` 用戶端二進位檔。 這些命令會將 `linkerd` 用戶端二進位檔複製到 `PATH` 中的標準使用者程式位置。

```bash
sudo cp ./linkerd2-cli-$LINKERD_VERSION-linux /usr/local/bin/linkerd
sudo chmod +x /usr/local/bin/linkerd
```

如果您想要讓 Linkerd `linkerd` 用戶端二進位檔的命令列完成，請依照下列方式進行設定：

```bash
# Generate the bash completion file and source it in your current shell
mkdir -p ~/completions && linkerd completion bash > ~/completions/linkerd.bash
source ~/completions/linkerd.bash

# Source the bash completion file in your .bashrc so that the command-line completions
# are permanently available in your shell
echo "source ~/completions/linkerd.bash" >> ~/.bashrc
```

<!-- LINKS - external -->
[install-wsl]: https://docs.microsoft.com/windows/wsl/install-win10