---
title: -Kernelek Jupyter notebook az Azure HDInsight Spark-fürtökön
description: További információ a PySpark PySpark3 és Spark kernelek Jupyter notebookokhoz elérhető az Azure HDInsight Spark-fürtökkel.
keywords: a spark, jupyter spark a jupyter notebook
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 05/27/2019
ms.author: hrasheed
ms.openlocfilehash: b2ae24c0449b009db6fcecdd8a1366ea5154629a
ms.sourcegitcommit: 25a60179840b30706429c397991157f27de9e886
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/28/2019
ms.locfileid: "66257792"
---
# <a name="kernels-for-jupyter-notebook-on-apache-spark-clusters-in-azure-hdinsight"></a>-Kernelek Jupyter notebook az Azure HDInsight az Apache Spark-fürtök 

HDInsight Spark-fürtökön is használhatja a Jupyter notebookkal a kernelt biztosítanak [Apache Spark](https://spark.apache.org/) az alkalmazások teszteléséhez. A rendszermag egy olyan program, fut, és a kód értelmezi. A három kernelt a következők:

- **PySpark** – Python2 írt alkalmazások esetén.
- **PySpark3** – Python3 írt alkalmazások esetén.
- **A Spark** – scalában írt alkalmazások esetén.

Ebből a cikkből megismerheti, hogyan használhatja ezeket kernelekkel és azok használatának előnyei.

## <a name="prerequisites"></a>Előfeltételek

Az Apache Spark-fürt a HDInsight. További útmutatásért lásd: [Apache Spark-fürt létrehozása az Azure HDInsightban](apache-spark-jupyter-spark-sql.md).

## <a name="create-a-jupyter-notebook-on-spark-hdinsight"></a>A Spark HDInsight Jupyter notebook létrehozása

1. Az a [az Azure portal](https://portal.azure.com/), válassza ki a Spark-fürtöt.  Lásd: [fürtök listázása és megjelenítése](../hdinsight-administer-use-portal-linux.md#showClusters) vonatkozó utasításokat. A **áttekintése** megnyílik megtekintéséhez.

2. Az a **áttekintése** nézet a **fürt irányítópultjai** jelölje ki **Jupyter notebook**. Ha a rendszer felkéri rá, adja meg a fürthöz tartozó rendszergazdai hitelesítő adatokat.

    ![A Jupyter notebook Spark](./media/apache-spark-jupyter-notebook-kernels/hdinsight-spark-open-jupyter-interactive-spark-sql-query.png "Spark a Jupyter notebook") 
  
   > [!NOTE]  
   > A Jupyter notebook Spark-fürtön a böngészőben a következő URL-cím megnyitásával is elérheti. Cserélje le a **CLUSTERNAME** elemet a fürt nevére:
   >
   > `https://CLUSTERNAME.azurehdinsight.net/jupyter`

3. Válassza ki **új**, majd válassza ki vagy **Pyspark**, **PySpark3**, vagy **Spark** , hozzon létre egy notebookot. A Spark kernel Scala-alkalmazások, PySpark kernelt Python2-alkalmazásokhoz és PySpark3 kernel használata Python3-alkalmazásokhoz.
   
    ![Kernelek Jupyter notebookokhoz Spark](./media/apache-spark-jupyter-notebook-kernels/kernel-jupyter-notebook-on-spark.png "kernelek Jupyter notebookokhoz Spark") 

4. A kiválasztott kernel jegyzetfüzet nyílik meg.

## <a name="benefits-of-using-the-kernels"></a>A kernelekkel használatának előnyei

Az alábbiakban néhány előnyei az új kernelekkel a Jupyter notebookot HDInsight Spark-fürtökön.

- **Környezetek előbeállítást**. A **PySpark**, **PySpark3**, vagy a **Spark** kernelekkel, nem kell explicit módon beállítva a Spark- vagy Hive-környezetek, az alkalmazások használatának megkezdése előtt. Ezeket a rendszer alapértelmezés szerint elérhető. Ezek a környezetek a következők:
   
  * **sc** – a Spark környezet
  * **kontext sqlContext** – struktúráját környezethez.
   
    Így nem kell a környezetek beállítása a következőhöz hasonló utasításokat:
   
         sc = SparkContext('yarn-client')
         sqlContext = HiveContext(sc)
   
    Ehelyett közvetlenül használhatja az előre beállított környezetekről az alkalmazásban.

- **Cella magics**. A PySpark kernel tartalmaz néhány előre meghatározott "magics", amelyek különleges parancsok, amelyek segítségével meghívhatja a `%%` (például `%%MAGIC` `<args>`). A varázsparancs kell lennie az első szótól egy kódcellába, és lehetővé teszik a tartalom több sornyi. Magic szót kell lennie az első szótól a cellában. Hozzáadása a Magic Quadrant, még akkor is, a megjegyzéseket, mielőtt bármi hibát okoz.     Magics további információkért lásd: [Itt](https://ipython.readthedocs.org/en/stable/interactive/magics.html).
   
    Az alábbi táblázat a különböző magics a kernelekkel keresztül érhető el.

   | Magic Quadrant | Példa | Leírás |
   | --- | --- | --- |
   | help |`%%help` |Létrehoz egy táblát az összes rendelkezésre álló magics példa és leírás |
   | Adatai |`%%info` |A jelenlegi Livy-végpont kimenetek munkamenet-információk |
   | Konfigurálása |`%%configure -f`<br>`{"executorMemory": "1000M"`,<br>`"executorCores": 4`} |Konfigurálja a paramétereket a munkamenet létrehozásához. A kényszerítési jelzőt (-f) megadása kötelező, ha a munkamenet már létrejött, amely biztosítja, hogy a munkamenet eltávolítja és újra létre kell hozni. Tekintse meg [Livy a POST /sessions kérelem törzse](https://github.com/cloudera/livy#request-body) érvényes paramétereinek listáját. Paramétereket kell átadni, egy JSON-karakterlánc, és későbbinek kell lennie a következő sorban a Magic Quadrant, ahogyan az a példában oszlop. |
   | sql |`%%sql -o <variable name>`<br> `SHOW TABLES` |Futtatják a Hive-lekérdezést a kontext sqlContext ellen. Ha a `-o` paramétert, a lekérdezés eredménye a rendszer megőrzi a %% helyi Python-környezetben egy [Pandas](https://pandas.pydata.org/) dataframe. |
   | helyi |`%%local`<br>`a=1` |A következő sorokban minden kód helyileg hajtja végre. Kód akár a kernelt használja függetlenül érvényes Python2-kódot kell lennie. Igen, akkor is, ha a kiválasztott **PySpark3** vagy **Spark** kernelekkel létrehozásakor a notebook használatakor a `%%local` magic egy cellába, a cella csak rendelkeznie kell érvényes Python2-kódot. |
   | logs |`%%logs` |Az aktuális Livy-munkamenet jelenít meg a naplókat. |
   | delete |`%%delete -f -s <session number>` |A jelenlegi Livy-végpont egy adott munkamenet törlése. Maga a kernel kezdeményezett munkamenet nem törölhető. |
   | tisztítás |`%%cleanup -f` |Törli a jelenlegi Livy-végpont gazdanevét a jegyzetfüzet-munkamenet az összes előadáshoz. A kényszerített jelző -f megadása kötelező. |

   > [!NOTE]  
   > A magics hozzáadta a PySpark kernel, mellett is használhatja a [beépített IPython magics](https://ipython.org/ipython-doc/3/interactive/magics.html#cell-magics), többek között `%%sh`. Használhatja a `%%sh` futtathat szkripteket és kódblokkot az a fürt átjárócsomópontjával Magic Quadrant.

- **Vizualizáció automatikus**. A Pyspark kernel automatikusan megjeleníti a Hive- és SQL-lekérdezések kimenetének. Választhat a különböző típusú megjelenítések, beleértve a tábla, torta, vonal, terület, sávon.

## <a name="parameters-supported-with-the-sql-magic"></a>A támogatott paraméterek a %% sql Magic Quadrant
A `%%sql` Magic Quadrant támogatja a különböző paraméterek, amelyek segítségével szabályozhatja, amelyet a lekérdezések futtatásakor kapott kimenetre típusa. A következő táblázat felsorolja a kimenetet.

| Paraméter | Példa | Leírás |
| --- | --- | --- |
| -o |`-o <VARIABLE NAME>` |Használja ezt a paramétert a lekérdezés eredménye megőrizni a a %% helyi Python-környezetet, mint egy [Pandas](https://pandas.pydata.org/) dataframe. A dataframe változó nevét, adja meg a változó neve. |
| -q |`-q` |Ezzel kikapcsolja a Vizualizációk a cellával kapcsolatban. Ha nem szeretné autovisualize egy cella tartalmát, és csak adathalmazaként rögzítené azt szeretné, majd használja `-q -o <VARIABLE>`. Tiltsa le az vizualizációkat rögzítse az eredményeket nélkül szeretne (például egy SQL-lekérdezés futtatása például egy `CREATE TABLE` utasítás), használja `-q` megadása nélkül egy `-o` argumentum. |
| -m |`-m <METHOD>` |Ahol **METÓDUS** vagy **igénybe** vagy **minta** (alapértelmezett érték a **igénybe**). Ha a módszer **igénybe**, a kernel MAXROWS (később az alábbi táblázatban ismertetett) által megadott adatok eredményhalmaz tetején elemeket választja ki. Ha a módszer **minta**, a kernel-elemek a következők szerint az adatkészlet véletlenszerűen minták `-r` paramétert, a következő táblázatban leírt. |
| -r |`-r <FRACTION>` |Itt **TÖREDÉK** 0,0 és 1,0 közötti lebegőpontos szám. Ha az SQL-lekérdezést a minta módszer `sample`, majd a kernel véletlenszerűen minták a megadott töredék meg az eredményhalmaz elemét. Ha például egy SQL-lekérdezés futtatása a argumentumokkal `-m sample -r 0.01`, majd az eredményül kapott sorokat a 1 % véletlenszerűen történik az adatgyűjtés. |
| -n |`-n <MAXROWS>` |**MAXROWS** egy egész érték. A kernel kimeneti sorok számát korlátozza **MAXROWS**. Ha **MAXROWS** el például egy negatív szám **-1**, majd az eredményhalmaz sorok száma nem korlátozódik. |

**Példa**

    %%sql -q -m sample -r 0.1 -n 500 -o query2
    SELECT * FROM hivesampletable

A fenti utasítást a következőket teszi:

* Kiválasztja az összes rekordot **hivesampletable**.
* Mivel - q, autovisualization kikapcsolása.
* Mivel `-m sample -r 0.1 -n 500` véletlenszerűen 10 %-a hivesampletable sora minták és a eredménykészlet 500 sor mérete korlátozza.
* Végül mert használtuk `-o query2` nevű dataframe-be is írja a kimeneti **lekérdezés2**.

## <a name="considerations-while-using-the-new-kernels"></a>Az új kernelekkel használata során megfontolandó szempontok

Bármelyik kernelt használ, a fürt erőforrásainak elhagyná a notebookok futó használ.  Az alábbi kernelekkel a környezetek előre van állítva, mert a notebookok egyszerűen kilép a környezet nem kill, és ezért a fürt erőforrásainak továbbra is használhatják. Bevált gyakorlat, hogy használja a **zárja be és Halt** lehetőséget a notebookot a **fájl** menü, amikor végzett, a jegyzetfüzetet, amely megszakítja a környezetet, és majd kilép a notebook használatával.

## <a name="where-are-the-notebooks-stored"></a>A notebookok tárolására?

Ha a fürt Azure Storage, az alapértelmezett tárfiókot használja, a Jupyter notebooks menti, és storage-fiók a **/HdiNotebooks** mappát.  Notebookok, szöveges fájlok és mappák belül hoz létre a Jupyter érhetők el a storage-fiókból.  Például, ha a Jupyter használatával hozzon létre egy mappát **SajátMappa** és a egy Jegyzetfüzet **myfolder/mynotebook.ipynb**, elérheti, hogy a notebook `/HdiNotebooks/myfolder/mynotebook.ipynb` a tárfiókon belül.  Az ellenkezője is igaz, vagyis fel kell töltenie egy jegyzetfüzetet közvetlenül a storage-fiókjában, ha `/HdiNotebooks/mynotebook1.ipynb`, valamint Jupyterről származó látható tartozó notebookról is.  Notebookok maradnak a tárfiókban, a fürt törlése után is.

> [!NOTE]  
> HDInsight-fürtöket az Azure Data Lake Storage az alapértelmezett tárolóként ne tárolja a notebookok társított storage-ban.

A notebookok a tárfiókba lesznek mentve módja kompatibilis [Apache Hadoop HDFS](https://hadoop.apache.org/docs/r1.2.1/hdfs_design.html). Ha tehát Ön SSH-t a fürt használhatja kezelési parancsok fájlt, az alábbi kódrészletben látható módon:

    hdfs dfs -ls /HdiNotebooks                            # List everything at the root directory – everything in this directory is visible to Jupyter from the home page
    hdfs dfs –copyToLocal /HdiNotebooks                   # Download the contents of the HdiNotebooks folder
    hdfs dfs –copyFromLocal example.ipynb /HdiNotebooks   # Upload a notebook example.ipynb to the root folder so it’s visible from Jupyter

Függetlenül attól, hogy a fürt használja az Azure Storage vagy az Azure Data Lake Storage az alapértelmezett tárfiók, a notebookok is tárolja, a fürt átjárócsomópontjával, `/var/lib/jupyter`.

## <a name="supported-browser"></a>Támogatott böngésző

Csak a Google Chrome Jupyter notebookok a HDInsight Spark-fürtökön támogatott.

## <a name="feedback"></a>Visszajelzés

Az új kernelekkel fázis fejlődő és idővel részletes lesz. Ez is jelentheti, hogy API-k módosulhatnak, mivel ezek kernelekkel részletes. Örömmel ezen új kernelekkel használatakor rendelkező visszajelzését. Ez akkor hasznos, ezek kernelekkel végleges kialakításában. A megjegyzések/visszajelzését hagyhatja a **visszajelzés** szakaszban Ez a cikk alján.

## <a name="seealso"></a>Lásd még:
* [Áttekintés: Az Apache Spark on Azure HDInsight](apache-spark-overview.md)

### <a name="scenarios"></a>Forgatókönyvek
* [Az Apache Spark és BI: Spark on HDInsight használatával, BI-eszközökkel interaktív adatelemzés végrehajtása](apache-spark-use-bi-tools.md)
* [Az Apache Spark és Machine Learning: A Spark használata a HDInsight HVAC-adatok épület-hőmérséklet elemzésére](apache-spark-ipython-notebook-machine-learning.md)
* [Az Apache Spark és Machine Learning: A HDInsight Spark használata az élelmiszervizsgálati eredmények előrejelzésére](apache-spark-machine-learning-mllib-ipython.md)
* [A webhelynapló elemzése a HDInsight az Apache Spark használatával](apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-run-applications"></a>Alkalmazások létrehozása és futtatása
* [Önálló alkalmazás létrehozása a Scala használatával](apache-spark-create-standalone-application.md)
* [Feladatok távoli futtatása egy Apache Spark-fürtön az Apache Livy használatával](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Eszközök és bővítmények
* [Az IntelliJ IDEA HDInsight-eszközei beépülő moduljának használata Spark Scala-alkalmazások létrehozásához és elküldéséhez](apache-spark-intellij-tool-plugin.md)
* [Az Apache Spark-alkalmazások távoli hibakeresése az IntelliJ IDEA HDInsight-eszközei beépülő használata](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Az Apache Zeppelin notebookok használata a HDInsight Apache Spark-fürt](apache-spark-zeppelin-notebook.md)
* [Külső csomagok használata Jupyter notebookokkal](apache-spark-jupyter-notebook-use-external-packages.md)
* [A Jupyter telepítése a számítógépre, majd csatlakozás egy HDInsight Spark-fürthöz](apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Erőforrások kezelése
* [Apache Spark-fürt erőforrásainak kezelése az Azure HDInsightban](apache-spark-resource-manager.md)
* [Apache Spark-fürtön futó feladatok nyomon követése és hibakeresése a HDInsightban](apache-spark-job-debugging.md)
