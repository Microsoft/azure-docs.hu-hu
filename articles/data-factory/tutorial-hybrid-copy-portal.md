---
title: Adatok másolása SQL Serverról blob Storage-ba a Azure Portal használatával
description: Megismerheti az adatok a helyszíni adattárolókból a felhőbe, az Azure Data Factory saját üzemeltetésű integrációs moduljával történő másolásának folyamatát.
ms.author: abnarain
author: nabhishek
ms.service: data-factory
ms.topic: tutorial
ms.custom: seo-lt-2019; seo-dt-2019
ms.date: 02/18/2021
ms.openlocfilehash: 4bfbd83f3f3910e1231bcce4043d9b59ccc512db
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "104606650"
---
# <a name="copy-data-from-a-sql-server-database-to-azure-blob-storage"></a>Adatok másolása SQL Server-adatbázisból az Azure Blob Storage-ba

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Ebben az oktatóanyagban a Azure Data Factory felhasználói felülettel (UI) létrehoz egy olyan adatfeldolgozó-folyamatot, amely egy SQL Server-adatbázisból másolja az Azure Blob Storage-ba. Létrehozhat és alkalmazhat egy saját üzemeltetésű integrációs modult, amely adatokat helyez át a helyszíni és a felhőalapú adattárolók között.

> [!NOTE]
> Ez a cikk nem mutatja be részletesen a Data Factory szolgáltatást. További információ: [A Data Factory bemutatása](introduction.md).

Az oktatóanyagban az alábbi lépéseket fogja végrehajtani:

> [!div class="checklist"]
> * Adat-előállító létrehozása
> * Hozzon létre egy saját üzemeltetésű integrációs modult.
> * SQL Server- és Azure Storage-beli társított szolgáltatások létrehozása.
> * SQL Server- és Azure Blob-adatkészletek létrehozása.
> * Folyamat létrehozása másolási tevékenységgel az adatok áthelyezéséhez
> * Folyamat futásának indítása
> * A folyamat futásának monitorozása.

## <a name="prerequisites"></a>Előfeltételek
### <a name="azure-subscription"></a>Azure-előfizetés
Ha még nem rendelkezik Azure-előfizetéssel, első lépésként [hozzon létre egy ingyenes](https://azure.microsoft.com/free/) fiókot.

### <a name="azure-roles"></a>Azure-szerepkörök
A adat-előállító példányok létrehozásához az Azure-ba való bejelentkezéshez használt felhasználói fióknak *közreműködői* vagy *tulajdonosi* szerepkörrel kell rendelkeznie, vagy az Azure-előfizetés *rendszergazdájának* kell lennie.

Az előfizetésben található engedélyek megtekintéséhez lépjen be az Azure Portalra. Válassza ki a felhasználónevet a jobb felső sarokban, majd válassza az **Engedélyek** elemet. Ha több előfizetéshez is rendelkezik hozzáféréssel, válassza ki a megfelelő előfizetést. A felhasználók szerepkörhöz való hozzáadásával kapcsolatos útmutatásért lásd: Azure- [szerepkörök kiosztása a Azure Portal használatával](../role-based-access-control/role-assignments-portal.md).

### <a name="sql-server-2014-2016-and-2017"></a>SQL Server 2014, 2016 és 2017
Ebben az oktatóanyagban egy SQL Server-adatbázist használ *forrásként* szolgáló adattárként. Az oktatóanyagban létrehozott adatfeldolgozó folyamata a SQL Server-adatbázisból (forrás) a blob Storage-ba (fogadóba) másolja az adatait. Ezután hozzon létre egy **EMP** nevű táblát a SQL Server-adatbázisban, és szúrjon be néhány minta bejegyzést a táblába.

1. Indítsa el az SQL Server Management Studiót. Ha még nincs telepítve a számítógépen, tekintse meg az [SQL Server Management Studio letöltését ismertető](/sql/ssms/download-sql-server-management-studio-ssms) cikket.

1. Csatlakozzon az SQL Server-példányához a hitelesítő adataival.

1. Hozzon létre egy mintaadatbázist. A fanézetben kattintson a jobb gombbal a **Databases** (Adatbázisok) elemre, majd válassza a **New Database** (Új adatbázis) elemet.
1. Az **New Database** (Új adatbázis) ablakban adjon nevet az új adatbázisnak, majd kattintson az **OK** gombra.

1. Az **EMP** tábla létrehozásához és a mintaadatok beszúrásához futtassa a következő lekérdezési szkriptet az adatbázison. A fanézetben kattintson a jobb gombbal a létrehozott adatbázisra, majd válassza a **New Query** (Új lekérdezés) elemet.

   ```
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
Ebben az oktatóanyagban egy általános célú Azure Storage-fiókot (ebben az esetben blobtárolót) használunk cél-/fogadóadattárként. Ha még nem rendelkezik általános célú Azure Storage-fiókkal, tekintse meg a [Tárfiók létrehozását](../storage/common/storage-account-create.md) ismertető cikket. Az oktatóanyagban létrehozott adatfeldolgozó folyamata az SQL Server-adatbázisból (forrás) a blob Storage-ba (fogadóba) másolja az adatait. 

#### <a name="get-the-storage-account-name-and-account-key"></a>Tárfióknév és fiókkulcs beszerzése
Ebben az oktatóanyagban a tárfiók nevét és kulcsát használjuk. A tárfiók nevét és kulcsát az alábbi lépéseket követve szerezheti be:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com) az Azure-ban használt felhasználónevével és jelszavával.

1. A bal oldali panelen válassza a **Minden szolgáltatás** lehetőséget. Szűrjön rá a **Tárolás** kulcsszóra, majd válassza a **Tárfiókok** lehetőséget.

    ![Tárfiók keresése](media/doc-common-process/search-storage-account.png)

1. A Storage-fiókok listájában szükség esetén szűrje a Storage-fiókját. Ezután válassza ki a tárfiókot.

1. A **Tárfiók** ablakban válassza a **Hozzáférési kulcsok** elemet.

1. Másolja a **Tárfiók neve** és **1. kulcs** mező értékét, majd illessze be őket egy jegyzettömbbe vagy más szerkesztőbe az oktatóanyag későbbi részeiben történő használatra.

#### <a name="create-the-adftutorial-container"></a>Adftutorial tároló létrehozása
Ebben a szakaszban egy **adftutorial** nevű blobtárolót hoz létre a Blob Storage-ban.

1. A **Storage-fiók** ablakban lépjen az **Áttekintés** elemre, majd válassza a **tárolók** lehetőséget.

    ![A Blobok elem választása](media/tutorial-hybrid-copy-powershell/select-blobs.png)

1. Hozzon létre egy újat a **tárolók** ablakban a **+ tároló** elemre kattintva.

1. Az **Új tároló** ablak **Név** mezőjébe írja be az **adftutorial** nevet. Ezután kattintson a **Létrehozás** elemre.

1. A tárolók listájában válassza ki az imént létrehozott **adftutorial** .

1. Tartsa meg a **tároló** ablakát a **adftutorial** megnyitásához. Ezzel ellenőrizheti az oktatóanyag végén található kimenetet. A Data Factory automatikusan létrehozza a kimeneti mappát a tárolóban, így nem kell újat létrehoznia.

## <a name="create-a-data-factory"></a>Adat-előállító létrehozása
Ebben a lépésben létrehoz egy adat-előállítót, és elindítja a Data Factory felhasználói felületét, hogy létrehozzon egy folyamatot az adat-előállítóban.

1. Nyissa meg a **Microsoft Edge** vagy a **Google Chrome** böngészőt. A Data Factory felhasználói felületének használata jelenleg csak a Microsoft Edge-ben és a Google Chrome-ban támogatott.
1. A bal oldali menüben válassza az **erőforrás**-  >  **integráció** létrehozása  >  **Data Factory**:

   ![Data Factory kiválasztása az „Új” ablaktáblán](./media/doc-common-process/new-azure-data-factory-menu.png)

1. Az **Új adat-előállító** lap **Név** mezőjében adja meg az **ADFTutorialDataFactory** értéket.

   Az adat-előállító nevének *globálisan egyedinek* kell lennie. Ha a Név mezőnél az alábbi hibaüzenet jelenik meg, módosítsa az adat-előállító nevét (például a következőre: sajátneveADFTutorialDataFactory). A Data Factory-összetevők elnevezési szabályait a [Data Factory elnevezési szabályait](naming-rules.md) ismertető cikkben találja.

    :::image type="content" source="./media/doc-common-process/name-not-available-error.png" alt-text="Új adatelőállító-hibaüzenet az ismétlődő névhez.":::

1. Válassza ki azt az **Azure-előfizetést**, amelyben az adat-előállítót létre szeretné hozni.
1. **Erőforráscsoport:** hajtsa végre a következő lépések egyikét:

   - Válassza a **meglévő használata** lehetőséget, majd válasszon ki egy meglévő erőforráscsoportot a legördülő listából.

   - Válassza az **új létrehozása** lehetőséget, és adja meg az erőforráscsoport nevét.
        
     Az erőforráscsoportokkal kapcsolatos információkért tekintse meg az [Erőforráscsoportok használata az Azure-erőforrások kezeléséhez](../azure-resource-manager/management/overview.md) ismertető cikket.
1. A **Verzió** résznél válassza a **V2** értéket.
1. A **hely** területen válassza ki az adat-előállító helyét. A legördülő listán csak a támogatott helyek jelennek meg. A Data Factory által használt adattárak (például a Storage és az SQL Database) és számítási erőforrások (például az Azure HDInsight) más régiókban is lehetnek.
1. Válassza a **Létrehozás** lehetőséget.

1. A létrehozás befejezése után megjelenik a **Data Factory** lap a képen látható módon:

    :::image type="content" source="./media/doc-common-process/data-factory-home-page.png" alt-text="A Azure Data Factory kezdőlapja, a szerző & monitor csempével.":::
1. A Data Factory felhasználói felületének külön lapon történő elindításához válassza a **Létrehozás és monitorozás** csempét.


## <a name="create-a-pipeline"></a>Folyamat létrehozása

1. Az **Első lépések** lapon válassza a **Folyamat létrehozása** lehetőséget. A rendszer automatikusan létrehoz egy folyamatot. A folyamat fanézetben jelenik meg, és megnyílik a szerkesztő is.

   ![Első lépések lap](./media/doc-common-process/get-started-page.png)

1. A **Tulajdonságok** terület általános paneljén adja meg  a SQLServerToBlobPipeline **nevet**. Ezután csukja össze a panelt a jobb felső sarokban található tulajdonságok ikonra kattintva.

1. A **tevékenységek** eszközben bontsa ki az **Áthelyezés & átalakítás** elemet. Húzza a **Másolás** tevékenységet a folyamat tervezési felületére. Állítsa a tevékenység nevét a következőre: **CopySqlServerToAzureBlobActivity**.

1. A **Tulajdonságok** ablakban lépjen a **Forrás** lapra, és válassza a **+ Új** elemet.

1. Az **új adatkészlet** párbeszédpanelen keressen **SQL Server**. Válassza a **SQL Server** lehetőséget, majd kattintson a **Folytatás** gombra.
    ![Új SqlServer adatkészlet](./media/tutorial-hybrid-copy-portal/create-sqlserver-dataset.png)

1. A **tulajdonságok beállítása** párbeszédpanel Név mezőjébe írja be a **SqlServerDataset** **nevet**. A **társított szolgáltatás** területen válassza az **+ új** lehetőséget. Ebben a lépésben egy kapcsolatot hoz létre a forrásadatkészlettel (az SQL Server-adatbázissal).

1. Az **új társított szolgáltatás** párbeszédpanelen adja hozzá a **nevet** a **SqlServerLinkedService** mezőhöz. A **Csatlakozás Integration Runtime használatával** területen válassza az **+ új** lehetőséget.  Ebben a szakaszban egy saját üzemeltetésű Integration Runtime átjárót hozhat létre, és társíthatja azt az SQL Server-adatbázist futtató helyszíni géppel. A saját üzemeltetésű integrációs modul az a komponens, amely adatokat másol a gépen futó SQL Server-adatbázisból a Blob Storage-ba.

1. A **Integration Runtime telepítés** párbeszédpanelen válassza a **saját** üzemeltetésű lehetőséget, majd kattintson a **Folytatás** gombra.

1. A név mezőben adja meg a **TutorialIntegrationRuntime**. Ezután kattintson a **Létrehozás** elemre.

1. A beállítások lapon **kattintson ide a számítógép expressz telepítésének elindításához**. Ez a művelet telepíti az integrációs modult a számítógépére, és regisztrálja azt a Data Factoryban. Használhatja a manuális telepítési lehetőséget is. Ehhez töltse le a telepítőfájlt, futtassa, majd a kulccsal regisztrálja az integrációs modult.
    ![Integrációs modul telepítése](./media/tutorial-hybrid-copy-portal/intergration-runtime-setup.png)

1. A **Integration Runtime (helyi) expressz telepítés** ablakban válassza a **Bezárás** lehetőséget a folyamat befejeződése után.

    ![Az integrációs modul (saját üzemeltetésű) expressz telepítése](./media/tutorial-hybrid-copy-portal/integration-runtime-setup-successful.png)

1. Az **új társított szolgáltatás (SQL Server)** párbeszédpanelen ellenőrizze, hogy a **TutorialIntegrationRuntime** van-e kiválasztva a **Csatlakozás Integration Runtime használatával**. Ezután hajtsa végre a következő lépéseket:

    a. A **Név** mezőben adja meg az **SqlServerLinkedService** nevet.

    b. A **Kiszolgáló neve** alatt adja meg az SQL Server-példány nevét.

    c. Az **Adatbázis neve** alatt adja meg az **emp** táblával rendelkező adatbázis nevét.

    d. A **Hitelesítés típusa** alatt válassza ki azt a hitelesítési típust, amelyet a Data Factorynak az SQL Server-adatbázishoz történő csatlakozáshoz használnia kell.

    e. A **Felhasználónév** és a **Jelszó** alatt adja meg a felhasználónevet és a jelszót. Szükség esetén használja a *SajátTartomány \\ myuser* .

    f. Válassza a **Kapcsolat tesztelése** elemet. Ezzel a lépéssel ellenőrizheti, hogy Data Factory tud-e csatlakozni a SQL Server-adatbázishoz a létrehozott saját üzemeltetésű integrációs modul használatával.

    : A társított szolgáltatás mentéséhez válassza a **Létrehozás** lehetőséget.
 
    ![Új társított szolgáltatás (SQL Server)](./media/tutorial-hybrid-copy-portal/new-sqlserver-linked-service.png)

1. A társított szolgáltatás létrehozása után vissza fog térni a SqlServerDataset **tulajdonságok beállítása** lapjára. Tegye a következőket:

    a. Ellenőrizze, hogy a **Társított szolgáltatás** mezőben látható-e az **SqlServerLinkedService** szolgáltatás.

    b. A **tábla neve** területen válassza a **[dbo] elemet. [ EMP]**.
    
    c. Válassza az **OK** lehetőséget.

1. Lépjen az **SQLServerToBlobPipeline** folyamatot tartalmazó lapra, vagy válassza az **SQLServerToBlobPipeline** folyamatot a fanézetben.

1. Lépjen a **Fogadó** lapra a **Tulajdonságok** ablak alján, és válassza a **+ Új** elemet.

1. Az **új adatkészlet** párbeszédpanelen válassza az **Azure Blob Storage** elemet. Ezután válassza a **Folytatás** elemet.

1. A **formátum kiválasztása** párbeszédpanelen válassza ki az adatai formátumának típusát. Ezután válassza a **Folytatás** elemet.

    ![Adatformátum kiválasztása](./media/doc-common-process/select-data-format.png)

1. A **Tulajdonságok megadása** párbeszédpanelen adja meg a **AzureBlobDataset** nevet. A **Társított szolgáltatások** szövegbeviteli mező mellett válassza az **+ Új** lehetőséget.

1. Az **új társított szolgáltatás (Azure Blob Storage)** párbeszédpanelen írja be a **AzureStorageLinkedService** nevet, válassza ki a Storage-fiók nevét a **Storage-fiók** neve listából. Tesztelje a kapcsolódást, majd válassza a **Létrehozás** lehetőséget a társított szolgáltatás telepítéséhez.

1. A társított szolgáltatás létrehozása után vissza kell térnie a **set Properties (tulajdonságok beállítása** ) lapra. Válassza az **OK** lehetőséget.

1. Nyissa meg a fogadó adatkészletet. A **Kapcsolat** lapon hajtsa végre az alábbi lépéseket:

    a. Ellenőrizze, hogy a **Társított szolgáltatás** részen az **AzureStorageLinkedService** van-e kiválasztva.

    b. A **fájl elérési útja** mezőbe írja be a **adftutorial/Fromonprem** értéket a **tároló/könyvtár** részhez. Ha a kimeneti mappa nem létezik az adftutorial tárolóban, a Data Factory automatikusan létrehozza azt.

    c. A **fájl** résznél válassza a **dinamikus tartalom hozzáadása** lehetőséget.
    ![dinamikus kifejezés a fájlnév feloldásához](./media/tutorial-hybrid-copy-portal/file-name.png)

    d. Adja hozzá `@CONCAT(pipeline().RunId, '.txt')` , majd válassza a **Befejezés** lehetőséget. Ez a művelet átnevezi a fájlt a PipelineRunID.txt.

1. Lépjen a megnyitott folyamatot tartalmazó lapra, vagy válassza ki a folyamatot a fanézetben. Ellenőrizze, hogy a **Fogadó-adatkészlet** mezőben az **AzureBlobDataset** érték van-e kiválasztva.

1. A folyamat beállításainak érvényesítéséhez válassza az **Érvényesítés** elemet a folyamat eszköztárán. A **cső érvényesítési kimenetének** bezárásához válassza a **>>** ikont.
    ![folyamat ellenőrzése](./media/tutorial-hybrid-copy-portal/validate-pipeline.png)
    

1. A Data Factory létrehozott entitások közzétételéhez válassza az **összes közzététele** lehetőséget.

1. Várjon, amíg megjelenik a **Közzététel befejeződött** előugró ablak. A közzététel állapotának megtekintéséhez jelölje be az **értesítések megjelenítése** hivatkozást az ablak tetején. Az értesítési ablak bezárásához válassza a **Bezárás** elemet.


## <a name="trigger-a-pipeline-run"></a>Folyamat futtatásának aktiválása
Válassza az **trigger hozzáadása** elemet a folyamat eszköztárán, majd válassza az **aktiválás most** lehetőséget.

## <a name="monitor-the-pipeline-run"></a>A folyamat futásának monitorozása

1. Lépjen a **figyelés** lapra. Megjelenik az előző lépésben manuálisan aktivált folyamat.

1. A folyamat futtatásához társított tevékenységek megtekintéséhez válassza a **SQLServerToBlobPipeline** lehetőséget a *folyamat neve* alatt. 
    ![Folyamatfuttatások monitorozása](./media/tutorial-hybrid-copy-portal/pipeline-runs.png)

1. A **tevékenység-futtatások** lapon válassza a részletek (szemüvegek képe) hivatkozást a másolási művelet részleteinek megtekintéséhez. Ha vissza szeretne térni a folyamat futtatási nézetéhez, válassza a felül lévő **összes folyamat futtatása** lehetőséget.

## <a name="verify-the-output"></a>Kimenet ellenőrzése
A folyamat automatikusan létrehozza a *fromonprem* nevű kimeneti mappát az `adftutorial` blobtárolóban. Ellenőrizze, hogy a *[pipeline().RunId].txt* fájl megjelenik-e a kimeneti mappában.


## <a name="next-steps"></a>Következő lépések
A példában szereplő folyamat adatokat másol az egyik helyről egy másikra a Blob Storage-ban. Megtanulta végrehajtani az alábbi műveleteket:

> [!div class="checklist"]
> * Adat-előállító létrehozása
> * Hozzon létre egy saját üzemeltetésű integrációs modult.
> * SQL Server- és Storage-beli társított szolgáltatások létrehozása.
> * SQL Server- és Blob Storage-adatkészletek létrehozása.
> * Folyamat létrehozása másolási tevékenységgel az adatok áthelyezéséhez
> * Folyamat futásának indítása
> * A folyamat futásának monitorozása.

A Data Factory által támogatott adattárak listáját a [támogatott adattárakat](copy-activity-overview.md#supported-data-stores-and-formats) ismertető cikk tartalmazza.

Az adatok egy forrásból egy célhelyre történő tömeges másolásának megismeréséhez lépjen a következő oktatóanyagra:

> [!div class="nextstepaction"]
>[Adatok tömeges másolása](tutorial-bulk-copy-portal.md)