---
title: Az Azure Data Lake Analytics áttekintése
description: A Data Lake Analytics lehetővé teszi, hogy a felhőbeli adatokból kinyert információkat vállalkozása hasznára fordíthassa, bármennyi adatról legyen is szó.
services: data-lake-analytics
author: saveenr
ms.author: saveenr
ms.reviewer: jasonwhowell
ms.assetid: 1e1d443a-48a2-47fb-bc00-bf88274222de
ms.service: data-lake-analytics
ms.topic: overview
ms.date: 06/23/2017
ms.openlocfilehash: 65bf1b0d0b2168d8469c3e0233212eea6000d1ad
ms.sourcegitcommit: a989fb89cc5172ddd825556e45359bac15893ab7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/01/2020
ms.locfileid: "85800783"
---
# <a name="what-is-azure-data-lake-analytics"></a>Mi az az Azure Data Lake Analytics?

Az Azure Data Lake Analytics egy igény szerinti elemzési feladatokat végző szolgáltatás, amely leegyszerűsíti a big data-elemzéseket. Az üzembe helyezés, a konfigurálás és a hardver beállítása helyett lekérdezéseket írhat az adatátalakítás és az értékes információk kinyerése érdekében. Az elemző szolgáltatás bármekkora munkát képes elvégezni, csak be kell állítania, hogy mekkora teljesítményre van szüksége. Költséghatékony megoldás, hiszen csak az elvégzett munkáért kell fizetni. 

## <a name="dynamic-scaling"></a>Dinamikus méretezés
  
A Data Lake Analytics dinamikusan kiosztja az erőforrásokat, és lehetővé teszi az adatok terabájtjain és petabájtjain való elemzést. Csak a felhasznált számítási kapacitásért kell fizetnie. Nem kell újraírnia a programot, ha növeli vagy csökkenti a tárolt adatmennyiséget vagy a felhasznált számítási erőforrások számát. 

## <a name="develop-faster-debug-and-optimize-smarter-using-familiar-tools"></a>Gyorsabb fejlesztés, intelligensebb hibakeresés és optimalizálás a már ismerős eszközökkel
  
A Data Lake Analytics átfogóan integrálja a Visual Studiót. A már ismert eszközökkel futtathatja a kódot, vagy végezhet rajta hibakeresést és finomhangolást. Az U-SQL feladatok vizualizációja révén láthatja, hogyan fut a program nagyobb méretek esetén, így egyszerűbb azonosítani a teljesítménybeli szűk keresztmetszeteket, valamint optimalizálni a költségeket.

## <a name="u-sql-simple-and-familiar-powerful-and-extensible"></a>U-SQL: egyszerű és ismerős, sokoldalú és bővíthető
  
A Data Lake Analytics tartalmazza a U-SQL lekérdezésnyelvet, amely kibővíti az SQL ismerős, egyszerű, deklaratív természetét a C# kifejezőerejével. A U-SQL nyelv ugyanazt az elosztott futtatási környezetet használja, amely a Microsoft belső, exabájt méretű Data Lake adattárát működteti. Mostantól az SQL- és a .NET-fejlesztők feldolgozhatják és elemezhetik az adataikat a már meglévő ismereteik segítségével.

## <a name="integrates-seamlessly-with-your-it-investments"></a>Zökkenőmentes integráció az informatikai befektetésekkel
  
A Data Lake Analytics a meglévő informatikai befektetéseket használja az identitások, a kezelés és a biztonság terén. Ez a megközelítés leegyszerűsíti az adatszabályozást, és megkönnyíti az aktuális adatalkalmazások bővítését. A Data Lake Analytics integrálja az Active Directoryt a felhasználók kezeléséhez és a jogosultságokhoz, és beépített figyelést és felülvizsgálatot is biztosít.

## <a name="affordable-and-cost-effective"></a>Megfizethető és költséghatékony

A Data Lake Analytics egy költséghatékony megoldás big data-számítási feladatok futtatásához. Az adatok feldolgozásakor feladatonként kell fizetnie. Nem szükséges hardver-, licenc- vagy szolgáltatásspecifikus támogatási megállapodásokat kötnie. Mivel a feladat indulásakor és befejezésekor a rendszer automatikusan méretezi magát, soha nem kell a szükségesnél többet fizetnie. [További tudnivalók a költségek szabályozásáról és a pénzmegtakarításról](https://aka.ms/adlasavemoney).

## <a name="works-with-all-your-azure-data"></a>Az összes Azure Data-szolgáltatással együttműködik
  
A Azure Data Lake Storage Data Lake Analytics a legmagasabb teljesítmény-, átviteli sebesség-és párhuzamos, valamint az Azure Storage-Blobok, Azure SQL Database, Azure szinapszis Analytics (korábban Azure SQL Data Warehouse) használatával működik.

## <a name="next-steps"></a>Következő lépések

* Ismerkedés a Data Lake Analytics [Azure Portal](data-lake-analytics-get-started-portal.md)  |  [Azure PowerShell](data-lake-analytics-get-started-powershell.md)  |  [CLI](data-lake-analytics-get-started-cli.md) használatával
* Azure Data Lake Analytics kezelése az [Azure Portal](data-lake-analytics-manage-use-portal.md)  |  [Azure PowerShell](data-lake-analytics-manage-use-powershell.md)  |  [CLI](data-lake-analytics-manage-use-cli.md)  |  [Azure .net SDK](data-lake-analytics-manage-use-dotnet-sdk.md) -val  |  [Node.js](data-lake-analytics-manage-use-nodejs.md)
* [A költségek szabályozása és pénz megtakarítása a Data Lake Analytics használatával](https://1drv.ms/f/s!AvdZLquGMt47h213Hg3rhl-Tym1c)
