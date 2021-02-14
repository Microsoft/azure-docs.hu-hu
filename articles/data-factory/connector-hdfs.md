---
title: Adatok másolása a HDFS a Azure Data Factory használatával
description: Megtudhatja, hogyan másolhat adatok egy felhőből vagy helyszíni HDFS-forrásból a fogadó adattárakba egy Azure Data Factory folyamat másolási tevékenységének használatával.
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.date: 12/18/2020
ms.author: jingwang
ms.openlocfilehash: 3ee1b1f48d91ba1245c0173d2e00a20778932d35
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/14/2021
ms.locfileid: "100367084"
---
# <a name="copy-data-from-the-hdfs-server-by-using-azure-data-factory"></a>Adatok másolása a HDFS-kiszolgálóról Azure Data Factory használatával

> [!div class="op_single_selector" title1="Válassza ki a használni kívánt Data Factory-szolgáltatás verzióját:"]
> * [1-es verzió](v1/data-factory-hdfs-connector.md)
> * [Aktuális verzió](connector-hdfs.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Ez a cikk azt ismerteti, hogyan másolhatók adatok a Hadoop elosztott fájlrendszer (HDFS) kiszolgálóról. A Azure Data Factoryről a [bevezető cikkben](introduction.md)olvashat bővebben.

## <a name="supported-capabilities"></a>Támogatott képességek

Az HDFS-összekötő a következő tevékenységek esetében támogatott:

- [Másolási tevékenység](copy-activity-overview.md) [támogatott forrás-és fogadó mátrixtal](copy-activity-overview.md)
- [Keresési tevékenység](control-flow-lookup-activity.md)
- [Tevékenység törlése](delete-activity.md)

Az HDFS-összekötő a következőket támogatja:

- Fájlok másolása *Windows* (Kerberos) vagy *Névtelen* hitelesítés használatával.
- Fájlok másolása a *webhdfs* protokoll vagy a *beépített DistCp-* támogatás használatával.
- A fájlok másolása, illetve a [támogatott fájlformátumokat és tömörítési kodekeket](supported-file-formats-and-compression-codecs.md)tartalmazó fájlok elemzése vagy létrehozása.

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

> [!NOTE]
> Győződjön meg arról, hogy az Integration Runtime hozzáférhet az *összes* [name Node-kiszolgálóhoz]: [name Node port] és [adatcsomópont-kiszolgálók]: [adatcsomópont-port] a Hadoop-fürthöz. Az alapértelmezett [name Node port] a 50070, és az alapértelmezett [adatcsomópont-port] a 50075.

## <a name="get-started"></a>Bevezetés

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

A következő szakaszokban részletesen ismertetjük azokat a tulajdonságokat, amelyek a HDFS-re vonatkozó Data Factory-entitások definiálására szolgálnak.

## <a name="linked-service-properties"></a>Társított szolgáltatás tulajdonságai

A HDFS társított szolgáltatás a következő tulajdonságokat támogatja:

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| típus | A *Type* tulajdonságot *Hdfs* értékre kell beállítani. | Yes |
| url |A HDFS URL-címe |Yes |
| authenticationType | Az engedélyezett értékek: *Névtelen* vagy *Windows*. <br><br> A helyszíni környezet beállításához tekintse meg a Kerberos- [hitelesítés használata a HDFS-összekötőhöz](#use-kerberos-authentication-for-the-hdfs-connector) című szakaszt. |Yes |
| userName (Felhasználónév) |A Windows-hitelesítéshez használt Felhasználónév. Kerberos-hitelesítés esetén a **\<username> @ \<domain> . com** kiterjesztést kell megadni. |Igen (Windows-hitelesítéshez) |
| jelszó |A Windows-hitelesítés jelszava. Megjelöli ezt a mezőt SecureString, hogy biztonságosan tárolja azt az adatgyárban, vagy [hivatkozzon az Azure Key vaultban tárolt titkos kulcsra](store-credentials-in-key-vault.md). |Igen (Windows-hitelesítéshez) |
| Connectvia tulajdonsággal | Az adattárhoz való csatlakozáshoz használt [integrációs](concepts-integration-runtime.md) modul. További információt az [Előfeltételek](#prerequisites) című szakaszban talál. Ha nincs megadva az Integration Runtime, a szolgáltatás az alapértelmezett Azure Integration Runtime használja. |No |

**Példa: Névtelen hitelesítés használata**

```json
{
    "name": "HDFSLinkedService",
    "properties": {
        "type": "Hdfs",
        "typeProperties": {
            "url" : "http://<machine>:50070/webhdfs/v1/",
            "authenticationType": "Anonymous",
            "userName": "hadoop"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Példa: Windows-hitelesítés használata**

```json
{
    "name": "HDFSLinkedService",
    "properties": {
        "type": "Hdfs",
        "typeProperties": {
            "url" : "http://<machine>:50070/webhdfs/v1/",
            "authenticationType": "Windows",
            "userName": "<username>@<domain>.com (for Kerberos auth)",
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

Az adatkészletek definiálásához rendelkezésre álló csoportok és tulajdonságok teljes listájáért lásd: [adatkészletek a Azure Data Factoryban](concepts-datasets-linked-services.md). 

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

A HDFS a következő tulajdonságokat támogatja a `location` Format-alapú adatkészlet beállítások területén:

| Tulajdonság   | Leírás                                                  | Kötelező |
| ---------- | ------------------------------------------------------------ | -------- |
| típus       | Az  `location` adatkészletben található Type tulajdonságot *HdfsLocation* értékre kell állítani. | Yes      |
| folderPath | A mappa elérési útja. Ha a mappa szűréséhez helyettesítő karaktert szeretne használni, hagyja ki ezt a beállítást, és határozza meg az elérési utat a tevékenység forrásának beállításai között. | No       |
| fileName   | A megadott folderPath tartozó fájlnév. Ha helyettesítő karaktert szeretne használni a fájlok szűréséhez, hagyja ki ezt a beállítást, és adja meg a fájl nevét a tevékenység forrásának beállításaiban. | No       |

**Példa**

```json
{
    "name": "DelimitedTextDataset",
    "properties": {
        "type": "DelimitedText",
        "linkedServiceName": {
            "referenceName": "<HDFS linked service name>",
            "type": "LinkedServiceReference"
        },
        "schema": [ < physical schema, optional, auto retrieved during authoring > ],
        "typeProperties": {
            "location": {
                "type": "HdfsLocation",
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

A tevékenységek definiálásához rendelkezésre álló csoportok és tulajdonságok teljes listáját a következő témakörben találja: [folyamatok és tevékenységek a Azure Data Factoryban](concepts-pipelines-activities.md). Ez a szakasz a HDFS-forrás által támogatott tulajdonságok listáját tartalmazza.

### <a name="hdfs-as-source"></a>HDFS forrásként

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

A következő tulajdonságok támogatottak a HDFS a `storeSettings` Format-alapú másolási forrás beállításai alatt:

| Tulajdonság                 | Leírás                                                  | Kötelező                                      |
| ------------------------ | ------------------------------------------------------------ | --------------------------------------------- |
| típus                     | A *Type* tulajdonságot a `storeSettings` **HdfsReadSettings** értékre kell állítani. | Yes                                           |
| ***A másolandó fájlok megkeresése*** |  |  |
| 1. lehetőség: statikus elérési út<br> | Másolja az adatkészletben megadott mappa vagy fájl elérési útját. Ha az összes fájlt egy mappából szeretné másolni, azt is meg kell adnia `wildcardFileName` `*` . |  |
| 2. lehetőség: helyettesítő karakter<br>- wildcardFolderPath | A mappa elérési útja helyettesítő karakterekkel a forrás mappák szűréséhez. <br>Az engedélyezett helyettesítő karakterek a következők: `*` (nulla vagy több karakternek felel meg) és `?` (nulla vagy egyetlen karakternek felel meg). A használatával `^` elkerülheti, hogy a tényleges mappanév helyettesítő karakterrel vagy a Escape-karakterrel rendelkezik-e. <br>További példákat a [mappa-és fájlszűrő-példák](#folder-and-file-filter-examples)című témakörben talál. | No                                            |
| 2. lehetőség: helyettesítő karakter<br>- wildcardFileName | A forrásfájl szűréséhez a megadott folderPath/wildcardFolderPath helyettesítő karaktereket tartalmazó fájlnév. <br>Az engedélyezett helyettesítő karakterek a következők: `*` (nulla vagy több karakternek felel meg) és `?` (a nulla vagy egy karakter egyezése) `^`  További példákat a [mappa-és fájlszűrő-példák](#folder-and-file-filter-examples)című témakörben talál. | Yes |
| 3. lehetőség: a fájlok listája<br>- fileListPath | Egy megadott fájl másolását jelzi. Mutasson egy szövegfájlra, amely tartalmazza a másolni kívánt fájlok listáját (egy-egy fájl soronként, az adatkészletben konfigurált elérési út relatív elérési útjával).<br/>Ha ezt a beállítást használja, ne adja meg a fájl nevét az adatkészletben. További példákat itt talál: [fájllista példák](#file-list-examples). |No |
| ***További beállítások*** |  | |
| rekurzív | Azt jelzi, hogy az adatok rekurzív módon olvashatók-e az almappákból, vagy csak a megadott mappából. Ha `recursive` a értéke *true (igaz* ), a fogadó pedig egy fájl alapú tároló, akkor a fogadó nem másol vagy hoz létre üres mappát vagy almappát. <br>Az engedélyezett értékek: *true* (alapértelmezett) és *false (hamis*).<br>Ez a tulajdonság nem érvényes a konfiguráláskor `fileListPath` . |No |
| deleteFilesAfterCompletion | Azt jelzi, hogy a rendszer törli-e a bináris fájlokat a forrás-áruházból, miután sikeresen áthelyezte a célhelyre. A fájl törlése fájl alapján történik, így ha a másolási tevékenység meghiúsul, néhány fájl már át lett másolva a célhelyre, és törlődik a forrásból, míg mások továbbra is a forrás-áruházban maradnak. <br/>Ez a tulajdonság csak bináris fájlok másolási forgatókönyv esetén érvényes. Az alapértelmezett érték: false. |No |
| modifiedDatetimeStart    | A fájlok szűrése a *legutóbb módosított* attribútum alapján történik. <br>A fájlok akkor vannak kiválasztva, ha az utolsó módosítás időpontja a következő tartományon belül van: `modifiedDatetimeStart` `modifiedDatetimeEnd` . Az idő az UTC-időzónára vonatkozik *2018-12-01T05:00:00Z* formátumban. <br> A tulajdonságok lehetnek NULL értékűek, ami azt jelenti, hogy a rendszer nem alkalmazza a file Attribute szűrőt az adatkészletre.  Ha a `modifiedDatetimeStart` dátum datetime értékkel rendelkezik `modifiedDatetimeEnd` , de null értékű, az azt jelenti, hogy azok a fájlok vannak kiválasztva, amelyek utolsó módosítási attribútuma nagyobb vagy egyenlő, mint a DateTime érték.  Ha a `modifiedDatetimeEnd` dátum datetime értékkel rendelkezik `modifiedDatetimeStart` , de null értékű, az azt jelenti, hogy azok a fájlok vannak kiválasztva, amelyek utolsó módosítási attribútuma kisebb a DateTime értéknél.<br/>Ez a tulajdonság nem érvényes a konfiguráláskor `fileListPath` . | No                                            |
| modifiedDatetimeEnd      | Lásd fentebb.  
| enablePartitionDiscovery | A particionált fájlok esetében adja meg, hogy szeretné-e elemezni a partíciókat a fájl elérési útján, majd adja hozzá őket további forrásként szolgáló oszlopként.<br/>Az engedélyezett értékek: **false** (alapértelmezett) és **true (igaz**). | No                                            |
| partitionRootPath | Ha engedélyezve van a partíciók felderítése, akkor a particionált mappák adatoszlopként való olvasásához a gyökér elérési útját kell megadni.<br/><br/>Ha nincs megadva, a rendszer alapértelmezés szerint<br/>– Ha a fájl elérési útját használja az adatkészletben vagy a forrásban található fájlok listáján, a partíció gyökerének elérési útja az adatkészletben konfigurált útvonal.<br/>– Ha helyettesítő mappa szűrőt használ, a partíció gyökerének elérési útja az első helyettesítő karakter előtti Alútvonal.<br/><br/>Tegyük fel például, hogy az adatkészletben az elérési utat "root/Folder/Year = 2020/hónap = 08/Day = 27" értékre konfigurálja:<br/>– Ha a partíció gyökerének elérési útját "gyökér/mappa/év = 2020" értékre állítja, a másolási tevékenység két további oszlopot fog előállítani, `month` és a `day` "08" és "27" értéket is kijelöli a fájlokban lévő oszlopokon kívül.<br/>– Ha nincs megadva a partíció gyökerének elérési útja, nem jön létre további oszlop. | No                                            |
| maxConcurrentConnections | A tárolóhoz egyidejűleg csatlakozni képes kapcsolatok száma. Csak akkor adhat meg értéket, ha korlátozni szeretné az egyidejű kapcsolódást az adattárhoz. | No                                            |
| ***DistCp-beállítások*** |  | |
| distcpSettings | A HDFS DistCp használatakor használandó tulajdonságérték. | No |
| resourceManagerEndpoint | A fonal (még egy erőforrás-egyeztető) végpontja | Igen, ha DistCp használ |
| tempScriptPath | Mappa elérési útja, amely a temp DistCp parancs parancsfájljának tárolására szolgál. A parancsfájlt a Data Factory hozza létre, és a másolási feladatok befejezése után el lesz távolítva. | Igen, ha DistCp használ |
| distcpOptions | A DistCp parancshoz megadott további beállítások. | No |

**Példa**

```json
"activities":[
    {
        "name": "CopyFromHDFS",
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
                    "type": "HdfsReadSettings",
                    "recursive": true,
                    "distcpSettings": {
                        "resourceManagerEndpoint": "resourcemanagerendpoint:8088",
                        "tempScriptPath": "/usr/hadoop/tempscript",
                        "distcpOptions": "-m 100"
                    }
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

Ez a szakasz az eredményül kapott viselkedést ismerteti, ha helyettesítő karaktert használ a mappa elérési útjának és fájlnevének használatával.

| folderPath | fileName             | rekurzív | A forrás mappa szerkezete és a szűrő eredménye (a **félkövérrel szedett** fájlok beolvasása) |
| :--------- | :------------------- | :-------- | :----------------------------------------------------------- |
| `Folder*`  | (üres, alapértelmezett használata) | hamis     | Mappa<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File2.jsbekapcsolva**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.jsbekapcsolva<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5.csv<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `Folder*`  | (üres, alapértelmezett használata) | true      | Mappa<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File2.jsbekapcsolva**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File4.jsbekapcsolva**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `Folder*`  | `*.csv`              | hamis     | Mappa<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2.jsbekapcsolva<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.jsbekapcsolva<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5.csv<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `Folder*`  | `*.csv`              | true      | Mappa<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2.jsbekapcsolva<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.jsbekapcsolva<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |

### <a name="file-list-examples"></a>Példák a fájllista

Ez a szakasz a másolási tevékenység forrásában lévő fájllista elérési útját eredményező viselkedést ismerteti. Feltételezi, hogy rendelkezik a következő forrás-mappa struktúrával, és a félkövéren szedett fájlokat szeretné másolni:

| Példa a forrás struktúrájára                                      | Tartalom FileListToCopy.txt                             | Azure Data Factory konfiguráció                                            |
| ------------------------------------------------------------ | --------------------------------------------------------- | ------------------------------------------------------------ |
| legfelső szintű<br/>&nbsp;&nbsp;&nbsp;&nbsp;Mappa<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File2.jsbekapcsolva<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.jsbekapcsolva<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Metaadatok<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;FileListToCopy.txt | File1.csv<br>Subfolder1/File3.csv<br>Subfolder1/File5.csv | **Az adatkészletben:**<br>-Mappa elérési útja: `root/FolderA`<br><br>**A másolási tevékenység forrása:**<br>-Fájllista elérési útja: `root/Metadata/FileListToCopy.txt` <br><br>A fájllista elérési útja ugyanazon az adattárban található szövegfájlra mutat, amely tartalmazza a másolni kívánt fájlok listáját (egy sor, soronként egy fájl, az adatkészletben konfigurált elérési út relatív elérési útját). |

## <a name="use-distcp-to-copy-data-from-hdfs"></a>Adatok másolása a HDFS-ből a DistCp használatával

A [DistCp](https://hadoop.apache.org/docs/current3/hadoop-distcp/DistCp.html) egy Hadoop natív parancssori eszköz, amellyel elosztott másolatot készíthet egy Hadoop-fürtön. Amikor futtat egy parancsot a DistCp-ben, először felsorolja az összes másolandó fájlt, majd több leképezési feladatot hoz létre a Hadoop-fürtben. Az egyes leképezési feladatok bináris másolatot készítenek a forrásról a fogadóba.

A másolási tevékenység támogatja a DistCp használatával a fájlok másolását az Azure Blob Storage-ba (beleértve a [szakaszos másolást](copy-activity-performance.md)) vagy egy Azure-beli adatlake-tárolót. Ebben az esetben a DistCp a saját üzemeltetésű integrációs modul futtatása helyett kihasználhatja a fürt erejét. A DistCp használata jobb másolási sebességet biztosít, különösen akkor, ha a fürt nagy teljesítményű. A másolási tevékenység az adatgyárban megadott konfiguráció alapján automatikusan létrehoz egy DistCp-parancsot, elküldi azt a Hadoop-fürtnek, és figyeli a másolási állapotot.

### <a name="prerequisites"></a>Előfeltételek

Ahhoz, hogy a DistCp a fájlok másolását a HDFS-ből az Azure Blob Storage-ba (beleértve a lépcsőzetes másolást is) vagy az Azure-beli adattárat, győződjön meg arról, hogy a Hadoop-fürt megfelel a következő követelményeknek:

* A MapReduce és a FONALas szolgáltatások engedélyezve vannak.  
* A fonal verziója 2,5 vagy újabb.  
* A HDFS-kiszolgáló integrálva van a tároló adattárával: **Azure Blob Storage** vagy **Azure Data Lake Store (ADLS Gen1)**: 

    - Az Azure Blob-fájlrendszer natív módon támogatott a Hadoop 2,7 óta. A Hadoop környezeti konfigurációban csak a JAR elérési utat kell megadnia.
    - Azure Data Lake Store fájlrendszer a Hadoop 3.0.0-alfa1-től kezdve van csomagolva. Ha a Hadoop-fürt verziója korábbi ennél a verziónál, manuálisan kell importálnia Azure Data Lake Store kapcsolódó JAR-csomagokat (Azure-datalake-Store. jar) a fürtbe innen [, és](https://hadoop.apache.org/releases.html)meg kell adnia a jar-fájl elérési útját a Hadoop környezeti konfigurációjában.

* Készítse elő a Temp mappát a HDFS-ben. Ez a temp mappa egy DistCp-rendszerhéj-parancsfájl tárolására szolgál, így KB szintű helyet foglal le.
* Győződjön meg arról, hogy a HDFS társított szolgáltatásban megadott felhasználói fiók rendelkezik a következő engedélyekkel:
   * Alkalmazás beküldése a FONALba.
   * Hozzon létre egy almappát, és Olvasson/írhat fájlokat a temp mappában.

### <a name="configurations"></a>Konfigurációk

A DistCp-hez kapcsolódó konfigurációk és példák esetében válassza a [HDFS forrásként](#hdfs-as-source) szakaszt.

## <a name="use-kerberos-authentication-for-the-hdfs-connector"></a>Kerberos-hitelesítés használata az HDFS-összekötőhöz

Két lehetőség áll rendelkezésre a helyszíni környezet beállítására Kerberos-hitelesítés használatára a HDFS-összekötőhöz. Kiválaszthatja, hogy melyik illik jobban a helyzethez.
* 1. lehetőség: [Csatlakozás egy saját üzemeltetésű integrációs modulhoz a Kerberos-tartományban](#kerberos-join-realm)
* 2. lehetőség: [kölcsönös megbízhatóság engedélyezése a Windows-tartomány és a Kerberos tartomány között](#kerberos-mutual-trust)

Mindkét beállításnál ügyeljen arra, hogy bekapcsolja a webhdfs a Hadoop-fürthöz:

1. Hozza létre a HTTP-rendszerbiztonsági tag és a keytab kiterjesztésű a webhdfs.

    > [!IMPORTANT]
    > A HTTP Kerberos-rendszerbiztonsági tag a Kerberos HTTP-SPNEGO specifikációjának megfelelően "**http/**" értékkel kell kezdődnie. További információt [itt talál](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/WebHDFS.html#HDFS_Configuration_Options).

    ```bash
    Kadmin> addprinc -randkey HTTP/<namenode hostname>@<REALM.COM>
    Kadmin> ktadd -k /etc/security/keytab/spnego.service.keytab HTTP/<namenode hostname>@<REALM.COM>
    ```

2. HDFS-konfigurációs beállítások: adja hozzá a következő három tulajdonságot a alkalmazásban `hdfs-site.xml` .
    ```xml
    <property>
        <name>dfs.webhdfs.enabled</name>
        <value>true</value>
    </property>
    <property>
        <name>dfs.web.authentication.kerberos.principal</name>
        <value>HTTP/_HOST@<REALM.COM></value>
    </property>
    <property>
        <name>dfs.web.authentication.kerberos.keytab</name>
        <value>/etc/security/keytab/spnego.service.keytab</value>
    </property>
    ```

### <a name="option-1-join-a-self-hosted-integration-runtime-machine-in-the-kerberos-realm"></a><a name="kerberos-join-realm"></a>1. lehetőség: csatlakozás egy saját üzemeltetésű integrációs modulhoz a Kerberos-tartományban

#### <a name="requirements"></a>Követelmények

* A saját üzemeltetésű integrációs modul számítógépének csatlakoznia kell a Kerberos-tartományhoz, és nem lehet Windows-tartományhoz csatlakoznia.

#### <a name="how-to-configure"></a>Konfigurálás

**A KDC-kiszolgálón:**

Hozzon létre egy rendszerbiztonsági tag számára a használni kívánt Azure Data Factory, és határozza meg a jelszót.

> [!IMPORTANT]
> A Felhasználónév nem tartalmazhatja a hostname nevet.

```bash
Kadmin> addprinc <username>@<REALM.COM>
```

**A saját üzemeltetésű Integration Runtime gépen:**

1.  Futtassa a Ksetup segédprogramot a Kerberos kulcsszolgáltató (KDC) kiszolgáló és a tartomány konfigurálásához.

    A gépet egy munkacsoport tagjaként kell konfigurálni, mert egy Kerberos-tartomány eltér a Windows-tartománytól. Ezt a konfigurációt úgy érheti el, ha beállítja a Kerberos-tartományt, és hozzáadja a KDC-kiszolgálót a következő parancsok futtatásával. Cserélje le a *REALM.com* a saját tartománynevére.

    ```cmd
    C:> Ksetup /setdomain REALM.COM
    C:> Ksetup /addkdc REALM.COM <your_kdc_server_address>
    ```

    A parancsok futtatása után indítsa újra a gépet.

2.  Ellenőrizze a konfigurációt a `Ksetup` paranccsal. A kimenetnek az alábbihoz hasonlónak kell lennie:

    ```cmd
    C:> Ksetup
    default realm = REALM.COM (external)
    REALM.com:
        kdc = <your_kdc_server_address>
    ```

**Az adatgyárban:**

* Konfigurálja a HDFS-összekötőt a Windows-hitelesítéssel együtt a Kerberos egyszerű felhasználónevével és jelszavával a HDFS-adatforráshoz való csatlakozáshoz. A konfiguráció részleteiért olvassa el a [HDFS társított szolgáltatás tulajdonságai](#linked-service-properties) szakaszt.

### <a name="option-2-enable-mutual-trust-between-the-windows-domain-and-the-kerberos-realm"></a><a name="kerberos-mutual-trust"></a>2. lehetőség: kölcsönös megbízhatóság engedélyezése a Windows-tartomány és a Kerberos tartomány között

#### <a name="requirements"></a>Követelmények

*   A saját üzemeltetésű Integration Runtime-gépnek csatlakoznia kell egy Windows-tartományhoz.
*   Engedéllyel kell rendelkeznie a tartományvezérlő beállításainak frissítéséhez.

#### <a name="how-to-configure"></a>Konfigurálás

> [!NOTE]
> Cserélje le a REALM.COM és a AD.COM-t a következő oktatóanyagban a saját tartománynevével és a tartományvezérlővel.

**A KDC-kiszolgálón:**

1. Szerkessze a KDC konfigurációját a *krb5. conf* fájlban, hogy a KDC megbízhatónak minősítse a Windows-tartományt a következő konfigurációs sablonra való hivatkozással. Alapértelmezés szerint a konfiguráció a következő helyen található: */etc/krb5.conf állományt*.

   ```config
   [logging]
    default = FILE:/var/log/krb5libs.log
    kdc = FILE:/var/log/krb5kdc.log
    admin_server = FILE:/var/log/kadmind.log
            
   [libdefaults]
    default_realm = REALM.COM
    dns_lookup_realm = false
    dns_lookup_kdc = false
    ticket_lifetime = 24h
    renew_lifetime = 7d
    forwardable = true
            
   [realms]
    REALM.COM = {
     kdc = node.REALM.COM
     admin_server = node.REALM.COM
    }
   AD.COM = {
    kdc = windc.ad.com
    admin_server = windc.ad.com
   }
            
   [domain_realm]
    .REALM.COM = REALM.COM
    REALM.COM = REALM.COM
    .ad.com = AD.COM
    ad.com = AD.COM
            
   [capaths]
    AD.COM = {
     REALM.COM = .
    }
    ```

   A fájl konfigurálása után indítsa újra a KDC szolgáltatást.

2. Készítse elő a *krbtgt/REALM. COM \@ ad.com* nevű rendszerbiztonsági tag a KDC-kiszolgálón a következő paranccsal:

    ```cmd
    Kadmin> addprinc krbtgt/REALM.COM@AD.COM
    ```

3. A *Hadoop.Security.auth_to_local* HDFS szolgáltatás konfigurációs fájljában adja hozzá a t `RULE:[1:$1@$0](.*\@AD.COM)s/\@.*//` .

**A tartományvezérlőn:**

1.  `Ksetup`Tartományi bejegyzés hozzáadásához futtassa a következő parancsokat:

    ```cmd
    C:> Ksetup /addkdc REALM.COM <your_kdc_server_address>
    C:> ksetup /addhosttorealmmap HDFS-service-FQDN REALM.COM
    ```

2.  Hozzon létre megbízhatóságot a Windows-tartományból a Kerberos tartományba. a [password] a fő *krbtgt/REALM. COM \@ ad.com* tartozó jelszó.

    ```cmd
    C:> netdom trust REALM.COM /Domain: AD.COM /add /realm /password:[password]
    ```

3.  Válassza ki a Kerberosban használt titkosítási algoritmust.

    a. Válassza a **Kiszolgálókezelő**  >  **csoportházirend felügyeleti**  >  **tartomány**  >  **csoportházirend objektumok**  >  **alapértelmezett vagy aktív tartományi házirend** elemet, majd válassza a **Szerkesztés** lehetőséget.

    b. A **csoportházirend-felügyeleti szerkesztő** ablaktáblán válassza a **számítógép-konfigurációs**  >  **házirendek**  >  **Windows-beállítások**  >  **biztonsági beállítások**  >  **helyi házirendek**  >  **biztonsági beállítások**, majd a **hálózati biztonság beállítása: a Kerberos számára engedélyezett titkosítási típusok konfigurálása** lehetőséget.

    c. Válassza ki a KDC-kiszolgálóhoz való csatlakozáskor használni kívánt titkosítási algoritmust. Az összes beállítást kiválaszthatja.

    ![Képernyőkép a "hálózati biztonság: Kerberos-titkosítás engedélyezése" panelen](media/connector-hdfs/config-encryption-types-for-kerberos.png)

    d. A `Ksetup` parancs használatával adja meg a megadott tartományon használni kívánt titkosítási algoritmust.

    ```cmd
    C:> ksetup /SetEncTypeAttr REALM.COM DES-CBC-CRC DES-CBC-MD5 RC4-HMAC-MD5 AES128-CTS-HMAC-SHA1-96 AES256-CTS-HMAC-SHA1-96
    ```

4.  Hozzon létre egy leképezést a tartományi fiók és a Kerberos-tag között, hogy a Kerberos-rendszerbiztonsági tag legyen a Windows-tartományban.

    a. Válassza a **felügyeleti eszközök**  >  **Active Directory felhasználók és számítógépek** lehetőséget.

    b. A speciális szolgáltatások konfigurálásához válassza a  >  **speciális szolgáltatások** megtekintése lehetőséget.

    c. A **speciális szolgáltatások** ablaktáblán kattintson a jobb gombbal arra a fiókra, amelyhez leképezéseket szeretne létrehozni, majd a **név-hozzárendelések** panelen válassza a **Kerberos-nevek** lapot.

    d. Adjon hozzá egy rendszerbiztonsági tag a tartományhoz.

       ![A "biztonsági azonosítók leképezése" panel](media/connector-hdfs/map-security-identity.png)

**A saját üzemeltetésű Integration Runtime gépen:**

* A következő parancsok futtatásával `Ksetup` adhat hozzá egy tartományi bejegyzést.

   ```cmd
   C:> Ksetup /addkdc REALM.COM <your_kdc_server_address>
   C:> ksetup /addhosttorealmmap HDFS-service-FQDN REALM.COM
   ```

**Az adatgyárban:**

* Konfigurálja a HDFS-összekötőt a Windows-hitelesítéssel együtt a tartományi fiókkal vagy a Kerberos-rendszerbiztonsági tag használatával a HDFS-adatforráshoz való csatlakozáshoz. A konfiguráció részleteiért tekintse meg a [HDFS társított szolgáltatás tulajdonságai](#linked-service-properties) szakaszt.

## <a name="lookup-activity-properties"></a>Keresési tevékenység tulajdonságai

További információ a keresési tevékenység tulajdonságairól: [keresési tevékenység Azure Data Factoryban](control-flow-lookup-activity.md).

## <a name="delete-activity-properties"></a>Tevékenység tulajdonságainak törlése

A törlési tevékenység tulajdonságaival kapcsolatos információkért lásd: [tevékenység törlése a Azure Data Factoryban](delete-activity.md).

## <a name="legacy-models"></a>Örökölt modellek

>[!NOTE]
>A következő modellek továbbra is támogatottak a visszamenőleges kompatibilitás érdekében. Javasoljuk, hogy a korábban tárgyalt új modellt használja, mert a Azure Data Factory szerzői felhasználói felülete átváltott az új modell generálására.

### <a name="legacy-dataset-model"></a>Örökölt adatkészlet-modell

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| típus | Az adatkészlet *Type* tulajdonságát *fájlmegosztás* értékre kell beállítani. |Yes |
| folderPath | A mappa elérési útja. Helyettesítő karakteres szűrő használata támogatott. Az engedélyezett helyettesítő karakterek `*` (nulla vagy több karakternek felelnek meg) és `?` (a nulla vagy egyetlen karakternek felelnek meg); `^` Ha a tényleges fájlnév helyettesítő karaktere vagy a escape-karakter található, akkor Escape-karaktert kell használnia. <br/><br/>Példák: gyökérmappa/almappa/, további példák a [mappák és a fájlok szűrése példákban](#folder-and-file-filter-examples). |Yes |
| fileName |  A megadott "folderPath" fájlhoz tartozó fájlok neve vagy helyettesítő karakteres szűrője. Ha nem ad meg értéket ehhez a tulajdonsághoz, az adatkészlet a mappában található összes fájlra mutat. <br/><br/>A Filter (szűrő) esetében megengedett helyettesítő karakterek `*` (nulla vagy több karakternek felelnek meg) és `?` (nulla vagy egyetlen karakter).<br/>– 1. példa: `"fileName": "*.csv"`<br/>– 2. példa: `"fileName": "???20180427.txt"`<br/>A használatával `^` elkerülheti, hogy a tényleges mappanév helyettesítő karakterrel vagy a Escape-karakterrel rendelkezik-e. |No |
| modifiedDatetimeStart | A fájlok szűrése a *legutóbb módosított* attribútum alapján történik. A fájlok akkor vannak kiválasztva, ha az utolsó módosítás időpontja a következő tartományon belül van: `modifiedDatetimeStart` `modifiedDatetimeEnd` . Az időpontot az UTC-időzónára alkalmazza a *2018-12-01T05:00:00Z* formátumban. <br/><br/> Vegye figyelembe, hogy az adatáthelyezés általános teljesítményét a rendszer a beállítás engedélyezésével fogja érinteni, ha nagy számú fájlra kívánja alkalmazni a fájlmegosztást. <br/><br/> A tulajdonságok lehetnek NULL értékűek, ami azt jelenti, hogy a rendszer nem alkalmazza a file Attribute szűrőt az adatkészletre.  Ha a `modifiedDatetimeStart` dátum datetime értékkel rendelkezik `modifiedDatetimeEnd` , de null értékű, az azt jelenti, hogy azok a fájlok vannak kiválasztva, amelyek utolsó módosítási attribútuma nagyobb vagy egyenlő, mint a DateTime érték.  Ha a `modifiedDatetimeEnd` dátum datetime értékkel rendelkezik `modifiedDatetimeStart` , de null értékű, az azt jelenti, hogy azok a fájlok vannak kiválasztva, amelyek utolsó módosítási attribútuma kisebb a DateTime értéknél.| No |
| modifiedDatetimeEnd | A fájlok szűrése a *legutóbb módosított* attribútum alapján történik. A fájlok akkor vannak kiválasztva, ha az utolsó módosítás időpontja a következő tartományon belül van: `modifiedDatetimeStart` `modifiedDatetimeEnd` . Az időpontot az UTC-időzónára alkalmazza a *2018-12-01T05:00:00Z* formátumban. <br/><br/> Vegye figyelembe, hogy az adatáthelyezés általános teljesítményét a rendszer a beállítás engedélyezésével fogja érinteni, ha nagy számú fájlra kívánja alkalmazni a fájlmegosztást. <br/><br/> A tulajdonságok lehetnek NULL értékűek, ami azt jelenti, hogy a rendszer nem alkalmazza a file Attribute szűrőt az adatkészletre.  Ha a `modifiedDatetimeStart` dátum datetime értékkel rendelkezik `modifiedDatetimeEnd` , de null értékű, az azt jelenti, hogy azok a fájlok vannak kiválasztva, amelyek utolsó módosítási attribútuma nagyobb vagy egyenlő, mint a DateTime érték.  Ha a `modifiedDatetimeEnd` dátum datetime értékkel rendelkezik `modifiedDatetimeStart` , de null értékű, az azt jelenti, hogy azok a fájlok vannak kiválasztva, amelyek utolsó módosítási attribútuma kisebb a DateTime értéknél.| No |
| formátumban | Ha fájlokat szeretne másolni a fájl alapú tárolók között (bináris másolás), ugorja át a formátum szakaszt a bemeneti és a kimeneti adatkészlet-definíciókban is.<br/><br/>Ha a fájlokat egy adott formátummal szeretné elemezni, a következő fájlformátum-típusok támogatottak: *Szövegformátum*, *JsonFormat*, *AvroFormat*, *OrcFormat*, *ParquetFormat*. A *Type (típus* ) tulajdonságot állítsa a Format értékre a következő értékek egyikére. További információ: [szöveg formátuma](supported-file-formats-and-compression-codecs-legacy.md#text-format), JSON- [Formátum](supported-file-formats-and-compression-codecs-legacy.md#json-format), [Avro formátum](supported-file-formats-and-compression-codecs-legacy.md#avro-format), [ork-formátum](supported-file-formats-and-compression-codecs-legacy.md#orc-format)és [parketta formátuma](supported-file-formats-and-compression-codecs-legacy.md#parquet-format) . |Nem (csak bináris másolási forgatókönyv esetén) |
| tömörítés | Adja meg az adattömörítés típusát és szintjét. További információ: [támogatott fájlformátumok és tömörítési kodekek](supported-file-formats-and-compression-codecs-legacy.md#compression-support).<br/>A támogatott típusok a következők: *gzip*, *deflate*, *bzip2* és *ZipDeflate*.<br/>A támogatott szintek a következők: *optimális* és *leggyorsabb*. |No |

>[!TIP]
>Egy mappa összes fájljának másolásához csak a **folderPath** kell megadni.<br>Egy megadott névvel rendelkező fájl másolásához adja meg a **folderPath** és a **fájlnév nevű fájlnevet** .<br>Ha egy mappában lévő fájlok egy részhalmazát szeretné másolni, akkor a **folderPath** és a **filename** paramétert a helyettesítő karakteres szűrővel.

**Példa**

```json
{
    "name": "HDFSDataset",
    "properties": {
        "type": "FileShare",
        "linkedServiceName":{
            "referenceName": "<HDFS linked service name>",
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
| típus | A másolási tevékenység forrásának *Type* tulajdonságát *HdfsSource* értékre kell állítani. |Yes |
| rekurzív | Azt jelzi, hogy az adatok rekurzív módon olvashatók-e az almappákból, vagy csak a megadott mappából. Ha a rekurzív értéke TRUE ( *igaz* ), a fogadó pedig egy fájl alapú tároló, akkor a fogadó nem másolja vagy hozza létre az üres mappát vagy almappát.<br/>Az engedélyezett értékek: *true* (alapértelmezett) és *false (hamis*). | No |
| distcpSettings | A HDFS DistCp használatakor használt tulajdonság. | No |
| resourceManagerEndpoint | A FONÁL Resource Manager-végpont | Igen, ha DistCp használ |
| tempScriptPath | Mappa elérési útja, amely a temp DistCp parancs parancsfájljának tárolására szolgál. A parancsfájlt a Data Factory hozza létre, és a másolási feladatok befejezése után el lesz távolítva. | Igen, ha DistCp használ |
| distcpOptions | A DistCp parancs további beállításokkal is rendelkezik. | No |
| maxConcurrentConnections | A tárolóhoz egyidejűleg csatlakozni képes kapcsolatok száma. Csak akkor adhat meg értéket, ha korlátozni szeretné az egyidejű kapcsolódást az adattárhoz. | No |

**Példa: HDFS forrás a másolási tevékenységben a DistCp használatával**

```json
"source": {
    "type": "HdfsSource",
    "distcpSettings": {
        "resourceManagerEndpoint": "resourcemanagerendpoint:8088",
        "tempScriptPath": "/usr/hadoop/tempscript",
        "distcpOptions": "-m 100"
    }
}
```

## <a name="next-steps"></a>Következő lépések
A Azure Data Factory a másolási tevékenység által forrásként és nyelőként támogatott adattárak listájáért lásd: [támogatott adattárak](copy-activity-overview.md#supported-data-stores-and-formats).
