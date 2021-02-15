---
title: Adatok betöltése az Azure Synapse Analyticsbe
description: Az Adatmásolás az Azure szinapszis Analytics szolgáltatásba Azure Data Factory használatával
ms.author: jingwang
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 01/29/2020
ms.openlocfilehash: 94f5884cb9d1886a9e7aa6b09817b26bba6d47f7
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/14/2021
ms.locfileid: "100370716"
---
# <a name="load-data-into-azure-synapse-analytics-by-using-azure-data-factory"></a>Az Azure szinapszis Analyticsbe való betöltés Azure Data Factory használatával

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Az [Azure szinapszis Analytics](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md) egy felhőalapú, kibővíthető adatbázis, amely képes a nagy mennyiségű, a kapcsolatok és a nem kapcsolatok kezelésére. Az Azure szinapszis Analytics a nagymértékben párhuzamos feldolgozási (MPP) architektúrára épül, amelyet a vállalati adattárház számítási feladataihoz optimalizáltak. A felhő rugalmasságának köszönhetően rugalmasan méretezheti a tárolást és a számításokat egymástól függetlenül.

Az Azure szinapszis Analytics használatának első lépései mostantól minden eddiginél könnyebben használhatók Azure Data Factory használatakor. A Azure Data Factory egy teljes körűen felügyelt felhőalapú adatintegrációs szolgáltatás. A szolgáltatás segítségével feltöltheti az Azure szinapszis Analytics szolgáltatást a meglévő rendszerből származó adatokkal, és időt takaríthat meg az elemzési megoldások létrehozásakor.

Azure Data Factory a következő előnyöket biztosítja az Azure szinapszis Analyticsbe való betöltéshez:

* **Egyszerűen beállítható**: egy intuitív 5 lépésből álló varázsló, amely nem igényel parancsfájlt.
* **Gazdag adattár-támogatás**: beépített támogatás a helyszíni és felhőalapú adattárak gazdag készletéhez. Részletes listát a [támogatott adattárakkal](copy-activity-overview.md#supported-data-stores-and-formats)foglalkozó táblázatban talál.
* **Biztonságos és megfelelő**: az adatátvitel HTTPS-vagy ExpressRoute-kapcsolaton keresztül történik. A globális szolgáltatás jelenléte biztosítja, hogy az adatai soha nem hagyják el a földrajzi határt.
* **Páratlan teljesítmény a Base használatával**: a Base a leghatékonyabb módszer az adatok Azure szinapszis-elemzésbe való áthelyezésére. Az átmeneti blob funkcióval nagy terhelési sebességet érhet el minden típusú adattárból, beleértve az Azure Blob Storage-t és a Data Lake Storet is. (A Base alapértelmezés szerint támogatja az Azure Blob Storage-t és a Azure Data Lake Store.) Részletekért lásd: [másolási tevékenység teljesítménye](copy-activity-performance.md).

Ebből a cikkből megtudhatja, hogyan _tölthetők be a Azure SQL Databaseból származó adatok az Azure szinapszis Analytics_ szolgáltatásba a Data Factory adatok másolása eszköz használatával. Az adatok más típusú adattárakból történő másolásához hasonló lépéseket kell követnie.

> [!NOTE]
> További információ: [adatok másolása az Azure szinapszis Analytics szolgáltatásba vagy onnan az Azure Data Factory használatával](connector-azure-sql-data-warehouse.md).

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés: Ha nem rendelkezik Azure-előfizetéssel, a Kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/) .
* Azure szinapszis Analytics: az adattárház az SQL-adatbázisból másolt adatok tárolására használatos. Ha nem rendelkezik Azure szinapszis Analytics-elemzéssel, tekintse meg az [Azure szinapszis Analytics létrehozása](../synapse-analytics/sql-data-warehouse/load-data-from-azure-blob-storage-using-copy.md)című témakör utasításait.
* Azure SQL Database: az oktatóanyag az Adventure Works LT minta adatkészletből másolja az adatokat Azure SQL Database. A mintaadatbázis létrehozásához SQL Database a [mintaadatbázis létrehozása a Azure SQL Databaseban](../azure-sql/database/single-database-create-quickstart.md)című témakör útmutatását követve.
* Azure Storage-fiók: az Azure Storage a tömeges másolási művelet során _átmeneti_ blobként szolgál. Ha még nem rendelkezik Azure Storage-fiókkal, a szükséges utasításokat a [Storage-fiók létrehozását](../storage/common/storage-account-create.md) ismertető cikkben találja.

## <a name="create-a-data-factory"></a>Adat-előállító létrehozása

1. A bal oldali menüben válassza az **erőforrás létrehozása**  >  **adatok és Analitika**  >  **Data Factory**:

2. Az **új adatok előállítója** lapon adja meg a következő elemek értékeit:

    * **Név**: írja be a *LoadSQLDWDemo* nevet. Az adatelőállító nevének * globálisan egyedinek kell lennie. Ha a "LoadSQLDWDemo" nevű "nem érhető el" hibaüzenet jelenik meg, adjon meg egy másik nevet az adatelőállítónak. Használhatja például a _**sajátneve**_**ADFTutorialDataFactory** nevet. Próbálkozzon újra az adatelőállító létrehozásával. A Data Factory-összetevők elnevezési szabályait a [Data Factory elnevezési szabályait](naming-rules.md) ismertető cikkben találja.
    * **Előfizetés**: válassza ki azt az Azure-előfizetést, amelyben létre kívánja hozni az adatelőállítót. 
    * **Erőforráscsoport**: válasszon ki egy meglévő erőforráscsoportot a legördülő listából, vagy válassza az **új létrehozása** lehetőséget, és adja meg az erőforráscsoport nevét. Az erőforráscsoportokkal kapcsolatos információkért tekintse meg a [Using resource groups to manage your Azure resources](../azure-resource-manager/management/overview.md) (Erőforráscsoportok használata az Azure-erőforrások kezeléséhez) című cikket.  
    * **Verzió**: Válassza a **V2** lehetőséget.
    * **Hely**: válassza ki az adatelőállító helyét. A legördülő listán csak a támogatott helyek jelennek meg. A Refactory által használt adattárak más helyszíneken és régiókban is lehetnek. Ezek az adattárak a következők: Azure Data Lake Store, Azure Storage, Azure SQL Database stb.

3. Válassza a **Létrehozás** lehetőséget.
4. A létrehozás befejezése után nyissa meg az adatait a gyárban. Megjelenik a **Data Factory** kezdőlapja, ahogy az a következő képen látható:

   ![Data factory kezdőlap](./media/doc-common-process/data-factory-home-page.png)

   Az Adatintegráció alkalmazás külön lapon való elindításához kattintson a **Létrehozás és figyelés** csempére.

## <a name="load-data-into-azure-synapse-analytics"></a>Adatok betöltése az Azure Synapse Analyticsbe

1. Az Adatok másolása eszköz elindításához az **Első lépések** oldalon kattintson az **Adatok másolása** csempére.

2. A **Tulajdonságok** lapon adja meg a **CopyFromSQLToSQLDW** mezőt a **feladat neve** mezőben, majd kattintson a **Tovább gombra**.

    ![Tulajdonságok lap](./media/load-azure-sql-data-warehouse/copy-data-tool-properties-page.png)

3. A **forrás adattár** oldalon hajtsa végre a következő lépéseket:
    >[!TIP]
    >Ebben az oktatóanyagban az *SQL-hitelesítést* használja a forrás adattároló hitelesítési típusaként, de más támogatott hitelesítési módszereket is választhat: az *egyszerű szolgáltatásnév* és a *felügyelt identitás* , ha szükséges. A részletekért tekintse meg a [cikk](./connector-azure-sql-database.md#linked-service-properties) megfelelő részeit.
    >Az adattárak titkos kulcsainak biztonságos tárolásához ajánlott egy Azure Key Vault is használni. A részletes illusztrációkat [ebben a cikkben](./store-credentials-in-key-vault.md) találja.

    a. kattintson az **+ új kapcsolatok létrehozása** lehetőségre.

    b. Válassza ki **Azure SQL Database** a katalógusból, és válassza a **Folytatás** lehetőséget. Az összekötők szűréséhez a keresőmezőbe írja be az "SQL" kifejezést.

    ![Azure SQL-adatbázis kiválasztása](./media/load-azure-sql-data-warehouse/select-azure-sql-db-source.png)

    c. Az **új társított szolgáltatás** oldalon válassza ki a kiszolgáló nevét és az adatbázis nevét a legördülő listából, majd adja meg a felhasználónevet és a jelszót. Kattintson a **Kapcsolódás tesztelése** elemre a beállítások ellenőrzéséhez, majd válassza a **Létrehozás** lehetőséget.

    ![Azure SQL-adatbázis konfigurálása](./media/load-azure-sql-data-warehouse/configure-azure-sql-db.png)

    d. Forrásnak válassza ki az újonnan létrehozott társított szolgáltatást, majd kattintson a **Tovább** gombra.

4. A táblázatok **kiválasztásához, amelyből másolni kívánja az adatait, vagy használjon egyéni lekérdezési** oldalt, írja be a **SalesLT** a táblák szűréséhez. A másoláshoz **használja az összes** táblát, majd válassza a **tovább** lehetőséget.

    ![Forrástábla kiválasztása](./media/load-azure-sql-data-warehouse/select-source-tables.png)

5. A **szűrő alkalmazása** lapon adja meg a beállításokat, vagy válassza a **tovább** lehetőséget.

6. A **cél adattár** oldalon hajtsa végre a következő lépéseket:
    >[!TIP]
    >Ebben az oktatóanyagban az *SQL-hitelesítést* használja a célhely-adattár hitelesítési típusaként, de más támogatott hitelesítési módszereket is választhat:*egyszerű szolgáltatásnév* és *felügyelt identitás* használata, ha szükséges. A részletekért tekintse meg a [cikk](./connector-azure-sql-data-warehouse.md#linked-service-properties) megfelelő részeit.
    >Az adattárak titkos kulcsainak biztonságos tárolásához ajánlott egy Azure Key Vault is használni. A részletes illusztrációkat [ebben a cikkben](./store-credentials-in-key-vault.md) találja.

    a. Kapcsolat hozzáadásához kattintson a **+ Új kapcsolat létrehozása** lehetőségre

    b. Válassza ki az **Azure szinapszis Analytics** elemet a katalógusból, és válassza a **Folytatás** lehetőséget.

    ![Az Azure szinapszis Analytics kiválasztása](./media/load-azure-sql-data-warehouse/select-azure-sql-dw-sink.png)

    c. Az **új társított szolgáltatás** oldalon válassza ki a kiszolgáló nevét és az adatbázis nevét a legördülő listából, majd adja meg a felhasználónevet és a jelszót. Kattintson a **Kapcsolódás tesztelése** elemre a beállítások ellenőrzéséhez, majd válassza a **Létrehozás** lehetőséget.

    ![Az Azure szinapszis Analytics konfigurálása](./media/load-azure-sql-data-warehouse/configure-azure-sql-dw.png)

    d. Válassza ki fogadóként az újonnan létrehozott társított szolgáltatást, majd kattintson a **Tovább** gombra.

7. A **tábla-hozzárendelés** lapon tekintse át a tartalmat, és kattintson a **Tovább gombra**. Megjelenik egy intelligens táblázat-hozzárendelés. A forrástábla a táblák nevei alapján van leképezve a céltábla számára. Ha a forrás tábla nem létezik a célhelyen, Azure Data Factory alapértelmezés szerint ugyanazzal a névvel hozza létre a célhelyet. A forrástábla egy meglévő céltábla számára is képezhető le.

   ![Tábla hozzárendelése oldal](./media/load-azure-sql-data-warehouse/table-mapping.png)

8. Az **oszlop-hozzárendelés** lapon tekintse át a tartalmat, és kattintson a **Tovább gombra**. Az intelligens tábla leképezése az oszlop nevén alapul. Ha lehetővé teszi, hogy Data Factory automatikusan létrehozza a táblákat, az adattípus-konverzió akkor fordulhat elő, ha a forrás-és a célhelyek között inkompatibilitás van. Ha a forrás és a cél oszlop között nem támogatott adattípusú átalakítás van, a megfelelő tábla mellett hibaüzenet jelenik meg.

    ![Oszlop-hozzárendelési lap](./media/load-azure-sql-data-warehouse/schema-mapping.png)

9. A **Beállítások** lapon végezze el a következő lépéseket:

    a. Az **előkészítési beállítások** szakaszban kattintson az **+ új** elemre az átmeneti tárolók új létrehozásához. A tárterület az adatok előkészítéséhez használatos, mielőtt betöltődik az Azure szinapszis Analyticsbe a Base használatával. A másolás befejezése után a rendszer automatikusan megtisztítja az Azure Blob Storage ideiglenes adattartalmát.

    b. Az **új társított szolgáltatás** oldalon válassza ki a Storage-fiókját, majd válassza a **Létrehozás** lehetőséget a társított szolgáltatás telepítéséhez.

    c. Törölje az **alapértelmezett típus használata** lehetőséget, majd kattintson a **tovább** gombra.

    ![A bázisterület konfigurálása](./media/load-azure-sql-data-warehouse/configure-polybase.png)

10. Az **Összefoglalás** lapon tekintse át a beállításokat, majd kattintson a **Tovább gombra**.

    ![Összefoglaló lap](./media/load-azure-sql-data-warehouse/summary-page.png)

11. A folyamat (feladat) figyeléséhez az **Üzembe helyezés** lapon kattintson a **Monitorozás** elemre. 
 
12. Figyelje meg, hogy a bal oldalon található **Figyelés** lap automatikusan ki lesz választva. Ha a folyamat futása sikeresen befejeződött, válassza a **folyamat neve** oszlopban a **CopyFromSQLToSQLDW** hivatkozást a tevékenység futtatási részleteinek megtekintéséhez vagy a folyamat újrafuttatásához.

    [![Folyamatfuttatások monitorozása](./media/load-azure-sql-data-warehouse/pipeline-monitoring.png)](./media/load-azure-sql-data-warehouse/pipeline-monitoring.png#lightbox)

13. Ha vissza szeretne váltani a folyamat futási nézetére, válassza az **összes folyamat futtatása** hivatkozást a felső részen. A lista frissítéséhez kattintson a **Frissítés** gombra.

    ![Tevékenységfuttatások monitorozása](./media/load-azure-sql-data-warehouse/activity-monitoring.png)

14. Az egyes másolási tevékenységek végrehajtási részleteinek figyeléséhez válassza a tevékenységek **neve** alatt található **részletek** hivatkozást (szemüveg ikon) a tevékenység futtatási nézetében. A forrásról a fogadóra másolt adatok mennyiségét, az adatátvitelt, a végrehajtási lépéseket és a megfelelő időtartamot, valamint a használt konfigurációkat is figyelheti.
    ![Tevékenység-futtatási részletek figyelése – első](./media/load-azure-sql-data-warehouse/monitor-activity-run-details-1.png)

    ![Tevékenység-futtatási részletek figyelése – második](./media/load-azure-sql-data-warehouse/monitor-activity-run-details-2.png)

## <a name="next-steps"></a>Következő lépések

A következő cikkből megismerheti az Azure szinapszis Analytics támogatását:

> [!div class="nextstepaction"]
>[Azure szinapszis Analytics-összekötő](connector-azure-sql-data-warehouse.md)