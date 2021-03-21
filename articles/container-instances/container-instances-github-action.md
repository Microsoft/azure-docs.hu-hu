---
title: Tároló-példány üzembe helyezése a GitHub-művelettel
description: Hozzon létre egy GitHub-műveletet, amely automatizálja a szükséges lépéseket a tároló lemezképének kiépítéséhez, leküldéséhez és üzembe helyezéséhez Azure Container Instances
ms.topic: article
ms.date: 08/20/2020
ms.custom: github-actions-azure, devx-track-azurecli
ms.openlocfilehash: 1409d8fc1430cd9bf67bd735d9826a74979d495b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98762964"
---
# <a name="configure-a-github-action-to-create-a-container-instance"></a>GitHub-művelet konfigurálása tárolópéldány létrehozásához

A [GitHub-műveletek](https://docs.github.com/en/actions) a GitHub szolgáltatásainak egyik csomagja, amellyel automatizálható a szoftverfejlesztői munkafolyamatok ugyanazon a helyen, mint a kód tárolása és a lekéréses kérelmek és problémák közös használata.

Az [üzembe helyezés Azure Container instances](https://github.com/azure/aci-deploy) GitHub művelettel automatizálható egyetlen tároló üzembe helyezése Azure Container instances. A művelettel az az [Container Create][az-container-create] parancshoz hasonló tároló-példány tulajdonságait állíthatja be.

Ez a cikk bemutatja, hogyan állíthat be egy munkafolyamatot egy GitHub-tárházban, amely a következő műveleteket hajtja végre:

* Rendszerkép létrehozása Docker
* A rendszerkép leküldése egy Azure Container registrybe
* A tároló lemezképének üzembe helyezése Azure Container-példányon

Ez a cikk a munkafolyamat beállításának két módját mutatja be:

* [GitHub-munkafolyamat konfigurálása](#configure-github-workflow) – hozzon létre egy munkafolyamatot egy GitHub-tárházban az üzembe helyezés Azure Container instances művelettel és egyéb műveletekkel.  
* [CLI-bővítmény használata](#use-deploy-to-azure-extension) – az `az container app up` Azure CLI [üzembe helyezés az Azure](https://github.com/Azure/deploy-to-azure-cli-extension) -ban bővítményében található parancs használatával. Ez a parancs leegyszerűsíti a GitHub-munkafolyamatok és a telepítés lépéseinek létrehozását.

> [!IMPORTANT]
> A Azure Container Instances GitHub-művelete jelenleg előzetes verzióban érhető el. Az előzetes verziók azzal a feltétellel érhetők el, hogy Ön beleegyezik a [kiegészítő használati feltételekbe][terms-of-use]. A szolgáltatás néhány eleme megváltozhat a nyilvános rendelkezésre állás előtt.

## <a name="prerequisites"></a>Előfeltételek

* **GitHub-fiók** – hozzon létre egy fiókot https://github.com , ha még nem rendelkezik ilyennel.
* **Azure CLI** – a Azure Cloud Shell vagy az Azure CLI helyi telepítését használhatja az Azure CLI lépéseinek elvégzéséhez. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése][azure-cli-install].
* **Azure Container Registry** – ha még nem rendelkezik ilyennel, hozzon létre egy Azure Container registryt az alapszintű szinten az [azure CLI](../container-registry/container-registry-get-started-azure-cli.md), [Azure Portal](../container-registry/container-registry-get-started-portal.md)vagy más módszerek használatával. Jegyezze fel a központi telepítéshez használt erőforráscsoportot, amelyet a GitHub-munkafolyamathoz használ a rendszer.

## <a name="set-up-repo"></a>Tárház beállítása

* A cikkben szereplő példákhoz használja a GitHubot a következő tárház elágazásához: https://github.com/Azure-Samples/acr-build-helloworld-node

  Ez a tárház egy Docker és forrásfájlokat tartalmaz egy kis webalkalmazás tároló-rendszerképének létrehozásához.

  ![A GitHub Fork (Leágaztatás) gombjának (kiemelve) képernyőképe](../container-registry/media/container-registry-tutorial-quick-build/quick-build-01-fork.png)

* Győződjön meg arról, hogy a műveletek engedélyezve vannak a tárházban. Navigáljon az elágazó tárházhoz, és válassza a **Beállítások**  >  **műveletek** lehetőséget. A **műveletek engedélyei** területen győződjön meg arról, hogy a **helyi és a harmadik féltől származó műveletek engedélyezése ehhez a tárházhoz** beállítás van kiválasztva.

## <a name="configure-github-workflow"></a>GitHub-munkafolyamat konfigurálása

### <a name="create-service-principal-for-azure-authentication"></a>Egyszerű szolgáltatásnév létrehozása az Azure-hitelesítéshez

A GitHub-munkafolyamatban Azure-beli hitelesítő adatokat kell megadnia az Azure CLI-vel való hitelesítéshez. Az alábbi példa létrehoz egy szolgáltatásnevet a közreműködői szerepkörrel a tároló-beállításjegyzékhez tartozó erőforráscsoporthoz.

Először kérje le az erőforráscsoport erőforrás-AZONOSÍTÓját. Helyettesítse be a csoport nevét a következő az [Group show][az-group-show] paranccsal:

```azurecli
groupId=$(az group show \
  --name <resource-group-name> \
  --query id --output tsv)
```

Használja az az [ad SP Create-for-RBAC][az-ad-sp-create-for-rbac] az egyszerű szolgáltatás létrehozásához:

```azurecli
az ad sp create-for-rbac \
  --scope $groupId \
  --role Contributor \
  --sdk-auth
```

A kimenet a következőhöz hasonló:

```json
{
  "clientId": "xxxx6ddc-xxxx-xxxx-xxx-ef78a99dxxxx",
  "clientSecret": "xxxx79dc-xxxx-xxxx-xxxx-aaaaaec5xxxx",
  "subscriptionId": "xxxx251c-xxxx-xxxx-xxxx-bf99a306xxxx",
  "tenantId": "xxxx88bf-xxxx-xxxx-xxxx-2d7cd011xxxx",
  "activeDirectoryEndpointUrl": "https://login.microsoftonline.com",
  "resourceManagerEndpointUrl": "https://management.azure.com/",
  "activeDirectoryGraphResourceId": "https://graph.windows.net/",
  "sqlManagementEndpointUrl": "https://management.core.windows.net:8443/",
  "galleryEndpointUrl": "https://gallery.azure.com/",
  "managementEndpointUrl": "https://management.core.windows.net/"
}
```

Mentse a JSON-kimenetet, mert egy későbbi lépésben használatos. Jegyezze fel a `clientId` -t is, amelyre frissítenie kell a szolgáltatásnevet a következő szakaszban.

### <a name="update-service-principal-for-registry-authentication"></a>Szolgáltatásnév frissítése a beállításjegyzék-hitelesítéshez

Frissítse az Azure-szolgáltatás egyszerű hitelesítő adatait, hogy engedélyezze a leküldéses és lekéréses hozzáférést a tároló-beállításjegyzékhez. Ez a lépés lehetővé teszi, hogy a GitHub-munkafolyamat az egyszerű szolgáltatásnév használatával [hitelesítse a tároló-beállításjegyzéket](../container-registry/container-registry-auth-service-principal.md) , és leküldse és lekérje a Docker-rendszerképet. 

Szerezze be a tároló-beállításjegyzék erőforrás-AZONOSÍTÓját. Helyettesítse be a beállításjegyzék nevét a következő az [ACR show][az-acr-show] paranccsal:

```azurecli
registryId=$(az acr show \
  --name <registry-name> \
  --query id --output tsv)
```

Az az [role hozzárendelés Create][az-role-assignment-create] paranccsal rendelje hozzá a AcrPush szerepkört, amely leküldéses és lekéréses hozzáférést biztosít a beállításjegyzékhez. Az egyszerű szolgáltatásnév ügyfél-AZONOSÍTÓjának behelyettesítése:

```azurecli
az role assignment create \
  --assignee <ClientId> \
  --scope $registryId \
  --role AcrPush
```

### <a name="save-credentials-to-github-repo"></a>Hitelesítő adatok mentése a GitHub-tárházba

1. A GitHub felhasználói felületén navigáljon az elágazó tárházhoz, és válassza a **Beállítások**  >  **titkok** lehetőséget. 

1. A következő titkok hozzáadásához válassza az **új titok hozzáadása** lehetőséget:

|Titkos  |Érték  |
|---------|---------|
|`AZURE_CREDENTIALS`     | A szolgáltatás egyszerű létrehozási lépésének teljes JSON-kimenete |
|`REGISTRY_LOGIN_SERVER`   | A beállításjegyzék bejelentkezési kiszolgálójának neve (az összes kisbetűs). Példa: *myregistry.azurecr.IO*        |
|`REGISTRY_USERNAME`     |  Az `clientId` egyszerű szolgáltatás létrehozásakor a JSON-kimenetből       |
|`REGISTRY_PASSWORD`     |  Az `clientSecret` egyszerű szolgáltatás létrehozásakor a JSON-kimenetből |
| `RESOURCE_GROUP` | Az egyszerű szolgáltatásnév hatóköréhez használt erőforráscsoport neve |

### <a name="create-workflow-file"></a>Munkafolyamat-fájl létrehozása

1. A GitHub felhasználói felületén válassza a **műveletek**  >  **Új munkafolyamat** elemet.
1. Válassza **a munkafolyamat beállítása saját maga** lehetőséget.
1. Az **új fájl szerkesztése** területen illessze be a következő YAML-tartalmat a mintakód felülírásához. Fogadja el az alapértelmezett fájlnevet `main.yml` , vagy adja meg a választott fájlnevet.
1. Válassza a **véglegesítés indítása** lehetőséget, opcionálisan adja meg a véglegesítés rövid és részletes leírását, majd válassza az **új fájl véglegesítés** elemet.

```yml
on: [push]
name: Linux_Container_Workflow

jobs:
    build-and-deploy:
        runs-on: ubuntu-latest
        steps:
        # checkout the repo
        - name: 'Checkout GitHub Action'
          uses: actions/checkout@main
          
        - name: 'Login via Azure CLI'
          uses: azure/login@v1
          with:
            creds: ${{ secrets.AZURE_CREDENTIALS }}
        
        - name: 'Build and push image'
          uses: azure/docker-login@v1
          with:
            login-server: ${{ secrets.REGISTRY_LOGIN_SERVER }}
            username: ${{ secrets.REGISTRY_USERNAME }}
            password: ${{ secrets.REGISTRY_PASSWORD }}
        - run: |
            docker build . -t ${{ secrets.REGISTRY_LOGIN_SERVER }}/sampleapp:${{ github.sha }}
            docker push ${{ secrets.REGISTRY_LOGIN_SERVER }}/sampleapp:${{ github.sha }}

        - name: 'Deploy to Azure Container Instances'
          uses: 'azure/aci-deploy@v1'
          with:
            resource-group: ${{ secrets.RESOURCE_GROUP }}
            dns-name-label: ${{ secrets.RESOURCE_GROUP }}${{ github.run_number }}
            image: ${{ secrets.REGISTRY_LOGIN_SERVER }}/sampleapp:${{ github.sha }}
            registry-login-server: ${{ secrets.REGISTRY_LOGIN_SERVER }}
            registry-username: ${{ secrets.REGISTRY_USERNAME }}
            registry-password: ${{ secrets.REGISTRY_PASSWORD }}
            name: aci-sampleapp
            location: 'west us'
```

### <a name="validate-workflow"></a>Munkafolyamat ellenőrzése

A munkafolyamat-fájl véglegesíte után a rendszer elindítja a munkafolyamatot. A munkafolyamat előrehaladásának áttekintéséhez navigáljon a **műveletek**  >  **munkafolyamatok** elemre. 

![Munkafolyamat-folyamatjelző megtekintése](./media/container-instances-github-action/github-action-progress.png)

A munkafolyamat egyes lépései állapotának és eredményének megtekintésével kapcsolatos információkért tekintse meg a [munkafolyamat-futtatási előzmények megtekintése](https://docs.github.com/en/actions/managing-workflow-runs/viewing-workflow-run-history) című témakört. Ha a munkafolyamat nem fejeződött be, tekintse [meg a naplók megtekintése a hibák diagnosztizálásához](https://docs.github.com/en/actions/managing-workflow-runs/using-workflow-run-logs#viewing-logs-to-diagnose-failures)című témakört.

Amikor a munkafolyamat sikeresen befejeződik, az az [Container show][az-container-show] paranccsal lekérheti az *ACI-PéldaAlkalmazás* nevű tároló példány adatait. Helyettesítse be az erőforráscsoport nevét: 

```azurecli
az container show \
  --resource-group <resource-group-name> \
  --name aci-sampleapp \
  --query "{FQDN:ipAddress.fqdn,ProvisioningState:provisioningState}" \
  --output table
```

A kimenet a következőhöz hasonló:

```console
FQDN                                   ProvisioningState
---------------------------------      -------------------
aci-action01.westus.azurecontainer.io  Succeeded
```

A példány üzembe helyezése után navigáljon a tároló teljes tartománynevéhez a böngészőben, és tekintse meg a futó webalkalmazást.

![Webalkalmazás futtatása böngészőben](./media/container-instances-github-action/github-action-container.png)

## <a name="use-deploy-to-azure-extension"></a>Üzembe helyezés az Azure-bővítmény használatával

Azt is megteheti, hogy az Azure CLI-ben az [üzembe helyezés az Azure](https://github.com/Azure/deploy-to-azure-cli-extension) -ban bővítmény használatával konfigurálja a munkafolyamatot. A `az container app up` bővítményben lévő parancs bemeneti paramétereket hoz létre a Azure Container instances üzembe helyezéséhez szükséges munkafolyamatok beállításához. 

Az Azure CLI-vel létrehozott munkafolyamat hasonló ahhoz a munkafolyamathoz, amelyet [manuálisan létrehozhat a GitHub használatával](#configure-github-workflow).

### <a name="additional-prerequisite"></a>További előfeltételek

A forgatókönyv [előfeltételeinek](#prerequisites) és tárházának [beállítása](#set-up-repo) mellett telepítenie kell az Azure CLI  **üzembe helyezés az Azure-ba bővítményét** is.

A bővítmény telepítéséhez futtassa az az [Extension Add][az-extension-add] parancsot:

```azurecli
az extension add \
  --name deploy-to-azure
```

További információ a bővítmények kereséséről, telepítéséről és kezeléséről: [bővítmények használata az Azure CLI-vel](/cli/azure/azure-cli-extensions-overview).

### <a name="run-az-container-app-up"></a>Az `az container app up` parancs futtatása

Az az [Container app up][az-container-app-up] parancs futtatásához legalább a következőket kell megadnia:

* Az Azure Container Registry neve, például *myregistry*
* A GitHub-tárház URL-címe, például: `https://github.com/<your-GitHub-Id>/acr-build-helloworld-node`

Minta parancs:

```azurecli
az container app up \
  --acr myregistry \
  --repository https://github.com/myID/acr-build-helloworld-node
```

### <a name="command-progress"></a>Parancs állapota

* Ha a rendszer kéri, adja meg a GitHub hitelesítő adatait, vagy adja meg a GitHub-fiókkal való hitelesítéshez szükséges adattárház *-és* *felhasználói* hatókörökkel rendelkező [GitHub személyes hozzáférési tokent](https://docs.github.com/en/github/authenticating-to-github/creating-a-personal-access-token) (Pat). Ha a GitHub hitelesítő adatait adja meg, a parancs létrehoz egy PAT-t az Ön számára. A munkafolyamat konfigurálásához kövesse a további kéréseket.

* A parancs a munkafolyamat adattárházának titkait hozza létre:

  * Egyszerű szolgáltatásnév hitelesítő adatai az Azure CLI-hez
  * Az Azure Container Registry eléréséhez szükséges hitelesítő adatok

* Miután a parancs véglegesíti a munkafolyamat-fájlt a tárházba, a rendszer elindítja a munkafolyamatot. 

A kimenet a következőhöz hasonló:

```console
[...]
Checking in file github/workflows/main.yml in the Github repository myid/acr-build-helloworld-node
Creating workflow...
GitHub Action Workflow has been created - https://github.com/myid/acr-build-helloworld-node/runs/515192398
GitHub workflow completed.
Workflow succeeded
Your app is deployed at:  http://acr-build-helloworld-node.eastus.azurecontainer.io:8080/
```

A GitHub felhasználói felületen a munkafolyamat állapotának és eredményeinek megtekintéséhez tekintse meg a [munkafolyamat-futtatási előzmények megtekintése](https://docs.github.com/en/actions/managing-workflow-runs/viewing-workflow-run-history)című témakört.

### <a name="validate-workflow"></a>Munkafolyamat ellenőrzése

A munkafolyamat üzembe helyez egy Azure Container-példányt a GitHub-tárház alapnevével, ebben az esetben: *ACR-Build-HelloWorld-Node*. Ha a munkafolyamat sikeresen befejeződik, olvassa el az *ACR-Build-HelloWorld-Node* nevű tároló-példányt az az [Container show][az-container-show] parancs futtatásával. Helyettesítse be az erőforráscsoport nevét: 

```azurecli
az container show \
  --resource-group <resource-group-name> \
  --name acr-build-helloworld-node \
  --query "{FQDN:ipAddress.fqdn,ProvisioningState:provisioningState}" \
  --output table
```

A kimenet a következőhöz hasonló:

```console
FQDN                                                   ProvisioningState
---------------------------------                      -------------------
acr-build-helloworld-node.westus.azurecontainer.io     Succeeded
```

A példány üzembe helyezése után navigáljon a tároló teljes tartománynevéhez a böngészőben, és tekintse meg a futó webalkalmazást.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Állítsa le a tárolópéldányt az [az container delete][az-container-delete] paranccsal:

```azurecli
az container delete \
  --name <instance-name>
  --resource-group <resource-group-name>
```

Az erőforráscsoport és az ahhoz tartozó összes erőforrás törléséhez futtassa az az [Group delete][az-group-delete] parancsot:

```azurecli
az group delete \
  --name <resource-group-name>
```

## <a name="next-steps"></a>Következő lépések

További műveletek a [GitHub-piactéren](https://github.com/marketplace?type=actions) a fejlesztési munkafolyamat automatizálásához


<!-- LINKS - external -->
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

<!-- LINKS - internal -->

[azure-cli-install]: /cli/azure/install-azure-cli
[az-group-show]: /cli/azure/group#az-group-show
[az-group-delete]: /cli/azure/group#az-group-delete
[az-ad-sp-create-for-rbac]: /cli/azure/ad/sp#az-ad-sp-create-for-rbac
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
[az-container-create]: /cli/azure/container#az-container-create
[az-acr-show]: /cli/azure/acr#az-acr-show
[az-container-show]: /cli/azure/container#az-container-show
[az-container-delete]: /cli/azure/container#az-container-delete
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-container-app-up]: /cli/azure/ext/deploy-to-azure/container/app#ext-deploy-to-azure-az-container-app-up
