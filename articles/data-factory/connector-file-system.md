---
title: Adatok másolása a fájlrendszerből a Azure Data Factory használatával
description: Megtudhatja, hogyan másolhat a fájlrendszerből származó adatokból támogatott fogadó adattárakba (vagy) a támogatott forrás-adattárakból a fájlrendszerbe Azure Data Factory használatával.
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.date: 03/29/2021
ms.author: jingwang
ms.openlocfilehash: c49b543b13dddf4c4ba7e36196795c6a0d638ae2
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "105731955"
---
# <a name="copy-data-to-or-from-a-file-system-by-using-azure-data-factory"></a>Adatok másolása fájlrendszerből vagy rendszerbe a Azure Data Factory használatával
> [!div class="op_single_selector" title1="Válassza ki az Ön által használt Data Factory-szolgáltatás verzióját:"]
> * [1-es verzió](v1/data-factory-onprem-file-system-connector.md)
> * [Aktuális verzió](connector-file-system.md)
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Ez a cikk azt ismerteti, hogyan másolhatók az adatok a fájlrendszerbe. A Azure Data Factoryről a [bevezető cikkben](introduction.md)olvashat bővebben.

## <a name="supported-capabilities"></a>Támogatott képességek

Ez a fájlrendszer-összekötő a következő tevékenységek esetén támogatott:

- [Másolási tevékenység](copy-activity-overview.md) [támogatott forrás/fogadó mátrixtal](copy-activity-overview.md)
- [Keresési tevékenység](control-flow-lookup-activity.md)
- [GetMetadata tevékenység](control-flow-get-metadata-activity.md)
- [Tevékenység törlése](delete-activity.md)

A fájlrendszer-összekötő különösen a következőket támogatja:

- Fájlok másolása helyi gépre vagy hálózati fájlmegosztásba. Linux-fájlmegosztás használatához telepítse a [Samba](https://www.samba.org/) -t a Linux-kiszolgálóra.
- Fájlok másolása **Windows** -hitelesítéssel.
- Fájlok másolása a-ként vagy a fájlok elemzése/létrehozása a [támogatott fájlformátumokkal és tömörítési kodekekkel](supported-file-formats-and-compression-codecs.md).

> [!NOTE]
> A csatlakoztatott hálózati meghajtó nem támogatott hálózati fájlmegosztás adatainak betöltésekor. Használja inkább a tényleges elérési utat, például ` \\server\share` :.

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

## <a name="getting-started"></a>Első lépések

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

A következő szakaszokban részletesen ismertetjük azokat a tulajdonságokat, amelyek a fájlrendszerre vonatkozó Data Factory-entitások definiálására szolgálnak.

## <a name="linked-service-properties"></a>Társított szolgáltatás tulajdonságai

A fájlrendszerhez társított szolgáltatás a következő tulajdonságokat támogatja:

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| típus | A Type tulajdonságot a következőre kell beállítani: **fileserver**. | Yes |
| gazda | Megadja a másolni kívánt mappa gyökerének elérési útját. A "Escape" karakter használata a \" karakterlánc speciális karaktereinek számára. Példákat a következő témakörben talál: példa [társított szolgáltatás és adatkészlet-definíciók](#sample-linked-service-and-dataset-definitions) . | Yes |
| userId | Itt adhatja meg annak a felhasználónak az AZONOSÍTÓját, aki hozzáfér a kiszolgálóhoz. | Yes |
| jelszó | A felhasználó jelszavának megadása (userId). Megjelöli ezt a mezőt SecureString, hogy biztonságosan tárolja Data Factoryban, vagy [hivatkozjon a Azure Key Vault tárolt titkos kulcsra](store-credentials-in-key-vault.md). | Yes |
| Connectvia tulajdonsággal | Az adattárhoz való kapcsolódáshoz használt [Integration Runtime](concepts-integration-runtime.md) . További tudnivalók az [Előfeltételek](#prerequisites) szakaszban olvashatók. Ha nincs megadva, az alapértelmezett Azure Integration Runtime használja. |No |

### <a name="sample-linked-service-and-dataset-definitions"></a>Példa társított szolgáltatás és adatkészlet-definíciók

| Eset | "host" a társított szolgáltatás definíciójában | "folderPath" az adatkészlet definíciójában |
|:--- |:--- |:--- |
| Helyi mappa Integration Runtime gépen: <br/><br/>Példák: D: \\ \* vagy D:\folder\subfolder\\* |JSON-ban: `D:\\`<br/>Felhasználói felületen: `D:\` |A JSON-ban: `.\\` vagy `folder\\subfolder`<br>Felhasználói felületen: `.\` vagy `folder\subfolder` |
| Távoli megosztott mappa: <br/><br/>Példák: \\ \\ MyServer \\ Share \\ \* vagy \\ \\ MyServer \\ Share \\ mappa \\ almappája\\* |JSON-ban: `\\\\myserver\\share`<br/>Felhasználói felületen: `\\myserver\share` |A JSON-ban: `.\\` vagy `folder\\subfolder`<br/>Felhasználói felületen: `.\` vagy `folder\subfolder` |

>[!NOTE]
>Felhasználói felületen keresztüli szerzői műveletek esetén nem szükséges dupla fordított perjelet () megadnia, `\\` mint a JSON-n keresztül, adjon meg egy fordított perjelet.

**Példa**

```json
{
    "name": "FileLinkedService",
    "properties": {
        "type": "FileServer",
        "typeProperties": {
            "host": "<host>",
            "userId": "<domain>\\<user>",
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

Az adatkészletek definiálásához rendelkezésre álló csoportok és tulajdonságok teljes listáját az [adatkészletek](concepts-datasets-linked-services.md) című cikkben találja. 

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

A fájlrendszer a következő tulajdonságokat támogatja a `location` Format-alapú adatkészlet beállítások területén:

| Tulajdonság   | Leírás                                                  | Kötelező |
| ---------- | ------------------------------------------------------------ | -------- |
| típus       | Az `location` adatkészletben található Type tulajdonságot **FileServerLocation** értékre kell állítani. | Yes      |
| folderPath | A mappa elérési útja. Ha a mappa szűréséhez helyettesítő karaktert szeretne használni, hagyja ki ezt a beállítást, és a tevékenység forrásának beállításai között válassza a lehetőséget. | No       |
| fileName   | A fájlnév a megadott folderPath alatt. Ha helyettesítő karaktereket szeretne használni a fájlok szűréséhez, hagyja ki ezt a beállítást, és a tevékenység forrásának beállításai között válassza a lehetőséget. | No       |

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

## <a name="copy-activity-properties"></a>Másolási tevékenység tulajdonságai

A tevékenységek definiálásához elérhető csoportok és tulajdonságok teljes listáját a [folyamatok](concepts-pipelines-activities.md) című cikkben találja. Ez a szakasz a fájlrendszer forrása és a fogadó által támogatott tulajdonságok listáját tartalmazza.

### <a name="file-system-as-source"></a>Fájlrendszer forrásként

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

A fájlrendszer a következő tulajdonságokat támogatja a `storeSettings` Format-alapú másolási forrás beállítások területén:

| Tulajdonság                 | Leírás                                                  | Kötelező                                      |
| ------------------------ | ------------------------------------------------------------ | --------------------------------------------- |
| típus                     | A Type tulajdonságot a `storeSettings` **FileServerReadSettings** értékre kell állítani. | Yes                                           |
| ***Keresse meg a másolandó fájlokat:*** |  |  |
| 1. lehetőség: statikus elérési út<br> | Másolja az adatkészletben megadott mappa vagy fájl elérési útját. Ha az összes fájlt egy mappából szeretné másolni, azt is meg kell adnia `wildcardFileName` `*` . |  |
| 2. lehetőség: Kiszolgálóoldali szűrő<br>– fileFilter  | Fájlkiszolgáló oldali natív szűrő, amely jobb teljesítményt nyújt, mint a 3. lehetőség helyettesítő szűrője. `*`Nulla vagy több karakter összeegyeztetésére, valamint `?` a nulla vagy egy karakter megfeleltetésére használható. Az [ebben a szakaszban](/dotnet/api/system.io.directory.getfiles#System_IO_Directory_GetFiles_System_String_System_String_System_IO_SearchOption_)található **Megjegyzések** szintaxisáról és megjegyzéseit itt találja. | No                                                          |
| 3. lehetőség: ügyféloldali szűrő<br>- wildcardFolderPath | A mappa elérési útja helyettesítő karakterekkel a forrás mappák szűréséhez. Ez a szűrő az ADF oldalon történik, az ADF a megadott elérési úton lévő mappák/fájlok enumerálása, majd a helyettesítő szűrő alkalmazása.<br>Az engedélyezett helyettesítő karakterek a következők: `*` (nulla vagy több karakternek felel meg) és `?` (a nulla vagy egy karakter egyezése) `^` <br>További példákat a [mappák és a fájlok szűrésére szolgáló példákban](#folder-and-file-filter-examples)talál. | No                                            |
| 3. lehetőség: ügyféloldali szűrő<br>- wildcardFileName | A forrásfájl szűréséhez a megadott folderPath/wildcardFolderPath helyettesítő karaktereket tartalmazó fájlnév. Ez a szűrő az ADF oldalon történik, az ADF a megadott elérési úton lévő fájlok enumerálása, majd a helyettesítő szűrő alkalmazása.<br>Az engedélyezett helyettesítő karakterek a következők: `*` (nulla vagy több karakternek felel meg) és `?` (a nulla vagy egy karakter egyezése) `^`<br>További példákat a [mappák és a fájlok szűrésére szolgáló példákban](#folder-and-file-filter-examples)talál. | Yes |
| 3. lehetőség: a fájlok listája<br>- fileListPath | Egy adott fájl másolását jelzi. Mutasson egy szövegfájlra, amely tartalmazza a másolni kívánt fájlok listáját, soronként egy fájlt, amely az adatkészletben konfigurált útvonal relatív elérési útja.<br/>Ha ezt a beállítást használja, ne adja meg a fájl nevét az adatkészletben. További példákat a [fájllista példákban](#file-list-examples)talál. |No |
| ***További beállítások:*** |  | |
| rekurzív | Azt jelzi, hogy az adatok rekurzív módon olvashatók-e az almappákból, vagy csak a megadott mappából. Vegye figyelembe, hogy ha a rekurzív értéke TRUE (igaz), a fogadó pedig egy fájl alapú tároló, a fogadó nem másolja vagy hozza létre az üres mappát vagy almappát. <br>Az engedélyezett értékek: **true** (alapértelmezett) és **false (hamis**).<br>Ez a tulajdonság nem érvényes a konfiguráláskor `fileListPath` . |No |
| deleteFilesAfterCompletion | Azt jelzi, hogy a rendszer törli-e a bináris fájlokat a forrás-áruházból, miután sikeresen áthelyezte a célhelyre. A fájl törlése fájl alapján történik, így ha a másolási tevékenység meghiúsul, néhány fájl már át lett másolva a célhelyre, és törlődik a forrásból, míg mások továbbra is a forrás-áruházban maradnak. <br/>Ez a tulajdonság csak bináris fájlok másolási forgatókönyv esetén érvényes. Az alapértelmezett érték: false. |No |
| modifiedDatetimeStart    | A fájlok szűrése a következő attribútum alapján: utoljára módosítva. <br>A fájlok akkor lesznek kiválasztva, ha az utolsó módosítás időpontja a és a közötti időintervallumon belül van `modifiedDatetimeStart` `modifiedDatetimeEnd` . Az idő az UTC-időzónára vonatkozik "2018-12-01T05:00:00Z" formátumban. <br> A tulajdonságok lehetnek NULL értékűek, ami azt jelenti, hogy a rendszer nem alkalmazza a file Attribute szűrőt az adatkészletre.  Ha `modifiedDatetimeStart` a dátum datetime értékkel rendelkezik `modifiedDatetimeEnd` , de null értékű, az azt jelenti, hogy azok a fájlok lesznek kiválasztva, amelyek utolsó módosított attribútuma nagyobb vagy egyenlő, mint a DateTime érték.  Ha `modifiedDatetimeEnd` a dátum datetime értékkel rendelkezik `modifiedDatetimeStart` , de null értékű, az azt jelenti, hogy azok a fájlok, amelyek utolsó módosítási attribútuma kisebb, mint a DateTime érték, ki lesz választva.<br/>Ez a tulajdonság nem érvényes a konfiguráláskor `fileListPath` . | No                                            |
| modifiedDatetimeEnd      | Lásd fentebb.                                               | No                                            |
| enablePartitionDiscovery | A particionált fájlok esetében adja meg, hogy szeretné-e elemezni a partíciókat a fájl elérési útján, majd adja hozzá őket további forrásként szolgáló oszlopként.<br/>Az engedélyezett értékek: **false** (alapértelmezett) és **true (igaz**). | No                                            |
| partitionRootPath | Ha engedélyezve van a partíciók felderítése, akkor a particionált mappák adatoszlopként való olvasásához a gyökér elérési útját kell megadni.<br/><br/>Ha nincs megadva, a rendszer alapértelmezés szerint<br/>– Ha a fájl elérési útját használja az adatkészletben vagy a forrásban található fájlok listáján, a partíció gyökerének elérési útja az adatkészletben konfigurált útvonal.<br/>– Ha helyettesítő mappa szűrőt használ, a partíció gyökerének elérési útja az első helyettesítő karakter előtti Alútvonal.<br/><br/>Tegyük fel például, hogy az adatkészletben az elérési utat "root/Folder/Year = 2020/hónap = 08/Day = 27" értékre konfigurálja:<br/>– Ha a partíció gyökerének elérési útját "gyökér/mappa/év = 2020" értékre állítja, a másolási tevékenység két további oszlopot fog előállítani, `month` és a `day` "08" és "27" értéket is kijelöli a fájlokban lévő oszlopokon kívül.<br/>– Ha nincs megadva a partíció gyökerének elérési útja, nem jön létre további oszlop. | No                                            |
| maxConcurrentConnections |A tevékenység futtatása során az adattárhoz létesített egyidejű kapcsolatok felső határa. Csak akkor adhat meg értéket, ha korlátozni szeretné az egyidejű kapcsolatokat.| No                                            |

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
                    "type": "DelimitedTextReadSettings",
                    "skipLineCount": 10
                },
                "storeSettings":{
                    "type": "FileServerReadSettings",
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

### <a name="file-system-as-sink"></a>Fájlrendszer fogadóként

[!INCLUDE [data-factory-v2-file-sink-formats](../../includes/data-factory-v2-file-sink-formats.md)]

A fájlrendszer a következő tulajdonságokat támogatja a `storeSettings` Format-alapú másolási fogadó beállításai alatt:

| Tulajdonság                 | Leírás                                                  | Kötelező |
| ------------------------ | ------------------------------------------------------------ | -------- |
| típus                     | A Type tulajdonságot a `storeSettings` **FileServerWriteSettings** értékre kell állítani. | Yes      |
| copyBehavior             | Meghatározza a másolási viselkedést, ha a forrás fájl-alapú adattárból származó fájlok.<br/><br/>Az engedélyezett értékek a következők:<br/><b>-PreserveHierarchy (alapértelmezett)</b>: megőrzi a fájl-hierarchiát a célmappában. A forrásfájl a forrás mappájához relatív elérési útja megegyezik a célfájl relatív elérési útjával.<br/><b>-FlattenHierarchy</b>: a forrás mappából származó összes fájl a célmappa első szintjén van. A célként megadott fájlok automatikusan generált névvel rendelkeznek. <br/><b>-MergeFiles</b>: az összes fájlt egyesíti a forrás mappájából egy fájlba. Ha meg van adva a fájl neve, az egyesített fájl neve a megadott név. Ellenkező esetben ez egy automatikusan létrehozott fájl neve. | No       |
| maxConcurrentConnections |A tevékenység futtatása során az adattárhoz létesített egyidejű kapcsolatok felső határa. Csak akkor adhat meg értéket, ha korlátozni szeretné az egyidejű kapcsolatokat.| No       |

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
                    "type": "FileServerWriteSettings",
                    "copyBehavior": "PreserveHierarchy"
                }
            }
        }
    }
]
```

### <a name="folder-and-file-filter-examples"></a>Példák a mappák és a fájlok szűrésére

Ez a szakasz a mappa elérési útjának és fájlnevének a helyettesítő karakteres szűrőkkel való viselkedését írja le.

| folderPath | fileName | rekurzív | A forrás mappa szerkezete és a szűrő eredménye (a **félkövérrel szedett** fájlok beolvasása)|
|:--- |:--- |:--- |:--- |
| `Folder*` | (üres, alapértelmezett használata) | hamis | Mappa<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File2.jsbekapcsolva**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.jsbekapcsolva<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5.csv<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `Folder*` | (üres, alapértelmezett használata) | true | Mappa<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File2.jsbekapcsolva**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File4.jsbekapcsolva**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `Folder*` | `*.csv` | hamis | Mappa<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2.jsbekapcsolva<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.jsbekapcsolva<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5.csv<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `Folder*` | `*.csv` | true | Mappa<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2.jsbekapcsolva<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.jsbekapcsolva<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |

### <a name="file-list-examples"></a>Példák a fájllista

Ez a szakasz a fájllista elérési útjának a másolási tevékenység forrásában való használatának eredményét írja le.

Feltételezve, hogy rendelkezik a következő forrás-mappa struktúrájával, és félkövéren szeretné átmásolni a fájlokat:

| Példa a forrás struktúrájára                                      | Tartalom FileListToCopy.txt                             | ADF-konfiguráció                                            |
| ------------------------------------------------------------ | --------------------------------------------------------- | ------------------------------------------------------------ |
| legfelső szintű<br/>&nbsp;&nbsp;&nbsp;&nbsp;Mappa<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File2.jsbekapcsolva<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.jsbekapcsolva<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Metaadatok<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;FileListToCopy.txt | File1.csv<br>Subfolder1/File3.csv<br>Subfolder1/File5.csv | **Az adatkészletben:**<br>-Mappa elérési útja: `root/FolderA`<br><br>**A másolási tevékenység forrása:**<br>-Fájllista elérési útja: `root/Metadata/FileListToCopy.txt` <br><br>A fájl-lista elérési útja ugyanazon az adattárban található szövegfájlra mutat, amely tartalmazza a másolni kívánt fájlok listáját, és soronként egy fájlt az adatkészletben konfigurált útvonal relatív elérési útjával. |

### <a name="recursive-and-copybehavior-examples"></a>rekurzív és copyBehavior példák

Ez a szakasz a rekurzív és copyBehavior értékek különböző kombinációinak másolási műveletének eredményét írja le.

| rekurzív | copyBehavior | Forrás mappa szerkezete | Eredményül kapott cél |
|:--- |:--- |:--- |:--- |
| true |preserveHierarchy | Mappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fájl2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fájl3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | A célmappa Mappa1 ugyanazzal a struktúrával jön létre, mint a forrás:<br/><br/>Mappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fájl2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fájl3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5. |
| true |flattenHierarchy | Mappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fájl2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fájl3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | A célként megadott Mappa1 a következő szerkezettel jön létre: <br/><br/>Mappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;a file1 automatikusan generált neve<br/>&nbsp;&nbsp;&nbsp;&nbsp;a Fájl2 automatikusan generált neve<br/>&nbsp;&nbsp;&nbsp;&nbsp;a fájl3 automatikusan generált neve<br/>&nbsp;&nbsp;&nbsp;&nbsp;a File4 automatikusan generált neve<br/>&nbsp;&nbsp;&nbsp;&nbsp;a File5 automatikusan generált neve |
| true |mergeFiles | Mappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fájl2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fájl3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | A célként megadott Mappa1 a következő szerkezettel jön létre: <br/><br/>Mappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1 + Fájl2 + fájl3 + File4 + file 5 tartalom egyesítve egyetlen fájlba az automatikusan létrehozott fájlnévvel |
| hamis |preserveHierarchy | Mappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fájl2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fájl3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | A célmappa Mappa1 a következő szerkezettel jön létre<br/><br/>Mappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fájl2<br/><br/>A fájl3, a File4 és a File5 Subfolder1 nem vesznek fel. |
| hamis |flattenHierarchy | Mappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fájl2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fájl3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | A célmappa Mappa1 a következő szerkezettel jön létre<br/><br/>Mappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;a file1 automatikusan generált neve<br/>&nbsp;&nbsp;&nbsp;&nbsp;a Fájl2 automatikusan generált neve<br/><br/>A fájl3, a File4 és a File5 Subfolder1 nem vesznek fel. |
| hamis |mergeFiles | Mappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fájl2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fájl3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | A célmappa Mappa1 a következő szerkezettel jön létre<br/><br/>Mappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;A file1 + Fájl2 tartalma egy fájlba van egyesítve, automatikusan létrehozott fájlnévvel. a file1 automatikusan generált neve<br/><br/>A fájl3, a File4 és a File5 Subfolder1 nem vesznek fel. |

## <a name="lookup-activity-properties"></a>Keresési tevékenység tulajdonságai

A tulajdonságok részleteinek megismeréséhez tekintse meg a [keresési tevékenységet](control-flow-lookup-activity.md).

## <a name="getmetadata-activity-properties"></a>GetMetadata tevékenység tulajdonságai

A tulajdonságok részleteinek megismeréséhez tekintse meg a [GetMetaData tevékenységet](control-flow-get-metadata-activity.md) 

## <a name="delete-activity-properties"></a>Tevékenység tulajdonságainak törlése

A tulajdonságok részleteinek megismeréséhez tekintse meg a [tevékenység törlése](delete-activity.md) lehetőséget.

## <a name="legacy-models"></a>Örökölt modellek

>[!NOTE]
>A következő modellek továbbra is támogatottak a visszamenőleges kompatibilitás érdekében. Azt javasoljuk, hogy használja a fenti szakaszban említett új modellt, és az ADF authoring felhasználói felülete átvált az új modell generálására.

### <a name="legacy-dataset-model"></a>Örökölt adatkészlet-modell

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| típus | Az adatkészlet Type tulajdonságát a következőre kell beállítani: **fájlmegosztás** |Yes |
| folderPath | A mappa elérési útja. A helyettesítő karakteres szűrő támogatott, az engedélyezett helyettesítő karakterek a következők: `*` (nulla vagy több karakternek felel meg) és `?` (a nulla vagy az egyetlen karakternek felel meg); `^` Ha a tényleges mappanevet helyettesítő karakter vagy a escape-karakter található, akkor a Escape karaktert kell használnia. <br/><br/>Példák: gyökérmappa/almappa/, további példák a példaként szolgáló [társított szolgáltatás és adatkészlet-definíciók](#sample-linked-service-and-dataset-definitions) , valamint a [mappa-és fájlszűrő-példák](#folder-and-file-filter-examples)elemre. |No |
| fileName | A fájl (ok) **neve vagy helyettesítő szűrője** a megadott "folderPath". Ha nem ad meg értéket ehhez a tulajdonsághoz, az adatkészlet a mappában található összes fájlra mutat. <br/><br/>A Filter (szűrő) esetében az engedélyezett helyettesítő karakterek a következők: `*` (nulla vagy több karakternek felel meg) és `?` (nulla vagy egyetlen karakternek felel meg).<br/>– 1. példa: `"fileName": "*.csv"`<br/>– 2. példa: `"fileName": "???20180427.txt"`<br/>`^`Ha a tényleges fájlnév helyettesítő karakter vagy ez a escape-karakter található, akkor Escape-karaktert kell használnia.<br/><br/>Ha nincs megadva fájlnév a kimeneti adatkészlethez, és a **preserveHierarchy** nincs megadva a tevékenység fogadójában, a másolási tevékenység automatikusan létrehozza a fájlnevet a következő mintával: "*adat. [ tevékenység futtatási azonosítója GUID]. [GUID if FlattenHierarchy]. [formátum, ha konfigurálva]. [tömörítés, ha konfigurálva]*", például" Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt. gz "; Ha táblázatos forrásból másol a lekérdezés helyett táblanév használatával, a név minta a következő: "*[Table Name]. [ Format]. [tömörítés, ha konfigurálva]*", például" MyTable.csv ". |No |
| modifiedDatetimeStart | A fájlok szűrése a következő attribútum alapján: utoljára módosítva. A fájlok akkor lesznek kiválasztva, ha az utolsó módosítás időpontja a és a közötti időintervallumon belül van `modifiedDatetimeStart` `modifiedDatetimeEnd` . Az idő az UTC-időzónára vonatkozik "2018-12-01T05:00:00Z" formátumban. <br/><br/> Ügyeljen arra, hogy az adatáthelyezés általános teljesítményét a beállítás engedélyezésével befolyásolja, ha nagy mennyiségű fájlból szeretne szűrni a fájlmegosztást. <br/><br/> A tulajdonságok lehetnek NULL értékűek, ami azt jelenti, hogy a rendszer nem alkalmazza a file Attribute szűrőt az adatkészletre.  Ha `modifiedDatetimeStart` a dátum datetime értékkel rendelkezik `modifiedDatetimeEnd` , de null értékű, az azt jelenti, hogy azok a fájlok lesznek kiválasztva, amelyek utolsó módosított attribútuma nagyobb vagy egyenlő, mint a DateTime érték.  Ha `modifiedDatetimeEnd` a dátum datetime értékkel rendelkezik `modifiedDatetimeStart` , de null értékű, az azt jelenti, hogy azok a fájlok, amelyek utolsó módosítási attribútuma kisebb, mint a DateTime érték, ki lesz választva.| No |
| modifiedDatetimeEnd | A fájlok szűrése a következő attribútum alapján: utoljára módosítva. A fájlok akkor lesznek kiválasztva, ha az utolsó módosítás időpontja a és a közötti időintervallumon belül van `modifiedDatetimeStart` `modifiedDatetimeEnd` . Az idő az UTC-időzónára vonatkozik "2018-12-01T05:00:00Z" formátumban. <br/><br/> Ügyeljen arra, hogy az adatáthelyezés általános teljesítményét a beállítás engedélyezésével befolyásolja, ha nagy mennyiségű fájlból szeretne szűrni a fájlmegosztást. <br/><br/> A tulajdonságok lehetnek NULL értékűek, ami azt jelenti, hogy a rendszer nem alkalmazza a file Attribute szűrőt az adatkészletre.  Ha `modifiedDatetimeStart` a dátum datetime értékkel rendelkezik `modifiedDatetimeEnd` , de null értékű, az azt jelenti, hogy azok a fájlok lesznek kiválasztva, amelyek utolsó módosított attribútuma nagyobb vagy egyenlő, mint a DateTime érték.  Ha `modifiedDatetimeEnd` a dátum datetime értékkel rendelkezik `modifiedDatetimeStart` , de null értékű, az azt jelenti, hogy azok a fájlok, amelyek utolsó módosítási attribútuma kisebb, mint a DateTime érték, ki lesz választva.| No |
| formátumban | Ha **fájlokat szeretne másolni** a fájl alapú tárolók között (bináris másolás), ugorja át a formátum szakaszt mind a bemeneti, mind a kimeneti adatkészlet-definíciókban.<br/><br/>Ha a fájlokat egy adott formátummal szeretné elemezni vagy előállítani, a következő fájlformátum-típusok támogatottak: **Szövegformátum**, **JsonFormat**, **AvroFormat**, **OrcFormat**, **ParquetFormat**. A **Type (típus** ) tulajdonságot állítsa a Format értékre a következő értékek egyikére. További információkért lásd: [Szövegformátum](supported-file-formats-and-compression-codecs-legacy.md#text-format), JSON- [Formátum](supported-file-formats-and-compression-codecs-legacy.md#json-format), [Avro formátum](supported-file-formats-and-compression-codecs-legacy.md#avro-format), [ork-formátum](supported-file-formats-and-compression-codecs-legacy.md#orc-format)és a [parketta formátuma](supported-file-formats-and-compression-codecs-legacy.md#parquet-format) című rész. |Nem (csak bináris másolási forgatókönyv esetén) |
| tömörítés | Adja meg az adattömörítés típusát és szintjét. További információ: [támogatott fájlformátumok és tömörítési kodekek](supported-file-formats-and-compression-codecs-legacy.md#compression-support).<br/>A támogatott típusok a következők: **gzip**, **deflate**, **BZip2** és **ZipDeflate**.<br/>A támogatott szintek a következők: **optimális** és **leggyorsabb**. |No |

>[!TIP]
>Egy mappa összes fájljának másolásához csak a **folderPath** kell megadni.<br>Egy adott névvel rendelkező egyetlen fájl másolásához adja meg a **folderPath** és a fájlnév nevű **fájlnevet** .<br>Ha egy mappában lévő fájlok egy részhalmazát szeretné másolni, akkor a **folderPath** és a **filename** paramétert a helyettesítő karakteres szűrővel.

>[!NOTE]
>Ha a "fileFilter" tulajdonságot használta a fájl szűrőhöz, a rendszer továbbra is támogatja a-t, míg a rendszer a "fileName" kifejezéshez hozzáadott új szűrő funkció használatát javasolja.

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

### <a name="legacy-copy-activity-source-model"></a>Örökölt másolási tevékenység forrásának modellje

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| típus | A másolási tevékenység forrásának Type tulajdonságát a következőre kell beállítani: **FileSystemSource** |Yes |
| rekurzív | Azt jelzi, hogy az adatok rekurzív módon olvashatók-e az almappákból, vagy csak a megadott mappából. Vegye figyelembe, hogy ha a rekurzív értéke TRUE (igaz), a fogadó pedig a fájl alapú tároló, akkor a rendszer nem másolja/hozza létre az üres mappát/almappát a fogadóban.<br/>Az engedélyezett értékek: **true** (alapértelmezett), **false** | No |
| maxConcurrentConnections |A tevékenység futtatása során az adattárhoz létesített egyidejű kapcsolatok felső határa. Csak akkor adhat meg értéket, ha korlátozni szeretné az egyidejű kapcsolatokat.| No |

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

### <a name="legacy-copy-activity-sink-model"></a>Örökölt másolási tevékenység fogadó modellje

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| típus | A másolási tevékenység fogadójának Type tulajdonságát a következőre kell beállítani: **FileSystemSink** |Yes |
| copyBehavior | Meghatározza a másolási viselkedést, ha a forrás fájl-alapú adattárból származó fájlok.<br/><br/>Az engedélyezett értékek a következők:<br/><b>-PreserveHierarchy (alapértelmezett)</b>: megőrzi a fájl-hierarchiát a célmappában. A forrásfájl a forrás mappájához relatív elérési útja megegyezik a célfájl relatív elérési útjával.<br/><b>-FlattenHierarchy</b>: a forrás mappából származó összes fájl a célmappa első szintjén van. A célfájl automatikusan generált névvel rendelkezik. <br/><b>-MergeFiles</b>: az összes fájlt egyesíti a forrás mappájából egy fájlba. Az egyesítés során nem történik meg a rekordok deduplikálása. Ha a fájl neve meg van adva, az egyesített fájlnév a megadott név lesz. Ellenkező esetben az automatikusan létrehozott fájlnév lenne. | No |
| maxConcurrentConnections |A tevékenység futtatása során az adattárhoz létesített egyidejű kapcsolatok felső határa. Csak akkor adhat meg értéket, ha korlátozni szeretné az egyidejű kapcsolatokat.| No |

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

## <a name="next-steps"></a>Következő lépések
A Azure Data Factory a másolási tevékenység által forrásként és nyelőként támogatott adattárak listáját lásd: [támogatott adattárak](copy-activity-overview.md#supported-data-stores-and-formats).