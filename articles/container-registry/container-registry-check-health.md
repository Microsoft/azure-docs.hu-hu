---
title: Beállításjegyzék állapotának keresése
description: Megtudhatja, hogyan futtathat egy gyors diagnosztikai parancsot az Azure Container Registry használata során felmerülő gyakori problémák azonosításához, beleértve a helyi Docker-konfigurációt és a beállításjegyzékhez való kapcsolódást.
ms.topic: article
ms.date: 07/02/2019
ms.openlocfilehash: f27a99818260553cbd7ba26158db0064c145a21f
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "88245383"
---
# <a name="check-the-health-of-an-azure-container-registry"></a>Azure Container Registry állapotának megtekintése

Az Azure Container Registry használatakor időnként problémák merülhetnek fel. Előfordulhat például, hogy nem tudja lekérni a tároló rendszerképét, mert a helyi környezetben Docker-probléma van. Vagy a hálózati hiba miatt nem lehet csatlakozni a beállításjegyzékhez. 

Első diagnosztikai lépésként futtassa az az [ACR renézz-Health][az-acr-check-health] parancsot a környezet állapotával kapcsolatos információk lekéréséhez és a megcélzott beállításjegyzékhez való igény szerinti hozzáféréshez. Ez a parancs az Azure CLI 2.0.67 vagy újabb verziójában érhető el. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése][azure-cli].

A beállításjegyzék további hibaelhárítási útmutatóját a következő témakörben talál:
* [Beállításjegyzékbeli bejelentkezés – problémamegoldás](container-registry-troubleshoot-login.md)
* [A beállításjegyzék hálózati problémáinak elhárítása](container-registry-troubleshoot-access.md)
* [Beállításjegyzékbeli teljesítmény – problémamegoldás](container-registry-troubleshoot-performance.md)

## <a name="run-az-acr-check-health"></a>Futtatás az ACR ellenõrzés-Health

A következő példák a parancs futtatásának különböző módjait mutatják be `az acr check-health` .

> [!NOTE]
> Ha Azure Cloud Shellban futtatja a parancsot, a helyi környezet nincs bejelölve. Megtekintheti azonban a cél beállításjegyzékhez való hozzáférést.

### <a name="check-the-environment-only"></a>Csak a környezet ellenőrzését

A helyi Docker-démon, a CLI-verzió és a Helm-ügyfél konfigurációjának vizsgálatához további paraméterek nélkül futtassa a parancsot:

```azurecli
az acr check-health
```

### <a name="check-the-environment-and-a-target-registry"></a>A környezet és a cél beállításjegyzékének keresése

A beállításjegyzékhez való hozzáférés ellenőrzéséhez, valamint a helyi környezet-ellenőrzések végrehajtásához adja meg a cél-beállításjegyzék nevét. Például:

```azurecli
az acr check-health --name myregistry
```

## <a name="error-reporting"></a>Hibajelentés

A parancs adatokat naplóz a standard kimenetre. Ha a rendszer problémát észlel, hibakódot és leírást is tartalmaz. További információ a kódokról és a lehetséges megoldásokról: a [hiba leírása](container-registry-health-error-reference.md).

Alapértelmezés szerint a parancs leáll, amikor hibát talál. Futtathatja is a parancsot úgy, hogy az minden állapot-ellenőrzés kimenetét megadja, még akkor is, ha a rendszer hibákat talál. Adja hozzá a `--ignore-errors` paramétert az alábbi példákban látható módon:

```azurecli
# Check environment only
az acr check-health --ignore-errors

# Check environment and target registry
az acr check-health --name myregistry --ignore-errors
```      

Példa a kimenetre:

```console
$ az acr check-health --name myregistry --ignore-errors --yes

Docker daemon status: available
Docker version: Docker version 18.09.2, build 6247962
Docker pull of 'mcr.microsoft.com/mcr/hello-world:latest' : OK
ACR CLI version: 2.2.9
Helm version:
Client: &version.Version{SemVer:"v2.14.1", GitCommit:"5270352a09c7e8b6e8c9593002a73535276507c0", GitTreeState:"clean"}
DNS lookup to myregistry.azurecr.io at IP 40.xxx.xxx.162 : OK
Challenge endpoint https://myregistry.azurecr.io/v2/ : OK
Fetch refresh token for registry 'myregistry.azurecr.io' : OK
Fetch access token for registry 'myregistry.azurecr.io' : OK
```  



## <a name="next-steps"></a>Következő lépések

Az az ACR Return [-Health][az-acr-check-health] parancs által visszaadott hibakódokkal kapcsolatos részletekért tekintse meg az [állapot-ellenőrzési hiba referenciáját](container-registry-health-error-reference.md).

Tekintse [meg a](container-registry-faq.md) gyakori kérdések és a Azure Container Registry kapcsolatos egyéb ismert problémák gyakori kérdéseit.





<!-- LINKS - internal -->
[azure-cli]: /cli/azure/install-azure-cli
[az-acr-check-health]: /cli/azure/acr#az-acr-check-health
