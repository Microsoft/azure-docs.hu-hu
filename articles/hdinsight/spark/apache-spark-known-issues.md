---
title: Apache Spark-fürttel kapcsolatos problémák elhárítása az Azure HDInsight
description: Ismerkedjen meg az Azure HDInsight Apache Spark-fürtökkel kapcsolatos problémákról és azok megoldásáról.
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: troubleshooting
ms.date: 08/15/2019
ms.openlocfilehash: c92b55d3ac7f4476b7b74d25b40150a74c6ea1cf
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98930163"
---
# <a name="known-issues-for-apache-spark-cluster-on-hdinsight"></a>Ismert problémák a Apache Spark-fürtön a HDInsight-on

Ez a dokumentum a HDInsight Spark nyilvános előzetes verziójával kapcsolatos összes ismert problémát nyomon követi.  

## <a name="apache-livy-leaks-interactive-session"></a>Apache Livy-szivárgások interaktív munkamenete
Amikor az [Apache Livy](https://livy.incubator.apache.org/) újraindul (az [Apache Ambari](https://ambari.apache.org/) vagy a átjárócsomóponthoz 0 virtuális gép újraindítása miatt) egy interaktív munkamenettel még életben van, egy interaktív feladatütemezés kerül kiszivárgásra. Ennek eredményeképpen az új feladatok elhelyezhetők az elfogadott állapotba.

**Kockázatcsökkentő**

A probléma megkerüléséhez kövesse az alábbi eljárást:

1. SSH-t a átjárócsomóponthoz. További információk: [Az SSH használata HDInsighttal](../hdinsight-hadoop-linux-use-ssh-unix.md).

2. Futtassa a következő parancsot a Livy-on keresztül indított interaktív feladatok alkalmazás-azonosítóinak megkereséséhez.

   ```bash
   yarn application –list
   ```

    Az alapértelmezett feladatok neve Livy, ha a feladatok olyan interaktív Livy-munkamenettel kezdődtek, amelyben nincsenek megadva explicit nevek. A [Jupyter notebook](https://jupyter.org/)által elindított Livy-munkamenethez a feladatok neve a következővel kezdődik: `remotesparkmagics_*` .

3. Futtassa a következő parancsot a feladatok leöléséhez.

   ```bash
   yarn application –kill <Application ID>
   ```

Elindul az új feladatok futtatása.

## <a name="spark-history-server-not-started"></a>A Spark History-kiszolgáló nincs elindítva
A Spark History-kiszolgáló nem indul el automatikusan a fürt létrehozása után.  

**Kockázatcsökkentő**

Manuálisan indítsa el az előzmények kiszolgálót a Ambari-ből.

## <a name="permission-issue-in-spark-log-directory"></a>Engedélyezési probléma a Spark-napló címtárában
a hdiuser a következő hibaüzenetet kapja, amikor elküld egy feladatot a Spark-Submit paranccsal:

```
java.io.FileNotFoundException: /var/log/spark/sparkdriver_hdiuser.log (Permission denied)
```

És nem írt be illesztőprogram-naplót.

**Kockázatcsökkentő**

1. Adja hozzá a hdiuser a Hadoop-csoporthoz.
2. Adja meg a 777 engedélyeket a/var/log/Spark a fürt létrehozása után.
3. Frissítse a Spark-napló helyét a Ambari használatával a 777 engedélyekkel rendelkező könyvtárba.  
4. Futtassa a Spark-Submit parancsot sudo-ként.  

## <a name="spark-phoenix-connector-is-not-supported"></a>Spark-Phoenix összekötő nem támogatott

A HDInsight Spark-fürtök nem támogatják az Spark-Phoenix-összekötőt.

**Kockázatcsökkentő**

Ehelyett a Spark-HBase-összekötőt kell használnia. Az utasításokért lásd: [az Spark-HBase-összekötő használata](https://web.archive.org/web/20190112153146/https://blogs.msdn.microsoft.com/azuredatalake/2016/07/25/hdinsight-how-to-use-spark-hbase-connector/).

## <a name="issues-related-to-jupyter-notebooks"></a>Jupyter-jegyzetfüzetekkel kapcsolatos problémák

A Jupyter-jegyzetfüzetekkel kapcsolatos ismert problémák a következők.

### <a name="notebooks-with-non-ascii-characters-in-filenames"></a>Nem ASCII-karaktereket tartalmazó jegyzetfüzetek a fájlnevekben

Ne használjon nem ASCII-karaktereket Jupyter Notebook fájlnevekben. Ha a Jupyter felhasználói felületén próbál meg feltölteni egy fájlt, amely nem ASCII fájlnévvel rendelkezik, hibaüzenet nélkül meghiúsul. A Jupyter nem teszi lehetővé a fájl feltöltését, de nem mutat látható hibát sem.

### <a name="error-while-loading-notebooks-of-larger-sizes"></a>Hiba történt a nagyobb méretű jegyzetfüzetek betöltésekor

Előfordulhat, **`Error loading notebook`** hogy a nagyobb méretű jegyzetfüzetek betöltésekor hibaüzenet jelenik meg.  

**Kockázatcsökkentő**

Ha ezt a hibaüzenetet kapja, nem jelenti azt, hogy az adatai sérültek vagy elvesznek.  A jegyzetfüzetek még mindig a lemezen vannak a-ben `/var/lib/jupyter` , és az SSH-t a fürtbe is elérheti. További információk: [Az SSH használata HDInsighttal](../hdinsight-hadoop-linux-use-ssh-unix.md).

Miután az SSH-val csatlakozott a fürthöz, a notebookot a fürtből a helyi gépre (SCP vagy megnyerő használatával) biztonsági másolatként másolhatja, hogy megakadályozza a jegyzetfüzetben található fontos adatok elvesztését. Ezután az 8001-as porton keresztül az SSH-alagúttal elérheti a átjárócsomóponthoz a Jupyter eléréséhez anélkül, hogy az átjárón kellene haladnia.  Innen törölheti a jegyzetfüzet kimenetét, és visszaállíthatja a notebook méretének minimalizálásához.

Ha meg szeretné akadályozni, hogy ez a hiba a jövőben is megtörténjen, kövesse az ajánlott eljárásokat:

* Fontos, hogy a jegyzetfüzet mérete kicsi legyen. A Spark-feladatok olyan kimenetét, amelyet visszaküld a Jupyter-be, megőrzi a jegyzetfüzetben.  Az ajánlott eljárás az általános Jupyter, hogy ne fusson `.collect()` a nagyméretű RDD vagy dataframes; Ehelyett ha egy RDD tartalmát szeretné megtekinteni, érdemes lehet a futtatást vagy a `.take()` `.sample()` kimenetet, hogy a kimenet ne legyen túl nagy.
* Emellett a jegyzetfüzetek mentésekor törölje az összes kimeneti cella méretét a méret csökkentése érdekében.

### <a name="notebook-initial-startup-takes-longer-than-expected"></a>A jegyzetfüzet kezdeti indítása a vártnál hosszabb időt vesz igénybe

A Spark Magic használatával Jupyter Notebook első kód utasítása több mint egy percet is igénybe vehet.  

**Magyarázat**

Ez azért történik, mert az első kód cellájának futtatásakor. A háttérben ez a munkamenet-konfiguráció, a Spark, az SQL és a kaptár környezetek beállítását indítja el. A kontextusok beállítása után az első utasítás fut, és ez azt a benyomást kelti, hogy az utasítás végrehajtása hosszú ideig tartott.

### <a name="jupyter-notebook-timeout-in-creating-the-session"></a>Jupyter Notebook időtúllépés a munkamenet létrehozásakor

Ha a Spark-fürt erőforrásai nem állnak rendelkezésre, akkor a Jupyter Notebook Spark és PySpark kernele időtúllépési kísérletet tesz a munkamenet létrehozásához.

**Enyhítését**

1. Szabadítson fel néhány erőforrást a Spark-fürtön a alábbiak szerint:

   * A többi Spark-jegyzetfüzet leállításához lépjen a Bezárás és leállítás menüre, vagy kattintson a Leállítás gombra a notebook Explorerben.
   * Más Spark-alkalmazások leállítása a FONALból.

2. Indítsa újra az elindítani próbált jegyzetfüzetet. Elegendő erőforrást kell elérhetőnek lennie ahhoz, hogy most létrehozza a munkamenetet.

## <a name="see-also"></a>Lásd még

* [Overview: Apache Spark on Azure HDInsight (Áttekintés: Apache Spark on Azure HDInsight)](apache-spark-overview.md)

### <a name="scenarios"></a>Forgatókönyvek

* [Apache Spark BI: interaktív adatelemzés végrehajtása a Spark on HDInsight és a BI Tools használatával](apache-spark-use-bi-tools.md)
* [Apache Spark a Machine Learning használatával: a Spark in HDInsight használata az építési hőmérséklet elemzésére a HVAC-adatok használatával](apache-spark-ipython-notebook-machine-learning.md)
* [Apache Spark a Machine Learning használatával: az élelmiszer-ellenőrzési eredmények előrejelzéséhez használja a Spark in HDInsight](apache-spark-machine-learning-mllib-ipython.md)
* [Webhely-naplózási elemzés Apache Spark használatával a HDInsight-ben](apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-run-applications"></a>Alkalmazások létrehozása és futtatása

* [Önálló alkalmazás létrehozása a Scala használatával](apache-spark-create-standalone-application.md)
* [Feladatok távoli futtatása egy Apache Spark-fürtön az Apache Livy használatával](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Eszközök és bővítmények

* [Az IntelliJ IDEA HDInsight-eszközei beépülő moduljának használata Spark Scala-alkalmazások létrehozásához és elküldéséhez](apache-spark-intellij-tool-plugin.md)
* [Az IntelliJ IDEA HDInsight-eszközei beépülő moduljának használata a Apache Spark alkalmazások távoli hibakereséséhez](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Apache Zeppelin notebookok használata Apache Spark-fürttel a HDInsight-on](apache-spark-zeppelin-notebook.md)
* [A HDInsight Apache Spark-fürtben Jupyter Notebook számára elérhető kernelek](apache-spark-jupyter-notebook-kernels.md)
* [Külső csomagok használata Jupyter notebookokkal](apache-spark-jupyter-notebook-use-external-packages.md)
* [A Jupyter telepítése a számítógépre, majd csatlakozás egy HDInsight Spark-fürthöz](apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Erőforrások kezelése

* [Apache Spark-fürt erőforrásainak kezelése az Azure HDInsightban](apache-spark-resource-manager.md)
* [Apache Spark-fürtön futó feladatok nyomon követése és hibakeresése a HDInsightban](apache-spark-job-debugging.md)
