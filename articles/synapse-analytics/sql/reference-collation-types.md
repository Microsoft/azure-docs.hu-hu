---
title: Rendezési támogatás
description: Rendezési típusok támogatása a szinapszis SQL-hez az Azure szinapszis Analyticsben
author: filippopovic
ms.service: synapse-analytics
ms.topic: reference
ms.subservice: sql
ms.date: 04/15/2020
ms.author: fipopovi
ms.reviewer: jrasnick
ms.openlocfilehash: 436dbac814197556385a33d956928f97fd4716bf
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "93311917"
---
# <a name="database-collation-support-for-synapse-sql-in-azure-synapse-analytics"></a>Adatbázis-rendezési támogatás a szinapszis SQL-hez az Azure szinapszis Analyticsben 

A rendezések a területi beállítás, a kódlap, a rendezési sorrend és a karakteres adattípusok esetében a karakterek megkülönböztetésére szolgáló szabályokat biztosítják. Ha kiválasztotta, a rendezési adatokat igénylő összes oszlop és kifejezés örökli a kiválasztott rendezést az adatbázis-beállítástól. Az alapértelmezett öröklés felülbírálható úgy, hogy egy karakter alapú adattípus esetében explicit módon megadhat egy másik rendezést.

Az adatbázis alapértelmezett rendezését az új dedikált SQL Pool-adatbázis létrehozásakor módosíthatja a Azure Portal. Ezzel a képességgel még könnyebben hozhat létre egy új adatbázist a 3800 támogatott adatbázis-rendezések egyikével.

Megadhatja az alapértelmezett kiszolgáló nélküli SQL-készlet adatbázis-rendezését a létrehozáskor az adatbázis létrehozása utasítás használatával.

## <a name="change-collation"></a>Rendezés módosítása
A dedikált SQL Pool-adatbázis alapértelmezett rendezésének módosításához frissítsen a rendezés mezőre a létesítési élményben. Ha például meg szeretné változtatni az alapértelmezett rendezést a kis-és nagybetűk megkülönböztetésére, akkor a rendezést SQL_Latin1_General_CP1_CI_ASról SQL_Latin1_General_CP1_CS_ASra kell átnevezni. 

Egy kiszolgáló nélküli SQL Pool-adatbázis alapértelmezett rendezésének módosításához használhatja az ALTER DATABASE utasítást.

## <a name="list-of-unsupported-collation-types"></a>Nem támogatott rendezési típusok listája
*    Japanese_Bushu_Kakusu_140_BIN
*    Japanese_Bushu_Kakusu_140_BIN2
*    Japanese_Bushu_Kakusu_140_CI_AI_VSS
*    Japanese_Bushu_Kakusu_140_CI_AI_WS_VSS
*    Japanese_Bushu_Kakusu_140_CI_AI_KS_VSS
*    Japanese_Bushu_Kakusu_140_CI_AI_KS_WS_VSS
*    Japanese_Bushu_Kakusu_140_CI_AS_VSS
*    Japanese_Bushu_Kakusu_140_CI_AS_WS_VSS
*    Japanese_Bushu_Kakusu_140_CI_AS_KS_VSS
*    Japanese_Bushu_Kakusu_140_CI_AS_KS_WS_VSS
*    Japanese_Bushu_Kakusu_140_CS_AI_VSS
*    Japanese_Bushu_Kakusu_140_CS_AI_WS_VSS
*    Japanese_Bushu_Kakusu_140_CS_AI_KS_VSS
*    Japanese_Bushu_Kakusu_140_CS_AI_KS_WS_VSS
*    Japanese_Bushu_Kakusu_140_CS_AS_VSS
*    Japanese_Bushu_Kakusu_140_CS_AS_WS_VSS
*    Japanese_Bushu_Kakusu_140_CS_AS_KS_VSS
*    Japanese_Bushu_Kakusu_140_CS_AS_KS_WS_VSS
*    Japanese_Bushu_Kakusu_140_CI_AI
*    Japanese_Bushu_Kakusu_140_CI_AI_WS
*    Japanese_Bushu_Kakusu_140_CI_AI_KS
*    Japanese_Bushu_Kakusu_140_CI_AI_KS_WS
*    Japanese_Bushu_Kakusu_140_CI_AS
*    Japanese_Bushu_Kakusu_140_CI_AS_WS
*    Japanese_Bushu_Kakusu_140_CI_AS_KS
*    Japanese_Bushu_Kakusu_140_CI_AS_KS_WS
*    Japanese_Bushu_Kakusu_140_CS_AI
*    Japanese_Bushu_Kakusu_140_CS_AI_WS
*    Japanese_Bushu_Kakusu_140_CS_AI_KS
*    Japanese_Bushu_Kakusu_140_CS_AI_KS_WS
*    Japanese_Bushu_Kakusu_140_CS_AS
*    Japanese_Bushu_Kakusu_140_CS_AS_WS
*    Japanese_Bushu_Kakusu_140_CS_AS_KS
*    Japanese_Bushu_Kakusu_140_CS_AS_KS_WS
*    Japanese_XJIS_140_BIN
*    Japanese_XJIS_140_BIN2
*    Japanese_XJIS_140_CI_AI_VSS
*    Japanese_XJIS_140_CI_AI_WS_VSS
*    Japanese_XJIS_140_CI_AI_KS_VSS
*    Japanese_XJIS_140_CI_AI_KS_WS_VSS
*    Japanese_XJIS_140_CI_AS_VSS
*    Japanese_XJIS_140_CI_AS_WS_VSS
*    Japanese_XJIS_140_CI_AS_KS_VSS
*    Japanese_XJIS_140_CI_AS_KS_WS_VSS
*    Japanese_XJIS_140_CS_AI_VSS
*    Japanese_XJIS_140_CS_AI_WS_VSS
*    Japanese_XJIS_140_CS_AI_KS_VSS
*    Japanese_XJIS_140_CS_AI_KS_WS_VSS
*    Japanese_XJIS_140_CS_AS_VSS
*    Japanese_XJIS_140_CS_AS_WS_VSS
*    Japanese_XJIS_140_CS_AS_KS_VSS
*    Japanese_XJIS_140_CS_AS_KS_WS_VSS
*    Japanese_XJIS_140_CI_AI
*    Japanese_XJIS_140_CI_AI_WS
*    Japanese_XJIS_140_CI_AI_KS
*    Japanese_XJIS_140_CI_AI_KS_WS
*    Japanese_XJIS_140_CI_AS
*    Japanese_XJIS_140_CI_AS_WS
*    Japanese_XJIS_140_CI_AS_KS
*    Japanese_XJIS_140_CI_AS_KS_WS
*    Japanese_XJIS_140_CS_AI
*    Japanese_XJIS_140_CS_AI_WS
*    Japanese_XJIS_140_CS_AI_KS
*    Japanese_XJIS_140_CS_AI_KS_WS
*    Japanese_XJIS_140_CS_AS
*    Japanese_XJIS_140_CS_AS_WS
*    Japanese_XJIS_140_CS_AS_KS
*    Japanese_XJIS_140_CS_AS_KS_WS

Emellett a dedikált SQL-készlet nem támogatja a következő rendezési típusokat:

*    SQL_EBCDIC1141_CP1_CS_AS
*    SQL_EBCDIC277_2_CP1_CS_AS
*    UTF-8

## <a name="check-the-current-collation"></a>Az aktuális rendezés keresése
Az adatbázis aktuális rendezésének ellenőrzését a következő T-SQL-kódrészlet futtatásával végezheti el:
```sql
SELECT DATABASEPROPERTYEX(DB_NAME(), 'Collation') AS Collation;
```
Ha a tulajdonság paraméterként a "rendezés" értéket adta át, a DatabasePropertyEx függvény a megadott adatbázis aktuális rendezését adja vissza. További információ a DatabasePropertyEx függvényről az MSDN webhelyen.

## <a name="next-steps"></a>Következő lépések

A dedikált SQL-készlet és a kiszolgáló nélküli SQL-készlet ajánlott eljárásaival kapcsolatos további információkért tekintse meg a következő cikkeket:

- [Ajánlott eljárások a dedikált SQL-készlethez](best-practices-sql-pool.md)
- [Ajánlott eljárások kiszolgáló nélküli SQL-készlethez](best-practices-sql-on-demand.md)


