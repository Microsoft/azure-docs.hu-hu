---
title: 'Oktatóanyag: kiszolgáló nélküli SQL-készlet használata Azure-beli nyílt adatkészletek elemzéséhez az Azure szinapszis Studióban'
description: Ebből az oktatóanyagból megtudhatja, hogyan végezheti el a feltárt adatok elemzését a különböző Azure Open-adatkészletek kiszolgáló nélküli SQL-készlettel való kombinálásával, és megjelenítheti az eredményeket az Azure szinapszis Studióban
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: tutorial
ms.subservice: sql
ms.date: 11/20/2020
ms.author: stefanazaric
ms.reviewer: jrasnick
ms.openlocfilehash: d37597f8667c461e8d61f8214483f57eb702c2a0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "97007551"
---
# <a name="tutorial-explore-and-analyze-data-lakes-with-serverless-sql-pool"></a>Oktatóanyag: az adattavak feltárása és elemzése kiszolgáló nélküli SQL-készlettel

Ebből az oktatóanyagból megtudhatja, hogyan végezheti el a felderítő adatok elemzését. A különböző Azure Open-adatkészleteket kiszolgáló nélküli SQL-készlettel kombinálhatja. Ezután megjelenítheti az eredményeket a szinapszis Studio for Azure szinapszis Analytics szolgáltatásban.

A OPENROWSET (BULK...) függvény lehetővé teszi a fájlok elérését az Azure Storage-ban. A [OpenRowset](develop-openrowset.md) függvény egy távoli adatforrás tartalmát olvassa be (például fájl), és a tartalmat sorok halmaza adja vissza.

## <a name="automatic-schema-inference"></a>Automatikus séma-következtetés

Mivel az adattárolás a Parquet fájlformátumban történik, az automatikus séma-következtetés elérhető. Az adatokat egyszerűen lekérdezheti a fájlok összes oszlopának adattípusának listázása nélkül. A virtuális oszlop mechanizmust és a filepath függvényt is használhatja a fájlok bizonyos részhalmazának kiszűrésére.

Először Ismerkedjen meg a New York-i taxi-adatszolgáltatással a következő lekérdezés futtatásával:

```sql
SELECT TOP 100 * FROM
    OPENROWSET(
        BULK 'https://azureopendatastorage.blob.core.windows.net/nyctlc/yellow/puYear=*/puMonth=*/*.parquet',
        FORMAT='PARQUET'
    ) AS [nyc]
```

A [New York City (NYC) taxi-adatkészlet](https://azure.microsoft.com/services/open-datasets/catalog/nyc-taxi-limousine-commission-yellow-taxi-trip-records/) a következőket tartalmazza:

- A pick-up és a drop-off dátumok és időpontok.
- A pick-up és a drop-off tárolóhelyek. 
- Utazási távolságok.
- Részletezett viteldíjak.
- Díjszabási típusok.
- Fizetési típusok. 
- Illesztőprogram – jelentett utasok száma.

Hasonlóképpen lekérdezheti a nyilvános ünnepnapok adatkészletét a következő lekérdezés használatával:

```sql
SELECT TOP 100 * FROM
    OPENROWSET(
        BULK 'https://azureopendatastorage.blob.core.windows.net/holidaydatacontainer/Processed/*.parquet',
        FORMAT='PARQUET'
    ) AS [holidays]
```

Végül a következő lekérdezéssel is lekérdezheti az időjárási adatokat használó adatkészletet:

```sql
SELECT
    TOP 100 *
FROM  
    OPENROWSET(
        BULK 'https://azureopendatastorage.blob.core.windows.net/isdweatherdatacontainer/ISDWeather/year=*/month=*/*.parquet',
        FORMAT='PARQUET'
    ) AS [weather]
```

Az egyes oszlopok jelentésével kapcsolatos további információkért tekintse meg az adathalmazok leírását: 
- [NYC-taxi](https://azure.microsoft.com/services/open-datasets/catalog/nyc-taxi-limousine-commission-yellow-taxi-trip-records/)
- [Ünnepnapok](https://azure.microsoft.com/services/open-datasets/catalog/public-holidays/)
- [Időjárási adatszolgáltatások](https://azure.microsoft.com/services/open-datasets/catalog/noaa-integrated-surface-data/)

## <a name="time-series-seasonality-and-outlier-analysis"></a>Idősorozat, szezonális és kiugró elemzés

A következő lekérdezéssel egyszerűen összefoglalhatja a taxik éves számát:

```sql
SELECT
    YEAR(tpepPickupDateTime) AS current_year,
    COUNT(*) AS rides_per_year
FROM
    OPENROWSET(
        BULK 'https://azureopendatastorage.blob.core.windows.net/nyctlc/yellow/puYear=*/puMonth=*/*.parquet',
        FORMAT='PARQUET'
    ) AS [nyc]
WHERE nyc.filepath(1) >= '2009' AND nyc.filepath(1) <= '2019'
GROUP BY YEAR(tpepPickupDateTime)
ORDER BY 1 ASC
```

Az alábbi kódrészlet a taxik éves számának eredményét mutatja:

![A taxi rides eredményének éves száma](./media/tutorial-data-analyst/yearly-taxi-rides.png)

Az adatok megjeleníthetők a szinapszis Studióban úgy, hogy a **táblázatról** a **diagram** nézetre vált. Különböző típusú diagramok közül választhat, például a **területen**, a **sáv**, az **oszlop**, a **vonal**, a **torta** és a **Scatter**. Ebben az esetben ábrázolja az **oszlopdiagram** és a **category (kategória** ) oszlopot a **current_year** értékre:

![A rides/Year diagramot bemutató oszlopdiagram](./media/tutorial-data-analyst/column-chart-rides-year.png)

Ebből a vizualizációból megtudhatja, hogy az évek során csökkenő számú Ride-számok milyen tendenciát mutatnak. Ezt a csökkenést valószínűleg a Ride-Sharing vállalatok közelmúltbeli megnövekedett népszerűsége okozza.

> [!NOTE]
> Az oktatóanyag írásakor a 2019-es adatmennyiség hiányos. Ennek eredményeképpen óriási mennyiségű csökkenés van az adott évre vonatkozó túrák számában.

Ezután az elemzést egyetlen évre koncentráljuk, például 2016. A következő lekérdezés az év folyamán a túrák napi számát adja vissza:

```sql
SELECT
    CAST([tpepPickupDateTime] AS DATE) AS [current_day],
    COUNT(*) as rides_per_day
FROM
    OPENROWSET(
        BULK 'https://azureopendatastorage.blob.core.windows.net/nyctlc/yellow/puYear=*/puMonth=*/*.parquet',
        FORMAT='PARQUET'
    ) AS [nyc]
WHERE nyc.filepath(1) = '2016'
GROUP BY CAST([tpepPickupDateTime] AS DATE)
ORDER BY 1 ASC
```

A következő kódrészlet a lekérdezés eredményét jeleníti meg:

![A 2016-es találatok napi száma](./media/tutorial-data-analyst/daily-rides.png)

Újra könnyedén megjelenítheti az adatdiagramot úgy, **current_day** hogy az **oszlopdiagram** a **category (kategória** ) oszlopra van beállítva, és a **Jelmagyarázat (sorozat)** oszlopa **rides_per_day** értékre van állítva.

![Oszlopdiagram, amely a 2016-as napi számú túrákat mutatja](./media/tutorial-data-analyst/column-chart-daily-rides.png)

A mintaterület diagramon láthatja, hogy heti minta van, szombatonként, a csúcs napjával. A nyári hónapokban a vakációk miatt kevesebb taxi rides van. Azt is figyelje meg, hogy a taxi-túrák száma jelentős mértékben elesik, és nem egyértelmű minta, hogy mikor és miért jelentkeznek.

Ezután nézzük meg, hogy a drop in rides összekapcsolja-e a munkaszüneti napokat. Láthatjuk, hogy van-e összefüggés a New York-i taxi-adatkészlethez való csatlakozással a Public Holidays adatkészlettel:

```sql
WITH taxi_rides AS
(
    SELECT
        CAST([tpepPickupDateTime] AS DATE) AS [current_day],
        COUNT(*) as rides_per_day
    FROM  
        OPENROWSET(
            BULK 'https://azureopendatastorage.blob.core.windows.net/nyctlc/yellow/puYear=*/puMonth=*/*.parquet',
            FORMAT='PARQUET'
        ) AS [nyc]
    WHERE nyc.filepath(1) = '2016'
    GROUP BY CAST([tpepPickupDateTime] AS DATE)
),
public_holidays AS
(
    SELECT
        holidayname as holiday,
        date
    FROM
        OPENROWSET(
            BULK 'https://azureopendatastorage.blob.core.windows.net/holidaydatacontainer/Processed/*.parquet',
            FORMAT='PARQUET'
        ) AS [holidays]
    WHERE countryorregion = 'United States' AND YEAR(date) = 2016
)
SELECT
*
FROM taxi_rides t
LEFT OUTER JOIN public_holidays p on t.current_day = p.date
ORDER BY current_day ASC
```

![A New York-i taxik és a munkaszüneti adatkészletek eredményeinek megjelenítése](./media/tutorial-data-analyst/rides-public-holidays.png)

Most pedig szeretnénk kiemelni a taxik számát a nyilvános ünnepek alatt. Erre a célra a **category (kategória** ) oszlopra, a **rides_per_day** és az **ünnepnapot** a **Jelmagyarázat (adatsorozat)** oszlopai **közül választjuk** .

![A nyilvános ünnepek diagramja során beérkező taxik száma](./media/tutorial-data-analyst/plot-chart-public-holidays.png)

A mintaterület diagramon láthatja, hogy a nyilvános ünnepek alatt a taxis túrák száma alacsonyabb. Január 23-án még egy megmagyarázhatatlan nagy csökkenés van. Az időjárási adatokat tartalmazó adathalmaz lekérdezésével vizsgáljuk meg a New York-i időjárási időt:

```sql
SELECT
    AVG(windspeed) AS avg_windspeed,
    MIN(windspeed) AS min_windspeed,
    MAX(windspeed) AS max_windspeed,
    AVG(temperature) AS avg_temperature,
    MIN(temperature) AS min_temperature,
    MAX(temperature) AS max_temperature,
    AVG(sealvlpressure) AS avg_sealvlpressure,
    MIN(sealvlpressure) AS min_sealvlpressure,
    MAX(sealvlpressure) AS max_sealvlpressure,
    AVG(precipdepth) AS avg_precipdepth,
    MIN(precipdepth) AS min_precipdepth,
    MAX(precipdepth) AS max_precipdepth,
    AVG(snowdepth) AS avg_snowdepth,
    MIN(snowdepth) AS min_snowdepth,
    MAX(snowdepth) AS max_snowdepth
FROM
    OPENROWSET(
        BULK 'https://azureopendatastorage.blob.core.windows.net/isdweatherdatacontainer/ISDWeather/year=*/month=*/*.parquet',
        FORMAT='PARQUET'
    ) AS [weather]
WHERE countryorregion = 'US' AND CAST([datetime] AS DATE) = '2016-01-23' AND stationname = 'JOHN F KENNEDY INTERNATIONAL AIRPORT'
```

![Időjárási adathalmaz eredményeinek vizualizációja](./media/tutorial-data-analyst/weather-data-set-visualization.png)

A lekérdezés eredménye azt jelzi, hogy a taxik számának csökkenése a következő okból történt:

- A mai napon hóviharban volt a nagy hóban (~ 30 cm).
- Hideg volt (a hőmérséklet nem éri el a nulla fokos Celsius-fokot).
- Szeles (~ 10 m/s).

Ebből az oktatóanyagból megtudhatta, hogy az adatelemzők hogyan tudják gyorsan végrehajtani a felderítő adatelemzést, és hogy a kiszolgáló nélküli SQL-készlet használatával könnyedén egyesítheti a különböző adatkészleteket, és megjelenítheti az eredményeket az Azure szinapszis Studio használatával.

## <a name="next-steps"></a>Következő lépések

A kiszolgáló nélküli SQL-Power BI Desktop készlet összekapcsolásával és a jelentések létrehozásával kapcsolatos további információkért lásd: [kiszolgáló nélküli SQL-készlet összekötése a Power bi Desktop és jelentések létrehozásához](tutorial-connect-power-bi-desktop.md).

Ha szeretné megtudni, hogyan használhat külső táblákat a kiszolgáló nélküli SQL-készletben, tekintse meg a [külső táblák használata a SZINAPSZIS SQL használatával](develop-tables-external-tables.md?tabs=sql-pool) című részt
 
