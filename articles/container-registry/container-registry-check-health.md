---
title: A beállításjegyzék állapotának ellenőrzése
description: Megtudhatja, hogyan futtathat egy gyors diagnosztikai parancsot az Azure-beli tároló-beállításjegyzékek gyakori problémáinak azonosításához, beleértve a helyi Docker-konfigurációt és a beállításjegyzékhez való kapcsolódást
ms.topic: article
ms.date: 07/02/2019
ms.openlocfilehash: fec05efe67f5c502f36ee90eec57ba283b15a4a0
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107761744"
---
# <a name="check-the-health-of-an-azure-container-registry"></a>Azure Container Registry állapotának ellenőrzése

Azure-beli tároló-beállításjegyzék használata esetén időnként problémákba ütközhet. Előfordulhat például, hogy a Docker helyi környezetben való problémája miatt nem tud lekért tároló rendszerképet. Vagy egy hálózati probléma megakadályozhatja, hogy csatlakozzon a beállításjegyzékhez. 

Első diagnosztikai lépésként futtassa [az az acr check-health][az-acr-check-health] parancsot a környezet állapotáról és opcionálisan a céljegyzékhez való hozzáféréshez. Ez a parancs az Azure CLI 2.0.67-es vagy újabb verziójában érhető el. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése][azure-cli].

További hibaelhárítási útmutató a beállításjegyzékhez:
* [A beállításjegyzékbe való bejelentkezés hibaelhárítása](container-registry-troubleshoot-login.md)
* [A beállításjegyzék hálózati problémáinak elhárítása](container-registry-troubleshoot-access.md)
* [Beállításjegyzék teljesítményének hibaelhárítása](container-registry-troubleshoot-performance.md)

## <a name="run-az-acr-check-health"></a>Az az acr check-health futtatása

Az alábbi példák a parancs futtatásának különböző módjait `az acr check-health` mutatják be.

> [!NOTE]
> Ha a parancsot az Azure Cloud Shell futtatja, a helyi környezet nincs bejelölve. A céljegyzékhez való hozzáférést azonban ellenőrizheti.

### <a name="check-the-environment-only"></a>Csak a környezet ellenőrzése

A helyi Docker-démon, a parancssori felület verziója és a Helm-ügyfél konfigurációjának ellenőrzéshez futtassa a parancsot további paraméterek nélkül:

```azurecli
az acr check-health
```

### <a name="check-the-environment-and-a-target-registry"></a>A környezet és a céljegyzék ellenőrzése

A beállításjegyzékhez való hozzáférés ellenőrzéséhez és a helyi környezetek ellenőrzéséhez adja át a céljegyzék nevét. Például:

```azurecli
az acr check-health --name myregistry
```

## <a name="error-reporting"></a>Hibajelentés

A parancs a szabványos kimenetbe naplózza az adatokat. Ha a rendszer hibát észlel, hibaüzenetet és leírást ad meg. A kódokkal és a lehetséges megoldásokkal kapcsolatos további információkért tekintse meg a [hibareferenciát.](container-registry-health-error-reference.md)

Alapértelmezés szerint a parancs leáll, ha hibát talál. A parancsot úgy is futtathatja, hogy az az összes állapot-ellenőrzéshez kimenetet biztosít, még akkor is, ha hibákat talál. Adja hozzá `--ignore-errors` a paramétert az alábbi példákban látható módon:

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

Az [az acr check-health][az-acr-check-health] parancs által visszaadott hibakódokkal kapcsolatos részletekért tekintse meg az Állapotellenőrzési [hiba referenciáját.](container-registry-health-error-reference.md)

A gyakori [kérdéseket](container-registry-faq.md) és az egyéb ismert problémákat a gyakori kérdések között Azure Container Registry.





<!-- LINKS - internal -->
[azure-cli]: /cli/azure/install-azure-cli
[az-acr-check-health]: /cli/azure/acr#az_acr_check_health
