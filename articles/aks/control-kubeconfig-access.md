---
title: A kubeconfig elérésének korlátozása Azure Kubernetes Service (AKS)
description: Megtudhatja, hogyan szabályozhatja a Kubernetes konfigurációs fájlhoz (kubeconfig) való hozzáférést a fürt rendszergazdái és a fürt felhasználói számára
services: container-service
ms.topic: article
ms.date: 05/06/2020
ms.openlocfilehash: 279ca9800d7d721cc2e77d269cb577d8bd166d41
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107765556"
---
# <a name="use-azure-role-based-access-control-to-define-access-to-the-kubernetes-configuration-file-in-azure-kubernetes-service-aks"></a>Az Azure szerepköralapú hozzáférés-vezérlésének használata a Kubernetes konfigurációs fájlhoz való hozzáférés meghatározásához a Azure Kubernetes Service (AKS) szolgáltatásban

A Kubernetes-fürtök az eszközzel `kubectl` kommunikálhatnak. Az Azure CLI egyszerű lehetőséget biztosít a hozzáférési hitelesítő adatok és konfigurációs adatok lekért eléréséhez az AKS-fürtökhöz való csatlakozáshoz a `kubectl` használatával. Az Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC) használatával korlátozhatja, hogy ki férhet hozzá a Kubernetes-konfigurációhoz *(kubeconfig)* és korlátozhatja az engedélyeket.

Ez a cikk bemutatja, hogyan rendelhet hozzá olyan Azure-szerepköröket, amelyek korlátozzák, hogy ki kaphat konfigurációs információkat egy AKS-fürthöz.

## <a name="before-you-begin"></a>Előkészületek

Ez a cikk feltételezi, hogy már van egy meglévő AKS-fürt. Ha AKS-fürtre van szüksége, tekintse meg az AKS rövid útmutatóját az [Azure CLI][aks-quickstart-cli] vagy a [Azure Portal.][aks-quickstart-portal]

Ehhez a cikkhez az Azure CLI 2.0.65-ös vagy újabb verziójára is szükség van. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése][azure-cli-install].

## <a name="available-cluster-roles-permissions"></a>Elérhető fürtszerepk szerepkörök engedélyei

Ha az eszközzel kommunikál egy AKS-fürtöt, a rendszer egy konfigurációs fájlt használ, amely meghatározza a fürt `kubectl` kapcsolati adatait. Ezt a konfigurációs fájlt általában *a ~/.kube/config fájl tárolja.* Ebben a *kubeconfig-fájlban* több fürt is definiálható. A fürtök között a [kubectl config use-context paranccsal válthat.][kubectl-config-use-context]

Az [az aks get-credentials][az-aks-get-credentials] paranccsal lekérte az AKS-fürt hozzáférési hitelesítő adatait, és egyesítheti azokat a *kubeconfig fájlban.* Az Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC) használatával szabályozhatja a hitelesítő adatokhoz való hozzáférést. Ezekkel az Azure-szerepkörökkel meghatározhatja, hogy ki és milyen engedélyekkel rendelkezik majd a fürtön belül a *Kubeconfig-fájl* lekéréséhez.

A két beépített szerepkör a következő:

* **Azure Kubernetes Service fürt rendszergazdai szerepköre**  
  * Hozzáférést biztosít a *Microsoft.ContainerService/managedClusters/listClusterAdminCredential/action* API-híváshoz. Ez az [API-hívás listázza a fürt rendszergazdai hitelesítő adatait.][api-cluster-admin]
  * Letölti a *kubeconfig-et* a *clusterAdmin szerepkörhöz.*
* **Azure Kubernetes Service felhasználói szerepkör létrehozása**
  * Hozzáférést biztosít a *Microsoft.ContainerService/managedClusters/listClusterUserCredential/action* API-híváshoz. Ez az [API-hívás listázza a fürt felhasználói hitelesítő adatait.][api-cluster-user]
  * Letölti a *kubeconfig for* *clusterUser* szerepkört.

Ezek az Azure-szerepkörök alkalmazhatók egy Azure Active Directory (AD) felhasználóra vagy csoportra.

> [!NOTE]
> Az Azure AD-t használó fürtökön a *clusterUser* szerepkört használó felhasználók egy üres *kubeconfig* fájllal kérik a bejelentkezést. Bejelentkezés után a felhasználók az Azure AD felhasználói vagy csoportbeállításaik alapján férhetnek hozzá. A *fürtAdmin szerepkörű felhasználók rendszergazdai* hozzáféréssel rendelkezik.
>
> Azok a fürtök, amelyek nem használják az Azure AD-t, csak *a clusterAdmin szerepkört* használják.

## <a name="assign-role-permissions-to-a-user-or-group"></a>Szerepkör-engedélyek hozzárendelése felhasználóhoz vagy csoporthoz

Az elérhető szerepkörök egyikének hozzárendeléséhez le kell szereznie az AKS-fürt erőforrás-azonosítóját és az Azure AD-felhasználói fiók vagy -csoport azonosítóját. Az alábbi példaparancsok:

* A *myResourceGroup* erőforráscsoportban a *myAKSCluster* nevű fürt [az aks show][az-aks-show] parancsával szerezze be a fürt erőforrás-azonosítóját. Szükség szerint adja meg a saját fürt- és erőforráscsoport-nevét.
* A felhasználói [azonosítót az az account show és][az-account-show] az az [ad user show][az-ad-user-show] paranccsal használhatja.
* Végül rendeljen hozzá egy szerepkört [az az role assignment create paranccsal.][az-role-assignment-create]

A következő példa hozzárendeli *a Azure Kubernetes Service-rendszergazdai szerepkört* egy egyéni felhasználói fiókhoz:

```azurecli-interactive
# Get the resource ID of your AKS cluster
AKS_CLUSTER=$(az aks show --resource-group myResourceGroup --name myAKSCluster --query id -o tsv)

# Get the account credentials for the logged in user
ACCOUNT_UPN=$(az account show --query user.name -o tsv)
ACCOUNT_ID=$(az ad user show --id $ACCOUNT_UPN --query objectId -o tsv)

# Assign the 'Cluster Admin' role to the user
az role assignment create \
    --assignee $ACCOUNT_ID \
    --scope $AKS_CLUSTER \
    --role "Azure Kubernetes Service Cluster Admin Role"
```

> [!IMPORTANT]
> Bizonyos esetekben a *user.name* neve eltér a *userPrincipalName* névtől, például Az Azure AD vendégfelhasználói esetén:
>
> ```output
> $ az account show --query user.name -o tsv
> user@contoso.com
> $ az ad user list --query "[?contains(otherMails,'user@contoso.com')].{UPN:userPrincipalName}" -o tsv
> user_contoso.com#EXT#@contoso.onmicrosoft.com
> ```
>
> Ebben az esetben állítsa  be a ACCOUNT_UPN értékét az Azure AD-felhasználó *userPrincipalName* értékével. Ha például a fiók *user.name* *felhasználó \@ contoso.com:*
> 
> ```azurecli-interactive
> ACCOUNT_UPN=$(az ad user list --query "[?contains(otherMails,'user@contoso.com')].{UPN:userPrincipalName}" -o tsv)
> ```

> [!TIP]
> Ha engedélyeket szeretne hozzárendelni egy Azure AD-csoporthoz, frissítse az előző példában látható paramétert a felhasználó helyett a csoport `--assignee`  *objektumazonosítójával.* Egy csoport objektumazonosítójának beszerzéséhez használja az [az ad group show][az-ad-group-show] parancsot. Az alábbi példa lekérte az appdev nevű Azure AD-csoport *objektumazonosítóját:*`az ad group show --group appdev --query objectId -o tsv`

Az előző hozzárendelést szükség szerint módosíthatja a Fürt felhasználói *szerepköréhez.*

Az alábbi példakimenet azt mutatja, hogy a szerepkör-hozzárendelés sikeresen létrejött:

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

## <a name="get-and-verify-the-configuration-information"></a>A konfigurációs adatok lekért és ellenőrzése

Az Azure-szerepkörök hozzárendelése után az [az aks get-credentials][az-aks-get-credentials] paranccsal lekérte az AKS-fürt *kubeconfig-definícióját.* Az alábbi példa lekért *--admin hitelesítő* adatokat, amelyek akkor működnek megfelelően, ha a felhasználó fürt rendszergazdai *szerepkört kapott:*

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster --admin
```

Ezután a [kubectl config view paranccsal][kubectl-config-view]  ellenőrizheti, hogy a fürt környezetében megjelenik-e a rendszergazdai konfigurációs adatok alkalmazása:

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

## <a name="remove-role-permissions"></a>Szerepkör-engedélyek eltávolítása

A szerepkör-hozzárendelések eltávolításához használja [az az role assignment delete][az-role-assignment-delete] parancsot. Adja meg a fiókazonosítót és a fürterőforrás-azonosítót az előző parancsokban kapottak szerint. Ha a szerepkört nem felhasználóhoz, hanem csoporthoz rendelte, adja meg a megfelelő csoportobjektum azonosítóját a paraméter fiókobjektum-azonosítója `--assignee` helyett:

```azurecli-interactive
az role assignment delete --assignee $ACCOUNT_ID --scope $AKS_CLUSTER
```

## <a name="next-steps"></a>Következő lépések

Az AKS-fürtökhöz való hozzáférés fokozott biztonsága érdekében [integrálja a Azure Active Directory hitelesítést.][aad-integration]

<!-- LINKS - external -->
[kubectl-config-use-context]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#config
[kubectl-config-view]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#config

<!-- LINKS - internal -->
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[azure-cli-install]: /cli/azure/install-azure-cli
[az-aks-get-credentials]: /cli/azure/aks#az_aks_get_credentials
[azure-rbac]: ../role-based-access-control/overview.md
[api-cluster-admin]: /rest/api/aks/managedclusters/listclusteradmincredentials
[api-cluster-user]: /rest/api/aks/managedclusters/listclusterusercredentials
[az-aks-show]: /cli/azure/aks#az_aks_show
[az-account-show]: /cli/azure/account#az_account_show
[az-ad-user-show]: /cli/azure/ad/user#az_ad_user_show
[az-role-assignment-create]: /cli/azure/role/assignment#az_role_assignment_create
[az-role-assignment-delete]: /cli/azure/role/assignment#az_role_assignment_delete
[aad-integration]: ./azure-ad-integration-cli.md
[az-ad-group-show]: /cli/azure/ad/group#az_ad_group_show
