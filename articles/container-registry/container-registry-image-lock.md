---
title: Rendszerképek zárolása
description: Adja meg a tárolók rendszerképének vagy tárházának attribútumait, hogy ne lehessen törölni vagy felülírni az Azure Container registryben.
ms.topic: article
ms.date: 09/30/2019
ms.openlocfilehash: da84767523bb6d948b71b1c1ad2ddaffb628354a
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "77659696"
---
# <a name="lock-a-container-image-in-an-azure-container-registry"></a>Tároló rendszerképének zárolása egy Azure Container registryben

Az Azure Container registryben zárolhatja a rendszerkép verzióját vagy a tárházat, így nem törölhető és nem frissíthető. Egy rendszerkép vagy egy tárház zárolásához frissítse az attribútumait az Azure CLI-parancs az [ACR repository Update][az-acr-repository-update]paranccsal. 

Ehhez a cikkhez az Azure CLI-t Azure Cloud Shell vagy helyileg kell futtatni (2.0.55 vagy újabb verzió ajánlott). A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése][azure-cli].

> [!IMPORTANT]
> Ez a cikk nem vonatkozik a teljes beállításjegyzék zárolására, például a **beállítások > zárolások** használata a Azure Portalban vagy `az lock` Az Azure CLI parancsai. A beállításjegyzék-erőforrások zárolása nem akadályozza meg a tárházban lévő adatok létrehozását, frissítését és törlését. A beállításjegyzék zárolása csak olyan felügyeleti műveletekre vonatkozik, mint például a replikálások hozzáadása vagy törlése, vagy magát a beállításjegyzéket kell törölni. További információ a [zárolási erőforrásokról a váratlan változások megelőzése](../azure-resource-manager/management/lock-resources.md)érdekében.

## <a name="scenarios"></a>Forgatókönyvek

Alapértelmezés szerint a Azure Container Registryban lévő címkézett képek *változhatnak*, ezért a megfelelő engedélyekkel többször is frissítheti és leküldheti a rendszerképet ugyanazzal a címkével egy beállításjegyzékbe. A tároló lemezképeit szükség szerint is [törölheti](container-registry-delete.md) . Ez a viselkedés akkor hasznos, ha képeket fejleszt, és a beállításjegyzéknek meg kell őriznie a méretet.

Ha azonban éles környezetben helyez üzembe egy tároló-lemezképet, előfordulhat *, hogy egy* nem módosítható tároló-lemezképre van szüksége. Egy nem módosítható rendszerkép az egyik, hogy véletlenül nem lehet törölni vagy felülírni.

A beállításjegyzékben található rendszerképek [címkézéséhez](container-registry-image-tag-version.md) és a lemezképek verziószámozásához szükséges stratégiákat itt tekintheti meg.

A tárház attribútumainak beállításához használja az az [ACR adattár Update][az-acr-repository-update] parancsot, így a következőket teheti:

* Rendszerkép verziójának vagy teljes tárházának zárolása

* Rendszerkép-verzió vagy-tárház védetté tétele törlésből, de frissítések engedélyezése

* Olvasási (lekéréses) műveletek megakadályozása egy rendszerkép vagy egy teljes tárház esetében

Példákat a következő részekben talál. 

## <a name="lock-an-image-or-repository"></a>Rendszerkép vagy adattár zárolása 

### <a name="show-the-current-repository-attributes"></a>Az aktuális tárház attribútumainak megjelenítése
A tárház aktuális attribútumainak megtekintéséhez futtassa a következőt az [ACR adattár show][az-acr-repository-show] paranccsal:

```azurecli
az acr repository show \
    --name myregistry --repository myrepo \
    --output jsonc
```

### <a name="show-the-current-image-attributes"></a>Az aktuális képattribútum megjelenítése
A címkék aktuális attribútumainak megtekintéséhez futtassa a következőt az [ACR adattár show][az-acr-repository-show] paranccsal:

```azurecli
az acr repository show \
    --name myregistry --image image:tag \
    --output jsonc
```

### <a name="lock-an-image-by-tag"></a>Rendszerkép zárolása címke alapján

A *myrepo/MyImage: címke* rendszerképének zárolásához a *myregistry*-ben futtassa a következőt az [ACR adattár Update][az-acr-repository-update] paranccsal:

```azurecli
az acr repository update \
    --name myregistry --image myrepo/myimage:tag \
    --write-enabled false
```

### <a name="lock-an-image-by-manifest-digest"></a>Rendszerkép zárolása manifest Digest használatával

A manifest Digest által azonosított *myrepo-vagy myimage* 256-rendszerkép zárolásához `sha256:...` futtassa a következő parancsot: (Egy vagy több képcímkéhez társított jegyzékfájl megkereséséhez futtassa az az [ACR repository show-Manifests][az-acr-repository-show-manifests] parancsot.)

```azurecli
az acr repository update \
    --name myregistry --image myrepo/myimage@sha256:123456abcdefg \
    --write-enabled false
```

### <a name="lock-a-repository"></a>Adattár zárolása

A *myrepo/MyImage* adattár és a benne található összes rendszerkép zárolásához futtassa a következő parancsot:

```azurecli
az acr repository update \
    --name myregistry --repository myrepo/myimage \
    --write-enabled false
```

## <a name="protect-an-image-or-repository-from-deletion"></a>Rendszerkép vagy adattár törlése a törlésből

### <a name="protect-an-image-from-deletion"></a>Rendszerkép elleni védelem törlése

A következő parancs futtatásával engedélyezheti a *myrepo/MyImage: a címke* rendszerképének frissítését, de nem törölheti:

```azurecli
az acr repository update \
    --name myregistry --image myrepo/myimage:tag \
    --delete-enabled false --write-enabled true
```

### <a name="protect-a-repository-from-deletion"></a>Adattár elleni védelem törlése

A következő parancs beállítja a *myrepo/MyImage* tárházat, így nem törölhető. Az egyes lemezképek továbbra is frissíthetők vagy törölhetők.

```azurecli
az acr repository update \
    --name myregistry --repository myrepo/myimage \
    --delete-enabled false --write-enabled true
```

## <a name="prevent-read-operations-on-an-image-or-repository"></a>Olvasási műveletek megakadályozása egy képen vagy adattáron

A következő parancs futtatásával megakadályozhatja az olvasási (lekéréses) műveleteket a *myrepo/MyImage: tag* rendszerképén:

```azurecli
az acr repository update \
    --name myregistry --image myrepo/myimage:tag \
    --read-enabled false
```

A következő parancs futtatásával megakadályozhatja, hogy az olvasási műveletek a *myrepo/MyImage* adattár összes lemezképén fussanak:

```azurecli
az acr repository update \
    --name myregistry --repository myrepo/myimage \
    --read-enabled false
```

## <a name="unlock-an-image-or-repository"></a>Rendszerkép vagy adattár zárolásának feloldása

A következő parancs futtatásával állíthatja vissza a *myrepo/MyImage: címke* rendszerképének alapértelmezett viselkedését, hogy törölhető és frissítve legyen:

```azurecli
az acr repository update \
    --name myregistry --image myrepo/myimage:tag \
    --delete-enabled true --write-enabled true
```

A *myrepo/MyImage* adattár és az összes rendszerkép alapértelmezett viselkedésének visszaállításához, hogy azok törölhetők és frissíthetők legyenek, futtassa a következő parancsot:

```azurecli
az acr repository update \
    --name myregistry --repository myrepo/myimage \
    --delete-enabled true --write-enabled true
```

## <a name="next-steps"></a>Következő lépések

Ebből a cikkből megtudhatta, hogyan használhatja az az [ACR adattár Update][az-acr-repository-update] parancsot az adattárban található képverziók törlésének vagy frissítésének megakadályozására. További attribútumok beállításához tekintse meg az az [ACR adattár Update][az-acr-repository-update] Command Reference című témakört.

A rendszerkép-verzióhoz vagy-tárházhoz beállított attribútumok megtekintéséhez használja az az [ACR repository show][az-acr-repository-show] parancsot.

A törlési műveletekkel kapcsolatos részletekért lásd: [tárolók rendszerképének törlése Azure Container Registry][container-registry-delete].

<!-- LINKS - Internal -->
[az-acr-repository-update]: /cli/azure/acr/repository#az-acr-repository-update
[az-acr-repository-show]: /cli/azure/acr/repository#az-acr-repository-show
[az-acr-repository-show-manifests]: /cli/azure/acr/repository#az-acr-repository-show-manifests
[azure-cli]: /cli/azure/install-azure-cli
[container-registry-delete]: container-registry-delete.md

