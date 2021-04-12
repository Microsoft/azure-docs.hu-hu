---
title: Naplók és mérőszámok elemzése az Azure Spring Cloud-ban | Microsoft Docs
description: Ismerje meg, hogyan elemezheti a diagnosztikai adatait az Azure Spring Cloud-ban
author: bmitchell287
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 01/06/2020
ms.author: brendm
ms.custom: devx-track-java
ms.openlocfilehash: 68b9aee49e4cf7c02c07fc7dd90e9cc6065a443c
ms.sourcegitcommit: 20f8bf22d621a34df5374ddf0cd324d3a762d46d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/09/2021
ms.locfileid: "107259421"
---
# <a name="analyze-logs-and-metrics-with-diagnostics-settings"></a>Naplók és mérőszámok elemzése diagnosztikai beállításokkal

**Ez a cikk a következőkre vonatkozik:** ✔️ Java ✔️ C #

Az Azure Spring Cloud diagnosztikai funkciójának használatával a naplók és a metrikák a következő szolgáltatásokkal elemezhetők:

* Használja az Azure Log Analyticst, ahol az Azure Storage-ba írja az adatbevitelt. A naplók Log Analyticsba való exportálásakor késés történik.
* Naplókat menthet egy Storage-fiókba a naplózáshoz vagy a manuális ellenőrzéshez. Megadhatja a megőrzési időt (napokban).
* Adatfolyam-naplók küldése az Event hub-nak harmadik féltől származó szolgáltatás vagy egyéni elemzési megoldás betöltéséhez.

Válassza ki a figyelni kívánt naplózási kategóriát és metrikai kategóriát.

> [!TIP]
> Csak szeretné továbbítani a naplókat? Tekintse meg ezt az [Azure CLI-parancsot](/cli/azure/ext/spring-cloud/spring-cloud/app#ext-spring-cloud-az-spring-cloud-app-logs)!

## <a name="logs"></a>Naplók

|Napló | Description |
|----|----|
| **ApplicationConsole** | Az összes felhasználói alkalmazás konzoljának naplója. |
| **SystemLogs** | Jelenleg csak a [Spring Cloud config Server](https://cloud.spring.io/spring-cloud-config/reference/html/#_spring_cloud_config_server) naplózza ezt a kategóriát. |

## <a name="metrics"></a>Mérőszámok

A metrikák teljes listájáért lásd: [Spring Cloud mérőszámok](./spring-cloud-concept-metrics.md#user-metrics-options).

A kezdéshez engedélyezze, hogy az egyik szolgáltatás megkapja az adatfogadást. A Log Analytics konfigurálásával kapcsolatos további tudnivalókért tekintse meg a [log Analytics beszerzése a Azure monitorban](../azure-monitor/logs/log-analytics-tutorial.md)című témakört.

## <a name="configure-diagnostics-settings"></a>Diagnosztikai beállítások konfigurálása

1. A Azure Portal nyissa meg az Azure Spring Cloud-példányát.
1. Válassza a **diagnosztikai beállítások** lehetőséget, majd válassza a **diagnosztika hozzáadása beállítást**.
1. Adja meg a beállítás nevét, majd válassza ki, hová szeretné elküldeni a naplókat. A következő három lehetőség bármelyik kombinációját kiválaszthatja:
    * **Archiválás egy Storage-fiókba**
    * **Streamelés eseményközpontba**
    * **Küldés a Log Analyticsnek**

1. Válassza ki a figyelni kívánt naplózási kategóriát és metrikai kategóriát, majd adja meg a megőrzési időt (napokban). A megőrzési idő csak a Storage-fiókra vonatkozik.
1. Kattintson a **Mentés** gombra.

> [!NOTE]
> 1. A naplók és a metrikák kibocsátása között akár 15 percet is igénybe vehet, és amikor megjelennek a Storage-fiókban, az Event hub vagy a Log Analytics.
> 1. Ha az Azure Spring Cloud-példányt törli vagy áthelyezi, a művelet nem fog a **diagnosztikai beállítások** erőforrásaihoz kaszkádba helyezni. A **diagnosztikai beállítások** erőforrásait manuálisan kell törölni a szülővel szembeni művelet előtt, azaz az Azure Spring Cloud-példányon. Ellenkező esetben, ha egy új Azure Spring Cloud-példány van kiépítve ugyanazzal az erőforrás-AZONOSÍTÓval, mint a törölt, vagy ha az Azure Spring Cloud-példány vissza lett helyezve, a korábbi **diagnosztikai beállítások** erőforrásai tovább bővítik azt.

## <a name="view-the-logs-and-metrics"></a>A naplók és a metrikák megtekintése
A naplók és a metrikák megtekintésére többféle módszer áll rendelkezésre a következő fejlécekben leírtak szerint.

### <a name="use-the-logs-blade"></a>A naplók panel használata

1. A Azure Portal nyissa meg az Azure Spring Cloud-példányát.
1. A **naplóbeli keresés** ablaktábla megnyitásához válassza a **naplók** lehetőséget.
1. A **táblák** keresési mezőjében
   * A naplók megtekintéséhez adjon meg egy egyszerű lekérdezést, például:

    ```sql
    AppPlatformLogsforSpring
    | limit 50
    ```
   * A metrikák megtekintéséhez adjon meg egy egyszerű lekérdezést, például:

    ```sql
    AzureMetrics
    | limit 50
    ```
1. A keresés eredményének megtekintéséhez válassza a **Futtatás** lehetőséget.

### <a name="use-log-analytics"></a>A Log Analytics használata

1. A Azure Portal a bal oldali ablaktáblán válassza a **log Analytics** lehetőséget.
1. Válassza ki a diagnosztikai beállítások hozzáadásakor kiválasztott Log Analytics munkaterületet.
1. A **naplóbeli keresés** ablaktábla megnyitásához válassza a **naplók** lehetőséget.
1. A **táblák** keresési mezőjében
   * a naplók megtekintéséhez adjon meg egy egyszerű lekérdezést, például:

    ```sql
    AppPlatformLogsforSpring
    | limit 50
    ```
    * a metrikák megtekintéséhez adjon meg egy egyszerű lekérdezést, például:

    ```sql
    AzureMetrics
    | limit 50
    ```

1. A keresés eredményének megtekintéséhez válassza a **Futtatás** lehetőséget.
1. A szűrési feltétel beállításával az adott alkalmazás vagy példány naplóit is megkeresheti:

    ```sql
    AppPlatformLogsforSpring
    | where ServiceName == "YourServiceName" and AppName == "YourAppName" and InstanceName == "YourInstanceName"
    | limit 50
    ```
> [!NOTE]
> `==` megkülönbözteti a kis-és nagybetűket, de `=~` nem.

Ha többet szeretne megtudni a Log Analytics használt lekérdezési nyelvről, tekintse meg a [Azure monitor a naplók lekérdezését](/azure/data-explorer/kusto/query/)ismertető témakört. Ha az összes Log Analytics-naplót egy központi ügyfélről szeretné lekérdezni, tekintse meg az [Azure adatkezelőt](https://docs.microsoft.com/azure/data-explorer/query-monitor-data).

### <a name="use-your-storage-account"></a>A Storage-fiók használata

1. A Azure Portalban keresse meg a **Storage-fiókokat** a bal oldali navigációs panelen vagy a keresőmezőbe.
1. Válassza ki azt a Storage-fiókot, amelyet a diagnosztikai beállítások hozzáadásakor választott ki.
1. A blob- **tároló** panel megnyitásához válassza a **Blobok** lehetőséget.
1. Az alkalmazás naplófájljainak áttekintéséhez keressen rá az elemzések **-naplók-applicationconsole** nevű tárolóra.
1. Az alkalmazás metrikáinak áttekintéséhez keressen rá a következő nevű tárolóra: **pt1m**.

A diagnosztikai adatok Storage-fiókba való küldésével kapcsolatos további tudnivalókért lásd: [diagnosztikai adatok tárolása és megtekintése az Azure Storage-ban](../storage/common/storage-introduction.md).

### <a name="use-your-event-hub"></a>Az Event hub használata

1. A Azure Portal keresse meg **Event Hubs** a bal oldali navigációs panelen vagy a keresőmezőbe.

1. Keresse meg és válassza ki azt az Event hub-t, amelyet a diagnosztikai beállítások hozzáadásakor választott ki.
1. Az **Event hub-lista** panel megnyitásához válassza a **Event Hubs** lehetőséget.
1. Az alkalmazás naplófájljainak áttekintéséhez keressen rá az "elemzések – **naplók – applicationconsole**" nevű Event hub kifejezésre.
1. Az alkalmazás-metrikák áttekintéséhez keressen rá az **pt1m** nevű Event hub kifejezésre.

Ha többet szeretne megtudni a diagnosztikai információk esemény-központba küldéséről, olvassa el a következő témakört: [Streaming Azure Diagnostics adatok a gyors elérési úton Event Hubs használatával](../azure-monitor/agents/diagnostics-extension-stream-event-hubs.md).

## <a name="analyze-the-logs"></a>A naplók elemzése

Az Azure Log Analytics Kusto motorral fut, így elemzés céljából lekérdezheti a naplókat. A naplók Kusto használatával történő lekérdezésének gyors bevezetéséhez tekintse át a [log Analytics oktatóanyagot](../azure-monitor/logs/log-analytics-tutorial.md).

Az alkalmazás naplói kritikus információkat és részletes naplókat biztosítanak az alkalmazás állapotával, teljesítményével és egyéb adataival kapcsolatban. A következő részekben néhány egyszerű lekérdezés segíti az alkalmazás jelenlegi és múltbeli állapotának megértését.

### <a name="show-application-logs-from-azure-spring-cloud"></a>Alkalmazás-naplók megjelenítése az Azure Spring Cloud-ból

Ha szeretné áttekinteni az Azure Spring Cloud-ból származó alkalmazás-naplók listáját, akkor az idő szerint rendezve jelenítse meg a legutóbbi naplókat, és futtassa az alábbi lekérdezést:

```sql
AppPlatformLogsforSpring
| project TimeGenerated , ServiceName , AppName , InstanceName , Log
| sort by TimeGenerated desc
```

### <a name="show-logs-entries-containing-errors-or-exceptions"></a>Hibákat vagy kivételeket tartalmazó naplók bejegyzéseinek megjelenítése

A hibát vagy kivételt megemlítő rendezetlen naplóbejegyzések áttekintéséhez futtassa a következő lekérdezést:

```sql
AppPlatformLogsforSpring
| project TimeGenerated , ServiceName , AppName , InstanceName , Log
| where Log contains "error" or Log contains "exception"
```

Ezzel a lekérdezéssel hibákat kereshet, vagy módosíthatja a lekérdezési feltételeket adott hibakódok vagy kivételek kereséséhez.

### <a name="show-the-number-of-errors-and-exceptions-reported-by-your-application-over-the-last-hour"></a>Az alkalmazás által az elmúlt órában jelentett hibák és kivételek számának megjelenítése

A következő lekérdezés futtatásával létrehozhat egy tortadiagramot, amely megjeleníti az alkalmazás által az elmúlt órában naplózott hibák és kivételek számát:

```sql
AppPlatformLogsforSpring
| where TimeGenerated > ago(1h)
| where Log contains "error" or Log contains "exception"
| summarize count_per_app = count() by AppName
| sort by count_per_app desc
| render piechart
```

### <a name="learn-more-about-querying-application-logs"></a>További információ az alkalmazási naplók lekérdezéséről

Azure Monitor széles körű támogatást nyújt az alkalmazások naplófájljainak lekérdezéséhez Log Analytics használatával. A szolgáltatással kapcsolatos további tudnivalókért tekintse meg a következő témakört: az [első lépések a naplózási lekérdezésekkel kapcsolatban Azure monitor](../azure-monitor/logs/get-started-queries.md). Az alkalmazás naplófájljainak elemzésére szolgáló lekérdezések létrehozásával kapcsolatos további információkért lásd: [a Azure monitorban található lekérdezések áttekintése](../azure-monitor/logs/log-query-overview.md).

## <a name="frequently-asked-questions-faq"></a>Gyakori kérdések (GYIK)

### <a name="how-to-convert-multi-line-java-stack-traces-into-a-single-line"></a>Többsoros Java stack-nyomkövetés átalakítása egyetlen sorba

Megkerülő megoldás, hogy a többsoros verem nyomkövetéseit egyetlen sorba alakítsa át. A Java-napló kimenetét módosíthatja a verem nyomkövetési üzeneteinek újraformázásához, a sortörési karaktereket pedig tokenre cserélve. Ha Java Logback-függvénytárat használ, a következőképpen formázhatja a verem nyomkövetési üzeneteit `%replace(%ex){'[\r\n]+', '\\n'}%nopex` :

```xml
<configuration>
    <appender name="CONSOLE" class="ch.qos.logback.core.ConsoleAppender">
        <encoder>
            <pattern>
                level: %level, message: "%logger{36}: %msg", exceptions: "%replace(%ex){'[\r\n]+', '\\n'}%nopex"%n
            </pattern>
        </encoder>
    </appender>
    <root level="INFO">
        <appender-ref ref="CONSOLE"/>
    </root>
</configuration>
```
Ezután az alábbi módon lecserélheti a tokent sortörési karakterekkel Log Analytics az alábbiak szerint:

```sql
AppPlatformLogsforSpring
| extend Log = array_strcat(split(Log, '\\n'), '\n')
```
Használhatja ugyanezt a stratégiát más Java-naplókhoz is.

## <a name="next-steps"></a>Következő lépések

* [Rövid útmutató: az első Azure Spring Cloud-alkalmazás üzembe helyezése](spring-cloud-quickstart.md)
