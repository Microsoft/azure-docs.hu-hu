---
title: 'Rövid útmutató: Apache Spark létrehozása sablon használatával – Azure HDInsight'
description: Ez a rövid útmutató bemutatja, hogyan használhatja Resource Manager sablont egy Apache Spark-fürt létrehozásához Azure HDInsight Spark SQL-lekérdezés futtatásához.
ms.date: 03/13/2020
ms.topic: quickstart
ms.service: hdinsight
ms.custom:
- subject-armqs
- mode-arm
ms.openlocfilehash: 6d3f8c3313d9e6be38acdbecb683899ea8dd5376
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2021
ms.locfileid: "107538016"
---
# <a name="quickstart-create-apache-spark-cluster-in-azure-hdinsight-using-arm-template"></a>Rövid útmutató: Apache Spark létrehozása Azure HDInsight ARM-sablon használatával

Ebben a rövid útmutatóban egy Azure Resource Manager sablont (ARM-sablont) fog használni egy [Apache Spark fürt](./apache-spark-overview.md) létrehozásához a Azure HDInsight. Ezután létrehoz egy Jupyter Notebook, majd spark SQL-lekérdezéseket futtat a Apache Hive alapján. Az Azure HDInsight egy felügyelt, teljes körű, nyílt forráskódú elemzési szolgáltatás vállalatok részére. A Apache Spark HDInsight-keretrendszer memóriaalapú feldolgozással teszi lehetővé a gyors adatelemzést és fürt-számítástechnikát. Jupyter Notebook lehetővé teszi az adatokkal való interakciót, kód és Markdown-szöveg kombinálása, valamint egyszerű vizualizációk létrehozása.

Ha több fürtöt használ együtt, hozzon létre egy virtuális hálózatot, és ha Spark-fürtöt használ, akkor a fürtöt is Hive Warehouse Connector. További információ: [Plan a virtual network for Azure HDInsight](../hdinsight-plan-virtual-network-deployment.md) and Integrate Apache Spark and Apache Hive with the [Hive Warehouse Connector.](../interactive-query/apache-hive-warehouse-connector.md)

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]

Ha a környezet megfelel az előfeltételeknek, és már ismeri az ARM-sablonokat, kattintson az **Üzembe helyezés az Azure-ban** gombra. A sablon az Azure Portalon fog megnyílni.

[:::image type="icon" source="../../media/template-deployments/deploy-to-azure.svg" alt-text="Üzembe helyezés az Azure-ban":::](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-spark-linux%2Fazuredeploy.json)

## <a name="prerequisites"></a>Előfeltételek

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a name="review-the-template"></a>A sablon áttekintése

Az ebben a gyorsútmutatóban használt sablon az [Azure-gyorssablonok](https://azure.microsoft.com/resources/templates/101-hdinsight-spark-linux) közül származik.

:::code language="json" source="~/quickstart-templates/101-hdinsight-spark-linux/azuredeploy.json":::

A sablonban két Azure-erőforrás van definiálva:

* [Microsoft.Storage/storageAccounts:](/azure/templates/microsoft.storage/storageaccounts)Hozzon létre egy Azure Storage-fiókot.
* [Microsoft.HDInsight/cluster:](/azure/templates/microsoft.hdinsight/clusters)HDInsight-fürt létrehozása.

## <a name="deploy-the-template"></a>A sablon üzembe helyezése

1. Az alábbi **Üzembe helyezés az Azure-ban** gombra kattintva jelentkezzen be az Azure-ba, és nyissa meg az ARM-sablont.

    [:::image type="icon" source="../../media/template-deployments/deploy-to-azure.svg" alt-text="Üzembe helyezés az Azure-ban":::](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-spark-linux%2Fazuredeploy.json)

1. Adja meg vagy válassza ki a következő értékeket:

    |Tulajdonság |Leírás |
    |---|---|
    |Előfizetés|A legördülő listában válassza ki a fürthöz használt Azure-előfizetést.|
    |Erőforráscsoport|A legördülő listában válassza ki a meglévő erőforráscsoportot, vagy válassza az **Új létrehozása lehetőséget.**|
    |Hely|Az érték automatikusan kitölti az erőforráscsoporthoz használt helyet.|
    |Fürt neve|Adjon meg egy globálisan egyedi nevet. Ehhez a sablonhoz csak kisbetűket és számokat használjon.|
    |Fürt bejelentkezési felhasználóneve|Adja meg a felhasználónevet, az alapértelmezett érték **az admin.**|
    |Fürt bejelentkezési jelszava|Adjon meg egy jelszót. A jelszónak legalább 10 karakter hosszúságúnak kell lennie, és tartalmaznia kell legalább egy számjegyet, egy nagybetűt és egy kisbetűt, valamint egy nem alfanumerikus karaktert (kivéve a " " ' karaktert). |
    |SSH-felhasználónév|Adja meg a felhasználónevet, az alapértelmezett érték **az sshuser**|
    |SSH-jelszó|Adja meg a jelszót.|

    :::image type="content" source="./media/apache-spark-jupyter-spark-sql/resource-manager-template-spark.png " alt-text="Spark-fürt létrehozása a HDInsightban Azure Resource Manager sablon használatával" border="true":::

1. Tekintse át **a HASZNÁLATI FELTÉTELEKET.** Ezután válassza az Elfogadom a fenti **feltételeket** és feltételeket lehetőséget, majd a **Vásárlás lehetőséget.** Értesítést kap arról, hogy az üzembe helyezés folyamatban van. Egy fürt létrehozása nagyjából 20 percet vesz igénybe.

Ha problémába fullad a HDInsight-fürtök létrehozása, akkor lehet, hogy nem rendelkezik a megfelelő engedélyekkel. További információért tekintse meg [a hozzáférés-vezérlésre vonatkozó követelményeket](../hdinsight-hadoop-customize-cluster-linux.md#access-control).

## <a name="review-deployed-resources"></a>Üzembe helyezett erőforrások áttekintése

A fürt létrehozása után az Üzembe  helyezés sikeres értesítés jelenik meg az **Erőforráshoz ugrás hivatkozással.** Az Erőforráscsoport lapon megjelenik az új HDInsight-fürt és a fürthöz társított alapértelmezett tároló. Minden fürt rendelkezik [Egy Azure Storage-](../hdinsight-hadoop-use-blob-storage.md)vagy [Azure Data Lake Storage Gen1-](../hdinsight-hadoop-use-data-lake-storage-gen1.md)vagy [`Azure Data Lake Storage Gen2`](../hdinsight-hadoop-use-data-lake-storage-gen2.md) egy függőséggel. Ezt alapértelmezett tárfióknak nevezzük. A HDInsight-fürtnek és az alapértelmezett tárfióknak ugyanabban az Azure-régióban kell lennie. A fürtök törlésével nem törli a tárfiók függőségét. Ezt alapértelmezett tárfióknak nevezzük. A HDInsight-fürtnek és az alapértelmezett tárfióknak ugyanabban az Azure-régióban kell lennie. A fürtök törlésével nem törli a tárfiókot.

## <a name="create-a-jupyter-notebook-file"></a>Jupyter Notebook-fájl létrehozása

[Jupyter Notebook](https://jupyter.org/) egy interaktív jegyzetfüzet-környezet, amely különböző programozási nyelveket támogat. A kódfájlokkal Jupyter Notebook adatokat, kódokat kombinálhat Markdown-szövegekkel, és egyszerű vizualizációkat hajthat végre.

1. Nyissa meg az [Azure Portalt](https://portal.azure.com).

2. Válassza a **HDInsight-fürtök** lehetőséget, majd a létrehozott fürtöt.

    :::image type="content" source="./media/apache-spark-jupyter-spark-sql/azure-portal-open-hdinsight-cluster.png" alt-text="Nyissa meg a HDInsight-fürtöt a Azure Portal" border="true":::

3. A portál Fürt **irányítópultok szakaszában** válassza a **Jupyter Notebook.** Ha a rendszer kéri, adja meg a fürthöz tartozó bejelentkezési hitelesítő adatokat.

   :::image type="content" source="./media/apache-spark-jupyter-spark-sql/hdinsight-spark-open-jupyter-interactive-spark-sql-query.png " alt-text="Nyissa Jupyter Notebook interaktív Spark SQL-lekérdezés futtatásához" border="true":::

4. Jegyzetfüzet **létrehozásához** válassza az Új  >  **PySpark** lehetőséget.

   :::image type="content" source="./media/apache-spark-jupyter-spark-sql/hdinsight-spark-create-jupyter-interactive-spark-sql-query.png " alt-text="Hozzon létre egy Jupyter Notebook-fájlt interaktív Spark SQL-lekérdezés futtatásához" border="true":::

   Az új notebook létrejött, és Untitled(Untitled.pynb) néven nyílt meg.

## <a name="run-apache-spark-sql-statements"></a>Sql Apache Spark-utasítások futtatása

Az SQL az adatok lekérdezésére és átalakítására leggyakrabban és legszélesebb körben használt nyelv. A Spark SQL az Apache Spark bővítményeként működik a strukturált adatok ismerős SQL-szintaxissal való feldolgozásához.

1. Ellenőrizze, hogy a kernel készen áll-e. A kernel akkor áll készen, ha a neve mellett a notebookban egy üres kör látható. A teli kör azt jelenti, hogy a kernel foglalt.

    :::image type="content" source="./media/apache-spark-jupyter-spark-sql/jupyter-spark-kernel-status.png " alt-text="Kernelállapot" border="true"::: alt-text="Kernelállapot" border="true":::

    A notebook első indításakor a kernel a háttérben elvégez néhány feladatot. Várja meg, hogy a kernel elkészüljön.

1. Illessze be a következő kódot egy üres cellába, majd nyomja le a **SHIFT + ENTER** billentyűkombinációt annak futtatásához. A parancs felsorolja a fürtön található Hive-táblákat:

    ```sql
    %%sql
    SHOW TABLES
    ```

    Ha egy Jupyter Notebook használ a HDInsight-fürthöz, egy előre beállított munkamenetet kap, amely Hive-lekérdezések Spark SQL-sel való `spark` futtatására használható. A `%%sql` megadja a Jupyter-notebook számára, hogy az előre beállított `spark`-munkamenetet használja a Hive-lekérdezés futtatásához. A lekérdezés lekérdezi az első 10 sort egy Hive-táblából (**hivesampletable**), amely alapértelmezés szerint minden HDInsight-fürtben megtalálható. A lekérdezés első elküldésekor a Jupyter létrehoz egy Spark-alkalmazást a notebookhoz. Ez körülbelül 30 másodpercet vesz igénybe. Ha a Spark-alkalmazás elkészült, a lekérdezés végrehajtása körülbelül egy másodperc alatt meg fog állni, és elő állítja az eredményeket. A kimenet a következőképpen fog kinézni:

    :::image type="content" source="./media/apache-spark-jupyter-spark-sql/hdinsight-spark-get-started-hive-query.png " alt-text="Apache Hive HDInsight" border="true":::y in HDInsight" border="true":::

    Minden alkalommal, amikor a Jupyterben lekérdezést futtat, a webböngésző ablakának címsorában **(Foglalt)** állapot jelenik meg a notebook neve mellett. A jobb felső sarokban lévő **PySpark** felirat mellett ekkor egy teli kör is megjelenik.

1. Futtasson egy másik lekérdezést a `hivesampletable` adatainak megtekintéséhez.

    ```sql
    %%sql
    SELECT * FROM hivesampletable LIMIT 10
    ```

    A képernyő frissül, és megjeleníti a lekérdezés kimenetét.

    :::image type="content" source="./media/apache-spark-jupyter-spark-sql/hdinsight-spark-get-started-hive-query-output.png " alt-text="Hive-lekérdezés kimenete a HDInsightban" border="true"::: Insight" border="true":::

1. A notebook **File** (Fájl) menüjében kattintson a **Close and Halt** (Bezárás és leállítás) elemre. A jegyzetfüzet leállítása felszabadítja a fürt erőforrásait, beleértve a Spark-alkalmazást is.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

A rövid útmutató befejezése után törölheti a fürtöt. A HDInsight segítségével az adatok az Azure Storage-ban tárolódnak, így a nem használt fürtök biztonságosan törölhetők. A HDInsight-fürtökért akkor is díjat kell fizetnie, ha nincs használatban. Mivel a fürt díja sokszor több, mint a tárolás díja, gazdasági szempontból logikus törölni a nem használt fürtök költségeit.

A Azure Portal lépjen a fürtre, és válassza a **Törlés lehetőséget.**

:::image type="content" source="./media/apache-spark-jupyter-spark-sql/hdinsight-azure-portal-delete-cluster.png " alt-text="Azure Portal HDInsight-fürt" border="true":::rálátási fürtje" border="true":::

Az erőforráscsoport nevét kiválasztva is megnyílik az erőforráscsoport oldala, ahol kiválaszthatja az **Erőforráscsoport törlése** elemet. Az erőforráscsoport törlésével a HDInsight-fürtöt és az alapértelmezett tárfiókot is törli.

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban megtanulta, hogyan hozhat létre Apache Spark fürtöt a HDInsightban, és hogyan futtathat alapszintű Spark SQL-lekérdezést. A következő oktatóanyag azt is bemutatja, hogyan futtathat interaktív lekérdezéseket mintaadatokon egy HDInsight-fürt használatával.

> [!div class="nextstepaction"]
> [Interaktív lekérdezések futtatása Apache Spark](./apache-spark-load-data-run-query.md)
