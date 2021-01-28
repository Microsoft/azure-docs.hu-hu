---
title: 'Rövid útmutató: Spark-fürt létrehozása a HDInsight-ben a Azure Portal használatával'
description: Ez a rövid útmutató bemutatja, hogyan használható a Azure Portal egy Apache Spark-fürt létrehozásához az Azure HDInsight, és hogyan futtathat Spark SQL-lekérdezést.
ms.service: hdinsight
ms.topic: quickstart
ms.custom: mvc
ms.date: 02/25/2020
ms.openlocfilehash: 18225703b2a390f64b4bebd58b0167dd77c454d0
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/28/2021
ms.locfileid: "98930374"
---
# <a name="quickstart-create-apache-spark-cluster-in-azure-hdinsight-using-azure-portal"></a>Rövid útmutató: Apache Spark-fürt létrehozása az Azure HDInsight Azure Portal használatával

Ebben a rövid útmutatóban a Azure Portal használatával hoz létre egy Apache Spark-fürtöt az Azure HDInsight-ben. Ezután létrehozhat egy Jupyter Notebook, és használhatja a Spark SQL-lekérdezések futtatásához Apache Hive táblákon. Az Azure HDInsight egy felügyelt, teljes körű, nyílt forráskódú elemzési szolgáltatás vállalatok részére. A HDInsight Apache Spark keretrendszere lehetővé teszi a gyors adatelemzést és a fürtözést a memóriabeli feldolgozás használatával. Jupyter Notebook lehetővé teszi az adatkezelést, a kódok Markdown szöveggel való egyesítését és egyszerű vizualizációk használatát.

Az elérhető konfigurációk részletes ismertetését lásd: [fürtök beállítása a HDInsight-ben](../hdinsight-hadoop-provision-linux-clusters.md). A portál fürtön való létrehozásával kapcsolatos további információkért lásd: [fürtök létrehozása a portálon](../hdinsight-hadoop-create-linux-clusters-portal.md).

Ha egyszerre több fürtöt használ, létre kell hoznia egy virtuális hálózatot, és ha Spark-fürtöt használ, akkor a méhkas Warehouse-összekötőt is érdemes használnia. További információ: [virtuális hálózat megtervezése az Azure HDInsight](../hdinsight-plan-virtual-network-deployment.md) és [a Apache Spark és Apache Hive integrálása a kaptár Warehouse-összekötővel](../interactive-query/apache-hive-warehouse-connector.md).

> [!IMPORTANT]  
> A HDInsight-fürtök számlázása percenként történik, akár használja őket, akár nem. Mindig törölje a fürtöt, ha már nem használja. További információkért lásd a cikk [Az erőforrások eltávolítása](#clean-up-resources) című szakaszát.

## <a name="prerequisites"></a>Előfeltételek

Aktív előfizetéssel rendelkező Azure-fiók. [Hozzon létre egy fiókot ingyenesen](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

## <a name="create-an-apache-spark-cluster-in-hdinsight"></a>Apache Spark-fürt létrehozása a HDInsight-ben

A Azure Portal használatával olyan HDInsight-fürtöt hozhat létre, amely Azure Storage-blobokat használ fürtként. A 2. generációs Data Lake Storage használatáról [a fürtök HDInsightban történő beállításáról szóló rövid útmutatóban](../hdinsight-hadoop-provision-linux-clusters.md) talál további információt.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

1. A felső menüben válassza az **+ erőforrás létrehozása** lehetőséget.

    ![Erőforrás létrehozása Azure Portal](./media/apache-spark-jupyter-spark-sql-use-portal/azure-portal-create-resource.png "Erőforrás létrehozása az Azure Portalon")

1. Válassza az **Analytics**  >  **Azure HDInsight** lehetőséget a **HDInsight-fürt létrehozása** lap megjelenítéséhez.

1. Az **alapok** lapon adja meg a következő információkat:

    |Tulajdonság  |Leírás  |
    |---------|---------|
    |Előfizetés  | A legördülő listában válassza ki a fürthöz használt Azure-előfizetést. |
    |Erőforráscsoport | A legördülő listából válassza ki a meglévő erőforráscsoportot, vagy válassza az **új létrehozása** lehetőséget.|
    |Fürt neve | Adjon meg egy globálisan egyedi nevet.|
    |Régió   | A legördülő listából válassza ki azt a régiót, ahol a fürtöt létrehozták. |
    |Fürt típusa| A lista megnyitásához válassza a fürt típusának kiválasztása lehetőséget. A listából válassza a **Spark** elemet.|
    |Fürt verziója|Ez a mező automatikusan feltölti az alapértelmezett verziót a fürt típusának kiválasztása után.|
    |Fürt bejelentkezési felhasználóneve| Adja meg a fürt bejelentkezési felhasználónevét.  Az alapértelmezett név a **rendszergazda**. Ezzel a fiókkal jelentkezhet be a Jupyter Notebookba a rövid útmutatóban később. |
    |Fürt bejelentkezési jelszava| Adja meg a fürt bejelentkezési jelszavát. |
    |Secure Shell- (SSH-) felhasználónév| Adja meg az SSH-felhasználónevet. A rövid útmutatóhoz használt SSH-felhasználónév: **sshuser**. Alapértelmezés szerint ennek a fióknak ugyanaz a jelszava, mint a *fürt bejelentkezési felhasználónevéhez* tartozó fióknak. |

    ![A képernyőképen a H D Insight-fürt létrehozása látható az alapok lapon.](./media/apache-spark-jupyter-spark-sql-use-portal/azure-portal-cluster-basics-spark.png "Spark-fürt létrehozása a HDInsight-ben az alapvető konfigurációk")

    Kattintson a **Tovább gombra: storage >>** a **tárterület** oldalának folytatásához.

1. A **Storage** területen adja meg a következő értékeket:

    |Tulajdonság  |Leírás  |
    |---------|---------|
    |Elsődleges tároló típusa|Használja az alapértelmezett értéket az **Azure Storage**-ban.|
    |Kiválasztási módszer|Használja az alapértelmezett értéket a **listából**.|
    |Az elsődleges tárfiók|Használja az automatikusan feltöltött értéket.|
    |Tároló|Használja az automatikusan feltöltött értéket.|

    ![A képernyőképen a H D Insight-fürt létrehozása látható a Storage lapon.](./media/apache-spark-jupyter-spark-sql-use-portal/azure-portal-cluster-storage.png "Spark-fürt létrehozása a HDInsight-ben az alapvető konfigurációk")

    A folytatáshoz válassza a **felülvizsgálat + létrehozás** lehetőséget.

1. A **felülvizsgálat + létrehozás** területen válassza a **Létrehozás** lehetőséget. A fürt létrehozása nagyjából 20 percet vesz igénybe. A következő munkamenetre csak a fürt létrehozását követően lehet továbblépni.

Ha a HDInsight-fürtök létrehozásával kapcsolatos problémába ütközne, előfordulhat, hogy nem rendelkezik a megfelelő engedélyekkel. További információért tekintse meg [a hozzáférés-vezérlésre vonatkozó követelményeket](../hdinsight-hadoop-customize-cluster-linux.md#access-control).

## <a name="create-a-jupyter-notebook"></a>Jupyter-notebook létrehozása

A Jupyter Notebook egy interaktív notebook-környezet, amely számos programozási nyelvet támogat. A notebook lehetővé teszi az adatai használatát, a kódok és markdown-szövegek egyesítését, valamint egyszerű vizualizációk elvégzését.

1. Egy webböngészőből nyissa meg a következőt: `https://CLUSTERNAME.azurehdinsight.net/jupyter` , ahol a a `CLUSTERNAME` fürt neve. Ha a rendszer kéri, adja meg a fürthöz tartozó bejelentkezési hitelesítő adatokat.

1.   >  Jegyzetfüzet létrehozásához válassza az új **PySpark** lehetőséget.

   ![Jupyter Notebook létrehozása az interaktív Spark SQL-lekérdezés futtatásához](./media/apache-spark-jupyter-spark-sql-use-portal/hdinsight-spark-create-jupyter-interactive-spark-sql-query.png "Jupyter Notebook létrehozása az interaktív Spark SQL-lekérdezés futtatásához")

   Az új notebook létrejött, és Untitled(Untitled.pynb) néven nyílt meg.

## <a name="run-apache-spark-sql-statements"></a>Apache Spark SQL-utasítások futtatása

Az SQL az adatok lekérdezéséhez és meghatározásához leggyakrabban és legszélesebb körben használt nyelv. A Spark SQL az Apache Spark bővítményeként működik a strukturált adatok ismerős SQL-szintaxissal való feldolgozásához.

1. Ellenőrizze, hogy a kernel készen áll-e. A kernel akkor áll készen, ha a neve mellett a notebookban egy üres kör látható. A teli kör azt jelenti, hogy a kernel foglalt.

    ![A képernyőképen egy Jupyter ablak látható, amely egy PySpark-kijelzővel rendelkezik.](./media/apache-spark-jupyter-spark-sql/jupyter-spark-kernel-status.png "Struktúra-lekérdezés a HDInsight")

    A notebook első indításakor a kernel a háttérben elvégez néhány feladatot. Várja meg, hogy a kernel elkészüljön.

1. Illessze be a következő kódot egy üres cellába, majd nyomja le a **SHIFT + ENTER** billentyűkombinációt annak futtatásához. A parancs felsorolja a fürtön található Hive-táblákat:

    ```PySpark
    %%sql
    SHOW TABLES
    ```

    Ha a HDInsight-fürttel Jupyter Notebook használ, olyan beállításkészletet kap, `sqlContext` amellyel a Spark SQL használatával futtathat kaptár-lekérdezéseket. A `%%sql` megadja a Jupyter notebook számára, hogy az előre beállított `sqlContext` elemet használja a Hive-lekérdezés futtatásához. A lekérdezés lekérdezi az első 10 sort egy Hive-táblából (**hivesampletable**), amely alapértelmezés szerint minden HDInsight-fürtben megtalálható. Az eredmények lekérdezése körülbelül 30 másodpercet vesz igénybe. A kimenet a következőképpen fog kinézni:

    ![Képernyőfelvétel: az ebben a rövid útmutatóban létrehozott jegyzetfüzet Jupyter ablaka.](./media/apache-spark-jupyter-spark-sql-use-portal/hdinsight-spark-get-started-hive-query.png "Struktúra-lekérdezés a HDInsight")

    Minden alkalommal, amikor a Jupyterben lekérdezést futtat, a webböngésző ablakának címsorában **(Foglalt)** állapot jelenik meg a notebook neve mellett. A jobb felső sarokban lévő **PySpark** felirat mellett ekkor egy teli kör is megjelenik.

1. Futtasson egy másik lekérdezést a `hivesampletable` adatainak megtekintéséhez.

    ```PySpark
    %%sql
    SELECT * FROM hivesampletable LIMIT 10
    ```

    A képernyő frissül, és megjeleníti a lekérdezés kimenetét.

    ![Struktúra-lekérdezés kimenete a HDInsight-ben](./media/apache-spark-jupyter-spark-sql-use-portal/hdinsight-spark-get-started-hive-query-output.png "Struktúra-lekérdezés kimenete a HDInsight-ben")

1. A notebook **File** (Fájl) menüjében kattintson a **Close and Halt** (Bezárás és leállítás) elemre. A notebook leállítása felszabadítja a fürt erőforrásait.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

A HDInsight menti az adatait az Azure Storage-ban vagy Azure Data Lake Storageban, így biztonságosan törölheti a fürtöt, ha az nincs használatban. A HDInsight-fürtökért is fizetnie kell, még akkor is, ha nincs használatban. Mivel a fürt díjai több időt vesznek igénybe, mint a tárterületre vonatkozó díjak, a gazdasági érzékek törlik a fürtöket, ha nincsenek használatban. Ha azt tervezi, hogy rögtön elvégzi a [További lépések](#next-steps) szakaszban található oktatóanyagot is, akkor érdemes lehet megtartani a fürtöt.

Lépjen vissza az Azure Portalra és válassza a **Törlés** lehetőséget.

![HDInsight-fürt törlése Azure Portal](./media/apache-spark-jupyter-spark-sql-use-portal/hdinsight-azure-portal-delete-cluster.png "HDInsight-fürt törlése")

Az erőforráscsoport nevét kiválasztva is megnyílik az erőforráscsoport oldala, ahol kiválaszthatja az **Erőforráscsoport törlése** elemet. Az erőforráscsoport törlésével törli a HDInsight-fürtöt és az alapértelmezett Storage-fiókot is.

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban megtanulta, hogyan hozhat létre Apache Spark-fürtöt a HDInsight-ben, és hogyan futtathat egy alapszintű Spark SQL-lekérdezést. Folytassa a következő oktatóanyaggal, amelyből megtudhatja, hogyan futtathat HDInsight-fürtöket interaktív lekérdezések futtatásához a mintaadatok használatával.

> [!div class="nextstepaction"]
> [Interaktív lekérdezések futtatása Apache Sparkon](./apache-spark-load-data-run-query.md)