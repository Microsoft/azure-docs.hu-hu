---
title: Felügyelt identitás az ACR-feladatban
description: Engedélyezze az Azure-erőforrások felügyelt identitását egy Azure Container Registry feladatban, hogy a feladat hozzáférjen más Azure-erőforrásokhoz, például más privát tárolóregisztrálókhoz.
services: container-registry
author: dlepow
manager: gwallace
ms.service: container-registry
ms.topic: article
ms.date: 01/14/2020
ms.author: danlep
ms.openlocfilehash: 19d63861a98884ff2f5103946c19e2226c4b14b7
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107781162"
---
# <a name="use-an-azure-managed-identity-in-acr-tasks"></a>Azure által felügyelt identitás használata a ACR-feladatok 

Engedélyezze az [Azure-erőforrások felügyelt](../active-directory/managed-identities-azure-resources/overview.md) identitását egy [ACR-feladatban,](container-registry-tasks-overview.md)így a feladat anélkül férhet hozzá más Azure-erőforrásokhoz, hogy hitelesítő adatokat kellene megadnia vagy kezelnie. Egy felügyelt identitással például engedélyezheti egy feladatlépés számára a tároló rendszerképének leküldését vagy leküldését egy másik regisztrációs adatbázisba.

Ebből a cikkből megtudhatja, hogyan engedélyezheti egy felhasználó által hozzárendelt vagy rendszer által hozzárendelt felügyelt identitást egy ACR-feladatban az Azure CLI használatával. Használhatja a Azure Cloud Shell vagy az Azure CLI helyi telepítését. Ha helyileg szeretné használni, a 2.0.68-as vagy újabb verzió szükséges. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése][azure-cli-install].

Szemléltetés céljából a cikkben szereplő példaparancsok [az az acr task create][az-acr-task-create] paranccsal hoznak létre egy alapszintű rendszerkép-összeállítási feladatot, amely lehetővé teszi a felügyelt identitások használatát. A biztonságos erőforrások ACR-feladatból felügyelt identitással való elérésének mintaforgatókönyveiért lásd:

* [Több regisztrációs adatbázisra kiterjedő hitelesítés](container-registry-tasks-cross-registry-authentication.md)
* [Külső erőforrások elérése a tárolóban tárolt titkos Azure Key Vault](container-registry-tasks-authentication-key-vault.md)

## <a name="why-use-a-managed-identity"></a>Miért érdemes felügyelt identitást használni?

Az Azure-erőforrások felügyelt identitása automatikusan felügyelt identitást biztosít a kiválasztott Azure-szolgáltatásoknak a Azure Active Directory. Az ACR-feladatokat konfigurálhatja felügyelt identitással, így a feladat anélkül férhet hozzá más biztonságos Azure-erőforrásokhoz, hogy a feladat lépéseibe beadja a hitelesítő adatokat.

A felügyelt identitások kétféle típusúak:

* *Felhasználó által hozzárendelt identitások,* amelyek több erőforráshoz is hozzárendelhetőek, és a kívánt ideig megmaradnak. A felhasználó által hozzárendelt identitások jelenleg előzetes verzióban állnak rendelkezésre.

* Egy *rendszer által hozzárendelt identitás,* amely egyedi egy adott erőforráshoz, például egy ACR-feladathoz, és az erőforrás teljes élettartama alatt tart.

Egy ACR-feladatban engedélyezheti az identitástípusokat, vagy mindkettőt. Adjon hozzáférést az identitásnak egy másik erőforráshoz, akárcsak bármely más rendszerbiztonsági tag. A feladat futtatásakor az identitással fér hozzá az erőforráshoz minden olyan feladat lépésében, amely hozzáférést igényel.

## <a name="steps-to-use-a-managed-identity"></a>Felügyelt identitás használatának lépései

Kövesse ezeket a magas szintű lépéseket, ha felügyelt identitást használ egy ACR-feladattal.

### <a name="1-optional-create-a-user-assigned-identity"></a>1. (Nem kötelező) Felhasználó által hozzárendelt identitás létrehozása

Ha felhasználó által hozzárendelt identitást tervez használni, használjon egy meglévő identitást, vagy hozza létre az identitást az Azure CLI vagy más Azure-eszközök használatával. Használja például az [az identity create][az-identity-create] parancsot. 

Ha csak rendszer által hozzárendelt identitást tervez használni, hagyja ki ezt a lépést. Az ACR-feladat létrehozásakor létre kell hoznia egy rendszer által hozzárendelt identitást.

### <a name="2-enable-identity-on-an-acr-task"></a>2. Identitás engedélyezése ACR-feladaton

ACR-feladat létrehozásakor engedélyezheti a felhasználó által hozzárendelt identitást, a rendszer által hozzárendelt identitást vagy mindkettőt. Például az az acr task create parancs Azure CLI-beli futtatásakor adja át a `--assign-identity` paramétert. [][az-acr-task-create]

A rendszer által hozzárendelt identitás engedélyezéséhez adja meg a értéket `--assign-identity` érték nélkül, vagy `assign-identity [system]` érték nélkül. Az alábbi példaparancs egy Linux-feladatot hoz létre egy nyilvános GitHub-adattárból, amely létrehozza a rendszerképet, és engedélyezi a rendszer által hozzárendelt `hello-world` felügyelt identitást:

```azurecli
az acr task create \
    --image hello-world:{{.Run.ID}} \
    --name hello-world --registry MyRegistry \
    --context https://github.com/Azure-Samples/acr-build-helloworld-node.git#main \
    --file Dockerfile \
    --commit-trigger-enabled false \
    --assign-identity
```

Felhasználó által hozzárendelt identitás engedélyezéséhez adja meg a értéket az identitás `--assign-identity` *erőforrás-azonosítójával.* Az alábbi példaparancs egy Linux-feladatot hoz létre egy nyilvános GitHub-adattárból, amely létrehozza a rendszerképet, és engedélyezi a felhasználó által hozzárendelt `hello-world` felügyelt identitást:

```azurecli
az acr task create \
    --image hello-world:{{.Run.ID}} \
    --name hello-world --registry MyRegistry \
    --context https://github.com/Azure-Samples/acr-build-helloworld-node.git#main \
    --file Dockerfile \
    --commit-trigger-enabled false
    --assign-identity <resourceID>
```

Az identitás erőforrás-azonosítóját az az identity show parancs futtatásával [kaphatja][az-identity-show] meg. A *myResourceGroup* erőforráscsoportban a *myUserAssignedIdentity* azonosító erőforrás-azonosítója a következő: 

```
"/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myUserAssignedIdentity"
```

### <a name="3-grant-the-identity-permissions-to-access-other-azure-resources"></a>3. Engedélyek megadása az identitás számára más Azure-erőforrások eléréséhez

A feladat követelményeitől függően adjon engedélyeket az identitásnak más Azure-erőforrások eléréséhez. Példák:

* Rendeljen a felügyelt identitáshoz egy leküldéses, leküldéses és leküldéses vagy egyéb engedélyekkel rendelkező szerepkört egy cél tároló-beállításjegyzékhez az Azure-ban. A beállításjegyzék-szerepkörök teljes listáját a szerepkörök és engedélyek [Azure Container Registry tekintse meg.](container-registry-roles.md) 
* Rendeljen hozzá egy szerepkört a felügyelt identitáshoz az Azure Key Vaultban tárolt titkos kulcsok olvassához.

Az [Azure CLI vagy](../role-based-access-control/role-assignments-cli.md) más Azure-eszközök használatával kezelheti az erőforrásokhoz való szerepköralapú hozzáférést. Futtassa például az [az role assignment create][az-role-assignment-create] parancsot az identitás erőforráshoz való hozzárendelése érdekében. 

Az alábbi példa hozzárendeli a felügyelt identitáshoz a tároló-beállításjegyzékből való lekért engedélyeket. A parancs megadja *a* feladatidentitás egyszerű azonosítóját és a céljegyzék erőforrás-azonosítóját. 


```azurecli
az role assignment create \
  --assignee <principalID> \
  --scope <registryID> \
  --role acrpull
```

### <a name="4-optional-add-credentials-to-the-task"></a>4. (Nem kötelező) Hitelesítő adatok hozzáadása a feladathoz

Ha a feladatnak hitelesítő adatokra van szüksége a rendszerképek egy másik egyéni regisztrációs adatbázisba való leküldéséhez vagy leküldéséhez, vagy más erőforrások eléréséhez, adja hozzá a hitelesítő adatokat a feladathoz. Futtassa [az az acr task credential add][az-acr-task-credential-add] parancsot a hitelesítő adatok hozzáadásához, majd adja át a paramétert annak jelzésére, hogy az identitás hozzáfér a hitelesítő `--use-identity` adatokhoz. 

Ha például hitelesítő adatokat szeretne hozzáadni egy rendszer által hozzárendelt identitáshoz az Azure Container Registry *targetregistry hitelesítéséhez,* adja át a következőt: `use-identity [system]`

```azurecli
az acr task credential add \
    --name helloworld \
    --registry myregistry \
    --login-server targetregistry.azurecr.io \
    --use-identity [system]
```

Ha hitelesítő adatokat szeretne hozzáadni egy felhasználó által hozzárendelt identitáshoz a beállításjegyzék *targetregistry* adatbázisával való hitelesítéshez, adja át a értéket az identitás `use-identity`  ügyfél-azonosítójával. Például:

```azurecli
az acr task credential add \
    --name helloworld \
    --registry myregistry \
    --login-server targetregistry.azurecr.io \
    --use-identity <clientID>
```

Az identitás ügyfél-azonosítóját az az identity show parancs futtatásával [kaphatja][az-identity-show] meg. Az ügyfél-azonosító a űrlap GUID `xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx` azonosítója.

### <a name="5-run-the-task"></a>5. A feladat futtatása

Miután konfigurált egy feladatot egy felügyelt identitással, futtassa a feladatot. A cikkben létrehozott feladatok egyikének tesztelésére például manuálisan aktiválja azt [az az acr task run paranccsal.][az-acr-task-run] Ha további, automatizált feladat-eseményindítókat konfigurált, a feladat automatikusan elindul.

## <a name="next-steps"></a>Következő lépések

Ebben a cikkben megtanulta, hogyan engedélyezheti és használhatja a felhasználó által hozzárendelt vagy rendszer által hozzárendelt felügyelt identitást egy ACR-feladatban. A biztonságos erőforrások ACR-feladatból felügyelt identitással való elérésének forgatókönyveiért lásd:

* [Több regisztrációs adatbázisra kiterjedő hitelesítés](container-registry-tasks-cross-registry-authentication.md)
* [Külső erőforrások elérése a tárolóban tárolt titkos Azure Key Vault](container-registry-tasks-authentication-key-vault.md)


<!-- LINKS - Internal -->
[az-role-assignment-create]: /cli/azure/role/assignment#az_role_assignment_create
[az-identity-create]: /cli/azure/identity#az_identity_create
[az-identity-show]: /cli/azure/identity#az_identity_show
[az-acr-task-create]: /cli/azure/acr/task#az_acr_task_create
[az-acr-task-run]: /cli/azure/acr/task#az_acr_task_run
[az-acr-task-credential-add]: /cli/azure/acr/task/credential#az_acr_task_credential_add
[azure-cli-install]: /cli/azure/install-azure-cli
