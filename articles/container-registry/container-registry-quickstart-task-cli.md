---
title: Rövid útmutató – Tároló rendszerképének igény szerinti összeállítása az Azure-ban
description: A Azure Container Registry paranccsal gyorsan, igény szerint, az Azure-felhőben építhet ki, lekulpelhet és futtathat Docker-tároló rendszerképeket.
ms.topic: quickstart
ms.date: 09/25/2020
ms.custom: contperf-fy21q1, devx-track-azurecli
ms.openlocfilehash: d3ef126812cb36874b74c8569d141787f71a29da
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107781450"
---
# <a name="quickstart-build-and-run-a-container-image-using-azure-container-registry-tasks"></a>Rövid útmutató: Tároló rendszerképének összeállítása és futtatása a Azure Container Registry-feladatok

Ebben a rövid útmutatóban Azure Container Registry-feladatok használatával gyorsan, helyi Docker-telepítés nélkül, natív módon építhet ki, leküldhet és futtathat egy Docker-tároló rendszerképét az Azure-ban. [][container-registry-tasks-overview] ACR-feladatok a tárolókban található Azure Container Registry a tárolók rendszerképének kezeléséhez és módosításához a tároló életciklusa során. Ez a példa bemutatja, hogyan lehet a "belső ciklusú" tároló rendszerkép-fejlesztési ciklusát a felhőbe kiszervezésre igény szerinti buildekkel egy helyi Docker-fájl használatával. 

A rövid útmutatót követően megismerheti a ACR-feladatok speciális funkcióit az [oktatóanyagok segítségével.](container-registry-tutorial-quick-task.md) ACR-feladatok automatizálhatja a rendszerkép-összeállításokat kód véglegesítések vagy alapként szolgáló rendszerképfrissítések alapján, vagy több tárolót tesztelhet párhuzamosan, egyéb forgatókönyvek mellett. 

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]
    
- Ehhez a rövid útmutatóhoz az Azure CLI 2.0.58-as vagy újabb verziójára lesz szükség. Ha a Azure Cloud Shell, a legújabb verzió már telepítve van.

## <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása

Ha még nem rendelkezik tároló-beállításjegyzékvel, először hozzon létre egy erőforráscsoportot [az az group create paranccsal.][az-group-create] Az Azure-erőforráscsoport olyan logikai tároló, amelybe a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat.

A következő példában létrehozunk egy *myResourceGroup* nevű erőforráscsoportot az *eastus* helyen.

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

## <a name="create-a-container-registry"></a>Tárolóregisztrációs adatbázis létrehozása

Hozzon létre egy tároló-beállításjegyzéket [az az acr create paranccsal.][az-acr-create] A tárolóregisztrációs adatbázis nevének egyedinek kell lennie az Azure-ban, és 5–50 alfanumerikus karaktert kell tartalmaznia. A következő példában *a myContainerRegistry008 lesz* használva. Ezt cserélje le egy egyedi értékre.

```azurecli-interactive
az acr create --resource-group myResourceGroup \
  --name myContainerRegistry008 --sku Basic
```

Ez a példa létrehoz egy *Alapszintű* beállításjegyzéket, amely egy költségoptimalált lehetőség a fejlesztők számára a Azure Container Registry. Az elérhető szolgáltatásszintekkel kapcsolatos részletekért lásd: [Container Registry service tiers (Tároló-beállításjegyzék szolgáltatási rétegei).][container-registry-skus]

## <a name="build-and-push-image-from-a-dockerfile"></a>Rendszerkép összeállítása és leküldése Docker-fájlból

Most használja a Azure Container Registry a lemezképek felépítéséhez és leküldéshez. Először hozzon létre egy helyi munkakönyvtárat, majd hozzon létre egy *Dockerfile nevű Dockerfile-t* a következő egyetlen ssokkal: `FROM mcr.microsoft.com/hello-world` . Ez egy egyszerű példa egy Linux-tároló rendszerképének felépítésére a `hello-world` Microsoft Container Registry. Létrehozhatja saját standard Docker-fájlját, és rendszerképeket készíthet más platformokhoz. Ha Bash-rendszerhéjban dolgozik, hozza létre a Dockerfile-t a következő paranccsal:

```bash
echo FROM mcr.microsoft.com/hello-world > Dockerfile
```

Futtassa [az az acr build][az-acr-build] parancsot, amely felépíti a rendszerképet, és a rendszerkép sikeres buildelése után leküldi azt a regisztrációs adatbázisba. Az alábbi példa felépíti és lekulpeli a `sample/hello-world:v1` rendszerképet. A parancs végén található beállítja a `.` Docker-fájl helyét, ebben az esetben az aktuális könyvtárat.

```azurecli-interactive
az acr build --image sample/hello-world:v1 \
  --registry myContainerRegistry008 \
  --file Dockerfile . 
```

A sikeres build és leküldés kimenete az alábbihoz hasonló:

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

Most gyorsan futtassa az ön által felépített és a regisztrációs adatbázisba lekért rendszerképet. Itt az [az acr run paranccsal][az-acr-run] futtatja a tárolóparancsot. A tárolófejlesztési munkafolyamatban ez lehet egy érvényesítési lépés a lemezkép üzembe helyezése előtt, vagy a parancsot egy többlépéses [YAML-fájlba is foglalhatja.][container-registry-tasks-multi-step] 

Az alábbi példa a `$Registry` használatával adja meg azt a beállításjegyzéket, ahol a parancsot futtatja:

```azurecli-interactive
az acr run --registry myContainerRegistry008 \
  --cmd '$Registry/sample/hello-world:v1' /dev/null
```

A példában található paraméter az alapértelmezett konfigurációban futtatja a tárolót, de további paramétereket vagy `cmd` `cmd` más `docker run` `docker` parancsokat is támogat.

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

Ha már nincs rá szükség, az [az group delete][az-group-delete] paranccsal eltávolíthatja az erőforráscsoportot, a tároló-beállításjegyzéket és az ott tárolt tároló rendszerképeket.

```azurecli
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban a ACR-feladatok funkcióit használta egy Docker-tároló rendszerképének natív módon, helyi Docker-telepítés nélküli, natív felépítéséhez, leküldéséhez és futtatásához az Azure-ban. Folytassa a Azure Container Registry-feladatok oktatóanyagokkal, amelyekből megtudhatja, hogyan automatizálhatja a rendszerkép-ACR-feladatok és a frissítéseket a ACR-feladatok használatával.

> [!div class="nextstepaction"]
> [Azure Container Registry-feladatok oktatóanyagok][container-registry-tutorial-quick-task]

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
[az-acr-create]: /cli/azure/acr#az_acr_create
[az-acr-build]: /cli/azure/acr#az_acr_build
[az-acr-run]: /cli/azure/acr#az_acr_run
[az-group-create]: /cli/azure/group#az_group_create
[az-group-delete]: /cli/azure/group#az_group_delete
[azure-cli]: /cli/azure/install-azure-cli
[container-registry-tasks-overview]: container-registry-tasks-overview.md
[container-registry-tasks-multi-step]: container-registry-tasks-multi-step.md
[container-registry-tutorial-quick-task]: container-registry-tutorial-quick-task.md
[container-registry-skus]: container-registry-skus.md
[azure-cli-install]: /cli/azure/install-azure-cli
