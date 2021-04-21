---
title: Újraindítási szabályzat egyszer futtatott feladatokhoz
description: Megtudhatja, hogyan használhatja a Azure Container Instances olyan tevékenységek végrehajtásához, amelyek befejezésig futnak, például buildelési, tesztelési vagy képremegjelenítési feladatokban.
ms.topic: article
ms.date: 08/11/2020
ms.openlocfilehash: 3bce208e3663ecfcebe520be92de3ac4443c0c8f
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107771154"
---
# <a name="run-containerized-tasks-with-restart-policies"></a>Tárolóalapú feladatok futtatása újraindítási szabályzatokkal

A tárolók Azure Container Instances szolgáltatásban való üzembe helyezésének egyszerűsége és gyorsasága kényelmes platformot biztosít, amellyel egy tárolópéldányban hajthat végre olyan egyszer futó feladatokat, mint a létrehozás, a tesztelés és a képrenderelés.

A konfigurálható újraindítási szabályzat segítségével megadhatja, hogy a tárolók leálljanak, amikor a folyamataik befejeződtek. Mivel a tárolópéldányok számlázása másodpercalapú, csak azokért a számítási erőforrásokért kell díjat fizetnie, amelyek használatban voltak, amikor a feladatot végrehajtó tároló futott.

A cikkben bemutatott példák az Azure CLI-t használják. Az Azure CLI 2.0.21-es vagy újabb verziójának helyileg telepítve kell [lennie,][azure-cli-install]vagy a cli-t kell használnia a [Azure Cloud Shell.](../cloud-shell/overview.md)

## <a name="container-restart-policy"></a>Tároló-újraindítási szabályzat

Amikor [tárolócsoportot](container-instances-container-groups.md) hoz létre a Azure Container Instances, három újraindítási szabályzatbeállítás közül egyet is megadhat.

| Újraindítási szabályzat   | Leírás |
| ---------------- | :---------- |
| `Always` | A tárolócsoportban lévő tárolókat a rendszer mindig újraindítja. Ez az **alapértelmezés szerint** érvényben lévő beállítás, ha nem ad meg újraindítási szabályzatot a tároló létrehozásakor. |
| `Never` | A tárolócsoportban lévő tárolókat a rendszer sosem indítja újra. A tárolók legfeljebb egyszer futnak. |
| `OnFailure` | A tárolócsoportban lévő tárolókat a rendszer csak akkor indítja újra, ha a tárolóban végrehajtott folyamat meghiúsult (azaz nullától eltérő kilépési kóddal zárul). A tárolók legalább egyszer futnak. |

[!INCLUDE [container-instances-restart-ip](../../includes/container-instances-restart-ip.md)]

## <a name="specify-a-restart-policy"></a>Újraindítási szabályzat megadása

Az újraindítási szabályzat megadásának módja attól függ, hogyan hozza létre a tárolópéldányokat, például az Azure CLI-ben, Azure PowerShell parancsmagokkal vagy a Azure Portal. Az Azure CLI-ban adja meg a `--restart-policy` paramétert az [az container create hívatásakor.][az-container-create]

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name mycontainer \
    --image mycontainerimage \
    --restart-policy OnFailure
```

## <a name="run-to-completion-example"></a>Futtatás befejezésig példa

Az újraindítási szabályzatot úgy láthatja, ha létrehoz egy tárolópéldányt a Microsoft [aci-wordcount rendszerképből,][aci-wordcount-image] és megadja az `OnFailure` újraindítási szabályzatot. Ez a példatároló egy Python-szkriptet futtat, amely alapértelmezés szerint elemzi a [Hamlet](http://shakespeare.mit.edu/hamlet/full.html)szövegét, kiírja a 10 leggyakoribb szót az STDOUT-be, majd kilép.

Futtassa a példatárolót a következő [az container create paranccsal:][az-container-create]

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name mycontainer \
    --image mcr.microsoft.com/azuredocs/aci-wordcount:latest \
    --restart-policy OnFailure
```

Az Azure Container Instances elindítja a tárolót, majd leállítja, amikor az alkalmazás (vagy ebben az esetben a szkript) kilép. Amikor Azure Container Instances le egy tárolót, amelynek újraindítási szabályzata vagy , a tároló állapota `Never` `OnFailure` **Leállítva állapotú.** A tároló állapotát az az container show paranccsal [ellenőrizheti:][az-container-show]

```azurecli-interactive
az container show \
    --resource-group myResourceGroup \
    --name mycontainer \
    --query containers[0].instanceView.currentState.state
```

Példa a kimenetre:

```bash
"Terminated"
```

Amint a példatároló *Terminated* (Leállított) állapotra vált, a feladat kimenetét a tárolónaplókban ellenőrizheti. Futtassa [az az container logs][az-container-logs] parancsot a szkript kimenetének megtekintéséhez:

```azurecli-interactive
az container logs --resource-group myResourceGroup --name mycontainer
```

Kimenet:

```bash
[('the', 990),
 ('and', 702),
 ('of', 628),
 ('to', 610),
 ('I', 544),
 ('you', 495),
 ('a', 453),
 ('my', 441),
 ('in', 399),
 ('HAMLET', 386)]
```

Ez a példa az STDOUT-nak küldött kimenetet mutatja be. Előfordulhat azonban, hogy a tárolóba írt feladatok a kimenetüket állandó tárolóba írják a későbbi lekéréshez. Például egy [Azure-fájlmegosztásra.](./container-instances-volume-azure-files.md)

## <a name="next-steps"></a>Következő lépések

A feladatalapú forgatókönyvek, például egy nagy méretű adatkészlet több tárolóval történő [](container-instances-environment-variables.md) kötegelt feldolgozása [](container-instances-start-command.md) futásidőben is kihasználhatja az egyéni környezeti változók vagy parancssorok előnyeit.

A teljes futtatásig futó tárolók kimenetének megőrzésével kapcsolatos részletekért lásd: Azure-fájlmegosztás csatlakoztatása a [Azure Container Instances.](./container-instances-volume-azure-files.md)

<!-- LINKS - External -->
[aci-wordcount-image]: https://hub.docker.com/_/microsoft-azuredocs-aci-wordcount

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container#az_container_create
[az-container-logs]: /cli/azure/container#az_container_logs
[az-container-show]: /cli/azure/container#az_container_show
[azure-cli-install]: /cli/azure/install-azure-cli
