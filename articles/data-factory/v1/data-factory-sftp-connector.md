---
title: Adatok áthelyezése az SFTP-kiszolgálóról Azure Data Factory használatával
description: Ismerje meg, hogyan helyezhet át egy helyszíni vagy egy felhőalapú SFTP-kiszolgáló adatait Azure Data Factory használatával.
author: linda33wj
ms.author: jingwang
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/12/2018
robots: noindex
ms.openlocfilehash: b40e9dc83629362da899d2b5ff29ad42e21c4e32
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "100382650"
---
# <a name="move-data-from-an-sftp-server-using-azure-data-factory"></a>Adatok áthelyezése SFTP-kiszolgálóról Azure Data Factory használatával
> [!div class="op_single_selector" title1="Válassza ki az Ön által használt Data Factory-szolgáltatás verzióját:"]
> * [1-es verzió](data-factory-sftp-connector.md)
> * [2-es verzió (aktuális verzió)](../connector-sftp.md)

> [!NOTE]
> Ez a cikk a Data Factory 1-es verziójára vonatkozik. Ha a Data Factory szolgáltatás aktuális verzióját használja, tekintse [meg a SFTPconnector a v2-ben](../connector-sftp.md)című témakört.

Ez a cikk azt ismerteti, hogyan használható a másolási tevékenység a Azure Data Factoryban, hogy egy helyszíni/Felhőbeli SFTP-kiszolgálóról egy támogatott fogadó adattárba helyezze át az adatátvitelt. Ez a cikk az adattovábbítási [tevékenységekről](data-factory-data-movement-activities.md) szóló cikket ismerteti, amely általános áttekintést nyújt a másolási tevékenységgel végzett adatáthelyezésről és a forrásként/mosogatóként támogatott adattárakról.

A adatfeldolgozó jelenleg csak az SFTP-kiszolgálóról származó adatok áthelyezését támogatja más adattárakba, az adatok más adattárból SFTP-kiszolgálóra történő áthelyezését azonban nem. A helyszíni és a felhőalapú SFTP-kiszolgálókat is támogatja.

> [!NOTE]
> A másolási tevékenység nem törli a forrásfájlt, miután sikeresen átmásolta a célhelyre. Ha sikeres másolás után törölni kell a forrásfájlt, hozzon létre egy egyéni tevékenységet a fájl törléséhez, és használja a folyamatot a folyamatban.

## <a name="supported-scenarios-and-authentication-types"></a>Támogatott forgatókönyvek és hitelesítési típusok
Ezt az SFTP-összekötőt használhatja a **Cloud SFTP-kiszolgálókról és a helyszíni SFTP-kiszolgálókról** származó adatok másolásához is. Az SFTP-kiszolgálóhoz való csatlakozáskor az **alapszintű** és a **SshPublicKey** hitelesítési típusok támogatottak.

Amikor helyszíni SFTP-kiszolgálóról másol Adatmásolást, telepítenie kell egy adatkezelés átjárót a helyszíni környezetben/Azure-beli virtuális gépen. Az átjáróval kapcsolatos részletekért tekintse meg [adatkezelés átjárót](data-factory-data-management-gateway.md) . Az átjáró beállításával és használatával kapcsolatos részletes útmutatásért lásd: az [adatáthelyezés a helyszíni helyszínek és a felhő között](data-factory-move-data-between-onprem-and-cloud.md) .

## <a name="getting-started"></a>Első lépések
Létrehozhat egy másolási tevékenységgel rendelkező folyamatot, amely egy SFTP-forrásból származó adatok különböző eszközök/API-k használatával helyezhető át.

- A folyamat létrehozásának legegyszerűbb módja a **Másolás varázsló** használata. Tekintse meg az [oktatóanyag: folyamat létrehozása a másolás varázslóval](data-factory-copy-data-wizard-tutorial.md) című témakört, amely gyors áttekintést nyújt a folyamat létrehozásáról az adatmásolási varázsló használatával.

- A következő eszközöket is használhatja a folyamat létrehozásához: **Visual Studio**, **Azure PowerShell**, **Azure Resource Manager template**, **.NET API** és **REST API**. A másolási tevékenységgel rendelkező folyamat létrehozásával kapcsolatos részletes utasításokat a [másolási tevékenységről szóló oktatóanyagban](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) talál. A JSON-minták az SFTP-kiszolgálóról az Azure Blob Storageba másolt adatok másolásához lásd a [JSON-példa: adatok másolása az SFTP-kiszolgálóról az Azure blobba](#json-example-copy-data-from-sftp-server-to-azure-blob) című szakaszt a jelen cikkből.

## <a name="linked-service-properties"></a>Társított szolgáltatás tulajdonságai
A következő táblázat az FTP-hez társított szolgáltatáshoz tartozó JSON-elemek leírását tartalmazza.

| Tulajdonság | Leírás | Kötelező |
| --- | --- | --- |
| típus | A Type tulajdonságot a következőre kell beállítani: `Sftp` . |Yes |
| gazda | Az SFTP-kiszolgáló neve vagy IP-címe. |Yes |
| port |Az a port, amelyen az SFTP-kiszolgáló figyel. Az alapértelmezett érték: 21 |No |
| authenticationType |Adja meg a hitelesítési típust. Megengedett értékek: **Alapszintű**, **SshPublicKey**. <br><br> Tekintse meg az [alapszintű hitelesítés használatát](#using-basic-authentication) és az [SSH nyilvános kulcsú hitelesítés](#using-ssh-public-key-authentication) használatát ismertető SZAKASZT további tulajdonságok és JSON-minták használatával. |Yes |
| skipHostKeyValidation | Megadhatja, hogy kihagyja-e a gazdagép kulcsának érvényesítését | Nem. Az alapértelmezett érték: false |
| hostKeyFingerprint | A gazdagép kulcsának ujj-nyomtatását határozza meg. | Igen, ha a `skipHostKeyValidation` értéke hamis.  |
| Átjáró neve |A helyszíni SFTP-kiszolgálóhoz való kapcsolódáshoz használandó adatkezelés átjáró neve. | Igen, ha helyszíni SFTP-kiszolgálóról másol adatok. |
| encryptedCredential | Titkosított hitelesítő adatok az SFTP-kiszolgálóhoz való hozzáféréshez. Automatikusan jön létre, ha alapszintű hitelesítést (username + Password) vagy SshPublicKey hitelesítést (Felhasználónév + titkos kulcs elérési útja vagy tartalma) ad meg a másolási varázslóban vagy a ClickOnce előugró ablakban. | Nem. Csak a helyszíni SFTP-kiszolgálóról származó adatok másolásakor érvényes. |

### <a name="using-basic-authentication"></a>Alapszintű hitelesítés használata

Ha alapszintű hitelesítést szeretne használni, állítsa a as értékre `authenticationType` `Basic` , és adja meg a következő TULAJDONSÁGOKAT az SFTP-összekötő általános beállításai mellett az utolsó szakaszban bemutatott módon:

| Tulajdonság | Leírás | Kötelező |
| --- | --- | --- |
| username | Az a felhasználó, aki hozzáfér az SFTP-kiszolgálóhoz. |Yes |
| jelszó | A felhasználó jelszava (username). | Yes |

#### <a name="example-basic-authentication"></a>Példa: alapszintű hitelesítés
```json
{
    "name": "SftpLinkedService",
    "properties": {
        "type": "Sftp",
        "typeProperties": {
            "host": "mysftpserver",
            "port": 22,
            "authenticationType": "Basic",
            "username": "xxx",
            "password": "xxx",
            "skipHostKeyValidation": false,
            "hostKeyFingerPrint": "ssh-rsa 2048 xx:00:00:00:xx:00:x0:0x:0x:0x:0x:00:00:x0:x0:00",
            "gatewayName": "mygateway"
        }
    }
}
```

#### <a name="example-basic-authentication-with-encrypted-credential"></a>Példa: egyszerű hitelesítés titkosított hitelesítő adatokkal

```JSON
{
    "name": "SftpLinkedService",
    "properties": {
        "type": "Sftp",
        "typeProperties": {
            "host": "mysftpserver",
            "port": 22,
            "authenticationType": "Basic",
            "username": "xxx",
            "encryptedCredential": "xxxxxxxxxxxxxxxxx",
            "skipHostKeyValidation": false,
            "hostKeyFingerPrint": "ssh-rsa 2048 xx:00:00:00:xx:00:x0:0x:0x:0x:0x:00:00:x0:x0:00",
            "gatewayName": "mygateway"
        }
      }
}
```

### <a name="using-ssh-public-key-authentication"></a>Nyilvános SSH-kulcsos hitelesítés használata

Az SSH nyilvános kulcsú hitelesítés használatához állítsa a `authenticationType` as lehetőséget `SshPublicKey` , majd adja meg az alábbi TULAJDONSÁGOKAT az SFTP-összekötő általános beállításai mellett, az utolsó szakaszban bemutatott módon:

| Tulajdonság | Leírás | Kötelező |
| --- | --- | --- |
| username |Az SFTP-kiszolgálóhoz hozzáféréssel rendelkező felhasználó |Yes |
| privateKeyPath | Az átjáró által elérhető titkos kulcsfájl abszolút elérési útját határozza meg. | Válassza a `privateKeyPath` vagy a `privateKeyContent` . <br><br> Csak a helyszíni SFTP-kiszolgálóról származó adatok másolásakor érvényes. |
| privateKeyContent | A titkos kulcs tartalmának szerializált karakterlánca. A másolás varázsló elolvashatja a titkos kulcs fájlját, és automatikusan kibonthatja a titkos kulcs tartalmát. Ha bármilyen más eszközt vagy SDK-t használ, használja helyette a privateKeyPath tulajdonságot. | Válassza a `privateKeyPath` vagy a `privateKeyContent` . |
| Jelszót | Adja meg a pass kifejezést/jelszót a titkos kulcs visszafejtéséhez, ha a kulcsot egy pass kifejezés védi. | Igen, ha a titkos kulcs fájlját egy pass kifejezés védi. |

> [!NOTE]
> Az SFTP-összekötő támogatja az RSA/DSA OpenSSH-kulcsot. Győződjön meg arról, hogy a kulcsfájl tartalma "-----BEGIN [RSA/DSA] titkos kulccsal-----". Ha a titkos kulcs fájlja PPK-formátumú fájl, a PuTTY eszközzel konvertálhatja a. PPK-ből az OpenSSH formátumba.

#### <a name="example-sshpublickey-authentication-using-private-key-filepath"></a>Példa: SshPublicKey-hitelesítés a titkos kulcs filePath használatával

```json
{
    "name": "SftpLinkedServiceWithPrivateKeyPath",
    "properties": {
        "type": "Sftp",
        "typeProperties": {
            "host": "mysftpserver",
            "port": 22,
            "authenticationType": "SshPublicKey",
            "username": "xxx",
            "privateKeyPath": "D:\\privatekey_openssh",
            "passPhrase": "xxx",
            "skipHostKeyValidation": true,
            "gatewayName": "mygateway"
        }
    }
}
```

#### <a name="example-sshpublickey-authentication-using-private-key-content"></a>Példa: SshPublicKey hitelesítés a titkos kulcs tartalmának használatával

```json
{
    "name": "SftpLinkedServiceWithPrivateKeyContent",
    "properties": {
        "type": "Sftp",
        "typeProperties": {
            "host": "mysftpserver.westus.cloudapp.azure.com",
            "port": 22,
            "authenticationType": "SshPublicKey",
            "username": "xxx",
            "privateKeyContent": "<base64 string of the private key content>",
            "passPhrase": "xxx",
            "skipHostKeyValidation": true
        }
    }
}
```

## <a name="dataset-properties"></a>Adatkészlet tulajdonságai
Az adatkészletek definiálásához rendelkezésre álló & tulajdonságok teljes listáját az [adatkészletek létrehozása](data-factory-create-datasets.md) című cikkben találja. Az adatkészletek JSON-típusai, például a struktúra, a rendelkezésre állás és a szabályzat, az összes adatkészlet esetében hasonlóak.

A **typeProperties** szakasz eltérő az egyes adatkészletek esetében. Az adatkészlet típusára jellemző információkat biztosít. A **fájlmegosztás** adatkészlet típusú adatkészlet typeProperties szakasza a következő tulajdonságokkal rendelkezik:

| Tulajdonság | Leírás | Kötelező |
| --- | --- | --- |
| folderPath |A mappa alárendelt elérési útja. A karakterláncban a speciális karaktereknél használja a Escape karaktert. Példákat a következő témakörben talál: példa társított szolgáltatás és adatkészlet-definíciók.<br/><br/>Ezt a tulajdonságot kombinálhatja a **partitionBy** , hogy a mappa elérési útjai a szelet kezdő/befejező dátum-és időpontjain alapulnak. |Yes |
| fileName |Adja meg a fájl nevét a **folderPath** , ha azt szeretné, hogy a tábla egy adott fájlra hivatkozzon a mappában. Ha nem ad meg értéket ehhez a tulajdonsághoz, a tábla a mappában található összes fájlra mutat.<br/><br/>Ha a fájlnév nincs megadva egy kimeneti adatkészlethez, a létrehozott fájl neve a következő formátumú lesz: <br/><br/>`Data.<Guid>.txt` (Példa: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt |No |
| fileFilter |Adja meg azt a szűrőt, amelyet a folderPath található fájlok részhalmazának kiválasztására kíván használni az összes fájl helyett.<br/><br/>Az engedélyezett értékek a következők: `*` (több karakter) és `?` (egyetlen karakter).<br/><br/>1. példa: `"fileFilter": "*.log"`<br/>2. példa: `"fileFilter": 2014-1-?.txt"`<br/><br/> a fileFilter egy bemeneti fájlmegosztás adatkészlet esetében alkalmazható. Ez a tulajdonság nem támogatott a HDFS. |No |
| partitionedBy |a partitionedBy használható egy dinamikus folderPath, az idősorozat-adatfájlok fájlnevének megadására. Például a folderPath paramétert minden óra adatértéknél. |No |
| formátumban | A következő típusú formátumok támogatottak: **Szövegformátum**, **JsonFormat**, **AvroFormat**, **OrcFormat**, **ParquetFormat**. A **Type (típus** ) tulajdonságot állítsa a Format értékre a következő értékek egyikére. További információkért lásd: [Szövegformátum](data-factory-supported-file-and-compression-formats.md#text-format), JSON- [Formátum](data-factory-supported-file-and-compression-formats.md#json-format), [Avro formátum](data-factory-supported-file-and-compression-formats.md#avro-format), [ork-formátum](data-factory-supported-file-and-compression-formats.md#orc-format)és a [parketta formátuma](data-factory-supported-file-and-compression-formats.md#parquet-format) című rész. <br><br> Ha **fájlokat szeretne másolni** a fájl alapú tárolók között (bináris másolás), ugorja át a formátum szakaszt mind a bemeneti, mind a kimeneti adatkészlet-definíciókban. |No |
| tömörítés | Adja meg az adattömörítés típusát és szintjét. A támogatott típusok a következők: **gzip**, **deflate**, **BZip2** és **ZipDeflate**. A támogatott szintek a következők: **optimális** és **leggyorsabb**. További információ: [fájl-és Tömörítési formátumok Azure Data Factoryban](data-factory-supported-file-and-compression-formats.md#compression-support). |No |
| useBinaryTransfer |Adja meg, hogy a bináris átviteli módot használja-e. A bináris mód és a hamis ASCII esetében igaz. Alapértelmezett érték: true (igaz). Ez a tulajdonság csak akkor használható, ha a társított társított szolgáltatás típusa: FTP. |No |

> [!NOTE]
> a filename és a fileFilter nem használható egyszerre.

### <a name="using-partionedby-property"></a>A partionedBy tulajdonság használata
Ahogy azt az előző szakaszban is említettük, megadhat egy dinamikus folderPath, a fájlnevet az idősoros adatsorokhoz a partitionedBy használatával. Ezt megteheti a Data Factory makrókkal és a SliceStart rendszerváltozóval, amely az adott adatszeletek logikai időtartamát jelzi.

Az idősorozat-adatkészletek, az ütemezés és a szeletek megismeréséhez lásd: [adatkészletek létrehozása](data-factory-create-datasets.md), [Ütemezés & végrehajtás](data-factory-scheduling-and-execution.md)és [folyamatok létrehozása](data-factory-create-pipelines.md) .

#### <a name="sample-1"></a>1. példa:

```json
"folderPath": "wikidatagateway/wikisampledataout/{Slice}",
"partitionedBy":
[
    { "name": "Slice", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyyMMddHH" } },
],
```
Ebben a példában a ({slice}) helyére Data Factory rendszerváltozó SliceStart értéke (YYYYMMDDHH) van megadva. A SliceStart a szelet kezdő időpontját jelöli. A folderPath különbözik az egyes szeletekhez. Például: tulajdonság beállítása wikidatagateway/wikisampledataout/2014100103 vagy tulajdonság beállítása wikidatagateway/wikisampledataout/2014100104.

#### <a name="sample-2"></a>2. minta:

```json
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

Míg a tevékenység typeProperties szakaszában elérhető tulajdonságok az egyes tevékenységtípusok esetében eltérőek. Másolási tevékenység esetén a típus tulajdonságai a források és a mosdók típusától függően változnak.

[!INCLUDE [data-factory-file-system-source](../../../includes/data-factory-file-system-source.md)]

## <a name="supported-file-and-compression-formats"></a>Támogatott fájl- és tömörítési formátumok
A részletekért tekintse meg a [fájl-és tömörítési formátumokat Azure Data Factory](data-factory-supported-file-and-compression-formats.md) cikkben.

## <a name="json-example-copy-data-from-sftp-server-to-azure-blob"></a>JSON-példa: adatok másolása az SFTP-kiszolgálóról az Azure blobba
Az alábbi példa olyan JSON-definíciókat tartalmaz, amelyeket a [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) vagy a [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md)használatával hozhat létre a folyamat létrehozásához. Bemutatják, hogyan másolhatók az SFTP-forrásból származó adatok az Azure Blob Storageba. Az adatok azonban **közvetlenül** a forrásokból bármelyik forrásból átmásolhatók, ha a másolási tevékenység a Azure Data Factoryban [szerepel.](data-factory-data-movement-activities.md#supported-data-stores-and-formats)

> [!IMPORTANT]
> Ez a példa JSON-kódrészleteket biztosít. Nem tartalmaz részletes útmutatást az adatelőállító létrehozásához. Részletes útmutatásért lásd: az [adatáthelyezés a helyszíni helyszínek és a felhőalapú cikkek között](data-factory-move-data-between-onprem-and-cloud.md) .

A minta a következő adatgyári entitásokat tartalmazhatja:

* Egy [SFTP](#linked-service-properties)típusú társított szolgáltatás.
* [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties)típusú társított szolgáltatás.
* [Fájlmegosztás](#dataset-properties)típusú bemeneti [adatkészlet](data-factory-create-datasets.md) .
* [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties)típusú kimeneti [adatkészlet](data-factory-create-datasets.md) .
* [FileSystemSource](#copy-activity-properties) és [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties)használó másolási tevékenységgel rendelkező [folyamat](data-factory-create-pipelines.md) .

A minta óránként másolja egy SFTP-kiszolgáló adatait egy Azure-blobba. Az ezekben a mintákban használt JSON-tulajdonságokat a mintákat követő szakaszokban ismertetjük.

**SFTP társított szolgáltatás**

Ez a példa egyszerű szövegként használja az egyszerű hitelesítést a felhasználónévvel és a jelszóval. A következő módszerek egyikét is használhatja:

* Egyszerű hitelesítés titkosított hitelesítő adatokkal
* Nyilvános SSH-kulcsos hitelesítés

Lásd: az FTP-hez [társított szolgáltatás](#linked-service-properties) szakasza, amely a különböző típusú hitelesítésekhez használható.

```JSON

{
    "name": "SftpLinkedService",
    "properties": {
        "type": "Sftp",
        "typeProperties": {
            "host": "mysftpserver",
            "port": 22,
            "authenticationType": "Basic",
            "username": "myuser",
            "password": "mypassword",
            "skipHostKeyValidation": false,
            "hostKeyFingerPrint": "ssh-rsa 2048 xx:00:00:00:xx:00:x0:0x:0x:0x:0x:00:00:x0:x0:00",
            "gatewayName": "mygateway"
        }
    }
}
```
**Azure Storage társított szolgáltatás**

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
**SFTP bemeneti adatkészlet**

Ez az adatkészlet az SFTP-mappára `mysharedfolder` és-fájlra hivatkozik `test.csv` . A folyamat átmásolja a fájlt a célhelyre.

A "külső": "true" beállítás azt tájékoztatja a Data Factory szolgáltatást, hogy az adatkészlet kívül esik az adat-előállítón, és nem az adat-előállító tevékenysége.

```JSON
{
  "name": "SFTPFileInput",
  "properties": {
    "type": "FileShare",
    "linkedServiceName": "SftpLinkedService",
    "typeProperties": {
      "folderPath": "mysharedfolder",
      "fileName": "test.csv"
    },
    "external": true,
    "availability": {
      "frequency": "Hour",
      "interval": 1
    }
  }
}
```

**Azure blobkimeneti adatkészlet**

A rendszer óránként egy új blobba írja az adatbevitelt (frekvencia: óra, intervallum: 1). A blob mappájának elérési útját a rendszer dinamikusan kiértékeli a feldolgozás alatt álló szelet kezdési időpontja alapján. A mappa elérési útja a kezdési idő év, hónap, nap és óra részét használja.

```JSON
{
    "name": "AzureBlobOutput",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties": {
            "folderPath": "mycontainer/sftp/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
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

**Másolási tevékenységgel rendelkező folyamat**

A folyamat egy másolási tevékenységet tartalmaz, amely a bemeneti és a kimeneti adatkészletek használatára van konfigurálva, és óránkénti futásra van ütemezve. A folyamat JSON-definíciójában a **forrás** típusa **FileSystemSource** értékre van állítva, a **fogadó típusa** pedig **BlobSink**.

```JSON
{
    "name": "pipeline",
    "properties": {
        "activities": [{
            "name": "SFTPToBlobCopy",
            "inputs": [{
                "name": "SFTPFileInput"
            }],
            "outputs": [{
                "name": "AzureBlobOutput"
            }],
            "type": "Copy",
            "typeProperties": {
                "source": {
                    "type": "FileSystemSource"
                },
                "sink": {
                    "type": "BlobSink"
                }
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "NewestFirst",
                "retry": 1,
                "timeout": "00:05:00"
            }
        }],
        "start": "2017-02-20T18:00:00Z",
        "end": "2017-02-20T19:00:00Z"
    }
}
```

## <a name="performance-and-tuning"></a>Teljesítmény és hangolás
A [másolási tevékenység teljesítményének & hangolási útmutatójában](data-factory-copy-activity-performance.md) megismerheti azokat a főbb tényezőket, amelyek hatással vannak az adatáthelyezés (másolási tevékenység) teljesítményére Azure Data Factory és az optimalizálás különféle módjaival.

## <a name="next-steps"></a>Következő lépések
Lásd az alábbi cikkeket:

* A [másolási](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) tevékenységről szóló oktatóanyag részletesen ismerteti a folyamat másolási tevékenységgel történő létrehozását.
