---
title: 設定實體伺服器的 Azure Migrate 設備
description: 瞭解如何設定適用于實體伺服器評估的 Azure Migrate 設備。
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 11/19/2019
ms.author: raynew
ms.openlocfilehash: ade2a76c168002e573a6504e31769b83f0c6eb37
ms.sourcegitcommit: dbde4aed5a3188d6b4244ff7220f2f75fce65ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/19/2019
ms.locfileid: "74185896"
---
# <a name="set-up-an-appliance-for-physical-servers"></a>設定實體伺服器的設備

本文說明當您使用 Azure Migrate： Server 評估工具來評估實體伺服器時，如何設定 Azure Migrate 設備。

> [!NOTE]
> 如果此處提及您尚未在 Azure Migrate 入口網站中看到的功能，請稍候。 它們將會在下週前後出現。

Azure Migrate 設備是輕量設備，供 Azure Migrate 伺服器評估用來執行下列動作：

- 探索內部部署伺服器。
- 將探索到的伺服器的中繼資料和效能資料傳送至 Azure Migrate Server 評估。

[深入瞭解](migrate-appliance.md)Azure Migrate 設備。


## <a name="appliance-deployment-steps"></a>設備部署步驟

若要設定設備，請：
- 從 Azure 入口網站下載含有 Azure Migrate 安裝程式指令碼的 ZIP 壓縮檔案。
- 從 ZIP 壓縮檔案解壓縮內容。 以系統管理權限啟動 PowerShell 主控台。
- 執行 PowerShell 指令碼以啟動設備 Web 應用程式。
- 第一次設定設備，並向 Azure Migrate 專案進行註冊。

## <a name="download-the-installer-script"></a>下載安裝程式指令碼

下載設備的 ZIP 壓縮檔案。

1. 在[移轉目標] > [伺服器] > [Azure Migrate：伺服器評估] 中，按一下[探索]。
2. 在 [探索機器] > [機器是否已虛擬化?] 中，按一下 [未虛擬化/其他]。
3. 按一下 [下載] 以下載 ZIP 壓縮檔案。

    ![下載 VM](./media/how-to-set-up-appliance-hyper-v/download-appliance-hyperv.png)


### <a name="verify-security"></a>確認安全性

請先確認 ZIP 檔案安全無虞再進行部署。

1. 在存放下載檔案的目標電腦上，開啟系統管理員命令視窗。
2. 執行下列命令以產生 VHD 的雜湊
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - 使用方式範例：```C:\>CertUtil -HashFile C:\AzureMigrate\AzureMigrate.ova SHA256```
3.  針對最新的設備版本，產生的雜湊應符合這些設定。

  **演算法** | **雜湊值**
  --- | ---
  MD5 | 5b98cf68dad495696c789bdad8f0d91b
  SHA256 | c2364887738875a31634eb0cf5defd0398f813d41069875976ca076297071e6f



## <a name="run-the-azure-migrate-installer-script"></a>執行 Azure Migrate 安裝程式指令碼
= 此安裝程式指令碼會執行下列作業︰

- 安裝用於實體伺服器探索及評定的代理程式與 Web 應用程式。
- 安裝 Windows 角色，包括 Windows 啟用服務、IIS 與 PowerShell ISE。
- 下載並安裝 IIS 可讀寫模組。 [詳細資訊](https://www.microsoft.com/download/details.aspx?id=7435)。
- 使用 Azure Migrate 的持續設定詳細資料來更新登錄機碼 (HKLM)。
- 在路徑底下建立下列檔案：
    - **組態檔**：%Programdata%\Microsoft Azure\Config
    - **記錄檔**：%Programdata%\Microsoft Azure\Logs

執行指令碼，如下所示：

1. 將 ZIP 壓縮檔案解壓縮至會裝載設備之伺服器上的資料夾。
2. 在上述伺服器上，使用系統管理 (提高的) 權限來啟動 PowerShell。
3. 將 PowerShell 目錄變更為已從下載的 ZIP 壓縮檔案解壓縮內容的資料夾。
4. 執行下列命令，以執行指令碼：
    ```
    PS C:\Users\Administrators\Desktop> AzureMigrateInstaller-physical.ps1
    ```
指令碼會在成功完成時啟動設備 Web 應用程式。



### <a name="verify-appliance-access-to-azure"></a>確認設備是否能存取 Azure

請確定設備 VM 可以連線到所需的[Azure url](migrate-support-matrix-hyper-v.md#assessment-appliance-url-access)。

## <a name="configure-the-appliance"></a>設定設備

第一次設定設備。

1. 在任何可連線至 VM 的電腦上開啟瀏覽器，並開啟設備 web 應用程式的 URL： **HTTPs://*設備名稱或 IP 位址*： 44368**。

   或者，您也可以按一下應用程式捷徑，從桌面開啟應用程式。
2. 在 [Web 應用程式] > [設定必要條件] 中，執行下列動作：
    - **授權**：接受授權條款，並閱讀協力廠商資訊。
    - 連線**能力**：應用程式會檢查 VM 是否可存取網際網路。 如果 VM 使用 Proxy：
        - 按一下 [Proxy 設定]，然後以 http://ProxyIPAddress 或 http://ProxyFQDN 格式指定 Proxy 位址和接聽連接埠。
        - 如果 Proxy 需要驗證，請指定認證。
        - 僅支援 HTTP Proxy。
    - **時間同步**：已驗證時間。 設備上的時間應該與網際網路時間同步，VM 探索才能正常運作。
    - **安裝更新**： Azure Migrate Server 評估會檢查設備是否已安裝最新的更新。

### <a name="register-the-appliance-with-azure-migrate"></a>向 Azure Migrate 註冊設備

1. 按一下 [登入]。 如果未出現，請確定您已在瀏覽器中停用快顯封鎖程式。
2. 在新的索引標籤上，使用您的 Azure 認證登入。
    - 使用您的使用者名稱和密碼登入。
    - 不支援使用 PIN 登入。
3. 成功登入後，返回 Web 應用程式。
4. 選取 Azure Migrate 專案建立所在的訂用帳戶。 然後選取專案。
5. 指定設備的名稱。 名稱應該是英數位元，且長度不超過 14 個字元。
6. 按一下 [註冊]。


## <a name="start-continuous-discovery"></a>開始連續探索

從設備連接到實體伺服器，然後開始探索。

1. 按一下 [新增認證] 以指定設備會用來探索伺服器的帳戶認證。  
2. 指定**作業系統**、認證的易記名稱、**使用者名稱**與**密碼**，然後按一下 [新增]。
您可以新增一組適用於 Windows 與 Linux 伺服器的認證。
4. 按一下 [新增伺服器]，然後指定伺服器詳細資料 - FQDN/IP 位址與認證的易記名稱 (每列一筆輸入) 以連線至伺服器。
3. 按一下 [驗證]。 驗證之後，就會顯示可探索的伺服器清單。
    - 如果伺服器驗證失敗，請將滑鼠停留在 [狀態] 欄中的圖示上以檢閱錯誤。 修正問題，然後再次驗證。
    - 若要移除伺服器，請選取 > [刪除]。
4. 驗證之後，按一下 [儲存並開始探索] 來開始探索程序。

這會開始探索。 所探索到 VM 的中繼資料會在大約 15 分鐘後出現在 Azure 入口網站中。

## <a name="verify-servers-in-the-portal"></a>在入口網站中驗證伺服器

探索完成之後，您可以確認伺服器是否出現在入口網站中。

1. 開啟 Azure Migrate 儀表板。
2. 在 [Azure Migrate - 伺服器] > [Azure Migrate：伺服器評估] 頁面中，按一下圖示以顯示 [探索到的伺服器] 計數。


## <a name="next-steps"></a>後續步驟

嘗試使用 Azure Migrate Server 評估[來評估實體伺服器](tutorial-assess-physical.md)。
