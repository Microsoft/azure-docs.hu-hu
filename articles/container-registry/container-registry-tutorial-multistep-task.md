---
title: Oktatóanyag – Többlépéses ACR-feladat
description: Ez az oktatóanyag bemutatja, hogyan konfigurálhat egy Azure Container Registry-feladatot egy többlépéses munkafolyamat automatikus aktiválásához, amely tároló-rendszerképeket hoz létre, futtat és lekérhetők a felhőben, amikor forráskódot véglegesít egy Git-adattárban.
ms.topic: tutorial
ms.date: 11/24/2020
ms.custom: seodec18, mvc, devx-track-azurecli
ms.openlocfilehash: 6d6ca8c41ce808df4b32bc10a7b537ffae809b3a
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107780676"
---
# <a name="tutorial-run-a-multi-step-container-workflow-in-the-cloud-when-you-commit-source-code"></a>Oktatóanyag: Többlépéses tároló-munkafolyamat futtatása a felhőben forráskód véglegesítésekor

A gyors feladat mellett [a](container-registry-tutorial-quick-task.md)ACR-feladatok többlépéses, többtárolós munkafolyamatokat is támogat, amelyek automatikusan aktiválódnak, amikor forráskódot véglegesít egy Git-adattárban. 

Ebből az oktatóanyagból megtudhatja, hogyan használhatja a YAML-példafájlokat olyan többlépéses feladatok meghatározásához, amelyek egy vagy több tároló-rendszerképet hoznak létre, futtatnak és továbbkullának egy regisztrációs adatbázisba a forráskód véglegesítéséhez. Ha olyan feladatot hoz létre, amely csak egyetlen rendszerképet automatizál kód véglegesítéskor, tekintse meg a következőt: Oktatóanyag: Tárolólemezképek buildének automatizálása a felhőben forráskód [véglegesítéskor.](container-registry-tutorial-build-task.md) Az operációs rendszer frissítésének ACR-feladatok: Az operációs rendszer és a keretrendszer javításának [automatizálása a ACR-feladatok,](container-registry-tasks-overview.md)

Ebben az oktatóanyagban:

> [!div class="checklist"]
> * Többlépéses feladat meghatározása YAML-fájl használatával
> * Tevékenység létrehozása
> * Másik beállításjegyzékhez való hozzáférés engedélyezéséhez hitelesítő adatokat is hozzáadhat a feladathoz
> * A feladat tesztelése
> * Tevékenységek állapotának megtekintése
> * A feladat aktiválása kódvéglegesítéssel

Ez az oktatóanyag feltételezi, hogy elvégezte az [előző oktatóanyag](container-registry-tutorial-quick-task.md) lépéseit. Ha még nem tette meg, hajtsa végre az előző oktatóanyag [Előfeltételek](container-registry-tutorial-quick-task.md#prerequisites) szakaszának lépéseit, mielőtt továbblépne.

[!INCLUDE [container-registry-task-tutorial-prereq.md](../../includes/container-registry-task-tutorial-prereq.md)]
[!INCLUDE [azure-cli-prepare-your-environment-h3.md](../../includes/azure-cli-prepare-your-environment-h3.md)]

## <a name="create-a-multi-step-task"></a>Többlépéses feladat létrehozása

Most, hogy befejezte az ahhoz szükséges lépéseket, hogy a ACR-feladatok beolvassa a véglegesítési állapotot, és webhookokat hozzon létre egy adattárban, hozzon létre egy többlépéses feladatot, amely elindítja a tároló rendszerképének létrehozásához, futtatásához és leerítéséhez szükséges lépéseket.

### <a name="yaml-file"></a>YAML-fájl

A többlépéses feladatok lépéseit egy [YAML-fájlban adhatja meg.](container-registry-tasks-reference-yaml.md) Az oktatóanyag első többlépéses feladatának definíciója a fájlban található, amely a klónozott `taskmulti.yaml` GitHub-adattár gyökerében található:

```yml
version: v1.1.0
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

Ez a többlépéses feladat a következőket teszi:

1. Futtat egy `build` lépést, amely egy lemezképet hoz létre a munkakönyvtárban található Docker-fájlból. A rendszerkép célja a , a beállításjegyzék, ahol a feladat fut, és a rendszer egy egyedi ACR-feladatok `Run.Registry` azonosítóval van megcímkézve. 
1. Egy lépés `cmd` futtatásával futtatja a rendszerképet egy ideiglenes tárolóban. Ez a példa elindít egy hosszan futó tárolót a háttérben, és visszaadja a tároló azonosítóját, majd leállítja a tárolót. Valós forgatókönyvekben a futó tároló megfelelő futtatásának biztosítása érdekében érdemes lehet lépéseket tenni a futó tároló teszteléséhez.
1. Egy lépésben a lek pusholja a futtatás `push` beállításjegyzékbe felépített rendszerképet.

### <a name="task-command"></a>Feladat parancs

Először lássa el ezeket a rendszerhéj-környezeti változókat a környezetnek megfelelő értékekkel. Ez a lépés nem feltétlenül szükséges, de némileg könnyebbé teszi az oktatóanyagban lévő többsoros Azure CLI-parancsok végrehajtását. Ha nem tölti fel ezeket a környezeti változókat, minden értéket manuálisan kell lecserélnie, amikor az megjelenik a példaparancsok között.

```console
ACR_NAME=<registry-name>        # The name of your Azure container registry
GIT_USER=<github-username>      # Your GitHub user account name
GIT_PAT=<personal-access-token> # The PAT you generated in the previous section
```

Most hozza létre a feladatot a következő [az acr task create parancs végrehajtásával:][az-acr-task-create]

```azurecli-interactive
az acr task create \
    --registry $ACR_NAME \
    --name example1 \
    --context https://github.com/$GIT_USER/acr-build-helloworld-node.git#main \
    --file taskmulti.yaml \
    --git-access-token $GIT_PAT
```

Ez a feladat azt határozza meg,  hogy a ACR-feladatok a által megadott adattár fő ágában található főágba való kódbefejtéskor a többlépéses feladatot az adott ágban található kódból `--context` fogja futtatni. Az adattár gyökerében megadott YAML-fájl határozza meg `--file` a lépéseket. 

A sikeres [az acr task create][az-acr-task-create] parancs kimenete az alábbihoz hasonló:

```output
{
  "agentConfiguration": {
    "cpu": 2
  },
  "creationDate": "2020-11-20T03:14:31.763887+00:00",
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
    "contextPath": "https://github.com/gituser/acr-build-helloworld-node.git#main",
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
          "branch": "main",
          "repositoryUrl": "https://github.com/gituser/acr-build-helloworld-node.git#main",
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

A többlépéses feladat tesztelése manuálisan, az [az acr task run][az-acr-task-run] paranccsal indítható el:

```azurecli-interactive
az acr task run --registry $ACR_NAME --name example1
```

Alapértelmezés szerint az `az acr task run` a naplókimenetet a konzolra streameli a parancs végrehajtásakor. A kimenet az egyes feladat lépései futtatásának előrehaladását mutatja. Az alábbi kimenet a legfontosabb lépések megjelenítése érdekében van tömörítve.

```output
Queued a run with ID: cab
Waiting for an agent...
2020/11/20 00:03:31 Downloading source code...
2020/11/20 00:03:33 Finished downloading source code
2020/11/20 00:03:33 Using acb_vol_cfe6bd55-3076-4215-8091-6a81aec3d1b1 as the home volume
2020/11/20 00:03:33 Creating Docker network: acb_default_network, driver: 'bridge'
2020/11/20 00:03:34 Successfully set up Docker network: acb_default_network
2020/11/20 00:03:34 Setting up Docker configuration...
2020/11/20 00:03:34 Successfully set up Docker configuration
2020/11/20 00:03:34 Logging in to registry: myregistry.azurecr.io
2020/11/20 00:03:35 Successfully logged into myregistry.azurecr.io
2020/11/20 00:03:35 Executing step ID: acb_step_0. Working directory: '', Network: 'acb_default_network'
2020/11/20 00:03:35 Scanning for dependencies...
2020/11/20 00:03:36 Successfully scanned dependencies
2020/11/20 00:03:36 Launching container with name: acb_step_0
Sending build context to Docker daemon  24.06kB
[...]
Successfully built f669bfd170af
Successfully tagged myregistry.azurecr.io/hello-world:cf19
2020/11/20 00:03:43 Successfully executed container: acb_step_0
2020/11/20 00:03:43 Executing step ID: acb_step_1. Working directory: '', Network: 'acb_default_network'
2020/11/20 00:03:43 Launching container with name: acb_step_1
279b1cb6e092b64c8517c5506fcb45494cd5a0bd10a6beca3ba97f25c5d940cd
2020/11/20 00:03:44 Successfully executed container: acb_step_1
2020/11/20 00:03:44 Executing step ID: acb_step_2. Working directory: '', Network: 'acb_default_network'
2020/11/20 00:03:44 Pushing image: myregistry.azurecr.io/hello-world:cf19, attempt 1
[...]
2020/11/20 00:03:46 Successfully pushed image: myregistry.azurecr.io/hello-world:cf19
2020/11/20 00:03:46 Step ID: acb_step_0 marked as successful (elapsed time in seconds: 7.425169)
2020/11/20 00:03:46 Populating digests for step ID: acb_step_0...
2020/11/20 00:03:47 Successfully populated digests for step ID: acb_step_0
2020/11/20 00:03:47 Step ID: acb_step_1 marked as successful (elapsed time in seconds: 0.827129)
2020/11/20 00:03:47 Step ID: acb_step_2 marked as successful (elapsed time in seconds: 2.112113)
2020/11/20 00:03:47 The following dependencies were found:
2020/11/20 00:03:47
- image:
    registry: myregistry.azurecr.io
    repository: hello-world
    tag: cf19
    digest: sha256:6b981a8ca8596e840228c974c929db05c0727d8630465de536be74104693467a
  runtime-dependency:
    registry: registry.hub.docker.com
    repository: library/node
    tag: 15-alpine
    digest: sha256:8dafc0968fb4d62834d9b826d85a8feecc69bd72cd51723c62c7db67c6dec6fa
  git:
    git-head-revision: 1a3065388a0238e52865db1c8f3e97492a43444c

Run ID: cab was successful after 18s
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

```azurecli-interactive
Username for 'https://github.com': <github-username>
Password for 'https://githubuser@github.com': <personal-access-token>
```

Miután lekért egy véglegesítést az adattárba, a ACR-feladatok által létrehozott webhook Azure Container Registry. Megjeleníti az aktuálisan futó feladat naplóit az összeállítási folyamat ellenőrzéséhez és monitorozásához:

```azurecli-interactive
az acr task logs --registry $ACR_NAME
```

A kimenet az alábbihoz hasonló, és az aktuálisan (vagy legutóbb) futtatott feladatot mutatja:

```output
Showing logs of the last created run.
Run ID: cad

[...]

Run ID: cad was successful after 37s
```

## <a name="list-builds"></a>Összeállítások listázása

Az ACR Tasks által a regisztrációs adatbázis számára elkészített feladatfuttatások listájának megtekintéséhez futtassa az [az acr task list-runs][az-acr-task-list-runs] parancsot:

```azurecli-interactive
az acr task list-runs --registry $ACR_NAME --output table
```

A parancs kimenete az alábbihoz hasonlóan néz ki. Megjelennek az ACR Tasks által végrehajtott futtatások, és a „Git Commit” (Gitbeli véglegesítés) jelölés jelenik meg a legutóbbi feladat TRIGGER oszlopában:

```output
RUN ID    TASK            PLATFORM    STATUS     TRIGGER    STARTED               DURATION
--------  --------------  ----------  ---------  ---------  --------------------  ----------
cad       example1        linux       Succeeded  Commit     2020-11-20T00:22:15Z  00:00:35
cac       taskhelloworld  linux       Succeeded  Commit     2020-11-20T00:22:15Z  00:00:22
cab       example1        linux       Succeeded  Manual     2020-11-20T00:18:36Z  00:00:47
```

## <a name="create-a-multi-registry-multi-step-task"></a>Több beállításjegyzékbeli többlépéses feladat létrehozása

ACR-feladatok a feladat futtatáskor alapértelmezés szerint rendelkezik a rendszerképek leküldéséhez vagy leküldéséhez a beállításjegyzékből. Érdemes lehet többlépéses feladatot futtatni, amely a futtatás beállításjegyzéken kívül egy vagy több regisztrációs adatbázist is megcélz. Előfordulhat például, hogy egy regisztrációs adatbázisban kell rendszerképeket összeépítenie, és különböző címkékkel kell tárolnia a rendszerképeket egy második beállításjegyzékben, amelyet egy éles rendszer is hozzáfér. Ez a példa bemutatja, hogyan hozhat létre ilyen feladatot, és hogyan adhatja meg egy másik beállításjegyzék hitelesítő adatait.

Ha még nem rendelkezik második beállításjegyzékvel, hozzon létre egyet ehhez a példához. Amennyiben létre kell hoznia a regisztrációs adatbázist, tekintse meg az [előző oktatóanyagot](container-registry-tutorial-quick-task.md) vagy a [Rövid útmutató: Tárolóregisztrációs adatbázis létrehozása az Azure CLI-vel](container-registry-get-started-azure-cli.md) című cikket.

A feladat létrehozásához szüksége lesz a regisztrációs adatbázis bejelentkezési kiszolgálójának nevére, amely a mycontainerregistrydate.azurecr.io *(csak* kisbetűs). Ebben a példában a második regisztrációs adatbázist használja a build dátuma szerint címkézett rendszerképek tárolására.

### <a name="yaml-file"></a>YAML-fájl

A második példa többlépéses feladat a fájlban van definiálva, amely a klónozott `taskmulti-multiregistry.yaml` GitHub-adattár gyökerében található:

```yml
version: v1.1.0
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

Ez a többlépéses feladat a következőket teszi:

1. Két lépést `build` futtat a lemezképek a munkakönyvtárban található Docker-fájlból való felépítéséhez:
    * Az első célja a , a beállításjegyzék, ahol a feladat fut, és fel van címkézve a `Run.Registry` ACR-feladatok azonosítóval. 
    * A második a értékével azonosított beállításjegyzéket célozza, amelyet a feladat létrehozásakor (vagy a számára átadott külső fájlon keresztül) ad `regDate` `values.yaml` `az acr task create` meg. Ez a rendszerkép a futtatás dátumával van megcímkézve.
1. Futtat egy `cmd` lépést az egyik beépített tároló futtatásához. Ez a példa elindít egy hosszan futó tárolót a háttérben, és visszaadja a tároló azonosítóját, majd leállítja a tárolót. Valós helyzetben tesztelhet egy futó tárolót, hogy ellenőrizze, megfelelően fut-e.
1. Egy lépésben a lekért rendszerképeket, az elsőt a futtatás beállításjegyzékbe, a másodikat pedig a által azonosított `push` beállításjegyzékbe. `regDate`

### <a name="task-command"></a>Feladat parancs

A korábban definiált rendszerhéj-környezeti változók használatával hozza létre a feladatot a következő [az acr task create parancs végrehajtásával.][az-acr-task-create] Helyettesítse be a regisztrációs adatbázis nevét a *mycontainerregistrydate névvel.*

```azurecli-interactive
az acr task create \
    --registry $ACR_NAME \
    --name example2 \
    --context https://github.com/$GIT_USER/acr-build-helloworld-node.git#main \
    --file taskmulti-multiregistry.yaml \
    --git-access-token $GIT_PAT \
    --set regDate=mycontainerregistrydate.azurecr.io
```

### <a name="add-task-credential"></a>Feladat hitelesítő adatainak hozzáadása

Ha rendszerképeket szeretne leküldni a(z) értékével azonosított beállításjegyzékbe, az az acr task credential add paranccsal adja hozzá a regisztrációs adatbázis bejelentkezési hitelesítő adatait a `regDate` feladathoz. [][az-acr-task-credential-add]

Ebben [a](container-registry-auth-service-principal.md) példában azt javasoljuk, hogy hozzon létre egy egyszerű szolgáltatást, amely hozzáféréssel rendelkezik az *AcrPush* szerepkörre vonatkozó beállításjegyzékhez, hogy az jogosultsággal rendelkezik a rendszerképek leküldéséhez. A szolgáltatásnév létrehozásához tekintse meg ezt az [Azure CLI-szkriptet.](https://github.com/Azure-Samples/azure-cli-samples/blob/master/container-registry/service-principal-create/service-principal-create.sh)

Adja át a szolgáltatásnév alkalmazásazonosítóját és jelszavát a következő `az acr task credential add` parancsban. Mindenképpen frissítse a bejelentkezési kiszolgáló *mycontainerregistrydate* nevét a második regisztrációs adatbázis nevére:

```azurecli-interactive
az acr task credential add --name example2 \
    --registry $ACR_NAME \
    --login-server mycontainerregistrydate.azurecr.io \
    --username <service-principal-application-id> \
    --password <service-principal-password>
```

A CLI visszaadja a hozzáadott regisztrációs adatbázis bejelentkezési kiszolgálójának nevét.

### <a name="test-the-multi-step-workflow"></a>A többlépéses munkafolyamat tesztelése

Ahogy az előző példában is, a többlépéses feladat tesztelésekor manuálisan aktiválja azt [az az acr task run parancs végrehajtásával.][az-acr-task-run] Ha a feladatot a Git-adattárban véglegesítéssel aktiválni, tekintse meg [a Build aktiválása véglegesítéssel című szakaszt.](#trigger-a-build-with-a-commit)

```azurecli-interactive
az acr task run --registry $ACR_NAME --name example2
```

Alapértelmezés szerint az `az acr task run` a naplókimenetet a konzolra streameli a parancs végrehajtásakor. Ahogy korábban, a kimenet az egyes tevékenység lépései futtatásának előrehaladását mutatja. A kimenet a fő lépések megjelenítése érdekében tömörítve van.

Kimenet:

```output
Queued a run with ID: cf1g
Waiting for an agent...
2020/11/20 04:33:39 Downloading source code...
2020/11/20 04:33:41 Finished downloading source code
2020/11/20 04:33:42 Using acb_vol_4569b017-29fe-42bd-83b2-25c45a8ac807 as the home volume
2020/11/20 04:33:42 Creating Docker network: acb_default_network, driver: 'bridge'
2020/11/20 04:33:43 Successfully set up Docker network: acb_default_network
2020/11/20 04:33:43 Setting up Docker configuration...
2020/11/20 04:33:44 Successfully set up Docker configuration
2020/11/20 04:33:44 Logging in to registry: mycontainerregistry.azurecr.io
2020/11/20 04:33:45 Successfully logged into mycontainerregistry.azurecr.io
2020/11/20 04:33:45 Logging in to registry: mycontainerregistrydate.azurecr.io
2020/11/20 04:33:47 Successfully logged into mycontainerregistrydate.azurecr.io
2020/11/20 04:33:47 Executing step ID: acb_step_0. Working directory: '', Network: 'acb_default_network'
2020/11/20 04:33:47 Scanning for dependencies...
2020/11/20 04:33:47 Successfully scanned dependencies
2020/11/20 04:33:47 Launching container with name: acb_step_0
Sending build context to Docker daemon  25.09kB
[...]
Successfully tagged mycontainerregistry.azurecr.io/hello-world:cf1g
2020/11/20 04:33:55 Successfully executed container: acb_step_0
2020/11/20 04:33:55 Executing step ID: acb_step_1. Working directory: '', Network: 'acb_default_network'
2020/11/20 04:33:55 Scanning for dependencies...
2020/11/20 04:33:56 Successfully scanned dependencies
2020/11/20 04:33:56 Launching container with name: acb_step_1
Sending build context to Docker daemon  25.09kB
[...]
Successfully tagged mycontainerregistrydate.azurecr.io/hello-world:20190503-043342z
2020/11/20 04:33:57 Successfully executed container: acb_step_1
2020/11/20 04:33:57 Executing step ID: acb_step_2. Working directory: '', Network: 'acb_default_network'
2020/11/20 04:33:57 Launching container with name: acb_step_2
721437ff674051b6be63cbcd2fa8eb085eacbf38d7d632f1a079320133182101
2020/11/20 04:33:58 Successfully executed container: acb_step_2
2020/11/20 04:33:58 Executing step ID: acb_step_3. Working directory: '', Network: 'acb_default_network'
2020/11/20 04:33:58 Launching container with name: acb_step_3
test
2020/11/20 04:34:09 Successfully executed container: acb_step_3
2020/11/20 04:34:09 Executing step ID: acb_step_4. Working directory: '', Network: 'acb_default_network'
2020/11/20 04:34:09 Pushing image: mycontainerregistry.azurecr.io/hello-world:cf1g, attempt 1
The push refers to repository [mycontainerregistry.azurecr.io/hello-world]
[...]
2020/11/20 04:34:12 Successfully pushed image: mycontainerregistry.azurecr.io/hello-world:cf1g
2020/11/20 04:34:12 Pushing image: mycontainerregistrydate.azurecr.io/hello-world:20190503-043342z, attempt 1
The push refers to repository [mycontainerregistrydate.azurecr.io/hello-world]
[...]
2020/11/20 04:34:19 Successfully pushed image: mycontainerregistrydate.azurecr.io/hello-world:20190503-043342z
2020/11/20 04:34:19 Step ID: acb_step_0 marked as successful (elapsed time in seconds: 8.125744)
2020/11/20 04:34:19 Populating digests for step ID: acb_step_0...
2020/11/20 04:34:21 Successfully populated digests for step ID: acb_step_0
2020/11/20 04:34:21 Step ID: acb_step_1 marked as successful (elapsed time in seconds: 2.009281)
2020/11/20 04:34:21 Populating digests for step ID: acb_step_1...
2020/11/20 04:34:23 Successfully populated digests for step ID: acb_step_1
2020/11/20 04:34:23 Step ID: acb_step_2 marked as successful (elapsed time in seconds: 0.795440)
2020/11/20 04:34:23 Step ID: acb_step_3 marked as successful (elapsed time in seconds: 11.446775)
2020/11/20 04:34:23 Step ID: acb_step_4 marked as successful (elapsed time in seconds: 9.734973)
2020/11/20 04:34:23 The following dependencies were found:
2020/11/20 04:34:23
- image:
    registry: mycontainerregistry.azurecr.io
    repository: hello-world
    tag: cf1g
    digest: sha256:75354e9edb995e8661438bad9913deed87a185fddd0193811f916d684b71a5d2
  runtime-dependency:
    registry: registry.hub.docker.com
    repository: library/node
    tag: 15-alpine
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
    tag: 15-alpine
    digest: sha256:8dafc0968fb4d62834d9b826d85a8feecc69bd72cd51723c62c7db67c6dec6fa
  git:
    git-head-revision: 9d9023473c46a5e2c315681b11eb4552ef0faccc

Run ID: cf1g was successful after 46s
```

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban megtanulta, hogyan hozhat létre többlépéses, többtárolós feladatokat, amelyek automatikusan aktiválódnak, amikor forráskódot véglegesít egy Git-adattárban. A többlépéses feladatok speciális funkcióit, beleértve a párhuzamos és a függő lépésvégrehajtást is, tekintse meg a [ACR-feladatok YAML-referenciát.](container-registry-tasks-reference-yaml.md) Folytassa a következő oktatóanyaggal, amelyből megtudhatja, hogyan hozhat létre olyan feladatokat, amelyek a tárolórendszerképek alapként szolgáló rendszerképeinek frissítésekor aktiválnak összeállításokat.

> [!div class="nextstepaction"]
> [Összeállítások automatizálása alapként szolgáló rendszerképek frissítésekor](container-registry-tutorial-base-image-update.md)

<!-- LINKS - External -->
[sample-repo]: https://github.com/Azure-Samples/acr-build-helloworld-node

<!-- LINKS - Internal -->
[azure-cli]: /cli/azure/install-azure-cli
[az-acr-task]: /cli/azure/acr/task
[az-acr-task-create]: /cli/azure/acr/task#az_acr_task_create
[az-acr-task-run]: /cli/azure/acr/task#az_acr_task_run
[az-acr-task-list-runs]: /cli/azure/acr/task#az_acr_task_list_runs
[az-acr-task-credential-add]: /cli/azure/acr/task/credential#az_acr_task_credential_add    
[az-login]: /cli/azure/reference-index#az_login

<!-- IMAGES -->
[build-task-01-new-token]: ./media/container-registry-tutorial-build-tasks/build-task-01-new-token.png
[build-task-02-generated-token]: ./media/container-registry-tutorial-build-tasks/build-task-02-generated-token.png
