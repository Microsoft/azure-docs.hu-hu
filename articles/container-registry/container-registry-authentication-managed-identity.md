---
title: Hitelesítés felügyelt identitással
description: Hozzáférést biztosít a privát tárolójegyzék rendszerképéhez egy felhasználó által hozzárendelt vagy rendszer által hozzárendelt felügyelt Azure-identitással.
ms.topic: article
ms.date: 01/16/2019
ms.openlocfilehash: 213f49356fdc2444f8bc2cb4635e96015aff0a61
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107781540"
---
# <a name="use-an-azure-managed-identity-to-authenticate-to-an-azure-container-registry"></a>Azure-beli felügyelt identitás használata az Azure Container Registryben való hitelesítéshez 

Az [Azure-erőforrások felügyelt](../active-directory/managed-identities-azure-resources/overview.md) identitásával anélkül hitelesíthet egy Azure-beli tárolójegyzékben egy másik Azure-erőforrásból, hogy meg kellene adnia vagy kezelnie kellene a regisztrációs adatbázis hitelesítő adatait. Például állítson be egy felhasználó által hozzárendelt vagy rendszer által hozzárendelt felügyelt identitást egy Linux rendszerű virtuális gépen a tároló-beállításjegyzék tárolórendszerképének elérésére, amilyen egyszerűen csak egy nyilvános regisztrációs adatbázist használ. Másik lehetőségként állítson be egy [](../aks/use-managed-identity.md) Azure Kubernetes Service fürtöt a felügyelt identitása használatával a tárolólemezképek le Azure Container Registry a podok üzembe helyezéséhez.

Ebben a cikkben a felügyelt identitásokkal és a következővel ismerkedik meg:

> [!div class="checklist"]
> * Felhasználó vagy rendszer által hozzárendelt identitás engedélyezése Azure-beli virtuális gépen
> * Hozzáférés megadása az identitás számára egy Azure Container Registryhez
> * A felügyelt identitás használata a regisztrációs adatbázis eléréséhez és egy tároló rendszerképének lekértéhez 

Az Azure-erőforrások létrehozásához ehhez a cikkhez az Azure CLI 2.0.55-ös vagy újabb verziójára lesz szükség. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése][azure-cli].

A tároló-beállításjegyzék beállításához és a tároló rendszerképének leküldéséhez a Docker helyileg is telepítve kell lennie. A Docker csomagokat biztosít, amelyekkel a Docker egyszerűen konfigurálható bármely [macOS][docker-mac], [Windows][docker-windows] vagy [Linux][docker-linux] rendszeren.

## <a name="why-use-a-managed-identity"></a>Miért érdemes felügyelt identitást használni?

Ha még nem ismeri az Azure-erőforrások felügyelt identitására vonatkozó funkciót, tekintse meg ezt az [áttekintést](../active-directory/managed-identities-azure-resources/overview.md).

Miután beállította a kiválasztott Azure-erőforrásokat egy felügyelt identitással, adjon hozzáférést az identitásnak egy másik erőforráshoz, mint bármely más rendszerbiztonsági tag. Hozzárendelhet például egy felügyelt identitáshoz egy leküldéses, leküldéses és leküldéses szerepkört vagy más engedélyeket egy Privát regisztrációs adatbázishoz az Azure-ban. (A beállításjegyzék-szerepkörök teljes listájáért tekintse meg a Azure Container Registry [és engedélyeket.)](container-registry-roles.md) Egy identitásnak hozzáférést adhat egy vagy több erőforráshoz.

Ezután az identitás használatával hitelesítheti magát bármely olyan szolgáltatásban, amely támogatja az [Azure AD-hitelesítést,](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication)anélkül, hogy a hitelesítő adatokat a kódban lenne. A forgatókönyvtől függően válassza ki a felügyelt identitással való hitelesítés módszerét. Ahhoz, hogy az identitás használatával hozzáférjen egy Azure Container Registryhez egy virtuális gépről, hitelesítést kell végeznie a Azure Resource Manager. 

> [!NOTE]
> Jelenleg az Olyan szolgáltatások, mint az Azure Web App for Containers vagy Azure Container Instances nem használhatja a felügyelt identitásukat az Azure Container Registry-val való hitelesítéshez, amikor tároló-rendszerképet kér le magának a tárolóerőforrásnak az üzembe helyezéséhez. Az identitás csak a tároló futtatása után érhető el. Ha ezeket az erőforrásokat rendszerképekkel Azure Container Registry üzembe, egy másik hitelesítési módszer, például [szolgáltatásnév](container-registry-auth-service-principal.md) használata javasolt.

## <a name="create-a-container-registry"></a>Tárolóregisztrációs adatbázis létrehozása

Ha még nem rendelkezik Azure Container Registryvel, hozzon létre egy regisztrációs adatbázist, és leküldéses egy minta tároló rendszerképet. A lépésekért lásd: Rövid útmutató: Privát tároló-beállításjegyzék létrehozása [az Azure CLI használatával.](container-registry-get-started-azure-cli.md)

Ez a cikk feltételezi, hogy a tároló `aci-helloworld:v1` rendszerképe a regisztrációs adatbázisban van tárolva. A példák a *myContainerRegistry beállításjegyzék-nevet használják.* A későbbi lépésekben cserélje le a helyére a saját regisztrációs adatbázisát és rendszerképneveit.

## <a name="create-a-docker-enabled-vm"></a>Docker-kompatibilis virtuális gép létrehozása

Docker-kompatibilis Ubuntu virtuális gép létrehozása. Az Azure CLI-t is [telepítenie](/cli/azure/install-azure-cli) kell a virtuális gépen. Ha már rendelkezik Azure-beli virtuális géppel, hagyja ki ezt a lépést a virtuális gép létrehozásához.

Egy alapértelmezett Ubuntu Azure-beli virtuális gép üzembe helyezése [az az vm create használatával.][az-vm-create] A következő példa létrehoz egy *myDockerVM* nevű virtuális gépet egy *meglévő, myResourceGroup nevű erőforráscsoportban:*

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myDockerVM \
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys
```

A virtuális gép létrehozása néhány percig tart. Amikor a parancs befejeződik, jegyezze fel az `publicIpAddress` Azure CLI által megjelenített üzenetet. Ezzel a címmel SSH-kapcsolatokat létesít a virtuális géphez.

### <a name="install-docker-on-the-vm"></a>A Docker telepítése a virtuális gépen

A virtuális gép futtatása után létesítsen SSH-kapcsolatot a virtuális géphez. Cserélje *le a publicIpAddress* helyére a virtuális gép nyilvános IP-címét.

```bash
ssh azureuser@publicIpAddress
```

Futtassa a következő parancsot a Docker virtuális gépre való telepítéséhez:

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

Az Azure CLI ubuntus virtuális gépre való telepítéséhez kövesse az Azure CLI telepítése az [apt](/cli/azure/install-azure-cli-apt) használatával részben található lépéseket. Ebben a cikkben a 2.0.55-ös vagy újabb verziót telepítse.

Lépjen ki az SSH-munkamenetből.

## <a name="example-1-access-with-a-user-assigned-identity"></a>1. példa: Hozzáférés felhasználó által hozzárendelt identitással

### <a name="create-an-identity"></a>Identitás létrehozása

Hozzon létre egy identitást az előfizetésben [az az identity create paranccsal.](/cli/azure/identity#az_identity_create) Használhatja ugyanazt az erőforráscsoportot, amelyet korábban a tároló-beállításjegyzék vagy a virtuális gép létrehozásához használt, vagy egy másikat.

```azurecli-interactive
az identity create --resource-group myResourceGroup --name myACRId
```

Az identitás a következő lépésekben való konfigurálásához használja az [az identity show][az_identity_show] parancsot az identitás erőforrás-azonosítójának és szolgáltatásnév-azonosítójának változókban való tárolására.

```azurecli
# Get resource ID of the user-assigned identity
userID=$(az identity show --resource-group myResourceGroup --name myACRId --query id --output tsv)

# Get service principal ID of the user-assigned identity
spID=$(az identity show --resource-group myResourceGroup --name myACRId --query principalId --output tsv)
```

Mivel egy későbbi lépésben szüksége lesz az identitás azonosítójára, amikor bejelentkezik a CLI-be a virtuális gépről, mutassa az értéket:

```bash
echo $userID
```

Az azonosító a következő formában van megszabadva:

```
/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myACRId
```

### <a name="configure-the-vm-with-the-identity"></a>A virtuális gép konfigurálása az identitással

Az alábbi [az vm identity assign][az-vm-identity-assign] parancs a felhasználó által hozzárendelt identitással konfigurálja a Docker virtuális gépet:

```azurecli
az vm identity assign --resource-group myResourceGroup --name myDockerVM --identities $userID
```

### <a name="grant-identity-access-to-the-container-registry"></a>Identitás-hozzáférés megadása a tároló-beállításjegyzékhez

Most konfigurálja az identitást a tároló-beállításjegyzék eléréséhez. Először az [az acr show paranccsal][az-acr-show] szerezze be a regisztrációs adatbázis erőforrás-azonosítóját:

```azurecli
resourceID=$(az acr show --resource-group myResourceGroup --name myContainerRegistry --query id --output tsv)
```

Az [az role assignment create paranccsal][az-role-assignment-create] rendelje hozzá az AcrPull szerepkört a beállításjegyzékhez. Ez a szerepkör [lekért engedélyeket biztosít](container-registry-roles.md) a regisztrációs adatbázishoz. Leküldési és leküldési engedélyeket is az ACRPush szerepkör hozzárendelésével adhat meg.

```azurecli
az role assignment create --assignee $spID --scope $resourceID --role acrpull
```

### <a name="use-the-identity-to-access-the-registry"></a>A beállításjegyzék elérése az identitással

SSH-kapcsolat az identitással konfigurált Docker virtuális géppel. Futtassa a következő Azure CLI-parancsokat a virtuális gépen telepített Azure CLI használatával.

Először hitelesítse magát az Azure CLI-hez [az az login használatával][az-login]a virtuális gépen konfigurált identitással. A helyett helyettesítse be az előző lépésben lekért `<userID>` identitás azonosítóját. 

```azurecli
az login --identity --username <userID>
```

Ezután hitelesítse magát a beállításjegyzékben [az az acr login fiókkal.][az-acr-login] Ha ezt a parancsot használja, a CLI a futtatáskor létrehozott Active Directory jogkivonatot használja a munkamenet zökkenőmentes hitelesítéséhez a `az login` tároló-beállításjegyzékben. (A virtuális gép beállításaitól függően előfordulhat, hogy a paranccsal kell futtatnia ezt a parancsot és a docker-parancsokat. `sudo`

```azurecli
az acr login --name myContainerRegistry
```

Megjelenik egy `Login succeeded` üzenet. Ezután hitelesítő adatok `docker` megadása nélkül futtathat parancsokat. Futtassa például a [docker pull (docker][docker-pull] lekért) adatokat a rendszerkép lekért nevének megadásával a `aci-helloworld:v1` regisztrációs adatbázis bejelentkezési kiszolgálójának nevével. A bejelentkezési kiszolgáló neve a tároló-beállításjegyzék nevéből (csak kisbetűkből) áll, amelyet követ `.azurecr.io` – például `mycontainerregistry.azurecr.io` : .

```
docker pull mycontainerregistry.azurecr.io/aci-helloworld:v1
```

## <a name="example-2-access-with-a-system-assigned-identity"></a>2. példa: Hozzáférés rendszer által hozzárendelt identitással

### <a name="configure-the-vm-with-a-system-managed-identity"></a>A virtuális gép konfigurálása rendszer által felügyelt identitással

Az alábbi [az vm identity assign][az-vm-identity-assign] parancs rendszer által hozzárendelt identitással konfigurálja a Docker virtuális gépet:

```azurecli
az vm identity assign --resource-group myResourceGroup --name myDockerVM 
```

Az [az vm show paranccsal][az-vm-show] állítson be egy változót a VM identitásának értékére (a szolgáltatásnév azonosítója) a későbbi `principalId` lépésekben való használathoz.

```azurecli-interactive
spID=$(az vm show --resource-group myResourceGroup --name myDockerVM --query identity.principalId --out tsv)
```

### <a name="grant-identity-access-to-the-container-registry"></a>Identitás-hozzáférés megadása a tároló-beállításjegyzékhez

Most konfigurálja az identitást a tároló-beállításjegyzék eléréséhez. Először az [az acr show paranccsal][az-acr-show] szerezze be a regisztrációs adatbázis erőforrás-azonosítóját:

```azurecli
resourceID=$(az acr show --resource-group myResourceGroup --name myContainerRegistry --query id --output tsv)
```

Az [az role assignment create paranccsal][az-role-assignment-create] rendelje hozzá az AcrPull szerepkört az identitáshoz. Ez a szerepkör [lekért engedélyeket biztosít](container-registry-roles.md) a beállításjegyzékhez. Leküldési és leküldési engedélyeket is az ACRPush szerepkör hozzárendelésével adhat meg.

```azurecli
az role assignment create --assignee $spID --scope $resourceID --role acrpull
```

### <a name="use-the-identity-to-access-the-registry"></a>A beállításjegyzék elérése az identitással

SSH-kapcsolat az identitással konfigurált Docker virtuális géppel. Futtassa a következő Azure CLI-parancsokat a virtuális gépen telepített Azure CLI használatával.

Először hitelesítse az Azure CLI-t [az az login használatával][az-login]a rendszer által hozzárendelt identitással a virtuális gépen.

```azurecli
az login --identity
```

Ezután hitelesítse magát a beállításjegyzékben [az az acr login fiókkal.][az-acr-login] Ha ezt a parancsot használja, a CLI a futtatáskor létrehozott Active Directory jogkivonatot használja a munkamenet zökkenőmentes hitelesítéséhez a `az login` tároló-beállításjegyzékben. (A virtuális gép beállításaitól függően előfordulhat, hogy a paranccsal kell futtatnia a docker-parancsokat. `sudo`

```azurecli
az acr login --name myContainerRegistry
```

Megjelenik egy `Login succeeded` üzenet. Ezután hitelesítő adatok `docker` megadása nélkül futtathat parancsokat. Futtassa például a [docker pull(docker pull)][docker-pull] adatokat a rendszerkép lekért értékéhez, és adja meg a regisztrációs adatbázis bejelentkezési `aci-helloworld:v1` kiszolgálójának nevét. A bejelentkezési kiszolgáló neve a tároló-beállításjegyzék nevéből (csak kisbetűkből) áll, majd ezt követi `.azurecr.io` – például `mycontainerregistry.azurecr.io` : .

```
docker pull mycontainerregistry.azurecr.io/aci-helloworld:v1
```

## <a name="next-steps"></a>Következő lépések

Ebben a cikkben a felügyelt identitások felügyelt identitásokkal való Azure Container Registry és a következővel:

> [!div class="checklist"]
> * Felhasználó vagy rendszer által hozzárendelt identitás engedélyezése Azure-beli virtuális gépen
> * Hozzáférés megadása az identitás számára egy Azure Container Registryhez
> * A felügyelt identitás használata a regisztrációs adatbázis eléréséhez és egy tároló rendszerképének lekértéhez

* További információ az [Azure-erőforrások felügyelt identitási szolgáltatásról.](../active-directory/managed-identities-azure-resources/index.yml)


<!-- LINKS - external -->
[docker-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-login]: https://docs.docker.com/engine/reference/commandline/login/
[docker-mac]: https://docs.docker.com/docker-for-mac/
[docker-pull]: https://docs.docker.com/engine/reference/commandline/pull/
[docker-windows]: https://docs.docker.com/docker-for-windows/

<!-- LINKS - Internal -->
[az-login]: /cli/azure/reference-index#az_login
[az-acr-login]: /cli/azure/acr#az_acr_login
[az-acr-show]: /cli/azure/acr#az_acr_show
[az-vm-create]: /cli/azure/vm#az_vm_create
[az-vm-show]: /cli/azure/vm#az_vm_show
[az-vm-identity-assign]: /cli/azure/vm/identity#az_vm_identity_assign
[az-role-assignment-create]: /cli/azure/role/assignment#az_role_assignment_create
[az-acr-login]: /cli/azure/acr#az_acr_login
[az-identity-show]: /cli/azure/identity#az_identity_show
[azure-cli]: /cli/azure/install-azure-cli
