---
title: Adatok áthelyezése SAP HANAról Azure Data Factory használatával
description: Útmutató az adatok SAP HANA Azure Data Factory használatával történő áthelyezéséhez.
author: linda33wj
ms.author: jingwang
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/10/2018
robots: noindex
ms.openlocfilehash: 9c192a5f84f36fbdea0dd1d280928aa40186d4ee
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/14/2021
ms.locfileid: "100386985"
---
# <a name="move-data-from-sap-hana-using-azure-data-factory"></a>Adatok áthelyezése SAP HANAról Azure Data Factory használatával
> [!div class="op_single_selector" title1="Válassza ki az Ön által használt Data Factory-szolgáltatás verzióját:"]
> * [1-es verzió](data-factory-sap-hana-connector.md)
> * [2-es verzió (aktuális verzió)](../connector-sap-hana.md)

> [!NOTE]
> Ez a cikk a Data Factory 1-es verziójára vonatkozik. Ha a Data Factory szolgáltatás aktuális verzióját használja, tekintse [meg az SAP HANA Connector v2-ben](../connector-sap-business-warehouse.md)című témakört.

Ez a cikk azt ismerteti, hogyan használható a másolási tevékenység a Azure Data Factoryban egy helyszíni SAP HANA adatainak áthelyezéséhez. Az [adattovábbítási tevékenységekről](data-factory-data-movement-activities.md) szóló cikkre épül, amely általános áttekintést nyújt az adatáthelyezésről a másolási tevékenységgel.

A helyszíni SAP HANA adattárból bármely támogatott fogadó adattárba másolhat adatok. A másolási tevékenység által mosogatóként támogatott adattárak listáját a [támogatott adattárak](data-factory-data-movement-activities.md#supported-data-stores-and-formats) táblázatban tekintheti meg. A adatfeldolgozó jelenleg csak az adatok áthelyezését támogatja egy SAP HANAról más adattárakba, de az adatok más adattárakból egy SAP HANAba való áthelyezésére nem.

## <a name="supported-versions-and-installation"></a>Támogatott verziók és telepítés
Ez az összekötő a SAP HANA adatbázis bármely verzióját támogatja. Támogatja a HANA-adatmodellek (például analitikai és számítási nézetek) és a sor/oszlop táblák adatainak másolását SQL-lekérdezések használatával.

A SAP HANA-példányhoz való kapcsolódás engedélyezéséhez telepítse a következő összetevőket:
- **Adatkezelés átjáró**: Data Factory a szolgáltatás támogatja a helyszíni adattárakhoz (beleértve a SAP HANA) való csatlakozást adatkezelés átjáró nevű összetevő használatával. Ha többet szeretne megtudni az átjáró beállításáról adatkezelés átjáróról, és részletes útmutatást talál a helyszíni [adattárak közötti adatáthelyezés a felhőalapú adattárba](data-factory-move-data-between-onprem-and-cloud.md) című cikkben olvashat. Az átjáróra akkor is szükség van, ha a SAP HANA egy Azure IaaS virtuális gépen (VM) üzemel. Az átjárót ugyanarra a virtuális gépre telepítheti, mint az adattár vagy egy másik virtuális gép, feltéve, hogy az átjáró csatlakozni tud az adatbázishoz.
- **SAP HANA ODBC-illesztő** az átjáró számítógépén. Az SAP HANA ODBC-illesztőprogramot az [SAP Software Download Center központból](https://support.sap.com/swdc) töltheti le. Keressen rá a **Windows-ügyfél kulcsszava SAP HANA**. 

## <a name="getting-started"></a>Első lépések
Létrehozhat egy másolási tevékenységgel rendelkező folyamatot, amely különböző eszközök/API-k használatával helyez át egy helyszíni SAP HANA-adattárból származó adatokkal. 

- A folyamat létrehozásának legegyszerűbb módja a **Másolás varázsló** használata. Tekintse meg az [oktatóanyag: folyamat létrehozása a másolás varázslóval](data-factory-copy-data-wizard-tutorial.md) című témakört, amely gyors áttekintést nyújt a folyamat létrehozásáról az adatmásolási varázsló használatával. 
- A következő eszközöket is használhatja a folyamat létrehozásához: **Visual Studio**, **Azure PowerShell**, **Azure Resource Manager template**, **.NET API** és **REST API**. A másolási tevékenységgel rendelkező folyamat létrehozásával kapcsolatos részletes utasításokat a [másolási tevékenységről szóló oktatóanyagban](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) talál. 

Függetlenül attól, hogy az eszközöket vagy API-kat használja, a következő lépések végrehajtásával hozhat létre egy folyamatot, amely egy forrás adattárból egy fogadó adattárba helyezi át az adatait:

1. **Társított szolgáltatások** létrehozása a bemeneti és kimeneti adattáraknak az adat-előállítóhoz való összekapcsolásához.
2. Hozzon létre **adatkészleteket** a másolási művelet bemeneti és kimeneti adatok ábrázolásához. 
3. Hozzon **létre egy másolási tevékenységgel rendelkező folyamatot** , amely egy adatkészletet bemenetként és egy adatkészlet kimenetként való elvégzéséhez szükséges. 

A varázsló használatakor a rendszer automatikusan létrehozza a Data Factory entitások (társított szolgáltatások, adatkészletek és a folyamat) JSON-definícióit. Ha eszközöket/API-kat használ (kivéve a .NET API-t), akkor ezeket a Data Factory entitásokat JSON-formátumban kell megadnia.  A helyszíni SAP HANAokból származó adatok másolásához használt Data Factory JSON-definíciókkal rendelkező minta esetében lásd a jelen cikk JSON- [példa: adatok másolása a SAP HANAról az Azure blobba](#json-example-copy-data-from-sap-hana-to-azure-blob) című szakaszát. 

A következő szakaszokban részletesen ismertetjük a SAP HANA adattárra jellemző Data Factory-entitások definiálásához használt JSON-tulajdonságokat:

## <a name="linked-service-properties"></a>Társított szolgáltatás tulajdonságai
A következő táblázat a SAP HANA társított szolgáltatáshoz tartozó JSON-elemek leírását tartalmazza.

Tulajdonság | Leírás | Megengedett értékek | Kötelező
-------- | ----------- | -------------- | --------
kiszolgáló | Annak a kiszolgálónak a neve, amelyen az SAP HANA-példány található. Ha a kiszolgáló testreszabott portot használ, adja meg a t `server:port` . | sztring | Yes
authenticationType | A hitelesítés típusa. | karakterlánc. "Alapszintű" vagy "Windows" | Yes 
username | Az SAP-kiszolgálóhoz hozzáféréssel rendelkező felhasználó neve | sztring | Yes
jelszó | A felhasználó jelszava. | sztring | Yes
Átjáró neve | Annak az átjárónak a neve, amelyet a Data Factory szolgáltatásnak használnia kell a helyszíni SAP HANA-példányhoz való kapcsolódáshoz. | sztring | Yes
encryptedCredential | A titkosított hitelesítő adat karakterlánca. | sztring | No

## <a name="dataset-properties"></a>Adatkészlet tulajdonságai
Az adatkészletek definiálásához rendelkezésre álló & tulajdonságok teljes listáját az [adatkészletek létrehozása](data-factory-create-datasets.md) című cikkben találja. Az adathalmazok (például a struktúra, a rendelkezésre állás és a szabályzat) minden adatkészlet esetében hasonlóak (például az Azure SQL, az Azure Blob, az Azure Table stb.).

A **typeProperties** szakasz különbözik az egyes adatkészletek típusaitól, és információt nyújt az adattárban található adatok helyéről. Nincs olyan Type-specifikus tulajdonság, amely a **RelationalTable** típusú SAP HANA adatkészlet esetében nem támogatott. 


## <a name="copy-activity-properties"></a>Másolási tevékenység tulajdonságai
A tevékenységek definiálásához elérhető & tulajdonságok teljes listáját a [folyamatok létrehozása](data-factory-create-pipelines.md) című cikkben találja. A tulajdonságok, például a név, a leírás, a bemeneti és a kimeneti táblák, a szabályzatok minden típusú tevékenységhez elérhetők.

Míg a tevékenység **typeProperties** szakaszában elérhető tulajdonságok az egyes tevékenységtípusok esetében eltérőek. Másolási tevékenység esetén a források és a nyelők típusaitól függően változnak.

Ha a másolási tevékenységben szereplő forrás **RelationalSource** típusú (amely magában foglalja a SAP HANA), a következő tulajdonságok érhetők el a typeProperties szakaszban:

| Tulajdonság | Leírás | Megengedett értékek | Kötelező |
| --- | --- | --- | --- |
| lekérdezés | Azt az SQL-lekérdezést adja meg, amely az SAP HANA-példányból olvassa be az adatok beolvasását. | SQL-lekérdezés. | Yes |

## <a name="json-example-copy-data-from-sap-hana-to-azure-blob"></a>JSON-példa: adatok másolása SAP HANAból az Azure-Blobba
Az alábbi minta JSON-definíciókat tartalmaz, amelyek segítségével a [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) vagy a [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md)használatával hozhat létre folyamatokat. Ez a minta bemutatja, hogyan másolhat adatok egy helyszíni SAP HANAból egy Azure-Blob Storageba. Az [itt](data-factory-data-movement-activities.md#supported-data-stores-and-formats) felsorolt mosogatók adatai azonban **közvetlenül** átmásolhatók a Azure Data Factoryban található másolási tevékenység használatával.  

> [!IMPORTANT]
> Ez a példa JSON-kódrészleteket biztosít. Nem tartalmaz részletes útmutatást az adatelőállító létrehozásához. Részletes útmutatásért lásd: az [adatáthelyezés a helyszíni helyszínek és a felhőalapú cikkek között](data-factory-move-data-between-onprem-and-cloud.md) .

A minta a következő adatgyári entitásokat tartalmazhatja:

1. [SapHana](#linked-service-properties)típusú társított szolgáltatás.
2. [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties)típusú társított szolgáltatás.
3. [RelationalTable](#dataset-properties)típusú bemeneti [adatkészlet](data-factory-create-datasets.md) .
4. [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties)típusú kimeneti [adatkészlet](data-factory-create-datasets.md) .
5. [RelationalSource](#copy-activity-properties) és [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties)használó másolási tevékenységgel rendelkező [folyamat](data-factory-create-pipelines.md) .

A minta egy SAP HANA példány adatait másolja át egy Azure-blobba óránként. Az ezekben a mintákban használt JSON-tulajdonságokat a mintákat követő szakaszokban ismertetjük.

Első lépésként állítsa be az adatkezelési átjárót. Az utasítások a helyszíni [helyszínek és a felhő közötti adatáthelyezést](data-factory-move-data-between-onprem-and-cloud.md) ismertetik.

### <a name="sap-hana-linked-service"></a>Társított szolgáltatás SAP HANA
Ez a társított szolgáltatás összekapcsolja SAP HANA példányát az adatelőállítóval. A Type tulajdonság értéke **SapHana**. A typeProperties szakasz a SAP HANA példányra vonatkozó kapcsolatok adatait tartalmazza.

```json
{
    "name": "SapHanaLinkedService",
    "properties":
    {
        "type": "SapHana",
        "typeProperties":
        {
            "server": "<server name>",
            "authenticationType": "<Basic, or Windows>",
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

### <a name="sap-hana-input-dataset"></a>Bemeneti adatkészlet SAP HANA

Ez az adatkészlet határozza meg a SAP HANA adatkészletet. A Data Factory adatkészlet típusát állítsa **RelationalTable** értékre. Jelenleg nem határoz meg egy SAP HANA adatkészlet típus-specifikus tulajdonságait. A másolási tevékenység definíciójában szereplő lekérdezés meghatározza, hogy milyen adatok olvashatók a SAP HANA-példányból. 

Ha a külső tulajdonságot True (igaz) értékre állítja, az a Data Factory szolgáltatás, amely a tábla kívül esik az adatgyárban, nem az adatelőállító tevékenysége.

A gyakoriság és az intervallum tulajdonságai határozzák meg az ütemtervet. Ebben az esetben az adatok beolvasása az SAP HANA-példányból óránként történik. 

```json
{
    "name": "SapHanaDataset",
    "properties": {
        "type": "RelationalTable",
        "linkedServiceName": "SapHanaLinkedService",
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
Ez az adatkészlet határozza meg a kimeneti Azure Blob-adatkészletet. A Type tulajdonság értéke AzureBlob. A typeProperties szakasz a SAP HANA példányból másolt adatok tárolásának helyét adja meg. Az új blobba való írás minden órában történik (frekvencia: óra, intervallum: 1). A blob mappájának elérési útját a rendszer dinamikusan kiértékeli a feldolgozás alatt álló szelet kezdési időpontja alapján. A mappa elérési útja a kezdési idő év, hónap, nap és óra részét használja.

```json
{
    "name": "AzureBlobDataSet",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties": {
            "folderPath": "mycontainer/saphana/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
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

A folyamat egy másolási tevékenységet tartalmaz, amely a bemeneti és a kimeneti adatkészletek használatára van konfigurálva, és óránkénti futásra van ütemezve. A folyamat JSON-definíciójában a **forrás** típusa **RelationalSource** (SAP HANA forrás) értékre van **állítva, a fogadó típusa pedig** **BlobSink**. A **lekérdezési** tulajdonsághoz megadott SQL-lekérdezés a másoláshoz az elmúlt órában kijelöli az összes adatforrást.

```json
{
    "name": "CopySapHanaToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [
            {
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "RelationalSource",
                        "query": "<SQL Query for HANA>"
                    },
                    "sink": {
                        "type": "BlobSink",
                        "writeBatchSize": 0,
                        "writeBatchTimeout": "00:00:00"
                    }
                },
                "inputs": [
                    {
                        "name": "SapHanaDataset"
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
                "name": "SapHanaToBlob"
            }
        ],
        "start": "2017-03-01T18:00:00Z",
        "end": "2017-03-01T19:00:00Z"
    }
}
```


### <a name="type-mapping-for-sap-hana"></a>SAP HANA leképezésének típusa
Ahogy azt az [adattovábbítási tevékenységek](data-factory-data-movement-activities.md) című cikk ismerteti, a másolási tevékenység az alábbi kétlépéses megközelítéssel hajtja végre az automatikus típus-konverziókat a forrás típusairól a fogadó típusokra:

1. Konvertálás natív forrásokból .NET-típusra
2. Konvertálás .NET-típusról natív fogadó típusra

Az adatok SAP HANAból való áthelyezésekor a rendszer a következő leképezéseket használja SAP HANA típusokból .NET-típusokra.

SAP HANA típusa | .NET-alapú típus
------------- | ---------------
TINYINT | Bájt
SMALLINT | Int16
INT | Int32
BIGINT | Int64
VALÓS SZÁM | Egyirányú
DUPLÁN | Egyirányú
DECIMÁLIS | Tizedesjegy
LOGIKAI | Bájt
VARCHAR | Sztring
NVARCHAR | Sztring
CLOB | Bájt []
ALPHANUM | Sztring
BLOB | Bájt []
DATE | DateTime
TIME | időtartam
IDŐBÉLYEGZŐ | DateTime
SECONDDATE | DateTime

## <a name="known-limitations"></a>Ismert korlátozások
Az adatok SAP HANAból való másolása néhány ismert korlátozást mutat be:

- Az NVARCHAR sztringeket a rendszer legfeljebb 4000 Unicode karakter hosszúságúra csonkolja
- A SMALLDECIMAL nem támogatott
- A VARBINARY nem támogatott
- Az érvényes dátumok 1899/12/30 és 9999/12/31 közöttiek

## <a name="map-source-to-sink-columns"></a>Forrás leképezése a fogadó oszlopokra
A forrás adatkészletben lévő oszlopok a fogadó adatkészlet oszlopaihoz való leképezésével kapcsolatos további tudnivalókért lásd: [adatkészlet oszlopainak leképezése Azure Data Factoryban](data-factory-map-columns.md).

## <a name="repeatable-read-from-relational-sources"></a>Megismételhető olvasás a rokon forrásokból
Az adatok a kapcsolódó adattárakból való másolása során érdemes megismételni a nem kívánt eredmények elkerülését. Azure Data Factory a szeleteket manuálisan is újra futtathatja. Az újrapróbálkozási szabályzatot is konfigurálhatja egy adatkészlethez, hogy a rendszer hiba esetén újrafuttassa a szeleteket. Ha egy szeletet mindkét módon újrafuttat, meg kell győződnie arról, hogy a szeletek hányszor futnak. Lásd: [megismételhető olvasás a rokon forrásokból](data-factory-repeatable-copy.md#repeatable-read-from-relational-sources)

## <a name="performance-and-tuning"></a>Teljesítmény és hangolás
A [másolási tevékenység teljesítményének & hangolási útmutatójában](data-factory-copy-activity-performance.md) megismerheti azokat a főbb tényezőket, amelyek hatással vannak az adatáthelyezés (másolási tevékenység) teljesítményére Azure Data Factory és az optimalizálás különféle módjaival.
