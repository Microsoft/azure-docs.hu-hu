---
title: Streaming Apache Spark Apache Kafka-Azure HDInsight
description: Megtudhatja, hogyan használhatja a Apache Sparkt az adatátvitelhez vagy a Apache Kafka DStreams használatával történő továbbításához. Ebben a példában az adatok továbbítása a Spark on HDInsight Jupyter Notebook használatával végezhető el.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 11/21/2019
ms.openlocfilehash: d14b96843b489b28fc7d83348e39638272c06da5
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98942756"
---
# <a name="apache-spark-streaming-dstream-example-with-apache-kafka-on-hdinsight"></a>Apache Spark streaming (DStream) példa a Apache Kafka on HDInsight

Megtudhatja, hogyan használhatja a [Apache Spark](https://spark.apache.org/) -t a HDInsight-be való adatátvitelhez vagy a [Apache Kafka](https://kafka.apache.org/) a [DStreams](https://spark.apache.org/docs/latest/api/java/org/apache/spark/streaming/dstream/DStream.html)használatával történő továbbításához. Ez a példa a Spark-fürtön futó [Jupyter Notebookt](https://jupyter.org/) használ.

> [!NOTE]  
> A dokumentum lépései olyan Azure-erőforráscsoportot hoznak létre, amely Spark on HDInsight- és Kafka on HDInsight-fürtöt is tartalmaz. Mindkét fürt Azure virtuális hálózatban található, így a Spark-fürt közvetlenül kommunikálhat a Kafka-fürttel.
>
> Amikor végzett a dokumentum lépéseivel, ne felejtse el törölni a fürtöket a további díjak elkerülése érdekében.

> [!IMPORTANT]  
> Ez a példa az DStreams-t használja, amely egy régebbi Spark streaming-technológia. Az újabb Spark streaming-funkciókat használó például a [Spark strukturált streaming Apache Kafka](hdinsight-apache-kafka-spark-structured-streaming.md) dokumentummal című részében talál további információt.

## <a name="create-the-clusters"></a>A fürtök létrehozása

A HDInsight Apache Kafka nem biztosít hozzáférést a Kafka-közvetítők számára a nyilvános interneten keresztül. A Kafka-vel megbeszélt mindennek ugyanabban az Azure-beli virtuális hálózatban kell lennie, mint a Kafka-fürt csomópontjain. Ebben a példában a Kafka és a Spark-fürtök egy Azure-beli virtuális hálózaton találhatók. Az alábbi ábrán a fürtök közötti kommunikáció látható:

![Azure virtuális hálózatban lévő Spark- és Kafka-fürtök ábrája](./media/hdinsight-apache-spark-with-kafka/apache-spark-kafka-vnet.png)

> [!NOTE]  
> Bár a Kafka maga a virtuális hálózaton belüli kommunikációra korlátozódik, a fürt egyéb szolgáltatásai, például az SSH és a Ambari is elérhetők az interneten keresztül. További információ a HDInsighttal elérhető nyilvános portokról: [A HDInsight által használt portok és URI-k](hdinsight-hadoop-port-settings-for-services.md).

Habár az Azure Virtual Network, a Kafka és a Spark-fürtök manuálisan is létrehozhatók, könnyebben Azure Resource Manager sablont használni. Az alábbi lépéseket követve üzembe helyezhet egy Azure-beli virtuális hálózatot, Kafka-t és Spark-fürtöt az Azure-előfizetésében.

1. Az alábbi gombbal jelentkezzen be az Azure szolgáltatásba, és nyissa meg a sablont az Azure Portalon.

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Farmtemplates%2Fcreate-linux-based-kafka-spark-cluster-in-vnet-v4.1.json" target="_blank"><img src="./media/hdinsight-apache-spark-with-kafka/hdi-deploy-to-azure1.png" alt="Deploy to Azure button for new cluster"></a>

    A Azure Resource Manager sablon a következő helyen található: **https://hditutorialdata.blob.core.windows.net/armtemplates/create-linux-based-kafka-spark-cluster-in-vnet-v4.1.json** .

    > [!WARNING]  
    > A HDInsightban futó Kafka platform rendelkezésre állásának biztosításához fürtjének legalább három feldolgozó csomópontot kell tartalmaznia. A sablon egy három feldolgozó csomópontot tartalmazó Kafka-fürtöt hoz létre.

    Ez a sablon egy HDInsight 3,6-fürtöt hoz létre a Kafka és a Spark számára egyaránt.

1. Az alábbi információk segítségével töltheti fel a bejegyzéseket az **Egyéni telepítés** szakaszban:

    |Tulajdonság |Érték |
    |---|---|
    |Erőforráscsoport|Hozzon létre egy csoportot, vagy válasszon ki egy meglévőt.|
    |Hely|Válasszon ki egy földrajzilag közel lévő helyet.|
    |Alap fürt neve|A rendszer ezt az értéket használja a Spark-és Kafka-fürtök alapneveként. A **hdistreaming** megadása például egy __Spark-hdistreaming__ nevű Spark-fürtöt és egy **Kafka-hdistreaming** nevű Kafka-fürtöt hoz létre.|
    |Fürt bejelentkezési felhasználóneve|A Spark és a Kafka fürtök rendszergazdai felhasználóneve.|
    |Fürt bejelentkezési jelszava|A Spark-és Kafka-fürtök rendszergazdai felhasználói jelszava.|
    |SSH-felhasználónév|A Spark-és Kafka-fürtökhöz létrehozandó SSH-felhasználó.|
    |SSH-jelszó|A Spark-és Kafka-fürtök SSH-felhasználójának jelszava.|

    ![HDInsight egyéni telepítési paraméterek](./media/hdinsight-apache-spark-with-kafka/hdinsight-parameters.png)

1. Olvassa át a **használati feltételeket**, majd válassza az **Elfogadom a fenti feltételeket és kikötéseket** lehetőséget.

1. Végül válassza a **vásárlás** lehetőséget. A fürtök létrehozása körülbelül 20 percet vesz igénybe.

Az erőforrások létrehozása után megjelenik egy összefoglaló lap.

![Erőforráscsoport-összefoglalás a vnet és a fürtökhöz](./media/hdinsight-apache-spark-with-kafka/hdinsight-group-blade.png)

> [!IMPORTANT]  
> Figyelje meg, hogy a HDInsight-fürtök neve **Spark-BASENAME** és **Kafka-BASENAME**, ahol a BASENAME a sablonban megadott név. Ezeket a neveket a fürtökhöz való csatlakozáskor a későbbi lépésekben használhatja.

## <a name="use-the-notebooks"></a>Jegyzetfüzetek használata

A dokumentumban ismertetett példa kódja a következő címen érhető el: [https://github.com/Azure-Samples/hdinsight-spark-scala-kafka](https://github.com/Azure-Samples/hdinsight-spark-scala-kafka) .

## <a name="delete-the-cluster"></a>A fürt törlése

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

Mivel a jelen dokumentumban szereplő lépések mindkét fürtöt létrehozzák ugyanabban az Azure-erőforráscsoportban, törölheti az erőforráscsoportot a Azure Portal. A csoport törlése eltávolítja az ezt a dokumentumot, a fürt által használt Azure Virtual Network és Storage-fiókot tartalmazó összes erőforrást.

## <a name="next-steps"></a>Következő lépések

Ebben a példában megtanulta, hogyan használhatja a Sparkot a Kafka olvasására és írására. Az alábbi hivatkozásokat követve megismerheti a Kafka használatának egyéb módjait:

* [Ismerkedés a HDInsight Apache Kafkaával](kafka/apache-kafka-get-started.md)
* [Apache Kafka replikájának létrehozása a MirrorMaker használatával a HDInsight](kafka/apache-kafka-mirroring.md)
* [Apache Storm használata a HDInsight Apache Kafka használatával](hdinsight-apache-storm-with-kafka.md)
