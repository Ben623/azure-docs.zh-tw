---
title: PowerShell 範例
description: 尋找部分常見 App Service 案例的 Azure PowerShell 範例。 了解如何將 App Service 部署或管理工作自動化。
tags: azure-service-management
ms.assetid: b48d1137-8c04-46e0-b430-101e07d7e470
ms.topic: sample
ms.date: 03/08/2017
ms.custom: mvc
ms.openlocfilehash: f32db2a4dda0ceb69644b64a5c76155b448c5a28
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/24/2020
ms.locfileid: "74687019"
---
# <a name="powershell-samples-for-azure-app-service"></a>Azure App Service 的 PowerShell 範例

下表包含使用 Azure PowerShell 所建置的 PowerShell 指令碼連結。

| | |
|-|-|
|**建立應用程式**||
| [建立可從 GitHub 部署的應用程式](./scripts/powershell-deploy-github.md?toc=%2fpowershell%2fmodule%2ftoc.json)| 建立可從 GitHub 提取程式碼的 App Service 應用程式。 |
| [建立可從 GitHub 連續部署的應用程式](./scripts/powershell-continuous-deployment-github.md?toc=%2fpowershell%2fmodule%2ftoc.json)| 建立可從 GitHub 持續部署程式碼的 App Service 應用程式。 |
| [建立應用程式並使用 FTP 部署程式碼](./scripts/powershell-deploy-ftp.md?toc=%2fpowershell%2fmodule%2ftoc.json) | 建立 App Service 應用程式，並使用 FTP 從本機目錄上傳檔案。 |
| [建立應用程式並從本機 Git 存放庫部署程式碼](./scripts/powershell-deploy-local-git.md?toc=%2fpowershell%2fmodule%2ftoc.json) | 建立 App Service 應用程式，並從本機 Git 存放庫設定程式碼推送。 |
| [建立應用程式並將程式碼部署至預備環境](./scripts/powershell-deploy-staging-environment.md?toc=%2fpowershell%2fmodule%2ftoc.json) | 建立具有部署位置以用於暫存程式碼變更的 App Service 應用程式。 |
|**設定應用程式**||
| [將自訂網域對應至應用程式](./scripts/powershell-configure-custom-domain.md?toc=%2fpowershell%2fmodule%2ftoc.json)| 建立 App Service 應用程式，並使自訂網域名稱與之對應。 |
| [將自訂 SSL 憑證繫結至應用程式](./scripts/powershell-configure-ssl-certificate.md?toc=%2fpowershell%2fmodule%2ftoc.json)| 建立 App Service 應用程式，並使自訂網域名稱的 SSL 憑證與之繫結。 |
|**調整應用程式**||
| [手動調整應用程式](./scripts/powershell-scale-manual.md?toc=%2fpowershell%2fmodule%2ftoc.json) | 建立 App Service 應用程式，並跨 2 個執行個體調整它。 |
| [透過高可用性架構將應用程式調整為全球可用](./scripts/powershell-scale-high-availability.md?toc=%2fpowershell%2fmodule%2ftoc.json) | 在兩個不同的地理區域中建立兩個 App Service 應用程式，並使用 Azure 流量管理員讓它們可透過單一端點來使用。 |
|**將應用程式連線至資源**||
| [將應用程式連線至 SQL 資料庫](./scripts/powershell-connect-to-sql.md?toc=%2fpowershell%2fmodule%2ftoc.json)| 建立 App Service 應用程式和 SQL Database，然後將資料庫連接字串新增至應用程式設定。 |
| [將應用程式連線至儲存體帳戶](./scripts/powershell-connect-to-storage.md?toc=%2fpowershell%2fmodule%2ftoc.json)| 建立 App Service 應用程式和儲存體帳戶，然後將儲存體連接字串新增至應用程式設定。 |
|**備份與還原應用程式**||
| [備份應用程式](./scripts/powershell-backup-onetime.md?toc=%2fpowershell%2fmodule%2ftoc.json) | 建立 App Service 應用程式，並為其建立一次性備份。 |
| [建立應用程式的排程備份](./scripts/powershell-backup-scheduled.md?toc=%2fpowershell%2fmodule%2ftoc.json) | 建立 App Service 應用程式，並為其建立排定的備份。 |
| [刪除應用程式的備份](./scripts/powershell-backup-delete.md?toc=%2fpowershell%2fmodule%2ftoc.json) | 刪除應用程式的現有備份。 |
| [從備份還原應用程式](./scripts/powershell-backup-restore.md?toc=%2fpowershell%2fmodule%2ftoc.json) | 從先前完成的備份中還原應用程式。 |
| [還原跨多個訂用帳戶的備份](./scripts/powershell-backup-restore-diff-sub.md?toc=%2fpowershell%2fmodule%2ftoc.json) | 從另一個訂用帳戶中的備份還原 Web 應用程式。 |
|**監視應用程式**||
| [使用 Web 伺服器記錄監視應用程式](./scripts/powershell-monitor.md?toc=%2fpowershell%2fmodule%2ftoc.json) | 建立 App Service 應用程式、為其啟用記錄，並將記錄下載到本機電腦。 |
| | |
