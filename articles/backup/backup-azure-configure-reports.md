---
title: 設定 Power BI 報告
description: 使用復原服務保存庫針對 Azure 備份設定 Power BI 報告。
ms.topic: conceptual
ms.date: 07/09/2019
ms.openlocfilehash: 6e8482aea4f1d757550d4490e3a9972f664729c9
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/19/2019
ms.locfileid: "74173196"
---
# <a name="configure-azure-backup-reports"></a>設定 Azure 備份報告

此文章說明使用「復原服務」保存庫針對「Azure 備份」設定報告時，所需依循的步驟。 本文也說明如何使用 Power BI 來存取報告。 完成這些步驟之後，您可以直接移至 Power BI 來檢閱、自訂及建立報告。

> [!IMPORTANT]
> 從 2018 年 11 月 1 日起，部分客戶在 Power BI 的 Azure 備份應用程式中載入資料時可能會出現問題，其中顯示「我們在 JSON 輸入結尾發現額外字元。 此例外狀況是由 IDataReader 介面所引起。」
這是因為資料以變更過的格式載入儲存體帳戶。
請下載最新應用程式 (1.8 版) 以避免此問題。
>
>

## <a name="supported-scenarios"></a>支援的案例

- 藉由使用「Azure 復原服務代理程式」，可針對 Azure 虛擬機器備份及將檔案/資料夾備份到雲端，支援「Azure 備份」報告。
- 目前不支援 Azure SQL Database、Azure 檔案共用、Data Protection Manager 及「Azure 備份」伺服器的報告。
- 如果針對每個保存庫皆設定相同的儲存體帳戶，您便可以跨保存庫和訂用帳戶檢閱報告。 所選儲存體帳戶必須與「復原服務」保存庫位於相同的區域。
- Power BI 中報告的排程重新整理頻率為 24 小時。 您也可以在 Power BI 中執行報表的隨選重新整理。 在此情況下，會使用客戶儲存體帳戶中的最新資料來轉譯報告。

## <a name="prerequisites"></a>先決條件

- 建立 [Azure 儲存體帳戶](../storage/common/storage-quickstart-create-account.md)以針對報告進行設定。 這個儲存體帳戶會用來儲存與報告相關的資料。
- [建立 Power BI 帳戶](https://powerbi.microsoft.com/landing/signin/)，以使用 Power BI 入口網站來檢視、自訂及建立您自己的報告。
- 註冊資源提供者 **Microsoft.insights** (如果尚未註冊的話)。 請使用儲存體帳戶和「復原服務」保存庫的訂用帳戶，以便讓報告資料能夠流向儲存體帳戶。 若要執行此步驟，請前往 Azure 入口網站，選取 [訂用帳戶] > [資源提供者]，然後查看是否有此提供者以註冊它。

## <a name="configure-storage-account-for-reports"></a>針對報告設定儲存體帳戶

請依照下列步驟，使用 Azure 入口網站為「復原服務」保存庫設定儲存體帳戶。 此設定只需進行一次。 設定儲存體帳戶之後，您可以直接移至 Power BI 來查看範本應用程式，並使用報表。

1. 如果您已開啟「復原服務」保存庫，請移至下一個步驟。 如果您並未開啟「復原服務」保存庫，請在 Azure 入口網站中，選取 [所有服務]。

   - 在資源清單中，輸入**復原服務**。
   - 當您開始輸入時，清單會根據您輸入的文字進行篩選。 當您看到 [復原服務保存庫]時，請選取它。
   - 隨即會出現 [復原服務保存庫] 清單。 在 [復原服務保存庫] 清單中選取保存庫。

     選取的保存庫儀表板隨即開啟。
2. 從出現在保存庫底下的項目清單中，選取 [監視與報告] 區段底下的 [備份報告]，以針對報告設定儲存體帳戶。

      ![選取備份報告功能表項目步驟 2](./media/backup-azure-configure-reports/backup-reports-settings.PNG)
3. 在 [備份報告] 刀鋒視窗上，選取 [診斷設定] 連結。 此連結會開啟 [診斷設定] UI，可用來將資料推送至客戶儲存體帳戶。

      ![啟用診斷步驟 3](./media/backup-azure-configure-reports/backup-azure-configure-reports.png)
4. 選取 [開啟診斷] 以開啟要用來設定儲存體帳戶的 UI。

      ![開啟診斷步驟 4](./media/backup-azure-configure-reports/enable-diagnostics.png)
5. 在 [名稱] 方塊中，輸入設定名稱。 選取 [封存至儲存體帳戶] 核取方塊，以便讓報告資料能夠開始流入儲存體帳戶。

      ![啟用診斷步驟 5](./media/backup-azure-configure-reports/select-setting-name.png)
6. 選取 [儲存體帳戶]，從儲存報告資料的清單中選取相關訂用帳戶和儲存體帳戶，然後按一下 [確定]。

      ![選取儲存體帳戶步驟 6](./media/backup-azure-configure-reports/select-subscription-sa.png)
7. 在 [記錄] 區段底下，選取 [AzureBackupReport] 核取方塊。 移動滑桿以選取此報告資料的保留期間。 儲存體帳戶中的報告資料會依據使用此滑桿選取的期間來保留。

      ![儲存儲存體帳戶步驟 7](./media/backup-azure-configure-reports/save-diagnostic-settings.png)
8. 檢閱所有變更，然後選取 [儲存]。 此動作會確保儲存您的所有變更，並且現在已設定妥儲存體帳戶來儲存報告資料。

9. [診斷設定] 資料表現在會顯示針對保存庫啟用的新設定。 如果未顯示，重新整理資料表，即可看見更新的設定。

      ![檢視診斷設定步驟 9](./media/backup-azure-configure-reports/diagnostic-setting-row.png)

> [!NOTE]
> 在您儲存儲存體帳戶來設定報告之後，請「等候 24 小時」，讓初始資料推送完成。 請只在該時間之後，才在 Power BI 中匯入 Azure 備份應用程式。 如需詳細資訊，請參閱[常見問題集](backup-azure-monitor-alert-faq.md)一節。
>
>

## <a name="view-reports-in-power-bi"></a>在 Power BI 中檢視報告

使用「復原服務」保存庫針對報告設定儲存體帳戶之後，大約需要 24 小時的時間，報告資料才會開始流入。 在設定儲存體帳戶的 24 小時後，請依照下列步驟在 Power BI 中檢視報告。
如果要自訂和共用報表，請 建立工作區並執行下列步驟

1. [登入](https://powerbi.microsoft.com/landing/signin/) Power BI。
2. 流覽至 [**應用程式] > 從 Microsoft Appsource 取得更多應用程式**。 依照[說明如何存取內容套件的 Power BI 文件](https://powerbi.microsoft.com/documentation/powerbi-content-packs-services/)中的步驟進行操作。

3. 在 [搜尋] 列中，輸入 **Azure 備份**，然後選取 [立即取得]。

      ![取得範本應用程式](./media/backup-azure-configure-reports/template-app-get.png)
4. 輸入在先前步驟 5 中所設定儲存體帳戶的名稱，然後選取 [下一步]。

    ![輸入儲存體帳戶名稱](./media/backup-azure-configure-reports/content-pack-storage-account-name.png)
5. 使用驗證方法「金鑰」輸入此儲存體帳戶的儲存體帳戶金鑰。 若要[檢視並複製儲存體存取金鑰](../storage/common/storage-account-manage.md#access-keys)，請移至 Azure 入口網站中您的儲存體帳戶。

     ![輸入儲存體帳戶](./media/backup-azure-configure-reports/content-pack-storage-account-key.png) <br/>

6. 選取 [登入]。 登入成功之後，您會看到正在匯入資料通知。

    ![匯入內容套件](./media/backup-azure-configure-reports/content-pack-importing-data.png) <br/>

    匯入完成之後，您會看到「成功」通知。 如果儲存體帳戶中的資料量很大，則匯入範本應用程式可能需要較長的時間。

    ![成功匯入內容套件](./media/backup-azure-configure-reports/content-pack-import-success.png) <br/>

7. 成功匯入資料之後，您可以在流覽窗格的 [**應用程式**] 中看到**Azure 備份**範本應用程式。 在 [儀表板]、[報告] 和 [資料集] 下，清單現在會顯示 Azure 備份。

8. 在 [儀表板] 底下，選取 [Azure 備份]，這會顯示一組已釘選的重要報告。

      ![Azure 備份儀表板](./media/backup-azure-configure-reports/azure-backup-dashboard.png) <br/>
9. 若要檢視完整的一組報告，請在儀表板中選取任何報告。

      ![Azure 備份作業健康情況](./media/backup-azure-configure-reports/azure-backup-job-health.png) <br/>
10. 選取報告中的每個索引標籤，以檢視該方面的報告。

      ![Azure 備份報告索引標籤](./media/backup-azure-configure-reports/reports-tab-view.png)

## <a name="troubleshooting-errors"></a>錯誤疑難排解

| 錯誤詳細資料 | 解決方案 |
| --- | --- |
| 在您為「備份報告」設定儲存體帳戶之後，[儲存體帳戶] 仍然顯示 [未設定]。 | 如果您已成功設定儲存體帳戶，則儘管發生此問題，報告資料仍然會流入。 若要解決此問題，請前往 Azure 入口網站，然後選取 [所有服務] > [診斷設定] > [復原服務保存庫] > [編輯設定]。 請刪除先前所做的設定，然後在同一個刀鋒視窗上建立新的設定。 這次，請在 [名稱] 方塊中選取 [服務]。 此時便會顯示所設定的儲存體帳戶。 |
|在 Power BI 中匯入 Azure 備份範本應用程式之後，就會出現「404-找不到容器」錯誤訊息。 | 如先前所述，在於「復原服務」保存庫中設定報告之後，您必須等候 24 小時，才能在 Power BI 中正確查看報告。 如果您在 24 小時之前嘗試存取報告，便會出現此錯誤訊息，因為還沒有完整的資料可供顯示有效的報告。 |

## <a name="next-steps"></a>後續步驟

在您設定儲存體帳戶並匯入 Azure 備份範本應用程式之後，接下來的步驟是自訂報表，並使用報告資料模型來建立報表。 如需詳細資訊，請參閱下列文章。

- [使用 Azure 備份報告資料模型](backup-azure-reports-data-model.md)
- [在 Power BI 中篩選報告](https://powerbi.microsoft.com/documentation/powerbi-service-about-filters-and-highlighting-in-reports/)
- [在 Power BI 中建立報告](https://powerbi.microsoft.com/documentation/powerbi-service-create-a-new-report/)
