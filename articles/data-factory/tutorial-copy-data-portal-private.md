---
title: Privát végpontok használata egy Azure Data Factory létrehozásához
description: Az oktatóanyag részletes útmutatását követve létrehozhat egy adat-előállítót egy folyamat és az Azure Portal használatával. A folyamat a másolási tevékenységgel másol adatokat az Azure Blob Storage-ból egy Azure SQL adatbázisba.
author: linda33wj
ms.service: data-factory
ms.topic: tutorial
ms.custom: seo-lt-2019
ms.date: 04/14/2021
ms.author: jingwang
ms.openlocfilehash: 191ad61990e10fdb718eebf1a8f57d8edaadcf35
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/15/2021
ms.locfileid: "107515454"
---
# <a name="copy-data-securely-from-azure-blob-storage-to-a-sql-database-by-using-private-endpoints"></a>Adatok biztonságos másolása az Azure Blob Storage-ból egy SQL-adatbázisba privát végpontok használatával

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Ebben az oktatóanyagban az Azure Data Factory felhasználói felületének használatával hoz létre egy adat-előállítót. *Az adat-előállítóban lévő folyamat biztonságosan másol adatokat az Azure Blob Storage-ból egy Azure SQL-adatbázisba (mindkettő csak a kijelölt hálózatokhoz engedélyezi a hozzáférést) privát végpontok használatával a Azure Data Factory [felügyelt](managed-virtual-network-private-endpoint.md)Virtual Network.* Az oktatóanyagban szereplő konfigurációs minta fájlalapú adattárból relációs adattárba való másolásra vonatkozik. A forrásként és fogadóként támogatott adattárakat a Támogatott [adattárak és](./copy-activity-overview.md) -formátumok táblázatban láthatja.

> [!NOTE]
> Ha még csak ismerkedik a Data Factory használatával, olvassa el [az Azure Data Factory használatának első lépéseit](./introduction.md) ismertető cikket.

Az oktatóanyag során a következő lépéseket hajtja végre:

* Adat-előállító létrehozása
* Másolási tevékenységgel rendelkező folyamat létrehozása.


## <a name="prerequisites"></a>Előfeltételek
* **Azure-előfizetés.** Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes Azure-fiókot](https://azure.microsoft.com/free/) a virtuális gép létrehozásának megkezdése előtt.
* **Azure Storage-fiók.** A Blob Storage lesz használatban *forrás* adattárként. Ha még nem rendelkezik tárfiókkal, tekintse meg az [Azure Storage-fiók létrehozásának](../storage/common/storage-account-create.md?tabs=azure-portal) lépéseit ismertető cikket. *Győződjön meg arról, hogy a tárfiók csak a kiválasztott hálózatokról engedélyezi a hozzáférést.* 
* **Azure SQL Database:**. Ezt az adatbázist használjuk *fogadó* adattárként. Ha nincs adatbázisa, Azure SQL SQL-adatbázis [létrehozása](../azure-sql/database/single-database-create-quickstart.md) című lépésre. *Győződjön meg arról, SQL Database fiók csak a kiválasztott hálózatokról engedélyezi a hozzáférést.* 

### <a name="create-a-blob-and-a-sql-table"></a>Blob és SQL-tábla létrehozása

Most készítse elő a blobtárolót és az SQL-adatbázist az oktatóanyaghoz az alábbi lépések elvégzésével.

#### <a name="create-a-source-blob"></a>Forrás blob létrehozása

1. Nyissa meg a Jegyzettömböt. Másolja be a következő szöveget, és mentse **emp.txt** néven egy fájlba a lemezen.

    ```
    FirstName,LastName
    John,Doe
    Jane,Doe
    ```

1. Hozzon létre egy **adftutorial nevű tárolót** a blobtárolóban. Ebben a tárolóban hozzon létre egy **input** nevű mappát. Ezután töltse fel az **emp.txt** fájlt az **input** mappába. Ezekhez a feladatokhoz használja az Azure Portalt vagy olyan eszközöket, mint az [Azure Storage Explorer](https://storageexplorer.com/).

#### <a name="create-a-sink-sql-table"></a>Fogadó SQL-tábla létrehozása

A következő SQL-szkripttel hozza létre a **dbo.emp** táblát az SQL Database-ben.

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

## <a name="create-a-data-factory"></a>Adat-előállító létrehozása
Ebben a lépésben létrehoz egy adat-előállítót, és elindítja a Data Factory felhasználói felületét, hogy létrehozzon egy folyamatot az adat-előállítóban.

1. Nyissa meg Microsoft Edge vagy a Google Chrome böngészőt. Jelenleg csak a Microsoft Edge És a Google Chrome böngészők támogatják a Data Factory felhasználói felületét.

1. A bal oldali menüben válassza az **Erőforrás létrehozása**  >  **Analytics-Data Factory.**  >  

1. Az **Új adat-előállító** lap **Név** mezőjében adja meg az **ADFTutorialDataFactory** értéket.

   Az Azure data factory nevének globálisan *egyedinek kell lennie.* Ha hibaüzenetet kap a névértékről, adjon meg egy másik nevet az adat-előállítónak (például sajátneveADFTutorialDataFactory). A Data Factory-összetevők elnevezési szabályait a [Data Factory elnevezési szabályait](./naming-rules.md) ismertető cikkben találja.

1. Válassza ki azt az **Azure-előfizetést**, amelyben az adat-előállítót létre szeretné hozni.

1. **Erőforráscsoport:** hajtsa végre a következő lépések egyikét:

    - Válassza **a Meglévő használata** lehetőséget, majd válasszon ki egy meglévő erőforráscsoportot a legördülő listából.
    - Válassza **az Új létrehozása** lehetőséget, és adja meg egy erőforráscsoport nevét. 
     
    Az erőforráscsoportokkal kapcsolatos információkért tekintse meg az [Erőforráscsoportok használata az Azure-erőforrások kezeléséhez](../azure-resource-manager/management/overview.md) ismertető cikket. 

1. A **Verzió** résznél válassza a **V2** értéket.

1. A **Hely** területen válassza ki az adat-előállító helyét. A legördülő listában csak a támogatott helyek jelennek meg. Az adat-előállítók által használt adattárak (például az Azure Storage és az SQL Database) és számítási erőforrások (például az Azure HDInsight) más régiókban is lehetnek.

1. Válassza a **Létrehozás** lehetőséget.

1. A létrehozás befejezése után az értesítés megjelenik az Értesítési központban. Válassza **az Erőforrás ugrás lehetőséget** a Data Factory kiválasztásához. 

1. A Data Factory felhasználói felületének külön lapon történő elindításához válassza a **Létrehozás és figyelés** csempét.

## <a name="create-an-azure-integration-runtime-in-data-factory-managed-virtual-network"></a>Azure integration runtime létrehozása felügyelt Data Factory Virtual Network
Ebben a lépésben létrehoz egy Azure integration runtime-t, és engedélyezi Data Factory felügyelt Virtual Network.

1. A Data Factory a Kezelés, majd  az **Új** lehetőséget választva hozzon létre egy új Azure integration Runtime-t.

   ![Képernyőkép egy új Azure integration Runtime létrehozásáról.](./media/tutorial-copy-data-portal-private/create-new-azure-ir.png)
1. Az **Integrációskörnyezet beállítása lapon** a szükséges képességek alapján válassza ki, hogy melyik integrációskörnyezetet hozza létre. Ebben az oktatóanyagban válassza az **Azure, Saját üzemeltetett lehetőséget,** majd kattintson a **Folytatás gombra.** 
1. Válassza **az Azure** lehetőséget, majd kattintson a **Folytatás** gombra egy Azure Integration Runtime létrehozásához.

   ![Képernyőkép egy új Azure-beli integrációs környezetről.](./media/tutorial-copy-data-portal-private/azure-ir.png)
1. A **Virtuális hálózat konfigurálása (előzetes verzió) alatt válassza** az Engedélyezés **lehetőséget.**

   ![Képernyőkép egy új Azure integrációs környezet engedélyezéséről.](./media/tutorial-copy-data-portal-private/enable-managed-vnet.png)
1. Válassza a **Létrehozás** lehetőséget.

## <a name="create-a-pipeline"></a>Folyamat létrehozása
Ebben a lépésben létrehoz egy másolási tevékenységgel rendelkező folyamatot az adat-előállítóban. A másolási tevékenység adatokat másol egy Blob Storage-ból egy SQL Database-be. A [rövid útmutatóban](./quickstart-create-data-factory-portal.md) létrehozott egy folyamatot az alábbi lépéseket követve:

1. Hozza létre a társított szolgáltatást.
1. Hozzon létre bemeneti és kimeneti adatkészleteket.
1. Folyamat létrehozása.

Ebben az oktatóanyagban először létrehoz egy folyamatot. Ezután társított szolgáltatásokat és adatkészleteket hozunk létre, amikor szükség van rájuk a folyamat konfigurálásához.

1. Az **Első lépések** lapon válassza a **Folyamat létrehozása** lehetőséget.

   ![Folyamat létrehozását bemutató képernyőkép.](./media/doc-common-process/get-started-page.png)
1. A folyamat tulajdonságok paneljére írja be a **CopyPipeline** nevet a folyamat neveként.

1. A Tevékenységek **eszközkészletben** bontsa ki az  Áthelyezés **és** átalakítás kategóriát, és húzza az Adatok másolása tevékenységet az eszközkészletből a folyamat tervezőfelületére. A névhez adja meg a **CopyFromBlobToSql** nevet.

    ![A másolási tevékenységet bemutató képernyőkép.](./media/tutorial-copy-data-portal-private/drag-drop-copy-activity.png)

### <a name="configure-a-source"></a>Forrás konfigurálása

>[!TIP]
>Ebben az oktatóanyagban a **fiókkulcsot** használja a forrásadattár hitelesítési típusaként. Szükség esetén más támogatott hitelesítési módszereket is választhat, például **a SAS URI-t,** a **szolgáltatásnévt** és a **felügyelt** identitást. További információért tekintse meg az Adatok másolása és átalakítása az Azure Blob Storage-ban a következő dokumentum [megfelelő szakaszait: Azure Data Factory.](./connector-azure-blob-storage.md#linked-service-properties)
>
>Az adattárak titkos adatainak biztonságos tárolása érdekében javasoljuk, hogy használja a Azure Key Vault. További információ és illusztráció: Hitelesítő adatok tárolása a [Azure Key Vault.](./store-credentials-in-key-vault.md)

#### <a name="create-a-source-dataset-and-linked-service"></a>Forrásadatkészlet és csatolt szolgáltatás létrehozása

1. Ugrás a **Forrás lapra.** **Forrásadatkészlet létrehozásához** válassza az + Új lehetőséget.

1. Az Új **adatkészlet párbeszédpanelen** válassza a **Azure Blob Storage,** majd a Folytatás **lehetőséget.** A forrásadatok egy Blob Storage-ban vannak, tehát forrásadatkészletként az **Azure Blob Storage**-ot válassza.

1. A Formátum **kiválasztása párbeszédpanelen** válassza ki az adatok formátumtípusát, majd válassza a Folytatás **lehetőséget.**

1. A Tulajdonságok **beállítása párbeszédpanelen** a Név mezőbe írja be a **SourceBlobDataset** **nevet.** Jelölje be az Első sor **fejlécként jelölőnégyzetet.** A Linked **service (Csatolt szolgáltatás) szövegmezőben** válassza **az + Új lehetőséget.**

1. Az Új **csatolt szolgáltatás (Azure Blob Storage)** párbeszédpanelen adja meg az **AzureStorageLinkedService** nevet **Névként,** majd válassza ki a tárfiókját a **Tárfiók neve listából.** 

1. Győződjön meg arról, hogy engedélyezi **az interaktív szerzői funkciókat.** Az engedélyezése körülbelül egy percet is igénybe vehet.

    ![Az interaktív tartalom készítését bemutató képernyőkép.](./media/tutorial-copy-data-portal-private/interactive-authoring.png)

1. Válassza a **Kapcsolat tesztelése** elemet. Sikertelennek kell lennie, ha a  tárfiók csak a Kijelölt hálózatokról engedélyezi a hozzáférést, és Data Factory kell létrehoznia hozzá egy privát végpontot, amely használat előtt jóvá lesz hagyva. A hibaüzenetben megjelenik egy hivatkozás, amely egy privát végpont létrehozására hivatkozik, amely alapján létrehozhat egy felügyelt privát végpontot. Másik lehetőségként közvetlenül a Kezelés lapra **kell** átmenni, és a következő szakaszban található utasításokat követve létrehozhat egy felügyelt privát végpontot. [](#create-a-managed-private-endpoint)

   > [!NOTE]
   > Előfordulhat, **hogy** a Kezelés lap nem érhető el az összes data factory-példányhoz. Ha nem látja, a Privát végpontok létesítése lehetőség kiválasztásával férhet hozzá a privát  >    >  **végponthoz.**
1. Ne nyissa meg a párbeszédpanelt, majd nyissa meg a tárfiókját.

1. A privát hivatkozás [jóváhagyásához kövesse](#approval-of-a-private-link-in-a-storage-account) az ebben a szakaszban található utasításokat.

1. Vissza a párbeszédpanelre. Válassza **ismét a Kapcsolat tesztelése** lehetőséget, majd válassza a **Létrehozás** lehetőséget a csatolt szolgáltatás üzembe helyezéséhez.

1. A csatolt szolgáltatás a létrehozása után visszatér a **Tulajdonságok beállítása lapra.** A **Fájl elérési útja** mellett válassza a **Tallózás** lehetőséget.

1. Az **adftutorial/input** mappában válassza  ki aemp.txtfájlt, majd kattintson az **OK gombra.**

1. Válassza az **OK** lehetőséget. Automatikusan a folyamat oldalára kerül. A Forrás **lapon** ellenőrizze, hogy a **SourceBlobDataset van-e** kiválasztva. A lapon lévő adatok előnézetének megtekintéséhez válassza az **Adatok előnézete** elemet.

    ![A forrásadatkészletet bemutató képernyőkép.](./media/tutorial-copy-data-portal-private/source-dataset-selected.png)

#### <a name="create-a-managed-private-endpoint"></a>Felügyelt privát végpont létrehozása

Ha a kapcsolat tesztelése során nem kattintott a hivatkozásra, kövesse az elérési utat. Most létre kell hoznia egy felügyelt privát végpontot, amely a létrehozott csatolt szolgáltatáshoz fog csatlakozni.

1. Ugrás a **Kezelés lapra.**

   > [!NOTE]
   > Előfordulhat, **hogy** a Kezelés lap nem érhető el az összes Data Factory számára. Ha nem látja, a Privát végpontok létesítése lehetőség kiválasztásával férhet hozzá a privát  >    >  **végponthoz.**

1. Ugrás a **Felügyelt privát végpontok szakaszra.**

1. A **Felügyelt privát végpontok** alatt válassza az + **Új lehetőséget.**

    ![Képernyőkép a Felügyelt privát végpontok Új gombról.](./media/tutorial-copy-data-portal-private/new-managed-private-endpoint.png) 

1. Válassza ki **Azure Blob Storage** csempét a listából, majd válassza a **Folytatás lehetőséget.**

1. Adja meg a létrehozott tárfiók nevét.

1. Válassza a **Létrehozás** lehetőséget.

1. Néhány másodperc elteltével látnia kell, hogy a létrehozott privát kapcsolathoz jóváhagyásra van szükség.

1. Válassza ki a létrehozott privát végpontot. Egy hivatkozással jóváhagyhatja a privát végpontot a tárfiók szintjén.

    ![Képernyőkép a Felügyelt privát végpont panelről.](./media/tutorial-copy-data-portal-private/manage-private-endpoint.png) 

#### <a name="approval-of-a-private-link-in-a-storage-account"></a>Privát kapcsolat jóváhagyása tárfiókban
1. A tárfiók beállítások szakaszában kattintson a Privát **végponti** kapcsolatok **elemre.**

1. Jelölje be a létrehozott privát végpont jelölőnégyzetét, majd válassza a **Jóváhagyás lehetőséget.**

    ![A privát végpont Jóváhagyás gombját bemutató képernyőkép.](./media/tutorial-copy-data-portal-private/approve-private-endpoint.png)

1. Adjon meg egy leírást, és válassza az **igen lehetőséget.**
1. Vissza lap **Kezelés lapjának** Felügyelt privát  végpontok szakaszát Data Factory.
1. Körülbelül egy-két perc múlva a privát végpont jóváhagyásának kell megjelennie a Data Factory felületén.


### <a name="configure-a-sink"></a>Fogadó konfigurálása
>[!TIP]
>Ebben az oktatóanyagban **SQL-hitelesítést** fog használni a fogadóadattár hitelesítési típusaként. Szükség esetén más támogatott hitelesítési módszereket  is választhat, például egyszerű szolgáltatást és **felügyelt** identitást. További információért tekintse meg az Adatok másolása és átalakítása a Azure SQL Database [használatával](./connector-azure-sql-database.md#linked-service-properties)a Azure Data Factory.
>
>Az adattárak titkos adatainak biztonságos tárolása érdekében javasoljuk, hogy használja a Azure Key Vault. További információ és illusztráció: Hitelesítő adatok tárolása a [Azure Key Vault.](./store-credentials-in-key-vault.md)

#### <a name="create-a-sink-dataset-and-linked-service"></a>Fogadó adatkészlet és csatolt szolgáltatás létrehozása
1. Váltson a **Fogadó** lapra, és válassza az **+ Új** elemet egy fogadó-adatkészlet létrehozásához.

1. Az Új **adatkészlet párbeszédpanelen** a keresőmezőbe írja be az **SQL** adatokat az összekötők szűréséhez. Válassza **Azure SQL Database** lehetőséget, majd válassza a **Folytatás lehetőséget.** Ebben az oktatóanyagban adatokat másol egy SQL Database-be.

1. A Tulajdonságok **beállítása párbeszédpanelen** a Név mezőbe írja be az **OutputSqlDataset** **nevet.** A Csatolt **szolgáltatás legördülő** listában válassza az + **Új lehetőséget.** Az adatkészleteket mindig társítani kell egy társított szolgáltatáshoz. A társított szolgáltatás rendelkezik azzal a kapcsolati sztringgel, amely használatával a Data Factory a futtatás során kapcsolódik az SQL Database-hez. Az adatkészlet meghatározza azt a tárolót, mappát és fájlt (az utóbbi nem kötelező), ahova a rendszer az adatokat másolja.

1. Az **Új csatolt szolgáltatás (Azure SQL Database)** párbeszédpanelen kövesse az alábbi lépéseket:

    1. A **Név** mezőbe írja az **AzureSqlDatabaseLinkedService** nevet.
    1. A **Kiszolgáló neve** mezőben válassza ki az SQL Server-példányát.
    1. Győződjön meg arról, hogy engedélyezi **az interaktív szerzői funkciókat.**
    1. Az **Adatbázis neve** mezőben válassza ki az SQL Database-adatbázisát.
    1. A **Felhasználónév** mezőben adja meg a felhasználó nevét.
    1. A **Jelszó** mezőben adja meg a felhasználó jelszavát.
    1. Válassza a **Kapcsolat tesztelése** elemet. Ennek sikertelennek kell lennie, mert  az SQL-kiszolgáló csak a Kijelölt hálózatokról engedélyezi a hozzáférést, és Data Factory létre kell hoznia hozzá egy privát végpontot, amelyet a használata előtt jóvá kell hagyni. A hibaüzenetben megjelenik egy hivatkozás, amely egy privát végpont létrehozására hivatkozik, amely alapján létrehozhat egy felügyelt privát végpontot. Másik lehetőségként közvetlenül a Kezelés lapra is **ússhat,** és a következő szakaszban található utasításokat követve létrehozhat egy felügyelt privát végpontot.
    1. Ne nyissa meg a párbeszédpanelt, majd nyissa meg a kiválasztott SQL Servert.
    1. A privát hivatkozás [jóváhagyásához kövesse](#approval-of-a-private-link-in-sql-server) az ebben a szakaszban található utasításokat.
    1. Vissza a párbeszédpanelre. Válassza **ismét a Kapcsolat tesztelése** lehetőséget, majd válassza a **Létrehozás** lehetőséget a csatolt szolgáltatás üzembe helyezéséhez.

1. Automatikusan megjelenik a **Tulajdonságok beállítása** párbeszédpanel. A **Tábla** területen válassza a **[dbo].[emp]** elemet. Ez után válassza az **OK** gombot.

1. A folyamat lapfülére, majd a **Fogadó** adatkészletben ellenőrizze, hogy az **OutputSqlDataset van-e** kiválasztva.

    ![A Folyamat lapot bemutató képernyőkép.](./media/tutorial-copy-data-portal-private/pipeline-tab-2.png)

Igény szerint leképezheti a forrás sémáját a cél megfelelő sémájához a másolási tevékenység [sémaleképezésének lépésével.](./copy-activity-schema-and-type-mapping.md)

#### <a name="create-a-managed-private-endpoint"></a>Felügyelt privát végpont létrehozása

Ha a kapcsolat tesztelése során nem kattintott a hivatkozásra, kövesse az elérési utat. Most létre kell hoznia egy felügyelt privát végpontot, amely a létrehozott csatolt szolgáltatáshoz fog csatlakozni.

1. Ugrás a **Kezelés lapra.**
1. Ugrás a **Felügyelt privát végpontok szakaszra.**
1. A **Felügyelt privát végpontok** alatt válassza az + **Új lehetőséget.**

    ![Képernyőkép a Felügyelt privát végpontok Új gombról.](./media/tutorial-copy-data-portal-private/new-managed-private-endpoint.png) 

1. Válassza a **Azure SQL Database** csempét a listából, majd válassza a **Folytatás lehetőséget.**
1. Adja meg a kiválasztott SQL-kiszolgáló nevét.
1. Válassza a **Létrehozás** lehetőséget.
1. Néhány másodperc elteltével látnia kell, hogy a létrehozott privát kapcsolathoz jóváhagyásra van szükség.
1. Válassza ki a létrehozott privát végpontot. Egy hivatkozással jóváhagyhatja a privát végpontot az SQL Server szintjén.


#### <a name="approval-of-a-private-link-in-sql-server"></a>Privát hivatkozás jóváhagyása a SQL Server
1. Az SQL-kiszolgálón a Beállítások szakaszban kattintson a Privát **végponti** **kapcsolatok** elemre.
1. Jelölje be a létrehozott privát végpont jelölőnégyzetét, majd válassza a **Jóváhagyás lehetőséget.**
1. Adjon meg egy leírást, és válassza az **igen lehetőséget.**
1. Vissza lap **Kezelés lapjának** Felügyelt privát  végpontok szakaszát Data Factory.
1. Egy-két percet igénybe kell vennie, hogy megjelenjen a jóváhagyás a privát végponthoz.

#### <a name="debug-and-publish-the-pipeline"></a>A folyamat hibakeresése és közzététele

Elvégezheti a folyamat hibakeresését, mielőtt összetevőket (társított szolgáltatások, adatkészletek és folyamat) tenne közzé a Data Factoryben vagy a saját Azure Repos Git-adattárában.

1. A folyamat hibakereséséhez válassza a **Hibakeresés** elemet az eszköztáron. A folyamat futtatási állapotát az ablak alján található **Kimenet** lapon tekintheti meg.
1. Miután a folyamat sikeresen lefutott, a felső eszköztáron válassza az **Összes közzététele lehetőséget.** Ez a művelet közzéteszi a létrehozott entitásokat (adatkészleteket és Data Factory.
1. Várjon, amíg megjelenik a **Sikeres közzététel** üzenet. Az értesítési üzenetekhez válassza az **Értesítések megjelenítése** lehetőséget a jobb felső sarokban (harang gomb).


#### <a name="summary"></a>Összefoglalás
A példában lévő folyamat adatokat másol a Blob Storage-ból SQL Database felügyelt tároló privát végpontjainak használatával Data Factory felügyelt Virtual Network. Megtanulta végrehajtani az alábbi műveleteket:

* Adat-előállító létrehozása
* Másolási tevékenységgel rendelkező folyamat létrehozása.