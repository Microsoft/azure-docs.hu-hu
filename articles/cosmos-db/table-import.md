---
title: Meglévő adatáttelepítés Table API-fiókba Azure Cosmos DB
description: Megtudhatja, hogyan telepíthet át vagy importálhat helyszíni vagy Felhőbeli információkat egy Azure Table API-fiókba Azure Cosmos DB-ban.
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.topic: tutorial
ms.date: 12/07/2017
ms.author: sngun
ms.custom: seodec18
ms.openlocfilehash: 77f4c928db695bd4193ad46c93e0efbd16decf29
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2021
ms.locfileid: "106443338"
---
# <a name="migrate-your-data-to-an-azure-cosmos-db-table-api-account"></a>Az adatait áttelepítheti egy Azure Cosmos DB Table API-fiókba
[!INCLUDE[appliesto-table-api](includes/appliesto-table-api.md)]

Ez az oktatóanyag útmutatást nyújt az adatimportáláshoz a Azure Cosmos DB [Table API](table-introduction.md)való használatra. Ha az Azure Table Storageban tárolt adatai vannak, akkor az adatáttelepítés eszközzel vagy a AzCopy importálhatja az adatait a Azure Cosmos DB Table APIba. 

Ez az oktatóanyag a következő feladatokat mutatja be:

> [!div class="checklist"]
> * Adatimportálás az adatáttelepítési eszközzel
> * Adatok importálása az AzCopy használatával

## <a name="prerequisites"></a>Előfeltételek

* **Átviteli sebesség növelése:** Az adatok áttelepítésének időtartama az egyes tárolók vagy tárolók készlete számára beállított átviteli sebességtől függ. Nagyobb adatmigrálásoknál mindenképpen növelje az átviteli sebességet. A migrálás befejezése után, a költségtakarékosság érdekében csökkentse az átviteli sebességet.

* **Azure Cosmos db erőforrások létrehozása:** Az adatok áttelepítésének megkezdése előtt hozza létre az összes táblát a Azure Portal. Ha olyan Azure Cosmos DB-fiókra végez áttelepítést, amelynek adatbázis-szintű átviteli sebessége van, akkor a Azure Cosmos DB táblák létrehozásakor ügyeljen arra, hogy adjon meg egy partíciós kulcsot.

## <a name="data-migration-tool"></a>Adatáttelepítési eszköz

A Azure Cosmos DB parancssori adatáttelepítési eszköz (dt.exe) segítségével importálhatja meglévő Azure-Table Storage-adatait egy Table API-fiókba. 

A Table-adatáttelepítés:

1. Töltse le a migrálási eszközt a [GitHubról](https://github.com/azure/azure-documentdb-datamigrationtool).
2. Futtassa `dt.exe` a parancsot a forgatókönyvhöz tartozó parancssori argumentumok használatával. A `dt.exe` a következő formátumban fogad el parancsokat:

   ```bash
    dt.exe [/<option>:<value>] /s:<source-name> [/s.<source-option>:<value>] /t:<target-name> [/t.<target-option>:<value>] 
   ```

A parancs támogatott beállításai a következők:

* **/ErrorLog:** Választható. Az adatátviteli hibák átirányítására szolgáló CSV-fájl neve.
* **/OverwriteErrorLog:** Választható. Írja felül a hibanapló fájlját.
* **/ProgressUpdateInterval:** Nem kötelező, alapértelmezett érték: `00:00:01` . A képernyőn megjelenő adatátviteli folyamat frissítésének időintervalluma.
* **/ErrorDetails:** Nem kötelező, alapértelmezett érték: `None` . Megadja, hogy a rendszer részletes információkat jelenít meg a következő hibákhoz: `None` , `Critical` , vagy `All` .
* **/EnableCosmosTableLog:** Választható. Irányítsa a naplót egy Azure Cosmos DB Table-fiókba. Ha be van állítva, ez alapértelmezés szerint a cél fiók kapcsolódási karakterláncát adja meg, kivéve, ha `/CosmosTableLogConnectionString` az meg van határozva. Ez akkor hasznos, ha az eszköz több példánya egyidejűleg fut.
* **/CosmosTableLogConnectionString:** Választható. A kapcsolódási karakterlánc, amely a naplót egy távoli Azure Cosmos DB Table-fiókba irányítja.

### <a name="command-line-source-settings"></a>Parancssori forrásbeállítások

Ha az áttelepítés forrásaként az Azure Table Storaget adja meg, használja az alábbi forrás beállításokat.

* **/s: AzureTable:** Adatokat olvas Table Storageból.
* **/s.ConnectionString:** A tábla végpontjának a kapcsolatok karakterlánca. Ezt a Azure Portal kérheti le.
* **/s.LocationMode:** Nem kötelező, alapértelmezett érték: `PrimaryOnly` . Meghatározza, hogy a Table Storagehoz való csatlakozáskor melyik hely módot kell használni:,, `PrimaryOnly` `PrimaryThenSecondary` `SecondaryOnly` , `SecondaryThenPrimary` .
* **/s.table:** Az Azure-tábla neve.
* **/s.InternalFields:** A `All` tábla áttelepítésére van beállítva, mert az `RowKey` `PartitionKey` importáláshoz szükséges.
* **/s.Filter:** Választható. Az alkalmazni kívánt karakterlánc szűrése.
* **/s.Projection:** Választható. A kijelölni kívánt oszlopok listája

A forrás-kapcsolódási karakterlánc lekéréséhez Table Storageból való importáláskor nyissa meg a Azure Portal. Válassza ki a **Storage**  >  **-fiók**  >  **hozzáférési kulcsait**, és másolja a **kapcsolati karakterláncot**.

:::image type="content" source="./media/table-import/storage-table-access-key.png" alt-text="Képernyőfelvétel: a Storage-fiókok > fiók > hozzáférési kulcsok beállításai, és kiemeli a másolási ikont.":::

### <a name="command-line-target-settings"></a>Parancssori célbeállítások

Ha a Azure Cosmos DB Table API az áttelepítés céljaként adja meg, akkor használja a következő célzási beállításokat.

* **/t: TableAPIBulk:** Adatok feltöltése a Azure Cosmos DB Table API kötegekben.
* **/t.ConnectionString:** A tábla végpontjának a kapcsolatok karakterlánca.
* **/t.TableName:** Megadja az írni kívánt tábla nevét.
* **/t.overwrite:** Nem kötelező, alapértelmezett érték: `false` . Meghatározza, hogy a meglévő értékeket felül kell-e írni.
* **/t.MaxInputBufferSize:** Nem kötelező, alapértelmezett érték: `1GB` . Az adatok fogadóba való kiürítése előtt a pufferben lévő bemeneti bájtok becsült becslése.
* **/t.throughput:** Nem kötelező, a szolgáltatás alapértelmezett értéke, ha nincs megadva. Meghatározza a tábla konfigurálásának sebességét.
* **/t.MaxBatchSize:** Nem kötelező, alapértelmezett érték: `2MB` . A köteg méretét bájtban kell megadni.

### <a name="sample-command-source-is-table-storage"></a>Minta parancs: a forrás Table Storage

Az alábbi parancssori minta azt mutatja be, hogyan lehet importálni a Table Storageból a Table APIba:

```bash
dt /s:AzureTable /s.ConnectionString:DefaultEndpointsProtocol=https;AccountName=<Azure Table storage account name>;AccountKey=<Account Key>;EndpointSuffix=core.windows.net /s.Table:<Table name> /t:TableAPIBulk /t.ConnectionString:DefaultEndpointsProtocol=https;AccountName=<Azure Cosmos DB account name>;AccountKey=<Azure Cosmos DB account key>;TableEndpoint=https://<Account name>.table.cosmosdb.azure.com:443 /t.TableName:<Table name> /t.Overwrite
```

## <a name="migrate-data-by-using-azcopy"></a>Adatok migrálása az AzCopy használatával

A AzCopy parancssori segédprogrammal az adatok áttelepíthetők a Table Storageról a Azure Cosmos DB Table API. A AzCopy használatához először exportálja az adatait az [adatok exportálása Table Storageból](/previous-versions/azure/storage/storage-use-azcopy#export-data-from-table-storage)című témakörben leírtak szerint. Ezt követően importálja az Azure Cosmos DB az [Azure Cosmos DB Table API](/previous-versions/azure/storage/storage-use-azcopy#import-data-into-table-storage)című témakörben leírtak szerint.

Az Azure Cosmos DBba való importáláskor tekintse meg a következő mintát. Vegye figyelembe, hogy az érték a ( `/Dest` `cosmosdb` nem) értéket használja `core` .

Importálási példaparancs:

```bash
AzCopy /Source:C:\myfolder\ /Dest:https://myaccount.table.cosmosdb.windows.net/mytable1/ /DestKey:key /Manifest:"myaccount_mytable_20140103T112020.manifest" /EntityOperation:InsertOrReplace
```

## <a name="next-steps"></a>Következő lépések

Megtudhatja, hogyan kérdezheti le az információkat a Azure Cosmos DB Table API használatával. 

> [!div class="nextstepaction"]
>[Hogyan kérdezhetők le adatok?](../cosmos-db/tutorial-query-table.md)




