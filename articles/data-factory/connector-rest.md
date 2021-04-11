---
title: Adatok másolása REST-végpontra a és a rendszerből a Azure Data Factory használatával
description: Megtudhatja, hogyan másolhat adatok egy felhőből vagy helyszíni REST-forrásból a fogadó adattárakba vagy a támogatott forrás-adattárból egy REST-tárolóba egy Azure Data Factory folyamat másolási tevékenységének használatával.
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.date: 03/16/2021
ms.author: jingwang
ms.openlocfilehash: 779a8745688e6a1fb8a15bc9119c6fbc1803ca2c
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/31/2021
ms.locfileid: "106078927"
---
# <a name="copy-data-from-and-to-a-rest-endpoint-by-using-azure-data-factory"></a>Adatok másolása REST-végpontra a és a rendszerből a Azure Data Factory használatával
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Ez a cikk azt ismerteti, hogyan használható a másolási tevékenység a Azure Data Factoryban az adatok és a REST-végpontok közötti másoláshoz. A cikk a [másolási tevékenységre épül Azure Data Factoryban](copy-activity-overview.md), amely a másolási tevékenység általános áttekintését mutatja be.

A REST-összekötő, a [http-összekötő](connector-http.md)és a [webtábla-összekötő](connector-web-table.md) közötti különbség a következő:

- A **Rest-összekötő** kifejezetten támogatja a REST API-k adatainak másolását; 
- A **http-összekötő** általános az adatok bármely http-végpontból való lekéréséhez, például a fájl letöltéséhez. A REST-összekötő előtt előfordulhat, hogy a HTTP-összekötő használatával másolhatja az adatok REST API-ból való másolását, amely támogatott, de kevésbé működik a REST-összekötőhöz képest.
- A **web Table Connector** kibontja a táblázat tartalmát egy HTML-weboldalról.

## <a name="supported-capabilities"></a>Támogatott képességek

A REST-forrásból származó adatok bármely támogatott fogadó adattárba másolhatók. Az adatok bármely támogatott forrásból származó adattárból átmásolhatók egy REST-tárolóba. A másolási tevékenység által a forrásként és a fogadóként támogatott adattárak listájáért lásd: [támogatott adattárak és-formátumok](copy-activity-overview.md#supported-data-stores-and-formats).

Ez az általános REST-összekötő a következőket támogatja:

- Adatok másolása REST-végpontról a **Get** vagy **post** metódusok használatával, valamint az adatok másolása Rest-végpontra a **post**, a **put** vagy a **patch** metódus használatával.
- Adatok másolása a következő hitelesítések egyikével: **Névtelen**, **alapszintű**, **HRE egyszerű szolgáltatásnév** és **felügyelt identitások az Azure-erőforrásokhoz**.
- **[Tördelés](#pagination-support)** a REST API-kon.
- A REST as forrásként másolja a REST JSON-választ a [-ként](#export-json-response-as-is) , vagy elemezze a [séma-hozzárendelés](copy-activity-schema-and-type-mapping.md#schema-mapping)használatával. Csak a **JSON** -beli válasz-adattartalom támogatott.

> [!TIP]
> Az adatok lekérésére vonatkozó kérelem teszteléséhez, mielőtt a REST-összekötőt a Data Factoryban konfigurálja, ismerkedjen meg a fejléc és a törzs követelményeivel kapcsolatos API-specifikációval. Az érvényesítéshez használhatja a Poster vagy a webböngésző eszközeit.

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

## <a name="get-started"></a>Bevezetés

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

A következő szakaszokban részletesen ismertetjük azokat a tulajdonságokat, amelyek segítségével meghatározható Data Factory a REST-összekötőre jellemző entitások.

## <a name="linked-service-properties"></a>Társított szolgáltatás tulajdonságai

A REST társított szolgáltatás a következő tulajdonságokat támogatja:

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| típus | A **Type** tulajdonságot **RestService** értékre kell beállítani. | Yes |
| url | A REST-szolgáltatás alap URL-címe. | Yes |
| enableServerCertificateValidation | Azt határozza meg, hogy a rendszer ellenőrizze-e a kiszolgálóoldali TLS/SSL-tanúsítványt a végponthoz való csatlakozáskor. | No<br /> (az alapértelmezett érték **igaz**) |
| authenticationType | A REST-szolgáltatáshoz való kapcsolódáshoz használt hitelesítés típusa. Az engedélyezett értékek: **Névtelen**, **alapszintű**, **AadServicePrincipal** és **ManagedServiceIdentity**. A felhasználó-alapú OAuth nem támogatott. Emellett a tulajdonságban konfigurálhatja a hitelesítési fejléceket is `authHeader` . Tekintse meg az alábbi, a további tulajdonságok és példák című szakaszt.| Yes |
| authHeaders | További HTTP-kérelmek fejlécei a hitelesítéshez.<br/> Az API-kulcsos hitelesítés használatához például válassza a "névtelen" lehetőséget, és adja meg az API-kulcsot a fejlécben. | No |
| Connectvia tulajdonsággal | Az adattárhoz való kapcsolódáshoz használt [Integration Runtime](concepts-integration-runtime.md) . További tudnivalók az [Előfeltételek](#prerequisites) szakaszban olvashatók. Ha nincs megadva, ez a tulajdonság az alapértelmezett Azure Integration Runtime használja. |No |

### <a name="use-basic-authentication"></a>Egyszerű hitelesítés használata

Állítsa a **authenticationType** tulajdonságot **alapértékre.** Az előző szakaszban leírt általános tulajdonságok mellett a következő tulajdonságokat is meg kell adni:

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| userName (Felhasználónév) | A REST-végpont eléréséhez használandó Felhasználónév. | Yes |
| jelszó | A felhasználó jelszava (a **Felhasználónév** értéke). A mező megjelölése **SecureString** -típusként, hogy biztonságosan tárolja azt Data Factoryban. [Hivatkozhat a Azure Key Vaultban tárolt titkos kulcsra](store-credentials-in-key-vault.md)is. | Yes |

**Példa**

```json
{
    "name": "RESTLinkedService",
    "properties": {
        "type": "RestService",
        "typeProperties": {
            "authenticationType": "Basic",
            "url" : "<REST endpoint>",
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

### <a name="use-aad-service-principal-authentication"></a>HRE-szolgáltatás egyszerű hitelesítésének használata

Állítsa a **authenticationType** tulajdonságot **AadServicePrincipal** értékre. Az előző szakaszban leírt általános tulajdonságok mellett a következő tulajdonságokat is meg kell adni:

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| servicePrincipalId | Azure Active Directory alkalmazás ügyfél-AZONOSÍTÓjának megadásához. | Yes |
| servicePrincipalKey | A Azure Active Directory alkalmazás kulcsának megadásához. Megjelöli ezt a mezőt **SecureString** , hogy biztonságosan tárolja Data Factoryban, vagy [hivatkozjon a Azure Key Vault tárolt titkos kulcsra](store-credentials-in-key-vault.md). | Yes |
| Bérlő | Adja meg a bérlői adatokat (tartománynevet vagy bérlői azonosítót), amely alatt az alkalmazás található. A Azure Portal jobb felső sarkában lévő egér fölé helyezve lekéri. | Yes |
| aadResourceId | Itt adhatja meg, hogy milyen HRE-erőforrást kér az engedélyezéshez, például: `https://management.core.windows.net` .| Yes |
| azureCloudType | Az egyszerű szolgáltatás hitelesítéséhez adja meg a HRE-alkalmazás regisztrálásához használt Azure-beli felhőalapú környezet típusát. <br/> Az engedélyezett értékek a következők: **AzurePublic**, **AzureChina**, **AzureUsGovernment** és **AzureGermany**. Alapértelmezés szerint a rendszer az adatfeldolgozó felhőalapú környezetét használja. | No |

**Példa**

```json
{
    "name": "RESTLinkedService",
    "properties": {
        "type": "RestService",
        "typeProperties": {
            "url": "<REST endpoint e.g. https://www.example.com/>",
            "authenticationType": "AadServicePrincipal",
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalKey": {
                "value": "<service principal key>",
                "type": "SecureString"
            },
            "tenant": "<tenant info, e.g. microsoft.onmicrosoft.com>",
            "aadResourceId": "<AAD resource URL e.g. https://management.core.windows.net>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="use-managed-identities-for-azure-resources-authentication"></a><a name="managed-identity"></a> Felügyelt identitások használata az Azure-erőforrások hitelesítéséhez

Állítsa a **authenticationType** tulajdonságot **ManagedServiceIdentity** értékre. Az előző szakaszban leírt általános tulajdonságok mellett a következő tulajdonságokat is meg kell adni:

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| aadResourceId | Itt adhatja meg, hogy milyen HRE-erőforrást kér az engedélyezéshez, például: `https://management.core.windows.net` .| Yes |

**Példa**

```json
{
    "name": "RESTLinkedService",
    "properties": {
        "type": "RestService",
        "typeProperties": {
            "url": "<REST endpoint e.g. https://www.example.com/>",
            "authenticationType": "ManagedServiceIdentity",
            "aadResourceId": "<AAD resource URL e.g. https://management.core.windows.net>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="using-authentication-headers"></a>Hitelesítési fejlécek használata

Emellett a kérések fejléceit a beépített hitelesítési típusokkal együtt is konfigurálhatja a hitelesítéshez.

**Példa: API-kulcsos hitelesítés használata**

```json
{
    "name": "RESTLinkedService",
    "properties": {
        "type": "RestService",
        "typeProperties": {
            "url": "<REST endpoint>",
            "authenticationType": "Anonymous",
            "authHeader": {
                "x-api-key": {
                    "type": "SecureString",
                    "value": "<API key>"
                }
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

Ez a szakasz a REST-adatkészlet által támogatott tulajdonságok listáját tartalmazza. 

Az adatkészletek definiálásához rendelkezésre álló csoportok és tulajdonságok teljes listáját lásd: [adatkészletek és társított szolgáltatások](concepts-datasets-linked-services.md). 

Az adatok REST-ből való másolásához a következő tulajdonságok támogatottak:

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| típus | Az adatkészlet **Type** tulajdonságát **RestResource** értékre kell állítani. | Yes |
| relativeUrl | Az adatforrást tartalmazó erőforrás relatív URL-címe. Ha nincs megadva ez a tulajdonság, a rendszer csak a társított szolgáltatás definíciójában megadott URL-címet használja. A HTTP-összekötő a következő összevont URL-címről másolja az adatait: `[URL specified in linked service]/[relative URL specified in dataset]` . | No |

Ha a, a `requestMethod` `additionalHeaders` `requestBody` és az adatkészletben is a beállítást használja, a rendszer `paginationRules` továbbra is támogatja a-t, míg az új modellt a tevékenység előrehaladásával fogja használni.

**Példa**

```json
{
    "name": "RESTDataset",
    "properties": {
        "type": "RestResource",
        "typeProperties": {
            "relativeUrl": "<relative url>"
        },
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<REST linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Másolási tevékenység tulajdonságai

Ez a szakasz a REST-forrás és a fogadó által támogatott tulajdonságok listáját tartalmazza.

A tevékenységek definiálásához elérhető csoportok és tulajdonságok teljes listáját lásd: [folyamatok](concepts-pipelines-activities.md). 

### <a name="rest-as-source"></a>REST forrásként

A másolási tevékenység **forrása** szakasz a következő tulajdonságokat támogatja:

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| típus | A másolási tevékenység forrásának **Type** tulajdonságát **RestSource** értékre kell állítani. | Yes |
| requestMethod | A HTTP-metódus. Az engedélyezett értékek: **Get** (alapértelmezett) és **post**. | No |
| additionalHeaders | További HTTP-kérelmek fejlécei. | No |
| requestBody | A HTTP-kérelem törzse. | No |
| paginationRules | A következő lapra irányuló kérelmek összeállításához szükséges tördelési szabályok. A részletekért tekintse meg a [tördelés támogatását](#pagination-support) ismertető szakaszt. | No |
| httpRequestTimeout | A válasz kéréséhez szükséges HTTP-kérelem időkorlátja (a **TimeSpan** érték). Ez az érték a válasz lekérésének időtúllépése, nem pedig a válaszüzenetek olvasásának időtúllépése. Az alapértelmezett érték a **00:01:40**.  | No |
| requestInterval | Az a várakozási idő, ameddig a következő lapra küldött kérelem elküldése előtt elküldve. Az alapértelmezett érték **00:00:01** |  No |

>[!NOTE]
>A REST-összekötő figyelmen kívül hagyja a ben megadott "elfogadás" fejlécet `additionalHeaders` . Mivel a REST-összekötő csak a JSON-beli választ támogatja, automatikusan létrehozza a fejlécét `Accept: application/json` .

**1. példa: a Get metódus használata tördeléssel**

```json
"activities":[
    {
        "name": "CopyFromREST",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<REST input dataset name>",
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
                "type": "RestSource",
                "additionalHeaders": {
                    "x-user-defined": "helloworld"
                },
                "paginationRules": {
                    "AbsoluteUrl": "$.paging.next"
                },
                "httpRequestTimeout": "00:01:00"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

**2. példa: a post metódus használata**

```json
"activities":[
    {
        "name": "CopyFromREST",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<REST input dataset name>",
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
                "type": "RestSource",
                "requestMethod": "Post",
                "requestBody": "<body for POST REST request>",
                "httpRequestTimeout": "00:01:00"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="rest-as-sink"></a>REST as fogadó

A másolási tevékenység fogadója szakasz a következő  tulajdonságokat támogatja:

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| típus | A másolási tevékenység fogadójának **Type** tulajdonságát **RestSink** értékre kell állítani. | Yes |
| requestMethod | A HTTP-metódus. Az engedélyezett értékek: **post** (alapértelmezett), **put** és **patch**. | No |
| additionalHeaders | További HTTP-kérelmek fejlécei. | No |
| httpRequestTimeout | A válasz kéréséhez szükséges HTTP-kérelem időkorlátja (a **TimeSpan** érték). Ez az érték a válasz lekérésének időtúllépése, nem az adatírás időkorlátja. Az alapértelmezett érték a **00:01:40**.  | No |
| requestInterval | A különböző kérelmek közötti időközi idő ezredmásodpercben. A kérelem intervallumának értékének egy [10, 60000] közötti számnak kell lennie. |  No |
| httpCompressionType | A HTTP-tömörítési típus, amelyet az adatok optimális tömörítési szinttel történő küldésekor kell használni. Az engedélyezett értékek a **none** és a **gzip**. | No |
| writeBatchSize | A REST-gyűjtőbe írandó rekordok száma kötegben. Az alapértelmezett érték a 10000. | No |

A REST-összekötő mint fogadó együttműködik a JSON-t elfogadó REST API-kkal. Az adatküldés a JSON-ban a következő mintával történik. Szükség esetén a másolási tevékenység [sémájának leképezésével](copy-activity-schema-and-type-mapping.md#schema-mapping) átalakíthatja a forrásadatokat úgy, hogy azok megfeleljenek a REST API által várt adattartalomnak.

```json
[
    { <data object> },
    { <data object> },
    ...
]
```

**Példa**

```json
"activities":[
    {
        "name": "CopyToREST",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<REST output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "RestSink",
                "requestMethod": "POST",
                "httpRequestTimeout": "00:01:40",
                "requestInterval": 10,
                "writeBatchSize": 10000,
                "httpCompressionType": "none",
            },
        }
    }
]
```

## <a name="pagination-support"></a>Tördelési támogatás

A REST API-k adatainak másolásakor a REST API egy ésszerű számú kérelemre korlátozza az egyes kérések válaszának hasznos méretét. míg nagy mennyiségű adattal tér vissza, az eredmény több oldalra van bontva, és a hívóknak egymást követő kéréseket kell küldeniük az eredmény következő oldalának beolvasásához. Az egyik oldalra vonatkozó kérelem általában dinamikus, és az előző oldal válaszában visszaadott információkból áll.

Ez az általános REST-összekötő a következő tördelési mintákat támogatja: 

* A következő kérelem abszolút vagy relatív URL-címe = tulajdonság értéke az aktuális válasz törzsében
* A következő kérelem abszolút vagy relatív URL-címe = fejléc értéke az aktuális válasz fejlécekben
* A következő kérelem lekérdezési paramétere = tulajdonság értéke az aktuális válasz törzsében
* Következő kérelem lekérdezési paramétere = fejléc értéke az aktuális válasz fejlécekben
* Következő kérelem fejléce = tulajdonság értéke az aktuális válasz törzsében
* Következő kérelem fejléce = fejléc értéke az aktuális válasz fejlécekben

A **tördelési szabályok** az adatkészlet olyan szótárában vannak definiálva, amely egy vagy több kis-és nagybetűket megkülönböztető kulcs-érték párokat tartalmaz. A rendszer a konfigurációt fogja használni a második lapról kezdődő kérelem létrehozásához. Az összekötő leáll, ha az 204-as HTTP-állapotkód (nincs tartalom), vagy a "paginationRules" JSONPath egyik kifejezése null értéket ad vissza.

A tördelési szabályokban **támogatott kulcsok** :

| Kulcs | Leírás |
|:--- |:--- |
| AbsoluteUrl | Azt az URL-címet adja meg, amely a következő kérést adja ki. **Abszolút URL-cím vagy relatív URL-cím** is lehet. |
| QueryParameters. *request_query_parameter* VAGY QueryParameters [' request_query_parameter '] | a "request_query_parameter" felhasználó által definiált, amely egy lekérdezési paraméter nevére hivatkozik a következő HTTP-kérelem URL-címében. |
| Fejlécek. *request_header* VAGY fejlécek ["request_header"] | a "request_header" felhasználó által definiált, amely egy fejléc nevére hivatkozik a következő HTTP-kérelemben. |

A tördelési szabályokban **támogatott értékek** :

| Érték | Leírás |
|:--- |:--- |
| Fejlécek. *response_header* VAGY fejlécek ["response_header"] | a "response_header" felhasználó által definiált, amely a jelenlegi HTTP-válaszban egy fejléc nevére hivatkozik, amelynek értéke a következő kérelem kibocsátására szolgál majd. |
| A "$" kezdetű JSONPath kifejezés (amely a válasz törzsének gyökerét jelöli) | A válasz törzsének csak egy JSON-objektumot kell tartalmaznia. A JSONPath kifejezésnek egyetlen primitív értéket kell visszaadnia, amely a következő kérelem kibocsátására szolgál majd. |

**Példa**

A Facebook Graph API a következő struktúra válaszát adja vissza, amely esetben a következő oldal URL-címe szerepel a ***lapozásban. tovább***:

```json
{
    "data": [
        {
            "created_time": "2017-12-12T14:12:20+0000",
            "name": "album1",
            "id": "1809938745705498_1809939942372045"
        },
        {
            "created_time": "2017-12-12T14:14:03+0000",
            "name": "album2",
            "id": "1809938745705498_1809941802371859"
        },
        {
            "created_time": "2017-12-12T14:14:11+0000",
            "name": "album3",
            "id": "1809938745705498_1809941879038518"
        }
    ],
    "paging": {
        "cursors": {
            "after": "MTAxNTExOTQ1MjAwNzI5NDE=",
            "before": "NDMyNzQyODI3OTQw"
        },
        "previous": "https://graph.facebook.com/me/albums?limit=25&before=NDMyNzQyODI3OTQw",
        "next": "https://graph.facebook.com/me/albums?limit=25&after=MTAxNTExOTQ1MjAwNzI5NDE="
    }
}
```

A kapcsolódó REST másolási tevékenység forrásának konfigurációja, különösen a `paginationRules` következők:

```json
"typeProperties": {
    "source": {
        "type": "RestSource",
        "paginationRules": {
            "AbsoluteUrl": "$.paging.next"
        },
        ...
    },
    "sink": {
        "type": "<sink type>"
    }
}
```

## <a name="use-oauth"></a>Az OAuth használata
Ez a szakasz azt ismerteti, hogyan használható megoldás-sablon az adatok REST-összekötőről Azure Data Lake Storageba való másolásához JSON formátumban a OAuth használatával. 

### <a name="about-the-solution-template"></a>Tudnivalók a megoldási sablonról

A sablon két tevékenységet tartalmaz:
- A **webes** tevékenység beolvassa a tulajdonosi jogkivonatot, majd továbbítja azt a későbbi másolási tevékenységnek engedélyezésként.
- A **másolási** tevékenység átmásolja az adatait a többiből a Azure Data Lake Storageba.

A sablon két paramétert határoz meg:
- A **SinkContainer** a gyökérmappa elérési útja, ahová a rendszer átmásolja az adatait a Azure Data Lake Storageba. 
- A **SinkDirectory** az a könyvtár elérési útja, amelynek a gyökerében az adatait másolja a rendszer a Azure Data Lake Storageba. 

### <a name="how-to-use-this-solution-template"></a>A megoldás sablonjának használata

1. Lépjen a **Másolás Rest vagy http használatával OAuth** sablonnal. Hozzon létre egy új kapcsolatokat a forrás-kapcsolatok számára. 
    ![Új kapcsolatok létrehozása](media/solution-template-copy-from-rest-or-http-using-oauth/source-connection.png)

    Alább láthatók az új társított szolgáltatás (REST) beállításainak főbb lépései:
    
     1. Az **alap URL-cím** területen adja meg a saját forrás Rest-szolgáltatás URL-címét. 
     2. A **Hitelesítés típusa mezőben** válassza a *Névtelen* lehetőséget.
        ![Új REST-kapcsolatok](media/solution-template-copy-from-rest-or-http-using-oauth/new-rest-connection.png)

2. Hozzon létre egy új kapcsolatokat a cél-kapcsolatok számára.  
    ![Új Gen2-kapcsolatok](media/solution-template-copy-from-rest-or-http-using-oauth/destination-connection.png)

3. Válassza **a sablon használata** lehetőséget.
    ![Sablon használata](media/solution-template-copy-from-rest-or-http-using-oauth/use-this-template.png)

4. A folyamat a következő példában látható módon jelenik meg: a  ![ képernyőképen a sablon alapján létrehozott folyamat látható.](media/solution-template-copy-from-rest-or-http-using-oauth/pipeline.png)

5. Válassza a **webes** tevékenység lehetőséget. A **Beállítások** területen adja meg a megfelelő **URL-címet**, **metódust**, **fejlécet** és **törzset** , hogy beolvassa a OAuth tulajdonosi jogkivonatot azon szolgáltatás bejelentkezési API-ból, amelyről adatokat szeretne másolni. A sablonban található helyőrző Azure Active Directory (HRE) OAuth mutatja be. Megjegyzés: a REST-összekötő natív módon támogatja a HRE-hitelesítést, itt csak egy példa a OAuth folyamatra. 

    | Tulajdonság | Leírás |
    |:--- |:--- |
    | URL-cím |Adja meg a OAuth tulajdonosi jogkivonatának beolvasásához használandó URL-címet. például a mintában itt látható https://login.microsoftonline.com/microsoft.onmicrosoft.com/oauth2/token |
    | Metódus | A HTTP-metódus. Az engedélyezett értékek: **post** és **Get**. | 
    | Fejlécek | A fejléc felhasználó által definiált, amely egy fejléc nevére hivatkozik a HTTP-kérelemben. | 
    | Törzs | A HTTP-kérelem törzse. | 

    ![Folyamat](media/solution-template-copy-from-rest-or-http-using-oauth/web-settings.png)

6. Az **adatok másolása** tevékenységben válassza a *forrás* fület, és láthatja, hogy az előző lépésben beolvasott tulajdonosi jogkivonat (access_token) át lesz adva az adatok másolása tevékenységnek a további fejlécek szerinti **engedélyezéséhez** . A folyamat futtatásának megkezdése előtt erősítse meg a következő tulajdonságok beállításait.

    | Tulajdonság | Leírás |
    |:--- |:--- |
    | Kérelem metódusa | A HTTP-metódus. Az engedélyezett értékek: **Get** (alapértelmezett) és **post**. | 
    | További fejlécek | További HTTP-kérelmek fejlécei.| 

   ![Forrás-hitelesítés másolása](media/solution-template-copy-from-rest-or-http-using-oauth/copy-data-settings.png)

7. Válassza a **hibakeresés** lehetőséget, adja meg a **paramétereket**, majd kattintson a **Befejezés gombra**.
   ![Folyamat futtatása](media/solution-template-copy-from-rest-or-http-using-oauth/pipeline-run.png) 

8. Ha a folyamat futtatása sikeresen befejeződött, a következő példához hasonló eredményt láthat: a ![ folyamat futási eredménye](media/solution-template-copy-from-rest-or-http-using-oauth/run-result.png) 

9. Kattintson a webaktivitás "output" ikonjára a **műveletek** oszlopban, a szolgáltatás által visszaadott access_token jelenik meg.

   ![Jogkivonat kimenete](media/solution-template-copy-from-rest-or-http-using-oauth/token-output.png) 

10. Kattintson a **műveletek** oszlopban található CopyActivity "bemenet" ikonjára, és a CopyActivity által beolvasott access_token a hitelesítéshez adja át a rendszer. 

    ![Jogkivonat bemenete](media/solution-template-copy-from-rest-or-http-using-oauth/token-input.png)
        
    >[!CAUTION] 
    >Ha el szeretné kerülni a token egyszerű szövegbe való naplózását, engedélyezze a "biztonságos kimenet" lehetőséget a webes tevékenységben, és a "biztonságos bevitel" lehetőséget a másolási tevékenységben.


## <a name="export-json-response-as-is"></a>JSON-válasz exportálása a következőképpen:

Ezt a REST-összekötőt használhatja a REST API JSON-válasz exportálására a különböző file-alapú tárolók esetében. Az ilyen sémák és sémák közötti másoláshoz ugorjon a másolási tevékenységben a "Structure" (más néven *séma*) szakaszra.

## <a name="schema-mapping"></a>Séma-hozzárendelés

Ha adatokat szeretne másolni a REST-végpontról a táblázatos fogadóba, tekintse meg a [séma-hozzárendelést](copy-activity-schema-and-type-mapping.md#schema-mapping).

## <a name="next-steps"></a>Következő lépések

A másolási tevékenység által támogatott adattárak listáját a Azure Data Factoryban található forrásként és nyelőként tekintse meg a [támogatott adattárak és-formátumok](copy-activity-overview.md#supported-data-stores-and-formats)című témakörben.
