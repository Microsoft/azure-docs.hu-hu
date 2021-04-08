---
title: Feladat-futtatási naplók megtekintése – feladatok
description: Az ACR-feladatok által létrehozott futtatási naplók megtekintése és kezelése.
ms.topic: article
ms.date: 03/09/2020
ms.openlocfilehash: b2a10d4a3a2746acf38445673af994c6317c77de
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "93027176"
---
# <a name="view-and-manage-task-run-logs"></a>Feladat-futtatási naplók megtekintése és kezelése

[Azure Container Registry feladatok](container-registry-tasks-overview.md) mindegyike futtatja a napló kimenetét, amelyet megvizsgálva megállapíthatja, hogy a feladat lépései sikeresen futottak-e. 

Ez a cikk a feladat-futtatási naplók megtekintését és kezelését ismerteti.

## <a name="view-streamed-logs"></a>Továbbított naplók megtekintése

Ha a feladatot manuálisan indítja el, a rendszer a napló kimenetét közvetlenül a konzolon továbbítja. Ha például manuálisan indítja el a feladatot az az [ACR Build](/cli/azure/acr#az-acr-build), az [ACR Run](/cli/azure/acr#az-acr-run)vagy [az ACR Task Run](/cli/azure/acr/task#az-acr-task-run) parancs használatával, a napló kimenete a konzolra áramlik. 

A következő minta az [ACR Run](/cli/azure/acr#az-acr-run) paranccsal manuálisan indít el egy olyan feladatot, amely egy, a beállításjegyzékből lekért tárolót futtat:

```azurecli
az acr run --registry mycontainerregistry1220 \
  --cmd '$Registry/samples/hello-world:v1' /dev/null
```

Továbbított napló:

```console
Queued a run with ID: cf4
Waiting for an agent...
2020/03/09 20:30:10 Alias support enabled for version >= 1.1.0, please see https://aka.ms/acr/tasks/task-aliases for more information.
2020/03/09 20:30:10 Creating Docker network: acb_default_network, driver: 'bridge'
2020/03/09 20:30:10 Successfully set up Docker network: acb_default_network
2020/03/09 20:30:10 Setting up Docker configuration...
2020/03/09 20:30:11 Successfully set up Docker configuration
2020/03/09 20:30:11 Logging in to registry: mycontainerregistry1220azurecr.io
2020/03/09 20:30:12 Successfully logged into mycontainerregistry1220azurecr.io
2020/03/09 20:30:12 Executing step ID: acb_step_0. Timeout(sec): 600, Working directory: '', Network: 'acb_default_network'
2020/03/09 20:30:12 Launching container with name: acb_step_0
Unable to find image 'mycontainerregistry1220azurecr.io/samples/hello-world:v1' locally
v1: Pulling from samples/hello-world
Digest: sha256:92c7f9c92844bbbb5d0a101b22f7c2a7949e40f8ea90c8b3bc396879d95e888a
Status: Downloaded newer image for mycontainerregistry1220azurecr.io/samples/hello-world:v1

Hello from Docker!
This message shows that your installation appears to be working correctly.
[...]

2020/03/09 20:30:13 Successfully executed container: acb_step_0
2020/03/09 20:30:13 Step ID: acb_step_0 marked as successful (elapsed time in seconds: 1.180081)

Run ID: cf4 was successful after 5s
```

## <a name="view-stored-logs"></a>Tárolt naplók megtekintése 

Azure Container Registry az összes feladathoz naplókat futtat. A tárolt futtatási naplók a Azure Portalban tekinthetők meg. Vagy használja az az [ACR Task logs](/cli/azure/acr/task#az-acr-task-logs) parancsot egy kiválasztott napló megtekintéséhez. Alapértelmezés szerint a rendszer 30 napig őrzi meg a naplókat.

Ha egy feladat automatikusan aktiválódik, például a forráskód frissítése, a tárolt naplók elérésének *egyetlen* módja a futtatási naplók megtekintése. Az automatikus feladat-eseményindítók közé tartoznak a forráskód-véglegesítő vagy a lekéréses kérelmek, az alaprendszerkép frissítései és az időzítő eseményindítói.

A futtatási naplók megtekintése a portálon:

1. Navigáljon a tároló-beállításjegyzékhez.
1. A **szolgáltatások** területen válassza a **feladatok**  >  **Futtatás** lehetőséget.
1. Válassza ki a futtatási **azonosítót** a futtatási állapot megtekintéséhez és a naplók futtatásához. A napló ugyanazokat az információkat tartalmazza, mint a továbbított napló, ha van ilyen.

![A feladat futtatási bejelentkezési portáljának megtekintése](./media/container-registry-tasks-logs/portal-task-run-logs.png)

Ha az Azure CLI-vel szeretné megtekinteni a naplót, futtassa az [az ACR Task logs](/cli/azure/acr/task#az-acr-task-logs) parancsot, és adjon meg egy futtatási azonosítót, egy feladatnév vagy egy, a létrehozási feladat által létrehozott rendszerképet. Ha meg van adva a feladat neve, a parancs az utolsó létrehozott Futtatás naplóját jeleníti meg.

A következő példa a futtatási naplót a *CF4* azonosítóval adja eredményül:

```azurecli
az acr task logs --registry mycontainerregistry1220 \
  --run-id cf4
```

## <a name="alternative-log-storage"></a>Alternatív naplózási tár

Előfordulhat, hogy egy helyi fájlrendszeren szeretné tárolni a feladat futtatási naplóit, vagy alternatív archiválási megoldást, például az Azure Storage-t használja.

Hozzon létre például egy helyi *tasklogs* könyvtárat, és irányítsa át az az [ACR Task logs](/cli/azure/acr/task#az-acr-task-logs) kimenetét egy helyi fájlba:

```azurecli
mkdir ~/tasklogs

az acr task logs --registry mycontainerregistry1220 \
  --run-id cf4 > ~/tasklogs/cf4.log
```

Helyi naplófájlokat is menthet az Azure Storage szolgáltatásba. Például az [Azure CLI](../storage/blobs/storage-quickstart-blobs-cli.md), a [Azure Portal](../storage/blobs/storage-quickstart-blobs-portal.md)vagy más módszerek használatával tölthet fel fájlokat egy Storage-fiókba.

## <a name="next-steps"></a>Következő lépések

* További információ a [Azure Container Registry feladatokról](container-registry-tasks-overview.md)


<!-- LINKS - Internal -->
[azure-cli]: /cli/azure/install-azure-cli
[az-acr-build]: /cli/azure/acr#az-acr-build
[az-acr-pack-build]: /cli/azure/acr/pack#az-acr-pack-build
[az-acr-task]: /cli/azure/acr/task
[az-acr-task-create]: /cli/azure/acr/task#az-acr-task-create
[az-acr-task-run]: /cli/azure/acr/task#az-acr-task-run
[az-acr-task-update]: /cli/azure/acr/task#az-acr-task-update
[az-login]: /cli/azure/reference-index#az-login
[az-login-service-principal]: /cli/azure/authenticate-azure-cli

<!-- IMAGES -->
[quick-build-01-fork]: ./media/container-registry-tutorial-quick-build/quick-build-01-fork.png
[quick-build-02-browser]: ./media/container-registry-tutorial-quick-build/quick-build-02-browser.png
