---
title: Az adatátalakítás leképezési adatfolyam használatával
description: Ez az oktatóanyag részletes útmutatást nyújt a Azure Data Factory használatával történő adatátalakításhoz a leképezési adatfolyammal
author: dcstwh
ms.author: weetok
ms.reviewer: makromer
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 03/11/2021
ms.openlocfilehash: 2363afc4c84ee7606410ceecd6819c12d0333dbb
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "105563293"
---
# <a name="transform-data-using-mapping-data-flows"></a>Adatok átalakítása adatfolyamok leképezésével

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Ha még csak ismerkedik az Azure Data Factory használatával, olvassa el [az Azure Data Factory használatának első lépéseit](introduction.md) ismertető cikket.

Ebben az oktatóanyagban a Azure Data Factory felhasználói felületét (UX) fogja használni egy olyan folyamat létrehozásához, amely egy Azure Data Lake Storage-(ADLS-) Gen2-forrásból származó adatok másolását és átalakítását végzi egy ADLS Gen2 fogadóba a leképezési adatfolyam használatával. Az oktatóanyagban szereplő konfigurációs minta kiterjeszthető, ha az adatátalakítást a leképezési folyamat használatával végezi el

 >[!NOTE]
   >Ez az oktatóanyag általában az adatfolyamatok hozzárendelését jelenti. Az adatfolyamatok Azure Data Factory és szinapszis folyamatokban egyaránt elérhetők. Ha még nem ismeri az Azure szinapszis-folyamatokban lévő adatfolyamatokat, kövesse az [Azure szinapszis-folyamatokat használó Adatfolyamatokat](../synapse-analytics/concepts-data-flow-overview.md) . 
   
Az oktatóanyag során a következő lépéseket hajtja végre:

> [!div class="checklist"]
> * Adat-előállító létrehozása
> * Folyamat létrehozása adatfolyam-tevékenységgel.
> * Hozzon létre egy leképezési adatfolyamot négy átalakítással.
> * A folyamat próbafuttatása
> * Adatfolyam-tevékenység figyelése

## <a name="prerequisites"></a>Előfeltételek
* **Azure-előfizetés**. Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes Azure-fiókot](https://azure.microsoft.com/free/) a virtuális gép létrehozásának megkezdése előtt.
* **Azure Storage-fiók**. A ADLS-tárolók *forrásaként* és fogadó *adattárakként* használhatók. Ha még nem rendelkezik tárfiókkal, tekintse meg az [Azure Storage-fiók létrehozásának](../storage/common/storage-account-create.md) lépéseit ismertető cikket.

Az oktatóanyagban átalakított fájl MoviesDB.csv, amely [itt](https://raw.githubusercontent.com/djpmsft/adf-ready-demo/master/moviesDB.csv)található. A fájl GitHubról történő lekéréséhez másolja a tartalmat egy tetszőleges szövegszerkesztőbe, és mentse helyileg a. csv-fájlként. A fájlnak a Storage-fiókba való feltöltéséhez lásd: [Blobok feltöltése a Azure Portal](../storage/blobs/storage-quickstart-blobs-portal.md). Ez a példa egy "Sample-adat" nevű tárolóra hivatkozik.

## <a name="create-a-data-factory"></a>Adat-előállító létrehozása

Ebben a lépésben létrehoz egy adatelőállítót, és megnyitja a Data Factory UX-t egy folyamat létrehozásához az adatelőállítóban.

1. Nyissa meg a **Microsoft Edge** vagy a **Google Chrome böngészőt**. Jelenleg Data Factory felhasználói felület csak a Microsoft Edge és a Google Chrome böngészőben támogatott.
2. A bal oldali menüben válassza az **erőforrás**-  >  **integráció** létrehozása  >  **Data Factory**:

   ![Data Factory kiválasztása az „Új” ablaktáblán](./media/doc-common-process/new-azure-data-factory-menu.png)

3. Az **Új adat-előállító** lap **Név** mezőjében adja meg az **ADFTutorialDataFactory** értéket.

   Az Azure-beli adatgyár nevének *globálisan egyedinek* kell lennie. Ha a név értékével kapcsolatos hibaüzenet kap, adjon meg másik nevet az adat-előállítóhoz. (például Sajátneveadftutorialdatafactory). A Data Factory-összetevők elnevezési szabályait a [Data Factory elnevezési szabályait](naming-rules.md) ismertető cikkben találja.

    :::image type="content" source="./media/doc-common-process/name-not-available-error.png" alt-text="Új adatelőállító-hibaüzenet az ismétlődő névhez.":::
4. Válassza ki azt az **Azure-előfizetést**, amelyben az adat-előállítót létre szeretné hozni.
5. **Erőforráscsoport:** hajtsa végre a következő lépések egyikét:

    a. Válassza a **meglévő használata** lehetőséget, majd válasszon ki egy meglévő erőforráscsoportot a legördülő listából.

    b. Válassza az **új létrehozása** lehetőséget, és adja meg az erőforráscsoport nevét. 
         
    Az erőforráscsoportokkal kapcsolatos információkért tekintse meg az [Erőforráscsoportok használata az Azure-erőforrások kezeléséhez](../azure-resource-manager/management/overview.md) ismertető cikket. 
6. A **Verzió** résznél válassza a **V2** értéket.
7. A **Hely** területen válassza ki az adat-előállító helyét. A legördülő listán csak a támogatott helyek jelennek meg. Az adattárak (például az Azure Storage és a SQL Database) és a számítási erőforrások (például az Azure HDInsight) más régiókban is használhatók.
8. Válassza a **Létrehozás** lehetőséget.
9. A létrehozás befejezése után megjelenik az értesítési központban megjelenő értesítés. Válassza az **Ugrás az erőforráshoz** lehetőséget, hogy megnyissa az adatfeldolgozó lapot.
10. A Data Factory felhasználói felületének külön lapon történő elindításához válassza a **Létrehozás és figyelés** csempét.

## <a name="create-a-pipeline-with-a-data-flow-activity"></a>Folyamat létrehozása adatfolyam-tevékenységgel

Ebben a lépésben olyan folyamatot hoz létre, amely egy adatfolyam-tevékenységet tartalmaz.

1. Az **Első lépések** lapon válassza a **Folyamat létrehozása** lehetőséget.

   ![Folyamat létrehozása](./media/doc-common-process/get-started-page.png)

1. A folyamat **általános** lapján adja meg a **TransformMovies** nevet a folyamat **neveként** .
1. A gyári felső sávban csúsztassa az **adatfolyam hibakeresési** csúszkáját. A hibakeresési mód lehetővé teszi az átalakítási logika interaktív tesztelését egy élő Spark-fürtön. Az adatfolyam-fürtök 5-7 percet vesznek igénybe, és a felhasználóknak javasoljuk, hogy először bekapcsolják a hibakeresést, ha az adatforgalom fejlesztését tervezik. További információ: [hibakeresési mód](concepts-data-flow-debug-mode.md).

    ![Adatfolyam-tevékenység](media/tutorial-data-flow/dataflow1.png)
1. A **tevékenységek** ablaktáblán bontsa ki az **áthelyezés és átalakítás** egyezést. Húzza az **adatfolyam** tevékenységet a panelről a folyamat vászonra.

    ![Képernyőkép, amely megjeleníti a folyamat vászonját, ahol elhúzhatja az adatfolyam tevékenységeit.](media/tutorial-data-flow/activity1.png)
1. Az **adatfolyam hozzáadása** felugró ablakban válassza az **új adatfolyam létrehozása** lehetőséget, majd nevezze el az adatfolyam- **TransformMovies**. Ha elkészült, kattintson a Befejezés gombra.

    ![Képernyőfelvétel: az új adatfolyam létrehozásakor az adatfolyamatok nevének megjelenítése.](media/tutorial-data-flow/activity2.png)

## <a name="build-transformation-logic-in-the-data-flow-canvas"></a>Átalakítási logika létrehozása az adatfolyam-vásznon

Miután létrehozta az adatfolyamatot, a rendszer automatikusan elküldi az adatfolyam-vásznon. Ebben a lépésben egy olyan adatfolyamatot fog létrehozni, amely a ADLS-tárolóban lévő moviesDB.csv viszi, és összesíti a vígjátékok 1910 és 2000 közötti átlagos minősítését. Ezt a fájlt ezután vissza kell írnia a ADLS-tárolóba.

1. Az adatfolyam-vásznon adja hozzá a forrást a **forrás hozzáadása** mezőre kattintva.

    ![Képernyőkép, amely a forrás hozzáadása mezőt jeleníti meg.](media/tutorial-data-flow/dataflow2.png)
1. Nevezze el a forrás **MoviesDB**. Új forrás adatkészlet létrehozásához kattintson az **új** elemre.

    ![Képernyőkép, amely azt mutatja, hogy hol válassza az új lehetőséget a forrás nevének megadása után.](media/tutorial-data-flow/dataflow3.png)
1. Válassza a **Azure Data Lake Storage Gen2** lehetőséget. Kattintson a Folytatás gombra.

    ![Képernyőkép, amely megjeleníti a Azure Data Lake Storage Gen2 csempét.](media/tutorial-data-flow/dataset1.png)
1. Válassza a **DelimitedText** lehetőséget. Kattintson a Folytatás gombra.

    ![Képernyőkép, amely a DelimitedText csempét jeleníti meg.](media/tutorial-data-flow/dataset2.png)
1. Nevezze el az adatkészlet **MoviesDB**. A társított szolgáltatás legördülő menüben válassza az **új** lehetőséget.

    ![A társított szolgáltatás legördülő listáját megjelenítő képernyőkép.](media/tutorial-data-flow/dataset3.png)
1. A társított szolgáltatás létrehozása képernyőn nevezze el a ADLS Gen2 társított szolgáltatás **ADLSGen2** , és adja meg a hitelesítési módszert. Ezután adja meg a kapcsolatok hitelesítő adatait. Ebben az oktatóanyagban a fiók kulcsát használjuk a Storage-fiókhoz való kapcsolódáshoz. Kattintson a **Kapcsolódás tesztelése** lehetőségre a hitelesítő adatok helyes beírásának ellenőrzéséhez. Ha elkészült, kattintson a Létrehozás gombra.

    ![Társított szolgáltatás](media/tutorial-data-flow/ls1.png)
1. Miután visszatért az adatkészlet-létrehozási képernyőre, adja meg, hogy hol található a fájl **elérési útja** mezőben. Ebben az oktatóanyagban a fájl moviesDB.csv található a tároló mintája – adathalmazban. Ahogy a fájl fejléceket tartalmaz, tekintse meg az **első sort fejlécként**. Válassza a **Kapcsolódás/áruház** lehetőséget, hogy a fejléc-sémát közvetlenül a tárolóban lévő fájlból importálja. Ha elkészült, kattintson az OK gombra.

    ![Adathalmazok](media/tutorial-data-flow/dataset4.png)
1. Ha a hibakeresési fürt elindult, lépjen a forrás-átalakítás **adatelőnézet** lapjára, és kattintson a **frissítés** gombra az adatok pillanatképének beolvasásához. Az adatelőnézet használatával ellenőrizheti, hogy az átalakítás megfelelően van-e konfigurálva.

    ![Képernyőkép, amely bemutatja, hogy a rendszer hol tekintheti meg az adatait, és ellenőrizze, hogy az átalakítás megfelelően van-e konfigurálva.](media/tutorial-data-flow/dataflow4.png)
1. Az adatfolyam-vászonon a forrás csomópont mellett kattintson a plusz ikonra egy új átalakítás hozzáadásához. A hozzáadott első átalakítás egy **szűrő**.

    ![Adatfolyam-vászon](media/tutorial-data-flow/dataflow5.png)
1. Nevezze el a szűrő átalakítási **FilterYears**. Kattintson a **szűrés** elem melletti kifejezés mezőre a Kifejezésszerkesztő megnyitásához. Itt adja meg a szűrési feltételt.

    ![A szűrést a kifejezés mezőben megjelenítő képernyőkép.](media/tutorial-data-flow/filter1.png)
1. Az adatfolyam-kifejezés-szerkesztővel interaktív módon hozhat létre kifejezéseket különböző átalakításokban való használatra. A kifejezések tartalmazhatnak beépített függvényeket, a bemeneti sémából származó oszlopokat és a felhasználó által definiált paramétereket. A kifejezések létrehozásával kapcsolatos további információkért lásd: [adatáramlási kifejezés-szerkesztő](concepts-data-flow-expression-builder.md).

    Ebben az oktatóanyagban a műfaji komédia azon filmjeit szeretné szűrni, amelyek a 1910-es és a 2000-as évek közötti időszakban jöttek létre. Az év jelenleg karakterlánc, a függvény használatával át kell alakítani egész számra ```toInteger()``` . Használja a nagyobb vagy egyenlő értéket (>=), és kisebb vagy egyenlő, mint a (<=) operátorok az 1910 és a 200 – literális Year értékekkel való összehasonlításhoz. Egyesítse ezeket a kifejezéseket a és a (&&) operátorral együtt. A kifejezés a következőképpen érkezik:

    ```toInteger(year) >= 1910 && toInteger(year) <= 2000```

    Ha szeretné megkeresni, hogy mely filmek a vígjátékok, a ```rlike()``` függvény használatával megtalálhatja a "komédia" mintát az oszlopok műfajában. Union The rlike kifejezés az év összehasonlításával a Get:

    ```toInteger(year) >= 1910 && toInteger(year) <= 2000 && rlike(genres, 'Comedy')```

    Ha a hibakeresési fürt aktív, a **frissítés** gombra kattintva ellenőrizheti, hogy a kifejezés kimenete a használt bemenetekhez képest látható-e. A logikát az adatáramlás kifejezésének nyelve alapján több, mint egy megfelelő választ kaphat.

    ![Szűrő](media/tutorial-data-flow/filter2.png)

    Kattintson a **Mentés és Befejezés** gombra, ha elkészült a kifejezéssel.

1. Egy **Adatelőnézet** beolvasása annak ellenőrzéséhez, hogy a szűrő megfelelően működik-e.

    ![Képernyőkép, amely a beolvasott adatelőnézetet jeleníti meg.](media/tutorial-data-flow/filter3.png)
1. A hozzáadni kívánt következő átalakítás a **séma-módosító** alatt létrehozott **összesített** transzformáció.

    ![Az összesített séma-módosítót megjelenítő képernyőkép.](media/tutorial-data-flow/agg1.png)
1. Nevezze el az összesített átalakítási **AggregateComedyRatings**. A **Csoportosítás** lapon válassza az **év** lehetőséget a legördülő listából, hogy csoportosítsa az összesítéseket a film által kiváltott év alapján.

    ![Képernyőfelvétel: az év lehetőséget a Group By (összesítési beállítások) lapon.](media/tutorial-data-flow/agg2.png)
1. Nyissa meg az **összesítések** lapot. A bal oldali szövegmezőben nevezze el az összesítő oszlop **AverageComedyRating**. Kattintson a jobb oldali kifejezés mezőre az összesítő kifejezés megadásához a Expression Builder használatával.

    ![Képernyőkép, amely az összesítési beállítások lapon az év lehetőséget jeleníti meg.](media/tutorial-data-flow/agg3.png)
1. Az oszlop- **minősítés** átlagának lekéréséhez használja az ```avg()``` összesítő függvényt. Mivel a **minősítés** egy karakterlánc ```avg()``` , és egy numerikus bemenetet vesz igénybe, az értéket egy számra kell konvertálnia a ```toInteger()``` függvényen keresztül. A kifejezés a következőképpen néz ki:

    ```avg(toInteger(Rating))```

    Ha elkészült **, kattintson a Mentés és Befejezés** gombra.

    ![A mentett kifejezést megjelenítő képernyőkép.](media/tutorial-data-flow/agg4.png)
1. Az átalakítás kimenetének megtekintéséhez nyissa meg az **adatelőnézet** lapot. Figyelje meg, hogy csak két oszlop van, az **év** és a **AverageComedyRating**.

    ![Összesítés](media/tutorial-data-flow/agg3.png)
1. Ezután hozzá kíván **adni egy** fogadó átalakítást a **célhely** területen.

    ![Képernyőkép, amely bemutatja, hová kell hozzáadni a fogadó átalakítást a célhelyen.](media/tutorial-data-flow/sink1.png)
1. Nevezze el a fogadó fogadót. A fogadó adatkészlet létrehozásához kattintson az **új** elemre.

    ![Képernyőkép, amely megjeleníti a fogadó nevét, és létrehoz egy új fogadó adatkészletet.](media/tutorial-data-flow/sink2.png)
1. Válassza a **Azure Data Lake Storage Gen2** lehetőséget. Kattintson a Folytatás gombra.

    ![A kiválasztható Azure Data Lake Storage Gen2 csempét bemutató képernyőkép.](media/tutorial-data-flow/dataset1.png)
1. Válassza a **DelimitedText** lehetőséget. Kattintson a Folytatás gombra.

    ![Adathalmaz](media/tutorial-data-flow/dataset2.png)
1. Nevezze el a fogadó adatkészletet **MoviesSink**. A társított szolgáltatás mezőben válassza ki a 6. lépésben létrehozott ADLS Gen2 társított szolgáltatást. Adja meg azt a kimeneti mappát, ahová az adatokat írni kívánja. Ebben az oktatóanyagban a "kimenet" mappába írunk a "Sample-recontainer" tárolóban. A mappának nem kell előre megadnia, és dinamikusan létre lehet hozni. Állítsa az **első sort fejlécként** True (igaz) értékre, és válassza a **nincs lehetőséget** a **séma importálása** lehetőségnél. Kattintson a Finish (Befejezés) gombra.

    ![Sink (Fogadó)](media/tutorial-data-flow/sink3.png)

Most, hogy befejezte az adatfolyam felépítése. Készen áll arra, hogy a folyamaton fusson.

## <a name="running-and-monitoring-the-data-flow"></a>Az adatfolyam futtatása és figyelése

A folyamat a közzététel előtt hibakeresést végezhet. Ebben a lépésben az adatfolyam-folyamat hibakeresési futtatását fogja elindítani. Míg az adatelőnézet nem ír adatírást, a hibakeresési kísérlet az adatait a fogadó célhelyére fogja írni.

1. Nyissa meg a folyamat vászonját. Hibakeresési Futtatás indításához kattintson a **hibakeresés** gombra.

    ![Képernyőkép, amely megjeleníti a folyamat vászonját a hibakeresés kiemelésével.](media/tutorial-data-flow/pipeline1.png)
1. Az adatfolyam-tevékenységek feldolgozási folyamata az aktív hibakeresési fürtöt használja, de még legalább egy percet is igénybe kell vennie az inicializáláshoz. Az előrehaladást a **kimenet** lapon követheti nyomon. Ha a Futtatás sikeres, kattintson a szemüvegek ikonra a figyelés ablaktábla megnyitásához.

    ![Folyamat](media/tutorial-data-flow/pipeline2.png)
1. A figyelés ablaktáblán láthatja az egyes átalakítási lépésekben eltöltött sorok és idő számát.

    ![A figyelés ablaktáblát megjelenítő képernyőkép, amelyen megtekintheti az egyes átalakítási lépésekben eltöltött sorok és idő számát.](media/tutorial-data-flow/pipeline3.png)
1. A transzformációra kattintva részletes információkat kaphat az oszlopokról és az adatok particionálásáról.

    ![Figyelés](media/tutorial-data-flow/pipeline4.png)

Ha ezt az oktatóanyagot helyesen követte, a fogadó mappájába írt 83 és 2 oszlopot kell írnia. A blob Storage ellenőrzésével ellenőrizheti, hogy helyesek-e az adatok.

## <a name="next-steps"></a>Következő lépések

Az oktatóanyagban szereplő folyamat egy olyan adatfolyamot futtat, amely összegzi a 1910 és 2000 közötti vígjátékok átlagos minősítését, és az adatokat a ADLS írja. Megtanulta végrehajtani az alábbi műveleteket:

> [!div class="checklist"]
> * Adat-előállító létrehozása
> * Folyamat létrehozása adatfolyam-tevékenységgel.
> * Hozzon létre egy leképezési adatfolyamot négy átalakítással.
> * A folyamat próbafuttatása
> * Adatfolyam-tevékenység figyelése

További információ az [adatfolyam kifejezésének nyelvéről](data-flow-expression-functions.md).