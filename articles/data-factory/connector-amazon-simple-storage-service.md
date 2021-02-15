---
title: Adatok másolása az Amazon Simple Storage szolgáltatásból (S3)
description: Ismerje meg, hogyan másolhat adatok az Amazon Simple Storage Service (S3) szolgáltatásból a Azure Data Factory használatával támogatott fogadó adattárakba.
ms.author: jingwang
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 01/14/2021
ms.openlocfilehash: 2680c930bfa8451eec7dd518d3c535e0d04046cc
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/14/2021
ms.locfileid: "100387886"
---
# <a name="copy-data-from-amazon-simple-storage-service-by-using-azure-data-factory"></a>Az Amazon Simple Storage szolgáltatásból származó adatok másolása Azure Data Factory használatával
> [!div class="op_single_selector" title1="Válassza ki az Ön által használt Data Factory-szolgáltatás verzióját:"]
>
> * [1-es verzió](v1/data-factory-amazon-simple-storage-service-connector.md)
> * [Aktuális verzió](connector-amazon-simple-storage-service.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Ez a cikk az Amazon Simple Storage Service (Amazon S3) adatainak másolását ismerteti. A Azure Data Factoryről a [bevezető cikkben](introduction.md)olvashat bővebben.

>[!TIP]
>Ha többet szeretne megtudni az Amazon S3-ból az Azure Storage-ba irányuló adatáttelepítési forgatókönyvről, tekintse meg a Azure Data Factory használata az adatok áttelepítésére az [Amazon S3-ból az Azure Storage](data-migration-guidance-s3-azure-storage.md)

## <a name="supported-capabilities"></a>Támogatott képességek

Ez az Amazon S3-összekötő a következő tevékenységek esetén támogatott:

- [Másolási tevékenység](copy-activity-overview.md) [támogatott forrás/fogadó mátrixtal](copy-activity-overview.md)
- [Keresési tevékenység](control-flow-lookup-activity.md)
- [GetMetadata tevékenység](control-flow-get-metadata-activity.md)
- [Tevékenység törlése](delete-activity.md)

Pontosabban, ez az Amazon S3-összekötő támogatja a fájlok másolását vagy a fájlok elemzését a [támogatott fájlformátumokkal és tömörítési kodekekkel](supported-file-formats-and-compression-codecs.md). A [fájl metaadatainak megőrzése a másolás során](#preserve-metadata-during-copy)is megtartható. Az összekötő az [AWS Signature 4-es verzióját](https://docs.aws.amazon.com/general/latest/gr/signature-version-4.html) használja az S3-ra irányuló kérések hitelesítéséhez.

>[!TIP]
>Ezt az Amazon S3-összekötőt használhatja *bármely S3-kompatibilis tárolási szolgáltatóról*, például a [Google Cloud Storage](connector-google-cloud-storage.md)-ból származó adatok másolásához. A társított szolgáltatás konfigurációjában határozza meg a kapcsolódó szolgáltatás URL-címét.

## <a name="required-permissions"></a>Szükséges engedélyek

Az Amazon S3-ból származó adatok másolásához győződjön meg arról, hogy a következő engedélyeket adta meg az Amazon S3-objektum műveleteihez: `s3:GetObject` és `s3:GetObjectVersion` .

Ha Data Factory felhasználói felületet használ a létrehozáshoz, további `s3:ListAllMyBuckets` és `s3:ListBucket` / `s3:GetBucketLocation` engedélyek szükségesek olyan műveletekhez, mint a társított szolgáltatáshoz való csatlakozás és a gyökérből való böngészés. Ha nem szeretné megadni ezeket az engedélyeket, a felhasználói felületen válassza a "kapcsolódás a fájl elérési útjához" vagy a "Tallózás a megadott elérési útról" lehetőséget.

Az Amazon S3-engedélyek teljes listájáért lásd: [engedélyek megadása az AWS-hely házirendjében](https://docs.aws.amazon.com/AmazonS3/latest/dev/using-with-s3-actions.html) .

## <a name="getting-started"></a>Első lépések

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)] 

A következő szakaszokban részletesen ismertetjük az Amazon S3-ra jellemző Data Factory-entitások definiálásához használt tulajdonságokat.

## <a name="linked-service-properties"></a>Társított szolgáltatás tulajdonságai

Az Amazon S3 társított szolgáltatás a következő tulajdonságokat támogatja:

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| típus | A **Type** tulajdonságot **AmazonS3** értékre kell beállítani. | Yes |
| authenticationType | Adja meg az Amazon S3-hoz való kapcsolódáshoz használt hitelesítési típust. Használhat hozzáférési kulcsokat az AWS identitás-és hozzáférés-kezelési (IAM) fiókhoz vagy az [ideiglenes biztonsági hitelesítő adatokhoz](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_credentials_temp.html).<br>Az engedélyezett értékek a következők: `AccessKey` (alapértelmezett) és `TemporarySecurityCredentials` . |No |
| accessKeyId | A titkos elérési kulcs azonosítója. |Yes |
| secretAccessKey | Maga a titkos elérési kulcs. Megjelöli ezt a mezőt **SecureString** , hogy biztonságosan tárolja Data Factoryban, vagy [hivatkozjon a Azure Key Vault tárolt titkos kulcsra](store-credentials-in-key-vault.md). |Yes |
| sessionToken | Az [ideiglenes biztonsági hitelesítő adatok](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_credentials_temp.html) hitelesítésének használatakor alkalmazható. Ismerje meg, hogyan [kérhet ideiglenes biztonsági hitelesítő adatokat](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_credentials_temp_request.html#api_getsessiontoken) az AWS-ből.<br>Megjegyzés: az AWS ideiglenes hitelesítő adatai 15 perc és 36 óra között lejárnak a beállítások alapján. Ellenőrizze, hogy érvényes-e a hitelesítő adat a tevékenység végrehajtásakor, különösen a működés közbeni számítási feladatokhoz – például rendszeres időközönként frissítheti és tárolhatja Azure Key Vaultban.<br>Megjelöli ezt a mezőt **SecureString** , hogy biztonságosan tárolja Data Factoryban, vagy [hivatkozjon a Azure Key Vault tárolt titkos kulcsra](store-credentials-in-key-vault.md). |No |
| serviceUrl | Az egyéni S3-végpontot akkor válassza, ha a hivatalos Amazon S3 szolgáltatástól eltérő, S3-kompatibilis tárolási szolgáltatóról másol Adatmásolást. Az adatok Google Cloud Storage-ból való másolásához például a következőt kell megadnia: `https://storage.googleapis.com` . | No |
| forcePathStyle | Azt jelzi, hogy a virtuálisan üzemeltetett stílusú hozzáférés helyett az S3 [elérésiút-stílusú](https://docs.aws.amazon.com/AmazonS3/latest/dev/VirtualHosting.html#path-style-access) elérést kívánja-e használni. Az engedélyezett értékek: **false** (alapértelmezett), **true (igaz**).<br>Ha a hivatalos Amazon S3 szolgáltatástól eltérő, S3-kompatibilis tárolási szolgáltatóhoz csatlakozik, és az adattár eléréséhez elérésiút-stílusú hozzáférés szükséges (például [Oracle Cloud Storage](https://docs.oracle.com/iaas/Content/Object/Tasks/s3compatibleapi.htm)), állítsa a tulajdonságot True (igaz) értékre. Ha elérésiút-stílusú hozzáférésre van szükség, ellenőrizze, hogy minden adattár dokumentációja be van-e kapcsolva. |No |
| Connectvia tulajdonsággal | Az adattárhoz való csatlakozáshoz használt [integrációs](concepts-integration-runtime.md) modul. Használhatja az Azure Integration Runtime vagy a saját üzemeltetésű integrációs modult (ha az adattár egy magánhálózaton található). Ha ez a tulajdonság nincs megadva, a szolgáltatás az alapértelmezett Azure Integration Runtime-t használja. |No |

>[!TIP]
>Az egyéni S3 szolgáltatás URL-címének megadása, ha az adatok másolása nem a hivatalos Amazon S3-szolgáltatástól eltérő, S3-kompatibilis tárolóból történt.

**Példa: hozzáférési kulcs hitelesítésének használata**

```json
{
    "name": "AmazonS3LinkedService",
    "properties": {
        "type": "AmazonS3",
        "typeProperties": {
            "accessKeyId": "<access key id>",
            "secretAccessKey": {
                "type": "SecureString",
                "value": "<secret access key>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Példa: ideiglenes biztonsági hitelesítő adatok hitelesítésének használata**

```json
{
    "name": "AmazonS3LinkedService",
    "properties": {
        "type": "AmazonS3",
        "typeProperties": {
            "authenticationType": "TemporarySecurityCredentials",
            "accessKeyId": "<access key id>",
            "secretAccessKey": {
                "type": "SecureString",
                "value": "<secret access key>"
            },
            "sessionToken": {
                "type": "SecureString",
                "value": "<session token>"
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

Az Amazon S3 a következő tulajdonságokat támogatja a `location` Format-alapú adatkészlet beállításaiban:

| Tulajdonság   | Leírás                                                  | Kötelező |
| ---------- | ------------------------------------------------------------ | -------- |
| típus       | Az  `location` adatkészletben található Type tulajdonságot **AmazonS3Location** értékre kell állítani. | Yes      |
| bucketName | Az S3-gyűjtő neve.                                          | Yes      |
| folderPath | Az adott gyűjtőhöz tartozó mappa elérési útja. Ha helyettesítő karaktert szeretne használni a mappa szűréséhez, hagyja ki ezt a beállítást, és a tevékenység forrásának beállításainál válassza ki azt. | No       |
| fileName   | A fájl neve az adott gyűjtő és mappa elérési útjában. Ha helyettesítő karaktert szeretne használni a fájlok szűréséhez, hagyja ki ezt a beállítást, és a tevékenység forrásának beállításainál válassza ki azt. | No       |
| version | Az S3 objektum verziója, ha az S3 Verziószámozás engedélyezve van. Ha nincs megadva, a rendszer beolvassa a legújabb verziót. |No |

**Példa**

```json
{
    "name": "DelimitedTextDataset",
    "properties": {
        "type": "DelimitedText",
        "linkedServiceName": {
            "referenceName": "<Amazon S3 linked service name>",
            "type": "LinkedServiceReference"
        },
        "schema": [ < physical schema, optional, auto retrieved during authoring > ],
        "typeProperties": {
            "location": {
                "type": "AmazonS3Location",
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

A tevékenységek definiálásához elérhető csoportok és tulajdonságok teljes listáját a [folyamatok](concepts-pipelines-activities.md) című cikkben találja. Ez a szakasz az Amazon S3 forrás által támogatott tulajdonságok listáját tartalmazza.

### <a name="amazon-s3-as-a-source-type"></a>Amazon S3 forrás típusa

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

A következő tulajdonságok támogatottak az Amazon S3 `storeSettings` -ban a Format-alapú másolási forrás beállításai között:

| Tulajdonság                 | Leírás                                                  | Kötelező                                                    |
| ------------------------ | ------------------------------------------------------------ | ----------------------------------------------------------- |
| típus                     | A **Type** tulajdonságot a `storeSettings` **AmazonS3ReadSettings** értékre kell állítani. | Yes                                                         |
| ***Keresse meg a másolandó fájlokat:*** |  |  |
| 1. lehetőség: statikus elérési út<br> | Másolja az adatkészletben megadott gyűjtő vagy mappa vagy fájl elérési útját. Ha egy gyűjtőből vagy mappából kívánja átmásolni az összes fájlt, azt is meg kell adni `wildcardFileName` `*` . |  |
| 2. lehetőség: S3 előtag<br>-előtag | Az S3-kulcsnév előtagja a forrás S3-fájlok szűréséhez az adatkészletben konfigurált megadott gyűjtő alatt. Az S3-kulcsok, amelyeknek a neve kezdődik, `bucket_in_dataset/this_prefix` ki vannak választva. A szolgáltatás S3's, amely jobb teljesítményt nyújt, mint a helyettesítő karakteres szűrő.<br/><br/>Ha előtagot használ, és úgy dönt, hogy a fájl alapú fogadóba másol egy megőrzött hierarchiát, a rendszer megőrzi az utolsó "/" előtag utáni alútvonalat. Tegyük fel például, hogy rendelkezik forrással  `bucket/folder/subfolder/file.txt` , és konfigurálja az előtagot `folder/sub` , majd a megőrzött fájl elérési útja `subfolder/file.txt` . | No |
| 3. lehetőség: helyettesítő karakter<br>- wildcardFolderPath | A mappa elérési útja a forrás mappák szűréséhez az adatkészletben konfigurált megadott gyűjtőben lévő helyettesítő karakterekkel. <br>Az engedélyezett helyettesítő karakterek a következők: `*` (nulla vagy több karakternek felel meg) és `?` (nulla vagy egyetlen karakternek felel meg). `^`Ha a mappa neve helyettesítő karakter vagy a escape-karakter található, akkor a (z) használatával elkerülheti a menekülési karaktert. <br>További példákat a [mappák és a fájlok szűrésére szolgáló példákban](#folder-and-file-filter-examples)talál. | No                                            |
| 3. lehetőség: helyettesítő karakter<br>- wildcardFileName | Az adott gyűjtő és mappa elérési útján (vagy helyettesítő mappa elérési útján) található, helyettesítő karakterekkel rendelkező fájlnév a forrásfájlok szűréséhez. <br>Az engedélyezett helyettesítő karakterek a következők: `*` (nulla vagy több karakternek felel meg) és `?` (nulla vagy egyetlen karakternek felel meg). `^`Ha a fájl neve helyettesítő karakter vagy a escape-karakter található, akkor a (z) használatával elkerülheti a menekülési karaktert.  További példákat a [mappák és a fájlok szűrésére szolgáló példákban](#folder-and-file-filter-examples)talál. | Yes |
| 4. lehetőség: a fájlok listája<br>- fileListPath | Egy adott fájl másolását jelzi. Mutasson egy szövegfájlra, amely tartalmazza a másolni kívánt fájlok listáját, soronként egy fájlt, amely az adatkészletben konfigurált útvonal relatív elérési útja.<br/>Ha ezt a beállítást használja, ne adjon meg fájlnevet az adatkészletben. További példákat a [fájllista példákban](#file-list-examples)talál. |No |
| ***További beállítások:*** |  | |
| rekurzív | Azt jelzi, hogy az adatok rekurzív módon olvashatók-e az almappákból, vagy csak a megadott mappából. Vegye figyelembe, hogy ha a **rekurzív** értéke **true (igaz** ), a fogadó pedig egy fájl alapú tároló, a fogadó nem másolja vagy hozza létre az üres mappát vagy almappát. <br>Az engedélyezett értékek: **true** (alapértelmezett) és **false (hamis**).<br>Ez a tulajdonság nem érvényes a konfiguráláskor `fileListPath` . |No |
| deleteFilesAfterCompletion | Azt jelzi, hogy a rendszer törli-e a bináris fájlokat a forrás-áruházból, miután sikeresen áthelyezte a célhelyre. A fájl törlése fájl alapján történik, így ha a másolási tevékenység meghiúsul, néhány fájl már át lett másolva a célhelyre, és törlődik a forrásból, míg mások továbbra is a forrás-áruházban maradnak. <br/>Ez a tulajdonság csak bináris fájlok másolási forgatókönyv esetén érvényes. Az alapértelmezett érték: false. |No |
| modifiedDatetimeStart    | A fájlok a következő attribútum alapján vannak szűrve: utoljára módosítva. <br>A fájlok akkor lesznek kiválasztva, ha az utolsó módosítás időpontja a és a közötti időintervallumon belül van `modifiedDatetimeStart` `modifiedDatetimeEnd` . Az idő az UTC-időzónára vonatkozik "2018-12-01T05:00:00Z" formátumban. <br> A tulajdonságok lehetnek **Null értékűek**, ami azt jelenti, hogy a rendszer nem alkalmazza a file Attribute szűrőt az adatkészletre.  Ha a `modifiedDatetimeStart` dátum datetime értékkel rendelkezik `modifiedDatetimeEnd` , de **Null** értékű, akkor azok a fájlok lesznek kiválasztva, amelyek utolsó módosított attribútuma nagyobb vagy egyenlő, mint a DateTime érték.  Ha a `modifiedDatetimeEnd` dátum datetime értékkel rendelkezik `modifiedDatetimeStart` , de **Null** értékű, akkor azok a fájlok lesznek kiválasztva, amelyek utolsó módosítási attribútuma kisebb a DateTime értéknél.<br/>Ez a tulajdonság nem érvényes a konfiguráláskor `fileListPath` . | No                                            |
| modifiedDatetimeEnd      | Lásd fentebb.                                               | No                                                          |
| enablePartitionDiscovery | A particionált fájlok esetében adja meg, hogy szeretné-e elemezni a partíciókat a fájl elérési útján, majd adja hozzá őket további forrásként szolgáló oszlopként.<br/>Az engedélyezett értékek: **false** (alapértelmezett) és **true (igaz**). | No                                            |
| partitionRootPath | Ha engedélyezve van a partíciók felderítése, akkor a particionált mappák adatoszlopként való olvasásához a gyökér elérési útját kell megadni.<br/><br/>Ha nincs megadva, a rendszer alapértelmezés szerint<br/>– Ha a fájl elérési útját használja az adatkészletben vagy a forrásban található fájlok listáján, a partíció gyökerének elérési útja az adatkészletben konfigurált útvonal.<br/>– Ha helyettesítő mappa szűrőt használ, a partíció gyökerének elérési útja az első helyettesítő karakter előtti Alútvonal.<br/>– Ha előtagot használ, a partíció gyökerének elérési útja az utolsó "/" előtti Alútvonal. <br/><br/>Tegyük fel például, hogy az adatkészletben az elérési utat "root/Folder/Year = 2020/hónap = 08/Day = 27" értékre konfigurálja:<br/>– Ha a partíció gyökerének elérési útját "gyökér/mappa/év = 2020" értékre állítja, a másolási tevékenység két további oszlopot fog előállítani, `month` és a `day` "08" és "27" értéket is kijelöli a fájlokban lévő oszlopokon kívül.<br/>– Ha nincs megadva a partíció gyökerének elérési útja, nem jön létre további oszlop. | No                                            |
| maxConcurrentConnections | Az adattárhoz való egyidejű kapcsolatok száma. Csak akkor kell megadni, ha az adattárban való egyidejű kapcsolatokat szeretné korlátozni. | No                                                          |

**Példa**

```json
"activities":[
    {
        "name": "CopyFromAmazonS3",
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
                    "type": "AmazonS3ReadSettings",
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

Ez a szakasz azt ismerteti, hogyan történik a fájl-lista elérési útjának másolása egy másolási tevékenység forrásaként.

Tegyük fel, hogy rendelkezik a következő forrás-mappa struktúrával, és félkövéren szeretné átmásolni a fájlokat:

| Példa a forrás struktúrájára                                      | Tartalom FileListToCopy.txt                             | Data Factory konfiguráció                                            |
| ------------------------------------------------------------ | --------------------------------------------------------- | ------------------------------------------------------------ |
| gyűjtő<br/>&nbsp;&nbsp;&nbsp;&nbsp;Mappa<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File2.jsbekapcsolva<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.jsbekapcsolva<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Metaadatok<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;FileListToCopy.txt | File1.csv<br>Subfolder1/File3.csv<br>Subfolder1/File5.csv | **Az adatkészletben:**<br>Gyűjtő `bucket`<br>-Mappa elérési útja: `FolderA`<br><br>**A másolási tevékenység forrása:**<br>-Fájllista elérési útja: `bucket/Metadata/FileListToCopy.txt` <br><br>A fájllista elérési útja ugyanazon az adattárban található szövegfájlra mutat, amely tartalmazza a másolni kívánt fájlok listáját, a soronként egy fájlt, valamint az adatkészletben konfigurált útvonal relatív elérési útját. |

## <a name="preserve-metadata-during-copy"></a>Metaadatok megőrzése a másolás során

Amikor fájlokat másol az Amazon S3-ból Azure Data Lake Storage Gen2 vagy Azure Blob Storage-ba, dönthet úgy, hogy megőrzi a fájl metaadatait az adatokkal együtt. További információ: a [metaadatok megőrzése](copy-activity-preserve-metadata.md#preserve-metadata).

## <a name="lookup-activity-properties"></a>Keresési tevékenység tulajdonságai

A tulajdonságok részleteinek megismeréséhez tekintse meg a [keresési tevékenységet](control-flow-lookup-activity.md).

## <a name="getmetadata-activity-properties"></a>GetMetadata tevékenység tulajdonságai

A tulajdonságok részleteinek megismeréséhez tekintse meg a [GetMetaData tevékenységet](control-flow-get-metadata-activity.md). 

## <a name="delete-activity-properties"></a>Tevékenység tulajdonságainak törlése

A tulajdonságok részleteinek megismeréséhez tekintse meg a [törlési tevékenységet](delete-activity.md).

## <a name="legacy-models"></a>Örökölt modellek

>[!NOTE]
>A következő modellek továbbra is támogatottak a visszamenőleges kompatibilitás érdekében. Javasoljuk, hogy a korábban említett új modellt használja. Az Data Factory szerzői felhasználói felülete átváltott az új modell generálására.

### <a name="legacy-dataset-model"></a>Örökölt adatkészlet-modell

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| típus | Az adatkészlet **Type** tulajdonságát **AmazonS3Object** értékre kell állítani. |Yes |
| bucketName | Az S3-gyűjtő neve. A helyettesítő karakteres szűrő nem támogatott. |Igen, a másolási vagy keresési tevékenység esetében nem a GetMetadata tevékenységhez |
| kulcs | Az S3 objektum kulcsának neve vagy helyettesítő szűrője a megadott gyűjtőben. Csak akkor érvényes, ha az **előtag** tulajdonság nincs megadva. <br/><br/>A helyettesítő karakteres szűrő a mappa részeként és a fájlnév résznél is támogatott. Az engedélyezett helyettesítő karakterek a következők: `*` (nulla vagy több karakternek felel meg) és `?` (nulla vagy egyetlen karakternek felel meg).<br/>– 1. példa: `"key": "rootfolder/subfolder/*.csv"`<br/>– 2. példa: `"key": "rootfolder/subfolder/???20180427.txt"`<br/>További példa a [mappa-és fájlszűrő-példákban](#folder-and-file-filter-examples). A használatával `^` elkerülheti, hogy a tényleges mappája vagy fájlneve helyettesítő karakterrel vagy a menekülési karakterrel rendelkezik. |No |
| előtag | Az S3-objektum kulcsának előtagja. Azok az objektumok, amelyek esetében ezzel az előtaggal kezdődnek a kulcsok. Csak akkor érvényes, ha nincs megadva a **Key** tulajdonság. |No |
| version | Az S3 objektum verziója, ha az S3 Verziószámozás engedélyezve van. Ha nincs megadva verzió, a rendszer a legújabb verziót fogja lekérni. |No |
| modifiedDatetimeStart | A fájlok a következő attribútum alapján vannak szűrve: utoljára módosítva. A fájlok akkor lesznek kiválasztva, ha az utolsó módosítás időpontja a és a közötti időintervallumon belül van `modifiedDatetimeStart` `modifiedDatetimeEnd` . Az időpontot az UTC időzónára alkalmazza a "2018-12-01T05:00:00Z" formátumban. <br/><br/> Vegye figyelembe, hogy a beállítás engedélyezése hatással lesz az adatáthelyezés általános teljesítményére, ha nagy mennyiségű fájlt szeretne szűrni. <br/><br/> A tulajdonságok lehetnek **Null értékűek**, ami azt jelenti, hogy a rendszer nem alkalmazza a file Attribute szűrőt az adatkészletre.  Ha a `modifiedDatetimeStart` dátum datetime értékkel rendelkezik `modifiedDatetimeEnd` , de **Null** értékű, akkor azok a fájlok lesznek kiválasztva, amelyek utolsó módosított attribútuma nagyobb vagy egyenlő, mint a DateTime érték.  Ha a `modifiedDatetimeEnd` dátum datetime értékkel rendelkezik `modifiedDatetimeStart` , de null értékű, akkor azok a fájlok lesznek kiválasztva, amelyek utolsó módosítási attribútuma kisebb a DateTime értéknél.| No |
| modifiedDatetimeEnd | A fájlok a következő attribútum alapján vannak szűrve: utoljára módosítva. A fájlok akkor lesznek kiválasztva, ha az utolsó módosítás időpontja a és a közötti időintervallumon belül van `modifiedDatetimeStart` `modifiedDatetimeEnd` . Az időpontot az UTC időzónára alkalmazza a "2018-12-01T05:00:00Z" formátumban. <br/><br/> Vegye figyelembe, hogy a beállítás engedélyezése hatással lesz az adatáthelyezés általános teljesítményére, ha nagy mennyiségű fájlt szeretne szűrni. <br/><br/> A tulajdonságok lehetnek **Null értékűek**, ami azt jelenti, hogy a rendszer nem alkalmazza a file Attribute szűrőt az adatkészletre.  Ha a `modifiedDatetimeStart` dátum datetime értékkel rendelkezik `modifiedDatetimeEnd` , de **Null** értékű, akkor azok a fájlok lesznek kiválasztva, amelyek utolsó módosított attribútuma nagyobb vagy egyenlő, mint a DateTime érték.  Ha a `modifiedDatetimeEnd` dátum datetime értékkel rendelkezik `modifiedDatetimeStart` , de **Null** értékű, akkor azok a fájlok lesznek kiválasztva, amelyek utolsó módosítási attribútuma kisebb a DateTime értéknél.| No |
| formátumban | Ha fájlokat szeretne másolni a fájl alapú tárolók között (bináris másolás), ugorja át a formátum szakaszt mind a bemeneti, mind a kimeneti adatkészlet-definíciókban.<br/><br/>Ha a fájlokat egy adott formátummal szeretné elemezni vagy előállítani, a következő fájlformátum-típusok támogatottak: **Szövegformátum**, **JsonFormat**, **AvroFormat**, **OrcFormat**, **ParquetFormat**. A **Type (típus** ) tulajdonságot állítsa a **Format** értékre a következő értékek egyikére. További információ: [szöveg formátuma](supported-file-formats-and-compression-codecs-legacy.md#text-format), JSON- [Formátum](supported-file-formats-and-compression-codecs-legacy.md#json-format), [Avro formátum](supported-file-formats-and-compression-codecs-legacy.md#avro-format), [ork-formátum](supported-file-formats-and-compression-codecs-legacy.md#orc-format)és [parketta formátuma](supported-file-formats-and-compression-codecs-legacy.md#parquet-format) . |Nem (csak bináris másolási forgatókönyv esetén) |
| tömörítés | Adja meg az adattömörítés típusát és szintjét. További információ: [támogatott fájlformátumok és tömörítési kodekek](supported-file-formats-and-compression-codecs-legacy.md#compression-support).<br/>A támogatott típusok a **gzip**, a **deflate**, a **BZip2** és a **ZipDeflate**.<br/>A támogatott szintek **optimálisak** és **leggyorsabbak**. |No |

>[!TIP]
>Ha egy mappa összes fájlját át szeretné másolni, akkor a **bucketName** meg kell adnia a mappa részét képező gyűjtőhöz és **előtaghoz** .
>
>Egy adott névvel rendelkező egyetlen fájl másolásához adja meg a **bucketName** a mappa részeként **, valamint a** fájl nevét.
>
>Ha egy mappában lévő fájlok egy részhalmazát szeretné másolni, válassza a **bucketName** lehetőséget a mappa részeként **, valamint a** helyettesítő karakteres szűrőt.

**Példa: előtag használata**

```json
{
    "name": "AmazonS3Dataset",
    "properties": {
        "type": "AmazonS3Object",
        "linkedServiceName": {
            "referenceName": "<Amazon S3 linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "bucketName": "testbucket",
            "prefix": "testFolder/test",
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

**Példa: kulcs és verzió használata (nem kötelező)**

```json
{
    "name": "AmazonS3Dataset",
    "properties": {
        "type": "AmazonS3",
        "linkedServiceName": {
            "referenceName": "<Amazon S3 linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "bucketName": "testbucket",
            "key": "testFolder/testfile.csv.gz",
            "version": "XXXXXXXXXczm0CJajYkHf0_k6LhBmkcL",
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

### <a name="legacy-source-model-for-the-copy-activity"></a>A másolási tevékenységhez használt örökölt forrásoldali modell

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| típus | A másolási tevékenység forrásának **Type** tulajdonságát **FileSystemSource** értékre kell állítani. |Yes |
| rekurzív | Azt jelzi, hogy az adatok rekurzív módon olvashatók-e az almappákból, vagy csak a megadott mappából. Vegye figyelembe, hogy ha a **rekurzív** értéke **true (igaz** ), a fogadó pedig egy fájl alapú tároló, akkor a fogadó nem másolja vagy hozza létre az üres mappát vagy almappát.<br/>Az engedélyezett értékek: **true** (alapértelmezett) és **false (hamis**). | No |
| maxConcurrentConnections | Az adattárhoz való kapcsolódáshoz szükséges kapcsolatok száma. Csak akkor kell megadni, ha az adattárban való egyidejű kapcsolatokat szeretné korlátozni. | No |

**Példa**

```json
"activities":[
    {
        "name": "CopyFromAmazonS3",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Amazon S3 input dataset name>",
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

## <a name="next-steps"></a>Következő lépések
A Azure Data Factory másolási tevékenység által forrásként és fogadóként támogatott adattárak listájáért lásd: [támogatott adattárak](copy-activity-overview.md#supported-data-stores-and-formats).
