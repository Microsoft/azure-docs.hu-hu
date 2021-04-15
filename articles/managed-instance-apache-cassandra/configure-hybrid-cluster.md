---
title: Rövid útmutató – Hibrid fürt konfigurálása felügyelt Azure-példányokkal az Apache Cassandrában
description: Ez a rövid útmutató bemutatja, hogyan konfigurálható egy hibrid fürt az Apache Cassandrára vonatkozó felügyelt Azure-példányokkal.
author: TheovanKraay
ms.author: thvankra
ms.service: managed-instance-apache-cassandra
ms.topic: quickstart
ms.date: 03/02/2021
ms.openlocfilehash: 9f3ad2a5d5b275ff611653855eff73bd36afda9f
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2021
ms.locfileid: "107379417"
---
# <a name="quickstart-configure-a-hybrid-cluster-with-azure-managed-instance-for-apache-cassandra-preview"></a>Rövid útmutató: Hibrid fürt konfigurálása felügyelt Azure-példányokkal az Apache Cassandrában (előzetes verzió)

Az Apache Cassandrára használható Felügyelt Azure-példány automatizált üzembe helyezési és méretezési műveleteket biztosít a felügyelt nyílt forráskódú Apache Cassandra-adatközpontokhoz. Ez a szolgáltatás segít felgyorsítani a hibrid forgatókönyveket, és csökkenteni a folyamatos karbantartást.

> [!IMPORTANT]
> Az Azure Managed Instance for Apache Cassandra jelenleg nyilvános előzetes verzióban érhető el.
> Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik.
> További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Ez a rövid útmutató azt mutatja be, hogyan használhatók az Azure CLI-parancsok egy hibrid fürt konfigurálására. Ha meglévő adatközpontjai egy saját vagy egy adott környezetben vannak, az Apache Cassandrára használt Azure Managed Instance használatával további adatközpontokat adhat hozzá a fürthöz, és tarthatja karban azokat.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

* Ehhez a cikkhez az Azure CLI 2.12.1-es vagy újabb verziójára van szükség. Ha az alkalmazást Azure Cloud Shell, a legújabb verzió már telepítve van.

* [Az Azure Virtual Network](../virtual-network/virtual-networks-overview.md) kapcsolatot biztosít a saját vagy a saját környezetéhez. A helyszíni környezetek Azure-hoz való csatlakoztatásával kapcsolatos további információkért tekintse meg a Helyszíni hálózat csatlakoztatása az [Azure-hoz cikket.](/azure/architecture/reference-architectures/hybrid-networking/)

## <a name="configure-a-hybrid-cluster"></a><a id="create-account"></a>Hibrid fürt konfigurálása

1. Jelentkezzen be a [Azure Portal,](https://portal.azure.com/) és lépjen a Virtual Network erőforráshoz.

1. Nyissa meg **az Alhálózatok lapot,** és hozzon létre egy új alhálózatot. Az Alhálózat hozzáadása űrlap  mezőivel kapcsolatos további információkért tekintse meg a Virtual Network [cikket:](../virtual-network/virtual-network-manage-subnet.md#add-a-subnet)

   :::image type="content" source="./media/configure-hybrid-cluster/subnet.png" alt-text="Adjon hozzá egy új alhálózatot a Virtual Network." lightbox="./media/configure-hybrid-cluster/subnet.png" border="true":::
    <!-- ![image](./media/configure-hybrid-cluster/subnet.png) -->

    > [!NOTE]
    > Az Apache Cassandrához használható felügyelt Azure-példány üzembe helyezéséhez internet-hozzáférés szükséges. Az üzembe helyezés meghiúsul olyan környezetekben, ahol az internet-hozzáférés korlátozott. Győződjön meg arról, hogy nem blokkolja a virtuális hálózatban a következő, a felügyelt Cassandra megfelelő működését nélkülözhetetlen Azure-szolgáltatásokhoz való hozzáférést:
    > - Azure Storage
    > - Azure KeyVault
    > - Azure Virtual Machine Scale Sets
    > - Azure Monitoring
    > - Azure Active Directory
    > - Azure Security

1. Most speciális engedélyeket alkalmazunk arra a virtuális hálózatra és alhálózatra, amelyre a Cassandra managed Instance-nek szüksége van az Azure CLI használatával. Használja a parancsot, és cserélje le a `az role assignment create` `<subscription ID>` , és `<resource group name>` `<VNet name>` értékeket a megfelelő értékekre:

   ```azurecli-interactive
   az role assignment create --assignee a232010e-820c-4083-83bb-3ace5fc29d0b --role 4d97b98b-1d4f-4787-a291-c67834d212e7 --scope /subscriptions/<subscription ID>/resourceGroups/<resource group name>/providers/Microsoft.Network/virtualNetworks/<VNet name>
   ```

   > [!NOTE]
   > Az előző parancs és értékei rögzített szolgáltatásmértékek, illetve `assignee` `role` szerepkör-azonosítók.

1. A következő lépés az erőforrások konfigurálása a hibrid fürthöz. Mivel már rendelkezik fürtöt, a fürt neve itt csak a meglévő fürt nevét azonosító logikai erőforrás lesz. Ügyeljen arra, hogy a meglévő fürt nevét használja a következő szkriptben a `clusterName` és `clusterNameOverride` változók definiálása során. Szüksége lesz a magcsomópontokra, a nyilvános ügyféltanúsítványokra (ha nyilvános/titkos kulcsot konfigurált a Cassandra-végponton), valamint a meglévő fürt tanúsítványaira is.

   > [!NOTE]
   > Az alább megadott változó értéke pontosan megegyezik a fenti parancsban megadott `delegatedManagementSubnetId` `--scope` értékkel:

   ```azurecli-interactive
   resourceGroupName='MyResourceGroup'
   clusterName='cassandra-hybrid-cluster-legal-name'
   clusterNameOverride='cassandra-hybrid-cluster-illegal-name'
   location='eastus2'
   delegatedManagementSubnetId='/subscriptions/<subscription ID>/resourceGroups/<resource group name>/providers/Microsoft.Network/virtualNetworks/<VNet name>/subnets/<subnet name>'
    
   # You can override the cluster name if the original name is not legal for an Azure resource:
   # overrideClusterName='ClusterNameIllegalForAzureResource'
   # the default cassandra version will be v3.11
    
   az managed-cassandra cluster create \
      --cluster-name $clusterName \
      --resource-group $resourceGroupName \
      --location $location \
      --delegated-management-subnet-id $delegatedManagementSubnetId \
      --external-seed-nodes 10.52.221.2,10.52.221.3,10.52.221.4
      --client-certificates 'BEGIN CERTIFICATE-----\n...PEM format..\n-----END CERTIFICATE-----','BEGIN CERTIFICATE-----\n...PEM format...\n-----END CERTIFICATE-----' \
      --external-gossip-certificates 'BEGIN CERTIFICATE-----\n...PEM format 1...\n-----END CERTIFICATE-----','BEGIN CERTIFICATE-----\n...PEM format 2...\n-----END CERTIFICATE-----'
   ```

    > [!NOTE]
    > Tudnia kell, hol vannak a meglévő nyilvános és/vagy tanúsítványtanúsítványok. Ha nem biztos abban, hogy a parancs futtatásával `keytool -list -keystore <keystore-path> -rfc -storepass <password>` kinyomtathatja a tanúsítványokat. 

1. A fürterőforrás létrehozása után futtassa a következő parancsot a fürt telepítési részleteinek lekértéhez:

   ```azurecli-interactive
   resourceGroupName='MyResourceGroup'
   clusterName='cassandra-hybrid-cluster'
    
   az managed-cassandra cluster show \
       --cluster-name $clusterName \
       --resource-group $resourceGroupName \
   ```

1. Az előző parancs a felügyelt példány környezetével kapcsolatos információkat ad vissza. A tanúsítványtanúsítványokra azért van szükség, hogy telepítheti őket a meglévő adatközpont csomópontjaira. Az alábbi képernyőképen az előző parancs kimenete és a tanúsítványok formátuma látható:

   :::image type="content" source="./media/configure-hybrid-cluster/show-cluster.png" alt-text="Szerezze be a tanúsítvány adatait a fürtből." lightbox="./media/configure-hybrid-cluster/show-cluster.png" border="true":::
    <!-- ![image](./media/configure-hybrid-cluster/show-cluster.png) -->

1. Ezután hozzon létre egy új adatközpontot a hibrid fürtben. A változóértékeket cserélje le a fürt adataira:

   ```azurecli-interactive
   resourceGroupName='MyResourceGroup'
   clusterName='cassandra-hybrid-cluster'
   dataCenterName='dc1'
   dataCenterLocation='eastus2'
    
   az managed-cassandra datacenter create \
       --resource-group $resourceGroupName \
       --cluster-name $clusterName \
       --data-center-name $dataCenterName \
       --data-center-location $dataCenterLocation \
       --delegated-subnet-id $delegatedManagementSubnetId \
       --node-count 9 
   ```

1. Most, hogy létrehozta az új adatközpontot, futtassa az adatközpont megjelenítése parancsot a részletek megtekintéséhez:

   ```azurecli-interactive
   resourceGroupName='MyResourceGroup'
   clusterName='cassandra-hybrid-cluster'
   dataCenterName='dc1'
    
   az managed-cassandra datacenter show \
       --resource-group $resourceGroupName \
       --cluster-name $clusterName \
       --data-center-name $dataCenterName 
   ```

1. Az előző parancs kimenete az új adatközpont magcsomópontjai. Adja hozzá az új adatközpont magcsomópontját a meglévő adatközpont konfigurációhoz a *cassandra.yaml fájlban.* Telepítse a felügyelt példány korábban összegyűjtött tanúsítványait:

   :::image type="content" source="./media/configure-hybrid-cluster/show-datacenter.png" alt-text="Az adatközpont részleteinek lekérte." lightbox="./media/configure-hybrid-cluster/show-datacenter.png" border="true":::
    <!-- ![image](./media/configure-hybrid-cluster/show-datacenter.png) -->

    > [!NOTE]
    > Ha további adatközpontokat szeretne hozzáadni, megismételheti a fenti lépéseket, de csak a magcsomópontokra van szüksége. 

1. Végül használja a következő CQL-lekérdezést az egyes kulcsterek replikációs stratégiájának frissítéséhez, hogy az tartalmazza a fürt összes adatközpontját:

   ```bash
   ALTER KEYSPACE "ks" WITH REPLICATION = {'class': 'NetworkTopologyStrategy', ‘on-premise-dc': 3, ‘managed-instance-dc': 3};
   ```
   A jelszótáblákat is frissítenie kell:

   ```bash
    ALTER KEYSPACE "system_auth" WITH REPLICATION = {'class': 'NetworkTopologyStrategy', ‘on-premise-dc': 3, ‘managed-instance-dc': 3}
   ```

## <a name="troubleshooting"></a>Hibaelhárítás

Ha hibába ütközik az Virtual Network-engedélyek alkalmazásakor, például Nem található felhasználó vagy szolgáltatásnév a gráfadatbázisban az *"e5007d2c-4b13-4a74-9b6a-605d99f03501"* gráfadatbázisban, ugyanezt az engedélyt manuálisan is alkalmazhatja a Azure Portal. Az engedélyek portálról való alkalmazásához válassza a meglévő virtuális hálózat Hozzáférés-vezérlés **(IAM)** panelét, és adjon hozzá egy "Azure Cosmos DB" szerepkör-hozzárendelést a "Hálózati rendszergazda" szerepkörhöz. Ha két bejegyzés jelenik meg a "Azure Cosmos DB" keresésekor, adja hozzá mindkét bejegyzést az alábbi képen látható módon: 

   :::image type="content" source="./media/create-cluster-cli/apply-permissions.png" alt-text="Engedélyek alkalmazása" lightbox="./media/create-cluster-cli/apply-permissions.png" border="true":::

> [!NOTE] 
> A Azure Cosmos DB szerepkör-hozzárendelés csak telepítési célokra szolgál. Az Apache Cassandrára felügyelt Azure Managed Instanced nem rendelkezik háttérbeli függőségekkel a Azure Cosmos DB.  

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha nem folytatja a felügyeltpéldány-fürt használatát, törölje a következő lépésekkel:

1. A bal oldali menüben válassza Azure Portal **erőforráscsoportok lehetőséget.**
1. A listából válassza ki a rövid útmutatóhoz létrehozott erőforráscsoportot.
1. Az erőforráscsoport Áttekintés **panelen** válassza az **Erőforráscsoport törlése lehetőséget.**
3. A következő ablakban adja meg a törölni kívánt erőforráscsoport nevét, majd válassza a **Törlés lehetőséget.**

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban megtanulta, hogyan hozhat létre hibrid fürtöt az Azure CLI és az Apache Cassandrára használt Felügyelt Azure-példány használatával. Most már elkezdhet dolgozni a fürtön.

> [!div class="nextstepaction"]
> [Felügyelt Azure-példány kezelése Apache Cassandra-erőforrásokhoz az Azure CLI használatával](manage-resources-cli.md)
