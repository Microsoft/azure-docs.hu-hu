---
title: Azure HDInsight-Azure Data Lake Storage Gen2 létrehozása – portál
description: Ismerje meg, hogyan használhatja a Azure Data Lake Storage Gen2t az Azure HDInsight-fürtökkel a portál használatával.
author: guyhay
ms.author: guyhay
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive,seoapr2020
ms.date: 09/17/2020
ms.openlocfilehash: 5eb6cfc7f0dbc0bc886550e1d601dc52dff66267
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "104866369"
---
# <a name="create-a-cluster-with-data-lake-storage-gen2-using-the-azure-portal"></a>Hozzon létre egy fürtöt Data Lake Storage Gen2 használatával a Azure Portal

A Azure Portal egy webalapú felügyeleti eszköz a Microsoft Azure felhőben üzemeltetett szolgáltatásokhoz és erőforrásokhoz. Ebből a cikkből megtudhatja, hogyan hozhat létre Linux-alapú Azure HDInsight-fürtöket a portál használatával. További részletek állnak rendelkezésre a [HDInsight-fürtök létrehozásával](./hdinsight-hadoop-provision-linux-clusters.md)kapcsolatban.

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

Ha még nincs Azure-előfizetése, kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

A Data Lake Storage Gen2 for Storage szolgáltatást használó HDInsight-fürt létrehozásához kövesse az alábbi lépéseket egy hierarchikus névtérrel rendelkező Storage-fiók konfigurálásához.

## <a name="create-a-user-assigned-managed-identity"></a>Felhasználó által hozzárendelt felügyelt identitás létrehozása

Hozzon létre egy felhasználó által hozzárendelt felügyelt identitást, ha még nem rendelkezik ilyennel.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. A bal felső sarokban kattintson az **erőforrás létrehozása** elemre.
1. A keresőmezőbe írja be a **felhasználó által hozzárendelt** értéket, és kattintson a **felhasználóhoz rendelt felügyelt identitás** elemre.
1. Kattintson a **Létrehozás** lehetőségre.
1. Adja meg a felügyelt identitás nevét, válassza ki a megfelelő előfizetést, erőforráscsoportot és helyet.
1. Kattintson a **Létrehozás** lehetőségre.

További információ arról, hogyan működnek a felügyelt identitások az Azure HDInsight-ben: [felügyelt identitások az Azure HDInsight](hdinsight-managed-identities.md).

:::image type="content" source="./media/hdinsight-hadoop-use-data-lake-storage-gen2/create-user-assigned-managed-identity-portal.png" alt-text="Felhasználó által hozzárendelt felügyelt identitás létrehozása":::

## <a name="create-a-storage-account-to-use-with-data-lake-storage-gen2"></a>Data Lake Storage Gen2hez használandó Storage-fiók létrehozása

Hozzon létre egy Azure Data Lake Storage Gen2hoz használandó Storage-fiókot.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. A bal felső sarokban kattintson az **erőforrás létrehozása** elemre.
1. A keresőmezőbe írja be a **Storage** kifejezést, majd kattintson a **Storage-fiók** elemre.
1. Kattintson a **Létrehozás** lehetőségre.
1. A **Storage-fiók létrehozása** képernyőn:
    1. Válassza ki a megfelelő előfizetést és erőforráscsoportot.
    1. Adja meg a Storage-fiók nevét Data Lake Storage Gen2.
    1. Kattintson a **speciális** fülre.
    1. Kattintson a **Data Lake Storage Gen2** alatt található **hierarchikus névtér** melletti **engedélyezve** lehetőségre.
    1. Kattintson a **Felülvizsgálat + létrehozás** elemre.
    1. Kattintson a **Létrehozás** gombra

A Storage-fiók létrehozása során felmerülő egyéb lehetőségekkel kapcsolatos további információkért lásd: gyors útmutató [: Storage-fiók létrehozása Azure Data Lake Storage Gen2hoz](../storage/blobs/create-data-lake-storage-account.md).

:::image type="content" source="./media/hdinsight-hadoop-use-data-lake-storage-gen2/azure-data-lake-storage-account-create-advanced.png" alt-text="A Storage-fiók létrehozását bemutató képernyőkép a Azure Portal":::

## <a name="set-up-permissions-for-the-managed-identity-on-the-data-lake-storage-gen2"></a>A felügyelt identitásra vonatkozó engedélyek beállítása a Data Lake Storage Gen2

Rendelje hozzá a felügyelt identitást a Storage- **blob adattulajdonosi** szerepkörhöz a Storage-fiókban.

1. A [Azure Portal](https://portal.azure.com)nyissa meg a Storage-fiókját.
1. Válassza ki a Storage-fiókját, majd válassza a **hozzáférés-vezérlés (iam)** lehetőséget a fiók hozzáférés-vezérlési beállításainak megjelenítéséhez. Válassza ki a **szerepkör-hozzárendelések** lapot a szerepkör-hozzárendelések listájának megtekintéséhez.

    :::image type="content" source="./media/hdinsight-hadoop-use-data-lake-storage-gen2/portal-access-control.png" alt-text="A Storage hozzáférés-vezérlési beállításait ábrázoló képernyőfelvétel":::

1. Új szerepkör hozzáadásához kattintson a **+ szerepkör-hozzárendelés hozzáadása** gombra.
1. A **szerepkör-hozzárendelés hozzáadása** ablakban válassza ki a **Storage blob-adat tulajdonosi** szerepkörét. Ezután válassza ki azt az előfizetést, amelynek a felügyelt identitás-és Storage-fiókja van. Ezután a Keresés gombra kattintva keresse meg a korábban létrehozott, felhasználó által hozzárendelt felügyelt identitást. Végül válassza ki a felügyelt identitást, és a rendszer a **kijelölt tagok** területen jelenik meg.

    :::image type="content" source="./media/hdinsight-hadoop-use-data-lake-storage-gen2/add-rbac-role3-window.png" alt-text="Az Azure-szerepkörök hozzárendelését bemutató képernyőkép":::

1. Kattintson a **Mentés** gombra. A kiválasztott felhasználó által hozzárendelt identitás mostantól a kiválasztott szerepkör alatt szerepel.
1. A kezdeti beállítás befejezése után létrehozhat egy fürtöt a portálon keresztül. A fürtnek ugyanabban az Azure-régióban kell lennie, mint a Storage-fióknak. A fürt létrehozási menüjének **tárterület** lapján válassza a következő beállításokat:

    * Az **elsődleges tároló típusa** beállításnál válassza a **Azure Data Lake Storage Gen2** lehetőséget.
    * Az **elsődleges Storage-fiók** területen keresse meg és válassza ki az újonnan létrehozott Storage-fiókot Data Lake Storage Gen2 tárolóval.

    * Az **identitás** területen válassza ki az újonnan létrehozott, felhasználó által hozzárendelt felügyelt identitást.

        :::image type="content" source="./media/hdinsight-hadoop-use-data-lake-storage-gen2/azure-portal-cluster-storage-gentwo.png" alt-text="A Data Lake Storage Gen2 Azure HDInsight való használatának tárolási beállításai":::

    > [!NOTE]
    > * Ha Data Lake Storage Gen2 használatával szeretne másodlagos Storage-fiókot hozzáadni, akkor a Storage-fiók szintjén egyszerűen rendelje hozzá a korábban létrehozott felügyelt identitást a hozzáadni kívánt új Data Lake Storage Gen2hoz. Javasoljuk, hogy adjon hozzá egy másodlagos Storage-fiókot Data Lake Storage Gen2 a "további Storage-fiókok" panelen a HDInsight nem támogatott.
    > * Engedélyezheti az RA-GRS vagy az RA-ZRS az Azure Blob Storage-fiókban, amelyet a HDInsight használ. A fürt az RA-GRS vagy az RA-ZRS másodlagos végponttal való létrehozása azonban nem támogatott.

## <a name="delete-the-cluster"></a>A fürt törlése

Lásd: [HDInsight-fürt törlése a böngésző, a PowerShell vagy az Azure CLI használatával](./hdinsight-delete-cluster.md).

## <a name="troubleshoot"></a>Hibaelhárítás

Ha problémába ütközik a HDInsight-fürtök létrehozása során, tekintse meg [a hozzáférés-vezérlésre vonatkozó követelményeket](./hdinsight-hadoop-customize-cluster-linux.md#access-control).

## <a name="next-steps"></a>Következő lépések

Sikeresen létrehozott egy HDInsight-fürtöt. Most megtudhatja, hogyan dolgozhat a fürttel.

### <a name="apache-spark-clusters"></a>Fürtök Apache Spark

* [Linux-alapú HDInsight-fürtök testreszabása parancsfájl-műveletek használatával](hdinsight-hadoop-customize-cluster-linux.md)
* [Önálló alkalmazás létrehozása a Scala használatával](spark/apache-spark-create-standalone-application.md)
* [Feladatok távoli futtatása egy Apache Spark-fürtön az Apache Livy használatával](spark/apache-spark-livy-rest-interface.md)
* [Apache Spark BI: interaktív adatelemzés végrehajtása a Spark on HDInsight és a BI Tools használatával](spark/apache-spark-use-bi-tools.md)
* [Apache Spark a Machine Learning használatával: az élelmiszer-ellenőrzési eredmények előrejelzéséhez használja a Spark in HDInsight](spark/apache-spark-machine-learning-mllib-ipython.md)

### <a name="apache-hadoop-clusters"></a>Fürtök Apache Hadoop

* [Apache Hive használata a HDInsight](hadoop/hdinsight-use-hive.md)
* [A MapReduce használata a HDInsight](hadoop/hdinsight-use-mapreduce.md)

### <a name="apache-hbase-clusters"></a>Apache HBase-fürtök

* [Ismerkedés az Apache HBase a HDInsight](hbase/apache-hbase-tutorial-get-started-linux.md)
* [Java-alkalmazások fejlesztése az Apache HBase a HDInsight](hbase/apache-hbase-build-java-maven-linux.md)
