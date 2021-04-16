---
title: Delta Lake ETL adatfolyamokkal
description: Ez az oktatóanyag részletes útmutatást nyújt a Delta Lake-adatok adatfolyamokkal való átalakításához és elemzéséhez
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2021
ms.date: 04/16/2021
ms.openlocfilehash: 4a88ed2df74d3eebb96c42e2cdc87b14153419cd
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2021
ms.locfileid: "107565372"
---
# <a name="transform-data-in-delta-lake-using-mapping-data-flows"></a>A Delta Lake adatainak átalakítása adatfolyamok leképezése használatával

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Ha még csak ismerkedik az Azure Data Factory használatával, olvassa el [az Azure Data Factory használatának első lépéseit](introduction.md) ismertető cikket.

Ebben az oktatóanyagban az adatfolyamvászon használatával fog adatfolyamokat létrehozni, amelyek lehetővé teszik az adatok elemzését és átalakítását a Azure Data Lake Storage Gen2-ben (ADLS) és a Delta Lake-ben való tárolást.

## <a name="prerequisites"></a>Előfeltételek
* **Azure-előfizetés.** Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes Azure-fiókot](https://azure.microsoft.com/free/) a virtuális gép létrehozásának megkezdése előtt.
* **Azure Storage-fiók.** Az ADLS-tárolók *forrás- és* *fogadóadattáraként* használhatók. Ha még nem rendelkezik tárfiókkal, tekintse meg az [Azure Storage-fiók létrehozásának](../storage/common/storage-account-create.md) lépéseit ismertető cikket.

Az oktatóanyagban átalakított fájl MoviesDB.csv, amely itt [található.](https://github.com/kromerm/adfdataflowdocs/blob/master/sampledata/moviesDB2.csv) A fájl GitHubról való lekéréséhez másolja a tartalmát egy ön által választott szövegszerkesztőbe, és mentse helyileg .csv-fájlként. A fájl tárfiókba való feltöltésével lásd: [Blobok feltöltése a Azure Portal.](../storage/blobs/storage-quickstart-blobs-portal.md) A példák egy "sample-data" nevű tárolóra hivatkoznak.

## <a name="create-a-data-factory"></a>Adat-előállító létrehozása

Ebben a lépésben létrehoz egy adat-előállítót, és megnyitja a Data Factory felhasználói felületét egy folyamat létrehozásához az adat-előállítóban.

1. Nyissa **meg Microsoft Edge** vagy a Google Chrome **böngészőt.** A felhasználói Data Factory jelenleg csak az Microsoft Edge és a Google Chrome böngészőkben támogatott.
1. A bal oldali menüben válassza az **Erőforrás-integrációs** csoport  >    >  **létrehozása Data Factory**
1. Az Új **data factory lap** Név **részében** adja meg az **ADFTutorialDataFactory nevet**
1. Válassza ki azt az **Azure-előfizetést**, amelyben az adat-előállítót létre szeretné hozni.
1. **Erőforráscsoport:** hajtsa végre a következő lépések egyikét:

    a. Válassza **a Meglévő használata** lehetőséget, majd válasszon ki egy meglévő erőforráscsoportot a legördülő listából.

    b. Válassza **az Új létrehozása** lehetőséget, és adja meg egy erőforráscsoport nevét. 
         
    Az erőforráscsoportokkal kapcsolatos információkért tekintse meg az [Erőforráscsoportok használata az Azure-erőforrások kezeléséhez](../azure-resource-manager/management/overview.md) ismertető cikket. 
1. A **Verzió** résznél válassza a **V2** értéket.
1. A **Hely** területen válassza ki az adat-előállító helyét. A legördülő listán csak a támogatott helyek jelennek meg. Az adat-előállítók által használt adattárak (például az Azure Storage és SQL Database) és számítási erőforrások (például Azure HDInsight) más régiókban is előfordulhatnak.
1. Válassza a **Létrehozás** lehetőséget.
1. A létrehozás befejezése után az értesítés megjelenik az Értesítési központban. Válassza **az Erőforrás ugrás lehetőséget** a Data factory oldalának kiválasztásához.
1. A Data Factory felhasználói felületének külön lapon történő elindításához válassza a **Létrehozás és figyelés** csempét.

## <a name="create-a-pipeline-with-a-data-flow-activity"></a>Folyamat létrehozása adatfolyam-tevékenységgel

Ebben a lépésben egy adatfolyam-tevékenységet tartalmazó folyamatot fog létrehozni.

1. Az **Első lépések** lapon válassza a **Folyamat létrehozása** lehetőséget.

   ![Folyamat létrehozása](./media/doc-common-process/get-started-page.png)

1. A folyamat **Általános** lapján adja meg a **DeltaLake** nevet **a folyamat** neveként.
1. A Tevékenységek **panelen** bontsa ki a **Move and Transform (Áthelyezés és átalakítás)** et. Húzza át a **Adatfolyam** tevékenységet a panelről a folyamatvászonra.

    ![Képernyőkép a folyamatvászonról, ahol eldobhatja a Adatfolyam tevékenységet.](media/tutorial-data-flow/activity1.png)
1. Az **Új Adatfolyam** előugró ablakban válassza az **Új** adattár létrehozása lehetőséget Adatfolyam az adatfolyamnak adja a **DeltaLake nevet.** Ha végzett, kattintson a Befejezés gombra.

    ![Új adatfolyam létrehozásakor az adatfolyam elnevezési helyét bemutató képernyőkép.](media/tutorial-data-flow/activity2.png)
1. A folyamatvászon felső sávjában húzza be a Adatfolyam **hibakeresési csúszkát.** A hibakeresési mód lehetővé teszi az átalakítási logika interaktív tesztelését egy élő Spark-fürtön. Adatfolyam 5–7 percet is igénybe vegyen a bemelegítés, és a felhasználóknak ajánlott először bekapcsolni a hibakeresést, ha Adatfolyam terveznek. További információ: [Hibakeresési mód.](concepts-data-flow-debug-mode.md)

    ![Az Adatfolyam hibakeresési csúszkája helyének képernyőképe.](media/tutorial-data-flow/dataflow1.png)

## <a name="build-transformation-logic-in-the-data-flow-canvas&quot;></a>Átalakítási logika összeállítása az adatfolyam-vásznon

Ebben az oktatóanyagban két adatfolyamot fog létrehozni. A fő adatfolyam egy egyszerű forrás, amely egy új Delta Lake-et hoz létre a fentiekből származó filmek CSV-fájlja alapján. Végül létre fogja hozni az alábbi folyamattervet az adatok a Delta Lake-ben való frissítéséhez.

![Végső folyamat](media/data-flow/data-flow-tutorial-6.png &quot;Végső folyamat")

### <a name="tutorial-objectives"></a>Oktatóanyag célkitűzései

1. Vegyük a MoviesCSV-adatkészlet forrását a fentiek közül, és alkotnak egy új Delta Lake-et abból
1. A logika összeállítása az 1988-as filmek értékelésének "1"-re való frissítésében
1. Az összes film törlése az 1950-es évből
1. Új filmek beszúrása a 2021-es évhez az 1960-ból származó filmek másolása

### <a name="start-from-a-blank-data-flow-canvas"></a>Kezdés üres adatfolyam-vászonból

1. Kattintson a forrásátalakításra
1. Kattintson az új elemre az adatkészlet mellett az alsó panelen 1 Új csatolt szolgáltatás létrehozása a ADLS Gen2
1. Válassza a Tagolt szöveg lehetőséget az adatkészlet típusaként
1. Az adatkészletnek a "MoviesCSV" nevet kell adni 
1. Mutasson a fenti tárolóba feltöltött MoviesCSV fájlra
1. Állítsa be vesszővel tagoltként, és foglalja bele a fejlécet az első sorba 
1. Ugrás a forrásvetület lapra, és kattintson az "Adattípusok észlelése" elemre.
1. A leképezés beállítása után folytathatja a munkát 
1. Fogadó-átalakítás hozzáadása
1. A Delta beágyazott adatkészlet-típus. A tárfiókra kell ADLS Gen2.
   
   ![Beágyazott adatkészlet](media/data-flow/data-flow-tutorial-5.png "Beágyazott adatkészlet")

1. Válasszon egy mappanevet a storage-tárolóban, amelyben az ADF-et szeretné létrehozni a Delta Lake-hez
1. Vissza a folyamat tervezőjéhez, és kattintson a Hibakeresés gombra a folyamat hibakeresési módban való végrehajtásához, amely csak ezt az adatfolyam-tevékenységet futtatja a vásznon. Ezzel létrehozza az új Delta Lake-et a ADLS Gen2.
1. A Factory Resources (Gyári erőforrások) menüben kattintson az > Data flow (Adatfolyam) elemre. 
1. Használja ismét a MoviesCSV-t forrásként, és kattintson ismét az "Adattípusok észlelése" gombra
1. Szűrőátalakítás hozzáadása a forrásátalakításhoz a gráfban
1. Csak a három évnek megfelelő filmsorok engedélyezése, amelyek 1950, 1988 és 1960 lesznek
1. Minden 1988-as film minősítését frissítse "1"-re úgy, hogy mostantól egy származtatott oszlopátalakítást ad hozzá a szűrőátalakításhoz
1. Ugyanabban a származtatott oszlopban hozzon létre 2021-es filmekből egy meglévő évet, és módosítsa az évet 2021-re. Válassza az 1960-ast.
1. A három származtatott oszlop így fog kinézni

   ![Származtatott oszlop](media/data-flow/data-flow-tutorial-2.png "Származtatott oszlop")
   
1. ```Update, insert, delete, and upsert``` A szabályzatok az alter Row átalakításban vannak létrehozva. Adjon hozzá egy alter row transformation (sorátalakítást) a származtatott oszlop után.
1. Az alter row szabályzatnak így kell kinéznie.

   ![Sor módosítása](media/data-flow/data-flow-tutorial-3.png "Sor módosítása")
   
1. Most, hogy minden egyes sortípushoz beállította a megfelelő szabályzatot, ellenőrizze, hogy a megfelelő frissítési szabályok vannak-e beállítva a fogadó-átalakításhoz

   ![Sink (Fogadó)](media/data-flow/data-flow-tutorial-4.png "Sink (Fogadó)")
   
1. Itt a Delta Lake-fogadót használjuk a ADLS Gen2 Data Lake-hez, és engedélyezünk beszúrásokat, frissítéseket és törléseket. 
1. Vegye figyelembe, hogy a Kulcsoszlopok egy összetett kulcs, amely a Film elsődleges kulcs oszlopát és az év oszlopot tartalmazza. Ennek az az oka, hogy a 2021-es hamis filmek az 1960 sor megkétintéséből vannak létrehozva. Ez az egyediség biztosításával elkerüli az ütközéseket, amikor a meglévő sorokat keresi.

### <a name="download-completed-sample"></a>Befejezett minta letöltése
[Itt egy mintamegoldás a Delta-folyamathoz, amely egy adatfolyamot tartalmaz a tóban lévő sorok frissítéséhez/törléséhez:](https://github.com/kromerm/adfdataflowdocs/blob/master/sampledata/DeltaPipeline.zip)

## <a name="next-steps"></a>Következő lépések

További információ az [adatfolyam-kifejezés nyelvről.](data-flow-expression-functions.md)
