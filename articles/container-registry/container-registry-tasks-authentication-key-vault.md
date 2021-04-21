---
title: Külső hitelesítés az ACR-feladatból
description: Konfigurál egy Azure Container Registry feladatot (ACR-feladat) egy Azure-Docker Hub-kulcstartóban tárolt hitelesítő adatok olvasására az Azure-erőforrások felügyelt identitásának használatával.
ms.topic: article
ms.date: 07/06/2020
ms.openlocfilehash: 16404f9244818d91c5333eb5eec5944bfdd9df98
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107781198"
---
# <a name="external-authentication-in-an-acr-task-using-an-azure-managed-identity"></a>Külső hitelesítés egy ACR-feladatban azure-beli felügyelt identitás használatával 

Egy [ACR-feladatban](container-registry-tasks-overview.md)engedélyezheti [a felügyelt identitást az Azure-erőforrásokhoz.](container-registry-tasks-authentication-managed-identity.md) A feladat az identitás használatával hozzáférhet más Azure-erőforrásokhoz anélkül, hogy hitelesítő adatokat kellene megadnia vagy kezelnie. 

Ebből a cikkből megtudhatja, hogyan engedélyezheti a felügyelt identitásokat egy olyan feladatban, amely hozzáfér az Azure Key Vaultban tárolt titkos kulcsokhoz. 

Az Azure-erőforrások létrehozásához ehhez a cikkhez az Azure CLI 2.0.68-as vagy újabb verziójára lesz szükség. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése][azure-cli].

## <a name="scenario-overview"></a>A forgatókönyv áttekintése

A példafeladat beolvassa Docker Hub Azure Key Vaultban tárolt hitelesítő adatokat. A hitelesítő adatok olyan Docker Hub fiókhoz valók, amely írási (leküldési) engedélyekkel rendelkezik egy privát Docker Hub tárházba. A hitelesítő adatok olvasásához konfigurálja a feladatot egy felügyelt identitással, és rendelje hozzá a megfelelő engedélyeket. Az identitáshoz társított feladat egy rendszerképet hoz létre, és bejelentkezik a Docker Hub, hogy a rendszerképet a privát repo-ba lek push-re. 

Ez a példa felhasználó vagy rendszer által hozzárendelt felügyelt identitás használatának lépéseit mutatja be. Az identitásválasztás a szervezet igényeitől függ.

Valós helyzetben a vállalat a buildfolyamat részeként közzétehet képeket egy privát Docker Hub egy privát Docker Hub számára. 

## <a name="prerequisites"></a>Előfeltételek

Szüksége van egy Azure Container Registryre, amelyben a feladatot futtatja. Ebben a cikkben ennek a regisztrációs adatbázisnak a *myregistry* a neve. A későbbi lépésekben cserélje le a helyére a saját regisztrációs adatbázisának nevét.

Ha még nem rendelkezik Azure Container Registryvel, tekintse meg a következőt: Rövid útmutató: Privát tároló-beállításjegyzék létrehozása [az Azure CLI használatával.](container-registry-get-started-azure-cli.md) Még nem kell rendszerképeket leküldést a regisztrációs adatbázisba.

Szüksége lesz egy privát adattárra a Docker Hub, valamint egy Docker Hub-fiókra, amely rendelkezik az adattárba való íráshoz szükséges engedélyekkel. Ebben a példában ennek az objektumnak a neve *hubuser/hubrepo.* 

## <a name="create-a-key-vault-and-store-secrets"></a>Kulcstartó létrehozása és titkos kulcsok tárolása

Először is, ha szükséges, hozzon létre egy *myResourceGroup* nevű erőforráscsoportot az *eastus* helyen az alábbi [az group create paranccsal:][az-group-create]

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Kulcstartó létrehozásához használja az [az keyvault create][az-keyvault-create] parancsot. Mindenképpen egyedi kulcstartónevet adjon meg. 

```azurecli-interactive
az keyvault create --name mykeyvault --resource-group myResourceGroup --location eastus
```

Tárolja a Docker Hub hitelesítő adatokat a kulcstartóban az [az keyvault secret set paranccsal.][az-keyvault-secret-set] Ezekben a parancsokban az értékek környezeti változókban vannak átkedve:

```azurecli
# Store Docker Hub user name
az keyvault secret set \
  --name UserName \
  --value $USERNAME \
  --vault-name mykeyvault

# Store Docker Hub password
az keyvault secret set \
  --name Password \
  --value $PASSWORD \
  --vault-name mykeyvault
```

Egy valós forgatókönyvben a titkos kulcsok valószínűleg egy külön folyamatban vannak beállítva és karbantartva.

## <a name="define-task-steps-in-yaml-file"></a>Feladat lépései a YAML-fájlban

A példafeladat lépései egy [YAML-fájlban vannak definiálva.](container-registry-tasks-reference-yaml.md) Hozzon létre egy nevű `dockerhubtask.yaml` fájlt egy helyi munkakönyvtárban, és illessze be az alábbi tartalmat. Mindenképpen cserélje le a fájlban található kulcstartó nevét a kulcstartó nevére.

```yml
version: v1.1.0
# Replace mykeyvault with the name of your key vault
secrets:
  - id: username
    keyvault: https://mykeyvault.vault.azure.net/secrets/UserName
  - id: password
    keyvault: https://mykeyvault.vault.azure.net/secrets/Password
steps:
# Log in to Docker Hub
  - cmd: bash echo '{{.Secrets.password}}' | docker login --username '{{.Secrets.username}}' --password-stdin 
# Build image
  - build: -t {{.Values.PrivateRepo}}:$ID https://github.com/Azure-Samples/acr-tasks.git -f hello-world.dockerfile
# Push image to private repo in Docker Hub
  - push:
    - {{.Values.PrivateRepo}}:$ID
```

A feladat lépései a következőket ják:

* Titkos hitelesítő adatok kezelése az Docker Hub.
* Hitelesítés Docker Hub a titkos kulcsok a parancsnak való `docker login` átadásával.
* Rendszerképet az [Azure-Samples/acr-tasks](https://github.com/Azure-Samples/acr-tasks.git) adattára mintáként használt Docker-fájlját használva buildelhet.
* A rendszerkép leküldése a privát Docker Hub tárházba.


## <a name="option-1-create-task-with-user-assigned-identity"></a>1. lehetőség: Feladat létrehozása felhasználó által hozzárendelt identitással

Az ebben a szakaszban található lépések létrehoznak egy feladatot, és engedélyezik a felhasználó által hozzárendelt identitást. Ha ehelyett rendszer által hozzárendelt identitást szeretne engedélyezni, lásd: 2. lehetőség: Feladat létrehozása rendszer által [hozzárendelt identitással.](#option-2-create-task-with-system-assigned-identity) 

[!INCLUDE [container-registry-tasks-user-assigned-id](../../includes/container-registry-tasks-user-assigned-id.md)]

### <a name="create-task"></a>Feladat létrehozása

Hozza létre a *dockerhubtask* feladatot a következő [az acr task create parancs végrehajtásával.][az-acr-task-create] A feladat forráskódkörnyezet nélkül fut, és a parancs a munkakönyvtárban `dockerhubtask.yaml` található fájlra hivatkozik. A paraméter átadja a felhasználó által hozzárendelt identitás `--assign-identity` erőforrás-azonosítóját. 

```azurecli
az acr task create \
  --name dockerhubtask \
  --registry myregistry \
  --context /dev/null \
  --file dockerhubtask.yaml \
  --assign-identity $resourceID
```

[!INCLUDE [container-registry-tasks-user-id-properties](../../includes/container-registry-tasks-user-id-properties.md)]


### <a name="grant-identity-access-to-key-vault"></a>Identitás-hozzáférés megadása a Key Vaulthoz

Futtassa a [következő az keyvault set-policy parancsot][az-keyvault-set-policy] egy hozzáférési szabályzat kulcstartón való beállítására. Az alábbi példa lehetővé teszi, hogy az identitás beolvassa a titkos kulcsokat a kulcstartóból. 

```azurecli
az keyvault set-policy --name mykeyvault \
  --resource-group myResourceGroup \
  --object-id $principalID \
  --secret-permissions get
```

Folytassa a [feladat manuális futtatásával.](#manually-run-the-task)

## <a name="option-2-create-task-with-system-assigned-identity"></a>2. lehetőség: Feladat létrehozása rendszer által hozzárendelt identitással

Az ebben a szakaszban található lépések létrehoznak egy feladatot, és engedélyezik a rendszer által hozzárendelt identitást. Ha ehelyett felhasználó által hozzárendelt identitást szeretne engedélyezni, lásd: 1. lehetőség: Feladat létrehozása felhasználó által [hozzárendelt identitással.](#option-1-create-task-with-user-assigned-identity) 

### <a name="create-task"></a>Feladat létrehozása

Hozza létre a *dockerhubtask* feladatot a következő [az acr task create parancs végrehajtásával.][az-acr-task-create] A feladat forráskódkörnyezet nélkül fut, és a parancs a munkakönyvtárban `dockerhubtask.yaml` található fájlra hivatkozik. A `--assign-identity` paraméter érték nélkül engedélyezi a rendszer által hozzárendelt identitást a feladaton.  

```azurecli
az acr task create \
  --name dockerhubtask \
  --registry myregistry \
  --context /dev/null \
  --file dockerhubtask.yaml \
  --assign-identity 
```

[!INCLUDE [container-registry-tasks-system-id-properties](../../includes/container-registry-tasks-system-id-properties.md)]

### <a name="grant-identity-access-to-key-vault"></a>Identitás-hozzáférés megadása a Key Vaulthoz

Futtassa a [következő az keyvault set-policy parancsot][az-keyvault-set-policy] egy hozzáférési szabályzat kulcstartón való beállítására. Az alábbi példa lehetővé teszi, hogy az identitás beolvassa a titkos kulcsokat a kulcstartóból. 

```azurecli
az keyvault set-policy --name mykeyvault \
  --resource-group myResourceGroup \
  --object-id $principalID \
  --secret-permissions get
```

## <a name="manually-run-the-task"></a>A feladat manuális futtatása

Annak ellenőrzéséhez, hogy a felügyelt identitást engedélyező feladat sikeresen fut-e, manuálisan aktiválja a feladatot az [az acr task run paranccsal.][az-acr-task-run] A `--set` paraméterrel adva át a privát objektum nevét a feladatnak. Ebben a példában a helyőrző-repo neve *hubuser/hubrepo.*

```azurecli
az acr task run --name dockerhubtask --registry myregistry --set PrivateRepo=hubuser/hubrepo
```

Ha a feladat sikeresen lefut, a kimenetben a sikeres hitelesítés látható Docker Hub, és a rendszerkép sikeresen fel lett építve és le lett küldve a privát adatokba:

```console
Queued a run with ID: cf24
Waiting for an agent...
2019/06/20 18:05:55 Using acb_vol_b1edae11-30de-4f2b-a9c7-7d743e811101 as the home volume
2019/06/20 18:05:58 Creating Docker network: acb_default_network, driver: 'bridge'
2019/06/20 18:05:58 Successfully set up Docker network: acb_default_network
2019/06/20 18:05:58 Setting up Docker configuration...
2019/06/20 18:05:59 Successfully set up Docker configuration
2019/06/20 18:05:59 Logging in to registry: myregistry.azurecr.io
2019/06/20 18:06:00 Successfully logged into myregistry.azurecr.io
2019/06/20 18:06:00 Executing step ID: acb_step_0. Timeout(sec): 600, Working directory: '', Network: 'acb_default_network'
2019/06/20 18:06:00 Launching container with name: acb_step_0
[...]
Login Succeeded
2019/06/20 18:06:02 Successfully executed container: acb_step_0
2019/06/20 18:06:02 Executing step ID: acb_step_1. Timeout(sec): 600, Working directory: '', Network: 'acb_default_network'
2019/06/20 18:06:02 Scanning for dependencies...
2019/06/20 18:06:04 Successfully scanned dependencies
2019/06/20 18:06:04 Launching container with name: acb_step_1
Sending build context to Docker daemon    129kB
[...]
2019/06/20 18:06:07 Successfully pushed image: hubuser/hubrepo:cf24
2019/06/20 18:06:07 Step ID: acb_step_0 marked as successful (elapsed time in seconds: 2.064353)
2019/06/20 18:06:07 Step ID: acb_step_1 marked as successful (elapsed time in seconds: 2.594061)
2019/06/20 18:06:07 Populating digests for step ID: acb_step_1...
2019/06/20 18:06:09 Successfully populated digests for step ID: acb_step_1
2019/06/20 18:06:09 Step ID: acb_step_2 marked as successful (elapsed time in seconds: 2.743923)
2019/06/20 18:06:09 The following dependencies were found:
2019/06/20 18:06:09
- image:
    registry: registry.hub.docker.com
    repository: hubuser/hubrepo
    tag: cf24
    digest: sha256:92c7f9c92844bbbb5d0a101b22f7c2a7949e40f8ea90c8b3bc396879d95e899a
  runtime-dependency:
    registry: registry.hub.docker.com
    repository: library/hello-world
    tag: latest
    digest: sha256:0e11c388b664df8a27a901dce21eb89f11d8292f7fca1b3e3c4321bf7897bffe
  git:
    git-head-revision: b0ffa6043dd893a4c75644c5fed384c82ebb5f9e

Run ID: cf24 was successful after 15s
```

Annak ellenőrzéshez, hogy a rendszerkép lekért-e, ellenőrizze a (ebben a példában) címkét a privát `cf24` Docker Hub-ban.

## <a name="next-steps"></a>Következő lépések

* További információ a [felügyelt identitás ACR-feladatokban való engedélyezéséről.](container-registry-tasks-authentication-managed-identity.md)
* Lásd a [ACR-feladatok YAML-referenciát](container-registry-tasks-reference-yaml.md)


<!-- LINKS - Internal -->
[az-login]: /cli/azure/reference-index#az_login
[az-acr-login]: /cli/azure/acr#az_acr_login
[az-acr-show]: /cli/azure/acr#az_acr_show
[az-acr-build]: /cli/azure/acr#az_acr_build
[az-acr-repository-show-tags]: /cli/azure/acr/repository#az_acr_repository_show_tags
[az-role-assignment-create]: /cli/azure/role/assignment#az_role_assignment_create
[az-acr-login]: /cli/azure/acr#az_acr_login
[az-identity-create]: /cli/azure/identity#az_identity_create
[az-identity-show]: /cli/azure/identity#az_identity_show
[azure-cli]: /cli/azure/install-azure-cli
[az-acr-task-create]: /cli/azure/acr/task#az_acr_task_create
[az-acr-task-show]: /cli/azure/acr/task#az_acr_task_show
[az-acr-task-run]: /cli/azure/acr/task#az_acr_task_run
[az-acr-task-list-runs]: /cli/azure/acr/task#az_acr_task_list_runs
[az-acr-task-credential-add]: /cli/azure/acr/task/credential#az_acr_task_credential_add
[az-group-create]: /cli/azure/group?#az_group_create
[az-keyvault-create]: /cli/azure/keyvault?#az_keyvault_create
[az-keyvault-secret-set]: /cli/azure/keyvault/secret#az_keyvault_secret_set
[az-keyvault-set-policy]: /cli/azure/keyvault#az_keyvault_set_policy
