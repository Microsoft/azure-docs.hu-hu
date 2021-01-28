---
title: 'Gyors útmutató: az Azure HDInsight Apache Hive az Apache Zeppelin-vel'
description: Ebből a rövid útmutatóból megtudhatja, hogyan futtathat Apache Hive lekérdezéseket az Apache Zeppelin használatával.
ms.service: hdinsight
ms.topic: quickstart
ms.custom: hdinsightactive
ms.date: 12/03/2019
ms.openlocfilehash: 1199fee82532eacf1f0ef41b877b970d7d9f2cf5
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/28/2021
ms.locfileid: "98941056"
---
# <a name="quickstart-execute-apache-hive-queries-in-azure-hdinsight-with-apache-zeppelin"></a>Gyors útmutató: Apache Hive lekérdezések végrehajtása az Azure HDInsight az Apache Zeppelin-vel

Ebből a rövid útmutatóból megtudhatja, hogyan futtathat [Apache Hive](https://hive.apache.org/) -lekérdezéseket az Azure HDInsight-ben az Apache Zeppelin használatával. A HDInsight interaktív lekérdezési fürtök olyan [Apache Zeppelin](https://zeppelin.apache.org/) -jegyzetfüzeteket tartalmaznak, amelyekkel interaktív struktúra-lekérdezéseket futtathat.

Ha még nincs Azure-előfizetése, kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Előfeltételek

An méretű HDInsight interaktív lekérdezési fürt. Lásd: [fürt létrehozása](../hadoop/apache-hadoop-linux-tutorial-get-started.md) HDInsight-fürt létrehozásához.  Győződjön meg arról, hogy az **interaktív lekérdezési** fürt típusa van kiválasztva.

## <a name="create-an-apache-zeppelin-note"></a>Apache Zeppelin-Megjegyzés létrehozása

1. Cserélje le a `CLUSTERNAME` nevet a fürt nevére a következő URL-címben `https://CLUSTERNAME.azurehdinsight.net/zeppelin` . Ezután írja be az URL-címet egy böngészőben.

2. Adja meg a fürt bejelentkezési felhasználónevét és jelszavát. A Zeppelin lapon létrehozhat egy új megjegyzést, vagy megnyithatja a meglévő megjegyzéseket. A **HiveSample** tartalmaz néhány minta kaptár-lekérdezést.  

    ![HDInsight interaktív lekérdezés Zeppelin](./media/hdinsight-connect-hive-zeppelin/hdinsight-hive-zeppelin.png)

3. Válassza az **új Megjegyzés létrehozása** lehetőséget.

4. Az **új Megjegyzés létrehozása** párbeszédpanelen írja be vagy válassza ki a következő értékeket:

    - Megjegyzés neve: adja meg a jegyzet nevét.
    - Alapértelmezett értelmező: válassza a **JDBC** elemet a legördülő listából.

5. Válassza a **Létrehozás Megjegyzés** lehetőséget.

6. Adja meg a következő kaptár-lekérdezést a Code (kód) szakaszban, majd nyomja le a **SHIFT + ENTER** billentyűkombinációt:

    ```hive
    %jdbc(hive)
    show tables
    ```

    ![A HDInsight interaktív lekérdezési Zeppelin lekérdezéseket futtat](./media/hdinsight-connect-hive-zeppelin/hdinsight-hive-zeppelin-query.png)

    Az első sorban található **% JDBC (kaptár)** utasítás azt jelzi, hogy a jegyzetfüzet a kaptár JDBC-értelmező használatát mutatja be.

    A lekérdezés egy **hivesampletable** nevű struktúrás táblát ad vissza.

    A következő két további, a **hivesampletable**-ben futtatott kaptár-lekérdezést hajt végre:

    ```hive
    %jdbc(hive)
    select * from hivesampletable limit 10

    %jdbc(hive)
    select ${group_name}, count(*) as total_count
    from hivesampletable
    group by ${group_name=market,market|deviceplatform|devicemake}
    limit ${total_count=10}
    ```

    A hagyományos struktúrához képest a lekérdezés eredményeinek gyorsabban kell visszatérniük.

### <a name="additional-examples"></a>További példák

1. Hozzon létre egy táblát. Hajtsa végre az alábbi kódot a Zeppelin jegyzetfüzetben:

    ```hql
    %jdbc(hive)
    CREATE EXTERNAL TABLE log4jLogs (
        t1 string,
        t2 string,
        t3 string,
        t4 string,
        t5 string,
        t6 string,
        t7 string)
    ROW FORMAT DELIMITED
    FIELDS TERMINATED BY ' '
    STORED AS TEXTFILE;
    ```

1. Betöltés az új táblába. Hajtsa végre az alábbi kódot a Zeppelin jegyzetfüzetben:

    ```hql
    %jdbc(hive)
    LOAD DATA
    INPATH 'wasbs:///example/data/sample.log'
    INTO TABLE log4jLogs;
    ```

1. Egyetlen rekord beszúrása. Hajtsa végre az alábbi kódot a Zeppelin jegyzetfüzetben:

    ```hql
    %jdbc(hive)
    INSERT INTO TABLE log4jLogs2
    VALUES ('A', 'B', 'C', 'D', 'E', 'F', 'G');
    ```

További szintaxisért tekintse át a [kaptár nyelvi útmutatóját](https://cwiki.apache.org/confluence/display/Hive/LanguageManual) .

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

A gyors üzembe helyezés befejezése után érdemes lehet törölni a fürtöt. A HDInsight az adatait az Azure Storage tárolja, így biztonságosan törölheti a fürtöt, ha az nincs használatban. A HDInsight-fürtökért is fizetnie kell, még akkor is, ha nincs használatban. Mivel a fürt díjai több időt vesznek igénybe, mint a tárterületre vonatkozó díjak, a gazdasági érzékek törlik a fürtöket, ha nincsenek használatban.

Fürt törléséhez tekintse [meg a HDInsight-fürt törlése a böngészőben, a PowerShell vagy az Azure CLI használatával](../hdinsight-delete-cluster.md)című témakört.

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban megtanulta, hogyan futtathat Apache Hive lekérdezéseket az Azure HDInsight-ben az Apache Zeppelin használatával. A kaptár-lekérdezésekkel kapcsolatos további tudnivalókért a következő cikk bemutatja, hogyan hajthat végre lekérdezéseket a Visual Studióval.

> [!div class="nextstepaction"]
> [Kapcsolódás az Azure HDInsight és Apache Hive-lekérdezések futtatása a Visual studióhoz készült Data Lake Tools használatával](../hadoop/apache-hadoop-visual-studio-tools-get-started.md)
