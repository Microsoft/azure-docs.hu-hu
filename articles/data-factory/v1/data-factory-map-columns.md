---
title: Adatkészlet oszlopainak megfeleltetése Azure Data Factory
description: Ismerje meg, hogyan képezhető le a forrás oszlopai a cél oszlopokra.
author: linda33wj
ms.author: jingwang
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/10/2018
robots: noindex
ms.openlocfilehash: fef2c6f120ae25e6aa1846d4971ff707da9bab92
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "100371124"
---
# <a name="map-source-dataset-columns-to-destination-dataset-columns"></a>Forrás adatkészlet oszlopainak leképezése a cél adatkészlet oszlopaira
> [!NOTE]
> Ez a cikk a Data Factory 1-es verziójára vonatkozik. 

Az oszlop-hozzárendeléssel megadhatja, hogy a forrástábla "szerkezetében" milyen oszlopok legyenek megadva a fogadó tábla "Structure" oszlopában. A **columnMapping** tulajdonság a másolási tevékenység **typeProperties** szakaszában érhető el.

Az oszlop-hozzárendelés a következő forgatókönyveket támogatja:

* A forrás adatkészlet struktúrájában lévő összes oszlop a fogadó adatkészlet struktúrájában lévő összes oszlopra van leképezve.
* A forrás-adatkészlet struktúrájában lévő oszlopok egy részhalmaza a fogadó adatkészlet struktúrájában lévő összes oszlopra van leképezve.

A következő hibák a kivételt eredményezik:

* Kevesebb oszlop vagy több oszlop szerepel a (z) "Structure" táblában a leképezésben megadottnél.
* Ismétlődő leképezés.
* Az SQL-lekérdezés eredménye nem rendelkezik a leképezésben megadott oszlopnevet.

> [!NOTE]
> A következő minták az Azure SQL és az Azure Blob esetében használhatók, de minden olyan adattárra alkalmazhatók, amely támogatja a téglalap alakú adatkészleteket. Az adatkészlet és a társított szolgáltatás definícióinak módosítása példákban, hogy a megfelelő adatforrásban lévő adatokat mutassanak.

## <a name="sample-1--column-mapping-from-azure-sql-to-azure-blob"></a>1. példa – oszlop-hozzárendelés az Azure SQL-ből az Azure blobba
Ebben a példában a bemeneti tábla egy struktúrát tartalmaz, és egy Azure SQL Database SQL-táblára mutat.

```json
{
    "name": "AzureSQLInput",
    "properties": {
        "structure": 
         [
           { "name": "userid"},
           { "name": "name"},
           { "name": "group"}
         ],
        "type": "AzureSqlTable",
        "linkedServiceName": "AzureSqlLinkedService",
        "typeProperties": {
            "tableName": "MyTable"
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

Ebben a példában a kimeneti tábla egy szerkezettel rendelkezik, amely egy Azure Blob Storage-beli blobra mutat.

```json
{
    "name": "AzureBlobOutput",
    "properties":
    {
         "structure": 
          [
                { "name": "myuserid"},
                { "name": "myname" },
                { "name": "mygroup"}
          ],
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
            "folderPath": "mycontainer/myfolder",
            "fileName":"myfile.csv",
            "format":
            {
                "type": "TextFormat",
                "columnDelimiter": ","
            }
        },
        "availability":
        {
            "frequency": "Hour",
            "interval": 1
        }
    }
}
```

A következő JSON a másolási tevékenységet definiálja egy folyamaton belül. A forrás oszlopai a fogadó (**columnMappings**) oszlopokra vannak leképezve a **Translator** tulajdonság használatával.

```json
{
    "name": "CopyActivity",
    "description": "description", 
    "type": "Copy",
    "inputs":  [ { "name": "AzureSQLInput"  } ],
    "outputs":  [ { "name": "AzureBlobOutput" } ],
    "typeProperties":    {
        "source":
        {
            "type": "SqlSource"
        },
        "sink":
        {
            "type": "BlobSink"
        },
        "translator": 
        {
            "type": "TabularTranslator",
            "ColumnMappings": "UserId: MyUserId, Group: MyGroup, Name: MyName"
        }
    },
   "scheduler": {
          "frequency": "Hour",
          "interval": 1
        }
}
```
**Oszlop-hozzárendelési folyamat:**

![Oszlop-hozzárendelési folyamat](./media/data-factory-map-columns/column-mapping-flow.png)

## <a name="sample-2--column-mapping-with-sql-query-from-azure-sql-to-azure-blob"></a>2. minta – oszlop-hozzárendelés SQL-lekérdezéssel az Azure SQL-ből az Azure blobba
Ebben a példában egy SQL-lekérdezést használunk az adatok Azure SQL-ből való kinyerésére ahelyett, hogy egyszerűen megadta a tábla nevét és az oszlopnevek nevét a "Structure" (struktúra) szakaszban. 

```json
{
    "name": "CopyActivity",
    "description": "description", 
    "type": "CopyActivity",
    "inputs":  [ { "name": " AzureSQLInput"  } ],
    "outputs":  [ { "name": " AzureBlobOutput" } ],
    "typeProperties":
    {
        "source":
        {
            "type": "SqlSource",
            "SqlReaderQuery": "$$Text.Format('SELECT * FROM MyTable WHERE StartDateTime = \\'{0:yyyyMMdd-HH}\\'', WindowStart)"
        },
        "sink":
        {
            "type": "BlobSink"
        },
        "Translator": 
        {
            "type": "TabularTranslator",
            "ColumnMappings": "UserId: MyUserId, Group: MyGroup,Name: MyName"
        }
    },
    "scheduler": {
          "frequency": "Hour",
          "interval": 1
        }
}
```
Ebben az esetben a lekérdezés eredményei először a forrás "struktúra" részében megadott oszlopokra vannak leképezve. Ezt követően a "Structure" forrás oszlopai a columnMappings-ben megadott szabályok szerint vannak leképezve a "Structure" oszlopba.  Tegyük fel, hogy a lekérdezés 5 oszlopot ad vissza, a forrás "struktúra" részében megadott két további oszlopot.

**Oszlop-hozzárendelési folyamat**

![Oszlop-hozzárendelési folyamat – 2](./media/data-factory-map-columns/column-mapping-flow-2.png)

## <a name="next-steps"></a>Következő lépések
A másolási tevékenység használatával kapcsolatos oktatóanyagért tekintse meg a következő cikket: 

- [Adatok másolása Blob Storageból SQL Database](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
