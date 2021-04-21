---
title: Azure RBAC kezelése a Kubernetesben az Azure-ból
titleSuffix: Azure Kubernetes Service
description: Megtudhatja, hogyan használhatja az Azure RBAC-t a Kubernetes-hitelesítéshez a Azure Kubernetes Service (AKS) használatával.
services: container-service
ms.topic: article
ms.date: 09/21/2020
ms.author: jpalma
author: palma21
ms.openlocfilehash: c708a577a1c2e4bb8f7ddff90f458afd0d9e566f
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107782998"
---
# <a name="use-azure-rbac-for-kubernetes-authorization-preview"></a>Azure RBAC használata Kubernetes-engedélyezéshez (előzetes verzió)

Ma már kihasználhatja az integrált hitelesítést a Azure Active Directory [(Azure AD) és az AKS között.](managed-aad.md) Ha engedélyezve van, ez az integráció lehetővé teszi az ügyfelek számára, hogy Azure AD-felhasználókat, -csoportokat vagy szolgáltatásnévket használjanak a Kubernetes RBAC témáiként. További [információt itt láthat.](azure-ad-rbac.md)
Ezzel a funkcióval nem kell külön kezelnie a Kubernetes felhasználói identitásait és hitelesítő adatait. Az Azure RBAC-t és a Kubernetes RBAC-t azonban külön kell beállítania és kezelnie. Az AKS RBAC-hitelesítéssel és -engedélyezéssel kapcsolatos további részleteket itt [talál:](concepts-identity.md).

Ez a dokumentum egy új megközelítést fed le, amely lehetővé teszi az Azure-erőforrások, az AKS- és a Kubernetes-erőforrások egységes felügyeletét és hozzáférés-vezérlését.

## <a name="before-you-begin"></a>Előkészületek

A Kubernetes-erőforrásokhoz való RBAC Azure-beli kezelése lehetővé teszi, hogy azure-beli vagy natív Kubernetes-mechanizmusokkal kezelje a fürterőforrások RBAC-ját. Ha engedélyezve van, az Azure AD-rendszerbiztonsági tagokat kizárólag az Azure RBAC ellenőrzi, míg a rendszeres Kubernetes-felhasználókat és -szolgáltatásfiókokat kizárólag a Kubernetes RBAC ellenőrzi. Az AKS RBAC-hitelesítéssel és -engedélyezéssel kapcsolatos további részleteket itt [talál:](concepts-identity.md#azure-rbac-for-kubernetes-authorization-preview).

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

### <a name="prerequisites"></a>Előfeltételek 
- Győződjön meg arról, hogy az Azure CLI 2.9.0-s vagy újabb verzióját használja
- Győződjön meg arról, hogy `EnableAzureRBACPreview` engedélyezve van a funkciójelző.
- Győződjön meg arról, hogy a `aks-preview` [CLI-bővítmény][az-extension-add] 0.4.55-ös vagy újabb verzióval van telepítve
- Győződjön meg arról, hogy telepítette a [kubectl v1.18.3+ programot.][az-aks-install-cli]

#### <a name="register-enableazurerbacpreview-preview-feature"></a>Előzetes `EnableAzureRBACPreview` verziójú funkció regisztrálása

A Kubernetes-hitelesítéshez Azure RBAC-t használó AKS-fürt létrehozásához engedélyeznie kell a funkciójelölőt `EnableAzureRBACPreview` az előfizetésén.

Regisztrálja `EnableAzureRBACPreview` a funkciójelölőt [az az feature register paranccsal][az-feature-register] az alábbi példában látható módon:

```azurecli-interactive
az feature register --namespace "Microsoft.ContainerService" --name "EnableAzureRBACPreview"
```

 A regisztrációs állapotot az az feature list paranccsal [ellenőrizheti:][az-feature-list]

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/EnableAzureRBACPreview')].{Name:name,State:properties.state}"
```

Amikor elkészült, frissítse a *Microsoft.ContainerService* erőforrás-szolgáltató regisztrációját [az az provider register paranccsal:][az-provider-register]

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

#### <a name="install-aks-preview-cli-extension"></a>Az aks-preview CLI-bővítmény telepítése

Azure RBAC-t használó AKS-fürt létrehozásához az *aks-preview CLI-bővítmény* 0.4.55-ös vagy újabb verziójára van szükség. Telepítse az *aks-preview* Azure CLI-bővítményt az [az extension add][az-extension-add] paranccsal, vagy telepítse az elérhető frissítéseket az az extension update [paranccsal:][az-extension-update]

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

### <a name="limitations"></a>Korlátozások

- Felügyelt [Azure AD-integrációt igényel.](managed-aad.md)
- Az előzetes verzióban nem integrálhatja a Kuberneteshez való Azure RBAC-hitelesítést a meglévő fürtökbe, az általánosan elérhető (GA) verzióban azonban igen.
- Használja a [kubectl v1.18.3+ et.][az-aks-install-cli]
- Ha CRD-ket és egyéni szerepkör-definíciókat készít, a CRD-ket jelenleg csak a szolgáltatásban lehet `Microsoft.ContainerService/managedClusters/*/read` biztosítani. Az AKS azon dolgozik, hogy részletesebb engedélyeket biztosítson a CRD-khez. A többi objektumhoz használhatja az adott API-csoportokat, például: `Microsoft.ContainerService/apps/deployments/read` .
- Az új szerepkör-hozzárendelések propagálása és az engedélyezési kiszolgáló által való frissítése akár 5 perc is lehet.
- Megköveteli, hogy a hitelesítéshez konfigurált Azure AD-bérlő megegyezik az AKS-fürtöt kiszolgáló előfizetés bérlőével. 

## <a name="create-a-new-cluster-using-azure-rbac-and-managed-azure-ad-integration"></a>Új fürt létrehozása az Azure RBAC és a felügyelt Azure AD-integráció használatával

Hozzon létre egy AKS-fürtöt az alábbi parancssori felületi parancsokkal.

Hozzon létre egy Azure-erőforráscsoportot:

```azurecli-interactive
# Create an Azure resource group
az group create --name myResourceGroup --location westus2
```

Hozza létre az AKS-fürtöt felügyelt Azure AD-integrációval és Azure RBAC-val a Kubernetes-hitelesítéshez.

```azurecli-interactive
# Create an AKS-managed Azure AD cluster
az aks create -g MyResourceGroup -n MyManagedCluster --enable-aad --enable-azure-rbac
```

Az Azure AD-integrációval és a Kubernetes-hitelesítéshez szükséges Azure RBAC-val sikeresen létre lett hozva egy fürt a válasz törzsében a következő szakasz:

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

Az AKS a következő négy beépített szerepkört biztosítja:


| Szerepkör                                | Leírás  |
|-------------------------------------|--------------|
| Azure Kubernetes Service RBAC-olvasó  | Csak olvasási hozzáférést biztosít a névtér legtöbb objektumának eléréséhez. Nem engedélyezi a szerepkörök vagy szerepkörkötések megtekintését. Ez a szerepkör nem engedélyezi a megtekintését, mivel a titkos kulcsok tartalmának beolvasása lehetővé teszi a ServiceAccount hitelesítő adatokhoz való hozzáférést a névtérben, ami lehetővé tenné az API-hozzáférést, mint a névtérben bármely ServiceAccount (a jogosultságok eszkalálásának egy `Secrets` formája)  |
| Azure Kubernetes Service RBAC-író | Olvasási/írási hozzáférést biztosít a névtér legtöbb objektumához. Ez a szerepkör nem engedélyezi a szerepkörök vagy szerepkörkötések megtekintését vagy módosítását. Ez a szerepkör azonban lehetővé teszi a podok bármely ServiceAccount-ként való hozzáférését és futtatását a névtérben, így a névtérben bármely `Secrets` ServiceAccount API-hozzáférési szintje elérhető. |
| Azure Kubernetes Service RBAC-rendszergazda  | Lehetővé teszi a rendszergazdai hozzáférést, amely egy névtéren belül adható meg. Olvasási/írási hozzáférést biztosít a névtér (vagy fürthatókör) legtöbb erőforrása számára, beleértve a szerepkörök és szerepkörkötések létrehozására való képességet a névtérben. Ez a szerepkör nem engedélyezi írási hozzáférést az erőforráskvótához vagy a névtérhez. |
| Azure Kubernetes Service RBAC-fürt rendszergazdája  | Lehetővé teszi, hogy a felügyelői hozzáférés bármilyen műveletet hajtson végre bármely erőforráson. Teljes körű vezérlést biztosít a fürt minden erőforrása és minden névtér felett. |


A teljes **AKS-fürtre** hatókörrel rendelkező szerepkör-hozzárendelések az Access Control fürterőforrás Access Control (IAM Azure Portal) paneljével vagy azure CLI-parancsokkal jelennek meg az alábbi módon:

```bash
# Get your AKS Resource ID
AKS_ID=$(az aks show -g MyResourceGroup -n MyManagedCluster --query id -o tsv)
```

```azurecli-interactive
az role assignment create --role "Azure Kubernetes Service RBAC Admin" --assignee <AAD-ENTITY-ID> --scope $AKS_ID
```

ahol lehet egy felhasználónév (például ) vagy akár `<AAD-ENTITY-ID>` egy user@contoso.com szolgáltatásnév ClientID-e.

Szerepkör-hozzárendeléseket is létrehozhat, amelyek  hatóköre a fürt egy adott névterére terjed ki:

```azurecli-interactive
az role assignment create --role "Azure Kubernetes Service RBAC Viewer" --assignee <AAD-ENTITY-ID> --scope $AKS_ID/namespaces/<namespace-name>
```

Jelenleg a névterek hatókörű szerepkör-hozzárendeléseket az Azure CLI-n keresztül kell konfigurálni.


### <a name="create-custom-roles-definitions"></a>Egyéni szerepkör-definíciók létrehozása

Dönthet úgy is, hogy saját szerepkör-definíciót hoz létre, majd a fenti módon rendeli hozzá.

Az alábbi példa egy olyan szerepkör-definíciót mutat be, amely lehetővé teszi a felhasználó számára, hogy csak az üzemelő példányokat olvassa, és semmi mást. A lehetséges műveletek teljes listáját itt [ellenőrizheti.](../role-based-access-control/resource-provider-operations.md#microsoftcontainerservice)


Másolja az alábbi JSON-t egy nevű `deploy-view.json` fájlba.

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

Cserélje `<YOUR SUBSCRIPTION ID>` le a helyére az előfizetéséből származó azonosítót, amelyet a következő futtatásával kaphat meg:

```azurecli-interactive
az account show --query id -o tsv
```


Most létrehozhatjuk a szerepkör-definíciót az alábbi parancsnak a mentett mappából való `deploy-view.json` futtatásával:

```azurecli-interactive
az role definition create --role-definition @deploy-view.json 
```

Most, hogy már megvan a szerepkör-definíciója, a következő futtatásával rendelheti hozzá egy felhasználóhoz vagy más identitáshoz:

```azurecli-interactive
az role assignment create --role "AKS Deployment Viewer" --assignee <AAD-ENTITY-ID> --scope $AKS_ID
```

## <a name="use-azure-rbac-for-kubernetes-authorization-with-kubectl"></a>Az Azure RBAC használata Kubernetes-hitelesítéshez a használatával `kubectl`

> [!NOTE]
> Az alábbi parancs futtatásával győződjön meg arról, hogy a legújabb kubectl fut:
>
> ```azurecli-interactive
> az aks install-cli
> ```
> Előfordulhat, hogy jogosultságokkal kell `sudo` futtatnia. 

Most, hogy hozzárendelt egy szerepkört és engedélyeket. Elindíthatja a Kubernetes API hívását, például `kubectl` innen: .

Ehhez először szerezze be a fürt kubeconfig parancsát az alábbi paranccsal:

```azurecli-interactive
az aks get-credentials -g MyResourceGroup -n MyManagedCluster
```

> [!IMPORTANT]
> A fenti lépés [végrehajtásához Azure Kubernetes Service](../role-based-access-control/built-in-roles.md#azure-kubernetes-service-cluster-user-role) fürtfelhasználó beépített szerepkörre lesz szüksége.

Most már használhatja a kubectl-t például a fürt csomópontjainak listához. Az első futtatáskor be kell jelentkeznie, és a következő parancsok a megfelelő hozzáférési jogkivonatot fogják használni.

```azurecli-interactive
kubectl get nodes
To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code AAAAAAAAA to authenticate.

NAME                                STATUS   ROLES   AGE    VERSION
aks-nodepool1-93451573-vmss000000   Ready    agent   3h6m   v1.15.11
aks-nodepool1-93451573-vmss000001   Ready    agent   3h6m   v1.15.11
aks-nodepool1-93451573-vmss000002   Ready    agent   3h6m   v1.15.11
```


## <a name="use-azure-rbac-for-kubernetes-authorization-with-kubelogin"></a>Az Azure RBAC használata Kubernetes-hitelesítéshez a használatával `kubelogin`

Az olyan további forgatókönyvek blokkolásának feloldásához, mint a nem interaktív bejelentkezések, a régebbi verziók vagy az SSO használata több fürtön anélkül, hogy új fürtbe kellene bejelentkeznie, felvéve a jogkivonat érvényességét, az AKS létrehozott egy nevű exec beépülő `kubectl` modult. [`kubelogin`](https://github.com/Azure/kubelogin)

A következő futtatásával használhatja:

```bash
export KUBECONFIG=/path/to/kubeconfig
kubelogin convert-kubeconfig
``` 

Az első alkalommal interaktívan kell bejelentkeznie, mint a normál kubectl használatával, de később már nincs rá szüksége, még az új Azure AD-fürtökre sem (amíg a jogkivonat érvényes).

```bash
kubectl get nodes
To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code AAAAAAAAA to authenticate.

NAME                                STATUS   ROLES   AGE    VERSION
aks-nodepool1-93451573-vmss000000   Ready    agent   3h6m   v1.15.11
aks-nodepool1-93451573-vmss000001   Ready    agent   3h6m   v1.15.11
aks-nodepool1-93451573-vmss000002   Ready    agent   3h6m   v1.15.11
```


## <a name="clean-up"></a>A fölöslegessé vált elemek eltávolítása

### <a name="clean-role-assignment"></a>Szerepkör-hozzárendelés megtisztítva

```azurecli-interactive
az role assignment list --scope $AKS_ID --query [].id -o tsv
```
Másolja ki az azonosítót vagy azonosítókat az összes addigi hozzárendelésből.

```azurecli-interactive
az role assignment delete --ids <LIST OF ASSIGNMENT IDS>
```

### <a name="clean-up-role-definition"></a>Szerepkör-definíciók tisztítása

```azurecli-interactive
az role definition delete -n "AKS Deployment Viewer"
```

### <a name="delete-cluster-and-resource-group"></a>Fürt és erőforráscsoport törlése

```azurecli-interactive
az group delete -n MyResourceGroup
```

## <a name="next-steps"></a>Következő lépések

- Az AKS-hitelesítésről, az engedélyezésről, a Kubernetes RBAC-ről és az Azure RBAC-ről itt [olvashat bővebben.](concepts-identity.md)
- Az Azure RBAC-ről itt [olvashat bővebben.](../role-based-access-control/overview.md)
- A Kubernetes-hitelesítéshez használható egyéni Azure-szerepkörök részletes meghatározásához használható összes műveletről itt [olvashat bővebben.](../role-based-access-control/resource-provider-operations.md#microsoftcontainerservice)


<!-- LINKS - Internal -->
[aks-support-policies]: support-policies.md
[aks-faq]: faq.md
[az-extension-add]: /cli/azure/extension#az_extension_add
[az-extension-update]: /cli/azure/extension#az_extension_update
[az-feature-list]: /cli/azure/feature#az_feature_list
[az-feature-register]: /cli/azure/feature#az_feature_register
[az-aks-install-cli]: /cli/azure/aks#az_aks_install_cli
[az-provider-register]: /cli/azure/provider#az_provider_register
