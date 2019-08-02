---
title: Azure 應用程式解決方案範本供應專案發行指南 |Azure Marketplace
description: 本文說明在 Azure Marketplace 中發佈解決方案範本的需求。
services: Azure, Marketplace, Compute, Storage, Networking, Blockchain, Security
author: ellacroi
manager: nunoc
ms.service: marketplace
ms.topic: article
ms.date: 11/15/2018
ms.author: ellacroi
ms.openlocfilehash: 8d02d942fce7bd51a116cc4c19eac9faca0060ef
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/26/2019
ms.locfileid: "68561485"
---
# <a name="azure-applications-solution-template-offer-publishing-guide"></a>Azure 應用程式：解決方案範本供應項目發佈指南

解決方案範本是在 Marketplace 中發佈解決方案的其中一個主要方法。 您可以使用本指南來了解這項供應項目的需求。 

當您的解決方案除了單一 VM 之外，還需要以自動化方式進行額外的部署和設定時，請使用「Azure 應用程式：解決方案範本」供應項目類型。 您可以使用「Azure 應用程式：解決方案範本」來自動佈建一或多部 VM。 您也可以佈建網路和儲存體資源。 「Azure 應用程式：解決方案範本」供應項目類型可為單一 VM 和整個 IaaS 型解決方案提供自動化的優點。

這些解決方案範本是交易供應項目，會透過 Marketplace 來部署並計費。 使用者會看到的呼籲行動是「立即取得」。


## <a name="requirements-for-solution-templates"></a>解決方案範本的需求

| **需求** | **詳細資料**  |
| ---------------  | -----------  |
|計費和計量    |  資源會在客戶的 Azure 訂用帳戶中加以佈建。 隨用隨付 (PAYGO) 虛擬機器將透過 Microsoft 與客戶交易，並透過客戶的 Azure 訂用帳戶 (PAYGO) 計費。  <br/> 在自備授權 (BYOL) 的案例中，雖然 Microsoft 會向客戶訂用帳戶中產生的基礎結構成本收費，但您將直接向客戶收取軟體授權費用。   |
|Azure 相容的虛擬硬碟 (VHD)  |   VM 必須建置在 Windows 或 Linux 上。  如需詳細資訊，請參閱[建立與 Azure 相容的 VHD](./cloud-partner-portal/virtual-machine/cpp-create-vhd.md)。 |
| 客戶使用狀況屬性 | 在所有發佈至 Azure Marketplace 的解決方案範本上，都必須啟用客戶使用狀況屬性。 如需客戶使用狀況屬性及其啟用方式的詳細資訊，請參閱 [Azure 合作夥伴客戶使用狀況屬性](./azure-partner-customer-usage-attribution.md)。  |
| 使用受控磁碟 | [受控磁碟](https://docs.microsoft.com/azure/virtual-machines/windows/managed-disks-overview)是 Azure 中 IaaS vm 的持續性磁片的預設選項。 您必須使用方案範本中的受控磁碟。 <br> <br> 1.遵循在 Azure ARM 範本中使用受控磁碟的[指引](https://docs.microsoft.com/azure/virtual-machines/windows/using-managed-disks-template-deployments)和[範例](https://github.com/Azure/azure-quickstart-templates/blob/master/managed-disk-support-list.md), 以更新您的解決方案範本。 <br> <br> 2.請依照下列指示將受控磁碟的基礎 VHD 匯入到儲存體帳戶, 以將 VHD 發佈為 Marketplace 中的映射: <br> <ul> <li> [PowerShell](https://docs.microsoft.com/azure/virtual-machines/scripts/virtual-machines-windows-powershell-sample-copy-managed-disks-vhd?toc=%2fpowershell%2fmodule%2ftoc.json) </li> <li> [CLI](https://docs.microsoft.com/azure/virtual-machines/scripts/virtual-machines-linux-cli-sample-copy-managed-disks-vhd?toc=%2fcli%2fmodule%2ftoc.json) </li> </ul> |

## <a name="next-steps"></a>後續步驟
如果您還未註冊，請在 Marketplace 中[註冊](https://azuremarketplace.microsoft.com/sell)。

如果您已註冊且要建立新的供應項目或使用現有供應項目，請登入 [Cloud Partner 入口網站](https://cloudpartner.azure.com)以建立或完成您的供應項目。
