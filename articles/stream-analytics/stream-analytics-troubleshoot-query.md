---
title: Azure Stream Analytics lekérdezések hibáinak megoldása
description: Ez a cikk azokat a technikákat ismerteti, amelyekkel Azure Stream Analytics-feladatokban található lekérdezéseket lehet elhárítani.
author: sidramadoss
ms.author: sidram
ms.service: stream-analytics
ms.topic: troubleshooting
ms.date: 03/31/2020
ms.custom: seodec18
ms.openlocfilehash: 0dc36b817d5b5cdf731edecd64e1879c153d866a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98015130"
---
# <a name="troubleshoot-azure-stream-analytics-queries"></a>Azure Stream Analytics lekérdezések hibáinak megoldása

Ez a cikk a Stream Analytics-lekérdezések fejlesztésével és hibaelhárításával kapcsolatos gyakori problémákat ismerteti.

Ez a cikk a Azure Stream Analytics lekérdezések fejlesztésével, a lekérdezési problémák megoldásával és a problémák megoldásával kapcsolatos gyakori problémákat ismerteti. Számos hibaelhárítási lépéshez engedélyezni kell az erőforrás-naplókat a Stream Analytics feladatokhoz. Ha nincs engedélyezve az erőforrás-naplók, tekintse meg [a Azure stream Analytics az erőforrás-naplók használatával történő hibakeresését](stream-analytics-job-diagnostic-logs.md)ismertető témakört.

## <a name="query-is-not-producing-expected-output"></a>A lekérdezés nem hozza létre a várt kimenetet

1.  Hibák vizsgálata helyi teszteléssel:

    - Azure Portal a **lekérdezés** lapon válassza a **teszt** elemet. A letöltött mintaadatok használatával [tesztelheti a lekérdezést](stream-analytics-test-query.md). Vizsgálja meg a hibákat, és próbálja meg kijavítani azokat.   
    - [A lekérdezést helyileg is tesztelheti](stream-analytics-live-data-local-testing.md) a Visual studióhoz vagy a [Visual Studio Code](visual-studio-code-local-run-live-input.md)-hoz készült Azure stream Analytics eszközökkel. 

2.  A [lekérdezések hibakeresése lépésről lépésre helyileg](debug-locally-using-job-diagram-vs-code.md) , a Azure stream Analytics Tools for Visual Studio Code-ban található Job diagram használatával. A feladatütemezés azt mutatja be, hogy az adatok hogyan áramlanak be a bemeneti forrásokból (Event hub, IoT Hub stb.) több lekérdezési lépéssel, végül pedig a kimeneti mosogatók használatával. Minden lekérdezési lépés a parancsfájlban definiált ideiglenes eredményhalmaz számára van leképezve a WITH utasítás használatával. Megtekintheti az adatokat, valamint a metrikákat az egyes köztes eredményhalmaz-készletekben a probléma forrásának megállapításához.

    ![A feladatütemezés előzetes verziójának eredménye](./media/debug-locally-using-job-diagram-vs-code/preview-result.png)

3.  Ha [**időbélyegzőt**](/stream-analytics-query/timestamp-by-azure-stream-analytics)használ, ellenőrizze, hogy az események időbélyegei nagyobbak-e a [feladatok kezdési idejénél](./stream-analytics-time-handling.md).

4.  Távolítsa el a gyakori buktatókat, például a következőket:
    - A lekérdezés [**Where**](/stream-analytics-query/where-azure-stream-analytics) záradéka szűrte az összes eseményt, ami megakadályozza a kimenet generálását.
    - A [**Cast**](/stream-analytics-query/cast-azure-stream-analytics) függvény meghiúsul, így a feladat sikertelen lesz. Ha el szeretné kerülni a leadott hibák beírását, használja a [**TRY_CAST**](/stream-analytics-query/try-cast-azure-stream-analytics) helyet.
    - Ha a Window functions funkciót használja, várja meg a teljes ablak időtartamát, hogy megjelenjen a lekérdezés kimenete.
    - Az események időbélyege megelőzi a feladatok kezdési idejét, és az események el lesznek dobva.
    - Az [**illesztési**](/stream-analytics-query/join-azure-stream-analytics) feltételek nem egyeznek. Ha nincsenek egyezések, nulla kimenet jelenik meg.

5.  Győződjön meg arról, hogy az esemény-rendezési házirendek a várt módon vannak konfigurálva. Lépjen a **Beállítások** menüpontra, és válassza az [**események rendezése**](./stream-analytics-time-handling.md)lehetőséget. A rendszer *nem* alkalmazza a házirendet, ha a **teszt** gombot használja a lekérdezés teszteléséhez. Ez az eredmény az egyik különbség a böngészőn belüli tesztelés és az éles környezetben futó feladatok között. 

6. Hibakeresés tevékenység-és erőforrás-naplók használatával:
    - Használjon [Tevékenységnaplók](../azure-resource-manager/management/view-activity-logs.md)használatát, és szűrje a hibákat a hibák azonosításához és hibakereséséhez.
    - A hibák azonosításához és hibakereséséhez használja a [feladatok erőforrás-naplóit](stream-analytics-job-diagnostic-logs.md) .

## <a name="resource-utilization-is-high"></a>Az erőforrás-használat magas

Győződjön meg arról, hogy kihasználja Azure Stream Analytics párhuzamos előnyeit. Megtudhatja, hogyan méretezheti Stream Analytics feladatok [lekérdezési párhuzamos](stream-analytics-parallelization.md) a bemeneti partíciók konfigurálásával és az elemzési lekérdezés definíciójának finomhangolásával.

Ha az Erőforrás kihasználtsága következetesen meghaladja a 80%-ot, a rendszer megnöveli a vízjel késleltetését, és a várakozó események száma nő, és a folyamatos átviteli egységeket is növelni kell. A magas kihasználtság azt jelzi, hogy a feladatoknak a maximálisan lefoglalt erőforrásokhoz való közelségét kell használnia.

## <a name="debug-queries-progressively"></a>Lekérdezések fokozatos hibakeresése

A valós idejű adatfeldolgozás során hasznos lehet annak ismerete, hogy az adatmennyiség hogyan néz ki a lekérdezés közepén. Ezt a Visual Studióban a feladatütemezés használatával tekintheti meg. Ha nem rendelkezik a Visual Studióval, további lépéseket is végrehajthat a köztes adatmennyiségek kinyomtatásához.

Mivel az Azure Stream Analytics feladatok bemenetei vagy lépései többször is beolvashatók, további SELECT INTO utasítások is megírhatók. Így a rendszer a közbenső adatokat a tárolóba helyezi, és lehetővé teszi az adatok helyességének vizsgálatát, mint ahogy a program hibakeresése során *megtekinti a változókat* .

Egy Azure Stream Analytics-feladatban az alábbi lekérdezés egy stream-bemenettel, két hivatkozási adatbemenettel és egy Azure Table Storage kimenettel rendelkezik. A lekérdezés összekapcsolja az adatokat az Event hub-ból és két hivatkozási blobot a név és a kategória információinak lekéréséhez:

![Példa Stream Analytics kijelölés a lekérdezésben](./media/stream-analytics-select-into/stream-analytics-select-into-query1.png)

Vegye figyelembe, hogy a művelet fut, de a kimenetben nem készül esemény. Az itt látható **figyelési** csempén láthatja, hogy a bemenet adatokat állít elő, de nem tudja, hogy az **illesztés** melyik lépése okozta az összes eldobott eseményt.

![A Stream Analytics monitorozási csempe](./media/stream-analytics-select-into/stream-analytics-select-into-monitor.png)

Ebben az esetben hozzáadhat néhány további KIJELÖLÉSt az utasításokhoz a köztes összekapcsolási eredmények és a bemenetből beolvasott adatok között.

Ebben a példában két új "ideiglenes kimenetet" adtunk hozzá. Az Ön számára bármilyen fogadó lehet. Itt az Azure Storage-t használjuk példaként:

![További SELECT INTO utasítások hozzáadása a Stream Analytics lekérdezéshez](./media/stream-analytics-select-into/stream-analytics-select-into-outputs.png)

Ezután a következőhöz hasonló módon írhatja át a lekérdezést:

![KIJELÖLÉS átírása Stream Analytics lekérdezésbe](./media/stream-analytics-select-into/stream-analytics-select-into-query2.png)

Most indítsa újra a feladatot, és néhány percen belül futtassa. Ezután lekérdezheti a temp1 és a temp2 a Visual Studio Cloud Explorerben a következő táblázatok létrehozásához:

**temp1 táblázat** 
 ![ SELECT INTO temp1 Table Stream Analytics Query](./media/stream-analytics-select-into/stream-analytics-select-into-temp-table-1.png)

**temp2 táblázat** 
 ![ SELECT INTO temp2 Table Stream Analytics Query](./media/stream-analytics-select-into/stream-analytics-select-into-temp-table-2.png)

Amint láthatja, a temp1 és a temp2 egyaránt tartalmaz adatokat, és a Name (név) oszlop helyesen van feltöltve a temp2. Mivel azonban még mindig nincsenek adatokat a kimenetben, valami nem stimmel:

![Válassza ki a output1 táblát, amely nem rendelkezik adatStream Analytics lekérdezéssel](./media/stream-analytics-select-into/stream-analytics-select-into-out-table-1.png)

Az adatmintavételezéssel szinte biztos lehet abban, hogy a probléma a második ILLESZTÉSsel van ellátva. A hivatkozásokat letöltheti a blobból, és megtekintheti a következőket:

![Válassza ki a ref Table Stream Analytics lekérdezést](./media/stream-analytics-select-into/stream-analytics-select-into-ref-table-1.png)

Amint láthatja, az ebben a hivatkozási adatokban található GUID formátuma eltér a (temp2) [from] oszlopának formátumával. Ezért nem érkeznek meg az output1 a várt módon.

Megjavíthatja az adatformátumot, feltöltheti a hivatkozási blobra, majd próbálkozzon újra:

![Válassza ki a temp Table Stream Analytics lekérdezést](./media/stream-analytics-select-into/stream-analytics-select-into-ref-table-2.png)

Ezúttal a kimenetben lévő adatok formázása és feltöltése a várt módon történik.

![Utolsó táblázat Stream Analytics lekérdezés kiválasztása](./media/stream-analytics-select-into/stream-analytics-select-into-final-table.png)

## <a name="get-help"></a>Segítség kérése

További segítségért próbálja ki a [Microsoft Q&a Azure stream Analytics kérdéseit](/answers/topics/azure-stream-analytics.html).

## <a name="next-steps"></a>Következő lépések

* [Bevezetés a Azure Stream Analyticsba](stream-analytics-introduction.md)
* [Get started using Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md) (Bevezetés az Azure Stream Analytics használatába)
* [Scale Azure Stream Analytics jobs (Azure Stream Analytics-feladatok méretezése)](stream-analytics-scale-jobs.md)
* [Azure Stream Analytics Query Language Reference (Referencia az Azure Stream Analytics lekérdezési nyelvhez)](/stream-analytics-query/stream-analytics-query-language-reference)
* [Az Azure Stream Analytics felügyeleti REST API referenciája](/rest/api/streamanalytics/)
