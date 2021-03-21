---
title: Adatok másolása a MongoDB örökölt használatával
description: Megtudhatja, hogyan másolhat adatmásolási tevékenységet a Mongo DB-ből egy örökölt Azure Data Factory-folyamat másolási tevékenységének használatával.
author: linda33wj
ms.author: jingwang
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019; seo-dt-2019
ms.date: 08/12/2019
ms.openlocfilehash: e13a1a5a939d314bdf4500c0827fa13201505016
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "100368846"
---
# <a name="copy-data-from-mongodb-using-azure-data-factory-legacy"></a>Adatok másolása a MongoDB Azure Data Factory használatával (örökölt)

> [!div class="op_single_selector" title1="Válassza ki az Ön által használt Data Factory-szolgáltatás verzióját:"]
> * [1-es verzió](v1/data-factory-on-premises-mongodb-connector.md)
> * [Aktuális verzió](connector-mongodb.md)
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Ez a cikk azt ismerteti, hogyan használható a másolási tevékenység a Azure Data Factoryban az adatok MongoDB-adatbázisból történő másolásához. A másolási [tevékenység áttekintő](copy-activity-overview.md) cikkében található, amely a másolási tevékenység általános áttekintését jeleníti meg.

>[!IMPORTANT]
>Az ADF kibocsát egy új MongoDB-összekötőt, amely jobb natív MongoDB-támogatást biztosít az ehhez az ODBC-alapú implementációhoz képest, a részletekért lásd a [MongoDB-összekötőt](connector-mongodb.md) ismertető cikket. Ez az örökölt MongoDB-összekötő továbbra is támogatott a visszafelé compability, míg az új munkaterhelések esetében az új összekötőt kell használnia.

## <a name="supported-capabilities"></a>Támogatott képességek

A MongoDB-adatbázisból bármilyen támogatott fogadó adattárba másolhat adatok. A másolási tevékenység által a forrásként/mosogatóként támogatott adattárak listáját a [támogatott adattárak](copy-activity-overview.md#supported-data-stores-and-formats) táblázatban tekintheti meg.

Ez a MongoDB-összekötő a következőket támogatja:

- MongoDB- **verziók 2,4, 2,6, 3,0, 3,2, 3,4 és 3,6**.
- Adatok másolása az **alapszintű** vagy a **Névtelen** hitelesítés használatával.

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

A Integration Runtime egy beépített MongoDB-illesztőprogramot biztosít, ezért nem kell manuálisan telepítenie az illesztőprogramokat az adatok MongoDB történő másolásakor.

## <a name="getting-started"></a>Első lépések

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

A következő szakaszokban részletesen ismertetjük az MongoDB-összekötőhöz tartozó Data Factory-entitások definiálásához használt tulajdonságokat.

## <a name="linked-service-properties"></a>Társított szolgáltatás tulajdonságai

A MongoDB társított szolgáltatás a következő tulajdonságokat támogatja:

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| típus |A Type tulajdonságot a következőre kell beállítani: **MongoDb** |Yes |
| kiszolgáló |A MongoDB-kiszolgáló IP-címe vagy állomásneve. |Yes |
| port |A MongoDB-kiszolgáló által az ügyfélkapcsolatok figyeléséhez használt TCP-port. |Nem (az alapértelmezett érték 27017) |
| databaseName |Az elérni kívánt MongoDB-adatbázis neve. |Yes |
| authenticationType | A MongoDB-adatbázishoz való kapcsolódáshoz használt hitelesítés típusa.<br/>Az engedélyezett értékek: **Basic** és **Anonymous**. |Yes |
| username |Felhasználói fiók a MongoDB eléréséhez. |Igen (ha alapszintű hitelesítést használ). |
| jelszó |A felhasználó jelszava. Megjelöli ezt a mezőt SecureString, hogy biztonságosan tárolja Data Factoryban, vagy [hivatkozjon a Azure Key Vault tárolt titkos kulcsra](store-credentials-in-key-vault.md). |Igen (ha alapszintű hitelesítést használ). |
| authSource |Annak a MongoDB-adatbázisnak a neve, amelyet a hitelesítés hitelesítő adatainak ellenőrzéséhez használni kíván. |Nem. Az alapszintű hitelesítéshez az alapértelmezett érték a databaseName tulajdonsággal megadott rendszergazdai fiók és adatbázis használata. |
| enableSsl | Megadja, hogy a kiszolgálóval létesített kapcsolatok titkosítva vannak-e a TLS protokollal. Az alapértelmezett érték a hamis.  | No |
| allowSelfSignedServerCert | Megadja, hogy engedélyezi-e az önaláírt tanúsítványokat a kiszolgálóról. Az alapértelmezett érték a hamis.  | No |
| Connectvia tulajdonsággal | Az adattárhoz való kapcsolódáshoz használt [Integration Runtime](concepts-integration-runtime.md) . További tudnivalók az [Előfeltételek](#prerequisites) szakaszban olvashatók. Ha nincs megadva, az alapértelmezett Azure Integration Runtime használja. |No |

**Példa**

```json
{
    "name": "MongoDBLinkedService",
    "properties": {
        "type": "MongoDb",
        "typeProperties": {
            "server": "<server name>",
            "databaseName": "<database name>",
            "authenticationType": "Basic",
            "username": "<username>",
            "password": {
                "type": "SecureString",
                "value": "<password>"
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

Az adatkészletek definiálásához rendelkezésre álló csoportok és tulajdonságok teljes listáját lásd: [adatkészletek és társított szolgáltatások](concepts-datasets-linked-services.md). A MongoDB adatkészlet a következő tulajdonságokat támogatja:

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| típus | Az adatkészlet Type tulajdonságát a következőre kell beállítani: **MongoDbCollection** | Yes |
| collectionName |A gyűjtemény neve a MongoDB adatbázisban. |Yes |

**Példa**

```json
{
    "name": "MongoDbDataset",
    "properties": {
        "type": "MongoDbCollection",
        "linkedServiceName": {
            "referenceName": "<MongoDB linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "collectionName": "<Collection name>"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Másolási tevékenység tulajdonságai

A tevékenységek definiálásához elérhető csoportok és tulajdonságok teljes listáját a [folyamatok](concepts-pipelines-activities.md) című cikkben találja. Ez a szakasz a MongoDB forrás által támogatott tulajdonságok listáját tartalmazza.

### <a name="mongodb-as-source"></a>MongoDB forrásként

A másolási tevékenység **forrása** szakasz a következő tulajdonságokat támogatja:

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| típus | A másolási tevékenység forrásának Type tulajdonságát a következőre kell beállítani: **MongoDbSource** | Yes |
| lekérdezés |Az egyéni SQL-92 lekérdezés használatával olvashatja el az adatolvasást. Például: select * from Sajáttábla. |Nem (ha meg van adva a "collectionName" az adatkészletben) |

**Példa**

```json
"activities":[
    {
        "name": "CopyFromMongoDB",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<MongoDB input dataset name>",
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
                "type": "MongoDbSource",
                "query": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

> [!TIP]
> Az SQL-lekérdezés megadásakor figyeljen a DateTime formátumra. Például: `SELECT * FROM Account WHERE LastModifiedDate >= '2018-06-01' AND LastModifiedDate < '2018-06-02'` vagy a paraméter használata `SELECT * FROM Account WHERE LastModifiedDate >= '@{formatDateTime(pipeline().parameters.StartTime,'yyyy-MM-dd HH:mm:ss')}' AND LastModifiedDate < '@{formatDateTime(pipeline().parameters.EndTime,'yyyy-MM-dd HH:mm:ss')}'`

## <a name="schema-by-data-factory"></a>Séma Data Factory szerint

Azure Data Factory a szolgáltatás egy MongoDB-gyűjtemény sémáját a gyűjteményben a **legújabb 100-dokumentumok** használatával. Ha ezek a 100-dokumentumok nem tartalmaznak teljes sémát, egyes oszlopok figyelmen kívül hagyhatók a másolási művelet során.

## <a name="data-type-mapping-for-mongodb"></a>Adattípusok leképezése MongoDB

Az adatok MongoDB-ből való másolása során a rendszer a következő leképezéseket használja a MongoDB adattípusok között az ideiglenes adattípusok Azure Data Factory. A másolási tevékenység a forrás sémájának és adattípusának a fogadóba való leképezésével kapcsolatos tudnivalókat lásd: [séma-és adattípus-leképezések](copy-activity-schema-and-type-mapping.md) .

| MongoDB adattípusa | Az adatgyár átmeneti adattípusa |
|:--- |:--- |
| Bináris |Bájt [] |
| Logikai érték |Logikai |
| Dátum |DateTime |
| NumberDouble |Dupla |
| NumberInt |Int32 |
| NumberLong |Int64 |
| ObjectID |Sztring |
| Sztring |Sztring |
| UUID |Guid |
| Objektum |Normalizálva a "_" oszlopokkal beágyazott elválasztóként |

> [!NOTE]
> Ha többet szeretne megtudni a virtuális táblákat használó tömbök támogatásáról, tekintse meg az [összetett típusok támogatása virtuális táblák használatával](#support-for-complex-types-using-virtual-tables) című szakaszt.
>
> Jelenleg a következő MongoDB-adattípusok nem támogatottak: DBPointer, JavaScript, max/min kulcs, reguláris kifejezés, szimbólum, időbélyeg, nincs meghatározva.

## <a name="support-for-complex-types-using-virtual-tables"></a>Összetett típusok támogatása virtuális táblák használatával

A Azure Data Factory egy beépített ODBC-illesztővel csatlakozik a MongoDB-adatbázishoz, és másolja azokat. Az olyan összetett típusok esetében, mint például a tömbök vagy különböző típusú objektumok a dokumentumokban, az illesztőprogram újranormalizálja az adatmennyiséget a megfelelő virtuális táblákba. Pontosabban, ha egy tábla ilyen oszlopokat tartalmaz, az illesztőprogram a következő virtuális táblákat hozza létre:

* Egy **alaptábla**, amely ugyanazokat az adatokkal rendelkezik, mint a valós tábla, kivéve a komplex típusú oszlopokat. Az alaptábla ugyanazt a nevet használja, mint az azt jelképező valódi tábla.
* Az egyes komplex típusú oszlopokhoz tartozó **virtuális táblázat** , amely kibővíti a beágyazott adattípusokat. A virtuális táblák neve a valódi tábla, a "_" elválasztója és a tömb vagy objektum neve alapján történik.

A virtuális táblák a valós táblázatba tartozó, az illesztőprogramnak a denormalizált információhoz való hozzáférésének engedélyezésére vonatkoznak. A MongoDB-tömbök tartalmát a virtuális táblák lekérdezésével és a hozzájuk való csatlakozással érheti el.

### <a name="example"></a>Példa

Például a ExampleTable itt egy olyan MongoDB-táblázat, amely egyetlen oszloppal rendelkezik, és az egyes cellákban található objektumok tömbje, valamint egy skaláris típusú tömbvel rendelkező oszlop – értékelések.

| _id | Ügyfél neve | Számlák | Szolgáltatásszint | Minősítések |
| --- | --- | --- | --- | --- |
| 1111 |ABC |[{invoice_id: "123", elem: "kenyérpirító", Ár: "456", kedvezmény: "0.2"}, {invoice_id: "124", elem: "sütő", Ár: "1235", kedvezmény: "0,2"}] |Ezüst |[5, 6] |
| 2222 |XYZ |[{invoice_id: "135", elem: "hűtőszekrény", Ár: "12543", kedvezmény: "0,0"}] |Arany |[1, 2] |

Az illesztőprogram több virtuális táblát fog előállítani, hogy ezt az egyetlen táblát képviseljék. Az első virtuális tábla a "ExampleTable" nevű alaptábla, amely a példában látható. Az alaptábla az eredeti tábla összes adatát tartalmazza, de a tömbökből származó adatok ki lettek hagyva, és ki lettek bontva a virtuális táblákban.

| _id | Ügyfél neve | Szolgáltatásszint |
| --- | --- | --- |
| 1111 |ABC |Ezüst |
| 2222 |XYZ |Arany |

A következő táblázatok a példában szereplő eredeti tömböket képviselő virtuális táblákat mutatják be. Ezek a táblák a következőket tartalmazzák:

* Az eredeti tömb soraihoz tartozó eredeti elsődleges kulcs oszlopra való hivatkozás (a _id oszlopon keresztül)
* Az eredeti tömbben lévő adatok pozíciójának jelzése
* A tömbben lévő egyes elemek kibontott adathalmaza

**"ExampleTable_Invoices" tábla:**

| _id | ExampleTable_Invoices_dim1_idx | invoice_id | item | price | Kedvezmény |
| --- | --- | --- | --- | --- | --- |
| 1111 |0 |123 |kenyérpirító |456 |0,2 |
| 1111 |1 |124 |sütő |1235 |0,2 |
| 2222 |0 |135 |hűtőszekrény |12543 |0,0 |

**"ExampleTable_Ratings" tábla:**

| _id | ExampleTable_Ratings_dim1_idx | ExampleTable_Ratings |
| --- | --- | --- |
| 1111 |0 |5 |
| 1111 |1 |6 |
| 2222 |0 |1 |
| 2222 |1 |2 |

## <a name="next-steps"></a>Következő lépések
A Azure Data Factory a másolási tevékenység által forrásként és nyelőként támogatott adattárak listáját lásd: [támogatott adattárak](copy-activity-overview.md#supported-data-stores-and-formats).
