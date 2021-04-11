---
title: Adatok betöltése az Azure Data Lake Storage Gen2-be
description: Az Adatmásolás Azure Data Factory használatával Azure Data Lake Storage Gen2
ms.author: jingwang
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 02/18/2021
ms.openlocfilehash: 075778e2650d3b1f67447078eb6cb130849bbb35
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "104593781"
---
# <a name="load-data-into-azure-data-lake-storage-gen2-with-azure-data-factory"></a>Betöltés az Azure Data Lake Storage Gen2ba Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

A Azure Data Lake Storage Gen2 az [Azure Blob Storage](../storage/blobs/storage-blobs-introduction.md)-ba beépített, Big Data Analytics szolgáltatáshoz rendelt képességek összessége. Lehetővé teszi az adatkapcsolatot a fájlrendszer és az objektum tárolási paradigmáinak használatával.

A Azure Data Factory (ADF) egy teljes körűen felügyelt felhőalapú adatintegrációs szolgáltatás. A szolgáltatással feltöltheti a tavat a helyszíni és felhőalapú adattárakból származó adatokkal, és időt takaríthat meg az elemzési megoldások kiépítésekor. A támogatott összekötők részletes listáját a [támogatott adattárakkal](copy-activity-overview.md#supported-data-stores-and-formats)foglalkozó táblázatban tekintheti meg.

A Azure Data Factory kibővíthető, felügyelt adatátviteli megoldást kínál. Az ADF kibővített architektúrája miatt az adatok nagy adatátviteli sebességgel is beolvashatók. Részletekért lásd: [másolási tevékenység teljesítménye](copy-activity-performance.md).

Ez a cikk bemutatja, hogyan tölthetők be a Data Factory Adatok másolása eszköz az _Amazon Web Services S3 szolgáltatásból_ _Azure Data Lake Storage Gen2ba_ való adatok betöltéséhez. Az adatok más típusú adattárakból történő másolásához hasonló lépéseket kell követnie.

>[!TIP]
>Az adatok Azure Data Lake Storage Gen1ból a Gen2 való másolásához tekintse meg [ezt a konkrét bemutatót](load-azure-data-lake-storage-gen2-from-gen1.md).

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés: Ha nem rendelkezik Azure-előfizetéssel, a Kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/) .
* Azure Storage-fiók Data Lake Storage Gen2 engedélyezve: Ha nincs Storage-fiókja, [hozzon létre egy fiókot](https://ms.portal.azure.com/#create/Microsoft.StorageAccount-ARM).
* AWS-fiók egy olyan S3 gyűjtővel, amely tartalmaz egy adatforrást: Ez a cikk bemutatja, hogyan másolhat adatok az Amazon S3-ból. A hasonló lépéseket követve más adattárakat is használhat.

## <a name="create-a-data-factory"></a>Adat-előállító létrehozása

1. A bal oldali menüben válassza az **erőforrás**-  >  **integráció** létrehozása  >  **Data Factory**:
   
   ![Data Factory kiválasztása az „Új” ablaktáblán](./media/doc-common-process/new-azure-data-factory-menu.png)

2. Az **új adat-előállító** lapon adja meg a következő mezők értékét:
 
    * **Név**: adjon meg egy globálisan egyedi nevet az Azure-beli adatgyár számára. Ha "a *YourDataFactoryName* nem érhető el" hibaüzenet jelenik meg, adjon meg egy másik nevet az adatelőállítónak. Használhatja például a _**sajátneve**_**ADFTutorialDataFactory** nevet. Próbálkozzon újra az adatelőállító létrehozásával. A Data Factory-összetevők elnevezési szabályait a [Data Factory elnevezési szabályait](naming-rules.md) ismertető cikkben találja.
    * **Előfizetés**: válassza ki azt az Azure-előfizetést, amelyben létre kívánja hozni az adatelőállítót. 
    * **Erőforráscsoport**: válasszon ki egy meglévő erőforráscsoportot a legördülő listából, vagy válassza az **új létrehozása** lehetőséget, és adja meg az erőforráscsoport nevét. Az erőforráscsoportokkal kapcsolatos információkért tekintse meg a [Using resource groups to manage your Azure resources](../azure-resource-manager/management/overview.md) (Erőforráscsoportok használata az Azure-erőforrások kezeléséhez) című cikket.  
    * **Verzió**: Válassza a **V2** lehetőséget.
    * **Hely**: válassza ki az adatelőállító helyét. A legördülő listán csak a támogatott helyek jelennek meg. A Refactory által használt adattárak más helyszíneken és régiókban is lehetnek. 

3. Válassza a **Létrehozás** lehetőséget.

4. A létrehozás befejezése után nyissa meg az adatait a gyárban. Megjelenik a **Data Factory** kezdőlapja, ahogy az a következő képen látható: 
   
   :::image type="content" source="./media/doc-common-process/data-factory-home-page.png" alt-text="A Azure Data Factory kezdőlapja, a szerző & monitor csempével.":::

   Az Adatintegráció alkalmazás külön lapon való elindításához kattintson a **Létrehozás és figyelés** csempére.

## <a name="load-data-into-azure-data-lake-storage-gen2"></a>Adatok betöltése az Azure Data Lake Storage Gen2-be

1. Az Adatok másolása eszköz elindításához az **Első lépések** oldalon kattintson az **Adatok másolása** csempére.

2. A **Tulajdonságok** lapon adja meg a **CopyFromAmazonS3ToADLS** mezőt a **feladat neve** mezőben, majd kattintson a **Tovább gombra**.

    ![Tulajdonságok lap](./media/load-azure-data-lake-storage-gen2/copy-data-tool-properties-page.png)
3. A **Forrásadattár** oldalon kattintson az **Új kapcsolat létrehozása** lehetőségre. Válassza ki az **Amazon S3** elemet az összekötő-katalógusból, és válassza a **Folytatás** lehetőséget.
    
    ![Forrás adattár S3 oldal](./media/load-azure-data-lake-storage-gen2/source-data-store-page-s3.png)
    
4. Az **új társított szolgáltatás (Amazon S3)** oldalon hajtsa végre a következő lépéseket:

   1. Itt adhatja meg a **hozzáférési kulcs azonosítójának** értékét.
   2. A **titkos hozzáférési kulcs** értékének megadása.
   3. Kattintson a **Kapcsolódás tesztelése** elemre a beállítások ellenőrzéséhez, majd válassza a **Létrehozás** lehetőséget.

      ![Amazon S3-fiók meghatározása](./media/load-azure-data-lake-storage-gen2/specify-amazon-s3-account.png)
   4. Ekkor megjelenik egy új AmazonS3-kapcsolatok létrehozása. Kattintson a **Tovább** gombra. 

5. **A bemeneti fájl vagy mappa kiválasztása** lapon tallózzon az átmásolni kívánt mappához és fájlhoz. Válassza ki a mappát vagy a fájlt, majd válassza **a kiválasztás lehetőséget.**

    ![Bemeneti fájl vagy mappa kiválasztása](./media/load-azure-data-lake-storage-gen2/choose-input-folder.png)

6. A **rekurzív** és a **bináris másolási** beállítások ellenőrzésével határozza meg a másolási viselkedést. Kattintson a **Tovább** gombra.

    ![A képernyőfelvételen a bemeneti fájl vagy mappa kiválasztása látható, ahol kiválaszthatja a bináris másolás és rekurzív módon lehetőséget.](./media/load-azure-data-lake-storage-gen2/specify-binary-copy.png)
    
7. A **cél adattár** lapon kattintson az **+ új kapcsolatok létrehozása** lehetőségre, majd válassza a **Azure Data Lake Storage Gen2** lehetőséget, majd a **Folytatás** lehetőséget.

    ![Céladattár lap](./media/load-azure-data-lake-storage-gen2/destination-data-storage-page.png)

8. Az **új társított szolgáltatás (Azure Data Lake Storage Gen2)** lapon hajtsa végre a következő lépéseket:

   1. Válassza ki Data Lake Storage Gen2 kezelésére képes fiókját a „Tárfiók neve” legördülő listából.
   2. Válassza a **Létrehozás** lehetőséget a kapcsolódás létrehozásához. Ezután kattintson a **Tovább** gombra.   

        ![Azure Data Lake Storage Gen2 fiók meghatározása](./media/load-azure-data-lake-storage-gen2/specify-azure-data-lake-storage.png)

9. A **kimeneti fájl vagy mappa kiválasztása** lapon adja meg a **copyfroms3** a kimeneti mappa neveként, majd kattintson a **tovább** gombra. Az ADF a másolás során létrehozza a megfelelő ADLS Gen2 fájlrendszert és almappákat, ha az nem létezik.

    ![Képernyőfelvétel: a megadott mappa elérési útja.](./media/load-azure-data-lake-storage-gen2/specify-adls-path.png)

10. A **Beállítások** lapon kattintson a **Tovább** gombra az alapértelmezett beállítások használatához.

    ![Beállítások lap](./media/load-azure-data-lake-storage-gen2/copy-settings.png)

11. Az **Összefoglalás** lapon tekintse át a beállításokat, majd kattintson a **Tovább gombra**.

    ![Összefoglaló lap](./media/load-azure-data-lake-storage-gen2/copy-summary.png)

12. A folyamat (feladat) figyeléséhez az **Üzembe helyezés** lapon kattintson a **Monitorozás** elemre. 
 
13. Ha a folyamat futása sikeresen befejeződött, egy manuális trigger által aktivált folyamat-Futtatás jelenik meg. A **folyamat neve** oszlopban található hivatkozások használatával megtekintheti a tevékenységek részleteit, és újra futtathatja a folyamatot.

    ![Folyamatfuttatások monitorozása](./media/load-azure-data-lake-storage-gen2/monitor-pipeline-runs.png)

14. A folyamat futtatásához társított tevékenységek megtekintéséhez jelölje ki a **CopyFromAmazonS3ToADLS** HIVATKOZÁST a folyamat neve oszlopban. A másolási művelet részleteinek megtekintéséhez kattintson a tevékenység neve oszlop **részletek** hivatkozására (szemüveg ikon). A forrásról a fogadóra másolt adatok mennyiségét, az adatátvitelt, a végrehajtási lépéseket, valamint a megfelelő időtartamot és a használt konfigurációt is figyelheti.
 
    ![Tevékenységfuttatások monitorozása](./media/load-azure-data-lake-storage-gen2/monitor-activity-runs.png)
    
    ![Tevékenység-futtatási részletek figyelése](./media/load-azure-data-lake-storage-gen2/monitor-activity-run-details.png)

15. A nézet frissítéséhez válassza a Frissítés parancsot. Válassza a felül található **összes folyamat futtatása** lehetőséget a folyamat futási nézetének visszalépéséhez.

16. Ellenőrizze, hogy a rendszer átmásolta-e az adatait a Data Lake Storage Gen2-fiókjába.

## <a name="next-steps"></a>Következő lépések

* [Másolási tevékenység – áttekintés](copy-activity-overview.md)
* [Azure Data Lake Storage Gen2-összekötő](connector-azure-data-lake-storage.md)
