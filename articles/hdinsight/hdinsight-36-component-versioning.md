---
title: Apache Hadoop összetevők és verziók – Azure HDInsight 3,6
description: Ismerkedjen meg az Azure HDInsight 3,6-es verziójának Apache Hadoop összetevőivel és verzióival.
ms.service: hdinsight
ms.topic: conceptual
author: deshriva
ms.author: deshriva
ms.date: 02/08/2021
ms.openlocfilehash: 32b287c3d7b1974db5a079d1ee84aaafad3faed7
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "105727688"
---
# <a name="hdinsight-36-component-versions"></a>HDInsight 3,6 összetevő-verziók

Ebben a cikkben megismerheti a Apache Hadoop környezeti összetevőit és verzióit az Azure HDInsight 3,6-ben.

## <a name="support-for-hdinsight-36"></a>A HDInsight 3,6 támogatása

2021. július 1-től a Microsoft alapszintű támogatás biztosít bizonyos HDI 3,6-fürtökhöz.
Az alábbi táblázat felsorolja a HDInsight 3,6-fürtök támogatási időkeretét.

| Fürt típusa                    | Keretrendszer verziója | Standard szintű támogatás lejárata       | alapszintű támogatás lejárati dátum | Nyugdíjazás dátuma |
|---------------------------------|-------------------|-----------------------------------|------------------------------|-----------------|
| HDInsight 3,6 Hadoop            | 2.7.3             | Június 30., 2021                     | 2022. április 3.                | 2022. április 4. |
| HDInsight 3,6 Spark             | 2.3               | Június 30., 2021                     | 2022. április 3.                | 2022. április 4. |
| HDInsight 3,6 Kafka             | 1.1               | Június 30., 2021                     | 2022. április 3.                | 2022. április 4. |
| HDInsight 3,6 HBase             | 1.1               | Június 30., 2021                     | 2022. április 3.                | 2022. április 4. |
| HDInsight 3,6 interaktív lekérdezés | 2.1               | Június 30., 2021                     | 2022. április 3.                | 2022. április 4. |
| HDInsight 3,6 Storm             | 1.1               | Június 30., 2021                     | 2022. április 3.                | 2022. április 4. |
| HDInsight 3,6 ML-szolgáltatások      | 9,3               | -                                 | -                            | December 31., 2020 |
| HDInsight 3,6 Spark             | 2,2               | -                                 | -                            | Június 30., 2020 |
| HDInsight 3,6 Spark             | 2.1               | -                                 | -                            | Június 30., 2020 |
| HDInsight 3,6 Kafka             | 1.0               | -                                 | -                            | Június 30., 2020 |

## <a name="apache-components-available-with-hdinsight-version-36"></a>Az HDInsight 3,6-es verziójában elérhető Apache-összetevők

A 3,6-es HDInsight társított OSS-összetevők verziói a következő táblázatban láthatók.

| Összetevő              | HDInsight 3,6 (alapértelmezett)     |
|------------------------|-----------------------------|
| Apache Hadoop és fonal | 2.7.3                       |
| Apache TEZ             | 0.7.0                       |
| Apache Pig             | 0.16.0                      |
| Apache Hive            | (2.1.0 az ESP interaktív lekérdezésen) |
| Apache TEZ Hive2       | 0.8.4                       |
| Apache Ranger          | 0.7.0                       |
| Apache HBase           | 1.1.2                       |
| Apache Sqoop           | 1.4.6                       |
| Apache Oozie           | 4.2.0                       |
| Apache Zookeeper       | 3.4.6                       |
| Apache Storm           | 1.1.0                       |
| Apache Mahout          | 0.9.0 +                      |
| Apache Phoenix         | 4.7.0                       |
| Apache Spark           | 2.3.2.                      |
| Apache Livy            | 0,4.                        |
| Apache Kafka           | 1.1                         |
| Apache Ambari          | 2.6.0                       |
| Apache Zeppelin        | 0.7.3                       |
| Mono                   | 4.2.1                       |

## <a name="next-steps"></a>Következő lépések

- [Fürt beállítása Apache Hadoop, Spark és más HDInsight](hdinsight-hadoop-provision-linux-clusters.md)
- [Enterprise Security Package](./enterprise-security-package.md)
- [Munkahelyi Apache Hadoop HDInsight Windows rendszerű SZÁMÍTÓGÉPRŐL](hdinsight-hadoop-windows-tools.md)
