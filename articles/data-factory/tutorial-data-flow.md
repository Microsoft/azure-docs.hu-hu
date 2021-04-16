---
title: Adatok átalakítása leképezési adatfolyam használatával
description: Ez az oktatóanyag részletes útmutatást nyújt a Azure Data Factory adatok leképezési adatfolyammal való átalakításához
author: dcstwh
ms.author: weetok
ms.reviewer: makromer
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 04/14/2021
ms.openlocfilehash: 0842dad0e0ea6f9987727e8abf3d0eaf8a59e821
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/15/2021
ms.locfileid: "107517518"
---
# <a name="transform-data-using-mapping-data-flows"></a>Adatok átalakítása adatfolyamok leképezésével

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Ha még csak ismerkedik az Azure Data Factory használatával, olvassa el [az Azure Data Factory használatának első lépéseit](introduction.md) ismertető cikket.

Ebben az oktatóanyagban az Azure Data Factory felhasználói felület (UX) használatával fog létrehozni egy folyamatot, amely adatokat másol és alakít át egy Azure Data Lake Storage-forrásból (ADLS) Gen2-forrásból egy ADLS Gen2-fogadóba leképezési adatfolyam használatával. Az oktatóanyagban használt konfigurációs minta kibővíthető az adatok leképezési adatfolyam használatával való átalakításakor

 >[!NOTE]
   >Ez az oktatóanyag általában adatfolyamok leképezésére való. Az adatfolyamok a Azure Data Factory Synapse Pipelinesban is elérhetők. Ha még nem használja az adatfolyamokat a Azure Synapse Pipelinesban, kövesse az Adatfolyam [a Azure Synapse folyamatok használatával](../synapse-analytics/concepts-data-flow-overview.md) 
   
Az oktatóanyag során a következő lépéseket hajtja végre:

> [!div class="checklist"]
> * Adat-előállító létrehozása
> * Hozzon létre egy folyamatot egy Adatfolyam tevékenységgel.
> * Leképezési adatfolyam összeállítása négy átalakítással.
> * A folyamat próbafuttatása
> * Tevékenység Adatfolyam figyelése

## <a name="prerequisites"></a>Előfeltételek
* **Azure-előfizetés.** Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes Azure-fiókot](https://azure.microsoft.com/free/) a virtuális gép létrehozásának megkezdése előtt.
* **Azure Storage-fiók.** Az ADLS-tárolót *forrás- és* *fogadóadattárként* használhatja. Ha még nem rendelkezik tárfiókkal, tekintse meg az [Azure Storage-fiók létrehozásának](../storage/common/storage-account-create.md) lépéseit ismertető cikket.

Az oktatóanyagban átalakított fájl MoviesDB.csv, amely itt [található.](https://raw.githubusercontent.com/djpmsft/adf-ready-demo/master/moviesDB.csv) A fájl GitHubról való lekéréséhez másolja a tartalmát egy ön által választott szövegszerkesztőbe a helyi .csv-fájlként való mentéshez. A fájl tárfiókba való feltöltésével lásd: [Blobok feltöltése a Azure Portal.](../storage/blobs/storage-quickstart-blobs-portal.md) A példák a "sample-data" nevű tárolóra hivatkoznak.

## <a name="create-a-data-factory"></a>Adat-előállító létrehozása

Ebben a lépésben létrehoz egy adat-előállítót, és megnyitja a Data Factory felhasználói felületét egy folyamat létrehozásához az adat-előállítóban.

1. Nyissa **meg Microsoft Edge** vagy a Google Chrome **böngészőt.** A felhasználói Data Factory jelenleg csak a böngészők és a Google Chrome böngészőkben Microsoft Edge támogatottak.
2. A bal oldali menüben válassza az **Erőforrás-integráció** létrehozása  >  **lehetőséget**  >  **Data Factory:**

   ![Data Factory kiválasztása az „Új” ablaktáblán](./media/doc-common-process/new-azure-data-factory-menu.png)

3. Az **Új adat-előállító** lap **Név** mezőjében adja meg az **ADFTutorialDataFactory** értéket.

   Az Azure data factory nevének globálisan *egyedinek kell lennie.* Ha a név értékével kapcsolatos hibaüzenet kap, adjon meg másik nevet az adat-előállítóhoz. (például sajátneveADFTutorialDataFactory). A Data Factory-összetevők elnevezési szabályait a [Data Factory elnevezési szabályait](naming-rules.md) ismertető cikkben találja.

    :::image type="content" source="./media/doc-common-process/name-not-available-error.png" alt-text="Új data factory hibaüzenet duplikált név esetén.":::
4. Válassza ki azt az **Azure-előfizetést**, amelyben az adat-előállítót létre szeretné hozni.
5. **Erőforráscsoport:** hajtsa végre a következő lépések egyikét:

    a. Válassza **a Meglévő használata** lehetőséget, majd válasszon ki egy meglévő erőforráscsoportot a legördülő listából.

    b. Válassza **az Új létrehozása** lehetőséget, és adja meg egy erőforráscsoport nevét. 
         
    Az erőforráscsoportokkal kapcsolatos információkért tekintse meg az [Erőforráscsoportok használata az Azure-erőforrások kezeléséhez](../azure-resource-manager/management/overview.md) ismertető cikket. 
6. A **Verzió** résznél válassza a **V2** értéket.
7. A **Hely** területen válassza ki az adat-előállító helyét. A legördülő listán csak a támogatott helyek jelennek meg. Az adat-előállítók által használt adattárak (például az Azure Storage és SQL Database) és számítási erőforrások (például Azure HDInsight) más régiókban is előfordulhatnak.
8. Válassza a **Létrehozás** lehetőséget.
9. A létrehozás befejezése után az értesítés megjelenik az Értesítési központban. Az **Adat-előállító lapra** való navigáláshoz válassza az Ugrás az erőforráshoz lehetőséget.
10. A Data Factory felhasználói felületének külön lapon történő elindításához válassza a **Létrehozás és figyelés** csempét.

## <a name="create-a-pipeline-with-a-data-flow-activity"></a>Folyamat létrehozása egy Adatfolyam tevékenységgel

Ebben a lépésben egy folyamatot fog létrehozni, amely egy Adatfolyam tartalmaz.

1. Az **Első lépések** lapon válassza a **Folyamat létrehozása** lehetőséget.

   ![Folyamat létrehozása](./media/doc-common-process/get-started-page.png)

1. A folyamat **Általános** lapján adja meg a **TransformMovies** nevet **a folyamat** neveként.
1. A Tevékenységek **panelen** bontsa ki a **Move and Transform (Áthelyezés és átalakítás)** et. Húzza át a **Adatfolyam** tevékenységet a panelről a folyamatvászonra.

    ![Képernyőkép a folyamatvászonról, ahol eldobhatja a Adatfolyam tevékenységet.](media/tutorial-data-flow/activity1.png)
1. Az Új **Adatfolyam** előugró ablakban válassza az Új alkalmazás **létrehozása lehetőséget Adatfolyam** az adatfolyamnak adja a **TransformMovies nevet.** Ha végzett, kattintson a Befejezés gombra.

    ![Új adatfolyam létrehozásakor az adatfolyam elnevezési helyét bemutató képernyőkép.](media/tutorial-data-flow/activity2.png)
1. A folyamatvászon felső sávjában húzza be a Adatfolyam **hibakeresési csúszkát.** A hibakeresési mód lehetővé teszi az átalakítási logika interaktív tesztelését egy élő Spark-fürtön. Adatfolyam 5–7 percet is igénybe vegyen a bemelegítés, és a felhasználóknak ajánlott először bekapcsolni a hibakeresést, ha Adatfolyam terveznek. További információ: [Hibakeresési mód.](concepts-data-flow-debug-mode.md)

    ![Adatfolyam tevékenység](media/tutorial-data-flow/dataflow1.png)

## <a name="build-transformation-logic-in-the-data-flow-canvas"></a>Átalakítási logika összeállítása az adatfolyam-vásznon

Miután létrehozott egy Adatfolyam, a rendszer automatikusan az adatfolyamvászonra küldi. Ebben a lépésben egy olyan adatfolyamot fog felépíteni, amely az ADLS-tárolóban moviesDB.csv és összesíti a comedies átlagos minősítését 1910 és 2000 között. Ezután ezt a fájlt visszaírja az ADLS-tárolóba.

1. Az adatfolyam vásznán adjon hozzá egy forrást a **Forrás hozzáadása mezőre** kattintva.

    ![A Forrás hozzáadása mezőt bemutató képernyőkép.](media/tutorial-data-flow/dataflow2.png)
1. A forrásnak a **MoviesDB nevet kell ad.** Új **forrásadatkészlet** létrehozásához kattintson az Új elemre.

    ![A forrás elnevezése után az Új kiválasztásának helyét bemutató képernyőkép.](media/tutorial-data-flow/dataflow3.png)
1. Válassza a **Azure Data Lake Storage Gen2** lehetőséget. Kattintson a Folytatás gombra.

    ![Képernyőkép a Azure Data Lake Storage Gen2 csempéről.](media/tutorial-data-flow/dataset1.png)
1. Válassza **a Tagolt Szöveg lehetőséget.** Kattintson a Folytatás gombra.

    ![A Tagolt Szöveg csempét bemutató képernyőkép.](media/tutorial-data-flow/dataset2.png)
1. Nevezze el az adatkészletet **MoviesDB néven.** A csatolt szolgáltatás legördülő menüben válassza az Új **lehetőséget.**

    ![Képernyőkép a Csatolt szolgáltatás legördülő listáról.](media/tutorial-data-flow/dataset3.png)
1. A csatolt szolgáltatás létrehozási képernyőjén nevezze el az ADLS gen2 csatolt szolgáltatást **ADLSGen2** néven, és adja meg a hitelesítési módszert. Ezután adja meg a kapcsolat hitelesítő adatait. Ebben az oktatóanyagban a fiókkulcsot használjuk a tárfiókhoz való csatlakozáshoz. A Kapcsolat tesztelése **gombra kattintva** ellenőrizheti, hogy helyesen adta-e meg a hitelesítő adatait. Ha elkészült, kattintson a Létrehozás gombra.

    ![Csatolt szolgáltatás](media/tutorial-data-flow/ls1.png)
1. Miután visszatért az adatkészlet létrehozására vonatkozó képernyőre, adja meg, hol található a fájl a Fájl elérési útja **mezőben.** Ebben az oktatóanyagban a moviesDB.csv tároló mintaadatok között található. Mivel a fájl fejlécekkel rendelkezik, jelölje be az **Első sor fejlécként jelölőnégyzetet.** Válassza **a Kapcsolat/tárolóból** lehetőséget a fejlécséma importáláshoz közvetlenül a tárolóban található fájlból. Ha végzett, kattintson az OK gombra.

    ![Adathalmazok](media/tutorial-data-flow/dataset4.png)
1. Ha a hibakeresési fürt elindult, a forrásátalakítás Adatelőnézet lapján kattintson a Frissítés gombra az adatok pillanatképének megtekintéséhez.   Az adatelőnézettel ellenőrizheti, hogy az átalakítás megfelelően van-e konfigurálva.

    ![Képernyőkép az adatok előnézetének megtekintéséről az átalakítás megfelelő konfigurálásának ellenőrzéséhez.](media/tutorial-data-flow/dataflow4.png)
1. Új átalakítás hozzáadásához kattintson a plusz ikonra a forráscsomópont mellett az adatfolyam vásznán. Az első hozzáadott átalakítás egy **szűrő.**

    ![Adatfolyam vászon](media/tutorial-data-flow/dataflow5.png)
1. Nevezze el a szűrőátalakítást **FilterYears néven.** A Kifejezésszerkesztő megnyitásához kattintson a **Szűrés a következőn melletti** kifejezésmezőre. Itt megadhatja a szűrési feltételt.

    ![A Kifejezés szűrése mezőt bemutató képernyőkép.](media/tutorial-data-flow/filter1.png)
1. Az adatfolyam-kifejezésszerkesztővel interaktív módon építhet ki különböző átalakítások során használható kifejezéseket. A kifejezések beépített függvényeket, a bemeneti séma oszlopait és a felhasználó által megadott paramétereket tartalmazhatnak. A kifejezések felépítésével kapcsolatos további információkért lásd: Adatfolyam [kifejezésszerkesztő.](concepts-data-flow-expression-builder.md)

    Ebben az oktatóanyagban az 1910 és 2000 közötti műfaji műfajok filmjeit szeretné szűrni. Mivel az év jelenleg egy sztring, egész számra kell konvertálni a függvény ```toInteger()``` használatával. A nagyobb vagy egyenlő (>=) és kisebb vagy egyenlő (<=) operátorok használatával hasonlíthatja össze az 1910-es és 200-as konstansértékeket. Ezeket a kifejezéseket a és a (&&) operátorral összesiti. A kifejezés a következőként jön ki:

    ```toInteger(year) >= 1910 && toInteger(year) <= 2000```

    Annak kiderítésében, hogy mely filmek a vétesek, a függvény használatával megkeresheti a ```rlike()``` "Which" mintát az oszlop műfajai között. Az rlike kifejezés és az év összehasonlításának uniója a következőképpen:

    ```toInteger(year) >= 1910 && toInteger(year) <= 2000 && rlike(genres, 'Comedy')```

    Ha aktív a hibakeresési fürt, a Frissítés  gombra kattintva ellenőrizheti a logikát, így láthatja a kifejezés kimenetét a használt bemenetekkel összehasonlítva. Több helyes válasz is van arra, hogyan valósítható meg ez a logika az adatfolyam-kifejezés nyelv használatával.

    ![Szűrő](media/tutorial-data-flow/filter2.png)

    Ha **végzett** a kifejezéssel, kattintson a Mentés és befejezés gombra.

1. A szűrő **megfelelő működését a Data Preview lekérése** után ellenőrizheti.

    ![A lekért Adatelőnézet képernyőképe.](media/tutorial-data-flow/filter3.png)
1. A következő hozzáadni fog egy Összesítés átalakítást **a** **Sémamódosító alatt.**

    ![Képernyőkép az Aggregate schema módosítóról.](media/tutorial-data-flow/agg1.png)
1. Az összesített átalakításnak nevezze el **az AggregateComedyRatings nevet.** A Csoportosítás **lapon** a legördülő **menüből** válassza az év lehetőséget, hogy az összesítéseket a film kiugró évének szerint csoportosítsa.

    ![Képernyőkép az Év lehetőségről a Csoportosítási beállítások lapon.](media/tutorial-data-flow/agg2.png)
1. Ugrás az **Összesítések lapra.** A bal oldali szövegmezőben adja az **AverageComedyRating** nevet az összesített oszlopnak. A jobb kifejezésmezőre kattintva adja meg az összesítő kifejezést a kifejezésszerkesztőben.

    ![Képernyőkép az Összesítések lap Beállítások összesítése lapján található Év lehetőségről.](media/tutorial-data-flow/agg3.png)
1. A Minősítés oszlop átlagának **lekért értékhez** használja az ```avg()``` aggregátum függvényt. Mivel **az Értékelés** egy sztring, és numerikus bemenetet vesz fel, az értéket számra kell konvertálnunk a ```avg()``` ```toInteger()``` függvényen keresztül. Ez a kifejezés a következő:

    ```avg(toInteger(Rating))```

    Ha **végzett, kattintson** a Mentés és befejezés gombra.

    ![Képernyőkép a mentett kifejezésről.](media/tutorial-data-flow/agg4.png)
1. A **transzformáció kimenetének megtekintéséhez** válassza az Adatelőnézet lapot. Figyelje meg, hogy csak két oszlop van: **year** és **AverageComedyRating.**

    ![Összesítés](media/tutorial-data-flow/agg3.png)
1. Ezután hozzá kell adni egy **fogadó-átalakítást** a Cél **alatt.**

    ![Képernyőkép a fogadó-átalakítás célhelyhez való hozzáadásáról a Cél alatt.](media/tutorial-data-flow/sink1.png)
1. A fogadónak nevezze el a **fogadót.** Kattintson **az Új** gombra a fogadó adatkészlet létrehozásához.

    ![Képernyőkép a fogadó elnevezési helyének és új fogadó adatkészletének létrehozásáról.](media/tutorial-data-flow/sink2.png)
1. Válassza a **Azure Data Lake Storage Gen2** lehetőséget. Kattintson a Folytatás gombra.

    ![Képernyőkép a Azure Data Lake Storage Gen2 csempéről.](media/tutorial-data-flow/dataset1.png)
1. Válassza **a Tagolt Szöveg lehetőséget.** Kattintson a Folytatás gombra.

    ![Adathalmaz](media/tutorial-data-flow/dataset2.png)
1. Nevezze el a fogadó adatkészletet **MoviesSink néven.** A csatolt szolgáltatáshoz válassza ki a 6. lépésben létrehozott ADLS gen2-et. Adjon meg egy kimeneti mappát az adatok írásához. Ebben az oktatóanyagban a "sample-data" tároló output mappájába írunk. A mappának nem kell előre léteznie, és dinamikusan is létre lehet őket létrehozni. Állítsa **az Első sor fejlécet** igazra, és válassza a Nincs **lehetőséget** a **Séma importálása beállításhoz.** Kattintson a Finish (Befejezés) gombra.

    ![Sink (Fogadó)](media/tutorial-data-flow/sink3.png)

Az adatfolyam elkészült. Készen áll arra, hogy futtassa a folyamatban.

## <a name="running-and-monitoring-the-data-flow"></a>Az alkalmazás futtatása és Adatfolyam

Közzététel előtt hibakeresést is futtathat a folyamatokon. Ebben a lépésben elindítja az adatfolyamat hibakeresési futtatását. Bár az adatelőnézet nem ír adatokat, a hibakeresési futtatás adatokat ír a fogadó célhelyre.

1. Ugrás a folyamatvászonra. Kattintson **a Hibakeresés** gombra a hibakeresési futtatás aktiválásához.

    ![Képernyőkép a folyamatvászonról a debug kiemeléssel.](media/tutorial-data-flow/pipeline1.png)
1. Az összes Adatfolyam folyamat hibakeresése az aktív hibakeresési fürtöt használja, de az inicializálása még legalább egy percet igénybe veszi. A folyamat előrehaladását a Kimenet lapon **követheti** nyomon. Ha a futtatás sikeres volt, kattintson a szemüveg ikonra a figyelési panel megnyitásához.

    ![Folyamat](media/tutorial-data-flow/pipeline2.png)
1. A figyelési panelen láthatja az egyes átalakítási lépés során töltött sorok számát és idejét.

    ![Képernyőkép a figyelési panelről, ahol az egyes átalakítási lépésekkel töltött sorok és idő látható.](media/tutorial-data-flow/pipeline3.png)
1. Az átalakításra kattintva részletes információkat kap az oszlopokról és az adatok particionálásáról.

    ![Figyelés](media/tutorial-data-flow/pipeline4.png)

Ha megfelelően követte ezt az oktatóanyagot, 83 sort és 2 oszlopot kellett írnia a fogadómappába. A blobtároló ellenőrzésével ellenőrizheti, hogy az adatok helyesek-e.

## <a name="next-steps"></a>Következő lépések

Az oktatóanyagban a folyamat egy adatfolyamot futtat, amely összesíti a comedies átlagos minősítését 1910-től 2000-ig, és az adatokat az ADLS-be írja. Megtanulta végrehajtani az alábbi műveleteket:

> [!div class="checklist"]
> * Adat-előállító létrehozása
> * Hozzon létre egy folyamatot egy Adatfolyam tevékenységgel.
> * Leképezési adatfolyam összeállítása négy átalakítással.
> * A folyamat próbafuttatása
> * Tevékenység Adatfolyam figyelése

További információ az [adatfolyam-kifejezés nyelvről.](data-flow-expression-functions.md)