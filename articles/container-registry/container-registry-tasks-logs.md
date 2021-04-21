---
title: Feladatfuttassa naplók megtekintése – Feladatok
description: A felhasználók által létrehozott futtatásnaplók ACR-feladatok.
ms.topic: article
ms.date: 03/09/2020
ms.openlocfilehash: ce5f33853be2aa48bcfd1916c7f8b94b9702f38c
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107781054"
---
# <a name="view-and-manage-task-run-logs"></a>Feladatfuttassa naplók megtekintése és kezelése

Az egyes tevékenységfut [Azure Container Registry tevékenységek](container-registry-tasks-overview.md) naplókimenetet hoznak létre, amelyet megvizsgálva megállapíthatja, hogy a tevékenység lépései sikeresen lefuttak-e. 

Ez a cikk a feladatfuttassa naplók megtekintését és kezelését ismerteti.

## <a name="view-streamed-logs"></a>Streamelési naplók megtekintése

Amikor manuálisan indít el egy feladatot, a naplókimenet közvetlenül a konzolra lesz streamelve. Ha például manuálisan aktivál egy feladatot az [az acr build](/cli/azure/acr#az_acr_build), [az acr run](/cli/azure/acr#az_acr_run)vagy az az [acr task run](/cli/azure/acr/task#az_acr_task_run) paranccsal, a napló kimenete a konzolra streamelve látható. 

Az alábbi [az acr run mintaparancs](/cli/azure/acr#az_acr_run) manuálisan aktivál egy feladatot, amely egy tárolót futtat, amely ugyanerről a beállításjegyzékből van lekért tároló:

```azurecli
az acr run --registry mycontainerregistry1220 \
  --cmd '$Registry/samples/hello-world:v1' /dev/null
```

Streamelési napló:

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

Azure Container Registry minden tevékenység futtatásnaplóit tárolja. A tárolt futtatásnaplókat a következő Azure Portal. Vagy az [az acr task logs](/cli/azure/acr/task#az_acr_task_logs) paranccsal megtekintheti a kiválasztott naplót. Alapértelmezés szerint a naplók 30 napig maradnak meg.

Ha egy feladat automatikusan aktiválódik, például egy forráskódfrissítéssel, csak a  tárolt naplók elérésével lehet megtekinteni a futtatás naplóit. Az automatikus feladat-eseményindítók közé tartoznak a forráskód véglegesítései, a lekéréses kérelmek, az alapként való rendszerkép-frissítések és az időzítő eseményindítók.

Futtatásnaplók megtekintése a portálon:

1. Lépjen a tároló-beállításjegyzékhez.
1. A **Szolgáltatások között** válassza a Feladatok **futtatása**  >  **lehetőséget.**
1. Válasszon ki **egy futtatásazonosítót** a futtatás állapotának megtekintéséhez és a naplók futtatásához. Ha létrejön, a napló ugyanazt az információt tartalmazza, mint a streamelési napló.

![A feladatfuttassa bejelentkezési portál megtekintése](./media/container-registry-tasks-logs/portal-task-run-logs.png)

Ha az Azure CLI-t használva meg kell tekintenie egy naplót, futtassa [az az acr task logs (az acr task logs)](/cli/azure/acr/task#az_acr_task_logs) futtatását, és adja meg a futtatás azonosítóját, a feladat nevét vagy az összeállítási feladat által létrehozott adott rendszerképet. Ha a feladat neve meg van adva, a parancs megjeleníti a legutóbb létrehozott futtatás naplóját.

Az alábbi példa a cf4 azonosítójú futtatás naplóját *tartalmazza:*

```azurecli
az acr task logs --registry mycontainerregistry1220 \
  --run-id cf4
```

## <a name="alternative-log-storage"></a>Alternatív naplótárolás

Érdemes lehet a feladatfuttatási naplókat egy helyi fájlrendszeren tárolni, vagy alternatív archiválási megoldást használni, például az Azure Storage-et.

Hozzon létre például egy helyi *tasklogs* könyvtárat, és irányítsa [át az az acr task logs kimenetét](/cli/azure/acr/task#az_acr_task_logs) egy helyi fájlba:

```azurecli
mkdir ~/tasklogs

az acr task logs --registry mycontainerregistry1220 \
  --run-id cf4 > ~/tasklogs/cf4.log
```

A helyi naplófájlokat az Azure Storage-ba is mentheti. A fájlok tárfiókba való feltöltéséhez például használja az [Azure CLI-t,](../storage/blobs/storage-quickstart-blobs-cli.md)a [Azure Portal](../storage/blobs/storage-quickstart-blobs-portal.md), vagy más metódusokat.

## <a name="next-steps"></a>Következő lépések

* További információ a [Azure Container Registry-feladatok](container-registry-tasks-overview.md)


<!-- LINKS - Internal -->
[azure-cli]: /cli/azure/install-azure-cli
[az-acr-build]: /cli/azure/acr#az_acr_build
[az-acr-pack-build]: /cli/azure/acr/pack#az_acr_pack_build
[az-acr-task]: /cli/azure/acr/task
[az-acr-task-create]: /cli/azure/acr/task#az_acr_task_create
[az-acr-task-run]: /cli/azure/acr/task#az_acr_task_run
[az-acr-task-update]: /cli/azure/acr/task#az_acr_task_update
[az-login]: /cli/azure/reference-index#az_login
[az-login-service-principal]: /cli/azure/authenticate-azure-cli

<!-- IMAGES -->
[quick-build-01-fork]: ./media/container-registry-tutorial-quick-build/quick-build-01-fork.png
[quick-build-02-browser]: ./media/container-registry-tutorial-quick-build/quick-build-02-browser.png
