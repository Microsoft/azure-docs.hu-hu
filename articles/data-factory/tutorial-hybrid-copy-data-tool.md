---
title: Helyszíni adatok másolása az Azure Adatok másolása eszközével | Microsoft Docs
description: Létrehozhat egy Azure-beli adat-előállítót, majd az Adatok másolása eszközzel másolhat adatokat egy helyszíni SQL Server-adatbázisból az Azure Blob Storage-tárolóba.
services: data-factory
documentationcenter: ''
author: nabhishek
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: tutorial
ms.date: 04/09/2018
ms.author: abnarain
ms.openlocfilehash: cbefc274e4ebb65f61769a7931dbed305acffe2f
ms.sourcegitcommit: e42c778d38fd623f2ff8850bb6b1718cdb37309f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/19/2019
ms.locfileid: "69617544"
---
# <a name="copy-data-from-an-on-premises-sql-server-database-to-azure-blob-storage-by-using-the-copy-data-tool"></a>Adatok másolása az Adatok másolása eszközzel helyszíni SQL Server-adatbázisból Azure Blob Storage-tárolóba
> [!div class="op_single_selector" title1="Válassza ki az Ön által használt Data Factory-szolgáltatás verzióját:"]
> * [1-es verzió](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
> * [Aktuális verzió](tutorial-hybrid-copy-data-tool.md)

Ebben az oktatóanyagban egy adat-előállítót hoz létre az Azure Portal használatával. Ezután az Adatok másolása eszközzel létrehoz egy folyamatot, amely egy helyszíni SQL Server-adatbázisból másol adatokat egy Azure-blobtárolóba.

> [!NOTE]
> - Ha még csak ismerkedik az Azure Data Factory szolgáltatással, olvassa el a [Data Factory szolgáltatást bemutató](introduction.md) cikket.

Az oktatóanyagban az alábbi lépéseket fogja végrehajtani:

> [!div class="checklist"]
> * Adat-előállító létrehozása
> * Folyamat létrehozása az Adatok másolása eszközzel.
> * A folyamat és a tevékenységek futásának monitorozása

## <a name="prerequisites"></a>Előfeltételek
### <a name="azure-subscription"></a>Azure-előfizetés
Ha még nem rendelkezik Azure-előfizetéssel, első lépésként [hozzon létre egy ingyenes](https://azure.microsoft.com/free/) fiókot.

### <a name="azure-roles"></a>Azure-szerepkörök
Az adat-előállító példányok létrehozásához annak a felhasználói fióknak, amellyel belép az Azure-ba, a *Közreműködő* vagy *Tulajdonos* szerepkör tagjának, vagy az Azure-előfizetés *rendszergazdájának* kell lennie. 

Az előfizetésben található engedélyek megtekintéséhez lépjen be az Azure Portalra. Válassza ki a felhasználónevét a jobb felső sarokban, majd válassza az **Engedélyek** elemet. Ha több előfizetéshez is rendelkezik hozzáféréssel, válassza ki a megfelelő előfizetést. Ha szeretne példautasításokat látni egy felhasználó szerepkörhöz adására, olvassa el a [Hozzáférés kezelése az RBAC és az Azure Portal használatával](../role-based-access-control/role-assignments-portal.md) című cikket.

### <a name="sql-server-2014-2016-and-2017"></a>SQL Server 2014, 2016 és 2017
Ebben az oktatóanyagban egy helyszíni SQL Server-adatbázist használunk *forrásadattárként*. Az oktatóanyag során létrehozott adat-előállító folyamata adatokat másol egy helyszíni SQL Server-adatbázisból (forrás) egy Blob Storage-tárolóba (fogadó). Ezután létre fog hozni egy **emp** nevű táblát az SQL Server-adatbázisban, és beszúr a táblába néhány mintabejegyzést. 

1. Indítsa el az SQL Server Management Studiót. Ha még nincs telepítve a számítógépen, tekintse meg az [SQL Server Management Studio letöltését ismertető](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) cikket. 

1. Csatlakozzon az SQL Server-példányához a hitelesítő adataival. 

1. Hozzon létre egy mintaadatbázist. A fanézetben kattintson a jobb gombbal a **Databases** (Adatbázisok) elemre, majd válassza a **New Database** (Új adatbázis) elemet. 

1. Az **New Database** (Új adatbázis) ablakban adjon nevet az új adatbázisnak, majd kattintson az **OK** gombra. 

1. Az **emp** tábla létrehozásához és néhány mintaadat beszúrásához futtassa a következő lekérdezési szkriptet az adatbázison. A fanézetben kattintson a jobb gombbal a létrehozott adatbázisra, majd válassza a **New Query** (Új lekérdezés) elemet.

    ```sql
    CREATE TABLE dbo.emp
    (
        ID int IDENTITY(1,1) NOT NULL,
        FirstName varchar(50),
        LastName varchar(50)
    )
    GO
    
    INSERT INTO emp (FirstName, LastName) VALUES ('John', 'Doe')
    INSERT INTO emp (FirstName, LastName) VALUES ('Jane', 'Doe')
    GO
    ```

### <a name="azure-storage-account"></a>Azure Storage-fiók
Ebben az oktatóanyagban egy általános célú Azure Storage-fiókot (ebben az esetben blobtárolót) használunk cél-/fogadóadattárként. Ha még nem rendelkezik általános célú tárfiókkal, a létrehozást segítő útmutatásért tekintse meg a [tárfiók létrehozását](../storage/common/storage-quickstart-create-account.md) ismertető cikket. Az oktatóanyag során létrehozott adat-előállító folyamata adatokat másol a helyszíni SQL Server-adatbázisból (forrás) a Blob Storage tárolóba (fogadó). 

#### <a name="get-the-storage-account-name-and-account-key"></a>Tárfióknév és fiókkulcs beszerzése
Ebben az oktatóanyagban a tárfiók nevét és kulcsát használjuk. A tárfiók nevét és kulcsát az alábbi lépéseket követve szerezheti be: 

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com) az Azure-ban használt felhasználónevével és jelszavával. 

1. A bal oldali ablaktáblán válassza a **minden szolgáltatás**lehetőséget. Szűrjön rá a **Tárolás** kulcsszóra, majd válassza a **Tárfiókok** lehetőséget.

    ![Tárfiók keresése](media/doc-common-process/search-storage-account.png)

1. A tárfiókok listájában állítson be szűrőt a tárfiókhoz, ha szükséges. Ezután válassza ki a tárfiókot. 

1. A **Tárfiók** ablakban válassza a **Hozzáférési kulcsok** elemet.


1. Másolja a **Tárfiók neve** és **1. kulcs** mező értékét, majd illessze be őket egy jegyzettömbbe vagy más szerkesztőbe az oktatóanyag későbbi részeiben történő használatra. 

#### <a name="create-the-adftutorial-container"></a>Adftutorial tároló létrehozása 
Ebben a szakaszban egy **adftutorial** nevű blobtárolót hoz létre a Blob Storage-ban. 

1. A **Tárfiók** ablakban váltson át az **Áttekintés** panelre, majd válassza a **Blobok** elemet. 

1. A **Blobok** ablakban válassza a **+ tároló**elemet. 

1. Az **új tároló** ablakban a név mezőbe írja be a **adftutorial** **nevet**, majd kattintson az **OK gombra**. 

1. A tárolók listájában kattintson az **adftutorial** elemre.


1. Ne zárja be az **adftutorial** **tároló** ablakát. Ezzel ellenőrizheti az oktatóanyag végén található kimenetet. A Data Factory automatikusan létrehozza a kimeneti mappát a tárolóban, így nem kell újat létrehoznia.


## <a name="create-a-data-factory"></a>data factory létrehozása

1. A bal oldali menüben válassza az **+ erőforrás** > létrehozása**elemzési** > **Data Factory**elemet. 
  
   ![Új adat-előállító létrehozása](./media/doc-common-process/new-azure-data-factory-menu.png)

1. Az **Új adat-előállító** lap **Név** mezőjében adja meg az **ADFTutorialDataFactory** értéket. 

   Az adat-előállító nevének *globálisan egyedinek* kell lennie. Ha a Név mezőnél az alábbi hibaüzenet jelenik meg, módosítsa az adat-előállító nevét (például a következőre: sajátneveADFTutorialDataFactory). A Data Factory-összetevők elnevezési szabályait a [Data Factory elnevezési szabályait](naming-rules.md) ismertető cikkben találja.

   ![Új adat-előállító neve](./media/doc-common-process/name-not-available-error.png)
1. Válassza ki azt az **Azure-előfizetést**, amelyben az adat-előállítót létre szeretné hozni. 
1. **Erőforráscsoport:** hajtsa végre a következő lépések egyikét:
  
   - Kattintson a **Meglévő használata** elemre, majd a legördülő listából válasszon egy meglévő erőforráscsoportot.

   - Kattintson az **Új létrehozása** elemre, és adja meg az erőforráscsoport nevét. 
        
     Az erőforráscsoportokkal kapcsolatos információkért tekintse meg az [Erőforráscsoportok használata az Azure-erőforrások kezeléséhez](../azure-resource-manager/resource-group-overview.md) ismertető cikket.
1. A **Verzió** résznél válassza a **V2** értéket.
1. A **Hely** alatt válassza ki az adat-előállító helyét. A legördülő listán csak a támogatott helyek jelennek meg. Az adat-előállítók által használt adattárak (például Azure Storage és SQL Database) és számítási erőforrások (például Azure HDInsight) más helyeken/régiókban is lehetnek.
1. Kattintson a **Létrehozás** gombra.

1. A létrehozás befejezése után a **Data Factory** lap a képen látható módon jelenik meg.
  
     ![Data factory kezdőlap](./media/doc-common-process/data-factory-home-page.png)
1. A Data Factory felhasználói felületének külön lapon történő elindításához válassza a **Tartalomkészítés és monitorozás** csempét. 

## <a name="use-the-copy-data-tool-to-create-a-pipeline"></a>Folyamat létrehozása az Adatok másolása eszközzel

1. Az Adatok másolása eszköz elindításához az **első lépéseket** ismertető oldalon válassza az **Adatok másolása** csempét. 

   ![Első lépések lap](./media/doc-common-process/get-started-page.png)

1. Az Adatok másolása eszköz **Tulajdonságok** lapján a **Feladat neve** alatt írja be a következőt: **CopyFromOnPremSqlToAzureBlobPipeline**. Ezután kattintson a **Tovább** gombra. Az Adatok másolása eszköz létrehoz egy folyamatot a mezőben megadott néven. 
  ![Feladat neve](./media/tutorial-hybrid-copy-data-tool/properties-page.png)

1. A **Forrásadattár** oldalon kattintson az **Új kapcsolat létrehozása** lehetőségre. 


1. Az **új társított szolgáltatás**területen keressen **SQL Server**, majd válassza a **Folytatás**lehetőséget. 

1. Az **új társított szolgáltatás (SQL Server)** párbeszédpanel **név**mezőjébe írja be a következőt: **SqlServerLinkedService**. Válassza a **+Új** elemet a **Csatlakozás integrációs modulon keresztül** résznél. Létre kell hoznia egy saját üzemeltetésű integrációs modult, le kell töltenie a gépére, és regisztrálnia kell a Data Factoryban. A saját üzemeltetésű integrációs modul adatokat másol a helyszíni környezetből a felhőbe.


1. A **Integration Runtime telepítés** párbeszédpanelen válassza a **saját**üzemeltetésű lehetőséget. Ezután kattintson a **Tovább** gombra. 

   ![Integrációs modul létrehozása](./media/tutorial-hybrid-copy-data-tool/create-integration-runtime-dialog0.png)

1. A **Integration Runtime beállítása** párbeszédpanel név területén írja be a **TutorialIntegrationRuntime** **nevet**. Ezután kattintson a **Tovább** gombra. 


1. A **Integration Runtime telepítés** párbeszédpanelen válassza **a kattintson ide, hogy elindítsa a számítógép expressz telepítését**. Ez a művelet telepíti az integrációs modult a számítógépére, és regisztrálja azt a Data Factoryban. Használhatja a manuális telepítési lehetőséget is. Ehhez töltse le a telepítőfájlt, futtassa, majd a kulccsal regisztrálja az integrációs modult. 

1. Futtassa a letöltött alkalmazást. Az expressz telepítés állapota látható az ablakban. 

    ![Expressz telepítés állapota](./media/tutorial-hybrid-copy-data-tool/express-setup-status.png)

1. Az **új társított szolgáltatás (SQL Server)** párbeszédpanelen ellenőrizze, hogy a **TutorialIntegrationRuntime** van-e kiválasztva a Integration Runtime mezőhöz. Ezután hajtsa végre a következő lépéseket:

    a. A **Név** mezőben adja meg az **SqlServerLinkedService** nevet.

    b. A **Kiszolgáló neve** alatt adja meg a helyszíni SQL Server-példánya nevét.

    c. Az **Adatbázis neve** mezőben adja meg a helyszíni adatbázis nevét.

    d. A **Hitelesítés típusa** mezőben válassza a megfelelő hitelesítést.

    e. A **Felhasználónév** mezőben adja meg a helyszíni SQL Server-kiszolgálóhoz hozzáféréssel rendelkező felhasználó nevét.

    f. Adja meg a felhasználónévhez tartozó **jelszót**. 

    g. Tesztelje a kapcsolatokat, és válassza a **Befejezés**lehetőséget.

      ![Integrációs modul kiválasztva](./media/tutorial-hybrid-copy-data-tool/integration-runtime-selected.png)

1. A **forrás adattár** lapon válassza a **tovább**lehetőséget.

1. A **Másolni kívánt adatokat tartalmazó táblák kiválasztása vagy egyéni lekérdezés használata** lapon válassza a listáról a **[dbo].[emp]** táblát, és kattintson a **Tovább** gombra. Az adatbázison alapuló bármelyik másik táblát is kiválaszthatja.

1. A **Céladattár** oldalon válassza ki az **Új kapcsolat létrehozása** elemet


1. Az **új társított szolgáltatás**területen keresse meg és válassza ki az **Azure Blob**elemet, majd kattintson a **Continue (folytatás**) gombra. 

   ![Blob-tároló kiválasztása](./media/tutorial-hybrid-copy-data-tool/select-destination-data-store.png)

1. Az **Új társított szolgáltatás (Azure Blob Storage)** párbeszédpanelen végezze el az alábbi lépéseket: 

   a. A **Név** mezőbe írja be az **AzureStorageLinkedService** nevet.

   b. A **Csatlakozás integrációs modulon keresztül** területen válassza ki a **TutorialIntegrationRuntime** értéket

   c. A **Tárfiók neve** alatt a legördülő menüből válassza ki a tárfiókja nevét. 

   d. Válassza a **Finish** (Befejezés) elemet.

1. Győződjön meg arról, hogy az **Azure Blob Storage** van kiválasztva a **célhely adattára** párbeszédpanelen. Ezután kattintson a **Tovább** gombra. 

1. **A kimeneti fájl vagy mappa kiválasztása** párbeszédpanelen a **Mappa elérési útja** alatt adja meg az **adftutorial/fromonprem** nevet. Az előfeltételek részeként létrehozott egy **adftutorial** nevű tárolót. Ha a kimeneti mappa nem létezik (jelen esetben a **fromonprem**), a Data Factory automatikusan létrehozza. Használhatja a **Tallózás** gombot is a blob Storage és a tárolók/mappák tallózásához. Ha nem ad meg semmilyen értéket a **Fájlnév** résznél, alapértelmezés szerint a forrásnevet fogja használni (jelen esetben a **dbo.emp** nevet).
           
   ![Kimeneti fájl vagy mappa kiválasztása](./media/tutorial-hybrid-copy-data-tool/choose-output-file-folder.png)

1. A **Fájlformátum beállításai** párbeszédpanelen kattintson a **Tovább** gombra. 

1. A **Beállítások** párbeszédpanelen kattintson a **Tovább** gombra. 

1. Az **Összefoglaló** párbeszédpanelen tekintse át az összes beállítás értékét, és kattintson a **Tovább** gombra. 

1. A létrehozott folyamat vagy feladat monitorozásához az **Üzembe helyezés** lapon válassza a **Monitorozás** elemet.

   ![Üzembe helyezés lap](./media/tutorial-hybrid-copy-data-tool/deployment-page.png)

1. A **Monitorozás** lapon megtekintheti a létrehozott folyamat állapotát. A **Művelet** oszlop hivatkozásaival megtekintheti a folyamat futásához társított tevékenységfuttatásokat, illetve újra futtathatja a folyamatot. 
   
1. Kattintson a **Műveletek** oszlopban található **Tevékenységfuttatások megtekintése** hivatkozásra a folyamat futásához társított tevékenységfuttatások megtekintéséhez. A másolási művelet részleteinek megtekintéséhez válassza a **Műveletek** oszlop **Részletek** hivatkozását (szemüveg ikon). Ha vissza szeretne váltani a **folyamat futási** nézetére, válassza a felső **folyamat futtatása** lehetőséget.

1. Ellenőrizze, hogy látja-e a kimeneti fájlt az **adftutorial** tároló **fromonprem** mappájában. 


1. A szerkesztő módra való váltáshoz kattintson a bal oldalon található **Szerkesztés** fülre. A szerkesztővel frissítheti a társított szolgáltatásokat, az adatkészleteket és az eszközzel létrehozott folyamatokat. A szerkesztőben megnyitott entitáshoz társított JSON-kód megtekintéséhez kattintson a **Kód** elemre. Az entitások Data Factory felhasználói felületen való szerkesztéséről [a jelen oktatóanyag Azure Portal-verziójában](tutorial-copy-data-portal.md) talál további információt.

   ![Szerkesztés lap](./media/tutorial-hybrid-copy-data-tool/edit-tab.png)


## <a name="next-steps"></a>További lépések
A példában szereplő folyamat adatokat másol egy helyszíni SQL Server-adatbázisból Blob Storage-tárolóba. Megismerte, hogyan végezheti el az alábbi műveleteket: 

> [!div class="checklist"]
> * Adat-előállító létrehozása
> * Folyamat létrehozása az Adatok másolása eszközzel.
> * A folyamat és a tevékenységek futásának monitorozása

A Data Factory által támogatott adattárak listáját a [támogatott adattárakat](copy-activity-overview.md#supported-data-stores-and-formats) ismertető cikk tartalmazza.

Az adatok egy forrásból egy célhelyre történő tömeges másolásának megismeréséhez lépjen a következő oktatóanyagra:

> [!div class="nextstepaction"]
>[Adatok tömeges másolása](tutorial-bulk-copy-portal.md)
