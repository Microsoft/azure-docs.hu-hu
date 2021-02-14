---
title: Az új fájlok növekményes másolása az idő particionált fájlneve alapján
description: Hozzon létre egy Azure-beli adatelőállítót, majd a Adatok másolása eszközzel fokozatosan töltse be az új fájlokat csak az idő particionált fájlnév alapján.
author: dearandyxu
ms.author: yexu
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 11/09/2020
ms.openlocfilehash: 8e68852434a4a8bea43b575523a60c9346b2a569
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/14/2021
ms.locfileid: "100384775"
---
# <a name="incrementally-copy-new-files-based-on-time-partitioned-file-name-by-using-the-copy-data-tool"></a>Az új fájlok növekményes másolása a Adatok másolása eszköz használatával, az időparticionált fájl neve alapján

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Ebben az oktatóanyagban egy adat-előállítót hoz létre az Azure Portal használatával. Ezt követően a Adatok másolása eszközzel létrehozhat egy folyamatot, amely Növekményesen átmásolja az új fájlokat az Azure Blob Storage-ból az Azure Blob Storage-ba történő időpartíciós fájlnév alapján.

> [!NOTE]
> Ha még csak ismerkedik az Azure Data Factory használatával, olvassa el [az Azure Data Factory használatának első lépéseit](introduction.md) ismertető cikket.

Az oktatóanyagban az alábbi lépéseket fogja végrehajtani:

> [!div class="checklist"]
> * Adat-előállító létrehozása
> * Folyamat létrehozása az Adatok másolása eszközzel.
> * A folyamat és a tevékenységek futásának monitorozása

## <a name="prerequisites"></a>Előfeltételek

* **Azure-előfizetés**: Ha nem rendelkezik Azure-előfizetéssel, első lépésként mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/).
* **Azure Storage** _-_ fiók: a blob Storage használata _forrásként_ és fogadó adattárként. Ha még nem rendelkezik Azure Storage-fiókkal, a szükséges utasításokat a [Storage-fiók létrehozását](../storage/common/storage-account-create.md) ismertető cikkben találja.

### <a name="create-two-containers-in-blob-storage"></a>Két tároló létrehozása a blob Storage-ban

Készítse elő a blob Storage-t az oktatóanyaghoz az alábbi lépések végrehajtásával.

1. Hozzon létre egy **forrás** nevű tárolót.  Hozzon létre egy mappa elérési útját **2020/03/17/03** -ként a tárolóban. Hozzon létre egy üres szövegfájlt, és nevezze el **file1.txtként**. Töltse fel a file1.txtt a Storage-fiókban a mappa elérési útja **forrás/2020/03/17/03** mappájába.  Ennek elvégzéséhez különböző eszközök állnak rendelkezésére, például az [Azure Storage Explorer](https://storageexplorer.com/).

    ![fájlok feltöltése](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/upload-file.png)

    > [!NOTE]
    > Állítsa be a mappa nevét az UTC időpontra.  Ha például az aktuális UTC-idő 3:38 március 17-én, 2020-kor, a mappa elérési útját a forrás **/2020/03/17/03/** a **forrás/{év}/{hónap}/{nap}/{Hour}/** értékkel hozza létre.

2. Hozzon létre egy **célhely** nevű tárolót. Ennek elvégzéséhez különböző eszközök állnak rendelkezésére, például az [Azure Storage Explorer](https://storageexplorer.com/).

## <a name="create-a-data-factory"></a>Adat-előállító létrehozása

1. A bal oldali menüben válassza az **erőforrás**-  >  **integráció** létrehozása  >  **Data Factory**:

   ![Data Factory kiválasztása az „Új” ablaktáblán](./media/doc-common-process/new-azure-data-factory-menu.png)

2. Az **Új adat-előállító** lap **Név** mezőjében adja meg az **ADFTutorialDataFactory** értéket.

    Az adat-előállító nevének _globálisan egyedinek_ kell lennie. A következő hibaüzenet jelenhet meg:

   ![Új adat-előállító hibaüzenete](./media/doc-common-process/name-not-available-error.png)

   Ha a név értékével kapcsolatos hibaüzenet kap, adjon meg másik nevet az adat-előállítóhoz. Például: _**sajátneve**_**ADFTutorialDataFactory**. A Data Factory-összetevők elnevezési szabályait a [Data Factory elnevezési szabályait](naming-rules.md) ismertető cikkben találja.
3. Válassza ki az Azure-**előfizetést** az új adat-előállító létrehozásához.
4. **Erőforráscsoport:** hajtsa végre a következő lépések egyikét:

    a. Válassza a **meglévő használata** lehetőséget, majd válasszon ki egy meglévő erőforráscsoportot a legördülő listából.

    b. Válassza az **új létrehozása** lehetőséget, és adja meg az erőforráscsoport nevét. 
         
    Az erőforráscsoportokkal kapcsolatos információkért tekintse meg az [Erőforráscsoportok használata az Azure-erőforrások kezeléséhez](../azure-resource-manager/management/overview.md) ismertető cikket.

5. A **Verzió** résznél válassza a **V2** értéket.
6. A **Hely** alatt válassza ki az adat-előállító helyét. A legördülő listán csak a támogatott helyek jelennek meg. Az adat-előállítók által használt adattárak (például Azure Storage és SQL Database) és számítási erőforrások (például Azure HDInsight) más helyeken/régiókban is lehetnek.
7. Válassza a **Létrehozás** lehetőséget.
8. A létrehozás befejezése után a **Data Factory** kezdőlapja jelenik meg.
9. Az Azure Data Factory felhasználói felületének (UI) külön lapon történő megnyitásához kattintson a **Tartalomkészítés és monitorozás** csempére.

    ![Data factory kezdőlap](./media/doc-common-process/data-factory-home-page.png)


## <a name="use-the-copy-data-tool-to-create-a-pipeline"></a>Folyamat létrehozása az Adatok másolása eszközzel

1. Az **első lépések** lapon válassza ki a **Adatok másolása** címet a adatok másolása eszköz elindításához.

   ![Az Adatok másolása eszköz csempéje](./media/doc-common-process/get-started-page.png)

2. A **Tulajdonságok** oldalon hajtsa végre a következő lépéseket:

    a. A **feladat neve** alatt adja meg a **DeltaCopyFromBlobPipeline**.

    b. A **feladat lépésszám vagy a feladat ütemezés** alatt válassza **a rendszeres Futtatás ütemezés** szerint lehetőséget.

    c. Az **trigger típusa** területen válassza a **kiesési ablak** lehetőséget.

    d. Az **Ismétlődés** alatt adja meg az **1 óra** értéket.

    e. Kattintson a **Tovább** gombra.

    A Data Factory felhasználói felülete létrehoz egy folyamatot a megadott feladatnéven.

    ![Tulajdonságok lap](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/copy-data-tool-properties-page.png)
3. A **Forrásadattár** oldalon hajtsa végre az alábbi lépéseket:

    a. Kapcsolat hozzáadásához kattintson a **+ Új kapcsolat létrehozása** lehetőségre
    
    b. Válassza ki az Azure Blob Storage elemet a katalógusból, majd válassza a Folytatás lehetőséget.
    
    c. Az **új társított szolgáltatás (Azure Blob Storage)** lapon adja meg a társított szolgáltatás nevét. Válassza ki az Azure-előfizetését, és válassza ki a Storage-fiók **nevét a Storage-fiók neve** listából. Tesztelje a kapcsolatokat, majd válassza a **Létrehozás** lehetőséget.

    ![Forrásadattár lap](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/source-data-store-page-linkedservice.png)

    d. Válassza ki az újonnan létrehozott társított szolgáltatást a **forrás adattár** lapon, majd kattintson a **tovább** gombra.

4. **A bemeneti fájl vagy mappa kiválasztása** oldalon kövesse az alábbi lépéseket:

    a. Tallózással keresse meg és válassza ki a **forrás** tárolót, majd válassza **a kiválasztás lehetőséget.**

    ![A képernyőfelvételen a bemeneti fájl vagy mappa kiválasztása párbeszédpanel jelenik meg.](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/choose-input-file-folder.png)

    b. A **fájl betöltése viselkedés** területen válassza a **növekményes betöltés: idő particionált mappák/fájlnevek** lehetőséget.

    c. Írja be a dinamikus mappa elérési útját **forrás/{év}/{hónap}/{nap}/{Hour}/**, és módosítsa a formátumot a következő képernyőképen látható módon. Győződjön meg a **bináris másolásról** , majd kattintson a **tovább** gombra.

    ![Képernyőfelvétel: a bemeneti fájl vagy mappa kiválasztása párbeszédpanel, ahol a kiválasztott mappa található.](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/check-binary-copy.png)     

5. A **cél adattár** lapon válassza ki a **AzureBlobStorage**, amely ugyanaz a Storage-fiók, mint az adatforrás-tároló, majd kattintson a **tovább** gombra.

    ![Céladattár lap](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/destination-data-store-page-select-linkedservice.png)
6. A **kimeneti fájl vagy mappa kiválasztása** lapon hajtsa végre a következő lépéseket:

    a. Tallózással keresse meg és jelölje ki a **célmappát** , majd kattintson a **kiválasztás** elemre.

    b. Írja be a dinamikus mappa elérési útját **célként/{Year}/{hónap}/{Day}/{Hour}/**, és módosítsa a formátumot az alábbiak szerint:

    ![A képernyőképen a kimeneti fájl vagy mappa kiválasztása párbeszédpanel jelenik meg.](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/output-file-name.png)

    c. Kattintson a **Tovább** gombra.

    ![Képernyőfelvétel: a kimeneti fájl vagy mappa kiválasztása párbeszédpanel a következő kijelöléssel.](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/click-next-after-output-folder.png)
7. A **Beállítások** lapon kattintson a **Tovább** gombra.

8. Az **Összefoglalás** lapon tekintse át a beállításokat, majd kattintson a **Tovább** gombra.

    ![Összefoglaló lap](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/summary-page.png)

9. A folyamat (feladat) figyeléséhez az **Üzembe helyezés** lapon kattintson a **Monitorozás** elemre.
    ![Üzembe helyezés lap](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/deployment-page.png)

10. Figyelje meg, hogy a bal oldalon található **Figyelés** lap automatikusan ki lesz választva.  Várnia kell, amíg a folyamat automatikusan elindul (körülbelül egy óra elteltével). Ha fut, kattintson a folyamat neve hivatkozásra **DeltaCopyFromBlobPipeline** a tevékenység futtatási részleteinek megtekintéséhez vagy a folyamat újrafuttatásához. A lista frissítéséhez kattintson a **Frissítés** gombra.

    ![Képernyőfelvétel: a folyamat futtatási paneljének megjelenítése.](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/monitor-pipeline-runs-1.png)
11. Csak egy (másolási) tevékenység található a folyamatban, ezért csak egy bejegyzést lát. Módosítsa a **forrás** -és a **célhely** oszlopának szélességét (ha szükséges) a további részletek megjelenítéséhez. a forrásfájl (file1.txt) a forrás  */2020/03/17/03* / *cél/2020/03/17/* 03/verzióról a fájlnévre lett másolva. 

    ![Képernyőfelvétel: a folyamat futtatásának részletei.](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/monitor-pipeline-runs2.png)

    Ugyanezt a Azure Storage Explorer használatával is ellenőrizheti (a https://storageexplorer.com/) fájlok vizsgálatához.

    ![A képernyőképen a folyamat futási adatai láthatók a célhelyen.](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/monitor-pipeline-runs3.png)

12. Hozzon létre egy másik üres szövegfájlt az új névvel **file2.txtként**. Töltse fel a file2.txt fájlt a Storage-fiókjában található, a következő elérési útra: **forrás/2020/03/17/04** . Ennek elvégzéséhez különböző eszközök állnak rendelkezésére, például az [Azure Storage Explorer](https://storageexplorer.com/).

    > [!NOTE]
    > Előfordulhat, hogy az új mappa elérési útjának létrehozására van szükség. Állítsa be a mappa nevét az UTC időpontra.  Ha például az aktuális UTC-idő 4:20-kor, 2020-kor, akkor a mappa elérési útját a következő szabály alapján hozhatja létre: **forrás/2020/03/17/04/** a (z) **{Year}/{month}/{Day}/{Hour}/**.

13. Ha vissza szeretne térni a **folyamat futási** nézetéhez, válassza a **minden folyamat fut** lehetőséget, és várjon, amíg egy másik óra elteltével a folyamat automatikusan újraindul.  

    ![Képernyőfelvétel: az összes folyamat futtatási hivatkozása, amely a laphoz való visszatérésre mutat.](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/monitor-pipeline-runs5.png)

14. Válassza ki a második folyamathoz tartozó új **DeltaCopyFromBlobPipeline** -hivatkozást, amikor eljön, és végezze el a részletek áttekintését. Látni fogja, hogy a forrásfájl (file2.txt) a  **forrás/2020/03/17/04/** **cél/2020/03/17/04** /pontról lett átmásolva ugyanazzal a fájlnévvel. Ugyanezt a Azure Storage Explorer használatával is ellenőrizheti (a https://storageexplorer.com/) tárolóban lévő fájlok vizsgálatához  .


## <a name="next-steps"></a>Következő lépések
Folytassa a következő oktatóanyaggal, amelyben az adatok Azure Spark-fürtök használatával való átalakítását ismerheti meg:

> [!div class="nextstepaction"]
>[Adatok átalakítása a felhőben a Spark használatával](tutorial-transform-data-spark-portal.md)
