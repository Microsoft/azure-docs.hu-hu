---
title: Meglévő adatáttelepítés Table API fiókba Azure Cosmos DB
description: Megtudhatja, hogyan telepítheti át vagy importálhatja a helyszíni vagy a Felhőbeli információkat az Azure Table API-fiókba Azure Cosmos DB.
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.topic: tutorial
ms.date: 12/07/2017
ms.author: sngun
ms.custom: seodec18
ms.openlocfilehash: e876ca028532bb3721146e90a91d68c4c12bf79f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "93096077"
---
# <a name="migrate-your-data-to-azure-cosmos-db-table-api-account"></a>Adatok migrálása az Azure Cosmos DB Table API-fiókba
[!INCLUDE[appliesto-table-api](includes/appliesto-table-api.md)]

Ez az oktatóanyag útmutatást nyújt az adatimportáláshoz a Azure Cosmos DB [Table API](table-introduction.md)való használatra. Ha az Azure Table Storage-ban tárolja az adatokat, az adatmigrálási eszközzel vagy az AzCopy segítségével importálhatja azokat az Azure Cosmos DB Table API-ba. Ha az Azure Cosmos DB Table API (előzetes verzió) szolgáltatásban létrehozott fiókban tárolja az adatokat, az adatmigrálási eszközt kell használnia az adatok migrálásához. 

Ez az oktatóanyag a következő feladatokat mutatja be:

> [!div class="checklist"]
> * Adatok importálása az adatmigrálási eszközzel
> * Adatok importálása az AzCopy használatával
> * Migrálás a Table API előzetes verziójából a Table API-ba 

## <a name="prerequisites"></a>Előfeltételek

* **Átviteli sebesség növelése:** Az adatok áttelepítésének időtartama az egyes tárolók vagy tárolók készlete számára beállított átviteli sebességtől függ. Nagyobb adatmigrálásoknál mindenképpen növelje az átviteli sebességet. A migrálás befejezése után, a költségtakarékosság érdekében csökkentse az átviteli sebességet. Az átviteli sebesség Azure Portalon való növeléséről bővebben Az Azure Cosmos DB teljesítményszintjei és tarifacsomagjai című cikkben olvashat.

* **Azure Cosmos DB-erőforrások létrehozása:** Az adatok migrálásának indítása előtt hozza létre előre az összes táblát az Azure Portalról. Ha egy olyan Azure Cosmos DB-fiókba migrál, amely adatbázisszintű átviteli sebességgel rendelkezik, az Azure Cosmos DB-táblák létrehozásakor mindenképp adjon meg egy partíciókulcsot.

## <a name="data-migration-tool"></a>Adatmigrálási eszköz

Az Azure Cosmos DB parancssori adatmigrálási eszközének (dt.exe) segítségével importálhatja az Azure Table Storage-ban meglévő adatait a Table API általánosan elérhető verziójában létrehozott fiókba, vagy migrálhatja a Table API előzetes verziójában létrehozott fiókban található adatait a Table API általánosan elérhető verziójában létrehozott fiókba. Az egyéb források jelenleg nem támogatottak. A felhasználói felületen alapuló adatmigrálási eszköz (dtui.exe) jelenleg nem támogatott a Table API-fiókok esetében. 

Egy tábla adatainak migrálásához hajtsa végre az alábbi feladatokat:

1. Töltse le a migrálási eszközt a [GitHubról](https://github.com/azure/azure-documentdb-datamigrationtool).
2. Futtassa a `dt.exe` fájlt a forgatókönyvnek megfelelő parancssori argumentumokkal. A `dt.exe` a következő formátumban fogad el parancsokat:

   ```bash
    dt.exe [/<option>:<value>] /s:<source-name> [/s.<source-option>:<value>] /t:<target-name> [/t.<target-option>:<value>] 
   ```

A parancs támogatott beállításai a következők:

* **/ErrorLog:** Választható. Az adatátviteli hibák átirányítására szolgáló CSV-fájl neve
* **/OverwriteErrorLog:** Választható. Hiba naplófájljának felülírása
* **/ProgressUpdateInterval:** Nem kötelező, az alapértelmezett érték a 00:00:01. A képernyőn megjelenő adatátviteli folyamat frissítésének időintervalluma
* **/ErrorDetails:** Nem kötelező, az alapértelmezett érték a none. Megadja, hogy a rendszer részletes információkat jelenít meg a következő hibákhoz: nincs, kritikus, mind
* **/EnableCosmosTableLog:** Választható. Irányítsa a naplót egy Cosmos Table-fiókba. Ha be van állítva, ez alapértelmezés szerint a cél fiók kapcsolódási karakterláncát adja meg, kivéve, ha a/CosmosTableLogConnectionString is meg van adni. Ez akkor hasznos, ha a DT több példánya egyszerre fut.
* **/CosmosTableLogConnectionString:** Választható. ConnectionString, hogy a naplót egy távoli Cosmos Table-fiókba irányítsa.

### <a name="command-line-source-settings"></a>Parancssori forrásbeállítások

Használja az alábbi forrásbeállításokat, amikor egy Azure Table Storage-tárolót vagy a Table API előzetes verzióját adja meg a migrálás forrásaként.

* **/s: AzureTable:** Adatok beolvasása az Azure Table Storage-ból
* **/s.ConnectionString:** A tábla végpontjának a kapcsolatok karakterlánca. Ez a Azure Portalból kérhető le.
* **/s.LocationMode:** Nem kötelező, az alapértelmezett érték a PrimaryOnly. Meghatározza, hogy az Azure Table Storage-hoz való csatlakozáskor milyen tárolási módot kell használni: PrimaryOnly, PrimaryThenSecondary, SecondaryOnly, SecondaryThenPrimary
* **/s.table:** Az Azure-tábla neve
* **/s.InternalFields:** Állítsa az All értékre a tábla áttelepítéséhez, mert az importáláshoz RowKey és PartitionKey szükséges.
* **/s.Filter:** Választható. Alkalmazni kívánt karakterlánc szűrése
* **/s.Projection:** Választható. A kijelölni kívánt oszlopok listája

Ha az Azure Table Storage-ból való importáláskor szeretné lekérni a forrás kapcsolati karakterláncot, nyissa meg a Azure Portal, és kattintson a **Storage**  >  **-fiók**  >  **hozzáférési kulcsa** elemre, majd a Másolás gombra kattintva másolja a **kapcsolati karakterláncot**.

:::image type="content" source="./media/table-import/storage-table-access-key.png" alt-text="Képernyőfelvétel: a Storage-fiókok > fiók > hozzáférési kulcsok beállításai, és kiemeli a másolás gombot.":::

Ha Azure Cosmos db Table API (előzetes verzió) fiókból történő importáláskor szeretné lekérni a forrás-kapcsolódási karakterláncot, nyissa meg a Azure Portal, kattintson **Azure Cosmos db** a  >  **fiók**  >  **kapcsolódási karakterlánca** elemre, majd a Másolás gombra kattintva másolja a **kapcsolódási karakterláncot**.

:::image type="content" source="./media/table-import/cosmos-connection-string.png" alt-text="Képernyőkép a HBase forrás beállításairól":::

[Példaparancs az Azure Table Storage esetén](#azure-table-storage)

[Példaparancs az Azure Cosmos DB Table API (előzetes verzió) esetén](#table-api-preview)

### <a name="command-line-target-settings"></a>Parancssori célbeállítások

Használja az alábbi célbeállításokat, amikor az Azure Cosmos DB Table API-t adja meg a migrálás céljaként.

* **/t: TableAPIBulk:** Adatok feltöltése az Azure CosmosDB-táblába kötegekben
* **/t.ConnectionString:** A tábla végpontjának a kapcsolatok karakterlánca
* **/t.TableName:** A írni kívánt tábla nevét adja meg.
* **/t.overwrite:** Nem kötelező, az alapértelmezett érték a false. Meghatározza, hogy a meglévő értékeket felül kell-e írni
* **/t.MaxInputBufferSize:** Nem kötelező, az alapértelmezett érték 1 GB. Az adatok fogadóba való kiürítése előtt a pufferbe való bemeneti bájtok becsült értéke
* **/t.throughput:** Nem kötelező, a szolgáltatás alapértelmezett értéke, ha nincs megadva. Meghatározza a tábla konfigurálásának sebességét
* **/t.MaxBatchSize:** Nem kötelező, az alapértelmezett érték a 2 MB. A köteg méretének megadása bájtban

<a id="azure-table-storage"></a>
### <a name="sample-command-source-is-azure-table-storage"></a>Példaparancs: a forrás az Azure Table Storage

Ez a parancssori példa bemutatja, hogyan importálhatók adatok az Azure Table Storage-ból a Table API-ba:

```bash
dt /s:AzureTable /s.ConnectionString:DefaultEndpointsProtocol=https;AccountName=<Azure Table storage account name>;AccountKey=<Account Key>;EndpointSuffix=core.windows.net /s.Table:<Table name> /t:TableAPIBulk /t.ConnectionString:DefaultEndpointsProtocol=https;AccountName=<Azure Cosmos DB account name>;AccountKey=<Azure Cosmos DB account key>;TableEndpoint=https://<Account name>.table.cosmosdb.azure.com:443 /t.TableName:<Table name> /t.Overwrite
```

<a id="table-api-preview"></a>
### <a name="sample-command-source-is-azure-cosmos-db-table-api-preview"></a>Példaparancs: a forrás az Azure Cosmos DB Table API (előzetes verzió)

Ez a parancssori példa bemutatja, hogyan importálhatók adatok a Table API előzetes verziójából a Table API általánosan elérhető verziójába:

```bash
dt /s:AzureTable /s.ConnectionString:DefaultEndpointsProtocol=https;AccountName=<Table API preview account name>;AccountKey=<Table API preview account key>;TableEndpoint=https://<Account Name>.documents.azure.com; /s.Table:<Table name> /t:TableAPIBulk /t.ConnectionString:DefaultEndpointsProtocol=https;AccountName=<Azure Cosmos DB account name>;AccountKey=<Azure Cosmos DB account key>;TableEndpoint=https://<Account name>.table.cosmosdb.azure.com:443 /t.TableName:<Table name> /t.Overwrite
```

## <a name="migrate-data-by-using-azcopy"></a>Adatok migrálása az AzCopy használatával

Az AzCopy parancssori segédprogram használata a másik módja az adatok migrálásának az Azure Table Storage-ból az Azure Cosmos DB Table API-ba. Az AzCopy használatakor először exportálni kell az adatokat az [adatoknak a Table Storage-ból való exportálását](/previous-versions/azure/storage/storage-use-azcopy#export-data-from-table-storage) ismertető cikkben leírt módon, majd importálni kell az adatokat az Azure Cosmos DB-be, az [Azure Cosmos DB Table API-val](/previous-versions/azure/storage/storage-use-azcopy#import-data-into-table-storage) foglalkozó cikkben leírtak szerint.

Az Azure Cosmos DB-be való importálás végrehajtásakor tekintse meg az alábbi példát. Vegye figyelembe, hogy a /Dest értékként a cosmosdb, nem pedig a core értéket használja.

Importálási példaparancs:

```bash
AzCopy /Source:C:\myfolder\ /Dest:https://myaccount.table.cosmosdb.windows.net/mytable1/ /DestKey:key /Manifest:"myaccount_mytable_20140103T112020.manifest" /EntityOperation:InsertOrReplace
```

## <a name="migrate-from-table-api-preview-to-table-api"></a>Migrálás a Table API előzetes verziójából a Table API-ba.

> [!WARNING]
> Ha azonnal szeretné kihasználni az általánosan elérhető táblák előnyeit, akkor migrálja a meglévő előzetes verziójú táblákat a jelen szakaszban leírtak szerint. Ha ezt elmulasztja, a következő hetekben automatikusan elvégezzük a migrálást az előzetes verziót használó meglévő ügyfelek számára. Vegye azonban figyelembe, hogy az automatikusan migrált előzetes verziójú táblákra bizonyos korlátozások vonatkozhatnak, az újonnan létrehozott táblákra vonatkozóan viszont nincsenek ilyen korlátozások.

A Table API mostantól általánosan elérhető. Különbségek vannak az előzetes verzió táblái és az általános elérhető verzió táblái között a felhőben futó kód és az ügyfélnél futó kód szempontjából egyaránt. Ezért nem javasoljuk, hogy egy előzetes verziójú SDK-ügyfelet egy általánosan elérhető Table API-fiókkal együtt használjon, vagy fordítva. Ha a Table API előzetes verziójának felhasználói továbbra is használni szeretnék a meglévő tábláikat éles környezetben, akkor az előzetes verziójú környezetből az általánosan elérhető verziójú környezetbe kell migrálniuk, vagy meg kell várniuk az automatikus migrálást. Ha megvárja az automatikus migrálást, értesítést kap majd a migrált táblákra vonatkozó korlátozásokról. A migrálást követően korlátozás nélkül hozhat majd létre új táblákat a meglévő fiókjában (a korlátozások csak a migrált táblákra vonatkoznak).

Migrálás a Table API előzetes verziójából a Table API általánosan elérhető verziójába:

1. Hozzon létre egy új Azure Cosmos DB-fiókot, és állítsa be a fiók API-típusát az Azure Table értékre az [Adatbázisfiók létrehozása](create-table-dotnet.md#create-a-database-account) című szakaszban leírtak szerint.

2. Módosítsa az ügyfeleket, hogy azok a [Table API SDK-k](table-sdk-dotnet.md) általánosan elérhető kiadását használják.

3. Migrálja az ügyféladatokat az előzetes verzió tábláiból az általánosan elérhető verzió tábláiba az adatmigrálási eszközzel. Az adatmigrálási eszköz ilyen célú használatára vonatkozó utasításokat az [adatmigrálási eszközzel](#data-migration-tool) foglalkozó szakasz ismerteti. 

## <a name="next-steps"></a>Következő lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * Adatok importálása az adatmigrálási eszközzel.
> * Adatok importálása az AzCopy használatával.
> * Migrálás a Table API előzetes verziójából a Table API-ba.

Továbbléphet a következő oktatóanyagra, amelyben megtudhatja, hogyan kérdezhet le adatokat az Azure Cosmos DB Table API használatával. 

> [!div class="nextstepaction"]
>[Hogyan kérdezhetők le adatok?](../cosmos-db/tutorial-query-table.md)