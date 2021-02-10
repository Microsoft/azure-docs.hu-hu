---
title: Összetevők és verziók Apache Hadoop – Azure HDInsight
description: Ismerje meg az Azure HDInsight Apache Hadoop összetevőit és verzióit.
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive,hdiseo17may2017,seoapr2020
ms.date: 08/13/2020
ms.openlocfilehash: d1f4c57986f356f267448ff43de23973534b8126
ms.sourcegitcommit: 49ea056bbb5957b5443f035d28c1d8f84f5a407b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/09/2021
ms.locfileid: "100006666"
---
# <a name="apache-components-and-versions-available-for-azure-hdinsight"></a>Az Azure HDInsight elérhető Apache-összetevők és-verziók

Ebben a cikkben megismerheti az Azure HDInsight és a Enterprise Security Package [Apache Hadoop](https://hadoop.apache.org/) környezeti összetevőit és verzióit. Azt is megtudhatja, hogyan tekintheti meg a Hadoop összetevő-verzióit a HDInsight-ben.

## <a name="apache-components-available-with-different-hdinsight-versions"></a>Különböző HDInsight-verziókkal elérhető Apache-összetevők

Az Azure HDInsight több Hadoop-fürtöt is támogat, amelyek bármikor üzembe helyezhetők. 2017. április 4-én az Azure HDInsight által használt alapértelmezett fürt verziója 3,6 volt.

A HDInsight-fürtökhöz társított verziók az alábbi táblázatban láthatók.

> [!NOTE]
> Előfordulhat, hogy a HDInsight szolgáltatás alapértelmezett verziója értesítés nélkül változhat. Ha a verzió-függőséggel rendelkezik, adja meg a HDInsight verzióját, amikor létrehozza a fürtöket a .NET SDK-val a Azure PowerShell és a klasszikus Azure CLI használatával.

| Összetevő              | HDInsight 4.0 | HDInsight 3,6 (alapértelmezett)     |
|------------------------|---------------|-----------------------------|
| Apache Hadoop és fonal | 3.1.1         | 2.7.3                       |
| Apache TEZ             | 0.9.1         | 0.7.0                       |
| Apache Pig             | 0.16.1        | 0.16.0                      |
| Apache Hive            | 3.1.0         | 1.2.1 (2.1.0 az ESP interaktív lekérdezésen) |
| Apache TEZ Hive2       | -             | 0.8.4                       |
| Apache Ranger          | 1.1.0         | 0.7.0                       |
| Apache HBase           | 2.1.6         | 1.1.2                       |
| Apache Sqoop           | 1.4.7         | 1.4.6                       |
| Apache Oozie           | 4.3.1         | 4.2.0                       |
| Apache Zookeeper       | 3.4.6         | 3.4.6                       |
| Apache Storm           | -             | 1.1.0                       |
| Apache Mahout          | -             | 0.9.0 +                      |
| Apache Phoenix         | 5             | 4.7.0                       |
| Apache Spark           | 2.4.4         | 2.3.2.                      |
| Apache Livy            | 0,5           | 0,4.                        |
| Apache Kafka           | 2.1.1         | 1.1                         |
| Apache Ambari          | 2.7.0         | 2.6.0                       |
| Apache Zeppelin        | 0.8.0         | 0.7.3                       |
| Mono                   | 4.2.1         | 4.2.1                       |

> [!NOTE]
> HDInsight 4,0: a Spark 2,4 és a Kafka 2,1 teljes mértékben támogatott. Azonban a Spark 2,3 és a Kafka 1,1 típusú fürtök nincsenek kiszolgálva. HDInsight 3,6: a Spark 2,3 és a Kafka 1,1 teljes mértékben támogatott.  

## <a name="check-for-current-apache-component-version-information"></a>Az Apache összetevő aktuális verziójával kapcsolatos információk keresése

A HDInsight-Hadoop társított környezet-összetevők verzióit a HDInsight frissítéseivel lehet megváltoztatni. A Hadoop-összetevők ellenőrzéséhez és a fürthöz használt verziók ellenőrzéséhez használja a Ambari REST API. A **GetComponentInformation** parancs adatokat kér le a szolgáltatás összetevőiről. További információt az [Apache Ambari dokumentációjában](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md)talál.

### <a name="release-notes"></a>Kibocsátási megjegyzések

További kibocsátási megjegyzések a HDInsight legújabb verzióiban: [HDInsight kibocsátási megjegyzések](hdinsight-release-notes.md).

## <a name="supported-hdinsight-versions"></a>Támogatott HDInsight-verziók

### <a name="support-expiration-and-retirement-for-hdinsight-versions"></a>A HDInsight-verziók lejáratának és kivonulásának támogatása

A **támogatás lejárata** azt jelenti, hogy a Microsoft már nem nyújt támogatást az adott HDInsight-verzióhoz. És már nem érhető el a Azure Portal a fürt létrehozásához. Ezek a verziók az Azure CLI-vel vagy a különböző SDK-k használatával is létrehozhatók.

A **nyugdíjazás** azt jelenti, hogy egy HDInsight-verzió meglévő fürtjének futtatása továbbra is a következő:. Ezen verzió új fürtök nem hozhatók létre semmilyen módon, beleértve a CLI-t és az SDK-kat is. A vezérlési sík egyéb funkciói, például a manuális skálázás és az automatikus skálázás is előfordulhat, hogy a nyugdíjazás után nem működnek. A támogatás nem érhető el a kivont verziókhoz.

A következő táblázat a HDInsight verzióját sorolja fel. A támogatási lejárat és a nyugdíjazás dátuma is megadható, ha ismertek.

### <a name="available-versions"></a>Elérhető verziók

Ez a táblázat felsorolja a HDInsight azon verzióit, amelyek elérhetők a Azure Portalban, valamint más üzembe helyezési módszerekkel, például a PowerShell és a .NET SDK használatával.

| HDInsight verziója | VM OS | Kiadási dátum | Támogatás lejárati dátuma | Nyugdíjazás dátuma | Magas rendelkezésre állás |  Rendelkezésre állás a Azure Portal |
| --- | --- | --- | --- | --- | --- | --- |
| HDInsight 4.0 |Ubuntu 16.0.4 LTS |24, 2018. szeptember | | |Igen |Igen |
| HDInsight 3,6 |Ubuntu 16.0.4 LTS |2017. április 4.      | * Június 30., 2021 |Június 30., 2021 |Igen |Igen |

* Kiterjesztjük bizonyos HDInsight 3,6-fürtök támogatási időkeretét

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

> [!NOTE]
> Ha a fürt verziójának támogatása lejár, előfordulhat, hogy a Azure Portal nem érhető el. Bizonyos esetekben a fürtcsomópontok továbbra is elérhetők a Windows PowerShell [New-AzHDInsightCluster](/powershell/module/az.hdinsight/new-azhdinsightcluster) parancsában és a .net SDK-ban lévő **Version** paraméterrel, amíg meg nem történik a verzió kivonulási dátuma.

### <a name="retired-versions"></a>Kivont verziók

Ez a táblázat felsorolja azokat a HDInsight-verziókat, amelyek nem érhetők el a Azure Portalban.

| HDInsight verziója | HDP verziója | VM OS | Kiadási dátum | Támogatás lejárati dátuma | Nyugdíjazás dátuma | Magas rendelkezésre állás |  Rendelkezésre állás a Azure Portal |
| --- | --- | --- | --- | --- | --- | --- | --- |
| HDInsight 3,5 |HDP 2,5 |Ubuntu 16.0.4 LTS |Szeptember 30., 2016 |Szeptember 5., 2017 |Június 28., 2018 |Igen |Nem |
| HDInsight 3,4 |HDP 2,4 |Ubuntu 14.0.4 LTS |Március 29., 2016 |December 29., 2016 |2018. január 9. |Igen |Nem |
| HDInsight 3,3 |HDP 2,3 |Windows Server 2012 R2 |2015. december 2. |2016. június 27. |2018. július 31. |Igen |Nem |
| HDInsight 3,3 |HDP 2,3 |Ubuntu 14.0.4 LTS |2015. december 2. |2016. június 27. |2017. július 31. |Igen |Nem |
| HDInsight 3,2 |HDP 2,2 |Ubuntu 12,04 LTS vagy Windows Server 2012 R2 |Február 18., 2015 |2016. március 1. |2017. április 1. |Igen |Nem |
| HDInsight 3,1 |HDP 2,1 |Windows Server 2012 R2 |Június 24., 2014 |2015. május 18. |Június 30., 2016 |Igen |Nem |
| HDInsight 3,0 |HDP 2,0 |Windows Server 2012 R2 |Február 11., 2014 |Szeptember 17., 2014 |2015. június 30. |Igen |Nem |
| HDInsight 2,1 |HDP 1,3 |Windows Server 2012 R2 |Október 28., 2013 |2014. május 12. |2015. május 31. |Igen |Nem |
| HDInsight 1,6 |HDP 1,1 | |Október 28., 2013 |Április 26., 2014 |2015. május 31. |Nem |Nem |

> [!NOTE]
> A HDInsight 2,1-es és újabb verzióiban a két fő csomóponttal rendelkező, magasan elérhető fürtök üzembe helyezése alapértelmezés szerint megtörténik. Nem érhetők el a 1,6-es verziójú HDInsight-fürtökhöz.

## <a name="service-level-agreement-for-hdinsight-cluster-versions"></a>A HDInsight-fürt verzióira vonatkozó szolgáltatási szintű szerződés

A szolgáltatói szerződés _támogatási ablakként_ van meghatározva. A támogatási időszak azt az időtartamot használja, ameddig a Microsoft ügyfélszolgálata és támogatása támogatja a HDInsight verzióját. Ha a verzió elérte a _támogatás lejárati dátumát_, a HDInsight-fürt a támogatási ablakon kívül esik. A HDInsight X verziójának támogatása (újabb X + 1 verzió után) a következő:

- **1. képlet:** Adja hozzá a 180 napot a HDInsight-fürt X verziójának kiadási dátumához.
- **2. képlet:** Adja hozzá a 90 nappal azt a dátumot, amikor a HDInsight-fürt X + 1 verziója elérhetővé válik a Azure Portal.

A lejárati _dátum_ az a dátum, amely után a fürt verziója nem hozható létre a HDInsight. 2017. július 31-ig nem lehet átméretezni egy HDInsight-fürtöt a kivonulási dátum után.

## <a name="default-node-configuration-and-virtual-machine-sizes-for-clusters"></a>A csomópontok alapértelmezett konfigurációja és a virtuális gépek méretei a fürtökhöz

További információ arról, hogy mely virtuálisgép-SKU-ket szeretné kijelölni a fürt számára: az [Azure HDInsight-fürt konfigurációjának részletei](hdinsight-supported-node-configuration.md).

## <a name="next-steps"></a>Következő lépések

- [Fürt beállítása Apache Hadoop, Spark és más HDInsight](hdinsight-hadoop-provision-linux-clusters.md)
- [Enterprise Security Package](./enterprise-security-package.md)
- [Az Azure HDInsight-verziókhoz tartozó Hortonworks kibocsátási megjegyzések](./hortonworks-release-notes.md)
- [Munkahelyi Apache Hadoop HDInsight Windows rendszerű SZÁMÍTÓGÉPRŐL](hdinsight-hadoop-windows-tools.md)


