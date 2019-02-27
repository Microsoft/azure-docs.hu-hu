---
title: Függőségi nyomkövetés az Azure Application Insights |} A Microsoft Docs
description: Az Application Insights segítségével elemezheti a használati adatokat, a rendelkezésre állást és a teljesítményt a helyszíni vagy Microsoft Azure webalkalmazásán.
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.assetid: d15c4ca8-4c1a-47ab-a03d-c322b4bb2a9e
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: mbullwin
ms.openlocfilehash: 4aa18ae791e5fa573eae76d5bdb9c45b9311e6b5
ms.sourcegitcommit: 24906eb0a6621dfa470cb052a800c4d4fae02787
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/27/2019
ms.locfileid: "56888083"
---
# <a name="set-up-application-insights-dependency-tracking"></a>Application Insights beállítása: Függőségi nyomkövetés
A *függőségi* egy külső összetevő, amely hívja meg az alkalmazást. Fontos általában nevű HTTP-n vagy egy adatbázist vagy egy fájlrendszert használó szolgáltatás. [Az Application Insights](../../azure-monitor/app/app-insights-overview.md) méri a mennyi az alkalmazás megvárja, amíg függőségeket, és milyen gyakran egy függőségi hívás sikertelen lesz. Vizsgálja meg az adott hívásokat, és összekapcsolhatja őket a kérelmeket és kivételeket.

A-a-beépített függőségi figyelő jelenleg az ilyen típusú függőségek hívásainak jelentések:

* Kiszolgáló
  * SQL Database-adatbázisok
  * ASP.NET webes és WCF-szolgáltatások, amelyek a HTTP-alapú kötések használata
  * A helyi vagy távoli HTTP-hívások
  * Az Azure Cosmos DB, tábla, a blob storage és queue 
* Weblapok
  * AJAX-hívások

Works figyelése használatával [bájt kód instrumentation](https://msdn.microsoft.com/library/z9z62c29.aspx) körülbelül módszerek kiválasztása vagy DiagnosticSource visszahívások (a legújabb .NET SDK-k), a .NET-keretrendszer alapján. Teljesítménybeli terhelést okoz a minimális.

Saját SDK-hívásokat figyelése egyéb függőségeket, mind az ügyfél és kiszolgáló kódban is kiírhatja használatával a [TrackDependency API](../../azure-monitor/app/api-custom-events-metrics.md#trackdependency).

> [!NOTE]
> Az Azure Cosmos DB automatikusan rögzíti csak akkor, ha [HTTP/HTTPS](../../cosmos-db/performance-tips.md#networking) szolgál. Az Application Insights nem rögzíti a TCP-módot.

## <a name="set-up-dependency-monitoring"></a>Állítsa be a függőség figyelése
Részleges függőségi gyűjtött automatikusan a [Application Insights SDK](asp-net.md). Mindazok az adatok lekéréséhez a megfelelő ügynököt, a gazdagép-kiszolgáló telepítéséhez.

| Platform | Telepítés |
| --- | --- |
| Az IIS-kiszolgálón |Mindkét [a kiszolgálón telepítse az Állapotfigyelőt](../../azure-monitor/app/monitor-performance-live-website-now.md) vagy [frissíteni az alkalmazást a .NET-keretrendszer 4.6-os vagy újabb](https://go.microsoft.com/fwlink/?LinkId=528259) , és telepítse a [Application Insights SDK](asp-net.md) az alkalmazásban. |
| Azure Web App |A WebApp Vezérlőpultját, a [nyissa meg az Application Insights paneljén a WebApp Vezérlőpultját](../../azure-monitor/app/azure-web-apps.md) , és válassza ki a telepítés, ha a rendszer kéri. |
| Azure Cloud Service |[Használat indítási feladat](../../azure-monitor/app/cloudservices.md) vagy [telepítse a .NET-keretrendszer 4.6 +](../../cloud-services/cloud-services-dotnet-install-dotnet.md) |

## <a name="where-to-find-dependency-data"></a>Hol található a függőségi adatokat
* [Alkalmazás-hozzárendelés](#application-map) megjeleníti az alkalmazás és a szomszédos összetevők közötti függőségek.
* [Teljesítmény, a böngésző és a hiba panelek](#performance-and-failure-blades) kiszolgáló függőségi adatok megjelenítése.
* [Böngészők panelen](#ajax-calls) jeleníti meg a felhasználók böngészőinek AJAX-hívások.
* Kattintson végig ellenőrizze azok függőségi hívásaihoz lassú vagy hibás kérésekből származó.
* [Analytics](#analytics) használható függőségi adatokat lekérdezni.

## <a name="application-map"></a>Alkalmazástérkép
Alkalmazás-hozzárendelés egy Vizualizáció támogatására, az alkalmazás összetevői közötti függőségek felderítése funkcionál. Az alkalmazásból a telemetriai adatokból automatikusan hozza létre. Ez a példa bemutatja a böngésző szkripteket az AJAX-hívások és REST-hívások a server App két külső.

![Alkalmazástérkép](./media/asp-net-dependencies/08.png)

* **Keresse meg a mezőkben** kapcsolódó függőségi és más típusú diagramok.
* **A térkép rögzítése** , a [irányítópult](../../azure-monitor/app/app-insights-dashboards.md), ahol ki teljesen működőképes lesz.

[További információk](../../azure-monitor/app/app-map.md).

## <a name="performance-and-failure-blades"></a>Teljesítmény és a sikertelen paneljein
A teljesítmény panel tartalmazza a server app függőségi hívások időtartama. Van egy összegző diagram és a egy hívás alapján szegmentált táblázatot.

![Teljesítmény panelen függőségi diagramjain](./media/asp-net-dependencies/dependencies-in-performance-blade.png)

Kattintson az összegző diagramok vagy a tábla elemek hívásokat a nyers előfordulását.

![Függőségi hívás példányok](./media/asp-net-dependencies/dependency-call-instance.png)

**Hibák száma** jelennek meg a **hibák** panelen. Hiba a visszatérési kód, amely nem szerepel a tartomány 200-399, vagy ismeretlen.

> [!NOTE]
> **100 %-os hiba?** – Ez valószínűleg azt jelzi, hogy csak azért kapja a részleges függőségi adatokat. Kell [függőségi figyelés beállítása a platformjának megfelelő](#set-up-dependency-monitoring).
>
>

## <a name="ajax-calls"></a>AJAX-hívások
A böngészők panelen az AJAX-hívások időtartama és sikertelen arányát jeleníti meg [JavaScript a weboldalakon](../../azure-monitor/app/javascript.md). Ezek függőségekként jelennek meg.

## <a name="diagnosis"></a> Lassú kérelmek diagnosztizálása
Minden egyes kérés esemény társítva a függőségi hívások, kivételeket és eseményeket, amelyek a rendszer nyomon követi, amíg az alkalmazás a kérés feldolgozása folyamatban van. Így az egyes kérések rosszul hajt végre, ha annak, hogy van-e lassú válaszai a függőség miatt.

Nézzük meg, amely egy példát.

### <a name="tracing-from-requests-to-dependencies"></a>A kérelmek, a függőségek nyomon követése
A teljesítmény panel megnyitásához, és tekintse meg a rács kérések:

![Kéréseit átlagok és száma](./media/asp-net-dependencies/02-reqs.png)

A felső egy a nagyon hosszú időt vesz igénybe. Nézzük meg, ha azt is megtudhatja, hol a telik el.

Kattintson az egyéni lekérési események megtekintéséhez a sorhoz:

![A kérelem események listája](./media/asp-net-dependencies/03-instances.png)

Kattintson a tovább vizsgálja meg, és görgessen le a távoli függőségi hívás ehhez a kérelemhez kapcsolódó bármely hosszú ideig futó példány:

![Távoli függőségekhez intézett hívások keresse meg, azonosíthatja a szokatlan időtartama](./media/asp-net-dependencies/04-dependencies.png)

A kérelem egy hívás egy helyi service telt karbantartási idő a legtöbb tűnik.

További információért a sorhoz kiválasztása:

![Kattintson végig a távoli függőség nem sokkal azonosításához](./media/asp-net-dependencies/05-detail.png)

Úgy tűnik, ez az, hogy a probléma. Mi a probléma már pinpointed, tehát most sínen szeretné tudni, miért Ez a meghívás tart sokáig.

### <a name="request-timeline"></a>Kérelem ütemterv
Más esetben nem nincs függőségi hívás, amely túl hosszú. De között idősor nézetének, láthatjuk, ahol a késés a belső feldolgozási történt:

![Távoli függőségekhez intézett hívások keresse meg, azonosíthatja a szokatlan időtartama](./media/asp-net-dependencies/04-1.png)

Úgy tűnik, hogy nagy eseményáramlási kimaradást követően az első függőség hívása, így a kódban, hogy miért ez megnézzük kell.

### <a name="profile-your-live-site"></a>Az élő webhelyet profil

Nem tudja, ahol az idő halad? A [Application Insights profiler](../../azure-monitor/app/profiler.md) nyomkövetések HTTP meghívja a élő webhelyre, és megjeleníti a funkciókról a kódban a leghosszabb időt vett igénybe.

## <a name="failed-requests"></a>Sikertelen kérelmek
Sikertelen kérelmek is lehet társítva, a függőségek hívásainak sikertelen. Ismét hogy végigkattinthat nyomon követheti a problémát.

![Kattintson a sikertelen kérelmeit tartalmazó diagram](./media/asp-net-dependencies/06-fail.png)

Kattintson végig a sikertelen kérelmek előfordulása, és tekintse meg a kapcsolódó eseményeket.

![Kattintson egy kérés típusa, a másik nézet ugyanazon-példány beolvasásához, kattintson rá a kivétel részletes-példány.](./media/asp-net-dependencies/07-faildetail.png)

## <a name="analytics"></a>Elemzés
Nyomon követheti a függőségeket a [Kusto-lekérdezés nyelvi](/azure/kusto/query/). Néhány példa:

* Keresse meg az összes sikertelen függőségi hívások:

```

    dependencies | where success != "True" | take 10
```

* Keresse meg az AJAX-hívások:

```

    dependencies | where client_Type == "Browser" | take 10
```

* Függőségi hívások kérelmekhez keresése:

```

    dependencies
    | where timestamp > ago(1d) and  client_Type != "Browser"
    | join (requests | where timestamp > ago(1d))
      on operation_Id  
```


* Keresés AJAX-hívások lapmegtekintés társított:

```

    dependencies
    | where timestamp > ago(1d) and  client_Type == "Browser"
    | join (browserTimings | where timestamp > ago(1d))
      on operation_Id
```



## <a name="custom-dependency-tracking"></a>Egyéni függőségi nyomkövetés
A standard szintű függőség-követési modul automatikusan felderíti a külső függőségei, például adatbázisok és a REST API-k. Azonban szükség lehet néhány további összetevők azonos módon kell kezelni.

Is írhat kódot a függőségi adatokat küldő azonos [TrackDependency API](../../azure-monitor/app/api-custom-events-metrics.md#trackdependency) , amely a globális modulok használják.

Ha a kódot készíthet, amelyeket Ön nem írt szerelvény, például sikerült, minden hívás ismerje meg, milyen közreműködői adatbeolvasási a válaszidők az időt. Ahhoz, hogy ezek az adatok az Application Insights függőségi diagramjain jelennek meg, küldje el használatával `TrackDependency`.

```csharp

            var startTime = DateTime.UtcNow;
            var timer = System.Diagnostics.Stopwatch.StartNew();
            try
            {
                success = dependency.Call();
            }
            finally
            {
                timer.Stop();
                telemetry.TrackDependency("myDependency", "myCall", startTime, timer.Elapsed, success);
                // The call above has been made obsolete in the latest SDK. The updated call follows this format:
                // TrackDependency (string dependencyTypeName, string dependencyName, string data, DateTimeOffset startTime, TimeSpan duration, bool success);
            }
```

Ha azt szeretné, hogy kikapcsolja a standard szintű függőségi követési modul, távolítsa el a hivatkozást a DependencyTrackingTelemetryModule [ApplicationInsights.config](../../azure-monitor/app/configuration-with-applicationinsights-config.md).

## <a name="troubleshooting"></a>Hibaelhárítás
*Függőségi sikeres mindig jelző IGAZ vagy hamis értéket jeleníti meg.*

*Nem jelenik meg a teljes SQL-lekérdezést.*

Tekintse meg az alábbi táblázat, és úgy döntött, hogy a helyes konfiguráció engedélyezése az alkalmazás-függőségfigyelés biztosítására.

| Platform | Telepítés |
| --- | --- |
| Az IIS-kiszolgálón |Mindkét [a kiszolgálón telepítse az Állapotfigyelőt](../../azure-monitor/app/monitor-performance-live-website-now.md). Vagy [frissíteni az alkalmazást a .NET-keretrendszer 4.6-os vagy újabb](https://go.microsoft.com/fwlink/?LinkId=528259) , és telepítse a [Application Insights SDK](asp-net.md) az alkalmazásban. |
| Az IIS Express |Használja helyette az IIS-kiszolgálón. |
| Azure Web App |A WebApp Vezérlőpultját, a [nyissa meg az Application Insights paneljén a WebApp Vezérlőpultját](../../azure-monitor/app/azure-web-apps.md) , és válassza ki a telepítés, ha a rendszer kéri. |
| Azure-felhőszolgáltatás |[Használat indítási feladat](../../azure-monitor/app/cloudservices.md) vagy [telepítse a .NET-keretrendszer 4.6 +](../../cloud-services/cloud-services-dotnet-install-dotnet.md). |

## <a name="video"></a>Videó

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/112/player]

## <a name="next-steps"></a>További lépések
* [Kivételek](../../azure-monitor/app/asp-net-exceptions.md)
* [Felhasználók és Lapadatok](../../azure-monitor/app/javascript.md)
* [Rendelkezésre állás](../../azure-monitor/app/monitor-web-app-availability.md)
