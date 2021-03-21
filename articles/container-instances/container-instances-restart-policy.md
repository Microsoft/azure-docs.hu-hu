---
title: Újraindítási szabályzat a futtatási egyszeri feladatokhoz
description: Megtudhatja, hogyan használhatja a Azure Container Instances a befejezésre futó feladatok végrehajtásához, például a létrehozási, tesztelési vagy képrenderelési feladatokban.
ms.topic: article
ms.date: 08/11/2020
ms.openlocfilehash: 49280549fa834b82574f81494f1cf44817d8be5d
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102203827"
---
# <a name="run-containerized-tasks-with-restart-policies"></a>Tárolóalapú feladatok futtatása újraindítási szabályzatokkal

A tárolók Azure Container Instances szolgáltatásban való üzembe helyezésének egyszerűsége és gyorsasága kényelmes platformot biztosít, amellyel egy tárolópéldányban hajthat végre olyan egyszer futó feladatokat, mint a létrehozás, a tesztelés és a képrenderelés.

A konfigurálható újraindítási szabályzat segítségével megadhatja, hogy a tárolók leálljanak, amikor a folyamataik befejeződtek. Mivel a tárolópéldányok számlázása másodpercalapú, csak azokért a számítási erőforrásokért kell díjat fizetnie, amelyek használatban voltak, amikor a feladatot végrehajtó tároló futott.

A cikkben bemutatott példák az Azure CLI-t használják. Az Azure CLI verziójának 2.0.21 [vagy újabb verziójára van szükség][azure-cli-install], vagy a CLI-t kell használnia a [Azure Cloud Shell](../cloud-shell/overview.md).

## <a name="container-restart-policy"></a>Tároló újraindítási szabályzata

Amikor Azure Container Instancesban hoz létre egy [tároló csoportot](container-instances-container-groups.md) , megadhatja a három újraindítási házirend-beállítás egyikét.

| Újraindítási szabályzat   | Leírás |
| ---------------- | :---------- |
| `Always` | A tárolócsoportban lévő tárolókat a rendszer mindig újraindítja. Ez az **alapértelmezés szerint** érvényben lévő beállítás, ha nem ad meg újraindítási szabályzatot a tároló létrehozásakor. |
| `Never` | A tárolócsoportban lévő tárolókat a rendszer sosem indítja újra. A tárolók legfeljebb egyszer futnak. |
| `OnFailure` | A tárolócsoportban lévő tárolókat a rendszer csak akkor indítja újra, ha a tárolóban végrehajtott folyamat meghiúsult (azaz nullától eltérő kilépési kóddal zárul). A tárolók legalább egyszer futnak. |

[!INCLUDE [container-instances-restart-ip](../../includes/container-instances-restart-ip.md)]

## <a name="specify-a-restart-policy"></a>Újraindítási szabályzat meghatározása

Az újraindítási szabályzatok megadásának módja attól függ, hogyan hozza létre a tároló példányait, például az Azure CLI-vel, Azure PowerShell-parancsmagokkal vagy a Azure Portal. Az Azure CLI-ben határozza meg a `--restart-policy` paramétert az [az Container Create][az-container-create]parancs meghívásakor.

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name mycontainer \
    --image mycontainerimage \
    --restart-policy OnFailure
```

## <a name="run-to-completion-example"></a>Futtatás a befejezésig példa

Ha szeretné megtekinteni az újraindítási szabályzatot, hozzon létre egy Container-példányt a Microsoft [ACI-WordCount][aci-wordcount-image] rendszerképből, és határozza meg az `OnFailure` Újraindítási szabályzatot. Ez a példában szereplő tároló egy Python-szkriptet futtat, amely alapértelmezés szerint a Shakespeare [Hamlet](http://shakespeare.mit.edu/hamlet/full.html)szövegét elemzi, a 10 leggyakoribb szót írja a stdout értékre, majd kilép.

Futtassa a példában szereplő tárolót a következő az [Container Create][az-container-create] paranccsal:

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name mycontainer \
    --image mcr.microsoft.com/azuredocs/aci-wordcount:latest \
    --restart-policy OnFailure
```

Az Azure Container Instances elindítja a tárolót, majd leállítja, amikor az alkalmazás (vagy ebben az esetben a szkript) kilép. Amikor Azure Container Instances leállít egy olyan tárolót, amelynek újraindítási szabályzata `Never` vagy `OnFailure` , a tároló állapota leállítva értékre van állítva. A tároló állapotát az [az Container show][az-container-show] paranccsal tekintheti meg:

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

Amint a példatároló *Terminated* (Leállított) állapotra vált, a feladat kimenetét a tárolónaplókban ellenőrizheti. Futtassa az az [Container logs][az-container-logs] parancsot a szkript kimenetének megtekintéséhez:

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

Ez a példa azt a kimenetet mutatja, amelyet a parancsfájl az STDOUT-nak küld. A tárolóban lévő feladatok azonban Ehelyett a kimenetet állandó tárterületre írhatja a későbbi lekéréshez. Egy [Azure-fájlmegosztás](./container-instances-volume-azure-files.md)esetében például.

## <a name="next-steps"></a>Következő lépések

A feladat-alapú forgatókönyvek, például a Batch több tárolóval rendelkező nagyméretű adathalmazok feldolgozásával kihasználhatják az egyéni [környezeti változókat](container-instances-environment-variables.md) vagy a [parancssori vonalakat](container-instances-start-command.md) futásidőben.

Az [Azure-fájlmegosztás Azure Container Instancessal való csatlakoztatásával](./container-instances-volume-azure-files.md)kapcsolatos részletekért tekintse meg a (z) című témakört.

<!-- LINKS - External -->
[aci-wordcount-image]: https://hub.docker.com/_/microsoft-azuredocs-aci-wordcount

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container#az-container-create
[az-container-logs]: /cli/azure/container#az-container-logs
[az-container-show]: /cli/azure/container#az-container-show
[azure-cli-install]: /cli/azure/install-azure-cli
