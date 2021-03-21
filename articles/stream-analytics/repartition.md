---
title: Újraparticionálás használata Azure Stream Analytics feladatok optimalizálása érdekében
description: Ez a cikk azt ismerteti, hogyan lehet az újraparticionálással optimalizálni Azure Stream Analytics feladatokat, amelyeket nem lehet párhuzamosan használni.
ms.service: stream-analytics
author: sidramadoss
ms.author: sidram
ms.date: 03/04/2021
ms.topic: conceptual
ms.custom: mvc
ms.openlocfilehash: 95749f2acea6b605cfdba5a4f3d4f5526e751c5a
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102182536"
---
# <a name="use-repartitioning-to-optimize-processing-with-azure-stream-analytics"></a>Az újraparticionálással optimalizálja a feldolgozást Azure Stream Analytics

Ebből a cikkből megtudhatja, hogy az újraparticionálással hogyan méretezheti a Azure Stream Analytics-lekérdezést olyan forgatókönyvek esetében, amelyek nem lehetnek teljesen [párhuzamosak](stream-analytics-scale-jobs.md).

Előfordulhat, hogy nem tudja használni a [párhuzamos](stream-analytics-parallelization.md) , ha:

* A bemeneti adatfolyamhoz tartozó partíciós kulcs nem szabályozható.
* A forrás "spray" bemenete több partíción keresztül történik, amelyeket később egyesíteni kell.

Az újraparticionálás vagy az újrakeverés szükséges, ha olyan adatfolyamon dolgoz fel adatokat, amely nem egy természetes bemeneti séma alapján van felosztva, például Event Hubs **PartitionID** . Az újraparticionáláskor az egyes szegmensek egymástól függetlenül dolgozhatók fel, ami lehetővé teszi a folyamatos átviteli folyamat lineáris felskálázását. 

## <a name="how-to-repartition"></a>Újraparticionálás
A bemenet 2 módon particionálható:
1. Használjon egy külön Stream Analytics feladatot, amely az újraparticionálást végzi
2. Egyetlen feladatot használjon, de először az egyéni elemzési logika előtt végezze el az újraparticionálást.

### <a name="creating-a-separate-stream-analytics-job-to-repartition-input"></a>Külön Stream Analytics feladatok létrehozása a bemenet újraparticionálásához
Létrehozhat egy feladatot, amely beolvassa a bemeneteket, és az Event hub kimenetére ír egy partíciós kulcs használatával. Az Event hub ezután bemenetként szolgálhat egy másik Stream Analytics feladathoz, ahol megvalósíthatja az elemzési logikát. Az Event hub kimenetének a feladatokban való konfigurálásakor meg kell adnia azt a partíciós kulcsot, amellyel a Stream Analytics az adatok újraparticionálását végzi. 
```sql
-- For compat level 1.2 or higher
SELECT * 
INTO output
FROM input

--For compat level 1.1 or lower
SELECT *
INTO output
FROM input PARTITION BY PartitionId
```

### <a name="repartition-input-within-a-single-stream-analytics-job"></a>A bemenet újraparticionálása egyetlen Stream Analytics-feladaton belül
Olyan lépést is bevezet a lekérdezésben, amely először újraparticionálja a bemenetet, és ezt a lekérdezés más lépései is használhatják. Ha például a (z) **DeviceID** alapján szeretné újraparticionálni a bemenetet, a lekérdezés a következő lesz:
```sql
WITH RepartitionedInput AS 
( 
SELECT * 
FROM input PARTITION BY DeviceID
)

SELECT DeviceID, AVG(Reading) as AvgNormalReading  
INTO output
FROM RepartitionedInput  
GROUP BY DeviceId, TumblingWindow(minute, 1)  
```

A következő példa két újraparticionált adatstreamet egyesít. Két újraparticionált adatstream csatlakoztatásakor a streameknek ugyanazzal a partíciós kulccsal és számmal kell rendelkezniük. Az eredmény egy olyan adatfolyam, amely ugyanazzal a partíciós sémával rendelkezik.

```sql
WITH step1 AS (SELECT * FROM input1 PARTITION BY DeviceID),
step2 AS (SELECT * FROM input2 PARTITION BY DeviceID)

SELECT * INTO output FROM step1 PARTITION BY DeviceID UNION step2 PARTITION BY DeviceID
```

A kimeneti sémának meg kell egyeznie az adatfolyam-séma kulcsával és számával, hogy az egyes alstreamek egymástól függetlenül is kiüríthető legyen. Az adatfolyamot egy másik séma is egyesítheti és újraparticionálhatja a kiürítés előtt, de el kell kerülnie ezt a metódust, mivel az felveszi a feldolgozás általános késését, és növeli az erőforrás-kihasználtságot.

## <a name="streaming-units-for-repartitions"></a>Újraparticionálások folyamatos átviteli egységei

Kísérletezzen és figyelje meg a feladatok erőforrás-használatát a szükséges partíciók pontos számának megállapításához. A [folyamatos átviteli egységek (su)](stream-analytics-streaming-unit-consumption.md) számát az egyes partíciók számára szükséges fizikai erőforrások alapján kell beállítani. Általánosságban elmondható, hogy az egyes partíciók esetében hat SUs szükséges. Ha nincs elegendő erőforrás rendelve a feladathoz, a rendszer csak akkor alkalmazza az újraparticionálást, ha a feladat előnyeit biztosítja.

## <a name="repartitions-for-sql-output"></a>SQL-kimenet újraparticionálása

Ha a feladata SQL Database-t használ a kimenethez, az átviteli sebesség maximalizálása érdekében használjon explicit újraparticionálást az optimális partíciók számának megfelelően. Mivel az SQL nyolc író használatával működik a legjobban, a folyamat átparticionálása a kiürítés előtt nyolcra, vagy valahol további upstream, a feladatok teljesítményére is felhasználható. 

Ha több mint 8 bemeneti partíció van, akkor előfordulhat, hogy a bemeneti particionálási séma öröklése nem megfelelő választás. Vegye fontolóra [a-ben való](/stream-analytics-query/into-azure-stream-analytics#into-shard-count) használatát a lekérdezésben, hogy explicit módon adja meg a kimeneti írók számát. 

A következő példa beolvassa a bevitelt, függetlenül attól, hogy a rendszer természetesen particionálja, és újraparticionálja a stream tízszeresére a DeviceID dimenzió alapján, és kiüríti az adatokat a kimenetre. 

```sql
SELECT * INTO [output] FROM [input] PARTITION BY DeviceID INTO 10
```

További információ: [Azure stream Analytics kimenet Azure SQL Database](stream-analytics-sql-output-perf.md).


## <a name="next-steps"></a>Következő lépések

* [Ismerkedés a Azure Stream Analytics](stream-analytics-introduction.md)
* [A lekérdezési párhuzamos kihasználása Azure Stream Analytics](stream-analytics-parallelization.md)
