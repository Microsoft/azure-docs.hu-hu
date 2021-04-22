---
title: Naplók és metrikák elemzése a Azure Spring Cloud | Microsoft Docs
description: Ismerje meg, hogyan elemezheti a diagnosztikai adatokat a Azure Spring Cloud
author: bmitchell287
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 01/06/2020
ms.author: brendm
ms.custom: devx-track-java
ms.openlocfilehash: 6472ba7dd055de97a1855211f21fd0c75eea814f
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2021
ms.locfileid: "107874116"
---
# <a name="analyze-logs-and-metrics-with-diagnostics-settings"></a>Naplók és metrikák elemzése diagnosztikai beállításokkal

**Ez a cikk a következőkre vonatkozik:** ✔️ Java ✔️ C #

A diagnosztikai funkciók Azure Spring Cloud naplókat és metrikákat elemezhet az alábbi szolgáltatások bármelyikével:

* Használja az Azure Log Analyticset, ahol az adatok az Azure Storage-ba vannak írva. A naplók Log Analyticsbe exportálása késéssel történik.
* Naplók mentése egy tárfiókba naplózás vagy manuális vizsgálat céljából. Megadhatja a megőrzési időt (napokban).
* Naplók streamelése az eseményközpontba külső szolgáltatás vagy egyéni elemzési megoldás által történő behozás céljából.

Válassza ki a figyelni kívánt naplókategóriát és metrikakategóriát.

> [!TIP]
> Csak streamelni szeretné a naplóit? Tekintse meg ezt az [Azure CLI-parancsot!](/cli/azure/spring-cloud/app#az_spring_cloud_app_logs)

## <a name="logs"></a>Naplók

|Napló | Description |
|----|----|
| **ApplicationConsole (Alkalmazáskonzol)** | Az összes ügyfélalkalmazás konzolnaplója. |
| **SystemLogs** | Jelenleg csak [Spring Cloud Config Server](https://cloud.spring.io/spring-cloud-config/reference/html/#_spring_cloud_config_server) ebbe a kategóriába tartozó naplókat. |

## <a name="metrics"></a>Mérőszámok

A metrikák teljes listáját a Metrikák [Spring Cloud.](./spring-cloud-concept-metrics.md#user-metrics-options)

Első lépésekként engedélyezze ezen szolgáltatások egyikének az adatok fogadását. A Log Analytics konfigurálásával kapcsolatos további információkért lásd: Ismerkedés a [Log Analytics szolgáltatással a Azure Monitor.](../azure-monitor/logs/log-analytics-tutorial.md)

## <a name="configure-diagnostics-settings"></a>Diagnosztikai beállítások konfigurálása

1. A Azure Portal a saját Azure Spring Cloud meg.
1. Válassza **a Diagnosztikai beállítások lehetőséget,** majd válassza a Diagnosztikai beállítás hozzáadása **lehetőséget.**
1. Adja meg a beállítás nevét, majd válassza ki, hová szeretné küldeni a naplókat. Az alábbi három lehetőség bármelyik kombinációját választhatja:
    * **Archiválás tárfiókba**
    * **Streamelés eseményközpontba**
    * **Küldés a Log Analyticsnek**

1. Válassza ki a figyelni kívánt naplókategóriát és metrikakategóriát, majd adja meg a megőrzési időt (napokban). A megőrzési idő csak a tárfiókra vonatkozik.
1. Kattintson a **Mentés** gombra.

> [!NOTE]
> 1. Akár 15 perces eltérés is lehet a naplók vagy metrikák kibocsátása és a tárfiókban, az eseményközpontban vagy a Log Analyticsben való megjelenése között.
> 1. Ha az Azure Spring Cloud-példányt törlik vagy áthelyezik, a művelet nem lesz kaszkádolt a **diagnosztikai beállítások erőforrásaihoz.** A **diagnosztikai beállítások erőforrásait** manuálisan kell törölni a szülőre vonatkozó művelet előtt, azaz a Azure Spring Cloud előtt. Ellenkező esetben, ha egy új Azure Spring Cloud-példányt a törölt erőforrás-azonosítóval azonos erőforrás-azonosítóval hoznak létre,  vagy ha a Azure Spring Cloud-példányt visszahelyezték, a korábbi diagnosztikai beállítások erőforrásai tovább bővítik azt.

## <a name="view-the-logs-and-metrics"></a>Naplók és metrikák megtekintése
A naplók és metrikák megtekintésére többféle módszer létezik az alábbi fejlécek alapján.

### <a name="use-the-logs-blade"></a>A Naplók panel használata

1. A Azure Portal a saját Azure Spring Cloud meg.
1. A Naplók **keresése panel megnyitásához** válassza a Naplók **lehetőséget.**
1. A Táblák **keresőmezőben**
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
1. A keresési eredmények megtekintéséhez válassza a **Futtatás lehetőséget.**

### <a name="use-log-analytics"></a>A Log Analytics használata

1. A Azure Portal bal oldali panelen válassza a **Log Analytics lehetőséget.**
1. Válassza ki a diagnosztikai beállítások hozzáadásakor kiválasztott Log Analytics-munkaterületet.
1. A Naplók **keresése panel megnyitásához** válassza a Naplók **lehetőséget.**
1. A Táblák **keresőmezőben:**
   * a naplók megtekintéséhez adjon meg egy egyszerű lekérdezést, például:

    ```sql
    AppPlatformLogsforSpring
    | limit 50
    ```
    * A metrikák megtekintéséhez adjon meg egy egyszerű lekérdezést, például:

    ```sql
    AzureMetrics
    | limit 50
    ```

1. A keresési eredmények megtekintéséhez válassza a **Futtatás lehetőséget.**
1. Az adott alkalmazás vagy példány naplóiban egy szűrési feltétel beállításával kereshet:

    ```sql
    AppPlatformLogsforSpring
    | where ServiceName == "YourServiceName" and AppName == "YourAppName" and InstanceName == "YourInstanceName"
    | limit 50
    ```
> [!NOTE]
> `==` A megkülönbözteti a kis- és nagybetűket, de `=~` nem.

A Log Analyticsben használt lekérdezési nyelvvel kapcsolatos további információkért lásd a naplólekérdezések [Azure Monitor.](/azure/data-explorer/kusto/query/) Az összes Log Analytics-napló központi ügyfélről való lekérdezéséhez tekintse meg a [Azure Data Explorer.](https://docs.microsoft.com/azure/data-explorer/query-monitor-data)

### <a name="use-your-storage-account"></a>A tárfiók használata

1. A Azure Portal keresse meg a **Tárfiókok et** a bal oldali navigációs panelen vagy a keresőmezőben.
1. Válassza ki a diagnosztikai beállítások hozzáadásakor kiválasztott tárfiókot.
1. A Blobtároló **panel megnyitásához** válassza a **Blobok lehetőséget.**
1. Az alkalmazásnaplók áttekintéshez keressen rá az **insights-logs-applicationconsole nevű tárolóra.**
1. Az alkalmazásmetrikák áttekintéshez keressen rá az **insights-metrics-pt1m nevű tárolóra.**

A diagnosztikai adatok tárfiókba való küldésével kapcsolatos további információkért lásd: Diagnosztikai adatok tárolása és megtekintése az [Azure Storage-ban.](../storage/common/storage-introduction.md)

### <a name="use-your-event-hub"></a>Az eseményközpont használata

1. A Azure Portal **keresse** meg Event Hubs bal oldali navigációs panelen vagy keresőmezőben.

1. Keresse meg és válassza ki a diagnosztikai beállítások hozzáadásakor kiválasztott eseményközpontot.
1. Az **Eseményközpontok listája panel megnyitásához** válassza **a** Event Hubs.
1. Az alkalmazásnaplók áttekintéshez keressen egy **insights-logs-applicationconsole nevű eseményközpontot.**
1. Az alkalmazásmetrikák áttekintéshez keressen rá az **insights-metrics-pt1m nevű eseményközpontra.**

A diagnosztikai információk eseményközpontba való küldésével kapcsolatos további információkért lásd: Streamelési Azure Diagnostics adatok streamelése a gyors elérési úton [a következő használatával: Event Hubs.](../azure-monitor/agents/diagnostics-extension-stream-event-hubs.md)

## <a name="analyze-the-logs"></a>A naplók elemzése

Az Azure Log Analytics egy Kusto-motorral fut, így lekérdezheti a naplókat elemzés céljából. A naplók Kusto használatával történő lekérdezésének gyors bemutatásáért tekintse meg a [Log Analytics oktatóanyagát.](../azure-monitor/logs/log-analytics-tutorial.md)

Az alkalmazásnaplók kritikus információkat és részletes naplókat biztosítanak az alkalmazás állapotáról, teljesítményéről és sok másról. A következő szakaszok néhány egyszerű lekérdezést tartalmaznak, amelyek segítenek megérteni az alkalmazás aktuális és korábbi állapotát.

### <a name="show-application-logs-from-azure-spring-cloud"></a>Alkalmazásnaplók megjelenítése Azure Spring Cloud

A következő lekérdezés futtatásával áttekintheti az alkalmazásnaplók Azure Spring Cloud idő szerint rendezve a legújabb naplókat:

```sql
AppPlatformLogsforSpring
| project TimeGenerated , ServiceName , AppName , InstanceName , Log
| sort by TimeGenerated desc
```

### <a name="show-logs-entries-containing-errors-or-exceptions"></a>Hibákat vagy kivételeket tartalmazó naplóbejegyzések megjelenítése

A hibát vagy kivételt megemlítő nem rendezetlen naplóbejegyzések áttekintéséhez futtassa a következő lekérdezést:

```sql
AppPlatformLogsforSpring
| project TimeGenerated , ServiceName , AppName , InstanceName , Log
| where Log contains "error" or Log contains "exception"
```

Ezzel a lekérdezéssel hibákat találhat, vagy módosíthatja a lekérdezési kifejezéseket adott hibakódok vagy kivételek kereséséhez.

### <a name="show-the-number-of-errors-and-exceptions-reported-by-your-application-over-the-last-hour"></a>Az alkalmazás által jelentett hibák és kivételek számának megjelenítése az elmúlt órában

Az alkalmazás által az elmúlt órában naplózott hibák és kivételek számát megjelenítő tortadiagram létrehozásához futtassa a következő lekérdezést:

```sql
AppPlatformLogsforSpring
| where TimeGenerated > ago(1h)
| where Log contains "error" or Log contains "exception"
| summarize count_per_app = count() by AppName
| sort by count_per_app desc
| render piechart
```

### <a name="learn-more-about-querying-application-logs"></a>További információ az alkalmazásnaplók lekérdezésről

Azure Monitor széles körű támogatást nyújt az alkalmazásnaplók Log Analytics használatával való lekérdezéséhez. További információ erről a szolgáltatásról: Ismerkedés a [naplólekérdezésekkel](../azure-monitor/logs/get-started-queries.md)a Azure Monitor. További információ az alkalmazásnaplók elemzéséhez szükséges lekérdezések építéséről: [A naplólekérdezések](../azure-monitor/logs/log-query-overview.md)áttekintése a Azure Monitor.

## <a name="frequently-asked-questions-faq"></a>Gyakori kérdések (GYIK)

### <a name="how-to-convert-multi-line-java-stack-traces-into-a-single-line"></a>Hogyan konvertálhatók a többsoros Java-verem nyomkövetései egyetlen sorba?

Megkerülő megoldással egyetlen sorra konvertálhatja a többsoros verem nyomkövetését. A Java-napló kimenetét módosíthatja úgy, hogy újraformálja a verem nyomkövetési üzeneteit, és a newline karaktereket egy tokenre cserélje. Ha Java Logback-kódtárat használ, újraformálhatja a veremkövetési üzeneteket a következő `%replace(%ex){'[\r\n]+', '\\n'}%nopex` hozzáadásával:

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
Ezután a tokent újsori karakterekre cserélheti újra a Log Analyticsben az alábbiak szerint:

```sql
AppPlatformLogsforSpring
| extend Log = array_strcat(split(Log, '\\n'), '\n')
```
Más Java-naplótárakhoz is használhatja ugyanezt a stratégiát.

## <a name="next-steps"></a>Következő lépések

* [Rövid útmutató: Az első Azure Spring Cloud üzembe helyezése](spring-cloud-quickstart.md)
