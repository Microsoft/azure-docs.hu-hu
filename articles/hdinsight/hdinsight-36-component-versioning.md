---
title: Apache Hadoop összetevők és verziók – Azure HDInsight 3,6
description: Ismerkedjen meg az Azure HDInsight 3,6-es verziójának Apache Hadoop összetevőivel és verzióival.
ms.service: hdinsight
ms.topic: conceptual
author: deshriva
ms.author: deshriva
ms.date: 02/08/2021
ms.openlocfilehash: 0180f94109d01443390da363d9e09c5ad0b26d18
ms.sourcegitcommit: 4bda786435578ec7d6d94c72ca8642ce47ac628a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/16/2021
ms.locfileid: "103496116"
---
# <a name="hdinsight-36-component-versions"></a>HDInsight 3,6 összetevő-verziók

Ebben a cikkben megismerheti a Apache Hadoop környezeti összetevőit és verzióit az Azure HDInsight 3,6-ben.

## <a name="support-for-hdinsight-36"></a>A HDInsight 3,6 támogatása

Az alábbi táblázat felsorolja a HDInsight 3,6-fürtök támogatási időkeretét.

| Fürt típusa                    | Keretrendszer verziója | Jelenlegi támogatás lejárata        | Új támogatás lejárati dátuma |
|---------------------------------|-------------------|-----------------------------------|-----------------------------|
| HDInsight 3,6 Hadoop            | 2.7.3             | Dec. 31.2020                      | Június 30., 2021               |
| HDInsight 3,6 Spark             | 2.3               | Dec. 31.2020                      | Június 30., 2021               |
| HDInsight 3,6 Spark             | 2,2               | Kivonulás június 30-ig, 2020          |                             |
| HDInsight 3,6 Spark             | 2.1               | Kivonulás június 30-ig, 2020          |                             |
| HDInsight 3,6 Kafka             | 1.1               | Dec. 31.2020                      | Június 30., 2021               |
| HDInsight 3,6 Kafka             | 1.0               | 2020. június 30-án megszűnt.         |                             |
| HDInsight 3,6 HBase             | 1.1               | Dec. 31.2020                      | Június 30., 2021               |
| HDInsight 3,6 interaktív lekérdezés | 2.1               | Dec. 31.2020                      | Június 30., 2021               |
| HDInsight 3,6 Storm             | 1.1               | Dec. 31.2020                      | Június 30., 2021               |
| HDInsight 3,6 ML-szolgáltatások      | 9,3               | Dec. 31.2020                      | Dec. 31.2020                |
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