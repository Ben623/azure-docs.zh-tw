---
title: Azure 儲存體總管疑難排解指南 | Microsoft Docs
description: Azure 儲存體總管偵錯技術的概觀
services: virtual-machines
author: Deland-Han
ms.service: virtual-machines
ms.topic: troubleshooting
ms.date: 06/15/2018
ms.author: delhan
ms.openlocfilehash: fd34ab7cd899549962663e8cee8ee2121c39c49e
ms.sourcegitcommit: 64798b4f722623ea2bb53b374fb95e8d2b679318
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/11/2019
ms.locfileid: "67840391"
---
# <a name="azure-storage-explorer-troubleshooting-guide"></a>Azure 儲存體總管疑難排解指南

Microsoft Azure 儲存體總管是一個獨立應用程式，可讓您在 Windows、macOS 和 Linux 上輕鬆使用 Azure 儲存體資料。 應用程式可以連線至裝載於 Azure、National Clouds 和 Azure Stack 上的儲存體帳戶。

本指南摘要說明儲存體總管中常見的問題解決方案。

## <a name="role-based-access-control-permission-issues"></a>角色型存取控制的權限問題

[角色型存取控制 (RBAC)](https://docs.microsoft.com/azure/role-based-access-control/overview)藉由結合到權限集合提供更細緻的存取管理 Azure 資源_角色_。 以下是一些您可以遵循以取得使用儲存體總管中的 RBAC 的建議。

### <a name="what-do-i-need-to-see-my-resources-in-storage-explorer"></a>我要請參閱我在儲存體總管 中的資源？

如果您遇到存取使用 RBAC 的儲存體資源的問題，可能是因為您沒有被指派適當的角色。 下列各節說明儲存體總管目前需要存取您的儲存體資源的權限。

如果您不確定您有適當的角色或權限，請連絡您的 Azure 帳戶系統管理員。

#### <a name="read-listget-storage-accounts"></a>讀取：列出/取得儲存體帳戶

您必須列出儲存體帳戶的權限。 您可以指派 「 讀者 」 角色，以取得此權限。

#### <a name="list-storage-account-keys"></a>列出儲存體帳戶金鑰

儲存體總管也可以使用帳戶金鑰來驗證要求。 您可以取得存取金鑰與更強大的角色，例如 「 參與者 」 角色。

> [!NOTE]
> 存取金鑰授與不受限制的權限會保留它們的任何人。 因此，通常不建議他們交給帳戶使用者。 如果您需要撤銷存取金鑰，您可以重新產生它們[Azure 入口網站](https://portal.azure.com/)。

#### <a name="data-roles"></a>資料角色

您必須指派至少一個角色，授與存取權從資源讀取資料。 例如，如果您要列出或下載 blob，您將至少需要 「 儲存體 Blob 資料讀者 」 角色。

### <a name="why-do-i-need-a-management-layer-role-to-see-my-resources-in-storage-explorer"></a>為什麼需要管理層級角色，即可查看我的資源在儲存體總管？

Azure 儲存體有兩個層級的存取權限：_管理_並_資料_。 透過管理層存取的訂用帳戶和儲存體帳戶。 透過資料層存取的容器、 blob 和其他資料資源。 例如，如果您想要從 Azure 取得的儲存體帳戶清單，請管理端點傳送要求。 如果您想在帳戶中的 blob 容器的清單，您會將要求傳送至適當的服務端點。

RBAC 角色可能會包含管理] 或 [資料層存取的權限。 「 讀者 」 角色，例如，授與您唯讀存取權管理層資源。

嚴格來說，「 讀者 」 角色會不提供任何資料層級權限，並不需要存取資料層。

儲存體總管可讓您更輕鬆地存取您的資源所收集其連線到您的 Azure 資源中，為您所需的資訊。 例如，若要顯示 blob 容器，儲存體總管會傳送至 blob 服務端點清單容器要求。 若要取得該端點，儲存體總管會搜尋訂用帳戶清單，並儲存體帳戶有存取權。 但是，若要尋找您的訂用帳戶和儲存體帳戶，儲存體總管也需要存取管理層。

如果您還沒有授與任何管理層級權限的角色，儲存體總管無法取得所需連接到資料層的資訊。

### <a name="what-if-i-cant-get-the-management-layer-permissions-i-need-from-my-administrator"></a>如果我不能管理層級權限需要系統管理員身分從嗎？

我們目前還沒有 RBAC 相關的解決方案。 因應措施，您可以要求 SAS URI[附加至您的資源](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer?tabs=linux#use-a-sas-uri)。

## <a name="error-self-signed-certificate-in-certificate-chain-and-similar-errors"></a>Error:憑證鏈結中的自我簽署憑證 (和類似錯誤)

憑證錯誤是由下列兩種情況的其中一個所造成：

1. 應用程式是透過「透明 proxy」連線，這表示伺服器 (例如您公司的伺服器) 是使用自我簽署憑證攔截 HTTPS 流量、解密再加密。
2. 您正在執行的應用程式，會將自我簽署的 SSL 憑證插入您收到的 HTTPS 訊息。 會插入憑證的應用程式例子包括防毒軟體和網路流量檢查軟體。

當儲存體總管看見自我簽署或不受信任的憑證，就無法再得知收到的 HTTPS 訊息是否已被更改。 如果您有一份自我簽署憑證，便可以執行下列步驟，指示儲存體總管信任該憑證：

1. 取得憑證的 Base-64 編碼 X.509 (.cer) 複本
2. 按一下 [編輯]   > [SSL 憑證]   > [匯入憑證]  ，然後使用檔案選擇器來尋找、選取及開啟 .cer 檔案

此問題也可能是因為有多個憑證 (根憑證與中繼憑證)。 必須新增這兩個憑證，才能解決錯誤。

如果您不確定的憑證來自何處，您可以嘗試下列步驟來尋找它：

1. 安裝 Open SSL
    * [Windows](https://slproweb.com/products/Win32OpenSSL.html) (任一輕裝版足可應對)
    * Mac 和 Linux：應該包含在作業系統中
2. 執行 Open SSL
    * Windows：開啟安裝目錄，按一下 **/bin/** ，再按兩下 **openssl.exe**。
    * Mac 和 Linux：從終端機執行 **openssl**。
3. 執行 `s_client -showcerts -connect microsoft.com:443`
4. 尋找自我簽署憑證。 如果您不確定哪一個憑證是自我簽署，請尋找任何位置主旨`("s:")`和 簽發者`("i:")`都相同。
5. 發現任何自我簽署的憑證時，請將每個憑證從 **-----BEGIN CERTIFICATE-----** 到 **-----END CERTIFICATE-----** (含) 的所有內容，複製並貼到新的 .cer 檔案。
6. 開啟儲存體總管，按一下 [編輯]   > [SSL 憑證]   > [匯入憑證]  ，然後使用檔案選擇器來尋找、選取及開啟您所建立的 .cer 檔案。

如果找不到任何自我簽署的憑證，使用上述步驟，請與我們連絡透過意見反應工具，如需詳細說明。 您也可以選擇從命令列啟動儲存體總管`--ignore-certificate-errors`旗標。 使用這個旗標啟動時，儲存體總管會忽略憑證錯誤。

## <a name="sign-in-issues"></a>登入問題

### <a name="blank-sign-in-dialog"></a>空白的 [登入] 對話方塊

空白的登入對話方塊的原因通常 ADFS 所要求儲存體總管來執行支援的 Electron 的重新導向。 若要解決此問題，您可以嘗試使用裝置程式碼流程來登入。 若要這樣做，完成下列步驟：

1. 功能表：預覽-> [使用裝置程式碼登入]。
2. 開啟 [連線] 對話方塊 (透過左側垂直列上的插頭圖示，或透過在 [帳戶] 面板上的 [新增帳戶])。
3. 選擇您想要登入哪些的環境。
4. 按一下 [登入] 按鈕。
5. 遵循下一個面板上的指示。

如果您發現自己到您想要使用，因為預設瀏覽器已登入不同的帳戶的帳戶登入時發生問題，您可以：

1. 手動將連結和程式碼複製到您瀏覽器的隱私工作階段。
2. 手動將連結和程式碼複製到其他瀏覽器。

### <a name="reauthentication-loop-or-upn-change"></a>重新驗證迴圈或 UPN 變更

如果您在重新驗證的迴圈中，或已變更的其中一個帳戶的 UPN，請嘗試下列步驟：

1. 移除所有的帳戶，然後關閉 [儲存體總管]
2. 從您的機器中刪除 .IdentityService 資料夾。 在 Windows 中，該資料夾位於 `C:\users\<username>\AppData\Local`。 對於 Mac 和 Linux，您可以在使用者目錄的根目錄中找到此資料夾。
3. 如果您是在 Mac 或 Linux 上，您也需要從您的作業系統金鑰儲存區中刪除 Microsoft.Developer.IdentityService 項目。 在 Mac 上，金鑰儲存區會是 "Gnome Keychain" 應用程式。 針對 Linux，此應用程式通常稱為 "Keyring"，但此名稱可能會因為您的散發版本不同而有差異。

### <a name="conditional-access"></a>條件式存取

在 Windows 10、 Linux 或 macOS 上使用儲存體總管時，不支援條件式存取。 這是因為儲存體總管所使用的 AAD 程式庫中的限制。

## <a name="mac-keychain-errors"></a>Mac Keychain 錯誤

macOS 鑰匙圈有時會進入導致 [儲存體總管] 的驗證程式庫發生問題的狀態。 若要發揮這種狀態中的金鑰鏈，請嘗試下列步驟：

1. 關閉 [儲存體總管]。
2. 開啟鑰匙圈 (**cmd + 空格鍵**，鍵入 keychain，按 Enter)。
3. 選取 [登入] 鑰匙圈。
4. 按一下掛鎖圖示以鎖定鑰匙圈 (完成時，掛鎖會以動畫方式顯示到鎖定的位置，視您所開啟的應用程式而定，它可能需要幾秒鐘的時間)。

    ![image](./media/storage-explorer-troubleshooting/unlockingkeychain.png)

5. 啟動儲存體總管。
6. 快顯視窗應該會隨即出現，並包含類似「服務中樞想要存取 keychain」的訊息。 當它，輸入您的 Mac 系統管理員帳戶密碼，並按一下**永遠允許**(或**允許**如果**一律允許**無法使用)。
7. 嘗試登入。

### <a name="general-sign-in-troubleshooting-steps"></a>一般登入疑難排解步驟

* 如果您是在 macOS 上，並透過 「 正在等候驗證...」 永遠不會出現 [登入] 視窗對話方塊中，然後再次嘗試[這些步驟](#mac-keychain-errors)
* 重新啟動儲存體總管
* 如果驗證視窗空白，請在關閉驗證對話方塊之前先等待至少一分鐘。
* 確認您的機器和儲存體總管都已正確設定 Proxy 和憑證設定。
* 如果您是在 Windows 上，且可以存取 Visual Studio 2019 在相同電腦上登入，請嘗試登入 Visual Studio 2019。 成功登入 Visual Studio 2019 之後, 您可以開啟儲存體總管，並查看您的帳戶，在 [帳戶] 面板。

如果這些方法都沒有用，請[在 GitHub 上開立問題](https://github.com/Microsoft/AzureStorageExplorer/issues)。

### <a name="missing-subscriptions-and-broken-tenants"></a>訂用帳戶遺失和租用戶損毀

如果您無法擷取訂用帳戶，您已成功登入後，請嘗試下列疑難排解方法：

* 確認您的帳戶可存取預期的訂用帳戶。 您可以登入入口網站中，您想要使用的 Azure 環境，以確認您的存取權。
* 請確定您已登入使用正確的 Azure 環境 （Azure、 Azure 中國 21Vianet、 Azure Germany、 Azure 美國政府或自訂環境）。
* 如果您是在 proxy 背景，請確定您已正確設定儲存體總管的 proxy。
* 嘗試移除再重新新增帳戶。
* 如果沒有 [詳細資訊] 連結，查看，並看到什麼錯誤訊息已報告的租用戶之失敗。 如果 you'ren 無法確定該如何處理錯誤訊息，請參閱則自由[GitHub 上開立](https://github.com/Microsoft/AzureStorageExplorer/issues)。

## <a name="cant-remove-attached-account-or-storage-resource"></a>無法移除連結的帳戶或儲存體資源

如果您無法移除連結的帳戶或儲存體資源，透過 UI，您可以手動刪除連結的所有資源，藉由刪除下列資料夾：

* Windows：`%AppData%/StorageExplorer`
* macOS：`/Users/<your_name>/Library/Application Support/StorageExplorer`
* Linux：`~/.config/StorageExplorer`

> [!NOTE]
> 請先關閉儲存體總管，然後再刪除上述資料夾。

> [!NOTE]
> 如果您曾經匯入任何 SSL 憑證，請備份 `certs` 目錄的內容。 稍後，您可以使用備份來重新匯入 SSL 憑證。

## <a name="proxy-issues"></a>Proxy 問題

首先，確定您輸入的下列資訊都正確：

* Proxy URL 和連接埠號碼
* 使用者名稱和密碼 (如果 proxy 要求)

> [!NOTE]
> 儲存體總管不支援 proxy 自動設定檔的 proxy 設定。

### <a name="common-solutions"></a>常見的解決方案

如果您仍然遇到問題，請嘗試下列疑難排解方法：

* 如果可以不使用 proxy 連接到網際網路，請確認儲存體總管不啟用 proxy 設定也能運作。 如果是這種情況，可能是 proxy 設定發生問題。 請和您的 proxy 系統管理員合作找出問題。
* 確認使用 proxy 伺服器的其他應用程式是否如預期般運作。
* 請確認您可以連接到您想要使用的 Azure 環境入口網站
* 確認您可以收到服務端點的回應。 在瀏覽器中輸入其中一個端點 URL。 如果可以連線，您應該會收到 InvalidQueryParameterValue 或類似的 XML 回應。
* 如果有其他人也用您的 proxy 伺服器使用儲存體總管，請確認他們可以連線。 如果他們可以連線，您可能必須連絡您的 proxy 伺服器管理員。

### <a name="tools-for-diagnosing-issues"></a>診斷問題的工具

如果您有網路工具，例如 Fiddler for Windows 時，您可以診斷問題，如下所示：

* 如果必須透過 proxy 工作，您可能必須設定網路工具透過 proxy 連線。
* 檢查網路工具使用的連接埠號碼。
* 輸入本機主機 URL 和網路工具的連接埠號碼，當做儲存體總管的 proxy 設定。 如果作業正確，您的網路工具就會開始記錄儲存體總管向管理和服務端點提出的網路要求。 例如，輸入 https://cawablobgrs.blob.core.windows.net/ 回應為您的 blob 端點，在瀏覽器中，而且您會收到類似下列程式碼，這表示資源存在於，雖然您無法存取它。

![程式碼範例](./media/storage-explorer-troubleshooting/4022502_en_2.png)

### <a name="contact-proxy-server-admin"></a>連絡 proxy 伺服器管理員

如果 proxy 設定正確，您可能要連絡您的 proxy 伺服器管理員，並

* 請確定您的 proxy 不會封鎖 Azure 管理或資源端點的流量。
* 確認 proxy 伺服器使用的驗證通訊協定。 儲存體總管目前不支援 NTLM proxy。

## <a name="unable-to-retrieve-children-error-message"></a>「無法擷取子系」錯誤訊息

如果您透過 proxy 連線至 Azure，請確認您的 proxy 設定正確無誤。 如果您要授與存取資源的訂用帳戶或帳戶擁有者，請確認您已閱讀或列出該資源的權限。

## <a name="connection-string-doesnt-have-complete-configuration-settings"></a>連接字串不會有完整的組態設定

如果您收到這個錯誤訊息時，很可能您沒有所需的權限，可取得儲存體帳戶的金鑰。 若要確認是否為此情形，請移至入口網站並找到您的儲存體帳戶。 您可以快速地執行這項操作您的儲存體帳戶的節點上按一下滑鼠右鍵，然後按一下 [開啟在入口網站]。 這麼做之後，會移至 [存取金鑰] 刀鋒視窗。 如果您沒有權限可檢視索引鍵，您會看到 「 您沒有存取 」 訊息的頁面。 若要解決此問題，您可以從其他人取得帳戶金鑰，並附加具有名稱和金鑰，或儲存體帳戶之 sas 要求的人並用它來附加儲存體帳戶。

如果您看到的帳戶金鑰，提出問題在 GitHub 上讓我們協助您解決問題。

## <a name="issues-with-sas-url"></a>SAS URL 問題

如果您使用 SAS URL 連線到服務，而碰到此錯誤：

* 請確認 URL 提供讀取或列出資源的必要權限。
* 請確認 URL 尚未過期。
* 如果 SAS URL 是以存取原則為基礎，請確認尚未撤銷存取原則。

如果您不慎以無效的 SAS URL 進行連結，但是無法中斷連結，請遵循下列步驟：

1. 執行儲存體總管時，按下 F12 以開啟開發人員工具視窗。
2. 按一下 [應用程式] 索引標籤，然後在左邊樹狀目錄中，按一下 [本機儲存體] > file://。
3. 尋找與有問題的 SAS URI 服務類型相關聯的索引鍵。 例如，如果是 Blob 容器的 SAS URI 不正確，請尋找名為 `StorageExplorer_AddStorageServiceSAS_v1_blob` 的索引鍵。
4. 索引鍵的值應該是 JSON 陣列。 尋找與不正確 URI 相關聯的物件，並將它移除。
5. 按下 Ctrl+R，重新載入儲存體總管。

## <a name="linux-dependencies"></a>Linux 相依項目

<!-- Storage Explorer 1.9.0 and later is available as a snap from the Snap Store. The Storage Explorer snap installs all of its dependencies with no extra hassle.

Storage Explorer requires the use of a password manager, which may need to be connected manually before Storage Explorer will work correctly. You can connect Storage Explorer to your system's password manager with the following command:

```bash
snap connect storage-explorer:password-manager-service :password-manager-service
```

You can also download the application .tar.gz file, but you'll have to install dependencies manually. -->

> [!IMPORTANT]
> 所提供的儲存體總管.tar.gz 下載僅適用於 Ubuntu 散發套件。 其他散發套件尚未驗證，而且可能需要替代或其他封裝。

這些封裝是最常見的需求，在 Linux 上的儲存體總管：

* [.NET core 2.0 執行階段](https://docs.microsoft.com/dotnet/core/linux-prerequisites?tabs=netcore2x)
* `libgconf-2-4`
* `libgnome-keyring0` 或 `libgnome-keyring-dev`
* `libgnome-keyring-common`

> [!NOTE]
> 儲存體總管版本 1.7.0 和稍早需要.NET Core 2.0。 如果您有較新版的.NET Core 安裝，則您必須[修補儲存體總管](#patching-storage-explorer-for-newer-versions-of-net-core)。 如果您執行儲存體總管 1.8.0 或更高的然後您應該能夠最多可以使用.NET Core 2.2。 2\.2 版本尚未驗證能夠在這個階段。

# <a name="ubuntu-1904tab1904"></a>[Ubuntu 19.04](#tab/1904)

1. 下載儲存體總管。
2. 安裝[.NET Core 執行階段](https://dotnet.microsoft.com/download/linux-package-manager/ubuntu19-04/runtime-current)。
3. 執行下列命令：
   ```bash
   sudo apt-get install libgconf-2-4 libgnome-keyring0
   ```

# <a name="ubuntu-1804tab1804"></a>[Ubuntu 18.04](#tab/1804)

1. 下載儲存體總管。
2. 安裝[.NET Core 執行階段](https://dotnet.microsoft.com/download/linux-package-manager/ubuntu18-04/runtime-current)。
3. 執行下列命令：
   ```bash
   sudo apt-get install libgconf-2-4 libgnome-keyring-common libgnome-keyring0
   ```

# <a name="ubuntu-1604tab1604"></a>[Ubuntu 16.04](#tab/1604)

1. 下載儲存體總管
2. 安裝[.NET Core 執行階段](https://dotnet.microsoft.com/download/linux-package-manager/ubuntu16-04/runtime-current)。
3. 執行下列命令：
   ```bash
   sudo apt install libgnome-keyring-dev
   ```

# <a name="ubuntu-1404tab1404"></a>[Ubuntu 14.04](#tab/1404)

1. 下載儲存體總管
2. 安裝[.NET Core 執行階段](https://dotnet.microsoft.com/download/linux-package-manager/ubuntu14-04/runtime-current)。
3. 執行下列命令：
   ```bash
   sudo apt install libgnome-keyring-dev
   ```

### <a name="patching-storage-explorer-for-newer-versions-of-net-core"></a>較新版本的.NET Core 的修補，儲存體總管

儲存體總管 1.7.0 或更舊版本，您可能需要修補的儲存體總管所使用的.NET Core 版本。

1. 下載新版 1.5.43 StreamJsonRpc[從 nuget](https://www.nuget.org/packages/StreamJsonRpc/1.5.43)。 尋找頁面的右手邊的 [下載套件] 連結。
2. 下載封裝之後, 變更其副檔名從`.nupkg`至`.zip`。
3. 將封裝解壓縮。
4. 開啟 `streamjsonrpc.1.5.43/lib/netstandard1.1/` 資料夾。
5. 複製`StreamJsonRpc.dll`到儲存體總管資料夾內的下列位置：
   * `StorageExplorer/resources/app/ServiceHub/Services/Microsoft.Developer.IdentityService/`
   * `StorageExplorer/resources/app/ServiceHub/Hosts/ServiceHub.Host.Core.CLR.x64/`

## <a name="open-in-explorer-from-azure-portal-doesnt-work"></a>開啟在 檔案總管從 Azure 入口網站無法運作

如果在 Azure 入口網站的 開啟在 總管 按鈕不適合您，請確定您使用相容的瀏覽器。 下列瀏覽器的相容性測試。
* Microsoft Edge
* Mozilla Firefox
* Google Chrome
* Microsoft Internet Explorer

## <a name="next-steps"></a>後續步驟

如果這些解決方法對您都沒有用，請[在 GitHub 上開立問題](https://github.com/Microsoft/AzureStorageExplorer/issues)。 您也可以使用左下角的 [回報問題到 GitHub] 按鈕，以快速前往 GitHub。

![意見反應](./media/storage-explorer-troubleshooting/feedback-button.PNG)
