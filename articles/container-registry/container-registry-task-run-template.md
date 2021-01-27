---
title: Gyors feladat futtatása sablonnal
description: Az ACR-feladatok futtatásával rendszerképeket hozhat létre Azure Resource Manager sablon használatával
ms.topic: article
ms.date: 04/22/2020
ms.openlocfilehash: 6e8023c088ac328c2b6e95fccd0230c4d40325c1
ms.sourcegitcommit: 436518116963bd7e81e0217e246c80a9808dc88c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/27/2021
ms.locfileid: "98916065"
---
# <a name="run-acr-tasks-using-resource-manager-templates"></a>ACR-feladatok futtatása Resource Manager-sablonok használatával

Az [ACR-feladatok](container-registry-tasks-overview.md) a Azure Container Registryon belüli szolgáltatások egyik csomagja, amely segít a tároló-rendszerképek felügyeletében és módosításához a tárolók életciklusa során. 

Ez a cikk Azure Resource Manager sablonon alapuló példákat mutat be a gyors feladatok futtatásához, hasonlóan az az [ACR Build][az-acr-build] parancs használatával manuálisan is létrehozható.

A feladatok futtatására szolgáló Resource Manager-sablonok az Automation-forgatókönyvekben hasznosak, és kibővítik a funkcióit `az acr build` . Például:

* Sablon használatával létrehozhat egy tároló-beállításjegyzéket, és azonnal várólistára helyezheti a feladatok futtatását egy tároló rendszerképének létrehozásához és leküldéséhez.
* Hozzon létre vagy engedélyezzen további erőforrásokat, amelyeket felhasználhat egy gyors feladatok futtatásához, például felügyelt identitást az Azure-erőforrásokhoz

## <a name="limitations"></a>Korlátozások

* Meg kell adnia egy távoli környezetet, például egy GitHub-tárházat a feladat futtatásának [forrásaként](container-registry-tasks-overview.md#context-locations) . Helyi forrás kontextus nem használható.
* Felügyelt identitás használatával futtatott feladatok esetében csak a *felhasználó által hozzárendelt* felügyelt identitás engedélyezett.

## <a name="prerequisites"></a>Előfeltételek

* **GitHub-fiók** – hozzon létre egy fiókot https://github.com , ha még nem rendelkezik ilyennel. 
* **Elágazási minta adattár** – az itt bemutatott feladatokhoz használja a GitHub felhasználói felületét a következő minta tárház a GitHub-fiókba való elágazásához: https://github.com/Azure-Samples/acr-build-helloworld-node . Ez a tárház minta-Dockerfiles és forráskódot tartalmaz a kisméretű tárolók lemezképének létrehozásához.

## <a name="example-create-registry-and-queue-task-run"></a>Példa: beállításjegyzék és várólista-futtatási feladat létrehozása

Ez a példa egy [minta sablonnal](https://github.com/Azure/acr/tree/master/docs/tasks/run-as-deployment/quickdockerbuild) hoz létre egy tároló-beállításjegyzéket, és egy, a rendszerképet létrehozó és leküldő feladat-futtatási várólistát. 

### <a name="template-parameters"></a>Sablon paraméterei

Ebben a példában adja meg a következő sablon paramétereinek értékeit:

|Paraméter  |Érték  |
|---------|---------|
|registryName     |A létrehozott beállításjegyzék egyedi neve         |
|repository     |Cél adattár a felépítési feladathoz        |
|taskRunName     |A feladat futtatásának neve, amely a képcímkét adja meg |
|sourceLocation     |A felépítési feladat távoli környezete, például: https://github.com/Azure-Samples/acr-build-helloworld-node . A tárház gyökerében található Docker egy kisméretű Node.js webalkalmazáshoz hoz létre egy tároló-rendszerképet. Ha kívánja, használja a tárházat a létrehozási környezetként.         |

### <a name="deploy-the-template"></a>A sablon üzembe helyezése

Telepítse a sablont az az [Deployment Group Create][az-deployment-group-create] paranccsal. Ez a példa létrehozza és leküldi a *HelloWorld-Node: TestRun-* rendszerképet egy *mycontainerregistry* nevű beállításjegyzékbe.

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

Az előző parancs a paramétereket a parancssorban adja át. Ha szükséges, adja át őket egy [paraméter-fájlban](../azure-resource-manager/templates/parameter-files.md).

### <a name="verify-deployment"></a>Az üzembe helyezés ellenőrzése

Az üzembe helyezés sikeres befejeződése után ellenőrizze, hogy a rendszerképet a következő futtatásával építették-e: az [ACR repository show-Tags][az-acr-repository-show-tags]:

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

### <a name="view-run-log"></a>Futtatási napló megtekintése

A feladat futtatásával kapcsolatos részletek megtekintéséhez tekintse meg a futtatási naplót.

Először szerezze be a futtatási azonosítót az [az ACR Task List-][az-acr-task-list-runs] Runs paranccsal.
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

Futtassa az [ACR Task logs][az-acr-task-logs] parancsot a FUTTATÁSi azonosítóhoz tartozó feladat-futtatási naplók megtekintéséhez, ebben az esetben a következő *CA1*:

```azurecli
az acr task logs \
  --registry mycontainerregistry \
  --run-id ca1
```

A kimenet a feladat futtatási naplóját jeleníti meg.

A Azure Portal is megtekintheti a feladat futtatási naplóját. 

1. Navigáljon a tároló beállításjegyzékéhez
2. A **szolgáltatások** területen válassza a **feladatok**  >  **Futtatás** lehetőséget.
3. Válassza ki a futtatási azonosítót, ebben az esetben a *CA1*. 

A portál megjeleníti a feladat futtatási naplóját.

## <a name="example-task-run-with-managed-identity"></a>Példa: feladat futtatása felügyelt identitással

[A felhasználó](https://github.com/Azure/acr/tree/master/docs/tasks/run-as-deployment/quickdockerbuildwithidentity) által hozzárendelt felügyelt identitást engedélyező feladatütemezés használata a feladatok futtatásához. A feladat futtatása során az identitás egy másik Azure Container registryből származó rendszerkép lekérésére hitelesíti. 

Ez a forgatókönyv hasonló a [több beállításjegyzékbeli hitelesítéshez egy ACR-feladatban egy Azure által felügyelt identitás használatával](container-registry-tasks-cross-registry-authentication.md). Előfordulhat például, hogy egy szervezet egy központi beállításjegyzéket tart fenn, amely több fejlesztői csapat által elért alaplemezképekkel rendelkezik.

### <a name="prepare-base-registry"></a>Alapszintű beállításjegyzék előkészítése

Demonstrációs célokra hozzon létre egy külön tároló-beállításjegyzéket az alapadatbázisként, és küldjön le egy Node.js kiinduló rendszerképet a Docker hub-ból.

1. Hozzon létre egy második tároló-beállításjegyzéket (például *mybaseregistry*) az alaplemezképek tárolásához.
1. Kérje le a `node:9-alpine` rendszerképet a Docker hub-ból, és címkézze fel az alap beállításjegyzékbe, majd küldje el az alap beállításjegyzékbe:

  ```azurecli
  docker pull node:9-alpine
  docker tag node:9-alpine mybaseregistry.azurecr.io/baseimages/node:9-alpine
  az acr login -n mybaseregistry
  docker push mybaseregistry.azurecr.io/baseimages/node:9-alpine
  ```

### <a name="create-new-dockerfile"></a>Új Docker létrehozása

Hozzon létre egy Docker, amely lekéri az alaprendszerképet az alapszintű beállításjegyzékből. Hajtsa végre a következő lépéseket a GitHub-tárház helyi villájában, például: `https://github.com/myGitHubID/acr-build-helloworld-node.git` .

1. A GitHub felhasználói felületén válassza az **új fájl létrehozása** lehetőséget.
1. Nevezze el a fájlt *Docker* , és illessze be a következő tartalmakat. Helyettesítse be a *mybaseregistry* beállításjegyzékbeli nevét.
    ```
    FROM mybaseregistry.azurecr.io/baseimages/node:9-alpine
    COPY . /src
    RUN cd /src && npm install
    EXPOSE 80
    CMD ["node", "/src/server.js"]
    ```
 1. Válassza az **új fájl véglegesítés** lehetőséget.

[!INCLUDE [container-registry-tasks-user-assigned-id](../../includes/container-registry-tasks-user-assigned-id.md)]

### <a name="give-identity-pull-permissions-to-the-base-registry"></a>Identitás-lekérési engedélyek megadása az alap beállításjegyzékhez

Adja meg a felügyelt identitás engedélyeit az alapszintű beállításjegyzékből való lekéréshez, *mybaseregistry*.

Használja az az [ACR show][az-acr-show] parancsot az alapszintű beállításjegyzék erőforrás-azonosítójának lekéréséhez és egy változóban való tárolásához:

```azurecli
baseregID=$(az acr show \
  --name mybaseregistry \
  --query id --output tsv)
```

Az az [szerepkör-hozzárendelés létrehozási][az-role-assignment-create] parancs használatával rendelje hozzá a Acrpull szerepkört az alap beállításjegyzékhez. Ez a szerepkör csak a lemezképek beállításjegyzékből való lekéréséhez rendelkezik jogosultságokkal.

```azurecli
az role assignment create \
  --assignee $principalID \
  --scope $baseregID \
  --role acrpull
```

### <a name="template-parameters"></a>Sablon paraméterei

Ebben a példában adja meg a következő sablon paramétereinek értékeit:

|Paraméter  |Érték  |
|---------|---------|
|registryName     |Azon beállításjegyzék neve, amelyben a rendszerkép épül  |
|repository     |Cél adattár a felépítési feladathoz        |
|taskRunName     |A feladat futtatásának neve, amely a képcímkét adja meg |
|userAssignedIdentity |A feladatban engedélyezett, felhasználó által hozzárendelt identitás erőforrás-azonosítója|
|customRegistryIdentity | A felhasználó által hozzárendelt identitás ügyfél-azonosítója engedélyezve a feladatban, amely az egyéni beállításjegyzékkel való hitelesítéshez használatos |
|customRegistry |A feladatban elért egyéni beállításjegyzék bejelentkezési kiszolgálójának neve, például *mybaseregistry.azurecr.IO*|
|sourceLocation     |A felépítési feladat távoli környezete, például *https://github.com/ \<your-GitHub-ID\> /ACR-Build-HelloWorld-Node.* |
|dockerFilePath | A Docker elérési útja a távoli környezetben, a rendszerkép felépítéséhez. |

### <a name="deploy-the-template"></a>A sablon üzembe helyezése

Telepítse a sablont az az [Deployment Group Create][az-deployment-group-create] paranccsal. Ez a példa létrehozza és leküldi a *HelloWorld-Node: TestRun-* rendszerképet egy *mycontainerregistry* nevű beállításjegyzékbe. A kiinduló rendszerkép a *mybaseregistry.azurecr.IO*.

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

Az előző parancs a paramétereket a parancssorban adja át. Ha szükséges, adja át őket egy [paraméter-fájlban](../azure-resource-manager/templates/parameter-files.md).

### <a name="verify-deployment"></a>Az üzembe helyezés ellenőrzése

Az üzembe helyezés sikeres befejeződése után ellenőrizze, hogy a rendszerképet a következő futtatásával építették-e: az [ACR repository show-Tags][az-acr-repository-show-tags]:

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

### <a name="view-run-log"></a>Futtatási napló megtekintése

A futtatási napló megtekintéséhez tekintse meg az [előző szakasz](#view-run-log)lépéseit.

## <a name="next-steps"></a>Következő lépések

 * Az [ACR GitHub](https://github.com/Azure/acr/tree/master/docs/tasks/run-as-deployment)-tárházában további példákat talál a sablonra.
 * A sablon tulajdonságaival kapcsolatos részletekért tekintse meg a feladatok [futtatására](/azure/templates/microsoft.containerregistry/2019-06-01-preview/registries/taskruns) és a [feladatokra](/azure/templates/microsoft.containerregistry/2019-06-01-preview/registries/tasks)vonatkozó sablon-referenciát.


<!-- LINKS - Internal -->
[azure-cli]: /cli/azure/install-azure-cli
[az-acr-build]: /cli/azure/acr#az-acr-build
[az-acr-show]: /cli/azure/acr#az-acr-show
[az-acr-task-run]: /cli/azure/acr/task#az-acr-task-run
[az-acr-task-logs]: /cli/azure/acr/task#az-acr-task-logs
[az-acr-repository-show-tags]: /cli/azure/acr/repository#az-acr-repository-show-tags
[az-acr-task-list-runs]: /cli/azure/acr/task#az-acr-task-list-runs
[az-deployment-group-create]: /cli/azure/deployment/group#az-deployment-group-create
[az-identity-create]: /cli/azure/identity#az-identity-create
[az-identity-show]: /cli/azure/identity#az-identity-show
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
