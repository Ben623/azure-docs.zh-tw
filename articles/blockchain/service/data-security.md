---
title: Azure 區塊鏈服務安全性
description: Azure 區塊鏈服務資料存取和安全性概念
ms.date: 11/22/2019
ms.topic: conceptual
ms.reviewer: janders
ms.openlocfilehash: 06bf4e0fa4037b07505a4f816fc7af56c14576d8
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/15/2020
ms.locfileid: "75982234"
---
# <a name="azure-blockchain-service-security"></a>Azure 區塊鏈服務安全性

Azure 區塊鏈服務使用數個 Azure 功能來讓您的資料維持安全且可用。 我們使用隔離、加密與驗證來保護資料。

## <a name="isolation"></a>隔離

Azure 區塊鏈服務資源會在私人虛擬網路中隔離。 每個交易和驗證節點都是虛擬機器（VM）。 一個虛擬網路中的 Vm 無法直接與不同虛擬網路中的 Vm 通訊。 隔離可確保在虛擬網路內的通訊保持私用。 如需 Azure 虛擬網路隔離的詳細資訊，請參閱[Azure 公用雲端中的隔離](../../security/fundamentals/isolation-choices.md#networking-isolation)。

![VNET 圖表](./media/data-security/vnet.png)

## <a name="encryption"></a>加密

使用者資料會儲存在 Azure 儲存體中。 使用者資料會在行動和待用時加密，以提供安全性和機密性。 如需詳細資訊，請參閱： [Azure 儲存體安全性指南](../../storage/blobs/security-recommendations.md)。

## <a name="authentication"></a>驗證

您可以透過 RPC 端點將交易傳送至區塊鏈節點。 用戶端會使用反向 proxy 伺服器與交易節點進行通訊，以處理使用者驗證，並透過 SSL 加密資料。

![驗證圖表](./media/data-security/authentication.png)

有三種驗證模式可進行 RPC 存取。

### <a name="basic-authentication"></a>基本驗證

基本驗證會使用包含使用者名稱和密碼的 HTTP 驗證標頭。 [使用者名稱] 是區塊鏈節點的名稱。 在布建成員或節點期間設定密碼。 您可以使用 Azure 入口網站或 CLI 來變更密碼。

### <a name="access-keys"></a>存取金鑰

存取金鑰會使用包含在端點 URL 中的隨機產生字串。 兩個存取金鑰有助於啟用金鑰輪替。 您可以從 Azure 入口網站和 CLI 重新產生金鑰。

### <a name="azure-active-directory"></a>Azure Active Directory

Azure Active Directory （Azure AD）使用以宣告為基礎的驗證機制，其使用者是使用 Azure AD 使用者認證 Azure AD 進行驗證。 Azure AD 提供以雲端為基礎的身分識別管理，並可讓客戶在整個企業中使用單一身分識別，並存取雲端上的應用程式。 Azure 區塊鏈 Service 會與 Azure AD 啟用識別碼同盟、單一登入和多重要素驗證整合。 您可以為組織中的使用者、群組和應用程式角色指派區塊鏈成員和節點存取權。

Azure AD 用戶端 proxy 可在[GitHub](https://github.com/Microsoft/azure-blockchain-connector/releases)上取得。 用戶端 proxy 會將使用者引導至 Azure AD 登入頁面，並在驗證成功時取得持有人權杖。 之後，使用者會將乙太坊用戶端應用程式（例如 Geth 或 Truffle）連接到用戶端 proxy 的端點。 最後，提交交易時，用戶端 proxy 會將持有人權杖插入 HTTP 標頭，而反向 proxy 會使用 OAuth 通訊協定來驗證權杖。

## <a name="keys-and-ethereum-accounts"></a>金鑰和乙太坊帳戶

布建 Azure 區塊鏈服務成員時，會產生乙太坊帳戶和公用和私密金鑰組。 私密金鑰是用來將交易傳送至區塊鏈。 乙太坊帳戶是公開金鑰雜湊的最後20個位元組。 乙太坊帳戶也稱為「錢包」。

私用和公開金鑰組會以 JSON 格式儲存為 keyfile。 私密金鑰會使用建立區塊鏈總帳服務時輸入的密碼進行加密。

私密金鑰是用來數位簽署交易。 在私用區塊鏈中，由私用金鑰簽署的智慧合約代表簽署者的身分識別。 若要驗證簽章的有效性，接收者可以將簽署者的公開金鑰與從簽章計算出來的位址進行比較。

Constellation 金鑰可用來唯一識別仲裁節點。 Constellation 索引鍵會在節點布建時產生，並在仲裁的私用交易的 privateFor 參數中指定。

## <a name="next-steps"></a>後續步驟

請參閱[如何設定 Azure 區塊鏈 Service 的 Azure Active Directory 存取](configure-aad.md)。
