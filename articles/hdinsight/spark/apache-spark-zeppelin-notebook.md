---
title: Zeppelin notebookok & Apache Spark cluster – Azure HDInsight
description: Részletes útmutató a Apache Spark-fürtökkel rendelkező Zeppelin notebookok Azure HDInsight való használatához.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive,seoapr2020
ms.date: 04/23/2020
ms.openlocfilehash: ba58c007dc0bc3776f429366651cb79404b137b3
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98928473"
---
# <a name="use-apache-zeppelin-notebooks-with-apache-spark-cluster-on-azure-hdinsight"></a>Apache Zeppelin-notebookok használata Apache Spark-fürtökön az Azure HDInsight rendszerében

A HDInsight Spark-fürtök közé tartoznak az [Apache Zeppelin](https://zeppelin.apache.org/) notebookok. Apache Spark feladatok futtatásához használja a jegyzetfüzeteket. Ebből a cikkből megtudhatja, hogyan használhatja a Zeppelin notebookot egy HDInsight-fürtön.

## <a name="prerequisites"></a>Előfeltételek

* Apache Spark-fürt megléte a HDInsightban. További útmutatásért lásd: [Apache Spark-fürt létrehozása az Azure HDInsightban](apache-spark-jupyter-spark-sql.md).
* A fürtök elsődleges tárolójának URI-sémája. A séma az `wasb://` Azure Blob Storage esetében `abfs://` Azure Data Lake Storage Gen2 vagy Azure Data Lake Storage Gen1 esetében lenne `adl://` . Ha a biztonságos átvitel engedélyezve van a Blob Storage számára, akkor az URI a következő lesz: `wasbs://` .  További információ: [biztonságos átvitel megkövetelése az Azure Storage-ban](../../storage/common/storage-require-secure-transfer.md) .

## <a name="launch-an-apache-zeppelin-notebook"></a>Apache Zeppelin-jegyzetfüzet elindítása

1. A Spark-fürt **áttekintése** lapon válassza ki a **Zeppelin jegyzetfüzet** elemet a **fürt irányítópultján**. Adja meg a fürt rendszergazdai hitelesítő adatait.  

   > [!NOTE]  
   > A következő URL-címet a böngészőben is elérheti, ha megnyithatja a Zeppelin jegyzetfüzetet a fürthöz. Cserélje le a **CLUSTERNAME** nevet a fürt nevére:
   >
   > `https://CLUSTERNAME.azurehdinsight.net/zeppelin`

2. Hozzon létre új notebookot. A fejléc panelen navigáljon a **Jegyzetfüzet**  >  **új Megjegyzés létrehozása** elemére.

    ![Új Zeppelin-jegyzetfüzet létrehozása](./media/apache-spark-zeppelin-notebook/hdinsight-create-zeppelin-notebook.png "Új Zeppelin-jegyzetfüzet létrehozása")

    Adja meg a jegyzetfüzet nevét, majd válassza a **Megjegyzés létrehozása** lehetőséget.

3. Győződjön meg arról, hogy a jegyzetfüzet fejléce csatlakoztatott állapotot mutat. Ezt a jobb felső sarokban található zöld pont jelöli.

    ![Zeppelin notebook állapota](./media/apache-spark-zeppelin-notebook/hdinsight-zeppelin-connected.png "Zeppelin notebook állapota")

4. Töltse be a mintaadatokat egy ideiglenes táblába. Amikor létrehoz egy Spark-fürtöt a HDInsight-ben, a rendszer átmásolja a minta adatfájlt `hvac.csv` a társított Storage-fiókba `\HdiSamples\SensorSampleData\hvac` .

    Illessze be a következő kódrészletet az új jegyzetfüzetben alapértelmezés szerint létrehozott üres bekezdésbe.

    ```scala
    %livy2.spark
    //The above magic instructs Zeppelin to use the Livy Scala interpreter

    // Create an RDD using the default Spark context, sc
    val hvacText = sc.textFile("wasbs:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")

    // Define a schema
    case class Hvac(date: String, time: String, targettemp: Integer, actualtemp: Integer, buildingID: String)

    // Map the values in the .csv file to the schema
    val hvac = hvacText.map(s => s.split(",")).filter(s => s(0) != "Date").map(
        s => Hvac(s(0),
                s(1),
                s(2).toInt,
                s(3).toInt,
                s(6)
        )
    ).toDF()

    // Register as a temporary table called "hvac"
    hvac.registerTempTable("hvac")
    ```

    Nyomja le a **SHIFT + ENTER** billentyűkombinációt, vagy kattintson a bekezdés **Lejátszás** gombjára a kódrészlet futtatásához. A bekezdés jobb oldali sarkában lévő állapotnak a kész, FÜGGŐben lévő, befejezett értékűre kell mutatnia. A kimenet az azonos bekezdés alján jelenik meg. A képernyőkép a következő képhez hasonlít:

    ![Ideiglenes tábla létrehozása nyers adatokból](./media/apache-spark-zeppelin-notebook/hdinsight-zeppelin-load-data.png "Ideiglenes tábla létrehozása nyers adatokból")

    Az egyes bekezdésekhez címet is megadhat. A bekezdés jobb oldali sarkában válassza a **Beállítások** ikont (lánckerék), majd válassza a **title (cím megjelenítése**) lehetőséget.  

    > [!NOTE]  
    > % spark2-értelmező nem támogatott a Zeppelin jegyzetfüzetekben az összes HDInsight-verzióban, és a (z)% sh tolmács nem támogatott a HDInsight 4,0-től kezdődően.

5. Most már futtathat Spark SQL-utasításokat is a `hvac` táblán. Illessze be a következő lekérdezést egy új bekezdésbe. A lekérdezés lekérdezi az építési azonosítót. Emellett a cél és a tényleges hőmérséklet közötti különbség is az egyes épületek esetében egy adott időpontban. Nyomja le a **SHIFT + ENTER** billentyűkombinációt.

    ```sql
    %sql
    select buildingID, (targettemp - actualtemp) as temp_diff, date from hvac where date = "6/1/13"
    ```  

    Az elején található **% SQL** -utasítás azt jelzi, hogy a jegyzetfüzet a Livy Scala tolmácsot használja.

6. Válassza a **oszlopdiagram** ikont a Megjelenítés módosításához.  a **Beállítások**, amelyek a **oszlopdiagram** kiválasztása után jelennek meg, lehetővé teszi a **kulcsok** és **értékek** kiválasztását.  Az alábbi képernyőfelvételen a kimenet látható.

    ![Spark SQL-utasítás futtatása a notebook1 használatával](./media/apache-spark-zeppelin-notebook/hdinsight-zeppelin-spark-query-1.png "Spark SQL-utasítás futtatása a notebook1 használatával")

7. A Spark SQL-utasításokat a lekérdezésben szereplő változók használatával is futtathatja. A következő kódrészlet bemutatja, hogyan definiálhat egy változót a lekérdezésben a lekérdezéssel megadható `Temp` lehetséges értékekkel. A lekérdezés első futtatásakor a legördülő menü automatikusan kitöltődik a változóhoz megadott értékekkel.

    ```sql
    %sql  
    select buildingID, date, targettemp, (targettemp - actualtemp) as temp_diff from hvac where targettemp > "${Temp = 65,65|75|85}"
    ```

    Illessze be ezt a kódrészletet egy új bekezdésbe, majd nyomja le a **SHIFT + ENTER** billentyűkombinációt. Ezután válassza a **65** elemet a **temp** legördülő listából.

8. Válassza a **oszlopdiagram** ikont a Megjelenítés módosításához.  Ezután válassza a **Beállítások** lehetőséget, és végezze el a következő módosításokat:

   * **Csoportok:**  **Targettemp** hozzáadása.  
   * **Értékek:** 1. Törlés **dátuma**.  2. **Temp_diff** hozzáadása.  3.  Módosítsa a gyűjtőt az **összegből** az **AVG** értékre.  

     Az alábbi képernyőfelvételen a kimenet látható.

     ![Spark SQL-utasítás futtatása a notebook2 használatával](./media/apache-spark-zeppelin-notebook/hdinsight-zeppelin-spark-query-2.png "Spark SQL-utasítás futtatása a notebook2 használatával")

## <a name="how-do-i-use-external-packages-with-the-notebook"></a>A Hogyan külső csomagokat használ a jegyzetfüzettel?

A HDInsight Apache Spark-fürtben található Zeppelin notebook a fürtben nem szereplő külső, Közösség által biztosított csomagokat is használhat. A rendelkezésre álló csomagok teljes listájához keresse meg a [Maven-tárházat](https://search.maven.org/) . Lekérheti az egyéb forrásokból származó elérhető csomagok listáját is. A közösségi hozzájárulású csomagok teljes listája például a [Spark-csomagokban](https://spark-packages.org/)érhető el.

Ebből a cikkből megtudhatja, hogyan használhatja a [Spark-CSV-](https://search.maven.org/#artifactdetails%7Ccom.databricks%7Cspark-csv_2.10%7C1.4.0%7Cjar) csomagot a Jupyter notebook.

1. Nyissa meg a tolmács beállításait. A jobb felső sarokban válassza ki a bejelentkezett felhasználó nevét, majd válassza a **tolmács** lehetőséget.

    ![Tolmács elindítása](./media/apache-spark-zeppelin-notebook/zeppelin-launch-interpreter.png "Struktúra kimenete")

2. Görgessen a **livy2**, majd válassza a **Szerkesztés** lehetőséget.

    ![Tolmács beállításai1 módosítása](./media/apache-spark-zeppelin-notebook/zeppelin-use-external-package-1.png "Tolmács beállításai1 módosítása")

3. Navigáljon a kulcshoz `livy.spark.jars.packages` , és állítsa be az értékét a következő formátumban: `group:id:version` . Ha tehát a [Spark-CSV](https://search.maven.org/#artifactdetails%7Ccom.databricks%7Cspark-csv_2.10%7C1.4.0%7Cjar) csomagot szeretné használni, a kulcs értékét a értékre kell állítania `com.databricks:spark-csv_2.10:1.4.0` .

    ![Tolmács settings2 módosítása](./media/apache-spark-zeppelin-notebook/zeppelin-use-external-package-2.png "Tolmács settings2 módosítása")

    Válassza a **Mentés** lehetőséget, majd kattintson az **OK gombra** a Livy-értelmező újraindításához.

4. Ha szeretné megismerni, hogyan lehet megérkezni a fent megadott kulcs értékeként, itt találja a következőt:.

    a. Keresse meg a csomagot a Maven-tárházban. Ehhez a cikkhez a [Spark-CSV-](https://search.maven.org/#artifactdetails%7Ccom.databricks%7Cspark-csv_2.10%7C1.4.0%7Cjar)fájlt használtuk.

    b. A tárházból Gyűjtse össze a **GroupID**, a **ArtifactId** és a **verzió** értékeit.

    ![Külső csomagok használata Jupyter Notebook](./media/apache-spark-zeppelin-notebook/use-external-packages-with-jupyter.png "Külső csomagok használata Jupyter Notebook")

    c. Összefűzi a három értéket kettősponttal elválasztva (**:**).

    ```
    com.databricks:spark-csv_2.10:1.4.0
    ```

## <a name="where-are-the-zeppelin-notebooks-saved"></a>Hol lesznek mentve a Zeppelin jegyzetfüzetek?

A Zeppelin jegyzetfüzetek a fürt átjárócsomópontokkal lesznek mentve. Tehát ha törli a fürtöt, a jegyzetfüzetek is törlődnek. Ha szeretné megőrizni a jegyzetfüzeteket a későbbi használat érdekében más fürtökön, exportálnia kell azokat a feladatok futtatása után. Jegyzetfüzet exportálásához válassza ki az **Exportálás** ikont az alábbi képen látható módon.

![Jegyzetfüzet letöltése](./media/apache-spark-zeppelin-notebook/zeppelin-download-notebook.png "A jegyzetfüzet letöltése")

Ez a művelet JSON-fájlként menti a jegyzetfüzetet a letöltési helyen.

## <a name="use-shiro-to-configure-access-to-zeppelin-interpreters-in-enterprise-security-package-esp-clusters"></a>`Shiro`A használatával konfigurálhatja a Zeppelin-tolmácsokhoz való hozzáférést Enterprise Security Package (ESP) fürtökben

A fentiekben leírtak szerint a `%sh` tolmács nem támogatott a HDInsight 4,0-től kezdődően. Emellett mivel a `%sh` tolmács olyan biztonsági problémákat is bevezet, mint például a parancssori felületi parancsok használata, a HDInsight 3,6 ESP-fürtökből is el lett távolítva. Ez azt jelenti, hogy `%sh` az értelmező nem érhető el, ha az **új Megjegyzés létrehozása** vagy a tolmács felhasználói felülete alapértelmezés szerint lehetőségre kattint.

Az emelt szintű tartományi felhasználók a `Shiro.ini` fájl segítségével szabályozhatják a tolmács felhasználói felületének elérését. Csak ezek a felhasználók hozhatnak létre új `%sh` tolmácsokat, és állíthatnak be engedélyeket az egyes új `%sh` tolmácsok számára. A fájl használatával történő hozzáférés vezérléséhez `shiro.ini` kövesse az alábbi lépéseket:

1. Adjon meg egy új szerepkört egy meglévő tartományi csoport neve alapján. A következő példában a `adminGroupName` HRE Kiemelt jogosultságú felhasználók csoportja. Ne használjon speciális karaktereket vagy szóközöket a csoport nevében. A `=` szerepkör engedélyeinek megadása utáni karakterek. `*` azt jelenti, hogy a csoport teljes körű engedélyekkel rendelkezik.

    ```
    [roles]
    adminGroupName = *
    ```

2. Adja hozzá az új szerepkört a Zeppelin-tolmácsokhoz való hozzáféréshez. A következő példában a összes felhasználója `adminGroupName` hozzáférést kap a Zeppelin-tolmácsokhoz, és új tolmácsokat hozhat létre. Több szerepkört is beállíthat a zárójelek között `roles[]` , vesszővel elválasztva. Ezután a szükséges engedélyekkel rendelkező felhasználók hozzáférhetnek a Zeppelin-tolmácsokhoz.

    ```
    [urls]
    /api/interpreter/** = authc, roles[adminGroupName]
    ```

## <a name="livy-session-management"></a>Livy munkamenet-kezelés

A Zeppelin jegyzetfüzet első kódja bekezdése egy új Livy-munkamenetet hoz létre a fürtben. Ez a munkamenet az összes később létrehozott Zeppelin-jegyzetfüzetben meg van osztva. Ha a Livy-munkamenetet bármilyen okból leölik, a feladatok nem futnak a Zeppelin jegyzetfüzetből.

Ilyen esetben a következő lépéseket kell elvégeznie, mielőtt elkezdené a feladatok futtatását egy Zeppelin-jegyzetfüzetből.  

1. Indítsa újra a Livy-tolmácsot a Zeppelin jegyzetfüzetből. Ehhez nyissa meg a tolmács beállításait úgy, hogy kiválasztja a bejelentkezett felhasználó nevét a jobb felső sarokban, majd kiválasztja a **tolmács** elemet.

    ![Tolmács elindítása](./media/apache-spark-zeppelin-notebook/zeppelin-launch-interpreter.png "Struktúra kimenete")

2. Görgessen a **livy2**, majd válassza az **Újraindítás** lehetőséget.

    ![A Livy-értelmező újraindítása](./media/apache-spark-zeppelin-notebook/hdinsight-zeppelin-restart-interpreter.png "A Zeppelin-tolmács újraindítása")

3. Kód cellájának futtatása meglévő Zeppelin-jegyzetfüzetből. Ez a kód egy új Livy-munkamenetet hoz létre a HDInsight-fürtben.

## <a name="general-information"></a>Általános információk

### <a name="validate-service"></a>Szolgáltatás ellenőrzése

A szolgáltatás Ambari való ellenőrzéséhez navigáljon `https://CLUSTERNAME.azurehdinsight.net/#/main/services/ZEPPELIN/summary` oda, ahol a CLUSTERNAME a fürt neve.

A szolgáltatás parancssorból való érvényesítéséhez az SSH-t a fő csomóponthoz kell bejelentkeznie. Váltson a felhasználóra a Zeppelin parancs használatával `sudo su zeppelin` . Állapot parancsai:

|Parancs |Leírás |
|---|---|
|`/usr/hdp/current/zeppelin-server/bin/zeppelin-daemon.sh status`|A szolgáltatás állapota.|
|`/usr/hdp/current/zeppelin-server/bin/zeppelin-daemon.sh --version`|Szolgáltatás verziója.|
|`ps -aux | grep zeppelin`|Azonosítsa a PID-t.|

### <a name="log-locations"></a>Naplók helye

|Szolgáltatás |Elérési út |
|---|---|
|Zeppelin – kiszolgáló|/usr/hdp/current/zeppelin-server/|
|Kiszolgálói naplók|/var/log/zeppelin|
|Konfiguráció-értelmező, `Shiro` , site.xml, log4j|/usr/HDP/current/Zeppelin-Server/conf vagy/etc/Zeppelin/conf|
|PID-könyvtár|/var/run/zeppelin|

### <a name="enable-debug-logging"></a>Hibakeresési naplózás engedélyezése

1. Navigáljon `https://CLUSTERNAME.azurehdinsight.net/#/main/services/ZEPPELIN/summary` oda, ahol a CLUSTERNAME a fürt neve.

1. Navigáljon a **konfigurációk**  >  **speciális Zeppelin-log4j-Properties**  >  **log4j_properties_content**.

1. Módosítás a következőre: `log4j.appender.dailyfile.Threshold = INFO` `log4j.appender.dailyfile.Threshold = DEBUG` .

1. Hozzáadás `log4j.logger.org.apache.zeppelin.realm=DEBUG` .

1. Mentse a módosításokat, és indítsa újra a szolgáltatást.

## <a name="next-steps"></a>Következő lépések

* [Overview: Apache Spark on Azure HDInsight (Áttekintés: Apache Spark on Azure HDInsight)](apache-spark-overview.md)
* [A HDInsight Apache Spark-fürtben Jupyter Notebook számára elérhető kernelek](apache-spark-jupyter-notebook-kernels.md)
* [A Jupyter telepítése a számítógépre, majd csatlakozás egy HDInsight Spark-fürthöz](apache-spark-jupyter-notebook-install-locally.md)
