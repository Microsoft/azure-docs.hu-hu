---
title: 'Rövid útmutató: Apache Spark-fürt létrehozása sablon használatával – Azure HDInsight'
description: Ez a rövid útmutató bemutatja, hogyan használható a Resource Manager-sablon Apache Spark-fürt létrehozásához az Azure HDInsight, és hogyan futtathat Spark SQL-lekérdezést.
ms.service: hdinsight
ms.topic: quickstart
ms.custom: subject-armqs
ms.date: 03/13/2020
ms.openlocfilehash: 1bbf3cbffe2ab51f10e86dc3b9fe4ea6040a7911
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/28/2021
ms.locfileid: "98930280"
---
# <a name="quickstart-create-apache-spark-cluster-in-azure-hdinsight-using-arm-template"></a>Rövid útmutató: Apache Spark-fürt létrehozása az Azure HDInsight ARM-sablon használatával

Ebben a rövid útmutatóban egy Azure Resource Manager sablon (ARM-sablon) használatával hoz létre egy [Apache Spark](./apache-spark-overview.md) -fürtöt az Azure HDInsight-ben. Ezután létrehozhat egy Jupyter Notebook fájlt, és használhatja a Spark SQL-lekérdezések futtatásához Apache Hive táblákon. Az Azure HDInsight egy felügyelt, teljes körű, nyílt forráskódú elemzési szolgáltatás vállalatok részére. A HDInsight Apache Spark keretrendszere lehetővé teszi a gyors adatelemzést és a fürtözést a memóriabeli feldolgozás használatával. Jupyter Notebook lehetővé teszi az adatkezelést, a kódok Markdown szöveggel való egyesítését és egyszerű vizualizációk használatát.

Ha egyszerre több fürtöt használ, létre kell hoznia egy virtuális hálózatot, és ha Spark-fürtöt használ, akkor a méhkas Warehouse-összekötőt is érdemes használnia. További információ: [virtuális hálózat megtervezése az Azure HDInsight](../hdinsight-plan-virtual-network-deployment.md) és [a Apache Spark és Apache Hive integrálása a kaptár Warehouse-összekötővel](../interactive-query/apache-hive-warehouse-connector.md).

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]

Ha a környezet megfelel az előfeltételeknek, és már ismeri az ARM-sablonokat, kattintson az **Üzembe helyezés az Azure-ban** gombra. A sablon az Azure Portalon fog megnyílni.

[![Üzembe helyezés az Azure-ban](../../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-spark-linux%2Fazuredeploy.json)

## <a name="prerequisites"></a>Előfeltételek

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a name="review-the-template"></a>A sablon áttekintése

Az ebben a gyorsútmutatóban használt sablon az [Azure-gyorssablonok](https://azure.microsoft.com/resources/templates/101-hdinsight-spark-linux) közül származik.

:::code language="json" source="~/quickstart-templates/101-hdinsight-spark-linux/azuredeploy.json":::

Két Azure-erőforrás van definiálva a sablonban:

* [Microsoft. Storage/storageAccounts](/azure/templates/microsoft.storage/storageaccounts): hozzon létre egy Azure Storage-fiókot.
* [Microsoft. HDInsight/cluster](/azure/templates/microsoft.hdinsight/clusters): hozzon létre egy HDInsight-fürtöt.

## <a name="deploy-the-template"></a>A sablon üzembe helyezése

1. Az Azure-ba való bejelentkezéshez és az ARM-sablon megnyitásához válassza az alábbi **üzembe helyezés az Azure** -ban gombot.

    [![Üzembe helyezés az Azure-ban](../../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-spark-linux%2Fazuredeploy.json)

1. Adja meg vagy válassza ki a következő értékeket:

    |Tulajdonság |Leírás |
    |---|---|
    |Előfizetés|A legördülő listában válassza ki a fürthöz használt Azure-előfizetést.|
    |Erőforráscsoport|A legördülő listából válassza ki a meglévő erőforráscsoportot, vagy válassza az **új létrehozása** lehetőséget.|
    |Hely|Az érték automatikusan kitöltődik az erőforráscsoporthoz használt hellyel.|
    |Fürt neve|Adjon meg egy globálisan egyedi nevet. Ehhez a sablonhoz csak kisbetűket és számokat használjon.|
    |Fürt bejelentkezési felhasználóneve|Adja meg a felhasználónevet, az alapértelmezett érték a **rendszergazda**.|
    |Fürt bejelentkezési jelszava|Adja meg a jelszót. A jelszónak legalább 10 karakterből kell állnia, és tartalmaznia kell legalább egy számot, egy nagybetűs és egy kisbetűs betűt, egy nem alfanumerikus karaktert (kivéve a következő karaktereket: "" "). |
    |SSH-Felhasználónév|Adja meg a felhasználónevet, az alapértelmezett érték a **sshuser** .|
    |SSH-jelszó|Adja meg a jelszót.|

    ![Spark-fürt létrehozása a HDInsight-ben Azure Resource Manager sablon használatával](./media/apache-spark-jupyter-spark-sql/resource-manager-template-spark.png "Spark-fürt létrehozása a HDInsight-ben Azure Resource Manager sablon használatával")

1. Tekintse át a **használati** feltételeket. Ezután válassza **az Elfogadom a fenti feltételeket és kikötéseket**, majd a **vásárlás** lehetőséget. Értesítést kap arról, hogy a telepítés folyamatban van. Egy fürt létrehozása nagyjából 20 percet vesz igénybe.

Ha a HDInsight-fürtök létrehozásával kapcsolatos problémába ütközne, előfordulhat, hogy nem rendelkezik a megfelelő engedélyekkel. További információért tekintse meg [a hozzáférés-vezérlésre vonatkozó követelményeket](../hdinsight-hadoop-customize-cluster-linux.md#access-control).

## <a name="review-deployed-resources"></a>Üzembe helyezett erőforrások áttekintése

A fürt létrehozása után az **üzembe helyezés sikeres** értesítést fog kapni, amely a **Go to Resource** hivatkozást fogja tartalmazni. Az erőforráscsoport lap felsorolja az új HDInsight-fürtöt és a fürthöz társított alapértelmezett tárolót. Minden fürt rendelkezik [Azure Storage](../hdinsight-hadoop-use-blob-storage.md)-, [Azure Data Lake Storage Gen1](../hdinsight-hadoop-use-data-lake-storage-gen1.md)-vagy [`Azure Data Lake Storage Gen2`](../hdinsight-hadoop-use-data-lake-storage-gen2.md) függőséggel. Ezt az alapértelmezett Storage-fióknak nevezzük. A HDInsight-fürtnek és az alapértelmezett Storage-fióknak ugyanabban az Azure-régióban kell elhelyezkednie. A fürtök törlése nem törli a tárolási fiók függőségét. Ezt az alapértelmezett Storage-fióknak nevezzük. A HDInsight-fürtnek és az alapértelmezett Storage-fióknak ugyanabban az Azure-régióban kell elhelyezkednie. A fürtök törlésével nem törlődik a Storage-fiók.

## <a name="create-a-jupyter-notebook-file"></a>Jupyter Notebook-fájl létrehozása

A [Jupyter notebook](https://jupyter.org/) egy interaktív notebook-környezet, amely különböző programozási nyelveket támogat. Használhat egy Jupyter Notebook fájlt az adatokkal való kommunikációhoz, a kód Markdown-szöveggel való összekapcsolásához és egyszerű vizualizációk elvégzéséhez.

1. Nyissa meg az [Azure Portalt](https://portal.azure.com).

2. Válassza a **HDInsight-fürtök** lehetőséget, majd a létrehozott fürtöt.

    ![HDInsight-fürt megnyitása a Azure Portal](./media/apache-spark-jupyter-spark-sql/azure-portal-open-hdinsight-cluster.png)

3. A portálon, a **fürt irányítópultok** szakaszban válassza a **Jupyter notebook** lehetőséget. Ha a rendszer kéri, adja meg a fürthöz tartozó bejelentkezési hitelesítő adatokat.

   ![Az interaktív Spark SQL-lekérdezés futtatásához Jupyter Notebook megnyitása](./media/apache-spark-jupyter-spark-sql/hdinsight-spark-open-jupyter-interactive-spark-sql-query.png "Az interaktív Spark SQL-lekérdezés futtatásához Jupyter Notebook megnyitása")

4.   >  Jegyzetfüzet létrehozásához válassza az új **PySpark** lehetőséget.

   ![Jupyter Notebook-fájl létrehozása az interaktív Spark SQL-lekérdezés futtatásához](./media/apache-spark-jupyter-spark-sql/hdinsight-spark-create-jupyter-interactive-spark-sql-query.png "Jupyter Notebook létrehozása az interaktív Spark SQL-lekérdezés futtatásához")

   Az új notebook létrejött, és Untitled(Untitled.pynb) néven nyílt meg.

## <a name="run-apache-spark-sql-statements"></a>Apache Spark SQL-utasítások futtatása

Az SQL az adatok lekérdezésére és átalakítására leggyakrabban és legszélesebb körben használt nyelv. A Spark SQL az Apache Spark bővítményeként működik a strukturált adatok ismerős SQL-szintaxissal való feldolgozásához.

1. Ellenőrizze, hogy a kernel készen áll-e. A kernel akkor áll készen, ha a neve mellett a notebookban egy üres kör látható. A teli kör azt jelenti, hogy a kernel foglalt.

    ![Kernel állapota](./media/apache-spark-jupyter-spark-sql/jupyter-spark-kernel-status.png "Kernel állapota")

    A notebook első indításakor a kernel a háttérben elvégez néhány feladatot. Várja meg, hogy a kernel elkészüljön.

1. Illessze be a következő kódot egy üres cellába, majd nyomja le a **SHIFT + ENTER** billentyűkombinációt annak futtatásához. A parancs felsorolja a fürtön található Hive-táblákat:

    ```sql
    %%sql
    SHOW TABLES
    ```

    Ha Jupyter Notebook fájlt használ a HDInsight-fürttel, egy előre beállított `spark` munkamenetet kap, amellyel a Spark SQL használatával futtathat kaptár-lekérdezéseket. A `%%sql` megadja a Jupyter-notebook számára, hogy az előre beállított `spark`-munkamenetet használja a Hive-lekérdezés futtatásához. A lekérdezés lekérdezi az első 10 sort egy Hive-táblából (**hivesampletable**), amely alapértelmezés szerint minden HDInsight-fürtben megtalálható. Amikor először küldi el a lekérdezést, a Jupyter létrehoz egy Spark-alkalmazást a jegyzetfüzethez. Ez körülbelül 30 másodpercet vesz igénybe. Miután a Spark-alkalmazás elkészült, a lekérdezés végrehajtása körülbelül egy másodperc alatt történik, és létrehozza az eredményeket. A kimenet a következőképpen fog kinézni:

    ![Apache Hive lekérdezés a HDInsight](./media/apache-spark-jupyter-spark-sql/hdinsight-spark-get-started-hive-query.png "Struktúra-lekérdezés a HDInsight")

    Minden alkalommal, amikor a Jupyterben lekérdezést futtat, a webböngésző ablakának címsorában **(Foglalt)** állapot jelenik meg a notebook neve mellett. A jobb felső sarokban lévő **PySpark** felirat mellett ekkor egy teli kör is megjelenik.

1. Futtasson egy másik lekérdezést a `hivesampletable` adatainak megtekintéséhez.

    ```sql
    %%sql
    SELECT * FROM hivesampletable LIMIT 10
    ```

    A képernyő frissül, és megjeleníti a lekérdezés kimenetét.

    ![Struktúra-lekérdezés kimenete a HDInsight-ben](./media/apache-spark-jupyter-spark-sql/hdinsight-spark-get-started-hive-query-output.png "Struktúra-lekérdezés kimenete a HDInsight-ben")

1. A notebook **File** (Fájl) menüjében kattintson a **Close and Halt** (Bezárás és leállítás) elemre. A jegyzetfüzet leállítása a fürt erőforrásainak kiadását, beleértve a Spark-alkalmazást is.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

A gyors üzembe helyezés befejezése után érdemes lehet törölni a fürtöt. A HDInsight az adatait az Azure Storage tárolja, így biztonságosan törölheti a fürtöt, ha az nincs használatban. A HDInsight-fürtökért is fizetnie kell, még akkor is, ha nincs használatban. Mivel a fürt díjai több időt vesznek igénybe, mint a tárterületre vonatkozó díjak, a gazdasági érzékek törlik a fürtöket, ha nincsenek használatban.

A Azure Portal navigáljon a fürthöz, és válassza a **Törlés** lehetőséget.

![HDInsight-fürt törlése Azure Portal](./media/apache-spark-jupyter-spark-sql/hdinsight-azure-portal-delete-cluster.png "HDInsight-fürt törlése")

Az erőforráscsoport nevét kiválasztva is megnyílik az erőforráscsoport oldala, ahol kiválaszthatja az **Erőforráscsoport törlése** elemet. Az erőforráscsoport törlésével törli a HDInsight-fürtöt és az alapértelmezett Storage-fiókot is.

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban megtanulta, hogyan hozhat létre Apache Spark-fürtöt a HDInsight-ben, és hogyan futtathat egy alapszintű Spark SQL-lekérdezést. Folytassa a következő oktatóanyaggal, amelyből megtudhatja, hogyan futtathat HDInsight-fürtöket interaktív lekérdezések futtatásához a mintaadatok használatával.

> [!div class="nextstepaction"]
> [Interaktív lekérdezések futtatása Apache Sparkon](./apache-spark-load-data-run-query.md)
