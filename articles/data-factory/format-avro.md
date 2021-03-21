---
title: Avro formátuma Azure Data Factory
description: Ez a témakör azt ismerteti, hogyan kezelhető a Avro formátuma Azure Data Factoryban.
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.date: 09/15/2020
ms.author: jingwang
ms.openlocfilehash: 4be499e8e304f34b1cab10aed41b5b98a5f24e9b
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "100392595"
---
# <a name="avro-format-in-azure-data-factory"></a>Avro formátuma Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Kövesse ezt a cikket, ha **elemezni szeretné a Avro-fájlokat, vagy Avro formátumban kell írnia azokat**. 

A Avro formátum a következő összekötők esetében támogatott: [Amazon S3](connector-amazon-simple-storage-service.md), [Azure Blob](connector-azure-blob-storage.md), [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md), [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md), [Azure file Storage](connector-azure-file-storage.md), [fájlrendszer](connector-file-system.md), [FTP](connector-ftp.md), [Google Cloud Storage](connector-google-cloud-storage.md), [HDFS](connector-hdfs.md), [http](connector-http.md)és [SFTP](connector-sftp.md).

## <a name="dataset-properties"></a>Adatkészlet tulajdonságai

Az adatkészletek definiálásához rendelkezésre álló csoportok és tulajdonságok teljes listáját az [adatkészletek](concepts-datasets-linked-services.md) című cikkben találja. Ez a szakasz a Avro adatkészlet által támogatott tulajdonságok listáját tartalmazza.

| Tulajdonság         | Leírás                                                  | Kötelező |
| ---------------- | ------------------------------------------------------------ | -------- |
| típus             | Az adatkészlet Type tulajdonságát **Avro** értékre kell állítani. | Yes      |
| location         | A fájl (ok) helyének beállításai. Minden fájl alapú összekötőhöz tartozik a saját hely típusa és a támogatott tulajdonságai `location` . **Tekintse meg a részleteket az összekötő cikk-> adatkészlet tulajdonságai szakaszban**. | Yes      |
| avroCompressionCodec | A Avro-fájlok írásakor használandó tömörítési kodek. A Avro-fájlokból való olvasáskor Data Factory automatikusan meghatározza a tömörítési kodeket a fájl metaadatai alapján.<br>A támogatott típusok a következők: "**none**" (alapértelmezett), "**deflate**", "**Snappy**". Megjegyzés: a másolási tevékenység jelenleg nem támogatja a Snappy-t az Avro-fájlok olvasásakor/írásakor. | No       |

> [!NOTE]
> A Avro-fájlok nem támogatják az oszlopnév üres területét.

Alább látható egy példa az Azure Blob Storage Avro adatkészletére:

```json
{
    "name": "AvroDataset",
    "properties": {
        "type": "Avro",
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
            "avroCompressionCodec": "snappy"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Másolási tevékenység tulajdonságai

A tevékenységek definiálásához elérhető csoportok és tulajdonságok teljes listáját a [folyamatok](concepts-pipelines-activities.md) című cikkben találja. Ez a szakasz a Avro forrás és a fogadó által támogatott tulajdonságok listáját tartalmazza.

### <a name="avro-as-source"></a>Avro forrásként

A másolási tevékenység ***\* forrása \**** szakaszban a következő tulajdonságok támogatottak.

| Tulajdonság      | Leírás                                                  | Kötelező |
| ------------- | ------------------------------------------------------------ | -------- |
| típus          | A másolási tevékenység forrásának Type tulajdonságát **AvroSource** értékre kell állítani. | Yes      |
| storeSettings | Az adattárakból származó adatok beolvasására szolgáló tulajdonságok csoportja. A fájl alapú összekötők a saját támogatott olvasási beállításaival rendelkeznek `storeSettings` . **Tekintse meg a részleteket az összekötőről szóló cikk – > másolási tevékenység tulajdonságai szakaszban**. | No       |

### <a name="avro-as-sink"></a>Avro fogadóként

A másolási ***\* \* tevékenység*** fogadója szakaszban a következő tulajdonságok támogatottak.

| Tulajdonság      | Leírás                                                  | Kötelező |
| ------------- | ------------------------------------------------------------ | -------- |
| típus          | A másolási tevékenység forrásának Type tulajdonságát **AvroSink** értékre kell állítani. | Yes      |
| formatSettings          | Tulajdonságok csoportja. Tekintse meg az alábbi **Avro írási beállítások** táblázatát.| No      |
| storeSettings | Az adattárakba való adatíráshoz szükséges tulajdonságok csoportja. A fájl alapú összekötők a saját támogatott írási beállításaival rendelkeznek `storeSettings` . **Tekintse meg a részleteket az összekötőről szóló cikk – > másolási tevékenység tulajdonságai szakaszban**. | No       |

Támogatott **Avro írási beállítások** a alatt `formatSettings` :

| Tulajdonság      | Leírás                                                  | Kötelező                                              |
| ------------- | ------------------------------------------------------------ | ----------------------------------------------------- |
| típus          | A formatSettings típusát **AvroWriteSettings** értékre kell beállítani. | Yes                                                   |
| maxRowsPerFile | Az Adatmappában való íráskor több fájl írására is választhatja, és a fájlok maximális számát is megadhatja.  | No |
| fileNamePrefix | `maxRowsPerFile`A konfigurálásakor alkalmazható.<br> Adja meg a fájlnév előtagját, ha több fájlra ír be adatírást, ami a következő mintának eredményezte: `<fileNamePrefix>_00000.<fileExtension>` . Ha nincs megadva, a rendszer automatikusan létrehozza a fájlnév-előtagot. Ez a tulajdonság nem érvényes, ha a forrás fájl-vagy [partíciós](copy-activity-performance-features.md)tárolásra képes adattár.  | No |

## <a name="mapping-data-flow-properties"></a>Adatfolyam-tulajdonságok leképezése

Az adatfolyamatok leképezése során a következő adattárakban olvashatók és írhatók a Avro formátuma: [Azure Blob Storage](connector-azure-blob-storage.md#mapping-data-flow-properties), [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md#mapping-data-flow-properties)és [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md#mapping-data-flow-properties).

### <a name="source-properties"></a>Forrás tulajdonságai

Az alábbi táblázat felsorolja a Avro-forrás által támogatott tulajdonságokat. Ezeket a tulajdonságokat a **forrás beállításai** lapon módosíthatja.

| Név | Leírás | Kötelező | Megengedett értékek | Adatfolyam-parancsfájl tulajdonsága |
| ---- | ----------- | -------- | -------------- | ---------------- |
| Wild kártya elérési útjai | A rendszer feldolgozza a helyettesítő karakteres elérési úttal egyező összes fájlt. Felülbírálja az adatkészletben beállított mappát és a fájl elérési útját. | nem | Karakterlánc [] | wildcardPaths |
| Partíció gyökerének elérési útja | A particionált fájlok esetében megadhatja a partíció gyökerének elérési útját, hogy a particionált mappák oszlopként legyenek olvashatók. | nem | Sztring | partitionRootPath |
| Fájlok listája | Azt jelzi, hogy a forrás egy szövegfájlra mutat-e, amely a feldolgozandó fájlokat listázza | nem | `true` vagy `false` | fileList |
| A fájl nevét tároló oszlop | Új oszlop létrehozása a forrásfájl nevével és elérési útjával | nem | Sztring | rowUrlColumn |
| Befejezés után | A fájlok törlése vagy áthelyezése a feldolgozás után. A fájl elérési útja a tároló gyökeréből indul el | nem | Törlés: `true` vagy `false` <br> Áthelyezése `['<from>', '<to>']` | purgeFiles <br> moveFiles |
| Szűrés utoljára módosítva | Válassza ki a fájlok szűrését az utolsó módosításuk alapján | nem | Időbélyeg | modifiedAfter <br> modifiedBefore |
| Nem található fájlok engedélyezése | Ha az értéke igaz, a rendszer nem dobja el a hibát, ha nem található fájl | nem | `true` vagy `false` | ignoreNoFilesFound |

### <a name="sink-properties"></a>Fogadó tulajdonságai

Az alábbi táblázatban az Avro-fogadó által támogatott tulajdonságok szerepelnek. Ezeket a tulajdonságokat a **Beállítások** lapon módosíthatja.

| Név | Leírás | Kötelező | Megengedett értékek | Adatfolyam-parancsfájl tulajdonsága |
| ---- | ----------- | -------- | -------------- | ---------------- |
| Mappa törlése | Ha a célmappa az írás előtt törlődik | nem | `true` vagy `false` | truncate |
| Fájlnév beállítás | Az írt adatnév formátuma. Alapértelmezés szerint egy fájl/partíció formátumban `part-#####-tid-<guid>` | nem | Minta: karakterlánc <br> /Partíció: karakterlánc [] <br> Oszlopbeli adatként: karakterlánc <br> Kimenet egyetlen fájlba: `['<fileName>']`  | filePattern <br> partitionFileNames <br> rowUrlColumn <br> partitionFileNames |
| Az összes idézőjel | Az összes érték beszúrása idézőjelek közé | nem | `true` vagy `false` | quoteAll |

## <a name="data-type-support"></a>Adattípusok támogatása

### <a name="copy-activity"></a>Másolási tevékenység
A Avro [összetett adattípusok](https://avro.apache.org/docs/current/spec.html#schema_complex) nem támogatottak (rekordok, enumerálások, tömbök, térképek, szakszervezetek és rögzített) a másolási tevékenységben.

### <a name="data-flows"></a>Adatfolyamok
Az adatfolyamatok Avro-fájljainak használatakor az összetett adattípusok olvashatók és írhatók, de ügyeljen rá, hogy először törölje a fizikai sémát az adatkészletből. Az adatfolyamatokban beállíthatja a logikai kivetítést, és kiszármaztathatja az összetett struktúrákat tartalmazó oszlopokat, majd automatikusan leképezheti ezeket a mezőket egy Avro-fájlra.

## <a name="next-steps"></a>Következő lépések

- [Másolási tevékenység – áttekintés](copy-activity-overview.md)
- [Keresési tevékenység](control-flow-lookup-activity.md)
- [GetMetadata tevékenység](control-flow-get-metadata-activity.md)
