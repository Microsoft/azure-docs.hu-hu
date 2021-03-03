---
title: Adatok másolása SQL Serverról blob Storage-ba a PowerShell használatával
description: Megismerheti az adatok a helyszíni adattárolókból az Azure-beli felhőbe, az Azure Data Factory saját üzemeltetésű Integration Runtime átjáróval történő másolásának folyamatát.
author: nabhishek
ms.author: abnarain
ms.service: data-factory
ms.topic: tutorial
ms.custom: seo-lt-2019; seo-dt-2019, devx-track-azurepowershell
ms.date: 02/18/2021
ms.openlocfilehash: 33c3138013c43062465cce16189e6c19d22ae4d5
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/03/2021
ms.locfileid: "101724931"
---
# <a name="tutorial-copy-data-from-a-sql-server-database-to-azure-blob-storage"></a>Oktatóanyag: adatok másolása SQL Server-adatbázisból az Azure Blob Storage-ba

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Ebben az oktatóanyagban a Azure PowerShell használatával hozzon létre egy olyan adatfeldolgozó folyamatot, amely egy SQL Server-adatbázisból másol át egy adatait az Azure Blob Storage-ba. Létrehozhat és alkalmazhat egy saját üzemeltetésű integrációs modult, amely adatokat helyez át a helyszíni és a felhőalapú adattárolók között.

> [!NOTE]
> Ez a cikk nem mutatja be részletesen a Data Factory szolgáltatást. További információkért lásd: [Az Azure Data Factory bemutatása](introduction.md).

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

Az előfizetésben található engedélyek megtekintéséhez kattintson az Azure Portalon a felhasználónevére a jobb felső sarokban, majd válassza az **Engedélyek** elemet. Ha több előfizetéshez is rendelkezik hozzáféréssel, válassza ki a megfelelő előfizetést. A felhasználók szerepkörhöz való hozzáadásával kapcsolatos útmutatásért tekintse meg az [Azure-szerepkörök kiosztása a Azure Portal használatával](../role-based-access-control/role-assignments-portal.md) című cikket.

### <a name="sql-server-2014-2016-and-2017"></a>SQL Server 2014, 2016 és 2017
Ebben az oktatóanyagban egy SQL Server-adatbázist használ *forrásként* szolgáló adattárként. Az oktatóanyagban létrehozott adatfeldolgozó folyamata a SQL Server-adatbázisból (forrás) származó adatok másolása az Azure Blob Storage-ba (fogadó). Ezután létre fog hozni egy **emp** nevű táblát az SQL Server-adatbázisban, és beszúr a táblába néhány mintabejegyzést.

1. Indítsa el az SQL Server Management Studiót. Ha még nincs telepítve a számítógépen, tekintse meg az [Az SQL Server Management Studio letöltését ismertető](/sql/ssms/download-sql-server-management-studio-ssms) szakaszt.

1. Csatlakozzon az SQL Server-példányához a hitelesítő adataival.

1. Hozzon létre egy mintaadatbázist. A fanézetben kattintson a jobb gombbal a **Databases** (Adatbázisok) elemre, majd válassza a **New Database** (Új adatbázis) elemet.

1. Az **New Database** (Új adatbázis) ablakban adjon nevet az új adatbázisnak, majd kattintson az **OK** gombra.

1. Az **EMP** tábla létrehozásához és a mintaadatok beszúrásához futtassa a következő lekérdezési szkriptet az adatbázison. A fanézetben kattintson a jobb gombbal a létrehozott adatbázisra, majd válassza a **New Query** (Új lekérdezés) elemet.

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
Ebben az oktatóanyagban egy általános célú Azure Storage-fiókot (ebben az esetben Azure Blob Storage-tárolót) használunk cél-/fogadóadattárként. Ha még nem rendelkezik általános célú Azure Storage-fiókkal, tekintse meg a [Tárfiók létrehozását](../storage/common/storage-account-create.md) ismertető cikket. Az oktatóanyagban létrehozott adatfeldolgozó folyamata az SQL Server-adatbázisból (forrás) származó adatok másolása erre az Azure Blob Storage-tárolóra (fogadó). 

#### <a name="get-storage-account-name-and-account-key"></a>Tárfióknév és fiókkulcs beszerzése
Ebben az oktatóanyagban az Azure Storage-fiók nevét és kulcsát használjuk. A tárfiók nevét és kulcsát az alábbi módon szerezheti be:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com) az Azure felhasználónevével és jelszavával.

1. A bal oldali ablaktáblán válassza ki a **További szolgáltatások** lehetőséget, szűrjön rá a **Tárolás** kulcsszóra, majd válassza ki a **Tárfiókok** lehetőséget.

    ![Tárfiók keresése](media/doc-common-process/search-storage-account.png)

1. A tárfiókok listájában állítson be szűrőt a tárfiók nevéhez (ha szükséges), majd válassza ki a tárfiókját.

1. A **Tárfiók** ablakban válassza a **Hozzáférési kulcsok** elemet.

1. Másolja a **Tárfiók neve** és **1. kulcs** mező értékét, majd illessze be őket egy jegyzettömbbe vagy más szerkesztőbe az oktatóanyag későbbi részeiben történő használatra.

#### <a name="create-the-adftutorial-container"></a>Adftutorial tároló létrehozása
Ebben a szakaszban egy **adftutorial** nevű BLOB-tárolót hoz létre az Azure Blob Storage-ban.

1. A **Tárfiók** ablakban váltson át az **Áttekintés** panelre, majd válassza a **Blobok** elemet.

    ![A Blobok elem választása](media/tutorial-hybrid-copy-powershell/select-blobs.png)

1. A **Blob service** ablakban válassza a **Tároló** elemet.

1. Az **Új tároló** ablak **Név** mezőjébe írja be az **adftutorial** nevet, majd kattintson az **OK** gombra.

    ![Tárolónév megadása](media/tutorial-hybrid-copy-powershell/new-container-dialog.png)

1. A tárolók listájában kattintson az **adftutorial** elemre.  

1. Tartsa meg a **tároló** ablakát a **adftutorial** megnyitásához. A segítségével ellenőrizheti az oktatóanyag eredményét. A Data Factory automatikusan létrehozza a kimeneti mappát a tárolóban, így nem kell újat létrehoznia.


### <a name="windows-powershell"></a>Windows PowerShell

#### <a name="install-azure-powershell"></a>Az Azure PowerShell telepítése

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Ha még nincs a gépén, telepítse az Azure PowerShell legújabb verzióját. Részletes információk: [Az Azure PowerShell telepítése és konfigurálása](/powershell/azure/install-Az-ps).

#### <a name="log-in-to-powershell"></a>Bejelentkezés a PowerShellbe

1. Indítsa el a PowerShellt a számítógépen, és ne zárja be a gyors üzembe helyezési oktatóanyag végéig. Ha bezárja és újra megnyitja, akkor újra futtatnia kell ezeket a parancsokat.

1. Futtassa a következő parancsot, majd adja meg az Azure Portalra való bejelentkezéshez használt Azure-beli felhasználói nevét és jelszavát:

    ```powershell
    Connect-AzAccount
    ```        

1. Ha több Azure-előfizetéssel rendelkezik, futtassa a következő parancsot a használni kívánt előfizetés kiválasztásához. Cserélje le az **SubscriptionId** -t az Azure-előfizetés azonosítójával:

    ```powershell
    Select-AzSubscription -SubscriptionId "<SubscriptionId>"    
    ```

## <a name="create-a-data-factory"></a>Adat-előállító létrehozása

1. Adjon meg egy olyan változót, amelyet később a PowerShell-parancsokban az erőforráscsoport neveként fog használni. Másolja az alábbi parancsszöveget a PowerShellbe, adja meg az [Azure-erőforráscsoport](../azure-resource-manager/management/overview.md) nevét (idézőjelek között, például így: `"adfrg"`), majd futtassa a parancsot. 
   
    ```powershell
    $resourceGroupName = "ADFTutorialResourceGroup"
    ```

1. Futtassa az alábbi parancsot az Azure-erőforráscsoport létrehozásához:

    ```powershell
    New-AzResourceGroup $resourceGroupName -location 'East US'
    ```

    Ha az erőforráscsoport már létezik, előfordulhat, hogy nem kívánja felülírni. Rendeljen egy másik értéket a `$resourceGroupName` változóhoz, majd futtassa újra a parancsot.

1. Adjon meg egy olyan változót, amelyet később a PowerShell-parancsokban az adat-előállító neveként használhat. A névnek betűvel vagy számmal kell kezdődnie, és csak betűket, számokat és kötőjelet (-) tartalmazhat.

    > [!IMPORTANT]
    >  Frissítse az adat-előállító nevét egy globálisan egyedi névvel. Például: ADFTutorialFactorySP1127.

    ```powershell
    $dataFactoryName = "ADFTutorialFactory"
    ```

1. Adjon meg egy változót az adat-előállító helyéhez:

    ```powershell
    $location = "East US"
    ```  

1. Az adat-előállító létrehozásához futtassa az alábbi `Set-AzDataFactoryV2`-parancsmagot:

    ```powershell       
    Set-AzDataFactoryV2 -ResourceGroupName $resourceGroupName -Location $location -Name $dataFactoryName
    ```

> [!NOTE]
>
> * Az adat-előállító nevének globálisan egyedinek kell lennie. Ha a következő hibaüzenetet kapja, módosítsa a nevet, majd próbálkozzon újra.
>    ```
>    The specified data factory name 'ADFv2TutorialDataFactory' is already in use. Data factory names must be globally unique.
>    ```
> * Az adat-előállító példányok létrehozásához annak a felhasználói fióknak, amellyel belép az Azure-ba, a *közreműködő* vagy *tulajdonos* szerepkör tagjának, vagy az Azure-előfizetés *rendszergazdájának* kell lennie.
> * Azon Azure-régiók megtekintéséhez, amelyekben jelenleg elérhető a Data Factory, a következő lapon válassza ki az Önt érdeklő régiókat, majd bontsa ki az **Elemzés** részt, és keresse meg a **Data Factory**: [Elérhető termékek régiók szerint](https://azure.microsoft.com/global-infrastructure/services/) szakaszt. Az adat-előállítók által használt adattárak (Azure Storage, Azure SQL Database stb.) és számítási erőforrások (Azure HDInsight stb.) más régiókban is lehetnek.
>
>

## <a name="create-a-self-hosted-integration-runtime"></a>Saját üzemeltetésű Integration Runtime létrehozása

Ebben a szakaszban egy saját üzemeltetésű Integration Runtime átjárót hozhat létre, és társíthatja azt az SQL Server-adatbázist futtató helyszíni géppel. A saját üzemeltetésű Integration Runtime az a komponens, amely adatokat másol a gépen futó SQL Server-adatbázisból az Azure Blob Storage-ba.

1. Hozzon létre egy változót az integrációs modul nevéhez. Adjon meg egy egyedi nevet, és jegyezze fel. Az oktatóanyag későbbi részében használni fogja.

    ```powershell
   $integrationRuntimeName = "ADFTutorialIR"
    ```

1. Hozzon létre egy saját üzemeltetésű integrációs modult.

    ```powershell
    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $resourceGroupName -DataFactoryName $dataFactoryName -Name $integrationRuntimeName -Type SelfHosted -Description "selfhosted IR description"
    ```

    Itt látható a minta kimenete:

    ```console
    Name              : ADFTutorialIR
    Type              : SelfHosted
    ResourceGroupName : <resourceGroupName>
    DataFactoryName   : <dataFactoryName>
    Description       : selfhosted IR description
    Id                : /subscriptions/<subscription ID>/resourceGroups/<resourceGroupName>/providers/Microsoft.DataFactory/factories/<dataFactoryName>/integrationruntimes/<integrationRuntimeName>
    ```

1. Futtassa az alábbi parancsot a létrehozott Integration Runtime állapotának lekéréséhez:

    ```powershell
   Get-AzDataFactoryV2IntegrationRuntime -name $integrationRuntimeName -ResourceGroupName $resourceGroupName -DataFactoryName $dataFactoryName -Status
    ```

    Itt látható a minta kimenete:

    ```console
    State                     : NeedRegistration
    Version                   :
    CreateTime                : 9/10/2019 3:24:09 AM
    AutoUpdate                : On
    ScheduledUpdateDate       :
    UpdateDelayOffset         :
    LocalTimeZoneOffset       :
    InternalChannelEncryption :
    Capabilities              : {}
    ServiceUrls               : {eu.frontend.clouddatahub.net}
    Nodes                     : {}
    Links                     : {}
    Name                      : <Integration Runtime name>
    Type                      : SelfHosted
    ResourceGroupName         : <resourceGroup name>
    DataFactoryName           : <dataFactory name>
    Description               : selfhosted IR description
    Id                        : /subscriptions/<subscription ID>/resourceGroups/<resourceGroupName>/providers/Microsoft.DataFactory/factories/<dataFactoryName>/integrationruntimes/<integrationRuntimeName>
    ```

1. Futtassa az alábbi parancsot a *hitelesítési kulcsok* lekéréséhez, hogy a saját üzemeltetésű Integration Runtime átjárót regisztrálhassa a Data Factory-szolgáltatásban a felhőben. Másolja be az egyik kulcsot (idézőjelek nélkül) a saját üzemeltetésű Integration Runtime regisztrálásához, amelyet a következő lépésben telepíteni fog a gépére.

    ```powershell
    Get-AzDataFactoryV2IntegrationRuntimeKey -Name $integrationRuntimeName -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName | ConvertTo-Json
    ```

    Itt látható a minta kimenete:

    ```json
    {
        "AuthKey1":  "IR@0000000000-0000-0000-0000-000000000000@xy0@xy@xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx=",
        "AuthKey2":  "IR@0000000000-0000-0000-0000-000000000000@xy0@xy@yyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyy="
    }
    ```

## <a name="install-the-integration-runtime"></a>Az Integration Runtime telepítése
1. Töltse le az [Azure Data Factory Integration Runtime](https://www.microsoft.com/download/details.aspx?id=39717) modult egy helyi Windows-gépre, és futtassa a telepítést.

1. A **Microsoft Integration Runtime telepítővarázslójának üdvözlő oldalán** kattintson a **Tovább** gombra.  

1. A **Végfelhasználói licencszerződés** ablakban fogadja el a felhasználási feltételeket és a licencszerződést, majd válassza a **Tovább** lehetőséget.

1. A **Célmappa** ablakban válassza a **Tovább** lehetőséget.

1. A **Microsoft Integration Runtime telepítésre kész** ablakban kattintson a **Telepítés** gombra.

1. **A Microsoft Integration Runtime telepítővarázslójának befejező lapján** kattintson a **Befejezés** gombra.

1. Az **Integration Runtime (helyi) regisztrálása** ablakban illessze be az előző szakaszban mentett kulcsot, és kattintson a **Regisztráció** gombra.

    ![Integrációs modul regisztrálása](media/tutorial-hybrid-copy-powershell/register-integration-runtime.png)

1. Az **új Integration Runtime (helyi) csomópont** ablakban válassza a **Befejezés** lehetőséget.

    ![Integration Runtime új csomópontja ablak](media/tutorial-hybrid-copy-powershell/new-integration-runtime-node-page.png)

 1. A helyi Integration Runtime sikeres regisztrációja esetén a következő üzenet jelenik meg:

    ![Sikeres regisztráció](media/tutorial-hybrid-copy-powershell/registered-successfully.png)

1. Az **Integration Runtime (helyi) regisztrálása** ablakban kattintson **A Configuration Manager indítása** gombra.

1. A csomópont a felhőszolgáltatáshoz való csatlakozásakor a következő üzenet jelenik meg:

    ![Csomópont csatlakoztatva](media/tutorial-hybrid-copy-powershell/node-is-connected.png)

1. Teszteltje az SQL Server-adatbázissal létesített kapcsolatot az alábbi módon:

    a. A **Configuration Manager** ablakban váltson a **diagnosztika** lapra.

    b. Az **Adatforrás típusa** mezőben válassza az **SqlServer** lehetőséget.

    c. Adja meg a kiszolgáló nevét.

    d. Adja meg az adatbázis nevét.

    e. Válassza ki a hitelesítési módot.

    f. Adja meg a felhasználónevet.

    : Adja meg a felhasználónévhez tartozó jelszót.

    h. Annak ellenőrzéséhez, hogy az Integration Runtime kapcsolódik-e az SQL Serverhez, kattintson a **Teszt** gombra.  
    ![Sikeres csatlakozás](media/tutorial-hybrid-copy-powershell/config-manager-diagnostics-tab.png)

    Sikeres csatlakozás esetén egy zöld pipa jelenik meg. Sikertelen csatlakozás esetén a hibához kapcsolódó hibaüzenet jelenik meg. Javítsa ki a hibákat, és ellenőrizze, hogy az Integration Runtime kapcsolódik-e az SQL Server-példányhoz.

    Jegyezze fel az összes korábbi értéket, hogy az oktatóanyag további részében is felhasználhassa őket.

## <a name="create-linked-services"></a>Társított szolgáltatások létrehozása
Adattárak és a számítási szolgáltatások adat-előállítóhoz történő társításához társított szolgáltatásokat kell létrehoznia az adat-előállítóban. Ebben az oktatóanyagban összekapcsolja az Azure Storage-fiókját, és SQL Server példányt az adattárhoz. A társított szolgáltatások azon kapcsolatadatokkal rendelkeznek, amelyeket a Data Factory szolgáltatás használ futtatáskor a hozzájuk való kapcsolódáshoz.

### <a name="create-an-azure-storage-linked-service-destinationsink"></a>Azure Storage-beli társított szolgáltatás (cél/fogadó) létrehozása
Ebben a lépésben az Azure Storage-fiókot társítja az adat-előállítóval.

1. Hozzon létre egy *AzureStorageLinkedService.json* nevű JSON-fájlt a *C:\ADFv2Tutorial* mappában a következő kóddal. Ha még nem létezik *ADFv2Tutorial* nevű mappa, hozzon létre egyet.  

    > [!IMPORTANT]
    > A fájl mentése előtt cserélje le a \<accountName> és a \<accountKey> nevet az Azure Storage-fiókjának nevére és kulcsára. Ezeket az [Előfeltételek](#get-storage-account-name-and-account-key) szakaszban jegyezte fel.

   ```json
    {
        "name": "AzureStorageLinkedService",
        "properties": {
            "annotations": [],
            "type": "AzureBlobStorage",
            "typeProperties": {
                "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountName>;AccountKey=<accountKey>;EndpointSuffix=core.windows.net"
            }
        }
    }
   ```

1. A PowerShellben váltson a *C:\ADFv2Tutorial* mappára.
   ```powershell
   Set-Location 'C:\ADFv2Tutorial'    
   ```

1. Futtassa a `Set-AzDataFactoryV2LinkedService` parancsmagot az AzureStorageLinkedService társított szolgáltatás létrehozásához:

   ```powershell
   Set-AzDataFactoryV2LinkedService -DataFactoryName $dataFactoryName -ResourceGroupName $ResourceGroupName -Name "AzureStorageLinkedService" -File ".\AzureStorageLinkedService.json"
   ```

   Itt látható egy mintakimenet:

    ```console
    LinkedServiceName : AzureStorageLinkedService
    ResourceGroupName : <resourceGroup name>
    DataFactoryName   : <dataFactory name>
    Properties        : Microsoft.Azure.Management.DataFactory.Models.AzureBlobStorageLinkedService
    ```

    Ha „fájl nem található” hiba jelenik meg, a `dir` parancs futtatásával ellenőrizze, hogy a fájl létezik-e. Ha a fájlnévhez tartozó kiterjesztés *.txt* (például AzureStorageLinkedService.json.txt), távolítsa el, majd futtassa ismét a PowerShell-parancsot.

### <a name="create-and-encrypt-a-sql-server-linked-service-source"></a>SQL Server-alapú társított szolgáltatás létrehozása és titkosítása (forrás)
Ebben a lépésben összekapcsolja SQL Server példányát az adatelőállítóval.

1. Hozzon létre egy *SqlServerLinkedService.json* nevű JSON-fájlt a *C:\ADFv2Tutorial* mappában a következő kód használatával:

    > [!IMPORTANT]
    > Válassza ki az SQL Serverhez való kapcsolódáshoz használt hitelesítési módszernek megfelelő szakaszt.

    **SQL-hitelesítés használata (sa):**

    ```json
    {  
        "name":"SqlServerLinkedService",
        "type":"Microsoft.DataFactory/factories/linkedservices",
        "properties":{  
            "annotations":[  

            ],
            "type":"SqlServer",
            "typeProperties":{  
                "connectionString":"integrated security=False;data source=<serverName>;initial catalog=<databaseName>;user id=<userName>;password=<password>"
            },
            "connectVia":{  
                "referenceName":"<integration runtime name> ",
                "type":"IntegrationRuntimeReference"
            }
        }
    }
   ```    

    **Windows-hitelesítés használata:**

    ```json
    {  
        "name":"SqlServerLinkedService",
        "type":"Microsoft.DataFactory/factories/linkedservices",
        "properties":{  
            "annotations":[  

            ],
            "type":"SqlServer",
            "typeProperties":{  
                "connectionString":"integrated security=True;data source=<serverName>;initial catalog=<databaseName>",
                "userName":"<username> or <domain>\\<username>",
                "password":{  
                    "type":"SecureString",
                    "value":"<password>"
                }
            },
            "connectVia":{  
                "referenceName":"<integration runtime name>",
                "type":"IntegrationRuntimeReference"
            }
        }
    }
    ```

    > [!IMPORTANT]
    > - Válassza ki az SQL Server-példányhoz való kapcsolódáshoz használt hitelesítési módszernek megfelelő szakaszt.
    > - Cserélje le az értékét  **\<integration runtime name>** az Integration Runtime nevére.
    > - A fájl mentése előtt cserélje le a,, **\<servername>** **\<databasename>** **\<username>** és **\<password>** értéket a SQL Server példányának értékeire.
    > - Ha fordított perjel karaktert (\\) kell használnia a felhasználói fiók vagy a kiszolgáló nevében, használja előtte a feloldójelet (\\). Használja például a *SajátTartomány \\ \\ myuser*.

1. A bizalmas adatok (felhasználónév, jelszó stb.) titkosításához futtassa a `New-AzDataFactoryV2LinkedServiceEncryptedCredential` parancsmagot.  
    A titkosítás a hitelesítő adatokat az adatvédelmi API (DPAPI) segítségével titkosítja. A titkosított hitelesítő adatok tárolása a saját üzemeltetésű integrációs modul csomópontján helyileg történik (a helyi gépen). A kimenő hasznos adatok átirányíthatóak egy másik JSON-fájlba (ebben az esetben az *encryptedLinkedService.json* fájlba), amely titkosított hitelesítő adatokat tartalmaz.

   ```powershell
   New-AzDataFactoryV2LinkedServiceEncryptedCredential -DataFactoryName $dataFactoryName -ResourceGroupName $ResourceGroupName -IntegrationRuntimeName $integrationRuntimeName -File ".\SQLServerLinkedService.json" > encryptedSQLServerLinkedService.json
   ```

1. Futtassa az alábbi parancsot, amely létrehozza az EncryptedSqlServerLinkedService fájlt:

   ```powershell
   Set-AzDataFactoryV2LinkedService -DataFactoryName $dataFactoryName -ResourceGroupName $ResourceGroupName -Name "EncryptedSqlServerLinkedService" -File ".\encryptedSqlServerLinkedService.json"
   ```


## <a name="create-datasets"></a>Adatkészletek létrehozása
Ebben a lépésben bemeneti és kimeneti adatkészleteket hoz létre. A másolási művelet bemeneti és kimeneti adatait képviselik, amely adatokat másol a SQL Server-adatbázisból az Azure Blob Storage-ba.

### <a name="create-a-dataset-for-the-source-sql-server-database"></a>Adatkészlet létrehozása a forrás SQL Server-adatbázishoz
Ebben a lépésben megadhat egy adatkészletet, amely az SQL Server-adatbázispéldányban tárolt adatokat jelöli. Az adatkészlet típusa SqlServerTable. Ez az előző lépésben létrehozott SQL Server társított szolgáltatásra vonatkozik. A társított szolgáltatás azon kapcsolatadatokkal rendelkezik, amelyeket a Data Factory szolgáltatás használ futtatáskor az SQL Server-példányhoz való kapcsolódásra. Ez az adatkészlet határozza meg az SQL-táblát az adatokat tartalmazó adatbázisban. Ebben az oktatóanyagban a forrásadatokat az **emp** tábla tartalmazza.

1. Hozzon létre egy *SqlServerDataset.json* nevű JSON-fájlt a *C:\ADFv2Tutorial* mappában a következő kóddal:  
    ```json
    {  
        "name":"SqlServerDataset",
        "properties":{  
            "linkedServiceName":{  
                "referenceName":"EncryptedSqlServerLinkedService",
                "type":"LinkedServiceReference"
            },
            "annotations":[  

            ],
            "type":"SqlServerTable",
            "schema":[  

            ],
            "typeProperties":{  
                "schema":"dbo",
                "table":"emp"
            }
        }
    }
    ```

1. Az SqlServerDataset adatkészlet létrehozásához futtassa a `Set-AzDataFactoryV2Dataset` parancsmagot.

    ```powershell
    Set-AzDataFactoryV2Dataset -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "SqlServerDataset" -File ".\SqlServerDataset.json"
    ```

    Itt látható a minta kimenete:

    ```console
    DatasetName       : SqlServerDataset
    ResourceGroupName : <resourceGroupName>
    DataFactoryName   : <dataFactoryName>
    Structure         :
    Properties        : Microsoft.Azure.Management.DataFactory.Models.SqlServerTableDataset
    ```

### <a name="create-a-dataset-for-azure-blob-storage-sink"></a>Adatkészlet létrehozása a Azure Blob Storage-hez (fogadó)
Ebben a lépésben az Azure Blob Storage-be másolni kívánt adatokat képviselő adatkészletet adja meg. Az adatkészlet típusa AzureBlob. Ez az oktatóanyagban korábban létrehozott Azure Storage társított szolgáltatásra vonatkozik.

A társított szolgáltatás azon kapcsolatadatokkal rendelkezik, amelyeket az adat-előállító használ futtatáskor az Azure Storage-fiókhoz való kapcsolódásra. Ez az adatkészlet azt az Azure Storage-beli mappát határozza meg, amelybe a rendszer az adatokat másolja az SQL Server-adatbázisból. Ebben az oktatóanyagban a mappa a következő: *adftutorial/fromonprem*, ahol az `adftutorial` a blobtároló, a `fromonprem` pedig a mappa.

1. Hozzon létre egy *AzureBlobDataset.json* nevű JSON-fájlt a *C:\ADFv2Tutorial* mappában a következő kóddal:

    ```json
    {  
        "name":"AzureBlobDataset",
        "properties":{  
            "linkedServiceName":{  
                "referenceName":"AzureStorageLinkedService",
                "type":"LinkedServiceReference"
            },
            "annotations":[  

            ],
            "type":"DelimitedText",
            "typeProperties":{  
                "location":{  
                    "type":"AzureBlobStorageLocation",
                    "folderPath":"fromonprem",
                    "container":"adftutorial"
                },
                "columnDelimiter":",",
                "escapeChar":"\\",
                "quoteChar":"\""
            },
            "schema":[  

            ]
        },
        "type":"Microsoft.DataFactory/factories/datasets"
    }
    ```

1. Az AzureBlobDataset adatkészlet létrehozásához futtassa a `Set-AzDataFactoryV2Dataset` parancsmagot.

    ```powershell
    Set-AzDataFactoryV2Dataset -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "AzureBlobDataset" -File ".\AzureBlobDataset.json"
    ```

    Itt látható a minta kimenete:

    ```console
    DatasetName       : AzureBlobDataset
    ResourceGroupName : <resourceGroupName>
    DataFactoryName   : <dataFactoryName>
    Structure         :
    Properties        : Microsoft.Azure.Management.DataFactory.Models.DelimitedTextDataset
    ```

## <a name="create-a-pipeline"></a>Folyamat létrehozása
Ebben az oktatóanyagban létre fog hozni egy másolási tevékenységgel rendelkező folyamatot. A másolási tevékenység az SqlServerDataset készletet használja bemeneti adatkészletként, illetve az AzureBlobDataset készletet kimeneti adatkészletként. A forrás típusa *SqlSource*, a fogadó típusa *BlobSink*.

1. Hozzon létre egy *SqlServerToBlobPipeline.json* nevű JSON-fájlt a *C:\ADFv2Tutorial* mappában a következő kóddal:

    ```json
    {  
        "name":"SqlServerToBlobPipeline",
        "properties":{  
            "activities":[  
                {  
                    "name":"CopySqlServerToAzureBlobActivity",
                    "type":"Copy",
                    "dependsOn":[  

                    ],
                    "policy":{  
                        "timeout":"7.00:00:00",
                        "retry":0,
                        "retryIntervalInSeconds":30,
                        "secureOutput":false,
                        "secureInput":false
                    },
                    "userProperties":[  

                    ],
                    "typeProperties":{  
                        "source":{  
                            "type":"SqlServerSource"
                        },
                        "sink":{  
                            "type":"DelimitedTextSink",
                            "storeSettings":{  
                                "type":"AzureBlobStorageWriteSettings"
                            },
                            "formatSettings":{  
                                "type":"DelimitedTextWriteSettings",
                                "quoteAllText":true,
                                "fileExtension":".txt"
                            }
                        },
                        "enableStaging":false
                    },
                    "inputs":[  
                        {  
                            "referenceName":"SqlServerDataset",
                            "type":"DatasetReference"
                        }
                    ],
                    "outputs":[  
                        {  
                            "referenceName":"AzureBlobDataset",
                            "type":"DatasetReference"
                        }
                    ]
                }
            ],
            "annotations":[  

            ]
        }
    }
    ```

1. Az SQLServerToBlobPipeline folyamat létrehozásához futtassa a `Set-AzDataFactoryV2Pipeline` parancsmagot.

    ```powershell
    Set-AzDataFactoryV2Pipeline -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "SQLServerToBlobPipeline" -File ".\SQLServerToBlobPipeline.json"
    ```

    Itt látható a minta kimenete:

    ```console
    PipelineName      : SQLServerToBlobPipeline
    ResourceGroupName : <resourceGroupName>
    DataFactoryName   : <dataFactoryName>
    Activities        : {CopySqlServerToAzureBlobActivity}
    Parameters        :  
    ```

## <a name="create-a-pipeline-run"></a>Folyamat futásának létrehozása
Indítsa el az SQLServerToBlobPipeline folyamat futását, és őrizze meg a folyamat futtatási azonosítóját későbbi monitorozás céljából.

```powershell
$runId = Invoke-AzDataFactoryV2Pipeline -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -PipelineName 'SQLServerToBlobPipeline'
```

## <a name="monitor-the-pipeline-run"></a>A folyamat futásának monitorozása

1. Az SQLServerToBlobPipeline folyamat futási állapotának folyamatos ellenőrzéséhez futtassa a következő szkriptet PowerShellben, majd nyomtassa ki a végső eredményt:

    ```powershell
    while ($True) {
        $result = Get-AzDataFactoryV2ActivityRun -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -PipelineRunId $runId -RunStartedAfter (Get-Date).AddMinutes(-30) -RunStartedBefore (Get-Date).AddMinutes(30)

        if (($result | Where-Object { $_.Status -eq "InProgress" } | Measure-Object).count -ne 0) {
            Write-Host "Pipeline run status: In Progress" -foregroundcolor "Yellow"
            Start-Sleep -Seconds 30
        }
        else {
            Write-Host "Pipeline 'SQLServerToBlobPipeline' run finished. Result:" -foregroundcolor "Yellow"
            $result
            break
        }
    }
    ```

    Itt látható a példa futtatás kimenete:

    ```console
    ResourceGroupName    : <resourceGroupName>
    DataFactoryName      : <dataFactoryName>
    ActivityRunId        : 24af7cf6-efca-4a95-931d-067c5c921c25
    ActivityName         : CopySqlServerToAzureBlobActivity
    ActivityType         : Copy
    PipelineRunId        : 7b538846-fd4e-409c-99ef-2475329f5729
    PipelineName         : SQLServerToBlobPipeline
    Input                : {source, sink, enableStaging}
    Output               : {dataRead, dataWritten, filesWritten, sourcePeakConnections...}
    LinkedServiceName    :
    ActivityRunStart     : 9/11/2019 7:10:37 AM
    ActivityRunEnd       : 9/11/2019 7:10:58 AM
    DurationInMs         : 21094
    Status               : Succeeded
    Error                : {errorCode, message, failureType, target}
    AdditionalProperties : {[retryAttempt, ], [iterationHash, ], [userProperties, {}], [recoveryStatus, None]...}
    ```

1. Az SQLServerToBlobPipeline folyamat futtatási azonosítójának lekéréséhez és a tevékenységfuttatás részletes eredményének áttekintéséhez futtassa az alábbi parancsot:

    ```powershell
    Write-Host "Pipeline 'SQLServerToBlobPipeline' run result:" -foregroundcolor "Yellow"
    ($result | Where-Object {$_.ActivityName -eq "CopySqlServerToAzureBlobActivity"}).Output.ToString()
    ```

    Itt látható a példa futtatás kimenete:

    ```json
    {  
        "dataRead":36,
        "dataWritten":32,
        "filesWritten":1,
        "sourcePeakConnections":1,
        "sinkPeakConnections":1,
        "rowsRead":2,
        "rowsCopied":2,
        "copyDuration":18,
        "throughput":0.01,
        "errors":[  

        ],
        "effectiveIntegrationRuntime":"ADFTutorialIR",
        "usedParallelCopies":1,
        "executionDetails":[  
            {  
                "source":{  
                    "type":"SqlServer"
                },
                "sink":{  
                    "type":"AzureBlobStorage",
                    "region":"CentralUS"
                },
                "status":"Succeeded",
                "start":"2019-09-11T07:10:38.2342905Z",
                "duration":18,
                "usedParallelCopies":1,
                "detailedDurations":{  
                    "queuingDuration":6,
                    "timeToFirstByte":0,
                    "transferDuration":5
                }
            }
        ]
    }
    ```

## <a name="verify-the-output"></a>Kimenet ellenőrzése
A folyamat automatikusan létrehozza a *fromonprem* nevű kimeneti mappát az `adftutorial` blobtárolóban. Győződjön meg arról, hogy a *dbo.emp.txt* fájl megjelenik a kimeneti mappában.

1. Az Azure Portal **adftutorial** tároló ablakában kattintson a **Frissítés** elemre a kimeneti mappa megtekintéséhez.
1. A mappák listájában válassza a `fromonprem` elemet.
1. Ellenőrizze, hogy megjelenik-e a `dbo.emp.txt` nevű fájl.

    ![Kimeneti fájl](media/tutorial-hybrid-copy-powershell/fromonprem-file.png)


## <a name="next-steps"></a>Következő lépések
A példában szereplő folyamat adatokat másol az egyik helyről egy másikra egy Azure Blob Storage-ban. Megtanulta végrehajtani az alábbi műveleteket:

> [!div class="checklist"]
> * Adat-előállító létrehozása
> * Hozzon létre egy saját üzemeltetésű integrációs modult.
> * SQL Server- és Azure Storage-beli társított szolgáltatások létrehozása.
> * SQL Server- és Azure Blob-adatkészletek létrehozása.
> * Folyamat létrehozása másolási tevékenységgel az adatok áthelyezéséhez
> * Folyamat futásának indítása
> * A folyamat futásának monitorozása.

A Data Factory által támogatott adattárak listáját lásd: [támogatott adattárak](copy-activity-overview.md#supported-data-stores-and-formats).

Folytassa a következő oktatóanyaggal, amelyben az adatok egy forrásból egy célhelyre történő tömeges másolását ismerheti meg:

> [!div class="nextstepaction"]
>[Adatok tömeges másolása](tutorial-bulk-copy.md)