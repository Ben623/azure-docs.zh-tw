---
title: 虛擬機器供應專案的定價 |Azure Marketplace
description: 說明指定虛擬機器供應項目定價的三種方法。
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: pabutler
ms.openlocfilehash: c7ea5afeb46c30837c2ae53e871bb64f5d8cf292
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/08/2019
ms.locfileid: "73827328"
---
<a name="pricing-for-virtual-machine-offers"></a>虛擬機器供應項目定價
==================================

指定虛擬機器供應項目定價的方法有三種：自訂核心定價、每一核心定價，以及試算表定價。


<a name="customized-core-pricing"></a>自訂核心定價
-----------------------

定價專屬於各個地區和核心組合。 必須在定義的 **virtualMachinePricing**/**regionPrices** 區段中指定銷售清單中的每個區域。  在您的要求中為每個[區域](#regions)使用正確的貨幣代碼。  下列範例示範這些需求：

``` json
    "virtualMachinePricing": 
    {
        ... 
        "coreMultiplier": 
        {
            "currency": "USD",
                "individually": 
                {
                    "sharedcore": 1,
                    "1core": 2,
                    "2core": 2,
                    "4core": 2,
                    "6core": 2,
                    "8core": 2,
                    "10core": 4,
                    "12core": 4,
                    "16core": 4,
                    "20core": 4,
                    "24core": 4,
                    "32core": 6,
                    "36core": 6,
                    "40core": 6,
                    "44core": 6,
                    "48core": 10,
                    "60core": 10,
                    "64core": 10,
                    "72core": 10,
                    "80core": 12,
                    "96core": 12,
                    "120core": 15,
                    "128core": 15,
                    "208core": 20,
                    "416core": 30
                }
        }
        ...
     }
```


<a name="per-core-pricing"></a>每一核心定價
----------------

在此情況下，發行者會為其 SKU 指定一個價格 (USD)，並自動產生所有其他價格。 每個核心的價格會在要求中的**單一**參數中指定。

``` json
     "virtualMachinePricing": 
     {
         ...
         "coreMultiplier": 
         {
             "currency": "USD",
             "single": 1.0
         }
     }
```


<a name="spreadsheet-pricing"></a>試算表定價
-------------------

發行者還可以將他們的定價試算表上傳至暫存位置，然後像其他檔案成品一樣，在要求中包含 URI。 然後上傳試算表、進行轉譯以評估指定的價格排程，最後使用定價資訊更新供應項目。 針對供應項目後續的 GET 要求將會傳回試算表 URI 和該區域的評估價格。

``` json
     "virtualMachinePricing": 
     {
         ...
         "spreadSheetPricing": 
         {
             "uri": "https://blob.storage.azure.com/<your_spreadsheet_location_here>/prices.xlsx",
         }
     }
```

<a name="new-core-sizes-added-on-722019"></a>7/2/2019 新增的核心大小
---------------------------

針對新的 Azure 虛擬機器大小（根據核心數目），在2019年7月2日新增新價格的 VM 發行者已收到通知。  新的價格是針對核心大小10、44、48、60、120、208和416。  針對現有的 VM，會根據目前的價格自動計算這些核心大小的新價格。  發行者必須等到2019年8月1日，才能查看額外的價格，並進行任何想要的變更。  在此日期之後（如果發行者尚未重新發行），這些新核心大小自動計算的價格將會生效。


<a name="regions"></a>區域
-------

下表顯示您可以為自訂核心定價指定的不同區域，以及其相對應的貨幣代碼。

| **區域** | **名稱**             | **貨幣代碼** |
|------------|----------------------|-------------------|
| DZ         | 阿爾及利亞              | DZD               |
| AR         | 阿根廷            | ARS 披索               |
| AU         | 澳大利亞            | 澳幣               |
| AT         | 奧地利              | 歐元               |
| BH         | 巴林              | BHD               |
| BY         | 白俄羅斯              | 盧布               |
| BE         | 比利時              | 歐元               |
| BR         | 巴西               | 美元               |
| BG         | 保加利亞             | BGN               |
| CA         | 加拿大               | 加拿大幣               |
| CL         | 智利                | CLP               |
| CO         | 哥倫比亞             | COP               |
| CR         | 哥斯大黎加           | CRC               |
| HR         | 克羅埃西亞              | HRK               |
| CY         | 賽浦路斯               | 歐元               |
| CZ         | 捷克共和國       | CZK               |
| DK         | 丹麥              | 丹麥幣               |
| DO         | 多明尼加共和國   | 美元               |
| EC         | 厄瓜多              | 美元               |
| EG         | 埃及                | EGP               |
| SV         | 薩爾瓦多          | 美元               |
| EE         | 愛沙尼亞              | 歐元               |
| FI         | 芬蘭              | 歐元               |
| FR         | 法國               | 歐元               |
| DE         | 德國              | 歐元               |
| GR         | 希臘               | 歐元               |
| GT         | 瓜地馬拉            | GTQ               |
| HK         | 香港特別行政區        | 港幣               |
| HU         | 匈牙利              | HUF               |
| IS         | 冰島              | ISK               |
| IN         | 印度                | 印度幣               |
| ID         | 印尼            | 印尼盾               |
| IE         | 愛爾蘭              | 歐元               |
| IL         | 以色列               | ILS               |
| IT         | 義大利                | 歐元               |
| JP         | 日本                | 日圓               |
| JO         | 約旦               | JOD               |
| KZ         | 哈薩克           | KZT               |
| KE         | 肯亞                | KES               |
| KR         | 南韓                | 韓元               |
| KW         | 科威特               | KWD               |
| LV         | 拉脫維亞               | 歐元               |
| LI         | 列支敦斯登        | 瑞士法郎               |
| LT         | 立陶宛            | 歐元               |
| LU         | 盧森堡           | 歐元               |
| MK         | 北馬其頓      | MKD               |
| MY         | 馬來西亞             | 馬來西亞林吉特               |
| MT         | 馬爾他                | 歐元               |
| MX         | 墨西哥               | 墨西哥批索               |
| ME         | 蒙特內哥羅           | 歐元               |
| MA         | 摩洛哥              | MAD               |
| NL         | 荷蘭          | 歐元               |
| NZ         | 紐西蘭          | 紐西蘭幣               |
| NG         | 奈及利亞              | NGN               |
| 否         | 挪威               | 挪威克朗               |
| OM         | 阿曼                 | OMR               |
| PK         | 巴基斯坦             | PKR               |
| PA         | 巴拿馬               | 美元               |
| PY         | 巴拉圭             | PYG               |
| PE         | 祕魯                 | PEN               |
| PH         | 菲律賓          | PHP               |
| PL         | 波蘭               | PLN               |
| PT         | 葡萄牙             | 歐元               |
| PR         | 波多黎各          | 美元               |
| QA         | 卡達                | QAR               |
| RO         | 羅馬尼亞              | RON               |
| RU         | 俄羅斯               | 盧布               |
| SA         | 沙烏地阿拉伯         | SAR 里亞爾               |
| RS         | 塞爾維亞               | RSD               |
| SG         | 新加坡            | SGD               |
| SK         | 斯洛伐克             | 歐元               |
| SI         | 斯洛維尼亞             | 歐元               |
| ZA         | 南非         | 南非幣               |
| ES         | 西班牙                | 歐元               |
| LK         | 斯里蘭卡            | 美元               |
| SE         | 瑞典               | 瑞典克朗               |
| CH         | 瑞士          | 瑞士法郎               |
| TW         | 台灣               | 新台幣               |
| 二四         | 泰國             | THB               |
| TT         | 千里達及托巴哥  | TTD               |
| TN         | 突尼西亞              | TND               |
| TR         | 土耳其               | 土耳其里拉               |
| UA         | 烏克蘭              | UAH               |
| AE         | 阿拉伯聯合大公國 | 歐元               |
| GB         | 英國       | 英鎊               |
| US         | 美國        | 美元               |
| UY         | 烏拉圭              | UYU               |
| VE         | 委內瑞拉            | 美元               |
|  |  |  |
