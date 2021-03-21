---
title: Adatok másolása az Oracle-be vagy onnan a Data Factory használatával
description: Megtudhatja, hogyan másolhat adatok egy helyszíni Oracle-adatbázisba vagy onnan a Azure Data Factory használatával.
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.date: 05/15/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 02fc142a08176aa577250417c0e394218e832f34
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "100387342"
---
# <a name="copy-data-to-or-from-oracle-on-premises-by-using-azure-data-factory"></a>Adatok másolása a helyszíni Oracle-be vagy onnan a Azure Data Factory használatával

> [!div class="op_single_selector" title1="Válassza ki az Ön által használt Data Factory-szolgáltatás verzióját:"]
> * [1-es verzió](data-factory-onprem-oracle-connector.md)
> * [2-es verzió (aktuális verzió)](../connector-oracle.md)

> [!NOTE]
> Ez a cikk az Azure Data Factory 1-es verziójára vonatkozik. Ha a Azure Data Factory szolgáltatás aktuális verzióját használja, tekintse [meg az Oracle-összekötőt a v2-ben](../connector-oracle.md).


Ez a cikk azt ismerteti, hogyan használható a másolási tevékenység a Azure Data Factoryban az adatok egy helyszíni Oracle-adatbázisba való áthelyezéséhez. A cikk az adatáthelyezési [tevékenységekre](data-factory-data-movement-activities.md)épül, amely általános áttekintést nyújt az adatáthelyezésről a másolási tevékenység használatával.

## <a name="supported-scenarios"></a>Támogatott esetek

Az *Oracle-adatbázisból* a következő adattárakba másolhatja a kívánt adatok:

[!INCLUDE [data-factory-supported-sink](../../../includes/data-factory-supported-sinks.md)]

A következő adattárakból származó adatok másolhatók *egy Oracle-adatbázisba*:

[!INCLUDE [data-factory-supported-sources](../../../includes/data-factory-supported-sources.md)]

## <a name="prerequisites"></a>Előfeltételek

Data Factory támogatja a helyszíni Oracle-forrásokhoz való kapcsolódást adatkezelés átjáró használatával. Az adatkezelés-átjáróval kapcsolatos további tudnivalókért tekintse meg [adatkezelés átjárót](data-factory-data-management-gateway.md) . Az adatok áthelyezéséről az adatfolyamatokban az átjáró beállításával kapcsolatos részletes útmutatásért lásd: [adatok áthelyezése a helyszínről a felhőbe](data-factory-move-data-between-onprem-and-cloud.md).

Az átjáróra akkor is szükség van, ha az Oracle egy Azure-beli infrastruktúra-szolgáltatásként (IaaS) üzemelő virtuális gépen fut. Az átjárót ugyanarra a IaaS virtuális gépre telepítheti, mint az adattár vagy egy másik virtuális gép, feltéve, hogy az átjáró csatlakozni tud az adatbázishoz.

> [!NOTE]
> A kapcsolattal és az átjáróval kapcsolatos hibák elhárítására vonatkozó tippekért lásd az [átjárókkal kapcsolatos problémák elhárítása](data-factory-data-management-gateway.md#troubleshooting-gateway-issues)című témakört.

## <a name="supported-versions-and-installation"></a>Támogatott verziók és telepítés

Ez az Oracle-összekötő az illesztőprogramok két verzióját támogatja:

- **Microsoft Driver for Oracle (ajánlott)**: a adatkezelés Gateway 2,7-es verziójától kezdve az Oracle-hez készült Microsoft-illesztőprogram automatikusan települ az átjáróval. Nem kell telepítenie vagy frissítenie az illesztőprogramot az Oracle-kapcsolat létesítéséhez. Az illesztőprogram használatával jobb másolási teljesítményt is használhat. Az Oracle-adatbázisok ezen verziói támogatottak:
  - Oracle 12c R1 (12,1)
  - Oracle 11g R1, R2 (11,1, 11,2)
  - Oracle 10g R1, R2 (10,1, 10,2)
  - Oracle 9i R1, R2 (9.0.1, 9,2)
  - Oracle 8i R3 (8.1.7)

    > [!NOTE]
    > Az Oracle-proxykiszolgáló nem támogatott.

    > [!IMPORTANT]
    > Az Oracle-hez készült Microsoft-illesztőprogram jelenleg csak az Oracle-ből származó adatok másolását támogatja. Az illesztőprogram nem támogatja az Oracle-nek való írást. Az adatkezelés átjáró **diagnosztika** lapján a csatlakozás tesztelése funkció nem támogatja ezt az illesztőprogramot. Másik lehetőségként használhatja a másolás varázslót a kapcsolat ellenőrzéséhez.
    >

- **Oracle-adatszolgáltató a .net-hez**: az Oracle-adatszolgáltató használatával adatok másolhatók a vagy az Oracle-ből. Ez az összetevő a [Windowshoz készült Oracle-adatelérési összetevők](https://www.oracle.com/technetwork/topics/dotnet/downloads/)részét képezi. Telepítse a megfelelő verziót (32 bites vagy 64 bites) azon a gépen, amelyen az átjáró telepítve van. [Az Oracle-adatszolgáltató .net 12,1](https://docs.oracle.com/database/121/ODPNT/InstallSystemRequirements.htm#ODPNT149) -es verziója Oracle Database 10g 2. és újabb verzióit is elérheti.

    Ha az **xcopy telepítése** lehetőséget választja, hajtsa végre a readme.htm fájlban ismertetett lépéseket. Javasoljuk, hogy válassza ki azt a telepítőt, amely a felhasználói felületen (nem az XCopy-telepítőn) van.

    A szolgáltató telepítése után indítsa újra a adatkezelés Gateway Host szolgáltatást a gépen a szolgáltatások kisalkalmazás vagy a adatkezelés Gateway Configuration Manager használatával.

Ha a másolás varázslót használja a másolási folyamat létrehozásához, az illesztőprogram típusa a következő: automeghatározva. Alapértelmezés szerint a Microsoft illesztőprogramját használja a rendszer, kivéve, ha az átjáró verziója korábbi, mint a 2,7-es verzió, vagy ha az Oracle-t a fogadóként választja.

## <a name="get-started"></a>Bevezetés

Létrehozhat egy másolási tevékenységet tartalmazó folyamatot. A folyamat különböző eszközök vagy API-k használatával helyezi át az adatait egy helyszíni Oracle-adatbázisba vagy onnan.

A folyamat létrehozásának legegyszerűbb módja a másolás varázsló használata. A folyamat létrehozásához a Adatok másolása varázsló segítségével tekintse meg [az oktatóanyag: folyamat létrehozása a másolás varázslóval](data-factory-copy-data-wizard-tutorial.md) című témakört.

A következő eszközök egyikét is használhatja egy folyamat létrehozásához: **Visual Studio**, **Azure PowerShell**, **Azure Resource Manager sablon**, **.NET API** vagy a **REST API**. A másolási tevékenységgel rendelkező folyamat létrehozásával kapcsolatos részletes utasításokért tekintse meg a [másolási tevékenységről szóló oktatóanyagot](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) .

Függetlenül attól, hogy az eszközöket vagy API-kat használja, hajtsa végre a következő lépéseket egy olyan folyamat létrehozásához, amely egy forrás adattárból egy fogadó adattárba helyezi át az adatait:

1. Hozzon létre egy **adatelőállítót**. Egy adatelőállító egy vagy több folyamatot is tartalmazhat.
2. **Társított szolgáltatások** létrehozása a bemeneti és kimeneti adattáraknak az adat-előállítóhoz való összekapcsolásához. Ha például egy Oracle-adatbázisból másolja az Azure Blob Storage-ba, hozzon létre két társított szolgáltatást, hogy az Oracle Database-t és az Azure Storage-fiókot a saját adatokkal rendelkező gyárához kapcsolja. Az Oracle-specifikus társított szolgáltatások tulajdonságainál tekintse meg a [társított szolgáltatás tulajdonságai](#linked-service-properties)című témakört.
3. Hozzon létre **adatkészleteket** a másolási művelet bemeneti és kimeneti adatok ábrázolásához. Az előző lépésben szereplő példában létrehoz egy adatkészletet, amely megadja az Oracle-adatbázisban a bemeneti adatokat tartalmazó táblázatot. Létrehoz egy másik adatkészletet a blob-tároló és az Oracle-adatbázisból másolt adatok tárolására szolgáló mappa megadásához. Az Oracle-specifikus adatkészlet-tulajdonságok esetében lásd: [adatkészlet tulajdonságai](#dataset-properties).
4. Olyan **folyamat** létrehozása, amely egy másolási tevékenységgel rendelkezik, amely egy adatkészletet bemenetként és egy adatkészlet kimenetként való elvégzésére szolgál. Az előző példában a **OracleSource** forrásként és **BlobSink** kell használni a másolási tevékenységhez. Hasonlóképpen, ha az Azure Blob Storage-ból egy Oracle-adatbázisba másol, a másolási tevékenységben a **BlobSource** és a **OracleSink** is használható. Az Oracle-adatbázisra jellemző másolási tevékenység tulajdonságai: [másolási tevékenység tulajdonságai](#copy-activity-properties). Az adattárak forrásként vagy fogadóként való használatáról az előző szakaszban található adattárat tartalmazó hivatkozást válassza.

A varázsló használatakor a rendszer automatikusan létrehozza a Data Factory entitások JSON-definícióit: társított szolgáltatások, adatkészletek és a folyamat. Ha eszközöket vagy API-kat használ (kivéve a .NET API-t), akkor ezeket a Data Factory entitásokat JSON-formátumban kell megadnia. Olyan mintáknál, amelyek JSON-definíciókkal rendelkeznek az adatok egy helyszíni Oracle-adatbázisba való másolásához használt Data Factory entitásokhoz, lásd a JSON-példákat.

A következő szakaszokban részletesen ismertetjük a Data Factory entitások definiálásához használt JSON-tulajdonságokat.

## <a name="linked-service-properties"></a>Társított szolgáltatás tulajdonságai

A következő táblázat az Oracle-hez társított szolgáltatáshoz tartozó JSON-elemeket ismerteti:

| Tulajdonság | Leírás | Kötelező |
| --- | --- | --- |
| típus |A **Type** tulajdonságot **OnPremisesOracle** értékre kell beállítani. |Yes |
| driverType | Itt adhatja meg, hogy melyik illesztőprogramot használja az adatok egy Oracle-adatbázisba való másolásához. Az engedélyezett értékek a következők: **Microsoft** és **ODP** (alapértelmezett). Lásd: [támogatott verzió és telepítés](#supported-versions-and-installation) az illesztőprogram részleteihez. | No |
| connectionString | A **ConnectionString** tulajdonsághoz tartozó Oracle Database-példányhoz való kapcsolódáshoz szükséges információk megadása. | Yes |
| Átjáró neve | A helyszíni Oracle-kiszolgálóhoz való kapcsolódáshoz használt átjáró neve. |Yes |

**Példa: a Microsoft-illesztőprogram használata**

> [!TIP]
> Ha az "ORA-01025: UPI paraméter a tartományon kívül" üzenet jelenik meg, és az Oracle verziója 8i, adja hozzá `WireProtocolMode=1` a kapcsolódási karakterláncot, és próbálkozzon újra:

```json
{
    "name": "OnPremisesOracleLinkedService",
    "properties": {
        "type": "OnPremisesOracle",
        "typeProperties": {
            "driverType": "Microsoft",
            "connectionString":"Host=<host>;Port=<port>;Sid=<service ID>;User Id=<user name>;Password=<password>;",
            "gatewayName": "<gateway name>"
        }
    }
}
```

**Példa: az ODP-illesztőprogram használata**

Az engedélyezett formátumok megismeréséhez lásd: [Oracle-adatszolgáltató a .net ODP](https://www.connectionstrings.com/oracle-data-provider-for-net-odp-net/)szolgáltatáshoz.

```json
{
    "name": "OnPremisesOracleLinkedService",
    "properties": {
        "type": "OnPremisesOracle",
        "typeProperties": {
            "connectionString": "Data Source=(DESCRIPTION=(ADDRESS=(PROTOCOL=TCP)(HOST=<host name>)(PORT=<port number>))(CONNECT_DATA=(SERVICE_NAME=<service ID>))); User Id=<user name>;Password=<password>;",
            "gatewayName": "<gateway name>"
        }
    }
}
```

## <a name="dataset-properties"></a>Adatkészlet tulajdonságai

Az adatkészletek definiálásához elérhető csoportok és tulajdonságok teljes listáját lásd: [adatkészletek létrehozása](data-factory-create-datasets.md).

Az adatkészlet JSON-fájljának (például a struktúra, a rendelkezésre állás és a szabályzat) részei hasonlóak az összes adatkészlet-típushoz (például Oracle, Azure Blob Storage és Azure Table Storage).

A **typeProperties** szakasz különbözik az egyes adatkészletek típusaitól, és információt nyújt az adattárban található adatok helyéről. A **OracleTable** típusú adatkészlet **typeProperties** szakasza a következő tulajdonságokkal rendelkezik:

| Tulajdonság | Leírás | Kötelező |
| --- | --- | --- |
| tableName |Annak az Oracle-adatbázisnak a neve, amelyre a társított szolgáltatás hivatkozik. |Nem (ha a **oracleReaderQuery** vagy a **OracleSource** meg van adva) |

## <a name="copy-activity-properties"></a>Másolási tevékenység tulajdonságai

A tevékenységek definiálásához elérhető csoportok és tulajdonságok teljes listáját lásd: [folyamatok létrehozása](data-factory-create-pipelines.md).

A tulajdonságok, például a név, a leírás, a bemeneti és a kimeneti táblák, valamint a szabályzatok minden típusú tevékenységhez elérhetők.

> [!NOTE]
> A másolási tevékenység csak egy bemenetet hoz létre, és csak egy kimenetet állít elő.

A tevékenység **typeProperties** szakaszában elérhető tulajdonságok az egyes tevékenységtípusok esetében eltérőek. A másolási tevékenység tulajdonságai a forrás és a fogadó típusától függően változnak.

### <a name="oraclesource"></a>OracleSource

A másolási tevékenységben, ha a forrás a **OracleSource** típusú, a következő tulajdonságok érhetők el a **typeProperties** szakaszban:

| Tulajdonság | Leírás | Megengedett értékek | Kötelező |
| --- | --- | --- | --- |
| oracleReaderQuery |Az egyéni lekérdezés használatával olvashatja el az adatolvasást. |Egy SQL-lekérdezési karakterlánc. Például: "Select \* from **sajáttábla**". <br/><br/>Ha nincs megadva, a rendszer a következő SQL-utasítást hajtja végre: "Select \* from **sajáttábla**" |No<br />(ha  a táblanév **meg van adva** ) |

### <a name="oraclesink"></a>OracleSink

A **OracleSink** a következő tulajdonságokat támogatja:

| Tulajdonság | Leírás | Megengedett értékek | Kötelező |
| --- | --- | --- | --- |
| writeBatchTimeout |Az a várakozási idő, ameddig a Batch INSERT művelet befejeződik, mielőtt időtúllépés történt. |**időtartomány**<br/><br/> Példa: 00:30:00 (30 perc) |No |
| writeBatchSize |Beilleszti az adatmennyiséget az SQL-táblába, ha a puffer mérete eléri a **writeBatchSize** értékét. |Egész szám (sorok száma) |Nem (alapértelmezett: 100) |
| sqlWriterCleanupScript |Meghatározza a másolási tevékenység végrehajtásának lekérdezését, hogy egy adott szelet adattisztítása megtörténjen. |Egy lekérdezési utasítás. |No |
| sliceIdentifierColumnName |Megadja a másolási tevékenység oszlopának nevét egy automatikusan létrehozott szelet azonosítójának kitöltéséhez. A **sliceIdentifierColumnName** értékének használatával törölheti egy adott szelet adatmennyiségét az újrafuttatáskor. |A bináris adattípusú oszlop neve **(32)**. |No |

## <a name="json-examples-for-copying-data-to-and-from-the-oracle-database"></a>JSON-példák az adatok az Oracle-adatbázisba és onnan történő másolásához

Az alábbi példák olyan JSON-definíciókat biztosítanak, amelyek segítségével a [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) vagy a [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md)használatával hozhat létre folyamatokat. A példák azt mutatják be, hogyan másolhatók az adatok egy Oracle-adatbázisból vagy az Azure Blob Storage-ból vagy-ból. Az adattárakat azonban a Azure Data Factoryban található másolási tevékenység használatával másolhatók a [támogatott adattárakban és-formátumokban](data-factory-data-movement-activities.md#supported-data-stores-and-formats) felsorolt mosogatók bármelyikére.

**Példa: adatok másolása az Oracle-ből az Azure Blob Storage-ba**

A mintában a következő Data Factory entitások vannak:

* [OnPremisesOracle](data-factory-onprem-oracle-connector.md#linked-service-properties)típusú társított szolgáltatás.
* [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties)típusú társított szolgáltatás.
* [OracleTable](data-factory-onprem-oracle-connector.md#dataset-properties)típusú bemeneti [adatkészlet](data-factory-create-datasets.md) .
* [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties)típusú kimeneti [adatkészlet](data-factory-create-datasets.md) .
* Egy másolási tevékenységgel rendelkező [folyamat](data-factory-create-pipelines.md) , amely a [OracleSource](data-factory-onprem-oracle-connector.md#copy-activity-properties) használja forrásként és [BlobSinkként](data-factory-azure-blob-connector.md#copy-activity-properties) .

A minta egy helyszíni Oracle-adatbázisban lévő táblából másolja át az adatait egy blobba óránként. A mintában használt különböző tulajdonságokkal kapcsolatos további információkért tekintse meg a mintákat követő szakaszokat.

**Oracle társított szolgáltatás**

```json
{
    "name": "OnPremisesOracleLinkedService",
    "properties": {
        "type": "OnPremisesOracle",
        "typeProperties": {
            "driverType": "Microsoft",
            "connectionString":"Host=<host>;Port=<port>;Sid=<service ID>;User Id=<username>;Password=<password>;",
            "gatewayName": "<gateway name>"
        }
    }
}
```

**Azure Blob Storage társított szolgáltatás**

```json
{
    "name": "StorageLinkedService",
    "properties": {
        "type": "AzureStorage",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<account key>"
        }
    }
}
```

**Oracle bemeneti adatkészlet**

A minta azt feltételezi, hogy létrehozott egy **sajáttábla** nevű táblázatot az Oracle-ben. Egy **timestampcolumn** nevű oszlopot tartalmaz az idősorozat-adatsorokhoz.

**Külső** beállítás: az **igaz** érték azt a Data Factory szolgáltatást tájékoztatja, hogy az adatkészlet kívül esik az adat-előállítón, és hogy az adatkészletet nem egy tevékenység hozta létre az adat-előállítóban.

```json
{
    "name": "OracleInput",
    "properties": {
        "type": "OracleTable",
        "linkedServiceName": "OnPremisesOracleLinkedService",
        "typeProperties": {
            "tableName": "MyTable"
        },
        "external": true,
        "availability": {
            "offset": "01:00:00",
            "interval": "1",
            "anchorDateTime": "2014-02-27T12:00:00",
            "frequency": "Hour"
        },
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

**Azure blobkimeneti adatkészlet**

A rendszer óránként egy új blobba írja az adatbevitelt (**frekvencia**: **óra**, **intervallum**: **1**). A blob mappájának elérési útját és fájlnevét a feldolgozás alatt álló szelet kezdési időpontja alapján dinamikusan értékeli a rendszer. A mappa elérési útja a kezdési időpont év, hónap, nap és óra részét használja.

```json
{
    "name": "AzureBlobOutput",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
            "folderPath": "mycontainer/myfolder/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
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
            "format": {
                "type": "TextFormat",
                "columnDelimiter": "\t",
                "rowDelimiter": "\n"
            }
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        }
    }
}
```

**Másolási tevékenységgel rendelkező folyamat**

A folyamat egy másolási tevékenységet tartalmaz, amely a bemeneti és kimeneti adatkészletek használatára van konfigurálva, és az ütemezett futtatás óránként történik. A folyamat JSON-definíciójában a **forrás** típusa **OracleSource** értékre van állítva, a **fogadó típusa pedig** **BlobSink** értékre van állítva. A **oracleReaderQuery** tulajdonsággal megadott SQL-lekérdezés a másoláshoz az elmúlt órában kijelöli az adott adatforrást.

```json
{
    "name":"SamplePipeline",
    "properties":{
        "start":"2014-06-01T18:00:00",
        "end":"2014-06-01T19:00:00",
        "description":"pipeline for a copy activity",
        "activities":[
            {
                "name": "OracletoBlob",
                "description": "copy activity",
                "type": "Copy",
                "inputs": [
                    {
                        "name": " OracleInput"
                    }
                ],
                "outputs": [
                    {
                        "name": "AzureBlobOutput"
                    }
                ],
                "typeProperties": {
                    "source": {
                        "type": "OracleSource",
                        "oracleReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd HH:mm}\\' AND timestampcolumn < \\'{1:yyyy-MM-dd HH:mm}\\'', WindowStart, WindowEnd)"
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

**Példa: adatok másolása az Azure Blob Storage-ból az Oracle-be**

Ez a minta bemutatja, hogyan másolhat adatok egy Azure Blob Storage-fiókból egy helyszíni Oracle Database-be. Az adatok másolása azonban *közvetlenül* a [támogatott adattárakban és-formátumokban](data-factory-data-movement-activities.md#supported-data-stores-and-formats) felsorolt forrásokból is elvégezhető a Azure Data Factory másolási tevékenységének használatával.

A mintában a következő Data Factory entitások vannak:

* [OnPremisesOracle](data-factory-onprem-oracle-connector.md#linked-service-properties)típusú társított szolgáltatás.
* [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties)típusú társított szolgáltatás.
* [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties)típusú bemeneti [adatkészlet](data-factory-create-datasets.md) .
* [OracleTable](data-factory-onprem-oracle-connector.md#dataset-properties)típusú kimeneti [adatkészlet](data-factory-create-datasets.md) .
* Olyan [folyamat](data-factory-create-pipelines.md) , amely egy másolási tevékenységgel rendelkezik, amely [BlobSource](data-factory-azure-blob-connector.md#copy-activity-properties) használ a [OracleSink](data-factory-onprem-oracle-connector.md#copy-activity-properties) forrásként.

A minta minden órában átmásolja az adatait egy blobból a helyszíni Oracle-adatbázisban lévő táblába. A mintában használt különböző tulajdonságokkal kapcsolatos további információkért tekintse meg a mintákat követő szakaszokat.

**Oracle társított szolgáltatás**

```json
{
    "name": "OnPremisesOracleLinkedService",
    "properties": {
        "type": "OnPremisesOracle",
        "typeProperties": {
            "connectionString": "Data Source=(DESCRIPTION=(ADDRESS=(PROTOCOL=TCP)(HOST=<host name>)(PORT=<port number>))(CONNECT_DATA=(SERVICE_NAME=<service ID>)));
            User Id=<username>;Password=<password>;",
            "gatewayName": "<gateway name>"
        }
    }
}
```

**Azure Blob Storage társított szolgáltatás**

```json
{
    "name": "StorageLinkedService",
    "properties": {
        "type": "AzureStorage",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<account key>"
        }
    }
}
```

**Azure blobbemeneti adatkészlet**

Az adatok minden órában egy új blobból származnak (**frekvencia**: **óra**, **intervallum**: **1**). A blob mappájának elérési útját és fájlnevét a feldolgozás alatt álló szelet kezdési időpontja alapján dinamikusan értékeli a rendszer. A mappa elérési útja a kezdési időpont év, hónap és nap részét használja. A fájlnév a kezdési idő óra részét használja. A **külső** beállítás: **true (igaz** ) értékkel tájékoztatja a Data Factory szolgáltatást, hogy ez a tábla az adatelőállítón kívül esik, és nem az adatelőállító tevékenysége.

```json
{
    "name": "AzureBlobInput",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
            "folderPath": "mycontainer/myfolder/yearno={Year}/monthno={Month}/dayno={Day}",
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
                }
            ],
            "format": {
                "type": "TextFormat",
                "columnDelimiter": ",",
                "rowDelimiter": "\n"
            }
        },
        "external": true,
        "availability": {
            "frequency": "Day",
            "interval": 1
        },
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

**Oracle kimeneti adatkészlet**

A minta feltételezi, hogy létrehozott egy **sajáttábla** nevű táblázatot az Oracle-ben. Hozza létre a táblázatot az Oracle-ben azonos számú oszloppal, amelyek várhatóan tartalmazzák a blob CSV-fájlját. Minden órában új sor kerül a táblázatba.

```json
{
    "name": "OracleOutput",
    "properties": {
        "type": "OracleTable",
        "linkedServiceName": "OnPremisesOracleLinkedService",
        "typeProperties": {
            "tableName": "MyTable"
        },
        "availability": {
            "frequency": "Day",
            "interval": "1"
        }
    }
}
```

**Másolási tevékenységgel rendelkező folyamat**

A folyamat egy másolási tevékenységet tartalmaz, amely úgy van konfigurálva, hogy a bemeneti és kimeneti adatkészleteket használja, és minden órában fusson. A folyamat JSON-definíciójában a **forrás** típusa **BlobSource** értékre van állítva, a **fogadó típusa pedig** **OracleSink** értékre van állítva.

```json
{
    "name":"SamplePipeline",
    "properties":{
        "start":"2014-06-01T18:00:00",
        "end":"2014-06-05T19:00:00",
        "description":"pipeline with a copy activity",
        "activities":[
            {
                "name": "AzureBlobtoOracle",
                "description": "Copy Activity",
                "type": "Copy",
                "inputs": [
                    {
                        "name": "AzureBlobInput"
                    }
                ],
                "outputs": [
                    {
                        "name": "OracleOutput"
                    }
                ],
                "typeProperties": {
                    "source": {
                        "type": "BlobSource"
                    },
                    "sink": {
                        "type": "OracleSink"
                    }
                },
                "scheduler": {
                    "frequency": "Day",
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


## <a name="troubleshooting-tips"></a>Hibaelhárítási tippek

### <a name="problem-1-net-framework-data-provider"></a>1. probléma: a .NET-keretrendszer adatszolgáltatója

**Hibaüzenet**

```text
Copy activity met invalid parameters: 'UnknownParameterName', Detailed message: Unable to find the requested .NET Framework Data Provider. It may not be installed.
```

**Lehetséges okok**

* A .NET-keretrendszer Oracle-adatszolgáltatója nem lett telepítve.
* A .NET-keretrendszer Oracle-adatszolgáltatója a .NET-keretrendszer 2,0-es verziójában lett telepítve, és nem található a .NET-keretrendszer 4,0 mappáiban.

**Resolution** (Osztás)

* Ha még nem telepítette az Oracle-hez készült .NET-szolgáltatót, [telepítse azt](https://www.oracle.com/technetwork/topics/dotnet/downloads/), majd próbálja megismételni a forgatókönyvet.
* Ha a szolgáltató telepítése után is megjelenik a hibaüzenet, hajtsa végre a következő lépéseket:
    1. Nyissa meg a .NET 2,0 számítógép-konfigurációs fájlját a <rendszerlemez:\Windows\Microsoft.NET\Framework64\v2.0.50727\CONFIG\machine.config mappából \> .
    2. Keresse meg a **.net-hez készült Oracle-adatszolgáltatót**. Meg kell tudnia találni a bejegyzést, ahogy az a **System. adat** DbProviderFactories alatt a következő mintában látható  >  :`<add name="Oracle Data Provider for .NET" invariant="Oracle.DataAccess.Client" description="Oracle Data Provider for .NET" type="Oracle.DataAccess.Client.OracleClientFactory, Oracle.DataAccess, Version=2.112.3.0, Culture=neutral, PublicKeyToken=89b483f429c47342" />`
* Másolja a bejegyzést a machine.config fájlba a következő .NET 4,0 mappában: <rendszerlemez \>:\Windows\Microsoft.NET\Framework64\v4.0.30319\Config\machine.config. Ezután módosítsa a verziót 4. xxx. x.x.
* Telepítse <ODP.NET telepített elérési útját \>\11.2.0\client_1\odp.net\bin\4\Oracle.DataAccess.dll a globális szerelvény-gyorsítótárban (GAC) a **Gacutil/i [szolgáltatói útvonal]** futtatásával.

### <a name="problem-2-datetime-formatting"></a>2. probléma: dátum/idő formázása

**Hibaüzenet**

```text
Message=Operation failed in Oracle Database with the following error: 'ORA-01861: literal does not match format string'.,Source=,''Type=Oracle.DataAccess.Client.OracleException,Message=ORA-01861: literal does not match format string,Source=Oracle Data Provider for .NET,'.
```

**Resolution** (Osztás)

Előfordulhat, hogy módosítania kell a lekérdezési karakterláncot a másolási tevékenység alapján, hogy a dátumok hogyan vannak konfigurálva az Oracle-adatbázisban. Íme egy példa (a **to_date** függvény használatával):

```console   
"oracleReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= to_date(\\'{0:MM-dd-yyyy HH:mm}\\',\\'MM/DD/YYYY HH24:MI\\') AND timestampcolumn < to_date(\\'{1:MM-dd-yyyy HH:mm}\\',\\'MM/DD/YYYY HH24:MI\\') ', WindowStart, WindowEnd)"
```


## <a name="type-mapping-for-oracle"></a>Az Oracle típusának leképezése

Ahogy az [adatátviteli tevékenységekben](data-factory-data-movement-activities.md)is említettük, a másolási tevékenység az alábbi kétlépéses megközelítéssel hajtja végre az automatikus típus-konverziókat a forrás típusairól a fogadó típusokra:

1. Konvertálja a natív forrásból származó típusokat a .NET típusra.
2. Alakítsa át a .NET-típusról a natív fogadó típusra.

Ha az Oracle-ből helyez át adatátvitelt, a következő leképezéseket használja az Oracle-adattípusból a .NET-típusra, és fordítva:

| Oracle-adattípus | .NET-keretrendszer adattípusa |
| --- | --- |
| BFÁJL |Bájt [] |
| BLOB |Bájt []<br/>(csak a Microsoft-illesztőprogram használata esetén támogatott az Oracle 10g és újabb verziók esetében) |
| CHAR |Sztring |
| CLOB |Sztring |
| DATE |DateTime |
| FLOAT |Decimális, karakterlánc (ha a pontosság > 28) |
| EGÉSZ SZÁM |Decimális, karakterlánc (ha a pontosság > 28) |
| ÉV ÉS HÓNAP KÖZÖTTI IDŐSZAK |Int32 |
| IDŐINTERVALLUM – MÁSODPERC |időtartam |
| HOSSZÚ |Sztring |
| HOSSZÚ NYERS |Bájt [] |
| NCHAR |Sztring |
| NCLOB |Sztring |
| SZÁMA |Decimális, karakterlánc (ha a pontosság > 28) |
| NVARCHAR2 |Sztring |
| NYERS |Bájt [] |
| ROWID |Sztring |
| IDŐBÉLYEGZŐ |DateTime |
| IDŐBÉLYEG HELYI IDŐZÓNÁVAL |DateTime |
| IDŐBÉLYEG IDŐZÓNÁVAL |DateTime |
| ELŐJEL NÉLKÜLI EGÉSZ SZÁM |Szám |
| VARCHAR2 |Sztring |
| XML |Sztring |

> [!NOTE]
> A Microsoft-illesztőprogram használata esetén a rendszer nem támogatja az adattípusok **intervallumát a hónap** és a **nap közötti időszakban** .

## <a name="map-source-to-sink-columns"></a>Forrás leképezése a fogadó oszlopokra

Ha többet szeretne megtudni a forrás-adatkészlet oszlopainak a fogadó adatkészlet oszlopaihoz való hozzárendeléséről, olvassa el a következőt: [adatkészlet-oszlopok leképezése Data Factory](data-factory-map-columns.md).

## <a name="repeatable-read-from-relational-sources"></a>Megismételhető olvasás a rokon forrásokból

Amikor Adatmásolást végez a kapcsolódó adattárakból, a nem kívánt eredmények elkerüléséhez tartsa szem előtt az ismétlődést. Azure Data Factoryban manuálisan is újrafuttathatja a szeleteket. Az újrapróbálkozási szabályzatot is konfigurálhatja egy adatkészlethez, hogy hiba esetén újra lehessen futtatni a szeleteket. Ha egy szeletet manuálisan vagy újrapróbálkozási szabályzattal futtat újra, ügyeljen arra, hogy a szeletek hányszor fussanak. További információ: [megismételhető olvasás a kapcsolódó forrásokból](data-factory-repeatable-copy.md#repeatable-read-from-relational-sources).

## <a name="performance-and-tuning"></a>Teljesítmény és finomhangolás

A [másolási tevékenység teljesítményének és hangolásának útmutatójában](data-factory-copy-activity-performance.md) megismerheti azokat a főbb tényezőket, amelyek hatással vannak az adatáthelyezés (másolási tevékenység) teljesítményére a Azure Data Factoryban. Az optimalizálásának különböző módjairól is tájékozódhat.
