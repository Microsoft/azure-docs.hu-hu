---
title: Az adatraktár rendezési típusai
description: Az Azure szinapszis Analyticsben dedikált SQL-készlethez támogatott rendezési típusok.
services: synapse-analytics
author: antvgski
manager: igorstan
ms.service: synapse-analytics
ms.subservice: sql
ms.topic: conceptual
ms.date: 12/04/2019
ms.author: anvang
ms.reviewer: jrasnick
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: 8d76d70c4c84f1d63d4643e1fba3f66d3541beff
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/04/2020
ms.locfileid: "93305250"
---
# <a name="database-collation-support-for-dedicated-sql-pool-in-azure-synapse-analytics"></a>Adatbázis-rendezési támogatás a dedikált SQL-készlethez az Azure szinapszis Analyticsben 

Az adatbázis alapértelmezett rendezését az új dedikált SQL Pool-adatbázis létrehozásakor módosíthatja a Azure Portal. Ezzel a képességgel még könnyebben hozhat létre egy új adatbázist a 3800 támogatott adatbázis-rendezések egyikével.

A rendezések lehetővé teszik a területi beállítás, a kódlap, a rendezési sorrend és a karakteres adattípusok megkülönböztetésére vonatkozó szabályok megadását. Ha kiválasztotta, a rendezési adatokat igénylő összes oszlop és kifejezés örökli a kiválasztott rendezést az adatbázis-beállítástól. Az alapértelmezett öröklés felülbírálható úgy, hogy egy karakter alapú adattípus esetében explicit módon megadhat egy másik rendezést.

## <a name="changing-collation"></a>Rendezés módosítása

Ha módosítani szeretné az alapértelmezett rendezést, frissítse a rendezés mezőt a létesítési élményben.

Ha például meg szeretné változtatni az alapértelmezett rendezést a kis-és nagybetűk megkülönböztetésére, egyszerűen nevezze át a rendezést SQL_Latin1_General_CP1_CI_ASról SQL_Latin1_General_CP1_CS_ASra.

## <a name="list-of-unsupported-collation-types"></a>Nem támogatott rendezési típusok listája

* Japanese_Bushu_Kakusu_140_BIN
* Japanese_Bushu_Kakusu_140_BIN2
* Japanese_Bushu_Kakusu_140_CI_AI_VSS
* Japanese_Bushu_Kakusu_140_CI_AI_WS_VSS
* Japanese_Bushu_Kakusu_140_CI_AI_KS_VSS
* Japanese_Bushu_Kakusu_140_CI_AI_KS_WS_VSS
* Japanese_Bushu_Kakusu_140_CI_AS_VSS
* Japanese_Bushu_Kakusu_140_CI_AS_WS_VSS
* Japanese_Bushu_Kakusu_140_CI_AS_KS_VSS
* Japanese_Bushu_Kakusu_140_CI_AS_KS_WS_VSS
* Japanese_Bushu_Kakusu_140_CS_AI_VSS
* Japanese_Bushu_Kakusu_140_CS_AI_WS_VSS
* Japanese_Bushu_Kakusu_140_CS_AI_KS_VSS
* Japanese_Bushu_Kakusu_140_CS_AI_KS_WS_VSS
* Japanese_Bushu_Kakusu_140_CS_AS_VSS
* Japanese_Bushu_Kakusu_140_CS_AS_WS_VSS
* Japanese_Bushu_Kakusu_140_CS_AS_KS_VSS
* Japanese_Bushu_Kakusu_140_CS_AS_KS_WS_VSS
* Japanese_Bushu_Kakusu_140_CI_AI
* Japanese_Bushu_Kakusu_140_CI_AI_WS
* Japanese_Bushu_Kakusu_140_CI_AI_KS
* Japanese_Bushu_Kakusu_140_CI_AI_KS_WS
* Japanese_Bushu_Kakusu_140_CI_AS
* Japanese_Bushu_Kakusu_140_CI_AS_WS
* Japanese_Bushu_Kakusu_140_CI_AS_KS
* Japanese_Bushu_Kakusu_140_CI_AS_KS_WS
* Japanese_Bushu_Kakusu_140_CS_AI
* Japanese_Bushu_Kakusu_140_CS_AI_WS
* Japanese_Bushu_Kakusu_140_CS_AI_KS
* Japanese_Bushu_Kakusu_140_CS_AI_KS_WS
* Japanese_Bushu_Kakusu_140_CS_AS
* Japanese_Bushu_Kakusu_140_CS_AS_WS
* Japanese_Bushu_Kakusu_140_CS_AS_KS
* Japanese_Bushu_Kakusu_140_CS_AS_KS_WS
* Japanese_XJIS_140_BIN
* Japanese_XJIS_140_BIN2
* Japanese_XJIS_140_CI_AI_VSS
* Japanese_XJIS_140_CI_AI_WS_VSS
* Japanese_XJIS_140_CI_AI_KS_VSS
* Japanese_XJIS_140_CI_AI_KS_WS_VSS
* Japanese_XJIS_140_CI_AS_VSS
* Japanese_XJIS_140_CI_AS_WS_VSS
* Japanese_XJIS_140_CI_AS_KS_VSS
* Japanese_XJIS_140_CI_AS_KS_WS_VSS
* Japanese_XJIS_140_CS_AI_VSS
* Japanese_XJIS_140_CS_AI_WS_VSS
* Japanese_XJIS_140_CS_AI_KS_VSS
* Japanese_XJIS_140_CS_AI_KS_WS_VSS
* Japanese_XJIS_140_CS_AS_VSS
* Japanese_XJIS_140_CS_AS_WS_VSS
* Japanese_XJIS_140_CS_AS_KS_VSS
* Japanese_XJIS_140_CS_AS_KS_WS_VSS
* Japanese_XJIS_140_CI_AI
* Japanese_XJIS_140_CI_AI_WS
* Japanese_XJIS_140_CI_AI_KS
* Japanese_XJIS_140_CI_AI_KS_WS
* Japanese_XJIS_140_CI_AS
* Japanese_XJIS_140_CI_AS_WS
* Japanese_XJIS_140_CI_AS_KS
* Japanese_XJIS_140_CI_AS_KS_WS
* Japanese_XJIS_140_CS_AI
* Japanese_XJIS_140_CS_AI_WS
* Japanese_XJIS_140_CS_AI_KS
* Japanese_XJIS_140_CS_AI_KS_WS
* Japanese_XJIS_140_CS_AS
* Japanese_XJIS_140_CS_AS_WS
* Japanese_XJIS_140_CS_AS_KS
* Japanese_XJIS_140_CS_AS_KS_WS
* SQL_EBCDIC1141_CP1_CS_AS
* SQL_EBCDIC277_2_CP1_CS_AS

## <a name="checking-the-current-collation"></a>Az aktuális rendezés ellenőrzése

Az adatbázis aktuális rendezésének ellenőrzését a következő T-SQL-kódrészlet futtatásával végezheti el:

```sql
SELECT DATABASEPROPERTYEX(DB_NAME(), 'Collation') AS Collation;
```

Ha a tulajdonság paraméterként a "rendezés" értéket adta át, a DatabasePropertyEx függvény a megadott adatbázis aktuális rendezését adja vissza. További információ: [DatabasePropertyEx](/sql/t-sql/functions/databasepropertyex-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest).
