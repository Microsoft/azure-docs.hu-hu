---
title: Az Azure Time Series Insights egyéni nézeteinek megosztása paraméteres URL-címek használatával | Microsoft Docs
description: Ez a cikk bemutatja, hogyan lehet paraméteres URL-címeket fejleszteni az Azure Time Series Insightsban az ügyfélnézetek egyszerű megosztásához.
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: anshan
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile, anshan
ms.topic: conceptual
ms.workload: big-data
ms.date: 11/21/2017
ms.custom: seodec18
ms.openlocfilehash: 3b6c4b8cebdff772ea1bd4ac95e4c8c06043f5d5
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2019
ms.locfileid: "64728162"
---
# <a name="share-a-custom-view-using-a-parameterized-url"></a>Egyéni nézet megosztása paraméteres URL-cím használatával

Az adott egyéni nézetnek a Time Series Insights Explorerben történő megosztásához programozott módon hozhatja létre az egyéni nézet paraméteres URL-címét.

A Time Series Insights Explorer támogatja az URL-alapú lekérdezési paraméterek használatát a nézeteknek közvetlenül az URL-címből történő megadásához.  Például kizárólag az URL-cím használatával adhatja meg a célkörnyezetet, a keresési predikátumot és a kívánt időtartományt is. Ha a felhasználó a testreszabott URL-címre kattint, a felületen egy, közvetlenül a Time Series Insights portál adott adategységére mutató hivatkozás jelenik meg.  Adathozzáférési szabályzatok érvényesek. 

## <a name="environment-id"></a>Környezetazonosító

A célkörnyezet azonosítóját az `environmentId=<guid>` paraméter adja meg.  Ez a paraméter a teljes adathozzáférési tartománynév egy összetevője, amelyet az Azure Portal környezetáttekintési ablakának jobb felső sarkában talál meg.  Minden, ami az`env.timeseries.azure.com` előtt szerepel. Példa környezetazonosító paraméterre: `?environmentId=10000000-0000-0000-0000-100000000108`.

## <a name="time"></a>Time

Paraméteres URL-cím esetében abszolút vagy relatív időértékeket is megadhat.

### <a name="absolute-time-values"></a>Abszolút időértékek

Abszolút időértékek megadásához használja a következő paramétereket: `from=<integer>` és `to=<integer>`. 

A `from=<integer>` egy olyan érték, amely JavaScript-ezredmásodpercben adja meg a keresés kezdési időpontját.

A `to=<integer>` egy olyan érték, amely JavaScript-ezredmásodpercben adja meg a keresés befejezési időpontját. 

Az adott dátumhoz tartozó JavaScript-ezredmásodpercek meghatározását lásd: [Epoch & Unix Timestamp Converter](https://www.freeformatter.com/epoch-timestamp-to-date-converter.html).

### <a name="relative-time-values"></a>Relatív időértékek

Relatív időértékek esetében a `relativeMillis=<value>` paramétert használja, ahol a *value* (érték) a legutóbbi háttérbeli adatművelet óta eltelt idő JavaScript-ezredmásodpercben.

A `&relativeMillis=3600000` például a legutóbbi 60 perc adatait jeleníti meg.

Az elfogadott értékek megfelelnek a Time Series Insights Explorer **quick time** menüjének, és a következőket tartalmazzák:

- 1800000 (a legutóbbi 30 perc)
- 3600000 (a legutóbbi 60 perc)
- 10800000 (a legutóbbi 3 óra)
- 21600000 (a legutóbbi 6 óra)
- 43200000 (a legutóbbi 12 óra)
- 86400000 (a legutóbbi 24 óra)
- 604800000 (a legutóbbi 7 nap)
- 2592000000 (a legutóbbi 30 óra)

### <a name="optional-parameters"></a>Választható paraméterek

Az adott Time Series Insights-nézet kifejezéseit a `timeSeriesDefinitions=<collection of term objects>` paraméter határozza meg, amely a következőket tartalmazza:

- "name": "\<karakterlánc >"
  - A *kifejezés* neve.
- "splitBy":"\<string>"
  - A *felosztás* alapjául szolgáló oszlop neve.
- "measureName": "\<karakterlánc >"
  - A *mérték* oszlopneve.
- "predikátum": "\<karakterlánc >"
  - A kiszolgálóoldali szűrés *where* záradéka.
- „useSum”:„igaz”
  - Ez egy nem kötelező paraméter, amely meghatározza az összeg használatát a mértékhez.  Vegye figyelembe, ha az „Események” a kiválasztott mérték, a darabszám alapértelmezés szerint ki van választva.  Ha az „Események” nincs kiválasztva, az átlag van alapértelmezettként kiválasztva.  

A multiChartStack=<true/false> paraméter engedélyezi a diagramon történő rétegezést, a multiChartSameScale=<true/false> paraméter pedig engedélyezi ugyanannak az Y tengely irányú skálázásnak a használatát több feltételen egy választható paraméteren belül.  

- multiChartStack=false
  - Az alapértelmezett érték a true, ezért false értéket adjon meg a rétegezéshez.
- multiChartStack=false&multiChartSameScale=true 
  - A rétegezést engedélyeznie kell az azonos Y tengely irányú skálázás több feltételben való használatához.  Ennek alapértelmezett értéke false, tehát a funkció engedélyezéséhez a true értéket kell megadnia.  
  
A `timeBucketUnit=<Unit>&timeBucketSize=<integer>` lehetővé teszi, hogy az időköz csúszkáját egy részletesebb vagy simább, módosítsa a diagram nézet több összesítve.  
- `timeBucketUnit=<Unit>&timeBucketSize=<integer>`
  - Egységek = nap, óra, perc, másodperc, ezredmásodperc.  Az egység nevét mindig nagybetűvel írja.
  - Az egységek számának megadásához adja át a kívánt egész számot a timeBucketSize paraméterhez.  Vegye figyelembe, hogy a részletesség legfeljebb 7 napos lehet.  
  
A `timezoneOffset=<integer>` a paraméter lehetővé teszi, hogy az UTC Időzóna időeltolódással lehet megtekinteni kívánt diagram időzónáját.  
  - `timezoneOffset=-<integer>`
    - Az egész számot mindig ezredmásodpercben kell megadnia.  
    - Vegye figyelembe, hogy ez a funkció kissé eltér a TSI Explorerben engedélyezett funkciótól, a helyi (böngésző szerinti) időt vagy az egyezményes világidőt (UTC) is kiválaszthatja.  
 
### <a name="examples"></a>Példák

Idősorozat-meghatározások URL-paraméterként történő hozzáadásához például használhatja a következőket:

```https
&timeSeriesDefinitions=[{"name":"F1PressureId","splitBy":"Id","measureName":"Pressure","predicate":"'Factory1'"},{"name":"F2TempStation","splitBy":"Station","measureName":"Temperature","predicate":"'Factory2'"},
{"name":"F3VibrationPL","splitBy":"ProductionLine","measureName":"Vibration","predicate":"'Factory3'"}]
```

Használja ezeket a példa idősorozat-meghatározásokat a következőkhöz: 

- környezetazonosító
- a legutóbbi 60 perc adatai
- a választható paramétereket tartalmazó kifejezések (F1PressureID, F2TempStation és F3VibrationPL)
 
Ezáltal az alábbi paraméteres URL-címet állíthatja össze egy nézethez:

```https
https://insights.timeseries.azure.com/samples?environmentId=10000000-0000-0000-0000-100000000108&relativeMillis=3600000&timeSeriesDefinitions=[{"name":"F1PressureId","splitBy":"Id","measureName":"Pressure","predicate":"'Factory1'"},{"name":"F2TempStation","splitBy":"Station","measureName":"Temperature","predicate":"'Factory2'"},{"name":"F3VibrationPL","splitBy":"ProductionLine","measureName":"Vibration","predicate":"'Factory3'"}]
```

Ha az előbb említett URL-cím által leírt nézet létrehozásához a Time Series Insights Explorert használta, az a következőképpen fog kinézni:

![A Time Series Insights Explorer kifejezései](media/parameterized-url/url1.png)

A teljes nézet (a diagramot is beleértve) a következőképpen fog kinézni:

![Diagramnézet](media/parameterized-url/url2.png)

## <a name="next-steps"></a>További lépések

- Ismerje meg, hogyan [lekérdezés adatait az C# ](time-series-insights-query-data-csharp.md).