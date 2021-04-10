---
title: Apache Spark fürtkonfiguráció optimalizálása – Azure HDInsight
description: Megtudhatja, hogyan konfigurálhatja a Apache Spark-fürtöt az Azure HDInsight teljesítményének maximalizálása érdekében.
ms.service: hdinsight
ms.topic: conceptual
ms.date: 08/21/2020
ms.custom: contperf-fy21q1
ms.openlocfilehash: f70e9f242b0f12abf58e72554e7cb6819ce8f7a9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98944740"
---
# <a name="cluster-configuration-optimization-for-apache-spark"></a>Fürtkonfiguráció optimalizálása Apache Spark

Ez a cikk azt ismerteti, hogyan optimalizálható a Apache Spark-fürt konfigurációja a legjobb teljesítmény érdekében az Azure HDInsight.

## <a name="overview"></a>Áttekintés

A Spark-fürt számítási feladataitól függően megállapíthatja, hogy egy nem alapértelmezett Spark-konfiguráció a Spark-feladatok jobbá tételéhez vezetne.  A nem alapértelmezett fürtkonfiguráció érvényesítéséhez végezzen teljesítményteszt-tesztelést a minta számítási feladatokkal.

Az alábbiakban néhány gyakori paramétert módosíthat:

|Paraméter |Leírás |
|---|---|
|--NUM-végrehajtók|Beállítja a megfelelő számú végrehajtót.|
|– végrehajtó – magok|Beállítja a magok számát az egyes végrehajtók számára. Általában közepes méretű végrehajtókkal kell rendelkeznie, mivel más folyamatok a rendelkezésre álló memóriát használják.|
|--végrehajtó – memória|Beállítja az egyes végrehajtók számára a memória méretét, amely a FONALon megjelenő halom méretét vezérli. Hagyjon némi memóriát a végrehajtás terheléséhez.|

## <a name="select-the-correct-executor-size"></a>A megfelelő végrehajtó méretének kiválasztása

A végrehajtó konfigurációjának meghatározásakor vegye figyelembe a Java Garbage Collection (GC) terhelését.

* A végrehajtó méretének csökkentésére szolgáló tényezők:
    1. Csökkentse a 32 GB alatti halom méretét a GC terhelésének < 10%-os megtartásához.
    2. Csökkentse a magok számát, hogy a GC terhelése < 10% legyen.

* A végrehajtó méretének növelésére szolgáló tényezők:
    1. A végrehajtók közötti kommunikációs terhelés csökkentése.
    2. Csökkentse a nyitott kapcsolatok számát a végrehajtók (N2) között nagyobb fürtökön (>100 végrehajtók).
    3. Növelje a nagy mennyiségű memóriát, hogy megfeleljen a memória-igényes feladatok számára.
    4. Nem kötelező: csökkentse a végrehajtó memória terhelését.
    5. Nem kötelező: növelje a használatot és a párhuzamosságot a processzor túllépésével.

Általános szabály a végrehajtó méretének kiválasztásakor:

1. Indítson el 30 GB-ot végrehajtóként, és terjesszen rendelkezésre álló gépi magokat.
2. Növelje a végrehajtó magok számát a nagyobb fürtöknél (> 100 végrehajtók).
3. A méretet a próbaverziós futtatások és az előző tényezők, például a GC terhelése alapján módosíthatja.

Az egyidejű lekérdezések futtatásakor vegye figyelembe a következőket:

1. Indítson el 30 GB-ot végrehajtóként és minden gépi magot.
2. Több párhuzamos Spark-alkalmazás létrehozása a processzor túllépésével (körülbelül 30%-os késéssel).
3. Lekérdezések terjesztése párhuzamos alkalmazások között.
4. A méretet a próbaverziós futtatások és az előző tényezők, például a GC terhelése alapján módosíthatja.

További információ a végrehajtók konfigurálásáról a Ambari használatával: [Apache Spark Settings-Spark végrehajtók](apache-spark-settings.md#configuring-spark-executors).

A lekérdezési teljesítmény figyelése a kiugró vagy más teljesítménnyel kapcsolatos problémák esetében az Idősor nézet alapján. Emellett az SQL Graph, a feladatok statisztikái és így tovább. A (z) és a Spark-előzményeket használó Spark-feladatok hibakeresésével kapcsolatos információkért lásd: [Apache Spark Azure HDInsight futó feladatok hibakeresése](apache-spark-job-debugging.md). A fonal idővonal-kiszolgálójának használatáról a következő témakörben talál további információt: [Apache HADOOP fonalak alkalmazási naplói](../hdinsight-hadoop-access-yarn-app-logs-linux.md).

Előfordulhat, hogy a végrehajtók közül egy vagy több lassabb, mint a többi, és a feladatok végrehajtása sokkal hosszabb ideig tart. Ez a lassulás gyakran fordul elő nagyobb fürtökön (> 30 csomópont). Ebben az esetben a munkát nagyobb számú feladatra osztja fel, így az ütemező kompenzálhatja a lassú feladatokat. Például legalább kétszer annyi feladatnak kell lennie, mint a végrehajtó magok száma az alkalmazásban. A feladatokhoz tartozó spekulatív végrehajtást is engedélyezheti `conf: spark.speculation = true` .

## <a name="next-steps"></a>Következő lépések

* [Az adatfeldolgozás optimalizálása Apache Spark](optimize-cluster-configuration.md)
* [Az adattároló optimalizálása Apache Spark](optimize-data-storage.md)
* [Memória használatának optimalizálása Apache Spark](optimize-memory-usage.md)
