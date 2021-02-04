---
title: Az Azure AD használata az Azure Kubernetes szolgáltatásban
description: Ismerje meg, hogyan használhatja az Azure AD-t az Azure Kubernetes szolgáltatásban (ak)
services: container-service
ms.topic: article
ms.date: 02/1/2021
ms.author: miwithro
ms.openlocfilehash: 7f6cf503a459175e3109a515b666bbeaa3a25b4d
ms.sourcegitcommit: 5b926f173fe52f92fcd882d86707df8315b28667
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/04/2021
ms.locfileid: "99549999"
---
# <a name="aks-managed-azure-active-directory-integration"></a>AK által felügyelt Azure Active Directory integráció

Az AK által felügyelt Azure AD-integráció az Azure AD integrációs felületének egyszerűsítésére szolgál, ahol a felhasználóknak korábban egy ügyfélalkalmazás, egy kiszolgálói alkalmazás létrehozása és az Azure AD-bérlő szükséges a címtár-olvasási engedélyek megadásához. Az új verzióban az AK erőforrás-szolgáltató kezeli az ügyfél-és kiszolgálói alkalmazásokat.

## <a name="azure-ad-authentication-overview"></a>Az Azure AD-hitelesítés áttekintése

A Kubernetes szerepköralapú hozzáférés-vezérlést (Kubernetes RBAC) konfigurálhat a felhasználó identitása vagy a címtár csoportjának tagsága alapján. Az Azure AD-hitelesítés az OpenID-kapcsolattal rendelkező AK-fürtökhöz van megadva. Az OpenID Connect egy OAuth 2,0 protokollra épülő identitási réteg. Az OpenID Connecttel kapcsolatos további információkért tekintse meg az [ID Connect dokumentációját][open-id-connect].

További információ az Azure AD integrációs folyamatáról a [Azure Active Directory Integration Concepts dokumentációjában](concepts-identity.md#azure-active-directory-integration).

## <a name="limitations"></a>Korlátozások 

* Az AK által felügyelt Azure AD-integráció nem tiltható le.
* a nem Kubernetes RBAC-kompatibilis fürtök nem támogatottak az AK által felügyelt Azure AD-integrációhoz
* Az AK által felügyelt Azure AD-integrációhoz társított Azure AD-bérlő módosítása nem támogatott

## <a name="prerequisites"></a>Előfeltételek

* Az Azure CLI verziója 2.11.0 vagy újabb verzió
* Kubectl a [1.18.1](https://github.com/kubernetes/kubernetes/blob/master/CHANGELOG/CHANGELOG-1.18.md#v1181) vagy a [kubelogin](https://github.com/Azure/kubelogin) minimális verziójával
* Ha a [helmot](https://github.com/helm/helm)használja, a Helm 3,3 minimális verzióját kell használnia.

> [!Important]
> A Kubectl-t a 1.18.1 vagy a kubelogin minimális verziójával kell használni. Ha nem a megfelelő verziót használja, a rendszer a hitelesítési problémákat észleli.

A kubectl és a kubelogin telepítéséhez használja a következő parancsokat:

```azurecli-interactive
sudo az aks install-cli
kubectl version --client
kubelogin --version
```

[Ezeket az utasításokat](https://kubernetes.io/docs/tasks/tools/install-kubectl/) más operációs rendszerekhez használhatja.

## <a name="before-you-begin"></a>Előkészületek

A fürthöz Azure AD-csoportra van szükség. Erre a csoportra rendszergazdai csoportként van szükség a fürt rendszergazdai engedélyeinek biztosításához. Használhat meglévő Azure AD-csoportot, vagy létrehozhat egy újat. Jegyezze fel az Azure AD-csoport objektum-AZONOSÍTÓját.

```azurecli-interactive
# List existing groups in the directory
az ad group list --filter "displayname eq '<group-name>'" -o table
```

Ha új Azure AD-csoportot szeretne létrehozni a fürt rendszergazdájához, használja a következő parancsot:

```azurecli-interactive
# Create an Azure AD group
az ad group create --display-name myAKSAdminGroup --mail-nickname myAKSAdminGroup
```

## <a name="create-an-aks-cluster-with-azure-ad-enabled"></a>AK-fürt létrehozása az Azure AD-vel engedélyezve

Hozzon létre egy AK-fürtöt az alábbi CLI-parancsok használatával.

Azure-erőforráscsoport létrehozása:

```azurecli-interactive
# Create an Azure resource group
az group create --name myResourceGroup --location centralus
```

AK-fürt létrehozása és rendszergazdai hozzáférés engedélyezése az Azure AD-csoport számára

```azurecli-interactive
# Create an AKS-managed Azure AD cluster
az aks create -g myResourceGroup -n myManagedCluster --enable-aad --aad-admin-group-object-ids <id> [--aad-tenant-id <id>]
```

Az AK által felügyelt Azure AD-fürtök sikeres létrehozása a válasz törzsének következő szakasza.
```output
"AADProfile": {
    "adminGroupObjectIds": [
      "5d24****-****-****-****-****afa27aed"
    ],
    "clientAppId": null,
    "managed": true,
    "serverAppId": null,
    "serverAppSecret": null,
    "tenantId": "72f9****-****-****-****-****d011db47"
  }
```

A fürt létrehozása után megkezdheti a hozzáférését.

## <a name="access-an-azure-ad-enabled-cluster"></a>Hozzáférés egy Azure AD-kompatibilis fürthöz

A következő lépések elvégzéséhez szüksége lesz az [Azure Kubernetes Service cluster felhasználói](../role-based-access-control/built-in-roles.md#azure-kubernetes-service-cluster-user-role) beépített szerepkörre.

A fürt eléréséhez szükséges felhasználói hitelesítő adatok beszerzése:
 
```azurecli-interactive
 az aks get-credentials --resource-group myResourceGroup --name myManagedCluster
```
A bejelentkezéshez kövesse az utasításokat.

A fürt csomópontjainak megtekintéséhez használja a kubectl Get Nodes parancsot:

```azurecli-interactive
kubectl get nodes

NAME                       STATUS   ROLES   AGE    VERSION
aks-nodepool1-15306047-0   Ready    agent   102m   v1.15.10
aks-nodepool1-15306047-1   Ready    agent   102m   v1.15.10
aks-nodepool1-15306047-2   Ready    agent   102m   v1.15.10
```
Konfigurálja az [Azure szerepköralapú hozzáférés-vezérlést (Azure RBAC)](./azure-ad-rbac.md) további biztonsági csoportok konfigurálásához a fürtökhöz.

## <a name="troubleshooting-access-issues-with-azure-ad"></a>Az Azure AD hozzáférési problémáinak elhárítása

> [!Important]
> Az alábbiakban ismertetett lépések megkerülik a normál Azure AD-csoport hitelesítését. Csak vészhelyzetben használható.

Ha véglegesen letiltja azt, hogy nem fér hozzá a fürthöz hozzáféréssel rendelkező érvényes Azure AD-csoporthoz, akkor továbbra is beszerezheti a rendszergazdai hitelesítő adatokat a fürt közvetlen eléréséhez.

A lépések végrehajtásához hozzá kell férnie az [Azure Kubernetes szolgáltatás-fürt rendszergazdai](../role-based-access-control/built-in-roles.md#azure-kubernetes-service-cluster-admin-role) beépített szerepköréhez.

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myManagedCluster --admin
```

## <a name="enable-aks-managed-azure-ad-integration-on-your-existing-cluster"></a>Az AK által felügyelt Azure AD-integráció engedélyezése a meglévő fürtön

Engedélyezheti az AK által felügyelt Azure AD-integrációt a meglévő Kubernetes RBAC-kompatibilis fürtön. Győződjön meg arról, hogy a felügyeleti csoport megtartja a hozzáférést a fürtön.

```azurecli-interactive
az aks update -g MyResourceGroup -n MyManagedCluster --enable-aad --aad-admin-group-object-ids <id-1> [--aad-tenant-id <id>]
```

Az AK által felügyelt Azure AD-fürt sikeres aktiválása a következő szakaszt tartalmazza a válasz törzsében:

```output
"AADProfile": {
    "adminGroupObjectIds": [
      "5d24****-****-****-****-****afa27aed"
    ],
    "clientAppId": null,
    "managed": true,
    "serverAppId": null,
    "serverAppSecret": null,
    "tenantId": "72f9****-****-****-****-****d011db47"
  }
```

Töltse le újra a felhasználói hitelesítő adatokat a fürt eléréséhez a [következő lépések][access-cluster]végrehajtásával.

## <a name="upgrading-to-aks-managed-azure-ad-integration"></a>Frissítés az AK által felügyelt Azure AD-integrációra

Ha a fürt örökölt Azure AD-integrációt használ, frissíthet az AK által felügyelt Azure AD-integrációra.

```azurecli-interactive
az aks update -g myResourceGroup -n myManagedCluster --enable-aad --aad-admin-group-object-ids <id> [--aad-tenant-id <id>]
```

Egy AK által felügyelt Azure AD-fürt sikeres áttelepítése a következő szakaszt tartalmazza a válasz törzsében:

```output
"AADProfile": {
    "adminGroupObjectIds": [
      "5d24****-****-****-****-****afa27aed"
    ],
    "clientAppId": null,
    "managed": true,
    "serverAppId": null,
    "serverAppSecret": null,
    "tenantId": "72f9****-****-****-****-****d011db47"
  }
```

Ha el szeretné érni a fürtöt, kövesse az alábbi [lépéseket.][access-cluster]

## <a name="non-interactive-sign-in-with-kubelogin"></a>Nem interaktív bejelentkezés a kubelogin

Vannak olyan nem interaktív forgatókönyvek, mint például a folyamatos integrációs folyamatok, amelyek jelenleg nem érhetők el a kubectl. [`kubelogin`](https://github.com/Azure/kubelogin)A paranccsal hozzáférhet a fürthöz nem interaktív egyszerű szolgáltatás-bejelentkezéssel.

## <a name="use-conditional-access-with-azure-ad-and-aks"></a>Feltételes hozzáférés használata az Azure AD-vel és az AK-val

Ha az Azure AD-t az AK-fürthöz integrálja, a [feltételes hozzáférést][aad-conditional-access] is használhatja a fürthöz való hozzáférés szabályozására.

> [!NOTE]
> Az Azure AD feltételes hozzáférés prémium szintű Azure AD képesség.

A következő lépések végrehajtásával hozhat létre például egy olyan feltételes hozzáférési szabályzatot, amelyet AK-val kell használni:

1. A Azure Portal tetején keresse meg és válassza a Azure Active Directory lehetőséget.
1. A bal oldali Azure Active Directory menüjében válassza a *vállalati alkalmazások* lehetőséget.
1. A bal oldali vállalati alkalmazások menüjében válassza a *feltételes hozzáférés* lehetőséget.
1. A bal oldali feltételes hozzáférés menüben válassza a *házirendek* , majd az *új* szabályzat lehetőséget.
    :::image type="content" source="./media/managed-aad/conditional-access-new-policy.png" alt-text="Feltételes hozzáférési szabályzat hozzáadása":::
1. Adja meg a szabályzat nevét, például: *AK-Policy*.
1. Válassza a *felhasználók és csoportok* lehetőséget, majd a *Belefoglalás* területen válassza a *felhasználók és csoportok kiválasztása* lehetőséget. Válassza ki azokat a felhasználókat és csoportokat, amelyeken alkalmazni szeretné a szabályzatot. Ehhez a példához válassza ki ugyanazt az Azure AD-csoportot, amely rendszergazdai hozzáféréssel rendelkezik a fürthöz.
    :::image type="content" source="./media/managed-aad/conditional-access-users-groups.png" alt-text="Felhasználók vagy csoportok kiválasztása a feltételes hozzáférési szabályzat alkalmazásához":::
1. Válassza a *felhőalapú alkalmazások vagy műveletek* lehetőséget, majd a *Belefoglalás* területen válassza az *alkalmazások kiválasztása* lehetőséget. Keresse meg az *Azure Kubernetes szolgáltatást* , és válassza az *Azure Kubernetes Service HRE Server* lehetőséget.
    :::image type="content" source="./media/managed-aad/conditional-access-apps.png" alt-text="Az Azure Kubernetes Service AD Server kiválasztása a feltételes hozzáférési szabályzat alkalmazásához":::
1. A *Hozzáférés-vezérlés* alatt válassza ki az *Engedélyezés* elemet. Válassza a *hozzáférés engedélyezése* lehetőséget, majd az *eszköz megfelelőként való megjelölésének megkövetelése* beállítást.
    :::image type="content" source="./media/managed-aad/conditional-access-grant-compliant.png" alt-text="Csak a megfelelő eszközök engedélyezése a feltételes hozzáférési házirendhez":::
1. A *házirend engedélyezése* területen válassza *a* be, majd a *Létrehozás* lehetőséget.
    :::image type="content" source="./media/managed-aad/conditional-access-enable-policy.png" alt-text="A feltételes hozzáférési szabályzat engedélyezése":::

Szerezze be a fürt eléréséhez szükséges felhasználói hitelesítő adatokat, például:

```azurecli-interactive
 az aks get-credentials --resource-group myResourceGroup --name myManagedCluster
```

A bejelentkezéshez kövesse az utasításokat.

A `kubectl get nodes` parancs használatával tekintse meg a fürt csomópontjait:

```azurecli-interactive
kubectl get nodes
```

Az utasításokat követve jelentkezzen be újra. Figyelje meg, hogy a rendszer sikeresen bejelentkezett, de a rendszergazdának szüksége van arra, hogy az Azure AD által felügyelt eszköz hozzáférjen az erőforráshoz.

A Azure Portal navigáljon a Azure Active Directory elemre, válassza a *vállalati alkalmazások* lehetőséget, majd a *tevékenység* területen válassza a *bejelentkezések* lehetőséget. Figyelje *meg, hogy* a felül található bejegyzés *sikertelen* , és a sikeres *feltételes hozzáférés* .  Válassza ki a bejegyzést, majd válassza a *feltételes hozzáférés* lehetőséget a *részletek* területen. Figyelje meg, hogy a feltételes hozzáférési szabályzata megjelenik.

:::image type="content" source="./media/managed-aad/conditional-access-sign-in-activity.png" alt-text="Sikertelen bejelentkezési bejegyzés a feltételes hozzáférési szabályzat miatt":::

## <a name="next-steps"></a>Következő lépések

* Tudnivalók az [Azure RBAC-integrációról a Kubernetes-hitelesítéshez][azure-rbac-integration]
* Ismerje meg az [Azure ad-integrációt a KUBERNETES RBAC][azure-ad-rbac].
* A [kubelogin](https://github.com/Azure/kubelogin) használatával férhet hozzá az Azure-hitelesítés olyan szolgáltatásaihoz, amelyek nem érhetők el a kubectl-ben.
* További információ az [AK-ról és a Kubernetes-identitással kapcsolatos fogalmakról][aks-concepts-identity].
* A [Azure Resource Manager-(ARM-) Sablonok ][aks-arm-template] használatával hozzon létre AK által felügyelt Azure ad-kompatibilis fürtöket.

<!-- LINKS - external -->
[kubernetes-webhook]:https://kubernetes.io/docs/reference/access-authn-authz/authentication/#webhook-token-authentication
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[aks-arm-template]: /azure/templates/microsoft.containerservice/managedclusters

<!-- LINKS - Internal -->
[aad-conditional-access]: ../active-directory/conditional-access/overview.md
[azure-rbac-integration]: manage-azure-rbac.md
[aks-concepts-identity]: concepts-identity.md
[azure-ad-rbac]: azure-ad-rbac.md
[az-aks-create]: /cli/azure/aks?view=azure-cli-latest#az-aks-create
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[az-group-create]: /cli/azure/group#az-group-create
[open-id-connect]:../active-directory/develop/v2-protocols-oidc.md
[az-ad-user-show]: /cli/azure/ad/user#az-ad-user-show
[rbac-authorization]: concepts-identity.md#role-based-access-controls-rbac
[operator-best-practices-identity]: operator-best-practices-identity.md
[azure-ad-rbac]: azure-ad-rbac.md
[azure-ad-cli]: azure-ad-integration-cli.md
[access-cluster]: #access-an-azure-ad-enabled-cluster
[aad-migrate]: #upgrading-to-aks-managed-azure-ad-integration
