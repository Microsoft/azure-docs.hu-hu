---
title: Rövid útmutató – Felügyelt Azure-példány létrehozása Apache Cassandra-fürthöz a Azure Portal
description: Ez a rövid útmutató bemutatja, hogyan hozhat létre felügyelt Azure-példányt az Apache Cassandra-fürthöz a Azure Portal.
author: TheovanKraay
ms.author: thvankra
ms.service: managed-instance-apache-cassandra
ms.topic: quickstart
ms.date: 03/02/2021
ms.custom: references_regions, devx-track-azurecli
ms.openlocfilehash: e42f85bb79dcb1bfe14cacbbfda3576888b841c9
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2021
ms.locfileid: "107481328"
---
# <a name="quickstart-create-an-azure-managed-instance-for-apache-cassandra-cluster-from-the-azure-portal-preview"></a>Rövid útmutató: Felügyelt Azure-példány létrehozása Apache Cassandra-fürthöz a Azure Portal (előzetes verzió)
 
Az Apache Cassandrára használható Felügyelt Azure-példány automatizált üzembe helyezési és méretezési műveleteket biztosít a felügyelt nyílt forráskódú Apache Cassandra-adatközpontokhoz, felgyorsítja a hibrid forgatókönyveket, és csökkenti a folyamatos karbantartást.

> [!IMPORTANT]
> Az Azure Managed Instance for Apache Cassandra jelenleg nyilvános előzetes verzióban érhető el.
> Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik.
> További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Ez a rövid útmutató bemutatja, hogyan hozhat létre felügyelt Azure Azure Portal példányt az Apache Cassandra-fürthöz a Azure Portal használatával.

## <a name="prerequisites"></a>Előfeltételek

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a name="create-a-managed-instance-cluster"></a><a id="create-account"></a>Felügyeltpéldány-fürt létrehozása

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

1. A keresősávban keressen rá az **Apache Cassandra felügyelt** példánya kifejezésre, és válassza ki az eredményt.

   :::image type="content" source="./media/create-cluster-portal/search-portal.png" alt-text="Keresse meg a felügyelt példányt az Apache Cassandrára." lightbox="./media/create-cluster-portal/search-portal.png" border="true":::

1. Válassza **a Felügyelt példány létrehozása az Apache Cassandra-fürthöz gombot.**

   :::image type="content" source="./media/create-cluster-portal/create-cluster.png" alt-text="A fürt létrehozása." lightbox="./media/create-cluster-portal/create-cluster.png" border="true":::

1. A **Felügyelt példány létrehozása az Apache Cassandrában panelen** adja meg a következő adatokat:

   * **Előfizetés** – Válassza ki Azure-előfizetését a legördülő menüből.
   * **Erőforráscsoport**– Adja meg, hogy új erőforráscsoportot szeretne létrehozni, vagy egy meglévőt szeretne használni. Az erőforráscsoport olyan tároló, amely egy adott Azure-megoldás kapcsolódó erőforrásait tartalmazza. További információt az [Azure-erőforráscsoport áttekintését ismertető](../azure-resource-manager/management/overview.md) cikkben talál.
   * **Fürt neve** – Adja meg a fürt nevét.
   * **Hely** – A fürt üzembe helyezésének helye.
   * **Termékváltozat** – A fürt termékváltozatának típusa.
   * **Nem. csomópontok száma**– A fürtben lévő csomópontok száma. Ezek a csomópontok az adatok replikáiként viselkednek.
   * **Kezdeti Cassandra-rendszergazdai jelszó** – A fürt létrehozásához használt jelszó.
   * **Erősítse meg a Cassandra rendszergazdai jelszavát** – A jelszó újra megadása.

    > [!NOTE]
    > A nyilvános előzetes verzióban létrehozhatja a felügyeltpéldány-fürtöt az USA keleti régiójában, az USA nyugati régiójában, az USA 2. keleti régiójában, az *USA 2.* nyugati régiójában, az USA középső régiójában, az USA déli középső régiójában, Észak-Európában Kelet-Ázsia, Nyugat-Európában, az USA déli régiójában és Kelet-Ausztráliában.

   :::image type="content" source="./media/create-cluster-portal/create-cluster-page.png" alt-text="Töltse ki a fürt létrehozása űrlapot." lightbox="./media/create-cluster-portal/create-cluster-page.png" border="true":::

1. Ezután válassza **a Hálózat lapot.**

1. A Hálózat **panelen** válassza  ki a Virtual Network **alhálózatot.** Kiválaszthat egy meglévő Virtual Network, vagy létrehozhat egy újat.

   :::image type="content" source="./media/create-cluster-portal/networking.png" alt-text="Konfigurálja a hálózat részleteit." lightbox="./media/create-cluster-portal/networking.png" border="true":::

    > [!NOTE]
    > Az Apache Cassandrához használható felügyelt Azure-példány üzembe helyezéséhez internet-hozzáférés szükséges. Az üzembe helyezés meghiúsul olyan környezetekben, ahol az internet-hozzáférés korlátozott. Győződjön meg arról, hogy nem blokkolja a virtuális hálózatban a következő alapvető Fontosságú Azure-szolgáltatásokhoz való hozzáférést, amelyek a felügyelt Cassandra megfelelő működését biztosítják:
    > - Azure Storage
    > - Azure KeyVault
    > - Azure Virtual Machine Scale Sets
    > - Azure Monitoring
    > - Azure Active Directory
    > - Azure Security

1. Ha az előző lépésben létrehozott egy új VNetet, ugorjon a 8. lépésre. Ha egy meglévő VNetet választott ki, a fürt létrehozása előtt speciális engedélyeket kell alkalmaznia az Virtual Network alhálózatra. Ezt az paranccsal, a , és a megfelelő `az role assignment create` `<subscription ID>` `<resource group name>` `<VNet name>` értékekre cserélve használhatja:

   ```azurecli-interactive
   az role assignment create --assignee a232010e-820c-4083-83bb-3ace5fc29d0b --role 4d97b98b-1d4f-4787-a291-c67834d212e7 --scope /subscriptions/<subscription ID>/resourceGroups/<resource group name>/providers/Microsoft.Network/virtualNetworks/<VNet name>
   ```

   > [!NOTE]
   > Az előző parancs és értékei rögzített értékek, ezeket az értékeket pontosan a `assignee` `role` parancsban említettek szerint adja meg. Ha ezt nem teszi meg, az hibákhoz vezet a fürt létrehozásakor. Ha hibába ütközik a parancs végrehajtásakor, előfordulhat, hogy nem rendelkezik a futtatáshoz szükséges engedélyekkel, forduljon a rendszergazdához az engedélyekért.

1. Most, hogy végzett a hálózattal, kattintson az **Áttekintés + létrehozás**  >  **gombra.**

    > [!NOTE]
    > A fürt létrehozása akár 15 percet is igénybe vehet.

   :::image type="content" source="./media/create-cluster-portal/review-create.png" alt-text="Tekintse át az összefoglalást a fürt létrehozásához." lightbox="./media/create-cluster-portal/review-create.png" border="true":::


1. Az üzembe helyezés befejezése után ellenőrizze az erőforráscsoportban az újonnan létrehozott felügyeltpéldány-fürtöt:

   :::image type="content" source="./media/create-cluster-portal/managed-instance.png" alt-text="A fürt létrehozása után az Áttekintés lap." lightbox="./media/create-cluster-portal/managed-instance.png" border="true":::

1. A fürtcsomópontok tallózáshoz lépjen a fürt létrehozásához használt Virtual Network panelre, és nyissa meg az **Áttekintés** panelt a megtekintésükhöz:

   :::image type="content" source="./media/create-cluster-portal/resources.png" alt-text="Tekintse meg a fürt erőforrásait." lightbox="./media/create-cluster-portal/resources.png" border="true":::


## <a name="connecting-to-your-cluster"></a>Csatlakozás a fürthöz

Az Apache Cassandrára készült Felügyelt Azure-példány nem hoz létre nyilvános IP-címekkel csomópontokat, ezért az újonnan létrehozott Cassandra-fürthöz való csatlakozáshoz létre kell hoznia egy másik erőforrást a virtuális hálózaton belül. Ez lehet egy alkalmazás, vagy egy virtuális gép, amely az Apache nyílt forráskódú [CQLSH](https://cassandra.apache.org/doc/latest/tools/cqlsh.html) lekérdezési eszközével van telepítve. Ubuntu-alapú [virtuális gép üzembe](https://azure.microsoft.com/resources/templates/101-vm-simple-linux/) helyezéséhez használhat sablont. Az üzembe helyezéskor SSH-val csatlakozzon a géphez, és telepítse a CQLSH-t az alábbi parancsokkal:

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

Ha hibába ütközik az Virtual Network-engedélyek alkalmazásakor, például Nem található felhasználó vagy szolgáltatásnév a gráfadatbázisban az *"e5007d2c-4b13-4a74-9b6a-605d99f03501"* gráfadatbázisban, ugyanezt az engedélyt manuálisan is alkalmazhatja a Azure Portal. Az engedélyek portálról való alkalmazásához válassza a meglévő virtuális hálózat Hozzáférés-vezérlés **(IAM)** panelét, és adjon hozzá egy "Azure Cosmos DB" szerepkör-hozzárendelést a "Hálózati rendszergazda" szerepkörhöz. Ha két bejegyzés jelenik meg a "Azure Cosmos DB" keresésekor, adja hozzá mindkét bejegyzést az alábbi képen látható módon: 

   :::image type="content" source="./media/create-cluster-cli/apply-permissions.png" alt-text="Engedélyek alkalmazása" lightbox="./media/create-cluster-cli/apply-permissions.png" border="true":::

> [!NOTE] 
> A Azure Cosmos DB szerepkör-hozzárendelés csak telepítési célokra szolgál. Az Apache Cassandrára felügyelt Azure Managed Instanced nem rendelkezik háttérbeli függőségekkel a Azure Cosmos DB.   

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha nem folytatja a felügyeltpéldány-fürt használatát, törölje a következő lépésekkel:

1. A bal oldali menüben válassza Azure Portal **Erőforráscsoportok lehetőséget.**
1. A listából válassza ki a rövid útmutatóhoz létrehozott erőforráscsoportot.
1. Az erőforráscsoport Áttekintés **panelen** válassza az **Erőforráscsoport törlése lehetőséget.**
1. A következő ablakban adja meg a törölni kívánt erőforráscsoport nevét, majd válassza a **Törlés lehetőséget.**

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban megtanulta, hogyan hozhat létre felügyelt Azure-példányt az Apache Cassandra-fürthöz a Azure Portal. Most már elkezdhet dolgozni a fürtön:

> [!div class="nextstepaction"]
> [Felügyelt fürt Apache Spark üzembe helyezése Azure Databricks](deploy-cluster-databricks.md)
