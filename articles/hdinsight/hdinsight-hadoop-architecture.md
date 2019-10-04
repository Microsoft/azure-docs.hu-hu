---
title: Apache Hadoop architektúra – Azure HDInsight
description: Ismerteti Apache Hadoop tárolását és feldolgozását az Azure HDInsight-fürtökön.
author: ashishthaps
ms.author: ashishth
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/27/2019
ms.openlocfilehash: 3767ea10d777a0ea7ad88a2ffa4793e866ffbe6c
ms.sourcegitcommit: c79aa93d87d4db04ecc4e3eb68a75b349448cd17
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/18/2019
ms.locfileid: "71091482"
---
# <a name="apache-hadoop-architecture-in-hdinsight"></a>Apache Hadoop-architektúra a HDInsightban

A [Apache Hadoop](https://hadoop.apache.org/) két alapvető összetevőből áll: a [Apache HADOOP elosztott fájlrendszer (HDFS)](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsUserGuide.html) , amely tárolót biztosít, és [Apache Hadoop még egy olyan erőforrás-tárgyalót (fonalat)](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html) , amely feldolgozást biztosít. A tárolási és feldolgozási képességekkel a fürt képes lesz a [MapReduce](https://hadoop.apache.org/docs/current/hadoop-mapreduce-client/hadoop-mapreduce-client-core/MapReduceTutorial.html) programok futtatására a kívánt adatfeldolgozás végrehajtásához.

> [!NOTE]  
> A HDFS nem a HDInsight-fürtön belül helyezi üzembe a tároló biztosításához. Ehelyett egy HDFS-kompatibilis illesztőfelület-réteget használ a Hadoop-összetevők. A tényleges tárolási képességet Azure Storage vagy Azure Data Lake Storage biztosítjuk. A Hadoop esetében a HDInsight-fürtön futtatott MapReduce-feladatok úgy futnak, mintha egy HDFS voltak, és így nem szükséges módosítani a tárolási igényeiket. A HDInsight Hadoop a tárolás kiszervezve, de a FONÁL feldolgozása továbbra is egy alapvető összetevő. További információ: [Az Azure HDInsight bemutatása](hadoop/apache-hadoop-introduction.md).

Ez a cikk a FONALat mutatja be, valamint azt, hogyan koordinálja az alkalmazások végrehajtását a HDInsight-on.

## <a name="apache-hadoop-yarn-basics"></a>A fonal Apache Hadoop alapjai 

A fonal a Hadoop-ben szabályozza és koordinálja az adatfeldolgozást. A fonal két fő szolgáltatással rendelkezik, amelyek folyamatokként futnak a fürt csomópontjain: 

* ResourceManager 
* NodeManager

A erőforráskezelő a fürt számítási erőforrásait a MapReduce-feladatokhoz hasonló alkalmazások számára biztosítja. A erőforráskezelő tárolóként engedélyezi ezeket az erőforrásokat, ahol mindegyik tároló a CPU-magok és a memória memóriájának kiosztását tartalmazza. Ha kombinálja a fürtben rendelkezésre álló összes erőforrást, majd a magok és a memória blokkokban való elosztását, minden egyes blokk erőforrás egy tároló. A fürt minden csomópontja rendelkezik bizonyos számú tároló kapacitásával, ezért a fürtnek van rögzített korlátja a rendelkezésre álló tárolók számánál. A tárolóban lévő erőforrások kiosztása konfigurálható. 

Amikor egy MapReduce-alkalmazás egy fürtön fut, a erőforráskezelő biztosítja az alkalmazást, hogy mely tárolók futnak. A erőforráskezelő nyomon követi a futó alkalmazások állapotát, a rendelkezésre álló szektorcsoportok kapacitását, és nyomon követi az alkalmazásokat, és felszabadítja az erőforrásokat. 

A erőforráskezelő egy webkiszolgáló-folyamatot is futtat, amely webes felhasználói felületet biztosít az alkalmazások állapotának figyeléséhez.

Amikor egy felhasználó elküld egy MapReduce alkalmazást a fürtön való futtatásra, az alkalmazás a erőforráskezelő lesz elküldve. A erőforráskezelő pedig kioszt egy tárolót az elérhető NodeManager-csomópontokon. A NodeManager-csomópontok, ahol az alkalmazás ténylegesen fut. Az első lefoglalt tároló egy ApplicationMaster nevű speciális alkalmazást futtat. Ennek a ApplicationMaster az a feladata, hogy az elküldött alkalmazás futtatásához szükséges további tárolók formájában beszerezze az erőforrásokat. A ApplicationMaster megvizsgálja az alkalmazás szakaszait, például a térképi fázist és a szakasz csökkentését, valamint azt, hogy mekkora mennyiségű adatok feldolgozása szükséges. A ApplicationMaster ezután megkéri (*egyezteti*) az erőforrásokat a erőforráskezelő az alkalmazás nevében. A erőforráskezelő a fürt Csomópontkezelők származó erőforrásokat biztosít ahhoz a ApplicationMaster, amelyet az alkalmazás végrehajtásához kíván használni. 

A Csomópontkezelők futtatják az alkalmazást alkotó feladatokat, majd jelentést készítenek az előrehaladásról és az állapotról a ApplicationMaster. A ApplicationMaster visszaküldi az alkalmazás állapotát a erőforráskezelő. A erőforráskezelő bármilyen eredményt ad vissza az ügyfélnek.

## <a name="yarn-on-hdinsight"></a>FONAL a HDInsight

Az összes HDInsight-fürt a FONALat helyezi üzembe. A erőforráskezelő a magas rendelkezésre álláshoz van telepítve egy elsődleges és egy másodlagos példánnyal, amely az első és a második főcsomóponton fut a fürtön belül. Egyszerre csak a erőforráskezelő egyetlen példánya aktív. A NodeManager-példányok a fürt rendelkezésre álló munkavégző csomópontjain futnak.

![Apache-fonal az Azure HDInsight](./media/hdinsight-hadoop-architecture/apache-yarn-on-hdinsight.png)

## <a name="next-steps"></a>További lépések

* [A MapReduce használata a HDInsight-alapú Apache Hadoopban](hadoop/hdinsight-use-mapreduce.md)
* [Az Azure HDInsight bemutatása](hadoop/apache-hadoop-introduction.md)
