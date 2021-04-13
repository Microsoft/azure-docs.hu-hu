---
title: 'Gyors útmutató: események fogadása Apache Storm használatával – Azure Event Hubs'
description: 'Gyors útmutató: ebben a cikkben arról olvashat, hogyan fogadhat eseményeket az Azure Event Hubs a Apache Storm használatával.'
ms.topic: quickstart
ms.date: 06/23/2020
ms.openlocfilehash: b79c2958a10d0193b706c92a28b1792d63c24b82
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2021
ms.locfileid: "107302964"
---
# <a name="quickstart-receive-events-from-event-hubs-using-apache-storm"></a>Gyors útmutató: események fogadása Event Hubs használatával Apache Storm

A [Apache Storm](https://storm.incubator.apache.org) egy elosztott, valós idejű számítási rendszer, amely leegyszerűsíti a nem kötött adatstreamek megbízható feldolgozását. Ez a szakasz azt mutatja be, hogyan használható az Azure Event Hubs Storm kiöntő az események Event Hubsból való fogadásához. A Apache Storm használatával több, különböző csomópontokban üzemeltetett folyamat között oszthat meg eseményeket. A Storm Event Hubs-integrációja leegyszerűsíti az események felhasználását azáltal, hogy a Storm Zookeeper-telepítésével, az állandó ellenőrzőpontok és a párhuzamos fogadások kezelésével Event Hubs.

A Event Hubs fogadási mintákkal kapcsolatos további információkért tekintse meg a [Event Hubs áttekintését][Event Hubs overview].

## <a name="prerequisites"></a>Előfeltételek
Mielőtt elkezdené a gyors üzembe helyezést, **hozzon létre egy Event Hubs névteret és egy Event hub-** t. A [Azure Portal](https://portal.azure.com) használatával hozzon létre Event Hubs típusú névteret, és szerezze be azokat a felügyeleti hitelesítő adatokat, amelyekre az alkalmazásnak szüksége van az Event hub-vel való kommunikációhoz. A névtér és az Event hub létrehozásához kövesse az [ebben a cikkben](event-hubs-create.md)ismertetett eljárást. 

## <a name="create-project-and-add-code"></a>Projekt létrehozása és kód hozzáadása

Ez az oktatóanyag egy [HDInsight Storm][HDInsight Storm] -telepítést használ, amely a Event Hubs kiöntő már elérhető.

1. Kövesse a [HDInsight Storm – első lépéseket](../hdinsight/storm/apache-storm-overview.md) ismertető eljárást, amellyel új HDInsight-fürtöt hozhat létre, és távoli asztal használatával csatlakozhat hozzá.
2. Másolja a `%STORM_HOME%\examples\eventhubspout\eventhubs-storm-spout-0.9-jar-with-dependencies.jar` fájlt a helyi fejlesztési környezetbe. Ez a Storm-kifolyócső eseményeket tartalmazza.
3. A következő parancs használatával telepítse a csomagot a helyi Maven-tárolóba. Ez lehetővé teszi, hogy egy későbbi lépésben hozzáadja hivatkozásként a Storm-projekthez.

    ```shell
    mvn install:install-file -Dfile=target\eventhubs-storm-spout-0.9-jar-with-dependencies.jar -DgroupId=com.microsoft.eventhubs -DartifactId=eventhubs-storm-spout -Dversion=0.9 -Dpackaging=jar
    ```
4. Az Eclipse-ben hozzon létre egy új Maven-projektet (kattintson a **fájl**, majd az **új**, majd a **projekt**) elemre.
   
    ![Fájl – > New-> Project][12]
5. Válassza **az alapértelmezett munkaterület helyének használata** lehetőséget, majd kattintson a **tovább** gombra.
6. Válassza ki a **Maven-archetípus-Gyorsindítás** archetípusot, majd kattintson a **tovább** gombra.
7. Szúrjon be egy **GroupID** és egy **ArtifactId**, majd kattintson a **Befejezés** gombra.
8. A **pom.xmlban** adja hozzá a következő függőségeket a `<dependency>` csomóponthoz.

    ```xml  
    <dependency>
        <groupId>org.apache.storm</groupId>
        <artifactId>storm-core</artifactId>
        <version>0.9.2-incubating</version>
        <scope>provided</scope>
    </dependency>
    <dependency>
        <groupId>com.microsoft.eventhubs</groupId>
        <artifactId>eventhubs-storm-spout</artifactId>
        <version>0.9</version>
    </dependency>
    <dependency>
        <groupId>com.netflix.curator</groupId>
        <artifactId>curator-framework</artifactId>
        <version>1.3.3</version>
        <exclusions>
            <exclusion>
                <groupId>log4j</groupId>
                <artifactId>log4j</artifactId>
            </exclusion>
            <exclusion>
                <groupId>org.slf4j</groupId>
                <artifactId>slf4j-log4j12</artifactId>
            </exclusion>
        </exclusions>
        <scope>provided</scope>
    </dependency>
    ```

9. A **src** mappában hozzon létre egy **config. properties** nevű fájlt, és másolja a következő tartalmakat, és helyettesítse a `receive rule key` és a `event hub name` értékeket:

    ```java
    eventhubspout.username = ReceiveRule
    eventhubspout.password = {receive rule key}
    eventhubspout.namespace = ioteventhub-ns
    eventhubspout.entitypath = {event hub name}
    eventhubspout.partitions.count = 16
       
    # if not provided, will use storm's zookeeper settings
    # zookeeper.connectionstring=localhost:2181
       
    eventhubspout.checkpoint.interval = 10
    eventhub.receiver.credits = 10
    ```
    A **eventhub. receiver. Credits** értéke határozza meg, hogy hány esemény van kötegelt feldolgozás alatt, mielőtt felszabadítja őket a Storm-folyamatba. Az egyszerűség kedvéért ez a példa 10 értékre állítja ezt az értéket. Éles környezetben általában magasabb értékekre kell beállítani; például 1024.
10. Hozzon létre egy új, **LoggerBolt** nevű osztályt a következő kóddal:
    
    ```java
    import java.util.Map;
    import org.slf4j.Logger;
    import org.slf4j.LoggerFactory;
    import backtype.storm.task.OutputCollector;
    import backtype.storm.task.TopologyContext;
    import backtype.storm.topology.OutputFieldsDeclarer;
    import backtype.storm.topology.base.BaseRichBolt;
    import backtype.storm.tuple.Tuple;
    
    public class LoggerBolt extends BaseRichBolt {
        private OutputCollector collector;
        private static final Logger logger = LoggerFactory
                  .getLogger(LoggerBolt.class);
    
        @Override
        public void execute(Tuple tuple) {
            String value = tuple.getString(0);
            logger.info("Tuple value: " + value);
   
            collector.ack(tuple);
        }
   
        @Override
        public void prepare(Map map, TopologyContext context, OutputCollector collector) {
            this.collector = collector;
            this.count = 0;
        }
        
        @Override
        public void declareOutputFields(OutputFieldsDeclarer declarer) {
            // no output fields
        }
    
    }
    ```
    
    Ez a Storm-bolt naplózza a fogadott események tartalmát. Ezt egyszerűen kiterjesztheti a tárolási szolgáltatás rekordok tárolására. Az [Event hub-HDInsight Storm] ezt a megközelítést használja az Azure Storage-ba és a Power BIba való adattároláshoz.
11. Hozzon létre egy **LogTopology** nevű osztályt a következő kóddal:
    
    ```java
    import java.io.FileReader;
    import java.util.Properties;
    import backtype.storm.Config;
    import backtype.storm.LocalCluster;
    import backtype.storm.StormSubmitter;
    import backtype.storm.generated.StormTopology;
    import backtype.storm.topology.TopologyBuilder;
    import com.microsoft.eventhubs.samples.EventCount;
    import com.microsoft.eventhubs.spout.EventHubSpout;
    import com.microsoft.eventhubs.spout.EventHubSpoutConfig;
        
    public class LogTopology {
        protected EventHubSpoutConfig spoutConfig;
        protected int numWorkers;
        
        protected void readEHConfig(String[] args) throws Exception {
            Properties properties = new Properties();
            if (args.length > 1) {
                properties.load(new FileReader(args[1]));
            } else {
                properties.load(EventCount.class.getClassLoader()
                        .getResourceAsStream("Config.properties"));
            }
        
            String username = properties.getProperty("eventhubspout.username");
            String password = properties.getProperty("eventhubspout.password");
            String namespaceName = properties
                    .getProperty("eventhubspout.namespace");
            String entityPath = properties.getProperty("eventhubspout.entitypath");
            String zkEndpointAddress = properties
                    .getProperty("zookeeper.connectionstring"); // opt
            int partitionCount = Integer.parseInt(properties
                    .getProperty("eventhubspout.partitions.count"));
            int checkpointIntervalInSeconds = Integer.parseInt(properties
                    .getProperty("eventhubspout.checkpoint.interval"));
            int receiverCredits = Integer.parseInt(properties
                    .getProperty("eventhub.receiver.credits")); // prefetch count
                                                                // (opt)
            System.out.println("Eventhub spout config: ");
            System.out.println("  partition count: " + partitionCount);
            System.out.println("  checkpoint interval: "
                    + checkpointIntervalInSeconds);
            System.out.println("  receiver credits: " + receiverCredits);
     
            spoutConfig = new EventHubSpoutConfig(username, password,
                    namespaceName, entityPath, partitionCount, zkEndpointAddress,
                    checkpointIntervalInSeconds, receiverCredits);
        
            // set the number of workers to be the same as partition number.
            // the idea is to have a spout and a logger bolt co-exist in one
            // worker to avoid shuffling messages across workers in storm cluster.
            numWorkers = spoutConfig.getPartitionCount();
        
            if (args.length > 0) {
                // set topology name so that sample Trident topology can use it as
                // stream name.
                spoutConfig.setTopologyName(args[0]);
            }
        }
        
        protected StormTopology buildTopology() {
            TopologyBuilder topologyBuilder = new TopologyBuilder();
       
            EventHubSpout eventHubSpout = new EventHubSpout(spoutConfig);
            topologyBuilder.setSpout("EventHubsSpout", eventHubSpout,
                    spoutConfig.getPartitionCount()).setNumTasks(
                    spoutConfig.getPartitionCount());
            topologyBuilder
                    .setBolt("LoggerBolt", new LoggerBolt(),
                            spoutConfig.getPartitionCount())
                    .localOrShuffleGrouping("EventHubsSpout")
                    .setNumTasks(spoutConfig.getPartitionCount());
            return topologyBuilder.createTopology();
        }
        
        protected void runScenario(String[] args) throws Exception {
            boolean runLocal = true;
            readEHConfig(args);
            StormTopology topology = buildTopology();
            Config config = new Config();
            config.setDebug(false);
        
            if (runLocal) {
                config.setMaxTaskParallelism(2);
                LocalCluster localCluster = new LocalCluster();
                localCluster.submitTopology("test", config, topology);
                Thread.sleep(5000000);
                localCluster.shutdown();
            } else {
                config.setNumWorkers(numWorkers);
                StormSubmitter.submitTopology(args[0], config, topology);
            }
        }
        
        public static void main(String[] args) throws Exception {
            LogTopology topology = new LogTopology();
            topology.runScenario(args);
        }
    }
    ```

    Ez az osztály létrehoz egy új Event Hubs kiöntőt, amely a konfigurációs fájlban található tulajdonságok használatával hozza létre. Fontos megjegyezni, hogy ez a példa annyi kiöntő feladatot hoz létre az Event hub partícióinak számaként, hogy az adott Event hub által engedélyezett maximális párhuzamosságot használja.

## <a name="next-steps"></a>Következő lépések
Az alábbi webhelyeken további információt talál az Event Hubsról:

* [Event Hubs áttekintése][Event Hubs overview]
* [Eseményközpont létrehozása](event-hubs-create.md)
* [Event Hubs – gyakori kérdések](event-hubs-faq.yml)

<!-- Links -->
[Event Hubs overview]: ./event-hubs-about.md
[HDInsight Storm]: ../hdinsight/storm/apache-storm-overview.md
[HDInsight Storm az Event hub példával]: https://github.com/Azure-Samples/hdinsight-java-storm-eventhub

<!-- Images -->

[12]: ./media/event-hubs-get-started-receive-storm/create-storm1.png
