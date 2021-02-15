---
title: Adatok áthelyezése az Amazon Vöröseltolódásból Azure Data Factory használatával
description: Megtudhatja, hogyan helyezhet át az Amazon Vöröseltolódásból származó adatok Azure Data Factory másolási tevékenység használatával.
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/22/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 025250f47bf0630be5ae988140a5feeecfd0eaf0
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/14/2021
ms.locfileid: "100377550"
---
# <a name="move-data-from-amazon-redshift-using-azure-data-factory"></a>Adatok áthelyezése az Amazon Vöröseltolódásból a Azure Data Factory használatával
> [!div class="op_single_selector" title1="Válassza ki az Ön által használt Data Factory-szolgáltatás verzióját:"]
> * [1-es verzió](data-factory-amazon-redshift-connector.md)
> * [2-es verzió (aktuális verzió)](../connector-amazon-redshift.md)

> [!NOTE]
> Ez a cikk a Data Factory 1-es verziójára vonatkozik. Ha a Data Factory szolgáltatás aktuális verzióját használja, tekintse [meg az Amazon vöröseltolódás-összekötőt a v2-ben](../connector-amazon-redshift.md).

Ez a cikk azt ismerteti, hogyan használható a másolási tevékenység a Azure Data Factory az Amazon Vöröseltolódásáról származó adatok áthelyezéséhez. A cikk az [adattovábbítási tevékenységekről](data-factory-data-movement-activities.md) szóló cikkre épül, amely általános áttekintést nyújt az adatáthelyezésről a másolási tevékenységgel.

Data Factory jelenleg csak az Amazon vöröseltolódásról származó adatok áthelyezését támogatja egy [támogatott fogadó adattárba](data-factory-data-movement-activities.md#supported-data-stores-and-formats). Az adatok más adattárakból az Amazon Vöröseltolódásba való áthelyezése nem támogatott.

> [!TIP]
> A legjobb teljesítmény elérése érdekében az Amazon-vöröseltolódás nagy mennyiségű adatának másolása során érdemes lehet a beépített vöröseltolódás **eltávolítására** szolgáló parancsot használni az Amazon Simple Storage Service (Amazon S3) használatával. Részletekért lásd: az [Eltávolítás használata az adatok az Amazon vöröseltolódásból történő másolásához](#use-unload-to-copy-data-from-amazon-redshift).

## <a name="prerequisites"></a>Előfeltételek
* Ha helyszíni adattárba helyezi át az adatátvitelt, telepítse [adatkezelés átjárót](data-factory-data-management-gateway.md) egy helyszíni gépre. A helyszíni számítógép IP-címének használatával adjon hozzáférést egy átjáróhoz az Amazon Vöröseltolódási fürthöz. Útmutatásért lásd: [hozzáférés engedélyezése a fürthöz](https://docs.aws.amazon.com/redshift/latest/gsg/rs-gsg-authorize-cluster-access.html).
* Az adatok Azure-adattárba való áthelyezéséhez tekintse meg az [Microsoft Azure adatközpontok által használt számítási IP-címeket és SQL-tartományokat](https://www.microsoft.com/download/details.aspx?id=41653).

## <a name="getting-started"></a>Első lépések
Létrehozhat egy másolási tevékenységgel rendelkező folyamatot, amely különböző eszközök és API-k használatával helyezi át az adatait egy Amazon Vöröseltolódási forrásból.

A folyamat létrehozásának legegyszerűbb módja a Azure Data Factory másolási varázsló használata. A folyamat a másolás varázslóval történő létrehozásával kapcsolatos gyors útmutatóért tekintse meg az [oktatóanyag: folyamat létrehozása a másolás varázslóval](data-factory-copy-data-wizard-tutorial.md)című témakört.

Létrehozhat egy folyamatot a Visual Studio, a Azure PowerShell vagy más eszközök használatával is. Azure Resource Manager sablonokat, a .NET API-t vagy a REST API is felhasználhatja a folyamat létrehozásához. A másolási tevékenységgel rendelkező folyamat lépésről lépésre történő létrehozásával kapcsolatban lásd a [másolási tevékenység oktatóanyagát](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).

Függetlenül attól, hogy az eszközöket vagy API-kat használja, a következő lépések végrehajtásával hozhat létre egy folyamatot, amely egy forrás adattárból egy fogadó adattárba helyezi át az adatait:

1. Társított szolgáltatások létrehozása a bemeneti és kimeneti adattáraknak az adat-előállítóhoz való összekapcsolásához.
2. Hozzon létre adatkészleteket a másolási művelet bemeneti és kimeneti adatok ábrázolásához.
3. Hozzon létre egy másolási tevékenységgel rendelkező folyamatot, amely egy adatkészletet bemenetként és egy adatkészlet kimenetként való elvégzéséhez szükséges.

A másolás varázsló használatakor a rendszer automatikusan létrehozza a Data Factory entitások JSON-definícióit. Ha eszközöket vagy API-kat használ (kivéve a .NET API-t), akkor a Data Factory entitásokat a JSON formátum használatával kell meghatároznia. A JSON-példa: adatok másolása az Amazon Vöröseltolódásból az Azure Blob Storage-ba – a Data Factory entitások JSON-definícióit jeleníti meg, amelyek az adatok Amazon vöröseltolódás-adattárból való másolásához használatosak.

A következő szakaszok ismertetik az Amazon vöröseltolódás Data Factory entitásának definiálásához használt JSON-tulajdonságokat.

## <a name="linked-service-properties"></a>Társított szolgáltatás tulajdonságai

Az alábbi táblázat az Amazon vöröseltolódás társított szolgáltatásához tartozó JSON-elemek leírásait tartalmazza.

| Tulajdonság | Leírás | Kötelező |
| --- | --- | --- |
| **típusa** |Ezt a tulajdonságot **AmazonRedshift** értékre kell beállítani. |Yes |
| **Server** |Az Amazon Vöröseltolódási kiszolgáló IP-címe vagy állomásneve. |Yes |
| **Port** |Az Amazon vöröseltolódás-kiszolgáló által az ügyfélkapcsolatok figyeléséhez használt TCP-port száma. |Nem (az alapértelmezett érték 5439) |
| **adatbázis** |Az Amazon vöröseltolódás-adatbázis neve. |Yes |
| **username** |Annak a felhasználónak a neve, aki hozzáfér az adatbázishoz. |Yes |
| **alaphelyzetbe állítása** |A felhasználói fiók jelszava. |Yes |

## <a name="dataset-properties"></a>Adatkészlet tulajdonságai

Az adatkészletek definiálásához rendelkezésre álló csoportok és tulajdonságok listáját az [adatkészletek létrehozása](data-factory-create-datasets.md) című cikkben találja. A **struktúra**, a **rendelkezésre állás** és a **házirend** fejezetei hasonlóak az összes adatkészlet típusához. Ilyen típusú adathalmazok például az Azure SQL, az Azure Blob Storage és az Azure Table Storage.

A **typeProperties** szakasz különbözik az egyes adatkészletek típusaitól, és információt nyújt a tárolóban lévő adatok helyéről. A **RelationalTable** típusú, Amazon vöröseltolódás-adatkészletet tartalmazó adatkészlet **typeProperties** szakasza a következő tulajdonságokkal rendelkezik:

| Tulajdonság | Leírás | Kötelező |
| --- | --- | --- |
| **tableName** |Annak a táblának a neve az Amazon vöröseltolódás-adatbázisban, amelyre a társított szolgáltatás hivatkozik. |Nem (ha a **RelationalSource** típusú másolási tevékenység **lekérdezési** tulajdonsága meg van adva) |

## <a name="copy-activity-properties"></a>Másolási tevékenység tulajdonságai

A tevékenységek definiálásához elérhető csoportok és tulajdonságok listáját a [folyamatok létrehozása](data-factory-create-pipelines.md) című cikkben találja. A **név**, a **Leírás**, a **beviteli** tábla, a **kimenet** tábla és a **házirend** tulajdonságai minden típusú tevékenységhez elérhetők. A **typeProperties** szakaszban elérhető tulajdonságok az egyes tevékenységtípusok esetében eltérőek. Másolási tevékenység esetén a tulajdonságok az adatforrások típusától és a mosdótól függően változnak.

Másolási tevékenység esetén, ha a forrás **AmazonRedshiftSource** típusú, a következő tulajdonságok érhetők el a **typeProperties** szakaszban:

| Tulajdonság | Leírás | Kötelező |
| --- | --- | --- |
| **lekérdezés** | Az egyéni lekérdezéssel olvashatja el az adatgyűjtést. |Nem (ha meg van adva egy adatkészlet **Táblanév** tulajdonsága) |
| **redshiftUnloadSettings** | A tulajdonságot tartalmazza **a vöröseltolódás eltávolítása** parancs használatakor. | No |
| **s3LinkedServiceName** | Az Amazon S3, amelyet ideiglenes tárolóként kell használni. A társított szolgáltatás egy **awsaccesskey használnia** típusú Azure Data Factory-név használatával van megadva. | A **redshiftUnloadSettings** tulajdonság használatakor szükséges |
| **bucketName** | Azt az Amazon S3 gyűjtőt jelzi, amely az ideiglenes adattároláshoz használható. Ha ez a tulajdonság nincs megadva, a másolási tevékenység automatikusan létrehoz egy gyűjtőt. | A **redshiftUnloadSettings** tulajdonság használatakor szükséges |

Azt is megteheti, hogy a **RelationalSource** típust használja, amely tartalmazza az Amazon vöröseltolódását, és a következő tulajdonsággal rendelkezik a **typeProperties** szakaszban. Megjegyzés: Ez a forrástípus nem támogatja a vöröseltolódás **KItöltése** parancsot.

| Tulajdonság | Leírás | Kötelező |
| --- | --- | --- |
| **lekérdezés** |Az egyéni lekérdezéssel olvashatja el az adatgyűjtést. | Nem (ha meg van adva egy adatkészlet **Táblanév** tulajdonsága) |

## <a name="use-unload-to-copy-data-from-amazon-redshift"></a>Az Eltávolítás használata az adatok az Amazon Vöröseltolódásból való másolásához

Az Amazon Vöröseltolódásának [**eltávolítási**](https://docs.aws.amazon.com/redshift/latest/dg/r_UNLOAD.html) parancsa eltávolít egy lekérdezés eredményét egy vagy több fájlra az Amazon S3-on. Ezt a parancsot az Amazon ajánlja a nagyméretű adathalmazok Vöröseltolódásból való másolásához.

**Példa: adatok másolása az Amazon Vöröseltolódásból az Azure szinapszis Analyticsbe**

Ebben a példában az Amazon vöröseltolódásról származó adatok másolása az Azure szinapszis Analytics szolgáltatásba. A példa a vöröseltolódás **KItöltési** parancsát, az előkészített adatok másolását és a Microsoft Base-t használja.

Ebben a példában a másolási tevékenység először a  **redshiftUnloadSettings** beállításban konfigurált módon távolítja el az Amazon vöröseltolódásról az Amazon S3-be az adatokból. Ezután az adatok az Amazon S3-ból az Azure Blob Storage-ba másolódnak a **stagingSettings** beállításban megadott módon. Végezetül az adatok betöltődik az Azure szinapszis Analyticsbe. Az összes ideiglenes formátumot a másolási tevékenység kezeli.

![Munkafolyamat másolása az Amazon Vöröseltolódásból az Azure szinapszis Analyticsbe](media/data-factory-amazon-redshift-connector/redshift-to-sql-dw-copy-workflow.png)

```json
{
    "name": "CopyFromRedshiftToSQLDW",
    "type": "Copy",
    "typeProperties": {
        "source": {
            "type": "AmazonRedshiftSource",
            "query": "select * from MyTable",
            "redshiftUnloadSettings": {
                "s3LinkedServiceName":"MyAmazonS3StorageLinkedService",
                "bucketName": "bucketForUnload"
            }
        },
        "sink": {
            "type": "SqlDWSink",
            "allowPolyBase": true
        },
        "enableStaging": true,
        "stagingSettings": {
            "linkedServiceName": "MyAzureStorageLinkedService",
            "path": "adfstagingcopydata"
        },
        "cloudDataMovementUnits": 32
        .....
    }
}
```

## <a name="json-example-copy-data-from-amazon-redshift-to-azure-blob-storage"></a>JSON-példa: adatok másolása az Amazon Vöröseltolódásból az Azure Blob Storage-ba
Ez a minta bemutatja, hogyan másolhat adatok egy Amazon vöröseltolódás-adatbázisból az Azure Blob Storageba. Az adatmásolási tevékenység használatával közvetlenül átmásolható a [támogatott](data-factory-data-movement-activities.md#supported-data-stores-and-formats) fogadók adatai.

A minta a következő adatgyári entitásokat tartalmazhatja:

* [AmazonRedshift](#linked-service-properties) típusú társított szolgáltatás
* [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties)típusú társított szolgáltatás.
* [RelationalTable](#dataset-properties) típusú bemeneti [adatkészlet](data-factory-create-datasets.md)
* [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties) típusú kimeneti [adatkészlet](data-factory-create-datasets.md)
* A [RelationalSource](#copy-activity-properties) és a [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties) tulajdonságot használó másolási tevékenységgel rendelkező [folyamat](data-factory-create-pipelines.md)

A minta az Amazon vöröseltolódását egy lekérdezési eredményből másolja egy Azure-blobba óránként. A mintában használt JSON-tulajdonságokat az entitás-definíciókat követő szakaszokban ismertetjük.

**Amazon vöröseltolódás társított szolgáltatás**

```json
{
    "name": "AmazonRedshiftLinkedService",
    "properties":
    {
        "type": "AmazonRedshift",
        "typeProperties":
        {
            "server": "< The IP address or host name of the Amazon Redshift server >",
            "port": "<The number of the TCP port that the Amazon Redshift server uses to listen for client connections.>",
            "database": "<The database name of the Amazon Redshift database>",
            "username": "<username>",
            "password": "<password>"
        }
    }
}
```

**Azure Blob Storage társított szolgáltatás**

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
**Amazon vöröseltolódás bemeneti adatkészlete**

A **külső** tulajdonság értéke "true" (igaz) értékre van állítva, hogy tájékoztassa a Data Factory szolgáltatást arról, hogy az adatkészlet kívül esik az adat-előállítón. Ez a tulajdonság azt jelzi, hogy az adatkészletet nem az adat-előállító tevékenysége állítja elő. Állítsa a tulajdonságot igaz értékre olyan bemeneti adatkészleten, amelyet nem a folyamat tevékenysége hozott létre.

```json
{
    "name": "AmazonRedshiftInputDataset",
    "properties": {
        "type": "RelationalTable",
        "linkedServiceName": "AmazonRedshiftLinkedService",
        "typeProperties": {
            "tableName": "<Table name>"
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true
    }
}
```

**Azure blobkimeneti adatkészlet**

Az új blobba való beírása óránként történik, ha a **Frequency** tulajdonságot "Hour" értékre állítja, az **intervallum** tulajdonság értéke pedig 1. A blob **folderPath** tulajdonsága dinamikusan ki van értékelve. A tulajdonság értéke a feldolgozás alatt álló szelet kezdő időpontján alapul. A mappa elérési útja a kezdési időpont év, hónap, nap és óra részét használja.

```json
{
    "name": "AzureBlobOutputDataSet",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties": {
            "folderPath": "mycontainer/fromamazonredshift/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
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

**Másolási tevékenység egy folyamatba egy Azure Vöröseltolódási forrással (RelationalSource típusú) és egy Azure Blob-fogadóval**

A folyamat egy másolási tevékenységet tartalmaz, amely a bemeneti és a kimeneti adatkészletek használatára van konfigurálva. A folyamat óránkénti futásra van ütemezve. A folyamat JSON-definíciójában a **forrás** típusa **RelationalSource** értékre van állítva, a fogadó **típusa pedig** **BlobSink**. A **lekérdezési** tulajdonsághoz megadott SQL-lekérdezés kiválasztja az előző órában másolandó adatok közül.

```json
{
    "name": "CopyAmazonRedshiftToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [
            {
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "AmazonRedshiftSource",
                        "query": "$$Text.Format('select * from MyTable where timestamp >= \\'{0:yyyy-MM-ddTHH:mm:ss}\\' AND timestamp < \\'{1:yyyy-MM-ddTHH:mm:ss}\\'', WindowStart, WindowEnd)",
                        "redshiftUnloadSettings": {
                            "s3LinkedServiceName":"myS3Storage",
                            "bucketName": "bucketForUnload"
                        }
                    },
                    "sink": {
                        "type": "BlobSink",
                        "writeBatchSize": 0,
                        "writeBatchTimeout": "00:00:00"
                    },
                    "cloudDataMovementUnits": 32
                },
                "inputs": [
                    {
                        "name": "AmazonRedshiftInputDataset"
                    }
                ],
                "outputs": [
                    {
                        "name": "AzureBlobOutputDataSet"
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
                "name": "AmazonRedshiftToBlob"
            }
        ],
        "start": "2014-06-01T18:00:00Z",
        "end": "2014-06-01T19:00:00Z"
    }
}
```
### <a name="type-mapping-for-amazon-redshift"></a>Típus leképezése Amazon vöröseltolódáshoz
Ahogy azt az [adattovábbítási tevékenységek](data-factory-data-movement-activities.md) című cikk ismerteti, a másolási tevékenység a forrás típusa és a fogadó típus közötti automatikus típus-konverziókat hajtja végre. A típusokat két lépésből álló megközelítéssel konvertáljuk:

1. Átalakítás natív forrásból típusról .NET-típusra
2. Átalakítás .NET-típusról natív fogadó típusra

A következő leképezések akkor használatosak, ha a másolási tevékenység egy Amazon Vöröseltolódási típusból .NET-típusra konvertálja az adatait:

| Amazon Vöröseltolódásának típusa | .NET-típus |
| --- | --- |
| SMALLINT |Int16 |
| EGÉSZ SZÁM |Int32 |
| BIGINT |Int64 |
| DECIMÁLIS |Tizedesjegy |
| VALÓS SZÁM |Egyirányú |
| DUPLA PONTOSSÁG |Dupla |
| LOGIKAI |Sztring |
| CHAR |Sztring |
| VARCHAR |Sztring |
| DATE |DateTime |
| IDŐBÉLYEGZŐ |DateTime |
| TEXT |Sztring |

## <a name="map-source-to-sink-columns"></a>Forrás leképezése a fogadó oszlopokra
Ha meg szeretné tudni, hogyan képezhetők le oszlopok a forrás-adatkészletben a fogadó adatkészlet oszlopaiba, tekintse meg a következőt: [adatkészlet oszlopainak megfeleltetése Azure Data Factory](data-factory-map-columns.md).

## <a name="repeatable-reads-from-relational-sources"></a>Ismétlődő olvasások a rokon forrásokból
Amikor Adatmásolást végez egy kapcsolódó adattárból, a nem kívánt eredmények elkerüléséhez tartsa szem előtt az ismétlődést. Azure Data Factory a szeleteket manuálisan is újra futtathatja. Az újrapróbálkozási **szabályzatot** is konfigurálhatja egy adatkészlethez, hogy hiba esetén újra lehessen futtatni a szeleteket. Győződjön meg arról, hogy ugyanazok az információk olvashatók, függetlenül attól, hogy hányszor futtatja újra a szeletet. Győződjön meg arról is, hogy ugyanaz az információ olvasható, függetlenül attól, hogy hogyan futtatja újra a szeletet. További információ: [ismétlődő olvasások a rokon forrásokból](data-factory-repeatable-copy.md#repeatable-read-from-relational-sources).

## <a name="performance-and-tuning"></a>Teljesítmény és finomhangolás
Ismerje meg a másolási tevékenység teljesítményét befolyásoló főbb tényezőket és a teljesítmény optimalizálásának módjait a [másolási tevékenység teljesítményének és hangolási útmutatójának](data-factory-copy-activity-performance.md)használatával.

## <a name="next-steps"></a>Következő lépések
A másolási tevékenységgel rendelkező folyamatok létrehozásával kapcsolatos részletes útmutatásért lásd a [másolási tevékenységről szóló oktatóanyagot](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).
