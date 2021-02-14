---
title: Adatok áthelyezése HTTP-forrásból – Azure
description: Megtudhatja, hogyan helyezhet át egy helyszíni vagy Felhőbeli HTTP-forrás adatait Azure Data Factory használatával.
author: linda33wj
ms.author: jingwang
ms.service: data-factory
ms.topic: conceptual
ms.date: 05/22/2018
robots: noindex
ms.openlocfilehash: ce29b5a112d70575a721b0b527947fd95868da80
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/14/2021
ms.locfileid: "100382922"
---
# <a name="move-data-from-an-http-source-by-using-azure-data-factory"></a>Adatok áthelyezése HTTP-forrásokból Azure Data Factory használatával

> [!div class="op_single_selector" title1="Válassza ki az Ön által használt Data Factory-szolgáltatás verzióját:"]
> * [1-es verzió](data-factory-http-connector.md)
> * [2-es verzió (aktuális verzió)](../connector-http.md)

> [!NOTE]
> Ez a cikk a Data Factory 1-es verziójára vonatkozik. Ha a Azure Data Factory szolgáltatás aktuális verzióját használja, tekintse meg a [http-összekötőt a v2-ben](../connector-http.md).

Ez a cikk azt ismerteti, hogyan használható a másolási tevékenység a Azure Data Factoryban egy helyszíni vagy Felhőbeli HTTP-végpont adatainak egy támogatott fogadó adattárba való áthelyezéséhez. Ez a cikk a [másolási tevékenységgel helyezi át az adatáthelyezést](data-factory-data-movement-activities.md), amely általános áttekintést nyújt az adatáthelyezésről a másolási tevékenység használatával. A cikk a másolási tevékenység által támogatott adattárakat is felsorolja a forrásként és a mosdóként.

Data Factory jelenleg csak az adatok HTTP-forrásból más adattárakba való áthelyezését támogatja. Nem támogatja az adatok más adattárakból HTTP-célhelyre való áthelyezését.

## <a name="supported-scenarios-and-authentication-types"></a>Támogatott forgatókönyvek és hitelesítési típusok

Ezt a HTTP-összekötőt használhatja a *felhőből és egy helyszíni http/S-végpontból* származó adatok lekérdezésére a http **Get** vagy **post** metódusok használatával. A következő hitelesítési típusok támogatottak: **Névtelen**, **alapszintű**, **kivonatoló**, **Windows** és **ClientCertificate**. Figyelje meg az összekötő és a [webes tábla összekötő](data-factory-web-table-connector.md)közötti különbséget. A webes tábla összekötő kibontja a táblázat tartalmát egy HTML-weboldalról.

Amikor helyszíni HTTP-végpontból másol Adatmásolást, adatkezelés átjárót kell telepítenie a helyszíni környezetben vagy egy Azure-beli virtuális gépen. Ha többet szeretne megtudni a adatkezelés átjáróról, valamint az átjáró beállításával kapcsolatos részletes utasításokért lásd: az [adatáthelyezés a helyszíni helyszínek és a felhő között](data-factory-move-data-between-onprem-and-cloud.md).

## <a name="get-started"></a>Bevezetés

Létrehozhat egy másolási tevékenységgel rendelkező folyamatot, amely különböző eszközök vagy API-k használatával helyezi át az adatok HTTP-forrásból való áthelyezését:

- A folyamat létrehozásának legegyszerűbb módja a Adatok másolása varázsló használata. A folyamat Adatok másolása varázslóval történő létrehozásával kapcsolatos gyors útmutatóért lásd [: oktatóanyag: folyamat létrehozása a másolás varázsló használatával](data-factory-copy-data-wizard-tutorial.md).

- A következő eszközöket is használhatja egy folyamat létrehozásához: a **Visual Studio**, **Azure PowerShell**, **Azure Resource Manager sablon**, a **.NET API** vagy a **REST API**. A másolási tevékenységgel rendelkező folyamat létrehozásával kapcsolatos részletes útmutatásért lásd a [másolási tevékenység oktatóanyagát](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md). A HTTP-forrásokból az Azure Blob Storage-ba adatmásoló JSON-minták esetében tekintse meg a [JSON-példákat](#json-examples).

## <a name="linked-service-properties"></a>Társított szolgáltatás tulajdonságai

A következő táblázat a HTTP-társított szolgáltatáshoz tartozó JSON-elemeket ismerteti:

| Tulajdonság | Leírás | Kötelező |
| --- | --- | --- |
| típus | A **Type** tulajdonságot **http**-értékre kell beállítani. | Yes |
| url | A webkiszolgáló alap URL-címe. | Yes |
| authenticationType | Megadja a hitelesítési típust. Az engedélyezett értékek: **Névtelen**, **alapszintű**, **kivonatoló**, **Windows** és **ClientCertificate**. <br><br> A jelen cikk későbbi szakaszaiban további tulajdonságokat és JSON-mintákat találhat a következő hitelesítési típusokhoz. | Yes |
| enableServerCertificateValidation | Megadja, hogy engedélyezi-e a kiszolgáló TLS/SSL-tanúsítványának érvényesítését, ha a forrás egy HTTPS-webkiszolgáló. Ha a HTTPS-kiszolgáló önaláírt tanúsítványt használ, ezt állítsa **hamis** értékre. | No<br /> (az alapértelmezett érték **igaz**) |
| Átjáró neve | A helyszíni HTTP-forráshoz való kapcsolódáshoz használt adatkezelés átjáró-példány neve. | Igen, ha helyszíni HTTP-forrásról másol adatokból |
| encryptedCredential | A HTTP-végpont eléréséhez használt titkosított hitelesítő adat. Az érték automatikusan létrejön, amikor a hitelesítési adatokat a másolás varázslóban vagy a **ClickOnce** párbeszédpanelen konfigurálja. | No<br /> (csak akkor érvényes, ha egy helyszíni HTTP-kiszolgálóról másol adatokból) |

A helyszíni HTTP-összekötő adatforrásához tartozó hitelesítő adatok beállításával kapcsolatos részletekért lásd: az [adatok áthelyezése a helyszíni források és a felhő között adatkezelés átjáró használatával](data-factory-move-data-between-onprem-and-cloud.md).

### <a name="using-basic-digest-or-windows-authentication"></a>Alapszintű, kivonatoló vagy Windows-hitelesítés használata

**AuthenticationType** beállítása **alapszintű**, **kivonatoló** vagy **Windows rendszerre**. Az előző szakaszokban leírt általános HTTP-összekötő tulajdonságain kívül állítsa be a következő tulajdonságokat:

| Tulajdonság | Leírás | Kötelező |
| --- | --- | --- |
| userName (Felhasználónév) | A HTTP-végpont eléréséhez használandó Felhasználónév. | Yes |
| jelszó | A felhasználó jelszava (**username**). | Yes |

**Példa: alapszintű, kivonatoló vagy Windows-hitelesítés használata**

```json
{
    "name": "HttpLinkedService",
    "properties":
    {
        "type": "Http",
        "typeProperties":
        {
            "authenticationType": "basic",
            "url" : "https://en.wikipedia.org/wiki/",
            "userName": "user name",
            "password": "password"
        }
    }
}
```

### <a name="using-clientcertificate-authentication"></a>ClientCertificate-hitelesítés használata

Az alapszintű hitelesítés használatához állítsa a **AuthenticationType** **ClientCertificate** értékre. Az előző szakaszokban leírt általános HTTP-összekötő tulajdonságain kívül állítsa be a következő tulajdonságokat:

| Tulajdonság | Leírás | Kötelező |
| --- | --- | --- |
| embeddedCertData | A PFX-fájl bináris fájljainak Base64 kódolású tartalma. | **EmbeddedCertData** vagy **certThumbprint** meghatározása |
| certThumbprint | Az átjárót tároló számítógép tanúsítványtárolójában telepített Tanúsítvány ujjlenyomata. Csak akkor érvényes, ha egy helyszíni HTTP-forrásból másol be egy Adatmásolást. | **EmbeddedCertData** vagy **certThumbprint** meghatározása |
| jelszó | A tanúsítványhoz társított jelszó. | No |

Ha **certThumbprint** használ a hitelesítéshez, és a tanúsítvány a helyi számítógép személyes tárolójába van telepítve, adjon olvasási jogosultságot az átjáró szolgáltatásnak:

1. Nyissa meg a Microsoft Management Console (MMC) programot. Adja hozzá a **helyi számítógépet** tároló **tanúsítványok** beépülő modult.
2. Bontsa ki a **tanúsítványok**  >  **személyes** csomópontot, majd válassza a **tanúsítványok** lehetőséget.
3. Kattintson a jobb gombbal a tanúsítványra a személyes tárolóban, majd válassza a **minden feladat**  > **titkos kulcsok kezelése** lehetőséget.
3. A **Biztonság** lapon adja hozzá azt a felhasználói fiókot, amely alatt az adatkezelés átjáró-gazda szolgáltatás fut, és olvasási hozzáféréssel rendelkezik a tanúsítványhoz.  

**Példa: ügyféltanúsítvány használata**

Ez a társított szolgáltatás az adatgyárat egy helyszíni HTTP-webkiszolgálóra kapcsolja. Egy olyan ügyféltanúsítványt használ, amely telepítve van a adatkezelés átjáróval rendelkező gépre.

```json
{
    "name": "HttpLinkedService",
    "properties":
    {
        "type": "Http",
        "typeProperties":
        {
            "authenticationType": "ClientCertificate",
            "url": "https://en.wikipedia.org/wiki/",
            "certThumbprint": "thumbprint of certificate",
            "gatewayName": "gateway name"
        }
    }
}
```

**Példa: ügyféltanúsítvány használata egy fájlban**

Ez a társított szolgáltatás az adatgyárat egy helyszíni HTTP-webkiszolgálóra kapcsolja. Egy ügyféltanúsítvány-fájlt használ azon a gépen, amelyen telepítve van adatkezelés átjáró.

```json
{
    "name": "HttpLinkedService",
    "properties":
    {
        "type": "Http",
        "typeProperties":
        {
            "authenticationType": "ClientCertificate",
            "url": "https://en.wikipedia.org/wiki/",
            "embeddedCertData": "Base64-encoded cert data",
            "password": "password of cert"
        }
    }
}
```

## <a name="dataset-properties"></a>Adatkészlet tulajdonságai

Az adatkészlet JSON-fájljának egyes részei, például a struktúra, a rendelkezésre állás és a házirend, hasonlóak az összes adatkészlet-típushoz (Azure SQL Database, Azure Blob Storage, Azure Table Storage).

Az adatkészletek definiálásához elérhető csoportok és tulajdonságok teljes listáját lásd: [adatkészletek létrehozása](data-factory-create-datasets.md).

A **typeProperties** szakasz eltérő az egyes adatkészletek esetében. A **typeProperties** szakasz információt nyújt az adattárban található adatok helyéről. A **http** -típus adatkészletének **typeProperties** szakasza a következő tulajdonságokkal rendelkezik:

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| típus | Az adatkészlet **típusát** **http**-értékre kell beállítani. | Yes |
| relativeUrl | Az adatforrást tartalmazó erőforrás relatív URL-címe. Ha nincs megadva az elérési út, a rendszer csak a társított szolgáltatás definíciójában megadott URL-címet használja. <br><br> Dinamikus URL-cím létrehozásához [Data Factory függvényeket és rendszerváltozókat](data-factory-functions-variables.md)használhat. Példa: **relativeUrl**: **$ $Text. Format ("/My/Report? month = {0: ÉÉÉÉ}-{0: PP} &FMT Package = CSV", SliceStart)**. | No |
| requestMethod | A HTTP-metódus. Az engedélyezett értékek a **Get** és a **post**. | No <br />(az alapértelmezett érték a **Get**) |
| additionalHeaders | További HTTP-kérelmek fejlécei. | No |
| requestBody | A HTTP-kérelem törzse. | No |
| formátumban | Ha *egy http-végpont adatait* az elemzés nélkül szeretné lekérni, ugorja át a **formázási** beállítást. <br><br> Ha a HTTP-válasz tartalmát a másolás során szeretné elemezni, a következő típusú formátumok támogatottak: **Szövegformátum**, **JsonFormat**, **AvroFormat**, **OrcFormat** és **ParquetFormat**. További információkért lásd: [Szövegformátum](data-factory-supported-file-and-compression-formats.md#text-format), [JSON formátum](data-factory-supported-file-and-compression-formats.md#json-format), [Avro formátum](data-factory-supported-file-and-compression-formats.md#avro-format), [ork formátum](data-factory-supported-file-and-compression-formats.md#orc-format)és [parketta formátum](data-factory-supported-file-and-compression-formats.md#parquet-format). |No |
| tömörítés | Adja meg az adattömörítés típusát és szintjét. Támogatott típusok: **gzip**, **deflate**, **BZip2** és **ZipDeflate**. Támogatott szintek: **optimális** és **leggyorsabb**. További információ: [fájl-és Tömörítési formátumok Azure Data Factoryban](data-factory-supported-file-and-compression-formats.md#compression-support). |No |

**Példa: a GET (alapértelmezett) metódus használata**

```json
{
  "name": "HttpSourceDataInput",
    "properties": {
    "type": "Http",
        "linkedServiceName": "HttpLinkedService",
        "typeProperties": {
          "relativeUrl": "XXX/test.xml",
          "additionalHeaders": "Connection: keep-alive\nUser-Agent: Mozilla/5.0\n"
        },
        "external": true,
        "availability": {
            "frequency": "Hour",
            "interval":  1
        }
    }
}
```

**Példa: a POST metódus használata**

```json
{
    "name": "HttpSourceDataInput",
    "properties": {
        "type": "Http",
        "linkedServiceName": "HttpLinkedService",
        "typeProperties": {
            "relativeUrl": "/XXX/test.xml",
       "requestMethod": "Post",
            "requestBody": "body for POST HTTP request"
        },
        "external": true,
        "availability": {
            "frequency": "Hour",
            "interval":  1
        }
    }
}
```

## <a name="copy-activity-properties"></a>Másolási tevékenység tulajdonságai

A tulajdonságok, például a név, a leírás, a bemeneti és a kimeneti táblák, valamint a szabályzatok minden típusú tevékenységhez elérhetők.

A tevékenységek definiálásához elérhető csoportok és tulajdonságok teljes listáját lásd: [folyamatok létrehozása](data-factory-create-pipelines.md). 

A tevékenység **typeProperties** szakaszában elérhető tulajdonságok az egyes tevékenységtípusok esetében eltérőek. Másolási tevékenység esetén a tulajdonságok a források típusától és a mosdótól függően változnak.

Jelenleg, ha a másolási tevékenység forrása a **HttpSource** típusú, a következő tulajdonságok támogatottak:

| Tulajdonság | Leírás | Kötelező |
| -------- | ----------- | -------- |
| httpRequestTimeout | A válasz kéréséhez szükséges HTTP-kérelem időkorlátja (a **TimeSpan** érték). A válasz lekérésének időtúllépése, nem pedig a válaszüzenetek olvasásának időtúllépése. | No<br />(alapértelmezett érték: **00:01:40**) |

## <a name="supported-file-and-compression-formats"></a>Támogatott fájl- és tömörítési formátumok

További információ: [fájl-és Tömörítési formátumok Azure Data Factory](data-factory-supported-file-and-compression-formats.md) .

## <a name="json-examples"></a>JSON-példák

Az alábbi példák olyan JSON-definíciókat biztosítanak, amelyek segítségével a [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) vagy a [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md)használatával hozhat létre folyamatokat. A példák azt mutatják be, hogyan másolhatók az adatok HTTP-forrásokból az Azure Blob Storage-ba. Az adatok azonban *közvetlenül* a forrásokból bármelyik olyan mosogatóba másolhatók, amelyet a Azure Data Factory másolási tevékenységgel [támogat](data-factory-data-movement-activities.md#supported-data-stores-and-formats) .

**Példa: adatok másolása HTTP-forrásból az Azure Blob Storage-ba**

A példához tartozó Data Factory megoldás a következő Data Factory entitásokat tartalmazza:

*   [Http](#linked-service-properties)típusú társított szolgáltatás.
*   [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties)típusú társított szolgáltatás.
*   [Http](#dataset-properties)típusú bemeneti [adatkészlet](data-factory-create-datasets.md) .
*   [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties)típusú kimeneti [adatkészlet](data-factory-create-datasets.md) .
*   Olyan [folyamat](data-factory-create-pipelines.md) , amely [HttpSource](#copy-activity-properties) és [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties)használó másolási tevékenységgel rendelkezik.

A minta óránként másolja át az adatait egy HTTP-forrásból egy Azure-blobba. Az ezekben a mintákban használt JSON-tulajdonságokat a mintákat követő szakaszokban ismertetjük.

### <a name="http-linked-service"></a>HTTP-társított szolgáltatás

Ez a példa a HTTP-alapú társított szolgáltatást használja névtelen hitelesítéssel. Lásd: [http-társított szolgáltatás](#linked-service-properties) a használható különböző típusú hitelesítésekhez.

```json
{
    "name": "HttpLinkedService",
    "properties":
    {
        "type": "Http",
        "typeProperties":
        {
            "authenticationType": "Anonymous",
            "url" : "https://en.wikipedia.org/wiki/"
        }
    }
}
```

### <a name="azure-storage-linked-service"></a>Azure Storage társított szolgáltatás

```json
{
  "name": "AzureStorageLinkedService",
  "properties": {
    "type": "AzureStorage",
    "typeProperties": {
      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<account key>"
    }
  }
}
```

### <a name="http-input-dataset"></a>HTTP bemeneti adatkészlet

A **külső** beállítása **igaz** érték esetén a Data Factory szolgáltatás, amely az adatkészleten kívül esik az adat-előállítón, és nem az adat-előállító tevékenysége.

```json
{
  "name": "HttpSourceDataInput",
    "properties": {
    "type": "Http",
        "linkedServiceName": "HttpLinkedService",
        "typeProperties": {
            "relativeUrl": "$$Text.Format('/my/report?month={0:yyyy}-{0:MM}&fmt=csv', SliceStart)",
        "additionalHeaders": "Connection: keep-alive\nUser-Agent: Mozilla/5.0\n"
    },
        "external": true,
        "availability": {
            "frequency": "Hour",
            "interval":  1
        }
    }
}

```

### <a name="azure-blob-output-dataset"></a>Azure blobkimeneti adatkészlet

A rendszer óránként egy új blobba írja az adatbevitelt (**frekvencia**: **óra**, **intervallum**: **1**).

```json
{
    "name": "AzureBlobOutput",
    "properties":
    {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties":
        {
            "folderPath": "adfgetstarted/Movies"
        },
        "availability":
        {
            "frequency": "Hour",
            "interval": 1
        }
    }
}
```

### <a name="pipeline-that-uses-a-copy-activity"></a>Másolási tevékenységet használó folyamat

A folyamat egy másolási tevékenységet tartalmaz, amely a bemeneti és a kimeneti adatkészletek használatára van konfigurálva. A másolási tevékenység óránkénti futásra van ütemezve. A folyamat JSON-definíciójában a **forrás** típusa **HttpSource** értékre van állítva, a **fogadó típusa pedig** **BlobSink** értékre van állítva.

A **HttpSource** által támogatott tulajdonságok listájáért lásd: [HttpSource](#copy-activity-properties).

```json
{  
    "name":"SamplePipeline",
    "properties":{  
    "start":"2014-06-01T18:00:00",
    "end":"2014-06-01T19:00:00",
    "description":"pipeline with a copy activity",
    "activities":[  
      {
        "name": "HttpSourceToAzureBlob",
        "description": "Copy from an HTTP source to an Azure blob",
        "type": "Copy",
        "inputs": [
          {
            "name": "HttpSourceDataInput"
          }
        ],
        "outputs": [
          {
            "name": "AzureBlobOutput"
          }
        ],
        "typeProperties": {
          "source": {
            "type": "HttpSource"
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
          "executionPriorityOrder": "OldestFirst",
          "retry": 0,
          "timeout": "01:00:00"
        }
      }
      ]
   }
}
```

> [!NOTE]
> Ha egy forrás adatkészletből származó oszlopokat kíván leképezni egy fogadó adatkészletből származó oszlopokra, tekintse meg [az adatkészlet oszlopainak leképezése Azure Data Factory](data-factory-map-columns.md).

## <a name="performance-and-tuning"></a>Teljesítmény és finomhangolás

Az adatáthelyezés (másolási tevékenység) teljesítményét befolyásoló főbb tényezőkről Azure Data Factory és az optimalizálás különböző módjairól a [másolási tevékenységek teljesítményének és hangolásának útmutatója](data-factory-copy-activity-performance.md)tartalmaz további információt.
