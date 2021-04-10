---
title: Tagolt szöveg formátuma Azure Data Factory
description: Ez a témakör azt ismerteti, hogyan kezelhető a tagolt szöveges formátum a Azure Data Factoryban.
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.date: 03/23/2021
ms.author: jingwang
ms.openlocfilehash: daf3691b48f7bf12e9ef51de7d4253dad9dbd2b1
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "105026870"
---
# <a name="delimited-text-format-in-azure-data-factory"></a>Tagolt szöveg formátuma Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Kövesse ezt a cikket, ha el szeretné végezni **a tagolt szövegfájlok elemzését vagy az adatmennyiség tagolt szöveges formátumba való írását**. 

A tagolt szöveges formátum a következő összekötők esetében támogatott: [Amazon S3](connector-amazon-simple-storage-service.md), [Azure Blob](connector-azure-blob-storage.md), [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md), [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md), [Azure file Storage](connector-azure-file-storage.md), [fájlrendszer](connector-file-system.md), [FTP](connector-ftp.md), [Google Cloud Storage](connector-google-cloud-storage.md), [HDFS](connector-hdfs.md), [http](connector-http.md)és [SFTP](connector-sftp.md).

## <a name="dataset-properties"></a>Adatkészlet tulajdonságai

Az adatkészletek definiálásához rendelkezésre álló csoportok és tulajdonságok teljes listáját az [adatkészletek](concepts-datasets-linked-services.md) című cikkben találja. Ez a szakasz a tagolt szöveges adatkészlet által támogatott tulajdonságok listáját tartalmazza.

| Tulajdonság         | Leírás                                                  | Kötelező |
| ---------------- | ------------------------------------------------------------ | -------- |
| típus             | Az adatkészlet Type tulajdonságát **DelimitedText** értékre kell állítani. | Yes      |
| location         | A fájl (ok) helyének beállításai. Minden fájl alapú összekötőhöz tartozik a saját hely típusa és a támogatott tulajdonságai `location` .  | Yes      |
| columnDelimiter  | A fájlban lévő oszlopok elkülönítésére szolgáló karakter (ek). <br>Az alapértelmezett érték a **vessző `,`**. Ha az oszlop elválasztója üres sztringként van definiálva, azaz nincs elválasztó karakter, a teljes sor egyetlen oszlopként lesz elvégezve.<br>Jelenleg az oszlop elválasztója üres sztringként vagy többkarakteres értékként csak a leképezési adatforgalom esetében támogatott, a másolási tevékenység azonban nem.  | No       |
| rowDelimiter     | Az egyetlen karakter vagy "\r\n" egy fájl sorainak elkülönítésére szolgál. <br>Az alapértelmezett érték a következő értékek egyike **: ["\r\n", "\r", "\n"]** és **"\n" vagy "\r\n", íráshoz** az adatforgalom és a másolási tevékenység hozzárendelésével <br>Ha a sor elválasztó értéke nem határolójel (üres karakterlánc), akkor az oszlop elválasztóját nem kötelező megadni (üres karakterlánc) is, ami azt jelenti, hogy a teljes tartalmat egyetlen értékként kell kezelni.<br>Jelenleg a sorok elválasztója üres sztringként csak a leképezési folyamat esetében támogatott, a másolási tevékenység azonban nem. | No       |
| quoteChar        | Az oszlop értékeit idézőjelek közé tartalmazó karakter. <br>Az alapértelmezett **érték idézőjelek** `"` . <br>Ha `quoteChar` üres karakterláncként van definiálva, az azt jelenti, hogy nincs idézőjel karakter, és az oszlop értéke nincs megadva, és a `escapeChar` rendszer az oszlop elválasztóját és saját magát is elkerüli. | No       |
| escapeChar       | Az idézőjelek között egy idézett értéken belüli egyetlen karakter.<br>Az alapértelmezett érték **fordított perjel `\`**. <br>Ha `escapeChar` üres karakterláncként van definiálva, a `quoteChar` értéket üres karakterláncként kell megadni, amely esetben ügyeljen arra, hogy az összes oszlop értéke ne tartalmazzon határolójeleket. | No       |
| firstRowAsHeader | Meghatározza, hogy az első sort fejlécként kell-e kezelni/kijelölni az oszlopok neveivel.<br>Az engedélyezett értékek: **true** és **false** (alapértelmezett).<br>Ha az első sor hamis fejlécként van megjelölve, jegyezze Column_ fel a felhasználói felületi adatok előnézetét és a keresési tevékenység kimenetének automatikus előállítását Prop_ {n} (0-tól kezdődően), a másolási tevékenységhez [explicit leképezést](copy-activity-schema-and-type-mapping.md#explicit-mapping) kell készíteni a forrásról a fogadóra, és meg kell keresnie az oszlopokat sorszám alapján (1-től kezdődően), és az adatfolyam-  | No       |
| nullValue        | Megadja a null értékű karakterlánc-ábrázolást. <br>Az alapértelmezett érték **üres karakterlánc**. | No       |
| encodingName     | A tesztelési fájlok olvasására/írására szolgáló kódolási típus. <br>Az engedélyezett értékek a következők: "UTF-8", "UTF-16", "UTF-16BE", "UTF-32", "UTF-32BE", "US-ASCII", "UTF-7", "BIG5", "EUC-JP", "EUC-KR", "GB2312", "GB18030", "JOHAB", "SHIFT-JIS", "CP875", "CP866", "IBM00858", "IBM037", "IBM273", "IBM437", "IBM500", "IBM737", "IBM775", "IBM850", "IBM852", "IBM855", "IBM857", "IBM860", "IBM861", "IBM863", "IBM864", "IBM865", "IBM869", "IBM870", "IBM01140", "IBM01141", "IBM01142", "IBM01143", "IBM01144", "IBM01145", "IBM01146", "IBM01147", "IBM01148", "IBM01149", "", "ISO-2022-JP", "ISO-2022-KR", "ISO-8859-1", "ISO-8859-2", "ISO-8859-3", "ISO-8859-4", "ISO-8859-5", "ISO-8859-6", "ISO-8859-7", "ISO-8859-8", "ISO-8859-9", "ISO-8859-13" "ISO-8859-15", "WINDOWS-874", "WINDOWS-1250", "WINDOWS-1251", "WINDOWS-1252", "WINDOWS-1253", "WINDOWS-1254", "WINDOWS-1255", "WINDOWS-1256", "WINDOWS-1257", "WINDOWS-1258".<br>Megjegyzés: a leképezési folyamat nem támogatja az UTF-7 kódolást. | No       |
| compressionCodec | A szövegfájlok olvasásához/írásához használt tömörítési kodek. <br>Az engedélyezett értékek a **bzip2**, a **gzip**, a **deflate**, a **ZipDeflate**, a **TarGzip**, a **tar**, a **Snappy** vagy a **lz4**. Az alapértelmezett érték nincs tömörítve. <br>**Megjegyzés:** jelenleg a másolási tevékenység nem támogatja a "snappy" & "lz4", és a leképezési folyamat nem támogatja a "ZipDeflate", a "TarGzip" és a "tar" típust. <br>**Megjegyzés** : Ha a másolási tevékenységgel kibontja a **ZipDeflate** / **TarGzip** / **tar** -fájl (oka) t, és az írás a fájl alapú fogadó adattárba történik, az alapértelmezett fájlok a mappába kerülnek, a `<path specified in dataset>/<folder named as source compressed file>/` `preserveZipFileNameAsFolder` / `preserveCompressionFileNameAsFolder` [másolási tevékenység forrásaként](#delimited-text-as-source) pedig megadhatja, hogy meg kell-e őrizni a tömörített fájl (ok) nevét a mappa szerkezeteként. | No       |
| compressionLevel | A tömörítési arány. <br>Az engedélyezett értékek az **optimálisak** vagy a **leggyorsabbek**.<br>- **Leggyorsabb:** A tömörítési műveletnek a lehető leggyorsabban kell elvégeznie, még akkor is, ha az eredményül kapott fájl nem tömöríthető optimálisan.<br>- **Optimális**: a tömörítési műveletet optimálisan kell tömöríteni, még akkor is, ha a művelet végrehajtása hosszú időt vesz igénybe. További információ: [tömörítési szint](/dotnet/api/system.io.compression.compressionlevel) témakör. | No       |

Alább látható egy példa az Azure Blob Storage tagolt szöveges adatkészletre:

```json
{
    "name": "DelimitedTextDataset",
    "properties": {
        "type": "DelimitedText",
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
            "columnDelimiter": ",",
            "quoteChar": "\"",
            "escapeChar": "\"",
            "firstRowAsHeader": true,
            "compressionCodec": "gzip"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Másolási tevékenység tulajdonságai

A tevékenységek definiálásához elérhető csoportok és tulajdonságok teljes listáját a [folyamatok](concepts-pipelines-activities.md) című cikkben találja. Ez a szakasz a tagolt szöveges forrás és a fogadó által támogatott tulajdonságok listáját tartalmazza.

### <a name="delimited-text-as-source"></a>Tagolt szöveg forrásként 

A másolási tevékenység ***\* forrása \**** szakaszban a következő tulajdonságok támogatottak.

| Tulajdonság       | Leírás                                                  | Kötelező |
| -------------- | ------------------------------------------------------------ | -------- |
| típus           | A másolási tevékenység forrásának Type tulajdonságát **DelimitedTextSource** értékre kell állítani. | Yes      |
| formatSettings | Tulajdonságok csoportja. Tekintse meg az alábbi, **tagolt szöveg olvasási beállítások** táblázatát. |  No       |
| storeSettings  | Az adattárakból származó adatok beolvasására szolgáló tulajdonságok csoportja. A fájl alapú összekötők a saját támogatott olvasási beállításaival rendelkeznek `storeSettings` . | No       |

Támogatott **tagolt szöveges olvasási beállítások** a következő alatt `formatSettings` :

| Tulajdonság      | Leírás                                                  | Kötelező |
| ------------- | ------------------------------------------------------------ | -------- |
| típus          | A formatSettings típusát **DelimitedTextReadSettings** értékre kell beállítani. | Yes      |
| skipLineCount | Az adatok bemeneti fájlokból való olvasásakor kihagyható **nem üres** sorok számát jelzi. <br>Ha a skipLineCount és a firstRowAsHeader tulajdonság is meg van adva, a rendszer először kihagyja a sorokat, majd beolvassa a fejléc-információkat a bemeneti fájlból. | No       |
| compressionProperties | Egy adott tömörítési kodekhez tartozó adatok kibontására szolgáló tulajdonságok csoportja. | No       |
| preserveZipFileNameAsFolder<br>(*a `compressionProperties` -ben -> ) `type` `ZipDeflateReadSettings`* |  Akkor érvényes, ha a bemeneti adatkészlet **ZipDeflate** tömörítéssel van konfigurálva. Azt jelzi, hogy a forrás zip-fájlnevet a másolás során a mappa szerkezeteként kell-e megőrizni.<br>– Ha **true (alapértelmezett)** értékre van állítva, Data Factory a kibontott fájlokat ír a következőre: `<path specified in dataset>/<folder named as source zip file>/` .<br>– Ha **hamis** értékre van állítva, Data Factory a kibontott fájlokat közvetlenül a következőre írja: `<path specified in dataset>` . A versenyzés vagy a váratlan viselkedés elkerülése érdekében ügyeljen arra, hogy ne legyenek duplikált fájlnevek különböző zip-fájlokban.  | No |
| preserveCompressionFileNameAsFolder<br>(*a `compressionProperties` -> `type` `TarGZipReadSettings` -ban `TarReadSettings` vagy a*-ben)  | Akkor érvényes, ha a bemeneti adatkészlet **TarGzip** / **tar** tömörítéssel van konfigurálva. Azt jelzi, hogy a forrás tömörített fájlnevet a másolás során a mappa szerkezeteként kell-e megőrizni.<br>– Ha **true (alapértelmezett)** értékre van állítva, Data Factory a kibontott fájlokat ír a következőre: `<path specified in dataset>/<folder named as source compressed file>/` . <br>-Ha **hamis** értékre van állítva, Data Factory a kibontott fájlokat közvetlenül a következőre írja: `<path specified in dataset>` . A versenyzés vagy a váratlan viselkedés elkerülése érdekében ügyeljen arra, hogy ne legyenek duplikált fájlnevek a különböző forrásfájlok között. | No |

```json
"activities": [
    {
        "name": "CopyFromDelimitedText",
        "type": "Copy",
        "typeProperties": {
            "source": {
                "type": "DelimitedTextSource",
                "storeSettings": {
                    "type": "AzureBlobStorageReadSettings",
                    "recursive": true
                },
                "formatSettings": {
                    "type": "DelimitedTextReadSettings",
                    "skipLineCount": 3,
                    "compressionProperties": {
                        "type": "ZipDeflateReadSettings",
                        "preserveZipFileNameAsFolder": false
                    }
                }
            },
            ...
        }
        ...
    }
]
```

### <a name="delimited-text-as-sink"></a>Tagolt szöveg fogadóként

A másolási ***\* \* tevékenység*** fogadója szakaszban a következő tulajdonságok támogatottak.

| Tulajdonság       | Leírás                                                  | Kötelező |
| -------------- | ------------------------------------------------------------ | -------- |
| típus           | A másolási tevékenység forrásának Type tulajdonságát **DelimitedTextSink** értékre kell állítani. | Yes      |
| formatSettings | Tulajdonságok csoportja. Tekintse meg az alábbi, **tagolt szöveges írási beállítások** táblázatát. |    No      |
| storeSettings  | Az adattárakba való adatíráshoz szükséges tulajdonságok csoportja. A fájl alapú összekötők a saját támogatott írási beállításaival rendelkeznek `storeSettings` .  | No       |

Támogatott **tagolt szöveges írási beállítások** a (z) alatt `formatSettings` :

| Tulajdonság      | Leírás                                                  | Kötelező                                              |
| ------------- | ------------------------------------------------------------ | ----------------------------------------------------- |
| típus          | A formatSettings típusát **DelimitedTextWriteSettings** értékre kell beállítani. | Yes                                                   |
| fileExtension | A kimeneti fájlok elnevezésére használt fájlkiterjesztés, például: `.csv` , `.txt` . Meg kell adni, ha az `fileName` nincs megadva a kimeneti DelimitedText adatkészletben. Ha a fájl neve konfigurálva van a kimeneti adatkészletben, akkor a rendszer a fogadó fájl nevét fogja használni, és a fájlkiterjesztés beállítás figyelmen kívül lesz hagyva.  | Igen, ha a fájl neve nincs megadva a kimeneti adatkészletben |
| maxRowsPerFile | Az Adatmappában való íráskor több fájl írására is választhatja, és a fájlok maximális számát is megadhatja.  | No |
| fileNamePrefix | `maxRowsPerFile`A konfigurálásakor alkalmazható.<br> Adja meg a fájlnév előtagját, ha több fájlra ír be adatírást, ami a következő mintának eredményezte: `<fileNamePrefix>_00000.<fileExtension>` . Ha nincs megadva, a rendszer automatikusan létrehozza a fájlnév-előtagot. Ez a tulajdonság nem érvényes, ha a forrás fájl-vagy [partíciós](copy-activity-performance-features.md)tárolásra képes adattár.  | No |

## <a name="mapping-data-flow-properties"></a>Adatfolyam-tulajdonságok leképezése

Az adatfolyamatok leképezése során a következő adattárakban olvashatók és írhatók a tagolt szöveg formátuma: [Azure Blob Storage](connector-azure-blob-storage.md#mapping-data-flow-properties), [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md#mapping-data-flow-properties)és [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md#mapping-data-flow-properties).

### <a name="source-properties"></a>Forrás tulajdonságai

Az alábbi táblázatban a tagolt szöveges forrás által támogatott tulajdonságok szerepelnek. Ezeket a tulajdonságokat a **forrás beállításai** lapon módosíthatja.

| Név | Leírás | Kötelező | Megengedett értékek | Adatfolyam-parancsfájl tulajdonsága |
| ---- | ----------- | -------- | -------------- | ---------------- |
| Wild kártya elérési útjai | A rendszer feldolgozza a helyettesítő karakteres elérési úttal egyező összes fájlt. Felülbírálja az adatkészletben beállított mappát és a fájl elérési útját. | nem | Karakterlánc [] | wildcardPaths |
| Partíció gyökerének elérési útja | A particionált fájlok esetében megadhatja a partíció gyökerének elérési útját, hogy a particionált mappák oszlopként legyenek olvashatók. | nem | Sztring | partitionRootPath |
| Fájlok listája | Azt jelzi, hogy a forrás egy szövegfájlra mutat-e, amely a feldolgozandó fájlokat listázza | nem | `true` vagy `false` | fileList |
| Többsoros sorok | A forrásfájl több sorra kiterjedő sorokat tartalmaz. A többsoros értékeknek idézőjelek közé kell esniük. | nem `true` vagy `false` | multiLineRow |
| A fájl nevét tároló oszlop | Új oszlop létrehozása a forrásfájl nevével és elérési útjával | nem | Sztring | rowUrlColumn |
| Befejezés után | A fájlok törlése vagy áthelyezése a feldolgozás után. A fájl elérési útja a tároló gyökeréből indul el | nem | Törlés: `true` vagy `false` <br> Áthelyezése `['<from>', '<to>']` | purgeFiles <br> moveFiles |
| Szűrés utoljára módosítva | Válassza ki a fájlok szűrését az utolsó módosításuk alapján | nem | Időbélyeg | modifiedAfter <br> modifiedBefore |
| Nem található fájlok engedélyezése | Ha az értéke igaz, a rendszer nem dobja el a hibát, ha nem található fájl | nem | `true` vagy `false` | ignoreNoFilesFound |

### <a name="source-example"></a>Forrás példa

Az alábbi ábrán egy példa látható egy tagolt szöveges forrás konfigurációra az adatfolyamatok leképezése során.

![DelimitedText forrása](media/data-flow/delimited-text-source.png)

A társított adatfolyam-parancsfájl:

```
source(
    allowSchemaDrift: true,
    validateSchema: false,
    multiLineRow: true,
    wildcardPaths:['*.csv']) ~> CSVSource
```

> [!NOTE]
> Az adatfolyam-források támogatják a Hadoop-fájlrendszerek által támogatott, korlátozott Linux-globbing

### <a name="sink-properties"></a>Fogadó tulajdonságai

Az alábbi táblázat felsorolja a tagolt szöveges fogadó által támogatott tulajdonságokat. Ezeket a tulajdonságokat a **Beállítások** lapon módosíthatja.

| Név | Leírás | Kötelező | Megengedett értékek | Adatfolyam-parancsfájl tulajdonsága |
| ---- | ----------- | -------- | -------------- | ---------------- |
| Mappa törlése | Ha a célmappa az írás előtt törlődik | nem | `true` vagy `false` | truncate |
| Fájlnév beállítás | Az írt adatnév formátuma. Alapértelmezés szerint egy fájl/partíció formátumban `part-#####-tid-<guid>` | nem | Minta: karakterlánc <br> /Partíció: karakterlánc [] <br> Fájlnév oszlop adatként: string <br> Kimenet egyetlen fájlba: `['<fileName>']` <br> Név mappa oszlop adatként: karakterlánc | filePattern <br> partitionFileNames <br> rowUrlColumn <br> partitionFileNames <br> rowFolderUrlColumn |
| Az összes idézőjel | Az összes érték beszúrása idézőjelek közé | nem | `true` vagy `false` | quoteAll |

rowFolderUrlColumn:

### <a name="sink-example"></a>Fogadó példa

Az alábbi ábrán egy példa található egy tagolt szöveges fogadó konfigurációra az adatforgalom leképezése során.

![DelimitedText fogadó](media/data-flow/delimited-text-sink.png)

A társított adatfolyam-parancsfájl:

```
CSVSource sink(allowSchemaDrift: true,
    validateSchema: false,
    truncate: true,
    skipDuplicateMapInputs: true,
    skipDuplicateMapOutputs: true) ~> CSVSink
```

## <a name="next-steps"></a>Következő lépések

- [Másolási tevékenység – áttekintés](copy-activity-overview.md)
- [Adatfolyam hozzárendelése](concepts-data-flow-overview.md)
- [Keresési tevékenység](control-flow-lookup-activity.md)
- [GetMetadata tevékenység](control-flow-get-metadata-activity.md)
