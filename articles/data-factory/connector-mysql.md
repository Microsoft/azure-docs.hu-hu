---
title: Adatok másolása a MySQL-ből Azure Data Factory használatával
description: Ismerkedjen meg a MySQL-összekötővel Azure Data Factoryban, amely lehetővé teszi, hogy egy MySQL-adatbázisból egy fogadóként támogatott adattárba másoljon adatokból.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: jingwang
ms.openlocfilehash: 03f8bffafe9ebfd95d439f920a5e00be27810c96
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75444230"
---
# <a name="copy-data-from-mysql-using-azure-data-factory"></a>Adatok másolása a MySQL-ből Azure Data Factory használatával
> [!div class="op_single_selector" title1="Válassza ki az Ön által használt Data Factory-szolgáltatás verzióját:"]
> * [1-es verzió](v1/data-factory-onprem-mysql-connector.md)
> * [Aktuális verzió](connector-mysql.md)

Ez a cikk azt ismerteti, hogyan használható a másolási tevékenység a Azure Data Factoryban az adatok MySQL-adatbázisból való másolásához. A másolási [tevékenység áttekintő](copy-activity-overview.md) cikkében található, amely a másolási tevékenység általános áttekintését jeleníti meg.

>[!NOTE]
>Az adatok [Azure Database for MySQL](../mysql/overview.md) szolgáltatásból történő másolásához használja a speciális [Azure Database for MySQL-összekötőt](connector-azure-database-for-mysql.md).

## <a name="supported-capabilities"></a>Támogatott képességek

Ez a MySQL-összekötő a következő tevékenységek esetén támogatott:

- [Másolási tevékenység](copy-activity-overview.md) [támogatott forrás/fogadó mátrixtal](copy-activity-overview.md)
- [Keresési tevékenység](control-flow-lookup-activity.md)

Az adatok a MySQL-adatbázisból bármilyen támogatott fogadó adattárba másolhatók. A másolási tevékenység által a forrásként/mosogatóként támogatott adattárak listáját a [támogatott adattárak](copy-activity-overview.md#supported-data-stores-and-formats) táblázatban tekintheti meg.

Pontosabban, ez a MySQL-összekötő a **5,6-es és a 5,7-es MySQL-verziót**támogatja.

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

A Integration Runtime a 3,7-es verziótól kezdődően beépített MySQL-illesztőprogramot biztosít, ezért nem kell manuálisan telepítenie az illesztőprogramokat.

Az 3,7-nál kisebb, saját üzemeltetésű IR-verziók esetében telepítenie kell a [MySQL-összekötőt/hálót a Microsoft Windows](https://dev.mysql.com/downloads/connector/net/) -verzióra a 6.6.5 és a 6.10.7 között a Integration Runtime gépen. Ez a 32 bites illesztőprogram kompatibilis a 64 bites IR-vel.

## <a name="getting-started"></a>Első lépések

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

A következő szakaszokban részletesen ismertetjük a MySQL-összekötőhöz tartozó Data Factory-entitások definiálásához használt tulajdonságokat.

## <a name="linked-service-properties"></a>Társított szolgáltatás tulajdonságai

A MySQL-hez társított szolgáltatás a következő tulajdonságokat támogatja:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A Type tulajdonságot a következőre kell beállítani: **MySQL** | Igen |
| connectionString | Az Azure Database for MySQL-példányhoz való kapcsolódáshoz szükséges adatok meghatározása.<br/> A jelszót a Azure Key Vaultban is elhelyezheti, és lekérheti a `password` konfigurációt a kapcsolatok sztringből. További részletekért tekintse meg a következő mintákat, és [tárolja a hitelesítő adatokat Azure Key Vault](store-credentials-in-key-vault.md) cikkben. | Igen |
| Connectvia tulajdonsággal | Az adattárhoz való kapcsolódáshoz használt [Integration Runtime](concepts-integration-runtime.md) . További tudnivalók az [Előfeltételek](#prerequisites) szakaszban olvashatók. Ha nincs megadva, az alapértelmezett Azure Integration Runtime használja. |Nem |

Egy tipikus `Server=<server>;Port=<port>;Database=<database>;UID=<username>;PWD=<password>`a kapcsolatok karakterlánca. További tulajdonságok állíthatók be az egyes esetekben:

| Tulajdonság | Leírás | Beállítások | Szükséges |
|:--- |:--- |:--- |:--- |
| SSLMode | Ez a beállítás határozza meg, hogy az illesztőprogram SSL-titkosítást és ellenőrzést használ-e a MySQL-hez való kapcsolódáskor. Például `SSLMode=<0/1/2/3/4>`| Letiltva (0)/ELŐNYben részesített (1) **(alapértelmezett)** /kötelező (2)/VERIFY_CA (3)/VERIFY_IDENTITY (4) | Nem |
| UseSystemTrustStore | Ezzel a beállítással adható meg, hogy a rendszer egy HITELESÍTÉSSZOLGÁLTATÓI tanúsítványt használ-e a rendszermegbízhatósági tárolóból vagy egy megadott PEM-fájlból. Például `UseSystemTrustStore=<0/1>;`| Engedélyezve (1)/Letiltva (0) **(alapértelmezett)** | Nem |

**Példa**

```json
{
    "name": "MySQLLinkedService",
    "properties": {
        "type": "MySql",
        "typeProperties": {
            "connectionString": "Server=<server>;Port=<port>;Database=<database>;UID=<username>;PWD=<password>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Példa: a Jelszó tárolása Azure Key Vault**

```json
{
    "name": "MySQLLinkedService",
    "properties": {
        "type": "MySql",
        "typeProperties": {
            "connectionString": "Server=<server>;Port=<port>;Database=<database>;UID=<username>;",
            "password": { 
                "type": "AzureKeyVaultSecret", 
                "store": { 
                    "referenceName": "<Azure Key Vault linked service name>", 
                    "type": "LinkedServiceReference" 
                }, 
                "secretName": "<secretName>" 
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

Ha a MySQL-hez társított szolgáltatást használta a következő hasznos adatokkal, akkor továbbra is támogatott, míg a rendszer azt javasolja, hogy az új továbbítást használja.

**Korábbi hasznos adatok:**

```json
{
    "name": "MySQLLinkedService",
    "properties": {
        "type": "MySql",
        "typeProperties": {
            "server": "<server>",
            "database": "<database>",
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

Az adatkészletek definiálásához rendelkezésre álló csoportok és tulajdonságok teljes listáját az [adatkészletek](concepts-datasets-linked-services.md) című cikkben találja. Ez a szakasz a MySQL-adatkészlet által támogatott tulajdonságok listáját tartalmazza.

Az adatok MySQL-ből való másolásához a következő tulajdonságok támogatottak:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | Az adatkészlet Type tulajdonságát a következőre kell beállítani: **MySqlTable** | Igen |
| tableName | A MySQL-adatbázisban található tábla neve. | Nem (ha a "lekérdezés" van megadva a tevékenység forrásában) |

**Példa**

```json
{
    "name": "MySQLDataset",
    "properties":
    {
        "type": "MySqlTable",
        "typeProperties": {},
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<MySQL linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

Ha `RelationalTable` gépelt adatkészletet használ, a rendszer továbbra is támogatja a-t, míg a rendszer azt javasolja, hogy az új továbbítást használja.

## <a name="copy-activity-properties"></a>Másolási tevékenység tulajdonságai

A tevékenységek definiálásához elérhető csoportok és tulajdonságok teljes listáját a [folyamatok](concepts-pipelines-activities.md) című cikkben találja. Ez a szakasz a MySQL-forrás által támogatott tulajdonságok listáját tartalmazza.

### <a name="mysql-as-source"></a>MySQL forrásként

Az adatok MySQL-ből történő másolásához a másolási tevékenység **forrása** szakaszban a következő tulajdonságok támogatottak:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A másolási tevékenység forrásának Type tulajdonságát a következőre kell beállítani: **MySqlSource** | Igen |
| lekérdezés | Az egyéni SQL-lekérdezés használatával olvassa be az adatolvasást. Például: `"SELECT * FROM MyTable"`. | Nem (ha meg van adva a "táblanév" az adatkészletben) |

**Példa**

```json
"activities":[
    {
        "name": "CopyFromMySQL",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<MySQL input dataset name>",
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
                "type": "MySqlSource",
                "query": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

Ha `RelationalSource` gépelt forrást használ, a rendszer továbbra is támogatja a-t, míg a rendszer azt javasolja, hogy az új továbbítást használja.

## <a name="data-type-mapping-for-mysql"></a>Adattípus-leképezés a MySQL-hez

Az adatok MySQL-ből való másolása során a rendszer a következő leképezéseket használja a MySQL-adattípusokból Azure Data Factory köztes adattípusokhoz. A másolási tevékenység a forrás sémájának és adattípusának a fogadóba való leképezésével kapcsolatos tudnivalókat lásd: [séma-és adattípus-leképezések](copy-activity-schema-and-type-mapping.md) .

| MySQL-adattípus | Az adatgyár átmeneti adattípusa |
|:--- |:--- |
| `bigint` |`Int64` |
| `bigint unsigned` |`Decimal` |
| `bit(1)` |`Boolean` |
| `bit(M), M>1`|`Byte[]`|
| `blob` |`Byte[]` |
| `bool` |`Int16` |
| `char` |`String` |
| `date` |`Datetime` |
| `datetime` |`Datetime` |
| `decimal` |`Decimal, String` |
| `double` |`Double` |
| `double precision` |`Double` |
| `enum` |`String` |
| `float` |`Single` |
| `int` |`Int32` |
| `int unsigned` |`Int64`|
| `integer` |`Int32` |
| `integer unsigned` |`Int64` |
| `long varbinary` |`Byte[]` |
| `long varchar` |`String` |
| `longblob` |`Byte[]` |
| `longtext` |`String` |
| `mediumblob` |`Byte[]` |
| `mediumint` |`Int32` |
| `mediumint unsigned` |`Int64` |
| `mediumtext` |`String` |
| `numeric` |`Decimal` |
| `real` |`Double` |
| `set` |`String` |
| `smallint` |`Int16` |
| `smallint unsigned` |`Int32` |
| `text` |`String` |
| `time` |`TimeSpan` |
| `timestamp` |`Datetime` |
| `tinyblob` |`Byte[]` |
| `tinyint` |`Int16` |
| `tinyint unsigned` |`Int16` |
| `tinytext` |`String` |
| `varchar` |`String` |
| `year` |`Int` |


## <a name="lookup-activity-properties"></a>Keresési tevékenység tulajdonságai

A tulajdonságok részleteinek megismeréséhez tekintse meg a [keresési tevékenységet](control-flow-lookup-activity.md).

## <a name="next-steps"></a>Következő lépések
A Azure Data Factory a másolási tevékenység által forrásként és nyelőként támogatott adattárak listáját lásd: [támogatott adattárak](copy-activity-overview.md#supported-data-stores-and-formats).
