---
title: Tároló csoport üzembe helyezése az Azure Virtual Networkben
description: Megtudhatja, hogyan helyezhet üzembe egy tároló csoportot egy új vagy meglévő Azure-beli virtuális hálózaton az Azure parancssori felületének használatával.
ms.topic: article
ms.date: 07/02/2020
ms.custom: devx-track-js, devx-track-azurecli
ms.openlocfilehash: b791d3f37809c2eca53f5a3cd34f7c44dd11ce40
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98028879"
---
# <a name="deploy-container-instances-into-an-azure-virtual-network"></a>Tárolópéldányok üzembe helyezése egy Azure virtuális hálózaton

Az [azure Virtual Network](../virtual-network/virtual-networks-overview.md) biztonságos, privát hálózatkezelést biztosít az Azure-hoz és a helyszíni erőforrásokhoz. Ha tároló-csoportokat telepít egy Azure-beli virtuális hálózatba, a tárolók biztonságosan kommunikálhatnak a virtuális hálózat többi erőforrásával.

Ez a cikk bemutatja, hogyan helyezhet üzembe tároló csoportokat az az [Container Create][az-container-create] paranccsal az Azure CLI-ben egy új virtuális hálózatra vagy egy meglévő virtuális hálózatra. 

Hálózati forgatókönyvek és korlátozások esetén lásd: [virtuális hálózati forgatókönyvek és erőforrások Azure Container Instanceshoz](container-instances-virtual-network-concepts.md).

> [!IMPORTANT]
> A tároló csoportok üzembe helyezése virtuális hálózatra általánosan elérhető Linux-tárolók esetén, ahol a Azure Container Instances elérhető. Részletekért lásd: [régiók és erőforrás rendelkezésre állása][container-regions]. 

A cikkben szereplő példák a bash-rendszerhéjra vannak formázva. Ha egy másik rendszerhéjt, például a PowerShellt vagy a parancssort részesíti előnyben, módosítsa a sor folytatási karaktereit ennek megfelelően.


## <a name="deploy-to-new-virtual-network"></a>Üzembe helyezés új virtuális hálózaton

Ha új virtuális hálózatra kíván üzembe helyezni, és az Azure-hoz automatikusan hozza létre a hálózati erőforrásokat, akkor a következőt kell megadnia az az [Container Create][az-container-create]:

* Virtuális hálózat neve
* CIDR formátumú virtuális hálózati címek előtagja
* Alhálózat neve
* Alhálózat CIDR formátuma

A virtuális hálózat és az alhálózati címek előtagjai határozzák meg a virtuális hálózat és az alhálózat címterület-területét. Ezek az értékek az osztály nélküli Inter-Domain Routing (CIDR) jelöléssel jelennek meg, például: `10.0.0.0/16` . További információ az alhálózatok használatáról: [virtuális hálózati alhálózat hozzáadása, módosítása vagy törlése](../virtual-network/virtual-network-manage-subnet.md).

Miután telepítette az első tároló csoportját ezzel a módszerrel, a virtuális hálózat és az alhálózatok nevének megadásával, vagy az Azure által automatikusan létrehozott hálózati profil megadásával ugyanarra az alhálózatra is telepíthető. Mivel az Azure delegálja az alhálózatot Azure Container Instancesre, *csak* a tároló csoportokat telepítheti az alhálózatra.

### <a name="example"></a>Példa

Az alábbi az [Container Create][az-container-create] parancs egy új virtuális hálózat és alhálózat beállításait adja meg. Adja meg egy olyan erőforráscsoport nevét, amely egy olyan régióban lett létrehozva, amelyben a tároló csoport központi telepítései [elérhetők](container-instances-region-availability.md)a virtuális hálózaton. Ez a parancs központilag telepíti a nyilvános Microsoft [ACI-HelloWorld][aci-helloworld] tárolót, amely egy statikus weblapot kiszolgáló kisméretű Node.js webkiszolgálót futtat. A következő szakaszban egy második tároló csoportot fog telepíteni ugyanahhoz az alhálózathoz, és tesztelni a két tároló példány közötti kommunikációt.

```azurecli
az container create \
  --name appcontainer \
  --resource-group myResourceGroup \
  --image mcr.microsoft.com/azuredocs/aci-helloworld \
  --vnet aci-vnet \
  --vnet-address-prefix 10.0.0.0/16 \
  --subnet aci-subnet \
  --subnet-address-prefix 10.0.0.0/24
```

Ha ezzel a módszerrel telepít egy új virtuális hálózatot, az üzembe helyezés eltarthat néhány percig, amíg a hálózati erőforrások létrejöttek. A kezdeti üzembe helyezést követően a további, egyazon alhálózatra történő központi telepítések gyorsabban megegyeznek.

## <a name="deploy-to-existing-virtual-network"></a>Üzembe helyezés meglévő virtuális hálózaton

Tároló csoport üzembe helyezése meglévő virtuális hálózaton:

1. Hozzon létre egy alhálózatot a meglévő virtuális hálózaton belül, használjon olyan meglévő alhálózatot, amelyben már telepítve van egy tároló csoport, vagy használjon egy meglévő alhálózatot, amely az *összes* többi erőforrást kiüríti
1. Helyezzen üzembe egy tároló csoportot az [az Container Create][az-container-create] paranccsal, és határozza meg a következők egyikét:
   * Virtuális hálózat neve és alhálózatának neve
   * Virtuális hálózati erőforrás-azonosító és alhálózati erőforrás-azonosító, amely lehetővé teszi, hogy egy másik erőforráscsoporthoz tartozó virtuális hálózatot használjon.
   * A hálózati profil neve vagy azonosítója, amelyet az [az Network profil List][az-network-profile-list] paranccsal szerezhet be

### <a name="example"></a>Példa

A következő példa egy második tároló-csoportot telepít ugyanarra az alhálózatra, amelyet korábban hozott létre, és ellenőrzi a két tároló példány közötti kommunikációt.

Először szerezze be az elsőként telepített Container-csoport IP-címét, a *appcontainer*:

```azurecli
az container show --resource-group myResourceGroup \
  --name appcontainer \
  --query ipAddress.ip --output tsv
```

A kimenet a saját alhálózat tároló csoportjának IP-címét jeleníti meg. Például:

```console
10.0.0.4
```

Most állítsa be a `CONTAINER_GROUP_IP` paranccsal lekért IP-címet `az container show` , és hajtsa végre a következő `az container create` parancsot. Ez a második tároló, a *commchecker* egy alpesi Linux-alapú rendszerképet futtat, és `wget` az első tároló csoport privát alhálózatának IP-címén hajtja végre.

```azurecli
CONTAINER_GROUP_IP=<container-group-IP-address>

az container create \
  --resource-group myResourceGroup \
  --name commchecker \
  --image alpine:3.5 \
  --command-line "wget $CONTAINER_GROUP_IP" \
  --restart-policy never \
  --vnet aci-vnet \
  --subnet aci-subnet
```

A második tároló üzembe helyezésének befejezése után húzza le a naplókat, hogy láthassa az `wget` általa végrehajtott parancs kimenetét:

```azurecli
az container logs --resource-group myResourceGroup --name commchecker
```

Ha a második tároló sikeresen kommunikált az elsővel, a kimenet a következőhöz hasonló:

```console
Connecting to 10.0.0.4 (10.0.0.4:80)
index.html           100% |*******************************|  1663   0:00:00 ETA
```

A napló kimenetének azt kell megjelennie, hogy `wget` képes volt csatlakozni a fájlhoz, és letölti az indexfájl az első tárolóból a saját magánhálózati IP-címének használatával a helyi alhálózaton. A két tároló csoport közötti hálózati forgalom a virtuális hálózaton belül marad.

### <a name="example---yaml"></a>Példa – YAML

YAML-fájl, [Resource Manager-sablon](https://github.com/Azure/azure-quickstart-templates/tree/master/101-aci-vnet
)vagy más programozott módszer, például a Python SDK használatával is üzembe helyezhet egy tároló csoportot egy meglévő virtuális hálózaton. 

Ha például egy YAML-fájlt használ, központilag telepítheti egy olyan virtuális hálózatra, amelyhez Azure Container Instances delegált alhálózat tartozik. A következő tulajdonságokat kell megadnia:

* `ipAddress`: A tároló csoport magánhálózati IP-címének beállításai.
  * `ports`: A megnyitni kívánt portok, ha vannak ilyenek.
  * `protocol`: A megnyitott port protokollja (TCP vagy UDP).
* `networkProfile`: A virtuális hálózat és az alhálózat hálózati beállításai.
  * `id`: A teljes erőforrás-kezelő erőforrás-azonosítója `networkProfile` .

A hálózati profil AZONOSÍTÓjának lekéréséhez futtassa az az [Network profil List][az-network-profile-list] parancsot, és adja meg az erőforráscsoport nevét, amely a virtuális hálózatot és a delegált alhálózatot tartalmazza.

``` azurecli
az network profile list --resource-group myResourceGroup \
  --query [0].id --output tsv
```

Példa a kimenetre:

```console
/subscriptions/<Subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkProfiles/aci-network-profile-aci-vnet-aci-subnet
```

A hálózati profil azonosítója után másolja a következő YAML egy új, *vnet-Deploy-ACI. YAML* nevű fájlba. A alatt `networkProfile` cserélje le az `id` imént beolvasott azonosítójú értéket, majd mentse a fájlt. Ez a YAML létrehoz egy *appcontaineryaml* nevű tároló csoportot a virtuális hálózatában.

```YAML
apiVersion: '2019-12-01'
location: westus
name: appcontaineryaml
properties:
  containers:
  - name: appcontaineryaml
    properties:
      image: mcr.microsoft.com/azuredocs/aci-helloworld
      ports:
      - port: 80
        protocol: TCP
      resources:
        requests:
          cpu: 1.0
          memoryInGB: 1.5
  ipAddress:
    type: Private
    ports:
    - protocol: tcp
      port: '80'
  networkProfile:
    id: /subscriptions/<Subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkProfiles/aci-network-profile-aci-vnet-subnet
  osType: Linux
  restartPolicy: Always
tags: null
type: Microsoft.ContainerInstance/containerGroups
```

Telepítse a tároló csoportot az az [Container Create][az-container-create] paranccsal, és adja meg a paraméter YAML-fájljának nevét `--file` :

```azurecli
az container create --resource-group myResourceGroup \
  --file vnet-deploy-aci.yaml
```

Miután az üzembe helyezés befejeződött, futtassa az az [Container show][az-container-show] parancsot az állapotának megjelenítéséhez. Példa a kimenetre:

```console
Name              ResourceGroup    Status    Image                                       IP:ports     Network    CPU/Memory       OsType    Location
----------------  ---------------  --------  ------------------------------------------  -----------  ---------  ---------------  --------  ----------
appcontaineryaml  myResourceGroup  Running   mcr.microsoft.com/azuredocs/aci-helloworld  10.0.0.5:80  Private    1.0 core/1.5 gb  Linux     westus
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

### <a name="delete-container-instances"></a>Tároló példányainak törlése

Ha elkészült a létrehozott tároló-példányokkal, törölje azokat a következő parancsokkal:

```azurecli
az container delete --resource-group myResourceGroup --name appcontainer -y
az container delete --resource-group myResourceGroup --name commchecker -y
az container delete --resource-group myResourceGroup --name appcontaineryaml -y
```

### <a name="delete-network-resources"></a>Hálózati erőforrások törlése

Ennek a funkciónak jelenleg több további parancsra van szüksége a korábban létrehozott hálózati erőforrások törléséhez. Ha a cikk előző részében szereplő, a virtuális hálózat és az alhálózat létrehozásához használt példás parancsokat használta, akkor a következő parancsfájllal törölheti a hálózati erőforrásokat. A parancsfájl feltételezi, hogy az erőforráscsoport egyetlen hálózati profillal rendelkező egyetlen virtuális hálózatot tartalmaz.

A parancsfájl végrehajtása előtt állítsa a `RES_GROUP` változót a törölni kívánt virtuális hálózatot és alhálózatot tartalmazó erőforráscsoport nevére. Frissítse a virtuális hálózat nevét, ha nem a `aci-vnet` korábban javasolt nevet használta. A parancsfájl a bash-rendszerhéjhoz van formázva. Ha inkább egy másik rendszerhéjt, például a PowerShellt vagy a parancssort részesíti előnyben, akkor ennek megfelelően módosítania kell a változó hozzárendelés és a hozzáférési jogosultságokat.

> [!WARNING]
> Ez a szkript törli az erőforrásokat! Törli a virtuális hálózatot és a benne található összes alhálózatot. Győződjön meg arról, hogy a parancsfájl futtatása előtt már nincs szüksége a virtuális hálózatban lévő *összes* erőforrásra, beleértve a benne található alhálózatokat is. A törlés után **ezek az erőforrások nem állíthatók helyre**.

```azurecli
# Replace <my-resource-group> with the name of your resource group
# Assumes one virtual network in resource group
RES_GROUP=<my-resource-group>

# Get network profile ID
# Assumes one profile in virtual network
NETWORK_PROFILE_ID=$(az network profile list --resource-group $RES_GROUP --query [0].id --output tsv)

# Delete the network profile
az network profile delete --id $NETWORK_PROFILE_ID -y

# Delete virtual network
az network vnet delete --resource-group $RES_GROUP --name aci-vnet
```

## <a name="next-steps"></a>Következő lépések

Ha új virtuális hálózatot, alhálózatot, hálózati profilt és tároló csoportot szeretne üzembe helyezni Resource Manager-sablonnal, tekintse meg [Az Azure Container Group létrehozása a VNet](https://github.com/Azure/azure-quickstart-templates/tree/master/101-aci-vnet
)használatával című témakört.

<!-- IMAGES -->
[aci-vnet-01]: ./media/container-instances-vnet/aci-vnet-01.png

<!-- LINKS - External -->
[aci-helloworld]: https://hub.docker.com/_/microsoft-azuredocs-aci-helloworld

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container#az-container-create
[az-container-show]: /cli/azure/container#az-container-show
[az-network-vnet-create]: /cli/azure/network/vnet#az-network-vnet-create
[az-network-profile-list]: /cli/azure/network/profile#az-network-profile-list
[container-regions]: container-instances-region-availability.md
