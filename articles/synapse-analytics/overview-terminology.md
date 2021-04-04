---
title: Terminológia – Azure szinapszis Analitika
description: Útmutató a felhasználónak az Azure szinapszis Analytics szolgáltatással való bejárásához
services: synapse-analytics
author: saveenr
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: overview
ms.date: 11/18/2020
ms.author: saveenr
ms.reviewer: jrasnick
ms.openlocfilehash: 828f37030ae567cacbaad25849b7ba24c561c20c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98132766"
---
# <a name="azure-synapse-analytics-terminology"></a>Az Azure szinapszis Analytics terminológiája

Ez a dokumentum végigvezeti az Azure szinapszis Analytics alapfogalmait.

## <a name="basics"></a>Alapvető beállítások

A **szinapszis munkaterület** biztonságos együttműködési határ az Azure-beli felhőalapú vállalati elemzésekhez. A munkaterület egy adott régióban van üzembe helyezve, és rendelkezik egy társított ADLS Gen2 fiókkal és fájlrendszerrel (az ideiglenes adattárolók tárolásához). A munkaterület egy erőforráscsoport alatt található.

A munkaterület lehetővé teszi, hogy az SQL és az Apache Spark használatával végezzen elemzéseket. Az SQL és a Spark Analytics számára elérhető erőforrások SQL-és Spark- **készletekbe** vannak rendezve. 

## <a name="linked-services"></a>Társított szolgáltatások

A munkaterület tetszőleges számú **társított szolgáltatást** tartalmazhat, amelyek lényegében kapcsolati karakterláncokat határoznak meg, amelyek meghatározzák a munkaterület külső erőforrásokhoz való kapcsolódásához szükséges kapcsolati adatokat.

## <a name="synapse-sql"></a>Synapse SQL

A **SZINAPSZIS SQL** lehetővé teszi a T-SQL-alapú elemzések elvégzését a szinapszis munkaterületen. A szinapszis SQL két felhasználási modellt tartalmaz: dedikált és kiszolgáló nélküli.  A dedikált modell esetében használjon **DEDIKÁLT SQL-készleteket**. A munkaterület tetszőleges számú készlettel rendelkezhet. A kiszolgáló nélküli modell használatához használja a kiszolgáló nélküli **SQL-készleteket**. Minden munkaterülethez tartozik egy ilyen készlet.

A szinapszis Studióban az SQL-készleteket **SQL-parancsfájlok** létrehozásával és futtatásával is használhatja.

## <a name="apache-spark-for-synapse"></a>A szinapszis Apache Spark

A Spark Analytics használatához hozzon létre és használjon **kiszolgáló nélküli Apache Spark készleteket** a szinapszis munkaterületen. A Spark-készlet használatának megkezdése során a munkaterületek egy **Spark-munkamenetet** hoznak létre az adott munkamenethez társított erőforrások kezeléséhez. 

A (z)-ben kétféleképpen használhatja a Sparkot:
* Az adatelemzést és-fejlesztést végző **Spark notebookok** a Scala, a PySpark, a C# és a SparkSQL használatával
* **Spark-feladatdefiníciók** a Batch Spark-feladatok jar-fájlokkal történő futtatásához.

## <a name="pipelines"></a>Pipelines

A folyamatok az Azure szinapszis adatintegrációját biztosítják – lehetővé teszi az adatáthelyezést a szolgáltatások és a tevékenységek között.

* A **folyamat** az olyan tevékenységek logikai csoportosítása, amelyek egy feladatot együtt hajtanak végre.
* A **tevékenységek** a folyamaton belüli műveleteket határozzák meg olyan adatokon, mint például az adatok másolása, jegyzetfüzetek vagy SQL-parancsfájlok futtatása.
* Az **adatfolyamatok** egy adott típusú tevékenység, amely kód nélküli működést biztosít az adatátalakításhoz, amely a szinapszis Sparkot használja a-borítón.
* **Trigger** – folyamat végrehajtása. Futtatható manuálisan vagy automatikusan (Schedule, bukdácsoló ablak vagy eseményvezérelt)
* **Integrációs adatkészlet** – olyan adatok elnevezett nézete, amelyek egyszerűen rámutatnak vagy hivatkoznak egy tevékenységben bemenetként és kimenetként használandó adatokra. Egy társított szolgáltatáshoz tartozik.

## <a name="next-steps"></a>Következő lépések

* [Az Azure Synapse Analytics első lépései](get-started.md)
* [Munkaterület létrehozása](quickstart-create-workspace.md)
* [Kiszolgáló nélküli SQL-készlet használata](quickstart-sql-on-demand.md)

