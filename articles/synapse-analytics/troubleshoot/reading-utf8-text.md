---
title: 'Hibakeresés: UTF-8 szöveg olvasása CSV-vagy PARQUEt-fájlokból kiszolgáló nélküli SQL-készlettel'
description: UTF-8 szöveg olvasása CSV-vagy PARQUEt-fájlokból kiszolgáló nélküli SQL-készlettel az Azure szinapszis Analyticsben
author: julieMSFT
ms.author: jrasnick
ms.topic: troubleshooting
ms.service: synapse-analytics
ms.subservice: sql
ms.date: 12/03/2020
ms.openlocfilehash: 70ce3c82790db0296d5359b5db2e6a323306c309
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96576417"
---
# <a name="troubleshoot-reading-utf-8-text-from-csv-or-parquet-files-using-serverless-sql-pool-in-azure-synapse-analytics"></a>Az UTF-8 szövegnek az Azure szinapszis Analyticsben kiszolgáló nélküli SQL-készlettel való olvasásával kapcsolatban felmerülő hibák

Ez a cikk hibaelhárítási lépéseket tartalmaz az UTF-8 szövegek CSV-vagy Parkett-fájlokból való olvasásához az Azure szinapszis Analytics kiszolgáló nélküli SQL-készletével.

Ha az UTF-8 szöveget egy CSV-vagy Parque-fájlból olvassa be a kiszolgáló nélküli SQL-készlettel, néhány speciális karakter, például az ü és az ö nem kerül konvertálásra, ha a lekérdezés VARCHAR oszlopokat ad vissza nem UTF8 rendezéssel. Ez egy ismert probléma a SQL Server és az Azure SQL-ben. A nem UTF8 rendezés az alapértelmezett a szinapszis SQL-ben, ezért a rendszer az ügyfelek lekérdezéseit fogja érinteni. A standard angol karaktereket és a kiterjesztett latin karakterek egyes részhalmazait használó ügyfelek nem láthatják az átalakítási hibákat. A helytelen konverziót részletesen ismertetjük, [hogy mindig UTF-8 rendezéseket használjon az UTF-8 szöveg olvasásához a kiszolgáló nélküli SQL-készletben](https://techcommunity.microsoft.com/t5/azure-synapse-analytics/always-use-utf-8-collations-to-read-utf-8-text-in-serverless-sql/ba-p/1883633)

## <a name="workaround"></a>Áthidaló megoldás

A probléma megoldása az, hogy mindig UTF-8 rendezést használjon az UTF-8 szöveg CSV-vagy Parque-fájlokból való olvasásakor.

- Sok esetben csak be kell állítania az UTF8-rendezést az adatbázison (metaadat-művelet).

   ```sql
   alter database MyDB
         COLLATE Latin1_General_100_BIN2_UTF8;
   ```

- Explicit módon megadhatja a rendezést a VARCHAR oszlopban a OPENROWSET vagy a külső táblában:

   ```sql
   select geo_id, cases = sum(cases)
   from openrowset(
           bulk 'latest/ecdc_cases.parquet', data_source = 'covid', format = 'parquet'
       ) with ( cases int,
                geo_id VARCHAR(6) COLLATE Latin1_General_100_BIN2_UTF8 ) as rows
   group by geo_id
   ```
 
- Ha az UTF8-adatokat olvasó külső táblákon nem adta meg az UTF8-rendezést, akkor újra létre kell hoznia az érintett külső táblákat, és az UTF8-oszlopokon (metaadat-művelet) kell beállítania az UTF8-rendezést.


## <a name="next-steps"></a>Következő lépések

* [Parketta-fájlok lekérdezése a szinapszis SQL-sel](../sql/query-parquet-files.md)
* [CSV-fájlok lekérdezése a szinapszis SQL-sel](../sql/query-single-csv-file.md)
* [CETAS a szinapszis SQL-sel](../sql/develop-tables-cetas.md)
* [Gyors útmutató: kiszolgáló nélküli SQL-készlet használata](../quickstart-sql-on-demand.md)
