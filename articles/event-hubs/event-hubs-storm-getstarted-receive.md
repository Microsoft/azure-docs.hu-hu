---
title: Események fogadása az Apache Storm – Azure Event Hubs használatával |} A Microsoft Docs
description: Ez a cikk információt nyújt az események fogadása az Azure Event Hubs, az Apache Storm használatával.
services: event-hubs
documentationcenter: ''
author: ShubhaVijayasarathy
manager: timlt
editor: ''
ms.assetid: ''
ms.service: event-hubs
ms.workload: na
ms.tgt_pltfrm: java
ms.devlang: multiple
ms.topic: article
ms.custom: seodec18
ms.date: 12/06/2018
ms.author: shvija
ms.openlocfilehash: 75a96127c48186befc48b2240f78e49cd5914239
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "60343417"
---
# <a name="receive-events-from-event-hubs-using-apache-storm"></a>Események fogadása az Event Hubs, az Apache Storm használatával

[Az Apache Storm](https://storm.incubator.apache.org) egy elosztott, valós idejű számítási rendszer, amely leegyszerűsíti a kötetlen adatstreamek megbízható feldolgozása. Ez a szakasz bemutatja, hogyan használhatja az Azure Event Hubs Storm spout események fogadása az Event hubs szolgáltatás. Apache Storm használatával, akkor is feloszthatja az eseményeket ezek különböző csomópontokon üzemelnek több folyamat között. A Storm és az Event Hubs-integráció leegyszerűsíti az eseményforrás, átlátható módon ellenőrzőpontok előrehaladási állapotát a Storm a Zookeeper-telepítés, kezeli az állandó ellenőrzőpontokat és a párhuzamos fogadásokat az Event hubs Eseményközpontokból.

Az Event Hubs szolgáltatásról további információt kap a mintákat, tekintse meg a [Event Hubs – áttekintés][Event Hubs overview].

## <a name="prerequisites"></a>Előfeltételek
Ez a rövid útmutató az megkezdése előtt **Event Hubs-névtér és eseményközpont létrehozása**. Használja a [az Azure portal](https://portal.azure.com) hozzon létre egy Event Hubs típusú névteret, és szerezze be az alkalmazása és az eseményközpont közötti kommunikációhoz szükséges felügyeleti hitelesítő adatokat. A névtér és eseményközpont létrehozásához hajtsa végre az eljárást a [Ez a cikk](event-hubs-create.md). 

## <a name="create-project-and-add-code"></a>Projekt létrehozása és kód hozzáadása

Ebben az oktatóanyagban egy [HDInsight Storm] [ HDInsight Storm] telepítése, amely már elérhető az Event Hubs spout is tartalmaz.

1. Kövesse a [HDInsight Storm – első lépések](../hdinsight/storm/apache-storm-overview.md) eljárást követve hozzon létre egy új HDInsight-fürtöt, és a távoli asztalon keresztül csatlakozni hozzá.
2. Másolás a `%STORM_HOME%\examples\eventhubspout\eventhubs-storm-spout-0.9-jar-with-dependencies.jar` fájlt a helyi fejlesztési környezetbe. Az esemény-storm-spout tartalmazza.
3. A következő paranccsal telepítse a csomagot a helyi Maven-tárolóba. Ez lehetővé teszi, hogy egy későbbi lépésben a Storm-projekt hivatkozásként való hozzáadáshoz.

    ```shell
    mvn install:install-file -Dfile=target\eventhubs-storm-spout-0.9-jar-with-dependencies.jar -DgroupId=com.microsoft.eventhubs -DartifactId=eventhubs-storm-spout -Dversion=0.9 -Dpackaging=jar
    ```
4. Az eclipse-ben hozzon létre egy új Maven-projektet (kattintson **fájl**, majd **új**, majd **projekt**).
   
    ![Fájl -> Új -> Projekt][12]
5. Válassza ki **használja az alapértelmezett munkaterület helye**, majd kattintson a **tovább**
6. Válassza ki a **maven-archetype-quickstart** archetype, majd kattintson a **tovább**
7. Helyezze be egy **GroupId** és **ArtifactId**, majd kattintson a **Befejezés**
8. A **pom.xml**, adja hozzá a következő függőségeket a `<dependency>` csomópont.

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

9. Az a **src** mappában nevű fájl létrehozása **Config.properties** , és másolja az alábbi tartalmat, és cserélje le a `receive rule key` és `event hub name` értékeket:

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
    Az érték **eventhub.receiver.credits** határozza meg, hány eseményt vannak kötegelni előtt címek felszabadításával azok a Storm-folyamat számára. Az egyszerűség kedvéért ebben a példában ez az érték 10 állítja be. Éles környezetben, általában ra kell állítani a magasabb értékek; Ha például 1024.
10. Hozzon létre egy új osztályt nevű **LoggerBolt** a következő kóddal:
    
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
    
    A Storm bolt naplózza a fogadott események tartalmát. Ez egyszerűen bővíthető tárolja a rekordokat egy adattárolási szolgáltatásban. A [Az Event Hubs-példában a HDInsight Storm] ugyanezzel a módszerrel használja az adatok tárolásához Azure Storage és a Power bi-ban.
11. Hozzon létre egy osztályt nevű **LogTopology** a következő kóddal:
    
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

    Ez az osztály hoz létre egy új Event Hubs-spout, a konfigurációs fájl a tulajdonságok segítségével hozza létre azt. Fontos megjegyezni, hogy ez a példa létrehoz, számos különböző feladat, az event hubs a partíciók száma az adott eseményközpont által engedélyezett maximális párhuzamossági használatához.

## <a name="next-steps"></a>További lépések
Az alábbi webhelyeken további információt talál az Event Hubsról:

* [Event Hubs – áttekintés][Event Hubs overview]
* [Eseményközpont létrehozása](event-hubs-create.md)
* [Event Hubs – gyakori kérdések](event-hubs-faq.md)

<!-- Links -->
[Event Hubs overview]: event-hubs-what-is-event-hubs.md
[HDInsight Storm]: ../hdinsight/storm/apache-storm-overview.md
[Az Event Hubs-példában a HDInsight Storm]: https://azure.microsoft.com/resources/samples/hdinsight-java-storm-eventhub/

<!-- Images -->

[12]: ./media/event-hubs-get-started-receive-storm/create-storm1.png
