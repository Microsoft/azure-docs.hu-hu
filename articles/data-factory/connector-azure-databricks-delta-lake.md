---
title: Adatok másolása Azure Databricks Delta-tóhoz és onnan
description: Megtudhatja, hogyan másolhat adatok Azure Databricks Delta Lake-be és onnan a Azure Data Factory folyamat másolási tevékenységének használatával.
services: data-factory
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 11/24/2020
ms.openlocfilehash: e32b93c669bffd382b1eb648111f9b8931b07eac
ms.sourcegitcommit: 54e1d4cdff28c2fd88eca949c2190da1b09dca91
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/31/2021
ms.locfileid: "99221142"
---
# <a name="copy-data-to-and-from-azure-databricks-delta-lake-by-using-azure-data-factory"></a>Adatok másolása Azure Databricks Delta-tóból és onnan a Azure Data Factory használatával

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Ez a cikk azt ismerteti, hogyan használható a másolási tevékenység a Azure Data Factoryban az adatok másolásához Azure Databricks Delta-tóba. A [másolási tevékenységre épül Azure Data Factory](copy-activity-overview.md) cikkben, amely a másolási tevékenység általános áttekintését jeleníti meg.

## <a name="supported-capabilities"></a>Támogatott képességek

A Azure Databricks Delta Lake Connector a következő tevékenységek esetén támogatott:

- [Másolási tevékenység](copy-activity-overview.md) [támogatott forrás/fogadó mátrix-](copy-activity-overview.md) táblázattal
- [Keresési tevékenység](control-flow-lookup-activity.md)

Általánosságban Azure Data Factory a Delta Lake-t a következő képességekkel támogatja, hogy megfeleljenek a különböző igényeinek.

- A másolási tevékenység támogatja a Azure Databricks Delta Lake Connector számára az adatok másolását bármely támogatott forrás adattárból a Azure Databricks Delta Lake Table és a Delta Lake Table bármely támogatott fogadó adattárba. Kihasználja a Databricks-fürtöt az adatok áthelyezésének elvégzéséhez, lásd az [Előfeltételek című szakaszban](#prerequisites)található részleteket.
- A [leképezési folyamat](concepts-data-flow-overview.md) támogatja az Azure Storage-ban a forrásként és a fogadóként használható általános [különbözeti formátumot](format-delta.md) , amely a kód nélküli ETL-re vonatkozó különbözeti fájlok olvasását és írását, valamint a felügyelt Azure Integration Runtime
- A Databricks-tevékenységek támogatják a kód-központú ETL-vagy gépi tanulási számítási feladatok [előkészítését](transform-data-databricks-notebook.md) a Delta-tavon.

## <a name="prerequisites"></a>Előfeltételek

A Azure Databricks Delta Lake Connector használatához létre kell hoznia egy fürtöt a Azure Databricksban.

- Az adatoknak a Delta Lake-be való másolásához a másolási tevékenység meghívja Azure Databricks-fürtöt az Azure Storage-ból származó adatok beolvasásához, amely az eredeti forrás vagy egy átmeneti terület, ahol a Data Factory először a beépített szakaszos másolással írja be a forrásadatokat. További információ a [Delta Lake](#delta-lake-as-sink)-ről fogadóként.
- Hasonlóképpen, az adatoknak a Delta Lake-ből való másolásához a másolási tevékenység meghívja Azure Databricks-fürtöt az adatok Azure Storage-ba való írásához, amely az eredeti fogadó, vagy egy átmeneti terület, ahol a Data Factory továbbra is az adatok a végső fogadóba való írását a beépített előkészített példány használatával. További információ a [Delta Lake forrásról](#delta-lake-as-source).

A Databricks-fürtnek hozzáféréssel kell rendelkeznie az Azure Blobhoz vagy Azure Data Lake Storage Gen2 fiókhoz, valamint a forrás/fogadó/előkészítéshez használt tároló-vagy fájlrendszerhez, valamint a Container/file rendszerhez, ahol a Delta Lake-táblákat szeretné írni.

- **Azure Data Lake Storage Gen2** használatához a Apache Spark konfigurációjának részeként konfigurálhat egy **egyszerű szolgáltatásnevet** vagy egy **Storage-fiók hozzáférési kulcsát** a Databricks-fürtön. Kövesse a [közvetlen hozzáférés az egyszerű szolgáltatással](/azure/databricks/data/data-sources/azure/azure-datalake-gen2#--access-directly-with-service-principal-and-oauth-20) vagy [a közvetlen hozzáférés a Storage-fiók elérési kulcsa használatával](/azure/databricks/data/data-sources/azure/azure-datalake-gen2#--access-directly-using-the-storage-account-access-key)című témakör lépéseit.

- Az **Azure Blob Storage** használatához a Apache Spark konfigurációjának részeként beállíthat egy **Storage-fiók hozzáférési kulcsát** vagy egy **sas-tokent** a Databricks-fürtön. Kövesse az [Azure Blob Storage elérésének lépései a RDD API használatával](/azure/databricks/data/data-sources/azure/azure-storage#access-azure-blob-storage-using-the-rdd-api)című témakör lépéseit.

A másolási tevékenység végrehajtása során, ha a konfigurált fürt meg lett szakítva, Data Factory automatikusan elindul. Ha Data Factory szerzői felhasználói felülettel készíti el a folyamatot, az olyan műveletek esetében, mint az adatelőnézet, élő fürtöt kell használnia, Data Factory nem indítja el a fürtöt az Ön nevében.

#### <a name="specify-the-cluster-configuration"></a>A fürtkonfiguráció beállítása

1. A **fürt mód** legördülő menüben válassza a **standard** lehetőséget.

2. A **Databricks Runtime verziója** legördülő menüben válasszon ki egy Databricks Runtime-verziót.

3. Az [automatikus optimalizálás](/azure/databricks/delta/optimizations/auto-optimize) bekapcsolásához adja hozzá a következő tulajdonságokat a [Spark-konfigurációhoz](/azure/databricks/clusters/configure#spark-config):

   ```
   spark.databricks.delta.optimizeWrite.enabled true
   spark.databricks.delta.autoCompact.enabled true
   ```

4. Konfigurálja a fürtöt az integrációs és a skálázási igényektől függően.

A fürt konfigurációjának részleteiért lásd: [fürtök konfigurálása](/azure/databricks/clusters/configure).

## <a name="get-started"></a>Bevezetés

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

A következő szakaszokban részletesen ismertetjük azokat a tulajdonságokat, amelyek a Azure Databricks Delta Lake-összekötőhöz tartozó Data Factory entitásokat határozzák meg.

## <a name="linked-service-properties"></a>Társított szolgáltatás tulajdonságai

A következő tulajdonságokat támogatja egy Azure Databricks Delta Lake társított szolgáltatás.

| Tulajdonság    | Leírás                                                  | Kötelező |
| :---------- | :----------------------------------------------------------- | :------- |
| típus        | A Type tulajdonságot **AzureDatabricksDeltaLake** értékre kell beállítani. | Yes      |
| domain      | Itt adhatja meg a Azure Databricks munkaterület URL-címét, például: `https://adb-xxxxxxxxx.xx.azuredatabricks.net` . |          |
| clusterId   | Egy meglévő fürthöz tartozó fürt AZONOSÍTÓjának megadására. Egy már létrehozott interaktív fürtnek kell lennie. <br>Az interaktív fürt Databricks munkaterületen található – > fürtök – > interaktív fürt neve – > Configuration-> címkék. [További információ](/azure/databricks/clusters/configure#cluster-tags). |          |
| accessToken | A hozzáférési token szükséges ahhoz, hogy a Data Factory hitelesíthető legyen a Azure Databricks. A hozzáférési tokent a databricks munkaterületen kell létrehozni. A hozzáférési token megkeresésének részletes lépései [itt](/azure/databricks/dev-tools/api/latest/authentication#generate-token)találhatók. |          |
| Connectvia tulajdonsággal  | Az adattárhoz való kapcsolódáshoz használt [integrációs](concepts-integration-runtime.md) modul. Használhatja az Azure Integration Runtime vagy egy saját üzemeltetésű integrációs modult (ha az adattár egy magánhálózaton található). Ha nincs megadva, az alapértelmezett Azure Integration Runtime-t használja. | No       |

**Példa**

```json
{
    "name": "AzureDatabricksDeltaLakeLinkedService",
    "properties": {
        "type": "AzureDatabricksDeltaLake",
        "typeProperties": {
            "domain": "https://adb-xxxxxxxxx.xx.azuredatabricks.net",
            "clusterId": "<cluster id>",
            "accessToken": {
                "type": "SecureString", 
                "value": "<access token>"
            }
        }
    }
}
```

## <a name="dataset-properties"></a>Adatkészlet tulajdonságai

Az adatkészletek definiálásához rendelkezésre álló csoportok és tulajdonságok teljes listáját az [adatkészletek](concepts-datasets-linked-services.md) című cikkben találja. 

A Azure Databricks Delta Lake adatkészlet a következő tulajdonságokat támogatja.

| Tulajdonság  | Leírás                                                  | Kötelező                    |
| :-------- | :----------------------------------------------------------- | :-------------------------- |
| típus      | Az adatkészlet Type tulajdonságát **AzureDatabricksDeltaLakeDataset** értékre kell állítani. | Yes                         |
| adatbázis | Az adatbázis neve. |Nem, forrás, igen, fogadó  |
| tábla | A különbözeti tábla neve. |Nem, forrás, igen, fogadó  |

**Példa**

```json
{
    "name": "AzureDatabricksDeltaLakeDataset",
    "properties": {
        "type": "AzureDatabricksDeltaLakeDataset",
        "typeProperties": {
            "database": "<database name>",
            "table": "<delta table name>"
        },
        "schema": [ < physical schema, optional, retrievable during authoring > ],
        "linkedServiceName": {
            "referenceName": "<name of linked service>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Másolási tevékenység tulajdonságai

A tevékenységek definiálásához elérhető csoportok és tulajdonságok teljes listáját a [folyamatok](concepts-pipelines-activities.md) című cikkben találja. Ez a szakasz a Azure Databricks Delta Lake forrás és a fogadó által támogatott tulajdonságok listáját tartalmazza.

### <a name="delta-lake-as-source"></a>Delta Lake forrásként

Azure Databricks Delta-tótól származó adatok másolásához a másolási tevékenység **forrása** szakaszban a következő tulajdonságok támogatottak.

| Tulajdonság                     | Leírás                                                  | Kötelező |
| :--------------------------- | :----------------------------------------------------------- | :------- |
| típus                         | A másolási tevékenység forrásának Type tulajdonságát **AzureDatabricksDeltaLakeSource** értékre kell állítani. | Yes      |
| lekérdezés          | Az adatolvasásra szolgáló SQL-lekérdezés meghatározása. Az időutazások vezérléséhez kövesse az alábbi mintát:<br>- `SELECT * FROM events TIMESTAMP AS OF timestamp_expression`<br>- `SELECT * FROM events VERSION AS OF version` | No       |
| exportSettings | Az adatok különbözeti táblából való beolvasásához használt speciális beállítások. | No       |
| ***Alatt `exportSettings` :** _ |  |  |
| típus | Az exportálási parancs típusa: _ * AzureDatabricksDeltaLakeExportCommand * *. | Yes |
| dateFormat | Dátum típusának formázása dátum formátumú karakterláncra. Az egyéni dátumformátum formátuma a következő: [datetime minta](https://spark.apache.org/docs/latest/sql-ref-datetime-pattern.html). Ha nincs megadva, az alapértelmezett értéket használja `yyyy-MM-dd` . | No |
| timestampFormat | Az időbélyeg típusának formázása időbélyeg-formátumú karakterláncra. Az egyéni dátumformátum formátuma a következő: [datetime minta](https://spark.apache.org/docs/latest/sql-ref-datetime-pattern.html). Ha nincs megadva, az alapértelmezett értéket használja `yyyy-MM-dd'T'HH:mm:ss[.SSS][XXX]` . | No |

#### <a name="direct-copy-from-delta-lake"></a>Közvetlen másolás a Delta-tótól

Ha a fogadó adattár és a formátum megfelel az ebben a szakaszban ismertetett feltételeknek, a másolási tevékenység használatával közvetlenül másolhat Azure Databricks különbözeti táblából a fogadóba. Data Factory ellenőrzi a beállításokat, és sikertelenül futtatja a másolási tevékenységet, ha a következő feltételek nem teljesülnek:

- A fogadó **társított szolgáltatás** az [Azure Blob Storage](connector-azure-blob-storage.md) vagy [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md). A fiók hitelesítő adatait előre be kell állítani Azure Databricks fürtkonfiguráció, további információ az [előfeltételekről](#prerequisites).

- A fogadó **adatformátuma** a következő konfigurációkból álló **parketta**, **tagolt szöveg** vagy **Avro** , és a fájl helyett egy mappára mutat.

    - A **parketta** formátuma esetén a tömörítési kodek **egyike sem**, a **Snappy** vagy a **gzip**.
    - **Tagolt szöveges** formátum esetén:
        - `rowDelimiter` egyetlen karakter.
        - `compression` a következők **egyike** lehet: none, **bzip2**, **gzip**.
        - `encodingName` Az UTF-7 nem támogatott.
    - **Avro** formátum esetén a tömörítési kodek **egyike sem**, a **deflate** vagy a **Snappy**.

- A másolási tevékenység forrásában nincs `additionalColumns` megadva.
- Ha az adatok tagolt szöveggé való másolása, a másolási tevékenység fogadójában a `fileExtension` ". csv" értéknek kell lennie.
- A másolási tevékenység leképezésében nincs engedélyezve a konverzió típusa.

**Példa**

```json
"activities":[
    {
        "name": "CopyFromDeltaLake",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Delta lake input dataset name>",
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
                "type": "AzureDatabricksDeltaLakeSource",
                "sqlReaderQuery": "SELECT * FROM events TIMESTAMP AS OF timestamp_expression"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

#### <a name="staged-copy-from-delta-lake"></a>Szakaszos másolás a Delta-tótól

Ha a fogadó adattára vagy formátuma nem felel meg a közvetlen másolás feltételeinek, ahogy azt az utolsó szakaszban is említettük, engedélyezze a beépített szakaszos másolatot egy ideiglenes Azure Storage-példány használatával. Az előkészített másolási funkció jobb átviteli sebességet is biztosít. Data Factory exportálja a Azure Databricks Delta-tóból származó adatok átmeneti tárterületre való exportálását, majd átmásolja az adatait a fogadóba, és végül törli az ideiglenes adatait az átmeneti tárolóból. Az adatok átmeneti használatával történő másolásával kapcsolatos részletekért tekintse meg a [Lépcsőzetes másolás című szakaszt](copy-activity-performance-features.md#staged-copy) .

A szolgáltatás használatához hozzon létre egy [Azure Blob Storage-beli társított szolgáltatást](connector-azure-blob-storage.md#linked-service-properties) vagy [Azure Data Lake Storage Gen2 társított szolgáltatást](connector-azure-data-lake-storage.md#linked-service-properties) , amely a Storage-fiókra hivatkozik átmeneti előkészítésként. Ezután adja meg a `enableStaging` és a `stagingSettings` tulajdonságokat a másolási tevékenységben.

>[!NOTE]
>Az átmeneti tárolási fiók hitelesítő adatait előre be kell állítani Azure Databricks fürtkonfiguráció, további információ az [előfeltételekről](#prerequisites).

**Példa**

```json
"activities":[
    {
        "name": "CopyFromDeltaLake",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Delta lake input dataset name>",
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
                "type": "AzureDatabricksDeltaLakeSource",
                "sqlReaderQuery": "SELECT * FROM events TIMESTAMP AS OF timestamp_expression"
            },
            "sink": {
                "type": "<sink type>"
            },
            "enableStaging": true,
            "stagingSettings": {
                "linkedServiceName": {
                    "referenceName": "MyStagingStorage",
                    "type": "LinkedServiceReference"
                },
                "path": "mystagingpath"
            }
        }
    }
]
```

### <a name="delta-lake-as-sink"></a>Delta Lake as fogadó

Ha az Adatmásolást Azure Databricks Delta-tóba szeretné másolni, a másolási **tevékenység** fogadója szakaszban a következő tulajdonságok támogatottak.

| Tulajdonság      | Leírás                                                  | Kötelező |
| :------------ | :----------------------------------------------------------- | :------- |
| típus          | A másolási tevékenység fogadójának Type tulajdonsága **AzureDatabricksDeltaLakeSink** értékre van állítva. | Yes      |
| preCopyScript | Adja meg a másolási tevékenység futtatásához szükséges SQL-lekérdezést, mielőtt az összes futtatás során Databricks-különbözeti táblába írna. Ezt a tulajdonságot használhatja az előre betöltött adatok törléséhez, illetve egy csonkolt tábla vagy vákuum-utasítás hozzáadásához. | No       |
| importSettings | Az adatkülönbözeti táblába való adatíráshoz használt speciális beállítások. | No |
| **_Alatt `importSettings` :_* _ |                                                              |  |
| típus | Az importálási parancs típusa: _ * AzureDatabricksDeltaLakeImportCommand * *. | Yes |
| dateFormat | Formázza a karakterláncot dátum típusúra dátumformátum formájában. Az egyéni dátumformátum formátuma a következő: [datetime minta](https://spark.apache.org/docs/latest/sql-ref-datetime-pattern.html). Ha nincs megadva, az alapértelmezett értéket használja `yyyy-MM-dd` . | No |
| timestampFormat | Formázza a karakterláncot timestamp típusúra időbélyeg-formátummal. Az egyéni dátumformátum formátuma a következő: [datetime minta](https://spark.apache.org/docs/latest/sql-ref-datetime-pattern.html). Ha nincs megadva, az alapértelmezett értéket használja `yyyy-MM-dd'T'HH:mm:ss[.SSS][XXX]` . | No |

#### <a name="direct-copy-to-delta-lake"></a>Közvetlen másolás a Delta Lake-be

Ha a forrás-adattár és-formátum megfelel az ebben a szakaszban ismertetett feltételeknek, a másolási tevékenység használatával közvetlenül másolhatja a forrásról Azure Databricks különbözeti tóhoz. Azure Data Factory ellenőrzi a beállításokat, és sikertelenül futtatja a másolási tevékenységet, ha a következő feltételek nem teljesülnek:

- A **forrás társított szolgáltatás** az [Azure Blob storage](connector-azure-blob-storage.md) vagy a [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md). A fiók hitelesítő adatait előre be kell állítani Azure Databricks fürtkonfiguráció, további információ az [előfeltételekről](#prerequisites).

- A **forrás adatformátuma** a következő konfigurációkból álló **parketta**, **tagolt szöveg** vagy **Avro** , és a fájl helyett egy mappára mutat.

    - A **parketta** formátuma esetén a tömörítési kodek **egyike sem**, a **Snappy** vagy a **gzip**.
    - **Tagolt szöveges** formátum esetén:
        - `rowDelimiter` alapértelmezett vagy egyetlen karakter.
        - `compression` a következők **egyike** lehet: none, **bzip2**, **gzip**.
        - `encodingName` Az UTF-7 nem támogatott.
    - **Avro** formátum esetén a tömörítési kodek **egyike sem**, a **deflate** vagy a **Snappy**.

- A másolási tevékenység forrása: 

    - `wildcardFileName` csak helyettesítő karaktereket tartalmaz `*` `?` , és nincs `wildcardFolderName` megadva.
    - `prefix`,,, `modifiedDateTimeStart` `modifiedDateTimeEnd` és `enablePartitionDiscovery` nincsenek megadva.
    - `additionalColumns` nincs megadva.

- A másolási tevékenység leképezésében nincs engedélyezve a konverzió típusa.

**Példa**

```json
"activities":[
    {
        "name": "CopyToDeltaLake",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Delta lake output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "AzureDatabricksDeltaLakeSink"
            }
        }
    }
]
```

#### <a name="staged-copy-to-delta-lake"></a>Szakaszos másolás a Delta Lake-be

Ha a forrás adattára vagy formátuma nem felel meg a közvetlen másolás feltételeinek, ahogy azt az utolsó szakaszban is említettük, engedélyezze a beépített szakaszos másolatot egy ideiglenes Azure Storage-példány használatával. Az előkészített másolási funkció jobb átviteli sebességet is biztosít. A Data Factory automatikusan átalakítja az adatoknak az adatformátumra vonatkozó követelményeket az átmeneti tárolóba, majd onnan betölti az adatok a Delta Lake-be. Végezetül törli az ideiglenes adatait a tárolóból. Az adatok átmeneti használatával történő másolásával kapcsolatos részletekért tekintse meg a [Lépcsőzetes másolás című szakaszt](copy-activity-performance-features.md#staged-copy) .

A szolgáltatás használatához hozzon létre egy [Azure Blob Storage-beli társított szolgáltatást](connector-azure-blob-storage.md#linked-service-properties) vagy [Azure Data Lake Storage Gen2 társított szolgáltatást](connector-azure-data-lake-storage.md#linked-service-properties) , amely a Storage-fiókra hivatkozik átmeneti előkészítésként. Ezután adja meg a `enableStaging` és a `stagingSettings` tulajdonságokat a másolási tevékenységben.

>[!NOTE]
>Az átmeneti tárolási fiók hitelesítő adatait előre be kell állítani Azure Databricks fürtkonfiguráció, további információ az [előfeltételekről](#prerequisites).

**Példa**

```json
"activities":[
    {
        "name": "CopyToDeltaLake",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Delta lake output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "AzureDatabricksDeltaLakeSink"
            },
            "enableStaging": true,
            "stagingSettings": {
                "linkedServiceName": {
                    "referenceName": "MyStagingBlob",
                    "type": "LinkedServiceReference"
                },
                "path": "mystagingpath"
            }
        }
    }
]
```

## <a name="monitoring"></a>Figyelés

Azure Data Factory a [másolási tevékenységek figyelési élményét](copy-activity-monitoring.md) is biztosítja más összekötők számára. Emellett, mivel a (z) és a különbözeti tóból való betöltése a Azure Databricks-fürtön fut, továbbra is [megtekintheti a fürtök részletes naplóit](/azure/databricks/clusters/clusters-manage#--view-cluster-logs) , és [figyelheti a teljesítményt](/azure/databricks/clusters/clusters-manage#--monitor-performance).

## <a name="lookup-activity-properties"></a>Keresési tevékenység tulajdonságai

További információ a tulajdonságokról: [keresési tevékenység](control-flow-lookup-activity.md).

## <a name="next-steps"></a>Következő lépések

A forrásként és fogadóként támogatott adattárak listáját a Data Factory másolási tevékenysége című részben tekintheti meg a [támogatott adattárak és-formátumok](copy-activity-overview.md#supported-data-stores-and-formats)című témakörben.
