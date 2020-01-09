---
title: 語音合成標記語言（SSML）-語音服務
titleSuffix: Azure Cognitive Services
description: 使用語音合成標記語言控制文字轉語音的發音和韻律。
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: erhopf
ms.openlocfilehash: d97073666a18a3ffb7a88e1d2350f213ef589e6a
ms.sourcegitcommit: 5925df3bcc362c8463b76af3f57c254148ac63e3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/31/2019
ms.locfileid: "75562522"
---
# <a name="speech-synthesis-markup-language-ssml"></a>語音合成標記語言 (SSML)

語音合成標記語言（SSML）是以 XML 為基礎的標記語言，可讓開發人員指定如何使用文字轉換語音服務，將輸入文字轉換成合成語音。 相較于純文字，SSML 讓開發人員能夠微調文字到語音轉換輸出的音調、發音、說話速度、音量和更多。 一般標點符號（例如在句號之後暫停），或使用正確的聲調（當句子以問號結尾時）會自動處理。

SSML 的語音服務執行是以全球資訊網協會的[語音合成標記語言1.0 版](https://www.w3.org/TR/speech-synthesis)為基礎。

> [!IMPORTANT]
> 中文、日文和韓文字元的計費方式為兩個字元。 如需詳細資訊，請參閱[定價](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/)。

## <a name="standard-neural-and-custom-voices"></a>標準、類神經和自訂語音

從標準和類神經語音中選擇，或為您的產品或品牌建立專屬的自訂語音。 75 + standard 語音提供45以上的語言和地區設定，而5類神經語音則提供4種語言和地區設定。 如需支援的語言、地區設定和語音 (神經和標準) 的完整清單，請參閱[語言支援](language-support.md)。

若要深入瞭解標準、類神經和自訂語音，請參閱[文字轉換語音的總覽](text-to-speech.md)。

## <a name="special-characters"></a>特殊字元

使用 SSML 轉換文字到合成的語音時，請記住，就像使用 XML 一樣，特殊字元（例如引號、撇號和方括弧）必須經過轉義。 如需詳細資訊，請參閱[可延伸標記語言 (XML) （XML）1.0：附錄 D](https://www.w3.org/TR/xml/#sec-entexpand)。

## <a name="supported-ssml-elements"></a>支援的 SSML 元素

每個 SSML 檔都是使用 SSML 元素（或標記）所建立。 這些元素可用來調整音調、韻律、音量等等。 下列各節將詳細說明每個專案的使用方式，以及當元素為必要或選擇性時。  

> [!IMPORTANT]
> 別忘了在屬性值前後使用雙引號。 格式正確且有效的 XML 的標準需要以雙引號括住屬性值。 例如，`<prosody volume="90">` 是格式正確且有效的元素，但 `<prosody volume=90>` 不是。 SSML 可能無法辨識不是以引號括住的屬性值。

## <a name="create-an-ssml-document"></a>建立 SSML 檔

`speak` 是根項目，而且是所有 SSML 檔的**必要**專案。 `speak` 元素包含重要資訊，例如版本、語言和標記詞彙定義。

**語法**

```xml
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xml:lang="string"></speak>
```

**屬性**

| 屬性 | 說明 | 必要/選用 |
|-----------|-------------|---------------------|
| version | 表示用來解讀檔標記的 SSML 規格版本。 目前的版本為1.0。 | 必要項 |
| xml:lang | 指定根文檔的語言。 此值可包含小寫、兩個字母的語言代碼（例如**en**），或語言代碼和大寫國家/地區（例如， **en-us**）。 | 必要項 |
| xmlns | 指定檔的 URI，以定義 SSML 檔的標記詞彙（元素類型和屬性名稱）。 目前的 URI 為 https://www.w3.org/2001/10/synthesis 。 | 必要項 |

## <a name="choose-a-voice-for-text-to-speech"></a>選擇文字轉換語音的語音

需要 `voice` 元素。 它是用來指定文字轉換語音所使用的語音。

**語法**

```xml
<voice name="string">
    This text will get converted into synthesized speech.
</voice>
```

**屬性**

| 屬性 | 說明 | 必要/選用 |
|-----------|-------------|---------------------|
| NAME | 識別文字到語音轉換輸出所使用的語音。 如需支援的語音的完整清單，請參閱[語言支援](language-support.md#text-to-speech)。 | 必要項 |

**範例**

> [!NOTE]
> 這個範例會使用 `en-US-Jessa24kRUS` 的聲音。 如需支援的語音的完整清單，請參閱[語言支援](language-support.md#text-to-speech)。

```XML
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice  name="en-US-Jessa24kRUS">
        This is the text that is spoken.
    </voice>
</speak>
```

## <a name="use-multiple-voices"></a>使用多重音源

在 `speak` 元素內，您可以為文字到語音轉換輸出指定多個語音。 這些語音可以採用不同的語言。 針對每個聲音，文字必須包裝在 `voice` 元素中。

**屬性**

| 屬性 | 說明 | 必要/選用 |
|-----------|-------------|---------------------|
| NAME | 識別文字到語音轉換輸出所使用的語音。 如需支援的語音的完整清單，請參閱[語言支援](language-support.md#text-to-speech)。 | 必要項 |

**範例**

```xml
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice  name="en-US-Jessa24kRUS">
        Good morning!
    </voice>
    <voice  name="en-US-Guy24kRUS">
        Good morning to you too Jessa!
    </voice>
</speak>
```

## <a name="adjust-speaking-styles"></a>調整說話樣式

> [!IMPORTANT]
> 這項功能只適用于神經語音。

根據預設，文字轉換語音服務會針對標準和類神經語音使用中性說話樣式來合成文字。 使用神經語音時，您可以調整說話樣式，以 `<mstts:express-as>` 元素表達 cheerfulness、理解或情感。 這是語音服務特有的選擇性元素。

目前，這些類神經語音支援說話的樣式調整：
* `en-US-JessaNeural`
* `zh-CN-XiaoxiaoNeural`

變更會在句子層級套用，而樣式會因語音而有所不同。 如果樣式不受支援，服務將會以預設的中性說話樣式傳回語音。

**語法**

```xml
<mstts:express-as type="string"></mstts:express-as>
```

**屬性**

| 屬性 | 說明 | 必要/選用 |
|-----------|-------------|---------------------|
| type | 指定說話的樣式。 目前，說話樣式是語音特有的。 | 如果要調整類神經語音的說話樣式，則為必要。 如果使用 `mstts:express-as`，則必須提供類型。 如果提供了不正確值，則會忽略此元素。 |

使用此表格來判斷每個類神經語音支援哪些說話樣式。

| 語音 | 類型 | 說明 |
|-------|------|-------------|
| `en-US-JessaNeural` | 類型 =`cheerful` | 表達正面且滿意的表情 |
| | 類型 =`empathy` | 表達管也和認知的意義 |
| | 類型 =`chat` | 以偶爾、寬鬆的語調說話 |
| | 類型 =`newscast` | 表達類似于新聞廣播的正式音調 |
| | 類型 =`customerservice` | 以易記且患者的方式說話，做為客戶服務 |
| `zh-CN-XiaoxiaoNeural` | 類型 =`newscast` | 表達類似于新聞廣播的正式音調 |
| | 類型 =`sentiment` | 傳達觸控訊息或故事 |

**範例**

這個 SSML 程式碼片段說明如何使用 `<mstts:express-as>` 元素，將說話風格變更為 `cheerful`。

```xml
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xmlns:mstts="https://www.w3.org/2001/mstts" xml:lang="en-US">
    <voice name="en-US-JessaNeural">
        <mstts:express-as type="cheerful">
            That'd be just amazing!
        </mstts:express-as>
    </voice>
</speak>
```

## <a name="add-or-remove-a-breakpause"></a>新增或移除中斷/暫停

使用 `break` 元素，在單字之間插入暫停（或中斷），或防止文字轉換語音服務自動暫停。

> [!NOTE]
> 使用此元素可覆寫單字或片語的文字轉換語音（TTS）的預設行為（如果該單字或片語的合成語音非自然）。 將 `strength` 設定為 `none` 以防止韻律中斷，這會由文字轉換語音服務自動插入。

**語法**

```xml
<break strength="string" />
<break time="string" />
```

**屬性**

| 屬性 | 說明 | 必要/選用 |
|-----------|-------------|---------------------|
| 程度 | 使用下列其中一個值，指定暫停的相對持續時間：<ul><li>無</li><li>x-弱式</li><li>不足</li><li>中（預設值）</li><li>強式</li><li>x-強式</li></ul> | 選用 |
| time | 指定暫停的絕對持續時間（以秒或毫秒為單位）。 有效值為2和500的範例 | 選用 |

| 程度 | 說明 |
|----------|-------------|
| 無; 如果未提供任何值，則為 | 0 毫秒 |
| x-弱式 | 250毫秒 |
| 不足 | 500 毫秒 |
| 中 | 750 毫秒 |
| 強式 | 1000毫秒 |
| x-強式 | 1250毫秒 |


**範例**

```xml
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice  name="en-US-Jessa24kRUS">
        Welcome to Microsoft Cognitive Services <break time="100ms" /> Text-to-Speech API.
    </voice>
</speak>
```

## <a name="specify-paragraphs-and-sentences"></a>指定段落和句子

`p` 和 `s` 元素分別用來代表段落和句子。 如果沒有這些元素，文字轉換語音服務會自動決定 SSML 檔的結構。

`p` 元素可能包含文字和下列元素： `audio`、`break`、`phoneme`、`prosody`、`say-as`、`sub`、`mstts:express-as`和 `s`。

`s` 元素可能包含文字和下列元素： `audio`、`break`、`phoneme`、`prosody`、`say-as`、`mstts:express-as`和 `sub`。

**語法**

```XML
<p></p>
<s></s>
```

**範例**

```XML
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice  name="en-US-Jessa24kRUS">
        <p>
            <s>Introducing the sentence element.</s>
            <s>Used to mark individual sentences.</s>
        </p>
        <p>
            Another simple paragraph.
            Sentence structure in this paragraph is not explicitly marked.
        </p>
    </voice>
</speak>
```

## <a name="use-phonemes-to-improve-pronunciation"></a>使用音素來改善發音

`ph` 元素用於 SSML 檔中的語音發音。 `ph` 元素只能包含文字，沒有其他元素。 一律提供人類可讀的語音做為回復。

語音字母由電話組成，其由字母、數位或字元組成，有時會組合。 每個電話都會說明語音的獨特聲音。 這與拉丁字母相較之下，其中任何字母可能代表多個說話的聲音。 請考慮字母 "c" 在「糖果」和「停止」單字中的不同發音，或在「內容」和「那些」單字中，字母組合「th」的不同發音。

**語法**

```XML
<phoneme alphabet="string" ph="string"></phoneme>
```

**屬性**

| 屬性 | 說明 | 必要/選用 |
|-----------|-------------|---------------------|
| 拉丁字母 | 指定合成 `ph` 屬性中字串的發音時，所要使用的語音字母。 指定字母的字串必須以小寫字母指定。 以下是您可以指定的可能字母。<ul><li>.ipa &ndash; 國際語音字母</li><li>sapi &ndash; 語音 API 電話集合</li><li>通用電話組 &ndash; 的 ups</li></ul>此字母僅適用于元素中的音素。 如需詳細資訊，請參閱[語音字母參考](https://msdn.microsoft.com/library/hh362879(v=office.14).aspx)。 | 選用 |
| 三相 | 包含電話的字串，指定 `phoneme` 元素中的單字發音。 如果指定的字串包含無法辨識的手機，文字轉換語音（TTS）服務會拒絕整個 SSML 檔，而且不會產生任何在檔中指定的語音輸出。 | 如果使用音素，則為必要。 |

**範例**

```XML
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice  name="en-US-Jessa24kRUS">
        <s>His name is Mike <phoneme alphabet="ups" ph="JH AU"> Zhou </phoneme></s>
    </voice>
</speak>
```

```xml
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice  name="en-US-Jessa24kRUS">
        <phoneme alphabet="ipa" ph="t&#x259;mei&#x325;&#x27E;ou&#x325;"> tomato </phoneme>
    </voice>
</speak>
```

## <a name="adjust-prosody"></a>調整韻律

`prosody` 元素用來指定文字到語音轉換輸出的音調、countour、範圍、速率、持續時間和音量的變更。 `prosody` 元素可能包含文字和下列元素： `audio`、`break`、`p`、`phoneme`、`prosody`、`say-as`、`sub`和 `s`。

由於韻律屬性的值可能會隨著寬範圍而有所不同，因此語音辨識器會將指派的值解釋為所選語音的實際韻律值應為的建議。 文字轉換語音服務會限制或替代不支援的值。 不支援值的範例是 1 MHz 或磁片區120。

**語法**

```XML
<prosody pitch="value" contour="value" range="value" rate="value" duration="value" volume="value"></prosody>
```

**屬性**

| 屬性 | 說明 | 必要/選用 |
|-----------|-------------|---------------------|
| 介紹 | 表示文字的基準間距。 您可以用下列方式表達音調：<ul><li>絕對值，以數位表示，後面接著 "Hz" （赫茲）。 例如，600Hz。</li><li>以數位表示的相對值，前面加上 "+" 或 "-"，後面接著 "Hz" 或 "st"，以指定要變更音調的數量。 例如： + 80Hz 或-2st。 "St" 表示變更單位是 semitone，這是標準 diatonic 尺規上的一半色調（半步驟）。</li><li>常數值：<ul><li>x-低</li><li>low</li><li>中</li><li>high</li><li>x-高</li><li>預設</li></ul></li></ul>。 | 選用 |
| 輪廓 | 類神經語音不支援等高線。 [等高線] 代表語音內容在語音輸出中指定時間位置的 [音調] 變更，做為目標陣列。 每個目標都是由一組參數配對所定義。 例如： <br/><br/>`<prosody contour="(0%,+20Hz) (10%,-2st) (40%,+10Hz)">`<br/><br/>每一組參數中的第一個值會指定音調變更的位置，以文字持續時間的百分比表示。 第二個值會使用相對值或音調的列舉值，指定要提高或降低音調的數量（請參閱 `pitch`）。 | 選用 |
| range  | 值，表示文字的音調範圍。 您可以使用相同的絕對值、相對值或用來描述 `pitch`的列舉值來表示 `range`。 | 選用 |
| rate  | 表示文字的說話速率。 您可以將 `rate` 表達為：<ul><li>相對值，以做為預設值之乘數的數位來表示。 例如，值*1*會導致速率不會變更。 值為 *.5*會產生速率的減半。 值為*3*會產生速率的增加三倍。</li><li>常數值：<ul><li>x-慢</li><li>slow</li><li>中</li><li>快速</li><li>x-快速</li><li>預設</li></ul></li></ul> | 選用 |
| duration  | 語音合成（TTS）服務讀取文字（以秒或毫秒為單位）時所經過的時間長度。 例如，2*秒*或*1800ms*。 | 選用 |
| 磁碟區  | 表示說話語音的音量層級。 您可以將磁片區表示為：<ul><li>絕對值，以0.0 到100.0 範圍內的數位表示，從*quietest*到*loudest*。 例如，75。 預設值為100.0。</li><li>以數位表示的相對值，其前面加上 "+" 或 "-"，以指定要變更磁片區的數量。 例如 + 10 或-5.5。</li><li>常數值：<ul><li>無聲</li><li>x-軟</li><li>軟</li><li>中</li><li>很</li><li>x-大聲</li><li>預設</li></ul></li></ul> | 選用 |

### <a name="change-speaking-rate"></a>改變說話速度

說話速率可以在單字或句子層級套用至標準語音。 而說話的速率只能套用至句子層級的類神經語音。

**範例**

```xml
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice  name="en-US-Guy24kRUS">
        <prosody rate="+30.00%">
            Welcome to Microsoft Cognitive Services Text-to-Speech API.
        </prosody>
    </voice>
</speak>
```

### <a name="change-volume"></a>變更音量

磁片區變更可以套用至單字或句子層級的標準語音。 而磁片區變更只能套用至句子層級的類神經語音。

**範例**

```xml
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice  name="en-US-Jessa24kRUS">
        <prosody volume="+20.00%">
            Welcome to Microsoft Cognitive Services Text-to-Speech API.
        </prosody>
    </voice>
</speak>
```

### <a name="change-pitch"></a>變更音高

音調變更可以套用至單字或句子層級的標準語音。 而音調變更只能套用至句子層級的類神經語音。

**範例**

```xml
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice  name="en-US-Guy24kRUS">
        Welcome to <prosody pitch="high">Microsoft Cognitive Services Text-to-Speech API.</prosody>
    </voice>
</speak>
```

### <a name="change-pitch-contour"></a>變更音高結構

> [!IMPORTANT]
> 類神經語音不支援音調輪廓變更。

**範例**

```xml
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice  name="en-US-Jessa24kRUS">
        <prosody contour="(80%,+20%) (90%,+30%)" >
            Good morning.
        </prosody>
    </voice>
</speak>
```
## <a name="say-as-element"></a>假設為元素  

`say-as` 是選擇性的專案，表示元素文字的內容類型（例如數位或日期）。 這會提供語音合成引擎關於如何朗讀文字的指引。 

**語法**

```XML
<say-as interpret-as="string" format="digit string" detail="string"> <say-as>
```

**屬性**

| 屬性 | 說明 | 必要/選用 |
|-----------|-------------|---------------------|
| 解讀為 | 表示元素文字的內容類型。 如需類型清單，請參閱下表。 | 必要項 |
| format | 針對可能有不明確格式的內容類型，提供元素文字精確格式的其他資訊。 SSML 會定義使用它們之內容類型的格式（請參閱下表）。 | 選用 |
| 詳細資料 | 表示要讀出的詳細資料層級。 例如，此屬性可能會要求語音合成引擎發音標點符號。 沒有針對 `detail`定義的標準值。 | 選用 |

<!-- I don't understand the last sentence. Don't we know which one Cortana uses? -->

以下是支援的 `interpret-as` 和 `format` 屬性的內容類型。 只有在 `interpret-as` 設定為 [日期和時間] 時，才包含 [`format`] 屬性。

| 解讀為 | format | 解讀 |
|--------------|--------|----------------|
| address | | 文字會以位址的形式讀出。 語音合成引擎 pronounces：<br /><br />`I'm at <say-as interpret-as="address">150th CT NE, Redmond, WA</say-as>`<br /><br />「我在150th 法院的美國華盛頓州 redmond」。 |
| 基數、數位 | | 文字是以基本數位來讀出。 語音合成引擎 pronounces：<br /><br />`There are <say-as interpret-as="cardinal">3</say-as> alternatives`<br /><br />「有三種替代方案」。 |
| 字元，拼出 | | 文字是以個別字母讀出（拼法）。 語音合成引擎 pronounces：<br /><br />`<say-as interpret-as="characters">test</say-as>`<br /><br />As "T E S T"。 |
| date  | dmy、mdy、ymd、ydm、ym、my、md、dm、d、m、y | 文字會以日期說出。 `format` 屬性會指定日期的格式（*d = day、m = month 和 y = year*）。 語音合成引擎 pronounces：<br /><br />`Today is <say-as interpret-as="date" format="mdy">10-19-2016</say-as>`<br /><br />As 「今天是2016年10月的第十九個」。 |
| 數位、number_digit | | 文字是以一系列的個別數位來讀出。 語音合成引擎 pronounces：<br /><br />`<say-as interpret-as="number_digit">123456789</say-as>`<br /><br />做為 "1 2 3 4 5 6 7 8 9"。 |
| 分數 | | 文字會以小數的形式讀出。 語音合成引擎 pronounces：<br /><br /> `<say-as interpret-as="fraction">3/8</say-as> of an inch`<br /><br />做為「一種八分之的一英寸」。 |
| 序數  | | 文字會以序號的形式讀出。 語音合成引擎 pronounces：<br /><br />`Select the <say-as interpret-as="ordinal">3rd</say-as> option`<br /><br />做為「選取第三個選項」。 |
| telephone  | | 文字會以電話號碼的形式讀出。 `format` 屬性可能包含代表國家/地區代碼的數位。 例如，美國的 "1" 或義大利的 "39"。 語音合成引擎可能會使用這項資訊來引導其電話號碼的發音。 電話號碼也可能包含國家/地區代碼，若是如此，則會優先于 `format`中的國家/地區代碼。 語音合成引擎 pronounces：<br /><br />`The number is <say-as interpret-as="telephone" format="1">(888) 555-1212</say-as>`<br /><br />As 「我的數位是區功能變數代碼 8 8 8 5 5 5 1 2 1 2」。 |
| time | hms12, hms24 | 文字會以一段時間讀出。 `format` 屬性會指定使用12小時制（hms12）或24小時制（hms24）來指定時間。 使用冒號來分隔代表小時、分鐘和秒數的數位。 以下是有效的時間範例：12:35、1:14:32、08:15 和02:50:45。 語音合成引擎 pronounces：<br /><br />`The train departs at <say-as interpret-as="time" format="hms12">4:00am</say-as>`<br /><br />「訓練離開在四個 M」。 |

**使用量**

`say-as` 元素只能包含文字。

**範例**

語音合成引擎會將下列範例當做「您的第一個要求是在10月第十九個 20 10 的一個聊天室，並于 12 35 P M 的初期抵達。」
 
```XML
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice  name="en-US-Jessa24kRUS">
    <p>
    Your <say-as interpret-as="ordinal"> 1st </say-as> request was for <say-as interpret-as="cardinal"> 1 </say-as> room
    on <say-as interpret-as="date" format="mdy"> 10/19/2010 </say-as>, with early arrival at <say-as interpret-as="time" format="hms12"> 12:35pm </say-as>.
    </p>
</speak>
```


## <a name="add-recorded-audio"></a>新增錄製的音訊

`audio` 是選擇性元素，可讓您將 MP3 音訊插入 SSML 檔中。 音訊元素的主體可能包含純文字或 SSML 標記，如果音訊檔無法使用或播放，就會說出來。 此外，`audio` 元素可以包含文字和下列元素： `audio`、`break`、`p`、`s`、`phoneme`、`prosody`、`say-as`和 `sub`。

SSML 檔中包含的任何音訊都必須符合下列需求：

* MP3 必須裝載在可存取網際網路的 HTTPS 端點上。 需要 HTTPS，而且裝載 MP3 檔案的網域必須提供有效、受信任的 SSL 憑證。
* MP3 必須是有效的 MP3 檔案（MPEG v2）。
* 位元速率必須是 48 kbps。
* 取樣速率必須是 16000 Hz。
* 單一回應中所有文字和音訊檔案的總時間總和不能超過90（90）秒。
* MP3 不得包含任何客戶特定或其他機密資訊。

**語法**

```xml
<audio src="string"/></audio>
```

**屬性**

| 屬性 | 說明 | 必要/選用 |
|-----------|-------------|---------------------|
| src | 指定音訊檔案的位置/URL。 | 如果在您的 SSML 檔中使用音訊元素，則為必要專案。 |

**範例**

```xml
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <p>
        <audio src="https://contoso.com/opinionprompt.wav"/>
        Thanks for offering your opinion. Please begin speaking after the beep.
        <audio src="https://contoso.com/beep.wav">
        Could not play the beep, please voice your opinion now. </audio>
    </p>
</speak>
```

## <a name="add-background-audio"></a>新增背景音訊

`mstts:backgroundaudio` 元素可讓您將背景音訊新增至 SSML 檔（或混合具有文字轉換語音的音訊檔案）。 有了 `mstts:backgroundaudio`，您就可以在背景中迴圈音訊檔案、從文字到語音的開頭淡入，然後在文字轉換語音的結尾淡出。

如果提供的背景音訊短于文字轉換語音或淡出，則會迴圈。 如果超過文字轉換語音，則會在淡出完成時停止。

每一份 SSML 檔只能有一個背景音訊檔案。 不過，您可以在 `voice` 元素內散置 `audio` 標記，將其他音訊新增至 SSML 檔。

**語法**

```XML
<mstts:backgroundaudio src="string" volume="string" fadein="string" fadeout="string"/>
```

**屬性**

| 屬性 | 說明 | 必要/選用 |
|-----------|-------------|---------------------|
| src | 指定背景音訊檔案的位置/URL。 | 如果您在 SSML 檔中使用背景音訊，則為必要項。 |
| 磁碟區 | 指定背景音訊檔案的磁片區。 **接受的值**： `0` `100` 內含。 預設值是 `1`。 | 選用 |
| fadein | 指定背景音訊淡入的持續時間（以毫秒為單位）。 預設值為 `0`，這相當於「不淡入」。 **接受的值**： `0` `10000` 內含。  | 選用 |
| fadeout | 指定背景音訊的持續時間（以毫秒為單位）。 預設值為 `0`，相當於 [不淡出]。**接受的值**： `0` `10000` 內含。  | 選用 |

**範例**

```xml
<speak version="1.0" xml:lang="en-US" xmlns:mstts="http://www.w3.org/2001/mstts">
    <mstts:backgroundaudio src="https://contoso.com/sample.wav" volume="0.7" fadein="3000" fadeout="4000"/>
    <voice name="Microsoft Server Speech Text to Speech Voice (en-US, Jessa24kRUS)">
        The text provided in this document will be spoken over the background audio.
    </voice>
</speak>
```

## <a name="next-steps"></a>後續步驟

* [語言支援：語音、地區設定、語言](language-support.md)
