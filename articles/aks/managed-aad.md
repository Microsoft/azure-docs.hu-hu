---
title: Az Azure AD használata az Azure Kubernetes szolgáltatásban
description: Ismerje meg, hogyan használhatja az Azure AD-t az Azure Kubernetes szolgáltatásban (ak)
services: container-service
ms.topic: article
ms.date: 02/1/2021
ms.author: miwithro
ms.openlocfilehash: 0e912de4cf3a9759abe4cb3df78255c0a9ba1557
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/08/2021
ms.locfileid: "107105864"
---
# <a name="aks-managed-azure-active-directory-integration"></a>AK által felügyelt Azure Active Directory integráció

Az AK által felügyelt Azure AD-integráció az Azure AD integrációs felületének egyszerűsítésére szolgál, ahol a felhasználóknak korábban egy ügyfélalkalmazás, egy kiszolgálói alkalmazás létrehozása és az Azure AD-bérlő szükséges a címtár-olvasási engedélyek megadásához. Az új verzióban az AK erőforrás-szolgáltató kezeli az ügyfél-és kiszolgálói alkalmazásokat.

## <a name="azure-ad-authentication-overview"></a>Az Azure AD-hitelesítés áttekintése

A Kubernetes szerepköralapú hozzáférés-vezérlést (Kubernetes RBAC) konfigurálhat a felhasználó identitása vagy a címtár csoportjának tagsága alapján. Az Azure AD-hitelesítés az OpenID-kapcsolattal rendelkező AK-fürtökhöz van megadva. Az OpenID Connect egy OAuth 2,0 protokollra épülő identitási réteg. Az OpenID Connecttel kapcsolatos további információkért tekintse meg az [ID Connect dokumentációját][open-id-connect].

További információ az Azure AD integrációs folyamatáról a [Azure Active Directory Integration Concepts dokumentációjában](concepts-identity.md#azure-ad-integration).

## <a name="limitations"></a>Korlátozások 

* Az AK által felügyelt Azure AD-integráció nem tiltható le.
* Az AK által felügyelt Azure AD-beli integrált fürt örökölt HRE való módosítása nem támogatott
* a nem Kubernetes RBAC-kompatibilis fürtök nem támogatottak az AK által felügyelt Azure AD-integrációhoz
* Az AK által felügyelt Azure AD-integrációhoz társított Azure AD-bérlő módosítása nem támogatott

## <a name="prerequisites"></a>Előfeltételek

* Az Azure CLI verziója 2.11.0 vagy újabb verzió
* Kubectl a [1.18.1](https://github.com/kubernetes/kubernetes/blob/master/CHANGELOG/CHANGELOG-1.18.md#v1181) vagy a [kubelogin](https://github.com/Azure/kubelogin) minimális verziójával
* Ha a [helmot](https://github.com/helm/helm)használja, a Helm 3,3 minimális verzióját kell használnia.

> [!Important]
> A Kubectl-t a 1.18.1 vagy a kubelogin minimális verziójával kell használni. A Kubernetes és a kubectl másodlagos verziói közötti különbség nem lehet nagyobb, mint 1 verzió. Ha nem a megfelelő verziót használja, a rendszer a hitelesítési problémákat észleli.

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

## <a name="configure-just-in-time-cluster-access-with-azure-ad-and-aks"></a>Igény szerinti fürthöz való hozzáférés konfigurálása az Azure AD-vel és az AK-val

A fürt hozzáférés-vezérlésének egy másik lehetősége a Privileged Identity Management (PIM) használata az igény szerinti kérelmekhez.

>[!NOTE]
> A PIM egy olyan prémium szintű Azure AD-képesség, amely prémium P2 SKU-t igényel. Az Azure AD SKU-ról további információt a [díjszabási útmutatóban][aad-pricing]talál.

Ha az egyidejű hozzáférési kérelmeket egy AK-fürthöz tartozó, AK által felügyelt Azure AD-integrációval szeretné integrálni, hajtsa végre a következő lépéseket:

1. A Azure Portal tetején keresse meg és válassza a Azure Active Directory lehetőséget.
1. Jegyezze fel a bérlő AZONOSÍTÓját, amelyet a többi, a webböngészőben található utasításhoz hasonlóan a `<tenant-id>` :::image type="content" source="./media/managed-aad/jit-get-tenant-id.png" alt-text="Azure Portal képernyő jelenik meg Azure Active Directory a bérlő azonosítójának kiemelése.":::
1. A bal oldali Azure Active Directory menüjében válassza a *csoportok* kiválasztása, majd az *új csoport* *lehetőséget.*
    :::image type="content" source="./media/managed-aad/jit-create-new-group.png" alt-text="Megjeleníti a Azure Portal Active Directory csoportok képernyőt, ahol az &quot;új csoport&quot; lehetőség ki van emelve.":::
1. Győződjön meg arról, hogy a csoport *biztonsági* típusa van kiválasztva, és adjon meg egy csoportnevet, például *myJITGroup*. Az *Azure ad-szerepkörökhöz rendelhető hozzá ehhez a csoporthoz (előzetes verzió)*, majd válassza az *Igen* lehetőséget. Végül válassza a *Létrehozás* lehetőséget.
    :::image type="content" source="./media/managed-aad/jit-new-group-created.png" alt-text="Megjeleníti a Azure Portal új csoport létrehozási képernyőjét.":::
1. A *csoportok* lapra kerül vissza. Válassza ki az újonnan létrehozott csoportot, és jegyezze fel az objektum AZONOSÍTÓját, amelyet a többi utasításhoz is a következőhöz utal `<object-id>` .
    :::image type="content" source="./media/managed-aad/jit-get-object-id.png" alt-text="Megjeleníti az imént létrehozott csoport Azure Portal képernyőjét, kiemelve az objektum azonosítóját":::
1. Helyezzen üzembe egy AK-t használó Azure AD-integrációval rendelkező AK-fürtöt a `<tenant-id>` `<object-id>` korábbi verziók és értékek használatával:
    ```azurecli-interactive
    az aks create -g myResourceGroup -n myManagedCluster --enable-aad --aad-admin-group-object-ids <object-id> --aad-tenant-id <tenant-id>
    ```
1. A Azure Portal vissza a bal oldali *tevékenység* menüjében válassza a *privilegizált hozzáférés (előzetes verzió)* lehetőséget, és válassza a *privilegizált hozzáférés engedélyezése* lehetőséget.
    :::image type="content" source="./media/managed-aad/jit-enabling-priv-access.png" alt-text="Megjelenik a Azure Portal privilegizált hozzáférésének (előzetes verzió) lapja, a &quot;privilegizált hozzáférés engedélyezése&quot; kiemelve":::
1. Válassza a *hozzárendelések hozzáadása* lehetőséget a hozzáférés megkezdéséhez.
    :::image type="content" source="./media/managed-aad/jit-add-active-assignment.png" alt-text="Az engedélyezés után a Azure Portal emelt szintű hozzáférés (előzetes verzió) képernyője jelenik meg. A &quot;hozzárendelések hozzáadása&quot; lehetőség ki van emelve.":::
1. Válassza ki a *tag* szerepkört, és válassza ki azokat a felhasználókat és csoportokat, akik számára engedélyezni kívánja a fürt elérését. Ezek a hozzárendelések bármikor módosíthatók a csoport rendszergazdája által. Ha készen áll a beléptetésre, válassza a *tovább* lehetőséget.
    :::image type="content" source="./media/managed-aad/jit-adding-assignment.png" alt-text="Megjelenik a Azure Portal-hozzárendelések hozzáadása képernyő, amelynek a mintája tagként való hozzáadásra van kiválasztva. A Next (tovább) lehetőség ki van emelve.":::
1. Válasszon egy *aktív* hozzárendelési típust, a kívánt időtartamot, és adjon meg egy indoklást. Ha készen áll a folytatásra, válassza a *hozzárendelés* lehetőséget. További információ a hozzárendelési típusokról: [jogosultsági szintű hozzáférési csoport (előzetes verzió) Kiosztása Privileged Identity Managementban][aad-assignments].
    :::image type="content" source="./media/managed-aad/jit-set-active-assignment-details.png" alt-text="Megjelenik a Azure Portal hozzárendelések hozzáadása beállítás képernyője. Egy &quot;aktív&quot; hozzárendelési típus van kiválasztva, és egy minta-indoklás lett megadva. A &quot;hozzárendelés&quot; beállítás ki van emelve.":::

A hozzárendelések elvégzése után ellenőrizze, hogy az igény szerinti hozzáférés működik-e a fürt eléréséhez. Például:

```azurecli-interactive
 az aks get-credentials --resource-group myResourceGroup --name myManagedCluster
```

Kövesse a bejelentkezés lépéseit.

A `kubectl get nodes` parancs használatával tekintse meg a fürt csomópontjait:

```azurecli-interactive
kubectl get nodes
```

Jegyezze fel a hitelesítési követelményt, és kövesse a lépéseket a hitelesítéshez. Ha a művelet sikeres, a következőhöz hasonló kimenetnek kell megjelennie:

```output
To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code AAAAAAAAA to authenticate.
NAME                                STATUS   ROLES   AGE     VERSION
aks-nodepool1-61156405-vmss000000   Ready    agent   6m36s   v1.18.14
aks-nodepool1-61156405-vmss000001   Ready    agent   6m42s   v1.18.14
aks-nodepool1-61156405-vmss000002   Ready    agent   6m33s   v1.18.14
```

### <a name="troubleshooting"></a>Hibaelhárítás

Ha `kubectl get nodes` a a következőhöz hasonló hibaüzenetet ad vissza:

```output
Error from server (Forbidden): nodes is forbidden: User "aaaa11111-11aa-aa11-a1a1-111111aaaaa" cannot list resource "nodes" in API group "" at the cluster scope
```

Győződjön meg arról, hogy a biztonsági csoport rendszergazdája *aktív* hozzárendelést adott a fiókhoz.

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
[aad-pricing]: https://azure.microsoft.com/pricing/details/active-directory/

<!-- LINKS - Internal -->
[aad-conditional-access]: ../active-directory/conditional-access/overview.md
[azure-rbac-integration]: manage-azure-rbac.md
[aks-concepts-identity]: concepts-identity.md
[azure-ad-rbac]: azure-ad-rbac.md
[az-aks-create]: /cli/azure/aks#az-aks-create
[az-aks-get-credentials]: /cli/azure/aks#az-aks-get-credentials
[az-group-create]: /cli/azure/group#az-group-create
[open-id-connect]:../active-directory/develop/v2-protocols-oidc.md
[az-ad-user-show]: /cli/azure/ad/user#az-ad-user-show
[rbac-authorization]: concepts-identity.md#role-based-access-controls-rbac
[operator-best-practices-identity]: operator-best-practices-identity.md
[azure-ad-rbac]: azure-ad-rbac.md
[azure-ad-cli]: azure-ad-integration-cli.md
[access-cluster]: #access-an-azure-ad-enabled-cluster
[aad-migrate]: #upgrading-to-aks-managed-azure-ad-integration
[aad-assignments]: ../active-directory/privileged-identity-management/groups-assign-member-owner.md#assign-an-owner-or-member-of-a-group
