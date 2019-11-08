---
title: 個人資訊的命名實體
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 10/21/2019
ms.author: aahi
ms.openlocfilehash: 3aa4da9a9cf3d1d4b664e81f1fd18f2b225d731d
ms.sourcegitcommit: 018e3b40e212915ed7a77258ac2a8e3a660aaef8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/07/2019
ms.locfileid: "73799733"
---
## <a name="personal-information-entity-types"></a>個人資訊實體類型：

### <a name="phone-number"></a>電話號碼

電話號碼。 

語言：

* 公開預覽： `English`

| 子類型名稱           | 說明                                           |
|------------------------|-------------------------------------------------------|
| N/A                    | 電話號碼，例如 `+1 123-123-123`。          |
| 歐盟電話號碼        | 歐盟專用的電話號碼。         |
| 歐盟行動電話號碼 | 歐盟專用的行動電話號碼。 |

### <a name="eu-gps-coordinates"></a>歐盟 GPS 座標

 歐盟內位置的 GPS 座標。 

語言：

* 公開預覽： `English`

| 子類型名稱 | 說明                               |
|--------------|-------------------------------------------|
| N/A          | 歐盟內的 GPS 座標 |

### <a name="azure-information"></a>Azure 資訊

可識別的 Azure 資訊，包括驗證資訊和連接字串。 

語言：

* 公開預覽： `English`

| 子類型名稱                          | 說明                                                                 |
|---------------------------------------|-----------------------------------------------------------------------------|
| Azure DocumentDB 驗證金鑰             | Azure DocumentDB 伺服器的授權金鑰。                           |
| Azure IAAS 資料庫連接字串 | Azure 基礎結構即服務（IaaS）資料庫的連接字串。 |
| Azure SQL 連接字串           | Azure SQL 資料庫的連接字串。                                |
| Azure IoT 連接字串           | 適用于 Azure 物聯網（IoT）的連接字串。                        |
| Azure 發佈設定密碼        | Azure 發佈設定的密碼。                                        |
| Azure Redis 快取連接字串   | Azure Cache for Redis 的連接字串。                             |
| Azure SAS                             | Azure 軟體即服務（SAS）的連接字串。                     |
| Azure 服務匯流排連接字串   | Azure 服務匯流排的連接字串。                                |
| Azure 儲存體帳戶金鑰             | Azure 儲存體帳戶的帳戶金鑰。                                   |
| Azure 儲存體帳戶金鑰（一般）   | Azure 儲存體帳戶的一般帳戶金鑰。                           |
| SQL Server 連接字串          | SQL server 的連接字串。                                         |

### <a name="identification"></a>識別

語言：

* 公開預覽： `English`

#### <a name="financial-account-identification"></a>財務帳戶識別

| 子類型名稱               | 說明                                                                |
|----------------------------|----------------------------------------------------------------------------|
| ABA 路由號碼        | 美國四間協會（ABA）運輸路線號碼。                  |
| SWIFT 程式碼                 | 付款指示資訊的 SWIFT 代碼。                           |
| 信用卡                | 信用卡號碼。                                                       |
| IBAN 代碼                  | 付款指示資訊的 IBAN 代碼。                            |

#### <a name="government-and-country-specific-identification"></a>政府和國家/地區特定的識別

下列實體會依國家/地區分組和列出：

阿根廷
* 國家（地區）身分識別（DNI）號碼

澳大利亞
* 稅務檔案編號 
* 驅動程式的授權識別碼
* Passport 識別碼
* 醫療帳戶號碼
* 銀行帳戶號碼（例如支票、省下和借方帳戶）

比利時
* 國家/地區號碼

巴西
* 法律實體編號（CNPJ）
* CPF 號碼
* 國家（地區）識別碼卡（RG）

加拿大
* Passport 識別碼
* 驅動程式的授權識別碼
* 健康保險號碼
* 個人健全狀況識別碼（PHIN）
* 社會安全號碼
* 銀行帳戶號碼（例如支票、省下和借方帳戶）

智利
* 身分識別卡號碼 

中國
* 身分識別卡號碼
* 常駐識別碼卡（中國）號碼

克羅埃西亞
* 識別碼卡編號
* 個人識別碼（OIB）號碼

捷克共和國
* 國家識別碼卡號碼

丹麥
* 個人識別碼

歐盟（EU）
* 國家/地區識別碼
* Passport 識別碼
* 驅動程式的授權識別碼
* 社會安全號碼（SSN）或對等識別碼
* EU 稅務識別編號（TIN）
* 歐盟轉帳卡編號

芬蘭
* 國家/地區識別碼
* Passport 識別碼

法國
* 國家（地區）識別碼卡（CNI）
* 社會安全號碼（INSEE）
* Passport 識別碼
* 驅動程式的授權識別碼

德國
* 識別碼卡編號
* Passport 識別碼
* 驅動程式的授權識別碼

希臘 
* 國家識別碼卡號碼

香港
* 識別碼卡（HKID）編號

印度
* 永久帳戶號碼（PAN）
* 唯一識別碼（Aadhaar）編號

印尼
* 識別碼卡號碼（KTP）

愛爾蘭
* 個人公用服務（PPS）號碼

以色列
* 國家/地區識別碼
* 銀行帳戶號碼（例如支票、省下和借方帳戶）

義大利
* 驅動程式的授權識別碼

日本
* 常駐註冊號碼
* 居住卡號
* 驅動程式的授權識別碼
* 社交保險號碼（SIN）
* Passport 識別碼
* 銀行帳戶號碼（例如支票、省下和借方帳戶）

馬來西亞
* 識別碼卡編號

荷蘭
* 公民服務（BSN）號碼

紐西蘭
* 健康情況號碼的一部

挪威
* 識別碼卡編號

菲律賓
* 統一的多用途 ID 號碼

波蘭
* 識別碼卡編號
* 國家（地區）識別碼（PESEL）
* Passport 識別碼

葡萄牙 
* 公民卡號

沙烏地阿拉伯
* 國家/地區識別碼

新加坡
* 國家註冊識別碼卡（NRIC）號碼

南非
* 識別碼
* 常駐註冊號碼

南韓
* 常駐註冊號碼

西班牙 
* 社會安全號碼 (SSN)

瑞典
* 國家/地區識別碼
* Passport 識別碼

台灣 
* 國家/地區識別碼
* 常駐憑證（ARC/TARC）號碼
* Passport 識別碼

泰國
* 人口識別程式碼

英國
* Passport 識別碼
* 驅動程式的授權識別碼
* 國家保險號碼（NINO）
* 國家健全狀況服務（NHS）號碼

美國
* 社會安全號碼 (SSN)
* 驅動程式的授權識別碼
* Passport 識別碼
* 選舉的變換編號
* 個別的稅務 ID 號碼（ITIN）
* 藥品強制代理（DEA）號碼
* 銀行帳戶號碼（例如支票、省下和借方帳戶）
