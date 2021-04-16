---
title: 'Rövid útmutató: Adatok átalakítása leképezési adatfolyam használatával'
description: Ez az oktatóanyag részletes útmutatást nyújt a Azure Synapse Analytics adatok leképezési adatfolyammal való átalakításához.
author: djpmsft
ms.author: daperlov
ms.reviewer: makromer
ms.service: synapse-analytics
ms.subservice: pipeline
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 11/03/2020
ms.openlocfilehash: 37696d2f4054e46125b39f3d5efa794ce54f94b5
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2021
ms.locfileid: "107567723"
---
# <a name="quickstart-transform-data-using-mapping-data-flows"></a>Rövid útmutató: Adatok átalakítása adatfolyamok leképezése használatával

Ebben a rövid útmutatóban az Azure Synapse Analytics használatával fog létrehozni egy folyamatot, amely leképezési adatfolyam használatával átalakítja az adatokat egy Azure Data Lake Storage Gen2- (ADLS Gen2-) forrásból egy ADLS Gen2-fogadóvá. A rövid útmutatóban használt konfigurációs minta kibővíthető az adatok leképezési adatfolyam használatával való átalakításakor

Ebben a rövid útmutatóban a következő lépéseket kell követnie:

> [!div class="checklist"]
> * Hozzon létre egy folyamatot egy Adatfolyam tevékenységgel a Azure Synapse Analytics.
> * Leképezési adatfolyam összeállítása négy átalakítással.
> * A folyamat próbafuttatása
> * Tevékenység Adatfolyam figyelése

## <a name="prerequisites"></a>Előfeltételek

* **Azure-előfizetés**: Ha nem rendelkezik Azure-előfizetéssel, első lépésként mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/).
* **Azure Synapse munkaterület:** Hozzon létre egy Synapse-munkaterületet a Azure Portal a [Rövid útmutató: Synapse-munkaterület létrehozása útmutatását követve.](quickstart-create-workspace.md)
* **Azure Storage-fiók:** Az ADLS-tárolót használja *forrás-* és *fogadóadattárként.* Ha még nem rendelkezik tárfiókkal, tekintse meg az [Azure Storage-fiók létrehozásának](../storage/common/storage-account-create.md) lépéseit ismertető cikket.

    Az oktatóanyagban átalakított fájl MoviesDB.csv, amely itt [található.](https://raw.githubusercontent.com/djpmsft/adf-ready-demo/master/moviesDB.csv) A fájl GitHubról való lekéréséhez másolja a tartalmát egy ön által választott szövegszerkesztőbe, hogy helyileg .csv-fájlként mentse. A fájl tárfiókba való feltöltésével lásd: [Blobok feltöltése a Azure Portal.](../storage/blobs/storage-quickstart-blobs-portal.md) A példák egy "sample-data" nevű tárolóra hivatkoznak.

### <a name="navigate-to-the-synapse-studio"></a>Lépjen a Synapse Studio

A Azure Synapse létrehozása után két módon nyithatja meg a Synapse Studio:

* Nyissa meg a Synapse-munkaterületet a [Azure Portal.](https://ms.portal.azure.com/#home) Az **Első lépések** alatt válassza Synapse Studio Megnyitás lehetőséget.
* Nyissa [Azure Synapse Analytics,](https://web.azuresynapse.net/) és jelentkezzen be a munkaterületre.

Ebben a rövid útmutatóban az "adftest2020" nevű munkaterületet használjuk példaként. Automatikusan a kezdőlapra Synapse Studio meg.

![Synapse Studio kezdőlapja](media/doc-common-process/synapse-studio-home.png)

## <a name="create-a-pipeline-with-a-data-flow-activity"></a>Folyamat létrehozása egy Adatfolyam tevékenységgel

A folyamat egy tevékenységkészlet végrehajtásának logikai folyamatát tartalmazza. Ebben a szakaszban egy folyamatot fog létrehozni, amely egy Adatfolyam tartalmaz.

1. Ugrás az **Integrálás lapra.** Válassza a pipelines fejléc melletti plusz ikont, majd a Pipeline (Folyamat) lehetőséget.

   ![Új folyamat létrehozása](media/doc-common-process/new-pipeline.png)

1. A folyamat **Tulajdonságok** beállításai lapján a Név alatt adja meg a **TransformMovies** **nevet.**

1. A *Tevékenységek ablaktáblán* az Áthelyezés és átalakítás *alatt* húzza az **Adatfolyam adatokat** a folyamatvászonra.

1. Az Adatfolyam **hozzáadása előugró** ablakban válassza az **Új adatfolyam létrehozása** Adatfolyam  ->  **lehetőséget.** Ha **végzett, kattintson** az OK gombra.

   ![Adatfolyam létrehozása](media/quickstart-data-flow/new-data-flow.png)

1. Az adatfolyamnak a Tulajdonságok lapon nevezze el a **TransformMovies** **nevet.**

## <a name="build-transformation-logic-in-the-data-flow-canvas"></a>Átalakítási logika összeállítása az adatfolyam-vásznon

Miután létrehozott egy Adatfolyam, a rendszer automatikusan az adatfolyamvászonra küldi. Ebben a lépésben egy olyan adatfolyamot fog felépíteni, amely az ADLS-tárolóban MoviesDB.csv- és a 1910 és 2000 között összesítette a comedies (1910 és 2000) átlagos minősítését. Ezt a fájlt ezután visszaírja az ADLS-tárolóba.

1. Az adatfolyam vászna felett húzza be az Adatfolyam **hibakeresési csúszkát.** A hibakeresési mód lehetővé teszi az átalakítási logika interaktív tesztelését egy élő Spark-fürtön. Adatfolyam 5–7 percet is igénybe vegyen a bemelegítés, és a felhasználóknak ajánlott először bekapcsolni a hibakeresést, ha Adatfolyam terveznek. További információ: [Hibakeresési mód.](../data-factory/concepts-data-flow-debug-mode.md?bc=%2fazure%2fsynapse-analytics%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fsynapse-analytics%2ftoc.json)

    ![A hibakeresés becsúsztatva](media/quickstart-data-flow/debug-on.png)

1. Az adatfolyam vásznán adjon hozzá egy forrást a Forrás **hozzáadása mezőre** kattintva.

1. A forrásnak a **MoviesDB nevet kell nevet ad.** Új **forrásadatkészlet** létrehozásához kattintson az Új elemre.

    ![Új forrásadatkészlet létrehozása](media/quickstart-data-flow/new-source-dataset.png)

1. Válassza a **Azure Data Lake Storage Gen2** lehetőséget. Kattintson a Folytatás gombra.

    ![Válassza a Azure Data Lake Storage Gen2](media/quickstart-data-flow/select-source-dataset.png)

1. Válassza **a Tagolt Szöveg lehetőséget.** Kattintson a Folytatás gombra.

1. Nevezze el az adatkészletet **MoviesDB néven.** A csatolt szolgáltatás legördülő menüben válassza az Új **lehetőséget.**

1. A csatolt szolgáltatás létrehozási képernyőjén nevezze el a ADLS Gen2 **ADLSGen2** nevet a csatolt szolgáltatásnak, és adja meg a hitelesítési módszert. Ezután adja meg a kapcsolat hitelesítő adatait. Ebben a rövid útmutatóban a fiókkulcsot használjuk a tárfiókhoz való csatlakozáshoz. A Kapcsolat tesztelése **gombra kattintva** ellenőrizheti, hogy helyesen adta-e meg a hitelesítő adatait. Ha **elkészült,** kattintson a Létrehozás gombra.

    ![Forrásként hivatkozott szolgáltatás létrehozása](media/quickstart-data-flow/adls-gen2-linked-service.png)

1. Miután visszatért az adatkészlet létrehozására vonatkozó képernyőre, a **Fájl** elérési útja mező alatt adja meg, hol található a fájl. Ebben a rövid útmutatóban a "MoviesDB.csv" fájl a "sample-data" tárolóban található. Mivel a fájl fejlécekkel rendelkezik, jelölje be az **Első sor fejlécként jelölőnégyzetet.** Válassza **a Kapcsolat/tárolóból** lehetőséget a fejlécséma importáláshoz közvetlenül a tárolóban található fájlból. Ha **végzett, kattintson** az OK gombra.

    ![Forrásadatkészlet beállításai](media/quickstart-data-flow/source-dataset-properties.png)

1. Ha a hibakeresési fürt elindult, a forrásátalakítás **Adatelőnézet** lapján kattintson a Frissítés gombra az adatok pillanatképének megtekintéséhez.  Az adatelőnézettel ellenőrizheti, hogy az átalakítás megfelelően van-e konfigurálva.

    ![Adatelőnézet](media/quickstart-data-flow/data-preview.png)

1. Új átalakítás hozzáadásához kattintson a plusz ikonra a forráscsomópont mellett az adatfolyam vásznán. Az első hozzáadott átalakítás egy **szűrő.**

    ![Szűrők hozzáadása](media/quickstart-data-flow/add-filter.png)

1. A szűrőátalakításnak nevezze el **a FilterYears nevet.** A kifejezésszerkesztő megnyitásához kattintson a **Szűrés a következőn melletti** kifejezésmezőre. Itt megadhatja a szűrési feltételt.

1. Az adatfolyam-kifejezésszerkesztővel interaktív módon építhet ki különböző átalakítások során használható kifejezéseket. A kifejezések tartalmazhatnak beépített függvényeket, a bemeneti séma oszlopait és a felhasználó által megadott paramétereket. A kifejezések felépítésével kapcsolatos további információkért lásd: Adatfolyam [kifejezésszerkesztő.](../data-factory/concepts-data-flow-expression-builder.md?bc=%2fazure%2fsynapse-analytics%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fsynapse-analytics%2ftoc.json)

    Ebben a rövid útmutatóban az 1910 és 2000 közötti műfaji műfajok filmjeit szeretné szűrni. Mivel az év jelenleg egy sztring, egész számra kell konvertálni a függvény ```toInteger()``` használatával. A nagyobb vagy egyenlő (>=) és kisebb vagy egyenlő (<=) operátorok használatával hasonlíthatja össze az 1910-es és 200-as konstansértékeket. Ezeket a kifejezéseket a és a (&&) operátorral összesiti. A kifejezés a következőként jön ki:

    ```toInteger(year) >= 1910 && toInteger(year) <= 2000```

    Annak kiderítésében, hogy mely filmek a vétesek, a függvény használatával megkeresheti a ```rlike()``` "Which" mintát az oszlop műfajai között. Az rlike kifejezés és az év összehasonlításának uniója a következőképpen:

    ```toInteger(year) >= 1910 && toInteger(year) <= 2000 && rlike(genres, 'Comedy')```

    ![Szűrési feltétel megadása](media/quickstart-data-flow/visual-expression-builder.png)

    Ha aktív a hibakeresési fürt, a Frissítés  gombra kattintva ellenőrizheti a logikát, hogy a használt bemenetekkel összehasonlítva lássa a kifejezés kimenetét. Több helyes válasz is van arra, hogyan valósítható meg ez a logika az adatfolyam-kifejezés nyelv használatával.

    Ha **végzett** a kifejezéssel, kattintson a Mentés és befejezés gombra.

1. A szűrő **megfelelő működését a Data Preview** lekérése után ellenőrizheti.

1. A következő hozzáadni fog egy Összesítés átalakítást **a** **Sémamódosító alatt.**

    ![Összesítés hozzáadása](media/quickstart-data-flow/add-aggregate.png)

1. Nevezze el az összesített átalakítást **AggregateComedyRatings néven.** A **Csoportosítás lapon** a legördülő **menüből** válassza az év lehetőséget az összesítések csoportosításhoz a film évszáma szerint.

    ![Beállítások összesítése 1](media/quickstart-data-flow/aggregate-settings.png)

1. Ugrás az **Összesítések lapra.** A bal oldali szövegmezőben adja az **AverageComedyRating** nevet az összesített oszlopnak. A jobb kifejezésmezőre kattintva adja meg az összesítő kifejezést a kifejezésszerkesztőben.

    ![Összesített beállítások 2](media/quickstart-data-flow/aggregate-settings-2.png)

1. A Minősítés oszlop átlagának **lekért értékhez** használja az ```avg()``` aggregátum függvényt. Mivel **az Értékelés** egy sztring, és numerikus bemenetet vesz fel, az értéket számra kell konvertálnunk a ```avg()``` ```toInteger()``` függvényen keresztül. Ez a kifejezés így néz ki:

    ```avg(toInteger(Rating))```

    Ha **végzett, kattintson** a Mentés és befejezés gombra.

    ![Átlagos minősítés](media/quickstart-data-flow/average-comedy-rating.png)

1. Az átalakítási **kimenet megtekintéséhez** kattintson az Adatelőnézet lapra. Figyelje meg, hogy csak két oszlop van: **year** és **AverageComedyRating.**

    ![Adatok összesítése – előzetes verzió](media/quickstart-data-flow/transformation-output.png)

1. Ezután hozzá kell adni egy **fogadó-átalakítást** a Cél **alatt.**

    ![Fogadó hozzáadása](media/quickstart-data-flow/add-sink.png)

1. A fogadónak nevezze el a **fogadót.** Kattintson **az Új** gombra a fogadó adatkészlet létrehozásához.

1. Válassza a **Azure Data Lake Storage Gen2** lehetőséget. Kattintson a Folytatás gombra.

1. Válassza **a Tagolt Szöveg lehetőséget.** Kattintson a Folytatás gombra.

1. Nevezze el a fogadó adatkészletet **MoviesSink néven.** A csatolt szolgáltatáshoz válassza ki ADLS Gen2 7. lépésben létrehozott összekapcsolt szolgáltatást. Adjon meg egy kimeneti mappát az adatok írásához. Ebben a rövid útmutatóban az output mappába írunk a sample-data tárolóban. A mappának nem kell előre léteznie, és dinamikusan is létre lehet őket létrehozni. Állítsa **az Első sor fejlécét** true (igaz) értékre, és válassza a None **(Nincs) lehetőséget** a Séma **importálása beállításhoz.** Ha **végzett, kattintson** az OK gombra.

    ![Fogadó-adatkészlet tulajdonságai](media/quickstart-data-flow/sink-dataset-properties.png)

Az adatfolyam elkészült. Készen áll arra, hogy futtassa a folyamatban.

## <a name="running-and-monitoring-the-data-flow"></a>Az alkalmazás futtatása és Adatfolyam

Közzététel előtt hibakeresést is futtathat a folyamatokon. Ebben a lépésben elindítja az adatfolyam-folyamat hibakeresési futtatását. Bár az adatelőnézet nem ír adatokat, a hibakeresési futtatás adatokat ír a fogadó célhelyre.

1. Ugrás a folyamatvászonra. Kattintson **a Hibakeresés** gombra a hibakeresési futtatás aktiválásához.

    ![Hibakeresési folyamat](media/quickstart-data-flow/debug-pipeline.png)

1. A tevékenységek folyamat Adatfolyam hibakeresése az aktív hibakeresési fürtöt használja, de az inicializálása még legalább egy percet igénybe veszi. A folyamat előrehaladását a Kimenet lapon **követheti** nyomon. Ha a futtatás sikeres volt, kattintson a szemüveg ikonra a figyelési panel megnyitásához.

    ![Kimenet hibakeresése](media/quickstart-data-flow/debugging-output.png)

1. A figyelési panelen láthatja az egyes átalakítási lépés során töltött sorok számát és idejét.

    ![Átalakítási monitorozás](media/quickstart-data-flow/4-transformations.png)

1. Az átalakításra kattintva részletes információkat kap az oszlopokról és az adatok particionálásáról.

    ![Az átalakítás részletei](media/quickstart-data-flow/transformation-details.png)

Ha helyesen követte ezt a rövid útmutatót, 83 sort és 2 oszlopot kellett írnia a fogadómappába. Az adatokat a blobtároló ellenőrzésével ellenőrizheti.


## <a name="next-steps"></a>Következő lépések

A következő cikkekben további információt talál a Azure Synapse Analytics támogatásról:

> [!div class="nextstepaction"]
> [Folyamat és tevékenységek](../data-factory/concepts-pipelines-activities.md?bc=%2fazure%2fsynapse-analytics%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fsynapse-analytics%2ftoc.json) 
>  [Adatfolyam-leképezés áttekintése](../data-factory/concepts-data-flow-overview.md?bc=%2fazure%2fsynapse-analytics%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fsynapse-analytics%2ftoc.json) 
>  [Adatfolyam-kifejezés nyelve](../data-factory/data-flow-expression-functions.md?bc=%2fazure%2fsynapse-analytics%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fsynapse-analytics%2ftoc.json)