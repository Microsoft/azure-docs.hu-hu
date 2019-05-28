---
title: Adatok áthelyezése az OData-források |} A Microsoft Docs
description: Tudnivalók az adatok áthelyezése az Azure Data Factory használatával az OData-források.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.assetid: de28fa56-3204-4546-a4df-21a21de43ed7
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: b2c665de94750c4c6f41bda47960fdb9ba17e819
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2019
ms.locfileid: "60824032"
---
# <a name="move-data-from-an-odata-source-using-azure-data-factory"></a>Helyezze át az adatok Azure Data Factory használatával az OData-Adatcsatornából forrás
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [1-es verzió](data-factory-odata-connector.md)
> * [2-es verzió (aktuális verzió)](../connector-odata.md)

> [!NOTE]
> Ez a cikk a Data Factory 1-es verziójára vonatkozik. Ha a jelenlegi verzió a Data Factory szolgáltatás használ, tekintse meg [OData-összekötő a v2-ben](../connector-odata.md).


Ez a cikk ismerteti az Azure Data Factory a másolási tevékenység használatával helyezheti át egy OData-forráshoz. Épül a [adattovábbítási tevékenységek](data-factory-data-movement-activities.md) című cikket, amely megadja az adatok áthelyezését a másolási tevékenységgel rendelkező általános áttekintése.

Másolhat adatokat egy OData-forráshoz bármely támogatott fogadó adattárba. A másolási tevékenység által fogadóként támogatott adattárak listáját lásd: a [támogatott adattárak](data-factory-data-movement-activities.md#supported-data-stores-and-formats) tábla. A Data factory jelenleg csak helyez át adatokat egy OData-forráshoz pedig más adattárakban, de amely adatokat helyez át más adattárakban egy OData-forráshoz, a nem támogatja.

## <a name="supported-versions-and-authentication-types"></a>Támogatott verziók és hitelesítési típusok
Az OData-összekötő 3.0 és 4.0-s verzióját, és mindkét felhőalapú OData adatait és a helyszíni OData-források lehet másolni az OData-verzió támogatja. Az utóbbi esetében az adatkezelési átjárót telepítenie kell. Lásd: [adatok áthelyezése a helyszíni és a felhő között](data-factory-move-data-between-onprem-and-cloud.md) adatkezelési átjáró részleteit ismertető cikket.

Alább hitelesítési típusok támogatottak:

* Hozzáférés **felhőalapú** OData-csatornáról, használhatja, hogy a névtelen, alapszintű (felhasználónév és jelszó) vagy az Azure Active Directory-alapú OAuth-hitelesítést.
* Hozzáférés **helyszíni** OData-csatornáról, használja névtelen, alapszintű (felhasználónév és jelszó) vagy Windows-hitelesítéssel.

## <a name="getting-started"></a>Első lépések
Létrehozhat egy folyamatot egy másolási tevékenységgel az adatok áthelyezéséhez az OData forrásból származó különböző eszközök/API-k használatával.

A folyamat létrehozásának legegyszerűbb módja az, hogy használja a **másolása varázsló**. Lásd: [oktatóanyag: Hozzon létre egy folyamatot a másolás varázsló használatával](data-factory-copy-data-wizard-tutorial.md) gyors bemutató létrehozása egy folyamatot az adatok másolása varázsló használatával.

A következő eszközök használatával hozzon létre egy folyamatot: **Az Azure portal**, **Visual Studio**, **Azure PowerShell-lel**, **Azure Resource Manager-sablon**, **.NET API**, és  **REST API-val**. Lásd: [másolási tevékenység oktatóanyagát](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) egy másolási tevékenységgel ellátott adatcsatorna létrehozása a részletes útmutatóját.

Az eszközök vagy az API-kat használja, hogy létrehoz egy folyamatot, amely a helyez át adatokat egy forrásadattárból egy fogadó adattárba a következő lépéseket fogja végrehajtani:

1. Hozzon létre **társított szolgáltatásokat** mutató hivatkozást a bemeneti és kimeneti adatokat tárolja a data factoryjához.
2. Hozzon létre **adatkészletek** , amely a másolási művelet bemeneti és kimeneti adatokat jelöli.
3. Hozzon létre egy **folyamat** egy másolási tevékenységgel, amely egy adatkészletet bemenetként, és a egy adatkészletet pedig kimenetként.

A varázsló használatakor a rendszer automatikusan létrehozza a Data Factory-entitásokat (társított szolgáltatások, adatkészletek és folyamat) JSON-definíciói az Ön számára. Eszközök/API-k (kivéve a .NET API) használatakor adja meg a Data Factory-entitások a JSON formátumban.  A Data Factory-entitások, amely adatokat másol egy OData-forráshoz használt JSON-definíciói egy minta: [JSON-példa: Adatok másolása az OData-erőforrás az Azure Blob](#json-example-copy-data-from-odata-source-to-azure-blob) című szakaszát.

A következő szakaszok az OData-forrásra adott Data Factory-entitások definiálásához használt JSON-tulajdonságokkal kapcsolatos részletekért:

## <a name="linked-service-properties"></a>Társított szolgáltatás tulajdonságai
A következő táblázat a JSON-elemeket társított OData-szolgáltatás leírását.

| Tulajdonság | Leírás | Szükséges |
| --- | --- | --- |
| type |A type tulajdonságot kell beállítani: **OData** |Igen |
| url |Az OData-szolgáltatás URL-címe. |Igen |
| authenticationType |Az OData-forráshoz való kapcsolódáshoz használt hitelesítés típusa. <br/><br/> Felhőalapú OData a lehetséges értékek: névtelen, alapszintű és OAuth (Megjegyzés: az Azure Data Factory jelenleg csak támogatás az Azure Active Directory-alapú OAuth). <br/><br/> A helyszíni OData a lehetséges értékek: névtelen, alapszintű és a Windows. |Igen |
| username |Ha alapszintű hitelesítést használ, adja meg a felhasználónevet. |Igen (csak akkor, ha az egyszerű hitelesítés használata esetén) |
| password |Adja meg a felhasználónévhez megadott felhasználói fiók jelszavát. |Igen (csak akkor, ha az egyszerű hitelesítés használata esetén) |
| authorizedCredential |Ha OAuth használ, kattintson a **engedélyezés** gombra a Data Factory Copy varázslót vagy a szerkesztőben, és adja meg a hitelesítő adatok a tulajdonság értéke lesz automatikusan létrehozott. |Igen (csak akkor, ha OAuth-hitelesítés használata esetén) |
| gatewayName |Az átjáró, amely a Data Factory szolgáltatás segítségével csatlakozhat a helyszíni OData-szolgáltatás neve. Csak adja meg, ha a másolt adatok helyi OData-forráshoz. |Nem |

### <a name="using-basic-authentication"></a>Alapszintű hitelesítés használata
```json
{
    "name": "inputLinkedService",
    "properties":
    {
        "type": "OData",
        "typeProperties":
        {
            "url": "https://services.odata.org/OData/OData.svc",
            "authenticationType": "Basic",
            "username": "username",
            "password": "password"
        }
    }
}
```

### <a name="using-anonymous-authentication"></a>A névtelen hitelesítés használatával
```json
{
    "name": "ODataLinkedService",
    "properties":
    {
        "type": "OData",
        "typeProperties":
        {
            "url": "https://services.odata.org/OData/OData.svc",
            "authenticationType": "Anonymous"
        }
    }
}
```

### <a name="using-windows-authentication-accessing-on-premises-odata-source"></a>Elérése OData-forráshoz a helyi Windows-hitelesítés használatával
```json
{
    "name": "inputLinkedService",
    "properties":
    {
        "type": "OData",
        "typeProperties":
        {
            "url": "<endpoint of on-premises OData source e.g. Dynamics CRM>",
            "authenticationType": "Windows",
            "username": "domain\\user",
            "password": "password",
            "gatewayName": "mygateway"
        }
    }
}
```

### <a name="using-oauth-authentication-accessing-cloud-odata-source"></a>OAuth hitelesítési hozzáférni felhőalapú OData-erőforrás használata
```json
{
    "name": "inputLinkedService",
    "properties":
    {
        "type": "OData",
            "typeProperties":
        {
            "url": "<endpoint of cloud OData source e.g. https://<tenant>.crm.dynamics.com/XRMServices/2011/OrganizationData.svc>",
            "authenticationType": "OAuth",
            "authorizedCredential": "<auto generated by clicking the Authorize button on UI>"
        }
    }
}
```

## <a name="dataset-properties"></a>Adatkészlet tulajdonságai
Szakaszok & adatkészletek definiálását tulajdonságainak teljes listáját lásd: a [adatkészletek létrehozása](data-factory-create-datasets.md) cikk. Például a szerkezetet, rendelkezésre állást és szabályzatát adatkészlet JSON szakaszok hasonlóak az összes adatkészlet esetében (az Azure SQL, az Azure blob-, az Azure table-, stb.).

A **typeProperties** szakasz eltérő az egyes adatkészlet, és az adattárban lévő adatok helyét ismerteti. A typeProperties szakasz típusú adatkészlet **ODataResource** (amely tartalmazza az OData-adatkészlet) a következő tulajdonságokkal rendelkezik.

| Tulajdonság | Leírás | Szükséges |
| --- | --- | --- |
| path |Az OData-erőforrás elérési útja |Nem |

## <a name="copy-activity-properties"></a>Másolási tevékenység tulajdonságai
Szakaszok & definiálását tevékenységek tulajdonságainak teljes listáját lásd: a [folyamatok létrehozása](data-factory-create-pipelines.md) cikk. Tulajdonságok, mint például a nevét, leírását, bemeneti és kimeneti táblák és a házirend az összes típusú tevékenységek érhetők el.

A tevékenység a typeProperties szakasz tulajdonságai másrészről tevékenységek minden típusának számától függ. A másolási tevékenységhez azok változhat a forrásként és fogadóként típusú is.

Ha a forrás típusa van **RelationalSource** (amely tartalmazza az OData) typeProperties szakasz érhető el az alábbi tulajdonságokat:

| Tulajdonság | Leírás | Példa | Kötelező |
| --- | --- | --- | --- |
| query |Az egyéni lekérdezés segítségével olvassa el az adatokat. |"?$select=Name, Description&$top=5" |Nem |

## <a name="type-mapping-for-odata"></a>OData-leképezés típusa
Ahogy korábban már említettük, az a [adattovábbítási tevékenységek](data-factory-data-movement-activities.md) a cikkben a másolási tevékenység végzi az automatikus típuskonverziók a fogadó-típusokat az alábbi kétlépéses módszer a forrás-típusok közül.

1. A natív forrástípusok átalakítása typ .NET
2. A .NET-típusból átalakítása natív fogadó típusa

OData-helyez át adatokat, amikor a következő hozzárendeléseket használják OData-típusok közül .NET típusa.

| OData-adatok típusa | .NET Type |
| --- | --- |
| Edm.Binary |Byte[] |
| Edm.Boolean |Bool |
| Edm.Byte |Byte[] |
| Edm.DateTime |DateTime |
| Edm.Decimal |Decimal |
| Edm.Double |Double |
| Edm.Single |Single |
| Edm.Guid |Guid |
| Edm.Int16 |Int16 |
| Edm.Int32 |Int32 |
| Edm.Int64 |Int64 |
| Edm.SByte |Int16 |
| Edm.String |String |
| Edm.Time |TimeSpan |
| Edm.DateTimeOffset |DateTimeOffset |

> [!Note]
> OData összetett adattípusok például objektum nem támogatottak.

## <a name="json-example-copy-data-from-odata-source-to-azure-blob"></a>JSON-példa: Adatok másolása az OData-erőforrás az Azure Blob
Ebben a példában biztosít, amellyel létrehoz egy folyamatot használatával példa JSON-definíciók [az Azure portal](data-factory-copy-activity-tutorial-using-azure-portal.md) vagy [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) vagy [Azure PowerShell-lel](data-factory-copy-activity-tutorial-using-powershell.md). Azok bemutatják, hogyan másolhat adatokat egy OData-forrás egy Azure Blob Storage. Azonban adatok átmásolhatók a conditions stated above fogadóként valamelyik [Itt](data-factory-data-movement-activities.md#supported-data-stores-and-formats) a másolási tevékenységgel az Azure Data Factoryban. A minta az alábbi Data Factory-entitások rendelkezik:

1. A társított szolgáltatás típusa [OData](#linked-service-properties).
2. A társított szolgáltatás típusa [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
3. Egy bemeneti [adatkészlet](data-factory-create-datasets.md) típusú [ODataResource](#dataset-properties).
4. Kimenet [adatkészlet](data-factory-create-datasets.md) típusú [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
5. A [folyamat](data-factory-create-pipelines.md) másolási tevékenységgel, amely használja [RelationalSource](#copy-activity-properties) és [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

A minta adatokat másol egy Azure-blobba OData-forráshoz óránként lekérdezését. Ezek a minták a használt JSON-tulajdonságokat a minták a következő szakaszok ismertetik.

**OData-beli társított szolgáltatást:** Ebben a példában a névtelen hitelesítést használ. Lásd: [OData-beli társított szolgáltatást](#linked-service-properties) használható hitelesítési típust a következő szakaszban.

```json
{
    "name": "ODataLinkedService",
    "properties":
    {
        "type": "OData",
        "typeProperties":
        {
            "url": "https://services.odata.org/OData/OData.svc",
            "authenticationType": "Anonymous"
        }
    }
}
```

**Az Azure Storage társított szolgáltatást:**

```json
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

**OData bemeneti adatkészlet:**

Beállítás az "external": "true" tájékoztatja a Data Factory szolgáltatásban, hogy az adatkészletet a data factory a külső, és nem hozzák az adat-előállító adott tevékenységéhez.

```json
{
    "name": "ODataDataset",
    "properties":
    {
        "type": "ODataResource",
        "typeProperties":
        {
            "path": "Products"
        },
        "linkedServiceName": "ODataLinkedService",
        "structure": [],
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true,
        "policy": {
            "retryInterval": "00:01:00",
            "retryTimeout": "00:10:00",
            "maximumRetry": 3
        }
    }
}
```

Adjon meg **elérési út** az adatkészlet definíciója nem kötelező megadni.

**Azure blobkimeneti adatkészlet:**

Adatokat írt egy új blob minden órában (frequency: óra, időköz: 1). A mappa elérési útját a BLOB a feldolgozás alatt álló szelet kezdő időpontja alapján dinamikusan kiértékeli. A mappa elérési útját használja, év, hónap, nap és óra részei a kezdési időpontot.

```json
{
    "name": "AzureBlobODataDataSet",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties": {
            "folderPath": "mycontainer/odata/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
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

**Másolási tevékenység az OData-erőforrás és a fogadó Blob egy folyamatban:**

A folyamat egy másolási tevékenység, amely a bemeneti és kimeneti adatkészleteket használatára van konfigurálva, és óránként ütemezett tartalmazza. A folyamat JSON-definíciót a **forrás** típusa **RelationalSource** és **fogadó** típusa **BlobSink**. A megadott SQL-lekérdezést a **lekérdezés** tulajdonság kiválasztja a legújabb (legújabb) adatok OData-forráshoz.

```json
{
    "name": "CopyODataToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [
            {
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "RelationalSource",
                        "query": "?$select=Name, Description&$top=5",
                    },
                    "sink": {
                        "type": "BlobSink",
                        "writeBatchSize": 0,
                        "writeBatchTimeout": "00:00:00"
                    }
                },
                "inputs": [
                    {
                        "name": "ODataDataSet"
                    }
                ],
                "outputs": [
                    {
                        "name": "AzureBlobODataDataSet"
                    }
                ],
                "policy": {
                    "timeout": "01:00:00",
                    "concurrency": 1
                },
                "scheduler": {
                    "frequency": "Hour",
                    "interval": 1
                },
                "name": "ODataToBlob"
            }
        ],
        "start": "2017-02-01T18:00:00Z",
        "end": "2017-02-03T19:00:00Z"
    }
}
```

Adjon meg **lekérdezés** a folyamat definíciója nem kötelező megadni. A **URL-cím** van, hogy a Data Factory szolgáltatás használ-e adatokat: + (Kötelező) a társított szolgáltatásban megadott URL-címet a adatkészletet (nem kötelező) és a lekérdezés (nem kötelező) a folyamat a megadott elérési út.

### <a name="type-mapping-for-odata"></a>OData-leképezés típusa
Említetteknek megfelelően az [adattovábbítási tevékenységek](data-factory-data-movement-activities.md) a cikkben a másolási tevékenység végzi az automatikus típuskonverziók a fogadó-típusokat az alábbi 2. lépés – a módszert használja a forrás típusa:

1. A natív forrástípusok átalakítása typ .NET
2. A .NET-típusból átalakítása natív fogadó típusa

OData-adatokat helyez át adatokat tárolja, ha .NET Tulajdonságtípusokat OData adattípusok vannak leképezve.

## <a name="map-source-to-sink-columns"></a>A fogadó-oszlopok térkép forrása
Fogadó-adatkészlet oszlopaihoz forrásadatkészlet leképezés oszlopai kapcsolatos további információkért lásd: [az Azure Data Factoryban adatkészletoszlopok leképezése](data-factory-map-columns.md).

## <a name="repeatable-read-from-relational-sources"></a>A relációs források megismételhető olvasása
Amikor adatmásolásra, relációs adatokat tárol, ismételhetőség tartsa szem előtt, nem kívánt eredmények elkerülése érdekében. Az Azure Data Factoryben futtathatja a szelet manuálisan. Beállíthatja az újrapróbálkozási szabályzat egy adatkészlethez, úgy, hogy a szelet akkor fut újra, ha hiba történik. Ha a szelet akkor fut újra, vagy módon, győződjön meg arról, hogy ugyanazokat az adatokat olvasható függetlenül attól, hogy hány alkalommal fut egy szeletet, kell. Lásd: [olvasni a relációs források Repeatable](data-factory-repeatable-copy.md#repeatable-read-from-relational-sources).

## <a name="performance-and-tuning"></a>Teljesítmény és finomhangolás
Lásd: [másolási tevékenységek teljesítményéhez és teljesítményhangolási útmutatóból](data-factory-copy-activity-performance.md) megismerheti a kulcsfontosságú szerepet játszik az adatáthelyezés (másolási tevékenység) az Azure Data Factory és a különféle módokon optimalizálhatja azt, hogy hatással lehet a teljesítményre.
