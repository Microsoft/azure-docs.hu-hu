---
title: Az adatfeldolgozás optimalizálása Apache Spark-Azure-HDInsight
description: Megtudhatja, hogyan választhatja ki a leghatékonyabb műveleteket Apache Spark az Azure HDInsight-ben tárolt adatok feldolgozásához.
ms.service: hdinsight
ms.topic: conceptual
ms.date: 05/20/2020
ms.custom: contperf-fy21q1
ms.openlocfilehash: 98457456bc1c8f9fdb08c32fdcd319a3aa9ff14a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98944722"
---
# <a name="data-processing-optimization-for-apache-spark"></a>Apache Spark adatfeldolgozási optimalizálása

Ez a cikk azt ismerteti, hogyan optimalizálható a Apache Spark-fürt konfigurációja a legjobb teljesítmény érdekében az Azure HDInsight.

## <a name="overview"></a>Áttekintés

Ha lassú feladattal rendelkezik egy JOIN vagy shuffle utasításban, az OK valószínűleg *adattorzítást* eredményez. Az adatok eldöntése az aszimmetria a feladatok adataiban. A térképes feladatok például 20 másodpercet is igénybe vehetnek. Ha azonban egy olyan feladatot futtat, amelyben az adatok csatlakoztatva vannak, vagy az adatkeverő órákat vesz igénybe. Az adatdöntés kijavításához a teljes kulcsot kell megállapítania, vagy egy *elkülönített sót* kell használnia a kulcsok csak néhány részhalmaza számára. Ha izolált sót használ, érdemes tovább szűrnie, hogy elkülönítse a sós kulcsok részhalmazát a térképi illesztésekben. Egy másik lehetőség egy gyűjtő oszlop bevezetése és a gyűjtők előzetes összesítése.

A lassú illesztéseket okozó másik tényező lehet az illesztés típusa. Alapértelmezés szerint a Spark az `SortMerge` illesztés típusát használja. Ez a fajta JOIN a legmegfelelőbb a nagyméretű adathalmazokhoz. Ez azonban egyébként költséges, mert először a bal és a jobb oldalt kell rendeznie az Adategyesítés előtt.

Az `Broadcast` illesztés a kisebb adatkészletekhez ideális, vagy ha az illesztés egyik oldala sokkal kisebb, mint a másik oldal. Az ilyen típusú illesztések az egyik oldalról az összes végrehajtóra mutatnak, ezért általában több memóriát igényelnek a szórások számára.

Az illesztési típust beállíthatja a konfigurációban, vagy beállíthatja az `spark.sql.autoBroadcastJoinThreshold` illesztési mutatót a DataFrame API-k ( `dataframe.join(broadcast(df2))` ) használatával.

```scala
// Option 1
spark.conf.set("spark.sql.autoBroadcastJoinThreshold", 1*1024*1024*1024)

// Option 2
val df1 = spark.table("FactTableA")
val df2 = spark.table("dimMP")
df1.join(broadcast(df2), Seq("PK")).
    createOrReplaceTempView("V_JOIN")

sql("SELECT col1, col2 FROM V_JOIN")
```

Ha gyűjtő táblákat használ, akkor egy harmadik illesztési típussal rendelkezik, amelyhez `Merge` csatlakozik. Egy megfelelően előre particionált és előre rendezett adatkészlet kihagyja a költséges rendezési szakaszt egy `SortMerge` illesztésből.

Az illesztések sorrendje, különösen az összetettebb lekérdezésekben. Kezdje a legszelektívebb illesztésekkel. Emellett olyan illesztéseket is helyezhet át, amelyek a sorok számát a lehetséges összesítések után növelhetik.

A Descartes-féle illesztések párhuzamosságának kezeléséhez beágyazott struktúrákat, ablakokat adhat hozzá, és lehet, hogy kihagy egy vagy több lépést a Spark-feladatokban.

## <a name="optimize-job-execution"></a>A feladat-végrehajtás optimalizálása

* Szükség szerint gyorsítótárazza, például ha kétszer használja az adatkészletet, majd gyorsítótárazza.
* Szórási változók az összes végrehajtóra. A változók csak egyszer szerializáltak, ami gyorsabb keresést eredményez.
* Használja a szál készletét az illesztőprogramon, ami gyorsabb műveletet eredményez számos feladathoz.

A futó feladatok rendszeres figyelése teljesítménnyel kapcsolatos problémák esetén. Ha további információkra van szüksége az egyes problémákkal kapcsolatban, vegye figyelembe az alábbi teljesítmény-profilkészítési eszközök egyikét:

* Az [Intel PAL eszköz](https://github.com/intel-hadoop/PAT) FIGYELI a CPU-t, a tárterületet és a hálózati sávszélesség-használatot.
* [Oracle Java 8 Mission Control](https://www.oracle.com/technetwork/java/javaseproducts/mission-control/java-mission-control-1998576.html) -profilok Spark és végrehajtó kód.

A Spark 2. x lekérdezési teljesítménye a Wolfram motor, amely a teljes fázisú programkódok generálásának függvénye. Bizonyos esetekben előfordulhat, hogy a teljes fázisú kód létrehozása le lesz tiltva. Ha például nem megváltoztathatatlan típust ( `string` ) használ az összesítési kifejezésben, a () helyett a következő `SortAggregate` jelenik meg: `HashAggregate` . Például a jobb teljesítmény érdekében próbálkozzon a következőkkel, majd engedélyezze újra a kód generálását:

```sql
MAX(AMOUNT) -> MAX(cast(AMOUNT as DOUBLE))
```

## <a name="next-steps"></a>Következő lépések

* [Az adattároló optimalizálása Apache Spark](optimize-data-storage.md)
* [Memória használatának optimalizálása Apache Spark](optimize-memory-usage.md)
* [A fürt konfigurációjának optimalizálása Apache Spark](optimize-cluster-configuration.md)
