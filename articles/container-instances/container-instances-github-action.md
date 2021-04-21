---
title: Tárolópéldány üzembe helyezése GitHub-művelet alapján
description: Konfiguráljon egy GitHub-műveletet, amely automatizálja a tároló rendszerképének buildhez, leküldéséhez és üzembe helyezéséhez szükséges Azure Container Instances
ms.topic: article
ms.date: 08/20/2020
ms.custom: github-actions-azure, devx-track-azurecli
ms.openlocfilehash: e6a4d9ecff292d79f132f933c36b0030e04f4efa
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107771298"
---
# <a name="configure-a-github-action-to-create-a-container-instance"></a>GitHub-művelet konfigurálása tárolópéldány létrehozásához

[GitHub Actions](https://docs.github.com/en/actions) a GitHub egy olyan szolgáltatáscsomagja, amely ugyanazon a helyen automatizálja a szoftverfejlesztési munkafolyamatokat, ahol a kódot tárolja, és együttműködik a lekéréses kérelmeken és problémákon.

A [Deploy to Azure Container Instances](https://github.com/azure/aci-deploy) GitHub művelet használatával automatizálhatja egyetlen tároló üzembe helyezését a Azure Container Instances. A művelet lehetővé teszi, hogy az az container create parancsban találhatóakhoz hasonló tulajdonságokat állítson be egy [tárolópéldányhoz.][az-container-create]

Ez a cikk bemutatja, hogyan állíthat be egy munkafolyamatot egy GitHub-adattárban, amely a következő műveleteket végzi el:

* Rendszerkép összeállítása Docker-fájlból
* A rendszerkép leküldése egy Azure Container Registrybe
* A tároló rendszerképének üzembe helyezése egy Azure Container Instance-példányon

Ez a cikk a munkafolyamat beállításának két módját mutatja be:

* [GitHub-munkafolyamat](#configure-github-workflow) konfigurálása – Munkafolyamat létrehozása GitHub-adattárban a Deploy to Azure Container Instances action and other actions (Üzembe helyezés Azure Container Instances műveletekhez) használatával.  
* [Parancssori felületi bővítmény használata](#use-deploy-to-azure-extension) – Használja az Azure `az container app up` CLI-beli Üzembe helyezés az [Azure-ban](https://github.com/Azure/deploy-to-azure-cli-extension) bővítményben lévő parancsot. Ez a parancs leegyszerűsíti a GitHub-munkafolyamat és az üzembe helyezés lépéseit.

> [!IMPORTANT]
> A GitHub-művelet Azure Container Instances jelenleg előzetes verzióban érhető el. Az előzetes verziók azzal a feltétellel érhetők el, hogy Ön beleegyezik a [kiegészítő használati feltételekbe][terms-of-use]. A szolgáltatás néhány eleme megváltozhat a nyilvános rendelkezésre állás előtt.

## <a name="prerequisites"></a>Előfeltételek

* **GitHub-fiók** – Hozzon létre egy fiókot a webhelyen, ha még https://github.com nem rendelkezik fiókkal.
* **Azure CLI** – Az Azure CLI Azure Cloud Shell az Azure CLI helyi telepítésével használhatja. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése][azure-cli-install].
* **Azure Container Registry** – Ha még nem létezik ilyen, hozzon létre egy Azure Container Registryt az Alapszintű szinten az [Azure CLI,](../container-registry/container-registry-get-started-azure-cli.md) [a Azure Portal](../container-registry/container-registry-get-started-portal.md)vagy más módszerek használatával. Jegyezze fel az üzembe helyezéshez használt erőforráscsoportot, amelyet a GitHub-munkafolyamathoz használ.

## <a name="set-up-repo"></a>Az repo beállítása

* A cikkben található példákhoz használja a GitHubot a következő adattár elágasztatni: https://github.com/Azure-Samples/acr-build-helloworld-node

  Ez az adattár egy Docker-fájlt és egy forrásfájlt tartalmaz egy kis méretű webalkalmazás tároló-rendszerképének létrehozásához.

  ![A GitHub Fork (Leágaztatás) gombjának (kiemelve) képernyőképe](../container-registry/media/container-registry-tutorial-quick-build/quick-build-01-fork.png)

* Győződjön meg arról, hogy a Műveletek engedélyezve vannak az adattárhoz. Keresse meg az eltárolt adattárat, és válassza a **Beállítások**  >  **műveletek lehetőséget.** Az **Actions permissions (Műveletek engedélyei)** mezőben győződjön meg arról, hogy a **Enable local and third party Actions for this repository** (Helyi és külső féltől származó műveletek engedélyezése ehhez az adattárhoz) lehetőség be van jelölve.

## <a name="configure-github-workflow"></a>GitHub-munkafolyamat konfigurálása

### <a name="create-service-principal-for-azure-authentication"></a>Szolgáltatásnév létrehozása Azure-hitelesítéshez

A GitHub-munkafolyamatban Azure-beli hitelesítő adatokat kell megadnia az Azure CLI-ben való hitelesítéshez. A következő példa létrehoz egy szolgáltatásnévt a Közreműködő szerepkörrel, amely a tároló-beállításjegyzék erőforráscsoportjának hatókörére van kihatva.

Először szerezze be az erőforráscsoport erőforrás-azonosítóját. Helyettesítse be a csoport nevét a következő [az group show parancsban:][az-group-show]

```azurecli
groupId=$(az group show \
  --name <resource-group-name> \
  --query id --output tsv)
```

Az [az ad sp create-for-rbac használatával][az-ad-sp-create-for-rbac] hozza létre a szolgáltatásnév:

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

Mentse a JSON-kimenetet, mert egy későbbi lépésben használni kell. Jegyezze fel a et is, amelyet frissítenie kell `clientId` a szolgáltatásnévvel a következő szakaszban.

### <a name="update-service-principal-for-registry-authentication"></a>Szolgáltatásnév frissítése beállításjegyzék-hitelesítéshez

Frissítse az Azure-szolgáltatásnév hitelesítő adatait, hogy engedélyezze a tároló-beállításjegyzék leküldéses és leküldéses hozzáférését. Ez a lépés lehetővé teszi, hogy [](../container-registry/container-registry-auth-service-principal.md) a GitHub-munkafolyamat a szolgáltatásnév használatával hitelesítse magát a tároló-beállításjegyzékben, és docker-rendszerképet lekérhetők és lekérhetők. 

Szerezze be a tároló-beállításjegyzék erőforrás-azonosítóját. Helyettesítse be a regisztrációs adatbázis nevét a következő [az acr show parancsban:][az-acr-show]

```azurecli
registryId=$(az acr show \
  --name <registry-name> \
  --query id --output tsv)
```

Az [az role assignment create használatával][az-role-assignment-create] rendelje hozzá az AcrPush szerepkört, amely leküldési és leküldési hozzáférést biztosít a beállításjegyzékhez. Helyettesítse be a szolgáltatásnév ügyfél-azonosítóját:

```azurecli
az role assignment create \
  --assignee <ClientId> \
  --scope $registryId \
  --role AcrPush
```

### <a name="save-credentials-to-github-repo"></a>Hitelesítő adatok mentése GitHub-adattárba

1. A GitHub felhasználói felületén keresse meg az elágazásos adattárat, és válassza a Settings Secrets **(Titkos beállítások)**  >  **lehetőséget.** 

1. Válassza **az Add a new secret (Új titkos titkos kulcsok** hozzáadása) lehetőséget a következő titkos kulcsok hozzáadásához:

|Titkos  |Érték  |
|---------|---------|
|`AZURE_CREDENTIALS`     | A szolgáltatásnév létrehozásának lépés teljes JSON-kimenete |
|`REGISTRY_LOGIN_SERVER`   | A regisztrációs adatbázis bejelentkezési kiszolgálójának neve (csak kisbetűk). Például: *myregistry.azurecr.io*        |
|`REGISTRY_USERNAME`     |  A `clientId` szolgáltatásnév létrehozásának JSON-kimenetében       |
|`REGISTRY_PASSWORD`     |  A `clientSecret` szolgáltatásnév létrehozásának JSON-kimenetében |
| `RESOURCE_GROUP` | A szolgáltatásnév hatókörének hatóköreként használt erőforráscsoport neve |

### <a name="create-workflow-file"></a>Munkafolyamat-fájl létrehozása

1. A GitHub felhasználói felületén válassza az Actions New workflow **(Műveletek**  >  **új munkafolyamat) lehetőséget.**
1. Válassza **a Munkafolyamat saját beállítása lehetőséget.**
1. Az **Új fájl szerkesztése menüben** illessze be a következő YAML-tartalmat a mintakód felülírásaként. Fogadja el az alapértelmezett `main.yml` fájlnevet, vagy adja meg a választott fájlnevet.
1. Válassza **a Véglegesítés kezdése** lehetőséget, igény szerint adja meg a véglegesítés rövid és kibővített leírását, majd válassza az **Új fájl véglegesítése lehetőséget.**

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

A munkafolyamat-fájl véglegesítését követően a munkafolyamat aktiválódik. A munkafolyamat előrehaladásának áttekintéshez lépjen az **Actions**  >  **Workflows (Műveletek munkafolyamatai) lapra.** 

![Munkafolyamat előrehaladásának megtekintése](./media/container-instances-github-action/github-action-progress.png)

A [munkafolyamat egyes lépésinek](https://docs.github.com/en/actions/managing-workflow-runs/viewing-workflow-run-history) állapotáról és eredményeinek megtekintéséről a Munkafolyamat-futtatás előzményeinek megtekintése szakasz nyújt tájékoztatást. Ha a munkafolyamat nem fejeződik be, tekintse meg [a Naplók megtekintése a hibák diagnosztizálásához szakaszt.](https://docs.github.com/en/actions/managing-workflow-runs/using-workflow-run-logs#viewing-logs-to-diagnose-failures)

Ha a munkafolyamat sikeresen befejeződött, az [az container show][az-container-show] parancs futtatásával szerezze be az *aci-sampleapp* nevű tárolópéldány adatait. Helyettesítse be az erőforráscsoport nevét: 

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

A példány kiépítése után nyissa meg a tároló teljes tartományát a böngészőben a futó webalkalmazás megtekintéséhez.

![Webalkalmazás futtatása böngészőben](./media/container-instances-github-action/github-action-container.png)

## <a name="use-deploy-to-azure-extension"></a>A Deploy to Azure extension (Üzembe helyezés az Azure-ban) használata

Másik lehetőségként az [Azure CLI-beli](https://github.com/Azure/deploy-to-azure-cli-extension) Üzembe helyezés az Azure-ban bővítmény használatával konfigurálhatja a munkafolyamatot. A `az container app up` bővítményben lévő parancs bemeneti paramétereket vesz fel, hogy beállítsa az üzembe helyező munkafolyamatot a Azure Container Instances. 

Az Azure CLI által létrehozott munkafolyamat hasonlít a GitHub használatával manuálisan [létrehozható munkafolyamathoz.](#configure-github-workflow)

### <a name="additional-prerequisite"></a>További előfeltételek

A forgatókönyv [](#prerequisites) előfeltételei és a [repo](#set-up-repo) beállítása mellett telepítenie kell a Deploy to Azure extension for the Azure CLI (Üzembe helyezés az **Azure-ban)** bővítményt is az Azure CLI-hez.

Futtassa [az az extension add][az-extension-add] parancsot a bővítmény telepítéséhez:

```azurecli
az extension add \
  --name deploy-to-azure
```

További információ a bővítmények kereséséről, telepítéséről és kezeléséről: [Bővítmények használata az Azure CLI-val.](/cli/azure/azure-cli-extensions-overview)

### <a name="run-az-container-app-up"></a>Az `az container app up` parancs futtatása

Az [az container app up parancs futtatásához][az-container-app-up] adja meg legalább a következőt:

* Az Azure Container Registry neve, például *myregistry*
* A GitHub-adattár URL-címe, például: `https://github.com/<your-GitHub-Id>/acr-build-helloworld-node`

Mintaparancs:

```azurecli
az container app up \
  --acr myregistry \
  --repository https://github.com/myID/acr-build-helloworld-node
```

### <a name="command-progress"></a>Parancs állapota

* Amikor a rendszer kéri, adja meg GitHub-hitelesítő adatait, vagy  adjon meg  egy személyes [hozzáférési jogkivonatot](https://docs.github.com/en/github/authenticating-to-github/creating-a-personal-access-token) (PAT), amely adattárat és felhasználói hatókört biztosít a GitHub-fiókkal való hitelesítéshez. Ha GitHub-hitelesítő adatokat ad meg, a parancs létrehoz egy PAT-t. A munkafolyamat konfigurálásához kövesse a további utasításokat.

* A parancs titkos táras titkos okat hoz létre a munkafolyamathoz:

  * Szolgáltatásnév hitelesítő adatai az Azure CLI-hez
  * Az Azure Container Registry eléréséhez szükséges hitelesítő adatok

* Miután a parancs véglegesítést ad a munkafolyamat-fájlnak az adattára számára, a munkafolyamat aktiválódik. 

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

Az egyes lépésekkel kapcsolatos munkafolyamatok állapotának és eredményeinek a GitHub felhasználói felületén való megtekintéséhez lásd: [Munkafolyamat-futtatás előzményeinek megtekintése.](https://docs.github.com/en/actions/managing-workflow-runs/viewing-workflow-run-history)

### <a name="validate-workflow"></a>Munkafolyamat ellenőrzése

A munkafolyamat üzembe helyez egy Azure-tárolópéldányt a GitHub-adattár alapnevének használatával, amely ebben az esetben *az acr-build-helloworld-node.* Ha a munkafolyamat sikeresen befejeződött, az az container show parancs futtatásával szerezze be az *acr-build-helloworld-node* nevű [tárolópéldány][az-container-show] adatait. Helyettesítse be az erőforráscsoport nevét: 

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

A példány kiépítése után nyissa meg a tároló teljes tartományát a böngészőben a futó webalkalmazás megtekintéséhez.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Állítsa le a tárolópéldányt az [az container delete][az-container-delete] paranccsal:

```azurecli
az container delete \
  --name <instance-name>
  --resource-group <resource-group-name>
```

Az erőforráscsoport és a benne álló összes erőforrás törléséhez futtassa az [az group delete][az-group-delete] parancsot:

```azurecli
az group delete \
  --name <resource-group-name>
```

## <a name="next-steps"></a>Következő lépések

A [githubi piactéren további](https://github.com/marketplace?type=actions) műveleteket kereshet a fejlesztési munkafolyamat automatizálásához


<!-- LINKS - external -->
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

<!-- LINKS - internal -->

[azure-cli-install]: /cli/azure/install-azure-cli
[az-group-show]: /cli/azure/group#az_group_show
[az-group-delete]: /cli/azure/group#az_group_delete
[az-ad-sp-create-for-rbac]: /cli/azure/ad/sp#az_ad_sp_create_for_rbac
[az-role-assignment-create]: /cli/azure/role/assignment#az_role_assignment_create
[az-container-create]: /cli/azure/container#az_container_create
[az-acr-show]: /cli/azure/acr#az_acr_show
[az-container-show]: /cli/azure/container#az_container_show
[az-container-delete]: /cli/azure/container#az_container_delete
[az-extension-add]: /cli/azure/extension#az_extension_add
[az-container-app-up]: /cli/azure/ext/deploy-to-azure/container/app#ext-deploy-to-azure-az-container-app-up
