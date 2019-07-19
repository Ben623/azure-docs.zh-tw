---
title: Windows Defender 進階威脅防護與 Azure 資訊安全中心
description: 本文件會介紹 Azure 資訊安全中心與 Windows Defender 進階威脅防護的整合。
services: security-center
documentationcenter: na
author: monhaber
manager: barbkess
editor: ''
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/27/2018
ms.author: v-mohabe
ms.openlocfilehash: 87f5a14bcd6003ad81b663ed97e5349dcbff2a30
ms.sourcegitcommit: a8b638322d494739f7463db4f0ea465496c689c6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/17/2019
ms.locfileid: "68296520"
---
# <a name="windows-defender-advanced-threat-protection-with-azure-security-center"></a>Windows Defender 進階威脅防護與 Azure 資訊安全中心

Azure 資訊安全中心正在透過與 [Windows Defender 進階威脅防護](https://www.microsoft.com/en-us/WindowsForBusiness/windows-atp) (ATP) 進行整合，來擴展其雲端工作負載防護平台的服務。
這項變革可讓端點偵測和回應 (EDR) 功能更臻完善。 進行 Windows Defender ATP 整合後，您可以找出異常狀況。 您也可以偵測及回應 Azure 資訊安全中心監視的伺服器端點上的進階攻擊。

## <a name="windows-defender-atp-features-in-security-center"></a>資訊安全中心中的 Windows Defender ATP 功能

當您使用 Windows Defender ATP 時, 您會取得:

- **新一代後缺口偵測感應器**：Windows 伺服器的 Windows Defender ATP 感應器可收集大量行為訊號陣列。

- **以分析為基礎、以雲端為架構的後缺口偵測功能**：Windows Defender ATP 可快速適應威脅的變化。 並使用進階分析與巨量資料。 Windows Defender ATP 具有 Intelligent Security Graph 的強大功能，可在 Windows、Azure 和 Office 偵測不明威脅的入侵。 它提供採取行動警示，讓您得以快速回應。

- **威脅情報**：Windows Defender ATP 可識別攻擊者的工具、技術和程序。 一旦偵測到就會產生警示。 它會使用由 Microsoft 威脅獵人和安全小組提供的資料，利用合作夥伴提供的情報變得更強大。

這些功能現於 Azure 資訊安全中心提供：

- **自動化上架**：Windows 伺服器在 Azure 資訊安全中心推出時，Windows Defender ATP 感應器會自動啟用。

- **單一窗口**：Azure 資訊安全中心主控台會顯示 Windows Defender ATP 警示。

- **詳細的機器調查**：Azure 資訊安全中心的客戶可以存取 Windows Defender ATP 主控台，以執行詳細的調查，找出缺口的範圍。

![Azure 資訊安全中心會顯示一份警示和每個警示的一般資訊的清單](media/security-center-wdatp/image1.png)

您可以藉由 Windows Defender ATP 樞紐分析進一步調查警示。 您會看到額外的資訊，例如警示流程樹狀圖和事件圖表。 您也可以查看詳細的機器時間軸，其中會顯示最多六個月歷程記錄期間的每項行為。

![Windows Defender ATP 的警示相關詳細資訊頁面](media/security-center-wdatp/image3.png)

## <a name="platform-support"></a>平台支援

資訊安全中心中的 windows Defender ATP 支援在屬於標準服務訂用帳戶的 Windows Server 2012 R2 和 Windows Server 2016 作業系統上進行偵測。

> [!NOTE]
> 當您使用 Azure 資訊安全中心來監視伺服器時, 系統會自動建立 Windows Defender ATP 租使用者, 而且 Windows Defender ATP 資料預設會儲存在歐洲。 如果您需要將資料移至另一個位置, 您必須聯絡 Microsoft 支援服務以重設租使用者。

## <a name="onboarding-servers-to-security-center"></a>讓伺服器在資訊安全中心上線 

若要讓伺服器在資訊安全中心上線，從 Windows Defender ATP 伺服器上線按一下 [移至 Azure 資訊安全中心以讓伺服器上線]  。

1. 在 [上線]  刀鋒視窗中，選取或建立工作區 (即資料儲存位置)。 <br>
2. 如果您看不到所有的工作區，可能是因為權限不足，請確定您的工作區設定為 Azure 安全性標準層。 如需詳細資訊，請參閱[升級為 Azure 資訊安全中心標準層以增強安全性](security-center-pricing.md)。
    
3. 選取 [新增伺服器]  以檢視如何安裝 Microsoft Monitoring Agent 的指示。 

4. 上線之後，您可以在 [計算與應用程式]  底下監視電腦。

   ![上線的電腦](media/security-center-wdatp/onboard-computers.png)

## <a name="enable-windows-defender-atp-integration"></a>啟用 Windows Defender ATP 整合

若要查看是否已啟用 Windows Defender ATP 整合, 請選取 [資訊**安全中心** > **定價 & 設定**] > 按一下您的訂用帳戶。

  ![Azure 資訊安全中心原則管理](media/security-center-wdatp/policy-management.png)

您可以在這裡查看目前已啟用的整合內容。

  ![已啟用 Windows Defender ATP 整合的 Azure 資訊安全中心威脅偵測設定頁面](media/security-center-wdatp/enable-integrations.png)

- 如果您已在 Azure 資訊安全中心標準層推出伺服器，您不必採取進一步的動作。 Azure 資訊安全中心會自動將伺服器上架至 Windows Defender ATP。 這可能需要多達 24 小時的時間。

- 如果您從未將伺服器上架到 Azure 資訊安全中心標準層，則可以照常將伺服器上架到 Azure 資訊安全中心。

- 如果您已透過 Windows Defender ATP 推出伺服器：
  - 參閱文件，了解[如何下架伺服器機器](https://go.microsoft.com/fwlink/p/?linkid=852906)的指南。
  - 將伺服器上架到 Azure 資訊安全中心。

## <a name="access-to-the-windows-defender-atp-portal"></a>存取至 Windows Defender ATP 入口網站

請依照指示[將使用者存取權指派到入口網站](https://docs.microsoft.com/windows/security/threat-protection/windows-defender-atp/assign-portal-access-windows-defender-advanced-threat-protection)。

## <a name="set-the-firewall-configuration"></a>設置防火牆設定

如果您具有封鎖匿名流量的 proxy 或防火牆，Windows Defender ATP 感應器會從系統內容連接，請務必允許匿名流量。 請依照指示[在 proxy 伺服器啟用 Windows Defender ATP 服務網址的存取權](https://docs.microsoft.com/windows/security/threat-protection/windows-defender-atp/configure-proxy-internet-windows-defender-advanced-threat-protection#enable-access-to-microsoft-defender-atp-service-urls-in-the-proxy-server)。

## <a name="test-the-feature"></a>測試功能

若要產生良性 Windows Defender ATP 測試警示：

1. 您可以使用遠端桌面來存取 Windows Server 2012 R2 VM 或 Windows Server 2016 VM。  開啟命令提示字元視窗。

2. 在提示中，複製並執行下列命令： 命令提示字元視窗將會自動關閉。

    ```
    powershell.exe -NoExit -ExecutionPolicy Bypass -WindowStyle Hidden (New-Object System.Net.WebClient).DownloadFile('http://127.0.0.1/1.exe', 'C:\\test-WDATP-test\\invoice.exe'); Start-Process 'C:\\test-WDATP-test\\invoice.exe'
    ```

   ![內含上述命令的命令提示字元視窗](media/security-center-wdatp/image4.jpeg)

3. 如果命令成功執行，您會在 Azure 資訊安全中心儀表板和 Windows Defender ATP 入口網站看到新的警示。 此警示可能需要幾分鐘才會顯示。

4. 若要在資訊安全中心檢閱警示，請前往**安全性警示** >  **可疑的 Powershell 命令列**。

5. 從調查視窗中，選取前往 Windows Defender ATP 入口網站的連結。

## <a name="next-steps"></a>後續步驟

- [在 Azure 資訊安全中心設定安全性原則](tutorial-security-policy.md)：了解如何為您的 Azure 訂用帳戶及資源群組設定安全性原則。
- [管理 Azure 資訊安全中心的安全性建議](security-center-recommendations.md)：了解建議如何協助保護您的 Azure 資源。
- [Azure 資訊安全中心的安全性健全狀況監視](security-center-monitoring.md)：了解如何監視 Azure 資源的健全狀況。
