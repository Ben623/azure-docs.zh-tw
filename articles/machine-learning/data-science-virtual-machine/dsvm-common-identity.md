---
title: 設定通用身分識別
titleSuffix: Azure Data Science Virtual Machine
description: 了解如何建立可跨多個資料科學虛擬機器使用的一般使用者帳戶。 您可以使用 Azure Active Directory 或內部部署 Active Directory，對資料科學虛擬機器驗證使用者。
keywords: 深度學習, 資料科學工具, 資料科學虛擬機器, 地理空間分析, 小組資料科學流程
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: vijetajo
ms.author: vijetaj
ms.topic: conceptual
ms.date: 05/08/2018
ms.openlocfilehash: 44f1f7ae3b290e1dbf01877f3881e1d95a238446
ms.sourcegitcommit: 5f67772dac6a402bbaa8eb261f653a34b8672c3a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/01/2019
ms.locfileid: "70208139"
---
# <a name="set-up-a-common-identity-on-a-data-science-virtual-machine"></a>在資料科學虛擬機器上設定通用身分識別

在 Microsoft Azure 的虛擬機器 (VM) 上, 包括資料科學虛擬機器 (DSVM), 您會在布建 VM 時建立本機使用者帳戶。 接著，使用者便可使用這些認證向 VM 進行驗證。 如果您有多個使用者需要存取的 Vm, 管理認證可能會非常繁瑣。 絕佳的解決方案是透過以標準為基礎的身分識別提供者來部署一般使用者帳戶和管理。 透過這種方法, 您可以使用一組認證來存取 Azure 上的多個資源, 包括多個 Dsvm。

Active Directory 是熱門的身分識別提供者, 且在 Azure 上支援作為雲端服務和內部部署目錄。 您可以使用 Azure Active Directory (Azure AD) 或內部部署 Active Directory，在獨立的 DSVM 上或 Azure 虛擬機器擴展集內的 DSVM 叢集上驗證使用者。 做法是將 DSVM 執行個體加入 Active Directory 網域。

如果您已經有 Active Directory, 您可以使用它作為通用身分識別提供者。 如果您沒有 Active Directory, 您可以透過[Azure Active Directory Domain Services](https://docs.microsoft.com/azure/active-directory-domain-services/) (Azure AD DS) 在 Azure 上執行受控 Active Directory 實例。

[Azure AD](https://docs.microsoft.com/azure/active-directory/)的檔提供詳細的[管理指示](https://docs.microsoft.com/azure/active-directory/choose-hybrid-identity-solution), 包括將 Azure AD 連接到您的內部部署目錄 (如果有的話) 的指引。

本文說明如何使用 Azure AD DS, 在 Azure 上設定完全受控的 Active Directory 網域服務。 接著, 您可以將 Dsvm 加入受控 Active Directory 網域。 這種方法可讓使用者透過一般使用者帳戶和認證, 存取 Dsvm (和其他 Azure 資源) 的集區。

## <a name="set-up-a-fully-managed-active-directory-domain-on-azure"></a>在 Azure 上設定完全受控的 Active Directory 網域

Azure AD DS 可透過在 Azure 上提供完全受控的服務，讓您輕鬆地管理您的身分識別。 在這個 Active Directory 網域上，您會管理使用者和群組。 若要在您的目錄中設定 Azure 託管的 Active Directory 網域和使用者帳戶, 請遵循下列步驟:

1. 在 Azure 入口網站中，將使用者新增至 Active Directory： 

   1. 使用具有目錄全域管理員身分的帳戶來登入[Azure Active Directory 系統管理中心](https://aad.portal.azure.com)。
    
   1. 選取 [Azure Active Directory]，然後選取 [使用者和群組]。
    
   1. 在 [**使用者和群組**] 中, 選取 [**所有使用者**], 然後選取 [**新增使用者**]。
   
           The **User** pane opens:
      
      ![[使用者] 窗格](./media/add-user.png)
    
   1. 輸入使用者的詳細資料，例如「名稱」和「使用者名稱」。 使用者名稱的網域名稱部分必須是初始預設網域名稱 "[網域名稱].onmicrosoft.com"，或是已驗證的非同盟[自訂網域名稱](../../active-directory/add-custom-domain.md)，例如"contoso.com"。
    
   1. 複製或記下產生的使用者密碼，以便在此程序完成後，將它提供給使用者。
    
   1. (選擇性) 您可以開啟使用者的 [設定檔]、[群組] 或 [目錄角色]，然後在其中填入資訊。 
    
   1. 在 [**使用者**] 底下, 選取 [**建立**]。
    
   1. 將產生的密碼安全地散發給新使用者, 讓他們可以登入。

1. 建立 Azure AD DS 執行個體。 遵循[使用 Azure 入口網站啟用 Azure Active Directory Domain Services](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-getting-started)中的指示 (「建立實例和設定基本設定」一節)。 請務必更新 Active Directory 中現有的使用者密碼，以便同步 Azure AD DS 中的密碼。 也請務必將 DNS 新增至 Azure AD DS, 如 < 在該區段中建立 Azure AD DS 實例 Azure 入口網站的 [基本] 視窗中的 [完成] 欄位中所述。

1. 在先前步驟的「建立和設定虛擬網路」一節中建立的虛擬網路中, 建立個別的 DSVM 子網。
1. 在 DSVM 子網中建立一或多個 DSVM 實例。
1. 依照[指示](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-join-ubuntu-linux-vm )將 DSVM 新增至 Active Directory。 
1. 掛接 Azure 檔案儲存體共用來裝載您的首頁或筆記本目錄, 讓您的工作區可以裝載在任何電腦上。 (如果您需要嚴格的檔案層級許可權, 則需要在一或多個 Vm 上執行的網路檔案系統 [NFS])。

   1. [建立 Azure 檔案服務共用](../../storage/files/storage-how-to-create-file-share.md)。
    
   2.  在 Linux DSVM 上掛接此共用。 當您在 Azure 入口網站的儲存體帳戶中選取 [連線 **]** 做為 Azure 檔案儲存體共用時, 會出現在 [LINUX] DSVM 的 bash shell 中執行的命令。 此命令看起來如下︰
   
   ```
   sudo mount -t cifs //[STORAGEACCT].file.core.windows.net/workspace [Your mount point] -o vers=3.0,username=[STORAGEACCT],password=[Access Key or SAS],dir_mode=0777,file_mode=0777,sec=ntlmssp
   ```
1. 例如, 假設您已在/data/workspace. 中掛接您的 Azure 檔案儲存體共用 現在, 為您在共用中的每個使用者建立目錄:/data/workspace/user1、/data/workspace/user2 等等。 在每個使用者的工作區中建立 `notebooks` 目錄。 
1. 在 `$HOME/userx/notebooks/remote` 中建立 `notebooks` 的符號連結。   

您現在已在 Azure 中託管 Active Directory 實例中的使用者。 藉由使用 Active Directory 認證, 使用者可以登入已加入 Azure AD DS 的任何 DSVM (SSH 或 JupyterHub)。 由於使用者工作區位於「Azure 檔案服務」共用上，因此使用者在使用 JupyterHub 時，可以從任何 DSVM 存取其筆記本和其他工作。

針對自動調整，您可以使用虛擬機器擴展集來建立 VM 的集區，這些 VM 都是以這種方式加入網域，而且已掛接共用磁碟。 使用者可以登入虛擬機器擴展集中的任何可用電腦, 而且能夠存取儲存其筆記本的共用磁片。 

## <a name="next-steps"></a>後續步驟

* [安全地儲存用來存取雲端資源的認證](dsvm-secure-access-keys.md)



