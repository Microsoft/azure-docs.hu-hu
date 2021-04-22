---
title: Felügyelt identitások használata a Azure Kubernetes Service
description: Megtudhatja, hogyan használhatja a felügyelt identitásokat Azure Kubernetes Service (AKS)
services: container-service
ms.topic: article
ms.date: 12/16/2020
ms.openlocfilehash: 7eb0ab6247e8afc27f938b8b4a25d1fb1ee723f4
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2021
ms.locfileid: "107876993"
---
# <a name="use-managed-identities-in-azure-kubernetes-service"></a>Felügyelt identitások használata a Azure Kubernetes Service

Jelenleg egy Azure Kubernetes Service (AKS-) fürtnek (pontosabban a Kubernetes felhőszolgáltatónak) identitásra van szüksége további erőforrások, például terheléselosztások és felügyelt lemezek létrehozásához az Azure-ban. Ez az identitás lehet felügyelt identitás *vagy* *szolgáltatásnév.* Ha egyszerű szolgáltatást [használ,](kubernetes-service-principal.md)meg kell adnia egyet, vagy az AKS létrehoz egyet az Ön nevében. Ha felügyelt identitást használ, azt az AKS automatikusan létre fogja hozni. A szolgáltatásnévvel működő fürtök végül olyan állapotba jutnak, amelyben meg kell újítani őket a fürt megfelelő állapotának eléréséhez. A szolgáltatásnév kezelése összetettebbé teszi a szolgáltatást, ezért egyszerűbb a felügyelt identitások használata. Ugyanezek az engedélykövetelmények vonatkoznak a szolgáltatásnévre és a felügyelt identitásra is.

*A felügyelt identitások* lényegében a szolgáltatásnév burkolói, és egyszerűbbé teszik a felügyeletüket. A MI hitelesítő adatainak rotációja 46 naponta automatikusan megtörténik az Azure Active Directory alapján. Az AKS rendszer által hozzárendelt és felhasználó által hozzárendelt felügyelt identitástípusokat is használ. Ezek az identitások jelenleg nem módosíthatók. További tudnivalókért olvassa el az [Azure-erőforrások felügyelt identitását.](../active-directory/managed-identities-azure-resources/overview.md)

## <a name="before-you-begin"></a>Előkészületek

A következő erőforrásnak kell telepítve lennie:

- Az Azure CLI 2.15.1-es vagy újabb verziója

## <a name="limitations"></a>Korlátozások

* A felügyelt identitást támogató fürtök bérlői áthelyezése/áttelepítése nem támogatott.
* Ha a fürt engedélyezve `aad-pod-identity` van, Node-Managed Identity- (NMI-) podok úgy módosítják a csomópontok iptable-it, hogy elfogják az Azure Instance Metadata végpontra hívásokat. Ez a konfiguráció azt jelenti, hogy az NMI elfogja a metaadat-végpontra vonatkozó kéréseket, még akkor is, ha a pod nem használja a `aad-pod-identity` et. Az AzurePodIdentityException CRD konfigurálható úgy, hogy tájékoztassa arról, hogy a CRD-ben meghatározott címkéknek megfelelő podból származó metaadat-végpontra vonatkozó kéréseket az NMI-ben történő feldolgozás nélkül kell `aad-pod-identity` proxitálni. A kube-system névtérben címkével jelölt rendszerpodokat ki kell zárni a fájlból az `kubernetes.azure.com/managedby: aks`  `aad-pod-identity` AzurePodIdentityException CRD konfigurálásával. További információ: [Az aad-pod-identity](https://azure.github.io/aad-pod-identity/docs/configure/application_exception)letiltása egy adott podhoz vagy alkalmazáshoz.
  Kivétel konfiguráláshoz telepítse a [mic-exception YAML-t.](https://github.com/Azure/aad-pod-identity/blob/master/deploy/infra/mic-exception.yaml)

## <a name="summary-of-managed-identities"></a>Felügyelt identitások összegzése

Az AKS számos felügyelt identitást használ a beépített szolgáltatásokhoz és bővítményekhez.

| Identitás                       | Name    | Használati eset | Alapértelmezett engedélyek | Saját identitásának bring (Saját identitás)
|----------------------------|-----------|----------|
| Vezérlősík | nem látható | Az AKS-vezérlősík összetevői használják a fürterőforrások kezelésére, beleértve a bejövő terheléselosztásokat és az AKS által felügyelt nyilvános IP-eket, valamint az automatikus fürtskála-műveleteket | Közreműködői szerepkör a Node-erőforráscsoporthoz | támogatott
| Kubelet | AKS-fürt neve-ügynökkészlet | Hitelesítés Azure Container Registry (ACR) használatával | NA (kubernetes v1.15+) | Egyelőre nem támogatott
| Összead | AzureNPM | Nincs szükség identitásra | NA | No
| Összead | AzureCNI hálózatfigyelés | Nincs szükség identitásra | NA | No
| Összead | azure-policy (forgalomirányító) | Nincs szükség identitásra | NA | No
| Összead | azure-policy | Nincs szükség identitásra | NA | No
| Összead | Calico | Nincs szükség identitásra | NA | No
| Összead | Irányítópult | Nincs szükség identitásra | NA | No
| Összead | HTTPApplicationRouting | A szükséges hálózati erőforrásokat kezeli | Olvasó szerepkör a csomópont-erőforráscsoporthoz, közreműködői szerepkör a DNS-zónához | No
| Összead | Bejövő forgalom alkalmazásátjáró | A szükséges hálózati erőforrásokat kezeli| Közreműködői szerepkör a csomópont-erőforráscsoporthoz | No
| Összead | omsagent | AKS-metrikák elküldését Azure Monitor | Monitorozási metrikák közzétevője szerepkör | No
| Összead | Virtual-Node (ACIConnector) | Az alkalmazáshoz (ACI Azure Container Instances szükséges hálózati erőforrásokat kezeli | Közreműködői szerepkör a csomópont-erőforráscsoporthoz | No
| OSS-projekt | aad-pod-identity | Lehetővé teszi, hogy az alkalmazások biztonságosan hozzáférjenek a felhőbeli erőforrásokhoz Azure Active Directory (AAD) | NA | Az engedély megadásának lépései a következőnél: https://github.com/Azure/aad-pod-identity#role-assignment .

## <a name="create-an-aks-cluster-with-managed-identities"></a>AKS-fürt létrehozása felügyelt identitásokkal

Most már létrehozhat egy AKS-fürtöt felügyelt identitásokkal az alábbi CLI-parancsokkal.

Először hozzon létre egy Azure-erőforráscsoportot:

```azurecli-interactive
# Create an Azure resource group
az group create --name myResourceGroup --location westus2
```

Ezután hozzon létre egy AKS-fürtöt:

```azurecli-interactive
az aks create -g myResourceGroup -n myManagedCluster --enable-managed-identity
```

A fürt létrehozása után üzembe helyezheti az alkalmazás számítási feladatait az új fürtön, és úgy használhatja azt, mint a szolgáltatásnév-alapú AKS-fürtök esetében.

Végül szerezze be a fürt eléréséhez szükséges hitelesítő adatokat:

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myManagedCluster
```

## <a name="update-an-aks-cluster-to-managed-identities-preview"></a>AKS-fürt frissítése felügyelt identitásokként (előzetes verzió)

Mostantól az alábbi CLI-parancsokkal frissítheti a szolgáltatásnévvel működő AKS-fürtöt a felügyelt identitásokkal való együttműködéshez.

Először regisztrálja a funkciójelölőt a rendszer által hozzárendelt identitáshoz:

```azurecli-interactive
az feature register --namespace Microsoft.ContainerService -n MigrateToMSIClusterPreview
```

Frissítse a rendszer által hozzárendelt identitást:

```azurecli-interactive
az aks update -g <RGName> -n <AKSName> --enable-managed-identity
```

Regisztrálja a funkciójelölőt a felhasználó által hozzárendelt identitáshoz:

```azurecli-interactive
az feature register --namespace Microsoft.ContainerService -n UserAssignedIdentityPreview
```

Frissítse a felhasználó által hozzárendelt identitást:

```azurecli-interactive
az aks update -g <RGName> -n <AKSName> --enable-managed-identity --assign-identity <UserAssignedIdentityResourceID> 
```
> [!NOTE]
> Miután a rendszer által hozzárendelt vagy felhasználó által hozzárendelt identitások felügyelt identitásra frissültek, hajtson végre egy műveletet a csomópontokon a felügyelt identitás `az aks nodepool upgrade --node-image-only` frissítésének befejezéséhez.

## <a name="obtain-and-use-the-system-assigned-managed-identity-for-your-aks-cluster"></a>A rendszer által hozzárendelt felügyelt identitás beszerzése és használata az AKS-fürthöz

Az alábbi CLI-paranccsal ellenőrizze, hogy az AKS-fürt felügyelt identitást használ-e:

```azurecli-interactive
az aks show -g <RGName> -n <ClusterName> --query "servicePrincipalProfile"
```

Ha a fürt felügyelt identitásokat használ, az `clientId` "msi" értéket fogja látni. A szolgáltatásnévvel használt fürtök ehelyett az objektumazonosítót fogják mutatni. Például: 

```output
{
  "clientId": "msi"
}
```

Miután ellenőrizte, hogy a fürt felügyelt identitásokat használ-e, a vezérlősík rendszer által hozzárendelt identitásának objektumazonosítóját a következő paranccsal találhatja meg:

```azurecli-interactive
az aks show -g <RGName> -n <ClusterName> --query "identity"
```

```output
{
    "principalId": "<object-id>",
    "tenantId": "<tenant-id>",
    "type": "SystemAssigned",
    "userAssignedIdentities": null
},
```

> [!NOTE]
> Saját virtuális hálózat, statikus IP-cím vagy olyan csatlakoztatott Azure-lemez létrehozásához és használatával, ahol az erőforrások a munkavégző csomópont erőforráscsoportján kívül esnek, használja a fürt Rendszer által hozzárendelt felügyelt identitás PrincipalID azonosítóját a szerepkör-hozzárendelés végrehajtásához. További információ a szerepkör-hozzárendelésről: [Más Azure-erőforrásokhoz való hozzáférés delegálása.](kubernetes-service-principal.md#delegate-access-to-other-azure-resources)
>
> Az Azure-felhőszolgáltató által használt fürt felügyelt identitásának engedélyeinek feltöltése akár 60 percet is igénybe vehet.


## <a name="bring-your-own-control-plane-mi"></a>Saját vezérlősík mi-hez
Az egyéni vezérlősík-identitás lehetővé teszi a meglévő identitáshoz való hozzáférést a fürt létrehozása előtt. Ez a funkció olyan forgatókönyveket tesz lehetővé, mint az egyéni VNET vagy az UDR outboundType tulajdonságának használata előre létrehozott felügyelt identitással.

Telepítve kell lennie az Azure CLI 2.15.1-es vagy újabb verziójának.

### <a name="limitations"></a>Korlátozások
* Azure Government jelenleg nem támogatottak.
* Azure China 21Vianet jelenleg nem támogatottak.

Ha még nincs felügyelt identitása, hozzon létre egyet például az [az identity CLI használatával.][az-identity-create]

```azurecli-interactive
az identity create --name myIdentity --resource-group myResourceGroup
```
Az eredménynek így kell kinéznie:

```output
{                                                                                                                                                                                 
  "clientId": "<client-id>",
  "clientSecretUrl": "<clientSecretUrl>",
  "id": "/subscriptions/<subscriptionid>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myIdentity", 
  "location": "westus2",
  "name": "myIdentity",
  "principalId": "<principalId>",
  "resourceGroup": "myResourceGroup",                       
  "tags": {},
  "tenantId": "<tenant-id>>",
  "type": "Microsoft.ManagedIdentity/userAssignedIdentities"
}
```

Ha a felügyelt identitás az előfizetés része, az [az identity CLI][az-identity-list] paranccsal lekérdezheti azt.  

```azurecli-interactive
az identity list --query "[].{Name:name, Id:id, Location:location}" -o table
```

Most már használhatja a következő parancsot a fürt meglévő identitással való létrehozásához:

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --name myManagedCluster \
    --network-plugin azure \
    --vnet-subnet-id <subnet-id> \
    --docker-bridge-address 172.17.0.1/16 \
    --dns-service-ip 10.2.0.10 \
    --service-cidr 10.2.0.0/24 \
    --enable-managed-identity \
    --assign-identity <identity-id> \
```

A saját felügyelt identitásait használó sikeres fürtlétrehozás tartalmazza a userAssignedIdentities profiladatokat:

```output
 "identity": {
   "principalId": null,
   "tenantId": null,
   "type": "UserAssigned",
   "userAssignedIdentities": {
     "/subscriptions/<subscriptionid>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myIdentity": {
       "clientId": "<client-id>",
       "principalId": "<principal-id>"
     }
   }
 },
```

## <a name="bring-your-own-kubelet-mi-preview"></a>Saját kubelet MI-k (előzetes verzió)

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

A Kubelet-identitás lehetővé teszi a meglévő identitáshoz való hozzáférést a fürt létrehozása előtt. Ez a funkció olyan forgatókönyveket tesz lehetővé, mint például az ACR-hez való csatlakozás egy előre létrehozott felügyelt identitással.

### <a name="prerequisites"></a>Előfeltételek

- Telepítve kell lennie az Azure CLI 2.21.1-es vagy újabb verziójának.
- Telepítve kell lennie az aks-preview 0.5.10-es vagy újabb verziójának.

### <a name="limitations"></a>Korlátozások

- Csak felügyelt fürtök User-Assigned működik.
- Azure Government jelenleg nem támogatott.
- Azure China 21Vianet jelenleg nem támogatott.

Először regisztrálja a Szolgáltatásjelölőt a Kubelet-identitáshoz:

```azurecli-interactive
az feature register --namespace Microsoft.ContainerService -n CustomKubeletIdentityPreview
```

Eltarthat néhány percig, hogy az állapot Regisztrált *állapotúra mutasson.* A regisztrációs állapotot az az feature list paranccsal [ellenőrizheti:][az-feature-list]

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/CustomKubeletIdentityPreview')].{Name:name,State:properties.state}"
```

Ha készen áll, frissítse a *Microsoft.ContainerService* erőforrás-szolgáltató regisztrációját az [az provider register paranccsal:][az-provider-register]

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

### <a name="create-or-obtain-managed-identities"></a>Felügyelt identitások létrehozása vagy beszerzése

Ha még nem áll a vezérlősík felügyelt identitása, hozzon létre egyet. Az alábbi példa az [az identity create parancsot][az-identity-create] használja:

```azurecli-interactive
az identity create --name myIdentity --resource-group myResourceGroup
```

Az eredménynek így kell kinéznie:

```output
{                                  
  "clientId": "<client-id>",
  "clientSecretUrl": "<clientSecretUrl>",
  "id": "/subscriptions/<subscriptionid>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myIdentity", 
  "location": "westus2",
  "name": "myIdentity",
  "principalId": "<principalId>",
  "resourceGroup": "myResourceGroup",                       
  "tags": {},
  "tenantId": "<tenant-id>",
  "type": "Microsoft.ManagedIdentity/userAssignedIdentities"
}
```

Ha még nincs kubelet felügyelt identitása, hozzon létre egyet. Az alábbi példa az [az identity create parancsot][az-identity-create] használja:

```azurecli-interactive
az identity create --name myKubeletIdentity --resource-group myResourceGroup
```

Az eredménynek így kell kinéznie:

```output
{
  "clientId": "<client-id>",
  "clientSecretUrl": "<clientSecretUrl>",
  "id": "/subscriptions/<subscriptionid>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myKubeletIdentity", 
  "location": "westus2",
  "name": "myKubeletIdentity",
  "principalId": "<principalId>",
  "resourceGroup": "myResourceGroup",                       
  "tags": {},
  "tenantId": "<tenant-id>",
  "type": "Microsoft.ManagedIdentity/userAssignedIdentities"
}
```

Ha a meglévő felügyelt identitás az előfizetés része, az [az identity list][az-identity-list] paranccsal lekérdezheti:

```azurecli-interactive
az identity list --query "[].{Name:name, Id:id, Location:location}" -o table
```

### <a name="create-a-cluster-using-kubelet-identity"></a>Fürt létrehozása kubelet-identitással

Most már használhatja a következő parancsot a fürt meglévő identitásokkal való létrehozásához. Adja meg a vezérlősík identitásazonosítóját a és a `assign-identity` kubelet felügyelt `assign-kublet-identity` identitásával:

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --name myManagedCluster \
    --network-plugin azure \
    --vnet-subnet-id <subnet-id> \
    --docker-bridge-address 172.17.0.1/16 \
    --dns-service-ip 10.2.0.10 \
    --service-cidr 10.2.0.0/24 \
    --enable-managed-identity \
    --assign-identity <identity-id> \
    --assign-kubelet-identity <kubelet-identity-id> \
```

A saját Kubelet felügyelt identitással való sikeres fürtlétrehozás az alábbi kimenetet tartalmazza:

```output
  "identity": {
    "principalId": null,
    "tenantId": null,
    "type": "UserAssigned",
    "userAssignedIdentities": {
      "/subscriptions/<subscriptionid>/resourcegroups/resourcegroups/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myIdentity": {
        "clientId": "<client-id>",
        "principalId": "<principal-id>"
      }
    }
  },
  "identityProfile": {
    "kubeletidentity": {
      "clientId": "<client-id>",
      "objectId": "<object-id>",
      "resourceId": "/subscriptions/<subscriptionid>/resourcegroups/resourcegroups/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myKubeletIdentity"
    }
  },
```

## <a name="next-steps"></a>Következő lépések
* Felügyelt [identitást Azure Resource Manager fürtök ][aks-arm-template] létrehozásához használjon Azure Resource Manager sablonokat.

<!-- LINKS - external -->
[aks-arm-template]: /azure/templates/microsoft.containerservice/managedclusters

<!-- LINKS - internal -->
[az-identity-create]: /cli/azure/identity#az_identity_create
[az-identity-list]: /cli/azure/identity#az_identity_list
[az-feature-list]: /cli/azure/feature#az_feature_list
[az-provider-register]: /cli/azure/provider#az_provider_register
