---
title: Azure Data Factory 中的 JSON 格式
description: 本主題描述如何在 Azure Data Factory 中處理 JSON 格式。
author: linda33wj
manager: shwang
ms.reviewer: craigg
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 02/05/2020
ms.author: jingwang
ms.openlocfilehash: 7dac8d21e3b45307284ece15ca5ddbcc69db909b
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/13/2020
ms.locfileid: "79260640"
---
# <a name="json-format-in-azure-data-factory"></a>Azure Data Factory 中的 JSON 格式

當您想要**剖析 json 檔案，或將資料寫入 json 格式**時，請遵循這篇文章。 

下列連接器支援 JSON 格式： [Amazon S3](connector-amazon-simple-storage-service.md)、 [azure Blob](connector-azure-blob-storage.md)、 [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md)、 [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md)、 [Azure 檔案儲存體](connector-azure-file-storage.md)、[檔案系統](connector-file-system.md)、 [FTP](connector-ftp.md)、 [Google Cloud Storage](connector-google-cloud-storage.md)、 [HDFS](connector-hdfs.md)、 [HTTP](connector-http.md)和[SFTP](connector-sftp.md)。

## <a name="dataset-properties"></a>資料集屬性

如需可用來定義資料集的區段和屬性完整清單，請參閱[資料集](concepts-datasets-linked-services.md)一文。 本節提供 JSON 資料集所支援的屬性清單。

| 屬性         | 描述                                                  | 必要 |
| ---------------- | ------------------------------------------------------------ | -------- |
| type             | 資料集的類型屬性必須設定為**Json**。 | 是      |
| location         | 檔案的位置設定。 每個以檔案為基礎的連接器在 `location`之下都有自己的位置類型和支援的屬性。 **請參閱連接器文章-> 資料集屬性一節中的詳細資訊**。 | 是      |
| encodingName     | 用來讀取/寫入測試檔案的編碼類型。 <br>允許的值如下： "UTF-8"、"UTF-16"、"UTF-UTF-16BE"、"UTF-32"、"32BE"、"US-ASCII"，"UTF-7"，"BIG5"，"EUC-JP"，"EUC-KR"，"GB2312"，"GB18030"，"JOHAB"，"SHIFT-JIS"，"CP875"，"CP866"，"IBM00858"，"IBM037"，"IBM273"，"IBM437"，"IBM500"，"IBM737", "IBM775", "IBM850", "IBM852", "IBM855", "IBM857", "IBM860", "IBM861", "IBM863", "IBM864", "IBM865", "IBM869", "IBM870", "IBM01140", "IBM01141", "IBM01142", "IBM01143", "IBM01144", "IBM01145", "IBM01146", "IBM01147", "IBM01148", "IBM01149"，"ISO-2022-JP"，"ISO-2022-KR"，"ISO-8859-1"，"ISO-8859-2"，"ISO-8859-3"，"ISO-8859-4"，"ISO-8859-5"，"ISO-8859-6"，"ISO-8859-7"，"ISO-8859-8"，"ISO-8859-9"，"ISO-8859-13"，"ISO-8859-15"，"WINDOWS-874"，"WINDOWS-1250"，"WINDOWS-1251"，"WINDOWS-1252 "，" WINDOWS-1253 "，" WINDOWS-1254 "，" WINDOWS-1255 "，" WINDOWS-1256 "，" WINDOWS-1257 "，" WINDOWS-1258 "。| 否       |
| compressionCodec | 用來讀取/寫入文字檔的壓縮編解碼器。 <br>允許的值為**bzip2**、 **gzip**、 **deflate**、 **ZipDeflate**、 **snappy**或**lz4**。 表示在儲存檔案時使用。 <br>注意：目前的複製活動不支援 "snappy" & "lz4"。<br>請注意，使用複製活動來解壓縮 ZipDeflate 檔案並寫入檔案型接收資料存放區時，檔案將會解壓縮到資料夾： `<path specified in dataset>/<folder named as source zip file>/`。 | 否       |
| compressionLevel | 壓縮比例。 <br>允許的值為**最佳**或**最快**。<br>- **最快：** 即使未以最佳方式壓縮所產生的檔案，壓縮作業還是應該儘快完成。<br>- **最佳**：即使作業需要較長的時間才能完成，壓縮作業也應以最佳方式壓縮。 如需詳細資訊，請參閱 [壓縮層級](https://msdn.microsoft.com/library/system.io.compression.compressionlevel.aspx) 主題。 | 否       |

以下是 Azure Blob 儲存體上的 JSON 資料集範例：

```json
{
    "name": "JSONDataset",
    "properties": {
        "type": "Json",
        "linkedServiceName": {
            "referenceName": "<Azure Blob Storage linked service name>",
            "type": "LinkedServiceReference"
        },
        "schema": [ < physical schema, optional, retrievable during authoring > ],
        "typeProperties": {
            "location": {
                "type": "AzureBlobStorageLocation",
                "container": "containername",
                "folderPath": "folder/subfolder",
            },
            "compression": {
                "type": "gzip"
        }
    }
}
```

## <a name="copy-activity-properties"></a>複製活動屬性

如需可用來定義活動的區段和屬性完整清單，請參閱[管線](concepts-pipelines-activities.md)一文。 本節提供 JSON 來源和接收所支援的屬性清單。

### <a name="json-as-source"></a>JSON 做為來源

複製活動***\*來源\**** 區段中支援下列屬性。

| 屬性      | 描述                                                  | 必要 |
| ------------- | ------------------------------------------------------------ | -------- |
| type          | 複製活動來源的類型屬性必須設定為**JSONSource**。 | 是      |
| storeSettings | 一組屬性，說明如何從資料存放區讀取資料。 每個以檔案為基礎的連接器在 `storeSettings`之下都有自己支援的讀取設定。 **請參閱連接器中的詳細資料文章-> 複製活動屬性一節**。 | 否       |

### <a name="json-as-sink"></a>JSON 做為接收

複製活動***\*接收\**** 區段中支援下列屬性。

| 屬性      | 描述                                                  | 必要 |
| ------------- | ------------------------------------------------------------ | -------- |
| type          | 複製活動來源的類型屬性必須設定為**JSONSink**。 | 是      |
| formatSettings | 屬性的群組。 請參閱下面的**JSON 寫入設定**表格。 | 否       |
| storeSettings | 一組屬性，說明如何將資料寫入資料存放區。 每個以檔案為基礎的連接器在 `storeSettings`之下都有它自己的支援寫入設定。 **請參閱連接器中的詳細資料文章-> 複製活動屬性一節**。 | 否       |

`formatSettings`下支援的**JSON 寫入設定**：

| 屬性      | 描述                                                  | 必要                                              |
| ------------- | ------------------------------------------------------------ | ----------------------------------------------------- |
| type          | FormatSettings 的類型必須設定為**JsonWriteSettings**。 | 是                                                   |
| filePattern |表示每個 JSON 檔案中儲存的資料模式。 允許的值為︰**setOfObjects** 和 **arrayOfObjects**。 **預設值**為 **setOfObjects**。 關於這些模式的詳細資訊，請參閱 [JSON 檔案模式](#json-file-patterns)一節。 |否 |

### <a name="json-file-patterns"></a>JSON 檔案模式

複製活動可以自動偵測並剖析下列 JSON 檔案模式。 

- **類型 I：setOfObjects**

    每個檔案都會包含單一物件，或以行分隔/串連的多個物件。 
    在複製活動接收中選擇此選項時，複製活動會產生單一 JSON 檔案，每行一個物件（以行分隔）。

    * **單一物件 JSON 範例**

        ```json
        {
            "time": "2015-04-29T07:12:20.9100000Z",
            "callingimsi": "466920403025604",
            "callingnum1": "678948008",
            "callingnum2": "567834760",
            "switch1": "China",
            "switch2": "Germany"
        }
        ```

    * **以行分隔的 JSON 範例**

        ```json
        {"time":"2015-04-29T07:12:20.9100000Z","callingimsi":"466920403025604","callingnum1":"678948008","callingnum2":"567834760","switch1":"China","switch2":"Germany"}
        {"time":"2015-04-29T07:13:21.0220000Z","callingimsi":"466922202613463","callingnum1":"123436380","callingnum2":"789037573","switch1":"US","switch2":"UK"}
        {"time":"2015-04-29T07:13:21.4370000Z","callingimsi":"466923101048691","callingnum1":"678901578","callingnum2":"345626404","switch1":"Germany","switch2":"UK"}
        ```

    * **串連的 JSON 範例**

        ```json
        {
            "time": "2015-04-29T07:12:20.9100000Z",
            "callingimsi": "466920403025604",
            "callingnum1": "678948008",
            "callingnum2": "567834760",
            "switch1": "China",
            "switch2": "Germany"
        }
        {
            "time": "2015-04-29T07:13:21.0220000Z",
            "callingimsi": "466922202613463",
            "callingnum1": "123436380",
            "callingnum2": "789037573",
            "switch1": "US",
            "switch2": "UK"
        }
        {
            "time": "2015-04-29T07:13:21.4370000Z",
            "callingimsi": "466923101048691",
            "callingnum1": "678901578",
            "callingnum2": "345626404",
            "switch1": "Germany",
            "switch2": "UK"
        }
        ```

- **類型 II：arrayOfObjects**

    每個檔案都會包含物件的陣列。

    ```json
    [
        {
            "time": "2015-04-29T07:12:20.9100000Z",
            "callingimsi": "466920403025604",
            "callingnum1": "678948008",
            "callingnum2": "567834760",
            "switch1": "China",
            "switch2": "Germany"
        },
        {
            "time": "2015-04-29T07:13:21.0220000Z",
            "callingimsi": "466922202613463",
            "callingnum1": "123436380",
            "callingnum2": "789037573",
            "switch1": "US",
            "switch2": "UK"
        },
        {
            "time": "2015-04-29T07:13:21.4370000Z",
            "callingimsi": "466923101048691",
            "callingnum1": "678901578",
            "callingnum2": "345626404",
            "switch1": "Germany",
            "switch2": "UK"
        }
    ]
    ```

## <a name="mapping-data-flow-properties"></a>對應資料流程屬性

JSON 檔案類型可以當做接收和對應資料流程中的來源使用。

### <a name="creating-json-structures-in-a-derived-column"></a>在衍生的資料行中建立 JSON 結構

您可以透過衍生的資料行運算式產生器，將複雜的資料行加入至您的資料流程。 在 [衍生的資料行] 轉換中，加入新的資料行，然後按一下藍色方塊來開啟 [運算式產生器]。 若要讓資料行變得複雜，您可以手動輸入 JSON 結構，或使用 UX 以互動方式加入個子。

#### <a name="using-the-expression-builder-ux"></a>使用運算式產生器 UX

在輸出架構側邊窗格中，將滑鼠停留在資料行上，然後按一下加號圖示。 選取 [**加入 subcolumn** ]，將資料行設為複雜類型。

![新增 subcolumn](media/data-flow/addsubcolumn.png "新增 Subcolumn")

您可以用相同的方式加入其他資料行和個子。 針對每個非複雜欄位，可以在右邊的運算式編輯器中加入運算式。

![複雜資料行](media/data-flow/complexcolumn.png "複雜資料行")

#### <a name="entering-the-json-structure-manually"></a>手動輸入 JSON 結構

若要手動加入 JSON 結構，請加入新的資料行，並在編輯器中輸入運算式。 運算式會遵循下列一般格式：

```
@(
    field1=0,
    field2=@(
        field1=0
    )
)
```

如果已針對名為 "complexColumn" 的資料行輸入此運算式，則會將它寫入至接收，做為下列 JSON：

```
{
    "complexColumn": {
        "field1": 0,
        "field2": {
            "field1": 0
        }
    }
}
```

#### <a name="sample-manual-script-for-complete-hierarchical-definition"></a>完整階層式定義的範例手動腳本
```
@(
    title=Title,
    firstName=FirstName,
    middleName=MiddleName,
    lastName=LastName,
    suffix=Suffix,
    contactDetails=@(
        email=EmailAddress,
        phone=Phone
    ),
    address=@(
        line1=AddressLine1,
        line2=AddressLine2,
        city=City,
        state=StateProvince,
        country=CountryRegion,
        postCode=PostalCode
    ),
    ids=[
        toString(CustomerID), toString(AddressID), rowguid
    ]
)
```

### <a name="source-format-options"></a>來源格式選項

使用 JSON 資料集做為資料流程中的來源，可讓您設定五個額外的設定。 這些設定可以在 [**來源選項**] 索引標籤的 [ **JSON 設定**] [可折疊] 底下找到。  

![JSON 設定](media/data-flow/json-settings.png "JSON 設定")

#### <a name="default"></a>預設

根據預設，JSON 資料會以下列格式讀取。

```
{ "json": "record 1" }
{ "json": "record 2" }
{ "json": "record 3" }
```

#### <a name="single-document"></a>單一檔

如果選取了 [**單一**檔]，則對應的資料流程會從每個檔案讀取一個 JSON 檔。 

``` json
File1.json
{
    "json": "record 1"
}
File2.json
{
    "json": "record 2"
}
File3.json
{
    "json": "record 3"
}
```

#### <a name="unquoted-column-names"></a>不具引號的資料行名稱

如果選取了不具引號的資料**行名稱**，則對應資料流程會讀取未以引號括住的 JSON 資料行。 

```
{ json: "record 1" }
{ json: "record 2" }
{ json: "record 3" }
```

#### <a name="has-comments"></a>有批註

如果 JSON 資料具有 C 或C++樣式批註，請選取 [**具有批註**]。

``` json
{ "json": /** comment **/ "record 1" }
{ "json": "record 2" }
{ /** comment **/ "json": "record 3" }
```

#### <a name="single-quoted"></a>單引號

如果 JSON 欄位和值使用單引號，而不是雙引號，請選取 [**單引號**]。

```
{ 'json': 'record 1' }
{ 'json': 'record 2' }
{ 'json': 'record 3' }
```

#### <a name="backslash-escaped"></a>反斜線已轉義

如果使用反斜線來轉義 JSON 資料中的字元，請選取 [**單引號**]。

```
{ "json": "record 1" }
{ "json": "\} \" \' \\ \n \\n record 2" }
{ "json": "record 3" }
```

## <a name="next-steps"></a>後續步驟

- [複製活動概觀](copy-activity-overview.md)
- [對應資料流程](concepts-data-flow-overview.md)
- [查閱活動](control-flow-lookup-activity.md)
- [GetMetadata 活動](control-flow-get-metadata-activity.md)
