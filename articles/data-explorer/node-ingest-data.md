---
title: 'Gyors útmutató: Az Azure Data Explorer csomópontja library használata az adatok betöltése'
description: Ennek a rövid útmutatónak a segítségével megtanulhatja, hogyan tölthet be adatokat az Azure Data Explorerbe a Node.js használatával.
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: quickstart
ms.date: 10/25/2018
ms.openlocfilehash: 42a4d2e34eab3679f98d5a62099ab57e5924fcc3
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/16/2019
ms.locfileid: "65800427"
---
# <a name="quickstart-ingest-data-using-the-azure-data-explorer-node-library"></a>Gyors útmutató: Az Azure Data Explorer csomópontja library használata az adatok betöltése

Az Azure Adatkezelő egy gyors és hatékonyan skálázható adatáttekintési szolgáltatás napló- és telemetriaadatokhoz. Az Azure Data Explorer két ügyfélkódtárat biztosít a Node számára: egy [betöltési kódtárat](https://github.com/Azure/azure-kusto-node/tree/master/azure-kusto-ingest) és [egy adatkódtárat](https://github.com/Azure/azure-kusto-node/tree/master/azure-kusto-data). Ezekkel a kódtárakkal adatokat tölthet be egy fürtbe, illetve adatokat kérdezhet le a kódból. Ebben a rövid útmutatóban először létrehoz egy táblát és egy adatleképezést egy tesztfürtben. Ezután sorba helyezi a fürtbe való betöltést, és ellenőrzi az eredményeket.

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes Azure-fiókot](https://azure.microsoft.com/free/) a virtuális gép létrehozásának megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

A rövid útmutató elvégzéséhez az Azure-előfizetés mellett szüksége lesz a következőkre is:

* [Egy tesztfürt és -adatbázis](create-cluster-database-portal.md)

* A fejlesztői számítógépen telepített [Node.js](https://nodejs.org/en/download/)

## <a name="install-the-data-and-ingest-libraries"></a>Az adatok telepítése és a kódtárak betöltése

Az *azure kusto-ingest* és az *azure kusto-data* telepítése

```bash
npm i azure-kusto-ingest azure-kusto-data
```

## <a name="add-import-statements-and-constants"></a>Importálási utasítások és állandók hozzáadása

Osztályok importálása a kódtárakból

```javascript

const KustoConnectionStringBuilder = require("azure-kusto-data").KustoConnectionStringBuilder;
const KustoClient = require("azure-kusto-data").Client;
const KustoIngestClient = require("azure-kusto-ingest").IngestClient;
const IngestionProperties = require("azure-kusto-ingest").IngestionProperties;
const { DataFormat } = require("azure-kusto-ingest").IngestionPropertiesEnums;
const { BlobDescriptor } = require("azure-kusto-ingest").IngestionDescriptors;

```
Az alkalmazás hitelesítéséhez az Azure Data Explorer az Azure Active Directory-bérlő azonosítóját használja. A bérlőazonosító megkereséséhez kövesse az [Office 365-bérlőazonosító megkeresésének lépéseit](https://docs.microsoft.com/onedrive/find-your-office-365-tenant-id).

A kód futtatása előtt állítsa be az `authorityId`, `kustoUri`, `kustoIngestUri` és `kustoDatabase` értékét.

```javascript
const cluster = "MyCluster";
const region = "westus";
const authorityId = "microsoft.com";
const kustoUri = `https://${cluster}.${region}.kusto.windows.net:443`;
const kustoIngestUri = `https://ingest-${cluster}.${region}.kusto.windows.net:443`;
const kustoDatabase  = "Weather";
```

Most hozza létre a kapcsolati sztringet. Ez a példa eszközhitelesítést használ a fürt eléréséhez. Emellett az Azure Active Directory-alkalmazástanúsítványt, -alkalmazáskulcsot, illetve -felhasználót és -jelszót is használhatja.

Egy későbbi lépésben fogja létrehozni a céltáblát és a leképezést.

```javascript
const kcsbIngest = KustoConnectionStringBuilder.withAadDeviceAuthentication(kustoIngestUri, authorityId);
const kcsbData = KustoConnectionStringBuilder.withAadDeviceAuthentication(kustoUri, authorityId);
const destTable = "StormEvents";
const destTableMapping = "StormEvents_CSV_Mapping";
```

## <a name="set-source-file-information"></a>A forrásfájl adatainak beállítása

További osztályokat importálhat, és beállíthatja az adatforrásfájl állandóit. Ez a példa egy Azure Blob Storage-ban üzemeltetett mintafájlt használ. A **StormEvents** mintaadatkészlet a [környezeti adatok nemzeti központjaiból](https://www.ncdc.noaa.gov/stormevents/) származó, időjárással kapcsolatos adatokat tartalmaz.

```javascript
const container = "samplefiles";
const account = "kustosamplefiles";
const sas = "?st=2018-08-31T22%3A02%3A25Z&se=2020-09-01T22%3A02%3A00Z&sp=r&sv=2018-03-28&sr=b&sig=LQIbomcKI8Ooz425hWtjeq6d61uEaq21UVX7YrM61N4%3D";
const filePath = "StormEvents.csv";
const blobPath = `https://${account}.blob.core.windows.net/${container}/${filePath}${sas}`;
```

## <a name="create-a-table-on-your-test-cluster"></a>Tábla létrehozása a tesztfürtön

Hozzon létre egy táblát, amely megfelel a `StormEvents.csv` fájlban szereplő adatok sémájának. Ez a kód futtatásakor a következőhöz hasonló üzenetet adja vissza: *Jelentkezzen be, használja egy webböngészőben nyissa meg a https://microsoft.com/devicelogin , és írja be a kódot a hitelesítéshez XXXXXXXXX*. Kövesse a bejelentkezési lépéseket, majd térjen vissza a következő kódblokk futtatásához. A kapcsolatot létrehozó későbbi kódblokkokhoz ismét be kell jelentkeznie.

```javascript
const kustoClient = new KustoClient(kcsbData);
const createTableCommand = `.create table ${destTable} (StartTime: datetime, EndTime: datetime, EpisodeId: int, EventId: int, State: string, EventType: string, InjuriesDirect: int, InjuriesIndirect: int, DeathsDirect: int, DeathsIndirect: int, DamageProperty: int, DamageCrops: int, Source: string, BeginLocation: string, EndLocation: string, BeginLat: real, BeginLon: real, EndLat: real, EndLon: real, EpisodeNarrative: string, EventNarrative: string, StormSummary: dynamic)`;

kustoClient.executeMgmt(kustoDatabase, createTableCommand, (err, results) => {
    console.log(results.primaryResults[0][0].toString());
});
```

## <a name="define-ingestion-mapping"></a>Adatbetöltési leképezés meghatározása

Leképezheti a bejövő CSV-adatokat a tábla létrehozásakor használt oszlopnevekre és adattípusokra.

```javascript
const createMappingCommand = `.create table ${destTable} ingestion csv mapping '${destTableMapping}' '[{"Name":"StartTime","datatype":"datetime","Ordinal":0}, {"Name":"EndTime","datatype":"datetime","Ordinal":1},{"Name":"EpisodeId","datatype":"int","Ordinal":2},{"Name":"EventId","datatype":"int","Ordinal":3},{"Name":"State","datatype":"string","Ordinal":4},{"Name":"EventType","datatype":"string","Ordinal":5},{"Name":"InjuriesDirect","datatype":"int","Ordinal":6},{"Name":"InjuriesIndirect","datatype":"int","Ordinal":7},{"Name":"DeathsDirect","datatype":"int","Ordinal":8},{"Name":"DeathsIndirect","datatype":"int","Ordinal":9},{"Name":"DamageProperty","datatype":"int","Ordinal":10},{"Name":"DamageCrops","datatype":"int","Ordinal":11},{"Name":"Source","datatype":"string","Ordinal":12},{"Name":"BeginLocation","datatype":"string","Ordinal":13},{"Name":"EndLocation","datatype":"string","Ordinal":14},{"Name":"BeginLat","datatype":"real","Ordinal":16},{"Name":"BeginLon","datatype":"real","Ordinal":17},{"Name":"EndLat","datatype":"real","Ordinal":18},{"Name":"EndLon","datatype":"real","Ordinal":19},{"Name":"EpisodeNarrative","datatype":"string","Ordinal":20},{"Name":"EventNarrative","datatype":"string","Ordinal":21},{"Name":"StormSummary","datatype":"dynamic","Ordinal":22}]'`;

kustoClient.executeMgmt(kustoDatabase, createMappingCommand, (err, results) => {
    console.log(results.primaryResults[0][0].toString());
});
```

## <a name="queue-a-message-for-ingestion"></a>Üzenet várólistába helyezése a betöltéshez

Az üzeneteket várólistába helyezheti az adatokat a Blob Storage-ból való lekérése és az Azure Data Explorerbe való betöltése érdekében.

```javascript
const defaultProps  = new IngestionProperties(kustoDatabase, destTable, DataFormat.csv, null,destTableMapping, {'ignoreFirstRecord': 'true'});
const ingestClient = new KustoIngestClient(kcsbIngest, defaultProps);
// All ingestion properties are documented here: https://docs.microsoft.com/azure/kusto/management/data-ingest#ingestion-properties

const blobDesc = new BlobDescriptor(blobPath, 10);
ingestClient.ingestFromBlob(blobDesc,null, (err) => {
    if (err) throw new Error(err);
});
```

## <a name="validate-that-table-contains-data"></a>Annak ellenőrzése, hogy a tábla adatokat tartalmaz

Ellenőrizheti, hogy az adatok be lettek-e töltve a táblába. Várjon öt-tíz percet, amíg a várólistában lévő betöltés ütemezi a betöltést és betölti az adatokat az Azure Data Explorerbe. Ezután futtassa a következő kódot a `StormEvents`-táblában lévő rekordok számának lekérdezéséhez.

```javascript
const query = `${destTable} | count`;

kustoClient.execute(kustoDatabase, query, (err, results) => {
    if (err) throw new Error(err);  
    console.log(results.primaryResults[0][0].toString());
});
```

## <a name="run-troubleshooting-queries"></a>Hibaelhárítási lekérdezések futtatása

Jelentkezzen be a [https://dataexplorer.azure.com](https://dataexplorer.azure.com) oldalon, és csatlakozzon a fürthöz. Futtassa a következő parancsot az adatbázisban annak ellenőrzéséhez, hogy voltak-e betöltési hibák az elmúlt négy órában. A futtatás előtt cserélje le az adatbázis nevét.
    
```Kusto
.show ingestion failures
| where FailedOn > ago(4h) and Database == "<DatabaseName>"
```

Futtassa a következő parancsot az elmúlt négy órában végzett összes betöltési művelet állapotának megtekintéséhez. A futtatás előtt cserélje le az adatbázis nevét.

```Kusto
.show operations
| where StartedOn > ago(4h) and Database == "<DatabaseName>" and Operation == "DataIngestPull"
| summarize arg_max(LastUpdatedOn, *) by OperationId
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha el szeretné végezni a többi rövid útmutatót és oktatóanyagot, őrizze meg a létrehozott erőforrásokat. Ha nem szeretné, futtassa a következő parancsot az adatbázisban a `StormEvents`-tábla felesleges elemeinek eltávolításához.

```Kusto
.drop table StormEvents
```

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Lekérdezések írása](write-queries.md)
