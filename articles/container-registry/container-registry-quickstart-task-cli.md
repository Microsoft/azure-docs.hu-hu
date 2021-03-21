---
title: Rövid útmutató – tároló-rendszerkép létrehozása igény szerint az Azure-ban
description: Az Azure-felhőben Azure Container Registry parancsokkal gyorsan készíthet, küldhet és futtathat Docker-tároló rendszerképet.
ms.topic: quickstart
ms.date: 09/25/2020
ms.custom: contperf-fy21q1, devx-track-azurecli
ms.openlocfilehash: c6fe1fc246d112218b492072155175b2db99c8c9
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "97032949"
---
# <a name="quickstart-build-and-run-a-container-image-using-azure-container-registry-tasks"></a>Gyors útmutató: tároló-rendszerkép létrehozása és futtatása Azure Container Registry feladatokkal

Ebben a rövid útmutatóban [Azure Container Registry feladatok][container-registry-tasks-overview] parancsokkal hozhat létre, küldhet le és futtathat egy Docker-tároló rendszerképet natív módon az Azure-ban, helyi Docker-telepítés nélkül. Az ACR-feladatok a Azure Container Registryon belüli szolgáltatások egyik csomagja, amely segít a tároló-rendszerképek felügyeletében és módosításához a tárolók életciklusa során. Ez a példa azt mutatja be, hogyan lehet kiszervezni a "belső hurkos" tároló-rendszerkép fejlesztési ciklusát a felhőbe igény szerinti buildekkel egy helyi Docker használatával. 

Ebből a rövid [útmutatóból](container-registry-tutorial-quick-task.md)MEGISMERHETI az ACR-feladatok fejlettebb funkcióit az oktatóanyagok használatával. Az ACR-feladatok a programkódok véglegesítve vagy az alapképek frissítései alapján automatizálják a képbuildeket, vagy több tárolót tesztelnek párhuzamosan más forgatókönyvek között. 

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]
    
- Ehhez a rövid útmutatóhoz az Azure CLI 2.0.58 vagy újabb verziójára van szükség. Azure Cloud Shell használata esetén a legújabb verzió már telepítve van.

## <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása

Ha még nem rendelkezik tároló-beállításjegyzékkel, először hozzon létre egy erőforráscsoportot az az [Group Create][az-group-create] paranccsal. Az Azure-erőforráscsoport olyan logikai tároló, amelybe a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat.

A következő példában létrehozunk egy *myResourceGroup* nevű erőforráscsoportot az *eastus* helyen.

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

## <a name="create-a-container-registry"></a>Tárolóregisztrációs adatbázis létrehozása

Hozzon létre egy tároló-beállításjegyzéket az az [ACR Create][az-acr-create] paranccsal. A tárolóregisztrációs adatbázis nevének egyedinek kell lennie az Azure-ban, és 5–50 alfanumerikus karaktert kell tartalmaznia. A következő példában a rendszer a *myContainerRegistry008* használja. Ezt cserélje le egy egyedi értékre.

```azurecli-interactive
az acr create --resource-group myResourceGroup \
  --name myContainerRegistry008 --sku Basic
```

Ez a példa egy *alapszintű* beállításjegyzéket hoz létre, amely egy költséghatékony megoldás a fejlesztők számára a Azure Container Registry megismeréséhez. A rendelkezésre álló szolgáltatási szintek részletes ismertetését lásd: a [Container Registry szolgáltatási szintjei][container-registry-skus].

## <a name="build-and-push-image-from-a-dockerfile"></a>Rendszerkép létrehozása és leküldése egy Docker

Most a Azure Container Registry használatával hozzon létre és küldjön le egy rendszerképet. Először hozzon létre egy helyi munkakönyvtárat, majd hozzon létre egy *Docker* nevű Docker az egy sorral: `FROM mcr.microsoft.com/hello-world` . Ez egy egyszerű példa arra, hogy Linux-tárolót hozzon létre a `hello-world` Microsoft Container Registryban üzemeltetett rendszerképből. Létrehozhat saját standard szintű Docker, és képeket készíthet más platformokhoz is. Ha bash-rendszerhéjon dolgozik, hozza létre a Docker a következő paranccsal:

```bash
echo FROM mcr.microsoft.com/hello-world > Dockerfile
```

Futtassa az az [ACR Build][az-acr-build] parancsot, amely létrehozza a rendszerképet, és a rendszerkép sikeres létrehozása után leküldi a beállításjegyzékbe. A következő példa létrehozza és leküldi a `sample/hello-world:v1` rendszerképet. A `.` parancs végén adja meg a Docker helyét, ebben az esetben az aktuális könyvtárat.

```azurecli-interactive
az acr build --image sample/hello-world:v1 \
  --registry myContainerRegistry008 \
  --file Dockerfile . 
```

A sikeres buildek és leküldések kimenete a következőhöz hasonló:

```console
Packing source code into tar to upload...
Uploading archived source code from '/tmp/build_archive_b0bc1e5d361b44f0833xxxx41b78c24e.tar.gz'...
Sending context (1.856 KiB) to registry: mycontainerregistry008...
Queued a build with ID: ca8
Waiting for agent...
2019/03/18 21:56:57 Using acb_vol_4c7ffa31-c862-4be3-xxxx-ab8e615c55c4 as the home volume
2019/03/18 21:56:57 Setting up Docker configuration...
2019/03/18 21:56:58 Successfully set up Docker configuration
2019/03/18 21:56:58 Logging in to registry: mycontainerregistry008.azurecr.io
2019/03/18 21:56:59 Successfully logged into mycontainerregistry008.azurecr.io
2019/03/18 21:56:59 Executing step ID: build. Working directory: '', Network: ''
2019/03/18 21:56:59 Obtaining source code and scanning for dependencies...
2019/03/18 21:57:00 Successfully obtained source code and scanned for dependencies
2019/03/18 21:57:00 Launching container with name: build
Sending build context to Docker daemon  13.82kB
Step 1/1 : FROM mcr.microsoft.com/hello-world
latest: Pulling from hello-world
Digest: sha256:2557e3c07ed1e38f26e389462d03ed943586fxxxx21577a99efb77324b0fe535
Successfully built fce289e99eb9
Successfully tagged mycontainerregistry008.azurecr.io/sample/hello-world:v1
2019/03/18 21:57:01 Successfully executed container: build
2019/03/18 21:57:01 Executing step ID: push. Working directory: '', Network: ''
2019/03/18 21:57:01 Pushing image: mycontainerregistry008.azurecr.io/sample/hello-world:v1, attempt 1
The push refers to repository [mycontainerregistry008.azurecr.io/sample/hello-world]
af0b15c8625b: Preparing
af0b15c8625b: Layer already exists
v1: digest: sha256:92c7f9c92844bbbb5d0a101b22f7c2a7949e40f8ea90c8b3bc396879d95e899a size: 524
2019/03/18 21:57:03 Successfully pushed image: mycontainerregistry008.azurecr.io/sample/hello-world:v1
2019/03/18 21:57:03 Step ID: build marked as successful (elapsed time in seconds: 2.543040)
2019/03/18 21:57:03 Populating digests for step ID: build...
2019/03/18 21:57:05 Successfully populated digests for step ID: build
2019/03/18 21:57:05 Step ID: push marked as successful (elapsed time in seconds: 1.473581)
2019/03/18 21:57:05 The following dependencies were found:
2019/03/18 21:57:05
- image:
    registry: mycontainerregistry008.azurecr.io
    repository: sample/hello-world
    tag: v1
    digest: sha256:92c7f9c92844bbbb5d0a101b22f7c2a7949e40f8ea90c8b3bc396879d95e899a
  runtime-dependency:
    registry: registry.hub.docker.com
    repository: library/hello-world
    tag: v1
    digest: sha256:2557e3c07ed1e38f26e389462d03ed943586f744621577a99efb77324b0fe535
  git: {}

Run ID: ca8 was successful after 10s
```

## <a name="run-the-image"></a>A rendszerkép futtatása

Most gyorsan futtathatja a létrehozott és a beállításjegyzékbe leküldett képet. Itt az [az ACR Run][az-acr-run] parancsot használja a Container parancs futtatásához. A tároló-fejlesztési munkafolyamatban ez lehet egy érvényesítési lépés a lemezkép telepítése előtt, vagy a parancsot egy [többlépéses YAML-fájlba][container-registry-tasks-multi-step]is felveheti. 

A következő példa a `$Registry` parancsot használja arra, hogy megadja a beállításjegyzéket, amelyen a parancsot futtatja:

```azurecli-interactive
az acr run --registry myContainerRegistry008 \
  --cmd '$Registry/sample/hello-world:v1' /dev/null
```

Az `cmd` ebben a példában szereplő paraméter az alapértelmezett konfigurációban futtatja a tárolót, de `cmd` támogatja a további `docker run` paramétereket, vagy akár más `docker` parancsokat is.

A kimenet a következőkhöz hasonló:

```console
Packing source code into tar to upload...
Uploading archived source code from '/tmp/run_archive_ebf74da7fcb04683867b129e2ccad5e1.tar.gz'...
Sending context (1.855 KiB) to registry: mycontainerre...
Queued a run with ID: cab
Waiting for an agent...
2019/03/19 19:01:53 Using acb_vol_60e9a538-b466-475f-9565-80c5b93eaa15 as the home volume
2019/03/19 19:01:53 Creating Docker network: acb_default_network, driver: 'bridge'
2019/03/19 19:01:53 Successfully set up Docker network: acb_default_network
2019/03/19 19:01:53 Setting up Docker configuration...
2019/03/19 19:01:54 Successfully set up Docker configuration
2019/03/19 19:01:54 Logging in to registry: mycontainerregistry008.azurecr.io
2019/03/19 19:01:55 Successfully logged into mycontainerregistry008.azurecr.io
2019/03/19 19:01:55 Executing step ID: acb_step_0. Working directory: '', Network: 'acb_default_network'
2019/03/19 19:01:55 Launching container with name: acb_step_0

Hello from Docker!
This message shows that your installation appears to be working correctly.

To generate this message, Docker took the following steps:
 1. The Docker client contacted the Docker daemon.
 2. The Docker daemon pulled the "hello-world" image from the Docker Hub.
    (amd64)
 3. The Docker daemon created a new container from that image which runs the
    executable that produces the output you are currently reading.
 4. The Docker daemon streamed that output to the Docker client, which sent it
    to your terminal.

To try something more ambitious, you can run an Ubuntu container with:
 $ docker run -it ubuntu bash

Share images, automate workflows, and more with a free Docker ID:
 https://hub.docker.com/

For more examples and ideas, visit:
 https://docs.docker.com/get-started/

2019/03/19 19:01:56 Successfully executed container: acb_step_0
2019/03/19 19:01:56 Step ID: acb_step_0 marked as successful (elapsed time in seconds: 0.843801)

Run ID: cab was successful after 6s
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rá szükség, az az [Group delete][az-group-delete] paranccsal eltávolítható az erőforráscsoport, a tároló-beállításjegyzék és az ott tárolt tároló-lemezképek.

```azurecli
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban az ACR-feladatok funkcióit használta a Docker-tárolói rendszerképek gyors létrehozására, leküldésére és futtatására az Azure-ban, helyi Docker-telepítés nélkül. Folytassa a Azure Container Registry feladatok oktatóanyagával, amelyből megtudhatja, hogyan használhatja az ACR-feladatokat a rendszerkép-buildek és a frissítések automatizálására.

> [!div class="nextstepaction"]
> [Azure Container Registry feladatok – oktatóanyagok][container-registry-tutorial-quick-task]

<!-- LINKS - external -->
[docker-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-mac]: https://docs.docker.com/docker-for-mac/
[docker-push]: https://docs.docker.com/engine/reference/commandline/push/
[docker-pull]: https://docs.docker.com/engine/reference/commandline/pull/
[docker-rmi]: https://docs.docker.com/engine/reference/commandline/rmi/
[docker-run]: https://docs.docker.com/engine/reference/commandline/run/
[docker-tag]: https://docs.docker.com/engine/reference/commandline/tag/
[docker-windows]: https://docs.docker.com/docker-for-windows/
[azure-account]: https://azure.microsoft.com/free/

<!-- LINKS - internal -->
[az-acr-create]: /cli/azure/acr#az-acr-create
[az-acr-build]: /cli/azure/acr#az-acr-build
[az-acr-run]: /cli/azure/acr#az-acr-run
[az-group-create]: /cli/azure/group#az-group-create
[az-group-delete]: /cli/azure/group#az-group-delete
[azure-cli]: /cli/azure/install-azure-cli
[container-registry-tasks-overview]: container-registry-tasks-overview.md
[container-registry-tasks-multi-step]: container-registry-tasks-multi-step.md
[container-registry-tutorial-quick-task]: container-registry-tutorial-quick-task.md
[container-registry-skus]: container-registry-skus.md
[azure-cli-install]: /cli/azure/install-azure-cli
