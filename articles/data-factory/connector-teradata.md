---
title: Adatok másolása a Teradata Vantage-ból Azure Data Factory használatával
description: A Data Factory szolgáltatás Teradata-összekötője lehetővé teszi az adatok másolását a Data Factory által támogatott adattárakba a Teradata.
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/22/2021
ms.author: jingwang
ms.openlocfilehash: c65b295dca16a3453b2ee5472b9a3ee5e8d62df7
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/14/2021
ms.locfileid: "100381766"
---
# <a name="copy-data-from-teradata-vantage-by-using-azure-data-factory"></a>Adatok másolása a Teradata Vantage-ból Azure Data Factory használatával

> [!div class="op_single_selector" title1="Válassza ki az Ön által használt Data Factory-szolgáltatás verzióját:"]
>
> * [1-es verzió](v1/data-factory-onprem-teradata-connector.md)
> * [Aktuális verzió](connector-teradata.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Ez a cikk azt ismerteti, hogyan használható a másolási tevékenység a Azure Data Factoryban az adatok másolásához a Teradata Vantage-ból. A [másolási tevékenység áttekintésére](copy-activity-overview.md)épül.

## <a name="supported-capabilities"></a>Támogatott képességek

Ez a Teradata-összekötő a következő tevékenységek esetén támogatott:

- [Másolási tevékenység](copy-activity-overview.md) [támogatott forrás/fogadó mátrixtal](copy-activity-overview.md)
- [Keresési tevékenység](control-flow-lookup-activity.md)

A Teradata Vantage-ból származó adatok másolása bármely támogatott fogadó adattárba lehetséges. A másolási tevékenység által a forrásként/mosogatóként támogatott adattárak listáját a [támogatott adattárak](copy-activity-overview.md#supported-data-stores-and-formats) táblázatban tekintheti meg.

Ez a Teradata-összekötő a következőket támogatja:

- Teradata- **verzió: 14,10, 15,0, 15,10, 16,0, 16,10 és 16,20**.
- Adatok másolása **Alapszintű**, **Windows** vagy **LDAP** -hitelesítés használatával.
- Párhuzamos másolás egy Teradata-forrásból. A részletekért tekintse meg a [Parallel másolás a Teradata](#parallel-copy-from-teradata) szakaszát.

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

Ha saját üzemeltetésű Integration Runtime használ, vegye figyelembe, hogy a 3,18-es verziótól kezdődően beépített Teradata-illesztőprogramot biztosít. Nem kell manuálisan telepítenie az illesztőprogramot. Az illesztőprogramhoz a saját üzemeltetésű Integration Runtime-gépen a "Visual C++ Redistributable 2012 Update 4" szükséges. Ha még nincs telepítve, [töltse le innen.](https://www.microsoft.com/en-sg/download/details.aspx?id=30679)

## <a name="getting-started"></a>Első lépések

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

A következő szakaszokban részletesen ismertetjük a Teradata-összekötőhöz tartozó Data Factory entitások definiálásához használt tulajdonságokat.

## <a name="linked-service-properties"></a>Társított szolgáltatás tulajdonságai

A Teradata társított szolgáltatás a következő tulajdonságokat támogatja:

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| típus | A Type tulajdonságot **Teradata** értékre kell beállítani. | Yes |
| connectionString | Meghatározza az Teradata-példányhoz való kapcsolódáshoz szükséges adatokat. Tekintse át a következő mintákat.<br/>A jelszót Azure Key Vaultba is helyezheti, és lekérheti a `password` konfigurációt a kapcsolatok sztringből. További részletekért tekintse meg a [hitelesítő adatok tárolása Azure Key Vaultban](store-credentials-in-key-vault.md) című témakört. | Yes |
| username | Adja meg a Teradata való kapcsolódáshoz használandó felhasználónevet. Windows-hitelesítés használatakor érvényes. | No |
| jelszó | Adja meg a felhasználónévhez megadott felhasználói fiók jelszavát. Azt is megteheti, hogy [hivatkozik Azure Key Vault tárolt titkos kulcsra](store-credentials-in-key-vault.md). <br>A Windows-hitelesítés használatakor, vagy az alapszintű hitelesítéshez Key Vault jelszóra hivatkozik. | No |
| Connectvia tulajdonsággal | Az adattárhoz való kapcsolódáshoz használt [Integration Runtime](concepts-integration-runtime.md) . További tudnivalók az [Előfeltételek](#prerequisites) szakaszban olvashatók. Ha nincs megadva, az alapértelmezett Azure Integration Runtime használja. |No |

További kapcsolatok tulajdonságai az adott esetben a kapcsolatok karakterláncában állíthatók be:

| Tulajdonság | Leírás | Alapértelmezett érték |
|:--- |:--- |:--- |
| TdmstPortNumber | A Teradata-adatbázis eléréséhez használt port száma.<br>Ne módosítsa ezt az értéket, hacsak erre a technikai támogatás nem utasítja. | 1025 |
| UseDataEncryption | Meghatározza, hogy a Teradata-adatbázissal folytatott összes kommunikáció titkosítva legyen-e. Az engedélyezett értékek: 0 vagy 1.<br><br/>- **0 (letiltva, alapértelmezett)**: csak a hitelesítési adatok titkosítása.<br/>- **1 (engedélyezve)**: titkosítja az illesztőprogram és az adatbázis között átadott összes adathalmazt. | `0` |
| CharacterSet | A munkamenethez használandó karakterkészlet Például: `CharacterSet=UTF16` .<br><br/>Ez az érték lehet felhasználó által definiált karakterkészlet, vagy a következő előre definiált karakterkészletek egyike: <br/>– ASCII<br/>-UTF8<br/>– UTF16<br/>– LATIN1252_0A<br/>– LATIN9_0A<br/>– LATIN1_0A<br/>-SHIFT-JIS (Windows, DOS-kompatibilis, KANJISJIS_0S)<br/>-EUC (UNIX-kompatibilis, KANJIEC_0U)<br/>-IBM mainframe (KANJIEBCDIC5035_0I)<br/>– KANJI932_1S0<br/>-BIG5 (TCHBIG5_1R0)<br/>-GB (SCHGB2312_1T0)<br/>– SCHINESE936_6R0<br/>– TCHINESE950_8R0<br/>-NetworkKorean (HANGULKSC5601_2R4)<br/>– HANGUL949_7R0<br/>– ARABIC1256_6A0<br/>– CYRILLIC1251_2A0<br/>– HEBREW1255_5A0<br/>– LATIN1250_1A0<br/>– LATIN1254_7A0<br/>– LATIN1258_8A0<br/>– THAI874_4A0 | `ASCII` |
| MaxRespSize |Az SQL-kérelmekre vonatkozó válasz puffer maximális mérete (kilobájtban) (Tudásbázis). Például: `MaxRespSize=‭10485760‬` .<br/><br/>A Teradata-adatbázis 16,00-es vagy újabb verziójához a maximális érték a 7361536. A korábbi verziókat használó kapcsolatok esetében a maximális érték 1048576. | `65536` |
| MechanismName | Ha az LDAP protokollt szeretné használni a kapcsolat hitelesítéséhez, válassza a következőt: `MechanismName=LDAP` . | N/A |

**Példa egyszerű hitelesítés használatával**

```json
{
    "name": "TeradataLinkedService",
    "properties": {
        "type": "Teradata",
        "typeProperties": {
            "connectionString": "DBCName=<server>;Uid=<username>;Pwd=<password>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Példa Windows-hitelesítés használatára**

```json
{
    "name": "TeradataLinkedService",
    "properties": {
        "type": "Teradata",
        "typeProperties": {
            "connectionString": "DBCName=<server>",
            "username": "<username>",
            "password": "<password>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Példa LDAP-hitelesítés használatára**

```json
{
    "name": "TeradataLinkedService",
    "properties": {
        "type": "Teradata",
        "typeProperties": {
            "connectionString": "DBCName=<server>;MechanismName=LDAP;Uid=<username>;Pwd=<password>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

> [!NOTE]
>
> A következő hasznos adatok továbbra is támogatottak. Továbbra is az újat kell használnia.

**Korábbi hasznos adatok:**

```json
{
    "name": "TeradataLinkedService",
    "properties": {
        "type": "Teradata",
        "typeProperties": {
            "server": "<server>",
            "authenticationType": "<Basic/Windows>",
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

Ez a szakasz a Teradata adatkészlet által támogatott tulajdonságok listáját tartalmazza. Az adatkészletek definiálásához rendelkezésre álló csoportok és tulajdonságok teljes listáját lásd: [adatkészletek](concepts-datasets-linked-services.md).

Az adatok Teradata történő másolásához a következő tulajdonságok támogatottak:

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| típus | Az adatkészlet Type tulajdonságát be kell állítani `TeradataTable` . | Yes |
| adatbázis | Az Teradata-példány neve. | Nem (ha a "lekérdezés" van megadva a tevékenység forrásában) |
| tábla | A Teradata-példányban található tábla neve. | Nem (ha a "lekérdezés" van megadva a tevékenység forrásában) |

**Példa**

```json
{
    "name": "TeradataDataset",
    "properties": {
        "type": "TeradataTable",
        "typeProperties": {},
        "schema": [],        
        "linkedServiceName": {
            "referenceName": "<Teradata linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

> [!NOTE]
>
> `RelationalTable` a Type adatkészlet továbbra is támogatott. Javasoljuk azonban, hogy az új adatkészletet használja.

**Korábbi hasznos adatok:**

```json
{
    "name": "TeradataDataset",
    "properties": {
        "type": "RelationalTable",
        "linkedServiceName": {
            "referenceName": "<Teradata linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {}
    }
}
```

## <a name="copy-activity-properties"></a>Másolási tevékenység tulajdonságai

Ez a szakasz a Teradata forrás által támogatott tulajdonságok listáját tartalmazza. A tevékenységek definiálásához rendelkezésre álló csoportok és tulajdonságok teljes listáját lásd: [folyamatok](concepts-pipelines-activities.md). 

### <a name="teradata-as-source"></a>Teradata forrásként

>[!TIP]
>Az adatok Teradata történő hatékony betöltéséhez az adatparticionálás használatával tudjon meg többet a [párhuzamos másolásról a Teradata](#parallel-copy-from-teradata) szakaszból.

Az adatok Teradata történő másolásához a másolási tevékenység **forrása** szakaszban a következő tulajdonságok támogatottak:

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| típus | A másolási tevékenység forrásának Type tulajdonságát a következőre kell beállítani: `TeradataSource` . | Yes |
| lekérdezés | Az egyéni SQL-lekérdezés használatával olvassa be az adatolvasást. Például: `"SELECT * FROM MyTable"`.<br>Ha engedélyezi a particionált terhelést, össze kell kapcsolnia a lekérdezéshez tartozó beépített partíciós paramétereket. Példákat a [párhuzamos másolás Teradata](#parallel-copy-from-teradata) szakaszban talál. | Nem (ha meg van adva táblázat az adatkészletben) |
| partitionOptions | Meghatározza az adatok Teradata való betöltéséhez használt adatparticionálási beállításokat. <br>Értékek engedélyezése: **none** (alapértelmezett), **kivonat** -és **DynamicRange**.<br>Ha engedélyezve van egy partíciós beállítás (azaz nem `None` ), akkor a Teradata származó adatok párhuzamos betöltésének fokát a [`parallelCopies`](copy-activity-performance-features.md#parallel-copy) másolási tevékenység beállításai vezérlik. | No |
| partitionSettings | Határozza meg az adatparticionálási beállítások csoportját. <br>Akkor alkalmazza, ha a partíció beállítása nem `None` . | No |
| partitionColumnName | Adja meg annak a forrás oszlopnak a nevét, amelyet a tartomány partíció vagy a kivonatoló partíció használ a párhuzamos másoláshoz. Ha nincs megadva, a rendszer automatikusan felismeri a tábla elsődleges indexét, és a partíciós oszlopként használja. <br>Akkor alkalmazza, ha a partíció beállítás értéke `Hash` vagy `DynamicRange` . Ha lekérdezést használ a forrásadatok, a Hook `?AdfHashPartitionCondition` vagy  `?AdfRangePartitionColumnName` a WHERE záradék beolvasásához. Lásd: példa [párhuzamos másolással a Teradata](#parallel-copy-from-teradata) szakaszból. | No |
| partitionUpperBound | Az adatmásolásra szolgáló partíciós oszlop maximális értéke. <br>Akkor alkalmazza, ha a partíció lehetőség van `DynamicRange` . Ha lekérdezést használ a forrásadatok beolvasásához, `?AdfRangePartitionUpbound` a WHERE záradékban lévő hookot. Példaként tekintse meg a [Parallel másolás a Teradata](#parallel-copy-from-teradata) szakaszát. | No |
| partitionLowerBound | Az adatmásolásra szolgáló partíciós oszlop minimális értéke. <br>Akkor alkalmazza, ha a partíciós beállítás van `DynamicRange` . Ha lekérdezést használ a forrásadatok beolvasásához, `?AdfRangePartitionLowbound` a WHERE záradékban lévő hookot. Példaként tekintse meg a [Parallel másolás a Teradata](#parallel-copy-from-teradata) szakaszát. | No |

> [!NOTE]
>
> `RelationalSource` a típus másolási forrása továbbra is támogatott, de nem támogatja az új, beépített párhuzamos terhelést a Teradata (partíciós beállítások). Javasoljuk azonban, hogy az új adatkészletet használja.

**Példa: adatok másolása egy alapszintű, partíció nélküli lekérdezés használatával**

```json
"activities":[
    {
        "name": "CopyFromTeradata",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Teradata input dataset name>",
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
                "type": "TeradataSource",
                "query": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="parallel-copy-from-teradata"></a>Párhuzamos másolás a Teradata

A Data Factory Teradata-összekötő beépített adatparticionálást biztosít az adatok párhuzamos Teradata való másolásához. Az adatparticionálási beállítások a másolási tevékenység **forrástábla** tábláján találhatók.

![Képernyőfelvétel a partíciós beállításokról](./media/connector-teradata/connector-teradata-partition-options.png)

A particionált másolás engedélyezésekor a Data Factory párhuzamos lekérdezéseket futtat a Teradata-forráson az adatpartíciók betöltéséhez. A párhuzamos mértéket a [`parallelCopies`](copy-activity-performance-features.md#parallel-copy) másolási tevékenység beállításai vezérlik. Ha például négy értékre van állítva `parallelCopies` , Data Factory egyidejűleg létrehoz és futtat négy lekérdezést a megadott partíciós beállítás és beállítások alapján, és mindegyik lekérdezés a Teradata származó adatok egy részét kéri le.

Javasoljuk, hogy engedélyezze a párhuzamos másolást az adatok particionálásával, különösen akkor, ha nagy mennyiségű adatmennyiséget tölt be a Teradata. Az alábbiakban a különböző forgatókönyvekhez javasolt konfigurációk szerepelnek. Az adatok file-alapú adattárba való másolása során a rendszer úgy helyezi át, hogy több fájlként írjon egy mappába (csak a mappa nevét adja meg), amely esetben a teljesítmény jobb, mint egyetlen fájl írásakor.

| Eset                                                     | Javasolt beállítások                                           |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| Teljes terhelés a nagyméretű táblából.                                   | **Partíciós beállítás**: kivonat. <br><br/>A végrehajtás során a Data Factory automatikusan észleli az elsődleges index oszlopot, alkalmaz egy kivonatot, és az adatokat partíciók szerint másolja. |
| Nagy mennyiségű adattal tölthetők be egyéni lekérdezések használatával.                 | **Partíciós beállítás**: kivonat.<br>**Lekérdezés**: `SELECT * FROM <TABLENAME> WHERE ?AdfHashPartitionCondition AND <your_additional_where_clause>` .<br>**Partíciós oszlop**: a kivonatoló partíció alkalmazásához használt oszlop megadása. Ha nincs megadva, a Data Factory automatikusan észleli a Teradata adatkészletben megadott tábla PK oszlopát.<br><br>A végrehajtás során Data Factory helyettesíti `?AdfHashPartitionCondition` a kivonatoló partíció logikáját, és elküldi a Teradata. |
| Nagy mennyiségű adattal tölthetők be egy egyéni lekérdezéssel, amely egy egész számú oszlopból álló, egyenletesen elosztott értékkel rendelkezik a tartomány particionálásához. | **Partíciós beállítások**: dinamikus tartományú partíció.<br>**Lekérdezés**: `SELECT * FROM <TABLENAME> WHERE ?AdfRangePartitionColumnName <= ?AdfRangePartitionUpbound AND ?AdfRangePartitionColumnName >= ?AdfRangePartitionLowbound AND <your_additional_where_clause>` .<br>**Partíciós oszlop**: az adatparticionáláshoz használt oszlop megadására szolgál. Az oszlop egész adattípussal is particionálható.<br>**Partíció felső határa** és a **partíció alsó határa**: adja meg, hogy a partíció oszlop alapján szeretne-e szűrést végezni, hogy csak az alsó és a felső tartomány között legyenek lekérdezve.<br><br>A végrehajtás során a Data Factory lecseréli, `?AdfRangePartitionColumnName` `?AdfRangePartitionUpbound` és az `?AdfRangePartitionLowbound` egyes partíciók tényleges oszlop-és értéktartomány-tartományát, és elküldi a Teradata. <br>Ha például az "ID" partíciós oszlop az 1 értékre van állítva, a felső határ pedig 80, a párhuzamos másolási beállítás értéke 4, Data Factory 4 partíció alapján kérdezi le az adatmennyiséget. Az azonosítóik [1, 20], [21, 40], [41, 60] és [61, 80] között vannak. |

**Példa: lekérdezés kivonatoló partícióval**

```json
"source": {
    "type": "TeradataSource",
    "query": "SELECT * FROM <TABLENAME> WHERE ?AdfHashPartitionCondition AND <your_additional_where_clause>",
    "partitionOption": "Hash",
    "partitionSettings": {
        "partitionColumnName": "<hash_partition_column_name>"
    }
}
```

**Példa: lekérdezés dinamikus tartományú partícióval**

```json
"source": {
    "type": "TeradataSource",
    "query": "SELECT * FROM <TABLENAME> WHERE ?AdfRangePartitionColumnName <= ?AdfRangePartitionUpbound AND ?AdfRangePartitionColumnName >= ?AdfRangePartitionLowbound AND <your_additional_where_clause>",
    "partitionOption": "DynamicRange",
    "partitionSettings": {
        "partitionColumnName": "<dynamic_range_partition_column_name>",
        "partitionUpperBound": "<upper_value_of_partition_column>",
        "partitionLowerBound": "<lower_value_of_partition_column>"
    }
}
```

## <a name="data-type-mapping-for-teradata"></a>Adattípusok leképezése Teradata

Az adatok Teradata történő másolásakor a következő leképezések érvényesek. Ha szeretné megtudni, hogyan képezi le a másolási tevékenység a forrás sémát és az adattípust a fogadónak, tekintse meg a [séma-és adattípus-leképezéseket](copy-activity-schema-and-type-mapping.md).

| Teradata adattípusa | Data Factory időközi adattípus |
|:--- |:--- |
| BigInt |Int64 |
| Blob |Bájt [] |
| Bájt |Bájt [] |
| ByteInt |Int16 |
| Char |Sztring |
| CLOB |Sztring |
| Date |DateTime |
| Tizedesjegy |Tizedesjegy |
| Dupla |Dupla |
| Grafikus |Nem támogatott. Explicit szereposztás alkalmazása a forrás lekérdezésben. |
| Egész szám |Int32 |
| Intervallum napja |Nem támogatott. Explicit szereposztás alkalmazása a forrás lekérdezésben. |
| Nap és óra közötti időszak |Nem támogatott. Explicit szereposztás alkalmazása a forrás lekérdezésben. |
| Időköz (nap és perc) |Nem támogatott. Explicit szereposztás alkalmazása a forrás lekérdezésben. |
| Időintervallum – másodperc |Nem támogatott. Explicit szereposztás alkalmazása a forrás lekérdezésben. |
| Intervallum óra |Nem támogatott. Explicit szereposztás alkalmazása a forrás lekérdezésben. |
| Óra és perc közötti intervallum |Nem támogatott. Explicit szereposztás alkalmazása a forrás lekérdezésben. |
| Óra és másodperc közötti intervallum |Nem támogatott. Explicit szereposztás alkalmazása a forrás lekérdezésben. |
| Időköz (perc) |Nem támogatott. Explicit szereposztás alkalmazása a forrás lekérdezésben. |
| Időköz (perc) – másodperc |Nem támogatott. Explicit szereposztás alkalmazása a forrás lekérdezésben. |
| Intervallum hónapja |Nem támogatott. Explicit szereposztás alkalmazása a forrás lekérdezésben. |
| Másodperc intervalluma |Nem támogatott. Explicit szereposztás alkalmazása a forrás lekérdezésben. |
| Intervallum éve |Nem támogatott. Explicit szereposztás alkalmazása a forrás lekérdezésben. |
| Év és hónap közötti időszak |Nem támogatott. Explicit szereposztás alkalmazása a forrás lekérdezésben. |
| Szám |Dupla |
| Időszak (dátum) |Nem támogatott. Explicit szereposztás alkalmazása a forrás lekérdezésben. |
| Időszak (idő) |Nem támogatott. Explicit szereposztás alkalmazása a forrás lekérdezésben. |
| Időtartam (idő zónával) |Nem támogatott. Explicit szereposztás alkalmazása a forrás lekérdezésben. |
| Időszak (timestamp) |Nem támogatott. Explicit szereposztás alkalmazása a forrás lekérdezésben. |
| Időtartam (időbélyegző az időzónával) |Nem támogatott. Explicit szereposztás alkalmazása a forrás lekérdezésben. |
| SmallInt |Int16 |
| Idő |időtartam |
| Időzónával ellátott idő |időtartam |
| Időbélyeg |DateTime |
| Időbélyeg időzónával |DateTime |
| VarByte |Bájt [] |
| VarChar |Sztring |
| VarGraphic |Nem támogatott. Explicit szereposztás alkalmazása a forrás lekérdezésben. |
| Xml |Nem támogatott. Explicit szereposztás alkalmazása a forrás lekérdezésben. |


## <a name="lookup-activity-properties"></a>Keresési tevékenység tulajdonságai

A tulajdonságok részleteinek megismeréséhez tekintse meg a [keresési tevékenységet](control-flow-lookup-activity.md).


## <a name="next-steps"></a>Következő lépések
A Data Factory a másolási tevékenység által forrásként és nyelőként támogatott adattárak listáját lásd: [támogatott adattárak](copy-activity-overview.md#supported-data-stores-and-formats).
