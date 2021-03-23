---
title: A HDInsight-alapú Apache Kafka bemutatása – Azure
description: 'Ismerje meg a HDInsight-alapú Apache Kafkát: Mi ez, mire való, hol találhat rá példákat, és hol találhatja meg az első lépésekre vonatkozó információt?'
ms.service: hdinsight
ms.topic: overview
ms.custom: hdinsightactive
ms.date: 02/25/2020
ms.openlocfilehash: baf41b9e66b14e4b255b6e13b22691357f9e7309
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/23/2021
ms.locfileid: "104870806"
---
# <a name="what-is-apache-kafka-in-azure-hdinsight"></a>Mi az Azure HDInsight Apache Kafka

Az [Apache Kafka](https://kafka.apache.org) egy nyílt forráskódú elosztott streamelési platform streamadatfolyamatok és -alkalmazások létrehozásához. A Kafka az üzenetsorokhoz hasonló üzenetközvetítő funkciót is biztosít, amellyel adatstreameket tehet közzé, illetve feliratkozhat rájuk.

A HDInsight-alapú Kafka jellegzetességei:

* Ez egy felügyelt szolgáltatás, amely egyszerűsített konfigurációs folyamatot biztosít. Ennek eredménye egy, a Microsoft által tesztelt és támogatott konfiguráció.

* A Microsoft 99,9%-os szolgáltatói szerződést (SLA) nyújt a Kafka üzemidejével kapcsolatban. További információkért lásd [a HDInsight szolgáltatói szerződésével kapcsolatos információkat ismertető](https://azure.microsoft.com/support/legal/sla/hdinsight/v1_0/) dokumentumot.

* A Kafka az Azure Managed Disks szolgáltatást használja háttértárként. A Managed Disks akár 16 TB tárhelyet biztosíthat Kafka-közvetítőnként. További információ a felügyelt lemezek a HDInsight-beli Kafka-vel való konfigurálásáról: [Apache Kafka skálázhatóságának növelése a HDInsight-on](apache-kafka-scalability.md).

    További tudnivalók a felügyelt lemezekről: [Azure Managed Disks](../../virtual-machines/managed-disks-overview.md).

* A Kafkát egy egydimenziós állványnézettel tervezték. Az Azure az állványokat két dimenzióra osztja: a frissítési tartományokra (UD) és a tartalék tartományokra (FD). A Microsoft biztosítja azokat az eszközöket, amelyek kiegyensúlyozhatják a Kafka-partíciókat és -replikákat a frissítési és tartalék tartományok között.

    További információ: [magas rendelkezésre állás Apache Kafka on HDInsight](apache-kafka-high-availability.md).

* A HDInsight lehetővé teszi a feldolgozó csomópontok (amelyeken a Kafka-közvetítő fut) számának a fürt létrehozását követő módosítását. A Azure Portal, a Azure PowerShell és más Azure felügyeleti felületekből is elvégezhető a növekvő skálázás. A Kafka esetében érdemes kiegyenlíteni a partíciók replikáit a méretezési műveleteket követően. A partíciók kiegyenlítése lehetővé teszi, hogy a Kafka kihasználja a feldolgozó csomópontok új mennyiségét.

   A HDInsight Kafka nem támogatja a fürtön belüli közvetítők számának lefelé méretezését vagy csökkentését. Ha kísérlet történt a csomópontok számának csökkentésére, `InvalidKafkaScaleDownRequestErrorCode` hibaüzenetet ad vissza.

    További információ: [magas rendelkezésre állás Apache Kafka on HDInsight](apache-kafka-high-availability.md).

* Azure Monitor naplók használatával figyelheti a Kafka-t a HDInsight. Azure Monitor naplózza a virtuális gépekre vonatkozó információkat, például a lemez-és NIC-metrikákat, valamint a Kafka JMX metrikáit.

    További információ: [a naplók elemzése Apache Kafka a HDInsight](apache-kafka-log-analytics-operations-management.md).

## <a name="apache-kafka-on-hdinsight-architecture"></a>Apache Kafka a HDInsight architektúrán

Az alábbi ábra egy tipikus Kafka-konfigurációt mutat be, amely felhasználói csoportok, particionálás és replikálás használatával biztosítja az események párhuzamos olvasását hibatűréssel:

:::image type="content" source="./media/apache-kafka-introduction/kafka-cluster-diagram.png" alt-text="Kafka-fürtkonfigurációs ábra" border="false":::

A Kafka-fürt állapotát az Apache ZooKeeper felügyeli. A ZooKeeper egyidejű, rugalmas és kis késleltetésű tranzakciókhoz készült.

A Kafka a rekordokat (adatokat) **témakörökben** tárolja. A rekordokat **előállítók** hozzák létre, és **fogyasztók** használják fel. Az előállítók Kafka-**közvetítőknek** adják tovább a rekordokat. A HDInsight-fürt mindegyik feldolgozó csomópontja egy Kafka-közvetítő.

A témakörök particionálják a rekordokat a közvetítők között. A rekordok felhasználásakor partíciónként legfeljebb egy fogyasztó használható, az adatok párhuzamos feldolgozása érdekében.

A partíciók csomópontok közötti duplikálásához a rendszer replikációt alkalmaz, amely védelmet nyújt a csomópontok (közvetítők) leállásával szemben. Az ábrán *(L)* jelöléssel rendelkező partíció az adott partíció vezetője. Az előállítói forgalmat a csomópontok vezetőjéhez irányítja a rendszer a ZooKeeper által kezelt állapot segítségével.

## <a name="why-use-apache-kafka-on-hdinsight"></a>Miért érdemes Apache Kafka a HDInsight?

Néhány gyakori feladat és minta, amelyek a HDInsight-alapú Kafkával elvégezhetők:

|Használat |Leírás |
|---|---|
|Apache Kafka-adatmennyiség replikálása|A Kafka biztosítja a MirrorMaker segédprogramot, amely a Kafka-fürtök között replikálja az adatgyűjtést. További információ a MirrorMaker használatáról: [Apache Kafka témakörök replikálása Apache Kafka a HDInsight](apache-kafka-mirroring.md).|
|Közzététel – előfizetés üzenetkezelési mintája|A Kafka egy termelői API-t biztosít a rekordok egy Kafka-témakörbe való közzétételéhez. A fogyasztói API-ra a témakörökre való feliratkozáskor van szükség. További információ: [Start with apache Kafka on HDInsight](apache-kafka-get-started.md).|
|Stream-feldolgozás|A Kafka használatát gyakran használják Apache Storm vagy Spark a valós idejű adatfolyam-feldolgozáshoz. A Kafka 0.10.0.0 (HDInsight 3.5-ös és 3.6-os verzió) egy olyan streamelési API-t vezetett be, amely lehetővé teszi a streammegoldások Storm vagy Spark nélküli fejlesztését. További információ: [Start with apache Kafka on HDInsight](apache-kafka-get-started.md).|
|Horizontális skálázhatóság|A Kafka partíciók a HDInsight-fürt csomópontjain találhatóak. A fogyasztói folyamatok társíthatók az egyes partíciókkal, így biztosítható a terheléselosztás a rekordok használatakor. További információ: [Start with apache Kafka on HDInsight](apache-kafka-get-started.md).|
|Sorrendben történő kézbesítés|Minden partíción belül a rendszer a rekordokat a kapott sorrendben tárolja a streamben. Partíciónként egy fogyasztói folyamat társításával garantálhatja, hogy a rekordok feldolgozása érkezési sorrendben történjen. További információ: [Start with apache Kafka on HDInsight](apache-kafka-get-started.md).|
|Üzenetkezelés|Mivel támogatja a közzétételi-előfizetési üzenet mintáját, a Kafka-t gyakran használják a Message brokerként.|
|Tevékenységek nyomon követése|Mivel a Kafka a rekordok sorrendbe rendezését biztosítja, felhasználhatja a tevékenységek nyomon követését és újbóli létrehozását. Ilyen tevékenységek például a felhasználók műveletei egy webhelyen vagy egy alkalmazásban.|
|Összesítés|Az adatfolyam-feldolgozás használatával a különböző streamekről származó információk összevonhatók az adatoknak az operatív adatokba való összevonásához és központosításához.|
|Átalakítás|Az adatfolyam-feldolgozás használatával több bemeneti témakörből származó adatok egyesíthetők és bővíthetők egy vagy több kimeneti témakörbe.|

## <a name="next-steps"></a>Következő lépések

A HDInsighton futó Apache Kafka használatának megismeréséhez tekintse meg a következő hivatkozásokat:

* [Rövid útmutató: Apache Kafka létrehozása a HDInsight-on](apache-kafka-get-started.md)

* [A Kafka és a REST proxy használata](rest-proxy.md)

* [Oktatóanyag: Apache Spark használata a HDInsight Apache Kafka használatával](../hdinsight-apache-spark-with-kafka.md)

* [Oktatóanyag: Az Apache Storm használata az Apache Kafka on HDInsighttal](../hdinsight-apache-storm-with-kafka.md)
