---
title: 在 Azure Data Lake Storage Gen2 （預覽）中使用 PowerShell for files & Acl
description: 使用 PowerShell Cmdlet 來管理已啟用階層命名空間（HNS）之儲存體帳戶中的目錄和檔案和目錄存取控制清單（ACL）。
services: storage
author: normesta
ms.service: storage
ms.subservice: data-lake-storage-gen2
ms.topic: conceptual
ms.date: 11/24/2019
ms.author: normesta
ms.reviewer: prishet
ms.openlocfilehash: 71f90fb361e8fc45ee2ce8672990965fca801a49
ms.sourcegitcommit: 85e7fccf814269c9816b540e4539645ddc153e6e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/26/2019
ms.locfileid: "74533937"
---
# <a name="use-powershell-for-files--acls-in-azure-data-lake-storage-gen2-preview"></a>在 Azure Data Lake Storage Gen2 （預覽）中使用 PowerShell for files & Acl

本文說明如何使用 PowerShell 來建立和管理已啟用階層命名空間（HNS）之儲存體帳戶中的目錄、檔案和許可權。 

> [!IMPORTANT]
> 本文中所述的 PowerShell 模組目前為公開預覽狀態。

[Gen1 至 Gen2 對應](#gen1-gen2-map) | [提供意見](https://github.com/Azure/azure-powershell/issues)反應

## <a name="prerequisites"></a>先決條件

> [!div class="checklist"]
> * Azure 訂閱。 請參閱 [取得 Azure 免費試用](https://azure.microsoft.com/pricing/free-trial/)。
> * 已啟用階層命名空間（HNS）的儲存體帳戶。 請遵循[這些](data-lake-storage-quickstart-create-account.md)指示來建立一個。
> * .NET Framework 已安裝4.7.2 或更新版本。 請參閱[下載 .NET Framework](https://dotnet.microsoft.com/download/dotnet-framework)。
> * PowerShell 版本 `5.1` 或更高版本。

## <a name="install-powershell-modules"></a>安裝 PowerShell 模組

1. 使用下列命令，確認已安裝的 PowerShell 版本 `5.1` 或更高版本。 

    ```powershell
    echo $PSVersionTable.PSVersion.ToString() 
    ```
    
    若要升級您的 PowerShell 版本，請參閱[升級現有的 Windows PowerShell](https://docs.microsoft.com/powershell/scripting/install/installing-windows-powershell?view=powershell-6#upgrading-existing-windows-powershell)
    
2. 安裝最新的**PowershellGet**模組。 然後，關閉並重新開啟 Powershell 主控台。

    ```powershell
    install-Module PowerShellGet –Repository PSGallery –Force 
    ```

3.  安裝**Az. Storage** preview 模組。

    ```powershell
    install-Module Az.Storage -Repository PSGallery -RequiredVersion 1.9.1-preview –AllowPrerelease –AllowClobber –Force 
    ```

    如需有關如何安裝 PowerShell 模組的詳細資訊，請參閱[安裝 Azure PowerShell 模組](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.0.0)。

## <a name="connect-to-the-account"></a>連接到帳戶

1. 開啟 Windows PowerShell 命令視窗。

2. 使用 `Connect-AzAccount` 命令登入 Azure 訂用帳戶並遵循畫面上的指示。

   ```powershell
   Connect-AzAccount
   ```

3. 如果您的身分識別與多個訂用帳戶相關聯，則請將您的使用中訂用帳戶設定為您要在其中建立和管理目錄之儲存體帳戶的訂用帳戶。

   ```powershell
   Select-AzSubscription -SubscriptionId <subscription-id>
   ```

   以您訂用帳戶的識別碼取代 `<subscription-id>` 的預留位置值。

4. 取得儲存體帳戶。

   ```powershell
   $storageAccount = Get-AzStorageAccount -ResourceGroupName "<resource-group-name>" -AccountName "<storage-account-name>"
   ```

   * 以您的資源組名取代 `<resource-group-name>` 的預留位置值。

   * 使用您的儲存體帳戶名稱取代 `<storage-account-name>` 預留位置值。

5. 取得儲存體帳戶內容。

   ```powershell
   $ctx = $storageAccount.Context
   ```

## <a name="create-a-file-system"></a>建立檔案系統

檔案系統作為檔案的容器。 您可以使用 `New-AzDatalakeGen2FileSystem` Cmdlet 來建立一個。 

這個範例會建立名為 `my-file-system`的檔案系統。

```powershell
$filesystemName = "my-file-system"
New-AzDatalakeGen2FileSystem -Context $ctx -Name $filesystemName
```

## <a name="create-a-directory"></a>建立目錄

使用 `New-AzDataLakeGen2Item` Cmdlet 建立目錄參考。 

這個範例會將名為 `my-directory` 的目錄新增至檔案系統。

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
New-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname -Directory
```

這個範例會新增相同的目錄，但也會設定許可權、umask、屬性值和中繼資料值。 

```powershell
$dir = New-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname -Directory -Permission rwxrwxrwx -Umask ---rwx---  -Property @{"ContentEncoding" = "UDF8"; "CacheControl" = "READ"} -Metadata  @{"tag1" = "value1"; "tag2" = "value2" }
```

## <a name="show-directory-properties"></a>顯示目錄屬性

這個範例會使用 `Get-AzDataLakeGen2Item` Cmdlet 取得目錄，然後將屬性值列印到主控台。

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
$dir =  Get-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname
$dir.ACL
$dir.Permissions
$dir.Directory.PathProperties.Group
$dir.Directory.PathProperties.Owner
$dir.Directory.Metadata
$dir.Directory.Properties
```

## <a name="rename-or-move-a-directory"></a>重新命名或移動目錄

使用 `Move-AzDataLakeGen2Item` Cmdlet 來重新命名或移動目錄。

這個範例會將目錄從名稱 `my-directory` 重新命名為 `my-new-directory`的名稱。

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
$dirname2 = "my-new-directory/"
Move-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname -DestFileSystem $filesystemName -DestPath $dirname2
```

這個範例會將名為 `my-directory` 的目錄移到名為 `my-subdirectory``my-directory-2` 的子目錄。 這個範例也會將 umask 套用至子目錄。

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
$dirname2 = "my-directory-2/my-subdirectory/"
Move-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname1 -DestFileSystem $filesystemName -DestPath $dirname2 -Umask --------x -PathRenameMode Posix
```

## <a name="delete-a-directory"></a>刪除目錄

使用 `Remove-AzDataLakeGen2Item` Cmdlet 來刪除目錄。

這個範例會刪除名為 `my-directory`的目錄。 

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
Remove-AzDataLakeGen2Item  -Context $ctx -FileSystem $filesystemName -Path $dirname 
```

您可以使用 `-Force` 參數來移除檔案，而不會出現提示。

## <a name="download-from-a-directory"></a>從目錄下載

使用 `Get-AzDataLakeGen2ItemContent` Cmdlet 從目錄下載檔案。

這個範例會從名為 `my-directory`的目錄下載名為 `upload.txt` 的檔案。 

```powershell
$filesystemName = "my-file-system"
$filePath = "my-directory/upload.txt"
$downloadFilePath = "download.txt"
Get-AzDataLakeGen2ItemContent -Context $ctx -FileSystem $filesystemName -Path $filePath -Destination $downloadFilePath
```

## <a name="list-directory-contents"></a>列出目錄內容

使用 `Get-AzDataLakeGen2ChildItem` Cmdlet 來列出目錄的內容。

這個範例會列出名為 `my-directory`的目錄內容。 

若要列出檔案系統的內容，請省略命令中的 `-Path` 參數。

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
Get-AzDataLakeGen2ChildItem -Context $ctx -FileSystem $filesystemName -Path $dirname
```

這個範例會列出名為 `my-directory` 的目錄內容，並在清單中包含 Acl。 它也會使用 `-Recurse` 參數來列出所有子目錄的內容。

若要列出檔案系統的內容，請省略命令中的 `-Path` 參數。

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
Get-AzDataLakeGen2ChildItem -Context $ctx -FileSystem $filesystemName -Path $dirname -Recurse -FetchPermission
```

## <a name="upload-a-file-to-a-directory"></a>將檔案上傳到目錄

使用 `New-AzDataLakeGen2Item` Cmdlet，將檔案上傳到目錄。

這個範例會將名為 `upload.txt` 的檔案上傳至名為 `my-directory`的目錄。 

```powershell
$localSrcFile =  "upload.txt"
$filesystemName = "my-file-system"
$dirname = "my-directory/"
$destPath = $dirname + (Get-Item $localSrcFile).Name
New-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $destPath -Source $localSrcFile -Force 
```

這個範例會上傳相同的檔案，但是會設定目的地檔案的許可權、umask、屬性值和中繼資料值。 這個範例也會將這些值列印到主控台。

```powershell
$file = New-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $destPath -Source $localSrcFile -Permission rwxrwxrwx -Umask ---rwx--- -Property @{"ContentEncoding" = "UDF8"; "CacheControl" = "READ"} -Metadata  @{"tag1" = "value1"; "tag2" = "value2" }
$file1
$file1.File.Metadata
$file1.File.Properties
```

## <a name="show-file-properties"></a>顯示檔案屬性

這個範例會使用 `Get-AzDataLakeGen2Item` Cmdlet 來取得檔案，然後將屬性值列印到主控台。

```powershell
$filepath =  "my-directory/upload.txt"
$filesystemName = "my-file-system"
$file = Get-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $filepath
$file
$file.ACL
$file.Permissions
$file.File.PathProperties.Group
$file.File.PathProperties.Owner
$file.File.Metadata
$file.File.Properties
```

## <a name="delete-a-file"></a>刪除檔案

使用 `Remove-AzDataLakeGen2Item` Cmdlet 來刪除檔案。

這個範例會刪除名為 `upload.txt`的檔案。 

```powershell
$filesystemName = "my-file-system"
$filepath = "upload.txt"
Remove-AzDataLakeGen2Item  -Context $ctx -FileSystem $filesystemName -Path $filepath 
```

您可以使用 `-Force` 參數來移除檔案，而不會出現提示。

## <a name="manage-access-permissions"></a>管理存取權限

您可以取得、設定及更新目錄和檔案的存取權限。

### <a name="get-directory-and-file-permissions"></a>取得目錄和檔案許可權

使用 `Get-AzDataLakeGen2Item`Cmdlet 取得目錄或檔案的 ACL。

這個範例會取得**目錄**的 acl，然後將 acl 列印到主控台。

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
$dir = Get-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname
$dir.ACL
```

這個範例會取得**檔案的 acl，然後**將 acl 列印到主控台。

```powershell
$filePath = "my-directory/upload.txt"
$file = Get-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $filePath
$file.ACL
```

下圖顯示取得目錄的 ACL 之後的輸出。

![取得 ACL 輸出](./media/data-lake-storage-directory-file-acl-powershell/get-acl.png)

在此範例中，擁有使用者具有 [讀取]、[寫入] 和 [執行] 許可權。 擁有群組只有「讀取」和「執行」許可權。 如需存取控制清單的詳細資訊，請參閱[Azure Data Lake Storage Gen2 中的存取控制](data-lake-storage-access-control.md)。

### <a name="set-directory-and-file-permissions"></a>設定目錄和檔案許可權

使用 `New-AzDataLakeGen2ItemAclObject` Cmdlet 來建立擁有使用者、擁有群組或其他使用者的 ACL。 然後，使用 `Update-AzDataLakeGen2Item` Cmdlet 來認可 ACL。

這個範例會在擁有使用者、擁有群組或其他使用者的**目錄**上設定 acl，然後將 acl 列印到主控台。

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
$acl = New-AzDataLakeGen2ItemAclObject -AccessControlType user -Permission rw- 
$acl = New-AzDataLakeGen2ItemAclObject -AccessControlType group -Permission rw- -InputObject $acl 
$acl = New-AzDataLakeGen2ItemAclObject -AccessControlType other -Permission "-wx" -InputObject $acl
Update-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname -Acl $acl
$dir = Get-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname
$dir.ACL
```
這個範例會針對擁有使用者、擁有群組或其他使用者 **，在檔案**上設定 acl，然後將 acl 列印到主控台。

```powershell
$filesystemName = "my-file-system"
$filePath = "my-directory/upload.txt"
$acl = New-AzDataLakeGen2ItemAclObject -AccessControlType user -Permission rw- 
$acl = New-AzDataLakeGen2ItemAclObject -AccessControlType group -Permission rw- -InputObject $acl 
$acl = New-AzDataLakeGen2ItemAclObject -AccessControlType other -Permission "-wx" -InputObject $acl
Update-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $filePath -Acl $acl
$file = Get-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $filePath
$file.ACL
```

下圖顯示設定檔案的 ACL 後的輸出。

![取得 ACL 輸出](./media/data-lake-storage-directory-file-acl-powershell/set-acl.png)

在此範例中，擁有使用者和擁有群組只有「讀取」和「寫入」許可權。 所有其他使用者都具有 [寫入] 和 [執行] 許可權。 如需存取控制清單的詳細資訊，請參閱[Azure Data Lake Storage Gen2 中的存取控制](data-lake-storage-access-control.md)。

### <a name="update-directory-and-file-permissions"></a>更新目錄和檔案許可權

使用 `Get-AzDataLakeGen2Item` Cmdlet 取得目錄或檔案的 ACL。 然後，使用 `New-AzDataLakeGen2ItemAclObject` Cmdlet 來建立新的 ACL 專案。 使用 `Update-AzDataLakeGen2Item` Cmdlet 來套用新的 ACL。

這個範例會為使用者提供目錄的寫入和執行許可權。

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
$Id = "xxxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
$acl = (Get-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname).ACL
$acl = New-AzDataLakeGen2ItemAclObject -AccessControlType user -EntityId $id -Permission "-wx" -InputObject $acl
Update-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname -Acl $acl
```
這個範例會為使用者提供檔案的寫入和執行許可權。

```powershell
$filesystemName = "my-file-system"
$fileName = "my-directory/upload.txt"
$Id = "xxxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
$acl = (Get-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $fileName).ACL
$acl = New-AzDataLakeGen2ItemAclObject -AccessControlType user -EntityId $id -Permission "-wx" -InputObject $acl
Update-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $fileName -Acl $acl
```

### <a name="set-permissions-on-all-items-in-a-file-system"></a>在檔案系統中設定所有專案的許可權

您可以使用 `Get-AzDataLakeGen2Item` 和 `-Recurse` 參數搭配 `Update-AzDataLakeGen2Item` Cmdlet，以遞迴方式設定檔案系統中所有目錄和檔案的 ACL。 

```powershell
$filesystemName = "my-file-system"
$acl = New-AzDataLakeGen2ItemAclObject -AccessControlType user -Permission rw- 
$acl = New-AzDataLakeGen2ItemAclObject -AccessControlType group -Permission rw- -InputObject $acl 
$acl = New-AzDataLakeGen2ItemAclObject -AccessControlType other -Permission "-wx" -InputObject $acl
Get-AzDataLakeGen2ChildItem -Context $ctx -FileSystem $filesystemName -Recurse | Update-AzDataLakeGen2Item -Acl $acl
```
<a id="gen1-gen2-map" />

## <a name="gen1-to-gen2-mapping"></a>Gen1 至 Gen2 對應

下表顯示用於 Data Lake Storage Gen1 的 Cmdlet 對應至 Data Lake Storage Gen2 之 Cmdlet 的方式。

|Data Lake Storage Gen1 Cmdlet| Data Lake Storage Gen2 Cmdlet|
|--------|---------|
|AzDataLakeStoreChildItem|AzDataLakeGen2ChildItem|
|AzDataLakeStoreItem <br>AzDataLakeStoreItemAclEntry<br>AzDataLakeStoreItemOwner<br>AzDataLakeStoreItemPermission<br>AzDataLakeStoreItemContent<br>新增-AzDataLakeStoreItem|AzDataLakeGen2Item|
|AzDataLakeStoreItemContent|新增-AzDataLakeGen2Item|
|移動-AzDataLakeStoreItem|移動-AzDataLakeGen2Item|
|移除-AzDataLakeStoreItem|移除-AzDataLakeGen2Item|
|設定-AzDataLakeStoreItemOwner <br>設定-AzDataLakeStoreItemPermission<br>設定-AzDataLakeStoreItemPermission<br>設定-AzDataLakeStoreItemAcl|更新-AzDataLakeGen2Item|

## <a name="see-also"></a>另請參閱

* [已知問題](data-lake-storage-known-issues.md#api-scope-data-lake-client-library)
* [搭配 Azure 儲存體使用 Azure PowerShell](../common/storage-powershell-guide-full.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)
* [儲存體 PowerShell Cmdlet](/powershell/module/az.storage)。

