---
title: Adatmásolás az Azure Adatok másolása eszköz használatával
description: Hozzon létre egy Azure Data Factory, majd a Adatok másolása eszközzel másolja át az adatait az Azure Blob Storage egyik helyéről egy másik helyre.
author: dearandyxu
ms.author: yexu
ms.service: data-factory
ms.topic: quickstart
ms.date: 11/09/2020
ms.openlocfilehash: 48624e415649be2fb76aed99c5cb0e1b3b12f04b
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/14/2021
ms.locfileid: "100372858"
---
# <a name="quickstart-use-the-copy-data-tool-to-copy-data"></a>Gyors útmutató: az Adatok másolása eszközzel másolhatja az Adatmásolást

> [!div class="op_single_selector" title1="Válassza ki az Ön által használt Data Factory-szolgáltatás verzióját:"]
> * [1-es verzió](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
> * [Aktuális verzió](quickstart-create-data-factory-copy-data-tool.md)

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Ebben a rövid útmutatóban egy adat-előállítót hoz létre az Azure Portal használatával. Ezután az Adatok másolása eszközzel létrehoz egy folyamatot, amely adatokat másol egy Azure Blob Storage-beli mappából egy másik mappába. 

> [!NOTE]
> Ha még csak ismerkedik az Azure Data Factory szolgáltatással, a gyors útmutató elvégzése előtt tekintse meg a következő cikket: [Bevezetés az Azure Data Factory használatába](introduction.md). 

[!INCLUDE [data-factory-quickstart-prerequisites](../../includes/data-factory-quickstart-prerequisites.md)] 

## <a name="create-a-data-factory"></a>Adat-előállító létrehozása

1. Indítsa el a **Microsoft Edge** vagy a **Google Chrome** böngészőt. A Data Factory felhasználói felületének használata jelenleg csak a Microsoft Edge-ben és a Google Chrome-ban támogatott.
1. Nyissa meg az [Azure Portal](https://portal.azure.com). 
1. A Azure Portal menüben válassza az **erőforrás létrehozása**  >  **integráció**  >  **Data Factory**:

    ![Új adat-előállító létrehozása](./media/doc-common-process/new-azure-data-factory-menu.png)

1. Az **Új data factory** lap **Név** mezőjében adja meg az **ADFTutorialDataFactory** értéket. 
 
   A Azure Data Factory nevének *globálisan egyedinek* kell lennie. Ha a következő hiba jelenik meg, módosítsa az adatgyár nevét (például **&lt; sajátneve &gt; ADFTutorialDataFactory**), és próbálkozzon újra a létrehozással. A Data Factory-összetevők elnevezési szabályait a [Data Factory elnevezési szabályait](naming-rules.md) ismertető cikkben találja.
  
   ![Hibaüzenet, ha egy név nem érhető el](./media/doc-common-process/name-not-available-error.png)
1. **Előfizetés:** válassza ki azt az Azure-előfizetést, amelyben az adat-előállítót létre szeretné hozni. 
1. **Erőforráscsoport:** hajtsa végre a következő lépések egyikét:
     
   - Kattintson a **Meglévő használata** elemre, majd válasszon egy meglévő erőforráscsoportot a listából. 
   - Válassza az **új létrehozása** lehetőséget, és adja meg az erőforráscsoport nevét.   
         
   Az erőforráscsoportokkal kapcsolatos információkért tekintse meg a [Using resource groups to manage your Azure resources](../azure-resource-manager/management/overview.md) (Erőforráscsoportok használata az Azure-erőforrások kezeléséhez) című cikket.  
1. A **Verzió** résznél válassza a **V2** értéket.
1. **Hely:** válassza ki az adat-előállító helyét.

   A listában csak a Data Factory által támogatott helyek és az Azure Data Factory-metaadatok tárolási helye jelenik meg. A Data Factory által használt adattárak (például az Azure Storage és a Azure SQL Database) és a számítási erőforrások (például az Azure HDInsight) más régiókban is futhatnak.

1. Válassza a **Létrehozás** lehetőséget.

1. A létrehozás befejezése után megjelenik a **Data Factory** lap. Az Azure Data Factory felhasználói felületi (UI) alkalmazás külön lapon történő elindításához kattintson a **Létrehozás és monitorozás** csempére.
   
   ![Az adat-előállító kezdőlapja a „Létrehozás és monitorozás” csempével](./media/doc-common-process/data-factory-home-page.png)

## <a name="start-the-copy-data-tool"></a>Az Adatok másolása eszköz elindítása

1. Az Adatok másolása eszköz elindításához az **első lépéseket** ismertető oldalon kattintson az **Adatok másolása** csempére. 

   ![„Adatok másolása” csempe](./media/doc-common-process/get-started-page.png)

1. Az Adatok másolása eszköz **Tulajdonságok** lapján megadhatja a folyamat nevét és leírását, majd kattintson a **Tovább** lehetőségre. 

   ![„Tulajdonságok” lap](./media/quickstart-create-data-factory-copy-data-tool/copy-data-tool-properties-page.png)
1. A **Forrásadattár** oldalon hajtsa végre az alábbi lépéseket:

    a. Kattintson az **+ új kapcsolódás létrehozása** lehetőségre a kapcsolatok hozzáadásához.

    b. Válassza ki azt a társított szolgáltatástípus-típust, amelyet létre szeretne hozni a forrás kapcsolathoz. Ebben az oktatóanyagban az **Azure Blob Storaget** használjuk. Válassza ki a katalógusból, majd kattintson a **Continue (folytatás**) gombra.
    
    ![BLOB kiválasztása](./media/quickstart-create-data-factory-copy-data-tool/select-blob-source.png)

    c. Az **új társított szolgáltatás (Azure Blob Storage)** lapon adja meg a társított szolgáltatás nevét. Válassza ki a Storage-fiók **nevét a Storage-fiók neve** listából, válassza a kapcsolatok, majd a **Létrehozás** lehetőséget. 

    ![Az Azure Blob Storage-fiók konfigurálása](./media/quickstart-create-data-factory-copy-data-tool/configure-blob-storage.png)

    d. Válassza ki az újonnan létrehozott társított szolgáltatást forrásként, majd kattintson a **tovább** gombra.


1. **A bemeneti fájl vagy mappa kiválasztása** lapon kövesse az alábbi lépéseket:

   a. A **Tallózás** gombra kattintva keresse meg a **adftutorial/input** mappát, válassza ki a **emp.txt** fájlt, majd kattintson a **kiválasztás** elemre. 

   d. A **bináris másolás** jelölőnégyzet bejelölésével másolja a fájlt a **következőre**, majd kattintson a Tovább gombra. 

   ![„A bemeneti fájl vagy mappa kiválasztása” lap](./media/quickstart-create-data-factory-copy-data-tool/select-binary-copy.png)


1. A **cél adattár** lapon válassza ki a létrehozott **Azure Blob Storage** társított szolgáltatást, majd kattintson a **tovább** gombra. 

1. A **kimeneti fájl vagy mappa kiválasztása** lapon adja meg a mappa elérési útjának **adftutorial/kimenetét** , majd kattintson a **tovább** gombra. 

   ![„A kimeneti fájl vagy mappa kiválasztása” lap](./media/quickstart-create-data-factory-copy-data-tool/configure-sink-path.png) 

1. A **Beállítások** lapon kattintson a **Tovább** gombra az alapértelmezett konfigurációk használatához. 

1. Tekintse át a beállításokat az **Összegzés** lapon, majd válassza a **Tovább** gombot. 

1. Az **Üzembe helyezés kész** lapon kattintson a **Monitorozás** elemre a létrehozott folyamat monitorozásához. 

    ![„Üzembe helyezés kész” lap](./media/quickstart-create-data-factory-copy-data-tool/deployment-page.png)

1. Az alkalmazás a **figyelő** lapra vált. Ezen a lapon látható a folyamat állapota. A lista frissítéséhez kattintson a **frissítés** gombra. Kattintson a **folyamat neve** alatt látható hivatkozásra a tevékenység futtatási részleteinek megtekintéséhez vagy a folyamat újrafuttatásához. 
   
    ![Folyamat frissítése](./media/quickstart-create-data-factory-copy-data-tool/refresh-pipeline.png)

1. A másolási művelettel kapcsolatos további információkért a tevékenység futtatása lapon válassza a **részletek** hivatkozást (szemüveg ikon) a **tevékenység neve** oszlopban. A tulajdonságokkal kapcsolatos részletekért tekintse meg a [másolási tevékenység áttekintését](copy-activity-overview.md). 

1. Ha vissza szeretne térni a folyamat futási nézetéhez, válassza a **minden folyamat futtatása** hivatkozást a navigációs menüben. A nézet frissítéséhez válassza a **Frissítés** parancsot. 

1. Ellenőrizze, hogy az **emp.txt** fájl az **adftutorial** tároló **output** mappájában jött-e létre. Ha a kimeneti mappa nem létezik, a Data Factory szolgáltatás automatikusan létrehozza azt. 

1. A kapcsolódó szolgáltatások, adatkészletek és folyamatok szerkesztéséhez lépjen a bal oldali panelen a **Monitorozás** lap fölötti **Szerző** lapra. Annak megismeréséhez, hogy ezek hogyan szerkeszthetőek a Data Factory felhasználói felületén, olvassa el az [adat-előállító az Azure Portal használatával történő létrehozását](quickstart-create-data-factory-portal.md) ismertető cikket.

    ![Szerző lap kiválasztása](./media/quickstart-create-data-factory-copy-data-tool/select-author.png)

## <a name="next-steps"></a>Következő lépések
A példában szereplő folyamat adatokat másol az egyik helyről egy másikra az Azure Blob Storage-ban. A Data Factory más forgatókönyvekben való használatát ismertető további információért tekintse meg az [oktatóanyagokat](tutorial-copy-data-portal.md). 
