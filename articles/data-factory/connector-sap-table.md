---
title: Adatok másolása SAP-táblából
description: Megtudhatja, hogyan másolhat adatok egy SAP-táblából egy Azure Data Factory folyamat másolási tevékenységének használatával támogatott fogadó adattárakba.
services: data-factory
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 09/01/2020
ms.openlocfilehash: 608694c07894c8bdff8b1101d607e07ea4383764
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "89279830"
---
# <a name="copy-data-from-an-sap-table-by-using-azure-data-factory"></a>Adatok másolása SAP-táblából Azure Data Factory használatával

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Ez a cikk azt ismerteti, hogyan használható a másolási tevékenység a Azure Data Factoryban az adatok SAP-táblából való másolásához. További információ: [másolási tevékenység áttekintése](copy-activity-overview.md).

>[!TIP]
>Az ADF SAP-adatintegrációs forgatókönyvre vonatkozó általános támogatásának megismeréséhez tekintse meg az [SAP-Adatintegráció Azure Data Factory tanulmány használatával](https://github.com/Azure/Azure-DataFactory/blob/master/whitepaper/SAP%20Data%20Integration%20using%20Azure%20Data%20Factory.pdf) című témakört, amely részletesen ismerteti az egyes SAP-összekötőket, a comparsion és

## <a name="supported-capabilities"></a>Támogatott képességek

Ez az SAP Table Connector a következő tevékenységek esetében támogatott:

- [Másolási tevékenység](copy-activity-overview.md) [támogatott forrás/fogadó mátrixtal](copy-activity-overview.md)
- [Keresési tevékenység](control-flow-lookup-activity.md)

Az adatok az SAP-táblázatokból bármilyen támogatott fogadó adattárba másolhatók. A másolási tevékenység által forrásként vagy nyelőként támogatott adattárak listáját a [támogatott adattárak](copy-activity-overview.md#supported-data-stores-and-formats) táblázatban tekintheti meg.

Az SAP Table Connector különösen a következőket támogatja:

- Adatok másolása SAP-táblából a következőben:

  - SAP ERP központi összetevő (SAP ECC) 7,01-es vagy újabb verzió (az SAP-támogatási csomag egy, a 2015-es kiadás után kiadott új veremben).
  - Az SAP Business Warehouse (SAP BW) 7,01-es vagy újabb verziója (egy, az 2015-es verzió után kiadott SAP-támogatási csomagban).
  - SAP S/4HANA.
  - Más termékek az SAP Business Suite 7,01-es vagy újabb verziójával (az SAP támogatási csomagjának a 2015 után kiadott legújabb verziójában).

- Adatok másolása egy SAP átlátszó táblából, egy készletezett táblából, egy fürtözött táblából és egy nézetből.
- Adatok másolása egyszerű hitelesítéssel vagy biztonságos hálózati kommunikáció (SNC) használatával, ha a SNC konfigurálva van.
- Csatlakozás SAP-alkalmazáskiszolgáló vagy SAP-üzenetkezelő kiszolgálóhoz.
- Adatok beolvasása alapértelmezett vagy egyéni RFC-n keresztül.

Az 7,01-es vagy újabb verzió az SAP NetWeaver verziójára hivatkozik, az SAP ECC verziója helyett. Például az SAP ECC 6,0 EHP Hotels 7 általánosan a NetWeaver verziója >= 7,4. Ha nem biztos a környezetében, a következő lépésekkel ellenőrizheti a verziót az SAP-rendszerből:
1.  Az SAP-rendszerhez való kapcsolódáshoz használja az SAP GUI-t. 
2.  Nyissa **meg a**  ->  **rendszerállapotot**. 
3.  Ellenőrizze a SAP_BASIS kiadását, és győződjön meg arról, hogy a mérete egyenlő vagy nagyobb, mint 701.  
      ![SAP_BASIS keresése](./media/connector-sap-table/sap-basis.png)

## <a name="prerequisites"></a>Előfeltételek

Az SAP Table Connector használatához a következőket kell tennie:

- Saját üzemeltetésű integrációs modul (3,17-es vagy újabb verzió) beállítása. További információ: saját üzemeltetésű [integrációs modul létrehozása és konfigurálása](create-self-hosted-integration-runtime.md).

- Töltse le a 64 bites [SAP-összekötőt Microsoft .NET 3,0](https://support.sap.com/en/product/connectors/msnet.html) -re az SAP webhelyéről, és telepítse a saját üzemeltetésű Integration Runtime-gépre. A telepítés során győződjön meg arról, hogy az **opcionális telepítési lépések** ablakban a **szerelvények telepítése a GAC** -ba lehetőségre van kiválasztva.

  ![A .NET-hez készült SAP-összekötő telepítése](./media/connector-sap-business-warehouse-open-hub/install-sap-dotnet-connector.png)

- A Data Factory SAP Table connectorban használt SAP-felhasználónak a következő engedélyekkel kell rendelkeznie:

  - A Remote Function Call (RFC) célhelyek használatának engedélyezése.
  - A S_SDSAUTH engedélyezési objektum végrehajtási tevékenységének engedélyei.

## <a name="get-started"></a>Bevezetés

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

A következő szakaszokban részletesen ismertetjük az SAP Table connectorra jellemző Data Factory entitások definiálásához használt tulajdonságokat.

## <a name="linked-service-properties"></a>Társított szolgáltatás tulajdonságai

A SAP BW Open hub társított szolgáltatás a következő tulajdonságokat támogatja:

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| `type` | A tulajdonságot a következőre kell `type` beállítani: `SapTable` . | Igen |
| `server` | Annak a kiszolgálónak a neve, amelyen az SAP-példány található.<br/>A használatával csatlakozhat egy SAP-alkalmazáskiszolgáló eléréséhez. | Nem |
| `systemNumber` | Az SAP-szolgáltatás rendszerszáma.<br/>A használatával csatlakozhat egy SAP-alkalmazáskiszolgáló eléréséhez.<br/>Engedélyezett érték: egy kétszámjegyű decimális szám, amely karakterláncként van megadva. | Nem |
| `messageServer` | Az SAP-üzenet kiszolgálójának állomásneve.<br/>A használatával csatlakozhat egy SAP-üzenetküldési kiszolgálóhoz. | Nem |
| `messageServerService` | Az üzenet kiszolgálójának szolgáltatásnév vagy portszáma.<br/>A használatával csatlakozhat egy SAP-üzenetküldési kiszolgálóhoz. | Nem |
| `systemId` | Annak az SAP-rendszernek az azonosítója, amelyben a tábla található.<br/>A használatával csatlakozhat egy SAP-üzenetküldési kiszolgálóhoz. | Nem |
| `logonGroup` | Az SAP-rendszerhez tartozó bejelentkezési csoport.<br/>A használatával csatlakozhat egy SAP-üzenetküldési kiszolgálóhoz. | Nem |
| `clientId` | Az ügyfél azonosítója az SAP-rendszeren.<br/>Engedélyezett érték: egy háromjegyű decimális szám, amely karakterláncként van megadva. | Igen |
| `language` | Az SAP-rendszer által használt nyelv.<br/>Az alapértelmezett érték `EN`.| Nem |
| `userName` | Az SAP-kiszolgálóhoz hozzáféréssel rendelkező felhasználó neve. | Igen |
| `password` | A felhasználó jelszava. Adja meg ezt a mezőt `SecureString` úgy, hogy az biztonságosan tárolja Data Factoryban, vagy [hivatkozzon a Azure Key Vaultban tárolt titkos kulcsra](store-credentials-in-key-vault.md). | Igen |
| `sncMode` | A SNC aktiválási jelzője annak az SAP-kiszolgálónak a eléréséhez, ahol a tábla található.<br/>Akkor használja, ha a SNC használatával szeretne csatlakozni az SAP-kiszolgálóhoz.<br/>Az engedélyezett értékek: `0` (kikapcsolva, alapértelmezett) vagy (bekapcsolva `1` ). | Nem |
| `sncMyName` | A kezdeményező SNC-neve az SAP-kiszolgáló eléréséhez, ahol a tábla található.<br/>Akkor érvényes `sncMode` , ha be van kapcsolva. | Nem |
| `sncPartnerName` | A kommunikációs partner SNC-neve az SAP-kiszolgáló eléréséhez, ahol a tábla található.<br/>Akkor érvényes `sncMode` , ha be van kapcsolva. | Nem |
| `sncLibraryPath` | A külső biztonsági termék könyvtára, amely hozzáfér az SAP-kiszolgálóhoz, ahol a tábla található.<br/>Akkor érvényes `sncMode` , ha be van kapcsolva. | Nem |
| `sncQop` | Az alkalmazni kívánt védelmi szint SNC-minőség.<br/>Akkor érvényes `sncMode` , ha be van kapcsolva. <br/>Az engedélyezett értékek: `1` (hitelesítés), `2` (integritás), `3` (adatvédelem), `8` (alapértelmezett), `9` (maximum). | Nem |
| `connectVia` | Az adattárhoz való csatlakozáshoz használt [integrációs](concepts-integration-runtime.md) modul. A saját üzemeltetésű integrációs modulra van szükség, ahogy azt az [Előfeltételekben](#prerequisites)korábban említettük. |Igen |

**1. példa: Kapcsolódás SAP Application Serverhez**

```json
{
    "name": "SapTableLinkedService",
    "properties": {
        "type": "SapTable",
        "typeProperties": {
            "server": "<server name>",
            "systemNumber": "<system number>",
            "clientId": "<client ID>",
            "userName": "<SAP user>",
            "password": {
                "type": "SecureString",
                "value": "<Password for SAP user>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="example-2-connect-to-an-sap-message-server"></a>2. példa: Kapcsolódás SAP-üzenetkezelő kiszolgálóhoz

```json
{
    "name": "SapTableLinkedService",
    "properties": {
        "type": "SapTable",
        "typeProperties": {
            "messageServer": "<message server name>",
            "messageServerService": "<service name or port>",
            "systemId": "<system ID>",
            "logonGroup": "<logon group>",
            "clientId": "<client ID>",
            "userName": "<SAP user>",
            "password": {
                "type": "SecureString",
                "value": "<Password for SAP user>"
            }
        },
        "connectVia": {
            "referenceName": "<name of integration runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="example-3-connect-by-using-snc"></a>3. példa: a kapcsolat a SNC használatával

```json
{
    "name": "SapTableLinkedService",
    "properties": {
        "type": "SapTable",
        "typeProperties": {
            "server": "<server name>",
            "systemNumber": "<system number>",
            "clientId": "<client ID>",
            "userName": "<SAP user>",
            "password": {
                "type": "SecureString",
                "value": "<Password for SAP user>"
            },
            "sncMode": 1,
            "sncMyName": "<SNC myname>",
            "sncPartnerName": "<SNC partner name>",
            "sncLibraryPath": "<SNC library path>",
            "sncQop": "8"
        },
        "connectVia": {
            "referenceName": "<name of integration runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>Adatkészlet tulajdonságai

Az adatkészletek definiálásához szükséges csoportok és tulajdonságok teljes listáját lásd: [adatkészletek](concepts-datasets-linked-services.md). A következő szakasz az SAP-táblázat adatkészletében támogatott tulajdonságok listáját tartalmazza.

Az adatoknak a és a SAP BW Open hub társított szolgáltatásba való másolásához a következő tulajdonságok támogatottak:

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| `type` | A tulajdonságot a következőre kell `type` beállítani: `SapTableResource` . | Igen |
| `tableName` | Annak az SAP-táblának a neve, amelyből az adatok másolása megtörténjen. | Igen |

### <a name="example"></a>Példa

```json
{
    "name": "SAPTableDataset",
    "properties": {
        "type": "SapTableResource",
        "typeProperties": {
            "tableName": "<SAP table name>"
        },
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<SAP table linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Másolási tevékenység tulajdonságai

A tevékenységek definiálási szakaszainak és tulajdonságainak teljes listáját lásd: [folyamatok](concepts-pipelines-activities.md). A következő szakasz az SAP-táblázat forrása által támogatott tulajdonságokat tartalmazza.

### <a name="sap-table-as-source"></a>SAP-táblázat forrásként

Az adatok SAP-táblából történő másolásához a következő tulajdonságok támogatottak:

| Tulajdonság                         | Leírás                                                  | Kötelező |
| :------------------------------- | :----------------------------------------------------------- | :------- |
| `type`                             | A tulajdonságot a következőre kell `type` beállítani: `SapTableSource` .         | Igen      |
| `rowCount`                         | A beolvasandó sorok száma.                              | Nem       |
| `rfcTableFields`                 | Az SAP-táblából Másolandó mezők (oszlopok). Például: `column0, column1`. | Nem       |
| `rfcTableOptions`                | Az SAP-tábla sorainak szűrésére szolgáló beállítások. Például: `COLUMN0 EQ 'SOMEVALUE'`. Tekintse meg a jelen cikk későbbi, a SAP-lekérdezés operátora című táblázatot is. | Nem       |
| `customRfcReadTableFunctionModule` | Egyéni RFC-függvény modul, amely az adatok SAP-táblából való beolvasására használható.<br>Egyéni RFC-függvények modullal meghatározhatja az adatok lekérésének módját az SAP-rendszerből, és visszaküldheti őket a Data Factorynak. Az egyéni függvény moduljának olyan felülettel kell rendelkeznie (importálás, exportálás, táblák), amely hasonló a ( `/SAPDS/RFC_READ_TABLE2` Data Factory) által használt alapértelmezett interfészhez.<br>Data Factory | Nem       |
| `partitionOption`                  | Az SAP-táblázatból beolvasott partíciós mechanizmus. A támogatott lehetőségek a következők: <ul><li>`None`</li><li>`PartitionOnInt` (normál egész szám vagy egész érték nulla kitöltéssel a bal oldalon, például `0000012345` )</li><li>`PartitionOnCalendarYear` (4 számjegy a következő formátumban: "éééé")</li><li>`PartitionOnCalendarMonth` (6 számjegy a következő formátumban: "YYYYMM")</li><li>`PartitionOnCalendarDate` (8 számjegy a következő formátumban: "ÉÉÉÉHHNN")</li></ul> | Nem       |
| `partitionColumnName`              | Az adatparticionáláshoz használt oszlop neve.                | Nem       |
| `partitionUpperBound`              | A-ben megadott oszlop maximális értéke a `partitionColumnName` particionálás folytatásához lesz használva. | Nem       |
| `partitionLowerBound`              | A-ben megadott oszlop minimális értéke a `partitionColumnName` particionálás folytatásához lesz használva. (Megjegyzés: `partitionLowerBound` nem lehet "0", ha a partíció lehetőség `PartitionOnInt` ) | Nem       |
| `maxPartitionsNumber`              | Az a partíciók maximális száma, amelybe az adatmennyiséget fel kell osztani.     | Nem       |
| `sapDataColumnDelimiter` | Az a karakter, amelyet a rendszer elválasztóként használ az SAP RFC számára a kimeneti adatokat felosztva. | Nem |

>[!TIP]
>Ha az SAP-táblázat nagy mennyiségű adattal rendelkezik (például több milliárd sor), akkor `partitionOption` `partitionSetting` a és az az adatait kisebb partíciókra kell bontani. Ebben az esetben az adatok egy partíció alapján kerülnek beolvasásra, és az egyes adatpartíciók egyetlen RFC-hívással kérhetők le az SAP-kiszolgálóról.<br/>
<br/>
>`partitionOption` `partitionOnInt` Például az egyes partíciókban lévő sorok számát a következő képlettel számítjuk ki: (a és a közötti összes sor `partitionUpperBound` `partitionLowerBound` )/ `maxPartitionsNumber` .<br/>
<br/>
>Ha párhuzamosan szeretné betölteni az adatpartíciókat a másolás felgyorsításához, a párhuzamos mértéket a [`parallelCopies`](copy-activity-performance-features.md#parallel-copy) másolási tevékenység beállításai vezérlik. Ha például négyre van állítva `parallelCopies` , Data Factory egyidejűleg létrehoz és futtat négy lekérdezést a megadott partíciós beállítás és beállítások alapján, és mindegyik lekérdezés az adatok egy részét kéri le az SAP-táblából. Javasoljuk, hogy a `maxPartitionsNumber` tulajdonság értékének többszörösét végezze el `parallelCopies` . Az adatok file-alapú adattárba másolásakor a rendszer úgy is Újrafuttatja, hogy több fájlként is ír egy mappába (csak a mappa nevét adja meg), amely esetben a teljesítmény jobb, mint egyetlen fájlba írás.

A alkalmazásban `rfcTableOptions` a következő általános SAP-lekérdezési operátorokkal szűrheti a sorokat:

| Operátor | Leírás |
| :------- | :------- |
| `EQ` | Egyenlő |
| `NE` | Nem egyenlő |
| `LT` | Kisebb, mint |
| `LE` | Kisebb vagy egyenlő |
| `GT` | Nagyobb, mint |
| `GE` | Nagyobb vagy egyenlő |
| `IN` | A következőképpen: `TABCLASS IN ('TRANSP', 'INTTAB')` |
| `LIKE` | A következőképpen: `LIKE 'Emma%'` |

### <a name="example"></a>Példa

```json
"activities":[
    {
        "name": "CopyFromSAPTable",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<SAP table input dataset name>",
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
                "type": "SapTableSource",
                "partitionOption": "PartitionOnInt",
                "partitionSettings": {
                     "partitionColumnName": "<partition column name>",
                     "partitionUpperBound": "2000",
                     "partitionLowerBound": "1",
                     "maxPartitionsNumber": 500
                 }
            },
            "sink": {
                "type": "<sink type>"
            },
            "parallelCopies": 4
        }
    }
]
```

## <a name="data-type-mappings-for-an-sap-table"></a>Az SAP-táblázat adattípus-hozzárendelései

Az adatok SAP-táblából való másolása során a rendszer a következő leképezéseket használja az SAP Table adattípusokból a Azure Data Factory köztes adattípusokra. Ha szeretné megtudni, hogyan képezi le a másolási tevékenység a forrás sémát és az adattípust a fogadóra, tekintse meg a [séma-és adattípus-leképezéseket](copy-activity-schema-and-type-mapping.md)

| SAP ABAP-típus | Data Factory időközi adattípus |
|:--- |:--- |
| `C` Karakterlánc | `String` |
| `I` Egész | `Int32` |
| `F` Float | `Double` |
| `D` Dátum | `String` |
| `T` Idő | `String` |
| `P` (BCD-csomag, pénznem, decimális, mennyiség) | `Decimal` |
| `N` Numerikus | `String` |
| `X` (Bináris és nyers) | `String` |

## <a name="lookup-activity-properties"></a>Keresési tevékenység tulajdonságai

A tulajdonságok részleteinek megismeréséhez tekintse meg a [keresési tevékenységet](control-flow-lookup-activity.md).


## <a name="next-steps"></a>Következő lépések

A Azure Data Factoryban a másolási tevékenység által a forrásként és a nyelőként támogatott adattárak listáját lásd: [támogatott adattárak](copy-activity-overview.md#supported-data-stores-and-formats).
