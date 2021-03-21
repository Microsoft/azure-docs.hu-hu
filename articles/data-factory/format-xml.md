---
title: XML-formátum a Azure Data Factoryban
description: Ez a témakör a Azure Data Factory XML-formátumának kezelését ismerteti.
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.date: 10/29/2020
ms.author: jingwang
ms.openlocfilehash: a4082e1e00ecb1d3b0157fcc35616704eaab0aae
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "100386356"
---
# <a name="xml-format-in-azure-data-factory"></a>XML-formátum a Azure Data Factoryban

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Ha szeretné **elemezni az XML-fájlokat**, kövesse ezt a cikket. 

Az XML formátum a következő összekötők esetében támogatott: [Amazon S3](connector-amazon-simple-storage-service.md), [Azure Blob](connector-azure-blob-storage.md), [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md), [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md), [Azure file Storage](connector-azure-file-storage.md), [fájlrendszer](connector-file-system.md), [FTP](connector-ftp.md), [Google Cloud Storage](connector-google-cloud-storage.md), [HDFS](connector-hdfs.md), [http](connector-http.md)és [SFTP](connector-sftp.md). Forrásként, de nem fogadóként támogatott.

## <a name="dataset-properties"></a>Adatkészlet tulajdonságai

Az adatkészletek definiálásához rendelkezésre álló csoportok és tulajdonságok teljes listáját az [adatkészletek](concepts-datasets-linked-services.md) című cikkben találja. Ez a szakasz az XML-adatkészlet által támogatott tulajdonságok listáját tartalmazza.

| Tulajdonság         | Leírás                                                  | Kötelező |
| ---------------- | ------------------------------------------------------------ | -------- |
| típus             | Az adatkészlet Type tulajdonságát **XML-** re kell állítani. | Yes      |
| location         | A fájl (ok) helyének beállításai. Minden fájl alapú összekötőhöz tartozik a saját hely típusa és a támogatott tulajdonságai `location` . **Tekintse meg a részleteket az összekötő cikk-> adatkészlet tulajdonságai szakaszban**. | Yes      |
| encodingName     | A tesztelési fájlok olvasására/írására szolgáló kódolási típus. <br>Az engedélyezett értékek a következők: "UTF-8", "UTF-16", "UTF-16BE", "UTF-32", "UTF-32BE", "US-ASCII", "UTF-7", "BIG5", "EUC-JP", "EUC-KR", "GB2312", "GB18030", "JOHAB", "SHIFT-JIS", "CP875", "CP866", "IBM00858", "IBM037", "IBM273", "IBM437", "IBM500", "IBM737", "IBM775", "IBM850", "IBM852", "IBM855", "IBM857", "IBM860", "IBM861", "IBM863", "IBM864", "IBM865", "IBM869", "IBM870", "IBM01140", "IBM01141", "IBM01142", "IBM01143", "IBM01144", "IBM01145", "IBM01146", "IBM01147", "IBM01148", "IBM01149", "", "ISO-2022-JP", "ISO-2022-KR", "ISO-8859-1", "ISO-8859-2", "ISO-8859-3", "ISO-8859-4", "ISO-8859-5", "ISO-8859-6", "ISO-8859-7", "ISO-8859-8", "ISO-8859-9", "ISO-8859-13" "ISO-8859-15", "WINDOWS-874", "WINDOWS-1250", "WINDOWS-1251", "WINDOWS-1252", "WINDOWS-1253", "WINDOWS-1254", "WINDOWS-1255", "WINDOWS-1256", "WINDOWS-1257", "WINDOWS-1258".| No       |
| nullValue | Megadja a null értékű karakterlánc-ábrázolást.<br/>Az alapértelmezett érték **üres karakterlánc**. | No |
| tömörítés | A fájltömörítés konfigurálására szolgáló tulajdonságok csoportja. Akkor konfigurálja ezt a szakaszt, ha a tevékenység végrehajtása során tömörítést vagy kibontást szeretne végezni. | No |
| típus<br>(*alatt `compression`*) | Az XML-fájlok olvasásához/írásához használt tömörítési kodek. <br>Az engedélyezett értékek a **bzip2**, a **gzip**, a **deflate**, a **ZipDeflate**, a **TarGzip**, a **tar**, a **Snappy** vagy a **lz4**. Az alapértelmezett érték nincs tömörítve.<br>**Megjegyzés:** jelenleg a másolási tevékenység nem támogatja a "snappy" & "lz4", és a leképezési folyamat nem támogatja a "ZipDeflate", a "TarGzip" és a "tar" típust.<br>**Megjegyzés** : Ha a másolási tevékenységgel kibontja a **ZipDeflate** / **TarGzip** / **tar** -fájl (oka) t, és az írás a fájl alapú fogadó adattárba történik, az alapértelmezett fájlok a mappába kerülnek, a `<path specified in dataset>/<folder named as source compressed file>/` `preserveZipFileNameAsFolder` / `preserveCompressionFileNameAsFolder` [másolási tevékenység forrásaként](#xml-as-source) pedig megadhatja, hogy meg kell-e őrizni a tömörített fájl (ok) nevét a mappa szerkezeteként. | Nem.  |
| szint<br/>(*alatt `compression`*) | A tömörítési arány. <br>Az engedélyezett értékek az **optimálisak** vagy a **leggyorsabbek**.<br>- **Leggyorsabb:** A tömörítési műveletnek a lehető leggyorsabban kell elvégeznie, még akkor is, ha az eredményül kapott fájl nem tömöríthető optimálisan.<br>- **Optimális**: a tömörítési műveletet optimálisan kell tömöríteni, még akkor is, ha a művelet végrehajtása hosszú időt vesz igénybe. További információ: [tömörítési szint](/dotnet/api/system.io.compression.compressionlevel) témakör. | No       |

Az alábbi példa az Azure-beli XML-adatkészletet mutatja be Blob Storage:

```json
{
    "name": "XMLDataset",
    "properties": {
        "type": "Xml",
        "linkedServiceName": {
            "referenceName": "<Azure Blob Storage linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "location": {
                "type": "AzureBlobStorageLocation",
                "container": "containername",
                "folderPath": "folder/subfolder",
            },
            "compression": {
                "type": "ZipDeflate"
            }
        }
    }
}
```

## <a name="copy-activity-properties"></a>Másolási tevékenység tulajdonságai

A tevékenységek definiálásához elérhető csoportok és tulajdonságok teljes listáját a [folyamatok](concepts-pipelines-activities.md) című cikkben találja. Ez a szakasz az XML-forrás által támogatott tulajdonságok listáját tartalmazza.

Ismerje meg, hogyan képezhető le az XML-adatok és a fogadó adattára/formátuma a [séma-hozzárendelésből](copy-activity-schema-and-type-mapping.md). Az XML-fájlok előzetes megtekintésekor a JSON-hierarchiával megjelenő adat jelenik meg, és a JSON-útvonal használatával a mezőkre mutat.

### <a name="xml-as-source"></a>XML forrásként

A másolási tevékenység ***\* forrása \**** szakaszban a következő tulajdonságok támogatottak. További információ az [XML-összekötő viselkedéséről](#xml-connector-behavior).

| Tulajdonság      | Leírás                                                  | Kötelező |
| ------------- | ------------------------------------------------------------ | -------- |
| típus          | A másolási tevékenység forrásának Type tulajdonságát **XmlSource** értékre kell állítani. | Yes      |
| formatSettings | Tulajdonságok csoportja. Tekintse meg az alábbi **XML-olvasási beállítások** táblázatot. | No       |
| storeSettings | Az adattárakból származó adatok beolvasására szolgáló tulajdonságok csoportja. A fájl alapú összekötők a saját támogatott olvasási beállításaival rendelkeznek `storeSettings` . **Tekintse meg a részleteket az összekötőről szóló cikk – > másolási tevékenység tulajdonságai szakaszban**. | No       |

Támogatott **XML-olvasási beállítások** a következő alatt `formatSettings` :

| Tulajdonság      | Leírás                                                  | Kötelező |
| ------------- | ------------------------------------------------------------ | -------- |
| típus          | A formatSettings típusát **XmlReadSettings** értékre kell beállítani. | Yes      |
| validationMode | Megadja, hogy az XML-séma érvényesíthető-e.<br>Az engedélyezett értékek a következők: **none** (alapértelmezett, nincs érvényesítés), **XSD** (ellenőrzés az XSD használatával), **DTD** (ellenőrzés a DTD használatával). | No |
| névterek | Azt határozza meg, hogy a névtér engedélyezhető-e az XML-fájlok elemzésekor. Az engedélyezett értékek: **true** (alapértelmezett), **false**. | No |
| namespacePrefixes | Névtér URI-ja az előtag-megfeleltetéshez, amely az XML-fájl elemzésekor használt mezők elnevezésére szolgál.<br/>Ha egy XML-fájl névteret tartalmaz, és a névtér engedélyezve van, alapértelmezés szerint a mezőnév ugyanaz, mint az XML-dokumentumban.<br>Ha a névtér URI-ja számára van definiálva elem ebben a térképen, a mező neve: `prefix:fieldName` . | No |
| detectDataType | Azt határozza meg, hogy az egész, dupla és logikai adattípusokat kell-e felderíteni. Az engedélyezett értékek: **true** (alapértelmezett), **false**.| No |
| compressionProperties | Egy adott tömörítési kodekhez tartozó adatok kibontására szolgáló tulajdonságok csoportja. | No       |
| preserveZipFileNameAsFolder<br>(*a `compressionProperties` -ben -> ) `type` `ZipDeflateReadSettings`*  | Akkor érvényes, ha a bemeneti adatkészlet **ZipDeflate** tömörítéssel van konfigurálva. Azt jelzi, hogy a forrás zip-fájlnevet a másolás során a mappa szerkezeteként kell-e megőrizni.<br>– Ha **true (alapértelmezett)** értékre van állítva, Data Factory a kibontott fájlokat ír a következőre: `<path specified in dataset>/<folder named as source zip file>/` .<br>– Ha **hamis** értékre van állítva, Data Factory a kibontott fájlokat közvetlenül a következőre írja: `<path specified in dataset>` . A versenyzés vagy a váratlan viselkedés elkerülése érdekében ügyeljen arra, hogy ne legyenek duplikált fájlnevek különböző zip-fájlokban.  | No |
| preserveCompressionFileNameAsFolder<br>(*a `compressionProperties` -> `type` `TarGZipReadSettings` -ban `TarReadSettings` vagy a*-ben) | Akkor érvényes, ha a bemeneti adatkészlet **TarGzip** / **tar** tömörítéssel van konfigurálva. Azt jelzi, hogy a forrás tömörített fájlnevet a másolás során a mappa szerkezeteként kell-e megőrizni.<br>– Ha **true (alapértelmezett)** értékre van állítva, Data Factory a kibontott fájlokat ír a következőre: `<path specified in dataset>/<folder named as source compressed file>/` . <br>-Ha **hamis** értékre van állítva, Data Factory a kibontott fájlokat közvetlenül a következőre írja: `<path specified in dataset>` . A versenyzés vagy a váratlan viselkedés elkerülése érdekében ügyeljen arra, hogy ne legyenek duplikált fájlnevek a különböző forrásfájlok között. | No |

## <a name="mapping-data-flow-properties"></a>Adatfolyam-tulajdonságok leképezése

Az adatfolyamatok leképezése során a következő adattárakban olvasható és írható XML-formátum: [Azure Blob Storage](connector-azure-blob-storage.md#mapping-data-flow-properties), [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md#mapping-data-flow-properties)és [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md#mapping-data-flow-properties). Az XML-fájlokra XML-adatkészletek vagy [beágyazott adatkészletek](data-flow-source.md#inline-datasets)használatával is rámutathat.

### <a name="source-properties"></a>Forrás tulajdonságai

Az alábbi táblázatban az XML-forrás által támogatott tulajdonságok szerepelnek. Ezeket a tulajdonságokat a **forrás beállításai** lapon módosíthatja. További információ az [XML-összekötő viselkedéséről](#xml-connector-behavior). A beágyazott adatkészletek használatakor további beállításokat fog látni, amelyek megegyeznek az [adatkészlet tulajdonságai](#dataset-properties) szakaszban leírt tulajdonságokkal. 

| Név | Leírás | Kötelező | Megengedett értékek | Adatfolyam-parancsfájl tulajdonsága |
| ---- | ----------- | -------- | -------------- | ---------------- |
| Wild kártya elérési útjai | A rendszer feldolgozza a helyettesítő karakteres elérési úttal egyező összes fájlt. Felülbírálja az adatkészletben beállított mappát és a fájl elérési útját. | No | Karakterlánc [] | wildcardPaths |
| Partíció gyökerének elérési útja | A particionált fájlok esetében megadhatja a partíció gyökerének elérési útját, hogy a particionált mappák oszlopként legyenek olvashatók. | Nem | Sztring | partitionRootPath |
| Fájlok listája | Azt jelzi, hogy a forrás egy szövegfájlra mutat-e, amely a feldolgozandó fájlokat listázza | No | `true` vagy `false` | fileList |
| A fájl nevét tároló oszlop | Új oszlop létrehozása a forrásfájl nevével és elérési útjával | Nem | Sztring | rowUrlColumn |
| Befejezés után | A fájlok törlése vagy áthelyezése a feldolgozás után. A fájl elérési útja a tároló gyökeréből indul el | No | Törlés: `true` vagy `false` <br> Áthelyezése `['<from>', '<to>']` | purgeFiles <br>moveFiles |
| Szűrés utoljára módosítva | Válassza ki a fájlok szűrését az utolsó módosításuk alapján | No | Időbélyeg | modifiedAfter <br>modifiedBefore |
| Ellenőrzési mód | Megadja, hogy az XML-séma érvényesíthető-e. | No | `None` (alapértelmezés szerint nincs érvényesítés)<br>`xsd` (ellenőrzés az XSD használatával)<br>`dtd` (ellenőrzés a DTD használatával). | validationMode |
| Névterek | Azt határozza meg, hogy a névtér engedélyezhető-e az XML-fájlok elemzésekor. | No | `true` (alapértelmezett) vagy `false` | névterek |
| Névtér-előtag párok | Névtér URI-ja az előtag-megfeleltetéshez, amely az XML-fájl elemzésekor használt mezők elnevezésére szolgál.<br/>Ha egy XML-fájl névteret tartalmaz, és a névtér engedélyezve van, alapértelmezés szerint a mezőnév ugyanaz, mint az XML-dokumentumban.<br>Ha a névtér URI-ja számára van definiálva elem ebben a térképen, a mező neve: `prefix:fieldName` . | No | Tömb mintázattal`['URI1'->'prefix1','URI2'->'prefix2']` | namespacePrefixes |
| Nem található fájlok engedélyezése | Ha az értéke igaz, a rendszer nem dobja el a hibát, ha nem található fájl | nem | `true` vagy `false` | ignoreNoFilesFound |

### <a name="xml-source-script-example"></a>Példa XML-forrás parancsfájlra

Az alábbi szkript egy XML-forrás konfigurációjának példája, amely az adatfolyamatok adatkészlet mód használatával történő leképezésére szolgál.

```
source(allowSchemaDrift: true,
    validateSchema: false,
    validationMode: 'xsd',
    namespaces: true) ~> XMLSource
```

Az alábbi szkript egy XML-forrás konfigurációját mutatja be beágyazott adatkészlet módban.

```
source(allowSchemaDrift: true,
    validateSchema: false,
    format: 'xml',
    fileSystem: 'filesystem',
    folderPath: 'folder',
    validationMode: 'xsd',
    namespaces: true) ~> XMLSource
```

## <a name="xml-connector-behavior"></a>XML-összekötő viselkedése

Az XML forrásként való használatakor vegye figyelembe a következőket.

- XML-attribútumok:

    - Egy elem attribútumai a hierarchia elemének almezőiként lesznek értelmezve.
    - Az attribútum mező neve a mintát követi `@attributeName` .

- XML-séma ellenőrzése:

    - Dönthet úgy, hogy nem érvényesíti a sémát, illetve nem ellenőrzi a sémát az XSD vagy a DTD használatával.
    - Ha XSD-vagy DTD-t használ az XML-fájlok érvényesítéséhez, az XSD/DTD-t az XML-fájlokon belül relatív elérési úttal kell megadnia.

- Névtér-kezelő:

    - A névtér letiltható az adatfolyam használatakor, ebben az esetben a névteret definiáló attribútumok normál attribútumokként lesznek értelmezve.
    - Ha a névtér engedélyezve van, az elem és az attribútumok nevei követik a mintát       `namespaceUri,elementName` és `namespaceUri,@attributeName` alapértelmezés szerint. Megadhatja az egyes névtér-URI-azonosítókat a forrásban, amely esetben az elem és az attribútumok neve követi a mintát `definedPrefix:elementName` vagy `definedPrefix:@attributeName` ehelyett.

- Érték oszlop:

    - Ha egy XML-elemben egyszerű szöveges érték és attribútumok/gyermek elemek is szerepelnek, az egyszerű szöveges érték a beépített mezőnév értékkel lesz értelmezve `_value_` . És az elem névterét is örökli, ha érvényes.

## <a name="next-steps"></a>Következő lépések

- [Másolási tevékenység – áttekintés](copy-activity-overview.md)
- [Adatfolyam hozzárendelése](concepts-data-flow-overview.md)
- [Keresési tevékenység](control-flow-lookup-activity.md)
- [GetMetadata tevékenység](control-flow-get-metadata-activity.md)