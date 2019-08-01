---
title: 共同作業時的安全性-LUIS
titleSuffix: Azure Cognitive Services
description: 撰寫存取權適用於擁有者和共同作業者。 就私人應用程式而言，端點存取權適用於擁有者和共同作業者。
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 06/12/2019
ms.author: diberry
ms.openlocfilehash: 25f6b675846f028b4ba9d49cce9e075bc79eece9
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/26/2019
ms.locfileid: "68560669"
---
# <a name="authoring-and-endpoint-user-access"></a>撰寫和端點的使用者存取權
撰寫存取權適用於擁有者和共同作業者。 就私人應用程式而言，端點存取權適用於擁有者和共同作業者。 對於公用應用程式, 擁有自己的 Azure[認知服務](../cognitive-services-apis-create-account.md)或[LUIS](luis-how-to-azure-subscription.md#create-prediction-endpoint-runtime-resource-in-the-azure-portal)資源的所有人都可以使用端點存取, 而且具有公用應用程式的識別碼。 

## <a name="access-to-authoring"></a>存取撰寫
從 [LUIS](luis-reference-regions.md#luis-website) 網站或[撰寫 API](https://go.microsoft.com/fwlink/?linkid=2092087) 存取應用程式時，會受到應用程式擁有者所控制。 

擁有者和所有共同作業者都具備可撰寫應用程式的存取權。 

|撰寫存取權包括|注意|
|--|--|
|新增或移除端點金鑰||
|匯出版本||
|匯出端點記錄||
|匯入版本||
|將應用程式設定為公用|當應用程式為公用時，任何具有撰寫或端點金鑰的使用者都可查詢該應用程式。|
|修改模型|
|發行|
|檢閱用於[主動式學習](luis-how-to-review-endpoint-utterances.md)的端點語句|
|定型|

## <a name="access-to-endpoint"></a>存取端點

查詢端點的存取權是由 [應用程式資訊]  頁面的 [管理]  區段中的設定所控制。 

![將應用程式設定為公用](./media/luis-concept-security/set-application-as-public.png)

|[私人端點](#private-app-endpoint-security)|[公用端點](#public-app-endpoint-access)|
|:--|:--|
|可供擁有者和共同作業者使用|可供擁有者、共同作業者，以及任何知道應用程式識別碼的人員使用|

### <a name="private-app-endpoint-security"></a>私人應用程式端點安全性

私人應用程式的端點僅供下列金鑰使用：

|金鑰和使用者|說明|
|--|--|
|擁有者的撰寫金鑰| 最多 1000 次端點叫用|
|共同作業者的撰寫金鑰| 最多 1000 次端點叫用|
|由作者或共同作業者指派給 LUIS 的任何金鑰|以金鑰使用量為基礎|

#### <a name="microsoft-user-accounts"></a>Microsoft 使用者帳戶

作者和共同作業者可以將金鑰指派給私人 LUIS 應用程式。 在 Azure 入口網站中建立 LUIS 金鑰的 Microsoft 使用者帳戶，必須是應用程式擁有者或應用程式共同作業者。 您無法從另一個 Azure 帳戶將金鑰指派給私人應用程式。

若要深入了解 Active Directory 使用者帳戶，請參閱 [Azure Active Directory 租用戶使用者](luis-how-to-collaborate.md#azure-active-directory-tenant-user)。 

### <a name="public-app-endpoint-access"></a>公用應用程式端點存取權

將應用程式設定為公用之後，「任何」  有效的 LUIS 撰寫金鑰或 LUIS 端點金鑰都能查詢您的應用程式，只要該金鑰尚未使用整個端點配額即可。

使用者如果不是擁有者或共同作業者，在取得應用程式識別碼的情況下，只能存取公用應用程式。 LUIS 沒有公用「市集」  或其他方式可供搜尋公用應用程式。  

公用應用程式會在所有區域中發佈，以便具有區域型 LUIS 資源金鑰的使用者在與資源金鑰相關聯的區域中存取應用程式。

## <a name="microsoft-user-accounts"></a>Microsoft 使用者帳戶

作者和共同作業者可以在 [Publish] \(發佈\) 頁面上將金鑰新增至 LUIS。 在 Azure 入口網站中建立 LUIS 金鑰的 Microsoft 使用者帳戶，必須是應用程式擁有者或應用程式共同作業者。 

若要深入了解 Active Directory 使用者帳戶，請參閱 [Azure Active Directory 租用戶使用者](luis-how-to-collaborate.md#azure-active-directory-tenant-user)。 

## <a name="securing-the-endpoint"></a>保護端點 

您可以在伺服器對伺服器環境中呼叫 LUIS 端點，來控管可以看見 LUIS 端點金鑰的使用者。 如果您是從 Bot 使用 LUIS，則 Bot 與 LUIS 之間的連線已經是安全連線。 如果您要直接呼叫 LUIS 端點，則應該建立一個具有受控存取權 (例如 [AAD](https://azure.microsoft.com/services/active-directory/)) 的伺服器端 API (例如 Azure [函數](https://azure.microsoft.com/services/functions/))。 呼叫伺服器端 API 並確認驗證和授權之後，請將呼叫繼續傳遞給 LUIS。 雖然此策略並無法防止攔截式攻擊，但可向您的使用者混淆端點、讓您能夠追蹤存取，以及讓您新增端點回應記錄功能 (例如[Application Insights](https://azure.microsoft.com/services/application-insights/))。  

## <a name="security-compliance"></a>安全性合規性
 
[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-security-compliance.md)]

## <a name="next-steps"></a>後續步驟

請參閱[最佳做法](luis-concept-best-practices.md)，以了解如何使用意圖和圖示來獲得最佳預測。
