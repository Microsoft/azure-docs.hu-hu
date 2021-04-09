---
title: Delta Lake ETL adatfolyamatokkal
description: Ez az oktatóanyag részletes útmutatást nyújt az adatfolyamatok használatához a Delta Lake-ben tárolt adatforgalom átalakításához és elemzéséhez
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2021
ms.date: 02/09/2021
ms.openlocfilehash: cb8d44353e826df14ed3baab2c4ca66ffed4a569
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "100417665"
---
# <a name="transform-data-in-delta-lake-using-mapping-data-flows"></a>Adatátalakítás a Delta Lake-ben a leképezési adatforgalom használatával

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Ha még csak ismerkedik az Azure Data Factory használatával, olvassa el [az Azure Data Factory használatának első lépéseit](introduction.md) ismertető cikket.

Ebben az oktatóanyagban az adatáramlási vászon használatával olyan adatfolyamatokat hozhat létre, amelyek lehetővé teszik a Azure Data Lake Storage (ADLS) Gen2 lévő adatelemzést és átalakítást, valamint a Delta Lake-ben való tárolását.

## <a name="prerequisites"></a>Előfeltételek
* **Azure-előfizetés**. Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes Azure-fiókot](https://azure.microsoft.com/free/) a virtuális gép létrehozásának megkezdése előtt.
* **Azure Storage-fiók**. A ADLS-tárolók *forrásaként* és fogadó *adattárakként* használhatók. Ha még nem rendelkezik tárfiókkal, tekintse meg az [Azure Storage-fiók létrehozásának](../storage/common/storage-account-create.md) lépéseit ismertető cikket.

Az oktatóanyagban átalakított fájl MoviesDB.csv, amely [itt](https://github.com/kromerm/adfdataflowdocs/blob/master/sampledata/moviesDB2.csv)található. A fájl GitHubról történő lekéréséhez másolja a tartalmat egy tetszőleges szövegszerkesztőbe, és mentse helyileg a. csv-fájlként. A fájlnak a Storage-fiókba való feltöltéséhez lásd: [Blobok feltöltése a Azure Portal](../storage/blobs/storage-quickstart-blobs-portal.md). Ez a példa egy "Sample-adat" nevű tárolóra hivatkozik.

## <a name="create-a-data-factory"></a>Adat-előállító létrehozása

Ebben a lépésben létrehoz egy adatelőállítót, és megnyitja a Data Factory UX-t egy folyamat létrehozásához az adatelőállítóban.

1. Nyissa meg a **Microsoft Edge** vagy a **Google Chrome böngészőt**. Jelenleg Data Factory felhasználói felület csak a Microsoft Edge és a Google Chrome böngészőben támogatott.
1. A bal oldali menüben válassza az **erőforrás**-  >  **integráció** létrehozása  >  **Data Factory**
1. Az **új adat-előállító** oldalon, a **név** mezőben adja meg a **ADFTutorialDataFactory**
1. Válassza ki azt az **Azure-előfizetést**, amelyben az adat-előállítót létre szeretné hozni.
1. **Erőforráscsoport:** hajtsa végre a következő lépések egyikét:

    a. Válassza a **meglévő használata** lehetőséget, majd válasszon ki egy meglévő erőforráscsoportot a legördülő listából.

    b. Válassza az **új létrehozása** lehetőséget, és adja meg az erőforráscsoport nevét. 
         
    Az erőforráscsoportokkal kapcsolatos információkért tekintse meg az [Erőforráscsoportok használata az Azure-erőforrások kezeléséhez](../azure-resource-manager/management/overview.md) ismertető cikket. 
1. A **Verzió** résznél válassza a **V2** értéket.
1. A **Hely** területen válassza ki az adat-előállító helyét. A legördülő listán csak a támogatott helyek jelennek meg. Az adattárak (például az Azure Storage és a SQL Database) és a számítási erőforrások (például az Azure HDInsight) más régiókban is használhatók.
1. Válassza a **Létrehozás** lehetőséget.
1. A létrehozás befejezése után megjelenik az értesítési központban megjelenő értesítés. Válassza az **Ugrás az erőforráshoz** lehetőséget, hogy megnyissa az adatfeldolgozó lapot.
1. A Data Factory felhasználói felületének külön lapon történő elindításához válassza a **Létrehozás és figyelés** csempét.

## <a name="create-a-pipeline-with-a-data-flow-activity"></a>Folyamat létrehozása adatfolyam-tevékenységgel

Ebben a lépésben olyan folyamatot hoz létre, amely egy adatfolyam-tevékenységet tartalmaz.

1. Az **Első lépések** lapon válassza a **Folyamat létrehozása** lehetőséget.

   ![Folyamat létrehozása](./media/doc-common-process/get-started-page.png)

1. A folyamat **általános** lapján adja meg a **DeltaLake** nevet a folyamat **neveként** .
1. A gyári felső sávban csúsztassa az **adatfolyam hibakeresési** csúszkáját. A hibakeresési mód lehetővé teszi az átalakítási logika interaktív tesztelését egy élő Spark-fürtön. Az adatfolyam-fürtök 5-7 percet vesznek igénybe, és a felhasználóknak javasoljuk, hogy először bekapcsolják a hibakeresést, ha az adatforgalom fejlesztését tervezik. További információ: [hibakeresési mód](concepts-data-flow-debug-mode.md).

    ![Adatfolyam-tevékenység](media/tutorial-data-flow/dataflow1.png)
1. A **tevékenységek** ablaktáblán bontsa ki az **áthelyezés és átalakítás** egyezést. Húzza az **adatfolyam** tevékenységet a panelről a folyamat vászonra.

    ![Képernyőkép, amely megjeleníti a folyamat vászonját, ahol elhúzhatja az adatfolyam tevékenységeit.](media/tutorial-data-flow/activity1.png)
1. Az **adatfolyam hozzáadása** felugró ablakban válassza az **új adatfolyam létrehozása** lehetőséget, majd nevezze el az adatfolyam- **DeltaLake**. Ha elkészült, kattintson a Befejezés gombra.

    ![Képernyőfelvétel: az új adatfolyam létrehozásakor az adatfolyamatok nevének megjelenítése.](media/tutorial-data-flow/activity2.png)

## <a name="build-transformation-logic-in-the-data-flow-canvas&quot;></a>Átalakítási logika létrehozása az adatfolyam-vásznon

Ebben az oktatóanyagban két adatfolyamatot fog előállítani. Az ököl-adatfolyam egy egyszerű forrás a fogadó számára, amely egy új Delta-tó létrehozásához szükséges a fenti filmek CSV-fájlból. Végül az alábbi folyamat kialakításával is létrehozhatja az adatváltozásokat a Delta Lake-ben.

![Végső folyamat](media/data-flow/data-flow-tutorial-6.png &quot;Végső folyamat")

### <a name="tutorial-objectives"></a>Oktatóanyag céljai

1. A fenti MoviesCSV-adatkészlet forrásának megváltoztatásához új Delta-tavat kell megalkotni
1. Az 1988-es filmek frissített minősítési logikájának létrehozása az "1" értékre
1. Az összes film törlése a 1950-ből
1. Új filmek beszúrása 2021-re a filmek duplikálása a 1960-ből

### <a name="start-from-a-blank-data-flow-canvas"></a>Kezdés üres adatfolyam-vászonból

1. Kattintson a forrás-átalakításra
1. Kattintson az új adatkészlet elemre az alsó panelen 1. hozzon létre egy új társított szolgáltatást a következőhöz: ADLS Gen2
1. Tagolt szöveg választása az adatkészlet típusához
1. Nevezze el a "MoviesCSV" adatkészletet 
1. Mutasson arra a MoviesCSV-fájlra, amelyet a fenti tárolóba töltött fel
1. Állítsa be a vesszővel tagolt értékre, és illessze be az első sor fejlécét 
1. Lépjen a forrás kivetítés lapra, és kattintson az "adattípusok észlelése" elemre.
1. Ha elvégezte a leképezési készletet, folytassa a 
1. Fogadó átalakítás hozzáadása
1. A Delta egy beágyazott adatkészlet-típus. Ekkor a ADLS Gen2 Storage-fiókra kell mutatnia.
   
   ![Beágyazott adatkészlet](media/data-flow/data-flow-tutorial-5.png "Beágyazott adatkészlet")

1. Válasszon egy mappanevet a Storage-tárolóban, ahol az ADF-hez szeretné létrehozni a Delta-tavat
1. Lépjen vissza a folyamat-tervezőbe, és kattintson a hibakeresés gombra a folyamat hibakeresési módban való végrehajtásához, csak ez az adatfolyam tevékenység a vásznon. Ekkor létrejön az új Delta Lake ADLS Gen2.
1. A gyári erőforrások területen kattintson az új > adatfolyam elemre. 
1. Használja újra a MoviesCSV forrásként, majd kattintson az "adattípusok észlelése" elemre.
1. Szűrő átalakításának hozzáadása a forrás átalakításához a gráfban
1. Csak a három évre vonatkozó, a 1950-es, a 1988-es és a 1960-as értéknek megfelelő mozgókép-sorok engedélyezése
1. Az egyes 1988-filmek minősítésének frissítése az "1" értékre mostantól egy származtatott oszlop átalakításának hozzáadása a szűrő átalakításához
1. Ugyanebben a származtatott oszlopban hozzon létre filmeket 2021-re egy meglévő év elvégzésével, és módosítsa az évet 2021-re. Válasszon 1960.
1. A három származtatott oszlop így fog kinézni

   ![Származtatott oszlop](media/data-flow/data-flow-tutorial-2.png "Származtatott oszlop")
   
1. ```Update, insert, delete, and upsert``` a szabályzatok az Alter Row transzformációban jönnek létre. A származtatott oszlop után adjon hozzá egy Alter sort transzformációt.
1. Az Alter Row-szabályzatoknak így kell kinézniük.

   ![Sor módosítása](media/data-flow/data-flow-tutorial-3.png "Sor módosítása")
   
1. Most, hogy beállította a megfelelő szabályzatot az egyes módosítási sorok típusaihoz, győződjön meg arról, hogy a megfelelő frissítési szabályok be vannak állítva a fogadó átalakításban.

   ![Sink (Fogadó)](media/data-flow/data-flow-tutorial-4.png "Sink (Fogadó)")
   
1. Itt a Delta Lake fogadót használjuk a ADLS Gen2-adattóhoz, és lehetővé tesszük a lapkák, a frissítések és a törlés használatát. 
1. Vegye figyelembe, hogy a kulcs oszlopai a mozgókép elsődleges kulcsának oszlopa és az év oszlopból álló összetett kulcsok. Ennek az az oka, hogy hamis 2021 filmeket hoztunk létre a 1960-sorok másolásával. Ezzel elkerülheti az ütközéseket, amikor megkeresi a meglévő sorokat az egyediség biztosításával.

### <a name="download-completed-sample"></a>Befejezett minta letöltése
[Íme egy példa a különbözeti folyamatra, amely egy adatfolyamattal rendelkezik a-tavon lévő sorok frissítéséhez és törléséhez:](https://github.com/kromerm/adfdataflowdocs/blob/master/sampledata/DeltaPipeline.zip)

## <a name="next-steps"></a>Következő lépések

További információ az [adatfolyam kifejezésének nyelvéről](data-flow-expression-functions.md).
