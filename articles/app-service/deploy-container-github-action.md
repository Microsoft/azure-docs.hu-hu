---
title: Egyéni tároló CI/CD a GitHub-műveletekből
description: Megtudhatja, hogyan helyezhet üzembe egyéni linuxos tárolót a GitHub-műveletek használatával App Service egy CI/CD-folyamatból.
ms.devlang: na
ms.topic: article
ms.date: 12/04/2020
ms.author: jafreebe
ms.reviewer: ushan
ms.custom: github-actions-azure
ms.openlocfilehash: 1fe09970bcb9b9432b9b6f22de04bb24f1e84fa8
ms.sourcegitcommit: d63f15674f74d908f4017176f8eddf0283f3fac8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/07/2021
ms.locfileid: "106582241"
---
# <a name="deploy-a-custom-container-to-app-service-using-github-actions"></a>Egyéni tároló üzembe helyezése a GitHub-műveletek használatával App Service

A [GitHub-műveletek](https://docs.github.com/en/actions) révén rugalmasan hozhat létre automatizált szoftverfejlesztői munkafolyamatokat. Az [Azure web Deploy művelettel](https://github.com/Azure/webapps-deploy)automatizálhatja a munkafolyamatot, hogy egyéni tárolókat helyezzen üzembe [app Service](overview.md) a GitHub-műveletek használatával.

A munkafolyamatot egy YAML-(. YML) fájl határozza meg a `/.github/workflows/` tárház elérési útjában. Ez a definíció a munkafolyamat különböző lépéseit és paramétereit tartalmazza.

Azure App Service tároló munkafolyamathoz a fájl három szakaszt tartalmaz:

|Section  |Feladatok  |
|---------|---------|
|**Hitelesítés** | 1. az egyszerű szolgáltatásnév vagy a közzétételi profil beolvasása. <br /> 2. hozzon létre egy GitHub-titkot. |
|**Létrehozás** | 1. hozza létre a környezetet. <br /> 2. hozza létre a tároló rendszerképét. |
|**Telepítés** | 1. Telepítse a tároló lemezképét. |

## <a name="prerequisites"></a>Előfeltételek

- Aktív előfizetéssel rendelkező Azure-fiók. [Hozzon létre egy fiókot ingyenesen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- Egy GitHub-fiók. Ha még nem rendelkezik ilyennel, regisztráljon [ingyenesen](https://github.com/join). A Azure App Service üzembe helyezéséhez programkódot kell létrehoznia egy GitHub-tárházban. 
- Egy működő tároló-beállításjegyzék és Azure App Service-alkalmazás tárolók számára. Ez a példa Azure Container Registry használ. Győződjön meg arról, hogy a teljes telepítést a tárolók Azure App Serviceához hajtja végre. A hagyományos webalkalmazásokkal ellentétben a tárolók Web Apps szolgáltatás nem rendelkezik alapértelmezett kezdőlaptal. Tegye közzé a tárolót, hogy legyen egy működő példája.
    - [Megtudhatja, hogyan hozhat létre egy tárolóval Node.js alkalmazást a Docker használatával, leküldheti a tároló lemezképét egy beállításjegyzékbe, majd üzembe helyezheti a rendszerképet Azure App Service](/azure/developer/javascript/tutorial-vscode-docker-node-01)
        
## <a name="generate-deployment-credentials"></a>Központi telepítési hitelesítő adatok előállítása

Az Azure App Services a GitHub-műveletekhez való hitelesítésének ajánlott módja a közzétételi profil. A hitelesítést egy egyszerű szolgáltatással is elvégezheti, de a folyamat további lépéseket igényel. 

Mentse a közzétételi profil hitelesítő adatait vagy egyszerű szolgáltatásnevet [GitHub-titokként](https://docs.github.com/en/actions/reference/encrypted-secrets) az Azure-beli hitelesítéshez. A titkos kulcsot a munkafolyamaton belül érheti el. 

# <a name="publish-profile"></a>[Profil közzététele](#tab/publish-profile)

A közzétételi profil egy alkalmazás szintű hitelesítő adat. A közzétételi profil beállítása GitHub-titokként. 

1. Nyissa meg az App Service-t a Azure Portal. 

1. Az **Áttekintés** lapon válassza a **közzétételi profil beolvasása** elemet.

    > [!NOTE]
    > Október 2020 a linuxos webalkalmazások `WEBSITE_WEBDEPLOY_USE_SCM` számára a `true` **Fájl letöltése előtt** be kell állítani az Alkalmazásbeállítások beállítást. Ez a követelmény a jövőben el lesz távolítva. Lásd: [app Service alkalmazás konfigurálása a Azure Portalban](./configure-common.md), hogy megtudja, hogyan konfigurálhatja a Common Web App-beállításokat.  

1. Mentse a letöltött fájlt. A fájl tartalmát a GitHub-titok létrehozásához fogja használni.

# <a name="service-principal"></a>[Szolgáltatásnév](#tab/service-principal)

Az [Azure CLI](/cli/azure/)-ben létrehozhat egy [egyszerű szolgáltatást](../active-directory/develop/app-objects-and-service-principals.md#service-principal-object) az az [ad SP Create-for-RBAC](/cli/azure/ad/sp#az-ad-sp-create-for-rbac) parancs használatával. Futtassa ezt a parancsot [Azure Cloud Shell](https://shell.azure.com/) a Azure Portalban, vagy kattintson a **TRY IT (kipróbálás** ) gombra.

```azurecli-interactive
az ad sp create-for-rbac --name "myApp" --role contributor \
                            --scopes /subscriptions/<subscription-id>/resourceGroups/<group-name>/providers/Microsoft.Web/sites/<app-name> \
                            --sdk-auth
```

A példában cserélje le a helyőrzőket az előfizetés-AZONOSÍTÓra, az erőforráscsoport nevére és az alkalmazás nevére. A kimenet egy JSON-objektum, amelynek a szerepkör-hozzárendelési hitelesítő adatai biztosítják a hozzáférést a App Service alkalmazáshoz. A JSON-objektum másolása később.

```output 
  {
    "clientId": "<GUID>",
    "clientSecret": "<GUID>",
    "subscriptionId": "<GUID>",
    "tenantId": "<GUID>",
    (...)
  }
```

> [!IMPORTANT]
> Mindig jó gyakorlat a minimális hozzáférés megadására. Az előző példában szereplő hatókör az adott App Service alkalmazásra korlátozódik, nem a teljes erőforráscsoporthoz.

---
## <a name="configure-the-github-secret-for-authentication"></a>A GitHub-titok konfigurálása a hitelesítéshez

# <a name="publish-profile"></a>[Profil közzététele](#tab/publish-profile)

A [githubon](https://github.com/)tallózzon a tárházban, válassza a **beállítások > titkok > új titok hozzáadása** lehetőséget.

Az [alkalmazás szintű hitelesítő adatok](#generate-deployment-credentials)használatához illessze be a letöltött közzétételi profil tartalmát a titkos kulcs érték mezőjébe. Nevezze el a titkot `AZURE_WEBAPP_PUBLISH_PROFILE` .

A GitHub-munkafolyamatok konfigurálásakor használja az `AZURE_WEBAPP_PUBLISH_PROFILE` Azure-webalkalmazás üzembe helyezése műveletet. Például:
    
```yaml
- uses: azure/webapps-deploy@v2
  with:
    publish-profile: ${{ secrets.AZURE_WEBAPP_PUBLISH_PROFILE }}
```

# <a name="service-principal"></a>[Szolgáltatásnév](#tab/service-principal)

A [githubon](https://github.com/)tallózzon a tárházban, válassza a **beállítások > titkok > új titok hozzáadása** lehetőséget.

[Felhasználói szintű hitelesítő adatok](#generate-deployment-credentials)használatához illessze be a teljes JSON-kimenetet az Azure CLI-parancsból a titkos kulcs érték mezőjébe. Adja meg a titkot a nevet, például: `AZURE_CREDENTIALS` .

Amikor később konfigurálja a munkafolyamat-fájlt, az `creds` Azure bejelentkezési művelethez tartozó titkos kulcsot használja. Például:

```yaml
- uses: azure/login@v1
  with:
    creds: ${{ secrets.AZURE_CREDENTIALS }}
```

---

## <a name="configure-github-secrets-for-your-registry"></a>GitHub-titkok konfigurálása a beállításjegyzékhez

Adja meg a Docker bejelentkezési művelettel használni kívánt titkokat. A dokumentumban szereplő példa Azure Container Registryt használ a tároló-beállításjegyzékhez. 

1. Nyissa meg a tárolót a Azure Portal vagy a Docker-ben, és másolja a felhasználónevet és a jelszót. A Azure Container Registry felhasználónevét és jelszavát a beállításjegyzék **Beállítások**  >  **hozzáférési kulcsok** területén található Azure Portalban találja. 

2. Adjon meg egy új titkot a nevű beállításjegyzékbeli felhasználónévhez `REGISTRY_USERNAME` . 

3. Adjon meg egy új titkot a nevű beállításjegyzék-jelszóhoz `REGISTRY_PASSWORD` . 

## <a name="build-the-container-image"></a>A tároló rendszerképének összeállítása

Az alábbi példa egy Node.JS Docker-rendszerképet felépítő munkafolyamat egy részét mutatja be. A [Docker-bejelentkezés](https://github.com/azure/docker-login) használatával jelentkezzen be egy privát tároló-beállításjegyzékbe. Ez a példa Azure Container Registry használ, de ugyanez a művelet más beállításjegyzékek esetében is működik. 


```yaml
name: Linux Container Node Workflow

on: [push]

jobs:
  build:
    runs-on: ubuntu-latest

    steps:
    - uses: actions/checkout@v2
    - uses: azure/docker-login@v1
      with:
        login-server: mycontainer.azurecr.io
        username: ${{ secrets.REGISTRY_USERNAME }}
        password: ${{ secrets.REGISTRY_PASSWORD }}
    - run: |
        docker build . -t mycontainer.azurecr.io/myapp:${{ github.sha }}
        docker push mycontainer.azurecr.io/myapp:${{ github.sha }}     
```

A [Docker-bejelentkezés](https://github.com/azure/docker-login) használatával egyszerre több tároló-beállításjegyzékbe is bejelentkezhet. Ez a példa két új GitHub-titkot tartalmaz a docker.io-vel történő hitelesítéshez. A példa azt feltételezi, hogy a beállításjegyzék legfelső szintjén van egy Docker. 

```yml
name: Linux Container Node Workflow

on: [push]

jobs:
  build:
    runs-on: ubuntu-latest

    steps:
    - uses: actions/checkout@v2
    - uses: azure/docker-login@v1
      with:
        login-server: mycontainer.azurecr.io
        username: ${{ secrets.REGISTRY_USERNAME }}
        password: ${{ secrets.REGISTRY_PASSWORD }}
    - uses: azure/docker-login@v1
      with:
        login-server: index.docker.io
        username: ${{ secrets.DOCKERIO_USERNAME }}
        password: ${{ secrets.DOCKERIO_PASSWORD }}
    - run: |
        docker build . -t mycontainer.azurecr.io/myapp:${{ github.sha }}
        docker push mycontainer.azurecr.io/myapp:${{ github.sha }}     
```

## <a name="deploy-to-an-app-service-container"></a>Üzembe helyezés App Service tárolón

Ha a lemezképet a App Serviceban lévő egyéni tárolóba szeretné telepíteni, használja a `azure/webapps-deploy@v2` műveletet. Ehhez a művelethez hét paraméter tartozik:

| **Paraméter**  | **Magyarázat**  |
|---------|---------|
| **alkalmazás neve** | Szükséges A App Service alkalmazás neve | 
| **közzétételi profil** | Választható A következőkre vonatkozik: Web Apps (Windows és Linux) és Web App containers (Linux). A multi-Container forgatókönyv nem támogatott. A profil ( \* . publishsettings) fájl tartalmának közzététele a web Deploy Secrets szolgáltatásban | 
| **tárolóhely neve** | Választható Adja meg az üzemi tárolóhelytől eltérő meglévő tárolóhelyet |
| **csomag** | Választható Csak a webalkalmazásra vonatkozik: csomag vagy mappa elérési útja. \*. zip, \* . War, \* . jar vagy egy telepítendő mappa |
| **képek** | Szükséges Csak a webalkalmazás-tárolók esetében érvényes: adja meg a teljes tároló rendszerkép (ek) nevét. Például: "myregistry.azurecr.io/nginx:latest" vagy "Python: 3.7.2-Alpine/". Többtárolós alkalmazások esetén több tároló-rendszerkép is megadható (több sorba tagolt) |
| **konfigurációs fájl** | Választható Csak a webalkalmazás-tárolók esetében érvényes: a Docker-Compose fájl elérési útja. Teljes elérési útnak kell lennie, vagy az alapértelmezett munkakönyvtárhoz viszonyítva kell lennie. Többtárolós alkalmazások esetén szükséges. |
| **indítás – parancs** | Választható Adja meg az indítási parancsot. Pl.: DotNet-Futtatás vagy DotNet filename.dll |

# <a name="publish-profile"></a>[Profil közzététele](#tab/publish-profile)

```yaml
name: Linux Container Node Workflow

on: [push]

jobs:
  build:
    runs-on: ubuntu-latest

    steps:
    - uses: actions/checkout@v2

    - uses: azure/docker-login@v1
      with:
        login-server: mycontainer.azurecr.io
        username: ${{ secrets.REGISTRY_USERNAME }}
        password: ${{ secrets.REGISTRY_PASSWORD }}

    - run: |
        docker build . -t mycontainer.azurecr.io/myapp:${{ github.sha }}
        docker push mycontainer.azurecr.io/myapp:${{ github.sha }}     

    - uses: azure/webapps-deploy@v2
      with:
        app-name: 'myapp'
        publish-profile: ${{ secrets.AZURE_WEBAPP_PUBLISH_PROFILE }}
        images: 'mycontainer.azurecr.io/myapp:${{ github.sha }}'
```
# <a name="service-principal"></a>[Szolgáltatásnév](#tab/service-principal)

```yaml
on: [push]

name: Linux_Container_Node_Workflow

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
    
    - uses: azure/docker-login@v1
      with:
        login-server: mycontainer.azurecr.io
        username: ${{ secrets.REGISTRY_USERNAME }}
        password: ${{ secrets.REGISTRY_PASSWORD }}
    - run: |
        docker build . -t mycontainer.azurecr.io/myapp:${{ github.sha }}
        docker push mycontainer.azurecr.io/myapp:${{ github.sha }}     
      
    - uses: azure/webapps-deploy@v2
      with:
        app-name: 'myapp'
        images: 'mycontainer.azurecr.io/myapp:${{ github.sha }}'
    
    - name: Azure logout
      run: |
        az logout
```

---

## <a name="next-steps"></a>Következő lépések

Megtalálhatja a GitHubon különböző adattárakba csoportosított műveleteit, amelyek mindegyike dokumentációt és példákat tartalmaz, amelyek segítséget nyújtanak a GitHub használatához a CI/CD-hez, és az alkalmazások üzembe helyezését az Azure-ban.

- [Az Azure-ba telepítendő műveletek munkafolyamatai](https://github.com/Azure/actions-workflow-samples)

- [Azure-bejelentkezés](https://github.com/Azure/login)

- [Azure WebApp](https://github.com/Azure/webapps-deploy)

- [Docker-bejelentkezés/kijelentkezés](https://github.com/Azure/docker-login)

- [Munkafolyamatokat kiváltó események](https://docs.github.com/en/actions/reference/events-that-trigger-workflows)

- [K8s üzembe helyezése](https://github.com/Azure/k8s-deploy)

- [Kezdő munkafolyamatok](https://github.com/actions/starter-workflows)