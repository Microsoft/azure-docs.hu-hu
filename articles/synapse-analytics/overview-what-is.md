---
title: Mi az az Azure Synapse Analytics?
description: A Azure Synapse Analytics
services: synapse-analytics
author: saveenr
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: overview
ms.date: 03/24/2021
ms.author: saveenr
ms.reviewer: jrasnick
ms.openlocfilehash: 652f98659f96b36e3185432e50d9d36dc569bd43
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2021
ms.locfileid: "107537949"
---
# <a name="what-is-azure-synapse-analytics"></a>Mi az az Azure Synapse Analytics?

**Azure Synapse** egy nagyvállalati elemzési szolgáltatás, amely felgyorsítja az adattárház- és big data elemzésekhez. Azure Synapse egyesíti a vállalati  adatraktározásban használt SQL-technológiák, az big data-hoz használt Spark-technológiák,  az adatintegrációs és ETL/ELT-folyamatok, valamint más Azure-szolgáltatásokkal, például az **Power BI,** a **CosmosDB** és az **AzureML** szolgáltatásokkal való mély integrációt. 

![A Azure Synapse Analytics ábrája.](./media/overview-what-is/synapse-architecture.png)

## <a name="industry-leading-sql"></a>Iparágvezető SQL

**Synapse SQL** egy elosztott lekérdezési rendszer a T-SQL-hez, amely lehetővé teszi az adatraktározást és az adat-virtualizálási forgatókönyveket, és kiterjeszti a T-SQL-t a streamelési és gépi tanulási forgatókönyvekre.

* Synapse SQL kiszolgáló **nélküli** és dedikált **erőforrásmodelleket** is kínál. A kiszámítható teljesítmény és a költséghatékonyság érdekében hozzon létre dedikált SQL-készleteket az SQL-táblákban tárolt adatok hatékony feldolgozásához. Nem tervezett vagy adatlökös számítási feladatokhoz használja a mindig elérhető, kiszolgáló nélküli SQL-végpontot.
* Beépített streamelési **képességek használata** az adatok felhőbeli adatforrásból SQL-táblákba való feltöltéséhez
* AI integrálása SQL-sel gépi **tanulási modellekkel** adatok pontozására a [T-SQL PREDICT függvény használatával](/sql/t-sql/queries/predict-transact-sql?view=azure-sqldw-latest&preserve-view=true)

## <a name="industry-standard-apache-spark"></a>Iparági szabványnak megfelelő Apache Spark

**Apache Spark** a Azure Synapse és zökkenőmentesen integrálja Apache Spark legnépszerűbb nyílt forráskódú big data-motort, amely az adat-előkészítéshez, az adattervezéshez, az ETL-hez és a gépi tanuláshoz használatos.

* Ml-modellek SparkML-algoritmusokkal és AzureML-integrációval a Apache Spark 2.4-es és a Linux Foundation Delta Lake beépített támogatásával.
* Egyszerűsített erőforrásmodell, amely nem kell a fürtök kezelésével törődni.
* Gyors Spark-indítás és agresszív automatikus skálázás.
* A .NET for Spark beépített támogatása lehetővé teszi a C# szakértelmének és a meglévő .NET-kódnak a Spark-alkalmazásokban való újrafelhasználát.

## <a name="working-with-your-data-lake"></a>A Data Lake-hez való munka

Azure Synapse az SQL és a Spark együttes használata közötti hagyományos technológiai akadályokat. Az igényei és szakértelmének megfelelően zökkenőmentesen keverheti a találatokat.

* A Spark vagy a Hive zökkenőmentesen felhasználja a data lake-ben található fájlokon definiált táblákat.
* Az SQL és a Spark közvetlenül felderítheti és elemezheti a Data Lake-ben tárolt Parquet-, CSV-, TSV- és JSON-fájlokat.
* Gyors, skálázható adatbetöltés SQL- és Spark-adatbázisok között

## <a name="built-in-data-integration"></a>Beépített adatintegráció

Azure Synapse adatintegrációs motort és a Azure Data Factory-t tartalmazza, így részletes, nagy léptékű ETL-folyamatok hozhatók létre anélkül, hogy elhagyják Azure Synapse Analytics.

* Több mint 90 adatforrásból származó adatok bemenő adatai
* Code-Free ETL és adatfolyam-tevékenységek
* Jegyzetfüzetek, Spark-feladatok, tárolt eljárások, SQL-szkriptek és egyéb műveletek vezénylelése

## <a name="unified-experience"></a>Egységes felhasználói élmény 

**Synapse Studio** egyetlen lehetőséget kínál a vállalatok számára megoldások építésére, karbantartására és biztonságossá építésére egyetlen felhasználói élményben

* Fő feladatok végrehajtása: be- és felfedezés, előkészítés, vezénylés, vizualizáció
* Erőforrások, használat és felhasználók figyelése az SQL és a Spark használatával
* Szerepköralapú hozzáférés-vezérlés használata az elemzési erőforrásokhoz való hozzáférés egyszerűsítésére
* SQL- vagy Spark-kód írása és integrálása vállalati CI-/CD-folyamatokkal

## <a name="engage-with-the-synapse-community"></a>Lépjen kapcsolatba a Synapse-közösséggel

- [Microsoft Q&A:](/answers/topics/azure-synapse-analytics.html)Technikai kérdések.
- [Stack Overflow:](https://stackoverflow.com/questions/tagged/azure-synapse)Fejlesztési kérdések.

## <a name="next-steps"></a>Következő lépések

* [Az Azure Synapse Analytics első lépései](get-started.md)
* [Munkaterület létrehozása](quickstart-create-workspace.md)
* [Kiszolgáló nélküli SQL-készlet használata](quickstart-sql-on-demand.md)
