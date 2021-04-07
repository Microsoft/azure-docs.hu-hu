---
title: Azure-beli adatelőállító létrehozása a Azure Data Factory felhasználói felület használatával
description: Létrehozhat egy adat-előállítót egy olyan folyamattal, amely adatokat másol egy Azure Blob Storage-beli helyről egy másik helyre.
author: linda33wj
ms.service: data-factory
ms.topic: quickstart
ms.date: 12/14/2020
ms.author: jingwang
ms.openlocfilehash: 8b33005b7f0d08e6da963457813b012199d89f5f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "104655109"
---
# <a name="quickstart-create-a-data-factory-by-using-the-azure-data-factory-ui"></a>Gyorsútmutató: Adat-előállító létrehozása az Azure Data Factory felhasználói felületén 

> [!div class="op_single_selector" title1="Válassza ki az Ön által használt Data Factory-szolgáltatás verzióját:"]
> * [1-es verzió](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
> * [Aktuális verzió](quickstart-create-data-factory-portal.md)

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Ez a rövid útmutató ismerteti, hogyan használható az Azure Data Factory felhasználói felülete egy adat-előállító létrehozásához és monitorozásához. Az adat-előállítóban létrehozott folyamat adatokat *másol* az Azure-blobtároló egyik mappájából egy másikba. Az adatAzure Data Factory használatával történő *átalakításával* kapcsolatban lásd: [az adatforgalom leképezése](concepts-data-flow-overview.md).

> [!NOTE]
> Ha még csak ismerkedik az Azure Data Factory szolgáltatással, a gyors útmutató elvégzése előtt tekintse meg a következő cikket: [Bevezetés az Azure Data Factory használatába](introduction.md). 

[!INCLUDE [data-factory-quickstart-prerequisites](../../includes/data-factory-quickstart-prerequisites.md)] 

### <a name="video"></a>Videó 
A következő videó megtekintése segíthet az Azure Data Factory felhasználói felületének megismerésében: 
>[!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Visually-build-pipelines-for-Azure-Data-Factory-v2/Player]

## <a name="create-a-data-factory"></a>Adat-előállító létrehozása

1. Indítsa el a **Microsoft Edge** vagy a **Google Chrome** böngészőt. A Data Factory felhasználói felületének használata jelenleg csak a Microsoft Edge-ben és a Google Chrome-ban támogatott.
1. Nyissa meg az [Azure Portal](https://portal.azure.com). 
1. Az Azure Portal menüjében válassza az **Erőforrás létrehozása** elemet.
1. Válassza az **integráció** lehetőséget, majd válassza a **Data Factory** lehetőséget. 
   
   :::image type="content" source="./media/doc-common-process/new-azure-data-factory-menu.png" alt-text="Data Factory kijelölés az új ablaktáblán.":::

1. A **Data Factory létrehozása** lap **alapok** lapján válassza ki azt az Azure- **előfizetést** , amelyben létre kívánja hozni az adat-előállítót.
1. **Erőforráscsoport:** hajtsa végre a következő lépések egyikét:

    a. Válasszon ki egy meglévő erőforráscsoportot a legördülő listából.

    b. Válassza az **új létrehozása** lehetőséget, és adja meg egy új erőforráscsoport nevét.
    
    Az erőforráscsoportokkal kapcsolatos információkért tekintse meg az [Erőforráscsoportok használata az Azure-erőforrások kezeléséhez](../azure-resource-manager/management/overview.md) ismertető cikket. 

1. A **régió** mezőben válassza ki az adatelőállító helyét.

   A listában csak a Data Factory által támogatott helyek és az Azure Data Factory-metaadatok tárolási helye jelenik meg. A Data Factory által használt adattárak (például az Azure Storage és a Azure SQL Database) és a számítási erőforrások (például az Azure HDInsight) más régiókban is futhatnak.
 
1. A **név** mezőbe írja be a következőt: **ADFTutorialDataFactory**.
   Az Azure-beli adatgyár nevének *globálisan egyedinek* kell lennie. Ha a következő hiba jelenik meg, módosítsa az adatgyár nevét (például **&lt; sajátneve &gt; ADFTutorialDataFactory**), és próbálkozzon újra a létrehozással. A Data Factory-összetevők elnevezési szabályait a [Data Factory elnevezési szabályait](naming-rules.md) ismertető cikkben találja.

    :::image type="content" source="./media/doc-common-process/name-not-available-error.png" alt-text="Új adatelőállító-hibaüzenet az ismétlődő névhez.":::

1. A **Verzió** résznél válassza a **V2** értéket.

1. Válassza a **Next (tovább): git-konfiguráció** lehetőséget, majd jelölje be a **git későbbi konfigurálása** jelölőnégyzetet.

1. Válassza a **felülvizsgálat + létrehozás** lehetőséget, majd az érvényesítés után válassza a **Létrehozás** lehetőséget. A létrehozás befejezése után válassza az **erőforrás keresése** lehetőséget, és lépjen a **Data Factory** lapra. 

1. Válassza a **szerző & figyelés** csempét, hogy elindítsa a Azure Data Factory felhasználói felületi (UI) alkalmazást egy külön böngésző lapon.
   
    :::image type="content" source="./media/doc-common-process/data-factory-home-page.png" alt-text="A Azure Data Factory kezdőlapja, a szerző & monitor csempével.":::
   
   > [!NOTE]
   > Ha úgy látja, hogy a webböngésző az "engedélyezés" gombra kattint, törölje a jelet a **harmadik féltől származó cookie-k és a hely adatvédelme** jelölőnégyzetből. Vagy hagyja bejelölve, hozzon létre egy kivételt a **login.microsoftonline.com**, majd próbálja meg újból megnyitni az alkalmazást.
   

## <a name="create-a-linked-service"></a>Társított szolgáltatás létrehozása
Ebben az eljárásban létrehoz egy társított szolgáltatást, amely összekapcsolja az Azure Storage-fiókot az adatelőállítóval. A társított szolgáltatás azon kapcsolatadatokkal rendelkezik, amelyeket a Data Factory szolgáltatás használ futtatáskor a hozzá való kapcsolódáshoz.

1. A Azure Data Factory felhasználói felület lapon nyissa meg a [**kezelés**](./author-management-hub.md) fület a bal oldali ablaktáblán.

1. A társított szolgáltatások lapon válassza az **+ új** lehetőséget egy új társított szolgáltatás létrehozásához.

   :::image type="content" source="./media/doc-common-process/new-linked-service.png" alt-text="Új társított szolgáltatás.":::
   
1. Az **Új társított szolgáltatás** lapon válassza az **Azure Blob Storage** lehetőséget, majd kattintson a **Folytatás** gombra. 

1. Az új társított szolgáltatás (Azure Blob Storage) lapon hajtsa végre a következő lépéseket: 

   a. A **Név** mezőbe írja be az **AzureStorageLinkedService** nevet.

   b. A **Storage-fiók neve** mezőben válassza ki az Azure Storage-fiók nevét.

   c. Kattintson a **Kapcsolat tesztelése** elemre annak ellenőrzéséhez, hogy a Data Factory szolgáltatás kapcsolódik-e a tárfiókhoz. 

   d. A társított szolgáltatás mentéséhez válassza a **Létrehozás** lehetőséget. 

      :::image type="content" source="./media/quickstart-create-data-factory-portal/linked-service.png" alt-text="Társított szolgáltatás.":::


## <a name="create-datasets"></a>Adatkészletek létrehozása
A feladat részeként két adatkészletet hoz létre, az **InputDataset** és az **OutputDataset** adatkészletet. Ezek az adatkészletek **AzureBlob** típusúak. Az előző szakaszban létrehozott Azure Storage-beli társított szolgáltatásra hivatkoznak. 

A bemeneti adatkészlet a bemeneti mappában lévő forrásadatokat jelenti. A bemeneti adatkészlet definíciójában adhatja meg a forrásadatokat tartalmazó blobtároló (**adftutorial**), mappa (**input**) és fájl (**emp.txt**) nevét. 

A kimeneti adatkészlet a célhelyre másolt adatokat jelenti. A kimeneti adatkészlet definíciójában adhatja meg annak a blobtárolónak (**adftutorial**), mappának (**output**) és fájlnak a nevét, amelybe az adatok át lesznek másolva. Egy folyamat minden egyes futtatásához egyedi azonosító tartozik. Ehhez az azonosítóhoz a **RunId** rendszerváltozó használatával férhet hozzá. A rendszer a folyamatfuttatási azonosító alapján dinamikusan kiértékeli a kimeneti fájl nevét.   

A társított szolgáltatás beállításaiban a forrásadatokat tartalmazó Azure Storage-fiókot adta meg. A forrásadatkészlet beállításainál megadja a forrásadatok pontos helyét (blobtároló, mappa és fájl). A fogadó adatkészlet beállításainál megadja az adatok másolásának célhelyét (blobtároló, mappa és fájl). 

1. Válassza a **Szerző** fület a bal oldali ablaktáblán.

1. Válassza a **+** (plusz) gombot, majd válassza az **adatkészlet** lehetőséget.

   :::image type="content" source="./media/quickstart-create-data-factory-portal/new-dataset-menu.png" alt-text="Adatkészlet létrehozására szolgáló menü.":::

1. Az **új adatkészlet** lapon válassza az **Azure Blob Storage** lehetőséget, majd kattintson a **Folytatás** gombra. 

1. A **formátum kiválasztása** lapon válassza ki az adatai formátumának típusát, majd kattintson a **Folytatás** gombra. Ebben az esetben válassza a **bináris** elemet, ha a fájlokat a tartalom elemzése nélkül másolja.

   :::image type="content" source="./media/quickstart-create-data-factory-portal/select-format.png" alt-text="Válassza a formátum lehetőséget.":::   
   
1. A **készlet tulajdonságai** lapon hajtsa végre a következő lépéseket:

    a. A **név** mezőben adja meg a **InputDataset**. 

    b. **Társított szolgáltatás**: válassza az **AzureStorageLinkedService** értéket.

    c. **Fájl elérési útja**: kattintson a **Tallózás** gombra.

    d. A **fájl vagy mappa kiválasztása** ablakban keresse meg a **bemeneti** mappát az **adftutorial** tárolóban, válassza ki a **emp.txt** fájlt, majd kattintson az **OK gombra**.
    
    e. Válassza az **OK** lehetőséget.   

    :::image type="content" source="./media/quickstart-create-data-factory-portal/set-properties-for-inputdataset.png" alt-text="InputDataset tulajdonságainak beállítása.":::

1. A kimeneti adatkészlet létrehozásához ismételje meg ezeket a lépéseket:  

    a. Válassza a **+** (plusz) gombot, majd válassza az **adatkészlet** lehetőséget.

    b. Az **új adatkészlet** lapon válassza az **Azure Blob Storage** lehetőséget, majd kattintson a **Folytatás** gombra.

    c. A **formátum kiválasztása** lapon válassza ki az adatai formátumának típusát, majd kattintson a **Folytatás** gombra.

    d. A **készlet tulajdonságai** lapon adja meg a név **OutputDataset** . Válassza a **AzureStorageLinkedService** társított szolgáltatásként lehetőséget.

    e. A **fájl elérési útja** területen adja meg az **adftutorial/output** értéket. Ha a **kimeneti** mappa nem létezik, a másolási tevékenység futásidőben hozza létre.

    f. Válassza az **OK** lehetőséget.   

    :::image type="content" source="./media/quickstart-create-data-factory-portal/set-properties-for-outputdataset.png" alt-text="OutputDataset tulajdonságainak beállítása.":::    

## <a name="create-a-pipeline"></a>Folyamat létrehozása 
A feladat részeként létrehozza és megerősíti azt a másolási tevékenységgel rendelkező folyamatot, amely a bemeneti és a kimeneti adatkészletet használja. A másolási tevékenység adatokat másol a bemeneti adatkészlet beállításaiban megadott fájlból a kimeneti adatkészlet beállításaiban megadott fájlba. Ha a bemeneti adatkészlet csak egy mappát ad meg (de fájlnevet nem), a másolási tevékenység a forrásmappában található összes fájlt átmásolja a célhelyre. 

1. Válassza a **+** (plusz) gombot, majd válassza a **folyamat** elemet. 

1. A **Tulajdonságok** terület általános paneljén adja meg  a CopyPipeline **nevet**. Ezután csukja össze a panelt a jobb felső sarokban található tulajdonságok ikonra kattintva.

1. A **Tevékenységek** eszközkészletben bontsa ki az **Áthelyezés és átalakítás** elemet. Húzza a **adatok másolása** tevékenységet a **tevékenységek** eszközkészletből a folyamat tervező felületére. A **Tevékenységek** eszközkészletben kereshet is az egyes tevékenységek között. Adja meg a **CopyFromBlobToBlob** értéket a **Név** mezőben.

    :::image type="content" source="./media/quickstart-create-data-factory-portal/copy-activity.png" alt-text="Adatmásolási tevékenység létrehozása.":::   

1. Váltson a **Forrás** lapra a másolási tevékenység beállításainál, és válassza az **InputDataset** lehetőséget a **Forrásadatkészlet** mezőben.

1. Váltson a **Fogadó** lapra a másolási tevékenység beállításainál, és válassza az **OutputDataset** lehetőséget a **Fogadó adatkészlet** mezőben.

1. A folyamat beállításainak érvényesítéséhez a vászon fölött kattintson az **Érvényesítés** elemre a folyamat eszköztárán. Győződjön meg róla, hogy a folyamat érvényesítése sikerült. Az érvényesítési kimenet bezárásához kattintson a jobb felső sarokban található Érvényesítés gombra. 

   :::image type="content" source="./media/quickstart-create-data-factory-portal/pipeline-validate.png"   alt-text="Folyamat ellenőrzése.":::

## <a name="debug-the-pipeline"></a>Hibakeresés a folyamaton
Ebben a lépésben elvégzi a folyamat hibakeresését a Data Factoryban történő üzembe helyezés előtt. 

1. A vászon fölött a folyamat eszköztárán kattintson a **Hibakeresés** lehetőségre egy tesztfuttatás indításához. 
    
1. Győződjön meg arról, hogy látja a folyamatfuttatás állapotát a folyamat beállításainak **Kimenet** lapjának alsó részén. 
 
    :::image type="content" source="./media/quickstart-create-data-factory-portal/pipeline-output.png" alt-text="Folyamat futtatásának kimenete":::

1. Győződjön meg róla, hogy a kimeneti fájl látható az **adftutorial** tároló **output** mappájában. Ha a kimeneti mappa nem létezik, a Data Factory szolgáltatás automatikusan létrehozza azt. 

## <a name="trigger-the-pipeline-manually"></a>A folyamat manuális aktiválása
A feladat részeként entitásokat (társított szolgáltatásokat, adatkészleteket és folyamatokat) helyez üzembe az Azure Data Factoryban. Ezután manuálisan fogja aktiválni a folyamat futtatását. 

1. A folyamat aktiválása előtt közzé kell tennie az entitásokat a Data Factory számára. A közzétételhez válassza az **összes közzététele** lehetőséget a felső oldalon. 

    :::image type="content" source="./media/quickstart-create-data-factory-portal/publish-all.png" alt-text="Az összes közzététele.":::    

1. A folyamat manuális elindításához válassza az **trigger hozzáadása** lehetőséget a folyamat eszköztáron, majd válassza az **aktiválás most** lehetőséget. A **folyamat futtatása** lapon kattintson az **OK gombra**.

## <a name="monitor-the-pipeline"></a>A folyamat figyelése

1. Váltson a bal oldali **Monitorozás** lapra. A lista frissítéséhez kattintson a **Frissítés** elemre.

    :::image type="content" source="./media/quickstart-create-data-factory-portal/monitor-trigger-now-pipeline.png" alt-text="A folyamatok figyelésének lapja"::: 
1. Válassza ki a **CopyPipeline** hivatkozást, ekkor megjelenik a másolási tevékenység futtatása ezen a lapon. 

1. A másolási művelet részleteinek megtekintéséhez válassza a **részletek** (szemüvegek képe) hivatkozást. A tulajdonságokkal kapcsolatos részletekért tekintse meg a [másolási tevékenység áttekintését](copy-activity-overview.md). 

   :::image type="content" source="./media/quickstart-create-data-factory-portal/copy-operation-details.png" alt-text="Másolási művelet részletei.":::
1. Győződjön meg arról, hogy megjelenik egy új fájl az **output** mappában. 
1. Váltson vissza a **folyamat** futtatása nézetre a **tevékenység futtatása** nézetből a **minden folyamat futtatása** hivatkozásra kattintva. 

## <a name="trigger-the-pipeline-on-a-schedule"></a>A folyamat aktiválása ütemezés szerint
Az oktatóanyagnak ez a feladata nem kötelező. A folyamat rendszeres időközönként (például óránként, naponta) történő futtatásához létrehozhat egy *ütemező eseményindítót*. A feladat részeként egy olyan eseményindítót fog létrehozni, amely a megadott záró dátumig és időpontig percenként fut. 

1. Váltson a **Szerző** lapra. 

1. Nyissa meg a folyamatot, válassza az **trigger hozzáadása** lehetőséget a folyamat eszköztárán, majd válassza az **új/szerkesztés** lehetőséget. 

1. Az **Eseményindítók hozzáadása** lapon kattintson az **Eseményindító kiválasztása**, majd pedig az **Új** elemre. 

1. Az **új trigger** lapon a **Befejezés** alatt válassza **a dátum** lehetőséget, adja meg az aktuális időpont után néhány perccel későbbi befejezési időt, majd kattintson az **OK gombra**. 

   Minden egyes folyamatfuttatásnak van bizonyos költségvonzata, ezért a befejezés időpontját csak néhány perccel a kezdés időpontja utánra állítsa be. Győződjön meg arról, hogy a két időponthoz tartozó dátum megegyezik. Azonban győződjön meg arról, hogy elegendő idő van a folyamat futtatására a közzétételi idő és a befejezési idő között. Az eseményindító csak a Data Factoryban való közzététel után lesz aktív, a felhasználói felületen történő mentéskor még nem. 

1. Az **új trigger** lapon jelölje be az **aktiválva** jelölőnégyzetet, majd kattintson az **OK gombra**. 

    :::image type="content" source="./media/quickstart-create-data-factory-portal/trigger-settings-next.png" alt-text="Új trigger-beállítás.":::   
1. Tekintse át a figyelmeztető üzenetet, és kattintson **az OK gombra**.

1. Válassza az **összes közzététele** lehetőséget a Data Factory módosításainak közzétételéhez. 

1. Váltson a bal oldali **Monitorozás** lapra. A lista frissítéséhez kattintson a **Frissítés** gombra. Láthatja, hogy a folyamat percenként fut a közzététel időpontja és a befejezés időpontja között. 

   Figyelje meg az **aktivált by** oszlop értékeit. A manuális eseményindító-futtatás egy korábban elvégzett lépésből (**Aktiválás most**) származik. 

1. Váltson a **trigger-futtatások** nézetre. 

1. Győződjön meg arról, hogy a megadott befejezési dátumig és időpontig futtatott minden folyamatfuttatáshoz létrejött egy kimeneti fájl az **output** mappában. 

## <a name="next-steps"></a>Következő lépések
A példában szereplő folyamat adatokat másol az egyik helyről egy másikra az Azure Blob Storage-ban. A Data Factory más forgatókönyvekben való használatát ismertető további információért tekintse meg az [oktatóanyagokat](tutorial-copy-data-portal.md).
