---
title: Adatok áthelyezése FTP-kiszolgálóról Azure Data Factory használatával
description: Tudnivalók az adatok FTP-kiszolgálóról Azure Data Factory használatával történő áthelyezéséről.
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.date: 05/02/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: a8a8ca44fbdb7610f85bc53c23d502d2efb01c8b
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "100363950"
---
# <a name="move-data-from-an-ftp-server-by-using-azure-data-factory"></a>Adatok áthelyezése FTP-kiszolgálóról Azure Data Factory használatával
> [!div class="op_single_selector" title1="Válassza ki az Ön által használt Data Factory-szolgáltatás verzióját:"]
> * [1-es verzió](data-factory-ftp-connector.md)
> * [2-es verzió (aktuális verzió)](../connector-ftp.md)

> [!NOTE]
> Ez a cikk a Data Factory 1-es verziójára vonatkozik. Ha a Data Factory szolgáltatás aktuális verzióját használja, tekintse meg az [FTP-összekötőt a v2-ben](../connector-ftp.md).

Ez a cikk azt ismerteti, hogyan használható a másolási tevékenység a Azure Data Factoryban az adatok FTP-kiszolgálóról való áthelyezéséhez. Az [adattovábbítási tevékenységekről](data-factory-data-movement-activities.md) szóló cikkre épül, amely általános áttekintést nyújt az adatáthelyezésről a másolási tevékenységgel.

Az adatok másolása egy FTP-kiszolgálóról bármely támogatott fogadó adattárba lehetséges. A másolási tevékenység által mosogatóként támogatott adattárak listáját a [támogatott adattárak](data-factory-data-movement-activities.md#supported-data-stores-and-formats) táblázatban tekintheti meg. Data Factory jelenleg csak az FTP-kiszolgálóról más adattárakba helyezi át az adatok áthelyezését, az adatok más adattárakból egy FTP-kiszolgálóra való áthelyezését azonban nem. A helyszíni és a Felhőbeli FTP-kiszolgálókat is támogatja.

> [!NOTE]
> A másolási tevékenység nem törli a forrásfájlt, miután sikeresen átmásolta a célhelyre. Ha sikeres másolás után törölni kell a forrásfájlt, hozzon létre egy egyéni tevékenységet a fájl törléséhez, és használja a folyamatot a folyamatban.

## <a name="enable-connectivity"></a>Kapcsolat engedélyezése
Ha **helyszíni FTP-kiszolgálóról egy** Felhőbeli adattárba (például az Azure Blob Storage-ba) helyez át adatátvitelt, akkor adatkezelés átjárót kell telepítenie és használnia. A adatkezelés átjáró egy, a helyszíni gépre telepített ügyfél, amely lehetővé teszi a felhőalapú szolgáltatások számára a helyszíni erőforrásokhoz való kapcsolódást. Részletekért lásd: [adatkezelés átjáró](data-factory-data-management-gateway.md). Az átjáró beállításával és használatával kapcsolatos részletes utasításokért lásd: az [adatáthelyezés a helyszíni helyszínek és a felhő között](data-factory-move-data-between-onprem-and-cloud.md). Az átjáró használatával csatlakozhat egy FTP-kiszolgálóhoz, még akkor is, ha a kiszolgáló egy Azure-beli infrastruktúra-szolgáltatás (IaaS) virtuális gép (VM).

Az átjárót ugyanarra a helyszíni gépre vagy IaaS virtuális gépre is telepítheti az FTP-kiszolgálóként. Javasoljuk azonban, hogy az átjárót külön gépre vagy IaaS virtuális gépre telepítse, hogy elkerülje az erőforrás-tartalmat, és a jobb teljesítmény érdekében. Ha az átjárót külön gépre telepíti, a gépnek el kell tudnia érni az FTP-kiszolgálót.

## <a name="get-started"></a>Bevezetés
Létrehozhat egy másolási tevékenységgel rendelkező folyamatot, amely különböző eszközök vagy API-k használatával helyez át egy FTP-forrásból származó adatokkal.

A folyamat létrehozásának legegyszerűbb módja a **Data Factory másolási varázsló** használata. Lásd [: oktatóanyag: folyamat létrehozása a másolás varázslóval](data-factory-copy-data-wizard-tutorial.md) a gyors útmutatóhoz.

A következő eszközöket is használhatja a folyamat létrehozásához: **Visual Studio**, **PowerShell**, **Azure Resource Manager sablon**, **.NET API** és **REST API**. A másolási tevékenységgel rendelkező folyamat létrehozásával kapcsolatos részletes utasításokat a [másolási tevékenységről szóló oktatóanyagban](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) talál.

Függetlenül attól, hogy az eszközöket vagy API-kat használja, hajtsa végre a következő lépéseket egy olyan folyamat létrehozásához, amely egy forrás adattárból egy fogadó adattárba helyezi át az adattárolót:

1. **Társított szolgáltatások** létrehozása a bemeneti és kimeneti adattáraknak az adat-előállítóhoz való összekapcsolásához.
2. Hozzon létre **adatkészleteket** a másolási művelet bemeneti és kimeneti adatok ábrázolásához.
3. Hozzon **létre egy másolási tevékenységgel rendelkező folyamatot** , amely egy adatkészletet bemenetként és egy adatkészlet kimenetként való elvégzéséhez szükséges.

A varázsló használatakor a rendszer automatikusan létrehozza a Data Factory entitások (társított szolgáltatások, adatkészletek és a folyamat) JSON-definícióit. Ha eszközöket vagy API-kat használ (kivéve a .NET API-t), akkor ezeket a Data Factory entitásokat JSON-formátumban kell megadnia. Az adatok FTP-adattárból való másolásához használt Data Factory JSON-definíciókkal rendelkező minta esetében tekintse meg a jelen cikk [JSON-példa: adatok másolása FTP-kiszolgálóról az Azure blobra](#json-example-copy-data-from-ftp-server-to-azure-blob) című szakaszát.

> [!NOTE]
> A támogatott fájl-és tömörítési formátumokkal kapcsolatos részletekért tekintse [meg a Azure Data Factory fájl-és tömörítési formátumait](data-factory-supported-file-and-compression-formats.md).

A következő szakaszokban részletesen ismertetjük a JSON-tulajdonságokat, amelyek a Data Factory az FTP-hez kapcsolódó entitások definiálására szolgálnak.

## <a name="linked-service-properties"></a>Társított szolgáltatás tulajdonságai
A következő táblázat az FTP-hez társított szolgáltatáshoz tartozó JSON-elemeket ismerteti.

| Tulajdonság | Leírás | Kötelező | Alapértelmezett |
| --- | --- | --- | --- |
| típus |Állítsa be ezt a FTP. |Yes |&nbsp; |
| gazda |Adja meg az FTP-kiszolgáló nevét vagy IP-címét. |Yes |&nbsp; |
| authenticationType |Adja meg a hitelesítési típust. |Yes |Alapszintű, névtelen |
| username |Válassza ki azt a felhasználót, aki hozzáfér az FTP-kiszolgálóhoz. |No |&nbsp; |
| jelszó |A felhasználó (username) jelszavának megadása. |No |&nbsp; |
| encryptedCredential |A titkosított hitelesítő adatok megadása az FTP-kiszolgáló eléréséhez. |No |&nbsp; |
| Átjáró neve |Adja meg az átjáró nevét adatkezelés átjárón egy helyszíni FTP-kiszolgálóhoz való kapcsolódáshoz. |No |&nbsp; |
| port |Itt adhatja meg azt a portot, amelyen az FTP-kiszolgáló figyel. |No |21 |
| enableSsl |Adja meg, hogy az FTP-t SSL/TLS-csatornán keresztül kívánja-e használni. |No |true |
| enableServerCertificateValidation |Adja meg, hogy engedélyezi-e a kiszolgáló TLS/SSL-tanúsítványának érvényesítését az SSL/TLS-csatornán keresztüli FTP használata esetén. |No |true |

>[!NOTE]
>Az FTP-összekötő támogatja az FTP-kiszolgáló titkosítás nélküli vagy explicit SSL/TLS titkosítással való elérését. nem támogatja az implicit SSL/TLS-titkosítást.

### <a name="use-anonymous-authentication"></a>Névtelen hitelesítés használata

```JSON
{
    "name": "FTPLinkedService",
    "properties": {
        "type": "FtpServer",
        "typeProperties": {
            "authenticationType": "Anonymous",
              "host": "myftpserver.com"
        }
    }
}
```

### <a name="use-username-and-password-in-plain-text-for-basic-authentication"></a>A Felhasználónév és a jelszó egyszerű szövegként való használata egyszerű hitelesítéshez

```JSON
{
    "name": "FTPLinkedService",
    "properties": {
    "type": "FtpServer",
        "typeProperties": {
            "host": "myftpserver.com",
            "authenticationType": "Basic",
            "username": "Admin",
            "password": "123456"
        }
    }
}
```

### <a name="use-port-enablessl-enableservercertificatevalidation"></a>Port, enableSsl, enableServerCertificateValidation használata

```JSON
{
    "name": "FTPLinkedService",
    "properties": {
        "type": "FtpServer",
        "typeProperties": {
            "host": "myftpserver.com",
            "authenticationType": "Basic",
            "username": "Admin",
            "password": "123456",
            "port": "21",
            "enableSsl": true,
            "enableServerCertificateValidation": true
        }
    }
}
```

### <a name="use-encryptedcredential-for-authentication-and-gateway"></a>EncryptedCredential használata a hitelesítéshez és az átjáróhoz

```JSON
{
    "name": "FTPLinkedService",
    "properties": {
        "type": "FtpServer",
        "typeProperties": {
            "host": "myftpserver.com",
            "authenticationType": "Basic",
            "encryptedCredential": "xxxxxxxxxxxxxxxxx",
            "gatewayName": "mygateway"
        }
    }
}
```

## <a name="dataset-properties"></a>Adatkészlet tulajdonságai
Az adatkészletek definiálásához rendelkezésre álló csoportok és tulajdonságok teljes listáját lásd: [adatkészletek létrehozása](data-factory-create-datasets.md). Az adatkészletek JSON-típusai, például a struktúra, a rendelkezésre állás és a szabályzat, az összes adatkészlet esetében hasonlóak.

A **typeProperties** szakasz eltérő az egyes adatkészletek esetében. Az adatkészlet típusára jellemző információkat biztosít. A **fájlmegosztás** típusú adatkészlet **typeProperties** szakasza a következő tulajdonságokkal rendelkezik:

| Tulajdonság | Leírás | Kötelező |
| --- | --- | --- |
| folderPath |A mappa alútvonala. A karakterláncban a speciális karaktereknél használja a Escape karaktert. Példákat a következő témakörben talál: példa társított szolgáltatás és adatkészlet-definíciók.<br/><br/>Ezt a tulajdonságot kombinálhatja a **partitionBy** , hogy a mappa elérési útjai a szelet kezdő és záró dátum-és időpontjain alapulnak. |Yes |
| fileName |Adja meg a fájl nevét a **folderPath** , ha azt szeretné, hogy a tábla egy adott fájlra hivatkozzon a mappában. Ha nem ad meg értéket ehhez a tulajdonsághoz, a tábla a mappában található összes fájlra mutat.<br/><br/>Ha a **fájlnév** nincs megadva egy kimeneti adatkészlethez, a generált fájl neve a következő formátumú: <br/><br/>`Data.<Guid>.txt` (Példa: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt) |No |
| fileFilter |Adja meg a **folderPath** található fájlok részhalmazának kiválasztásához használandó szűrőt, nem pedig az összes fájlt.<br/><br/>Az engedélyezett értékek a következők: `*` (több karakter) és `?` (egyetlen karakter).<br/><br/>1. példa: `"fileFilter": "*.log"`<br/>2. példa: `"fileFilter": 2014-1-?.txt"`<br/><br/> a **fileFilter** egy bemeneti fájlmegosztás adatkészlet esetében alkalmazható. Ez a tulajdonság a Hadoop elosztott fájlrendszer (HDFS) esetében nem támogatott. |No |
| partitionedBy |Dinamikus **folderPath** és **fájlnevek** megadására szolgál az idősorozat-adatsorokhoz. Megadhat például egy **folderPath** , amely minden óránként paraméterként van megadva. |No |
| formátumban | A következő típusú formátumok támogatottak: **Szövegformátum**, **JsonFormat**, **AvroFormat**, **OrcFormat**, **ParquetFormat**. A **Type (típus** ) tulajdonságot állítsa a Format értékre a következő értékek egyikére. További információ: [szöveg formátuma](data-factory-supported-file-and-compression-formats.md#text-format), JSON- [Formátum](data-factory-supported-file-and-compression-formats.md#json-format), [Avro formátum](data-factory-supported-file-and-compression-formats.md#avro-format), [ork-formátum](data-factory-supported-file-and-compression-formats.md#orc-format)és [parketta formátuma](data-factory-supported-file-and-compression-formats.md#parquet-format) . <br><br> Ha fájlokat szeretne másolni a fájl alapú tárolók (bináris másolás) között, ugorja át a formátum szakaszt a bemeneti és a kimeneti adatkészlet-definíciókban is. |No |
| tömörítés | Adja meg az adattömörítés típusát és szintjét. A támogatott típusok a **gzip**, a **deflate**, a **BZip2** és a **ZipDeflate**, valamint a támogatott szintek **optimálisak** és **leggyorsabbak**. További információ: [fájl-és Tömörítési formátumok Azure Data Factoryban](data-factory-supported-file-and-compression-formats.md#compression-support). |No |
| useBinaryTransfer |Adja meg, hogy a bináris átviteli módot kívánja-e használni. Az értékek a bináris mód esetében igazak (ez az alapértelmezett érték), és false for ASCII. Ez a tulajdonság csak akkor használható, ha a társított társított szolgáltatás típusa: FTP. |No |

> [!NOTE]
> a **filename** és a **fileFilter** nem használható egyszerre.

### <a name="use-the-partionedby-property"></a>A partionedBy tulajdonság használata
Ahogy az előző szakaszban is említettük, megadhat egy dinamikus **folderPath** és **fájlnevet** az idősorozat-adatsorokhoz a **partitionedBy** tulajdonsággal.

Az idősorozat-adatkészletek, az ütemezés és a szeletek megismeréséhez tekintse meg az [adatkészletek](data-factory-create-datasets.md), az [Ütemezés és a végrehajtás](data-factory-scheduling-and-execution.md)és a [folyamatok létrehozása](data-factory-create-pipelines.md)című témakört.

#### <a name="sample-1"></a>1. példa

```json
"folderPath": "wikidatagateway/wikisampledataout/{Slice}",
"partitionedBy":
[
    { "name": "Slice", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyyMMddHH" } },
],
```
Ebben a példában a {slice} helyére Data Factory rendszerváltozó SliceStart értéke van megadva a megadott formátumban (YYYYMMDDHH). A SliceStart a szelet kezdő időpontját jelöli. A mappa elérési útja különbözik az egyes szeletekhez. (Például tulajdonság beállítása wikidatagateway/wikisampledataout/2014100103 vagy tulajdonság beállítása wikidatagateway/wikisampledataout/2014100104.)

#### <a name="sample-2"></a>2. példa

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
Ebben a példában a SliceStart év, hónap, nap és időpont a **folderPath** és a **fájlnév** tulajdonságai által használt különálló változókra van kibontva.

## <a name="copy-activity-properties"></a>Másolási tevékenység tulajdonságai
A tevékenységek definiálásához elérhető csoportok és tulajdonságok teljes listáját lásd: [folyamatok létrehozása](data-factory-create-pipelines.md). A tulajdonságok, például a név, a leírás, a bemeneti és a kimeneti táblák, valamint a házirendek minden típusú tevékenységhez elérhetők.

A tevékenység **typeProperties** szakaszában elérhető tulajdonságok, másfelől a tevékenység típusától függően változhatnak. A másolási tevékenységhez a típus tulajdonságai a források és a mosdók típusától függően változnak.

A másolási tevékenységben, ha a forrás **FileSystemSource** típusú, a következő tulajdonság érhető el a **typeProperties** szakaszban:

| Tulajdonság | Leírás | Megengedett értékek | Kötelező |
| --- | --- | --- | --- |
| rekurzív |Azt jelzi, hogy az adatok rekurzív módon olvashatók-e az almappákból, vagy csak a megadott mappából. |Igaz, hamis (alapértelmezett) |No |

## <a name="json-example-copy-data-from-ftp-server-to-azure-blob"></a>JSON-példa: adatok másolása az FTP-kiszolgálóról az Azure Blobba
Ez a minta bemutatja, hogyan másolhat adatokat egy FTP-kiszolgálóról az Azure Blob Storage-ba. Az adattárakat azonban a Data Factory másolási tevékenységének használatával közvetlenül átmásolhatja a [támogatott adattárokban és formátumokban](data-factory-data-movement-activities.md#supported-data-stores-and-formats)megadott mosogatóba.

Az alábbi példák olyan JSON-definíciókat biztosítanak, amelyek segítségével a [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md)vagy a [PowerShell](data-factory-copy-activity-tutorial-using-powershell.md)használatával hozhat létre folyamatokat:

* [FTP](#linked-service-properties) típusú társított szolgáltatás
* [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties) típusú társított szolgáltatás
* [Fájlmegosztás](#dataset-properties) típusú bemeneti [adatkészlet](data-factory-create-datasets.md)
* [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties) típusú kimeneti [adatkészlet](data-factory-create-datasets.md)
* [FileSystemSource](#copy-activity-properties) és [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties) használó másolási tevékenységgel rendelkező [folyamat](data-factory-create-pipelines.md)

A minta óránként másolja az adatait egy FTP-kiszolgálóról egy Azure-blobba. Az ezekben a mintákban használt JSON-tulajdonságokat a mintákat követő szakaszokban ismertetjük.

### <a name="ftp-linked-service"></a>FTP-társított szolgáltatás

Ez a példa egyszerű hitelesítést használ a felhasználónévvel és a jelszóval egyszerű szövegként. A következő módszerek egyikét is használhatja:

* Névtelen hitelesítés
* Egyszerű hitelesítés titkosított hitelesítő adatokkal
* FTP SSL/TLS protokollon keresztül (FTPS)

Tekintse meg az FTP-hez [társított szolgáltatás](#linked-service-properties) című szakaszt a használható különböző típusú hitelesítésekhez.

```JSON
{
    "name": "FTPLinkedService",
    "properties": {
        "type": "FtpServer",
        "typeProperties": {
            "host": "myftpserver.com",
            "authenticationType": "Basic",
            "username": "Admin",
            "password": "123456"
        }
    }
}
```
### <a name="azure-storage-linked-service"></a>Azure Storage társított szolgáltatás

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
### <a name="ftp-input-dataset"></a>FTP bemeneti adatkészlet

Ez az adatkészlet az FTP-mappára `mysharedfolder` és-fájlra hivatkozik `test.csv` . A folyamat átmásolja a fájlt a célhelyre.

A **külső** beállítása **igaz** érték esetén a Data Factory szolgáltatás, amely az adatkészleten kívül esik az adat-előállítón, és nem az adat-előállító tevékenysége.

```JSON
{
  "name": "FTPFileInput",
  "properties": {
    "type": "FileShare",
    "linkedServiceName": "FTPLinkedService",
    "typeProperties": {
      "folderPath": "mysharedfolder",
      "fileName": "test.csv",
      "useBinaryTransfer": true
    },
    "external": true,
    "availability": {
      "frequency": "Hour",
      "interval": 1
    }
  }
}
```

### <a name="azure-blob-output-dataset"></a>Azure Blob kimeneti adatkészlet

A rendszer óránként egy új blobba írja az adatbevitelt (frekvencia: óra, intervallum: 1). A rendszer dinamikusan kiértékeli a blob mappájának elérési útját a feldolgozás alatt álló szelet kezdési időpontja alapján. A mappa elérési útja a kezdési időpont év, hónap, nap és óra részét használja.

```JSON
{
    "name": "AzureBlobOutput",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties": {
            "folderPath": "mycontainer/ftp/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
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


### <a name="a-copy-activity-in-a-pipeline-with-file-system-source-and-blob-sink"></a>Másolási tevékenység egy folyamatban a fájlrendszer forrásával és a blob-fogadóval

A folyamat egy másolási tevékenységet tartalmaz, amely a bemeneti és kimeneti adatkészletek használatára van konfigurálva, és óránkénti futásra van ütemezve. A folyamat JSON-definíciójában a **forrás** típusa **FileSystemSource** értékre van állítva, a **fogadó típusa pedig** **BlobSink**.

```JSON
{
    "name": "pipeline",
    "properties": {
        "activities": [{
            "name": "FTPToBlobCopy",
            "inputs": [{
                "name": "FtpFileInput"
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
        "start": "2016-08-24T18:00:00Z",
        "end": "2016-08-24T19:00:00Z"
    }
}
```
> [!NOTE]
> Ha az oszlopokat a forrás adatkészletből a fogadó adatkészletből származó oszlopokra kívánja leképezni, tekintse meg [Azure Data Factory az adatkészlet oszlopainak](data-factory-map-columns.md)

## <a name="next-steps"></a>Következő lépések
Lásd az alábbi cikkeket:

* Az adatáthelyezés (másolási tevékenység) teljesítményét befolyásoló fő tényezőkről Data Factory, valamint az optimalizálásának különböző módjairól a [másolási tevékenység teljesítményének és hangolási útmutatója](data-factory-copy-activity-performance.md)című témakörben olvashat.

* A másolási tevékenységgel rendelkező folyamatok létrehozásával kapcsolatos részletes útmutatásért lásd a [másolási tevékenységről szóló oktatóanyagot](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).
