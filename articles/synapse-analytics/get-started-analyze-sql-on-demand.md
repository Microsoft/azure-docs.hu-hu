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
ms.openlocfilehash: 7c228bfe5897b45e6345234f2ed8e0f5cfbec73a
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2021
ms.locfileid: "107312790"
---
# <a name="analyze-data-with-a-serverless-sql-pool"></a>Az adatelemzés kiszolgáló nélküli SQL-készlettel

Ebből az oktatóanyagból megtudhatja, hogyan elemezheti az adatelemzést kiszolgáló nélküli SQL-készlettel. 

## <a name="the-built-in-serverless-sql-pool"></a>A beépített kiszolgáló nélküli SQL-készlet

A kiszolgáló nélküli SQL-készletek lehetővé teszik az SQL használatát anélkül, hogy kapacitást kellene fenntartania. A kiszolgáló nélküli SQL-készlet számlázása a lekérdezés futtatásához feldolgozott adatmennyiségen alapul, nem a lekérdezés futtatásához használt csomópontok számától függ.

Minden munkaterülethez tartozik egy **beépített,** előre konfigurált kiszolgáló nélküli SQL-készlet. 

## <a name="analyze-nyc-taxi-data-with-a-serverless-sql-pool"></a>A New York-i taxi-adatbázis elemzése kiszolgáló nélküli SQL-készlettel


1. A szinapszis Studióban nyissa meg a **fejlesztés** hubot
1. Hozzon létre egy új SQL-szkriptet.
1. Illessze be a következő kódot a szkriptbe.

    ```
    SELECT
        TOP 100 *
    FROM
        OPENROWSET(
                BULK 'https://contosolake.dfs.core.windows.net/users/NYCTripSmall.parquet',
            FORMAT='PARQUET'
        ) AS [result]
    ```
1. Kattintson a **Futtatás** gombra.

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Az adatelemzés kiszolgáló nélküli Spark-készlettel](get-started-analyze-spark.md)
