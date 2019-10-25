---
title: Az Azure Application Insights telemetria szűrése a Java-webalkalmazásban | Microsoft Docs
description: Csökkentse a telemetria forgalmat úgy, hogy kiszűri azokat az eseményeket, amelyeket nem kell figyelnie.
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 3/14/2019
ms.openlocfilehash: de2a7c73b87254a6fd2e6c5dc942a9c93d28c2d4
ms.sourcegitcommit: 8e271271cd8c1434b4254862ef96f52a5a9567fb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/23/2019
ms.locfileid: "72819359"
---
# <a name="filter-telemetry-in-your-java-web-app"></a>Telemetria szűrése a Java-webalkalmazásban

A szűrők segítségével kiválaszthatja azt a telemetria, amelyet a [Java-webalkalmazás küld Application Insights](java-get-started.md). Vannak olyan beépített szűrők, amelyeket használhat, és saját egyéni szűrőket is írhat.

A beépített szűrők a következők:

* Nyomkövetés súlyossági szintje
* Konkrét URL-címek, kulcsszavak vagy válaszok kódjai
* Gyors válaszok – ez a kérelmek, amelyekhez az alkalmazás gyorsan reagált
* Adott események nevei

> [!NOTE]
> A szűrők elferdítik az alkalmazás metrikáit. Dönthet például úgy, hogy a lassú válaszok diagnosztizálásához beállít egy szűrőt a gyors válaszadási idő elvetéséhez. Azonban tisztában kell lennie azzal, hogy Application Insights által jelentett átlagos válaszidő a valós fordulatszámnál lassabb lesz, és a kérések száma kisebb lesz, mint a tényleges szám.
> Ha ez problémát jelent, használja inkább a [mintavételezést](../../azure-monitor/app/sampling.md) .

## <a name="setting-filters"></a>Szűrők beállítása

A ApplicationInsights. xml fájlban adjon hozzá egy `TelemetryProcessors` szakaszt a következő példához hasonlóan:


```XML

    <ApplicationInsights>
      <TelemetryProcessors>

        <BuiltInProcessors>
           <Processor type="TraceTelemetryFilter">
                  <Add name="FromSeverityLevel" value="ERROR"/>
           </Processor>

           <Processor type="RequestTelemetryFilter">
                  <Add name="MinimumDurationInMS" value="100"/>
                  <Add name="NotNeededResponseCodes" value="200-400"/>
           </Processor>

           <Processor type="PageViewTelemetryFilter">
                  <Add name="DurationThresholdInMS" value="100"/>
                  <Add name="NotNeededNames" value="home,index"/>
                  <Add name="NotNeededUrls" value=".jpg,.css"/>
           </Processor>

           <Processor type="TelemetryEventFilter">
                  <!-- Names of events we don't want to see -->
                  <Add name="NotNeededNames" value="Start,Stop,Pause"/>
           </Processor>

           <!-- Exclude telemetry from availability tests and bots -->
           <Processor type="SyntheticSourceFilter">
                <!-- Optional: specify which synthetic sources,
                     comma-separated
                     - default is all synthetics -->
                <Add name="NotNeededSources" value="Application Insights Availability Monitoring,BingPreview"
           </Processor>

        </BuiltInProcessors>

        <CustomProcessors>
          <Processor type="com.fabrikam.MyFilter">
            <Add name="Successful" value="false"/>
          </Processor>
        </CustomProcessors>

      </TelemetryProcessors>
    </ApplicationInsights>

```




[Vizsgálja meg a beépített processzorok teljes készletét](https://github.com/Microsoft/ApplicationInsights-Java/tree/master/core/src/main/java/com/microsoft/applicationinsights/internal/processor).

## <a name="built-in-filters"></a>Beépített szűrők

### <a name="metric-telemetry-filter"></a>Metrikus telemetria szűrő

```XML

           <Processor type="MetricTelemetryFilter">
                  <Add name="NotNeeded" value="metric1,metric2"/>
           </Processor>
```

* `NotNeeded` – az egyéni metrikák neveinek vesszővel tagolt listája.


### <a name="page-view-telemetry-filter"></a>Oldal nézet telemetria szűrője

```XML

           <Processor type="PageViewTelemetryFilter">
                  <Add name="DurationThresholdInMS" value="500"/>
                  <Add name="NotNeededNames" value="page1,page2"/>
                  <Add name="NotNeededUrls" value="url1,url2"/>
           </Processor>
```

* `DurationThresholdInMS` – az időtartam a lap betöltéséhez szükséges időt jelenti. Ha ez a beállítás be van állítva, a rendszer nem adja meg az adott időpontnál gyorsabban betöltött lapokat.
* `NotNeededNames` – a lapok neveinek vesszővel tagolt listája.
* `NotNeededUrls` – URL-töredékek vesszővel tagolt listája. `"home"` például kiszűri az összes olyan oldalt, amely az URL-címben "Home".


### <a name="request-telemetry-filter"></a>Telemetria-szűrő kérése


```XML

           <Processor type="RequestTelemetryFilter">
                  <Add name="MinimumDurationInMS" value="500"/>
                  <Add name="NotNeededResponseCodes" value="page1,page2"/>
                  <Add name="NotNeededUrls" value="url1,url2"/>
           </Processor>
```



### <a name="synthetic-source-filter"></a>Szintetikus Forrás szűrő

Kiszűri az összes olyan telemetria, amely értékeket tartalmaz a SyntheticSource tulajdonságban. Ilyenek például a botoktól, a pókoktól és a rendelkezésre állási tesztektől érkező kérések.

A telemetria kiszűrése az összes szintetikus kérelem esetében:


```XML

           <Processor type="SyntheticSourceFilter" />
```

Kiszűri az adott szintetikus források telemetria:


```XML

           <Processor type="SyntheticSourceFilter" >
                  <Add name="NotNeeded" value="source1,source2"/>
           </Processor>
```

* `NotNeeded` – szintetikus források neveinek vesszővel tagolt listája.

### <a name="telemetry-event-filter"></a>Telemetria-esemény szűrője

Egyéni események szűrése (a [TrackEvent ()](../../azure-monitor/app/api-custom-events-metrics.md#trackevent)használatával naplózva).


```XML

           <Processor type="TelemetryEventFilter" >
                  <Add name="NotNeededNames" value="event1, event2"/>
           </Processor>
```


* `NotNeededNames` – az események neveinek vesszővel tagolt listája.


### <a name="trace-telemetry-filter"></a>Nyomkövetési telemetria szűrője

Szűri a naplók nyomkövetéseit (a [TrackTrace ()](../../azure-monitor/app/api-custom-events-metrics.md#tracktrace) vagy egy [naplózási keretrendszer-gyűjtővel](java-trace-logs.md)naplózva).

```XML

           <Processor type="TraceTelemetryFilter">
                  <Add name="FromSeverityLevel" value="ERROR"/>
           </Processor>
```

* `FromSeverityLevel` érvényes értékek a következők:
  *  Az összes nyomkövetés kiszűrése
  *  Nyomkövetés – nincs szűrés. a nyomkövetési szinttel egyenlő
  *  INFORMÁCIÓ – kiszűrési NYOMKÖVETÉSi szint
  *  Figyelmeztetés – kiszűrheti a NYOMKÖVETÉSt és az adatokat
  *  HIBA – figyelmeztetés, információ, nyomkövetés kiszűrése
  *  KRITIKUS – az összes kritikus kiszűrése


## <a name="custom-filters"></a>Egyéni szűrők

### <a name="1-code-your-filter"></a>1. a szűrő kódja

A kódban hozzon létre egy olyan osztályt, amely megvalósítja `TelemetryProcessor`:

```Java

    package com.fabrikam.MyFilter;
    import com.microsoft.applicationinsights.extensibility.TelemetryProcessor;
    import com.microsoft.applicationinsights.telemetry.Telemetry;

    public class SuccessFilter implements TelemetryProcessor {

       /* Any parameters that are required to support the filter.*/
       private final String successful;

       /* Initializers for the parameters, named "setParameterName" */
       public void setNotNeeded(String successful)
       {
          this.successful = successful;
       }

       /* This method is called for each item of telemetry to be sent.
          Return false to discard it.
          Return true to allow other processors to inspect it. */
       @Override
       public boolean process(Telemetry telemetry) {
        if (telemetry == null) { return true; }
        if (telemetry instanceof RequestTelemetry)
        {
            RequestTelemetry requestTelemetry = (RequestTelemetry)telemetry;
            return request.getSuccess() == successful;
        }
        return true;
       }
    }

```


### <a name="2-invoke-your-filter-in-the-configuration-file"></a>2. a szűrő meghívása a konfigurációs fájlban

A ApplicationInsights. xml fájlban:

```XML


    <ApplicationInsights>
      <TelemetryProcessors>
        <CustomProcessors>
          <Processor type="com.fabrikam.SuccessFilter">
            <Add name="Successful" value="false"/>
          </Processor>
        </CustomProcessors>
      </TelemetryProcessors>
    </ApplicationInsights>

```

### <a name="3-invoke-your-filter-java-spring"></a>3. a szűrő meghívása (Java Spring)

A Spring Framework-alapú alkalmazások esetében az egyéni telemetria processzorokat Bean-ként kell regisztrálni a fő alkalmazás osztályban. Ezt követően automatikusan drótba kerülnek, amikor az alkalmazás elindul.

```Java
@Bean
public TelemetryProcessor successFilter() {
      return new SuccessFilter();
}
```

Létre kell hoznia a saját szűrő paramétereit a `application.properties`ban, és a Spring boot külső konfigurációs keretrendszerét kell használnia, hogy átadja ezeket a paramétereket az egyéni szűrőnek. 


## <a name="troubleshooting"></a>Hibakeresés

*A szűrő nem működik.*

* Győződjön meg arról, hogy érvényes paraméter-értékeket adott meg. Például az időtartamoknak egész számnak kell lenniük. Az értékek érvénytelenek, mert a szűrő figyelmen kívül lesz hagyva. Ha az egyéni szűrő kivételt okoz a konstruktorból vagy a set metódusból, a rendszer figyelmen kívül hagyja.

## <a name="next-steps"></a>Következő lépések

* [Mintavételezés](../../azure-monitor/app/sampling.md) – vegye fontolóra a mintavételezést olyan Alternatív megoldásként, amely nem rontja a metrikákat.
