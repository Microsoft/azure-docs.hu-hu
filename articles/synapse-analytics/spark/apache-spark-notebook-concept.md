---
title: Az Azure szinapszis Analytics notebookok áttekintése
description: Ez a cikk áttekintést nyújt az Azure szinapszis Analytics notebookokon keresztül elérhető képességekről.
services: synapse-analytics
author: midesa
ms.service: synapse-analytics
ms.topic: conceptual
ms.date: 11/18/2020
ms.author: midesa
ms.reviewer: jrasnick
ms.subservice: spark
ms.openlocfilehash: 5efdfcea7b6d727038bad124368179b9395bfafd
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "97093277"
---
# <a name="azure-synapse-analytics-notebooks"></a>Azure szinapszis Analytics-jegyzetfüzetek

A szinapszis Studio notebook egy webes felület, amellyel élő kódot, vizualizációkat és narratív szöveget tartalmazó fájlokat hozhat létre. A jegyzetfüzetek jó kiindulópontot nyújtanak az ötletek érvényesítéséhez és a gyors kísérletekhez az adatokból származó elemzések megszerzéséhez. 

Az Azure szinapszis Studio notebook használatával a következőket teheti:

* Első lépések a nulla telepítési erőfeszítéssel.
* A beépített nagyvállalati biztonsági funkciókkal megőrizheti az adatvédelmet.
* Elemezheti az adatokat nyers formátumokban (CSV, txt, JSON stb.), a feldolgozott fájlformátumokat (a parketta, a Delta Lake, az ork stb.) és az SQL táblázatos adatfájlokat a Spark és az SQL között.
* Legyen hatékony a fejlett szerzői képességek és beépített adatvizualizációk révén.

Ez a szakasz a különböző nyelveket, adatvizualizációkat, parameterizing jegyzetfüzeteket, folyamatokat és egyéb megoldásokat tartalmazó cikkeket tartalmaz. Emellett referenciákat és oktatóanyagokat is tartalmaz, amelyekkel megtudhatja, hogyan kezdheti el a jegyzetfüzetek fejlesztését.

## <a name="create-manage-and-use-notebooks"></a>Jegyzetfüzetek létrehozása, kezelése és használata
A-jegyzetfüzeteket a szinapszis Studio felhasználói felületének használatával kezelheti. 

A jegyzetfüzetek létrehozásával és kezelésével kapcsolatos további információkért tekintse meg a következő cikkeket:
  - Jegyzetfüzetek kezelése
    - [Jegyzetfüzetek létrehozása](./spark/../apache-spark-development-using-notebooks.md#create-a-notebook)
    - [Jegyzetfüzetek fejlesztése](./spark/../apache-spark-development-using-notebooks.md#develop-notebooks)
    - [Adat beolvasása egy jegyzetfüzetbe](./spark/../apache-spark-development-using-notebooks.md#bring-data-to-a-notebook)
    - [Több nyelv használata a Magic parancsok és az ideiglenes táblák használatával](./spark/../apache-spark-development-using-notebooks.md#integrate-a-notebook)
    - [Cella Magic-parancsok használata](./spark/../apache-spark-development-using-notebooks.md#magic-commands)
  - Fejlesztés
    - [A Spark-munkamenet beállításainak konfigurálása](./spark/../apache-spark-development-using-notebooks.md#spark-session-config)
    - [A Microsoft Spark segédprogramok használata](./spark/../microsoft-spark-utilities.md)
    - [Az adatmegjelenítés a notebookok és a könyvtárak használatával](./spark/../apache-spark-data-visualization.md)
    - [Jegyzetfüzet integrálása folyamatba](./spark/../apache-spark-development-using-notebooks.md#integrate-a-notebook)


## <a name="next-steps"></a>Következő lépések
A jegyzetfüzeteket széles körben használják az adatok előkészítésében, az adatvizualizációban, a gépi tanulásban és más big data helyzetekben is. Ha többet szeretne megtudni arról, hogyan használhatók a jegyzetfüzetek az adatelemzéshez és a big data forgatókönyvekhez, tekintse meg a következő oktatóanyagokat:
  - [Jegyzetfüzet létrehozása](./spark/../../quickstart-apache-spark-notebook.md)
  - [Vizualizációk létrehozása a szinapszis Studio notebookokkal](./spark/../apache-spark-data-visualization-tutorial.md)
  - [Gépi tanulási modellek készítése Apache Spark MLlib](./spark/../apache-spark-machine-learning-mllib-notebook.md)
  - [Gépi tanulási modellek készítése az Azure automatizált ML-vel](./spark/../apache-spark-azure-machine-learning-tutorial.md)