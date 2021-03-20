---
title: Útmutató a repülési és a csoportos adatelemzési folyamat prediktív karbantartásához
description: Technikai útmutató a megoldási sablonhoz a repülési, segédprogramok és szállítás prediktív karbantartásához.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: previous-author=fboylu, previous-ms.author=fboylu
ms.openlocfilehash: 3edeee8f41c806c90f32208c0c4f174c76ba38d0
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "93321983"
---
# <a name="technical-guide-to-the-solution-template-for-predictive-maintenance-in-aerospace"></a>Technikai útmutató a repülőgépipari prediktív karbantartás megoldási sablonja számára

> [!Important]
> Ez a cikk elavult. A repülőgépiparban a prediktív karbantartásról szóló vita továbbra is releváns, de a jelenlegi információk esetében az [üzleti közönség megoldásának áttekintése](https://github.com/Azure/cortana-intelligence-predictive-maintenance-aerospace)című témakörben talál további információt.


A megoldási sablonok célja, hogy felgyorsítsa a E2E-bemutató létrehozásának folyamatát. Az üzembe helyezett sablon az előfizetését a szükséges összetevőkkel hozza létre, majd létrehozza a közöttük lévő kapcsolatokat. Az adatfolyamatot az adatfeldolgozó alkalmazásból származó mintaadatok alapján is magokba helyezi, amelyet a helyi gépen a megoldás sablonjának telepítése után tölthet le és telepíthet. A generátortól származó adatok hidratálják az adatfolyamatot, és elkezdik létrehozni a gépi tanulási előrejelzéseket, amelyek ezután megjeleníthetők a Power BI irányítópulton.

A telepítési folyamat végigvezeti a megoldás hitelesítő adatainak beállításához szükséges számos lépésen. Ügyeljen arra, hogy rögzítse az üzembe helyezés során megadott hitelesítő adatokat, például a megoldás nevét, a felhasználónevet és a jelszót. 


A cikk célja:
- Az előfizetésben kiépített hivatkozási architektúra és összetevők leírása.
- Bemutatjuk, hogyan cserélje le a mintaadatok a saját adataira. 
- Megmutatjuk, hogyan módosíthatja a megoldás sablonját.  

## <a name="overview"></a>Áttekintés
![Prediktív karbantartási architektúra](./media/predictive-maintenance-technical-guide/predictive-maintenance-architecture.png)

A megoldás telepítésekor aktiválja az Azure-szolgáltatásokat, például az Event hub, a Stream Analytics, a HDInsight, a Data Factory és a Machine Learning. Az architektúra diagram bemutatja, hogyan épül fel a repülési megoldás sablonjának prediktív karbantartása. Ezeket a szolgáltatásokat megvizsgálhatja a Azure Portal a megoldás telepítésével létrehozott megoldási sablon diagramban (kivéve a HDInsight, amelyet igény szerint kell kiépíteni, amikor a kapcsolódó folyamat tevékenységeit futtatni kell, és később törlik).
Töltse le [a diagram teljes méretű verzióját](https://download.microsoft.com/download/1/9/B/19B815F0-D1B0-4F67-AED3-A40544225FD1/ca-topologies-maintenance-prediction.png).

A következő szakaszok a megoldási részeket ismertetik.

## <a name="data-source-and-ingestion"></a>Adatforrás és betöltés
### <a name="synthetic-data-source"></a>Szintetikus adatforrás
Ehhez a sablonhoz a rendszer a használt adatforrást egy letöltött asztali alkalmazásból hozza létre, amelyet a sikeres telepítés után helyileg futtat.

Az alkalmazás letöltéséhez és telepítéséhez szükséges utasítások megkereséséhez válassza ki az első csomópontot, a prediktív karbantartási adat-előállítót a megoldás sablonjának diagramján. Az utasítások a tulajdonságok sávban találhatók. Ez az alkalmazás az [Azure Event hub](#azure-event-hub) szolgáltatást a megoldás többi folyamatában használt adatpontokkal vagy eseményekkel együtt táplálja. Ez az adatforrás a [NASA-adattárházból](https://c3.nasa.gov/dashlink/resources/139/) származó nyilvánosan elérhető adatokból származik, a [Turbofan motor degradációs szimulációs adatkészletének](https://ti.arc.nasa.gov/tech/dash/groups/pcoe/prognostic-data-repository/#turbofan)használatával.

Az Event Generation alkalmazás csak a számítógépen való végrehajtás közben tölti fel az Azure Event hub-t.  

### <a name="azure-event-hub"></a>Azure Event Hub  
Az [Azure Event hub](https://azure.microsoft.com/services/event-hubs/) szolgáltatás a szintetikus adatforrás által megadott bemenet címzettje.

## <a name="data-preparation-and-analysis"></a>Adatfeldolgozás és-elemzés  
### <a name="azure-stream-analytics"></a>Azure Stream Analytics
A [Azure stream Analytics](https://azure.microsoft.com/services/stream-analytics/) segítségével közel valós idejű elemzéseket biztosíthat az [Azure Event hub](#azure-event-hub) szolgáltatásból származó bemeneti streamhez. Ezután közzéteheti az eredményeket egy [Power bi](https://powerbi.microsoft.com) irányítópulton, és archiválja az összes nyers bejövő eseményt az [Azure Storage](https://azure.microsoft.com/services/storage/) szolgáltatásba a [Azure Data Factory](https://azure.microsoft.com/documentation/services/data-factory/) szolgáltatás általi későbbi feldolgozás céljából.

### <a name="hdinsight-custom-aggregation"></a>Egyéni HDInsight összesítése
A HDInsight használatával futtassa a [kaptár](/archive/blogs/uk_faculty_connection/getting-started-with-microsoft-big-data-hive-hdinsight-jump-start) -parancsfájlokat (Azure Data Factory) a Azure stream Analytics erőforrás használatával archivált nyers események összesítésének biztosításához.

### <a name="azure-machine-learning"></a>Azure Machine Learning
Készítse elő az előrejelzéseket egy adott repülőgép-motor hátralévő hasznos élettartamára (RUL) a [Azure Machine learning szolgáltatással](https://azure.microsoft.com/services/machine-learning/) kapott bemeneti adatok használatával (Azure Data Factory). 

## <a name="data-publishing"></a>Adatközzététel
### <a name="azure-sql-database"></a>Azure SQL Database
Használja a [Azure SQL Database](https://azure.microsoft.com/services/sql-database/) a Azure Machine learning által fogadott előrejelzések tárolására, amelyeket a rendszer a [Power bi](https://powerbi.microsoft.com) irányítópulton használ fel.

## <a name="data-consumption"></a>Adathasználat
### <a name="power-bi"></a>Power BI
A [Power bi](https://powerbi.microsoft.com) segítségével megjelenítheti a [Azure stream Analytics](https://azure.microsoft.com/services/stream-analytics/)által biztosított összesítéseket és riasztásokat tartalmazó irányítópultot, valamint a [Azure Machine learning](https://azure.microsoft.com/services/machine-learning/)használatával előállított [Azure SQL Databaseban](https://azure.microsoft.com/services/sql-database/) tárolt RUL-előrejelzéseket.

## <a name="how-to-bring-in-your-own-data"></a>Saját adataik beszerzése
Ez a szakasz azt ismerteti, hogyan hozhatja ki saját adatait az Azure-ba, és milyen területekre van szükség az architektúrához tartozó adatváltozások megváltoztatásához.

Nem valószínű, hogy az adatkészlet megegyezik az ehhez a megoldás-sablonhoz használt [Turbofan-motor-romlási szimulációs](https://ti.arc.nasa.gov/tech/dash/groups/pcoe/prognostic-data-repository/#turbofan) adatkészlettel használt adatkészlettel. Az adatai és a követelmények megismerése rendkívül fontos, hogy a sablon hogyan módosítható a saját adataival. 

A következő részek a sablon azon részeit tárgyalják, amelyek módosítást igényelnek új adatkészlet bevezetésekor.

### <a name="azure-event-hub"></a>Azure Event Hub
Az Azure Event hub általános; az adatközpont CSV-vagy JSON-formátumban is elküldhető. Nem történik különleges feldolgozás az Azure Event hub-ban, de fontos, hogy megértse az általa táplált adatgyűjtést.

Ez a dokumentum nem ismerteti az adatai betöltését, de az Event hub API-k használatával egyszerűen küldhet eseményeket vagy az Azure Event hub-ba is.

### <a name="azure-stream-analytics"></a><a name="azure-stream-analytics-1"></a>Azure Stream Analytics
A Azure Stream Analytics erőforrás segítségével közel valós idejű elemzéseket biztosíthat az adatfolyamok beolvasásával és az adatok tetszőleges számú forrásra való kihelyezésével.

A repülőgépipari megoldás sablonjának prediktív karbantartásához a Azure Stream Analytics lekérdezés négy allekérdezésből áll, amelyek mindegyike az Azure Event hub szolgáltatásból származó eseményeket használó lekérdezéseket tartalmazza, és négy különböző helynek ad kimenetet. Ezek a kimenetek három Power BI adatkészletből és egy Azure Storage-helyből állnak.

A Azure Stream Analytics lekérdezés a következő címen érhető el:

* Kapcsolódás a Azure Portalhoz
* A Stream Analytics feladatok megkeresése ![ stream Analytics ikon ](./media/predictive-maintenance-technical-guide/icon-stream-analytics.png) , amelyet a megoldás telepítésekor generáltak (*például* **maintenancesa02asapbi** és **maintenancesa02asablob** a prediktív karbantartási megoldáshoz)
* Kiválasztásával
  
  * ***Bemenetek*** a lekérdezés bemenetének megtekintéséhez
  * ***Lekérdezés*** a lekérdezés megtekintéséhez
  * ***Kimenetek*** a különböző kimenetek megtekintéséhez

Azure Stream Analytics lekérdezési felépítéssel kapcsolatos információkért tekintse meg az MSDN [stream Analytics lekérdezési útmutatójában](/stream-analytics-query/stream-analytics-query-language-reference) .

Ebben a megoldásban a lekérdezések három adatkészletet jelenítenek meg, közel valós idejű elemzési információkkal a bejövő adatfolyamról a megoldás sablonjának részeként megadott Power BI irányítópultra. Mivel a bejövő adatformátummal kapcsolatban implicit ismeretekkel rendelkezik, ezeket a lekérdezéseket az adatformátuma alapján kell módosítani.

A második Stream Analytics **maintenancesa02asablob** található lekérdezés egyszerűen kiadja az [Event hub](https://azure.microsoft.com/services/event-hubs/) összes eseményét az [Azure Storage](https://azure.microsoft.com/services/storage/) -nak, ezért az adatformátumtól függetlenül semmilyen módosítást nem igényel, mivel a teljes eseményre vonatkozó információt a rendszer a tárterületre továbbítja.

### <a name="azure-data-factory"></a>Azure Data Factory
A [Azure Data Factory](https://azure.microsoft.com/documentation/services/data-factory/) szolgáltatás összehangolja az adatátvitelt és-feldolgozást. A repülőgépipari megoldás sablonjának prediktív karbantartása során az adatelőállító három olyan [folyamatból](../../data-factory/concepts-pipelines-activities.md) áll, amelyek különböző technológiák használatával helyezik át és dolgozzák fel az adatfeldolgozást.  Az adat-előállító eléréséhez nyissa meg a megoldás üzembe helyezésével létrehozott megoldás-sablon alján található Data Factory csomópontot. Az adatkészletek alatt előforduló hibák oka, hogy az adat-előállító üzembe helyezése az adatgenerátor elindítása előtt történik. Ezek a hibák figyelmen kívül hagyhatók, és nem akadályozzák meg, hogy az adatok gyára működőképes legyen.

![Adatkészlet-hibák Data Factory](./media/predictive-maintenance-technical-guide/data-factory-dataset-error.png)

Ez a szakasz a [Azure Data Factoryban](https://azure.microsoft.com/documentation/services/data-factory/)található szükséges [folyamatokat és tevékenységeket](../../data-factory/concepts-pipelines-activities.md) ismerteti. Itt látható a megoldás diagram nézete.

![Azure Data Factory](./media/predictive-maintenance-technical-guide/azure-data-factory.png)

A gyár két folyamata tartalmazza az adatparticionálásra és-összesítésre használt [kaptár](/archive/blogs/uk_faculty_connection/getting-started-with-microsoft-big-data-hive-hdinsight-jump-start) -parancsfájlokat. Ha ezt megjegyezte, a parancsfájlok a telepítés során létrehozott [Azure Storage](https://azure.microsoft.com/services/storage/) -fiókban találhatók. A helyük a következő: maintenancesascript \\ \\ script \\ \\ kaptár \\ \\ (vagy https://[a megoldás neve]. blob. Core. Windows. net/maintenancesascript).

A [Azure stream Analytics](#azure-stream-analytics-1) lekérdezésekhez hasonlóan a [struktúra](/archive/blogs/uk_faculty_connection/getting-started-with-microsoft-big-data-hive-hdinsight-jump-start) parancsfájljai is implicit ismerettel rendelkeznek a bejövő adatformátummal kapcsolatban, és az adatformátuma alapján kell módosítani.

#### <a name="aggregateflightinfopipeline"></a>*AggregateFlightInfoPipeline*
Ez [a folyamat](../../data-factory/concepts-pipelines-activities.md) egyetlen tevékenységet tartalmaz – egy [HDInsightHive](../../data-factory/transform-data-using-hadoop-hive.md) tevékenységet, amely egy [struktúra](/archive/blogs/uk_faculty_connection/getting-started-with-microsoft-big-data-hive-hdinsight-jump-start) parancsfájlt futtató [HDInsightLinkedService](/previous-versions/azure/dn893526(v=azure.100)) használ az [Azure Storage](https://azure.microsoft.com/services/storage/) -ban az [Azure stream Analytics](https://azure.microsoft.com/services/stream-analytics/) -feladatokban elhelyezett adatok particionálásához.

A [](/archive/blogs/uk_faculty_connection/getting-started-with-microsoft-big-data-hive-hdinsight-jump-start) particionálási feladat ***AggregateFlightInfo. HQL***

#### <a name="mlscoringpipeline"></a>*MLScoringPipeline*
Ez a [folyamat](../../data-factory/concepts-pipelines-activities.md) több olyan tevékenységet tartalmaz, amelyek végeredménye az ehhez a megoldási sablonhoz társított [Azure Machine learning](https://azure.microsoft.com/services/machine-learning/) -kísérlet pontozásos előrejelzése.

A benne foglalt tevékenységek a következők:

* [HDInsightHive](../../data-factory/transform-data-using-hadoop-hive.md) -tevékenység olyan [HDInsightLinkedService](/previous-versions/azure/dn893526(v=azure.100)) használatával, amely egy [struktúra](/archive/blogs/uk_faculty_connection/getting-started-with-microsoft-big-data-hive-hdinsight-jump-start) -parancsfájlt futtat a [Azure Machine learning](https://azure.microsoft.com/services/machine-learning/) kísérlethez szükséges összesítések és funkciók tervezéséhez.
  A [](/archive/blogs/uk_faculty_connection/getting-started-with-microsoft-big-data-hive-hdinsight-jump-start) particionálási feladat ***PrepareMLInput. HQL***.
* [Másolási](/previous-versions/azure/dn835035(v=azure.100)) tevékenység, amely áthelyezi az eredményeket a [HDInsightHive](../../data-factory/transform-data-using-hadoop-hive.md) tevékenységből egyetlen, az [AzureMLBatchScoring](/previous-versions/azure/dn894009(v=azure.100)) tevékenység által elérhető [Azure Storage](https://azure.microsoft.com/services/storage/) -blobba.
* A [AzureMLBatchScoring](/previous-versions/azure/dn894009(v=azure.100)) tevékenység meghívja a [Azure Machine learning](https://azure.microsoft.com/services/machine-learning/) kísérletet, és egyetlen [Azure Storage](https://azure.microsoft.com/services/storage/) -blobba helyezi az eredményeket.

#### <a name="copyscoredresultpipeline"></a>*CopyScoredResultPipeline*
Ez a [folyamat](../../data-factory/concepts-pipelines-activities.md) egyetlen tevékenységet tartalmaz: egy [másolási](/previous-versions/azure/dn835035(v=azure.100)) tevékenységet, amely áthelyezi a [Azure Machine learning](#azure-machine-learning) kísérlet eredményeit a ***MLScoringPipeline*** a megoldás sablonjának telepítése során kiépített [Azure SQL Databasera](https://azure.microsoft.com/services/sql-database/) .

### <a name="azure-machine-learning"></a>Azure Machine Learning
Az ehhez a megoldási sablonhoz használt [Azure Machine learning](https://azure.microsoft.com/services/machine-learning/) kísérlet a repülőgép-hajtóművek hátralévő hasznos élettartamát (RUL) biztosítja. A kísérlet a felhasznált adathalmazra vonatkozik, és szükség van a-ben bevitt adatmódosításra vagy-cserére.

## <a name="monitor-progress"></a>Figyelési folyamat
Az adatgenerátor elindítása után a folyamat megkezdi a dehidratálás megkezdését, és a megoldás különböző összetevői elkezdik a műveletet az adatelőállító által kiadott parancsok követésével. A folyamat két módon figyelhető.

* Az egyik Stream Analytics feladat a nyers bejövő adatot a blob Storage-ba írja. Ha a megoldás Blob Storage elemére kattint a képernyőn, akkor sikeresen üzembe helyezte a megoldást, majd a jobb oldali panelen kattintson a Megnyitás gombra, a rendszer elvégzi a [Azure Portal](https://portal.azure.com/). Ha van, kattintson a Blobok elemre. A következő panelen megtekintheti a tárolók listáját. Kattintson a **maintenancesadata** elemre. A következő panelen a **rawdata** mappa található. A rawdata mappában olyan mappák találhatók, mint például az Hour = 17 és az Hour = 18. Ezeknek a mappáknak a jelenléte azt jelzi, hogy a rendszer nyers adatmennyiséget generál a számítógépen, és a blob Storage-ban tárolja őket. Ezekben a mappákban a CSV-fájlok véges méretben kell megjelenniük a MEGABÁJTban.
* A folyamat utolsó lépése az adatok írása (például a gépi tanulásból származó előrejelzések) SQL Databaseba. Előfordulhat, hogy legfeljebb három órát kell várnia, hogy az adott információ megjelenjen a SQL Databaseban. A SQL Databaseban rendelkezésre álló adatmennyiség figyelésének egyik módja a [Azure Portal](https://portal.azure.com/). A bal oldali panelen keresse meg az SQL-adatbázisok csomópontot, :::image type="icon" source="./media/predictive-maintenance-technical-guide/icon-SQL-databases.png" border="false"::: és kattintson rá. Ezután keresse meg az adatbázis **pmaintenancedb** , és kattintson rá. A lenti következő lapon kattintson a kezelés elemre.
   
    ![Kezelés ikon](./media/predictive-maintenance-technical-guide/icon-manage.png)
   
    Itt az új lekérdezés és lekérdezés lehetőségre kattintva megadhatja a sorok számát (például válassza a Count (*) értéket a PMResult). Az adatbázis növekedésével a tábla sorainak száma növekedni fog.

## <a name="power-bi-dashboard"></a>Power BI-irányítópult

Állítson be egy Power BI irányítópultot, amely megjeleníti a Azure Stream Analytics adatait (a gyors elérési utat) és a kötegelt előrejelzési eredményeket az Azure Machine learningből (hideg útvonal).

### <a name="set-up-the-cold-path-dashboard"></a>A hideg útvonal irányítópultjának beállítása
A hideg útvonal adatfolyamatában a cél az, hogy az egyes repülőgép-hajtóművek prediktív RUL (hátralévő hasznos élettartama) beszerezze a repülést (ciklus). Az előrejelzési eredményt 3 óránként frissíti a rendszer, hogy előre megjósolja, hogy az elmúlt 3 órában milyen repülőgép-hajtóművek lettek befejezve.

Power BI egy Azure SQL Databasehoz csatlakozik az adatforrásként, ahol a rendszer az előrejelzési eredményeket tárolja. 

Megjegyzés: 
1.    A megoldás üzembe helyezése után az előrejelzés az adatbázisban 3 órán belül megjelenik. A generátor letöltésével kapott pbix-fájl tartalmaz néhány magot, hogy azonnal létre lehessen hozni a Power BI irányítópultot. 
2.    Ebben a lépésben az ingyenes szoftver [Power bi asztal](/power-bi/fundamentals/desktop-get-the-desktop)letöltésére és telepítésére vonatkozó előfeltétel.

A következő lépések bemutatják, hogyan csatlakoztatható a pbix-fájl a megoldás üzembe helyezésének időpontjában (például előrejelzési eredmények) tartalmazó SQL Database a vizualizációhoz.

1. Az adatbázis hitelesítő adatainak beolvasása.
   
   Mielőtt továbblép a következő lépésekre, szüksége lesz az **adatbázis-kiszolgáló nevére, az adatbázis nevére, a felhasználónévre és a jelszóra** . A következő lépésekkel megtudhatja, hogyan keresheti meg őket.
   
   * A megoldási sablon diagramjának **"Azure SQL Database"** után a zöldre vált, kattintson rá, majd kattintson a **Megnyitás** gombra.
   * Ekkor megjelenik egy új böngésző lap/ablak, amely megjeleníti a Azure Portal lapot. Kattintson a bal oldali panelen található **erőforráscsoportok** elemre.
   * Válassza ki a megoldás üzembe helyezéséhez használt előfizetést, majd válassza a **"YourSolutionName \_ ResourceGroup"** lehetőséget.
   * Az új kiugró panelen kattintson az ikonra az  :::image type="icon" source="./media/predictive-maintenance-technical-guide/icon-sql.png" border="false"::: adatbázis eléréséhez. Az adatbázis neve az ikon mellett található (például **"pmaintenancedb"**), az **adatbázis-kiszolgáló neve** pedig a kiszolgálónév tulajdonság alatt jelenik meg, és a **YourSolutionName.database.Windows.net** hasonlónak kell lennie.
   * Az adatbázis **felhasználóneve** és **jelszava** megegyezik a megoldás üzembe helyezése során korábban rögzített felhasználónévvel és jelszóval.
2. Frissítse a hideg Path jelentési fájl adatforrását Power BI Desktop.
   
   * Kattintson duplán a **PowerBI \\ PredictiveMaintenanceAerospace. pbix** fájlra abban a mappában, ahova letöltötte és kicsomagolta a létrehozó fájlt. Ha a fájl megnyitásakor figyelmeztető üzenet jelenik meg, akkor hagyja figyelmen kívül őket. A fájl tetején kattintson a **"lekérdezések szerkesztése"** elemre.
     
     ![Lekérdezések szerkesztése](./media/predictive-maintenance-technical-guide/edit-queries.png)
   * Két tábla, **RemainingUsefulLife** és **PMResult** jelenik meg. Válassza ki az első táblázatot, és kattintson a " ![ ](./media/predictive-maintenance-technical-guide/icon-query-settings.png) **forrás"** elem melletti  lekérdezési beállítások ikonra a jobb oldali **"lekérdezés beállításai"** panelen. A figyelmeztető üzeneteket hagyja figyelmen kívül.
   * A kiugró ablakban cserélje le a **"kiszolgáló"** és az **"adatbázis"** elemet a saját kiszolgáló-és adatbázis-neveire, majd kattintson **az OK** gombra. A kiszolgáló neve mezőben adja meg a 1433-as portot (**YourSolutionName.database.Windows.net, 1433**). Hagyja meg az adatbázis mezőt **pmaintenancedb**. A képernyőn megjelenő figyelmeztető üzenetek figyelmen kívül hagyása.
   * A következő kiugró ablakban két lehetőség jelenik meg a bal oldali ablaktáblán (**Windows** és **adatbázis**). Kattintson az **adatbázis** elemre, töltse ki a **"username"** és a **"password"** (a megoldás első telepítésekor megadott felhasználónevet és jelszót, és hozzon létre egy Azure SQL Database). A ***válassza ki, hogy melyik szinten szeretné alkalmazni ezeket a beállításokat***, és jelölje be az adatbázis-szint beállítást. Ezután kattintson a **"kapcsolat"** gombra.
   * Kattintson a második táblázatra, **PMResult** kattintson a "forrás" elemre a ![ ](./media/predictive-maintenance-technical-guide/icon-navigation.png) jobb  oldali **"lekérdezés beállításai"** panelen, majd frissítse a kiszolgáló és az adatbázis nevét a fenti lépésekkel  , majd kattintson az OK gombra.
   * Miután visszatért az előző oldalra, zárjuk be az ablakot. Üzenet jelenik meg – kattintson az **alkalmaz** gombra. Végül kattintson a Save ( **Mentés** ) gombra a módosítások mentéséhez. Az Power BI-fájl már létrejött a Kapcsolódás a kiszolgálóhoz. Ha a vizualizációk üresek, ügyeljen rá, hogy a jelmagyarázatok jobb felső sarkában található radír ikonra kattintva törölje a vizualizációk kiválasztott elemeit. Használja a refresh (frissítés) gombot a vizualizációk új adatainak megjelenítéséhez. Kezdetben csak a vizualizációk adatai jelennek meg, mivel a rendszer 3 óránként frissíti az adatelőállítót. 3 óra elteltével a vizualizációkban szereplő új előrejelzések jelennek meg, amikor frissíti az adatait.
3. Választható Tegye közzé a hűtőházi útvonal irányítópultját, hogy [Power bi online állapotba](https://www.powerbi.com/). Ehhez a lépéshez Power BI fiókra (vagy munkahelyi vagy iskolai fiókra) van szükség.
   
   * Kattintson a **Közzététel** gombra, és néhány másodperccel később megjelenik egy ablak, amely a "közzététel a Power bi sikerhez!" lehetőséget jeleníti meg. zöld pipa jelzi. Kattintson az alábbi hivatkozásra "a PredictiveMaintenanceAerospace. pbix megnyitása a Power BIban" elemre. Részletes utasításokért tekintse meg [a Power bi Desktop közzétételét](https://support.powerbi.com/knowledgebase/articles/461278-publish-from-power-bi-desktop)ismertető témakört.
   * Új irányítópult létrehozásához: kattintson a **+** bal oldali ablaktábla **irányítópultok** területén található jelre. Adja meg az új irányítópult "prediktív karbantartási bemutató" nevét.
   * Miután megnyitotta a jelentést, kattintson a ![ rögzítés ikonra az ](./media/predictive-maintenance-technical-guide/icon-pin.png) összes vizualizáció rögzítéséhez az irányítópulton. Részletes utasításokért lásd: [csempe rögzítése Power bi irányítópulton egy jelentésből](https://support.powerbi.com/knowledgebase/articles/430323-pin-a-tile-to-a-power-bi-dashboard-from-a-report).
     Lépjen az irányítópult lapra, és állítsa be a vizualizációk méretét és helyét, és szerkessze a címét. A csempék szerkesztésével kapcsolatos részletes utasításokért lásd: [csempe szerkesztése – átméretezés, áthelyezés, átnevezés, rögzítés, törlés, hivatkozás hozzáadása](https://powerbi.microsoft.com/documentation/powerbi-service-edit-a-tile-in-a-dashboard/#rename). Az alábbi példa egy olyan irányítópultot mutat be, amely a rögzített elérésű elérésiút-vizualizációkat jeleníti meg.  Attól függően, hogy mennyi ideig futtatja az adatgenerátort, a vizualizációk számai eltérőek lehetnek.
     <br/>
     ![Végső nézet](./media/predictive-maintenance-technical-guide/final-view.png)
     <br/>
   * Az adat frissítésének megadásához vigye az egérmutatót a **PredictiveMaintenanceAerospace** adatkészletre, kattintson a ![ három pontot ábrázoló ikonra, ](./media/predictive-maintenance-technical-guide/icon-elipsis.png) majd válassza az **ütemezett frissítés** lehetőséget.
     <br/>
     > [!NOTE]
     > Ha figyelmeztető üzenet jelenik meg, kattintson a **hitelesítő adatok szerkesztése** lehetőségre, és győződjön meg arról, hogy az adatbázis hitelesítő adatai ugyanazok, mint az 1. lépésben leírt módon.
     <br/>
     ![Frissítés ütemezése](./media/predictive-maintenance-technical-guide/schedule-refresh.png)
     <br/>
   * Bontsa ki az **ütemterv frissítése** szakaszt. Kapcsolja be az adatok naprakészen tartását.
     <br/>
   * Az igények alapján ütemezze a frissítést. További információk: [Adatfrissítés Power BIban](https://support.powerbi.com/knowledgebase/articles/474669-data-refresh-in-power-bi).

### <a name="setup-hot-path-dashboard"></a>A gyors elérési út irányítópultjának beállítása
A következő lépések bemutatják, hogyan jelenítheti meg Stream Analytics-feladatok kimenetét a megoldás központi telepítésekor létrehozott adatokból. A következő lépések végrehajtásához egy [Power bi online](https://www.powerbi.com/) fiókra van szükség. Ha nem rendelkezik fiókkal, [létrehozhat egyet](https://powerbi.microsoft.com/pricing).

1. Azure Stream Analytics (ASA) Power BI kimenetének hozzáadása.
   
   * Követnie kell a [Azure Stream Analytics & Power bi: egy elemzési irányítópultot, amely valós idejű láthatóságot biztosít a folyamatos átviteli adatok](../../stream-analytics/stream-analytics-power-bi-dashboard.md) számára, hogy a Azure stream Analytics feladat kimenetét Power bi irányítópultként állítsa be.
   * Az ASA-lekérdezés három kimenettel rendelkezik, amelyek a következők: **aircraftmonitor**, **aircraftalert** és **flightsbyhour**. A lekérdezést a Query (lekérdezés) lapra kattintva tekintheti meg. Az egyes tábláknak megfelelően hozzá kell adnia egy kimenetet az ASA-hoz. Az első kimenet (**aircraftmonitor**) hozzáadásakor győződjön meg arról, hogy a **kimeneti alias**, az **adatkészlet neve** és a **tábla neve** azonos (**aircraftmonitor**). A **aircraftalert** és a **flightsbyhour** kimenetének hozzáadásához ismételje meg a lépéseket. Ha mind a három kimeneti táblát hozzáadta, és elindította az ASA-feladatot, egy megerősítő üzenetet kell kapnia ("a Stream Analytics-feladatok sikeres maintenancesa02asapbi").
2. Jelentkezzen be [Power bi online](https://www.powerbi.com) -ba
   
   * A saját munkaterület bal oldali panel adatkészletek szakaszában a ***DATASET** _ Names _ * aircraftmonitor * *, a **aircraftalert** és a **flightsbyhour** értéknek kell megjelennie. Az előző lépésben Azure Stream Analytics leküldött adatfolyam-adatok. Előfordulhat, hogy az adatkészlet **flightsbyhour** nem jelenik meg egyszerre a másik két adatkészletből, mert az SQL-lekérdezés jellegéből ered. Ez azonban egy óra elteltével jelenik meg.
   * Győződjön meg arról, hogy a ***vizualizációk*** ablaktábla meg van nyitva, és a képernyő jobb oldalán jelenik meg.
3. Ha a Power BIba áramló adatfolyamok vannak, elkezdheti megjeleníteni a folyamatos átviteli adatforgalmat. Az alábbi példa egy olyan irányítópultot mutat be, amelynek a gyors elérési útja rögzített. A megfelelő adatkészleteken alapuló irányítópult-csempéket is létrehozhat. Attól függően, hogy mennyi ideig futtatja az adatgenerátort, a vizualizációk számai eltérőek lehetnek.

    ![Irányítópult nézet](media/predictive-maintenance-technical-guide/dashboard-view.png)

1. Íme néhány lépés, amely a fenti csempék egyikét hozza létre: a "az érzékelő 11. küszöbértéke és az 48,26-es küszöb" csempe:
   
   * Kattintson a bal oldali panel adatkészletek szakaszának adatkészlet **aircraftmonitor** elemére.
   * Kattintson a **vonal diagram** ikonjára.
   * Kattintson a **feldolgozott** gombra a **mezők** ablaktáblán, hogy az a "tengely" alatt látható legyen a **vizualizációk** ablaktáblán.
   * Kattintson a "S11" és a "S11-riasztás" elemre, \_ hogy mindkettő megjelenjen az "értékek" alatt. Kattintson a **S11** és a **S11 \_ riasztás** melletti kis nyílra, és módosítsa az "összeg" kifejezést az "átlag" értékre.
   * Kattintson a **Save (Mentés** ) gombra a lap tetején, és nevezze el a "aircraftmonitor" jelentést. A "aircraftmonitor" nevű jelentés a bal oldali **navigátor** ablaktáblájának **jelentések** szakaszában jelenik meg.
   * Kattintson a **rögzítési vizualizáció** ikonra a diagram jobb felső sarkában. Az irányítópultok kiválasztásához a "rögzítés az irányítópulton" ablak jelenhet meg. Válassza a "prediktív karbantartási bemutató" lehetőséget, majd kattintson a "PIN-kód" elemre.
   * Vigye az egérmutatót erre a csempére az irányítópulton, kattintson a jobb felső sarokban található "szerkesztés" ikonra, hogy megváltoztassa a címét: "az érzékelő 11. küszöbértéke és a 48,26-es küszöb", az alcím pedig "a flotta átlaga az idő múlásával".

## <a name="delete-your-solution"></a>Megoldás törlése
Győződjön meg arról, hogy leállítja az adatgenerátort, ha az adatgenerátor futtatásakor nem használja aktívan a megoldást, magasabb költségekkel jár. Ha nem használja, törölje a megoldást. A megoldás törlése törli az előfizetésben kiépített összes összetevőt a megoldás telepítésekor. A megoldás törléséhez kattintson a megoldás nevére a megoldás sablonjának bal oldali paneljén, majd kattintson a **Törlés** gombra.

## <a name="cost-estimation-tools"></a>Költségbecslés eszközei
A következő két eszköz érhető el, amelyekkel jobban megismerheti az előfizetésében az alábbi, a repülőgépipari megoldás sablonjának prediktív karbantartásának futtatásával járó költségeket:

* [Microsoft Azure Cost kalkulátor eszköz (online)](https://azure.microsoft.com/pricing/calculator/)
* [Microsoft Azure Cost kalkulátor eszköz (asztali)](https://www.microsoft.com/download/details.aspx?id=43376)