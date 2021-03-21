---
title: Adatok másolása a Google Cloud Storage-ból a Azure Data Factory használatával
description: További információ a Google Cloud Storage-ból származó adatok másolásáról a Azure Data Factory használatával támogatott fogadó adattárakra.
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.date: 03/17/2021
ms.author: jingwang
ms.openlocfilehash: b8e518ba23c877ee80197ad94a6bc01b23b044a3
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "104588987"
---
# <a name="copy-data-from-google-cloud-storage-by-using-azure-data-factory"></a>Adatok másolása a Google Cloud Storage-ból a Azure Data Factory használatával

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Ez a cikk az adatok Google Cloud Storage-ból (GCS) történő másolását ismerteti. A Azure Data Factoryről a [bevezető cikkben](introduction.md)olvashat bővebben.

## <a name="supported-capabilities"></a>Támogatott képességek

Ez a Google Cloud Storage-összekötő a következő tevékenységek esetén támogatott:

- [Másolási tevékenység](copy-activity-overview.md) [támogatott forrás/fogadó mátrixtal](copy-activity-overview.md)
- [Keresési tevékenység](control-flow-lookup-activity.md)
- [GetMetadata tevékenység](control-flow-get-metadata-activity.md)
- [Tevékenység törlése](delete-activity.md)

Pontosabban, ez a Google Cloud Storage-összekötő támogatja a fájlok másolását, vagy a fájlok elemzését a [támogatott fájlformátumokkal és tömörítési kodekekkel](supported-file-formats-and-compression-codecs.md). Kihasználja a GCS S3-kompatibilis együttműködési képességét.

## <a name="prerequisites"></a>Előfeltételek

A következő beállítás szükséges a Google Cloud Storage-fiókjában:

1. A Google Cloud Storage-fiók együttműködési képességének engedélyezése
2. Állítsa be azt az alapértelmezett projektet, amely a cél GCS gyűjtőből másolni kívánt adatokra vonatkozó beállításokat tartalmazza.
3. Hozzon létre egy szolgáltatásfiókot, és határozza meg a megfelelő szintű engedélyeket a Cloud IAM használatával a GCP-on. 
4. A szolgáltatásfiók hozzáférési kulcsainak létrehozása.

![A Google Cloud Storage elérési kulcsának beolvasása](media/connector-google-cloud-storage/google-storage-cloud-settings.png)

## <a name="required-permissions"></a>Szükséges engedélyek

A Google Cloud Storage-ból származó adatok másolásához győződjön meg arról, hogy a következő engedélyek vannak megadva az objektum-műveletekhez: ` storage.objects.get` és ` storage.objects.list` .

Ha Data Factory felhasználói felületet használ a létrehozáshoz, további ` storage.buckets.list` engedélyekre van szükség az olyan műveletekhez, mint a társított szolgáltatáshoz való csatlakozás és a gyökérből való böngészés. Ha nem szeretné megadni ezt az engedélyt, a felhasználói felületen válassza a "kapcsolódás a fájl elérési útjához" vagy a "Tallózás a megadott elérési útról" lehetőséget.

A Google Cloud Storage szerepköreinek és a kapcsolódó engedélyek teljes listájáért lásd: a Google Cloud site [Felhőbeli tárhelyének iam-szerepkörei](https://cloud.google.com/storage/docs/access-control/iam-roles) .

## <a name="getting-started"></a>Első lépések

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)] 

A következő szakaszokban részletesen ismertetjük azokat a tulajdonságokat, amelyek a Google Cloud Storage-hoz kapcsolódó Data Factory-entitások definiálására szolgálnak.

## <a name="linked-service-properties"></a>Társított szolgáltatás tulajdonságai

A Google Cloud Storage társított szolgáltatásai a következő tulajdonságokat támogatják:

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| típus | A **Type** tulajdonságot **GoogleCloudStorage** értékre kell beállítani. | Yes |
| accessKeyId | A titkos elérési kulcs azonosítója. A hozzáférési kulcs és a titok megkereséséhez tekintse meg az [Előfeltételek](#prerequisites)című témakört. |Yes |
| secretAccessKey | Maga a titkos elérési kulcs. Ezt a mezőt **SecureString** kell megjelölni, hogy biztonságosan tárolja Data Factoryban, vagy [hivatkozjon a Azure Key Vault tárolt titkos kulcsra](store-credentials-in-key-vault.md). |Yes |
| serviceUrl | Határozza meg az egyéni GCS-végpontot `https://storage.googleapis.com` . | Yes |
| Connectvia tulajdonsággal | Az adattárhoz való csatlakozáshoz használt [integrációs](concepts-integration-runtime.md) modul. Használhatja az Azure Integration Runtime vagy a saját üzemeltetésű integrációs modult (ha az adattár egy magánhálózaton található). Ha ez a tulajdonság nincs megadva, a szolgáltatás az alapértelmezett Azure Integration Runtime-t használja. |No |

Bemutatunk egy példát:

```json
{
    "name": "GoogleCloudStorageLinkedService",
    "properties": {
        "type": "GoogleCloudStorage",
        "typeProperties": {
            "accessKeyId": "<access key id>",
            "secretAccessKey": {
                "type": "SecureString",
                "value": "<secret access key>"
            },
            "serviceUrl": "https://storage.googleapis.com"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>Adatkészlet tulajdonságai

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

A Google Cloud Storage a következő tulajdonságokat támogatja a `location` Format-alapú adatkészlet beállításai alatt:

| Tulajdonság   | Leírás                                                  | Kötelező |
| ---------- | ------------------------------------------------------------ | -------- |
| típus       | Az  `location` adatkészletben található Type tulajdonságot **GoogleCloudStorageLocation** értékre kell állítani. | Yes      |
| bucketName | A GCS-gyűjtő neve.                                          | Yes      |
| folderPath | Az adott gyűjtőhöz tartozó mappa elérési útja. Ha helyettesítő karaktert szeretne használni a mappa szűréséhez, hagyja ki ezt a beállítást, és a tevékenység forrásának beállításainál válassza ki azt. | No       |
| fileName   | A fájl neve az adott gyűjtő és mappa elérési útjában. Ha helyettesítő karaktert szeretne használni a fájlok szűréséhez, hagyja ki ezt a beállítást, és válassza ki a tevékenység forrásának beállításait. | No       |

**Példa**

```json
{
    "name": "DelimitedTextDataset",
    "properties": {
        "type": "DelimitedText",
        "linkedServiceName": {
            "referenceName": "<Google Cloud Storage linked service name>",
            "type": "LinkedServiceReference"
        },
        "schema": [ < physical schema, optional, auto retrieved during authoring > ],
        "typeProperties": {
            "location": {
                "type": "GoogleCloudStorageLocation",
                "bucketName": "bucketname",
                "folderPath": "folder/subfolder"
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

A tevékenységek definiálásához elérhető csoportok és tulajdonságok teljes listáját a [folyamatok](concepts-pipelines-activities.md) című cikkben találja. Ez a szakasz a Google Cloud Storage-forrás által támogatott tulajdonságok listáját tartalmazza.

### <a name="google-cloud-storage-as-a-source-type"></a>Google Cloud Storage forrás típusa

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

A Google Cloud Storage szolgáltatásban a következő tulajdonságok támogatottak a `storeSettings` Format-alapú másolási forrás beállításai alatt:

| Tulajdonság                 | Leírás                                                  | Kötelező                                                    |
| ------------------------ | ------------------------------------------------------------ | ----------------------------------------------------------- |
| típus                     | A **Type** tulajdonságot a `storeSettings` **GoogleCloudStorageReadSettings** értékre kell állítani. | Yes                                                         |
| ***Keresse meg a másolandó fájlokat:*** |  |  |
| 1. lehetőség: statikus elérési út<br> | Másolja az adatkészletben megadott gyűjtő vagy mappa vagy fájl elérési útját. Ha egy gyűjtőből vagy mappából kívánja átmásolni az összes fájlt, azt is meg kell adni `wildcardFileName` `*` . |  |
| 2. lehetőség: GCS-előtag<br>-előtag | Az adatkészletben a forrás GCS-fájlok szűréséhez konfigurált megadott gyűjtő alatt található GCS-kulcs előtagja. Azok a GCS-kulcsok, amelyekhez a nevek kezdődnek, `bucket_in_dataset/this_prefix` ki vannak választva. A GCS szolgáltatás-oldalsó szűrőjét használja, amely jobb teljesítményt nyújt, mint a helyettesítő karakteres szűrő. | No |
| 3. lehetőség: helyettesítő karakter<br>- wildcardFolderPath | A mappa elérési útja a forrás mappák szűréséhez az adatkészletben konfigurált megadott gyűjtőben lévő helyettesítő karakterekkel. <br>Az engedélyezett helyettesítő karakterek a következők: `*` (nulla vagy több karakternek felel meg) és `?` (nulla vagy egyetlen karakternek felel meg). `^`Ha a mappa neve helyettesítő karakter vagy a escape-karakter található, akkor a (z) használatával elkerülheti a menekülési karaktert. <br>További példákat a [mappák és a fájlok szűrésére szolgáló példákban](#folder-and-file-filter-examples)talál. | No                                            |
| 3. lehetőség: helyettesítő karakter<br>- wildcardFileName | Az adott gyűjtő és mappa elérési útján (vagy helyettesítő mappa elérési útján) található, helyettesítő karakterekkel rendelkező fájlnév a forrásfájlok szűréséhez. <br>Az engedélyezett helyettesítő karakterek a következők: `*` (nulla vagy több karakternek felel meg) és `?` (nulla vagy egyetlen karakternek felel meg). `^`Ha a fájl neve helyettesítő karakter vagy a escape-karakter található, akkor a (z) használatával elkerülheti a menekülési karaktert.  További példákat a [mappák és a fájlok szűrésére szolgáló példákban](#folder-and-file-filter-examples)talál. | Yes |
| 3. lehetőség: a fájlok listája<br>- fileListPath | Egy adott fájl másolását jelzi. Mutasson egy szövegfájlra, amely tartalmazza a másolni kívánt fájlok listáját, soronként egy fájlt, amely az adatkészletben konfigurált útvonal relatív elérési útja.<br/>Ha ezt a beállítást használja, ne adja meg a fájl nevét az adatkészletben. További példákat a [fájllista példákban](#file-list-examples)talál. |No |
| ***További beállítások:*** |  | |
| rekurzív | Azt jelzi, hogy az adatok rekurzív módon olvashatók-e az almappákból, vagy csak a megadott mappából. Vegye figyelembe, hogy ha a **rekurzív** értéke **true (igaz** ), a fogadó pedig egy fájl alapú tároló, a fogadó nem másolja vagy hozza létre az üres mappát vagy almappát. <br>Az engedélyezett értékek: **true** (alapértelmezett) és **false (hamis**).<br>Ez a tulajdonság nem érvényes a konfiguráláskor `fileListPath` . |No |
| deleteFilesAfterCompletion | Azt jelzi, hogy a rendszer törli-e a bináris fájlokat a forrás-áruházból, miután sikeresen áthelyezte a célhelyre. A fájl törlése fájl alapján történik, így ha a másolási tevékenység meghiúsul, néhány fájl már át lett másolva a célhelyre, és törlődik a forrásból, míg mások továbbra is a forrás-áruházban maradnak. <br/>Ez a tulajdonság csak bináris fájlok másolási forgatókönyv esetén érvényes. Az alapértelmezett érték: false. |No |
| modifiedDatetimeStart    | A fájlok a következő attribútum alapján vannak szűrve: utoljára módosítva. <br>A fájlok akkor lesznek kiválasztva, ha az utolsó módosítás időpontja a és a közötti időintervallumon belül van `modifiedDatetimeStart` `modifiedDatetimeEnd` . Az időpontot az UTC időzónára alkalmazza a "2018-12-01T05:00:00Z" formátumban. <br> A tulajdonságok lehetnek **Null értékűek**, ami azt jelenti, hogy a rendszer nem alkalmazza a file Attribute szűrőt az adatkészletre.  Ha a `modifiedDatetimeStart` dátum datetime értékkel rendelkezik `modifiedDatetimeEnd` , de **Null** értékű, akkor azok a fájlok lesznek kiválasztva, amelyek utolsó módosított attribútuma nagyobb vagy egyenlő, mint a DateTime érték.  Ha a `modifiedDatetimeEnd` dátum datetime értékkel rendelkezik `modifiedDatetimeStart` , de **Null** értékű, akkor azok a fájlok lesznek kiválasztva, amelyek utolsó módosítási attribútuma kisebb a DateTime értéknél.<br/>Ez a tulajdonság nem érvényes a konfiguráláskor `fileListPath` . | No                                            |
| modifiedDatetimeEnd      | Lásd fentebb.                                               | No                                                          |
| enablePartitionDiscovery | A particionált fájlok esetében adja meg, hogy szeretné-e elemezni a partíciókat a fájl elérési útján, majd adja hozzá őket további forrásként szolgáló oszlopként.<br/>Az engedélyezett értékek: **false** (alapértelmezett) és **true (igaz**). | No                                            |
| partitionRootPath | Ha engedélyezve van a partíciók felderítése, akkor a particionált mappák adatoszlopként való olvasásához a gyökér elérési útját kell megadni.<br/><br/>Ha nincs megadva, a rendszer alapértelmezés szerint<br/>– Ha a fájl elérési útját használja az adatkészletben vagy a forrásban található fájlok listáján, a partíció gyökerének elérési útja az adatkészletben konfigurált útvonal.<br/>– Ha helyettesítő mappa szűrőt használ, a partíció gyökerének elérési útja az első helyettesítő karakter előtti Alútvonal.<br/><br/>Tegyük fel például, hogy az adatkészletben az elérési utat "root/Folder/Year = 2020/hónap = 08/Day = 27" értékre konfigurálja:<br/>– Ha a partíció gyökerének elérési útját "gyökér/mappa/év = 2020" értékre állítja, a másolási tevékenység két további oszlopot fog előállítani, `month` és a `day` "08" és "27" értéket is kijelöli a fájlokban lévő oszlopokon kívül.<br/>– Ha nincs megadva a partíció gyökerének elérési útja, nem jön létre további oszlop. | No                                            |
| maxConcurrentConnections |A tevékenység futtatása során az adattárhoz létesített egyidejű kapcsolatok felső határa. Csak akkor adhat meg értéket, ha korlátozni szeretné az egyidejű kapcsolatokat.| No                                                          |

**Példa**

```json
"activities":[
    {
        "name": "CopyFromGoogleCloudStorage",
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
                    "type": "GoogleCloudStorageReadSettings",
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

### <a name="folder-and-file-filter-examples"></a>Példák a mappák és a fájlok szűrésére

Ez a szakasz a mappa elérési útjának és fájlnevének a helyettesítő karakteres szűrőkkel való viselkedését írja le.

| gyűjtő | kulcs | rekurzív | A forrás mappa szerkezete és a szűrő eredménye (a félkövérrel szedett fájlok beolvasása)|
|:--- |:--- |:--- |:--- |
| gyűjtő | `Folder*/*` | hamis | gyűjtő<br/>&nbsp;&nbsp;&nbsp;&nbsp;Mappa<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File2.jsbekapcsolva**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.jsbekapcsolva<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| gyűjtő | `Folder*/*` | true | gyűjtő<br/>&nbsp;&nbsp;&nbsp;&nbsp;Mappa<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File2.jsbekapcsolva**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File4.jsbekapcsolva**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| gyűjtő | `Folder*/*.csv` | hamis | gyűjtő<br/>&nbsp;&nbsp;&nbsp;&nbsp;Mappa<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File2.jsbekapcsolva<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.jsbekapcsolva<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| gyűjtő | `Folder*/*.csv` | true | gyűjtő<br/>&nbsp;&nbsp;&nbsp;&nbsp;Mappa<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File2.jsbekapcsolva<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.jsbekapcsolva<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |

### <a name="file-list-examples"></a>Példák a fájllista

Ez a szakasz a másolási tevékenység forrásában lévő fájllista elérési útja alapján létrejövő viselkedést ismerteti.

Tegyük fel, hogy rendelkezik a következő forrás-mappa struktúrával, és félkövéren szeretné átmásolni a fájlokat:

| Példa a forrás struktúrájára                                      | Tartalom FileListToCopy.txt                             | Data Factory konfiguráció                                            |
| ------------------------------------------------------------ | --------------------------------------------------------- | ------------------------------------------------------------ |
| gyűjtő<br/>&nbsp;&nbsp;&nbsp;&nbsp;Mappa<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File2.jsbekapcsolva<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.jsbekapcsolva<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Metaadatok<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;FileListToCopy.txt | File1.csv<br>Subfolder1/File3.csv<br>Subfolder1/File5.csv | **Az adatkészletben:**<br>Gyűjtő `bucket`<br>-Mappa elérési útja: `FolderA`<br><br>**A másolási tevékenység forrása:**<br>-Fájllista elérési útja: `bucket/Metadata/FileListToCopy.txt` <br><br>A fájllista elérési útja ugyanazon az adattárban található szövegfájlra mutat, amely tartalmazza a másolni kívánt fájlok listáját, a soronként egy fájlt, valamint az adatkészletben konfigurált útvonal relatív elérési útját. |

## <a name="lookup-activity-properties"></a>Keresési tevékenység tulajdonságai

A tulajdonságok részleteinek megismeréséhez tekintse meg a [keresési tevékenységet](control-flow-lookup-activity.md).

## <a name="getmetadata-activity-properties"></a>GetMetadata tevékenység tulajdonságai

A tulajdonságok részleteinek megismeréséhez tekintse meg a [GetMetaData tevékenységet](control-flow-get-metadata-activity.md). 

## <a name="delete-activity-properties"></a>Tevékenység tulajdonságainak törlése

A tulajdonságok részleteinek megismeréséhez tekintse meg a [törlési tevékenységet](delete-activity.md).

## <a name="legacy-models"></a>Örökölt modellek

Ha egy Amazon S3-összekötőt használ az adatok Google Cloud Storage-ból való másolásához, az továbbra is támogatott a visszamenőleges kompatibilitás érdekében. Javasoljuk, hogy a korábban említett új modellt használja. Az Data Factory szerzői felhasználói felülete átváltott az új modell generálására.

## <a name="next-steps"></a>Következő lépések
A Azure Data Factory másolási tevékenység által forrásként és fogadóként támogatott adattárak listájáért lásd: [támogatott adattárak](copy-activity-overview.md#supported-data-stores-and-formats).
