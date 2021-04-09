---
title: Mi az Apache HBase az Azure HDInsight?
description: Az Apache HBase HDInsightban való használatának bevezetése, amely egy, a Hadoop programra épített NoSQL-adatbázis. Megismerheti a használati eseteket, és összehasonlíthatja a HBase programot más Hadoop-fürtökkel.
ms.service: hdinsight
ms.topic: overview
ms.custom: hdinsightactive,hdiseo17may2017,seoapr2020
ms.date: 04/20/2020
ms.openlocfilehash: 98872cc1315d946c63825318d2b98460031e128a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98942914"
---
# <a name="what-is-apache-hbase-in-azure-hdinsight"></a>Mi az Apache HBase az Azure HDInsight

Az [Apache HBase](https://hbase.apache.org/) egy nyílt forráskódú, NoSQL-adatbázis, amely a [Google BigTable](https://cloud.google.com/bigtable/)szolgáltatáson alapuló, Apache Hadoopra épül. A HBase véletlenszerű hozzáférést és erős konzisztenciát biztosít a séma nélküli adatbázisban található nagy mennyiségű adattal kapcsolatban. Az adatbázist az oszlopok családja rendezi.

A felhasználói szemszögből a HBase hasonló egy adatbázishoz. Az adattárolás egy tábla soraiban és oszlopaiban történik, és egy sorban lévő, oszlopos család szerint csoportosított adatmennyiség. A HBase séma nélküli adatbázis. Az oszlopok és az adattípusok nem definiálhatók a használatuk előtt. A nyílt forráskód lineáris módon méreteződik át a több ezer csomópontnyi adat petabájtjainak kezelése érdekében. Támaszkodhat az adatredundanciára, a kötegelt feldolgozásra és az elosztott alkalmazások által a Hadoop-környezetben elérhető egyéb funkciókra is.

## <a name="how-is-apache-hbase-implemented-in-azure-hdinsight"></a>Hogyan valósítható meg az Apache HBase az Azure HDInsight?

A HDInsight HBase az Azure környezetbe integrált felügyelt fürtként érhető el. A fürtök úgy vannak konfigurálva, hogy közvetlenül az [Azure Storage](./../hdinsight-hadoop-use-blob-storage.md)-ban tárolják az adataikat, ami alacsony késést és nagyobb rugalmasságot biztosít a teljesítmény és a költséghatékonyság terén. Ez a tulajdonság lehetővé teszi, hogy az ügyfelek olyan interaktív webhelyeket hozzanak létre, amelyek nagy adatkészletekkel működnek. Olyan szolgáltatásokat hozhat létre, amelyek több millió végpontból származó érzékelő-és telemetria-adatok tárolására szolgálnak. Az adatok elemzéséhez Hadoop-feladatokkal. A HBase és a Hadoop jó kiindulási pont az Azure-beli big data-projekthez. A szolgáltatások lehetővé teszik a valós idejű alkalmazások számára a nagyméretű adatkészletek használatával történő munkát.

A HDInsight implementációja a HBase kibővített architektúrájának használatával biztosítja a táblák automatikus horizontális felskálázását. Az olvasási és írási műveletek, valamint az automatikus feladatátvétel erős konzisztenciája. A teljesítményt a memóriába való gyorsítótárazás növeli az olvasáshoz, és a nagy streaming-kapacitás az írásokhoz. A HBase-fürt a virtuális hálózaton belül hozható létre. További információ: [HDInsight-fürtök létrehozása az Azure Virtual Networkön](./apache-hbase-provision-vnet.md).

## <a name="how-is-data-managed-in-hdinsight-hbase"></a>Hogyan történik az adatok kezelése a HDInsight HBase-ben?

Az adatok a HBase-ben a(z) `create`, `get`, `put` és `scan` parancsokkal kezelhetők a HBase rendszerhéjból. Az adatok a(z) `put` paranccsal írhatók, és a(z) `get` paranccsal olvashatók az adatbázisban. A(z) `scan` paranccsal szerezhetők be adatok a táblák több sorából. Az adatok a HBase C# API-val is kezelhetők, amely egy ügyfélkönyvtárat biztosít a HBase REST API-n felül. A HBase-adatbázis a [Apache Hive](https://hive.apache.org/)használatával is lekérdezhető. A programozási modellek bevezetését itt tekintheti meg: az [Apache HBase és Apache Hadoop a HDInsight használatának első lépései](./apache-hbase-tutorial-get-started-linux.md). A rendszer szintén elérhetővé teszi a munkafolyamatokat, amelyek lehetővé teszik az adatfeldolgozást az adatbázist futtató csomópontokon.

> [!NOTE]  
> A HBase nem támogatja a Thriftet a HDInsightban.

## <a name="use-cases-for-apache-hbase"></a>Az Apache HBase használati esetei

A kanonikus használati eset, amelyre a BigTable (és az HBase-t) a webes keresésből hozták létre. A keresőmotorok indexeket építenek, amelyek kifejezéseket képeznek le az azokat tartalmazó weblapokra. A HBase azonban sok más használati esethez megfelelő – amelyek közül több ebben a szakaszban van felsorolva.

|Forgatókönyv |Leírás |
|---|---|
|Kulcs-érték tároló|A HBase használható kulcs-érték tárolóként, és alkalmas az üzenetek rendszerének kezelésére. A Facebook HBase használ az üzenetkezelési rendszerhez, és ideális megoldás az internetes kommunikáció tárolására és kezelésére. A WebTable a HBase eszközt használja a weblapokról kinyert táblázatok keresésére és kezelésére.|
|Érzékelői adatok|A HBase hasznos a növekményesen, különböző forrásokból gyűjtött adatok rögzítéséhez. Ezek az adatkészletek a közösségi elemzések és az idősorozatok közé tartoznak. Az interaktív irányítópultok naprakészen tartása a trendek és a számlálók használatával, valamint a naplózási rendszerek kezelése. Ilyenek például a Bloomberg Trader Terminal és a Open Time Series-adatbázis (OpenTSDB). A OpenTSDB tárolja és hozzáférést biztosít a kiszolgálói rendszerek állapotáról összegyűjtött mérőszámokhoz.|
|Valós idejű lekérdezés|A [Apache Phoenix](https://phoenix.apache.org/) az Apache HBase SQL-lekérdezési motorja. A szolgáltatás JDBC-illesztőprogramként érhető el, és lehetővé teszi a HBase-táblák lekérdezését és kezelését az SQL használatával.|
|A HBase platformként|Az alkalmazások a HBase felett futhatnak adattárolóként. Ilyenek például a Phoenix, a OpenTSDB, `Kiji` a és a Titan. Az alkalmazások integrálhatók is a HBase eszközzel. Ilyenek például a következők: [Apache Hive](https://hive.apache.org/), Apache Pig, [Solr](https://lucene.apache.org/solr/), Apache Storm, Apache Flume, [Apache Impala](https://impala.apache.org/), Apache Spark, `Ganglia` és Apache Drill.|

## <a name="next-steps"></a>Következő lépések

* [Az Apache HBase használatának első lépései a HDInsight Apache Hadoop](./apache-hbase-tutorial-get-started-linux.md)
* [HDInsight-fürtök létrehozása az Azure Virtual Networkön](./apache-hbase-provision-vnet.md)
* [Apache HBase-replikálás konfigurálása a HDInsight-ben](apache-hbase-replication.md)
