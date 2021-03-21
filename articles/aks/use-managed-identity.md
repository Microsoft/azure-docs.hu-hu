---
title: Felügyelt identitások használata az Azure Kubernetes szolgáltatásban
description: Ismerje meg, hogyan használhatók a felügyelt identitások az Azure Kubernetes szolgáltatásban (ak)
services: container-service
ms.topic: article
ms.date: 12/16/2020
ms.openlocfilehash: 3ace7f1c93ab3918f460d245a863db43d98f1db5
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102176093"
---
# <a name="use-managed-identities-in-azure-kubernetes-service"></a>Felügyelt identitások használata az Azure Kubernetes szolgáltatásban

Jelenleg egy Azure Kubernetes Service (ak) fürt (konkrétan a Kubernetes Cloud Provider) identitást igényel további erőforrások, például terheléselosztó és felügyelt lemezek létrehozásához az Azure-ban. Ez az identitás *felügyelt identitás* vagy *egyszerű szolgáltatásnév* lehet. Ha [szolgáltatásnevet](kubernetes-service-principal.md)használ, meg kell adnia egy vagy AK-t az Ön nevében. Ha felügyelt identitást használ, a rendszer automatikusan létrehozza ezt. Az egyszerű szolgáltatásokat használó fürtök végül olyan állapotot érnek el, amelyben az egyszerű szolgáltatásnevet meg kell újítani a fürt működésének megtartása érdekében. Az egyszerű szolgáltatások kezelése bonyolultságot biztosít, ezért a felügyelt identitások könnyebben használhatók. Ugyanezek az engedélyezési követelmények érvényesek az egyszerű szolgáltatásokra és a felügyelt identitásokra is.

A *felügyelt identitások* lényegében burkolók az egyszerű szolgáltatásokban, és egyszerűbbé teszik a felügyeletet. A hitelesítő adatok elforgatása az Azure Active Directory alapértelmezett értékének megfelelően automatikusan megtörténik a 46 naponta. Az AK rendszerhez hozzárendelt és felhasználó által hozzárendelt felügyelt identitási típusokat használ. Ezek az identitások jelenleg nem változtathatók meg. További tudnivalókért tekintse meg az [Azure-erőforrások felügyelt identitásait](../active-directory/managed-identities-azure-resources/overview.md)ismertető témakört.

## <a name="before-you-begin"></a>Előkészületek

A következő erőforrást kell telepítenie:

- Az Azure CLI, 2.15.1 vagy újabb verzió

## <a name="limitations"></a>Korlátozások

* A felügyelt identitást engedélyező fürtök áthelyezési/áttelepíti a bérlők nem támogatottak.
* Ha a fürt `aad-pod-identity` engedélyezve van, Node-Managed Identity (NMI) hüvely módosítja a csomópontok iptables-t az Azure-példány metaadatainak végpontjának hívására. Ez a konfiguráció azt jelenti, hogy a metaadat-végpontra irányuló kéréseket a NMI akkor is elfogja, ha a pod nem használja `aad-pod-identity` . A AzurePodIdentityException CRD konfigurálható úgy, hogy tájékoztassa `aad-pod-identity` , hogy a tőkekövetelmény-ből származó, a CRD-ban definiált címkével rendelkező, a NMI-ben való feldolgozás nélkül proxyra irányuló kérelmeket. A `kubernetes.azure.com/managedby: aks` _Kube-_ rendszernévtérben címkével ellátott rendszerhüvelyeket ki kell ZÁRNI a `aad-pod-identity` AzurePodIdentityException CRD konfigurálásával. További információ: [a HRE-Pod-Identity letiltása egy adott Pod vagy alkalmazáshoz](https://azure.github.io/aad-pod-identity/docs/configure/application_exception).
  Kivétel konfigurálásához telepítse a [MIC-Exception YAML](https://github.com/Azure/aad-pod-identity/blob/master/deploy/infra/mic-exception.yaml).

## <a name="summary-of-managed-identities"></a>Felügyelt identitások összefoglalása

Az AK számos felügyelt identitást használ a beépített szolgáltatásokhoz és bővítményekhez.

| Identitás                       | Name    | Használati eset | Alapértelmezett engedélyek | Saját identitás használata
|----------------------------|-----------|----------|
| Vezérlősík | nem látható | Az AK-vezérlési sík összetevői használják a fürt erőforrásainak kezelésére, beleértve a bejövő terheléselosztást és az AK által felügyelt nyilvános IP-címeket, valamint a fürt automatikus méretezési műveleteit | A csomópont-erőforráscsoport közreműködői szerepköre | támogatott
| Kubelet | AK-fürt neve – agentpool | Hitelesítés Azure Container Registry (ACR) | NA (kubernetes v 1.15 +) | Egyelőre nem támogatott
| Bővítmény | AzureNPM | Nincs szükség identitásra | NA | No
| Bővítmény | AzureCNI-hálózat figyelése | Nincs szükség identitásra | NA | No
| Bővítmény | Azure-Policy (forgalomirányító) | Nincs szükség identitásra | NA | No
| Bővítmény | Azure-szabályzat | Nincs szükség identitásra | NA | No
| Bővítmény | Calico | Nincs szükség identitásra | NA | No
| Bővítmény | Irányítópult | Nincs szükség identitásra | NA | No
| Bővítmény | HTTPApplicationRouting | A szükséges hálózati erőforrások kezelése | A DNS-zónához tartozó csomópont-erőforráscsoport, közreműködői szerepkör olvasói szerepköre | No
| Bővítmény | Bejövő alkalmazások átjárója | A szükséges hálózati erőforrások kezelése| A csomópont-erőforráscsoport közreműködői szerepköre | No
| Bővítmény | omsagent | AK-metrikák küldésére szolgál Azure Monitor | A metrikák figyelése – közzétevői szerepkör | No
| Bővítmény | Virtual-Node (ACIConnector) | Azure Container Instances (ACI) szükséges hálózati erőforrásait kezeli | A csomópont-erőforráscsoport közreműködői szerepköre | No
| OSS-projekt | HRE-Pod-Identity | Lehetővé teszi az alkalmazások számára a felhőalapú erőforrások biztonságos elérését Azure Active Directory (HRE) használatával | NA | Az engedélyek megadásának lépései https://github.com/Azure/aad-pod-identity#role-assignment .

## <a name="create-an-aks-cluster-with-managed-identities"></a>AK-fürt létrehozása felügyelt identitásokkal

Most már létrehozhat egy AK-fürtöt a felügyelt identitásokkal a következő CLI-parancsok használatával.

Először hozzon létre egy Azure-erőforráscsoportot:

```azurecli-interactive
# Create an Azure resource group
az group create --name myResourceGroup --location westus2
```

Ezután hozzon létre egy AK-fürtöt:

```azurecli-interactive
az aks create -g myResourceGroup -n myManagedCluster --enable-managed-identity
```

A felügyelt identitásokat használó sikeres fürtök az egyszerű szolgáltatás profiljának adatait tartalmazzák:

```output
"servicePrincipalProfile": {
    "clientId": "msi"
  }
```

Az alábbi paranccsal lekérdezheti a vezérlési sík felügyelt identitásának ObjectId:

```azurecli-interactive
az aks show -g myResourceGroup -n myManagedCluster --query "identity"
```

Az eredménynek a következőhöz hasonlóan kell kinéznie:

```output
{
  "principalId": "<object_id>",   
  "tenantId": "<tenant_id>",      
  "type": "SystemAssigned"                                 
}
```

Miután létrehozta a fürtöt, az alkalmazás számítási feladatait telepítheti az új fürtre, és ugyanúgy kezelheti azt, mint a Service-Principal-alapú AK-fürtökkel.

> [!NOTE]
> Saját VNet, statikus IP-cím vagy csatolt Azure-lemez létrehozásához és használatához, ahol az erőforrások a munkavégző csomópont-erőforráscsoporton kívül esnek, a szerepkör-hozzárendelés végrehajtásához használja a fürthöz rendelt felügyelt identitás PrincipalID. A szerepkör-hozzárendeléssel kapcsolatos további információkért lásd: [hozzáférés delegálása más Azure-erőforrásokhoz](kubernetes-service-principal.md#delegate-access-to-other-azure-resources).
>
> Az engedélyek az Azure Cloud Provider által használt fürtözött felügyelt identitáshoz való jogosultsága akár 60 percet is igénybe vehet.

Végül kapjon hitelesítő adatokat a fürt eléréséhez:

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myManagedCluster
```
## <a name="update-an-aks-cluster-to-managed-identities-preview"></a>AK-fürt frissítése felügyelt identitásokra (előzetes verzió)

Most már frissítheti az egyszerű szolgáltatásnév használatával működő AK-fürtöt a felügyelt identitásokkal való együttműködéshez a következő CLI-parancsokkal.

Először regisztrálja a szolgáltatás jelölőjét a rendszer által hozzárendelt identitáshoz:

```azurecli-interactive
az feature register --namespace Microsoft.ContainerService -n MigrateToMSIClusterPreview
```

A rendszer által hozzárendelt identitás frissítése:

```azurecli-interactive
az aks update -g <RGName> -n <AKSName> --enable-managed-identity
```

Regisztrálja a szolgáltatás jelölőjét a felhasználó által hozzárendelt identitáshoz:

```azurecli-interactive
az feature register --namespace Microsoft.ContainerService -n UserAssignedIdentityPreview
```

A felhasználó által hozzárendelt identitás frissítése:

```azurecli-interactive
az aks update -g <RGName> -n <AKSName> --enable-managed-identity --assign-identity <UserAssignedIdentityResourceID> 
```
> [!NOTE]
> Miután a rendszer által hozzárendelt vagy felhasználó által hozzárendelt identitásokat frissítette a felügyelt identitásra, hajtson végre egy műveletet a `az aks nodepool upgrade --node-image-only` csomópontokon a felügyelt identitás frissítésének befejezéséhez.

## <a name="bring-your-own-control-plane-mi"></a>Saját vezérlő síkja
Az egyéni vezérlő sík identitása lehetővé teszi, hogy a fürt létrehozása előtt hozzáférést biztosítson a meglévő identitáshoz. Ez a funkció olyan forgatókönyveket tesz lehetővé, mint például az egyéni VNET vagy a UDR outboundType egy előre létrehozott felügyelt identitás használatával.

Telepítenie kell az Azure CLI, a 2.15.1 vagy a újabb verzióját.

### <a name="limitations"></a>Korlátozások
* A Azure Government jelenleg nem támogatott.
* Az Azure China 21Vianet jelenleg nem támogatott.

Ha még nem rendelkezik felügyelt identitással, folytassa a következő lépéssel, és hozzon létre egyet például az [az Identity CLI][az-identity-create]használatával.

```azurecli-interactive
az identity create --name myIdentity --resource-group myResourceGroup
```
Az eredménynek a következőhöz hasonlóan kell kinéznie:

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

Ha a felügyelt identitás része az előfizetésnek, akkor az [az Identity CLI paranccsal][az-identity-list] kérdezheti le.  

```azurecli-interactive
az identity list --query "[].{Name:name, Id:id, Location:location}" -o table
```

Most a következő paranccsal hozhatja létre a fürtöt a meglévő identitásával:

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

A saját felügyelt identitások használatával történő sikeres fürtök a userAssignedIdentities-profil adatait tartalmazzák:

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
* Felügyelt identitást használó fürtök létrehozásához használjon [Azure Resource Manager (ARM) sablonokat ][aks-arm-template] .

<!-- LINKS - external -->
[aks-arm-template]: /azure/templates/microsoft.containerservice/managedclusters
[az-identity-create]: /cli/azure/identity#az-identity-create
[az-identity-list]: /cli/azure/identity#az-identity-list
