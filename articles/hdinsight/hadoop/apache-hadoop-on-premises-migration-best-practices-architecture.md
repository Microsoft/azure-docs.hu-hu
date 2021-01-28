---
title: 'Architektúra: helyszíni Apache Hadoop az Azure HDInsight'
description: Ismerje meg az architektúrával kapcsolatos ajánlott eljárásokat a helyszíni Hadoop-fürtök Azure HDInsight történő áttelepítéséhez.
ms.reviewer: ashishth
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 12/06/2019
ms.openlocfilehash: eb01f2fabb89c6928dc4919987efa27df36a8a7a
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/28/2021
ms.locfileid: "98946443"
---
# <a name="migrate-on-premises-apache-hadoop-clusters-to-azure-hdinsight---architecture-best-practices"></a>Helyszíni Apache Hadoop-fürtök migrálása az Azure HDInsight-architektúrára – ajánlott eljárások

Ez a cikk az Azure HDInsight-rendszerek architektúrájának javaslatait ismerteti. Egy sorozat része, amely ajánlott eljárásokat biztosít a helyszíni Apache Hadoop rendszerek Azure HDInsight való áttelepítésének segítésére.

## <a name="use-multiple-workload-optimized-clusters"></a>Több munkaterhelés használatára optimalizált fürtök használata

Számos helyszíni Apache Hadoop üzemelő példány egyetlen nagyméretű fürtből áll, amely sok munkaterhelést támogat. Ez az egyetlen fürt összetett lehet, és az egyes szolgáltatásokhoz való illetéktelen behatolást igényelhet, hogy minden működjenek együtt. A helyszíni Hadoop-fürtök Azure HDInsight-re való áttelepítéséhez meg kell változtatni a megközelítést.

Az Azure HDInsight-fürtök adott típusú számítási használatra vannak kialakítva. Mivel a tárterület több fürtön is megosztható, lehetséges, hogy több munkaterhelés-optimalizált számítási fürtöt hozhat létre a különböző feladatok igényeinek kielégítése érdekében. Minden egyes fürthöz az adott számítási feladathoz tartozó optimális konfiguráció tartozik. A következő táblázat a HDInsight és a megfelelő munkaterhelések támogatott fürtjének típusait sorolja fel.

|Számítási feladat|HDInsight-fürt típusa|
|---|---|
|Kötegelt feldolgozás (ETL/ELT)|Hadoop, Spark|
|Adatraktározás|Hadoop, Spark, interaktív lekérdezés|
|IoT/streaming|Kafka, Storm, Spark|
|NoSQL tranzakciós feldolgozás|HBase|
|Interaktív és gyorsabb lekérdezések memórián belüli gyorsítótárazással|Interaktív lekérdezés|
|Adattudomány|ML szolgáltatások, Spark|

A következő táblázat a HDInsight-fürtök létrehozásához használható különböző metódusokat mutatja be.

|Eszköz|Böngésző-alapú|Parancssor|REST API|SDK|
|---|---|---|---|---|
|[Azure Portal](../hdinsight-hadoop-create-linux-clusters-portal.md)|X||||
|[Azure Data Factory](../hdinsight-hadoop-create-linux-clusters-adf.md)|X|X|X|X|
|[Azure CLI (ver 1,0)](../hdinsight-hadoop-create-linux-clusters-azure-cli.md)||X|||
|[Azure PowerShell](../hdinsight-hadoop-create-linux-clusters-azure-powershell.md)||X|||
|[cURL](../hdinsight-hadoop-create-linux-clusters-curl-rest.md)||X|X||
|[.NET SDK](/dotnet/api/overview/azure/hdinsight?view=azure-dotnet&preserve-view=true)||||X|
|[Python SDK](/python/api/overview/azure/hdinsight)||||X|
|[Java SDK](/java/api/overview/azure/hdinsight)||||X|
|[Azure Resource Manager-sablonok](../hdinsight-hadoop-create-linux-clusters-arm-templates.md)||X|||

További információkért lásd a [fürtök típusai a HDInsight](../hadoop/apache-hadoop-introduction.md)című cikket.

## <a name="use-transient-on-demand-clusters"></a>Átmeneti igény szerinti fürtök használata

A HDInsight-fürtök hosszú ideig nem használhatók fel. Az erőforrás-költségek megtakarításának elősegítése érdekében a HDInsight támogatja az igény szerinti átmeneti fürtöket, amelyek a munkaterhelés sikeres befejeződése után törölhetők.

Fürt törlésekor a társított Storage-fiók és a külső metaadatok nem törlődnek. A fürtöt később újra létre lehet hozni ugyanazzal a Storage-fiókkal és meta-tárolókkal.

Az Azure Data Factory használatával ütemezhetik az igény szerinti HDInsight-fürtök létrehozását. További információkért tekintse [meg az igény szerinti Apache Hadoop-fürtök létrehozása a HDInsight-ben a Azure Data Factory használatával](../hdinsight-hadoop-create-linux-clusters-adf.md)című cikket.

## <a name="decouple-storage-from-compute"></a>Tárterület kiválasztása a számítási feladatokból

A szokásos helyszíni Hadoop üzemelő példányok ugyanazt a gépet használják az adattárolás és az adatfeldolgozás számára. Mivel közösen helyezkednek el, a számítási és a tárolási kapacitást együtt kell méretezni.

A HDInsight-fürtökön a tárterületet nem szükséges a számítási és az Azure Storage-ban, Azure Data Lake Storage vagy mindkettőn elhelyezni. A tárterületnek a számításból való leválasztása a következő előnyökkel jár:

- Fürtök közötti adatmegosztás.
- Átmeneti fürtök használata, mivel az adatmennyiség nem függ a fürttől.
- Csökkentett tárolási díj.
- A tárterület és a számítások méretezése külön történik.
- Adatreplikáció régiók között.

A számítási fürtöket az Azure-régióban található Storage-fiók erőforrásaihoz közelítve hozhatja létre a számítási és tárolási műveletek elválasztásának a teljesítményének csökkentése érdekében. A nagy sebességű hálózatok azt teszik hatékonyabbá, hogy a számítási csomópontok hozzáférjenek az Azure Storage-ban tárolt adatokhoz.

## <a name="use-external-metadata-stores"></a>Külső metaadat-tárolók használata

Két fő metaadattárak működik a HDInsight-fürtökkel: [Apache Hive](https://hive.apache.org/) és [Apache Oozie](https://oozie.apache.org/). A Hive-metaadattár a központi séma tárháza, amelyet adatfeldolgozó motorok használhatnak, például a Hadoop, a Spark, a LLAP, a Presto és az Apache Pig. A Oozie metaadattár az ütemezés részleteit és a folyamatban lévő és befejezett Hadoop feladatok állapotát tárolja.

A HDInsight Azure SQL Database használ a kaptár és a Oozie metaadattárak. A HDInsight-fürtökben kétféleképpen állíthatók be metaadattár:

1. Alapértelmezett metaadattár

    - Nincs további díj.
    - A metaadattár a fürt törlésekor törlődik.
    - A metaadattár nem oszthatók meg különböző fürtök között.
    - Az alapszintű Azure SQL DB-t használja, amely öt DTU korláttal rendelkezik.

1. Egyéni külső metaadattár

    - A metaadattár külső Azure SQL Database adható meg.
    - A fürtöket a metaadatok elvesztése nélkül hozhatja létre és törölheti, beleértve a kaptár-séma Oozie feladatának részleteit.
    - Az egyetlen metaadattár-adatbázis különböző típusú fürtökkel osztható meg.
    - A metaadattár igény szerint méretezhető.
    - További információ: [külső metaadat-tárolók használata az Azure HDInsight-ben](../hdinsight-use-external-metadata-stores.md).

## <a name="best-practices-for-hive-metastore"></a>Ajánlott eljárások a kaptár Metaadattár

Néhány HDInsight Hive-metaadattár ajánlott eljárás a következő:

- A számítási erőforrások és a metaadatok elkülönítéséhez használjon egyéni külső metaadattár.
- Kezdje egy S2 szintű Azure SQL-példánnyal, amely 50 DTU és 250 GB tárterületet biztosít. Ha szűk keresztmetszet jelenik meg, az adatbázis felskálázása felfelé is elvégezhető.
- Ne ossza meg a HDInsight-fürt egyik verziójához létrehozott metaadattár eltérő verziójú fürtökkel. A különböző kaptár-verziók különböző sémákat használnak. Egy metaadattár például nem osztható meg a kaptár 1,2 és a kaptár 2,1 fürtökkel.
- Rendszeresen biztonsági másolatot készíthet az egyéni metaadattár.
- Tartsa a metaadattár és a HDInsight-fürtöt ugyanabban a régióban.
- A teljesítmény és a rendelkezésre állás metaadattár figyelése Azure SQL Database monitorozási eszközök, például Azure Portal vagy Azure Monitor naplók használatával.
- A `ANALYZE TABLE` táblák és oszlopok statisztikáinak létrehozásához szükség szerint hajtsa végre a parancsot. Például: `ANALYZE TABLE [table_name] COMPUTE STATISTICS`.

## <a name="best-practices-for-different-workloads"></a>Ajánlott eljárások a különböző számítási feladatokhoz

- Érdemes lehet LLAP-fürtöt használni az interaktív struktúra-lekérdezésekhez a továbbfejlesztett válaszidő- [LLAP](https://cwiki.apache.org/confluence/display/Hive/LLAP) a kaptár 2,0 új funkciója, amely lehetővé teszi a lekérdezések memórián belüli gyorsítótárazását. A LLAP sokkal gyorsabban teszi a kaptár-lekérdezéseket, és akár [26x gyorsabban, mint a kaptár 1. x bizonyos esetekben](https://hortonworks.com/blog/announcing-apache-hive-2-1-25x-faster-queries-much/).
- Vegye fontolóra a Spark-feladatok használatát a kaptár-feladatok helyett.
- Érdemes lehet LLAP-lekérdezésekkel rendelkező Impala-alapú lekérdezéseket cserélni.
- Érdemes lehet MapReduce-feladatokat felváltani a Spark-feladatokkal.
- Érdemes lehet kis késleltetésű Spark batch-feladatokat felváltani a Spark Structured streaming-feladatok használatával.
- Az adatelőkészítéshez érdemes Azure Data Factory (ADF) 2,0-et használni.
- Tekintse át a Ambari.
- Módosítsa az adattárolót a helyszíni HDFS a WASB vagy a ADLS vagy az ADFS használatával a parancsfájlok feldolgozásához.
- Érdemes lehet a Ranger RBAC használni a kaptár-táblákon és a naplózáson.
- Vegye fontolóra a CosmosDB használatát a MongoDB vagy a Cassandra helyett.

## <a name="next-steps"></a>Következő lépések

Olvassa el a következő cikket a sorozatban:

- [Infrastruktúra-ajánlott eljárások a helyszíni Azure HDInsight Hadoop áttelepítéshez](apache-hadoop-on-premises-migration-best-practices-infrastructure.md)