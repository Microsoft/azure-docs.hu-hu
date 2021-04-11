---
title: Ajánlott eljárások a fájloknak az adatfolyamatokkal való írásához
description: Ez az oktatóanyag ajánlott eljárásokat biztosít a fájlok és az adatfolyamok közötti adatforgalomhoz való íráshoz
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2021
ms.date: 04/01/2021
ms.openlocfilehash: 8010f3f95c9358714b659df5821a375bd8488ad8
ms.sourcegitcommit: d63f15674f74d908f4017176f8eddf0283f3fac8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/07/2021
ms.locfileid: "106582246"
---
# <a name="best-practices-for-writing-to-files-to-data-lake-with-data-flows"></a>Ajánlott eljárások a fájloknak az adatfolyamatokkal való írásához

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Ha még csak ismerkedik az Azure Data Factory használatával, olvassa el [az Azure Data Factory használatának első lépéseit](introduction.md) ismertető cikket.

Ebből az oktatóanyagból megismerheti azokat az ajánlott eljárásokat, amelyek alkalmazhatók a fájlok ADLS Gen2 vagy az Azure Blob Storage adatfolyamatok használatával történő írásakor. Szüksége lesz egy Azure Blob Storage-fiókhoz vagy egy Azure Data Lake Store Gen2-fiókhoz, amely a Parquet-fájlok olvasására, majd az eredmények mappákba való tárolására szolgál.

## <a name="prerequisites"></a>Előfeltételek
* **Azure-előfizetés**. Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes Azure-fiókot](https://azure.microsoft.com/free/) a virtuális gép létrehozásának megkezdése előtt.
* **Azure Storage-fiók**. A ADLS-tárolók *forrásaként* és fogadó *adattárakként* használhatók. Ha még nem rendelkezik tárfiókkal, tekintse meg az [Azure Storage-fiók létrehozásának](../storage/common/storage-account-create.md) lépéseit ismertető cikket.

Az oktatóanyag lépései azt feltételezik, hogy 

## <a name="create-a-data-factory"></a>Adat-előállító létrehozása

Ebben a lépésben létrehoz egy adatelőállítót, és megnyitja a Data Factory UX-t egy folyamat létrehozásához az adatelőállítóban.

1. Nyissa meg a **Microsoft Edge** vagy a **Google Chrome böngészőt**. Jelenleg Data Factory felhasználói felület csak a Microsoft Edge és a Google Chrome böngészőben támogatott.
1. A bal oldali menüben válassza az **erőforrás**-  >  **integráció** létrehozása  >  **Data Factory**
1. Az **új adat-előállító** oldalon, a **név** mezőben adja meg a **ADFTutorialDataFactory**
1. Válassza ki azt az **Azure-előfizetést**, amelyben az adat-előállítót létre szeretné hozni.
1. **Erőforráscsoport:** hajtsa végre a következő lépések egyikét:

    a. Válassza a **meglévő használata** lehetőséget, majd válasszon ki egy meglévő erőforráscsoportot a legördülő listából.
    
    b. Válassza az **új létrehozása** lehetőséget, és adja meg az erőforráscsoport nevét. Az erőforráscsoportok megismeréséhez lásd: [erőforráscsoportok használata az Azure-erőforrások kezeléséhez](../azure-resource-manager/management/overview.md).
    
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

## <a name="build-transformation-logic-in-the-data-flow-canvas"></a>Átalakítási logika létrehozása az adatfolyam-vásznon

Bármilyen forrásadatokat (ebben az oktatóanyagban egy Parquet) használunk, és egy fogadó átalakítással kell letenni az adattárolást parketta formátumban, a leghatékonyabban a adattó ETL-re vonatkozó mechanizmusait használva.

![Végső folyamat](media/data-flow/parts-final.png "Végső folyamat")

### <a name="tutorial-objectives"></a>Oktatóanyag céljai

1. Válassza ki bármelyik forrás-adatkészletet egy új adatfolyamban
1. Adatfolyamatok használata a fogadó adatkészlet hatékony particionálásához
1. Particionált adatai ADLS Gen2 Lake-mappákban

### <a name="start-from-a-blank-data-flow-canvas"></a>Kezdés üres adatfolyam-vászonból

Először állítsa be az adatáramlási környezetet az alábbiakban ismertetett mechanizmusok mindegyikéhez ADLS Gen2

1. Kattintson a forrás-átalakításra.
1. Az alsó panelen kattintson az adatkészlet melletti új gombra.
1. Válasszon ki egy adatkészletet, vagy hozzon létre újat. Ebben a bemutatóban egy felhasználói adat nevű parketta-adathalmazt fogunk használni.
1. Származtatott oszlop átalakításának hozzáadása. Ezt úgy fogjuk használni, hogy dinamikusan beállítjuk a kívánt mappák nevét.
1. Egy fogadó átalakítás hozzáadása.
   
### <a name="hierarchical-folder-output"></a>Hierarchikus mappa kimenete

Nagyon gyakori, hogy egyedi értékeket használjon az adatokban, hogy mappa-hierarchiákat hozzon létre az adatok a tóban való particionálásához. Ez egy nagyon optimális módszer a tóban és a Sparkban lévő adatok rendszerezésére és feldolgozására (az adatforgalom mögötti számítási motor). A kimenet ily módon történő rendszerezése azonban kisebb teljesítménnyel jár. Várhatóan kisebb csökkenést tapasztal a folyamat teljes teljesítményében a fogadóban ezzel a mechanizmussal.

1. Térjen vissza az adatfolyam-tervezőhöz, és szerkessze a fent létrehozott adatfolyamot. Kattintson a fogadó átalakításra.
1. Kattintson az optimalizálás > a particionálás > kulcs beállítása elemre.
1. Válassza ki azokat az oszlopokat, amelyeket a hierarchikus mappa struktúrájának beállításához használni kíván.
1. Figyelje meg, hogy az alábbi példa az év és a hónap nevet használja a mappák elnevezési oszlopaihoz. Az eredmények az űrlap mappái lesznek ```releaseyear=1990/month=8``` .
1. Amikor egy adatfolyam-forráshoz fér hozzá az adatpartícióhoz, a fenti legfelső szintű mappára kell mutatnia, ```releaseyear``` és minden további mappához helyettesítő karaktert kell használnia, például: ```**/**/*.parquet```
1. Az adatértékek módosításához, vagy ha a mappanevek szintetikus értékeket kell létrehoznia, használja a származtatott oszlop transzformációját a mappák neveiben használni kívánt értékek létrehozásához.

![Kulcs particionálás](media/data-flow/key-parts.png "Kulcs particionálás")
   
### <a name="name-folder-as-data-values"></a>Név mappa adatértékként

Egy valamivel jobb teljesítményű fogadó módszer a Lake-adatokhoz olyan ADLS Gen2 használatával, amely nem nyújt előnyt a kulcs/érték particionáláshoz ```Name folder as column data``` . Míg a hierarchikus struktúra fő particionálási stílusa lehetővé teszi az adatszeletek feldolgozását, ez a módszer egy összevont mappastruktúrát, amely gyorsabban írhat adatmennyiséget.

1. Térjen vissza az adatfolyam-tervezőhöz, és szerkessze a fent létrehozott adatfolyamot. Kattintson a fogadó átalakításra.
1. Kattintson az optimalizálás > particionálás beállítása > az aktuális particionálás használata lehetőségre.
1. Kattintson a beállítások > név mappa oszlop adatként elemre.
1. Válassza ki a mappák nevének generálásához használni kívánt oszlopot.
1. Az adatértékek módosításához, vagy ha a mappanevek szintetikus értékeket kell létrehoznia, használja a származtatott oszlop transzformációját a mappák neveiben használni kívánt értékek létrehozásához.

![Mappa beállítás](media/data-flow/folders.png "Mappák")

### <a name="name-file-as-data-values"></a>Fájlnév adatértékként

A fenti oktatóanyagokban felsorolt módszerek jó használati esetek a mappa-kategóriák létrehozásához a adat-tóban. A módszerek által alkalmazott alapértelmezett elnevezési séma a Spark végrehajtói feladatok AZONOSÍTÓját használja. Esetenként előfordulhat, hogy a kimeneti fájl nevét egy adatfolyam-szöveges fogadóban szeretné beállítani. Ez a módszer csak kis fájlokkal való használatra javasolt. A partíciós fájlok egyetlen kimeneti fájlba való egyesítésének folyamata hosszú ideig futó folyamat.

1. Térjen vissza az adatfolyam-tervezőhöz, és szerkessze a fent létrehozott adatfolyamot. Kattintson a fogadó átalakításra.
1. Kattintson az optimalizálás > a particionálás > egyetlen partíció beállítása elemre. Ez az egyetlen partíciós követelmény, amely szűk keresztmetszetet hoz létre a végrehajtási folyamatban a fájlok egyesítése során. Ez a beállítás csak kisméretű fájlok esetében ajánlott.
1. Kattintson a beállítások > a fájlnév oszlop adatként elemre.
1. Válassza ki a fájlnevek generálásához használni kívánt oszlopot.
1. Az adatértékek módosításához, vagy ha a fájlnevekhez szintetikus értékeket kell létrehoznia, használja a származtatott oszlop transzformációját a fájlnevekben használni kívánt értékek létrehozásához.

## <a name="next-steps"></a>Következő lépések

További információ az [adatfolyamok elsüllyedéről](data-flow-sink.md).
