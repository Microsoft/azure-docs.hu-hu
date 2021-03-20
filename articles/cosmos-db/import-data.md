---
title: 'Oktatóanyag: a Azure Cosmos DB adatbázis-áttelepítési eszköze'
description: 'Oktatóanyag: a nyílt forráskódú Azure Cosmos db áttelepítési eszközök használatával különböző forrásokból (például MongoDB, SQL Server, Table Storage, Amazon DynamoDB, CSV vagy JSON-fájlokból) importálhatja az adatAzure Cosmos DBokat. A CSV-fájlokat JSON formátumba konvertálhatja.'
author: deborahc
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: tutorial
ms.date: 10/23/2020
ms.author: dech
ms.openlocfilehash: 1cee4d2ad1bc7f362a045a5991624ec43521b8d2
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96341648"
---
# <a name="tutorial-use-data-migration-tool-to-migrate-your-data-to-azure-cosmos-db"></a>Oktatóanyag: Adatok Azure Cosmos DB-be migrálása az adatmigrálási eszköz használatával
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Ez az oktatóanyag útmutatást nyújt az Azure Cosmos DB adatmigrálási eszközének használatával kapcsolatban, amellyel különböző forrásokból importálhat adatokat Azure Cosmos-tárolókba és -táblákba. Importálhat JSON- és CSV-fájlokat, SQL-, MongoDB-, Azure Table Storage- és Amazon DynamoDB-adatbázisokat, illetve akár Azure Cosmos DB SQL API-gyűjteményeket is. Az adatokat gyűjteményekbe és táblákba migrálja az Azure Cosmos DB-vel való használathoz. Az adatáttelepítési eszközzel emellett SQL API-beli egypartíciós gyűjteményt is telepíthet át többpartíciós gyűjteménybe.

> [!NOTE]
> A Azure Cosmos DB adat-áttelepítési eszköz egy nyílt forráskódú eszköz, amely kis áttelepítéshez készült. Nagyobb áttelepítésekhez tekintse meg az [útmutató az adatfeldolgozáshoz című útmutatót](cosmosdb-migrationchoices.md).

* **[SQL API](./introduction.md)** – az adatáttelepítési eszközben található bármelyik forrás-beállítást használhatja az adat kis léptékű importálásához. [Ismerje meg az Adatimportálási lehetőségeket nagy léptékben](cosmosdb-migrationchoices.md).
* **[Table API](table-introduction.md)** – az adatáttelepítési eszköz vagy a [AzCopy](table-import.md#migrate-data-by-using-azcopy) használatával importálhat adatfájlokat. További információért olvassa el az [Adatok importálása az Azure Cosmos DB Table API-val való használathoz](table-import.md) című témakört.
* **[Azure Cosmos db API-MongoDB](mongodb-introduction.md)** – az adatáttelepítési eszköz nem támogatja Azure Cosmos db API-ját MongoDB vagy forrásként, illetve célként. Ha Azure Cosmos DB gyűjteményekben lévő vagy azokon kívüli gyűjteményekbe szeretné áttelepíteni az adatait, tekintse át a [MongoDB-adatbázisok áttelepítését egy Cosmos-adatbázisba Azure Cosmos db API-val a MongoDB](../dms/tutorial-mongodb-cosmos-db.md?toc=%2fazure%2fcosmos-db%2ftoc.json%253ftoc%253d%2fazure%2fcosmos-db%2ftoc.json) című témakört. Az adatáttelepítési eszközzel azonban exportálhat adatokat MongoDB-ből Azure Cosmos DB SQL API-gyűjteményekbe az SQL API-val való használathoz.
* **[Cassandra API](graph-introduction.md)** – az adatáttelepítési eszköz nem támogatott importálási eszköz Cassandra API-fiókokhoz. [Tudnivalók az Adatimportálási lehetőségekről a Cassandra APIba](cosmosdb-migrationchoices.md#azure-cosmos-db-cassandra-api)
* **[GREMLIN API](graph-introduction.md)** – az adatáttelepítési eszköz jelenleg nem támogatott importálási eszköz a Gremlin API-fiókokhoz. [Tudnivalók az áttelepítés Gremlin API-ba történő importálási lehetőségeiről](cosmosdb-migrationchoices.md#other-apis) 

Ez az oktatóanyag a következő feladatokat mutatja be:

> [!div class="checklist"]
> * Az adatáttelepítési eszköz telepítése
> * Adatok importálása különböző adatforrásokból
> * Exportálás Azure Cosmos DB-ből JSON-ba

## <a name="prerequisites"></a><a id="Prerequisites"></a>Előfeltételek

A cikkben szereplő utasítások követése előtt győződjön meg arról, hogy a következő lépéseket hajtja végre:

* **Telepítse** a [Microsoft .NET Framework 4,51](https://www.microsoft.com/download/developer-tools.aspx) -es vagy újabb verzióját.

* **Átviteli sebesség növelése:** Az adatok áttelepítésének időtartama az egyes gyűjteményekhez vagy gyűjtemények készletéhez beállított átviteli sebességtől függ. Nagyobb adatmigrálásoknál mindenképpen növelje az átviteli sebességet. A migrálás befejezése után, a költségtakarékosság érdekében csökkentse az átviteli sebességet. További információ a Azure Portal [teljesítményének](performance-levels.md) növeléséről: a Azure Cosmos db teljesítményszint és [díjszabási](https://azure.microsoft.com/pricing/details/cosmos-db/) szintjei.

* **Azure Cosmos DB-erőforrások létrehozása:** Az adatok migrálásának indítása előtt hozza létre előre az összes gyűjteményt az Azure Portalról. Az adatbázis-szintű átviteli sebességű Azure Cosmos DB-fiókra való áttelepítéshez adjon meg egy partíciós kulcsot az Azure Cosmos-tárolók létrehozásakor.

> [!IMPORTANT]
> Annak ellenőrzéséhez, hogy az adatáttelepítési eszköz az Azure Cosmos-fiókokhoz való csatlakozáskor Transport Layer Security (TLS) 1,2-et használ-e, használja a .NET-keretrendszer 4,7-es verzióját, vagy kövesse a [jelen cikkben](/dotnet/framework/network-programming/tls)található utasításokat.

## <a name="overview"></a><a id="Overviewl"></a>Áttekintés

Az adatáttelepítési eszköz egy nyílt forráskódú megoldás, mellyel adatokat importálhat az Azure Cosmos DB-be különféle forrásokból, ideértve az alábbiakat:

* JSON-fájlok
* MongoDB
* SQL Server
* CSV-fájlok
* Azure Table Storage
* Amazon DynamoDB
* A HBase
* Azure Cosmos-tárolók

Az importálási eszköz tartalmaz egy grafikus felhasználói felületet (dtui.exe), de használhatja a parancssori verziót is (dt.exe). Valójában lehetőség van arra, hogy a kapcsolódó parancsot a felhasználói felületen keresztüli importálás beállítása után kiírja. A táblázatos forrásadatok, például a SQL Server-vagy CSV-fájlok átalakításával hierarchikus kapcsolatok (aldokumentumok) hozhatók létre az importálás során. Az alábbiakban többet is megtudhat a választható forrásokról, mintaparancsokat találhat az egyes forrástípusokból való importáláshoz, és áttekintheti az importálás eredményét.

> [!NOTE]
> A kis áttelepítéshez csak a Azure Cosmos DB áttelepítési eszközt kell használnia. Nagyméretű áttelepítésekhez tekintse meg az [útmutató az adatfeldolgozáshoz című útmutatót](cosmosdb-migrationchoices.md).

## <a name="installation"></a><a id="Install"></a>Telepítés

Az áttelepítési eszköz forráskódját elérheti [ebben a GitHub-adattárban](https://github.com/azure/azure-documentdb-datamigrationtool). Letöltheti a forráskódot, és lefordíthatja helyben a megoldást, vagy [letöltheti az előre lefordított bináris fájlokat is](https://aka.ms/csdmtool). Ezután futtathatja az alábbiak egyikét:

* **Dtui.exe**: Az eszköz grafikus felhasználói felületet használó verziója.
* **Dtui.exe**: Az eszköz grafikus parancssori verziója.

## <a name="select-data-source"></a>Adatforrás kiválasztása

Az eszköz telepítése után megkezdheti az adatok importálását. Milyen típusú adatokat szeretne importálni?

* [JSON-fájlok](#JSON)
* [MongoDB](#MongoDB)
* [MongoDB-exportfájlok](#MongoDBExport)
* [SQL Server](#SQL)
* [CSV-fájlok](#CSV)
* [Azure Table storage](#AzureTableSource)
* [Amazon DynamoDB](#DynamoDBSource)
* [Blob](#BlobImport)
* [Azure Cosmos-tárolók](#SQLSource)
* [HBase](#HBaseSource)
* [Azure Cosmos DB – tömeges importálás](#SQLBulkTarget)
* [Azure Cosmos DB – szekvenciális rekordimportálás](#SQLSeqTarget)

## <a name="import-json-files"></a><a id="JSON"></a>JSON-fájlok importálása

A JSON-fájl forrásaként szolgáló importáló beállítás lehetővé teszi egy vagy több, JSON-dokumentumok tömbjét tartalmazó JSON-fájl vagy JSON-fájl importálását. Ha olyan mappákat ad hozzá, amelyekben a JSON-fájlok importálhatók, akkor lehetősége van az almappákban található fájlok rekurzív keresésére.

:::image type="content" source="./media/import-data/jsonsource.png" alt-text="Képernyőkép a JSON-fájlok importálási beállításairól – Adatbázis-áttelepítési eszközök":::

A következő formátumú a kapcsolatok karakterlánca:

`AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>`

* A a `<CosmosDB Endpoint>` végpont URI-ja. Ez az érték a Azure Portalból szerezhető be. Navigáljon az Azure Cosmos-fiókjához. Nyissa meg az **Áttekintés** ablaktáblát, és másolja az **URI** értékét.
* A a `<AccountKey>` "password" vagy az **elsődleges kulcs**. Ez az érték a Azure Portalból szerezhető be. Navigáljon az Azure Cosmos-fiókjához. Nyissa meg a **kapcsolatok karakterláncait** vagy a **kulcsok** ablaktáblát, és másolja a "jelszó" vagy az **elsődleges kulcs** értékét.
* A a `<CosmosDB Database>` CosmosDB-adatbázis neve.

Például: `AccountEndpoint=https://myCosmosDBName.documents.azure.com:443/;AccountKey=wJmFRYna6ttQ79ATmrTMKql8vPri84QBiHTt6oinFkZRvoe7Vv81x9sn6zlVlBY10bEPMgGM982wfYXpWXWB9w==;Database=myDatabaseName`

> [!NOTE]
> Az ellenőrzés parancs használatával győződjön meg arról, hogy a kapcsolati karakterlánc mezőben megadott Cosmos DB fiók elérhető.

Néhány parancssori példa JSON-fájlok importálásához:

```console
#Import a single JSON file
dt.exe /s:JsonFile /s.Files:.\Sessions.json /t:DocumentDBBulk /t.ConnectionString:"AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:Sessions /t.CollectionThroughput:2500

#Import a directory of JSON files
dt.exe /s:JsonFile /s.Files:C:\TESessions\*.json /t:DocumentDBBulk /t.ConnectionString:" AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:Sessions /t.CollectionThroughput:2500

#Import a directory (including sub-directories) of JSON files
dt.exe /s:JsonFile /s.Files:C:\LastFMMusic\**\*.json /t:DocumentDBBulk /t.ConnectionString:" AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:Music /t.CollectionThroughput:2500

#Import a directory (single), directory (recursive), and individual JSON files
dt.exe /s:JsonFile /s.Files:C:\Tweets\*.*;C:\LargeDocs\**\*.*;C:\TESessions\Session48172.json;C:\TESessions\Session48173.json;C:\TESessions\Session48174.json;C:\TESessions\Session48175.json;C:\TESessions\Session48177.json /t:DocumentDBBulk /t.ConnectionString:"AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:subs /t.CollectionThroughput:2500

#Import a single JSON file and partition the data across 4 collections
dt.exe /s:JsonFile /s.Files:D:\\CompanyData\\Companies.json /t:DocumentDBBulk /t.ConnectionString:"AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:comp[1-4] /t.PartitionKey:name /t.CollectionThroughput:2500
```

## <a name="import-from-mongodb"></a><a id="MongoDB"></a>Importálás MongoDB-ből

> [!IMPORTANT]
> Ha a MongoDB-hez Azure Cosmos DB API-val konfigurált Cosmos-fiókba importálja, kövesse az alábbi [utasításokat](../dms/tutorial-mongodb-cosmos-db.md?toc=%2fazure%2fcosmos-db%2ftoc.json%253ftoc%253d%2fazure%2fcosmos-db%2ftoc.json).

A MongoDB forrás-importáló lehetőséggel egyetlen MongoDB-gyűjteményből importálhatja, opcionálisan szűrheti a dokumentumokat egy lekérdezés használatával, és kivetítéssel módosíthatja a dokumentum struktúráját.  

:::image type="content" source="./media/import-data/mongodbsource.png" alt-text="Képernyőkép a MongoDB forrás beállításairól":::

A kapcsolati sztring a szabványos MongoDB-formátumot követi:

`mongodb://<dbuser>:<dbpassword>@<host>:<port>/<database>`

> [!NOTE]
> Ellenőrizze a Verify paranccsal, hogy elérhető-e a kapcsolati sztringben megadott MongoDB-példány.

Adja meg annak a gyűjteménynek a nevét, amelyből adatokat kíván importálni. Megadhatja vagy megadhat egy fájlt egy lekérdezéshez, például a `{pop: {$gt:5000}}` vagy egy leképezést (például), `{loc:0}` hogy mindkét szűrőhöz és az importált adatalakzathoz is kiszűrje.

Néhány parancssori példa MongoDB-adatbázisból való importáláshoz:

```console
#Import all documents from a MongoDB collection
dt.exe /s:MongoDB /s.ConnectionString:mongodb://<dbuser>:<dbpassword>@<host>:<port>/<database> /s.Collection:zips /t:DocumentDBBulk /t.ConnectionString:"AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:BulkZips /t.IdField:_id /t.CollectionThroughput:2500

#Import documents from a MongoDB collection which match the query and exclude the loc field
dt.exe /s:MongoDB /s.ConnectionString:mongodb://<dbuser>:<dbpassword>@<host>:<port>/<database> /s.Collection:zips /s.Query:{pop:{$gt:50000}} /s.Projection:{loc:0} /t:DocumentDBBulk /t.ConnectionString:"AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:BulkZipsTransform /t.IdField:_id/t.CollectionThroughput:2500
```

## <a name="import-mongodb-export-files"></a><a id="MongoDBExport"></a>MongoDB-exportfájlok importálása

> [!IMPORTANT]
> Ha a MongoDB-t támogató Azure Cosmos DB-fiókba importál, kövesse az alábbi [utasításokat](../dms/tutorial-mongodb-cosmos-db.md?toc=%2fazure%2fcosmos-db%2ftoc.json%253ftoc%253d%2fazure%2fcosmos-db%2ftoc.json).

Forrásként MongoDB-exportálási JSON-fájlokat választva importálhat egy vagy több olyan JSON-fájlt, amelyet a mongoexport segédprogrammal hozott létre.  

:::image type="content" source="./media/import-data/mongodbexportsource.png" alt-text="Képernyőkép a MongoDB-exportfájlok importálási beállításairól":::

Ha olyan mappákat ad hozzá, amelyek MongoDB exportálnak JSON-fájlokat az importáláshoz, akkor lehetősége van az almappákban található fájlok rekurzív keresésére is.

Néhány parancssori példa MongoDB-exportálási JSON-fájlok importáláshoz:

```console
dt.exe /s:MongoDBExport /s.Files:D:\mongoemployees.json /t:DocumentDBBulk /t.ConnectionString:"AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:employees /t.IdField:_id /t.Dates:Epoch /t.CollectionThroughput:2500
```

## <a name="import-from-sql-server"></a><a id="SQL"></a>Importálás SQL Server-adatbázisból

Forrásként SQL-t választva importálhat egy adott SQL Server-adatbázist, és opcionálisan szűrheti az importálandó rekordokat egy lekérdezéssel. Emellett módosíthatja a dokumentumstruktúrát egy beágyazási elválasztó megadásával (erre alább visszatérünk még).  

:::image type="content" source="./media/import-data/sqlexportsource.png" alt-text="Képernyőkép az SQL forrás beállításairól – Adatbázis-áttelepítési eszközök":::

A kapcsolati sztring a szabványos SQL-kapcsolati sztringek formátumát követi.

> [!NOTE]
> Ellenőrizze a Verify paranccsal, hogy elérhető-e a kapcsolati sztringben megadott SQL Server-példány.

A beágyazási elválasztó tulajdonsággal hierarchikus kapcsolatokat (aldokumentumokat) hozhat létre az importálás során. Vizsgálja meg a következő SQL-lekérdezést:

`select CAST(BusinessEntityID AS varchar) as Id, Name, AddressType as [Address.AddressType], AddressLine1 as [Address.AddressLine1], City as [Address.Location.City], StateProvinceName as [Address.Location.StateProvinceName], PostalCode as [Address.PostalCode], CountryRegionName as [Address.CountryRegionName] from Sales.vStoreWithAddresses WHERE AddressType='Main Office'`

Mely az alábbi (részleges) eredményt adja vissza:

:::image type="content" source="./media/import-data/sqlqueryresults.png" alt-text="Képernyőkép az SQL-lekérdezés eredményéről":::

Megfigyelheti az Address.AddressType és az Address.Location.StateProvinceName áljelet. A „.” beágyazási elválasztó megadása miatt az importáló eszköz létrehozza az Address és az Address.Location aldokumentumot az importálás során. Ez például az alábbi Azure Cosmos DB-beli végleges dokumentumot eredményezheti:

*{ "id": "956", "Name": "Finer Sales and Service", "Address": { "AddressType": "Main Office", "AddressLine1": "#500-75 O'Connor Street", "Location": { "City": "Ottawa", "StateProvinceName": "Ontario" }, "PostalCode": "K4B 1S2", "CountryRegionName": "Canada" } }*

Néhány parancssori példa SQL Server-adatbázisból való importáláshoz:

```console
#Import records from SQL which match a query
dt.exe /s:SQL /s.ConnectionString:"Data Source=<server>;Initial Catalog=AdventureWorks;User Id=advworks;Password=<password>;" /s.Query:"select CAST(BusinessEntityID AS varchar) as Id, * from Sales.vStoreWithAddresses WHERE AddressType='Main Office'" /t:DocumentDBBulk /t.ConnectionString:" AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:Stores /t.IdField:Id /t.CollectionThroughput:2500

#Import records from sql which match a query and create hierarchical relationships
dt.exe /s:SQL /s.ConnectionString:"Data Source=<server>;Initial Catalog=AdventureWorks;User Id=advworks;Password=<password>;" /s.Query:"select CAST(BusinessEntityID AS varchar) as Id, Name, AddressType as [Address.AddressType], AddressLine1 as [Address.AddressLine1], City as [Address.Location.City], StateProvinceName as [Address.Location.StateProvinceName], PostalCode as [Address.PostalCode], CountryRegionName as [Address.CountryRegionName] from Sales.vStoreWithAddresses WHERE AddressType='Main Office'" /s.NestingSeparator:. /t:DocumentDBBulk /t.ConnectionString:" AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:StoresSub /t.IdField:Id /t.CollectionThroughput:2500
```

## <a name="import-csv-files-and-convert-csv-to-json"></a><a id="CSV"></a>CSV-fájlok importálása és CSV-fájlok konvertálása JSON formátumba

Forrásként CSV-fájlt választva importálhat egy vagy több CSV-fájlt. Ha CSV-fájlokat tartalmazó mappákat ad hozzá az importáláshoz, akkor lehetősége van az almappákban található fájlok rekurzív keresésére is.

:::image type="content" source="media/import-data/csvsource.png" alt-text="Képernyőkép a CSV forrás beállításairól – CSV konvertálása JSON formátumba":::

Az SQL forráshoz hasonlóan itt is használhatja a beágyazási elválasztó tulajdonságot, ha hierarchikus kapcsolatokat (aldokumentumokat) kíván létrehozni az importálás során. Vizsgálja meg például az alábbi CSV-fejlécsort és -adatsorokat:

:::image type="content" source="./media/import-data/csvsample.png" alt-text="Képernyőkép a CSV-mintarekordokról – CSV konvertálása JSON formátumba":::

Megfigyelheti a DomainInfo.Domain_Name és a RedirectInfo.Redirecting áljelet. A „.” beágyazási elválasztó megadása miatt az importáló eszköz létrehozza a DomainInfo és a RedirectInfo aldokumentumot az importálás során. Ez például az alábbi Azure Cosmos DB-beli végleges dokumentumot eredményezheti:

*{"DomainInfo": {"Domain_Name": "ACUS.GOV", "Domain_Name_Address": "https: \/ /www.acus.gov"}, "szövetségi ügynökség": "a Egyesült Államok felügyeleti Konferenciája", "RedirectInfo": {"átirányítás": "0", "Redirect_Destination": ""}, "id": "9cc565c5-EBCD-1c03-ebd3-cc3e2ecd814d"}*

Az importálási eszköz kísérletet tesz a CSV-fájlokban lévő nem jegyzett értékek típusára vonatkozó információk következtetésére (az idézőjeles értékeket a rendszer mindig karakterláncként kezeli).  A típusokat a következő sorrendben azonosítja: szám, datetime, boolean.  

Két további dolgot is fontos tudnia a CSV-importálásról:

1. Az eszköz a nem idézőjeles értékekről alapértelmezés szerint mindig levágja a tabulátor- és szóközkaraktereket, míg az idézőjeles értékeket pontosan megőrzi. Ezt a viselkedést felülbírálhatja a „Trim quoted values” (Idézőjeles értékek csonkolása) jelölőnégyzettel vagy a /s.TrimQuoted parancssori kapcsolóval.
2. Alapértelmezés szerint a nem idézőjeles nullértéket null értékként kezeli az eszköz. Ezt a viselkedést felülbírálhatja (vagyis a nem idézőjeles nullértéket egy „null” sztringként kezelheti) a „Treat unquoted NULL as string” (Nem idézőjeles NULL kezelése sztringként) jelölőnégyzettel vagy a /s.NoUnquotedNulls parancssori kapcsolóval.

Parancssori példa CSV-fájl importáláshoz:

```console
dt.exe /s:CsvFile /s.Files:.\Employees.csv /t:DocumentDBBulk /t.ConnectionString:"AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:Employees /t.IdField:EntityID /t.CollectionThroughput:2500
```

## <a name="import-from-azure-table-storage"></a><a id="AzureTableSource"></a>Importálás Azure Table Storage-ből

Forrás Azure Table Storage-et választva importálhat egy adott Azure Table Storage-táblát. Igény esetén szűrheti is az importálandó táblaentitásokat.

Az Azure Table Storageból importált adatokat Azure Cosmos DB táblákat és entitásokat is elvégezheti a Table API való használatra. Az importált adatokat gyűjteményekhez és dokumentumokhoz is lehet exportálni az SQL API-val való használatra. Table API azonban csak a parancssori segédprogramban érhető el célként. Az adatáttelepítési eszköz felhasználói felületének használatával nem lehet exportálni Table APIra. További információért olvassa el az [Adatok importálása az Azure Cosmos DB Table API-val való használathoz](table-import.md) című témakört.

:::image type="content" source="./media/import-data/azuretablesource.png" alt-text="Képernyőkép az Azure Table Storage forrás beállításairól":::

Az Azure Table Storage kapcsolati sztringjének formátuma a következő:

`DefaultEndpointsProtocol=<protocol>;AccountName=<Account Name>;AccountKey=<Account Key>;`

> [!NOTE]
> Ellenőrizze a Verify paranccsal, hogy elérhető-e a kapcsolati sztringben megadott Azure Table Storage-példány.

Adja meg annak az Azure-táblának a nevét, amelyből importálni kíván. Igény esetén adhat meg egy [szűrőt](/visualstudio/azure/vs-azure-tools-table-designer-construct-filter-strings) is.

Az Azure Table Storage importálási forráshoz a következő további beállításokat adhatja meg:

1. Include Internal Fields (Belső mezők belefoglalása)
   1. All (Mind) – Az összes belső mező (PartitionKey, RowKey és Timestamp) belefoglalása
   2. None (Egyik sem) – Az összes belső mező kizárása
   3. RowKey – Csak a RowKey mező belefoglalása
2. Select Columns (Oszlopok kijelölése)
   1. Az Azure Table Storage-szűrők nem támogatják a kivetítéseket. Ha csak bizonyos Azure Table Storage-entitástulajdonságokat szeretne importálni, adja őket hozzá a Select Columns listához. Az összes többi entitástulajdonságot figyelmen kívül hagyja az eszköz.

Parancssori példa Azure Table Storage-ből való importáláshoz:

```console
dt.exe /s:AzureTable /s.ConnectionString:"DefaultEndpointsProtocol=https;AccountName=<Account Name>;AccountKey=<Account Key>" /s.Table:metrics /s.InternalFields:All /s.Filter:"PartitionKey eq 'Partition1' and RowKey gt '00001'" /s.Projection:ObjectCount;ObjectSize  /t:DocumentDBBulk /t.ConnectionString:" AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:metrics /t.CollectionThroughput:2500
```

## <a name="import-from-amazon-dynamodb"></a><a id="DynamoDBSource"></a>Importálás Amazon DynamoDB-ből

Az Amazon DynamoDB forrás-importáló lehetőség lehetővé teszi egyetlen Amazon DynamoDB-táblázatból való importálást. Igény szerint szűrheti az importálandó entitásokat. Számos beépített sablon a rendelkezésére áll, hogy a lehető legegyszerűbben beállíthassa az importálást.

:::image type="content" source="./media/import-data/dynamodbsource1.png" alt-text="Képernyőkép az Amazon DynamoDB forrás lehetőségeiről – adatbázis-áttelepítési eszközök.":::

:::image type="content" source="./media/import-data/dynamodbsource2.png" alt-text="Képernyőkép az Amazon DynamoDB-forrás lehetőségeiről sablon – adatbázis-áttelepítési eszközök.":::

Az Amazon DynamoDB kapcsolati sztringjének formátuma a következő:

`ServiceURL=<Service Address>;AccessKey=<Access Key>;SecretKey=<Secret Key>;`

> [!NOTE]
> Ellenőrizze a Verify paranccsal, hogy elérhető-e a kapcsolati sztringben megadott Amazon DynamoDB-példány.

Parancssori példa Amazon DynamoDB-ből való importáláshoz:

```console
dt.exe /s:DynamoDB /s.ConnectionString:ServiceURL=https://dynamodb.us-east-1.amazonaws.com;AccessKey=<accessKey>;SecretKey=<secretKey> /s.Request:"{   """TableName""": """ProductCatalog""" }" /t:DocumentDBBulk /t.ConnectionString:"AccountEndpoint=<Azure Cosmos DB Endpoint>;AccountKey=<Azure Cosmos DB Key>;Database=<Azure Cosmos database>;" /t.Collection:catalogCollection /t.CollectionThroughput:2500
```

## <a name="import-from-azure-blob-storage"></a><a id="BlobImport"></a>Importálás Azure Blob Storage-ből

Forrásként JSON-fájlt, MongoDB-exportfájlt vagy CSV-fájlt választva importálhat egy vagy több Azure Blob Storage-beli fájlt. Miután megadta a blobtároló URL-címét és fiókkulcsát, adjon meg egy reguláris kifejezést is az importálandó fájlok kijelöléséhez.

:::image type="content" source="./media/import-data/blobsource.png" alt-text="Képernyőkép a Blob forrás beállításairól":::

Parancssori példa Azure Blob Storage-beli JSON-fájlok importáláshoz:

```console
dt.exe /s:JsonFile /s.Files:"blobs://<account key>@account.blob.core.windows.net:443/importcontainer/.*" /t:DocumentDBBulk /t.ConnectionString:"AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:doctest
```

## <a name="import-from-a-sql-api-collection"></a><a id="SQLSource"></a>Importálás egy SQL API-gyűjteményből

A Azure Cosmos DB forrás-importáló lehetőséggel adatok importálhatók egy vagy több Azure Cosmos-tárolóból, és opcionálisan szűrheti a dokumentumokat egy lekérdezés használatával.  

:::image type="content" source="./media/import-data/documentdbsource.png" alt-text="Képernyőkép az Azure Cosmos DB forrás beállításairól":::

Az Azure Cosmos DB kapcsolati sztringjének formátuma a következő:

`AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;`

A Azure Cosmos DB fiók kapcsolódási karakterláncát a Azure Portal kulcsok lapjáról kérheti le, a következő témakörben leírtak szerint: [Azure Cosmos db fiók kezelése](./how-to-manage-database-account.md). Az adatbázis nevét azonban a következő formátumban kell hozzáfűzni a kapcsolódási karakterlánchoz:

`Database=<CosmosDB Database>;`

> [!NOTE]
> Ellenőrizze a Verify paranccsal, hogy elérhető-e a kapcsolati sztringben megadott Azure Cosmos DB-példány.

Egyetlen Azure Cosmos-tárolóból való importáláshoz adja meg annak a gyűjteménynek a nevét, amelyből az adatok importálhatók. Ha egynél több Azure Cosmos-tárolóból szeretne importálni, adjon meg egy reguláris kifejezést, hogy egyezzen egy vagy több gyűjtemény nevével (például collection01 | collection02 | collection03). Opcionálisan megadhatja vagy megadhat egy fájlt a lekérdezéshez, amely az importált adatszűrési és-formázási lekérdezést is megadja.

> [!NOTE]
> Mivel a gyűjtemény mező fogadja a reguláris kifejezéseket, ha egyetlen gyűjteményből importál, amelynek a neve reguláris kifejezéssel rendelkezik, akkor ezeket a karaktereket ennek megfelelően kell megszökni.

Az Azure Cosmos DB importálási forráshoz a következő speciális beállításokat adhatja meg:

1. Include Internal Fields (Belső mezők belefoglalása): Meghatározza, hogy az exportálás tartalmazza-e az Azure Cosmos DB dokumentumrendszerének tulajdonságait (például _rid, _ts).
2. Number of Retries on Failure (Újrapróbálkozások számára sikertelenség esetén): Meghatározza, hogy az eszköz hány alkalommal kísérelje meg ismét az Azure Cosmos DB-hez való csatlakozást átmeneti hiba fellépése esetén (például ha megszakad a hálózati kapcsolat).
3. Retry Interval (Újrapróbálkozások időköze): Meghatározza, hogy az eszköz mennyi ideig várjon, mielőtt ismét megkísérli az Azure Cosmos DB-hez való kapcsolódást átmeneti hiba fellépése esetén (például ha megszakad a hálózati kapcsolat).
4. Connection Mode (Kapcsolatmód): Meghatározza az Azure Cosmos DB-hez használandó kapcsolatmódot. Az elérhető lehetőségek: DirectTcp (Közvetlen TCP), DirectHttps (Közvetlen HTTPS) és Gateway (Átjáró). A közvetlen kapcsolatmódok gyorsabbak, míg az átjárómód könnyebben használható tűzfalon keresztül, mivel csak a 443-as portot használja.

:::image type="content" source="./media/import-data/documentdbsourceoptions.png" alt-text="Képernyőkép az Azure Cosmos DB forrás speciális beállításairól":::

> [!TIP]
> Az importálási eszköz alapértelmezés szerint DirectTcp módot használ. Ha tűzfalproblémákat tapasztal, váltson Gateway kapcsolatmódra, mely csak a 443-as portot használja.

Néhány parancssori példa Azure Cosmos DB-ből való importáláshoz:

```console
#Migrate data from one Azure Cosmos container to another Azure Cosmos containers
dt.exe /s:DocumentDB /s.ConnectionString:"AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /s.Collection:TEColl /t:DocumentDBBulk /t.ConnectionString:" AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:TESessions /t.CollectionThroughput:2500

#Migrate data from more than one Azure Cosmos container to a single Azure Cosmos container
dt.exe /s:DocumentDB /s.ConnectionString:"AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /s.Collection:comp1|comp2|comp3|comp4 /t:DocumentDBBulk /t.ConnectionString:"AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:singleCollection /t.CollectionThroughput:2500

#Export an Azure Cosmos container to a JSON file
dt.exe /s:DocumentDB /s.ConnectionString:"AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /s.Collection:StoresSub /t:JsonFile /t.File:StoresExport.json /t.Overwrite
```

> [!TIP]
> Az Azure Cosmos DB adatimportálási eszköze támogatja az adatok [Azure Cosmos DB Emulatorból](local-emulator.md) való importálását is. Adatok helyi emulátorból való importálásakor állítsa a végpontot a `https://localhost:<port>` értékre.

## <a name="import-from-hbase"></a><a id="HBaseSource"></a>Importálás HBase-ből

Forrásként HBase-t választva importálhat adatokat egy HBase-táblából, és opcionálisan szűrheti az adatokat. Számos beépített sablon a rendelkezésére áll, hogy a lehető legegyszerűbben beállíthassa az importálást.

:::image type="content" source="./media/import-data/hbasesource1.png" alt-text="Képernyőkép a HBase-forrás lehetőségeiről.":::

:::image type="content" source="./media/import-data/hbasesource2.png" alt-text="Képernyőkép a HBase-forrás lehetőségeiről a helyi szűrési menü kibontásával.":::

A HBase Stargate kapcsolati sztringjének formátuma a következő:

`ServiceURL=<server-address>;Username=<username>;Password=<password>`

> [!NOTE]
> Ellenőrizze a Verify paranccsal, hogy elérhető-e a kapcsolati sztringben megadott HBase-példány.

Parancssori példa HBase-ből való importáláshoz:

```console
dt.exe /s:HBase /s.ConnectionString:ServiceURL=<server-address>;Username=<username>;Password=<password> /s.Table:Contacts /t:DocumentDBBulk /t.ConnectionString:"AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:hbaseimport
```

## <a name="import-to-the-sql-api-bulk-import"></a><a id="SQLBulkTarget"></a>Importálás SQL API-ba (tömeges importálás)

Az Azure Cosmos DB tömeges importálójával egy Azure Cosmos DB-beli tárolt eljárás segítségével hatékonyabban importálhat bármelyik választható forrásból. Az eszköz támogatja az importálást egyetlen particionált Azure Cosmos-tárolóba. Támogatja továbbá a többrétegű importálást is, amely során az adatparticionálás több, egypartíciós Azure Cosmos-tárolón keresztül történik. Az adatok particionálásával kapcsolatban további információkat az [Azure Cosmos DB particionálási és méretezési eljárásait](partitioning-overview.md) ismertető cikkben talál. Az eszköz létrehozza, végrehajtja, majd törli a tárolt eljárást a célgyűjtemény(ek)ben.  

:::image type="content" source="./media/import-data/documentdbbulk.png" alt-text="Képernyőkép az Azure Cosmos DB tömeges importálási beállításairól":::

Az Azure Cosmos DB kapcsolati sztringjének formátuma a következő:

`AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;`

Az Azure Cosmos DB-fiók kapcsolati sztringjét beszerezheti az Azure Portal Kulcsok lapjáról az [Azure Cosmos DB-fiók kezelése](./how-to-manage-database-account.md) című cikkben ismertetett módon, az adatbázis nevét azonban hozzá kell fűznie a kapcsolati sztringhez a következő formátumot követve:

`Database=<CosmosDB Database>;`

> [!NOTE]
> Ellenőrizze a Verify paranccsal, hogy elérhető-e a kapcsolati sztringben megadott Azure Cosmos DB-példány.

Egyetlen gyűjtemény importálásához írja be annak a gyűjteménynek a nevét, amelyből importálni kívánja az adatokat, majd kattintson az Add (Hozzáadás) gombra. Ha egynél több gyűjteménybe szeretne importálni, egyenként adja meg az egyes gyűjtemények nevét, vagy használja a következő szintaxist egynél több gyűjtemény megadásához: *collection_prefix*[index-End index]. Ha egynél több gyűjteményt ad meg a fenti szintaxissal, tartsa szem előtt a következő irányelveket:

1. Csak az integer adattípuson alapuló minták támogatottak. A „gyűjtemény[0-3]” minta például a következő gyűjteményeket hozza létre: gyűjtemény0, gyűjtemény1, gyűjtemény2, gyűjtemény3.
2. Használhat rövidített szintaxist: A „gyűjtemény[3]” minta ugyanúgy az 1. lépésben említett gyűjteménykészletet hozza létre.
3. Adhat meg több behelyettesítést is. A „gyűjtemény[0-1] [0-9]” minta például 20 gyűjteménynevet generál kezdő nulla használatával (gyűjtemény01, ..02, ..03).

A gyűjteménynév (vagy nevek) megadása után válassza ki a gyűjtemény(ek)hez használni kívánt átviteli sebességet (400 – 10 000 kérelemegység). Nagyobb importálási teljesítményhez válasszon magasabb átviteli sebességet. A teljesítményszintekről bővebben [Az Azure Cosmos DB teljesítményszintjei](performance-levels.md) című témakörben tájékozódhat.

> [!NOTE]
> A beállított átviteli sebesség csak a gyűjtemény létrehozásakor érvényesül. Ha a megadott gyűjtemény már létezik, az átviteli sebessége nem módosul.

Ha egynél több gyűjteménybe importál, az importálási eszköz támogatja a kivonatoló skálázást. Ebben az esetben a partíciós kulcsként használni kívánt dokumentum-tulajdonságot kell megadnia. (Ha a partíciós kulcs üresen marad, a rendszer véletlenszerűen felosztja a dokumentumokat a cél gyűjtemények között.)

Az importálás során megadhatja, hogy az importálási forrás melyik mezőjét használja Azure Cosmos DB Document ID tulajdonságként. Ha a dokumentumok nem rendelkeznek ezzel a tulajdonsággal, az importálási eszköz azonosító tulajdonság értékeként létrehoz egy GUID azonosítót.

Elérhető néhány speciális beállítás is importáláskor. Például az eszköz tartalmaz egy alapértelmezett tömeges importálási tárolt eljárást (BulkInsert.js), de adhat meg helyette saját importálási tárolt eljárást is:

 :::image type="content" source="./media/import-data/bulkinsertsp.png" alt-text="Képernyőkép az Azure Cosmos DB tömeges beszúrási tárolt eljárásának beállításáról":::

Emellett dátum típusú értékek importálásakor (például SQL Serverből vagy MongoDB-ből) három különböző importálási lehetőség közül választhat:

 :::image type="content" source="./media/import-data/datetimeoptions.png" alt-text="Képernyőkép az Azure Cosmos DB importálási dátum- és időbeállításáról":::

* String (Sztring): Megőrzés sztringértékként
* Epoch (Alapidőszak): Megőrzés Epoch típusú számértékként
* Both (Mindkettő): Megőrzés mind sztringként, mind pedig Epoch típusú számértékként. Ez a beállítás létrehoz egy aldokumentumot, például: "date_joined": { "Value": "2013-10-21T21:17:25.2410000Z", "Epoch": 1382390245 }

Az Azure Cosmos DB tömeges importálási eszköze a következő speciális beállításokat támogatja:

1. Batch Size (Kötegméret): Az alapértelmezett kötegméret 50.  Nagyméretű dokumentumok importálásakor célszerű csökkenteni a kötegméretet, kisméretű dokumentumok importálásakor pedig növelni a kötegméretet.
2. Max Script Size (bytes) (Maximális szkriptfájlméret (bájt)): Az alapértelmezett maximális szkriptfájlméret 512 KB.
3. Automatikus azonosító generálásának letiltása: Ha minden importálandó dokumentum rendelkezik azonosító mezővel, akkor ezzel a beállítással növelheti a teljesítményt. Az egyedi azonosító mezőből hiányzó dokumentumok importálása nem történt meg.
4. Meglévő dokumentumok frissítése: az eszköz alapértelmezés szerint nem cseréli le a meglévő dokumentumokat azonosító ütközésekkel. Ha ezt a lehetőséget választja, a felülírja a meglévő dokumentumokat a megfelelő azonosítókkal. Ez a funkció a meglévő dokumentumokat frissítő ütemezett adatáttelepítésekhez hasznos.
5. Sikertelen újrapróbálkozások száma: azt határozza meg, hogy a rendszer milyen gyakran próbálkozzon újra a kapcsolattal Azure Cosmos DB az átmeneti hibák (például a hálózati kapcsolat megszakítása) során.
6. Retry Interval (Újrapróbálkozások időköze): Meghatározza, hogy az eszköz mennyi ideig várjon, mielőtt ismét megkísérli az Azure Cosmos DB-hez való kapcsolódást átmeneti hiba fellépése esetén (például ha megszakad a hálózati kapcsolat).
7. Connection Mode (Kapcsolatmód): Meghatározza az Azure Cosmos DB-hez használandó kapcsolatmódot. Az elérhető lehetőségek: DirectTcp (Közvetlen TCP), DirectHttps (Közvetlen HTTPS) és Gateway (Átjáró). A közvetlen kapcsolatmódok gyorsabbak, míg az átjárómód könnyebben használható tűzfalon keresztül, mivel csak a 443-as portot használja.

:::image type="content" source="./media/import-data/docdbbulkoptions.png" alt-text="Képernyőkép az Azure Cosmos DB speciális tömeges importálási beállításairól":::

> [!TIP]
> Az importálási eszköz alapértelmezés szerint DirectTcp módot használ. Ha tűzfalproblémákat tapasztal, váltson Gateway kapcsolatmódra, mely csak a 443-as portot használja.

## <a name="import-to-the-sql-api-sequential-record-import"></a><a id="SQLSeqTarget"></a>Importálás az SQL API-ba (szekvenciális rekordimportálás)

Az Azure Cosmos DB szekvenciális rekord-importáló lehetővé teszi, hogy egy rekord-rekord alapján importáljon egy rendelkezésre álló forrás lehetőségből. Ez a lehetőség akkor lehet hasznos, ha olyan meglévő gyűjteménybe importál, amelynek már kimerült a tárolt eljárási kvótája. Az eszköz támogatja az importálást egyetlen (egypartíciós és több partíciós) Azure Cosmos-tárolóban. Emellett támogatja a többrétegű importálást is, amely során az adatparticionálás több, mint egy partíciós vagy több partíciós Azure Cosmos-tárolón keresztül történik. Az adatok particionálásával kapcsolatban további információkat az [Azure Cosmos DB particionálási és méretezési eljárásait](partitioning-overview.md) ismertető cikkben talál.

:::image type="content" source="./media/import-data/documentdbsequential.png" alt-text="Képernyőkép az Azure Cosmos DB szekvenciális rekordimportálási beállításairól":::

Az Azure Cosmos DB kapcsolati sztringjének formátuma a következő:

`AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;`

A Azure Cosmos DB fiókhoz tartozó kapcsolódási karakterláncot a Azure Portal kulcsok lapjáról kérheti le, a következő témakörben leírtak szerint: [Azure Cosmos db fiók kezelése](./how-to-manage-database-account.md). Az adatbázis nevét azonban a következő formátumban kell hozzáfűzni a kapcsolódási karakterlánchoz:

`Database=<Azure Cosmos database>;`

> [!NOTE]
> Ellenőrizze a Verify paranccsal, hogy elérhető-e a kapcsolati sztringben megadott Azure Cosmos DB-példány.

Egyetlen gyűjteménybe való importáláshoz adja meg annak a gyűjteménynek a nevét, amelybe az adatimportálást be szeretné írni, majd kattintson a Hozzáadás gombra. Ha több gyűjteménybe szeretne importálni, egyenként adja meg az egyes gyűjtemények nevét. A következő szintaxist is használhatja több gyűjtemény megadásához: *collection_prefix*[index-End index]. Ha egynél több gyűjteményt ad meg a fenti szintaxison keresztül, tartsa szem előtt a következő irányelveket:

1. Csak az integer adattípuson alapuló minták támogatottak. A „gyűjtemény[0-3]” minta például a következő gyűjteményeket hozza létre: gyűjtemény0, gyűjtemény1, gyűjtemény2, gyűjtemény3.
2. Használhat rövidített szintaxist: A „gyűjtemény[3]” minta ugyanúgy az 1. lépésben említett gyűjteménykészletet hozza létre.
3. Adhat meg több behelyettesítést is. A „gyűjtemény[0-1] [0-9]” minta például 20 gyűjteménynevet hoz létre kezdő nulla használatával (gyűjtemény01, ..02, ..03).

A gyűjteménynév (vagy nevek) megadása után válassza ki a gyűjtemény(ek)hez használni kívánt átviteli sebességet (400 – 250 000 kérelemegység). Nagyobb importálási teljesítményhez válasszon magasabb átviteli sebességet. A teljesítményszintekről bővebben [Az Azure Cosmos DB teljesítményszintjei](performance-levels.md) című témakörben tájékozódhat. A 10 000 kérelemegységnél magasabb átviteli sebességű gyűjteményekbe való importáláshoz partíciókulcsot kell használnia. Ha 250 000 kérelemegységet meghaladó átviteli sebességet kíván használni, akkor külön kérelmeznie kell a portálon a növelés végrehajtását.

> [!NOTE]
> A beállított átviteli sebesség csak a gyűjtemény vagy adatbázis létrehozásakor érvényesül. Ha a megadott gyűjtemény már létezik, az átviteli sebessége nem módosul.

Egynél több gyűjteménybe történő importálás esetén az importálási eszköz támogatja a kivonatoló skálázást. Ebben az esetben a partíciós kulcsként használni kívánt dokumentum-tulajdonságot kell megadnia. (Ha a partíciós kulcs üresen marad, a rendszer véletlenszerűen felosztja a dokumentumokat a cél gyűjtemények között.)

Az importálás során megadhatja, hogy az importálási forrás melyik mezőjét használja Azure Cosmos DB Document ID tulajdonságként. (Ha a dokumentumok nem rendelkeznek ezzel a tulajdonsággal, az importálási eszköz azonosító tulajdonság értékeként létrehoz egy GUID azonosítót.)

Elérhető néhány speciális beállítás is importáláskor. Dátum típusú értékek importálásakor (például SQL Serverből vagy MongoDB-ből) három különböző importálási lehetőség közül választhat:

 :::image type="content" source="./media/import-data/datetimeoptions.png" alt-text="Képernyőkép az Azure Cosmos DB importálási dátum- és időbeállításáról":::

* String (Sztring): Megőrzés sztringértékként
* Epoch (Alapidőszak): Megőrzés Epoch típusú számértékként
* Both (Mindkettő): Megőrzés mind sztringként, mind pedig Epoch típusú számértékként. Ez a beállítás létrehoz egy aldokumentumot, például: "date_joined": { "Value": "2013-10-21T21:17:25.2410000Z", "Epoch": 1382390245 }

Az Azure Cosmos DB szekvenciális rekordimportáló eszköze a következő speciális beállításokat támogatja:

1. Number of Parallel Requests (Párhuzamos kérelmek száma): A párhuzamos kérelmek alapértelmezett száma kettő. Kisméretű dokumentumok importálásakor célszerű növelni a párhuzamos kérelmek számát. Ha azonban túl nagy értéket ad meg, akkor a rendszer korlátozhatja az importálás sebességét.
2. Automatikus azonosító generálásának letiltása: Ha minden importálandó dokumentum rendelkezik azonosító mezővel, akkor ezzel a beállítással növelheti a teljesítményt. Az egyedi azonosító mezőből hiányzó dokumentumok importálása nem történt meg.
3. Meglévő dokumentumok frissítése: az eszköz alapértelmezés szerint nem cseréli le a meglévő dokumentumokat azonosító ütközésekkel. Ha ezt a lehetőséget választja, a felülírja a meglévő dokumentumokat a megfelelő azonosítókkal. Ez a funkció a meglévő dokumentumokat frissítő ütemezett adatáttelepítésekhez hasznos.
4. Sikertelen újrapróbálkozások száma: azt határozza meg, hogy a rendszer milyen gyakran próbálkozzon újra a kapcsolattal Azure Cosmos DB az átmeneti hibák (például a hálózati kapcsolat megszakítása) során.
5. Újrapróbálkozási időköz: azt határozza meg, hogy mennyi ideig kell várni az átmeneti hibák (például a hálózati kapcsolat megszakítása) során Azure Cosmos DB kapcsolat ismételt megkísérlése között.
6. Connection Mode (Kapcsolatmód): Meghatározza az Azure Cosmos DB-hez használandó kapcsolatmódot. Az elérhető lehetőségek: DirectTcp (Közvetlen TCP), DirectHttps (Közvetlen HTTPS) és Gateway (Átjáró). A közvetlen kapcsolatmódok gyorsabbak, míg az átjárómód könnyebben használható tűzfalon keresztül, mivel csak a 443-as portot használja.

:::image type="content" source="./media/import-data/documentdbsequentialoptions.png" alt-text="Képernyőkép az Azure Cosmos DB speciális szekvenciálisrekord-importálási beállításairól":::

> [!TIP]
> Az importálási eszköz alapértelmezés szerint DirectTcp módot használ. Ha tűzfalproblémákat tapasztal, váltson Gateway kapcsolatmódra, mely csak a 443-as portot használja.

## <a name="specify-an-indexing-policy"></a><a id="IndexingPolicy"></a>Indexelési házirend megadása

Ha engedélyezi az áttelepítési eszköznek az Azure Cosmos DB SQL API-gyűjtemények létrehozását az importálás során, megadhatja a gyűjtemények indexelési házirendjét. Az Azure Cosmos DB tömeges importálójának és az Azure Cosmos DB szekvenciális rekordimportálójának speciális beállításokat tartalmazó területén keresse meg az Indexing Policy (Indexelési házirend) szakaszt.

:::image type="content" source="./media/import-data/indexingpolicy1.png" alt-text="Képernyőkép a Azure Cosmos DB indexelési házirend speciális beállításairól.":::

Az Indexing Policy speciális beállításnál megadhatja az indexelési házirend fájlját, írhat be manuálisan egy indexelési házirendet, valamint az alapértelmezetten elérhető sablonok közül is választhat egyet (az indexelési házirend mezőjére jobb gombbal kattintva).

Az eszköz az alábbi házirendsablonokat tartalmazza beépítve:

* Default (Alapértelmezett): Ez a házirend a legjobb, ha a sztringekkel kapcsolatos esélyegyenlőségi lekérdezéseket hajt végre. Akkor is működik, ha ORDER BY, Range és Esélyegyenlőségi lekérdezéseket használ a számokhoz. Alacsonyabb indextárolási terhelést nyújt, mint a Range házirend.
* Range (Tartomány): Ez a szabályzat akkor a legjobb, ha ORDER BY, Range és Esélyegyenlőségi lekérdezéseket használ a számok és a karakterláncok esetében. Magasabb indextárolási terheléssel jár, mint a Default és a Hash házirend.

:::image type="content" source="./media/import-data/indexingpolicy2.png" alt-text="Képernyőkép a Azure Cosmos DB indexelési házirendje speciális beállítások megadásával megadhatja a cél adatait.":::

> [!NOTE]
> Ha nem ad meg indexelési házirendet, a rendszer az alapértelmezett házirendet alkalmazza. Az indexelési házirendekről bővebben [Az Azure Cosmos DB indexelési házirendjei](index-policy.md) című cikkben tájékozódhat.

## <a name="export-to-json-file"></a>Exportálás JSON-fájlba

A Azure Cosmos DB JSON-exportőr lehetővé teszi, hogy a rendelkezésre álló források bármelyikét egy JSON-dokumentumba exportálja. Az eszköz kezeli az exportálást. Dönthet úgy is, hogy megtekinti az eredményül kapott áttelepítési parancsot, és saját maga futtatja a parancsot. A művelet során kapott JSON-fájlt tárolhatja helyben vagy Azure Blob Storage-fiókban.

:::image type="content" source="./media/import-data/jsontarget.png" alt-text="Képernyőkép az Azure Cosmos DB helyi JSON-fájlba való exportálási beállításáról":::

:::image type="content" source="./media/import-data/jsontarget2.png" alt-text="Képernyőkép az Azure Cosmos DB Azure Blob Storage-beli JSON-fájlba való exportálási beállításáról":::

Dönthet úgy is, hogy szépít az eredményül kapott JSON-t. Ez a művelet növeli az eredményül kapott dokumentum méretét, miközben a tartalom több ember számára olvasható lesz.

* Szabványos JSON-exportálás

  ```JSON
  [{"id":"Sample","Title":"About Paris","Language":{"Name":"English"},"Author":{"Name":"Don","Location":{"City":"Paris","Country":"France"}},"Content":"Don's document in Azure Cosmos DB is a valid JSON document as defined by the JSON spec.","PageViews":10000,"Topics":[{"Title":"History of Paris"},{"Title":"Places to see in Paris"}]}]
  ```

* Prettified JSON-exportálás

  ```JSON
    [
     {
    "id": "Sample",
    "Title": "About Paris",
    "Language": {
      "Name": "English"
    },
    "Author": {
      "Name": "Don",
      "Location": {
        "City": "Paris",
        "Country": "France"
      }
    },
    "Content": "Don's document in Azure Cosmos DB is a valid JSON document as defined by the JSON spec.",
    "PageViews": 10000,
    "Topics": [
      {
        "Title": "History of Paris"
      },
      {
        "Title": "Places to see in Paris"
      }
    ]
    }]
  ```

Parancssori példa a JSON-fájl Azure Blob Storage-ba történő exportálásához:

```console
dt.exe /ErrorDetails:All /s:DocumentDB /s.ConnectionString:"AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB database_name>" /s.Collection:<CosmosDB collection_name>
/t:JsonFile /t.File:"blobs://<Storage account key>@<Storage account name>.blob.core.windows.net:443/<Container_name>/<Blob_name>"
/t.Overwrite
```

## <a name="advanced-configuration"></a>Speciális konfiguráció

Adja meg a speciális konfigurációs képernyőn annak a naplófájlnak a helyét, amelybe az esetlegesen előforduló hibákat szeretné menteni. Az alábbi szabályok érvényesek erre a lapra:

1. Ha nincs megadva fájlnév, a rendszer minden hibát visszaadott az eredmények lapon.
2. Ha könyvtárnév nélküli fájlnevet ad meg, akkor a fájlt az eszköz az aktuális környezeti könyvtárban hozza létre (vagy írja felül).
3. Ha kijelöl egy meglévő fájlt, a fájl felülíródik, nincs hozzáfűzési lehetőség.
4. Ezután adja meg, hogy az eszköz mit naplózzon: az összes üzenetet, csak a kritikus üzeneteket vagy egyetlen hibaüzenetet sem. Végül döntse el, hogy milyen gyakorisággal frissüljön a képernyőn látható átviteli üzenet a művelet előrehaladása során.

   :::image type="content" source="./media/import-data/AdvancedConfiguration.png" alt-text="A Speciális konfiguráció képernyő képernyőképe":::

## <a name="confirm-import-settings-and-view-command-line"></a>Importálási beállítások megerősítése és a parancssor megtekintése

1. A forrásadatok, a célhelyek és a speciális beállítások megadását követően tekintse át az áttelepítés összegzését, és ha kívánja, tekintse át vagy másolja át az eredményül kapott áttelepítési parancsot. (A parancs másolása hasznos az importálási műveletek automatizálásához.)

    :::image type="content" source="./media/import-data/summary.png" alt-text="Képernyőkép az összegző képernyőről.":::

    :::image type="content" source="./media/import-data/summarycommand.png" alt-text="Képernyőkép az összegző képernyőről a parancssor előzetes verziójával.":::

2. Ha elégedett a forrás- és célbeállításokkal, válassza az **Import** (Importálás) gombot. Az importálás előrehaladása során figyelemmel kísérheti a képernyőn az eltelt időt, az átvitt elemek számát és a hibákkal kapcsolatos információkat (ha nem adott meg fájlnevet a speciális konfigurációs lapon). Ha a művelet kész, exportálhatja az eredményt (például a sikertelen importálású elemek feldolgozásához).

    :::image type="content" source="./media/import-data/viewresults.png" alt-text="Képernyőkép Azure Cosmos DB JSON-exportálási lehetőségről.":::

3. Új importálást is indíthat, ha alaphelyzetbe állítja az összes értéket, vagy megtartja a meglévő beállításokat. (Például dönthet úgy, hogy megtartja a kapcsolódási karakterlánc információit, a forrás és a cél választását stb.)

    :::image type="content" source="./media/import-data/newimport.png" alt-text="Képernyőkép a Azure Cosmos DB JSON-exportálási lehetőségről az új importálás megerősítése párbeszédpanellel.":::

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban a következőket hajtotta végre:

> [!div class="checklist"]
> * Az adatáttelepítési eszköz telepítése
> * Adatok importálása különböző adatforrásokból
> * Exportálás Azure Cosmos DB-ből JSON-ba

Továbbléphet a következő oktatóanyagra, melyben megismerheti, hogy hogyan kérdezhet le adatokat az Azure Cosmos DB segítségével.

> [!div class="nextstepaction"]
>[Hogyan kérdezhetők le adatok?](../cosmos-db/tutorial-query-sql-api.md)
