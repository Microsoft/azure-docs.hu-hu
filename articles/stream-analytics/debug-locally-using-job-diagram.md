---
title: Azure Stream Analytics lekérdezések hibakeresése helyileg a Visual Studióban a feladatok diagramjának használatával
description: Ez a cikk azt ismerteti, hogyan lehet a lekérdezéseket helyileg hibakeresést végezni a Azure Stream Analytics Tools for Visual Studio alkalmazásban.
author: su-jie
ms.author: sujie
ms.service: stream-analytics
ms.topic: how-to
ms.date: 01/23/2020
ms.openlocfilehash: d0e94fda1fb21be1a01516f4cecf657426ae867e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98019448"
---
# <a name="debug-azure-stream-analytics-queries-locally-using-job-diagram-in-visual-studio"></a>Azure Stream Analytics lekérdezések hibakeresése helyileg a Visual Studióban a feladatok diagramjának használatával

Az eredmény nélküli vagy váratlan eredményekkel rendelkező feladatok gyakori hibaelhárítási forgatókönyvek a streaming lekérdezésekhez. A feladatütemezés a Visual Studióban helyileg, a köztes eredményhalmaz és az egyes lépések metrikáinak vizsgálatára is használható. A feladatok diagramjai segíthetnek a probléma okának gyors elkülönítésében a hibák elhárítása során.

## <a name="debug-a-query-using-job-diagram"></a>Lekérdezés hibakeresése a feladatütemezés használatával

A bemeneti adatoknak a kimeneti adatokba való átalakítására szolgáló Azure Stream Analytics szkript. A feladatütemezés azt mutatja be, hogy az adatok hogyan áramlanak be a bemeneti forrásokból (Event hub, IoT Hub stb.) több lekérdezési lépéssel, végül pedig a kimeneti mosogatók számára. Minden lekérdezési lépés egy utasítás használatával egy, a parancsfájlban definiált ideiglenes eredményhalmaz számára van leképezve `WITH` . Megtekintheti az adatokat, valamint az egyes lekérdezési lépések mérőszámait az egyes köztes eredményhalmaz-készletekben, hogy megtalálja a probléma forrását.

> [!NOTE]
> Ez a feladatütemezés csak egy csomópontban jeleníti meg a helyi tesztelés adatait és mérőszámait. Nem használható a teljesítmény finomhangolásához és a hibaelhárításhoz.

### <a name="start-local-testing"></a>Helyi tesztelés indítása

Ebből a rövid útmutatóból megtudhatja [, hogyan](stream-analytics-quick-create-vs.md) hozhat létre egy stream Analytics feladatot a Visual Studióval, vagy hogyan [exportálhat egy meglévő feladatot egy helyi projektbe](stream-analytics-vs-tools.md#export-jobs-to-a-project). Ha a lekérdezést helyi bemeneti adatokkal szeretné tesztelni, kövesse az alábbi [utasításokat](stream-analytics-live-data-local-testing.md). Ha élő bemenettel szeretne tesztelni, lépjen a következő lépésre.

> [!NOTE]
> Ha a feladatot helyi projektbe exportálja, és egy élő bemeneti adatfolyamon szeretne tesztelni, akkor újra meg kell adnia az összes bemenet hitelesítő adatait.  

Válassza ki a bemeneti és kimeneti forrást a parancsfájl-szerkesztőből, és válassza a **helyi Futtatás** lehetőséget. A feladatütemezés a jobb oldalon jelenik meg.

### <a name="view-the-intermediate-result-set"></a>A köztes eredményhalmaz megtekintése  

1. A lekérdezési lépés kiválasztásával navigáljon a parancsfájlhoz. A rendszer automatikusan átirányítja a megfelelő szkriptre a bal oldali szerkesztőben.

   ![Feladatok diagramjának navigálása parancsfájl](./media/debug-locally-using-job-diagram/navigate-script.png)

2. Válassza ki a lekérdezési lépést, és válassza az **előnézet** lehetőséget a felugró ablakban. Az eredményhalmaz az alsó eredmény ablakának egyik lapján jelenik meg.

   ![A feladatütemezés előzetes verziójának eredménye](./media/debug-locally-using-job-diagram/preview-result.png)

### <a name="view-step-metrics"></a>Lépés metrikáinak megtekintése

Ebben a szakaszban megismerheti a diagram egyes részeihez elérhető metrikákat.

#### <a name="input-sources-live-stream"></a>Bemeneti források (élő stream)

![Feladatütemezés élő bemeneti forrásai](./media/debug-locally-using-job-diagram/live-input.png)

|Metric|Leírás|
|-|-|
|**TaxiRide**| A bemenet neve.|
|**Event Hub** | Bemeneti forrás típusa|
|**Események**|Az olvasott események száma.|
|**Várakozó-események forrásai**|Hány üzenetet kell olvasni Event Hubs és IoT Hub bemenetekhez.|
|**Bájtban lévő események**|A beolvasott bájtok száma.|
| **Csökkentett teljesítményű események**|Azon események száma, amelyek nem a deszerializálás miatt voltak probléma.|
|**Korai események**| Az alkalmazás időbélyegét tartalmazó események száma a felső vízjel előtt.|
|**Késői események**| Az alkalmazás időbélyegzővel rendelkező eseményeinek száma a felső vízjel után.|
|**Eseményforrások**| Az olvasott adategységek száma. Például a Blobok száma.|

#### <a name="input-sources-local-input"></a>Bemeneti források (helyi bemenet)

![Munkaköri diagram helyi bemeneti forrásai](./media/debug-locally-using-job-diagram/local-input.png)

|Metric|Leírás|
|-|-|
|**TaxiRide**| A bemenet neve.|
|**Sorok száma**| A lépésből generált sorok száma.|
|**Adatok mérete**| Az ebből a lépésből generált adatok mérete.|
|**Helyi bemenet**| Helyi adatok használata bemenetként.|

#### <a name="query-steps"></a>A lekérdezés lépései

![Feladatütemezés lekérdezési lépése](./media/debug-locally-using-job-diagram/query-step.png)

|Metric|Leírás|
|-|-|
|**TripData**|Az ideiglenes eredményhalmaz neve.|
|**Sorok száma**| A lépésből generált sorok száma.|
|**Adatok mérete**| Az ebből a lépésből generált adatok mérete.|
  
#### <a name="output-sinks-live-output"></a>Kimeneti mosogatók (élő kimenet)

![A helyi kimeneti mosogatókat bemutató feladatütemezés.](./media/debug-locally-using-job-diagram/live-output.png)

|Metric|Leírás|
|-|-|
|**regionaggEH**|A kimenet neve.|
|**Események**|A nyelők számára Kimenõ események száma.|

#### <a name="output-sinks-local-output"></a>Kimeneti mosogatók (helyi kimenet)

![Feladatütemezés helyi kimeneti elsüllyedés](./media/debug-locally-using-job-diagram/local-output.png)

|Metric|Leírás|
|-|-|
|**regionaggEH**|A kimenet neve.|
|**Helyi kimenet**| Eredmény kimenete egy helyi fájlba.|
|**Sorok száma**| A sorok kimenetének száma a helyi fájlba.|
|**Adatok mérete**| Az adatkimenet mérete a helyi fájlba.|

### <a name="close-job-diagram"></a>Feladatok diagramjának lezárása

Ha már nincs szüksége a feladatütemezés megadására, a jobb felső sarokban kattintson a **Bezárás** gombra. A diagram ablakának bezárása után újra kell indítani a helyi tesztelést, hogy megtekintse.

### <a name="view-job-level-metrics-and-stop-running"></a>A feladatok szintjének metrikáinak megtekintése és a Futtatás leállítása

A felugró konzolon megjelennek a feladatok szintjének egyéb mérőszámai. Ha le szeretné állítani a feladatot, nyomja le a **CTRL + C** billentyűkombinációt a konzolon.

![Feladatok diagramjának leállítása](./media/debug-locally-using-job-diagram/stop-job.png)

## <a name="limitations"></a>Korlátozások

* A hitelesítési modellre vonatkozó korlátozások miatt nem támogatottak a Power BI és Azure Data Lake Storage Gen1 kimeneti mosogatók.

* Csak a Felhőbeli beviteli lehetőségek támogatják az [időszabályzatokat](./stream-analytics-time-handling.md) , míg a helyi beviteli beállítások nem.

## <a name="next-steps"></a>Következő lépések

* [Rövid útmutató: Stream Analytics-feladatok létrehozása a Visual Studio használatával](stream-analytics-quick-create-vs.md)
* [A Visual Studio használata Azure Stream Analytics feladatok megtekintéséhez](stream-analytics-vs-tools.md)
* [Élő adatellenőrzés helyi tesztelése a Visual Studio Azure Stream Analytics eszközeivel (előzetes verzió)](stream-analytics-live-data-local-testing.md)