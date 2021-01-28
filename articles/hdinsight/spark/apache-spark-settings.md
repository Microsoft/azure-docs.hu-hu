---
title: A Spark beállításainak konfigurálása – Azure HDInsight
description: Azure HDInsight-fürt Apache Spark beállításainak megtekintése és konfigurálása
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive,seoapr2020
ms.date: 04/24/2020
ms.openlocfilehash: 148ba2ce256b11b623bf28cf7723dbb0cce60830
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/28/2021
ms.locfileid: "98929746"
---
# <a name="configure-apache-spark-settings"></a>Az Apache Spark beállításainak konfigurálása

An méretű HDInsight Spark-fürt tartalmazza a Apache Spark könyvtár telepítését.  Minden HDInsight-fürt tartalmaz alapértelmezett konfigurációs paramétereket az összes telepített szolgáltatásához, beleértve a Sparkot is.  A HDInsight Apache Hadoop-fürt kezelésének egyik fő aspektusa a munkaterhelés figyelése, beleértve a Spark-feladatokat is. A Spark-feladatok legjobb futtatásához a fürt logikai konfigurációjának meghatározásakor vegye figyelembe a fizikai fürt konfigurációját.

Az alapértelmezett HDInsight Apache Spark-fürt a következő csomópontokat tartalmazza: három Apache ZooKeeper csomópont, két fő csomópont és egy vagy több munkavégző csomópont:

![Spark HDInsight architektúra](./media/apache-spark-settings/spark-hdinsight-arch.png)

A virtuális gépek és a virtuálisgép-méretek száma a HDInsight-fürt csomópontjaira hatással lehet a Spark-konfigurációra. A nem alapértelmezett HDInsight-konfigurációs értékek gyakran nem alapértelmezett Spark-konfigurációs értékeket igényelnek. HDInsight Spark-fürt létrehozásakor az egyes összetevőknél javasolt virtuálisgép-méretek láthatók. Jelenleg az Azure-hoz készült [memória-optimalizált Linux virtuális gépek mérete](../../virtual-machines/sizes-memory.md) D12 v2 vagy újabb.

## <a name="apache-spark-versions"></a>Apache Spark verziók

Használja a fürt legjobb Spark-verzióját.  A HDInsight szolgáltatás a Spark és a HDInsight több verzióját is tartalmazza.  A Spark minden verziója tartalmaz egy alapértelmezett fürtkonfiguráció-készletet.  

Új fürt létrehozásakor több Spark-verzió közül választhat. A teljes lista megjelenítéséhez  [HDInsight összetevőket és verziókat](../hdinsight-component-versioning.md).

> [!NOTE]  
> A HDInsight szolgáltatás Apache Spark alapértelmezett verziója figyelmeztetés nélkül változhat. Ha a verziótól függ, a Microsoft azt javasolja, hogy a .NET SDK, a Azure PowerShell és a klasszikus Azure parancssori felület használatával hozzon létre egy adott verziót.

Apache Spark három rendszerkonfigurációs hellyel rendelkezik:

* A Spark-tulajdonságok vezérlik a legtöbb alkalmazás-paramétert, és egy `SparkConf` objektum vagy Java-rendszertulajdonság használatával állíthatók be.
* A környezeti változók az egyes csomópontokon futtatott parancsfájlokon keresztül, például az IP-cím beállítására használhatók `conf/spark-env.sh` .
* A naplózás a használatával konfigurálható `log4j.properties` .

A Spark egy adott verziójának kiválasztásakor a fürt tartalmazza az alapértelmezett konfigurációs beállításokat.  Az alapértelmezett Spark-konfigurációs értékeket egyéni Spark-konfigurációs fájl használatával módosíthatja.  Erre mutat példát az alábbi ábra.

```
spark.hadoop.io.compression.codecs org.apache.hadoop.io.compress.GzipCodec
spark.hadoop.mapreduce.input.fileinputformat.split.minsize 1099511627776
spark.hadoop.parquet.block.size 1099511627776
spark.sql.files.maxPartitionBytes 1099511627776
spark.sql.files.openCostInBytes 1099511627776
```

A fenti példában szereplő példa több alapértelmezett értéket felülbírál az öt Spark konfigurációs paraméternél.  Ezek az értékek a tömörítési kodek, Apache Hadoop a MapReduce felosztásának minimális mérete és a parketta-blokkok mérete. Emellett a Spar SQL Partition és a Open File sizes alapértelmezett értékei.  Ezek a konfigurációs változások úgy vannak kiválasztva, hogy a kapcsolódó adatok és feladatok (ebben a példában a genomikai adatok) speciális tulajdonságokkal rendelkezzenek. Ezek a tulajdonságok az egyéni konfigurációs beállítások használatával hatékonyabban fognak megjelenni.

---

## <a name="view-cluster-configuration-settings"></a>Fürtkonfiguráció beállításainak megtekintése

A fürtön a teljesítmény optimalizálása előtt ellenőrizze, hogy az aktuális HDInsight-fürt konfigurációs beállításai megtalálhatók-e. Indítsa el a HDInsight-irányítópultot a Azure Portal a Spark-fürt ablaktábláján található **irányítópult** hivatkozásra kattintva. Jelentkezzen be a fürt rendszergazdai felhasználónevével és jelszavával.

Megjelenik az Apache Ambari webes KEZELŐFELÜLETe, amely a fő fürterőforrás-használati metrikák irányítópultját jeleníti meg.  A Ambari-irányítópulton a Apache Spark konfiguráció és más telepített szolgáltatások láthatók. Az irányítópult tartalmaz egy **konfigurációs előzmények** lapot, ahol megtekintheti a telepített szolgáltatások információit, beleértve a Sparkot.

A Apache Spark konfigurációs értékeinek megtekintéséhez válassza a konfiguráció **előzményei** lehetőséget, majd válassza a **Spark2** lehetőséget.  Válassza a **konfigurációk** fület, majd a `Spark` szolgáltatás listában válassza a (vagy a `Spark2` verziótól függően) hivatkozást.  Ekkor megjelenik a fürthöz tartozó konfigurációs értékek listája:

![Spark-konfigurációk](./media/apache-spark-settings/spark-configurations.png)

Az egyes Spark-konfigurációs értékek megtekintéséhez és módosításához válassza a cím "Spark" elemét.  A Spark-konfigurációk a következő kategóriákban tartalmazzák az egyéni és a speciális konfigurációs értékeket is:

* Egyéni Spark2 – alapértékek
* Egyéni Spark2 – mérőszámok – tulajdonságok
* Speciális Spark2 – alapértékek
* Speciális Spark2 – env
* Speciális spark2 – kaptár-site – felülbírálás

Ha a konfigurációs értékek nem alapértelmezett készletét hozza létre, a frissítési előzmények láthatók.  Ez a konfigurációs előzmények segíthetnek megtekinteni, hogy melyik nem alapértelmezett konfiguráció optimális teljesítményű.

> [!NOTE]  
> Ha szeretné megtekinteni, de nem módosítja a Spark-fürt általános konfigurációs beállításait, válassza a **környezet** fület a legfelső szintű **Spark-feladatok felhasználói** felületén.

## <a name="configuring-spark-executors"></a>Spark-végrehajtók konfigurálása

A következő ábrán a legfontosabb Spark-objektumok láthatók: az illesztőprogram-program és a hozzá tartozó Spark-környezet, valamint a Fürtfelügyelő és az *n* feldolgozó csomópontok.  Minden feldolgozói csomópont tartalmaz egy végrehajtót, egy gyorsítótárat és egy *n* Task instances-példányt.

![Fürtözött objektumok](./media/apache-spark-settings/hdi-spark-architecture.png)

A Spark-feladatok a feldolgozói erőforrásokat, különösen a memóriát használják, ezért gyakori a Spark-konfigurációs értékek beállítása a feldolgozó csomópont-végrehajtók számára.

Három fő paraméter, amelyet gyakran a Spark-konfigurációk hangolására módosítanak az alkalmazási követelmények javítása érdekében, `spark.executor.instances` `spark.executor.cores` és `spark.executor.memory` . A végrehajtó egy Spark-alkalmazáshoz indított folyamat. Egy végrehajtó fut a munkavégző csomóponton, és felelős az alkalmazás feladataiért. A munkavégző csomópontok és a feldolgozó csomópontok mérete határozza meg a végrehajtók számát és a végrehajtó méretét. Ezeket az értékeket a rendszer a `spark-defaults.conf` fürt fő csomópontjain tárolja.  Ezeket az értékeket egy futó fürtben is szerkesztheti, ha az **Egyéni Spark-** alapértékek lehetőséget választja a Ambari webes felhasználói felületén.  A módosítások elvégzése után a felhasználói felület az összes érintett szolgáltatás **újraindítását** kéri.

> [!NOTE]  
> Ez a három konfigurációs paraméter konfigurálható a fürt szintjén (a fürtön futó összes alkalmazás esetében), valamint az egyes alkalmazásokhoz is.

A Spark-végrehajtók által használt erőforrásokkal kapcsolatos további információforrás a Spark-alkalmazás felhasználói felülete.  A felhasználói felületen a  **végrehajtók** a konfiguráció és a felhasznált erőforrások összefoglaló és részletes nézeteit jelenítik meg.  Döntse el, hogy módosítja-e a végrehajtók értékét a teljes fürtön, vagy a feladatok bizonyos készleteit.

![Spark-végrehajtók](./media/apache-spark-settings/apache-spark-executors.png)

Vagy használhatja a Ambari REST API a HDInsight és a Spark-fürt konfigurációs beállításainak programozott ellenőrzéséhez.  További információ a [githubon elérhető Apache AMBARI API-referenciában](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md)található.

A Spark számítási feladattól függően előfordulhat, hogy egy nem alapértelmezett Spark-konfiguráció optimálisabb Spark feladat-végrehajtásokat biztosít.  A nem alapértelmezett fürtkonfiguráció érvényesítéséhez végezzen teljesítményteszt-tesztelést a minta számítási feladatokkal.  Néhány gyakori paraméter, amelyeket érdemes lehet módosítani:

|Paraméter |Leírás|
|---|---|
|--NUM-végrehajtók|Beállítja a végrehajtók számát.|
|– végrehajtó – magok|Beállítja a magok számát az egyes végrehajtók számára. Javasoljuk, hogy a közepes méretű végrehajtók használatával más folyamatok is használják a rendelkezésre álló memória egy részét.|
|--végrehajtó – memória|Az egyes végrehajtók memória-méretének (halom méretének) szabályozása Apache Hadoop FONALon, és némi memóriát kell hagyni a végrehajtáshoz.|

Íme egy példa két feldolgozói csomópontra, különböző konfigurációs értékekkel:

![Két csomópontos konfiguráció](./media/apache-spark-settings/executor-configuration.png)

Az alábbi lista a fő Spark-végrehajtó memória paramétereit mutatja be.

|Paraméter |Leírás|
|---|---|
|spark.executor. Memory|Meghatározza a végrehajtó számára elérhető memória teljes mennyiségét.|
|Spark. Storage. memoryFraction|(alapértelmezett ~ 60%) a megőrzött RDD tárolására rendelkezésre álló memória mennyiségét határozza meg.|
|Spark. shuffle. memoryFraction|(alapértelmezett ~ 20%) meghatározza a shuffle számára fenntartott memória mennyiségét.|
|Spark. Storage. unrollFraction és Spark. Storage. safetyFraction|(összesen ~ 30%-a teljes memória) – ezeket az értékeket a Spark belsőleg használja, és nem módosítható.|

A fonal az egyes Spark-csomópontokon lévő tárolók által használt memória maximális számát szabályozza. Az alábbi ábrán a szálak konfigurációs objektumai és a Spark-objektumok közötti csomópont-kapcsolatok láthatók.

![A fonal Spark memóriájának kezelése](./media/apache-spark-settings/hdi-yarn-spark-memory.png)

## <a name="change-parameters-for-an-application-running-in-jupyter-notebook"></a>Jupyter Notebook futó alkalmazás paramétereinek módosítása

A HDInsight-alapú Spark-fürtök több összetevőt is tartalmaznak alapértelmezés szerint. Ezen összetevők mindegyike alapértelmezett konfigurációs értékeket tartalmaz, amelyeket szükség esetén felül lehet bírálni.

|Összetevő |Leírás|
|---|---|
|Spark Core|Spark Core, Spark SQL, Spark streaming API-k, GraphX és Apache Spark MLlib.|
|Anaconda|Python csomagkezelő.|
|Apache Livy|A HDInsight Spark-fürtre a távoli feladatok küldéséhez használt Apache Spark REST API.|
|Jupyter notebookok és Apache Zeppelin notebookok|Interaktív böngészőalapú felhasználói felület a Spark-fürttel való interakcióhoz.|
|ODBC-illesztő|A HDInsight-alapú Spark-fürtök összekapcsolása az üzleti intelligencia (BI) eszközeivel, például a Microsoft Power BI és a tabló használatával.|

A Jupyter Notebook futó alkalmazások esetében a `%%configure` paranccsal a saját jegyzetfüzetből végezheti el a konfigurációs módosításokat. Ezek a konfigurációs változások a notebook-példányról futtatott Spark-feladatokra lesznek alkalmazva. Az első kódrészlet futtatása előtt végezze el az ilyen módosításokat az alkalmazás elején. A rendszer a módosított konfigurációt alkalmazza a Livy-munkamenetre a létrehozásakor.

> [!NOTE]  
> Ha az alkalmazás egy későbbi szakaszában szeretné módosítani a konfigurációt, használja a `-f` (Force) paramétert. Azonban az alkalmazás minden folyamata el fog veszni.

Az alábbi kód azt mutatja be, hogyan lehet módosítani egy Jupyter Notebook futó alkalmazás konfigurációját.

```
%%configure
{"executorMemory": "3072M", "executorCores": 4, "numExecutors":10}
```

## <a name="conclusion"></a>Összegzés

Az alapvető konfigurációs beállítások figyelésével biztosíthatja, hogy a Spark-feladatok kiszámítható és elvégezhető módon fussanak. Ezek a beállítások segítenek meghatározni a legjobb Spark-fürt konfigurációját az adott számítási feladatokhoz.  Emellett figyelnie kell a hosszan futó és az erőforrás-igényes Spark-feladatok végrehajtását.  A nem megfelelő konfigurációktól, például helytelen méretű végrehajtókkal kapcsolatos memória-nyomást a leggyakoribb kihívások középpontba kerül. Emellett a hosszan futó műveleteket és a feladatokat, amelyek Descartes-alapú műveleteket eredményeznek.

## <a name="next-steps"></a>Következő lépések

* [A HDInsight-ben elérhető összetevők és verziók Apache Hadoop?](../hdinsight-component-versioning.md)
* [Apache Spark-fürt erőforrásainak kezelése a HDInsight-ben](apache-spark-resource-manager.md)
* [Apache Spark konfiguráció](https://spark.apache.org/docs/latest/configuration.html)
* [Apache Spark futtatása Apache Hadoop-SZÁLon](https://spark.apache.org/docs/latest/running-on-yarn.html)
