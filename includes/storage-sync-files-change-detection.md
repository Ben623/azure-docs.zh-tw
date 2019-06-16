---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 10/26/2018
ms.author: tamram
ms.openlocfilehash: beb08c29587e4ce522131142fd61925b5af45fa9
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "66114548"
---
不會立即偵測及複寫使用 Azure 入口網站或 SMB 對 Azure 檔案共用所做的變更，和伺服器端點的變更不一樣。 Azure 檔案還沒有變更通知或日誌功能，因此無法在檔案變更時自動啟動同步工作階段。 在 Windows Server 上，Azure 檔案同步會使用 [Windows USN 日誌](https://msdn.microsoft.com/library/windows/desktop/aa363798.aspx)，在檔案變更時自動啟動同步工作階段。<br /><br /> 若要偵測 Azure 檔案共用的變更，Azure 檔案同步有個已排程的作業，稱為「變更偵測作業」  。 變更偵測作業會列舉檔案共用的每個檔案，然後比較該檔案的同步版本。 當變更偵測作業判斷檔案已有變更時，Azure 檔案同步就會起始同步工作階段。 變更偵測作業會每隔 24 小時起始。 由於變更偵測作業的運作方式是列舉 Azure 檔案共用的每個檔案，大命名空間會比小命名空間耗費更長的時間。 大命名空間判斷哪些檔案已變更所耗費的時間，可能會超過 24 小時 (每隔 24 小時執行一次偵測作業)。<br /><br />
請注意，使用 REST 對 Azure 檔案共用所做的變更，不會更新 SMB 上次修改時間，而且將不會同步顯示為變更。 <br /><br />
我們發現，為 Azure 檔案新增變更偵測，類似於在 Windows 伺服器上磁碟區的 USN 變更偵測。 請至 [Azure 檔案 UserVoice](https://feedback.azure.com/forums/217298-storage/category/180670-files)投票，協助我們決定此功能的進一步開發優先順序。
