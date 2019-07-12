---
title: Azure 中的 Red Hat Enterprise Linux 映像 | Microsoft Docs
description: 了解 Microsoft Azure 中的 Red Hat Enterprise Linux 映像
services: virtual-machines-linux
documentationcenter: ''
author: BorisB2015
manager: gwallace
editor: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 6/6/2019
ms.author: borisb
ms.openlocfilehash: f7ae82b0376489e21b35e4e94dce32805bea69c6
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/09/2019
ms.locfileid: "67708390"
---
# <a name="red-hat-enterprise-linux-images-in-azure"></a>Azure 中的 Red Hat Enterprise Linux 映像
本文說明 Azure Marketplace 中所提供的 Red Hat Enterprise Linux (RHEL) 映像，以及與其命名和保留期相關的原則。

如需所有 RHEL 版本的 Red Hat 支援原則資訊，請參閱 [Red Hat Enterprise Linux 生命週期](https://access.redhat.com/support/policy/updates/errata)頁面。

>[!Important]
> 目前可在 Azure Marketplace 中取得的 RHEL 映像支援自備訂用帳戶 (BYOS) 或隨用隨付 (PAYG) 授權模型。 不支援 [Azure Hybrid Use Benefit](../windows/hybrid-use-benefit-licensing.md)和 BYOS 與 PAYG 之間的動態切換。 切換授權模式時，需要從對應的映像重新部署 VM。

>[!Note]
> 對於 Azure Marketplace 資源庫中 RHEL 映像的任何相關問題，請向 Microsoft 提出支援票證。

## <a name="images-available-in-the-ui"></a>UI 中的可用映像
當您在 Marketplace 中搜尋 “Red Hat”，或是在 Azure 入口網站 UI 中建立資源時，您將會看到可用 RHEL 映像的子集，以及相關的 Red Hat 產品。 您隨時可以使用 Azure CLI/PowerShell/API 取得可用 VM 映像的完整集合。

若要查看 Azure 中可用 Red Hat 映像的完整集合，請執行下列命令

```azurecli-interactive
az vm image list --publisher RedHat --all
```

## <a name="naming-convention"></a>命名慣例
Azure 中的 VM 映像是依發行者、供應項目、SKU 及版本進行組織。 「發行者:供應項目:SKU:版本」的組合便是映像 URN，並能唯一識別要使用的映像。

例如，`RedHat:RHEL:7-RAW:7.6.2018103108` 代表建置於 2018 年 10 月 31 日的 RHEL 7.6 未經處理分割映像。

下面顯示如何建立 RHEL 7.6 VM 的範例。
```azurecli-interactive
az vm create --name RhelVM --resource-group TestRG --image RedHat:RHEL:7-RAW:7.6.2018103108 --no-wait
```

### <a name="the-latest-moniker"></a>"Latest" (最新) Moniker
Azure REST API 允許不使用特定的版本，而是使用 "latest" (最新) 的 Moniker 作為版本。 使用 "latest" 將會佈建針對指定發行者、供應項目和 SKU 的最新可用映像。

例如，`RedHat:RHEL:7-RAW:latest` 代表可用的最新 RHEL 7 系列未經處理分割映像。

```azurecli-interactive
az vm create --name RhelVM --resource-group TestRG --image RedHat:RHEL:7-RAW:latest --no-wait
```

>[!NOTE]
> 一般而言，比較版本以決定最新版本的程序會遵循 [CompareTo 方法](https://msdn.microsoft.com/library/a5ts8tb6.aspx)來進行。

### <a name="current-naming-convention"></a>目前的命名慣例
所有目前已發行的 RHEL 映像都是使用隨用隨付模型，並連線至 [Azure 中的 Red Hat Update Infrastructure (RHUI)](https://aka.ms/rhui-update)。 新的命名慣例已被採用 RHEL 7 系列映像所在的磁碟資料分割配置 (未經處理、 LVM) 而不是版本的 SKU 中指定。 RHEL 映像版本將會包含 7 原始或 7 LVM。 RHEL 6 系列的命名目前尚未變更。

會有 2 種類型的 RHEL 7 映像 Sku 中此命名慣例：Sku 列出的次要版本和不要的 Sku。 如果您想要使用原始 7] 或 [7-LVM SKU，您可以指定您想要部署的版本中的 RHEL 次要版本。 如果您選擇的 「 最新 」 版本，您就能佈建 RHEL 的最新次要版本。

>[!NOTE]
> 在適用於 SAP 的 RHEL 映像集中，RHEL 版本會維持固定。 因此，其命名慣例會包含 SKU 的特定版本。

>[!NOTE]
> RHEL 6 映像集並未移至新的命名慣例。

## <a name="extended-update-support-eus"></a>擴充的更新支援 (EUS)
年 4 月 2019年的 RHEL 映像可用時，會附加至擴充更新支援 (EUS) 存放庫預設。 中有更多有關 RHEL EUS [Red Hat 的文件](https://access.redhat.com/articles/rhel-eus)。

有提供有關如何切換您的 VM EUS 和 EUS 支援生命週期結束日期的更多詳細的指示[此處](https://aka.ms/rhui-update#rhel-eus-and-version-locking-rhel-vms)。

>[!NOTE]
> EUS 不支援 RHEL 額外項目。 這表示，如果您要安裝的套件，通常可從 RHEL Extras 通道，您將無法再進行此作業在 EUS 上。 Red Hat Extras 產品生命週期中有詳細說明[此處](https://access.redhat.com/support/policy/updates/extras/)。

### <a name="for-customers-that-want-to-use-eus-images"></a>針對想要使用 EUS 映像的客戶：
想要使用附加至 EUS 存放庫的映像的客戶應該使用包含在 SKU 中的 RHEL 次要版本號碼將 RHEL 映像。 這些映像會是未經處理資料分割 (也就不是 LVM)。

例如，您可能會看到下列 2 個 RHEL 7.4 可用的映像：
```bash
RedHat:RHEL:7-RAW:7.4.2018010506
RedHat:RHEL:7.4:7.4.2019041718
```
在此情況下，`RedHat:RHEL:7.4:7.4.2019041718`會附加到 EUS 存放庫，根據預設，和`RedHat:RHEL:7-RAW:7.4.2018010506`會附加到非 EUS 存放庫，根據預設。

### <a name="for-customers-that-dont-want-to-use-eus-images"></a>針對不想要使用 EUS 映像的客戶：
如果您不想使用預設連接到 EUS 映像，部署的映像，不包含在 SKU 中的次要版本號碼。

#### <a name="rhel-images-with-eus"></a>EUS RHEL 映像
下表將適用於 RHEL 映像包含在 SKU 中的次要版本。

>[!NOTE]
> 在撰寫本文時，只有 RHEL 7.4 和更新版本的次要版本有 EUS 支援。 EUS 已不再支援適用於 RHEL < = 7.3。

次要版本 |EUS 影像範例              |EUS 狀態                                                   |
:-------------|:------------------------------|:------------------------------------------------------------|
RHEL 7.4      |RedHat:RHEL:7.4:7.4.2019041718 | 映像發佈 2019 年 4 月和更新版本將會是預設 EUS|
RHEL 7.5      |RedHat:RHEL:7.5:7.5.2019060305 | 映像發佈 2019 年 6 月和更新版本將會是預設 EUS |
RHEL 7.6      |RedHat:RHEL:7.6:7.6.2019052206 | 映像發佈 2019 年和更新版本將會是預設 EUS  |
RHEL 8.0      |N/A                            | 沒有 EUS 目前映像目前可用                 |


## <a name="list-of-rhel-images-available"></a>可用的 RHEL 映像清單
下列供應項目為目前可供一般使用的 SKU：

供應項目| SKU | 分割 | 佈建 | 注意
:----|:----|:-------------|:-------------|:-----
RHEL          | 7-RAW    | RAW    | Linux 代理程式 | RHEL 7 系列的映像。 <br> 未附加到 EUS 存放庫的預設值。
|             | 7-LVM    | LVM    | Linux 代理程式 | RHEL 7 系列的映像。 <br> 未附加到 EUS 存放庫的預設值。
|             | 7-RAW-CI | RAW-CI | Cloud-init  | RHEL 7 系列的映像。 <br> 未附加到 EUS 存放庫的預設值。
|             | 6.7      | RAW    | Linux 代理程式 | RHEL 6.7 映像，舊版命名慣例
|             | 6.8      | RAW    | Linux 代理程式 | RHEL 6.8 與上方相同
|             | 6.9      | RAW    | Linux 代理程式 | RHEL 6.9 與上方相同
|             | 6.10     | RAW    | Linux 代理程式 | RHEL 6.10 與上方相同
|             | 7.2      | RAW    | Linux 代理程式 | RHEL 7.2 與上方相同
|             | 7.3      | RAW    | Linux 代理程式 | RHEL 7.3 與上方相同
|             | 7.4      | RAW    | Linux 代理程式 | 適用於 RHEL 7.4 與以上所述相同。 <br> 根據預設，自 2019 年 4 月起附加 EUS 存放庫
|             | 7.5      | RAW    | Linux 代理程式 | 適用於 RHEL 7.5 與以上所述相同。 <br> 根據預設，自 2019 年 6 月起附加 EUS 存放庫
|             | 7.6      | RAW    | Linux 代理程式 | 適用於 RHEL 7.6 與以上所述相同。 <br> 根據預設，自 2019 年起附加 EUS 存放庫
RHEL-SAP      | 7.4      | LVM    | Linux 代理程式 | 適用於 SAP HANA 和商務應用程式的 RHEL 7.4
|             | 7.5      | LVM    | Linux 代理程式 | 適用於 SAP HANA 和商務應用程式的 RHEL 7.5
RHEL-SAP-HANA | 6.7      | RAW    | Linux 代理程式 | 適用於 SAP HANA 的 RHEL 6.7
|             | 7.2      | LVM    | Linux 代理程式 | 適用於 SAP HANA 的 RHEL 7.2
|             | 7.3      | LVM    | Linux 代理程式 | 適用於 SAP HANA 的 RHEL 7.3
RHEL-SAP-APPS | 6.8      | RAW    | Linux 代理程式 | 適用於 SAP 商務應用程式的 RHEL 6.8
|             | 7.3      | LVM    | Linux 代理程式 | 適用於 SAP 商務應用程式的 RHEL 7.3

### <a name="old-naming-convention"></a>舊版命名慣例
在上述的命名慣例變更之前，RHEL 7 系列映像和 RHEL 6 系列映像都是使用其 SKU 的特定版本。

您將能在完整映像清單中找到數值 SKU。 Microsoft 和 Red Hat 將會在推出新的次要版本時建立新的數值 SKU。

### <a name="other-available-offers-and-skus"></a>其他可用供應項目和 SKU
可用供應項目和 SKU 的完整清單可能會包含上述表格所列內容以外的其他映像，例如 `RedHat:rhel-ocp-marketplace:rhel74:7.4.1`。 這些供應項目可能是用來為特定 Marketplace 解決方案提供支援，也可能是基於預覽和測試目的而發行。 它們隨時都有可能在不發出警告的情況下被變更或移除。 除非它們的存在已由 Microsoft 或 Red Hat 公開記錄，否則請不要使用它們。

## <a name="publishing-policy"></a>發行原則
Microsoft 和 Red Hat 會在發行新的次要版本時更新映像以解決特定的 CVE，或是進行偶爾的設定變更/更新。 我們致力於以最快的速度提供已更新的映像，也就是在 CVE 修正發行或可供使用後的三個工作天之內。

我們只會更新特定映像系列中的最新次要版本。 在發行較新的次要版本之後，我們將會停止更新較舊的次要版本。 例如，在發行 RHEL 7.6 之後，便不會再次更新 RHEL 7.5 映像。

>[!NOTE]
> 從 RHEL 隨用隨付映像佈建的作用中 Azure VM 會連線至 Azure RHUI，並可以在 Red Hat 發行更新和修正，並將它們複寫至 Azure RHUI (這通常會在 Red Hat 正式發行後的 24 小時內發生) 後立即接收到它們。 這些 VM 不需要新的已發行映像以取得更新，且客戶能完全控制該更新的起始時機。

## <a name="image-retention-policy"></a>映像保留原則
我們目前的原則是保留先前所發行的所有映像。 我們保留移除已知會造成任何類型問題之映像的權利。 例如，我們可能會移除因後續平台或元件更新而具有不正確設定的映像。 可能會被移除的映像將會遵循目前的 Marketplace 原則，並在移除映像之前，於最多 30 天前提供通知。

## <a name="next-steps"></a>後續步驟
* 請[在這裡](https://aka.ms/rhui-update)了解 Azure Red Hat Update Infrastructure。
* 如需所有 RHEL 版本的 Red Hat 支援原則資訊，請參閱 [Red Hat Enterprise Linux 生命週期](https://access.redhat.com/support/policy/updates/errata)頁面。
