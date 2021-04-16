---
title: 'Oktatóanyag: Adatok elemzése kiszolgáló nélküli SQL-készlet használatával – első lépések'
description: Ez az oktatóanyag bemutatja, hogyan elemezhet adatokat egy kiszolgáló nélküli SQL-készletben a Spark-adatbázisokban található adatok használatával.
services: synapse-analytics
author: saveenr
ms.author: saveenr
manager: julieMSFT
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.subservice: sql
ms.topic: tutorial
ms.date: 04/15/2021
ms.openlocfilehash: c6f2dfe0d4846227400ac9b3c7ac3e6ead8f0b57
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2021
ms.locfileid: "107567553"
---
# <a name="analyze-data-with-a-serverless-sql-pool"></a>Adatok elemzése kiszolgáló nélküli SQL-készlet használatával

Ez az oktatóanyag bemutatja, hogyan elemezhet adatokat kiszolgáló nélküli SQL-készlet használatával. 

## <a name="the-built-in-serverless-sql-pool"></a>A beépített kiszolgáló nélküli SQL-készlet

A kiszolgáló nélküli SQL-készletek segítségével kapacitás lefoglalása nélkül használhatja az SQL-t. A kiszolgáló nélküli SQL-készlet számlázása a lekérdezés futtatásához feldolgozott adatmennyiségen alapul, nem a lekérdezés futtatásához használt csomópontok számán.

Minden munkaterülethez tartozik egy beépített nevű előre konfigurált kiszolgáló nélküli **SQL-készlet.** 

## <a name="analyze-nyc-taxi-data-with-a-serverless-sql-pool"></a>NYC Taxi adatainak elemzése kiszolgáló nélküli SQL-készlet használatával

1. A Synapse Studio a Develop **(Fejlesztés) központba**
1. Hozzon létre egy új SQL-szkriptet.
1. Illessze be az alábbi kódot a szkriptbe.

    ```
    SELECT
        TOP 100 *
    FROM
        OPENROWSET(
            BULK 'https://contosolake.dfs.core.windows.net/users/NYCTripSmall.parquet',
            FORMAT='PARQUET'
        ) AS [result]
    ```
1. Kattintson a **Futtatás** elemre. 

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Adatok elemzése kiszolgáló nélküli Spark-készletben](get-started-analyze-spark.md)
