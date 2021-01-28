---
title: Lekérdezés Apache Hive a JDBC-illesztőn keresztül – Azure HDInsight
description: Egy Java-alkalmazásból származó JDBC-illesztőprogrammal küldhet Apache Hive lekérdezéseket a HDInsight Hadoop. Programozott módon és a mókus SQL-ügyfélről is kapcsolódhat.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive,hdiseo17may2017,seoapr2020
ms.date: 04/20/2020
ms.openlocfilehash: d23b376384262c208fed70306e62634592d0b46b
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/28/2021
ms.locfileid: "98946763"
---
# <a name="query-apache-hive-through-the-jdbc-driver-in-hdinsight"></a>Az Apache Hive lekérdezése a HDInsight JDBC-illesztőjén keresztül

[!INCLUDE [ODBC-JDBC-selector](../../../includes/hdinsight-selector-odbc-jdbc.md)]

Ismerje meg, hogyan használható a JDBC-illesztőprogram Java-alkalmazásokból. Apache Hive-lekérdezések elküldése az Azure HDInsight Apache Hadoop. A jelen dokumentumban található információk azt mutatják be, hogyan lehet programozott módon és a mókus SQL-ügyfélről csatlakozni.

További információ a kaptár JDBC felületéről: [HiveJDBCInterface](https://cwiki.apache.org/confluence/display/Hive/HiveJDBCInterface).

## <a name="prerequisites"></a>Előfeltételek

* An méretű HDInsight Hadoop-fürt. Az első lépésekhez tekintse meg [Az Azure HDInsight megismerését](apache-hadoop-linux-tutorial-get-started.md)ismertető témakört. Győződjön meg arról, hogy a szolgáltatás HiveServer2 fut.
* A [Java Developer Kit (JDK) 11-es](https://www.oracle.com/technetwork/java/javase/downloads/jdk11-downloads-5066655.html) vagy újabb verziója.
* [Mókus SQL](http://squirrel-sql.sourceforge.net/). A mókus egy JDBC ügyfélalkalmazás.

## <a name="jdbc-connection-string"></a>JDBC-kapcsolati sztring

Az Azure-beli HDInsight-fürtök JDBC-kapcsolatai az 443-as porton keresztül történnek. A forgalmat a TLS/SSL protokoll védi. A fürtök mögött található nyilvános átjáró átirányítja a forgalmat arra a portra, amelyet a HiveServer2 ténylegesen figyel. A következő kapcsolódási sztring a HDInsight használandó formátumot mutatja:

```http
    jdbc:hive2://CLUSTERNAME.azurehdinsight.net:443/default;transportMode=http;ssl=true;httpPath=/hive2
```

Cserélje le a `CLUSTERNAME` kifejezést a HDInsight-fürt nevére.

Vagy a Ambari felhasználói felületén keresztül is lekérheti a **> kaptár > konfigurációk > Advanced**.

![JDBC-kapcsolatok karakterláncának beolvasása a Ambari használatával](./media/apache-hadoop-connect-hive-jdbc-driver/hdinsight-get-connection-string-through-ambari.png)

### <a name="host-name-in-connection-string"></a>Állomásnév a kapcsolódó karakterláncban

A (z) "CLUSTERNAME.azurehdinsight.net" állomásnév a kapcsolatok karakterláncában megegyezik a fürt URL-címével. Azure Portal használatával szerezheti be.

### <a name="port-in-connection-string"></a>Port a csatlakozási karakterláncban

Az 443-es **portot** csak az Azure-beli virtuális hálózaton kívüli helyekről lehet csatlakozni a fürthöz. A HDInsight egy felügyelt szolgáltatás, ami azt jelenti, hogy a fürttel létesített összes kapcsolat biztonságos átjárón keresztül lett felügyelve. Közvetlenül a 10001-as vagy a 10000-es porton nem tud csatlakozni a 2. HiveServer. Ezek a portok nem jelennek meg kívülre.

## <a name="authentication"></a>Hitelesítés

A kapcsolódáskor a hitelesítéshez használja a HDInsight-fürt rendszergazdájának nevét és jelszavát. A JDBC-ügyfelektől, például a mókus SQL-től adja meg a rendszergazdai nevet és jelszót az ügyfél beállításai között.

Egy Java-alkalmazásból a kapcsolatok létrehozásakor a nevet és a jelszót kell használnia. Például a következő Java-kód új kapcsolatokat nyit meg:

```java
DriverManager.getConnection(connectionString,clusterAdmin,clusterPassword);
```

## <a name="connect-with-squirrel-sql-client"></a>Kapcsolat a mókus SQL-ügyféllel

A mókus SQL egy JDBC-ügyfél, amely a kaptár-lekérdezések távoli futtatására használható a HDInsight-fürttel. A következő lépések azt feltételezik, hogy már telepítette a mókus SQL-t.

1. Hozzon létre egy könyvtárat, amely bizonyos, a fürtből másolandó fájlokat tartalmaz.

2. A következő parancsfájlban cserélje le a parancsot a `sshuser` fürthöz tartozó SSH-felhasználói fiók nevére.  Cserélje le `CLUSTERNAME` a nevet a HDInsight-fürt nevére.  A parancssorban módosítsa a munkahelyi könyvtárat az előző lépésben létrehozottra, majd írja be a következő parancsot a fájlok HDInsight-fürtből való másolásához:

    ```cmd
    scp sshuser@CLUSTERNAME-ssh.azurehdinsight.net:/usr/hdp/current/hadoop-client/{hadoop-auth.jar,hadoop-common.jar,lib/log4j-*.jar,lib/slf4j-*.jar,lib/curator-*.jar} .

    scp sshuser@CLUSTERNAME-ssh.azurehdinsight.net:/usr/hdp/current/hive-client/lib/{commons-codec*.jar,commons-logging-*.jar,hive-*-*.jar,httpclient-*.jar,httpcore-*.jar,libfb*.jar,libthrift-*.jar} .
    ```

3. Indítsa el a mókus SQL-alkalmazást. Az ablak bal oldalán válassza az **illesztőprogramok** lehetőséget.

    ![Illesztőprogramok lap az ablak bal oldalán](./media/apache-hadoop-connect-hive-jdbc-driver/hdinsight-squirreldrivers.png)

4. Az **illesztőprogramok** párbeszédpanel tetején található ikonok közül válassza az **+** ikont az illesztőprogram létrehozásához.

    ![Mókus SQL Application-illesztőprogramok ikon](./media/apache-hadoop-connect-hive-jdbc-driver/hdinsight-driversicons.png)

5. Az illesztőprogram hozzáadása párbeszédpanelen adja meg a következő adatokat:

    |Tulajdonság | Érték |
    |---|---|
    |Név|Hive|
    |Példa URL-címére|`jdbc:hive2://localhost:443/default;transportMode=http;ssl=true;httpPath=/hive2`|
    |Extra osztály elérési útja|A **Hozzáadás** gomb használatával adja hozzá a korábban letöltött összes jar-fájlt.|
    |Osztály neve|org. Apache. kaptár. JDBC. HiveDriver|

   ![illesztőprogram-párbeszédpanel hozzáadása paraméterekkel](./media/apache-hadoop-connect-hive-jdbc-driver/hdinsight-add-driver.png)

   A beállítások mentéséhez kattintson **az OK gombra** .

6. A mókus SQL-ablak bal oldalán válassza az **aliasok** lehetőséget. Ezután válassza a **+** ikont a kapcsolódási alias létrehozásához.

    !["Mókus SQL új alias párbeszédpanel hozzáadása"](./media/apache-hadoop-connect-hive-jdbc-driver/hdinsight-new-aliases.png)

7. Használja az alábbi értékeket az **alias hozzáadása** párbeszédpanelen:

    |Tulajdonság |Érték |
    |---|---|
    |Név|Struktúra a HDInsight|
    |Illesztő|A **struktúra** -illesztőprogram kiválasztásához használja a legördülő listát.|
    |URL-cím|`jdbc:hive2://CLUSTERNAME.azurehdinsight.net:443/default;transportMode=http;ssl=true;httpPath=/hive2`. Cserélje le a **CLUSTERNAME** kifejezést a HDInsight-fürt nevére.|
    |Felhasználónév|A fürt bejelentkezési fiókjának neve a HDInsight-fürthöz. Az alapértelmezett érték a **rendszergazda**.|
    |Jelszó|A fürt bejelentkezési fiókjának jelszava.|

    ![alias hozzáadása párbeszédpanel paraméterekkel](./media/apache-hadoop-connect-hive-jdbc-driver/hdinsight-addalias-dialog.png)

    > [!IMPORTANT]
    > A **teszt** gomb használatával ellenőrizze, hogy a kapcsolódás működik-e. Ha a **Csatlakozás a következőhöz: struktúra a HDInsight** párbeszédpanelen megjelenik, válassza a **Csatlakozás** lehetőséget a teszt végrehajtásához. Ha a teszt sikeres, a **sikeres kapcsolatok** párbeszédpanel jelenik meg. Ha hiba történik, tekintse meg a [Hibaelhárítás](#troubleshooting)című témakört.

    A kapcsolódási alias mentéséhez használja az **alias hozzáadása** párbeszédpanel alján található **OK** gombot.

8. A mókus SQL-oldal tetején található **Kapcsolódás** legördülő listáról válassza a **kaptár elemet a HDInsight**. Ha a rendszer kéri, válassza a **Csatlakozás** lehetőséget.

    ![paraméterekkel rendelkező kapcsolatok párbeszédpanel](./media/apache-hadoop-connect-hive-jdbc-driver/hdinsight-connect-dialog.png)

9. Ha csatlakoztatva van, adja meg a következő lekérdezést az SQL-lekérdezés párbeszédpanelen, majd válassza a **Futtatás** ikont (egy futó személy). Az eredmények területnek a lekérdezés eredményét kell megjelenítenie.

    ```hiveql
    select * from hivesampletable limit 10;
    ```

    ![SQL-lekérdezési párbeszédpanel, beleértve az eredményeket](./media/apache-hadoop-connect-hive-jdbc-driver/hdinsight-sqlquery-dialog.png)

## <a name="connect-from-an-example-java-application"></a>Csatlakozási példa Java-alkalmazásból

A (z) HDInsight található struktúra lekérdezése Java-ügyféllel című példa a következő címen érhető el: [https://github.com/Azure-Samples/hdinsight-java-hive-jdbc](https://github.com/Azure-Samples/hdinsight-java-hive-jdbc) . A minta létrehozásához és futtatásához kövesse az adattár utasításait.

## <a name="troubleshooting"></a>Hibaelhárítás

### <a name="unexpected-error-occurred-attempting-to-open-an-sql-connection"></a>Váratlan hiba történt az SQL-kapcsolatok megnyitására tett kísérlet során.

**Tünetek**: a 3,3-es vagy annál újabb verziójú HDInsight-fürtökhöz való kapcsolódáskor hibaüzenetet kaphat, ha váratlan hiba történt. A hiba verem-nyomkövetése a következő sorokkal kezdődik:

```java
java.util.concurrent.ExecutionException: java.lang.RuntimeException: java.lang.NoSuchMethodError: org.apache.commons.codec.binary.Base64.<init>(I)V
at java.util.concurrent.FutureTas...(FutureTask.java:122)
at java.util.concurrent.FutureTask.get(FutureTask.java:206)
```

**OK**: ezt a hibát a mókushoz tartozó Commons-codec. jar fájl régebbi verziója okozta.

**Megoldás**: a hiba elhárításához kövesse az alábbi lépéseket:

1. Lépjen ki a Mókusból, majd nyissa meg azt a könyvtárat, ahol a mókus telepítve van a rendszeren `C:\Program Files\squirrel-sql-4.0.0\lib` . A mókus könyvtárában a könyvtár alatt `lib` cserélje le a meglévő Commons-codec. jar fájlt a HDInsight-fürtről letöltöttre.

1. Indítsa újra a mókust. A hiba többé nem fordulhat elő, amikor a HDInsight-beli struktúrához csatlakozik.

### <a name="connection-disconnected-by-hdinsight"></a>Kapcsolat leválasztva a HDInsight

**Tünetek**: Ha nagy mennyiségű (több GBS) adatmennyiséget szeretne LETÖLTENI a JDBC/ODBC-n keresztül, a kapcsolat a HDInsight váratlanul megszakad a letöltés során.

**OK**: ezt a hibát az átjáró-csomópontok korlátozásai okozzák. Az adatok JDBC/ODBC-ből való lekérése során minden adatnak át kell haladnia az átjáró csomópontján. Az átjárók azonban nem tudnak nagy mennyiségű adatokat letölteni, így előfordulhat, hogy az átjáró lezárta a kapcsolatokat, ha az nem tudja kezelni a forgalmat.

**Megoldás**: ne használja a JDBC/ODBC-illesztőt nagy mennyiségű adatforrás letöltéséhez. Adatok másolása közvetlenül a blob Storage-ból.

## <a name="next-steps"></a>Következő lépések

Most, hogy megismerte, hogyan használhatja a JDBC-t a kaptár használatára, az alábbi hivatkozásokat követve megismerheti az Azure HDInsight való munkavégzés egyéb módszereit.

* Az [Azure HDInsight-ban a Microsoft Power BI Apache Hivei az adatmegjelenítést](apache-hadoop-connect-hive-power-bi.md).
* [Interaktív lekérdezési struktúra-adatmegjelenítés az Azure HDInsight Power BIával](../interactive-query/apache-hadoop-connect-hive-power-bi-directquery.md).
* [Az Excel és a HDInsight összekötése a Microsoft kaptár ODBC-illesztővel](apache-hadoop-connect-excel-hive-odbc-driver.md).
* Az [Excel Apache Hadoop Power Query használatával csatlakoztatható](apache-hadoop-connect-excel-power-query.md).
* [Apache Hive használata a HDInsight](hdinsight-use-hive.md)
* [Az Apache Pig és a HDInsight használata](../use-pig.md)
* [MapReduce-feladatok használata a HDInsightban](hdinsight-use-mapreduce.md)
