---
title: Adatok másolása Azure blobból vagy az operációs rendszer az Azure Data Factory használatával |} A Microsoft Docs
description: Ismerje meg, hogyan másolhat adatokat a fájlrendszert, hogy támogatott fogadó adattárakba (vagy) a fájlrendszerben adattárak támogatott forrás az Azure Data Factory használatával.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 04/29/2019
ms.author: jingwang
ms.openlocfilehash: bdc3272cffb84b19bfbab8ffe61814b3f5ea8b1d
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "65228565"
---
# <a name="copy-data-to-or-from-a-file-system-by-using-azure-data-factory"></a>Adatok másolása, illetve a Windows operációs rendszer az Azure Data Factory használatával
> [!div class="op_single_selector" title1="Válassza ki a Data Factory szolgáltatás használ:"]
> * [1-es verzió](v1/data-factory-onprem-file-system-connector.md)
> * [Aktuális verzió](connector-file-system.md)

Ez a cikk ismerteti, hogyan másolhat adatokat, és a fájlrendszerhez. Azure Data Factory szolgáltatásról, olvassa el a [bevezető cikk](introduction.md).

## <a name="supported-capabilities"></a>Támogatott képességek

A fájlrendszer-összekötő a következő tevékenységek esetén támogatott:

- [Másolási tevékenység](copy-activity-overview.md) a [támogatott forrás/fogadó mátrix](copy-activity-overview.md)
- [Keresési tevékenység](control-flow-lookup-activity.md)
- [GetMetadata tevékenység](control-flow-get-metadata-activity.md)

Pontosabban a fájlrendszer-összekötő támogatja:

- Fájlok másolása a/helyi géphez vagy hálózati fájlmegosztást. Linux fájlmegosztás használatához telepítse [Samba](https://www.samba.org/) a Linux-kiszolgálón.
- Másolás a fájlok **Windows** hitelesítést.
- Másolja a fájlokat,-fájlok az elemzés/létrehozása, vagy a [támogatott fájlformátumok és tömörítési kodek](supported-file-formats-and-compression-codecs.md).

## <a name="prerequisites"></a>Előfeltételek

Másolhat adatokat egy fájlrendszer, amely nem érhető el nyilvánosan, /, akkor be kell állítania egy helyi Integration Runtime. Lásd: [helyi Integration Runtime](create-self-hosted-integration-runtime.md) részleteivel.

## <a name="getting-started"></a>Első lépések

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

A következő szakaszok segítségével határozhatók meg a Data Factory-entitások adott fájlrendszerbe-tulajdonságokkal kapcsolatos részletekért.

## <a name="linked-service-properties"></a>Társított szolgáltatás tulajdonságai

Társított fájlrendszer-szolgáltatását a következő tulajdonságok támogatottak:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A type tulajdonságot kell beállítani: **FileServer**. | Igen |
| host | Megadja a gyökér elérési útja a másolni kívánt mappa. Használja az escape-karaktert "\" a karakterláncban szereplő speciális karakterek. Lásd: [minta társított szolgáltatás és az adatkészlet-definíciók](#sample-linked-service-and-dataset-definitions) példákat. | Igen |
| userid | Adja meg a felhasználó, aki hozzáfér a server azonosítója. | Igen |
| password | Adja meg a jelszót a felhasználó (felhasználóazonosító). Ez a mező megjelölése tárolja biztonságos helyen a Data Factory, a SecureString vagy [hivatkozik az Azure Key Vaultban tárolt titkos](store-credentials-in-key-vault.md). | Igen |
| connectVia | A [Integration Runtime](concepts-integration-runtime.md) az adattárban való kapcsolódáshoz használandó. (Ha az adattár nyilvánosan hozzáférhető) használhatja a helyi Integration Runtime vagy az Azure integrációs modul. Ha nincs megadva, az alapértelmezett Azure integrációs modult használja. |Nem |

### <a name="sample-linked-service-and-dataset-definitions"></a>Példa társított szolgáltatás és az adatkészlet-definíciók

| Forgatókönyv | a társított szolgáltatás definíciójában a "host" | Az adatkészlet-definícióban "folderPath" |
|:--- |:--- |:--- |
| Az Integration Runtime gépen helyi mappa: <br/><br/>Példák: D:\\ \* vagy D:\folder\subfolder\\* |A JSON-ban: `D:\\`<br/>A felhasználói felület: `D:\` |A JSON: `.\\` vagy `folder\\subfolder`<br>A felhasználói felület: `.\` vagy `folder\subfolder` |
| Távoli megosztott mappa: <br/><br/>Példák: \\ \\myserver\\megosztása\\ \* vagy \\ \\myserver\\megosztása\\mappa\\almappa\\* |A JSON-ban: `\\\\myserver\\share`<br/>A felhasználói felület: `\\myserver\share` |A JSON: `.\\` vagy `folder\\subfolder`<br/>A felhasználói felület: `.\` vagy `folder\subfolder` |

>[!NOTE]
>Ha létrehozási felhasználói felületen keresztül, nem szükséges bemeneti dupla fordított perjelet (`\\`) karaktert, mint amikor JSON-n keresztül, adjon meg egy fordított perjel.

**Példa**

```json
{
    "name": "FileLinkedService",
    "properties": {
        "type": "FileServer",
        "typeProperties": {
            "host": "<host>",
            "userid": "<domain>\\<user>",
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

Szakaszok és adatkészletek definiálását tulajdonságainak teljes listáját lásd: a [adatkészletek](concepts-datasets-linked-services.md) cikk. 

- A **Parquet és tagolt szövegformátum**, tekintse meg [Parquet és elválasztójellel tagolt szöveges formátumban adatkészlet](#parquet-and-delimited-text-format-dataset) szakaszban.
- Más formátumú hasonló **ORC/Avro/JSON/bináris formátum**, tekintse meg [más formátumú adatkészlet](#other-format-dataset) szakaszban.

### <a name="parquet-and-delimited-text-format-dataset"></a>Parquet és elválasztójellel tagolt szöveges formátum adatkészlet

Az adatok másolásához, és a fájlrendszerből **Parquet vagy tagolt szövegformátum**, tekintse meg [Parquet formátum](format-parquet.md) és [tagolt szövegformátum](format-delimited-text.md) cikk formátum-alapú adatkészlet és támogatott beállítások. A következő tulajdonságok támogatottak a fájlrendszer `location` formátum-alapú adatkészlet beállításai:

| Tulajdonság   | Leírás                                                  | Szükséges |
| ---------- | ------------------------------------------------------------ | -------- |
| type       | A type tulajdonság alatt `location` adatkészlet értékre kell állítani **FileServerLocation**. | Igen      |
| folderPath | Mappa elérési útját. Ha azt szeretné, helyettesítő karaktert tartalmazó szűrő mappába használandó, hagyja ki ezt a beállítást, és adja meg a tevékenység – forrásbeállítások. | Nem       |
| fileName   | A fájlnév a megadott folderPath alatt. Ha szeretné használni a helyettesítő karaktert tartalmazó fájlok szűrésére, hagyja ki ezt a beállítást, és adja meg a tevékenység – forrásbeállítások. | Nem       |

> [!NOTE]
> **Fájlmegosztás** továbbra is támogatja a Parquet vagy szöveges formátum a következő szakaszban említett adatkészlet típusa – a Másolás/keresési/GetMetadata tevékenység az előző verziókkal való kompatibilitás, de nem működik az adatfolyam-leképezés. A jövőben az új modell használata javasolt, és ezek a típusok létrehozása a felhasználói felület szerzői ADF változott.

**Példa**

```json
{
    "name": "DelimitedTextDataset",
    "properties": {
        "type": "DelimitedText",
        "linkedServiceName": {
            "referenceName": "<File system linked service name>",
            "type": "LinkedServiceReference"
        },
        "schema": [ < physical schema, optional, auto retrieved during authoring > ],
        "typeProperties": {
            "location": {
                "type": "FileServerLocation",
                "folderPath": "root/folder/subfolder"
            },
            "columnDelimiter": ",",
            "quoteChar": "\"",
            "firstRowAsHeader": true,
            "compressionCodec": "gzip"
        }
    }
}
```

### <a name="other-format-dataset"></a>Más formátumú adatkészlet

Az adatok másolásához, és a fájlrendszerből **ORC/Avro/JSON/bináris formátum**, a következő tulajdonságok támogatottak:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A type tulajdonságot az adatkészlet értékre kell állítani: **FileShare** |Igen |
| folderPath | A mappa elérési útját. Helyettesítő karaktert tartalmazó szűrő támogatott, a helyettesítő karakterek engedélyezve vannak: `*` (nulla vagy több olyan karakterre illeszkedik) és `?` (megegyezik a nulla vagy önálló karakter); használata `^` elkerülésére, ha a tényleges mappanevet helyettesítő elemet vagy a escape karaktere belül. <br/><br/>Példák: a gyökérmappa/almappa /, tekintse meg a további példákat [minta társított szolgáltatás és az adatkészlet-definíciók](#sample-linked-service-and-dataset-definitions) és [példák a mappához és fájlhoz szűrők](#folder-and-file-filter-examples). |Nem |
| fileName | **Név vagy helyettesítő karaktert tartalmazó szűrő** az fájl(ok) a megadott "folderPath" alatt. Ez a tulajdonság értékét nem adja meg, ha az adatkészlet mutat a mappában lévő összes fájlt. <br/><br/>Szűrő esetén engedélyezett a helyettesítő karaktereket: `*` (nulla vagy több olyan karakterre illeszkedik) és `?` (megegyezik a nulla vagy önálló karakter).<br/>-1. példa: `"fileName": "*.csv"`<br/>– 2. példa: `"fileName": "???20180427.txt"`<br/>Használat `^` elkerülésére, ha a fájl tényleges nevét helyettesítő elemet vagy a escape karaktere belül.<br/><br/>Ha nincs megadva fájlnév egy kimeneti adatkészletet és **preserveHierarchy** nincs megadva a tevékenység fogadó, a másolási tevékenység létrehozza a fájl neve a következő mintának: "*Adatokat. [a tevékenység futtatása azonosító GUID]. [GUID Ha FlattenHierarchy]. [Ha a konfigurált formátum]. [Ha konfigurálta a tömörítés]* ", például: "Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt.gz"; tábla neve helyett lekérdezési táblázatos forrásból másol, ha van-e a névminta " *[táblanév]. [ formátum]. [Ha konfigurálta a tömörítés]* ", például: "MyTable.csv". |Nem |
| modifiedDatetimeStart | Az attribútum alapján fájlok szűrés: Utolsó módosítás. A fájlok lesz kiválasztva, ha az utolsó módosítás időpontja közötti időtartományban `modifiedDatetimeStart` és `modifiedDatetimeEnd`. Az idő UTC időzóna szerint formátumban alkalmazott "2018-12-01T05:00:00Z". <br/><br/> Ügyeljen arra, hogy általános teljesítményét adatáthelyezési érint ez a beállítás engedélyezésével, ha meg szeretné szűrő fájlok hatalmas mennyiségű fájlt. <br/><br/> A Tulajdonságok lehet olyan nem fájlszűrő attribútum alkalmazandó az adatkészletet, amely a NULL.  Amikor `modifiedDatetimeStart` dátum és idő értékkel rendelkezik, de `modifiedDatetimeEnd` má hodnotu NULL, azt jelenti, hogy a fájlokat, amelyek utolsó módosítás attribútum értéke nagyobb, mint vagy egyenlő a dátum és idő értékkel lesz kiválasztva.  Amikor `modifiedDatetimeEnd` dátum és idő értékkel rendelkezik, de `modifiedDatetimeStart` má hodnotu NULL, azt jelenti, hogy a fájlokat, amelyek utolsó módosítás attribútum értéke kisebb, mint a dátum/idő értéket fog jelölni.| Nem |
| modifiedDatetimeEnd | Az attribútum alapján fájlok szűrés: Utolsó módosítás. A fájlok lesz kiválasztva, ha az utolsó módosítás időpontja közötti időtartományban `modifiedDatetimeStart` és `modifiedDatetimeEnd`. Az idő UTC időzóna szerint formátumban alkalmazott "2018-12-01T05:00:00Z". <br/><br/> Ügyeljen arra, hogy általános teljesítményét adatáthelyezési érint ez a beállítás engedélyezésével, ha meg szeretné szűrő fájlok hatalmas mennyiségű fájlt. <br/><br/> A Tulajdonságok lehet olyan nem fájlszűrő attribútum alkalmazandó az adatkészletet, amely a NULL.  Amikor `modifiedDatetimeStart` dátum és idő értékkel rendelkezik, de `modifiedDatetimeEnd` má hodnotu NULL, azt jelenti, hogy a fájlokat, amelyek utolsó módosítás attribútum értéke nagyobb, mint vagy egyenlő a dátum és idő értékkel lesz kiválasztva.  Amikor `modifiedDatetimeEnd` dátum és idő értékkel rendelkezik, de `modifiedDatetimeStart` má hodnotu NULL, azt jelenti, hogy a fájlokat, amelyek utolsó módosítás attribútum értéke kisebb, mint a dátum/idő értéket fog jelölni.| Nem |
| format | Ha azt szeretné, hogy **, a fájlok másolása a-rendszer** közötti fájlalapú tárolók (bináris másolat), hagyja ki a format szakaszban mindkét bemeneti és kimeneti adatkészlet-definíciókban.<br/><br/>Ha szeretné elemezni, vagy hozzon létre egy adott formátumú fájlok formátuma a következő fájltípusokat támogatja: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**, **ParquetFormat**. Állítsa be a **típus** tulajdonság alatt formátumot az alábbi értékek egyikére. További információkért lásd: [szövegformátum](supported-file-formats-and-compression-codecs.md#text-format), [Json formátumban](supported-file-formats-and-compression-codecs.md#json-format), [Avro formátum](supported-file-formats-and-compression-codecs.md#avro-format), [Orc formátum](supported-file-formats-and-compression-codecs.md#orc-format), és [Parquetformátum](supported-file-formats-and-compression-codecs.md#parquet-format) szakaszokat. |Nem (csak a bináris másolás esetén) |
| compression | Adja meg a típus és az adatok tömörítési szintje. További információkért lásd: [támogatott fájlformátumok és tömörítési kodek](supported-file-formats-and-compression-codecs.md#compression-support).<br/>Támogatott típusok a következők: **A GZip**, **Deflate**, **BZip2**, és **ZipDeflate**.<br/>Támogatott szintek a következők: **Optimális** és **leggyorsabb**. |Nem |

>[!TIP]
>Másolja egy mappában található összes fájlt, adja meg a **folderPath** csak.<br>Adja meg a megadott nevű egyetlen fájl másolásához **folderPath** mappára vonatkozó részt a és **fileName** nevére.<br>Másolja a fájlokat egy mappában egy részét, adja meg a **folderPath** mappára vonatkozó részt a és **fileName** helyettesítő szűrővel.

>[!NOTE]
>Ha fájlt szűrő "fileFilter" tulajdonságot használja, továbbra is támogatott-van, miközben Ön a "fájlnevet" a jövőben hozzáadott új szűrő funkció használata javasolt.

**Példa**

```json
{
    "name": "FileSystemDataset",
    "properties": {
        "type": "FileShare",
        "linkedServiceName":{
            "referenceName": "<file system linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "folderPath": "folder/subfolder/",
            "fileName": "*",
            "modifiedDatetimeStart": "2018-12-01T05:00:00Z",
            "modifiedDatetimeEnd": "2018-12-01T06:00:00Z",
            "format": {
                "type": "TextFormat",
                "columnDelimiter": ",",
                "rowDelimiter": "\n"
            },
            "compression": {
                "type": "GZip",
                "level": "Optimal"
            }
        }
    }
}
```

## <a name="copy-activity-properties"></a>Másolási tevékenység tulajdonságai

Szakaszok és tulajdonságok definiálását tevékenységek teljes listáját lásd: a [folyamatok](concepts-pipelines-activities.md) cikk. Ez a szakasz a fájl rendszer forrásaként és fogadó által támogatott tulajdonságok listáját tartalmazza.

### <a name="file-system-as-source"></a>Fájlrendszer forrásként

- A Másolás **Parquet és tagolt szövegformátum**, tekintse meg [elválasztójellel tagolt szöveges formátum forrás- és Parquet](#parquet-and-delimited-text-format-source) szakaszban.
- A más formátumú, például a Másolás **ORC/Avro/JSON/bináris formátum**, tekintse meg [más formátumú forrás](#other-format-source) szakaszban.

#### <a name="parquet-and-delimited-text-format-source"></a>Parquet és elválasztójellel tagolt szöveges formátum forrása

Adatok másolása a fájlrendszer **Parquet vagy tagolt szövegformátum**, tekintse meg [Parquet formátum](format-parquet.md) és [tagolt szövegformátum](format-delimited-text.md) formátum-alapú másolási tevékenység forrása a cikk és a támogatott beállítás. A következő tulajdonságok támogatottak a fájlrendszer `storeSettings` formátum-alapú másolási forrásaként beállításait:

| Tulajdonság                 | Leírás                                                  | Szükséges                                      |
| ------------------------ | ------------------------------------------------------------ | --------------------------------------------- |
| type                     | A type tulajdonság alatt `storeSettings` értékre kell állítani **FileServerReadSetting**. | Igen                                           |
| recursive                | Azt jelzi, hogy az adatok olvasható rekurzív módon az almappák vagy csak a megadott mappába. Vegye figyelembe, hogy ha a rekurzív értéke igaz, és a fogadó a fájlalapú tároló, egy üres mappát vagy almappát nem másolja vagy létrehozott, a fogadó. Engedélyezett értékek a következők **igaz** (alapértelmezett), és **hamis**. | Nem                                            |
| wildcardFolderPath       | Mappa elérési útja a forrás mappák helyettesítő karakter. <br>Helyettesítő karakterek engedélyezettek: `*` (nulla vagy több olyan karakterre illeszkedik) és `?` (megegyezik a nulla vagy önálló karakter); használata `^` elkerülésére, ha a tényleges mappanevet helyettesítő elemet vagy a escape karaktere belül. <br>További példák a [példák a mappához és fájlhoz szűrők](#folder-and-file-filter-examples). | Nem                                            |
| wildcardFileName         | A szűrő forrásfájljaihoz megadott folderPath/wildcardFolderPath mellett helyettesítő karaktereket is tartalmazó fájl neve. <br>Helyettesítő karakterek engedélyezettek: `*` (nulla vagy több olyan karakterre illeszkedik) és `?` (megegyezik a nulla vagy önálló karakter); használata `^` elkerülésére, ha a tényleges mappanevet helyettesítő elemet vagy a escape karaktere belül.  További példák a [példák a mappához és fájlhoz szűrők](#folder-and-file-filter-examples). | Igen, ha `fileName` nincs megadva a következő adatkészlet |
| modifiedDatetimeStart    | Az attribútum alapján fájlok szűrés: Utolsó módosítás. A fájlok lesz kiválasztva, ha az utolsó módosítás időpontja közötti időtartományban `modifiedDatetimeStart` és `modifiedDatetimeEnd`. Az idő UTC időzóna szerint formátumban alkalmazott "2018-12-01T05:00:00Z". <br> A Tulajdonságok lehet null értékű, ami jelenti azt, hogy nincs fájlszűrő attribútum alkalmazandó az adatkészletet.  Amikor `modifiedDatetimeStart` dátum és idő értékkel rendelkezik, de `modifiedDatetimeEnd` má hodnotu NULL, azt jelenti, hogy a fájlokat, amelyek utolsó módosítás attribútum értéke nagyobb, mint vagy egyenlő a dátum és idő értékkel lesz kiválasztva.  Amikor `modifiedDatetimeEnd` dátum és idő értékkel rendelkezik, de `modifiedDatetimeStart` má hodnotu NULL, azt jelenti, hogy a fájlokat, amelyek utolsó módosítás attribútum értéke kisebb, mint a dátum/idő értéket fog jelölni. | Nem                                            |
| modifiedDatetimeEnd      | Ugyanaz, mint a fenti.                                               | Nem                                            |
| maxConcurrentConnections | A szeretne csatlakozni a storage-tároló egyidejű kapcsolatok száma. Adja meg, csak akkor, ha szeretné korlátozni a egyidejű kapcsolat az adattárba. | Nem                                            |

> [!NOTE]
> A Parquet vagy tagolt szövegformátum **FileSystemSource** továbbra is támogatott a következő szakaszban említett típusa másolási tevékenység forrása – az előző verziókkal való kompatibilitás. A jövőben az új modell használata javasolt, és ezek a típusok létrehozása a felhasználói felület szerzői ADF változott.

**Példa**

```json
"activities":[
    {
        "name": "CopyFromFileSystem",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Delimited text input dataset name>",
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
                "type": "DelimitedTextSource",
                "formatSettings":{
                    "type": "DelimitedTextReadSetting",
                    "skipLineCount": 10
                },
                "storeSettings":{
                    "type": "FileServerReadSetting",
                    "recursive": true,
                    "wildcardFolderPath": "myfolder*A",
                    "wildcardFileName": "*.csv"
                }
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

#### <a name="other-format-source"></a>Más formátumú forrás

Adatok másolása a fájlrendszer **ORC/Avro/JSON/bináris formátum**, a következő tulajdonságok támogatottak a másolási tevékenység **forrás** szakasz:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A másolási tevékenység forrása type tulajdonsága értékre kell állítani: **FileSystemSource** |Igen |
| recursive | Azt jelzi, hogy az adatok olvasható rekurzív módon az almappákból vagy csak a megadott mappába. Megjegyzés: Ha a rekurzív értéke igaz, és a fogadó fájlalapú tároló, üres mappa/alárendelt-folder nem lesz másolva vagy hozható létre, a fogadó.<br/>Engedélyezett értékek a következők: **igaz** (alapértelmezett), **false (hamis)** | Nem |
| maxConcurrentConnections | A szeretne csatlakozni a storage-tároló egyidejű kapcsolatok száma. Adja meg, csak akkor, ha szeretné korlátozni a egyidejű kapcsolat az adattárba. | Nem |

**Példa**

```json
"activities":[
    {
        "name": "CopyFromFileSystem",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<file system input dataset name>",
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
                "type": "FileSystemSource",
                "recursive": true
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="file-system-as-sink"></a>Fájlrendszer pedig a fogadó

- A Másolás **Parquet és tagolt szövegformátum**, tekintse meg [Parquet és elválasztójellel tagolt szöveges formátum fogadó](#parquet-and-delimited-text-format-sink) szakaszban.
- A más formátumú, például a Másolás **ORC/Avro/JSON/bináris formátum**, tekintse meg [más formátumú fogadó](#other-format-sink) szakaszban.

#### <a name="parquet-and-delimited-text-format-sink"></a>Parquet és elválasztójellel tagolt szöveges formátum fogadó

Adatok másolása a fájlrendszer **Parquet vagy tagolt szövegformátum**, tekintse meg [Parquet formátum](format-parquet.md) és [tagolt szövegformátum](format-delimited-text.md) formátum-alapú másolási tevékenység fogadó foglalkozó és támogatott beállítások. A következő tulajdonságok támogatottak a fájlrendszer `storeSettings` formátum-alapú másolási fogadó beállításait:

| Tulajdonság                 | Leírás                                                  | Szükséges |
| ------------------------ | ------------------------------------------------------------ | -------- |
| type                     | A type tulajdonság alatt `storeSettings` értékre kell állítani **FileServerWriteSetting**. | Igen      |
| copyBehavior             | A másolási viselkedés határozza meg, ha a forrás fájlok fájlalapú adattárból.<br/><br/>Engedélyezett értékek a következők:<br/><b>-PreserveHierarchy (alapértelmezett)</b>: Megőrzi a hierarchiája a célmappában. A forrásmappa forrás-fájl elérési útja megegyezik a célmappában a célfájl elérési útja.<br/><b>-FlattenHierarchy</b>: Minden fájl a forrásmappából szerepelnek, az első szintjét a célmappában. A cél fájlok automatikusan létrehozott névvel rendelkeznek. <br/><b>-MergeFiles</b>: Egy fájl összes fájlt a forrásmappából egyesíti. A fájl neve meg van adva, az egyesített fájlnév-e a megadott néven. Ellenkező esetben egy automatikusan létrehozott nevét. | Nem       |
| maxConcurrentConnections | Az adattár egyidejűleg csatlakozni a kapcsolatok száma. Adja meg, csak akkor, ha szeretné korlátozni a egyidejű kapcsolat az adattárba. | Nem       |

> [!NOTE]
> A Parquet vagy tagolt szövegformátum **FileSystemSink** továbbra is támogatott a következő szakaszban említett típusa másolási tevékenység fogadó-visszamenőleges kompatibilitásra. A jövőben az új modell használata javasolt, és ezek a típusok létrehozása a felhasználói felület szerzői ADF változott.

**Példa**

```json
"activities":[
    {
        "name": "CopyToFileSystem",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Parquet output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "ParquetSink",
                "storeSettings":{
                    "type": "FileServerWriteSetting",
                    "copyBehavior": "PreserveHierarchy"
                }
            }
        }
    }
]
```

#### <a name="other-format-sink"></a>Más formátumú fogadó

Adatok másolása a fájlrendszer **ORC/Avro/JSON/bináris formátum**, a következő tulajdonságok támogatottak a **fogadó** szakaszban:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A másolási tevékenység fogadó type tulajdonsága értékre kell állítani: **FileSystemSink** |Igen |
| copyBehavior | A másolási viselkedés határozza meg, ha a forrás fájlok fájlalapú adattárból.<br/><br/>Engedélyezett értékek a következők:<br/><b>-PreserveHierarchy (alapértelmezett)</b>: megőrzi a hierarchiája a célmappában. A forrásmappa forrás-fájl elérési útja megegyezik a célmappában a célfájl elérési útja.<br/><b>-FlattenHierarchy</b>: a forrásmappából a fájlok a célmappában az első szintjét is. A cél fájlok automatikusan létrehozott nevet adni. <br/><b>-MergeFiles</b>: egyesíti a forrásmappából egy fájl összes fájlt. Ha a fájl/Blob neve van megadva, az egyesített fájl neve lesz a megadott név; Ellenkező esetben lenne automatikusan létrehozott fájl nevét. | Nem |
| maxConcurrentConnections | A szeretne csatlakozni a storage-tároló egyidejű kapcsolatok száma. Adja meg, csak akkor, ha szeretné korlátozni a egyidejű kapcsolat az adattárba. | Nem |

**Példa**

```json
"activities":[
    {
        "name": "CopyToFileSystem",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<file system output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "FileSystemSink",
                "copyBehavior": "PreserveHierarchy"
            }
        }
    }
]
```

### <a name="folder-and-file-filter-examples"></a>Mappa és fájl szűrő példák

Ez a szakasz ismerteti a mappa elérési útját és nevét, az eredményül kapott viselkedéstől helyettesítő szűrőket.

| folderPath | fileName | recursive | Forrás-mappa szerkezete és szűrő eredmény (a fájlok **félkövér** adatok lekérése)|
|:--- |:--- |:--- |:--- |
| `Folder*` | (üres, használhatja az alapértelmezettet) | false | FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5.csv<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `Folder*` | (üres, használhatja az alapértelmezettet) | true | FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File4.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `Folder*` | `*.csv` | false | FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5.csv<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `Folder*` | `*.csv` | true | FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |

### <a name="recursive-and-copybehavior-examples"></a>a rekurzív és copyBehavior példák

Ez a szakasz ismerteti az eredményül kapott viselkedéstől a másolási művelet rekurzív és copyBehavior értékek különböző kombinációihoz.

| recursive | copyBehavior | Forrás mappastruktúra | Eredményül kapott cél |
|:--- |:--- |:--- |:--- |
| true |preserveHierarchy | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | a célmappában mappa1 szerkezete ugyanaz, mint a forrás hozzon létre:<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5. |
| true |flattenHierarchy | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | A cél mappa1 jön létre az alábbi struktúra használatával: <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Automatikusan létrehozott nevet a file1 kiszolgálón<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatikusan létrehozott nevet File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatikusan létrehozott nevet fájl3<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatikusan létrehozott nevet File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatikusan létrehozott nevet File5 |
| true |mergeFiles | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | A cél mappa1 jön létre az alábbi struktúra használatával: <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1 + File2 + fájl3 + File4 + 5 fájl tartalmát egy fájlba a fájl automatikusan létrehozott névvel egyesítésekor |
| false |preserveHierarchy | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | Az alábbi struktúra használatával jön létre a célmappában mappa1<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/><br/>Fájl3, File4 és File5 Subfolder1 a rendszer nem követi. |
| false |flattenHierarchy | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | Az alábbi struktúra használatával jön létre a célmappában mappa1<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Automatikusan létrehozott nevet a file1 kiszolgálón<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatikusan létrehozott nevet File2<br/><br/>Fájl3, File4 és File5 Subfolder1 a rendszer nem követi. |
| false |mergeFiles | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | Az alábbi struktúra használatával jön létre a célmappában mappa1<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1 + 2. fájl tartalmát egy fájlba a fájl automatikusan létrehozott névvel összefésülése megtörténjen. Automatikusan létrehozott nevet a file1 kiszolgálón<br/><br/>Fájl3, File4 és File5 Subfolder1 a rendszer nem követi. |

## <a name="next-steps"></a>További lépések
A másolási tevékenység az Azure Data Factory által forrásként és fogadóként támogatott adattárak listáját lásd: [támogatott adattárak](copy-activity-overview.md##supported-data-stores-and-formats).
