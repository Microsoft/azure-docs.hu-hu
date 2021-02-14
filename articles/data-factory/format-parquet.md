---
title: A parketta formátuma Azure Data Factory
description: Ez a témakör azt ismerteti, hogyan kezelhető a parketta formátuma Azure Data Factoryban.
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.date: 09/27/2020
ms.author: jingwang
ms.openlocfilehash: a10403b5f26b551458a9e20330bc817512f707de
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/14/2021
ms.locfileid: "100386391"
---
# <a name="parquet-format-in-azure-data-factory"></a>A parketta formátuma Azure Data Factory
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Kövesse ezt a cikket, ha szeretné **elemezni a parketta-fájlokat, vagy a parketta formátumba írja az adatírást**. 

A parketta formátuma a következő összekötők esetén támogatott: [Amazon S3](connector-amazon-simple-storage-service.md), [Azure Blob](connector-azure-blob-storage.md), [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md), [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md), [Azure file Storage](connector-azure-file-storage.md), [fájlrendszer](connector-file-system.md), [FTP](connector-ftp.md), [Google Cloud Storage](connector-google-cloud-storage.md), [HDFS](connector-hdfs.md), [http](connector-http.md)és [SFTP](connector-sftp.md).

## <a name="dataset-properties"></a>Adatkészlet tulajdonságai

Az adatkészletek definiálásához rendelkezésre álló csoportok és tulajdonságok teljes listáját az [adatkészletek](concepts-datasets-linked-services.md) című cikkben találja. Ez a szakasz a Parquet-adatkészlet által támogatott tulajdonságok listáját tartalmazza.

| Tulajdonság         | Leírás                                                  | Kötelező |
| ---------------- | ------------------------------------------------------------ | -------- |
| típus             | Az adatkészlet Type tulajdonságát a **Parquet** értékre kell beállítani. | Yes      |
| location         | A fájl (ok) helyének beállításai. Minden fájl alapú összekötőhöz tartozik a saját hely típusa és a támogatott tulajdonságai `location` . **Tekintse meg a részleteket az összekötő cikk-> adatkészlet tulajdonságai szakaszban**. | Yes      |
| compressionCodec | A Parquet-fájlokba való íráskor használandó tömörítési kodek. A Parquet-fájlokból való olvasáskor az adat-előállítók automatikusan meghatározzák a tömörítési kodeket a fájl metaadatai alapján.<br>A támogatott típusok a következők: "**none**", "**gzip**", "**Snappy**" (alapértelmezett) és "**LZO**". Megjegyzés: a másolási tevékenység jelenleg nem támogatja a LZO a parketta-fájlok olvasása/írása közben. | No       |

> [!NOTE]
> A Parquet-fájlok nem támogatják a szóközöket az oszlop nevében.

Alább látható egy példa a Parquet-adatkészletre az Azure Blob Storageban:

```json
{
    "name": "ParquetDataset",
    "properties": {
        "type": "Parquet",
        "linkedServiceName": {
            "referenceName": "<Azure Blob Storage linked service name>",
            "type": "LinkedServiceReference"
        },
        "schema": [ < physical schema, optional, retrievable during authoring > ],
        "typeProperties": {
            "location": {
                "type": "AzureBlobStorageLocation",
                "container": "containername",
                "folderPath": "folder/subfolder",
            },
            "compressionCodec": "snappy"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Másolási tevékenység tulajdonságai

A tevékenységek definiálásához elérhető csoportok és tulajdonságok teljes listáját a [folyamatok](concepts-pipelines-activities.md) című cikkben találja. Ez a szakasz a Parquet forrás és a fogadó által támogatott tulajdonságok listáját tartalmazza.

### <a name="parquet-as-source"></a>Parketta forrásként

A másolási tevékenység ***\* forrása \**** szakaszban a következő tulajdonságok támogatottak.

| Tulajdonság      | Leírás                                                  | Kötelező |
| ------------- | ------------------------------------------------------------ | -------- |
| típus          | A másolási tevékenység forrásának Type tulajdonságát **ParquetSource** értékre kell állítani. | Yes      |
| storeSettings | Az adattárakból származó adatok beolvasására szolgáló tulajdonságok csoportja. A fájl alapú összekötők a saját támogatott olvasási beállításaival rendelkeznek `storeSettings` . **Tekintse meg a részleteket az összekötőről szóló cikk – > másolási tevékenység tulajdonságai szakaszban**. | No       |

### <a name="parquet-as-sink"></a>Parketta mint fogadó

A másolási ***\* \* tevékenység*** fogadója szakaszban a következő tulajdonságok támogatottak.

| Tulajdonság      | Leírás                                                  | Kötelező |
| ------------- | ------------------------------------------------------------ | -------- |
| típus          | A másolási tevékenység fogadójának Type tulajdonságát **ParquetSink** értékre kell állítani. | Yes      |
| formatSettings | Tulajdonságok csoportja. Tekintse meg a **parketta írási beállítások** táblázatát alább. |    No      |
| storeSettings | Az adattárakba való adatíráshoz szükséges tulajdonságok csoportja. A fájl alapú összekötők a saját támogatott írási beállításaival rendelkeznek `storeSettings` . **Tekintse meg a részleteket az összekötőről szóló cikk – > másolási tevékenység tulajdonságai szakaszban**. | No       |

Támogatott **parketta írási beállításai** a alatt `formatSettings` :

| Tulajdonság      | Leírás                                                  | Kötelező                                              |
| ------------- | ------------------------------------------------------------ | ----------------------------------------------------- |
| típus          | A formatSettings típusát **ParquetWriteSettings** értékre kell beállítani. | Yes                                                   |
| maxRowsPerFile | Az Adatmappában való íráskor több fájl írására is választhatja, és a fájlok maximális számát is megadhatja.  | No |
| fileNamePrefix | `maxRowsPerFile`A konfigurálásakor alkalmazható.<br> Adja meg a fájlnév előtagját, ha több fájlra ír be adatírást, ami a következő mintának eredményezte: `<fileNamePrefix>_00000.<fileExtension>` . Ha nincs megadva, a rendszer automatikusan létrehozza a fájlnév-előtagot. Ez a tulajdonság nem érvényes, ha a forrás fájl-vagy [partíciós](copy-activity-performance-features.md)tárolásra képes adattár.  | No |

## <a name="mapping-data-flow-properties"></a>Adatfolyam-tulajdonságok leképezése

Az adatfolyamatok leképezése során a következő adattárakban olvashatók és írhatók a parketta formátuma: [Azure Blob Storage](connector-azure-blob-storage.md#mapping-data-flow-properties), [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md#mapping-data-flow-properties)és [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md#mapping-data-flow-properties).

### <a name="source-properties"></a>Forrás tulajdonságai

Az alábbi táblázat a Parquet-forrás által támogatott tulajdonságokat sorolja fel. Ezeket a tulajdonságokat a **forrás beállításai** lapon módosíthatja.

| Név | Leírás | Kötelező | Megengedett értékek | Adatfolyam-parancsfájl tulajdonsága |
| ---- | ----------- | -------- | -------------- | ---------------- |
| Formátum | A formátumot kötelező megadni `parquet` | igen | `parquet` | formátumban |
| Wild kártya elérési útjai | A rendszer feldolgozza a helyettesítő karakteres elérési úttal egyező összes fájlt. Felülbírálja az adatkészletben beállított mappát és a fájl elérési útját. | nem | Karakterlánc [] | wildcardPaths |
| Partíció gyökerének elérési útja | A particionált fájlok esetében megadhatja a partíció gyökerének elérési útját, hogy a particionált mappák oszlopként legyenek olvashatók. | nem | Sztring | partitionRootPath |
| Fájlok listája | Azt jelzi, hogy a forrás egy szövegfájlra mutat-e, amely a feldolgozandó fájlokat listázza | nem | `true` vagy `false` | fileList |
| A fájl nevét tároló oszlop | Új oszlop létrehozása a forrásfájl nevével és elérési útjával | nem | Sztring | rowUrlColumn |
| Befejezés után | A fájlok törlése vagy áthelyezése a feldolgozás után. A fájl elérési útja a tároló gyökeréből indul el | nem | Törlés: `true` vagy `false` <br> Áthelyezése `[<from>, <to>]` | purgeFiles <br> moveFiles |
| Szűrés utoljára módosítva | Válassza ki a fájlok szűrését az utolsó módosításuk alapján | nem | Időbélyeg | modifiedAfter <br> modifiedBefore |
| Nem található fájlok engedélyezése | Ha az értéke igaz, a rendszer nem dobja el a hibát, ha nem található fájl | nem | `true` vagy `false` | ignoreNoFilesFound |

### <a name="source-example"></a>Forrás példa

Az alábbi képen egy példa látható a parketta forrásának konfigurálására az adatfolyamatok leképezése során.

![Parketta forrása](media/data-flow/parquet-source.png)

A társított adatfolyam-parancsfájl:

```
source(allowSchemaDrift: true,
    validateSchema: false,
    rowUrlColumn: 'fileName',
    format: 'parquet') ~> ParquetSource
```

### <a name="sink-properties"></a>Fogadó tulajdonságai

Az alábbi táblázat a Parquet fogadó által támogatott tulajdonságokat sorolja fel. Ezeket a tulajdonságokat a **Beállítások** lapon módosíthatja.

| Név | Leírás | Kötelező | Megengedett értékek | Adatfolyam-parancsfájl tulajdonsága |
| ---- | ----------- | -------- | -------------- | ---------------- |
| Formátum | A formátumot kötelező megadni `parquet` | igen | `parquet` | formátumban |
| Mappa törlése | Ha a célmappa az írás előtt törlődik | nem | `true` vagy `false` | truncate |
| Fájlnév beállítás | Az írt adatnév formátuma. Alapértelmezés szerint egy fájl/partíció formátumban `part-#####-tid-<guid>` | nem | Minta: karakterlánc <br> /Partíció: karakterlánc [] <br> Oszlopbeli adatként: karakterlánc <br> Kimenet egyetlen fájlba: `['<fileName>']` | filePattern <br> partitionFileNames <br> rowUrlColumn <br> partitionFileNames |

### <a name="sink-example"></a>Fogadó példa

Az alábbi képen egy olyan parketta-fogadó konfiguráció látható, amely az adatfolyamatok leképezésére szolgál.

![Parketta-fogadó](media/data-flow/parquet-sink.png)

A társított adatfolyam-parancsfájl:

```
ParquetSource sink(
    format: 'parquet',
    filePattern:'output[n].parquet',
    truncate: true,
    allowSchemaDrift: true,
    validateSchema: false,
    skipDuplicateMapInputs: true,
    skipDuplicateMapOutputs: true) ~> ParquetSink
```

## <a name="data-type-support"></a>Adattípusok támogatása

A parketta összetett adattípusai (például a Térkép, a lista, a STRUCT) jelenleg csak adatforgalomban támogatottak, nem a másolási tevékenységekben. Ha összetett típusokat szeretne használni az adatfolyamatokban, ne importálja az adatkészletben a sémát, így a séma üres marad az adatkészletben. Ezután a forrás-átalakításban importálja a leképezést.

## <a name="using-self-hosted-integration-runtime"></a>Saját üzemeltetésű Integration Runtime használata

> [!IMPORTANT]
> A saját üzemeltetésű Integration Runtime, például a helyszíni és a Felhőbeli adattárak közötti másoláshoz, ha nem a (z) **rendszerű parketta-** fájlokat másolja, telepítenie kell a **64 bites JRE 8 (Java Runtime Environment) vagy a OpenJDK** és a **Microsoft Visual C++ 2010 terjeszthető csomagot** az IR-gépen. További részletekért olvassa el a következő bekezdést.

A saját üzemeltetésű, a Parquet-fájlok szerializálásával/deszerializálásával futó másolás esetén az ADF a Java futtatókörnyezetet úgy keresi meg, hogy először ellenőrzi a JRE beállításjegyzékét *`(SOFTWARE\JavaSoft\Java Runtime Environment\{Current Version}\JavaHome)`* , ha nem található, másodsorban a OpenJDK rendszer-változó ellenőrzése *`JAVA_HOME`* .

- A **JRE használatához**: a 64 bites IR használatához 64 bites JRE szükséges. [Itt](https://go.microsoft.com/fwlink/?LinkId=808605)találhatja meg.
- **A OpenJDK használata**: az IR 3,13-es verzió óta támogatott. Csomagolja ki a jvm.dllt a OpenJDK összes többi szükséges szerelvényével a saját üzemeltetésű IR-gépre, és ennek megfelelően állítsa be a rendszerkörnyezeti változót JAVA_HOME.
- **Visual c++ 2010 Újraterjeszthető csomag telepítése: a** visual c++ 2010 Újraterjeszthető csomag nincs telepítve a saját üzemeltetésű IR-telepítésekkel. [Itt](https://www.microsoft.com/download/details.aspx?id=14632)találhatja meg.

> [!TIP]
> Ha saját üzemeltetésű Integration Runtime használatával másol adatokba vagy a-ból a parketta formátumba, és a "hiba történt a Java, üzenet: **Java. lang. működése OutOfMemoryError: Java heap Space**" kifejezéssel, akkor hozzáadhat egy környezeti változót a saját üzemeltetésű integrációs `_JAVA_OPTIONS` modult futtató gépen, amely a JVM minimális/maximális méretének módosítását teszi lehetővé, majd újból futtatja a folyamatot.

![JVM-halom méretének beállítása a saját üzemeltetésű IR-ben](./media/supported-file-formats-and-compression-codecs/set-jvm-heap-size-on-selfhosted-ir.png)

Példa: állítsa be `_JAVA_OPTIONS` a változót értékkel `-Xms256m -Xmx16g` . A jelző `Xms` meghatározza a Java virtuális gép (JVM) kezdeti memória-kiosztási készletét, míg `Xmx` a maximális memória-kiosztási készletet adja meg. Ez azt jelenti, hogy a JVM a memóriával fog elindulni, `Xms` és a memória maximális mennyiségét fogja tudni használni `Xmx` . Alapértelmezés szerint az ADF minimális 64 MB és Max 1G értéket használ.

## <a name="next-steps"></a>Következő lépések

- [Másolási tevékenység – áttekintés](copy-activity-overview.md)
- [Adatfolyam hozzárendelése](concepts-data-flow-overview.md)
- [Keresési tevékenység](control-flow-lookup-activity.md)
- [GetMetadata tevékenység](control-flow-get-metadata-activity.md)
