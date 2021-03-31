---
title: Adatintegráció a Azure Data Factory és az Azure-adatmegosztás használatával
description: Adatmásolás, átalakítás és megosztás az Azure Data Factory és az Azure-beli adatmegosztás használatával
author: dcstwh
ms.author: weetok
ms.service: data-factory
ms.topic: tutorial
ms.custom: seo-lt-2019
ms.date: 12/09/2020
ms.openlocfilehash: fa424f7e1f5e1f885dd433b8abc8aae1dc1bc206
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "97006157"
---
# <a name="data-integration-using-azure-data-factory-and-azure-data-share"></a>Adatintegráció a Azure Data Factory és az Azure-adatmegosztás használatával

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Mivel az ügyfelek a modern adattárház-és elemzési projektekhez csatlakoznak, nem csupán több adatmennyiségre van szükségük, hanem a saját adatbirtokában is jobban megtekinthetik az adatmennyiséget. Ez a workshop azt mutatja be, hogy a Azure Data Factory és az Azure-beli adatmegosztás fejlesztése hogyan egyszerűsíthető az Azure-beli Adatintegráció és-kezelés 

Ha a Code-Free ETL/ELT lehetővé teszi, hogy átfogó képet hozzon létre az adatokon, a Azure Data Factory fejlesztése lehetővé teszi, hogy az adatmérnökök magabiztosan és így nagyobb értéket kapjanak a vállalat számára. Az Azure-beli adatmegosztás lehetővé teszi, hogy az üzleti vállalkozások megosztójának irányítását irányított módon végezze el.

Ebben a workshopban Azure Data Factory (ADF) használatával végezheti el az adatok Azure SQL Databaseba való betöltését Azure Data Lake Storage Gen2 (ADLS Gen2). Miután megtörtént az adatgyűjtés a tóban, át kell alakítania a leképezési adatfolyamatok, a gyári natív transzformációs szolgáltatás és az Azure szinapszis Analytics között. Ezt követően az Azure-adatmegosztás használatával megoszthatja a táblázatot az átalakított adataival és néhány további adattal. 

A laborban használt adatkészletek New York-i taxik. A SQL Database-adatbázisba való importáláshoz töltse le a [taxi-bacpac fájlt](https://github.com/djpmsft/ADF_Labs/blob/master/sample-data/taxi-data.bacpac).

## <a name="prerequisites"></a>Előfeltételek

* **Azure-előfizetés**: Ha nem rendelkezik Azure-előfizetéssel, első lépésként mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/).

* **Azure SQL Database**: Ha nem rendelkezik SQL-adatbázissal, Ismerje meg, hogyan [hozhat létre SQL db-fiókot](../azure-sql/database/single-database-create-quickstart.md?tabs=azure-portal)

* **Azure Data Lake Storage Gen2 Storage-fiók**: Ha nem rendelkezik ADLS Gen2 Storage-fiókkal, megtudhatja, hogyan [hozhat létre ADLS Gen2 Storage](../storage/common/storage-account-create.md)-fiókot.

* **Azure szinapszis Analytics**: Ha nem rendelkezik Azure szinapszis Analytics-elemzéssel, Ismerje meg, hogyan [hozhat létre Azure-beli szinapszis Analytics-példányt](../synapse-analytics/sql-data-warehouse/create-data-warehouse-portal.md).

* **Azure Data Factory**: Ha még nem hozott létre egy adatelőállítót, olvassa el [a hogyan hozhat létre egy adatelőállítót](./quickstart-create-data-factory-portal.md).

* **Azure-adatmegosztás**: Ha még nem hozott létre adatmegosztást, olvassa el az [adatmegosztás létrehozása](../data-share/share-your-data.md#create-a-data-share-account)című témakört.

## <a name="set-up-your-azure-data-factory-environment"></a>A Azure Data Factory környezet beállítása

Ebből a szakaszból megtudhatja, hogyan érheti el a Azure Data Factory felhasználói felületét (ADF UX) a Azure Portal. Egyszer az ADF UX-ben három társított szolgáltatást fog konfigurálni az általunk használt adattárakhoz: Azure SQL DB, ADLS Gen2 és Azure szinapszis Analytics.

Azure Data Factory társított szolgáltatásokban adja meg a kapcsolati adatokat a külső erőforrásokhoz. Azure Data Factory jelenleg több mint 85 összekötőt támogat.

### <a name="open-the-azure-data-factory-ux"></a>Az Azure Data Factory UX megnyitása

1. Nyissa meg a [Azure Portalt](https://portal.azure.com) a Microsoft Edge vagy a Google Chrome böngészőben.
1. A lap tetején található keresősáv használatával keressen rá az "adatgyárak" kifejezésre.

    ![1. portál](media/lab-data-flow-data-share/portal1.png)
1. Kattintson a saját adatgyár-erőforrására az erőforrás panel megnyitásához.

    ![2. portál](media/lab-data-flow-data-share/portal2.png)
1. Az ADF UX megnyitásához kattintson a **Szerző és a figyelő** elemre. Az ADF UX a adf.azure.com címen is elérhető.

    ![3. portál](media/lab-data-flow-data-share/portal3.png)
1. A rendszer átirányítja az ADF UX weboldalára. Ez az oldal a gyors indulású, az oktatói videókat és az oktatóanyagokra mutató hivatkozásokat tartalmaz a adat-előállítói fogalmak megismeréséhez. A szerzői műveletek elindításához kattintson a bal oldali sáv ceruza ikonjára.

    ![Portál konfigurálása](media/lab-data-flow-data-share/configure1.png)

### <a name="create-an-azure-sql-database-linked-service"></a>Azure SQL Database-beli társított szolgáltatás létrehozása

1. A szerzői műveletek lapon olyan adat-előállítói erőforrásokat hozhat létre, mint a folyamatok, adatkészletek, adatfolyamatok, eseményindítók és társított szolgáltatások. Társított szolgáltatás létrehozásához kattintson a jobb alsó sarokban található **kapcsolatok** gombra.

    ![Portál 2. konfigurálása](media/lab-data-flow-data-share/configure2.png)
1. Új társított szolgáltatás hozzáadásához a kapcsolatok lapon kattintson az **új** elemre.

    ![Portál konfigurálása 3](media/lab-data-flow-data-share/configure3.png)
1. Az elsőként konfigurált társított szolgáltatás egy Azure SQL-adatbázis. A keresősáv használatával szűrheti az adattár-listát. Kattintson a **Azure SQL Database** csempére, és kattintson a Folytatás gombra.

    ![Portál konfigurálása 4](media/lab-data-flow-data-share/configure-4.png)
1. Az SQL DB konfigurációs ablaktábláján írja be a "SQLDB" nevet a társított szolgáltatás neveként. Adja meg a hitelesítő adatait, hogy a adat-előállító csatlakozhasson az adatbázishoz. SQL-hitelesítés használata esetén adja meg a kiszolgáló nevét, az adatbázist, a felhasználónevét és a jelszavát. A kapcsolatok adatainak ellenőrzéséhez kattintson a **kapcsolatok tesztelése** lehetőségre. Ha elkészült, kattintson a **Létrehozás** gombra.

    ![Portál 5. konfigurálás](media/lab-data-flow-data-share/configure5.png)

### <a name="create-an-azure-synapse-analytics-linked-service"></a>Azure szinapszis Analytics társított szolgáltatás létrehozása

1. Ismételje meg ugyanezt a folyamatot egy Azure szinapszis Analytics társított szolgáltatás hozzáadásához. A kapcsolatok lapon kattintson az **új** elemre. Válassza ki az **Azure szinapszis Analytics** csempét, és kattintson a Continue (folytatás) gombra.

    ![Portál – 6. konfigurálás](media/lab-data-flow-data-share/configure-6.png)
1. A társított szolgáltatás konfigurációja ablaktáblán írja be a "SQLDW" nevet a társított szolgáltatás neveként. Adja meg a hitelesítő adatait, hogy a adat-előállító csatlakozhasson az adatbázishoz. SQL-hitelesítés használata esetén adja meg a kiszolgáló nevét, az adatbázist, a felhasználónevét és a jelszavát. A kapcsolatok adatainak ellenőrzéséhez kattintson a **kapcsolatok tesztelése** lehetőségre. Ha elkészült, kattintson a **Létrehozás** gombra.

    ![Portál konfigurálása 7](media/lab-data-flow-data-share/configure-7.png)

### <a name="create-an-azure-data-lake-storage-gen2-linked-service"></a>Azure Data Lake Storage Gen2 társított szolgáltatás létrehozása

1. Az ehhez a laborhoz szükséges utolsó társított szolgáltatás egy Azure Data Lake Storage Gen2.  A kapcsolatok lapon kattintson az **új** elemre. Válassza ki a **Azure Data Lake Storage Gen2** csempét, és kattintson a Folytatás gombra.

    ![Portál konfigurálása 8](media/lab-data-flow-data-share/configure8.png)
1. A társított szolgáltatás konfigurációja ablaktáblán írja be a "ADLSGen2" nevet a társított szolgáltatás neveként. Ha a fiók kulcsos hitelesítését használja, válassza ki ADLS Gen2 Storage-fiókját a **Storage-fiók neve** legördülő listából. A kapcsolatok adatainak ellenőrzéséhez kattintson a **kapcsolatok tesztelése** lehetőségre. Ha elkészült, kattintson a **Létrehozás** gombra.

    ![Portál konfigurálása 9](media/lab-data-flow-data-share/configure9.png)

### <a name="turn-on-data-flow-debug-mode"></a>Adatfolyam-hibakeresési mód bekapcsolása

Az *adatátalakítás a leképezési adatfolyam használatával* szakaszban a leképezési adatfolyamatok kiépítése. A leképezési adatforgalom kiépítése előtt ajánlott eljárás a hibakeresési mód bekapcsolása, amely lehetővé teszi, hogy másodpercek alatt tesztelje az átalakítási logikát egy aktív Spark-fürtön.

A hibakeresés bekapcsolásához kattintson a Factory felső sávján található **adatfolyam-hibakeresés** csúszkára. Kattintson az OK gombra a megerősítő párbeszédpanel előugró ablakában. A fürt körülbelül 5-7 percet vesz igénybe. Folytassa az *Azure SQL db-ből származó adatok betöltését ADLS Gen2 a másolási tevékenységgel az* inicializáláskor.

![A portál 10. beállítása](media/lab-data-flow-data-share/configure10.png)

## <a name="ingest-data-using-the-copy-activity"></a>Adatbevitel a másolási tevékenység használatával

Ebben a szakaszban egy másolási tevékenységgel rendelkező folyamatot fog létrehozni, amely egy Azure SQL-ADATBÁZISból egy táblázatot tölt be egy ADLS Gen2 Storage-fiókba. Megtudhatja, hogyan adhat hozzá egy folyamatot, konfigurálhat egy adatkészletet, és hogyan végezhet hibakeresést az ADF UX használatával. Az ebben a szakaszban használt konfigurációs minta alkalmazható a kapcsolódó adattárból a fájl alapú adattárba történő másolásra.

Azure Data Factory egy folyamat olyan tevékenységek logikai csoportosítása, amelyek együttesen végeznek feladatokat. A tevékenységek határozzák meg az adatokon végrehajtandó műveletet. Egy adatkészlet a társított szolgáltatásban használni kívánt adatokat mutat.

### <a name="create-a-pipeline-with-a-copy-activity"></a>Másolási tevékenységgel rendelkező folyamat létrehozása

1. A gyári erőforrások ablaktáblán kattintson a plusz ikonra az új erőforrás menü megnyitásához. Válassza a **folyamat** lehetőséget.

    ![Portál 1. példánya](media/lab-data-flow-data-share/copy1.png)
1. A folyamat vászonjának **általános** lapján adjon nevet a folyamatnak, amely valamilyen leíró jellegű, például "IngestAndTransformTaxiData".

    ![Portál 2. másolata](media/lab-data-flow-data-share/copy2.png)
1. A folyamat vászonjának tevékenységek paneljén nyissa meg az **áthelyezés és átalakítás** egyezést, és húzza az **adatmásolási** tevékenység alakzatot a vászonra. Adjon egy leíró nevet a másolási tevékenységnek, például "IngestIntoADLS".

    ![Portál – 3. másolat](media/lab-data-flow-data-share/copy3.png)

### <a name="configure-azure-sql-db-source-dataset"></a>Az Azure SQL DB-forrás adatkészletének konfigurálása

1. Kattintson a másolási tevékenység **forrás** fülére. Új adatkészlet létrehozásához kattintson az **új** elemre. A forrás lesz a "dbo" tábla. A korábban konfigurált "SQLDB" társított szolgáltatásban található TripData.

    ![Portal 4. példánya](media/lab-data-flow-data-share/copy4.png)
1. Keresse meg **Azure SQL Database** , majd kattintson a Folytatás gombra.

    ![Portál 5. példánya](media/lab-data-flow-data-share/copy-5.png)
1. Hívja meg a "TripData" adatkészletet. A társított szolgáltatásként válassza a "SQLDB" lehetőséget. Válassza a táblanév dbo elemet. TripData "a tábla neve legördülő listából. Importálja a sémát a **kapcsolatok/tárolóból**. Ha elkészült, kattintson az OK gombra.

    ![Portál 6. példánya](media/lab-data-flow-data-share/copy6.png)

Sikeresen létrehozta a forrás adatkészletet. Győződjön meg arról, hogy a forrás beállításai között az alapértelmezett érték **tábla** van kiválasztva a lekérdezés használata mezőben.

### <a name="configure-adls-gen2-sink-dataset"></a>ADLS Gen2 fogadó adatkészlet konfigurálása

1. Kattintson a másolási tevékenység **mosogató** fülére. Új adatkészlet létrehozásához kattintson az **új** elemre.

    ![Portál 7. példánya](media/lab-data-flow-data-share/copy7.png)
1. Keresse meg **Azure Data Lake Storage Gen2** , majd kattintson a Folytatás gombra.

    ![Portál 8. példánya](media/lab-data-flow-data-share/copy8.png)
1. A formátum kiválasztása ablaktáblán válassza a **DelimitedText** lehetőséget a CSV-fájl írásakor. Kattintson a Folytatás gombra.

    ![A Portal 9. másolása](media/lab-data-flow-data-share/copy9.png)
1. Nevezze el a fogadó adatkészletet "TripDataCSV". A társított szolgáltatásként válassza a "ADLSGen2" lehetőséget. Adja meg, hová szeretné írni a CSV-fájlt. Például megírhatja az adatait a `trip-data.csv` tárolóba a fájlba `staging-container` . Állítsa az **első sort fejlécként** igaz értékre, ha azt szeretné, hogy a kimeneti adatokat fejlécek legyenek. Mivel még nem található fájl a célhelyen, állítsa a **séma importálása** **nincs** értékre. Ha elkészült, kattintson az OK gombra.

    ![Portál 10. példánya](media/lab-data-flow-data-share/copy10.png)

### <a name="test-the-copy-activity-with-a-pipeline-debug-run"></a>A másolási tevékenység tesztelése egy folyamat hibakeresési futtatásával

1. A másolási tevékenység helyes működésének ellenőrzéséhez kattintson a folyamat vászon tetején található **hibakeresés** gombra a hibakeresési Futtatás végrehajtásához. A hibakeresési műveletek lehetővé teszik a folyamat végpontok közötti vagy teljes körű tesztelését, mielőtt a rendszer közzéteszi azt a Refactory szolgáltatásban.

    ![Portál 11. példánya](media/lab-data-flow-data-share/copy11.png)
1. A hibakeresési Futtatás figyeléséhez nyissa meg a folyamat vászon **kimenet** lapját. A figyelés képernyő 20 másodpercenként automatikusan frissül, vagy ha manuálisan kattint a frissítés gombra. A másolási tevékenység speciális figyelési nézettel rendelkezik, amely a **műveletek** oszlopban található szem szemüveg ikonra kattintva érhető el.

    ![Portál 12. példánya](media/lab-data-flow-data-share/copy12.png)
1. A másolás figyelése nézet a tevékenység végrehajtási részleteit és a teljesítmény jellemzőit adja meg. Megtekintheti az olyan információkat, mint például az olvasott/írt, a sorok olvasása/írása, a fájlok olvasása/írása és az átviteli sebesség. Ha úgy konfigurálta a mindent, hogy az ADLS-fogadó egyetlen fájljába írja a 49 999 sort.

    ![Portál 13. példánya](media/lab-data-flow-data-share/copy13.png)
1. Mielőtt továbblép a következő szakaszra, a rendszer azt javasolja, hogy tegye közzé a változtatásokat a Refactory szolgáltatásban úgy, hogy az **összes közzététele** elemre kattint a Factory felső sávján. A laborban nem tárgyalt Azure Data Factory támogatja a teljes git-integrációt. A git-integráció lehetővé teszi a verziókövetés, az ismétlődő mentést egy adattárban, és az együttműködés egy adatgyárban. További információ: [verziókövetés a Azure Data Factoryban](./source-control.md#troubleshooting-git-integration).

    ![Portal publish 1](media/lab-data-flow-data-share/publish1.png)

## <a name="transform-data-using-mapping-data-flow"></a>Adatok átalakítása adatfolyam-leképezéssel

Most, hogy sikeresen átmásolta az adatokat a Azure Data Lake Storageba, itt az ideje, hogy csatlakozzon, és összesítse az adatokat egy adattárházba. A leképezési adatfolyamot fogjuk használni, Azure Data Factory a vizuálisan tervezett átalakítási szolgáltatást. Az adatfolyamatok leképezése lehetővé teszi a felhasználók számára az átalakítási logikai kódok kifejlesztését, és az ADF szolgáltatás által kezelt Spark-fürtökön történő végrehajtását.

Az ebben a lépésben létrehozott adatfolyam összekapcsolja az előző szakaszban létrehozott "TripDataCSV" adatkészletet egy "dbo" táblával. A "SQLDB" tárolt TripFares négy fő oszlop alapján. Ezután az oszlop alapján összesíti az adatokat `payment_type` , hogy kiszámítsa bizonyos mezők átlagát, és az Azure szinapszis Analytics-táblázatba írja őket.

### <a name="add-a-data-flow-activity-to-your-pipeline"></a>Adatfolyam-tevékenység hozzáadása a folyamathoz

1. A folyamat vászonjának tevékenységek paneljén nyissa meg az **áthelyezés és átalakítás** , majd az **adatfolyam** tevékenység a vászonra húzásával.

    ![Portál adatfolyama 1](media/lab-data-flow-data-share/dataflow1.png)
1. A megnyíló oldalsó ablaktáblán válassza az **új adatfolyam létrehozása** elemet, és válassza az **adatforgalom leképezése** lehetőséget. Kattintson az **OK** gombra.

    ![Portál adatforgalmának 2](media/lab-data-flow-data-share/dataflow2.png)
1. A rendszer átirányítja az adatáramlási vászonra, ahol felépítheti az átalakítási logikát. Az Általános lapon nevezze el a "JoinAndAggregateData" adatfolyamatot.

    ![Portál adatforgalmának 3](media/lab-data-flow-data-share/dataflow3.png)

### <a name="configure-your-trip-data-csv-source"></a>Az utazási adatai CSV-forrásának konfigurálása

1. Először a két forrás átalakítását kell beállítania. Az első forrás a "TripDataCSV" DelimitedText adatkészletre mutat. A forrás-átalakítás hozzáadásához kattintson a vászon **hozzáadása** mezőjére.

    ![Portál adatfolyama 4](media/lab-data-flow-data-share/dataflow4.png)
1. Nevezze el a forrás "TripDataCSV" nevet, és válassza ki a "TripDataCSV" adatkészletet a forrás legördülő menüből. Ha nem emlékszik, akkor a rendszer kezdetben nem importált sémát az adatkészlet létrehozásakor, mert ott nem található adat. Mivel `trip-data.csv` most már létezik, kattintson a **Szerkesztés** gombra az adatkészlet-beállítások lapon való ugráshoz.

    ![Portál adatfolyama 5](media/lab-data-flow-data-share/dataflow5.png)
1. Nyissa meg a TAB **sémát** , és kattintson a **séma importálása** elemre. Válassza ki a **Kapcsolódás/áruházból** lehetőséget közvetlenül a file Store-ból való importáláshoz. a karakterlánc típusú 14 oszlopnak szerepelnie kell.

    ![Portál-adatfolyam 6](media/lab-data-flow-data-share/dataflow6.png)
1. Lépjen vissza a "JoinAndAggregateData" adatfolyamathoz. Ha a hibakeresési fürt elindult (a hibakeresési csúszka mellett egy zöld kör jelzi), az adatok az **adatelőnézet** lapon olvashatók be. Az adatelőnézet beolvasásához kattintson a **frissítés** gombra.

    ![Portál – adatfolyam 7](media/lab-data-flow-data-share/dataflow7.png)

> [!Note]
> Az adatelőnézet nem ír.

### <a name="configure-your-trip-fares-sql-db-source"></a>Az utazás díjszabásának konfigurálása SQL DB-forráshoz

1. A második felvenni kívánt forrás az "dbo" SQL DB-táblára mutat. TripFares'. A "TripDataCSV" forrás alatt egy másik **hozzáadási forrás** mező jelenik meg. Kattintson rá egy új forrás átalakítás hozzáadásához.

    ![Portál – adatfolyam 8](media/lab-data-flow-data-share/dataflow8.png)
1. Nevezze el ezt a forrást: "TripFaresSQL". Az új SQL DB-adatkészlet létrehozásához kattintson a forrás adatkészlet mező melletti **új** elemre.

    ![Portál adatfolyama 9](media/lab-data-flow-data-share/dataflow9.png)
1. Válassza ki a **Azure SQL Database** csempét, és kattintson a Folytatás gombra. *Megjegyzés: Előfordulhat, hogy az adatelőállítóban található számos összekötő nem támogatott a leképezési adatfolyamban. Az adatoknak az egyik forrásból való átalakításához a másolási tevékenység használatával egy támogatott forrásba kell* bevenni.

    ![Portál adatfolyama 10](media/lab-data-flow-data-share/dataflow-10.png)
1. Hívja meg a "TripFares" adatkészletet. A társított szolgáltatásként válassza a "SQLDB" lehetőséget. Válassza a táblanév dbo elemet. TripFares "a tábla neve legördülő listából. Importálja a sémát a **kapcsolatok/tárolóból**. Ha elkészült, kattintson az OK gombra.

    ![Portál – adatfolyam 11](media/lab-data-flow-data-share/dataflow11.png)
1. Az adatelőnézet **lapon megtekintheti az adatait** .

    ![Portál adatfolyama 12](media/lab-data-flow-data-share/dataflow12.png)

### <a name="inner-join-tripdatacsv-and-tripfaressql"></a>Belső illesztés TripDataCSV és TripFaresSQL

1. Új átalakítás hozzáadásához kattintson a "TripDataCSV" jobb alsó sarkában található plusz ikonra. **Több bemenet/kimenet** területen válassza a **Csatlakozás** lehetőséget.

    ![1. portál csatlakoztatása](media/lab-data-flow-data-share/join1.png)
1. Nevezze el az "InnerJoinWithTripFares" illesztési átalakítót. Válassza a jobb oldali stream legördülő menüből a "TripFaresSQL" elemet. Válassza a **belső** lehetőséget az összekapcsolás típusaként. Ha többet szeretne megtudni a különböző illesztési típusokról a leképezési adatfolyamban, tekintse meg a [Csatlakozás típusai](./data-flow-join.md#join-types)című részt.

    Válassza ki, hogy mely oszlopokat kívánja egyeztetni az egyes adatfolyamok **között az illesztési feltételek** legördülő listából. További illesztési feltétel hozzáadásához kattintson a egy meglévő feltétel melletti plusz ikonra. Alapértelmezés szerint az összes csatlakoztatási feltétel össze van foglalva egy és operátorral, ami azt jelenti, hogy az összes feltételnek teljesülnie kell az egyeztetéshez. Ebben a laborban szeretnénk egyeztetni a következő oszlopokat:,, `medallion` `hack_license` `vendor_id` és `pickup_datetime`

    ![Portál csatlakoztatása 2](media/lab-data-flow-data-share/join2.png)
1. Ellenőrizze, hogy sikeresen csatlakozott-e 25 oszlophoz egy adatelőnézetsel együtt.

    ![3. portál csatlakoztatása](media/lab-data-flow-data-share/join3.png)

### <a name="aggregate-by-payment_type"></a>Összesítés payment_type szerint

1. Az illesztés átalakításának befejezése után vegyen fel egy összesített átalakítást a "InnerJoinWithTripFares" melletti plusz ikonra kattintva. Válassza az **Összesítés** lehetőséget a **séma-módosító** alatt.

    ![Portál Agg 1](media/lab-data-flow-data-share/agg1.png)
1. Adja a "AggregateByPaymentType" összesítő átalakítás nevét. Válassza `payment_type` a csoportosítás oszlop szerint lehetőséget.

    ![2. portál Agg](media/lab-data-flow-data-share/agg2.png)
1. Nyissa meg az **összesítések** lapot. Itt két összesítést kell megadnia:
    * Az átlagos viteldíj fizetési típus szerint csoportosítva
    * A teljes utazási távolság fizetési típus szerint csoportosítva

    Először létre kell hoznia az átlagos viteldíj kifejezést. Az **oszlop hozzáadása vagy kiválasztása** feliratú szövegmezőbe írja be a következőt: "average_fare".

    ![3. portál Agg](media/lab-data-flow-data-share/agg3.png)
1. Az összesítési kifejezés megadásához kattintson a **kifejezés megadása** feliratú kék mezőre. Ekkor megnyílik az adatáramlási kifejezés-szerkesztő, amely a bemeneti séma, a beépített függvények és műveletek, valamint a felhasználó által definiált paraméterek használatával vizuálisan hozza létre az adatfolyam-kifejezéseket. A Kifejezésszerkesztő funkcióival kapcsolatos további információkért tekintse meg a Kifejezésszerkesztő [dokumentációját](./concepts-data-flow-expression-builder.md).

    Az átlagos viteldíj beszerzéséhez használja az `avg()` összesítési függvényt, hogy összesítse az `total_amount` oszlopot egy egész számra `toInteger()` . Az adatáramlás kifejezésének nyelvében ez a következőként van definiálva: `avg(toInteger(total_amount))` . Ha elkészült, kattintson **a Mentés és Befejezés** gombra.

    ![Portál Agg 4](media/lab-data-flow-data-share/agg4.png)
1. További aggregációs kifejezés hozzáadásához kattintson a mellette található plusz ikonra `average_fare` . Válassza az **oszlop hozzáadása** lehetőséget.

    ![Portál Agg 5](media/lab-data-flow-data-share/agg5.png)
1. Az **oszlop hozzáadása vagy kiválasztása** feliratú szövegmezőbe írja be a következőt: "total_trip_distance". Ahogy az utolsó lépésben, nyissa meg a kifejezés-szerkesztőt a kifejezésbe való belépéshez.

    A teljes utazási távolság beszerzéséhez használja az `sum()` összesítési függvényt, hogy összesítse az `trip_distance` oszlopot egy egész számmal `toInteger()` . Az adatáramlás kifejezésének nyelvében ez a következőként van definiálva: `sum(toInteger(trip_distance))` . Ha elkészült, kattintson **a Mentés és Befejezés** gombra.

    ![Portál Agg 6](media/lab-data-flow-data-share/agg6.png)
1. Tesztelje az átalakítási logikát az **adatelőnézet** lapon. Amint láthatja, a korábbinál jóval kevesebb sor és oszlop szerepel. Csak az ebben az átalakításban definiált három csoportosítási és összesítési oszlop folytatja a műveletet. Mivel a mintában csak öt fizetéstípus-csoport van, csak öt sor van leképezve.

    ![Portál Agg 7](media/lab-data-flow-data-share/agg7.png)

### <a name="configure-you-azure-synapse-analytics-sink"></a>Az Azure szinapszis Analytics-fogadó konfigurálása

1. Most, hogy elvégezte az átalakítási logikát, készen állunk arra, hogy az Azure szinapszis Analytics-táblázatban lévő adatvesztést. Vegyen fel egy fogadó transzformációt a **cél** szakaszban.

    ![Portál fogadója 1](media/lab-data-flow-data-share/sink1.png)
1. Nevezze el a fogadó "SQLDWSink" nevet. Az új Azure szinapszis Analytics-adatkészlet létrehozásához kattintson a fogadó adatkészlet mező melletti **új** elemre.

    ![2. portál](media/lab-data-flow-data-share/sink2.png)

1. Válassza ki az **Azure szinapszis Analytics** csempét, és kattintson a Continue (folytatás) gombra.

    ![3. portál](media/lab-data-flow-data-share/sink-3.png)
1. Hívja meg a "AggregatedTaxiData" adatkészletet. A társított szolgáltatásként válassza a "SQLDW" lehetőséget. Válassza az **új tábla létrehozása** lehetőséget, és nevezze el az új tábla dbo. AggregateTaxiData. Ha elkészült, kattintson az OK gombra.

    ![Portál fogadója 4](media/lab-data-flow-data-share/sink4.png)
1. Nyissa meg a fogadó **Beállítások** lapját. Mivel új táblát hozunk létre, ki kell választania a tábla létrehozása lehetőséggel az **újbóli létrehozás táblát** . Törölje az **előkészítés engedélyezése** beállítást, amely azt jelzi, hogy sor-vagy batch-beszúrást végez-e.

    ![Portál fogadója 5](media/lab-data-flow-data-share/sink5.png)

Sikeresen létrehozta az adatfolyamatot. Most itt az ideje, hogy egy folyamat tevékenységében fusson.

### <a name="debug-your-pipeline-end-to-end"></a>A folyamat végpontok közötti hibakeresése

1. Lépjen vissza a **IngestAndTransformData** folyamat lapjára. Figyelje meg a "IngestIntoADLS" másolási tevékenység zöld mezőjét. Húzza át a "JoinAndAggregateData" adatfolyam-tevékenységre. Ez létrehoz egy "sikeres" műveletet, ami azt eredményezi, hogy az adatfolyam tevékenysége csak akkor fut le, ha a másolás sikeres.

    ![1. portál-folyamat](media/lab-data-flow-data-share/pipeline1.png)
1. Ahogy a másolási tevékenység esetében, kattintson a **hibakeresés** gombra a hibakeresési Futtatás végrehajtásához. Hibakeresési műveletek esetén az adatfolyam-tevékenység az aktív hibakeresési fürtöt fogja használni az új fürt felpörgetése helyett. Ez a folyamat eltarthat egy kicsit több, mint egy percet.

    ![2. portál-folyamat](media/lab-data-flow-data-share/pipeline2.png)
1. A másolási tevékenységhez hasonlóan az adatfolyam speciális figyelési nézettel is rendelkezik, melyet a tevékenység befejezésekor a szemüveg ikon is elérhet.

    ![3. portál-folyamat](media/lab-data-flow-data-share/pipeline3.png)
1. A figyelés nézetben megtekintheti az egyszerűsített adatfolyam-diagramot, valamint a végrehajtás időpontját és sorait az egyes végrehajtási fázisokban. Ha helyesen tette meg, akkor a tevékenységnek öt sorba kell tartoznia az összesített 49 999-sorokban.

    ![4. portál-folyamat](media/lab-data-flow-data-share/pipeline4.png)
1. Az átalakításra kattintva további részleteket tudhat meg a végrehajtásáról, például particionálja az adatokat, illetve az új/frissített/eldobott oszlopokat.

    ![Portál adatcsatorna 5](media/lab-data-flow-data-share/pipeline5.png)

Ezzel befejezte a tesztkörnyezet adatelőállító részét. Tegye közzé erőforrásait, ha az eseményindítókkal szeretné működővé tenni őket. Sikeresen futtatott egy folyamatot, amely Azure SQL Database adatokat töltött le Azure Data Lake Storage a másolási tevékenység használatával, majd összesíti az adatokat egy Azure szinapszis Analyticsbe. Az adatgyűjtés sikeres ellenőrzéséhez tekintse meg a SQL Server magát.

## <a name="share-data-using-azure-data-share"></a>Adatmegosztás az Azure Data Share szolgáltatás használatával

Ebből a szakaszból megtudhatja, hogyan állíthat be új adatmegosztást a Azure Portal használatával. Ez magában foglalja egy új adatmegosztás létrehozását, amely adatkészleteket fog tartalmazni Azure Data Lake Store Gen2 és az Azure szinapszis Analyticsből. Ezután konfigurálhat egy pillanatkép-ütemtervet, amely lehetővé teszi az adatok számára, hogy automatikusan frissítse a velük megosztott adatok frissítését. Ezután meghívja a címzetteket az adatmegosztásra. 

Ha létrehozta az adatmegosztást, akkor a kalapot vált, és az *adatfogyasztó* lesz. Az adatfogyasztóként elsajátíthatja az adatmegosztási meghívások elfogadásának folyamatát, beállíthatja, hogy a rendszer hol fogadja el az adatokat, és hogyan rendelje hozzá az adatkészleteket a különböző tárolóhelyekhez. Ezután elindít egy pillanatképet, amely az Önnel megosztott adatok másolását a megadott célhelyre másolja. 

### <a name="sharing-data-data-provider-flow"></a>Adatmegosztás (adatszolgáltatói folyamat)

1. Nyissa meg a Azure Portalt a Microsoft Edge vagy a Google Chrome böngészőben.

1. A lap tetején található keresősáv használatával keressen rá az **Adatmegosztások** keresése elemre.

    ![Portál hirdetései](media/lab-data-flow-data-share/portal-ads.png)

1. Válassza ki a "provider" nevű adatmegosztási fiókot a névben. Például: **DataProvider0102**. 

1. Válassza **az adatmegosztás megkezdése** lehetőséget.

    ![Megosztás megkezdése](media/lab-data-flow-data-share/ads-start-sharing.png)

1. Válassza a **+ Létrehozás** lehetőséget az új adatmegosztás konfigurálásának megkezdéséhez. 

1. A **megosztás neve** területen adjon meg egy tetszőleges nevet. Ez az adatfogyasztó által látható megosztási név, ezért ügyeljen arra, hogy adjon meg egy leíró nevet, például TaxiData.

1. A **Leírás** alatt helyezzen el egy mondatot, amely leírja az adatmegosztás tartalmát. Az adatmegosztás az egész világra kiterjedő taxi-adatmennyiséget fogja tartalmazni, amelyet számos tároló tárol, például az Azure szinapszis Analytics és a Azure Data Lake Store. 

1. A **használati feltételek** alatt adja meg azokat a feltételeket, amelyeket az adatfogyasztónak be kell tartania. Néhány példa: "Ne ossza meg ezeket az adatvédelmet a szervezeten kívül" vagy "a jogi megállapodásra" hivatkozik. 

    ![Megosztás részletei](media/lab-data-flow-data-share/ads-details.png)

1. Válassza a **Folytatás** lehetőséget. 

1. Válassza az **adatkészletek hozzáadása** lehetőséget 

    ![1. adatkészlet hozzáadása](media/lab-data-flow-data-share/add-dataset.png)

1. Válassza az **Azure szinapszis Analytics** elemet, és válassza ki az Azure szinapszis Analytics azon tábláját, amelyet az ADF-transzformációk befoglaltak.

    ![Adatkészlet SQL-adatbázisának hozzáadása](media/lab-data-flow-data-share/add-dataset-sql.png)


1. A folytatáshoz meg kell adni egy parancsfájlt, amelyet futtatnia kell. A megadott parancsfájl létrehoz egy felhasználót az SQL-adatbázisban, hogy az Azure-beli adatmegosztási MSI hitelesítse magát a nevében. 

> [!IMPORTANT]
> A szkript futtatása előtt be kell állítania magát a SQL Server Active Directory-rendszergazdája számára. 

1. Nyisson meg egy új fület, és navigáljon a Azure Portal. Másolja ki a megadott parancsfájlt, és hozzon létre egy felhasználót abban az adatbázisban, amelynek az adatait meg szeretné osztani. Ehhez jelentkezzen be a EDW-adatbázisba a Query Explorer (előzetes verzió) használatával a HRE-hitelesítés használatával. 

    A parancsfájlt úgy kell módosítania, hogy a felhasználó létrehozása zárójelek között történjen. Például
    
    a [dataprovider-XXXX] felhasználó létrehozása a külső bejelentkezésből;  exec sp_addrolemember db_owner, [dataprovider-XXXX];
    
1. Váltson vissza az Azure-adatmegosztásra, ahol adatkészleteket adott hozzá az adatmegosztáshoz. 

1. Válassza a **EDW** lehetőséget, majd válassza a **AggregatedTaxiData** lehetőséget a táblához. 

1. Válassza az **adatkészlet hozzáadása** lehetőséget

    Most már van egy SQL-táblázatunk, amely az adatkészlet részét képezi. Ezután további adatkészleteket fogunk hozzáadni a Azure Data Lake Storeból. 

1. Válassza az **adatkészlet hozzáadása** lehetőséget, és válassza ki **Azure Data Lake Store Gen2**

    ![Adatkészlet ADLs hozzáadása](media/lab-data-flow-data-share/add-dataset-adls.png)

1. Kattintson a **Tovább** gombra.

1. Bontsa ki a *wwtaxidata*. A *Boston taxi-adatközpont* kibontása. Figyelje meg, hogy a fájlok szintjén is megosztható. 

1. Válassza ki a *Boston taxi* -adatmappát, és adja hozzá a teljes mappát az adatmegosztáshoz. 

1. Válassza az **adatkészletek hozzáadása** lehetőséget

1. Tekintse át a hozzáadott adatkészleteket. Az adatmegosztáshoz hozzá kell adni egy SQL-táblázatot és egy ADLS Gen2 mappát. 

1. Válassza a **Folytatás** elemet

1. Ezen a képernyőn adhat hozzá címzetteket az adatmegosztáshoz. A hozzáadott címzettek megkapják az adatmegosztásra vonatkozó meghívókat. Ennek a labornak a céljára 2 e-mail címet kell hozzáadnia:

    1. Az Azure-előfizetés e-mail-címe. 

        ![Címzettek hozzáadása](media/lab-data-flow-data-share/add-recipients.png)

    1. Adja hozzá a nevet a kitalált adatfogyasztónak *janedoe@fabrikam.com* .

1. Ezen a képernyőn beállíthat egy pillanatkép-beállítást az adatfogyasztó számára. Ez lehetővé teszi, hogy az Ön által meghatározott időközönként rendszeres frissítéseket kapjon az adatairól. 

1. Vizsgálja meg a **Pillanatkép-ütemezéseket** , és konfigurálja az adatok óránkénti frissítését az *Ismétlődés* legördülő lista használatával.  

1. Válassza a **Létrehozás** lehetőséget.

    Most már rendelkezik aktív adatmegosztással. Lehetővé teszi, hogy az adatmegosztás létrehozásakor megtekintse az adatszolgáltatóként megjelenített tudnivalókat. 

1. Válassza ki a létrehozott adatmegosztást, a **DataProvider** címet. Az **adatmegosztásban** megjelenő **megosztások** lehetőség kiválasztásával navigáljon hozzá. 

1. Kattintson a pillanatkép-ütemterv elemre. Ha ezt választja, letilthatja a pillanatkép-ütemtervet. 

1. Ezután válassza az **adatkészletek** lapot. A létrehozást követően további adatkészleteket is hozzáadhat ehhez az adatmegosztáshoz. 

1. Válassza az **előfizetés megosztása** lapot. Még nem léteznek megosztási előfizetések, mert az adatfogyasztó még nem fogadta el a meghívót.

1. Navigáljon a **meghívások** lapra. Itt láthatja a függőben lévő meghívó (k) listáját. 

    ![Függőben lévő meghívások](media/lab-data-flow-data-share/pending-invites.png)

1. Válassza ki a meghívót *janedoe@fabrikam.com* . Válassza a Törlés elemet. Ha a címzett még nem fogadta el a meghívót, többé nem fogja tudni megtenni. 

1. Válassza az **Előzmények** fület. Semmi sem jelenik meg, mert az adatfogyasztó még nem fogadta el a meghívót, és nem váltott ki pillanatképet. 

### <a name="receiving-data-data-consumer-flow"></a>Adatfogadás (adatfeldolgozó folyamat)

Most, hogy áttekintette az adatmegosztást, készen állunk arra, hogy átkapcsoljuk a kontextust, és felhasználjuk az adatfeldolgozást. 

Most már rendelkeznie kell egy Azure-beli adatmegosztási meghívással a Beérkezett üzenetek mappájában Microsoft Azure. Indítsa el az Outlook Web Access (outlook.com) alkalmazást, és jelentkezzen be az Azure-előfizetéséhez megadott hitelesítő adatok használatával.

A kapott e-mailben kattintson a meghívás > megtekintése elemre. Ezen a ponton szimulálni kell az adatfogyasztói élményt, amikor elfogad egy adatszolgáltatót az adatmegosztásra. 

![Meghívó e-mailben](media/lab-data-flow-data-share/email-invite.png)

A rendszer kérheti az előfizetés kiválasztását. Győződjön meg arról, hogy a tesztkörnyezetben használt előfizetést választotta. 

1. Kattintson a meghívót *DataProvider* címre. 

1. Ebben a Meghívási képernyőn a korábban adatszolgáltatóként konfigurált adatmegosztással kapcsolatos különböző adatokat láthatja. Tekintse át a részleteket, és fogadja el a használati feltételeket, ha meg van megadva.

1. Válassza ki azt az előfizetést és erőforráscsoportot, amely már létezik a laborhoz. 

1. **Adatmegosztási fiók** esetén válassza a **DataConsumer** lehetőséget. Létrehozhat egy új adatmegosztási fiókot is. 

1. A **fogadott megosztás neve** mellett láthatja, hogy az alapértelmezett megosztás neve az adatszolgáltató által megadott név. Adjon meg egy felhasználóbarát nevet, amely leírja a kapott információkat, például a **TaxiDataShare**.

    ![Meghívás elfogadása](media/lab-data-flow-data-share/consumer-accept.png)

1. Dönthet úgy, hogy **most elfogadja és konfigurálja** a **későbbiekben, vagy elfogadja és konfigurálja** azokat. Ha az elfogadás és a Konfigurálás lehetőséget választja, meg kell adnia egy olyan Storage-fiókot, amelyben az összes adatmásolt. Ha később elfogadja és konfigurálja a megosztást, a megosztásban lévő adatkészletek le lesznek képezve, és manuálisan kell leképezni őket. Ezt később is eldönthetjük. 

1. Válassza **az elfogadás és konfigurálás később** lehetőséget. 

    Ennek a beállításnak a konfigurálásakor a rendszer létrehoz egy megosztási előfizetést, de nincs hová rendelve az adatterületnek, mert nincs leképezve célhely. 

    Ezután konfigurálni fogjuk az adatmegosztáshoz tartozó adatkészlet-hozzárendeléseket. 

1. Válassza ki a kapott megosztást (az 5. lépésben megadott nevet).

    Az **trigger pillanatképe** szürkén jelenik meg, de a megosztás aktív. 

1. Válassza az **adatkészletek** lapot. Figyelje meg, hogy minden adatkészlet le van képezve, ami azt jelenti, hogy nincs célhelye az adatmásoláshoz. 

    ![nem leképezett adatkészletek](media/lab-data-flow-data-share/unmapped.png)

1. Válassza ki az Azure szinapszis Analytics-táblázatot, majd válassza a **+ leképezés a célként** lehetőséget.

1. A képernyő jobb oldalán válassza ki a **cél adattípus** legördülő listát. 

    Az SQL-adattárakat számos adattárhoz hozzárendelheti. Ebben az esetben egy Azure SQL Database lesz hozzárendelve.

    ![leképezés](media/lab-data-flow-data-share/mapping-options.png)
    
    Választható Válassza ki **Azure Data Lake Store Gen2** a célként megadott adattípusként. 
    
    Választható Válassza ki azt az előfizetést, erőforráscsoportot és Storage-fiókot, amelyet már dolgozott. 
    
    Választható Dönthet úgy is, hogy az adatait CSV-vagy parketta-formátumban fogadja az adattóba. 

1. A **célként megadott adattípus** mellett válassza a Azure SQL Database lehetőséget. 

1. Válassza ki azt az előfizetést, erőforráscsoportot és Storage-fiókot, amelyet már dolgozott. 

    ![leképezés az SQL-re](media/lab-data-flow-data-share/map-to-sqldb.png)

1. A folytatás előtt létre kell hoznia egy új felhasználót a SQL Server a megadott parancsfájl futtatásával. Először másolja a vágólapra megadott parancsfájlt. 

1. Nyisson meg egy új Azure Portal lapot. Ne zárjuk be a meglévő fület, mert egy pillanat alatt vissza kell térnie rá. 

1. Az új lapon megnyitva navigáljon az **SQL-adatbázisokhoz**.

1. Válassza ki az SQL-adatbázist (csak az előfizetésében kell lennie). Ügyeljen arra, hogy ne válassza ki az adatraktárat. 

1. A **Lekérdezés-szerkesztő kiválasztása (előzetes verzió)**

1. A HRE-hitelesítés használatával jelentkezzen be a lekérdezés-szerkesztőbe. 

1. Futtassa az adatmegosztásban megadott lekérdezést (a 14. lépésben átmásolva a vágólapra). 

    Ez a parancs lehetővé teszi, hogy az Azure-beli adatmegosztási szolgáltatás felügyelt identitásokat használjon az Azure-szolgáltatásokhoz, hogy hitelesítse a SQL Server, hogy képes legyen az adatai másolására. 

1. Lépjen vissza az eredeti lapra, és válassza a **leképezés célhelyre** lehetőséget.

1. Ezután válassza ki az adatkészlet részét képező Azure Data Lake Gen2 mappát, és rendelje hozzá egy Azure Blob Storage-fiókhoz. 

    ![storage](media/lab-data-flow-data-share/storage-map.png)

    Az összes hozzárendelt adatkészlet esetében most már készen áll az adatok fogadására az adatszolgáltatótól. 

    ![leképezve](media/lab-data-flow-data-share/all-mapped.png)
    
1. Válassza a **részletek** lehetőséget. 

    Figyelje meg, hogy az **trigger pillanatképe** már nem szürkén jelenik meg, mert az adatmegosztás most már rendelkezik célhelyekkel a másoláshoz.

1. Válassza a pillanatkép elindítása-> teljes másolás lehetőséget. 

    ![eseményindító](media/lab-data-flow-data-share/trigger-full.png)

    Ekkor megkezdődik az adatok másolása az új adatmegosztási fiókba. Valós forgatókönyv esetén ezek az adatok egy harmadik féltől származnak. 

    Az adatgyűjtés körülbelül 3-5 percet vesz igénybe. A folyamat figyeléséhez kattintson az **Előzmények** lapra. 

    Amíg megvárja, navigáljon az eredeti adatmegosztáshoz (DataProvider), és tekintse meg a **Share Subscriptions and History (megosztás előfizetések** és **Előzmények** ) lap állapotát. Figyelje meg, hogy már van aktív előfizetés, és adatszolgáltatóként is megfigyelheti, hogy az adatfogyasztó megkezdte-e a velük megosztott adatforgalom fogadását. 

1. Váltson vissza az adatfogyasztó adatmegosztására. Az trigger állapotának sikeres megkeresése után navigáljon a cél SQL-adatbázishoz és a-tóhoz, és tekintse meg, hogy az adatai a megfelelő tárolókban vannak-e. 

Gratulálunk, befejezte a labort!