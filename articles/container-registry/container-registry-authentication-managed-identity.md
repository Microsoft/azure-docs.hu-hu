---
title: Hitelesítés felügyelt identitással
description: Hozzáférés biztosítása a privát tároló beállításjegyzékében lévő rendszerképekhez felhasználó által hozzárendelt vagy rendszer által hozzárendelt Azure-identitás használatával.
ms.topic: article
ms.date: 01/16/2019
ms.openlocfilehash: 2ab27e8548882b5bd296dc45e4bb74d3d6ba357b
ms.sourcegitcommit: b8995b7dafe6ee4b8c3c2b0c759b874dff74d96f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2021
ms.locfileid: "106285484"
---
# <a name="use-an-azure-managed-identity-to-authenticate-to-an-azure-container-registry"></a>Azure-beli felügyelt identitás használata az Azure Container registryben való hitelesítéshez 

[Felügyelt identitás használata az Azure-erőforrásokhoz](../active-directory/managed-identities-azure-resources/overview.md) egy másik Azure-erőforrásból származó Azure Container registryben való hitelesítéshez anélkül, hogy a beállításjegyzék hitelesítő adatait kellene megadnia vagy kezelnie. Beállíthat például egy felhasználó által hozzárendelt vagy rendszerhez rendelt felügyelt identitást egy Linux rendszerű virtuális gépen, hogy a tároló-beállításjegyzékből egyszerűen hozzáférjen a tároló-lemezképekhez. Vagy állítson be egy Azure Kubernetes Service-fürtöt a [felügyelt identitás](../aks/use-managed-identity.md) használatára, hogy lekérje a tároló lemezképeit Azure Container Registryról a pod-környezetek számára.

Ebben a cikkben többet tudhat meg a felügyelt identitásokról és az alábbiakról:

> [!div class="checklist"]
> * Felhasználó által hozzárendelt vagy rendszer által hozzárendelt identitás engedélyezése Azure-beli virtuális gépen
> * Az identitás hozzáférésének biztosítása egy Azure Container registryhez
> * A felügyelt identitás használata a beállításjegyzék eléréséhez és a tároló rendszerképének lekéréséhez 

Az Azure-erőforrások létrehozásához ehhez a cikkhez az Azure CLI 2.0.55 vagy újabb verzióját kell futtatnia. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése][azure-cli].

A tároló-beállításjegyzék beállításához és a tároló rendszerképének leküldéséhez helyileg kell telepíteni a Docker-t is. A Docker csomagokat biztosít, amelyekkel a Docker egyszerűen konfigurálható bármely [macOS][docker-mac], [Windows][docker-windows] vagy [Linux][docker-linux] rendszeren.

## <a name="why-use-a-managed-identity"></a>Miért érdemes felügyelt identitást használni?

Ha még nem ismeri az Azure-erőforrások felügyelt identitására vonatkozó funkciót, tekintse meg ezt az [áttekintést](../active-directory/managed-identities-azure-resources/overview.md).

Miután beállította a kiválasztott Azure-erőforrásokat egy felügyelt identitással, adjon egy másik erőforráshoz hozzáférő hozzáférést, ugyanúgy, mint bármely rendszerbiztonsági tag. Rendeljen hozzá például egy felügyelt identitást egy olyan szerepkörhöz, amely lekéréses, leküldéses és lekéréses, illetve más engedélyekkel rendelkezik az Azure-beli privát beállításjegyzék (A beállításjegyzék szerepköreinek teljes listáját lásd: [Azure Container Registry szerepkörök és engedélyek](container-registry-roles.md).) Identitás-hozzáférést biztosíthat egy vagy több erőforráshoz.

Ezt követően az identitás használatával hitelesítheti magát az [Azure ad-hitelesítést támogató szolgáltatásokban](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication)a kódban szereplő hitelesítő adatok nélkül. A forgatókönyvtől függően válassza ki, hogyan kell hitelesíteni a hitelesítést a felügyelt identitás használatával. Ha az identitást szeretné használni egy Azure Container Registry virtuális gépről való eléréséhez, akkor a Azure Resource Manager segítségével végezheti el a hitelesítést. 

> [!NOTE]
> Jelenleg a szolgáltatások, például az Azure Web App for Containers vagy Azure Container Instances nem használhatják a felügyelt identitást a Azure Container Registry való hitelesítéshez, amikor a tároló-lemezképet a tároló-erőforrás üzembe helyezéséhez húzza. Az identitás csak a tároló futása után érhető el. Ezeknek az erőforrásoknak a Azure Container Registryból származó rendszerképekkel való üzembe helyezéséhez egy másik hitelesítési módszer, például az [egyszerű szolgáltatásnév](container-registry-auth-service-principal.md) használata javasolt.

## <a name="create-a-container-registry"></a>Tárolóregisztrációs adatbázis létrehozása

Ha még nem rendelkezik Azure Container Registry-regisztrációval, hozzon létre egy beállításjegyzéket, és küldje el a minta-tároló képét. A lépéseket a rövid útmutató [: privát tároló beállításjegyzékének létrehozása az Azure CLI használatával](container-registry-get-started-azure-cli.md)című témakörben tekintheti meg.

Ez a cikk azt feltételezi, hogy a `aci-helloworld:v1` tároló rendszerképe a beállításjegyzékben van tárolva. A példák a *myContainerRegistry* beállításjegyzékbeli nevét használják. Cserélje le a változót a saját beállításjegyzékére és a képek nevére a későbbi lépésekben.

## <a name="create-a-docker-enabled-vm"></a>Docker-kompatibilis virtuális gép létrehozása

Hozzon létre egy Docker-kompatibilis Ubuntu virtuális gépet. Telepítenie kell az [Azure CLI](/cli/azure/install-azure-cli) -t is a virtuális gépre. Ha már rendelkezik Azure-beli virtuális géppel, ugorja át ezt a lépést a virtuális gép létrehozásához.

Telepítsen egy alapértelmezett Ubuntu Azure-beli virtuális gépet az [az VM Create][az-vm-create]paranccsal. Az alábbi példa egy *myDockerVM* nevű virtuális gépet hoz létre egy *myResourceGroup* nevű meglévő erőforráscsoporthoz:

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myDockerVM \
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys
```

A virtuális gép létrehozása néhány percig tart. Ha a parancs befejeződik, jegyezze fel az `publicIpAddress` Azure CLI által megjelenített adatmennyiséget. Ezzel a címtől SSH-kapcsolatokat hozhat a virtuális géphez.

### <a name="install-docker-on-the-vm"></a>A Docker telepítése a virtuális gépre

A virtuális gép futása után létesítsen SSH-kapcsolatokat a virtuális géppel. Cserélje le a *publicIpAddress* -t a virtuális gép nyilvános IP-címére.

```bash
ssh azureuser@publicIpAddress
```

Futtassa a következő parancsot a Docker telepítéséhez a virtuális gépen:

```bash
sudo apt update
sudo apt install docker.io -y
```

A telepítés után futtassa a következő parancsot annak ellenőrzéséhez, hogy a Docker megfelelően fut-e a virtuális gépen:

```bash
sudo docker run -it mcr.microsoft.com/hello-world
```

Kimenet:

```
Hello from Docker!
This message shows that your installation appears to be working correctly.
[...]
```

### <a name="install-the-azure-cli"></a>Az Azure CLI összetevő telepítése

Az Azure CLI az Ubuntu rendszerű virtuális gépen való telepítéséhez kövesse az Azure CLI az [apt-vel](/cli/azure/install-azure-cli-apt) való telepítésének lépéseit. Ehhez a cikkhez győződjön meg arról, hogy a 2.0.55 vagy újabb verzióját telepíti.

Lépjen ki az SSH-munkamenetből.

## <a name="example-1-access-with-a-user-assigned-identity"></a>1. példa: hozzáférés felhasználó által hozzárendelt identitással

### <a name="create-an-identity"></a>Identitás létrehozása

Hozzon létre egy identitást az előfizetésben az az [Identity Create](/cli/azure/identity#az_identity_create) paranccsal. Használhatja ugyanazt az erőforráscsoportot, amelyet korábban használt a tároló-beállításjegyzék vagy a virtuális gép létrehozásához, vagy egy másikat.

```azurecli-interactive
az identity create --resource-group myResourceGroup --name myACRId
```

Az identitásnak az alábbi lépésekben való konfigurálásához használja az [az Identity show] [az_identity_show] parancsot az identitás erőforrás-AZONOSÍTÓjának és egyszerű szolgáltatásnév AZONOSÍTÓjának a változókban való tárolásához.

```azurecli
# Get resource ID of the user-assigned identity
userID=$(az identity show --resource-group myResourceGroup --name myACRId --query id --output tsv)

# Get service principal ID of the user-assigned identity
spID=$(az identity show --resource-group myResourceGroup --name myACRId --query principalId --output tsv)
```

Mivel az Identity AZONOSÍTÓját egy későbbi lépésben kell megadnia, amikor bejelentkezik a CLI-be a virtuális gépről, a következő értéket jeleníti meg:

```bash
echo $userID
```

Az azonosító a következőket képezi:

```
/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myACRId
```

### <a name="configure-the-vm-with-the-identity"></a>A virtuális gép konfigurálása az identitással

A következő az [VM Identity assign][az-vm-identity-assign] paranccsal konfigurálja a DOCKER virtuális gépet a felhasználó által hozzárendelt identitással:

```azurecli
az vm identity assign --resource-group myResourceGroup --name myDockerVM --identities $userID
```

### <a name="grant-identity-access-to-the-container-registry"></a>Identitás-hozzáférés biztosítása a tároló beállításjegyzékéhez

Most konfigurálja az identitást a tároló-beállításjegyzék eléréséhez. Először használja az az [ACR show][az-acr-show] parancsot a beállításjegyzék erőforrás-azonosítójának lekéréséhez:

```azurecli
resourceID=$(az acr show --resource-group myResourceGroup --name myContainerRegistry --query id --output tsv)
```

Az az [role hozzárendelés Create][az-role-assignment-create] paranccsal rendelje hozzá a AcrPull szerepkört a beállításjegyzékhez. Ez a szerepkör [lekéréses engedélyeket](container-registry-roles.md) biztosít a beállításjegyzéknek. A lekéréses és leküldéses engedélyek megadásához rendelje hozzá a ACRPush szerepkört.

```azurecli
az role assignment create --assignee $spID --scope $resourceID --role acrpull
```

### <a name="use-the-identity-to-access-the-registry"></a>Az identitás használata a beállításjegyzék eléréséhez

SSH-t az identitással konfigurált Docker virtuális gépre. Futtassa az alábbi Azure CLI-parancsokat a virtuális gépen telepített Azure CLI használatával.

Először végezze el a hitelesítést az Azure CLI-vel az az [login][az-login]paranccsal, a virtuális gépen konfigurált identitás használatával. A esetében cserélje le az `<userID>` előző lépésben lekért identitás azonosítóját. 

```azurecli
az login --identity --username <userID>
```

Ezután hitelesítse magát a beállításjegyzékben az [az ACR login][az-acr-login]paranccsal. Ha ezt a parancsot használja, a CLI az Active Directory tokent használja, amely akkor jön létre, amikor a `az login` munkamenetet a tároló beállításjegyzékével zökkenőmentesen hitelesíti. (A virtuális gép telepítésének függvényében szükség lehet a parancs és a Docker-parancsok futtatására `sudo` .)

```azurecli
az acr login --name myContainerRegistry
```

Egy üzenetnek kell megjelennie `Login succeeded` . Ezután parancsok futtatásával `docker` hitelesítő adatok megadása nélkül is futtathat parancsokat. Például futtassa a [Docker pull][docker-pull] parancsot a rendszerkép lekéréséhez `aci-helloworld:v1` , adja meg a beállításjegyzék bejelentkezési kiszolgálójának nevét. A bejelentkezési kiszolgáló neve a tároló-beállításjegyzék neve (az összes kisbetűs), majd a (z) `.azurecr.io` , például: `mycontainerregistry.azurecr.io` .

```
docker pull mycontainerregistry.azurecr.io/aci-helloworld:v1
```

## <a name="example-2-access-with-a-system-assigned-identity"></a>2. példa: hozzáférés rendszerhez rendelt identitással

### <a name="configure-the-vm-with-a-system-managed-identity"></a>A virtuális gép konfigurálása rendszer által felügyelt identitással

A következő az [VM Identity assign][az-vm-identity-assign] paranccsal konfigurálhatja a DOCKER virtuális gépet egy rendszerhez rendelt identitással:

```azurecli
az vm identity assign --resource-group myResourceGroup --name myDockerVM 
```

Az az [VM show][az-vm-show] paranccsal állítson be egy változót a `principalId` virtuális gép identitásának (a szolgáltatás egyszerű azonosítója) értékére a későbbi lépésekben való használatra.

```azurecli-interactive
spID=$(az vm show --resource-group myResourceGroup --name myDockerVM --query identity.principalId --out tsv)
```

### <a name="grant-identity-access-to-the-container-registry"></a>Identitás-hozzáférés biztosítása a tároló beállításjegyzékéhez

Most konfigurálja az identitást a tároló-beállításjegyzék eléréséhez. Először használja az az [ACR show][az-acr-show] parancsot a beállításjegyzék erőforrás-azonosítójának lekéréséhez:

```azurecli
resourceID=$(az acr show --resource-group myResourceGroup --name myContainerRegistry --query id --output tsv)
```

Az az [szerepkör-hozzárendelés létrehozási][az-role-assignment-create] parancs használatával rendelje hozzá a AcrPull szerepkört az identitáshoz. Ez a szerepkör [lekéréses engedélyeket](container-registry-roles.md) biztosít a beállításjegyzéknek. A lekéréses és leküldéses engedélyek megadásához rendelje hozzá a ACRPush szerepkört.

```azurecli
az role assignment create --assignee $spID --scope $resourceID --role acrpull
```

### <a name="use-the-identity-to-access-the-registry"></a>Az identitás használata a beállításjegyzék eléréséhez

SSH-t az identitással konfigurált Docker virtuális gépre. Futtassa az alábbi Azure CLI-parancsokat a virtuális gépen telepített Azure CLI használatával.

Először hitelesítse az Azure CLI-t az [az login][az-login]paranccsal, a rendszer által hozzárendelt identitás használatával a virtuális gépen.

```azurecli
az login --identity
```

Ezután hitelesítse magát a beállításjegyzékben az [az ACR login][az-acr-login]paranccsal. Ha ezt a parancsot használja, a CLI az Active Directory tokent használja, amely akkor jön létre, amikor a `az login` munkamenetet a tároló beállításjegyzékével zökkenőmentesen hitelesíti. (A virtuális gép telepítésének függvényében szükség lehet a parancs és a Docker-parancsok futtatására `sudo` .)

```azurecli
az acr login --name myContainerRegistry
```

Egy üzenetnek kell megjelennie `Login succeeded` . Ezután parancsok futtatásával `docker` hitelesítő adatok megadása nélkül is futtathat parancsokat. Például futtassa a [Docker pull][docker-pull] parancsot a rendszerkép lekéréséhez `aci-helloworld:v1` , adja meg a beállításjegyzék bejelentkezési kiszolgálójának nevét. A bejelentkezési kiszolgáló neve a tároló-beállításjegyzék neve (az összes kisbetűs), majd a (z) `.azurecr.io` , például: `mycontainerregistry.azurecr.io` .

```
docker pull mycontainerregistry.azurecr.io/aci-helloworld:v1
```

## <a name="next-steps"></a>Következő lépések

Ebből a cikkből megtudhatta, hogyan használhatja a felügyelt identitásokat a Azure Container Registry és a következőket:

> [!div class="checklist"]
> * Felhasználó által hozzárendelt vagy rendszerhez rendelt identitás engedélyezése egy Azure-beli virtuális gépen
> * Az identitás hozzáférésének biztosítása egy Azure Container registryhez
> * A felügyelt identitás használata a beállításjegyzék eléréséhez és a tároló rendszerképének lekéréséhez

* További információ az [Azure-erőforrások felügyelt identitásáról](../active-directory/managed-identities-azure-resources/index.yml).


<!-- LINKS - external -->
[docker-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-login]: https://docs.docker.com/engine/reference/commandline/login/
[docker-mac]: https://docs.docker.com/docker-for-mac/
[docker-pull]: https://docs.docker.com/engine/reference/commandline/pull/
[docker-windows]: https://docs.docker.com/docker-for-windows/

<!-- LINKS - Internal -->
[az-login]: /cli/azure/reference-index#az-login
[az-acr-login]: /cli/azure/acr#az-acr-login
[az-acr-show]: /cli/azure/acr#az-acr-show
[az-vm-create]: /cli/azure/vm#az-vm-create
[az-vm-show]: /cli/azure/vm#az-vm-show
[az-vm-identity-assign]: /cli/azure/vm/identity#az-vm-identity-assign
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
[az-acr-login]: /cli/azure/acr#az-acr-login
[az-identity-show]: /cli/azure/identity#az-identity-show
[azure-cli]: /cli/azure/install-azure-cli
