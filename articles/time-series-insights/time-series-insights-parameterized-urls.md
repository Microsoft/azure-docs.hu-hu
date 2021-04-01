---
title: Egyéni nézetek megosztása paraméteres URL-címekkel – Azure Time Series Insights | Microsoft Docs
description: Megtudhatja, hogyan hozhat létre paraméteres URL-címeket a Azure Time Series Insights testreszabott Explorer-nézeteinek egyszerű megosztásához.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.topic: conceptual
ms.workload: big-data
ms.date: 10/02/2020
ms.custom: seodec18
ms.openlocfilehash: 9bf857a66643b1e95ea2559601761a7217babad4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "91665327"
---
# <a name="share-a-custom-view-using-a-parameterized-url"></a>Egyéni nézet megosztása paraméteres URL-cím használatával

Az egyéni nézetek Azure Time Series Insights Explorerben való megosztásához programozott módon hozhatja létre az egyéni nézet paraméteres URL-címét.

Azure Time Series Insights Explorer támogatja az URL-lekérdezési paramétereket, amelyek közvetlenül az URL-címről határozzák meg a nézeteket. Például kizárólag az URL-cím használatával adhatja meg a célkörnyezetet, a keresési predikátumot és a kívánt időtartományt is. Amikor a felhasználó kiválasztja a testreszabott URL-címet, az illesztőfelület közvetlenül az adott eszközre mutató hivatkozást biztosít a Azure Time Series Insights portálon. Adathozzáférési szabályzatok érvényesek.

> [!TIP]
>
> * Tekintse meg az ingyenes [Azure Time Series Insights bemutatót](https://insights.timeseries.azure.com/samples).
> * Olvassa el a mellékelt [Azure Time Series Insights Explorer](./time-series-insights-explorer.md) dokumentációját.

## <a name="environment-id"></a>Környezetazonosító

A célkörnyezet azonosítóját az `environmentId=<guid>` paraméter adja meg. Ez az adatelérési teljes tartománynév összetevője, és a Azure Portal környezetének jobb felső sarkában található. Ez minden, ami megelőzi `env.timeseries.azure.com` .

Példa környezetazonosító paraméterre: `?environmentId=10000000-0000-0000-0000-100000000108`.

## <a name="time"></a>Idő

Paraméteres URL-cím esetében abszolút vagy relatív időértékeket is megadhat.

### <a name="absolute-time-values"></a>Abszolút időértékek

Abszolút időértékek megadásához használja a következő paramétereket: `from=<integer>` és `to=<integer>`.

* A `from=<integer>` egy olyan érték, amely JavaScript-ezredmásodpercben adja meg a keresés kezdési időpontját.
* A `to=<integer>` egy olyan érték, amely JavaScript-ezredmásodpercben adja meg a keresés befejezési időpontját.

> [!TIP]
> A dátumok JavaScript ezredmásodpercbe való egyszerű lefordításához próbálkozzon az [Epoch & Unix timestamp átalakítóval](https://www.freeformatter.com/epoch-timestamp-to-date-converter.html).

### <a name="relative-time-values"></a>Relatív időértékek

A relatív idő értékéhez használja a `relativeMillis=<value>` értéket, ahol az *érték* a JavaScript EZREDMÁSODPERCben az API-ból kapott legutóbbi időbélyegtől.

A `&relativeMillis=3600000` például a legutóbbi 60 perc adatait jeleníti meg.

Az elfogadott értékek a Azure Time Series Insights Explorer **gyors idő** menüjének felelnek meg, és a következők:

* `1800000` (Az elmúlt 30 perc)
* `3600000` (Utolsó 60 perc)
* `10800000` (Az elmúlt 3 óra)
* `21600000` (Az elmúlt 6 óra)
* `43200000` (Az elmúlt 12 óra)
* `86400000` (Az elmúlt 24 óra)
* `604800000` (Az elmúlt 7 nap)
* `2592000000` (Az elmúlt 30 óra)

### <a name="optional-parameters"></a>Választható paraméterek

A `timeSeriesDefinitions=<collection of term objects>` paraméter olyan predikátum-kifejezéseket határoz meg, amelyek egy Azure Time Series Insights nézetben fognak megjelenni:

| Paraméter | URL-tétel | Leírás |
| --- | --- | --- |
| **név** | `\<string>` | A *kifejezés* neve. |
| **splitBy** | `\<string>` | A *felosztás* alapjául szolgáló oszlop neve. |
| **measureName** | `\<string>` | A *mérték* oszlopneve. |
| **predikátum** | `\<string>` | A kiszolgálóoldali szűrés *where* záradéka. |
| **useSum** | `true` | Egy opcionális paraméter, amely a mérték összegének használatát határozza meg. |

> [!NOTE]
> Ha `Events` a a kiválasztott **useSum** mérték, a darabszám alapértelmezés szerint ki van választva.  
> Ha `Events` nincs bejelölve, az átlag alapértelmezés szerint ki van választva. |

* A `multiChartStack=<true/false>` kulcs-érték párok lehetővé teszik a diagramon belüli halmozást.
* A `multiChartSameScale=<true/false>` kulcs-érték pár lehetővé teszi, hogy ugyanaz az Y tengely skálája a feltételek között egy opcionális paraméteren belül legyen.  
* A `timeBucketUnit=<Unit>&timeBucketSize=<integer>` lehetővé teszi az intervallum csúszkájának módosítását, hogy a diagram részletesebb vagy simább legyen.  
* A `timezoneOffset=<integer>` paraméter lehetővé teszi, hogy beállítsa a diagram időzónáját, amelyet az UTC szerint kell megtekinteni.

| Pár (ok) | Leírás |
| --- | --- |
| `multiChartStack=false` | `true` Alapértelmezés szerint engedélyezve van, ezért adja át `false` a stack-nek. |
| `multiChartStack=false&multiChartSameScale=true` | A rétegezést engedélyeznie kell az azonos Y tengely irányú skálázás több feltételben való használatához.  Alapértelmezés szerint ez a `false` `true` funkció lehetővé teszi ezt a funkciót. |
| `timeBucketUnit=<Unit>&timeBucketSize=<integer>` | Egység = `days` , `hours` , `minutes` , `seconds` , `milliseconds` .  Az egység nevét mindig nagybetűvel írja. </br> Adja meg az egységek számát az **timeBucketSize** kívánt egész számának átadásával.  |
| `timezoneOffset=-<integer>` | Az egész számot mindig ezredmásodpercben kell megadnia. |

> [!NOTE]
> a **timeBucketUnit** értékeit akár 7 napig is elvégezheti.
> a **timezoneOffset** értékei sem UTC, sem helyi idő szerint nem használhatók.

### <a name="examples"></a>Példák

Ha idősorozat-definíciókat szeretne hozzáadni egy Azure Time Series Insights-környezethez URL-paraméterként, fűzze hozzá a következőt:

```URL parameter
&timeSeriesDefinitions=[{"name":"F1PressureId","splitBy":"Id","measureName":"Pressure","predicate":"'Factory1'"},{"name":"F2TempStation","splitBy":"Station","measureName":"Temperature","predicate":"'Factory2'"},
{"name":"F3VibrationPL","splitBy":"ProductionLine","measureName":"Vibration","predicate":"'Factory3'"}]
```

Az idősorozat-definíciók használata a következőhöz:

* A környezeti azonosító
* Az utolsó 60 perc
* A választható paramétereket tartalmazó feltételek (**F1PressureID**, **F2TempStation** és **F3VibrationPL**)

A nézethez a következő paraméteres URL-címet hozhatja létre:

```URL
https://insights.timeseries.azure.com/classic/samples?environmentId=10000000-0000-0000-0000-100000000108&relativeMillis=3600000&timeSeriesDefinitions=[{"name":"F1PressureId","splitBy":"Id","measureName":"Pressure","predicate":"'Factory1'"},{"name":"F2TempStation","splitBy":"Station","measureName":"Temperature","predicate":"'Factory2'"},{"name":"F3VibrationPL","splitBy":"ProductionLine","measureName":"Vibration","predicate":"'Factory3'"}]
```

[![Azure Time Series Insights Explorer paraméteres URL-címe](media/parameterized-url/share-parameterized-url.png)](media/parameterized-url/share-parameterized-url.png#lightbox)

> [!TIP]
> Tekintse meg az Explorer Live [-t a fenti URL-cím használatával](https://insights.timeseries.azure.com/classic/samples?environmentId=10000000-0000-0000-0000-100000000108&relativeMillis=3600000&timeSeriesDefinitions=[%7B%22name%22:%22F1PressureId%22,%22splitBy%22:%22Id%22,%22measureName%22:%22Pressure%22,%22predicate%22:%22%27Factory1%27%22%7D,%7B%22name%22:%22F2TempStation%22,%22splitBy%22:%22Station%22,%22measureName%22:%22Temperature%22,%22predicate%22:%22%27Factory2%27%22%7D,%7B%22name%22:%22F3VibrationPL%22,%22splitBy%22:%22ProductionLine%22,%22measureName%22:%22Vibration%22,%22predicate%22:%22%27Factory3%27%22%7D]
) .

A fenti URL-cím leírja és megjeleníti a paraméteres Azure Time Series Insights Explorer nézetet.

* A paraméteres predikátumok.

  [![Azure Time Series Insights Explorer paraméteres predikátumai.](media/parameterized-url/share-parameterized-url-predicates.png)](media/parameterized-url/share-parameterized-url-predicates.png#lightbox)

* A megosztott teljes diagram nézet.

  [![A megosztott teljes diagram nézet.](media/parameterized-url/share-parameterized-url-full-chart.png)](media/parameterized-url/share-parameterized-url-full-chart.png#lightbox)

## <a name="next-steps"></a>Következő lépések

* Útmutató az [adatlekérdezéshez a C# használatával](time-series-insights-query-data-csharp.md).

* Ismerkedjen meg a [Azure Time Series Insights Explorerrel](./time-series-insights-explorer.md).
