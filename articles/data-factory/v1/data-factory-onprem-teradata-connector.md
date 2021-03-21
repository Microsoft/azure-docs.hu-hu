---
title: Adatok áthelyezése a Teradata a Azure Data Factory használatával
description: Ismerkedjen meg a Data Factory szolgáltatás Teradata-összekötővel, amely lehetővé teszi az adatok áthelyezését a Teradata-adatbázisból
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: ef992ed907bc070643f290e7fd536de05ebf9242
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "100387206"
---
# <a name="move-data-from-teradata-using-azure-data-factory"></a>Adatok áthelyezése a Teradata a Azure Data Factory használatával
> [!div class="op_single_selector" title1="Válassza ki az Ön által használt Data Factory-szolgáltatás verzióját:"]
> * [1-es verzió](data-factory-onprem-teradata-connector.md)
> * [2-es verzió (aktuális verzió)](../connector-teradata.md)

> [!NOTE]
> Ez a cikk a Data Factory 1-es verziójára vonatkozik. Ha a Data Factory szolgáltatás aktuális verzióját használja, tekintse meg a [Teradata-összekötőt a v2-ben](../connector-teradata.md).

Ez a cikk azt ismerteti, hogyan használható a másolási tevékenység a Azure Data Factoryban az adatok egy helyszíni Teradata-adatbázisból való áthelyezéséhez. Az [adattovábbítási tevékenységekről](data-factory-data-movement-activities.md) szóló cikkre épül, amely általános áttekintést nyújt az adatáthelyezésről a másolási tevékenységgel.

A helyszíni Teradata-adattárból bármely támogatott fogadó adattárba másolhat adatok. A másolási tevékenység által mosogatóként támogatott adattárak listáját a [támogatott adattárak](data-factory-data-movement-activities.md#supported-data-stores-and-formats) táblázatban tekintheti meg. A Teradata-adattár jelenleg csak az adatok áthelyezését támogatja más adattárakba, de az adatok más adattárakból a Teradata-adattárba való áthelyezésére nem.

## <a name="prerequisites"></a>Előfeltételek
A (z) adatfeldolgozó támogatja a helyszíni Teradata-forrásokhoz való kapcsolódást a adatkezelés átjárón keresztül. Az átjáró beállításával adatkezelés kapcsolatos további információkért lásd: az [adatáthelyezés a helyszíni helyszínek és a felhő között](data-factory-move-data-between-onprem-and-cloud.md) .

Az átjáróra akkor is szükség van, ha a Teradata egy Azure IaaS virtuális gépen fut. Az átjárót ugyanarra a IaaS virtuális gépre telepítheti, mint az adattár vagy egy másik virtuális gép, feltéve, hogy az átjáró csatlakozni tud az adatbázishoz.

> [!NOTE]
> A kapcsolat/átjáróval kapcsolatos problémák elhárításához kapcsolódó tippekért lásd: [átjárókkal kapcsolatos problémák elhárítása](data-factory-data-management-gateway.md#troubleshooting-gateway-issues) .

## <a name="supported-versions-and-installation"></a>Támogatott verziók és telepítés
Ahhoz, hogy adatkezelés átjáró csatlakozni tudjanak a Teradata-adatbázishoz, telepítenie kell a [.net-adatszolgáltatót a Teradata](https://go.microsoft.com/fwlink/?LinkId=278886) 14-es vagy újabb verziójára a adatkezelés átjáróval megegyező rendszeren. A Teradata 12-es és újabb verziója támogatott.

## <a name="getting-started"></a>Első lépések
Létrehozhat egy másolási tevékenységgel rendelkező folyamatot, amely különböző eszközök/API-k használatával helyez át egy helyszíni Cassandra-adattárból származó adatokkal.

- A folyamat létrehozásának legegyszerűbb módja a **Másolás varázsló** használata. Tekintse meg az [oktatóanyag: folyamat létrehozása a másolás varázslóval](data-factory-copy-data-wizard-tutorial.md) című témakört, amely gyors áttekintést nyújt a folyamat létrehozásáról az adatmásolási varázsló használatával.
- A következő eszközöket is használhatja a folyamat létrehozásához: **Visual Studio**, **Azure PowerShell**, **Azure Resource Manager template**, **.NET API** és **REST API**. A másolási tevékenységgel rendelkező folyamat létrehozásával kapcsolatos részletes utasításokat a [másolási tevékenységről szóló oktatóanyagban](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) talál.

Függetlenül attól, hogy az eszközöket vagy API-kat használja, a következő lépések végrehajtásával hozhat létre egy folyamatot, amely egy forrás adattárból egy fogadó adattárba helyezi át az adatait:

1. **Társított szolgáltatások** létrehozása a bemeneti és kimeneti adattáraknak az adat-előállítóhoz való összekapcsolásához.
2. Hozzon létre **adatkészleteket** a másolási művelet bemeneti és kimeneti adatok ábrázolásához.
3. Hozzon **létre egy másolási tevékenységgel rendelkező folyamatot** , amely egy adatkészletet bemenetként és egy adatkészlet kimenetként való elvégzéséhez szükséges.

A varázsló használatakor a rendszer automatikusan létrehozza a Data Factory entitások (társított szolgáltatások, adatkészletek és a folyamat) JSON-definícióit. Ha eszközöket/API-kat használ (kivéve a .NET API-t), akkor ezeket a Data Factory entitásokat JSON-formátumban kell megadnia.  A helyszíni Teradata-adattárakból származó adatok másolásához használt Data Factory JSON-definíciókkal rendelkező minta esetében lásd a jelen cikk [JSON-példa: adatok másolása a Teradata-ből az Azure blobba](#json-example-copy-data-from-teradata-to-azure-blob) című szakaszát.

A következő szakaszokban részletesen ismertetjük a Teradata-adattárra jellemző Data Factory entitások definiálásához használt JSON-tulajdonságokat:

## <a name="linked-service-properties"></a>Társított szolgáltatás tulajdonságai
A következő táblázat a Teradata társított szolgáltatáshoz tartozó JSON-elemek leírását tartalmazza.

| Tulajdonság | Leírás | Kötelező |
| --- | --- | --- |
| típus |A Type tulajdonságot a következőre kell beállítani: **OnPremisesTeradata** |Yes |
| kiszolgáló |A Teradata-kiszolgáló neve. |Yes |
| authenticationType |A Teradata-adatbázishoz való kapcsolódáshoz használt hitelesítés típusa. A lehetséges értékek a következők: névtelen, alapszintű és Windows. |Yes |
| username |Ha alapszintű vagy Windows-hitelesítést használ, adja meg a felhasználónevet. |No |
| jelszó |Adja meg a felhasználónévhez megadott felhasználói fiókhoz tartozó jelszót. |No |
| Átjáró neve |Annak az átjárónak a neve, amelyet a Data Factory szolgáltatásnak használnia kell a helyszíni Teradata-adatbázishoz való kapcsolódáshoz. |Yes |

## <a name="dataset-properties"></a>Adatkészlet tulajdonságai
Az adatkészletek definiálásához rendelkezésre álló & tulajdonságok teljes listáját az [adatkészletek létrehozása](data-factory-create-datasets.md) című cikkben találja. Az adathalmazok (például a struktúra, a rendelkezésre állás és a szabályzat) minden adatkészlet esetében hasonlóak (például az Azure SQL, az Azure Blob, az Azure Table stb.).

A **typeProperties** szakasz különbözik az egyes adatkészletek típusaitól, és információt nyújt az adattárban található adatok helyéről. Jelenleg nem támogatottak a Teradata adatkészlet típusának tulajdonságai.

## <a name="copy-activity-properties"></a>Másolási tevékenység tulajdonságai
A tevékenységek definiálásához elérhető & tulajdonságok teljes listáját a [folyamatok létrehozása](data-factory-create-pipelines.md) című cikkben találja. A tulajdonságok, például a név, a leírás, a bemeneti és a kimeneti táblák, valamint a házirendek minden típusú tevékenységhez elérhetők.

Míg a tevékenység typeProperties szakaszában elérhető tulajdonságok az egyes tevékenységtípusok esetében eltérőek. Másolási tevékenység esetén a források és a nyelők típusaitól függően változnak.

Ha a forrás **RelationalSource** típusú (amely magában foglalja a Teradata), a **typeProperties** szakaszban a következő tulajdonságok érhetők el:

| Tulajdonság | Leírás | Megengedett értékek | Kötelező |
| --- | --- | --- | --- |
| lekérdezés |Az egyéni lekérdezés használatával olvashatja el az adatolvasást. |SQL-lekérdezési karakterlánc. Például: select * from Sajáttábla. |Yes |

### <a name="json-example-copy-data-from-teradata-to-azure-blob"></a>JSON-példa: adatok másolása a Teradata-ből az Azure-Blobba
Az alábbi példa olyan JSON-definíciókat tartalmaz, amelyeket a [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) vagy a [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md)használatával hozhat létre a folyamat létrehozásához. Bemutatják, hogyan másolhatók adatok a Teradata-ből az Azure-Blob Storageba. Az adatmásolási művelet azonban az [itt](data-factory-data-movement-activities.md#supported-data-stores-and-formats) megadott összes mosogatóba átmásolható a Azure Data Factoryban.

A minta a következő adatgyári entitásokat tartalmazhatja:

1. [OnPremisesTeradata](#linked-service-properties)típusú társított szolgáltatás.
2. [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties)típusú társított szolgáltatás.
3. [RelationalTable](#dataset-properties)típusú bemeneti [adatkészlet](data-factory-create-datasets.md) .
4. [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties)típusú kimeneti [adatkészlet](data-factory-create-datasets.md) .
5. A [RelationalSource](#copy-activity-properties) és [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties)használó másolási tevékenységgel rendelkező [folyamat](data-factory-create-pipelines.md) .

A minta egy lekérdezési eredményből másolja az adatait a Teradata-adatbázisba óránként egy blobba. Az ezekben a mintákban használt JSON-tulajdonságokat a mintákat követő szakaszokban ismertetjük.

Első lépésként állítsa be az adatkezelési átjárót. Az utasítások a helyszíni [helyszínek és a felhő közötti adatáthelyezést](data-factory-move-data-between-onprem-and-cloud.md) ismertetik.

**Teradata társított szolgáltatás:**

```json
{
    "name": "OnPremTeradataLinkedService",
    "properties": {
        "type": "OnPremisesTeradata",
        "typeProperties": {
            "server": "<server>",
            "authenticationType": "<authentication type>",
            "username": "<username>",
            "password": "<password>",
            "gatewayName": "<gatewayName>"
        }
    }
}
```

**Azure Blob Storage társított szolgáltatás:**

```json
{
    "name": "AzureStorageLinkedService",
    "properties": {
        "type": "AzureStorageLinkedService",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<AccountName>;AccountKey=<AccountKey>"
        }
    }
}
```

**Teradata bemeneti adatkészlete:**

A minta feltételezi, hogy létrehozott egy "Sajáttábla" táblát a Teradata-ben, és tartalmaz egy "Timestamp" nevű oszlopot az idősorozat-adatsorokhoz.

A "külső" beállítása: igaz értékkel tájékoztatja a Data Factory szolgáltatást arról, hogy a tábla kívül esik az adatelőállítón, és nem az adatelőállító tevékenysége.

```json
{
    "name": "TeradataDataSet",
    "properties": {
        "published": false,
        "type": "RelationalTable",
        "linkedServiceName": "OnPremTeradataLinkedService",
        "typeProperties": {
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true,
        "policy": {
            "externalData": {
                "retryInterval": "00:01:00",
                "retryTimeout": "00:10:00",
                "maximumRetry": 3
            }
        }
    }
}
```

**Azure-Blob kimeneti adatkészlete:**

A rendszer óránként egy új blobba írja az adatbevitelt (frekvencia: óra, intervallum: 1). A blob mappájának elérési útját a rendszer dinamikusan kiértékeli a feldolgozás alatt álló szelet kezdési időpontja alapján. A mappa elérési útja a kezdési idő év, hónap, nap és óra részét használja.

```json
{
    "name": "AzureBlobTeradataDataSet",
    "properties": {
        "published": false,
        "location": {
            "type": "AzureBlobLocation",
            "folderPath": "mycontainer/teradata/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
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
            ],
            "linkedServiceName": "AzureStorageLinkedService"
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        }
    }
}
```
**Másolási tevékenységgel rendelkező folyamat:**

A folyamat egy másolási tevékenységet tartalmaz, amely a bemeneti és a kimeneti adatkészletek használatára van konfigurálva, és óránkénti futásra van ütemezve. A folyamat JSON-definíciójában a **forrás** típusa **RelationalSource** értékre van állítva, a **fogadó típusa** pedig **BlobSink**. A **lekérdezési** tulajdonsághoz megadott SQL-lekérdezés a másoláshoz az elmúlt órában kijelöli az összes adatforrást.

```json
{
    "name": "CopyTeradataToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [
            {
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "RelationalSource",
                        "query": "$$Text.Format('select * from MyTable where timestamp >= \\'{0:yyyy-MM-ddTHH:mm:ss}\\' AND timestamp < \\'{1:yyyy-MM-ddTHH:mm:ss}\\'', SliceStart, SliceEnd)"
                    },
                    "sink": {
                        "type": "BlobSink",
                        "writeBatchSize": 0,
                        "writeBatchTimeout": "00:00:00"
                    }
                },
                "inputs": [
                    {
                        "name": "TeradataDataSet"
                    }
                ],
                "outputs": [
                    {
                        "name": "AzureBlobTeradataDataSet"
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
                "name": "TeradataToBlob"
            }
        ],
        "start": "2014-06-01T18:00:00Z",
        "end": "2014-06-01T19:00:00Z",
        "isPaused": false
    }
}
```
## <a name="type-mapping-for-teradata"></a>Típus leképezése Teradata
Ahogy azt az [adattovábbítási tevékenységek](data-factory-data-movement-activities.md) című cikkben említettük, a másolási tevékenység az alábbi kétlépéses megközelítéssel hajtja végre az automatikus típus-konverziókat a forrás típusairól a fogadó típusokra:

1. Konvertálás natív forrásokból .NET-típusra
2. Konvertálás .NET-típusról natív fogadó típusra

Az adatok Teradata való áthelyezésekor a rendszer a következő leképezéseket használja a Teradata típusról .NET-típusra.

| Teradata-adatbázis típusa | .NET-keretrendszer típusa |
| --- | --- |
| Char |Sztring |
| CLOB |Sztring |
| Grafikus |Sztring |
| VarChar |Sztring |
| VarGraphic |Sztring |
| Blob |Bájt [] |
| Bájt |Bájt [] |
| VarByte |Bájt [] |
| BigInt |Int64 |
| ByteInt |Int16 |
| Tizedesjegy |Tizedesjegy |
| Dupla |Dupla |
| Egész szám |Int32 |
| Szám |Dupla |
| SmallInt |Int16 |
| Date |Dátum/idő |
| Idő |időtartam |
| Időzónával ellátott idő |Sztring |
| Időbélyeg |DateTime |
| Időbélyeg időzónával |DateTimeOffset |
| Intervallum napja |időtartam |
| Nap és óra közötti időszak |időtartam |
| Időköz (nap és perc) |időtartam |
| Időintervallum – másodperc |időtartam |
| Intervallum óra |időtartam |
| Óra és perc közötti intervallum |időtartam |
| Óra és másodperc közötti intervallum |időtartam |
| Időköz (perc) |időtartam |
| Időköz (perc) – másodperc |időtartam |
| Másodperc intervalluma |időtartam |
| Intervallum éve |Sztring |
| Év és hónap közötti időszak |Sztring |
| Intervallum hónapja |Sztring |
| Időszak (dátum) |Sztring |
| Időszak (idő) |Sztring |
| Időtartam (idő zónával) |Sztring |
| Időszak (timestamp) |Sztring |
| Időtartam (időbélyegző az időzónával) |Sztring |
| Xml |Sztring |

## <a name="map-source-to-sink-columns"></a>Forrás leképezése a fogadó oszlopokra
A forrás adatkészletben lévő oszlopok a fogadó adatkészlet oszlopaihoz való leképezésével kapcsolatos további tudnivalókért lásd: [adatkészlet oszlopainak leképezése Azure Data Factoryban](data-factory-map-columns.md).

## <a name="repeatable-read-from-relational-sources"></a>Megismételhető olvasás a rokon forrásokból
Az adatok a kapcsolódó adattárakból való másolása során érdemes megismételni a nem kívánt eredmények elkerülését. Azure Data Factory a szeleteket manuálisan is újra futtathatja. Az újrapróbálkozási szabályzatot is konfigurálhatja egy adatkészlethez, hogy a rendszer hiba esetén újrafuttassa a szeleteket. Ha egy szeletet mindkét módon újrafuttat, meg kell győződnie arról, hogy a szeletek hányszor futnak. Lásd: [megismételhető olvasás a rokon forrásokból](data-factory-repeatable-copy.md#repeatable-read-from-relational-sources).

## <a name="performance-and-tuning"></a>Teljesítmény és hangolás
A [másolási tevékenység teljesítményének & hangolási útmutatójában](data-factory-copy-activity-performance.md) megismerheti azokat a főbb tényezőket, amelyek hatással vannak az adatáthelyezés (másolási tevékenység) teljesítményére Azure Data Factory és az optimalizálás különféle módjaival.
