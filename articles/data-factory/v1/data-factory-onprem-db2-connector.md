---
title: Adatok áthelyezése a DB2-ből Azure Data Factory használatával
description: Ismerje meg, hogyan helyezhetők át adatok egy helyszíni DB2-adatbázisból Azure Data Factory másolási tevékenység használatával
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: bcd1e6f9f9be1765e76399707ae36776a9f1987d
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/14/2021
ms.locfileid: "100387359"
---
# <a name="move-data-from-db2-by-using-azure-data-factory-copy-activity"></a>Adatok áthelyezése a DB2-ből Azure Data Factory másolási tevékenység használatával
> [!div class="op_single_selector" title1="Válassza ki az Ön által használt Data Factory-szolgáltatás verzióját:"]
> * [1-es verzió](data-factory-onprem-db2-connector.md)
> * [2-es verzió (aktuális verzió)](../connector-db2.md)

> [!NOTE]
> Ez a cikk a Data Factory 1-es verziójára vonatkozik. Ha a Data Factory szolgáltatás aktuális verzióját használja, tekintse meg a [DB2-összekötőt a v2-ben](../connector-db2.md).


Ez a cikk azt ismerteti, hogyan használható a másolási tevékenység a Azure Data Factoryban egy helyszíni DB2-adatbázisból egy adattárba való másoláshoz. Az Adatmásolás bármely olyan áruházba, amely támogatott fogadóként szerepel a [Data Factory adatáthelyezési tevékenységek](data-factory-data-movement-activities.md#supported-data-stores-and-formats) cikkben. Ez a témakör a Data Factory cikkre épül, amely áttekintést nyújt az adatáthelyezésről a másolási tevékenység használatával, és felsorolja a támogatott adattár-kombinációkat. 

Data Factory jelenleg csak egy DB2-adatbázisból származó adatok áthelyezését támogatja egy [támogatott fogadó adattárba](data-factory-data-movement-activities.md#supported-data-stores-and-formats). Az adatok más adattárakból DB2-adatbázisba való áthelyezése nem támogatott.

## <a name="prerequisites"></a>Előfeltételek
Data Factory támogatja a helyszíni DB2-adatbázishoz való csatlakozást az [adatkezelési átjáró](data-factory-data-management-gateway.md)használatával. Az átjáró adatfolyamatának az adatok áthelyezéséhez való beállításával kapcsolatos részletes útmutatásért lásd az [adatok áthelyezése a helyszínről a felhőbe](data-factory-move-data-between-onprem-and-cloud.md) című cikket.

Egy átjáróra akkor is szükség van, ha a DB2-t az Azure IaaS virtuális gépen üzemelteti. Az átjárót ugyanarra a IaaS virtuális gépre telepítheti, amelyen az adattár is található. Ha az átjáró csatlakozni tud az adatbázishoz, az átjárót egy másik virtuális gépre is telepítheti.

Az adatkezelési átjáró beépített DB2-illesztőprogramot biztosít, így nem kell manuálisan telepítenie az illesztőprogramot az adatok DB2-ből való másolásához.

> [!NOTE]
> A kapcsolatok és az átjárókkal kapcsolatos problémák elhárításával kapcsolatos tippekért tekintse meg az [átjáróval kapcsolatos problémák elhárítása](data-factory-data-management-gateway.md#troubleshooting-gateway-issues) című cikket.


## <a name="supported-versions"></a>Támogatott verziók
A Data Factory DB2-összekötő a következő IBM DB2 platformokat és verziókat támogatja az elosztott kapcsolati adatbázisok architektúrája (DRDA) SQL Access Manager 9, 10 és 11 verzióival:

* IBM DB2 for z/OS 11,1-es verzió
* IBM DB2 for z/OS 10,1-es verzió
* IBM DB2 for i (AS400) 7,2-es verzió
* IBM DB2 for i (AS400) 7,1-es verzió
* IBM DB2 Linux, UNIX és Windows rendszerhez (LUW) 11-es verzió
* IBM DB2 a LUW 10,5-es verzióhoz
* IBM DB2 a LUW 10,1-es verzióhoz

> [!TIP]
> Ha a következő hibaüzenet jelenik meg: "az SQL-utasítás végrehajtási kérelmének megfelelő csomag nem található. SQLSTATE = 51002 SQLCODE =-805, "az ok a szükséges csomag nem jön létre a normál felhasználó számára az operációs rendszeren. A probléma megoldásához kövesse az alábbi utasításokat a DB2-kiszolgáló típusához:
> - DB2 for i (AS400): lehetővé teszi, hogy a felhasználó létrehozza a gyűjteményt a normál felhasználó számára a másolási tevékenység futtatása előtt. A gyűjtemény létrehozásához használja a következő parancsot: `create collection <username>`
> - DB2 for z/OS vagy LUW: magas jogosultsági szintű fiók használata – egy olyan Kiemelt felhasználó vagy rendszergazda, amely csomag-vagy BINDADD rendelkezik, és a VÉGREHAJTÁSt a nyilvános engedélyekkel együtt engedélyezi – a másolás egyszeri futtatásához. A másolás során a rendszer automatikusan létrehozza a szükséges csomagot. Ezt követően visszaválthat a normál felhasználóra a későbbi másolási futtatásokhoz.

## <a name="getting-started"></a>Első lépések
Létrehozhat egy másolási tevékenységgel rendelkező folyamatot, amely különböző eszközök és API-k használatával helyezi át az adatok egy helyszíni DB2-adattárból való áthelyezéséhez: 

- A folyamat létrehozásának legegyszerűbb módja a Azure Data Factory másolási varázsló használata. A folyamat a másolás varázslóval történő létrehozásával kapcsolatos gyors útmutatóért tekintse meg az [oktatóanyag: folyamat létrehozása a másolás varázslóval](data-factory-copy-data-wizard-tutorial.md)című témakört. 
- Eszközöket is használhat a folyamat létrehozásához, beleértve a Visual studiót, a Azure PowerShellt, a Azure Resource Manager sablont, a .NET API-t és a REST API. A másolási tevékenységgel rendelkező folyamat lépésről lépésre történő létrehozásával kapcsolatban lásd a [másolási tevékenység oktatóanyagát](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md). 

Függetlenül attól, hogy az eszközöket vagy API-kat használja, a következő lépések végrehajtásával hozhat létre egy folyamatot, amely egy forrás adattárból egy fogadó adattárba helyezi át az adatait:

1. Társított szolgáltatások létrehozása a bemeneti és kimeneti adattáraknak az adat-előállítóhoz való összekapcsolásához.
2. Hozzon létre adatkészleteket a másolási művelet bemeneti és kimeneti adatok ábrázolásához. 
3. Hozzon létre egy másolási tevékenységgel rendelkező folyamatot, amely egy adatkészletet bemenetként és egy adatkészlet kimenetként való elvégzéséhez szükséges. 

A másolás varázsló használatakor a rendszer automatikusan létrehozza a Data Factory társított szolgáltatások, adatkészletek és folyamat entitások JSON-definícióit. Ha eszközöket vagy API-kat használ (kivéve a .NET API-t), akkor a Data Factory entitásokat a JSON formátum használatával kell meghatároznia. A JSON-példa: az adatok a DB2-ből az Azure Blob Storage-ba való másolása megjeleníti az adatok helyszíni DB2-adattárból történő másolásához használt Data Factory entitások JSON-definícióit.

A következő szakaszokban részletesen ismertetjük a DB2-adattárra jellemző Data Factory entitások definiálásához használt JSON-tulajdonságokat.

## <a name="db2-linked-service-properties"></a>DB2 társított szolgáltatás tulajdonságai
A következő táblázat felsorolja a DB2-hez társított szolgáltatáshoz tartozó JSON-tulajdonságokat.

| Tulajdonság | Leírás | Kötelező |
| --- | --- | --- |
| **típusa** |Ezt a tulajdonságot **OnPremisesDb2** értékre kell beállítani. |Yes |
| **Server** |A DB2-kiszolgáló neve. |Yes |
| **adatbázis** |A DB2-adatbázis neve. |Yes |
| **séma** |A séma neve a DB2-adatbázisban. Ez a tulajdonság megkülönbözteti a kis-és nagybetűket. |No |
| **authenticationType** |A DB2-adatbázishoz való kapcsolódáshoz használt hitelesítés típusa. A lehetséges értékek a következők: névtelen, alapszintű és Windows. |Yes |
| **username** |A felhasználói fiók neve, ha alapszintű vagy Windows-hitelesítést használ. |No |
| **alaphelyzetbe állítása** |A felhasználói fiók jelszava. |No |
| **Átjáró neve** |Annak az átjárónak a neve, amelyet a Data Factory szolgáltatásnak használnia kell a helyszíni DB2-adatbázishoz való kapcsolódáshoz. |Yes |

## <a name="dataset-properties"></a>Adatkészlet tulajdonságai
Az adatkészletek definiálásához rendelkezésre álló csoportok és tulajdonságok listáját az [adatkészletek létrehozása](data-factory-create-datasets.md) című cikkben találja. Az adathalmazok (például a **struktúra**, a **rendelkezésre állás** **és az adatkészlet** JSON-szabályzata) minden adatkészlet esetében hasonlóak (az Azure SQL, az Azure Blob Storage, az Azure Table Storage stb.).

A **typeProperties** szakasz különbözik az egyes adatkészletek típusaitól, és információt nyújt az adattárban található adatok helyéről. A **RelationalTable** típusú adatkészlet **typeProperties** szakasza, amely tartalmazza a DB2-adatkészletet, a következő tulajdonsággal rendelkezik:

| Tulajdonság | Leírás | Kötelező |
| --- | --- | --- |
| **tableName** |Annak a DB2-adatbázis-példánynak a neve, amelyre a társított szolgáltatás hivatkozik. Ez a tulajdonság megkülönbözteti a kis-és nagybetűket. |Nem (ha a **RelationalSource** típusú másolási tevékenység **lekérdezési** tulajdonsága meg van adva) |

## <a name="copy-activity-properties"></a>Másolási tevékenység tulajdonságai
A másolási tevékenységek definiálásához elérhető csoportok és tulajdonságok listáját a [folyamatok létrehozása](data-factory-create-pipelines.md) című cikkben találja. A másolási tevékenység tulajdonságai, például a **név**, a **Leírás**, a **bemeneti** tábla, a **kimenetek** tábla és a **házirend**, minden típusú tevékenység számára elérhetők. A tevékenység **typeProperties** szakaszában elérhető tulajdonságok az egyes tevékenységtípusok esetében eltérőek. Másolási tevékenység esetén a tulajdonságok az adatforrások típusától és a mosdótól függően változnak.

Másolási tevékenység esetén, ha a forrás **RelationalSource** típusú (amely tartalmazza a DB2-t), a **typeProperties** szakaszban a következő tulajdonságok érhetők el:

| Tulajdonság | Leírás | Megengedett értékek | Kötelező |
| --- | --- | --- | --- |
| **lekérdezés** |Az egyéni lekérdezéssel olvashatja el az adatgyűjtést. |SQL-lekérdezési karakterlánc. Például: `"query": "select * from "MySchema"."MyTable""` |Nem (ha meg van adva egy adatkészlet **Táblanév** tulajdonsága) |

> [!NOTE]
> A séma és a tábla neve megkülönbözteti a kis-és nagybetűket. A lekérdezési utasításban a tulajdonságok nevét "" (idézőjelek) használatával tegye meg.

## <a name="json-example-copy-data-from-db2-to-azure-blob-storage"></a>JSON-példa: adatok másolása a DB2-ből az Azure Blob Storage-ba
Ez a példa JSON-definíciókat tartalmaz, amelyek segítségével a [Visual Studióval](data-factory-copy-activity-tutorial-using-visual-studio.md)vagy a [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md)használatával hozhat létre folyamatokat. A példa bemutatja, hogyan másolhat adatok egy DB2-adatbázisból a blob Storage-ba. Az adatmásolt adattárakat azonban Azure Data Factory másolási tevékenység használatával lehet átmásolni a [támogatott adattárba](data-factory-data-movement-activities.md#supported-data-stores-and-formats) .

A mintában a következő Data Factory entitások vannak:

- [OnPremisesDb2](data-factory-onprem-db2-connector.md) típusú DB2-alapú társított szolgáltatás
- [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties) típusú Azure Blob Storage-beli társított szolgáltatás
- [RelationalTable](data-factory-onprem-db2-connector.md#dataset-properties) típusú bemeneti [adatkészlet](data-factory-create-datasets.md)
- [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties) típusú kimeneti [adatkészlet](data-factory-create-datasets.md)
- A [RelationalSource](data-factory-onprem-db2-connector.md#copy-activity-properties) és a [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties) tulajdonságot használó másolási tevékenységgel rendelkező [folyamat](data-factory-create-pipelines.md)

A minta az adatok lekérdezési eredményét egy DB2-adatbázisban egy Azure-blobba másolja óránként. A mintában használt JSON-tulajdonságokat az entitás-definíciókat követő szakaszokban ismertetjük.

Első lépésként telepítse és konfigurálja az adatátjárót. Az utasítások a helyszíni [helyszínek és a felhő közötti adatáthelyezést](data-factory-move-data-between-onprem-and-cloud.md) ismertetik.

**DB2 társított szolgáltatás**

```json
{
    "name": "OnPremDb2LinkedService",
    "properties": {
        "type": "OnPremisesDb2",
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

**Azure Blob Storage társított szolgáltatás**

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

**DB2 bemeneti adatkészlet**

A minta azt feltételezi, hogy létrehozott egy "Sajáttábla" nevű táblát, amelynek oszlopa "Timestamp" felirattal van ellátva az idősorozat-adatként.

A **külső** tulajdonság értéke "true" (igaz). Ez a beállítás tájékoztatja a Data Factory szolgáltatást arról, hogy ez az adatkészlet kívül esik az adat-előállítón, és nem az adat-előállító tevékenysége. Figyelje meg, hogy a **Type** tulajdonság értéke **RelationalTable**.


```json
{
    "name": "Db2DataSet",
    "properties": {
        "type": "RelationalTable",
        "linkedServiceName": "OnPremDb2LinkedService",
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

**Azure blobkimeneti adatkészlet**

Az új blobba való beírása óránként történik, ha a **Frequency** tulajdonságot "Hour" értékre állítja, az **intervallum** tulajdonság értéke pedig 1. A blob **folderPath** tulajdonságát a rendszer dinamikusan kiértékeli a feldolgozás alatt álló szelet kezdési időpontja alapján. A mappa elérési útja a kezdési időpont év, hónap, nap és óra részét használja.

```json
{
    "name": "AzureBlobDb2DataSet",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties": {
            "folderPath": "mycontainer/db2/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
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

**Másolási tevékenység folyamata**

A folyamat egy másolási tevékenységet tartalmaz, amely a megadott bemeneti és kimeneti adatkészletek használatára van konfigurálva, és amely óránkénti futásra van ütemezve. A folyamat JSON-definíciójában a **forrás** típusa **RelationalSource** értékre van állítva, a fogadó **típusa pedig** **BlobSink**. A **lekérdezési** tulajdonsághoz megadott SQL-lekérdezés kiválasztja az "Orders" tábla adatait.

```json
{
    "name": "CopyDb2ToBlob",
    "properties": {
        "description": "pipeline for the copy activity",
        "activities": [
            {
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "RelationalSource",
                        "query": "select * from \"Orders\""
                    },
                    "sink": {
                        "type": "BlobSink"
                    }
                },
                "inputs": [
                    {
                        "name": "Db2DataSet"
                    }
                ],
                "outputs": [
                    {
                        "name": "AzureBlobDb2DataSet"
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
                "name": "Db2ToBlob"
            }
        ],
        "start": "2014-06-01T18:00:00Z",
        "end": "2014-06-01T19:00:00Z"
    }
}
```

## <a name="type-mapping-for-db2"></a>Típus leképezése DB2-hez
Ahogy azt az [adattovábbítási tevékenységek](data-factory-data-movement-activities.md) című cikk ismerteti, a másolási tevékenység az alábbi kétlépéses megközelítéssel hajtja végre az automatikus típus-konverziót a forrás típusról a fogadó típusra:

1. Átalakítás natív forrásból típusról .NET-típusra
2. Átalakítás .NET-típusról natív fogadó típusra

A következő leképezéseket használja a rendszer, amikor a másolási tevékenység egy DB2-típusról .NET-típusra konvertálja az adatait:

| DB2-adatbázis típusa | .NET-keretrendszer típusa |
| --- | --- |
| SmallInt |Int16 |
| Egész szám |Int32 |
| BigInt |Int64 |
| Valós |Egyirányú |
| Dupla |Dupla |
| Float |Dupla |
| Tizedesjegy |Tizedesjegy |
| DecimalFloat |Tizedesjegy |
| Numerikus |Tizedesjegy |
| Date |Dátum/idő |
| Idő |időtartam |
| Időbélyeg |DateTime |
| Xml |Bájt [] |
| Char |Sztring |
| VarChar |Sztring |
| LongVarChar |Sztring |
| DB2DynArray |Sztring |
| Bináris |Bájt [] |
| VarBinary |Bájt [] |
| LongVarBinary |Bájt [] |
| Grafikus |Sztring |
| VarGraphic |Sztring |
| LongVarGraphic |Sztring |
| CLOB |Sztring |
| Blob |Bájt [] |
| DbClob |Sztring |
| SmallInt |Int16 |
| Egész szám |Int32 |
| BigInt |Int64 |
| Valós |Egyirányú |
| Dupla |Dupla |
| Float |Dupla |
| Tizedesjegy |Tizedesjegy |
| DecimalFloat |Tizedesjegy |
| Numerikus |Tizedesjegy |
| Date |Dátum/idő |
| Idő |időtartam |
| Időbélyeg |DateTime |
| Xml |Bájt [] |
| Char |Sztring |

## <a name="map-source-to-sink-columns"></a>Forrás leképezése a fogadó oszlopokra
Ha meg szeretné tudni, hogyan képezhetők le oszlopok a forrás-adatkészletben a fogadó adatkészlet oszlopaiba, tekintse meg a következőt: [adatkészlet oszlopainak megfeleltetése Azure Data Factory](data-factory-map-columns.md).

## <a name="repeatable-reads-from-relational-sources"></a>Ismétlődő olvasások a rokon forrásokból
Amikor Adatmásolást végez egy kapcsolódó adattárból, a nem kívánt eredmények elkerüléséhez tartsa szem előtt az ismétlődést. Azure Data Factory a szeleteket manuálisan is újra futtathatja. Az újrapróbálkozási **szabályzat** tulajdonságot is konfigurálhatja egy adatkészlethez, ha hiba esetén újra szeretne futtatni egy szeletet. Győződjön meg arról, hogy ugyanaz az információ olvasható, függetlenül attól, hogy hányszor futtatja újra a szeletet, és hogy a szelet hogyan fut újra. További információ: [ismétlődő olvasások a rokon forrásokból](data-factory-repeatable-copy.md#repeatable-read-from-relational-sources).

## <a name="performance-and-tuning"></a>Teljesítmény és finomhangolás
Ismerje meg a másolási tevékenység teljesítményét befolyásoló főbb tényezőket és a teljesítmény optimalizálásának módjait a [másolási tevékenység teljesítményének és hangolási útmutatójának](data-factory-copy-activity-performance.md)használatával.
