---
title: Azure-beli adatelőállító létrehozása a Azure Data Factory felhasználói felület használatával
description: Létrehozhat egy adat-előállítót egy olyan folyamattal, amely adatokat másol egy Azure Blob Storage-beli helyről egy másik helyre.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: quickstart
ms.date: 02/25/2020
ms.author: jingwang
ms.openlocfilehash: f7a70454e395f5f9d39266e0777749e1fcbef68e
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/29/2020
ms.locfileid: "81419357"
---
# <a name="quickstart-create-a-data-factory-by-using-the-azure-data-factory-ui"></a>Gyors útmutató: a Azure Data Factory felhasználói felületének használatával létrehozhatja az adatelőállítót

> [!div class="op_single_selector" title1="Válassza ki az Ön által használt Data Factory-szolgáltatás verzióját:"]
> * [1-es verzió](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
> * [Aktuális verzió](quickstart-create-data-factory-portal.md)

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Ez a rövid útmutató ismerteti, hogyan használható az Azure Data Factory felhasználói felülete egy adat-előállító létrehozásához és monitorozásához. Az adat-előállítóban létrehozott folyamat adatokat *másol* az Azure-blobtároló egyik mappájából egy másikba. Az adatok Azure Data Factoryval történő *átalakításának* útmutatásáért olvassa el az [az adatok Spark segítségével történő átalakítását ismertető oktatóanyagot](tutorial-transform-data-spark-portal.md).

> [!NOTE]
> Ha még csak ismerkedik az Azure Data Factory szolgáltatással, a gyors útmutató elvégzése előtt tekintse meg a következő cikket: [Bevezetés az Azure Data Factory használatába](data-factory-introduction.md). 

[!INCLUDE [data-factory-quickstart-prerequisites](../../includes/data-factory-quickstart-prerequisites.md)] 

### <a name="video"></a>Videó 
A következő videó megtekintése segíthet az Azure Data Factory felhasználói felületének megismerésében: 
>[!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Visually-build-pipelines-for-Azure-Data-Factory-v2/Player]

## <a name="create-a-data-factory"></a>Data factory létrehozása

1. Indítsa el a **Microsoft Edge** vagy a **Google Chrome** böngészőt. A Data Factory felhasználói felületének használata jelenleg csak a Microsoft Edge-ben és a Google Chrome-ban támogatott.
1. Lépjen a [Azure Portal](https://portal.azure.com). 
1. Az Azure Portal menüjében válassza az **Erőforrás létrehozása** elemet.
   
   ![Válassza az erőforrás létrehozása lehetőséget Azure Portal menüből](./media/doc-common-process/create-a-resource.png)
1. Válassza az **elemzés**lehetőséget, majd válassza a **Data Factory**lehetőséget. 
   
   ![Data Factory kiválasztása az „Új” ablaktáblán](./media/doc-common-process/new-azure-data-factory-menu.png)
1. Az **Új data factory** lap **Név** mezőjében adja meg az **ADFTutorialDataFactory** értéket. 
 
   Az Azure-beli adatgyár nevének *globálisan egyedinek*kell lennie. Ha a következő hiba jelenik meg, módosítsa az adatgyár nevét (például ** &lt;sajátneve&gt;ADFTutorialDataFactory**), és próbálkozzon újra a létrehozással. A Data Factory-összetevők elnevezési szabályait a [Data Factory elnevezési szabályait](naming-rules.md) ismertető cikkben találja.
  
   ![Hibaüzenet, ha egy név nem érhető el](./media/doc-common-process/name-not-available-error.png)
1. **Előfizetés:** válassza ki azt az Azure-előfizetést, amelyben az adat-előállítót létre szeretné hozni. 
1. **Erőforráscsoport:** hajtsa végre a következő lépések egyikét:
     
   - Kattintson a **Meglévő használata** elemre, majd válasszon egy meglévő erőforráscsoportot a listából. 
   - Válassza az **új létrehozása**lehetőséget, és adja meg az erőforráscsoport nevét.   
         
   Az erőforráscsoportokkal kapcsolatos információkért tekintse meg a [Using resource groups to manage your Azure resources](../azure-resource-manager/management/overview.md) (Erőforráscsoportok használata az Azure-erőforrások kezeléséhez) című cikket.  
1. A **Verzió** résznél válassza a **V2** értéket.
1. **Hely:** válassza ki az adat-előállító helyét.

   A listában csak a Data Factory által támogatott helyek és az Azure Data Factory-metaadatok tárolási helye jelenik meg. A Data Factory által használt adattárak (például az Azure Storage és a Azure SQL Database) és a számítási erőforrások (például az Azure HDInsight) más régiókban is futhatnak.

1. Kattintson a **Létrehozás** gombra. A létrehozás befejezése után válassza az **erőforrás keresése** lehetőséget, és lépjen a **Data Factory** lapra. 

1. Az Azure Data Factory felhasználói felületi (UI) alkalmazás külön lapon történő elindításához kattintson a **Létrehozás és monitorozás** csempére.
   
   ![Az adat-előállító kezdőlapja a „Létrehozás és monitorozás” csempével](./media/doc-common-process/data-factory-home-page.png)
   
   > [!NOTE]
   > Ha úgy látja, hogy a webböngésző az "engedélyezés" gombra kattint, törölje a jelet a **harmadik féltől származó cookie-k és a hely adatvédelme** jelölőnégyzetből. Vagy hagyja bejelölve, hozzon létre egy kivételt a **login.microsoftonline.com**, majd próbálja meg újból megnyitni az alkalmazást.
   
1. Az **Első lépések** lapon váltson a **Szerző** lapra a bal oldali ablaktáblán. 

    ![„Első lépések” lap](./media/doc-common-process/get-started-page-author-button.png)

## <a name="create-a-linked-service"></a>Társított szolgáltatás létrehozása
Ebben az eljárásban létrehoz egy társított szolgáltatást, amely összekapcsolja az Azure Storage-fiókot az adatelőállítóval. A társított szolgáltatás azon kapcsolatadatokkal rendelkezik, amelyeket a Data Factory szolgáltatás használ futtatáskor a hozzá való kapcsolódáshoz.

1. Válassza a **kapcsolatok**lehetőséget, majd az eszköztáron kattintson az **új** gombra (a**kapcsolatok** gomb a bal oldali oszlop alján található a **gyári erőforrások**alatt). 

1. Az **Új társított szolgáltatás** lapon válassza az **Azure Blob Storage** lehetőséget, majd kattintson a **Folytatás** gombra. 

1. Az új társított szolgáltatás (Azure Blob Storage) lapon hajtsa végre a következő lépéseket: 

   a. A **Név** mezőbe írja be az **AzureStorageLinkedService** nevet.

   b. A **Storage-fiók neve**mezőben válassza ki az Azure Storage-fiók nevét.

   c. Kattintson a **Kapcsolat tesztelése** elemre annak ellenőrzéséhez, hogy a Data Factory szolgáltatás kapcsolódik-e a tárfiókhoz. 

   d. A társított szolgáltatás mentéséhez válassza a **Létrehozás** lehetőséget. 

      ![Új társított szolgáltatás](./media/quickstart-create-data-factory-portal/linked-service.png)


## <a name="create-datasets"></a>Adatkészletek létrehozása
A feladat részeként két adatkészletet hoz létre, az **InputDataset** és az **OutputDataset** adatkészletet. Ezek az adatkészletek **AzureBlob** típusúak. Az előző szakaszban létrehozott Azure Storage-beli társított szolgáltatásra hivatkoznak. 

A bemeneti adatkészlet a bemeneti mappában lévő forrásadatokat jelenti. A bemeneti adatkészlet definíciójában adhatja meg a forrásadatokat tartalmazó blobtároló (**adftutorial**), mappa (**input**) és fájl (**emp.txt**) nevét. 

A kimeneti adatkészlet a célhelyre másolt adatokat jelenti. A kimeneti adatkészlet definíciójában adhatja meg annak a blobtárolónak (**adftutorial**), mappának (**output**) és fájlnak a nevét, amelybe az adatok át lesznek másolva. Egy folyamat minden egyes futtatásához egyedi azonosító tartozik. Ehhez az azonosítóhoz a **RunId** rendszerváltozó használatával férhet hozzá. A rendszer a folyamatfuttatási azonosító alapján dinamikusan kiértékeli a kimeneti fájl nevét.   

A társított szolgáltatás beállításaiban a forrásadatokat tartalmazó Azure Storage-fiókot adta meg. A forrásadatkészlet beállításainál megadja a forrásadatok pontos helyét (blobtároló, mappa és fájl). A fogadó adatkészlet beállításainál megadja az adatok másolásának célhelyét (blobtároló, mappa és fájl). 
 
1. Válassza a **+** (plusz) gombot, majd válassza az **adatkészlet**lehetőséget.

   ![Adatkészlet létrehozására szolgáló menü](./media/quickstart-create-data-factory-portal/new-dataset-menu.png)
1. Az **új adatkészlet** lapon válassza az **Azure Blob Storage**lehetőséget, majd kattintson a **Folytatás**gombra. 

1. A **formátum kiválasztása** lapon válassza ki az adatai formátumának típusát, majd kattintson a **Folytatás**gombra. Ebben az esetben válassza a **bináris** elemet, ha a fájlokat a tartalom elemzése nélkül másolja.

   ![Formátum kiválasztása](./media/quickstart-create-data-factory-portal/select-format.png)
   
1. A **készlet tulajdonságai** lapon hajtsa végre a következő lépéseket:

    a. A **név**mezőben adja meg a **InputDataset**. 

    b. **Társított szolgáltatás**: válassza az **AzureStorageLinkedService** értéket.

    c. **Fájl elérési útja**: kattintson a **Tallózás** gombra.

    d. A **fájl vagy mappa kiválasztása** ablakban keresse meg a **bemeneti** mappát az **adftutorial** tárolóban, válassza ki az **EMP. txt** fájlt, majd kattintson az **OK gombra**.
    
    e. Kattintson az **OK** gombra.   

    ![InputDataset tulajdonságainak beállítása](./media/quickstart-create-data-factory-portal/set-properties-for-inputdataset.png)
1. A kimeneti adatkészlet létrehozásához ismételje meg ezeket a lépéseket:  

    a. Válassza a **+** (plusz) gombot, majd válassza az **adatkészlet**lehetőséget.

    b. Az **új adatkészlet** lapon válassza az **Azure Blob Storage**lehetőséget, majd kattintson a **Folytatás**gombra.

    c. A **formátum kiválasztása** lapon válassza ki az adatai formátumának típusát, majd kattintson a **Folytatás**gombra.

    d. A **készlet tulajdonságai** lapon adja meg a név **OutputDataset** . Válassza a **AzureStorageLinkedService** társított szolgáltatásként lehetőséget.

    e. A **fájl elérési útja**területen adja meg az **adftutorial/output**értéket. Ha a **kimeneti** mappa nem létezik, a másolási tevékenység futásidőben hozza létre.

    f. Kattintson az **OK** gombra.   

    ![OutputDataset tulajdonságainak beállítása](./media/quickstart-create-data-factory-portal/set-properties-for-outputdataset.png)
## <a name="create-a-pipeline"></a>Folyamat létrehozása 
A feladat részeként létrehozza és megerősíti azt a másolási tevékenységgel rendelkező folyamatot, amely a bemeneti és a kimeneti adatkészletet használja. A másolási tevékenység adatokat másol a bemeneti adatkészlet beállításaiban megadott fájlból a kimeneti adatkészlet beállításaiban megadott fájlba. Ha a bemeneti adatkészlet csak egy mappát ad meg (de fájlnevet nem), a másolási tevékenység a forrásmappában található összes fájlt átmásolja a célhelyre. 

1. Válassza a **+** (plusz) gombot, majd válassza a **folyamat**elemet. 

1. Az **Általános** lapon a **CopyPipeline****Nevet** adja meg. 

1. A **Tevékenységek** eszközkészletben bontsa ki az **Áthelyezés és átalakítás** elemet. Húzza a **adatok másolása** tevékenységet a **tevékenységek** eszközkészletből a folyamat tervező felületére. A **Tevékenységek** eszközkészletben kereshet is az egyes tevékenységek között. Adja meg a **CopyFromBlobToBlob** értéket a **Név** mezőben.
   ![Adatmásolási tevékenység létrehozása](./media/quickstart-create-data-factory-portal/copy-activity.png)

1. Váltson a **Forrás** lapra a másolási tevékenység beállításainál, és válassza az **InputDataset** lehetőséget a **Forrásadatkészlet** mezőben.

1. Váltson a **Fogadó** lapra a másolási tevékenység beállításainál, és válassza az **OutputDataset** lehetőséget a **Fogadó adatkészlet** mezőben.

1. A folyamat beállításainak érvényesítéséhez a vászon fölött kattintson az **Érvényesítés** elemre a folyamat eszköztárán. Győződjön meg róla, hogy a folyamat érvényesítése sikerült. Az érvényesítési kimenet bezárásához kattintson a **>>** (jobbra mutató nyíl) gombra. 
   ![Folyamat ellenőrzése](./media/quickstart-create-data-factory-portal/pipeline-validate.png)

## <a name="debug-the-pipeline"></a>Hibakeresés a folyamaton
Ebben a lépésben elvégzi a folyamat hibakeresését a Data Factoryban történő üzembe helyezés előtt. 

1. A vászon fölött a folyamat eszköztárán kattintson a **Hibakeresés** lehetőségre egy tesztfuttatás indításához. 
    
1. Győződjön meg arról, hogy látja a folyamatfuttatás állapotát a folyamat beállításainak **Kimenet** lapjának alsó részén. 
 
    ![Folyamat futtatásának kimenete](./media/quickstart-create-data-factory-portal/pipeline-output.png)

1. Győződjön meg róla, hogy a kimeneti fájl látható az **adftutorial** tároló **output** mappájában. Ha a kimeneti mappa nem létezik, a Data Factory szolgáltatás automatikusan létrehozza azt. 

## <a name="trigger-the-pipeline-manually"></a>A folyamat manuális aktiválása
A feladat részeként entitásokat (társított szolgáltatásokat, adatkészleteket és folyamatokat) helyez üzembe az Azure Data Factoryban. Ezután manuálisan fogja aktiválni a folyamat futtatását. 

1. A folyamat aktiválása előtt közzé kell tennie az entitásokat a Data Factory számára. A közzétételhez válassza az **összes közzététele** lehetőséget a felső oldalon. 
    ![Az összes közzététele](./media/quickstart-create-data-factory-portal/publish-all.png)

1. A folyamat manuális elindításához válassza az **trigger hozzáadása** lehetőséget a folyamat eszköztáron, majd válassza az **aktiválás most**lehetőséget. A **folyamat futtatása** lapon válassza a **Befejezés**lehetőséget.

## <a name="monitor-the-pipeline"></a>A folyamat figyelése

1. Váltson a bal oldali **Monitorozás** lapra. A lista frissítéséhez kattintson a **Frissítés** elemre.

   ![A folyamatok figyelésének lapja](./media/quickstart-create-data-factory-portal/monitor-trigger-now-pipeline.png)
1. Válassza ki a **CopyPipeline** hivatkozást, ekkor megjelenik a másolási tevékenység futtatása ezen a lapon. 

1. A másolási művelet részleteinek megtekintéséhez válassza a **részletek** (szemüvegek képe) hivatkozást. A tulajdonságokkal kapcsolatos részletekért tekintse meg a [másolási tevékenység áttekintését](copy-activity-overview.md). 

   ![A másolási művelet részletei](./media/quickstart-create-data-factory-portal/copy-operation-details.png)
1. Győződjön meg arról, hogy megjelenik egy új fájl az **output** mappában. 
1. Váltson vissza a **folyamat** futtatása nézetre a **tevékenység futtatása** nézetből a **minden folyamat futtatása** hivatkozásra kattintva. 

## <a name="trigger-the-pipeline-on-a-schedule"></a>A folyamat aktiválása ütemezés szerint
Az oktatóanyagnak ez a feladata nem kötelező. A folyamat rendszeres időközönként (például óránként, naponta) történő futtatásához létrehozhat egy *ütemező eseményindítót*. A feladat részeként egy olyan eseményindítót fog létrehozni, amely a megadott záró dátumig és időpontig percenként fut. 

1. Váltson a **Szerző** lapra. 

1. Nyissa meg a folyamatot, válassza az **trigger hozzáadása** lehetőséget a folyamat eszköztárán, majd válassza az **új/szerkesztés**lehetőséget. 

1. Az **Eseményindítók hozzáadása** lapon kattintson az **Eseményindító kiválasztása**, majd pedig az **Új** elemre. 

1. Az **új trigger** lapon a **Befejezés**alatt válassza **a dátum**lehetőséget, adja meg az aktuális időpont után néhány perccel későbbi befejezési időt, majd kattintson az **OK gombra**. 

   Minden egyes folyamatfuttatásnak van bizonyos költségvonzata, ezért a befejezés időpontját csak néhány perccel a kezdés időpontja utánra állítsa be. Győződjön meg arról, hogy a két időponthoz tartozó dátum megegyezik. Azonban győződjön meg arról, hogy elegendő idő van a folyamat futtatására a közzétételi idő és a befejezési idő között. Az eseményindító csak a Data Factoryban való közzététel után lesz aktív, a felhasználói felületen történő mentéskor még nem. 

1. Az **új trigger** lapon jelölje be az **aktiválva** jelölőnégyzetet, majd kattintson az **OK gombra**. 

   ![Új trigger-beállítás](./media/quickstart-create-data-factory-portal/trigger-settings-next.png)
1. Tekintse át a figyelmeztető üzenetet, és kattintson **az OK gombra**.

1. Válassza az **összes közzététele** lehetőséget a Data Factory módosításainak közzétételéhez. 

1. Váltson a bal oldali **Monitorozás** lapra. A lista frissítéséhez kattintson a **Frissítés** gombra. Láthatja, hogy a folyamat percenként fut a közzététel időpontja és a befejezés időpontja között. 

   Figyelje meg az **aktivált by** oszlop értékeit. A manuális eseményindító-futtatás egy korábban elvégzett lépésből (**Aktiválás most**) származik. 

1. Váltson a **trigger-futtatások** nézetre. 

1. Győződjön meg arról, hogy a megadott befejezési dátumig és időpontig futtatott minden folyamatfuttatáshoz létrejött egy kimeneti fájl az **output** mappában. 

## <a name="next-steps"></a>További lépések
A példában szereplő folyamat adatokat másol az egyik helyről egy másikra az Azure Blob Storage-ban. A Data Factory más forgatókönyvekben való használatát ismertető további információért tekintse meg az [oktatóanyagokat](tutorial-copy-data-portal.md). 
