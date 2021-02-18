---
title: A Java-webalkalmazások teljesítményének monitorozása Linux rendszeren – Azure | Microsoft Docs
description: A Java-webhely bővített alkalmazás-teljesítményének figyelése a Application Insights gyűjtött beépülő modullal.
ms.topic: conceptual
ms.date: 03/14/2019
author: MS-jgol
ms.custom: devx-track-java
ms.author: jgol
ms.openlocfilehash: 08d8deb4c7769c4f8a202050d7b5515439d691f4
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/17/2021
ms.locfileid: "100573845"
---
# <a name="collectd-linux-performance-metrics-in-application-insights-deprecated"></a>begyűjtve: Linux teljesítmény-metrikák a Application Insights [elavult]

> [!IMPORTANT]
> A Java-alkalmazások figyelésének **ajánlott módszere** az automatikus kiépítés használata a kód módosítása nélkül. Kérjük, kövesse az **[Application Insights Java 3,0-ügynökre](./java-in-process-agent.md)** vonatkozó irányelveket.

A Linux rendszerbeli teljesítmény-mérőszámok megismeréséhez [Application Insights](./app-insights-overview.md)-ben telepítse a [begyűjtést](https://collectd.org/), valamint a Application Insights beépülő modullal. Ez a nyílt forráskódú megoldás különféle rendszerszintű és hálózati statisztikákat gyűjt.

A gyűjtést általában akkor fogja használni, ha [a Java-webszolgáltatását már Application Insights][java]-mel konfigurálta. Több adat nyújt segítséget az alkalmazás teljesítményének növeléséhez vagy a problémák diagnosztizálásához. 

## <a name="get-your-instrumentation-key"></a>A kialakítási kulcs beszerzése
A [Microsoft Azure Portal](https://portal.azure.com)nyissa meg azt a [Application Insights](./app-insights-overview.md) -erőforrást, amelyen meg szeretné jeleníteni az adott adatforrást. (Vagy [hozzon létre egy új erőforrást](./create-new-resource.md).)

Készítsen másolatot a kialakítási kulcsról, amely azonosítja az erőforrást.

![Böngésszen az összes, nyissa meg az erőforrást, majd az alapvető erőforrások legördülő menüben válassza ki és másolja a kialakítási kulcsot](./media/java-collectd/instrumentation-key-001.png)

## <a name="install-collectd-and-the-plug-in"></a>A begyűjtött és a beépülő modul telepítése
A Linux Server rendszerű gépeken:

1. Telepítse a [gyűjtött](https://collectd.org/) 5.4.0 vagy újabb verziót.
2. Töltse le a [Application Insights gyűjtött író beépülő modult](https://github.com/microsoft/ApplicationInsights-Java/tree/master/core/src/main/java/com/microsoft/applicationinsights/internal). Jegyezze fel a verziószámot.
3. Másolja a beépülő modult a-ba `/usr/share/collectd/java` .
4. Szerkesztés `/etc/collectd/collectd.conf` :
   * Győződjön meg arról, hogy [a Java beépülő modul](https://collectd.org/wiki/index.php/Plugin:Java) engedélyezve van.
   * Frissítse a Java. class. Path JVMArg, hogy az tartalmazza a következő JAR-t. Frissítse a verziószámot, hogy az megfeleljen a letöltöttnek:
   * `/usr/share/collectd/java/applicationinsights-collectd-1.0.5.jar`
   * Adja hozzá ezt a kódrészletet az erőforrás rendszerállapot-kulcsának használatával:

```XML

     LoadPlugin "com.microsoft.applicationinsights.collectd.ApplicationInsightsWriter"
     <Plugin ApplicationInsightsWriter>
        InstrumentationKey "Your key"
     </Plugin>
```

Itt egy minta konfigurációs fájl része:

```XML

    ...
    # collectd plugins
    LoadPlugin cpu
    LoadPlugin disk
    LoadPlugin load
    ...

    # Enable Java Plugin
    LoadPlugin "java"

    # Configure Java Plugin
    <Plugin "java">
      JVMArg "-verbose:jni"
      JVMArg "-Djava.class.path=/usr/share/collectd/java/applicationinsights-collectd-1.0.5.jar:/usr/share/collectd/java/collectd-api.jar"

      # Enabling Application Insights plugin
      LoadPlugin "com.microsoft.applicationinsights.collectd.ApplicationInsightsWriter"

      # Configuring Application Insights plugin
      <Plugin ApplicationInsightsWriter>
        InstrumentationKey "12345678-1234-1234-1234-123456781234"
      </Plugin>

      # Other plugin configurations ...
      ...
    </Plugin>
    ...
```

Konfiguráljon más [begyűjtött beépülő](https://collectd.org/wiki/index.php/Table_of_Plugins)modulokat, amelyek különböző forrásokból származó különböző adatokat gyűjthetnek.

Az újraindítást a [manuális](https://collectd.org/wiki/index.php/First_steps)alapján kell összegyűjteni.

## <a name="view-the-data-in-application-insights"></a>Application Insightsban lévő adatmegjelenítés
A Application Insights erőforrásban nyissa meg a [metrikákat, és adja hozzá a diagramokat, és][metrics]válassza ki az egyéni kategóriából megjeleníteni kívánt metrikákat.

Alapértelmezés szerint a metrikák összesítése minden olyan gazdagépen történik, amelyről a metrikák gyűjtése megtörtént. A metrikák egy gazdagépen való megtekintéséhez a diagram részletei panelen kapcsolja be a csoportosítást, majd válassza a csoport begyűjtve – gazdagép lehetőséget.

## <a name="to-exclude-upload-of-specific-statistics"></a>Adott statisztika feltöltésének kizárása
Alapértelmezés szerint a Application Insights beépülő modul az összes olyan adatot elküldi, amelyet az engedélyezett "READ" beépülő modulok gyűjtöttek össze. 

Adott beépülő modulok vagy adatforrások adatainak kizárása:

* Szerkessze a konfigurációs fájlt. 
* A-ben `<Plugin ApplicationInsightsWriter>` adja hozzá a következőhöz hasonló direktíva-sorokat:

| Irányelv | Hatás |
| --- | --- |
| `Exclude disk` |A beépülő modul által összegyűjtött összes adatok kizárása `disk` |
| `Exclude disk:read,write` |Zárja ki a nevű `read` és a `write` `disk` beépülő modulból származó forrásokat. |

Külön irányelvek egy sortöréssel.

## <a name="problems"></a>Problémákat tapasztal?
*Nem látok információt a portálon*

* Nyissa meg a [keresést][diagnostic] , és ellenőrizze, hogy a nyers események megérkeztek-e. Időnként hosszabb időt is igénybe kell venni a metrikák Explorerben.
* Előfordulhat, hogy [tűzfal-kivételeket kell beállítania a kimenő adatértékekhez](./ip-addresses.md) .
* A nyomkövetés engedélyezése a Application Insights beépülő modulban. A sor hozzáadása a következőn belül `<Plugin ApplicationInsightsWriter>` :
  * `SDKLogger true`
* Nyisson meg egy terminált, és kezdjen el részletes módban összegyűjteni, hogy megtekintse a jelentett problémákat:
  * `sudo collectd -f`

## <a name="known-issue"></a>Ismert probléma

Az Application Insights írási beépülő modul nem kompatibilis bizonyos olvasási beépülő modullal. Egyes beépülő modulok néha "NaN"-t küldenek, ahol a Application Insights beépülő modul egy lebegőpontos számot vár.

Tünet: a begyűjtött napló megjeleníti az "AI:... SyntaxError: váratlan token N ".

Megkerülő megoldás: a probléma az írási beépülő modulok által összegyűjtött adatokat nem zárja ki. 

<!--Link references-->

[api]: ./api-custom-events-metrics.md
[apiexceptions]: ./api-custom-events-metrics.md#track-exception
[availability]: ./monitor-web-app-availability.md
[diagnostic]: ./diagnostic-search.md
[eclipse]: app-insights-java-eclipse.md
[java]: java-get-started.md
[javalogs]: java-trace-logs.md
[metrics]: ../essentials/metrics-charts.md

