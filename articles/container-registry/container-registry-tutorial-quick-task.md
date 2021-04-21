---
title: Oktatóanyag – Tároló rendszerképének gyors összeállítása
description: Ebben az oktatóanyagban megtudhatja, hogyan állíthat össze Docker-tárolórendszerképet az Azure-ban az Azure Container Registry Tasks (ACR Tasks) használatával, majd hogyan helyezheti azokat üzembe az Azure Container Instances szolgáltatásban.
ms.topic: tutorial
ms.date: 11/24/2020
ms.custom: seodec18, mvc, devx-track-azurecli
ms.openlocfilehash: 282e6ea56835fba679510a29af936c1fbcb3ead2
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107775348"
---
# <a name="tutorial-build-and-deploy-container-images-in-the-cloud-with-azure-container-registry-tasks"></a>Oktatóanyag: Tárolólemezképek buildelése és üzembe helyezése a felhőben az Azure Container Registry Tasks használatával

Az [ACR Tasks](container-registry-tasks-overview.md) az Azure Container Registry egy szolgáltatáscsomagja, amely lehetővé teszi a Docker-tárolórendszerképek zökkenőmentes és hatékony összeállítását az Azure-ban. Ebben a cikkben az ACR Tasks *gyors feladat* funkciójának használatával ismerkedhet meg.

A „belső” fejlesztési ciklus a kódírás, alkalmazás-összeállítás és alkalmazástesztelés ismétlődő folyamata a verziókövetésbe küldés előtt. A gyors feladat funkció kiterjeszti a „belső” fejlesztési ciklust a felhőre, és lehetővé teszi az összeállítások sikerességének ellenőrzését, valamint a sikeresen összeállított rendszerképek automatikus leküldését a tárolóregisztrációs adatbázisba. A rendszerképek összeállítása natív módon a felhőben történik, a regisztrációs adatbázis közelében, ami meggyorsítja az üzembe helyezést.

A Docker-fájlokkal kapcsolatos ismeretek közvetlenül kamatoztathatók az ACR Tasks használata során. A Docker-fájlokat nem, csupán a futtatott parancsot kell módosítani, ha az ACR Tasks segítségével szeretne a felhőben összeállítást végrehajtani. 

Ez az oktatóanyag egy sorozat első része, és az alábbiakat ismerteti:

> [!div class="checklist"]
> * Mintaalkalmazás forráskódjának lekérése
> * Tárolórendszerkép összeállítása az Azure-ban
> * Tároló üzembe helyezése az Azure Container Instances szolgáltatásban

A további oktatóanyagokban elsajátítja majd, hogyan lehet az ACR Tasks segítségével automatizálni a tárolórendszerképek összeállítását kódvéglegesítés, illetve az alapként szolgáló rendszerképek frissítése alkalmával. ACR-feladatok többlépéses feladatokat is futtathat egy [YAML-fájl](container-registry-tasks-multi-step.md)használatával, amely több tároló építési, leküldési és opcionális tesztelési lépéseit határozza meg.

## <a name="prerequisites"></a>Előfeltételek

### <a name="github-account"></a>GitHub-fiók

Hozzon létre egy fiókot a https://github.com webhelyen, ha még nem rendelkezik fiókkal. Ebben az oktatóanyag-sorozatban egy GitHub-adattárat használunk az ACR Tasks automatizált rendszerkép-összeállítási képességeinek bemutatásához.

### <a name="fork-sample-repository"></a>Mintaadattár leágaztatása

Ezután a GitHub felhasználói felületén ágaztassa le a mintaadattárat a GitHub-fiókba. Ebben az oktatóanyagban egy tárolórendszerképet állít össze az adattárban lévő forrásból, a következő oktatóanyagban pedig egy véglegesítést küld le a leágaztatott adattárba egy automatizált feladat elindítása céljából.

Ágaztassa le ezt az adattárat: https://github.com/Azure-Samples/acr-build-helloworld-node.

![A GitHub Fork (Leágaztatás) gombjának (kiemelve) képernyőképe][quick-build-01-fork]

### <a name="clone-your-fork"></a>A leágaztatás klónozása

Miután leágaztatta az adattárat, klónozza a leágaztatást, és lépjen a helyi klónt tartalmazó könyvtárba.

Klónozza az adattárat a `git` használatával, és cserélje **\<your-github-username\>** le a helyére a GitHub-felhasználónevét:

```console
git clone https://github.com/<your-github-username>/acr-build-helloworld-node
```

Lépjen a forráskódot tartalmazó könyvtárba:

```console
cd acr-build-helloworld-node
```

### <a name="bash-shell"></a>Bash-felület

Az oktatóanyag-sorozatban használt parancsok a Bash-felületnek megfelelően vannak formázva. Ha inkább a PowerShellt, a parancssort vagy egy másik rendszerhéjat szeretne használni, ennek megfelelően esetleg módosítania kell a sorok tartalmát és a környezeti változók formátumát.

[!INCLUDE [azure-cli-prepare-your-environment-h3.md](../../includes/azure-cli-prepare-your-environment-h3.md)]

## <a name="build-in-azure-with-acr-tasks"></a>Összeállítás az Azure-ban az ACR Tasks használatával

Miután lekérte a forráskódot a gépre, az alábbi lépéseket követve hozzon lére egy tárolóregisztrációs adatbázist, és állítsa össze a tárolórendszerképet az ACR Tasks használatával.

A mintaparancsok könnyebb végrehajtása érdekében a sorozat oktatóanyagai rendszerhéj-környezeti változókat használnak. Futtassa a következő parancsot az `ACR_NAME` változó beállításához. Cserélje **\<registry-name\>** le a helyére az új tárolójegyzék egyedi nevét. A beállításjegyzék nevének egyedinek kell lennie az Azure-ban, csak kisbetűket tartalmazhat, és 5–50 alfanumerikus karaktert tartalmazhat. Az oktatóanyagban létrehozott egyéb erőforrások is ezen néven alapulnak, így csak ezt az első változót kell módosítania.

```console
ACR_NAME=<registry-name>
```

Miután a tárolóregisztrációs adatbázis környezeti változóját ellátta értékkel, az oktatóanyagban szereplő többi parancsot az értékek szerkesztése nélkül másolhatja és beillesztheti. Az alábbi parancsok végrehajtásával hozzon létre egy erőforráscsoportot és egy tárolóregisztrációs adatbázist:

```azurecli
RES_GROUP=$ACR_NAME # Resource Group name

az group create --resource-group $RES_GROUP --location eastus
az acr create --resource-group $RES_GROUP --name $ACR_NAME --sku Standard --location eastus
```

Miután a regisztrációs adatbázis létrejött, az ACR Tasks használatával állítson össze egy tárolórendszerképet a mintakódból. Az [az acr build][az-acr-build] parancs futtatásával hajtson végre egy *gyors feladatot*:

```azurecli
az acr build --registry $ACR_NAME --image helloacrtasks:v1 .
```

Az [az acr build][az-acr-build] parancs kimenete az alábbihoz hasonló. Láthatja a forráskód (a „környezet”) feltöltését az Azure-ba, valamint az ACR Tasks által a felhőben futtatott `docker build` művelet részleteit. Mivel az ACR Tasks a `docker build` használatával állítja össze a rendszerképeket, a Docker-fájlokat nem kell módosítani az ACR Tasks használatának azonnali megkezdéséhez.

```output
Packing source code into tar file to upload...
Sending build context (4.813 KiB) to ACR...
Queued a build with build ID: da1
Waiting for build agent...
2020/11/18 18:31:42 Using acb_vol_01185991-be5f-42f0-9403-a36bb997ff35 as the home volume
2020/11/18 18:31:42 Setting up Docker configuration...
2020/11/18 18:31:43 Successfully set up Docker configuration
2020/11/18 18:31:43 Logging in to registry: myregistry.azurecr.io
2020/11/18 18:31:55 Successfully logged in
Sending build context to Docker daemon   21.5kB
Step 1/5 : FROM node:15-alpine
15-alpine: Pulling from library/node
Digest: sha256:8dafc0968fb4d62834d9b826d85a8feecc69bd72cd51723c62c7db67c6dec6fa
Status: Image is up to date for node:15-alpine
 ---> a56170f59699
Step 2/5 : COPY . /src
 ---> 88087d7e709a
Step 3/5 : RUN cd /src && npm install
 ---> Running in e80e1263ce9a
npm notice created a lockfile as package-lock.json. You should commit this file.
npm WARN helloworld@1.0.0 No repository field.

up to date in 0.1s
Removing intermediate container e80e1263ce9a
 ---> 26aac291c02e
Step 4/5 : EXPOSE 80
 ---> Running in 318fb4c124ac
Removing intermediate container 318fb4c124ac
 ---> 113e157d0d5a
Step 5/5 : CMD ["node", "/src/server.js"]
 ---> Running in fe7027a11787
Removing intermediate container fe7027a11787
 ---> 20a27b90eb29
Successfully built 20a27b90eb29
Successfully tagged myregistry.azurecr.io/helloacrtasks:v1
2020/11/18 18:32:11 Pushing image: myregistry.azurecr.io/helloacrtasks:v1, attempt 1
The push refers to repository [myregistry.azurecr.io/helloacrtasks]
6428a18b7034: Preparing
c44b9827df52: Preparing
172ed8ca5e43: Preparing
8c9992f4e5dd: Preparing
8dfad2055603: Preparing
c44b9827df52: Pushed
172ed8ca5e43: Pushed
8dfad2055603: Pushed
6428a18b7034: Pushed
8c9992f4e5dd: Pushed
v1: digest: sha256:b038dcaa72b2889f56deaff7fa675f58c7c666041584f706c783a3958c4ac8d1 size: 1366
2020/11/18 18:32:43 Successfully pushed image: myregistry.azurecr.io/helloacrtasks:v1
2020/11/18 18:32:43 Step ID acb_step_0 marked as successful (elapsed time in seconds: 15.648945)
The following dependencies were found:
- image:
    registry: myregistry.azurecr.io
    repository: helloacrtasks
    tag: v1
    digest: sha256:b038dcaa72b2889f56deaff7fa675f58c7c666041584f706c783a3958c4ac8d1
  runtime-dependency:
    registry: registry.hub.docker.com
    repository: library/node
    tag: 15-alpine
    digest: sha256:8dafc0968fb4d62834d9b826d85a8feecc69bd72cd51723c62c7db67c6dec6fa
  git: {}

Run ID: da1 was successful after 1m9.970148252s
```

A kimenet vége felé az ACR Tasks a rendszerkép észlelt függőségeit jeleníti meg. Ez lehetővé teszi az ACR Tasks számára a rendszerképek összeállításának automatikus elindítását az alapként szolgáló rendszerképek frissítésekor, például amikor az alapul szolgáló rendszerképet az operációs rendszer vagy a keretrendszer javításával frissítették. Az oktatóanyag-sorozat egy későbbi része ismerteti, hogy az ACR Tasks hogyan támogatja az alapként szolgáló rendszerképek frissítését.

## <a name="deploy-to-azure-container-instances"></a>Üzembe helyezés az Azure Container Instances szolgáltatásban

Az ACR Tasks alapértelmezés szerint automatikusan leküldi a sikeresen összeállított rendszerképeket a regisztrációs adatbázisba, így Ön onnan azonnal üzembe helyezheti azokat.

Ebben a szakaszban létrehoz egy Azure Key Vault kulcstárolót és egy szolgáltatásnevet, majd üzembe helyezi a tárolót az Azure Container Instances (ACI) szolgáltatásban a szolgáltatásnév hitelesítő adataival.

### <a name="configure-registry-authentication"></a>Regisztrációs adatbázis hitelesítésének konfigurálása

Az Azure-beli tárolóregisztrációs adatbázisok eléréséhez minden éles forgatókönyvben [szolgáltatásneveket][service-principal-auth] ajánlott használni. A szolgáltatásnevek lehetővé teszik a szerepköralapú hozzáférés-vezérlés biztosítását a tárolórendszerképek számára. Konfigurálhat például egy olyan szolgáltatásnevet, amely csak lekérés céljából férhet hozzá a regisztrációs adatbázishoz.

#### <a name="create-a-key-vault"></a>Kulcstartó létrehozása

Ha még nem rendelkezik tárolóval az [Azure Key Vaultban](../key-vault/index.yml), hozzon létre egyet az Azure CLI alábbi parancsaival.

```azurecli
AKV_NAME=$ACR_NAME-vault

az keyvault create --resource-group $RES_GROUP --name $AKV_NAME
```

#### <a name="create-a-service-principal-and-store-credentials"></a>Szolgáltatásnév létrehozása és a hitelesítő adatok tárolása

Létre kell hoznia egy szolgáltatásnevet, és el kell tárolnia annak hitelesítő adatait a kulcstárolóban.

Az [az ad sp create-for-rbac][az-ad-sp-create-for-rbac] paranccsal hozhatja létre a szolgáltatásnevet, és a **jelszavát** az [az keyvault secret set][az-keyvault-secret-set] paranccsal tárolhatja el a tárolóban:

```azurecli
# Create service principal, store its password in AKV (the registry *password*)
az keyvault secret set \
  --vault-name $AKV_NAME \
  --name $ACR_NAME-pull-pwd \
  --value $(az ad sp create-for-rbac \
                --name $ACR_NAME-pull \
                --scopes $(az acr show --name $ACR_NAME --query id --output tsv) \
                --role acrpull \
                --query password \
                --output tsv)
```

Az előző parancs argumentuma az acrpull szerepkörrel konfigurálja a szolgáltatásnévt, amely csak lekért hozzáférést biztosít a `--role` beállításjegyzékhez.  Leküldéses és leküldéses hozzáférés megadásához módosítsa a argumentumot `--role` *acrpush (acrpush) argumentumra.*

Ezután tárolja el a szolgáltatásnév *appId* azonosítóját a tárolóban, amely az Azure Container Registry szolgáltatásban a hitelesítéskor megadandó **felhasználónév** lesz:

```azurecli
# Store service principal ID in AKV (the registry *username*)
az keyvault secret set \
    --vault-name $AKV_NAME \
    --name $ACR_NAME-pull-usr \
    --value $(az ad sp show --id http://$ACR_NAME-pull --query appId --output tsv)
```

Létrehozott egy Azure Key Vault-tárolót, és eltárolt benne két titkos kulcsot:

* `$ACR_NAME-pull-usr`: A szolgáltatásnév azonosítója, amely a tárolóregisztrációs adatbázis **felhasználóneveként** szolgál.
* `$ACR_NAME-pull-pwd`: A szolgáltatásnév jelszava, amely a tárolóregisztrációs adatbázis **jelszavaként** szolgál.

Innentől ezekre a titkos kulcsokra név alapján hivatkozhat, amikor Ön vagy az alkalmazások és szolgáltatások rendszerképeket kérnek le a regisztrációs adatbázisból.

### <a name="deploy-a-container-with-azure-cli"></a>Tároló üzembe helyezése az Azure CLI használatával

Miután a szolgáltatásnév hitelesítő adatait titkos kulcsként mentette az Azure Key Vaultban, az alkalmazások és a szolgáltatások a kulcsok használatával hozzáférhetnek a privát regisztrációs adatbázishoz.

A következő [az container create][az-container-create] parancs végrehajtásával helyezzen üzembe egy tárolópéldányt. A parancs a szolgáltatásnévnek az Azure Key Vault-tárolóban tárolt hitelesítő adatait használja a tárolóregisztrációs adatbázisban való hitelesítéshez.

```azurecli
az container create \
    --resource-group $RES_GROUP \
    --name acr-tasks \
    --image $ACR_NAME.azurecr.io/helloacrtasks:v1 \
    --registry-login-server $ACR_NAME.azurecr.io \
    --registry-username $(az keyvault secret show --vault-name $AKV_NAME --name $ACR_NAME-pull-usr --query value -o tsv) \
    --registry-password $(az keyvault secret show --vault-name $AKV_NAME --name $ACR_NAME-pull-pwd --query value -o tsv) \
    --dns-name-label acr-tasks-$ACR_NAME \
    --query "{FQDN:ipAddress.fqdn}" \
    --output table
```

A `--dns-name-label` értékének egyedinek kell lennie az Azure-ban, így az előző parancs hozzáfűzi a tárolóregisztrációs adatbázis nevét a tároló DNS-nevének címkéjéhez. A parancs kimenete a tároló teljes tartománynevét jeleníti meg, például:

```output
FQDN
----------------------------------------------
acr-tasks-myregistry.eastus.azurecontainer.io
```

Jegyezze fel a tároló teljes tartománynevét, amelyet a következő szakaszban fog használni.

### <a name="verify-the-deployment"></a>Az üzemelő példány ellenőrzése

A tároló indítási folyamatának megtekintéséhez használja az [az container attach][az-container-attach] parancsot:

```azurecli
az container attach --resource-group $RES_GROUP --name acr-tasks
```

A kimenet először a tároló állapotát jeleníti meg a rendszerkép leindításakor, majd a helyi konzol STDOUT és STDERR fájlját a tárolóhoz `az container attach` köti.

```output
Container 'acr-tasks' is in state 'Running'...
(count: 1) (last timestamp: 2020-11-18 18:39:10+00:00) pulling image "myregistry.azurecr.io/helloacrtasks:v1"
(count: 1) (last timestamp: 2020-11-18 18:39:15+00:00) Successfully pulled image "myregistry.azurecr.io/helloacrtasks:v1"
(count: 1) (last timestamp: 2020-11-18 18:39:17+00:00) Created container
(count: 1) (last timestamp: 2020-11-18 18:39:17+00:00) Started container

Start streaming logs:
Server running at http://localhost:80
```

Amikor megjelenik a `Server running at http://localhost:80` üzenet, adja meg a tároló teljes tartománynevét a böngészőben a futó alkalmazás megtekintéséhez. A teljes tartománynévnek meg kell jelennie az előző szakaszban végrehajtott `az container create` parancs kimenetében.

:::image type="content" source="media/container-registry-tutorial-quick-build/quick-build-02-browser.png" alt-text="Böngészőben futó mintaalkalmazás":::

A konzolt a `Control+C` billentyűkombinációval választhatja le a tárolóról.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Állítsa le a tárolópéldányt az [az container delete][az-container-delete] paranccsal:

```azurecli
az container delete --resource-group $RES_GROUP --name acr-tasks
```

Az oktatóanyagban létrehozott *összes* erőforrás, így a tárolóregisztrációs adatbázis, a kulcstároló és a szolgáltatásnév eltávolításához hajtsa végre az alábbi parancsokat. Ezeket az erőforrásokat azonban a sorozat [következő oktatóanyagában](container-registry-tutorial-build-task.md) is használjuk majd, ezért érdemes megtartani azokat, ha egyből továbblép a következő oktatóanyagra.

```azurecli
az group delete --resource-group $RES_GROUP
az ad sp delete --id http://$ACR_NAME-pull
```

## <a name="next-steps"></a>Következő lépések

Miután egy gyors feladattal tesztelte a belső ciklust, konfiguráljon egy **összeállítási feladatot** a tárolórendszerképek összeállításának automatikus aktiválására, amikor forráskódot véglegesít egy Git-adattárban:

> [!div class="nextstepaction"]
> [Automatikus összeállítás aktiválása feladatokkal](container-registry-tutorial-build-task.md)

<!-- LINKS - External -->
[sample-archive]: https://github.com/Azure-Samples/acr-build-helloworld-node/archive/master.zip

<!-- LINKS - Internal -->
[azure-cli]: /cli/azure/install-azure-cli
[az-acr-build]: /cli/azure/acr#az_acr_build
[az-ad-sp-create-for-rbac]: /cli/azure/ad/sp#az_ad_sp_create_for_rbac
[az-container-attach]: /cli/azure/container#az_container_attach
[az-container-create]: /cli/azure/container#az_container_create
[az-container-delete]: /cli/azure/container#az_container_delete
[az-keyvault-create]: /cli/azure/keyvault/secret#az_keyvault_create
[az-keyvault-secret-set]: /cli/azure/keyvault/secret#az_keyvault_secret_set
[az-login]: /cli/azure/reference-index#az_login
[service-principal-auth]: container-registry-auth-service-principal.md

<!-- IMAGES -->
[quick-build-01-fork]: ./media/container-registry-tutorial-quick-build/quick-build-01-fork.png
[quick-build-02-browser]: ./media/container-registry-tutorial-quick-build/quick-build-02-browser.png
