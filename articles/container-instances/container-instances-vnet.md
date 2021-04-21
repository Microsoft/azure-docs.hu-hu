---
title: Tárolócsoport üzembe helyezése azure-beli virtuális hálózaton
description: Megtudhatja, hogyan helyezhet üzembe egy tárolócsoportot egy új vagy meglévő Azure-beli virtuális hálózaton az Azure parancssori felületével.
ms.topic: article
ms.date: 07/02/2020
ms.custom: devx-track-js, devx-track-azurecli
ms.openlocfilehash: 44be66957aa745179ffe4cd00db75f1d47237dfc
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107771046"
---
# <a name="deploy-container-instances-into-an-azure-virtual-network"></a>Tárolópéldányok üzembe helyezése egy Azure virtuális hálózaton

[Az Azure Virtual Network](../virtual-network/virtual-networks-overview.md) biztonságos magánhálózatot biztosít az Azure-beli és a helyszíni erőforrások számára. Ha tárolócsoportokat helyez üzembe egy Azure-beli virtuális hálózatban, a tárolók biztonságosan kommunikálhatnak a virtuális hálózat más erőforrásaival.

Ez a cikk bemutatja, hogyan helyezhet üzembe tárolócsoportokat egy új virtuális hálózaton vagy egy meglévő virtuális hálózaton az Azure CLI [az container create][az-container-create] parancsával. 

Hálózati forgatókönyvek és korlátozások: Virtuális hálózati forgatókönyvek és erőforrások a [Azure Container Instances.](container-instances-virtual-network-concepts.md)

> [!IMPORTANT]
> A tárolócsoport virtuális hálózaton való üzembe helyezése általánosan elérhető a Linux-tárolókhoz, ahol a legtöbb Azure Container Instances elérhető. Részletekért lásd: [Régiók és erőforrások rendelkezésre állása.][container-regions] 

A cikk példái a Bash-rendszerhéjhoz vannak formázva. Ha egy másik rendszerhéjat, például a PowerShellt vagy a parancssort részesíti előnyben, ennek megfelelően módosítsa a sor folytatási karaktereit.


## <a name="deploy-to-new-virtual-network"></a>Üzembe helyezés új virtuális hálózaton

Ha új virtuális hálózatban kell üzembe helyeznie, és az Azure-ral automatikusan létre kell hoznia a hálózati erőforrásokat, adja meg a következőket az [az container create végrehajtásakor:][az-container-create]

* Virtuális hálózat neve
* Virtuális hálózat címelőtagja CIDR formátumban
* Alhálózat neve
* Alhálózati címelőtag CIDR formátumban

A virtuális hálózat és az alhálózat címelőtagja adja meg a virtuális hálózat és az alhálózat címterét. Ezek az értékek az Útválasztás (CIDR) Inter-Domain, például: `10.0.0.0/16` . További információ az alhálózatok munkavégzésével kapcsolatban: Virtuális hálózat alhálózatának [hozzáadása, módosítása vagy törlése.](../virtual-network/virtual-network-manage-subnet.md)

Miután üzembe helyezett egy első tárolócsoportot ezzel a módszerrel, ugyanazon az alhálózaton is üzembe helyezheti a virtuális hálózat és az alhálózat nevének vagy az Azure által automatikusan létrehozott hálózati profilnak a megadásával. Mivel az Azure delegálta az alhálózatot  a Azure Container Instances, csak tárolócsoportokat helyezhet üzembe az alhálózaton.

### <a name="example"></a>Példa

Az alábbi [az container create parancs][az-container-create] egy új virtuális hálózat és alhálózat beállításait határozza meg. Adja meg annak az erőforráscsoportnak a nevét, amely egy olyan régióban lett létrehozva, amelyben a tárolócsoportok virtuális hálózatban való üzembe [helyezése](container-instances-region-availability.md)elérhető. Ez a parancs üzembe helyez egy nyilvános Microsoft [aci-helloworld][aci-helloworld] tárolót, amely egy Node.js webkiszolgálót futtat, amely egy statikus weboldalt szolgál ki. A következő szakaszban üzembe helyez egy második tárolócsoportot ugyanazon az alhálózaton, és teszteli a két tárolópéldány közötti kommunikációt.

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

Ha ezzel a módszerrel helyez üzembe egy új virtuális hálózatot, az üzembe helyezés eltarthat néhány percig, amíg a hálózati erőforrások létrejönnek. A kezdeti üzembe helyezést követően a tárolócsoport ugyanazon az alhálózaton való üzembe helyezése gyorsabban befejeződik.

## <a name="deploy-to-existing-virtual-network"></a>Üzembe helyezés meglévő virtuális hálózaton

Tárolócsoport üzembe helyezése meglévő virtuális hálózaton:

1. Hozzon létre egy alhálózatot a meglévő virtuális hálózaton belül, használjon egy meglévő alhálózatot, amelyben már üzembe van helyezni egy tárolócsoportot, vagy használjon egy meglévő, az összes többi erőforrásból *kiürített* alhálózatot
1. Telepítsen egy tárolócsoportot [az az container create][az-container-create] segítségével, és adja meg a következők egyikét:
   * Virtuális hálózat és alhálózat neve
   * A virtuális hálózat erőforrás-azonosítója és az alhálózati erőforrás-azonosító, amely lehetővé teszi egy másik erőforráscsoportból származó virtuális hálózat használatát
   * Hálózati profil neve vagy azonosítója, amelyet az [az network profile list][az-network-profile-list] használatával szerezhet be

### <a name="example"></a>Példa

Az alábbi példa üzembe helyez egy második tárolócsoportot a korábban létrehozott alhálózaton, és ellenőrzi a két tárolópéldány közötti kommunikációt.

Először szerezze be az első üzembe helyezett tárolócsoport, az *appcontainer IP-címét:*

```azurecli
az container show --resource-group myResourceGroup \
  --name appcontainer \
  --query ipAddress.ip --output tsv
```

A kimenet a privát alhálózaton lévő tárolócsoport IP-címét jeleníti meg. Például:

```console
10.0.0.4
```

Most állítsa be az parancsot a `CONTAINER_GROUP_IP` lekért IP-címre, `az container show` és hajtsa végre a következő `az container create` parancsot. A második, *commchecker* tároló egy Alpine Linux-alapú rendszerképet futtat, és az első tárolócsoport magánhálózati `wget` IP-címére hajt végre végrehajtást.

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

Miután a második tároló üzembe helyezése befejeződött, le kell húznia a naplóit, hogy lássa a végrehajtott parancs `wget` kimenetét:

```azurecli
az container logs --resource-group myResourceGroup --name commchecker
```

Ha a második tároló sikeresen kommunikált az elsővel, a kimenet a következőre hasonlít:

```console
Connecting to 10.0.0.4 (10.0.0.4:80)
index.html           100% |*******************************|  1663   0:00:00 ETA
```

A napló kimenetének azt kell mutatnia, hogy az képes volt csatlakozni az indexfájlhoz, és le tudta tölteni az első tárolóból a helyi alhálózat magánhálózati `wget` IP-címével. A két tárolócsoport közötti hálózati forgalom a virtuális hálózaton belül maradt.

### <a name="example---yaml"></a>Példa – YAML

Tárolócsoportot YAML-fájl, [Resource Manager-sablon](https://github.com/Azure/azure-quickstart-templates/tree/master/101-aci-vnet
)vagy más programozott módszer, például a Python SDK használatával is üzembe helyezhet egy meglévő virtuális hálózaton. 

YAML-fájl használata esetén például üzembe helyezhet egy virtuális hálózatot egy alhálózattal, amely a Azure Container Instances. Adja meg a következő tulajdonságokat:

* `ipAddress`: A tárolócsoport magánhálózati IP-címének beállításai.
  * `ports`: A megnyílik portok, ha van ilyen.
  * `protocol`: A megnyitott port protokollja (TCP vagy UDP).
* `networkProfile`: A virtuális hálózat és az alhálózat hálózati beállításai.
  * `id`: a Resource Manager erőforrás-azonosítója. `networkProfile`

A hálózati profil azonosítójának leához futtassa az [az network profile list][az-network-profile-list] parancsot, és adja meg a virtuális hálózatot és a delegált alhálózatot tartalmazó erőforráscsoport nevét.

``` azurecli
az network profile list --resource-group myResourceGroup \
  --query [0].id --output tsv
```

Példa a kimenetre:

```console
/subscriptions/<Subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkProfiles/aci-network-profile-aci-vnet-aci-subnet
```

Ha már megvan a hálózati profil azonosítója, másolja a következő YAML-t egy új fájlba *vnet-deploy-aci.yaml névvel.* A alatt cserélje le az értéket az előbb `networkProfile` lekért azonosítóra, majd mentse a `id` fájlt. Ez a YAML létrehoz egy *appcontaineryaml* nevű tárolócsoportot a virtuális hálózatban.

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

Telepítse a tárolócsoportot [az az container create paranccsal,][az-container-create] és adja meg a YAML-fájl nevét a `--file` paraméterhez:

```azurecli
az container create --resource-group myResourceGroup \
  --file vnet-deploy-aci.yaml
```

Az üzembe helyezés befejezése után futtassa [az az container show][az-container-show] parancsot az állapotának megjelenítéséhez. Példa a kimenetre:

```console
Name              ResourceGroup    Status    Image                                       IP:ports     Network    CPU/Memory       OsType    Location
----------------  ---------------  --------  ------------------------------------------  -----------  ---------  ---------------  --------  ----------
appcontaineryaml  myResourceGroup  Running   mcr.microsoft.com/azuredocs/aci-helloworld  10.0.0.5:80  Private    1.0 core/1.5 gb  Linux     westus
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

### <a name="delete-container-instances"></a>Tárolópéldányok törlése

Ha végzett a létrehozott tárolópéldányokkal, törölje őket a következő parancsokkal:

```azurecli
az container delete --resource-group myResourceGroup --name appcontainer -y
az container delete --resource-group myResourceGroup --name commchecker -y
az container delete --resource-group myResourceGroup --name appcontaineryaml -y
```

### <a name="delete-network-resources"></a>Hálózati erőforrások törlése

Ez a funkció jelenleg több további parancsot igényel a korábban létrehozott hálózati erőforrások törléséhez. Ha a cikk korábbi szakaszaiban példaparancsokat használt a virtuális hálózat és az alhálózat létrehozásához, akkor a következő szkripttel törölheti ezeket a hálózati erőforrásokat. A szkript feltételezi, hogy az erőforráscsoport egyetlen virtuális hálózatot tartalmaz, egyetlen hálózati profillal.

A szkript végrehajtása előtt állítsa a változót a törölni szükséges virtuális hálózatot és alhálózatot tartalmazó erőforráscsoport `RES_GROUP` nevére. Frissítse a virtuális hálózat nevét, ha nem a korábban javasolt `aci-vnet` nevet használja. A szkript a Bash-rendszerhéjhoz van formázva. Ha egy másik rendszerhéjat, például a PowerShellt vagy a parancssort részesíti előnyben, ennek megfelelően kell módosítania a változó-hozzárendelést és a hozzáférési okat.

> [!WARNING]
> Ez a szkript törli az erőforrásokat! Törli a virtuális hálózatot és az összes benne lévő alhálózatot. A szkript futtatása előtt  győződjön meg arról, hogy már nincs szüksége a virtuális hálózatban lévő erőforrásokra, beleértve a benne található alhálózatokat is. A törlés után **ezek az erőforrások nem állíthatók vissza.**

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

Új virtuális hálózat, alhálózat, hálózati profil és tárolócsoport Resource Manager sablonnal való üzembe helyezéséhez lásd: Azure-tárolócsoport létrehozása [virtuális hálózattal.](https://github.com/Azure/azure-quickstart-templates/tree/master/101-aci-vnet
)

<!-- IMAGES -->
[aci-vnet-01]: ./media/container-instances-vnet/aci-vnet-01.png

<!-- LINKS - External -->
[aci-helloworld]: https://hub.docker.com/_/microsoft-azuredocs-aci-helloworld

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container#az_container_create
[az-container-show]: /cli/azure/container#az_container_show
[az-network-vnet-create]: /cli/azure/network/vnet#az_network_vnet_create
[az-network-profile-list]: /cli/azure/network/profile#az_network_profile_list
[container-regions]: container-instances-region-availability.md
