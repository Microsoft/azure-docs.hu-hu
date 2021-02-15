---
title: Betöltés az Azure Data Lake Storage Gen1ba
description: Az Adatmásolás Azure Data Factory használatával Azure Data Lake Storage Gen1
ms.author: jingwang
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 01/17/2018
ms.openlocfilehash: 2d307a279bee56440f7354ad2c92664fd2af86b9
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/14/2021
ms.locfileid: "100370767"
---
# <a name="load-data-into-azure-data-lake-storage-gen1-by-using-azure-data-factory"></a>Betöltés az Azure Data Lake Storage Gen1ba Azure Data Factory használatával

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

A [Azure Data Lake Storage Gen1](../data-lake-store/data-lake-store-overview.md) (korábbi nevén Azure Data Lake Store) egy nagyvállalati szintű Hyper-Scale tárház az Big Data analitikus számítási feladatokhoz. Data Lake Storage Gen1 segítségével bármilyen méretű, típusú és betöltési sebességgel rögzítheti az adatmennyiséget. Az adatrögzítés egyetlen helyen történik az operatív és a felderítő elemzéshez.

A Azure Data Factory egy teljes körűen felügyelt felhőalapú adatintegrációs szolgáltatás. A szolgáltatás segítségével feltöltheti a tavat a meglévő rendszerből származó adatokkal, és időt takaríthat meg az elemzési megoldások létrehozásakor.

Azure Data Factory a következő előnyöket biztosítja az adatData Lake Storage Gen1ba való betöltéshez:

* **Egyszerűen beállítható**: egy intuitív 5 lépésből álló varázsló, amely nem igényel parancsfájlt.
* **Gazdag adattár-támogatás**: beépített támogatás a helyszíni és felhőalapú adattárak gazdag készletéhez. Részletes listát a [támogatott adattárakkal](copy-activity-overview.md#supported-data-stores-and-formats)foglalkozó táblázatban talál.
* **Biztonságos és megfelelő**: az adatátvitel HTTPS-vagy ExpressRoute-kapcsolaton keresztül történik. A globális szolgáltatás jelenléte biztosítja, hogy az adatai soha nem hagyják el a földrajzi határt.
* **Nagy teljesítményű**: akár 1 GB/s betöltési sebesség a Data Lake Storage Gen1ba. Részletekért lásd: [másolási tevékenység teljesítménye](copy-activity-performance.md).

Ez a cikk bemutatja, hogyan tölthetők be a Data Factory Adatok másolása eszközzel az _Amazon S3-ból származó adatok Data Lake Storage Gen1ba való betöltéséhez_. Az adatok más típusú adattárakból történő másolásához hasonló lépéseket kell követnie.

> [!NOTE]
> További információ: [adatok másolása Data Lake Storage Gen1ba vagy a Azure Data Factory használatával](connector-azure-data-lake-store.md).

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés: Ha nem rendelkezik Azure-előfizetéssel, a Kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/) .
* Data Lake Storage Gen1 fiók: Ha nem rendelkezik Data Lake Storage Gen1-fiókkal, tekintse meg a [Data Lake Storage Gen1 fiók létrehozása](../data-lake-store/data-lake-store-get-started-portal.md#create-a-data-lake-storage-gen1-account)című témakör utasításait.
* Amazon S3: Ez a cikk bemutatja, hogyan másolhat adatok az Amazon S3-ból. A hasonló lépéseket követve más adattárakat is használhat.

## <a name="create-a-data-factory"></a>Adat-előállító létrehozása

1. A bal oldali menüben válassza az **erőforrás létrehozása**  >  **elemzési**  >  **Data Factory**:
   
   ![Data Factory kiválasztása az „Új” ablaktáblán](./media/quickstart-create-data-factory-portal/new-azure-data-factory-menu.png)

2. Az **új adat-előállító** lapon adja meg az alábbi képen látható mezők értékét: 
      
   ![Új adat-előállító lap](./media/load-data-into-azure-data-lake-store//new-azure-data-factory.png)
 
    * **Név**: adjon meg egy globálisan egyedi nevet az Azure-beli adatgyár számára. Ha "a \" LoadADLSG1Demo nem érhető el" hibaüzenet jelenik \" meg, adjon meg egy másik nevet az adatelőállító számára. Használhatja például a _**sajátneve**_**ADFTutorialDataFactory** nevet. Próbálkozzon újra az adatelőállító létrehozásával. A Data Factory-összetevők elnevezési szabályait a [Data Factory elnevezési szabályait](naming-rules.md) ismertető cikkben találja.
    * **Előfizetés**: válassza ki azt az Azure-előfizetést, amelyben létre kívánja hozni az adatelőállítót. 
    * **Erőforráscsoport**: válasszon ki egy meglévő erőforráscsoportot a legördülő listából, vagy válassza az **új létrehozása** lehetőséget, és adja meg az erőforráscsoport nevét. Az erőforráscsoportokkal kapcsolatos információkért tekintse meg a [Using resource groups to manage your Azure resources](../azure-resource-manager/management/overview.md) (Erőforráscsoportok használata az Azure-erőforrások kezeléséhez) című cikket.  
    * **Verzió**: Válassza a **V2** lehetőséget.
    * **Hely**: válassza ki az adatelőállító helyét. A legördülő listán csak a támogatott helyek jelennek meg. A Refactory által használt adattárak más helyszíneken és régiókban is lehetnek. Ezek az adattárak a következők: Azure Data Lake Storage Gen1, Azure Storage, Azure SQL Database stb.

3. Válassza a **Létrehozás** lehetőséget.
4. A létrehozás befejezése után nyissa meg az adatait a gyárban. Megjelenik a **Data Factory** kezdőlapja, ahogy az a következő képen látható: 
   
   ![Data factory kezdőlap](./media/load-data-into-azure-data-lake-store/data-factory-home-page.png)

   Az Adatintegráció alkalmazás külön lapon való elindításához kattintson a **Létrehozás és figyelés** csempére.

## <a name="load-data-into-data-lake-storage-gen1"></a>Betöltés az Data Lake Storage Gen1ba

1. Az **első lépések** lapon válassza ki a **adatok másolása** csempét a adatok másolása eszköz elindításához: 

   ![Az Adatok másolása eszköz csempéje](./media/load-data-into-azure-data-lake-store/copy-data-tool-tile.png)
2. A **Tulajdonságok** lapon adja meg a **CopyFromAmazonS3ToADLS** a **feladat neve** mezőben, majd válassza a **Next (tovább**) gombot:

    ![Tulajdonságok lap](./media/load-data-into-azure-data-lake-store/copy-data-tool-properties-page.png)
3. A **forrás adattár** lapon kattintson az **+ új kapcsolatok létrehozása** lehetőségre:

    ![Forrásadattár lap](./media/load-data-into-azure-data-lake-store/source-data-store-page.png)
    
    Válassza ki az **Amazon S3** elemet, majd válassza a **Folytatás** lehetőséget.
    
    ![Forrás adattár S3 oldal](./media/load-data-into-azure-data-lake-store/source-data-store-page-s3.png)
    
4. Az **Amazon S3-kapcsolatok meghatározása** oldalon hajtsa végre a következő lépéseket: 
   1. Itt adhatja meg a **hozzáférési kulcs azonosítójának** értékét.
   2. A **titkos hozzáférési kulcs** értékének megadása.
   3. Válassza a **Befejezés** gombot.
   
      ![Képernyőfelvétel: az új társított szolgáltatás ablaktábla, ahol megadhatja az értékeket.](./media/load-data-into-azure-data-lake-store/specify-amazon-s3-account.png)
   
   4. Ekkor megjelenik egy új-kapcsolatok. Kattintson a **Tovább** gombra.
   
   ![A képernyőképen az új-kapcsolatok láthatók.](./media/load-data-into-azure-data-lake-store/specify-amazon-s3-account-created.png)
   
5. **A bemeneti fájl vagy mappa kiválasztása** lapon tallózzon az átmásolni kívánt mappához és fájlhoz. Válassza ki a mappát vagy a fájlt, válassza a **kiválasztás** lehetőséget, majd válassza a **Next (tovább**) gombot:

    ![Bemeneti fájl vagy mappa kiválasztása](./media/load-data-into-azure-data-lake-store/choose-input-folder.png)

6. Válassza ki a másolási viselkedést a **fájlok rekurzív** és **bináris másolás** (fájlok másolása) beállításainak kiválasztásával. Válassza a **Next (tovább**) lehetőséget:

    ![Képernyőfelvétel: a bemeneti fájl vagy mappa kiválasztása, ahol kiválaszthatja a fájl rekurzív és bináris másolatának másolása lehetőséget.](./media/load-data-into-azure-data-lake-store/specify-binary-copy.png)
    
7. A **cél adattár** lapon kattintson az **+ új kapcsolatok létrehozása** lehetőségre, majd válassza a **Azure Data Lake Storage Gen1** lehetőséget, majd a **Folytatás**:

    ![Céladattár lap](./media/load-data-into-azure-data-lake-store/destination-data-storage-page.png)

8. Az **új társított szolgáltatás (Azure Data Lake Storage Gen1)** lapon hajtsa végre a következő lépéseket: 

   1. Válassza ki Data Lake Storage Gen1 fiókját a **Data Lake Store-fiók nevéhez**.
   2. Adja meg a **bérlőt**, és válassza a Befejezés lehetőséget.
   3. Kattintson a **Tovább** gombra.
   
   > [!IMPORTANT]
   > Ebben az útmutatóban egy felügyelt identitást használ az Azure-erőforrásokhoz az Data Lake Storage Gen1-fiók hitelesítéséhez. Ügyeljen arra, hogy az MSI-t a megfelelő engedélyekkel adja meg Data Lake Storage Gen1 az alábbi [utasításokat](connector-azure-data-lake-store.md#managed-identity)követve.
   
   ![Data Lake Storage Gen1 fiók meghatározása](./media/load-data-into-azure-data-lake-store/specify-adls.png)
9. A **kimeneti fájl vagy mappa kiválasztása** lapon adja meg a **copyfroms3** a kimeneti mappa neveként, majd válassza a **Next (tovább**) gombot: 

    ![Képernyőfelvétel: a megadott mappa elérési útja.](./media/load-data-into-azure-data-lake-store/specify-adls-path.png)

10. A **Beállítások** lapon válassza a **tovább** lehetőséget:

    ![Beállítások lap](./media/load-data-into-azure-data-lake-store/copy-settings.png)
11. Az **Összefoglalás** lapon tekintse át a beállításokat, majd kattintson a **Next (tovább) gombra**:

    ![Összefoglaló lap](./media/load-data-into-azure-data-lake-store/copy-summary.png)
12. Az **üzembe helyezés lapon** válassza a **figyelő** lehetőséget a folyamat figyeléséhez (feladat):

    ![Üzembe helyezés lap](./media/load-data-into-azure-data-lake-store/deployment-page.png)
13. Figyelje meg, hogy a bal oldalon található **Figyelés** lap automatikusan ki lesz választva. A **műveletek** oszlop a tevékenység futtatási részleteinek megtekintésére és a folyamat újrafuttatására mutató hivatkozásokat tartalmaz:

    ![Folyamatfuttatások monitorozása](./media/load-data-into-azure-data-lake-store/monitor-pipeline-runs.png)
14. Ha meg szeretné tekinteni a folyamat futtatásához társított tevékenység-futtatásokat, válassza a **műveletek** oszlop **Megtekintés tevékenység futtatása** hivatkozását. Csak egy (másolási) tevékenység található a folyamatban, ezért csak egy bejegyzést lát. Ha vissza szeretne váltani a folyamat futási nézetére, válassza a felül található **folyamatok** hivatkozást. A lista frissítéséhez kattintson a **Frissítés** gombra. 

    ![Tevékenységfuttatások monitorozása](./media/load-data-into-azure-data-lake-store/monitor-activity-runs.png)

15. Az egyes másolási tevékenységek végrehajtási részleteinek figyeléséhez válassza a tevékenységek figyelése nézet **műveletek** területén található **részletek** hivatkozást. A forrásról a fogadóra másolt adatok mennyiségét, az adatátvitelt, a végrehajtási lépéseket és a megfelelő időtartamot, valamint a használt konfigurációkat is figyelheti:

    ![Tevékenység-futtatási részletek figyelése](./media/load-data-into-azure-data-lake-store/monitor-activity-run-details.png)

16. Ellenőrizze, hogy a rendszer átmásolta-e az adatait a Data Lake Storage Gen1-fiókjába: 

    ![Data Lake Storage Gen1 kimenet ellenőrzése](./media/load-data-into-azure-data-lake-store/adls-copy-result.png)

## <a name="next-steps"></a>Következő lépések

A következő cikkből megismerheti a Data Lake Storage Gen1 támogatását: 

> [!div class="nextstepaction"]
>[Azure Data Lake Storage Gen1-összekötő](connector-azure-data-lake-store.md)
