---
title: Adatok másolása HTTP-forrásokból Azure Data Factory használatával
description: Megtudhatja, hogyan másolhat adatok egy felhőből vagy helyszíni HTTP-forrásról a fogadó adattárakba egy Azure Data Factory folyamat másolási tevékenységének használatával.
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.date: 12/10/2019
ms.author: jingwang
ms.openlocfilehash: 0462dac12d41fff667212902152b420d1460186d
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/14/2021
ms.locfileid: "100383636"
---
# <a name="copy-data-from-an-http-endpoint-by-using-azure-data-factory"></a>Adatok másolása HTTP-végpontból Azure Data Factory használatával

> [!div class="op_single_selector" title1="Válassza ki az Ön által használt Data Factory-szolgáltatás verzióját:"]
> * [1-es verzió](v1/data-factory-http-connector.md)
> * [Aktuális verzió](connector-http.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Ez a cikk azt ismerteti, hogyan használható a másolási tevékenység a Azure Data Factoryban az adatok HTTP-végpontról történő másolásához. A cikk a [másolási tevékenységre épül Azure Data Factoryban](copy-activity-overview.md), amely a másolási tevékenység általános áttekintését mutatja be.

A HTTP-összekötő, a REST- [összekötő](connector-rest.md) és a [webes tábla összekötője](connector-web-table.md) közötti különbség a következő:

- A **Rest-összekötő** kifejezetten támogatja a REST API-k adatainak másolását; 
- A **http-összekötő** általános az adatok bármely http-végpontból való lekéréséhez, például a fájl letöltéséhez. A REST-összekötő elérhetővé válása előtt előfordulhat, hogy a HTTP-összekötőt használja az adatok REST API-ból való másolásához, amely támogatott, de kevésbé működik a REST-összekötőhöz képest.
- A **web Table Connector** kibontja a táblázat tartalmát egy HTML-weboldalról.

## <a name="supported-capabilities"></a>Támogatott képességek

Ez a HTTP-összekötő a következő tevékenységek esetén támogatott:

- [Másolási tevékenység](copy-activity-overview.md) [támogatott forrás/fogadó mátrixtal](copy-activity-overview.md)
- [Keresési tevékenység](control-flow-lookup-activity.md)

A HTTP-forrásokból származó adatok bármely támogatott fogadó adattárba másolhatók. A másolási tevékenység által a forrásként és a fogadóként támogatott adattárak listájáért lásd: [támogatott adattárak és-formátumok](copy-activity-overview.md#supported-data-stores-and-formats).

Ezt a HTTP-összekötőt a következő célra használhatja:

- Adatok lekérése HTTP/S-végpontról a HTTP **Get** vagy **post** metódusok használatával.
- Adatok beolvasása a következő hitelesítések egyikével: **Névtelen**, **alapszintű**, **kivonatoló**, **Windows** vagy **ClientCertificate**.
- Másolja a HTTP-választ a-ként, vagy elemezze a [támogatott fájlformátumok és tömörítési kodekek](supported-file-formats-and-compression-codecs.md)használatával.

> [!TIP]
> Ha a HTTP-összekötő Data Factoryban való konfigurálása előtt szeretne tesztelni egy HTTP-kérelmet az adatlekérdezéshez, ismerkedjen meg a fejléc és a törzs követelményeivel kapcsolatos API-specifikációval. Az érvényesítéshez használhatja a Poster vagy a webböngésző eszközeit.

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

## <a name="get-started"></a>Bevezetés

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

A következő szakaszokban részletesen ismertetjük azokat a tulajdonságokat, amelyekkel meghatározhatja Data Factory a HTTP-összekötőre jellemző entitásokat.

## <a name="linked-service-properties"></a>Társított szolgáltatás tulajdonságai

A HTTP társított szolgáltatás a következő tulajdonságokat támogatja:

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| típus | A **Type** tulajdonságot **HttpServer** értékre kell beállítani. | Yes |
| url | A webkiszolgáló alap URL-címe. | Yes |
| enableServerCertificateValidation | Itt adhatja meg, hogy engedélyezi-e a kiszolgáló TLS/SSL-tanúsítványának érvényesítését egy HTTP-végponthoz való csatlakozáskor. Ha a HTTPS-kiszolgáló önaláírt tanúsítványt használ, állítsa **hamis** értékre a tulajdonságot. | No<br /> (az alapértelmezett érték **igaz**) |
| authenticationType | Megadja a hitelesítési típust. Az engedélyezett értékek: **Névtelen**, **alapszintű**, **kivonatoló**, **Windows** és **ClientCertificate**. <br><br> A következő szakaszokban találhat további tulajdonságokat és JSON-mintákat a fenti hitelesítési típusokhoz. | Yes |
| Connectvia tulajdonsággal | Az adattárhoz való kapcsolódáshoz használt [Integration Runtime](concepts-integration-runtime.md) . További tudnivalók az [Előfeltételek](#prerequisites) szakaszban olvashatók. Ha nincs megadva, a rendszer az alapértelmezett Azure Integration Runtime használja. |No |

### <a name="using-basic-digest-or-windows-authentication"></a>Alapszintű, kivonatoló vagy Windows-hitelesítés használata

Állítsa a **authenticationType** tulajdonságot **alapszintű**, **kivonatoló** vagy **Windows** értékre. Az előző szakaszban leírt általános tulajdonságok mellett a következő tulajdonságokat is meg kell adni:

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| userName (Felhasználónév) | A HTTP-végpont eléréséhez használandó Felhasználónév. | Yes |
| jelszó | A felhasználó jelszava (a **Felhasználónév** értéke). A mező megjelölése **SecureString** -típusként, hogy biztonságosan tárolja azt Data Factoryban. [Hivatkozhat a Azure Key Vaultban tárolt titkos kulcsra](store-credentials-in-key-vault.md)is. | Yes |

**Példa**

```json
{
    "name": "HttpLinkedService",
    "properties": {
        "type": "HttpServer",
        "typeProperties": {
            "authenticationType": "Basic",
            "url" : "<HTTP endpoint>",
            "userName": "<user name>",
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

### <a name="using-clientcertificate-authentication"></a>ClientCertificate-hitelesítés használata

A ClientCertificate-hitelesítés használatához állítsa a **authenticationType** tulajdonságot **ClientCertificate** értékre. Az előző szakaszban leírt általános tulajdonságok mellett a következő tulajdonságokat is meg kell adni:

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| embeddedCertData | Base64 kódolású tanúsítvány-adattartalom. | Adjon meg **embeddedCertData** vagy **certThumbprint**. |
| certThumbprint | A saját üzemeltetésű Integration Runtime gép tanúsítványtárolójában telepített Tanúsítvány ujjlenyomata. Csak akkor érvényes, ha a Integration Runtime saját üzemeltetésű típusa van megadva a **connectvia tulajdonsággal** tulajdonságban. | Adjon meg **embeddedCertData** vagy **certThumbprint**. |
| jelszó | A tanúsítványhoz társított jelszó. A mező megjelölése **SecureString** -típusként, hogy biztonságosan tárolja azt Data Factoryban. [Hivatkozhat a Azure Key Vaultban tárolt titkos kulcsra](store-credentials-in-key-vault.md)is. | No |

Ha **certThumbprint** használ a hitelesítéshez, és a tanúsítvány a helyi számítógép személyes tárolójába van telepítve, adja meg az olvasási engedélyeket a saját üzemeltetésű Integration Runtime számára:

1. Nyissa meg a Microsoft Management Console (MMC) programot. Adja hozzá a **helyi számítógépet** tároló **tanúsítványok** beépülő modult.
2. Bontsa ki a **tanúsítványok**  >  **személyes** csomópontot, majd válassza a **tanúsítványok** lehetőséget.
3. Kattintson a jobb gombbal a tanúsítványra a személyes tárolóban, majd válassza a **minden feladat**  >  **titkos kulcsok kezelése** lehetőséget.
3. A **Biztonság** lapon adja hozzá azt a felhasználói fiókot, amelyben a Integration Runtime gazda szolgáltatás (DIAHostService) fut, és olvasási hozzáféréssel rendelkezik a tanúsítványhoz.

**1. példa: a certThumbprint használata**

```json
{
    "name": "HttpLinkedService",
    "properties": {
        "type": "HttpServer",
        "typeProperties": {
            "authenticationType": "ClientCertificate",
            "url": "<HTTP endpoint>",
            "certThumbprint": "<thumbprint of certificate>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**2. példa: a embeddedCertData használata**

```json
{
    "name": "HttpLinkedService",
    "properties": {
        "type": "HttpServer",
        "typeProperties": {
            "authenticationType": "ClientCertificate",
            "url": "<HTTP endpoint>",
            "embeddedCertData": "<Base64-encoded cert data>",
            "password": {
                "type": "SecureString",
                "value": "password of cert"
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

A HTTP a következő tulajdonságokat támogatja a `location` Formátum-alapú adatkészlet beállításai között:

| Tulajdonság    | Leírás                                                  | Kötelező |
| ----------- | ------------------------------------------------------------ | -------- |
| típus        | Az `location` adatkészletben található Type tulajdonságot **HttpServerLocation** értékre kell állítani. | Yes      |
| relativeUrl | Az adatforrást tartalmazó erőforrás relatív URL-címe. A HTTP-összekötő a következő összevont URL-címről másolja az adatait: `[URL specified in linked service][relative URL specified in dataset]` .   | No       |

> [!NOTE]
> A HTTP-kérések támogatott mérete körülbelül 500 KB. Ha a webes végpontnak átadni kívánt hasznos adatok mérete meghaladja a 500 KB-ot, érdemes lehet kisebb adattömbökbe felvenni a hasznos adatokat.

**Példa**

```json
{
    "name": "DelimitedTextDataset",
    "properties": {
        "type": "DelimitedText",
        "linkedServiceName": {
            "referenceName": "<HTTP linked service name>",
            "type": "LinkedServiceReference"
        },
        "schema": [ < physical schema, optional, auto retrieved during authoring > ],
        "typeProperties": {
            "location": {
                "type": "HttpServerLocation",
                "relativeUrl": "<relative url>"
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

Ez a szakasz a HTTP-forrás által támogatott tulajdonságok listáját tartalmazza.

A tevékenységek definiálásához elérhető csoportok és tulajdonságok teljes listáját lásd: [folyamatok](concepts-pipelines-activities.md). 

### <a name="http-as-source"></a>HTTP forrásként

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

A HTTP a következő tulajdonságokat támogatja a `storeSettings` Format-alapú másolási forrás beállításaiban:

| Tulajdonság                 | Leírás                                                  | Kötelező |
| ------------------------ | ------------------------------------------------------------ | -------- |
| típus                     | A Type tulajdonságot a `storeSettings` **HttpReadSettings** értékre kell állítani. | Yes      |
| requestMethod            | A HTTP-metódus. <br>Az engedélyezett értékek: **Get** (alapértelmezett) és **post**. | No       |
| addtionalHeaders         | További HTTP-kérelmek fejlécei.                             | No       |
| requestBody              | A HTTP-kérelem törzse.                               | No       |
| httpRequestTimeout           | A válasz kéréséhez szükséges HTTP-kérelem időkorlátja (a **TimeSpan** érték). Ez az érték a válasz lekérésének időtúllépése, nem pedig a válaszüzenetek olvasásának időtúllépése. Az alapértelmezett érték a **00:01:40**. | No       |
| maxConcurrentConnections | A tárolási tárolóhoz való kapcsolódáshoz szükséges kapcsolatok száma egyidejűleg. Csak akkor kell megadni, ha az egyidejű kapcsolódást szeretné korlátozni az adattárral. | No       |

**Példa**

```json
"activities":[
    {
        "name": "CopyFromHTTP",
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
                    "type": "HttpReadSettings",
                    "requestMethod": "Post",
                    "additionalHeaders": "<header key: header value>\n<header key: header value>\n",
                    "requestBody": "<body for POST HTTP request>"
                }
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="lookup-activity-properties"></a>Keresési tevékenység tulajdonságai

A tulajdonságok részleteinek megismeréséhez tekintse meg a [keresési tevékenységet](control-flow-lookup-activity.md).

## <a name="legacy-models"></a>Örökölt modellek

>[!NOTE]
>A következő modellek továbbra is támogatottak a visszamenőleges kompatibilitás érdekében. Azt javasoljuk, hogy használja a fenti szakaszban említett új modellt, és az ADF authoring felhasználói felülete átvált az új modell generálására.

### <a name="legacy-dataset-model"></a>Örökölt adatkészlet-modell

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| típus | Az adatkészlet **Type** tulajdonságát **HttpFile** értékre kell állítani. | Yes |
| relativeUrl | Az adatforrást tartalmazó erőforrás relatív URL-címe. Ha nincs megadva ez a tulajdonság, a rendszer csak a társított szolgáltatás definíciójában megadott URL-címet használja. | No |
| requestMethod | A HTTP-metódus. Az engedélyezett értékek: **Get** (alapértelmezett) és **post**. | No |
| additionalHeaders | További HTTP-kérelmek fejlécei. | No |
| requestBody | A HTTP-kérelem törzse. | No |
| formátumban | Ha az adatokat a HTTP-végpontról kívánja lekérdezni, az elemzés nélkül, majd másolja az adatokat egy fájl alapú tárolóba, ugorja át a **Formátum** szakaszt a bemeneti és a kimeneti adatkészlet-definíciókban is.<br/><br/>Ha a HTTP-válasz tartalmát a másolás során szeretné elemezni, a következő fájlformátum-típusok támogatottak: **Szövegformátum**, **JsonFormat**, **AvroFormat**, **OrcFormat** és **ParquetFormat**. A **Formátum** alatt állítsa be a **Type** tulajdonságot az alábbi értékek egyikére. További információ: JSON- [Formátum](supported-file-formats-and-compression-codecs-legacy.md#json-format), [szöveges formátum](supported-file-formats-and-compression-codecs-legacy.md#text-format), [Avro formátum](supported-file-formats-and-compression-codecs-legacy.md#avro-format), ork- [Formátum](supported-file-formats-and-compression-codecs-legacy.md#orc-format)és [parketta formátum](supported-file-formats-and-compression-codecs-legacy.md#parquet-format). |No |
| tömörítés | Adja meg az adattömörítés típusát és szintjét. További információ: [támogatott fájlformátumok és tömörítési kodekek](supported-file-formats-and-compression-codecs-legacy.md#compression-support).<br/><br/>Támogatott típusok: **gzip**, **deflate**, **BZip2** és **ZipDeflate**.<br/>Támogatott szintek:  **optimális** és **leggyorsabb**. |No |

> [!NOTE]
> A HTTP-kérések támogatott mérete körülbelül 500 KB. Ha a webes végpontnak átadni kívánt hasznos adatok mérete meghaladja a 500 KB-ot, érdemes lehet kisebb adattömbökbe felvenni a hasznos adatokat.

**1. példa: a Get metódus használata (alapértelmezett)**

```json
{
    "name": "HttpSourceDataInput",
    "properties": {
        "type": "HttpFile",
        "linkedServiceName": {
            "referenceName": "<HTTP linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "relativeUrl": "<relative url>",
            "additionalHeaders": "Connection: keep-alive\nUser-Agent: Mozilla/5.0\n"
        }
    }
}
```

**2. példa: a post metódus használata**

```json
{
    "name": "HttpSourceDataInput",
    "properties": {
        "type": "HttpFile",
        "linkedServiceName": {
            "referenceName": "<HTTP linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "relativeUrl": "<relative url>",
            "requestMethod": "Post",
            "requestBody": "<body for POST HTTP request>"
        }
    }
}
```

### <a name="legacy-copy-activity-source-model"></a>Örökölt másolási tevékenység forrásának modellje

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| típus | A másolási tevékenység forrásának **Type** tulajdonságát **HttpSource** értékre kell állítani. | Yes |
| httpRequestTimeout | A válasz kéréséhez szükséges HTTP-kérelem időkorlátja (a **TimeSpan** érték). Ez az érték a válasz lekérésének időtúllépése, nem pedig a válaszüzenetek olvasásának időtúllépése. Az alapértelmezett érték a **00:01:40**.  | No |

**Példa**

```json
"activities":[
    {
        "name": "CopyFromHTTP",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<HTTP input dataset name>",
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
                "type": "HttpSource",
                "httpRequestTimeout": "00:01:00"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="next-steps"></a>Következő lépések

A másolási tevékenység által támogatott adattárak listáját a Azure Data Factoryban található forrásként és nyelőként tekintse meg a [támogatott adattárak és-formátumok](copy-activity-overview.md#supported-data-stores-and-formats)című témakörben.
