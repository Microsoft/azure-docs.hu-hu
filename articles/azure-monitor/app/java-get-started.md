---
title: 'Gyors útmutató: Java Web App Analytics az Azure Application Insights'
description: 'Alkalmazásteljesítmény-figyelés Java-webalkalmazásokhoz az Application Insights használatával. '
ms.topic: conceptual
ms.date: 11/22/2020
author: MS-jgol
ms.custom: devx-track-java
ms.author: jgol
ms.openlocfilehash: cdad5a0ec158f216a63ae7e1e2abc10d2841b3bc
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "100593733"
---
# <a name="quickstart-get-started-with-application-insights-in-a-java-web-project"></a>Gyorsútmutató: Az Application Insights használatba vétele egy webes Java-projektben


> [!CAUTION]
> A Java-alkalmazások figyelésére november 2020-én a Azure Monitor Application Insights Java 3,0-ügynököt használó automatikus rendszerállapotot javasoljuk. További információ az első lépésekről: [Application Insights Java 3,0-ügynök](./java-in-process-agent.md).

Ebben a rövid útmutatóban az Application Insights SDK-t használja a rendszer, a függőségek nyomon követéséhez és a teljesítményszámlálók összegyűjtéséhez, a teljesítménnyel kapcsolatos problémák és kivételek diagnosztizálásához, valamint kód írásához a felhasználók által az alkalmazással való használat nyomon követéséhez.

Az Application Insights egy bővíthető elemzési szolgáltatás a webfejlesztők számára, amely segít megérteni az élő alkalmazása teljesítményét és használatát. Az Application Insights a Linux, Unix vagy Windows rendszeren futó Java alkalmazásokat támogatja.

## <a name="prerequisites"></a>Előfeltételek

* Aktív előfizetéssel rendelkező Azure-fiók. [Hozzon létre egy fiókot ingyenesen](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
* Működő Java-alkalmazás.

## <a name="get-an-application-insights-instrumentation-key"></a>Application Insights-kialakítási kulcs beszerzése

> [!IMPORTANT]
> Az új Azure-régiókban a rendszerállapot-kulcsok helyett a kapcsolatok sztringjét **kell** használnia. A [kapcsolódási karakterlánc](./sdk-connection-string.md?tabs=java) azonosítja azt az erőforrást, amelyhez hozzá szeretné rendelni a telemetria-adatait. Azt is lehetővé teszi, hogy módosítsa az erőforrás által a telemetria célként használt végpontokat. A kapcsolódási karakterláncot át kell másolnia, és hozzá kell adnia az alkalmazás kódjához vagy egy környezeti változóhoz.
1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).
2. Hozzon létre egy Application Insights-erőforrást az Azure Portalon. Állítsa be a Java webalkalmazás alkalmazástípust.

3. Keresse meg az új erőforrás kialakítási kulcsát. Ezt a kulcsot nemsokára a kódprojektbe kell illesztenie.

    ![Az új erőforrás áttekintésében kattintson a Tulajdonságok gombra, és másolja le a kialakítási kulcsot](./media/java-get-started/instrumentation-key-001.png)

## <a name="add-the-application-insights-sdk-for-java-to-your-project"></a>A Javához készült Application Insights SDK hozzáadása a projekthez

*Válassza ki a projekt típusát.*

# <a name="maven"></a>[Maven](#tab/maven)

Ha a projekt már be van állítva a Maven for Build használatára, egyesítse a következő kódot a *pom.xml* fájlba.

Ezután frissítse a projektfüggőségeket, hogy letöltse a bináris fájlokat.

```XML
    <dependencies>
      <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>applicationinsights-web-auto</artifactId>
        <!-- or applicationinsights-web for manual web filter registration -->
        <!-- or applicationinsights-core for bare API -->
        <version>2.6.2</version>
      </dependency>
    </dependencies>
```

# <a name="gradle"></a>[Gradle](#tab/gradle)

Ha a projekt már be van állítva a Gradle for Build használatára, egyesítse a következő kódot a *Build. Gradle* fájlba.

Ezután frissítse a projektfüggőségeket, hogy letöltse a bináris fájlokat.

```gradle
    dependencies {
      compile group: 'com.microsoft.azure', name: 'applicationinsights-web-auto', version: '2.6.2'
      // or applicationinsights-web for manual web filter registration
      // or applicationinsights-core for bare API
    }
```

---

### <a name="questions"></a>Kérdések
* *Mi a kapcsolat a és az `-web-auto` `-web` összetevők között `-core` ?*
  * `applicationinsights-web-auto` olyan mérőszámokat biztosít, amelyek nyomon követik a HTTP servlet-kérelmek számát és a válaszadási időpontokat, ha automatikusan regisztrálja a Application Insights servlet szűrőt futásidőben.
  * `applicationinsights-web` olyan metrikákat is biztosít, amelyek nyomon követik a HTTP servlet-kérelmek számát és a válaszadási időpontokat, de az alkalmazásban manuálisan kell regisztrálni az Application Insights servlet-szűrőt.
  * `applicationinsights-core` csak az operációs rendszer nélküli API-t biztosítja, például ha az alkalmazás nem servlet-alapú.
  
* *Hogyan frissíthetek az SDK legújabb verziójára?*
  * A Java-alkalmazások figyelésére november 2020-én a Azure Monitor Application Insights Java 3,0-ügynököt használó automatikus rendszerállapotot javasoljuk. További információ az első lépésekről: [Application Insights Java 3,0-ügynök](./java-in-process-agent.md).

## <a name="add-an-applicationinsightsxml-file"></a>*ApplicationInsights.xml* fájl hozzáadása
Adja hozzá *ApplicationInsights.xml* a projekt erőforrások mappájához, vagy győződjön meg arról, hogy a projekt üzembe helyezési osztályának elérési útjához van adva. Másolja bele a következő XML-t.

Cserélje le a kialakítási kulcsot arra a típusra, amelyet a Azure Portal kapott.

```XML
<?xml version="1.0" encoding="utf-8"?>
<ApplicationInsights xmlns="http://schemas.microsoft.com/ApplicationInsights/2013/Settings" schemaVersion="2014-05-30">

   <!-- The key from the portal: -->
   <InstrumentationKey>** Your instrumentation key **</InstrumentationKey>

   <!-- HTTP request component (not required for bare API) -->
   <TelemetryModules>
      <Add type="com.microsoft.applicationinsights.web.extensibility.modules.WebRequestTrackingTelemetryModule"/>
      <Add type="com.microsoft.applicationinsights.web.extensibility.modules.WebSessionTrackingTelemetryModule"/>
      <Add type="com.microsoft.applicationinsights.web.extensibility.modules.WebUserTrackingTelemetryModule"/>
   </TelemetryModules>

   <!-- Events correlation (not required for bare API) -->
   <!-- These initializers add context data to each event -->
   <TelemetryInitializers>
      <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebOperationIdTelemetryInitializer"/>
      <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebOperationNameTelemetryInitializer"/>
      <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebSessionTelemetryInitializer"/>
      <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebUserTelemetryInitializer"/>
      <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebUserAgentTelemetryInitializer"/>
   </TelemetryInitializers>

</ApplicationInsights>
```

Opcionálisan a konfigurációs fájl bármely, az alkalmazás számára elérhető helyen lehet.  A System tulajdonság `-Dapplicationinsights.configurationDirectory` Megadja a *ApplicationInsights.xml* tartalmazó könyvtárat. Az `E:\myconfigs\appinsights\ApplicationInsights.xml` mappában tárolt konfigurációs fájl konfigurálásához például a `-Dapplicationinsights.configurationDirectory="E:\myconfigs\appinsights"` tulajdonság használható.

* A kialakítási kulcsot a telemetria minden elemével megkapja, és ez közli az Application Insights eszközzel, hogy megjelenítse azt az erőforrásban.
* A HTTP-kérelemösszetevő nem kötelező. Automatikusan telemetriát küld a kérelmekkel és válaszidőkkel kapcsolatban a portálra.
* Az eseménykorreláció a HTTP-kérelemösszetevő további eleme. Azonosítót rendel a kiszolgáló által fogadott összes kérelemhez. Ezután hozzáadja ezt az azonosítót tulajdonságként a telemetria minden eleméhez, mint a "Operation.Id" tulajdonságot. Lehetővé teszi az egyes kérelmekkel társított telemetria korrelációját, ha beállít egy szűrőt a [diagnosztikai keresésben][diagnostic].

### <a name="alternative-ways-to-set-the-instrumentation-key"></a>A kialakítási kulcs beállításának egyéb módjai
Az Application Insights SDK ebben a sorrendben keresi a kulcsot:

1. System tulajdonság:-DAPPINSIGHTS_INSTRUMENTATIONKEY = your_ikey
2. Környezeti változó: APPINSIGHTS_INSTRUMENTATIONKEY
3. Konfigurációs fájl: *ApplicationInsights.xml*

[Beállíthatja a programkódban](./api-custom-events-metrics.md#ikey) is:

```java
    String instrumentationKey = "00000000-0000-0000-0000-000000000000";

    if (instrumentationKey != null)
    {
        TelemetryConfiguration.getActive().setInstrumentationKey(instrumentationKey);
    }
```

## <a name="add-agent"></a>Ügynök hozzáadása

[Telepítse a Java-ügynököt](java-agent.md) a kimenő HTTP-hívások, a JDBC-lekérdezések, az alkalmazások naplózása és a jobb működés elnevezésének rögzítéséhez.

## <a name="run-your-application"></a>Az alkalmazás futtatása
Futtassa hibakeresés módban a fejlesztési számítógépén, vagy tegye közzé a kiszolgálóján.

## <a name="view-your-telemetry-in-application-insights"></a>A telemetria megtekintése az Application Insights szolgáltatásban
Térjen vissza az Application Insights-erőforráshoz a [Microsoft Azure Portalon](https://portal.azure.com).

A HTTP-kérelemadatok az áttekintési panelen jelennek meg. (Ha nincsenek ott, várjon néhány másodpercig, majd kattintson a Frissítés gombra.)

![A mintaadatok áttekintését bemutató képernyőkép](./media/java-get-started/overview-graphs.png)

[További információk a metrikákról.][metrics]

Részletesebb összesített mérőszámokért kattintson bármelyik diagramra.

![Application Insights hibák panel diagramokkal](./media/java-get-started/006-barcharts.png)

### <a name="instance-data"></a>Példányadatok
Kattintson az adott kérelemtípusokra az egyes példányok megtekintéséhez.

![Részletezés egy adott minta nézetbe](./media/java-get-started/007-instance.png)

### <a name="analytics-powerful-query-language"></a>Elemzés: Erőteljes lekérdezési nyelv
Ahogy egyre több adatot gyűjt össze, lekérdezéseket futtathat az adatok összegzéséhez és egyéni példányok megkereséséhez is.  Az [elemzés](../logs/log-query-overview.md) erőteljes eszköz a teljesítmény és a használat megértéséhez és diagnosztikai célokra is.

![Példa elemzésre](./media/java-get-started/0025.png)

## <a name="install-your-app-on-the-server"></a>Az alkalmazás telepítése a kiszolgálón
Most tegye közzé az alkalmazást a kiszolgálón, hagyja, hogy mások használják, és nézze, ahogyan a telemetria megjelenik a portálon.

* Győződjön meg arról, hogy a tűzfal lehetővé teszi, hogy az alkalmazás telemetriát küldjön ezekre a portokra:

  * dc.services.visualstudio.com:443
  * f5.services.visualstudio.com:443

* Ha a kimenő forgalmat át kell irányítani egy tűzfalon, adja meg a `http.proxyHost` és a `http.proxyPort` rendszertulajdonságot.

* Windows-kiszolgálókon telepítse a következőt:

  * [Microsoft Visual C++ újraterjeszthető csomag](https://www.microsoft.com/download/details.aspx?id=40784)

    (Ez az összetevő lehetővé teszi a teljesítményszámlálókat.)

## <a name="azure-app-service-aks-vms-config"></a>Azure App Service, AK, virtuális gépek konfigurációja

Az Azure Resource Providers szolgáltatásban futtatott alkalmazások monitorozásának legjobb és legegyszerűbb megközelítése az Application Insights automatikus rendszerállapotának használata a [Java 3,0-ügynökön](./java-in-process-agent.md)keresztül.


## <a name="exceptions-and-request-failures"></a>Kivételek és kérelemhibák
A nem kezelt kivételeket és a kérelmekkel kapcsolatos hibákat a Application Insights webes szűrő automatikusan gyűjti.

Ha más kivételekkel szeretne adatokat gyűjteni, a [kódban trackException () hívásokat is beszúrhat][apiexceptions].

## <a name="monitor-method-calls-and-external-dependencies"></a>Metódushívások és külső függőségek megfigyelése
[Telepítse a Java ügynököt](java-agent.md) a megadott belső módszerek és a JDBC-n keresztül végzett hívások naplózásához, időzítési adatokkal.

És automatikus művelet-elnevezéssel.

## <a name="w3c-distributed-tracing"></a>W3C elosztott nyomkövetés

A Application Insights Java SDK mostantól támogatja a [W3C elosztott nyomkövetést](https://w3c.github.io/trace-context/).

A bejövő SDK konfigurációját a [korrelációról](correlation.md)szóló cikkben ismertetjük.

A kimenő SDK-konfiguráció a [AI-Agent.xml](java-agent.md) fájlban van definiálva.

## <a name="performance-counters"></a>Teljesítményszámlálók
Nyissa meg a **vizsgálat**, **mérőszámok** lehetőséget, és tekintse meg a teljesítményszámlálók tartományát.

![Képernyőfelvétel a metrikák panelről a folyamat saját bájtjainak kiválasztásával](./media/java-get-started/011-perf-counters.png)

### <a name="customize-performance-counter-collection"></a>Teljesítményszámláló-gyűjtemény testreszabása
A teljesítményszámlálók szabványos készlete gyűjtésének letiltásához adja hozzá a következő kódot a *ApplicationInsights.xml* fájl legfelső csomópontjában:

```XML
    <PerformanceCounters>
       <UseBuiltIn>False</UseBuiltIn>
    </PerformanceCounters>
```

### <a name="collect-additional-performance-counters"></a>További teljesítményszámlálók gyűjtése
További gyűjtendő teljesítményszámlálókat határozhat meg.

#### <a name="jmx-counters-exposed-by-the-java-virtual-machine"></a>JMX-számlálók (a Java virtuális gép által feltárt)

```XML
    <PerformanceCounters>
      <Jmx>
        <Add objectName="java.lang:type=ClassLoading" attribute="TotalLoadedClassCount" displayName="Loaded Class Count"/>
        <Add objectName="java.lang:type=Memory" attribute="HeapMemoryUsage.used" displayName="Heap Memory Usage-used" type="composite"/>
      </Jmx>
    </PerformanceCounters>
```

* `displayName`– Az Application Insights portálon megjelenő név.
* `objectName`– A JMX objektum neve.
* `attribute`– A JMX objektum nevének lehívni kívánt attribútuma
* `type` (nem kötelező) – a JMX objektum attribútumának típusa:
  * Alapértelmezett: egyszerű típus, például int vagy long.
  * `composite`: a teljesítményszámláló-adatok az „Attribútum.Adat” formátumban szerepelnek
  * `tabular`: a teljesítményszámláló-adatok táblázatsor formájában szerepelnek

#### <a name="windows-performance-counters"></a>Windows-teljesítményszámlálók
Mindegyik [Windows-teljesítményszámláló](/windows/win32/perfctrs/performance-counters-portal) egy kategória tagja (ugyanúgy, ahogyan a mezők osztályok tagjai). A kategóriák globálisak lehetnek, vagy számozott vagy elnevezett példányokkal rendelkezhetnek.

```XML
    <PerformanceCounters>
      <Windows>
        <Add displayName="Process User Time" categoryName="Process" counterName="%User Time" instanceName="__SELF__" />
        <Add displayName="Bytes Printed per Second" categoryName="Print Queue" counterName="Bytes Printed/sec" instanceName="Fax" />
      </Windows>
    </PerformanceCounters>
```

* displayName – Az Application Insights portálon megjelenő név.
* categoryName – A teljesítményszámláló kategóriája (teljesítményobjektum), amelyhez ez a teljesítményszámláló társítva van.
* counterName – A teljesítményszámláló neve.
* instanceName – A teljesítményszámláló-kategória példányneve, vagy üres sztring („”), ha a kategória egyetlen példányt tartalmaz. Ha a categoryName Folyamat, és a gyűjteni kívánt teljesítményszámláló az aktuális JVM folyamatról származik, amelyen az alkalmazása fut, adja meg a következőt: `"__SELF__"`.

### <a name="unix-performance-counters"></a>Unix-teljesítményszámlálók
* [Telepítse a gyűjteményt az Application Insights beépülő modullal](java-collectd.md) számos rendszer- és hálózati adat lekéréséhez.

## <a name="get-user-and-session-data"></a>Felhasználói és munkamenetadatok lekérése
Telemetriát küld a webkiszolgálóról. Az alkalmazás teljes körű megfigyelése érdekében további megfigyelést adhat hozzá:

* [Adjon telemetriát a weblapokhoz][usage] a lapmegtekintések és a felhasználói mérőszámok megfigyelése érdekében.
* [Beállíthat webes teszteket][availability] annak biztosításához, hogy az alkalmazás mindig elérhető és válaszkész legyen.

## <a name="send-your-own-telemetry"></a>Saját telemetria küldése
Most, hogy telepítette az SDK-t, az API-val saját telemetriát küldhet.

* [Nyomon követheti az egyéni eseményeket és mérőszámokat][api], hogy megtudja, hogyan használják a felhasználók az alkalmazását.
* [Eseményeket és naplókat kereshet][diagnostic], amelyek segítenek a problémák diagnosztizálásában.

## <a name="availability-web-tests"></a>Rendelkezésre állási webes tesztek
Az Application Insights rendszeres időközönként teszteli a webhelyét, hogy működik és jól válaszol-e.

[További információ a rendelkezésre állási webes tesztek beállításáról.][availability]

## <a name="questions-problems"></a>Kérdése van? Problémákat tapasztal?
[A Java hibaelhárítása](java-troubleshoot.md)

## <a name="next-steps"></a>Következő lépések
* [Függőségi hívások figyelése](java-agent.md)
* [Unix-teljesítményszámlálók figyelése](java-collectd.md)
* [A weboldalak figyelésével](javascript.md) megfigyelheti az oldalbetöltési időket, az AJAX-hívásokat és a böngészőkivételeket.
* [Egyéni telemetriát](./api-custom-events-metrics.md) írhat a böngészőben vagy a kiszolgálón való használat nyomon követése érdekében.
* Az [Elemzések](../logs/log-query-overview.md) használatával hatékony telemetriai lekérdezéseket hajthat végre az alkalmazásból
* További információ: [Azure Java-fejlesztőknek](/java/azure).

<!--Link references-->

[api]: ./api-custom-events-metrics.md
[apiexceptions]: ./api-custom-events-metrics.md#trackexception
[availability]: ./monitor-web-app-availability.md
[diagnostic]: ./diagnostic-search.md
[javalogs]: java-trace-logs.md
[metrics]: ../essentials/metrics-charts.md
[usage]: javascript.md