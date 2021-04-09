---
title: 'Gyors útmutató: Apache HBase-fürt létrehozása sablon használatával – Azure HDInsight'
description: Ez a rövid útmutató bemutatja, hogyan hozhat létre egy Apache HBase-fürtöt az Azure HDInsight a Resource Manager-sablonok használatával.
ms.service: hdinsight
ms.topic: quickstart
ms.custom: subject-armqs
ms.date: 03/12/2020
ms.openlocfilehash: 0d56fa4e1c93cec514076593d229e58b25af142a
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "104865145"
---
# <a name="quickstart-create-apache-hbase-cluster-in-azure-hdinsight-using-arm-template"></a>Gyors útmutató: Apache HBase-fürt létrehozása az Azure HDInsight az ARM-sablon használatával

Ebben a rövid útmutatóban egy Azure Resource Manager sablon (ARM-sablon) használatával hozhat létre [Apache HBase](./apache-hbase-overview.md) -fürtöt az Azure HDInsight. A HBase egy nyílt forráskódú, NoSQL-adatbázis, amely a [Google BigTable](https://cloud.google.com/bigtable/)után készült, és Apache Hadoop a modellen alapul.

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]

Ha a környezet megfelel az előfeltételeknek, és már ismeri az ARM-sablonokat, kattintson az **Üzembe helyezés az Azure-ban** gombra. A sablon az Azure Portalon fog megnyílni.

[:::image type="icon" source="../../media/template-deployments/deploy-to-azure.svg" alt-text="Üzembe helyezés az Azure-ban":::](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-hbase-linux%2Fazuredeploy.json)

## <a name="prerequisites"></a>Előfeltételek

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a name="review-the-template"></a>A sablon áttekintése

Az ebben a gyorsútmutatóban használt sablon az [Azure-gyorssablonok](https://azure.microsoft.com/resources/templates/101-hdinsight-hbase-linux/) közül származik.

:::code language="json" source="~/quickstart-templates/101-hdinsight-hbase-linux/azuredeploy.json":::

Két Azure-erőforrás van definiálva a sablonban:

* [Microsoft. Storage/storageAccounts](/azure/templates/microsoft.storage/storageaccounts): hozzon létre egy Azure Storage-fiókot.
* [Microsoft. HDInsight/cluster](/azure/templates/microsoft.hdinsight/clusters): hozzon létre egy HDInsight-fürtöt.

## <a name="deploy-the-template"></a>A sablon üzembe helyezése

1. Az Azure-ba való bejelentkezéshez és az ARM-sablon megnyitásához válassza az alábbi **üzembe helyezés az Azure** -ban gombot.

    [:::image type="icon" source="../../media/template-deployments/deploy-to-azure.svg" alt-text="Üzembe helyezés az Azure-ban":::](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-hbase-linux%2Fazuredeploy.json)

1. Adja meg vagy válassza ki a következő értékeket:

    |Tulajdonság |Leírás |
    |---|---|
    |Előfizetés|A legördülő listában válassza ki a fürthöz használt Azure-előfizetést.|
    |Erőforráscsoport|A legördülő listából válassza ki a meglévő erőforráscsoportot, vagy válassza az **új létrehozása** lehetőséget.|
    |Hely|Az érték automatikusan kitöltődik az erőforráscsoporthoz használt hellyel.|
    |Fürt neve|Adjon meg egy globálisan egyedi nevet. Ehhez a sablonhoz csak kisbetűket és számokat használjon.|
    |Fürt bejelentkezési felhasználóneve|Adja meg a felhasználónevet, az alapértelmezett érték a **rendszergazda**.|
    |Fürt bejelentkezési jelszava|Adja meg a jelszót. A jelszónak legalább 10 karakterből kell állnia, és tartalmaznia kell legalább egy számot, egy nagybetűs és egy kisbetűs betűt, egy nem alfanumerikus karaktert (kivéve a következő karaktereket: "" "). |
    |SSH-Felhasználónév|Adja meg a felhasználónevet, az alapértelmezett érték a sshuser.|
    |SSH-jelszó|Adja meg a jelszót.|

    :::image type="content" source="./media/quickstart-resource-manager-template/resource-manager-template-hbase.png" alt-text="Resource Manager-sablon HBase üzembe helyezése" border="true":::

1. Tekintse át a **használati** feltételeket. Ezután válassza **az Elfogadom a fenti feltételeket és kikötéseket**, majd a **vásárlás** lehetőséget. Értesítést kap arról, hogy a telepítés folyamatban van. Egy fürt létrehozása nagyjából 20 percet vesz igénybe.


## <a name="review-deployed-resources"></a>Üzembe helyezett erőforrások áttekintése

A fürt létrehozása után az **üzembe helyezés sikeres** értesítést fog kapni, amely a **Go to Resource** hivatkozást fogja tartalmazni. Az erőforráscsoport lap felsorolja az új HDInsight-fürtöt és a fürthöz társított alapértelmezett tárolót. Minden fürt rendelkezik egy [Azure Blob Storage](../hdinsight-hadoop-use-blob-storage.md) -fiókkal, egy [Azure Data Lake Storage Gen1](../hdinsight-hadoop-use-data-lake-storage-gen1.md)vagy egy  [`Azure Data Lake Storage Gen2`](../hdinsight-hadoop-use-data-lake-storage-gen2.md) függőséggel. Ezt az alapértelmezett Storage-fióknak nevezzük. A HDInsight-fürtnek és az alapértelmezett Storage-fióknak ugyanabban az Azure-régióban kell elhelyezkednie. A fürtök törlésével nem törlődik a Storage-fiók.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

A gyors üzembe helyezés befejezése után érdemes lehet törölni a fürtöt. A HDInsight az adatait az Azure Storage tárolja, így biztonságosan törölheti a fürtöt, ha az nincs használatban. A HDInsight-fürtökért is fizetnie kell, még akkor is, ha nincs használatban. Mivel a fürt díjai több időt vesznek igénybe, mint a tárterületre vonatkozó díjak, a gazdasági érzékek törlik a fürtöket, ha nincsenek használatban.

A Azure Portal navigáljon a fürthöz, és válassza a **Törlés** lehetőséget.

[Resource Manager-sablon HBase törlése](./media/quickstart-resource-manager-template/azure-portal-delete-hbase.png)

Az erőforráscsoport nevét kiválasztva is megnyílik az erőforráscsoport oldala, ahol kiválaszthatja az **Erőforráscsoport törlése** elemet. Az erőforráscsoport törlésével törli a HDInsight-fürtöt és az alapértelmezett Storage-fiókot is.

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban megtanulta, hogyan hozhat létre egy Apache HBase-fürtöt a HDInsight egy ARM-sablonnal. A következő cikkben megtudhatja, hogyan kérdezheti le a HBase a HDInsight-ben a HBase Shell használatával.

> [!div class="nextstepaction"]
> [Apache HBase lekérdezése az Azure HDInsight a HBase-Rendszerhéjral](./query-hbase-with-hbase-shell.md)
