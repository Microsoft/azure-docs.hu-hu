---
title: Azure Data Factory összekötők hibáinak megoldása
description: Megtudhatja, hogyan lehet elhárítani a Azure Data Factory összekötői problémáit.
services: data-factory
author: linda33wj
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 02/08/2021
ms.author: jingwang
ms.reviewer: craigg
ms.custom: has-adal-ref
ms.openlocfilehash: 2395e8e0027755357e65aab247185c02f7b1723d
ms.sourcegitcommit: 706e7d3eaa27f242312d3d8e3ff072d2ae685956
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/09/2021
ms.locfileid: "99980711"
---
# <a name="troubleshoot-azure-data-factory-connectors"></a>Azure Data Factory összekötők hibáinak megoldása

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Ez a cikk az Azure Data Factory-összekötővel kapcsolatos problémák elhárításának gyakori módszereit ismerteti.

## <a name="azure-blob-storage"></a>Azure Blob Storage

### <a name="error-code-azurebloboperationfailed"></a>Hibakód: AzureBlobOperationFailed

- **Üzenet**: a blob művelete sikertelen volt. ContainerName:% containerName;, elérési út:% Path;. "

- **OK**: a blob Storage művelettel kapcsolatos probléma.

- **Javaslat**: a hiba részleteinek megtekintéséhez tekintse meg a [blob Storage hibakódokat](https://docs.microsoft.com/rest/api/storageservices/blob-service-error-codes). További segítségért forduljon a Blob Storage csapatához.


### <a name="invalid-property-during-copy-activity"></a>Érvénytelen tulajdonság a másolási tevékenység során

- **Üzenet**: `Copy activity \<Activity Name> has an invalid "source" property. The source type is not compatible with the dataset \<Dataset Name> and its linked service \<Linked Service Name>. Please verify your input against.`

- **OK**: az adatkészletben definiált típus nem konzisztens a másolási tevékenységben definiált forrás-vagy fogadó típussal.

- **Megoldás**: szerkessze az adatkészlet vagy a folyamat JSON-definícióját, hogy a típusok konzisztensek legyenek, majd futtassa újra a telepítést.


## <a name="azure-cosmos-db"></a>Azure Cosmos DB

### <a name="error-message-request-size-is-too-large"></a>Hibaüzenet: a kérelem mérete túl nagy

- **Tünetek**: ha a Azure Cosmos DBba másol egy alapértelmezett írási batch-méretet, a következő hibaüzenet jelenik meg: `Request size is too large.`

- **OK**: Azure Cosmos db korlátozza az egyetlen kérelem méretét 2 MB-ra. A képlet a *kérelem mérete = egy dokumentum mérete \* írási köteg mérete*. Ha a dokumentum mérete nagy, az alapértelmezett viselkedés túl nagy méretű kérést eredményez. Beállíthatja az írási köteg méretét.

- **Megoldás**: a másolási tevékenység fogadójában csökkentse az *írási köteg méretének* értékét (az alapértelmezett érték 10000).

### <a name="error-message-unique-index-constraint-violation"></a>Hibaüzenet: egyedi index megkötésének megsértése

- **Tünetek**: amikor az adatmásolás Azure Cosmos DBba kerül, a következő hibaüzenet jelenik meg:

    `Message=Partition range id 0 | Failed to import mini-batch. 
    Exception was Message: {"Errors":["Encountered exception while executing function. Exception = Error: {\"Errors\":[\"Unique index constraint violation.\"]}...`

- **OK**: két lehetséges oka van:

    - 1. ok: Ha írási viselkedésként használja a **beszúrást** , ez a hiba azt jelenti, hogy a forrásadatok sorokkal vagy objektumokkal rendelkeznek UGYANAZZAL az azonosítóval.
    - 2. ok: Ha a **Upsert** -et írási viselkedésként használja, és egy másik egyedi kulcsot állít be a tárolóhoz, akkor ez a hiba azt jelenti, hogy a forrásadatok sorait vagy objektumait eltérő azonosítókkal, de a definiált egyedi kulcshoz tartozó értékkel kell megadnia.

- **Megoldás**: 

    - Az 1. ok esetében a **Upsert** beállítása írási viselkedésként.
    - A 2. ok esetén győződjön meg arról, hogy minden dokumentum eltérő értékkel rendelkezik a definiált egyedi kulcshoz.

### <a name="error-message-request-rate-is-large"></a>Hibaüzenet: a kérelmek aránya nagy

- **Tünetek**: amikor az adatmásolás Azure Cosmos DBba kerül, a következő hibaüzenet jelenik meg:

    `Type=Microsoft.Azure.Documents.DocumentClientException,
    Message=Message: {"Errors":["Request rate is large"]}`

- **OK**: a használt kérelmek száma (RUS) nagyobb, mint a Azure Cosmos DBban konfigurált elérhető RUS. Annak megismeréséhez, hogy Azure Cosmos DB hogyan számítja ki az RUs-t, tekintse meg [a kérelmek egységei Azure Cosmos db](../cosmos-db/request-units.md#request-unit-considerations).

- **Megoldás**: próbálja ki a következő két megoldás valamelyikét:

    - Növelje a *Container RUs* -számot nagyobb értékre a Azure Cosmos DBban. Ez a megoldás javítja a másolási tevékenység teljesítményét, de a Azure Cosmos DB további költségeket von maga után. 
    - Csökkentse a *writeBatchSize* kisebb értékre (például 1000), és csökkentse a *parallelCopies* kisebb értékre (például 1). Ezzel a megoldással csökkenthető a másolási teljesítmény, de a Azure Cosmos DB nem jár további költségekkel.

### <a name="columns-missing-in-column-mapping"></a>Oszlop-hozzárendelésből hiányzó oszlopok

- **Tünetek**: Ha az oszlop-hozzárendeléshez Azure Cosmos db sémát importál, egyes oszlopok hiányoznak. 

- **OK**: Data Factory kikövetkezteti a sémát az első 10 Azure Cosmos db dokumentumból. Ha egyes dokumentumok oszlopai vagy tulajdonságai nem tartalmaznak értékeket, a rendszer nem észleli Data Factory a sémát, ezért nem jelenik meg.

- **Megoldás**: a lekérdezés a következő kódban látható módon beállítható úgy, hogy az eredményhalmaz üres értékekkel jelenjen meg az oszlop értékeiben. Tegyük fel, hogy a *lehetetlen* oszlop hiányzik az első 10 dokumentumból. Azt is megteheti, hogy manuálisan hozzáadja az oszlopot a leképezéshez.

    ```sql
    select c.company, c.category, c.comments, (c.impossible??'') as impossible from c
    ```

### <a name="error-message-the-guidrepresentation-for-the-reader-is-csharplegacy"></a>Hibaüzenet: az olvasó GuidRepresentation CSharpLegacy

- **Tünetek**: amikor Azure Cosmos db MongoAPI vagy MongoDB származó adatok másolását az univerzálisan egyedi azonosító (UUID) mezővel, a következő hibaüzenet jelenik meg:

    `Failed to read data via MongoDB client., 
    Source=Microsoft.DataTransfer.Runtime.MongoDbV2Connector,Type=System.FormatException, 
    Message=The GuidRepresentation for the reader is CSharpLegacy which requires the binary sub type to be UuidLegacy not UuidStandard.,Source=MongoDB.Bson,’“,`

- **OK**: az UUID-t kétféleképpen lehet ábrázolni a bináris JSON-ban (BSON): UuidStardard és UuidLegacy. Alapértelmezés szerint a UuidLegacy az adatolvasásra szolgál. Hibaüzenet jelenik meg, ha az MongoDB-beli UUID-adatai UuidStandard.

- **Megoldás**: a MongoDB-kapcsolatok karakterláncában adja hozzá a *uuidRepresentation = standard* beállítást. További információ: MongoDB- [kapcsolatok karakterlánca](connector-mongodb.md#linked-service-properties).
            
## <a name="azure-cosmos-db-sql-api"></a>Azure Cosmos DB (SQL API)

### <a name="error-code-cosmosdbsqlapioperationfailed"></a>Hibakód: CosmosDbSqlApiOperationFailed

- **Üzenet**: `CosmosDbSqlApi operation Failed. ErrorMessage: %msg;.`

- **OK**: a CosmosDbSqlApi művelettel kapcsolatos probléma.

- **Javaslat**: a hiba részleteinek megtekintéséhez tekintse meg [Azure Cosmos db Súgó dokumentumát](../cosmos-db/troubleshoot-dot-net-sdk.md). További segítségért forduljon a Azure Cosmos DB csapatához.

## <a name="azure-data-lake-storage-gen1"></a>1. generációs Azure Data Lake Storage

### <a name="error-message-the-underlying-connection-was-closed-could-not-establish-trust-relationship-for-the-ssltls-secure-channel"></a>Hibaüzenet: az alapul szolgáló kapcsolat bezárult: nem sikerült létrehozni a megbízhatósági kapcsolatot az SSL/TLS biztonságos csatorna számára.

- **Tünetek**: a másolási tevékenység a következő hiba miatt meghiúsul: 

    `Message: ErrorCode = UserErrorFailedFileOperation, Error Message = The underlying connection was closed: Could not establish trust relationship for the SSL/TLS secure channel.`

- **OK**: a tanúsítvány ellenőrzése a TLS-kézfogás során meghiúsult.

- **Megoldás**: áthidaló megoldásként használja a szakaszos másolatot a Azure Data Lake Storage Gen1 TRANSPORT Layer Security (TLS) érvényesítésének kihagyásához. Újból létre kell hoznia ezt a problémát, és össze kell gyűjtenie a Hálózatfigyelő (netmon) nyomkövetést, majd a hálózati csapatot kell használnia a helyi hálózati konfiguráció ellenőrzéséhez.

    ![A hibaelhárítási problémák Azure Data Lake Storage Gen1i kapcsolatainak ábrája.](./media/connector-troubleshoot-guide/adls-troubleshoot.png)


### <a name="error-message-the-remote-server-returned-an-error-403-forbidden"></a>Hibaüzenet: a távoli kiszolgáló a következő hibát adta vissza: (403) tiltott

- **Tünetek**: a másolási tevékenység a következő hiba miatt meghiúsul: 

   `Message: The remote server returned an error: (403) Forbidden.   
    Response details: {"RemoteException":{"exception":"AccessControlException""message":"CREATE failed with error 0x83090aa2 (Forbidden. ACL verification failed. Either the resource does not exist or the user is not authorized to perform the requested operation.)....`

- **OK**: az egyik lehetséges ok az, hogy az Ön által használt szolgáltatásnév vagy felügyelt identitás nem rendelkezik engedéllyel bizonyos mappákhoz vagy fájlokhoz való hozzáféréshez.

- **Megoldás**: adjon meg megfelelő engedélyeket a másolandó mappákhoz és almappákhoz. További információ: [adatok másolása Azure Data Lake Storage Gen1 vagy rendszerből Azure Data Factory használatával](connector-azure-data-lake-store.md#linked-service-properties).

### <a name="error-message-failed-to-get-access-token-by-using-service-principal-adal-error-service_unavailable"></a>Hibaüzenet: nem sikerült lekérni a hozzáférési tokent az egyszerű szolgáltatásnév használatával. ADAL-hiba: service_unavailable

- **Tünetek**: a másolási tevékenység a következő hiba miatt meghiúsul:

    `Failed to get access token by using service principal.  
    ADAL Error: service_unavailable, The remote server returned an error: (503) Server Unavailable.`

- **OK**: ha a Azure Active Directory által birtokolt szolgáltatási jogkivonat-kiszolgáló (STS) nem érhető el, az azt jelenti, hogy túl elfoglalt a kérelmek kezeléséhez, és visszaadja a 503-es http-hibát. 

- **Megoldás**: több perc elteltével futtassa újra a másolási tevékenységet.


## <a name="azure-data-lake-storage-gen2"></a>2. generációs Azure Data Lake Storage

### <a name="error-code-adlsgen2operationfailed"></a>Hibakód: ADLSGen2OperationFailed

- **Üzenet**: `ADLS Gen2 operation failed for: %adlsGen2Message;.%exceptionData;.`

- **Okok és javaslatok**: ennek a hibának a különböző okai okozhatják. A lehetséges okok elemzéséhez és a kapcsolódó javaslathoz az alábbi listában tájékozódhat.

  | Elemzés oka                                               | Ajánlás                                               |
  | :----------------------------------------------------------- | :----------------------------------------------------------- |
  | Ha Azure Data Lake Storage Gen2 hibát jelez, a művelet sikertelen volt.| A Azure Data Lake Storage Gen2 által kiváltott részletes hibaüzenetek megtekintése. Ha a hiba átmeneti hiba, próbálja megismételni a műveletet. További segítségért forduljon az Azure Storage támogatási szolgálatához, és adja meg a kérelem AZONOSÍTÓját a hibaüzenetben. |
  | Ha a hibaüzenet tartalmazza a "tiltott" karakterláncot, akkor előfordulhat, hogy az Ön által használt egyszerű szolgáltatásnév vagy felügyelt identitás nem rendelkezik megfelelő engedélyekkel a Azure Data Lake Storage Gen2 eléréséhez. | A hiba megoldásához tekintse meg az [Adatmásolási és-átalakítási Azure Data Lake Storage Gen2 Azure Data Factory használatával](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage#service-principal-authentication)című témakört. |
  | Ha a hibaüzenet tartalmazza a "InternalServerError" karakterláncot, a Azure Data Lake Storage Gen2 visszaadja a hibát. | Előfordulhat, hogy a hibát egy átmeneti hiba okozta. Ha mégis, próbálja meg újra elvégezni a műveletet. Ha a probléma továbbra is fennáll, forduljon az Azure Storage támogatási szolgálatához, és adja meg a kérelem AZONOSÍTÓját a hibaüzenetből. |

### <a name="request-to-azure-data-lake-storage-gen2-account-caused-a-timeout-error"></a>A Azure Data Lake Storage Gen2 fiókra vonatkozó kérelem időtúllépési hibát okozott.

- **Üzenet**: 
  * Hibakód = `UserErrorFailedBlobFSOperation`
  * Hibaüzenet = `BlobFS operation failed for: A task was canceled.`

- **OK**: a problémát a Azure Data Lake Storage Gen2 fogadó időtúllépési hibája okozza, amely általában a saját üzemeltetésű INTEGRATION RUNTIME (IR) gépen történik.

- **Javaslat**: 

    - Ha lehetséges, helyezze a saját üzemeltetésű IR-gépet és a cél Azure Data Lake Storage Gen2 fiókot ugyanabban a régióban. Ez segít elkerülni a véletlenszerű időtúllépési hibát, és jobb teljesítményt eredményez.

    - Ellenőrizze, hogy van-e speciális hálózati beállítás (például ExpressRoute), és győződjön meg arról, hogy a hálózat rendelkezik-e elegendő sávszélességgel. Javasoljuk, hogy csökkentse a saját üzemeltetésű integrációs modul-beállítást, ha a teljes sávszélesség alacsony. Így elkerülhető, hogy a hálózati erőforrások versenye több egyidejű feladaton keresztül történjen.

    - Ha a fájlméret közepes vagy kicsi, kisebb blokk méretet használjon a nem bináris másoláshoz az időtúllépési hiba enyhítése érdekében. További információ: [blob Storage Put blokk](/rest/api/storageservices/put-block).

       Az egyéni blokk méretének megadásához szerkessze a tulajdonságot a JSON-fájlkezelőben az itt látható módon:
    
        ```
        "sink": {
            "type": "DelimitedTextSink",
            "storeSettings": {
                "type": "AzureBlobFSWriteSettings",
                "blockSizeInMB": 8
            }
        }
        ```

                  
## <a name="azure-files-storage"></a>Azure Files Storage

### <a name="error-code-azurefileoperationfailed"></a>Hibakód: AzureFileOperationFailed

- **Üzenet**: `Azure File operation Failed. Path: %path;. ErrorMessage: %msg;.`

- **OK**: a Azure Files tárolási művelettel kapcsolatos probléma.

- **Javaslat**: a hiba részleteinek megtekintéséhez tekintse meg a [Azure Files súgóját](https://docs.microsoft.com/rest/api/storageservices/file-service-error-codes). További segítségért forduljon a Azure Files csapatához.


## <a name="azure-synapse-analytics-azure-sql-database-and-sql-server"></a>Azure szinapszis analitika, Azure SQL Database és SQL Server

### <a name="error-code-sqlfailedtoconnect"></a>Hibakód: SqlFailedToConnect

- **Üzenet**: `Cannot connect to SQL Database: '%server;', Database: '%database;', User: '%user;'. Check the linked service configuration is correct, and make sure the SQL Database firewall allows the integration runtime to access.`
- **Okok és javaslatok**: ennek a hibának a különböző okai okozhatják. A lehetséges okok elemzéséhez és a kapcsolódó javaslathoz az alábbi listában tájékozódhat.

    | Elemzés oka                                               | Ajánlás                                               |
    | :----------------------------------------------------------- | :----------------------------------------------------------- |
    | Az Azure SQL esetében, ha a hibaüzenet tartalmazza a "SqlErrorNumber = 47073" karakterláncot, az azt jelenti, hogy a nyilvános hálózati hozzáférés megtagadva a kapcsolati beállításban. | Az Azure SQL tűzfalon állítsa a *nem* értékre a **nyilvános hálózati hozzáférés megtagadása** beállítást. További információ: [Azure SQL-kapcsolati beállítások](https://docs.microsoft.com/azure/azure-sql/database/connectivity-settings#deny-public-network-access). |
    | Az Azure SQL esetében, ha a hibaüzenet tartalmaz egy SQL-hibakódot (például "SqlErrorNumber = [errorcode]"), tekintse meg az Azure SQL hibaelhárítási útmutatóját. | Javaslat: [kapcsolódási problémák és egyéb hibák elhárítása Azure SQL Database és az Azure SQL felügyelt példányával](https://docs.microsoft.com/azure/azure-sql/database/troubleshoot-common-errors-issues). |
    | Ellenőrizze, hogy a 1433-es port a tűzfal engedélyezési listájában van-e. | További információ: [SQL Server által használt portok](https://docs.microsoft.com/sql/sql-server/install/configure-the-windows-firewall-to-allow-sql-server-access#ports-used-by-). |
    | Ha a hibaüzenet tartalmazza a "SqlException" karakterláncot, SQL Database a hiba azt jelzi, hogy egy adott művelet meghiúsult. | További információ: SQL-hibakód keresése az [adatbázismotor hibáiban](https://docs.microsoft.com/sql/relational-databases/errors-events/database-engine-events-and-errors). További segítségért forduljon az Azure SQL támogatási szolgálatához. |
    | Ha ez egy átmeneti probléma (például instabil hálózati kapcsolatok), az újrapróbálkozáshoz adja hozzá a tevékenység-házirendben az Újrapróbálkozás lehetőséget. | További információ: [folyamatok és tevékenységek Azure Data Factoryban](https://docs.microsoft.com/azure/data-factory/concepts-pipelines-activities#activity-policy). |
    | Ha a hibaüzenet tartalmazza az "ügyfél IP-címmel"... karakterláncot. a kiszolgáló nem érhető el, és a Azure SQL Databasehoz próbál csatlakozni, a hibát általában egy Azure SQL Database tűzfallal kapcsolatos probléma okozza. | Az Azure SQL Server tűzfal konfigurációjában engedélyezze az **Azure-szolgáltatások és-erőforrások elérésének engedélyezése ehhez a kiszolgálóhoz** lehetőséget. További információ: [Azure SQL Database és az Azure szinapszis IP-tűzfalszabályok](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure). |
    
### <a name="error-code-sqloperationfailed"></a>Hibakód: SqlOperationFailed

- **Üzenet**: `A database operation failed. Please search error to get more details.`

- **Okok és javaslatok**: ennek a hibának a különböző okai okozhatják. A lehetséges okok elemzéséhez és a kapcsolódó javaslathoz az alábbi listában tájékozódhat.

    | Elemzés oka                                               | Ajánlás                                               |
    | :----------------------------------------------------------- | :----------------------------------------------------------- |
    | Ha a hibaüzenet tartalmazza a "SqlException" karakterláncot, SQL Database hibát jelez, ami azt jelzi, hogy egy bizonyos művelet meghiúsult. | Ha az SQL-hiba nem egyértelmű, próbálja meg módosítani az adatbázist a legújabb kompatibilitási szintre (150). Ez a verzió a legújabb SQL-hibákat dobja el. További információért lásd a [dokumentációt](/sql/t-sql/statements/alter-database-transact-sql-compatibility-level#backwardCompat). <br/> További információ az SQL-hibák elhárításáról: SQL-hibakód keresése az [adatbázismotor hibáiban](https://docs.microsoft.com/sql/relational-databases/errors-events/database-engine-events-and-errors). További segítségért forduljon az Azure SQL támogatási szolgálatához. |
    | Ha a hibaüzenet tartalmazza a "PdwManagedToNativeInteropException" karakterláncot, azt általában a forrás-és a fogadó oszlop méretének eltérése okozza. | A forrás és a fogadó oszlop méretének megkeresése. További segítségért forduljon az Azure SQL támogatási szolgálatához. |
    | Ha a hibaüzenet tartalmazza a "InvalidOperationException" karakterláncot, azt általában érvénytelen bemeneti adatok okozzák. | Annak azonosításához, hogy melyik sor észlelte a problémát, engedélyezze a hibatűrési funkciót a másolási tevékenységnél, amely további vizsgálat céljából átirányíthatja a problémás sorokat a tárolóba. További információ: a [másolási tevékenység hibatűrése Azure Data Factoryban](https://docs.microsoft.com/azure/data-factory/copy-activity-fault-tolerance). |


### <a name="error-code-sqlunauthorizedaccess"></a>Hibakód: SqlUnauthorizedAccess

- **Üzenet**: `Cannot connect to '%connectorName;'. Detail Message: '%message;'`

- **OK**: a hitelesítő adatok helytelenek, vagy a bejelentkezési fiók nem fér hozzá az SQL-adatbázishoz.

- **Javaslat**: Ellenőrizze, hogy a bejelentkezési fiók rendelkezik-e megfelelő engedélyekkel az SQL-adatbázis eléréséhez.


### <a name="error-code-sqlopenconnectiontimeout"></a>Hibakód: SqlOpenConnectionTimeout

- **Üzenet**: `Open connection to database timeout after '%timeoutValue;' seconds.`

- **OK**: a probléma lehet az SQL Database átmeneti meghibásodása.

- **Javaslat**: Próbálja megismételni a műveletet a társított szolgáltatás kapcsolati karakterláncának nagyobb kapcsolati időtúllépési értékkel való frissítéséhez.


### <a name="error-code-sqlautocreatetabletypemapfailed"></a>Hibakód: SqlAutoCreateTableTypeMapFailed

- **Üzenet**: `Type '%dataType;' in source side cannot be mapped to a type that supported by sink side(column name:'%columnName;') in autocreate table.`

- **OK**: az autolétrehozási tábla nem tudja kielégíteni a forrás követelményét.

- **Javaslat**: frissítse az oszlop típusát a *leképezésekben*, vagy manuálisan hozza létre a fogadó táblát a célkiszolgálón.


### <a name="error-code-sqldatatypenotsupported"></a>Hibakód: SqlDataTypeNotSupported

- **Üzenet**: `A database operation failed. Check the SQL errors.`

- **OK**: Ha a probléma az SQL-forrásban fordul elő, és a hiba a SqlDateTime túlcsorduláshoz kapcsolódik, az adatérték meghaladja a logikai típus tartományát (1/1/1753 12:00:00 – 12/31/9999 11:59:59 PM).

- **Javaslat**: a típust a forrás SQL-lekérdezésben szereplő karakterláncba írja, vagy a másolási tevékenység oszlop leképezésében módosítsa az oszlop típusát *karakterláncra*.

- **OK**: Ha a probléma az SQL-fogadóban történik, és a hiba a SqlDateTime túlcsorduláshoz kapcsolódik, az adatérték meghaladja a fogadó tábla engedélyezett tartományát.

- **Javaslat**: frissítse a kapcsolódó oszlop típusát a fogadó táblában lévő *datetime2* típusra.


### <a name="error-code-sqlinvaliddbstoredprocedure"></a>Hibakód: SqlInvalidDbStoredProcedure

- **Üzenet**: `The specified Stored Procedure is not valid. It could be caused by that the stored procedure doesn't return any data. Invalid Stored Procedure script: '%scriptName;'.`

- **OK**: a megadott tárolt eljárás érvénytelen. Ennek az lehet az oka, hogy a tárolt eljárás nem ad vissza semmilyen adatértéket.

- **Javaslat**: Ellenőrizze a tárolt eljárást az SQL-eszközök használatával. Győződjön meg arról, hogy a tárolt eljárás képes visszaadni az adatvesztést.


### <a name="error-code-sqlinvaliddbquerystring"></a>Hibakód: SqlInvalidDbQueryString

- **Üzenet**: `The specified SQL Query is not valid. It could be caused by that the query doesn't return any data. Invalid query: '%query;'`

- **OK**: a megadott SQL-lekérdezés érvénytelen. Ennek az lehet az oka, hogy a lekérdezés nem ad vissza semmilyen adatértéket.

- **Javaslat**: az SQL-lekérdezés ÉRVÉNYESÍTÉSe SQL-eszközök használatával. Győződjön meg arról, hogy a lekérdezés képes visszaadni az adatkérést.


### <a name="error-code-sqlinvalidcolumnname"></a>Hibakód: SqlInvalidColumnName

- **Üzenet**: `Column '%column;' does not exist in the table '%tableName;', ServerName: '%serverName;', DatabaseName: '%dbName;'.`

- **OK**: az oszlop nem található, mert a konfiguráció helytelen lehet.

- **Javaslat**: Ellenőrizze a lekérdezésben szereplő oszlopot, a *struktúrát* az adatkészletben, valamint a tevékenység *leképezéseit* .


### <a name="error-code-sqlbatchwritetimeout"></a>Hibakód: SqlBatchWriteTimeout

- **Üzenet**: `Timeouts in SQL write operation.`

- **OK**: a problémát egy SQL-adatbázis átmeneti hibája okozhatja.

- **Javaslat**: Próbálja megismételni a műveletet. Ha a probléma továbbra is fennáll, forduljon az Azure SQL támogatási szolgálatához.


### <a name="error-code-sqlbatchwritetransactionfailed"></a>Hibakód: SqlBatchWriteTransactionFailed

- **Üzenet**: `SQL transaction commits failed.`

- **OK**: Ha a kivétel részletei folyamatosan jelzik a tranzakció időtúllépését, az integrációs modul és az adatbázis közötti hálózati késés nagyobb, mint az alapértelmezett 30 másodperces küszöbérték.

- **Javaslat**: frissítse az SQL-társított szolgáltatás kapcsolati karakterláncát egy olyan *kapcsolati időtúllépési* értékkel, amely egyenlő vagy nagyobb, mint 120, és futtassa újra a tevékenységet.

- **OK**: Ha a kivétel részletei időnként azt jelzik, hogy az SQL-kapcsolat megszakadt, lehet, hogy az átmeneti hálózati hiba vagy egy SQL Database-oldal probléma.

- **Javaslat**: Próbálja megismételni a tevékenységet, és tekintse át az SQL Database-oldal metrikáit.


### <a name="error-code-sqlbulkcopyinvalidcolumnlength"></a>Hibakód: SqlBulkCopyInvalidColumnLength

- **Üzenet**: `SQL Bulk Copy failed due to receive an invalid column length from the bcp client.`

- **OK**: az SQL tömeges másolása nem sikerült, mert érvénytelen oszlopot kapott a tömeges másolási program segédprogram (BCP) ügyfelétől.

- **Javaslat**: a problémát észlelő sor azonosításához engedélyezze a hibatűrés funkciót a másolási tevékenységnél. Ez további vizsgálat céljából átirányíthatja a problémás sorokat a tárolóba. További információ: a [másolási tevékenység hibatűrése Azure Data Factoryban](https://docs.microsoft.com/azure/data-factory/copy-activity-fault-tolerance).


### <a name="error-code-sqlconnectionisclosed"></a>Hibakód: SqlConnectionIsClosed

- **Üzenet**: `The connection is closed by SQL Database.`

- **OK**: az SQL-kiszolgáló lezárta az SQL-adatbázist, ha egy magas egyidejű Futtatás és a kiszolgáló leállítja a kapcsolódást.

- **Javaslat**: Próbálja megismételni a kapcsolatokat. Ha a probléma továbbra is fennáll, forduljon az Azure SQL támogatási szolgálatához.


### <a name="error-message-conversion-failed-when-converting-from-a-character-string-to-uniqueidentifier"></a>Hibaüzenet: nem sikerült az átalakítás a karakterláncról uniqueidentifier való átalakításkor

- **Tünetek**: Ha táblázatos adatforrásból (például SQL Server) másol adatokat az Azure szinapszis analyticsbe szakaszos másolással és a (z)-alapú használatával, a következő hibaüzenet jelenik meg:

   `ErrorCode=FailedDbOperation,Type=Microsoft.DataTransfer.Common.Shared.HybridDeliveryException, 
    Message=Error happened when loading data into Azure Synapse Analytics., 
    Source=Microsoft.DataTransfer.ClientLibrary,Type=System.Data.SqlClient.SqlException, 
    Message=Conversion failed when converting from a character string to uniqueidentifier...`

- **OK**: az Azure szinapszis Analytics-alapú albase nem tud üres karakterláncot konvertálni egy GUID azonosítóra.

- **Megoldás**: a másolási tevékenység fogadójában, a Alapszintű beállítások területen állítsa a **típus alapértelmezett** beállítását *false (hamis*) értékre.


### <a name="error-message-expected-data-type-decimalxx-offending-value"></a>Hibaüzenet: várt adattípus: decimális (x, x), jogsértő érték

- **Tünetek**: Ha táblázatos adatforrásból (például SQL Server) másol adatokat az Azure szinapszis analyticsbe a szakaszos másolás és a Kiindulás használatával, a következő hibaüzenet jelenik meg:

    `ErrorCode=FailedDbOperation,Type=Microsoft.DataTransfer.Common.Shared.HybridDeliveryException, 
    Message=Error happened when loading data into Azure Synapse Analytics., 
    Source=Microsoft.DataTransfer.ClientLibrary,Type=System.Data.SqlClient.SqlException, 
    Message=Query aborted-- the maximum reject threshold (0 rows) was reached while reading from an external source: 1 rows rejected out of total 415 rows processed. (/file_name.txt)  
    Column ordinal: 18, Expected data type: DECIMAL(x,x), Offending value:..`

- **OK**: az Azure szinapszis Analytics-alapú albase nem tud üres karakterláncot (null értéket) beszúrni egy decimális oszlopba.

- **Megoldás**: a másolási tevékenység fogadójában, a Alapszintű beállítások területen állítsa a **típus alapértelmezett** beállítását false (hamis) értékre.


### <a name="error-message-java-exception-message-hdfsbridgecreaterecordreader"></a>Hibaüzenet: Java-kivételt jelző üzenet: HdfsBridge:: CreateRecordReader

- **Tünetek**: az adatok az Azure szinapszis analyticsbe való másolása a Base használatával, és a következő hibaüzenetet kapja:

    `Message=110802;An internal DMS error occurred that caused this operation to fail.  
    Details: Exception: Microsoft.SqlServer.DataWarehouse.DataMovement.Common.ExternalAccess.HdfsAccessException, 
    Message: Java exception raised on call to HdfsBridge_CreateRecordReader.  
    Java exception message:HdfsBridge::CreateRecordReader - Unexpected error encountered creating the record reader.: Error [HdfsBridge::CreateRecordReader - Unexpected error encountered creating the record reader.] occurred while accessing external file.....`

- **OK**: lehetséges, hogy a séma (az oszlopszélesség teljes szélessége) túl nagy (1 MB-nál nagyobb). Az összes oszlop méretének hozzáadásával keresse meg a cél Azure szinapszis Analytics-táblázat sémáját:

    - Int = 4 bájt
    - Bigint = 8 bájt
    - Varchar (n), char (n), Binary (n), varbinary (n) = n bájt
    - Nvarchar (n), nchar (n) = n * 2 bájt
    - Dátum = 6 bájt
    - Datetime/(2), idő adattípusúra = 16 bájt
    - DateTimeOffset = 20 bájt
    - Decimális = 19 bájt
    - Float = 8 bájt
    - Pénz = 8 bájt
    - Túlcsordulási = 4 bájt
    - Valós = 4 bájt
    - Smallint = 2 bájt
    - Idő = 12 bájt
    - Tinyint = 1 bájt

- **Megoldás**: 
    - Csökkentse az oszlopszélességet 1 MB-nál kisebb értékre.
    - Vagy használjon tömeges beszúrási módszert a "Base" letiltásával.


### <a name="error-message-the-condition-specified-using-http-conditional-headers-is-not-met"></a>Hibaüzenet: a HTTP feltételes fejléc (ek) használatával megadott feltétel nem teljesül.

- **Tünetek**: SQL-lekérdezést használ az adatok Azure szinapszis Analyticsből való lekéréséhez, és a következő hibaüzenetet kapja:

    `...StorageException: The condition specified using HTTP conditional header(s) is not met...`

- **OK**: az Azure szinapszis Analytics hibát észlelt a külső tábla Azure Storage-beli lekérdezése során.

- **Megoldás**: futtassa ugyanazt a lekérdezést SQL Server Management Studioban (SSMS), és ellenőrizze, hogy ugyanaz az eredmény jelenik-e meg. Ha ezt teszi, nyisson meg egy támogatási jegyet az Azure szinapszis Analytics szolgáltatáshoz, és adja meg az Azure szinapszis Analytics-kiszolgálót és az adatbázis nevét.


### <a name="performance-tier-is-low-and-leads-to-copy-failure"></a>A teljesítményszint alacsony, és sikertelen másolást eredményez

- **Tünetek**: az Adatmásolás a Azure SQL Databaseba, és a következő hibaüzenetet kapja: `Database operation failed. Error message from database execution : ExecuteNonQuery requires an open and available Connection. The connection's current state is closed.`

- **OK**: a Azure SQL Database S1 elérte a bemeneti/kimeneti (I/O) korlátokat.

- **Megoldás**: frissítse a Azure SQL Database teljesítményszint a probléma megoldásához. 


### <a name="sql-table-cant-be-found"></a>Nem található az SQL-tábla 

- **Tünetek**: a hibrid adatok egy helyszíni SQL Server táblába másolhatók, és a következő hibaüzenetet kapják:`Cannot find the object "dbo.Contoso" because it does not exist or you do not have permissions.`

- **OK**: az aktuális SQL-fiók nem rendelkezik megfelelő engedélyekkel a .net SqlBulkCopy. WriteToServer által kiadott kérelmek végrehajtásához.

- **Megoldás**: váltson magasabb jogosultsági szintű SQL-fiókra.


### <a name="error-message-string-or-binary-data-is-truncated"></a>Hibaüzenet: a karakterlánc vagy a bináris fájl csonkítva jelenik meg

- **Tünetek**: hiba történik, amikor egy helyszíni Azure SQL Server-táblába másol egy Adatmásolást. 

- **OK**: a CX SQL-tábla sémájának definíciója egy vagy több olyan oszlopot tartalmaz, amelynek a vártnál kevesebb a hossza.

- **Megoldás**: a probléma megoldásához próbálja meg a következőket:

    1. A problémát okozó sorok hibaelhárításához alkalmazza az SQL-fogadó [hibatűrését](./copy-activity-fault-tolerance.md), különösen a "redirectIncompatibleRowSettings".

        > [!NOTE]
        > A hibatűréshez további végrehajtási időre lehet szükség, ami magasabb költségekhez vezethet.

    2. Ellenőrizze, hogy az átirányított információk az SQL Table-séma oszlopának hosszán legyenek-e láthatók, hogy mely oszlopokat kell frissíteni.

    3. A tábla sémájának frissítése ennek megfelelően.


## <a name="azure-table-storage"></a>Azure Table Storage

### <a name="error-code-azuretableduplicatecolumnsfromsource"></a>Hibakód: AzureTableDuplicateColumnsFromSource

- **Üzenet**: `Duplicate columns with same name '%name;' are detected from source. This is NOT supported by Azure Table Storage sink.`

- **OK**: a duplikált forrás oszlopai a következő okok egyike miatt fordulhatnak elő:
   * Az adatbázist forrásként használja, és az alkalmazott táblához csatlakozik.
   * A fejlécsorban duplikált oszlopnevek szerepelnek a strukturálatlan CSV-fájlokban.

- **Javaslat**: szükség szerint ellenőrizze és javítsa ki a forrás oszlopokat.


## <a name="db2"></a>DB2

### <a name="error-code-db2driverrunfailed"></a>Hibakód: DB2DriverRunFailed

- **Üzenet**: `Error thrown from driver. Sql code: '%code;'`

- **OK**: Ha a hibaüzenet tartalmazza a "SQLSTATE = 51002 SQLCODE =-805" karakterláncot, kövesse a "Tipp" című részt az [adatok másolása DB2-ből Azure Data Factory használatával](https://docs.microsoft.com/azure/data-factory/connector-db2#linked-service-properties).

- **Javaslat**: próbálkozzon a "NULLID" beállítással a `packageCollection`  tulajdonságban.


## <a name="delimited-text-format"></a>Tagolt szövegformátum

### <a name="error-code-delimitedtextcolumnnamenotallownull"></a>Hibakód: DelimitedTextColumnNameNotAllowNull

- **Üzenet**: `The name of column index %index; is empty. Make sure column name is properly specified in the header row.`

- **OK**: Ha a "firstRowAsHeader" be van állítva a tevékenységben, az első sor lesz az oszlop neve. Ez a hiba azt jelenti, hogy az első sor üres értéket tartalmaz (például "columna, ColumnB").

- **Javaslat**: Ellenőrizze az első sort, és ha üres, javítsa ki az értéket.


### <a name="error-code-delimitedtextmorecolumnsthandefined"></a>Hibakód: DelimitedTextMoreColumnsThanDefined

- **Üzenet**: `Error found when processing '%function;' source '%name;' with row number %rowCount;: found more columns than expected column count: %expectedColumnCount;.`

- **Okok és javaslatok**: ennek a hibának a különböző okai okozhatják. A lehetséges okok elemzéséhez és a kapcsolódó javaslathoz az alábbi listában tájékozódhat.

  | Elemzés oka                                               | Ajánlás                                               |
  | :----------------------------------------------------------- | :----------------------------------------------------------- |
  | A problémás sor oszlopainak száma nagyobb, mint az első sor oszlopainak száma. Ennek oka az lehet, hogy egy adatprobléma vagy helytelen oszlop elválasztója vagy az idézőjel karakteres beállítások vannak megadva. | A sorok számának lekérése a hibaüzenetből, a sor oszlopának beolvasása és az adatok kijavítása. |
  | Ha a várt oszlopok száma "1" egy hibaüzenetben, lehetséges, hogy helytelen tömörítési vagy formázási beállításokat adott meg, ami miatt a Data Factory helytelenül elemezheti a fájlokat. | A formázási beállítások alapján ellenőrizze, hogy a forrásfájlok megfelelnek-e a fájloknak. |
  | Ha a forrás mappa, előfordulhat, hogy a megadott mappában található fájlok eltérő sémával rendelkeznek. | Győződjön meg arról, hogy a megadott mappában lévő fájlok azonos sémával rendelkeznek. |


## <a name="dynamics-365-common-data-service-and-dynamics-crm"></a>Dynamics 365, Common Data Service és Dynamics CRM

### <a name="error-code-dynamicscreateserviceclienterror"></a>Hibakód: DynamicsCreateServiceClientError

- **Üzenet**: `This is a transient issue on Dynamics server side. Try to rerun the pipeline.`

- **OK**: a probléma átmeneti probléma a Dynamics-kiszolgáló oldalán.

- **Javaslat**: futtassa újra a folyamatot. Ha nem sikerül újra, próbálja meg csökkenteni a párhuzamosságot. Ha a probléma továbbra is fennáll, forduljon a Dynamics ügyfélszolgálatához.


### <a name="missing-columns-when-you-import-a-schema-or-preview-data"></a>Hiányzó oszlopok a séma vagy az előnézeti adatelőnézetek importálásakor

- **Tünetek**: egyes oszlopok hiányoznak a sémák vagy az előnézeti adatelőnézetek importálásakor. Hibaüzenet: `The valid structure information (column name and type) are required for Dynamics source.`

- **OK**: Ez a probléma a kialakítás alapján történik, mert Data Factory nem tud megjeleníteni olyan oszlopokat, amelyek nem tartalmaznak értéket az első 10 rekordban. Győződjön meg arról, hogy a hozzáadott oszlopok formátuma megfelelő. 

- **Javaslat**: manuálisan adja hozzá az oszlopokat a leképezés lapon.


### <a name="error-code-dynamicsmissingtargetformultitargetlookupfield"></a>Hibakód: DynamicsMissingTargetForMultiTargetLookupField

- **Üzenet**: `Cannot find the target column for multi-target lookup field: '%fieldName;'.`

- **OK**: a cél oszlop nem létezik a forrásban vagy az oszlop-hozzárendelésben.

- **Javaslat**:  
  1. Győződjön meg arról, hogy a forrás tartalmazza a cél oszlopot. 
  2. Adja hozzá a cél oszlopot az oszlop-hozzárendelésben. Győződjön meg arról, hogy a fogadó oszlop a *{Mezőnév} @EntityReference* formátumban van.


### <a name="error-code-dynamicsinvalidtargetformultitargetlookupfield"></a>Hibakód: DynamicsInvalidTargetForMultiTargetLookupField

- **Üzenet**: `The provided target: '%targetName;' is not a valid target of field: '%fieldName;'. Valid targets are: '%validTargetNames;'`

- **OK**: az entitás neve egy többhelyes keresési mező célként megadott entitása.

- **Javaslat**: adjon meg egy érvényes entitás nevét a többhelyes keresési mezőhöz.


### <a name="error-code-dynamicsinvalidtypeformultitargetlookupfield"></a>Hibakód: DynamicsInvalidTypeForMultiTargetLookupField

- **Üzenet**: `The provided target type is not a valid string. Field: '%fieldName;'.`

- **OK**: a cél oszlopban szereplő érték nem karakterlánc.

- **Javaslat**: adjon meg egy érvényes karakterláncot a többszörös célként szolgáló keresési cél oszlopban.


### <a name="error-code-dynamicsfailedtorequetserver"></a>Hibakód: DynamicsFailedToRequetServer

- **Üzenet**: `The Dynamics server or the network is experiencing issues. Check network connectivity or check Dynamics server log for more details.`

- **OK**: a Dynamics-kiszolgáló nem stabil vagy nem érhető el, vagy a hálózat problémákba ütközik.

- **Javaslat**: további részletekért keresse meg a hálózati kapcsolatot, vagy keresse meg a Dynamics Server naplóját. További segítségért forduljon a Dynamics ügyfélszolgálatához.
  

## <a name="ftp"></a>FTP

### <a name="error-code-ftpfailedtoconnecttoftpserver"></a>Hibakód: FtpFailedToConnectToFtpServer

- **Üzenet**: `Failed to connect to FTP server. Please make sure the provided server information is correct, and try again.`

- **OK**: az FTP-kiszolgálóhoz helytelen társított szolgáltatástípus használható, például a Secure FTP (SFTP) társított szolgáltatás használata az FTP-kiszolgálóhoz való kapcsolódáshoz.

- **Javaslat**: keresse meg a célkiszolgáló portját. Az FTP a 21-es portot használja.


## <a name="http"></a>HTTP

### <a name="error-code-httpfilefailedtoread"></a>Hibakód: HttpFileFailedToRead

- **Üzenet**: `Failed to read data from http server. Check the error from http server：%message;`

- **OK**: Ez a hiba akkor fordul elő, ha Azure Data Factory beszél a http-kiszolgálóról, de a HTTP-kérelem művelete meghiúsul.

- **Javaslat**: a hibaüzenetben keresse meg a HTTP-állapotkódot, és javítsa ki a távoli kiszolgáló hibáját.


## <a name="oracle"></a>Oracle

### <a name="error-code-argumentoutofrangeexception"></a>Hibakód: ArgumentOutOfRangeException

- **Üzenet**: `Hour, Minute, and Second parameters describe an un-representable DateTime.`

- **OK**: a Data Factory a DateTime értékeket a 0001-01-01 00:00:00 és 9999-12-31 23:59:59 közötti tartomány támogatja. Az Oracle azonban a DateTime értékek szélesebb körét támogatja, például a BC Century vagy a min/mp>59, amely a Data Factory hibáját eredményezi.

- **Javaslat**: 

    Ha szeretné megtekinteni, hogy az Oracle-beli érték a Data Factory tartományban van-e, futtassa a parancsot `select dump(<column name>)` . 

    Az eredményben szereplő bájtok sorrendjének megismeréséhez tekintse meg az [Oracle-ben tárolt dátumok?](https://stackoverflow.com/questions/13568193/how-are-dates-stored-in-oracle)című témakört.


## <a name="orc-format"></a>ORC formátum

### <a name="error-code-orcjavainvocationexception"></a>Hibakód: OrcJavaInvocationException

- **Üzenet**: `An error occurred when invoking Java, message: %javaException;.`
- **Okok és javaslatok**: ennek a hibának a különböző okai okozhatják. A lehetséges okok elemzéséhez és a kapcsolódó javaslathoz az alábbi listában tájékozódhat.

    | Elemzés oka                                               | Ajánlás                                               |
    | :----------------------------------------------------------- | :----------------------------------------------------------- |
    | Ha a hibaüzenet tartalmazza a "Java. lang. OutOfMemory", a "Java heap Space" és a "doubleCapacity" karakterláncot, az integrációs modul egy korábbi verziójában általában memória-kezelési probléma van. | Ha önkiszolgáló Integration Runtime használ, javasoljuk, hogy frissítsen a legújabb verzióra. |
    | Ha a hibaüzenet tartalmazza a "Java. lang. OutOfMemory" karakterláncot, az Integration Runtime nem rendelkezik elegendő erőforrással a fájlok feldolgozásához. | Az egyidejű futtatások korlátozása az Integration Runtime-ban. A saját üzemeltetésű integrációs modul esetében egy nagy teljesítményű, 8 GB-nál nagyobb memóriával rendelkező gépre méretezhető. |
    |Ha a hibaüzenet tartalmazza a "NullPointerReference" karakterláncot, az ok lehet átmeneti hiba. | Próbálja meg újra a műveletet. Ha a probléma továbbra is fennáll, forduljon az ügyfélszolgálathoz. |
    | Ha a hibaüzenet tartalmazza a "BufferOverflowException" karakterláncot, az ok lehet átmeneti hiba. | Próbálja meg újra a műveletet. Ha a probléma továbbra is fennáll, forduljon az ügyfélszolgálathoz. |
    | Ha a hibaüzenet tartalmazza a "Java. lang. ClassCastException:org. Apache. Hadoop. kaptár. serde2. IO. HiveCharWritable" karakterláncot, nem lehet a org. Apache. Hadoop. IO. Text fájlba írni, az ok lehet a Java futtatókörnyezetben a típus-átalakítási probléma. Ez általában azt jelenti, hogy a forrásadatok nem kezelhetők jól a Java-futtatókörnyezetben. | Ez egy adatprobléma. Használjon karakterláncot a char vagy varchar helyett az ork formátumában. |

### <a name="error-code-orcdatetimeexceedlimit"></a>Hibakód: OrcDateTimeExceedLimit

- **Üzenet**: `The Ticks value '%ticks;' for the datetime column must be between valid datetime ticks range -621355968000000000 and 2534022144000000000.`

- **OK**: Ha a DateTime érték "0001-01-01 00:00:00", a [Julián-naptár és a Gergely-naptár](https://en.wikipedia.org/wiki/Proleptic_Gregorian_calendar#Difference_between_Julian_and_proleptic_Gregorian_calendar_dates)közötti különbségek okozhatják.

- **Javaslat**: a kullancsok értékének megadásával és a "0001-01-01 00:00:00" datetime érték használatának elkerülésével.


## <a name="parquet-format"></a>Parquet formátum

### <a name="error-code-parquetjavainvocationexception"></a>Hibakód: ParquetJavaInvocationException

- **Üzenet**: `An error occurred when invoking java, message: %javaException;.`

- **Okok és javaslatok**: ennek a hibának a különböző okai okozhatják. A lehetséges okok elemzéséhez és a kapcsolódó javaslathoz az alábbi listában tájékozódhat.

    | Elemzés oka                                               | Ajánlás                                               |
    | :----------------------------------------------------------- | :----------------------------------------------------------- |
    | Ha a hibaüzenet tartalmazza a "Java. lang. OutOfMemory", a "Java heap Space" és a "doubleCapacity" karakterláncot, az általában a Integration Runtime korábbi verziójában található memória-kezelési probléma. | Ha saját üzemeltetésű integrációs modult használ, és a verzió korábbi, mint a 3.20.7159.1, javasoljuk, hogy frissítsen a legújabb verzióra. |
    | Ha a hibaüzenet tartalmazza a "Java. lang. OutOfMemory" karakterláncot, az Integration Runtime nem rendelkezik elegendő erőforrással a fájlok feldolgozásához. | Az egyidejű futtatások korlátozása az Integration Runtime-ban. A saját üzemeltetésű integrációs modul esetében akár 8 GB-nál nagyobb memóriával, akár egy nagy teljesítményű géppel is méretezhető. |
    | Ha a hibaüzenet tartalmazza a "NullPointerReference" karakterláncot, átmeneti hiba lehet. | Próbálja meg újra a műveletet. Ha a probléma továbbra is fennáll, forduljon az ügyfélszolgálathoz. |

### <a name="error-code-parquetinvalidfile"></a>Hibakód: ParquetInvalidFile

- **Üzenet**: `File is not a valid Parquet file.`

- **OK**: ez egy parketta-fájl probléma.

- **Javaslat**: Ellenőrizze, hogy a bemenet érvényes parketta-fájl-e.


### <a name="error-code-parquetnotsupportedtype"></a>Hibakód: ParquetNotSupportedType

- **Üzenet**: `Unsupported Parquet type. PrimitiveType: %primitiveType; OriginalType: %originalType;.`

- **OK**: a Azure Data Factory nem támogatja a parketta formátumát.

- **Javaslat**: Ellenőrizze a forrásadatokat úgy, hogy a [másolási tevékenység által támogatott fájlformátumokat és tömörítési kodekeket Azure Data Factory](https://docs.microsoft.com/azure/data-factory/supported-file-formats-and-compression-codecs).


### <a name="error-code-parquetmisseddecimalprecisionscale"></a>Hibakód: ParquetMissedDecimalPrecisionScale

- **Üzenet**: `Decimal Precision or Scale information is not found in schema for column: %column;.`

- **OK**: a pontosság és a skála elemzése megtörtént, de ilyen adatok nem lettek megadva.

- **Javaslat**: a forrás nem a megfelelő pontossággal és skálázással kapcsolatos információkat ad vissza. A probléma oszlopban keresse meg az adatokat.


### <a name="error-code-parquetinvaliddecimalprecisionscale"></a>Hibakód: ParquetInvalidDecimalPrecisionScale

- **Üzenet**: `Invalid Decimal Precision or Scale. Precision: %precision; Scale: %scale;.`

- **OK**: a séma érvénytelen.

- **Javaslat**: a probléma oszlopban keresse meg a pontosságot és a méretezést.


### <a name="error-code-parquetcolumnnotfound"></a>Hibakód: ParquetColumnNotFound

- **Üzenet**: `Column %column; does not exist in Parquet file.`

- **OK**: a forrás sémája nem egyezik a fogadó sémával.

- **Javaslat**: vizsgálja meg a tevékenység leképezéseit. Győződjön meg arról, hogy a forrás oszlop a megfelelő fogadó oszlopra van leképezve.


### <a name="error-code-parquetinvaliddataformat"></a>Hibakód: ParquetInvalidDataFormat

- **Üzenet**: `Incorrect format of %srcValue; for converting to %dstType;.`

- **OK**: az adattípusok nem alakíthatók át a leképezések. forrásban megadott típusra.

- **Javaslat**: Ellenőrizze a forrásadatokat, vagy adja meg a megfelelő adattípust ehhez az oszlophoz a másolási tevékenység oszlop leképezésében. További információ: [másolási tevékenység által támogatott fájlformátumok és tömörítési kodekek Azure Data Factoryban](https://docs.microsoft.com/azure/data-factory/supported-file-formats-and-compression-codecs).


### <a name="error-code-parquetdatacountnotmatchcolumncount"></a>Hibakód: ParquetDataCountNotMatchColumnCount

- **Üzenet**: `The data count in a row '%sourceColumnCount;' does not match the column count '%sinkColumnCount;' in given schema.`

- **OK**: a forrás oszlopainak száma és a fogadó oszlopok száma nem egyezik.

- **Javaslat**: Ellenőrizze, hogy a forrás oszlop száma megegyezik-e a "leképezés" fogadó oszlopának számával.


### <a name="error-code-parquetdatatypenotmatchcolumntype"></a>Hibakód: ParquetDataTypeNotMatchColumnType

- **Üzenet**: `The data type %srcType; is not match given column type %dstType; at column '%columnIndex;'.`

- **OK**: a forrásból származó adatok nem alakíthatók át a fogadóban definiált típusra.

- **Javaslat**: adjon meg egy megfelelő típust a Mapping. mosogatóban.


### <a name="error-code-parquetbridgeinvaliddata"></a>Hibakód: ParquetBridgeInvalidData

- **Üzenet**: `%message;`

- **OK**: az adatérték túllépte a korlátot.

- **Javaslat**: Próbálja megismételni a műveletet. Ha a probléma továbbra is fennáll, lépjen kapcsolatba velünk.


### <a name="error-code-parquetunsupportedinterpretation"></a>Hibakód: ParquetUnsupportedInterpretation

- **Üzenet**: `The given interpretation '%interpretation;' of Parquet format is not supported.`

- **OK**: Ez a forgatókönyv nem támogatott.

- **Javaslat**: a "ParquetInterpretFor" nem lehet "sparkSql".


### <a name="error-code-parquetunsupportfilelevelcompressionoption"></a>Hibakód: ParquetUnsupportFileLevelCompressionOption

- **Üzenet**: `File level compression is not supported for Parquet.`

- **OK**: Ez a forgatókönyv nem támogatott.

- **Javaslat**: távolítsa el a "fájba" részt a hasznos adatok között.


### <a name="error-code-usererrorjniexception"></a>Hibakód: UserErrorJniException

- **Üzenet**: `Cannot create JVM: JNI return code [-6][JNI call failed: Invalid arguments.]`

- **OK**: nem hozható létre Java virtuális gép (JVM), mert egyes tiltott (globális) argumentumok be vannak állítva.

- **Javaslat**: Jelentkezzen be arra a gépre, amely a saját üzemeltetésű integrációs modul *minden csomópontját* üzemelteti. Győződjön meg arról, hogy a rendszerváltozó helyesen van beállítva, az alábbiak szerint: `_JAVA_OPTIONS "-Xms256m -Xmx16g" with memory bigger than 8 G` . Indítsa újra az összes IR-csomópontot, majd futtassa újra a folyamatot.


### <a name="arithmetic-overflow"></a>Aritmetikai túlcsordulás

- **Tünetek**: a Parquet-fájlok másolásakor hibaüzenet fordult elő: `Message = Arithmetic Overflow., Source = Microsoft.DataTransfer.Common`

- **OK**: jelenleg csak a pontosság tizedes tört <= 38, a <= 20 egész szám hosszúsága pedig támogatott, ha az Oracle-ből a parkettára másol fájlokat. 

- **Megoldás**: áthidaló megoldásként bármilyen oszlopot átalakíthat a probléma VARCHAR2.


### <a name="no-enum-constant"></a>Nem állandó Enum

- **Tünetek**: a következő hibaüzenet jelenik meg, amikor az Adatmásolás parketta-formátumba történt: `java.lang.IllegalArgumentException:field ended by &apos;;&apos;` , vagy: `java.lang.IllegalArgumentException:No enum constant org.apache.parquet.schema.OriginalType.test` .

- **OK**: 

    A probléma oka lehet a szóközök vagy a nem támogatott speciális karakterek (például:;). {} () \n\t =) az oszlop nevében, mert a parketta nem támogatja ezt a formátumot. 

    Ilyen például a *contoso (test)* nevű oszlop, amely a [kód](https://github.com/apache/parquet-mr/blob/master/parquet-column/src/main/java/org/apache/parquet/schema/MessageTypeParser.java) alapján zárójelekben fogja elemezni a típust `Tokenizer st = new Tokenizer(schemaString, " ;{}()\n\t");` . A hibát a rendszer eldobta, mert nincs ilyen "teszt" típus.

    A támogatott típusok kereséséhez lépjen a GitHub [Apache/Parque-Mr webhelyére](https://github.com/apache/parquet-mr/blob/master/parquet-column/src/main/java/org/apache/parquet/schema/OriginalType.java).

- **Megoldás**: 

    Ellenőrizze, hogy van-e a következő:
    - A fogadó oszlop neve szóközöket is tartalmazhat.
    - Az oszlop neveként az első sort szóközökkel együtt kell használni.
    - A OriginalType típusa támogatott. Próbálja meg elkerülni ezeket a speciális karaktereket: `,;{}()\n\t=` . 


## <a name="rest"></a>REST

### <a name="error-code-restsinkcallfailed"></a>Hibakód: RestSinkCallFailed

- **Üzenet**: `Rest Endpoint responded with Failure from server. Check the error from server:%message;`

- **OK**: Ez a hiba akkor fordul elő, ha Azure Data Factory a REST-végpontot HTTP protokollon keresztül beszél, és a kérés művelete meghiúsul.

- **Javaslat**: keresse meg a HTTP-állapotkódot vagy az üzenetet a hibaüzenetben, és javítsa ki a távoli kiszolgáló hibáját.

### <a name="unexpected-network-response-from-the-rest-connector"></a>Váratlan hálózati válasz a REST-összekötőtől

- **Tünetek**: a végpont időnként váratlan választ kap (400, 401, 403, 500) a REST-összekötőből.

- **OK**: a REST forrás-összekötő az URL-címet és a http-metódust/fejlécet/törzset használja a társított szolgáltatás/adatkészlet/másolás forrása paraméterként, amikor HTTP-kérést hoz létre. A problémát valószínűleg egy vagy több megadott paraméter hibája okozza.

- **Megoldás**: 
    - A Parancssor ablakban a "curl" használatával ellenőrizze, hogy az OK (**elfogadás** és **felhasználói ügynök** fejlécei mindig szerepeljenek-e):
    
      `curl -i -X <HTTP method> -H <HTTP header1> -H <HTTP header2> -H "Accept: application/json" -H "User-Agent: azure-data-factory/2.0" -d '<HTTP body>' <URL>`
      
      Ha a parancs ugyanazt a váratlan választ adja vissza, akkor a "curl" értékkel javítsa ki az előző paramétereket, amíg vissza nem adja a várt választ. 

      A parancs fejlettebb használatához használhatja a "curl--help" parancsot is.

    - Ha csak a Data Factory REST-összekötő váratlan választ ad vissza, további hibaelhárításért forduljon a Microsoft támogatási szolgálatához.
    
    - Vegye figyelembe, hogy a "curl" nem alkalmas az SSL-tanúsítvány érvényesítési hibájának ismételt létrehozására. Bizonyos helyzetekben a "curl" parancs végrehajtása az SSL-tanúsítvány érvényesítési problémáinak hiánya nélkül történt meg. Ha azonban ugyanazon URL-címet egy böngészőben hajtja végre, a rendszer nem ad vissza SSL-tanúsítványt az ügyfél számára a kiszolgálóval való megbízhatósági kapcsolat létrehozásához.

      Az előző esetben ajánlott a **Poster** és a **Hegedűs** eszközök használata.


## <a name="sftp"></a>SFTP

#### <a name="error-code-sftpoperationfail"></a>Hibakód: SftpOperationFail

- **Üzenet**: `Failed to '%operation;'. Check detailed error from SFTP.`

- **OK**: az SFTP-művelettel kapcsolatos probléma.

- **Javaslat**: a hiba részleteinek megkeresése az SFTP-től.


### <a name="error-code-sftprenameoperationfail"></a>Hibakód: SftpRenameOperationFail

- **Üzenet**: `Failed to rename the temp file. Your SFTP server doesn't support renaming temp file, set "useTempFileRename" as false in copy sink to disable uploading to temp file.`

- **OK**: az SFTP-kiszolgáló nem támogatja a temp fájl átnevezését.

- Javaslat: állítsa a "useTempFileRename" értéket hamis értékre a másolási **fogadóban** az ideiglenes fájlba való feltöltés letiltásához.


### <a name="error-code-sftpinvalidsftpcredential"></a>Hibakód: SftpInvalidSftpCredential

- **Üzenet**: `Invalid SFTP credential provided for '%type;' authentication type.`

- **OK**: a titkos kulcs tartalmának beolvasása az Azure Key vaultból vagy SDK-ból történik, de helytelenül van kódolva.

- **Javaslat**:  

    Ha a titkos kulcs tartalma a kulcstartóból származik, az eredeti kulcsfájl működhet, ha közvetlenül az SFTP társított szolgáltatásba tölti fel.

    További információkért lásd: [adatok másolása az SFTP-kiszolgálóra Azure Data Factory használatával](https://docs.microsoft.com/azure/data-factory/connector-sftp#using-ssh-public-key-authentication). A titkos kulcs tartalma Base64 kódolású titkos SSH-kulcs.

    Kódolja a *teljes* eredeti titkos kulcsot Base64 kódolással, és tárolja a kódolt karakterláncot a kulcstartóban. Az eredeti titkos kulcsfájl az a fájl, amely az SFTP társított szolgáltatásban működhet, ha a fájlból a **feltöltés** lehetőséget választja.

    Íme néhány példa a karakterlánc létrehozásához:

    - C#-kód használata:

        ```
        byte[] keyContentBytes = File.ReadAllBytes(Private Key Path);
        string keyContent = Convert.ToBase64String(keyContentBytes, Base64FormattingOptions.None);
        ```

    - Python-kód használata:

        ```
        import base64
        rfd = open(r'{Private Key Path}', 'rb')
        keyContent = rfd.read()
        rfd.close()
        print base64.b64encode(Key Content)
        ```

    - Használjon harmadik féltől származó Base64 átalakító eszközt. Javasoljuk [, hogy a kódolás Base64 formátumú](https://www.base64encode.org/) eszközre történjen.

- **OK**: helytelen a kulcs tartalmának formátuma.

- **Javaslat**:  

    A PKCS # 8 formátum SSH titkos kulcsa (a "-----BEGIN ENCRYPTED PRIVATE KEY-----" kifejezéssel kezdődően) jelenleg nem támogatott az SFTP-kiszolgáló eléréséhez Data Factory. 

    Ha a kulcsot a hagyományos SSH-kulcs formátumára szeretné átalakítani, a "-----megkezdhető RSA titkos kulcs-----" kezdetű paranccsal futtassa a következő parancsokat:

    ```
    openssl pkcs8 -in pkcs8_format_key_file -out traditional_format_key_file
    chmod 600 traditional_format_key_file
    ssh-keygen -f traditional_format_key_file -p
    ```

- **OK**: érvénytelen hitelesítő adatok vagy titkos kulcs tartalma.

- **Javaslat**: Ha meg szeretné tekinteni, hogy helyes-e a kulcsfájl vagy a jelszó, ellenőrizze, hogy van-e megfelelő eszköz, például a megnyerő eszközzel.

### <a name="sftp-copy-activity-failed"></a>Az SFTP-másolási tevékenység nem sikerült

- **Tünetek**: 
  * Hibakód: UserErrorInvalidColumnMappingColumnNotFound 
  * Hibaüzenet: `Column 'AccMngr' specified in column mapping cannot be found in source data.`

- **OK**: a forrás nem tartalmaz "AccMngr" nevű oszlopot.

- **Megoldás**: annak megállapításához, hogy létezik-e a "AccMngr" oszlop, ellenőrizze az adatkészlet konfigurációját a cél adatkészlet oszlop leképezésével.


### <a name="error-code-sftpfailedtoconnecttosftpserver"></a>Hibakód: SftpFailedToConnectToSftpServer

- **Üzenet**: `Failed to connect to SFTP server '%server;'.`

- **OK**: Ha a hibaüzenetben a "szoftvercsatorna olvasási művelete 30 000 ezredmásodperc után túllépte az időkorlátot", az egyik lehetséges ok az, hogy helytelen társított szolgáltatástípus van használatban az SFTP-kiszolgálóhoz. Előfordulhat például, hogy az FTP-társított szolgáltatást használja az SFTP-kiszolgálóhoz való kapcsolódáshoz.

- **Javaslat**: keresse meg a célkiszolgáló portját. Alapértelmezés szerint az SFTP a 22-es portot használja.

- **OK**: Ha a hibaüzenetben a "kiszolgáló válasza nem tartalmazza az ssh-protokoll azonosítását" karakterláncot tartalmazza, az egyik lehetséges ok az, hogy az SFTP-kiszolgáló leszabályozza a kapcsolatokat. Data Factory több kapcsolatot hoz létre az SFTP-kiszolgálóról párhuzamosan, és időnként az SFTP-kiszolgáló szabályozása is megjelenhet. A különböző kiszolgálók általában különböző hibákat adnak vissza, amikor a szabályozás során jelentkeznek.

- **Javaslat**:  

    Itt adhatja meg az SFTP-adatkészlet egyidejű kapcsolatainak maximális számát 1 értékként, majd futtassa újra a másolási tevékenységet. Ha a tevékenység sikeres, biztos lehet benne, hogy a szabályozás oka.

    Ha előléptetni szeretné az alacsony átviteli sebességet, forduljon az SFTP-rendszergazdához, és növelje az egyidejű kapcsolati korlátot, vagy hajtsa végre az alábbi műveletek egyikét:

    * Ha saját üzemeltetésű IR-t használ, adja hozzá a saját üzemeltetésű IR-gép IP-címét az engedélyezési listához.
    * Ha Azure IR használ, vegyen fel [Azure Integration Runtime IP-címeket](https://docs.microsoft.com/azure/data-factory/azure-integration-runtime-ip-addresses). Ha nem szeretne IP-címtartományt hozzáadni az SFTP-kiszolgáló engedélyezési listájához, használja helyette a saját üzemeltetésű IR-t.

## <a name="sharepoint-online-list"></a>SharePoint Online-lista

### <a name="error-code-sharepointonlineauthfailed"></a>Hibakód: SharePointOnlineAuthFailed

- **Üzenet**: `The access token generated failed, status code: %code;, error message: %message;.`

- **OK**: lehet, hogy a szolgáltatás egyszerű azonosítója és kulcsa helytelenül van beállítva.

- **Javaslat**: Ellenőrizze, hogy a regisztrált alkalmazás (egyszerű szolgáltatásnév) és a kulcs megfelelően van-e beállítva.


## <a name="xml-format"></a>XML formátum

### <a name="error-code-xmlsinknotsupported"></a>Hibakód: XmlSinkNotSupported

- **Üzenet**: `Write data in XML format is not supported yet, choose a different format!`

- **OK**: egy XML-adatkészlet fogadó adatkészletként lett használva a másolási tevékenységben.

- **Javaslat**: használjon egy adatkészletet más formátumban a fogadó adatkészletből.


### <a name="error-code-xmlattributecolumnnameconflict"></a>Hibakód: XmlAttributeColumnNameConflict

- **Üzenet**: `Column names %attrNames;' for attributes of element '%element;' conflict with that for corresponding child elements, and the attribute prefix used is '%prefix;'.`

- **OK**: az attribútum előtagját használták, ami az ütközést okozta.

- **Javaslat**: állítson be egy másik értéket a "attributePrefix" tulajdonsághoz.


### <a name="error-code-xmlvaluecolumnnameconflict"></a>Hibakód: XmlValueColumnNameConflict

- **Üzenet**: `Column name for the value of element '%element;' is '%columnName;' and it conflicts with the child element having the same name.`

- **OK**: az egyik alárendelt elem neve az elem értékének oszlop neveként lett használva.

- **Javaslat**: állítson be egy másik értéket a "valueColumn" tulajdonsághoz.


### <a name="error-code-xmlinvalid"></a>Hibakód: XmlInvalid

- **Üzenet**: `Input XML file '%file;' is invalid with parsing error '%error;'.`

- **OK**: a bemeneti XML-fájl nem megfelelően formázott.

- **Javaslat**: javítsa ki az XML-fájlt, hogy megfelelően legyen formázva.


## <a name="general-copy-activity-error"></a>Általános másolási tevékenység hibája

### <a name="error-code-jrenotfound"></a>Hibakód: JreNotFound

- **Üzenet**: `Java Runtime Environment cannot be found on the Self-hosted Integration Runtime machine. It is required for parsing or writing to Parquet/ORC files. Make sure Java Runtime Environment has been installed on the Self-hosted Integration Runtime machine.`

- **OK**: a saját üzemeltetésű IR nem találja a Java futtatókörnyezetet. Az egyes források olvasásához Java futtatókörnyezet szükséges.

- **Javaslat**: Ellenőrizze az integrációs modul környezetét, lásd: saját üzemeltetésű [Integration Runtime használata](https://docs.microsoft.com/azure/data-factory/format-parquet#using-self-hosted-integration-runtime).


### <a name="error-code-wildcardpathsinknotsupported"></a>Hibakód: WildcardPathSinkNotSupported

- **Üzenet**: `Wildcard in path is not supported in sink dataset. Fix the path: '%setting;'.`

- **OK**: a fogadó adatkészlet nem támogatja a helyettesítő karaktereket.

- **Javaslat**: keresse meg a fogadó adatkészletet, és írja át újra az elérési utat helyettesítő karakteres érték használata nélkül.


### <a name="fips-issue"></a>FIPS-probléma

- **Tünetek**: a másolási tevékenység sikertelen a FIPS-kompatibilis, saját üzemeltetésű IR-gépen, a következő hibaüzenettel: `This implementation is not part of the Windows Platform FIPS validated cryptographic algorithms.` 

- **OK**: Ez a hiba akkor fordulhat elő, ha olyan összekötőket másol, mint például az Azure Blob, az SFTP és így tovább. A Federal Information Processing Standards (FIPS) meghatározott titkosítási algoritmusokat határoz meg, amelyeket használhat. Ha a FIPS mód engedélyezve van a gépen, bizonyos helyzetekben a másolási tevékenységtől függő titkosítási osztályok blokkolva vannak.

- **Megoldás**: Ismerje meg, hogy [Miért nem ajánljuk többé a "FIPS mód" használatát](https://techcommunity.microsoft.com/t5/microsoft-security-baselines/why-we-8217-re-not-recommending-8220-fips-mode-8221-anymore/ba-p/701037), és ellenőrizze, hogy le tudja-e tiltani a FIPS-t a saját üzemeltetésű IR-gépen.

    Ha csak azt szeretné, hogy Azure Data Factory a FIPS megkerülését, és a tevékenység futása sikeres legyen, tegye a következőket:

    1. Nyissa meg azt a mappát, ahol a saját üzemeltetésű IR telepítve van. Az elérési út általában *C:\Program Files\Microsoft Integration Runtime \<IR version> \Shared*.

    2. Nyissa meg a *diawp.exe.config* fájlt, majd a szakasz végén adja hozzá a következőt `<runtime>` `<enforceFIPSPolicy enabled="false"/>` , ahogy az itt látható:

        ![Képernyőkép a diawp.exe.config fájl egy szakaszáról, amely a FIPS letiltását mutatja.](./media/connector-troubleshoot-guide/disable-fips-policy.png)

    3. Mentse a fájlt, majd indítsa újra a saját üzemeltetésű IR-gépet.


## <a name="next-steps"></a>Következő lépések

További hibaelhárítási segítségért próbálja ki ezeket az erőforrásokat:

*  [Data Factory blog](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [Data Factory szolgáltatásra vonatkozó kérelmek](https://feedback.azure.com/forums/270578-data-factory)
*  [Azure-videók](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [A Microsoft Q&egy oldalt](/answers/topics/azure-data-factory.html)
*  [Data Factory Stack Overflow fóruma](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [Twitter-információk a Data Factory](https://twitter.com/hashtag/DataFactory)
