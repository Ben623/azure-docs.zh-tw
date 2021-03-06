---
author: cynthn
ms.author: cynthn
ms.date: 01/23/2020
ms.topic: include
ms.service: virtual-machines-linux
manager: gwallace
ms.openlocfilehash: ec1b77118f94501363d950d72a65a67ece79ff77
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/24/2020
ms.locfileid: "76748802"
---
標準化的虛擬機器（VM）映射可讓組織遷移至雲端，並確保部署的一致性。 映射通常包含預先定義的安全性和設定，以及必要的軟體。 設定您自己的映射處理管線需要時間、基礎結構和設定，但使用 Azure VM 映射產生器時，只需提供簡單的設定來描述您的映射、將它提交至服務，然後建立映射並加以散發。
 
Azure VM 映射產生器（Azure 映射產生器）可讓您從以 Windows 或 Linux 為基礎的 Azure Marketplace 映射、現有的自訂映射或 Red Hat Enterprise Linux （RHEL） ISO 開始，並開始新增您自己的自訂專案。 因為映射產生器是建立在[HashiCorp Packer](https://packer.io/)上，您也可以匯入現有的 Packer shell 布建程式腳本。 您也可以在[Azure 共用映射資源庫](https://docs.microsoft.com/azure/virtual-machines/windows/shared-image-galleries)中，指定您想要裝載映射的位置，做為受控映射或 VHD。

> [!IMPORTANT]
> Azure 映射產生器目前為公開預覽版。
> 此預覽版本是在沒有服務等級協定的情況下提供，不建議用於生產工作負載。 可能不支援特定功能，或可能已經限制功能。 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="preview-features"></a>預覽功能

針對預覽版本，支援下列功能：

- 建立黃金基準影像，其中包括您的最低安全性和公司設定，並允許部門根據其需求進一步進行自訂。
- 修補現有的映射，映射產生器可讓您持續修補現有的自訂映射。
- 與 Azure 共用映射資源庫整合，可讓您全域散發、版本及調整影像，並提供映射管理系統。
- 與現有的映射組建管線整合，只需從管線呼叫影像產生器，或使用簡單的預覽影像產生器 Azure DevOps 工作。
- 將現有的映射自訂管線遷移至 Azure。 使用您現有的腳本、命令和進程來自訂映射。
- 使用 Red Hat 攜帶您自己的訂用帳戶支援。 建立 Red Hat Enterprise 映射以與合格、未使用的 Red Hat 訂用帳戶搭配使用。
- 以 VHD 格式建立映射。
 

## <a name="regions"></a>區域
Azure 映射產生器服務會在這些區域中提供預覽。 映射可以散佈在這些區域之外。
- 美國東部
- 美國東部 2
- 美國中西部
- 美國西部
- 美國西部 2

## <a name="os-support"></a>OS 支援
AIB 將支援 Azure Marketplace 基本 OS 映射：
- Ubuntu 18.04
- Ubuntu 16.04
- RHEL 7.6、7。7
- CentOS 7.6、7。7
- SLES 12 SP4
- SLES 15、SLES 15 SP1
- 適用于虛擬桌面的 Windows 10 RS5 Enterprise/Professional/Enterprise （EVD） 
- Windows 2016
- Windows 2019

AIB 將支援 RHEL ISO 的，作為的來源：
- RHEL 7.3
- RHEL 7。4
- RHEL 7.5

RHEL 7.6 Iso 不受支援，但正在進行測試。

## <a name="how-it-works"></a>運作方式


![Azure 映射產生器的概念圖](./media/virtual-machines-image-builder-overview/image-builder.png)

Azure 映射產生器是完全受控的 Azure 服務，可供 Azure 資源提供者存取。 Azure 映射產生器流程有三個主要部分：來源、自訂和散發，這些都是以範本表示。 下圖顯示元件及其部分屬性。 
 


**影像產生器進程** 

![Azure 映射產生器進程的概念繪圖](./media/virtual-machines-image-builder-overview/image-builder-process.png)

1. 建立映射範本作為 json 檔案。 此 json 檔案包含映射來源、自訂和散發的相關資訊。 [Azure 映射](https://github.com/danielsollondon/azvmimagebuilder/tree/master/quickquickstarts)產生器 GitHub 存放庫中有多個範例。
1. 將它提交給服務，這會在您指定的資源群組中建立映射範本成品。 在背景中，映射產生器會視需要下載來源映射或 ISO 和腳本。 這些會儲存在您的訂用帳戶中自動建立的個別資源群組中，格式為： IT_\<DestinationResourceGroup > _\<TemplateName >。 
1. 建立映射範本之後，您就可以建立映射。 在背景影像產生器中，會使用範本和來源檔案，在 IT_\<DestinationResourceGroup > _\<TemplateName > 資源群組中建立 VM （預設大小： Standard_D1_v2）、網路、公用 IP、NSG 和存放裝置。
1. 作為映射建立的一部分，映射產生器會根據範本散發映射，然後刪除 IT_\<DestinationResourceGroup > _\<TemplateName > 為進程建立的資源群組中的其他資源。


## <a name="permissions"></a>權限

若要允許 Azure VM 映射產生器將映射發佈至受控映射或共用映射資源庫，您必須為服務「Azure 虛擬機器映射產生器」提供「參與者」許可權（應用程式識別碼： cf32a0cc-373c-47c9-9156-0db11f6a6dfc）的資源群組。 

如果您使用現有的自訂受控映射或映射版本，則 Azure 映射產生器必須至少有這些資源群組的「讀取者」存取權。

您可以使用 Azure CLI 來指派存取權：

```azurecli-interactive
az role assignment create \
    --assignee cf32a0cc-373c-47c9-9156-0db11f6a6dfc \
    --role Contributor \
    --scope /subscriptions/$subscriptionID/resourceGroups/<distributeResoureGroupName>
```

您可以使用 PowerShell 指派存取權：

```azurePowerShell-interactive
New-AzRoleAssignment -ObjectId ef511139-6170-438e-a6e1-763dc31bdf74 -Scope /subscriptions/$subscriptionID/resourceGroups/<distributeResoureGroupName> -RoleDefinitionName Contributor
```


如果找不到服務帳戶，這可能表示您要新增角色指派的訂用帳戶尚未針對資源提供者註冊。


## <a name="costs"></a>費用
建立、建立和儲存映射時，您將會產生一些計算、網路和儲存體成本。 這些成本類似于手動建立自訂映射所產生的成本。 對於資源，您將以 Azure 費率向您收費。 

在映射建立過程中，會下載檔案並將其儲存在 `IT_<DestinationResourceGroup>_<TemplateName>` 資源群組中，這將會產生少量的儲存成本。 如果您不想要保留這些，請在映射組建之後刪除**映射範本**。
 
映射產生器會使用 D1v2 VM 大小、儲存體，以及 VM 所需的網路功能，來建立 VM。 這些資源會在建立程式期間持續，而且一旦影像產生器完成影像的建立後，就會刪除。 
 
Azure 映射產生器會將映射散發至您選擇的區域，這可能會產生網路輸出費用。
 
## <a name="next-steps"></a>後續步驟 
 
若要試用 Azure 映射產生器，請參閱建立[Linux](../articles/virtual-machines/linux/image-builder.md)或[Windows](../articles/virtual-machines/windows/image-builder.md)映射的文章。
 
 
