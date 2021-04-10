---
title: Azure Stream Analytics az erőforrás-naplók használatával – problémamegoldás
description: Ez a cikk bemutatja, hogyan elemezheti Azure Stream Analytics az erőforrás-naplókat.
author: jseb225
ms.author: jeanb
ms.service: stream-analytics
ms.topic: troubleshooting
ms.custom: contperf-fy21q1
ms.date: 06/18/2020
ms.openlocfilehash: 93d881419c4854b8e46608e150b55072267e0347
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "100574405"
---
# <a name="troubleshoot-azure-stream-analytics-by-using-resource-logs"></a>Azure Stream Analyticsek hibakeresése erőforrás-naplók használatával

Az Azure Stream Analytics-feladat feldolgozása néha váratlanul leáll. Fontos, hogy ilyen esetekben hibaelhárítást tudjon végezni. A hibákat váratlan lekérdezési eredmény, az eszközök kapcsolata vagy váratlan szolgáltatásleállás okozhatja. A Stream Analyticsban található erőforrás-naplók segítségével azonosíthatja a problémák okát, amikor azok bekövetkeznek, és csökkentik a helyreállítási időt.

Erősen ajánlott az erőforrás-naplók engedélyezése az összes feladathoz, mivel ez nagy mértékben segíti a hibakeresést és a figyelést.

## <a name="log-types"></a>Naplózási típusok

Stream Analytics kétféle naplót kínál:

* A [tevékenységek naplói](../azure-monitor/essentials/platform-logs-overview.md) (always on), amelyek betekintést nyújtanak a feladatokon végrehajtott műveletekre.

* [Erőforrás-naplók](../azure-monitor/essentials/platform-logs-overview.md) (konfigurálható), amelyek mélyebb betekintést biztosítanak a feladatokkal megjelenő összes műveletbe. Az erőforrás-naplók a feladatok létrehozásakor és befejezésekor kezdődnek, amikor a rendszer törli a feladatot. Ezek a feladatok a feladatoknak a frissítésekor és a futása közben is lefedik az eseményeket.

> [!NOTE]
> Olyan szolgáltatásokat használhat, mint például az Azure Storage, az Azure Event Hubs és a Azure Monitor naplók a nem megfelelő adatelemzések elemzéséhez. Ezen szolgáltatások díjszabási modellje alapján számítjuk fel a díjat.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="debugging-using-activity-logs"></a>Hibakeresés a Tevékenységnaplók használatával

A Tevékenységnaplók alapértelmezés szerint be vannak kapcsolva, és magas szintű betekintést nyújtanak a Stream Analytics-feladatok által végrehajtott műveletekre. A tevékenység naplóiban található információk segíthetnek megtalálni a feladatát befolyásoló problémák kiváltó okát. A következő lépések végrehajtásával használhatja a Stream Analyticsi tevékenység naplóit:

1. Jelentkezzen be a Azure Portalba, és válassza a **műveletnapló** lehetőséget az **Áttekintés** területen.

   ![Stream Analytics tevékenység naplója](./media/stream-analytics-job-diagnostic-logs/stream-analytics-menu.png)

2. A végrehajtott műveletek listája látható. Minden olyan művelet, amely a feladat hibáját okozta, piros információs buborékmal rendelkezik.

3. Kattintson egy műveletre az összesítő nézet megtekintéséhez. Az itt található információk gyakran korlátozottak. Ha további információt szeretne megtudni a műveletről, kattintson a **JSON** elemre.

   ![Stream Analytics tevékenység naplózási műveletének összegzése](./media/stream-analytics-job-diagnostic-logs/operation-summary.png)

4. Görgessen le a JSON **Tulajdonságok** szakaszába, amely részletesen ismerteti a sikertelen műveletet okozó hibát. Ebben a példában a hiba oka, hogy egy futásidejű hiba történt a kötött szélességi értékektől. Egy Stream Analytics feladatsor által feldolgozott adatok eltérése adathibát okoz. Megismerheti a [bemeneti és kimeneti adatok különböző hibáit és azok okait](./data-errors.md).

   ![JSON-hiba részletei](./media/stream-analytics-job-diagnostic-logs/error-details.png)

5. A JSON-hibaüzeneten alapuló korrekciós műveleteket is végrehajthat. Ebben a példában ellenőrzi, hogy a szélességi érték a-90 fok és a 90 fok közé kerüljön-e a lekérdezéshez.

6. Ha a tevékenység naplóiban található hibaüzenet nem bizonyulhat hasznosnak a kiváltó ok azonosításához, az erőforrás-naplók engedélyezéséhez és a Azure Monitor naplók használatához.

## <a name="send-diagnostics-to-azure-monitor-logs"></a>Diagnosztika küldése Azure Monitor naplókba

Az erőforrás-naplók bekapcsolása és a Azure Monitor naplókba való elküldése kifejezetten ajánlott. Alapértelmezés szerint **ki vannak kapcsolva** . A bekapcsolásához hajtsa végre az alábbi lépéseket:

1.  Ha még nem rendelkezik ilyennel, hozzon létre egy Log Analytics munkaterületet. Azt javasoljuk, hogy a Log Analytics munkaterülete ugyanabban a régióban legyen, mint a Stream Analytics feladatokkal.

2.  Jelentkezzen be a Azure Portalba, és navigáljon a Stream Analytics feladatokhoz. A **figyelés** területen válassza a **diagnosztikai naplók** lehetőséget. Ezután kattintson **a diagnosztika bekapcsolása** elemre.

    ![Panel navigálás az erőforrás-naplókhoz](./media/stream-analytics-job-diagnostic-logs/diagnostic-logs-monitoring.png)  

2.  Adjon meg egy **nevet** a **diagnosztikai beállítások nevében** **, és** keresse meg a **végrehajtáshoz** és a **szerzői** műveletekhez tartozó jelölőnégyzeteket a **AllMetrics** **alatt.** Ezután válassza a **küldés log Analytics** lehetőséget, és válassza ki a munkaterületet. Kattintson a **Mentés** gombra.

    ![Az erőforrás-naplók beállításai](./media/stream-analytics-job-diagnostic-logs/logs-setup.png)

3. A Stream Analytics-feladatok indításakor a rendszer átirányítja az erőforrás-naplókat a Log Analytics-munkaterületre. Ha meg szeretné tekinteni a feladathoz tartozó erőforrás-naplókat, válassza a **figyelés** szakaszban található **naplók** lehetőséget.

   ![Képernyőfelvétel: az általános menü, ahol a naplók ki vannak választva.](./media/stream-analytics-job-diagnostic-logs/diagnostic-logs.png)

4. Stream Analytics olyan előre definiált lekérdezéseket biztosít, amelyek segítségével könnyedén megkeresheti a kívánt naplókat. Bármelyik előre definiált lekérdezést kiválaszthatja a bal oldali ablaktáblán, majd a **Futtatás** elemet is kiválaszthatja. A lekérdezés eredményei az alsó ablaktáblán jelennek meg. 

   ![A képernyőképen egy Stream Analytics feladatokhoz tartozó naplók láthatók.](./media/stream-analytics-job-diagnostic-logs/logs-example.png)

## <a name="resource-log-categories"></a>Erőforrás-naplók kategóriái

Azure Stream Analytics az erőforrás-naplók két kategóriáját rögzíti:

* **Szerzői** műveletek: rögzíti a feladatok létrehozásához kapcsolódó műveleteket, például a feladatok létrehozását, a bemenetek és kimenetek hozzáadását és törlését, a lekérdezés hozzáadását és frissítését, valamint a feladatok elindítását és leállítását.

* **Végrehajtás**: rögzíti a feladatok végrehajtása során előforduló eseményeket.
    * Csatlakozási hibák
    * Adatfeldolgozási hibák, beleértve az alábbiakat:
        * Azok az események, amelyek nem felelnek meg a lekérdezés definíciójának (nem egyező mezőtípus és értékek, hiányzó mezők stb.)
        * Kifejezés-értékelési hibák
    * Egyéb események és hibák

## <a name="resource-logs-schema"></a>Erőforrás-naplók sémája

Az összes napló JSON formátumban van tárolva. Mindegyik bejegyzés a következő általános karakterlánc-mezőket tartalmazhatja:

Név | Leírás
------- | -------
time | A napló időbélyegzője (UTC).
resourceId | Annak az erőforrásnak az azonosítója, amelyre a művelet bekerült, nagybetűvel. Magában foglalja az előfizetés-azonosítót, az erőforráscsoportot és a feladatnév nevét. Például: **/Subscriptions/6503D296-DAC1-4449-9B03-609A1F4A1C87/RESOURCEGROUPS/My-Resource-Group/Providers/Microsoft. STREAMANALYTICS/STREAMINGJOBS/MYSTREAMINGJOB**.
category | Napló kategóriája, **végrehajtás** vagy **szerzői** művelet.
operationName | A naplózott művelet neve. Például az **események küldése: az SQL kimeneti írási hiba mysqloutput**.
status | A művelet állapota. Például sikertelen vagy **sikeres** **volt** .
szint | Naplózási szint. Például: **hiba**, **Figyelmeztetés** vagy **tájékoztató**.
properties | Naplóbejegyzés-specifikus részletek, JSON-karakterláncként szerializálva. További információkért tekintse meg a cikk következő részeit.

### <a name="execution-log-properties-schema"></a>Végrehajtási napló tulajdonságai sémája

A végrehajtási naplók információkkal rendelkeznek a Stream Analytics feladatok végrehajtása során történt eseményekről. A tulajdonságok sémája attól függően változik, hogy az esemény Adathiba vagy általános esemény-e.

### <a name="data-errors"></a>Adathibák

Minden olyan hiba, amely akkor fordul elő, amikor a feladatsor az adatok feldolgozását végzi. Ezek a naplók leggyakrabban az adatok olvasási, szerializálási és írási műveletei során jönnek létre. Ezek a naplók nem tartalmaznak csatlakozási hibákat. A csatlakozási hibákat általános eseményekként kezeli a rendszer. További információt a különböző [bemeneti és kimeneti adatokkal kapcsolatos hibák](./data-errors.md)okairól itt talál.

Név | Leírás
------- | -------
Forrás | A feladathoz tartozó bemenet vagy kimenet neve, ahol a hiba történt.
Üzenet | A hibához tartozó üzenet.
Típus | A hiba típusa. Például: **DataConversionError**, **CsvParserError** vagy **ServiceBusPropertyColumnMissingError**.
Adatok | Olyan információkat tartalmaz, amelyek hasznosak a hiba forrásának pontos megtalálásához. A mérettől függően a csonkítás függ.

A **operationName** értékétől függően az adathibák a következő sémával rendelkeznek:

* Az **események szerializálása** az esemény-olvasási műveletek során következik be. Akkor fordulnak elő, ha a bemeneti adatok nem felelnek meg a lekérdezési sémának az alábbi okok valamelyike miatt:

   * A következő *típus nem egyezik az esemény (de) szerializálásakor*: a hibát okozó mezőt azonosítja.

   * *Nem lehet olvasni egy eseményt, érvénytelen szerializálás*: felsorolja a bemeneti adatok azon helyével kapcsolatos információkat, ahol a hiba történt. Tartalmazza a Blobok bemenetének, eltolásának és az adatok mintájának a nevét.

* Az **események küldése** az írási műveletek során történik. A hibát okozó folyamatos átviteli eseményt azonosítják.

### <a name="generic-events"></a>Általános események

Az általános események minden mást lefedik.

Név | Leírás
-------- | --------
Hiba | választható Hiba adatai. Általában ez a kivételi információ, ha elérhető.
Üzenet| A naplózási üzenet.
Típus | Az üzenet típusa. Leképezés a hibák belső kategorizálására. Például: **JobValidationError** vagy **BlobOutputAdapterInitializationFailure**.
Korrelációs azonosító | GUID, amely egyedileg azonosítja a feladatok végrehajtását. Az összes végrehajtási naplóbejegyzés abban az időponttól kezdve, amíg a feladatoknak nem kell megegyezniük a **korrelációs azonosító** értékével.

## <a name="next-steps"></a>Következő lépések

* [AdatStream Analyticsi hibák](./data-errors.md)
* [Stream Analytics lekérdezés nyelvi referenciája](/stream-analytics-query/stream-analytics-query-language-reference)