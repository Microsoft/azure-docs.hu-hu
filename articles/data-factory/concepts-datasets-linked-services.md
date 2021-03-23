---
title: Adathalmazok
description: Tudnivalók a Data Factory adatkészletekről. Az adatkészletek bemeneti/kimeneti adatokat jelölnek.
author: dcstwh
ms.author: weetok
ms.reviewer: jburchel
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 08/24/2020
ms.openlocfilehash: 167fb0014f2f0a9e7a2530fe276289f94347146e
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/22/2021
ms.locfileid: "104785784"
---
# <a name="datasets-in-azure-data-factory"></a>Adathalmazok az Azure Data Factoryben
> [!div class="op_single_selector" title1="Válassza ki az Ön által használt Data Factory-szolgáltatás verzióját:"]
> * [1-es verzió](v1/data-factory-create-datasets.md)
> * [Aktuális verzió](concepts-datasets-linked-services.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]


Ez a cikk ismerteti, hogy mely adatkészletek, hogyan vannak meghatározva JSON formátumban, és hogyan használják őket Azure Data Factory folyamatokban.

Ha Data Factory új, tekintse meg az áttekintést a [Azure Data Factory bemutatása](introduction.md) című témakört.

## <a name="overview"></a>Áttekintés
A data factory egy vagy több folyamattal rendelkezhet. A **folyamat** olyan **tevékenységek** logikai csoportosítása, amelyek együttesen végeznek feladatokat. A folyamat tevékenységei meghatározzák az adatokon végrehajtandó műveleteket. Az **adatkészlet** mostantól olyan elnevezett nézet, amely egyszerűen rámutat vagy hivatkozik a **tevékenységekben** használni kívánt adatokra bemenetként és kimenetként. Az adatkészletek adatokat határoznak meg a különböző adattárakban, például táblákban, fájlokban, mappákban és dokumentumokban. Az Azure Blob-adatkészlet például meghatározza a blobtárolót és azt a Blob Storage-mappát, amelyből a tevékenység beolvassa az adatokat.

Adatkészlet létrehozása előtt létre kell hoznia egy [**társított szolgáltatást**](concepts-linked-services.md) , amely összekapcsolja az adattárat az adat-előállítóval. A társított szolgáltatások nagyon hasonlóak a kapcsolati sztringekhoz, amelyek meghatározzák azokat a kapcsolati információkat, amelyeket a Data Factory a külső erőforrásokhoz történő csatlakozáshoz igényel. Gondoljon erre; az adatkészlet a társított adattárakon belüli adatszerkezetet jelöli, és a társított szolgáltatás határozza meg az adatforráshoz való kapcsolódást. Egy Azure Storage-beli társított szolgáltatás például egy Storage-fiókhoz csatolja az adatelőállítót. Az Azure Blob-adatkészlet az Azure Storage-fiókban található BLOB-tárolót és mappát jelöli, amely a feldolgozandó bemeneti blobokat tartalmazza.

Példa erre a forgatókönyvre. Az adatok blob Storage-ból SQL Databaseba való másolásához létre kell hoznia két társított szolgáltatást: Azure Blob Storage és Azure SQL Database. Ezután hozzon létre két adatkészletet: tagolt szöveges adatkészlet (amely az Azure Blob Storage társított szolgáltatásra hivatkozik, feltételezve, hogy a szöveges fájlok forrásaként szerepelnek) és az Azure SQL Table adatkészlet (amely a Azure SQL Database társított szolgáltatásra hivatkozik). Az Azure Blob Storage és az Azure SQL Database társított szolgáltatások olyan kapcsolati karakterláncokat tartalmaznak, amelyek Data Factory a futtatókörnyezet használatával csatlakoznak az Azure Storage-hoz és a Azure SQL Databasehoz. A tagolt szöveges adatkészlet meghatározza a blob-tárolóban található bemeneti blobokat, valamint a formázással kapcsolatos beállításokat tartalmazó BLOB-tárolót és blob mappát. Az Azure SQL Table adatkészlet meghatározza a SQL Database azon SQL-tábláját, amelybe az adatokat másolni kívánja.

A következő ábra a folyamat, a tevékenység, az adatkészlet és a társított szolgáltatás közötti kapcsolatokat mutatja Data Factoryban:

![Kapcsolat a folyamat, a tevékenység, az adatkészlet és a társított szolgáltatások között](media/concepts-datasets-linked-services/relationship-between-data-factory-entities.png)


## <a name="dataset-json"></a>Adatkészlet JSON
Data Factoryban található adatkészlet a következő JSON-formátumban van definiálva:

```json
{
    "name": "<name of dataset>",
    "properties": {
        "type": "<type of dataset: DelimitedText, AzureSqlTable etc...>",
        "linkedServiceName": {
                "referenceName": "<name of linked service>",
                "type": "LinkedServiceReference",
        },
        "schema":[

        ],
        "typeProperties": {
            "<type specific property>": "<value>",
            "<type specific property 2>": "<value 2>",
        }
    }
}
```
A fenti JSON-tulajdonságokat a következő táblázat ismerteti:

Tulajdonság | Leírás | Kötelező |
-------- | ----------- | -------- |
name | Az adatkészlet neve. Lásd: [Azure Data Factory elnevezési szabályok](naming-rules.md). |  Igen |
típus | Az adatkészlet típusa. A Data Factory által támogatott típusok egyikét kell megadnia (például: DelimitedText, tulajdonsága azuresqltable). <br/><br/>Részletekért lásd: [adatkészletek típusai](#dataset-type). | Igen |
schema | Az adatkészlet sémája a fizikai adattípust és-alakzatot jelöli. | Nem |
typeProperties | A típus tulajdonságai eltérőek az egyes típusoknál. A támogatott típusokkal és azok tulajdonságaival kapcsolatos részletekért lásd: [adatkészlet típusa](#dataset-type). | Igen |

Az adatkészlet sémájának importálásakor kattintson a **séma importálása** gombra, és válassza az Importálás a forrásból vagy egy helyi fájlból lehetőséget. A legtöbb esetben közvetlenül a forrásból importálja a sémát. Ha azonban már van helyi sémafájl (egy parketta-fájl vagy egy, a fejlécekkel rendelkező CSV), akkor a Data Factory a fájlra alapozva kiválaszthatja a sémát.

A másolási tevékenységben az adatkészletek a forrás és a fogadó területen használatosak. Az adatkészletben definiált séma nem kötelező hivatkozás. Ha a forrás és a fogadó közötti oszlop/mező hozzárendelést kívánja alkalmazni, tekintse meg a [séma és típus leképezése](copy-activity-schema-and-type-mapping.md)című témakört.

Az adatforgalomban az adatkészletek a forrás-és fogadó átalakításokban használatosak. Az adatkészletek határozzák meg az alapszintű adatsémákat. Ha az adatai nem rendelkeznek sémával, a forrás és a fogadó esetében használhatja a séma eltolódását. Az adatkészletek metaadatai a forrás-átalakulásban jelennek meg a forrás-kivetítés során. A forrás-átalakításban a kivetítés a definiált nevekkel és típusokkal rendelkező adatfolyam-adatforgalomra vonatkozik.

## <a name="dataset-type"></a>Adatkészlet típusa

A Azure Data Factory számos különböző típusú adatkészletet támogat, a használt adattáraktól függően. A Data Factory által támogatott adattárak listáját az [Összekötők áttekintése című](connector-overview.md) cikkben találja. Kattintson egy adattárra, és Ismerje meg, hogyan hozhat létre egy társított szolgáltatást és egy adatkészletet.

Egy tagolt szöveges adatkészlet esetében például az adatkészlet típusa **DelimitedText** értékre van állítva, ahogy az a következő JSON-mintában látható:

```json
{
    "name": "DelimitedTextInput",
    "properties": {
        "linkedServiceName": {
            "referenceName": "AzureBlobStorage",
            "type": "LinkedServiceReference"
        },
        "annotations": [],
        "type": "DelimitedText",
        "typeProperties": {
            "location": {
                "type": "AzureBlobStorageLocation",
                "fileName": "input.log",
                "folderPath": "inputdata",
                "container": "adfgetstarted"
            },
            "columnDelimiter": ",",
            "escapeChar": "\\",
            "quoteChar": "\""
        },
        "schema": []
    }
}
```

## <a name="create-datasets"></a>Adatkészletek létrehozása
Adatkészleteket az alábbi eszközök vagy SDK-k egyikével hozhat létre: [.NET API](quickstart-create-data-factory-dot-net.md), [PowerShell](quickstart-create-data-factory-powershell.md), [REST API](quickstart-create-data-factory-rest-api.md), Azure Resource Manager sablon és Azure Portal

## <a name="current-version-vs-version-1-datasets"></a>Az aktuális verzió és az 1. verzió adatkészletei

Íme néhány különbség a Data Factory és a Data Factory 1. verziójú adatkészletek között:

- A külső tulajdonság nem támogatott a jelenlegi verzióban. Egy [trigger](concepts-pipeline-execution-triggers.md)váltja fel.
- A házirend és a rendelkezésre állási tulajdonságok nem támogatottak az aktuális verzióban. A folyamat kezdő időpontja az [eseményindítótól](concepts-pipeline-execution-triggers.md)függ.
- A hatókörrel rendelkező adatkészletek (a folyamatokban megadott adatkészletek) nem támogatottak az aktuális verzióban.

## <a name="next-steps"></a>Következő lépések
A következő oktatóanyag részletes útmutatást nyújt a folyamatok és adatkészletek létrehozásához ezen eszközök vagy SDK-k egyikének használatával.

- [Gyors útmutató: adat-előállító létrehozása .NET használatával](quickstart-create-data-factory-dot-net.md)
- [Gyors útmutató: adatelőállító létrehozása a PowerShell használatával](quickstart-create-data-factory-powershell.md)
- [Gyors útmutató: adatelőállító létrehozása REST API használatával](quickstart-create-data-factory-rest-api.md)
- [Gyors útmutató: adatelőállító létrehozása Azure Portal használatával](quickstart-create-data-factory-portal.md)
