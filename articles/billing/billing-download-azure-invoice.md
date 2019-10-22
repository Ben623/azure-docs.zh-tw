---
title: 檢視及下載您 Microsoft Azure 發票
description: 說明如何檢視及下載您的 Microsoft Azure 發票。
keywords: 帳單發票,發票下載,azure 發票,azure 使用量
author: bandersmsft
manager: jureid
tags: billing
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/01/2019
ms.author: banders
ms.openlocfilehash: 80ec40a7411a370460d663084f9f7034b28e1a2e
ms.sourcegitcommit: 0576bcb894031eb9e7ddb919e241e2e3c42f291d
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/15/2019
ms.locfileid: "72375766"
---
# <a name="view-and-download-your-microsoft-azure-invoice"></a>檢視及下載您 Microsoft Azure 發票

針對大多數的訂用帳戶，您可以從 [Azure 入口網站](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)下載您的發票，或透過電子郵件傳送它。 如果您是 Azure Enterprise 合約客戶 (EA 客戶)，您無法下載您的組織發票。 發票會傳送給已設定接收註冊發票的人員。

只有特定角色具有檢視發票的權限。 例如，帳戶管理員或企業系統管理員。 若要深入了解如何取得計費資訊的存取權，請參閱[使用角色管理 Azure 計費的存取權](billing-manage-access.md)。

如果您有 Microsoft 客戶合約 (MCA)，您必須具備下列其中一個角色，才能取得您的發票：

- 帳單設定檔擁有者
- 參與者
- 讀取者
- 發票管理員

如果您有 Microsoft 合作夥伴合約 (MPA)，您必須是夥伴組織中的全域管理員或管理員代理人，才能檢視及下載 Azure 發票。 [檢查您的計費帳戶類型](#check-your-billing-account-type)，以找出您所需的權限。 

<!-- For more information about billing roles for Microsoft Customer Agreements, see [Billing profile roles and tasks](billing-understand-mca-roles.md#billing-profile-roles-and-tasks). -->

## <a name="noinvoice"></a> 為什麼您無法取得發票

您沒有看到發票的可能原因如下︰

- 您的 Azure 訂閱不滿 30 天。

- Azure 會在您的發票期間結束時向您收取費用。 因此，發票可能尚未產生。 等到計費期間結束。

- 您沒有檢視發票的權限。 如果您有 MCA 或 MPA，您必須是帳單設定檔擁有者、參與者、讀者或發票管理員。 對於其他訂用帳戶，如果您不是帳戶管理員，則可能看不到舊發票。 若要深入了解取得計費資訊的存取權，請參閱[使用角色管理 Azure 計費的存取權](billing-manage-access.md)。

- 如果您的訂用帳戶有免費試用或每月點數金額，您只有在超過每月點數金額時才會收到發票。 如果您有 Microsoft 客戶合約或 Microsoft 合作夥伴合約，則一律會收到發票。 

## <a name="download-invoices-in-the-azure-portal"></a>在 Azure 入口網站中下載發票

1. 登入 [Azure 入口網站](https://portal.azure.com)。
1. 搜尋 [成本管理 + 帳單]  。
1. 視存取權之不同，您可能必須選取計費帳戶或帳單設定檔。
1. 在左側功能表中，選取 [計費]  之下的 [發票]  。
1. 在發票方格中，尋找您要下載之發票的資料列。
1. 按一下下載圖示或資料列結尾處的省略符號 (`...`)。
1. 在下載功能表中，選取 [發票]  。

如需發票的詳細資訊，請參閱[了解 Microsoft Azure 帳單](billing-understand-your-bill.md)。 如需管理成本的說明，請參閱[使用 Azure 計費與成本管理避免非預期的成本](billing-getting-started.md)。

## <a name="get-your-subscriptions-invoices-in-email"></a>以電子郵件取得您訂用帳戶的發票

您可以選擇加入並設定其他收件者來透過電子郵件接收您的 Azure 發票。 這項功能可能無法用於支援供應項目、Enterprise 合約或 Azure in Open 等特定訂用帳戶。

1. 在[訂用帳戶頁面](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)上，選取您的訂用帳戶。 針對您擁有的每個訂用帳戶選擇加入。 依序按一下 [發票]  和 [以電子郵件寄送我的發票]  。

    ![顯示選擇加入流程的螢幕擷取畫面](./media/billing-download-azure-invoice-daily-usage-date/InvoicesDeepLink.PNG)

2. 按一下 [選擇加入]  並接受條款。

    ![顯示加入流程步驟 2 的螢幕擷取畫面](./media/billing-download-azure-invoice-daily-usage-date/InvoiceArticleStep2.PNG)

3. 接受合約後，您就可以設定其他收件者。 移除收件者時，不會再儲存電子郵件地址。 如果您改變心意，則必須重新加以新增。

    ![顯示加入流程步驟 3 的螢幕擷取畫面](./media/billing-download-azure-invoice-daily-usage-date/InvoiceArticleStep3.PNG)

如果在依照這些步驟執行之後並未收到電子郵件，請在[設定檔的通訊喜好設定](https://account.windowsazure.com/profile)中確定您的電子郵件地址是正確的。

## <a name="opt-out-of-getting-your-subscriptions-invoices-in-email"></a>選擇不要以電子郵件取得您訂用帳戶的發票

若要選擇不以電子郵件取得發票，請遵循上述步驟，然後按一下 [退出以電子郵件傳送發票]  。 此選項會移除任何已設定為透過電子郵件接收發票的電子郵件地址。 如果您選擇重新加入，則可重新設定收件者。

 ![顯示退出流程的螢幕擷取畫面](./media/billing-download-azure-invoice-daily-usage-date/InvoiceArticleStep4.PNG)

<!-- Does following section apply to MPA too? -->
## <a name="get-your-microsoft-customer-agreement-invoices-in-email"></a>以電子郵件取得 Microsoft 客戶合約的發票

如果您有 Microsoft 客戶合約，您可以選擇要以電子郵件取得您的發票。 所有帳單設定檔擁有者、參與者、讀者和發票管理員都會透過電子郵件取得發票。 讀者無法更新電子郵件發票喜好設定。

1. 登入 [Azure 入口網站](https://portal.azure.com)。
1. 搜尋 [成本管理 + 帳單]  。
1. 選取帳單設定檔。 視存取權之不同，您可能必須先選取計費帳戶。
1. 在 [設定]  下方，選取 [屬性]  。
1. 在 [電子郵件發票]  底下，選取 [更新電子郵件發票喜好設定]  。

    ![顯示電子郵件發票屬性的螢幕擷取畫面](./media/billing-download-azure-invoice/billingprofile-email.png)

1. 選取 [加入]  。
1. 按一下 [更新]  。

## <a name="opt-out-of-getting-your-microsoft-customer-agreement-invoices-in-email"></a>選擇不要以電子郵件取得 Microsoft 客戶合約的發票

若要選擇不以電子郵件取得發票，請遵循上述步驟，然後按一下 [退出]  。所有擁有者、參與者、讀者和發票管理員也都會選擇不透過電子郵件取得發票。 如果您是讀者，則無法變更電子郵件發票喜好設定。

## <a name="check-your-billing-account-type"></a>檢查您的計費帳戶類型
[!INCLUDE [billing-check-account-type](../../includes/billing-check-account-type.md)]

## <a name="need-help-contact-us"></a>需要協助嗎？ 與我們連絡。

如果您有問題或需要協助，請[建立支援要求](https://go.microsoft.com/fwlink/?linkid=2083458)。

## <a name="next-steps"></a>後續步驟

若要深入了解您的發票和費用，請參閱：

- [檢視及下載您的 Microsoft Azure 使用量和費用](billing-download-azure-daily-usage.md)
- [了解 Microsoft Azure 帳單](billing-understand-your-bill.md)
- [了解 Azure 發票上的詞彙](billing-understand-your-invoice.md)
- [了解您 Microsoft Azure 詳細使用量上的字詞](billing-understand-your-usage.md)
- [檢視組織的 Azure 定價](billing-ea-pricing.md)

如果您有 Microsoft 客戶合約，請參閱：

- [了解帳單設定檔發票上的費用](billing-mca-understand-your-bill.md)
- [了解帳單設定檔發票上的詞彙](billing-mca-understand-your-invoice.md)
- [了解帳單設定檔的 Azure 使用量和費用檔案](billing-mca-understand-your-usage.md)
- [檢視及下載帳單設定檔的稅務文件](billing-mca-download-tax-document.md)
- [檢視組織的 Azure 定價](billing-ea-pricing.md)
