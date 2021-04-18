---
title: Felügyelt identitások használata a Azure Kubernetes Service
description: Megtudhatja, hogyan használhatja a felügyelt identitásokat Azure Kubernetes Service (AKS)
services: container-service
ms.topic: article
ms.date: 12/16/2020
ms.openlocfilehash: 58813504c5de057e06433b2e955931b37560d825
ms.sourcegitcommit: 950e98d5b3e9984b884673e59e0d2c9aaeabb5bb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2021
ms.locfileid: "107600658"
---
# <a name="use-managed-identities-in-azure-kubernetes-service"></a>Felügyelt identitások használata a Azure Kubernetes Service

Jelenleg az Azure Kubernetes Service -fürtöknek (különösen a Kubernetes-felhőszolgáltatónak) identitásra van szüksége további erőforrások, például terheléselosztások és felügyelt lemezek létrehozásához az Azure-ban. Ez az identitás lehet felügyelt *identitás vagy* *szolgáltatásnév.* Ha egyszerű szolgáltatást [használ,](kubernetes-service-principal.md)meg kell adnia egyet, vagy az AKS létrehoz egyet az Ön nevében. Ha felügyelt identitást használ, azt az AKS automatikusan létre fogja hozni. A szolgáltatásnévvel működő fürtök végül olyan állapotba jutnak, amelyben meg kell újítani őket a fürt megfelelő állapotának eléréséhez. A szolgáltatásnév kezelése összetettebbé teszi a szolgáltatást, ezért egyszerűbb a felügyelt identitások használata. Ugyanezek az engedélykövetelmények vonatkoznak a szolgáltatásnévre és a felügyelt identitásra is.

*A felügyelt identitások* lényegében a szolgáltatásnév burkolói, és egyszerűbbé teszik a felügyeletüket. A MI hitelesítő adatainak rotációja 46 naponta automatikusan megtörténik az Azure Active Directory szerint. Az AKS rendszer által hozzárendelt és felhasználó által hozzárendelt felügyelt identitástípusokat is használ. Ezek az identitások jelenleg nem módosíthatók. További tudnivalókért olvassa el az [Azure-erőforrások felügyelt identitásokkal kapcsolatos cikkét.](../active-directory/managed-identities-azure-resources/overview.md)

## <a name="before-you-begin"></a>Előkészületek

A következő erőforrásnak kell telepítve lennie:

- Az Azure CLI 2.15.1-es vagy újabb verziója

## <a name="limitations"></a>Korlátozások

* A felügyelt identitással kompatibilis fürtök bérlői áthelyezése/áttelepítése nem támogatott.
* Ha a fürt engedélyezve `aad-pod-identity` van, Node-Managed Identity- (NMI-) podok úgy módosítják a csomópontok iptable-it, hogy elfogják az Azure Instance Metadata végpontra való hívásokat. Ez a konfiguráció azt jelenti, hogy az NMI elfogja a metaadat-végpontra vonatkozó kéréseket, még akkor is, ha a pod nem használja a `aad-pod-identity` et. Az AzurePodIdentityException CRD konfigurálható úgy, hogy tájékoztassa arról, hogy a CRD-ben meghatározott címkéknek megfelelő podból származó metaadat-végpontra vonatkozó kéréseket az NMI-ben történő feldolgozás nélkül kell proxyra `aad-pod-identity` állítani. A kube-system névtérben címkével jelölt rendszerpodokat ki kell zárni a fájlból az `kubernetes.azure.com/managedby: aks`  `aad-pod-identity` AzurePodIdentityException CRD konfigurálásával. További információ: [Disable aad-pod-identity for a specific pod or application (Aad-pod-identitás letiltása adott podhoz vagy alkalmazáshoz).](https://azure.github.io/aad-pod-identity/docs/configure/application_exception)
  Kivétel konfiguráláshoz telepítse a [mic-exception YAML-t.](https://github.com/Azure/aad-pod-identity/blob/master/deploy/infra/mic-exception.yaml)

## <a name="summary-of-managed-identities"></a>Felügyelt identitások összefoglalása

Az AKS számos felügyelt identitást használ a beépített szolgáltatásokhoz és bővítményekhez.

| Identitás                       | Name    | Használati eset | Alapértelmezett engedélyek | Saját identitással
|----------------------------|-----------|----------|
| Vezérlősík | nem látható | Az AKS-vezérlősík összetevői használják a fürterőforrások kezelésére, beleértve a bejövő terheléselosztásokat és az AKS által felügyelt nyilvános IP-eket, valamint az automatikus fürtméretezői műveleteket | Közreműködői szerepkör a Node-erőforráscsoporthoz | támogatott
| Kubelet | AKS-fürt név-ügynökkészlete | Hitelesítés Azure Container Registry (ACR) használatával | NA (kubernetes v1.15+) | Egyelőre nem támogatott
| Összead | AzureNPM | Nincs szükség identitásra | NA | No
| Összead | AzureCNI hálózatfigyelés | Nincs szükség identitásra | NA | No
| Összead | azure-policy (gatekeeper) | Nincs szükség identitásra | NA | No
| Összead | azure-policy | Nincs szükség identitásra | NA | No
| Összead | Calico | Nincs szükség identitásra | NA | No
| Összead | Irányítópult | Nincs szükség identitásra | NA | No
| Összead | HTTPApplicationRouting | A szükséges hálózati erőforrásokat kezeli | Olvasó szerepkör a csomópont-erőforráscsoporthoz, közreműködői szerepkör a DNS-zónához | No
| Összead | Bejövő forgalom alkalmazásátjárója | A szükséges hálózati erőforrásokat kezeli| Közreműködői szerepkör a csomópont-erőforráscsoporthoz | No
| Összead | omsagent | AKS-metrikák elküldését Azure Monitor | Monitorozási metrikák közzétevője szerepkör | No
| Összead | Virtual-Node (ACIConnector) | Felügyeli a hálózati erőforrásokat az Azure Container Instances (ACI) számára | Közreműködői szerepkör a csomópont-erőforráscsoporthoz | No
| OSS-projekt | aad-pod-identity | Lehetővé teszi, hogy az alkalmazások biztonságosan hozzáférjenek a felhőbeli erőforrásokhoz Azure Active Directory (AAD) használatával | NA | Az engedély megadásának lépései a következőnél: https://github.com/Azure/aad-pod-identity#role-assignment .

## <a name="create-an-aks-cluster-with-managed-identities"></a>AKS-fürt létrehozása felügyelt identitásokkal

Most már létrehozhat felügyelt identitásokkal egy AKS-fürtöt az alábbi CLI-parancsokkal.

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

## <a name="update-an-aks-cluster-to-managed-identities-preview"></a>AKS-fürt frissítése felügyelt identitásra (előzetes verzió)

A szolgáltatásnévvel jelenleg működő AKS-fürtök mostantól a következő PARANCSSORi felületi parancsokkal frissítve kezelhetők a felügyelt identitásokkal.

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
> Saját virtuális hálózat, statikus IP-cím vagy csatlakoztatott Azure-lemez létrehozásához és használatához, ahol az erőforrások a munkavégző csomópont erőforráscsoportján kívül esnek, használja a fürt Rendszer által hozzárendelt felügyelt identitás PrincipalID azonosítóját a szerepkör-hozzárendelés végrehajtásához. További információ a szerepkör-hozzárendelésről: [Más Azure-erőforrásokhoz való hozzáférés delegálása.](kubernetes-service-principal.md#delegate-access-to-other-azure-resources)
>
> Az Azure-felhőszolgáltató által használt fürt felügyelt identitásának engedélyengedélyeinek feltöltése akár 60 percet is igénybe vehet.


## <a name="bring-your-own-control-plane-mi"></a>Saját vezérlősík mi-hez
Az egyéni vezérlősík-identitás lehetővé teszi, hogy a fürt létrehozása előtt hozzáférést biztosítsunk a meglévő identitáshoz. Ez a funkció olyan forgatókönyveket tesz lehetővé, mint például egyéni VNET vagy UDR outboundType használata előre létrehozott felügyelt identitással.

Telepítve kell lennie az Azure CLI 2.15.1-es vagy újabb verziójának.

### <a name="limitations"></a>Korlátozások
* Azure Government jelenleg nem támogatott.
* Azure China 21Vianet jelenleg nem támogatott.

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

Ha a felügyelt identitás az előfizetés része, az [az identity CLI-paranccsal][az-identity-list] lekérdezheti.  

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

## <a name="next-steps"></a>Következő lépések
* Felügyelt [identitást Azure Resource Manager (ARM)-sablonokkal ][aks-arm-template] hozhat létre.

<!-- LINKS - external -->
[aks-arm-template]: /azure/templates/microsoft.containerservice/managedclusters
[az-identity-create]: /cli/azure/identity#az-identity-create
[az-identity-list]: /cli/azure/identity#az-identity-list
