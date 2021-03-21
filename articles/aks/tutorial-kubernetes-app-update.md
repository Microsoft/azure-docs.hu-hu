---
title: Azure-on futó Kubernetes oktatóanyag – Alkalmazás frissítése
description: Ebben az Azure Kubernetes Service-hez (AKS-hez) tartozó oktatóanyagban megismerheti, hogyan frissítheti a meglévő alkalmazástelepítéseket az AKS-ben az alkalmazáskód új verziójával.
services: container-service
ms.topic: tutorial
ms.date: 01/12/2021
ms.custom: mvc
ms.openlocfilehash: b969e3ec1c670c0a12129289c8ff7eb81df51ff9
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98250655"
---
# <a name="tutorial-update-an-application-in-azure-kubernetes-service-aks"></a>Oktatóanyag: Alkalmazások frissítése az Azure Kubernetes Service-ben (AKS)

A Kubernetesben való telepítésüket követően az alkalmazások egy új tárolórendszerkép- vagy rendszerképverzió megadásával frissíthetőek. A rendszer úgy készíti el a frissítést, hogy csak a központi telepítés egy részét frissítse egyszerre. Ennek az eltolásos frissítésnek köszönhetően az alkalmazás a frissítés során is tovább fut. Ezen kívül visszaállítási mechanizmust is biztosít az üzembe helyezés során fellépő hibák esetére.

Ebben az oktatóanyagban, amely egy hétrészes sorozat hatodik része, az Azure Vote mintaalkalmazást frissítjük. Az alábbiak végrehajtásának módját ismerheti meg:

> [!div class="checklist"]
> * Az előtér-alkalmazás kódjának frissítése
> * Frissített tárolórendszerkép létrehozása
> * A tárolórendszerkép leküldése az Azure Container Registrybe
> * A frissített tárolórendszerkép üzembe helyezése

## <a name="before-you-begin"></a>Előkészületek

Az előző oktatóanyagokban egy alkalmazás egy tároló-rendszerképbe van csomagolva. A rendszerkép fel lett töltve Azure Container Registryba, és létrehozott egy AK-fürtöt. Az alkalmazás ezután üzembe lett helyezve az AK-fürtön.

Emellett klónoztunk egy alkalmazás-adattárat, amely tartalmazza az alkalmazás forráskódját, valamint a jelen oktatóanyagban használt, előre létrehozott Docker Compose-fájlt. Győződjön meg arról, hogy létrehozta a tárház klónozását, és módosította a címtárakat a klónozott könyvtárba. Ha még nem végezte el ezeket a lépéseket, és szeretné követni, kezdje az [1. oktatóanyag – tároló lemezképek létrehozása][aks-tutorial-prepare-app]című témakörben.

Ehhez az oktatóanyaghoz az Azure CLI 2.0.53 vagy újabb verzióját kell futtatnia. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése][azure-cli-install].

## <a name="update-an-application"></a>Alkalmazás frissítése

Módosítsuk a mintaalkalmazást, majd frissítsük az AKS-fürtben már üzembe helyezett verziót. Győződjön meg arról, hogy a klónozott *Azure-szavazó-app-Redis* címtárban van. A minta alkalmazás forráskódja az *Azure-vote* címtárban is megtalálható. Nyissa meg a *config_file.cfg* fájlt egy szerkesztővel (például `vi`):

```console
vi azure-vote/azure-vote/config_file.cfg
```

Módosítsa a *VOTE1VALUE* és a *VOTE2VALUE* értékeit különböző értékekre, például színekre. A következő példa a frissített értékeket jeleníti meg:

```
# UI Configurations
TITLE = 'Azure Voting App'
VOTE1VALUE = 'Blue'
VOTE2VALUE = 'Purple'
SHOWHOST = 'false'
```

Mentse és zárja be a fájlt. A `vi` alkalmazásban használja a t `:wq` .

## <a name="update-the-container-image"></a>A tárolórendszerkép frissítése

Az előtéri rendszerkép újbóli létrehozásához és a frissített alkalmazás teszteléséhez használja a [docker-compose][docker-compose] parancsot. A `--build` argumentummal lehet utasítani a Docker Compose-t, hogy hozza újra létre az alkalmazás rendszerképét:

```console
docker-compose up --build -d
```

## <a name="test-the-application-locally"></a>Az alkalmazás helyi tesztelése

Ha ellenőrizni szeretné, hogy a frissített tárolórendszerkép megjeleníti-e a módosításokat, nyissa meg a `http://localhost:8080` címet egy helyi böngészőben.

:::image type="content" source="media/container-service-kubernetes-tutorials/vote-app-updated.png" alt-text="Képernyőkép, amely egy helyi webböngészőben helyileg megnyitott Azure-beli szavazási alkalmazás frissített tároló-rendszerképére mutat példát.":::

A *config_file. cfg* fájlban megadott frissített értékek a futó alkalmazásban jelennek meg.

## <a name="tag-and-push-the-image"></a>A rendszerkép címkézése és leküldése

A frissített rendszerkép megfelelő használatához címkézze fel az *azure-vote-front* rendszerképet az ACR-beállításjegyzék bejelentkezési kiszolgálójának nevével. Szerezze be a bejelentkezési kiszolgáló nevét az az [ACR List](/cli/azure/acr) paranccsal:

```azurecli
az acr list --resource-group myResourceGroup --query "[].{acrLoginServer:loginServer}" --output table
```

A [docker tag][docker-tag] paranccsal címkézze fel a rendszerképet. Az `<acrLoginServer>` helyére az ACR bejelentkezési kiszolgáló nevét vagy a nyilvános beállításjegyzék gazdanevét írja, és frissítse a rendszerkép verzióját a *:v2* értékre a következőképpen:

```console
docker tag mcr.microsoft.com/azuredocs/azure-vote-front:v1 <acrLoginServer>/azure-vote-front:v2
```

Most a [docker push][docker-push] paranccsal töltse fel a rendszerképet a regisztrációs adatbázisba. Az `<acrLoginServer>` helyére az ACR bejelentkezési kiszolgálójának nevét írja be.

> [!NOTE]
> Ha problémákba ütközik az ACR-beállításjegyzékben, győződjön meg arról, hogy még mindig be van jelentkezve. Futtassa az az [ACR login][az-acr-login] parancsot az [Azure Container Registry létrehozása](tutorial-kubernetes-prepare-acr.md#create-an-azure-container-registry) lépésben létrehozott Azure Container Registry neve alapján. Például: `az acr login --name <azure container registry name>`.

```console
docker push <acrLoginServer>/azure-vote-front:v2
```

## <a name="deploy-the-updated-application"></a>A frissített alkalmazás üzembe helyezése

A maximális üzemidő biztosításához az Application Pod több példányának futnia kell. Ellenőrizze a futó előtérbeli példányok számát a [kubectl get pods][kubectl-get] paranccsal:

```
$ kubectl get pods

NAME                               READY     STATUS    RESTARTS   AGE
azure-vote-back-217588096-5w632    1/1       Running   0          10m
azure-vote-front-233282510-b5pkz   1/1       Running   0          10m
azure-vote-front-233282510-dhrtr   1/1       Running   0          10m
azure-vote-front-233282510-pqbfk   1/1       Running   0          10m
```

Ha nem rendelkezik több előtérbeli hüvelyrel, a következőképpen méretezheti az *Azure-vote-elülső* telepítést:

```console
kubectl scale --replicas=3 deployment/azure-vote-front
```

Az alkalmazás frissítéséhez használja a [kubectl set][kubectl-set] parancsot. Az `<acrLoginServer>` helyére a tárolóregisztrációs adatbázis bejelentkezési kiszolgálójának nevét vagy gazdanevét írja, és adja meg a *v2* alkalmazásverziót:

```console
kubectl set image deployment azure-vote-front azure-vote-front=<acrLoginServer>/azure-vote-front:v2
```

Az üzemelő példány monitorozásához használja a [kubectl get pod][kubectl-get] parancsot. A frissített alkalmazás üzembe helyezése során a rendszer a podokat megszünteti, majd az új tárolórendszerképpel újból létrehozza.

```console
kubectl get pods
```

A következő példa megszűnő podokat és új futó példányokat mutat be az üzembe helyezés előrehaladtával:

```
$ kubectl get pods

NAME                               READY     STATUS        RESTARTS   AGE
azure-vote-back-2978095810-gq9g0   1/1       Running       0          5m
azure-vote-front-1297194256-tpjlg  1/1       Running       0          1m
azure-vote-front-1297194256-tptnx  1/1       Running       0          5m
azure-vote-front-1297194256-zktw9  1/1       Terminating   0          1m
```

## <a name="test-the-updated-application"></a>A frissített alkalmazás tesztelése

A frissített alkalmazás megtekintéséhez először kérje le az `azure-vote-front` szolgáltatás külső IP-címét:

```console
kubectl get service azure-vote-front
```

Most nyisson meg egy webböngészőt a szolgáltatás IP-címére:

:::image type="content" source="media/container-service-kubernetes-tutorials/vote-app-updated-external.png" alt-text="Képernyőkép, amely egy helyi webböngészőben megnyitott AK-fürtben futó frissített Rendszerképbeli Azure-beli szavazási alkalmazásra mutat példát.":::

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban frissített egy alkalmazást, és felvetítette ezt a frissítést az AK-fürtbe. Megtanulta végrehajtani az alábbi műveleteket:

> [!div class="checklist"]
> * Az előtér-alkalmazás kódjának frissítése
> * Frissített tárolórendszerkép létrehozása
> * A tárolórendszerkép leküldése az Azure Container Registrybe
> * A frissített tárolórendszerkép üzembe helyezése

Folytassa a következő oktatóanyaggal, amely az AKS-fürtök új Kubernetes-verzióra történő frissítését ismerteti.

> [!div class="nextstepaction"]
> [Kubernetes frissítése][aks-tutorial-upgrade]

<!-- LINKS - external -->
[docker-compose]: https://docs.docker.com/compose/
[docker-push]: https://docs.docker.com/engine/reference/commandline/push/
[docker-tag]: https://docs.docker.com/engine/reference/commandline/tag/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-set]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#set

<!-- LINKS - internal -->
[aks-tutorial-prepare-app]: ./tutorial-kubernetes-prepare-app.md
[aks-tutorial-upgrade]: ./tutorial-kubernetes-upgrade-cluster.md
[az-acr-login]: /cli/azure/acr
[azure-cli-install]: /cli/azure/install-azure-cli
