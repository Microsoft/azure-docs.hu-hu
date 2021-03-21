---
title: Adatmásolási és-átalakítási Azure Cosmos DB (SQL API)
description: Megtudhatja, hogyan másolhat adatok az Azure Cosmos DB (SQL API) és a rendszerből, és hogyan alakíthat át Azure Cosmos DB (SQL API) adatait Data Factory használatával.
ms.author: jingwang
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 03/17/2021
ms.openlocfilehash: d42f30ebd72dca81255ddc02a9440db19979536d
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "104608066"
---
# <a name="copy-and-transform-data-in-azure-cosmos-db-sql-api-by-using-azure-data-factory"></a>Adatok másolása és átalakítása az Azure Cosmos DB-ben (SQL API) az Azure Data Factory használatával

> [!div class="op_single_selector" title1="Válassza ki az Ön által használt Data Factory-szolgáltatás verzióját:"]
> * [1-es verzió](v1/data-factory-azure-documentdb-connector.md)
> * [Aktuális verzió](connector-azure-cosmos-db.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Ez a cikk azt ismerteti, hogyan használhatja az Azure Data Factory másolási tevékenységét, amellyel adatokat másolhat az Azure Cosmos DB-be (SQL API) és onnan ki, és hogyan használhatja az Adatfolyamot az adatok átalakítására az Azure Cosmos DB-ben (SQL API). A Azure Data Factoryről a [bevezető cikkben](introduction.md)olvashat bővebben.



>[!NOTE]
>Ez az összekötő csak Cosmos DB SQL API-t támogatja. A MongoDB API-hoz tekintse [meg a MONGODB API](connector-azure-cosmos-db-mongodb-api.md)-hoz készült összekötőjét a következő témakörben: Azure Cosmos db. Más API-típusok jelenleg nem támogatottak.

## <a name="supported-capabilities"></a>Támogatott képességek

Ez a Azure Cosmos DB (SQL API) összekötő a következő tevékenységek esetében támogatott:

- [Másolási tevékenység](copy-activity-overview.md) [támogatott forrás/fogadó mátrixtal](copy-activity-overview.md)
- [Adatfolyam hozzárendelése](concepts-data-flow-overview.md)
- [Keresési tevékenység](control-flow-lookup-activity.md)

Másolási tevékenység esetén ez a Azure Cosmos DB (SQL API) összekötő a következőket támogatja:

- Adatok másolása a és a rendszerből a Azure Cosmos DB [SQL API](../cosmos-db/introduction.md)-ba.
- Írás a Azure Cosmos DB **Insert** vagy **upsert**.
- JSON-dokumentumok importálása és exportálása, illetve adatok másolása táblázatos adatkészletbe vagy másolással. Ilyenek például az SQL Database és a CSV-fájlok. A dokumentumok fájlként való másolásához JSON-fájlokba vagy egy másik Azure Cosmos DB gyűjteményből vagy más-gyűjteményből: [JSON-dokumentumok importálása és exportálása](#import-and-export-json-documents).

A Data Factory a [Azure Cosmos db tömeges végrehajtó függvénytárral](https://github.com/Azure/azure-cosmosdb-bulkexecutor-dotnet-getting-started) integrálódik, hogy a lehető legjobb teljesítményt nyújtsa a Azure Cosmos db írásakor.

> [!TIP]
> Az [adatáttelepítési videó](https://youtu.be/5-SRNiC_qOU) végigvezeti az adatok Azure Blob Storage-ból Azure Cosmos DBba való másolásának lépésein. A videó ismerteti a teljesítmény-hangolási megfontolásokat is, amelyek az adatfeldolgozást általában a Azure Cosmos DB.

## <a name="get-started"></a>Bevezetés

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

A következő szakaszokban részletesen ismertetjük azokat a tulajdonságokat, amelyekkel Azure Cosmos DB (SQL API) Data Factory entitásokat lehet meghatározni.

## <a name="linked-service-properties"></a>Társított szolgáltatás tulajdonságai

A Azure Cosmos DB (SQL API) társított szolgáltatás a következő tulajdonságokat támogatja:

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| típus | A **Type** tulajdonságot **CosmosDb** értékre kell beállítani. | Yes |
| connectionString |Itt adhatja meg, hogy milyen információkra van szükség a Azure Cosmos DB-adatbázishoz való kapcsolódáshoz.<br />**Megjegyzés**: az alábbi példákban látható módon meg kell adnia az adatbázis adatait a kapcsolatok karakterláncában. <br/> A Azure Key Vault is elhelyezheti a fiók kulcsát, és lekérheti a `accountKey` konfigurációt a kapcsolatok sztringből. További részletekért tekintse meg a következő mintákat, és [tárolja a hitelesítő adatokat Azure Key Vault](store-credentials-in-key-vault.md) cikkben. |Yes |
| Connectvia tulajdonsággal | Az adattárhoz való kapcsolódáshoz használt [Integration Runtime](concepts-integration-runtime.md) . Használhatja a Azure Integration Runtime vagy a saját üzemeltetésű integrációs modult (ha az adattár egy magánhálózaton található). Ha ez a tulajdonság nincs megadva, a rendszer az alapértelmezett Azure Integration Runtime használja. |No |

**Példa**

```json
{
    "name": "CosmosDbSQLAPILinkedService",
    "properties": {
        "type": "CosmosDb",
        "typeProperties": {
            "connectionString": "AccountEndpoint=<EndpointUrl>;AccountKey=<AccessKey>;Database=<Database>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Példa: fiók kulcsának tárolása Azure Key Vault**

```json
{
    "name": "CosmosDbSQLAPILinkedService",
    "properties": {
        "type": "CosmosDb",
        "typeProperties": {
            "connectionString": "AccountEndpoint=<EndpointUrl>;Database=<Database>",
            "accountKey": { 
                "type": "AzureKeyVaultSecret", 
                "store": { 
                    "referenceName": "<Azure Key Vault linked service name>", 
                    "type": "LinkedServiceReference" 
                }, 
                "secretName": "<secretName>" 
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>Adatkészlet tulajdonságai

Az adatkészletek definiálásához rendelkezésre álló csoportok és tulajdonságok teljes listáját lásd: [adatkészletek és társított szolgáltatások](concepts-datasets-linked-services.md).

Azure Cosmos DB (SQL API) adatkészlet esetében a következő tulajdonságok támogatottak: 

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| típus | Az adatkészlet **Type** tulajdonságát **CosmosDbSqlApiCollection** értékre kell állítani. |Yes |
| collectionName |A Azure Cosmos DB dokumentum-gyűjtemény neve. |Yes |

Ha a "DocumentDbCollection" típusú adatkészletet használja, akkor továbbra is támogatott, ha visszamenőlegesen kompatibilis a másolási és keresési tevékenységekkel, az adatfolyam nem támogatott. Azt javasoljuk, hogy használja az új modellt a jövőre.

**Példa**

```json
{
    "name": "CosmosDbSQLAPIDataset",
    "properties": {
        "type": "CosmosDbSqlApiCollection",
        "linkedServiceName":{
            "referenceName": "<Azure Cosmos DB linked service name>",
            "type": "LinkedServiceReference"
        },
        "schema": [],
        "typeProperties": {
            "collectionName": "<collection name>"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Másolási tevékenység tulajdonságai

Ez a szakasz azoknak a tulajdonságoknak a listáját tartalmazza, amelyeket a Azure Cosmos DB (SQL API) forrás és a fogadó támogat. A tevékenységek definiálásához elérhető csoportok és tulajdonságok teljes listáját lásd: [folyamatok](concepts-pipelines-activities.md).

### <a name="azure-cosmos-db-sql-api-as-source"></a>Azure Cosmos DB (SQL API) forrásként

Az adatok Azure Cosmos DBból (SQL API) történő másolásához állítsa a **forrás** típusát a másolási tevékenység **DocumentDbCollectionSource**. 

A másolási tevékenység **forrása** szakasz a következő tulajdonságokat támogatja:

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| típus | A másolási tevékenység forrásának **Type** tulajdonságát **CosmosDbSqlApiSource** értékre kell állítani. |Yes |
| lekérdezés |Az adatolvasás Azure Cosmos DB lekérdezésének megadásához.<br/><br/>Példa:<br /> `SELECT c.BusinessEntityID, c.Name.First AS FirstName, c.Name.Middle AS MiddleName, c.Name.Last AS LastName, c.Suffix, c.EmailPromotion FROM c WHERE c.ModifiedDate > \"2009-01-01T00:00:00\"` |No <br/><br/>Ha nincs megadva, a rendszer az SQL-utasítást hajtja végre: `select <columns defined in structure> from mycollection` |
| preferredRegions | Azoknak a régióknak az előnyben részesített listája, amelyekhez csatlakozni kíván az adatok Cosmos DBból való beolvasása során. | No |
| pageSize | A lekérdezési eredményben szereplő dokumentumok száma oldalanként. Az alapértelmezett érték a "-1", ami azt jelenti, hogy a szolgáltatás oldalsó dinamikus oldalának mérete legfeljebb 1000. | No |
| detectDatetime | Azt határozza meg, hogy a rendszer a dátumokat a dokumentumokban szereplő karakterlánc-értékek alapján vizsgálja-e. Az engedélyezett értékek: **true** (alapértelmezett), **false**. | No |

Ha "DocumentDbCollectionSource" típusú forrást használ, továbbra is támogatja a-t a visszafelé való kompatibilitás érdekében. Azt javasoljuk, hogy használja az új modellt, amely gazdagabb képességeket biztosít az adatok Cosmos DBból való másolásához.

**Példa**

```json
"activities":[
    {
        "name": "CopyFromCosmosDBSQLAPI",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Cosmos DB SQL API input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "CosmosDbSqlApiSource",
                "query": "SELECT c.BusinessEntityID, c.Name.First AS FirstName, c.Name.Middle AS MiddleName, c.Name.Last AS LastName, c.Suffix, c.EmailPromotion FROM c WHERE c.ModifiedDate > \"2009-01-01T00:00:00\"",
                "preferredRegions": [
                    "East US"
                ]
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

Ha Cosmos DBről másol adatokból, kivéve, ha [JSON-dokumentumokat kíván exportálni](#import-and-export-json-documents), az ajánlott eljárás a másolási tevékenységben való leképezés megadása. Data Factory tiszteletben tartja a tevékenységhez megadott leképezést – ha egy sor nem tartalmaz értéket egy oszlophoz, a rendszer null értéket ad meg az oszlop értékéhez. Ha nem ad meg leképezést, Data Factory a séma kikövetkezteti a sémát az adathalmaz első sorának használatával. Ha az első sor nem tartalmazza a teljes sémát, néhány oszlop hiányzik a tevékenység műveletének eredményében.

### <a name="azure-cosmos-db-sql-api-as-sink"></a>Azure Cosmos DB (SQL API) fogadóként

Az adatAzure Cosmos DB (SQL API) másolásához állítsa a fogadó **típust** a másolás tevékenység **DocumentDbCollectionSink**. 

A másolási tevékenység fogadója szakasz a következő  tulajdonságokat támogatja:

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| típus | A másolási tevékenység fogadójának **Type** tulajdonságát **CosmosDbSqlApiSink** értékre kell állítani. |Yes |
| writeBehavior |Ismerteti, hogyan lehet az Azure Cosmos DBba írni az adatbevitelt. Megengedett értékek: **Insert** és **upsert**.<br/><br/>A **upsert** viselkedése a dokumentum cseréje, ha már létezik ilyen azonosítójú dokumentum. Ha nem, szúrja be a dokumentumot.<br /><br />**Megjegyzés**: Data Factory automatikusan létrehoz egy azonosítót egy dokumentumhoz, ha nincs megadva azonosító az eredeti dokumentumban vagy oszlop-hozzárendeléssel. Ez azt jelenti, hogy meg kell győződnie arról, hogy a **upsert** a várt módon működnek, a dokumentum azonosítója. |No<br />(az alapértelmezett érték a **Beszúrás**) |
| writeBatchSize | Data Factory az [Azure Cosmos db tömeges végrehajtó függvénytárat](https://github.com/Azure/azure-cosmosdb-bulkexecutor-dotnet-getting-started) használja az adatAzure Cosmos DBba való íráshoz. A **writeBatchSize** tulajdonság az ADF által a könyvtár számára elérhető dokumentumok méretét határozza meg. A **writeBatchSize** értékének növelésével növelheti a teljesítményt, és csökkentheti az értéket, ha a dokumentum mérete nagyméretű – lásd alább látható tippeket. |No<br />(az alapértelmezett érték **10 000**) |
| disableMetricsCollection | Data Factory olyan mérőszámokat gyűjt, mint például a Cosmos DB RUs a másolási teljesítmény optimalizálása és a javaslatok érdekében. Ha ezt a viselkedést érinti, a `true` kikapcsolásához válassza a következőt:. | Nem (alapértelmezett érték `false` ) |
| maxConcurrentConnections |A tevékenység futtatása során az adattárhoz létesített egyidejű kapcsolatok felső határa. Csak akkor adhat meg értéket, ha korlátozni szeretné az egyidejű kapcsolatokat.| No |


>[!TIP]
>Ha JSON-dokumentumokat szeretne importálni, tekintse meg a [JSON-dokumentumok importálása vagy exportálása](#import-and-export-json-documents) szakaszt; a táblázatos adatokból történő másoláshoz tekintse át az [áttelepítés a viszonyítási adatbázisból a Cosmos DBre](#migrate-from-relational-database-to-cosmos-db)című témakört.

>[!TIP]
>Cosmos DB korlátozza az egyszeri kérelmek méretét 2 MB-ra. A képlet a kérelem mérete = egyetlen dokumentum mérete * írási köteg mérete. Ha a **"kérés mérete túl nagy."** hibaüzenet jelenik meg, **csökkentse az `writeBatchSize` értéket** a másolási fogadó konfigurációjában.

Ha "DocumentDbCollectionSink" típusú forrást használ, továbbra is támogatja a-t a visszafelé való kompatibilitás érdekében. Azt javasoljuk, hogy használja az új modellt, amely gazdagabb képességeket biztosít az adatok Cosmos DBból való másolásához.

**Példa**

```json
"activities":[
    {
        "name": "CopyToCosmosDBSQLAPI",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Document DB output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "CosmosDbSqlApiSink",
                "writeBehavior": "upsert"
            }
        }
    }
]
```

### <a name="schema-mapping"></a>Séma-hozzárendelés

Az adatok Azure Cosmos DBból táblázatos vagy fordítottra történő másolásához tekintse meg a [séma-hozzárendelést](copy-activity-schema-and-type-mapping.md#schema-mapping).

## <a name="mapping-data-flow-properties"></a>Adatfolyam-tulajdonságok leképezése

A leképezési adatfolyamban lévő adatátalakítás során a gyűjtemények Cosmos DBban való olvasását és írását is elvégezheti. További információ: a forrás- [átalakítás](data-flow-source.md) és a fogadó [transzformáció](data-flow-sink.md) a leképezési adatfolyamatokban.

### <a name="source-transformation"></a>Forrás-átalakítás

A Azure Cosmos DB vonatkozó beállítások a forrás-átalakítás **forrás beállításai** lapján érhetők el. 

**Rendszeroszlopok belefoglalása:** Ha az értéke true, a ```id``` ```_ts``` , és más rendszeroszlopok is szerepelni fognak a CosmosDB-ből származó adatfolyam-metaadatokban. Gyűjtemények frissítésekor fontos, hogy a meglévő sor azonosítóját is megragadja.

**Oldalméret:** A lekérdezési eredményben szereplő dokumentumok száma oldalanként. Az alapértelmezett érték az "-1", amely a szolgáltatás dinamikus oldalát használja akár 1000-ig.

**Átviteli sebesség:** Adja meg a CosmosDB-gyűjteményre alkalmazni kívánt RUs számának nem kötelező értékét az olvasási művelet során az adott adatfolyam minden egyes végrehajtásához. Minimális értéke 400.

**Előnyben részesített régiók:** Válassza ki a folyamathoz használni kívánt olvasási régiókat.

#### <a name="json-settings"></a>JSON-beállítások

**Egyetlen dokumentum:** Akkor válassza ezt a lehetőséget, ha az ADF a teljes fájlt egyetlen JSON-doc-ként kezeli.

Nem **jegyzett oszlopnevek:** Akkor válassza ezt a lehetőséget, ha a JSON-beli oszlopnevek nem idézőjelként szerepelnek.

**Megjegyzésekkel rendelkezik:** Akkor használja ezt a beállítást, ha a JSON-dokumentumok megjegyzésekkel rendelkeznek az adataiban.

**Egyszer idézett:** Ezt akkor kell kiválasztani, ha a dokumentumban lévő oszlopok és értékek idézőjelek között vannak megadva.

**Megmenekült fordított perjel:** Ha fordított perjeleket használ a JSON-beli Escape-karakterekhez, válassza ezt a lehetőséget.

### <a name="sink-transformation"></a>Fogadó transzformáció

A Azure Cosmos DB vonatkozó beállítások a fogadó átalakítás **Beállítások** lapján érhetők el.

**Frissítési módszer:** Meghatározza, hogy mely műveletek engedélyezettek az adatbázis célhelyén. Az alapértelmezett érték a beszúrások engedélyezése. Sorok frissítéséhez, upsert vagy törléséhez módosítani kell az Alter-Row transzformációt a műveletek sorainak címkézéséhez. A frissítések, upsert és törlések esetében meg kell adni a kulcs oszlopát vagy oszlopait annak meghatározásához, hogy melyik sort kell megváltoztatni.

**Gyűjtési művelet:** Meghatározza, hogy az írás előtt újra létre kell-e hozni a célhelyet.
* Nincs: a gyűjteményhez nem kerül sor művelet.
* Újból létrehozva: a gyűjtemény eldobása és újbóli létrehozása megtörténik

**Batch-méret**: egy egész szám, amely azt jelöli, hogy hány objektumot ír a rendszer az egyes kötegekben Cosmos db gyűjteménybe. Általában az alapértelmezett batch-mérettől kezdve elegendő. Az érték további finomhangolásához vegye figyelembe a következőket:

- Cosmos DB korlátozza az egyszeri kérelmek méretét 2 MB-ra. A képlet a "kérelem mérete = egyetlen dokumentum mérete * batch size". Ha a "kérés mérete túl nagy" hibaüzenet jelenik meg, csökkentse a Batch méret értékét.
- Minél nagyobb a köteg mérete, annál jobb átviteli sebességű ADF érhető el, míg a számítási feladatok elvégzéséhez elegendő RUs kiosztása szükséges.

**Partíciós kulcs:** Adjon meg egy karakterláncot, amely a gyűjtemény partíciós kulcsát jelöli. Például: ```/movies/title```

**Átviteli sebesség:** Adja meg a CosmosDB-gyűjteményre alkalmazni kívánt RUs számának nem kötelező értékét az adott adatfolyam minden egyes végrehajtásához. Minimális értéke 400.

**Írási átviteli sebesség költségvetése:** Egy egész szám, amely az adatfolyam-írási művelethez lefoglalni kívánt RUs-t jelöli, a gyűjteményhez lefoglalt teljes átviteli sebességtől számítva.

## <a name="lookup-activity-properties"></a>Keresési tevékenység tulajdonságai

A tulajdonságok részleteinek megismeréséhez tekintse meg a [keresési tevékenységet](control-flow-lookup-activity.md).

## <a name="import-and-export-json-documents"></a>JSON-dokumentumok importálása és exportálása

Ezt a Azure Cosmos DB (SQL API) összekötőt egyszerűen elvégezheti:

* Dokumentumok másolása két Azure Cosmos DB gyűjtemény között.
* A különböző forrásokból származó JSON-dokumentumokat importálhatja Azure Cosmos DBba, beleértve az Azure Blob Storage-ból, Azure Data Lake Storeból és más, a Azure Data Factory által támogatott fájl-alapú tárolókat.
* JSON-dokumentumok exportálása Azure Cosmos DB gyűjteményből különböző file-alapú áruházakba.

Séma – agnosztikus másolás:

* Az Adatok másolása eszköz használatakor válassza az **Exportálás másként lehetőséget a JSON-fájlok vagy a Cosmos db-gyűjtemény** lehetőségre.
* Ha tevékenység-létrehozást használ, válassza a JSON formátum elemet a forrás vagy a fogadó megfelelő fájljával.

## <a name="migrate-from-relational-database-to-cosmos-db"></a>Migrálás a viszonyítási adatbázisból a Cosmos DBba

Ha például SQL Serverról Azure Cosmos DBra végez áttelepítést, a másolási tevékenység könnyedén leképezheti a táblázatos adatokat a forrásból a JSON-dokumentumok Cosmos DBba való lelapulása érdekében. Bizonyos esetekben érdemes lehet újratervezni az adatmodellt úgy, hogy optimalizálja a NoSQL használati eseteit az [Azure Cosmos db adatmodellezése](../cosmos-db/modeling-data.md)alapján, például az adatok denormalizálása egy JSON-dokumentumban található összes kapcsolódó alelem beágyazásával. Ilyen esetben tekintse át [ezt a cikket](../cosmos-db/migrate-relational-to-cosmos-db-sql-api.md) , amely bemutatja, hogyan érheti el Azure Data Factory másolási tevékenység használatával.

## <a name="next-steps"></a>Következő lépések

A másolási tevékenység által támogatott adattárak listáját a Azure Data Factoryban található forrásként és nyelőként tekintheti meg. lásd: [támogatott adattárak](copy-activity-overview.md#supported-data-stores-and-formats).