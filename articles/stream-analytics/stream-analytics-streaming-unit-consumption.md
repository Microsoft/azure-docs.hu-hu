---
title: Folyamatos átviteli egységek Azure Stream Analytics
description: Ez a cikk a folyamatos átviteli egységek beállítását és a Azure Stream Analytics teljesítményét befolyásoló egyéb tényezőket ismerteti.
author: JSeb225
ms.author: jeanb
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 08/28/2020
ms.openlocfilehash: a5a0e6feba966d2d10c5cd36432c3d5db172a795
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98020009"
---
# <a name="understand-and-adjust-streaming-units"></a>A streamelési egységek ismertetése és módosítása

A streaming Units (SUs) a Stream Analytics feladatok végrehajtásához lefoglalt számítástechnikai erőforrásokat jelöli. Minél magasabb az SU-k száma, annál több processzor- és memória-erőforrás van lefoglalva a feladathoz. Ez a kapacitás lehetővé teszi, hogy a lekérdezési logikára koncentráljon, és eldöntse, hogy a hardvert hogyan kell kezelni a Stream Analytics-feladatok időben történő futtatásához.

A kis késésű streamfeldolgozás érdekében az Azure Stream Analytics-feladatok minden feldolgozást a memóriában hajtanak végre. A memória elfogyása esetén a folyamatos átviteli feladatok sikertelenek. Ennek eredményeképpen éles feladatokhoz fontos figyelni a folyamatos átviteli feladatok erőforrás-felhasználását, és gondoskodni kell arról, hogy elegendő erőforrás legyen kiosztva a 24/7-ot futtató feladatok megtartásához.

A (z)% kihasználtsági metrika, amely 0% és 100% közötti tartományba esik, a számítási feladatok memória-felhasználását írja le. Minimális helyigényű folyamatos átviteli feladatoknál ez a metrika általában 10% és 20% között van. Ha a SU%-kihasználtság magas (meghaladja a 80%-ot), vagy ha a bemeneti események várakozó kapnak (akár alacsony SU%-os kihasználtsággal is, mivel nem jeleníti meg a CPU-használatot), valószínűleg több számítási erőforrásra van szükség, amelyhez az SUs számának növelésére van szükség. Érdemes megtartani a SU metrika 80% alatti értékét az alkalmi tüskék beszámításához. A megnövekedett számítási feladatokra és a folyamatos átviteli egységek növelésére való reagáláshoz érdemes lehet 80%-os riasztást beállítani a SU kihasználtsági metrikán. Azt is megteheti, hogy a vízjel késleltetése és a várakozó események mérőszámai hatással vannak a szolgáltatásra.

## <a name="configure-stream-analytics-streaming-units-sus"></a>Stream Analytics streaming Units (SUs) konfigurálása
1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

2. Az erőforrások listájában keresse meg a méretezni kívánt Stream Analytics-feladatot, majd nyissa meg. 

3. A feladatok lapon a **configure (Konfigurálás** ) fejléc alatt válassza a **skála** lehetőséget. Az SUs alapértelmezett száma 3 a feladatok létrehozásakor.

    ![Azure Portal Stream Analytics feladatok konfigurálása][img.stream.analytics.preview.portal.settings.scale]
    
4. A feladatokhoz tartozó SUs beállításához használja a csúszkát. Figyelje meg, hogy az adott SU-beállításokra korlátozódik. 
5. Megváltoztathatja a feladathoz hozzárendelt SUs számát még akkor is, ha fut. Ez nem lehetséges, ha a feladattípus [nem particionált kimenetet](./stream-analytics-parallelization.md#query-using-non-partitioned-output) használ, vagy [több lépésből álló lekérdezéssel rendelkezik, és értékek alapján eltérő partíciót](./stream-analytics-parallelization.md#multi-step-query-with-different-partition-by-values)tartalmaz. Lehet, hogy a feladatok futtatásakor a SU értékek közül választhat. 

## <a name="monitor-job-performance"></a>Feladatok teljesítményének figyelése
A Azure Portal használatával nyomon követheti a feladatok átviteli sebességét:

![Feladatok Azure Stream Analytics figyelése][img.stream.analytics.monitor.job]

Kiszámítja a munkaterhelés várható átviteli sebességét. Ha az átviteli sebesség a vártnál kisebb, állítsa be a bemeneti partíciót, hangolja be a lekérdezést, és adja hozzá az SUs-t a feladathoz.

## <a name="how-many-sus-are-required-for-a-job"></a>Hány SU-ra van szükség egy feladathoz?

Egy adott feladathoz szükséges SUs számának kiválasztása a bemenetek és a feladatban definiált lekérdezés partíciójának konfigurációjától függ. A **skála** lapon állíthatja be a megfelelő számú SUs-értéket. Az ajánlott eljárás a szükségesnél több SUs kiosztása. A Stream Analytics feldolgozó motor a további memória lefoglalásának díja szerint optimalizálja a késést és az átviteli sebességet.

Általánosságban elmondható, hogy az ajánlott eljárás az, ha 6 SUs-t használ olyan lekérdezések esetén, amelyek nem használják a **particionálást**. Ezt követően határozza meg az édes helyet egy próba-és hiba módszer használatával, amelyben módosítja a SUs számát a reprezentatív mennyiségű adat átadása után, és vizsgálja meg a SU% kihasználtsági mérőszámot. A Stream Analytics-feladatok által felhasználható folyamatos átviteli egységek maximális száma a feladatokhoz megadott lekérdezés lépéseinek és az egyes lépésekben lévő partíciók számának a számától függ. A korlátozásokról további információt [itt](./stream-analytics-parallelization.md#calculate-the-maximum-streaming-units-of-a-job)talál.

Az SUs megfelelő számának kiválasztásával kapcsolatos további információkért tekintse meg a következő oldalt: [Azure stream Analytics feladatok méretezése az átviteli sebesség növelése érdekében](stream-analytics-scale-jobs.md)

> [!Note]
> Annak kiválasztása, hogy egy adott feladathoz hány SUs szükséges, a bemenetek és a feladathoz megadott lekérdezés partíciójának konfigurációjától függ. Kiválaszthatja a kvótát a feladatokhoz az SUs-ben. Alapértelmezés szerint minden Azure-előfizetés legfeljebb 500 SUs-kvótával rendelkezik egy adott régió összes elemzési feladatához. A kvótán túli előfizetések SUs-növeléséhez forduljon [Microsoft ügyfélszolgálatahoz](https://support.microsoft.com). A SUs/feladatok érvényes értékei: 1, 3, 6, és akár 6-onként is.

## <a name="factors-that-increase-su-utilization"></a>A streamelési egységek százalékos kihasználtságát növelő tényezők 

Az időbeli (időalapú) lekérdezési elemek a Stream Analytics által biztosított állapot-nyilvántartó operátorok alapvető készletei. Stream Analytics a műveletek állapotát belsőleg kezeli a felhasználó nevében, a memóriahasználat kezelésével, a rugalmasság ellenőrzésével és az állapot helyreállításával a szolgáltatás frissítésekor. Annak ellenére, hogy Stream Analytics teljes mértékben felügyeli az állapotokat, számos ajánlott gyakorlattal kapcsolatos ajánlást kell figyelembe venni a felhasználók számára.

Vegye figyelembe, hogy az összetett lekérdezési logikával rendelkező feladatok akkor is magas SU%-os kihasználtsággal rendelkezhetnek, ha a bemeneti események nem folyamatosan érkeznek. Ez a bemeneti és a kimeneti események hirtelen nyársa után fordulhat elő. A feladat továbbra is megtarthatja a memóriában az állapotot, ha a lekérdezés összetett.

A SU% kihasználtsága hirtelen eltérhet 0-ra egy rövid ideig, mielőtt visszatér a várt szintre. Ez átmeneti hibák vagy a rendszer által kezdeményezett frissítések miatt fordul elő. Előfordulhat, hogy a feladatokhoz tartozó folyamatos átviteli egységek száma nem csökkenti a SU% kihasználtságot, ha a lekérdezés nem [teljesen párhuzamos](./stream-analytics-parallelization.md).

Ha a kihasználtságot egy adott időszakban hasonlítja össze, használja az [esemény-díjszabási metrikákat](stream-analytics-monitoring.md). A InputEvents és a OutputEvents metrikák azt mutatják be, hogy hány esemény olvasása és feldolgozása történt meg. Vannak olyan mérőszámok, amelyek jelzik a hibák számát, valamint a deszerializálási hibákat is. Ha az események száma/idő egységenként nő, a legtöbb esetben a SU% nő.

## <a name="stateful-query-logic-in-temporal-elements"></a>Állapot-nyilvántartó lekérdezési logika az időbeli elemekben
Azure Stream Analytics feladatok egyik egyedi funkciója az állapot-nyilvántartó feldolgozás végrehajtása, például ablakos összesítések, időbeli illesztések és időbeli analitikai függvények. Ezek az operátorok megőrzik az állapotadatok állapotát. A lekérdezési elemek maximális ablakméret hét nap. 

Az időszakos ablak fogalma számos Stream Analytics lekérdezési elemben jelenik meg:
1. Ablakos összesítések: kivonási, átugró és csúszó ablakok CSOPORTOSÍTÁSa

2. Időbeli illesztések: csatlakozás a DATEDIFF függvénnyel

3. Időbeli analitikai függvények: ISFIRST, utolsó és késés a korlát IDŐTARTAMával

Az alábbi tényezők befolyásolják a felhasznált memóriát (a folyamatos átviteli egységek metrikájának részét) Stream Analytics feladatok esetében:

## <a name="windowed-aggregates"></a>Ablakos összesítések
Egy ablakos összesítéshez felhasznált memória (állapot mérete) nem mindig az ablak méretével arányos. Ehelyett a felhasznált memória az adatmennyiség, vagy a csoportok száma az egyes időablakokban is arányos.


Például a következő lekérdezésben a társított szám a `clusterid` lekérdezés kardinálisa. 

   ```sql
   SELECT count(*)
   FROM input 
   GROUP BY  clusterid, tumblingwindow (minutes, 5)
   ```

Az előző lekérdezésben felmerülő problémák enyhítése érdekében az Event hub által particionált, és a lekérdezés vertikális felskálázásával kapcsolatos problémákat is elküldheti, ha `clusterid` a rendszeren az alábbi példában látható módon feldolgozza az egyes bemeneti partíciókat a **Partition by** paranccsal.

   ```sql
   SELECT count(*) 
   FROM input PARTITION BY PartitionId
   GROUP BY PartitionId, clusterid, tumblingwindow (minutes, 5)
   ```

A rendszer több csoport között osztja el a lekérdezést a particionálása után. Ennek eredményeképpen az `clusterid` egyes csomópontokra érkező értékek száma csökken, így csökkentve a csoportosítási operátor által felszámított számos értéket. 

Az Event hub-partíciókat a csoportosítási kulcsnak kell particionálnia, hogy elkerülje a csökkentési lépés szükségességét. További információ: [Event Hubs Overview (áttekintés](../event-hubs/event-hubs-about.md)). 

## <a name="temporal-joins"></a>Időbeli illesztések
Az ideiglenes illesztések által felhasznált memória (az állapot mérete) arányos az illesztés időbeli kígyózik-helyiségében lévő események számával, amely az esemény bemeneti sebessége, szorozva a csatorna méretével. Ez azt jelenti, hogy az összekapcsolások által felhasznált memória a DateDiff időtartományával arányos, és az események átlagos száma szorzata.

Az illesztésben szereplő nem egyező események száma befolyásolja a lekérdezés memóriahasználat használatát. A következő lekérdezés a kattintásokat generáló oldalmegjelenéseket keresi:

   ```sql
   SELECT clicks.id
   FROM clicks 
   INNER JOIN impressions ON impressions.id = clicks.id AND DATEDIFF(hour, impressions, clicks) between 0 AND 10.
   ```

Ebben a példában lehetséges, hogy sok hirdetés látható, és kevés ember kattint rá, és meg kell őriznie az összes eseményt az időablakban. A felhasznált memória arányos az ablak méretével és az események gyakoriságával. 

Ennek megoldásához küldje el az eseményeket az Event hub által particionált, az illesztési kulcsok (ebben az esetben azonosító) alapján, és bővítse a lekérdezést úgy, hogy lehetővé teszi, hogy a rendszeren az egyes bemeneti partíciókat külön lehessen feldolgozni a  **Partition by** paranccsal, az alábbiak szerint:

   ```sql
   SELECT clicks.id
   FROM clicks PARTITION BY PartitionId
   INNER JOIN impressions PARTITION BY PartitionId 
   ON impression.PartitionId = clicks.PartitionId AND impressions.id = clicks.id AND DATEDIFF(hour, impressions, clicks) between 0 AND 10 
   ```

A rendszer több csoport között osztja el a lekérdezést a particionálása után. Ennek eredményeképpen az egyes csomópontokra érkező események száma csökken, ami csökkenti a csatlakozás ablakban megőrzött állapot méretét. 

## <a name="temporal-analytic-functions"></a>Időbeli analitikai függvények
Az időbeli elemzési függvények által felhasznált memória (az állapot mérete) arányos az esemény arányával, szorozva az időtartammal. Az analitikai függvények által felhasznált memória nem arányos az ablak méretével, hanem a partíciók számával az egyes időablakokban.

A szervizelés hasonló az időbeli csatlakozáshoz. A lekérdezés felskálázása a **Partition by** paranccsal végezhető el. 

## <a name="out-of-order-buffer"></a>Nem sorrendben lévő puffer 
A felhasználó beállíthatja a nem sorrendben lévő puffer méretét az események rendezése konfigurációs ablaktáblán. A puffer az ablak időtartamára vonatkozó bemenetek tárolására szolgál, és átrendezi őket. A puffer mérete az esemény bemeneti arányának a megrendelési ablak méretétől számított értékével arányos. Az ablak alapértelmezett mérete 0. 

A nem megrendelési puffer túlcsordulásának szervizeléséhez kibővíthető lekérdezés a **Partition by** használatával. A rendszer több csoport között osztja el a lekérdezést a particionálása után. Ennek eredményeképpen az egyes csomópontokra érkező események száma csökken, így csökkentve az egyes átrendezési pufferekben lévő események számát. 

## <a name="input-partition-count"></a>Bemeneti partíciók száma 
Egy adott feladathoz tartozó összes bemeneti partíció pufferrel rendelkezik. Minél nagyobb számú bemeneti partíció, annál több erőforrást használ a feladatok. Az egyes folyamatos átviteli egységek esetében a Azure Stream Analytics nagyjából 1 MB/s bemenetet dolgozhat fel. Ezért optimalizálható az Stream Analytics folyamatos átviteli egységek számának és az Event hub-ban található partíciók számának egyeztetésével. 

Általában egy folyamatos átviteli egységgel konfigurált feladatok elegendőek az Event hub két partícióval (amely az Event hub számára minimálisan szükséges). Ha az Event hub több partícióval rendelkezik, a Stream Analytics-feladatok több erőforrást használnak, de nem feltétlenül az Event hub által biztosított extra átviteli sebességet használják. 

6 folyamatos átviteli egységgel rendelkező feladatokhoz szükség lehet az Event hub 4 vagy 8 partícióra. Azonban Kerülje a túl sok felesleges partíciót, mivel ez túlzott erőforrás-használatot okoz. Például egy legalább 16 partíciót tartalmazó Event hub egy olyan Stream Analytics-feladatokban, amely 1 folyamatos átviteli egységgel rendelkezik. 

## <a name="reference-data"></a>Hivatkozási érték 
A gyors keresés érdekében az ASA-ben lévő hivatkozási adatmennyiség betöltődik a memóriába. Az aktuális implementációban a hivatkozási adattal rendelkező összes csatlakoztatási művelet a memóriában tárolja a hivatkozási adatmennyiséget, még akkor is, ha ugyanazokat a hivatkozási adatmennyiségeket többször is csatlakoztatja. A-sel rendelkező lekérdezések esetében minden partíció rendelkezik a hivatkozási adatmásolattal **, így** a partíciók teljes mértékben le vannak választva. A szorzó hatására a memóriahasználat gyorsan elvégezhető, ha több partícióval többször is összekapcsolja a hivatkozási adatokat.  

### <a name="use-of-udf-functions"></a>UDF függvények használata
UDF-függvény hozzáadásakor Azure Stream Analytics betölti a JavaScript-futtatókörnyezetet a memóriába. Ez hatással lesz a SU%-ra.

## <a name="next-steps"></a>Következő lépések
* [Párhuzamosítható-lekérdezések létrehozása a Azure Stream Analyticsban](stream-analytics-parallelization.md)
* [Azure Stream Analytics feladatok méretezése az átviteli sebesség növelése érdekében](stream-analytics-scale-jobs.md)

<!--Image references-->

[img.stream.analytics.monitor.job]: ./media/stream-analytics-scale-jobs/StreamAnalytics.job.monitor-NewPortal.png
[img.stream.analytics.configure.scale]: ./media/stream-analytics-scale-jobs/StreamAnalytics.configure.scale.png
[img.stream.analytics.perfgraph]: ./media/stream-analytics-scale-jobs/perf.png
[img.stream.analytics.streaming.units.scale]: ./media/stream-analytics-scale-jobs/StreamAnalyticsStreamingUnitsExample.jpg
[img.stream.analytics.preview.portal.settings.scale]: ./media/stream-analytics-scale-jobs/StreamAnalyticsPreviewPortalJobSettings-NewPortal.png
