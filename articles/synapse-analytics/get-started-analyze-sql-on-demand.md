---
title: 'Oktatóanyag: az adatelemzés megkezdése kiszolgáló nélküli SQL-készlettel'
description: Ebből az oktatóanyagból megtudhatja, hogyan elemezheti az információkat egy kiszolgáló nélküli SQL-készlettel a Spark-adatbázisokban található adatforrások használatával.
services: synapse-analytics
author: saveenr
ms.author: saveenr
manager: julieMSFT
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.subservice: sql
ms.topic: tutorial
ms.date: 12/31/2020
ms.openlocfilehash: 5f0a7477df2e281748c053ea8c7e7d3e79626296
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "104588018"
---
# <a name="analyze-data-with-a-serverless-sql-pool"></a>Az adatelemzés kiszolgáló nélküli SQL-készlettel

Ebből az oktatóanyagból megtudhatja, hogyan elemezheti az információkat a kiszolgáló nélküli SQL-készlettel a Spark-adatbázisokban található adatforrások használatával. 

## <a name="the-built-in-serverless-sql-pool"></a>A beépített kiszolgáló nélküli SQL-készlet

A kiszolgáló nélküli SQL-készletek lehetővé teszik az SQL használatát anélkül, hogy kapacitást kellene fenntartania. A kiszolgáló nélküli SQL-készlet számlázása a lekérdezés futtatásához feldolgozott adatmennyiségen alapul, nem a lekérdezés futtatásához használt csomópontok számától függ.

Minden munkaterülethez tartozik egy **beépített,** előre konfigurált kiszolgáló nélküli SQL-készlet. 

## <a name="analyze-nyc-taxi-data-in-blob-storage-using-serverless-sql-pool"></a>NYC-alapú taxi-adatelemzés a blob Storage-ban kiszolgáló nélküli SQL-készlettel

Ebben a szakaszban egy kiszolgáló nélküli SQL-készletet fog használni a New York-i taxi-szolgáltatás Azure Blob Storage-fiókban való elemzéséhez.

1. A szinapszis Studióban nyissa meg a **fejlesztés** hubot
1. Hozzon létre egy új SQL-szkriptet.
1. Illessze be a következő kódot a szkriptbe.

    ```
    SELECT
        TOP 100 *
    FROM
        OPENROWSET(
            BULK     'https://azureopendatastorage.blob.core.windows.net/nyctlc/yellow/puYear=*/puMonth=*/*.parquet',
            FORMAT = 'parquet'
        ) AS [result];
    ```
1. Kattintson a **Futtatás** gombra.

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Az adatelemzés kiszolgáló nélküli Spark-készlettel](get-started-analyze-spark.md)
