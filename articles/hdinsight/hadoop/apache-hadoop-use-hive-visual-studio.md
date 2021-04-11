---
title: Apache Hive & Data Lake Tools for Visual Studio – Azure HDInsight
description: Ismerje meg, hogy a Visual studióhoz készült Data Lake Tools segítségével hogyan futtathat Apache Hive-lekérdezéseket az Azure HDInsight Apache Hadoop használatával.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 11/27/2019
ms.openlocfilehash: 4512c9d9fdb66713ba24fbf30278e5d5dbb2ae23
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "104863751"
---
# <a name="run-apache-hive-queries-using-the-data-lake-tools-for-visual-studio"></a>Apache Hive-lekérdezések futtatása a Data Lake Tools for Visual Studióval

Megtudhatja, hogyan használhatja a Visual studióhoz készült Data Lake-eszközöket a Apache Hive lekérdezéséhez. A Data Lake-eszközök lehetővé teszik a kaptár-lekérdezések egyszerű létrehozását, beküldését és figyelését az Azure HDInsight Apache Hadoop.

## <a name="prerequisites"></a>Előfeltételek

* Egy Apache Hadoop-fürt a HDInsight-on. Az elem létrehozásával kapcsolatos információkért lásd: [Apache Hadoop-fürt létrehozása az Azure HDInsight Resource Manager-sablon használatával](./apache-hadoop-linux-tutorial-get-started.md).

* [Visual Studio](https://visualstudio.microsoft.com/vs/). A cikkben szereplő lépések a Visual Studio 2019-et használják.

* A Visual studióhoz készült HDInsight-eszközök és a Visual studióhoz készült Azure Data Lake eszközök. További információ az eszközök telepítéséről és konfigurálásáról: [Data Lake Tools for Visual Studio telepítése](apache-hadoop-visual-studio-tools-get-started.md#install-data-lake-tools-for-visual-studio).

## <a name="run-apache-hive-queries-using-the-visual-studio"></a>Apache Hive lekérdezések futtatása a Visual Studio használatával

Hive-lekérdezések létrehozására és futtatására két lehetősége van:

* Alkalmi lekérdezések létrehozása.
* Hozzon létre egy kaptár-alkalmazást.

### <a name="create-an-ad-hoc-hive-query"></a>Ad-hoc kaptár-lekérdezés létrehozása

Az alkalmi lekérdezések **kötegelt** vagy **interaktív** módban is végrehajthatók.

1. Indítsa el a **Visual studiót** , és válassza a **Folytatás kód nélkül** lehetőséget.

2. A **Server Explorerben** kattintson a jobb gombbal az **Azure** elemre, válassza a **Kapcsolódás Microsoft Azure előfizetéshez**... lehetőséget, és fejezze be a bejelentkezési folyamatot.

3. Bontsa ki a **HDInsight** csomópontot, kattintson a jobb gombbal arra a fürtre, amelyen futtatni szeretné a lekérdezést, majd válassza a **kaptár-lekérdezés írása** lehetőséget.

4. Adja meg a következő kaptár-lekérdezést:

    ```hql
    SELECT * FROM hivesampletable;
    ```

5. Válassza a **Végrehajtás** lehetőséget. A végrehajtási mód alapértelmezett értéke az **interaktív**.

    :::image type="content" source="./media/apache-hadoop-use-hive-visual-studio/vs-execute-hive-query.png" alt-text="Interaktív kaptár-lekérdezés végrehajtása, Visual Studio" border="true":::

6. Ha ugyanezt a lekérdezést **Batch** módban szeretné futtatni, a legördülő listát az **Interaktívról** a **kötegbe** kapcsolhatja. A végrehajtási gomb **a végrehajtásról** a **küldésre** változik.

    :::image type="content" source="./media/apache-hadoop-use-hive-visual-studio/visual-studio-batch-query.png" alt-text="Batch-struktúra beküldése – lekérdezés, Visual Studio" border="true":::

    A Hive szerkesztője támogatja az IntelliSense-t. A Data Lake Tools for Visual Studio támogatja a távoli metaadatok betöltését a Hive-szkript szerkesztésekor. Ha például a (z) értéket adja meg `SELECT * FROM` , az IntelliSense felsorolja az összes javasolt táblanév nevét. Amikor megad egy táblanevet, az IntelliSense listázza az oszlopneveket. Az eszközök a legtöbb Hive DML-utasítást, -segédlekérdezést és beépített UDF-et támogatják. Az IntelliSense csak a HDInsight eszköztáron kijelölt fürt metaadatait javasolja.

7. A lekérdezés eszköztáron (a lekérdezés lap alatti és a lekérdezés szövege felett) válassza a **Küldés** lehetőséget, vagy válassza a **Küldés** elem melletti legördülő nyilat, és válassza a **speciális** lehetőséget a legördülő listából. Ha az utóbbi lehetőséget választja,

8. Ha a speciális küldés lehetőséget választotta, a **parancsfájl elküldése** párbeszédpanelen konfigurálja a **feladatok nevét**, az **argumentumokat**, a **további konfigurációkat** és az **állapot könyvtárat** . Ezután válassza a **Submit (Küldés**) lehetőséget.

    :::image type="content" source="./media/apache-hadoop-use-hive-visual-studio/vs-tools-submit-jobs-advanced.png" alt-text="Parancsfájl elküldése párbeszédpanel, HDInsight Hadoop-struktúra lekérdezése" border="true":::

### <a name="create-a-hive-application"></a>Hive alkalmazás létrehozása

Ha kaptár-lekérdezést szeretne futtatni egy kaptár-alkalmazás létrehozásával, kövesse az alábbi lépéseket:

1. Nyissa meg a **Visual studiót**.

2. A **Start** ablakban válassza az **új projekt létrehozása** lehetőséget.

3. A **create a New Project (új projekt létrehozása** ) ablakban a **sablonok keresése** mezőbe írja be a *kaptár* kifejezést. Ezután válassza ki a **kaptár alkalmazást** , és kattintson a **Tovább gombra**.

4. Az **új projekt konfigurálása** ablakban adja meg a **projekt nevét**, válasszon ki vagy hozzon létre egy **helyet** az új projekthez, majd válassza a **Létrehozás** lehetőséget.

5. Nyissa meg a projekttel létrehozott **script. HQL** fájlt, és illessze be a következő HiveQL-utasításokat:

    ```hql
    set hive.execution.engine=tez;
    DROP TABLE log4jLogs;
    CREATE EXTERNAL TABLE log4jLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string)
    ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
    STORED AS TEXTFILE LOCATION '/example/data/';
    SELECT t4 AS sev, COUNT(*) AS count FROM log4jLogs WHERE t4 = '[ERROR]' AND  INPUT__FILE__NAME LIKE '%.log' GROUP BY t4;
    ```

    Ezek az utasítások a következő műveleteket végzik el:

    * `DROP TABLE`: Törli a táblázatot, ha az létezik.

    * `CREATE EXTERNAL TABLE`: Új "külső" táblát hoz létre a kaptárban. A külső táblák csak a struktúra tábla definícióját tárolják. (Az eredeti helyen maradnak az adathalmazok.)

        > [!NOTE]  
        > A külső táblákat akkor kell használni, ha a mögöttes adatokat külső forrás, például egy MapReduce vagy egy Azure-szolgáltatás frissíti.
        >
        > Egy külső tábla eldobása **nem** törli az adatforrást, csak a tábla definícióját.

    * `ROW FORMAT`: A struktúra az adat formázásának módját mutatja. Ebben az esetben az egyes naplók mezői szóközzel vannak elválasztva.

    * `STORED AS TEXTFILE LOCATION`: Megadja a Kaptárat, hogy az adattárolás a *példában/* adatkönyvtárban történik, és hogy a szövegként van tárolva.

    * `SELECT`: Kiválasztja az összes olyan sor számát, amelyben `t4` az oszlop tartalmazza az értéket `[ERROR]` . Ez az utasítás a értéket adja vissza `3` , mert három sor tartalmazza ezt az értéket.

    * `INPUT__FILE__NAME LIKE '%.log'`: Arra utasítja a kaptárt, hogy csak a. log fájlban végződő fájlok adatait küldje vissza. Ez a záradék korlátozza a keresést a *sample. log* fájlban, amely tartalmazza az adathalmazt.

6. Válassza ki a lekérdezéshez használni kívánt HDInsight-fürtöt a lekérdezési fájl eszköztárról (amely hasonló megjelenéssel rendelkezik az ad hoc lekérdezési eszköztárhoz). Ezután módosítsa az **interaktív** elemet a **Batch** (ha szükséges) értékre, majd válassza a **Submit (Küldés** ) lehetőséget az utasítások struktúra-feladatokként való futtatásához.

   Megjelenik a **kaptár-feladatok összegzése** , és megjeleníti a futó feladattal kapcsolatos információkat. A **frissítés** hivatkozásra kattintva frissítse a feladatokat, amíg a **feladatok állapota** **Befejezve** nem változik.

   :::image type="content" source="./media/apache-hadoop-use-hive-visual-studio/hdinsight-job-summary.png" alt-text="Befejezett struktúra-feladatok összefoglalása, struktúra alkalmazása, Visual Studio" border="true":::

7. A feladatok kimenetének megtekintéséhez válassza a **feladatok kimenete** elemet. Ekkor megjelenik `[ERROR] 3` a lekérdezés által visszaadott érték.

### <a name="additional-example"></a>További példa

Az alábbi példa az `log4jLogs` előző eljárásban létrehozott táblára támaszkodik, és [létrehoz egy kaptár-alkalmazást](#create-a-hive-application).

1. A **Server Explorerben** kattintson a jobb gombbal a fürtre, és válassza a **kaptár-lekérdezés írása** lehetőséget.

2. Adja meg a következő kaptár-lekérdezést:

    ```hql
    set hive.execution.engine=tez;
    CREATE TABLE IF NOT EXISTS errorLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) STORED AS ORC;
    INSERT OVERWRITE TABLE errorLogs SELECT t1, t2, t3, t4, t5, t6, t7 FROM log4jLogs WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log';
    ```

    Ezek az utasítások a következő műveleteket végzik el:

    * `CREATE TABLE IF NOT EXISTS`: Létrehoz egy táblázatot, ha még nem létezik. Mivel a `EXTERNAL` kulcsszó nincs használatban, az utasítás belső táblát hoz létre. A belső táblákat a rendszer a struktúra adattárházában tárolja, és a struktúra kezeli.

        > [!NOTE]  
        > `EXTERNAL`A táblákkal ellentétben a belső táblák eldobása is törli az alapul szolgáló adatokat.

    * `STORED AS ORC`: Az adatok az *optimalizált sorok oszlopos* (ORK) formátumban vannak tárolva. Az ork kiválóan optimalizált és hatékony formátum a kaptárak adatok tárolására.

    * `INSERT OVERWRITE ... SELECT`: Kijelöli azokat a sorokat `log4jLogs` , amelyek tartalmazzák a táblát `[ERROR]` , majd beszúrja az adatait a `errorLogs` táblába.

3. Szükség esetén módosítsa az **interaktív** elemet a **Batch** szolgáltatásban, majd válassza a **Küldés** lehetőséget.

4. Annak ellenőrzéséhez, hogy a feladatot hozta-e létre, nyissa meg a **Server Explorert** , és bontsa ki az **Azure**  >  **HDInsight** elemet. Bontsa ki a HDInsight-fürtöt, majd bontsa ki a **kaptár-adatbázisok**  >  **alapértelmezett értéket**. Megjelenik a **alkalmazásnaplókat** tábla és a **log4jLogs** tábla.

## <a name="next-steps"></a>Következő lépések

Amint láthatja, a Visual studióhoz készült HDInsight Tools egyszerű módszert biztosít a HDInsight-beli kaptár-lekérdezésekkel való együttműködésre.

* A HDInsight struktúrával kapcsolatos általános információkért lásd: [Mi az a Apache Hive és a HiveQL az Azure HDInsight?](hdinsight-use-hive.md)

* További információ a HDInsight-beli Hadoop-használattal kapcsolatos egyéb módszerekről: a [MapReduce használata az Apache Hadoop on HDInsight](hdinsight-use-mapreduce.md)

* További információ a Visual studióhoz készült HDInsight-eszközökről:[Data Lake eszközök használata a Visual studióhoz az Azure HDInsight való kapcsolódáshoz és Apache Hive lekérdezések futtatásához](apache-hadoop-visual-studio-tools-get-started.md)
