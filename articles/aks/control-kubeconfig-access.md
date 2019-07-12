---
title: Az Azure Kubernetes Service (AKS) kubeconfig való hozzáférés korlátozása
description: Ismerje meg, hogyan férhet hozzá a Kubernetes konfigurációs fájl (kubeconfig) a fürt rendszergazdák és a fürt felhasználók
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 05/31/2019
ms.author: mlearned
ms.openlocfilehash: cbc653b86ed83f9d6a7348d39f51dc7cd49c6892
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/07/2019
ms.locfileid: "67615675"
---
# <a name="use-azure-role-based-access-controls-to-define-access-to-the-kubernetes-configuration-file-in-azure-kubernetes-service-aks"></a>Hozzáférés megadása a Kubernetes konfigurációs fájl az Azure Kubernetes Service (AKS) az Azure szerepköralapú hozzáférés-vezérlés használatával

Kubernetes-fürtök használata kezelheti a `kubectl` eszközt. Az Azure CLI beolvasni a hozzáférési hitelesítő adatok egyszerű megoldást kínál, és a konfigurációs adatokat szeretne csatlakozni az AKS-fürtök használatával `kubectl`. Korlátot, akik kérheti, hogy a Kubernetes-konfigurációját (*kubeconfig*) információt, és korlátozza az engedélyeket, majd rendelkezik, használhatja az Azure szerepköralapú hozzáférés-vezérlők (RBAC).

Ez a cikk bemutatja, hogyan rendelje hozzá ezt a korlátot, akik az AKS-fürt konfigurációs információkat kaphat az RBAC-szerepkörök.

## <a name="before-you-begin"></a>Előkészületek

Ez a cikk azt feltételezi, hogy egy meglévő AKS-fürtöt. Ha egy AKS-fürtre van szüksége, tekintse meg az AKS gyors [az Azure CLI-vel][aks-quickstart-cli] or [using the Azure portal][aks-quickstart-portal].

Ez a cikk is szükséges, hogy futnak-e az Azure CLI 2.0.65 verzió vagy újabb. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése][azure-cli-install].

## <a name="available-cluster-roles-permissions"></a>Elérhető fürtözött szerepkörök engedélyek

Ha egy AKS fürt használata kezelheti a `kubectl` az eszközt, a konfigurációs fájlt, amely meghatározza a fürtkapcsolati adatok használják. Ezt a konfigurációs fájlt általában tárolva *~/.kube/config*. Több fürt lehet definiálni ez *kubeconfig* fájlt. Fürtök közötti váltás a [kubectl konfigurációs használatát – helyi][kubectl-config-use-context] parancsot.

A [az aks get-credentials][az-aks-get-credentials] parancs lehetővé teszi a hozzáférési hitelesítő adatok beszerzése egy AKS-fürtöt, és egyesíti azokat az *kubeconfig* fájlt. Az Azure szerepköralapú hozzáférés-vezérlők (RBAC) segítségével férhet hozzá ezeket a hitelesítő adatokat. Ezeket az Azure RBAC-szerepkörök segítségével meghatározhatja, hogy ki lehet lekérdezni a *kubeconfig* fájlt, és milyen engedélyeket, majd a fürtön belül van.

A két beépített szerepkörök a következők:

* **Az Azure Kubernetes Service-fürt rendszergazdai szerepkör**  
    * Engedélyezi a hozzáférést *Microsoft.ContainerService/managedClusters/listClusterAdminCredential/action* API-hívás. Az API-hívás [sorolja fel a fürt rendszergazdai hitelesítő adataival][api-cluster-admin].
    * Letöltések *kubeconfig* számára a *clusterAdmin* szerepkör.
* **Az Azure Kubernetes Service-fürt felhasználói szerepkör**
    * Engedélyezi a hozzáférést *Microsoft.ContainerService/managedClusters/listClusterUserCredential/action* API-hívás. Az API-hívás [sorolja fel a fürt felhasználói hitelesítő adatok][api-cluster-user].
    * Letöltések *kubeconfig* a *clusterUser* szerepkör.

Ezek az RBAC-szerepkörök egy Azure Active Directory (AD) felhasználó vagy csoport is alkalmazható.

## <a name="assign-role-permissions-to-a-user-or-group"></a>Szerepkör-engedélyek hozzárendelése egy felhasználóhoz vagy csoporthoz

Az elérhető szerepkörök hozzárendeléséhez kell az erőforrás-Azonosítóját az AKS-fürtöt és az Azure AD felhasználói fiók vagy csoport azonosítója. A következő Példaparancsok:

* A fürt resource ID használatával beolvasása a [az aks show][az-aks-show] parancsot a fürt neve *myAKSCluster* a a *myResourceGroup* erőforráscsoportot. Adja meg a saját fürt- és erőforrás-csoport neve, igény szerint.
* Használja a [az fiók show][az-account-show] and [az ad user show][az-ad-user-show] parancsok beolvasni a felhasználói azonosítóját.
* Végül hozzárendel egy szerepkör használatával a [az szerepkör-hozzárendelés létrehozása][az-role-assignment-create] parancsot.

Az alábbi példa a *Azure Kubernetes Service fürt rendszergazdai szerepkör* egyedi felhasználói fiókokhoz:

```azurecli-interactive
# Get the resource ID of your AKS cluster
AKS_CLUSTER=$(az aks show --resource-group myResourceGroup --name myAKSCluster --query id -o tsv)

# Get the account credentials for the logged in user
ACCOUNT_UPN=$(az account show --query user.name -o tsv)
ACCOUNT_ID=$(az ad user show --upn-or-object-id $ACCOUNT_UPN --query objectId -o tsv)

# Assign the 'Cluster Admin' role to the user
az role assignment create \
    --assignee $ACCOUNT_ID \
    --scope $AKS_CLUSTER \
    --role "Azure Kubernetes Service Cluster Admin Role"
```

> [!TIP]
> Ha meg szeretné engedélyek hozzárendelése az Azure AD-csoportok, frissítse a `--assignee` paraméter a objektumazonosítójú az előző példában látható a *csoport* helyett egy *felhasználói*. A csoport Objektumazonosítójának megszerzéséhez használja a [az ad-csoport megjelenítése][az-ad-group-show] parancsot. Az alábbi példa lekéri Objektumazonosítóját az Azure AD-csoport nevű *appdev*: `az ad group show --group appdev --query objectId -o tsv`

A korábbi hozzárendelés módosíthatja a *fürt felhasználói szerepkör* igény szerint.

Az alábbi példa kimenetében látható, a szerepkör-hozzárendelés létrehozása sikerült:

```
{
  "canDelegate": null,
  "id": "/subscriptions/<guid>/resourcegroups/myResourceGroup/providers/Microsoft.ContainerService/managedClusters/myAKSCluster/providers/Microsoft.Authorization/roleAssignments/b2712174-5a41-4ecb-82c5-12b8ad43d4fb",
  "name": "b2712174-5a41-4ecb-82c5-12b8ad43d4fb",
  "principalId": "946016dd-9362-4183-b17d-4c416d1f8f61",
  "resourceGroup": "myResourceGroup",
  "roleDefinitionId": "/subscriptions/<guid>/providers/Microsoft.Authorization/roleDefinitions/0ab01a8-8aac-4efd-b8c2-3ee1fb270be8",
  "scope": "/subscriptions/<guid>/resourcegroups/myResourceGroup/providers/Microsoft.ContainerService/managedClusters/myAKSCluster",
  "type": "Microsoft.Authorization/roleAssignments"
}
```

## <a name="get-and-verify-the-configuration-information"></a>GET, és ellenőrizze a konfigurációs adatokat

Az RBAC-szerepkörökhöz rendelt, használja a [az aks get-credentials][az-aks-get-credentials] parancs használatával beszerezheti az *kubeconfig* az AKS-fürt definíciója. Az alábbi példa lekéri a *– rendszergazdai* megfelelően működik, ha a felhasználó megkapta-e hitelesítő adatokat a *fürt rendszergazdai szerepkör*:

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster --admin
```

Ezután a [kubectl konfigurációs nézet][kubectl-config-view] paranccsal ellenőrizheti, hogy a *környezet* esetében a fürt látható, hogy a felügyeleti konfigurációs adatokat:

```
$ kubectl config view

apiVersion: v1
clusters:
- cluster:
    certificate-authority-data: DATA+OMITTED
    server: https://myaksclust-myresourcegroup-19da35-4839be06.hcp.eastus.azmk8s.io:443
  name: myAKSCluster
contexts:
- context:
    cluster: myAKSCluster
    user: clusterAdmin_myResourceGroup_myAKSCluster
  name: myAKSCluster-admin
current-context: myAKSCluster-admin
kind: Config
preferences: {}
users:
- name: clusterAdmin_myResourceGroup_myAKSCluster
  user:
    client-certificate-data: REDACTED
    client-key-data: REDACTED
    token: e9f2f819a4496538b02cefff94e61d35
```

## <a name="remove-role-permissions"></a>Távolítsa el a szerepkör engedélyei

Szerepkör-hozzárendelések eltávolításához használja a [az szerepkör-hozzárendelés törlése][az-role-assignment-delete] parancsot. A fiók-fürterőforrás-Azonosítót, és adja meg az előző parancsokkal beszerzett. Ha a szerepkörhöz rendelt felhasználó helyett inkább egy csoportot, adja meg a megfelelő csoportházirend-objektum Objektumazonosítója fiók helyett azonosítója a `--assignee` paramétert:

```azurecli-interactive
az role assignment delete --assignee $ACCOUNT_ID --scope $AKS_CLUSTER
```

## <a name="next-steps"></a>További lépések

AKS-fürtök esetén a hozzáférést a fokozott biztonságot [Azure Active Directory-hitelesítés integrálása][aad-integration].

<!-- LINKS - external -->
[kubectl-config-use-context]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#config
[kubectl-config-view]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#config

<!-- LINKS - internal -->
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[azure-cli-install]: /cli/azure/install-azure-cli
[az-aks-get-credentials]: /cli/azure/aks#az-aks-get-credentials
[azure-rbac]: ../role-based-access-control/overview.md
[api-cluster-admin]: /rest/api/aks/managedclusters/listclusteradmincredentials
[api-cluster-user]: /rest/api/aks/managedclusters/listclusterusercredentials
[az-aks-show]: /cli/azure/aks#az-aks-show
[az-account-show]: /cli/azure/account#az-account-show
[az-ad-user-show]: /cli/azure/ad/user#az-ad-user-show
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
[az-role-assignment-delete]: /cli/azure/role/assignment#az-role-assignment-delete
[aad-integration]: azure-ad-integration.md
[az-ad-group-show]: /cli/azure/ad/group#az-ad-group-show
