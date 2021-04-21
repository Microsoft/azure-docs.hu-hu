---
title: Adatbázisközi hitelesítés az ACR-feladatból
description: Konfigurál egy Azure Container Registry feladatot (ACR-feladat) egy másik privát Azure Container Registry elérésére az Azure-erőforrások felügyelt identitásának használatával
ms.topic: article
ms.date: 07/06/2020
ms.openlocfilehash: a9b70a44de0cfccb9a61bc24575281e440db6e32
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107781122"
---
# <a name="cross-registry-authentication-in-an-acr-task-using-an-azure-managed-identity"></a>Adatbázisközi hitelesítés egy ACR-feladatban Azure által felügyelt identitással 

Egy [ACR-feladatban](container-registry-tasks-overview.md)engedélyezheti [a felügyelt identitást az Azure-erőforrásokhoz.](container-registry-tasks-authentication-managed-identity.md) A feladat az identitás használatával hozzáférhet más Azure-erőforrásokhoz anélkül, hogy hitelesítő adatokat kellene megadnia vagy kezelnie. 

Ebből a cikkből megtudhatja, hogyan engedélyezheti a felügyelt identitást egy feladatban, hogy a rendszerképet a feladat futtatásához használttól eltérő beállításjegyzékből olvassa be.

Az Azure-erőforrások létrehozásához ehhez a cikkhez az Azure CLI 2.0.68-as vagy újabb verziójára lesz szükség. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése][azure-cli].

## <a name="scenario-overview"></a>A forgatókönyv áttekintése

A példafeladat lekért egy alapként szolgáló rendszerképet egy másik Azure Container Registryből egy alkalmazás-rendszerkép felépítéséhez és leküldéséhez. Az alapként megadott rendszerkép le kérnie a feladatot egy felügyelt identitással kell konfigurálnia, és hozzá kell rendelnie a megfelelő engedélyeket. 

Ez a példa felhasználó vagy rendszer által hozzárendelt felügyelt identitás használatának lépéseit mutatja be. Az identitásválasztás a szervezet igényeitől függ.

Egy valós forgatókönyvben a szervezetek olyan alapként használt rendszerképeket tartatnak fenn, amelyek az összes fejlesztői csapat által az alkalmazások fejlesztésére használhatók. Ezek az alapként használható rendszerképek egy vállalati regisztrációs adatbázisban vannak tárolva, és minden fejlesztői csapat csak lekért jogosultságokkal rendelkezik. 

## <a name="prerequisites"></a>Előfeltételek

Ehhez a cikkhez két Azure-beli tárolóregisztrálóra lesz szüksége:

* Az első regisztrációs adatbázis használatával hozhat létre és hajthat végre ACR-feladatokat. Ebben a cikkben ennek a regisztrációs adatbázisnak a *myregistry* a neve. 
* A második beállításjegyzék egy alapként használt rendszerképet hoz létre, amelyből a feladat lemezképet hoz létre. Ebben a cikkben a második beállításjegyzék neve *mybaseregistry*. 

A későbbi lépésekben cserélje le a helyére a saját regisztrációs adatbázisneveit.

Ha még nem rendelkezik a szükséges Azure-beli tároló-beállításjegyzékekkel, tekintse meg a rövid útmutató: Privát tárolóregisztr-adatbázis létrehozása [az Azure CLI használatávalcímű rövid útmutatót.](container-registry-get-started-azure-cli.md) Még nem kell rendszerképeket leküldést a regisztrációs adatbázisba.

## <a name="prepare-base-registry"></a>Alap beállításjegyzék előkészítése

Bemutatási célból futtassa az [az acr import][az-acr-import] fájlt egy nyilvános Node.js-rendszerkép importálásához az Docker Hub adatbázisból az alapjegyzékbe. A gyakorlatban a szervezet egy másik csapata vagy folyamata rendszerképeket tart fenn az alapjegyzékben.

```azurecli
az acr import --name mybaseregistry \
  --source docker.io/library/node:15-alpine \
  --image baseimages/node:15-alpine 
```

## <a name="define-task-steps-in-yaml-file"></a>Feladat lépései a YAML-fájlban

A példa többlépéses feladat lépéseit egy [YAML-fájl határozza meg.](container-registry-tasks-reference-yaml.md) [](container-registry-tasks-multi-step.md) Hozzon létre egy nevű fájlt a `helloworldtask.yaml` helyi munkakönyvtárban, és illessze be az alábbi tartalmat. Frissítse a értékét a build lépésében az alap beállításjegyzék `REGISTRY_NAME` kiszolgálónevével.

```yml
version: v1.1.0
steps:
# Replace mybaseregistry with the name of your registry containing the base image
  - build: -t $Registry/hello-world:$ID  https://github.com/Azure-Samples/acr-build-helloworld-node.git#main -f Dockerfile-app --build-arg REGISTRY_NAME=mybaseregistry.azurecr.io
  - push: ["$Registry/hello-world:$ID"]
```

A buildelési lépés az `Dockerfile-app` [Azure-Samples/acr-build-helloworld-node](https://github.com/Azure-Samples/acr-build-helloworld-node.git) adattára fájlját használja egy rendszerkép felépítéséhez. A `--build-arg` az alap-beállításjegyzékre hivatkozik az alapként hivatkozó rendszerkép lekért adatokhoz. Sikeres felépítés esetén a rendszer leküldi a rendszerképet a feladat futtatásához használt beállításjegyzékbe.

## <a name="option-1-create-task-with-user-assigned-identity"></a>1. lehetőség: Feladat létrehozása felhasználó által hozzárendelt identitással

Az ebben a szakaszban található lépések létrehoznak egy feladatot, és engedélyezik a felhasználó által hozzárendelt identitást. Ha ehelyett rendszer által hozzárendelt identitást szeretne engedélyezni, lásd: 2. lehetőség: Feladat létrehozása rendszer által [hozzárendelt identitással.](#option-2-create-task-with-system-assigned-identity) 

[!INCLUDE [container-registry-tasks-user-assigned-id](../../includes/container-registry-tasks-user-assigned-id.md)]

### <a name="create-task"></a>Feladat létrehozása

Hozza létre *a helloworldtask* feladatot a következő [az acr task create parancs végrehajtásával.][az-acr-task-create] A feladat forráskódkörnyezet nélkül fut, és a parancs a `helloworldtask.yaml` munkakönyvtárban található fájlra hivatkozik. A paraméter átadja a felhasználó `--assign-identity` által hozzárendelt identitás erőforrás-azonosítóját. 

```azurecli
az acr task create \
  --registry myregistry \
  --name helloworldtask \
  --context /dev/null \
  --file helloworldtask.yaml \
  --assign-identity $resourceID
```

[!INCLUDE [container-registry-tasks-user-id-properties](../../includes/container-registry-tasks-user-id-properties.md)]

### <a name="give-identity-pull-permissions-to-the-base-registry"></a>Identitás le pull-engedélyeinek adjuk az alap beállításjegyzéket

Ebben a szakaszban engedélyeket ad a felügyelt identitásnak a *mybaseregistry* alap beállításjegyzékből való lekért adatokhoz.

Az [az acr show paranccsal][az-acr-show] szerezze be az alap beállításjegyzék erőforrás-azonosítóját, és tárolja egy változóban:

```azurecli
baseregID=$(az acr show --name mybaseregistry --query id --output tsv)
```

Az [az role assignment create paranccsal][az-role-assignment-create] rendelje hozzá az identitást az alap `acrpull` beállításjegyzékhez. Ez a szerepkör csak a regisztrációs adatbázis rendszerképeinek lekért engedélyével rendelkezik.

```azurecli
az role assignment create \
  --assignee $principalID \
  --scope $baseregID \
  --role acrpull
```

Folytassa a [Cél-beállításjegyzék hitelesítő adatainak hozzáadása a feladathoz.](#add-target-registry-credentials-to-task)

## <a name="option-2-create-task-with-system-assigned-identity"></a>2. lehetőség: Feladat létrehozása rendszer által hozzárendelt identitással

Az ebben a szakaszban található lépések létrehoznak egy feladatot, és engedélyezik a rendszer által hozzárendelt identitást. Ha ehelyett felhasználó által hozzárendelt identitást szeretne engedélyezni, lásd: 1. lehetőség: Feladat létrehozása felhasználó által [hozzárendelt identitással.](#option-1-create-task-with-user-assigned-identity) 

### <a name="create-task"></a>Feladat létrehozása

Hozza létre a *helloworldtask* feladatot a következő [az acr task create parancs végrehajtásával.][az-acr-task-create] A feladat forráskódkörnyezet nélkül fut, és a parancs a munkakönyvtárban `helloworldtask.yaml` található fájlra hivatkozik. A `--assign-identity` paraméter érték nélkül engedélyezi a rendszer által hozzárendelt identitást a feladaton. 

```azurecli
az acr task create \
  --registry myregistry \
  --name helloworldtask \
  --context /dev/null \
  --file helloworldtask.yaml \
  --assign-identity 
```
[!INCLUDE [container-registry-tasks-system-id-properties](../../includes/container-registry-tasks-system-id-properties.md)]

### <a name="give-identity-pull-permissions-to-the-base-registry"></a>Identitás le pull-engedélyeinek adjuk az alap beállításjegyzéket

Ebben a szakaszban adjon engedélyt a felügyelt identitásnak a *mybaseregistry* alapjegyzékből való lekért adatokhoz.

Az [az acr show paranccsal][az-acr-show] szerezze be az alap beállításjegyzék erőforrás-azonosítóját, és tárolja egy változóban:

```azurecli
baseregID=$(az acr show --name mybaseregistry --query id --output tsv)
```

Az [az role assignment create paranccsal][az-role-assignment-create] rendelje hozzá az identitást az alap `acrpull` beállításjegyzékhez. Ez a szerepkör csak a regisztrációs adatbázis rendszerképeinek lekért engedélyével rendelkezik.

```azurecli
az role assignment create \
  --assignee $principalID \
  --scope $baseregID \
  --role acrpull
```

## <a name="add-target-registry-credentials-to-task"></a>Cél beállításjegyzékbeli hitelesítő adatok hozzáadása a feladathoz

Most használja az [az acr task credential add][az-acr-task-credential-add] parancsot, hogy engedélyezze a feladatnak az alap beállításjegyzékben való hitelesítést az identitás hitelesítő adataival. Futtassa a feladatban engedélyezett felügyelt identitás típusának megfelelő parancsot. Ha engedélyezte a felhasználó által hozzárendelt identitást, adja meg a azonosítót az `--use-identity` identitás ügyfél-azonosítójával. Ha engedélyezte a rendszer által hozzárendelt identitást, adja meg a következőt: `--use-identity [system]` .

```azurecli
# Add credentials for user-assigned identity to the task
az acr task credential add \
  --name helloworldtask \
  --registry myregistry \
  --login-server mybaseregistry.azurecr.io \
  --use-identity $clientID

# Add credentials for system-assigned identity to the task
az acr task credential add \
  --name helloworldtask \
  --registry myregistry \
  --login-server mybaseregistry.azurecr.io \
  --use-identity [system]
```

## <a name="manually-run-the-task"></a>A feladat manuális futtatása

Annak ellenőrzéséhez, hogy a felügyelt identitást engedélyező feladat sikeresen lefut-e, aktiválja manuálisan a feladatot az [az acr task run paranccsal.][az-acr-task-run] 

```azurecli
az acr task run \
  --name helloworldtask \
  --registry myregistry
```

Ha a feladat sikeresen lefut, a kimenet a következőre hasonlít:

```
Queued a run with ID: cf10
Waiting for an agent...
2019/06/14 22:47:32 Using acb_vol_dbfbe232-fd76-4ca3-bd4a-687e84cb4ce2 as the home volume
2019/06/14 22:47:39 Creating Docker network: acb_default_network, driver: 'bridge'
2019/06/14 22:47:40 Successfully set up Docker network: acb_default_network
2019/06/14 22:47:40 Setting up Docker configuration...
2019/06/14 22:47:41 Successfully set up Docker configuration
2019/06/14 22:47:41 Logging in to registry: myregistry.azurecr.io
2019/06/14 22:47:42 Successfully logged into myregistry.azurecr.io
2019/06/14 22:47:42 Logging in to registry: mybaseregistry.azurecr.io
2019/06/14 22:47:43 Successfully logged into mybaseregistry.azurecr.io
2019/06/14 22:47:43 Executing step ID: acb_step_0. Timeout(sec): 600, Working directory: '', Network: 'acb_default_network'
2019/06/14 22:47:43 Scanning for dependencies...
2019/06/14 22:47:45 Successfully scanned dependencies
2019/06/14 22:47:45 Launching container with name: acb_step_0
Sending build context to Docker daemon   25.6kB
Step 1/6 : ARG REGISTRY_NAME
Step 2/6 : FROM ${REGISTRY_NAME}/baseimages/node:15-alpine
15-alpine: Pulling from baseimages/node
[...]
Successfully built 41b49a112663
Successfully tagged myregistry.azurecr.io/hello-world:cf10
2019/06/14 22:47:56 Successfully executed container: acb_step_0
2019/06/14 22:47:56 Executing step ID: acb_step_1. Timeout(sec): 600, Working directory: '', Network: 'acb_default_network'
2019/06/14 22:47:56 Pushing image: myregistry.azurecr.io/hello-world:cf10, attempt 1
The push refers to repository [myregistry.azurecr.io/hello-world]
[...]
2019/06/14 22:48:00 Step ID: acb_step_1 marked as successful (elapsed time in seconds: 2.517011)
2019/06/14 22:48:00 The following dependencies were found:
2019/06/14 22:48:00
- image:
    registry: myregistry.azurecr.io
    repository: hello-world
    tag: cf10
    digest: sha256:611cf6e3ae3cb99b23fadcd89fa144e18aa1b1c9171ad4a0da4b62b31b4e38d1
  runtime-dependency:
    registry: mybaseregistry.azurecr.io
    repository: baseimages/node
    tag: 15-alpine
    digest: sha256:e8e92cffd464fce3be9a3eefd1b65dc9cbe2484da31c11e813a4effc6105c00f
  git:
    git-head-revision: 0f988779c97fe0bfc7f2f74b88531617f4421643

Run ID: cf10 was successful after 32s
```

Futtassa [az az acr repository show-tags][az-acr-repository-show-tags] parancsot annak ellenőrzéséhez, hogy a rendszerkép létre lett-e hozva, és sikeresen le lett-e küldve a *myregistry-be:*

```azurecli
az acr repository show-tags --name myregistry --repository hello-world --output tsv
```

Példa a kimenetre:

```console
cf10
```

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
[azure-cli]: /cli/azure/install-azure-cli
[az-acr-task-create]: /cli/azure/acr/task#az_acr_task_create
[az-acr-task-show]: /cli/azure/acr/task#az_acr_task_show
[az-acr-task-run]: /cli/azure/acr/task#az_acr_task_run
[az-acr-task-list-runs]: /cli/azure/acr/task#az_acr_task_list_runs
[az-acr-task-credential-add]: /cli/azure/acr/task/credential#az_acr_task_credential_add
[az-group-create]: /cli/azure/group?#az_group_create
