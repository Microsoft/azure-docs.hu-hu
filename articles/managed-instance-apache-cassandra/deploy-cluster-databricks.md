---
title: Gyors útmutató – felügyelt Apache Spark-fürt üzembe helyezése Azure Databricks
description: Ez a rövid útmutató bemutatja, hogyan helyezhet üzembe egy felügyelt Apache Spark-fürtöt az Azure Portal használatával Azure Databricks segítségével.
author: TheovanKraay
ms.author: thvankra
ms.service: managed-instance-apache-cassandra
ms.topic: quickstart
ms.date: 03/02/2021
ms.openlocfilehash: fd0d5c5b73ae1fb1eae7f38a22913018555ebe11
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "101748710"
---
# <a name="quickstart-deploy-a-managed-apache-spark-cluster-preview-with-azure-databricks"></a>Gyors útmutató: felügyelt Apache Spark-fürt (előzetes verzió) üzembe helyezése Azure Databricks

Az Apache Cassandra Azure felügyelt példánya automatizált üzembe helyezési és skálázási műveleteket biztosít a felügyelt nyílt forráskódú Apache Cassandra-adatközpontok számára, felgyorsítja a hibrid forgatókönyveket, és csökkenti a folyamatos karbantartást.

> [!IMPORTANT]
> Az Apache Cassandra Azure felügyelt példánya jelenleg nyilvános előzetes verzióban érhető el.
> Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik.
> További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Ez a rövid útmutató azt ismerteti, hogyan használható a Azure Portal egy teljes körűen felügyelt Apache Spark-fürt létrehozásához az Azure-beli felügyelt példány Azure-beli Virtual Networkén az Apache Cassandra-fürtön belül. A Spark-fürtöt a Azure Databricksban fogja létrehozni. Később létrehozhat vagy csatolhat jegyzetfüzeteket a fürthöz, beolvashatja a különböző adatforrásokból származó adatok adatait, és elemezheti az elemzéseket.

További információt az [Azure-Virtual Network (Virtual Network injekció) Azure Databricks üzembe helyezésével](/azure/databricks/administration-guide/cloud-configurations/azure/vnet-inject)kapcsolatos részletes utasításokról is talál.

## <a name="create-an-azure-databricks-cluster"></a>Azure Databricks-fürt létrehozása

Az alábbi lépéseket követve hozzon létre egy Azure Databricks-fürtöt egy olyan Virtual Network, amely rendelkezik az Azure felügyelt példányával az Apache Cassandra használatával:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

1. A bal oldali navigációs panelen keresse meg az **erőforráscsoportok** lehetőséget, és keresse meg azt az erőforráscsoportot, amely tartalmazza a felügyelt példány központi telepítését Virtual Network.

1. Nyissa meg a **Virtual Network** erőforrást, és jegyezze fel a **címterület méretét**:

    :::image type="content" source="./media/deploy-cluster-databricks/virtual-network-address-space.png" alt-text="A Virtual Network címterület beolvasása." border="true":::

1. Az erőforráscsoport területen válassza a **Hozzáadás** lehetőséget, majd keressen rá **Azure Databricks** a keresőmezőbe:

    :::image type="content" source="./media/deploy-cluster-databricks/databricks.png" alt-text="Azure Databricks keresése." border="true":::

1. Azure Databricks fiók létrehozásához válassza a **Létrehozás** elemet:

    :::image type="content" source="./media/deploy-cluster-databricks/databricks-create.png" alt-text="Hozzon létre egy Azure Databricks fiókot." border="true":::

1. Adja meg az alábbi értékeket:

   * **Munkaterület neve** – adja meg a Databricks-munkaterület nevét.
   * **Régió** – ügyeljen arra, hogy ugyanazt a régiót válassza, mint a Virtual Network.
   * **Díjszabási szint** – válassza a standard, prémium vagy próbaverzió lehetőséget. További információkért a csomagokkal kapcsolatban tekintse meg a [Databricks díjszabását ismertető oldalt](https://azure.microsoft.com/pricing/details/databricks/).

    :::image type="content" source="./media/deploy-cluster-databricks/select-name.png" alt-text="Töltse ki a munkaterület nevét, régióját és árképzési szintjét a Databricks-fiókhoz." border="true":::

1. Ezután válassza a **hálózatkezelés** lapot, és adja meg a következő adatokat:

   * **Azure Databricks munkaterület üzembe helyezése a Virtual Networkban (VNet)** – válassza az **Igen** lehetőséget.
   * **Virtual Network** – a legördülő listából válassza ki azt a Virtual Network, ahol a felügyelt példány létezik.
   * **Nyilvános alhálózat neve** – adja meg a nyilvános alhálózat nevét.
   * **Nyilvános ALHÁLÓZAT CIDR tartománya** – adjon meg egy IP-címtartományt a nyilvános alhálózat számára.
   * **Magánhálózati alhálózat neve** – adja meg a magánhálózati alhálózat nevét.
   * **Magánhálózati ALHÁLÓZAT CIDR tartománya** – adjon meg egy IP-címtartományt a privát alhálózat számára.

   A tartomány-ütközések elkerülése érdekében győződjön meg arról, hogy nagyobb tartományokat választ ki. Ha szükséges, használja a [vizualizáció alhálózati kalkulátort](https://www.fryguy.net/wp-content/tools/subnets.html) a tartományok felosztásához:

   :::image type="content" source="./media/deploy-cluster-databricks/subnet-calculator.png" alt-text="Használja a Virtual Network alhálózati számológépet." border="true":::

   Az alábbi képernyőkép a hálózatkezelés ablaktáblán látható részleteket mutatja:

   :::image type="content" source="./media/deploy-cluster-databricks/subnets.png" alt-text="A nyilvános és a privát alhálózatok nevének megadásához." border="true":::

1. Válassza a **felülvizsgálat és létrehozás** , majd a **Létrehozás** lehetőséget a munkaterület telepítéséhez.

1. A **munkaterület elindítása** a létrehozása után.

1. A rendszer átirányítja az Azure Databricks portáljára. A portálon válassza az **új fürt** lehetőséget.

1. Az **új fürt** ablaktáblán fogadja el az alapértelmezett értékeket a következő mezőktől eltérő összes mezőhöz:

   * **Fürt neve** – adja meg a fürt nevét.
   * **Databricks Runtime verzió** – válassza ki a Cassandra-összekötő által támogatott Scala 2,11 vagy alacsonyabb verziót.

    :::image type="content" source="./media/deploy-cluster-databricks/spark-cluster.png" alt-text="Válassza ki a Databricks futásidejű verzióját és a Spark-fürtöt." border="true":::

1. Bontsa ki a **Speciális beállítások elemet** , és adja hozzá a következő konfigurációt. Ne felejtse el lecserélni a csomópont IP-címeit és a hitelesítő adatokat:

    ```java
    spark.cassandra.connection.host <node1 IP>,<node 2 IP>, <node IP>
    spark.cassandra.auth.password cassandra
    spark.cassandra.connection.port 9042
    spark.cassandra.auth.username cassandra
    spark.cassandra.connection.ssl.enabled true
    ```

1. A **kódtárak** lapon telepítse a Spark-összekötő legújabb verzióját Cassandra (*Spark-Cassandra-Connector*), és indítsa újra a fürtöt:

    :::image type="content" source="./media/deploy-cluster-databricks/connector.png" alt-text="Telepítse a Cassandra-összekötőt." border="true":::

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha nem kívánja tovább használni ezt a felügyelt példány-fürtöt, törölje a következő lépésekkel:

1. Azure Portal bal oldali menüjében válassza az **erőforráscsoportok** lehetőséget.
1. A listából válassza ki az ehhez a rövid útmutatóhoz létrehozott erőforráscsoportot.
1. Az erőforráscsoport **Áttekintés** paneljén válassza az **erőforráscsoport törlése** elemet.
3. A következő ablakban adja meg a törölni kívánt erőforráscsoport nevét, majd válassza a **Törlés** lehetőséget.

## <a name="next-steps"></a>Következő lépések

Ebből a rövid útmutatóból megtudhatta, hogyan hozhat létre teljes mértékben felügyelt Apache Spark-fürtöt az Azure felügyelt példányának az Apache Cassandra-fürtön belüli Virtual Network. Ezután megtudhatja, hogyan kezelheti a fürt és az adatközpont erőforrásait: 

> [!div class="nextstepaction"]
> [Azure felügyelt példány kezelése Apache Cassandra-erőforrásokhoz az Azure CLI használatával](manage-resources-cli.md)

