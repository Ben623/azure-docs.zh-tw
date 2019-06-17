---
title: 針對 Azure 資料箱磁碟進行疑難排解 | Microsoft Docs
description: 說明如何針對在 Azure 資料箱磁碟中察覺的問題進行疑難排解。
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: article
ms.date: 04/2/2019
ms.author: alkohli
ms.openlocfilehash: f9d01b56da2650be395878ce07e4aae73495061f
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "64939628"
---
# <a name="troubleshoot-issues-in-azure-data-box-disk"></a>對 Azure 資料箱磁碟中的問題進行疑難排解

本文適用於 Microsoft Azure 資料箱磁碟，並描述您在部署此解決方案時所見任何問題的疑難排解工作流程。 

本文包含下列小節：

- 下載診斷記錄
- 查詢活動記錄
- 資料箱磁碟解除鎖定工具錯誤
- 資料箱磁碟分割複製工具錯誤

## <a name="download-diagnostic-logs"></a>下載診斷記錄

如果在資料複製程序期間有任何錯誤，則入口網站會顯示前往資料夾 (診斷記錄位於該資料夾) 的路徑。 

診斷記錄可以是：
- 錯誤記錄
- 詳細資訊記錄  

若要瀏覽至複製記錄的路徑，請移至與資料箱訂單相關聯的儲存體帳戶。 

1.  移至 [一般 > 訂單詳細資料]  ，並記下與訂單相關聯的儲存體帳戶。
 

2.  移至 [所有資源]  並搜尋在上一個步驟中所識別出的儲存體帳戶。 選取並按一下儲存體帳戶。

    ![複製記錄 1](./media/data-box-disk-troubleshoot/data-box-disk-copy-logs1.png)

3.  移至 [Blob 服務 > 瀏覽 Blob]  並尋找對應到儲存體帳戶的 Blob。 移至 [diagnosticslogcontainer > waies]  。 

    ![複製記錄 2](./media/data-box-disk-troubleshoot/data-box-disk-copy-logs2.png)

    您應該會看到資料複製的錯誤記錄和詳細資訊記錄。 選取並按一下每個檔案，然後下載本機複本。

## <a name="query-activity-logs"></a>查詢活動記錄

使用活動記錄可在進行疑難排解時發現錯誤，或監視貴組織使用者修改資源的方式。 透過活動記錄，您可以判斷︰

- 在訂用帳戶的資源上進行了哪些作業。
- 起始作業的人員。
- 發生作業的時間。
- 作業狀態。
- 其他可能協助您研究作業的屬性值。

活動記錄包含在您的資源上執行的所有寫入作業 (例如 PUT、POST、DELETE)，但不包含讀取作業 (例如 GET)。

活動記錄會保留 90 天。 您可以查詢任何的日期範圍，只要開始日期不是在過去 90 天以前。 您也可以依據 Insights 中其中一個內建查詢來進行篩選。 例如，按一下錯誤然後選取並按一下特定錯誤，以了解根本原因。

## <a name="data-box-disk-unlock-tool-errors"></a>資料箱磁碟解除鎖定工具錯誤


| 錯誤訊息/工具行為      | 建議                                                                                               |
|-------------------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------|
| None<br><br>資料箱磁碟解除鎖定工具損毀。                                                                            | 未安裝 BitLocker。 請確定執行資料箱磁碟解除鎖定工具的主機電腦已安裝 BitLocker。                                                                            |
| 不支援最新版本的 .NET Framework。 支援的版本是 4.5 版和更新版本。<br><br>工具結束並且有訊息。  | 未安裝 .NET 4.5。 請在執行資料箱磁碟解除鎖定工具的主機電腦上，安裝 .NET 4.5 或更新版本。                                                                            |
| 無法解除鎖定或驗證任何磁碟區。 連絡 Microsoft 支援服務。  <br><br>此工具無法解除鎖定或驗證任何已鎖定的磁碟機。 | 此工具無法使用提供的通行金鑰，解除鎖定任何已鎖定的磁碟機。 連絡 Microsoft 支援服務以進行後續步驟。                                                |
| 下列磁碟區已解除鎖定並經過驗證。 <br>磁碟區磁碟機代號：E:<br>無法使用下列通行金鑰解除鎖定任何磁碟區：werwerqomnf、qwerwerqwdfda <br><br>此工具會解除鎖定部分磁碟機，並列出成功和失敗的磁碟機代號。| 部分成功。 無法使用提供的通行金鑰解除鎖定部分磁碟機。 連絡 Microsoft 支援服務以進行後續步驟。 |
| 找不到鎖定的磁碟區。 請確認從 Microsoft 處接收的磁碟已正確連線且處於鎖定狀態。          | 此工具找不到任何鎖定的磁碟機。 可能磁碟機已解除鎖定，或未偵測到磁碟機。 請確定磁碟機已連線且已鎖定。                                                           |
| 嚴重錯誤:參數不正確<br>參數名稱：invalid_arg<br>使用方式：<br>DataBoxDiskUnlock /PassKeys:<passkey_list_separated_by_semicolon><br><br>範例：DataBoxDiskUnlock /PassKeys:passkey1;passkey2;passkey3<br>範例：DataBoxDiskUnlock /SystemCheck<br>範例：DataBoxDiskUnlock /Help<br><br>/PassKeys：     從 Azure 資料箱磁碟訂單取得此通行金鑰。 通行金鑰會將您的磁碟解除鎖定。<br>/Help：         這個選項提供 Cmdlet 使用方式和範例的說明。<br>/SystemCheck：  這個選項會檢查您的系統是否符合執行工具的需求。<br><br>按任意鍵以結束。 | 輸入的參數無效。 唯一允許的參數是 /SystemCheck、 /PassKey 和 /Help。                                                                            |

## <a name="data-box-disk-split-copy-tool-errors"></a>資料箱磁碟分割複製工具錯誤

|錯誤訊息/警告  |建議 |
|---------|---------|
|[資訊]擷取磁碟區的 BitLocker 密碼： m <br>[Error]擷取磁碟區 m： 的 BitLocker 金鑰時攔截到例外狀況<br> 序列未包含項目。|如果目的地資料箱磁碟處於離線狀態，就會擲回這個錯誤。 <br> 對線上磁碟使用 `diskmgmt.msc` 工具。|
|[錯誤] 擲回例外狀況：WMI 作業失敗:<br> Method=UnlockWithNumericalPassword, ReturnValue=2150694965, <br>Win32Message=所提供的復原密碼格式無效。 <br>BitLocker 修復原密碼是 48 位數字。 <br>請確認復原密碼的格式正確，然後再試一次。|先使用資料箱磁碟解除鎖定工具將磁碟解除鎖定，然後重試命令。 如需詳細資訊，請移至 <li> [解除鎖定適用於 Windows 用戶端的資料箱磁碟](data-box-disk-deploy-set-up.md#unlock-disks-on-windows-client)。 </li><li> [解除鎖定適用於 Linux 用戶端的資料箱磁碟](data-box-disk-deploy-set-up.md#unlock-disks-on-linux-client)。 </li>|
|[錯誤] 擲回例外狀況：存在於目標磁碟機上的 DriveManifest.xml 檔案。 <br> 這表示目標磁碟機可能已透過不同的日誌檔案來準備。 <br>若要將更多資料新增至相同磁碟機，請使用先前的日誌檔案。 若要刪除現有資料，並再次使用目標磁碟進行新的匯入作業，則請刪除磁碟機上的 DriveManifest.xml。 以新的日誌檔案重新執行此命令。| 當您嘗試對多個匯入工作階段使用同一組磁碟機時，就會收到這個錯誤。 <br> 一組磁碟機僅適用於一個分割及複製工作階段。|
|[錯誤] 擲回例外狀況：CopySessionId importdata-sept-test-1 參考前一個複製工作階段，而且無法用於新的複製工作階段。|如果嘗試使用前一個已成功完成的作業名稱來作為新作業的名稱，就會回報此錯誤。<br> 請為您的新作業指派唯一名稱。|
|[資訊] 目的地檔案或目錄名稱超過 NTFS 長度限制。 |當目的檔案因為檔案路徑太長而重新命名時，就會回報此訊息。<br> 請修改 `config.json` 檔案中的配置選項，以控制此行為。|
|[錯誤] 擲回例外狀況：錯誤的 JSON 逸出序列。 |當 config.json 的格式無效時，就會回報此訊息。 <br> 請在儲存檔案之前，先確認您的 `config.json` 是使用 [JSONlint](https://jsonlint.com/)。|

## <a name="deployment-issues-for-linux"></a>Linux 的部署問題

本節詳細說明一些當使用 Linux 用戶端進行資料複製時，於資料箱磁碟部署期間最常面臨的問題。

### <a name="issue-drive-getting-mounted-as-read-only"></a>問題：磁碟機以唯讀模式掛接
 
**原因** 

原因可能是未整理的檔案系統。 

將磁碟機重新掛接為讀寫不適用於資料箱磁碟。 此情節不適用於由 dislocker 解密的磁碟機。 您可能已經使用下列命令成功重新掛接磁碟機：

    `# mount -o remount, rw /mnt/DataBoxDisk/mountVol1`

雖然已經成功重新掛接，但資料不會保存。

**解決方案**

執行下列步驟在您的 Linux 系統上：

1. 安裝`ntfsprogs`ntfsfix 公用程式的套件。
2. 取消掛接後解除鎖定工具所提供的磁碟機的掛接點。 磁碟機而異的掛接點數目。

    ```
    unmount /mnt/DataBoxDisk/mountVol1
    ```

3. 執行`ntfsfix`上對應的路徑。 反白顯示的數字應該與步驟 2 相同。

    ```
    ntfsfix /mnt/DataBoxDisk/bitlockerVol1/dislocker-file
    ```

4. 執行下列命令來移除可能會造成連接問題的休眠中繼資料。

    ```
    ntfs-3g -o remove_hiberfile /mnt/DataBoxDisk/bitlockerVol1/dislocker-file /mnt/DataBoxDisk/mountVol1
    ```

5. 執行全新的卸載。

    ```
    ./DataBoxDiskUnlock_x86_64 /unmount
    ```

6. 執行全新的解除鎖定，並掛接。
7. 寫入檔案，以測試掛接點。
8. 卸載並重新掛接，以驗證檔案持續性。
9. 繼續進行複製的資料。
 
### <a name="issue-error-with-data-not-persisting-after-copy"></a>問題：複製之後資料沒有保留的錯誤
 
**原因** 

如果您發現磁碟機卸載之後沒有資料 (雖然資料有複製到其中)，則您可能在磁碟機掛接為唯讀之後，將它重新掛接為讀寫。

**解決方案**
 
如果是此情況，請參閱[磁碟機掛接為唯讀](#issue-drive-getting-mounted-as-read-only)的解決方法。

如果不是此情況，請複製有資料箱磁碟解除鎖定工具的資料夾之中的記錄，並[連絡 Microsoft 支援服務](data-box-disk-contact-microsoft-support.md)。

## <a name="deployment-issues-for-windows"></a>Windows 部署問題

本節詳細說明一些當使用 Windows 用戶端進行資料複製時，於資料箱磁碟部署期間最常面臨的問題

### <a name="issue-could-not-unlock-drive-from-bitlocker"></a>問題：無法從 BitLocker 將磁碟機解除鎖定
 
**原因** 

您使用 BitLocker 對話方塊中的密碼，並嘗試透過 BitLocker 解除鎖定磁碟機對話方塊來解除鎖定。 這不可行。 

**解決方案**

若要將資料箱磁碟解除鎖定，您需要使用「資料箱磁碟解除鎖定工具」，並提供來自 Azure 入口網站的密碼。 如需詳細資訊，請移至[教學課程：針對 Azure 資料箱磁碟解除封裝、連線然後解除鎖定](data-box-disk-deploy-set-up.md#connect-to-disks-and-get-the-passkey)。
 
### <a name="issue-could-not-unlock-or-verify-some-volumes-contact-microsoft-support"></a>問題：無法解除鎖定或驗證某些磁碟區。 連絡 Microsoft 支援服務。
 
**原因** 

您可能會在錯誤記錄檔中看到下列錯誤，且無法解除鎖定或驗證某些磁碟區。

`Exception System.IO.FileNotFoundException: Could not load file or assembly 'Microsoft.Management.Infrastructure, Version=1.0.0.0, Culture=neutral, PublicKeyToken=31bf3856ad364e35' or one of its dependencies. The system cannot find the file specified.`
 
這表示您的 Windows 用戶端上可能沒有適當版本的 Windows PowerShell。

**解決方案**

您可以安裝 [Windows PowerShell v 5.0](https://www.microsoft.com/download/details.aspx?id=54616) \(英文\) 並重試作業。
 
如果您仍然無法將磁碟區解除鎖定，請複製有資料箱磁碟解除鎖定工具的資料夾之中的記錄，並[連絡 Microsoft 支援服務](data-box-disk-contact-microsoft-support.md)。

## <a name="next-steps"></a>後續步驟

- 深入了解如何[透過 Azure 入口網站管理資料箱磁碟](data-box-portal-ui-admin.md)。
