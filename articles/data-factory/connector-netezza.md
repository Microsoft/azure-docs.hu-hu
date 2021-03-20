---
title: Adatok másolása a Netezza a Azure Data Factory használatával
description: Bemutatjuk, hogy miként másolhatók adatok a Netezza-ből a támogatott fogadó adattárakba egy Azure Data Factory folyamat másolási tevékenységének használatával.
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.date: 05/28/2020
ms.author: jingwang
ms.openlocfilehash: 4b12c1f24f389634004de3d487a693bc588a7241
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "100374354"
---
# <a name="copy-data-from-netezza-by-using-azure-data-factory"></a>Adatok másolása a Netezza a Azure Data Factory használatával
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Ez a cikk azt ismerteti, hogyan használható a másolási tevékenység a Azure Data Factoryban az adatok Netezza való másolásához. A cikk a [másolási tevékenységre épül Azure Data Factoryban](copy-activity-overview.md), amely a másolási tevékenység általános áttekintését mutatja be.

>[!TIP]
>A Netezza-ből az Azure-ba irányuló adatáttelepítési forgatókönyvek esetében további információ a [Azure Data Factory használatával a helyszíni Netezza-kiszolgálóról az Azure-ba való Migrálás](data-migration-guidance-netezza-azure-sqldw.md)során.

## <a name="supported-capabilities"></a>Támogatott képességek

Ez a Netezza-összekötő a következő tevékenységek esetén támogatott:

- [Másolási tevékenység](copy-activity-overview.md) [támogatott forrás/fogadó mátrixtal](copy-activity-overview.md)
- [Keresési tevékenység](control-flow-lookup-activity.md)


Az adatok a Netezza bármely támogatott fogadó adattárba másolhatók. A másolási tevékenység által a forrásként és a fogadóként támogatott adattárak listájáért lásd: [támogatott adattárak és-formátumok](copy-activity-overview.md#supported-data-stores-and-formats).

Az Netezza-összekötő támogatja a forrásról való párhuzamos másolást. A részletekért tekintse meg a [Parallel másolás a Netezza](#parallel-copy-from-netezza) szakaszát.

A Azure Data Factory egy beépített illesztőprogramot biztosít a kapcsolat engedélyezéséhez. Az összekötő használatához nem kell manuálisan telepítenie az illesztőprogramot.

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

## <a name="get-started"></a>Bevezetés

A másolási tevékenységet használó folyamatokat a .NET SDK, a Python SDK, a Azure PowerShell, a REST API vagy egy Azure Resource Manager sablon használatával is létrehozhatja. A másolási tevékenységgel rendelkező folyamat létrehozásával kapcsolatos részletes utasításokért tekintse meg a [másolási tevékenységről szóló oktatóanyagot](quickstart-create-data-factory-dot-net.md) .

A következő szakaszokban részletesen ismertetjük azokat a tulajdonságokat, amelyekkel meghatározhatja Data Factory az Netezza-összekötőhöz tartozó entitásokat.

## <a name="linked-service-properties"></a>Társított szolgáltatás tulajdonságai

A Netezza társított szolgáltatás a következő tulajdonságokat támogatja:

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| típus | A **Type** tulajdonságot **Netezza** értékre kell beállítani. | Yes |
| connectionString | ODBC-kapcsolati sztring a Netezza való kapcsolódáshoz. <br/>A jelszót a Azure Key Vaultban is elhelyezheti, és lekérheti a `pwd` konfigurációt a kapcsolatok sztringből. További részletekért tekintse meg a következő mintákat, és [tárolja a hitelesítő adatokat Azure Key Vault](store-credentials-in-key-vault.md) cikkben. | Yes |
| Connectvia tulajdonsággal | Az adattárhoz való kapcsolódáshoz használt [Integration Runtime](concepts-integration-runtime.md) . További tudnivalók az [Előfeltételek](#prerequisites) szakaszban olvashatók. Ha nincs megadva, a rendszer az alapértelmezett Azure Integration Runtime használja. |No |

Egy tipikus kapcsolatok karakterlánca: `Server=<server>;Port=<port>;Database=<database>;UID=<user name>;PWD=<password>` . A következő táblázat a további beállítható tulajdonságokat ismerteti:

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| SecurityLevel | Az a biztonsági szint, amelyet az illesztőprogram az adattárhoz való kapcsolódáshoz használ. Az illesztőprogram támogatja az SSL-kapcsolatokat az SSL 3-as verziójának használatával történő egyirányú hitelesítéssel. <br>Példa: `SecurityLevel=preferredSecured`. A támogatott értékek a következők:<br/>- **Csak nem biztonságos** (**OnlyUnSecured**): az illesztőprogram nem használ SSL-t.<br/>- **Előnyben részesített nem védett (preferredUnSecured) (alapértelmezett)**: Ha a kiszolgáló választási lehetőséget biztosít, az illesztőprogram nem használ SSL-t. <br/>- **Előnyben részesített biztonságos (preferredSecured)**: Ha a kiszolgáló választási lehetőséget biztosít, az illesztőprogram SSL-t használ. <br/>- **Csak biztonságos (onlySecured)**: az illesztőprogram nem csatlakozik, ha nem érhető el SSL-kapcsolat. | No |
| HitelesítésszolgáltatóiTanúsítványfájl | A kiszolgáló által használt SSL-tanúsítvány teljes elérési útja. Például: `CaCertFile=<cert path>;`| Igen, ha az SSL engedélyezve van |

**Példa**

```json
{
    "name": "NetezzaLinkedService",
    "properties": {
        "type": "Netezza",
        "typeProperties": {
            "connectionString": "Server=<server>;Port=<port>;Database=<database>;UID=<user name>;PWD=<password>"
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
    "name": "NetezzaLinkedService",
    "properties": {
        "type": "Netezza",
        "typeProperties": {
            "connectionString": "Server=<server>;Port=<port>;Database=<database>;UID=<user name>;",
            "pwd": { 
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

Ez a szakasz a Netezza-adatkészlet által támogatott tulajdonságok listáját tartalmazza.

Az adatkészletek definiálásához rendelkezésre álló csoportok és tulajdonságok teljes listáját lásd: [adatkészletek](concepts-datasets-linked-services.md).

Az adatok Netezza való másolásához állítsa az adatkészlet **Type (típus** ) tulajdonságát **NetezzaTable** értékre. A következő tulajdonságok támogatottak:

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| típus | Az adatkészlet Type tulajdonságát a következőre kell beállítani: **NetezzaTable** | Yes |
| schema | A séma neve. |Nem (ha a "lekérdezés" van megadva a tevékenység forrásában)  |
| tábla | A tábla neve. |Nem (ha a "lekérdezés" van megadva a tevékenység forrásában)  |
| tableName | A sémával rendelkező tábla neve. Ez a tulajdonság visszamenőleges kompatibilitás esetén támogatott. `schema`A és `table` az új számítási feladatok használata. | Nem (ha a "lekérdezés" van megadva a tevékenység forrásában) |

**Példa**

```json
{
    "name": "NetezzaDataset",
    "properties": {
        "type": "NetezzaTable",
        "linkedServiceName": {
            "referenceName": "<Netezza linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {}
    }
}
```

## <a name="copy-activity-properties"></a>Másolási tevékenység tulajdonságai

Ez a szakasz a Netezza forrás által támogatott tulajdonságok listáját tartalmazza.

A tevékenységek definiálásához elérhető csoportok és tulajdonságok teljes listáját lásd: [folyamatok](concepts-pipelines-activities.md).

### <a name="netezza-as-source"></a>Netezza forrásként

>[!TIP]
>Az adatok Netezza történő hatékony betöltéséhez az adatparticionálás használatával tudjon meg többet a [párhuzamos másolásról a Netezza](#parallel-copy-from-netezza) szakaszból.

Az adatok Netezza való másolásához állítsa a **forrás** típusát a másolás tevékenység **NetezzaSource** értékére. A másolási tevékenység **forrása** szakasz a következő tulajdonságokat támogatja:

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| típus | A másolási tevékenység forrásának **Type** tulajdonságát **NetezzaSource** értékre kell állítani. | Yes |
| lekérdezés | Az egyéni SQL-lekérdezés használatával olvassa be az adatolvasást. Például: `"SELECT * FROM MyTable"` | Nem (ha meg van adva a "táblanév" az adatkészletben) |
| partitionOptions | Meghatározza az adatok Netezza való betöltéséhez használt adatparticionálási beállításokat. <br>Értékek engedélyezése: **none** (alapértelmezett), **DataSlice** és **DynamicRange**.<br>Ha engedélyezve van egy partíciós beállítás (azaz nem `None` ), a Netezza-adatbázisból származó adatok párhuzamos betöltésének foka a [`parallelCopies`](copy-activity-performance-features.md#parallel-copy) másolási tevékenység beállításával szabályozható. | No |
| partitionSettings | Határozza meg az adatparticionálási beállítások csoportját. <br>Akkor alkalmazza, ha a partíció beállítása nem `None` . | No |
| partitionColumnName | Adja meg a forrás oszlop **olyan egész típusú** nevét, amelyet a párhuzamos másolási tartomány particionálásakor fog használni. Ha nincs megadva, a rendszer automatikusan felismeri a tábla elsődleges kulcsát, és a partíció oszlopként használja. <br>Akkor alkalmazza, ha a partíciós beállítás van `DynamicRange` . Ha lekérdezést használ a forrásadatok beolvasásához, akkor a WHERE záradékot kell beolvasnia `?AdfRangePartitionColumnName` . Lásd: példa [párhuzamos másolással a Netezza](#parallel-copy-from-netezza) szakaszból. | No |
| partitionUpperBound | Az adatmásolásra szolgáló partíciós oszlop maximális értéke. <br>Akkor alkalmazza, ha a partíció lehetőség van `DynamicRange` . Ha lekérdezést használ a forrásadatok beolvasásához, `?AdfRangePartitionUpbound` a WHERE záradékban lévő hookot. Példaként tekintse meg a [Parallel másolás a Netezza](#parallel-copy-from-netezza) szakaszát. | No |
| partitionLowerBound | Az adatmásolásra szolgáló partíciós oszlop minimális értéke. <br>Akkor alkalmazza, ha a partíciós beállítás van `DynamicRange` . Ha lekérdezést használ a forrásadatok beolvasásához, `?AdfRangePartitionLowbound` a WHERE záradékban lévő hookot. Példaként tekintse meg a [Parallel másolás a Netezza](#parallel-copy-from-netezza) szakaszát. | No |

**Példa**

```json
"activities":[
    {
        "name": "CopyFromNetezza",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Netezza input dataset name>",
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
                "type": "NetezzaSource",
                "query": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="parallel-copy-from-netezza"></a>Párhuzamos másolás a Netezza

A Data Factory Netezza-összekötő beépített adatparticionálást biztosít az adatok párhuzamos Netezza való másolásához. Az adatparticionálási beállítások a másolási tevékenység **forrástábla** tábláján találhatók.

![Képernyőfelvétel a partíciós beállításokról](./media/connector-netezza/connector-netezza-partition-options.png)

A particionált másolás engedélyezésekor a Data Factory párhuzamos lekérdezéseket futtat a Netezza-forráson az adatpartíciók betöltéséhez. A párhuzamos mértéket a [`parallelCopies`](copy-activity-performance-features.md#parallel-copy) másolási tevékenység beállításai vezérlik. Ha például a négy értékre van állítva `parallelCopies` , Data Factory egyidejűleg létrehoz és futtat négy lekérdezést a megadott partíciós beállítás és beállítások alapján, és mindegyik lekérdezés a Netezza-adatbázisból származó adatok egy részét kéri le.

Javasoljuk, hogy engedélyezze a párhuzamos másolást az adatok particionálásával, különösen akkor, ha nagy mennyiségű adatmennyiséget tölt be a Netezza-adatbázisból. Az alábbiakban a különböző forgatókönyvekhez javasolt konfigurációk szerepelnek. Az adatok file-alapú adattárba való másolása során a rendszer úgy helyezi át, hogy több fájlként írjon egy mappába (csak a mappa nevét adja meg), amely esetben a teljesítmény jobb, mint egyetlen fájl írásakor.

| Eset                                                     | Javasolt beállítások                                           |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| Teljes terhelés a nagyméretű táblából.                                   | **Partíciós beállítás**: adatszelet. <br><br/>A végrehajtás során a Data Factory automatikusan particionálja az [Netezza-t a beépített adatszeletek](https://www.ibm.com/support/knowledgecenter/en/SSULQD_7.2.1/com.ibm.nz.adm.doc/c_sysadm_data_slices_parts_disks.html)alapján, és az Adatmásolást partíciók szerint másolja. |
| Nagy mennyiségű adattal tölthetők be egyéni lekérdezések használatával.                 | **Partíciós beállítás**: adatszelet.<br>**Lekérdezés**: `SELECT * FROM <TABLENAME> WHERE mod(datasliceid, ?AdfPartitionCount) = ?AdfDataSliceCondition AND <your_additional_where_clause>` .<br>A végrehajtás során Data Factory lecseréli `?AdfPartitionCount` (párhuzamos másolási számmal a másolási tevékenységnél) és `?AdfDataSliceCondition` az adatszelet partíciós logikáját, és elküldi a Netezza. |
| Nagy mennyiségű adattal tölthetők be egy egyéni lekérdezéssel, amely egy egész számú oszlopból álló, egyenletesen elosztott értékkel rendelkezik a tartomány particionálásához. | **Partíciós beállítások**: dinamikus tartományú partíció.<br>**Lekérdezés**: `SELECT * FROM <TABLENAME> WHERE ?AdfRangePartitionColumnName <= ?AdfRangePartitionUpbound AND ?AdfRangePartitionColumnName >= ?AdfRangePartitionLowbound AND <your_additional_where_clause>` .<br>**Partíciós oszlop**: az adatparticionáláshoz használt oszlop megadására szolgál. Az oszlop egész adattípussal is particionálható.<br>**Partíció felső határa** és a **partíció alsó határa**: adja meg, hogy a partíció oszlop alapján szeretne-e szűrést végezni, hogy csak az alsó és a felső tartomány között legyenek lekérdezve.<br><br>A végrehajtás során a Data Factory lecseréli, `?AdfRangePartitionColumnName` `?AdfRangePartitionUpbound` és az `?AdfRangePartitionLowbound` egyes partíciók tényleges oszlop-és értéktartomány-tartományát, és elküldi a Netezza. <br>Ha például az "ID" partíciós oszlop az 1 értékre van állítva, a felső határ pedig 80, a párhuzamos másolási beállítás értéke 4, Data Factory 4 partíció alapján kérdezi le az adatmennyiséget. Az azonosítóik [1, 20], [21, 40], [41, 60] és [61, 80] között vannak. |

**Példa: lekérdezés adatszelet-partícióval**

```json
"source": {
    "type": "NetezzaSource",
    "query": "SELECT * FROM <TABLENAME> WHERE mod(datasliceid, ?AdfPartitionCount) = ?AdfDataSliceCondition AND <your_additional_where_clause>",
    "partitionOption": "DataSlice"
}
```

**Példa: lekérdezés dinamikus tartományú partícióval**

```json
"source": {
    "type": "NetezzaSource",
    "query": "SELECT * FROM <TABLENAME> WHERE ?AdfRangePartitionColumnName <= ?AdfRangePartitionUpbound AND ?AdfRangePartitionColumnName >= ?AdfRangePartitionLowbound AND <your_additional_where_clause>",
    "partitionOption": "DynamicRange",
    "partitionSettings": {
        "partitionColumnName": "<dynamic_range_partition_column_name>",
        "partitionUpperBound": "<upper_value_of_partition_column>",
        "partitionLowerBound": "<lower_value_of_partition_column>"
    }
}
```

## <a name="lookup-activity-properties"></a>Keresési tevékenység tulajdonságai

A tulajdonságok részleteinek megismeréséhez tekintse meg a [keresési tevékenységet](control-flow-lookup-activity.md).


## <a name="next-steps"></a>Következő lépések

A másolási tevékenység által támogatott adattárak listáját a Azure Data Factoryban található forrásként és nyelőként tekintse meg a [támogatott adattárak és-formátumok](copy-activity-overview.md#supported-data-stores-and-formats)című témakörben.
