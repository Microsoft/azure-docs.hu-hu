---
title: GitHub Actions & Azure Kubernetes Service (előzetes verzió)
services: azure-dev-spaces
ms.date: 04/03/2020
ms.topic: conceptual
description: A lekéréses kérelmek módosításainak áttekintése és tesztelése közvetlenül a Azure Kubernetes Service a GitHub Actions és az Azure Dev Spaces használatával
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, containers, GitHub Actions, Helm, service mesh, service mesh routing, kubectl, k8s
manager: gwallace
ms.custom: devx-track-js, devx-track-azurecli
ms.openlocfilehash: 84d4f94cdb756b0bc11026baaa3acf065604c421
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107777544"
---
# <a name="github-actions--azure-kubernetes-service-preview"></a>GitHub Actions & Azure Kubernetes Service (előzetes verzió)

[!INCLUDE [Azure Dev Spaces deprecation](../../../includes/dev-spaces-deprecation.md)]

Az Azure Dev Spaces a GitHub Actions használatával munkafolyamatot biztosít, amely lehetővé teszi, hogy közvetlenül az AKS-ban tesztelje a lekéréses kérelmek módosításait, mielőtt egyesítené a lekéréses kérelmet az adattár fő ágával. Ha egy futó alkalmazás áttekinti a lekéréses kérelmek módosításait, az növelheti a fejlesztő és a csapattagok bizalmát is. Ez a futó alkalmazás abban is segíthet, hogy a csapat tagjai, például a termékmenedzserek és a tervezők a fejlesztés korai szakaszaiban a felülvizsgálati folyamat részeivé válnak.

Ebből az útmutatóból a következőket tudhatja meg:

* Az Azure Dev Spaces beállítása egy felügyelt Kubernetes-fürtön az Azure-ban.
* Több mikroszolgáltatással rendelkező nagyméretű alkalmazás üzembe helyezése egy Dev Space-térben.
* CI/CD beállítása GitHub-műveletekkel.
* Egyetlen mikroszolgáltatás tesztelése elkülönített Dev Space-térben a teljes alkalmazás kontextusában.

> [!IMPORTANT]
> Ez a szolgáltatás jelenleg előzetes kiadásban elérhető. Az előzetes verziók azzal a feltétellel érhetők el, hogy Ön beleegyezik a [kiegészítő használati feltételekbe](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). A szolgáltatás néhány eleme megváltozhat a nyilvános rendelkezésre állás előtt.

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés. Ha nem rendelkezik Azure-előfizetéssel, létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free).
* [Telepítette az Azure CLI-t.][azure-cli-installed]
* [A Helm 3 telepítve van.][helm-installed]
* GitHub-fiók engedélyezett [GitHub Actions használatával.][github-actions-beta-signup]
* Az [AKS-fürtön](https://github.com/Azure/dev-spaces/tree/master/samples/BikeSharingApp/README.md) futó Azure Dev Spaces Bike Sharing mintaalkalmazás.

## <a name="create-an-azure-container-registry"></a>Azure Container Registry létrehozása

Hozzon létre egy Azure Container Registry (ACR):

```azurecli
az acr create --resource-group MyResourceGroup --name <acrName> --sku Basic
```

> [!IMPORTANT]
> Az ACR nevének egyedinek kell lennie az Azure-ban, és 5–50 alfanumerikus karaktert kell tartalmaznia. Minden használt betűnek kisbetűnek kell lennie.

Mentse a *loginServer* értéket a kimenetből, mert egy későbbi lépésben használni kell.

## <a name="create-a-service-principal-for-authentication"></a>Szolgáltatásnév létrehozása hitelesítéshez

Szolgáltatásnév létrehozásához használja az [az ad sp create-for-rbac][az-ad-sp-create-for-rbac] használhatja. Például:

```azurecli
az ad sp create-for-rbac --sdk-auth --skip-assignment
```

Mentse a JSON-kimenetet, mert egy későbbi lépésben használni kell.

Az [az aks show használatával][az-aks-show] jelenítse meg az AKS-fürt azonosítóját: 

```azurecli
az aks show -g MyResourceGroup -n MyAKS  --query id
```

Az [az acr show használatával][az-acr-show] jelenítse meg *az* ACR azonosítóját:

```azurecli
az acr show --name <acrName> --query id
```

Az az role assignment  create [használatával][az-role-assignment-create] közreműködői hozzáférést adhat az AKS-fürthöz, és *AcrPush-hozzáférést adhat* az ACR-hez.

```azurecli
az role assignment create --assignee <ClientId> --scope <AKSId> --role Contributor
az role assignment create --assignee <ClientId>  --scope <ACRId> --role AcrPush
```

> [!IMPORTANT]
> Az AKS-fürtnek és az ACR-nek is tulajdonosnak kell lennie ahhoz, hogy hozzáférést biztosítson a szolgáltatásnévnek ezekhez az erőforrásokhoz.

## <a name="configure-your-github-action"></a>A GitHub-művelet konfigurálása

> [!IMPORTANT]
> Az adattárhoz GitHub Actions engedélyeznie kell a tárházat. Az GitHub Actions engedélyezéséhez lépjen a GitHubon az adattárra, kattintson a Műveletek lapra, és engedélyezze a műveleteket ehhez az adattárhoz.

Keresse meg az eltárolt adattárat, és kattintson a *Beállítások elemre.* A bal *oldali oldalsávon* kattintson a Titkos kulcsok elemre. Kattintson *az Add a new secret (Új titkos gombra)* elemre az alábbi új titkos gombra kattintva:

1. *AZURE_CREDENTIALS:* a szolgáltatásnév létrehozásának teljes kimenete.
1. *RESOURCE_GROUP:* az AKS-fürt erőforráscsoportja, amely ebben a példában *MyResourceGroup.*
1. *CLUSTER_NAME*: az AKS-fürt neve, amely ebben a példában *MyAKS*.
1. *CONTAINER_REGISTRY:* az ACR *bejelentkezési* kiszolgálója.
1. *HOST*: a Dev Space gazdagépe, amely a *<MASTER_SPACE>.<APP_NAME>.<HOST_SUFFIX>* formát veszi fel, amely ebben a példában a *dev.bikesharingweb.fedcab0987.eus.azds.io.*
1. *IMAGE_PULL_SECRET*: a használni kívánt titkos titkos név, például *demo-secret*.
1. *MASTER_SPACE*: a szülő Dev Space neve, amely ebben a példában *dev*.
1. *REGISTRY_USERNAME:* a szolgáltatásnév létrehozásának JSON-kimenetében a *clientId.*
1. *REGISTRY_PASSWORD:* a *clientSecret a* szolgáltatásnév létrehozásának JSON-kimenetében.

> [!NOTE]
> Ezeket a titkos kódokat a GitHub-művelet használja, és a [.github/workflows/bikes.yml fájlban vannak konfigurálva.][github-action-yaml]

Ha a pr-egyesítés után frissíteni szeretné a fő területet, adja hozzá a GATEWAY_HOST titkos formában *<MASTER_SPACE>.gateway.<HOST_SUFFIX>*, amely ebben *a* példában *dev.gateway.fedcab0987.eus.azds.io.* Miután egyesítette a módosításokat az elágazás fő ágával, egy másik művelet fog futni a teljes alkalmazás újraépítésére és futtatására a fő Dev Space-térben. Ebben a példában a főtér a *dev*. Ez a művelet a [.github/workflows/bikesharing.yml fájlban van konfigurálva.][github-action-bikesharing-yaml]

Továbbá, ha azt szeretné, hogy a lekért adatok változásai egy grandy térben fusson, frissítse a MASTER_SPACE *és* a HOST titkos *térben.* Ha például az alkalmazás *dev/azureuser1* gyermektéren fut a *Dev/azureuser1* gyermektérben, a le pr-kérelmet a dev/azureuser1 gyermektérben futtatja: 

* Frissítse *MASTER_SPACE* a kívánt gyermektérre szülőtérként, ebben a példában: *azureuser1*.
* Ebben a *példában* frissítse a *HOST<GRANDPARENT_SPACE>.<APP_NAME>.<HOST_SUFFIX>,* a következőre: *dev.bikesharingweb.fedcab0987.eus.azds.io.*

## <a name="create-a-new-branch-for-code-changes"></a>Új ág létrehozása a kód módosításaihoz

Lépjen a `BikeSharingApp/` webhelyre, és hozzon létre egy *bike-images nevű új ágat.*

```cmd
cd dev-spaces/samples/BikeSharingApp/
git checkout -b bike-images
```

[Szerkessze a Bikes/server.js][bikes-server-js] a 232. és a 233. sor eltávolításához:

```javascript
    // Hard code image url *FIX ME*
    theBike.imageUrl = "/static/logo.svg";
```

A szakasznak most így kell kinéznie:

```javascript
    var theBike = result;
    theBike.id = theBike._id;
    delete theBike._id;
```

Mentse a fájlt, majd használja a `git add` és a fájlt a módosítások `git commit` szakaszhoz.

```cmd
git add Bikes/server.js 
git commit -m "Removing hard coded imageUrl from /bikes/:id route"
```

## <a name="push-your-changes"></a>Módosítások leküldése

Az `git push` használatával leküldi az új ágat az elágaztatásos adattárba:

```cmd
git push origin bike-images
```

A leküldés befejezése után keresse meg az elágaztatásos adattárat a  GitHubon, és hozzon létre egy lekéréses kérelmet, amely az elágaztatásos adattár fő ágát alapágként hasonlítja össze a *bike-images ággal.*

A lekéréses kérelem megnyitása után lépjen a *Műveletek lapra.* Ellenőrizze, hogy elindult-e egy új művelet, és hogy a *Bikes szolgáltatást építi-e.*

## <a name="view-the-child-space-with-your-changes"></a>A gyermekterület megtekintése a módosításokkal

A művelet befejezése után egy megjegyzés fog látni egy URL-címet az új gyermektérhez a lekéréses kérelem változásai alapján.

> [!div class="mx-imgBorder"]
> ![GitHub-művelet URL-címe](../media/github-actions/github-action-url.png)

Navigáljon *a bikesharingweb szolgáltatáshoz* a megjegyzésből származó URL-cím megnyitásával. Felhasználóként *válassza az Aurelia Ezután (ügyfél)* lehetőséget, majd válasszon ki egy kerékpárt, amit bérelni fog. Ellenőrizze, hogy már nem látja-e a kerékpár helyőrző képét.

Ha egyesíti a módosításokat az elágazás fő ágával, egy másik művelet futtatásával újraépíti és futtatja a teljes alkalmazást a szülő Dev Space-térben.  Ebben a példában a szülőtér a *dev*. Ez a művelet a [.github/workflows/bikesharing.yml fájlban van konfigurálva.][github-action-bikesharing-yaml]

## <a name="clean-up-your-azure-resources"></a>Az Azure-erőforrások megtisztítása

```azurecli
az group delete --name MyResourceGroup --yes --no-wait
```

## <a name="next-steps"></a>Következő lépések

További információ az Azure Dev Spacesről.

> [!div class="nextstepaction"]
> [Az Azure Dev Spaces működése](../how-dev-spaces-works.md)

[azure-cli-installed]: /cli/azure/install-azure-cli
[az-ad-sp-create-for-rbac]: /cli/azure/ad/sp#az_ad_sp_create_for_rbac
[az-acr-show]: /cli/azure/acr#az_acr_show
[az-aks-show]: /cli/azure/aks#az_aks_show
[az-role-assignment-create]: /cli/azure/role/assignment#az_role_assignment_create
[bikes-server-js]: https://github.com/Azure/dev-spaces/blob/master/samples/BikeSharingApp/Bikes/server.js#L232-L233
[bike-sharing-gh]: https://github.com/Azure/dev-spaces/
[bike-sharing-values-yaml]: https://github.com/Azure/dev-spaces/blob/master/samples/BikeSharingApp/charts/values.yaml
[github-actions-beta-signup]: https://github.com/features/actions
[github-action-yaml]: https://github.com/Azure/dev-spaces/blob/master/.github/workflows/bikes.yml
[github-action-bikesharing-yaml]: https://github.com/Azure/dev-spaces/blob/master/.github/workflows/bikesharing.yml
[helm-installed]: https://helm.sh/docs/intro/install/
[supported-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service
[sp-acr]: ../../container-registry/container-registry-auth-service-principal.md
[sp-aks]: ../../aks/kubernetes-service-principal.md
