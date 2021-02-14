---
title: Azure Data Factory folyamat létrehozása privát végpontok használatával
description: Az oktatóanyag részletes útmutatását követve létrehozhat egy adat-előállítót egy folyamat és az Azure Portal használatával. A folyamat a másolási tevékenységet használja az adatok Azure Blob Storage-ból egy Azure SQL Database-be való másolásához.
author: linda33wj
ms.service: data-factory
ms.topic: tutorial
ms.custom: seo-lt-2019
ms.date: 01/15/2021
ms.author: jingwang
ms.openlocfilehash: 002ceba60853908e381f084e95ec1ee58e8580cb
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/14/2021
ms.locfileid: "100375816"
---
# <a name="copy-data-securely-from-azure-blob-storage-to-a-sql-database-by-using-private-endpoints"></a>Adatok biztonságos másolása az Azure Blob Storage-ból egy SQL-adatbázisba privát végpontok használatával

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Ebben az oktatóanyagban az Azure Data Factory felhasználói felületének használatával hoz létre egy adat-előállítót. *Az adatfeldolgozó folyamata biztonságos módon másolja az adatait az Azure Blob Storage-ból egy Azure SQL Database-adatbázisba (mindkettő csak a kiválasztott hálózatokhoz való hozzáférést engedélyezi) [Azure Data Factory felügyelt Virtual Networkban](managed-virtual-network-private-endpoint.md)lévő privát végpontok használatával.* Az oktatóanyagban szereplő konfigurációs minta fájlalapú adattárból relációs adattárba való másolásra vonatkozik. A forrásként és fogadóként támogatott adattárak listáját a [támogatott adattárak és-formátumok](./copy-activity-overview.md) táblázatban tekintheti meg.

> [!NOTE]
> Ha még csak ismerkedik a Data Factory használatával, olvassa el [az Azure Data Factory használatának első lépéseit](./introduction.md) ismertető cikket.

Az oktatóanyag során a következő lépéseket hajtja végre:

* Adat-előállító létrehozása
* Másolási tevékenységgel rendelkező folyamat létrehozása.


## <a name="prerequisites"></a>Előfeltételek
* **Azure-előfizetés**. Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes Azure-fiókot](https://azure.microsoft.com/free/) a virtuális gép létrehozásának megkezdése előtt.
* **Azure Storage-fiók**. A Blob Storage lesz használatban *forrás* adattárként. Ha még nem rendelkezik tárfiókkal, tekintse meg az [Azure Storage-fiók létrehozásának](../storage/common/storage-account-create.md?tabs=azure-portal) lépéseit ismertető cikket. *Győződjön meg arról, hogy a Storage-fiók csak a kiválasztott hálózatokról engedélyezi a hozzáférést.* 
* **Azure SQL Database**. Ezt az adatbázist használjuk *fogadó* adattárként. Ha nem rendelkezik Azure SQL Database-adatbázissal, tekintse meg az [SQL-adatbázis létrehozása](../azure-sql/database/single-database-create-quickstart.md) című témakört. *Győződjön meg arról, hogy az SQL Database-fiók csak a kiválasztott hálózatokról engedélyezi a hozzáférést.* 

### <a name="create-a-blob-and-a-sql-table"></a>Blob és SQL-tábla létrehozása

Most készítse elő a blob Storage-t és az SQL Database-t az oktatóanyaghoz az alábbi lépések végrehajtásával.

#### <a name="create-a-source-blob"></a>Forrás blob létrehozása

1. Nyissa meg a jegyzettömböt. Másolja be a következő szöveget, és mentse **emp.txt** néven egy fájlba a lemezen.

    ```
    FirstName,LastName
    John,Doe
    Jane,Doe
    ```

1. Hozzon létre egy **adftutorial** nevű tárolót a blob Storage-ban. Ebben a tárolóban hozzon létre egy **input** nevű mappát. Ezután töltse fel az **emp.txt** fájlt az **input** mappába. Ezekhez a feladatokhoz használja az Azure Portalt vagy olyan eszközöket, mint az [Azure Storage Explorer](https://storageexplorer.com/).

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

1. Nyissa meg a Microsoft Edge vagy a Google Chrome böngészőt. Jelenleg csak a Microsoft Edge és a Google Chrome böngésző támogatja a Data Factory felhasználói felületet.

1. A bal oldali menüben válassza az **erőforrás létrehozása**  >  **elemzési**  >  **Data Factory** elemet.

1. Az **Új adat-előállító** lap **Név** mezőjében adja meg az **ADFTutorialDataFactory** értéket.

   Az Azure-beli adatgyár nevének *globálisan egyedinek* kell lennie. Ha a név értékével kapcsolatos hibaüzenetet kap, adjon meg egy másik nevet az adatelőállítónak (például Sajátneveadftutorialdatafactory). A Data Factory-összetevők elnevezési szabályait a [Data Factory elnevezési szabályait](./naming-rules.md) ismertető cikkben találja.

1. Válassza ki azt az **Azure-előfizetést**, amelyben az adat-előállítót létre szeretné hozni.

1. **Erőforráscsoport:** hajtsa végre a következő lépések egyikét:

    - Válassza a **meglévő használata** lehetőséget, majd válasszon ki egy meglévő erőforráscsoportot a legördülő listából.
    - Válassza az **új létrehozása** lehetőséget, és adja meg az erőforráscsoport nevét. 
     
    Az erőforráscsoportokkal kapcsolatos információkért tekintse meg az [Erőforráscsoportok használata az Azure-erőforrások kezeléséhez](../azure-resource-manager/management/overview.md) ismertető cikket. 

1. A **Verzió** résznél válassza a **V2** értéket.

1. A **Hely** területen válassza ki az adat-előállító helyét. A legördülő listában csak a támogatott helyszínek jelennek meg. Az adat-előállítók által használt adattárak (például az Azure Storage és az SQL Database) és számítási erőforrások (például az Azure HDInsight) más régiókban is lehetnek.

1. Válassza a **Létrehozás** lehetőséget.

1. A létrehozás befejezése után megjelenik az értesítési központban megjelenő értesítés. Válassza az **Ugrás az erőforráshoz** lehetőséget, hogy megnyissa a **Data Factory** lapot.

1. A Data Factory felhasználói felületének külön lapon történő elindításához válassza a **Létrehozás és figyelés** csempét.

## <a name="create-an-azure-integration-runtime-in-data-factory-managed-virtual-network"></a>Azure Integration Runtime létrehozása Data Factory felügyelt Virtual Network
Ebben a lépésben létrehoz egy Azure Integration Runtime-t, és engedélyezi Data Factory felügyelt Virtual Network.

1. Az Data Factory portálon lépjen a **kezelés** elemre, és válassza az **új** lehetőséget az új Azure Integration Runtime létrehozásához.

   ![Képernyőkép, amely egy új Azure Integration Runtime létrehozását mutatja be.](./media/tutorial-copy-data-portal-private/create-new-azure-ir.png)
1. Az **Integration Runtime telepítése** lapon válassza ki, hogy melyik integrációs modult szeretné létrehozni a szükséges képességek alapján. Ebben az oktatóanyagban válassza az **Azure, saját** üzemeltetésű lehetőséget, majd kattintson a **Continue (folytatás**) gombra. 
1. Válassza az **Azure** lehetőséget, majd kattintson a **Folytatás** gombra egy Azure Integration Runtime létrehozásához.

   ![Képernyőkép, amely egy új Azure Integration Runtime-t jelenít meg.](./media/tutorial-copy-data-portal-private/azure-ir.png)
1. A **virtuális hálózat konfigurációja (előzetes verzió)** alatt válassza az **Engedélyezés** lehetőséget.

   ![Az új Azure Integration Runtime engedélyezését bemutató képernyőkép.](./media/tutorial-copy-data-portal-private/enable-managed-vnet.png)
1. Válassza a **Létrehozás** lehetőséget.

## <a name="create-a-pipeline"></a>Folyamat létrehozása
Ebben a lépésben létrehoz egy másolási tevékenységgel rendelkező folyamatot az adat-előállítóban. A másolási tevékenység adatokat másol egy Blob Storage-ból egy SQL Database-be. A [rövid útmutatóban](./quickstart-create-data-factory-portal.md) létrehozott egy folyamatot az alábbi lépéseket követve:

1. Hozza létre a társított szolgáltatást.
1. Hozzon létre bemeneti és kimeneti adatkészleteket.
1. Folyamat létrehozása.

Ebben az oktatóanyagban egy folyamat létrehozásával kezdi meg a folyamatot. Ezután társított szolgáltatásokat és adatkészleteket hozunk létre, amikor szükség van rájuk a folyamat konfigurálásához.

1. Az **Első lépések** lapon válassza a **Folyamat létrehozása** lehetőséget.

   ![A folyamat létrehozását bemutató képernyőkép.](./media/doc-common-process/get-started-page.png)
1. A folyamat tulajdonságok paneljén adja meg a **CopyPipeline** a folyamat neveként.

1. A **tevékenységek** eszközben bontsa ki az **áthelyezés és átalakítás** kategóriát, majd húzza az **adatok másolása** tevékenységet az eszközkészletből a folyamat tervező felületére. Adja meg a **copyfromblobtosql nevet** nevet.

    ![A másolási tevékenységet bemutató képernyőkép.](./media/tutorial-copy-data-portal-private/drag-drop-copy-activity.png)

### <a name="configure-a-source"></a>Forrás konfigurálása

>[!TIP]
>Ebben az oktatóanyagban a **fiók kulcsát** használja a forrás adattároló hitelesítési típusaként. Más támogatott hitelesítési módszereket is kiválaszthat, például **sas URI**-t,**egyszerű szolgáltatásnevet** és **felügyelt identitást** , ha szükséges. További információkért tekintse meg az [adatok másolása és átalakítása az Azure Blob Storage-ban](./connector-azure-blob-storage.md#linked-service-properties)című témakör megfelelő részeit a Azure Data Factory használatával.
>
>Ha biztonságosan szeretné tárolni az adattárak titkos adatait, javasoljuk, hogy használja a Azure Key Vault. További információ és illusztrációk: [hitelesítő adatok tárolása Azure Key Vaultban](./store-credentials-in-key-vault.md).

#### <a name="create-a-source-dataset-and-linked-service"></a>Forrás-adatkészlet és társított szolgáltatás létrehozása

1. Nyissa meg a **forrás** lapot. Forrás adatkészlet létrehozásához válassza az **+ új** lehetőséget.

1. Az **új adatkészlet** párbeszédpanelen válassza az **Azure Blob Storage** lehetőséget, majd kattintson a **Folytatás** gombra. A forrásadatok egy Blob Storage-ban vannak, tehát forrásadatkészletként az **Azure Blob Storage**-ot válassza.

1. A **formátum kiválasztása** párbeszédpanelen válassza ki az adatformátum típusát, majd kattintson a **Continue (folytatás**) elemre.

1. A **Tulajdonságok megadása** párbeszédpanelen adja meg a **sourceblobdataset lehetőség** **nevet**. Jelölje be az **első sor jelölőnégyzetét fejlécként**. A **társított szolgáltatás** szövegmezőben válassza az **+ új** lehetőséget.

1. Az **új társított szolgáltatás (Azure Blob Storage)** párbeszédpanelen írja be a **AzureStorageLinkedService** **nevet**, és válassza ki a Storage-fiók nevét a **Storage-fiók neve** listából. 

1. Győződjön meg arról, hogy az **interaktív szerzői műveletek** engedélyezve vannak. Egy percet is igénybe vehet.

    ![Az interaktív szerzői műveleteket bemutató képernyőkép.](./media/tutorial-copy-data-portal-private/interactive-authoring.png)

1. Válassza a **Kapcsolat tesztelése** elemet. Ha a Storage-fiók csak a **kiválasztott hálózatokról** engedélyezi a hozzáférést, és Data Factoryt igényel ahhoz, hogy olyan privát végpontot hozzon létre, amelyet a használata előtt jóvá kell hagyni. A hibaüzenetben egy olyan hivatkozást kell látnia, amely létrehoz egy privát végpontot, amelyet követve felügyelt privát végpontokat hozhat létre. Egy másik lehetőség, hogy közvetlenül a **kezelés** lapra lép, és a [következő szakaszban](#create-a-managed-private-endpoint) található utasításokat követve felügyelt privát végpontot hoz létre.

   > [!NOTE]
   > Előfordulhat, hogy a **kezelés** lap nem érhető el az összes adatfeldolgozó példányhoz. Ha nem látja, elérheti a privát végpontokat a **szerzői**  >  **kapcsolatok**  >  **privát végpontjának** kiválasztásával.
1. Tartsa nyitva a párbeszédpanelt, és nyissa meg a Storage-fiókját.

1. A privát hivatkozás jóváhagyásához kövesse az [ebben a szakaszban](#approval-of-a-private-link-in-a-storage-account) található utasításokat.

1. Lépjen vissza a párbeszédpanelre. Válassza a kapcsolat újbóli **tesztelése** lehetőséget, majd válassza a **Létrehozás** lehetőséget a társított szolgáltatás telepítéséhez.

1. A társított szolgáltatás létrehozása után visszakerül a **set Properties (tulajdonságok beállítása** ) lapra. A **Fájl elérési útja** mellett válassza a **Tallózás** lehetőséget.

1. Nyissa meg a **adftutorial/input** mappát, válassza ki a **emp.txt** fájlt, majd kattintson az **OK gombra**.

1. Válassza az **OK** lehetőséget. Automatikusan a folyamat lapra kerül. A **forrás** lapon ellenőrizze, hogy a **sourceblobdataset lehetőség** van-e kiválasztva. A lapon lévő adatok előnézetének megtekintéséhez válassza az **Adatok előnézete** elemet.

    ![A forrás adatkészletet megjelenítő képernyőkép.](./media/tutorial-copy-data-portal-private/source-dataset-selected.png)

#### <a name="create-a-managed-private-endpoint"></a>Felügyelt privát végpont létrehozása

Ha a kapcsolat tesztelésekor nem választotta ki a hiperhivatkozást, kövesse az elérési utat. Most létre kell hoznia egy felügyelt magánhálózati végpontot, amelyhez csatlakozni fog a létrehozott társított szolgáltatáshoz.

1. Lépjen a **kezelés** lapra.

   > [!NOTE]
   > Előfordulhat, hogy a **kezelés** lap nem érhető el az összes Data Factory példányhoz. Ha nem látja, elérheti a privát végpontokat a **szerzői**  >  **kapcsolatok**  >  **privát végpontjának** kiválasztásával.

1. Lépjen a **felügyelt privát végpontok** szakaszra.

1. Válassza az **+ új** lehetőséget a **felügyelt privát végpontok** alatt.

    ![A felügyelt privát végpontok új gombját bemutató képernyőkép.](./media/tutorial-copy-data-portal-private/new-managed-private-endpoint.png) 

1. Válassza ki az **Azure Blob Storage** csempét a listából, majd válassza a **Folytatás** lehetőséget.

1. Adja meg a létrehozott Storage-fiók nevét.

1. Válassza a **Létrehozás** lehetőséget.

1. Néhány másodperc elteltével látnia kell, hogy a privát hivatkozás létrehozása jóváhagyást igényel.

1. Válassza ki a létrehozott privát végpontot. Megtekintheti a hivatkozásokat, amelyekkel jóváhagyhatja a magánhálózati végpontot a Storage-fiók szintjén.

    ![A felügyelt privát végpont ablaktáblát megjelenítő képernyőkép.](./media/tutorial-copy-data-portal-private/manage-private-endpoint.png) 

#### <a name="approval-of-a-private-link-in-a-storage-account"></a>Privát hivatkozás jóváhagyása egy Storage-fiókban
1. A Storage-fiókban válassza a **privát végponti kapcsolatok** **lehetőséget a beállítások** szakaszban.

1. Jelölje be a létrehozott privát végpont jelölőnégyzetét, majd válassza a **jóváhagyás** lehetőséget.

    ![Képernyőkép, amely a privát végpont jóváhagyás gombját jeleníti meg.](./media/tutorial-copy-data-portal-private/approve-private-endpoint.png)

1. Adjon hozzá egy leírást, és válassza az **Igen** lehetőséget.
1. Lépjen vissza a Data Factory **kezelés** lapján található **felügyelt privát végpontok** szakaszra.
1. Egy vagy két perc után megjelenik a privát végpont jóváhagyása a Data Factory felhasználói felületen.


### <a name="configure-a-sink"></a>Fogadó konfigurálása
>[!TIP]
>Ebben az oktatóanyagban az **SQL-hitelesítést** használja a fogadó adattár hitelesítési típusaként. Más támogatott hitelesítési módszereket is kiválaszthat, például a **szolgáltatásnevet** és a **felügyelt identitást** , ha szükséges. További információkért tekintse meg az [adatok másolása és átalakítása Azure SQL Databaseban](./connector-azure-sql-database.md#linked-service-properties)című témakör megfelelő részeit Azure Data Factory használatával.
>
>Ha biztonságosan szeretné tárolni az adattárak titkos adatait, javasoljuk, hogy használja a Azure Key Vault. További információ és illusztrációk: [hitelesítő adatok tárolása Azure Key Vaultban](./store-credentials-in-key-vault.md).

#### <a name="create-a-sink-dataset-and-linked-service"></a>Fogadó adatkészlet és társított szolgáltatás létrehozása
1. Váltson a **Fogadó** lapra, és válassza az **+ Új** elemet egy fogadó-adatkészlet létrehozásához.

1. Az **új adatkészlet** párbeszédpanelen írja be az **SQL** kifejezést a keresőmezőbe az összekötők szűréséhez. Válassza a **Azure SQL Database** lehetőséget, majd kattintson a **Folytatás** gombra. Ebben az oktatóanyagban adatokat másol egy SQL Database-be.

1. A **Tulajdonságok megadása** párbeszédpanelen adja meg a **outputsqldataset érték** **nevet**. A **társított szolgáltatás** legördülő listából válassza az **+ új** lehetőséget. Az adatkészleteket mindig társítani kell egy társított szolgáltatáshoz. A társított szolgáltatás rendelkezik azzal a kapcsolati sztringgel, amely használatával a Data Factory a futtatás során kapcsolódik az SQL Database-hez. Az adatkészlet meghatározza azt a tárolót, mappát és fájlt (az utóbbi nem kötelező), ahova a rendszer az adatokat másolja.

1. Az **új társított szolgáltatás (Azure SQL Database)** párbeszédpanelen hajtsa végre a következő lépéseket:

    1. A **Név** mezőbe írja az **AzureSqlDatabaseLinkedService** nevet.
    1. A **Kiszolgáló neve** mezőben válassza ki az SQL Server-példányát.
    1. Győződjön meg arról, hogy az **interaktív szerzői műveletek** engedélyezve vannak.
    1. Az **Adatbázis neve** mezőben válassza ki az SQL Database-adatbázisát.
    1. A **Felhasználónév** mezőben adja meg a felhasználó nevét.
    1. A **Jelszó** mezőben adja meg a felhasználó jelszavát.
    1. Válassza a **Kapcsolat tesztelése** elemet. A művelet végrehajtása sikertelen, mert az SQL Server csak a **kiválasztott hálózatokról** engedélyezi a hozzáférést, és Data Factory kell létrehoznia egy privát végpontot, amelyet a használata előtt jóvá kell hagyni. A hibaüzenetben egy olyan hivatkozást kell látnia, amely létrehoz egy privát végpontot, amelyet követve felügyelt privát végpontokat hozhat létre. Egy másik lehetőség, hogy közvetlenül a **kezelés** lapra lép, és a következő szakaszban található utasításokat követve felügyelt privát végpontot hoz létre.
    1. Hagyja nyitva a párbeszédpanelt, majd lépjen a kiválasztott SQL Server-kiszolgálóra.
    1. A privát hivatkozás jóváhagyásához kövesse az [ebben a szakaszban](#approval-of-a-private-link-in-sql-server) található utasításokat.
    1. Lépjen vissza a párbeszédpanelre. Válassza a kapcsolat újbóli **tesztelése** lehetőséget, majd válassza a **Létrehozás** lehetőséget a társított szolgáltatás telepítéséhez.

1. Automatikusan a **set Properties (tulajdonságok beállítása** ) párbeszédpanelre kerül. A **Tábla** területen válassza a **[dbo].[emp]** elemet. Ez után válassza az **OK** gombot.

1. Lépjen a folyamat lapra, és a fogadó **adatkészletben** ellenőrizze, hogy a **outputsqldataset érték** van-e kiválasztva.

    ![A folyamat lapot megjelenítő képernyőkép.](./media/tutorial-copy-data-portal-private/pipeline-tab-2.png)

Igény szerint leképezheti a forrás sémáját a cél megfelelő sémájára a [másolási tevékenységben lévő séma-hozzárendelés](./copy-activity-schema-and-type-mapping.md)után.

#### <a name="create-a-managed-private-endpoint"></a>Felügyelt privát végpont létrehozása

Ha a kapcsolat tesztelésekor nem választotta ki a hiperhivatkozást, kövesse az elérési utat. Most létre kell hoznia egy felügyelt magánhálózati végpontot, amelyhez csatlakozni fog a létrehozott társított szolgáltatáshoz.

1. Lépjen a **kezelés** lapra.
1. Lépjen a **felügyelt privát végpontok** szakaszra.
1. Válassza az **+ új** lehetőséget a **felügyelt privát végpontok** alatt.

    ![A felügyelt privát végpontok új gombját bemutató képernyőkép.](./media/tutorial-copy-data-portal-private/new-managed-private-endpoint.png) 

1. Válassza ki a **Azure SQL Database** csempét a listából, majd válassza a **Folytatás** lehetőséget.
1. Adja meg a kiválasztott SQL-kiszolgáló nevét.
1. Válassza a **Létrehozás** lehetőséget.
1. Néhány másodperc elteltével látnia kell, hogy a privát hivatkozás létrehozása jóváhagyást igényel.
1. Válassza ki a létrehozott privát végpontot. Megtekintheti a hiperhivatkozást, amellyel jóváhagyhatja a privát végpontot az SQL Server szintjén.


#### <a name="approval-of-a-private-link-in-sql-server"></a>Privát hivatkozás jóváhagyása SQL Server
1. Az SQL Serverben a beállítások szakaszban válassza a **privát végponti kapcsolatok** **lehetőséget** .
1. Jelölje be a létrehozott privát végpont jelölőnégyzetét, majd válassza a **jóváhagyás** lehetőséget.
1. Adjon hozzá egy leírást, és válassza az **Igen** lehetőséget.
1. Lépjen vissza a Data Factory **kezelés** lapján található **felügyelt privát végpontok** szakaszra.
1. Egy vagy két percet vesz igénybe, hogy a jóváhagyás megjelenjen a saját végpontja számára.

#### <a name="debug-and-publish-the-pipeline"></a>A folyamat hibakeresése és közzététele

Elvégezheti a folyamat hibakeresését, mielőtt összetevőket (társított szolgáltatások, adatkészletek és folyamat) tenne közzé a Data Factoryben vagy a saját Azure Repos Git-adattárában.

1. A folyamat hibakereséséhez válassza a **Hibakeresés** elemet az eszköztáron. A folyamat futtatási állapotát az ablak alján található **Kimenet** lapon tekintheti meg.
1. A folyamat sikeres futtatását követően a felső eszköztáron válassza az **összes közzététele** lehetőséget. Ez a művelet közzéteszi a Data Factory létrehozott entitásokat (adatkészleteket és folyamatokat).
1. Várjon, amíg megjelenik a **Sikeres közzététel** üzenet. Az értesítési üzenetek megjelenítéséhez válassza az **értesítések megjelenítése** lehetőséget a jobb felső sarokban (harang gomb).


#### <a name="summary"></a>Összefoglalás
Az ebben a példában szereplő folyamat átmásolja az adatait a blob Storage-ból SQL Database a Data Factory felügyelt Virtual Networkban található privát végpontok használatával. Megtanulta végrehajtani az alábbi műveleteket:

* Adat-előállító létrehozása
* Másolási tevékenységgel rendelkező folyamat létrehozása.