---
title: 使用 Azure 入口網站來建立和加密 Linux VM
description: 在本快速入門中，您會了解如何使用 Azure 入口網站來建立和加密 Linux 虛擬機器
author: msmbaldwin
ms.author: mbaldwin
ms.service: virtual-machines-linux
ms.subservice: security
ms.topic: quickstart
ms.date: 10/02/2019
ms.openlocfilehash: 9f09f28dc63e7f061946a66beb59bd4c62be70aa
ms.sourcegitcommit: 5f39f60c4ae33b20156529a765b8f8c04f181143
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/10/2020
ms.locfileid: "78970565"
---
# <a name="quickstart-create-and-encrypt-a-virtual-machine-with-the-azure-portal"></a>快速入門：使用 Azure 入口網站建立和加密 Linux 虛擬機器

您可以透過 Azure 入口網站建立 Azure 虛擬機器 (VM)。 Azure 入口網站是以瀏覽器為基礎的使用者介面，可用來建立 VM 及其相關聯的資源。 本快速入門會示範如何使用 Azure 入口網站部署執行 Ubuntu 18.04 LTS 的 Linux 虛擬機器 (VM)、建立用來儲存加密金鑰的金鑰保存庫以及加密 VM。

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="sign-in-to-azure"></a>登入 Azure

登入 [Azure 入口網站](https://portal.azure.com)。

## <a name="create-a-virtual-machine"></a>建立虛擬機器

1. 選擇 Azure 入口網站左上角的 [建立資源]  。
1. 在 [新增] 頁面中，選取 [熱門] 底下的 [Ubuntu Server 18.04 LTS]  。
1. 在 [基本]  索引標籤中的 [專案詳細資料]  底下，確認已選取正確的訂用帳戶。
1. 針對 [資源群組]  ，選取您先前製作金鑰保存庫時所建立的資源群組 (例如 **myResourceGroup**)。
1. 針對 [虛擬機器名稱]  ，輸入 *MyVM*。
1. 針對 [區域]  ，選取您先前製作金鑰保存庫所使用的相同區域 (例如，**美國東部**)。
1. 確認 [大小]  為「標準 D2s v3」  。
1. 在 [Administrator 帳戶]  底下，選取 [密碼]  。 輸入使用者名稱和密碼。
    ![ResourceGroup 建立畫面](./media/disk-encryption/portal-qs-vm-creation.png)
1. 選取 [管理] 索引標籤，並確認您有診斷儲存體帳戶。 如果您沒有儲存體帳戶，請選取 [新建]，為您的新帳戶指定名稱，然後選取 [確定] ![ResourceGroup 建立畫面](./media/disk-encryption/portal-qs-vm-creation-storage.png)
1. 按一下 [檢閱 + 建立]。
1. 在 [建立虛擬機器]  頁面上，您可以看到即將建立的 VM 詳細資料。 準備完成後，請選取 [建立]  。

可能需要幾分鐘的時間才能部署好 VM。 部署完成時，請前往下一節。

## <a name="encrypt-the-virtual-machine"></a>將虛擬機器加密

1. VM 部署完成後，請選取 [移至資源]  。
1. 在左側邊欄中，選取 [磁碟]  。
1. 在 [磁碟] 畫面上，選取 [加密]  。 

    ![磁碟和加密選取項目](../media/disk-encryption/portal-qs-disks-to-encryption.png)

1. 在加密畫面的 [要加密的磁碟]  底下，選擇 [OS 和資料磁碟]  。
1. 在 [加密設定]  底下，選擇 [選取金鑰保存庫與金鑰以進行加密]  。
1. 在 [從 Azure Key Vault 選取金鑰]  畫面上，選取 [新建]  。

    ![磁碟和加密選取項目](../media/disk-encryption/portal-qs-keyvault-create.png)

1. 在 [建立金鑰保存庫]  畫面上，確定資源群組與您用來建立 VM 的相同。
1. 為您的金鑰保存庫提供名稱。  Azure 中的每個金鑰保存庫都必須有唯一的名稱。
1. 在 [存取原則]  索引標籤上，勾選 [用於磁碟區加密的 Azure 磁碟加密]  方塊。

    ![磁碟和加密選取項目](../media/disk-encryption/portal-qs-keyvault-enable.png)

1. 選取 [檢閱 + 建立]  。  
1. 在金鑰保存庫通過驗證後，選取 [建立]  。 此時您會回到 [從 Azure Key Vault 中選取金鑰]  畫面。
1. 將 [金鑰]  欄位保留為空白，然後選擇 [選取]  。
1. 在加密畫面頂端，按一下 [儲存]  。 快顯視窗會警告您 VM 將會重新開機。 按一下 [是]  。

## <a name="clean-up-resources"></a>清除資源

若不再需要，您可以刪除資源群組、虛擬機器和所有相關資源。 若要這樣做，請選取虛擬機器的資源群組，選取 [刪除]，然後確認要刪除的資源群組名稱。

## <a name="next-steps"></a>後續步驟

在本快速入門中，您已建立為加密金鑰啟用的 Key Vault、建立虛擬機器，並為虛擬機器啟用加密。  

> [!div class="nextstepaction"]
> [Azure 磁碟加密概觀](disk-encryption-overview.md)
