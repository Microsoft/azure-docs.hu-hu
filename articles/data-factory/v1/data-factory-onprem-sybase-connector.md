---
title: Adatok áthelyezése a Sybase használatával Azure Data Factory
description: Ismerje meg, hogyan helyezheti át adatait a Sybase-adatbázisból a Azure Data Factory használatával.
author: linda33wj
ms.author: jingwang
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/02/2018
robots: noindex
ms.openlocfilehash: 8142ec51f93a066faafa1f3e44d0f87517187425
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "100387240"
---
# <a name="move-data-from-sybase-using-azure-data-factory"></a>Adatok áthelyezése a Sybase használatával Azure Data Factory
> [!div class="op_single_selector" title1="Válassza ki az Ön által használt Data Factory-szolgáltatás verzióját:"]
> * [1-es verzió](data-factory-onprem-sybase-connector.md)
> * [2-es verzió (aktuális verzió)](../connector-sybase.md)

> [!NOTE]
> Ez a cikk a Data Factory 1-es verziójára vonatkozik. Ha a Data Factory szolgáltatás aktuális verzióját használja, tekintse meg a [Sybase-összekötőt a v2-ben](../connector-sybase.md).

Ez a cikk azt ismerteti, hogyan használható a másolási tevékenység a Azure Data Factoryban az adatok helyszíni Sybase-adatbázisból való áthelyezéséhez. Az [adattovábbítási tevékenységekről](data-factory-data-movement-activities.md) szóló cikkre épül, amely általános áttekintést nyújt az adatáthelyezésről a másolási tevékenységgel.

A helyszíni Sybase-adattárból bármilyen támogatott fogadó adattárba másolhat adatok. A másolási tevékenység által mosogatóként támogatott adattárak listáját a [támogatott adattárak](data-factory-data-movement-activities.md#supported-data-stores-and-formats) táblázatban tekintheti meg. A adatfeldolgozó jelenleg csak egy Sybase-adattárból származó adatok áthelyezését támogatja más adattárakba, de az adatok más adattárakból egy Sybase-adattárba való áthelyezésére nem. 

## <a name="prerequisites"></a>Előfeltételek
A Data Factory szolgáltatás támogatja a helyszíni Sybase-forrásokhoz való kapcsolódást a adatkezelés átjáró használatával. Az átjáró beállításával adatkezelés kapcsolatos további információkért lásd: az [adatáthelyezés a helyszíni helyszínek és a felhő között](data-factory-move-data-between-onprem-and-cloud.md) .

Az átjáróra akkor is szükség van, ha a Sybase-adatbázis egy Azure IaaS-beli virtuális gépen fut. Az átjárót ugyanarra a IaaS virtuális gépre telepítheti, mint az adattár vagy egy másik virtuális gép, feltéve, hogy az átjáró csatlakozni tud az adatbázishoz.

> [!NOTE]
> A kapcsolat/átjáróval kapcsolatos problémák elhárításához kapcsolódó tippekért lásd: [átjárókkal kapcsolatos problémák elhárítása](data-factory-data-management-gateway.md#troubleshooting-gateway-issues) .

## <a name="supported-versions-and-installation"></a>Támogatott verziók és telepítés
Ahhoz, hogy adatkezelés átjáró csatlakozhasson a Sybase-adatbázishoz, telepítenie kell az [adatszolgáltatót a Sybase iAnywhere. SQLAnywhere](https://go.microsoft.com/fwlink/?linkid=324846) 16 vagy újabb verzióra a adatkezelés átjáróval megegyező rendszeren. 

Az SAP Sybase SQL Anywhere (ASA) 16. és újabb verziója támogatott; Az IQ és a bemutató nem támogatott.

## <a name="getting-started"></a>Első lépések
Létrehozhat egy másolási tevékenységgel rendelkező folyamatot, amely különböző eszközök/API-k használatával helyez át egy helyszíni Cassandra-adattárból származó adatokkal. 

- A folyamat létrehozásának legegyszerűbb módja a **Másolás varázsló** használata. Tekintse meg az [oktatóanyag: folyamat létrehozása a másolás varázslóval](data-factory-copy-data-wizard-tutorial.md) című témakört, amely gyors áttekintést nyújt a folyamat létrehozásáról az adatmásolási varázsló használatával. 
- A következő eszközöket is használhatja a folyamat létrehozásához: **Visual Studio**, **Azure PowerShell**, **Azure Resource Manager template**, **.NET API** és **REST API**. A másolási tevékenységgel rendelkező folyamat létrehozásával kapcsolatos részletes utasításokat a [másolási tevékenységről szóló oktatóanyagban](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) talál. 

Függetlenül attól, hogy az eszközöket vagy API-kat használja, a következő lépések végrehajtásával hozhat létre egy folyamatot, amely egy forrás adattárból egy fogadó adattárba helyezi át az adatait:

1. **Társított szolgáltatások** létrehozása a bemeneti és kimeneti adattáraknak az adat-előállítóhoz való összekapcsolásához.
2. Hozzon létre **adatkészleteket** a másolási művelet bemeneti és kimeneti adatok ábrázolásához. 
3. Hozzon **létre egy másolási tevékenységgel rendelkező folyamatot** , amely egy adatkészletet bemenetként és egy adatkészlet kimenetként való elvégzéséhez szükséges. 

A varázsló használatakor a rendszer automatikusan létrehozza a Data Factory entitások (társított szolgáltatások, adatkészletek és a folyamat) JSON-definícióit. Ha eszközöket/API-kat használ (kivéve a .NET API-t), akkor ezeket a Data Factory entitásokat JSON-formátumban kell megadnia.  A helyszíni Sybase-adattárakból származó adatok másolásához használt Data Factory JSON-definíciókkal rendelkező minta esetében lásd a [JSON-példa: adatok másolása a Sybase-ből az Azure blobba](#json-example-copy-data-from-sybase-to-azure-blob) című szakaszt a jelen cikkből. 

A következő szakaszokban részletesen ismertetjük a Sybase-adattárra jellemző Data Factory entitások definiálásához használt JSON-tulajdonságokat:

## <a name="linked-service-properties"></a>Társított szolgáltatás tulajdonságai
A következő táblázat a Sybase-alapú társított szolgáltatáshoz tartozó JSON-elemek leírását tartalmazza.

| Tulajdonság | Leírás | Kötelező |
| --- | --- | --- |
| típus |A Type tulajdonságot a következőre kell beállítani: **OnPremisesSybase** |Yes |
| kiszolgáló |A Sybase-kiszolgáló neve. |Yes |
| adatbázis |A Sybase-adatbázis neve. |Yes |
| schema |A séma neve az adatbázisban. |No |
| authenticationType |A Sybase-adatbázishoz való kapcsolódáshoz használt hitelesítés típusa. A lehetséges értékek a következők: névtelen, alapszintű és Windows. |Yes |
| username |Ha alapszintű vagy Windows-hitelesítést használ, adja meg a felhasználónevet. |No |
| jelszó |Adja meg a felhasználónévhez megadott felhasználói fiókhoz tartozó jelszót. |No |
| Átjáró neve |Annak az átjárónak a neve, amelyet a Data Factory szolgáltatásnak használnia kell a helyszíni Sybase-adatbázishoz való kapcsolódáshoz. |Yes |

## <a name="dataset-properties"></a>Adatkészlet tulajdonságai
Az adatkészletek definiálásához rendelkezésre álló & tulajdonságok teljes listáját az [adatkészletek létrehozása](data-factory-create-datasets.md) című cikkben találja. Az adathalmazok (például a struktúra, a rendelkezésre állás és a szabályzat) minden adatkészlet esetében hasonlóak (például az Azure SQL, az Azure Blob, az Azure Table stb.).

A typeProperties szakasz különbözik az egyes adatkészletek típusaitól, és információt nyújt az adattárban található adatok helyéről. A **RelationalTable** típusú (Sybase-adatkészletet tartalmazó) adatkészlet **typeProperties** szakasza a következő tulajdonságokkal rendelkezik:

| Tulajdonság | Leírás | Kötelező |
| --- | --- | --- |
| tableName |Annak a táblának a neve, amelyre a társított szolgáltatás hivatkozik a Sybase-adatbázis példányában. |Nem (ha meg van adva a **RelationalSource** **lekérdezése** ) |

## <a name="copy-activity-properties"></a>Másolási tevékenység tulajdonságai
A tevékenységek meghatározására szolgáló & tulajdonságok teljes listáját a [folyamatok létrehozásával](data-factory-create-pipelines.md) foglalkozó cikkben találja. A tulajdonságok, például a név, a leírás, a bemeneti és a kimeneti táblák, valamint a szabályzatok minden típusú tevékenységhez elérhetők.

Míg a tevékenység typeProperties szakaszában elérhető tulajdonságok az egyes tevékenységtípusok esetében eltérőek. Másolási tevékenység esetén a források és a nyelők típusaitól függően változnak.

Ha a forrás **RelationalSource** típusú (amely magában foglalja a Sybase-t), a következő tulajdonságok érhetők el a **typeProperties** szakaszban:

| Tulajdonság | Leírás | Megengedett értékek | Kötelező |
| --- | --- | --- | --- |
| lekérdezés |Az egyéni lekérdezés használatával olvashatja el az adatolvasást. |SQL-lekérdezési karakterlánc. Például: select * from Sajáttábla. |Nem (ha meg van adva az **adatkészlet** **Táblanév** ) |


## <a name="json-example-copy-data-from-sybase-to-azure-blob"></a>JSON-példa: adatok másolása a Sybase-ből az Azure Blobba
Az alábbi példa olyan JSON-definíciókat tartalmaz, amelyeket a [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) vagy a [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md)használatával hozhat létre a folyamat létrehozásához. Bemutatják, hogyan másolhatók adatok a Sybase-adatbázisból az Azure Blob Storageba. Az adatmásolási művelet azonban az [itt](data-factory-data-movement-activities.md#supported-data-stores-and-formats) megadott összes mosogatóba átmásolható a Azure Data Factoryban.   

A minta a következő adatgyári entitásokat tartalmazhatja:

1. [OnPremisesSybase](data-factory-onprem-sybase-connector.md#linked-service-properties)típusú társított szolgáltatás.
2. [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties)típusú, kedvelt szolgáltatás.
3. [RelationalTable](data-factory-onprem-sybase-connector.md#dataset-properties)típusú bemeneti [adatkészlet](data-factory-create-datasets.md) .
4. [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties)típusú kimeneti [adatkészlet](data-factory-create-datasets.md) .
5. A [RelationalSource](data-factory-onprem-sybase-connector.md#copy-activity-properties) és [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties)használó másolási tevékenységgel rendelkező [folyamat](data-factory-create-pipelines.md) .

A minta minden órában átmásolja a Sybase-adatbázis lekérdezési eredményeiből származó adatok adatait egy blobba. Az ezekben a mintákban használt JSON-tulajdonságokat a mintákat követő szakaszokban ismertetjük.

Első lépésként állítsa be az adatkezelési átjárót. Az utasítások a helyszíni [helyszínek és a felhő közötti adatáthelyezést](data-factory-move-data-between-onprem-and-cloud.md) ismertetik.

**Sybase-társított szolgáltatás:**

```JSON
{
    "name": "OnPremSybaseLinkedService",
    "properties": {
        "type": "OnPremisesSybase",
        "typeProperties": {
            "server": "<server>",
            "database": "<database>",
            "schema": "<schema>",
            "authenticationType": "<authentication type>",
            "username": "<username>",
            "password": "<password>",
            "gatewayName": "<gatewayName>"
        }
    }
}
```

**Azure Blob Storage társított szolgáltatás:**

```JSON
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

**Sybase bemeneti adatkészlete:**

A minta feltételezi, hogy létrehozott egy "Sajáttábla" táblát a Sybase-ben, és tartalmaz egy "Timestamp" nevű oszlopot az idősorozat-adatsorokhoz.

A "külső" beállítása: a true értékkel tájékoztatja a Data Factory szolgáltatást, amely az adatkészletnek az adat-előállítón kívül esik, és nem az adat-előállító tevékenysége állítja elő. Figyelje meg, hogy a társított szolgáltatás **típusa a következő** : **RelationalTable**.

```JSON
{
    "name": "SybaseDataSet",
    "properties": {
        "type": "RelationalTable",
        "linkedServiceName": "OnPremSybaseLinkedService",
        "typeProperties": {},
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

```JSON
{
    "name": "AzureBlobSybaseDataSet",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties": {
            "folderPath": "mycontainer/sybase/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
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

**Másolási tevékenységgel rendelkező folyamat:**

A folyamat egy másolási tevékenységet tartalmaz, amely a bemeneti és a kimeneti adatkészletek használatára van konfigurálva, és óránkénti futásra van ütemezve. A folyamat JSON-definíciójában a **forrás** típusa **RelationalSource** értékre van állítva, a **fogadó típusa** pedig **BlobSink**. A **lekérdezés** tulajdonsághoz megadott SQL-lekérdezés a DBA-ből kiválasztja az adatait. Orders tábla az adatbázisban.

```JSON
{
    "name": "CopySybaseToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [
            {
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "RelationalSource",
                        "query": "select * from DBA.Orders"
                    },
                    "sink": {
                        "type": "BlobSink"
                    }
                },
                "inputs": [
                    {
                        "name": "SybaseDataSet"
                    }
                ],
                "outputs": [
                    {
                        "name": "AzureBlobSybaseDataSet"
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
                "name": "SybaseToBlob"
            }
        ],
        "start": "2014-06-01T18:00:00Z",
        "end": "2014-06-01T19:00:00Z"
    }
}
```

## <a name="type-mapping-for-sybase"></a>Típus leképezése a Sybase számára
Ahogy azt az [adattovábbítási tevékenységek](data-factory-data-movement-activities.md) című cikkben említettük, a másolási tevékenység az alábbi kétlépéses megközelítéssel hajtja végre az automatikus típus-konverziókat a forrás típusairól a fogadó típusokra:

1. Konvertálás natív forrásokból .NET-típusra
2. Konvertálás .NET-típusról natív fogadó típusra

A Sybase támogatja a T-SQL és a T-SQL típusokat. Az SQL types és a .NET típus közötti leképezési táblázat az [Azure SQL Connector](data-factory-azure-sql-connector.md) című cikkben található.

## <a name="map-source-to-sink-columns"></a>Forrás leképezése a fogadó oszlopokra
A forrás adatkészletben lévő oszlopok a fogadó adatkészlet oszlopaihoz való leképezésével kapcsolatos további tudnivalókért lásd: [adatkészlet oszlopainak leképezése Azure Data Factoryban](data-factory-map-columns.md).

## <a name="repeatable-read-from-relational-sources"></a>Megismételhető olvasás a rokon forrásokból
Az adatok a kapcsolódó adattárakból való másolása során érdemes megismételni a nem kívánt eredmények elkerülését. Azure Data Factory a szeleteket manuálisan is újra futtathatja. Az újrapróbálkozási szabályzatot is konfigurálhatja egy adatkészlethez, hogy a rendszer hiba esetén újrafuttassa a szeleteket. Ha egy szeletet mindkét módon újrafuttat, meg kell győződnie arról, hogy a szeletek hányszor futnak. Lásd: [megismételhető olvasás a rokon forrásokból](data-factory-repeatable-copy.md#repeatable-read-from-relational-sources).

## <a name="performance-and-tuning"></a>Teljesítmény és hangolás
A [másolási tevékenység teljesítményének & hangolási útmutatójában](data-factory-copy-activity-performance.md) megismerheti azokat a főbb tényezőket, amelyek hatással vannak az adatáthelyezés (másolási tevékenység) teljesítményére Azure Data Factory és az optimalizálás különféle módjaival.
