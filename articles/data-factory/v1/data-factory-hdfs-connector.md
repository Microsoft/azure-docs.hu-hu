---
title: Adatok áthelyezése a helyszíni HDFS
description: Ismerje meg, hogyan helyezheti át a helyszíni HDFS adatait Azure Data Factory használatával.
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 52e176e0fed85b649d482614667d695db539e5d1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "100383075"
---
# <a name="move-data-from-on-premises-hdfs-using-azure-data-factory"></a>Adatok áthelyezése helyszíni HDFS Azure Data Factory használatával
> [!div class="op_single_selector" title1="Válassza ki az Ön által használt Data Factory-szolgáltatás verzióját:"]
> * [1-es verzió](data-factory-hdfs-connector.md)
> * [2-es verzió (aktuális verzió)](../connector-hdfs.md)

> [!NOTE]
> Ez a cikk a Data Factory 1-es verziójára vonatkozik. Ha a Data Factory szolgáltatás aktuális verzióját használja, tekintse meg a [HDFS-összekötőt a v2-ben](../connector-hdfs.md).

Ez a cikk azt ismerteti, hogyan használható a másolási tevékenység a Azure Data Factoryban az adatok helyszíni HDFS való áthelyezéséhez. Az [adattovábbítási tevékenységekről](data-factory-data-movement-activities.md) szóló cikkre épül, amely általános áttekintést nyújt az adatáthelyezésről a másolási tevékenységgel.

Az adatok a HDFS bármely támogatott fogadó adattárba másolhatók. A másolási tevékenység által mosogatóként támogatott adattárak listáját a [támogatott adattárak](data-factory-data-movement-activities.md#supported-data-stores-and-formats) táblázatban tekintheti meg. A adatfeldolgozó jelenleg csak a helyszíni HDFS származó adatok áthelyezését támogatja más adattárakba, de az adatok más adattárakból a helyszíni HDFS való áthelyezésére nem.

> [!NOTE]
> A másolási tevékenység nem törli a forrásfájlt, miután sikeresen átmásolta a célhelyre. Ha sikeres másolás után törölni kell a forrásfájlt, hozzon létre egy egyéni tevékenységet a fájl törléséhez, és használja a folyamatot a folyamatban. 

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="enabling-connectivity"></a>Kapcsolat engedélyezése
A Data Factory szolgáltatás támogatja a helyszíni HDFS való csatlakozást a adatkezelés átjáró használatával. Az átjáró beállításával adatkezelés kapcsolatos további információkért lásd: az [adatáthelyezés a helyszíni helyszínek és a felhő között](data-factory-move-data-between-onprem-and-cloud.md) . Az átjáró segítségével akkor is csatlakozhat a HDFS, ha az egy Azure IaaS virtuális gépen üzemel.

> [!NOTE]
> Győződjön meg arról, hogy az adatkezelés átjáró hozzáférhet az **összes** [név csomópont-kiszolgálóhoz]: [name Node port] és [adatcsomópont-kiszolgálók]: [adatcsomópont-port] a Hadoop-fürthöz. Az alapértelmezett [name Node port] a 50070, és az alapértelmezett [adatcsomópont-port] a 50075.

Habár az átjárót ugyanarra a helyszíni gépre vagy az Azure-beli virtuális gépre is telepítheti HDFS, javasoljuk, hogy az átjárót külön gépre vagy Azure IaaS virtuális gépre telepítse. Az átjáró egy különálló gépen csökkenti az erőforrás-tartalmat, és javítja a teljesítményt. Ha az átjárót külön gépre telepíti, a gépnek el kell tudnia érni a gépet a HDFS.

## <a name="getting-started"></a>Első lépések
Létrehozhat egy másolási tevékenységgel rendelkező folyamatot, amely különböző eszközök/API-k használatával helyez át egy HDFS-forrásból származó adatokkal.

A folyamat létrehozásának legegyszerűbb módja a **Másolás varázsló** használata. Tekintse meg az [oktatóanyag: folyamat létrehozása a másolás varázslóval](data-factory-copy-data-wizard-tutorial.md) című témakört, amely gyors áttekintést nyújt a folyamat létrehozásáról az adatmásolási varázsló használatával.

A következő eszközöket is használhatja a folyamat létrehozásához: **Azure Portal**, **Visual Studio**, **Azure PowerShell**, **Azure Resource Manager sablon**, **.NET API** és **REST API**. A másolási tevékenységgel rendelkező folyamat létrehozásával kapcsolatos részletes utasításokat a [másolási tevékenységről szóló oktatóanyagban](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) talál.

Függetlenül attól, hogy az eszközöket vagy API-kat használja, a következő lépések végrehajtásával hozhat létre egy folyamatot, amely egy forrás adattárból egy fogadó adattárba helyezi át az adatait:

1. **Társított szolgáltatások** létrehozása a bemeneti és kimeneti adattáraknak az adat-előállítóhoz való összekapcsolásához.
2. Hozzon létre **adatkészleteket** a másolási művelet bemeneti és kimeneti adatok ábrázolásához.
3. Hozzon **létre egy másolási tevékenységgel rendelkező folyamatot** , amely egy adatkészletet bemenetként és egy adatkészlet kimenetként való elvégzéséhez szükséges.

A varázsló használatakor a rendszer automatikusan létrehozza a Data Factory entitások (társított szolgáltatások, adatkészletek és a folyamat) JSON-definícióit. Ha eszközöket/API-kat használ (kivéve a .NET API-t), akkor ezeket a Data Factory entitásokat JSON-formátumban kell megadnia.  A HDFS-adattárakból származó adatok másolásához használt Data Factory JSON-definíciókkal rendelkező minta esetében lásd a jelen cikk [JSON-példa: adatok másolása a helyszíni HDFS az Azure blobba](#json-example-copy-data-from-on-premises-hdfs-to-azure-blob) című szakaszát.

A következő szakaszokban részletesen ismertetjük a HDFS specifikus entitások Data Factory definiálásához használt JSON-tulajdonságokat:

## <a name="linked-service-properties"></a>Társított szolgáltatás tulajdonságai
A társított szolgáltatás egy adattárhoz csatol egy adattárolót egy adatgyárhoz. Hozzon létre egy **Hdfs** típusú társított szolgáltatást egy helyszíni Hdfs az adatgyárhoz való kapcsolásához. A következő táblázat a HDFS társított szolgáltatáshoz tartozó JSON-elemek leírását tartalmazza.

| Tulajdonság | Leírás | Kötelező |
| --- | --- | --- |
| típus |A Type tulajdonságot a következőre kell beállítani: **Hdfs** |Yes |
| url |A HDFS URL-címe |Yes |
| authenticationType |Névtelen vagy Windows. <br><br> Ha **Kerberos-hitelesítést** kíván használni a HDFS-összekötőhöz, tekintse meg [ezt a szakaszt](#use-kerberos-authentication-for-hdfs-connector) , és ennek megfelelően állítsa be a helyszíni környezetet. |Yes |
| userName (Felhasználónév) |Felhasználónév a Windows-hitelesítéshez. Kerberos-hitelesítés esetén válassza a következőt: `<username>@<domain>.com` . |Igen (Windows-hitelesítéshez) |
| jelszó |Jelszó a Windows-hitelesítéshez. |Igen (Windows-hitelesítéshez) |
| Átjáró neve |Annak az átjárónak a neve, amelyet a Data Factory szolgáltatásnak használnia kell a HDFS való kapcsolódáshoz. |Yes |
| encryptedCredential |[Új –](/powershell/module/az.datafactory/new-azdatafactoryencryptvalue) a hozzáférési hitelesítő adat AzDataFactoryEncryptValue kimenete. |No |

### <a name="using-anonymous-authentication"></a>Névtelen hitelesítés használata

```JSON
{
    "name": "hdfs",
    "properties":
    {
        "type": "Hdfs",
        "typeProperties":
        {
            "authenticationType": "Anonymous",
            "userName": "hadoop",
            "url" : "http://<machine>:50070/webhdfs/v1/",
            "gatewayName": "mygateway"
        }
    }
}
```

### <a name="using-windows-authentication"></a>Windows-hitelesítés használata

```JSON
{
    "name": "hdfs",
    "properties":
    {
        "type": "Hdfs",
        "typeProperties":
        {
            "authenticationType": "Windows",
            "userName": "<username>@<domain>.com (for Kerberos auth)",
            "password": "password",
            "url" : "http://<machine>:50070/webhdfs/v1/",
            "gatewayName": "mygateway"
        }
    }
}
```

## <a name="dataset-properties"></a>Adatkészlet tulajdonságai
Az adatkészletek definiálásához rendelkezésre álló & tulajdonságok teljes listáját az [adatkészletek létrehozása](data-factory-create-datasets.md) című cikkben találja. Az adathalmazok (például a struktúra, a rendelkezésre állás és a szabályzat) minden adatkészlet esetében hasonlóak (például az Azure SQL, az Azure Blob, az Azure Table stb.).

A **typeProperties** szakasz különbözik az egyes adatkészletek típusaitól, és információt nyújt az adattárban található adatok helyéről. A **fájlmegosztás** típusú (HDFS-adatkészletet tartalmazó) adatkészlet typeProperties szakasza a következő tulajdonságokkal rendelkezik

| Tulajdonság | Leírás | Kötelező |
| --- | --- | --- |
| folderPath |A mappa elérési útja. Például: `myfolder`<br/><br/>A karakterláncban a speciális karaktereknél használja a Escape karaktert. Például: a folder\subfolder esetében a mappa \\ \\ almappáját és a d:\samplefolder megadása esetén a d: samplefolder-t kell megadnia \\ \\ .<br/><br/>Ezt a tulajdonságot kombinálhatja a **partitionBy** , hogy a mappa elérési útjai a szelet kezdő/befejező dátum-és időpontjain alapulnak. |Yes |
| fileName |Adja meg a fájl nevét a **folderPath** , ha azt szeretné, hogy a tábla egy adott fájlra hivatkozzon a mappában. Ha nem ad meg értéket ehhez a tulajdonsághoz, a tábla a mappában található összes fájlra mutat.<br/><br/>Ha a fájlnév nincs megadva egy kimeneti adatkészlethez, a létrehozott fájl neve a következő formátumú lesz: <br/><br/>`Data.<Guid>.txt` (például: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt |No |
| partitionedBy |a partitionedBy használható egy dinamikus folderPath, az idősorozat-adatfájlok fájlnevének megadására. Példa: az folderPath paramétert minden óra adatértékhez. |No |
| formátumban | A következő típusú formátumok támogatottak: **Szövegformátum**, **JsonFormat**, **AvroFormat**, **OrcFormat**, **ParquetFormat**. A **Type (típus** ) tulajdonságot állítsa a Format értékre a következő értékek egyikére. További információkért lásd: [Szövegformátum](data-factory-supported-file-and-compression-formats.md#text-format), JSON- [Formátum](data-factory-supported-file-and-compression-formats.md#json-format), [Avro formátum](data-factory-supported-file-and-compression-formats.md#avro-format), [ork-formátum](data-factory-supported-file-and-compression-formats.md#orc-format)és a [parketta formátuma](data-factory-supported-file-and-compression-formats.md#parquet-format) című rész. <br><br> Ha **fájlokat szeretne másolni** a fájl alapú tárolók között (bináris másolás), ugorja át a formátum szakaszt mind a bemeneti, mind a kimeneti adatkészlet-definíciókban. |No |
| tömörítés | Adja meg az adattömörítés típusát és szintjét. A támogatott típusok a következők: **gzip**, **deflate**, **BZip2** és **ZipDeflate**. A támogatott szintek a következők: **optimális** és **leggyorsabb**. További információ: [fájl-és Tömörítési formátumok Azure Data Factoryban](data-factory-supported-file-and-compression-formats.md#compression-support). |No |

> [!NOTE]
> a filename és a fileFilter nem használható egyszerre.

### <a name="using-partionedby-property"></a>A partionedBy tulajdonság használata
Ahogy azt az előző szakaszban is említettük, megadhat egy dinamikus folderPath és fájlnevet az idősorozat-adatokhoz a **partitionedBy** tulajdonsággal, [Data Factory függvénnyel és a rendszerváltozókkal](data-factory-functions-variables.md).

Ha többet szeretne megtudni az idősorozat-adatkészletekről, az ütemezésről és a szeletekről, tekintse meg az [adatkészletek létrehozása](data-factory-create-datasets.md), az [Ütemezés & végrehajtás](data-factory-scheduling-and-execution.md)és a [folyamatok létrehozása](data-factory-create-pipelines.md) című cikket.

#### <a name="sample-1"></a>1. példa:

```JSON
"folderPath": "wikidatagateway/wikisampledataout/{Slice}",
"partitionedBy":
[
    { "name": "Slice", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyyMMddHH" } },
],
```
Ebben a példában a ({slice}) helyére Data Factory rendszerváltozó SliceStart értéke (YYYYMMDDHH) van megadva. A SliceStart a szelet kezdő időpontját jelöli. A folderPath különbözik az egyes szeletekhez. Például: tulajdonság beállítása wikidatagateway/wikisampledataout/2014100103 vagy tulajdonság beállítása wikidatagateway/wikisampledataout/2014100104.

#### <a name="sample-2"></a>2. minta:

```JSON
"folderPath": "wikidatagateway/wikisampledataout/{Year}/{Month}/{Day}",
"fileName": "{Hour}.csv",
"partitionedBy":
 [
    { "name": "Year", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyy" } },
    { "name": "Month", "value": { "type": "DateTime", "date": "SliceStart", "format": "MM" } },
    { "name": "Day", "value": { "type": "DateTime", "date": "SliceStart", "format": "dd" } },
    { "name": "Hour", "value": { "type": "DateTime", "date": "SliceStart", "format": "hh" } }
],
```
Ebben a példában a SliceStart év, hónap, nap és időpont a folderPath és a fájlnév tulajdonságai által használt különálló változókra van kinyerve.

## <a name="copy-activity-properties"></a>Másolási tevékenység tulajdonságai
A tevékenységek definiálásához elérhető & tulajdonságok teljes listáját a [folyamatok létrehozása](data-factory-create-pipelines.md) című cikkben találja. A tulajdonságok, például a név, a leírás, a bemeneti és a kimeneti táblák, valamint a házirendek minden típusú tevékenységhez elérhetők.

Míg a tevékenység typeProperties szakaszában elérhető tulajdonságok az egyes tevékenységtípusok esetében eltérőek. Másolási tevékenység esetén a források és a nyelők típusaitól függően változnak.

Másolási tevékenység esetén ha a forrás típusa **FileSystemSource** , a következő tulajdonságok érhetők el a typeProperties szakaszban:

A **FileSystemSource** a következő tulajdonságokat támogatja:

| Tulajdonság | Leírás | Megengedett értékek | Kötelező |
| --- | --- | --- | --- |
| rekurzív |Azt jelzi, hogy az adatok rekurzív módon olvashatók-e az alárendelt mappákból, vagy csak a megadott mappából. |Igaz, hamis (alapértelmezett) |No |

## <a name="supported-file-and-compression-formats"></a>Támogatott fájl- és tömörítési formátumok
A részletekért tekintse meg a [fájl-és tömörítési formátumokat Azure Data Factory](data-factory-supported-file-and-compression-formats.md) cikkben.

## <a name="json-example-copy-data-from-on-premises-hdfs-to-azure-blob"></a>JSON-példa: adatok másolása helyszíni HDFS az Azure Blobba
Ez a minta bemutatja, hogyan másolhat adatok egy helyszíni HDFS az Azure Blob Storageba. Az Adatmásolás azonban **közvetlenül** az [itt](data-factory-data-movement-activities.md#supported-data-stores-and-formats) megadott, a Azure Data Factoryban található másolási tevékenység használatával másolhatók.  

A minta JSON-definíciókat biztosít a következő Data Factory entitásokhoz. Ezekkel a definíciókkal olyan folyamatokat hozhat létre, amelyek a [Visual Studióval](data-factory-copy-activity-tutorial-using-visual-studio.md) vagy [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md)használatával másolják az HDFS-ből az Azure-Blob Storageba.

1. [OnPremisesHdfs](#linked-service-properties)típusú társított szolgáltatás.
2. [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties)típusú társított szolgáltatás.
3. [Fájlmegosztás](#dataset-properties)típusú bemeneti [adatkészlet](data-factory-create-datasets.md) .
4. [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties)típusú kimeneti [adatkészlet](data-factory-create-datasets.md) .
5. [FileSystemSource](#copy-activity-properties) és [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties)használó másolási tevékenységgel rendelkező [folyamat](data-factory-create-pipelines.md) .

A minta óránként másolja az adatait egy helyszíni HDFS egy Azure-blobba. Az ezekben a mintákban használt JSON-tulajdonságokat a mintákat követő szakaszokban ismertetjük.

Első lépésként állítsa be az adatkezelési átjárót. Az [adatáthelyezés a helyszíni helyszínek és a felhő között](data-factory-move-data-between-onprem-and-cloud.md) című cikk utasításait.

**HDFS társított szolgáltatás:** Ez a példa a Windows-hitelesítést használja. A használható hitelesítés különböző típusaival kapcsolatban lásd: [HDFS társított szolgáltatás](#linked-service-properties) szakasza.

```JSON
{
    "name": "HDFSLinkedService",
    "properties":
    {
        "type": "Hdfs",
        "typeProperties":
        {
            "authenticationType": "Windows",
            "userName": "Administrator",
            "password": "password",
            "url" : "http://<machine>:50070/webhdfs/v1/",
            "gatewayName": "mygateway"
        }
    }
}
```

**Azure Storage-beli társított szolgáltatás:**

```JSON
{
  "name": "AzureStorageLinkedService",
  "properties": {
    "type": "AzureStorage",
    "typeProperties": {
      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
    }
  }
}
```

**HDFS bemeneti adatkészlete:** Ez az adatkészlet a következő HDFS-mappára hivatkozik: DataTransfer/UnitTest/. A folyamat a mappában lévő összes fájlt átmásolja a célhelyre.

A "külső": "true" beállítás azt tájékoztatja a Data Factory szolgáltatást, hogy az adatkészlet kívül esik az adat-előállítón, és nem az adat-előállító tevékenysége.

```JSON
{
    "name": "InputDataset",
    "properties": {
        "type": "FileShare",
        "linkedServiceName": "HDFSLinkedService",
        "typeProperties": {
            "folderPath": "DataTransfer/UnitTest/"
        },
        "external": true,
        "availability": {
            "frequency": "Hour",
            "interval":  1
        }
    }
}
```

**Azure-Blob kimeneti adatkészlete:**

A rendszer óránként egy új blobba írja az adatbevitelt (frekvencia: óra, intervallum: 1). A blob mappájának elérési útját a rendszer dinamikusan kiértékeli a feldolgozás alatt álló szelet kezdési időpontja alapján. A mappa elérési útja a kezdési idő év, hónap, nap és óra részét használja.

```JSON
{
    "name": "OutputDataset",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties": {
            "folderPath": "mycontainer/hdfs/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
            "format": {
                "type": "TextFormat",
                "rowDelimiter": "\n",
                "columnDelimiter": "\t"
            },
            "partitionedBy": [
                {
                    "name": "Year",
                    "value": {
                        "type": "DateTime",
                        "date": "SliceStart",
                        "format": "yyyy"
                    }
                },
                {
                    "name": "Month",
                    "value": {
                        "type": "DateTime",
                        "date": "SliceStart",
                        "format": "MM"
                    }
                },
                {
                    "name": "Day",
                    "value": {
                        "type": "DateTime",
                        "date": "SliceStart",
                        "format": "dd"
                    }
                },
                {
                    "name": "Hour",
                    "value": {
                        "type": "DateTime",
                        "date": "SliceStart",
                        "format": "HH"
                    }
                }
            ]
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        }
    }
}
```

**Másolási tevékenység egy folyamatban a fájlrendszer forrásával és a blob-fogadóval:**

A folyamat tartalmaz egy másolási tevékenységet, amely a bemeneti és a kimeneti adatkészletek használatára van konfigurálva, és óránkénti futásra van ütemezve. A folyamat JSON-definíciójában a **forrás** típusa **FileSystemSource** értékre van állítva, a **fogadó típusa** pedig **BlobSink**. A **lekérdezési** tulajdonsághoz megadott SQL-lekérdezés a másoláshoz az elmúlt órában kijelöli az összes adatforrást.

```JSON
{
    "name": "pipeline",
    "properties":
    {
        "activities":
        [
            {
                "name": "HdfsToBlobCopy",
                "inputs": [ {"name": "InputDataset"} ],
                "outputs": [ {"name": "OutputDataset"} ],
                "type": "Copy",
                "typeProperties":
                {
                    "source":
                    {
                        "type": "FileSystemSource"
                    },
                    "sink":
                    {
                        "type": "BlobSink"
                    }
                },
                "policy":
                {
                    "concurrency": 1,
                    "executionPriorityOrder": "NewestFirst",
                    "retry": 1,
                    "timeout": "00:05:00"
                }
            }
        ],
        "start": "2014-06-01T18:00:00Z",
        "end": "2014-06-01T19:00:00Z"
    }
}
```

## <a name="use-kerberos-authentication-for-hdfs-connector"></a>Kerberos-hitelesítés használata a HDFS-összekötőhöz
Két lehetőség áll rendelkezésre a helyszíni környezet beállítására úgy, hogy a Kerberos-hitelesítést használják a HDFS-összekötőben. Kiválaszthatja, hogy melyik illik jobban az esethez.
* 1. lehetőség: [átjáró-gép csatlakoztatása a Kerberos-tartományban](#kerberos-join-realm)
* 2. lehetőség: [kölcsönös megbízhatóság engedélyezése a Windows és a Kerberos tartomány között](#kerberos-mutual-trust)

### <a name="option-1-join-gateway-machine-in-kerberos-realm"></a><a name="kerberos-join-realm"></a>1. lehetőség: átjáró-gép csatlakoztatása a Kerberos-tartományban

#### <a name="requirement"></a>Követelmény

* Az átjáró számítógépének csatlakoznia kell a Kerberos-tartományhoz, és nem csatlakozhat Windows-tartományhoz.

#### <a name="how-to-configure"></a>Konfigurálás:

**Az átjáró gépen:**

1. Futtassa a **Ksetup** segédprogramot a Kerberos KDC-kiszolgáló és-tartomány konfigurálásához.

   A gépet egy munkacsoport tagjaként kell konfigurálni, mert egy Kerberos-tartomány eltér a Windows-tartománytól. Ezt a Kerberos-tartomány beállításával és a KDC-kiszolgáló a következőképpen való hozzáadásával lehet megvalósítani. Szükség szerint cserélje le a *REALM.com* -t a saját megfelelő tartománynevére.

   ```cmd
   Ksetup /setdomain REALM.COM
   Ksetup /addkdc REALM.COM <your_kdc_server_address>
   ```

   **Indítsa újra** a gépet a 2 parancs végrehajtása után.

2. Ellenőrizze a konfigurációt a **Ksetup** paranccsal. A kimenetnek az alábbihoz hasonlónak kell lennie:

   ```cmd
   Ksetup
   default realm = REALM.COM (external)
   REALM.com:
      kdc = <your_kdc_server_address>
   ```

**Azure Data Factory:**

* Konfigurálja a HDFS-összekötőt a **Windows-hitelesítéssel** együtt a Kerberos egyszerű felhasználónevével és jelszavával a HDFS-adatforráshoz való csatlakozáshoz. A konfiguráció részleteinél keresse meg a [HDFS társított szolgáltatás tulajdonságai](#linked-service-properties) szakaszt.

### <a name="option-2-enable-mutual-trust-between-windows-domain-and-kerberos-realm"></a><a name="kerberos-mutual-trust"></a>2. lehetőség: kölcsönös megbízhatóság engedélyezése a Windows és a Kerberos tartomány között

#### <a name="requirement"></a>Követelmény
*    Az átjárót tartalmazó gépnek Windows-tartományhoz kell csatlakoznia.
*    Engedéllyel kell rendelkeznie a tartományvezérlő beállításainak frissítéséhez.

#### <a name="how-to-configure"></a>Konfigurálás:

> [!NOTE]
> Cserélje le a REALM.COM és a AD.COM-t a következő oktatóanyagban a saját megfelelő tartományára és tartományvezérlőre szükség szerint.

**KDC-kiszolgálón:**

1. Szerkessze a KDC konfigurációját a **krb5. conf** fájlban, hogy a KDC megbízzon a Windows-tartományon a következő konfigurációs sablonra hivatkozva. Alapértelmezés szerint a konfiguráció a következő helyen található: **/etc/krb5.conf állományt**.

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

   A konfiguráció után **indítsa újra** a KDC szolgáltatást.

2. Készítse elő a **krbtgt/REALM. COM \@ ad.com** nevű rendszerbiztonsági tag a KDC-kiszolgálón a következő paranccsal:

   ```cmd
   Kadmin> addprinc krbtgt/REALM.COM@AD.COM
   ```

3. **Hadoop.Security.auth_to_local** HDFS szolgáltatás konfigurációs fájljában adja hozzá a t `RULE:[1:$1@$0](.*\@AD.COM)s/\@.*//` .

**Tartományvezérlőn:**

1. A következő **Ksetup** parancsok futtatásával vegyen fel egy tartományi bejegyzést:

   ```cmd
   Ksetup /addkdc REALM.COM <your_kdc_server_address>
   ksetup /addhosttorealmmap HDFS-service-FQDN REALM.COM
   ```

2. Megbízhatósági kapcsolat létrehozása a Windows-tartományból a Kerberos tartományba. a [password] a fő **krbtgt/REALM. COM \@ ad.com** tartozó jelszó.

   ```cmd
   netdom trust REALM.COM /Domain: AD.COM /add /realm /passwordt:[password]
   ```

3. Válassza ki a Kerberosban használt titkosítási algoritmust.

   1. Lépjen a Kiszolgálókezelő > Csoportházirend felügyeleti > tartomány > Csoportházirend objektumok > alapértelmezett vagy aktív tartományi házirend elemre, és szerkessze a következőt:.

   2. A **csoportházirend-felügyeleti szerkesztő** előugró ablakban válassza a számítógép konfigurációja > házirendek > Windows beállítások > biztonsági beállítások > helyi házirendek > biztonsági beállítások, majd **a hálózati biztonság konfigurálása: a Kerberos számára engedélyezett titkosítási típusok konfigurálása** című részt.

   3. Válassza ki a KDC-hoz való csatlakozáskor használni kívánt titkosítási algoritmust. Általában egyszerűen kiválaszthatja az összes beállítást.

      ![A Kerberos konfigurációjának titkosítási típusai](media/data-factory-hdfs-connector/config-encryption-types-for-kerberos.png)

   4. A **Ksetup** parancs használatával megadhatja az adott tartományban használandó titkosítási algoritmust.

      ```cmd
      ksetup /SetEncTypeAttr REALM.COM DES-CBC-CRC DES-CBC-MD5 RC4-HMAC-MD5 AES128-CTS-HMAC-SHA1-96 AES256-CTS-HMAC-SHA1-96
      ```

4. Hozzon létre egy leképezést a tartományi fiók és a Kerberos-tag között a Kerberos-rendszerbiztonsági tag Windows-tartományban való használatához.

   1. Indítsa el a felügyeleti eszközöket > **Active Directory felhasználókat és számítógépeket**.

   2. A speciális szolgáltatások konfigurálásához kattintson a  >  **speciális szolgáltatások** megtekintése elemre.

   3. Keresse meg azt a fiókot, amelyhez leképezéseket szeretne létrehozni, majd kattintson a jobb gombbal a **név-hozzárendelések** megtekintéséhez > kattintson a **Kerberos-nevek** fülre.

   4. Adjon hozzá egy rendszerbiztonsági tag a tartományhoz.

      ![Térkép biztonsági identitása](media/data-factory-hdfs-connector/map-security-identity.png)

**Az átjáró gépen:**

* A következő **Ksetup** parancsok futtatásával vegyen fel egy tartományi bejegyzést.

   ```cmd
   Ksetup /addkdc REALM.COM <your_kdc_server_address>
   ksetup /addhosttorealmmap HDFS-service-FQDN REALM.COM
   ```

**Azure Data Factory:**

* Konfigurálja a HDFS-összekötőt a **Windows-hitelesítéssel** együtt a tartományi fiókkal vagy a Kerberos-rendszerbiztonsági tag használatával a HDFS-adatforráshoz való csatlakozáshoz. A konfiguráció részleteinél keresse meg a [HDFS társított szolgáltatás tulajdonságai](#linked-service-properties) szakaszt.

> [!NOTE]
> Ha az oszlopokat a forrás adatkészletből a fogadó adatkészletből származó oszlopokra kívánja leképezni, tekintse meg [Azure Data Factory az adatkészlet oszlopainak](data-factory-map-columns.md)

## <a name="performance-and-tuning"></a>Teljesítmény és hangolás
A [másolási tevékenység teljesítményének & hangolási útmutatójában](data-factory-copy-activity-performance.md) megismerheti azokat a főbb tényezőket, amelyek hatással vannak az adatáthelyezés (másolási tevékenység) teljesítményére Azure Data Factory és az optimalizálás különféle módjaival.
