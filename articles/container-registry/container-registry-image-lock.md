---
title: Rendszerképek zárolása
description: Állítson be attribútumokat egy tároló-rendszerképhez vagy -adattárhoz, hogy az ne törölhető vagy írható felül egy Azure-beli tároló-beállításjegyzékben.
ms.topic: article
ms.date: 09/30/2019
ms.openlocfilehash: 340beb1bb6666ddf0de7de38adee6be71f5f52bd
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107772342"
---
# <a name="lock-a-container-image-in-an-azure-container-registry"></a>Tároló rendszerképének zárolása egy Azure Container Registryben

Az Azure Container Registryben zárolhat egy rendszerképverziót vagy adattárat, hogy az ne törölhető vagy frissíthető legyen. Egy rendszerkép vagy adattár zárolása esetén frissítse az attribútumokat az [az acr repository update Azure CLI-paranccsal.][az-acr-repository-update] 

Ehhez a cikkhez az Azure CLI-t Azure Cloud Shell helyileg kell futtatni (2.0.55-ös vagy újabb verzió ajánlott). A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése][azure-cli].

> [!IMPORTANT]
> Ez a cikk nem vonatkozik a teljes regisztrációs adatbázis zárolására, például a Beállítások > **Zárolások** az Azure Portal vagy az `az lock` Azure CLI parancsai használatával. A beállításjegyzék-erőforrások zárolása nem akadályozza meg az adatok adattárakban való létrehozását, frissítését vagy törlését. A beállításjegyzék zárolása csak olyan felügyeleti műveleteket érint, mint a replikációk hozzáadása vagy törlése, vagy maga a beállításjegyzék törlése. További információ: [Erőforrások zárolása a váratlan módosítások megelőzése érdekében.](../azure-resource-manager/management/lock-resources.md)

## <a name="scenarios"></a>Forgatókönyvek

Alapértelmezés szerint a címkézett rendszerkép a *Azure Container Registry,* így a megfelelő engedélyekkel ismételten frissíthet és leküldhet egy azonos címkével rendelkező rendszerképet egy beállításjegyzékbe. A tároló rendszerképeket [szükség szerint is](container-registry-delete.md) törölni lehet. Ez a viselkedés akkor hasznos, ha rendszerképeket fejleszt, és meg kell tartania a regisztrációs adatbázis méretét.

Amikor azonban éles környezetben helyez üzembe egy tároló rendszerképét, előfordulhat, hogy nem módosítható *tároló rendszerképre* lesz szüksége. A nem módosítható rendszerképet nem lehet véletlenül törölni vagy felülírni.

A [tároló-rendszerképek címkézésére](container-registry-image-tag-version.md) és verziószámozásra vonatkozó javaslatok a regisztrációs adatbázisban található rendszerképek címkézésére és verziószámozásra vonatkozó stratégiákat tartalmaznak.

Az [az acr repository update paranccsal][az-acr-repository-update] állítsa be az adattárattribútumokat, hogy:

* Rendszerképverzió vagy egy teljes adattár zárolása

* Rendszerképverzió vagy -adattár törlése elleni védelem, de a frissítések engedélyezése

* Olvasási (le pull-) műveletek megakadályozása egy rendszerképverzión vagy egy teljes adattáron

Példákat az alábbi szakaszokban talál. 

## <a name="lock-an-image-or-repository"></a>Kép vagy adattár zárolása 

### <a name="show-the-current-repository-attributes"></a>Az aktuális adattárattribútumok megjelenítése
Az adattárak aktuális attribútumainak az alábbi [az acr repository show paranccsal][az-acr-repository-show] futtathatók:

```azurecli
az acr repository show \
    --name myregistry --repository myrepo \
    --output jsonc
```

### <a name="show-the-current-image-attributes"></a>Az aktuális képattribútumok megjelenítése
Egy címke aktuális attribútumainak az futtatásával tekintse meg a következő [az acr repository show][az-acr-repository-show] parancsot:

```azurecli
az acr repository show \
    --name myregistry --image image:tag \
    --output jsonc
```

### <a name="lock-an-image-by-tag"></a>Kép zárolása címke alapján

A *myrepo/myimage:tag* rendszerkép zároláshoz futtassa a *következő* [az acr repository update][az-acr-repository-update] parancsot:

```azurecli
az acr repository update \
    --name myregistry --image myrepo/myimage:tag \
    --write-enabled false
```

### <a name="lock-an-image-by-manifest-digest"></a>Kép zárolása jegyzékfájlkivonat alapján

A jegyzékkivonat által azonosított *myrepo/myimage* rendszerkép (SHA-256 kivonat, ként stb.) zároláshoz futtassa a `sha256:...` következő parancsot. (Egy vagy több képcímkéhez társított jegyzékkivonat megkeresése az [az acr repository show-manifests][az-acr-repository-show-manifests] paranccsal.)

```azurecli
az acr repository update \
    --name myregistry --image myrepo/myimage@sha256:123456abcdefg \
    --write-enabled false
```

### <a name="lock-a-repository"></a>Adattár zárolása

A *myrepo/myimage* adattár és a benne látható összes rendszerkép zároláshoz futtassa a következő parancsot:

```azurecli
az acr repository update \
    --name myregistry --repository myrepo/myimage \
    --write-enabled false
```

## <a name="protect-an-image-or-repository-from-deletion"></a>Rendszerkép vagy adattár védelme a törlés ellen

### <a name="protect-an-image-from-deletion"></a>Rendszerkép védelme a törlés ellen

A *myrepo/myimage:tag* rendszerkép frissítésének, de törlésnek a engedélyezése érdekében futtassa a következő parancsot:

```azurecli
az acr repository update \
    --name myregistry --image myrepo/myimage:tag \
    --delete-enabled false --write-enabled true
```

### <a name="protect-a-repository-from-deletion"></a>Adattár törlése elleni védelem

A következő parancs úgy állítja be a *myrepo/myimage* adattárat, hogy az ne törölhető legyen. Az egyes rendszerképek továbbra is frissíthetők vagy törölhetők.

```azurecli
az acr repository update \
    --name myregistry --repository myrepo/myimage \
    --delete-enabled false --write-enabled true
```

## <a name="prevent-read-operations-on-an-image-or-repository"></a>Képeken vagy adattárakon való olvasási műveletek megakadályozása

Ha meg szeretné akadályozni a *myrepo/myimage:tag* rendszerkép olvasási (le pull-) műveleteit, futtassa a következő parancsot:

```azurecli
az acr repository update \
    --name myregistry --image myrepo/myimage:tag \
    --read-enabled false
```

A *myrepo/myimage* adattárban az összes rendszerkép olvasási műveleteinek megakadályozásához futtassa a következő parancsot:

```azurecli
az acr repository update \
    --name myregistry --repository myrepo/myimage \
    --read-enabled false
```

## <a name="unlock-an-image-or-repository"></a>Kép vagy adattár zárolásának feloldása

A *myrepo/myimage:tag* rendszerkép alapértelmezett viselkedésének a törléshez és frissítéshez való visszaállításához futtassa a következő parancsot:

```azurecli
az acr repository update \
    --name myregistry --image myrepo/myimage:tag \
    --delete-enabled true --write-enabled true
```

A *myrepo/myimage* adattár és az összes rendszerkép alapértelmezett viselkedésének visszaállításához, hogy azok törölhetők és frissíthetők, futtassa a következő parancsot:

```azurecli
az acr repository update \
    --name myregistry --repository myrepo/myimage \
    --delete-enabled true --write-enabled true
```

## <a name="next-steps"></a>Következő lépések

Ebben a cikkben megtanulta, hogyan lehet az [az acr repository update][az-acr-repository-update] parancsot használni a rendszerképverziók adattárban való törlésének vagy frissítésének megakadályozására. További attribútumok beállítását az [az acr repository update parancsreferenciában][az-acr-repository-update] láthatja.

Egy rendszerképverzióhoz vagy adattárhoz beállított attribútumokat az [az acr repository show paranccsal láthatja.][az-acr-repository-show]

A törlési műveletekkel kapcsolatos részletekért lásd: [Tároló rendszerképének törlése a Azure Container Registry.][container-registry-delete]

<!-- LINKS - Internal -->
[az-acr-repository-update]: /cli/azure/acr/repository#az_acr_repository_update
[az-acr-repository-show]: /cli/azure/acr/repository#az_acr_repository_show
[az-acr-repository-show-manifests]: /cli/azure/acr/repository#az_acr_repository_show_manifests
[azure-cli]: /cli/azure/install-azure-cli
[container-registry-delete]: container-registry-delete.md
