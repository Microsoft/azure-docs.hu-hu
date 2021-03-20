---
title: 'Rövid útmutató: az adatátalakítás leképezési adatfolyam használatával'
description: Ez az oktatóanyag részletes útmutatást nyújt az Azure szinapszis Analytics használatával történő adatátalakításhoz az adatátviteli folyamattal.
author: djpmsft
ms.author: daperlov
ms.reviewer: makromer
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 11/03/2020
ms.openlocfilehash: cb0fd5464f7025d71dd384c56233aefa6a6cd364
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98218722"
---
# <a name="quickstart-transform-data-using-mapping-data-flows"></a>Gyors útmutató: az adatátalakítás a leképezési folyamatokkal

Ebben a rövid útmutatóban az Azure szinapszis Analytics használatával olyan folyamatot hoz létre, amely egy Azure Data Lake Storage Gen2 (ADLS Gen2) forrásból származó adatok egy ADLS Gen2 fogadóba való átalakítását használja a leképezési adatfolyam használatával. Az ebben a rövid útmutatóban szereplő konfigurációs minta kibontható, ha az adatátalakítást a leképezési adatfolyam használatával végezi el

Ebben a rövid útmutatóban a következő lépéseket hajtja végre:

> [!div class="checklist"]
> * Folyamat létrehozása adatfolyam-tevékenységgel az Azure szinapszis Analyticsben.
> * Hozzon létre egy leképezési adatfolyamot négy átalakítással.
> * A folyamat próbafuttatása
> * Adatfolyam-tevékenység figyelése

## <a name="prerequisites"></a>Előfeltételek

* **Azure-előfizetés**: Ha nem rendelkezik Azure-előfizetéssel, első lépésként mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/).
* **Azure szinapszis-munkaterület**: hozzon létre egy szinapszis-munkaterületet a Azure Portal használatával a gyors útmutató [: szinapszis-munkaterület létrehozása](quickstart-create-workspace.md)című témakör utasításait követve.
* **Azure Storage-fiók**: ADLS-tárolót használ *forrásként* és fogadóként tárolt *adattárakként* . Ha még nem rendelkezik tárfiókkal, tekintse meg az [Azure Storage-fiók létrehozásának](../storage/common/storage-account-create.md) lépéseit ismertető cikket.

    Az oktatóanyagban átalakított fájl MoviesDB.csv, amely [itt](https://raw.githubusercontent.com/djpmsft/adf-ready-demo/master/moviesDB.csv)található. A fájl GitHubról történő lekéréséhez másolja a tartalmat egy tetszőleges szövegszerkesztőbe, és mentse helyileg a. csv-fájlként. A fájlnak a Storage-fiókba való feltöltéséhez lásd: [Blobok feltöltése a Azure Portal](../storage/blobs/storage-quickstart-blobs-portal.md). Ez a példa egy "Sample-adat" nevű tárolóra hivatkozik.

### <a name="navigate-to-the-synapse-studio"></a>Navigáljon a szinapszis studióhoz

Az Azure szinapszis-munkaterület létrehozása után kétféleképpen nyithatja meg a szinapszis Studio alkalmazást:

* Nyissa meg a szinapszis munkaterületet a [Azure Portal](https://ms.portal.azure.com/#home). Válassza a **Megnyitás** lehetőséget a nyílt szinapszis Studio kártyán a kezdeti lépések szakaszban.
* Nyissa meg az [Azure szinapszis Analytics szolgáltatást](https://web.azuresynapse.net/) , és jelentkezzen be a munkaterületére.

Ebben a rövid útmutatóban példaként használjuk a "adftest2020" nevű munkaterületet. A rendszer automatikusan átirányítja a szinapszis Studio kezdőlapjára.

![Szinapszis Studio kezdőlapja](media/doc-common-process/synapse-studio-home.png)

## <a name="create-a-pipeline-with-a-data-flow-activity"></a>Folyamat létrehozása adatfolyam-tevékenységgel

A folyamatok egy adott tevékenységek végrehajtásának logikai folyamatát tartalmazzák. Ebben a szakaszban egy adatfolyamati tevékenységet tartalmazó folyamatot fog létrehozni.

1. Nyissa meg az **integrálás** lapot. Válassza a folyamatok fejléc melletti plusz ikont, és válassza a folyamat lehetőséget.

   ![Új folyamat létrehozása](media/doc-common-process/new-pipeline.png)

1. A folyamat **Tulajdonságok** beállításai lapján adja meg a **TransformMovies** **nevet**.

1. Az *áthelyezés és átalakítás* területen a *tevékenységek* ablaktáblán húzza az **adatfolyamot** a folyamat vászonra.

1. Az **adatfolyamatok hozzáadása** lap előugró ablakában válassza az **új adatfolyam**-adatfolyam létrehozása lehetőséget  ->  . Ha elkészült, kattintson **az OK gombra** .

   ![Adatfolyam létrehozása](media/quickstart-data-flow/new-data-flow.png)

1. Nevezze el az adatfolyam **TransformMovies** a **Tulajdonságok** lapon.

## <a name="build-transformation-logic-in-the-data-flow-canvas"></a>Átalakítási logika létrehozása az adatfolyam-vásznon

Miután létrehozta az adatfolyamatot, a rendszer automatikusan elküldi az adatfolyam-vásznon. Ebben a lépésben egy olyan adatfolyamatot fog létrehozni, amely a ADLS-tárolóban lévő MoviesDB.csv viszi, és összesíti a vígjátékok 1910 és 2000 közötti átlagos minősítését. Ezt a fájlt ezután vissza kell írnia a ADLS-tárolóba.

1. Az adatáramlási vászon felett csúsztassa az **adatfolyam hibakeresési** csúszkáját. A hibakeresési mód lehetővé teszi az átalakítási logika interaktív tesztelését egy élő Spark-fürtön. Az adatfolyam-fürtök 5-7 percet vesznek igénybe, és a felhasználóknak javasoljuk, hogy először bekapcsolják a hibakeresést, ha az adatforgalom fejlesztését tervezik. További információ: [hibakeresési mód](../data-factory/concepts-data-flow-debug-mode.md?bc=%2fazure%2fsynapse-analytics%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fsynapse-analytics%2ftoc.json).

    ![A hibakeresés bevetítése](media/quickstart-data-flow/debug-on.png)

1. Az adatfolyam-vásznon adja hozzá a forrást a **forrás hozzáadása** mezőre kattintva.

1. Nevezze el a forrás **MoviesDB**. Új forrás adatkészlet létrehozásához kattintson az **új** elemre.

    ![Új forrás adatkészlet létrehozása](media/quickstart-data-flow/new-source-dataset.png)

1. Válassza a **Azure Data Lake Storage Gen2** lehetőséget. Kattintson a Folytatás gombra.

    ![Azure Data Lake Storage Gen2 kiválasztása](media/quickstart-data-flow/select-source-dataset.png)

1. Válassza a **DelimitedText** lehetőséget. Kattintson a Folytatás gombra.

1. Nevezze el az adatkészlet **MoviesDB**. A társított szolgáltatás legördülő menüben válassza az **új** lehetőséget.

1. A társított szolgáltatás létrehozása képernyőn nevezze el a ADLS Gen2 társított szolgáltatás **ADLSGen2** , és adja meg a hitelesítési módszert. Ezután adja meg a kapcsolatok hitelesítő adatait. Ebben a rövid útmutatóban a fiók kulcsát használjuk a Storage-fiókhoz való kapcsolódáshoz. Kattintson a **Kapcsolódás tesztelése** lehetőségre a hitelesítő adatok helyes beírásának ellenőrzéséhez. Ha elkészült, kattintson a **Létrehozás** gombra.

    ![Forráshoz társított szolgáltatás létrehozása](media/quickstart-data-flow/adls-gen2-linked-service.png)

1. Miután visszatért az adatkészlet-létrehozási képernyőre, a **fájl elérési útja** mezőben adja meg, hogy hol található a fájl. Ebben a rövid útmutatóban a "MoviesDB.csv" fájl a "Sample-file" tárolóban található. Ahogy a fájl fejléceket tartalmaz, tekintse meg az **első sort fejlécként**. Válassza a **Kapcsolódás/áruház** lehetőséget, hogy a fejléc-sémát közvetlenül a tárolóban lévő fájlból importálja. Ha elkészült, kattintson **az OK gombra** .

    ![Forrásadatkészlet beállításai](media/quickstart-data-flow/source-dataset-properties.png)

1. Ha a hibakeresési fürt elindult, lépjen a forrás-átalakítás **adatelőnézet** lapjára, és kattintson a **frissítés** gombra az adatok pillanatképének beolvasásához. Az adatelőnézet használatával ellenőrizheti, hogy az átalakítás megfelelően van-e konfigurálva.

    ![Adatelőnézet](media/quickstart-data-flow/data-preview.png)

1. Az adatfolyam-vászonon a forrás csomópont mellett kattintson a plusz ikonra egy új átalakítás hozzáadásához. A hozzáadott első átalakítás egy **szűrő**.

    ![Szűrők hozzáadása](media/quickstart-data-flow/add-filter.png)

1. Nevezze el a szűrő átalakítási **FilterYears**. Kattintson a **szűrés** elem melletti kifejezés mezőre a Kifejezésszerkesztő megnyitásához. Itt adja meg a szűrési feltételt.

1. Az adatfolyam-kifejezés-szerkesztővel interaktív módon hozhat létre kifejezéseket különböző átalakításokban való használatra. A kifejezések tartalmazhatnak beépített függvényeket, a bemeneti sémából származó oszlopokat és a felhasználó által definiált paramétereket. A kifejezések létrehozásával kapcsolatos további információkért lásd: [adatáramlási kifejezés-szerkesztő](../data-factory/concepts-data-flow-expression-builder.md?bc=%2fazure%2fsynapse-analytics%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fsynapse-analytics%2ftoc.json).

    Ebben a rövid útmutatóban a műfaji komédia olyan filmjeit szeretné szűrni, amelyek a 1910-es és a 2000-os évek közötti időszakban jöttek létre. Az év jelenleg karakterlánc, a függvény használatával át kell alakítani egész számra ```toInteger()``` . Használja a nagyobb vagy egyenlő értéket (>=), és kisebb vagy egyenlő, mint a (<=) operátorok az 1910 és a 200 – literális Year értékekkel való összehasonlításhoz. Egyesítse ezeket a kifejezéseket a és a (&&) operátorral együtt. A kifejezés a következőképpen érkezik:

    ```toInteger(year) >= 1910 && toInteger(year) <= 2000```

    Ha szeretné megkeresni, hogy mely filmek a vígjátékok, a ```rlike()``` függvény használatával megtalálhatja a "komédia" mintát az oszlopok műfajában. Union The rlike kifejezés az év összehasonlításával a Get:

    ```toInteger(year) >= 1910 && toInteger(year) <= 2000 && rlike(genres, 'Comedy')```

    ![Szűrési feltétel meghatározása](media/quickstart-data-flow/visual-expression-builder.png)

    Ha a hibakeresési fürt aktív, a **frissítés** gombra kattintva ellenőrizheti, hogy a kifejezés kimenete a használt bemenetekhez képest látható-e. A logikát az adatáramlás kifejezésének nyelve alapján több, mint egy megfelelő választ kaphat.

    Kattintson a **Mentés és Befejezés** gombra, ha elkészült a kifejezéssel.

1. Egy **Adatelőnézet** beolvasása annak ellenőrzéséhez, hogy a szűrő megfelelően működik-e.

1. A hozzáadni kívánt következő átalakítás a **séma-módosító** alatt létrehozott **összesített** transzformáció.

    ![Összesítés hozzáadása](media/quickstart-data-flow/add-aggregate.png)

1. Nevezze el az összesített átalakítási **AggregateComedyRatings**. A **Csoportosítás** lapon válassza az **év** lehetőséget a legördülő listából, hogy csoportosítsa az összesítéseket a film által kiváltott év alapján.

    ![Összesített beállítások 1](media/quickstart-data-flow/aggregate-settings.png)

1. Nyissa meg az **összesítések** lapot. A bal oldali szövegmezőben nevezze el az összesítő oszlop **AverageComedyRating**. Kattintson a jobb oldali kifejezés mezőre az összesítő kifejezés megadásához a Expression Builder használatával.

    ![Összesített beállítások 2](media/quickstart-data-flow/aggregate-settings-2.png)

1. Az oszlop- **minősítés** átlagának lekéréséhez használja az ```avg()``` összesítő függvényt. Mivel a **minősítés** egy karakterlánc ```avg()``` , és egy numerikus bemenetet vesz igénybe, az értéket egy számra kell konvertálnia a ```toInteger()``` függvényen keresztül. A következő kifejezés így néz ki:

    ```avg(toInteger(Rating))```

    Ha elkészült **, kattintson a Mentés és Befejezés** gombra.

    ![Vígjátékok átlagos minősítése](media/quickstart-data-flow/average-comedy-rating.png)

1. Az átalakítás kimenetének megtekintéséhez nyissa meg az **adatelőnézet** lapot. Figyelje meg, hogy csak két oszlop van, az **év** és a **AverageComedyRating**.

    ![Összesített adatelőnézet](media/quickstart-data-flow/transformation-output.png)

1. Ezután hozzá kíván **adni egy** fogadó átalakítást a **célhely** területen.

    ![Fogadó hozzáadása](media/quickstart-data-flow/add-sink.png)

1. Nevezze el a fogadó fogadót. A fogadó adatkészlet létrehozásához kattintson az **új** elemre.

1. Válassza a **Azure Data Lake Storage Gen2** lehetőséget. Kattintson a Folytatás gombra.

1. Válassza a **DelimitedText** lehetőséget. Kattintson a Folytatás gombra.

1. Nevezze el a fogadó adatkészletet **MoviesSink**. Társított szolgáltatás esetén válassza ki a 7. lépésben létrehozott ADLS Gen2 társított szolgáltatást. Adja meg azt a kimeneti mappát, ahová az adatokat írni kívánja. Ebben a rövid útmutatóban a "output" mappát írunk a "Sample-recontainer" tárolóba. A mappának nem kell előre megadnia, és dinamikusan létre lehet hozni. Állítsa az **első sort fejlécként** True (igaz) értékre, és válassza a **nincs lehetőséget** a **séma importálása** lehetőségnél. Ha elkészült, kattintson **az OK gombra** .

    ![Fogadó adatkészlet tulajdonságai](media/quickstart-data-flow/sink-dataset-properties.png)

Most, hogy befejezte az adatfolyam felépítése. Készen áll arra, hogy a folyamaton fusson.

## <a name="running-and-monitoring-the-data-flow"></a>Az adatfolyam futtatása és figyelése

A folyamat a közzététel előtt hibakeresést végezhet. Ebben a lépésben az adatfolyam-folyamat hibakeresési futtatását fogja elindítani. Míg az adatelőnézet nem ír adatírást, a hibakeresési kísérlet az adatait a fogadó célhelyére fogja írni.

1. Nyissa meg a folyamat vászonját. Hibakeresési Futtatás indításához kattintson a **hibakeresés** gombra.

    ![Hibakeresési folyamat](media/quickstart-data-flow/debug-pipeline.png)

1. Az adatfolyam-tevékenységek feldolgozási folyamata az aktív hibakeresési fürtöt használja, de még legalább egy percet is igénybe kell vennie az inicializáláshoz. Az előrehaladást a **kimenet** lapon követheti nyomon. Ha a Futtatás sikeres, kattintson a szemüvegek ikonra a figyelés ablaktábla megnyitásához.

    ![Kimenet hibakeresése](media/quickstart-data-flow/debugging-output.png)

1. A figyelés ablaktáblán láthatja az egyes átalakítási lépésekben eltöltött sorok és idő számát.

    ![Átalakítás figyelése](media/quickstart-data-flow/4-transformations.png)

1. A transzformációra kattintva részletes információkat kaphat az oszlopokról és az adatok particionálásáról.

    ![Átalakítás részletei](media/quickstart-data-flow/transformation-details.png)

Ha ezt követően helyesen követte ezt a rövid útmutatót, a fogadó mappájába írás 83 sort és 2 oszlopot kell tartalmaznia. Az adatait a blob Storage ellenőrzésével ellenőrizheti.


## <a name="next-steps"></a>Következő lépések

Folytassa a következő cikkekkel az Azure szinapszis Analytics támogatásának megismeréséhez:

> [!div class="nextstepaction"]
> [Folyamat és tevékenységek](../data-factory/concepts-pipelines-activities.md?bc=%2fazure%2fsynapse-analytics%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fsynapse-analytics%2ftoc.json) 
>  A [leképezési adatfolyam áttekintése](../data-factory/concepts-data-flow-overview.md?bc=%2fazure%2fsynapse-analytics%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fsynapse-analytics%2ftoc.json) 
>  [Adatfolyam kifejezésének nyelve](../data-factory/data-flow-expression-functions.md?bc=%2fazure%2fsynapse-analytics%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fsynapse-analytics%2ftoc.json)