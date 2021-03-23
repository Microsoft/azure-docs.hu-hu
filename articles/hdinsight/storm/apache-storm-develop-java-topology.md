---
title: Apache Storm példa Java-topológiára – Azure HDInsight
description: Megtudhatja, hogyan hozhat létre Apache Storm-topológiákat Java-ban egy példa Word Count topológia létrehozásával.
ms.service: hdinsight
ms.topic: how-to
ms.custom: H1Hack27Feb2017,hdinsightactive,hdiseo17may2017,seoapr2020,devx-track-java
ms.date: 04/27/2020
ms.openlocfilehash: 8e51f99496514e40d6c3f85000384a14baf84946
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/23/2021
ms.locfileid: "104865366"
---
# <a name="create-an-apache-storm-topology-in-java"></a>Apache Storm topológia létrehozása javában

Megtudhatja, hogyan hozhat létre Java-alapú topológiát Apache Stormhoz. Olyan Storm-topológiát hoz létre, amely egy Word-Count alkalmazást implementál. Az Apache Maven használatával felépítheti és becsomagolhatja a projektet. Ezután megtudhatja, hogyan határozhatja meg a topológiát a Apache Storm Flux-keretrendszer használatával.

A dokumentum lépéseinek elvégzése után telepítheti a topológiát, hogy Apache Storm a HDInsight.

> [!NOTE]  
> A jelen dokumentumban létrehozott Storm-topológiai példák befejezett verziója a következő címen érhető el: [https://github.com/Azure-Samples/hdinsight-java-storm-wordcount](https://github.com/Azure-Samples/hdinsight-java-storm-wordcount) .

## <a name="prerequisites"></a>Előfeltételek

* [Java Developer Kit (JDK) 8-as verzió](/azure/developer/java/fundamentals/java-jdk-long-term-support)

* Az [Apache Maven](https://maven.apache.org/download.cgi) megfelelően [van telepítve](https://maven.apache.org/install.html) az Apache-ban.  A Maven egy projekt-összeállítási rendszer Java-projektekhez.

## <a name="test-environment"></a>Tesztkörnyezet

A cikkben használt környezet a Windows 10 rendszert futtató számítógép volt.  A parancsok végrehajtása egy parancssorban történt, a különböző fájlok pedig a Jegyzettömb alkalmazásban lettek szerkesztve.

A parancssorba írja be az alábbi parancsokat egy működő környezet létrehozásához:

```cmd
mkdir C:\HDI
cd C:\HDI
```

## <a name="create-a-maven-project"></a>Maven-projekt létrehozása

Adja meg a következő parancsot egy **WordCount** nevű Maven-projekt létrehozásához:

```cmd
mvn archetype:generate -DarchetypeArtifactId=maven-archetype-quickstart -DgroupId=com.microsoft.example -DartifactId=WordCount -DinteractiveMode=false

cd WordCount
mkdir resources
```

Ez a parancs létrehoz egy nevű könyvtárat `WordCount` az aktuális helyen, amely egy alapszintű Maven-projektet tartalmaz. A második parancs megváltoztatja a jelen munkakönyvtárat a következőre: `WordCount` . A harmadik parancs létrehoz egy új könyvtárat, `resources` amelyet később fog használni.  A `WordCount` könyvtár a következő elemeket tartalmazza:

* `pom.xml`: A Maven projekt beállításait tartalmazza.
* `src\main\java\com\microsoft\example`: Az alkalmazás kódját tartalmazza.
* `src\test\java\com\microsoft\example`: Az alkalmazáshoz tartozó teszteket tartalmazza.  

### <a name="remove-the-generated-example-code"></a>A generált mintakód eltávolítása

Törölje a generált teszt-és alkalmazásfájl-fájlokat `AppTest.java` , és `App.java` írja be az alábbi parancsokat:

```cmd
DEL src\main\java\com\microsoft\example\App.java
DEL src\test\java\com\microsoft\example\AppTest.java
```

## <a name="add-maven-repositories"></a>Maven-Tárházak hozzáadása

A HDInsight a Hortonworks-adatplatformon (HDP) alapul, ezért javasoljuk, hogy a Hortonworks-tárház használatával letöltse a Apache Storm projektjeinek függőségeit.  

A megnyitásához `pom.xml` írja be az alábbi parancsot:

```cmd
notepad pom.xml
```

Ezután adja hozzá a következő XML-fájlt a `<url>https://maven.apache.org</url>` sor után:

```xml
<repositories>
    <repository>
        <releases>
            <enabled>true</enabled>
            <updatePolicy>always</updatePolicy>
            <checksumPolicy>warn</checksumPolicy>
        </releases>
        <snapshots>
            <enabled>false</enabled>
            <updatePolicy>never</updatePolicy>
            <checksumPolicy>fail</checksumPolicy>
        </snapshots>
        <id>HDPReleases</id>
        <name>HDP Releases</name>
        <url>https://repo.hortonworks.com/content/repositories/releases/</url>
        <layout>default</layout>
    </repository>
    <repository>
        <releases>
            <enabled>true</enabled>
            <updatePolicy>always</updatePolicy>
            <checksumPolicy>warn</checksumPolicy>
        </releases>
        <snapshots>
            <enabled>false</enabled>
            <updatePolicy>never</updatePolicy>
            <checksumPolicy>fail</checksumPolicy>
        </snapshots>
        <id>HDPJetty</id>
        <name>Hadoop Jetty</name>
        <url>https://repo.hortonworks.com/content/repositories/jetty-hadoop/</url>
        <layout>default</layout>
    </repository>
</repositories>
```

## <a name="add-properties"></a>Tulajdonságok hozzáadása

A Maven segítségével meghatározhatja a tulajdonságok nevű projekt szintű értékeket. A-ben `pom.xml` adja hozzá a következő szöveget a `</repositories>` sor után:

```xml
<properties>
    <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
    <!--
    This is a version of Storm from the Hortonworks repository that is compatible with HDInsight 3.6.
    -->
    <storm.version>1.1.0.2.6.1.9-1</storm.version>
</properties>
```

Ezt az értéket már használhatja a további részeiben `pom.xml` . Például a Storm-összetevők verziójának megadásakor használhat `${storm.version}` egy értéket a rögzített kódolás helyett.

## <a name="add-dependencies"></a>Függőségek hozzáadása

Függőség hozzáadása Storm-összetevőkhöz. A alkalmazásban `pom.xml` adja hozzá a következő szöveget a `<dependencies>` szakaszhoz:

```xml
<dependency>
    <groupId>org.apache.storm</groupId>
    <artifactId>storm-core</artifactId>
    <version>${storm.version}</version>
    <!-- keep storm out of the jar-with-dependencies -->
    <scope>provided</scope>
</dependency>
```

A Maven a fordítás ideje alatt ezeket az információkat használja a `storm-core` Maven-tárházban való kereséshez. A rendszer először a helyi számítógép tárházában keres. Ha a fájlok nincsenek ott, a Maven letölti őket a nyilvános Maven-tárházból, és a helyi tárházban tárolja őket.

> [!NOTE]  
> Figyelje `<scope>provided</scope>` meg a szakasz sorát. Ez a beállítás azt jelzi, hogy a Maven kizárják a **Storm-Core-** t a létrehozott jar-fájlokból, mert azt a rendszer megadja.

## <a name="build-configuration"></a>Buildelési konfiguráció

A Maven beépülő moduljai lehetővé teszik a projekt összeállítási szakaszainak testreszabását. Például hogyan történik a projekt lefordítása, vagy egy JAR-fájlba való csomagolása. A alkalmazásban `pom.xml` adja hozzá a következő szöveget közvetlenül a `</project>` sor fölé.

```xml
<build>
    <plugins>
    </plugins>
    <resources>
    </resources>
</build>
```

Ez a szakasz beépülő modulok, erőforrások és egyéb Build-konfigurációs beállítások hozzáadására szolgál. A fájl teljes hivatkozását a `pom.xml` következő témakörben tekintheti meg: [https://maven.apache.org/pom.html](https://maven.apache.org/pom.html) .

### <a name="add-plug-ins"></a>Beépülő modulok hozzáadása

* **Exec Maven beépülő modul**

    A javában megvalósított Apache Storm-topológiák esetében az [exec Maven beépülő modul](https://www.mojohaus.org/exec-maven-plugin/) hasznos, mivel lehetővé teszi, hogy könnyedén futtassa a topológiát a fejlesztői környezetben. Adja hozzá a következőt a `<plugins>` fájl szakaszához `pom.xml` , hogy tartalmazza az exec Maven beépülő modult:

    ```xml
    <plugin>
        <groupId>org.codehaus.mojo</groupId>
        <artifactId>exec-maven-plugin</artifactId>
        <version>1.6.0</version>
        <executions>
            <execution>
                <goals>
                    <goal>exec</goal>
                </goals>
            </execution>
        </executions>
        <configuration>
            <executable>java</executable>
            <includeProjectDependencies>true</includeProjectDependencies>
            <includePluginDependencies>false</includePluginDependencies>
            <classpathScope>compile</classpathScope>
            <mainClass>${storm.topology}</mainClass>
            <cleanupDaemonThreads>false</cleanupDaemonThreads>
        </configuration>
    </plugin>
    ```

* **Apache Maven Compiler beépülő modul**

    Egy másik hasznos beépülő modul a [`Apache Maven Compiler Plugin`](https://maven.apache.org/plugins/maven-compiler-plugin/) , amely a fordítási beállítások módosítására szolgál. Módosítsa a Maven által az alkalmazás forrásához és céljához használt Java-verziót.

  * A __3,4-es vagy korábbi__ HDInsight esetében állítsa a forrás és a cél Java-verziót __1,7__-re.

  * A HDInsight __3,5__ esetében állítsa a forrás és a cél Java-verziót __1,8__-re.

  Adja hozzá a következő szöveget a `<plugins>` fájl szakaszához `pom.xml` , hogy tartalmazza az Apache Maven Compiler beépülő modulját. Ez a példa a 1,8-as verziót adja meg, így a cél HDInsight verziója 3,5.

  ```xml
  <plugin>
    <groupId>org.apache.maven.plugins</groupId>
    <artifactId>maven-compiler-plugin</artifactId>
    <version>3.8.1</version>
    <configuration>
            <source>1.8</source>
            <target>1.8</target>
    </configuration>
  </plugin>
  ```

### <a name="configure-resources"></a>Erőforrások konfigurálása

A Resources (erőforrások) szakasz lehetővé teszi a nem Code típusú erőforrások, például a topológia összetevői által igényelt konfigurációs fájlok beágyazását. Ebben a példában adja hozzá a következő szöveget a `<resources>` fájl szakaszához `pom.xml` . Ezután mentse és zárja be a fájlt.

```xml
<resource>
    <directory>${basedir}/resources</directory>
    <filtering>false</filtering>
    <includes>
            <include>log4j2.xml</include>
    </includes>
</resource>
```

Ez a példa hozzáadja az erőforrások könyvtárat a projekt gyökérkönyvtárában ( `${basedir}` ) az erőforrásokat tartalmazó helyként, és tartalmazza a nevű fájlt `log4j2.xml` . Ezzel a fájllal konfigurálhatók a topológia által naplózott információk.

## <a name="create-the-topology"></a>A topológia létrehozása

A Java-alapú Apache Storm topológia három olyan összetevőből áll, amelyeknek függőségként kell megadnia (vagy hivatkoznia kell).

* **Kiöntő**: külső forrásokból származó adatok beolvasása és adatstreamek kibocsátása a topológiába.

* **Csavarok**: kiöntő vagy más csavarok által kibocsátott adatfolyamok feldolgozását végzi, és egy vagy több streamet bocsát ki.

* **Topológia**: meghatározza a kiöntő és a csavarok elrendezését, és megadja a topológia belépési pontját.

### <a name="create-the-spout"></a>A kiöntő létrehozása

A külső adatforrások beállításához szükséges követelmények csökkentése érdekében a következő kiöntő egyszerűen véletlenszerű mondatokat bocsát ki. A kiöntő egy módosított verziója, amely a [Storm-Starter-példákkal](https://github.com/apache/storm/blob/0.10.x-branch/examples/storm-starter/src/jvm/storm/starter)van ellátva.  Bár ez a topológia egy kiöntőt használ, mások rendelkezhetnek több, különböző forrásokból származó adatokkal a topológiában`.`

Új fájl létrehozásához és megnyitásához írja be az alábbi parancsot `RandomSentenceSpout.java` :

```cmd
notepad src\main\java\com\microsoft\example\RandomSentenceSpout.java
```

Ezután másolja és illessze be az alábbi Java-kódot az új fájlba.  Ezután zárjuk be a fájlt.

```java
package com.microsoft.example;

import org.apache.storm.spout.SpoutOutputCollector;
import org.apache.storm.task.TopologyContext;
import org.apache.storm.topology.OutputFieldsDeclarer;
import org.apache.storm.topology.base.BaseRichSpout;
import org.apache.storm.tuple.Fields;
import org.apache.storm.tuple.Values;
import org.apache.storm.utils.Utils;

import java.util.Map;
import java.util.Random;

//This spout randomly emits sentences
public class RandomSentenceSpout extends BaseRichSpout {
  //Collector used to emit output
  SpoutOutputCollector _collector;
  //Used to generate a random number
  Random _rand;

  //Open is called when an instance of the class is created
  @Override
  public void open(Map conf, TopologyContext context, SpoutOutputCollector collector) {
  //Set the instance collector to the one passed in
    _collector = collector;
    //For randomness
    _rand = new Random();
  }

  //Emit data to the stream
  @Override
  public void nextTuple() {
  //Sleep for a bit
    Utils.sleep(100);
    //The sentences that are randomly emitted
    String[] sentences = new String[]{ "the cow jumped over the moon", "an apple a day keeps the doctor away",
        "four score and seven years ago", "snow white and the seven dwarfs", "i am at two with nature" };
    //Randomly pick a sentence
    String sentence = sentences[_rand.nextInt(sentences.length)];
    //Emit the sentence
    _collector.emit(new Values(sentence));
  }

  //Ack is not implemented since this is a basic example
  @Override
  public void ack(Object id) {
  }

  //Fail is not implemented since this is a basic example
  @Override
  public void fail(Object id) {
  }

  //Declare the output fields. In this case, an sentence
  @Override
  public void declareOutputFields(OutputFieldsDeclarer declarer) {
    declarer.declare(new Fields("sentence"));
  }
}
```

> [!NOTE]  
> Egy külső adatforrásból beolvasott kiöntő példához tekintse meg az alábbi példák egyikét:
>
> * [TwitterSampleSPout](https://github.com/apache/storm/blob/0.10.x-branch/examples/storm-starter/src/jvm/storm/starter/spout/TwitterSampleSpout.java): a Twitterről beolvasott kiöntő példa.
> * [Storm-Kafka](https://github.com/apache/storm/tree/0.10.x-branch/external/storm-kafka): a kafkaből beolvasott kiöntő.

### <a name="create-the-bolts"></a>A csavarok létrehozása

A csavarok kezelik az adatfeldolgozást. A csavarok bármit, például számítást, adatmegőrzést vagy külső összetevőkkel való kommunikációt végezhetnek. Ez a topológia két csavart használ:

* **SplitSentence**: a **RandomSentenceSpout** által kibocsátott mondatok kiosztása egyéni szavakkal.

* **WordCount**: megszámolja, hogy hányszor történtek az egyes szavak.

#### <a name="splitsentence"></a>SplitSentence

Új fájl létrehozásához és megnyitásához írja be az alábbi parancsot `SplitSentence.java` :

```cmd
notepad src\main\java\com\microsoft\example\SplitSentence.java
```

Ezután másolja és illessze be az alábbi Java-kódot az új fájlba.  Ezután zárjuk be a fájlt.

```java
package com.microsoft.example;

import java.text.BreakIterator;

import org.apache.storm.topology.BasicOutputCollector;
import org.apache.storm.topology.OutputFieldsDeclarer;
import org.apache.storm.topology.base.BaseBasicBolt;
import org.apache.storm.tuple.Fields;
import org.apache.storm.tuple.Tuple;
import org.apache.storm.tuple.Values;

//There are a variety of bolt types. In this case, use BaseBasicBolt
public class SplitSentence extends BaseBasicBolt {

  //Execute is called to process tuples
  @Override
  public void execute(Tuple tuple, BasicOutputCollector collector) {
    //Get the sentence content from the tuple
    String sentence = tuple.getString(0);
    //An iterator to get each word
    BreakIterator boundary=BreakIterator.getWordInstance();
    //Give the iterator the sentence
    boundary.setText(sentence);
    //Find the beginning first word
    int start=boundary.first();
    //Iterate over each word and emit it to the output stream
    for (int end=boundary.next(); end != BreakIterator.DONE; start=end, end=boundary.next()) {
      //get the word
      String word=sentence.substring(start,end);
      //If a word is whitespace characters, replace it with empty
      word=word.replaceAll("\\s+","");
      //if it's an actual word, emit it
      if (!word.equals("")) {
        collector.emit(new Values(word));
      }
    }
  }

  //Declare that emitted tuples contain a word field
  @Override
  public void declareOutputFields(OutputFieldsDeclarer declarer) {
    declarer.declare(new Fields("word"));
  }
}
```

#### <a name="wordcount"></a>WordCount

Új fájl létrehozásához és megnyitásához írja be az alábbi parancsot `WordCount.java` :

```cmd
notepad src\main\java\com\microsoft\example\WordCount.java
```

Ezután másolja és illessze be az alábbi Java-kódot az új fájlba.  Ezután zárjuk be a fájlt.

```java
package com.microsoft.example;

import java.util.HashMap;
import java.util.Map;
import java.util.Iterator;

import org.apache.storm.Constants;
import org.apache.storm.topology.BasicOutputCollector;
import org.apache.storm.topology.OutputFieldsDeclarer;
import org.apache.storm.topology.base.BaseBasicBolt;
import org.apache.storm.tuple.Fields;
import org.apache.storm.tuple.Tuple;
import org.apache.storm.tuple.Values;
import org.apache.storm.Config;

// For logging
import org.apache.logging.log4j.Logger;
import org.apache.logging.log4j.LogManager;

//There are a variety of bolt types. In this case, use BaseBasicBolt
public class WordCount extends BaseBasicBolt {
  //Create logger for this class
  private static final Logger logger = LogManager.getLogger(WordCount.class);
  //For holding words and counts
  Map<String, Integer> counts = new HashMap<String, Integer>();
  //How often to emit a count of words
  private Integer emitFrequency;

  // Default constructor
  public WordCount() {
      emitFrequency=5; // Default to 60 seconds
  }

  // Constructor that sets emit frequency
  public WordCount(Integer frequency) {
      emitFrequency=frequency;
  }

  //Configure frequency of tick tuples for this bolt
  //This delivers a 'tick' tuple on a specific interval,
  //which is used to trigger certain actions
  @Override
  public Map<String, Object> getComponentConfiguration() {
      Config conf = new Config();
      conf.put(Config.TOPOLOGY_TICK_TUPLE_FREQ_SECS, emitFrequency);
      return conf;
  }

  //execute is called to process tuples
  @Override
  public void execute(Tuple tuple, BasicOutputCollector collector) {
    //If it's a tick tuple, emit all words and counts
    if(tuple.getSourceComponent().equals(Constants.SYSTEM_COMPONENT_ID)
            && tuple.getSourceStreamId().equals(Constants.SYSTEM_TICK_STREAM_ID)) {
      for(String word : counts.keySet()) {
        Integer count = counts.get(word);
        collector.emit(new Values(word, count));
        logger.info("Emitting a count of " + count + " for word " + word);
      }
    } else {
      //Get the word contents from the tuple
      String word = tuple.getString(0);
      //Have we counted any already?
      Integer count = counts.get(word);
      if (count == null)
        count = 0;
      //Increment the count and store it
      count++;
      counts.put(word, count);
    }
  }

  //Declare that this emits a tuple containing two fields; word and count
  @Override
  public void declareOutputFields(OutputFieldsDeclarer declarer) {
    declarer.declare(new Fields("word", "count"));
  }
}
```

### <a name="define-the-topology"></a>A topológia definiálása

A topológia összeköti a kiöntő és a csavarokat egy gráfba. A gráf határozza meg, hogyan áramlik az adatforgalom az összetevők között. Emellett olyan párhuzamossági mutatókat is biztosít, amelyeket a Storm használ a fürtben található összetevők példányainak létrehozásakor.

A következő kép a topológiához tartozó összetevők gráfjának alapszintű ábrája.

:::image type="content" source="./media/apache-storm-develop-java-topology/word-count-topology1.png" alt-text="a kiöntő és a csavarok elrendezését bemutató ábra" border="false":::

A topológia megvalósításához írja be az alábbi parancsot egy új fájl létrehozásához és megnyitásához `WordCountTopology.java` :

```cmd
notepad src\main\java\com\microsoft\example\WordCountTopology.java
```

Ezután másolja és illessze be az alábbi Java-kódot az új fájlba.  Ezután zárjuk be a fájlt.

```java
package com.microsoft.example;

import org.apache.storm.Config;
import org.apache.storm.LocalCluster;
import org.apache.storm.StormSubmitter;
import org.apache.storm.topology.TopologyBuilder;
import org.apache.storm.tuple.Fields;

import com.microsoft.example.RandomSentenceSpout;

public class WordCountTopology {

  //Entry point for the topology
  public static void main(String[] args) throws Exception {
  //Used to build the topology
    TopologyBuilder builder = new TopologyBuilder();
    //Add the spout, with a name of 'spout'
    //and parallelism hint of 5 executors
    builder.setSpout("spout", new RandomSentenceSpout(), 5);
    //Add the SplitSentence bolt, with a name of 'split'
    //and parallelism hint of 8 executors
    //shufflegrouping subscribes to the spout, and equally distributes
    //tuples (sentences) across instances of the SplitSentence bolt
    builder.setBolt("split", new SplitSentence(), 8).shuffleGrouping("spout");
    //Add the counter, with a name of 'count'
    //and parallelism hint of 12 executors
    //fieldsgrouping subscribes to the split bolt, and
    //ensures that the same word is sent to the same instance (group by field 'word')
    builder.setBolt("count", new WordCount(), 12).fieldsGrouping("split", new Fields("word"));

    //new configuration
    Config conf = new Config();
    //Set to false to disable debug information when
    // running in production on a cluster
    conf.setDebug(false);

    //If there are arguments, we are running on a cluster
    if (args != null && args.length > 0) {
      //parallelism hint to set the number of workers
      conf.setNumWorkers(3);
      //submit the topology
      StormSubmitter.submitTopology(args[0], conf, builder.createTopology());
    }
    //Otherwise, we are running locally
    else {
      //Cap the maximum number of executors that can be spawned
      //for a component to 3
      conf.setMaxTaskParallelism(3);
      //LocalCluster is used to run locally
      LocalCluster cluster = new LocalCluster();
      //submit the topology
      cluster.submitTopology("word-count", conf, builder.createTopology());
      //sleep
      Thread.sleep(10000);
      //shut down the cluster
      cluster.shutdown();
    }
  }
}
```

### <a name="configure-logging"></a>Naplózás konfigurálása

A Storm az [Apache Log4j 2](https://logging.apache.org/log4j/2.x/) használatával naplózza az adatokat. Ha nem konfigurálja a naplózást, a topológia diagnosztikai adatokat bocsát ki. A naplózott fájl létrehozásához hozzon létre egy nevű fájlt `log4j2.xml` a `resources` könyvtárban az alábbi parancs beírásával:

```cmd
notepad resources\log4j2.xml
```

Ezután másolja és illessze be az alábbi XML-szöveget az új fájlba.  Ezután zárjuk be a fájlt.

```xml
<?xml version="1.0" encoding="UTF-8"?>
<Configuration>
    <Appenders>
        <Console name="STDOUT" target="SYSTEM_OUT">
            <PatternLayout pattern="%d{HH:mm:ss} [%t] %-5level %logger{36} - %msg%n"/>
        </Console>
    </Appenders>
    <Loggers>
        <Logger name="com.microsoft.example" level="trace" additivity="false">
            <AppenderRef ref="STDOUT"/>
        </Logger>
        <Root level="error">
            <Appender-Ref ref="STDOUT"/>
        </Root>
    </Loggers>
</Configuration>
```

Ez az XML a osztály új naplózó konfigurálja `com.microsoft.example` , amely tartalmazza az ebben a példában szereplő topológiában található összetevőket is. A szint a nyomkövetésre van beállítva a naplózó számára, amely rögzíti az ebben a topológiában található összetevők által kibocsátott naplózási adatokat.

A `<Root level="error">` szakasz a naplózás gyökérszintű szintjének konfigurálását (ami nem a-ban `com.microsoft.example` ) csak a hibajelentési adatok naplózására van konfigurálva.

A 2. Log4j naplózásának konfigurálásával kapcsolatos további információkért lásd: [https://logging.apache.org/log4j/2.x/manual/configuration.html](https://logging.apache.org/log4j/2.x/manual/configuration.html) .

> [!NOTE]  
> A Storm-verzió 0.10.0 és újabb verziója a Log4j 2. x verzióját használja. A Storm régebbi verziói az 1. x Log4j használják, amely más formátumot használt a naplózási konfigurációhoz. A régebbi konfigurációval kapcsolatos információkért lásd: [https://cwiki.apache.org/confluence/display/LOGGINGLOG4J/Log4jXmlFormat](https://cwiki.apache.org/confluence/display/LOGGINGLOG4J/Log4jXmlFormat) .

## <a name="test-the-topology-locally"></a>A topológia helyi tesztelése

A fájlok mentése után a következő paranccsal ellenőrizheti a topológia helyi tesztelését.

```cmd
mvn compile exec:java -Dstorm.topology=com.microsoft.example.WordCountTopology
```

Ahogy fut, a topológia az indítási adatokat jeleníti meg. A következő szöveg a szavak darabszámának kimenetét szemlélteti:

```output
17:33:27 [Thread-12-count] INFO  com.microsoft.example.WordCount - Emitting a count of 56 for word snow
17:33:27 [Thread-12-count] INFO  com.microsoft.example.WordCount - Emitting a count of 56 for word white
17:33:27 [Thread-12-count] INFO  com.microsoft.example.WordCount - Emitting a count of 112 for word seven
17:33:27 [Thread-16-count] INFO  com.microsoft.example.WordCount - Emitting a count of 195 for word the
17:33:27 [Thread-30-count] INFO  com.microsoft.example.WordCount - Emitting a count of 113 for word and
17:33:27 [Thread-30-count] INFO  com.microsoft.example.WordCount - Emitting a count of 57 for word dwarfs
17:33:27 [Thread-12-count] INFO  com.microsoft.example.WordCount - Emitting a count of 57 for word snow
```

Ez a példa azt jelzi, hogy a "és a" szó 113 alkalommal lett kibocsátva. A szám folyamatosan növekszik, amíg a topológia fut. Ennek a növekedésnek az az oka, hogy a kifolyó folyamatosan ugyanazt a mondatot bocsátja ki.

A szavak és a számok kibocsátása között 5 másodperces intervallum van. A **WordCount** összetevő úgy van konfigurálva, hogy csak a Tick rekord megérkezése esetén állítson elő információkat. Azt kéri, hogy a Tick rekordok csak öt másodpercenként kerüljön kézbesítésre.

## <a name="convert-the-topology-to-flux"></a>A topológia átalakítása a Fluxusba

A [Flux](https://storm.apache.org/releases/2.0.0/flux.html) egy új keretrendszer, amely a Storm 0.10.0 és újabb verziókban érhető el. A Flux segítségével elkülönítheti a konfigurációt a megvalósítástól. Az összetevők továbbra is Java-ban vannak definiálva, de a topológia egy YAML-fájl használatával van definiálva. Egy alapértelmezett topológia-definíciót is becsomagolhat a projektbe, vagy használhat önálló fájlt a topológia elküldésekor. Ha a topológiát a Storm-re küldi el, környezeti változók vagy konfigurációs fájlok használatával feltöltheti a YAML topológia-definíciós értékeit.

A YAML fájl határozza meg a topológia és a közöttük lévő adatfolyamok által használt összetevőket. A jar-fájl részeként YAML-fájlt is megadhat. Külső YAML-fájlt is használhat.

További információ a Fluxról: [Flux Framework ( https://storm.apache.org/releases/current/flux.html) ](https://storm.apache.org/releases/current/flux.html).

> [!WARNING]  
> Egy hiba miatt [( https://issues.apache.org/jira/browse/STORM-2055) ](https://issues.apache.org/jira/browse/STORM-2055) a Storm 1.0.1 esetében előfordulhat, hogy egy [Storm fejlesztői környezetet](https://storm.apache.org/releases/current/Setting-up-development-environment.html) kell telepítenie az adatáramlási topológiák helyi futtatásához.

1. Korábban `WordCountTopology.java` definiálta a topológiát, de nem szükséges a Flux esetében. Törölje a fájlt a következő paranccsal:

    ```cmd
    DEL src\main\java\com\microsoft\example\WordCountTopology.java
    ```

1. Új fájl létrehozásához és megnyitásához írja be az alábbi parancsot `topology.yaml` :

    ```cmd
    notepad resources\topology.yaml
    ```

    Ezután másolja és illessze be az alábbi szöveget az új fájlba.  Ezután zárjuk be a fájlt.

    ```yaml
    name: "wordcount"       # friendly name for the topology

    config:                 # Topology configuration
           topology.workers: 1     # Hint for the number of workers to create
  
    spouts:                 # Spout definitions
    - id: "sentence-spout"
           className: "com.microsoft.example.RandomSentenceSpout"
           parallelism: 1      # parallelism hint

    bolts:                  # Bolt definitions
    - id: "splitter-bolt"
           className: "com.microsoft.example.SplitSentence"
           parallelism: 1

    - id: "counter-bolt"
           className: "com.microsoft.example.WordCount"
           constructorArgs:
             - 10
           parallelism: 1

    streams:                # Stream definitions
    - name: "Spout --> Splitter" # name isn't used (placeholder for logging, UI, etc.)
           from: "sentence-spout"       # The stream emitter
           to: "splitter-bolt"          # The stream consumer
           grouping:                    # Grouping type
             type: SHUFFLE

    - name: "Splitter -> Counter"
           from: "splitter-bolt"
           to: "counter-bolt"
           grouping:
             type: FIELDS
             args: ["word"]           # field(s) to group on
    ```

1. Az alábbi parancs megadásával nyissa meg az `pom.xml` alábbi változatokat:

    ```cmd
    notepad pom.xml
    ```

   1. Adja hozzá a következő új függőséget a `<dependencies>` szakaszhoz:

        ```xml
        <!-- Add a dependency on the Flux framework -->
        <dependency>
            <groupId>org.apache.storm</groupId>
            <artifactId>flux-core</artifactId>
            <version>${storm.version}</version>
        </dependency>
        ```

   1. Adja hozzá a következő beépülő modult a `<plugins>` szakaszhoz. Ez a beépülő modul kezeli a projekthez tartozó csomag (jar-fájl) létrehozását, és a csomag létrehozásakor a Flux-re jellemző átalakításokat alkalmazza.

        ```xml
        <!-- build an uber jar -->
        <plugin>
            <groupId>org.apache.maven.plugins</groupId>
            <artifactId>maven-shade-plugin</artifactId>
            <version>3.2.1</version>
            <configuration>
                <transformers>
                    <!-- Keep us from getting a "can't overwrite file error" -->
                    <transformer implementation="org.apache.maven.plugins.shade.resource.ApacheLicenseResourceTransformer" />
                    <transformer implementation="org.apache.maven.plugins.shade.resource.ServicesResourceTransformer" />
                    <!-- We're using Flux, so refer to it as main -->
                    <transformer implementation="org.apache.maven.plugins.shade.resource.ManifestResourceTransformer">
                        <mainClass>org.apache.storm.flux.Flux</mainClass>
                    </transformer>
                </transformers>
                <!-- Keep us from getting a bad signature error -->
                <filters>
                    <filter>
                        <artifact>*:*</artifact>
                        <excludes>
                            <exclude>META-INF/*.SF</exclude>
                            <exclude>META-INF/*.DSA</exclude>
                            <exclude>META-INF/*.RSA</exclude>
                        </excludes>
                    </filter>
                </filters>
            </configuration>
            <executions>
                <execution>
                    <phase>package</phase>
                    <goals>
                        <goal>shade</goal>
                    </goals>
                </execution>
            </executions>
        </plugin>
        ```

   1. Az exec Maven beépülő modul szakaszban navigáljon a elemre, `<configuration>`  >  `<mainClass>` és váltson a következőre: `${storm.topology}` `org.apache.storm.flux.Flux` . Ez a beállítás lehetővé teszi, hogy a flow a topológia helyi futtatásával kezelje a fejlesztést.

   1. A `<resources>` szakaszban adja hozzá a következőket a következőhöz: `<includes>` . Ez az XML tartalmazza azt a YAML-fájlt, amely a projekt részeként meghatározza a topológiát.

        ```xml
        <include>topology.yaml</include>
        ```

## <a name="test-the-flux-topology-locally"></a>A Flux-topológia helyi tesztelése

1. Adja meg a következő parancsot a Flux-topológia fordításához és végrehajtásához a Maven használatával:

    ```cmd
    mvn compile exec:java -Dexec.args="--local -R /topology.yaml"
    ```

    > [!WARNING]  
    > Ha a topológia a Storm 1.0.1 BITS-t használja, ez a parancs sikertelen lesz. Ezt a hibát a okozza [https://issues.apache.org/jira/browse/STORM-2055](https://issues.apache.org/jira/browse/STORM-2055) . Ehelyett [telepítse a Storm-t a fejlesztési környezetében](https://storm.apache.org/releases/current/Setting-up-development-environment.html) , és kövesse az alábbi lépéseket:
    >
    > Ha [a Storm-t telepítette a fejlesztési környezetében](https://storm.apache.org/releases/current/Setting-up-development-environment.html), a következő parancsokat használhatja helyette:
    >
    > ```cmd
    > mvn compile package
    > storm jar target/WordCount-1.0-SNAPSHOT.jar org.apache.storm.flux.Flux --local -R /topology.yaml
    > ```

    A `--local` paraméter helyi módban futtatja a topológiát a fejlesztési környezetben. A `-R /topology.yaml` paraméter a `topology.yaml` fájl erőforrását használja a jar-fájlból a topológia definiálásához.

    Ahogy fut, a topológia az indítási adatokat jeleníti meg. A következő szöveg egy példa a kimenetre:

    ```
    17:33:27 [Thread-12-count] INFO  com.microsoft.example.WordCount - Emitting a count of 56 for word snow
    17:33:27 [Thread-12-count] INFO  com.microsoft.example.WordCount - Emitting a count of 56 for word white
    17:33:27 [Thread-12-count] INFO  com.microsoft.example.WordCount - Emitting a count of 112 for word seven
    17:33:27 [Thread-16-count] INFO  com.microsoft.example.WordCount - Emitting a count of 195 for word the
    17:33:27 [Thread-30-count] INFO  com.microsoft.example.WordCount - Emitting a count of 113 for word and
    17:33:27 [Thread-30-count] INFO  com.microsoft.example.WordCount - Emitting a count of 57 for word dwarfs
    ```

    A naplózott adatok kötegei között 10 másodperces késleltetés van.

2. Hozzon létre egy új topológiai YAML a projektből.

    1. A megnyitásához írja be az alábbi parancsot `topology.xml` :

    ```cmd
    notepad resources\topology.yaml
    ```

    1. Keresse meg a következő szakaszt, és módosítsa a értékét értékre `10` `5` . Ez a módosítás a szó kiosztási kötegei közötti intervallumot módosítja 10 másodperc és 5 között.  

    ```yaml
    - id: "counter-bolt"
           className: "com.microsoft.example.WordCount"
           constructorArgs:
             - 5
           parallelism: 1  
    ```

    1. Fájl mentése másként `newtopology.yaml` .

3. A topológia futtatásához írja be a következő parancsot:

    ```cmd
    mvn exec:java -Dexec.args="--local resources/newtopology.yaml"
    ```

    Vagy ha a fejlesztési környezetében a Storm van:

    ```cmd
    storm jar target/WordCount-1.0-SNAPSHOT.jar org.apache.storm.flux.Flux --local resources/newtopology.yaml
    ```

    Ez a parancs a `newtopology.yaml` as topológia-definíciót használja. Mivel nem találtunk bele a `compile` paramétert, a Maven az előző lépésekben létrehozott projekt verzióját használja.

    A topológia elindítása után figyelje meg, hogy a kibocsátott kötegek közötti idő módosult, hogy tükrözze a értékét `newtopology.yaml` . Így láthatja, hogy a YAML-fájlon keresztül módosíthatja a konfigurációt anélkül, hogy újra le kellene fordítania a topológiát.

További információ ezekről és a Flux-keretrendszer egyéb funkcióiról: [Flux ( https://storm.apache.org/releases/current/flux.html) ](https://storm.apache.org/releases/current/flux.html).

## <a name="trident"></a>Trident

A [Trident](https://storm.apache.org/releases/current/Trident-API-Overview.html) egy magas szintű absztrakció, amelyet a Storm biztosít. Támogatja az állapot-nyilvántartó feldolgozást. A Trident elsődleges előnye, hogy garantálja, hogy a topológia összes üzenete csak egyszer lesz feldolgozva. A Trident használata nélkül a topológia csak az üzenetek feldolgozását tudja garantálni legalább egyszer. Vannak más különbségek is, például a beépített összetevők, amelyek felhasználhatók a csavarok létrehozása helyett. A boltokat kevésbé általános összetevők, például szűrők, kivetítések és függvények váltja fel.

A Trident-alkalmazásokat a Maven-projektek használatával lehet létrehozni. Ugyanazokat az alapvető lépéseket használja, mint a cikkben korábban ismertetett – csak a kód különbözik. A Trident nem használható (jelenleg) a Flux-keretrendszerrel.

További információ a Trident-ról: a [Trident API áttekintése](https://storm.apache.org/releases/current/Trident-API-Overview.html).

## <a name="next-steps"></a>Következő lépések

Megtanulta, hogyan hozhat létre Apache Storm topológiát Java használatával. Most megtudhatja, hogyan:

* [Apache Storm-topológiák üzembe helyezése és kezelése a HDInsight](apache-storm-deploy-monitor-topology-linux.md)

* [Topológiák fejlesztése a Pythonnal](apache-storm-develop-python-topology.md)

Több példát Apache Storm topológiát talál, ha a HDInsight-ben [Apache Storm példa topológiákat keres](apache-storm-example-topology.md).