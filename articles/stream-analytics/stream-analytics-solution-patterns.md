---
title: Az Azure Stream Analytics megoldásmintái
description: Ismerje meg a Azure Stream Analytics gyakori megoldási mintáit, például az irányítópultot, az események üzenetkezelését, az adattárakat, a hivatkozási adatelemzést és a figyelést.
author: sidramadoss
ms.author: sidram
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/21/2019
ms.openlocfilehash: 1bd3c1099344bd266d7e3bc153613daaecfb412a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98020315"
---
# <a name="azure-stream-analytics-solution-patterns"></a>Az Azure Stream Analytics megoldásmintái

Az Azure-ban számos más szolgáltatáshoz hasonlóan a Stream Analytics a legjobb megoldás, ha egy nagyobb végpontok közötti megoldást szeretne létrehozni a többi szolgáltatással. Ez a cikk az egyszerű Azure Stream Analytics megoldásokat és különböző építészeti mintákat tárgyalja. Ezeket a mintákat felépítve összetettebb megoldásokat fejleszthet. A cikkben ismertetett minták számos különböző forgatókönyvben használhatók. Példák a forgatókönyvekre jellemző mintákra az [Azure-megoldás architektúráján](https://azure.microsoft.com/solutions/architecture/?product=stream-analytics).

## <a name="create-a-stream-analytics-job-to-power-real-time-dashboarding-experience"></a>Stream Analytics-feladatok létrehozása a valós idejű irányítópult-létrehozási élmény érdekében

A Azure Stream Analytics használatával gyorsan felállíthatók a valós idejű irányítópultok és riasztások. Egy egyszerű megoldás Event Hubs vagy IoT Hubból származó eseményeket tölt be, és [az Power bi irányítópultot adatfolyam-adatkészlettel táplálja](/power-bi/service-real-time-streaming). További információkért tekintse meg a következő témakört: a [csalárd hívási adatok elemzése stream Analytics és az eredmények megjelenítése Power bi irányítópulton](stream-analytics-real-time-fraud-detection.md).

![ASA Power BI irányítópult](media/stream-analytics-solution-patterns/power-bi-dashboard.png)

Ez a megoldás csak néhány percen belül felépíthető Azure Portal. Nincs jelentős kódolási probléma, és az SQL nyelv az üzleti logika kifejezésére szolgál.

Ez a megoldási minta az eseményforrás legalacsonyabb késését kínálja a böngésző Power BI irányítópultjának. Azure Stream Analytics az egyetlen Azure-szolgáltatás, amely ezen beépített képességgel rendelkezik.

## <a name="use-sql-for-dashboard"></a>Az SQL használata az irányítópulton

A Power BI irányítópult alacsony késést biztosít, de nem használható teljes értékű Power BI-jelentések készítéséhez. Az általános jelentéskészítési minta az, hogy az adatokat SQL Database először kiírja. Ezután használja a Power BI SQL-összekötőjét, hogy lekérdezze az SQL-t a legfrissebb adatértékekhez.

![ASA SQL-irányítópult](media/stream-analytics-solution-patterns/sql-dashboard.png)

A SQL Database használata nagyobb rugalmasságot biztosít, de valamivel nagyobb késéssel jár. Ez a megoldás optimális olyan feladatokhoz, amelyeknél a késési követelmények egy másodpercnél nagyobbak. Ezzel a módszerrel maximalizálhatja Power BI képességeit, hogy további szeleteket és kockákat tartalmazzon a jelentésekhez, és sokkal több vizualizációs lehetőséget biztosítson. Más irányítópult-megoldások, például a tabló használatának rugalmassága is megszerezhető.

Az SQL nem a nagy átviteli sebességű adattár. A Azure Stream Analytics SQL Database maximális átviteli sebessége jelenleg körülbelül 24 MB/s. Ha a megoldásban szereplő eseményforrás magasabb sebességű adatokat hoz létre, akkor a kimeneti sebesség csökkentése érdekében az SQL-re kell használnia a Stream Analytics feldolgozási logikáját. A szűrés, az ablakos összesítések, az időbeli illesztések és az analitikai függvények is használhatók. Az SQL-re irányuló kimeneti sebesség tovább optimalizálható a [Azure stream Analytics kimenet Azure SQL Databasera](stream-analytics-sql-output-perf.md)című témakörben ismertetett technikák használatával.

## <a name="incorporate-real-time-insights-into-your-application-with-event-messaging"></a>Valós idejű betekintést nyerhet az alkalmazásba az Event Messaging használatával

Stream Analytics a második legnépszerűbb használata a valós idejű riasztások létrehozásához. Ebben a megoldási mintában a Stream Analytics üzleti logikája az [időbeli és térbeli minták](stream-analytics-geospatial-functions.md) vagy [rendellenességek](stream-analytics-machine-learning-anomaly-detection.md)észlelésére, majd riasztási jelek létrehozására szolgál. Azonban az irányítópult-megoldástól eltérően, ahol a Stream Analytics előnyben részesített végpontként használ Power BI, több köztes adatnyelőt is használhat. Ezek a mosogatók a következők: Event Hubs, Service Bus és Azure Functions. Az alkalmazás-szerkesztőnek el kell döntenie, hogy melyik adatfogadó a legmegfelelőbb a forgatókönyvhöz.

Az alárendelt esemény fogyasztói logikáját a meglévő üzleti munkafolyamatban riasztások létrehozásához kell megvalósítani. Mivel Azure Functionsban egyéni logikát alkalmazhat, Azure Functions a leggyorsabb módszer az integráció végrehajtásához. Az Azure Function Stream Analytics-feladathoz tartozó kimenetként való használatának oktatóanyaga Azure Stream Analytics- [feladatok futtatási Azure Functionsjában](stream-analytics-with-azure-functions.md)található. A Azure Functions különböző típusú értesítéseket is támogat, beleértve a szöveget és az e-maileket. Az integrációhoz a logikai alkalmazás is használható, Event Hubs Stream Analytics és a logikai alkalmazás között.

![ASA-esemény üzenetkezelő alkalmazás](media/stream-analytics-solution-patterns/event-messaging-app.png)

A Event Hubs viszont a legrugalmasabb integrációs pontot kínálja. Számos más szolgáltatás, például az Azure Adatkezelő és az Time Series Insights a Event Hubsból származó eseményeket képes használni. A szolgáltatások közvetlenül csatlakoztathatók a Azure Stream Analytics Event Hubs-gyűjtőhöz a megoldás befejezéséhez. Az Azure-ban az ilyen integrációs forgatókönyvek esetében a legmagasabb átviteli sebességű üzenetkezelési közvetítő is elérhető. Event Hubs

## <a name="dynamic-applications-and-websites"></a>Dinamikus alkalmazások és webhelyek

A Azure Stream Analytics és az Azure Signaler szolgáltatás használatával egyéni valós idejű vizualizációkat hozhat létre, például irányítópultot vagy térképes vizualizációt. A Signaler használatával a webes ügyfelek frissíthetik a dinamikus tartalmakat, és valós időben jeleníthetők meg.

![ASA dinamikus alkalmazás](media/stream-analytics-solution-patterns/dynamic-app.png)

## <a name="incorporate-real-time-insights-into-your-application-through-data-stores"></a>Valós idejű betekintést nyerhet az alkalmazásba az adattárakon keresztül

A legtöbb Web Services és webalkalmazás jelenleg egy kérelem/válasz mintát használ a bemutató réteg kiszolgálására. A kérelem/válasz minta egyszerűen felépíthető, és az állapot nélküli előtér és a méretezhető tárolók, például a Cosmos DB használatával egyszerűen méretezhető, és alacsony a válaszadási idő.

A magas adatmennyiség gyakran okoz szűk keresztmetszetet a szifilisz-alapú rendszerekben. Az [Event beszerzési megoldás mintája](/azure/architecture/patterns/event-sourcing) a teljesítmény szűk keresztmetszetének kezelésére szolgál. Az időbeli mintázatok és az elemzések is nehézkesek és nem hatékonyak a hagyományos adattárból való kinyeréshez. A modern, nagy mennyiségű adatvezérelt alkalmazások gyakran adatfolyam-alapú architektúrát alkalmaznak. Azure Stream Analytics a mozgásban lévő adatok számítási motorja egy sarokköve az adott architektúrában.

![ASA-esemény beszerzése alkalmazás](media/stream-analytics-solution-patterns/event-sourcing-app.png)

Ebben a megoldási mintában az eseményeket Azure Stream Analytics szerint dolgozzák fel és összesíti az adattárakba. Az alkalmazási réteg a hagyományos kérés/válasz minta használatával kommunikál az adattárakkal. A nagyszámú esemény valós idejű feldolgozásának Stream Analyticse miatt az alkalmazás nagy mértékben skálázható az adattár rétegének tömeges felskálázása nélkül. Az adattár-réteg lényegében egy jelentős nézet a rendszeren. [Azure Cosmos DB Azure stream Analytics kimenete azt](stream-analytics-documentdb-output.md) írja le, hogyan használják a Cosmos db stream Analytics kimenetként.

A valós alkalmazásokban, ahol a feldolgozási logika összetett, és a logika bizonyos részeit egymástól függetlenül kell frissíteni, több Stream Analytics feladat is összeállítható együtt a közvetítői esemény-átvitelszervező Event Hubsával.

![ASA komplex esemény-beszerzési alkalmazás](media/stream-analytics-solution-patterns/event-sourcing-app-complex.png)

Ez a minta javítja a rendszerek rugalmasságát és kezelhetőségét. Azonban bár a Stream Analytics pontosan egyszer garantálja a feldolgozást, némi esély van arra, hogy az ismétlődő események a közbenső Event Hubsban is megjelenhetnek. Fontos, hogy az alsóbb rétegbeli Stream Analytics feladatokhoz a logikai kulcsok használatával lookback az eseményeket. Az események kézbesítésével kapcsolatos további információkért lásd: [esemény-kézbesítési garanciák](/stream-analytics-query/event-delivery-guarantees-azure-stream-analytics) referenciája.

## <a name="use-reference-data-for-application-customization"></a>Referenciák használata az alkalmazások testreszabásához

A Azure Stream Analytics hivatkozási adatszolgáltatása kifejezetten a végfelhasználói testreszabáshoz készült, például a riasztási küszöbérték, a feldolgozási szabályok és a [geofences](geospatial-scenarios.md). Az alkalmazási réteg fogadhatja a paraméterek változásait, és tárolhatja azokat SQL Databaseban. A Stream Analytics-feladatokban rendszeresen lekérdezéseket végeznek az adatbázis változásairól, és a testreszabási paramétereket elérhetővé teszik egy hivatkozással való összekapcsolással. Az alkalmazások testreszabásával kapcsolatos tudnivalókat lásd: [SQL-referenciák](sql-reference-data.md) és [hivatkozási adatok csatlakoztatása](/stream-analytics-query/reference-data-join-azure-stream-analytics).

Ez a minta olyan szabályok motorjának megvalósítására is használható, amelyekben a szabályok küszöbértékei a hivatkozási adatok alapján vannak meghatározva. További információ a szabályokról: [konfigurálható küszöbérték-alapú szabályok feldolgozása Azure stream Analyticsban](stream-analytics-threshold-based-rules.md).

![ASA-hivatkozási adatalkalmazás](media/stream-analytics-solution-patterns/reference-data-app.png)

## <a name="add-machine-learning-to-your-real-time-insights"></a>Machine Learning hozzáadása a valós idejű adatfelismerésekhez

A beépített [anomáliák észlelési modellje](stream-analytics-machine-learning-anomaly-detection.md) Azure stream Analytics a valós idejű alkalmazások Machine learning bevezetésének kényelmes módja. A Machine Learning igények szélesebb köre esetén lásd: a [Azure stream Analytics integrálása Azure Machine learning pontozási szolgáltatásával](stream-analytics-machine-learning-integration-tutorial.md).

A speciális felhasználók számára, akik az online képzést és a pontozást is ugyanabba a Stream Analytics folyamatba kívánják beépíteni, ennek a példának a segítségével a [lineáris regressziót](stream-analytics-high-frequency-trading.md)láthatjuk.

![ASA Machine Learning alkalmazás](media/stream-analytics-solution-patterns/machine-learning-app.png)

## <a name="real-time-data-warehousing"></a>Valós idejű adattárház

Egy másik gyakori minta a valós idejű adattárház, más néven adatfolyam-adattárház. Az Event Hubs és IoT Hub az alkalmazásból érkező eseményeken kívül a IoT Edge- [on futó Azure stream Analytics](stream-analytics-edge.md) az adatok megtisztítását, az adatok csökkentését, valamint az adattár és a továbbítási igények teljesítését is felhasználhatja. A IoT Edge-on futó Stream Analytics könnyedén képes kezelni a sávszélesség korlátozását és a kapcsolati problémákat a rendszeren. A Stream Analytics az Azure szinapszis Analytics szolgáltatásba való írás során akár 200 MB/s sebességű átviteli sebességet is támogat.

![ASA-adattárház](media/stream-analytics-solution-patterns/data-warehousing.png)


## <a name="archiving-real-time-data-for-analytics"></a>Valós idejű adatelemzések archiválása

A legtöbb adatelemzési és elemzési tevékenység továbbra is offline állapotban van. Az adatok archiválása Azure Stream Analytics Azure Data Lake Store Gen2 kimenet és a parketta kimeneti formátuma alapján lehetséges. Ez a képesség eltávolítja a súrlódást, hogy közvetlenül a Azure Data Lake Analyticsba, Azure Databricksba és az Azure HDInsight irányítsa az adatcsatornákat. A Azure Stream Analytics a megoldásban közel valós idejű ETL-motorként használható. Az archivált adatok Data Lake különböző számítási motorokkal is megismerhetik.

> [!div class="mx-imgBorder"]
> ![ASA offline Analitika](media/stream-analytics-solution-patterns/offline-analytics.png)

## <a name="use-reference-data-for-enrichment"></a>Referenciák használata a dúsításhoz

Az adatgazdagítás gyakran az ETL-motorok követelménye. Azure Stream Analytics támogatja a SQL Database és az Azure Blob Storage [-ból származó adatokkal való](stream-analytics-use-reference-data.md) gazdagodás támogatását. Az Adatbővítés a Azure Data Lake és az Azure szinapszis Analytics szolgáltatásban is elvégezhető.


![ASA offline elemzés az adatgazdagítás használatával](media/stream-analytics-solution-patterns/offline-analytics-enriched.png)

## <a name="operationalize-insights-from-archived-data"></a>Működővé tenni az archivált adatokból

Ha az offline elemzési mintát a közel valós idejű alkalmazási mintával kombinálja, létrehozhat egy visszajelzési hurkot. A visszajelzési hurok lehetővé teszi, hogy az alkalmazás automatikusan igazodjon az adatmodellek módosításához. Ez a visszajelzési hurok olyan egyszerű lehet, mint a riasztások küszöbértékének módosítása vagy a Machine Learning modellek átképzésének összetett módja. Ugyanez a megoldási architektúra alkalmazható a felhőben és a IoT Edge futó ASA-feladatokra is.

![ASA-bepillantást operacionalizálási](media/stream-analytics-solution-patterns/insights-operationalization.png)

## <a name="how-to-monitor-asa-jobs"></a>Az ASA-feladatok figyelése

Azure Stream Analytics az 24/7-es feladatok futtatásával a bejövő események valós időben történő feldolgozását is elvégezheti. A teljes alkalmazás állapota elengedhetetlen a szükséges üzemidő biztosításához. Noha a Stream Analytics az iparág egyetlen Streaming Analytics szolgáltatása, amely  [99,9%-os rendelkezésre állási garanciát](https://azure.microsoft.com/support/legal/sla/stream-analytics/v1_0/)kínál, továbbra is felmerülhet. Az évek során Stream Analytics metrikákat, naplókat és feladatokat vezetett be a feladatok állapotának megfelelően. Mindegyik Azure Monitor szolgáltatáson keresztül történik, és tovább exportálhatók a OMS. További információ: [stream Analytics feladatok figyelésének ismertetése és a lekérdezések figyelése](stream-analytics-monitoring.md).

![ASA-figyelés](media/stream-analytics-solution-patterns/monitoring.png)

Két fontos szempontot kell figyelni:

- [Sikertelen feladatok állapota](job-states.md)

    Először is ellenőrizze, hogy fut-e a feladatok. A futó állapotban lévő feladatok nélkül nem jönnek létre új metrikák vagy naplók. A feladatok sikertelen állapotra válthatnak különböző okok miatt, beleértve a magas SU kihasználtsági szintet (azaz az erőforrások futtatását).

- [Vízjel késleltetési metrikái](https://azure.microsoft.com/blog/new-metric-in-azure-stream-analytics-tracks-latency-of-your-streaming-pipeline/)

    Ez a mérőszám azt tükrözi, hogy a feldolgozási folyamat milyen messze van a falióra-idő (másodperc) alatt. A késések némelyike a belső feldolgozási logikához van hozzárendelve. Ennek eredményeképpen a növekvő trend figyelése sokkal fontosabb, mint az abszolút érték monitorozása. Az állandó állapot késleltetését az alkalmazás kialakításának kell megcéloznia, nem pedig figyeléssel vagy riasztásokkal.

Hiba esetén a tevékenységek naplói és [diagnosztikai naplói](stream-analytics-job-diagnostic-logs.md) a legjobb hely a hibák keresésének megkezdéséhez.

## <a name="build-resilient-and-mission-critical-applications"></a>Rugalmas és kritikus fontosságú alkalmazások létrehozása

Függetlenül attól, hogy Azure Stream Analytics SLA-garanciát, és hogy mennyire körültekintően futtatja a végpontok közötti alkalmazást, kiesések történnek. Ha az alkalmazása kritikus fontosságú, a leállás érdekében elő kell készíteni a leállásokat, hogy megfelelően helyreállítsa a helyreállítást.

A riasztási alkalmazások esetében a legfontosabb dolog a következő riasztás észlelése. Dönthet úgy, hogy újraindítja a feladatot a helyreállítás aktuális idejétől a korábbi riasztások figyelmen kívül hagyásával. A feladathoz tartozó kezdési idő szemantikai értékének első kimeneti időpontja, nem az első bemeneti idő. A bemenet visszacsévélése megfelelő időt biztosít, hogy az első kimenet a megadott időben befejeződjön és helyes legyen. A rendszer nem kap részleges összesítéseket, és váratlanul elindítja a riasztásokat.

Dönthet úgy is, hogy a kimenetet a múltban bizonyos ideig elindítják. A Event Hubs és a IoT Hub adatmegőrzési szabályzata is ésszerű mennyiségű adattal rendelkezik, hogy a feldolgozás a múltban is megtörténjen. A kompromisszum azt mutatja be, hogy milyen gyorsan lehet felvenni az aktuális időpontot, és megkezdeni a kellő időben új riasztások létrehozását. Az adat az idő múlásával gyorsan elveszíti az értékét, ezért fontos, hogy az aktuális időpontot gyorsan fel lehessen fogni. A gyors felzárkózás kétféleképpen végezhető el:

- További erőforrások (SU) kiépítése a felzárkózás során.
- Újraindítás az aktuális időpontból.

Az aktuális időpontra való újraindításhoz egyszerűen elvégezhető a kompromisszum, amely során a rendszer a feldolgozás során elhagy egy rést. Az újraindítással a riasztási forgatókönyvek esetében lehet OK, de az irányítópult-forgatókönyvek esetében problematikus lehet, és az archiválási és adatraktározási forgatókönyvek nem kezdők.

A több erőforrás kiépítés felgyorsíthatja a folyamatot, de a feldolgozási sebesség megváltozásának hatása összetett.

- Ellenőrizze, hogy a feladatok nagyobb számú SUs-értékre vannak-e méretezhetők. Nem minden lekérdezés méretezhető. Győződjön meg arról, hogy a lekérdezés [párhuzamosan](stream-analytics-parallelization.md)van.

- Győződjön meg arról, hogy elegendő partíció van a felsőbb rétegbeli Event Hubsban, vagy IoT Hub, hogy a bemeneti átviteli sebesség méretezéséhez további átviteli egységeket (TUs) adhat hozzá. Ne feledje, hogy minden Event Hubs TU-lefoglalja 2 MB/s kimeneti sebességgel kell kinéznie.

- Győződjön meg arról, hogy elegendő erőforrás van kiépítve a kimeneti tárolókban (pl. SQL Database, Cosmos DB), így nem szabályozhatja a kimenet túlterhelését, ami időnként a rendszer zárolását okozhatja.

A legfontosabb dolog a feldolgozási sebesség változásának előrejelzése, a forgatókönyvek tesztelése az éles környezetbe helyezés előtt, és készen áll a feldolgozás megfelelő méretezésére a hiba helyreállításának ideje alatt.

Abban az esetben, ha a bejövő események mindegyike késleltetve van, lehetséges, hogy az [összes késleltetett eseményt elveti a rendszer](stream-analytics-time-handling.md) , ha egy későn érkezett ablakot alkalmazott a feladatokhoz. Előfordulhat, hogy az események eldobása titokzatos viselkedést jelent az elején. mivel azonban a Stream Analytics egy valós idejű feldolgozó motor, a bejövő eseményeket arra vár, hogy a fal órája legyen lezárva. El kell dobnia azokat az eseményeket, amelyek megszegik ezeket a korlátozásokat.

### <a name="lambda-architectures-or-backfill-process"></a>Lambda architektúrák vagy backfill folyamat

Szerencsére az előző adatarchiválási minta felhasználható az ilyen késői események kecses feldolgozására. Ennek az az elképzelése, hogy az archiválási feladatokba beérkező események bekerülnek az érkezési időben, és az események az Azure-Blobba kerülnek be a megfelelő időben, vagy Azure Data Lake Store az esemény idejével. Nem számít, hogy milyen későn érkezik egy esemény, soha nem kerül eldobásra. A program mindig a megfelelő időben landol. A helyreállítás során újra feldolgozhatja az archivált eseményeket, és backfill az eredményeket a választott tárolóba. Ez hasonló a lambda-mintázatok megvalósításához.

![ASA-backfill](media/stream-analytics-solution-patterns/back-fill.png)

A backfill folyamatot egy offline batch-feldolgozó rendszerrel kell elvégezni, amely valószínűleg a Azure Stream Analytics eltérő programozási modellel rendelkezik. Ez azt jelenti, hogy újra végre kell hajtania a teljes feldolgozási logikát.

A visszatöltésére esetében továbbra is fontos, hogy legalább átmenetileg kiépítsen több erőforrást a kimeneti mosogatók számára, hogy a nagyobb átviteli sebességet kezeljék, mint a stabil állapot-feldolgozási igények.

|Forgatókönyvek  |Újraindítás most már csak  |Újraindítás az utolsó leállítási időpontból |Újraindítás most + backfill archivált eseményekkel|
|---------|---------|---------|---------|
|**Irányítópult**   |Hézagot hoz létre    |Rövid leállás esetén OK    |Hosszú leállás esetén használható |
|**Riasztások kezelése**   |Elfogadható |Rövid leállás esetén OK    |Nem szükséges |
|**Event beszerzési alkalmazás** |Elfogadható |Rövid leállás esetén OK    |Hosszú leállás esetén használható |
|**Adattárház**   |Adatvesztés  |Elfogadható |Nem szükséges |
|**Offline Analitika**  |Adatvesztés  |Elfogadható |Nem szükséges|

## <a name="putting-it-all-together"></a>Végső összeállítás

Nem nehéz elképzelni, hogy az összes fent említett megoldási minta egyesíthető egy összetett végpontok közötti rendszeren. Az összetett rendszer tartalmazhatja az irányítópultokat, a riasztásokat, az események beszerzését, az adattárolást és az offline elemzési funkciókat.

A legfontosabb, hogy megtervezze a rendszert a kiépíthető mintákban, így az egyes alrendszerek egymástól függetlenül létrehozhatók, vizsgálhatók, frissíthetők és helyreállíthatók.

## <a name="next-steps"></a>Következő lépések

Mostantól számos megoldási mintát látott Azure Stream Analytics használatával. Ezután megismerheti a részleteket, és létrehozhatja első Stream Analytics-feladatát:

* [Stream Analytics-feladat létrehozása az Azure Portalon](stream-analytics-quick-create-portal.md).
* [Hozzon létre egy stream Analytics feladatot Azure PowerShell használatával](stream-analytics-quick-create-powershell.md).
* [Hozzon létre egy stream Analytics feladatot a Visual Studio használatával](stream-analytics-quick-create-vs.md).
