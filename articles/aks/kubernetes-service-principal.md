---
title: Az Azure Kubernetes Services (AKS) szolgáltatásnevei
description: Az Azure Kubernetes Service-ben található fürthöz tartozó Azure Active Directory szolgáltatásnév létrehozása és felügyelete
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: conceptual
ms.date: 04/25/2019
ms.author: iainfou
ms.openlocfilehash: d8a8a2f005a92988158b3f9c36ce24936fb020b4
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/04/2019
ms.locfileid: "66475630"
---
# <a name="service-principals-with-azure-kubernetes-service-aks"></a>Szolgáltatásnevek és az Azure Kubernetes Service (AKS)

Az AKS-fürtöknek szükségük van egy [Azure Active Directory (AS) szolgáltatásnévre][aad-service-principal] az Azure API-kkal való kommunikációhoz. A szolgáltatásnévvel dinamikusan hozhat létre és kezelhet olyan egyéb Azure-erőforrásokat, mint az Azure Load Balancer vagy a tárolóregisztrációs adatbázis (ACR).

Ez a cikk azt mutatja be, hogyan lehet létrehozni és használni egy szolgáltatásnevet az AKS-fürtökhöz.

## <a name="before-you-begin"></a>Előkészületek

Azure AD szolgáltatásnév létrehozásához rendelkeznie kell alkalmazásregisztrációs engedéllyel az Azure AD-bérlőben és alkalmazások szerepkörhöz rendeléséhez az előfizetésben. Ha nem rendelkezik a szükséges engedélyekkel, lehet, hogy meg kell kérnie az Azure AD vagy az előfizetés rendszergazdáját, hogy biztosítsa a szükséges engedélyeket, vagy hogy hozzon létre előzetesen egy szolgáltatásnevet, hogy használhassa az AKS-fürthöz.

Ha egy egyszerű szolgáltatást használ egy másik Azure ad-bérlőben, érhetők el további szempontok az engedélyek körül, a fürt telepítésekor. Előfordulhat, hogy nem rendelkezik megfelelő engedélyeket az olvasási és írási könyvtár. További információkért lásd: [Mik azok az alapértelmezett felhasználói engedélyek az Azure Active Directoryban?][azure-ad-permissions]

Emellett az Azure CLI 2.0.59 verziójára van szükség, vagy később telepített és konfigurált. Futtatás `az --version` a verzió megkereséséhez. Ha telepíteni vagy frissíteni, tekintse meg kell [Azure CLI telepítése][install-azure-cli].

## <a name="automatically-create-and-use-a-service-principal"></a>Szolgáltatásnév automatikus létrehozása és használata

Amikor az Azure Portalon vagy az [az aks create][az-aks-create] paranccsal létrehoz egy AKS-fürtöt, az Azure automatikusan létre tud hozni egy szolgáltatásnevet.

Az alábbi Azure CLI-példaparancsban nincs megadva szolgáltatásnév. Ebben a forgatókönyvben az Azure CLI létrehoz egy szolgáltatásnevet az AKS-fürthöz. A művelet sikeres végrehajtásához az Azure-fiókjának rendelkeznie kell a szükséges jogosultságokkal a szolgáltatásnév létrehozásához.

```azurecli
az aks create --name myAKSCluster --resource-group myResourceGroup
```

## <a name="manually-create-a-service-principal"></a>Szolgáltatásnév manuális létrehozása

Egy szolgáltatásnév Azure CLI felületen való manuális létrehozásához használja az [az ad sp create-for-rbac][az-ad-sp-create] parancsot. A következő példában a `--skip-assignment` paraméter megakadályozza bármilyen további alapértelmezett hozzárendelés használatát:

```azurecli-interactive
az ad sp create-for-rbac --skip-assignment
```

A kimenet a következő példához hasonló. Jegyezze fel a saját `appId` és `password` adatait. A rendszer a következő szakaszban ezeket az értékeket fogja használni az AKS-fürtök létrehozása során.

```json
{
  "appId": "559513bd-0c19-4c1a-87cd-851a26afd5fc",
  "displayName": "azure-cli-2019-03-04-21-35-28",
  "name": "http://azure-cli-2019-03-04-21-35-28",
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

Ha az Azure Portal használatával helyez üzembe egy AKS-fürtöt, akkor a **Kubernetes-fürt létrehozása** párbeszédablak *Hitelesítés* oldalán válassza a **Szolgáltatásnév konfigurálása** lehetőséget. Válassza a **Meglévő használata** lehetőséget, majd adja meg a következő értékeket:

- A **szolgáltatásnév ügyfél-azonosítója** az *appId*
- A **szolgáltatásnév titkos ügyfélkulcsa** pedig *jelszó* értéke

![Az Azure Vote keresését ábrázoló kép](media/kubernetes-service-principal/portal-configure-service-principal.png)

## <a name="delegate-access-to-other-azure-resources"></a>Egyéb Azure-erőforrások hozzáférésének delegálása

Az AKS-fürthöz tartozó egyszerű szolgáltatás egyéb erőforrásainak elérésére használható. Például ha egy meglévő Azure virtuális hálózat alhálózatának az AKS-fürt üzembe helyezése, vagy összekapcsolhatja az Azure Container Registry (ACR), szüksége a szolgáltatásnévnek azon erőforrásokhoz való hozzáférés delegálására.

Engedélyeket delegálhatnak, hozzon létre egy szerepkör hozzárendelése a [az szerepkör-hozzárendelés létrehozása] [ az-role-assignment-create] parancsot. Rendelje hozzá a `appId` egy adott hatókörhöz, például egy erőforráscsoport vagy virtuálishálózat-erőforrást. Egy szerepkör majd meghatározza, milyen engedélyekkel a szolgáltatásnév rendelkezik az erőforráson, az alábbi példában látható módon:

```azurecli
az role assignment create --assignee <appId> --scope <resourceScope> --role Contributor
```

A `--scope` erőforrás kell lennie a teljes erőforrás-azonosítója, mint például a */subscriptions/\<guid\>/resourceGroups/myResourceGroup* vagy */subscriptions/\<GUID azonosítója \>/resourceGroups/myResourceGroupVnet/providers/Microsoft.Network/virtualNetworks/myVnet*

Az alábbi szakaszok általános delegálásokat, győződjön meg arról, hogy szükség lehet.

### <a name="azure-container-registry"></a>Azure Container Registry

Az Azure Container Registry (ACR) használja a rendszerképet a tárolóban, ha az AKS-fürt és rendszerképeket engedélyeket szeretne. Az AKS-fürtöt, az egyszerű szolgáltatás kell delegálni a *olvasó* a beállításjegyzék szerepkör. Részletes lépéseiért lásd: [Grant AKS hozzáférés az ACR-be][aks-to-acr].

### <a name="networking"></a>Hálózat

Speciális hálózatkezelés felhasználhatja, ahol a virtuális hálózat és alhálózat vagy nyilvános IP-címek szerepelnek-e egy másik erőforráscsoportot. Rendelje hozzá a következő szerepkör engedélykészletet egyikét:

- Hozzon létre egy [egyéni szerepkör] [ rbac-custom-role] és a következő szerepkör-engedélyek megadása:
  - *Microsoft.Network/virtualNetworks/subnets/join/action*
  - *Microsoft.Network/virtualNetworks/subnets/read*
  - *Microsoft.Network/publicIPAddresses/read*
  - *Microsoft.Network/publicIPAddresses/write*
  - *Microsoft.Network/publicIPAddresses/join/action*
- Vagy, rendelje hozzá a [hálózati közreműködő] [ rbac-network-contributor] beépített szerepkör az alhálózat a virtuális hálózaton belül

### <a name="storage"></a>Storage

Szükség lehet egy másik erőforráscsoportban található meglévő lemez erőforrások eléréséhez. Rendelje hozzá a következő szerepkör engedélykészletet egyikét:

- Hozzon létre egy [egyéni szerepkör] [ rbac-custom-role] és a következő szerepkör-engedélyek megadása:
  - *Microsoft.Compute/disks/read*
  - *Microsoft.Compute/disks/write*
- Vagy, rendelje hozzá a [Tárfiók-közreműködő] [ rbac-storage-contributor] arra az erőforráscsoportra beépített szerepkör

### <a name="azure-container-instances"></a>Azure Container Instances

Ha a Virtual Kubelet használatával integrálása az AKS és Azure Container Instances (ACI) futtatta az erőforráscsoportban, az AKS-fürtöt külön, rendelkeznie kell a az AKS szolgáltatás egyszerű *közreműködői* az ACI erőforrás engedélyeinek a csoport.

## <a name="additional-considerations"></a>Néhány fontos megjegyzés

AKS és Azure AD szolgáltatásnevek használata esetén vegye figyelembe a következőket.

- A Kubernetes egyszerű szolgáltatása része a fürtkonfigurációnak. Azonban nem ajánlott az identitást használni a fürt üzembe helyezésére.
- Alapértelmezés szerint egyszerű szolgáltatás hitelesítő adatai érvényesek az egy év. Is [frissítése vagy a szolgáltatásnév hitelesítő adatainak elforgatása] [ update-credentials] bármikor.
- Minden egyszerű szolgáltatás társítva van egy Azure AD-alkalmazáshoz. A Kubernetes-fürt szolgáltatásneve társítható bármilyen érvényes Azure AD-alkalmazásnévhez (például: *https://www.contoso.org/example* ). Az alkalmazás URL-címének nem szükséges valódi végpontnak lennie.
- Amikor megadja a szolgáltatásnév **Client ID-ját** (Ügyfél-azonosítóját), használja az `appId` értékét.
- Az ügynök csomópont virtuális gépein a Kubernetes-fürt, az egyszerű szolgáltatás hitelesítő adatai a fájlban tárolt `/etc/kubernetes/azure.json`
- Ha az [az aks create][az-aks-create] parancsot használja a szolgáltatásnév automatikus létrehozásához, a szolgáltatásnév hitelesítő adatai a `~/.azure/aksServicePrincipal.json` fájlba lesznek írva azon a gépen, amelyen a parancsot futtatta.
- Az [az aks create][az-aks-create] használatával létrehozott AKS-fürt törlésekor az automatikusan létrehozott szolgáltatásnév nem törlődik.
    - Az egyszerű szolgáltatás törléséhez lekérdezése a fürt *servicePrincipalProfile.clientId* , majd törölje a [az ad app delete][az-ad-app-delete]. Cserélje le a következő erőforrás csoport és a fürt nevét a saját értékeit:

        ```azurecli
        az ad sp delete --id $(az aks show -g myResourceGroup -n myAKSCluster --query servicePrincipalProfile.clientId -o tsv)
        ```

## <a name="troubleshoot"></a>Hibaelhárítás

Az egyszerű szolgáltatás hitelesítő adatai az AKS-fürt az Azure CLI által lettek gyorsítótárazva. Ha ezek a hitelesítő adatok lejártak, az AKS-fürt üzembe helyezése hibát tapasztal. A következő hibaüzenetet kapja, amikor fut [az aks létrehozása] [ az-aks-create] előfordulhat, hogy a gyorsítótárazott szolgáltatásnév hitelesítő adatainak hibás:

```console
Operation failed with status: 'Bad Request'.
Details: The credentials in ServicePrincipalProfile were invalid. Please see https://aka.ms/aks-sp-help for more details.
(Details: adal: Refresh request failed. Status Code = '401'.
```

Ellenőrizze a hitelesítőadat-fájlja a következő paranccsal kora:

```console
ls -la $HOME/.azure/aksServicePrincipal.json
```

A szolgáltatásnév hitelesítő adatainak alapértelmezett lejárati idejének egy év. Ha a *aksServicePrincipal.json* fájl régebbi, mint egy évig, törölje a fájlt, és próbálja meg újra telepíteni egy AKS-fürtöt.

## <a name="next-steps"></a>További lépések

Azure Active Directory szolgáltatásnevekkel kapcsolatos további információkért lásd: [alkalmazás és egyszerű szolgáltatási objektumok][service-principal].

A hitelesítő adatok frissítésével kapcsolatos információkért lásd: [frissítése vagy a hitelesítő adatok forgatása az aks-ben a szolgáltatásnévhez tartozó][update-credentials].

<!-- LINKS - internal -->
[aad-service-principal]:../active-directory/develop/app-objects-and-service-principals.md
[acr-intro]: ../container-registry/container-registry-intro.md
[az-ad-sp-create]: /cli/azure/ad/sp#az-ad-sp-create-for-rbac
[azure-load-balancer-overview]: ../load-balancer/load-balancer-overview.md
[install-azure-cli]: /cli/azure/install-azure-cli
[service-principal]:../active-directory/develop/app-objects-and-service-principals.md
[user-defined-routes]: ../load-balancer/load-balancer-overview.md
[az-ad-app-list]: /cli/azure/ad/app#az-ad-app-list
[az-ad-app-delete]: /cli/azure/ad/app#az-ad-app-delete
[az-aks-create]: /cli/azure/aks#az-aks-create
[rbac-network-contributor]: ../role-based-access-control/built-in-roles.md#network-contributor
[rbac-custom-role]: ../role-based-access-control/custom-roles.md
[rbac-storage-contributor]: ../role-based-access-control/built-in-roles.md#storage-account-contributor
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
[aks-to-acr]: ../container-registry/container-registry-auth-aks.md?toc=%2fazure%2faks%2ftoc.json#grant-aks-access-to-acr
[update-credentials]: update-credentials.md
[azure-ad-permissions]: ../active-directory/fundamentals/users-default-permissions.md
