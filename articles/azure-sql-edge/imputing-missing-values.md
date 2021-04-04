---
title: Kitöltési idő hiányosságai és a hiányzó értékek beszámítása – Azure SQL Edge
description: Ismerje meg, hogyan tölthetők le az idő és a hiányzó értékek az Azure SQL Edge-ben
keywords: SQL Edge, idősor
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 09/22/2020
ms.openlocfilehash: c444732a497d595235ac337d7f5c71fb84f17cca
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96185589"
---
# <a name="filling-time-gaps-and-imputing-missing-values"></a>Az időbeli hézagok kitöltése és az imputált hiányzó értékek 

Az idősorozat-adatok kezelésekor gyakran lehetséges, hogy az idősorozat-adatok hiányoznak az attribútumok értékei. Az is előfordulhat, hogy az adathalmazban az adat jellegéből adódóan vagy az adatgyűjtés megszakítása miatt is *van időkorlát.*

Ha például egy intelligens eszköz energiafogyasztási statisztikáit gyűjti, ha az eszköz nem működik, a használati statisztikákban rések lesznek. Hasonlóképpen, a gépi telemetria-adatgyűjtési forgatókönyvben lehetséges, hogy a különböző érzékelők úgy vannak konfigurálva, hogy különböző gyakorisággal bocsátják ki az adatait, ami a szenzorok hiányzó értékeit eredményezi. Ha például két érzékelő, feszültség és nyomás van konfigurálva, a 100 Hz-es és 10 Hz-es gyakoriságú frekvencián, a feszültség-érzékelő a másodpercenkénti percenként bocsátja ki az adatmennyiséget, míg a nyomás érzékelője csak másodpercenként egy-egy tizedik adatmennyiséget bocsát ki.

A következő táblázat egy olyan gépi telemetria adatkészletet ismertet, amely egy egymásodperces időközönként lett összegyűjtve. 

```
timestamp               VoltageReading  PressureReading
----------------------- --------------- ----------------
2020-09-07 06:14:41.000 164.990400      97.223600
2020-09-07 06:14:42.000 162.241300      93.992800
2020-09-07 06:14:43.000 163.271200      NULL
2020-09-07 06:14:44.000 161.368100      93.403700
2020-09-07 06:14:45.000 NULL            NULL
2020-09-07 06:14:46.000 NULL            98.364800
2020-09-07 06:14:49.000 NULL            94.098300
2020-09-07 06:14:51.000 157.695700      103.359100
2020-09-07 06:14:52.000 157.019200      NULL
2020-09-07 06:14:54.000 NULL            95.352000
2020-09-07 06:14:56.000 159.183500      100.748200

```

Az előző adatkészletnek két fontos jellemzője van. 

- Az adatkészlet nem tartalmaz olyan adatpontokat, amelyek több időbélyeg `2020-09-07 06:14:47.000` ,,, `2020-09-07 06:14:48.000` `2020-09-07 06:14:50.000` `2020-09-07 06:14:53.000` és `2020-09-07 06:14:55.000` . Ezek az időbélyegek az adatkészletben található *hézagok* .  
- `null`A feszültség és a nyomás beolvasásához hiányzó értékek vannak megadva. 

## <a name="gap-filling"></a>Térköz kitöltése 

A térköz kitöltése egy olyan módszer, amely segít a folytonos, rendezett időbélyeg-készlet létrehozásában az idősorozat-adatelemzések megkönnyítéséhez. Az Azure SQL Edge-ben a hézagok kitöltésének legegyszerűbb módja az idősorozat-adatkészletben egy ideiglenes tábla definiálása a kívánt időeloszlással, majd a `LEFT OUTER JOIN` `RIGHT OUTER JOIN` művelet végrehajtása az adatkészlet táblán. 

A `MachineTelemetry` fentiekben ismertetett adatmennyiséget például a következő lekérdezéssel hozhatja létre egymással összefüggő, időbélyegek rendezett készletének elemzéséhez. 

> [!NOTE]
> Az alábbi lekérdezés a hiányzó sorokat hozza létre, az attribútumok időbélyeg-értékeivel és `null` értékeivel együtt. 

```sql
Create Table #SeriesGenerate(dt datetime Primary key Clustered)
GO

Declare @startdate datetime = '2020-09-07 06:14:41.000', @endtime datetime = '2020-09-07 06:14:56.000'
While (@startdate <= @endtime)
BEGIN
Insert into #SeriesGenerate values (@startdate)
set @startdate = DATEADD(SECOND, 1, @startdate)
END

Select a.dt as timestamp, b.VoltageReading, b.PressureReading 
From 
#SeriesGenerate a LEFT OUTER JOIN MachineTelemetry b 
    on a.dt = b.[timestamp]
```
A fenti lekérdezés a következő kimenetet hozza létre, amely tartalmazza a megadott tartomány összes *egymásodperces* időbélyegét.

Itt látható az eredményhalmaz

```

timestamp               VoltageReading    PressureReading
----------------------- ----------------- ----------------
2020-09-07 06:14:41.000 164.990400        97.223600
2020-09-07 06:14:42.000 162.241300        93.992800
2020-09-07 06:14:43.000 163.271200        NULL
2020-09-07 06:14:44.000 161.368100        93.403700
2020-09-07 06:14:45.000 NULL              NULL
2020-09-07 06:14:46.000 NULL              98.364800
2020-09-07 06:14:47.000 NULL              NULL
2020-09-07 06:14:48.000 NULL              NULL
2020-09-07 06:14:49.000 NULL              94.098300
2020-09-07 06:14:50.000 NULL              NULL
2020-09-07 06:14:51.000 157.695700        103.359100
2020-09-07 06:14:52.000 157.019200        NULL
2020-09-07 06:14:53.000 NULL              NULL
2020-09-07 06:14:54.000 NULL              95.352000
2020-09-07 06:14:55.000 NULL              NULL
2020-09-07 06:14:56.000 159.183500        100.748200
```

## <a name="imputing-missing-values"></a>Hiányzó értékek beszámítása

Az előző lekérdezés a hiányzó időbélyegeket generálta az adatelemzéshez, de nem cserélte le a hiányzó (NULL értékkel jelölt) értékeket a `voltage` és olvasott adatokhoz `pressure` . Az Azure SQL Edge-ben egy új szintaxis lett hozzáadva a T-SQL `LAST_VALUE()` és a `FIRST_VALUE()` functions szolgáltatáshoz, amely a hiányzó értékek bevezetésére szolgáló mechanizmusokat biztosít az adatkészlet előző vagy következő értékei alapján. 

Az új szintaxis hozzáadja `IGNORE NULLS` és `RESPECT NULLS` záradékot hoz létre a `LAST_VALUE()` és a `FIRST_VALUE()` függvényekhez. Az adatkészlet következő lekérdezése a `MachineTelemetry` hiányzó értékeket a last_value függvény használatával számítja ki, ahol a hiányzó értékek az adatkészlet utolsó megfigyelt értékével lesznek lecserélve.

```sql
Select 
    timestamp,
    VoltageReading As OriginalVoltageValues,
    LAST_VALUE(VoltageReading) IGNORE NULLS OVER (ORDER BY timestamp) As ImputedUsingLastValue, 
    PressureReading As OriginalPressureValues,
    LAST_VALUE(PressureReading) IGNORE NULLS OVER (ORDER BY timestamp) As ImputedUsingLastValue
From 
MachineTelemetry 
order by timestamp 
```
Itt látható az eredményhalmaz

```

timestamp               OrigVoltageVals  ImputedVoltage OrigPressureVals  ImputedPressure
----------------------- ---------------- -------------- ----------------- ----------------
2020-09-07 06:14:41.000 164.990400       164.990400     97.223600         97.223600
2020-09-07 06:14:42.000 162.241300       162.241300     93.992800         93.992800
2020-09-07 06:14:43.000 163.271200       163.271200     NULL              93.992800
2020-09-07 06:14:44.000 161.368100       161.368100     93.403700         93.403700
2020-09-07 06:14:45.000 NULL             161.368100     NULL              93.403700
2020-09-07 06:14:46.000 NULL             161.368100     98.364800         98.364800
2020-09-07 06:14:49.000 NULL             161.368100     94.098300         94.098300
2020-09-07 06:14:51.000 157.695700       157.695700     103.359100        103.359100
2020-09-07 06:14:52.000 157.019200       157.019200     NULL              103.359100
2020-09-07 06:14:54.000 NULL             157.019200     95.352000         95.352000
2020-09-07 06:14:56.000 159.183500       159.183500     100.748200        100.748200

```

A következő lekérdezés a és a függvény használatával a hiányzó értékeket számítja ki `LAST_VALUE()` `FIRST_VALUE` . Esetében a kimeneti oszlop `ImputedVoltage` a hiányzó értékeket a legutolsó megfigyelt értékre cseréli, míg a kimeneti oszlop esetében a `ImputedPressure` hiányzó értékeket az adatkészlet következő megfigyelt értéke váltja fel. 

```sql
Select 
    dt as timestamp, 
    VoltageReading As OrigVoltageVals,
    LAST_VALUE(VoltageReading) IGNORE NULLS OVER (ORDER BY dt) As ImputedVoltage, 
    PressureReading As OrigPressureVals,
    First_VALUE(PressureReading) IGNORE NULLS OVER (ORDER BY dt ROWS 
                    BETWEEN CURRENT ROW AND UNBOUNDED FOLLOWING) As ImputedPressure
From 
(Select a.dt, b.VoltageReading,b.PressureReading  from 
    #SeriesGenerate a 
        LEFT OUTER JOIN 
    MachineTelemetry b 
        on a.dt = b.[timestamp]) A
order by timestamp
```
Itt látható az eredményhalmaz

```

timestamp               OrigVoltageVals  ImputedVoltage  OrigPressureVals  ImputedPressure
----------------------- ---------------- --------------- ----------------- ---------------
2020-09-07 06:14:41.000 164.990400       164.990400      97.223600         97.223600
2020-09-07 06:14:42.000 162.241300       162.241300      93.992800         93.992800
2020-09-07 06:14:43.000 163.271200       163.271200      NULL              93.403700
2020-09-07 06:14:44.000 161.368100       161.368100      93.403700         93.403700
2020-09-07 06:14:45.000 NULL             161.368100      NULL              98.364800
2020-09-07 06:14:46.000 NULL             161.368100      98.364800         98.364800
2020-09-07 06:14:47.000 NULL             161.368100      NULL              94.098300
2020-09-07 06:14:48.000 NULL             161.368100      NULL              94.098300
2020-09-07 06:14:49.000 NULL             161.368100      94.098300         94.098300
2020-09-07 06:14:50.000 NULL             161.368100      NULL              103.359100
2020-09-07 06:14:51.000 157.695700       157.695700      103.359100        103.359100
2020-09-07 06:14:52.000 157.019200       157.019200      NULL              95.352000
2020-09-07 06:14:53.000 NULL             157.019200      NULL              95.352000
2020-09-07 06:14:54.000 NULL             157.019200      95.352000         95.352000
2020-09-07 06:14:55.000 NULL             157.019200      NULL              100.748200
2020-09-07 06:14:56.000 159.183500       159.183500      100.748200        100.748200
```

> [!NOTE]
> A fenti lekérdezés a `FIRST_VALUE()` függvény használatával lecseréli a hiányzó értékeket a következő megfigyelt értékre. Ugyanez az eredmény egy záradék használatával is megvalósítható `LAST_VALUE()` `ORDER BY <ordering_column> DESC` .

## <a name="next-steps"></a>Következő lépések 

- [FIRST_VALUE (Transact-SQL)](/sql/t-sql/functions/first-value-transact-sql?toc=%2fazure%2fazure-sql-edge%2ftoc.json)
- [LAST_VALUE (Transact-SQL)](/sql/t-sql/functions/last-value-transact-sql?toc=%2fazure%2fazure-sql-edge%2ftoc.json)
- [DATE_BUCKET (Transact-SQL)](date-bucket-tsql.md)
- [Összesítő függvények (Transact-SQL)](/sql/t-sql/functions/aggregate-functions-transact-sql)