---
title: Adatok másolása SFTP-kiszolgálóra
description: Megtudhatja, hogyan másolhat adatok a és az SFTP-kiszolgálóra Azure Data Factory használatával.
ms.author: jingwang
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 03/17/2021
ms.openlocfilehash: 19b32bed15a4d292a7427d8401e777c7761e45a3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "104592030"
---
# <a name="copy-data-from-and-to-the-sftp-server-by-using-azure-data-factory"></a>Adatok másolása a és az SFTP-kiszolgálóra Azure Data Factory használatával

> [!div class="op_single_selector" title1="Válassza ki a használni kívánt Data Factory-szolgáltatás verzióját:"]
> * [1-es verzió](v1/data-factory-sftp-connector.md)
> * [Aktuális verzió](connector-sftp.md)
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Ez a cikk az adatok és a biztonságos FTP (SFTP) kiszolgálóra történő másolását ismerteti. A Azure Data Factoryről a [bevezető cikkben](introduction.md)olvashat bővebben.

## <a name="supported-capabilities"></a>Támogatott képességek

Az SFTP-összekötő a következő tevékenységeknél támogatott:

- [Másolási tevékenység](copy-activity-overview.md) [támogatott forrás/fogadó mátrixtal](copy-activity-overview.md)
- [Keresési tevékenység](control-flow-lookup-activity.md)
- [GetMetadata tevékenység](control-flow-get-metadata-activity.md)
- [Tevékenység törlése](delete-activity.md)

Az SFTP-összekötő a következőket támogatja:

- Fájlok másolása az SFTP-kiszolgálóra az **alapszintű**, az **SSH nyilvános kulcs** vagy a **többtényezős** hitelesítés használatával.
- A fájlok másolása, illetve a [támogatott fájlformátumokat és tömörítési kodekeket](supported-file-formats-and-compression-codecs.md)tartalmazó fájlok elemzése vagy létrehozása.

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

## <a name="get-started"></a>Bevezetés

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

A következő szakaszokban részletesen ismertetjük az SFTP-re vonatkozó Data Factory-entitások definiálásához használt tulajdonságokat.

## <a name="linked-service-properties"></a>Társított szolgáltatás tulajdonságai

Az SFTP társított szolgáltatás a következő tulajdonságokat támogatja:

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| típus | A Type tulajdonságot az *SFTP* értékre kell beállítani. |Yes |
| gazda | Az SFTP-kiszolgáló neve vagy IP-címe. |Yes |
| port | Az a port, amelyen az SFTP-kiszolgáló figyel.<br/>Az engedélyezett érték egész szám, az alapértelmezett érték pedig *22*. |No |
| skipHostKeyValidation | Megadhatja, hogy kihagyja-e a gazdagép kulcsának érvényesítését<br/>Az engedélyezett értékek: *true* és *false* (alapértelmezett).  | No |
| hostKeyFingerprint | A gazda kulcs ujjlenyomatának meghatározása. | Igen, ha a "skipHostKeyValidation" értéke false (hamis).  |
| authenticationType | Adja meg a hitelesítési típust.<br/>Az engedélyezett értékek az *alapszintű*, a *SshPublicKey* és a *többtényezős*. További tulajdonságok: az [Egyszerű hitelesítés használata](#use-basic-authentication) szakasz. JSON-példákat az [SSH nyilvános kulcsú hitelesítés használata](#use-ssh-public-key-authentication) című szakaszban talál. |Yes |
| Connectvia tulajdonsággal | Az adattárhoz való csatlakozáshoz használt [integrációs](concepts-integration-runtime.md) modul. További információt az [Előfeltételek](#prerequisites) című szakaszban talál. Ha nincs megadva az Integration Runtime, a szolgáltatás az alapértelmezett Azure Integration Runtime használja. |No |

### <a name="use-basic-authentication"></a>Egyszerű hitelesítés használata

Az alapszintű hitelesítés használatához állítsa a *authenticationType* tulajdonságot alapértékre, és adja meg az alábbi TULAJDONSÁGOKAT az SFTP-összekötő általános tulajdonságai *mellett, amelyek* az előző szakaszban lettek bevezetve:

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| userName (Felhasználónév) | Az a felhasználó, aki hozzáfér az SFTP-kiszolgálóhoz. |Yes |
| jelszó | A felhasználó jelszava (userName). Megjelöli ezt a mezőt SecureString, hogy biztonságosan tárolja azt az adatgyárban, vagy [hivatkozzon az Azure Key vaultban tárolt titkos kulcsra](store-credentials-in-key-vault.md). | Yes |

**Példa**

```json
{
    "name": "SftpLinkedService",
    "properties": {
        "type": "Sftp",
        "typeProperties": {
            "host": "<sftp server>",
            "port": 22,
            "skipHostKeyValidation": false,
            "hostKeyFingerPrint": "ssh-rsa 2048 xx:00:00:00:xx:00:x0:0x:0x:0x:0x:00:00:x0:x0:00",
            "authenticationType": "Basic",
            "userName": "<username>",
            "password": {
                "type": "SecureString",
                "value": "<password>"
            }
        },
        "connectVia": {
            "referenceName": "<name of integration runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="use-ssh-public-key-authentication"></a>Nyilvános SSH-kulcsos hitelesítés használata

Az SSH nyilvános kulcsú hitelesítés használatához állítsa a "authenticationType" tulajdonságot **SshPublicKey** értékre, és adja meg a következő TULAJDONSÁGOKAT az SFTP-összekötő általános beállításai mellett, az utolsó szakaszban bemutatottak szerint:

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| userName (Felhasználónév) | Az a felhasználó, aki hozzáfér az SFTP-kiszolgálóhoz. |Yes |
| privateKeyPath | Itt adhatja meg az integrációs modul által elérhető titkos kulcsfájl abszolút elérési útját. Ez csak akkor érvényes, ha az integrációs modul helyi típusa "Connectvia tulajdonsággal" van megadva. | Adjon meg `privateKeyPath` vagy `privateKeyContent` .  |
| privateKeyContent | Base64 kódolású titkos SSH-kulcs tartalma. Az SSH titkos kulcsának OpenSSH formátumúnak kell lennie. Megjelöli ezt a mezőt SecureString, hogy biztonságosan tárolja azt az adatgyárban, vagy [hivatkozzon az Azure Key vaultban tárolt titkos kulcsra](store-credentials-in-key-vault.md). | Adjon meg `privateKeyPath` vagy `privateKeyContent` . |
| Jelszót | A titkos kulcs visszafejtéséhez adja meg a pass kifejezést vagy a jelszót, ha a kulcsfájl vagy a kulcs tartalmát egy pass kifejezés védi. Megjelöli ezt a mezőt SecureString, hogy biztonságosan tárolja azt az adatgyárban, vagy [hivatkozzon az Azure Key vaultban tárolt titkos kulcsra](store-credentials-in-key-vault.md). | Igen, ha a titkos kulcs fájlját vagy a kulcs tartalmát egy pass kifejezés védi. |

> [!NOTE]
> Az SFTP-összekötő támogatja az RSA/DSA OpenSSH-kulcsot. Győződjön meg arról, hogy a kulcsfájl tartalma a következővel kezdődik: "-----BEGIN [RSA/DSA] titkos kulcs-----". Ha a titkos kulcsfájl PPK-formátumú fájl, a PuTTY eszköz használatával alakítsa át a PPK-ből az OpenSSH formátumba. 

**1. példa: SshPublicKey-hitelesítés a titkos kulcs filePath használatával**

```json
{
    "name": "SftpLinkedService",
    "properties": {
        "type": "Sftp",
        "typeProperties": {
            "host": "<sftp server>",
            "port": 22,
            "skipHostKeyValidation": true,
            "authenticationType": "SshPublicKey",
            "userName": "xxx",
            "privateKeyPath": "D:\\privatekey_openssh",
            "passPhrase": {
                "type": "SecureString",
                "value": "<pass phrase>"
            }
        },
        "connectVia": {
            "referenceName": "<name of integration runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**2. példa: SshPublicKey hitelesítés a titkos kulcs tartalmának használatával**

```json
{
    "name": "SftpLinkedService",
    "type": "Linkedservices",
    "properties": {
        "type": "Sftp",
        "typeProperties": {
            "host": "<sftp server>",
            "port": 22,
            "skipHostKeyValidation": true,
            "authenticationType": "SshPublicKey",
            "userName": "<username>",
            "privateKeyContent": {
                "type": "SecureString",
                "value": "<base64 string of the private key content>"
            },
            "passPhrase": {
                "type": "SecureString",
                "value": "<pass phrase>"
            }
        },
        "connectVia": {
            "referenceName": "<name of integration runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="use-multi-factor-authentication"></a>Multi-Factor Authentication használata

Ha a többtényezős hitelesítést szeretné használni, amely az alapszintű és az SSH nyilvános kulcsú hitelesítés kombinációja, adja meg a fenti szakaszokban leírt felhasználónevet, jelszót és a titkos kulcshoz tartozó adatokat.

**Példa: Multi-Factor Authentication**

```json
{
    "name": "SftpLinkedService",
    "properties": {
        "type": "Sftp",
        "typeProperties": {
            "host": "<host>",
            "port": 22,
            "authenticationType": "MultiFactor",
            "userName": "<username>",
            "password": {
                "type": "SecureString",
                "value": "<password>"
            },
            "privateKeyContent": {
                "type": "SecureString",
                "value": "<base64 encoded private key content>"
            },
            "passPhrase": {
                "type": "SecureString",
                "value": "<passphrase for private key>"
            }
        },
        "connectVia": {
            "referenceName": "<name of integration runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>Adatkészlet tulajdonságai

Az adatkészletek definiálásához rendelkezésre álló csoportok és tulajdonságok teljes listáját az [adatkészletek](concepts-datasets-linked-services.md) című cikkben találja. 

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

A következő tulajdonságokat támogatja az SFTP a `location` Format-alapú adatkészlet beállítások területén:

| Tulajdonság   | Leírás                                                  | Kötelező |
| ---------- | ------------------------------------------------------------ | -------- |
| típus       | Az  `location` adatkészletben található Type tulajdonságot *SftpLocation* értékre kell állítani. | Yes      |
| folderPath | A mappa elérési útja. Ha a mappa szűréséhez helyettesítő karaktert szeretne használni, hagyja ki ezt a beállítást, és határozza meg az elérési utat a tevékenység forrásának beállításai között. | No       |
| fileName   | A megadott folderPath tartozó fájlnév. Ha helyettesítő karaktert szeretne használni a fájlok szűréséhez, hagyja ki ezt a beállítást, és adja meg a fájl nevét a tevékenység forrásának beállításaiban. | No       |

**Példa**

```json
{
    "name": "DelimitedTextDataset",
    "properties": {
        "type": "DelimitedText",
        "linkedServiceName": {
            "referenceName": "<SFTP linked service name>",
            "type": "LinkedServiceReference"
        },
        "schema": [ < physical schema, optional, auto retrieved during authoring > ],
        "typeProperties": {
            "location": {
                "type": "SftpLocation",
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

A tevékenységek definiálásához elérhető csoportok és tulajdonságok teljes listáját a [folyamatok](concepts-pipelines-activities.md) című cikkben találja. Ez a szakasz az SFTP-forrás által támogatott tulajdonságok listáját tartalmazza.

### <a name="sftp-as-source"></a>SFTP forrásként

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

Az SFTP `storeSettings` Formátum-alapú másolási forrás beállításai alatt a következő tulajdonságok támogatottak:

| Tulajdonság                 | Leírás                                                  | Kötelező                                      |
| ------------------------ | ------------------------------------------------------------ | --------------------------------------------- |
| típus                     | A *Type* tulajdonságot a `storeSettings` *SftpReadSettings* értékre kell állítani. | Yes                                           |
| ***A másolandó fájlok megkeresése*** |  |  |
| 1. lehetőség: statikus elérési út<br> | Másolja az adatkészletben megadott mappa vagy fájl elérési útját. Ha az összes fájlt egy mappából szeretné másolni, azt is meg kell adnia `wildcardFileName` `*` . |  |
| 2. lehetőség: helyettesítő karakter<br>- wildcardFolderPath | A mappa elérési útja helyettesítő karakterekkel a forrás mappák szűréséhez. <br>Az engedélyezett helyettesítő karakterek `*` (nulla vagy több karakternek felelnek meg) és `?` (a nulla vagy egyetlen karakternek felelnek meg); `^` Ha a tényleges mappa neve helyettesítő karakter, vagy a escape-karakter szerepel a következőben, használja a Escape karaktert. <br>További példákat a [mappa-és fájlszűrő-példák](#folder-and-file-filter-examples)című témakörben talál. | No                                            |
| 2. lehetőség: helyettesítő karakter<br>- wildcardFileName | A forrásfájl szűréséhez a megadott folderPath/wildcardFolderPath helyettesítő karaktereket tartalmazó fájlnév. <br>Az engedélyezett helyettesítő karakterek `*` (nulla vagy több karakternek felelnek meg) és `?` (a nulla vagy egyetlen karakternek felelnek meg); `^` Ha a tényleges fájl neve helyettesítő karakter, vagy a escape-karakter szerepel a következőben, használja a Escape karaktert.  További példákat a [mappa-és fájlszűrő-példák](#folder-and-file-filter-examples)című témakörben talál. | Yes |
| 3. lehetőség: a fájlok listája<br>- fileListPath | Egy megadott fájl másolását jelzi. Mutasson egy szövegfájlra, amely tartalmazza a másolni kívánt fájlok listáját (egy-egy fájl soronként, az adatkészletben konfigurált elérési út relatív elérési útjával).<br/>Ha ezt a beállítást használja, ne adja meg a fájl nevét az adatkészletben. További példákat itt talál: [fájllista példák](#file-list-examples). |No |
| ***További beállítások*** |  | |
| rekurzív | Azt jelzi, hogy az adatok rekurzív módon olvashatók-e az almappákból, vagy csak a megadott mappából. Ha a rekurzív értéke TRUE (igaz), a fogadó pedig egy fájl alapú tároló, akkor a fogadó nem másol vagy hoz létre üres mappát vagy almappát. <br>Az engedélyezett értékek: *true* (alapértelmezett) és *false (hamis*).<br>Ez a tulajdonság nem érvényes a konfiguráláskor `fileListPath` . |No |
| deleteFilesAfterCompletion | Azt jelzi, hogy a rendszer törli-e a bináris fájlokat a forrás-áruházból, miután sikeresen áthelyezte a célhelyre. A fájl törlése fájl alapján történik, így ha a másolási tevékenység meghiúsul, néhány fájl már át lett másolva a célhelyre, és törlődik a forrásból, míg mások továbbra is a forrás-áruházban maradnak. <br/>Ez a tulajdonság csak bináris fájlok másolási forgatókönyv esetén érvényes. Az alapértelmezett érték: false. |No |
| modifiedDatetimeStart    | A fájlok szűrése a *legutóbb módosított* attribútum alapján történik. <br>A fájlok akkor vannak kiválasztva, ha az utolsó módosítás időpontja a következő tartományon belül van: `modifiedDatetimeStart` `modifiedDatetimeEnd` . Az idő az UTC-időzónára vonatkozik *2018-12-01T05:00:00Z* formátumban. <br> A tulajdonságok lehetnek NULL értékűek, ami azt jelenti, hogy a rendszer nem alkalmazza a file Attribute szűrőt az adatkészletre.  Ha a `modifiedDatetimeStart` dátum datetime értékkel rendelkezik `modifiedDatetimeEnd` , de null értékű, az azt jelenti, hogy azok a fájlok vannak kiválasztva, amelyek utolsó módosítási attribútuma nagyobb vagy egyenlő, mint a DateTime érték.  Ha a `modifiedDatetimeEnd` dátum datetime értékkel rendelkezik `modifiedDatetimeStart` , de null értékű, az azt jelenti, hogy azok a fájlok vannak kiválasztva, amelyek utolsó módosítási attribútuma kisebb a DateTime értéknél.<br/>Ez a tulajdonság nem érvényes a konfiguráláskor `fileListPath` . | No                                            |
| modifiedDatetimeEnd      | Lásd fentebb.                                               | No                                            |
| enablePartitionDiscovery | A particionált fájlok esetében adja meg, hogy szeretné-e elemezni a partíciókat a fájl elérési útján, majd adja hozzá őket további forrásként szolgáló oszlopként.<br/>Az engedélyezett értékek: **false** (alapértelmezett) és **true (igaz**). | No                                            |
| partitionRootPath | Ha engedélyezve van a partíciók felderítése, akkor a particionált mappák adatoszlopként való olvasásához a gyökér elérési útját kell megadni.<br/><br/>Ha nincs megadva, a rendszer alapértelmezés szerint<br/>– Ha a fájl elérési útját használja az adatkészletben vagy a forrásban található fájlok listáján, a partíció gyökerének elérési útja az adatkészletben konfigurált útvonal.<br/>– Ha helyettesítő mappa szűrőt használ, a partíció gyökerének elérési útja az első helyettesítő karakter előtti Alútvonal.<br/><br/>Tegyük fel például, hogy az adatkészletben az elérési utat "root/Folder/Year = 2020/hónap = 08/Day = 27" értékre konfigurálja:<br/>– Ha a partíció gyökerének elérési útját "gyökér/mappa/év = 2020" értékre állítja, a másolási tevékenység két további oszlopot fog előállítani, `month` és a `day` "08" és "27" értéket is kijelöli a fájlokban lévő oszlopokon kívül.<br/>– Ha nincs megadva a partíció gyökerének elérési útja, nem jön létre további oszlop. | No                                            |
| maxConcurrentConnections | A tevékenység futtatása során az adattárhoz létesített egyidejű kapcsolatok felső határa. Csak akkor adhat meg értéket, ha korlátozni szeretné az egyidejű kapcsolatokat.| No                                            |

**Példa**

```json
"activities":[
    {
        "name": "CopyFromSFTP",
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
                    "type": "SftpReadSettings",
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

### <a name="sftp-as-a-sink"></a>SFTP fogadóként

[!INCLUDE [data-factory-v2-file-sink-formats](../../includes/data-factory-v2-file-sink-formats.md)]

A következő tulajdonságok támogatottak az SFTP `storeSettings` Formátum alapú másolási fogadóban lévő beállítások területen:

| Tulajdonság                 | Leírás                                                  | Kötelező |
| ------------------------ | ------------------------------------------------------------ | -------- |
| típus                     | A *Type* tulajdonságot a `storeSettings` *SftpWriteSettings* értékre kell állítani. | Yes      |
| copyBehavior             | Meghatározza a másolási viselkedést, ha a forrás fájl-alapú adattárból származó fájlok.<br/><br/>Az engedélyezett értékek a következők:<br/><b>-PreserveHierarchy (alapértelmezett)</b>: megőrzi a fájl-hierarchiát a célmappában. A forrásfájl relatív elérési útja a forrás mappájához azonos a célfájl relatív elérési útjával.<br/><b>-FlattenHierarchy</b>: a forrás mappából származó összes fájl a célmappa első szintjén van. A célként megadott fájlok automatikusan generált névvel rendelkeznek. <br/><b>-MergeFiles</b>: az összes fájlt egyesíti a forrás mappájából egy fájlba. Ha meg van adva a fájl neve, az egyesített fájl neve a megadott név. Ellenkező esetben ez egy automatikusan létrehozott fájl neve. | No       |
| maxConcurrentConnections | A tevékenység futtatása során az adattárhoz létesített egyidejű kapcsolatok felső határa. Csak akkor adhat meg értéket, ha korlátozni szeretné az egyidejű kapcsolatokat. | No       |
| useTempFileRename | Jelezze, hogy az ideiglenes fájlokra kíván-e feltölteni, majd átnevezni, vagy közvetlenül a célmappába vagy a fájl helyére írja. Alapértelmezés szerint a Azure Data Factory először az ideiglenes fájlokba ír, majd átnevezi őket a feltöltés befejezésekor. Ez a szakasz segít (1) elkerülni az olyan ütközéseket, amelyek sérült fájlokat okozhatnak, ha más folyamatokat ír ugyanarra a fájlra, és (2) győződjön meg arról, hogy a fájl eredeti verziója létezik az átvitel során. Ha az SFTP-kiszolgáló nem támogatja az átnevezési műveletet, tiltsa le ezt a beállítást, és győződjön meg arról, hogy nem rendelkezik párhuzamos írási fájllal a célfájl számára. További információkért tekintse meg a tábla végén található hibaelhárítási tippet. | Nem. Az alapértelmezett érték *true (igaz*). |
| operationTimeout | Az a várakozási idő, ameddig az SFTP-kiszolgálóra való írási kérelem időtúllépése megtörténjen. Az alapértelmezett érték 60 perc (01:00:00).|No |

>[!TIP]
>Ha a "UserErrorSftpPathNotFound", "UserErrorSftpPermissionDenied" vagy "SftpOperationFail" hibaüzenet jelenik meg az SFTP-be való írás során, és *a használt SFTP-felhasználó rendelkezik a* megfelelő engedélyekkel, ellenőrizze, hogy működik-e az SFTP-kiszolgáló a fájl átnevezési műveletének támogatásával. Ha nem, tiltsa le a **feltöltés a temp file** ( `useTempFileRename` ) kapcsolóval, és próbálkozzon újra. A tulajdonsággal kapcsolatos további tudnivalókért tekintse meg az előző táblázatot. Ha a másolási tevékenységhez saját üzemeltetésű integrációs modult használ, ügyeljen arra, hogy a 4,6-es vagy újabb verziót használja.

**Példa**

```json
"activities":[
    {
        "name": "CopyToSFTP",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
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
                "type": "<source type>"
            },
            "sink": {
                "type": "BinarySink",
                "storeSettings":{
                    "type": "SftpWriteSettings",
                    "copyBehavior": "PreserveHierarchy"
                }
            }
        }
    }
]
```

### <a name="folder-and-file-filter-examples"></a>Példák a mappák és a fájlok szűrésére

Ez a szakasz azt a viselkedést ismerteti, amely a mappák elérési útjaival és fájlnevekkel rendelkező helyettesítő szűrők használatával jár.

| folderPath | fileName | rekurzív | A forrás mappa szerkezete és a szűrő eredménye (a **félkövérrel szedett** fájlok beolvasása)|
|:--- |:--- |:--- |:--- |
| `Folder*` | (üres, alapértelmezett használata) | hamis | Mappa<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File2.jsbekapcsolva**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.jsbekapcsolva<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5.csv<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `Folder*` | (üres, alapértelmezett használata) | true | Mappa<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File2.jsbekapcsolva**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File4.jsbekapcsolva**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `Folder*` | `*.csv` | hamis | Mappa<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2.jsbekapcsolva<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.jsbekapcsolva<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5.csv<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `Folder*` | `*.csv` | true | Mappa<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2.jsbekapcsolva<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.jsbekapcsolva<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |

### <a name="file-list-examples"></a>Példák a fájllista

Ez a táblázat a másolási tevékenység forrásában lévő fájllista elérési útját eredményező viselkedést ismerteti. Feltételezi, hogy rendelkezik a következő forrás-mappa struktúrával, és a félkövéren szedett fájlokat szeretné másolni:

| Példa a forrás struktúrájára                                      | Tartalom FileListToCopy.txt                             | Azure Data Factory konfiguráció                                            |
| ------------------------------------------------------------ | --------------------------------------------------------- | ------------------------------------------------------------ |
| legfelső szintű<br/>&nbsp;&nbsp;&nbsp;&nbsp;Mappa<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File2.jsbekapcsolva<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.jsbekapcsolva<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Metaadatok<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;FileListToCopy.txt | File1.csv<br>Subfolder1/File3.csv<br>Subfolder1/File5.csv | **Az adatkészletben:**<br>-Mappa elérési útja: `root/FolderA`<br><br>**A másolási tevékenység forrása:**<br>-Fájllista elérési útja: `root/Metadata/FileListToCopy.txt` <br><br>A fájllista elérési útja ugyanazon az adattárban található szövegfájlra mutat, amely tartalmazza a másolni kívánt fájlok listáját (egy sor, soronként egy fájl, az adatkészletben konfigurált elérési út relatív elérési útját). |

## <a name="lookup-activity-properties"></a>Keresési tevékenység tulajdonságai

További információ a keresési tevékenység tulajdonságairól: [keresési tevékenység Azure Data Factoryban](control-flow-lookup-activity.md).

## <a name="getmetadata-activity-properties"></a>GetMetadata tevékenység tulajdonságai

További információ a GetMetadata tevékenység tulajdonságairól: [GetMetaData Activity in Azure Data Factory](control-flow-get-metadata-activity.md). 

## <a name="delete-activity-properties"></a>Tevékenység tulajdonságainak törlése

A törlési tevékenység tulajdonságaival kapcsolatos információkért lásd: [tevékenység törlése a Azure Data Factoryban](delete-activity.md).

## <a name="legacy-models"></a>Örökölt modellek

>[!NOTE]
>A következő modellek továbbra is támogatottak a visszamenőleges kompatibilitás érdekében. Javasoljuk, hogy a korábban tárgyalt új modellt használja, mert a Azure Data Factory szerzői felhasználói felülete átváltott az új modell generálására.

### <a name="legacy-dataset-model"></a>Örökölt adatkészlet-modell

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| típus | Az adatkészlet *Type* tulajdonságát *fájlmegosztás* értékre kell állítani. |Yes |
| folderPath | A mappa elérési útja. Helyettesítő karakteres szűrő használata támogatott. Az engedélyezett helyettesítő karakterek `*` (nulla vagy több karakternek felelnek meg) és `?` (a nulla vagy egyetlen karakternek felelnek meg); `^` Ha a tényleges fájl neve helyettesítő karakter, vagy a escape-karakter szerepel a következőben, használja a Escape karaktert. <br/><br/>Példák: gyökérmappa/almappa/, további példák a [mappák és a fájlok szűrése példákban](#folder-and-file-filter-examples). |Yes |
| fileName |  A megadott "folderPath" alatt lévő fájlok **neve vagy helyettesítő karakteres szűrője** . Ha nem ad meg értéket ehhez a tulajdonsághoz, az adatkészlet a mappában található összes fájlra mutat. <br/><br/>A szűrő esetében az engedélyezett helyettesítő karakterek `*` (nulla vagy több karakternek felelnek meg) és `?` (nulla vagy egyetlen karakter).<br/>– 1. példa: `"fileName": "*.csv"`<br/>– 2. példa: `"fileName": "???20180427.txt"`<br/>A (z `^` ) használatával elkerülheti a mappát, ha az aktuális mappanév helyettesítő karakterrel rendelkezik, vagy a menekülési karakter szerepel a |No |
| modifiedDatetimeStart | A fájlok szűrése a *legutóbb módosított* attribútum alapján történik. A fájlok akkor vannak kiválasztva, ha az utolsó módosítás időpontja a következő tartományon belül van: `modifiedDatetimeStart` `modifiedDatetimeEnd` . Az idő az UTC-időzónára vonatkozik *2018-12-01T05:00:00Z* formátumban. <br/><br/> Ha nagy számú fájlból szeretne szűrni, a rendszer az adatáthelyezés általános teljesítményét fogja érinteni. <br/><br/> A tulajdonságok lehetnek NULL értékűek, ami azt jelenti, hogy a rendszer nem alkalmazza a file Attribute szűrőt az adatkészletre.  Ha a `modifiedDatetimeStart` dátum datetime értékkel rendelkezik `modifiedDatetimeEnd` , de null értékű, az azt jelenti, hogy azok a fájlok vannak kiválasztva, amelyek utolsó módosítási attribútuma nagyobb vagy egyenlő, mint a DateTime érték.  Ha a `modifiedDatetimeEnd` dátum datetime értékkel rendelkezik `modifiedDatetimeStart` , de null értékű, az azt jelenti, hogy azok a fájlok vannak kiválasztva, amelyek utolsó módosítási attribútuma kisebb a DateTime értéknél.| No |
| modifiedDatetimeEnd | A fájlok szűrése a *legutóbb módosított* attribútum alapján történik. A fájlok akkor vannak kiválasztva, ha az utolsó módosítás időpontja a következő tartományon belül van: `modifiedDatetimeStart` `modifiedDatetimeEnd` . Az idő az UTC-időzónára vonatkozik *2018-12-01T05:00:00Z* formátumban. <br/><br/> Ha nagy számú fájlból szeretne szűrni, a rendszer az adatáthelyezés általános teljesítményét fogja érinteni. <br/><br/> A tulajdonságok lehetnek NULL értékűek, ami azt jelenti, hogy a rendszer nem alkalmazza a file Attribute szűrőt az adatkészletre.  Ha a `modifiedDatetimeStart` dátum datetime értékkel rendelkezik `modifiedDatetimeEnd` , de null értékű, az azt jelenti, hogy azok a fájlok vannak kiválasztva, amelyek utolsó módosítási attribútuma nagyobb vagy egyenlő, mint a DateTime érték.  Ha a `modifiedDatetimeEnd` dátum datetime értékkel rendelkezik `modifiedDatetimeStart` , de null értékű, az azt jelenti, hogy azok a fájlok vannak kiválasztva, amelyek utolsó módosítási attribútuma kisebb a DateTime értéknél.| No |
| formátumban | Ha fájlokat szeretne másolni a fájl alapú tárolók között (bináris másolás), ugorja át a formátum szakaszt mind a bemeneti, mind a kimeneti adatkészlet-definíciókban.<br/><br/>Ha a fájlokat egy adott formátummal szeretné elemezni, a következő fájlformátum-típusok támogatottak: *Szövegformátum*, *JsonFormat*, *AvroFormat*, *OrcFormat* és *ParquetFormat*. A *Type (típus* ) tulajdonságot állítsa a Format értékre a következő értékek egyikére. További információkért lásd: [Szövegformátum](supported-file-formats-and-compression-codecs-legacy.md#text-format), JSON- [Formátum](supported-file-formats-and-compression-codecs-legacy.md#json-format), [Avro formátum](supported-file-formats-and-compression-codecs-legacy.md#avro-format), [ork-formátum](supported-file-formats-and-compression-codecs-legacy.md#orc-format)és a [parketta formátuma](supported-file-formats-and-compression-codecs-legacy.md#parquet-format) című rész. |Nem (csak bináris másolási forgatókönyv esetén) |
| tömörítés | Adja meg az adattömörítés típusát és szintjét. További információ: [támogatott fájlformátumok és tömörítési kodekek](supported-file-formats-and-compression-codecs-legacy.md#compression-support).<br/>A támogatott típusok a *gzip*, a *deflate*, a *BZip2* és a *ZipDeflate*.<br/>A támogatott szintek *optimálisak* és *leggyorsabbak*. |No |

>[!TIP]
>Egy mappa összes fájljának másolásához csak a *folderPath* kell megadni.<br>Egy megadott névvel rendelkező fájl másolásához adja meg a *folderPath* a mappa részét és *fájlnevét* a fájlnévvel.<br>Ha egy mappában lévő fájlok egy részhalmazát szeretné átmásolni, akkor a *folderPath* és a *fájlnevet* a helyettesítő karakteres szűrővel kell megadnia.

>[!NOTE]
>Ha a *fileFilter* tulajdonságot használta a fájl szűrőhöz, azt a rendszer továbbra is támogatja, de azt javasoljuk, hogy mostantól a *fájlnévhez* hozzáadott új szűrő funkciót használja.

**Példa**

```json
{
    "name": "SFTPDataset",
    "type": "Datasets",
    "properties": {
        "type": "FileShare",
        "linkedServiceName":{
            "referenceName": "<SFTP linked service name>",
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
| típus | A másolási tevékenység forrásának *Type* tulajdonságát *FileSystemSource* értékre kell állítani. |Yes |
| rekurzív | Azt jelzi, hogy az adatok rekurzív módon olvashatók-e az almappákból, vagy csak a megadott mappából. Ha a rekurzív értéke TRUE ( *igaz* ), a fogadó pedig egy fájl alapú tároló, az üres mappákat és almappákat a rendszer nem másolja vagy hozza létre a fogadón.<br/>Az engedélyezett értékek értéke *true* (alapértelmezett) és *false (hamis* ) | No |
| maxConcurrentConnections |A tevékenység futtatása során az adattárhoz létesített egyidejű kapcsolatok felső határa. Csak akkor adhat meg értéket, ha korlátozni szeretné az egyidejű kapcsolatokat.| No |

**Példa**

```json
"activities":[
    {
        "name": "CopyFromSFTP",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<SFTP input dataset name>",
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
A Azure Data Factory a másolási tevékenység által forrásként és nyelőként támogatott adattárak listájáért lásd: [támogatott adattárak](copy-activity-overview.md#supported-data-stores-and-formats).
