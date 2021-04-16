---
title: Adatintegráció Azure Data Factory és Azure Data Share
description: Adatok másolása, átalakítása és megosztása Azure Data Factory és Azure Data Share
author: dcstwh
ms.author: weetok
ms.service: data-factory
ms.topic: tutorial
ms.custom: seo-lt-2019
ms.date: 04/14/2021
ms.openlocfilehash: d3924c38f760a9698735a2757bdad2af5beb0e24
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/15/2021
ms.locfileid: "107518824"
---
# <a name="data-integration-using-azure-data-factory-and-azure-data-share"></a>Adatintegráció Azure Data Factory és Azure Data Share

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Ahogy az ügyfelek modern adattárház- és elemzési projektekbe kezdik őket, nem csupán több adatra van szükségük, hanem az adataik jobb láthatóságára is az adattáraikban. Ez a workshop bemutatja, hogyan egyszerűsíthető Azure Data Factory és Azure Data Share azure-beli adatintegráció és -kezelés. 

A kódmentes ETL/ELT engedélyezésétől az adatok átfogó nézetének létrehozásáig az Azure Data Factory fejlesztései lehetővé teszik az adatmérnökök számára, hogy magabiztosan több adatot és ezáltal több értéket teremtsen a vállalat számára. Azure Data Share lehetővé teszi az üzleti és üzleti megosztás szabályozott módon való megosztását.

Ebben a workshopban a Azure Data Factory (ADF) fogja használni az adatok Azure SQL Database (Azure Data Lake Storage Gen2) ADLS Gen2. Miután leküldi az adatokat a lake-be, átalakíthatja őket leképezési adatfolyamok, az adat-előállító natív átalakítási szolgáltatása segítségével, és át fogja őket Azure Synapse Analytics. Ezután átalakított adatokkal, valamint további adatokkal osztja meg a táblát a Azure Data Share. 

A laborban használt adatok a New York-i taxi adatai. Az adatbázisba való importáláshoz töltse SQL Database [taxiadatok bacpac fájlját.](https://github.com/djpmsft/ADF_Labs/blob/master/sample-data/taxi-data.bacpac)

## <a name="prerequisites"></a>Előfeltételek

* **Azure-előfizetés**: Ha nem rendelkezik Azure-előfizetéssel, első lépésként mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/).

* **Azure SQL Database:** Ha nincs SQL DB-adatbázisa, ismerje meg, hogyan hozhat létre [SQL DB-fiókot](../azure-sql/database/single-database-create-quickstart.md?tabs=azure-portal)

* **Azure Data Lake Storage Gen2 tárfiók:** Ha nem ADLS Gen2 tárfiókkal, megtudhatja, hogyan hozhat létre ADLS Gen2 [tárfiókot.](../storage/common/storage-account-create.md)

* **Azure Synapse Analytics:** Ha még nem Azure Synapse Analytics, megtudhatja, hogyan hozhat létre egy [Azure Synapse Analytics-példányt.](../synapse-analytics/sql-data-warehouse/create-data-warehouse-portal.md)

* **Azure Data Factory:** Ha még nem hozott létre adat-előállítót, tekintse meg az adat-előállítók [létrehozásáról készült következőt:](./quickstart-create-data-factory-portal.md).

* **Azure Data Share:** Ha még nem hozott létre adat megosztást, tekintse meg az adat megosztások [létrehozásáról készült részt.](../data-share/share-your-data.md#create-a-data-share-account)

## <a name="set-up-your-azure-data-factory-environment"></a>A saját Azure Data Factory beállítása

Ebben a szakaszban megtudhatja, hogyan férhet hozzá az Azure Data Factory felhasználói élményhez (ADF UX) a Azure Portal. Az ADF felhasználói felületét használva minden használt adattárhoz három csatolt szolgáltatást fog konfigurálni: Azure SQL DB, ADLS Gen2 és Azure Synapse Analytics.

A Azure Data Factory szolgáltatásokban határozzák meg a külső erőforrások kapcsolati adatait. Azure Data Factory jelenleg több mint 85 összekötőt támogat.

### <a name="open-the-azure-data-factory-ux"></a>Nyissa meg a Azure Data Factory felhasználói felületét

1. Nyissa meg [Azure Portal](https://portal.azure.com) böngészőben Microsoft Edge Google Chrome böngészőben.
1. Az oldal tetején található keresősáv használatával keressen rá a "Data Factories" kifejezésre

    ![1. portál](media/lab-data-flow-data-share/portal1.png)
1. Kattintson az adat-előállító erőforrására az erőforrás panel megnyitásához.

    ![2. portál](media/lab-data-flow-data-share/portal2.png)
1. Az ADF **felhasználói felület megnyitásához** kattintson az Author and Monitor (Szerkesztés és figyelés) elemre. Az ADF felhasználói felület a következő adf.azure.com.

    ![3. portál](media/lab-data-flow-data-share/portal3.png)
1. A rendszer átirányítja az ADF felhasználói felület kezdőlapjára. Ez az oldal gyorsútfelvételeket, oktatóvideókat és az adat-előállítóval kapcsolatos fogalmakat ismertető oktatóanyagokra mutató hivatkozásokat tartalmaz. A szerzői beállítások szerkesztésének elkezdésében kattintson a ceruza ikonra a bal oldali sávon.

    ![A portál konfigurálása](media/lab-data-flow-data-share/configure1.png)

### <a name="create-an-azure-sql-database-linked-service"></a>Azure SQL Database-beli társított szolgáltatás létrehozása

1. Csatolt szolgáltatás létrehozásához a bal oldali sávon válassza  a Manage **hub** (Központ kezelése) lehetőséget, majd a Kapcsolatok panelen válassza a **Linked services** (Csatolt szolgáltatások) lehetőséget, majd az **Új** lehetőséget egy új összekapcsolt szolgáltatás hozzáadásához.

    ![2. konfigurálás a portálon](media/lab-data-flow-data-share/configure2.png)
1. Az elsőként konfigurált összekapcsolt szolgáltatás egy Azure SQL adatbázis. A keresősáv használatával szűrheti az adattárlistát. Kattintson a **Azure SQL Database** csempére, majd a Folytatás gombra.

    ![4. konfigurálás a portálon](media/lab-data-flow-data-share/configure-4.png)
1. Az SQL DB konfigurációs panelen adja meg az "SQLDB" nevet a csatolt szolgáltatás neveként. Adja meg a hitelesítő adatait, hogy az adat-előállító csatlakozni tud az adatbázishoz. HA SQL-hitelesítést használ, adja meg a kiszolgáló nevét, az adatbázist, a felhasználónevet és a jelszót. A Kapcsolat tesztelése gombra kattintva ellenőrizheti, hogy a **kapcsolati adatok helyesek-e.** Ha **elkészült, kattintson** a Létrehozás gombra.

    ![5. konfigurálás a portálon](media/lab-data-flow-data-share/configure5.png)

### <a name="create-an-azure-synapse-analytics-linked-service"></a>Összekapcsolt Azure Synapse Analytics létrehozása

1. Ismételje meg ugyanezt a folyamatot egy új Azure Synapse Analytics hozzáadásához. A kapcsolatok lapon kattintson az Új **elemre.** Válassza a **Azure Synapse Analytics** csempét, majd kattintson a Folytatás gombra.

    ![6. konfigurálás a portálon](media/lab-data-flow-data-share/configure-6.png)
1. A csatolt szolgáltatás konfigurációs panelen adja meg az "SQLDW" nevet a csatolt szolgáltatás neveként. Adja meg a hitelesítő adatait, hogy az adat-előállító csatlakozni tud az adatbázishoz. HA SQL-hitelesítést használ, adja meg a kiszolgáló nevét, az adatbázist, a felhasználónevet és a jelszót. A Kapcsolat tesztelése gombra kattintva ellenőrizheti, hogy a **kapcsolati adatok helyesek-e.** Ha **elkészült,** kattintson a Létrehozás gombra.

    ![7. konfigurálás a portálon](media/lab-data-flow-data-share/configure-7.png)

### <a name="create-an-azure-data-lake-storage-gen2-linked-service"></a>Összekapcsolt Azure Data Lake Storage Gen2 létrehozása

1. A labor utolsó szükséges csatolt szolgáltatása egy Azure Data Lake Storage gen2.  A kapcsolatok lapon kattintson az Új **elemre.** Válassza ki **a Azure Data Lake Storage Gen2** csempét, és kattintson a Folytatás gombra.

    ![8. konfigurálás a portálon](media/lab-data-flow-data-share/configure8.png)
1. A csatolt szolgáltatás konfigurációs panelén adja meg az "ADLSGen2" nevet a csatolt szolgáltatás neveként. Ha fiókkulcsos hitelesítést használ, válassza ki ADLS Gen2 tárfiókját a **Tárfiók** neve legördülő menüből. A Kapcsolat tesztelése gombra kattintva ellenőrizheti, hogy a kapcsolati adatok **helyesek-e.** Ha **elkészült,** kattintson a Létrehozás gombra.

    ![9. konfigurálás a portálon](media/lab-data-flow-data-share/configure9.png)

### <a name="turn-on-data-flow-debug-mode"></a>Adatfolyam hibakeresési módjának bekapcsolása

Az Adatok *átalakítása leképezési adatfolyam használatával szakaszban* leképezési adatfolyamokat fog feléjük építeni. A leképezési adatfolyamok létrehozása előtt ajánlott bekapcsolni a hibakeresési módot, amely lehetővé teszi az átalakítási logika másodpercek alatt való tesztelését egy aktív Spark-fürtön.

A hibakeresés bekapcsolához  kattintson az Adatfolyam hibakeresése csúszkára az adatfolyam-vászon vagy folyamatvászon felső sávjában, ha **adatfolyam-tevékenységek vannak.** Kattintson az OK gombra, amikor megjelenik a megerősítő párbeszédpanel. A fürt létrehozása körülbelül 5–7 percet is igénybe fog venni. Folytassa a *következővel: Adatok* be Azure SQL adatbázisból ADLS Gen2 másolási tevékenységgel inicializálás közben.

![10-es konfigurálás a portálon](media/lab-data-flow-data-share/configure10.png)

![11-es konfigurálás a portálon](media/lab-data-flow-data-share/configure11.png)

## <a name="ingest-data-using-the-copy-activity"></a>Adatok bemásolása a másolási tevékenységgel

Ebben a szakaszban olyan másolási tevékenységgel fog létrehozni egy folyamatot, amely egy tábla egy Azure SQL-adatbázisból egy ADLS Gen2 tárfiókba. Megtudhatja, hogyan adhat hozzá folyamatot, hogyan konfigurálhatja az adatkészletet, és hogyan lehet hibakeresést végezni egy folyamaton az ADF felhasználói felület segítségével. Az ebben a szakaszban használt konfigurációs minta alkalmazható a relációs adattárból egy fájlalapú adattárba való másolásra.

A Azure Data Factory a folyamatok olyan tevékenységek logikai csoportosítása, amelyek együtt végeznek el egy feladatot. A tevékenységek meghatározzák az adatokon végrehajtani szükséges műveletet. Az adatkészletek a csatolt szolgáltatásban használni kívánt adatokra mutatnak.

### <a name="create-a-pipeline-with-a-copy-activity"></a>Másolási tevékenységgel kapcsolatos folyamat létrehozása

1. A gyári erőforrások panelen kattintson a plusz ikonra az új erőforrásmenü megnyitásához. Válassza a **Folyamat lehetőséget.**

    ![1. másolás a portálon](media/lab-data-flow-data-share/copy1.png)
1. A **folyamatvászon** Általános lapján nevezze el a folyamatot leíró nevet, például: "IngestAndTransformTaxiData".

    ![2. másolás a portálon](media/lab-data-flow-data-share/copy2.png)
1. A folyamatvászon Tevékenységek panelen  nyissa meg az Áthelyezés  és átalakítás inklúziót, és húzza az Adatok másolása tevékenységet a vászonra. Adjon leíró nevet a másolási tevékenységnek, például: "IngestIntoADLS".

    ![3. másolás a portálon](media/lab-data-flow-data-share/copy3.png)

### <a name="configure-azure-sql-db-source-dataset"></a>Adatbázis Azure SQL adatkészlet konfigurálása

1. Kattintson a **másolási tevékenység** Forrás lapfülére. Új adatkészlet létrehozásához kattintson az Új **elemre.** A forrás a "dbo" tábla lesz. A TripData a korábban konfigurált "SQLDB" csatolt szolgáltatásban található.

    ![Portál másolása 4](media/lab-data-flow-data-share/copy4.png)
1. Keressen rá a **Azure SQL Database,** majd kattintson a Folytatás gombra.

    ![Portál másolása 5](media/lab-data-flow-data-share/copy-5.png)
1. Hívja meg az adatkészletet "TripData"-nak. Csatolt szolgáltatásként válassza az "SQLDB" lehetőséget. Válassza a "dbo" táblanevet. TripData a tábla neve legördülő menüből. Importálja a **sémát a kapcsolatból/tárolóból.** Ha elkészült, kattintson az OK gombra.

    ![Portál másolása 6](media/lab-data-flow-data-share/copy6.png)

Sikeresen létrehozta a forrásadatkészletet. Győződjön meg arról, hogy a forrásbeállításokban az alapértelmezett **Tábla** érték van kiválasztva a lekérdezés használata mezőben.

### <a name="configure-adls-gen2-sink-dataset"></a>Fogadó ADLS Gen2 konfigurálása

1. Kattintson a **másolási tevékenység Fogadó** fülére. Új adatkészlet létrehozásához kattintson az Új **elemre.**

    ![Portál másolása 7](media/lab-data-flow-data-share/copy7.png)
1. Keressen rá a **Azure Data Lake Storage Gen2,** majd kattintson a Folytatás gombra.

    ![8. másolás a portálon](media/lab-data-flow-data-share/copy8.png)
1. A formátum kiválasztása panelen válassza a **Tagolt Szöveg** lehetőséget, miközben egy CSV-fájlba ír. Kattintson a Folytatás gombra.

    ![9. másolás a portálon](media/lab-data-flow-data-share/copy9.png)
1. A fogadó adatkészletnek nevezze el a "TripDataCSV" nevet. Válassza az "ADLSGen2" lehetőséget csatolt szolgáltatásként. Adja meg, hová szeretné írni a CSV-fájlt. Az adatokat például a tárolóban található `trip-data.csv` fájlba `staging-container` írhatja. Állítsa **az Első sor fejlécét** true (igaz) értékre, mivel azt szeretné, hogy a kimeneti adatok fejlécekkel is tartalmazzanak. Mivel még nincs fájl a célhelyen, állítsa a **Séma importálása** beállítását None (Nincs) **beállításra.** Ha elkészült, kattintson az OK gombra.

    ![Portál másolása 10](media/lab-data-flow-data-share/copy10.png)

### <a name="test-the-copy-activity-with-a-pipeline-debug-run"></a>A másolási tevékenység tesztelése folyamat hibakeresési futtatásával

1. Annak ellenőrzéséhez, hogy a  másolási tevékenység megfelelően működik-e, kattintson a folyamatvászon tetején található Hibakeresés gombra a hibakeresési futtatás végrehajtásához. A hibakeresési futtatás lehetővé teszi a folyamat tesztelését akár végpontok között, akár egy töréspontig, mielőtt közzétenjük az adat-előállító szolgáltatásban.

    ![A portál másolása 11](media/lab-data-flow-data-share/copy11.png)
1. A hibakeresési futtatás monitorozásához válassza a folyamatvászon **Kimenet** lapját. A figyelési képernyő 20 másodpercenként automatikusan frissül, vagy ha manuálisan kattint a frissítés gombra. A másolási tevékenység speciális figyelési nézetet biztosít, amely a Műveletek oszlopban található szemüveg ikonra **kattintva érhető** el.

    ![Portál másolása 12](media/lab-data-flow-data-share/copy12.png)
1. A másolásfigyelési nézet a tevékenység végrehajtási adatait és teljesítményjellemzőit tartalmazza. Olyan információkat láthat, mint az adatok olvasása/írása, a sorok olvasása/írása, a fájlok olvasása/írása és az átviteli sebesség. Ha mindent megfelelően konfigurált, 49 999 sort kell látnia, amelyek egy fájlba vannak írva az ADLS-fogadóban.

    ![Portálmásolat 13](media/lab-data-flow-data-share/copy13.png)
1. Mielőtt továbblép a következő szakaszra, javasolt közzétenni a módosításokat a Data Factory szolgáltatásban a gyári felső sáv **Publish all** (Az összes közzététele) parancsára kattintva. Bár ez a labor nem terjed ki rá, a Azure Data Factory támogatja a teljes Git-integrációt. A Git-integráció lehetővé teszi a verziószám-vezérlést, az iteratív mentést az adattárban és az együttműködést az adat-előállítókban. További információkért lásd a [forrásvezérlőt a Azure Data Factory.](./source-control.md#troubleshooting-git-integration)

    ![1. közzététel a portálon](media/lab-data-flow-data-share/publish1.png)

## <a name="transform-data-using-mapping-data-flow"></a>Adatok átalakítása adatfolyam-leképezéssel

Most, hogy sikeresen átmásolta az adatokat a Azure Data Lake Storage, ideje az adatokat egy adattárházba egyesíteni és összesíteni. A leképezési adatfolyamot fogjuk használni, Azure Data Factory vizuálisan megtervezett átalakítási szolgáltatást használjuk. Az adatfolyamok leképezése lehetővé teszi a felhasználók számára, hogy átalakítási logikai kódot fejlessnek ki és hajtsanak végre az ADF szolgáltatás által felügyelt Spark-fürtökön.

Az ebben a lépésben létrehozott belső adatfolyam az előző szakaszban létrehozott "TripDataCSV" adatkészletet egy "dbo" táblával illeszti össze. A TripFares négy kulcsoszlop alapján van tárolva az SQLDB-adatbázisban. Ezután az adatok oszlop alapján összesítve kiszámítják bizonyos mezők átlagát, és egy táblázatban `payment_type` Azure Synapse Analytics meg.

### <a name="add-a-data-flow-activity-to-your-pipeline"></a>Adatfolyam-tevékenység hozzáadása a folyamathoz

1. A folyamatvásznon a Tevékenységek  panelen nyissa meg  az Áthelyezés és átalakítás ét, majd húzza az Adatfolyam tevékenységet a vászonra.

    ![1. portáladatfolyam](media/lab-data-flow-data-share/dataflow1.png)
1. A megnyíló oldalpanelen válassza az **Új adatfolyam** létrehozása, majd az **Adatfolyam leképezése lehetőséget.** Kattintson az **OK** gombra.

    ![2. portáladatfolyam](media/lab-data-flow-data-share/dataflow2.png)
1. A rendszer arra az adatfolyam-vászonra irányítja, ahol az átalakítási logikát fogja építeni. Az általános lapon adja az adatfolyamnak a "JoinAndAggregateData" nevet.

    ![3. portáladatfolyam](media/lab-data-flow-data-share/dataflow3.png)

### <a name="configure-your-trip-data-csv-source"></a>Az útadatok CSV-forrásának konfigurálása

1. Első lépésként konfigurálja a két forrásátalakítást. Az első forrás a "TripDataCSV" tagoltText adatkészletre mutat. Forrásátalakítás hozzáadásához kattintson a vásznon **a Forrás** hozzáadása mezőre.

    ![4. portáladatfolyam](media/lab-data-flow-data-share/dataflow4.png)
1. Adja a forrásnak a "TripDataCSV" nevet, és válassza ki a "TripDataCSV" adatkészletet a forrás legördülő menüből. Ne feledje, hogy az adatkészlet létrehozásakor nem importált sémát, mert ott nem voltak adatok. Mivel `trip-data.csv` már létezik, kattintson a **Szerkesztés** gombra az adatkészlet beállításai lapra való ugráshoz.

    ![5. portáladatfolyam](media/lab-data-flow-data-share/dataflow5.png)
1. A Lap sémája **lapon kattintson** a **Séma importálása elemre.** Válassza **a From connection/store (Kapcsolat/tárolóból)** lehetőséget a fájltárolóból való közvetlen importáláshoz. 14 sztring típusú oszlopnak kell megjelennie.

    ![6. portáladatfolyam](media/lab-data-flow-data-share/dataflow6.png)
1. Vissza "JoinAndAggregateData" adatfolyamhoz. Ha a hibakeresési fürt elindult (ezt egy zöld kör jelzi a hibakeresési csúszka mellett), az Adatok előnézete lapon pillanatképet készíthet az **adatokról.** Kattintson **a Frissítés** gombra az adatelőnézet lekéréséhez.

    ![7. portáladatfolyam](media/lab-data-flow-data-share/dataflow7.png)

> [!Note]
> Az adatelőnézet nem ír adatokat.

### <a name="configure-your-trip-fares-sql-db-source"></a>Az utazássalúti adatok konfigurálása – SQL DB-forrás

1. A második hozzáadott forrás a "dbo" SQL DB-táblára mutat. TripFares" (Utazásfarmok) A "TripDataCSV" forrás alatt egy újabb **Forrás hozzáadása mező található.** Kattintson rá egy új forrásátalakítás hozzáadásához.

    ![8. portáladatfolyam](media/lab-data-flow-data-share/dataflow8.png)
1. A forrásnak nevezze el a "TripFaresSQL" nevet. Új **SQL** DB-adatkészlet létrehozásához kattintson a forrásadatkészlet mező melletti Új elemre.

    ![9. portáladatfolyam](media/lab-data-flow-data-share/dataflow9.png)
1. Válassza a **Azure SQL Database** csempét, és kattintson a Folytatás gombra. Megjegyzés: Előfordulhat, hogy az adat-előállító számos összekötőt nem támogat a leképezési *adatfolyam. Az egyik ilyen forrásból* származó adatok átalakításához a másolási tevékenység használatával egy támogatott forrásba kell behozni őket.

    ![Portáladatfolyam 10](media/lab-data-flow-data-share/dataflow-10.png)
1. Hívja meg az adatkészletet "TripFares"-nak. Csatolt szolgáltatásként válassza az "SQLDB" lehetőséget. Válassza a "dbo" táblanevet. TripFares a tábla neve legördülő menüből. Importálja a **sémát a kapcsolatból/tárolóból.** Ha elkészült, kattintson az OK gombra.

    ![11. portáladatfolyam](media/lab-data-flow-data-share/dataflow11.png)
1. Az adatok ellenőrzéséhez lekér egy adatelőnézetet az **Adatelőnézet lapon.**

    ![A portál adatfolyama 12](media/lab-data-flow-data-share/dataflow12.png)

### <a name="inner-join-tripdatacsv-and-tripfaressql"></a>A TripDataCSV és a TripFaresSQL belső illesztés

1. Új átalakítás hozzáadásához kattintson a TripDataCSV jobb alsó sarkában található plusz ikonra. A **Multiple inputs/outputs (Több bemenet/kimenet) alatt válassza** a Join (Csatlakozás) **lehetőséget.**

    ![Csatlakozás a portálhoz 1](media/lab-data-flow-data-share/join1.png)
1. Az illesztés-átalakításnak nevezze el az "InnerJoinWithTripFares" nevet. Válassza a TripFaresSQL lehetőséget a jobb stream legördülő menüből. Az **illesztés típusaként** válassza a Belső lehetőséget. A leképezési adatfolyamok különböző illesztési típusaival kapcsolatos további információkért lásd: [illesztési típusok.](./data-flow-join.md#join-types)

    Válassza ki az egyes streamek oszlopait az Illesztés **feltételei** legördülő menüben. További illesztés feltétel hozzáadásához kattintson egy meglévő feltétel melletti plusz ikonra. Alapértelmezés szerint minden összekapcsolás feltételt egy AND operátorral kombinál, ami azt jelenti, hogy az egyezéshez minden feltételnek teljesülnie kell. Ebben a laborban a következő oszlopokat szeretnénk megfeleltetni: `medallion` `hack_license` , , `vendor_id` és `pickup_datetime`

    ![Portál-csatlakozás 2](media/lab-data-flow-data-share/join2.png)
1. Ellenőrizze, hogy sikeresen összekapcsolt-e 25 oszlopot egy adatelőnézettel.

    ![Portál-csatlakozás 3](media/lab-data-flow-data-share/join3.png)

### <a name="aggregate-by-payment_type"></a>Összesítés payment_type

1. Miután befejezte az illesztés átalakítását, adjon hozzá egy összesítési átalakítást az InnerJoinWithTripFares melletti plusz ikonra kattintva. A **Sémamódosító** alatt válassza az Összesítés **lehetőséget.**

    ![Portál 1. agg.](media/lab-data-flow-data-share/agg1.png)
1. Az összesített átalakításnak nevezze el az "AggregateByPaymentType" nevet. Csoportosítás `payment_type` oszlopként válassza a lehetőséget.

    ![2. portál](media/lab-data-flow-data-share/agg2.png)
1. Ugrás az **Összesítések lapra.** Itt két aggregációt fog megadni:
    * Az átlagos ár fizetési típus szerint csoportosítva
    * Az utazás teljes távolsága fizetési típus szerint csoportosítva

    Először létre kell hoznia az átlagos árkifejezést. Az Add or **select a column**(Oszlop hozzáadása vagy kiválasztása) szövegmezőbe írja be a "average_fare" szöveget.

    ![Portál 3. agg.](media/lab-data-flow-data-share/agg3.png)
1. Az aggregáció kifejezésének beíratása után kattintson az Enter expression (Kifejezés beírása) **feliratú kék dobozra.** Ez megnyitja az adatfolyam-kifejezésszerkesztőt, amely adatfolyam-kifejezések vizuális létrehozására használható bemeneti sémával, beépített függvényekkel és műveletekkel, valamint felhasználó által definiált paraméterekkel. A kifejezésszerkesztő képességeivel kapcsolatos további információkért tekintse meg a [kifejezésszerkesztő dokumentációját.](./concepts-data-flow-expression-builder.md)

    Az átlagos ár lekért értékhez használja az aggregátum függvényt az egész számra való áttört oszlop összesítésére a `avg()` `total_amount` `toInteger()` használatával. Az adatfolyam-kifejezésnyelvben ez a következőként van `avg(toInteger(total_amount))` definiálva: . Ha **végzett, kattintson** a Mentés gombra, és fejezze be a munkát.

    ![Portál 4. agg.](media/lab-data-flow-data-share/agg4.png)
1. További aggregátumkifejezés hozzáadásához kattintson a melletti plusz `average_fare` ikonra. Válassza **az Oszlop hozzáadása lehetőséget.**

    ![Portál 5. agg.](media/lab-data-flow-data-share/agg5.png)
1. Az Add or **select a column**(Oszlop hozzáadása vagy kiválasztása) szövegmezőbe írja be a "total_trip_distance" szöveget. Ahogyan az előző lépésben is, nyissa meg a kifejezésszerkesztőt, és írja be a kifejezést.

    A teljes út távolságának lekért értékhez használja az aggregátum függvényt az oszlop egész számra való összesítésére a `sum()` `trip_distance` `toInteger()` használatával. Az adatfolyam-kifejezésnyelvben ez a következőként van `sum(toInteger(trip_distance))` definiálva: . Ha **végzett, kattintson** a Mentés gombra, és fejezze be a munkát.

    ![Portál 6. agg.](media/lab-data-flow-data-share/agg6.png)
1. Tesztelje az átalakítási logikát az **Adatelőnézet lapon.** Amint látható, a korábbiaknál lényegesen kevesebb sor és oszlop van. Csak az átalakításban meghatározott három csoport és aggregációs oszlop folytatja a lefelé irányuló munkát. Mivel a mintában csak öt fizetésitípus-csoport van, a kimenet csak öt sort tartalmaz.

    ![Portál 7. agg.](media/lab-data-flow-data-share/agg7.png)

### <a name="configure-you-azure-synapse-analytics-sink"></a>A fogadó Azure Synapse Analytics konfigurálása

1. Most, hogy befejeztük az átalakítási logikát, készen állunk arra, hogy az adatokat egy új táblába Azure Synapse Analytics. Adjon hozzá egy fogadóátalakítást a **Cél szakaszban.**

    ![Portál fogadója 1](media/lab-data-flow-data-share/sink1.png)
1. A fogadónak nevezze el az "SQLDWSink" nevet. Kattintson **a** fogadó adatkészlet mező melletti Új gombra egy új Azure Synapse Analytics létrehozásához.

    ![2. portál-fogadó](media/lab-data-flow-data-share/sink2.png)

1. Válassza a **Azure Synapse Analytics** csempét, majd kattintson a Folytatás gombra.

    ![Portál fogadója 3](media/lab-data-flow-data-share/sink-3.png)
1. Hívja meg az "AggregatedTaxiData" adatkészletet. Válassza az "SQLDW" lehetőséget csatolt szolgáltatásként. Válassza **az Új tábla létrehozása lehetőséget,** és adja az új táblának a dbo nevet. AggregateTaxiData. Ha elkészült, kattintson az OK gombra

    ![Portál fogadója 4](media/lab-data-flow-data-share/sink4.png)
1. Ugrás a **fogadó Beállítások** lapra. Mivel új táblát hozunk létre, a Tábla újbóli létrehozása lehetőséget kell választanunk **a** tábla művelet alatt. Törölje a **Enable staging (Előkészítés** engedélyezése) kijelölését, ami azt határozza meg, hogy sorról sorra vagy kötegbe van-e beszúrva.

    ![Portál fogadója 5](media/lab-data-flow-data-share/sink5.png)

Sikeresen létrehozta az adatfolyamot. Itt az ideje, hogy egy folyamattevékenységben futtassa.

### <a name="debug-your-pipeline-end-to-end"></a>A folyamat hibakeresése végpontok között

1. Vissza **IngestAndTransformData** folyamat lapját. Figyelje meg a zöld mezőt az IngestIntoADLS másolási tevékenységen. Húzza át a JoinAndAggregateData adatfolyam-tevékenységre. Ez "sikeresség esetén" létrehoz egy "sikeres" tevékenységet, ami miatt az adatfolyam-tevékenység csak akkor fut, ha a másolás sikeres volt.

    ![1. portál-folyamat](media/lab-data-flow-data-share/pipeline1.png)
1. Ahogyan a másolási tevékenységhez is, a **hibakeresési futtatás** végrehajtásához kattintson a Hibakeresés elemre. Hibakeresési futtatás esetén az adatfolyam-tevékenység az aktív hibakeresési fürtöt fogja használni ahelyett, hogy új fürtöt aktiválna. A folyamat végrehajtása kissé több mint egy percet fog igénybe venni.

    ![2. portál-folyamat](media/lab-data-flow-data-share/pipeline2.png)
1. A másolási tevékenységhez hasonló az adatfolyam is rendelkezik egy speciális figyelési nézettel, amelyet a szemüveg ikon a tevékenység befejezésekor elért.

    ![3. portál-folyamat](media/lab-data-flow-data-share/pipeline3.png)
1. A figyelési nézetben egy egyszerűsített adatfolyam-diagramot láthat, valamint az egyes végrehajtási fázisok végrehajtási időit és sorait. Ha helyesen tette, 49 999 sort kellett volna öt sorra összesítenie ebben a tevékenységben.

    ![4. portál-folyamat](media/lab-data-flow-data-share/pipeline4.png)
1. Az átalakításra kattintva további részleteket kaphat a végrehajtásról, például a particionálási információkat és az új/frissített/eldobott oszlopokat.

    ![5. portál-folyamat](media/lab-data-flow-data-share/pipeline5.png)

Ezzel befejezte a labor adat-előállító részét. Ha triggerekkel szeretné aktiválni az erőforrásokat, tegye közzé őket. Sikeresen futtatott egy folyamatot, amely adatokat betöltött a Azure SQL Database-Azure Data Lake Storage másolási tevékenységgel, majd az adatokat egy Azure Synapse Analytics. Az adatok sikeres megíratásának ellenőrzéséhez a saját maga SQL Server meg.

## <a name="share-data-using-azure-data-share"></a>Adatmegosztás az Azure Data Share szolgáltatás használatával

Ebben a szakaszban megtudhatja, hogyan állíthat be új adat megosztását a Azure Portal. Ehhez létre kell majd hoznunk egy új adat megosztást, amely az Azure Data Lake Store Gen2 és a Azure Synapse Analytics. Ezután konfigurálni fog egy pillanatkép-ütemezést, amely lehetőséget ad az adat felhasználóinak a velük megosztott adatok automatikus frissítésére. Ezután meghívja a címzetteket az adat-megosztásba. 

Miután létrehozott egy adat megosztását, át kell váltania a tálat, és az lesz az *adat felhasználója.* Adatfelvevőként végig fog menni egy adatmegoszolási meghívó elfogadásán, annak konfigurálásán, hogy hol szeretné megkapni az adatokat, és hogyan lehet az adatkészleteket különböző tárolási helyekre leképezni. Ezután elindít egy pillanatképet, amely a megadott célhelyre másolja az Ön által megosztott adatokat. 

### <a name="sharing-data-data-provider-flow"></a>Adatok megosztása (adatszolgáltatói folyamat)

1. Nyissa meg Azure Portal böngészőben vagy Microsoft Edge Google Chrome böngészőben.

1. Az oldal tetején található keresősáv használatával keressen rá az **Adatmegosztások kifejezésre**

    ![Portálhirdetések](media/lab-data-flow-data-share/portal-ads.png)

1. Válassza ki azt az adat-megosztási fiókot, amely nevében a "Provider" (Szolgáltató) név van megszabadva. Például: **DataProvider0102.** 

1. Válassza **az Adatok megosztásának elkezdása lehetőséget**

    ![Megosztás kezdete](media/lab-data-flow-data-share/ads-start-sharing.png)

1. Az **új adat megosztásának** konfigurálásához válassza a +Létrehozás lehetőséget. 

1. A **Megosztás neve alatt** adjon meg egy kívánt nevet. Ez az adat fogyasztója számára látható megosztásnév, ezért mindenképpen adjon neki leíró nevet, például TaxiData.

1. A **Leírás alatt** helyezzen el egy mondatot, amely leírja az adat megosztásának tartalmát. Az adat megosztás a taxis utazások világszerte elérhető adatait fogja tartalmazni, amelyek számos üzletben lesznek tárolva, beleértve a Azure Synapse Analytics Azure Data Lake Store. 

1. A **Használati feltételek** adja meg azokat a kifejezéseket, amelyek betartását az adat fogyasztója szeretné. Ilyen például a "Ne ossza el az adatokat a szervezeten kívül" vagy a "Tekintse meg a jogi megállapodásokat". 

    ![Megosztás részletei](media/lab-data-flow-data-share/ads-details.png)

1. Válassza a **Folytatás** lehetőséget. 

1. Válassza **az Adatkészletek hozzáadása lehetőséget** 

    ![1. adatkészlet hozzáadása](media/lab-data-flow-data-share/add-dataset.png)

1. Válassza **Azure Synapse Analytics** lehetőséget, és válasszon ki egy táblát a Azure Synapse Analytics, amelybe az ADF-átalakítások beültek.

    ![Sql-adatkészlet hozzáadása](media/lab-data-flow-data-share/add-dataset-sql.png)


1. A folytatás előtt kap egy szkriptet, amit futtatnia kell. A megadott szkript létrehoz egy felhasználót az SQL-adatbázisban, hogy Azure Data Share MSI hitelesítsen a nevében. 

> [!IMPORTANT]
> A szkript futtatása előtt be kell állítania magát a Active Directory rendszergazdájaként a SQL Server. 

1. Nyisson meg egy új lapot, és lépjen a Azure Portal. Másolja ki a megadott szkriptet, hogy létrehoz egy felhasználót abban az adatbázisban, amelyből adatokat szeretne megosztani. Ehhez AAD-hitelesítéssel kell bejelentkeznie az EDW-adatbázisba a Lekérdezéskezelő (előzetes verzió) használatával. 

    Módosítania kell a szkriptet, hogy a létrehozott felhasználó szögletes zárójelek között osson. Pl:
    
    create user [dataprovider-xxxx] from external login;  exec sp_addrolemember db_owner, [dataprovider-xxxx];
    
1. Váltson vissza arra Azure Data Share, ahol adatkészleteket adott hozzá az adat megosztásához. 

1. Válassza **az EDW lehetőséget,** majd válassza **az AggregatedTaxiData** táblát. 

1. Válassza **az Adatkészlet hozzáadása lehetőséget**

    Most már van egy SQL-táblánk, amely az adatkészletünk része. A következő lépés az Azure-beli virtuális Data Lake Store. 

1. Válassza **az Adatkészlet hozzáadása, majd** az Azure Data Lake Store **Gen2 lehetőséget**

    ![Adatkészlet-adls hozzáadása](media/lab-data-flow-data-share/add-dataset-adls.png)

1. Kattintson a **Tovább** gombra.

1. *Bontsa ki a wwtaxidata adatokat.* *Bontsa ki a Boston Taxi Data (Boston taxi adatai) adatokat.* Figyelje meg, hogy a megosztást a fájl szintjére is le lehet osztani. 

1. Válassza a *Boston Taxi Data* mappát, hogy a teljes mappát hozzáadja az adat megosztásához. 

1. Válassza **az Adatkészletek hozzáadása lehetőséget**

1. Tekintse át a hozzáadott adatkészleteket. Hozzá kell adni egy SQL-táblát és egy ADLS Gen2 nevű mappát az adat-megosztáshoz. 

1. Válassza a **Folytatás** elemet

1. Ezen a képernyőn hozzáadhat címzetteket az adat-megosztáshoz. A hozzáadt címzettek meghívókat kapnak az adat megosztására. Ebben a laborban két e-mail-címet kell hozzáadnia:

    1. A megadott Azure-előfizetés e-mail-címe. 

        ![Címzettek hozzáadása](media/lab-data-flow-data-share/add-recipients.png)

    1. Adja hozzá a nevet a nevű fiktív adat *janedoe@fabrikam.com* felhasználóhoz.

1. Ezen a képernyőn konfigurálhat egy pillanatkép-beállítást az adat fogyasztója számára. Ez lehetővé teszi számukra, hogy rendszeres frissítéseket fogadjanak az adatokról az Ön által meghatározott időközönként. 

1. Jelölje **be a Pillanatkép** ütemezése jelölőnégyzetet, és konfigurálja az adatok óránkénti frissítését az *Ismétlődés legördülő* menüben.  

1. Válassza a **Létrehozás** lehetőséget.

    Most már van aktív adat-megosztása. Áttekintheti, hogy mit láthat adatszolgáltatóként adat-megosztás létrehozásakor. 

1. Válassza ki a létrehozott, **DataProvider** című adat megosztását. A megosztások között az Elküldött megosztások lehetőség **kiválasztásával** **navigálhat Data Share.** 

1. Kattintson a Pillanatkép ütemezése elemre. Ha úgy dönt, letilthatja a pillanatkép-ütemezést. 

1. Ezután válassza az **Adatkészletek** lapot. A létrehozás után további adatkészleteket is hozzáadhat ehhez az adat megosztáshoz. 

1. Válassza az **Előfizetések megosztása** lapot. Még nem léteznek megosztási előfizetések, mert az adat fogyasztója még nem fogadta el a meghívást.

1. Lépjen a **Meghívók lapra.** Itt láthatja a függőben lévő meghívó(k) listáját. 

    ![Függőben lévő meghívók](media/lab-data-flow-data-share/pending-invites.png)

1. Válassza ki a *janedoe@fabrikam.com* meghívását. Válassza a Törlés elemet. Ha a címzett még nem fogadta el a meghívót, akkor erre már nem lesz képes. 

1. Válassza az **Előzmények** lapot. Egyelőre semmi sem jelenik meg, mert az adat fogyasztója még nem fogadta el a meghívást, és aktivált egy pillanatképet. 

### <a name="receiving-data-data-consumer-flow"></a>Adatok fogadása (adat fogyasztói folyamat)

Most, hogy áttekintettünk egy adat megosztását, készen állunk a környezetváltásra és az adathasználói skentő elhasználódására. 

Most már egy meghívóval Azure Data Share a beérkezett üzenetek között a Microsoft Azure. Indítsa el az Outlook Web Accesst (outlook.com), és jelentkezzen be az Azure-előfizetéséhez megadott hitelesítő adatokkal.

A kapott e-mailben kattintson a "Meghívók megtekintése >". Ezen a ponton szimulálni fogja az adat fogyasztói élményét, amikor adatszolgáltatói meghívót fogad az adat megosztására. 

![Meghívó e-mailben](media/lab-data-flow-data-share/email-invite.png)

Előfordulhat, hogy a rendszer arra kéri, hogy válasszon ki egy előfizetést. Győződjön meg arról, hogy azt az előfizetést választja ki, ahol a laborban dolgozott. 

1. Kattintson a *DataProvider (Adatprovider) című meghívásra.* 

1. Ezen a Meghívó képernyőn számos részletet fog észrevenni a korábban adatszolgáltatóként konfigurált adatmegoszlottról. Tekintse át a részleteket, és fogadja el a használati feltételeket, ha meg van téve.

1. Válassza ki a tesztkörnyezethez már létező előfizetést és erőforráscsoportot. 

1. Az **Adat share-fiók mezőben** válassza a **DataConsumer lehetőséget.** Létrehozhat egy új adat share-fiókot is. 

1. Figyelje meg, hogy **a Fogadott megosztás neve** mellett az alapértelmezett megosztásnév az adatszolgáltató által megadott név. Adjon egy felhasználóbarát nevet a megosztásnak, amely leírja a kapni fog adatokat, például **TaxiDataShare.**

    ![Meghívó elfogadása](media/lab-data-flow-data-share/consumer-accept.png)

1. Választhatja az Elfogadás és **konfigurálás most vagy** az **Elfogadás és konfigurálás később lehetőséget.** Ha úgy dönt, hogy most elfogadja és konfigurálja, meg kell adnia egy tárfiókot, ahová az összes adatot másolnia kell. Ha később elfogadja és konfigurálja az adatokat, a megosztásban található adatkészletek leképezetlenek lesznek, és manuálisan kell leképezni őket. Erre később még lesz lehetőség. 

1. Válassza az **Elfogadás lehetőséget, és konfigurálja később.** 

    A beállítás konfigurálásakor a rendszer létrehoz egy megosztási előfizetést, de az adatok leesnek, mivel még nem lett leképezve cél. 

    A következő lépés az adatleképezések konfigurálása az adat megosztásához. 

1. Válassza ki a Fogadott megosztást (az 5. lépésben megadott nevet).

    **Az eseményindító pillanatképe** szürkével van kiszürkülve, de a megosztás aktív. 

1. Válassza az **Adatkészletek lapot.** Figyelje meg, hogy minden adatkészlet Leképezetlen, ami azt jelenti, hogy nincs céljuk az adatok másolására. 

    ![leképezetlen adatkészletek](media/lab-data-flow-data-share/unmapped.png)

1. Válassza ki a Azure Synapse Analytics, majd válassza **a + Leképezés a célhoz lehetőséget.**

1. A képernyő jobb oldalán válassza a Cél **adattípus legördülő** menüt. 

    Az SQL-adatokat számos adattárra leképezheti. Ebben az esetben egy új alkalmazásra fogunk leképezni Azure SQL Database.

    ![Hozzárendelés](media/lab-data-flow-data-share/mapping-options.png)
    
    (Nem kötelező) Cél **adattípusként Data Lake Store Azure Data Lake Store Gen2** lehetőséget. 
    
    (Nem kötelező) Válassza ki azt az előfizetést, erőforráscsoportot és tárfiókot, amelyben dolgozott. 
    
    (Nem kötelező) Választhat, hogy csv vagy parquet formátumban fogadja az adatokat a data lake-be. 

1. A Cél **adattípus mellett válassza** a Azure SQL Database. 

1. Válassza ki azt az előfizetést, erőforráscsoportot és tárfiókot, amelyben dolgozott. 

    ![leképezés sql-be](media/lab-data-flow-data-share/map-to-sqldb.png)

1. A folytatás előtt létre kell hoznia egy új felhasználót a SQL Server szkript futtatásával. Először másolja a megadott szkriptet a vágólapra. 

1. Nyisson meg egy Azure Portal lapot. Ne zárja be a meglévő lapot, mert hamarosan vissza kell majd jönnie. 

1. A megnyitott új lapon lépjen az **SQL-adatbázisok lapra.**

1. Válassza ki az SQL-adatbázist (csak egynek kell lennie az előfizetésben). Ügyeljen arra, hogy ne válassza ki az adattárházat. 

1. Lekérdezésszerkesztő **kiválasztása (előzetes verzió)**

1. AAD-hitelesítés használata a Lekérdezésszerkesztőbe való bejelentkezéshez. 

1. Futtassa az adat megosztásában megadott lekérdezést (a 14. lépésben a vágólapra másolható). 

    Ez a parancs lehetővé Azure Data Share, hogy az Azure Data Share szolgáltatás felügyelt identitásokat használjon az Azure-szolgáltatásokhoz a hitelesítéshez a SQL Server, hogy adatokat másoltat a szolgáltatásba. 

1. Vissza az eredeti lapra, és válassza a **Leképezés célként lehetőséget.**

1. Ezután válassza ki az adatkészlet részét tartalmazó Azure Data Lake Gen2 mappát, és képezi le egy Azure Blob Storage fiókra. 

    ![storage](media/lab-data-flow-data-share/storage-map.png)

    Most, hogy minden adatkészlet le van leképezve, készen áll az adatok fogadására az adatszolgáltatótól. 

    ![Leképezés](media/lab-data-flow-data-share/all-mapped.png)
    
1. Válassza a **Részletek lehetőséget.** 

    Figyelje meg, **hogy a Trigger snapshot** (Eseményindító pillanatképe) már nincs szürkével kiszürkülve, mivel az adat megosztásának most már vannak célhelyei, amelyekbe másolni lehet.

1. Válassza az Eseményindító pillanatkép -> Teljes másolás lehetőséget. 

    ![Ravaszt](media/lab-data-flow-data-share/trigger-full.png)

    Ezzel adatokat másol az új adat share-fiókjába. Egy valós forgatókönyvben ezek az adatok egy harmadik féltől jönnek. 

    Az adatok körülbelül 3–5 percet fognak igénybe venni. A folyamat előrehaladását az Előzmények lapra **kattintva követheti** nyomon. 

    Várakozás közben navigáljon az eredeti adat megosztáshoz (DataProvider), és tekintse meg az Előfizetések és **előzmények** megosztása **lap** állapotát. Figyelje meg, hogy most már van aktív előfizetés, és adatszolgáltatóként azt is figyelheti, hogy az adat fogyasztója mikor kezdte meg fogadni a velük megosztott adatokat. 

1. Lépjen vissza az adat fogyasztója adat megosztásához. Ha az eseményindító állapota sikeres, navigáljon a cél SQL-adatbázishoz és a Data Lake-hez, és tekintse meg, hogy az adatok a megfelelő tárolókba megtörténtek-e. 

Gratulálunk, befejezte a labort!