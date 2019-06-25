---
title: 使用 Data Factory 從 Azure SQL Database 來回複製資料 | Microsoft Docs
description: 了解如何使用 Data Factory 將資料從支援的來源資料存放區複製到 Azure SQL Database，或從 SQL Database 複製到支援的接收資料存放區。
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 06/14/2019
ms.author: jingwang
ms.openlocfilehash: 90126a607065bdb10e2ff81ce6ab52809ecc3f36
ms.sourcegitcommit: a52d48238d00161be5d1ed5d04132db4de43e076
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/20/2019
ms.locfileid: "67273755"
---
# <a name="copy-data-to-or-from-azure-sql-database-by-using-azure-data-factory"></a>使用 Azure Data Factory 將資料複製到 Azure SQL Database 或從該處複製資料
> [!div class="op_single_selector" title1="選取您所使用的 Data Factory 服務的版本："]
> * [第 1 版](v1/data-factory-azure-sql-connector.md)
> * [目前的版本](connector-azure-sql-database.md)

本文概述如何從 Azure SQL Database 來回複製資料。 若要了解 Azure Data Factory，請閱讀[簡介文章](introduction.md)。

## <a name="supported-capabilities"></a>支援的功能

此 Azure SQL Database 連接器支援下列活動：

- [複製活動](copy-activity-overview.md)具有[支援來源/接收器矩陣](copy-activity-overview.md)資料表
- [對應資料流程](concepts-data-flow-overview.md)
- [查閱活動](control-flow-lookup-activity.md)
- [GetMetadata 活動](control-flow-get-metadata-activity.md)

具體而言，這個 Azure SQL Database 連接器支援下列功能：

- 使用 SQL 驗證和 Azure Active Directory (Azure AD) 應用程式權杖驗證搭配服務主體或 Azure 資源的受控識別來複製資料。
- 作為來源時，使用 SQL 查詢或預存程序來擷取資料。
- 在複製期間作為接收時，請使用自訂邏輯將資料附加到目的地資料表或叫用預存程序。

>[!NOTE]
>Azure SQL Database **[Always Encrypted](https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-database-engine?view=azuresqldb-current)** 目前不支援此連接器。 若要解決，您可以使用[一般的 ODBC 連接器](connector-odbc.md)和 SQL Server ODBC 驅動程式，透過自我裝載整合執行階段。 請遵循[本指南](https://docs.microsoft.com/sql/connect/odbc/using-always-encrypted-with-the-odbc-driver?view=azuresqldb-current)具有 ODBC 驅動程式下載及連接字串組態。

> [!IMPORTANT]
> 如果您使用 Azure Data Factory Integration Runtime 複製資料，請設定 [Azure SQL 伺服器防火牆](https://msdn.microsoft.com/library/azure/ee621782.aspx#ConnectingFromAzure)，讓 Azure 服務可存取伺服器。
> 如果您使用自我裝載整合執行階段來複製資料，請將 Azure SQL 伺服器防火牆設定為允許適當的 IP 範圍。 此範圍包括機器的 IP，用來連線到 Azure SQL Database。

## <a name="get-started"></a>開始使用

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

下列各節提供屬性的相關詳細資料，這些屬性是用來定義 Azure SQL Database 連接器專屬的 Data Factory 實體。

## <a name="linked-service-properties"></a>連結服務屬性

以下是支援 Azure SQL Database 已連結服務的屬性：

| 屬性 | 描述 | 必要項 |
|:--- |:--- |:--- |
| type | **type** 屬性必須設為 **AzureSqlDatabase**。 | 是 |
| connectionString | 針對 **connectionString** 屬性指定連線到 Azure SQL Database 執行個體所需的資訊。 <br/>將此欄位標記為 SecureString，將它安全地儲存在 Data Factory 中。 您也可以將密碼/服務主體金鑰放在 Azure Key Vault 中，而且，如果這是 SQL 驗證，則會從連接字串中提取 `password` 組態。 請參閱表格下方的 JSON 範例和[在 Azure Key Vault 中儲存認證](store-credentials-in-key-vault.md)一文深入了解詳細資料。 | 是 |
| servicePrincipalId | 指定應用程式的用戶端識別碼。 | 當您搭配服務主體使用 Azure AD 驗證時為是。 |
| servicePrincipalKey | 指定應用程式的金鑰。 將此欄位標記為 **SecureString**，將它安全地儲存在 Data Factory 中，或[參考 Azure Key Vault 中儲存的祕密](store-credentials-in-key-vault.md)。 | 當您搭配服務主體使用 Azure AD 驗證時為是。 |
| tenant | 指定您的應用程式所在租用戶的資訊 (網域名稱或租用戶識別碼)。 將滑鼠游標暫留在 Azure 入口網站右上角，即可擷取它。 | 當您搭配服務主體使用 Azure AD 驗證時為是。 |
| connectVia | 用來連線到資料存放區的[整合執行階段](concepts-integration-runtime.md)。 您可以使用 Azure Integration Runtime 或自我裝載整合執行階段 (如果您的資料存放區位於私人網路中)。 如果未指定，就會使用預設的 Azure Integration Runtime。 | 否 |

針對不同的驗證類型，請分別參閱下列有關先決條件和 JSON 範例的章節：

- [SQL 驗證](#sql-authentication)
- [Azure AD 應用程式權杖驗證：服務主體](#service-principal-authentication)
- [Azure AD 應用程式權杖驗證：適用於 Azure 資源的受控識別](#managed-identity)

>[!TIP]
>如果您遇到錯誤，其錯誤碼為 "UserErrorFailedToConnectToSqlServer"，以及「資料庫的工作階段限制為 XXX 並已達到。」訊息，請將 `Pooling=false` 新增至您的連接字串並再試一次。

### <a name="sql-authentication"></a>SQL 驗證

#### <a name="linked-service-example-that-uses-sql-authentication"></a>使用 SQL 驗證的連結服務範例

```json
{
    "name": "AzureSqlDbLinkedService",
    "properties": {
        "type": "AzureSqlDatabase",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Azure Key Vault 中的密碼：** 

```json
{
    "name": "AzureSqlDbLinkedService",
    "properties": {
        "type": "AzureSqlDatabase",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
            },
            "password": { 
                "type": "AzureKeyVaultSecret", 
                "store": { 
                    "referenceName": "<Azure Key Vault linked service name>", 
                    "type": "LinkedServiceReference" 
                }, 
                "secretName": "<secretName>" 
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="service-principal-authentication"></a>服務主體驗證

若要使用以服務主體為基礎的 Azure AD 應用程式權杖驗證，請遵循下列步驟：

1. **[從 Azure 入口網站建立 Azure Active Directory 應用程式](../active-directory/develop/howto-create-service-principal-portal.md#create-an-azure-active-directory-application)** 。 請記下應用程式名稱，以及下列可定義連結服務的值：

    - 應用程式識別碼
    - 應用程式金鑰
    - 租用戶識別碼

2. 如果您尚未這麼做，請在 Azure 入口網站上針對您的 Azure SQL 伺服器 **[佈建 Azure Active Directory 系統管理員](../sql-database/sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-azure-sql-database-server)** 。 Azure AD 系統管理員必須是 Azure AD 使用者或 Azure AD 群組，但不能是服務主體。 此步驟必須完成，如此您才可以在下一個步驟中使用 Azure AD 身分識別，為服務主體建立自主資料庫使用者。

3. 為服務主體 **[建立自主資料庫使用者](../sql-database/sql-database-aad-authentication-configure.md#create-contained-database-users-in-your-database-mapped-to-azure-ad-identities)** 。 以至少具有 ALTER ANY USER 權限的 Azure AD 身分識別，使用 SSMS 這類工具連線至您想要從中來回複製資料的資料庫。 執行下列 T-SQL： 
  
    ```sql
    CREATE USER [your application name] FROM EXTERNAL PROVIDER;
    ```

4. 如同您一般對 SQL 使用者或其他人所做的一樣，**將所需的權限授與服務主體**。 執行下列程式碼，或更多的選項是指[此處](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql?view=sql-server-2017)。

    ```sql
    EXEC sp_addrolemember [role name], [your application name];
    ```

5. 在 Azure Data Factory 中，**設定 Azure SQL Database 連結服務**。


#### <a name="linked-service-example-that-uses-service-principal-authentication"></a>使用服務主體驗證的連結服務範例

```json
{
    "name": "AzureSqlDbLinkedService",
    "properties": {
        "type": "AzureSqlDatabase",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;Connection Timeout=30"
            },
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalKey": {
                "type": "SecureString",
                "value": "<service principal key>"
            },
            "tenant": "<tenant info, e.g. microsoft.onmicrosoft.com>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="managed-identity"></a> Azure 資源的受控識別驗證

資料處理站可與 [Azure 資源的受控識別](data-factory-service-identity.md)相關聯，後者表示特定的資料處理站。 您可以使用這個受管理的身分識別的 Azure SQL Database 驗證。 指定的處理站可以使用此身分識別來存取資料庫，並從中來回複製資料。

若要使用受控身分識別驗證，請遵循下列步驟：

1. 如果您尚未這麼做，請在 Azure 入口網站上針對您的 Azure SQL 伺服器 **[佈建 Azure Active Directory 系統管理員](../sql-database/sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-azure-sql-database-server)** 。 Azure AD 系統管理員可以是 Azure AD 使用者或 Azure AD 群組。 如果您授與管理的身分識別系統管理員角色的群組，請略過步驟 3 和 4。 系統管理員將擁有完整的資料庫存取權。

2. **[建立自主的資料庫使用者](../sql-database/sql-database-aad-authentication-configure.md#create-contained-database-users-in-your-database-mapped-to-azure-ad-identities)** 的 Data Factory 受控身分識別。 以至少具有 ALTER ANY USER 權限的 Azure AD 身分識別，使用 SSMS 這類工具連線至您想要從中來回複製資料的資料庫。 執行下列 T-SQL： 
  
    ```sql
    CREATE USER [your Data Factory name] FROM EXTERNAL PROVIDER;
    ```

3. **授與 Data Factory 受控身分識別所需的權限**像您一般的 SQL 使用者和其他項目。 執行下列程式碼，或更多的選項是指[此處](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql?view=sql-server-2017)。

    ```sql
    EXEC sp_addrolemember [role name], [your Data Factory name];
    ```

4. 在 Azure Data Factory 中，**設定 Azure SQL Database 連結服務**。

**範例：**

```json
{
    "name": "AzureSqlDbLinkedService",
    "properties": {
        "type": "AzureSqlDatabase",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;Connection Timeout=30"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>資料集屬性

如需可用來定義資料集的區段和屬性完整清單，請參閱[資料集](https://docs.microsoft.com/azure/data-factory/concepts-datasets-linked-services)一文。 本節提供 Azure SQL Database 資料集所支援的屬性清單。

若要從或 Azure SQL Database，請複製資料，支援下列屬性：

| 屬性 | 描述 | 必要項 |
|:--- |:--- |:--- |
| type | 資料集的 **type** 屬性必須設定為 **AzureSqlTable**。 | 是 |
| tableName | Azure SQL Database 執行個體中連結服務所參考的資料表或檢視的名稱。 | 否 (來源)；是 (接收) |

#### <a name="dataset-properties-example"></a>資料集屬性範例

```json
{
    "name": "AzureSQLDbDataset",
    "properties":
    {
        "type": "AzureSqlTable",
        "linkedServiceName": {
            "referenceName": "<Azure SQL Database linked service name>",
            "type": "LinkedServiceReference"
        },
        "schema": [ < physical schema, optional, retrievable during authoring > ],
        "typeProperties": {
            "tableName": "MyTable"
        }
    }
}
```

## <a name="copy-activity-properties"></a>複製活動屬性

如需可用來定義活動的區段和屬性完整清單，請參閱[管線](concepts-pipelines-activities.md)一文。 本節提供 Azure SQL Database 來源和接收所支援屬性的清單。

### <a name="azure-sql-database-as-the-source"></a>Azure SQL Database 作為來源

若要從 Azure SQL Database 複製資料，請將複製活動來源中的 **type** 屬性設定為 **SqlSource**。 複製活動的 [來源]  區段支援下列屬性：

| 屬性 | 描述 | 必要項 |
|:--- |:--- |:--- |
| type | 複製活動來源的 **type**屬性必須設定為 **SqlSource**。 | 是 |
| SqlReaderQuery | 使用自訂 SQL 查詢來讀取資料。 範例： `select * from MyTable`. | 否 |
| sqlReaderStoredProcedureName | 從來源資料表讀取資料的預存程序名稱。 最後一個 SQL 陳述式必須是預存程序中的 SELECT 陳述式。 | 否 |
| storedProcedureParameters | 預存程序的參數。<br/>允許的值為名稱或值組。 參數的名稱和大小寫必須符合預存程序參數的名稱和大小寫。 | 否 |

### <a name="points-to-note"></a>注意事項

- 如果已為 **SqlSource** 指定 **sqlReaderQuery**，複製活動會針對 Azure SQL Database 來源執行這項查詢以取得資料。 您也可以指定預存程序。 指定 **sqlReaderStoredProcedureName** 和 **storedProcedureParameters** (如果預存程序接受參數)。
- 如果您未指定 **sqlReaderQuery** 或 **sqlReaderStoredProcedureName**，就會使用資料集 JSON **structure** 區段中定義的資料行來建構查詢。 `select column1, column2 from mytable` 會針對 Azure SQL Database 執行。 如果資料集定義沒有 **structure**，則會從資料表中選取所有資料行。

#### <a name="sql-query-example"></a>SQL 查詢範例

```json
"activities":[
    {
        "name": "CopyFromAzureSQLDatabase",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Azure SQL Database input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "SqlSource",
                "sqlReaderQuery": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

#### <a name="stored-procedure-example"></a>預存程序範例

```json
"activities":[
    {
        "name": "CopyFromAzureSQLDatabase",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Azure SQL Database input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "SqlSource",
                "sqlReaderStoredProcedureName": "CopyTestSrcStoredProcedureWithParameters",
                "storedProcedureParameters": {
                    "stringData": { "value": "str3" },
                    "identifier": { "value": "$$Text.Format('{0:yyyy}', <datetime parameter>)", "type": "Int"}
                }
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="stored-procedure-definition"></a>預存程序定義

```sql
CREATE PROCEDURE CopyTestSrcStoredProcedureWithParameters
(
    @stringData varchar(20),
    @identifier int
)
AS
SET NOCOUNT ON;
BEGIN
     select *
     from dbo.UnitTestSrcTable
     where dbo.UnitTestSrcTable.stringData != stringData
    and dbo.UnitTestSrcTable.identifier != identifier
END
GO
```

### <a name="azure-sql-database-as-the-sink"></a>Azure SQL Database 作為接收

> [!TIP]
> 深入了解支援的寫入行為、 組態和最佳做法是從[將資料載入 Azure SQL Database 的最佳做法](#best-practice-for-loading-data-into-azure-sql-database)。

若要將資料複製到 Azure SQL Database，請將複製活動接收中的 **type** 屬性設定為 **SqlSink**。 複製活動的 [接收]  區段支援下列屬性：

| 屬性 | 描述 | 必要項 |
|:--- |:--- |:--- |
| type | 複製活動接收的 **type** 屬性必須設定為：**SqlSink**。 | 是 |
| writeBatchSize | 要插入至 SQL 資料表的資料列的數目**每個批次**。<br/> 允許的值為**整數** (資料列數目)。 依預設，Data Factory 以動態方式決定適當的批次大小為基礎的資料列大小。 | 否 |
| writeBatchTimeout | 在逾時前等待批次插入作業完成的時間。<br/> 允許的值為**時間範圍**。 範例：“00:30:00” (30 分鐘)。 | 否 |
| preCopyScript | 指定一個 SQL 查詢，供「複製活動」在將資料寫入 Azure SQL Database 之前執行。 每一複製回合只會叫用此查詢一次。 使用此屬性來清除預先載入的資料。 | 否 |
| sqlWriterStoredProcedureName | 定義如何將來源資料套用到目標資料表的預存程序名稱。 <br/>此預存程序將會**依批次叫用**。 對於只執行一次，而且沒有任何資料來源，例如，刪除或截斷的作業，使用`preCopyScript`屬性。 | 否 |
| storedProcedureParameters |預存程序的參數。<br/>允許的值為：名稱和值組。 參數的名稱和大小寫必須符合預存程序參數的名稱和大小寫。 | 否 |
| sqlWriterTableType | 指定要在預存程序中使用的資料表類型名稱。 複製活動會讓正在移動的資料可用於此資料表類型的暫存資料表。 然後，預存程序程式碼可以合併正在複製的資料與現有的資料。 | 否 |

**範例 1： 附加資料**

```json
"activities":[
    {
        "name": "CopyToAzureSQLDatabase",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Azure SQL Database output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "SqlSink",
                "writeBatchSize": 100000
            }
        }
    }
]
```

**範例 2： 在複製期間呼叫的預存的程序**

若要了解更多詳細資料，請參閱[叫用 SQL 接收中的預存程序](#invoking-stored-procedure-for-sql-sink)。

```json
"activities":[
    {
        "name": "CopyToAzureSQLDatabase",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Azure SQL Database output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "SqlSink",
                "sqlWriterStoredProcedureName": "CopyTestStoredProcedureWithParameters",
                "sqlWriterTableType": "CopyTestTableType",
                "storedProcedureParameters": {
                    "identifier": { "value": "1", "type": "Int" },
                    "stringData": { "value": "str1" }
                }
            }
        }
    }
]
```

## <a name="best-practice-for-loading-data-into-azure-sql-database"></a>將資料載入 Azure SQL Database 的最佳作法

當您將資料複製到 Azure SQL Database 時，您可能需要不同的寫入行為：

- **[附加](#append-data)** ： 我的來源資料只會有新的記錄;
- **[Upsert](#upsert-data)** ： 我的來源資料有插入和更新;
- **[覆寫](#overwrite-entire-table)** :我想要重新載入整個維度資料表每次;
- **[寫入使用自訂邏輯](#write-data-with-custom-logic)** :我需要在最後一個插入至目的地資料表之前的額外處理。

請參閱分別章節，到如何在 ADF 和最佳作法設定。

### <a name="append-data"></a>附加資料

這是預設行為，此 Azure SQL Database 的接收連接器，和 ADF 嗎**bulk insert**有效率地寫入至您的資料表。 您只可以設定來源，並據以接收複製活動中。

### <a name="upsert-data"></a>更新插入資料

**選項我**（特別是當您有要複製的大型資料建議）：**大部分的高效能方法**執行 upsert 如下： 

- 首先，利用[資料庫範圍暫存資料表](https://docs.microsoft.com/sql/t-sql/statements/create-table-transact-sql?view=azuresqldb-current#database-scoped-global-temporary-tables-azure-sql-database)來大量載入，使用複製活動的所有記錄。 作業對資料庫範圍暫存資料表不會記錄，您可以載入數百萬筆記錄，以秒為單位。
- 在要套用的 ADF 執行預存程序活動[合併](https://docs.microsoft.com/sql/t-sql/statements/merge-transact-sql?view=azuresqldb-current)（或插入/更新） 陳述式，並使用 temp 資料表來源，以執行所有更新或插入做為單一交易中，減少往返量，並記錄作業。 在預存程序活動結束時，就可能被截斷暫存資料表以做好下次 upsert 循環。 

例如，，您也可以在 Azure Data Factory 中建立管線，其中含有**複製活動**與鏈結**預存程序活動**成功。 先前的資料複製從您的來源存放區到 Azure SQL Database 的暫存資料表，例如" **##UpsertTempTable**"做為資料集中的資料表名稱，然後第二個會叫用預存程序來從暫存資料表的來源資料合併到目標資料表，並清除暫存資料表。

![Upsert](./media/connector-azure-sql-database/azure-sql-database-upsert.png)

在資料庫中，定義與合併邏輯，如下所示，從上述的預存程序活動指的預存程序。 假設目標**行銷**具有三個資料行的資料表：**ProfileID**，**狀態**，以及**類別**，並執行 upsert 根據**ProfileID**資料行。

```sql
CREATE PROCEDURE [dbo].[spMergeData]
AS
BEGIN
    MERGE TargetTable AS target
    USING ##UpsertTempTable AS source
    ON (target.[ProfileID] = source.[ProfileID])
    WHEN MATCHED THEN
        UPDATE SET State = source.State
    WHEN NOT matched THEN
        INSERT ([ProfileID], [State], [Category])
      VALUES (source.ProfileID, source.State, source.Category);
    
    TRUNCATE TABLE ##UpsertTempTable
END
```

**選項二部分：** 或者，您可以選擇[叫用預存程序中複製活動](#invoking-stored-procedure-for-sql-sink)，同時請注意，這種方法會針對每個資料列中來源資料表，而不是利用大量執行插入成為預設方法在複製活動中，因此它並不適合用於大規模 upsert。

### <a name="overwrite-entire-table"></a>覆寫整份資料表

您可以設定**preCopyScript**接收複製活動中的屬性，在此情況下每個複製活動執行，ADF 指令碼會先執行，然後執行 插入資料的複本。 例如，若要以最新的資料覆寫整個資料表，您可以先指定指令碼以刪除所有記錄，再從來源大量載入新資料。

### <a name="write-data-with-custom-logic"></a>寫入使用自訂邏輯的資料

中所述類似[Upsert 資料](#upsert-data) 區段中，當您需要套用至目的地資料表中，來源資料在最後一次之前的額外處理可以) 針對大規模的載入至資料庫範圍暫存資料表，然後叫用預存程序或 b） 在複製期間叫用預存的程序。

## <a name="invoking-stored-procedure-for-sql-sink"></a> 從 SQL 接收器叫用預存程序

當您將資料複製到 Azure SQL Database 時，您也可以設定，並叫用使用者自訂預存程序搭配其他參數。

> [!TIP]
> 叫用預存程序會處理資料列逐列，而非大量作業，不建議針對大規模複製。 進一步了解[將資料載入 Azure SQL Database 的最佳做法](#best-practice-for-loading-data-into-azure-sql-database)。

您可以使用預存程序，當內建的複製機制不提供的用途，例如，適用於來源資料的最後一個插入至目的地資料表之前的額外處理。 一些額外處理的範例包括合併資料行、查閱其他的值，以及插入多個資料表中。

下列範例示範如何使用預存程序，對 Azure SQL Database 中的資料表執行 upsert。 假設輸入資料和接收器 **Marketing** 資料表各有三個資料行：**ProfileID**、**State** 和 **Category**。 根據 **ProfileID** 資料行執行 upsert，然後僅針對特定的類別套用。

**輸出資料集：** "tableName"應該是您的預存程序 （請參閱下列預存程序的指令碼） 相同的資料表型別參數名稱。

```json
{
    "name": "AzureSQLDbDataset",
    "properties":
    {
        "type": "AzureSqlTable",
        "linkedServiceName": {
            "referenceName": "<Azure SQL Database linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "tableName": "Marketing"
        }
    }
}
```

定義**SQL 接收器**一節中複製活動，如下所示。

```json
"sink": {
    "type": "SqlSink",
    "SqlWriterTableType": "MarketingType",
    "SqlWriterStoredProcedureName": "spOverwriteMarketing",
    "storedProcedureParameters": {
        "category": {
            "value": "ProductA"
        }
    }
}
```

在資料庫中，使用與 **SqlWriterStoredProcedureName** 相同的名稱來定義預存程序。 它會處理來自指定來源的輸入資料，並合併至輸出資料表。 預存程序中資料表類型的參數名稱應該與資料集中定義的 **tableName** 相同。

```sql
CREATE PROCEDURE spOverwriteMarketing @Marketing [dbo].[MarketingType] READONLY, @category varchar(256)
AS
BEGIN
  MERGE [dbo].[Marketing] AS target
  USING @Marketing AS source
  ON (target.ProfileID = source.ProfileID and target.Category = @category)
  WHEN MATCHED THEN
      UPDATE SET State = source.State
  WHEN NOT MATCHED THEN
      INSERT (ProfileID, State, Category)
      VALUES (source.ProfileID, source.State, source.Category);
END
```

在資料庫中，使用與 **sqlWriterTableType** 相同的名稱來定義資料表類型。 資料表類型的結構描述應該與輸入資料所傳回的結構描述相同。

```sql
CREATE TYPE [dbo].[MarketingType] AS TABLE(
    [ProfileID] [varchar](256) NOT NULL,
    [State] [varchar](256) NOT NULL,
    [Category] [varchar](256) NOT NULL
)
```

預存程序功能使用 [資料表值參數](https://msdn.microsoft.com/library/bb675163.aspx)。

## <a name="mapping-data-flow-properties"></a>對應資料流程屬性

了解詳細資料，從[來源轉換](data-flow-source.md)並[接收轉換](data-flow-sink.md)中對應的資料流。

## <a name="data-type-mapping-for-azure-sql-database"></a>Azure SQL Database 的資料類型對應

從 Azure SQL Database 複製資料或將資料複製到該處時，會使用下列從 Azure SQL Database 資料類型對應到 Azure Data Factory 過渡期資料類型的對應。 請參閱[結構描述和資料類型對應](copy-activity-schema-and-type-mapping.md)，以了解複製活動如何將來源結構描述和資料類型對應至接收。

| Azure SQL Database 資料類型 | Data Factory 過渡期資料類型 |
|:--- |:--- |
| bigint |Int64 |
| binary |Byte[] |
| bit |Boolean |
| char |String, Char[] |
| date |Datetime |
| Datetime |Datetime |
| datetime2 |Datetime |
| Datetimeoffset |DateTimeOffset |
| Decimal |Decimal |
| FILESTREAM attribute (varbinary(max)) |Byte[] |
| Float |Double |
| image |Byte[] |
| int |Int32 |
| money |Decimal |
| nchar |String, Char[] |
| ntext |String, Char[] |
| numeric |Decimal |
| nvarchar |String, Char[] |
| real |Single |
| rowversion |Byte[] |
| smalldatetime |Datetime |
| smallint |Int16 |
| smallmoney |Decimal |
| sql_variant |Object |
| text |String, Char[] |
| time |TimeSpan |
| timestamp |Byte[] |
| tinyint |Byte |
| uniqueidentifier |Guid |
| varbinary |Byte[] |
| varchar |String, Char[] |
| Xml |xml |

>[!NOTE]
> 針對對應至 Decimal 過渡期類型的資料類型，目前 ADF 支援的有效位數最多可達 28。 如果您有有效位數超過 28 的資料，請考慮在 SQL 查詢中將其轉換成字串。

## <a name="next-steps"></a>後續步驟
如需 Azure Data Factory 中的複製活動所支援作為來源和接收端的資料存放區清單，請參閱[支援的資料存放區和格式](copy-activity-overview.md##supported-data-stores-and-formats)。
