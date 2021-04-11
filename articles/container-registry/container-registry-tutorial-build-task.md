---
title: Oktatóanyag – rendszerkép létrehozása kód véglegesítve
description: Ebből az oktatóanyagból megtudhatja, hogyan konfigurálhat egy Azure Container Registry feladatot úgy, hogy automatikusan aktiválja a tároló rendszerképét a felhőben, amikor a forráskódot egy git-tárházba véglegesíti.
ms.topic: tutorial
ms.date: 11/24/2020
ms.custom: seodec18, mvc, devx-track-azurecli
ms.openlocfilehash: 9c642a6c52a2d992c617993964bedd3ee04a7076
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/31/2021
ms.locfileid: "106060329"
---
# <a name="tutorial-automate-container-image-builds-in-the-cloud-when-you-commit-source-code"></a>Oktatóanyag: Tárolólemezképek összeállításának automatizálása a felhőben forráskód véglegesítésekor

Egy [gyors feladaton](container-registry-tutorial-quick-task.md)kívül az ACR-feladatok támogatják az automatizált Docker-tárolók rendszerképét a felhőben, amikor a forráskódot egy git-tárházba véglegesíti. Az ACR-feladatokhoz támogatott git-környezetek a következők lehetnek: nyilvános vagy privát GitHub vagy Azure Repos.

> [!NOTE]
> Az ACR-feladatok jelenleg nem támogatják a commit vagy a pull kérelem eseményindítóit a GitHub Enterprise reposban.

Ebben az oktatóanyagban az ACR-feladat létrehozza és leküldi a Docker megadott egyetlen tároló-rendszerképet, amikor egy git-tárházba véglegesíti a forráskódot. Ha olyan [többlépéses feladatot](container-registry-tasks-multi-step.md) szeretne létrehozni, amely egy YAML-fájlt használ a kód végrehajtásához szükséges több tároló létrehozásához, leküldéséhez és opcionális teszteléséhez, tekintse meg [az oktatóanyag: többlépéses tároló-munkafolyamat futtatása a felhőben a forráskód](container-registry-tutorial-multistep-task.md)beléptetése során című témakört. Az ACR-feladatok áttekintését lásd: az [operációs rendszer és a keretrendszer javításának automatizálása az ACR-feladatokkal](container-registry-tasks-overview.md)

Ebben az oktatóanyagban:

> [!div class="checklist"]
> * Tevékenység létrehozása
> * A feladat tesztelése
> * Tevékenységek állapotának megtekintése
> * A feladat aktiválása kódvéglegesítéssel

Ez az oktatóanyag feltételezi, hogy elvégezte az [előző oktatóanyag](container-registry-tutorial-quick-task.md) lépéseit. Ha még nem tette meg, hajtsa végre az előző oktatóanyag [Előfeltételek](container-registry-tutorial-quick-task.md#prerequisites) szakaszának lépéseit, mielőtt továbblépne.

[!INCLUDE [container-registry-task-tutorial-prereq.md](../../includes/container-registry-task-tutorial-prereq.md)]
[!INCLUDE [azure-cli-prepare-your-environment-h3.md](../../includes/azure-cli-prepare-your-environment-h3.md)]

## <a name="create-the-build-task"></a>Az összeállítási feladat létrehozása

Most, miután végrehajtotta az ahhoz szükséges lépéseket, hogy az ACR Tasks olvashassa a véglegesítési állapotokat, és webhookokat hozhasson létre egy adattárban, létrehozhat egy feladatot, amely egy tárolórendszerkép összeállítását váltja ki az adattárban való véglegesítés esetén.

Először lássa el ezeket a rendszerhéj-környezeti változókat a környezetnek megfelelő értékekkel. Ez a lépés nem feltétlenül szükséges, de némileg könnyebbé teszi az oktatóanyagban lévő többsoros Azure CLI-parancsok végrehajtását. Ha nem tölti fel ezeket a környezeti változókat, manuálisan kell lecserélnie az egyes értékeket, bárhol is megjelenjenek a példában szereplő parancsokban.

```console
ACR_NAME=<registry-name>        # The name of your Azure container registry
GIT_USER=<github-username>      # Your GitHub user account name
GIT_PAT=<personal-access-token> # The PAT you generated in the previous section
```

Most hozza létre a feladatot a következő az [ACR Task Create][az-acr-task-create] parancs végrehajtásával:

```azurecli
az acr task create \
    --registry $ACR_NAME \
    --name taskhelloworld \
    --image helloworld:{{.Run.ID}} \
    --context https://github.com/$GIT_USER/acr-build-helloworld-node.git#main \
    --file Dockerfile \
    --git-access-token $GIT_PAT
```


Ez a feladat azt adja meg, hogy a rendszer minden alkalommal véglegesíti az adott adattár *fő* ágát `--context` , az ACR-feladatok pedig az adott ág kódjából fogják felépíteni a tároló rendszerképét. A `--file` rendszer a tárház gyökerében megadott Docker használja a rendszerkép létrehozásához. Az `--image` argumentum a `{{.Run.ID}}` egy parametrikus értékét adja meg a rendszerkép címkéjének a verzióra vonatkozó részéhez, ezzel biztosítva, hogy az összeállított rendszerkép egy adott összeállításhoz tartozzon és egyedi címkével legyen jelölve.

A sikeres [az acr task create][az-acr-task-create] parancs kimenete az alábbihoz hasonló:

```output
{
  "agentConfiguration": {
    "cpu": 2
  },
  "creationDate": "2010-11-19T22:42:32.972298+00:00",
  "id": "/subscriptions/<Subscription ID>/resourceGroups/myregistry/providers/Microsoft.ContainerRegistry/registries/myregistry/tasks/taskhelloworld",
  "location": "westcentralus",
  "name": "taskhelloworld",
  "platform": {
    "architecture": "amd64",
    "os": "Linux",
    "variant": null
  },
  "provisioningState": "Succeeded",
  "resourceGroup": "myregistry",
  "status": "Enabled",
  "step": {
    "arguments": [],
    "baseImageDependencies": null,
    "contextPath": "https://github.com/gituser/acr-build-helloworld-node#main",
    "dockerFilePath": "Dockerfile",
    "imageNames": [
      "helloworld:{{.Run.ID}}"
    ],
    "isPushEnabled": true,
    "noCache": false,
    "type": "Docker"
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
          "branch": "main",
          "repositoryUrl": "https://github.com/gituser/acr-build-helloworld-node#main",
          "sourceControlAuthProperties": null,
          "sourceControlType": "GitHub"
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

## <a name="test-the-build-task"></a>Az összeállítási feladat tesztelése

Most már rendelkezik az összeállítást definiáló feladattal. Az összeállítási folyamat teszteléséhez aktiváljon manuálisan egy összeállítást az [az acr task run][az-acr-task-run] parancs végrehajtásával:

```azurecli
az acr task run --registry $ACR_NAME --name taskhelloworld
```

Alapértelmezés szerint az `az acr task run` a naplókimenetet a konzolra streameli a parancs végrehajtásakor. A kimenet a legfontosabb lépések megjelenítéséhez van tömörítve.

```output
2020/11/19 22:51:00 Using acb_vol_9ee1f28c-4fd4-43c8-a651-f0ed027bbf0e as the home volume
2020/11/19 22:51:00 Setting up Docker configuration...
2020/11/19 22:51:02 Successfully set up Docker configuration
2020/11/19 22:51:02 Logging in to registry: myregistry.azurecr.io
2020/11/19 22:51:03 Successfully logged in
2020/11/19 22:51:03 Executing step: build
2020/11/19 22:51:03 Obtaining source code and scanning for dependencies...
2020/11/19 22:51:05 Successfully obtained source code and scanned for dependencies
Sending build context to Docker daemon  23.04kB
Step 1/5 : FROM node:15-alpine
[...]
Step 5/5 : CMD ["node", "/src/server.js"]
 ---> Running in 7382eea2a56a
Removing intermediate container 7382eea2a56a
 ---> e33cd684027b
Successfully built e33cd684027b
Successfully tagged myregistry.azurecr.io/helloworld:da2
2020/11/19 22:51:11 Executing step: push
2020/11/19 22:51:11 Pushing image: myregistry.azurecr.io/helloworld:da2, attempt 1
The push refers to repository [myregistry.azurecr.io/helloworld]
4a853682c993: Preparing
[...]
4a853682c993: Pushed
[...]
da2: digest: sha256:c24e62fd848544a5a87f06ea60109dbef9624d03b1124bfe03e1d2c11fd62419 size: 1366
2020/11/19 22:51:21 Successfully pushed image: myregistry.azurecr.io/helloworld:da2
2020/11/19 22:51:21 Step id: build marked as successful (elapsed time in seconds: 7.198937)
2020/11/19 22:51:21 Populating digests for step id: build...
2020/11/19 22:51:22 Successfully populated digests for step id: build
2020/11/19 22:51:22 Step id: push marked as successful (elapsed time in seconds: 10.180456)
The following dependencies were found:
- image:
    registry: myregistry.azurecr.io
    repository: helloworld
    tag: da2
    digest: sha256:c24e62fd848544a5a87f06ea60109dbef9624d03b1124bfe03e1d2c11fd62419
  runtime-dependency:
    registry: registry.hub.docker.com
    repository: library/node
    tag: 9-alpine
    digest: sha256:8dafc0968fb4d62834d9b826d85a8feecc69bd72cd51723c62c7db67c6dec6fa
  git:
    git-head-revision: 68cdf2a37cdae0873b8e2f1c4d80ca60541029bf


Run ID: ca6 was successful after 27s
```

## <a name="trigger-a-build-with-a-commit"></a>Összeállítás kiváltása véglegesítéssel

A feladat manuális futtatással való tesztelését követően aktiválja automatikusan a feladatot a forráskód módosításával.

Először ellenőrizze, hogy az [adattár][sample-repo] helyi klónját tartalmazó könyvtárban van:

```console
cd acr-build-helloworld-node
```

Ezután az alábbi parancsok futtatásával hozzon létre, véglegesítsen és küldjön le egy új fájlt a GitHub-adattár adott elágazásába:

```console
echo "Hello World!" > hello.txt
git add hello.txt
git commit -m "Testing ACR Tasks"
git push origin main
```

Előfordulhat, hogy a rendszer a GitHub hitelesítő adatok megadását kéri a `git push` parancs végrehajtásakor. Adja meg GitHub-felhasználónevét, valamint a jelszóként korábban létrehozott személyes hozzáférési jogkivonatot (PAT).

```azurecli
Username for 'https://github.com': <github-username>
Password for 'https://githubuser@github.com': <personal-access-token>
```

Miután leküldött egy véglegesítést az adattárba, az ACR Tasks által létrehozott webhook aktiválódik, és elindít egy összeállítást az Azure Container Registryben. Megjeleníti az aktuálisan futó feladat naplóit az összeállítási folyamat ellenőrzéséhez és monitorozásához:

```azurecli
az acr task logs --registry $ACR_NAME
```

A kimenet az alábbihoz hasonló, és az aktuálisan (vagy legutóbb) futtatott feladatot mutatja:

```output
Showing logs of the last created run.
Run ID: ca7

[...]

Run ID: ca7 was successful after 38s
```

## <a name="list-builds"></a>Összeállítások listázása

Az ACR Tasks által a regisztrációs adatbázis számára elkészített feladatfuttatások listájának megtekintéséhez futtassa az [az acr task list-runs][az-acr-task-list-runs] parancsot:

```azurecli
az acr task list-runs --registry $ACR_NAME --output table
```

A parancs kimenete az alábbihoz hasonlóan néz ki. Megjelennek az ACR Tasks által végrehajtott futtatások, és a „Git Commit” (Gitbeli véglegesítés) jelölés jelenik meg a legutóbbi feladat TRIGGER oszlopában:

```output
RUN ID    TASK            PLATFORM    STATUS     TRIGGER    STARTED               DURATION
--------  --------------  ----------  ---------  ---------  --------------------  ----------
ca7       taskhelloworld  linux       Succeeded  Commit     2020-11-19T22:54:34Z  00:00:29
ca6       taskhelloworld  linux       Succeeded  Manual     2020-11-19T22:51:47Z  00:00:24
ca5                       linux       Succeeded  Manual     2020-11-19T22:23:42Z  00:00:23
```

## <a name="next-steps"></a>Következő lépések

Ez az oktatóanyag azt mutatta be, hogyan használhatók a feladatok a tárolórendszerképek összeállításának automatikus aktiválására az Azure-ban, amikor forráskódot véglegesít egy Git-adattárban. Folytassa a következő oktatóanyaggal, amelyből megtudhatja, hogyan hozhat létre olyan feladatokat, amelyek a tárolórendszerképek alapként szolgáló rendszerképeinek frissítésekor aktiválnak összeállításokat.

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
[az-login]: /cli/azure/reference-index#az-login



