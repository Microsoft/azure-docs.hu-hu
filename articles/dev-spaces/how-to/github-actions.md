---
title: GitHub-műveletek & Azure Kubernetes Service (előzetes verzió)
services: azure-dev-spaces
ms.date: 04/03/2020
ms.topic: conceptual
description: Lekéréses kérelem módosításainak áttekintése és tesztelése közvetlenül az Azure Kubernetes Service-ben a GitHub-műveletek és az Azure dev Spaces használatával
keywords: Docker, Kubernetes, Azure, AK, Azure Kubernetes szolgáltatás, tárolók, GitHub-műveletek, Helm, Service Mesh, szolgáltatás háló útválasztás, kubectl, k8s
manager: gwallace
ms.custom: devx-track-js, devx-track-azurecli
ms.openlocfilehash: 37ad621609f5a5631b498e55483e5d16e8ac4472
ms.sourcegitcommit: dda0d51d3d0e34d07faf231033d744ca4f2bbf4a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2021
ms.locfileid: "102202110"
---
# <a name="github-actions--azure-kubernetes-service-preview"></a>GitHub-műveletek & Azure Kubernetes Service (előzetes verzió)

[!INCLUDE [Azure Dev Spaces deprecation](../../../includes/dev-spaces-deprecation.md)]

Az Azure dev Spaces egy olyan GitHub-műveletekkel rendelkező munkafolyamatot biztosít, amely lehetővé teszi, hogy a lekéréses kérelmeket közvetlenül az AK-ban tesztelje, mielőtt a pull-kérelem beolvad az adattár fő ágában. A lekéréses kérelmek változásainak áttekintéséhez futó alkalmazásokkal növelheti a fejlesztő és a csapattagok megbízhatóságát is. Ez a futó alkalmazás segítheti a csoporttagokat, például a termék-kezelőket és a tervezőket, a fejlesztés korai szakaszában a felülvizsgálati folyamat részévé válnak.

Ebből az útmutatóból a következőket tudhatja meg:

* Azure fejlesztői tárhelyek beállítása egy felügyelt Kubernetes-fürtön az Azure-ban.
* Helyezzen üzembe egy nagyméretű alkalmazást több szolgáltatással egy fejlesztői tárhelyen.
* A CI/CD beállítása GitHub-műveletekkel.
* Egyetlen, a teljes alkalmazás kontextusában lévő, elkülönített fejlesztési területen tesztelheti a szolgáltatást.

> [!IMPORTANT]
> Ez a szolgáltatás jelenleg előzetes kiadásban elérhető. Az előzetes verziók azzal a feltétellel érhetők el, hogy Ön beleegyezik a [kiegészítő használati feltételekbe](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). A szolgáltatás néhány eleme megváltozhat a nyilvános rendelkezésre állás előtt.

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés. Ha nem rendelkezik Azure-előfizetéssel, létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free).
* Az [Azure CLI telepítve van][azure-cli-installed].
* A [Helm 3 telepítve van][helm-installed].
* Egy GitHub-fiók, amelyen [engedélyezve van a GitHub-művelet][github-actions-beta-signup].
* Az [Azure dev Spaces Bike Sharing minta alkalmazás](https://github.com/Azure/dev-spaces/tree/master/samples/BikeSharingApp/README.md) egy AK-fürtön fut.

## <a name="create-an-azure-container-registry"></a>Azure Container Registry létrehozása

Azure Container Registry létrehozása (ACR):

```azurecli
az acr create --resource-group MyResourceGroup --name <acrName> --sku Basic
```

> [!IMPORTANT]
> Az ACR nevének egyedinek kell lennie az Azure-on belül, és 5-50 alfanumerikus karaktert kell tartalmaznia. A használt betűknek kisbetűsnek kell lenniük.

Mentse a *lekéréséhez* értéket a kimenetből, mert azt egy későbbi lépésben használják.

## <a name="create-a-service-principal-for-authentication"></a>Egyszerű szolgáltatásnév létrehozása a hitelesítéshez

Egyszerű szolgáltatásnév létrehozásához használja [az az ad SP Create-for-RBAC][az-ad-sp-create-for-rbac] . Például:

```azurecli
az ad sp create-for-rbac --sdk-auth --skip-assignment
```

Mentse a JSON-kimenetet, mert egy későbbi lépésben használatos.

Az az [AK show][az-aks-show] paranccsal jelenítheti meg az AK-fürt *azonosítóját* :

```azurecli
az aks show -g MyResourceGroup -n MyAKS  --query id
```

Az ACR *azonosítójának* megjelenítéséhez használja az [az ACR show következőt][az-acr-show] :

```azurecli
az acr show --name <acrName> --query id
```

Használja az [az role hozzárendelés Create][az-role-assignment-create] lehetőséget, hogy *közreműködői* hozzáférést biztosítson az AK-fürthöz, és *AcrPush* a hozzáférést az ACR-hez.

```azurecli
az role assignment create --assignee <ClientId> --scope <AKSId> --role Contributor
az role assignment create --assignee <ClientId>  --scope <ACRId> --role AcrPush
```

> [!IMPORTANT]
> Ahhoz, hogy a szolgáltatás egyszerű hozzáférést biztosítson az adott erőforrásokhoz, mindkét AK-fürt és az ACR tulajdonosának kell lennie.

## <a name="configure-your-github-action"></a>A GitHub-művelet konfigurálása

> [!IMPORTANT]
> A tárházhoz engedélyezve kell lennie a GitHub-műveleteknek. Ha engedélyezni szeretné a GitHub-műveleteket a tárházban, navigáljon a tárházhoz a GitHubon, kattintson a műveletek lapra, és válassza a műveletek engedélyezése ehhez a tárházhoz lehetőséget.

Navigáljon az elágazó tárházhoz, és kattintson a *Beállítások* elemre. Kattintson a *Secrets* elemre a bal oldali oldalsávon. Kattintson *az új titkos kulcs hozzáadása* lehetőségre az alábbi új titkos kódok hozzáadásához:

1. *AZURE_CREDENTIALS*: az egyszerű szolgáltatás létrehozásának teljes kimenete.
1. *RESOURCE_GROUP*: az AK-fürthöz tartozó erőforráscsoport, amely ebben a példában a *MyResourceGroup*.
1. *CLUSTER_NAME*: az AK-fürt neve, amely ebben a példában a *MyAKS*.
1. *CONTAINER_REGISTRY*: az ACR *lekéréséhez* .
1. *Gazdagép*: a fejlesztői terület gazdagépe, amely az űrlapot *<MASTER_SPACE>. <APP_NAME>. <HOST_SUFFIX>*, amely ebben a példában a *dev.bikesharingweb.fedcab0987.EUs.azds.IO*.
1. *IMAGE_PULL_SECRET*: a használni kívánt titok neve, például *demo-Secret*.
1. *MASTER_SPACE*: a szülő fejlesztői terület neve, amely ebben a példában a *dev*.
1. *REGISTRY_USERNAME*: az egyszerű *clientId* származó JSON-kimenetből származó adatok.
1. *REGISTRY_PASSWORD*: az egyszerű *clientSecret* származó JSON-kimenetből származó adatok.

> [!NOTE]
> Az összes ilyen titkot a GitHub-művelet használja, és a [. GitHub/munkafolyamatok/Bikes. YML][github-action-yaml]-ben vannak konfigurálva.

Ha szeretné frissíteni a főtárhelyet a PR-összefésülés után, adja hozzá a *GATEWAY_HOST* titkot, amely az űrlapot *<MASTER_SPACE>. Gateway. <* HOST_SUFFIX>, amely ebben a példában a *dev.Gateway.fedcab0987.EUs.azds.IO*. Miután összevonta a módosításokat az elágazás fő ágában, egy másik művelet fog futni, és a teljes alkalmazást futtatja a fő fejlesztői térben. Ebben a példában a fő terület a *dev*. Ez a művelet a [. GitHub/workflows/bikesharing. YML][github-action-bikesharing-yaml]-ben van konfigurálva.

Továbbá, ha szeretné, hogy a PR-ban lévő módosítások egy unoka-térben fussanak, frissítse a *MASTER_SPACE* és a *gazdagép* titkait. Ha például az alkalmazás a fejlesztés és a *azureuser1* területén fut a *dev* -ben, akkor a PR-t a *dev/azureuser1*:

* Frissítse *MASTER_SPACE* a fölérendelt területként használni kívánt gyermek területre, ebben a példában *azureuser1*.
* A *gazdagép* frissítése *<GRANDPARENT_SPACE>. <APP_NAME>. <HOST_SUFFIX>*, ebben a példában *dev.bikesharingweb.fedcab0987.EUs.azds.IO*.

## <a name="create-a-new-branch-for-code-changes"></a>Új ág létrehozása a kód módosításaihoz

Navigáljon, `BikeSharingApp/` és hozzon létre egy *Bike-images* nevű új ágat.

```cmd
cd dev-spaces/samples/BikeSharingApp/
git checkout -b bike-images
```

[Kerékpárok/server.js][bikes-server-js] szerkesztése a 232 és 233 sorok eltávolításához:

```javascript
    // Hard code image url *FIX ME*
    theBike.imageUrl = "/static/logo.svg";
```

A szakasznak így kell kinéznie:

```javascript
    var theBike = result;
    theBike.id = theBike._id;
    delete theBike._id;
```

Mentse a fájlt, majd használja a és a lehetőséget a `git add` `git commit` módosítások előkészítéséhez.

```cmd
git add Bikes/server.js 
git commit -m "Removing hard coded imageUrl from /bikes/:id route"
```

## <a name="push-your-changes"></a>A módosítások leküldése

A használatával `git push` leküldheti az új ágat az elágazó adattárba:

```cmd
git push origin bike-images
```

A leküldéses folyamat befejezése után navigáljon a GitHubon található elágazó adattárhoz, és hozzon létre egy pull-kérést *az elágazó tárház főágában,* a *Bike-images* ág összevetésével.

A lekéréses kérelem megnyitása után navigáljon a *műveletek* lapra. Ellenőrizze, hogy az új művelet elindult-e, és hogy a *Bikes* szolgáltatást épít-e ki.

## <a name="view-the-child-space-with-your-changes"></a>Az alárendelt terület megtekintése a módosításaival

A művelet befejezését követően egy megjegyzés jelenik meg az új gyermekobjektum URL-címével, a lekéréses kérelem módosításai alapján.

> [!div class="mx-imgBorder"]
> ![GitHub-művelet URL-címe](../media/github-actions/github-action-url.png)

Nyissa meg a *bikesharingweb* szolgáltatást, és nyissa meg az URL-címet a megjegyzésből. Válassza az *Aurelia Briggs (ügyfél)* lehetőséget felhasználóként, majd válassza ki a bérelni kívánt kerékpárt. Győződjön meg arról, hogy már nem jelenik meg a bike helyőrző képe.

Ha egyesíti a módosításokat az elágazás *fő* ágában, egy másik művelet fog futni, és a teljes alkalmazást futtatja a szülő fejlesztői térben. Ebben a példában a szülő terület a *dev*. Ez a művelet a [. GitHub/workflows/bikesharing. YML][github-action-bikesharing-yaml]-ben van konfigurálva.

## <a name="clean-up-your-azure-resources"></a>Azure-erőforrások karbantartása

```azurecli
az group delete --name MyResourceGroup --yes --no-wait
```

## <a name="next-steps"></a>Következő lépések

További információ az Azure dev Spaces működéséről.

> [!div class="nextstepaction"]
> [Az Azure Dev Spaces működése](../how-dev-spaces-works.md)

[azure-cli-installed]: /cli/azure/install-azure-cli
[az-ad-sp-create-for-rbac]: /cli/azure/ad/sp#az-ad-sp-create-for-rbac
[az-acr-show]: /cli/azure/acr#az-acr-show
[az-aks-show]: /cli/azure/aks#az-aks-show
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
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
