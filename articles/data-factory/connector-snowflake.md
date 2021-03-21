---
title: Az Adatmásolás és-átalakítás a hópehely-ban
description: Megtudhatja, hogyan másolhatja és alakíthatja át a hópehely-ban lévő adatfájlokat Data Factory használatával.
ms.author: jingwang
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 03/16/2021
ms.openlocfilehash: c18a48f8e72c28fd39f839566b18528806e7245d
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "103561583"
---
# <a name="copy-and-transform-data-in-snowflake-by-using-azure-data-factory"></a>A hópehely adatmásolási és-átalakítási szolgáltatásának Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Ez a cikk azt ismerteti, hogyan használható a másolási tevékenység a Azure Data Factoryban az adatok hópehely-ba való másolásához és az adatok a hópehely használatával történő átalakításához. A Data Factoryról a [bevezető cikkben](introduction.md)talál további információt.

## <a name="supported-capabilities"></a>Támogatott képességek

Ez a hópehely összekötő a következő tevékenységek esetében támogatott:

- [Másolási tevékenység](copy-activity-overview.md) [támogatott forrás/fogadó mátrix-](copy-activity-overview.md) táblázattal
- [Adatfolyam hozzárendelése](concepts-data-flow-overview.md)
- [Keresési tevékenység](control-flow-lookup-activity.md)

A másolási tevékenységhez ez a hópehely összekötő a következő függvényeket támogatja:

- Adatok másolása a hópehely-ből, amely a hópehely [másolatát használja a [Location]](https://docs.snowflake.com/en/sql-reference/sql/copy-into-location.html) paranccsal a legjobb teljesítmény eléréséhez.
- Másolja át az adatmásolt hópehely-ba, amely kihasználja a hópehely [másolását a [table]](https://docs.snowflake.com/en/sql-reference/sql/copy-into-table.html) paranccsal a legjobb teljesítmény elérése érdekében. Támogatja a hópehely-t az Azure-ban. 

## <a name="get-started"></a>Bevezetés

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

A következő szakaszokban részletesen ismertetjük azokat a tulajdonságokat, amelyek a hópehely-összekötőhöz tartozó Data Factory entitásokat határozzák meg.

## <a name="linked-service-properties"></a>Társított szolgáltatás tulajdonságai

A hópehely-társított szolgáltatások esetében a következő tulajdonságok támogatottak.

| Tulajdonság         | Leírás                                                  | Kötelező |
| :--------------- | :----------------------------------------------------------- | :------- |
| típus             | A Type tulajdonságot a **hópehely** értékre kell beállítani.              | Yes      |
| connectionString | Megadja a hópehely-példányhoz való kapcsolódáshoz szükséges adatokat. Megadhatja, hogy a jelszó vagy a teljes kapcsolódási karakterlánc legyen Azure Key Vaultban. További részletekért tekintse meg a táblázat alatti példákat, valamint a [Azure Key Vault cikkben tárolt hitelesítő adatokat](store-credentials-in-key-vault.md) .<br><br>Néhány tipikus beállítás:<br>- **Fiók neve:** A hópehely-fiók  [teljes fiókjának neve](https://docs.snowflake.net/manuals/user-guide/connecting.html#your-snowflake-account-name) (beleértve a régiót és a felhőalapú platformot azonosító további szegmenseket), például: xy12345. East-US-2. Azure.<br/>- **Felhasználónév:** A felhasználó bejelentkezési neve a kapcsolatban.<br>- **Jelszó:** A felhasználó jelszava.<br>- **Adatbázis:** Az alapértelmezett adatbázis, amelyet a csatlakozás után használ. Olyan meglévő adatbázisnak kell lennie, amelyhez a megadott szerepkör jogosultságokkal rendelkezik.<br>- **Raktár:** A virtuális raktár, amelyet a csatlakozás után használ. Egy meglévő raktárnak kell lennie, amelyhez a megadott szerepkör jogosultságokkal rendelkezik.<br>- **Szerepkör:** A hópehely-munkamenetben használni kívánt alapértelmezett hozzáférés-vezérlési szerepkör. A megadott szerepkörnek olyan meglévő szerepkörnek kell lennie, amely már hozzá van rendelve a megadott felhasználóhoz. Az alapértelmezett szerepkör nyilvános. | Yes      |
| Connectvia tulajdonsággal       | Az adattárhoz való kapcsolódáshoz használt [integrációs](concepts-integration-runtime.md) modul. Használhatja az Azure Integration Runtime vagy egy saját üzemeltetésű integrációs modult (ha az adattár egy magánhálózaton található). Ha nincs megadva, az alapértelmezett Azure Integration Runtime-t használja. | No       |

**Példa**

```json
{
    "name": "SnowflakeLinkedService",
    "properties": {
        "type": "Snowflake",
        "typeProperties": {
            "connectionString": "jdbc:snowflake://<accountname>.snowflakecomputing.com/?user=<username>&password=<password>&db=<database>&warehouse=<warehouse>&role=<myRole>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Jelszó Azure Key Vaultban:**

```json
{
    "name": "SnowflakeLinkedService",
    "properties": {
        "type": "Snowflake",
        "typeProperties": {
            "connectionString": "jdbc:snowflake://<accountname>.snowflakecomputing.com/?user=<username>&db=<database>&warehouse=<warehouse>&role=<myRole>",
            "password": {
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

Az adatkészletek definiálásához rendelkezésre álló csoportok és tulajdonságok teljes listáját az [adatkészletek](concepts-datasets-linked-services.md) című cikkben találja. 

A hópehely-adatkészlet a következő tulajdonságokat támogatja.

| Tulajdonság  | Leírás                                                  | Kötelező                    |
| :-------- | :----------------------------------------------------------- | :-------------------------- |
| típus      | Az adatkészlet Type tulajdonságát **SnowflakeTable** értékre kell állítani. | Yes                         |
| schema | A séma neve. Megjegyzés: a séma neve a kis-és nagybetűk megkülönböztetése az ADF-ben. |Nem, forrás, igen, fogadó  |
| tábla | A tábla vagy nézet neve. Megjegyzés: a tábla neve megkülönbözteti a kis-és nagybetűket az ADF-ben. |Nem, forrás, igen, fogadó  |

**Példa**

```json
{
    "name": "SnowflakeDataset",
    "properties": {
        "type": "SnowflakeTable",
        "typeProperties": {
            "schema": "<Schema name for your Snowflake database>",
            "table": "<Table name for your Snowflake database>"
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

A tevékenységek definiálásához elérhető csoportok és tulajdonságok teljes listáját a [folyamatok](concepts-pipelines-activities.md) című cikkben találja. Ez a szakasz a hópehely forrás és a fogadó által támogatott tulajdonságok listáját tartalmazza.

### <a name="snowflake-as-the-source"></a>A hópehely forrása

A hópehely-összekötő a [[Location]](https://docs.snowflake.com/en/sql-reference/sql/copy-into-location.html) paranccsal a legjobb teljesítmény eléréséhez használja a hópehely-példányt.

Ha a fogadó adattár és a formátum natív módon támogatott a hópehely COPY parancsban, a másolási tevékenység használatával közvetlenül másolhat a hópehely-ből a fogadóba. Részletekért lásd: [közvetlen másolás a hópehely-ból](#direct-copy-from-snowflake). Egyéb esetben a beépített, [szakaszos másolást használja a hópehely-ből](#staged-copy-from-snowflake).

Az adatok a hópehely-ből való másolásához a másolási tevékenység **forrása** szakaszban a következő tulajdonságok támogatottak.

| Tulajdonság                     | Leírás                                                  | Kötelező |
| :--------------------------- | :----------------------------------------------------------- | :------- |
| típus                         | A másolási tevékenység forrásának Type tulajdonságát **SnowflakeSource** értékre kell állítani. | Yes      |
| lekérdezés          | Meghatározza azt az SQL-lekérdezést, amely a hópehely adatok olvasására szolgál. Ha a séma, a tábla és az oszlopok neve kisbetűt tartalmaz, adja meg az objektumazonosítót a lekérdezésben, például: `select * from "schema"."myTable"` .<br>A tárolt eljárás végrehajtása nem támogatott. | No       |
| exportSettings | A hópehely adatok lekérésére használt speciális beállítások. A COPY into parancs által támogatott beállításokat is konfigurálhatja, hogy a Data Factory áthaladjon az utasítás meghívásakor. | No       |
| ***Alatt `exportSettings` :*** |  |  |
| típus | Az exportálási parancs típusa **SnowflakeExportCopyCommand** értékre van állítva. | Yes |
| additionalCopyOptions | További másolási lehetőségek a kulcs-érték párok szótáraként megadva. Példák: MAX_FILE_SIZE, FELÜLÍRÁS. További információ: a [hópehely másolási beállításai](https://docs.snowflake.com/en/sql-reference/sql/copy-into-location.html#copy-options-copyoptions). | No |
| additionalFormatOptions | További fájlformátum-beállítások, amelyek a parancs a kulcs-érték párok szótáraként való MÁSOLÁSához vannak megadva. Példák: DATE_FORMAT, TIME_FORMAT, TIMESTAMP_FORMAT. További információkért lásd a [hópehely formátum típusának beállításait](https://docs.snowflake.com/en/sql-reference/sql/copy-into-location.html#format-type-options-formattypeoptions). | No |

#### <a name="direct-copy-from-snowflake"></a>Közvetlen másolás a hópehely-ból

Ha a fogadó adattár és a formátum megfelel az ebben a szakaszban ismertetett feltételeknek, a másolási tevékenység használatával közvetlenül másolhat a hópehely-ből a fogadóba. Data Factory ellenőrzi a beállításokat, és sikertelenül futtatja a másolási tevékenységet, ha a következő feltételek nem teljesülnek:

- A fogadó **társított szolgáltatás** az [**Azure Blob Storage**](connector-azure-blob-storage.md) **közös hozzáférésű aláírás** -hitelesítéssel. Ha az adatok közvetlen másolását Azure Data Lake Storage Gen2re szeretné használni a következő támogatott formátumban, létrehozhat egy Azure Blob társított szolgáltatást SAS-hitelesítéssel a ADLS Gen2-fiókjához, hogy ne használja a [Lépcsőzetes másolást a hópehely](#staged-copy-from-snowflake)-ből.

- A fogadó **adatformátuma** a következő konfigurációkból álló **parketta**, **tagolt szöveg** vagy **JSON** .

    - A **parketta** formátuma esetén a tömörítési kodek **egyike sem**, a **Snappy** vagy a **LZO**.
    - **Tagolt szöveges** formátum esetén:
        - `rowDelimiter`**\r\n** vagy egyetlen karakter.
        - `compression` nem lehet **tömörítés**, **gzip**, **bzip2** vagy **deflate**.
        - `encodingName` Alapértelmezés szerint marad, vagy az **UTF-8** értékre van állítva.
        - `quoteChar`**dupla idézőjel**, **szimpla idézőjel** vagy **üres karakterlánc** (nincs idézőjel karakter).
    - **JSON** formátum esetén a közvetlen másolás csak azt az esetet támogatja, ha a forrás hópehely-tábla vagy a lekérdezési eredmény csak egyetlen oszlopot tartalmaz, és az oszlop adattípusa **Variant**, **Object** vagy **Array**.
        - `compression` nem lehet **tömörítés**, **gzip**, **bzip2** vagy **deflate**.
        - `encodingName` Alapértelmezés szerint marad, vagy az **UTF-8** értékre van állítva.
        - `filePattern` a másolási tevékenység fogadója alapértelmezés szerint vagy **setOfObjects** értékre van állítva.
- A másolási tevékenység forrása területen nincs `additionalColumns` megadva.
- Nincs megadva az oszlop-hozzárendelés.

**Példa**

```json
"activities":[
    {
        "name": "CopyFromSnowflake",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Snowflake input dataset name>",
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
                "type": "SnowflakeSource",
                "sqlReaderQuery": "SELECT * FROM MYTABLE",
                "exportSettings": {
                    "type": "SnowflakeExportCopyCommand",
                    "additionalCopyOptions": {
                        "MAX_FILE_SIZE": "64000000",
                        "OVERWRITE": true
                    },
                    "additionalFormatOptions": {
                        "DATE_FORMAT": "'MM/DD/YYYY'"
                    }
                }
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

#### <a name="staged-copy-from-snowflake"></a>Lépcsőzetes másolás a hópehely-ból

Ha a fogadó adattára vagy formátuma nem kompatibilis a hópehely MÁSOLÁSi paranccsal, ahogy azt az utolsó szakaszban is említettük, engedélyezze a beépített előkészített másolatot egy ideiglenes Azure Blob Storage-példány használatával. Az előkészített másolási funkció jobb átviteli sebességet is biztosít. Data Factory az adatok a hópehely-ba történő exportálását átmeneti tárolóba, majd átmásolja az adatait a fogadóba, és végül törli az ideiglenes adatait az átmeneti tárolóból. Az adatok átmeneti használatával történő másolásával kapcsolatos részletekért tekintse meg a [Lépcsőzetes másolás című szakaszt](copy-activity-performance-features.md#staged-copy) .

A szolgáltatás használatához hozzon létre egy [Azure Blob Storage-beli társított szolgáltatást](connector-azure-blob-storage.md#linked-service-properties) , amely az Azure Storage-fiókra hivatkozik átmeneti előkészítésként. Ezután adja meg a `enableStaging` és a `stagingSettings` tulajdonságokat a másolási tevékenységben.

> [!NOTE]
> Az átmeneti Azure Blob Storage-beli társított szolgáltatásnak a hópehely MÁSOLÁSi parancs által megkövetelt közös hozzáférésű aláírás-hitelesítést kell használnia. 

**Példa**

```json
"activities":[
    {
        "name": "CopyFromSnowflake",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Snowflake input dataset name>",
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
                "type": "SnowflakeSource",
                "sqlReaderQuery": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
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

### <a name="snowflake-as-sink"></a>Hópehely mint fogadó

A hópehely-összekötő a (z) [[table]](https://docs.snowflake.com/en/sql-reference/sql/copy-into-table.html) paranccsal a legjobb teljesítmény eléréséhez használja a hópehely-példányt. Támogatja az Azure-beli hópehely-adatírást.

Ha a forrás adattárat és a formátumot natív módon támogatja a hópehely COPY parancs, a másolási tevékenység használatával közvetlenül másolhatja a forrásról a hópehely-ra. Részletekért lásd: [közvetlen másolás a hópehely-](#direct-copy-to-snowflake)ba. Ellenkező esetben használja [a beépített szakaszos másolást a hópehely](#staged-copy-to-snowflake)-ra.

Ha az Adatmásolást a hópehely-ba szeretné másolni, a másolási **tevékenység** fogadója szakaszban a következő tulajdonságok támogatottak.

| Tulajdonság          | Leírás                                                  | Kötelező                                      |
| :---------------- | :----------------------------------------------------------- | :-------------------------------------------- |
| típus              | A másolási tevékenység fogadójának Type tulajdonsága **SnowflakeSink** értékre van állítva. | Yes                                           |
| preCopyScript     | Adja meg a másolási tevékenység futtatásához szükséges SQL-lekérdezést, mielőtt az összes futtatáskor beírja az adatbevitelt a hópehely-ba. Ezzel a tulajdonsággal törölheti az előre feltöltött adatkészleteket. | No                                            |
| importSettings | Az adatíráshoz használt speciális beállítások a hópehely-ban. A COPY into parancs által támogatott beállításokat is konfigurálhatja, hogy a Data Factory áthaladjon az utasítás meghívásakor. | No |
| ***Alatt `importSettings` :*** |                                                              |  |
| típus | Az importálási parancs típusa **SnowflakeImportCopyCommand** értékre van állítva. | Yes |
| additionalCopyOptions | További másolási lehetőségek a kulcs-érték párok szótáraként megadva. Példák: ON_ERROR, FORCE, LOAD_UNCERTAIN_FILES. További információ: a [hópehely másolási beállításai](https://docs.snowflake.com/en/sql-reference/sql/copy-into-table.html#copy-options-copyoptions). | No |
| additionalFormatOptions | A MÁSOLÁSi parancsnak a kulcs-érték párok szótárában megadott további fájlformátum-beállításai. Példák: DATE_FORMAT, TIME_FORMAT, TIMESTAMP_FORMAT. További információkért lásd a [hópehely formátum típusának beállításait](https://docs.snowflake.com/en/sql-reference/sql/copy-into-table.html#format-type-options-formattypeoptions). | No |

#### <a name="direct-copy-to-snowflake"></a>Közvetlen másolás a hópehely-ba

Ha a forrás-adattár és-formátum megfelel az ebben a szakaszban ismertetett feltételeknek, a másolási tevékenység használatával közvetlenül másolhatja a forrásról a hópehely-ra. Azure Data Factory ellenőrzi a beállításokat, és sikertelenül futtatja a másolási tevékenységet, ha a következő feltételek nem teljesülnek:

- A **forráshoz társított szolgáltatás** az [**Azure Blob Storage**](connector-azure-blob-storage.md) **közös hozzáférésű aláírás** -hitelesítéssel. Ha a következő támogatott formátumból szeretne közvetlenül átmásolni Azure Data Lake Storage Gen2 adatait, létrehozhat egy Azure Blob társított szolgáltatást SAS-hitelesítéssel a ADLS Gen2-fiókjához, hogy ne használja a  [szakaszos másolást a hópehely](#staged-copy-to-snowflake)-ra.

- A **forrás adatformátuma** a következő konfigurációknál a **parketta**, a **tagolt szöveg** vagy a **JSON** :

    - A **parketta** formátuma esetén a tömörítési kodek **egyike sem** vagy a **Snappy**.

    - **Tagolt szöveges** formátum esetén:
        - `rowDelimiter`**\r\n** vagy egyetlen karakter. Ha a sorok elválasztó karaktere nem "\r\n", akkor `firstRowAsHeader` **false értékűnek** kell lennie, és nincs `skipLineCount` megadva.
        - `compression` nem lehet **tömörítés**, **gzip**, **bzip2** vagy **deflate**.
        - `encodingName` Alapértelmezés szerint balra van állítva, vagy az "UTF-8", "UTF-16", "UTF-16BE", "UTF-32", "UTF-32BE", "BIG5", "EUC-JP", "EUC-KR", "GB18030", "ISO-2022-JP", "ISO-2022-KR", "ISO-8859-1", "ISO-8859-2", "ISO-8859-5", "ISO-8859-6", "ISO-8859-7", "ISO-8859-8", "ISO-8859-9", "WINDOWS-1250", "WINDOWS-1251", "WINDOWS-1252", "WINDOWS-1253", "Windows-1254", "Windows-1255".
        - `quoteChar`**dupla idézőjel**, **szimpla idézőjel** vagy **üres karakterlánc** (nincs idézőjel karakter).
    - **JSON** formátum esetén a közvetlen másolás csak azt az esetet támogatja, ha a csak egyetlen oszlopból áll, és az oszlop adattípusa **Variant**, **Object** vagy **Array**.
        - `compression` nem lehet **tömörítés**, **gzip**, **bzip2** vagy **deflate**.
        - `encodingName` Alapértelmezés szerint marad, vagy az **UTF-8** értékre van állítva.
        - Nincs megadva az oszlop-hozzárendelés.

- A másolási tevékenység forrása: 

   -  `additionalColumns` nincs megadva.
   - Ha a forrás mappa, a értéke `recursive` true (igaz).
   - `prefix`,,, `modifiedDateTimeStart` `modifiedDateTimeEnd` és `enablePartitionDiscovery` nincsenek megadva.

**Példa**

```json
"activities":[
    {
        "name": "CopyToSnowflake",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Snowflake output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "SnowflakeSink",
                "importSettings": {
                    "type": "SnowflakeImportCopyCommand",
                    "copyOptions": {
                        "FORCE": "TRUE",
                        "ON_ERROR": "SKIP_FILE",
                    },
                    "fileFormatOptions": {
                        "DATE_FORMAT": "YYYY-MM-DD",
                    }
                }
            }
        }
    }
]
```

#### <a name="staged-copy-to-snowflake"></a>Előkészített másolás a hópehely-ba

Ha a forrás adattára vagy formátuma nem kompatibilis a hópehely MÁSOLÁSi paranccsal, ahogy azt az utolsó szakaszban is említettük, engedélyezze a beépített előkészített másolatot egy ideiglenes Azure Blob Storage-példány használatával. Az előkészített másolási funkció jobb átviteli sebességet is biztosít. A Data Factory automatikusan átalakítja a hópehely adatformátumra vonatkozó követelményeinek megfelelő adatmennyiséget. Ezután meghívja a MÁSOLÁSi parancsot, hogy betöltse az adatbevitelt a hópehely-ba. Végezetül törli az ideiglenes adatait a blob Storage-ból. Az adatok átmeneti használatával történő másolásával kapcsolatos részletekért tekintse meg a [Lépcsőzetes másolás című szakaszt](copy-activity-performance-features.md#staged-copy) .

A szolgáltatás használatához hozzon létre egy [Azure Blob Storage-beli társított szolgáltatást](connector-azure-blob-storage.md#linked-service-properties) , amely az Azure Storage-fiókra hivatkozik átmeneti előkészítésként. Ezután adja meg a `enableStaging` és a `stagingSettings` tulajdonságokat a másolási tevékenységben.

> [!NOTE]
> Az átmeneti Azure Blob Storage-beli társított szolgáltatásnak a hópehely MÁSOLÁSi parancs által megkövetelt közös hozzáférésű aláírás-hitelesítést kell használnia.

**Példa**

```json
"activities":[
    {
        "name": "CopyToSnowflake",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Snowflake output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "SnowflakeSink"
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

## <a name="mapping-data-flow-properties"></a>Adatfolyam-tulajdonságok leképezése

Az adatok leképezési folyamatba való átalakításakor a rendszer beolvashatja és írhatja a hópehely-táblákat. További információ: a forrás- [átalakítás](data-flow-source.md) és a fogadó [transzformáció](data-flow-sink.md) a leképezési adatfolyamatokban. Kiválaszthatja, hogy egy hópehely-adatkészletet vagy egy [beágyazott adatkészletet](data-flow-source.md#inline-datasets) használ forrásként és fogadóként.

### <a name="source-transformation"></a>Forrás-átalakítás

Az alábbi táblázat a hópehely forrás által támogatott tulajdonságokat sorolja fel. Ezeket a tulajdonságokat a **forrás beállításai** lapon módosíthatja. Az összekötő a hópehely [belső adatátvitelt](https://docs.snowflake.com/en/user-guide/spark-connector-overview.html#internal-data-transfer)használja.

| Név | Leírás | Kötelező | Megengedett értékek | Adatfolyam-parancsfájl tulajdonsága |
| ---- | ----------- | -------- | -------------- | ---------------- |
| Tábla | Ha a tábla bemenetként lehetőséget választja, az adatfolyam beolvassa a hópehely-adatkészletben megadott tábla összes adatát, vagy a forrás beállításai között, ha beágyazott adatkészletet használ. | Nem | Sztring | *(csak a beágyazott adatkészletek esetében)*<br>tableName<br>schemaName |
| Lekérdezés | Ha a lekérdezés bemenetként lehetőséget választotta, adjon meg egy lekérdezést a hópehely adatok beolvasásához. Ez a beállítás felülbírálja az adatkészletben kiválasztott táblákat.<br>Ha a séma, a tábla és az oszlopok neve kisbetűt tartalmaz, adja meg az objektumazonosítót a lekérdezésben, például: `select * from "schema"."myTable"` . | Nem | Sztring | lekérdezés |

#### <a name="snowflake-source-script-examples"></a>Példa a hópehely forrás parancsfájlokra

Ha a hópehely-adatkészletet használja forrásként, a társított adatfolyam-parancsfájl a következő:

```
source(allowSchemaDrift: true,
    validateSchema: false,
    query: 'select * from MYTABLE',
    format: 'query') ~> SnowflakeSource
```

Ha beágyazott adatkészletet használ, a kapcsolódó adatfolyam-parancsfájl a következőket teszi:

```
source(allowSchemaDrift: true,
    validateSchema: false,
    format: 'query',
    query: 'select * from MYTABLE',
    store: 'snowflake') ~> SnowflakeSource
```

### <a name="sink-transformation"></a>Fogadó transzformáció

Az alábbi táblázatban a hópehely fogadó által támogatott tulajdonságok szerepelnek. Ezeket a tulajdonságokat a **Beállítások** lapon módosíthatja. Beágyazott adatkészlet használata esetén további beállítások jelennek meg, amelyek megegyeznek az [adatkészlet tulajdonságai](#dataset-properties) szakaszban leírt tulajdonságokkal. Az összekötő a hópehely [belső adatátvitelt](https://docs.snowflake.com/en/user-guide/spark-connector-overview.html#internal-data-transfer)használja.

| Név | Leírás | Kötelező | Megengedett értékek | Adatfolyam-parancsfájl tulajdonsága |
| ---- | ----------- | -------- | -------------- | ---------------- |
| Frissítési módszer | Adja meg, hogy milyen műveleteket lehet engedélyezni a hópehely-célhelyen.<br>Sorok frissítéséhez, upsert vagy törléséhez [módosítási sor átalakítására](data-flow-alter-row.md) van szükség a műveletek sorainak címkézéséhez. | Yes | `true` vagy `false` | törölhető <br/>Insertable <br/>frissíthető <br/>upsertable |
| Kulcsok oszlopai | A frissítések, upsert és törlések esetében meg kell adni a kulcs oszlopát vagy oszlopait annak meghatározásához, hogy melyik sort kell megváltoztatni. | No | Tömb | keys |
| Tábla művelete | Meghatározza, hogy a rendszer az összes sort újra létrehozza vagy eltávolítja a célhelyről az írás előtt.<br>- **Nincs**: a rendszer nem hajt végre műveletet a táblán.<br>- **Újból létrehozva**: a tábla eldobása és újbóli létrehozása megtörténik. Új tábla dinamikus létrehozásakor szükséges.<br>- **Csonkítás**: a céltábla összes sora el lesz távolítva. | No | `true` vagy `false` | hozza létre újra<br/>truncate |

#### <a name="snowflake-sink-script-examples"></a>A hópehely fogadó szkriptek példái

Ha a hópehely-adatkészletet fogadó típusúként használja, a társított adatfolyam-parancsfájl a következő:

```
IncomingStream sink(allowSchemaDrift: true,
    validateSchema: false,
    deletable:true,
    insertable:true,
    updateable:true,
    upsertable:false,
    keys:['movieId'],
    format: 'table',
    skipDuplicateMapInputs: true,
    skipDuplicateMapOutputs: true) ~> SnowflakeSink
```

Ha beágyazott adatkészletet használ, a kapcsolódó adatfolyam-parancsfájl a következőket teszi:

```
IncomingStream sink(allowSchemaDrift: true,
    validateSchema: false,
    format: 'table',
    tableName: 'table',
    schemaName: 'schema',
    deletable: true,
    insertable: true,
    updateable: true,
    upsertable: false,
    store: 'snowflake',
    skipDuplicateMapInputs: true,
    skipDuplicateMapOutputs: true) ~> SnowflakeSink
```

## <a name="lookup-activity-properties"></a>Keresési tevékenység tulajdonságai

További információ a tulajdonságokról: [keresési tevékenység](control-flow-lookup-activity.md).

## <a name="next-steps"></a>Következő lépések

A forrásként és fogadóként támogatott adattárak listáját a Data Factory másolási tevékenysége című részben tekintheti meg a [támogatott adattárak és-formátumok](copy-activity-overview.md#supported-data-stores-and-formats)című témakörben.
