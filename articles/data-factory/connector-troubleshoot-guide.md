---
title: Azure Data Factory-összekötők hibaelhárítása
description: Megtudhatja, hogyan lehet elhárítani a Azure Data Factory összekötői problémáit.
services: data-factory
author: linda33wj
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 01/07/2021
ms.author: jingwang
ms.reviewer: craigg
ms.custom: has-adal-ref
ms.openlocfilehash: 4b586b95232af2a154993523402f81ee88a22cda
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/28/2021
ms.locfileid: "98946157"
---
# <a name="troubleshoot-azure-data-factory-connectors"></a>Azure Data Factory-összekötők hibaelhárítása

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Ez a cikk a Azure Data Factory összekötők gyakori hibaelhárítási módszereit vizsgálja.
  
## <a name="azure-blob-storage"></a>Azure Blob Storage

### <a name="error-code-azurebloboperationfailed"></a>Hibakód: AzureBlobOperationFailed

- **Üzenet**: `Blob operation Failed. ContainerName: %containerName;, path: %path;.`

- **OK**: a blob Storage-művelettel kapcsolatos probléma lépett fel.

- **Javaslat**: vizsgálja meg a hibát a részletek között. Tekintse meg a blob Súgó dokumentumát: https://docs.microsoft.com/rest/api/storageservices/blob-service-error-codes . Ha segítségre van szüksége, lépjen kapcsolatba a Storage csapatával.


### <a name="invalid-property-during-copy-activity"></a>Érvénytelen tulajdonság a másolási tevékenység során

- **Üzenet**: `Copy activity <Activity Name> has an invalid "source" property. The source type is not compatible with the dataset <Dataset Name> and its linked service <Linked Service Name>. Please verify your input against.`

- **OK**: az adatkészletben definiált típus nem konzisztens a másolási tevékenységben definiált forrás/fogadó típussal.

- **Megoldás**: szerkessze az adatkészlet vagy a folyamat JSON-definícióját, hogy a típusok konzisztensek legyenek, majd futtassa újra a telepítést.


## <a name="azure-cosmos-db"></a>Azure Cosmos DB

### <a name="error-message-request-size-is-too-large"></a>Hibaüzenet: a kérelem mérete túl nagy

- **Tünetek**: az adatmásolás Azure Cosmos DBba az alapértelmezett írási köteg méretével, és a *"**kérés mérete túl nagy**"*.

- **OK**: a Cosmos db egy kérés méretét 2 MB-ra korlátozza. A képlet:, kérelem mérete = egydokumentumos méret * írási köteg mérete. Ha a dokumentum mérete nagy, az alapértelmezett viselkedés túl nagy méretű kérések méretét eredményezi. Beállíthatja az írási köteg méretét.

- **Megoldás**: a másolási tevékenység fogadójában csökkentse a "batch méretének megírása" értéket (az alapértelmezett érték 10000).

### <a name="error-message-unique-index-constraint-violation"></a>Hibaüzenet: egyedi index megkötésének megsértése

- **Tünetek**: az adatok Cosmos DBba való másolása során a következő hibaüzenetet kapta:

    ```
    Message=Partition range id 0 | Failed to import mini-batch. 
    Exception was Message: {"Errors":["Encountered exception while executing function. Exception = Error: {\"Errors\":[\"Unique index constraint violation.\"]}... 
    ```

- **OK**: két lehetséges oka van:

    - Ha a **Beszúrás** írási viselkedést használja, ez a hiba azt jelenti, hogy az adatforráshoz tartozó sorok/objektumok UGYANAZZAL az azonosítóval rendelkeznek.
    - Ha a **Upsert** -et írási viselkedésként használja, és egy másik egyedi kulcsot állít be a tárolóhoz, akkor ez a hiba azt jelenti, hogy az adatforráshoz tartozó sorok/objektumok eltérő azonosítókkal rendelkeznek, de a definiált egyedi kulcshoz ugyanaz az érték tartozik.

- **Megoldás**: 

    - A cause1 esetében állítsa a **Upsert** írási viselkedésként.
    - A 2. ok esetén győződjön meg arról, hogy minden dokumentum eltérő értékkel rendelkezik a definiált egyedi kulcshoz.

### <a name="error-message-request-rate-is-large"></a>Hibaüzenet: a kérelmek aránya nagy

- **Tünetek**: az adatok Cosmos DBba való másolása során a következő hibaüzenetet kapta:

    ```
    Type=Microsoft.Azure.Documents.DocumentClientException,
    Message=Message: {"Errors":["Request rate is large"]}
    ```

- **OK**: a használt kérelmek száma nagyobb, mint a Cosmos DBban konfigurált, rendelkezésre álló ru. Ismerje meg, hogyan számítja ki a Cosmos DB RU- [t innen.](../cosmos-db/request-units.md#request-unit-considerations)

- **Megoldás**: két megoldás:

    - **Növelje a tároló ru** -t nagyobb értékre Cosmos DBban, ami javítja a másolási tevékenység teljesítményét, de a Cosmos db további költségekkel járna. 
    - Csökkentse a **writeBatchSize** kisebb értékre (például 1000), és állítsa be a **parallelCopies** -t kisebb értékre (például 1), amely a jelenleginél rosszabban fogja a másolási teljesítményt, de a Cosmos db nem jár további költségekkel.

### <a name="column-missing-in-column-mapping"></a>Az oszlop-hozzárendelésből hiányzó oszlop

- **Tünetek**: Ha az oszlop-hozzárendeléshez Cosmos db sémát importál, egyes oszlopok hiányoznak. 

- **OK**: Az ADF kikövetkezteti a sémát az első 10 Cosmos db dokumentumból. Ha egyes oszlopok/tulajdonságok nem rendelkeznek értékkel ezekben a dokumentumokban, az ADF nem észleli azt, így nem fog megjelenni.

- **Megoldás**: az alábbi módon beállíthatja a lekérdezést úgy, hogy az oszlop kikényszerítve jelenjen meg az eredményhalmaz üres értékével: (feltételezhető, hogy "Impossible" oszlop hiányzik az első 10 dokumentumból). Azt is megteheti, hogy manuálisan hozzáadja az oszlopot a leképezéshez.

    ```sql
    select c.company, c.category, c.comments, (c.impossible??'') as impossible from c
    ```

### <a name="error-message-the-guidrepresentation-for-the-reader-is-csharplegacy"></a>Hibaüzenet: az olvasó GuidRepresentation CSharpLegacy

- **Tünetek**: Cosmos db MongoAPI/MongoDB és az UUID mezőből származó adatok másolása során a következő hibaüzenetet kapta:

    ```
    Failed to read data via MongoDB client.,
    Source=Microsoft.DataTransfer.Runtime.MongoDbV2Connector,Type=System.FormatException,
    Message=The GuidRepresentation for the reader is CSharpLegacy which requires the binary sub type to be UuidLegacy not UuidStandard.,Source=MongoDB.Bson,’“,
    ```

- **OK**: az UUID-t kétféleképpen lehet a BSON-UuidStardard és a UuidLegacy-ben ábrázolni. Alapértelmezés szerint a UuidLegacy az adatolvasásra szolgál. Ha a MongoDB-beli UUID-adatai UuidStandard, a rendszer hibát jelez.

- **Megoldás**: a MongoDB-kapcsolatok karakterláncában adja hozzá a "**uuidRepresentation = standard**" beállítást. További információ: MongoDB- [kapcsolatok karakterlánca](connector-mongodb.md#linked-service-properties).
            
## <a name="azure-cosmos-db-sql-api"></a>Azure Cosmos DB (SQL API)

### <a name="error-code--cosmosdbsqlapioperationfailed"></a>Hibakód: CosmosDbSqlApiOperationFailed

- **Üzenet**: `CosmosDbSqlApi operation Failed. ErrorMessage: %msg;.`

- **OK**: a CosmosDbSqlApi művelete találatot észlelt.

- **Javaslat**: vizsgálja meg a hibát a részletek között. Tekintse meg a [CosmosDb Súgó dokumentumát](../cosmos-db/troubleshoot-dot-net-sdk.md). Ha segítségre van szüksége, forduljon a CosmosDb csapatához.


## <a name="azure-data-lake-storage-gen1"></a>1. generációs Azure Data Lake Storage

### <a name="error-message-the-underlying-connection-was-closed-could-not-establish-trust-relationship-for-the-ssltls-secure-channel"></a>Hibaüzenet: az alapul szolgáló kapcsolat bezárult: nem sikerült létrehozni a megbízhatósági kapcsolatot az SSL/TLS biztonságos csatorna számára.

- **Tünetek**: a másolási tevékenység a következő hiba miatt meghiúsul: 

    ```
    Message: ErrorCode = `UserErrorFailedFileOperation`, Error Message = `The underlying connection was closed: Could not establish trust relationship for the SSL/TLS secure channel`.
    ```

- **OK**: a tanúsítvány ellenőrzése nem sikerült a TLS-kézfogás során.

- **Megoldás**: Áthidaló megoldás: a ADLS Gen1 TLS-ellenőrzésének kihagyása szakaszos másolással. Újból létre kell hoznia ezt a problémát, és össze kell gyűjtenie a netmon-nyomkövetést, majd a hálózati csapatot kell használnia a helyi hálózati konfiguráció vizsgálatához.

    ![Hibakeresés ADLS Gen1](./media/connector-troubleshoot-guide/adls-troubleshoot.png)


### <a name="error-message-the-remote-server-returned-an-error-403-forbidden"></a>Hibaüzenet: a távoli kiszolgáló a következő hibát adta vissza: (403) tiltott

- **Tünetek**: a másolási tevékenység a következő hiba miatt meghiúsul: 

    ```
    Message: The remote server returned an error: (403) Forbidden.. 
    Response details: {"RemoteException":{"exception":"AccessControlException""message":"CREATE failed with error 0x83090aa2 (Forbidden. ACL verification failed. Either the resource does not exist or the user is not authorized to perform the requested operation.)....
    ```

- **OK**: az egyik lehetséges ok az, hogy az Ön által használt szolgáltatásnév vagy felügyelt identitás nem rendelkezik engedéllyel az adott mappához vagy fájlhoz való hozzáféréshez.

- **Megoldás**: adjon meg megfelelő engedélyeket a másolandó mappákhoz és almappákhoz. Tekintse át [ezt a dokumentációt](connector-azure-data-lake-store.md#linked-service-properties).

### <a name="error-message-failed-to-get-access-token-by-using-service-principal-adal-error-service_unavailable"></a>Hibaüzenet: nem sikerült lekérni a hozzáférési tokent az egyszerű szolgáltatásnév használatával. ADAL-hiba: service_unavailable

- **Tünetek**: a másolási tevékenység a következő hiba miatt meghiúsul:

    ```
    Failed to get access token by using service principal. 
    ADAL Error: service_unavailable, The remote server returned an error: (503) Server Unavailable.
    ```

- **OK**: ha a Azure Active Directory által birtokolt szolgáltatás-jogkivonat-kiszolgáló (STS) nem érhető el, azaz a kérelmek kezeléséhez túl elfoglalt, a 503 http-hibát ad vissza. 

- **Megoldás**: több perc elteltével futtassa újra a másolási tevékenységet.


## <a name="azure-data-lake-storage-gen2"></a>2. generációs Azure Data Lake Storage

### <a name="error-code-adlsgen2operationfailed"></a>Hibakód: ADLSGen2OperationFailed

- **Üzenet**: `ADLS Gen2 operation failed for: %adlsGen2Message;.%exceptionData;.`

- **OK**: a ADLS Gen2 a művelet hibáját jelző hibaüzenetet nem sikerült.

- **Javaslat**: ADLS Gen2 által kiváltott részletes hibaüzenet. Ha átmeneti hiba okozta, próbálkozzon újra. Ha további segítségre van szüksége, vegye fel a kapcsolatot az Azure Storage támogatási szolgálatával, és adja meg a kérelem AZONOSÍTÓját a hibaüzenetben.

- **OK**: Ha a hibaüzenet "tiltott" üzenetet tartalmaz, akkor előfordulhat, hogy az Ön által használt egyszerű szolgáltatásnév vagy felügyelt identitás nem rendelkezik elegendő engedéllyel a ADLS Gen2 eléréséhez.

- **Javaslat**: Tekintse meg a Súgó dokumentumot: https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage#service-principal-authentication .

- **OK**: Ha a hibaüzenetben a "InternalServerError" szöveg szerepel, a ADLS Gen2 a hibát adja vissza.

- **Javaslat**: Előfordulhat, hogy az átmeneti hiba okozta. próbálkozzon újra. Ha a probléma továbbra is fennáll, vegye fel a kapcsolatot az Azure Storage támogatási szolgálatával, és adja meg a kérelem AZONOSÍTÓját a hibaüzenetben.

### <a name="request-to-adls-gen2-account-met-timeout-error"></a>Kérelem ADLS Gen2 fiókra vonatkozó PGE időtúllépési hibája

- **Üzenet**: hibakód = `UserErrorFailedBlobFSOperation` , hibaüzenet = `BlobFS operation failed for: A task was canceled` .

- **OK**: a problémát a ADLS Gen2 fogadó időtúllépési hibája okozza, ami többnyire a saját üzemeltetésű IR-gépen történik.

- **Javaslat**: 

    - Ha lehetséges, helyezze a saját üzemeltetésű IR-gépet és a cél ADLS Gen2 fiókot ugyanabban a régióban. Ez elkerülheti a véletlenszerű időtúllépési hibát, és jobb teljesítményt biztosíthat.

    - Ellenőrizze, hogy van-e olyan speciális hálózati beállítás, mint a ExpressRoute, és győződjön meg arról, hogy a hálózatban elegendő sávszélesség van. Azt javasoljuk, hogy csökkentse a saját üzemeltetésű IR egyidejű feladatok beállítását, ha a teljes sávszélesség alacsony, és ezzel elkerülhető, hogy a hálózati erőforrások versenye több egyidejű feladaton keresztül történjen.

    - A nem bináris másoláshoz használjon kisebb blokk-méretet, hogy csökkentse az időtúllépési hibát, ha a fájl mérete közepes vagy kicsi. Tekintse át a [blob Storage Put blokkot](/rest/api/storageservices/put-block).

       Az egyéni blokk méretének megadásához szerkessze a tulajdonságot a. JSON-szerkesztőben:
        ```
        "sink": {
            "type": "DelimitedTextSink",
            "storeSettings": {
                "type": "AzureBlobFSWriteSettings",
                "blockSizeInMB": 8
            }
        }
        ```

                  
## <a name="azure-file-storage"></a>Azure File Storage

### <a name="error-code--azurefileoperationfailed"></a>Hibakód: AzureFileOperationFailed

- **Üzenet**: `Azure File operation Failed. Path: %path;. ErrorMessage: %msg;.`

- **OK**: az Azure file Storage művelete találatot észlelt.

- **Javaslat**: vizsgálja meg a hibát a részletek között. Tekintse meg az Azure file Súgó dokumentumát: https://docs.microsoft.com/rest/api/storageservices/file-service-error-codes . Ha segítségre van szüksége, vegye fel a kapcsolatot a Storage csapatával.


## <a name="azure-synapse-analyticsazure-sql-databasesql-server"></a>Azure szinapszis Analitika/Azure SQL Database/SQL Server

### <a name="error-code--sqlfailedtoconnect"></a>Hibakód: SqlFailedToConnect

- **Üzenet**: `Cannot connect to SQL Database: '%server;', Database: '%database;', User: '%user;'. Check the linked service configuration is correct, and make sure the SQL Database firewall allows the integration runtime to access.`

- **OK**: Azure SQL: Ha a hibaüzenetben szerepel a "SqlErrorNumber = 47073", az azt jelenti, hogy a nyilvános hálózati hozzáférés megtagadva a kapcsolati beállításban.

- **Javaslat**: az Azure SQL tűzfalon állítsa a "nyilvános hálózati hozzáférés tiltása" lehetőséget a "nem" értékre. További információ: https://docs.microsoft.com/azure/azure-sql/database/connectivity-settings#deny-public-network-access .

- **OK**: Azure SQL: Ha a hibaüzenetben szerepel az SQL-hibakód (például "SqlErrorNumber = [errorcode]"), tekintse meg az Azure SQL hibaelhárítási útmutatóját.

- **Javaslat**: További információ: https://docs.microsoft.com/azure/azure-sql/database/troubleshoot-common-errors-issues .

- **OK**: Ellenőrizze, hogy a 1433-es port a tűzfal engedélyezési listájában van-e.

- **Javaslat**: kövesse ezt a hivatkozást a következő dokumentummal: https://docs.microsoft.com/sql/sql-server/install/configure-the-windows-firewall-to-allow-sql-server-access#ports-used-by- .

- **OK**: Ha a hibaüzenetben a "SqlException" szerepel, akkor a SQL Database a hibát jelző hiba miatt nem sikerült.

- **Javaslat**: a jelen dokumentációban található SQL-hibakód alapján keressen további részleteket: https://docs.microsoft.com/sql/relational-databases/errors-events/database-engine-events-and-errors . Ha további segítségre van szüksége, lépjen kapcsolatba az Azure SQL ügyfélszolgálatával.

- **OK**: Ha ez egy átmeneti probléma (például instabil hálózati kapcsolatok), akkor az újrapróbálkozáshoz adja a tevékenység-szabályzatot, hogy enyhítse.

- **Javaslat**: kövesse ezt a hivatkozást a következő dokumentációban: https://docs.microsoft.com/azure/data-factory/concepts-pipelines-activities#activity-policy .

- **OK**: Ha a hibaüzenetben szerepel az "ügyfél IP-címmel"... a nem fér hozzá a kiszolgálóhoz, és a Azure SQL Databasehoz próbál csatlakozni, általában Azure SQL Database tűzfal okozza a problémát.

- **Javaslat**: az Azure SQL Server tűzfal konfigurációjában engedélyezze az "Azure-szolgáltatások és-erőforrások elérésének engedélyezése a kiszolgálóhoz" lehetőséget. Dokumentáció: https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure .


### <a name="error-code--sqloperationfailed"></a>Hibakód: SqlOperationFailed

- **Üzenet**: `A database operation failed. Please search error to get more details.`

- **OK**: Ha a hibaüzenetben a "SqlException" szerepel, akkor a SQL Database a hibát jelző hiba miatt nem sikerült.

- **Javaslat**: Ha az SQL-hiba nem egyértelmű, próbálja meg módosítani az adatbázist a legújabb kompatibilitási szintre (150). Ez a legújabb SQL-hibákat is képes jelezni. Tekintse át a [részletes dokumentációt](/sql/t-sql/statements/alter-database-transact-sql-compatibility-level#backwardCompat).

    Az SQL-problémák elhárításához a jelen dokumentációban keresse meg az SQL-hibakódot a további részletekért: https://docs.microsoft.com/sql/relational-databases/errors-events/database-engine-events-and-errors . Ha további segítségre van szüksége, lépjen kapcsolatba az Azure SQL ügyfélszolgálatával.

- **OK**: Ha a hibaüzenet "PdwManagedToNativeInteropException"-t tartalmaz, általában a forrás-és a fogadó oszlop méretének eltérése okozta.

- **Javaslat**: a forrás-és a fogadó oszlopok méretének megkeresése. Ha további segítségre van szüksége, lépjen kapcsolatba az Azure SQL ügyfélszolgálatával.

- **OK**: Ha a hibaüzenet "InvalidOperationException"-t tartalmaz, általában érvénytelen bemeneti adatok okozzák.

- **Javaslat**: annak megállapításához, hogy melyik sor találkozik a problémával, engedélyezze a hibatűrési funkciót a másolási tevékenységnél, amely további vizsgálat céljából átirányíthatja a problémás sort (ka) t a tárolóba. Dokumentáció: https://docs.microsoft.com/azure/data-factory/copy-activity-fault-tolerance .


### <a name="error-code--sqlunauthorizedaccess"></a>Hibakód: SqlUnauthorizedAccess

- **Üzenet**: `Cannot connect to '%connectorName;'. Detail Message: '%message;'`

- **OK**: a hitelesítő adatok helytelenek, vagy a bejelentkezési fiók nem fér hozzá SQL Databasehoz.

- **Javaslat**: Győződjön meg arról, hogy a bejelentkezési fiók rendelkezik a SQL Database eléréséhez szükséges engedéllyel.


### <a name="error-code--sqlopenconnectiontimeout"></a>Hibakód: SqlOpenConnectionTimeout

- **Üzenet**: `Open connection to database timeout after '%timeoutValue;' seconds.`

- **OK**: lehetséges, hogy SQL Database átmeneti hiba történt.

- **Javaslat**: próbálja meg újra frissíteni a társított szolgáltatás kapcsolati karakterláncát nagyobb kapcsolati időtúllépési értékkel.


### <a name="error-code--sqlautocreatetabletypemapfailed"></a>Hibakód: SqlAutoCreateTableTypeMapFailed

- **Üzenet**: `Type '%dataType;' in source side cannot be mapped to a type that supported by sink side(column name:'%columnName;') in autocreate table.`

- **OK**: az automatikus létrehozási tábla nem tudja kielégíteni a forrás követelményét.

- **Javaslat**: frissítse az oszlop típusát a hozzárendelések területen, vagy manuálisan hozza létre a fogadó táblát a célkiszolgálón.


### <a name="error-code--sqldatatypenotsupported"></a>Hibakód: SqlDataTypeNotSupported

- **Üzenet**: `A database operation failed. Check the SQL errors.`

- **OK**: Ha a probléma az SQL-forráson történik, és a hiba a SqlDateTime túlcsorduláshoz kapcsolódik, az adatérték a logikai típus tartományán (1/1/1753 12:00:00 – 12/31/9999 11:59:59 PM) van.

- **Javaslat**: a típust írja a forrás SQL-lekérdezésbe, vagy a másolási tevékenység oszlopban módosítsa az oszlop típusát "string" értékre.

- **OK**: Ha a probléma az SQL-tárolón történik, és a hiba a SqlDateTime túlcsorduláshoz kapcsolódik, az adatérték a fogadó tábla megengedett tartományán kívül esik.

- **Javaslat**: a megfelelő oszlop típusának frissítése a fogadó táblában lévő "datetime2" típusra.


### <a name="error-code--sqlinvaliddbstoredprocedure"></a>Hibakód: SqlInvalidDbStoredProcedure

- **Üzenet**: `The specified Stored Procedure is not valid. It could be caused by that the stored procedure doesn't return any data. Invalid Stored Procedure script: '%scriptName;'.`

- **OK**: a megadott tárolt eljárás érvénytelen. Ennek oka az lehet, hogy a tárolt eljárás nem ad vissza semmilyen adatforrást.

- **Javaslat**: Ellenőrizze a tárolt eljárást az SQL-eszközök alapján. Győződjön meg arról, hogy a tárolt eljárás képes visszaadni az adatvesztést.


### <a name="error-code--sqlinvaliddbquerystring"></a>Hibakód: SqlInvalidDbQueryString

- **Üzenet**: `The specified SQL Query is not valid. It could be caused by that the query doesn't return any data. Invalid query: '%query;'`

- **OK**: a megadott SQL-lekérdezés érvénytelen. Ennek oka az lehet, hogy a lekérdezés nem ad vissza semmilyen adatforrást.

- **Javaslat**: az SQL-lekérdezés ÉRVÉNYESÍTÉSe SQL-eszközök alapján. Győződjön meg arról, hogy a lekérdezés képes visszaadni az adatkérést.


### <a name="error-code--sqlinvalidcolumnname"></a>Hibakód: SqlInvalidColumnName

- **Üzenet**: `Column '%column;' does not exist in the table '%tableName;', ServerName: '%serverName;', DatabaseName: '%dbName;'.`

- **OK**: az oszlop nem található. A lehetséges konfiguráció helytelen.

- **Javaslat**: Ellenőrizze a lekérdezésben szereplő oszlopot, a "Structure" objektumot és a "leképezéseket" a tevékenységben.


### <a name="error-code--sqlbatchwritetimeout"></a>Hibakód: SqlBatchWriteTimeout

- **Üzenet**: `Timeouts in SQL write operation.`

- **OK**: lehetséges, hogy SQL Database átmeneti hiba történt.

- **Javaslat**: próbálkozzon újra. Ha probléma Reprodukálási, forduljon az Azure SQL támogatási szolgálatához.


### <a name="error-code--sqlbatchwritetransactionfailed"></a>Hibakód: SqlBatchWriteTransactionFailed

- **Üzenet**: `SQL transaction commits failed`

- **OK**: Ha a kivétel részletei folyamatosan jelzik a tranzakció időtúllépését, az integrációs modul és az adatbázis közötti hálózati késés az alapértelmezett küszöbértéknél nagyobb, mint 30 másodperc.

- **Javaslat**: az SQL társított szolgáltatás kapcsolati karakterláncának frissítése a "kapcsolat időtúllépése" értékkel egyenlő, mint 120 vagy magasabb, majd futtassa újra a tevékenységet.

- **OK**: Ha a kivétel részletei időnként megszakadnak a SqlConnection, akkor csak átmeneti hálózati hiba vagy SQL Databasei oldal probléma

- **Javaslat**: próbálkozzon újra a tevékenységgel, és tekintse át SQL Database oldal metrikáit.


### <a name="error-code--sqlbulkcopyinvalidcolumnlength"></a>Hibakód: SqlBulkCopyInvalidColumnLength

- **Üzenet**: `SQL Bulk Copy failed due to receive an invalid column length from the bcp client.`

- **OK**: az SQL tömeges másolása nem sikerült, mert a BCP-ügyfél érvénytelen hosszúságú oszlopot kapott.

- **Javaslat**: annak megállapításához, hogy melyik sor találkozik a problémával, engedélyezze a hibatűrési funkciót a másolási tevékenységnél, amely további vizsgálat céljából átirányíthatja a problémás sort (ka) t a tárolóba. Dokumentáció: https://docs.microsoft.com/azure/data-factory/copy-activity-fault-tolerance .


### <a name="error-code--sqlconnectionisclosed"></a>Hibakód: SqlConnectionIsClosed

- **Üzenet**: `The connection is closed by SQL Database.`

- **OK**: az SQL-alapú kapcsolatok SQL Database lezárult, ha a nagy egyidejű Futtatás és a kiszolgáló leáll.

- **Javaslat**: a távoli kiszolgáló lezárta az SQL-kapcsolatokat. Próbálja megismételni. Ha probléma Reprodukálási, forduljon az Azure SQL támogatási szolgálatához.


### <a name="error-message-conversion-failed-when-converting-from-a-character-string-to-uniqueidentifier"></a>Hibaüzenet: nem sikerült az átalakítás a karakterláncról uniqueidentifier való átalakításkor

- **Tünetek**: Ha táblázatos adatforrásból (például SQL Server) másol adatokat az Azure szinapszis Analytics-be a szakaszos másolással és a kiindulással, a következő hibaüzenetet kapta:

    ```
    ErrorCode=FailedDbOperation,Type=Microsoft.DataTransfer.Common.Shared.HybridDeliveryException,
    Message=Error happened when loading data into Azure Synapse Analytics.,
    Source=Microsoft.DataTransfer.ClientLibrary,Type=System.Data.SqlClient.SqlException,
    Message=Conversion failed when converting from a character string to uniqueidentifier...
    ```

- **OK**: az Azure szinapszis Analytics-alapú albase nem tud üres karakterláncot konvertálni GUID-ra.

- **Megoldás**: a másolási tevékenység fogadójában, a Alapszintű beállítások területen állítsa a "**alapértelmezett típus használata**" beállítást hamis értékre.


### <a name="error-message-expected-data-type-decimalxx-offending-value"></a>Hibaüzenet: várt adattípus: decimális (x, x), jogsértő érték

- **Tünetek**: Ha táblázatos adatforrásból (például SQL Server) másol adatokat az Azure szinapszis Analytics-be a szakaszos másolással és a kiindulással, a következő hibaüzenetet kapta:

    ```
    ErrorCode=FailedDbOperation,Type=Microsoft.DataTransfer.Common.Shared.HybridDeliveryException,
    Message=Error happened when loading data into Azure Synapse Analytics.,
    Source=Microsoft.DataTransfer.ClientLibrary,Type=System.Data.SqlClient.SqlException,
    Message=Query aborted-- the maximum reject threshold (0 rows) was reached while reading from an external source: 1 rows rejected out of total 415 rows processed. (/file_name.txt) 
    Column ordinal: 18, Expected data type: DECIMAL(x,x), Offending value:..
    ```

- **OK**: az Azure szinapszis Analytics-alapú albase nem tud üres sztringet (null értéket) beszúrni decimális oszlopba.

- **Megoldás**: a másolási tevékenység fogadójában, a Alapszintű beállítások területen állítsa a "**alapértelmezett típus használata**" beállítást hamis értékre.


### <a name="error-message-java-exception-message-hdfsbridgecreaterecordreader"></a>Hibaüzenet: Java-kivételt jelző üzenet: HdfsBridge:: CreateRecordReader

- **Tünetek**: az adatok az Azure szinapszis analyticsbe való másolását a Base használatával végezheti el, és megtalálhatja a következő hibát:

    ```
    Message=110802;An internal DMS error occurred that caused this operation to fail. 
    Details: Exception: Microsoft.SqlServer.DataWarehouse.DataMovement.Common.ExternalAccess.HdfsAccessException, 
    Message: Java exception raised on call to HdfsBridge_CreateRecordReader. 
    Java exception message:HdfsBridge::CreateRecordReader - Unexpected error encountered creating the record reader.: Error [HdfsBridge::CreateRecordReader - Unexpected error encountered creating the record reader.] occurred while accessing external file.....
    ```

- **OK**: a lehetséges ok az, hogy a séma (az oszlopszélesség teljes szélessége) túl nagy (1 MB-nál nagyobb). Az összes oszlop méretének hozzáadásával keresse meg a cél Azure szinapszis Analytics-táblázat sémáját:

    - Int-> 4 bájt
    - Bigint – > 8 bájt
    - Varchar (n), char (n), Binary (n), varbinary (n) – > n bájt
    - Nvarchar (n), nchar (n) – > n * 2 bájt
    - Dátum – > 6 bájt
    - Datetime/(2), idő adattípusúra – > 16 bájt
    - DateTimeOffset – > 20 bájt
    - Decimális > 19 bájt
    - Float-> 8 bájt
    - Pénz – > 8 bájt
    - Túlcsordulási – > 4 bájt
    - Valós > 4 bájt
    - Smallint – > 2 bájt
    - Idő – > 12 bájt
    - Tinyint – > 1 bájt

- **Megoldás**: 
    - Csökkentse az oszlopszélességet 1 MB-nál kisebb értékre.
    - Másik lehetőségként használja a tömeges beszúrást a PolyBase letiltásával.


### <a name="error-message-the-condition-specified-using-http-conditional-headers-is-not-met"></a>Hibaüzenet: a HTTP feltételes fejléc (ek) használatával megadott feltétel nem teljesül.

- **Tünetek**: SQL-lekérdezést használ az adatok Azure szinapszis Analyticsből való lekéréséhez, és a következő hiba miatt:

    ```
    ...StorageException: The condition specified using HTTP conditional header(s) is not met...
    ```

- **OK**: az Azure szinapszis Analytics a külső tábla Azure Storage szolgáltatásban való lekérdezését találta.

- **Megoldás**: futtassa ugyanazt a LEKÉRDEZÉST a SSMS, és ellenőrizze, hogy ugyanazt az eredményt látja-e. Ha igen, nyisson meg egy támogatási jegyet az Azure szinapszis Analytics szolgáltatáshoz, és adja meg az Azure szinapszis Analytics-kiszolgáló és az adatbázis nevét a további hibák megoldásához.


### <a name="performance-tier-is-low-and-leads-to-copy-failure"></a>A teljesítményszint alacsony, és sikertelen másolást eredményez

- **Tünetek**: az alábbi hibaüzenet jelenik meg, amikor az adatok másolása Azure SQL Databaseba történt: `Database operation failed. Error message from database execution : ExecuteNonQuery requires an open and available Connection. The connection's current state is closed.`

- **OK**: Azure SQL Database S1 használatos, amely ilyen esetben az IO-korlátokat érinti.

- **Megoldás**: frissítse a Azure SQL Database teljesítményszint a probléma megoldásához. 


### <a name="sql-table-cannot-be-found"></a>Az SQL-tábla nem található 

- **Tünetek**: hiba történt az adatok hibridről helyszíni SQL Server táblába való másolása során:`Cannot find the object "dbo.Contoso" because it does not exist or you do not have permissions.`

- **OK**: az aktuális SQL-fiók nem rendelkezik megfelelő engedélyekkel a .net SqlBulkCopy. WriteToServer által kiadott kérelmek végrehajtásához.

- **Megoldás**: váltson magasabb jogosultsági szintű SQL-fiókra.


### <a name="error-message-string-or-binary-data-would-be-truncated"></a>Hibaüzenet: a karakterlánc vagy bináris adatértékek csonkítva lesznek

- **Tünetek**: hiba történt az adatok helyszíni vagy Azure SQL Server-táblába való másolása során: 

- **OK**: az SQL-tábla sémájának definíciójában egy vagy több olyan oszlop van, amelynek kevesebb a várakozási ideje.

- **Megoldás**: próbálkozzon a következő lépésekkel a probléma megoldásához:

    1. Alkalmazza az SQL-fogadó [hibatűrését](./copy-activity-fault-tolerance.md), különösen a "redirectIncompatibleRowSettings", hogy elhárítsa, mely sorokban van a probléma.

        > [!NOTE]
        > Vegye észre, hogy a hibatűréshez további végrehajtási idő is vezethet, ami magasabb költségeket eredményezhet.

    2. Az átirányított és az SQL Table-séma oszlop hosszával ellenőrizze, hogy melyik oszlop (oka) t kell frissíteni.

    3. A tábla sémájának frissítése ennek megfelelően.


## <a name="azure-table-storage"></a>Azure Table Storage

### <a name="error-code--azuretableduplicatecolumnsfromsource"></a>Hibakód: AzureTableDuplicateColumnsFromSource

- **Üzenet**: `Duplicate columns with same name '%name;' are detected from source. This is NOT supported by Azure Table Storage sink`

- **OK**: gyakori lehet a JOIN vagy strukturálatlan CSV-fájlokat tartalmazó SQL-lekérdezéshez

- **Javaslat**: Ellenőrizze a forrás oszlopokat, és ennek megfelelően javítsa ki azokat.


## <a name="db2"></a>DB2

### <a name="error-code--db2driverrunfailed"></a>Hibakód: DB2DriverRunFailed

- **Üzenet**: `Error thrown from driver. Sql code: '%code;'`

- **OK**: Ha a hibaüzenetben a "SQLSTATE = 51002 SQLCODE =-805" szerepel, tekintse meg a jelen dokumentum tippjét: https://docs.microsoft.com/azure/data-factory/connector-db2#linked-service-properties

- **Javaslat**: próbálja meg a "NULLID" beállítását a "packageCollection" tulajdonságban


## <a name="delimited-text-format"></a>Tagolt szöveg formátuma

### <a name="error-code--delimitedtextcolumnnamenotallownull"></a>Hibakód: DelimitedTextColumnNameNotAllowNull

- **Üzenet**: `The name of column index %index; is empty. Make sure column name is properly specified in the header row.`

- **OK**: a "firstRowAsHeader" tevékenységben való beállításakor az első sor lesz az oszlop neve. Ez a hiba azt jelenti, hogy az első sor üres értéket tartalmaz. Például: ' columna, ColumnB '.

- **Javaslat**: Ellenőrizze az első sort, és javítsa ki az értéket, ha üres érték van megadva.


### <a name="error-code--delimitedtextmorecolumnsthandefined"></a>Hibakód: DelimitedTextMoreColumnsThanDefined

- **Üzenet**: `Error found when processing '%function;' source '%name;' with row number %rowCount;: found more columns than expected column count: %expectedColumnCount;.`

- **OK**: a problémás sor oszlopainak száma nagyobb, mint az első sor oszlopainak száma. Ennek oka az lehet, hogy az adatprobléma vagy a helytelen oszlop-határolójel/quota karakteres beállítások szerepelnek.

- **Javaslat**: a sorok számának lekérése a hibaüzenetben, tekintse meg a sor oszlopát, és javítsa ki az adattípust.

- **OK**: Ha a várt oszlopok száma "1" a hibaüzenetben, lehet, hogy helytelen tömörítési vagy formázási beállításokat adott meg. Így az ADF helytelenül elemezte a fájl (oka) t.

- **Javaslat**: Ellenőrizze a formátum beállításait, és győződjön meg arról, hogy az megfelel a forrásfájl (ok) nak.

- **OK**: Ha a forrás egy mappa, lehetséges, hogy a megadott mappában található fájlok különböző sémával rendelkeznek.

- **Javaslat**: Győződjön meg arról, hogy a megadott mappában található fájlok azonos sémával rendelkeznek.


## <a name="dynamics-365common-data-servicedynamics-crm"></a>Dynamics 365/Common Data Service/Dynamics CRM

### <a name="error-code--dynamicscreateserviceclienterror"></a>Hibakód: DynamicsCreateServiceClientError

- **Üzenet**: `This is a transient issue on dynamics server side. Try to rerun the pipeline.`

- **OK**: ez egy átmeneti probléma a Dynamics Server oldalán.

- **Javaslat**: futtassa újra a folyamatot. Ha nem sikerül, próbálja meg csökkenteni a párhuzamosságot. Ha továbbra sem sikerül, forduljon a Dynamics ügyfélszolgálatához.


### <a name="columns-are-missing-when-previewingimporting-schema"></a>Az oszlopok hiányoznak a séma előnézete/importálása során

- **Tünetek**: egyes oszlopok nem hiányozhatnak a séma importálásakor vagy az adatnézetek megtekintésekor. Hibaüzenet: `The valid structure information (column name and type) are required for Dynamics source.`

- **OK**: Ez a probléma alapvetően a-design, mivel az ADF nem képes olyan oszlopokat megjeleníteni, amelyek nem rendelkeznek értékkel az első 10 rekordban. Győződjön meg arról, hogy a hozzáadott oszlopok formátuma megfelelő. 

- **Javaslat**: manuálisan adja hozzá az oszlopokat a leképezés lapon.


### <a name="error-code--dynamicsmissingtargetformultitargetlookupfield"></a>Hibakód: DynamicsMissingTargetForMultiTargetLookupField

- **Üzenet**: `Cannot find the target column for multi-target lookup field: '%fieldName;'.`

- **OK**: a cél oszlop nem létezik a forrás vagy az oszlop-hozzárendelésben.

- **Javaslat**: 1. Győződjön meg arról, hogy a forrás tartalmazza a cél oszlopot. 2. Adja hozzá a cél oszlopot az oszlop-hozzárendelésben. Győződjön meg arról, hogy a fogadó oszlop a (z) {mezőnév} mintázatú @EntityReference .


### <a name="error-code--dynamicsinvalidtargetformultitargetlookupfield"></a>Hibakód: DynamicsInvalidTargetForMultiTargetLookupField

- **Üzenet**: `The provided target: '%targetName;' is not a valid target of field: '%fieldName;'. Valid targets are: '%validTargetNames;"`

- **OK**: az entitás neve egy többhelyes keresési mező célként megadott entitása.

- **Javaslat**: adjon meg egy érvényes entitás nevét a többhelyes keresési mezőhöz.


### <a name="error-code--dynamicsinvalidtypeformultitargetlookupfield"></a>Hibakód: DynamicsInvalidTypeForMultiTargetLookupField

- **Üzenet**: `The provided target type is not a valid string. Field: '%fieldName;'.`

- **OK**: a TARGET oszlopban lévő érték nem karakterlánc.

- **Javaslat**: adjon meg egy érvényes karakterláncot a többszörös célként szolgáló keresési cél oszlopban.


### <a name="error-code--dynamicsfailedtorequetserver"></a>Hibakód: DynamicsFailedToRequetServer

- **Üzenet**: `The dynamics server or the network is experiencing issues. Check network connectivity or check dynamics server log for more details.`

- **OK**: a Dynamics-kiszolgáló nem stabil vagy nem érhető el, vagy a hálózat problémákba ütközik.

- **Javaslat**: a hálózati kapcsolat ellenőrzése vagy a Dynamics Server naplójának ellenőrzése további részletekért. További segítségért forduljon a Dynamics támogatási szolgálatához.
    

## <a name="ftp"></a>FTP

### <a name="error-code--ftpfailedtoconnecttoftpserver"></a>Hibakód: FtpFailedToConnectToFtpServer

- **Üzenet**: `Failed to connect to FTP server. Please make sure the provided server informantion is correct, and try again.`

- **OK**: lehet, hogy helytelen társított szolgáltatástípus van használatban az FTP-kiszolgálóhoz, például az SFTP társított szolgáltatás használatával csatlakozik egy FTP-kiszolgálóhoz.

- **Javaslat**: keresse meg a célkiszolgáló portját. Alapértelmezés szerint az FTP a 21-es portot használja.


## <a name="http"></a>Http

### <a name="error-code--httpfilefailedtoread"></a>Hibakód: HttpFileFailedToRead

- **Üzenet**: `Failed to read data from http server. Check the error from http server：%message;`

- **OK**: Ez a hiba akkor fordul elő, ha Azure Data Factory kommunikál a http-kiszolgálóval, de a HTTP-kérelem művelete meghiúsul.

- **Javaslat**: a http-állapotkód \ üzenetben keresse meg a hibaüzenetet, és javítsa ki a távoli kiszolgáló hibáját.


## <a name="oracle"></a>Oracle

### <a name="error-code-argumentoutofrangeexception"></a>Hibakód: ArgumentOutOfRangeException

- **Üzenet**: `Hour, Minute, and Second parameters describe an un-representable DateTime.`

- **OK**: Az ADF-ben a DateTime értékek a 0001-01-01 00:00:00 és 9999-12-31 23:59:59 közötti tartományban támogatottak. Az Oracle azonban támogatja a DateTime érték szélesebb tartományát (például a BC Century vagy a min/mp>59), ami az ADF hibája miatt meghiúsul.

- **Javaslat**: 

    A futtatásával `select dump(<column name>)` ellenőrizze, hogy az Oracle értéke az ADF tartományában van-e. 

    Ha tudni szeretné, hogy milyen bájtos sorozatot ad meg az eredmény, ellenőrizze a következőt: https://stackoverflow.com/questions/13568193/how-are-dates-stored-in-oracle .


## <a name="orc-format"></a>Ork formátum

### <a name="error-code--orcjavainvocationexception"></a>Hibakód: OrcJavaInvocationException

- **Üzenet**: `An error occurred when invoking java, message: %javaException;.`

- **OK**: Ha a hibaüzenetben a "Java. lang. OutOfMemory", a "Java heap Space" és a "doubleCapacity" szerepel, ez általában az integrációs modul régi verziójában található memória-kezelési probléma.

- **Javaslat**: Ha saját üzemeltetésű Integration Runtime használ, javasoljuk, hogy frissítsen a legújabb verzióra.

- **OK**: Ha a hibaüzenetben a "Java. lang. OutOfMemory" szerepel, az Integration Runtime nem rendelkezik elegendő erőforrással a fájl (ok) feldolgozásához.

- **Javaslat**: az egyidejű futtatások korlátozása az Integration Runtime-ban. A saját üzemeltetésű Integration Runtime esetében akár 8 GB-nál nagyobb memóriával, akár egy olyan, nagy teljesítményű géppel is méretezhető.

- **OK**: Ha a hibaüzenetben a "NullPointerReference" szöveg szerepel, ez egy átmeneti hiba.

- **Javaslat**: próbálkozzon újra. Ha a probléma továbbra is fennáll, forduljon az ügyfélszolgálathoz.

- **OK**: Ha a hibaüzenetben a "BufferOverflowException" szöveg szerepel, ez egy átmeneti hiba.

- **Javaslat**: próbálkozzon újra. Ha a probléma továbbra is fennáll, forduljon az ügyfélszolgálathoz.

- **OK**: Ha a hibaüzenetben a "Java. lang. ClassCastException:org. Apache. Hadoop. kaptár. serde2. IO. HiveCharWritable nem lehet a org. Apache. Hadoop. IO. Text" szövegre váltani, ez a típus átalakítási probléma a Java futtatókörnyezetben. A forrásadatok oka általában nem kezelhető megfelelően a Java-futtatókörnyezetben.

- **Javaslat**: ez az adatprobléma. A karakter/varchar helyett használja a sztringet az ork-formátumokban.

### <a name="error-code--orcdatetimeexceedlimit"></a>Hibakód: OrcDateTimeExceedLimit

- **Üzenet**: `The Ticks value '%ticks;' for the datetime column must be between valid datetime ticks range -621355968000000000 and 2534022144000000000.`

- **OK**: Ha a DateTime érték "0001-01-01 00:00:00", a Julián-naptár és a Gergely-naptár közötti különbség okozhatja. https://en.wikipedia.org/wiki/Proleptic_Gregorian_calendar#Difference_between_Julian_and_proleptic_Gregorian_calendar_dates.

- **Javaslat**: a kullancsok értékének megadásával és a "0001-01-01 00:00:00" datetime érték használatának elkerülésével.


## <a name="parquet-format"></a>Parketta formátuma

### <a name="error-code--parquetjavainvocationexception"></a>Hibakód: ParquetJavaInvocationException

- **Üzenet**: `An error occurred when invoking java, message: %javaException;.`

- **OK**: Ha a hibaüzenetben a "Java. lang. OutOfMemory", a "Java heap Space" és a "doubleCapacity" szerepel, ez általában az integrációs modul régi verziójában található memória-kezelési probléma.

- **Javaslat**: Ha saját üzemeltetésű Integration Runtime használ, és a verzió korábbi, mint a 3.20.7159.1, javasoljuk, hogy frissítsen a legújabb verzióra.

- **OK**: Ha a hibaüzenetben a "Java. lang. OutOfMemory" szerepel, az Integration Runtime nem rendelkezik elegendő erőforrással a fájl (ok) feldolgozásához.

- **Javaslat**: az egyidejű futtatások korlátozása az Integration Runtime-ban. A saját üzemeltetésű Integration Runtime esetében akár 8 GB-nál nagyobb memóriával, akár egy olyan, nagy teljesítményű géppel is méretezhető.

- **OK**: Ha a hibaüzenetben a "NullPointerReference" szöveg szerepel, ez egy átmeneti hiba.

- **Javaslat**: próbálkozzon újra. Ha a probléma továbbra is fennáll, forduljon az ügyfélszolgálathoz.


### <a name="error-code--parquetinvalidfile"></a>Hibakód: ParquetInvalidFile

- **Üzenet**: `File is not a valid Parquet file.`

- **OK**: a parketta-fájl hibája.

- **Javaslat**: Győződjön meg arról, hogy a bemenet érvényes parketta-fájl.


### <a name="error-code--parquetnotsupportedtype"></a>Hibakód: ParquetNotSupportedType

- **Üzenet**: `Unsupported Parquet type. PrimitiveType: %primitiveType; OriginalType: %originalType;.`

- **OK**: a Azure Data Factory nem támogatja a parketta formátumát.

- **Javaslat**: Ellenőrizze a forrásadatokat. Tekintse meg a doc: https://docs.microsoft.com/azure/data-factory/supported-file-formats-and-compression-codecs .


### <a name="error-code--parquetmisseddecimalprecisionscale"></a>Hibakód: ParquetMissedDecimalPrecisionScale

- **Üzenet**: `Decimal Precision or Scale information is not found in schema for column: %column;.`

- **OK**: próbálja meg elemezni a pontosságot és a skálázást, de nincs ilyen adat megadva.

- **Javaslat**: a forrás nem ad vissza pontos pontosságot és méretezést. A probléma oszlopban keresse meg a pontosságot és a méretezést.


### <a name="error-code--parquetinvaliddecimalprecisionscale"></a>Hibakód: ParquetInvalidDecimalPrecisionScale

- **Üzenet**: `Invalid Decimal Precision or Scale. Precision: %precision; Scale: %scale;.`

- **OK**: a séma érvénytelen.

- **Javaslat**: a probléma oszlopban keresse meg a pontosságot és a méretezést.


### <a name="error-code--parquetcolumnnotfound"></a>Hibakód: ParquetColumnNotFound

- **Üzenet**: `Column %column; does not exist in Parquet file.`

- **OK**: a forrás sémája nem egyezik a fogadó sémával.

- **Javaslat**: a "tevékenység" the'mappings megkeresése. Győződjön meg arról, hogy a forrás oszlop képezhető le a megfelelő fogadó oszlopra.


### <a name="error-code--parquetinvaliddataformat"></a>Hibakód: ParquetInvalidDataFormat

- **Üzenet**: `Incorrect format of %srcValue; for converting to %dstType;.`

- **OK**: az adattípusok nem alakíthatók át a leképezésekben megadott típusra. forrás

- **Javaslat**: Ellenőrizze a forrásadatokat, vagy adja meg a megfelelő adattípust ehhez az oszlophoz a másolási tevékenység oszlop-hozzárendelésében. Tekintse meg a doc: https://docs.microsoft.com/azure/data-factory/supported-file-formats-and-compression-codecs .


### <a name="error-code--parquetdatacountnotmatchcolumncount"></a>Hibakód: ParquetDataCountNotMatchColumnCount

- **Üzenet**: `The data count in a row '%sourceColumnCount;' does not match the column count '%sinkColumnCount;' in given schema.`

- **OK**: a forrás oszlopainak száma és a fogadó oszlopainak száma nem egyezik

- **Javaslat**: a dupla ellenőrzési forrás oszlopainak száma ugyanaz, mint a "leképezés" fogadó oszlopainak száma.


### <a name="error-code--parquetdatatypenotmatchcolumntype"></a>Hibakód: ParquetDataTypeNotMatchColumnType

- **Üzenet**: a (z)% srcType; adattípus nem felel meg a megadott típusú oszlopnak (% dstType;). a (z)% columnIndex; oszlopban.

- **OK**: a forrásból származó adatok nem alakíthatók át a fogadóban definiált típusra

- **Javaslat**: adjon meg egy megfelelő típust a Mapping. mosogatóban.


### <a name="error-code--parquetbridgeinvaliddata"></a>Hibakód: ParquetBridgeInvalidData

- **Üzenet**: `%message;`

- **OK**: az adatérték a korlátozáson felül

- **Javaslat**: próbálkozzon újra. Ha a probléma továbbra is fennáll, vegye fel velünk a kapcsolatot.


### <a name="error-code--parquetunsupportedinterpretation"></a>Hibakód: ParquetUnsupportedInterpretation

- **Üzenet**: `The given interpretation '%interpretation;' of Parquet format is not supported.`

- **OK**: nem támogatott forgatókönyv

- **Javaslat**: a "ParquetInterpretFor" nem lehet "sparkSql".


### <a name="error-code--parquetunsupportfilelevelcompressionoption"></a>Hibakód: ParquetUnsupportFileLevelCompressionOption

- **Üzenet**: `File level compression is not supported for Parquet.`

- **OK**: nem támogatott forgatókönyv

- **Javaslat**: távolítsa el a fájba a hasznos adatok között.


### <a name="error-code--usererrorjniexception"></a>Hibakód: UserErrorJniException

- **Üzenet**: `Cannot create JVM: JNI return code [-6][JNI call failed: Invalid arguments.]`

- **OK**: a JVM nem hozható létre, mert néhány tiltott (globális) argumentum be van állítva.

- **Javaslat**: Jelentkezzen be arra a gépre, amelyen a saját üzemeltetésű integrációs modul **egyes csomópontjai** futnak. Ellenőrizze, hogy a rendszerváltozó helyesen van-e beállítva, például: `_JAVA_OPTIONS "-Xms256m -Xmx16g" with memory bigger than 8 G` . Indítsa újra az összes IR-csomópontot, majd futtassa újra a folyamatot.


### <a name="arithmetic-overflow"></a>Aritmetikai túlcsordulás

- **Tünetek**: a Parquet-fájlok másolása során hibaüzenet jelenik meg: `Message = Arithmetic Overflow., Source = Microsoft.DataTransfer.Common`

- **OK**: jelenleg csak a pontossági <= 38, és a <= 20 egész szám hosszúsága támogatott, ha az Oracle-ből a parkettára másolja a fájlokat. 

- **Megoldás**: az ilyen problémákkal rendelkező oszlopokat megkerülő megoldásként konvertálhatja a VARCHAR2.


### <a name="no-enum-constant"></a>Nem állandó Enum

- **Tünetek**: hiba történt az adatok parketta formátumba való másolása során: `java.lang.IllegalArgumentException:field ended by &apos;;&apos;` , vagy: `java.lang.IllegalArgumentException:No enum constant org.apache.parquet.schema.OriginalType.test` .

- **OK**: 

    A problémát a szóközök vagy a nem támogatott karakterek (például:;) okozhatja. {} () \n\t =) az oszlopnév esetében, mivel a parketta nem támogatja az ilyen formátumot. 

    Például a *contoso (test)* nevű oszlopnév a [kód](https://github.com/apache/parquet-mr/blob/master/parquet-column/src/main/java/org/apache/parquet/schema/MessageTypeParser.java) alapján zárójelben fogja elemezni a típust `Tokenizer st = new Tokenizer(schemaString, " ;{}()\n\t");` . A hiba akkor jelenik meg, ha nincs ilyen "teszt" típus.

    A támogatott típusok ellenőrzését [itt](https://github.com/apache/parquet-mr/blob/master/parquet-column/src/main/java/org/apache/parquet/schema/OriginalType.java)tekintheti meg.

- **Megoldás**: 

    - Ellenőrizze, hogy vannak-e szóközök a fogadó oszlop nevében.

    - Ellenőrizze, hogy az első sor szóközökkel van-e felhasználva oszlopnév néven.

    - Ellenőrizze, hogy a OriginalType típusa támogatott-e. Próbálja meg elkerülni ezeket a speciális szimbólumokat `,;{}()\n\t=` . 


## <a name="rest"></a>REST

### <a name="error-code--restsinkcallfailed"></a>Hibakód: RestSinkCallFailed

- **Üzenet**: `Rest Endpoint responded with Failure from server. Check the error from server:%message;`

- **OK**: Ez a hiba akkor fordul elő, amikor a Azure Data Factory a REST-végpontot HTTP protokollon keresztül kommunikál, és a kérés művelete meghiúsul.

- **Javaslat**: a http-állapotkód \ üzenetben keresse meg a hibaüzenetet, és javítsa ki a távoli kiszolgáló hibáját.

### <a name="unexpected-network-response-from-rest-connector"></a>Nem várt hálózati válasz a REST-összekötőtől

- **Tünetek**: a végpont időnként váratlan választ kap (400/401/403/500) a REST-összekötőből.

- **OK**: a REST forrás-összekötő URL-címet és http-metódust/fejlécet/törzset használ a társított szolgáltatás/adatkészlet/másolás forrása paraméterként a HTTP-kérések létrehozásakor. A problémát valószínűleg egy vagy több megadott paraméter hibája okozza.

- **Megoldás**: 
    - A cmd ablakban a "curl" használatával ellenőrizze, hogy az OK vagy a nem (**elfogadva** és **felhasználói ügynök** fejlécei mindig szerepeljenek-e):
        ```
        curl -i -X <HTTP method> -H <HTTP header1> -H <HTTP header2> -H "Accept: application/json" -H "User-Agent: azure-data-factory/2.0" -d '<HTTP body>' <URL>
        ```
      Ha a parancs ugyanazt a váratlan választ adja vissza, javítsa a fenti paramétereket a "curl" értékkel, amíg vissza nem adja a várt választ. 

      Emellett használhatja a "curl--help" parancsot is a parancs fejlettebb használatához.

    - Ha csak az ADF REST-összekötő váratlan választ ad vissza, további hibaelhárításért forduljon a Microsoft támogatási szolgálatához.
    
    - Vegye figyelembe, hogy a "curl" nem lehet megfelelő az SSL-tanúsítvány érvényesítési hibájának reprodukálása érdekében. Bizonyos esetekben a "curl" parancs végrehajtása nem sikerült az SSL-tanúsítvány érvényesítési problémáinak elvégzése nélkül. Ha azonban ugyanazt az URL-címet a böngészőben hajtja végre, akkor a rendszer nem ad vissza SSL-tanúsítványt az első helyen az ügyfél számára a kiszolgálóval való megbízhatósági kapcsolat létrehozásához.

      A fenti esethez ajánlott eszközök, például a **Poster** és a **Hegedűs** .


## <a name="sftp"></a>SFTP

#### <a name="error-code--sftpoperationfail"></a>Hibakód: SftpOperationFail

- **Üzenet**: `Failed to '%operation;'. Check detailed error from SFTP.`

- **OK**: az SFTP-művelet találatot észlelt.

- **Javaslat**: az SFTP részletes hibáinak keresése.


### <a name="error-code--sftprenameoperationfail"></a>Hibakód: SftpRenameOperationFail

- **Üzenet**: `Failed to rename the temp file. Your SFTP server doesn't support renaming temp file, please set "useTempFileRename" as false in copy sink to disable uploading to temp file.`

- **OK**: az SFTP-kiszolgáló nem támogatja az ideiglenes fájl átnevezését.

- **Javaslat**: állítsa a "useTempFileRename" értéket hamis értékre a másolási gyűjtőben a temp fájlra való feltöltés letiltásához.


### <a name="error-code--sftpinvalidsftpcredential"></a>Hibakód: SftpInvalidSftpCredential

- **Üzenet**: `Invalid Sftp credential provided for '%type;' authentication type.`

- **OK**: a titkos kulcs tartalmának beolvasása a AKV/SDK-ból történik, de helytelenül van kódolva.

- **Javaslat**:  

    Ha a titkos kulcs tartalma a AKV-ből származik, és az eredeti kulcsfájl képes működni, ha az ügyfél közvetlenül az SFTP-hez társított szolgáltatáshoz tölti fel.

    Tekintse meg https://docs.microsoft.com/azure/data-factory/connector-sftp#using-ssh-public-key-authentication a privateKey tartalma Base64 kódolású titkos SSH-kulcs tartalmát.

    Kódolja **az eredeti titkos kulcsfájl teljes tartalmát** Base64 kódolással, és tárolja a kódolt karakterláncot a AKV. Az eredeti titkos kulcsfájl az, amely az SFTP-hez társított szolgáltatáson tud működni, ha a feltöltés fájlból lehetőségre kattint.

    Íme néhány példa a sztring generálásához:

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

    - Harmadik féltől származó Base64 átalakító eszköz használata

        Hasonló eszközök https://www.base64encode.org/ használata ajánlott.

- **OK**: helytelen a kulcs tartalmának formátuma.

- **Javaslat**:  

    A PKCS # 8 formátum SSH titkos kulcsa (a "-----BEGIN ENCRYPTED PRIVATE KEY-----" kezdetű) jelenleg nem támogatott az SFTP-kiszolgáló az ADF-ben való eléréséhez. 

    Az alábbi parancsok futtatásával alakítsa át a kulcsot a hagyományos SSH-kulcs formátumára (Kezdje a következővel: "-----BEGIN RSA titkos kulcs-----"):

    ```
    openssl pkcs8 -in pkcs8_format_key_file -out traditional_format_key_file
    chmod 600 traditional_format_key_file
    ssh-keygen -f traditional_format_key_file -p
    ```

- **OK**: Érvénytelen a hitelesítő adat vagy a titkos kulcs tartalma

- **Javaslat**: a kettős ellenőrzési eszközökkel, például a megnyert eszközzel ellenőrizze, hogy helyes-e a kulcs fájlja vagy jelszava.

### <a name="sftp-copy-activity-failed"></a>Az SFTP-másolási tevékenység nem sikerült

- **Tünetek**: hibakód: UserErrorInvalidColumnMappingColumnNotFound. Hibaüzenet: `Column &apos;AccMngr&apos; specified in column mapping cannot be found in source data.`

- **OK**: a forrás nem tartalmaz "AccMngr" nevű oszlopot.

- **Megoldás**: Ellenőrizze, hogy az adatkészlet hogyan lett konfigurálva úgy, hogy a cél adatkészlet oszlop hozzárendelésével erősítse meg, hogy van-e ilyen "AccMngr" oszlop.


### <a name="error-code--sftpfailedtoconnecttosftpserver"></a>Hibakód: SftpFailedToConnectToSftpServer

- **Üzenet**: `Failed to connect to Sftp server '%server;'.`

- **OK**: Ha a hibaüzenetben a szoftvercsatorna olvasási művelete 30000 ezredmásodpercet követően túllépte az időkorlátot, az egyik lehetséges ok az, hogy helytelen társított szolgáltatástípus van használatban az SFTP-kiszolgálóhoz, például az FTP-hez társított szolgáltatás az SFTP-kiszolgálóhoz való csatlakozáshoz.

- **Javaslat**: keresse meg a célkiszolgáló portját. Alapértelmezés szerint az SFTP a 22-es portot használja.

- **OK**: Ha a "kiszolgáló válasza nem tartalmazza az ssh-protokoll azonosítását" hibaüzenetet tartalmazza, az egyik lehetséges ok az, hogy az SFTP-kiszolgáló szabályozza a kapcsolatokat. Az ADF-ben több kapcsolat jön létre az SFTP-kiszolgálóról párhuzamosan történő letöltéshez, és néha az SFTP-kiszolgáló szabályozását fogja érinteni. Gyakorlatilag a különböző kiszolgálók eltérő hibát adnak vissza a szabályozás során.

- **Javaslat**:  

    Az SFTP-adatkészlet maximális egyidejű csatlakoztatását állítsa 1 értékre, majd futtassa újra a másolást. Ha sikerrel jár, biztos lehet benne, hogy a szabályozás oka.

    Ha előléptetni szeretné az alacsony átviteli sebességet, forduljon az SFTP rendszergazdájához az egyidejű kapcsolatok számának növeléséhez, vagy adja hozzá az alábbi IP-címet az engedélyezési listához:

    - Ha felügyelt IR-t használ, vegye fel az [Azure Datacenter IP-tartományait](https://www.microsoft.com/download/details.aspx?id=41653).
      Vagy telepítheti a saját üzemeltetésű integrációs modult, ha nem kívánja az IP-címtartományok nagy listáját hozzáadni az SFTP-kiszolgáló engedélyezési listájához.

    - Ha saját üzemeltetésű integrációs modult használ, adja hozzá a számítógép IP-címét, amely telepítette a legördülő listát az engedélyezési listához.


## <a name="sharepoint-online-list"></a>SharePoint Online-lista

### <a name="error-code--sharepointonlineauthfailed"></a>Hibakód: SharePointOnlineAuthFailed

- **Üzenet**: `The access token generated failed, status code: %code;, error message: %message;.`

- **OK**: elképzelhető, hogy az egyszerű szolgáltatásnév és a kulcs helytelenül van beállítva.

- **Javaslat**: a regisztrált alkalmazás (egyszerű szolgáltatásnév) és kulcs ellenőrzése, hogy megfelelően van-e beállítva.


## <a name="xml-format"></a>XML-formátum

### <a name="error-code--xmlsinknotsupported"></a>Hibakód: XmlSinkNotSupported

- **Üzenet**: `Write data in xml format is not supported yet, please choose a different format!`

- **OK**: a másolási tevékenységben XML-adatkészletet használt fogadó adatkészletként

- Javaslat: használjon egy adatkészletet eltérő formátumban másolási **fogadóként**.


### <a name="error-code--xmlattributecolumnnameconflict"></a>Hibakód: XmlAttributeColumnNameConflict

- **Üzenet**: `Column names %attrNames;' for attributes of element '%element;' conflict with that for corresponding child elements, and the attribute prefix used is '%prefix;'.`

- **OK**: az attribútum előtagját használta, ami az ütközést okozta.

- **Javaslat**: állítsa be a "attributePrefix" tulajdonság másik értékét.


### <a name="error-code--xmlvaluecolumnnameconflict"></a>Hibakód: XmlValueColumnNameConflict

- **Üzenet**: `Column name for the value of element '%element;' is '%columnName;' and it conflicts with the child element having the same name.`

- **OK**: az elem értékének az egyik alárendelt elem nevét használta.

- **Javaslat**: állítsa be a "valueColumn" tulajdonság másik értékét.


### <a name="error-code--xmlinvalid"></a>Hibakód: XmlInvalid

- **Üzenet**: `Input XML file '%file;' is invalid with parsing error '%error;'.`

- **OK**: a bemeneti XML-fájl nem megfelelően formázott.

- **Javaslat**: javítsa ki az XML-fájlt, hogy megfelelően legyen formázva.


## <a name="general-copy-activity-error"></a>Általános másolási tevékenység hibája

### <a name="error-code--jrenotfound"></a>Hibakód: JreNotFound

- **Üzenet**: `Java Runtime Environment cannot be found on the Self-hosted Integration Runtime machine. It is required for parsing or writing to Parquet/ORC files. Make sure Java Runtime Environment has been installed on the Self-hosted Integration Runtime machine.`

- **OK**: a saját üzemeltetésű Integration Runtime nem találja a Java futtatókörnyezetet. A Java-futtatókörnyezet szükséges az adott forrás olvasásához.

- **Javaslat**: az Integration Runtime-környezet, a dokumentációs dokumentum: https://docs.microsoft.com/azure/data-factory/format-parquet#using-self-hosted-integration-runtime


### <a name="error-code--wildcardpathsinknotsupported"></a>Hibakód: WildcardPathSinkNotSupported

- **Üzenet**: `Wildcard in path is not supported in sink dataset. Fix the path: '%setting;'.`

- **OK**: a fogadó adatkészlet nem támogatja a helyettesítő karaktert.

- **Javaslat**: keresse meg a fogadó adatkészletet, és javítsa ki az elérési utat helyettesítő érték nélkül.


### <a name="fips-issue"></a>FIPS-probléma

- **Tünetek**: a másolási tevékenység nem sikerül a FIPS-kompatibilis, saját üzemeltetésű Integration Runtime gépen, hibaüzenettel `This implementation is not part of the Windows Platform FIPS validated cryptographic algorithms.` . Ez akkor fordulhat elő, amikor az adatok az Azure Blobtal, az SFTP-val vagy más összekötővel együtt másolhatók.

- **OK**: a FIPS (szövetségi adatfeldolgozási szabványok) meghatározott titkosítási algoritmusokat határoz meg, amelyeket használhat. Ha a FIPS mód engedélyezve van a gépen, bizonyos helyzetekben a másolási tevékenységtől függő titkosítási osztályok blokkolva vannak.

- **Megoldás**: [ebben a cikkben](https://techcommunity.microsoft.com/t5/microsoft-security-baselines/why-we-8217-re-not-recommending-8220-fips-mode-8221-anymore/ba-p/701037)megismerheti a FIPS-es Windows-üzemmód jelenlegi helyzetét, és kiértékelheti, hogy letilthatja-e a FIPS-t a saját üzemeltetésű Integration Runtime gépen.

    Ha azonban csak azt szeretné, hogy Azure Data Factory a FIPS megkerülését, és a tevékenység futtatása sikeres legyen, kövesse az alábbi lépéseket:

    1. Nyissa meg azt a mappát, ahol a saját üzemeltetésű Integration Runtime telepítve van, általában a alatt `C:\Program Files\Microsoft Integration Runtime\<IR version>\Shared` .

    2. Nyissa meg a "diawp.exe.config" szakaszt, és vegye fel `<enforceFIPSPolicy enabled="false"/>` a `<runtime>` szakaszba az alábbihoz hasonló módon.

        ![A FIPS letiltása](./media/connector-troubleshoot-guide/disable-fips-policy.png)

    3. Indítsa újra a saját üzemeltetésű Integration Runtime számítógépet.


## <a name="next-steps"></a>Következő lépések

További hibaelhárítási segítségért próbálja ki ezeket az erőforrásokat:

*  [Data Factory blog](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [Data Factory szolgáltatásra vonatkozó kérelmek](https://feedback.azure.com/forums/270578-data-factory)
*  [Azure-videók](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [Microsoft Q&egy kérdés oldal](/answers/topics/azure-data-factory.html)
*  [Data Factory Stack Overflow fóruma](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [Twitter-információk a Data Factory](https://twitter.com/hashtag/DataFactory)