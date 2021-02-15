---
title: Excel-formátum Azure Data Factory
description: Ez a témakör a Azure Data Factory Excel-formátumának kezelését ismerteti.
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.date: 12/08/2020
ms.author: jingwang
ms.openlocfilehash: bef29bc958253be0498442f842dda67105ce799b
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/14/2021
ms.locfileid: "100386526"
---
# <a name="excel-format-in-azure-data-factory"></a>Excel-formátum Azure Data Factory
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Ha szeretné **elemezni az Excel-fájlokat**, kövesse ezt a cikket. A Azure Data Factory támogatja a ". xls" és az ". xlsx" is.

Az Excel formátum a következő összekötők esetében támogatott: [Amazon S3](connector-amazon-simple-storage-service.md), [Azure Blob](connector-azure-blob-storage.md), [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md), [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md), [Azure file Storage](connector-azure-file-storage.md), [fájlrendszer](connector-file-system.md), [FTP](connector-ftp.md), [Google Cloud Storage](connector-google-cloud-storage.md), [HDFS](connector-hdfs.md), [http](connector-http.md)és [SFTP](connector-sftp.md). Forrásként, de nem fogadóként támogatott. 

**Megjegyzés**: a ". xls" formátum nem támogatott a [http](connector-http.md)használata közben. 

## <a name="dataset-properties"></a>Adatkészlet tulajdonságai

Az adatkészletek definiálásához rendelkezésre álló csoportok és tulajdonságok teljes listáját az [adatkészletek](concepts-datasets-linked-services.md) című cikkben találja. Ez a szakasz az Excel-adatkészlet által támogatott tulajdonságok listáját tartalmazza.

| Tulajdonság         | Leírás                                                  | Kötelező |
| ---------------- | ------------------------------------------------------------ | -------- |
| típus             | Az adatkészlet Type tulajdonságát az **Excel** értékre kell beállítani.   | Yes      |
| location         | A fájl (ok) helyének beállításai. Minden fájl alapú összekötőhöz tartozik a saját hely típusa és a támogatott tulajdonságai `location` . | Yes      |
| SheetName tulajdonságbeállító        | Az Excel-munkalap neve az adatolvasáshoz.                       | Itt adhatja meg `sheetName` vagy `sheetIndex` |
| sheetIndex | Az Excel-munkalap indexe az adatok olvasására, 0-tól kezdődően. | Itt adhatja meg `sheetName` vagy `sheetIndex` |
| tartomány            | Az adott munkalapon lévő cellatartomány a szelektív adat megtalálásához, például:<br>-Nincs megadva: beolvassa a teljes munkalapot táblaként az első nem üres sorból és oszlopból.<br>- `A3`: az adott cellától kezdődő táblázat beolvasása, amely dinamikusan észleli az alábbi sorokat és az összes oszlopot a jobb oldalon.<br>- `A3:H5`: a rögzített tartomány beolvasása táblázatként<br>- `A3:A3`: az egyetlen cella beolvasása | No       |
| firstRowAsHeader | Meghatározza, hogy az adott munkalap vagy tartomány első sorát fejlécként kell-e kezelni az oszlopok neveivel.<br>Az engedélyezett értékek: **true** és **false** (alapértelmezett). | No       |
| nullValue        | Megadja a null értékű karakterlánc-ábrázolást. <br>Az alapértelmezett érték **üres karakterlánc**. | No       |
| tömörítés | A fájltömörítés konfigurálására szolgáló tulajdonságok csoportja. Akkor konfigurálja ezt a szakaszt, ha a tevékenység végrehajtása során tömörítést vagy kibontást szeretne végezni. | No |
| típus<br/>(*alatt `compression`*) | A JSON-fájlok olvasásához/írásához használt tömörítési kodek. <br>Az engedélyezett értékek a **bzip2**, a **gzip**, a **deflate**, a **ZipDeflate**, a **TarGzip**, a **tar**, a **Snappy** vagy a **lz4**. Az alapértelmezett érték nincs tömörítve.<br>**Megjegyzés:** jelenleg a másolási tevékenység nem támogatja a "snappy" & "lz4", és a leképezési folyamat nem támogatja a "ZipDeflate", a "TarGzip" és a "tar" típust.<br>**Vegye figyelembe** , hogy ha másolási tevékenységet használ a **ZipDeflate** -fájl (ok) kibontásához és a fájl alapú fogadó adattárba való íráshoz, a fájlok kikerülnek a mappába: `<path specified in dataset>/<folder named as source zip file>/` . | Nem.  |
| szint<br/>(*alatt `compression`*) | A tömörítési arány. <br>Az engedélyezett értékek az **optimálisak** vagy a **leggyorsabbek**.<br>- **Leggyorsabb:** A tömörítési műveletnek a lehető leggyorsabban kell elvégeznie, még akkor is, ha az eredményül kapott fájl nem tömöríthető optimálisan.<br>- **Optimális**: a tömörítési műveletet optimálisan kell tömöríteni, még akkor is, ha a művelet végrehajtása hosszú időt vesz igénybe. További információ: [tömörítési szint](/dotnet/api/system.io.compression.compressionlevel) témakör. | No       |

Az alábbi példa az Azure Blob Storage Excel-adatkészletet mutatja be:

```json
{
    "name": "ExcelDataset",
    "properties": {
        "type": "Excel",
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
            "sheetName": "MyWorksheet",
            "range": "A3:H5",
            "firstRowAsHeader": true
        }
    }
}
```

## <a name="copy-activity-properties"></a>Másolási tevékenység tulajdonságai

A tevékenységek definiálásához elérhető csoportok és tulajdonságok teljes listáját a [folyamatok](concepts-pipelines-activities.md) című cikkben találja. Ez a szakasz az Excel-forrás által támogatott tulajdonságok listáját tartalmazza.

### <a name="excel-as-source"></a>Excel forrásként 

A másolási tevékenység ***\* forrása \**** szakaszban a következő tulajdonságok támogatottak.

| Tulajdonság      | Leírás                                                  | Kötelező |
| ------------- | ------------------------------------------------------------ | -------- |
| típus          | A másolási tevékenység forrásának Type tulajdonságát **ExcelSource** értékre kell állítani. | Yes      |
| storeSettings | Az adattárakból származó adatok beolvasására szolgáló tulajdonságok csoportja. A fájl alapú összekötők a saját támogatott olvasási beállításaival rendelkeznek `storeSettings` . | No       |

```json
"activities": [
    {
        "name": "CopyFromExcel",
        "type": "Copy",
        "typeProperties": {
            "source": {
                "type": "ExcelSource",
                "storeSettings": {
                    "type": "AzureBlobStorageReadSettings",
                    "recursive": true
                }
            },
            ...
        }
        ...
    }
]
```

## <a name="mapping-data-flow-properties"></a>Adatfolyam-tulajdonságok leképezése

Az adatfolyamatok leképezése során a következő adattárakban olvashat Excel-formátumot: [Azure Blob Storage](connector-azure-blob-storage.md#mapping-data-flow-properties), [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md#mapping-data-flow-properties)és [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md#mapping-data-flow-properties). Az Excel-fájlok az Excel-adatkészletek használatával vagy egy [beágyazott adatkészletből](data-flow-source.md#inline-datasets)is kihelyezhetők.

### <a name="source-properties"></a>Forrás tulajdonságai

Az alábbi táblázatban az Excel-források által támogatott tulajdonságok szerepelnek. Ezeket a tulajdonságokat a **forrás beállításai** lapon módosíthatja. A beágyazott adatkészletek használatakor további beállításokat fog látni, amelyek megegyeznek az [adatkészlet tulajdonságai](#dataset-properties) szakaszban leírt tulajdonságokkal.

| Név                      | Leírás                                                  | Kötelező | Megengedett értékek                                            | Adatfolyam-parancsfájl tulajdonsága         |
| ------------------------- | ------------------------------------------------------------ | -------- | --------------------------------------------------------- | --------------------------------- |
| Wild kártya elérési útjai           | A rendszer feldolgozza a helyettesítő karakteres elérési úttal egyező összes fájlt. Felülbírálja az adatkészletben beállított mappát és a fájl elérési útját. | nem       | Karakterlánc []                                                  | wildcardPaths                     |
| Partíció gyökerének elérési útja       | A particionált fájlok esetében megadhatja a partíció gyökerének elérési útját, hogy a particionált mappák oszlopként legyenek olvashatók. | nem       | Sztring                                                    | partitionRootPath                 |
| Fájlok listája             | Azt jelzi, hogy a forrás egy szövegfájlra mutat-e, amely a feldolgozandó fájlokat listázza | nem       | `true` vagy `false`                                         | fileList                          |
| A fájl nevét tároló oszlop | Új oszlop létrehozása a forrásfájl nevével és elérési útjával       | nem       | Sztring                                                    | rowUrlColumn                      |
| Befejezés után          | A fájlok törlése vagy áthelyezése a feldolgozás után. A fájl elérési útja a tároló gyökeréből indul el | nem       | Törlés: `true` vagy `false` <br> Áthelyezése `['<from>', '<to>']` | purgeFiles <br> moveFiles         |
| Szűrés utoljára módosítva   | Válassza ki a fájlok szűrését az utolsó módosításuk alapján | nem       | Időbélyeg                                                 | modifiedAfter <br> modifiedBefore |
| Nem található fájlok engedélyezése | Ha az értéke igaz, a rendszer nem dobja el a hibát, ha nem található fájl | nem | `true` vagy `false` | ignoreNoFilesFound |

### <a name="source-example"></a>Forrás példa

Az alábbi képen egy példa látható egy Excel-forrás konfigurációra az adatfolyamatok adatkészlet mód használatával történő leképezéséhez.

![Excel-forrás](media/data-flow/excel-source.png)

A társított adatfolyam-parancsfájl:

```
source(allowSchemaDrift: true,
    validateSchema: false,
    wildcardPaths:['*.xls']) ~> ExcelSource
```

Ha beágyazott adatkészletet használ, a következő forrás-beállítási lehetőségek jelennek meg a leképezési adatforgalomban.

![Excel-forrás beágyazott adatkészlete](media/data-flow/excel-source-inline-dataset.png)

A társított adatfolyam-parancsfájl:

```
source(allowSchemaDrift: true,
    validateSchema: false,
    format: 'excel',
    fileSystem: 'container',
    folderPath: 'path',
    fileName: 'sample.xls',
    sheetName: 'worksheet',
    firstRowAsHeader: true) ~> ExcelSourceInlineDataset
```

## <a name="next-steps"></a>Következő lépések

- [Másolási tevékenység – áttekintés](copy-activity-overview.md)
- [Keresési tevékenység](control-flow-lookup-activity.md)
- [GetMetadata tevékenység](control-flow-get-metadata-activity.md)