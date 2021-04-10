---
title: Adatok másolása a Cassandra használatával Azure Data Factory
description: Megtudhatja, hogyan másolhatja át az adatait a Cassandra-ből a Azure Data Factory-folyamat másolási tevékenységének használatával támogatott fogadó adattárakba.
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.date: 08/12/2019
ms.author: jingwang
ms.openlocfilehash: a3cd3c3ae28ae302e9469a71d00054152a9b5fb5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "100383704"
---
# <a name="copy-data-from-cassandra-using-azure-data-factory"></a>Adatok másolása a Cassandra használatával Azure Data Factory
> [!div class="op_single_selector" title1="Válassza ki az Ön által használt Data Factory-szolgáltatás verzióját:"]
> * [1-es verzió](v1/data-factory-onprem-cassandra-connector.md)
> * [Aktuális verzió](connector-cassandra.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Ez a cikk azt ismerteti, hogyan használható a másolási tevékenység a Azure Data Factoryban adatok másolása Cassandra-adatbázisból. A másolási [tevékenység áttekintő](copy-activity-overview.md) cikkében található, amely a másolási tevékenység általános áttekintését jeleníti meg.

## <a name="supported-capabilities"></a>Támogatott képességek

Ez a Cassandra-összekötő a következő tevékenységek esetében támogatott:

- [Másolási tevékenység](copy-activity-overview.md) [támogatott forrás/fogadó mátrixtal](copy-activity-overview.md)
- [Keresési tevékenység](control-flow-lookup-activity.md)

Az adatok a Cassandra Database-ből bármilyen támogatott fogadó adattárba másolhatók. A másolási tevékenység által a forrásként/mosogatóként támogatott adattárak listáját a [támogatott adattárak](copy-activity-overview.md#supported-data-stores-and-formats) táblázatban tekintheti meg.

Pontosabban, ez a Cassandra Connector a következőket támogatja:

- Cassandra **2. x és 3. x verzió**.
- Adatok másolása az **alapszintű** vagy a **Névtelen** hitelesítés használatával.

>[!NOTE]
>A saját üzemeltetésű Integration Runtimeon futó tevékenységek esetében a Cassandra 3. x az IR 3,7-es és újabb verzió óta támogatott.

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

A Integration Runtime egy beépített Cassandra-illesztőprogramot biztosít, ezért nem kell manuálisan telepítenie az összes illesztőprogramot, amikor az adatok másolása a vagy a Cassandra értékről.

## <a name="getting-started"></a>Első lépések

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

A következő szakaszokban részletesen ismertetjük azokat a tulajdonságokat, amelyek a Cassandra connectorhoz tartozó Data Factory definiálására szolgálnak.

## <a name="linked-service-properties"></a>Társított szolgáltatás tulajdonságai

A Cassandra társított szolgáltatás a következő tulajdonságokat támogatja:

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| típus |A Type tulajdonságot a következőre kell beállítani: **Cassandra** |Yes |
| gazda |A Cassandra-kiszolgálók egy vagy több IP-címe vagy állomásneve.<br/>Megadhatja az IP-címek vagy állomásnevek vesszővel tagolt listáját, hogy az összes kiszolgálóhoz egyszerre kapcsolódjon. |Yes |
| port |A Cassandra-kiszolgáló által az ügyfélkapcsolatok figyeléséhez használt TCP-port. |Nem (az alapértelmezett érték 9042) |
| authenticationType | A Cassandra-adatbázishoz való kapcsolódáshoz használt hitelesítés típusa.<br/>Az engedélyezett értékek: **Basic** és **Anonymous**. |Yes |
| username |Adja meg a felhasználói fiók felhasználónevét. |Igen, ha a authenticationType értéke alapszintű. |
| jelszó |A felhasználói fiók jelszavának megadása. Megjelöli ezt a mezőt SecureString, hogy biztonságosan tárolja Data Factoryban, vagy [hivatkozjon a Azure Key Vault tárolt titkos kulcsra](store-credentials-in-key-vault.md). |Igen, ha a authenticationType értéke alapszintű. |
| Connectvia tulajdonsággal | Az adattárhoz való kapcsolódáshoz használt [Integration Runtime](concepts-integration-runtime.md) . További tudnivalók az [Előfeltételek](#prerequisites) szakaszban olvashatók. Ha nincs megadva, az alapértelmezett Azure Integration Runtime használja. |No |

>[!NOTE]
>Jelenleg nem támogatott a Cassandra-hez való kapcsolódás a TLS használatával.

**Példa**

```json
{
    "name": "CassandraLinkedService",
    "properties": {
        "type": "Cassandra",
        "typeProperties": {
            "host": "<host>",
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

Az adatkészletek definiálásához rendelkezésre álló csoportok és tulajdonságok teljes listáját az [adatkészletek](concepts-datasets-linked-services.md) című cikkben találja. Ez a szakasz a Cassandra adatkészlet által támogatott tulajdonságok listáját tartalmazza.

Az adatok Cassandra-ből való másolásához állítsa az adatkészlet Type (típus) tulajdonságát **CassandraTable** értékre. A következő tulajdonságok támogatottak:

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| típus | Az adatkészlet Type tulajdonságát a következőre kell beállítani: **CassandraTable** | Yes |
| kulcstartomány |A térköz vagy séma neve a Cassandra adatbázisban. |Nem (ha a "CassandraSource" paraméternél "lekérdezés" van megadva) |
| tableName |A tábla neve a Cassandra adatbázisban. |Nem (ha a "CassandraSource" paraméternél "lekérdezés" van megadva) |

**Példa**

```json
{
    "name": "CassandraDataset",
    "properties": {
        "type": "CassandraTable",
        "typeProperties": {
            "keySpace": "<keyspace name>",
            "tableName": "<table name>"
        },
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<Cassandra linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Másolási tevékenység tulajdonságai


A tevékenységek definiálásához elérhető csoportok és tulajdonságok teljes listáját a [folyamatok](concepts-pipelines-activities.md) című cikkben találja. Ez a szakasz a Cassandra Source által támogatott tulajdonságok listáját tartalmazza.

### <a name="cassandra-as-source"></a>Cassandra forrásként

Az adatok Cassandra-ből való másolásához állítsa a forrás típusát a másolás tevékenység **CassandraSource**. A másolási tevékenység **forrása** szakasz a következő tulajdonságokat támogatja:

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| típus | A másolási tevékenység forrásának Type tulajdonságát a következőre kell beállítani: **CassandraSource** | Yes |
| lekérdezés |Az egyéni lekérdezés használatával olvashatja el az adatolvasást. SQL-92 lekérdezés vagy CQL-lekérdezés. Lásd: [CQL-hivatkozás](https://docs.datastax.com/en/cql/3.1/cql/cql_reference/cqlReferenceTOC.html). <br/><br/>SQL-lekérdezés használatakor adja meg a **térköz nevét. a tábla neve** , amely a lekérdezni kívánt táblát jelöli. |Nem (ha meg van adva a "táblanév" és a "szóköz" az adatkészletben). |
| consistencyLevel |A konzisztencia szintje határozza meg, hogy hány replikának kell válaszolnia egy olvasási kérelemre, mielőtt adatvisszaad az ügyfélalkalmazás számára. Cassandra ellenőrzi a megadott számú replikát az adatolvasási kérelem teljesítéséhez. További részletek: az [adatkonzisztencia konfigurálása](https://docs.datastax.com/en/cassandra/2.1/cassandra/dml/dml_config_consistency_c.html) .<br/><br/>Az engedélyezett értékek: **egy**, **kettő**, **három**, **kvórum**, **mind**, **LOCAL_QUORUM**, **EACH_QUORUM** és **LOCAL_ONE**. |Nem (alapértelmezett érték `ONE` ) |

**Példa**

```json
"activities":[
    {
        "name": "CopyFromCassandra",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Cassandra input dataset name>",
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
                "type": "CassandraSource",
                "query": "select id, firstname, lastname from mykeyspace.mytable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="data-type-mapping-for-cassandra"></a>Cassandra típusú adattípusok leképezése

Az adatok Cassandra-ből való másolása során a rendszer a következő leképezéseket használja a Cassandra adattípusokból Azure Data Factory köztes adattípusokhoz. A másolási tevékenység a forrás sémájának és adattípusának a fogadóba való leképezésével kapcsolatos tudnivalókat lásd: [séma-és adattípus-leképezések](copy-activity-schema-and-type-mapping.md) .

| Cassandra adattípus | Az adatgyár átmeneti adattípusa |
|:--- |:--- |
| ASCII |Sztring |
| BIGINT |Int64 |
| BLOB |Bájt [] |
| LOGIKAI |Logikai |
| DECIMÁLIS |Tizedesjegy |
| DUPLÁN |Dupla |
| FLOAT |Egyirányú |
| INET |Sztring |
| INT |Int32 |
| TEXT |Sztring |
| IDŐBÉLYEGZŐ |DateTime |
| TIMEUUID |Guid |
| UUID |Guid |
| VARCHAR |Sztring |
| VARINT |Tizedesjegy |

> [!NOTE]
> A gyűjtési típusok (Térkép, beállítás, lista stb.) esetében tekintse át a következőt: a [Cassandra Collection types használata virtuális tábla használatával](#work-with-collections-using-virtual-table) szakasz.
>
> A felhasználó által definiált típusok nem támogatottak.
>
> A bináris oszlop és a sztring oszlop hosszának hossza nem lehet nagyobb, mint 4000.
>

## <a name="work-with-collections-using-virtual-table"></a>Gyűjtemények használata virtuális tábla használatával

A Azure Data Factory egy beépített ODBC-illesztővel csatlakozik a Cassandra-adatbázishoz, és másolja azokat. A gyűjtemény típusainak, például a map, a set és a List beállítás esetében az illesztőprogram a megfelelő virtuális táblákba újranormalizálja az adattípusokat. Pontosabban, ha egy tábla bármely gyűjtemény oszlopot tartalmaz, az illesztőprogram a következő virtuális táblákat hozza létre:

* Egy **alaptábla**, amely ugyanazokat az adatokkal rendelkezik, mint a valódi tábla, kivéve a gyűjtemény oszlopait. Az alaptábla ugyanazt a nevet használja, mint az azt jelképező valódi tábla.
* Az egyes gyűjtemények oszlopokhoz tartozó **virtuális táblázat** , amely kibővíti a beágyazott adattípusokat. A gyűjteményeket jelképező virtuális táblák a valódi tábla nevével, a "*VT*" elválasztóval és az oszlop nevével vannak elnevezve.

A virtuális táblák a valós táblázatba tartozó, az illesztőprogramnak a denormalizált információhoz való hozzáférésének engedélyezésére vonatkoznak. Részletekért lásd a példa szakaszt. A Cassandra Collections tartalmát a virtuális táblák lekérdezésével és a hozzájuk való csatlakozással érheti el.

### <a name="example"></a>Példa

Például a következő "ExampleTable" egy Cassandra adatbázis-tábla, amely egy "pk_int" nevű egész számú elsődleges kulcs oszlopot tartalmaz, egy érték nevű szöveges oszlop, egy lista oszlop, egy Térkép oszlop és egy beállított oszlop ("StringSet").

| pk_int | Érték | Lista | Térkép | StringSet |
| --- | --- | --- | --- | --- |
| 1 |"Sample Value 1" |["1", "2", "3"] |{"S1": "a", "S2": "b"} |{"A", "B", "C"} |
| 3 |"3. minta érték" |["100", "101", "102", "105"] |{"S1": "t"} |{"A", "E"} |

Az illesztőprogram több virtuális táblát fog előállítani, hogy ezt az egyetlen táblát képviseljék. A virtuális táblákban található idegenkulcs-oszlopok hivatkoznak a valós tábla elsődleges kulcs oszlopaira, és jelzik, hogy a virtuális tábla sorai melyik valós táblázatnak felelnek meg.

Az első virtuális tábla a "ExampleTable" nevű alaptábla a következő táblázatban látható: 

| pk_int | Érték |
| --- | --- |
| 1 |"Sample Value 1" |
| 3 |"3. minta érték" |

Az alaptábla ugyanazokat az adatokkal rendelkezik, mint az eredeti adatbázistábla, kivéve azokat a gyűjteményeket, amelyek ki vannak hagyva ebből a táblából, és más virtuális táblákban kibontva vannak.

A következő táblázatok azokat a virtuális táblákat mutatják be, amelyek a lista, a Térkép és a StringSet oszlopokból származó adatok újranormalizálása. A "_index" vagy "_key" végződésű neveket jelölő oszlopok az eredeti listán vagy a térképen belüli pozíciót jelölik. A "_value" végződésű neveket tartalmazó oszlopok a gyűjteményből származó kibontott adatokkal rendelkeznek.

**"ExampleTable_vt_List" tábla:**

| pk_int | List_index | List_value |
| --- | --- | --- |
| 1 |0 |1 |
| 1 |1 |2 |
| 1 |2 |3 |
| 3 |0 |100 |
| 3 |1 |101 |
| 3 |2 |102 |
| 3 |3 |103 |

**"ExampleTable_vt_Map" tábla:**

| pk_int | Map_key | Map_value |
| --- | --- | --- |
| 1 |S1 |A |
| 1 |S2 |b |
| 3 |S1 |t |

**"ExampleTable_vt_StringSet" tábla:**

| pk_int | StringSet_value |
| --- | --- |
| 1 |A |
| 1 |B |
| 1 |C |
| 3 |A |
| 3 |E |

## <a name="lookup-activity-properties"></a>Keresési tevékenység tulajdonságai

A tulajdonságok részleteinek megismeréséhez tekintse meg a [keresési tevékenységet](control-flow-lookup-activity.md).

## <a name="next-steps"></a>Következő lépések
A Azure Data Factory a másolási tevékenység által forrásként és nyelőként támogatott adattárak listáját lásd: [támogatott adattárak](copy-activity-overview.md#supported-data-stores-and-formats).
