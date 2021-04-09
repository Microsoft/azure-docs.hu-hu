---
title: Adatok áthelyezése OData-forrásokból
description: Ismerje meg, hogyan helyezhet át OData-forrásokból származó adatok Azure Data Factory használatával.
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: fae78459a752d78fe47f189bca67667e917ba561
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "100380066"
---
# <a name="move-data-from-an-odata-source-using-azure-data-factory"></a>Adatok áthelyezése egy OData-forrásból Azure Data Factory használatával
> [!div class="op_single_selector" title1="Válassza ki az Ön által használt Data Factory-szolgáltatás verzióját:"]
> * [1-es verzió](data-factory-odata-connector.md)
> * [2-es verzió (aktuális verzió)](../connector-odata.md)

> [!NOTE]
> Ez a cikk a Data Factory 1-es verziójára vonatkozik. Ha a Data Factory szolgáltatás aktuális verzióját használja, tekintse meg a [OData-összekötőt a v2-ben](../connector-odata.md).


Ez a cikk azt ismerteti, hogyan használható a másolási tevékenység a Azure Data Factoryban az adatok OData-forrásból való áthelyezéséhez. Az [adattovábbítási tevékenységekről](data-factory-data-movement-activities.md) szóló cikkre épül, amely általános áttekintést nyújt az adatáthelyezésről a másolási tevékenységgel.

A OData-forrásból származó adatok bármely támogatott fogadó adattárba másolhatók. A másolási tevékenység által mosogatóként támogatott adattárak listáját a [támogatott adattárak](data-factory-data-movement-activities.md#supported-data-stores-and-formats) táblázatban tekintheti meg. A adatfeldolgozó jelenleg csak a OData-forrásból származó adatok áthelyezését támogatja más adattárakba, az adatok más adattárakból egy OData-forrásba való áthelyezését azonban nem.

## <a name="supported-versions-and-authentication-types"></a>Támogatott verziók és hitelesítési típusok
Ez a OData-összekötő támogatja a 3,0-es és a 4,0-os OData, valamint a felhőalapú OData és a helyszíni OData-forrásokból származó adatok másolását is. Az utóbbi esetben telepítenie kell az adatkezelés-átjárót. Az adatkezelés átjáróval kapcsolatos további információkért lásd: [adatok áthelyezése a helyszíni és a felhő között](data-factory-move-data-between-onprem-and-cloud.md) .

Az alábbi hitelesítési típusok támogatottak:

* A **Felhőbeli** OData-hírcsatornák eléréséhez használhat névtelen, alapszintű (Felhasználónév és jelszó) vagy Azure Active Directory alapú OAuth-hitelesítést.
* A helyszíni OData **-** hírcsatornák eléréséhez használhat névtelen, alapszintű (Felhasználónév és jelszó) vagy Windows-hitelesítést.

## <a name="getting-started"></a>Első lépések
Létrehozhat egy másolási tevékenységgel rendelkező folyamatot, amely különböző eszközök/API-k használatával helyez át egy OData-forrásból származó adatokkal.

A folyamat létrehozásának legegyszerűbb módja a **Másolás varázsló** használata. Tekintse meg az [oktatóanyag: folyamat létrehozása a másolás varázslóval](data-factory-copy-data-wizard-tutorial.md) című témakört, amely gyors áttekintést nyújt a folyamat létrehozásáról az adatmásolási varázsló használatával.

A következő eszközöket is használhatja a folyamat létrehozásához: **Visual Studio**, **Azure PowerShell**, **Azure Resource Manager template**, **.NET API** és **REST API**. A másolási tevékenységgel rendelkező folyamat létrehozásával kapcsolatos részletes utasításokat a [másolási tevékenységről szóló oktatóanyagban](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) talál.

Függetlenül attól, hogy az eszközöket vagy API-kat használja, a következő lépések végrehajtásával hozhat létre egy folyamatot, amely egy forrás adattárból egy fogadó adattárba helyezi át az adatait:

1. **Társított szolgáltatások** létrehozása a bemeneti és kimeneti adattáraknak az adat-előállítóhoz való összekapcsolásához.
2. Hozzon létre **adatkészleteket** a másolási művelet bemeneti és kimeneti adatok ábrázolásához.
3. Hozzon **létre egy másolási tevékenységgel rendelkező folyamatot** , amely egy adatkészletet bemenetként és egy adatkészlet kimenetként való elvégzéséhez szükséges.

A varázsló használatakor a rendszer automatikusan létrehozza a Data Factory entitások (társított szolgáltatások, adatkészletek és a folyamat) JSON-definícióit. Ha eszközöket/API-kat használ (kivéve a .NET API-t), akkor ezeket a Data Factory entitásokat JSON-formátumban kell megadnia.  A OData-forrásokból származó adatok másolásához használt Data Factory JSON-definíciókkal rendelkező minta esetében lásd a jelen cikk [JSON-példa: adatok másolása a OData forrásból az Azure blobba](#json-example-copy-data-from-odata-source-to-azure-blob) című szakaszát.

A következő szakaszokban részletesen ismertetjük a OData-forrásra jellemző Data Factory-entitások definiálásához használt JSON-tulajdonságokat:

## <a name="linked-service-properties"></a>Társított szolgáltatás tulajdonságai
A következő táblázat a OData társított szolgáltatáshoz tartozó JSON-elemek leírását tartalmazza.

| Tulajdonság | Leírás | Kötelező |
| --- | --- | --- |
| típus |A Type tulajdonságot a következőre kell beállítani: **OData** |Yes |
| url |A OData szolgáltatás URL-címe. |Yes |
| authenticationType |A OData-forráshoz való kapcsolódáshoz használt hitelesítés típusa. <br/><br/> A Felhőbeli OData a lehetséges értékek a következők: névtelen, alapszintű és OAuth (Megjegyzés Azure Data Factory jelenleg csak Azure Active Directory-alapú OAuth-t támogatnak). <br/><br/> A helyszíni OData a lehetséges értékek a következők: névtelen, alapszintű és Windows. |Yes |
| username |Ha alapszintű hitelesítést használ, adja meg a felhasználónevet. |Igen (csak akkor, ha alapszintű hitelesítést használ) |
| jelszó |Adja meg a felhasználónévhez megadott felhasználói fiókhoz tartozó jelszót. |Igen (csak akkor, ha alapszintű hitelesítést használ) |
| authorizedCredential |Ha OAuth használ, kattintson az **Engedélyezés** gombra a Data Factory másolás varázslóban vagy a szerkesztőben, és adja meg a hitelesítő adatait, majd a tulajdonság értékét automatikusan létrehozza a rendszer. |Igen (csak OAuth-hitelesítés használata esetén) |
| Átjáró neve |Annak az átjárónak a neve, amelyet a Data Factory szolgáltatásnak használnia kell a helyszíni OData szolgáltatáshoz való kapcsolódáshoz. Csak akkor kell megadni, ha a helyszíni OData forrásból másol Adatmásolást. |No |

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

### <a name="using-anonymous-authentication"></a>Névtelen hitelesítés használata
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

### <a name="using-windows-authentication-accessing-on-premises-odata-source"></a>A helyszíni OData-forráshoz való hozzáférés Windows-hitelesítés használata
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

### <a name="using-oauth-authentication-accessing-cloud-odata-source"></a>OAuth-hitelesítés használata a felhőalapú OData-forráshoz való hozzáféréshez
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
Az adatkészletek definiálásához rendelkezésre álló & tulajdonságok teljes listáját az [adatkészletek létrehozása](data-factory-create-datasets.md) című cikkben találja. Az adathalmazok (például a struktúra, a rendelkezésre állás és a szabályzat) minden adatkészlet esetében hasonlóak (például az Azure SQL, az Azure Blob, az Azure Table stb.).

A **typeProperties** szakasz különbözik az egyes adatkészletek típusaitól, és információt nyújt az adattárban található adatok helyéről. A **ODataResource** típusú (OData-adatkészletet tartalmazó) adatkészlet typeProperties szakasza a következő tulajdonságokkal rendelkezik

| Tulajdonság | Leírás | Kötelező |
| --- | --- | --- |
| path |A OData erőforrás elérési útja |No |

## <a name="copy-activity-properties"></a>Másolási tevékenység tulajdonságai
A tevékenységek definiálásához elérhető & tulajdonságok teljes listáját a [folyamatok létrehozása](data-factory-create-pipelines.md) című cikkben találja. A tulajdonságok, például a név, a leírás, a bemeneti és a kimeneti táblák, valamint a szabályzatok minden típusú tevékenységhez elérhetők.

A tevékenység typeProperties szakaszában elérhető tulajdonságok az egyes tevékenységtípusok esetében eltérőek. Másolási tevékenység esetén a források és a nyelők típusaitól függően változnak.

Ha a forrás típusa **RelationalSource** (beleértve a OData is), a typeProperties szakaszban a következő tulajdonságok érhetők el:

| Tulajdonság | Leírás | Példa | Kötelező |
| --- | --- | --- | --- |
| lekérdezés |Az egyéni lekérdezés használatával olvashatja el az adatolvasást. |"? $select = név, leírás&$top = 5" |No |

## <a name="type-mapping-for-odata"></a>Típus leképezése OData
Ahogy azt az [adattovábbítási tevékenységek](data-factory-data-movement-activities.md) című cikk ismerteti, a másolási tevékenység az alábbi kétlépéses megközelítéssel hajtja végre az automatikus típus-konverziókat a forrás típusairól a fogadó típusokba.

1. Konvertálás natív forrásokból .NET-típusra
2. Konvertálás .NET-típusról natív fogadó típusra

Az adatok OData való áthelyezésekor a rendszer a következő leképezéseket használja a OData típusokból a .NET típusra.

| OData-adatok típusa | .NET-típus |
| --- | --- |
| EDM. Binary |Bájt [] |
| Edm.Boolean |Logikai |
| EDM. byte |Bájt [] |
| EDM. DateTime |DateTime |
| EDM. decimális |Tizedesjegy |
| Edm.Double |Dupla |
| EDM. Single |Egyirányú |
| EDM. GUID |Guid |
| EDM. Int16 |Int16 |
| Edm.Int32 |Int32 |
| Edm.Int64 |Int64 |
| EDM. sbyte érték |Int16 |
| Edm.String |Sztring |
| EDM. Time |időtartam |
| Edm.DateTimeOffset |DateTimeOffset |

> [!Note]
> Az összetett adattípusok (például az objektum) nem támogatottak a OData.

## <a name="json-example-copy-data-from-odata-source-to-azure-blob"></a>JSON-példa: adatok másolása a OData forrásból az Azure Blobba
Ez a példa JSON-definíciókat tartalmaz, amelyek segítségével a [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) vagy a [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md)használatával hozhat létre folyamatokat. Bemutatják, hogyan másolhatók adatok egy OData-forrásból egy Azure-Blob Storageba. Az adatmásolási művelet azonban az [itt](data-factory-data-movement-activities.md#supported-data-stores-and-formats) megadott összes mosogatóba átmásolható a Azure Data Factoryban. A mintában a következő Data Factory entitások vannak:

1. [OData](#linked-service-properties)típusú társított szolgáltatás.
2. [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties)típusú társított szolgáltatás.
3. [ODataResource](#dataset-properties)típusú bemeneti [adatkészlet](data-factory-create-datasets.md) .
4. [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties)típusú kimeneti [adatkészlet](data-factory-create-datasets.md) .
5. [RelationalSource](#copy-activity-properties) és [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties)használó másolási tevékenységgel rendelkező [folyamat](data-factory-create-pipelines.md) .

A minta adatok másolása egy OData-forrásról egy Azure-blobba óránként történik. Az ezekben a mintákban használt JSON-tulajdonságokat a mintákat követő szakaszokban ismertetjük.

**OData társított szolgáltatás:** Ez a példa a névtelen hitelesítést használja. A használható hitelesítés különböző típusaival kapcsolatban lásd: [OData társított szolgáltatás](#linked-service-properties) szakasza.

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

**Azure Storage-beli társított szolgáltatás:**

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

**OData bemeneti adatkészlete:**

A "külső": "true" beállítás azt tájékoztatja a Data Factory szolgáltatást, hogy az adatkészlet kívül esik az adat-előállítón, és nem az adat-előállító tevékenysége.

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

Az **elérési út** megadása az adatkészlet definíciójában nem kötelező.

**Azure-Blob kimeneti adatkészlete:**

A rendszer óránként egy új blobba írja az adatbevitelt (frekvencia: óra, intervallum: 1). A blob mappájának elérési útját a rendszer dinamikusan kiértékeli a feldolgozás alatt álló szelet kezdési időpontja alapján. A mappa elérési útja a kezdési idő év, hónap, nap és óra részét használja.

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

**Másolási tevékenység egy folyamatba OData forrással és blob-fogadóval:**

A folyamat egy másolási tevékenységet tartalmaz, amely a bemeneti és a kimeneti adatkészletek használatára van konfigurálva, és óránkénti futásra van ütemezve. A folyamat JSON-definíciójában a **forrás** típusa **RelationalSource** értékre van állítva, a **fogadó típusa** pedig **BlobSink**. A **lekérdezési** tulajdonsághoz megadott SQL-lekérdezés a legújabb (legújabb) adatok kiválasztását választja ki a OData forrásból.

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

A **lekérdezés** megadása a folyamat definíciójában nem kötelező. A Data Factory szolgáltatás által az adat lekéréséhez használt **URL-cím** : a társított szolgáltatásban (kötelező) és az adatkészletben megadott elérési úton megadott URL-cím (opcionális) + lekérdezés a folyamatban (nem kötelező).

### <a name="type-mapping-for-odata"></a>Típus leképezése OData
Ahogy azt az [adattovábbítási tevékenységek](data-factory-data-movement-activities.md) című cikk ismerteti, a másolási tevékenység az alábbi kétlépéses megközelítéssel hajtja végre az automatikus típus-konverziókat a forrás típusairól a fogadó típusokra:

1. Konvertálás natív forrásokból .NET-típusra
2. Konvertálás .NET-típusról natív fogadó típusra

Az adatok OData-adattárból való áthelyezésekor a OData-adattípusok .NET-típusokra vannak leképezve.

## <a name="map-source-to-sink-columns"></a>Forrás leképezése a fogadó oszlopokra
A forrás adatkészletben lévő oszlopok a fogadó adatkészlet oszlopaihoz való leképezésével kapcsolatos további tudnivalókért lásd: [adatkészlet oszlopainak leképezése Azure Data Factoryban](data-factory-map-columns.md).

## <a name="repeatable-read-from-relational-sources"></a>Megismételhető olvasás a rokon forrásokból
Az adatok a kapcsolódó adattárakból való másolása során érdemes megismételni a nem kívánt eredmények elkerülését. Azure Data Factory a szeleteket manuálisan is újra futtathatja. Az újrapróbálkozási szabályzatot is konfigurálhatja egy adatkészlethez, hogy a rendszer hiba esetén újrafuttassa a szeleteket. Ha egy szeletet mindkét módon újrafuttat, meg kell győződnie arról, hogy a szeletek hányszor futnak. Lásd: [megismételhető olvasás a rokon forrásokból](data-factory-repeatable-copy.md#repeatable-read-from-relational-sources).

## <a name="performance-and-tuning"></a>Teljesítmény és hangolás
A [másolási tevékenység teljesítményének & hangolási útmutatójában](data-factory-copy-activity-performance.md) megismerheti azokat a főbb tényezőket, amelyek hatással vannak az adatáthelyezés (másolási tevékenység) teljesítményére Azure Data Factory és az optimalizálás különféle módjaival.
