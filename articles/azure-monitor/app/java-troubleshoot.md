---
title: Java-webprojektek Application Insightsának hibáinak megoldása
description: Hibaelhárítási útmutató – élő Java-alkalmazások figyelése Application Insightsokkal.
ms.topic: conceptual
ms.date: 03/14/2019
author: MS-jgol
ms.custom: devx-track-java
ms.author: jgol
ms.openlocfilehash: df1f58418452ecafacddd013ab815ebca5cb8a35
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "100097551"
---
# <a name="troubleshooting-and-q-and-a-for-application-insights-for-java-sdk"></a>Hibaelhárítás és Q és A for Application Insights for Java SDK

> [!IMPORTANT]
> A Java-alkalmazások figyelésének ajánlott módszere az automatikus kiépítés használata a kód módosítása nélkül. Kérjük, kövesse az [Application Insights Java 3,0-ügynökre](./java-in-process-agent.md)vonatkozó irányelveket.

[Az Azure Application Insights-][java]mel kapcsolatos kérdések és problémák Java-ban? Íme néhány tipp.

## <a name="build-errors"></a>Felépítési hibák
**Az Eclipse-ben vagy a IntelliJ-ben, ha a Application Insights SDK-t Maven-vagy Gradle-n keresztül adja hozzá, a Build vagy az ellenőrzőösszeg érvényesítési hibáit.**

* Ha a függőségi `<version>` elem helyettesítő karakterekkel (például Maven) `<version>[2.0,)</version>` vagy (Gradle) rendelkező mintázatot használ `version:'2.+'` , próbáljon meg inkább egy adott verziót megadni, például: `2.6.2` .

## <a name="no-data"></a>Nincsenek adatkészletek
**Felvettem Application Insights sikeresen, és futtattam az alkalmazást, de még soha nem láttam az adataim a portálon.**

* Várjon egy percet, és kattintson a frissítés gombra. A diagramok rendszeresen frissülnek, de manuálisan is frissítheti őket. A frissítési időköz a diagram időtartományának függvénye.
* Győződjön meg arról, hogy rendelkezik a ApplicationInsights.xml fájlban (a projekt erőforrások mappájában) vagy környezeti változóként konfigurált kialakítási kulccsal.
* Ellenőrizze, hogy nincs `<DisableTelemetry>true</DisableTelemetry>` -e csomópont az XML-fájlban.
* Előfordulhat, hogy a tűzfalon meg kell nyitnia a 80-es és a 443-es TCP-portot a dc.services.visualstudio.com kimenő forgalmához. A [tűzfal-kivételek teljes listáját](./ip-addresses.md) lásd:
* A Microsoft Azure kezdőképernyőn tekintse meg a szolgáltatás állapotát jelző lapot. Ha vannak riasztási jelzések, várjon, amíg vissza nem tért az OK gombra, majd zárjunk be és nyissa meg újra a Application Insights alkalmazás paneljét.
* A [naplózás bekapcsolásához](#debug-data-from-the-sdk) `<SDKLogger />` vegyen fel egy elemet a legfelső csomópont alatt a ApplicationInsights.xml fájlban (a projekt erőforrások mappájába), és keresse meg az AI: info/Warning/Error utasításban található bejegyzéseket a gyanús naplókhoz. 
* Győződjön meg arról, hogy a Java SDK sikeresen betöltötte a helyes ApplicationInsights.xml fájlt, a "konfigurációs fájl sikeresen megtalálva" utasításban megtekintve a konzol kimeneti üzeneteit.
* Ha a konfigurációs fájl nem található, tekintse meg a kimeneti üzeneteket, ahol megtekintheti a konfigurációs fájl keresésének helyét, és győződjön meg arról, hogy a ApplicationInsights.xml az egyik keresési helyen található. Az általános szabály, hogy a konfigurációs fájlt a Application Insights SDK-tégelyek közelében helyezheti el. Például: a Tomcat-ben ez a WEB-INF/classs mappát jelenti. A fejlesztés során elhelyezheti ApplicationInsights.xml a webes projekt erőforrások mappájába.
* Tekintse meg a [GitHub-problémák oldalát](https://github.com/microsoft/ApplicationInsights-Java/issues) is, amely az SDK ismert problémáit tartalmazza.
* A verziók ütközésével kapcsolatos problémák elkerülése érdekében győződjön meg arról, hogy az Application Insights Core, web, Agent és naplózási hozzáfűzése azonos verzióját használja.

#### <a name="i-used-to-see-data-but-it-has-stopped"></a>Felhasználtam az adatmegjelenítést, de leállt
* Megtalálta az adatpontok havi kvótáját? A beállítások/kvóta és díjszabás megnyitásával tájékozódhat. Ha igen, lehetősége van a csomag frissítésére, vagy a további kapacitás megfizetésére. Tekintse meg a [díjszabási sémát](https://azure.microsoft.com/pricing/details/application-insights/).
* Nemrég frissítette az SDK-t? Győződjön meg arról, hogy csak egyedi SDK-tégelyek jelennek meg a projekt címtárában. Az SDK-nak nem lehet két különböző verziója.
* A megfelelő AI-erőforrást keresi? Adja meg az alkalmazás Rendszerállapotkulcsot az erőforráshoz, ahol a telemetria vár. Azonosnak kell lenniük.

#### <a name="i-dont-see-all-the-data-im-expecting"></a>Nem látom az összes várt adatértéket
* Nyissa meg a használati és becsült költségek lapot, és győződjön meg arról, hogy a [mintavételezés](./sampling.md) működik-e. (a 100%-os átviteli érték azt jelenti, hogy a mintavételezés nem működik.) A Application Insights szolgáltatás beállítható úgy, hogy csak az alkalmazásból érkező telemetria egy részét fogadja el. Ez segít megőrizni a telemetria havi kvótáját.
* Be van kapcsolva az SDK-mintavétel? Ha igen, az adat mintavétele az összes vonatkozó típushoz megadott sebességgel történik.
* A Java SDK egy régebbi verzióját futtatja? Az 2.0.1-es verziótól kezdődően a hibatűrési mechanizmust bevezetjük az időszakos hálózati és háttérbeli hibák kezelésére, valamint az adatmegőrzésre a helyi meghajtókon.
* A túlságosan nagy telemetria miatt szabályozásra kerül? Ha bekapcsolja az információ-naplózást, megjelenik egy "alkalmazás szabályozása" üzenet. Aktuális korlátunk a 32k telemetria-elemek/másodperc.

### <a name="java-agent-cannot-capture-dependency-data"></a>A Java-ügynök nem tudja rögzíteni a függőségi adatmennyiséget
* Konfigurálta a Java-ügynököt a [Java-ügynök konfigurálása](java-agent.md) után?
* Győződjön meg arról, hogy a Java Agent jar és a AI-Agent.xml fájl is ugyanabba a mappába kerül.
* Győződjön meg arról, hogy az automatikus gyűjtéshez szükséges függőséget az automatikus gyűjtemény támogatja. Jelenleg csak a MySQL, az MsSQL, a Oracle DB és az Azure cache támogatott a Redis függőségi gyűjteményhez.

## <a name="no-usage-data"></a>Nincsenek használati adatok
**Megjelennek a kérelmekkel és a válaszadási időpontokkal kapcsolatos információk, de nem találhatók meg az oldal nézet, a böngésző vagy a felhasználói információ.**

Sikeresen beállította az alkalmazást, hogy telemetria küldjön a kiszolgálóról. A következő lépés a [weblapok beállítása a telemetria a webböngészőből való elküldéséhez][usage].

Azt is megteheti, hogy ha az ügyfél egy [telefonon vagy más eszközön][platforms]lévő alkalmazás, telemetria is küldhet.

Használja ugyanazt a kialakítási kulcsot az ügyfél és a kiszolgáló telemetria beállításához. Az adatok ugyanabban a Application Insights erőforrásban jelennek meg, és az ügyfél és a kiszolgáló eseményei is összekapcsolhatók.

## <a name="disabling-telemetry"></a>Telemetria letiltása
**Hogyan lehet letiltani a telemetria-gyűjteményt?**

A kódban:

```Java

    TelemetryConfiguration config = TelemetryConfiguration.getActive();
    config.setTrackingIsDisabled(true);
```

**Vagy**

Frissítse ApplicationInsights.xml (a projekt erőforrások mappájából). Adja hozzá a következőt a gyökérszintű csomóponthoz:

```XML

    <DisableTelemetry>true</DisableTelemetry>
```

Az XML-metódus használatával újra kell indítania az alkalmazást az érték módosításakor.

## <a name="changing-the-target"></a>A cél módosítása
**Hogyan változtathatom meg, hogy a projekt melyik Azure-erőforráshoz küld adatokat?**

* [Szerezze be az új erőforrás rendszerállapot-kulcsát.][java]
* Ha a Azure Toolkit for Eclipse használatával adta hozzá Application Insights a projekthez, kattintson a jobb gombbal a webes projektre, válassza az **Azure**-t, **konfigurálja a Application Insights**, és módosítsa a kulcsot.
* Ha a kialakítási kulcsot környezeti változóként konfigurálta, frissítse a környezeti változó értékét új Rendszerállapotkulcsot.
* Ellenkező esetben frissítse a kulcsot ApplicationInsights.xml a projekt erőforrások mappájába.

## <a name="debug-data-from-the-sdk"></a>Adatok hibakeresése az SDK-ból

**Honnan tudhatom meg, hogy mit csinál az SDK?**

Ha többet szeretne megtudni arról, hogy mi történik az API-ban, adja hozzá a `<SDKLogger/>` ApplicationInsights.xml konfigurációs fájljának gyökérszintű csomópontját.

### <a name="applicationinsightsxml"></a>ApplicationInsights.xml

Azt is utasíthatja, hogy a naplózó fájlba írja a kimenetet:

```XML
  <SDKLogger type="FILE"><!-- or "CONSOLE" to print to stderr -->
    <Level>TRACE</Level>
    <UniquePrefix>AI</UniquePrefix>
    <BaseFolderPath>C:/agent/AISDK</BaseFolderPath>
</SDKLogger>
```

### <a name="spring-boot-starter"></a>Spring boot Starter

Ha az SDK-naplózást a Spring boot-alkalmazásokkal szeretné engedélyezni a Application Insights Spring boot Starter használatával, adja hozzá a következőt a `application.properties` fájlhoz:

```yaml
azure.application-insights.logger.type=file
azure.application-insights.logger.base-folder-path=C:/agent/AISDK
azure.application-insights.logger.level=trace
```

vagy a standard hibába való nyomtatáshoz:

```yaml
azure.application-insights.logger.type=console
azure.application-insights.logger.level=trace
```

### <a name="java-agent"></a>Java ügynök

A JVM-ügynök naplózásának engedélyezéséhez frissítse a [AI-Agent.xml fájlt](java-agent.md):

```xml
<AgentLogger type="FILE"><!-- or "CONSOLE" to print to stderr -->
    <Level>TRACE</Level>
    <UniquePrefix>AI</UniquePrefix>
    <BaseFolderPath>C:/agent/AIAGENT</BaseFolderPath>
</AgentLogger>
```

### <a name="java-command-line-properties"></a>Java parancssori tulajdonságok
_A verzió 2.4.0 óta_

A naplózás engedélyezése parancssori kapcsolók használatával a konfigurációs fájlok módosítása nélkül:

```
java -Dapplicationinsights.logger.file.level=trace -Dapplicationinsights.logger.file.uniquePrefix=AI -Dapplicationinsights.logger.baseFolderPath="C:/my/log/dir" -jar MyApp.jar
```

vagy a standard hibába való nyomtatáshoz:

```
java -Dapplicationinsights.logger.console.level=trace -jar MyApp.jar
```

## <a name="the-azure-start-screen"></a>Az Azure kezdőképernyő
**Megtekintem [a Azure Portal](https://portal.azure.com). A Térkép az alkalmazásról szól?**

Nem, az Azure-kiszolgálók állapotát mutatja az egész világon.

*Az Azure-beli kezdőképernyőn (kezdőképernyő) Hogyan kereshetek meg az alkalmazással kapcsolatos adatok?*

Feltételezve, hogy az [alkalmazást a Application Insightshoz állítja be][java], kattintson a Tallózás gombra, válassza a Application Insights lehetőséget, majd válassza ki az alkalmazáshoz létrehozott alkalmazás-erőforrást. A jövőben a közeljövőben rögzítheti az alkalmazást az üzenőfalon.

## <a name="intranet-servers"></a>Intranetes kiszolgálók
**Figyelhető egy kiszolgáló az intraneten?**

Igen, ha a kiszolgáló telemetria tud küldeni a Application Insights portálra a nyilvános interneten keresztül.

Előfordulhat, hogy [meg kell nyitnia néhány kimenő portot a kiszolgálója tűzfalán](./ip-addresses.md#outgoing-ports) , hogy az SDK adatküldést küldjön a portálra.

## <a name="data-retention"></a>Adatmegőrzés
**Mennyi ideig tartanak a portálon tárolt adat? Biztonságos?**

Tekintse meg [az adatmegőrzést és az adatvédelmet][data].

## <a name="debug-logging"></a>Hibakeresési naplózás
Application Insights használja `org.apache.http` . Ez a névtér alatt Application Insights Core tégelyben található `com.microsoft.applicationinsights.core.dependencies.http` . Ez lehetővé teszi, hogy a Application Insights olyan forgatókönyveket kezeljen, amelyekben ugyanazok a különböző verziói találhatók `org.apache.http` meg egy kódon belül.

>[!NOTE]
>Ha engedélyezi a HIBAKERESÉSi szintű naplózást az alkalmazás összes névterében, a rendszer az összes végrehajtó modult tiszteletben tartja, beleértve a `org.apache.http` nevet is `com.microsoft.applicationinsights.core.dependencies.http` . A Application Insights nem fogja tudni alkalmazni ezeket a hívásokat, mert az Apache Library a napló hívását végzi. A HIBAKERESÉSi szint naplózása jelentős mennyiségű naplófájlt eredményez, és nem ajánlott éles üzemi példányok esetén.

## <a name="next-steps"></a>Következő lépések
**Beállítottam Application Insights a Java Server-alkalmazáshoz. Mit tehetek?**

* [Weblapok rendelkezésre állásának monitorozása][availability]
* [Weblap használatának figyelése][usage]
* [A használat követése és a problémák diagnosztizálása az eszköz alkalmazásaiban][platforms]
* [Kód írása az alkalmazás használatának nyomon követéséhez][track]
* [Diagnosztikai naplók rögzítése][javalogs]

## <a name="get-help"></a>Segítség kérése
* [Stack Overflow](https://stackoverflow.com/questions/tagged/ms-application-insights)
* [Probléma fájl a GitHubon](https://github.com/microsoft/ApplicationInsights-Java/issues)

<!--Link references-->

[availability]: ./monitor-web-app-availability.md
[data]: ./data-retention-privacy.md
[java]: java-get-started.md
[javalogs]: java-trace-logs.md
[platforms]: ./platforms.md
[track]: ./api-custom-events-metrics.md
[usage]: javascript.md

