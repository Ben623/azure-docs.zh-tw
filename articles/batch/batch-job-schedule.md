---
title: 排程您的作業
description: 使用作業排程來管理您的工作。
services: batch
author: LauraBrenner
manager: evansma
editor: ''
ms.assetid: 63d9d4f1-8521-4bbb-b95a-c4cad73692d3
ms.service: batch
ms.topic: article
ms.workload: big-compute
ms.date: 02/20/2020
ms.author: labrenne
ms.custom: seodec18
ms.openlocfilehash: 55ea8fb4cc0e65deaa89d718c4a46513716dcf54
ms.sourcegitcommit: bc792d0525d83f00d2329bea054ac45b2495315d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/06/2020
ms.locfileid: "78672429"
---
# <a name="schedule-jobs-for-efficiency"></a>排定作業的效率

排程批次作業可讓您優先處理您想要先執行的作業，同時將相依于其他工作的工作納入考慮。 藉由排程您的作業，您可以確定您使用的資源數量最少。 節點可以在不需要時解除委任，相依于其他工作的工作則會即時啟動以優化工作流程。 一次只會執行一項作業。 新的版本將不會啟動，直到前一個完成為止。 您可以將作業設定為 [自動完成]。 

## <a name="benefit-of-job-scheduling"></a>作業排程的優點

排程工作的優點是您可以指定建立作業的排程。使用作業管理員工作排程的工作會與作業相關聯。 作業管理員工作將會建立作業的工作。 若要這麼做，作業管理員工作必須使用 Batch 帳戶進行驗證。 使用 AZ_BATCH_AUTHENTICATION_TOKEN 存取權杖。 權杖將允許存取其餘的作業。 

## <a name="use-the-portal-to-schedule-a-job"></a>使用入口網站來排程作業

   1. 登入 [Azure 入口網站](https://portal.azure.com/)。

   2. 選取您想要在其中排程作業的 Batch 帳戶。

   3. 選取 **[新增]** 以建立新的作業排程，並完成 [**基本] 表單**。



![排程作業][1]

**作業排程 ID**：此作業排程的唯一識別碼。

**顯示名稱**：作業的顯示名稱不一定是唯一的，但長度上限為1024個字元。

**不要執行到**：指定工作將執行的最早時間。 如果您未設定，則排程會準備好立即執行工作。

**不要在之後**執行：在您于此處設定的時間之後，不會執行任何工作。 如果您未指定時間，則會建立週期性的作業排程，在您明確地將其終止之前，會保持作用中狀態。

**週期間隔**：您可以指定工作之間的時間量。 您一次只能有一個作業，因此如果是在作業排程下建立新作業的時間，但先前的作業仍在執行中，則批次服務將不會建立新的作業，直到先前的工作完成為止。  

**開始視窗**：您可以在此指定時間間隔，從排程表示應建立作業的時間開始，直到應該完成為止。 如果目前的工作未在其視窗中完成，則下一個作業將無法啟動。

在 [基本] 表單的底部，您將指定要在其上執行作業的集區。 若要尋找您的集區識別碼資訊，請選取 [**更新**]。 

![指定集區][2]


**集區識別碼**：識別您將在其上執行作業的集區。

**作業**設定工作：如果您使用，請選取 [**更新**] 來命名作業管理員工作，以及作業準備和發行工作。

**優先順序**：為作業提供優先順序。

**最大時鐘時間**：設定作業可執行檔最大時間量。 如果它未在時間範圍內完成，Batch 會終止作業。 如果您未設定此值，作業就不會有時間限制。

**最大工作重試次數**：指定工作可以重試最多四次的次數。 這與 API 呼叫可能會有的重試次數不同。

**當所有工作都完成時**：預設為 [無動作]。

**當工作失敗時**：預設為 [無動作]。 如果重試計數已耗盡或啟動工作時發生錯誤，工作就會失敗。 

選取 [**儲存**] 之後，如果您移至左側導覽中的 [**作業**排程]，您可以選取 [**執行資訊**] 來追蹤作業的執行。


## <a name="for-more-information"></a>取得詳細資訊

若要使用 Azure CLI 來管理作業，請參閱[az batch job-schedule](https://docs.microsoft.com/cli/azure/batch/job-schedule?view=azure-cli-latest)。

## <a name="next-steps"></a>後續步驟

建立工作相依性[，以執行](batch-task-dependencies.md)相依于其他工作的工作。





[1]: ./media/batch-job-schedule/add_job_schedule-02.png
[2]: ./media/batch-job-schedule/add_job_schedule-03.png


