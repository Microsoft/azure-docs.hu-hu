---
title: Adatátviteli adatkezelés átjáró
description: Állítson be egy adatátjárót a helyszíni és a felhő közötti adatáthelyezéshez. Az adatáthelyezéshez használja a Azure Data Factory adatkezelés átjáróját.
author: nabhishek
ms.author: abnarain
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/10/2018
robots: noindex
ms.openlocfilehash: 53fce1744ccbf4289b2415e926e084c90d708a13
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "100380287"
---
# <a name="move-data-between-on-premises-sources-and-the-cloud-with-data-management-gateway"></a>Adatáthelyezés a helyszíni források és a felhő között adatkezelés átjáróval
> [!NOTE]
> Ez a cikk a Data Factory 1-es verziójára vonatkozik. Ha a Data Factory szolgáltatás aktuális verzióját használja, olvassa el a következőt: az [Adatmásolás a helyszíni és a felhő között a Data Factory használatával](../tutorial-hybrid-copy-powershell.md).

Ez a cikk áttekintést nyújt a helyszíni adattárak és a Felhőbeli adattárak közötti adatintegrációról Data Factory használatával. Az [adatátviteli tevékenységekre](data-factory-data-movement-activities.md) és a más adat-előállító alapfogalmakra vonatkozó cikkekre épül: [adatkészletek](data-factory-create-datasets.md) és [folyamatok](data-factory-create-pipelines.md).

## <a name="data-management-gateway"></a>Adatkezelési átjáró
Az adatok helyszíni adattárba való áthelyezésének engedélyezéséhez telepítenie kell adatkezelés átjárót a helyszíni gépen. Az átjáró telepíthető ugyanarra a gépre, mint az adattár vagy egy másik gépen, ha az átjáró csatlakozni tud az adattárhoz.

> [!IMPORTANT]
> Adatkezelés átjáróval kapcsolatos részletekért tekintse meg a [adatkezelés Gateway](data-factory-data-management-gateway.md) -cikket.

Az alábbi bemutató bemutatja, hogyan hozhat létre egy adatfeldolgozót egy olyan folyamattal, amely egy helyszíni **SQL Server** -adatbázisból egy Azure Blob Storage-ba helyezi át az adatátvitelt. A bemutató részeként telepíti és konfigurálja az adatkezelési átjárót a gépen.

## <a name="walkthrough-copy-on-premises-data-to-cloud"></a>Forgatókönyv: helyszíni Adatmásolás a felhőbe
Ebben az útmutatóban a következő lépéseket hajtja végre:

1. Adat-előállító létrehozása
2. Hozzon létre egy adatkezelési átjárót.
3. Társított szolgáltatások létrehozása a forrás-és fogadó adattárakhoz.
4. Adatkészletek létrehozása a bemeneti és kimeneti adatok ábrázolásához.
5. Folyamat létrehozása másolási tevékenységgel az adatok áthelyezéséhez

## <a name="prerequisites-for-the-tutorial"></a>Az oktatóanyag előfeltételei
A forgatókönyv elkezdése előtt a következő előfeltételek szükségesek:

* **Azure-előfizetés**.  Ha nem rendelkezik előfizetéssel, mindössze néhány perc alatt létrehozhat egy ingyenes próbafiókot. További részletekért tekintse meg az [ingyenes próbaverziót](https://azure.microsoft.com/pricing/free-trial/) ismertető cikket.
* **Azure Storage-fiók**. Ebben az oktatóanyagban a blob Storage-t használja **célként/fogadó** adattárként. Ha nem rendelkezik Azure Storage-fiókkal, tekintse meg a [Storage-fiók létrehozása](../../storage/common/storage-account-create.md) című cikket a létrehozás lépéseihez.
* **SQL Server**. Ebben az oktatóanyagban egy SQL Server-adatbázist használ **forrásként** szolgáló adattárként.

## <a name="create-data-factory"></a>Data factory létrehozása
Ebben a lépésben a Azure Portal használatával hoz létre egy **ADFTutorialOnPremDF** nevű Azure Data Factory-példányt.

1. Jelentkezzen be a [Azure Portalba](https://portal.azure.com).
2. Kattintson **az erőforrás létrehozása** elemre, majd az **intelligencia és Analitika** elemre, majd a **Data Factory** elemre.

   ![New (Új)->DataFactory](./media/data-factory-move-data-between-onprem-and-cloud/NewDataFactoryMenu.png)  
3. Az **új adatgyár** lapon adja meg a **ADFTutorialOnPremDF** nevet.

    ![Hozzáadás a Kezdőpulton](./media/data-factory-move-data-between-onprem-and-cloud/OnPremNewDataFactoryAddToStartboard.png)

   > [!IMPORTANT]
   > Az Azure data factory nevének globálisan egyedinek kell lennie. Ha a következő hibaüzenetet kapja: a **"ADFTutorialOnPremDF" nem érhető el** az adatfeldolgozó neve, módosítsa az adatgyár nevét (például yournameADFTutorialOnPremDF), és próbálkozzon újra a létrehozással. Ezt a nevet a ADFTutorialOnPremDF helyett használja az oktatóanyag hátralévő lépéseinek végrehajtásához.
   >
   > Az adatfeldolgozó neve a jövőben **DNS-** névként regisztrálható, így nyilvánosan láthatóvá válik.
   >
   >
4. Jelölje ki azt az **Azure-előfizetést**, ahol létre szeretné hozni a data factoryt.
5. Jelöljön ki egy meglévő **erőforráscsoportot**, vagy hozzon létre egyet. Az oktatóanyaghoz hozzon létre egy nevű erőforráscsoportot: **ADFTutorialResourceGroup**.
6. Kattintson a **Létrehozás** gombra az **új adatgyár** oldalon.

   > [!IMPORTANT]
   > Data Factory-példány létrehozásához a [Data Factory közreműködője](../../role-based-access-control/built-in-roles.md#data-factory-contributor) szerepkör tagjának kell lennie az előfizetés/erőforráscsoport szintjén.
   >
   >
7. A létrehozás befejezését követően megjelenik a **Data Factory** lap az alábbi képen látható módon:

   ![Data Factory Kezdőlap](./media/data-factory-move-data-between-onprem-and-cloud/OnPremDataFactoryHomePage.png)

## <a name="create-gateway"></a>Átjáró létrehozása
1. A **Data Factory** lapon kattintson a Létrehozás gombra, **és telepítse** a csempét, hogy elindítsa az adatelőállító **szerkesztőjét** .

    ![Az Author and deploy (Fejlesztés és üzembe helyezés) csempe](./media/data-factory-move-data-between-onprem-and-cloud/author-deploy-tile.png)
2. A Data Factory-szerkesztőben kattintson a **... elemre. További információ** az eszköztáron, majd kattintson az **új adatátjáró** elemre. Azt is megteheti, hogy a jobb gombbal az **adatátjárók** elemre kattint a fanézetben, majd az **új adatátjáró** elemre.

   ![Új adatátjáró az eszköztáron](./media/data-factory-move-data-between-onprem-and-cloud/NewDataGateway.png)
3. A **Létrehozás** lapon adja meg a **adftutorialgateway** nevet a **név** mezőben, majd kattintson **az OK** gombra.     

    ![Átjáró létrehozása lap](./media/data-factory-move-data-between-onprem-and-cloud/OnPremCreateGatewayBlade.png)

    > [!NOTE]
    > Ebben az útmutatóban a logikai átjárót csak egy csomóponttal (helyszíni Windows-géppel) hozza létre. Egy adatkezelési átjárót úgy méretezheti fel, hogy több helyszíni gépet társít az átjáróhoz. Az adatáthelyezési feladatok növekvő száma növelhető, amely egyidejűleg futhat egy csomóponton. Ez a funkció egyetlen csomóponttal rendelkező logikai átjáró esetén is elérhető. Részletekért lásd: az [adatkezelési átjáró skálázása Azure Data Factory](data-factory-data-management-gateway-high-availability-scalability.md) cikkben.  
4. A **configure (Konfigurálás** ) lapon kattintson a **telepítés közvetlenül ezen a számítógépen** elemre. Ez a művelet letölti az átjáró telepítési csomagját, telepíti, konfigurálja és regisztrálja az átjárót a számítógépen.  

   > [!NOTE]
   > Használja az Internet Explorert vagy a Microsoft ClickOnce-kompatibilis webböngészőt.
   >
   > Ha a Chrome-ot használja, lépjen a [Chrome webáruházba](https://chrome.google.com/webstore/), keressen a "ClickOnce" kulcsszóra, válassza ki az egyik ClickOnce-bővítményt, és telepítse.
   >
   > Tegye ugyanezt a Firefox esetében is (telepítse a bővítményt). Kattintson a **menü megnyitása** gombra az eszköztáron (**három vízszintes vonal** a jobb felső sarokban), kattintson a **bővítmények** lehetőségre, keressen rá a "ClickOnce" kulcsszóra, válassza ki az egyik ClickOnce-bővítményt, és telepítse azt.    
   >
   >

    ![Átjáró – Konfigurálás lap](./media/data-factory-move-data-between-onprem-and-cloud/OnPremGatewayConfigureBlade.png)

    Így a legegyszerűbb módszer (egy kattintással) az átjáró letöltésére, telepítésére, konfigurálására és regisztrálására egyetlen lépésben. Láthatja, hogy a **Microsoft adatkezelés Gateway Configuration Manager** alkalmazás telepítve van a számítógépen. A végrehajtható **ConfigManager.exe** a mappában is megtalálható: **C:\Program Files\Microsoft adatkezelés Gateway\2.0\Shared**.

    Az átjárót manuálisan is letöltheti és telepítheti az ezen a lapon található hivatkozások segítségével, és regisztrálhatja az **új kulcs** szövegmezőben látható kulccsal.

    Az átjáróval kapcsolatos további információkért lásd [adatkezelés átjáróról](data-factory-data-management-gateway.md) szóló cikket.

   > [!NOTE]
   > A adatkezelés-átjáró sikeres telepítéséhez és konfigurálásához a helyi számítógép rendszergazdájának kell lennie. További felhasználókat is hozzáadhat a **adatkezelés átjáró felhasználók** helyi Windows-csoportjához. A csoport tagjai a adatkezelés Gateway Configuration Manager eszközzel konfigurálhatják az átjárót.
   >
   >
5. Várjon néhány percet, vagy várjon, amíg meg nem jelenik a következő értesítési üzenet:

    ![Az átjáró telepítése sikerült](./media/data-factory-move-data-between-onprem-and-cloud/gateway-install-success.png)
6. Indítsa el **adatkezelés Gateway Configuration Manager** alkalmazást a számítógépen. A **Keresés** ablakban írja be **adatkezelés átjárót** a segédprogram eléréséhez. A végrehajtható **ConfigManager.exe** a mappában is megtalálható: **C:\Program Files\Microsoft adatkezelés Gateway\2.0\Shared**

    ![Átjáró Configuration Manager](./media/data-factory-move-data-between-onprem-and-cloud/OnPremDMGConfigurationManager.png)
7. Ellenőrizze, hogy megjelenik-e az `adftutorialgateway is connected to the cloud service` üzenet. A Lenti állapotsor a **Cloud Service-hez csatlakozik,** és **zöld pipa jelzi**.

    A **Kezdőlap** lapon a következő műveleteket is végrehajthatja:

   * **Regisztrálja** az átjárót egy kulccsal a Azure Portal a regisztráció gomb használatával.
   * **Állítsa le** az átjárót futtató gépen futó adatkezelés átjáró szolgáltatást.
   * A **frissítések ütemezett frissítését** a nap adott időpontjában kell telepíteni.
   * Az átjáró **utolsó frissítésének** megtekintése.
   * Itt adhatja meg, hogy mikor lehet telepíteni az átjáró frissítését.
8. Váltson a **Beállítások** lapra. A **tanúsítvány** szakaszban megadott tanúsítvány a portálon megadott helyszíni adattároló hitelesítő adatainak titkosítására és visszafejtésére szolgál. választható Kattintson a **módosítás** gombra a saját tanúsítványának használatához. Alapértelmezés szerint az átjáró a Data Factory szolgáltatás által automatikusan generált tanúsítványt használja.

    ![Átjáró-tanúsítvány konfigurálása](./media/data-factory-move-data-between-onprem-and-cloud/gateway-certificate.png)

    A **Beállítások** lapon a következő műveleteket is végrehajthatja:

   * Az átjáró által használt tanúsítvány megtekintése vagy exportálása.
   * Módosítsa az átjáró által használt HTTPS-végpontot.    
   * Állítsa be az átjáró által használandó HTTP-proxyt.     
9. választható Váltson a **diagnosztika** lapra, jelölje be a **részletes naplózás engedélyezése** beállítást, ha engedélyezni szeretné a részletes naplózást, amellyel elháríthatja az átjáróval kapcsolatos problémákat. A naplózási információk a **Eseménynapló** **alkalmazások és szolgáltatások naplók**  ->  **adatkezelés átjáró** csomópontjában találhatók.

    ![Diagnosztika lap](./media/data-factory-move-data-between-onprem-and-cloud/diagnostics-tab.png)

    A **diagnosztika** lapon a következő műveleteket is végrehajthatja:

   * A **tesztelési kapcsolatok** szakaszt egy helyszíni adatforráshoz használja az átjáró használatával.
   * Kattintson a **naplók megtekintése** lehetőségre a adatkezelés átjáró naplójának Eseménynapló ablakban való megjelenítéséhez.
   * Kattintson a **naplók küldése** elemre, ha az elmúlt hét nap naplóit tartalmazó zip-fájlt szeretne feltölteni a Microsoftnak a problémák elhárítása érdekében.
10. A **diagnosztika** lapon, a **kapcsolat tesztelése** szakaszban válassza a **SQLServer** lehetőséget az adattár típusához, adja meg az adatbázis-kiszolgáló nevét, az adatbázis nevét, a hitelesítés típusát, adja meg a felhasználónevet és a jelszót, majd kattintson a **tesztelés** elemre annak teszteléséhez, hogy az átjáró tud-e csatlakozni az adatbázishoz.
11. Váltson a böngészőre, és a **Azure Portal** kattintson az **OK** gombra a **Konfigurálás** lapon, majd az **új adatátjáró** lapon.
12. A bal oldali fanézetben a **adftutorialgateway** alatt található **adatátjárók** területen kell megjelennie.  Ha rákattint, megjelenik a társított JSON.

## <a name="create-linked-services"></a>Társított szolgáltatások létrehozása
Ebben a lépésben két társított szolgáltatást hoz létre: **AzureStorageLinkedService** és **SqlServerLinkedService**. A **SqlServerLinkedService** egy SQL Server adatbázisra hivatkozik, és a **AzureStorageLinkedService** társított szolgáltatás egy Azure BLOB-tárolót csatol az adatelőállítóhoz. Az útmutató későbbi részében létrehoz egy folyamatot, amely átmásolja a SQL Server adatbázis adatait az Azure Blob-tárolóba.

#### <a name="add-a-linked-service-to-a-sql-server-database"></a>Társított szolgáltatás hozzáadása SQL Server adatbázishoz
1. A **Data Factory szerkesztőben** kattintson az eszköztár **új adattár** elemére, és válassza a **SQL Server** lehetőséget.

   ![Új SQL Server társított szolgáltatás](./media/data-factory-move-data-between-onprem-and-cloud/NewSQLServer.png)
2. A jobb oldalon található **JSON-szerkesztőben** hajtsa végre a következő lépéseket:

   1. A **átjáró neve** esetében válassza a **adftutorialgateway**.    
   2. A **ConnectionString** alkalmazásban hajtsa végre a következő lépéseket:    

      1. A **kiszolgálónév** mezőbe írja be annak a kiszolgálónak a nevét, amely a SQL Server adatbázist üzemelteti.
      2. A **databasename** mezőben adja meg az adatbázis nevét.
      3. Kattintson a **titkosítás** gombra az eszköztáron. Megjelenik a hitelesítő adatok kezelője alkalmazás.

         ![Hitelesítő adatok kezelője alkalmazás](./media/data-factory-move-data-between-onprem-and-cloud/credentials-manager-application.png)
      4. A **hitelesítő adatok beállítása** párbeszédpanelen adja meg a hitelesítés típusát, a felhasználónevet és a jelszót, majd kattintson **az OK** gombra. Ha a kapcsolatok sikeresek, a rendszer a titkosított hitelesítő adatokat a JSON-ban tárolja, és bezárja a párbeszédpanelt.
      5. Zárja be az üres böngésző fület, amely elindította a párbeszédpanelt, ha az nincs automatikusan lezárva, és visszatér a lapra a Azure Portal.

         Az átjárót futtató gépen a hitelesítő adatok **titkosítva** vannak a Data Factory szolgáltatás tulajdonosa által használt tanúsítvánnyal. Ha inkább a adatkezelés átjáróhoz társított tanúsítványt szeretné használni, olvassa el a hitelesítő adatok biztonságos beállítása című témakört.    
   3. A SQL Server társított szolgáltatás üzembe helyezéséhez kattintson a parancssáv **Deploy (üzembe helyezés** ) elemére. A társított szolgáltatásnak a fanézetben kell megjelennie.

      ![SQL Server társított szolgáltatás a fanézetben](./media/data-factory-move-data-between-onprem-and-cloud/sql-linked-service-in-tree-view.png)    

#### <a name="add-a-linked-service-for-an-azure-storage-account"></a>Társított szolgáltatás hozzáadása Azure Storage-fiókhoz
1. A **Data Factory szerkesztőben** kattintson a parancssáv **új adattár** elemére, majd az **Azure Storage** elemre.
2. Adja meg az Azure Storage-fiók nevét a **fiók nevéhez**.
3. Adja meg az Azure Storage-fiók kulcsát a **fiók kulcsaként**.
4. A **AzureStorageLinkedService** telepítéséhez kattintson a **Deploy (üzembe helyezés** ) elemre.

## <a name="create-datasets"></a>Adatkészletek létrehozása
Ebben a lépésben olyan bemeneti és kimeneti adatkészleteket hoz létre, amelyek a másolási művelet be- és kimeneti adatait képezik majd (Helyszíni SQL Server-adatbázis => Azure Blob Storage). Az adatkészletek létrehozása előtt végezze el a következő lépéseket (a részletes lépések a lista után olvashatóak):

* Hozzon létre egy **emp** nevű táblát az adat-előállítóhoz társított szolgáltatásként hozzáadott SQL Server-adatbázisban, és szúrjon be a táblába néhány mintabejegyzést.
* Hozzon létre egy **adftutorial** nevű Blob-tárolót az adat-előállítóhoz társított szolgáltatásként hozzáadott Azure Blob Storage-fiókban.

### <a name="prepare-on-premises-sql-server-for-the-tutorial"></a>Helyszíni SQL Server előkészítése az oktatóanyaghoz
1. A SQL Server társított szolgáltatáshoz (**SqlServerLinkedService**) megadott adatbázisban a következő SQL-parancsfájl használatával hozza létre az **EMP** táblát az adatbázisban.

    ```SQL   
    CREATE TABLE dbo.emp
    (
        ID int IDENTITY(1,1) NOT NULL,
        FirstName varchar(50),
        LastName varchar(50),
        CONSTRAINT PK_emp PRIMARY KEY (ID)
    )
    GO
    ```
2. Szúrjon be a táblába néhány mintabejegyzést:

    ```SQL
    INSERT INTO emp VALUES ('John', 'Doe')
    INSERT INTO emp VALUES ('Jane', 'Doe')
    ```

### <a name="create-input-dataset"></a>Bemeneti adatkészlet létrehozása

1. A **Data Factory-szerkesztőben** kattintson a **... elemre. Továbbiak**, kattintson az **új adatkészlet** elemre a parancssorban, majd kattintson **SQL Server tábla** elemre.
2. Cserélje le a JSON-t a jobb oldali ablaktáblán a következő szövegre:

    ```JSON   
    {        
        "name": "EmpOnPremSQLTable",
        "properties": {
            "type": "SqlServerTable",
            "linkedServiceName": "SqlServerLinkedService",
            "typeProperties": {
                "tableName": "emp"
            },
            "external": true,
            "availability": {
                "frequency": "Hour",
                "interval": 1
            },
            "policy": {
                "externalData": {
                    "retryInterval": "00:01:00",
                    "retryTimeout": "00:10:00",
                    "maximumRetry": 3
                }
            }
        }
    }     
    ```       
   Vegye figyelembe a következő szempontokat:

   * a **típus** értéke **SqlServerTable**.
   * a **Táblanév** az **EMP** értékre van állítva.
   * a **linkedServiceName** értéke **SqlServerLinkedService** (ezt a társított szolgáltatást korábban a bemutatóban hozta létre).
   * Olyan bemeneti adatkészlethez, amelyet a Azure Data Factory nem egy másik folyamat generál, a **külsőt** **igaz** értékre kell állítani. Azt jelzi, hogy a bemeneti adatokat a rendszer a Azure Data Factory szolgáltatáson kívülről állítja elő. Opcionálisan megadhat bármilyen külső adatházirendet **a szabályzat szakaszban található** **externalData** elem használatával.    

   A JSON-tulajdonságokkal kapcsolatos részletekért tekintse [meg az adatok áthelyezése a SQL Serverba vagy az adatokat](data-factory-sqlserver-connector.md)
3. Az adatkészlet üzembe helyezéséhez kattintson a parancssáv **Deploy (üzembe helyezés** ) elemére.  

### <a name="create-output-dataset"></a>Kimeneti adatkészlet létrehozása

1. A **Data Factory szerkesztőben** kattintson a parancssáv **új adatkészlet** elemére, majd az **Azure Blob Storage** elemre.
2. Cserélje le a JSON-t a jobb oldali ablaktáblán a következő szövegre:

    ```JSON   
    {
        "name": "OutputBlobTable",
        "properties": {
            "type": "AzureBlob",
            "linkedServiceName": "AzureStorageLinkedService",
            "typeProperties": {
                "folderPath": "adftutorial/outfromonpremdf",
                "format": {
                    "type": "TextFormat",
                    "columnDelimiter": ","
                }
            },
            "availability": {
                "frequency": "Hour",
                "interval": 1
            }
        }
     }
    ```   
   Vegye figyelembe a következő szempontokat:

   * a **típus** értéke **AzureBlob**.
   * a **linkedServiceName** értéke **AzureStorageLinkedService** (ezt a társított szolgáltatást a 2. lépésben hozta létre).
   * a **folderPath** a **adftutorial/outfromonpremdf** értékre van állítva, ahol a outfromonpremdf a adftutorial tárolóban található mappa. Ha még nem létezik, hozza létre az **adftutorial** tárolót.
   * Az **availability** (rendelkezésre állás) paraméter **hourly** (óránként) értékre van állítva (a **frequency** (gyakoriság) paraméter **hour** (óra), az **interval** (időköz) paraméter pedig **1** értékre).  A Data Factory szolgáltatás minden órában létrehoz egy kimeneti adatszeletet az Azure SQL Database **EMP** táblájában.

   Ha nem adja meg a **filename** paramétert a **kimeneti táblához**, a **folderPath** létrehozott fájlok a következő formátumban lesznek elnevezve: `Data.<Guid>.txt` (például: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt.).

   Ha a **folderPath** és a **fileName** tulajdonságokat dinamikusan szeretné beállítani a **SliceStart** változó időpontja alapján, használja a partitionedBy tulajdonságot. A következő példában a folderPath tulajdonság a SliceStart (a feldolgozás alatt álló szelet kezdő időpontja) változó Év, Hónap és Nap értékeit, a fileName tulajdonság pedig a SliceStart változó Óra értékét használja. Ha például a szelet előállítása a 2014-10-20T08:00:00 időpontban kezdődik, a folderName tulajdonság beállítása wikidatagateway/wikisampledataout/2014/10/20, a fileName beállítása pedig 08.csv lesz.

    ```JSON
    "folderPath": "wikidatagateway/wikisampledataout/{Year}/{Month}/{Day}",
    "fileName": "{Hour}.csv",
    "partitionedBy":
    [

        { "name": "Year", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyy" } },
        { "name": "Month", "value": { "type": "DateTime", "date": "SliceStart", "format": "MM" } },
        { "name": "Day", "value": { "type": "DateTime", "date": "SliceStart", "format": "dd" } },
        { "name": "Hour", "value": { "type": "DateTime", "date": "SliceStart", "format": "hh" } }
    ],
    ```

    A JSON-tulajdonságokkal kapcsolatos részletekért lásd: [adatok áthelyezése az Azure-ba/-ból blob Storage](data-factory-azure-blob-connector.md) .
3. Az adatkészlet üzembe helyezéséhez kattintson a parancssáv **Deploy (üzembe helyezés** ) elemére. Győződjön meg arról, hogy az adatkészletek a fanézetben is megjelennek.  

## <a name="create-pipeline"></a>Folyamat létrehozása
Ebben a lépésben egy **másolási tevékenységgel** rendelkező folyamatot hoz létre, **amely a** **EmpOnPremSQLTable** -t használja bemenetként és **OutputBlobTableként** a kimenetként.

1. Data Factory szerkesztőben kattintson a **... elemre. Továbbiak**, majd az **új folyamat** elemre.
2. Cserélje le a JSON-t a jobb oldali ablaktáblán a következő szövegre:    

    ```JSON   
     {
         "name": "ADFTutorialPipelineOnPrem",
         "properties": {
         "description": "This pipeline has one Copy activity that copies data from an on premises SQL to Azure blob",
         "activities": [
           {
             "name": "CopyFromSQLtoBlob",
             "description": "Copy data from on premises SQL server to blob",
             "type": "Copy",
             "inputs": [
               {
                 "name": "EmpOnPremSQLTable"
               }
             ],
             "outputs": [
               {
                 "name": "OutputBlobTable"
               }
             ],
             "typeProperties": {
               "source": {
                 "type": "SqlSource",
                 "sqlReaderQuery": "select * from emp"
               },
               "sink": {
                 "type": "BlobSink"
               }
             },
             "Policy": {
               "concurrency": 1,
               "executionPriorityOrder": "NewestFirst",
               "style": "StartOfInterval",
               "retry": 0,
               "timeout": "01:00:00"
             }
           }
         ],
         "start": "2016-07-05T00:00:00Z",
         "end": "2016-07-06T00:00:00Z",
         "isPaused": false
       }
     }
    ```   
   > [!IMPORTANT]
   > A **start** (kezdés) tulajdonság értékét cserélje az aktuális, az **end** (befejezés) tulajdonság értékét pedig a következő napra.
   >
   >

   Vegye figyelembe a következő szempontokat:

   * A tevékenységek szakaszban csak olyan tevékenység van, amelynek **típusa** **másolásra** van beállítva.
   * A tevékenység **bemenete** **EmpOnPremSQLTable** értékre van állítva, és a tevékenység **kimenete** **OutputBlobTable** értékre van állítva.
   * A **typeProperties** szakaszban a **SqlSource** van megadva, mert a **forrás típusa** és a **BlobSink** a fogadó **típusaként** van megadva.
   * `select * from emp`A **SqlSource** **sqlReaderQuery** tulajdonságához SQL-lekérdezés van megadva.

   Mind a kezdő, mind a befejező dátum-időpont értéket [ISO formátumban](https://en.wikipedia.org/wiki/ISO_8601) kell megadni. Például: 2014-10-14T16:32:41Z. Az **end** (befejező) időpont megadása opcionális, a jelen oktatóanyagban azonban azt is használjuk.

   Ha nem ad meg értéket a **Befejezés** tulajdonsághoz, a rendszer a következőt számítja ki: "**Start + 48 óra**". A folyamat határozatlan ideig történő futtatásához adja meg a **9/9/9999** értéket az **end** (befejezés) tulajdonsághoz.

   Meghatározza azt az időtartamot, amelyben az adatszeletek feldolgozása az egyes Azure Data Factory adatkészletekhez megadott **rendelkezésre állási** tulajdonságok alapján történik.

   A fenti példában 24 adatszelet jön létre, mivel a nap minden órájában készül egy adatszelet.        
3. Az adatkészlet üzembe helyezéséhez kattintson a parancssáv **üzembe helyezés** gombjára (a tábla egy téglalap alakú adatkészlet). Ellenőrizze, hogy a folyamat megjelenik-e a fanézetben a **folyamatok** csomópont alatt.  
4. Most kattintson kétszer az **X** gombra a lap bezárásához, hogy visszalépjen a **ADFTutorialOnPremDF** **Data Factory** lapjára.

**Gratulálunk!** Sikeresen létrehozott egy Azure-beli adat-előállítót, társított szolgáltatásokat, adatkészleteket és egy folyamatot, és ütemezte a folyamatot.

#### <a name="view-the-data-factory-in-a-diagram-view"></a>A data factory megtekintése diagramnézetben
1. A **Azure Portal** kattintson a **diagram** csempére a **ADFTutorialOnPremDF** -adatelőállító kezdőlapján. :

    ![Diagram hivatkozása](./media/data-factory-move-data-between-onprem-and-cloud/OnPremDiagramLink.png)
2. Az alábbi képhez hasonló diagramnak kell megjelennie:

    ![Diagramnézet](./media/data-factory-move-data-between-onprem-and-cloud/OnPremDiagramView.png)

    Nagyíthatja, nagyíthatja, nagyíthatja a 100%-ot, nagyíthatja a méretet, automatikusan áthelyezheti a folyamatokat és az adatkészleteket, és megjelenítheti a leszármazási adatokat (kiemeli a kijelölt elemek felsőbb rétegbeli és alárendelt elemeit).  A tulajdonságok megtekintéséhez kattintson duplán egy objektumra (bemeneti/kimeneti adatkészlet vagy folyamat).

## <a name="monitor-pipeline"></a>Folyamat figyelése
Ebben a lépésben a Azure Portal használatával figyeli az Azure-beli adatgyárban zajló teendőket. PowerShell-parancsmagokat is használhat az adatkészletek és folyamatok figyeléséhez. A figyeléssel kapcsolatos részletekért lásd: [folyamatok figyelése és kezelése](data-factory-monitor-manage-pipelines.md).

1. A diagramon kattintson duplán a **EmpOnPremSQLTable** elemre.  

    ![EmpOnPremSQLTable szeletek](./media/data-factory-move-data-between-onprem-and-cloud/OnPremSQLTableSlicesBlade.png)
2. Figyelje meg, hogy az összes adatszelet **kész** állapotban van, mert a folyamat időtartama (Kezdési idő a befejezésig) múltbeli. Emellett azért is, mert az SQL Server adatbázisban szúrta be az adathalmazt, és az egész idő alatt van. Győződjön meg arról, hogy a **probléma szeletek** szakasz alján nem jelennek meg szeletek. Az összes szelet megtekintéséhez kattintson a szeletek listájának alján **található továbbiak** elemre.
3. Most az **adatkészletek** lapon kattintson a **OutputBlobTable** elemre.

    ![OputputBlobTable szeletek](./media/data-factory-move-data-between-onprem-and-cloud/OutputBlobTableSlicesBlade.png)
4. Kattintson a listából bármelyik adatszeletre, és megjelenik az **adatszelet** oldal. Megjelenik a szelethez tartozó tevékenységek futtatása. Általában csak egy tevékenység futtatása látható.  

    ![Adatszelet panel](./media/data-factory-move-data-between-onprem-and-cloud/DataSlice.png)

    Ha a szelet nem **Ready** (Kész) állapotú, az **Upstream slices that are not ready** (Nem kész állapotú fölérendelt szeletek) listában láthatja azokat a nem kész állapotú fölérendelt szeleteket, amelyek blokkolják az aktuális szelet végrehajtását.
5. A **tevékenység futtatási részleteinek** megtekintéséhez kattintson a lenti listából a tevékenység **futtatása** elemre.

   ![Tevékenység futtatásának részletei lap](./media/data-factory-move-data-between-onprem-and-cloud/ActivityRunDetailsBlade.png)

   Olyan információkat láthat, mint például az átviteli sebesség, az időtartam és az adatok átviteléhez használt átjáró.
6. Kattintson az **X** gombra az összes oldal bezárásához, amíg meg nem történik
7. vissza a **ADFTutorialOnPremDF** kezdőlapjára.
8. választható Kattintson a **folyamatok** elemre, kattintson a **ADFTutorialOnPremDF** elemre, és válassza a bemeneti táblák (**felhasznált**) vagy a kimeneti adatkészletek (**előállított**) szerinti részletezés lehetőséget.
9. A [Microsoft Storage Explorer](https://storageexplorer.com/) eszközzel ellenőrizheti, hogy minden órában létrejön-e blob/fájl.

   ![Azure Storage Explorer](./media/data-factory-move-data-between-onprem-and-cloud/OnPremAzureStorageExplorer.png)

## <a name="next-steps"></a>Következő lépések
* Az adatkezelés-átjáróval kapcsolatos további információkért tekintse meg a [adatkezelés átjáróval](data-factory-data-management-gateway.md) foglalkozó cikket.
* Lásd: [adatok másolása az Azure blobból az Azure SQL](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) -be, hogy megtudja, hogyan használható a másolási tevékenység az adatok forrás adattárból egy fogadó adattárba való áthelyezéséhez.
