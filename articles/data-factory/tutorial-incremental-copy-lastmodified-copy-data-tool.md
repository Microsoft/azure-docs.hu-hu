---
title: Adateszköz az új és frissített fájlok növekményes másolásához
description: Hozzon létre egy Azure-beli adatelőállítót, majd használja az Adatok másolása eszközt az új fájlok LastModifiedDate-alapú növekményes betöltéséhez.
author: dearandyxu
ms.author: yexu
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 02/18/2021
ms.openlocfilehash: 25e39d3fc9c56a282714bafb630fee65421fb5ac
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "104606667"
---
# <a name="incrementally-copy-new-and-changed-files-based-on-lastmodifieddate-by-using-the-copy-data-tool"></a>Új és módosított fájlok növekményes másolása LastModifiedDate alapján a Adatok másolása eszköz használatával

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Ebben az oktatóanyagban a Azure Portal használatával hozzon létre egy adatelőállítót. Ezután a Adatok másolása eszköz használatával létrehozhat egy folyamatot, amely fokozatosan átmásolja az új és módosított fájlokat, az Azure Blob Storage-ból az Azure Blob Storage-ba. A használatával `LastModifiedDate` határozza meg a másolandó fájlokat.

Miután elvégezte a lépéseket, Azure Data Factory megvizsgálja a forrás-tárolóban lévő összes fájlt, alkalmazza a fájl szűrőjét `LastModifiedDate` , és a célhelyre másolja a fájlokat, amelyek a legutóbbi időpont óta újak vagy frissültek. Vegye figyelembe, hogy ha Data Factory nagy mennyiségű fájlt vizsgál, továbbra is hosszú időtartamot kell várnia. A fájlok vizsgálata időigényes, még akkor is, ha a másolt adatmennyiség csökken.

> [!NOTE]
> Ha még csak ismerkedik a Data Factory használatával, olvassa el [az Azure Data Factory használatának első lépéseit](introduction.md) ismertető cikket.

Ebben az oktatóanyagban a következő feladatokat hajtja végre:

> [!div class="checklist"]
> * Adat-előállító létrehozása
> * Folyamat létrehozása az Adatok másolása eszközzel.
> * A folyamat és a tevékenységek futásának monitorozása

## <a name="prerequisites"></a>Előfeltételek

* **Azure-előfizetés**: Ha nem rendelkezik Azure-előfizetéssel, első lépésként mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/).
* **Azure Storage-fiók**: használjon blob Storage-t a forrás-és fogadó adattárakhoz. Ha nem rendelkezik Azure Storage-fiókkal, kövesse a Storage- [fiók létrehozása](../storage/common/storage-account-create.md)című témakör utasításait.

## <a name="create-two-containers-in-blob-storage"></a>Két tároló létrehozása a blob Storage-ban

Készítse elő a blob Storage-t az oktatóanyaghoz a következő lépések elvégzésével:

1. Hozzon létre egy **forrás** nevű tárolót. A feladat elvégzéséhez különböző eszközöket használhat, például [Azure Storage Explorer](https://storageexplorer.com/).

2. Hozzon létre egy **célhely** nevű tárolót.

## <a name="create-a-data-factory"></a>Adat-előállító létrehozása

1. A bal oldali panelen válassza az **Erőforrás létrehozása** elemet. **Integrációs**  >  **Data Factory** kiválasztása:

   ![Data Factory kiválasztása](./media/doc-common-process/new-azure-data-factory-menu.png)

2. Az **Új adat-előállító** lap **Név** mezőjében adja meg az **ADFTutorialDataFactory** értéket.

   Az adat-előállító nevének globálisan egyedinek kell lennie. A következő hibaüzenet jelenhet meg:

    :::image type="content" source="./media/doc-common-process/name-not-available-error.png" alt-text="Új adatelőállító-hibaüzenet az ismétlődő névhez.":::

   Ha a név értékével kapcsolatos hibaüzenet kap, adjon meg másik nevet az adat-előállítóhoz. Például: _**sajátneve**_**ADFTutorialDataFactory**. A Data Factory-összetevők elnevezési szabályait a [Data Factory elnevezési szabályait](naming-rules.md) ismertető cikkben találja.
3. Az **előfizetés** területen válassza ki azt az Azure-előfizetést, amelyben létre kívánja hozni az új adat-előállítót.
4. Az **erőforráscsoport** területen hajtsa végre az alábbi lépések egyikét:

    * Válassza a **meglévő használata** lehetőséget, majd válasszon ki egy meglévő erőforráscsoportot a listában.

    * Válassza az **új létrehozása** lehetőséget, majd adja meg az erőforráscsoport nevét.
         
    Az erőforráscsoportokkal kapcsolatos információkért tekintse meg az [Erőforráscsoportok használata az Azure-erőforrások kezeléséhez](../azure-resource-manager/management/overview.md) ismertető cikket.

5. A **Verzió** résznél válassza a **V2** értéket.
6. A **hely** területen válassza ki az adat-előállító helyét. A listában csak a támogatott helyszínek jelennek meg. Az adattárolók (például az Azure Storage és a Azure SQL Database) és a számítási erőforrások (például az Azure HDInsight) más helyszíneken és régiókban is lehetnek.
8. Válassza a **Létrehozás** lehetőséget.
9. Az adatelőállító létrehozása után megjelenik a The Factory kezdőlapja.
10. A Azure Data Factory felhasználói felületének (UI) külön lapon való megnyitásához válassza a **szerző & monitor** csempe:

    :::image type="content" source="./media/doc-common-process/data-factory-home-page.png" alt-text="A Azure Data Factory kezdőlapja, a szerző & monitor csempével.":::

## <a name="use-the-copy-data-tool-to-create-a-pipeline"></a>Folyamat létrehozása az Adatok másolása eszközzel

1. Az **első lépések** oldalon válassza a **adatok másolása** csempét a adatok másolása eszköz megnyitásához:

   ![Adatok másolása csempe](./media/doc-common-process/get-started-page.png)

2. A **Tulajdonságok** oldalon hajtsa végre a következő lépéseket:

    a. A **feladat neve** alatt adja meg a **DeltaCopyFromBlobPipeline**.

    b. A **feladat lépésszám vagy a feladat ütemezés** alatt válassza **a rendszeres Futtatás ütemezés** szerint lehetőséget.

    c. Az **trigger típusa** területen válassza a **kiesési ablak** lehetőséget.

    d. Az **Ismétlődés** alatt adja meg a **15 percet**.

    e. Kattintson a **Tovább** gombra.

    Data Factory létrehoz egy folyamatot a megadott feladathoz tartozó névvel.

    ![Adatmásolás tulajdonságai lap](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/copy-data-tool-properties-page.png)

3. A **forrás adattár** oldalon hajtsa végre a következő lépéseket:

    a. A kapcsolatok hozzáadásához válassza az  **új kapcsolatok létrehozása** lehetőséget.

    b. Válassza ki az **Azure Blob Storage** elemet a katalógusból, majd válassza a **Folytatás** lehetőséget:

    ![Azure blog Storage kiválasztása](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/source-data-store-page-select-blob.png)

    c. Az **új társított szolgáltatás (Azure Blob Storage)** lapon válassza ki a Storage-fiók nevét a **Storage-fiók neve** listából. Tesztelje a kapcsolatokat, majd válassza a **Létrehozás** lehetőséget.

    d. Válassza ki az új társított szolgáltatást, majd válassza a **Next (tovább**) gombot:

   ![Válassza ki az új társított szolgáltatást](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/source-data-store-page-select-linkedservice.png)

4. **A bemeneti fájl vagy mappa kiválasztása** lapon kövesse az alábbi lépéseket:

    a. Keresse meg és válassza ki a **forrás** mappát, majd válassza **a kiválasztás lehetőséget.**

    ![A bemeneti fájl vagy mappa kiválasztása](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/choose-input-file-folder.png)

    b. A **fájl betöltése viselkedés** területen válassza a **növekményes betöltés: LastModifiedDate** lehetőséget.

    c. Válassza a **bináris másolás** lehetőséget, majd válassza a **Next (tovább**) gombot:

     ![Válassza ki a bemeneti fájl vagy mappa lapot.](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/check-binary-copy.png)

5. A **cél adattár** lapon válassza ki a létrehozott **AzureBlobStorage** -szolgáltatást. Ez ugyanaz a Storage-fiók, mint a forrás-adattár. Ezután kattintson a **Tovább** gombra.

6. **A kimeneti fájl vagy mappa kiválasztása** lapon kövesse az alábbi lépéseket:

    a. Keresse meg és jelölje ki a **célmappát** , majd válassza **a kiválasztás lehetőséget:**

    ![Válassza ki a kimeneti fájl vagy mappa lapot](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/choose-output-file-folder.png)

    b. Kattintson a **Tovább** gombra.

7. A **Beállítások** lapon kattintson a **Tovább** gombra.

8. Az **Összefoglalás** lapon tekintse át a beállításokat, majd kattintson a **tovább** gombra.

    ![Összefoglaló lap](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/summary-page.png)

9. A folyamat (feladat) figyeléséhez az **Üzembe helyezés** lapon kattintson a **Monitorozás** elemre.

    ![Üzembe helyezés lap](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/deployment-page.png)

10. Figyelje meg, hogy a bal oldalon található **Figyelés** lap automatikusan ki lesz választva. Az alkalmazás a **figyelő** lapra vált. Megjelenik a folyamat állapota. A lista frissítéséhez kattintson a **Frissítés** gombra. Válassza a **folyamat neve** alatt látható hivatkozást a tevékenység futtatási részleteinek megtekintéséhez vagy a folyamat újbóli futtatásához.

    ![A lista frissítése és a tevékenység futtatási részleteinek megtekintése](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs-1.png)

11. Csak egy tevékenység (másolási tevékenység) található a folyamatban, így csak egy bejegyzés jelenik meg. A másolási művelet részleteinek megtekintéséhez válassza a **tevékenység neve** oszlopban a **részletek** hivatkozást (a szemüveg ikont). A tulajdonságokkal kapcsolatos részletekért tekintse meg a [másolási tevékenység áttekintése](copy-activity-overview.md)című témakört.

    ![Másolási tevékenység a folyamatban](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs2.png)

    Mivel a blob Storage-fiókban nincsenek fájlok a forrás tárolóban, nem jelenik meg a fiókban a cél tárolóba másolt fájlok:

    ![Nincsenek fájlok a forrás tárolóban vagy a tárolóban](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs3.png)

12. Hozzon létre egy üres szövegfájlt, és nevezze el **file1.txt**. Töltse fel ezt a szövegfájlt a Storage-fiókban található forrás tárolóba. Ezeket a feladatokat különböző eszközökkel hajthatja végre, például [Azure Storage Explorer](https://storageexplorer.com/).

    ![file1.txt létrehozása és feltöltése a forrás-tárolóba](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs3-1.png)

13. Ha vissza szeretne térni a **folyamat futási** nézetéhez, válassza a **minden folyamat futtatása** lehetőséget, és várja meg, amíg a folyamat automatikusan aktiválódik.  

14. A második folyamat futásának befejeződése után kövesse a korábban megemlített lépéseket a tevékenység futtatási részleteinek áttekintéséhez.  

    Látni fogja, hogy egy fájl (file1.txt) át lett másolva a forrás-tárolóból a blob Storage-fiók cél tárolójába:

    ![A rendszer átmásolta a file1.txt a forrás tárolójából a cél tárolóba.](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs6.png)

15. Hozzon létre egy másik üres szövegfájlt, és nevezze el **file2.txt**. Töltse fel ezt a szövegfájlt a blob Storage-fiókban található forrás tárolóba.

16. Ismételje meg a 13. és a 14. lépést a második szövegfájlban. Láthatja, hogy a folyamat futása során csak az új fájlt (file2.txt) másolta a rendszer a forrás tárolójából a Storage-fiók célhelyére.  

    Azt is megteheti, hogy csak egy fájlt másolt [Azure Storage Explorer](https://storageexplorer.com/) használatával a fájlok vizsgálatához:

    ![Fájlok vizsgálata Azure Storage Explorer használatával](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs8.png)


## <a name="next-steps"></a>Következő lépések
Az alábbi oktatóanyagból megtudhatja, hogyan alakíthat át adatátalakítást egy Apache Spark-fürt használatával az Azure-ban:

> [!div class="nextstepaction"]
>[A felhőben tárolt adatátalakítás Apache Spark fürt használatával](tutorial-transform-data-spark-portal.md)
