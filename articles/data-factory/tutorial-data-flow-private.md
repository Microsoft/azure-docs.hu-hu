---
title: Adatok átalakítása felügyelt Azure Data Factory-leképezési adatfolyammal
description: Ez az oktatóanyag részletes útmutatást nyújt az adatok Azure Data Factory adatfolyamokkal való átalakításához.
author: dcstwh
ms.author: weetok
ms.reviewer: makromer
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 04/14/2021
ms.openlocfilehash: ad101bee84256662d1436ba8d8a49304aecb9129
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/15/2021
ms.locfileid: "107518277"
---
# <a name="transform-data-securely-by-using-mapping-data-flow"></a>Adatok biztonságos átalakítása adatfolyam-leképezés használatával

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Ha még csak ismerkedik az Azure Data Factory használatával, olvassa el [az Azure Data Factory használatának első lépéseit](./introduction.md) ismertető cikket.

Ebben az oktatóanyagban az Data Factory felhasználói felület (UI) használatával fog létrehozni egy folyamatot, amely adatokat másol és alakít át egy Azure Data Lake Storage Gen2-forrásból egy Data Lake Storage Gen2-fogadóba (mindkettő csak a kijelölt hálózatokhoz engedélyezi a *hozzáférést)* egy leképezési adatfolyam használatával a Data Factory [Managed Virtual Network -ban.](managed-virtual-network-private-endpoint.md) Az oktatóanyagban az adatok leképezési adatfolyam használatával való átalakításakor kiterjesztheti a konfigurációs mintát.

Az oktatóanyag során a következő lépéseket hajtja végre:

> [!div class="checklist"]
>
> * Adat-előállító létrehozása
> * Folyamat létrehozása adatfolyam-tevékenységgel.
> * Leképezési adatfolyam összeállítása négy átalakítással.
> * A folyamat próbafuttatása
> * Adatfolyam-tevékenység figyelése.

## <a name="prerequisites"></a>Előfeltételek

* **Azure-előfizetés.** Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes Azure-fiókot](https://azure.microsoft.com/free/) a virtuális gép létrehozásának megkezdése előtt.
* **Azure Storage-fiók.** A Data Lake Storage *forrás- és* *fogadóadattáraként* használhatja. Ha még nem rendelkezik tárfiókkal, tekintse meg az [Azure Storage-fiók létrehozásának](../storage/common/storage-account-create.md?tabs=azure-portal) lépéseit ismertető cikket. *Győződjön meg arról, hogy a tárfiók csak a kiválasztott hálózatokról engedélyezi a hozzáférést.* 

Az oktatóanyagban átalakítunk egy fájlt moviesDB.csv, amely ezen a [GitHub-tartalomwebhelyen található.](https://raw.githubusercontent.com/djpmsft/adf-ready-demo/master/moviesDB.csv) A fájl GitHubról való lekéréséhez másolja a tartalmát egy ön által választott szövegszerkesztőbe, és mentse helyileg .csv-fájlként. A fájl tárfiókba való feltöltésével lásd: [Blobok feltöltése a Azure Portal.](../storage/blobs/storage-quickstart-blobs-portal.md) A példák egy **sample-data nevű tárolóra hivatkoznak.**

## <a name="create-a-data-factory"></a>Adat-előállító létrehozása

Ebben a lépésben létrehoz egy adat-előállítót, és megnyitja a Data Factory felhasználói felületét egy folyamat létrehozásához az adat-előállítóban.

1. Nyissa meg Microsoft Edge vagy a Google Chrome böngészőt. Jelenleg csak a Microsoft Edge És a Google Chrome böngészők támogatják a Data Factory felhasználói felületét.
1. A bal oldali menüben válassza az **Erőforrás létrehozása**  >  **Analytics-Data Factory.**  >  
1. Az **Új adat-előállító** lap **Név** mezőjében adja meg az **ADFTutorialDataFactory** értéket.

   Az adat-előállító nevének *globálisan egyedinek* kell lennie. Ha hibaüzenetet kap a névértékről, adjon meg egy másik nevet az adat-előállítónak (például sajátneveADFTutorialDataFactory). A Data Factory-összetevők elnevezési szabályait a [Data Factory elnevezési szabályait](naming-rules.md) ismertető cikkben találja.

1. Válassza ki azt az **Azure-előfizetést**, amelyben az adat-előállítót létre szeretné hozni.
1. **Erőforráscsoport:** hajtsa végre a következő lépések egyikét:

    * Válassza **a Meglévő használata** lehetőséget, majd válasszon ki egy meglévő erőforráscsoportot a legördülő listából.
    * Válassza **az Új létrehozása** lehetőséget, és adja meg egy erőforráscsoport nevét. 
         
    Az erőforráscsoportokkal kapcsolatos információkért tekintse meg az [Erőforráscsoportok használata az Azure-erőforrások kezeléséhez](../azure-resource-manager/management/overview.md) ismertető cikket. 
1. A **Verzió** résznél válassza a **V2** értéket.
1. A **Hely** területen válassza ki az adat-előállító helyét. A legördülő listában csak a támogatott helyek jelennek meg. Az adat-előállítók által használt adattárak (például az Azure Storage és Azure SQL Database) és számítási erőforrások (például Azure HDInsight) más régiókban is előfordulhatnak.

1. Válassza a **Létrehozás** lehetőséget.
1. A létrehozás befejezése után az értesítés megjelenik az Értesítési központban. Válassza **az Erőforrás ugrás lehetőséget** a Data Factory kiválasztásához. 
1. A Data Factory felhasználói felületének külön lapon történő elindításához válassza a **Létrehozás és figyelés** csempét.

## <a name="create-an-azure-ir-in-data-factory-managed-virtual-network"></a>Hozzon létre Azure IR felügyelt Data Factory Virtual Network

Ebben a lépésben létrehoz egy Azure IR, és engedélyezi Data Factory felügyelt Virtual Network.

1. A Data Factory a Kezelés lehetőséget, majd az **Új** lehetőséget választva hozzon létre egy új Azure IR.

   ![Új alkalmazás létrehozását Azure IR.](./media/tutorial-copy-data-portal-private/create-new-azure-ir.png)
1. Az **Integrációskörnyezet beállítása lapon** a szükséges képességek alapján válassza ki, hogy melyik integrációskörnyezetet hozza létre. Ebben az oktatóanyagban válassza az **Azure, Saját üzemeltetett lehetőséget,** majd kattintson a **Folytatás gombra.** 
1. Válassza **az Azure** lehetőséget, majd kattintson a **Folytatás** gombra egy Azure Integration Runtime létrehozásához.

   ![Képernyőkép egy új Azure IR.](./media/tutorial-copy-data-portal-private/azure-ir.png)

1. A **Virtuális hálózat konfigurálása (előzetes verzió) alatt válassza** az Engedélyezés **lehetőséget.**

   ![Új alkalmazás engedélyezését Azure IR.](./media/tutorial-copy-data-portal-private/enable-managed-vnet.png)

1. Válassza a **Létrehozás** lehetőséget.

## <a name="create-a-pipeline-with-a-data-flow-activity"></a>Folyamat létrehozása adatfolyam-tevékenységgel

Ebben a lépésben egy adatfolyam-tevékenységet tartalmazó folyamatot fog létrehozni.

1. Az **Első lépések** lapon válassza a **Folyamat létrehozása** lehetőséget.

   ![Folyamat létrehozását bemutató képernyőkép.](./media/doc-common-process/get-started-page.png)

1. A folyamat tulajdonságok panelen adja meg a **TransformMovies** nevet a folyamat neveként.
1. A Tevékenységek **ablaktáblán bontsa** ki az **Áthelyezés és átalakítás gombra.** Húzza a **Adatfolyam** tevékenységet a panelről a folyamatvászonra.

1. Az Adatfolyam **hozzáadása előugró** ablakban válassza az **Új** adatfolyam létrehozása, majd a Leképezés **Adatfolyam.** Ha **elkészült,** kattintson az OK gombra.

    ![Képernyőkép a Leképezés Adatfolyam.](media/tutorial-data-flow-private/mapping-dataflow.png)

1. Az adatfolyamnak a Tulajdonságok panelen nevezze el a **TransformMovies** nevet.
1. A folyamatvászon felső sávjában húzza a Adatfolyam **hibakeresési csúszkát.** A hibakeresési mód lehetővé teszi az átalakítási logika interaktív tesztelését egy élő Spark-fürtön. Adatfolyam 5–7 percet is igénybe vegyen a bemelegítés, és a felhasználóknak ajánlott először bekapcsolni a hibakeresést, ha a fejlesztést Adatfolyam tervezik. További információ: [Hibakeresési mód.](concepts-data-flow-debug-mode.md)

    ![Az Adatfolyam hibakeresési csúszkát bemutató képernyőkép.](media/tutorial-data-flow-private/dataflow-debug.png)

## <a name="build-transformation-logic-in-the-data-flow-canvas"></a>Átalakítási logika összeállítása az adatfolyam-vásznon

Az adatfolyam létrehozása után a rendszer automatikusan az adatfolyamvászonra küldi. Ebben a lépésben egy adatfolyamot fog összeépíteni, amely a moviesDB.csv-fájlt a Data Lake Storage-ben veszi fel, és összesíti a comedies 1910 és 2000 között kapott átlagos minősítését. Ezt a fájlt ezután visszaírja a Data Lake Storage.

### <a name="add-the-source-transformation"></a>A forrásátalakítás hozzáadása

Ebben a lépésben beállít egy Data Lake Storage Gen2 forrásként.

1. Az adatfolyam vásznán adjon hozzá egy forrást a **Forrás hozzáadása mező kiválasztásával.**

1. A forrásnak a **MoviesDB nevet kell nevet ad.** Új **forrásadatkészlet** létrehozásához válassza az Új lehetőséget.

1. Válassza **Azure Data Lake Storage Gen2** lehetőséget, majd válassza a **Folytatás lehetőséget.**

1. Válassza **a TagoltSzöveg** lehetőséget, majd válassza a Folytatás **lehetőséget.**

1. Nevezze el az adatkészletet **MoviesDB néven.** A csatolt szolgáltatás legördülő menüben válassza az Új **lehetőséget.**

1. A csatolt szolgáltatás létrehozási képernyőjén nevezze el a Data Lake Storage Gen2 **ADLSGen2** nevet, és adja meg a hitelesítési módszert. Ezután adja meg a kapcsolat hitelesítő adatait. Ebben az oktatóanyagban a fiókkulcsot **használjuk** a tárfiókhoz való csatlakozáshoz. 

1. Győződjön meg arról, hogy engedélyezi **az interaktív szerzői funkciókat.** Az engedélyezése egy percet is igénybe vehet.

    ![Az interaktív tartalom készítését bemutató képernyőkép.](./media/tutorial-data-flow-private/interactive-authoring.png)

1. Válassza a **Kapcsolat tesztelése** elemet. Ennek sikertelennek kell lennie, mert a tárfiók nem engedélyezi a hozzáférést egy privát végpont létrehozása és jóváhagyása nélkül. A hibaüzenetben megjelenik egy hivatkozás, amely egy privát végpont létrehozására hivatkozik, amely alapján létrehozhat egy felügyelt privát végpontot. Másik lehetőségként közvetlenül a Kezelés lapra [](#create-a-managed-private-endpoint) kell **átmenni,** és követni az ebben a szakaszban található utasításokat egy felügyelt privát végpont létrehozásához.

1. Ne nyissa meg a párbeszédpanelt, majd nyissa meg a tárfiókját.

1. A privát hivatkozás [jóváhagyásához kövesse](#approval-of-a-private-link-in-a-storage-account) az ebben a szakaszban található utasításokat.

1. Vissza a párbeszédpanelre. Válassza **ismét a Kapcsolat tesztelése** lehetőséget, majd válassza a **Létrehozás** lehetőséget a csatolt szolgáltatás üzembe helyezéséhez.

1. Az adatkészlet-létrehozási képernyőn adja meg, hogy a fájl hol található a Fájl elérési útja **mező** alatt. Ebben az oktatóanyagban a moviesDB.csv tároló **sample-data tárolójában található.** Mivel a fájl fejlécekkel rendelkezik, jelölje be az **Első sor fejlécként** jelölőnégyzetet. Válassza **a Kapcsolatból/tárolóból** lehetőséget a fejlécséma importáláshoz közvetlenül a tárolóban található fájlból. Ha **elkészült,** kattintson az OK gombra.

    ![A forrásútvonalat bemutató képernyőkép.](media/tutorial-data-flow-private/source-file-path.png)

1. Ha a hibakeresési fürt elindult, a forrásátalakítás **Adatelőnézet** lapján válassza a Frissítés lehetőséget az adatok pillanatképének megtekintéséhez.  Az adatelőnézettel ellenőrizheti, hogy az átalakítás megfelelően van-e konfigurálva.

    ![Az Adatelőnézet lapot bemutató képernyőkép.](media/tutorial-data-flow-private/data-preview.png)

#### <a name="create-a-managed-private-endpoint"></a>Felügyelt privát végpont létrehozása

Ha nem a hivatkozást használja az előző kapcsolat tesztelése során, kövesse az elérési utat. Most létre kell hoznia egy felügyelt privát végpontot, amely a létrehozott csatolt szolgáltatáshoz fog csatlakozni.

1. Ugrás a **Kezelés lapra.**

   > [!NOTE]
   > Előfordulhat, **hogy** a Kezelés lap nem érhető el az összes Data Factory számára. Ha nem látja, a Privát végpontok létesítése lehetőség kiválasztásával férhet hozzá a privát  >    >  **végponthoz.**

1. Ugrás a **Felügyelt privát végpontok szakaszra.**
1. A **Felügyelt privát végpontok** alatt válassza az + **Új lehetőséget.**

    ![Képernyőkép a Felügyelt privát végpontok Új gombról.](./media/tutorial-data-flow-private/new-managed-private-endpoint.png) 

1. Válassza a **Azure Data Lake Storage Gen2** csempét a listából, majd válassza a **Folytatás lehetőséget.**
1. Adja meg a létrehozott tárfiók nevét.
1. Válassza a **Létrehozás** lehetőséget.
1. Néhány másodperc elteltével látnia kell, hogy a létrehozott privát kapcsolathoz jóváhagyásra van szükség.
1. Válassza ki a létrehozott privát végpontot. Egy hivatkozással jóváhagyhatja a privát végpontot a tárfiók szintjén.

    ![A Privát végpont kezelése panel képernyőképe.](./media/tutorial-data-flow-private/manage-private-endpoint.png) 

#### <a name="approval-of-a-private-link-in-a-storage-account"></a>Privát kapcsolat jóváhagyása tárfiókban

1. A tárfiók beállítások szakaszában kattintson a Privát **végponti** kapcsolatok **elemre.**

1. Jelölje be a létrehozott privát végpont jelölőnégyzetét, majd válassza a **Jóváhagyás lehetőséget.**

    ![A privát végpont Jóváhagyás gombját bemutató képernyőkép.](./media/tutorial-data-flow-private/approve-private-endpoint.png)

1. Adjon meg egy leírást, és válassza az **igen lehetőséget.**
1. Vissza lap **Kezelés lapjának** Felügyelt privát  végpontok szakaszát Data Factory.
1. Körülbelül egy perc múlva megjelenik a jóváhagyás a privát végponthoz.

### <a name="add-the-filter-transformation"></a>A szűrőátalakítás hozzáadása

1. Új átalakítás hozzáadásához válassza a plusz ikont a forráscsomópont mellett az adatfolyam vásznán. Az első átalakítás, amit hozzá fog adni, egy **Szűrő.**

    ![Szűrő hozzáadását bemutató képernyőkép.](media/tutorial-data-flow-private/add-filter.png)
1. Nevezze el a szűrőátalakítást **FilterYears néven.** A kifejezésszerkesztő megnyitásához válassza a **Szűrés a következőn** melletti kifejezésmezőt. Itt megadhatja a szűrési feltételt.

    ![A FilterYears (Évévek) képernyőképe.](media/tutorial-data-flow-private/filter-years.png)
1. Az adatfolyam-kifejezésszerkesztővel interaktív módon építhet ki különböző átalakítások során használható kifejezéseket. A kifejezések beépített függvényeket, a bemeneti séma oszlopait és a felhasználó által definiált paramétereket tartalmazhatnak. További információ a kifejezések felépítéséről: [Adatfolyam-kifejezésszerkesztő.](./concepts-data-flow-expression-builder.md)

    * Ebben az oktatóanyagban az 1910 és 2000 között előhozott műfajban szeretné szűrni a filmek filmjeit. Mivel az év jelenleg egy sztring, egész számra kell konvertálni a függvény ```toInteger()``` használatával. A nagyobb vagy egyenlő (>=) és kisebb vagy egyenlő (<=) operátorok használatával hasonlítsa össze az 1910-es és 2000-es konstansértékeket. Ezeket a kifejezéseket a és a (&&) operátorral összesiti. A kifejezés a következőként jön ki:

        ```toInteger(year) >= 1910 && toInteger(year) <= 2000```

    * Annak kiderítésében, hogy mely filmek a vétesek, a függvény használatával megkeresheti a "Csak" mintát az ```rlike()``` oszlop műfajai között. Az rlike kifejezés és az év összehasonlításának uniója a következőképpen:

        ```toInteger(year) >= 1910 && toInteger(year) <= 2000 && rlike(genres, 'Comedy')```

    * Ha aktív a hibakeresési fürt, a Frissítés  lehetőség kiválasztásával ellenőrizheti a logikát, és láthatja a kifejezés kimenetét a használt bemenetekkel összehasonlítva. Több helyes válasz is van arra, hogyan valósítható meg ez a logika az adatfolyam-kifejezés nyelvének használatával.

        ![A szűrőkifejezést bemutató képernyőkép.](media/tutorial-data-flow-private/filter-expression.png)

    * Ha **végzett** a kifejezéssel, válassza a Mentés és befejezés lehetőséget.

1. A szűrő **megfelelő működését a Data Preview lekérése** után ellenőrizheti.

    ![A szűrt adatelőnézetet bemutató képernyőkép.](media/tutorial-data-flow-private/filter-data.png)

### <a name="add-the-aggregate-transformation"></a>Az összesítési átalakítás hozzáadása

1. A következő hozzáadni fog egy Összesítés átalakítást **a** **Sémamódosító alatt.**

    ![Képernyőkép az összesítés hozzáadásáról.](media/tutorial-data-flow-private/add-aggregate.png)
1. Az összesített átalakításnak nevezze el **az AggregateComedyRating nevet.** A **Csoportosítási alap lapon** válassza ki az **év** lehetőséget a legördülő menüből az összesítések csoportosításhoz a film kiugró évének szerint.

    ![Az összesítési csoportot bemutató képernyőkép.](media/tutorial-data-flow-private/group-by-year.png)
1. Ugrás az **Összesítések lapra.** A bal oldali szövegmezőben adja az **AverageComedyRating** nevet az összesített oszlopnak. A megfelelő kifejezésmezőt választva adja meg az összesítő kifejezést a kifejezésszerkesztőben.

    ![Képernyőkép az összesített oszlop nevéről.](media/tutorial-data-flow-private/name-column.png)
1. A Minősítés oszlop átlagának **lekért értékhez** használja az ```avg()``` aggregátum függvényt. Mivel **az Értékelés** egy sztring, és numerikus bemenetet vesz fel, az értéket számra kell konvertálnunk a ```avg()``` ```toInteger()``` függvényen keresztül. Ez a kifejezés így néz ki:

    ```avg(toInteger(Rating))```

1. Ha **elkészült, válassza** a Mentés lehetőséget, és fejezze be a munkát.

    ![Az összesítés mentését bemutató képernyőkép.](media/tutorial-data-flow-private/save-aggregate.png)
1. A **transzformáció kimenetének megtekintéséhez** válassza az Adatelőnézet lapot. Figyelje meg, hogy csak két oszlop van: **year** és **AverageComedyRating.**

### <a name="add-the-sink-transformation"></a>Fogadó-átalakítás hozzáadása

1. Ezután hozzá kell adni egy **fogadó-átalakítást** a Cél **alatt.**

    ![Képernyőkép egy fogadó hozzáadásáról.](media/tutorial-data-flow-private/add-sink.png)
1. A fogadónak nevezze el a **fogadót.** Válassza **az Új** lehetőséget a fogadó adatkészlet létrehozásához.

    ![Képernyőkép egy fogadó létrehozásáról.](media/tutorial-data-flow-private/create-sink.png)
1. Az Új **adatkészlet lapon** válassza a **Azure Data Lake Storage Gen2** majd a Folytatás **lehetőséget.**

1. A Formátum **kiválasztása lapon** válassza a **TagoltSzöveg,** majd a Folytatás **lehetőséget.**

1. Nevezze el a fogadó adatkészletet **MoviesSink néven.** A csatolt szolgáltatáshoz válassza a forrásátalakításhoz létrehozott **ADLSGen2** csatolt szolgáltatást. Adjon meg egy kimeneti mappát az adatok írásához. Ebben az oktatóanyagban a tároló  sample-data mappájának kimenetére **írunk.** A mappának nem kell előre léteznie, és dinamikusan is létre lehet őket létrehozni. Jelölje be **az Első sor fejlécként** jelölőnégyzetet, és a Séma importálása **beállításhoz** válassza a Nincs **lehetőséget.** Válassza az **OK** lehetőséget.

    ![A fogadó elérési útját bemutató képernyőkép.](media/tutorial-data-flow-private/sink-file-path.png)

Az adatfolyam elkészült. Készen áll arra, hogy futtassa a folyamatban.

## <a name="run-and-monitor-the-data-flow"></a>Az adatfolyam futtatása és figyelése

Közzététel előtt hibakeresést is futtathat a folyamatokon. Ebben a lépésben elindítja az adatfolyam-folyamat hibakeresési futtatását. Bár az adatelőnézet nem ír adatokat, a hibakeresési futtatás adatokat ír a fogadó célhelyre.

1. Ugrás a folyamatvászonra. A **hibakeresési** futtatás aktiválásához válassza a Hibakeresés lehetőséget.

1. Az adatfolyam-tevékenységek folyamat hibakeresése az aktív hibakeresési fürtöt használja, de az inicializálása még legalább egy percet vesz igénybe. A folyamat előrehaladását a Kimenet lapon **követheti** nyomon. A futtatás sikeres futtatása után válassza a szemüveg ikont a futtatás részleteinek kiválasztásához.

1. A Részletek lapon láthatja a sorok számát és az egyes átalakítási lépésenként eltöltött időt.

    ![A figyelési futtatás képernyőképe.](media/tutorial-data-flow-private/run-details.png)
1. Válasszon ki egy átalakítást, hogy részletes információkat kapjon az oszlopokról és az adatok particionálásáról.

Ha megfelelően követte az oktatóanyagot, 83 sort és 2 oszlopot kellett írnia a fogadó mappába. A blobtároló ellenőrzésével ellenőrizheti az adatok helyességét.

## <a name="summary"></a>Összefoglalás

Ebben az oktatóanyagban az Data Factory felhasználói felületével létrehozott egy folyamatot, amely adatokat másol és alakít át egy Data Lake Storage Gen2-forrásból egy Data Lake Storage Gen2-fogadóba (mindkettő csak a kijelölt hálózatokhoz engedélyezi a hozzáférést) egy leképezési adatfolyam használatával a Data Factory Managed Virtual Network - [Virtual Network.](managed-virtual-network-private-endpoint.md)