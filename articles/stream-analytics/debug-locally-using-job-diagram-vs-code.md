---
title: Azure Stream Analytics lekérdezések hibakeresése helyileg a Visual Studio Code-ban a feladatütemezés használatával
description: Ez a cikk azt ismerteti, hogyan lehet a lekérdezéseket helyileg felvenni a Visual Studio Code Azure Stream Analytics-bővítményében a feladatok diagramjának használatával.
author: su-jie
ms.author: sujie
ms.service: stream-analytics
ms.topic: how-to
ms.date: 06/23/2020
ms.openlocfilehash: 25ad696ad345fbf672f6bf26eb3f35a13fb03ea5
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98019499"
---
# <a name="debug-azure-stream-analytics-queries-locally-using-job-diagram-in-visual-studio-code"></a>Azure Stream Analytics lekérdezések hibakeresése helyileg a Visual Studio Code-ban a feladatütemezés használatával

Azok a folyamatos átviteli feladatok, amelyeknek nincs eredmény vagy váratlan eredményük, gyakran hibaelhárításra van szükségük. A Visual Studio Code bővítmény Azure Stream Analytics integrálja a feladatok diagramokat, metrikákat, diagnosztikai naplókat és közbenső eredményeket, így gyorsan elkülönítheti a probléma forrását. A feladatütemezés a lekérdezés helyi tesztelésével is használható a közbenső eredményhalmaz és az egyes lépések mérőszámai vizsgálatához.

## <a name="debug-a-query-using-job-diagram"></a>Lekérdezés hibakeresése a feladatütemezés használatával

A bemeneti adatoknak a kimeneti adatokba való átalakítására szolgáló Azure Stream Analytics szkript. A feladatütemezés azt mutatja be, hogy az adatok hogyan áramlanak be a bemeneti forrásokból, például az Event hub-ból vagy a IoT Hubból, és több lekérdezési lépést is végrehajtanak Minden lekérdezési lépés egy utasítás használatával egy, a parancsfájlban definiált ideiglenes eredményhalmaz számára van leképezve `WITH` . Megtekintheti az adatokat, valamint az egyes lekérdezési lépések mérőszámait az egyes köztes eredményhalmaz-készletekben, hogy megtalálja a probléma forrását.

> [!NOTE]
> Ez a feladatütemezés csak egy csomópontban jeleníti meg a helyi tesztelés adatait és mérőszámait. Nem használható a teljesítmény finomhangolásához és a hibaelhárításhoz.

### <a name="start-local-testing"></a>Helyi tesztelés indítása

Ebből a rövid útmutatóból megtudhatja [, hogyan](quick-create-visual-studio-code.md) hozhat létre egy stream Analytics feladatot a Visual Studio Code használatával, vagy hogyan [exportálhat egy meglévő feladatot egy helyi projektbe](visual-studio-code-explore-jobs.md). A bemenetekhez és kimenetekhez tartozó hitelesítő adatokat a rendszer automatikusan kitölti az exportált feladatokhoz.

Ha a lekérdezést helyi bemeneti adatokkal szeretné tesztelni, kövesse az alábbi [utasításokat](visual-studio-code-local-run.md). Ha élő bemenettel szeretne tesztelni, [konfigurálja a bemenetet](stream-analytics-add-inputs.md) a következő lépésre. 

Nyissa meg a *\. asaql* parancsfájlt, és válassza a **helyi Futtatás** lehetőséget. Ezután válassza a **helyi bemenet használata** lehetőséget, vagy **használjon élő bemenetet**. A feladatütemezés az ablak jobb oldalán jelenik meg.

### <a name="view-the-output-and-intermediate-result-set"></a>A kimenet és a köztes eredményhalmaz megtekintése  

1. A feladatok összes kimenete megjelenik a Visual Studio Code ablakának jobb alsó sarkában található eredmény ablakban.

   > [!div class="mx-imgBorder"]
   > ![A feladatok kimenetének eredményei](./media/debug-locally-using-job-diagram-vs-code/job-output-results.png)

2. A lekérdezési lépés kiválasztásával navigáljon a parancsfájlhoz. A rendszer automatikusan átirányítja a megfelelő szkriptre a bal oldali szerkesztőben. A köztes eredmény a Visual Studio Code ablakának jobb alsó sarkában található eredmény ablakban jelenik meg.

   > [!div class="mx-imgBorder"]
   > ![A feladatütemezés előzetes verziójának eredménye](./media/debug-locally-using-job-diagram-vs-code/preview-result.png)

### <a name="view-metrics"></a>Metrikák megtekintése

Ebben a szakaszban megismerheti a diagram egyes részeihez elérhető metrikákat.

1. Válassza a **mérőszámok** lapot a Visual Studio Code ablak jobb alsó sarkában található **eredmény** lap mellett.

2. Válassza ki a **feladatot** a legördülő listából. A Graph-csomópontok bármelyik üres területét kiválasztva megnyithatja a feladatok szintjének mérőszámait. Ez a nézet tartalmazza az összes mérőszámot, amelyek 10 másodpercenként frissülnek a feladatok futtatásakor. A diagramokon megtekintheti a metrikákat a jobb oldalon, és kiválaszthatja azokat.

   > [!div class="mx-imgBorder"]
   > ![Feladatok diagram metrikái](./media/debug-locally-using-job-diagram-vs-code/job-metrics.png)

3. A bemeneti metrikák megjelenítéséhez válassza ki a bemeneti adatforrás nevét a legördülő listából. Az alábbi képernyőképen található bemeneti forrás neve *idézőjel*. A bemeneti metrikákkal kapcsolatos további információkért lásd: [stream Analytics feladatok figyelésének ismertetése és a lekérdezések figyelése](stream-analytics-monitoring.md).

   > [!div class="mx-imgBorder"]
   > ![A feladatütemezés bemeneti metrikái](./media/debug-locally-using-job-diagram-vs-code/input-metrics.png)

4. Válasszon egy lekérdezési lépést a feladatütemezés közül, vagy válassza ki a lépés nevét a legördülő listából a lépés szintű mérőszámok megtekintéséhez. A vízjel késleltetése az egyetlen elérhető lépés mérőszáma.

   > [!div class="mx-imgBorder"]
   > ![Lépések mérőszámai](./media/debug-locally-using-job-diagram-vs-code/step-metrics.png)

5. A kimenettel kapcsolatos metrikák megjelenítéséhez válasszon ki egy kimenetet a diagramon vagy a legördülő listából. A kimeneti metrikákkal kapcsolatos további információkért lásd: [stream Analytics feladatok figyelésének ismertetése és a lekérdezések figyelése](stream-analytics-monitoring.md). Az élő kimeneti mosogatók nem támogatottak.

   > [!div class="mx-imgBorder"]
   > ![Kimeneti metrikák](./media/debug-locally-using-job-diagram-vs-code/output-metrics.png)

### <a name="view-diagnostic-logs"></a>Diagnosztikai naplók megtekintése

A feladatok szintjének diagnosztikai naplói a bemeneti adatforrások és a kimeneti tárolók diagnosztikai információit tartalmazzák. Ha kiválaszt egy bemeneti csomópontot vagy kimeneti csomópontot, a rendszer csak a megfelelő naplókat jeleníti meg. A lekérdezési lépés kiválasztásakor nem láthatók naplók. A naplókat a feladatok szintjén találja, a naplókat pedig súlyosság és idő szerint szűrheti.

   > [!div class="mx-imgBorder"]
   > ![Diagnosztikai naplók](./media/debug-locally-using-job-diagram-vs-code/diagnostic-logs.png)

   Jelöljön ki egy naplóbejegyzést a teljes üzenet megtekintéséhez.

   > [!div class="mx-imgBorder"]
   > ![Diagnosztikai naplók üzenete](./media/debug-locally-using-job-diagram-vs-code/diagnostic-logs-message.png)


## <a name="other-job-diagram-features"></a>Egyéb feladatütemezés-funkciók

A **Leállítás** vagy a **felfüggesztés** lehetőség kiválasztásával igény szerint megadhatja az eszköztáron az eszköztárat. A feladat szüneteltetése után folytathatja az utolsó kimenetet.

> [!div class="mx-imgBorder"]
> ![Feladat leállítása vagy szüneteltetése](./media/debug-locally-using-job-diagram-vs-code/stop-pause-job.png)

A feladatütemezés jobb felső sarkában válassza a **feladatok összegzése** lehetőséget a helyi feladatokhoz tartozó tulajdonságok és konfigurációk megtekintéséhez.

> [!div class="mx-imgBorder"]
> ![Helyi feladatok összegzése](./media/debug-locally-using-job-diagram-vs-code/job-summary.png)

## <a name="limitations"></a>Korlátozások

* Az élő kimeneti mosogatók nem támogatottak a helyi futtatásban.

* A feladat helyi futtatása a JavaScript függvénnyel csak a Windows operációs rendszeren támogatott.

* A C# egyéni kód és Azure Machine Learning függvények nem támogatottak. 

* Csak a Felhőbeli beviteli lehetőségek támogatják az [időszabályzatokat](./stream-analytics-time-handling.md) , míg a helyi bemeneti beállítások nem.

## <a name="next-steps"></a>Következő lépések

* [Rövid útmutató: Stream Analytics-feladatok létrehozása a Visual Studio Code használatával](quick-create-visual-studio-code.md)
* [A Visual Studio Code Azure Stream Analytics megismerése](visual-studio-code-explore-jobs.md)
* [A Visual Studio Code segítségével helyileg tesztelheti Stream Analytics lekérdezéseket a mintaadatok használatával](visual-studio-code-local-run.md)
* [Azure Stream Analytics feladatok helyi tesztelése élő bevitelsel a Visual Studio Code használatával](visual-studio-code-local-run-live-input.md)