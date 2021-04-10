---
title: Társított szolgáltatások az Azure Data Factoryben
description: A Data Factory társított szolgáltatásainak megismerése. Társított szolgáltatások hivatkozása számítási/adattárakkal a adatok gyárához.
author: dcstwh
ms.author: weetok
ms.reviewer: jburchel
ms.service: data-factory
ms.topic: conceptual
ms.date: 08/21/2020
ms.openlocfilehash: aaa690a4205951bd251a5230721e34fcb960a3b1
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "104782741"
---
# <a name="linked-services-in-azure-data-factory"></a>Társított szolgáltatások az Azure Data Factoryben

> [!div class="op_single_selector" title1="Válassza ki az Ön által használt Data Factory-szolgáltatás verzióját:"]
> * [1-es verzió](v1/data-factory-create-datasets.md)
> * [Aktuális verzió](concepts-linked-services.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Ez a cikk leírja, hogy a társított szolgáltatások milyen módon vannak definiálva a JSON formátumban, és hogyan használják őket Azure Data Factory folyamatokban.

Ha most ismerkedik a Data Factoryával, tekintse meg a [Azure Data Factory bemutatása](introduction.md) című témakört.

## <a name="overview"></a>Áttekintés

A data factory egy vagy több folyamattal rendelkezhet. A **folyamat** olyan **tevékenységek** logikai csoportosítása, amelyek együttesen végeznek feladatokat. A folyamat tevékenységei meghatározzák az adatokon végrehajtandó műveleteket. Előfordulhat például, hogy másolási tevékenységet használ az adatok SQL Serverról az Azure Blob Storage-ba való másolásához. Ezt követően felhasználhat egy kaptár-műveletet, amely egy Azure HDInsight-fürtön futó struktúra-parancsfájlt futtat a blob Storage-beli adatok feldolgozásához a kimeneti adatok előállításához. Végezetül pedig használhat egy második másolási tevékenységet, amellyel a kimeneti adatokat az Azure szinapszis Analyticsbe másolhatja, amelyeken az üzleti intelligencia (BI) jelentéskészítési megoldásai épülnek. A folyamatokkal és tevékenységekkel kapcsolatos további információkért lásd: [folyamatok és tevékenységek](concepts-pipelines-activities.md) Azure Data Factoryban.

Az **adatkészlet** mostantól olyan elnevezett nézet, amely egyszerűen rámutat vagy hivatkozik a **tevékenységekben** használni kívánt adatokra bemenetként és kimenetként.

Adatkészlet létrehozása előtt létre kell hoznia egy **társított szolgáltatást** , amely összekapcsolja az adattárat az adat-előállítóval. A társított szolgáltatások nagyon hasonlóak a kapcsolati sztringekhoz, amelyek meghatározzák azokat a kapcsolati információkat, amelyeket a Data Factory a külső erőforrásokhoz történő csatlakozáshoz igényel. Gondoljon erre; az adatkészlet a társított adattárakon belüli adatszerkezetet jelöli, és a társított szolgáltatás határozza meg az adatforráshoz való kapcsolódást. Egy Azure Storage-beli társított szolgáltatás például egy Storage-fiókhoz csatolja az adatelőállítót. Az Azure Blob-adatkészlet az Azure Storage-fiókban található BLOB-tárolót és mappát jelöli, amely a feldolgozandó bemeneti blobokat tartalmazza.

Példa erre a forgatókönyvre. Az adatok blob Storage-ból SQL Databaseba való másolásához létre kell hoznia két társított szolgáltatást: Azure Storage és Azure SQL Database. Ezután hozzon létre két adatkészletet: az Azure Blob-adatkészletet (amely az Azure Storage társított szolgáltatásra vonatkozik) és az Azure SQL Table-adatkészletet (amely a Azure SQL Database társított szolgáltatásra hivatkozik). Az Azure Storage és a Azure SQL Database társított szolgáltatások olyan kapcsolati karakterláncokat tartalmaznak, amelyeket a futtatókörnyezet az Azure Storage-hoz és a Azure SQL Databasehoz való kapcsolódáshoz Data Factory használ. Az Azure Blob-adatkészlet meghatározza a blob-tárolóban található bemeneti blobokat tartalmazó BLOB-tárolót és blob mappát. Az Azure SQL Table adatkészlet meghatározza a SQL Database azon SQL-tábláját, amelybe az adatokat másolni kívánja.

A következő ábra a folyamat, a tevékenység, az adatkészlet és a társított szolgáltatás közötti kapcsolatokat mutatja Data Factoryban:

![Kapcsolat a folyamat, a tevékenység, az adatkészlet és a társított szolgáltatások között](media/concepts-datasets-linked-services/relationship-between-data-factory-entities.png)

## <a name="linked-service-json"></a>Társított szolgáltatás JSON

Data Factory társított szolgáltatás JSON formátumban van definiálva a következő módon:

```json
{
    "name": "<Name of the linked service>",
    "properties": {
        "type": "<Type of the linked service>",
        "typeProperties": {
              "<data store or compute-specific type properties>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

A fenti JSON-tulajdonságokat a következő táblázat ismerteti:

Tulajdonság | Leírás | Kötelező |
-------- | ----------- | -------- |
name | A társított szolgáltatás neve. Lásd: [Azure Data Factory elnevezési szabályok](naming-rules.md). |  Yes |
típus | A társított szolgáltatás típusa. Például: AzureBlobStorage (adattár) vagy AzureBatch (számítás). Tekintse meg a typeProperties leírását. | Yes |
typeProperties | A típus tulajdonságai különbözőek az egyes adattárokhoz vagy számításokhoz. <br/><br/> A támogatott adattár-típusok és a típusuk tulajdonságai az [Összekötők áttekintése](copy-activity-overview.md#supported-data-stores-and-formats) című cikkben találhatók. Navigáljon az adattár-összekötő cikkhez, és ismerkedjen meg az adattárra jellemző típusok tulajdonságaival. <br/><br/> A támogatott számítási típusok és a hozzájuk tartozó típusok tulajdonságai a következő témakörben találhatók: [számítási társított szolgáltatások](compute-linked-services.md). | Yes |
Connectvia tulajdonsággal | Az adattárhoz való kapcsolódáshoz használt [Integration Runtime](concepts-integration-runtime.md) . Használhat Azure Integration Runtime vagy saját üzemeltetésű Integration Runtime (ha az adattár egy magánhálózaton található). Ha nincs megadva, az alapértelmezett Azure Integration Runtime használja. | No

## <a name="linked-service-example"></a>Társított szolgáltatás – példa

A következő társított szolgáltatás egy Azure Blob Storage-beli társított szolgáltatás. Figyelje meg, hogy a típus az Azure Blob Storage-ra van beállítva. Az Azure Blob Storage társított szolgáltatás típusának tulajdonságai között szerepel egy kapcsolati sztring. A Data Factory szolgáltatás ezt a kapcsolati karakterláncot használja az adattárhoz való kapcsolódáshoz futásidőben.

```json
{
    "name": "AzureBlobStorageLinkedService",
    "properties": {
        "type": "AzureBlobStorage",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="create-linked-services"></a>Társított szolgáltatások létrehozása

A társított szolgáltatások a Azure Data Factory UX-ben hozhatók létre a [felügyeleti központban](author-management-hub.md) , valamint a rájuk hivatkozó tevékenységek, adatkészletek vagy adatfolyamatok használatával.

A társított szolgáltatásokat a következő eszközök vagy SDK-k egyikével hozhatja létre: [.NET API](quickstart-create-data-factory-dot-net.md), [PowerShell](quickstart-create-data-factory-powershell.md), [REST API](quickstart-create-data-factory-rest-api.md), Azure Resource Manager sablon és Azure Portal.


## <a name="data-store-linked-services"></a>Adattárhoz társított szolgáltatások

A Data Factory által támogatott adattárak listáját az [Összekötők áttekintése című](copy-activity-overview.md#supported-data-stores-and-formats) cikkben találja. A támogatott kapcsolatok tulajdonságainak megismeréséhez kattintson egy adattárra.

## <a name="compute-linked-services"></a>Társított szolgáltatások számítása

A [számítási környezetek](compute-linked-services.md) ismertetése a különböző számítási környezetekről nyújt segítséget, amelyekkel kapcsolódhat az adat-előállítóhoz, valamint a különböző konfigurációkhoz.

## <a name="next-steps"></a>Következő lépések

A következő oktatóanyag részletes útmutatást nyújt a folyamatok és adatkészletek létrehozásához ezen eszközök vagy SDK-k egyikének használatával.

- [Gyors útmutató: adat-előállító létrehozása .NET használatával](quickstart-create-data-factory-dot-net.md)
- [Gyors útmutató: adatelőállító létrehozása a PowerShell használatával](quickstart-create-data-factory-powershell.md)
- [Gyors útmutató: adatelőállító létrehozása REST API használatával](quickstart-create-data-factory-rest-api.md)
- [Gyors útmutató: adatelőállító létrehozása Azure Portal használatával](quickstart-create-data-factory-portal.md)
