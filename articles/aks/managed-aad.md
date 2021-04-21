---
title: Az Azure AD használata Azure Kubernetes Service
description: Megtudhatja, hogyan használhatja az Azure AD-t Azure Kubernetes Service (AKS)
services: container-service
ms.topic: article
ms.date: 02/1/2021
ms.author: miwithro
ms.openlocfilehash: 3db9f8d895b4c13b5f969859f422e7b566722ffc
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107783070"
---
# <a name="aks-managed-azure-active-directory-integration"></a>Az AKS által felügyelt Azure Active Directory integrációja

Az AKS által felügyelt Azure AD-integráció leegyszerűsíti az Azure AD-integrációt, ahol a felhasználóknak korábban ügyfélalkalmazást és kiszolgálóalkalmazást kellett létrehozniuk, és az Azure AD-bérlőnek címtár olvasási engedélyeket kellett megadnia. Az új verzióban az AKS erőforrás-szolgáltató felügyeli Az ügyfél- és kiszolgálóalkalmazásokat.

## <a name="azure-ad-authentication-overview"></a>Az Azure AD-hitelesítés áttekintése

A fürt rendszergazdái konfigurálhatják a Kubernetes szerepköralapú hozzáférés-vezérlését (Kubernetes RBAC) a felhasználó identitása vagy címtárcsoport-tagsága alapján. Az Azure AD-hitelesítést az AKS-fürtök biztosítják OpenID Connect. OpenID Connect az OAuth 2.0 protokollra épülő identitásréteg. További információt a OpenID Connect Open [ID Connect dokumentációjában talál.][open-id-connect]

Az Azure AD-integrációs folyamatról az integrációs [Azure Active Directory dokumentációjában talál további információt.](concepts-identity.md#azure-ad-integration)

## <a name="limitations"></a>Korlátozások 

* Az AKS által felügyelt Azure AD-integráció nem tiltható le
* Az AKS által felügyelt, integrált Azure AD-fürt örökölt AAD-alapúra módosítása nem támogatott
* A nem Kubernetes RBAC-kompatibilis fürtök nem támogatottak az AKS által felügyelt Azure AD-integrációhoz
* Az AKS által felügyelt Azure AD-integrációhoz társított Azure AD-bérlő módosítása nem támogatott

## <a name="prerequisites"></a>Előfeltételek

* Az Azure CLI 2.11.0-s vagy újabb verziója
* Kubectl legalább [1.18.1-es](https://github.com/kubernetes/kubernetes/blob/master/CHANGELOG/CHANGELOG-1.18.md#v1181) vagy [kubelogin-verzióval](https://github.com/Azure/kubelogin)
* Helm használata esetén a [helm](https://github.com/helm/helm)minimális verziója 3.3.

> [!Important]
> A Kubectl-t legalább 1.18.1-es vagy kubelogin-verzióval kell használnia. A Kubernetes és a kubectl alverziói közötti különbség nem lehet 1-esnél több. Ha nem a megfelelő verziót használja, hitelesítési problémákat fog észre venni.

A kubectl és a kubelogin telepítéséhez használja a következő parancsokat:

```azurecli-interactive
sudo az aks install-cli
kubectl version --client
kubelogin --version
```

Ezeket [az utasításokat más](https://kubernetes.io/docs/tasks/tools/install-kubectl/) operációs rendszerekhez is használhatja.

## <a name="before-you-begin"></a>Előkészületek

A fürthöz szüksége lesz egy Azure AD-csoportra. Erre a csoportra rendszergazdai csoportként van szükség a fürt számára a fürt rendszergazdai engedélyeinek megadásához. Használhat egy meglévő Azure AD-csoportot, vagy létrehozhat egy újat. Rögzítse az Azure AD-csoport objektumazonosítóját.

```azurecli-interactive
# List existing groups in the directory
az ad group list --filter "displayname eq '<group-name>'" -o table
```

A következő paranccsal hozhat létre új Azure AD-csoportot a fürt rendszergazdái számára:

```azurecli-interactive
# Create an Azure AD group
az ad group create --display-name myAKSAdminGroup --mail-nickname myAKSAdminGroup
```

## <a name="create-an-aks-cluster-with-azure-ad-enabled"></a>AKS-fürt létrehozása engedélyezett Azure AD-val

Hozzon létre egy AKS-fürtöt az alábbi parancssori felületi parancsokkal.

Hozzon létre egy Azure-erőforráscsoportot:

```azurecli-interactive
# Create an Azure resource group
az group create --name myResourceGroup --location centralus
```

AKS-fürt létrehozása és felügyeleti hozzáférés engedélyezése az Azure AD-csoport számára

```azurecli-interactive
# Create an AKS-managed Azure AD cluster
az aks create -g myResourceGroup -n myManagedCluster --enable-aad --aad-admin-group-object-ids <id> [--aad-tenant-id <id>]
```

Az AKS által felügyelt Azure AD-fürt sikeres létrehozása a következő szakaszt tartalmazza a válasz törzsében:
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

A fürt létrehozása után hozzá tud férni.

## <a name="access-an-azure-ad-enabled-cluster"></a>Hozzáférés egy Azure AD-kompatibilis fürthöz

A következő lépések éhez [Azure Kubernetes Service fürtfelhasználó](../role-based-access-control/built-in-roles.md#azure-kubernetes-service-cluster-user-role) beépített szerepkörre lesz szüksége.

Szerezze be a felhasználói hitelesítő adatokat a fürt eléréséhez:
 
```azurecli-interactive
 az aks get-credentials --resource-group myResourceGroup --name myManagedCluster
```
A bejelentkezéshez kövesse az utasításokat.

A fürt csomópontjainak megtekintéséhez használja a kubectl get nodes parancsot:

```azurecli-interactive
kubectl get nodes

NAME                       STATUS   ROLES   AGE    VERSION
aks-nodepool1-15306047-0   Ready    agent   102m   v1.15.10
aks-nodepool1-15306047-1   Ready    agent   102m   v1.15.10
aks-nodepool1-15306047-2   Ready    agent   102m   v1.15.10
```
Konfigurálja [az Azure szerepköralapú hozzáférés-vezérlését (Azure RBAC),](./azure-ad-rbac.md) hogy további biztonsági csoportokat konfigurál a fürtökhöz.

## <a name="troubleshooting-access-issues-with-azure-ad"></a>Az Azure AD hozzáférési problémáinak elhárítása

> [!Important]
> Az alábbiakban ismertetett lépések megkerülik az Azure AD-csoport normál hitelesítését. Csak vészhelyzetben használja őket.

Ha véglegesen le van tiltva, mert nem rendelkezik hozzáféréssel a fürthöz hozzáféréssel rendelkező érvényes Azure AD-csoporthoz, akkor is beszerezheti a rendszergazdai hitelesítő adatokat a fürt közvetlen eléréséhez.

A lépések éhez hozzá kell férni az Azure Kubernetes Service fürt rendszergazdája [beépített](../role-based-access-control/built-in-roles.md#azure-kubernetes-service-cluster-admin-role) szerepkörhöz.

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myManagedCluster --admin
```

## <a name="enable-aks-managed-azure-ad-integration-on-your-existing-cluster"></a>Az AKS által felügyelt Azure AD-integráció engedélyezése a meglévő fürtön

Engedélyezheti az AKS által felügyelt Azure AD-integrációt a meglévő Kubernetes RBAC-kompatibilis fürtön. Győződjön meg arról, hogy a rendszergazdai csoport úgy van beállítva, hogy továbbra is hozzáférjen a fürthöz.

```azurecli-interactive
az aks update -g MyResourceGroup -n MyManagedCluster --enable-aad --aad-admin-group-object-ids <id-1> [--aad-tenant-id <id>]
```

Az AKS által felügyelt Azure AD-fürt sikeres aktiválása a válasz törzsének következő szakaszát tartalmazza

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

Töltse le újra a felhasználói hitelesítő adatokat a fürt eléréséhez az itt található lépéseket [követve.][access-cluster]

## <a name="upgrading-to-aks-managed-azure-ad-integration"></a>Frissítés AKS által felügyelt Azure AD-integrációra

Ha a fürt örökölt Azure AD-integrációt használ, frissíthet az AKS által felügyelt Azure AD-integrációra.

```azurecli-interactive
az aks update -g myResourceGroup -n myManagedCluster --enable-aad --aad-admin-group-object-ids <id> [--aad-tenant-id <id>]
```

Az AKS által felügyelt Azure AD-fürt sikeres áttelepítése a válasz törzsének következő szakaszát tartalmazza

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

Ha hozzá szeretne férni a fürthöz, kövesse az itt található [lépéseket.][access-cluster]

## <a name="non-interactive-sign-in-with-kubelogin"></a>Nem interaktív bejelentkezés a kubeloginnal

Vannak nem interaktív forgatókönyvek, például a folyamatos integrációs folyamatok, amelyek jelenleg nem érhetők el a kubectl-hez. A használatával nem interaktív szolgáltatásnév-bejelentkezéssel férhet hozzá a [`kubelogin`](https://github.com/Azure/kubelogin) fürthöz.

## <a name="use-conditional-access-with-azure-ad-and-aks"></a>Feltételes hozzáférés használata az Azure AD-val és az AKS-sel

Az Azure AD és az AKS-fürt [][aad-conditional-access] integrálása esetén feltételes hozzáféréssel is szabályozhatja a fürthöz való hozzáférést.

> [!NOTE]
> Az Azure AD feltételes hozzáférés prémium szintű Azure AD képesség.

Az AKS-sel használható példa feltételes hozzáférési szabályzat létrehozásához kövesse az alábbi lépéseket:

1. A képernyő tetején keresse meg Azure Portal és válassza ki a Azure Active Directory.
1. A bal oldali menüben Azure Active Directory válassza a Vállalati *alkalmazások lehetőséget.*
1. A vállalati alkalmazások menüjében a bal oldalon válassza a Feltételes *hozzáférés lehetőséget.*
1. A feltételes hozzáférés menüjében a bal oldalon válassza a Szabályzatok, majd *az* Új *szabályzat lehetőséget.*
    :::image type="content" source="./media/managed-aad/conditional-access-new-policy.png" alt-text="Feltételes hozzáférési szabályzat hozzáadása":::
1. Adjon nevet a szabályzatnak, például *aks-policy.*
1. Válassza *a Felhasználók és csoportok* lehetőséget, majd a Be *foglalás alatt válassza* a Felhasználók és csoportok kiválasztása *lehetőséget.* Válassza ki a felhasználókat és csoportokat, amelyekre alkalmazni szeretné a szabályzatot. Ebben a példában ugyanazt az Azure AD-csoportot válassza, amely felügyeleti hozzáféréssel rendelkezik a fürthöz.
    :::image type="content" source="./media/managed-aad/conditional-access-users-groups.png" alt-text="Felhasználók vagy csoportok kiválasztása a feltételes hozzáférési szabályzat alkalmazáshoz":::
1. Válassza *a Felhőalkalmazások vagy műveletek* lehetőséget, majd a Be include *(Tartalmazza) alatt válassza* a Select apps *(Alkalmazások kiválasztása) lehetőséget.* Keressen rá a *Azure Kubernetes Service,* és válassza *Azure Kubernetes Service AAD-kiszolgáló lehetőséget.*
    :::image type="content" source="./media/managed-aad/conditional-access-apps.png" alt-text="Az Azure Kubernetes Service AD-kiszolgáló kiválasztása a feltételes hozzáférési szabályzat alkalmazáshoz":::
1. A *Hozzáférés-vezérlés* alatt válassza ki az *Engedélyezés* elemet. Válassza *a Hozzáférés megadása,* majd *az Eszköz megfelelőként való jelölésének megkövetele lehetőséget.*
    :::image type="content" source="./media/managed-aad/conditional-access-grant-compliant.png" alt-text="Ha csak a megfelelő eszközök számára engedélyezi a feltételes hozzáférési szabályzatot,":::
1. A *Szabályzat engedélyezése alatt válassza* a *Be,* majd a *Létrehozás lehetőséget.*
    :::image type="content" source="./media/managed-aad/conditional-access-enable-policy.png" alt-text="A feltételes hozzáférési szabályzat engedélyezése":::

Szerezze be a felhasználói hitelesítő adatokat a fürt eléréséhez, például:

```azurecli-interactive
 az aks get-credentials --resource-group myResourceGroup --name myManagedCluster
```

A bejelentkezéshez kövesse az utasításokat.

A fürt `kubectl get nodes` csomópontjainak megtekintéséhez használja az parancsot:

```azurecli-interactive
kubectl get nodes
```

Az utasításokat követve jelentkezzen be újra. Figyelje meg, hogy megjelenik egy hibaüzenet, amely szerint sikeresen bejelentkezett, de a rendszergazda megköveteli, hogy a hozzáférést kérő eszközt az Azure AD kezelni tudja az erőforrás eléréséhez.

A Azure Portal lépjen a Azure Active Directory, válassza a  Vállalati alkalmazások  lehetőséget, majd a Tevékenység alatt válassza a *Bejelentkezések lehetőséget.* Figyelje meg a felül található  Sikertelen *állapottal* és a Sikeres feltételes *hozzáféréssel.* Válassza ki a bejegyzést, majd *a Részletek mezőben válassza a* Feltételes hozzáférés *lehetőséget.* Figyelje meg, hogy a feltételes hozzáférési szabályzat megjelenik a listában.

:::image type="content" source="./media/managed-aad/conditional-access-sign-in-activity.png" alt-text="A feltételes hozzáférési szabályzat miatt meghiúsult a bejelentkezés":::

## <a name="configure-just-in-time-cluster-access-with-azure-ad-and-aks"></a>Fürtökhöz való hozzáférés konfigurálása az Azure AD és az AKS használatával

A fürt hozzáférés-vezérlésének egy másik lehetősége a Privileged Identity Management (PIM) használata a kérelmekhez.

>[!NOTE]
> A PIM egy prémium szintű Azure AD funkció, amely prémium P2 termékváltozatot igényel. További információ az Azure AD termékcsomagokkalról: [díjszabási útmutató.][aad-pricing]

Az AKS által felügyelt Azure AD-integrációval az AKS-fürtökhöz való, az időben való hozzáférési kérelmek integrálásához kövesse az alábbi lépéseket:

1. Az oldal tetején keresse meg Azure Portal, és válassza a Azure Active Directory.
1. Jegyezze fel a bérlőazonosítót ( más néven a többi útmutatót webböngészőben) a Azure Portal Azure Active Directory képernyője jelenik meg, kiemelt `<tenant-id>` :::image type="content" source="./media/managed-aad/jit-get-tenant-id.png" alt-text="bérlőazonosítóval.":::
1. A bal oldali Azure Active Directory menü Kezelés menüjében válassza  a *Csoportok,* majd az *Új csoport lehetőséget.*
    :::image type="content" source="./media/managed-aad/jit-create-new-group.png" alt-text="Megjeleníti a Azure Portal Active Directory csoportok képernyőt, kiemelt &quot;Új csoport&quot; lehetőséggel.":::
1. Győződjön meg arról, hogy a Csoport típusa *biztonsági beállítás* van kiválasztva, és adjon meg egy csoportnevet, *például: myJITGroup.* Az *Azure AD-szerepkörök hozzárendelhetőek ehhez a csoporthoz (előzetes verzió)* alatt válassza az Igen *lehetőséget.* Végül válassza a *Létrehozás lehetőséget.*
    :::image type="content" source="./media/managed-aad/jit-new-group-created.png" alt-text="Megjeleníti Azure Portal új csoport létrehozására képernyőt.":::
1. A csoport vissza fog hozni a *Csoportok lapra.* Válassza ki az újonnan létrehozott csoportot, és jegyezze fel az objektumazonosítót, amely a többi utasításhoz a következőként `<object-id>` hivatkozik: .
    :::image type="content" source="./media/managed-aad/jit-get-object-id.png" alt-text="Megjeleníti Azure Portal újonnan létrehozott csoport képernyőképét, kiemelve az objektumazonosítót":::
1. AKS-fürt üzembe helyezése AKS által felügyelt Azure AD-integrációval a korábbi és értékek `<tenant-id>` `<object-id>` használatával:
    ```azurecli-interactive
    az aks create -g myResourceGroup -n myManagedCluster --enable-aad --aad-admin-group-object-ids <object-id> --aad-tenant-id <tenant-id>
    ```
1. A Azure Portal bal oldali Tevékenység menüjében  válassza az Emelt szintű hozzáférés *(előzetes verzió),* majd a Emelt szintű hozzáférés *engedélyezése lehetőséget.*
    :::image type="content" source="./media/managed-aad/jit-enabling-priv-access.png" alt-text="Megjelenik Azure Portal emelt szintű hozzáférés (előzetes verzió) lapja, kiemelt &quot;Emelt szintű hozzáférés engedélyezése&quot; beállítással":::
1. Válassza *a Hozzárendelések hozzáadása lehetőséget* a hozzáférés megadásának megkezdéséhez.
    :::image type="content" source="./media/managed-aad/jit-add-active-assignment.png" alt-text="Az Azure Portal megjelenik az emelt szintű hozzáférés (előzetes verzió) képernyő az engedélyezés után. A Hozzárendelések hozzáadása lehetőség ki van emelve.":::
1. Válasszon ki egy *tagi* szerepkört, és válassza ki azokat a felhasználókat és csoportokat, akiknek hozzáférést szeretne a fürthöz. Ezeket a hozzárendeléseket a csoport rendszergazdája bármikor módosíthatja. Ha készen áll a továbblépni, válassza a *Tovább lehetőséget.*
    :::image type="content" source="./media/managed-aad/jit-adding-assignment.png" alt-text="Megjelenik Azure Portal Hozzárendelések hozzáadása Tagság hozzáadása képernyője, amely a kiválasztott mintafelhasználót tagként adja hozzá. A &quot;Tovább&quot; lehetőség ki van emelve.":::
1. Válassza ki az *Aktív* hozzárendelési típust, a kívánt időtartamot, és adja meg az indoklást. Ha készen áll a folytatásra, válassza a Hozzárendelés *lehetőséget.* További információ a hozzárendelési típusokról: Jogosultság hozzárendelése emelt szintű hozzáférési csoportra [(előzetes verzió) a Privileged Identity Management.][aad-assignments]
    :::image type="content" source="./media/managed-aad/jit-set-active-assignment-details.png" alt-text="Megjelenik Azure Portal hozzárendelések hozzáadása beállítási képernyője. Az &quot;Aktív&quot; hozzárendelési típus van kiválasztva, és egy minta indoklás van megadva. Az Assign (Hozzárendelés) lehetőség ki van emelve.":::

A hozzárendelések létrehozása után a fürt eléréséhez ellenőrizze, hogy működik-e az időponthoz kötött hozzáférés. Például:

```azurecli-interactive
 az aks get-credentials --resource-group myResourceGroup --name myManagedCluster
```

A bejelentkezéshez kövesse a lépéseket.

A fürt `kubectl get nodes` csomópontjainak megtekintéséhez használja az parancsot:

```azurecli-interactive
kubectl get nodes
```

Jegyezze fel a hitelesítési követelményt, és kövesse a hitelesítéshez szükséges lépéseket. Sikeres művelet esetén az alábbihoz hasonló kimenetnek kell lennie:

```output
To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code AAAAAAAAA to authenticate.
NAME                                STATUS   ROLES   AGE     VERSION
aks-nodepool1-61156405-vmss000000   Ready    agent   6m36s   v1.18.14
aks-nodepool1-61156405-vmss000001   Ready    agent   6m42s   v1.18.14
aks-nodepool1-61156405-vmss000002   Ready    agent   6m33s   v1.18.14
```

### <a name="troubleshooting"></a>Hibaelhárítás

Ha `kubectl get nodes` a az alábbihoz hasonló hibát ad vissza:

```output
Error from server (Forbidden): nodes is forbidden: User "aaaa11111-11aa-aa11-a1a1-111111aaaaa" cannot list resource "nodes" in API group "" at the cluster scope
```

Győződjön meg arról, hogy a biztonsági csoport rendszergazdája aktív hozzárendelést adott a *fiókjának.*

## <a name="next-steps"></a>Következő lépések

* Tudnivalók a [Kubernetes-hitelesítéshez szükséges Azure RBAC-integrációról][azure-rbac-integration]
* Tudnivalók az Azure AD és a [Kubernetes RBAC integrációjáról.][azure-ad-rbac]
* A [kubelogin használatával](https://github.com/Azure/kubelogin) hozzáférhet az Azure-hitelesítés kubectlben nem elérhető funkcióihoz.
* További információ az [AKS és a Kubernetes identitással kapcsolatos fogalmairól.][aks-concepts-identity]
* Az [Azure Resource Manager (ARM)-sablonokkal ][aks-arm-template] AKS által felügyelt Azure AD-kompatibilis fürtök hozhatók létre.

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
[az-aks-create]: /cli/azure/aks#az_aks_create
[az-aks-get-credentials]: /cli/azure/aks#az_aks_get_credentials
[az-group-create]: /cli/azure/group#az_group_create
[open-id-connect]:../active-directory/develop/v2-protocols-oidc.md
[az-ad-user-show]: /cli/azure/ad/user#az_ad_user_show
[rbac-authorization]: concepts-identity.md#role-based-access-controls-rbac
[operator-best-practices-identity]: operator-best-practices-identity.md
[azure-ad-rbac]: azure-ad-rbac.md
[azure-ad-cli]: azure-ad-integration-cli.md
[access-cluster]: #access-an-azure-ad-enabled-cluster
[aad-migrate]: #upgrading-to-aks-managed-azure-ad-integration
[aad-assignments]: ../active-directory/privileged-identity-management/groups-assign-member-owner.md#assign-an-owner-or-member-of-a-group
