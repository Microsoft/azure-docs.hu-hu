---
title: Oktatóanyag – tároló több lépésből álló feladatokat - Azure Container Registry feladatok
description: Ebben az oktatóanyagban megismerheti, hogyan konfigurálhatja egy Azure Container beállításjegyzék feladattal aktiválják automatikusan hozhat létre, többlépéses munkafolyamat fut, és a tárolólemezképeket a felhőben, ha a forráskód Git-tárházba véglegesítése után.
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: tutorial
ms.date: 05/09/2019
ms.author: danlep
ms.custom: seodec18, mvc
ms.openlocfilehash: 09b8e5d31bc6a4ec24633889920e2768bb7ce538
ms.sourcegitcommit: f6c85922b9e70bb83879e52c2aec6307c99a0cac
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/11/2019
ms.locfileid: "65546572"
---
# <a name="tutorial-run-a-multi-step-container-workflow-in-the-cloud-when-you-commit-source-code"></a>Oktatóanyag: Többlépéses tároló munkafolyamat futtatása a felhőben, ha forráskódot véglegesítése

Mellett egy [gyors feladat](container-registry-tutorial-quick-task.md), ACR feladatokat több lépésből álló támogatja, több container alapú munkafolyamatok, amelyek automatikusan is indíthat, amikor a véglegesítés, forráskód Git-tárházba. 

Ebben az oktatóanyagban megismerheti, hogyan használhatja példa YAML-fájlok több lépésből álló feladatokat hozhat létre, meg futtatni, és egy vagy több tárolórendszerképek leküldése egy beállításjegyzéket, ha forráskódot véglegesítése után. Hozzon létre egy feladatot, amely csak automatizálja az egy kód véglegesítésére egyetlen rendszerképből épülő, lásd: [oktatóanyag: Felhőalapú tároló-rendszerképek létrehozásának automatizálása, ha forráskódot véglegesíti](container-registry-tutorial-build-task.md). ACR-tevékenységek áttekintését lásd: [OS automatizálása és keretrendszer javítás ACR feladatok](container-registry-tasks-overview.md),

Ebben az oktatóanyagban:

> [!div class="checklist"]
> * Egy YAML-fájllal többlépéses feladat meghatározása
> * Feladat létrehozása
> * Igény szerint adja hozzá a hitelesítő adatokat a feladat egy másik beállításjegyzék-hozzáférés engedélyezése
> * A feladat tesztelése
> * Tevékenységek állapotának megtekintése
> * A feladat aktiválása kódvéglegesítéssel

Ez az oktatóanyag feltételezi, hogy elvégezte az [előző oktatóanyag](container-registry-tutorial-quick-task.md) lépéseit. Ha még nem tette meg, hajtsa végre az előző oktatóanyag [Előfeltételek](container-registry-tutorial-quick-task.md#prerequisites) szakaszának lépéseit, mielőtt továbblépne.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Ha szeretné az Azure CLI helyileg használja, rendelkeznie kell Azure CLI-verzió **2.0.62** vagy később telepítve, és bejelentkezett [az bejelentkezési][az-login]. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretné a parancssori felületet, olvassa el [az Azure CLI telepítését][azure-cli] ismertető témakört.

[!INCLUDE [container-registry-task-tutorial-prereq.md](../../includes/container-registry-task-tutorial-prereq.md)]

## <a name="create-a-multi-step-task"></a>A többlépéses feladat létrehozása

Most, hogy végrehajtotta a lépéseket, lefoglaltsági állapota és webhookok létrehozása egy ACR-feladatok engedélyezéséhez szükséges, hozzon létre egy több lépésből álló feladat, amely elindítja a létrehozását, fut, és hogyan lehet továbbítani rá egy tárolórendszerképet.

### <a name="yaml-file"></a>YAML-fájl

A többlépéses feladat lépéseinek határoz meg egy [YAML-fájl](container-registry-tasks-reference-yaml.md). A fájl definiálva van ebben az oktatóanyagban az első példa többlépéses feladat `taskmulti.yaml`, azaz a klónozott GitHub-tárház gyökérkönyvtárában található:

```yml
version: v1.0.0
steps:
# Build target image
- build: -t {{.Run.Registry}}/hello-world:{{.Run.ID}} -f Dockerfile .
# Run image 
- cmd: -t {{.Run.Registry}}/hello-world:{{.Run.ID}}
  id: test
  detach: true
  ports: ["8080:80"]
- cmd: docker stop test
# Push image
- push:
  - {{.Run.Registry}}/hello-world:{{.Run.ID}}
```

A többlépéses feladat a következőket teszi:

1. Futtatások egy `build` állítson össze egy rendszerképet a docker-fájlt a munkakönyvtárban a lépést. A kép célokat a `Run.Registry`, a beállításjegyzék, ahol a feladat fut, és a egy egyedi ACR feladatait van megjelölve Futtatás azonosítóját. 
1. Futtatások egy `cmd` lépéssel a lemezkép futtathat egy ideiglenes tárolóban. Ebben a példában egy hosszú ideig futó tároló a háttérben elindul, és adja vissza a tároló azonosítója, majd leállítja a tárolót. A való életből vett helyzet a lépések teszteléséhez győződjön meg arról, hogy megfelelően fut-e a futó tárolót tartalmazhat.
1. Az egy `push` lépést, leküldi a lemezképet, lett létrehozva, a futtatási beállításjegyzék.

### <a name="task-command"></a>Feladat paranccsal

Először lássa el ezeket a rendszerhéj-környezeti változókat a környezetnek megfelelő értékekkel. Ez a lépés nem feltétlenül szükséges, de némileg könnyebbé teszi az oktatóanyagban lévő többsoros Azure CLI-parancsok végrehajtását. Ha ezeket a környezeti változókat nem feltöltéséhez, kézzel kell lecserélnie egyes bárhol a Példaparancsok megjelenik.

```azurecli-interactive
ACR_NAME=<registry-name>        # The name of your Azure container registry
GIT_USER=<github-username>      # Your GitHub user account name
GIT_PAT=<personal-access-token> # The PAT you generated in the previous section
```

Hozza létre a tevékenység az alábbiak végrehajtásával [az acr-feladat létrehozása] [ az-acr-task-create] parancsot:

```azurecli-interactive
az acr task create \
    --registry $ACR_NAME \
    --name example1 \
    --context https://github.com/$GIT_USER/acr-build-helloworld-node.git \
    --branch master \
    --file taskmulti.yaml \
    --git-access-token $GIT_PAT
```

Ez a feladat Megadja, hogy az idő kód számára fontos, hogy a *fő* a tárház által megadott `--context`, ACR tevékenységek a fiókirodában lévő kódból fognak futni a többlépéses feladat. A YAML-fájl által megadott `--file` a tárházból legfelső szintű határozza meg a lépéseket. 

A sikeres [az acr task create][az-acr-task-create] parancs kimenete az alábbihoz hasonló:

```console
{
  "agentConfiguration": {
    "cpu": 2
  },
  "creationDate": "2019-05-03T03:14:31.763887+00:00",
  "credentials": null,
  "id": "/subscriptions/<Subscription ID>/resourceGroups/myregistry/providers/Microsoft.ContainerRegistry/registries/myregistry/tasks/taskmulti",
  "location": "westus",
  "name": "example1",
  "platform": {
    "architecture": "amd64",
    "os": "linux",
    "variant": null
  },
  "provisioningState": "Succeeded",
  "resourceGroup": "myresourcegroup",
  "status": "Enabled",
  "step": {
    "baseImageDependencies": null,
    "contextAccessToken": null,
    "contextPath": "https://github.com/gituser/acr-build-helloworld-node.git",
    "taskFilePath": "taskmulti.yaml",
    "type": "FileTask",
    "values": [],
    "valuesFilePath": null
  },
  "tags": null,
  "timeout": 3600,
  "trigger": {
    "baseImageTrigger": {
      "baseImageTriggerType": "Runtime",
      "name": "defaultBaseimageTriggerName",
      "status": "Enabled"
    },
    "sourceTriggers": [
      {
        "name": "defaultSourceTriggerName",
        "sourceRepository": {
          "branch": "master",
          "repositoryUrl": "https://github.com/gituser/acr-build-helloworld-node.git",
          "sourceControlAuthProperties": null,
          "sourceControlType": "Github"
        },
        "sourceTriggerEvents": [
          "commit"
        ],
        "status": "Enabled"
      }
    ]
  },
  "type": "Microsoft.ContainerRegistry/registries/tasks"
}
```

## <a name="test-the-multi-step-workflow"></a>A többlépéses munkafolyamat tesztelése

A többlépéses feladat teszteléséhez, manuális aktiválása úgy, hogy végrehajtja a [az acr-feladat futtatása] [ az-acr-task-run] parancsot:

```azurecli-interactive
az acr task run --registry $ACR_NAME --name example1
```

Alapértelmezés szerint az `az acr task run` a naplókimenetet a konzolra streameli a parancs végrehajtásakor. A kimenet futó lépéseket a feladat előrehaladását jeleníti meg. Az alábbi kimenet van sűrített fontos lépések megjelenítéséhez.

```console
Queued a run with ID: cf19
Waiting for an agent...
2019/05/03 03:03:31 Downloading source code...
2019/05/03 03:03:33 Finished downloading source code
2019/05/03 03:03:33 Using acb_vol_cfe6bd55-3076-4215-8091-6a81aec3d1b1 as the home volume
2019/05/03 03:03:33 Creating Docker network: acb_default_network, driver: 'bridge'
2019/05/03 03:03:34 Successfully set up Docker network: acb_default_network
2019/05/03 03:03:34 Setting up Docker configuration...
2019/05/03 03:03:34 Successfully set up Docker configuration
2019/05/03 03:03:34 Logging in to registry: myregistry.azurecr.io
2019/05/03 03:03:35 Successfully logged into myregistry.azurecr.io
2019/05/03 03:03:35 Executing step ID: acb_step_0. Working directory: '', Network: 'acb_default_network'
2019/05/03 03:03:35 Scanning for dependencies...
2019/05/03 03:03:36 Successfully scanned dependencies
2019/05/03 03:03:36 Launching container with name: acb_step_0
Sending build context to Docker daemon  24.06kB

[...]

Successfully built f669bfd170af
Successfully tagged myregistry.azurecr.io/hello-world:cf19
2019/05/03 03:03:43 Successfully executed container: acb_step_0
2019/05/03 03:03:43 Executing step ID: acb_step_1. Working directory: '', Network: 'acb_default_network'
2019/05/03 03:03:43 Launching container with name: acb_step_1
279b1cb6e092b64c8517c5506fcb45494cd5a0bd10a6beca3ba97f25c5d940cd
2019/05/03 03:03:44 Successfully executed container: acb_step_1
2019/05/03 03:03:44 Executing step ID: acb_step_2. Working directory: '', Network: 'acb_default_network'
2019/05/03 03:03:44 Pushing image: myregistry.azurecr.io/hello-world:cf19, attempt 1

[...]

2019/05/03 03:03:46 Successfully pushed image: myregistry.azurecr.io/hello-world:cf19
2019/05/03 03:03:46 Step ID: acb_step_0 marked as successful (elapsed time in seconds: 7.425169)
2019/05/03 03:03:46 Populating digests for step ID: acb_step_0...
2019/05/03 03:03:47 Successfully populated digests for step ID: acb_step_0
2019/05/03 03:03:47 Step ID: acb_step_1 marked as successful (elapsed time in seconds: 0.827129)
2019/05/03 03:03:47 Step ID: acb_step_2 marked as successful (elapsed time in seconds: 2.112113)
2019/05/03 03:03:47 The following dependencies were found:
2019/05/03 03:03:47
- image:
    registry: myregistry.azurecr.io
    repository: hello-world
    tag: cf19
    digest: sha256:6b981a8ca8596e840228c974c929db05c0727d8630465de536be74104693467a
  runtime-dependency:
    registry: registry.hub.docker.com
    repository: library/node
    tag: 9-alpine
    digest: sha256:8dafc0968fb4d62834d9b826d85a8feecc69bd72cd51723c62c7db67c6dec6fa
  git:
    git-head-revision: 1a3065388a0238e52865db1c8f3e97492a43444c

Run ID: cf19 was successful after 18s
```

## <a name="trigger-a-build-with-a-commit"></a>Összeállítás kiváltása véglegesítéssel

A feladat manuális futtatással való tesztelését követően aktiválja automatikusan a feladatot a forráskód módosításával.

Először ellenőrizze, hogy az [adattár][sample-repo] helyi klónját tartalmazó könyvtárban van:

```azurecli-interactive
cd acr-build-helloworld-node
```

Ezután az alábbi parancsok futtatásával hozzon létre, véglegesítsen és küldjön le egy új fájlt a GitHub-adattár adott elágazásába:

```azurecli-interactive
echo "Hello World!" > hello.txt
git add hello.txt
git commit -m "Testing ACR Tasks"
git push origin master
```

Előfordulhat, hogy a rendszer a GitHub hitelesítő adatok megadását kéri a `git push` parancs végrehajtásakor. Adja meg GitHub-felhasználónevét, valamint a jelszóként korábban létrehozott személyes hozzáférési jogkivonatot (PAT).

```console
$ git push origin master
Username for 'https://github.com': <github-username>
Password for 'https://githubuser@github.com': <personal-access-token>
```

Egy véglegesítés az adattár már leküldött, miután a webhook ACR tevékenységek által létrehozott akkor aktiválódik, majd a feladat az Azure Container Registry címmel. Megjeleníti az aktuálisan futó feladat naplóit az összeállítási folyamat ellenőrzéséhez és monitorozásához:

```azurecli-interactive
az acr task logs --registry $ACR_NAME
```

A kimenet az alábbihoz hasonló, és az aktuálisan (vagy legutóbb) futtatott feladatot mutatja:

```console
$ az acr task logs --registry $ACR_NAME
Showing logs of the last created run.
Run ID: cf1d

[...]

Run ID: cf1d was successful after 37s
```

## <a name="list-builds"></a>Összeállítások listázása

Az ACR Tasks által a regisztrációs adatbázis számára elkészített feladatfuttatások listájának megtekintéséhez futtassa az [az acr task list-runs][az-acr-task-list-runs] parancsot:

```azurecli-interactive
az acr task list-runs --registry $ACR_NAME --output table
```

A parancs kimenete az alábbihoz hasonlóan néz ki. Megjelennek az ACR Tasks által végrehajtott futtatások, és a „Git Commit” (Gitbeli véglegesítés) jelölés jelenik meg a legutóbbi feladat TRIGGER oszlopában:

```console
$ az acr task list-runs --registry $ACR_NAME --output table

RUN ID    TASK       PLATFORM    STATUS     TRIGGER    STARTED               DURATION
--------  ---------  ----------  ---------  ---------  --------------------  ----------
cf1d      example1   linux       Succeeded  Commit     2019-05-03T04:16:44Z  00:00:37
cf1c      example1   linux       Succeeded  Commit     2019-05-03T04:16:44Z  00:00:39
cf1b      example1   linux       Succeeded  Manual     2019-05-03T03:10:30Z  00:00:31
cf1a      example1   linux       Succeeded  Commit     2019-05-03T03:09:32Z  00:00:31
cf19      example1   linux       Succeeded  Manual     2019-05-03T03:03:30Z  00:00:21
```

## <a name="create-a-multi-registry-multi-step-task"></a>Több beállításjegyzék többlépéses feladat létrehozása

ACR-feladatok alapértelmezés szerint leküldéses vagy rendszerképek lekérése a beállításjegyzékben, amelyen fut a feladat számára. Érdemes egy vagy több beállításjegyzékek, amellett a futtatási beállításjegyzék célzó többlépéses feladat futtatása. Szüksége lehet például egy regisztrációs adatbázisban található rendszerképek elkészítéséhez és lemezképek tárolása az eltérő címkéket éles rendszerek által elért második beállításjegyzékben. Ez a példa bemutatja, hogyan hozhat létre egy ilyen feladat, és adja meg egy másik beállításjegyzék hitelesítő adatait.

Ha még nem rendelkezik második beállításjegyzék, hozzon létre egy ebben a példában a. Ha a beállításjegyzék van szüksége, tekintse meg a [előző oktatóanyagban](container-registry-tutorial-quick-task.md), vagy [a rövid útmutató: Hozzon létre egy tároló-beállításjegyzéket az Azure CLI-vel](container-registry-get-started-azure-cli.md).

A feladat létrehozásához, az űrlap a tárolójegyzék bejelentkezési kiszolgálójának nevét kell *mycontainerregistrydate.azurecr.io* (csak kisbetűvel). Ebben a példában a második beállításjegyzék létrehozási dátum szerint címkézett képek használata.

### <a name="yaml-file"></a>YAML-fájl

A fájl definiálva van ebben az oktatóanyagban a második példa többlépéses feladat `taskmulti-multiregistry.yaml`, azaz a klónozott GitHub-tárház gyökérkönyvtárában található:

```yml
version: v1.0.0
steps:
# Build target images
- build: -t {{.Run.Registry}}/hello-world:{{.Run.ID}} -f Dockerfile .
- build: -t {{.Values.regDate}}/hello-world:{{.Run.Date}} -f Dockerfile .
# Run image 
- cmd: -t {{.Run.Registry}}/hello-world:{{.Run.ID}}
  id: test
  detach: true
  ports: ["8080:80"]
- cmd: docker stop test
# Push images
- push:
  - {{.Run.Registry}}/hello-world:{{.Run.ID}}
  - {{.Values.regDate}}/hello-world:{{.Run.Date}}
```

A többlépéses feladat a következőket teszi:

1. Két futtató `build` lemezképeket a docker-fájlt a munkakönyvtárban a lépéseket:
    * Az első célok a `Run.Registry`, a beállításjegyzékben, ahol a feladat fut, és van megjelölve az ACR-feladatok futtatása azonosítója. 
    * A második célozza meg, a beállításjegyzék értékét által azonosított `regDate`, amely a feladat létrehozásakor beállított (, vagy adjon meg egy külső keresztül `values.yaml` fájl átadott `az acr task create`). Ez a rendszerkép a futtatási dátummal van megjelölve.
1. Futtatások egy `cmd` lépés a beépített tárolók futtatásához. Ebben a példában egy hosszú ideig futó tároló a háttérben elindul, és adja vissza a tároló azonosítója, majd leállítja a tárolót. A való életből vett helyzet előfordulhat, hogy tesztelje, győződjön meg arról, hogy megfelelően fut-e a futó tárolót.
1. Az egy `push` lépést, leküldi a lemezképeket, amelyek készült, az első a futtatási regisztrációs adatbázisba, a másik pedig a beállításjegyzék által azonosított `regDate`.

### <a name="task-command"></a>Feladat paranccsal

A feladat a korábban meghatározott rendszerhéj környezeti változók használatával létrehozásához az alábbiak végrehajtásával [az acr-feladat létrehozása] [ az-acr-task-create] parancsot. A tárolójegyzéket nevét *mycontainerregistrydate*.

```azurecli-interactive
az acr task create \
    --registry $ACR_NAME \
    --name example2 \
    --context https://github.com/$GIT_USER/acr-build-helloworld-node.git \
    --branch master \
    --file taskmulti-image.yaml \
    --git-access-token $GIT_PAT \
    --set regDate=mycontainerregistrydate.azurecr.io
```

### <a name="add-task-credential"></a>Feladat hitelesítő adatainak hozzáadása

A beállításjegyzék értékét által azonosított leküldéses lemezképek `regDate`, használja a [az acr feladat hitelesítő adatok hozzáadása] [ az-acr-task-credential-add] parancs használatával adja hozzá a beállításjegyzékhez a bejelentkezési hitelesítő adatokat a feladathoz.

Ebben a példában azt javasoljuk, hogy hozzon létre egy [szolgáltatásnév](container-registry-auth-service-principal.md) hatóköre a beállításjegyzék-hozzáféréssel rendelkező a *AcrPush* szerepkör. A szolgáltatásnév létrehozásához tekintse meg ezt [Azure CLI-példaszkript](https://github.com/Azure-Samples/azure-cli-samples/blob/master/container-registry/service-principal-create/service-principal-create.sh).

A szolgáltatás egyszerű alkalmazás Azonosítóját és jelszavát adja át a következő `az acr task credential add` parancsot:

```azurecli-interactive
az acr task credential add --name example2 \
    --registry $ACR_NAME \
    --login-server mycontainerregistrydate.azurecr.io \
    --username <service-principal-application-id> \
    --password <service-principal-password>
```

A parancssori felület a hozzáadott tárolójegyzék bejelentkezési kiszolgálójának nevét adja vissza.

### <a name="test-the-multi-step-workflow"></a>A többlépéses munkafolyamat tesztelése

Az előző példában a többlépéses feladat teszteléséhez, manuális aktiválása úgy, hogy végrehajtja a [az acr-feladat futtatása] [ az-acr-task-run] parancsot. Egy véglegesítés a Git-tárházba feladat indításához című [elindítani a véglegesítéshez](#trigger-a-build-with-a-commit).

```azurecli-interactive
az acr task run --registry $ACR_NAME --name example2
```

Alapértelmezés szerint az `az acr task run` a naplókimenetet a konzolra streameli a parancs végrehajtásakor. Mint előtt, a megjelenítheti futó lépéseket a feladat előrehaladását. A kimenet van sűrített fontos lépések megjelenítése

Kimenet:

```console
Queued a run with ID: cf1g
Waiting for an agent...
2019/05/03 04:33:39 Downloading source code...
2019/05/03 04:33:41 Finished downloading source code
2019/05/03 04:33:42 Using acb_vol_4569b017-29fe-42bd-83b2-25c45a8ac807 as the home volume
2019/05/03 04:33:42 Creating Docker network: acb_default_network, driver: 'bridge'
2019/05/03 04:33:43 Successfully set up Docker network: acb_default_network
2019/05/03 04:33:43 Setting up Docker configuration...
2019/05/03 04:33:44 Successfully set up Docker configuration
2019/05/03 04:33:44 Logging in to registry: mycontainerregistry.azurecr.io
2019/05/03 04:33:45 Successfully logged into mycontainerregistry.azurecr.io
2019/05/03 04:33:45 Logging in to registry: mycontainerregistrydate.azurecr.io
2019/05/03 04:33:47 Successfully logged into mycontainerregistrydate.azurecr.io
2019/05/03 04:33:47 Executing step ID: acb_step_0. Working directory: '', Network: 'acb_default_network'
2019/05/03 04:33:47 Scanning for dependencies...
2019/05/03 04:33:47 Successfully scanned dependencies
2019/05/03 04:33:47 Launching container with name: acb_step_0
Sending build context to Docker daemon  25.09kB

[...]

Successfully tagged mycontainerregistry.azurecr.io/hello-world:cf1g
2019/05/03 04:33:55 Successfully executed container: acb_step_0
2019/05/03 04:33:55 Executing step ID: acb_step_1. Working directory: '', Network: 'acb_default_network'
2019/05/03 04:33:55 Scanning for dependencies...
2019/05/03 04:33:56 Successfully scanned dependencies
2019/05/03 04:33:56 Launching container with name: acb_step_1
Sending build context to Docker daemon  25.09kB

[...]

Successfully tagged mycontainerregistrydate.azurecr.io/hello-world:20190503-043342z
2019/05/03 04:33:57 Successfully executed container: acb_step_1
2019/05/03 04:33:57 Executing step ID: acb_step_2. Working directory: '', Network: 'acb_default_network'
2019/05/03 04:33:57 Launching container with name: acb_step_2
721437ff674051b6be63cbcd2fa8eb085eacbf38d7d632f1a079320133182101
2019/05/03 04:33:58 Successfully executed container: acb_step_2
2019/05/03 04:33:58 Executing step ID: acb_step_3. Working directory: '', Network: 'acb_default_network'
2019/05/03 04:33:58 Launching container with name: acb_step_3
test
2019/05/03 04:34:09 Successfully executed container: acb_step_3
2019/05/03 04:34:09 Executing step ID: acb_step_4. Working directory: '', Network: 'acb_default_network'
2019/05/03 04:34:09 Pushing image: mycontainerregistry.azurecr.io/hello-world:cf1g, attempt 1
The push refers to repository [mycontainerregistry.azurecr.io/hello-world]

[...]

2019/05/03 04:34:12 Successfully pushed image: mycontainerregistry.azurecr.io/hello-world:cf1g
2019/05/03 04:34:12 Pushing image: mycontainerregistrydate.azurecr.io/hello-world:20190503-043342z, attempt 1
The push refers to repository [mycontainerregistrydate.azurecr.io/hello-world]

[...]

2019/05/03 04:34:19 Successfully pushed image: mycontainerregistrydate.azurecr.io/hello-world:20190503-043342z
2019/05/03 04:34:19 Step ID: acb_step_0 marked as successful (elapsed time in seconds: 8.125744)
2019/05/03 04:34:19 Populating digests for step ID: acb_step_0...
2019/05/03 04:34:21 Successfully populated digests for step ID: acb_step_0
2019/05/03 04:34:21 Step ID: acb_step_1 marked as successful (elapsed time in seconds: 2.009281)
2019/05/03 04:34:21 Populating digests for step ID: acb_step_1...
2019/05/03 04:34:23 Successfully populated digests for step ID: acb_step_1
2019/05/03 04:34:23 Step ID: acb_step_2 marked as successful (elapsed time in seconds: 0.795440)
2019/05/03 04:34:23 Step ID: acb_step_3 marked as successful (elapsed time in seconds: 11.446775)
2019/05/03 04:34:23 Step ID: acb_step_4 marked as successful (elapsed time in seconds: 9.734973)
2019/05/03 04:34:23 The following dependencies were found:
2019/05/03 04:34:23
- image:
    registry: mycontainerregistry.azurecr.io
    repository: hello-world
    tag: cf1g
    digest: sha256:75354e9edb995e8661438bad9913deed87a185fddd0193811f916d684b71a5d2
  runtime-dependency:
    registry: registry.hub.docker.com
    repository: library/node
    tag: 9-alpine
    digest: sha256:8dafc0968fb4d62834d9b826d85a8feecc69bd72cd51723c62c7db67c6dec6fa
  git:
    git-head-revision: 9d9023473c46a5e2c315681b11eb4552ef0faccc
- image:
    registry: mycontainerregistrydate.azurecr.io
    repository: hello-world
    tag: 20190503-043342z
    digest: sha256:75354e9edb995e8661438bad9913deed87a185fddd0193811f916d684b71a5d2
  runtime-dependency:
    registry: registry.hub.docker.com
    repository: library/node
    tag: 9-alpine
    digest: sha256:8dafc0968fb4d62834d9b826d85a8feecc69bd72cd51723c62c7db67c6dec6fa
  git:
    git-head-revision: 9d9023473c46a5e2c315681b11eb4552ef0faccc

Run ID: cf1g was successful after 46s
```

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban megtudhatta, hogyan hozhat létre, amely automatikusan indítható el, ha a forráskód Git-tárházba véglegesítése több lépésből álló, több container alapú feladatok. Speciális funkcióit több lépésből álló feladatokat, beleértve a párhuzamos és függő lépés végrehajtásával, lásd: a [ACR feladatok YAML referencia](container-registry-tasks-reference-yaml.md). Folytassa a következő oktatóanyaggal, amelyből megtudhatja, hogyan hozhat létre olyan feladatokat, amelyek a tárolórendszerképek alapként szolgáló rendszerképeinek frissítésekor aktiválnak összeállításokat.

> [!div class="nextstepaction"]
> [Összeállítások automatizálása alapként szolgáló rendszerképek frissítésekor](container-registry-tutorial-base-image-update.md)

<!-- LINKS - External -->
[sample-repo]: https://github.com/Azure-Samples/acr-build-helloworld-node

<!-- LINKS - Internal -->
[azure-cli]: /cli/azure/install-azure-cli
[az-acr-task]: /cli/azure/acr/task
[az-acr-task-create]: /cli/azure/acr/task#az-acr-task-create
[az-acr-task-run]: /cli/azure/acr/task#az-acr-task-run
[az-acr-task-list-runs]: /cli/azure/acr/task#az-acr-task-list-runs
[az-acr-task-credential-add]: /cli/azure/acr/task/credential#az-acr-task-credential-add    
[az-login]: /cli/azure/reference-index#az-login

<!-- IMAGES -->
[build-task-01-new-token]: ./media/container-registry-tutorial-build-tasks/build-task-01-new-token.png
[build-task-02-generated-token]: ./media/container-registry-tutorial-build-tasks/build-task-02-generated-token.png
