---
title: 安裝適用于 SQL 資料倉儲的 Visual Studio 2019 |Microsoft Docs
description: 安裝適用於 Azure SQL 資料倉儲的 Visual Studio 和 SQL Server Development Tools (SSDT)
services: sql-data-warehouse
ms.custom: vs-azure
ms.workload: azure-vs
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: development
ms.date: 10/17/2019
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: 0ca59080b4056ba74e2f05038e07d647f12de765
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/04/2019
ms.locfileid: "73485054"
---
# <a name="getting-started-with-visual-studio-2019-for-sql-data-warehouse"></a>開始使用適用于 SQL 資料倉儲的 Visual Studio 2019
Visual Studio **2019** SQL SERVER DATA TOOLS （SSDT）是單一工具，可讓您執行下列動作：

- 連接、查詢及開發 SQL 資料倉儲的應用程式 
- 利用 [物件瀏覽器]，以視覺化方式探索資料模型中的所有物件，包括資料表、視圖、預存程式等等。
- 為您的物件產生 T-sql 資料定義語言（DDL）腳本
- 使用以狀態為基礎的方法與 SSDT 資料庫專案來開發您的資料倉儲
- 將您的資料庫專案與原始檔控制系統（例如 Git）整合 Azure Repos
- 使用自動化伺服器設定持續整合和部署管線，例如 Azure DevOps

> [!NOTE]
> 目前 Visual Studio SSDT 資料庫專案處於預覽狀態。 若要定期接收這項功能的更新，請在 [UserVoice] 投票。

## <a name="install-visual-studio-2019-preview"></a>安裝 Visual Studio 2019 Preview
請參閱[下載 Visual Studio 2019][] ，下載並安裝 Visual Studio **16.3 和**更新版本。 在安裝期間，選取資料儲存和處理工作負載。 Visual Studio 2019 中不再需要安裝獨立 SSDT。

## <a name="reporting-issues-with-ssdt-visual-studio-2019-preview"></a>報告 SSDT Visual Studio 2019 的問題（預覽）
若要在使用 SSDT 與 SQL 資料倉儲時回報問題，請傳送電子郵件至下列電子郵件通訊群組清單： <sqldwssdtpreview@service.microsoft.com>

## <a name="next-steps"></a>後續步驟
既然您已有最新版本的 SSDT，[就可以開始連線到您][connect]的 SQL 資料倉儲。

<!--Anchors-->

<!--Image references-->

<!--Articles-->
[connect]: ./sql-data-warehouse-query-visual-studio.md

<!--Other-->
[下載 Visual Studio 2019]: https://visualstudio.microsoft.com/downloads/
[Installing Visual Studio]: https://msdn.microsoft.com/library/e2h7fzkw.aspx
[SSDT Download]: https://msdn.microsoft.com/library/mt204009.aspx
[UserVoice]: https://feedback.azure.com/forums/307516-sql-data-warehouse/suggestions/13313247-database-project-from-visual-studio-to-support-azu
