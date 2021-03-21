---
title: Felügyelt identitás az ACR-feladatban
description: Felügyelt identitás engedélyezése az Azure-erőforrásokhoz egy Azure Container Registry feladatban, hogy a feladat hozzáférhessen más Azure-erőforrásokhoz, beleértve a privát tárolók beállításjegyzékeit is.
services: container-registry
author: dlepow
manager: gwallace
ms.service: container-registry
ms.topic: article
ms.date: 01/14/2020
ms.author: danlep
ms.openlocfilehash: 8f2749a18a5ac6aed0822553d59beaacc9060228
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98915947"
---
# <a name="use-an-azure-managed-identity-in-acr-tasks"></a>Azure által felügyelt identitás használata az ACR-feladatokban 

[Felügyelt identitás engedélyezése az Azure-erőforrásokhoz](../active-directory/managed-identities-azure-resources/overview.md) egy [ACR-feladatban](container-registry-tasks-overview.md), így a feladat hozzáférhet más Azure-erőforrásokhoz anélkül, hogy hitelesítő adatokat kellene megadnia vagy kezelnie. Például egy felügyelt identitás használatával engedélyezheti a feladatok lépéseit a tárolók rendszerképeinek egy másik beállításjegyzékbe való lekéréséhez vagy leküldéséhez.

Ebből a cikkből megtudhatja, hogyan használhatja az Azure CLI-t egy felhasználó által hozzárendelt vagy rendszerhez rendelt felügyelt identitás engedélyezésére egy ACR-feladatban. Az Azure CLI Azure Cloud Shell vagy helyi telepítését használhatja. Ha helyileg szeretné használni, a 2.0.68 vagy újabb verziót kötelező megadni. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése][azure-cli-install].

Illusztrációs célokra a cikkben szereplő példák az az [ACR Task Create][az-acr-task-create] paranccsal hozhatnak létre egy alapszintű rendszerkép-létrehozási feladatot, amely lehetővé teszi a felügyelt identitást. A biztonságos erőforrásoknak a felügyelt identitás használatával való eléréséhez az ACR-feladatokból a következő témakörben talál példákat:

* [Több regisztrációs adatbázisra kiterjedő hitelesítés](container-registry-tasks-cross-registry-authentication.md)
* [Külső erőforrások elérése Azure Key Vaultban tárolt titkos kulcsokkal](container-registry-tasks-authentication-key-vault.md)

## <a name="why-use-a-managed-identity"></a>Miért érdemes felügyelt identitást használni?

Az Azure-erőforrások felügyelt identitása a kiválasztott Azure-szolgáltatásokat a Azure Active Directory automatikusan felügyelt identitással biztosítja. Az ACR-feladatokat felügyelt identitással is konfigurálhatja, hogy a feladat hozzáférhessen más védett Azure-erőforrásokhoz anélkül, hogy a hitelesítő adatokat átadná a feladat lépéseibe.

A felügyelt identitások két típusúak:

* *Felhasználó által hozzárendelt identitások*, amelyek több erőforráshoz rendelhetők, és ameddig szeretnék megőrizni. A felhasználó által hozzárendelt identitások jelenleg előzetes verzióban érhetők el.

* Egy *rendszer által hozzárendelt identitás*, amely egyedi egy adott erőforráshoz, például egy ACR-feladathoz, és az adott erőforrás élettartamára vonatkozik.

Az ACR-feladatokban bármelyik vagy mindkét típusú identitás engedélyezhető. Az identitás hozzáférésének biztosítása egy másik erőforráshoz, ugyanúgy, mint bármely rendszerbiztonsági tag. A feladat futtatásakor a rendszer az identitás használatával fér hozzá az erőforráshoz bármely, hozzáférést igénylő feladat lépéseiben.

## <a name="steps-to-use-a-managed-identity"></a>A felügyelt identitás használatának lépései

Kövesse ezeket a magas szintű lépéseket, hogy felügyelt identitást alkalmazzon egy ACR-feladattal.

### <a name="1-optional-create-a-user-assigned-identity"></a>1. (nem kötelező) felhasználó által hozzárendelt identitás létrehozása

Ha felhasználó által hozzárendelt identitást szeretne használni, használjon egy meglévő identitást, vagy hozza létre az identitást az Azure CLI vagy más Azure-eszközök használatával. Használja például az az [Identity Create][az-identity-create] parancsot. 

Ha úgy tervezi, hogy csak rendszerhez rendelt identitást használ, hagyja ki ezt a lépést. Az ACR-feladat létrehozásakor létre kell hoznia egy rendszer által hozzárendelt identitást.

### <a name="2-enable-identity-on-an-acr-task"></a>2. az identitás engedélyezése ACR-feladaton

Ha egy ACR-feladatot hoz létre, opcionálisan engedélyezheti a felhasználó által hozzárendelt identitást, a rendszerhez rendelt identitást vagy mindkettőt. Adja át például a `--assign-identity` paramétert az az [ACR Task Create][az-acr-task-create] parancs az Azure CLI-ben való futtatásakor.

A rendszer által hozzárendelt identitás engedélyezéséhez adjon meg `--assign-identity` értéket vagy értéket `assign-identity [system]` . A következő példa egy nyilvános GitHub-tárházból hoz létre egy Linux-feladatot, amely létrehoz egy `hello-world` rendszerképet, és lehetővé teszi a rendszerhez rendelt felügyelt identitást:

```azurecli
az acr task create \
    --image hello-world:{{.Run.ID}} \
    --name hello-world --registry MyRegistry \
    --context https://github.com/Azure-Samples/acr-build-helloworld-node.git#main \
    --file Dockerfile \
    --commit-trigger-enabled false \
    --assign-identity
```

A felhasználó által hozzárendelt identitás engedélyezéséhez adja meg az `--assign-identity` identitás erőforrás- *azonosítójának* értékét. A következő példa egy nyilvános GitHub-tárházból hoz létre egy Linux-feladatot, amely létrehoz egy `hello-world` rendszerképet, és lehetővé teszi a felhasználó által hozzárendelt felügyelt identitást:

```azurecli
az acr task create \
    --image hello-world:{{.Run.ID}} \
    --name hello-world --registry MyRegistry \
    --context https://github.com/Azure-Samples/acr-build-helloworld-node.git#main \
    --file Dockerfile \
    --commit-trigger-enabled false
    --assign-identity <resourceID>
```

Az identitás erőforrás-AZONOSÍTÓját az az [Identity show][az-identity-show] parancs futtatásával kérheti le. Az erőforráscsoport *myResourceGroup* lévő azonosító *MYUSERASSIGNEDIDENTITY* erőforrás-azonosítója a következő: 

```
"/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myUserAssignedIdentity"
```

### <a name="3-grant-the-identity-permissions-to-access-other-azure-resources"></a>3. adja meg az identitás engedélyeit más Azure-erőforrásokhoz való hozzáféréshez

A feladat követelményeitől függően adja meg az identitás engedélyeit más Azure-erőforrások eléréséhez. Példák:

* A felügyelt identitást hozzárendelheti egy olyan szerepkörhöz, amely lekéréses, leküldéses és lekéréses, illetve egyéb engedélyekkel rendelkezik az Azure-beli cél tároló A beállításjegyzék szerepköreinek teljes listáját itt tekintheti meg: [Azure Container Registry szerepkörök és engedélyek](container-registry-roles.md). 
* Rendeljen hozzá egy szerepkört a felügyelt identitáshoz az Azure Key vaultban található titkos kódok olvasásához.

Az [Azure CLI](../role-based-access-control/role-assignments-cli.md) -vel vagy más Azure-eszközökkel kezelheti a szerepköralapú hozzáférést az erőforrásokhoz. Például futtassa az az [szerepkör-hozzárendelés létrehozása][az-role-assignment-create] parancsot az identitás szerepkör hozzárendeléséhez az erőforráshoz. 

A következő példa felügyelt identitást rendel hozzá a tároló-beállításjegyzékből való lekéréshez szükséges engedélyekhez. A parancs megadja a feladat identitásának *résztvevő-azonosítóját* és a cél beállításjegyzék *erőforrás-azonosítóját* .


```azurecli
az role assignment create \
  --assignee <principalID> \
  --scope <registryID> \
  --role acrpull
```

### <a name="4-optional-add-credentials-to-the-task"></a>4. (nem kötelező) hitelesítő adatok hozzáadása a feladathoz

Ha a feladatnak hitelesítő adatokra van szüksége a képek más egyéni beállításjegyzékbe való lekéréséhez vagy leküldéséhez, vagy más erőforrások eléréséhez, adja hozzá a hitelesítő adatokat a feladathoz Futtassa az az [ACR Task hitelesítőadat Add][az-acr-task-credential-add] parancsot a hitelesítő adatok hozzáadásához, és adja át a `--use-identity` paramétert annak jelzésére, hogy az identitás hozzáférhet a hitelesítő adatokhoz. 

Ha például hitelesítő adatokat szeretne hozzáadni egy rendszerhez rendelt identitáshoz az Azure Container Registry *targetregistry* való hitelesítéshez, adja meg a következőt `use-identity [system]` :

```azurecli
az acr task credential add \
    --name helloworld \
    --registry myregistry \
    --login-server targetregistry.azurecr.io \
    --use-identity [system]
```

Ha a felhasználó által hozzárendelt identitáshoz hitelesítő adatokat szeretne hozzáadni a beállításjegyzék *targetregistry* való hitelesítéshez, adja meg az `use-identity` identitás *ügyfél-azonosítójának* értékét. Például:

```azurecli
az acr task credential add \
    --name helloworld \
    --registry myregistry \
    --login-server targetregistry.azurecr.io \
    --use-identity <clientID>
```

Az identitás ügyfél-AZONOSÍTÓját az az [Identity show][az-identity-show] parancs futtatásával kérheti le. Az ügyfél-azonosító az űrlap GUID azonosítója `xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx` .

### <a name="5-run-the-task"></a>5. a feladat futtatása

Miután felügyelt identitással konfigurált egy feladatot, futtassa a feladatot. Ha például a cikkben létrehozott feladatok egyikét szeretné tesztelni, manuálisan aktiválja az az [ACR Task Run][az-acr-task-run] parancs használatával. Ha további, automatizált feladat-eseményindítókat konfigurált, a rendszer automatikusan elindítja a feladatot.

## <a name="next-steps"></a>Következő lépések

Ebben a cikkben megtanulta, hogyan engedélyezheti és használhatja a felhasználó által hozzárendelt vagy rendszerhez rendelt felügyelt identitást egy ACR-feladatban. A biztonságos erőforrásoknak a felügyelt identitás használatával való eléréséhez az ACR-feladatokból a következő témakörben talál további információt:

* [Több regisztrációs adatbázisra kiterjedő hitelesítés](container-registry-tasks-cross-registry-authentication.md)
* [Külső erőforrások elérése Azure Key Vaultban tárolt titkos kulcsokkal](container-registry-tasks-authentication-key-vault.md)


<!-- LINKS - Internal -->
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
[az-identity-create]: /cli/azure/identity#az-identity-create
[az-identity-show]: /cli/azure/identity#az-identity-show
[az-acr-task-create]: /cli/azure/acr/task#az-acr-task-create
[az-acr-task-run]: /cli/azure/acr/task#az-acr-task-run
[az-acr-task-credential-add]: /cli/azure/acr/task/credential#az-acr-task-credential-add
[azure-cli-install]: /cli/azure/install-azure-cli
