---
title: Az Apache Pig használata
titleSuffix: Azure HDInsight
description: Ismerje meg, hogyan használható a Pig a Apache Hadoop on HDInsight.
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: how-to
ms.date: 01/28/2020
ms.openlocfilehash: 7b74a41f7d6b636dddce0388d5ee0e0a12658d52
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/28/2021
ms.locfileid: "98944610"
---
# <a name="use-apache-pig-with-apache-hadoop-on-hdinsight"></a>Az Apache Pig használata a Apache Hadoop on HDInsight

Ismerje meg, hogyan használható az [Apache Pig](https://pig.apache.org/) és a HDInsight.

Az Apache Pig olyan platform, amely a *Pig Latin* néven ismert eljárási nyelv használatával Apache Hadoop programok létrehozására szolgál. A Pig egy alternatívája a Java számára a *MapReduce* -megoldások létrehozásához, és az Azure HDInsight része. A következő táblázat segítségével megismerheti a HDInsight által használható különféle módszereket:

## <a name="why-use-apache-pig"></a><a id="why"></a>Az Apache Pig használatának előnyei

Az adatok az Hadoop-ben való MapReduce használatával történő feldolgozásának egyik kihívása, hogy a feldolgozási logikát csak Térkép és egy csökkentő függvény használatával implementálja. Az összetett feldolgozáshoz gyakran kell a feldolgozást több olyan MapReduce-műveletbe felosztani, amely a kívánt eredmény elérése érdekében össze van fűzve.

A Pig lehetővé teszi, hogy a feldolgozást olyan átalakítások sorozata alapján határozza meg, amelyekben az adatforgalom a kívánt kimenetet eredményezi.

A Pig Latin nyelve lehetővé teszi a nyers bemenetből származó adatfolyamok leírását egy vagy több átalakításon keresztül a kívánt kimenet létrehozásához. A Pig Latin programok az alábbi általános mintát követik:

* **Load (Betöltés**): a fájlrendszerből módosítható adatok beolvasása.

* **Átalakítás**: az adatkezelés.

* **Dump vagy Store**: kimeneti adatokat a képernyőre, vagy tárolja feldolgozásra.

### <a name="user-defined-functions"></a>Felhasználó által meghatározott függvények

A Pig Latin is támogatja a felhasználó által definiált függvényeket (UDF), amely lehetővé teszi olyan külső összetevők meghívását, amelyek a Pig Latin nyelvben nehéznek bizonyuló logikát implementálnak.

A Pig Latin nyelvről további információt a [Pig Latin Reference Manual 1](https://archive.cloudera.com/cdh/3/pig/piglatin_ref1.html) és a [Pig Latin Reference Manual 2](https://archive.cloudera.com/cdh/3/pig/piglatin_ref2.html)című részben talál.

## <a name="example-data"></a><a id="data"></a>Példa az adatértékekre

A HDInsight különböző, a és a címtárakban tárolt adatkészleteket biztosít `/example/data` `/HdiSamples` . Ezek a könyvtárak a fürt alapértelmezett tárolójában találhatók. Az ebben a dokumentumban szereplő Pig példa a *log4j* fájlt használja `/example/data/sample.log` .

A fájlban lévő összes napló egy olyan mezőből áll, amely tartalmaz egy `[LOG LEVEL]` mezőt a típus és a súlyosság megjelenítéséhez, például:

```output
2012-02-03 20:26:41 SampleClass3 [ERROR] verbose detail for id 1527353937
```

Az előző példában a naplózási szint hiba.

> [!NOTE]  
> Létrehozhat egy log4j-fájlt is az [Apache log4j](https://en.wikipedia.org/wiki/Log4j) naplózási eszköz használatával, majd feltöltheti a fájlt a blobba. Útmutatásért tekintse [meg az adatok feltöltése a HDInsight](hdinsight-upload-data.md) című témakört. További információ arról, hogyan használhatók a Blobok az Azure Storage-ban a HDInsight használatával: az [azure blob Storage használata a HDInsight](hdinsight-hadoop-use-blob-storage.md).

## <a name="example-job"></a><a id="job"></a>Példa feladatokra

A következő Pig Latin-feladatok betöltik a `sample.log` fájlt a HDInsight-fürt alapértelmezett tárolójából. Ezután végrehajtja az átalakítások egy sorozatát, ami azt eredményezi, hogy hányszor fordult elő az egyes naplók a bemeneti adatokban. Az eredmények az STDOUT-ba íródnak.

```output
LOGS = LOAD 'wasb:///example/data/sample.log';
LEVELS = foreach LOGS generate REGEX_EXTRACT($0, '(TRACE|DEBUG|INFO|WARN|ERROR|FATAL)', 1)  as LOGLEVEL;
FILTEREDLEVELS = FILTER LEVELS by LOGLEVEL is not null;
GROUPEDLEVELS = GROUP FILTEREDLEVELS by LOGLEVEL;
FREQUENCIES = foreach GROUPEDLEVELS generate group as LOGLEVEL, COUNT(FILTEREDLEVELS.LOGLEVEL) as COUNT;
RESULT = order FREQUENCIES by COUNT desc;
DUMP RESULT;
```

Az alábbi képen egy Összegzés látható, hogy az egyes átalakítások mit tesznek az adatokon.

![Az átalakítások grafikus ábrázolása][image-hdi-pig-data-transformation]

## <a name="run-the-pig-latin-job"></a><a id="run"></a>A Pig Latin-feladatok futtatása

A HDInsight számos módszer használatával futtathat Pig Latin feladatokat. A következő táblázat segítségével eldöntheti, hogy melyik módszer a legmegfelelőbb, majd kövesse a bemutató hivatkozását.

## <a name="pig-and-sql-server-integration-services"></a>Pig és SQL Server Integration Services

A Pig-feladatok futtatásához SQL Server Integration Services (SSIS) használható. A SSIS készült Azure Feature Pack a következő összetevőket nyújtja, amelyek a HDInsight-on végzett Pig-feladatokkal működnek.

* [Azure HDInsight Pig-feladat][pigtask]

* [Azure-előfizetési kapcsolatkezelő][connectionmanager]

További információ [a SSIS készült][ssispack]Azure Feature Packről.

## <a name="next-steps"></a><a id="nextsteps"></a>Következő lépések

Most, hogy megtanulta, hogyan használhatja a Pigt a HDInsight-mel, az alábbi hivatkozásokat követve megismerheti az Azure HDInsight használatát.

* [Adatok feltöltése a HDInsightba](hdinsight-upload-data.md)
* [Apache Hive használata a HDInsight](./hadoop/hdinsight-use-hive.md)
* [Apache Sqoop használata a HDInsight](./hadoop/hdinsight-use-sqoop.md)
* [MapReduce-feladatok használata a HDInsightban](./hadoop/hdinsight-use-mapreduce.md)

[apachepig-home]: https://pig.apache.org/
[putty]: https://www.chiark.greenend.org.uk/~sgtatham/putty/download.html
[curl]: https://curl.haxx.se/
[pigtask]: /sql/integration-services/control-flow/azure-hdinsight-pig-task?viewFallbackFrom=sql-server-2014
[connectionmanager]: /sql/integration-services/connection-manager/azure-subscription-connection-manager?viewFallbackFrom=sql-server-2014
[ssispack]: /sql/integration-services/azure-feature-pack-for-integration-services-ssis?viewFallbackFrom=sql-server-2014
[hdinsight-admin-powershell]: hdinsight-administer-use-powershell.md

[hdinsight-use-hive]:../hdinsight-use-hive.md

[hdinsight-provision]: hdinsight-hadoop-provision-linux-clusters.md
[hdinsight-submit-jobs]:submit-apache-hadoop-jobs-programmatically.md#mapreduce-sdk

[Powershell-install-configure]: /powershell/azure/

[powershell-start]: https://technet.microsoft.com/library/hh847889.aspx


[image-hdi-pig-data-transformation]: ./media/use-pig/hdi-data-transformation.gif