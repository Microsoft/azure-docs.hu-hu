---
title: Azure Stream Analytics-bemenetek hibaelhárítása
description: Ez a cikk a Azure Stream Analytics-feladatokban lévő bemeneti kapcsolatok hibaelhárítási módszereit ismerteti.
author: sidramadoss
ms.author: sidram
ms.service: stream-analytics
ms.topic: troubleshooting
ms.date: 05/01/2020
ms.custom: seodec18
ms.openlocfilehash: 2d7171c9ec1e60447fb3342caa72098fb2eb9337
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98019907"
---
# <a name="troubleshoot-input-connections"></a>Bemeneti kapcsolatok hibaelhárítása

Ez a cikk a Azure Stream Analytics bemeneti kapcsolataival, a bemeneti problémák elhárításával és a problémák megoldásával kapcsolatos gyakori problémákat ismerteti. Számos hibaelhárítási lépéshez engedélyezni kell az erőforrás-naplókat a Stream Analytics feladatokhoz. Ha nincs engedélyezve az erőforrás-naplók, tekintse meg [a Azure stream Analytics az erőforrás-naplók használatával történő hibakeresését](stream-analytics-job-diagnostic-logs.md)ismertető témakört.

## <a name="input-events-not-received-by-job"></a>A feladatok által nem fogadott bemeneti események 

1.  Tesztelje a bemeneti és kimeneti kapcsolatot. Az egyes bemenetekhez és kimenetekhez tartozó **kapcsolat tesztelése** gomb használatával ellenőrizze, hogy kapcsolódik-e a bemenetekhez és kimenetekhez.

2.  Vizsgálja meg a bemeneti adatokat.

    1. Használja az egyes bemenetek [**mintaadatok**](./stream-analytics-test-query.md) gombját. Töltse le a bemeneti mintaadatok.
        
    1. Vizsgálja meg a mintaadatok a séma és az [adattípusok](/stream-analytics-query/data-types-azure-stream-analytics)megismeréséhez.
    
    1. Ellenőrizze az [Event hub mérőszámait](../event-hubs/event-hubs-metrics-azure-monitor.md) az események küldésének biztosításához. Az üzenetek metrikáinak nullánál nagyobbnak kell lenniük, ha Event Hubs üzeneteket fogadnak.

3.  Győződjön meg arról, hogy a bemeneti előnézetben időtartományt jelölt ki. Válassza az **időtartomány kiválasztása** lehetőséget, majd adja meg a minta időtartamát a lekérdezés tesztelése előtt.

## <a name="malformed-input-events-causes-deserialization-errors"></a>A helytelen formátumú bemeneti események deszerializálási hibákat okoznak 

A deszerializálási problémákat akkor okozhatja a rendszer, ha a Stream Analytics-feladathoz tartozó bemeneti adatfolyam helytelenül formázott üzeneteket tartalmaz. A helytelenül formázott üzenetet például egy hiányzó zárójel vagy kapcsos zárójel, egy JSON-objektum vagy egy helytelen időbélyeg-formátum okozhatja az idő mezőben. 
 
Ha egy Stream Analytics-feladatban helytelenül formázott üzenet érkezik egy bemenetből, a rendszer elveti az üzenetet, és figyelmeztetést küld. A Stream Analytics feladatának **bemenetek** csempén figyelmeztető szimbólum jelenik meg. A következő figyelmeztető szimbólum létezik, feltéve, hogy a feladatsor fut állapotban van:

![Azure Stream Analytics bemenetek csempe](media/stream-analytics-malformed-events/stream-analytics-inputs-tile.png)

Az erőforrás-naplók engedélyezésével megtekintheti a hiba részleteit és a hibát okozó üzenetet (hasznos adatokat). A deszerializálási hibák több okból is előfordulhatnak. Az adott deszerializálási hibákkal kapcsolatos további információkért lásd: [bemeneti adatok hibái](data-errors.md#input-data-errors). Ha az erőforrás-naplók nincsenek engedélyezve, egy rövid értesítés lesz elérhető a Azure Portal.

![Bemeneti adatok figyelmeztetési értesítése](media/stream-analytics-malformed-events/warning-message-with-offset.png)

Abban az esetben, ha az üzenet tartalma meghaladja a 32 KB-ot, vagy bináris formátumú, futtassa a [GitHub-minták tárházában](https://github.com/Azure/azure-stream-analytics/tree/master/Samples/CheckMalformedEventsEH)elérhető CheckMalformedEvents. cs kódot. Ez a kód beolvassa a partíció AZONOSÍTÓját, eltolását, és kinyomtatja az adott eltolásban található adatokat. 

## <a name="job-exceeds-maximum-event-hub-receivers"></a>A feladatoknál nagyobb az Event hub-vevőkészülékek maximális száma

A Event Hubs használatának ajánlott gyakorlata, ha több fogyasztói csoportot használ a feladatok méretezhetőségére. Egy adott bemenet Stream Analytics feladatában szereplő olvasók száma az egyetlen fogyasztói csoportban lévő olvasók számát érinti. A fogadók pontos száma a kibővíthető topológia logikájának belső implementációs részletein alapul, és nem külsőleg van kitéve. Az olvasók száma a feladatok indításakor vagy a feladatok frissítésekor változhat.

A következő hibaüzenetek jelennek meg, ha a fogadók száma meghaladja a maximális értéket. A hibaüzenet tartalmazza a felhasználói csoport keretében az Event hub-hoz létesített meglévő kapcsolatok listáját. A címke `AzureStreamAnalytics` azt jelzi, hogy a kapcsolatok az Azure streaming Service-ből származnak.

```
The streaming job failed: Stream Analytics job has validation errors: Job will exceed the maximum amount of Event Hub Receivers.

The following information may be helpful in identifying the connected receivers: Exceeded the maximum number of allowed receivers per partition in a consumer group which is 5. List of connected receivers – 
AzureStreamAnalytics_c4b65e4a-f572-4cfc-b4e2-cf237f43c6f0_1, 
AzureStreamAnalytics_c4b65e4a-f572-4cfc-b4e2-cf237f43c6f0_1, 
AzureStreamAnalytics_c4b65e4a-f572-4cfc-b4e2-cf237f43c6f0_1, 
AzureStreamAnalytics_c4b65e4a-f572-4cfc-b4e2-cf237f43c6f0_1, 
AzureStreamAnalytics_c4b65e4a-f572-4cfc-b4e2-cf237f43c6f0_1.
```

> [!NOTE]
> Ha az olvasók száma megváltozik a feladatok frissítésekor, a rendszer átmeneti figyelmeztetéseket ír a naplókba. Stream Analytics a feladatok automatikusan helyreállítják ezeket az átmeneti problémákat.

### <a name="add-a-consumer-group-in-event-hubs"></a>Fogyasztói csoport hozzáadása Event Hubs

Ha új fogyasztói csoportot szeretne hozzáadni a Event Hubs-példányban, kövesse az alábbi lépéseket:

1. Jelentkezzen be az Azure Portalra.

2. Keresse meg az Event hub-t.

3. Válassza ki **Event Hubs** az **entitások** fejléc alatt.

4. Válassza ki az Event hub nevet név szerint.

5. A **Event Hubs példány** lapon az **entitások** fejléc alatt válassza a **fogyasztói csoportok** lehetőséget. Megjelenik egy **$default** nevű fogyasztói csoport a listáján.

6. Válassza a **+ fogyasztói csoport** lehetőséget egy új fogyasztói csoport hozzáadásához. 

   ![Fogyasztói csoport hozzáadása Event Hubs](media/stream-analytics-event-hub-consumer-groups/new-eh-consumer-group.png)

7. Amikor létrehozta a bemenetet a Stream Analytics feladatban az Event hub-ra való Rámutatás céljából, ott adta meg a fogyasztói csoportot. Ha nincs megadva, a rendszer **$Defaultt** használ. Miután létrehozta az új fogyasztói csoportot, szerkessze az Event hub-bemenetet a Stream Analytics feladatban, és adja meg az új fogyasztói csoport nevét.

## <a name="readers-per-partition-exceeds-event-hubs-limit"></a>Az olvasók/partíciók száma meghaladja Event Hubs korlátot

Ha az adatfolyam-lekérdezési szintaxis többször is ugyanarra a bemeneti Event hub-erőforrásra hivatkozik, akkor a keresőmotor több olvasót is használhat egy adott felhasználói csoportból lekérdezéssel. Ha túl sok hivatkozás fordul elő ugyanahhoz a fogyasztói csoporthoz, a feladatokhoz legfeljebb öt korlát léphet fel. Ilyen körülmények között a következő szakaszban ismertetett megoldással tovább oszthat meg több adatbevitelt több fogyasztói csoport között. 

Az olyan forgatókönyvek, amelyekben a partíciók száma meghaladja az 5 Event Hubs korlátot, a következők:

* Több SELECT utasítás: Ha több SELECT utasítást használ, amelyek **ugyanarra** az Event hub-bemenetre hivatkoznak, akkor minden SELECT utasítás új fogadót hoz létre.

* UNION: Ha Uniót használ, több bemenet is lehet, amelyek **ugyanarra** az Event hub-ra és a fogyasztói csoportra vonatkoznak.

* Önillesztés: Ha önillesztési műveletet használ, lehetséges, hogy többször is hivatkozhat **ugyanarra** az Event hub-ra.

Az alábbi ajánlott eljárások segíthetnek enyhíteni az olyan forgatókönyveket, amelyekben a partíciók száma meghaladja az 5 Event Hubs korlátot.

### <a name="split-your-query-into-multiple-steps-by-using-a-with-clause"></a>A lekérdezés felosztása több lépésre a WITH záradék használatával

A WITH záradék egy ideiglenes elnevezett eredményhalmazt ad meg, amelyre a lekérdezés FROM záradéka hivatkozhat. A WITH záradék definiálása egyetlen SELECT utasítás végrehajtási hatókörében végezhető el.

Például a következő lekérdezés helyett:

```SQL
SELECT foo 
INTO output1
FROM inputEventHub

SELECT bar
INTO output2
FROM inputEventHub 
…
```

Használja a következőt:

```SQL
WITH data AS (
   SELECT * FROM inputEventHub
)

SELECT foo
INTO output1
FROM data

SELECT bar
INTO output2
FROM data
…
```

### <a name="ensure-that-inputs-bind-to-different-consumer-groups"></a>Győződjön meg arról, hogy a bemenetek a különböző fogyasztói csoportokba vannak kötve

Azokhoz a lekérdezésekhez, amelyekben három vagy több bemenet csatlakozik ugyanahhoz a Event Hubs fogyasztói csoporthoz, hozzon létre külön fogyasztói csoportokat. Ehhez további Stream Analytics bemeneteket kell létrehoznia.

### <a name="create-separate-inputs-with-different-consumer-groups"></a>Különálló bemenetek létrehozása különböző fogyasztói csoportokkal

Ugyanahhoz az Event hub-hoz különböző fogyasztói csoportokkal is létrehozhat külön bemeneteket. A következő UNION-lekérdezés olyan példa, amelyben a *InputOne* és a *InputTwo* ugyanarra az Event hub-forrásra hivatkozik. Bármely lekérdezés rendelkezhet különálló, különböző fogyasztói csoportokkal rendelkező bemenetekkel. A UNION lekérdezés csak egy példa.

```sql
WITH 
DataOne AS 
(
SELECT * FROM InputOne 
),

DataTwo AS 
(
SELECT * FROM InputTwo 
),

SELECT foo FROM DataOne
UNION 
SELECT foo FROM DataTwo

```

## <a name="readers-per-partition-exceeds-iot-hub-limit"></a>Az olvasók/partíciók száma meghaladja IoT Hub korlátot

Stream Analytics feladatok a IoT Hub beépített [Event hub-kompatibilis végpontját](../iot-hub/iot-hub-devguide-messages-read-builtin.md) használják a IoT hub-ból való kapcsolódáshoz és olvasáshoz. Ha az olvasás/partíció meghaladja a IoT Hub korlátait, az [Event hub megoldásait](#readers-per-partition-exceeds-event-hubs-limit) használhatja a megoldásához. Létrehozhat egy fogyasztói csoportot a beépített végponthoz IoT Hub portál végpont-munkamenetben vagy a [IOT hub SDK](/rest/api/iothub/IotHubResource/CreateEventHubConsumerGroup)-n keresztül.

## <a name="get-help"></a>Segítség kérése

További segítségért próbálja ki a [Microsoft Q&a Azure stream Analytics kérdéseit](/answers/topics/azure-stream-analytics.html).

## <a name="next-steps"></a>Következő lépések

* [Bevezetés a Azure Stream Analyticsba](stream-analytics-introduction.md)
* [Get started using Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md) (Bevezetés az Azure Stream Analytics használatába)
* [Scale Azure Stream Analytics jobs (Azure Stream Analytics-feladatok méretezése)](stream-analytics-scale-jobs.md)
* [Azure Stream Analytics Query Language Reference (Referencia az Azure Stream Analytics lekérdezési nyelvhez)](/stream-analytics-query/stream-analytics-query-language-reference)
* [Az Azure Stream Analytics felügyeleti REST API referenciája](/rest/api/streamanalytics/)