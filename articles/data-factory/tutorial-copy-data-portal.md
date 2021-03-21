---
title: Adatfeldolgozó folyamat létrehozása a Azure Portal használatával
description: Az oktatóanyag részletes útmutatását követve létrehozhat egy adat-előállítót egy folyamat és az Azure Portal használatával. A folyamat a másolási tevékenységet használja az adatok Azure Blob Storage-ból Azure SQL Databaseba való másolásához.
author: linda33wj
ms.service: data-factory
ms.topic: tutorial
ms.custom: seo-lt-2019
ms.date: 02/18/2021
ms.author: jingwang
ms.openlocfilehash: 08ab3c9181f4fdcfc9b55bf80c53f88ff26f90ce
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "104584389"
---
# <a name="copy-data-from-azure-blob-storage-to-a-database-in-azure-sql-database-by-using-azure-data-factory"></a>Adatok másolása az Azure Blob Storage-ból egy Azure SQL Database-adatbázisba Azure Data Factory használatával

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Ebben az oktatóanyagban az Azure Data Factory felhasználói felületének használatával hoz létre egy adat-előállítót. Az adatfeldolgozó folyamata az Azure Blob Storage-ból származó adatok másolását Azure SQL Database-adatbázisba másolja. Az oktatóanyagban szereplő konfigurációs minta fájlalapú adattárból relációs adattárba való másolásra vonatkozik. A forrásként és fogadóként támogatott adattárak listája a [támogatott adattárakat tartalmazó](copy-activity-overview.md#supported-data-stores-and-formats) táblázatban található.

> [!NOTE]
> Ha még csak ismerkedik a Data Factory használatával, olvassa el [az Azure Data Factory használatának első lépéseit](introduction.md) ismertető cikket.

Az oktatóanyagban az alábbi lépéseket fogja végrehajtani:

> [!div class="checklist"]
> * Adat-előállító létrehozása
> * Másolási tevékenységgel rendelkező folyamat létrehozása.
> * A folyamat próbafuttatása
> * A folyamat manuális aktiválása
> * A folyamat aktiválása ütemezés szerint
> * A folyamat és a tevékenységek futásának monitorozása

## <a name="prerequisites"></a>Előfeltételek
* **Azure-előfizetés**. Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes Azure-fiókot](https://azure.microsoft.com/free/) a virtuális gép létrehozásának megkezdése előtt.
* **Azure Storage-fiók**. A Blob Storage lesz használatban *forrás* adattárként. Ha még nem rendelkezik tárfiókkal, tekintse meg az [Azure Storage-fiók létrehozásának](../storage/common/storage-account-create.md) lépéseit ismertető cikket.
* **Azure SQL Database**. Ezt az adatbázist használjuk *fogadó* adattárként. Ha nem rendelkezik Azure SQL Database-adatbázissal, tekintse meg az [adatbázis létrehozása a Azure SQL Databaseban](../azure-sql/database/single-database-create-quickstart.md) című témakört a létrehozásához szükséges lépésekért.

### <a name="create-a-blob-and-a-sql-table"></a>Blob és SQL-tábla létrehozása

Készítse elő a Blob Storage-et és az SQL Database-t az oktatóanyaghoz a következő lépésekkel.

#### <a name="create-a-source-blob"></a>Forrás blob létrehozása

1. Indítsa el a Jegyzettömböt. Másolja be a következő szöveget, és mentse **emp.txt** néven egy fájlba a lemezen.

    ```
    FirstName,LastName
    John,Doe
    Jane,Doe
    ```

1. Hozzon létre egy **adftutorial** nevű tárolót a Blob Storage-ban. Ebben a tárolóban hozzon létre egy **input** nevű mappát. Ezután töltse fel az **emp.txt** fájlt az **input** mappába. Ezekhez a feladatokhoz használja az Azure Portalt vagy olyan eszközöket, mint az [Azure Storage Explorer](https://storageexplorer.com/).

#### <a name="create-a-sink-sql-table"></a>Fogadó SQL-tábla létrehozása

1. A következő SQL-szkripttel hozza létre a **dbo. EMP** táblát az adatbázisban:

    ```sql
    CREATE TABLE dbo.emp
    (
        ID int IDENTITY(1,1) NOT NULL,
        FirstName varchar(50),
        LastName varchar(50)
    )
    GO

    CREATE CLUSTERED INDEX IX_emp_ID ON dbo.emp (ID);
    ```

1. Engedélyezze az SQL Server elérését az Azure-szolgáltatások számára. Győződjön meg arról, hogy az **Azure-szolgáltatásokhoz való hozzáférés engedélyezése****BE** van kapcsolva az SQL Serverhez, hogy a Data Factory tudjon adatokat írni az SQL Serverre. A beállítás ellenőrzéséhez és bekapcsolásához lépjen a logikai SQL Server > áttekintés > **a** kiszolgáló tűzfalának beállítása> az **Azure-szolgáltatásokhoz való hozzáférés engedélyezése** lehetőségre.

## <a name="create-a-data-factory"></a>Adat-előállító létrehozása
Ebben a lépésben létrehoz egy adat-előállítót, és elindítja a Data Factory felhasználói felületét, hogy létrehozzon egy folyamatot az adat-előállítóban.

1. Nyissa meg a **Microsoft Edge** vagy a **Google Chrome böngészőt**. A Data Factory felhasználói felületének használata jelenleg csak a Microsoft Edge-ben és a Google Chrome-ban támogatott.
2. A bal oldali menüben válassza az **erőforrás létrehozása**  >  **integráció**  >  **Data Factory** elemet.
3. A **Data Factory létrehozása** lap **alapok** lapján válassza ki azt az Azure- **előfizetést** , amelyben létre kívánja hozni az adat-előállítót.
4. **Erőforráscsoport:** hajtsa végre a következő lépések egyikét:

    a. Válasszon ki egy meglévő erőforráscsoportot a legördülő listából.

    b. Válassza az **új létrehozása** lehetőséget, és adja meg egy új erőforráscsoport nevét.
    
    Az erőforráscsoportokkal kapcsolatos információkért tekintse meg az [Erőforráscsoportok használata az Azure-erőforrások kezeléséhez](../azure-resource-manager/management/overview.md) ismertető cikket. 
5. A **régió** területen válassza ki az adat-előállító helyét. A legördülő listán csak a támogatott helyek jelennek meg. Az adat-előállítók által használt adattárak (például az Azure Storage és az SQL Database) és számítási erőforrások (például az Azure HDInsight) más régiókban is lehetnek.
6. A **név** mezőben adja meg a **ADFTutorialDataFactory**.

   Az Azure-beli adatgyár nevének *globálisan egyedinek* kell lennie. Ha a név értékével kapcsolatos hibaüzenet kap, adjon meg másik nevet az adat-előállítóhoz. (például Sajátneveadftutorialdatafactory). A Data Factory-összetevők elnevezési szabályait a [Data Factory elnevezési szabályait](naming-rules.md) ismertető cikkben találja.

    :::image type="content" source="./media/doc-common-process/name-not-available-error.png" alt-text="Új adatelőállító-hibaüzenet az ismétlődő névhez.":::

7. A **Verzió** résznél válassza a **V2** értéket.
8. Válassza a fent látható **git-konfiguráció** fület, majd jelölje be a **git konfigurálása később** jelölőnégyzetet.
9. Válassza a **felülvizsgálat + létrehozás** lehetőséget, majd az érvényesítés után válassza a **Létrehozás** lehetőséget.
10. A létrehozás befejezése után megjelenik az értesítési központban megjelenő értesítés. Válassza az **Ugrás az erőforráshoz** lehetőséget, hogy megnyissa az adatfeldolgozó lapot.
11. Válassza a **szerző & a figyelő** lehetőséget, hogy elindítsa a Azure Data Factory felhasználói felületet egy külön lapon.


## <a name="create-a-pipeline"></a>Folyamat létrehozása
Ebben a lépésben létrehoz egy másolási tevékenységgel rendelkező folyamatot az adat-előállítóban. A másolási tevékenység adatokat másol egy Blob Storage-ból egy SQL Database-be. A [rövid útmutatóban](quickstart-create-data-factory-portal.md) létrehozott egy folyamatot az alábbi lépéseket követve:

1. Hozza létre a társított szolgáltatást.
1. Hozzon létre bemeneti és kimeneti adatkészleteket.
1. Folyamat létrehozása.

Ebben az oktatóanyagban először létrehozzuk a folyamatot. Ezután társított szolgáltatásokat és adatkészleteket hozunk létre, amikor szükség van rájuk a folyamat konfigurálásához.

1. Az **Első lépések** lapon válassza a **Folyamat létrehozása** lehetőséget.

   ![Folyamat létrehozása](./media/doc-common-process/get-started-page.png)

1. A **Tulajdonságok** terület általános paneljén adja meg  a CopyPipeline **nevet**. Ezután csukja össze a panelt a jobb felső sarokban található tulajdonságok ikonra kattintva.

1. A **tevékenységek** eszközben bontsa ki az **áthelyezés és átalakítás** kategóriát, majd húzza a **adatok másolása** tevékenységet az eszközkészletből a folyamat tervező felületére. Adja meg a **CopyFromBlobToSql** értéket a **Név** mezőben.

    ![Másolási tevékenység](./media/tutorial-copy-data-portal/drag-drop-copy-activity.png)

### <a name="configure-source"></a>Forrás konfigurálása

>[!TIP]
>Ebben az oktatóanyagban a *fiók kulcsát* használja a forrás adattároló hitelesítési típusaként, de más támogatott hitelesítési módszereket is választhat: *sas URI*,*egyszerű szolgáltatásnév* és *felügyelt identitás* , ha szükséges. A részletekért tekintse meg a [cikk](./connector-azure-blob-storage.md#linked-service-properties) megfelelő részeit.
>Az adattárak titkos kulcsainak biztonságos tárolásához ajánlott egy Azure Key Vault is használni. A részletes illusztrációkat [ebben a cikkben](./store-credentials-in-key-vault.md) találja.

1. Nyissa meg a **forrás** lapot. Forrás adatkészlet létrehozásához válassza az **+ új** lehetőséget.

1. Az **új adatkészlet** párbeszédpanelen válassza az **Azure Blob Storage** lehetőséget, majd kattintson a **Folytatás** gombra. A forrásadatok egy Blob Storage-ban vannak, tehát forrásadatkészletként az **Azure Blob Storage**-ot válassza.

1. A **formátum kiválasztása** párbeszédpanelen válassza ki az adatformátum típusát, majd kattintson a **Continue (folytatás**) elemre.

1. A **Tulajdonságok megadása** párbeszédpanelen adja meg a **sourceblobdataset lehetőség** nevet. Jelölje be az **első sor jelölőnégyzetét fejlécként**. A **társított szolgáltatás** szövegmezőben válassza az **+ új** lehetőséget.

1. Az **új társított szolgáltatás (Azure Blob Storage)** párbeszédpanelen írja be a **AzureStorageLinkedService** nevet, válassza ki a Storage-fiók nevét a **Storage-fiók neve** listából. A kapcsolat tesztelése lapon válassza a **Létrehozás** lehetőséget a társított szolgáltatás telepítéséhez.

1. A társított szolgáltatás létrehozása után a rendszer a **tulajdonságok beállítása** lapra navigál. A **Fájl elérési útja** mellett válassza a **Tallózás** lehetőséget.

1. Keresse meg a **adftutorial/input** mappát, válassza ki a **emp.txt** fájlt, majd kattintson az **OK gombra**.

1. Válassza az **OK** lehetőséget. Automatikusan megnyitja a folyamat lapot. A **forrás** lapon ellenőrizze, hogy a **sourceblobdataset lehetőség** van-e kiválasztva. A lapon lévő adatok előnézetének megtekintéséhez válassza az **Adatok előnézete** elemet.

    ![Forrásadatkészlet](./media/tutorial-copy-data-portal/source-dataset-selected.png)

### <a name="configure-sink"></a>Fogadó konfigurálása
>[!TIP]
>Ebben az oktatóanyagban az *SQL-hitelesítést* használja a fogadó adattár hitelesítési típusaként, de más támogatott hitelesítési módszereket is választhat: az *egyszerű szolgáltatásnév* és a *felügyelt identitás* , ha szükséges. A részletekért tekintse meg a [cikk](./connector-azure-sql-database.md#linked-service-properties) megfelelő részeit.
>Az adattárak titkos kulcsainak biztonságos tárolásához ajánlott egy Azure Key Vault is használni. A részletes illusztrációkat [ebben a cikkben](./store-credentials-in-key-vault.md) találja.

1. Váltson a **Fogadó** lapra, és válassza az **+ Új** elemet egy fogadó-adatkészlet létrehozásához.

1. Az **új adatkészlet** párbeszédpanelen írja be az "SQL" kifejezést a keresőmezőbe az összekötők szűréséhez, válassza a **Azure SQL Database** lehetőséget, majd kattintson a **Folytatás** gombra. Ebben az oktatóanyagban adatokat másol egy SQL Database-be.

1. A **Tulajdonságok megadása** párbeszédpanelen adja meg a **outputsqldataset érték** nevet. A **társított szolgáltatás** legördülő listából válassza az **+ új** lehetőséget. Az adatkészleteket mindig társítani kell egy társított szolgáltatáshoz. A társított szolgáltatás rendelkezik azzal a kapcsolati karakterlánccal, amelyet Data Factory használ a SQL Databasehoz való kapcsolódáshoz futásidőben. Az adatkészlet meghatározza azt a tárolót, mappát és fájlt (az utóbbi nem kötelező), ahova a rendszer az adatokat másolja.

1. Az **új társított szolgáltatás (Azure SQL Database)** párbeszédpanelen hajtsa végre a következő lépéseket:

    a. A **Név** mezőbe írja az **AzureSqlDatabaseLinkedService** nevet.

    b. A **Kiszolgáló neve** mezőben válassza ki az SQL Server-példányát.

    c. Az **adatbázis neve** területen válassza ki az adatbázist.

    d. A **Felhasználónév** mezőben adja meg a felhasználó nevét.

    e. A **Jelszó** mezőben adja meg a felhasználó jelszavát.

    f. A kapcsolat teszteléséhez válassza a **Kapcsolat tesztelése** elemet.

    : Válassza a **Létrehozás** lehetőséget a társított szolgáltatás telepítéséhez.

    ![Új társított szolgáltatás mentése](./media/tutorial-copy-data-portal/new-azure-sql-linked-service-window.png)

1. Automatikusan megnyitja a **tulajdonságok beállítása** párbeszédpanelt. A **Tábla** területen válassza a **[dbo].[emp]** elemet. Ez után válassza az **OK** gombot.

1. Lépjen a folyamatot tartalmazó lapra, és győződjön meg arról, hogy a **Fogadóadattár** mezőben az **OutputSqlDataset** érték van kiválasztva.

    ![Folyamat lap](./media/tutorial-copy-data-portal/pipeline-tab-2.png)       

A forrás sémáját leképezheti a cél megfelelő sémájára a [másolási tevékenységben lévő séma-hozzárendelés](copy-activity-schema-and-type-mapping.md)után.

## <a name="validate-the-pipeline"></a>A folyamat érvényesítése
A folyamat érvényesítéséhez válassza az **Érvényesítés** elemet az eszköztáron.

A folyamathoz társított JSON-kód megtekintéséhez kattintson a jobb felső sarokban található **kód** elemre.

## <a name="debug-and-publish-the-pipeline"></a>A folyamat hibakeresése és közzététele
Elvégezheti a folyamat hibakeresését, mielőtt összetevőket (társított szolgáltatások, adatkészletek és folyamat) tenne közzé a Data Factoryben vagy a saját Azure Repos Git-adattárában.

1. A folyamat hibakereséséhez válassza a **Hibakeresés** elemet az eszköztáron. A folyamat futtatási állapotát az ablak alján található **Kimenet** lapon tekintheti meg.

1. Ha a folyamat sikeresen futtatható, a felső eszköztáron válassza az **összes közzététele** lehetőséget. Ez a művelet közzéteszi a létrehozott entitásokat (adatkészleteket és folyamatokat) a Data Factoryben.

1. Várjon, amíg megjelenik a **Sikeres közzététel** üzenet. Az értesítési üzenetek megtekintéséhez kattintson az **Értesítések megjelenítése** elemre (csengő gomb) a jobb felső sarokban.

## <a name="trigger-the-pipeline-manually"></a>A folyamat manuális aktiválása
Ebben a lépésben manuálisan fogja aktiválni az előző lépésben közzétett folyamatot.

1. Kattintson az **Aktiválás** gombra az eszköztáron, majd válassza az **Aktiválás most** lehetőséget. A **folyamat futtatása** lapon kattintson az **OK gombra**.  

1. Lépjen a bal oldali **Figyelés** lapra. Itt láthat egy manuális eseményindító által aktivált folyamatfuttatást. A **folyamat neve** oszlopban található hivatkozások használatával megtekintheti a tevékenységek részleteit, és újra futtathatja a folyamatot.

    [![Folyamatfuttatások monitorozása](./media/tutorial-copy-data-portal/monitor-pipeline-inline-and-expended.png)](./media/tutorial-copy-data-portal/monitor-pipeline-inline-and-expended.png#lightbox)

1. A folyamat futtatásához társított tevékenységek megtekintéséhez jelölje ki a **CopyPipeline** hivatkozást a **folyamat neve** oszlopban. Ebben a példában csak egy tevékenység van, így csak egy bejegyzés jelenik meg a listában. A másolási művelet részleteinek megtekintéséhez kattintson a **tevékenység neve** oszlop **részletek** hivatkozására (szemüveg ikon). Válassza a felül található **összes folyamat futtatása** lehetőséget a folyamat futási nézetének visszalépéséhez. A nézet frissítéséhez válassza a **Frissítés** parancsot.

    [![Tevékenységfuttatások monitorozása](./media/tutorial-copy-data-portal/view-activity-runs-inline-and-expended.png)](./media/tutorial-copy-data-portal/view-activity-runs-inline-and-expended.png#lightbox)

1. Ellenőrizze, hogy az adatbázis **EMP** táblájában két további sor van-e hozzáadva.

## <a name="trigger-the-pipeline-on-a-schedule"></a>A folyamat aktiválása ütemezés szerint
Ebben az ütemezésben egy ütemezési eseményindítót fog létrehozni a folyamathoz. Az eseményindító a meghatározott ütemezés (például óránként vagy naponta) szerint futtatja a folyamatot. Itt úgy állíthatja be a triggert, hogy percenként fusson a megadott záró dátumig.

1. Lépjen a bal oldali Monitorozás lap feletti **Létrehozás** lapra.

1. Lépjen a folyamathoz, kattintson az eszköztáron az **Aktiválás**, majd az **Új/Szerkesztés** lehetőségre.

1. Az **Eseményindítók hozzáadása** párbeszédpanelen válassza az **+ új** lehetőséget az **eseményindító-területek kiválasztásához** .

1. Az **Új eseményindító** ablakban hajtsa végre az alábbi lépéseket:

    a. A **Név** mezőbe írja a **RunEveryMinute** nevet.

    b. Frissítse az trigger **kezdő dátumát** . Ha a dátum korábbi az aktuális dátum/idő, az trigger a módosítás közzétételekor lép érvénybe. 

    c. Az **időzóna** területen válassza a legördülő listát.

    d. Állítsa be az **ismétlődést** **1 percenként**.

    e. Jelölje be a **befejezési dátum megadása** jelölőnégyzetet, és a szakasz **végének** frissítése után néhány perccel az aktuális dátum és idő értéket. Az eseményindító csak a módosítások közzététele után lesz aktív. Ha úgy állítja be, hogy csak néhány percre van beállítva, és nem teszi közzé azt, akkor nem jelenik meg a trigger futtatása.

    f. Az **aktivált** beállításnál válassza az **Igen** lehetőséget.

    : Válassza az **OK** lehetőséget.

    > [!IMPORTANT]
    > Minden egyes folyamatfuttatásnak van bizonyos költségvonzata, ezért a befejezés időpontját ezt figyelembe véve adja meg.

1. Az **trigger szerkesztése** lapon tekintse át a figyelmeztetést, majd kattintson a **Mentés** gombra. A jelen példában található folyamat nem használ paramétereket.

1. A módosítás közzétételéhez kattintson az **összes közzététele** elemre.

1. Lépjen a bal oldali **Figyelés** lapra az aktivált folyamatfuttatások megtekintéséhez.

    [![Aktivált folyamatfuttatások](./media/tutorial-copy-data-portal/triggered-pipeline-runs-inline-and-expended.png)](./media/tutorial-copy-data-portal/triggered-pipeline-runs-inline-and-expended.png#lightbox)

1. Ha át szeretné váltani a **folyamat futási** nézetét az **trigger futtatása** nézetre, válassza az ablak bal oldalán az **aktiválási futtatások** lehetőséget.

1. Itt megtekintheti az eseményindító-futtatások listáját.

1. Ellenőrizze, hogy a megadott befejezési időig percenként (folyamatfuttatásonként) két sor be van-e szúrva az **emp** táblába.

## <a name="next-steps"></a>Következő lépések
A példában szereplő folyamat adatokat másol az egyik helyről egy másikra a Blob Storage-ban. Megtanulta végrehajtani az alábbi műveleteket:

> [!div class="checklist"]
> * Adat-előállító létrehozása
> * Másolási tevékenységgel rendelkező folyamat létrehozása.
> * A folyamat próbafuttatása
> * A folyamat manuális aktiválása
> * A folyamat aktiválása ütemezés szerint
> * A folyamat és a tevékenységek futásának monitorozása


A következő oktatóanyagra lépve megismerheti az adatok helyszíni rendszerből felhőre való másolásának folyamatát:

> [!div class="nextstepaction"]
>[Adatok másolása a helyszínről a felhőbe](tutorial-hybrid-copy-portal.md)
