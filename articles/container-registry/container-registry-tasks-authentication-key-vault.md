---
title: Külső hitelesítés az ACR-feladatból
description: Konfiguráljon egy Azure Container Registry feladatot (ACR-feladatot) az Azure Key vaultban tárolt Docker hub hitelesítő adatok olvasásához az Azure-erőforrások felügyelt identitásának használatával.
ms.topic: article
ms.date: 07/06/2020
ms.openlocfilehash: 0bc43f958a14016146160a06372af0b36a9fff75
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "86058129"
---
# <a name="external-authentication-in-an-acr-task-using-an-azure-managed-identity"></a>Külső hitelesítés egy ACR-feladatban egy Azure által felügyelt identitás használatával 

Egy [ACR-feladatban](container-registry-tasks-overview.md) [engedélyezheti az Azure-erőforrások felügyelt identitását](container-registry-tasks-authentication-managed-identity.md). A feladat használhatja az identitást más Azure-erőforrások elérésére, anélkül, hogy hitelesítő adatokat kellene megadnia vagy kezelnie. 

Ebből a cikkből megtudhatja, hogyan engedélyezheti a felügyelt identitásokat egy olyan feladatban, amely hozzáfér az Azure Key vaultban tárolt titkokhoz. 

Az Azure-erőforrások létrehozásához ehhez a cikkhez az Azure CLI 2.0.68 vagy újabb verzióját kell futtatnia. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése][azure-cli].

## <a name="scenario-overview"></a>A forgatókönyv áttekintése

A példa feladattal egy Azure Key vaultban tárolt Docker hub hitelesítő adatok olvashatók be. A hitelesítő adatok olyan Docker hub-fiókhoz tartoznak, amely írási (leküldéses) engedélyekkel rendelkezik egy privát Docker hub-tárházhoz. A hitelesítő adatok olvasásához a feladatot felügyelt identitással kell konfigurálni, és hozzá kell rendelnie a megfelelő engedélyeket. Az identitáshoz rendelt feladat létrehoz egy rendszerképet, és bejelentkezik a Docker hub-ba, hogy leküldje a képet a privát tárházba. 

Ez a példa egy felhasználó által hozzárendelt vagy rendszer által hozzárendelt felügyelt identitást használó lépéseket mutatja be. Az Ön által választott identitás a szervezet igényeitől függ.

A valós forgatókönyvekben a vállalatok képeket tehetnek közzé egy privát tárházban a Docker hub-ban egy összeállítási folyamat részeként. 

## <a name="prerequisites"></a>Előfeltételek

Szüksége lesz egy Azure Container registryre, amelyben futtatja a feladatot. Ebben a cikkben a beállításjegyzék neve *myregistry*. Cserélje le a változót a saját beállításjegyzékbeli nevére a későbbi lépésekben.

Ha még nem rendelkezik Azure Container Registry szolgáltatással, tekintse meg a következőt: rövid útmutató [: privát tároló-beállításjegyzék létrehozása az Azure CLI használatával](container-registry-get-started-azure-cli.md). A lemezképeket még nem kell leküldeni a beállításjegyzékbe.

Szüksége lesz egy privát adattárra is a Docker hub-ban, valamint egy Docker hub-fiókot, amely a tárházba való íráshoz szükséges engedélyekkel rendelkezik. Ebben a példában ez a tárház neve *hubuser/hubrepo*. 

## <a name="create-a-key-vault-and-store-secrets"></a>Key Vault létrehozása és a titkos kulcsok tárolása

Először is, ha szükséges, hozzon létre egy *myResourceGroup* nevű erőforráscsoportot a *eastus* helyen a következő az [Group Create][az-group-create] paranccsal:

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

A Key Vault létrehozásához használja az az kulcstartó [létrehozása][az-keyvault-create] parancsot. Ügyeljen arra, hogy egyedi kulcstároló-nevet adjon meg. 

```azurecli-interactive
az keyvault create --name mykeyvault --resource-group myResourceGroup --location eastus
```

Tárolja a szükséges Docker hub hitelesítő adatait a Key vaultban az az kulcstartó [Secret set][az-keyvault-secret-set] parancs használatával. Ezekben a parancsokban az értékek a környezeti változókban lesznek átadva:

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

Egy valós forgatókönyvben a titkokat valószínűleg egy külön folyamat fogja beállítani és karbantartani.

## <a name="define-task-steps-in-yaml-file"></a>Feladat lépéseinek meghatározása a YAML fájlban

A példához tartozó lépések egy [YAML fájlban](container-registry-tasks-reference-yaml.md)vannak meghatározva. Hozzon létre egy nevű fájlt `dockerhubtask.yaml` egy helyi munkakönyvtárban, és illessze be a következő tartalmakat. Ügyeljen arra, hogy a Key Vault nevét a Key Vault nevű fájlban cserélje le.

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

A feladat lépései a következők:

* Titkos hitelesítő adatok kezelése a Docker hub használatával történő hitelesítéshez.
* Hitelesítés a Docker hub használatával a titkoknak a parancsba való átadásával `docker login` .
* Hozzon létre egy rendszerképet az [Azure-Samples/ACR-Tasks](https://github.com/Azure-Samples/acr-tasks.git) tárház Docker használatával.
* Küldje le a rendszerképet a privát Docker hub adattárba.


## <a name="option-1-create-task-with-user-assigned-identity"></a>1. lehetőség: feladat létrehozása felhasználó által hozzárendelt identitással

Az ebben a szakaszban szereplő lépések egy feladatot hoznak létre, és engedélyezik a felhasználó által hozzárendelt identitást. Ha inkább a rendszer által hozzárendelt identitást szeretné engedélyezni, tekintse meg a [2. lehetőség: feladat létrehozása a rendszer által hozzárendelt identitással](#option-2-create-task-with-system-assigned-identity)című témakört. 

[!INCLUDE [container-registry-tasks-user-assigned-id](../../includes/container-registry-tasks-user-assigned-id.md)]

### <a name="create-task"></a>Feladat létrehozása

Hozza létre a feladat *dockerhubtask* a következő az [ACR Task Create][az-acr-task-create] parancs végrehajtásával. A feladat forráskód-környezet nélkül fut, és a parancs a `dockerhubtask.yaml` munkakönyvtárban található fájlra hivatkozik. A `--assign-identity` paraméter átadja a felhasználó által hozzárendelt identitás erőforrás-azonosítóját. 

```azurecli
az acr task create \
  --name dockerhubtask \
  --registry myregistry \
  --context /dev/null \
  --file dockerhubtask.yaml \
  --assign-identity $resourceID
```

[!INCLUDE [container-registry-tasks-user-id-properties](../../includes/container-registry-tasks-user-id-properties.md)]


### <a name="grant-identity-access-to-key-vault"></a>Identitás-hozzáférés biztosítása a Key vaulthoz

A Key vaultra vonatkozó hozzáférési szabályzat beállításához futtassa a következőt az kulcstartó [set-Policy][az-keyvault-set-policy] paranccsal. A következő példa lehetővé teszi, hogy az identitás a Key vaultból olvassa a titkos kulcsokat. 

```azurecli
az keyvault set-policy --name mykeyvault \
  --resource-group myResourceGroup \
  --object-id $principalID \
  --secret-permissions get
```

Folytassa [a feladat manuális futtatásával](#manually-run-the-task).

## <a name="option-2-create-task-with-system-assigned-identity"></a>2. lehetőség: feladat létrehozása rendszer által hozzárendelt identitással

Az ebben a szakaszban szereplő lépések egy feladatot hoznak létre, és engedélyezik a rendszer által hozzárendelt identitást. Ha inkább egy felhasználó által hozzárendelt identitást szeretne engedélyezni, tekintse meg az [1. lehetőség: feladat létrehozása felhasználó által hozzárendelt identitással](#option-1-create-task-with-user-assigned-identity)című témakört. 

### <a name="create-task"></a>Feladat létrehozása

Hozza létre a feladat *dockerhubtask* a következő az [ACR Task Create][az-acr-task-create] parancs végrehajtásával. A feladat forráskód-környezet nélkül fut, és a parancs a `dockerhubtask.yaml` munkakönyvtárban található fájlra hivatkozik. Az `--assign-identity` érték nélküli paraméter lehetővé teszi a rendszer által hozzárendelt identitást a feladatban.  

```azurecli
az acr task create \
  --name dockerhubtask \
  --registry myregistry \
  --context /dev/null \
  --file dockerhubtask.yaml \
  --assign-identity 
```

[!INCLUDE [container-registry-tasks-system-id-properties](../../includes/container-registry-tasks-system-id-properties.md)]

### <a name="grant-identity-access-to-key-vault"></a>Identitás-hozzáférés biztosítása a Key vaulthoz

A Key vaultra vonatkozó hozzáférési szabályzat beállításához futtassa a következőt az kulcstartó [set-Policy][az-keyvault-set-policy] paranccsal. A következő példa lehetővé teszi, hogy az identitás a Key vaultból olvassa a titkos kulcsokat. 

```azurecli
az keyvault set-policy --name mykeyvault \
  --resource-group myResourceGroup \
  --object-id $principalID \
  --secret-permissions get
```

## <a name="manually-run-the-task"></a>A feladat manuális futtatása

Annak ellenőrzéséhez, hogy a felügyelt identitást engedélyező feladat sikeresen fut-e, manuálisan aktiválja a feladatot az az [ACR Task Run][az-acr-task-run] paranccsal. A `--set` paraméter használatával lehet átadni a privát tárház nevét a feladatnak. Ebben a példában a helyőrző adattár neve *hubuser/hubrepo*.

```azurecli
az acr task run --name dockerhubtask --registry myregistry --set PrivateRepo=hubuser/hubrepo
```

A feladat sikeres futtatásakor a kimenet a Docker hub sikeres hitelesítését jeleníti meg, a rendszerkép pedig sikeresen felépítve és leküldve a privát tárházba:

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

A rendszerkép leküldésének megerősítéséhez keresse meg a saját Docker hub-tárházban található címkét ( `cf24` ebben a példában).

## <a name="next-steps"></a>Következő lépések

* További információ a [felügyelt identitások ACR-feladatokban való engedélyezéséről](container-registry-tasks-authentication-managed-identity.md).
* Lásd az [ACR-feladatok YAML-referenciáját](container-registry-tasks-reference-yaml.md)


<!-- LINKS - Internal -->
[az-login]: /cli/azure/reference-index#az-login
[az-acr-login]: /cli/azure/acr#az-acr-login
[az-acr-show]: /cli/azure/acr#az-acr-show
[az-acr-build]: /cli/azure/acr#az-acr-build
[az-acr-repository-show-tags]: /cli/azure/acr/repository#az-acr-repository-show-tags
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
[az-acr-login]: /cli/azure/acr#az-acr-login
[az-identity-create]: /cli/azure/identity#az-identity-create
[az-identity-show]: /cli/azure/identity#az-identity-show
[azure-cli]: /cli/azure/install-azure-cli
[az-acr-task-create]: /cli/azure/acr/task#az-acr-task-create
[az-acr-task-show]: /cli/azure/acr/task#az-acr-task-show
[az-acr-task-run]: /cli/azure/acr/task#az-acr-task-run
[az-acr-task-list-runs]: /cli/azure/acr/task#az-acr-task-list-runs
[az-acr-task-credential-add]: /cli/azure/acr/task/credential#az-acr-task-credential-add
[az-group-create]: /cli/azure/group?#az-group-create
[az-keyvault-create]: /cli/azure/keyvault?#az-keyvault-create
[az-keyvault-secret-set]: /cli/azure/keyvault/secret#az-keyvault-secret-set
[az-keyvault-set-policy]: /cli/azure/keyvault#az-keyvault-set-policy
