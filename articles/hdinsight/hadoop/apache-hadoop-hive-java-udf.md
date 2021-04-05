---
title: Java-felhasználó által definiált függvény (UDF) Apache Hive Azure HDInsight
description: Megtudhatja, hogyan hozhat létre olyan Java-alapú, felhasználó által definiált függvényt (UDF), amely együttműködik a Apache Hiveokkal. Ez a példa az UDF a szöveges karakterláncok táblázatát kisbetűsre alakítja át.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive,hdiseo17may2017, devx-track-java
ms.date: 11/20/2019
ms.openlocfilehash: bf9c2ea544c6b510a0507b6b020f0eae0b101dc7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98946561"
---
# <a name="use-a-java-udf-with-apache-hive-in-hdinsight"></a>Java UDF használata Apache Hive HDInsight

Megtudhatja, hogyan hozhat létre olyan Java-alapú, felhasználó által definiált függvényt (UDF), amely együttműködik a Apache Hiveokkal. Az ebben a példában szereplő Java UDF a szöveges karakterláncok egy táblázatát teljes kisbetűs karakterekké alakítja.

## <a name="prerequisites"></a>Előfeltételek

* Hadoop-fürt a HDInsight-on. Lásd: Ismerkedés [a HDInsight Linux rendszeren](./apache-hadoop-linux-tutorial-get-started.md).
* [Java Developer Kit (JDK) 8-as verzió](/azure/developer/java/fundamentals/java-jdk-long-term-support)
* Az [Apache Maven](https://maven.apache.org/download.cgi) megfelelően [van telepítve](https://maven.apache.org/install.html) az Apache-ban.  A Maven egy projekt-összeállítási rendszer Java-projektekhez.
* A fürtök elsődleges tárolójának [URI-sémája](../hdinsight-hadoop-linux-information.md#URI-and-scheme) . Ez az Azure Storage-hoz készült wasb://, a Azure Data Lake Storage Gen1 Azure Data Lake Storage Gen2 vagy adl://esetében abfs://. Ha a biztonságos átvitel engedélyezve van az Azure Storage-hoz, az URI a következő lesz: `wasbs://` .  Lásd még: [biztonságos átvitel](../../storage/common/storage-require-secure-transfer.md).

* Egy szövegszerkesztő vagy Java IDE

    > [!IMPORTANT]  
    > Ha Windows-ügyfélen hozza létre a Python-fájlokat, olyan szerkesztőt kell használnia, amely a LF sort használja. Ha nem biztos abban, hogy a szerkesztője az LF vagy a CRLF-t használja-e, tekintse meg a következő témakört: [Hibaelhárítás](#troubleshooting) című rész a CR karakter eltávolításához.

## <a name="test-environment"></a>Tesztkörnyezet

A cikkben használt környezet a Windows 10 rendszert futtató számítógép volt.  A parancsok végrehajtása egy parancssorban történt, a különböző fájlok pedig a Jegyzettömb alkalmazásban lettek szerkesztve. Ennek megfelelően módosítsa a környezetét.

A parancssorba írja be az alábbi parancsokat egy működő környezet létrehozásához:

```cmd
IF NOT EXIST C:\HDI MKDIR C:\HDI
cd C:\HDI
```

## <a name="create-an-example-java-udf"></a>Példa Java UDF-hoz

1. Hozzon létre egy új Maven-projektet a következő parancs beírásával:

    ```cmd
    mvn archetype:generate -DgroupId=com.microsoft.examples -DartifactId=ExampleUDF -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```

    Ez a parancs létrehoz egy nevű könyvtárat `exampleudf` , amely tartalmazza a Maven-projektet.

2. A projekt létrehozása után törölje a `exampleudf/src/test` projekt részeként létrehozott könyvtárat a következő parancs beírásával:

    ```cmd
    cd ExampleUDF
    rmdir /S /Q "src/test"
    ```

3. A megnyitásához `pom.xml` írja be az alábbi parancsot:

    ```cmd
    notepad pom.xml
    ```

    Ezután cserélje le a meglévő `<dependencies>` bejegyzést a következő XML-fájlra:

    ```xml
    <dependencies>
        <dependency>
            <groupId>org.apache.hadoop</groupId>
            <artifactId>hadoop-client</artifactId>
            <version>2.7.3</version>
            <scope>provided</scope>
        </dependency>
        <dependency>
            <groupId>org.apache.hive</groupId>
            <artifactId>hive-exec</artifactId>
            <version>1.2.1</version>
            <scope>provided</scope>
        </dependency>
    </dependencies>
    ```

    Ezek a bejegyzések határozzák meg a Hadoop és a struktúra HDInsight 3,6-es verzióját. A [HDInsight-összetevő verziószámozási](../hdinsight-component-versioning.md) dokumentumának HDInsight által biztosított Hadoop és kaptár verzióit a következő témakörben találja:.

    Vegyen fel egy `<build>` szakaszt a `</project>` fájl végén található sor elé. Ez a szakasz a következő XML-kódot tartalmazza:

    ```xml
    <build>
        <plugins>
            <!-- build for Java 1.8. This is required by HDInsight 3.6  -->
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-compiler-plugin</artifactId>
                <version>3.3</version>
                <configuration>
                    <source>1.8</source>
                    <target>1.8</target>
                </configuration>
            </plugin>
            <!-- build an uber jar -->
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-shade-plugin</artifactId>
                <version>3.2.1</version>
                <configuration>
                    <!-- Keep us from getting a can't overwrite file error -->
                    <transformers>
                        <transformer
                                implementation="org.apache.maven.plugins.shade.resource.ApacheLicenseResourceTransformer">
                        </transformer>
                        <transformer implementation="org.apache.maven.plugins.shade.resource.ServicesResourceTransformer">
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
        </plugins>
    </build>
    ```

    Ezek a bejegyzések a projekt felépítésének módját határozzák meg. Pontosabban a projekt által használt Java-verzió, valamint a fürthöz való üzembe helyezéshez szükséges uberjar létrehozása.

    A módosítások elvégzése után mentse a fájlt.

4. Új fájl létrehozásához és megnyitásához írja be az alábbi parancsot `ExampleUDF.java` :

    ```cmd
    notepad src/main/java/com/microsoft/examples/ExampleUDF.java
    ```

    Ezután másolja és illessze be az alábbi Java-kódot az új fájlba. Ezután zárjuk be a fájlt.

    ```java
    package com.microsoft.examples;

    import org.apache.hadoop.hive.ql.exec.Description;
    import org.apache.hadoop.hive.ql.exec.UDF;
    import org.apache.hadoop.io.*;

    // Description of the UDF
    @Description(
        name="ExampleUDF",
        value="returns a lower case version of the input string.",
        extended="select ExampleUDF(deviceplatform) from hivesampletable limit 10;"
    )
    public class ExampleUDF extends UDF {
        // Accept a string input
        public String evaluate(String input) {
            // If the value is null, return a null
            if(input == null)
                return null;
            // Lowercase the input string and return it
            return input.toLowerCase();
        }
    }
    ```

    Ez a kód egy olyan UDF-t valósít meg, amely egy karakterlánc-értéket fogad el, és a karakterlánc kisbetűs verzióját adja vissza.

## <a name="build-and-install-the-udf"></a>Az UDF létrehozása és telepítése

Az alábbi parancsokban cserélje le a helyére `sshuser` a tényleges felhasználónevet, ha más. Cserélje le `mycluster` a nevet a tényleges fürt nevére.

1. Fordítsa le és csomagolja ki az UDF-t a következő parancs beírásával:

    ```cmd
    mvn compile package
    ```

    Ez a parancs létrehozza és becsomagolja az UDF-t a `exampleudf/target/ExampleUDF-1.0-SNAPSHOT.jar` fájlba.

2. A `scp` paranccsal másolja a fájlt a HDInsight-fürtre a következő parancs beírásával:

    ```cmd
    scp ./target/ExampleUDF-1.0-SNAPSHOT.jar sshuser@mycluster-ssh.azurehdinsight.net:
    ```

3. Kapcsolódjon a fürthöz az SSH használatával a következő parancs beírásával:

    ```cmd
    ssh sshuser@mycluster-ssh.azurehdinsight.net
    ```

4. Az Open SSH-munkamenetből másolja a jar-fájlt a HDInsight Storage-ba.

    ```bash
    hdfs dfs -put ExampleUDF-1.0-SNAPSHOT.jar /example/jars
    ```

## <a name="use-the-udf-from-hive"></a>Az UDF használata a kaptárból

1. A következő parancs beírásával indítsa el a Beeline-ügyfelet az SSH-munkamenetből:

    ```bash
    beeline -u 'jdbc:hive2://localhost:10001/;transportMode=http'
    ```

    Ez a parancs feltételezi, hogy a fürthöz tartozó bejelentkezési fiók alapértelmezett **rendszergazdai** jogosultságát használta.

2. Miután megérkezett a `jdbc:hive2://localhost:10001/>` parancssorba, adja meg a következő parancsot az UDF struktúrához való hozzáadásához és funkcióként való megjelenítéséhez.

    ```hiveql
    ADD JAR wasbs:///example/jars/ExampleUDF-1.0-SNAPSHOT.jar;
    CREATE TEMPORARY FUNCTION tolower as 'com.microsoft.examples.ExampleUDF';
    ```

3. Az UDF segítségével konvertálhatja a táblázatból lekért értékeket a kisbetűs karakterláncokra.

    ```hiveql
    SELECT tolower(state) AS ExampleUDF, state FROM hivesampletable LIMIT 10;
    ```

    Ez a lekérdezés kiválasztja az állapotot a táblából, átalakítja a karakterláncot az kisbetű értékre, majd megjeleníti azokat a nem módosított névvel együtt. A kimenet az alábbi szöveghez hasonlóan jelenik meg:

    ```output
    +---------------+---------------+--+
    |  exampleudf   |     state     |
    +---------------+---------------+--+
    | california    | California    |
    | pennsylvania  | Pennsylvania  |
    | pennsylvania  | Pennsylvania  |
    | pennsylvania  | Pennsylvania  |
    | colorado      | Colorado      |
    | colorado      | Colorado      |
    | colorado      | Colorado      |
    | utah          | Utah          |
    | utah          | Utah          |
    | colorado      | Colorado      |
    +---------------+---------------+--+
    ```

## <a name="troubleshooting"></a>Hibaelhárítás

A kaptár-feladatok futtatásakor előfordulhat, hogy az alábbi szöveghez hasonló hibaüzenet jelenik meg:

```output
Caused by: org.apache.hadoop.hive.ql.metadata.HiveException: [Error 20001]: An error occurred while reading or writing to your custom script. It may have crashed with an error.
```

Ezt a problémát a Python-fájlban végződő sorok okozzák. Számos Windows-szerkesztő alapértelmezés szerint a CRLF-t használja, de a Linux-alkalmazások általában a TT-t várnak.

A következő PowerShell-utasítások segítségével távolítsa el a CR-karaktereket, mielőtt feltölti a fájlt a HDInsight:

```PowerShell
# Set $original_file to the python file path
$text = [IO.File]::ReadAllText($original_file) -replace "`r`n", "`n"
[IO.File]::WriteAllText($original_file, $text)
```

## <a name="next-steps"></a>Következő lépések

A struktúra használatának egyéb módjaival kapcsolatban lásd: [Apache Hive használata a HDInsight](hdinsight-use-hive.md).

A kaptár User-Defined functions szolgáltatással kapcsolatos további információkért tekintse meg a kaptár wiki [Apache Hive operátorok és User-Defined functions](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF) című szakaszát a Apache.org címen.