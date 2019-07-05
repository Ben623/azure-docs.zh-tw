---
title: 設定 Linux Java 應用程式-Azure App Service |Microsoft Docs
description: 了解如何設定在 Linux 上 Azure App Service 中執行的 Java 應用程式。
keywords: azure 應用程式服務、 web 應用程式，linux，oss、 java、 java ee jee，javaee
services: app-service
author: bmitchell287
manager: douge
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: java
ms.topic: article
ms.date: 06/26/2019
ms.author: brendm
ms.custom: seodec18
ms.openlocfilehash: 51ca597208b582e95fd305886dcf163744825eee
ms.sourcegitcommit: 79496a96e8bd064e951004d474f05e26bada6fa0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2019
ms.locfileid: "67509657"
---
# <a name="configure-a-linux-java-app-for-azure-app-service"></a>設定適用於 Azure App Service 的 Linux Java 應用程式

Linux 上的 Azure App Service 可讓 Java 開發人員在全受控 Linux 服務上，快速建置、部署和調整其 Tomcat 或 Java Standard Edition (SE) 封裝的 Web 應用程式。 從命令列或編輯器 (例如 IntelliJ、Eclipse 或 Visual Studio Code)，使用 Maven 外掛程式部署應用程式。

本指南提供重要概念和 Java 開發人員使用內建的 Linux 容器，App Service 中的指示。 如果您從未使用過 Azure App Service，請遵循[Java 快速入門](quickstart-java.md)並[PostgreSQL 教學課程的 Java](tutorial-java-enterprise-postgresql-app.md)第一次。

## <a name="deploying-your-app"></a>部署應用程式

您可以使用[適用於 Azure App Service 的 Maven 外掛程式](/java/api/overview/azure/maven/azure-webapp-maven-plugin/readme)將.jar 和.war 檔案部署。 與熱門的 Ide 部署也支援[適用於 IntelliJ 的 Azure 工具組](/java/azure/intellij/azure-toolkit-for-intellij)或是[適用於 Eclipse 的 Azure 工具組](/java/azure/eclipse/azure-toolkit-for-eclipse)。

否則，您的部署方法將取決於您的封存類型：

- 若要將 .war 檔案部署至 Tomcat，請使用 `/api/wardeploy/` 端點透過 POST 張貼您的封存檔案。 如需有關此 API 的詳細資訊，請參閱[這份文件](https://docs.microsoft.com/azure/app-service/deploy-zip#deploy-war-file)。
- 若要部署 Java SE 映像上的 .jar 檔案，請使用 Kudu 網站的 `/api/zipdeploy/` 端點。 如需有關此 API 的詳細資訊，請參閱[這份文件](https://docs.microsoft.com/azure/app-service/deploy-zip#rest)。

請勿使用 FTP 來部署 .war 或 .jar。 FTP 工具是為上傳啟動指令碼、相依性或其他執行階段檔案而設計的。 它不是部署網頁的最佳選擇。

## <a name="logging-and-debugging-apps"></a>記錄和偵錯應用程式

透過 Azure 入口網站，可以取得每個應用程式的效能報表、流量視覺化和健康狀態檢查。 如需詳細資訊，請參閱 < [Azure App Service 診斷概觀](../overview-diagnostics.md)。

### <a name="ssh-console-access"></a>SSH 主控台存取

[!INCLUDE [Open SSH session in browser](../../../includes/app-service-web-ssh-connect-builtin-no-h.md)]

### <a name="stream-diagnostic-logs"></a>資料流診斷記錄

[!INCLUDE [Access diagnostic logs](../../../includes/app-service-web-logs-access-no-h.md)]

如需詳細資訊，請參閱[使用 Azure CLI 串流處理記錄](../troubleshoot-diagnostic-logs.md#streaming-with-azure-cli)。

### <a name="app-logging"></a>應用程式記錄

透過 Azure 入口網站或 [Azure CLI](/cli/azure/webapp/log#az-webapp-log-config) 啟用[應用程式記錄](../troubleshoot-diagnostic-logs.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#enablediag)，設定 App Service 將應用程式的標準主控台輸出和標準主控台錯誤資料流寫入至本機檔案系統或 Azure Blob 儲存體。 設定後的 12 個小時會停用記錄至本機 App Service 檔案系統執行個體。 如果您需要較長的保留期，則請設定應用程式將輸出寫入至 Blob 儲存體容器。 Java 和 Tomcat 應用程式記錄檔位於 */home/LogFiles/應用程式/* 目錄。

如果您的應用程式使用 [Logback](https://logback.qos.ch/) 或 [Log4j](https://logging.apache.org/log4j) 追蹤，則您可以使用[在 Application Insights 中探索 Java 追蹤記錄](/azure/application-insights/app-insights-java-trace-logs)中的記錄架構設定指示，將這些要檢閱的追蹤轉送至 Azure Application Insights。

### <a name="troubleshooting-tools"></a>疑難排解工具

內建的 Java 映像為基礎[Alpine Linux](https://alpine-linux.readthedocs.io/en/latest/getting_started.html)作業系統。 使用`apk`套件管理員安裝的疑難排解工具或命令。

### <a name="flight-recorder"></a>飛行記錄器

App Service 上的所有 Linux Java 映像都已安裝，因此您可以輕鬆地連接至 JVM 並開始錄製程式碼剖析工具或產生的堆積傾印的 Zulu 飛行記錄器。

#### <a name="timed-recording"></a>逾時的記錄

若要取得啟動，透過 ssh 連線到您的 App Service，並執行`jcmd`命令，以查看執行的所有 Java 處理序的清單。 除了 jcmd 本身，您應該會看到與處理程序識別碼 (pid) 所執行的 Java 應用程式。

```shell
078990bbcd11:/home# jcmd
Picked up JAVA_TOOL_OPTIONS: -Djava.net.preferIPv4Stack=true
147 sun.tools.jcmd.JCmd
116 /home/site/wwwroot/app.jar
```

執行下列命令來啟動 JVM 的 30 秒錄製。 這將會在 JVM 程式碼剖析，並建立名為 JFR 檔案*jfr_example.jfr*主目錄中。 （請以您的 Java 應用程式的 pid 來取代 116。）

```shell
jcmd 116 JFR.start name=MyRecording settings=profile duration=30s filename="/home/jfr_example.jfr"
```

在 30 秒的間隔內，您可以驗證記錄正在進行執行`jcmd 116 JFR.check`。 這會顯示針對給定的 Java 程序的所有記錄。

#### <a name="continuous-recording"></a>連續的記錄

您可以使用 Zulu 飛行記錄器持續分析您的 Java 應用程式，執行階段效能的影響降到最低 ([來源](https://assets.azul.com/files/Zulu-Mission-Control-data-sheet-31-Mar-19.pdf))。 若要這樣做，請執行下列 Azure CLI 命令建立名為必要的設定 JAVA_OPTS 應用程式設定。 JAVA_OPTS 應用程式設定的內容會傳遞至`java`命令啟動您的應用程式時。

```azurecli
az webapp config appsettings set -g <your_resource_group> -n <your_app_name> --settings JAVA_OPTS=-XX:StartFlightRecording=disk=true,name=continuous_recording,dumponexit=true,maxsize=1024m,maxage=1d
```

如需詳細資訊，請參閱[Jcmd 命令參考](https://docs.oracle.com/javacomponents/jmc-5-5/jfr-runtime-guide/comline.htm#JFRRT190)。

### <a name="analyzing-recordings"></a>分析記錄

使用[FTPS](../deploy-ftp.md) JFR 檔案下載到本機電腦。 若要分析 JFR 檔案，下載並安裝[Zulu 的任務控制](https://www.azul.com/products/zulu-mission-control/)。 Zulu 任務控制項上的指示，請參閱[Azul 文件](https://docs.azul.com/zmc/)並[安裝指示](https://docs.microsoft.com/java/azure/jdk/java-jdk-flight-recorder-and-mission-control)。

## <a name="customization-and-tuning"></a>自訂和調整

適用於 Linux 的 azure App Service 支援的微調方塊和透過 Azure 入口網站和 CLI 進行自訂。 請檢閱以下文章中的非特定 Java web 應用程式組態：

- [設定應用程式設定](../configure-common.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#configure-app-settings)
- [設定自訂網域](../app-service-web-tutorial-custom-domain.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)
- [啟用 SSL](../app-service-web-tutorial-custom-ssl.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)
- [新增 CDN](../../cdn/cdn-add-to-web-app.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)
- [設定 Kudu 網站](https://github.com/projectkudu/kudu/wiki/Configurable-settings#linux-on-app-service-settings)

### <a name="set-java-runtime-options"></a>設定 Java 執行階段選項

若要設定配置的記憶體或其他 JVM 執行階段選項 Tomcat 和 Java SE 環境中，建立[應用程式設定](../configure-common.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#configure-app-settings)名為`JAVA_OPTS`的選項。 App Service Linux 會在啟動時將此設定當成環境變數傳遞至 Java 執行階段。

在 Azure 入口網站中，於 Web 應用程式的 [應用程式設定]  下，建立名為 `JAVA_OPTS` 且包含其他設定的新應用程式設定 (例如 `-Xms512m -Xmx1204m`)。

若要設定 Maven 外掛程式中的應用程式設定，設定/值標記的區段中新增 Azure 外掛程式。 下列範例會設定特定最小和最大 Java 堆積大小：

```xml
<appSettings>
    <property>
        <name>JAVA_OPTS</name>
        <value>-Xms512m -Xmx1204m</value>
    </property>
</appSettings>
```

執行具有其 App Service 方案中某個部署位置的單一應用程式開發人員，可以使用下列選項：

- B1 及 S1 執行個體： `-Xms1024m -Xmx1024m`
- B2 和 S2 執行個體： `-Xms3072m -Xmx3072m`
- B3 和 S3 的執行個體： `-Xms6144m -Xmx6144m`

調整應用程式堆積設定時，請檢閱 App Service 方案詳細資料，並考慮多個應用程式和部署位置需求以尋找最佳的記憶體配置。

如果您要部署的 JAR 應用程式，其應該為*app.jar* ，讓內建的映像可以正確地識別您的應用程式。 （Maven 外掛程式執行此重新命名自動）。如果您不想重新命名以 JAR *app.jar*，您可以上傳的命令執行 JAR 的殼層指令碼。 貼在此指令碼的完整路徑[啟動檔案](https://docs.microsoft.com/azure/app-service/containers/app-service-linux-faq#startup-file)入口網站的組態中的文字方塊。

### <a name="turn-on-web-sockets"></a>開啟 Web 通訊端

在應用程式的 [應用程式設定]  中，開啟 Azure 入口網站中的 Web 通訊端支援。 您必須重新啟動應用程式，設定才會生效。

搭配使用 Azure CLI 與下列命令，以開啟 Web 通訊端支援：

```azurecli-interactive
az webapp config set --name <app-name> --resource-group <resource-group-name> --web-sockets-enabled true
```

然後重新啟動您的應用程式：

```azurecli-interactive
az webapp stop --name <app-name> --resource-group <resource-group-name>
az webapp start --name <app-name> --resource-group <resource-group-name>
```

### <a name="set-default-character-encoding"></a>設定預設字元編碼

在 Azure 入口網站中，於 Web 應用程式的 [應用程式設定]  下，建立名為 `JAVA_OPTS` 且值為 `-Dfile.encoding=UTF-8` 的新應用程式設定。

或者，您可以使用 App Service Maven 外掛程式來設定應用程式設定。 在外掛程式設定中新增設定名稱和值標籤：

```xml
<appSettings>
    <property>
        <name>JAVA_OPTS</name>
        <value>-Dfile.encoding=UTF-8</value>
    </property>
</appSettings>
```

### <a name="adjust-startup-timeout"></a>調整啟動逾時

如果您的 Java 應用程式特別大時，您應該增加啟動時間限制。 若要這樣做，請建立 應用程式設定，`WEBSITES_CONTAINER_START_TIME_LIMIT`並將它設定為應用程式服務應該逾時前等候的秒數。最大值是`1800`秒。

## <a name="secure-applications"></a>保護應用程式

在 App Service for Linux 中執行之 Java 應用程式會有一組與其他應用程式相同的[安全性最佳做法](/azure/security/security-paas-applications-using-app-services)。

### <a name="authenticate-users"></a>驗證使用者

設定使用 Azure 入口網站中的應用程式驗證**驗證和授權**選項。 在這裡，您可以使用 Azure Active Directory 或社交登入 (例如 Facebook、Google 或 GitHub) 來啟用驗證。 只有在設定單一驗證提供者時，Azure 入口網站設定才會運作。 如需詳細資訊，請參閱[設定 App Service 應用程式使用 Azure Active Directory 登入](../configure-authentication-provider-aad.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)，以及其他身分識別提供者的相關文章。 如果您需要啟用多個登入提供者，請遵循[自訂 App Service 驗證](../app-service-authentication-how-to.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)一文中的指示。

#### <a name="tomcat"></a>Tomcat

Tomcat 應用程式存取使用者的宣告直接來自轉換之主體的 Tomcat servlet 物件複製到 Map 物件。 Map 物件會對應至該類型的宣告集合的每個宣告類型。 在下面的程式碼`request`的執行個體`HttpServletRequest`。

```java
Map<String, Collection<String>> map = (Map<String, Collection<String>>) request.getUserPrincipal();
```

現在您可以檢查`Map`任何特定的宣告的物件。 比方說，下列程式碼片段會逐一查看所有宣告類型，並會列印每個集合的內容。

```java
for (Object key : map.keySet()) {
        Object value = map.get(key);
        if (value != null && value instanceof Collection {
            Collection claims = (Collection) value;
            for (Object claim : claims) {
                System.out.println(claims);
            }
        }
    }
```

若要登出使用者，並執行其他動作，請參閱文件[App Service 驗證與授權使用量](https://docs.microsoft.com/azure/app-service/app-service-authentication-how-to)。 另外還有在 Tomcat 上的官方文件[HttpServletRequest 介面](https://tomcat.apache.org/tomcat-5.5-doc/servletapi/javax/servlet/http/HttpServletRequest.html)和其方法。 下列 servlet 也會提供方法，會根據您的應用程式服務組態：

```java
public boolean isSecure()
public String getRemoteAddr()
public String getRemoteHost()
public String getScheme()
public int getServerPort()
```

若要停用這項功能，建立名為應用程式設定`WEBSITE_AUTH_SKIP_PRINCIPAL`值為`1`。 若要停用所有 App Service 所加入的 servlet 篩選器，建立名為的設定`WEBSITE_SKIP_FILTERS`值為`1`。

#### <a name="spring-boot"></a>Spring Boot

Spring Boot 開發人員可以使用 [Azure Active Directory Spring Boot 簡易版](/java/azure/spring-framework/configure-spring-boot-starter-java-app-with-azure-active-directory?view=azure-java-stable)，以利用熟悉的 Spring Security 註釋和 API 來保護應用程式。 若要增加最大標頭大小，以確定您*application.properties*檔案。 建議值為 `16384`。

### <a name="configure-tlsssl"></a>設定 TLS/SSL

請遵循[繫結現有自訂 SSL 憑證](../app-service-web-tutorial-custom-ssl.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)中的指示，上傳現有的 SSL 憑證，並將其繫結至您應用程式的網域名稱。 根據預設，您的應用程式仍然允許 HTTP 連線；請遵循教學課程中的特定步驟，以強制執行 SSL 和 TLS。

### <a name="use-keyvault-references"></a>使用金鑰保存庫參考

[Azure 金鑰保存庫](../../key-vault/key-vault-overview.md)提供集中式的祕密管理存取原則和稽核歷程記錄。 您可以將機密資料 （例如密碼或連接字串） 儲存在金鑰保存庫，並在您的應用程式，透過環境變數存取這些機密資料。

首先，遵循[您的應用程式存取權授與 Key Vault](../app-service-key-vault-references.md#granting-your-app-access-to-key-vault)並[讓您的祕密的金鑰保存庫參考中為應用程式設定](../app-service-key-vault-references.md#reference-syntax)。 您可以驗證參考解析設為祕密的列印環境變數時從遠端存取應用程式服務的終端機。

若要插入這些 Spring 或 Tomcat 組態檔中的祕密，使用環境變數的資料隱碼語法 (`${MY_ENV_VAR}`)。 Spring 組態檔，請參閱此文件[外部化組態](https://docs.spring.io/spring-boot/docs/current/reference/html/boot-features-external-config.html)。

## <a name="configure-apm-platforms"></a>設定 APM 平台

本節說明如何連接使用 NewRelic 和 AppDynamics 的應用程式效能監視 (APM) 平台部署在 Linux 上的 Azure App Service 上的 Java 應用程式。

[設定 New Relic](#configure-new-relic)
[設定 AppDynamics](#configure-appdynamics)

### <a name="configure-new-relic"></a>設定 NewRelic

1. 在 [NewRelic.com](https://newrelic.com/signup) 建立 NewRelic 帳戶
2. 從 NewRelic 下載 Java 代理程式，所以會有類似的檔名*newrelic-java-x.x.x.zip*。
3. 複製您的授權金鑰，以供稍後設定代理程式時使用。
4. [透過 ssh 連線到您的 App Service 執行個體](app-service-linux-ssh-support.md)，並建立新的目錄 */home/site/wwwroot/apm*。
5. 打開包裝後的 NewRelic Java 代理程式檔案上傳至之下的目錄 */home/site/wwwroot/apm*。 您的代理程式的檔案應位於 */home/site/wwwroot/apm/newrelic*。
6. 修改在 YAML 檔案 */home/site/wwwroot/apm/newrelic/newrelic.yml*並以您自己的授權金鑰取代預留位置的授權值。
7. 在 Azure 入口網站中，瀏覽至您在 App Service 中的應用程式，並建立新的應用程式設定。
    - 如果您的應用程式使用 **Java SE**，請使用 `-javaagent:/home/site/wwwroot/apm/newrelic/newrelic.jar` 值建立名為 `JAVA_OPTS` 的環境變數。
    - 如果您使用 **Tomcat**，請使用 `-javaagent:/home/site/wwwroot/apm/newrelic/newrelic.jar` 值建立名為 `CATALINA_OPTS` 的環境變數。
    - 如果您使用**WildFly**，請參閱 New Relic 的文件[這裡](https://docs.newrelic.com/docs/agents/java-agent/additional-installation/wildfly-version-11-installation-java)安裝 Java 代理程式和 JBoss 設定的相關指引。
    - 如果您已經有 `JAVA_OPTS` 或 `CATALINA_OPTS` 的環境變數，請將 `javaagent` 選項附加至目前值的結尾。

### <a name="configure-appdynamics"></a>設定 AppDynamics

1. 在 [AppDynamics.com](https://www.appdynamics.com/community/register/) 建立 AppDynamics 帳戶
2. AppDynamics 網站下載 Java 代理程式、 檔案名稱將會類似於*AppServerAgent x.x.x.xxxxx.zip*
3. [透過 ssh 連線到您的 App Service 執行個體](app-service-linux-ssh-support.md)，並建立新的目錄 */home/site/wwwroot/apm*。
4. 將 Java 代理程式檔案上傳到下的目錄 */home/site/wwwroot/apm*。 您的代理程式的檔案應位於 */home/site/wwwroot/apm/appdynamics*。
5. 在 Azure 入口網站中，瀏覽至您在 App Service 中的應用程式，並建立新的應用程式設定。
    - 如果您使用 **Java SE**，請使用 `-javaagent:/home/site/wwwroot/apm/appdynamics/javaagent.jar -Dappdynamics.agent.applicationName=<app-name>` 值建立名為 `JAVA_OPTS` 的環境變數，其中 `<app-name>` 是您的 App Service 名稱。
    - 如果您使用 **Tomcat**，請使用 `-javaagent:/home/site/wwwroot/apm/appdynamics/javaagent.jar -Dappdynamics.agent.applicationName=<app-name>` 值建立名為 `CATALINA_OPTS` 的環境變數，其中 `<app-name>` 是您的 App Service 名稱。
    - 如果您使用**WildFly**，請參閱 AppDynamics 文件[這裡](https://docs.appdynamics.com/display/PRO45/JBoss+and+Wildfly+Startup+Settings)安裝 Java 代理程式和 JBoss 設定的相關指引。

## <a name="configure-jar-applications"></a>將 JAR 應用程式設定

### <a name="starting-jar-apps"></a>啟動 JAR 應用程式

根據預設，App Service 會預期命名為應用程式 JAR *app.jar*。 如果有此名稱，它會自動執行。 對於 Maven 的使用者，您可以設定的 JAR 名稱包括`<finalName>app</finalName>`中`<build>`一節您*pom.xml*。 [您可以執行相同的 Gradle](https://docs.gradle.org/current/dsl/org.gradle.api.tasks.bundling.Jar.html#org.gradle.api.tasks.bundling.Jar:archiveFileName)藉由設定`archiveFileName`屬性。

如果您想要使用不同的 JAR 名稱，您也必須提供[啟動命令](app-service-linux-faq.md#built-in-images)執行 JAR 檔案。 例如： `java -jar my-jar-app.jar` 。 您可以為您的啟動命令，在入口網站中，[設定] 下設定的值 > 一般設定，或使用應用程式設定名為`STARTUP_COMMAND`。

### <a name="server-port"></a>伺服器連接埠

App Service Linux 要求路由傳送連入連接埠 80，讓您的應用程式應該接聽連接埠 80。 您可以在您的應用程式設定 (例如 Spring 的*application.properties*檔案)，或在啟動命令 (例如`java -jar spring-app.jar --server.port=80`)。 請參閱下列文件的一般 Java 架構：

- [Spring Boot](https://docs.spring.io/spring-boot/docs/current/reference/html/howto-properties-and-configuration.html#howto-use-short-command-line-arguments)
- [SparkJava](http://sparkjava.com/documentation#embedded-web-server)
- [Micronaut](https://docs.micronaut.io/latest/guide/index.html#runningSpecificPort)
- [播放架構](https://www.playframework.com/documentation/2.6.x/ConfiguringHttps#Configuring-HTTPS)
- [Vertx](https://vertx.io/docs/vertx-core/java/#_start_the_server_listening)
- [Quarkus](https://quarkus.io/guides/application-configuration-guide)

## <a name="data-sources"></a>資料來源

### <a name="tomcat"></a>Tomcat

這些指示適用於所有資料庫連線。 您將必須在預留位置中填入您所選資料庫的驅動程式類別名稱和 JAR 檔案。 下表提供常見資料庫的類別名稱和驅動程式下載。

| 資料庫   | 驅動程式類別名稱                             | JDBC 驅動程式                                                                      |
|------------|-----------------------------------------------|------------------------------------------------------------------------------------------|
| PostgreSQL | `org.postgresql.Driver`                        | [下載](https://jdbc.postgresql.org/download.html)                                    |
| MySQL      | `com.mysql.jdbc.Driver`                        | [下載](https://dev.mysql.com/downloads/connector/j/) (請選取 [Platform Independent] \(不受平台影響\)) |
| SQL Server | `com.microsoft.sqlserver.jdbc.SQLServerDriver` | [下載](https://docs.microsoft.com/sql/connect/jdbc/download-microsoft-jdbc-driver-for-sql-server?view=sql-server-2017#available-downloads-of-jdbc-driver-for-sql-server)                                                           |

若要設定 Tomcat，以使用 Java Database Connectivity (JDBC) 或 Java 持續性 API (JPA)，先自訂`CATALINA_OPTS`會讀取在 Tomcat 在啟動時的環境變數。 請透過 [App Service Maven 外掛程式](https://github.com/Microsoft/azure-maven-plugins/blob/develop/azure-webapp-maven-plugin/README.md)中的應用程式設定來設定這些值：

```xml
<appSettings>
    <property>
        <name>CATALINA_OPTS</name>
        <value>"$CATALINA_OPTS -Ddbuser=${DBUSER} -Ddbpassword=${DBPASSWORD} -DconnURL=${CONNURL}"</value>
    </property>
</appSettings>
```

或中設定環境變數**組態** > **應用程式設定**在 Azure 入口網站中的頁面。

接著，決定資料來源應僅供在 Tomcat Servlet 上執行的一個應用程式還是所有應用程式使用。

#### <a name="application-level-data-sources"></a>應用程式層級的資料來源

1. 建立*context.xml*中的檔案*中繼 INF /* 專案的目錄。 建立*中繼 INF /* 如果不存在的目錄。

2. 在  *context.xml*，新增`Context`JNDI 位址連結資料來源的項目。 以上表中您驅動程式的類別名稱取代 `driverClassName` 預留位置。

    ```xml
    <Context>
        <Resource
            name="jdbc/dbconnection"
            type="javax.sql.DataSource"
            url="${dbuser}"
            driverClassName="<insert your driver class name>"
            username="${dbpassword}"
            password="${connURL}"
        />
    </Context>
    ```

3. 更新您的應用程式*web.xml*使用您的應用程式中的資料來源。

    ```xml
    <resource-env-ref>
        <resource-env-ref-name>jdbc/dbconnection</resource-env-ref-name>
        <resource-env-ref-type>javax.sql.DataSource</resource-env-ref-type>
    </resource-env-ref>
    ```

#### <a name="shared-server-level-resources"></a>伺服器層級的共用的資源

1. 複製的內容 */usr/local/tomcat/conf*成 */home/tomcat/conf*在您的 App Service Linux 執行個體上使用 SSH，如果您還沒有設定那里。

    ```bash
    mkdir -p /home/tomcat
    cp -a /usr/local/tomcat/conf /home/tomcat/conf
    ```

2. 加入內容項目，在您*server.xml*內`<Server>`項目。

    ```xml
    <Server>
    ...
    <Context>
        <Resource
            name="jdbc/dbconnection"
            type="javax.sql.DataSource"
            url="${dbuser}"
            driverClassName="<insert your driver class name>"
            username="${dbpassword}"
            password="${connURL}"
        />
    </Context>
    ...
    </Server>
    ```

3. 更新您的應用程式*web.xml*使用您的應用程式中的資料來源。

    ```xml
    <resource-env-ref>
        <resource-env-ref-name>jdbc/dbconnection</resource-env-ref-name>
        <resource-env-ref-type>javax.sql.DataSource</resource-env-ref-type>
    </resource-env-ref>
    ```

#### <a name="finalize-configuration"></a>完成設定

最後，將驅動程式 Jar 放置在 Tomcat classpath，然後重新啟動您的 App Service。

1. 請確定的 JDBC 驅動程式檔案都可 Tomcat classloader 放 */home/tomcat/lib*目錄。 (如果此目錄尚未存在，請建立此目錄)。若要將這些檔案上傳至 App Service 執行個體，請執行下列步驟：

    1. 在  [Cloud Shell](https://shell.azure.com)，安裝 webapp 擴充功能：

      ```azurecli-interactive
      az extension add -–name webapp
      ```

    2. 執行下列 CLI 命令，從您的本機系統建立 SSH 通道，至 App Service:

      ```azurecli-interactive
      az webapp remote-connection create --resource-group <resource-group-name> --name <app-name> --port <port-on-local-machine>
      ```

    3. 連接到與您的 SFTP 用戶端通道的本機連接埠，並上傳檔案 */home/tomcat/lib*資料夾。

    或著，您也可以使用 FTP 用戶端來上傳 JDBC 驅動程式。 請依照這些[指示來取得您的 FTP 認證](../deploy-configure-credentials.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json) \(英文\)。

2. 如果您已建立伺服器層級的資料來源，請重新啟動 App Service Linux 應用程式。 Tomcat 會將 `CATALINA_HOME` 重設為 `/home/tomcat/conf`，並使用已更新的設定。

### <a name="spring-boot"></a>Spring Boot

若要連接至 Spring Boot 應用程式中的資料來源，我們建議建立的連接字串，並插入至您*application.properties*檔案。

1. 在 [應用程式服務] 頁面的 [設定] 區段中，設定字串的名稱、 將您的 JDBC 連接字串貼到 [值] 欄位中，以及設定為 「 自訂 」 型別。 您可以選擇設定此連接字串，做為位置設定。

    此連接字串是可存取我們的應用程式作為環境變數，名為`CUSTOMCONNSTR_<your-string-name>`。 例如，前面所建立的連接字串將會命名為`CUSTOMCONNSTR_exampledb`。

2. 在您*application.properties*檔案中，參考環境變數名稱與此連接字串。 我們的範例中，我們會使用下列程式碼。

    ```yml
    app.datasource.url=${CUSTOMCONNSTR_exampledb}
    ```

請參閱[Spring Boot 文件上的資料存取](https://docs.spring.io/spring-boot/docs/current/reference/html/howto-data-access.html)並[外部化組態](https://docs.spring.io/spring-boot/docs/current/reference/html/boot-features-external-config.html)如需有關本主題。

## <a name="configure-java-ee-wildfly"></a>設定 Java EE (WildFly)

> [!NOTE]
> 在 App Service Linux 上的 Java Enterprise Edition 目前為預覽狀態。 此堆疊**不**建議用於生產環境對向工作。 在我們的 Java SE 和 Tomcat 堆疊上的資訊。

Linux 上的 azure App Service 可讓 Java 開發人員建置、 部署及調整 Java Enterprise (Java EE) 應用程式上完全受控的以 Linux 為基礎服務。  基礎的企業 Java 執行階段環境是開放原始碼[WildFly](https://wildfly.org/)應用程式伺服器。

本節包含下列小節：

- [使用 App Service 的小數位數](#scale-with-app-service)
- [自訂應用程式伺服器設定](#customize-application-server-configuration)
- [安裝模組相依性](#install-modules-and-dependencies)
- [設定資料來源](#configure-data-sources)
- [啟用訊息的提供者](#enable-messaging-providers)
- [設定工作階段管理的快取](#configure-session-management-caching)

### <a name="scale-with-app-service"></a>使用 App Service 調整

在 Linux 上的 App Service 中執行的 WildFly 應用程式伺服器會以獨立模式執行，而不會在網域設定中執行。 當您相應放大 App Service 方案時，每個 WildFly 執行個體會設定為獨立伺服器。

使用[調整規模規則](../../monitoring-and-diagnostics/monitoring-autoscale-get-started.md)以及藉由[增加執行個體計數](../web-sites-scale.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)，以垂直或水平方式調整您的應用程式。

### <a name="customize-application-server-configuration"></a>自訂應用程式伺服器設定

Web 應用程式執行個體是無狀態的因此在啟動以支援應用程式所需的 WildFly 設定必須設定每個新的執行個體已啟動。
您可以撰寫啟動 Bash 指令碼來呼叫 WildFly CLI，以便：

- 設定資料來源
- 設定傳訊提供者
- 加入 WildFly 伺服器組態的其他模組和相依性。

當 WildFly 已啟動並執行，但應用程式啟動前，就會執行指令碼。 使用指令碼應該[JBOSS CLI](https://docs.jboss.org/author/display/WFLY/Command+Line+Interface)從呼叫 */opt/jboss/wildfly/bin/jboss-cli.sh*以任何設定或變更伺服器啟動之後，需要設定應用程式伺服器。

請勿設定 WildFly 使用 CLI 互動模式。 您可以改用 `--file` 命令來為 JBoss CLI 提供命令的指令碼，例如：

```bash
/opt/jboss/wildfly/bin/jboss-cli.sh -c --file=/path/to/your/jboss_commands.cli
```

使用 FTP 將啟動指令碼中您的 App Service 執行個體底下的位置上傳您 */home*目錄，例如 */home/site/deployments/tools*。 如需詳細資訊，請參閱 <<c0> [ 將您的應用程式部署至 Azure App Service 中使用 FTP/S](https://docs.microsoft.com/azure/app-service/deploy-ftp)。

設定**啟動指令碼**欄位，是在 Azure 入口網站中啟動殼層指令碼的位置，例如 */home/site/deployments/tools/your-startup-script.sh*。

提供[應用程式設定](../configure-common.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#configure-app-settings)應用程式組態，將指令碼中使用的環境變數中。 應用程式設定會保留設定應用程式脫離版本控制所需的連接字串和其他祕密。

### <a name="install-modules-and-dependencies"></a>安裝模組相依性

若要安裝在 JBoss CLI 透過 WildFly classpath 模組及其相依性，您必須在自己的目錄中建立下列檔案。 部分模組和相依性可能需要其他設定 (例如 JNDI 命名或其他 API 特有的設定)，因此，這份清單是您將需要在大多數情況下設定相依性之項目的基本組合。

- [XML 模組描述元](https://jboss-modules.github.io/jboss-modules/manual/#descriptors) \(英文\)。 這個 XML 檔案會定義模組的名稱、屬性和相依性。 這個[範例 module.xml 檔案](https://access.redhat.com/documentation/en-us/jboss_enterprise_application_platform/6/html/administration_and_configuration_guide/example_postgresql_xa_datasource) \(英文\) 會定義 Postgres 模組、它的 JAR 檔案 JDBC 相依性，以及其他所需的模組相依性。
- 所有適用於您模組的必要 JAR 檔案相依性。
- 含有您 JBoss CLI 命令的指令碼，可用來設定新的模組。 這個檔案所包含的命令將透過 JBoss CLI 執行，以設定伺服器來使用相依性。 如需用來新增模組、資料來源和傳訊提供者的相關文件，請參閱[這份文件](https://access.redhat.com/documentation/red_hat_jboss_enterprise_application_platform/7.0/html-single/management_cli_guide/#how_to_cli) \(英文\)。
- Bash 啟動指令碼，會在上一個步驟中呼叫 JBoss CLI 並執行此指令碼。 重新啟動您的 App Service 執行個體時，或在向外延展期間佈建新的執行個體時，將執行這個檔案。這個啟動指令碼是在將 JBoss 命令傳遞給 JBoss CLI 時，您可針對應用程式執行任何其他設定的地方。 至少，這個檔案可以是用來將您的 JBoss CLI 命令指令碼傳遞至 JBoss CLI 的單一命令：

```bash
/opt/jboss/wildfly/bin/jboss-cli.sh -c --file=/path/to/your/jboss_commands.cli
```

之後的檔案和內容的模組，請遵循下列步驟來將模組新增到 WildFly 應用程式伺服器。

1. 使用 FTP 將檔案上傳到位置，以在您的 App Service 執行個體，在您 */home*目錄，例如 */home/site/deployments/tools*。 如需詳細資訊，請參閱 <<c0> [ 將您的應用程式部署至 Azure App Service 中使用 FTP/S](../deploy-ftp.md)。
2. 在**組態** > **一般設定**頁面上的 Azure 入口網站中，設定**啟動指令碼**欄位來啟動殼層指令碼的位置範例 */home/site/deployments/tools/startup.sh*。
3. 重新啟動您的 App Service 執行個體，藉由按下**重新啟動**按鈕**概觀**入口網站或使用 Azure CLI 的區段。

### <a name="configure-data-sources"></a>設定資料來源

若要設定 WildFly/JBoss 存取資料來源，您可以使用上述 < 安裝模組和相依性 > 一節所述的一般程序。 下一節提供此程序 PostgreSQL、 MySQL 和 SQL Server 資料來源的特定詳細資料。

本節假設您已經有應用程式，App Service 執行個體和 Azure 資料庫服務執行個體。 下面的指示，請參閱您的應用程式服務名稱、 其資源群組和資料庫連線資訊。 您可以找到這項資訊在 Azure 入口網站。

如果您想要瀏覽整個程序中，從一開始使用範例應用程式，請參閱[教學課程：建置 Java EE 與 Postgres web 應用程式在 Azure 中的](tutorial-java-enterprise-postgresql-app.md)。

下列步驟說明連線您現有的 App Service 和資料庫的需求。

1. 下載的 JDBC driver for [PostgreSQL](https://jdbc.postgresql.org/download.html)， [MySQL](https://dev.mysql.com/downloads/connector/j/)，或[SQL Server](https://docs.microsoft.com/sql/connect/jdbc/download-microsoft-jdbc-driver-for-sql-server)。 解壓縮下載的封存，以取得.jar 檔案的驅動程式。

2. 建立檔案的名稱，例如*module.xml*並新增下列標記。 取代`<module name>`預留位置 （包括括弧） 取代`org.postgres`適用於 PostgreSQL`com.mysql`適用於 MySQL，或`com.microsoft`適用於 SQL Server。 取代`<JDBC .jar file path>`.jar 檔案從上一個步驟的名稱，包括位置的完整路徑的檔案中放置您的 App Service 執行個體。 這可以是任何位置底下 */home*目錄。

    ```xml
    <?xml version="1.0" ?>
    <module xmlns="urn:jboss:module:1.1" name="<module name>">
        <resources>
           <resource-root path="<JDBC .jar file path>" />
        </resources>
        <dependencies>
            <module name="javax.api"/>
            <module name="javax.transaction.api"/>
        </dependencies>
    </module>
    ```

3. 建立檔案的名稱，例如*datasource commands.cli*並加入下列程式碼。 取代`<JDBC .jar file path>`與您在上一個步驟中使用的值。 取代`<module file path>`使用檔案名稱和應用程式服務的路徑，從上一個步驟中，例如 */home/module.xml*。

    **PostgreSQL**

    ```console
    module add --name=org.postgres --resources=<JDBC .jar file path> --module-xml=<module file path>

    /subsystem=datasources/jdbc-driver=postgres:add(driver-name=postgres,driver-module-name=org.postgres,driver-class-name=org.postgresql.Driver,driver-xa-datasource-class-name=org.postgresql.xa.PGXADataSource)

    data-source add --name=postgresDS --driver-name=postgres --jndi-name=java:jboss/datasources/postgresDS --connection-url=$DATABASE_CONNECTION_URL --user-name=$DATABASE_SERVER_ADMIN_FULL_NAME --password=$DATABASE_SERVER_ADMIN_PASSWORD --use-ccm=true --max-pool-size=5 --blocking-timeout-wait-millis=5000 --enabled=true --driver-class=org.postgresql.Driver --exception-sorter-class-name=org.jboss.jca.adapters.jdbc.extensions.postgres.PostgreSQLExceptionSorter --jta=true --use-java-context=true --valid-connection-checker-class-name=org.jboss.jca.adapters.jdbc.extensions.postgres.PostgreSQLValidConnectionChecker

    reload --use-current-server-config=true
    ```

    **MySQL**

    ```console
    module add --name=com.mysql --resources=<JDBC .jar file path> --module-xml=<module file path>

    /subsystem=datasources/jdbc-driver=mysql:add(driver-name=mysql,driver-module-name=com.mysql,driver-class-name=com.mysql.cj.jdbc.Driver)

    data-source add --name=mysqlDS --jndi-name=java:jboss/datasources/mysqlDS --connection-url=$DATABASE_CONNECTION_URL --driver-name=mysql --user-name=$DATABASE_SERVER_ADMIN_FULL_NAME --password=$DATABASE_SERVER_ADMIN_PASSWORD --use-ccm=true --max-pool-size=5 --blocking-timeout-wait-millis=5000 --enabled=true --driver-class=com.mysql.cj.jdbc.Driver --jta=true --use-java-context=true --exception-sorter-class-name=com.mysql.cj.jdbc.integration.jboss.ExtendedMysqlExceptionSorter

    reload --use-current-server-config=true
    ```

    **SQL Server**

    ```console
    module add --name=com.microsoft --resources=<JDBC .jar file path> --module-xml=<module file path>

    /subsystem=datasources/jdbc-driver=sqlserver:add(driver-name=sqlserver,driver-module-name=com.microsoft,driver-class-name=com.microsoft.sqlserver.jdbc.SQLServerDriver,driver-datasource-class-name=com.microsoft.sqlserver.jdbc.SQLServerDataSource)

    data-source add --name=sqlDS --jndi-name=java:jboss/datasources/sqlDS --driver-name=sqlserver --connection-url=$DATABASE_CONNECTION_URL --validate-on-match=true --background-validation=false --valid-connection-checker-class-name=org.jboss.jca.adapters.jdbc.extensions.mssql.MSSQLValidConnectionChecker --exception-sorter-class-name=org.jboss.jca.adapters.jdbc.extensions.mssql.MSSQLExceptionSorter

    reload --use-current-server-config=true
    ```

    這個檔案是由下一個步驟中所述的啟動指令碼執行。 它為 WildFly 模組安裝 JDBC 驅動程式、 建立對應的 WildFly 資料來源，並重新載入伺服器，以確保所做的變更才會生效。

4. 建立檔案的名稱，例如*startup.sh*並加入下列程式碼。 取代`<JBoss CLI script>`與您在上一個步驟中建立檔案的名稱。 請務必包含完整的路徑位置您會將檔案放在您的 App Service 執行個體，例如 */home/datasource-commands.cli*。

    ```bash
    #!/usr/bin/env bash
    /opt/jboss/wildfly/bin/jboss-cli.sh -c --file=<JBoss CLI script>
    ```

5. 利用 FTP 來上傳至您的 App Service 執行個體的 JDBC.jar 檔案、 模組 XML 檔案、 JBoss CLI 指令碼和啟動指令碼。 將這些檔案放在所指定的位置在上一個步驟中，這類 */home*。 如需 FTP 的詳細資訊，請參閱[將您的應用程式部署至 Azure App Service 中使用 FTP/S](https://docs.microsoft.com/azure/app-service/deploy-ftp)。

6. 您可以使用 Azure CLI 來將設定新增至您的 App Service，保存您的資料庫連接資訊。 取代`<resource group>`和`<webapp name>`您的 App Service 所使用的值。 取代`<database server name>`， `<database name>`， `<admin name>`，和`<admin password>`與您的資料庫連接資訊。 您可以從 Azure 入口網站取得您的 App Service 和資料庫資訊。

    **PostgreSQL:**

    ```bash
    az webapp config appsettings set \
        --resource-group <resource group> \
        --name <webapp name> \
        --settings \
            DATABASE_CONNECTION_URL=jdbc:postgresql://<database server name>:5432/<database name>?ssl=true \
            DATABASE_SERVER_ADMIN_FULL_NAME=<admin name> \
            DATABASE_SERVER_ADMIN_PASSWORD=<admin password>
    ```

    **MySQL:**

    ```bash
    az webapp config appsettings set \
        --resource-group <resource group> \
        --name <webapp name> \
        --settings \
            DATABASE_CONNECTION_URL=jdbc:mysql://<database server name>:3306/<database name>?ssl=true\&useLegacyDatetimeCode=false\&serverTimezone=GMT \
            DATABASE_SERVER_ADMIN_FULL_NAME=<admin name> \
            DATABASE_SERVER_ADMIN_PASSWORD=<admin password>
    ```

    **SQL Server:**

    ```bash
    az webapp config appsettings set \
        --resource-group <resource group> \
        --name <webapp name> \
        --settings \
            DATABASE_CONNECTION_URL=jdbc:sqlserver://<database server name>:1433;database=<database name>;user=<admin name>;password=<admin password>;encrypt=true;trustServerCertificate=false;hostNameInCertificate=*.database.windows.net;loginTimeout=30;
    ```

    DATABASE_CONNECTION_URL 值是不同的每個資料庫伺服器，並在 Azure 入口網站的值不同。 這裡 （以及在上述程式碼片段） 所顯示的 URL 格式是由 WildFly 才能使用：

    * **PostgreSQL:** `jdbc:postgresql://<database server name>:5432/<database name>?ssl=true`
    * **MySQL:** `jdbc:mysql://<database server name>:3306/<database name>?ssl=true\&useLegacyDatetimeCode=false\&serverTimezone=GMT`
    * **SQL Server:** `jdbc:sqlserver://<database server name>:1433;database=<database name>;user=<admin name>;password=<admin password>;encrypt=true;trustServerCertificate=false;hostNameInCertificate=*.database.windows.net;loginTimeout=30;`

7. 在 Azure 入口網站中，瀏覽至您的 App Service，並尋找**組態** > **一般設定**頁面。 設定**啟動指令碼**欄位的名稱和位置的啟動指令碼，例如 */home/startup.sh*。

下次重新啟動您的 App Service，它會執行啟動指令碼，並執行必要的設定步驟。 若要測試此組態正確地進行，您可以存取您使用 SSH 的 App Service，然後執行啟動指令碼自行從 Bash 提示字元。 您也可以檢查應用程式服務記錄檔。 如需這些選項的詳細資訊，請參閱[記錄和偵錯應用程式](#logging-and-debugging-apps)。

接下來，您必須更新您的應用程式的 WildFly 組態並重新部署它。 請使用下列步驟：

1. 開啟*src/main/resources/META-INF/persistence.xml*檔案，您的應用程式，並找出`<jta-data-source>`項目。 取代其內容，如下所示：

    **PostgreSQL**

    ```xml
    <jta-data-source>java:jboss/datasources/postgresDS</jta-data-source>
    ```

    **MySQL**

    ```xml
    <jta-data-source>java:jboss/datasources/mysqlDS</jta-data-source>
    ```

    **SQL Server**

    ```xml
    <jta-data-source>java:jboss/datasources/postgresDS</jta-data-source>
    ```

2. 重建並重新部署您的應用程式在 Bash 提示字元使用下列命令：

    ```bash
    mvn package -DskipTests azure-webapp:deploy
    ```

3. 重新啟動您的 App Service 執行個體，藉由按下**重新啟動**按鈕**概觀**一節在 Azure 入口網站或使用 Azure CLI。

您的 App Service 執行個體現在已設定為存取您的資料庫。

如需使用 WildFly 設定資料庫連線的詳細資訊，請參閱[PostgreSQL](https://developer.jboss.org/blogs/amartin-blog/2012/02/08/how-to-set-up-a-postgresql-jdbc-driver-on-jboss-7)， [MySQL](https://docs.jboss.org/jbossas/docs/Installation_And_Getting_Started_Guide/5/html/Using_other_Databases.html#Using_other_Databases-Using_MySQL_as_the_Default_DataSource)，或[SQL Server](https://docs.jboss.org/jbossas/docs/Installation_And_Getting_Started_Guide/5/html/Using_other_Databases.html#d0e3898)。

### <a name="enable-messaging-providers"></a>啟用訊息的提供者

若要使用服務匯流排啟用訊息導向的 Bean 作為傳訊機制：

1. 使用 [Apache QPId JMS 傳訊程式庫](https://qpid.apache.org/proton/index.html) \(英文\)。 針對應用程式，在您的 pom.xml (或其他組建檔案) 中包含此相依性。

2. 建立[服務匯流排資源](/azure/service-bus-messaging/service-bus-java-how-to-use-jms-api-amqp)。 建立 Azure 服務匯流排命名空間及該命名空間內的佇列，以及具有傳送和接收功能的共用存取原則。

3. 藉由針對您的原則主要金鑰進行 URL 編碼或[使用服務匯流排 SDK](/azure/service-bus-messaging/service-bus-java-how-to-use-jms-api-amqp#setup-jndi-context-and-configure-the-connectionfactory)，將共用存取原則金鑰傳遞至您的程式碼。

4. 透過適用於 JMS 提供者的模組 XML 描述元、.jar 相依性、JBoss CLI 命令及啟動指令碼，來遵循＜安裝模組和相依性＞一節中概述的步驟。 除了這四個檔案，您也必須建立一個 XML 檔案，來定義 JMS 佇列和主題的 JNDI 名稱。 如需參考設定檔，請參閱[這個存放庫](https://github.com/JasonFreeberg/widlfly-server-configs/tree/master/appconfig)。

### <a name="configure-session-management-caching"></a>設定工作階段管理快取

根據預設，Linux 上的 App Service 將使用工作階段親和性 Cookie，來確保具現有工作階段的用戶端要求會路由傳送到您應用程式的相同執行個體。 此預設行為不需要任何設定，但有一些限制：

- 如果應用程式執行個體會重新啟動或相應減少，應用程式伺服器中的使用者工作階段狀態將會遺失。
- 如果應用程式具有很長的工作階段逾時設定或固定的使用者數目，可能需要一些時間，才能自動調整新的執行個體來接收負載，因為只會將新的工作階段路由傳送至最新啟動的執行個體。

您可以設定要使用外部工作階段存放區，例如 WildFly [redis Azure 快取](/azure/azure-cache-for-redis/)。 您必須[停用現有的 ARR 執行個體同質性](https://azure.microsoft.com/blog/disabling-arrs-instance-affinity-in-windows-azure-web-sites/)關閉工作階段 cookie 為基礎的路由，並允許設定的 WildFly 工作階段存放區，運作不受干擾的組態。

## <a name="docker-containers"></a>Docker 容器

若要在您的容器中使用支援 Azure 的 Zulu JDK，請務必提取並使用預先建置映像 (如[適用於 Azure 的支援 Azul Zulu Enterprise 下載頁面](https://www.azul.com/downloads/azure-only/zulu/) \(英文\) 所述)，或使用來自 [Microsoft Java GitHub 存放庫](https://github.com/Microsoft/java/tree/master/docker) 的 `Dockerfile` 範例。

## <a name="statement-of-support"></a>支援聲明

### <a name="runtime-availability"></a>執行階段可用性

App Service for Linux 支援 Java Web 應用程式受控裝載的兩個執行階段：

- [Tomcat servlet 容器](https://tomcat.apache.org/)，適用於執行封裝為 Web 封存 (WAR) 檔案的應用程式。 支援的版本是 8.5 和 9.0。
- Java SE 執行階段環境，適用於執行封裝為 Java 封存 (JAR) 檔案的應用程式。 支援的版本為 Java 8 和 11。

### <a name="jdk-versions-and-maintenance"></a>JDK 版本和維護

OpenJDK 的 Azul Zulu Enterprise 組建是免費、多平台、可實際執行的 OpenJDK 散發套件，適用於 Azure 和 Azure Stack，由 Microsoft 與 Azul Systems 提供支援。 其中包含建置及執行 Java SE 應用程式所需的所有元件。 您可以安裝的 JDK 進行[Java JDK 安裝](https://aka.ms/azure-jdks)。

每年 1 月、4 月、7 月和 10 月都會以季為單位自動修補支援的 JDK。

### <a name="security-updates"></a>安全性更新

只要主要資訊安全漏洞的修補程式和修正程式在 Azul Systems 中變成可用，就予以發行。 在 [NIST Common Vulnerability Scoring System 第 2 版](https://nvd.nist.gov/cvss.cfm)上，「主要」弱點是由基本分數 9.0 或更大值定義。

### <a name="deprecation-and-retirement"></a>取代和淘汰

如果將淘汰支援的 Java 執行階段，則使用受影響執行階段的 Azure 開發人員將會在執行階段淘汰之前至少六個月收到淘汰通知。

## <a name="next-steps"></a>後續步驟

瀏覽[適用於 Java 開發人員的 Azure](/java/azure/) 中心，以找出 Azure 快速入門、教學課程和 Java 參考文件。

[App Service Linux 常見問題集](app-service-linux-faq.md)中回答了非 Java 開發特定的一般 App Service for Linux 使用問題。
