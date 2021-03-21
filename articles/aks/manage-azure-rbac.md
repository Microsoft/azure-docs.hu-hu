---
title: Azure-RBAC kezelése az Azure-beli Kubernetes
titleSuffix: Azure Kubernetes Service
description: Ismerje meg, hogyan használható az Azure RBAC az Kubernetes-engedélyezéshez az Azure Kubernetes Service (ak) szolgáltatással.
services: container-service
ms.topic: article
ms.date: 09/21/2020
ms.author: jpalma
author: palma21
ms.openlocfilehash: 9ce8bc71139b52d690893734435e6bfee090062d
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102184372"
---
# <a name="use-azure-rbac-for-kubernetes-authorization-preview"></a>Azure RBAC használata Kubernetes-engedélyezéshez (előzetes verzió)

Ma már [a Azure Active Directory (Azure ad) és az AK közötti integrált hitelesítést](managed-aad.md)is kihasználhatja. Ha ez a beállítás engedélyezve van, ez az integráció lehetővé teszi, hogy az ügyfelek az Azure AD-felhasználókat,-csoportokat vagy egyszerű szolgáltatásokat használják a Kubernetes-RBAC, [itt](azure-ad-rbac.md)találhat további információt.
Ezzel a funkcióval a Kubernetes felhasználói identitások és hitelesítő adatok külön kezelhetők. Azonban továbbra is külön kell beállítania és kezelnie az Azure RBAC és a Kubernetes RBAC. További információ a hitelesítésről és az RBAC-mel történő engedélyezésről: [itt](concepts-identity.md).

Ez a dokumentum egy olyan új megközelítést mutat be, amely lehetővé teszi az egységes felügyeleti és hozzáférés-vezérlést az Azure-erőforrások, az AK-és a Kubernetes-erőforrások között.

## <a name="before-you-begin"></a>Előkészületek

Az Azure-ból származó Kubernetes-erőforrások RBAC kezelése lehetővé teszi, hogy az Azure-ban vagy a natív Kubernetes-mechanizmusok használatával kezelhesse a RBAC. Ha ez a beállítás engedélyezve van, az Azure AD-rendszerbiztonsági tag kizárólag az Azure RBAC lesz érvényesítve, míg a normál Kubernetes-felhasználók és-szolgáltatásfiókok a Kubernetes-RBAC által kizárólag érvényesítve vannak. További információ a hitelesítésről és az RBAC-mel történő engedélyezésről: [itt](concepts-identity.md#azure-rbac-for-kubernetes-authorization-preview).

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

### <a name="prerequisites"></a>Előfeltételek 
- Győződjön meg arról, hogy az Azure CLI 2.9.0 vagy újabb verziója van
- Győződjön meg arról, hogy engedélyezve van a `EnableAzureRBACPreview` funkció jelzője.
- Győződjön meg arról, hogy a `aks-preview` [CLI-bővítmény][az-extension-add] v 0.4.55 vagy újabb verziója van telepítve
- Győződjön meg arról, hogy telepítette a [kubectl v 1.18.3 +][az-aks-install-cli]alkalmazást.

#### <a name="register-enableazurerbacpreview-preview-feature"></a>`EnableAzureRBACPreview`Előzetes verzió funkciójának regisztrálása

Az Azure RBAC Kubernetes-engedélyezést használó AK-fürtök létrehozásához engedélyeznie kell a `EnableAzureRBACPreview` szolgáltatás jelölőjét az előfizetésén.

Regisztrálja a `EnableAzureRBACPreview` szolgáltatás jelölőjét az az [Feature Register][az-feature-register] paranccsal az alábbi példában látható módon:

```azurecli-interactive
az feature register --namespace "Microsoft.ContainerService" --name "EnableAzureRBACPreview"
```

 A regisztrációs állapotot az az [Feature List][az-feature-list] parancs használatával tekintheti meg:

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/EnableAzureRBACPreview')].{Name:name,State:properties.state}"
```

Ha elkészült, frissítse a *Microsoft. tárolószolgáltatás* erőforrás-szolgáltató regisztrációját az az [Provider Register][az-provider-register] paranccsal:

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

#### <a name="install-aks-preview-cli-extension"></a>Az aks-preview CLI-bővítmény telepítése

Az Azure RBAC-t használó AK-fürtök létrehozásához a CLI *-előnézet CLI-* bővítményének 0.4.55 vagy újabb verziójára van szükség. Telepítse az *AK – előzetes* verzió Azure CLI bővítményét az az [Extension Add][az-extension-add] paranccsal, vagy telepítse az elérhető frissítéseket az az [Extension Update][az-extension-update] paranccsal:

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

### <a name="limitations"></a>Korlátozások

- [Felügyelt Azure ad-integrációt](managed-aad.md)igényel.
- Előzetes verzióban nem integrálható az Azure RBAC a Kubernetes-engedélyezésbe meglévő fürtökre, de az általánosan elérhetővé válik.
- A [kubectl v 1.18.3 +][az-aks-install-cli]használata.
- Ha rendelkezik CRDs, és egyéni szerepkör-definíciókat használ, az egyetlen módszer a CRDs lefedésére `Microsoft.ContainerService/managedClusters/*/read` . Az AK dolgozik a CRDs vonatkozó részletesebb engedélyek biztosításán. A többi objektum esetében használhatja az adott API-csoportokat, például: `Microsoft.ContainerService/apps/deployments/read` .
- Az új szerepkör-hozzárendelések akár 5perc is eltarthat, és az engedélyezési kiszolgáló frissítheti azokat.
- Az Azure AD-bérlőt úgy kell konfigurálni, hogy az AK-fürtöt tartalmazó előfizetés bérlője azonos legyen a hitelesítéshez. 

## <a name="create-a-new-cluster-using-azure-rbac-and-managed-azure-ad-integration"></a>Új fürt létrehozása az Azure RBAC és a felügyelt Azure AD-integráció használatával

Hozzon létre egy AK-fürtöt az alábbi CLI-parancsok használatával.

Azure-erőforráscsoport létrehozása:

```azurecli-interactive
# Create an Azure resource group
az group create --name myResourceGroup --location westus2
```

Hozza létre az AK-fürtöt a felügyelt Azure AD-integrációval és az Azure RBAC a Kubernetes engedélyezéséhez.

```azurecli-interactive
# Create an AKS-managed Azure AD cluster
az aks create -g MyResourceGroup -n MyManagedCluster --enable-aad --enable-azure-rbac
```

Az Azure AD-integrációval és az Azure RBAC for Kubernetes-engedélyezéssel rendelkező fürt sikeres létrehozása a következő szakaszban található a válasz törzsében:

```json
"AADProfile": {
    "adminGroupObjectIds": null,
    "clientAppId": null,
    "enableAzureRbac": true,
    "managed": true,
    "serverAppId": null,
    "serverAppSecret": null,
    "tenantId": "****-****-****-****-****"
  }
```

## <a name="create-role-assignments-for-users-to-access-cluster"></a>Szerepkör-hozzárendelések létrehozása a felhasználók számára a fürt eléréséhez

Az AK a következő négy beépített szerepkört biztosítja:


| Szerepkör                                | Leírás  |
|-------------------------------------|--------------|
| Azure Kubernetes Service RBAC-olvasó  | Lehetővé teszi a csak olvasási hozzáférést a névtérben lévő legtöbb objektum megtekintéséhez. Nem teszi lehetővé a szerepkörök és a szerepkör-kötések megtekintését. Ez a szerepkör nem teszi lehetővé a megtekintést `Secrets` , mivel a titkok tartalmának olvasása lehetővé teszi a ServiceAccount hitelesítő adatainak elérését a névtérben, ami lehetővé tenné az API-hozzáférést a névtérben található bármely ServiceAccount (a jogosultságok eszkalációjának formája)  |
| Az Azure Kubernetes Service RBAC-írója | Olvasási/írási hozzáférést tesz lehetővé a névtér legtöbb objektumához. Ez a szerepkör nem teszi lehetővé a szerepkörök vagy szerepkör-kötések megtekintését és módosítását. Ez a szerepkör azonban lehetővé teszi a `Secrets` hüvelyek elérését és futtatását a névtér bármely ServiceAccount, így felhasználható a névtérben található bármely SERVICEACCOUNT API-hozzáférési szintjeinek megszerzésére. |
| Az Azure Kubernetes Service RBAC rendszergazdája  | A rendszergazdai hozzáférés engedélyezése a névtéren belül. Írási/olvasási hozzáférés engedélyezése a névtér (vagy a fürt hatóköre) legtöbb erőforrásához, beleértve a szerepkörök és a szerepkör-kötések létrehozását a névtéren belül. Ez a szerepkör nem engedélyez írási hozzáférést az erőforrás-kvótához vagy magához a névtérhez. |
| Azure Kubernetes Service RBAC-fürt rendszergazdája  | Lehetővé teszi a felügyelők számára, hogy bármilyen műveletet végezzenek bármilyen erőforráson. Teljes hozzáférést biztosít a fürt összes erőforrásához és az összes névtérhez. |


A **teljes AK-fürtre** hatókörrel rendelkező szerepkör-hozzárendelések a fürterőforrás Access Control (iam) paneljén, Azure Portal vagy Azure CLI-parancsok használatával hajthatók végre, ahogy az alábbi ábrán is látható:

```bash
# Get your AKS Resource ID
AKS_ID=$(az aks show -g MyResourceGroup -n MyManagedCluster --query id -o tsv)
```

```azurecli-interactive
az role assignment create --role "Azure Kubernetes Service RBAC Admin" --assignee <AAD-ENTITY-ID> --scope $AKS_ID
```

hol `<AAD-ENTITY-ID>` lehet a Felhasználónév (például:), user@contoso.com vagy akár egy egyszerű szolgáltatásnév ClientID is.

A fürtön belüli adott **névtérhez** hatókörrel rendelkező szerepkör-hozzárendeléseket is létrehozhat:

```azurecli-interactive
az role assignment create --role "Azure Kubernetes Service RBAC Viewer" --assignee <AAD-ENTITY-ID> --scope $AKS_ID/namespaces/<namespace-name>
```

Napjainkban a névterekre hatókörrel rendelkező szerepkör-hozzárendeléseket az Azure CLI-n keresztül kell konfigurálni.


### <a name="create-custom-roles-definitions"></a>Egyéni szerepkör-definíciók létrehozása

Szükség esetén dönthet úgy is, hogy létrehoz egy saját szerepkör-definíciót, majd hozzárendeli a fentiekhez.

Az alábbi példa egy szerepkör-definíciót mutat be, amely lehetővé teszi, hogy a felhasználó csak az üzembe helyezéseket olvassa, és semmi mást. A lehetséges műveletek teljes listáját [itt](../role-based-access-control/resource-provider-operations.md#microsoftcontainerservice)tekintheti meg.


Másolja az alábbi JSON-fájlt egy nevű fájlba `deploy-view.json` .

```json
{
    "Name": "AKS Deployment Viewer",
    "Description": "Lets you view all deployments in cluster/namespace.",
    "Actions": [],
    "NotActions": [],
    "DataActions": [
        "Microsoft.ContainerService/managedClusters/apps/deployments/read"
    ],
    "NotDataActions": [],
    "assignableScopes": [
        "/subscriptions/<YOUR SUBSCRIPTION ID>"
    ]
}
```

`<YOUR SUBSCRIPTION ID>`Az előfizetése azonosítóját cserélje le, amelyet a következő futtatásával érhet el:

```azurecli-interactive
az account show --query id -o tsv
```


Most létrehozhatjuk a szerepkör-definíciót az alábbi parancs futtatásával abban a mappában, ahová mentette `deploy-view.json` :

```azurecli-interactive
az role definition create --role-definition @deploy-view.json 
```

Most, hogy már rendelkezik a szerepkör-definícióval, a következő parancs futtatásával rendelheti hozzá egy felhasználóhoz vagy más identitáshoz:

```azurecli-interactive
az role assignment create --role "AKS Deployment Viewer" --assignee <AAD-ENTITY-ID> --scope $AKS_ID
```

## <a name="use-azure-rbac-for-kubernetes-authorization-with-kubectl"></a>Az Azure RBAC használata a Kubernetes engedélyezéséhez `kubectl`

> [!NOTE]
> Az alábbi parancs futtatásával ellenőrizze, hogy rendelkezik-e a legújabb kubectl:
>
> ```azurecli-interactive
> az aks install-cli
> ```
> Előfordulhat, hogy a jogosultságokkal kell futtatnia `sudo` . 

Most, hogy hozzárendelte a kívánt szerepkört és engedélyeket. Megkezdheti a Kubernetes API meghívását, például: `kubectl` .

Erre a célra először kérje le a fürt kubeconfig az alábbi parancs használatával:

```azurecli-interactive
az aks get-credentials -g MyResourceGroup -n MyManagedCluster
```

> [!IMPORTANT]
> A fenti lépés végrehajtásához szüksége lesz az [Azure Kubernetes Service cluster felhasználói](../role-based-access-control/built-in-roles.md#azure-kubernetes-service-cluster-user-role) beépített szerepkörre.

Most használhatja a kubectl-t, például listázhatja a fürt csomópontjait. Amikor először futtatja, be kell jelentkeznie, és a következő parancsok a megfelelő hozzáférési tokent fogják használni.

```azurecli-interactive
kubectl get nodes
To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code AAAAAAAAA to authenticate.

NAME                                STATUS   ROLES   AGE    VERSION
aks-nodepool1-93451573-vmss000000   Ready    agent   3h6m   v1.15.11
aks-nodepool1-93451573-vmss000001   Ready    agent   3h6m   v1.15.11
aks-nodepool1-93451573-vmss000002   Ready    agent   3h6m   v1.15.11
```


## <a name="use-azure-rbac-for-kubernetes-authorization-with-kubelogin"></a>Az Azure RBAC használata a Kubernetes engedélyezéséhez `kubelogin`

Ha további forgatókönyveket szeretne feloldani, például nem interaktív bejelentkezéseket, régebbi `kubectl` verziókat vagy egyszeri bejelentkezést használ több fürtön anélkül, hogy be kellene jelentkeznie az új fürtbe, a jogkivonat még érvényes, az AK létrehozta a nevű exec beépülő modult [`kubelogin`](https://github.com/Azure/kubelogin) .

A futtatásához a következőket használhatja:

```bash
export KUBECONFIG=/path/to/kubeconfig
kubelogin convert-kubeconfig
``` 

Az első alkalommal interaktív módon kell bejelentkeznie, mint a normál kubectl, de az új Azure AD-fürtökhöz még nem kell többé szüksége (ha a token még érvényes).

```bash
kubectl get nodes
To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code AAAAAAAAA to authenticate.

NAME                                STATUS   ROLES   AGE    VERSION
aks-nodepool1-93451573-vmss000000   Ready    agent   3h6m   v1.15.11
aks-nodepool1-93451573-vmss000001   Ready    agent   3h6m   v1.15.11
aks-nodepool1-93451573-vmss000002   Ready    agent   3h6m   v1.15.11
```


## <a name="clean-up"></a>A fölöslegessé vált elemek eltávolítása

### <a name="clean-role-assignment"></a>Szerepkör-hozzárendelés törlése

```azurecli-interactive
az role assignment list --scope $AKS_ID --query [].id -o tsv
```
Másolja az azonosítót vagy az azonosítókat az összes olyan hozzárendelésből, amelyet tettél, majd.

```azurecli-interactive
az role assignment delete --ids <LIST OF ASSIGNMENT IDS>
```

### <a name="clean-up-role-definition"></a>Szerepkör-definíció törlése

```azurecli-interactive
az role definition delete -n "AKS Deployment Viewer"
```

### <a name="delete-cluster-and-resource-group"></a>Fürt és erőforráscsoport törlése

```azurecli-interactive
az group delete -n MyResourceGroup
```

## <a name="next-steps"></a>Következő lépések

- További információ az AK-hitelesítésről, az engedélyezésről, a Kubernetes RBAC és az [Azure RBAC.](concepts-identity.md)
- További információk az Azure RBAC-ről [itt](../role-based-access-control/overview.md)olvashat.
- További információ a Kubernetes engedélyezéséhez használható egyéni Azure-szerepkörök részletes meghatározásával kapcsolatos összes műveletről [itt](../role-based-access-control/resource-provider-operations.md#microsoftcontainerservice)olvashat bővebben.


<!-- LINKS - Internal -->
[aks-support-policies]: support-policies.md
[aks-faq]: faq.md
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-aks-install-cli]: /cli/azure/aks#az-aks-install-cli
[az-provider-register]: /cli/azure/provider#az-provider-register
