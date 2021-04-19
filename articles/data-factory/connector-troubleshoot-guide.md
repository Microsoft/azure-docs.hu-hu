---
title: Az Azure Data Factory hibaelhárítása
description: Ismerje meg, hogyan háríthatja el az összekötővel kapcsolatos problémákat a Azure Data Factory.
author: linda33wj
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 04/13/2021
ms.author: jingwang
ms.custom: has-adal-ref
ms.openlocfilehash: 21b5522f07519e9a0c3353cb2463e0ec49063f34
ms.sourcegitcommit: 3ed0f0b1b66a741399dc59df2285546c66d1df38
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/19/2021
ms.locfileid: "107713425"
---
# <a name="troubleshoot-azure-data-factory-connectors"></a>Az Azure Data Factory hibaelhárítása

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Ez a cikk az összekötők gyakori hibaelhárítási Azure Data Factory be.

## <a name="azure-blob-storage"></a>Azure Blob Storage

### <a name="error-code-azurebloboperationfailed"></a>Hibakód: AzureBlobOperationFailed

- **Üzenet:**"A blobművelet nem sikerült. ContainerName: %containerName;, elérési út: %path;."

- **Ok:** Probléma van a Blob Storage művelettel.

- **Javaslat:** A hiba részleteinek ellenőrzéshez tekintse meg a Blob Storage [hibakódokat.](/rest/api/storageservices/blob-service-error-codes) További segítségért forduljon a Blob Storage csapathoz.


### <a name="invalid-property-during-copy-activity"></a>Érvénytelen tulajdonság a másolási tevékenység során

- **Üzenet:**`Copy activity \<Activity Name> has an invalid "source" property. The source type is not compatible with the dataset \<Dataset Name> and its linked service \<Linked Service Name>. Please verify your input against.`

- **Ok:** Az adatkészletben definiált típus inkonzisztens a másolási tevékenységben meghatározott forrás- vagy fogadótípussal.

- **Megoldás: Szerkessze** az adatkészletet vagy a folyamat JSON-definícióját, hogy a típusok konzisztensek legyenek, majd futtassa újra az üzembe helyezést.


## <a name="azure-cosmos-db"></a>Azure Cosmos DB

### <a name="error-message-request-size-is-too-large"></a>Hibaüzenet: A kérelem mérete túl nagy

- **Tünetek:** Amikor adatokat másol Azure Cosmos DB alapértelmezett írási kötegmérettel, a következő hibaüzenet jelenik meg: `Request size is too large.`

- **Ok:** Azure Cosmos DB egy kérelem méretét 2 MB-re korlátozza. A képlet a *kérelem mérete = egydokumentumméretű \* írási kötegméret.* Ha a dokumentum mérete nagy, az alapértelmezett viselkedés túl nagy kérésméretet eredményez. Finomhangolhatja az írási köteg méretét.

- **Megoldás:** A másolási tevékenység  fogadóján csökkentse az írási köteg méretének értékét (az alapértelmezett érték 10000).

### <a name="error-message-unique-index-constraint-violation"></a>Hibaüzenet: Egyedi indexmegkötés megsértése

- **Tünetek:** Amikor adatokat másol a Azure Cosmos DB, a következő hibaüzenet jelenik meg:

    `Message=Partition range id 0 | Failed to import mini-batch. 
    Exception was Message: {"Errors":["Encountered exception while executing function. Exception = Error: {\"Errors\":[\"Unique index constraint violation.\"]}...`

- **Ok:** Két lehetséges oka lehet:

    - 1. ok:  Ha a Beszúrás írási viselkedést használja, ez a hiba azt jelenti, hogy a forrásadatok azonos azonosítójú sorokat vagy objektumokat tartalmaznak.
    - 2. ok: Ha az **Upsert** értéket használja írási viselkedésként, és egy másik egyedi kulcsot ad meg a tárolóhoz, ez a hiba azt jelenti, hogy a forrásadatok különböző azonosítóval és objektumokkal, de a meghatározott egyedi kulcs ugyanazon értékével rendelkezik.

- **Megoldás:** 

    - Az 1. ok érdekében állítsa be az **Upsert írási** viselkedést.
    - A 2. ok érdekében győződjön meg arról, hogy minden dokumentumhoz más érték van megadva a meghatározott egyedi kulcshoz.

### <a name="error-message-request-rate-is-large"></a>Hibaüzenet: A kérelmek sebessége nagy

- **Tünetek:** Amikor adatokat másol a Azure Cosmos DB, a következő hibaüzenet jelenik meg:

    `Type=Microsoft.Azure.Documents.DocumentClientException,
    Message=Message: {"Errors":["Request rate is large"]}`

- **Ok:** A felhasznált kérelemegységek (RU-k) száma nagyobb, mint a Azure Cosmos DB. A kérelemegységek kiszámításának Azure Cosmos DB lásd: Kérelemegységek a [Azure Cosmos DB.](../cosmos-db/request-units.md#request-unit-considerations)

- **Megoldás:** Próbálkozzon az alábbi két megoldás valamelyikével:

    - Növelje a *tároló RU-k* számát egy nagyobb értékre a Azure Cosmos DB. Ez a megoldás javítja a másolási tevékenység teljesítményét, de több költséggel jár a Azure Cosmos DB. 
    - Csökkentse a *writeBatchSize* értékét kisebb értékre, például 1000-re, a *párhuzamos* hatókörökét pedig csökkentse kisebb értékre, például 1-re. Ez a megoldás csökkenti a másolási futtatás teljesítményét, de nem jár több költséggel a Azure Cosmos DB.

### <a name="columns-missing-in-column-mapping"></a>Oszlopok hiányoznak az oszlopleképezésből

- **Tünetek:** Ha egy oszlopleképezéshez Azure Cosmos DB sémát importál, néhány oszlop hiányzik. 

- **Ok:** Data Factory első 10 dokumentum sémáját Azure Cosmos DB ki. Ha egyes dokumentumoszlopok vagy tulajdonságok nem tartalmaznak értékeket, a rendszer nem észleli a sémát Data Factory ezért nem jelenik meg.

- **Megoldás:** A lekérdezést az alábbi kódban látható módon hangolhatja, hogy az oszlopértékek üres értékekkel jelennek meg az eredményhalmazban. Tegyük fel, *hogy az* első 10 dokumentumból hiányzik a lehetetlen oszlop). Másik lehetőségként manuálisan is hozzáadhatja az oszlopot a leképezéshez.

    ```sql
    select c.company, c.category, c.comments, (c.impossible??'') as impossible from c
    ```

### <a name="error-message-the-guidrepresentation-for-the-reader-is-csharplegacy"></a>Hibaüzenet: Az olvasó GuidRepresentation (GuidRepresentation) a CSharpLegacy

- **Tünetek:** Amikor adatokat másol Azure Cosmos DB MongoAPI- vagy MongoDB-fájlból az univerzálisan egyedi azonosító (UUID) mezővel, a következő hibaüzenet jelenik meg:

    `Failed to read data via MongoDB client., 
    Source=Microsoft.DataTransfer.Runtime.MongoDbV2Connector,Type=System.FormatException, 
    Message=The GuidRepresentation for the reader is CSharpLegacy which requires the binary sub type to be UuidLegacy not UuidStandard.,Source=MongoDB.Bson,’“,`

- **Ok:** Az UUID kétféleképpen ábrázolható bináris JSON -ban (BSON): UuidStardard és UuidLegacy. Alapértelmezés szerint az UuidLegacy az adatok olvasására használatos. Ha a MongoDB UUID-adatai UuidStandard, hibaüzenetet kap.

- **Megoldás:** A MongoDB kapcsolati sztringben adja hozzá az *uuidRepresentation=standard* beállítást. További információ: [MongoDB kapcsolati sztring.](connector-mongodb.md#linked-service-properties)
            
## <a name="azure-cosmos-db-sql-api"></a>Azure Cosmos DB (SQL API)

### <a name="error-code-cosmosdbsqlapioperationfailed"></a>Hibakód: CosmosDbSqlApiOperationFailed

- **Üzenet:**`CosmosDbSqlApi operation Failed. ErrorMessage: %msg;.`

- **Ok:** A CosmosDbSqlApi művelettel kapcsolatos probléma.

- **Javaslat:** A hiba részleteinek ellenőrzéshez tekintse meg [Azure Cosmos DB súgódokumentumát.](../cosmos-db/troubleshoot-dot-net-sdk.md) További segítségért forduljon a Azure Cosmos DB csapathoz.

## <a name="azure-data-lake-storage-gen1"></a>1. generációs Azure Data Lake Storage

### <a name="error-message-the-underlying-connection-was-closed-could-not-establish-trust-relationship-for-the-ssltls-secure-channel"></a>Hibaüzenet: A mögöttes kapcsolat megszakadt: Nem sikerült megbízhatósági kapcsolatot létesíteni az SSL/TLS biztonságos csatornához.

- **Tünetek:** Copy tevékenység hiba a következő hibával meghiúsul: 

    `Message: ErrorCode = UserErrorFailedFileOperation, Error Message = The underlying connection was closed: Could not establish trust relationship for the SSL/TLS secure channel.`

- **Ok:** A tanúsítvány érvényesítése sikertelen volt a TLS-kézfogás során.

- **Megoldás:** Áthidaló megoldásként az szakaszos másolat használatával hagyja ki a Transport Layer Security (TLS) érvényesítését a Azure Data Lake Storage Gen1. Reprodukálja a problémát, gyűjtse össze a hálózati monitor (netmon) nyomkövetését, majd a hálózati csapattal ellenőrizze a helyi hálózati konfigurációt.

    ![Hibaelhárítási Azure Data Lake Storage Gen1 kapcsolatok ábrája.](./media/connector-troubleshoot-guide/adls-troubleshoot.png)


### <a name="error-message-the-remote-server-returned-an-error-403-forbidden"></a>Hibaüzenet: A távoli kiszolgáló hibát adott vissza: (403) Tiltott

- **Tünetek:** Copy tevékenység hiba a következő hibával meghiúsul: 

   `Message: The remote server returned an error: (403) Forbidden.   
    Response details: {"RemoteException":{"exception":"AccessControlException""message":"CREATE failed with error 0x83090aa2 (Forbidden. ACL verification failed. Either the resource does not exist or the user is not authorized to perform the requested operation.)....`

- **Ok:** Az egyik lehetséges ok az, hogy a használt szolgáltatásnév vagy felügyelt identitás nem rendelkezik engedéllyel bizonyos mappák vagy fájlok eléréséhez.

- **Megoldás:** Adjon megfelelő engedélyeket a másolni kívánt mappáknak és almappáknak. További információ: [Adatok másolása](connector-azure-data-lake-store.md#linked-service-properties)a Azure Data Lake Storage Gen1 a Azure Data Factory.

### <a name="error-message-failed-to-get-access-token-by-using-service-principal-adal-error-service_unavailable"></a>Hibaüzenet: Nem sikerült lekért hozzáférési jogkivonat szolgáltatásnévvel. ADAL-hiba: service_unavailable

- **Tünetek:** Copy tevékenység hiba a következő hibával meghiúsul:

    `Failed to get access token by using service principal.  
    ADAL Error: service_unavailable, The remote server returned an error: (503) Server Unavailable.`

- **Ok:** Ha az Azure Active Directory tulajdonában lévő szolgáltatás jogkivonat-kiszolgáló (STS) nem érhető el, az azt jelenti, hogy túl elfoglalt a kérelmek kezeléséhez, és az 503-as HTTP-hibát adja vissza. 

- **Megoldás:** Futtassa újra a másolási tevékenységet néhány perc múlva.


## <a name="azure-data-lake-storage-gen2"></a>2. generációs Azure Data Lake Storage

### <a name="error-code-adlsgen2operationfailed"></a>Hibakód: ADLSGen2OperationFailed

- **Üzenet:**`ADLS Gen2 operation failed for: %adlsGen2Message;.%exceptionData;.`

- **Okok és javaslatok:** A különböző okok ezt a hibát okozhatják. Az alábbi listában ellenőrizheti a lehetséges okelemzést és a kapcsolódó javaslatot.

  | Okelemzés                                               | Ajánlás                                               |
  | :----------------------------------------------------------- | :----------------------------------------------------------- |
  | Ha Azure Data Lake Storage Gen2 hibaüzenetet ad, amely azt jelzi, hogy egy művelet sikertelen volt.| Ellenőrizze a hibaüzenetek által Azure Data Lake Storage Gen2. Ha a hiba átmeneti hiba, próbálja meg újra a műveletet. További segítségért forduljon az Azure Storage ügyfélszolgálatához, és adja meg a kérés azonosítóját a hibaüzenetben. |
  | Ha a hibaüzenet tartalmazza a "Tiltott" sztringet, előfordulhat, hogy a használt szolgáltatásnév vagy felügyelt identitás nem rendelkezik megfelelő engedélyekkel a Azure Data Lake Storage Gen2. | A hiba elhárításához lásd: Adatok másolása és átalakítása a Azure Data Lake Storage Gen2 [használatával Azure Data Factory.](./connector-azure-data-lake-storage.md#service-principal-authentication) |
  | Ha a hibaüzenet tartalmazza az "InternalServerError" sztringet, akkor a hibaüzenetet a Azure Data Lake Storage Gen2. | A hibát átmeneti hiba okozhatja. Ha mégis, próbálja meg újra elvégezni a műveletet. Ha a probléma továbbra is fennáll, forduljon az Azure Storage ügyfélszolgálatához, és adja meg a kérés azonosítóját a hibaüzenetből. |

### <a name="request-to-azure-data-lake-storage-gen2-account-caused-a-timeout-error"></a>A fiók Azure Data Lake Storage Gen2 kérése időtúllépési hibát okozott

- **Üzenet:** 
  * Hibakód = `UserErrorFailedBlobFSOperation`
  * Hibaüzenet = `BlobFS operation failed for: A task was canceled.`

- **Ok:** A problémát a Azure Data Lake Storage Gen2 időtúllépési hibája okozza, amely általában a saját Integration Runtime (IR) gépen jelentkezik.

- **Javaslat**: 

    - Ha lehetséges, helyezze el a saját Azure Data Lake Storage Gen2 integrációs Azure Data Lake Storage Gen2 a fiókot. Ez segít elkerülni a véletlenszerű időtúllépési hibákat, és jobb teljesítményt eredményez.

    - Ellenőrizze, hogy van-e speciális hálózati beállítás, például az ExpressRoute, és győződjön meg arról, hogy a hálózat elegendő sávszélességgel rendelkezik. Javasoljuk, hogy csökkentse az egyidejű integrációs integrációs szolgáltatás egyidejű feladatbeállítását, ha a teljes sávszélesség alacsony. Ezzel elkerülhető a hálózati erőforrások több egyidejű feladaton keresztüli versengése.

    - Ha a fájl mérete közepes vagy kicsi, használjon kisebb blokkméretet a nembináris másoláshoz az ilyen időtúllépési hiba csökkentése érdekében. További információkért lásd a [Put Block Blob Storage.](/rest/api/storageservices/put-block)

       Az egyéni blokkméret megadásához szerkessze a tulajdonságot a JSON-fájlszerkesztőben az itt látható módon:
    
        ```
        "sink": {
            "type": "DelimitedTextSink",
            "storeSettings": {
                "type": "AzureBlobFSWriteSettings",
                "blockSizeInMB": 8
            }
        }
        ```

                  
## <a name="azure-files-storage"></a>Azure Files storage

### <a name="error-code-azurefileoperationfailed"></a>Hibakód: AzureFileOperationFailed

- **Üzenet:**`Azure File operation Failed. Path: %path;. ErrorMessage: %msg;.`

- **Ok:** Probléma van a Azure Files tárolási művelettel.

- **Javaslat:** A hiba részleteinek ellenőrzéshez tekintse meg a [Azure Files súgóját.](/rest/api/storageservices/file-service-error-codes) További segítségért forduljon a Azure Files csapathoz.


## <a name="azure-synapse-analytics-azure-sql-database-and-sql-server"></a>Azure Synapse Analytics, Azure SQL Database és SQL Server

### <a name="error-code-sqlfailedtoconnect"></a>Hibakód: SqlFailedToConnect

- **Üzenet:**`Cannot connect to SQL Database: '%server;', Database: '%database;', User: '%user;'. Check the linked service configuration is correct, and make sure the SQL Database firewall allows the integration runtime to access.`
- **Okok és javaslatok:** A különböző okok ezt a hibát okozhatják. Az alábbi listában ellenőrizheti a lehetséges okelemzést és a kapcsolódó javaslatot.

    | Okelemzés                                               | Ajánlás                                               |
    | :----------------------------------------------------------- | :----------------------------------------------------------- |
    | Ha Azure SQL hibaüzenet tartalmazza az "SqlErrorNumber=47073" sztringet, az azt jelenti, hogy a kapcsolati beállítás megtagadja a nyilvános hálózati hozzáférést. | A hálózati Azure SQL állítsa a **Nyilvános hálózati** hozzáférés megtagadása beállítást a Nem *lehetőségre.* További információ: Azure SQL [beállításai.](../azure-sql/database/connectivity-settings.md#deny-public-network-access) |
    | Ha Azure SQL hibaüzenet például az "SqlErrorNumber=[errorcode]" SQL-hibakódot tartalmazza, tekintse meg a Azure SQL útmutatót. | Javaslatért lásd: Csatlakozási problémák és egyéb hibák elhárítása Azure SQL Database [és Azure SQL Managed Instance.](../azure-sql/database/troubleshoot-common-errors-issues.md) |
    | Ellenőrizze, hogy az 1433-as port szerepel-e a tűzfal engedélyezései listáján. | További információ: [A](/sql/sql-server/install/configure-the-windows-firewall-to-allow-sql-server-access#ports-used-by-)SQL Server. |
    | Ha a hibaüzenet tartalmazza az "SqlException" sztringet, SQL Database hibaüzenet azt jelzi, hogy egy adott művelet meghiúsult. | További információ: SQL-hibakód az [adatbázismotor hibáiban.](/sql/relational-databases/errors-events/database-engine-events-and-errors) További segítségért forduljon a Azure SQL ügyfélszolgálatához. |
    | Ha ez egy átmeneti probléma (például instabil hálózati kapcsolat), adja hozzá az újrapróbálkozásokat a tevékenység-szabályzathoz a probléma csökkentése érdekében. | További információ: Folyamatok és tevékenységek a [Azure Data Factory.](./concepts-pipelines-activities.md#activity-policy) |
    | Ha a hibaüzenet tartalmazza a "Client with IP address '..." (Ügyfél a következő IP-címmel: ...) sztringet A nem fér hozzá a kiszolgálóhoz" üzenet jelenik meg, és Azure SQL Database-kiszolgálóhoz próbál csatlakozni, a hibát általában egy Azure SQL Database tűzfal hibája okozza. | Az Azure SQL Server tűzfal-konfigurációjában engedélyezze az **Azure-szolgáltatások és** -erőforrások hozzáférésének engedélyezése a kiszolgálóhoz beállítást. További információ: [IP-Azure SQL Database Azure Synapse beállítása.](../azure-sql/database/firewall-configure.md) |
    
### <a name="error-code-sqloperationfailed"></a>Hibakód: SqlOperationFailed

- **Üzenet:**`A database operation failed. Please search error to get more details.`

- **Okok és javaslatok:** A különböző okok ezt a hibát okozhatják. Tekintse meg az alábbi listát a lehetséges okelemzésért és a kapcsolódó javaslatokért.

    | Okelemzés                                               | Ajánlás                                               |
    | :----------------------------------------------------------- | :----------------------------------------------------------- |
    | Ha a hibaüzenet tartalmazza az "SqlException" sztringet, a SQL Database hibát jelez, amely szerint egy adott művelet meghiúsult. | Ha az SQL-hiba nem egyértelmű, próbálja meg az adatbázist a legújabb kompatibilitási szintre (150) módosítani. A legújabb verziójú SQL-hibákat okozhatja. További információért lásd a [dokumentációt](/sql/t-sql/statements/alter-database-transact-sql-compatibility-level#backwardCompat). <br/> További információ az SQL-hibák elhárításáról: SQL-hibakód alapján való keresés az [adatbázismotor hibáiban.](/sql/relational-databases/errors-events/database-engine-events-and-errors) További segítségért lépjen kapcsolatba a Azure SQL ügyfélszolgálattal. |
    | Ha a hibaüzenet tartalmazza a "PdwManagedToNativeInteropException" sztringet, azt általában a forrás és a fogadó oszlopméretek eltérése okozza. | Ellenőrizze a forrás- és fogadóoszlopok méretét is. További segítségért forduljon a Azure SQL ügyfélszolgálatához. |
    | Ha a hibaüzenet tartalmazza az "InvalidOperationException" sztringet, azt általában érvénytelen bemeneti adatok okják. | Annak azonosításához, hogy melyik sor ütközött a problémába, engedélyezze a hibatűrési funkciót a másolási tevékenységen, amely átirányíthatja a problémás sorokat a tárolóba további vizsgálatra. További információ: [Másolási tevékenység hibatűrése a Azure Data Factory.](./copy-activity-fault-tolerance.md) |


### <a name="error-code-sqlunauthorizedaccess"></a>Hibakód: SqlUnauthorizedAccess

- **Üzenet:**`Cannot connect to '%connectorName;'. Detail Message: '%message;'`

- **Ok:** A hitelesítő adatok helytelenek, vagy a bejelentkezési fiók nem tud hozzáférni az SQL-adatbázishoz.

- **Javaslat:** Ellenőrizze, hogy a bejelentkezési fiók megfelelő engedélyekkel rendelkezik-e az SQL-adatbázis eléréséhez.


### <a name="error-code-sqlopenconnectiontimeout"></a>Hibakód: SqlOpenConnectionTimeout

- **Üzenet:**`Open connection to database timeout after '%timeoutValue;' seconds.`

- **Ok:** A probléma az SQL-adatbázis átmeneti hibája lehet.

- **Javaslat:** Próbálja meg újra a műveletet a csatolt szolgáltatás kapcsolati sztringének magasabb kapcsolati időtúllépési értékkel való frissítéséhez.


### <a name="error-code-sqlautocreatetabletypemapfailed"></a>Hibakód: SqlAutoCreateTableTypeMapFailed

- **Üzenet:**`Type '%dataType;' in source side cannot be mapped to a type that supported by sink side(column name:'%columnName;') in autocreate table.`

- **Ok:** Az automatikuslétrehozási tábla nem felel meg a forráskövetelménynek.

- **Javaslat:** Frissítse az oszloptípust a *leképezésekben,* vagy hozza létre manuálisan a fogadó táblát a célkiszolgálón.


### <a name="error-code-sqldatatypenotsupported"></a>Hibakód: SqlDataTypeNotSupported

- **Üzenet:**`A database operation failed. Check the SQL errors.`

- **Ok:** Ha a probléma az SQL-forrásban fordul elő, és a hiba az SqlDateTime túlcsorduláshoz kapcsolódik, az adatérték meghaladja a logikai típus tartományát (1/1/1753 12:00:00 – 12/31/9999 11:59:59 PM).

- **Javaslat:** Váltsa át a típust a forrás SQL-lekérdezésben lévő sztringre, vagy a másolási tevékenység oszlopleképezésében módosítsa az oszlop típusát Sztring *típusra.*

- **Ok:** Ha a probléma az SQL-fogadón fordul elő, és a hiba az SqlDateTime túlcsordulásával kapcsolatos, az adatérték meghaladja a fogadó táblában engedélyezett tartományt.

- **Javaslat:** Frissítse a megfelelő oszloptípust a fogadó táblában található *datetime2* típusra.


### <a name="error-code-sqlinvaliddbstoredprocedure"></a>Hibakód: SqlInvalidDbStoredProcedure

- **Üzenet:**`The specified Stored Procedure is not valid. It could be caused by that the stored procedure doesn't return any data. Invalid Stored Procedure script: '%scriptName;'.`

- **Ok:** A megadott tárolt eljárás érvénytelen. Ennek az lehet az oka, hogy a tárolt eljárás nem ad vissza adatokat.

- **Javaslat:** Ellenőrizze a tárolt eljárást az SQL Tools használatával. Győződjön meg arról, hogy a tárolt eljárás képes adatokat visszaadni.


### <a name="error-code-sqlinvaliddbquerystring"></a>Hibakód: SqlInvalidDbQueryString

- **Üzenet:**`The specified SQL Query is not valid. It could be caused by that the query doesn't return any data. Invalid query: '%query;'`

- **Ok:** A megadott SQL-lekérdezés érvénytelen. Ennek az lehet az oka, hogy a lekérdezés nem ad vissza adatokat.

- **Javaslat:** Ellenőrizze az SQL-lekérdezést az SQL Tools használatával. Győződjön meg arról, hogy a lekérdezés képes adatokat visszaadni.


### <a name="error-code-sqlinvalidcolumnname"></a>Hibakód: SqlInvalidColumnName

- **Üzenet:**`Column '%column;' does not exist in the table '%tableName;', ServerName: '%serverName;', DatabaseName: '%dbName;'.`

- **Ok:** Az oszlop nem található, mert a konfiguráció helytelen lehet.

- **Javaslat:** Ellenőrizze a lekérdezés *oszlopát,* az adatkészlet  szerkezetét és a tevékenység leképezéseit.


### <a name="error-code-sqlbatchwritetimeout"></a>Hibakód: SqlBatchWriteTimeout

- **Üzenet:**`Timeouts in SQL write operation.`

- **Ok:** A problémát okozhatja egy átmeneti SQL-adatbázishiba.

- **Javaslat:** Próbálja meg újra a műveletet. Ha a probléma továbbra is fennáll, forduljon Azure SQL ügyfélszolgálatához.


### <a name="error-code-sqlbatchwritetransactionfailed"></a>Hibakód: SqlBatchWriteTransactionFailed

- **Üzenet:**`SQL transaction commits failed.`

- **Ok:** Ha a kivétel részletei folyamatosan tranzakciók időtúllépését jelzik, az integrációs és az adatbázis közötti hálózati késés nagyobb, mint az alapértelmezett 30 másodperces küszöbérték.

- **Javaslat:** Frissítse az SQL-hez csatolt szolgáltatás kapcsolati sztringet egy 120-as vagy annál nagyobb kapcsolati időtúllépési értékkel, majd futtassa újra *a* tevékenységet.

- **Ok:** Ha a kivétel részletei időszakosan azt jelzik, hogy az SQL-kapcsolat megszakadt, átmeneti hálózati hiba vagy SQL-adatbázisoldali probléma lehet.

- **Javaslat:** Próbálja újra a tevékenységet, és tekintse át az SQL-adatbázis oldali metrikákat.


### <a name="error-code-sqlbulkcopyinvalidcolumnlength"></a>Hibakód: SqlBulkCopyInvalidColumnLength

- **Üzenet:**`SQL Bulk Copy failed due to receive an invalid column length from the bcp client.`

- **Ok:** Az SQL tömeges másolása sikertelen volt, mert érvénytelen oszlophosszt kapott a tömeges másolási program segédprogram (bcp) ügyfelétől.

- **Javaslat:** Annak azonosításához, hogy melyik sor ütközött a problémába, engedélyezze a hibatűrési funkciót a másolási tevékenységen. Ez átirányíthatja a problémás sorokat a tárolóba további vizsgálatra. További információ: [Másolási tevékenység hibatűrése a Azure Data Factory.](./copy-activity-fault-tolerance.md)


### <a name="error-code-sqlconnectionisclosed"></a>Hibakód: SqlConnectionIsClosed

- **Üzenet:**`The connection is closed by SQL Database.`

- **Ok:** Az SQL-adatbázis lezárja az SQL-kapcsolatot, ha egy magas párhuzamos futtatás után a kiszolgáló le is bontja a kapcsolatot.

- **Javaslat:** Próbálja meg újra a kapcsolatot. Ha a probléma továbbra is fennáll, lépjen kapcsolatba Azure SQL ügyfélszolgálattal.


### <a name="error-message-conversion-failed-when-converting-from-a-character-string-to-uniqueidentifier"></a>Hibaüzenet: A konvertálás nem sikerült, amikor a rendszer egy karaktersringet uniqueidentifier-vé konvertált

- **Tünetek:** Ha egy táblázatos adatforrásból (például SQL Server) másol adatokat a Azure Synapse Analytics és a PolyBase használatával, a következő hibaüzenet jelenik meg:

   `ErrorCode=FailedDbOperation,Type=Microsoft.DataTransfer.Common.Shared.HybridDeliveryException, 
    Message=Error happened when loading data into Azure Synapse Analytics., 
    Source=Microsoft.DataTransfer.ClientLibrary,Type=System.Data.SqlClient.SqlException, 
    Message=Conversion failed when converting from a character string to uniqueidentifier...`

- **Ok:** Azure Synapse Analytics PolyBase nem tud üres sztringet GUID azonosítóvá alakítani.

- **Megoldás:** A másolási tevékenység fogadója PolyBase-beállítások alatt állítsa a típus alapértelmezett **beállítását** false *(hamis) értékre.*


### <a name="error-message-expected-data-type-decimalxx-offending-value"></a>Hibaüzenet: Várt adattípus: DECIMAL(x,x), Offending value

- **Tünetek:** Ha egy táblázatos adatforrásból (például SQL Server) másol adatokat a Azure Synapse Analytics-be az szakaszos másolás és a PolyBase használatával, a következő hibaüzenet jelenik meg:

    `ErrorCode=FailedDbOperation,Type=Microsoft.DataTransfer.Common.Shared.HybridDeliveryException, 
    Message=Error happened when loading data into Azure Synapse Analytics., 
    Source=Microsoft.DataTransfer.ClientLibrary,Type=System.Data.SqlClient.SqlException, 
    Message=Query aborted-- the maximum reject threshold (0 rows) was reached while reading from an external source: 1 rows rejected out of total 415 rows processed. (/file_name.txt)  
    Column ordinal: 18, Expected data type: DECIMAL(x,x), Offending value:..`

- **Ok:** Azure Synapse Analytics PolyBase nem tud üres sztringet (null értéket) beszúrni egy decimális oszlopba.

- **Megoldás:** A másolási tevékenység fogadóban, a PolyBase beállításai alatt állítsa az alapértelmezett típust **false** (hamis) értékre.


### <a name="error-message-java-exception-message-hdfsbridgecreaterecordreader"></a>Hibaüzenet: Java-kivételüzenet: HdfsBridge::CreateRecordReader

- **Tünetek:** A PolyBase használatával Azure Synapse Analytics adatokat a Azure Synapse Analytics, és a következő hibaüzenet jelenik meg:

    `Message=110802;An internal DMS error occurred that caused this operation to fail.  
    Details: Exception: Microsoft.SqlServer.DataWarehouse.DataMovement.Common.ExternalAccess.HdfsAccessException, 
    Message: Java exception raised on call to HdfsBridge_CreateRecordReader.  
    Java exception message:HdfsBridge::CreateRecordReader - Unexpected error encountered creating the record reader.: Error [HdfsBridge::CreateRecordReader - Unexpected error encountered creating the record reader.] occurred while accessing external file.....`

- **Ok:** Ennek az lehet az oka, hogy a séma (az oszlop teljes szélessége) túl nagy (1 MB-nál nagyobb). Ellenőrizze a céltábla Azure Synapse Analytics az összes oszlop méretének hozzáadásával:

    - Int = 4 bájt
    - Bigint = 8 bájt
    - Varchar(n), char(n), binary(n), varbinary(n) = n bájt
    - Nvarchar(n), nchar(n) = n*2 bájt
    - Dátum = 6 bájt
    - Datetime/(2), smalldatetime = 16 bájt
    - Datetimeoffset = 20 bájt
    - Decimális = 19 bájt
    - Lebegő érték = 8 bájt
    - Pénz = 8 bájt
    - Smallmoney = 4 bájt
    - Valós = 4 bájt
    - Smallint = 2 bájt
    - Idő = 12 bájt
    - Tinyint = 1 bájt

- **Megoldás:** 
    - Csökkentse az oszlop szélességét 1 MB-nál kisebbre.
    - Vagy használjon tömeges beszúrási módszert a PolyBase letiltásával.


### <a name="error-message-the-condition-specified-using-http-conditional-headers-is-not-met"></a>Hibaüzenet: A FELTÉTELES HTTP-fejléc(ekkel) megadott feltétel nem teljesül

- **Tünetek:** SQL-lekérdezéssel adatokat fog lekérdezni a Azure Synapse Analytics, és a következő hibaüzenetet kapja:

    `...StorageException: The condition specified using HTTP conditional header(s) is not met...`

- **Ok:** Azure Synapse Analytics hibába ütközött az Azure Storage külső táblája lekérdezése során.

- **Megoldás:** Futtassa ugyanezt a lekérdezést a SQL Server Management Studio (SSMS), és ellenőrizze, hogy ugyanazt az eredményt kap-e. Ha így van, nyisson meg egy támogatási jegyet a Azure Synapse Analytics, és adja meg Azure Synapse Analytics kiszolgáló és az adatbázis nevét.


### <a name="performance-tier-is-low-and-leads-to-copy-failure"></a>A teljesítményszint alacsony, és másolási hibát eredményez

- **Tünetek:** Adatokat másol a Azure SQL Database, és a következő hibaüzenetet kapja: `Database operation failed. Error message from database execution : ExecuteNonQuery requires an open and available Connection. The connection's current state is closed.`

- **Ok:** Azure SQL Database s1 bemeneti/kimeneti (I/O) korlátokba ütközött.

- **Megoldás:** Frissítse a Azure SQL Database teljesítményszintre a probléma megoldásához. 


### <a name="sql-table-cant-be-found"></a>Az SQL-tábla nem található 

- **Tünetek:** Adatokat másol a hibridből egy helyszíni SQL Server táblába, és a következő hibaüzenetet kapja:`Cannot find the object "dbo.Contoso" because it does not exist or you do not have permissions.`

- **Ok:** Az aktuális SQL-fiók nem rendelkezik megfelelő engedélyekkel a .NET SqlBulkCopy.WriteToServer által kiadott kérések végrehajtásához.

- **Megoldás:** Váltson egy kiemeltebb SQL-fiókra.


### <a name="error-message-string-or-binary-data-is-truncated"></a>Hibaüzenet: Sztring- vagy bináris adatok csonkolódtak

- **Tünetek:** Hiba történik, ha adatokat másol egy helyszíni Azure SQL Server táblába. 

- **Ok:** A Cx SQL-tábla sémadefiníciója egy vagy több, a vártnál rövidebb oszlopot tartalmaz.

- **Megoldás:** A probléma megoldásához próbálja meg a következőket:

    1. A problémás sorok hibaelhárításához alkalmazza [](./copy-activity-fault-tolerance.md)az SQL-fogadó hibatűrését, különösen az "redirectIncompatibleRowSettings" hibatűrést.

        > [!NOTE]
        > A hibatűréshez további végrehajtási időre lehet szükség, ami magasabb költségekkel is jár.

    2. Ellenőrizze az átirányított adatokat az SQL-tábla sémaoszlopának hossza alapján, hogy lássa, mely oszlopokat kell frissíteni.

    3. Ennek megfelelően frissítse a táblasémát.


## <a name="azure-table-storage"></a>Azure Table Storage

### <a name="error-code-azuretableduplicatecolumnsfromsource"></a>Hibakód: AzureTableDuplicateColumnsFromSource

- **Üzenet:**`Duplicate columns with same name '%name;' are detected from source. This is NOT supported by Azure Table Storage sink.`

- **Ok:** A duplikált forrásoszlopok az alábbi okok valamelyike miatt fordulhatnak elő:
   * Az adatbázist használja forrásként és alkalmazott tábla-illesztésként.
   * A fejléc sorában olyan strukturálatlan CSV-fájlok vannak, amelyekben duplikált oszlopnevek vannak.

- **Javaslat:** Szükség szerint ellenőrizze és javítsa ki a forrásoszlopokat.


## <a name="db2"></a>DB2

### <a name="error-code-db2driverrunfailed"></a>Hibakód: DB2DriverRunFailed

- **Üzenet:**`Error thrown from driver. Sql code: '%code;'`

- **Ok:** Ha a hibaüzenet az "SQLSTATE=51002 SQLCODE=-805" sztringet tartalmazza, kövesse az Adatok másolása a [DB2](./connector-db2.md#linked-service-properties)adatbázisból a következő használatával című Azure Data Factory.

- **Javaslat:** Próbálja meg beállítani a "NULLID" értéket a `packageCollection`  tulajdonságban.


## <a name="delimited-text-format"></a>Tagolt szövegformátum

### <a name="error-code-delimitedtextcolumnnamenotallownull"></a>Hibakód: DelimitedTextColumnNameNotAllowNull

- **Üzenet:**`The name of column index %index; is empty. Make sure column name is properly specified in the header row.`

- **Ok:** Amikor a "firstRowAsHeader" be van állítva a tevékenységben, a oszlop neve az első sor lesz. Ez a hiba azt jelenti, hogy az első sor egy üres értéket tartalmaz (például "ColumnA, ColumnB").

- **Javaslat:** Ellenőrizze az első sort, és javítsa ki az értéket, ha üres.


### <a name="error-code-delimitedtextmorecolumnsthandefined"></a>Hibakód: TagoltTextMoreColumnsThanDefined

- **Üzenet:**`Error found when processing '%function;' source '%name;' with row number %rowCount;: found more columns than expected column count: %expectedColumnCount;.`

- **Okok és javaslatok:** A különböző okok ezt a hibát okozhatják. Az alábbi listában ellenőrizheti a lehetséges okelemzést és a kapcsolódó javaslatot.

  | Okelemzés                                               | Ajánlás                                               |
  | :----------------------------------------------------------- | :----------------------------------------------------------- |
  | A problémás sor oszlopszáma nagyobb, mint az első sor oszlopszáma. Ezt okozhatja adat probléma, helytelen oszlopelválasztó vagy idézőjel-karakter-beállítások. | A hibaüzenet sorszámának leellenőrzése, a sor oszlopának ellenőrzése és az adatok kijavítása. |
  | Ha a várt oszlopszám "1" egy hibaüzenetben, előfordulhat, hogy helytelen tömörítési vagy formázási beállításokat adott meg, ami miatt Data Factory a fájlok helytelenül elemezték a fájlokat. | Ellenőrizze a formátumbeállításokat, hogy azok megegyeznek-e a forrásfájlokkal. |
  | Ha a forrás egy mappa, a megadott mappában lévő fájlok eltérő sémával is lehetnek. | Győződjön meg arról, hogy a megadott mappában lévő fájlok sémája megegyezik. |


## <a name="dynamics-365-common-data-service-and-dynamics-crm"></a>Dynamics 365, Common Data Service és Dynamics CRM

### <a name="error-code-dynamicscreateserviceclienterror"></a>Hibakód: DynamicsCreateServiceClientError

- **Üzenet:**`This is a transient issue on Dynamics server side. Try to rerun the pipeline.`

- **Ok:** A probléma átmeneti probléma a Dynamics-kiszolgáló oldalán.

- **Javaslat:** Futtassa újra a folyamatot. Ha ismét sikertelen, próbálja meg csökkenteni a párhuzamosságokat. Ha a probléma továbbra is fennáll, forduljon a Dynamics ügyfélszolgálatához.


### <a name="missing-columns-when-you-import-a-schema-or-preview-data"></a>Hiányzó oszlopok séma- vagy előnézeti adatok importálásakor

- **Tünetek:** Egyes oszlopok hiányoznak a séma vagy az előzetes verzió adatainak importálása során. Hibaüzenet: `The valid structure information (column name and type) are required for Dynamics source.`

- **Ok:** Ez a probléma azért van, mert Data Factory nem tudja olyan oszlopokat mutatni, amelyek nem tartalmaznak értékeket az első 10 rekordban. Győződjön meg arról, hogy a hozzáadott oszlopok formátuma megfelelő. 

- **Javaslat:** Adja hozzá manuálisan az oszlopokat a leképezési lapon.


### <a name="error-code-dynamicsmissingtargetformultitargetlookupfield"></a>Hibakód: DynamicsMissingTargetForMultiTargetLookupField

- **Üzenet:**`Cannot find the target column for multi-target lookup field: '%fieldName;'.`

- **Ok:** A céloszlop nem létezik a forrásban vagy az oszlopleképezésben.

- **Javaslat**:  
  1. Győződjön meg arról, hogy a forrás tartalmazza a céloszlopot. 
  2. Adja hozzá a céloszlopot az oszlopleképezéshez. Győződjön meg arról, hogy a fogadó oszlop formátuma *{fieldName} @EntityReference*.


### <a name="error-code-dynamicsinvalidtargetformultitargetlookupfield"></a>Hibakód: DynamicsInvalidTargetForMultiTargetLookupField

- **Üzenet:**`The provided target: '%targetName;' is not a valid target of field: '%fieldName;'. Valid targets are: '%validTargetNames;'`

- **Ok:** Egy nem megfelelő entitásnév van megszava egy több célból álló keresőmező célentitásaként.

- **Javaslat:** Adjon meg egy érvényes entitásnevet a több célból álló keresőmező számára.


### <a name="error-code-dynamicsinvalidtypeformultitargetlookupfield"></a>Hibakód: DynamicsInvalidTypeForMultiTargetLookupField

- **Üzenet:**`The provided target type is not a valid string. Field: '%fieldName;'.`

- **Ok:** A céloszlopban lévő érték nem sztring.

- **Javaslat:** Adjon meg egy érvényes sztringet a több cél keresési céloszlopában.


### <a name="error-code-dynamicsfailedtorequetserver"></a>Hibakód: DynamicsFailedToRequetServer

- **Üzenet:**`The Dynamics server or the network is experiencing issues. Check network connectivity or check Dynamics server log for more details.`

- **Ok:** A Dynamics-kiszolgáló instabil vagy nem érhető el, vagy a hálózaton problémák tapasztalhatók.

- **Javaslat:** További részletekért tekintse meg a hálózati kapcsolatot vagy a Dynamics-kiszolgáló naplóját. További segítségért forduljon a Dynamics ügyfélszolgálatához.


### <a name="error-code--dynamicsfailedtoconnect"></a>Hibakód: DynamicsFailedToConnect 
 
 - **Üzenet:**`Failed to connect to Dynamics: %message;` 
 

 - **Ok:** Ha a hibaüzenetben megjelenik, az azt jelenti, hogy a kiszolgáló bizonyos konfigurációi nem kompatibilisek az `Office 365 auth with OAuth failed` OAuth-hitelesítéssel. 
 
 - **Javaslat**: 
    1. Segítségért forduljon a Dynamics támogatási csapatához a részletes hibaüzenettel.  
    1. Használja a szolgáltatásnév-hitelesítést, és tekintse meg a következő cikket: [Példa: Dynamics Online Azure AD-szolgáltatásnévvel és tanúsítványhitelesítéssel.](https://docs.microsoft.com/azure/data-factory/connector-dynamics-crm-office-365#example-dynamics-online-using-azure-ad-service-principal-and-certificate-authentication) 
 

 - **Ok:** Ha a hibaüzenetben megjelenik, az azt jelenti, hogy rossz Dynamics-szolgáltatás URL-címét vagy proxyját/tűzfalát adja meg a forgalom `Unable to retrieve authentication parameters from the serviceUri` elfogása érdekében. 
 
 - **Javaslat**:
    1. Győződjön meg arról, hogy a megfelelő szolgáltatás URI-t tette a csatolt szolgáltatásba. 
    1. Ha a saját üzemeltetett integrációs adatbázist használja, győződjön meg arról, hogy a tűzfal/proxy nem fogta el a Dynamics-kiszolgálónak lekért kéréseket. 
   
 
 - **Ok:** Ha a hibaüzenetben megjelenik, az azt jelenti, hogy a kiszolgálóoldal nem várt `An unsecured or incorrectly secured fault was received from the other party` válaszokat adott vissza. 
 
 - **Javaslat**: 
    1. Az Office 365-hitelesítés használata esetén győződjön meg arról, hogy a felhasználónév és a jelszó helyes. 
    1. Győződjön meg arról, hogy a megfelelő szolgáltatás URI-t adja meg. 
    1. Ha regionális CRM URL-címet használ (az URL-címben a "crm" utáni szám található), győződjön meg arról, hogy a megfelelő regionális azonosítót használja.
    1. Segítségért forduljon a Dynamics támogatási csapatához. 
 

 - **Ok:** Ha a hibaüzenetben látható, az azt jelenti, hogy a szervezet neve helytelen, vagy rossz CRM-régióazonosítót használt `No Organizations Found` a szolgáltatás URL-címében. 
 
 - **Javaslat**: 
    1. Győződjön meg arról, hogy a megfelelő szolgáltatás URI-t adja meg.
    1. Ha a regionális CRM URL-címet használja (az URL-címben a "crm" utáni szám található), győződjön meg arról, hogy a megfelelő regionális azonosítót használja. 
    1. Segítségért forduljon a Dynamics támogatási csapatához. 

 
 - **Ok:** Ha az AAD-hez kapcsolódó hibaüzenet jelenik meg, az azt jelenti, hogy `401 Unauthorized` probléma van a szolgáltatásnévvel. 

 - **Javaslat:** Kövesse a hibaüzenet útmutatását a szolgáltatásnévvel kapcsolatos probléma megoldásához.  
 
 
 - **Ok:** Egyéb hibák esetén a probléma általában a kiszolgálóoldalon van. 

 - **Javaslat:** Az [XrmToolBox használatával](https://www.xrmtoolbox.com/) létesítsen kapcsolatot. Ha a hiba továbbra is fennáll, segítségért forduljon a Dynamics támogatási csapatához. 
 
 
### <a name="error-code--dynamicsoperationfailed"></a>Hibakód: DynamicsOperationFailed 
 
- **Üzenet:**`Dynamics operation failed with error code: %code;, error message: %message;.` 

- **Ok:** A művelet a kiszolgálóoldalon meghiúsult. 

- **Javaslat:** Bontsa ki a Dynamics-művelet hibakódját a hibaüzenetből, és részletesebb információkért tekintse meg a `Dynamics operation failed with error code: {code}` [Webszolgáltatás](https://docs.microsoft.com/powerapps/developer/data-platform/org-service/web-service-error-codes) hibakódok című cikket. Szükség esetén kapcsolatba léphet a Dynamics támogatási csapatával. 
 
 
### <a name="error-code--dynamicsinvalidfetchxml"></a>Hibakód: DynamicsInvalidFetchXml 
  
- **Üzenet:**`The Fetch Xml query specified is invalid.` 

- **Ok:** Hiba lépett fel a beolvasási XML-fájlban.  

- **Javaslat:** Javítsa ki a hibát a beolvasási XML-fájlban. 
 
 
### <a name="error-code--dynamicsmissingkeycolumns"></a>Hibakód: DynamicsMissingKeyColumns 
 
- **Üzenet:**`Input DataSet must contain keycolumn(s) in Upsert/Update scenario. Missing key column(s): %column;`
 
- **Ok:** A forrásadatok nem tartalmazzák a fogadó entitás kulcsoszlopát. 

- **Javaslat:** Győződjön meg arról, hogy a kulcsoszlopok a forrásadatok között vannak, vagy leképez egy forrásoszlopot a fogadó entitás kulcsoszlopára. 
 
 
### <a name="error-code--dynamicsprimarykeymustbeguid"></a>Hibakód: DynamicsPrimaryKeyMustBeGuid 
 
- **Üzenet:**`The primary key attribute '%attribute;' must be of type guid.` 
 
- **Ok:** Az elsődleges kulcs oszlopának típusa nem "Guid". 
 
- **Javaslat:** Győződjön meg arról, hogy a forrásadatok elsődleges kulcsoszlopa "Guid" típusú. 
 

### <a name="error-code--dynamicsalternatekeynotfound"></a>Hibakód: DynamicsAlternateKeyNotFound 
 
- **Üzenet:**`Cannot retrieve key information of alternate key '%key;' for entity '%entity;'.` 
 
- **Ok:** A megadott alternatív kulcs nem létezik, amelyet helytelen kulcsnevek vagy nem megfelelő engedélyek okozhatnak. 
 
- **Javaslat**: <br/> 
    1. Javítsa ki a kulcsnévben a el hibákat.<br/> 
    1. Győződjön meg arról, hogy rendelkezik a megfelelő engedélyekkel az entitáshoz. 
 
 
### <a name="error-code--dynamicsinvalidschemadefinition"></a>Hibakód: DynamicsInvalidSchemaDefinition 
 
- **Üzenet:**`The valid structure information (column name and type) are required for Dynamics source.` 
 
- **Ok:** Az oszlopleképezés fogadóoszlopai kihagyják a "type" tulajdonságot. 
 
- **Javaslat:** A portál JSON-szerkesztőjével hozzáadhatja a "type" tulajdonságot az oszlopleképezés oszlopaihoz. 


## <a name="ftp"></a>FTP

### <a name="error-code-ftpfailedtoconnecttoftpserver"></a>Hibakód: FtpFailedToConnectToFtpServer

- **Üzenet:**`Failed to connect to FTP server. Please make sure the provided server information is correct, and try again.`

- **Ok:** Helytelen csatolt szolgáltatástípus használható az FTP-kiszolgálóhoz, például a Biztonságos FTP (SFTP) csatolt szolgáltatás használatával egy FTP-kiszolgálóhoz való csatlakozáshoz.

- **Javaslat:** Ellenőrizze a célkiszolgáló portját. Az FTP a 21-es portot használja.


## <a name="http"></a>HTTP

### <a name="error-code-httpfilefailedtoread"></a>Hibakód: HttpFileFailedToRead

- **Üzenet:**`Failed to read data from http server. Check the error from http server：%message;`

- **Ok:** Ez a hiba akkor fordul elő Azure Data Factory amikor a http-kiszolgálóval beszélget, de a HTTP-kérés művelete meghiúsul.

- **Javaslat:** Ellenőrizze a HTTP-állapotkódot a hibaüzenetben, és javítsa ki a távoli kiszolgálóval kapcsolatban észlelt problémát.


## <a name="oracle"></a>Oracle

### <a name="error-code-argumentoutofrangeexception"></a>Hibakód: ArgumentOutOfRangeException

- **Üzenet:**`Hour, Minute, and Second parameters describe an un-representable DateTime.`

- **Ok:** A Data Factory-ben a DateTime értékek a 0001-01-01 00:00:00 és 9999-12-31 23:59:59 tartományban támogatottak. Az Oracle azonban a DateTime értékek szélesebb tartományát támogatja, például a BC century vagy a min/sec>59 értéket, ami meghibásodáshoz Data Factory.

- **Javaslat**: 

    Ha meg kell tudni, hogy az Oracle értéke a tartományon belül van-e, futtassa a `select dump(<column name>)` Data Factory. 

    Az eredményben megadott bájtsorozatról a Hogyan vannak tárolva a dátumok [az Oracle-ban?](https://stackoverflow.com/questions/13568193/how-are-dates-stored-in-oracle).


## <a name="orc-format"></a>ORC formátum

### <a name="error-code-orcjavainvocationexception"></a>Hibakód: OrcJavaInvocationException

- **Üzenet:**`An error occurred when invoking Java, message: %javaException;.`
- **Okok és javaslatok:** A különböző okok ezt a hibát okozhatják. Tekintse meg az alábbi listát a lehetséges okelemzésért és a kapcsolódó javaslatokért.

    | Okelemzés                                               | Ajánlás                                               |
    | :----------------------------------------------------------- | :----------------------------------------------------------- |
    | Ha a hibaüzenet a "java.lang.OutOfMemory", a "Java halomtér" és a "doubleCapacity" sztringeket tartalmazza, ez általában egy memóriakezelési probléma az integrációs környezet egy régebbi verziójában. | Ha saját fejlesztésű Integration Runtime használ, javasoljuk, hogy frissítsen a legújabb verzióra. |
    | Ha a hibaüzenet tartalmazza a "java.lang.OutOfMemory" sztringet, az integrációs környezet nem rendelkezik elegendő erőforrással a fájlok feldolgozásához. | Korlátozza az egyidejű futtatásokat az integrációs runtime-on. A saját üzemelő integrációs szolgáltatáshoz skálázhat fel horizontálisan egy nagy teljesítményű, 8 GB vagy annál nagyobb memóriával rendelkező gépre. |
    |Ha a hibaüzenet tartalmazza a "NullPointerReference" sztringet, az ok átmeneti hiba lehet. | Próbálja meg újra a műveletet. Ha a probléma továbbra is fennáll, forduljon az ügyfélszolgálathoz. |
    | Ha a hibaüzenet tartalmazza a "BufferOverflowException" sztringet, az ok átmeneti hiba lehet. | Próbálja meg újra a műveletet. Ha a probléma továbbra is fennáll, forduljon az ügyfélszolgálathoz. |
    | Ha a hibaüzenet a "java.lang.ClassCastException:org.apache.hadoop.hive.serde2.io.HiveCharWritable" sztringet tartalmazza, nem írható át org.apache.hadoop.io.Text sztringre, az ok típuskonverziós probléma lehet a Java-runtime-ban. Ez általában azt jelenti, hogy a forrásadatok nem kezelhetők jól a Java-futtatásban. | Ez egy adat problémája. Az ORC formátumú adatokban karakter vagy varchar helyett használjon sztringet. |

### <a name="error-code-orcdatetimeexceedlimit"></a>Hibakód: OrcDateTimeExceedLimit

- **Üzenet:**`The Ticks value '%ticks;' for the datetime column must be between valid datetime ticks range -621355968000000000 and 2534022144000000000.`

- **Ok:** Ha a datetime értéke "0001-01-01 00:00:00", akkor a Calendar naptár és a Gergely-naptár közötti különbségek okozhatják. [](https://en.wikipedia.org/wiki/Proleptic_Gregorian_calendar#Difference_between_Julian_and_proleptic_Gregorian_calendar_dates)

- **Javaslat:** Ellenőrizze az órajel értékét, és ne használja a "0001-01-01 00:00:00" dátum/idő értéket.


## <a name="parquet-format"></a>Parquet formátum

### <a name="error-code-parquetjavainvocationexception"></a>Hibakód: ParquetJavaInvocationException

- **Üzenet:**`An error occurred when invoking java, message: %javaException;.`

- **Okok és javaslatok:** A különböző okok ezt a hibát okozhatják. Az alábbi listában ellenőrizheti a lehetséges okelemzést és a kapcsolódó javaslatot.

    | Okelemzés                                               | Ajánlás                                               |
    | :----------------------------------------------------------- | :----------------------------------------------------------- |
    | Ha a hibaüzenet a "java.lang.OutOfMemory", a "Java-halomtér" és a "doubleCapacity" sztringeket tartalmazza, ez általában a memóriakezelés problémája a Integration Runtime. | Ha helyi integrációs kiszolgálót használ, és a verzió a 3.20.7159.1-esnél korábbi, javasoljuk, hogy frissítsen a legújabb verzióra. |
    | Ha a hibaüzenet tartalmazza a "java.lang.OutOfMemory" sztringet, az integrációs környezet nem rendelkezik elegendő erőforrással a fájlok feldolgozásához. | Korlátozza az egyidejű futtatásokat az integrációs runtime-on. A saját üzemelő integrációs szolgáltatáshoz skálázhat fel horizontálisan egy nagy teljesítményű, 8 GB vagy annál nagyobb memóriával rendelkező gépre. |
    | Ha a hibaüzenet tartalmazza a "NullPointerReference" sztringet, az átmeneti hiba lehet. | Próbálja meg újra a műveletet. Ha a probléma továbbra is fennáll, forduljon az ügyfélszolgálathoz. |

### <a name="error-code-parquetinvalidfile"></a>Hibakód: ParquetInvalidFile

- **Üzenet:**`File is not a valid Parquet file.`

- **Ok:** Ez egy Parquet-fájllal kapcsolatban van probléma.

- **Javaslat:** Ellenőrizze, hogy a bemenet egy érvényes Parquet-fájl-e.


### <a name="error-code-parquetnotsupportedtype"></a>Hibakód: ParquetNotSupportedType

- **Üzenet:**`Unsupported Parquet type. PrimitiveType: %primitiveType; OriginalType: %originalType;.`

- **Ok:** A Parquet formátum nem támogatott a Azure Data Factory.

- **Javaslat:** Ellenőrizze a forrásadatokat a Támogatott fájlformátumok és tömörítési kodekek másolási tevékenységgel a következő [Azure Data Factory.](./supported-file-formats-and-compression-codecs.md)


### <a name="error-code-parquetmisseddecimalprecisionscale"></a>Hibakód: ParquetMissedDecimalPrecisionScale

- **Üzenet:**`Decimal Precision or Scale information is not found in schema for column: %column;.`

- **Ok:** A rendszer elemezte a szám pontosságát és skáláját, de nem adott meg ilyen információt.

- **Javaslat:** A forrás nem a megfelelő pontossági és skálázással kapcsolatos információkat adja vissza. Az információkért tekintse meg a probléma oszlopát.


### <a name="error-code-parquetinvaliddecimalprecisionscale"></a>Hibakód: ParquetInvalidDecimalPrecisionScale

- **Üzenet:**`Invalid Decimal Precision or Scale. Precision: %precision; Scale: %scale;.`

- **Ok:** A séma érvénytelen.

- **Javaslat:** Ellenőrizze a probléma oszlopban a pontosságot és a méretezést.


### <a name="error-code-parquetcolumnnotfound"></a>Hibakód: ParquetColumnNotFound

- **Üzenet:**`Column %column; does not exist in Parquet file.`

- **Ok:** A forrásséma nem egyező a fogadósémával.

- **Javaslat:** Ellenőrizze a leképezéseket a tevékenységben. Győződjön meg arról, hogy a forrásoszlop a megfelelő fogadóoszlopra van leképezve.


### <a name="error-code-parquetinvaliddataformat"></a>Hibakód: ParquetInvalidDataFormat

- **Üzenet:**`Incorrect format of %srcValue; for converting to %dstType;.`

- **Ok:** Az adatok nem konvertálhatók a mappings.source fájlban megadott típusra.

- **Javaslat:** Ellenőrizze a forrásadatokat, vagy adja meg a megfelelő adattípust ehhez az oszlophoz a másolási tevékenység oszlopleképezésében. További információ: Támogatott fájlformátumok és tömörítési [kodekek másolási tevékenység](./supported-file-formats-and-compression-codecs.md)alapján a Azure Data Factory.


### <a name="error-code-parquetdatacountnotmatchcolumncount"></a>Hibakód: ParquetDataCountNotMatchColumnCount

- **Üzenet:**`The data count in a row '%sourceColumnCount;' does not match the column count '%sinkColumnCount;' in given schema.`

- **Ok:** Eltérés a forrásoszlopok száma és a fogadóoszlopok száma között.

- **Javaslat:** Ellenőrizze, hogy a forrásoszlopok száma megegyezik-e a fogadó oszlopszámával a leképezésben.


### <a name="error-code-parquetdatatypenotmatchcolumntype"></a>Hibakód: ParquetDataTypeNotMatchColumnType

- **Üzenet:**`The data type %srcType; is not match given column type %dstType; at column '%columnIndex;'.`

- **Ok:** A forrásból származó adatok nem konvertálhatók a fogadóban meghatározott típusra.

- **Javaslat:** Adja meg a megfelelő típust a mapping.sink fájlban.


### <a name="error-code-parquetbridgeinvaliddata"></a>Hibakód: ParquetBridgeInvalidData

- **Üzenet:**`%message;`

- **Ok:** Az adatérték túllépte a korlátot.

- **Javaslat:** Próbálja meg újra a műveletet. Ha a probléma továbbra is fennáll, lépjen kapcsolatba velünk.


### <a name="error-code-parquetunsupportedinterpretation"></a>Hibakód: ParquetUnsupportedInterpretation

- **Üzenet:**`The given interpretation '%interpretation;' of Parquet format is not supported.`

- **Ok:** Ez a forgatókönyv nem támogatott.

- **Javaslat:** A "ParquetInterpretFor" nem lehet "sparkSql".


### <a name="error-code-parquetunsupportfilelevelcompressionoption"></a>Hibakód: ParquetUnsupportFileLevelCompressionOption

- **Üzenet:**`File level compression is not supported for Parquet.`

- **Ok:** Ez a forgatókönyv nem támogatott.

- **Javaslat:** Távolítsa el a "CompressionType" típust a hasznos adatból.


### <a name="error-code-usererrorjniexception"></a>Hibakód: UserErrorJniException

- **Üzenet:**`Cannot create JVM: JNI return code [-6][JNI call failed: Invalid arguments.]`

- **Ok:** Java virtuális gép (JVM) nem lehet létrehozni, mert érvénytelen (globális) argumentumok vannak beállítva.

- **Javaslat:** Jelentkezzen be arra a gépre, amely *a* saját üzemelő integrációs rendszer összes csomópontját üzemelteti. Ellenőrizze, hogy a rendszerváltozó megfelelően van-e beállítva, az alábbiak szerint: `_JAVA_OPTIONS "-Xms256m -Xmx16g" with memory bigger than 8 G` . Indítsa újra az összes ir-csomópontot, majd futtassa újra a folyamatot.


### <a name="arithmetic-overflow"></a>Aritmetikai túlcsordulás

- **Tünetek:** Hibaüzenet történt a Parquet-fájlok másolása során: `Message = Arithmetic Overflow., Source = Microsoft.DataTransfer.Common`

- **Ok:** Jelenleg csak a decimális pontosság <= 38 és a <= 20 egészrész hossza támogatott, ha fájlokat másol Oracle-ről Parquetre. 

- **Megoldás:** Áthidaló megoldásként a problémás oszlopokat VARCHAR2-re konvertálhatja.


### <a name="no-enum-constant"></a>Nincs felsorolási állandó

- **Tünetek:** Hibaüzenet történt, amikor parquet formátumba másol adatokat: `java.lang.IllegalArgumentException:field ended by &apos;;&apos;` , vagy : `java.lang.IllegalArgumentException:No enum constant org.apache.parquet.schema.OriginalType.test` .

- **Ok:** 

    A problémát okozhatják szóközök vagy nem támogatott speciális karakterek (például; {} ()\n\t=) az oszlop nevében, mert a Parquet nem támogatja ezt a formátumot. 

    Például egy olyan oszlopnév, mint a *contoso(test)* szögletes zárójelben lévő típust elemez a [kódból.](https://github.com/apache/parquet-mr/blob/master/parquet-column/src/main/java/org/apache/parquet/schema/MessageTypeParser.java) `Tokenizer st = new Tokenizer(schemaString, " ;{}()\n\t");` A hiba azért történt, mert nincs ilyen "teszt" típus.

    A támogatott típusok ellenőrzéshez tekintse meg a GitHub [apache/parquet-mr webhelyét.](https://github.com/apache/parquet-mr/blob/master/parquet-column/src/main/java/org/apache/parquet/schema/OriginalType.java)

- **Megoldás:** 

    Ellenőrizze újra, hogy:
    - A fogadó oszlop neve szóközöket is tartalmazhat.
    - A szóközöket tartalmazó első sort használja az oszlop neveként.
    - Az OriginalType típus támogatott. Kerülje a következő speciális karaktereket: `,;{}()\n\t=` . 


## <a name="rest"></a>REST

### <a name="error-code-restsinkcallfailed"></a>Hibakód: RestSinkCallFailed

- **Üzenet:**`Rest Endpoint responded with Failure from server. Check the error from server:%message;`

- **Ok:** Ez a hiba akkor fordul elő, Azure Data Factory HTTP-protokollon keresztül a REST-végponttal folytatott beszélgetéseket, és a kérelemművelet meghiúsul.

- **Javaslat:** Ellenőrizze a HTTP-állapotkódot vagy a hibaüzenetben található üzenetet, és javítsa ki a távoli kiszolgálóval kapcsolatban észlelt problémát.

### <a name="unexpected-network-response-from-the-rest-connector"></a>Váratlan hálózati válasz a REST-összekötőtől

- **Tünetek:** A végpont néha váratlan választ (400, 401, 403, 500) kap a REST-összekötőtől.

- **Ok:** A REST-forrás-összekötő a hivatkozott szolgáltatás/adatkészlet/másolás forrás URL-címét és HTTP-metódusát/fejlécét/törzsét használja paraméterként a HTTP-kérések felépítéséhez. A problémát valószínűleg egy vagy több megadott paraméterben található hibák okják.

- **Megoldás:** 
    - A "curl" parancsablakban való használatával láthatja, hogy a paraméter az oka-e ( Az **Accept** és a **User-Agent** fejléceket mindig meg kell adnia):
    
      `curl -i -X <HTTP method> -H <HTTP header1> -H <HTTP header2> -H "Accept: application/json" -H "User-Agent: azure-data-factory/2.0" -d '<HTTP body>' <URL>`
      
      Ha a parancs ugyanazt a váratlan választ adja vissza, javítsa ki a fenti paramétereket a "curl" használatával, amíg a várt választ nem adja vissza. 

      A "curl--help" is használható a parancs speciálisabb használatára.

    - Ha csak a Data Factory REST-összekötő ad vissza váratlan választ, további hibaelhárításért forduljon a Microsoft ügyfélszolgálatához.
    
    - Vegye figyelembe, hogy a "curl" nem biztos, hogy alkalmas ssl-tanúsítványérvényesítési probléma reprodukálásához. Bizonyos esetekben a "curl" parancs végrehajtása sikeres volt, ssl-tanúsítványérvényesítési problémák nélkül. Ha azonban ugyanazt az URL-címet egy böngészőben hajtható végre, a rendszer nem ad vissza SSL-tanúsítványt, hogy az ügyfél megbízhatósági kapcsolatot létesítsen a kiszolgálóval.

      Az olyan **eszközök, mint a Postman** és a **Fiddler,** az előző esethez ajánlottak.


## <a name="sftp"></a>SFTP

#### <a name="error-code-sftpoperationfail"></a>Hibakód: SftpOperationFail

- **Üzenet:**`Failed to '%operation;'. Check detailed error from SFTP.`

- **Ok:** Az SFTP művelettel kapcsolatos probléma.

- **Javaslat:** Ellenőrizze a hiba részleteit az SFTP-től.


### <a name="error-code-sftprenameoperationfail"></a>Hibakód: SftpRenameOperationFail

- **Üzenet:**`Failed to rename the temp file. Your SFTP server doesn't support renaming temp file, set "useTempFileRename" as false in copy sink to disable uploading to temp file.`

- **Ok:** Az SFTP-kiszolgáló nem támogatja az ideiglenes fájl átméretezését.

- **Javaslat:** Állítsa a "useTempFileRename" értéket false (hamis) értékre a másolási fogadóban az ideiglenes fájlba való feltöltés letiltásához.


### <a name="error-code-sftpinvalidsftpcredential"></a>Hibakód: SftpInvalidSftpCredential

- **Üzenet:**`Invalid SFTP credential provided for '%type;' authentication type.`

- **Ok:** A titkos kulcs tartalma az Azure Key Vaultból vagy az SDK-ból lesz beolvasva, de nincs megfelelően kódolva.

- **Javaslat**:  

    Ha a titkos kulcs tartalma a kulcstartóból van, az eredeti kulcsfájl működik, ha közvetlenül az SFTP-hez kapcsolódó szolgáltatásba tölti fel.

    További információ: Adatok másolása az SFTP-kiszolgálóról és az [SFTP-kiszolgálóra az Azure Data Factory.](./connector-sftp.md#use-ssh-public-key-authentication) A titkos kulcs tartalma base64 kódolású SSH titkos kulcs tartalma.

    Kódolja *a teljes* eredeti titkos kulcsfájlt base64 kódolással, és tárolja a kódolt sztringet a kulcstartóban. Az eredeti titkos kulcsfájl az, amely működni tud az SFTP csatolt szolgáltatáson, ha a **fájl** feltöltése lehetőséget választja.

    Íme néhány minta, amelyek segítségével létrehozhatja a sztringet:

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

    - Használjon harmadik féltől származó base64 konverziós eszközt. Javasoljuk a [Kódolás Base64 formátumba eszközt.](https://www.base64encode.org/)

- **Ok:** Nem a megfelelő kulcstartalomformátum lett kiválasztva.

- **Javaslat**:  

    A PKCS#8 formátumú titkos SSH-kulcs (-----BEGIN TITKOSÍTOTT TITKOS KULCS----- kezdetű) jelenleg nem támogatott az SFTP-kiszolgáló eléréséhez a Data Factory. 

    A kulcs hagyományos SSH-kulcsformátumra való konvertálásának "-----BEGIN RSA PRIVATE KEY-----" előjelével kezdődően futtassa a következő parancsokat:

    ```
    openssl pkcs8 -in pkcs8_format_key_file -out traditional_format_key_file
    chmod 600 traditional_format_key_file
    ssh-keygen -f traditional_format_key_file -p
    ```

- **Ok:** Érvénytelen hitelesítő adatok vagy titkos kulcs tartalma.

- **Javaslat:** Annak ellenőrzéséhez, hogy a kulcsfájl vagy a jelszó helyes-e, ellenőrizze újra az olyan eszközökkel, mint a WinSCP.

### <a name="sftp-copy-activity-failed"></a>Az SFTP másolási tevékenysége meghiúsult

- **Tünetek:** 
  * Hibakód: UserErrorInvalidColumnMappingColumnNotFound 
  * Hibaüzenet: `Column 'AccMngr' specified in column mapping cannot be found in source data.`

- **Ok:** A forrás nem tartalmaz "AccMngr" nevű oszlopot.

- **Megoldás:** Annak megállapításához, hogy létezik-e az "AccMngr" oszlop, ellenőrizze az adatkészlet konfigurációját a céladatkészlet oszlopának leképezése segítségével.


### <a name="error-code-sftpfailedtoconnecttosftpserver"></a>Hibakód: SftpFailedToConnectToSftpServer

- **Üzenet:**`Failed to connect to SFTP server '%server;'.`

- **Ok:** Ha a hibaüzenet a "Socket read operation has timed after 30,000 ezredmásodpercet" sztringet tartalmazza, az egyik lehetséges ok az, hogy a rendszer helytelen csatolt szolgáltatástípust használ az SFTP-kiszolgálóhoz. Előfordulhat például, hogy az FTP-hez kapcsolódó szolgáltatást használja az SFTP-kiszolgálóhoz való csatlakozáshoz.

- **Javaslat:** Ellenőrizze a célkiszolgáló portját. Alapértelmezés szerint az SFTP a 22-es portot használja.

- **Ok:** Ha a hibaüzenet a "Server response does not contain SSH protocol identification" (A kiszolgáló válasza nem tartalmaz SSH-protokollazonosítást) sztringet tartalmazza, az egyik lehetséges ok az, hogy az SFTP-kiszolgáló lerekedt a kapcsolaton. Data Factory több kapcsolatot hoz létre az SFTP-kiszolgálóról való párhuzamos letöltéshez, és időnként SFTP-kiszolgáló szabályozása is előfordulhat. A különböző kiszolgálók általában különböző hibákat adnak vissza szabályozás esetén.

- **Javaslat**:  

    Adja meg az SFTP-adatkészlet egyidejű kapcsolatainak maximális számát 1-ként, majd futtassa újra a másolási tevékenységet. Ha a tevékenység sikeres, biztos lehet abban, hogy a szabályozás az oka.

    Ha meg szeretné előléptetni az alacsony átviteli sebességet, lépjen kapcsolatba az SFTP-rendszergazdával az egyidejű kapcsolatszám-korlát növeléséhez, vagy tegye a következők egyikét:

    * Ha saját maga által üzemeltetett integrációs integrációs kiszolgálót használ, adja hozzá a saját üzemeltetett integrációs integrációs kiszolgáló IP-címét az engedélyező listához.
    * Ha ip-címeket Azure IR, adja Azure Integration Runtime [IP-címeket.](./azure-integration-runtime-ip-addresses.md) Ha nem szeretne IP-tartományt hozzáadni az SFTP-kiszolgáló megengedő listájához, használja inkább a saját üzemeltett integrációs adatbázist.

## <a name="sharepoint-online-list"></a>SharePoint Online-lista

### <a name="error-code-sharepointonlineauthfailed"></a>Hibakód: SharePointOnlineAuthFailed

- **Üzenet:**`The access token generated failed, status code: %code;, error message: %message;.`

- **Ok:** Előfordulhat, hogy a szolgáltatásnév azonosítója és kulcsa nincs megfelelően beállítva.

- **Javaslat:** Ellenőrizze a regisztrált alkalmazást (szolgáltatásnév-azonosítót) és kulcsot, és ellenőrizze, hogy helyesen vannak-e beállítva.


## <a name="xml-format"></a>XML formátum

### <a name="error-code-xmlsinknotsupported"></a>Hibakód: XmlSinkNotSupported

- **Üzenet:**`Write data in XML format is not supported yet, choose a different format!`

- **Ok:** Egy XML-adatkészletet használt fogadó adatkészletként a másolási tevékenységben.

- **Javaslat:** Használjon a fogadó adatkészlettől eltérő formátumú adatkészletet.


### <a name="error-code-xmlattributecolumnnameconflict"></a>Hibakód: XmlAttributeColumnNameConflict

- **Üzenet:**`Column names %attrNames;' for attributes of element '%element;' conflict with that for corresponding child elements, and the attribute prefix used is '%prefix;'.`

- **Ok:** Az ütközést okozó attribútumelőtag lett használva.

- **Javaslat:** Állítson be egy másik értéket az "attributePrefix" tulajdonsághoz.


### <a name="error-code-xmlvaluecolumnnameconflict"></a>Hibakód: XmlValueColumnNameConflict

- **Üzenet:**`Column name for the value of element '%element;' is '%columnName;' and it conflicts with the child element having the same name.`

- **Ok:** Az egyik gyermekelem neve az elemérték oszlopneveként lett használva.

- **Javaslat:** Állítson be egy másik értéket a "valueColumn" tulajdonsághoz.


### <a name="error-code-xmlinvalid"></a>Hibakód: XmlInvalid

- **Üzenet:**`Input XML file '%file;' is invalid with parsing error '%error;'.`

- **Ok:** A bemeneti XML-fájl nem megfelelő formátumú.

- **Javaslat:** Javítsa ki az XML-fájlt, hogy megfelelő formátumú legyen.


## <a name="general-copy-activity-error"></a>Általános másolási tevékenységgel kapcsolatos hiba

### <a name="error-code-jrenotfound"></a>Hibakód: JreNotFound

- **Üzenet:**`Java Runtime Environment cannot be found on the Self-hosted Integration Runtime machine. It is required for parsing or writing to Parquet/ORC files. Make sure Java Runtime Environment has been installed on the Self-hosted Integration Runtime machine.`

- **Ok:** A saját üzemeltetett integrációs kiszolgáló nem találja a Java-futásidőt. A Java-futásidő az egyes források olvasmányához szükséges.

- **Javaslat:** Ellenőrizze az integrációskörnyezetet, lásd: [Saját üzemelő Integration Runtime.](./format-parquet.md#using-self-hosted-integration-runtime)


### <a name="error-code-wildcardpathsinknotsupported"></a>Hibakód: WildcardPathSinkNotSupported

- **Üzenet:**`Wildcard in path is not supported in sink dataset. Fix the path: '%setting;'.`

- **Ok:** A fogadó adatkészlet nem támogatja a helyettesítő karaktereket.

- **Javaslat:** Ellenőrizze a fogadó adatkészletet, és írja át az elérési utat helyettesítő karakteres érték használata nélkül.


### <a name="fips-issue"></a>FIPS-probléma

- **Tünetek:** Copy tevékenység FIPS-kompatibilis, saját üzemeltetett ir-gépeken a következő hibaüzenet jelenik meg: `This implementation is not part of the Windows Platform FIPS validated cryptographic algorithms.` 

- **Ok:** Ez a hiba akkor fordulhat elő, ha olyan összekötők használatával másol adatokat, mint az Azure Blob, az SFTP stb. A Federal Information Processing Standards (FIPS) titkosítási algoritmusok bizonyos készletét határozza meg, amelyek használata engedélyezett. Ha a FIPS mód engedélyezve van a gépen, bizonyos esetekben blokkolva vannak bizonyos titkosítási osztályok, amelyektől a másolási tevékenység függ.

- **Megoldás:** Megtudhatja, miért nem javasoljuk a ["FIPS mode" (FIPS mód)](https://techcommunity.microsoft.com/t5/microsoft-security-baselines/why-we-8217-re-not-recommending-8220-fips-mode-8221-anymore/ba-p/701037)használatát, és értékelje ki, hogy letilthatja-e a FIPS-t a saját üzemeltetett ir-gépén.

    Ha azt szeretné, hogy a fips Azure Data Factory megkerülése és a tevékenységfutatok sikeresek legyenek, tegye a következőket:

    1. Nyissa meg azt a mappát, ahová a saját telepítésű integrációs rendszer telepítve van. Az elérési út általában *a következő: C:\Program Files\Microsoft Integration Runtime \<IR version> \Shared*.

    2. Nyissa meg *diawp.exe.config* fájlt, majd a szakasz végén adja hozzá a `<runtime>` `<enforceFIPSPolicy enabled="false"/>` következőt:

        ![Képernyőkép a fájl diawp.exe.config FIPS letiltva megjelenítéséről.](./media/connector-troubleshoot-guide/disable-fips-policy.png)

    3. Mentse a fájlt, majd indítsa újra a saját üzemeltetett integrációs integrációs kiszolgálót.


## <a name="next-steps"></a>Következő lépések

Ha további hibaelhárítási segítségre van szüksége, próbálja ki az alábbi erőforrásokat:

*  [Data Factory blog](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [Data Factory szolgáltatáskérések](https://feedback.azure.com/forums/270578-data-factory)
*  [Azure-videók](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [Microsoft Q&A lap](/answers/topics/azure-data-factory.html)
*  [Stack Overflow fórum a Data Factory](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [Twitter-információk a Data Factory](https://twitter.com/hashtag/DataFactory)