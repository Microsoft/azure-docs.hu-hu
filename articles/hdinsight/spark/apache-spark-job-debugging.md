---
title: Azure HDInsighton futó Apache Spark-feladatok hibakeresése
description: Az Azure HDInsight-beli Spark-fürtön futó feladatok nyomon követése és hibakeresése a fonal felhasználói felület, a Spark felhasználói felület és a Spark-előzmények kiszolgáló használatával
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive,seoapr2020
ms.date: 04/23/2020
ms.openlocfilehash: 0dd250f0a8f67d7e370b8ff453e9cff4d88b7896
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "104866097"
---
# <a name="debug-apache-spark-jobs-running-on-azure-hdinsight"></a>Azure HDInsighton futó Apache Spark-feladatok hibakeresése

Ebből a cikkből megtudhatja, hogyan követheti nyomon és hibakereséssel Apache Spark HDInsight-fürtökön futó feladatokat. Hibakeresés a Apache Hadoop a fonal felhasználói felületén, a Spark felhasználói felületen és a Spark-előzmények kiszolgálóján keresztül. Egy Spark-feladatot a Spark-fürttel elérhető jegyzetfüzettel indíthat el, **gépi tanulás: az élelmiszer-ellenőrzési adatok prediktív elemzése a MLLib használatával**. A következő lépésekkel követheti nyomon a más megközelítéssel elküldött alkalmazásokat, például a **Spark-submitt**.

Ha még nincs Azure-előfizetése, kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Előfeltételek

* Apache Spark-fürt megléte a HDInsightban. További útmutatásért lásd: [Apache Spark-fürt létrehozása az Azure HDInsightban](apache-spark-jupyter-spark-sql.md).

* Meg kell kezdenie a jegyzetfüzet futtatását, **[gépi tanulás: prediktív elemzés az élelmiszer-ellenőrzési adataihoz a MLLib használatával](apache-spark-machine-learning-mllib-ipython.md)**. A jegyzetfüzet futtatásával kapcsolatos utasításokért kövesse a hivatkozást.  

## <a name="track-an-application-in-the-yarn-ui"></a>Alkalmazás nyomon követése a fonal felhasználói felületén

1. Indítsa el a fonal felhasználói felületét. Válasszon **fonalat** a **fürt irányítópultok** területen.

    :::image type="content" source="./media/apache-spark-job-debugging/launch-apache-yarn-ui.png" alt-text="Azure Portal a fonal felhasználói felületének indítása" border="true":::

   > [!TIP]  
   > Azt is megteheti, hogy a Ambari felhasználói felületéről is elindítja a fonal felhasználói felületét. A Ambari felhasználói felületének elindításához válassza a **Ambari Kezdőlap** lehetőséget a **fürt irányítópultok** területen. A Ambari felhasználói felületén navigáljon a **fonalak**  >  **gyors hivatkozásai** > az Active Resource Manager > **Resource Manager felhasználói felületén**.

2. Mivel a Spark-feladatot a Jupyter notebookok használatával indította el, az alkalmazás neve **remotesparkmagics** (a jegyzetfüzetek által elindított összes alkalmazás neve). A feladattal kapcsolatos további információk megtekintéséhez válassza ki az alkalmazás AZONOSÍTÓját az alkalmazás nevében. Ez a művelet elindítja az alkalmazás nézetét.

    :::image type="content" source="./media/apache-spark-job-debugging/find-application-id1.png" alt-text="Spark-előzmények kiszolgálója Spark-alkalmazás AZONOSÍTÓjának keresése" border="true":::

    A Jupyter-jegyzetfüzetekről indított alkalmazások esetében az állapot mindig **fut** , amíg ki nem lép a jegyzetfüzetből.

3. Az alkalmazás nézetből részletesebben is megtudhatja, hogy az alkalmazáshoz és a naplókhoz társított tárolók (StdOut/stderr) találhatók-e. A Spark felhasználói felületet úgy is elindíthatja, hogy a **nyomkövetési URL-címhez** tartozó hivatkozásra kattint az alább látható módon.

    :::image type="content" source="./media/apache-spark-job-debugging/download-container-logs.png" alt-text="A Spark History Server letöltési tárolójának naplói" border="true":::

## <a name="track-an-application-in-the-spark-ui"></a>Alkalmazás követése a Spark felhasználói felületén

A Spark felhasználói felületén megtekintheti azokat a Spark-feladatokat, amelyeket a korábban elindított alkalmazás szül.

1. A Spark felhasználói felület elindításához az alkalmazás nézetből válassza ki a **nyomkövetési URL-címre** mutató hivatkozást, ahogy az a fenti képernyőfelvételen látható. Megtekintheti a Jupyter Notebook futó alkalmazás által indított összes Spark-feladatot.

    :::image type="content" source="./media/apache-spark-job-debugging/view-apache-spark-jobs.png" alt-text="Spark History Server-feladatok lap" border="true":::

2. Válassza a **végrehajtók** fület az egyes végrehajtók feldolgozási és tárolási adatainak megtekintéséhez. A hívási verem lekéréséhez válassza a **szál memóriaképe** hivatkozást.

    :::image type="content" source="./media/apache-spark-job-debugging/view-spark-executors.png" alt-text="Spark History Server-végrehajtók lap" border="true":::

3. A **szakaszok** lapon megtekintheti az alkalmazáshoz társított szakaszokat.

    :::image type="content" source="./media/apache-spark-job-debugging/view-apache-spark-stages.png " alt-text="A Spark History Server szakaszai lap" border="true":::

    Az egyes szakaszok több feladattal is rendelkezhetnek, amelyekhez a végrehajtás statisztikáit tekintheti meg, például alább látható.

    :::image type="content" source="./media/apache-spark-job-debugging/view-spark-stages-details.png " alt-text="A Spark History Server szakaszai lap részletei" border="true":::

4. A fázis részletei lapon a DAG vizualizációt indíthatja el. Bontsa ki a **Dag vizualizáció** hivatkozását az oldal tetején, az alábbi ábrán látható módon.

    :::image type="content" source="./media/apache-spark-job-debugging/view-spark-stages-dag-visualization.png" alt-text="Spark-szakaszok megtekintése – DAG vizualizáció" border="true":::

    A DAG vagy a Direct Aclyic gráf az alkalmazás különböző szakaszait jelöli. A gráf minden kék mezője az alkalmazásból meghívott Spark-műveletet jelöli.

5. A szakasz részletei lapon elindíthatja az alkalmazás ütemtervének nézetét is. Bontsa ki az **esemény idővonalának** hivatkozását az oldal tetején, az alábbi ábrán látható módon.

    :::image type="content" source="./media/apache-spark-job-debugging/view-spark-stages-event-timeline.png" alt-text="Spark-szakaszok esemény idővonalának megtekintése" border="true":::

    Ez a rendszerkép a Spark-eseményeket idősor formájában jeleníti meg. Az Idősor nézet három szinten, a feladatok között, a feladatokon belül és egy fázison belül érhető el. A fenti kép rögzíti egy adott fázis idővonal-nézetét.

   > [!TIP]  
   > Ha bejelöli a **Nagyítás engedélyezése** jelölőnégyzetet, a bal oldali és a jobb oldali görgetést is görgetheti az idősor nézetben.

6. A Spark felhasználói felületének többi lapja is hasznos információkat nyújt a Spark-példányról.

   * Storage lap – ha az alkalmazás létrehoz egy RDD, a Storage (tárolás) lapon talál információt.
   * Környezet lap – ezen a lapon hasznos információkat olvashat a Spark-példányról, például:
     * Scala verziója
     * A fürthöz társított Eseménynapló-könyvtár
     * Az alkalmazás végrehajtó magjainak száma

## <a name="find-information-about-completed-jobs-using-the-spark-history-server"></a>A befejezett feladatokkal kapcsolatos információk megkeresése a Spark History Server használatával

A feladatok elvégzése után a rendszer megőrzi a feladattal kapcsolatos információkat a Spark History-kiszolgálón.

1. A Spark-előzmények kiszolgálójának elindításához az **Áttekintés** lapon válassza a **Spark History Server** elemet a **fürt irányítópultok** területen.

    :::image type="content" source="./media/apache-spark-job-debugging/launch-spark-history-server.png " alt-text="Azure Portal a Spark-előzmények kiszolgálójának elindítása" border="true":::

   > [!TIP]  
   > Azt is megteheti, hogy a Spark History Server felhasználói felületét is elindítja a Ambari felhasználói felületén. A Ambari felhasználói felületének elindításához az Áttekintés panelen válassza a **Ambari Kezdőlap** elemet a **fürt irányítópultok** területen. A Ambari felhasználói felületén navigáljon a **Spark2**  >  **Quick Links**  >  **Spark2 History Server felhasználói felületéhez**.

2. Megjelenik a felsorolt összes befejezett alkalmazás. Válassza ki az alkalmazás AZONOSÍTÓját, hogy további információkat adjon meg egy alkalmazásban.

    :::image type="content" source="./media/apache-spark-job-debugging/view-completed-applications.png " alt-text="A Spark History Server által befejezett alkalmazások" border="true":::

## <a name="see-also"></a>Lásd még

* [Apache Spark-fürt erőforrásainak kezelése az Azure HDInsightban](apache-spark-resource-manager.md)
* [Apache Spark feladatok hibakeresése kiterjesztett Spark History Server használatával](apache-azure-spark-history-server.md)
* [Alkalmazások hibakeresése a Azure Toolkit for IntelliJ SSH-n keresztül Apache Spark](apache-spark-intellij-tool-debug-remotely-through-ssh.md)
