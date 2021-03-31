---
title: Rövid útmutató – Azure felügyelt példány létrehozása Apache Cassandra-fürthöz a Azure Portal
description: Ez a rövid útmutató bemutatja, hogyan hozhat létre Azure-beli felügyelt példányt az Apache Cassandra-fürthöz a Azure Portal használatával.
author: TheovanKraay
ms.author: thvankra
ms.service: managed-instance-apache-cassandra
ms.topic: quickstart
ms.date: 03/02/2021
ms.custom: references_regions
ms.openlocfilehash: cb555eefb19b5db7ed7eb0792a813c295a4bf38b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "104588613"
---
# <a name="quickstart-create-an-azure-managed-instance-for-apache-cassandra-cluster-from-the-azure-portal-preview"></a>Gyors útmutató: Azure felügyelt példány létrehozása az Apache Cassandra-fürthöz a Azure Portal (előzetes verzió)
 
Az Apache Cassandra Azure felügyelt példánya automatizált üzembe helyezési és skálázási műveleteket biztosít a felügyelt nyílt forráskódú Apache Cassandra-adatközpontok számára, felgyorsítja a hibrid forgatókönyveket, és csökkenti a folyamatos karbantartást.

> [!IMPORTANT]
> Az Apache Cassandra Azure felügyelt példánya jelenleg nyilvános előzetes verzióban érhető el.
> Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik.
> További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Ez a rövid útmutató azt ismerteti, hogyan használható a Azure Portal egy Azure felügyelt példány létrehozásához az Apache Cassandra-fürthöz.

## <a name="prerequisites"></a>Előfeltételek

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a name="create-a-managed-instance-cluster"></a><a id="create-account"></a>Felügyelt példány fürt létrehozása

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

1. A keresősáv alatt keresse meg az **Apache Cassandra felügyelt példányát** , és válassza ki az eredményt.

   :::image type="content" source="./media/create-cluster-portal/search-portal.png" alt-text="Felügyelt példány keresése az Apache Cassandra-hoz." lightbox="./media/create-cluster-portal/search-portal.png" border="true":::

1. Válassza **a felügyelt példány létrehozása az Apache Cassandra-fürthöz** gombot.

   :::image type="content" source="./media/create-cluster-portal/create-cluster.png" alt-text="A fürt létrehozása." lightbox="./media/create-cluster-portal/create-cluster.png" border="true":::

1. A **felügyelt példány létrehozása az Apache Cassandra** panelhez lapon adja meg a következő adatokat:

   * **Előfizetés** – a legördülő listából válassza ki az Azure-előfizetését.
   * **Erőforráscsoport**– megadhatja, hogy új erőforráscsoportot kíván-e létrehozni, vagy egy meglévőt szeretne használni. Az erőforráscsoport olyan tároló, amely egy adott Azure-megoldás kapcsolódó erőforrásait tartalmazza. További információ: az [Azure-erőforráscsoport](../azure-resource-manager/management/overview.md) áttekintése című cikk.
   * **Fürt neve** – adja meg a fürt nevét.
   * **Hely** – hely, ahol a fürt üzembe lesz helyezve.
   * **SKU** – a fürthöz tartozó SKU típusa.
   * **Nem. csomópontok**– fürt csomópontjainak száma. Ezek a csomópontok az adatai replikái.
   * **Kezdeti Cassandra admin password** – a fürt létrehozásához használt jelszó.
   * Adja meg a **Cassandra admin password jelszót** – írja be újra a jelszót.

    > [!NOTE]
    > A nyilvános előzetes verzióban létrehozhatja a felügyelt példány fürtöt az USA keleti régiójában, az USA nyugati régiójában, az USA keleti régiójában, az USA 2. nyugati régiójában, az USA középső régiójában *, az USA déli középső régiójában, Észak-Európa, Nyugat-Európa, déli Kelet-Ázsia és Kelet-Ausztrália*

   :::image type="content" source="./media/create-cluster-portal/create-cluster-page.png" alt-text="Töltse ki a fürt létrehozása űrlapot." lightbox="./media/create-cluster-portal/create-cluster-page.png" border="true":::

1. Ezután válassza a **hálózatkezelés** lapot.

1. A **hálózat** ablaktáblán válassza ki a **Virtual Network** nevét és **alhálózatát**. Választhat meglévő Virtual Network, vagy létrehozhat egy újat.

   :::image type="content" source="./media/create-cluster-portal/networking.png" alt-text="Konfigurálja a hálózatkezelés részleteit." lightbox="./media/create-cluster-portal/networking.png" border="true":::

1. Ha az utolsó lépésben létrehozott egy új VNet, ugorjon a 8. lépésre. Ha a fürt létrehozása előtt kiválasztott egy meglévő VNet, néhány speciális engedélyt kell alkalmaznia a Virtual Network és az alhálózatra. Ehhez használja a `az role assignment create` parancsot, cserélje le a, `<subscription ID>` , `<resource group name>` `<VNet name>` és `<subnet name>` értéket a megfelelő értékekre:

   ```azurecli-interactive
   az role assignment create --assignee e5007d2c-4b13-4a74-9b6a-605d99f03501 --role 4d97b98b-1d4f-4787-a291-c67834d212e7 --scope /subscriptions/<subscription ID>/resourceGroups/<resource group name>/providers/Microsoft.Network/virtualNetworks/<VNet name>/subnets/<subnet name>
   ```

   > [!NOTE]
   > Az `assignee` `role` előző parancs és értékei rögzített értékek, pontosan a parancsban említett értékeket adja meg. Ha ezt nem teszi meg, a rendszer hibákat fog eredményezni a fürt létrehozásakor. Ha hibát tapasztal a parancs végrehajtásakor, előfordulhat, hogy nem rendelkezik a futtatásához szükséges engedélyekkel, forduljon a rendszergazdához.

1. Most, hogy elvégezte a hálózatkezelést, kattintson a **felülvizsgálat +**  >  **Létrehozás** létrehozása lehetőségre.

    > [!NOTE]
    > A fürt létrehozása akár 15 percet is igénybe vehet.

   :::image type="content" source="./media/create-cluster-portal/review-create.png" alt-text="A fürt létrehozásához tekintse át az összegzést." lightbox="./media/create-cluster-portal/review-create.png" border="true":::


1. Az üzembe helyezés befejeződése után ellenőrizze az erőforráscsoportot, hogy az újonnan létrehozott felügyelt példány-fürtöt szeretné-e látni:

   :::image type="content" source="./media/create-cluster-portal/managed-instance.png" alt-text="A fürt létrehozása után áttekintő oldal." lightbox="./media/create-cluster-portal/managed-instance.png" border="true":::

1. A fürtcsomópontok közötti tallózáshoz navigáljon a fürt létrehozásához használt Virtual Network ablaktáblára, és nyissa meg az **Áttekintés** panelt, ahol megtekintheti őket:

   :::image type="content" source="./media/create-cluster-portal/resources.png" alt-text="Tekintse meg a fürt erőforrásait." lightbox="./media/create-cluster-portal/resources.png" border="true":::


## <a name="connecting-to-your-cluster"></a>Csatlakozás a fürthöz

Az Apache Cassandra Azure felügyelt példánya nem hoz létre nyilvános IP-címekkel rendelkező csomópontokat, így az újonnan létrehozott Cassandra-fürthöz való kapcsolódáshoz létre kell hoznia egy másik erőforrást a VNet belül. Ez lehet egy alkalmazás, vagy egy olyan virtuális gép, amelyen az Apache nyílt forráskódú lekérdezési eszköze [CQLSH](https://cassandra.apache.org/doc/latest/tools/cqlsh.html) van telepítve. Az Ubuntu rendszerű virtuális gépeket [sablon](https://azure.microsoft.com/resources/templates/101-vm-simple-linux/) használatával is üzembe helyezheti. Üzembe helyezéskor az SSH használatával csatlakozzon a géphez, és telepítse a CQLSH az alábbi parancsokkal:

```bash
# Install default-jre and default-jdk
sudo apt update
sudo apt install openjdk-8-jdk openjdk-8-jre

# Install the Cassandra libraries in order to get CQLSH:
echo "deb http://www.apache.org/dist/cassandra/debian 311x main" | sudo tee -a /etc/apt/sources.list.d/cassandra.sources.list
curl https://downloads.apache.org/cassandra/KEYS | sudo apt-key add -
sudo apt-get update
sudo apt-get install cassandra

# Export the SSL variables:
export SSL_VERSION=TLSv1_2
export SSL_VALIDATE=false

# Connect to CQLSH (replace <IP> with the private IP addresses of the nodes in your Datacenter):
host=("<IP>" "<IP>" "<IP>")
cqlsh $host 9042 -u cassandra -p cassandra --ssl
```

## <a name="troubleshooting"></a>Hibaelhárítás

Ha a Virtual Networkre vonatkozó engedélyek alkalmazása során hibát tapasztal, például *nem találja a felhasználó vagy az egyszerű szolgáltatásnév kifejezést a Graph adatbázisban a "e5007d2c-4b13-4a74-9b6a-605d99f03501"* értékre, akkor ugyanezt az engedélyt manuálisan is alkalmazhatja a Azure Portal. Az engedélyek a portálról való alkalmazásához lépjen a meglévő virtuális hálózat **hozzáférés-vezérlés (iam)** paneljére, és adjon hozzá egy szerepkör-hozzárendelést a "Azure Cosmos db" szerepkörhöz a "hálózati rendszergazda" szerepkörhöz. Ha két bejegyzés jelenik meg, amikor a "Azure Cosmos DB" kifejezésre keres rá, adja hozzá a bejegyzéseket a következő képen látható módon: 

   :::image type="content" source="./media/create-cluster-cli/apply-permissions.png" alt-text="Engedélyek alkalmazása" lightbox="./media/create-cluster-cli/apply-permissions.png" border="true":::

> [!NOTE] 
> A Azure Cosmos DB szerepkör-hozzárendelés csak telepítési célokra szolgál. Az Apache Cassandra által felügyelt Azure-példányok nem rendelkeznek háttérbeli függőségekkel Azure Cosmos DB.   

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha nem kívánja tovább használni ezt a felügyelt példány-fürtöt, törölje a következő lépésekkel:

1. Azure Portal bal oldali menüjében válassza az **erőforráscsoportok** lehetőséget.
1. A listából válassza ki az ehhez a rövid útmutatóhoz létrehozott erőforráscsoportot.
1. Az erőforráscsoport **Áttekintés** paneljén válassza az **erőforráscsoport törlése** elemet.
1. A következő ablakban adja meg a törölni kívánt erőforráscsoport nevét, majd válassza a **Törlés** lehetőséget.

## <a name="next-steps"></a>Következő lépések

Ebből a rövid útmutatóból megtudhatta, hogyan hozhat létre Azure felügyelt példányt az Apache Cassandra-fürtökhöz Azure Portal használatával. Most már megkezdheti a fürttel való munkát:

> [!div class="nextstepaction"]
> [Felügyelt Apache Spark-fürt üzembe helyezése Azure Databricks](deploy-cluster-databricks.md)
