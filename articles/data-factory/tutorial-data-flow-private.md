---
title: Az adatátalakítás Azure Data Factory felügyelt virtuális hálózati leképezési adatfolyamattal
description: Ez az oktatóanyag részletes útmutatást nyújt a Azure Data Factory használatával történő adatátalakításhoz a leképezési adatfolyamatokkal.
author: dcstwh
ms.author: weetok
ms.reviewer: makromer
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 01/15/2021
ms.openlocfilehash: a5c93244862d72f9c8ea2928c41e699302b1752b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98249438"
---
# <a name="transform-data-securely-by-using-mapping-data-flow"></a>Az adatforgalom biztonságos átalakítása a leképezési adatfolyam használatával

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Ha még csak ismerkedik az Azure Data Factory használatával, olvassa el [az Azure Data Factory használatának első lépéseit](./introduction.md) ismertető cikket.

Ebben az oktatóanyagban a Data Factory felhasználói felületét (UI) fogja használni egy olyan folyamat létrehozásához, amely egy Azure Data Lake Storage Gen2 forrásból származó adatok egy Data Lake Storage Gen2 fogadóba való másolását és átalakítását végzi el *(mindkettő csak a kiválasztott hálózatokhoz való hozzáférést engedélyezi)* [Data Factory felügyelt Virtual Network](managed-virtual-network-private-endpoint.md)a leképezési folyamat használatával. Az oktatóanyagban szereplő konfigurációs minta kibontásával az Adatátalakítási folyamat használatával alakíthatja át az adatátvitelt.

Az oktatóanyag során a következő lépéseket hajtja végre:

> [!div class="checklist"]
>
> * Adat-előállító létrehozása
> * Folyamat létrehozása adatfolyam-tevékenységgel.
> * Hozzon létre egy leképezési adatfolyamot négy átalakítással.
> * A folyamat próbafuttatása
> * Adatfolyam-tevékenység figyelése.

## <a name="prerequisites"></a>Előfeltételek

* **Azure-előfizetés**. Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes Azure-fiókot](https://azure.microsoft.com/free/) a virtuális gép létrehozásának megkezdése előtt.
* **Azure Storage-fiók**. A Data Lake Storage *forrásként* *és fogadó* adattárakként használja. Ha még nem rendelkezik tárfiókkal, tekintse meg az [Azure Storage-fiók létrehozásának](../storage/common/storage-account-create.md?tabs=azure-portal) lépéseit ismertető cikket. *Győződjön meg arról, hogy a Storage-fiók csak a kiválasztott hálózatokról engedélyezi a hozzáférést.* 

Az oktatóanyagban átalakított fájl moviesDB.csv, amely ebben a [GitHub-tartalmi webhelyen](https://raw.githubusercontent.com/djpmsft/adf-ready-demo/master/moviesDB.csv)található. A fájl GitHubról történő lekéréséhez másolja a tartalmat egy tetszőleges szövegszerkesztőbe, és mentse helyileg. csv-fájlként. A fájlnak a Storage-fiókba való feltöltéséhez lásd: [Blobok feltöltése a Azure Portal](../storage/blobs/storage-quickstart-blobs-portal.md). A példák egy **minta-az** adattárolóra hivatkoznak.

## <a name="create-a-data-factory"></a>Adat-előállító létrehozása

Ebben a lépésben létrehoz egy adatelőállítót, és megnyitja a Data Factory felhasználói felületet egy folyamat létrehozásához az adatelőállítóban.

1. Nyissa meg a Microsoft Edge vagy a Google Chrome böngészőt. Jelenleg csak a Microsoft Edge és a Google Chrome böngésző támogatja a Data Factory felhasználói felületet.
1. A bal oldali menüben válassza az **erőforrás létrehozása**  >  **elemzési**  >  **Data Factory** elemet.
1. Az **Új adat-előállító** lap **Név** mezőjében adja meg az **ADFTutorialDataFactory** értéket.

   Az adat-előállító nevének *globálisan egyedinek* kell lennie. Ha a név értékével kapcsolatos hibaüzenetet kap, adjon meg egy másik nevet az adatelőállítónak (például Sajátneveadftutorialdatafactory). A Data Factory-összetevők elnevezési szabályait a [Data Factory elnevezési szabályait](naming-rules.md) ismertető cikkben találja.

1. Válassza ki azt az **Azure-előfizetést**, amelyben az adat-előállítót létre szeretné hozni.
1. **Erőforráscsoport:** hajtsa végre a következő lépések egyikét:

    * Válassza a **meglévő használata** lehetőséget, majd válasszon ki egy meglévő erőforráscsoportot a legördülő listából.
    * Válassza az **új létrehozása** lehetőséget, és adja meg az erőforráscsoport nevét. 
         
    Az erőforráscsoportokkal kapcsolatos információkért tekintse meg az [Erőforráscsoportok használata az Azure-erőforrások kezeléséhez](../azure-resource-manager/management/overview.md) ismertető cikket. 
1. A **Verzió** résznél válassza a **V2** értéket.
1. A **Hely** területen válassza ki az adat-előállító helyét. A legördülő listában csak a támogatott helyszínek jelennek meg. Az adattárak (például az Azure Storage és a Azure SQL Database) és a számítási erőforrások (például az Azure HDInsight) más régiókban is használhatók.

1. Válassza a **Létrehozás** lehetőséget.
1. A létrehozás befejezése után megjelenik az értesítési központban megjelenő értesítés. Válassza az **Ugrás az erőforráshoz** lehetőséget, hogy megnyissa a **Data Factory** lapot.
1. A Data Factory felhasználói felületének külön lapon történő elindításához válassza a **Létrehozás és figyelés** csempét.

## <a name="create-an-azure-ir-in-data-factory-managed-virtual-network"></a>Azure IR létrehozása Data Factory felügyelt Virtual Network

Ebben a lépésben létrehoz egy Azure IR, és engedélyezi Data Factory felügyelt Virtual Network.

1. A Data Factory portálon lépjen a **kezelés** elemre, és válassza az **új** lehetőséget új Azure IR létrehozásához.

   ![Képernyőkép, amely egy új Azure IR létrehozását mutatja be.](./media/tutorial-copy-data-portal-private/create-new-azure-ir.png)
1. Az **Integration Runtime telepítése** lapon válassza ki, hogy melyik integrációs modult szeretné létrehozni a szükséges képességek alapján. Ebben az oktatóanyagban válassza az **Azure, saját** üzemeltetésű lehetőséget, majd kattintson a **Continue (folytatás**) gombra. 
1. Válassza az **Azure** lehetőséget, majd kattintson a **Folytatás** gombra egy Azure Integration Runtime létrehozásához.

   ![Az új Azure IR bemutató képernyőkép.](./media/tutorial-copy-data-portal-private/azure-ir.png)

1. A **virtuális hálózat konfigurációja (előzetes verzió)** alatt válassza az **Engedélyezés** lehetőséget.

   ![Az új Azure IR engedélyezését bemutató képernyőkép.](./media/tutorial-copy-data-portal-private/enable-managed-vnet.png)

1. Válassza a **Létrehozás** lehetőséget.

## <a name="create-a-pipeline-with-a-data-flow-activity"></a>Folyamat létrehozása adatfolyam-tevékenységgel

Ebben a lépésben olyan folyamatot hoz létre, amely egy adatfolyam-tevékenységet tartalmaz.

1. Az **Első lépések** lapon válassza a **Folyamat létrehozása** lehetőséget.

   ![A folyamat létrehozását bemutató képernyőkép.](./media/doc-common-process/get-started-page.png)

1. A folyamat tulajdonságok paneljén adja meg a **TransformMovies** a folyamat neveként.
1. A gyári felső sávban csúsztassa az **adatfolyam hibakeresési** csúszkáját. A hibakeresési mód lehetővé teszi az átalakítási logika interaktív tesztelését egy élő Spark-fürtön. Az adatfolyam-fürtök öt – hét percet vesznek igénybe. Ha az adatforgalom fejlesztését tervezi, kapcsolja be először az adatfolyam- **hibakeresést** . További információ: [hibakeresési mód](./concepts-data-flow-debug-mode.md).

    ![Képernyőkép, amely megjeleníti az adatfolyam-hibakeresési csúszkát.](media/tutorial-data-flow-private/dataflow-debug.png)
1. A **tevékenységek** ablaktáblában bontsa ki az **áthelyezés és átalakítás** csomópontot. Húzza az **adatfolyam** tevékenységet a panelről a folyamat vászonra.

1. Az **adatfolyam hozzáadása** felugró ablakban válassza az **új adatfolyam létrehozása** lehetőséget, majd válassza **az adatforgalom leképezése** lehetőséget. Ha elkészült, kattintson **az OK gombra** .

    ![A leképezési adatfolyamot megjelenítő képernyőkép.](media/tutorial-data-flow-private/mapping-dataflow.png)

1. A Tulajdonságok ablaktáblán nevezze el az adatfolyam **TransformMovies** .

## <a name="build-transformation-logic-in-the-data-flow-canvas"></a>Átalakítási logika létrehozása az adatfolyam-vásznon

Miután létrehozta az adatfolyamatot, a rendszer automatikusan elküldi az adatfolyam-vásznon. Ebben a lépésben egy olyan adatfolyamatot fog létrehozni, amely a moviesDB.csv fájlt Data Lake Storage és összesíti a vígjátékok 1910 és 2000 közötti átlagos minősítését. Ezt a fájlt ezután visszaírja a Data Lake Storageba.

### <a name="add-the-source-transformation"></a>A forrás-átalakítás hozzáadása

Ebben a lépésben a Data Lake Storage Gen2t állítja be forrásként.

1. Az adatfolyam-vásznon adja hozzá a forrást a **forrás hozzáadása** mező kiválasztásával.

1. Nevezze el a forrás **MoviesDB**. Az **új** elemre kattintva hozzon létre egy új forrás-adatkészletet.

1. Válassza a **Azure Data Lake Storage Gen2** lehetőséget, majd kattintson a **Folytatás** gombra.

1. Válassza a **DelimitedText** lehetőséget, majd kattintson a **Folytatás** gombra.

1. Nevezze el az adatkészlet **MoviesDB**. A társított szolgáltatás legördülő menüben válassza az **új** lehetőséget.

1. A társított szolgáltatás létrehozása képernyőn nevezze el a Data Lake Storage Gen2 társított szolgáltatás **ADLSGen2** , és adja meg a hitelesítési módszert. Ezután adja meg a kapcsolatok hitelesítő adatait. Ebben az oktatóanyagban a **fiók kulcsát** használjuk a Storage-fiókhoz való kapcsolódáshoz. 

1. Győződjön meg arról, hogy az **interaktív szerzői műveletek** engedélyezve vannak. Egy percet is igénybe vehet.

    ![Az interaktív szerzői műveleteket bemutató képernyőkép.](./media/tutorial-data-flow-private/interactive-authoring.png)

1. Válassza a **Kapcsolat tesztelése** elemet. A művelet végrehajtása sikertelen, mert a Storage-fiók privát végpont létrehozása és jóváhagyása nélkül nem teszi lehetővé a hozzáférést. A hibaüzenetben egy olyan hivatkozást kell látnia, amely létrehoz egy privát végpontot, amelyet követve felügyelt privát végpontokat hozhat létre. Egy másik lehetőség, hogy közvetlenül a **kezelés** lapra lép, és az [ebben a szakaszban](#create-a-managed-private-endpoint) szereplő utasításokat követve felügyelt privát végpontot hoz létre.

1. Tartsa nyitva a párbeszédpanelt, és nyissa meg a Storage-fiókját.

1. A privát hivatkozás jóváhagyásához kövesse az [ebben a szakaszban](#approval-of-a-private-link-in-a-storage-account) található utasításokat.

1. Lépjen vissza a párbeszédpanelre. Válassza a kapcsolat újbóli **tesztelése** lehetőséget, majd válassza a **Létrehozás** lehetőséget a társított szolgáltatás telepítéséhez.

1. Az adatkészlet létrehozása képernyőn adja meg, hogy hol található a fájl **elérési útja** mezőben. Ebben az oktatóanyagban a fájl moviesDB.csv található a tároló **minta-** az adattárban. Mivel a fájl fejléceket tartalmaz, jelölje be az **első sort fejlécként** jelölőnégyzetet. Válassza a **Kapcsolódás/áruház** lehetőséget, hogy a fejléc-sémát közvetlenül a tárolóban lévő fájlból importálja. Ha elkészült, kattintson **az OK gombra** .

    ![Képernyőkép, amely a forrás elérési útját jeleníti meg.](media/tutorial-data-flow-private/source-file-path.png)

1. Ha a hibakeresési fürt elindult, lépjen a forrás-átalakítás **adatelőnézet** lapjára, és válassza a **frissítés** lehetőséget az adatok pillanatképének beolvasásához. Az adatelőnézet használatával ellenőrizheti, hogy az átalakítás megfelelően van-e konfigurálva.

    ![Az adatelőnézet fület megjelenítő képernyőkép.](media/tutorial-data-flow-private/data-preview.png)

#### <a name="create-a-managed-private-endpoint"></a>Felügyelt privát végpont létrehozása

Ha a fenti kapcsolat tesztelésekor nem használja a hiperhivatkozást, kövesse az elérési utat. Most létre kell hoznia egy felügyelt magánhálózati végpontot, amelyhez csatlakozni fog a létrehozott társított szolgáltatáshoz.

1. Lépjen a **kezelés** lapra.

   > [!NOTE]
   > Előfordulhat, hogy a **kezelés** lap nem érhető el az összes Data Factory példányhoz. Ha nem látja, elérheti a privát végpontokat a **szerzői**  >  **kapcsolatok**  >  **privát végpontjának** kiválasztásával.

1. Lépjen a **felügyelt privát végpontok** szakaszra.
1. Válassza az **+ új** lehetőséget a **felügyelt privát végpontok** alatt.

    ![A felügyelt privát végpontok új gombját bemutató képernyőkép.](./media/tutorial-data-flow-private/new-managed-private-endpoint.png) 

1. Válassza ki a **Azure Data Lake Storage Gen2** csempét a listából, majd válassza a **Folytatás** lehetőséget.
1. Adja meg a létrehozott Storage-fiók nevét.
1. Válassza a **Létrehozás** lehetőséget.
1. Néhány másodperc elteltével látnia kell, hogy a privát hivatkozás létrehozása jóváhagyást igényel.
1. Válassza ki a létrehozott privát végpontot. Megtekintheti a hivatkozásokat, amelyekkel jóváhagyhatja a magánhálózati végpontot a Storage-fiók szintjén.

    ![A privát végpont kezelése panelt bemutató képernyőkép.](./media/tutorial-data-flow-private/manage-private-endpoint.png) 

#### <a name="approval-of-a-private-link-in-a-storage-account"></a>Privát hivatkozás jóváhagyása egy Storage-fiókban

1. A Storage-fiókban válassza a **privát végponti kapcsolatok** **lehetőséget a beállítások** szakaszban.

1. Jelölje be a létrehozott privát végpont jelölőnégyzetét, majd válassza a **jóváhagyás** lehetőséget.

    ![A privát végpontok jóváhagyása gombot megjelenítő képernyőkép.](./media/tutorial-data-flow-private/approve-private-endpoint.png)

1. Adjon hozzá egy leírást, és válassza az **Igen** lehetőséget.
1. Lépjen vissza a Data Factory **kezelés** lapján található **felügyelt privát végpontok** szakaszra.
1. Körülbelül egy perc elteltével megjelenik a jóváhagyás a privát végponthoz.

### <a name="add-the-filter-transformation"></a>A szűrő átalakításának hozzáadása

1. Az adatáramlási vászonon a forrás csomópont mellett válassza a plusz ikont egy új átalakítás hozzáadásához. Az első felvenni kívánt átalakítás egy **szűrő**.

    ![A szűrő hozzáadását bemutató képernyőkép.](media/tutorial-data-flow-private/add-filter.png)
1. Nevezze el a szűrő átalakítási **FilterYears**. Kattintson a **szűrés** elem melletti kifejezés mezőre a Kifejezésszerkesztő megnyitásához. Itt adja meg a szűrési feltételt.

    ![Képernyőkép, amely a FilterYears mutatja.](media/tutorial-data-flow-private/filter-years.png)
1. Az adatfolyam-kifejezés-szerkesztővel interaktív módon hozhat létre kifejezéseket különböző átalakításokban való használatra. A kifejezések tartalmazhatnak beépített függvényeket, a bemeneti sémából származó oszlopokat és a felhasználó által definiált paramétereket. A kifejezések létrehozásával kapcsolatos további információkért lásd: [adatáramlási kifejezés-szerkesztő](./concepts-data-flow-expression-builder.md).

    * Ebben az oktatóanyagban olyan filmeket kíván szűrni a vígjátékok műfajában, amelyek a 1910-es és a 2000-as évek közötti időszakban jöttek létre. Mivel az év jelenleg sztring, át kell alakítania egy egész számra a ```toInteger()``` függvény használatával. Használja a nagyobb vagy egyenlő (>=) értéket, és kisebb vagy egyenlő (<=) operátorokkal hasonlítsa össze a 1910 és a 2000 literális év értékeit. Egyesítse ezeket a kifejezéseket a és a (&&) operátorral együtt. A kifejezés a következőképpen érkezik:

        ```toInteger(year) >= 1910 && toInteger(year) <= 2000```

    * Ha szeretné megkeresni, hogy mely Filmek vígjátékok, a ```rlike()``` függvénnyel megkeresheti a "komédia" mintát a műfajok oszlopban. Union The rlike kifejezés az év összehasonlításával a Get:

        ```toInteger(year) >= 1910 && toInteger(year) <= 2000 && rlike(genres, 'Comedy')```

    * Ha a hibakeresési fürt aktív, a **frissítés** gombra kattintva ellenőrizheti, hogy a kifejezés kimenete a használt bemenetekhez képest látható-e. A logikát az adatáramlás kifejezésének nyelvének használatával több helyes választ kaphat.

        ![A szűrési kifejezést megjelenítő képernyőkép.](media/tutorial-data-flow-private/filter-expression.png)

    * Ha elkészült a kifejezéssel, válassza a **Mentés és Befejezés** lehetőséget.

1. Egy **Adatelőnézet** beolvasása annak ellenőrzéséhez, hogy a szűrő megfelelően működik-e.

    ![A szűrt adatelőnézetet megjelenítő képernyőkép.](media/tutorial-data-flow-private/filter-data.png)

### <a name="add-the-aggregate-transformation"></a>Az összesített átalakítás hozzáadása

1. A hozzáadni kívánt következő átalakítás a **séma-módosító** alatt létrehozott **összesített** transzformáció.

    ![Az Összesítés hozzáadását bemutató képernyőkép.](media/tutorial-data-flow-private/add-aggregate.png)
1. Nevezze el az összesített átalakítási **AggregateComedyRating**. A **Csoportosítás** lapon a legördülő listából válassza az **év** lehetőséget, hogy csoportosítsa az összesítéseket az év során a mozgókép kilépésekor.

    ![Az összesítő csoportot megjelenítő képernyőkép.](media/tutorial-data-flow-private/group-by-year.png)
1. Nyissa meg az **összesítések** lapot. A bal oldali szövegmezőben nevezze el az összesítő oszlop **AverageComedyRating**. A jobb oldali kifejezés mező kiválasztásával adja meg az összesítő kifejezést a Expression Builder használatával.

    ![Az összesítő oszlop nevét megjelenítő képernyőkép.](media/tutorial-data-flow-private/name-column.png)
1. Az oszlop- **minősítés** átlagának lekéréséhez használja az ```avg()``` összesítő függvényt. Mivel a **minősítés** egy karakterlánc, és ```avg()``` egy numerikus bemenetet vesz igénybe, az értéket egy számra kell konvertálnia a ```toInteger()``` függvényen keresztül. A következő kifejezés így néz ki:

    ```avg(toInteger(Rating))```

1. Ha elkészült, válassza **a Mentés és Befejezés** lehetőséget.

    ![Az Összesítés mentését bemutató képernyőkép.](media/tutorial-data-flow-private/save-aggregate.png)
1. Az átalakítás kimenetének megtekintéséhez nyissa meg az **adatelőnézet** lapot. Figyelje meg, hogy csak két oszlop van, az **év** és a **AverageComedyRating**.

### <a name="add-the-sink-transformation"></a>A fogadó átalakításának hozzáadása

1. Ezután hozzá kíván **adni egy** fogadó átalakítást a **célhely** területen.

    ![A fogadó felvételét bemutató képernyőkép.](media/tutorial-data-flow-private/add-sink.png)
1. Nevezze el a fogadó fogadót. Válassza az **új** lehetőséget a fogadó adatkészlet létrehozásához.

    ![A fogadó létrehozását bemutató képernyőkép.](media/tutorial-data-flow-private/create-sink.png)
1. Az **új adatkészlet** lapon válassza a **Azure Data Lake Storage Gen2** lehetőséget, majd kattintson a **Folytatás** gombra.

1. A **formátum kiválasztása** lapon válassza a **DelimitedText** lehetőséget, majd kattintson a **Folytatás** gombra.

1. Nevezze el a fogadó adatkészletet **MoviesSink**. A társított szolgáltatás esetében válassza ugyanazt a **ADLSGen2** társított szolgáltatást, amelyet a forrás-átalakításhoz hozott létre. Adja meg azt a kimeneti mappát, ahová az adatokat írni kívánja. Ebben az oktatóanyagban a tároló **minta-adatokat** tartalmazó mappa **kimenetét** írunk. A mappának nem kell előre megadnia, és dinamikusan létre lehet hozni. Jelölje ki az **első sort fejlécként** jelölőnégyzetet, és válassza a **nincs** lehetőséget a **séma importálása** lehetőségnél. Válassza az **OK** lehetőséget.

    ![A fogadó elérési útját bemutató képernyőkép.](media/tutorial-data-flow-private/sink-file-path.png)

Most, hogy befejezte az adatfolyam felépítése. Készen áll arra, hogy a folyamaton fusson.

## <a name="run-and-monitor-the-data-flow"></a>Az adatfolyam futtatása és figyelése

A folyamat a közzététel előtt hibakeresést végezhet. Ebben a lépésben az adatfolyam-folyamat hibakeresési futtatását indítja el. Amíg az adatelőnézet nem ír adatírást, a hibakeresési kísérlet az adatait a fogadó célhelyére fogja írni.

1. Nyissa meg a folyamat vászonját. Hibakeresési Futtatás indításához válassza a **hibakeresés** lehetőséget.

1. Az adatfolyam-tevékenységek feldolgozási folyamata az aktív hibakeresési fürtöt használja, de még legalább egy percet is igénybe vesz az inicializáláshoz. Az előrehaladást a **kimenet** lapon követheti nyomon. A Futtatás sikeres futtatása után válassza a szemüvegek ikont a Futtatás részleteihez.

1. A Részletek lapon megtekintheti a sorok számát és az egyes átalakítási lépéseken töltött időt.

    ![A figyelési futtatást bemutató képernyőkép.](media/tutorial-data-flow-private/run-details.png)
1. Válasszon ki egy átalakítást, hogy részletes információkat kapjon az oszlopokról és az adatok particionálásáról.

Ha ezt az oktatóanyagot helyesen követte, a fogadó mappájába írt 83 és 2 oszlopot kell írnia. A blob Storage ellenőrzésével ellenőrizheti, hogy helyesek-e az adatok.

## <a name="summary"></a>Összefoglalás

Ebben az oktatóanyagban a Data Factory felhasználói felülettel létrehozott egy folyamatot, amely egy Data Lake Storage Gen2 forrásból származó adatok egy Data Lake Storage Gen2 fogadóba történő másolását és átalakítását végzi el (mindkettő csak a kiválasztott hálózatokhoz való hozzáférést teszi lehetővé) [Data Factory felügyelt Virtual Network](managed-virtual-network-private-endpoint.md)-ban való leképezési folyamat használatával.