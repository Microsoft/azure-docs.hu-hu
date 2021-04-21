---
title: Gyors feladatfutat sablonnal
description: ACR-feladat futtatásának várólistára kerülése rendszerkép buildelése egy Azure Resource Manager sablonnal
ms.topic: article
ms.date: 04/22/2020
ms.openlocfilehash: af7bebc311f81bb489fcc8be419f167ff6f9460a
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107781234"
---
# <a name="run-acr-tasks-using-resource-manager-templates"></a>Az ACR-feladatok futtatása Resource Manager sablonokkal

[ACR-feladatok](container-registry-tasks-overview.md) szolgáltatáscsomag a Azure Container Registry a tárolók rendszerképének kezeléséhez és módosításához a tároló életciklusa során. 

Ez a Azure Resource Manager példasablonokat mutat be a gyors feladatfuttassanak várólistára, hasonlóan ahhoz, amit manuálisan hozhat létre az [az acr build paranccsal.][az-acr-build]

A Resource Manager várólistára hozó sablon hasznos automatizálási forgatókönyvekben, és kiterjeszti a `az acr build` funkcióit. Például:

* Tároló-beállításjegyzék létrehozása sablon használatával, és egy feladatfutat azonnali várólistára kerülése tároló-rendszerkép létrehozásához és leküldéséhez
* Gyors feladatfuttatásban használható további erőforrások, például azure-erőforrások felügyelt identitásának létrehozása vagy engedélyezése

## <a name="limitations"></a>Korlátozások

* A feladatfuttatás forráshelyeként meg kell [](container-registry-tasks-overview.md#context-locations) adnia egy távoli környezetet, például egy GitHub-adattárat. Helyi forráskörnyezet nem használható.
* Felügyelt identitással futtatott feladatok esetén csak a felhasználó *által hozzárendelt* felügyelt identitások engedélyezettek.

## <a name="prerequisites"></a>Előfeltételek

* **GitHub-fiók** – Hozzon létre egy fiókot a webhelyen, ha még https://github.com nem rendelkezik fiókkal. 
* **Fork sample repository** –Az itt bemutatott példafeladatok esetében a GitHub felhasználói felületén elágasztatjuk a következő mintaadattárat a GitHub-fiókjába: https://github.com/Azure-Samples/acr-build-helloworld-node . Ez az adattára minta Docker-fájlokat és forráskódot tartalmaz a kis tárolók rendszerképének felépítéséhez.

## <a name="example-create-registry-and-queue-task-run"></a>Példa: Beállításjegyzék- és üzenetsor-feladat futtatása

Ez a példa egy [mintasablont használ](https://github.com/Azure/acr/tree/master/docs/tasks/run-as-deployment/quickdockerbuild) egy tároló-beállításjegyzék létrehozásához és egy rendszerképet felépítési és leküldési feladatfuttató várólistára való várakozásához. 

### <a name="template-parameters"></a>Sablon paraméterei

Ebben a példában adja meg a következő sablonparaméterek értékeit:

|Paraméter  |Érték  |
|---------|---------|
|registryName (beállításjegyzék neve)     |A létrehozott regisztrációs adatbázis egyedi neve         |
|repository     |A buildfeladat céltárháza        |
|taskRunName     |A feladatfuttassanak neve, amely a képcímkét határozza meg |
|sourceLocation (forráshely)     |A buildfeladat távoli környezete, például https://github.com/Azure-Samples/acr-build-helloworld-node : . Az adattár gyökerében található Dockerfile egy tároló rendszerképet épít fel egy kis méretű Node.js webalkalmazás számára. Ha szeretné, használja az eltekintő elírást az összeállítási környezetként.         |

### <a name="deploy-the-template"></a>A sablon üzembe helyezése

A sablon üzembe helyezése az [az deployment group create paranccsal.][az-deployment-group-create] Ez a példa egy *mycontainerregistry* nevű regisztrációs adatbázisba buildolja és lekulpeli a *helloworld-node:testrun* rendszerképet.

```azurecli
az deployment group create \
  --resource-group myResourceGroup \
  --template-uri https://raw.githubusercontent.com/Azure/acr/master/docs/tasks/run-as-deployment/quickdockerbuild/azuredeploy.json \
  --parameters \
    registryName=mycontainerregistry \
    repository=helloworld-node \
    taskRunName=testrun \
    sourceLocation=https://github.com/Azure-Samples/acr-build-helloworld-node.git#main
 ```

Az előző parancs átadja a paramétereket a parancssorban. Ha szükséges, adja át őket egy [paraméterfájlban.](../azure-resource-manager/templates/parameter-files.md)

### <a name="verify-deployment"></a>Az üzembe helyezés ellenőrzése

Az üzembe helyezés sikeres befejezése után az [az acr repository show-tags][az-acr-repository-show-tags]futtatásával ellenőrizze, hogy a rendszerkép fel lett-e építve:

```azurecli
az acr repository show-tags \
  --name mycontainerregistry \
  --repository helloworld-node --output table
```

Kimenet:

```console
Result
--------
testrun
```

### <a name="view-run-log"></a>Futtatásnapló megtekintése

A feladatfuttatás részleteinek megtekintéséhez tekintse meg a futtatás naplóját.

Először szerezze be a futtatás azonosítóját [az az acr task list-runs segítségével][az-acr-task-list-runs]
```azurecli
az acr task list-runs \
  --registry mycontainerregistry --output table
```

A kimenet a következőhöz hasonló:

```console
RUN ID    TASK    PLATFORM    STATUS     TRIGGER    STARTED               DURATION
--------  ------  ----------  ---------  ---------  --------------------  ----------
ca1               linux       Succeeded  Manual     2020-03-23T17:54:28Z  00:00:48
```

Futtassa [az az acr task logs (az acr task logs)][az-acr-task-logs] futtatását a futtatás azonosítójának (ebben az esetben *ca1) feladatfuttassa naplóinak megtekintéséhez:*

```azurecli
az acr task logs \
  --registry mycontainerregistry \
  --run-id ca1
```

A kimenet a feladatfuttassa naplót jeleníti meg.

A feladatfuttassa naplót a következő Azure Portal. 

1. Lépjen a tároló-beállításjegyzékhez
2. A **Szolgáltatások alatt válassza** a Feladatok **futtatása**  >  **lehetőséget.**
3. Válassza ki a futtatás azonosítóját, ebben az esetben *ca1*. 

A portálon megjelenik a feladatfuttassa napló.

## <a name="example-task-run-with-managed-identity"></a>Példa: Felügyelt identitással futtatott feladat

Használjon egy [mintasablont](https://github.com/Azure/acr/tree/master/docs/tasks/run-as-deployment/quickdockerbuildwithidentity) egy, a felhasználó által hozzárendelt felügyelt identitást lehetővé tő feladatfuttassa üzenetsorba. A feladat futtatása során az identitás hitelesíti magát, hogy lekért egy rendszerképet egy másik Azure-beli tároló-beállításjegyzékből. 

Ez a forgatókönyv hasonlít a regisztrációs adatbázisközi hitelesítéshez egy Azure által felügyelt identitást használó [ACR-feladatban.](container-registry-tasks-cross-registry-authentication.md) Előfordulhat például, hogy egy szervezet egy központosított regisztrációs adatbázist tart fenn, amely több fejlesztői csapat által elért alapként használt rendszerképeket tartalmaz.

### <a name="prepare-base-registry"></a>Alap beállításjegyzék előkészítése

Bemutatási célból hozzon létre egy különálló tároló-beállításjegyzéket alap beállításjegyzékként, és Node.js leküld egy alapként szolgáló rendszerképet a Docker Hub.

1. Hozzon létre egy második tároló-beállításjegyzéket( például *mybaseregistry)* az alapként szolgáló rendszerképek tárolására.
1. A rendszerképet a Docker Hub le, címkézni kell az alap beállításjegyzékhez, majd le kell lekulpelni `node:9-alpine` az alap beállításjegyzékbe:

  ```azurecli
  docker pull node:9-alpine
  docker tag node:9-alpine mybaseregistry.azurecr.io/baseimages/node:9-alpine
  az acr login -n mybaseregistry
  docker push mybaseregistry.azurecr.io/baseimages/node:9-alpine
  ```

### <a name="create-new-dockerfile"></a>Új Docker-fájl létrehozása

Hozzon létre egy Docker-fájl, amely lekérte az alapként használt rendszerképet az alapjegyzékből. Hajtsa végre a következő lépéseket a GitHub-adattár helyi elágajában, például: `https://github.com/myGitHubID/acr-build-helloworld-node.git` .

1. A GitHub felhasználói felületén válassza az **Új fájl létrehozása lehetőséget.**
1. Nevezze el a *fájlt Dockerfile-test néven,* és illessze be az alábbi tartalmat. Helyettesítse be a regisztrációs adatbázis nevét *a mybaseregistry névvel.*
    ```
    FROM mybaseregistry.azurecr.io/baseimages/node:9-alpine
    COPY . /src
    RUN cd /src && npm install
    EXPOSE 80
    CMD ["node", "/src/server.js"]
    ```
 1. Válassza **az Új fájl véglegesítése lehetőséget.**

[!INCLUDE [container-registry-tasks-user-assigned-id](../../includes/container-registry-tasks-user-assigned-id.md)]

### <a name="give-identity-pull-permissions-to-the-base-registry"></a>Identitás le pull-engedélyeinek adjuk az alap beállításjegyzéket

Adjon engedélyt a felügyelt identitásnak a *mybaseregistry* alap beállításjegyzékből való lekért adatokhoz.

Az [az acr show paranccsal][az-acr-show] szerezze be az alap beállításjegyzék erőforrás-azonosítóját, és tárolja egy változóban:

```azurecli
baseregID=$(az acr show \
  --name mybaseregistry \
  --query id --output tsv)
```

Az [az role assignment create paranccsal][az-role-assignment-create] rendelje hozzá az Acrpull-szerepkört az alap beállításjegyzékhez. Ez a szerepkör csak a regisztrációs adatbázis rendszerképeinek lekért engedélyével rendelkezik.

```azurecli
az role assignment create \
  --assignee $principalID \
  --scope $baseregID \
  --role acrpull
```

### <a name="template-parameters"></a>Sablon paraméterei

Ebben a példában adja meg a következő sablonparaméterek értékeit:

|Paraméter  |Érték  |
|---------|---------|
|registryName (beállításjegyzék neve)     |A regisztrációs adatbázis neve, ahol a rendszerkép fel van építve  |
|repository     |A buildfeladat céltárháza        |
|taskRunName     |A feladatfuttatás neve, amely a képcímkét határozza meg |
|userAssignedIdentity |A feladatban engedélyezett, felhasználó által hozzárendelt identitás erőforrás-azonosítója|
|customRegistryIdentity | A feladatban engedélyezett, egyéni beállításjegyzékkel való hitelesítéshez használt, felhasználó által hozzárendelt identitás ügyfél-azonosítója |
|customRegistry (egyéni regisztráció) |A feladatban elért egyéni beállításjegyzék bejelentkezési kiszolgálójának neve, például mybaseregistry.azurecr.io |
|sourceLocation (forráshely)     |A buildfeladat távoli környezete, például *https://github.com/ \<your-GitHub-ID\> /acr-build-helloworld-node.* |
|dockerFilePath | A dockerfile elérési útja a távoli környezetben, a rendszerkép felépítéséhez. |

### <a name="deploy-the-template"></a>A sablon üzembe helyezése

Telepítse a sablont az [az deployment group create paranccsal.][az-deployment-group-create] Ez a példa egy *mycontainerregistry* nevű regisztrációs adatbázisba buildolja és lekulpeli a *helloworld-node:testrun* rendszerképet. Az alap rendszerképet a rendszer a következőből *mybaseregistry.azurecr.io.*

```azurecli
az deployment group create \
  --resource-group myResourceGroup \
  --template-uri https://raw.githubusercontent.com/Azure/acr/master/docs/tasks/run-as-deployment/quickdockerbuildwithidentity/azuredeploy.json \
  --parameters \
    registryName=mycontainerregistry \
    repository=helloworld-node \
    taskRunName=basetask \
    userAssignedIdentity=$resourceID \
    customRegistryIdentity=$clientID \
    sourceLocation=https://github.com/<your-GitHub-ID>/acr-build-helloworld-node.git#main \
    dockerFilePath=Dockerfile-test \
    customRegistry=mybaseregistry.azurecr.io
```

Az előző parancs átadja a paramétereket a parancssorban. Ha szükséges, adja át őket egy [paraméterfájlban.](../azure-resource-manager/templates/parameter-files.md)

### <a name="verify-deployment"></a>Az üzembe helyezés ellenőrzése

Az üzembe helyezés sikeres befejezése után az [az acr repository show-tags][az-acr-repository-show-tags]futtatásával ellenőrizze, hogy a rendszerkép fel lett-e építve:

```azurecli
az acr repository show-tags \
  --name mycontainerregistry \
  --repository helloworld-node --output table
```

Kimenet:

```console
Result
--------
basetask
```

### <a name="view-run-log"></a>Futtatásnapló megtekintése

A futtatásnapló megtekintéséhez tekintse meg az előző szakasz [lépéseit.](#view-run-log)

## <a name="next-steps"></a>Következő lépések

 * További példasablonok az [ACR GitHub-adattárban.](https://github.com/Azure/acr/tree/master/docs/tasks/run-as-deployment)
 * A sablontulajdonságokkal kapcsolatos részletekért tekintse meg a feladatfutságok és a [feladatok sablonreferenciáját.](/azure/templates/microsoft.containerregistry/2019-06-01-preview/registries/taskruns) [](/azure/templates/microsoft.containerregistry/2019-06-01-preview/registries/tasks)


<!-- LINKS - Internal -->
[azure-cli]: /cli/azure/install-azure-cli
[az-acr-build]: /cli/azure/acr#az_acr_build
[az-acr-show]: /cli/azure/acr#az_acr_show
[az-acr-task-run]: /cli/azure/acr/task#az_acr_task_run
[az-acr-task-logs]: /cli/azure/acr/task#az_acr_task_logs
[az-acr-repository-show-tags]: /cli/azure/acr/repository#az_acr_repository_show_tags
[az-acr-task-list-runs]: /cli/azure/acr/task#az_acr_task_list_runs
[az-deployment-group-create]: /cli/azure/deployment/group#az_deployment_group_create
[az-identity-create]: /cli/azure/identity#az_identity_create
[az-identity-show]: /cli/azure/identity#az_identity_show
[az-role-assignment-create]: /cli/azure/role/assignment#az_role_assignment_create
