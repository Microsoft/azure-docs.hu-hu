---
title: Az Apache Ambari kaptár nézet használata az Azure HDInsight Apache Hadoop
description: Megtudhatja, hogyan használhatja a kaptár nézetet a webböngészőjéből a kaptár-lekérdezések elküldéséhez. A kaptár nézet a Linux-alapú HDInsight-fürthöz elérhető Ambari webes felhasználói felület részét képezi.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive,seoapr2020
ms.date: 04/23/2020
ms.openlocfilehash: 87a4d3960937450713747fa16bd473b4c34eff0e
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/23/2021
ms.locfileid: "104867865"
---
# <a name="use-apache-ambari-hive-view-with-apache-hadoop-in-hdinsight"></a>Apache Ambari Hive-nézet használata Apache Hadooppal a HDInsightban

[!INCLUDE [hive-selector](../../../includes/hdinsight-selector-use-hive.md)]

Megtudhatja, hogyan futtathat kaptár-lekérdezéseket az Apache Ambari kaptár nézet használatával. A kaptár nézet lehetővé teszi kaptár-lekérdezések létrehozását, optimalizálását és futtatását a böngészőben.

## <a name="prerequisites"></a>Előfeltételek

Hadoop-fürt a HDInsight-on. Lásd: Ismerkedés [a HDInsight Linux rendszeren](./apache-hadoop-linux-tutorial-get-started.md).

## <a name="run-a-hive-query"></a>Hive-lekérdezések futtatása

1. A [Azure Portal](https://portal.azure.com/)válassza ki a fürtöt.  Útmutatásért lásd: [fürtök listázása és megjelenítése](../hdinsight-administer-use-portal-linux.md#showClusters) . A fürt új portál nézetben nyílik meg.

1. A **fürt irányítópultok** területen válassza a **Ambari nézetek** elemet. Amikor a rendszer kéri a hitelesítést, használja a fürt `admin` létrehozásakor megadott bejelentkezési (alapértelmezett) fióknevet és jelszót. Azt is megnyithatja a böngészőben, ahol a a `https://CLUSTERNAME.azurehdinsight.net/#/main/views` `CLUSTERNAME` fürt neve.

1. A nézetek listájában válassza a __struktúra nézet__ lehetőséget.

    :::image type="content" source="./media/apache-hadoop-use-hive-ambari-view/select-apache-hive-view.png" alt-text="Apache Ambari – Apache Hive nézet kiválasztása" border="true":::

    A kaptár nézet oldal a következő képhez hasonló:

    :::image type="content" source="./media/apache-hadoop-use-hive-ambari-view/ambari-worksheet-view.png" alt-text="A struktúra nézet lekérdezési munkalapjának képe" border="true":::

1. A __lekérdezés__ lapon illessze be az alábbi HiveQL-utasításokat a munkalapra:

    ```hiveql
    DROP TABLE log4jLogs;
    CREATE EXTERNAL TABLE log4jLogs(
        t1 string,
        t2 string,
        t3 string,
        t4 string,
        t5 string,
        t6 string,
        t7 string)
    ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
    STORED AS TEXTFILE LOCATION '/example/data/';
    SELECT t4 AS loglevel, COUNT(*) AS count FROM log4jLogs
        WHERE t4 = '[ERROR]'
        GROUP BY t4;
    ```

    Ezek az utasítások a következő műveleteket végzik el:

    |Utasítás | Leírás |
    |---|---|
    |DROP TABLE|Törli a táblát és az adatfájlt arra az esetre, ha a tábla már létezik.|
    |KÜLSŐ TÁBLA LÉTREHOZÁSA|Létrehoz egy új "külső" táblát a kaptárban. A külső táblák csak a struktúra tábla definícióját tárolják. Az adatmező az eredeti helyen marad.|
    |SOR FORMÁTUMA|Megjeleníti az adat formázásának módját. Ebben az esetben az egyes naplók mezői szóközzel vannak elválasztva.|
    |TEXTFILE HELYEN TÁROLVA|Megjeleníti az adattárolás helyét, valamint a szövegként tárolt fájlokat.|
    |SELECT|Kiválasztja az összes olyan sor számát, ahol a T4 oszlop tartalmazza a [hiba] értéket.|

   > [!IMPORTANT]  
   > Hagyja meg az __adatbázis__ __alapértelmezett__ beállítását. A jelen dokumentumban szereplő példák a HDInsight alapértelmezett adatbázisát használják.

1. A lekérdezés elindításához válassza a **végrehajtás** a munkalap alatt lehetőséget. A gomb a narancssárga színűre változik, és a szöveg **leáll**.

1. A lekérdezés befejeződése után az **eredmények** lap a művelet eredményét jeleníti meg. A lekérdezés eredménye a következő szöveg:

    ```output
    loglevel       count
    [ERROR]        3
    ```

    A **napló** lapon megtekintheti a feladatok által létrehozott naplózási adatokat.

   > [!TIP]  
   > Töltse le vagy mentse az eredményeket az **eredmények** lap **műveletek** legördülő párbeszédpanelén.

### <a name="visual-explain"></a>Vizualizáció magyarázata

A lekérdezési terv vizualizációjának megjelenítéséhez válassza a munkalap alatti **vizuális magyarázat** lapot.

A lekérdezés **vizuális magyarázatot** ábrázoló nézete hasznos lehet az összetett lekérdezések folyamatának megismeréséhez.

### <a name="tez-ui"></a>Tez felhasználói felület

A lekérdezés TEZ felhasználói felületének megjelenítéséhez válassza a **TEZ felhasználói felület** fület a munkalap alatt.

> [!IMPORTANT]  
> A TEZ nem az összes lekérdezés feloldására szolgál. A TEZ használata nélkül is megoldhat sok lekérdezést.

## <a name="view-job-history"></a>Feladatelőzmények megtekintése

A __feladatok__ lap a struktúra-lekérdezések előzményeit jeleníti meg.

:::image type="content" source="./media/apache-hadoop-use-hive-ambari-view/apache-hive-job-history.png" alt-text="Apache Hive a feladatok lap előzményeinek megtekintése" border="true":::

## <a name="database-tables"></a>Adatbázistáblák

A __Tables (táblák__ ) lapon egy struktúra-adatbázisban lévő táblákkal dolgozhat.

:::image type="content" source="./media/apache-hadoop-use-hive-ambari-view/hdinsight-tables-tab.png" alt-text="A Apache Hive Tables lap képe" border="true":::

## <a name="saved-queries"></a>Mentett lekérdezések

A **lekérdezés** lapon igény szerint mentheti a lekérdezéseket. A lekérdezés mentése után újra felhasználhatja azt a __mentett lekérdezések__ lapról.

:::image type="content" source="./media/apache-hadoop-use-hive-ambari-view/ambari-saved-queries.png" alt-text="Apache Hive nézetek mentett lekérdezések lap" border="true":::

> [!TIP]  
> A mentett lekérdezések az alapértelmezett fürtöt tárolóban tárolódnak. A mentett lekérdezéseket az elérési út alatt találja `/user/<username>/hive/scripts` . Ezeket egyszerű szöveges fájlként tárolja a rendszer `.hql` .
>
> Ha törli a fürtöt, de megtartja a tárolót, használhat olyan segédprogramot, mint a [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) vagy a Data Lake Storage Explorer (az [Azure Portalról](https://portal.azure.com)) a lekérdezések lekéréséhez.

## <a name="user-defined-functions"></a>Felhasználó által definiált függvények

A struktúrát a felhasználó által definiált függvények (UDF) használatával is kiterjesztheti. Az UDF használatával olyan funkciót vagy logikát alkalmazhat, amely nem könnyen modellezhető a HiveQL-ben.

Deklaráljon és mentsen egy UDF a kaptár nézet tetején található **UDF** lapon. Ezek a UDF a **lekérdezés-szerkesztővel** is használhatók.

:::image type="content" source="./media/apache-hadoop-use-hive-ambari-view/user-defined-functions.png" alt-text="Apache Hive megtekintés UDF lap megjelenítése" border="true":::

Megjelenik a **UDF beszúrása** gomb a **lekérdezési szerkesztő** alján. Ez a bejegyzés a kaptár nézetben definiált UDF legördülő listáját jeleníti meg. Az UDF kiválasztásával HiveQL-utasítások adhatók hozzá a lekérdezéshez az UDF engedélyezéséhez.

Ha például egy UDF-t definiált a következő tulajdonságokkal:

* Erőforrás neve: myudfs

* Erőforrás elérési útja:/myudfs.jar

* UDF-név: myawesomeudf

* UDF-osztálynév: com. myudfs. Awesome

A **UDF beszúrása** gomb a **myudfs** nevű bejegyzést jeleníti meg, és az adott erőforráshoz definiált összes UDF számára egy másik legördülő listát. Ebben az esetben ez a **myawesomeudf**. A bejegyzés kiválasztásával hozzáadja a következőt a lekérdezés elejéhez:

```hiveql
add jar /myudfs.jar;
create temporary function myawesomeudf as 'com.myudfs.Awesome';
```

Ezután használhatja az UDF-t a lekérdezésben. Például: `SELECT myawesomeudf(name) FROM people;`.

A UDF és a HDInsight struktúrával való használatával kapcsolatos további információkért tekintse meg a következő cikkeket:

* [A Python használata Apache Hive és Apache Pig használatával a HDInsight-ben](python-udf-hdinsight.md)
* [Java UDF használata Apache Hive HDInsight](./apache-hadoop-hive-java-udf.md)

## <a name="hive-settings"></a>Struktúra beállításai

Módosíthatja a kaptárak különböző beállításait, például megváltoztathatja a kaptár végrehajtó motorját a TEZ (az alapértelmezett) értékről a MapReduce.

## <a name="next-steps"></a>Következő lépések

A HDInsight struktúra általános információi:

* [Apache Hive használata a HDInsight Apache Hadoop használatával](hdinsight-use-hive.md)
* [Az Apache Beeline-ügyfél használata Apache Hive-val](apache-hadoop-use-hive-beeline.md)
