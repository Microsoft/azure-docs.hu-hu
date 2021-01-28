---
title: Nyílt forráskódú szoftverek támogatása az Azure HDInsight
description: A Microsoft Azure a nyílt forráskódú technológiák általános támogatását biztosítja.
ms.service: hdinsight
ms.topic: how-to
ms.custom: seoapr2020
ms.date: 04/21/2020
ms.openlocfilehash: fec4cff974031982c782c9265a7d3186d6bb0233
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/28/2021
ms.locfileid: "98942559"
---
# <a name="open-source-software-support-in-azure-hdinsight"></a>Nyílt forráskódú szoftverek támogatása az Azure HDInsight

A Microsoft Azure HDInsight szolgáltatás olyan nyílt forráskódú technológiák környezetét használja, amelyeket a rendszer Apache Hadoop. A Microsoft Azure a nyílt forráskódú technológiák általános támogatását biztosítja. További információkért lásd az [Azure-támogatással kapcsolatos gyakori kérdések](https://azure.microsoft.com/support/faq/) **támogatásának hatókörét** ismertető szakaszt. A HDInsight szolgáltatás további szintű támogatást biztosít a beépített összetevőkhöz.

## <a name="components"></a>Összetevők

A HDInsight szolgáltatás két különböző típusú nyílt forráskódú összetevőből áll:

### <a name="built-in-components"></a>Beépített összetevők

Ezek az összetevők előre telepítve vannak a HDInsight-fürtökön, és biztosítják a fürt alapvető funkcióit. A következő összetevők tartoznak ehhez a kategóriához:

* [Apache HADOOP fonal](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html) Resource Manager.
* A kaptár lekérdezési nyelvének [HiveQL](https://cwiki.apache.org/confluence/display/Hive/LanguageManual).
* [Apache Mahout](https://mahout.apache.org/).

A fürt összetevőinek teljes listája elérhető a HDInsight-ben [elérhető Apache Hadoop-összetevők és-verziók közül?](hdinsight-component-versioning.md)

### <a name="custom-components"></a>Egyéni összetevők

A fürt felhasználója a munkaterheléshez bármilyen, a Közösségben elérhető vagy Ön által létrehozott összetevőt telepíthet vagy használhat.

> [!WARNING]  
> A HDInsight-fürthöz biztosított összetevők teljes mértékben támogatottak. Microsoft ügyfélszolgálata segít elkülöníteni és elhárítani ezeket az összetevőket érintő problémákat.
>
> Az egyéni összetevők kereskedelmileg ésszerű támogatást kapnak a probléma további megoldása érdekében. Előfordulhat, hogy a Microsoft ügyfélszolgálata fel tudja oldani a problémát. Vagy kérheti, hogy a nyílt forráskódú technológiákhoz elérhető csatornákat is igénybe vehet, amelyekben az adott technológia mélyreható szaktudása található. Számos közösségi webhely használható. Ilyenek például a [Microsoft Q&a HDInsight](/answers/topics/azure-hdinsight.html) és a [stack overflow](https://stackoverflow.com)kérdéseit.
>
> Az Apache-projektek az [Apache webhelyén](https://apache.org)is rendelkeznek Project-webhelyekkel. Ilyen például a [Hadoop](https://hadoop.apache.org/).

## <a name="component-usage"></a>Összetevő-használat

A HDInsight szolgáltatás számos módszert kínál az egyéni összetevők használatára. Ugyanez a támogatási szint érvényes, függetlenül attól, hogy az összetevő hogyan használható vagy van telepítve a fürtön. Az alábbi táblázat az egyéni összetevők HDInsight-fürtökön használt leggyakoribb módszereit ismerteti:

|Használat |Description |
|---|---|
|Feladatbeküldés|Az egyéni összetevőket végrehajtó vagy használó Hadoop vagy más típusú feladatok elküldhető a fürtbe.|
|Fürt testreszabása|A fürt létrehozása során megadhat további beállításokat és a fürtcsomópontokon telepített egyéni összetevőket.|
|Példák|A népszerű egyéni összetevők esetében a Microsoft és mások példákat is biztosítanak az összetevők HDInsight-fürtökön való használatára. Ezeket a mintákat támogatás nélkül biztosítjuk.|

## <a name="next-steps"></a>Következő lépések

* [Azure HDInsight-fürtök testreszabása parancsfájl-műveletek használatával](./hdinsight-hadoop-customize-cluster-linux.md)
* [Parancsfájl-műveleti parancsfájlok fejlesztése a HDInsight](hdinsight-hadoop-script-actions-linux.md)
* [Python-környezet biztonságos kezelése az Azure HDInsightban szkriptműveletekkel](./spark/apache-spark-python-package-installation.md)