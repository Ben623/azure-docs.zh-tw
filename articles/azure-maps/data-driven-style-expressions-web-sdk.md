---
title: Azure 地圖服務 Web SDK 中的資料驅動樣式表達式 |Microsoft Azure 對應
description: 在本文中，您將瞭解如何在 Microsoft Azure Maps Web SDK 中使用資料驅動樣式表達式。
author: rbrundritt
ms.author: richbrun
ms.date: 4/4/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendleton
ms.custom: codepen
ms.openlocfilehash: 8372012734d937da99c32d2d18fed91ae52c7444
ms.sourcegitcommit: f9601bbccddfccddb6f577d6febf7b2b12988911
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/12/2020
ms.locfileid: "75911767"
---
# <a name="data-driven-style-expressions-web-sdk"></a>資料驅動樣式表達式（Web SDK）

運算式可讓您將商務邏輯套用至樣式選項，以觀察資料來源中每個圖形中所定義的屬性。 運算式也可以用來篩選資料來源或圖層中的資料。 運算式可以包含條件式邏輯，例如 if 語句，而且也可以用來操作的資料。字串、邏輯和數學運算子。 

資料驅動樣式可以減少在設定樣式前後實行商務邏輯所需的程式碼數量。 與圖層搭配使用時，會在轉譯時于轉譯時間評估運算式，以便在相較于在 UI 執行緒上評估商務邏輯時提供更高的效能。

下列影片提供 Azure 地圖服務 Web SDK 中資料驅動樣式的總覽。

<br/>

<iframe src="https://channel9.msdn.com/Shows/Internet-of-Things-Show/Data-Driven-Styling-with-Azure-Maps/player" width="960" height="540" allowFullScreen frameBorder="0"></iframe>

運算式會以 JSON 陣列表示。 陣列中運算式的第一個元素是指定運算式運算子名稱的字串。 例如，"+" 或 "case"。 下一個元素（如果有的話）是運算式的引數。 每個引數都是常值（字串、數位、布林值或 `null`）或另一個運算式陣列。 下列虛擬程式碼會定義運算式的基本結構。 

```javascript
[ 
    expression_operator, 
    argument0, 
    argument1, 
    …
] 
```

Azure 地圖服務的 Web SDK 支援許多類型，可以單獨使用或與其他運算式搭配使用。

| 運算式的類型 | 說明 |
|---------------------|-------------|
| [匯總運算式](#aggregate-expression) | 定義計算的運算式，它會在一組資料上處理，而且可以搭配 `DataSource`的 `clusterProperties` 選項使用。 |
| [布林運算式](#boolean-expressions) | 布林運算式提供一組布林運算子運算式來評估布林值比較。 |
| [色彩運算式](#color-expressions) | 色彩運算式可讓您更輕鬆地建立和操作色彩值。 |
| [條件運算式](#conditional-expressions) | 條件運算式會提供類似 if 語句的邏輯作業。 |
| [日期運算式](#data-expressions) | 提供功能中屬性資料的存取權。 |
| [插補和步驟運算式](#interpolate-and-step-expressions) | 插補和步驟運算式可以用來透過插入曲線或步驟函式來計算值。 |
| [圖層特定運算式](#layer-specific-expressions) | 僅適用于單一層的特殊運算式。 |
| [數學運算式](#math-expressions) | 提供數學運算子來執行運算式架構內的資料驅動計算。 |
| [字串運算子運算式](#string-operator-expressions) | 字串運算子運算式會對字串執行轉換作業，例如串連和轉換案例。 |
| [類型運算式](#type-expressions) | 型別運算式提供的工具可用來測試和轉換不同的資料類型，例如字串、數位和布林值。 |
| [變數系結運算式](#variable-binding-expressions) | 變數系結運算式可讓計算的結果儲存在變數中，並在運算式中的其他地方多次參考，而不需要重新計算儲存的值。 |
| [Zoom 運算式](#zoom-expression) | 在轉譯時期，抓取地圖的目前縮放層級。 |

本檔中的所有範例都會使用下列功能，示範可以使用不同類型運算式的不同方式。 

```javascript
{
    "type": "Feature",
    "geometry": {
        "type": "Point",
        "coordinates": [-122.13284, 47.63699]
    },
    "properties": { 
        "id": 123,
        "entityType": "restaurant",
        "revenue": 12345,
        "subTitle": "Building 40", 
        "temperature": 72,
        "title": "Cafeteria", 
        "zoneColor": "red"
    }
}
```

## <a name="data-expressions"></a>日期運算式

日期運算式可讓您存取功能中的屬性資料。 

| 運算是 | 傳回類型 | 說明 |
|------------|-------------|-------------|
| `['at', number, array]` | object | 從陣列中抓取專案。 |
| `['geometry-type']` | string | 取得功能的 geometry 類型： Point、MultiPoint、LineString、MultiLineString、多邊形、MultiPolygon。 |
| `['get', string]` | value | 從目前功能的屬性取得屬性值。 如果要求的屬性遺失，則傳回 null。 |
| `['get', string, object]` | value | 從提供之物件的屬性取得屬性值。 如果要求的屬性遺失，則傳回 null。 |
| `['has', string]` | boolean | 判斷功能的屬性是否具有指定的屬性。 |
| `['has', string, object]` | boolean | 判斷物件的屬性是否具有指定的屬性。 |
| `['id']` | value | 取得功能的識別碼（如果有的話）。 |
| `['length', string | array]` | number | 取得字串或陣列的長度。 |

**範例**

您可以使用 `get` 運算式，直接在運算式中存取功能的屬性。 下列範例會使用功能的 "zoneColor" 值來指定反升圖層的 color 屬性。 

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    color: ['get', 'zoneColor'] //Get the zoneColor value.
});
```

如果所有點特徵都具有 [`zoneColor`] 屬性，則上述範例會正常執行，但如果沒有，則色彩可能會切換回「黑色」。 若要修改回退色彩，`case` 運算式可以與 `has` 運算式搭配使用，以檢查屬性是否存在，以及是否不傳回回退色彩。

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    color: [
        'case', //Use a conditional case expression.

        ['has', 'zoneColor'],   //Check to see if feature has a "zoneColor" property
        ['get', 'zoneColor'],   //If it does, use it.

        'blue'  //If it doesn't, default to blue.
    ]
});
```

反升和符號圖層預設會轉譯資料來源中所有圖形的座標。 這可以完成以反白顯示多邊形或線條的頂點。 圖層的 `filter` 選項，可以使用布林運算式內的 `['geometry-type']` 運算式，來限制它所呈現之功能的幾何類型。 下列範例會限制反升圖層，只呈現 `Point` 的功能。

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    filter: ['==', ['geometry-type'], 'Point']
});
```

下列範例將允許轉譯 `Point` 和 `MultiPoint` 功能。 

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    filter: ['any', ['==', ['geometry-type'], 'Point'], ['==', ['geometry-type'], 'MultiPoint']]
});
```

同樣地，多邊形的外框也會以線條圖層呈現。 若要線上條圖層中停用此行為，請加入僅允許 `LineString` 和 `MultiLineString` 功能的篩選。  

## <a name="math-expressions"></a>數學運算式

數學運算式提供數學運算子，以在 expression framework 中執行資料驅動計算。

| 運算是 | 傳回類型 | 說明 |
|------------|-------------|-------------|
| `['+', number, number, …]` | number | 計算指定數位的總和。 |
| `['-', number]` | number | 以指定的數位減去0。 |
| `['-', number, number]` | number | 將第一個數位減去第二個數字。 |
| `['*', number, number, …]` | number | 將指定的數位相乘。 |
| `['/', number, number]` | number | 將第一個數位除以第二個數字。 |
| `['%', number, number]` | number | 計算將第一個數位除以第二個數字時的餘數。 |
| `['^', number, number]` | number | 計算第一個值的值，該值為第二個數字的乘冪。 |
| `['abs', number]` | number | 計算指定之數字的絕對值。 |
| `['acos', number]` | number | 計算指定數位的反余弦值。 |
| `['asin', number]` | number | 計算指定數位的反正弦值。 |
| `['atan', number]` | number | 計算指定數位的反正切值。 |
| `['ceil', number]` | number | 將數位四捨五入到下一個整數。 |
| `['cos', number]` | number | 計算指定數位的 cos。 |
| `['e']` | number | 傳回 `e`的數學常數。 |
| `['floor', number]` | number | 將數位向下舍入到前一個整數。 |
| `['ln', number]` | number | 計算指定數位的自然對數。 |
| `['ln2']` | number | 傳回 `ln(2)`的數學常數。 |
| `['log10', number]` | number | 計算指定數位的底數為10的對數。 |
| `['log2', number]` | number | 計算指定數位的底數為2的對數。 |
| `['max', number, number, …]` | number | 計算指定數位集合中的最大數目。 |
| `['min', number, number, …]` | number | 計算指定數位集合中的最小數目。 |
| `['pi']` | number | 傳回 `PI`的數學常數。 |
| `['round', number]` | number | 將數位四捨五入至最接近的整數。 中間值會從零進位出來。 例如，`['round', -1.5]` 會評估為-2。 |
| `['sin', number]` | number | 計算指定數位的正弦值。 |
| `['sqrt', number]` | number | 計算指定之數字的平方根。 |
| `['tan', number]` | number | 計算指定數位的正切函數。 |

## <a name="aggregate-expression"></a>匯總運算式

匯總運算式會定義在一組資料上處理的計算，並可搭配 `DataSource`的 `clusterProperties` 選項使用。 這些運算式的輸出必須是數位或布林值。 

匯總運算式採用三個值;運算子值和初始值，以及從資料中的每項功能抓取屬性以套用匯總運算的運算式。 此運算式具有下列格式：

```javascript
[operator: string, initialValue: boolean | number, mapExpression: Expression]
```

- operator：接著會針對叢集中每個點的 `mapExpression` 所計算的所有值，套用至運算式函數。 支援的運算子; 
    - 若為數字： `+`、`*`、`max`、`min`
    - 針對布林值： `all`，`any`
- initialValue：初始值，其中的第一個計算值會針對進行匯總。
- mapExpression：套用至資料集內每個點的運算式。

**範例**

如果資料集內的所有功能都有一個 `revenue` 的屬性，也就是一個數位。 您可以使用下列匯總運算式來計算從資料集建立之叢集中所有點的總收益： `['+', 0, ['get', 'revenue']]`

## <a name="boolean-expressions"></a>布林運算式

布林運算式提供一組布林運算子運算式來評估布林值比較。

比較值時，會以嚴格的類型進行比較。 不同類型的值一律視為不相等。 在剖析階段已知類型不同的情況會被視為無效，而且會產生剖析錯誤。 

| 運算是 | 傳回類型 | 說明 |
|------------|-------------|-------------|
| `['! ', boolean]` | boolean | 邏輯否定。 如果輸入 `false`，則傳回 `true`，如果輸入 `true`，則傳回 `false`。 |
| `['!= ', value, value]` | boolean | 如果輸入值不相等，則傳回 `true`，否則 `false`。 |
| `['<', value, value]` | boolean | 如果第一個輸入嚴格小於第二個輸入，則會傳回 `true`，否則會 `false`。 引數必須是字串或兩個數字。 |
| `['<=', value, value]` | boolean | 如果第一個輸入小於或等於第二個輸入，則會傳回 `true`，否則會傳回 `false`。 引數必須是字串或兩個數字。 |
| `['==', value, value]` | boolean | 如果輸入值相等，則傳回 `true`，否則傳回 `false`。 引數必須是字串或兩個數字。 |
| `['>', value, value]` | boolean | 如果第一個輸入嚴格大於第二個輸入，則會傳回 `true`，否則會 `false`。 引數必須是字串或兩個數字。 |
| `['>=' value, value]` | boolean | 如果第一個輸入大於或等於第二個輸入，則傳回 `true`，否則傳回 `false`。 引數必須是字串或兩個數字。 |
| `['all', boolean, boolean, …]` | boolean | 如果所有輸入都 `true`，則會傳回 `true`，否則會傳回 `false`。 |
| `['any', boolean, boolean, …]` | boolean | 如果有任何輸入 `true`，則會傳回 `true`，否則會傳回 `false`。 |

## <a name="conditional-expressions"></a>條件運算式

條件運算式會提供類似 if 語句的邏輯作業。

下列運算式會針對輸入資料執行條件式邏輯作業。 例如，`case` 運算式會提供 "if/then/else" 邏輯，而 `match` 運算式就像是 "switch 語句"。 

### <a name="case-expression"></a>Case 運算式

`case` 運算式是一種條件運算式，提供 if 語句，例如邏輯（if/then/else）。 這種類型的運算式會逐步執行布林條件清單，並傳回第一個 true 布林條件的輸出值。

下列虛擬代碼會定義 `case` 運算式的結構。 

```javascript
[
    'case',
    condition1: boolean, 
    output1: value,
    condition2: boolean, 
    output2: value,
    ...,
    fallback: value
]
```

**範例**

下列範例會逐步執行不同的 boolean 條件，直到找到評估為 `true`的布林值，然後傳回該相關聯的值。 如果沒有布林條件評估為 `true`，則會傳回 fallback 值。 

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    color: [
        'case',

        //Check to see if the first boolean expression is true, and if it is, return its assigned result.
        ['has', 'zoneColor'],
        ['get', 'zoneColor'],

        //Check to see if the second boolean expression is true, and if it is, return its assigned result.
        ['all', ['has', ' temperature '], ['>', ['get', 'temperature'], 100]],
        'red',

        //Specify a default value to return.
        'green'
    ]
});
```

### <a name="match-expression"></a>Match 運算式

`match` 運算式是提供 switch 語句（例如邏輯）的條件運算式類型。 輸入可以是任何運算式，例如傳回字串或數位的 `['get', 'entityType']`。 每個標籤都必須是單一常值或常值陣列，其值必須是所有字串或所有數位。 如果陣列中有任何值相符，則輸入會符合。 每個標籤都必須是唯一的。 如果輸入類型不符合標籤的類型，則結果會是 fallback 值。

下列虛擬代碼會定義 `match` 運算式的結構。 

```javascript
[
    'match',
    input: number | string,
    label1: number | string | (number | string)[], 
    output1: value,
    label2: number | string | (number | string)[], 
    output2: value,
    ...,
    fallback: value
]
```

**範例**

下列範例會查看反升圖層中點特徵的 `entityType` 屬性會搜尋相符項。 如果找到相符的值，則會傳回指定的值，否則會傳回 fallback 值。

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    color: [
        'match',

        //Get the property to match.
        ['get', 'entityType'],

        //List the values to match and the result to return for each match.
        'restaurant', 'red',
        'park', 'green',

        //Specify a default value to return if no match is found.
        'black'
    ]
});
```

下列範例會使用陣列來列出一組應全部傳回相同值的標籤。 這比個別列出每個標籤更有效率。 在此情況下，如果 `entityType` 屬性是「餐廳」或「grocery_store」，則會傳回「紅色」的色彩。

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    color: [
        'match',

        //Get the property to match.
        ['get', 'entityType'],

        //List the values to match and the result to return for each match.
        ['restaurant', 'grocery_store'], 'red',

        'park', 'green',

        //Specify a default value to return if no match is found.
        'black'
    ]
});
```

下列範例會使用 match 運算式來執行「in 陣列」或「array contains」類型篩選準則，在此情況下，會篩選具有允許識別碼清單中識別碼值的資料。 當搭配使用運算式與篩選準則時，結果必須是布林值。

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    filter: [
        'match',  

        //Get the property to match.
        ['get', 'id'],  

         //List of values to match.
        [24, 53, 98], 

        //If there is a match, return true.
        true,
    
        //Otherwise return false.
        false
    ]
});
```

### <a name="coalesce-expression"></a>聯合運算式

`coalesce` 運算式會逐步執行一組運算式，直到取得第一個非 null 值，並傳回該值。 

下列虛擬代碼會定義 `coalesce` 運算式的結構。 

```javascript
[
    'coalesce', 
    value1, 
    value2, 
    …
]
```

**範例**

下列範例會使用 `coalesce` 運算式來設定符號圖層的 `textField` 選項。 如果功能遺漏 `title` 屬性或將其設定為 `null`，則運算式會接著嘗試尋找 `subtitle` 屬性（如果遺漏或 `null`的話），接著會切換回空字串。 

```javascript
var layer = new atlas.layer.SymbolLayer(datasource, null, {
    textOptions: {
        textField: [
            'coalesce',

            //Try getting the title property.
            ['get', 'title'],

            //If there is no title, try getting the subtitle. 
            ['get', 'subtitle'],

            //Default to an empty string.
            ''
        ]
    }
});
```

下列範例會使用 `coalesce` 運算式，從指定的影像名稱清單中取出地圖 sprite 中可用的第一個可用的影像圖示。

```javascript
var layer = new atlas.layer.SymbolLayer(datasource, null, {
    iconOptions: {
        image: [
            'coalesce',

            //Try getting the image with id 'missing-image'.
            ['image', 'missing-image'],

            //Specify an image id to fallback to. 
            'marker-blue'
        ]
    }
});
``` 

## <a name="type-expressions"></a>類型運算式

型別運算式提供的工具可用來測試和轉換不同的資料類型，例如字串、數位和布林值。

| 運算是 | 傳回類型 | 說明 |
|------------|-------------|-------------|
| `['literal', array]`<br/><br/>`['literal', object]` | 陣列 \| 物件 | 傳回常值陣列或物件值。 使用此運算式來防止陣列或物件被評估為運算式。 當運算式必須傳回陣列或物件時，這是必要的。 |
| `['image', string]` | string | 檢查是否已將指定的映射識別碼載入地圖影像 sprite。 如果是，則會傳回識別碼，否則會傳回 null。 |
| `['to-boolean', value]` | boolean | 將輸入值轉換為布林值。 當輸入是空字串、`0`、`false`、`null`或 `NaN`時，會產生 `false` 結果。否則其 `true`。 |
| `['to-color', value]`<br/><br/>`['to-color', value1, value2…]` | color | 將輸入值轉換成色彩。 如果提供多個值，則會依序評估每一個值，直到取得第一個成功的轉換為止。 如果無法轉換任何輸入，則運算式會是錯誤。 |
| `['to-number', value]`<br/><br/>`['to-number', value1, value2, …]` | number | 盡可能將輸入值轉換成數位。 如果輸入 `null` 或 `false`，則結果為0。 如果輸入是 `true`，則結果為1。 如果輸入為字串，則會使用 ECMAScript 語言規格的[ToNumber](https://tc39.github.io/ecma262/#sec-tonumber-applied-to-the-string-type)字串函數轉換成數位。 如果提供多個值，則會依序評估每一個值，直到取得第一個成功的轉換為止。 如果無法轉換任何輸入，則運算式會是錯誤。 |
| `['to-string', value]` | string | 將輸入值轉換成字串。 如果輸入 `null`，則會 `""`結果。 如果輸入是布林值，則結果會是 `"true"` 或 `"false"`。 如果輸入是數位，則會使用 ECMAScript 語言規格的[ToString](https://tc39.github.io/ecma262/#sec-tostring-applied-to-the-number-type) number 函數將它轉換成字串。 如果輸入是色彩，則會將它轉換成 CSS RGBA 色彩字串 `"rgba(r,g,b,a)"`。 否則，會使用 ECMAScript 語言規格的[json.stringify](https://tc39.github.io/ecma262/#sec-json.stringify)函數，將輸入轉換成字串。 |
| `['typeof', value]` | string | 傳回描述指定值之類型的字串。 |

> [!TIP]
> 如果類似于 `Expression name must be a string, but found number instead. If you wanted a literal array, use ["literal", [...]].` 的錯誤訊息出現在瀏覽器主控台中，表示您的程式碼中有一個運算式，其中有一個陣列沒有第一個值的字串。 如果您想要運算式傳回陣列，請使用 `literal` 運算式來包裝陣列。 下列範例會設定符號圖層的圖示 `offset` 選項，其必須是包含兩個數字的陣列，方法是使用 `match` 運算式，根據 point 功能的 `entityType` 屬性值，在兩個位移值之間進行選擇。
>
> ```javascript
> var layer = new atlas.layer.SymbolLayer(datasource, null, {
>     iconOptions: {
>         offset: [
>             'match',
>
>             //Get the entityType value.
>             ['get', 'entityType'],
>
>             //If the entity type is 'restaurant', return a different pixel offset. 
>             'restaurant', ['literal', [0, -10]],
>
>             //Default to value.
>             ['literal', [0, 0]]
>         ]
>     }
> });
> ```

## <a name="color-expressions"></a>色彩運算式

色彩運算式可讓您更輕鬆地建立和操作色彩值。

| 運算是 | 傳回類型 | 說明 |
|------------|-------------|-------------|
| `['rgb', number, number, number]` | color | 從*紅色*、*綠色*和*藍色*元件建立色彩值，其範圍必須介於 `0` 和 `255`之間，以及 `1`的 Alpha 元件。 如果有任何元件超出範圍，運算式就會是錯誤。 |
| `['rgba', number, number, number, number]` | color | 從*紅色*、*綠色*、*藍色*元件建立色彩值，其範圍必須介於 `0` 和 `255`之間，以及 `0` 和 `1`範圍內的 Alpha 元件。 如果有任何元件超出範圍，運算式就會是錯誤。 |
| `['to-rgba']` | \[數位、數位、數位、數位\] | 傳回四個元素的陣列，其中包含輸入色彩的*紅色*、*綠色*、*藍色*和*Alpha*元件（依該順序）。 |

**範例**

下列範例會建立具有*紅色*值 `255`的和 RGB 色彩值，並以 `temperature` 屬性值乘以 `2.5` 來計算*綠色*和*藍色*值。 當溫度變更時，色彩會變更為不同的*紅色*陰影。

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    color: [
        'rgb', //Create a RGB color value.

        255,    //Set red value to 255.

        ['*', 2.5, ['get', 'temperature']], //Multiple the temperature by 2.5 and set the green value.

        ['*', 2.5, ['get', 'temperature']]  //Multiple the temperature by 2.5 and set the blue value.
    ]
});
```

## <a name="string-operator-expressions"></a>字串運算子運算式

字串運算子運算式會對字串執行轉換作業，例如串連和轉換案例。 

| 運算是 | 傳回類型 | 說明 |
|------------|-------------|-------------|
| `['concat', string, string, …]` | string | 將多個字串串連在一起。 每個值都必須是字串。 如有需要，請使用 `to-string` 類型運算式將其他實數值型別轉換成字串。 |
| `['downcase', string]` | string | 轉換指定字串為小寫。 |
| `['upcase', string]` | string | 轉換指定字串為大寫。 |

**範例**

下列範例會將 point 功能的 `temperature` 屬性轉換成字串，然後將 "° F" 串連到它的結尾。

```javascript
var layer = new atlas.layer.SymbolLayer(datasource, null, {
    textOptions: {
        textField: ['concat', ['to-string', ['get', 'temperature']], '°F'],

        //Some additional style options.
        offset: [0, -1.5],
        size: 12,
        color: 'white'
    }
});
```

上述運算式會在地圖上轉譯一個釘選，其中的文字 "64 ° F" 會重迭在其上方，如下圖所示。

<center>

![字串運算子運算式範例](media/how-to-expressions/string-operator-expression.png) </center>

## <a name="interpolate-and-step-expressions"></a>插補和步驟運算式

插補和步驟運算式可以用來透過插入曲線或步驟函式來計算值。 這些運算式會採用傳回數值做為其輸入的運算式，例如 `['get',  'temperature']`。 輸入值會針對輸入和輸出值的配對進行評估（稱為「停止」），以判斷最適合插入曲線或步驟函式的值。 每個停止的輸入值都必須是數位，並以遞增順序排列。 輸出值必須是數位、數位陣列或色彩。

### <a name="interpolate-expression"></a>插補運算式

`interpolate` 運算式可以藉由在停止值之間插入，來計算連續、平滑的值集合。 傳回色彩值的 `interpolate` 運算式會產生色彩漸層，其中會選取結果值。

有三種類型的插補方法可在 `interpolate` 運算式中使用：
 
* `['linear']`-在一組停止之間呈線性插補。
* `['exponential', base]`-在停止之間以指數方式插補。 `base` 值控制輸出增加的速率。 較高的值會使輸出增加至範圍的高階。 接近1的 `base` 值會產生更線性增加的輸出。
* `['cubic-bezier', x1, y1, x2, y2]`-使用指定控制點所定義的[三次方貝茲曲線](https://developer.mozilla.org/docs/Web/CSS/timing-function)來進行插補。

以下是這些不同插補類型外觀的範例。 

| 線性  | 指數 | 三次方貝茲 |
|---------|-------------|--------------|
| ![線性插補圖形](media/how-to-expressions/linear-interpolation.png) | ![指數插補圖形](media/how-to-expressions/exponential-interpolation.png) | ![三次方貝塞爾插值圖形](media/how-to-expressions/bezier-curve-interpolation.png) |

下列虛擬代碼會定義 `interpolate` 運算式的結構。 

```javascript
[
    'interpolate',
    interpolation: ['linear'] | ['exponential', base] | ['cubic-bezier', x1, y1, x2, y2],
    input: number,
    stopInput1: number, 
    stopOutput1: value1,
    stopInput2: number, 
    stopOutput2: value2, 
    ...
]
```

**範例**

下列範例會根據 point 功能的 `temperature` 屬性，使用 `linear interpolate` 運算式來設定反升圖層的 `color` 屬性。 如果 `temperature` 值小於60，則會傳回 "blue"，如果介於60到低於70之間，則會傳回黃色，如果介於70和小於80，則會傳回黃色，如果有80或更大，則傳回「紅色」。

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    color: [
        'interpolate',
        ['linear'],
        ['get', 'temperature'],
        50,        
        'blue',
        60,
        'yellow',
        70,
        'orange',
        80,
        'red'
    ]
});
```

下圖將示範如何為上述運算式選擇色彩。
 
<center>

![插補運算式範例](media/how-to-expressions/interpolate-expression-example.png) </center>

### <a name="step-expression"></a>步驟運算式

`step` 運算式可以藉由評估停止所定義的[分段常數](http://mathworld.wolfram.com/PiecewiseConstantFunction.html)函式，來計算離散的階數結果值。 

下列虛擬代碼會定義 `step` 運算式的結構。 

```javascript
[
    'step',
    input: number,
    output0: value0,
    stop1: number, 
    output1: value1,
    stop2: number, 
    output2: value2, 
    ...
]
```

步驟運算式會在輸入值之前傳回 stop 的輸出值，如果輸入小於第一個停止，則傳回第一個輸入值。 

**範例**

下列範例會根據 point 功能的 `temperature` 屬性，使用 `step` 運算式來設定反升圖層的 `color` 屬性。 如果 `temperature` 值小於60，則會傳回 "blue"，如果介於60到低於70，則會傳回 "黃色"，如果介於70和小於80，則會傳回 "黃"，如果有80或更大，則傳回 "red"。

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    color: [
        'step',
        ['get', 'temperature'],
        'blue',
        60,
        'yellow',
        70,
        'orange',
        80,
        'red'
    ]
});
```

下圖將示範如何為上述運算式選擇色彩。
 
<center>

![步驟運算式範例](media/how-to-expressions/step-expression-example.png)
</center>

## <a name="layer-specific-expressions"></a>圖層特定運算式

僅適用于特定層級的特殊運算式。

### <a name="heat-map-density-expression"></a>熱度圖密度運算式

熱度圖密度運算式會抓取熱度圖圖層中每個圖元的熱度圖密度值，並將其定義為 `['heatmap-density']`。 這個值是介於 `0` 和 `1` 之間的數位，並與 `interpolation` 或 `step` 運算式搭配使用，以定義用來為熱度圖著色的色彩漸層。 這個運算式只能用在熱度圖圖層的[色彩選項](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.heatmaplayeroptions?view=azure-iot-typescript-latest#color)中。

> [!TIP]
> 插補運算式中位於索引0的色彩，或步驟色彩的預設色彩，會定義沒有資料並可用來定義背景色彩的區域色彩。 許多人偏好將此值設定為透明或半透明的黑色。 

**範例**

這個範例會使用「程式插值」運算式來建立平滑的色彩漸層，以呈現熱度圖。 

```javascript 
var layer = new atlas.layer.HeatMapLayer(datasource, null, {
    color: [
        'interpolate',
        ['linear'],
        ['heatmap-density'],
        0, 'transparent',
        0.01, 'purple',
        0.5, '#fb00fb',
        1, '#00c3ff'
    ]
});
```

除了使用平滑漸層來為熱度圖著色之外，也可以使用 `step` 運算式，在一組範圍內指定色彩。 使用 `step` 運算式來上色熱度圖，會以視覺化方式將密度分解成類似輪廓或雷達圖樣式地圖的範圍。  

```javascript 
var layer = new atlas.layer.HeatMapLayer(datasource, null, {
    color: [
        'step',
        ['heatmap-density'],
        'transparent',
        0.01, 'navy',
        0.25, 'navy',
        0.5, 'green',
        0.75, 'yellow',
        1, 'red'
    ]
});
```

如需詳細資訊，請參閱[新增熱度圖層](map-add-heat-map-layer.md)檔。

### <a name="line-progress-expression"></a>行進度運算式

線條進度運算式會沿著線條圖層中的梯度線抓取進度，並將其定義為 `['line-progress']`。 這個值是介於0和1之間的數位，而且會與 `interpolation` 或 `step` 運算式搭配使用。 這個運算式只能與線條圖層的[strokeGradient 選項]( https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.linelayeroptions?view=azure-iot-typescript-latest#strokegradient)搭配使用。 

> [!NOTE]
> 線條圖層的 `strokeGradient` 選項需要將資料來源的 [`lineMetrics`] 選項設定為 [`true`]。

**範例**

下列範例會使用 `['line-progress']` 運算式，將色彩漸層套用至線條的筆劃。

```javascript
var layer = new atlas.layer.LineLayer(datasource, null, {
    strokeGradient: [
        'interpolate',
        ['linear'],
        ['line-progress'],
        0, "blue",
        0.1, "royalblue",
        0.3, "cyan",
        0.5, "lime",
        0.7, "yellow",
        1, "red"
    ]
});
```

[查看即時範例](map-add-line-layer.md#line-stroke-gradient)

### <a name="text-field-format-expression"></a>文字欄位格式運算式

文字欄位格式運算式可以與符號圖層 `textOptions` 屬性的 `textField` 選項搭配使用，以提供混合文字格式。 這個運算式允許指定一組輸入字串和格式化選項。 您可以在此運算式中指定每個輸入字串的下列選項。

 * `'font-scale'`-指定字型大小的縮放比例。 如果指定的話，這個值將會覆寫個別字串之 `textOptions` 的 `size` 屬性。
 * `'text-font'`-指定此字串應使用的一或多個字型系列。 如果指定的話，這個值將會覆寫個別字串之 `textOptions` 的 `font` 屬性。
 * `'text-color'`-指定呈現時要套用至文字的色彩。 

下列虛擬代碼會定義文字欄位格式運算式的結構。 

```javascript
[
    'format', 
    input1: string, 
    options1: { 
        'font-scale': number, 
        'text-font': string[],
        'text-color': color
    },
    input2: string, 
    options2: { 
        'font-scale': number, 
        'text-font': string[] ,
        'text-color': color
    },
    …
]
```

**範例**

下列範例會藉由新增粗體字型並相應增加功能之 `title` 屬性的字型大小，來格式化文字欄位。 這個範例也會將功能的 [`subtitle`] 屬性加入至新行，並以相應縮小的字型大小和彩色紅色。

```javascript
var layer = new atlas.layer.SymbolLayer(datasource, null, {
    textOptions: {
        textField: [
            'format',

            //Bold the title property and scale its font size up.
            ['get', 'title'],
            {
                'text-font': ['literal', ['StandardFont-Bold']],
                'font-scale': 1.25
            },

            '\n', {},   //Add a new line without any formatting.

            //Scale the font size down of the subtitle property. 
            ['get', 'subtitle'],
            { 
                'font-scale': 0.75, 
                'text-color': 'red' 
            }
        ]
    }
});
```

此圖層會轉譯點功能，如下圖所示：
 
<center>

![具有格式化文字欄位的 Point 功能影像](media/how-to-expressions/text-field-format-expression.png) </center>

### <a name="number-format-expression"></a>數位格式運算式

`number-format` 運算式只能與符號層的 `textField` 選項搭配使用。 這個運算式會將提供的數位轉換成格式化字串。 這個運算式會包裝 JavaScript 的[toLocalString](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Number/toLocaleString)函數，並支援下列選項組。

 * `locale`-指定此選項可將數位轉換成字串，使其與指定的語言對齊。 將[BCP 47 語言標記](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Intl#Locale_identification_and_negotiation)傳遞到此選項。
 * `currency`-將數位轉換成代表貨幣的字串。 可能的值為[ISO 4217 貨幣代碼](https://en.wikipedia.org/wiki/ISO_4217)，例如美國元的「美元」、歐元的「歐元」或中文 RMB 的「CNY」。
 * `'min-fraction-digits'`-指定數位的字串版本中要包含的最小小數位數。
 * `'max-fraction-digits'`-指定數位的字串版本中要包含的最大小數位數。

下列虛擬代碼會定義文字欄位格式運算式的結構。 

```javascript
[
    'number-format', 
    input: number, 
    options: {
        locale: string, 
        currency: string, 
        'min-fraction-digits': number, 
        'max-fraction-digits': number
    }
]
```

**範例**

下列範例會使用 `number-format` 運算式來修改點特徵的 `revenue` 屬性如何在符號圖層的 `textField` 選項中呈現，使其顯示 US 美元的值。

```javascript
var layer = new atlas.layer.SymbolLayer(datasource, null, {
    textOptions: {
        textField: [
            'number-format', 
            ['get', 'revenue'], 
            { ‘currency’: 'USD' }
        ],

        offset: [0, 0.75]
    }
});
```

此圖層會轉譯點功能，如下圖所示：

<center>

![數位格式運算式範例](media/how-to-expressions/number-format-expression.png) </center>

## <a name="zoom-expression"></a>Zoom 運算式

`zoom` 運算式會在轉譯時用來抓取地圖的目前縮放層級，並定義為 `['zoom']`。 這個運算式會傳回地圖的最小和最大縮放層級範圍之間的數位。 使用此運算式可讓您以動態方式修改樣式，因為地圖的縮放層級已變更。 `zoom` 運算式只能與 `interpolate` 和 `step` 運算式搭配使用。

**範例**

根據預設，熱度圖圖層中轉譯之資料點的半徑對於所有縮放層級都有固定圖元半徑。 因為地圖會將資料匯總放大，而且熱度圖層看起來會不同。 `zoom` 運算式可以用來調整每個縮放層級的半徑，讓每個資料點都涵蓋對應的相同實體區域。 這會讓熱度圖層看起來更為靜態且一致。 地圖的每個縮放層級都會以垂直和水準方式，與上一個縮放層級相同的圖元數倍。 調整半徑，讓每個縮放層級加倍，會建立在所有縮放比例上看起來一致的熱度圖。 您可以使用 `zoom` 運算式搭配 `base 2 exponential interpolation` 運算式來完成這項作業，如下所示。 

```javascript 
var layer = new atlas.layer.HeatMapLayer(datasource, null, {
    radius: [
        'interpolate',
        ['exponential', 2],
        ['zoom'],
        
        //For zoom level 1 set the radius to 2 pixels.
        10, 2,

        //Between zoom level 1 and 19, exponentially scale the radius from 2 pixels to 10,000 pixels.
        19, 10000
    ]
};
```

[查看即時範例](map-add-heat-map-layer.md#consistent-zoomable-heat-map)

## <a name="variable-binding-expressions"></a>變數系結運算式

變數系結運算式會將計算的結果儲存在變數中，讓運算式中的其他地方可以多次參考，而不必重新計算。 對於牽涉到許多計算的運算式而言，這是有用的優化

| 運算是 | 傳回類型 | 說明 |
|--------------|---------------|--------------|
| \[<br/>&nbsp;&nbsp;&nbsp;&nbsp;「let」，<br/>&nbsp;&nbsp;&nbsp;&nbsp;name1：字串，<br/>&nbsp;&nbsp;&nbsp;&nbsp;value1： any，<br/>&nbsp;&nbsp;&nbsp;&nbsp;name2： string，<br/>&nbsp;&nbsp;&nbsp;&nbsp;value2： any，<br/>&nbsp;&nbsp;&nbsp;&nbsp;。<br/>&nbsp;&nbsp;&nbsp;&nbsp;childExpression<br/>\] | | 儲存一或多個值做為變數，以供傳回結果的子運算式中的 `var` 運算式使用。 |
| `['var', name: string]` | 任意 | 參考使用 `let` 運算式建立的變數。 |

**範例**

這個範例會使用運算式來計算相對於溫度比率的收益，然後使用 `case` 運算式來評估此值的不同布耳運算。 `let` 運算式是用來儲存相對於溫度比率的收益，因此它只需要計算一次，而 `var` 運算式會經常參考此變數，而不必重新計算。

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    color: [
        //Divide the point features `revenue` property by the `temperature` property and store it in a variable called `ratio`.
        'let', 'ratio', ['/', ['get', 'revenue'], ['get', 'temperature']],
        //Evaluate the child expression in which the stored variable will be used.
        [
            'case',

            //Check to see if the ratio is less than 100, return 'red'.
            ['<', ['var', 'ratio'], 100],
            'red',

            //Check to see if the ratio is less than 200, return 'green'.
            ['<', ['var', 'ratio'], 200],
            'green',

            //Return `blue` for values greater or equal to 200.
            'blue'
        ]
    ]
});
```

## <a name="next-steps"></a>後續步驟

如需更多執行運算式的程式碼範例，請參閱下列文章：

> [!div class="nextstepaction"] 
> [新增符號圖層](map-add-pin.md)

> [!div class="nextstepaction"] 
> [新增泡泡圖層](map-add-bubble-layer.md)

> [!div class="nextstepaction"]
> [新增線條圖層](map-add-line-layer.md)

> [!div class="nextstepaction"]
> [新增多邊形圖層](map-add-shape.md)

> [!div class="nextstepaction"] 
> [新增熱度圖圖層](map-add-heat-map-layer.md)

深入瞭解支援運算式的圖層選項：

> [!div class="nextstepaction"] 
> [BubbleLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.bubblelayeroptions?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"] 
> [HeatMapLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.heatmaplayeroptions?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"] 
> [LineLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.linelayeroptions?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"] 
> [PolygonLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.polygonlayeroptions?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"] 
> [SymbolLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.symbollayeroptions?view=azure-iot-typescript-latest) 
