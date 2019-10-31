---
title: CAF 移轉登陸區域藍圖範例 - 概觀
description: CAF 移轉登陸區域藍圖範例的概觀和架構。
author: DCtheGeek
ms.author: dacoulte
ms.date: 08/20/2019
ms.topic: sample
ms.service: blueprints
ms.custom: fasttrack-new
ms.openlocfilehash: 28a6bbb21cfd32d21d5d2ffdcaf10faf1a1f8a9f
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/30/2019
ms.locfileid: "73163544"
---
# <a name="overview-of-the-microsoft-cloud-adoption-framework-for-azure-migration-landing-zone-blueprint-sample"></a>適用於 Azure 的 Microsoft 雲端採用架構，移轉登陸區域藍圖範例的概觀

適用於 Azure 的 Microsoft 雲端採用架構 (CAF) 移轉登陸區域藍圖是一組基礎結構，可協助您設定第一個工作負載的移轉，以及管理您的雲端資產，使其與 CAF 一致。

[CAF 基礎](../caf-foundation/index.md)藍圖範例會延續此範例。

## <a name="architecture"></a>架構

CAF 移轉登陸區域藍圖範例會在 Azure 中部署基礎結構資源，供組織用來準備其訂用帳戶，以便將虛擬機器遷移至該處。 其也有助於放置管理其雲端資產所需的治理控制項。 此範例會部署和強制執行資源、原則和範本，讓組織能夠安心地開始使用 Azure。

![CAF 移轉登陸區域，影像說明第一個登陸區域的 CAF 指引中所安裝的內容 ](../../media/caf-blueprints/caf-migration-landing-zone-architecture.png)

此環境包含數個 Azure 服務，用來提供完全受到監視的企業級安全治理。 此環境包含：

- [Azure Key Vault](../../../../key-vault/key-vault-overview.md) 執行個體，用來裝載共用服務環境中已部署憑證、金鑰和秘密所使用的祕密
- 部署 [Log Analytics](../../../../azure-monitor/overview.md)，以確保當您開始進行安全部署時，所有動作和服務皆會記錄到中央位置
- 部署 [Azure資訊安全中心](../../../../security-center/security-center-intro.md) (標準版)，為您遷移的工作負載提供威脅防護。
- 部署 [Azure 虛擬網路](../../../../virtual-network/virtual-networks-overview.md)，為您的虛擬機器提供隔離的網路和子網路。
- 部署 [Azure Migrate 專案](../../../..//migrate/migrate-overview.md)以進行探索和評估。 我們正在新增伺服器評估、伺服器移轉、資料庫評估和資料庫移轉的工具。  


所有這些項目皆遵循 [Azure 架構中心 - 參考架構](/azure/architecture/reference-architectures/)中所發佈且經過實證的做法。

> [!NOTE]
> CAF 移轉藍圖會為您的工作負載配置登陸區域。 您仍然需要在此基本架構上執行虛擬機器/資料庫的評估和遷移。

如需詳細資訊，請參閱[適用於 Azure 的 Microsoft 雲端採用架構 - 移轉](/azure/architecture/cloud-adoption/migrate/)。

## <a name="next-steps"></a>後續步驟

您已檢閱了 CAF 移轉登陸區域藍圖範例的概觀和架構。

> [!div class="nextstepaction"]
>  [CAF 移轉登陸區域藍圖 - 部署步驟](./deploy.md)

有關藍圖及其使用方式的其他文件：

- 了解[藍圖生命週期](../../concepts/lifecycle.md)。
- 了解如何使用[靜態與動態參數](../../concepts/parameters.md)。
- 了解如何自訂[藍圖排序順序](../../concepts/sequencing-order.md)。
- 了解如何使用[藍圖資源鎖定](../../concepts/resource-locking.md)。
- 了解如何[更新現有的指派](../../how-to/update-existing-assignments.md)。