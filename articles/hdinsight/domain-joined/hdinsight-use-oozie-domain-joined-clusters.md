---
title: Apache Oozie-munkafolyamatok & Enterprise Security – Azure HDInsight
description: Biztonságos Apache Oozie-munkafolyamatok az Azure HDInsight Enterprise Security Package használatával. Ismerje meg, hogyan határozhat meg egy Oozie-munkafolyamatot, és hogyan küldhet el egy Oozie-feladatot.
ms.service: hdinsight
ms.topic: conceptual
ms.custom: seoapr2020
ms.date: 05/14/2020
ms.openlocfilehash: dc20a258028bb76351ae70362234e7c4c8c741a5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "101699402"
---
# <a name="run-apache-oozie-in-azure-hdinsight-clusters-with-enterprise-security-package"></a>Apache Oozie futtatása az Azure HDInsight-fürtökben Enterprise Security Package

Az Apache Oozie egy munkafolyamat-és koordinációs rendszer, amely Apache Hadoop feladatokat kezel. A Oozie integrálva van a Hadoop-verembe, és a következő feladatokat támogatja:

- Apache MapReduce
- Apache Pig
- Apache Hive
- Apache Sqoop

A Oozie segítségével a rendszerre vonatkozó feladatokat is ütemezhet, például a Java-programokat vagy a rendszerhéj-parancsfájlokat.

## <a name="prerequisite"></a>Előfeltétel

Enterprise Security Package (ESP) Azure HDInsight Hadoop-fürt. Lásd: [HDInsight-fürtök beállítása az ESP-vel](./apache-domain-joined-configure-using-azure-adds.md).

> [!NOTE]  
> Az Oozie nem ESP-fürtökön történő használatáról az [Apache Oozie-munkafolyamatok használata Linux-alapú Azure-HDInsight](../hdinsight-use-oozie-linux-mac.md)című cikkben talál részletes tájékoztatást.

## <a name="connect-to-an-esp-cluster"></a>Kapcsolódás ESP-fürthöz

A Secure Shell (SSH) szolgáltatással kapcsolatos további információkért lásd: [Kapcsolódás a HDInsight (Hadoop) az SSH használatával](../hdinsight-hadoop-linux-use-ssh-unix.md).

1. Kapcsolódjon a HDInsight-fürthöz az SSH használatával:

    ```bash
    ssh [DomainUserName]@<clustername>-ssh.azurehdinsight.net
    ```

1. A sikeres Kerberos-hitelesítés ellenőrzéséhez használja az `klist` parancsot. Ha nem, használja `kinit` a parancsot a Kerberos-hitelesítés elindításához.

1. Jelentkezzen be a HDInsight-átjáróba a Azure Data Lake Storage eléréséhez szükséges OAuth-token regisztrálásához:

    ```bash
    curl -I -u [DomainUserName@Domain.com]:[DomainUserPassword] https://<clustername>.azurehdinsight.net
    ```

    Az **200** -es állapot-reagálási kód sikeres regisztrációt jelez. Ha nem engedélyezett választ kap, ellenőrizze a felhasználónevet és a jelszót (például 401).

## <a name="define-the-workflow"></a>A munkafolyamat definiálása

A Oozie munkafolyamat-definíciókat Apache Hadoop Process Definition Language (hPDL) nyelven írták. a hPDL egy XML-folyamat definíciós nyelve. A munkafolyamat definiálásához hajtsa végre a következő lépéseket:

1. Tartományi felhasználó munkaterületének beállítása:

   ```bash
   hdfs dfs -mkdir /user/<DomainUser>
   cd /home/<DomainUserPath>
   cp /usr/hdp/<ClusterVersion>/oozie/doc/oozie-examples.tar.gz .
   tar -xvf oozie-examples.tar.gz
   hdfs dfs -put examples /user/<DomainUser>/
   ```

   Cserélje le `DomainUser` a nevet a tartomány felhasználónevére.
   Cserélje le a helyére a `DomainUserPath` tartományi felhasználó kezdőkönyvtárának elérési útját.
   Cserélje le `ClusterVersion` a-t a fürt adatplatformjának verziójára.

2. Új fájl létrehozásához és szerkesztéséhez használja az alábbi utasítást:

   ```bash
   nano workflow.xml
   ```

3. A nano Editor megnyitása után írja be a következő XML-fájlt a fájl tartalmába:

    ```xml
    <?xml version="1.0" encoding="UTF-8"?>
    <workflow-app xmlns="uri:oozie:workflow:0.4" name="map-reduce-wf">
       <credentials>
          <credential name="metastore_token" type="hcat">
             <property>
                <name>hcat.metastore.uri</name>
                <value>thrift://<active-headnode-name>-<clustername>.<Domain>.com:9083</value>
             </property>
             <property>
                <name>hcat.metastore.principal</name>
                <value>hive/_HOST@<Domain>.COM</value>
             </property>
          </credential>
          <credential name="hs2-creds" type="hive2">
             <property>
                <name>hive2.server.principal</name>
                <value>${jdbcPrincipal}</value>
             </property>
             <property>
                <name>hive2.jdbc.url</name>
                <value>${jdbcURL}</value>
             </property>
          </credential>
       </credentials>
       <start to="mr-test" />
       <action name="mr-test">
          <map-reduce>
             <job-tracker>${jobTracker}</job-tracker>
             <name-node>${nameNode}</name-node>
             <prepare>
                <delete path="${nameNode}/user/${wf:user()}/examples/output-data/mrresult" />
             </prepare>
             <configuration>
                <property>
                   <name>mapred.job.queue.name</name>
                   <value>${queueName}</value>
                </property>
                <property>
                   <name>mapred.mapper.class</name>
                   <value>org.apache.oozie.example.SampleMapper</value>
                </property>
                <property>
                   <name>mapred.reducer.class</name>
                   <value>org.apache.oozie.example.SampleReducer</value>
                </property>
                <property>
                   <name>mapred.map.tasks</name>
                   <value>1</value>
                </property>
                <property>
                   <name>mapred.input.dir</name>
                   <value>/user/${wf:user()}/${examplesRoot}/input-data/text</value>
                </property>
                <property>
                   <name>mapred.output.dir</name>
                   <value>/user/${wf:user()}/${examplesRoot}/output-data/mrresult</value>
                </property>
             </configuration>
          </map-reduce>
          <ok to="myHive2" />
          <error to="fail" />
       </action>
       <action name="myHive2" cred="hs2-creds">
          <hive2 xmlns="uri:oozie:hive2-action:0.2">
             <job-tracker>${jobTracker}</job-tracker>
             <name-node>${nameNode}</name-node>
             <jdbc-url>${jdbcURL}</jdbc-url>
             <script>${hiveScript2}</script>
             <param>hiveOutputDirectory2=${hiveOutputDirectory2}</param>
          </hive2>
          <ok to="myHive" />
          <error to="fail" />
       </action>
       <action name="myHive" cred="metastore_token">
          <hive xmlns="uri:oozie:hive-action:0.2">
             <job-tracker>${jobTracker}</job-tracker>
             <name-node>${nameNode}</name-node>
             <configuration>
                <property>
                   <name>mapred.job.queue.name</name>
                   <value>${queueName}</value>
                </property>
             </configuration>
             <script>${hiveScript1}</script>
             <param>hiveOutputDirectory1=${hiveOutputDirectory1}</param>
          </hive>
          <ok to="end" />
          <error to="fail" />
       </action>
       <kill name="fail">
          <message>Oozie job failed, error message[${wf:errorMessage(wf:lastErrorNode())}]</message>
       </kill>
       <end name="end" />
    </workflow-app>
    ```

4. Cserélje le a `clustername` nevet a fürt nevére.

5. A fájl mentéséhez válassza a **CTRL + X billentyűkombinációt**. Adja meg az **Y** értéket. Ezután kattintson az **ENTER** gombra.

    A munkafolyamat két részre oszlik:

   - **Hitelesítőadat.** Ez a szakasz a Oozie műveletek hitelesítéséhez használt hitelesítő adatokat veszi figyelembe:

     Ez a példa a kaptár-műveletek hitelesítését használja. További információ: [művelet hitelesítése](https://oozie.apache.org/docs/4.2.0/DG_ActionAuthentication.html).

     A hitelesítőadat-szolgáltatás lehetővé teszi, hogy a Oozie műveletek megszemélyesítsék a felhasználót a Hadoop-szolgáltatások eléréséhez.

   - **Művelet.** Ez a szakasz három műveletet tartalmaz: Map-csökkentse, kaptár Server 2 és a kaptár Server 1:

     - A Térkép-csökkentés művelet egy példát mutat be egy Oozie-csomagból a Map-csökkentse, amely az összesített szavak darabszámát adja vissza.

     - A kaptár-kiszolgáló 2 és a kaptár Server 1 műveletek futtatnak egy lekérdezést a HDInsight által biztosított minta struktúra-táblán.

     A kaptár-műveletek a hitelesítő adatok szakaszban meghatározott hitelesítő adatokat használják a hitelesítéshez a `cred` művelet elemben található kulcsszó használatával.

6. A következő parancs használatával másolja a fájlt a következőre `workflow.xml` `/user/<domainuser>/examples/apps/map-reduce/workflow.xml` :

    ```bash
    hdfs dfs -put workflow.xml /user/<domainuser>/examples/apps/map-reduce/workflow.xml
    ```

7. Cserélje le a `domainuser` nevet a tartomány felhasználónevére.

## <a name="define-the-properties-file-for-the-oozie-job"></a>A Oozie-feladatokhoz tartozó tulajdonságok fájljának megadása

1. Az alábbi utasítás használatával új fájlt hozhat létre és szerkeszthet a feladatok tulajdonságaihoz:

    ```bash
    nano job.properties
    ```

2. A nano Editor megnyitása után használja az alábbi XML-fájlt a fájl tartalmának:

   ```bash
   nameNode=adl://home
   jobTracker=headnodehost:8050
   queueName=default
   examplesRoot=examples
   oozie.wf.application.path=${nameNode}/user/[domainuser]/examples/apps/map-reduce/workflow.xml
   hiveScript1=${nameNode}/user/${user.name}/countrowshive1.hql
   hiveScript2=${nameNode}/user/${user.name}/countrowshive2.hql
   oozie.use.system.libpath=true
   user.name=[domainuser]
   jdbcPrincipal=hive/<active-headnode-name>.<Domain>.com@<Domain>.COM
   jdbcURL=[jdbcurlvalue]
   hiveOutputDirectory1=${nameNode}/user/${user.name}/hiveresult1
   hiveOutputDirectory2=${nameNode}/user/${user.name}/hiveresult2
   ```

   - Akkor használja az `adl://home` URI- `nameNode` t a tulajdonsághoz, ha Azure Data Lake Storage Gen1 elsődleges fürtös tárolóként. Ha Azure-Blob Storage használ, váltson a következőre: `wasb://home` . Ha Azure Data Lake Storage Gen2 használ, váltson a következőre: `abfs://home` .
   - Cserélje le a `domainuser` nevet a tartomány felhasználónevére.
   - Cserélje le a helyére `ClusterShortName` a fürt rövid nevét. Ha például a fürt neve https:// *[example link]* sechadoopcontoso.azurehdisnight.net, a a `clustershortname` fürt első hat karaktere: **sechad**.
   - Cserélje le a `jdbcurlvalue` elemet a struktúra konfigurációjának JDBC URL-címére. Ilyen például a JDBC: hive2://headnodehost: 10001/; transportMode = http.
   - A fájl mentéséhez válassza a CTRL + X billentyűkombinációt, írja be a következőt: `Y` , majd válassza az **ENTER** billentyűt.

   Ennek a tulajdonságnak a Oozie-feladatok futtatásakor helyileg jelen kell lennie.

## <a name="create-custom-hive-scripts-for-oozie-jobs"></a>Egyéni kaptár-parancsfájlok létrehozása a Oozie-feladatokhoz

A következő részekben látható módon létrehozhatja az 1. és a kaptár-kiszolgáló 2. struktúrájának két struktúrás parancsfájlját is.

### <a name="hive-server-1-file"></a>1. struktúra-kiszolgáló fájl

1. Hozzon létre és szerkesszen egy fájlt a kaptár Server 1 műveleteihez:

    ```bash
    nano countrowshive1.hql
    ```

2. Hozza létre a parancsfájlt:

    ```sql
    INSERT OVERWRITE DIRECTORY '${hiveOutputDirectory1}'
    ROW FORMAT DELIMITED FIELDS TERMINATED BY ','
    select devicemake from hivesampletable limit 2;
    ```

3. Mentse a fájlt Apache Hadoop elosztott fájlrendszerba (HDFS):

    ```bash
    hdfs dfs -put countrowshive1.hql countrowshive1.hql
    ```

### <a name="hive-server-2-file"></a>2. struktúra-kiszolgáló fájl

1. Hozzon létre és szerkesszen egy mezőt a kaptár-kiszolgáló 2 műveleteihez:

    ```bash
    nano countrowshive2.hql
    ```

2. Hozza létre a parancsfájlt:

    ```sql
    INSERT OVERWRITE DIRECTORY '${hiveOutputDirectory1}' 
    ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' 
    select devicemodel from hivesampletable limit 2;
    ```

3. Mentse a fájlt a HDFS:

    ```bash
    hdfs dfs -put countrowshive2.hql countrowshive2.hql
    ```

## <a name="submit-oozie-jobs"></a>Oozie-feladatok elküldése

Az ESP-fürtökhöz tartozó Oozie-feladatok elküldése olyan, mint az Oozie-feladatok nem ESP-fürtökbe való elküldése.

További információ: az [Apache Oozie és a Apache Hadoop használata a munkafolyamat definiálásához és futtatásához Linux-alapú Azure-HDInsight](../hdinsight-use-oozie-linux-mac.md).

## <a name="results-from-an-oozie-job-submission"></a>Oozie-feladatok beküldésének eredményei

Oozie feladatok futnak a felhasználó számára. Így mind a Apache Hadoop FONÁL, mind az Apache Ranger-napló a megszemélyesített felhasználóként futtatott feladatokat mutatja. A Oozie-feladatok parancssori felületének kimenete a következő kódhoz hasonlít:

```output
Job ID : 0000015-180626011240801-oozie-oozi-W
------------------------------------------------------------------------------------------------
Workflow Name : map-reduce-wf
App Path      : adl://home/user/alicetest/examples/apps/map-reduce/wf.xml
Status        : SUCCEEDED
Run           : 0
User          : alicetest
Group         : -
Created       : 2018-06-26 19:25 GMT
Started       : 2018-06-26 19:25 GMT
Last Modified : 2018-06-26 19:30 GMT
Ended         : 2018-06-26 19:30 GMT
CoordAction ID: -

Actions
------------------------------------------------------------------------------------------------
ID                        Status    Ext ID            ExtStatus                 ErrCode
------------------------------------------------------------------------------------------------
0000015-180626011240801-oozie-oozi-W@:start:    OK    -                         OK             -
------------------------------------------------------------------------------------------------
0000015-180626011240801-oozie-oozi-W@mr-test    OK    job_1529975666160_0051    SUCCEEDED      -
------------------------------------------------------------------------------------------------
0000015-180626011240801-oozie-oozi-W@myHive2    OK    job_1529975666160_0053    SUCCEEDED      -
------------------------------------------------------------------------------------------------
0000015-180626011240801-oozie-oozi-W@myHive    OK     job_1529975666160_0055    SUCCEEDED      -
------------------------------------------------------------------------------------------------
0000015-180626011240801-oozie-oozi-W@end       OK     -                         OK             -
-----------------------------------------------------------------------------------------------
```

A (2) struktúra-kiszolgáló két művelete a Oozie a felhasználó műveletét futtató naplókat jeleníti meg. A Ranger és a fonal nézetei csak a fürt rendszergazdája számára láthatók.

## <a name="configure-user-authorization-in-oozie"></a>Felhasználói hitelesítés konfigurálása a Oozie-ben

A Oozie önmagában olyan felhasználói engedélyezési konfigurációval rendelkezik, amely letilthatja a felhasználók számára más felhasználói feladatok leállítását vagy törlését. A konfiguráció engedélyezéséhez állítsa be a következőt: `oozie.service.AuthorizationService.security.enabled` `true` . 

További információ: az [Apache Oozie telepítése és konfigurálása](https://oozie.apache.org/docs/3.2.0-incubating/AG_Install.html).

Olyan összetevők esetében, mint például a kaptár Server 1, ahol a Ranger beépülő modul nem érhető el vagy nem támogatott, csak a durva HDFS engedélyezés lehetséges. A részletes engedélyezés csak a Ranger beépülő moduljain keresztül érhető el.

## <a name="get-the-oozie-web-ui"></a>A Oozie webes felhasználói felületének beolvasása

A Oozie webes felhasználói felülete webes nézetet biztosít a fürt Oozie-feladatai állapotához. A webes felhasználói felület beszerzéséhez hajtsa végre az alábbi lépéseket az ESP-fürtökben:

1. Adjon hozzá egy [peremhálózati csomópontot](../hdinsight-apps-use-edge-node.md) , és engedélyezze az [SSH Kerberos-hitelesítést](../hdinsight-hadoop-linux-use-ssh-unix.md).

2. A [Oozie webes felhasználói felületének](../hdinsight-use-oozie-linux-mac.md) lépéseit követve engedélyezze az SSH-bújtatást a peremhálózati csomóponton, és nyissa meg a webes felhasználói felületet.

## <a name="next-steps"></a>Következő lépések

- Az [Apache Oozie és a Apache Hadoop használatával megadhatja és futtathatja a munkafolyamatokat a Linux-alapú Azure-HDInsight](../hdinsight-use-oozie-linux-mac.md).
- [Kapcsolódás HDInsight (Apache Hadoop) SSH használatával](../hdinsight-hadoop-linux-use-ssh-unix.md#authentication-domain-joined-hdinsight).
