---
title: 使用 AzCopy v10 在 Azure Blob 儲存體之間傳輸資料 |Microsoft Docs
description: 本文包含 AzCopy 範例命令的集合，可協助您建立容器、複製檔案，以及在本機檔案系統和容器之間同步處理目錄。
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 10/22/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: dineshm
ms.openlocfilehash: 78e3f1d4f457e041d386ac7754d089b8b3635b08
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/06/2019
ms.locfileid: "73686646"
---
# <a name="transfer-data-with-azcopy-and-blob-storage"></a>使用 AzCopy 和 Blob 儲存體傳輸資料

AzCopy 是命令列公用程式，可讓您在儲存體帳戶之間複製資料。 本文包含可搭配 Blob 儲存體使用的範例命令。

## <a name="get-started"></a>開始使用

請參閱[開始使用 AzCopy](storage-use-azcopy-v10.md)一文以下載 AzCopy，並瞭解您可以為儲存體服務提供授權認證的方式。

> [!NOTE]
> 本文中的範例假設您已使用 `AzCopy login` 命令驗證您的身分識別。 然後，AzCopy 會使用您的 Azure AD 帳戶來授權存取 Blob 儲存體中的資料。
>
> 如果您想要使用 SAS 權杖來授權 blob 資料的存取權，您可以在每個 AzCopy 命令中，將該權杖附加至資源 URL。
>
> 例如： `'https://<storage-account-name>.blob.core.windows.net/<container-name>?<SAS-token>'`。

## <a name="create-a-container"></a>建立容器

> [!TIP]
> 本節中的範例會以單引號（' '）括住路徑引數。 在所有命令 shell 中使用單引號，但 Windows 命令介面（cmd.exe）除外。 如果您使用 Windows 命令 Shell （cmd.exe），請將路徑引數括在雙引號（""），而不是單引號（' '）。

您可以使用[azcopy make](storage-ref-azcopy-make.md)命令來建立容器。 本節中的範例會建立名為 `mycontainer` 的容器。

|    |     |
|--------|-----------|
| **語法** | `azcopy make 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>'` |
| **範例** | `azcopy make 'https://mystorageaccount.blob.core.windows.net/mycontainer'` |
| **範例**（階層式命名空間） | `azcopy make 'https://mystorageaccount.dfs.core.windows.net/mycontainer'` |

如需詳細的參考檔，請參閱[azcopy make](storage-ref-azcopy-make.md)。

## <a name="upload-files"></a>上傳檔案

您可以使用[azcopy copy](storage-ref-azcopy-copy.md)命令，從本機電腦上傳檔案和目錄。

本區段包含下列範例：

> [!div class="checklist"]
> * 上傳檔案
> * 上傳目錄
> * 上傳目錄的內容 
> * 上傳特定檔案

如需詳細的參考檔，請參閱[azcopy copy](storage-ref-azcopy-copy.md)。

> [!TIP]
> 本節中的範例會以單引號（' '）括住路徑引數。 在所有命令 shell 中使用單引號，但 Windows 命令介面（cmd.exe）除外。 如果您使用 Windows 命令 Shell （cmd.exe），請將路徑引數括在雙引號（""），而不是單引號（' '）。

### <a name="upload-a-file"></a>上傳檔案

|    |     |
|--------|-----------|
| **語法** | `azcopy copy '<local-file-path>' 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<blob-name>'` |
| **範例** | `azcopy copy 'C:\myDirectory\myTextFile.txt' 'https://mystorageaccount.blob.core.windows.net/mycontainer/myTextFile.txt'` |
| **範例**（階層式命名空間） | `azcopy copy 'C:\myDirectory\myTextFile.txt' 'https://mystorageaccount.dfs.core.windows.net/mycontainer/myTextFile.txt'` |

您也可以在檔案路徑或檔案名中的任何位置使用萬用字元符號（*）上傳檔案。 例如： `'C:\myDirectory\*.txt'`，或 `C:\my*\*.txt`。

> [!NOTE]
> 根據預設，AzCopy 會將資料上傳至區塊 blob。 若要將檔案上傳為附加 Blob 或分頁 Blob，請使用旗標 `--blob-type=[BlockBlob|PageBlob|AppendBlob]`。

### <a name="upload-a-directory"></a>上傳目錄

這個範例會將目錄（以及該目錄中的所有檔案）複製到 blob 容器。 結果是容器中具有相同名稱的目錄。

|    |     |
|--------|-----------|
| **語法** | `azcopy copy '<local-directory-path>' 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>' --recursive` |
| **範例** | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer' --recursive` |
| **範例**（階層式命名空間） | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.dfs.core.windows.net/mycontainer' --recursive` |

若要複製到容器內的目錄，只要在命令字串中指定該目錄的名稱即可。

|    |     |
|--------|-----------|
| **範例** | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer/myBlobDirectory' --recursive` |
| **範例**（階層式命名空間） | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.dfs.core.windows.net/mycontainer/myBlobDirectory' --recursive` |

如果您指定的目錄名稱不存在於容器中，AzCopy 會依該名稱建立新的目錄。

### <a name="upload-the-contents-of-a-directory"></a>上傳目錄的內容

您可以上傳目錄的內容，而不需使用萬用字元符號（*）來複製包含目錄本身。

|    |     |
|--------|-----------|
| **語法** | `azcopy copy '<local-directory-path>\*' 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<directory-path>` |
| **範例** | `azcopy copy 'C:\myDirectory\*' 'https://mystorageaccount.blob.core.windows.net/mycontainer/myBlobDirectory'` |
| **範例**（階層式命名空間） | `azcopy copy 'C:\myDirectory\*' 'https://mystorageaccount.dfs.core.windows.net/mycontainer/myBlobDirectory'` |

> [!NOTE]
> 附加 `--recursive` 旗標，以上傳所有子目錄中的檔案。

### <a name="upload-specific-files"></a>上傳特定檔案

您可以指定完整的檔案名，或使用包含萬用字元（*）的部分名稱。

#### <a name="specify-multiple-complete-file-names"></a>指定多個完整的檔案名

使用[azcopy copy](storage-ref-azcopy-copy.md)命令搭配 `--include-path` 選項。 使用分號（`;`）來分隔個別的檔案名。

|    |     |
|--------|-----------|
| **語法** | `azcopy copy '<local-directory-path>' 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>' --include-path <semicolon-separated-file-list>` |
| **範例** | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer' --include-path 'photos;documents\myFile.txt' --recursive` |
| **範例**（階層式命名空間） | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.dfs.core.windows.net/mycontainer' --include-path 'photos;documents\myFile.txt' --recursive` |

在此範例中，AzCopy 會傳輸 `C:\myDirectory\photos` 目錄和 `C:\myDirectory\documents\myFile.txt` 檔案。 您必須包含 `--recursive` 選項，才能傳送 `C:\myDirectory\photos` 目錄中的所有檔案。

您也可以使用 `--exclude-path` 選項來排除檔案。 若要深入瞭解，請參閱[azcopy 複製](storage-ref-azcopy-copy.md)參考檔。

#### <a name="use-wildcard-characters"></a>使用萬用字元

使用[azcopy copy](storage-ref-azcopy-copy.md)命令搭配 `--include-pattern` 選項。 指定包含萬用字元的部分名稱。 使用 semicolin （`;`）來分隔名稱。 

|    |     |
|--------|-----------|
| **語法** | `azcopy copy '<local-directory-path>' 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>' --include-pattern <semicolon-separated-file-list-with-wildcard-characters>` |
| **範例** | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer' --include-pattern 'myFile*.txt;*.pdf*'` |
| **範例**（階層式命名空間） | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.dfs.core.windows.net/mycontainer' --include-pattern 'myFile*.txt;*.pdf*'` |

您也可以使用 `--exclude-pattern` 選項來排除檔案。 若要深入瞭解，請參閱[azcopy 複製](storage-ref-azcopy-copy.md)參考檔。

[`--include-pattern`] 和 [`--exclude-pattern`] 選項僅適用于檔案名，而不會套用至路徑。  如果您想要複製存在於目錄樹狀結構中的所有文字檔，請使用 `–recursive` 選項來取得整個目錄樹狀結構，然後使用 `–include-pattern` 並指定 `*.txt` 來取得所有文字檔。

## <a name="download-files"></a>下載檔案

您可以使用[azcopy copy](storage-ref-azcopy-copy.md)命令，將 blob、目錄和容器下載至您的本機電腦。

本區段包含下列範例：

> [!div class="checklist"]
> * 下載檔案
> * 下載目錄
> * 下載目錄的內容
> * 下載特定檔案

> [!NOTE]
> 如果 blob 的 `Content-md5` 屬性值包含雜湊，則 AzCopy 會計算已下載資料的 MD5 雜湊，並驗證儲存在 blob 的 `Content-md5` 屬性中的 MD5 雜湊是否符合計算的雜湊。 如果這些值不相符，則下載會失敗，除非您將 `--check-md5=NoCheck` 或 `--check-md5=LogOnly` 附加至複製命令來覆寫此行為。

如需詳細的參考檔，請參閱[azcopy copy](storage-ref-azcopy-copy.md)。

> [!TIP]
> 本節中的範例會以單引號（' '）括住路徑引數。 在所有命令 shell 中使用單引號，但 Windows 命令介面（cmd.exe）除外。 如果您使用 Windows 命令 Shell （cmd.exe），請將路徑引數括在雙引號（""），而不是單引號（' '）。

### <a name="download-a-file"></a>下載檔案

|    |     |
|--------|-----------|
| **語法** | `azcopy copy 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<blob-path>' '<local-file-path>'` |
| **範例** | `azcopy copy 'https://mystorageaccount.blob.core.windows.net/mycontainer/myTextFile.txt' 'C:\myDirectory\myTextFile.txt'` |
| **範例**（階層式命名空間） | `azcopy copy 'https://mystorageaccount.dfs.core.windows.net/mycontainer/myTextFile.txt' 'C:\myDirectory\myTextFile.txt'` |

### <a name="download-a-directory"></a>下載目錄

|    |     |
|--------|-----------|
| **語法** | `azcopy copy 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<directory-path>' '<local-directory-path>' --recursive` |
| **範例** | `azcopy copy 'https://mystorageaccount.blob.core.windows.net/mycontainer/myBlobDirectory' 'C:\myDirectory'  --recursive` |
| **範例**（階層式命名空間） | `azcopy copy 'https://mystorageaccount.dfs.core.windows.net/mycontainer/myBlobDirectory 'C:\myDirectory'  --recursive` |

這個範例會產生名為 `C:\myDirectory\myBlobDirectory` 的目錄，其中包含所有下載的檔案。

### <a name="download-the-contents-of-a-directory"></a>下載目錄的內容

您可以下載目錄的內容，而不需使用萬用字元符號（*）複製包含的目錄本身。

> [!NOTE]
> 目前，只有沒有階層式命名空間的帳戶才支援此案例。

|    |     |
|--------|-----------|
| **語法** | `azcopy copy 'https://<storage-account-name>.blob.core.windows.net/<container-name>/*' '<local-directory-path>/'` |
| **範例** | `azcopy copy 'https://mystorageaccount.blob.core.windows.net/mycontainer/myBlobDirectory/*' 'C:\myDirectory'` |

> [!NOTE]
> 附加 `--recursive` 旗標，以下載所有子目錄中的檔案。

### <a name="download-specific-files"></a>下載特定檔案

您可以指定完整的檔案名，或使用包含萬用字元（*）的部分名稱。

#### <a name="specify-multiple-complete-file-names"></a>指定多個完整的檔案名

使用[azcopy copy](storage-ref-azcopy-copy.md)命令搭配 `--include-path` 選項。 使用 semicolin （`;`）來分隔個別的檔案名。

|    |     |
|--------|-----------|
| **語法** | `azcopy copy 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-or-directory-name>' '<local-directory-path>'  --include-path <semicolon-separated-file-list>` |
| **範例** | `azcopy copy 'https://mystorageaccount.blob.core.windows.net/mycontainer/FileDirectory' 'C:\myDirectory'  --include-path 'photos;documents\myFile.txt' --recursive` |
| **範例**（階層式命名空間） | `azcopy copy 'https://mystorageaccount.dfs.core.windows.net/mycontainer/FileDirectory' 'C:\myDirectory'  --include-path 'photos;documents\myFile.txt'--recursive` |

在此範例中，AzCopy 會傳輸 `https://mystorageaccount.blob.core.windows.net/mycontainer/FileDirectory/photos` 目錄和 `https://mystorageaccount.blob.core.windows.net/mycontainer/FileDirectory/documents/myFile.txt` 檔案。 您必須包含 `--recursive` 選項，才能傳送 `https://mystorageaccount.blob.core.windows.net/mycontainer/FileDirectory/photos` 目錄中的所有檔案。

您也可以使用 `--exclude-path` 選項來排除檔案。 若要深入瞭解，請參閱[azcopy 複製](storage-ref-azcopy-copy.md)參考檔。

#### <a name="use-wildcard-characters"></a>使用萬用字元

使用[azcopy copy](storage-ref-azcopy-copy.md)命令搭配 `--include-pattern` 選項。 指定包含萬用字元的部分名稱。 使用 semicolin （`;`）來分隔名稱。

|    |     |
|--------|-----------|
| **語法** | `azcopy copy 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-or-directory-name>' '<local-directory-path>' --include-pattern <semicolon-separated-file-list-with-wildcard-characters>` |
| **範例** | `azcopy copy 'https://mystorageaccount.blob.core.windows.net/mycontainer/FileDirectory' 'C:\myDirectory'  --include-pattern 'myFile*.txt;*.pdf*'` |
| **範例**（階層式命名空間） | `azcopy copy 'https://mystorageaccount.dfs.core.windows.net/mycontainer/FileDirectory' 'C:\myDirectory'  --include-pattern 'myFile*.txt;*.pdf*'` |

您也可以使用 `--exclude-pattern` 選項來排除檔案。 若要深入瞭解，請參閱[azcopy 複製](storage-ref-azcopy-copy.md)參考檔。

[`--include-pattern`] 和 [`--exclude-pattern`] 選項僅適用于檔案名，而不會套用至路徑。  如果您想要複製存在於目錄樹狀結構中的所有文字檔，請使用 `–recursive` 選項來取得整個目錄樹狀結構，然後使用 `–include-pattern` 並指定 `*.txt` 來取得所有文字檔。

## <a name="copy-blobs-between-storage-accounts"></a>在儲存體帳戶之間複製 blob

您可以使用 AzCopy 將 blob 複製到其他儲存體帳戶。 複製作業是同步的，因此當命令傳回時，表示已複製所有檔案。 

AzCopy 會使用[伺服器對伺服器](https://docs.microsoft.com/rest/api/storageservices/put-block-from-url) [api](https://docs.microsoft.com/rest/api/storageservices/put-page-from-url)，因此資料會直接複製到存放伺服器之間。 這些複製作業不會使用您電腦的網路頻寬。 您可以藉由設定 `AZCOPY_CONCURRENCY_VALUE` 環境變數的值來增加這些作業的輸送量。 若要深入瞭解，請參閱將[輸送量優化](storage-use-azcopy-configure.md#optimize-throughput)。

> [!NOTE]
> 此案例在目前版本中具有下列限制。
>
> - 僅支援沒有階層式命名空間的帳戶。
> - 您必須將 SAS 權杖附加至每個來源 URL。 如果您使用 Azure Active Directory （AD）提供授權認證，則只能從目的地 URL 省略 SAS 權杖。
>-  Premium 區塊 blob 儲存體帳戶不支援存取層。 藉由將 `s2s-preserve-access-tier` 設定為 `false`，從複製作業中省略 blob 的存取層（例如： `--s2s-preserve-access-tier=false`）。

本區段包含下列範例：

> [!div class="checklist"]
> * 將 blob 複製到另一個儲存體帳戶
> * 將目錄複寫到另一個儲存體帳戶
> * 將容器複製到另一個儲存體帳戶
> * 將所有容器、目錄和檔案複製到另一個儲存體帳戶

如需詳細的參考檔，請參閱[azcopy copy](storage-ref-azcopy-copy.md)。

> [!TIP]
> 本節中的範例會以單引號（' '）括住路徑引數。 在所有命令 shell 中使用單引號，但 Windows 命令介面（cmd.exe）除外。 如果您使用 Windows 命令 Shell （cmd.exe），請將路徑引數括在雙引號（""），而不是單引號（' '）。

### <a name="copy-a-blob-to-another-storage-account"></a>將 blob 複製到另一個儲存體帳戶

|    |     |
|--------|-----------|
| **語法** | `azcopy copy 'https://<source-storage-account-name>.blob.core.windows.net/<container-name>/<blob-path>?<SAS-token>' 'https://<destination-storage-account-name>.blob.core.windows.net/<container-name>/<blob-path>'` |
| **範例** | `azcopy copy 'https://mysourceaccount.blob.core.windows.net/mycontainer/myTextFile.txt?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net/mycontainer/myTextFile.txt'` |

### <a name="copy-a-directory-to-another-storage-account"></a>將目錄複寫到另一個儲存體帳戶

|    |     |
|--------|-----------|
| **語法** | `azcopy copy 'https://<source-storage-account-name>.blob.core.windows.net/<container-name>/<directory-path>?<SAS-token>' 'https://<destination-storage-account-name>.blob.core.windows.net/<container-name>' --recursive` |
| **範例** | `azcopy copy 'https://mysourceaccount.blob.core.windows.net/mycontainer/myBlobDirectory?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net/mycontainer' --recursive` |

### <a name="copy-a-container-to-another-storage-account"></a>將容器複製到另一個儲存體帳戶

|    |     |
|--------|-----------|
| **語法** | `azcopy copy 'https://<source-storage-account-name>.blob.core.windows.net/<container-name>?<SAS-token>' 'https://<destination-storage-account-name>.blob.core.windows.net/<container-name>' --recursive` |
| **範例** | `azcopy copy 'https://mysourceaccount.blob.core.windows.net/mycontainer?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net/mycontainer' --recursive` |

### <a name="copy-all-containers-directories-and-blobs-to-another-storage-account"></a>將所有容器、目錄和 blob 複製到另一個儲存體帳戶

|    |     |
|--------|-----------|
| **語法** | `azcopy copy 'https://<source-storage-account-name>.blob.core.windows.net/?<SAS-token>' 'https://<destination-storage-account-name>.blob.core.windows.net/' --recursive'` |
| **範例** | `azcopy copy 'https://mysourceaccount.blob.core.windows.net?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net' --recursive` |

## <a name="synchronize-files"></a>同步處理檔案

您可以同步處理本機檔案系統與 blob 容器的內容。 您也可以彼此同步處理容器和虛擬目錄。 同步處理是單向的。 換句話說，您可以選擇這兩個端點中的哪一個是來源，以及哪一個是目的地。 同步處理也會使用伺服器到伺服器 Api。

> [!NOTE]
> 目前，只有沒有階層式命名空間的帳戶才支援此案例。 目前的 AzCopy 版本不會在其他來源和目的地之間同步處理（例如：檔案儲存體或 Amazon Web Services （AWS） S3 bucket）。

[同步](storage-ref-azcopy-sync.md)命令會比較檔案名和上次修改的時間戳記。 將 [`--delete-destination` 選擇性] 旗標設定為 [`true`] 或 [`prompt`]，以刪除目的地目錄中的檔案（如果這些檔案不再存在於來原始目錄中）。

如果您將 `--delete-destination` 旗標設定為 `true` AzCopy 會刪除檔案，而不會提供提示。 如果您想要在 AzCopy 刪除檔案之前出現提示，請將 `--delete-destination` 旗標設定為 `prompt`。

> [!NOTE]
> 若要防止意外刪除，請務必先啟用「虛[刪除](https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete)」功能，然後再使用 `--delete-destination=prompt|true` 旗標。

如需詳細的參考檔，請參閱[azcopy sync](storage-ref-azcopy-sync.md)。

> [!TIP]
> 本節中的範例會以單引號（' '）括住路徑引數。 在所有命令 shell 中使用單引號，但 Windows 命令介面（cmd.exe）除外。 如果您使用 Windows 命令 Shell （cmd.exe），請將路徑引數括在雙引號（""），而不是單引號（' '）。

### <a name="update-a-container-with-changes-to-a-local-file-system"></a>使用本機檔案系統的變更來更新容器

在此情況下，容器是目的地，而本機檔案系統則是來源。

|    |     |
|--------|-----------|
| **語法** | `azcopy sync '<local-directory-path>' 'https://<storage-account-name>.blob.core.windows.net/<container-name>' --recursive` |
| **範例** | `azcopy sync 'C:\myDirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer' --recursive` |

### <a name="update-a-local-file-system-with-changes-to-a-container"></a>以容器的變更來更新本機檔案系統

在此情況下，本機檔案系統是目的地，而容器是來源。

|    |     |
|--------|-----------|
| **語法** | `azcopy sync 'https://<storage-account-name>.blob.core.windows.net/<container-name>' 'C:\myDirectory' --recursive` |
| **範例** | `azcopy sync 'https://mystorageaccount.blob.core.windows.net/mycontainer' 'C:\myDirectory' --recursive` |
|

### <a name="update-a-container-with-changes-in-another-container"></a>使用另一個容器中的變更來更新容器

出現在此命令中的第一個容器是來源。 第二個是目的地。

|    |     |
|--------|-----------|
| **語法** | `azcopy sync 'https://<source-storage-account-name>.blob.core.windows.net/<container-name>' 'https://<destination-storage-account-name>.blob.core.windows.net/<container-name>' --recursive` |
| **範例** | `azcopy sync 'https://mysourceaccount.blob.core.windows.net/mycontainer' 'https://mydestinationaccount.blob.core.windows.net/mycontainer' --recursive` |

### <a name="update-a-directory-with-changes-to-a-directory-in-another-file-share"></a>更新具有另一個檔案共用中目錄變更的目錄

出現在此命令中的第一個目錄是來源。 第二個是目的地。

|    |     |
|--------|-----------|
| **語法** | `azcopy sync 'https://<source-storage-account-name>.blob.core.windows.net/<container-name>/<directory-name>' 'https://<destination-storage-account-name>.blob.core.windows.net/<container-name>/<directory-name>' --recursive` |
| **範例** | `azcopy sync 'https://mysourceaccount.blob.core.windows.net/<container-name>/myDirectory' 'https://mydestinationaccount.blob.core.windows.net/mycontainer/myDirectory' --recursive` |

## <a name="next-steps"></a>後續步驟

在這些文章中尋找更多範例：

- [開始使用 AzCopy](storage-use-azcopy-v10.md)

- [教學課程：使用 AzCopy 將內部部署資料移轉至雲端儲存體](storage-use-azcopy-migrate-on-premises-data.md)

- [使用 AzCopy 和檔案儲存體轉送資料](storage-use-azcopy-files.md)

- [使用 AzCopy 和 Amazon S3 貯體轉送資料](storage-use-azcopy-s3.md)

- [對 AzCopy 進行設定、最佳化及疑難排解](storage-use-azcopy-configure.md)
