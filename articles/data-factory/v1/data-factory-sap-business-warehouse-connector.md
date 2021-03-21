---
title: Adatok áthelyezése az SAP Business Warehouse-ból Azure Data Factory használatával
description: Ismerje meg, hogyan helyezhet át adatátvitelt az SAP Business Warehouse-ból Azure Data Factory használatával.
author: linda33wj
ms.author: jingwang
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/10/2018
robots: noindex
ms.openlocfilehash: 513d6b28908b99594eaa525e86690fa75bffb103
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "100386968"
---
# <a name="move-data-from-sap-business-warehouse-using-azure-data-factory"></a>Adatok áthelyezése az SAP Business Warehouse-ból Azure Data Factory használatával
> [!div class="op_single_selector" title1="Válassza ki az Ön által használt Data Factory-szolgáltatás verzióját:"]
> * [1-es verzió](data-factory-sap-business-warehouse-connector.md)
> * [2-es verzió (aktuális verzió)](../connector-sap-business-warehouse.md)

> [!NOTE]
> Ez a cikk a Data Factory 1-es verziójára vonatkozik. Ha a Data Factory szolgáltatás aktuális verzióját használja, tekintse [meg az SAP Business Warehouse-összekötőt a v2-ben](../connector-sap-business-warehouse.md).


Ez a cikk azt ismerteti, hogyan használható a másolási tevékenység a Azure Data Factoryban az adatok helyszíni SAP Business Warehouse-ból (BW) való áthelyezéséhez. Az [adattovábbítási tevékenységekről](data-factory-data-movement-activities.md) szóló cikkre épül, amely általános áttekintést nyújt az adatáthelyezésről a másolási tevékenységgel.

Az adatok egy helyszíni SAP Business Warehouse-adattárból másolhatók bármely támogatott fogadó adattárba. A másolási tevékenység által mosogatóként támogatott adattárak listáját a [támogatott adattárak](data-factory-data-movement-activities.md#supported-data-stores-and-formats) táblázatban tekintheti meg. A adatfeldolgozó jelenleg csak az SAP Business Warehouse-ból származó adatok áthelyezését támogatja más adattárakba, az adatok más adattárakból egy SAP Business Warehouse-ba való áthelyezését azonban nem. 

## <a name="supported-versions-and-installation"></a>Támogatott verziók és telepítés
Ez az összekötő támogatja az SAP Business Warehouse 7. x verzióját. Támogatja az adatok másolását a InfoCubes és a QueryCubes (beleértve a kérelmeket) MDX-lekérdezések használatával.

A SAP BW-példányhoz való kapcsolódás engedélyezéséhez telepítse a következő összetevőket:
- **Adatkezelés átjáró**: Data Factory szolgáltatás támogatja a helyszíni adattárakhoz (beleértve az SAP Business Warehouse-hoz) való csatlakozást a adatkezelés Gateway nevű összetevő használatával. Ha többet szeretne megtudni az átjáró beállításáról adatkezelés átjáróról, és részletes útmutatást talál a helyszíni [adattárak közötti adatáthelyezés a felhőalapú adattárba](data-factory-move-data-between-onprem-and-cloud.md) című cikkben olvashat. Az átjáróra akkor is szükség van, ha az SAP Business Warehouse egy Azure IaaS virtuális gépen (VM) van tárolva. Az átjárót ugyanarra a virtuális gépre telepítheti, mint az adattár vagy egy másik virtuális gép, feltéve, hogy az átjáró csatlakozni tud az adatbázishoz.
- **SAP NetWeaver Library** az átjáró gépen. Az SAP NetWeaver kódtárat az SAP-rendszergazdától, vagy közvetlenül az [SAP Software Download Center](https://support.sap.com/swdc)webhelyről szerezheti be. Keresse meg az **SAP-megjegyzés #1025361** a legfrissebb verzió letöltési helyének lekéréséhez. Győződjön meg arról, hogy az SAP NetWeaver Library (32-bit vagy 64-bit) architektúrája megfelel az átjáró telepítésének. Ezután telepítse az SAP NetWeaver RFC SDK-ban található összes fájlt az SAP-Megjegyzés alapján. Az SAP NetWeaver könyvtárat az SAP-ügyféleszközök telepítése is tartalmazza.

> [!TIP]
> Helyezze a NetWeaver RFC SDK-ból kinyert DLL-eket a System32 mappába.

## <a name="getting-started"></a>Első lépések
Létrehozhat egy másolási tevékenységgel rendelkező folyamatot, amely különböző eszközök/API-k használatával helyez át egy helyszíni Cassandra-adattárból származó adatokkal. 

- A folyamat létrehozásának legegyszerűbb módja a **Másolás varázsló** használata. Tekintse meg az [oktatóanyag: folyamat létrehozása a másolás varázslóval](data-factory-copy-data-wizard-tutorial.md) című témakört, amely gyors áttekintést nyújt a folyamat létrehozásáról az adatmásolási varázsló használatával. 
- A következő eszközöket is használhatja a folyamat létrehozásához: **Visual Studio**, **Azure PowerShell**, **Azure Resource Manager template**, **.NET API** és **REST API**. A másolási tevékenységgel rendelkező folyamat létrehozásával kapcsolatos részletes utasításokat a [másolási tevékenységről szóló oktatóanyagban](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) talál. 

Függetlenül attól, hogy az eszközöket vagy API-kat használja, a következő lépések végrehajtásával hozhat létre egy folyamatot, amely egy forrás adattárból egy fogadó adattárba helyezi át az adatait:

1. **Társított szolgáltatások** létrehozása a bemeneti és kimeneti adattáraknak az adat-előállítóhoz való összekapcsolásához.
2. Hozzon létre **adatkészleteket** a másolási művelet bemeneti és kimeneti adatok ábrázolásához. 
3. Hozzon **létre egy másolási tevékenységgel rendelkező folyamatot** , amely egy adatkészletet bemenetként és egy adatkészlet kimenetként való elvégzéséhez szükséges. 

A varázsló használatakor a rendszer automatikusan létrehozza a Data Factory entitások (társított szolgáltatások, adatkészletek és a folyamat) JSON-definícióit. Ha eszközöket/API-kat használ (kivéve a .NET API-t), akkor ezeket a Data Factory entitásokat JSON-formátumban kell megadnia.  A helyszíni SAP Business Warehouse-ból származó adatok másolásához használt Data Factory JSON-definíciókkal rendelkező minta esetében lásd a [JSON-példa: adatok másolása az SAP Business Warehouse-ból az Azure blobba](#json-example-copy-data-from-sap-business-warehouse-to-azure-blob) című szakaszt a jelen cikkből. 

A következő szakaszokban részletesen ismertetjük a SAP BW adattárra jellemző Data Factory-entitások definiálásához használt JSON-tulajdonságokat:

## <a name="linked-service-properties"></a>Társított szolgáltatás tulajdonságai
Az alábbi táblázat az SAP Business Warehouse (BW) társított szolgáltatáshoz tartozó JSON-elemek leírását tartalmazza.

Tulajdonság | Leírás | Megengedett értékek | Kötelező
-------- | ----------- | -------------- | --------
kiszolgáló | Annak a kiszolgálónak a neve, amelyen az SAP BW-példány található. | sztring | Yes
systemNumber | A SAP BWrendszer rendszerszáma. | String típusú kétszámjegyű decimális szám. | Yes
ügyfél-azonosító | A-ügyfél ügyfél-azonosítója az SAP W rendszeren. | Karakterláncként megjelenített háromjegyű decimális szám. | Yes
username | Az SAP-kiszolgálóhoz hozzáféréssel rendelkező felhasználó neve | sztring | Yes
jelszó | A felhasználó jelszava. | sztring | Yes
Átjáró neve | Annak az átjárónak a neve, amelyet a Data Factory szolgáltatásnak használnia kell a helyszíni SAP BW-példányhoz való kapcsolódáshoz. | sztring | Yes
encryptedCredential | A titkosított hitelesítő adat karakterlánca. | sztring | No

## <a name="dataset-properties"></a>Adatkészlet tulajdonságai
Az adatkészletek definiálásához rendelkezésre álló & tulajdonságok teljes listáját az [adatkészletek létrehozása](data-factory-create-datasets.md) című cikkben találja. Az adathalmazok (például a struktúra, a rendelkezésre állás és a szabályzat) minden adatkészlet esetében hasonlóak (például az Azure SQL, az Azure Blob, az Azure Table stb.).

A **typeProperties** szakasz különbözik az egyes adatkészletek típusaitól, és információt nyújt az adattárban található adatok helyéről. Nincs olyan Type-specifikus tulajdonság, amely a **RelationalTable** típusú SAP BW adatkészlet esetében nem támogatott. 


## <a name="copy-activity-properties"></a>Másolási tevékenység tulajdonságai
A tevékenységek definiálásához elérhető & tulajdonságok teljes listáját a [folyamatok létrehozása](data-factory-create-pipelines.md) című cikkben találja. A tulajdonságok, például a név, a leírás, a bemeneti és a kimeneti táblák, a szabályzatok minden típusú tevékenységhez elérhetők.

Míg a tevékenység **typeProperties** szakaszában elérhető tulajdonságok az egyes tevékenységtípusok esetében eltérőek. Másolási tevékenység esetén a források és a nyelők típusaitól függően változnak.

Ha a másolási tevékenységben szereplő forrás **RelationalSource** típusú (amely magában foglalja a SAP BW), a következő tulajdonságok érhetők el a typeProperties szakaszban:

| Tulajdonság | Leírás | Megengedett értékek | Kötelező |
| --- | --- | --- | --- |
| lekérdezés | Megadja az MDX-lekérdezést, amely beolvassa az SAP BW-példány adatait. | MDX-lekérdezés. | Yes |


## <a name="json-example-copy-data-from-sap-business-warehouse-to-azure-blob"></a>JSON-példa: adatok másolása az SAP Business Warehouse-ból az Azure Blobba
Az alábbi példa olyan JSON-definíciókat tartalmaz, amelyeket a [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) vagy a [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md)használatával hozhat létre a folyamat létrehozásához. Ez a minta bemutatja, hogyan másolhat adatok egy helyszíni SAP Business Warehouse-ból egy Azure-Blob Storageba. Az Adatmásolás azonban **közvetlenül** az [itt](data-factory-data-movement-activities.md#supported-data-stores-and-formats) megadott, a Azure Data Factoryban található másolási tevékenység használatával másolhatók.  

> [!IMPORTANT]
> Ez a példa JSON-kódrészleteket biztosít. Nem tartalmaz részletes útmutatást az adatelőállító létrehozásához. Részletes útmutatásért lásd: az [adatáthelyezés a helyszíni helyszínek és a felhőalapú cikkek között](data-factory-move-data-between-onprem-and-cloud.md) .

A minta a következő adatgyári entitásokat tartalmazhatja:

1. [SapBw](#linked-service-properties)típusú társított szolgáltatás.
2. [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties)típusú társított szolgáltatás.
3. [RelationalTable](#dataset-properties)típusú bemeneti [adatkészlet](data-factory-create-datasets.md) .
4. [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties)típusú kimeneti [adatkészlet](data-factory-create-datasets.md) .
5. [RelationalSource](#copy-activity-properties) és [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties)használó másolási tevékenységgel rendelkező [folyamat](data-factory-create-pipelines.md) .

A minta egy SAP Business Warehouse-példány adatait másolja át óránként egy Azure-blobba. Az ezekben a mintákban használt JSON-tulajdonságokat a mintákat követő szakaszokban ismertetjük.

Első lépésként állítsa be az adatkezelési átjárót. Az utasítások a helyszíni [helyszínek és a felhő közötti adatáthelyezést](data-factory-move-data-between-onprem-and-cloud.md) ismertetik.

### <a name="sap-business-warehouse-linked-service"></a>SAP Business Warehouse társított szolgáltatás
Ez a társított szolgáltatás összekapcsolja SAP BW példányát az adatelőállítóval. A Type tulajdonság értéke **SapBw**. A typeProperties szakasz a SAP BW példányra vonatkozó kapcsolatok adatait tartalmazza. 

```json
{
    "name": "SapBwLinkedService",
    "properties":
    {
        "type": "SapBw",
        "typeProperties":
        {
            "server": "<server name>",
            "systemNumber": "<system number>",
            "clientId": "<client id>",
            "username": "<SAP user>",
            "password": "<Password for SAP user>",
            "gatewayName": "<gateway name>"
        }
    }
}
```

### <a name="azure-storage-linked-service"></a>Azure Storage társított szolgáltatás
Ez a társított szolgáltatás összeköti az Azure Storage-fiókját az adatelőállítóval. A Type tulajdonság értéke **AzureStorage**. A typeProperties szakasz az Azure Storage-fiókhoz tartozó kapcsolatok adatait tartalmazza.

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

### <a name="sap-bw-input-dataset"></a>Bemeneti adatkészlet SAP BW
Ez az adatkészlet határozza meg az SAP Business Warehouse-adatkészletet. A Data Factory adatkészlet típusát állítsa **RelationalTable** értékre. Jelenleg nem határoz meg egy SAP BW adatkészlet típus-specifikus tulajdonságait. A másolási tevékenység definíciójában szereplő lekérdezés meghatározza, hogy milyen adatok olvashatók a SAP BW-példányból. 

Ha a külső tulajdonságot True (igaz) értékre állítja, az a Data Factory szolgáltatás, amely a tábla kívül esik az adatgyárban, nem az adatelőállító tevékenysége.

A gyakoriság és az intervallum tulajdonságai határozzák meg az ütemtervet. Ebben az esetben az adatok beolvasása az SAP BW-példányból óránként történik. 

```json
{
    "name": "SapBwDataset",
    "properties": {
        "type": "RelationalTable",
        "linkedServiceName": "SapBwLinkedService",
        "typeProperties": {},
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true
    }
}
```



### <a name="azure-blob-output-dataset"></a>Azure Blob kimeneti adatkészlet
Ez az adatkészlet határozza meg a kimeneti Azure Blob-adatkészletet. A Type tulajdonság értéke AzureBlob. A typeProperties szakasz a SAP BW példányból másolt adatok tárolásának helyét adja meg. Az új blobba való írás minden órában történik (frekvencia: óra, intervallum: 1). A blob mappájának elérési útját a rendszer dinamikusan kiértékeli a feldolgozás alatt álló szelet kezdési időpontja alapján. A mappa elérési útja a kezdési idő év, hónap, nap és óra részét használja.

```json
{
    "name": "AzureBlobDataSet",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties": {
            "folderPath": "mycontainer/sapbw/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
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


### <a name="pipeline-with-copy-activity"></a>Másolási tevékenységgel rendelkező folyamat
A folyamat egy másolási tevékenységet tartalmaz, amely a bemeneti és a kimeneti adatkészletek használatára van konfigurálva, és óránkénti futásra van ütemezve. A folyamat JSON-definíciójában a **forrás** típusa **RelationalSource** (SAP BW forrás) értékre van **állítva, a fogadó típusa pedig** **BlobSink**. A **lekérdezési** tulajdonsághoz megadott lekérdezés a másoláshoz az elmúlt órában kijelöli az adatforrást.

```json
{
    "name": "CopySapBwToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [
            {
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "RelationalSource",
                        "query": "<MDX query for SAP BW>"
                    },
                    "sink": {
                        "type": "BlobSink",
                        "writeBatchSize": 0,
                        "writeBatchTimeout": "00:00:00"
                    }
                },
                "inputs": [
                    {
                        "name": "SapBwDataset"
                    }
                ],
                "outputs": [
                    {
                        "name": "AzureBlobDataSet"
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
                "name": "SapBwToBlob"
            }
        ],
        "start": "2017-03-01T18:00:00Z",
        "end": "2017-03-01T19:00:00Z"
    }
}
```

### <a name="type-mapping-for-sap-bw"></a>SAP BW leképezésének típusa
Ahogy azt az [adattovábbítási tevékenységek](data-factory-data-movement-activities.md) című cikk ismerteti, a másolási tevékenység az alábbi kétlépéses megközelítéssel hajtja végre az automatikus típus-konverziókat a forrás típusairól a fogadó típusokra:

1. Konvertálás natív forrásokból .NET-típusra
2. Konvertálás .NET-típusról natív fogadó típusra

Az adatok SAP BWból való áthelyezésekor a rendszer a következő leképezéseket használja SAP BW típusokból .NET-típusokra.

Adattípus a ABAP szótárban | .NET-adattípus
-------------------------------- | --------------
ACCP |    Int
CHAR | Sztring
Ügyf | Sztring
CURR | Tizedesjegy
CUKY | Sztring
DECEMBER | Tizedesjegy
FLTP | Dupla
INT1 | Bájt
INT2 | Int16
INT4 | Int
LANG | Sztring
LCHR | Sztring
LRAW | Bájt []
PREC | Int16
QUAN | Tizedesjegy
NYERS | Bájt []
RAWSTRING | Bájt []
KARAKTERLÁNC | Sztring
EGYSÉG | Sztring
DATS | Sztring
NUMC | Sztring
TIMS | Sztring

> [!NOTE]
> Ha az oszlopokat a forrás adatkészletből a fogadó adatkészletből származó oszlopokra kívánja leképezni, tekintse meg [Azure Data Factory az adatkészlet oszlopainak](data-factory-map-columns.md)


## <a name="map-source-to-sink-columns"></a>Forrás leképezése a fogadó oszlopokra
A forrás adatkészletben lévő oszlopok a fogadó adatkészlet oszlopaihoz való leképezésével kapcsolatos további tudnivalókért lásd: [adatkészlet oszlopainak leképezése Azure Data Factoryban](data-factory-map-columns.md).

## <a name="repeatable-read-from-relational-sources"></a>Megismételhető olvasás a rokon forrásokból
Az adatok a kapcsolódó adattárakból való másolása során érdemes megismételni a nem kívánt eredmények elkerülését. Azure Data Factory a szeleteket manuálisan is újra futtathatja. Az újrapróbálkozási szabályzatot is konfigurálhatja egy adatkészlethez, hogy a rendszer hiba esetén újrafuttassa a szeleteket. Ha egy szeletet mindkét módon újrafuttat, meg kell győződnie arról, hogy a szeletek hányszor futnak. Lásd: [megismételhető olvasás a rokon forrásokból](data-factory-repeatable-copy.md#repeatable-read-from-relational-sources)

## <a name="performance-and-tuning"></a>Teljesítmény és hangolás
A [másolási tevékenység teljesítményének & hangolási útmutatójában](data-factory-copy-activity-performance.md) megismerheti azokat a főbb tényezőket, amelyek hatással vannak az adatáthelyezés (másolási tevékenység) teljesítményére Azure Data Factory és az optimalizálás különféle módjaival.
