---
title: Az Azure Kubernetes Services (AKS) szolgáltatásnevei
description: Az Azure Kubernetes Service-ben található fürthöz tartozó Azure Active Directory szolgáltatásnév létrehozása és felügyelete
services: container-service
ms.topic: conceptual
ms.date: 06/16/2020
ms.openlocfilehash: 2f32ce96097e008ac1100c62c04b6bb7001ae972
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107779632"
---
# <a name="service-principals-with-azure-kubernetes-service-aks"></a>Szolgáltatásnevek és az Azure Kubernetes Service (AKS)

Az Azure API-k használatához az AKS-fürtöknek vagy egy Azure Active Directory [(AD)][aad-service-principal] szolgáltatásnévre vagy egy felügyelt [identitásra van szüksége.](use-managed-identity.md) Az egyéb Azure-erőforrások, például az Azure Load Balancer vagy a tároló-beállításjegyzék (ACR) dinamikus létrehozásához és kezeléséhez szolgáltatásnévre vagy felügyelt identitásra van szükség.

Ez a cikk azt mutatja be, hogyan lehet létrehozni és használni egy szolgáltatásnevet az AKS-fürtökhöz.

## <a name="before-you-begin"></a>Előkészületek

Azure AD szolgáltatásnév létrehozásához rendelkeznie kell alkalmazásregisztrációs engedéllyel az Azure AD-bérlőben és alkalmazások szerepkörhöz rendeléséhez az előfizetésben. Ha nem rendelkezik a szükséges engedélyekkel, lehet, hogy meg kell kérnie az Azure AD vagy az előfizetés rendszergazdáját, hogy biztosítsa a szükséges engedélyeket, vagy hogy hozzon létre előzetesen egy szolgáltatásnevet, hogy használhassa az AKS-fürthöz.

Ha egy másik Azure AD-bérlőből származó szolgáltatásnévt használ, további szempontokat kell figyelembe vennie a fürt üzembe helyezésekor elérhető engedélyekkel kapcsolatban. Előfordulhat, hogy nem rendelkezik a megfelelő engedélyekkel a címtárinformációk olvasásához és írásához. További információ: Mik az alapértelmezett felhasználói engedélyek a [Azure Active Directory?][azure-ad-permissions]

Az Azure CLI 2.0.59-es vagy újabb verziójára is telepítve és konfigurálva kell. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése][install-azure-cli].

## <a name="automatically-create-and-use-a-service-principal"></a>Szolgáltatásnév automatikus létrehozása és használata

Amikor az Azure Portalon vagy az [az aks create][az-aks-create] paranccsal létrehoz egy AKS-fürtöt, az Azure automatikusan létre tud hozni egy szolgáltatásnevet.

Az alábbi Azure CLI-példaparancsban nincs megadva szolgáltatásnév. Ebben a forgatókönyvben az Azure CLI létrehoz egy szolgáltatásnevet az AKS-fürthöz. A művelet sikeres végrehajtásához az Azure-fiókjának rendelkeznie kell a szükséges jogosultságokkal a szolgáltatásnév létrehozásához.

```azurecli
az aks create --name myAKSCluster --resource-group myResourceGroup
```

## <a name="manually-create-a-service-principal"></a>Szolgáltatásnév manuális létrehozása

Egy szolgáltatásnév Azure CLI felületen való manuális létrehozásához használja az [az ad sp create-for-rbac][az-ad-sp-create] parancsot. A következő példában a `--skip-assignment` paraméter megakadályozza bármilyen további alapértelmezett hozzárendelés használatát:

```azurecli-interactive
az ad sp create-for-rbac --skip-assignment --name myAKSClusterServicePrincipal
```

A kimenet a következő példához hasonló. Jegyezze fel a saját `appId` és `password` adatait. A rendszer a következő szakaszban ezeket az értékeket fogja használni az AKS-fürtök létrehozása során.

```json
{
  "appId": "559513bd-0c19-4c1a-87cd-851a26afd5fc",
  "displayName": "myAKSClusterServicePrincipal",
  "name": "http://myAKSClusterServicePrincipal",
  "password": "e763725a-5eee-40e8-a466-dc88d980f415",
  "tenant": "72f988bf-86f1-41af-91ab-2d7cd011db48"
}
```

## <a name="specify-a-service-principal-for-an-aks-cluster"></a>Szolgáltatásnév megadása AKS-fürthöz

Ha egy meglévő szolgáltatásnevet szeretne használni az AKS-fürt [az aks create][az-aks-create] paranccsal történő létrehozásakor, akkor használja az [az ad sp create-for-rbac][az-ad-sp-create] parancs kimenetéből a `--service-principal` és a `--client-secret` paramétereket az `appId` és a `password` megadásához:

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --service-principal <appId> \
    --client-secret <password>
```

> [!NOTE]
> Ha meglévő szolgáltatásnévvel és testreszabott titkos okkal használ, győződjön meg arról, hogy a titkos adat nem hosszabb 190 bájtnál.

Ha az Azure Portal használatával helyez üzembe egy AKS-fürtöt, akkor a **Kubernetes-fürt létrehozása** párbeszédablak *Hitelesítés* oldalán válassza a **Szolgáltatásnév konfigurálása** lehetőséget. Válassza a **Meglévő használata** lehetőséget, majd adja meg a következő értékeket:

- A **szolgáltatásnév ügyfél-azonosítója** az *appId*
- A **szolgáltatásnév titkos ügyfélkulcsa** pedig *jelszó* értéke

![Az Azure Vote keresését ábrázoló kép](media/kubernetes-service-principal/portal-configure-service-principal.png)

## <a name="delegate-access-to-other-azure-resources"></a>Más Azure-erőforrásokhoz való hozzáférés delegálása

Az AKS-fürt szolgáltatásnévvel más erőforrásokhoz is hozzáférhet. Ha például egy meglévő Azure-beli virtuális hálózat alhálózatán szeretné üzembe helyezni az AKS-fürtöt, vagy csatlakozni szeretne az Azure Container Registry (ACR) szolgáltatáshoz, delegálnia kell az erőforrásokhoz való hozzáférést a szolgáltatásnévnek.

Az engedélyek delegálához hozzon létre egy szerepkör-hozzárendelést [az az role assignment create paranccsal.][az-role-assignment-create] Rendelje hozzá a et `appId` egy adott hatókörnek, például egy erőforráscsoportnak vagy egy virtuális hálózati erőforrásnak. A szerepkör ezután meghatározza, hogy a szolgáltatásnév milyen engedélyekkel rendelkezik az erőforráson, az alábbi példában látható módon:

```azurecli
az role assignment create --assignee <appId> --scope <resourceScope> --role Contributor
```

Az erőforrás teljes erőforrás-azonosítójának kell lennie, például `--scope` *\<guid\> /subscriptions/ /resourceGroups/myResourceGroup* vagy *\<guid\> /subscriptions/ /resourceGroups/myResourceGroupVnet/providers/Microsoft.Network/virtualNetworks/myVnet*

> [!NOTE]
> Ha eltávolította a Közreműködő szerepkör-hozzárendelést a csomópont erőforráscsoportból, az alábbi műveletek meghiúsulhatnak.  

A következő szakaszok részletesen ismertetik a gyakori delegálásokat, amelyekre szükség lehet.

### <a name="azure-container-registry"></a>Azure Container Registry

Ha az Azure Container Registry (ACR) tárolólemezkép-tárolóként használja, engedélyeket kell adnunk az AKS-fürt szolgáltatásnévnek a rendszerképek olvasása és lekérte számára. Jelenleg az [az aks create vagy az az aks][az-aks-create] [update][az-aks-update] paranccsal integrálható a beállításjegyzék, és hozzárendelhető a megfelelő szerepkör a szolgáltatásnévhez. Részletes lépésekért lásd: Hitelesítés Azure Container Registry [a Azure Kubernetes Service.][aks-to-acr]

### <a name="networking"></a>Hálózatkezelés

Használhat speciális hálózatokat, ahol a virtuális hálózat és az alhálózat vagy a nyilvános IP-címek egy másik erőforráscsoportban vannak. Rendelje hozzá [a Hálózati közreműködő][rbac-network-contributor] beépített szerepkört a virtuális hálózat alhálózatához. Másik lehetőségként létrehozhat egy egyéni szerepkört, [amely][rbac-custom-role] engedéllyel rendelkezik az adott erőforráscsoportban a hálózati erőforrások eléréséhez. További [részletekért lásd: AKS-szolgáltatásengedélyek.][aks-permissions]

### <a name="storage"></a>Tárolás

Előfordulhat, hogy egy másik erőforráscsoport meglévő lemezerőforrását kell elérnie. Rendelje hozzá a következő szerepkör-engedélyek egyikét:

- Hozzon [létre egy egyéni szerepkört,][rbac-custom-role] és határozza meg a következő szerepkör-engedélyeket:
  - *Microsoft.Compute/disks/read*
  - *Microsoft.Compute/disks/write*
- Másik megoldásként [][rbac-storage-contributor] rendelje hozzá a Tárfiók-közreműködő beépített szerepkört az erőforráscsoporthoz

### <a name="azure-container-instances"></a>Azure Container Instances

Ha a Virtual Kubeletet az AKS-sel való integrációhoz használja, és úgy dönt, hogy az AKS-fürttől különálló erőforráscsoportban futtatja az Azure Container Instances (ACI) szolgáltatást, az AKS-szolgáltatásnévnek Közreműködői engedélyekkel kell rendelkeznie az ACI-erőforráscsoporton. 

## <a name="additional-considerations"></a>Néhány fontos megjegyzés

AKS és Azure AD szolgáltatásnevek használata esetén vegye figyelembe a következőket.

- A Kubernetes egyszerű szolgáltatása része a fürtkonfigurációnak. Azonban nem ajánlott az identitást használni a fürt üzembe helyezésére.
- Alapértelmezés szerint a szolgáltatásnév hitelesítő adatai egy évig érvényesek. A [szolgáltatásnév hitelesítő adatait bármikor][update-credentials] frissítheti vagy elforgathatja.
- Minden egyszerű szolgáltatás társítva van egy Azure AD-alkalmazáshoz. A Kubernetes-fürt egyszerű szolgáltatása bármilyen érvényes Azure AD-alkalmazásnévhez társítható (például: *https://www.contoso.org/example* ). Az alkalmazás URL-címének nem szükséges valódi végpontnak lennie.
- Amikor megadja a szolgáltatásnév **Client ID-ját** (Ügyfél-azonosítóját), használja az `appId` értékét.
- A Kubernetes-fürt ügynökcsomóponti virtuális gépei a szolgáltatásnév hitelesítő adatait a fájlban tárolják `/etc/kubernetes/azure.json`
- Ha az [az aks create][az-aks-create] parancsot használja a szolgáltatásnév automatikus létrehozásához, a szolgáltatásnév hitelesítő adatai a `~/.azure/aksServicePrincipal.json` fájlba lesznek írva azon a gépen, amelyen a parancsot futtatta.
- Ha nem ad át külön szolgáltatásnévket további AKS CLI-parancsokban, a rendszer a helyen található alapértelmezett `~/.azure/aksServicePrincipal.json` egyszerű szolgáltatást használja.  
- A fájlban található aksServicePrincipal.jsis eltávolíthatja, és az AKS létrehoz egy új egyszerű szolgáltatást.
- Az [az aks create][az-aks-create] használatával létrehozott AKS-fürt törlésekor az automatikusan létrehozott szolgáltatásnév nem törlődik.
    - A szolgáltatásnév törléséhez le kellkérdezni a *fürt servicePrincipalProfile.clientId-ját,* majd törölni kell az [az ad sp delete parancsot.][az-ad-sp-delete] Cserélje le a következő erőforráscsoport- és fürtneveket a saját értékeire:

        ```azurecli
        az ad sp delete --id $(az aks show -g myResourceGroup -n myAKSCluster --query servicePrincipalProfile.clientId -o tsv)
        ```

## <a name="troubleshoot"></a>Hibaelhárítás

Az AKS-fürt szolgáltatásnév hitelesítő adatait az Azure CLI gyorsítótárazza. Ha ezek a hitelesítő adatok lejártak, hibákba ütközik az AKS-fürtök üzembe helyezése során. Az [az aks create][az-aks-create] futtatásakor az alábbi hibaüzenet a gyorsítótárazott szolgáltatásnév hitelesítő adataival való problémát jelezhet:

```console
Operation failed with status: 'Bad Request'.
Details: The credentials in ServicePrincipalProfile were invalid. Please see https://aka.ms/aks-sp-help for more details.
(Details: adal: Refresh request failed. Status Code = '401'.
```

Ellenőrizze a hitelesítőadat-fájl korát a következő paranccsal:

```console
ls -la $HOME/.azure/aksServicePrincipal.json
```

A szolgáltatásnév hitelesítő adatainak alapértelmezett lejárati ideje egy év. Ha a *aksServicePrincipal.js* egy évnél régebbi, törölje a fájlt, és próbáljon meg újra üzembe helyezni egy AKS-fürtöt.

## <a name="next-steps"></a>Következő lépések

A szolgáltatásnév-Azure Active Directory lásd: Alkalmazás- és [szolgáltatásnév-objektumok.][service-principal]

A hitelesítő adatok frissítésére vonatkozó információkért lásd: Szolgáltatásnév hitelesítő adatainak frissítése vagy váltása az [AKS-ban.][update-credentials]

<!-- LINKS - internal -->
[aad-service-principal]:../active-directory/develop/app-objects-and-service-principals.md
[acr-intro]: ../container-registry/container-registry-intro.md
[az-ad-sp-create]: /cli/azure/ad/sp#az_ad_sp_create_for_rbac
[az-ad-sp-delete]: /cli/azure/ad/sp#az_ad_sp_delete
[azure-load-balancer-overview]: ../load-balancer/load-balancer-overview.md
[install-azure-cli]: /cli/azure/install-azure-cli
[service-principal]:../active-directory/develop/app-objects-and-service-principals.md
[user-defined-routes]: ../load-balancer/load-balancer-overview.md
[az-ad-app-list]: /cli/azure/ad/app#az_ad_app_list
[az-ad-app-delete]: /cli/azure/ad/app#az_ad_app_delete
[az-aks-create]: /cli/azure/aks#az_aks_create
[az-aks-update]: /cli/azure/aks#az_aks_update
[rbac-network-contributor]: ../role-based-access-control/built-in-roles.md#network-contributor
[rbac-custom-role]: ../role-based-access-control/custom-roles.md
[rbac-storage-contributor]: ../role-based-access-control/built-in-roles.md#storage-account-contributor
[az-role-assignment-create]: /cli/azure/role/assignment#az_role_assignment_create
[aks-to-acr]: cluster-container-registry-integration.md
[update-credentials]: update-credentials.md
[azure-ad-permissions]: ../active-directory/fundamentals/users-default-permissions.md
[aks-permissions]: concepts-identity.md#aks-service-permissions
