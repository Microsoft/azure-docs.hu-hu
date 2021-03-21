---
title: Apache Kafka számítási feladatok migrálása az Azure HDInsight 4,0
description: Megtudhatja, hogyan telepítheti át Apache Kafka számítási feladatait a 3,6-es HDInsight a HDInsight 4,0-ra.
ms.service: hdinsight
ms.topic: conceptual
ms.date: 12/18/2019
ms.openlocfilehash: 3967a5d96c35e4bac88dcd9a6c1fa95b78a6b2b1
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98939110"
---
# <a name="migrate-apache-kafka-workloads-to-azure-hdinsight-40"></a>Apache Kafka számítási feladatok migrálása az Azure HDInsight 4,0

Az Azure HDInsight 4,0 a legújabb nyílt forráskódú összetevőket kínálja a teljesítmény, a kapcsolat és a biztonság jelentős fejlesztésével. Ez a dokumentum ismerteti, hogyan telepítheti át Apache Kafka számítási feladatait a 3,6-HDInsight a HDInsight 4,0-es verzióra. A számítási feladatok HDInsight 4,0-re való áttelepítését követően számos olyan új funkciót is használhat, amelyek nem érhetők el a HDInsight 3,6-es verziójában.

## <a name="hdinsight-36-kafka-migration-paths"></a>HDInsight 3,6 Kafka-áttelepítési útvonalak

A HDInsight 3,6 a Kafka két verzióját támogatja: 1.0.0 és 1.1.0. A HDInsight 4,0 a 1.1.0 és a 2.1.0 verziót támogatja. Attól függően, hogy a Kafka melyik verzióját és a HDInsight melyik verzióját szeretné futtatni, több támogatott áttelepítési útvonal is létezik. Ezeket az elérési utakat alább ismertetjük, és az alábbi ábrán látható.

* **A Kafka és a HDInsight futtatása a legújabb verziókon (ajánlott)**: telepítse át a HDInsight 3,6-es és a Kafka 1.0.0-vagy 1.1.0-alkalmazást a HDInsight 4,0-re a Kafka-2.1.0 (a D és az E útvonalon).
* **Futtassa a HDInsight a legújabb verzión, de Kafka-t csak egy újabb verzióban**: telepítse át a HDInsight 3,6 és a Kafka 1.0.0 alkalmazást a HDInsight 4,0-re a Kafka 1.1.0-vel (a lenti útvonalon).
* **HDInsight futtatása a legújabb verzióban, a Kafka verziójának megőrzése**: HDInsight 3,6-es és Kafka 1.1.0-alkalmazás migrálása a HDInsight 4,0-be a Kafka 1.1.0-vel (a lenti elérési úttal).
* A **Kafka futtatása újabb verzióban, a HDInsight verziójának megőrzése**: Kafka 1.0.0-alkalmazás migrálása a 1.1.0-re, és maradjon a HDInsight 3,6 (az alábbi útvonalon). Vegye figyelembe, hogy ez a beállítás továbbra is új fürt üzembe helyezését igényli. A Kafka verziójának meglévő fürtön való frissítése nem támogatott. Miután létrehozta a kívánt verziót tartalmazó fürtöt, telepítse át a Kafka-ügyfeleket az új fürt használatára.

![A 3,6-es Apache Kafka frissítési útvonalai](./media/upgrade-threesix-to-four/apache-kafka-upgrade-path.png)

## <a name="apache-kafka-versions"></a>Apache Kafka verziók

### <a name="kafka-110"></a>Kafka 1.1.0
  
Ha a Kafka 1.0.0-ról a 1.1.0-re telepít át, akkor a következő új funkciók előnyeit veheti igénybe:

* A Kafka-vezérlő javította a vezérelt leállítást, így a brókerek újraindíthatók, és gyorsabban helyreállíthatók a problémák. 
* A [FetchRequests logikájának](https://issues.apache.org/jira/browse/KAFKA-6254) fejlesztése, amely lehetővé teszi, hogy több partíciót (és így több témakört) is kibővítse a fürtön. 
* A Kafka-kapcsolat támogatja a témakörök [fejléceit](https://issues.apache.org/jira/browse/KAFKA-5142) és [reguláris kifejezéseit](https://issues.apache.org/jira/browse/KAFKA-3073) . 

A frissítések teljes listáját a [Apache Kafka 1,1 kibocsátási megjegyzései](https://archive.apache.org/dist/kafka/1.1.0/RELEASE_NOTES.html)című témakörben tekintheti meg.

### <a name="apache-kafka-210"></a>Apache Kafka 2.1.0

Ha a Kafka 2,1-re telepít át, a következő funkciók közül választhat:

* Jobb közvetítői rugalmasság a továbbfejlesztett replikációs protokoll miatt.
* Új funkciók a KafkaAdminClient API-ban.
* Konfigurálható kvóta-kezelés.
* Zstandard-tömörítés támogatása.

A frissítések teljes listáját a [Apache Kafka 2,0 kibocsátási megjegyzései](https://archive.apache.org/dist/kafka/2.0.0/RELEASE_NOTES.html) és [Apache Kafka 2,1 kibocsátási megjegyzései](https://archive.apache.org/dist/kafka/2.1.0/RELEASE_NOTES.html)című témakörben tekintheti meg.

## <a name="kafka-client-compatibility"></a>Kafka-ügyfél kompatibilitása

Az új Kafka-közvetítők támogatják a régebbi ügyfeleket. A [KIP-35 – a protokoll verziójának lekérése](https://cwiki.apache.org/confluence/display/KAFKA/KIP-35+-+Retrieving+protocol+version) bevezetett egy mechanizmust a Kafka-közvetítő és a KIP-97 funkciójának dinamikus meghatározásához [: a továbbfejlesztett KAFKA-ügyfél RPC-kompatibilitási szabályzata](https://cwiki.apache.org/confluence/display/KAFKA/KIP-97%3A+Improved+Kafka+Client+RPC+Compatibility+Policy) új kompatibilitási szabályzatot és garanciákat vezetett be a Java-ügyfél számára. Korábban egy Kafka-ügyfélnek ugyanazzal a verzióval vagy újabb verzióval kellett kommunikálnia. Mostantól a Java-ügyfelek és más, a KIP-35-et támogató ügyfelek újabb verziói visszatérhetnek a `librdkafka` régebbi típusú kérelmekhez, vagy elhelyezhetik a megfelelő hibákat, ha a funkció nem érhető el.

![A Kafka-ügyfél kompatibilitásának frissítése](./media/upgrade-threesix-to-four/apache-kafka-client-compatibility.png)

Ne feledje, hogy az ügyfél támogatja a régebbi közvetítőket.  További információ: [kompatibilitási mátrix](https://cwiki.apache.org/confluence/display/KAFKA/Compatibility+Matrix).

## <a name="general-migration-process"></a>Általános áttelepítési folyamat

A következő áttelepítési útmutató feltételezi, hogy egy virtuális hálózatban egy HDInsight 3,6-es verzióban telepített Apache Kafka 1.0.0 vagy 1.1.0-fürt. A meglévő közvetítőnek van néhány témája, és aktívan használják a gyártók és a fogyasztók.

![Aktuális Kafka feltételezett környezet](./media/upgrade-threesix-to-four/apache-kafka-presumed-environment.png)

Az áttelepítés befejezéséhez hajtsa végre a következő lépéseket:

1. **Helyezzen üzembe egy új HDInsight 4,0-fürtöt és-ügyfelet a teszteléshez.** Helyezzen üzembe egy új HDInsight 4,0 Kafka-fürtöt. Ha több Kafka-fürt is kiválasztható, a legújabb verziót ajánlott kiválasztani. Az üzembe helyezés után szükség szerint állítsa be a paramétereket, és hozzon létre egy, a meglévő környezettel megegyező nevű témakört. Igény szerint állítsa be a TLS-t és a saját kulcsú (BYOK) titkosítást is. Ezután ellenőrizze, hogy megfelelően működik-e az új fürttel.

    ![Új HDInsight 4,0-fürtök üzembe helyezése](./media/upgrade-threesix-to-four/deploy-new-hdinsight-clusters.png)

1. **Állítsa át a fürtöt a gyártó alkalmazás számára, és várjon, amíg a jelenlegi fogyasztók az összes üzenetsor-adatmennyiséget felhasználják.** Ha az új HDInsight 4,0 Kafka-fürt készen áll, váltson át a meglévő gyártó célhelyére az új fürtre. Hagyja azt addig, amíg a meglévő fogyasztói alkalmazás a meglévő fürt összes adatait felhasználta.

    ![Fürt váltása a producer alkalmazáshoz](./media/upgrade-threesix-to-four/switch-cluster-producer-app.png)

1. **Állítsa át a fürtöt a fogyasztói alkalmazásban.** Miután megerősítette, hogy a meglévő fogyasztói alkalmazás befejezte a meglévő fürt összes adatainak felhasználását, váltson át az új fürthöz.

    ![Fürt váltása a fogyasztói alkalmazásban](./media/upgrade-threesix-to-four/switch-cluster-consumer-app.png)

1. **Szükség szerint távolítsa el a régi fürtöt, és tesztelje az alkalmazásokat.** Ha a kapcsoló elkészült, és megfelelően működik, távolítsa el a régi HDInsight 3,6 Kafka-fürtöt, valamint a tesztben használt gyártókat és fogyasztókat igény szerint.

## <a name="next-steps"></a>Következő lépések

* [Apache Kafka HDInsight-fürtök teljesítményoptimalizálása](apache-kafka-performance-tuning.md)
* [Rövid útmutató: Apache Kafka-fürt létrehozása az Azure HDInsight Azure Portal használatával](apache-kafka-get-started.md)
