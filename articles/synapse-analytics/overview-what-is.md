---
title: Mi az az Azure Synapse Analytics?
description: Az Azure szinapszis Analytics áttekintése
services: synapse-analytics
author: saveenr
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: overview
ms.date: 03/24/2021
ms.author: saveenr
ms.reviewer: jrasnick
ms.openlocfilehash: 78b31cb32e3df9b0d8e198d8c2122e492e609283
ms.sourcegitcommit: a9ce1da049c019c86063acf442bb13f5a0dde213
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2021
ms.locfileid: "105625813"
---
# <a name="what-is-azure-synapse-analytics"></a>Mi az az Azure Synapse Analytics?

Az **Azure szinapszis** egy nagyvállalati elemzési szolgáltatás, amely felgyorsítja az adattárházak és a Big Data rendszerek betekintésének idejét. Az Azure szinapszis összefoglalja a nagyvállalati adattárházak, a big data **használt Spark** **-technológiák,** az adatintegrációs és az ETL/elt, valamint az egyéb Azure-szolgáltatásokkal **(például** a **Power bi**, a **CosmosDB** és a **AzureML**) való mélyebb integrációt.

![Az Azure szinapszis analitikai architektúrájának ábrája.](./media/overview-what-is/synapse-architecture.png)

## <a name="industry-leading-sql"></a>Piacvezető SQL

A **SZINAPSZIS SQL** egy elosztott lekérdezési rendszer a t-SQL-hez, amely lehetővé teszi az adattárházak és az adatvirtualizációs forgatókönyvek használatát, és kiterjeszti a t-SQL-t a folyamatos átviteli és gépi tanulási helyzet

* A szinapszis SQL **kiszolgáló** nélküli és **dedikált** erőforrás-modelleket is biztosít. A kiszámítható teljesítmény és a költséghatékonyság érdekében hozzon létre dedikált SQL-készleteket az SQL-táblákban tárolt adatok hatékony feldolgozásához. Nem tervezett vagy feltört számítási feladatokhoz használja a mindig elérhető, kiszolgáló nélküli SQL-végpontot.
* A Felhőbeli adatforrásokból származó adatoknak az SQL-táblákba való beépítésére szolgáló beépített **streaming** -funkciók használata
* A AI és az SQL integrálása **gépi tanulási** modellekkel az adatpontszámhoz a [T-SQL prediktív függvény](/sql/t-sql/queries/predict-transact-sql?view=azure-sqldw-latest&preserve-view=true) használatával

## <a name="industry-standard-apache-spark"></a>Iparági szabványnak megfelelő Apache Spark

Az **Azure szinapszis Apache Sparke** mélyen és zökkenőmentesen integrálható Apache Spark-a legnépszerűbb nyílt forráskódú Big Data motor, amely adatelőkészítési, adattervezési, ETL-és gépi tanulási szolgáltatásokhoz használatos.

* SparkML-algoritmusokat és AzureML-integrációt biztosító ML-modellek a Linux Foundation Delta Lake beépített támogatásával Apache Spark 2,4.
* Egyszerűsített erőforrás-modell, amely felszabadul, hogy aggódnia kell a fürtök kezelésével kapcsolatban.
* Gyors Spark indítási és agresszív automatikus skálázás.
* A .NET for Spark beépített támogatása lehetővé teszi, hogy egy Spark-alkalmazásban újra felhasználhassa a C#-szaktudást és a meglévő .NET-kódot.

## <a name="working-with-your-data-lake"></a>A Data Lake használata

Az Azure szinapszis eltávolítja a hagyományos technológiai korlátokat az SQL és a Spark együttes használata között. Az igények és a szaktudás alapján zökkenőmentesen összekeverheti és összehasonlíthatja azokat.

* A megosztott struktúra-kompatibilis metaadatrendszer lehetővé teszi, hogy a (z) rendszer a (z)-ben lévő fájlokban meghatározott táblákat a Spark vagy a kaptár zökkenőmentesen felhasználja.
* Az SQL és a Spark közvetlenül képes felderíteni és elemezni az adattóban tárolt Parquet, CSV-, TSV-és JSON-fájlokat.
* Gyors skálázható betöltés és eltávolítás az SQL és a Spark adatbázisok közötti adattároláshoz

## <a name="built-in-data-integration"></a>Beépített Adatintegráció

Az Azure szinapszis ugyanazokat az adatintegrációs motort és tapasztalatokat tartalmazza, mint a Azure Data Factory, ami lehetővé teszi, hogy az Azure szinapszis Analytics elhagyása nélkül hozzon létre gazdag mennyiségű ETL-folyamatot.

* Adatok beolvasása 90 és adatforrásból
* Code-Free ETL adatáramlási tevékenységekkel
* A notebookok, a Spark-feladatok, a tárolt eljárások, az SQL-parancsfájlok és egyebek összehangolása

## <a name="unified-management-monitoring-and-security"></a>Egységes felügyelet, figyelés és biztonság

Az Azure szinapszis egyetlen lehetőséget biztosít a vállalatok számára az elemzési erőforrások kezelésére, a használat és a tevékenységek figyelésére, valamint a biztonság betartatására.

* Felhasználók társítása szerepkörhöz az elemzési erőforrásokhoz való hozzáférés egyszerűbbé tétele érdekében
* Részletes hozzáférés-vezérlés az adatkapcsolaton és a kódban
* Egyetlen irányítópult az erőforrások, a használat és a felhasználók figyeléséhez az SQL és a Spark között

## <a name="unified-experience"></a>Egységes felhasználói élmény

A **szinapszis Studio** a felhasználói élmény, amely az adatmérnökökkel együtt mindent összekapcsol. Lehetővé teszi, hogy a teljes elemzési megoldás létrehozásához szükséges összes feladatot elvégezzék.

* Kulcsfontosságú adatmérnöki feladatok az egyik helyen: betöltés, felfedezés, előkészítés, hangolás, megjelenítés
* Piacvezető termelékenység az SQL vagy a Spark-kód írásához: szerzői műveletek, hibakeresés és teljesítmény optimalizálása
* Integrálás a vállalati CI/CD-folyamattal

## <a name="engage-with-the-synapse-engineering-team"></a>A szinapszis mérnöki csapatának részvétele

- [Stack overflow](https://stackoverflow.com/questions/tagged/azure-synapse): forduljon a fejlesztési kérdésekhez.
- [A Microsoft Q&egy kérdést tartalmazó oldalt](/answers/topics/azure-synapse-analytics.html): technikai kérdéseket tehet fel.

## <a name="next-steps"></a>Következő lépések

* [Az Azure Synapse Analytics első lépései](get-started.md)
* [Munkaterület létrehozása](quickstart-create-workspace.md)
* [Kiszolgáló nélküli SQL-készlet használata](quickstart-sql-on-demand.md)
