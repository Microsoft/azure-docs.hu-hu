---
title: Kernelek Jupyter Notebook Azure HDInsight Spark-fürtökön
description: Ismerkedjen meg a PySpark, a PySpark3 és a Spark kernelekkel az Azure HDInsight-alapú Spark-fürtökkel elérhető Jupyter Notebookhoz.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive,hdiseo17may2017,seoapr2020
ms.date: 04/24/2020
ms.openlocfilehash: ef2bc5e00779200e5447c8829a437824657a2227
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "104865978"
---
# <a name="kernels-for-jupyter-notebook-on-apache-spark-clusters-in-azure-hdinsight"></a>Kernelek Jupyter Notebook Apache Spark-fürtökön az Azure HDInsight

A HDInsight Spark-fürtök olyan kerneleket biztosítanak, amelyeket az alkalmazások teszteléséhez a [Apache Spark](./apache-spark-overview.md) Jupyter notebook használhat. A kernel egy olyan program, amely futtatja és értelmezi a kódot. A három kernel a következők:

- **PySpark** – a Python2-ben írt alkalmazások esetében.
- **PySpark3** – a Python3-ben írt alkalmazások esetében.
- **Spark** – a Scala-ben írt alkalmazások esetében.

Ebből a cikkből megtudhatja, hogyan használhatja ezeket a rendszermagokat és a használatának előnyeit.

## <a name="prerequisites"></a>Előfeltételek

Egy Apache Spark-fürt a HDInsight-ben. További útmutatásért lásd: [Apache Spark-fürt létrehozása az Azure HDInsightban](apache-spark-jupyter-spark-sql.md).

## <a name="create-a-jupyter-notebook-on-spark-hdinsight"></a>Jupyter Notebook létrehozása a Spark HDInsight

1. A [Azure Portal](https://portal.azure.com/)válassza ki a Spark-fürtöt.  Lásd: [fürtök listázása és megjelenítése](../hdinsight-administer-use-portal-linux.md#showClusters) az utasításokhoz. Megnyílik az **Áttekintés** nézet.

2. Az **Áttekintés** nézet **fürt irányítópultok** mezőjében válassza a **Jupyter notebook** lehetőséget. Ha a rendszer felkéri rá, adja meg a fürthöz tartozó rendszergazdai hitelesítő adatokat.

    :::image type="content" source="./media/apache-spark-jupyter-notebook-kernels/hdinsight-spark-open-jupyter-interactive-spark-sql-query.png " alt-text="Jupyter Notebook on Apache Spark" border="true":::
  
   > [!NOTE]  
   > Az Jupyter Notebook a Spark-fürtön is elérheti, ha megnyitja a következő URL-címet a böngészőben. Cserélje le a **CLUSTERNAME** nevet a fürt nevére:
   >
   > `https://CLUSTERNAME.azurehdinsight.net/jupyter`

3. Válassza az **új** lehetőséget, majd válassza ki a **Pyspark**, a **PySpark3** vagy a **Spark** elemet egy jegyzetfüzet létrehozásához. Használja a PySpark kernelt a Scala-alkalmazásokhoz, a Python2-alkalmazásokhoz és a PySpark3 kernelhez a Python3-alkalmazásokhoz.

    :::image type="content" source="./media/apache-spark-jupyter-notebook-kernels/kernel-jupyter-notebook-on-spark.png " alt-text="Kernelek Jupyter Notebook a Sparkban" border="true":::

4. Megnyílik egy jegyzetfüzet a kiválasztott kernelrel.

## <a name="benefits-of-using-the-kernels"></a>A kernelek használatának előnyei

Íme néhány előnye az új kernelek használatában Jupyter Notebook a Spark HDInsight-fürtökön.

- **Előre beállított környezetek**. A  **PySpark**, a **PySpark3** vagy a **Spark** kernelek esetében nem kell explicit módon beállítania a Spark vagy a kaptár környezetét az alkalmazásokkal való munka megkezdése előtt. Ezek a kontextusok alapértelmezés szerint elérhetők. Ezek a kontextusok a következők:

  - **SC** – Spark-környezethez
  - **sqlContext** – a kaptár kontextusához

    Ezért **nem** kell az alábbihoz hasonló utasításokat futtatnia a kontextusok beállításához:

    ```sql
    sc = SparkContext('yarn-client')
    sqlContext = HiveContext(sc)
    ```

    Ehelyett közvetlenül is használhatja az előre definiált kontextusokat az alkalmazásban.

- **Cella-varázslatok**. A PySpark kernel néhány előre definiált "varázslatot" tartalmaz, amelyek olyan speciális parancsok, amelyeket meghívhat `%%` (például `%%MAGIC` `<args>` ). A Magic parancsnak a kód első szava kell lennie, és több sornyi tartalmat is engedélyeznie kell. A bűvös szó csak a cella első szava lehet. A Magic, még a megjegyzések előtt bármit is megadhat, ami hibát okoz.     A Magics szolgáltatással kapcsolatos további információkért lásd [itt](https://ipython.readthedocs.org/en/stable/interactive/magics.html).

    A következő táblázat a kerneleken keresztül elérhető különböző varázslatokat sorolja fel.

   | Magic | Példa | Leírás |
   | --- | --- | --- |
   | segítség |`%%help` |Létrehoz egy táblázatot az összes elérhető varázslatról a példával és a leírással |
   | információ |`%%info` |A jelenlegi Livy-végponthoz tartozó munkamenet-információk kimenete |
   | Konfigurálás |`%%configure -f`<br>`{"executorMemory": "1000M"`,<br>`"executorCores": 4`} |Konfigurálja a munkamenetek létrehozásához szükséges paramétereket. A kényszerített jelző ( `-f` ) megadása kötelező, ha már létrejött egy munkamenet, amely biztosítja a munkamenet eldobását és újbóli létrehozását. Tekintse meg az érvényes paraméterek listáját a [LIVY post/Sessions kérelem törzsében](https://github.com/cloudera/livy#request-body) . A paramétereket JSON-karakterláncként kell átadni, és a Magic után a következő sorban kell szerepelniük, ahogy az a példában látható oszlopban látható. |
   | sql |`%%sql -o <variable name>`<br> `SHOW TABLES` |Struktúra-lekérdezést hajt végre a sqlContext. Ha a `-o` paramétert átadja, a lekérdezés eredménye a (z)%% helyi Python-kontextusban marad a [pandák](https://pandas.pydata.org/) dataframe. |
   | helyi |`%%local`<br>`a=1` |A későbbi sorokban lévő összes kód helyileg lesz végrehajtva. A kódnak érvényes Python2-kódnak kell lennie, függetlenül attól, hogy melyik kernelt használja. Tehát akkor is, ha a **PySpark3** vagy a **Spark** kernelt választotta a jegyzetfüzet létrehozásakor, ha a `%%local` magict egy cellában használja, akkor a cellának csak érvényes Python2-kóddal kell rendelkeznie. |
   | naplók |`%%logs` |Megjeleníti a naplókat az aktuális Livy-munkamenethez. |
   | delete |`%%delete -f -s <session number>` |Törli az aktuális Livy-végpont adott munkamenetét. A rendszermaghoz indított munkamenet nem törölhető. |
   | tisztítás |`%%cleanup -f` |Törli az aktuális Livy-végpont összes munkamenetét, beleértve a jegyzetfüzetek munkamenetét is. A Force jelző-f megadása kötelező. |

   > [!NOTE]  
   > A PySpark kernel által hozzáadott varázslatok mellett használhatja a [beépített IPython-varázslatokat](https://ipython.org/ipython-doc/3/interactive/magics.html#cell-magics)is, beleértve a következőt: `%%sh` . A `%%sh` Magic használatával parancsfájlokat és blokkokat futtathat a fürt átjárócsomóponthoz.

- **Automatikus vizualizáció**. A Pyspark-kernel automatikusan megjeleníti a struktúra és az SQL-lekérdezések kimenetét. Számos különböző típusú vizualizáció közül választhat, például a tábla, a torta, a vonal, a terület és a sáv.

## <a name="parameters-supported-with-the-sql-magic"></a>A (z)%% SQL Magic által támogatott paraméterek

A `%%sql` Magic különböző paramétereket támogat, amelyek segítségével szabályozhatja a lekérdezések futtatásakor kapott kimenet típusát. A következő táblázat felsorolja a kimenetet.

| Paraméter | Példa | Leírás |
| --- | --- | --- |
| -o |`-o <VARIABLE NAME>` |Ezzel a paraméterrel megtarthatja a lekérdezés eredményét a (z)%% helyi Python kontextusban, [pandák](https://pandas.pydata.org/) dataframe. A dataframe változó neve a megadott név. |
| -q |`-q` |Ezzel a paraméterrel kikapcsolhatja a cella vizualizációit. Ha nem szeretné megjeleníteni a cella tartalmát, és csak dataframe szeretné rögzíteni, használja a következőt: `-q -o <VARIABLE>` . Ha az eredmények rögzítése nélkül szeretné kikapcsolni a vizualizációkat (például egy SQL-lekérdezés, például egy utasítás futtatásához `CREATE TABLE` ), használja az `-q` argumentum megadása nélkül `-o` . |
| – m |`-m <METHOD>` |Ahol a **metódus** vagy a **Take** vagy a **Sample** (alapértelmezett **érték).** Ha a metódus értéke **`take`** , a kernel a MAXROWS által meghatározott eredmény-adathalmaz elejétől (a táblázat későbbi részében leírtak szerint) kiválasztja az elemeket. Ha a metódus **minta**, a kernel véletlenszerűen mintákat hoz létre az adatkészletből a `-r` paraméternek megfelelően, a következő táblázatban ismertetett módon. |
| -r |`-r <FRACTION>` |Itt a **frakció** egy 0,0 és 1,0 közötti lebegőpontos szám. Ha az SQL-lekérdezés mintavételi módszere `sample` , akkor a kernel véletlenszerűen megkeresi az eredményhalmaz elemeinek megadott részét. Ha például SQL-lekérdezést futtat az argumentumokkal `-m sample -r 0.01` , akkor az eredmény sorainak 1%-a véletlenszerűen lett kiválasztva. |
| -n |`-n <MAXROWS>` |A **MAXROWS** egész érték. A kernel korlátozza az **MAXROWS** kimeneti sorok számát. Ha a **MAXROWS** negatív szám, például **-1**, akkor az eredményhalmaz sorainak száma nem korlátozott. |

**Példa**

```sql
%%sql -q -m sample -r 0.1 -n 500 -o query2
SELECT * FROM hivesampletable
```

A fenti utasítás a következő műveleteket végzi el:

- A **hivesampletable** összes rekordjának kijelölése.
- Mivel a-q-t használjuk, kikapcsolja az autovizualizációt.
- A használat során `-m sample -r 0.1 -n 500` véletlenszerűen mintákat vesz fel a hivesampletable sorainak 10%-ában, és az eredményhalmaz méretét 500 sorra korlátozza.
- Végül, mivel használtuk, `-o query2` a kimenetet egy **query2** nevű dataframe menti.

## <a name="considerations-while-using-the-new-kernels"></a>Az új kernelek használatának szempontjai

A használt kernelek, így a futtatott jegyzetfüzetek a fürt erőforrásait használják.  Ezekkel a kernelekkel, mivel a kontextusok előre vannak beállítva, egyszerűen kilépve a jegyzetfüzetek nem öli meg a környezetet. Így a fürt erőforrásai továbbra is használatban vannak. A notebook **fájl** menüjében a **Bezárás és a megállás** lehetőség használatával végezheti el a futtatást. A Bezárás megöli a környezetet, majd kilép a jegyzetfüzetből.

## <a name="where-are-the-notebooks-stored"></a>Hol tárolódnak a jegyzetfüzetek?

Ha a fürt az Azure Storage-t használja alapértelmezett Storage-fiókként, a rendszer a Jupyter-jegyzetfüzeteket a **/HdiNotebooks** mappa alatt menti a Storage-fiókba.  A Jupyter belül létrehozott jegyzetfüzetek, szövegfájlok és mappák elérhetők a Storage-fiókból.  Ha például a Jupyter használatával hoz létre egy mappát **`myfolder`** és egy jegyzetfüzet **MyFolder/mynotebook. ipynb**, a jegyzetfüzetet `/HdiNotebooks/myfolder/mynotebook.ipynb` a Storage-fiókon belül is elérheti.  A fordított érték szintén igaz, azaz ha közvetlenül a Storage-fiókjába tölt fel egy jegyzetfüzetet `/HdiNotebooks/mynotebook1.ipynb` , a jegyzetfüzet a Jupyter is látható.  A jegyzetfüzetek a fürt törlése után is a Storage-fiókban maradnak.

> [!NOTE]  
> Az alapértelmezett tárolóval rendelkező Azure Data Lake Storage HDInsight-fürtök nem tárolnak jegyzetfüzeteket a társított tárolóban.

A jegyzetfüzetek a Storage-fiókba való mentésének módja kompatibilis a [Apache HADOOP HDFS](https://hadoop.apache.org/docs/r1.2.1/hdfs_design.html). Ha a fürtbe SSH-t használ, használhatja a fájlkezelési parancsokat:

| Parancs | Leírás |
|---------|-------------|
| `hdfs dfs -ls /HdiNotebooks` | # Összes listázása a gyökérkönyvtárban – az ebben a könyvtárban található összes elem látható a Jupyter a kezdőlapon |
| `hdfs dfs –copyToLocal /HdiNotebooks` | # A HdiNotebooks mappa tartalmának letöltése|
| `hdfs dfs –copyFromLocal example.ipynb /HdiNotebooks` | # Töltsön fel egy jegyzetfüzetet, például a ipynb a gyökérkönyvtárba, hogy a Jupyter látható legyen. |

Azt határozza meg, hogy a fürt az Azure Storage-t használja-e, vagy Azure Data Lake Storage alapértelmezett Storage-fiókként, a jegyzetfüzeteket a fürt átjárócsomóponthoz is menti a rendszer `/var/lib/jupyter` .

## <a name="supported-browser"></a>Támogatott böngésző

A Spark HDInsight-fürtökön található Jupyter-jegyzetfüzetek csak a Google Chrome-ban támogatottak.

## <a name="suggestions"></a>Javaslatok

Az új kernelek folyamatosan fejlődnek, és idővel kiforrnak. Így az API-k megváltozhatnak, mivel ezek a kernelek kiforrtak. Nagyra értékeljük az új kernelek használata során felmerülő visszajelzéseket. A visszajelzés hasznos lehet ezen kernelek végleges kiadásának alakításában. A megjegyzéseit/visszajelzéseit a cikk alján található **visszajelzések** szakaszban hagyhatja.

## <a name="next-steps"></a>Következő lépések

- [Overview: Apache Spark on Azure HDInsight (Áttekintés: Apache Spark on Azure HDInsight)](apache-spark-overview.md)
- [Apache Zeppelin notebookok használata Apache Spark-fürttel a HDInsight-on](apache-spark-zeppelin-notebook.md)
- [Külső csomagok használata Jupyter notebookokkal](apache-spark-jupyter-notebook-use-external-packages.md)
- [A Jupyter telepítése a számítógépre, majd csatlakozás egy HDInsight Spark-fürthöz](apache-spark-jupyter-notebook-install-locally.md)
