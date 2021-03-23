---
title: Memóriabeli hiba elhárítása az Azure HDInsight
description: A HDInsight-ben észlelt memória-hiba elhárítása. Az ügyfél forgatókönyve több nagyméretű tábla lekérdezése.
keywords: kevés a memória, a bácsi, a kaptár beállításai
ms.service: hdinsight
ms.topic: troubleshooting
ms.custom: hdinsightactive
ms.date: 11/28/2019
ms.openlocfilehash: d1e8f596ee022a59baa89e7f78648c98420eb44b
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/23/2021
ms.locfileid: "104868868"
---
# <a name="fix-an-apache-hive-out-of-memory-error-in-azure-hdinsight"></a>Az Azure HDInsight Apache Hive memóriájában észlelt hiba elhárítása

Megtudhatja, hogyan lehet kijavítani egy Apache Hive memóriából (bácsi) származó hibát a nagyméretű táblák feldolgozásakor a struktúra memória-beállításainak konfigurálásával.

## <a name="run-apache-hive-query-against-large-tables"></a>Apache Hive lekérdezés futtatása nagyméretű táblákon

Az ügyfél egy struktúra-lekérdezést futtatott:

```sql
SELECT
    COUNT (T1.COLUMN1) as DisplayColumn1,
    …
    …
    ….
FROM
    TABLE1 T1,
    TABLE2 T2,
    TABLE3 T3,
    TABLE5 T4,
    TABLE6 T5,
    TABLE7 T6
where (T1.KEY1 = T2.KEY1….
    …
    …
```

A lekérdezés néhány árnyalata:

* A T1 egy nagy méretű tábla (TÁBLA1) aliasa, amely sok karakterlánc típusú oszloppal rendelkezik.
* A többi tábla nem túl nagy, de sok oszlopot tartalmaz.
* Minden táblázat csatlakozik egymáshoz, és bizonyos esetekben több oszlop szerepel a TÁBLA1 és másokban.

A kaptár lekérdezése 26 percet vett igénybe egy 24 csomópontos a3 HDInsight-fürtön. Az ügyfél a következő figyelmeztető üzeneteket észlelte:

```output
    Warning: Map Join MAPJOIN[428][bigTable=?] in task 'Stage-21:MAPRED' is a cross product
    Warning: Shuffle Join JOIN[8][tables = [t1933775, t1932766]] in Stage 'Stage-4:MAPRED' is a cross product
```

Az Apache TEZ-végrehajtó motor használatával. Ugyanez a lekérdezés 15 percig futott, és a következő hibaüzenetet dobta:

```output
    Status: Failed
    Vertex failed, vertexName=Map 5, vertexId=vertex_1443634917922_0008_1_05, diagnostics=[Task failed, taskId=task_1443634917922_0008_1_05_000006, diagnostics=[TaskAttempt 0 failed, info=[Error: Failure while running task:java.lang.RuntimeException: java.lang.OutOfMemoryError: Java heap space
        at
    org.apache.hadoop.hive.ql.exec.tez.TezProcessor.initializeAndRunProcessor(TezProcessor.java:172)
        at org.apache.hadoop.hive.ql.exec.tez.TezProcessor.run(TezProcessor.java:138)
        at
    org.apache.tez.runtime.LogicalIOProcessorRuntimeTask.run(LogicalIOProcessorRuntimeTask.java:324)
        at
    org.apache.tez.runtime.task.TezTaskRunner$TaskRunnerCallable$1.run(TezTaskRunner.java:176)
        at
    org.apache.tez.runtime.task.TezTaskRunner$TaskRunnerCallable$1.run(TezTaskRunner.java:168)
        at java.security.AccessController.doPrivileged(Native Method)
        at javax.security.auth.Subject.doAs(Subject.java:415)
        at org.apache.hadoop.security.UserGroupInformation.doAs(UserGroupInformation.java:1628)
        at
    org.apache.tez.runtime.task.TezTaskRunner$TaskRunnerCallable.call(TezTaskRunner.java:168)
        at
    org.apache.tez.runtime.task.TezTaskRunner$TaskRunnerCallable.call(TezTaskRunner.java:163)
        at java.util.concurrent.FutureTask.run(FutureTask.java:262)
        at java.util.concurrent.ThreadPoolExecutor.runWorker(ThreadPoolExecutor.java:1145)
        at java.util.concurrent.ThreadPoolExecutor$Worker.run(ThreadPoolExecutor.java:615)
        at java.lang.Thread.run(Thread.java:745)
    Caused by: java.lang.OutOfMemoryError: Java heap space
```

A hiba akkor is fennáll, ha nagyobb virtuális gépet használ (például D12).

## <a name="debug-the-out-of-memory-error"></a>A memórián kívüli hiba hibakeresése

Támogatási és mérnöki csapatunk közösen talált egy olyan problémát, amely miatt a memóriával kapcsolatos hiba az [Apache JIRA ismertetett ismert hibát](https://issues.apache.org/jira/browse/HIVE-8306)észlelt:

"A kaptár. Auto. convert. JOIN. noconditionaltask = true, ellenőrizze a noconditionaltask. size értéket, és ha a térképhez való csatlakozásnál a táblázatok mérete nem haladja meg a noconditionaltask. a terv mérete miatt a csomaghoz való csatlakozást eredményező probléma az, hogy a számítás nem veszi figyelembe a különböző szórótábla megvalósításával járó terhelést, ha a bemeneti méretek összege kisebb, mint a kisméretű lekérdezések noconditionaltask mérete."

Az hive-site.xml fájlban a **kaptár. Auto. convert. JOIN. noconditionaltask** értéke **true (igaz**):

```xml
<property>
    <name>hive.auto.convert.join.noconditionaltask</name>
    <value>true</value>
    <description>
            Whether Hive enables the optimization about converting common join into mapjoin based on the input file size.
            If this parameter is on, and the sum of size for n-1 of the tables/partitions for a n-way join is smaller than the
            specified size, the join is directly converted to a mapjoin (there is no conditional task).
    </description>
</property>
```

Valószínű, hogy a csatlakoztatási folyamat miatt a Java-tárterület kifogyott a memóriában. Ahogy az a HDInsight-ben, a [Hadoop fonal-memóriájának beállításai](/archive/blogs/shanyu/hadoop-yarn-memory-settings-in-hdinsight)című blogbejegyzésben leírtak szerint, a TEZ-végrehajtó motor használatakor a ténylegesen felhasznált halom terület a TEZ-tárolóhoz tartozik. Tekintse meg a TEZ tároló memóriáját ismertető következő képet.

:::image type="content" source="./media/hdinsight-hadoop-hive-out-of-memory-error-oom/hive-out-of-memory-error-oom-tez-container-memory.png" alt-text="Tez-tároló memória diagramja: a hiba a memóriában" border="false":::

Ahogy a blogbejegyzés is sugallja, a következő két memória-beállítás határozza meg a halom tárolójának memóriáját: **kaptár. TEZ. Container. size** és **kaptár. TEZ. Java. eldönti**. Tapasztalataink szerint a memórián kívüli kivétel nem azt jelenti, hogy a tároló mérete túl kicsi. Ez azt jelenti, hogy a Java halom mérete (kaptár. TEZ. Java. döntve) túl kicsi. Tehát amikor megjelenik a memória, megpróbálkozhat a **kaptár. TEZ. Java.** kiemeléssel. Szükség esetén előfordulhat, hogy a **kaptár. TEZ. Container. size** értékre kell emelkednie. A **Java.** kikapcsolási beállításnak a **container. size** 80%-ának kell lennie.

> [!NOTE]  
> A **kaptár. TEZ. Java. döntő** beállításnak mindig kisebbnek kell lennie, mint a **kaptár. TEZ. Container. size**.

Mivel a D12-gép 28 GB memóriával rendelkezik, úgy döntöttünk, hogy 10 GB-nyi (10240 MB) méretű tárolót használ, és az 80%-ot a Javához rendeli.

```console
SET hive.tez.container.size=10240
SET hive.tez.java.opts=-Xmx8192m
```

Az új beállításokkal a lekérdezés 10 percen belül sikeresen futott.

## <a name="next-steps"></a>Következő lépések

A bácsi-hiba beszerzése nem feltétlenül jelenti azt, hogy a tároló mérete túl kicsi. Ehelyett konfigurálnia kell a memória beállításait úgy, hogy a halom mérete megnövekszik, és a tárolói memória méretének legalább 80%-a. A kaptár-lekérdezések optimalizálásával kapcsolatban lásd: [Apache Hive lekérdezések optimalizálása a HDInsight Apache Hadoophoz](hdinsight-hadoop-optimize-hive-query.md).