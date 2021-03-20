---
title: Események feldolgozása Event Hubsról a Storm-Azure HDInsight
description: Ismerje meg, hogyan dolgozhatja fel az Azure Event Hubs adatait a Visual Studióban létrehozott C# Storm-topológiával, a Visual studióhoz készült HDInsight Tools használatával.
ms.service: hdinsight
ms.topic: how-to
ms.date: 01/14/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: 4393c6797f5a164a063b55f8994d7d37d278f3c4
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98929185"
---
# <a name="process-events-from-azure-event-hubs-with-apache-storm-on-hdinsight-c"></a>Események feldolgozása az Azure Event Hubs és az Apache Storm on HDInsight (C#) használatával

Megtudhatja, hogyan dolgozhat az Azure Event Hubs [Apache Storm](https://storm.apache.org/) a HDInsight-on. Ez a dokumentum C# Storm-topológiát használ az adatok olvasására és írására Event Hubs

> [!NOTE]  
> A projekt Java-verziója: [események feldolgozása az Azure Event Hubs és az Apache Storm on HDInsight (Java)](https://github.com/Azure-Samples/hdinsight-java-storm-eventhub).

## <a name="scpnet"></a>SCP.NET

A jelen dokumentumban ismertetett lépések a SCP.NET, egy NuGet-csomagot használnak, amely megkönnyíti a C#-topológiák és-összetevők létrehozását a HDInsight-alapú Storm használatával.

A HDInsight 3,4 és a Mono használata a C#-topológiák futtatására használható. A dokumentumban használt példa a HDInsight 3,6-es verzióval működik. Ha saját .NET-megoldásokat kíván létrehozni a HDInsight-hez, tekintse meg a [mono kompatibilitási](https://www.mono-project.com/docs/about-mono/compatibility/) dokumentumot az esetleges inkompatibilitás érdekében.

### <a name="cluster-versioning"></a>Fürt verziószámozása

A projekthez használt Microsoft. SCP. net. SDK NuGet-csomagnak meg kell egyeznie a HDInsight-on telepített Storm fő verziójával. A HDInsight 3,5-es és 3,6-es verziója a Storm 1. x-es verzióját használja, ezért ezeket a fürtöket az 1.0. x. x SCP.NET kell használnia.

A C#-topológiák a .NET 4,5-es megcélzására is alkalmasak.

## <a name="how-to-work-with-event-hubs"></a>A Event Hubs használata

A Microsoft olyan Java-összetevőket biztosít, amelyek használatával a Storm-topológiából Event Hubs lehet kommunikálni. Az ezen összetevők HDInsight 3,6 kompatibilis verzióját tartalmazó Java Archive-(JAR-) fájl megtalálható a következő helyen: [https://github.com/hdinsight/mvn-repo/raw/master/org/apache/storm/storm-eventhubs/1.1.0.1/storm-eventhubs-1.1.0.1.jar](https://github.com/hdinsight/mvn-repo/raw/master/org/apache/storm/storm-eventhubs/1.1.0.1/storm-eventhubs-1.1.0.1.jar) .

> [!IMPORTANT]  
> Az összetevők Java nyelven íródnak, így a C#-topológiából egyszerűen használhatók.

Ebben a példában a következő összetevőket használjuk:

* __EventHubSpout__: adatokat olvas Event Hubsból.
* __EventHubBolt__: adatot ír Event Hubsba.
* __EventHubSpoutConfig__: a EventHubSpout konfigurálására szolgál.
* __EventHubBoltConfig__: a EventHubBolt konfigurálására szolgál.

### <a name="example-spout-usage"></a>Példa a kiöntő használatra

A SCP.NET metódusokat biztosít a EventHubSpout a topológiához való hozzáadásához. Ezek a módszerek megkönnyítik a kiöntő hozzáadását, mint a Java-összetevők hozzáadására szolgáló általános metódusok használatát. Az alábbi példa bemutatja, hogyan hozhat létre kiöntőt a SCP.NET által biztosított __SetEventHubSpout__ és **EventHubSpoutConfig** metódusok használatával:

```csharp
 topologyBuilder.SetEventHubSpout(
    "EventHubSpout",
    new EventHubSpoutConfig(
        ConfigurationManager.AppSettings["EventHubSharedAccessKeyName"],
        ConfigurationManager.AppSettings["EventHubSharedAccessKey"],
        ConfigurationManager.AppSettings["EventHubNamespace"],
        ConfigurationManager.AppSettings["EventHubEntityPath"],
        eventHubPartitions),
    eventHubPartitions);
```

Az előző példa létrehoz egy __EventHubSpout__ nevű új kiöntő összetevőt, és úgy konfigurálja, hogy az egy Event hub használatával kommunikáljon. Az összetevő párhuzamossági mutatójának értéke az Event hub partícióinak száma. Ez a beállítás lehetővé teszi, hogy a Storm létrehozza az összetevő egy példányát az egyes partíciók esetében.

### <a name="example-bolt-usage"></a>Példa a bolt használatára

A **JavaComponmentConstructor** metódus használatával hozza létre a bolt egy példányát. Az alábbi példa bemutatja, hogyan hozhat létre és konfigurálhat egy új példányt a **EventHubBolt**:

```csharp
// Java construcvtor for the Event Hub Bolt
JavaComponentConstructor constructor = JavaComponentConstructor.CreateFromClojureExpr(
    String.Format(@"(org.apache.storm.eventhubs.bolt.EventHubBolt. (org.apache.storm.eventhubs.bolt.EventHubBoltConfig. " +
        @"""{0}"" ""{1}"" ""{2}"" ""{3}"" ""{4}"" {5}))",
        ConfigurationManager.AppSettings["EventHubPolicyName"],
        ConfigurationManager.AppSettings["EventHubPolicyKey"],
        ConfigurationManager.AppSettings["EventHubNamespace"],
        "servicebus.windows.net",
        ConfigurationManager.AppSettings["EventHubName"],
        "true"));

// Set the bolt to subscribe to data from the spout
topologyBuilder.SetJavaBolt(
    "eventhubbolt",
    constructor,
    partitionCount)
        .shuffleGrouping("Spout");
```

> [!NOTE]  
> Ez a példa egy karakterláncként átadott Clojure-kifejezést használ ahelyett, hogy a **JavaComponentConstructor** -t használja a **EventHubBoltConfig** létrehozásához, mert a kiöntő példa volt. Bármelyik módszer működik. Használja az Ön számára legmegfelelőbb módszert.

## <a name="download-the-completed-project"></a>A befejezett projekt letöltése

Az ebben a cikkben létrehozott projekt teljes verzióját letöltheti a [githubról](https://github.com/Azure-Samples/hdinsight-dotnet-java-storm-eventhub). A jelen cikkben ismertetett lépéseket követve azonban továbbra is meg kell adnia a konfigurációs beállításokat.

### <a name="prerequisites"></a>Előfeltételek

* Egy Apache Storm-fürt a HDInsight-on. Lásd: [hozzon létre Apache Hadoop fürtöket a Azure Portal használatával](../hdinsight-hadoop-create-linux-clusters-portal.md) , és válassza a **Storm** a **fürt típusa** lehetőséget.

* Egy [Azure Event hub](../../event-hubs/event-hubs-create.md).

* Az [Azure .net SDK](https://azure.microsoft.com/downloads/)-t.

* A [Visual studióhoz készült HDInsight Tools](../hadoop/apache-hadoop-visual-studio-tools-get-started.md).

* Java JDK 1,8 vagy újabb verzió a fejlesztői környezetben. A JDK letöltése elérhető az [Oracle](/azure/developer/java/fundamentals/java-jdk-long-term-support)-ből.

  * A **JAVA_HOME** környezeti változónak a Java-t tartalmazó könyvtárra kell mutatnia.
  * A **(z)% JAVA_HOME%/bin** könyvtárnak az elérési útnak kell lennie.

## <a name="download-the-event-hubs-components"></a>A Event Hubs-összetevők letöltése

Töltse le a Event Hubs kiöntő és a bolt összetevőt innen: [https://github.com/hdinsight/mvn-repo/raw/master/org/apache/storm/storm-eventhubs/1.1.0.1/storm-eventhubs-1.1.0.1.jar](https://github.com/hdinsight/mvn-repo/raw/master/org/apache/storm/storm-eventhubs/1.1.0.1/storm-eventhubs-1.1.0.1.jar) .

Hozzon létre egy nevű könyvtárat `eventhubspout` , és mentse a fájlt a könyvtárba.

## <a name="configure-event-hubs"></a>Event Hubs konfigurálása

Event Hubs a példa adatforrása. Használja a [Event Hubs első lépéseinek](../../event-hubs/event-hubs-create.md)"az Event hub létrehozása" című szakaszának információit.

1. Az Event hub létrehozása után tekintse meg a **EventHub** beállításait a Azure Portalban, és válassza a **megosztott hozzáférési házirendek** elemet. Válassza a **+ Hozzáadás** lehetőséget a következő szabályzatok létrehozásához:

   | Name | Engedélyek |
   | --- | --- |
   | író |Küldés |
   | olvasó |Figyelés |

    ![Képernyőfelvétel a hozzáférési házirendek megosztásáról ablak](./media/apache-storm-develop-csharp-event-hub-topology/share-access-policies.png)

2. Válassza ki az **olvasó** és az **író** szabályzatot. Másolja és mentse az elsődleges kulcs értékét mindkét házirendhez, mivel ezeket az értékeket később használják.

## <a name="configure-the-eventhubwriter"></a>A EventHubWriter konfigurálása

1. Ha még nem telepítette a HDInsight Tools for Visual Studio legújabb verzióját, tekintse meg a [HDInsight Tools for Visual Studio használatának első lépései](../hadoop/apache-hadoop-visual-studio-tools-get-started.md)című témakört.

2. Töltse le a megoldást a [eventhub-Storm-Hybrid-](https://github.com/Azure-Samples/hdinsight-dotnet-java-storm-eventhub)ból.

3. Nyissa meg a **EventHubExample. SLN**. A **EventHubWriter** projektben nyissa meg a **App.config** fájlt. Használja a korábban konfigurált Event hub információit a következő kulcsok értékének kitöltéséhez:

   | Kulcs | Érték |
   | --- | --- |
   | EventHubPolicyName |író (ha a Szabályzathoz más nevet használt a *küldési* engedéllyel, használja helyette.) |
   | EventHubPolicyKey |Az író házirend kulcsa. |
   | EventHubNamespace |Az Event hub-t tartalmazó névtér. |
   | EventHubName |Az Event hub neve. |
   | EventHubPartitionCount |Az Event hub partícióinak száma. |

4. Mentse és zárjuk be a **App.config** fájlt.

## <a name="configure-the-eventhubreader"></a>A EventHubReader konfigurálása

1. Nyissa meg a **EventHubReader** projektet.

2. Nyissa meg a **EventHubReader** **App.config** -fájlját. Használja a korábban konfigurált Event hub információit a következő kulcsok értékének kitöltéséhez:

   | Kulcs | Érték |
   | --- | --- |
   | EventHubPolicyName |olvasó (ha a Szabályzathoz más nevet használt a *figyelés* engedéllyel, használja helyette.) |
   | EventHubPolicyKey |Az olvasó házirend kulcsa. |
   | EventHubNamespace |Az Event hub-t tartalmazó névtér. |
   | EventHubName |Az Event hub neve. |
   | EventHubPartitionCount |Az Event hub partícióinak száma. |

3. Mentse és zárjuk be a **App.config** fájlt.

## <a name="deploy-the-topologies"></a>A topológiák üzembe helyezése

1. **Megoldáskezelő** kattintson a jobb gombbal a **EventHubReader** projektre, és válassza a **Küldés a Storm on HDInsight** lehetőséget.

    ![Képernyőkép a Megoldáskezelőről, a Beküldés a Storm on HDInsight kiemelve](./media/apache-storm-develop-csharp-event-hub-topology/submit-to-apache-storm.png)

2. A **topológia küldése** párbeszédpanelen válassza ki a **Storm-fürtöt**. Bontsa ki a **további konfigurációk** elemet, válassza a **Java-fájlelérési utak** lehetőséget, majd válassza a **...** lehetőséget, majd válassza ki azt a KÖNYVTÁRAT, amely a korábban letöltött jar-fájlt tartalmazza. Végül kattintson a **Submit (elküldés**) gombra.

    ![A topológia elküldése párbeszédpanel képernyőképe](./media/apache-storm-develop-csharp-event-hub-topology/submit-storm-topology.png)

3. A topológia elküldését követően megjelenik a **Storm-topológiák megjelenítője** . A topológiával kapcsolatos információk megtekintéséhez válassza ki a **EventHubReader** topológiát a bal oldali ablaktáblán.

    ![Képernyőkép a Storm-topológiák megjelenítőről](./media/apache-storm-develop-csharp-event-hub-topology/storm-topology-viewer.png)

4. **Megoldáskezelő** kattintson a jobb gombbal a **EventHubWriter** projektre, és válassza a **Küldés a Storm on HDInsight** lehetőséget.

5. A **topológia küldése** párbeszédpanelen válassza ki a **Storm-fürtöt**. Bontsa ki a **további konfigurációk** elemet, válassza a **Java-fájlelérési utak** lehetőséget, válassza a **...** lehetőséget, majd válassza ki azt a KÖNYVTÁRAT, amely a korábban letöltött jar-fájlt tartalmazza. Végül kattintson a **Submit (elküldés**) gombra.

6. A topológia elküldése után frissítse a topológia listáját a **Storm-topológiák megjelenítőben** annak ellenőrzéséhez, hogy mindkét topológia fut-e a fürtön.

7. A **Storm-topológiák megjelenítőben** válassza ki a **EventHubReader** -topológiát.

8. A bolt összetevő-összegzésének megnyitásához kattintson duplán a **LogBolt** összetevőre a diagramon.

9. A **végrehajtók** szakaszban válassza ki az egyik hivatkozást a **port** oszlopban. Ez az összetevő által naplózott információkat jeleníti meg. A naplózott adatok az alábbi szöveghez hasonlóak:

    ```output
    2017-03-02 14:51:29.255 m.s.p.TaskHost [INFO] Received C# STDOUT: 2017-03-02 14:51:29,255 [1] INFO  EventHubReader_LogBolt [(null)] - Received data: {"deviceValue":1830978598,"deviceId":"8566ccbc-034d-45db-883d-d8a31f34068e"}
    2017-03-02 14:51:29.283 m.s.p.TaskHost [INFO] Received C# STDOUT: 2017-03-02 14:51:29,283 [1] INFO  EventHubReader_LogBolt [(null)] - Received data: {"deviceValue":1756413275,"deviceId":"647a5eff-823d-482f-a8b4-b95b35ae570b"}
    2017-03-02 14:51:29.313 m.s.p.TaskHost [INFO] Received C# STDOUT: 2017-03-02 14:51:29,312 [1] INFO  EventHubReader_LogBolt [(null)] - Received data: {"deviceValue":1108478910,"deviceId":"206a68fa-8264-4d61-9100-bfdb68ee8f0a"}
    ```

## <a name="stop-the-topologies"></a>A topológiák leállítása

A topológiák leállításához jelölje ki az egyes topológiákat a **Storm topológia-megjelenítőben**, majd kattintson a **kill (Kill**) elemre.

![Képernyőkép a Storm topológia-megjelenítőről, a kill gomb kiemelésével](./media/apache-storm-develop-csharp-event-hub-topology/kill-storm-topology1.png)

## <a name="delete-your-cluster"></a>Fürt törlése

[!INCLUDE [delete-cluster-warning](../../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="next-steps"></a>Következő lépések

Ebből a dokumentumból megtudhatta, hogyan használhatja a Java Event Hubs kiöntőt és a boltokat egy C#-topológiából az Azure Event Hubs-ban tárolt adatokkal való munkához. A C#-topológiák létrehozásával kapcsolatos további tudnivalókért tekintse meg a következőket:

* [C#-topológiák fejlesztése a Visual Studio használatával történő HDInsight Apache Stormhoz](apache-storm-develop-csharp-visual-studio-topology.md)
* [SCP programozási útmutató](apache-storm-scp-programming-guide.md)
* [HDInsight alatt futó Apache Storm példatopológiái](apache-storm-example-topology.md)