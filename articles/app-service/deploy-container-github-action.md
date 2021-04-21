---
title: Egyéni CI/CD tároló GitHub Actions
description: Megtudhatja, hogyan helyezheti GitHub Actions egyéni Linux-tárolót a CI/CD-App Service való üzembe helyezéséhez.
ms.devlang: na
ms.topic: article
ms.date: 12/04/2020
ms.author: jafreebe
ms.reviewer: ushan
ms.custom: github-actions-azure
ms.openlocfilehash: bf9fba9142de82c6e8518198d54b5e74f1807838
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107789428"
---
# <a name="deploy-a-custom-container-to-app-service-using-github-actions"></a>Egyéni tároló üzembe helyezése App Service GitHub Actions

[GitHub Actions](https://docs.github.com/en/actions) biztosítja az automatizált szoftverfejlesztési munkafolyamatok felépítésének rugalmasságát. Az [Azure Web Deploy művelet használatával](https://github.com/Azure/webapps-deploy)automatizálhatja az egyéni tárolók üzembe helyezésének munkafolyamatát a App Service üzembe GitHub Actions. [](overview.md)

A munkafolyamatot egy YAML- (.yml-) fájl definiálja az `/.github/workflows/` adattár elérési útján. Ez a definíció tartalmazza a munkafolyamatban található különböző lépéseket és paramétereket.

Egy tároló Azure App Service munkafolyamathoz a fájl három szakaszból áll:

|Section  |Feladatok  |
|---------|---------|
|**Hitelesítés** | 1. Szolgáltatásnév lekérése vagy közzétételi profil. <br /> 2. GitHub-titkos kód létrehozása. |
|**Létrehozás** | 1. Hozza létre a környezetet. <br /> 2. A tároló rendszerképének összeállítása. |
|**Telepítés** | 1. A tároló rendszerképének üzembe helyezése. |

## <a name="prerequisites"></a>Előfeltételek

- Aktív előfizetéssel rendelkezik egy Azure-fiók. [Ingyenes fiók létrehozása](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- Egy GitHub-fiók. Ha még nem tudja, regisztráljon [ingyenesen.](https://github.com/join) Az üzembe helyezéshez egy GitHub-adattárban kell kódot Azure App Service. 
- Egy működő tároló-beállításjegyzék és Azure App Service alkalmazás tárolókhoz. Ebben a példában a Azure Container Registry. Mindenképpen teljes körű üzembe helyezést kell végrehajtania Azure App Service tárolókhoz. A hagyományos webalkalmazásokkal ellentétben a tárolókhoz használt webalkalmazások nem alapértelmezett kezdőlapot stb. Tegye közzé a tárolót, hogy legyen egy működő példa.
    - [Megtudhatja, hogyan hozhat létre tárolóba helyezett Node.js-alkalmazást a Docker használatával, hogyan helyezheti le a tároló rendszerképét egy regisztrációs adatbázisba, majd hogyan helyezheti üzembe a Azure App Service](/azure/developer/javascript/tutorial-vscode-docker-node-01)
        
## <a name="generate-deployment-credentials"></a>Üzembe helyezési hitelesítő adatok létrehozása

Az Azure-fiókkal való App Services ajánlott GitHub Actions közzétételi profillal. Szolgáltatásnévvel is hitelesíthet, de a folyamathoz további lépésekre van szükség. 

Az Azure-ral való hitelesítéshez mentse a közzétételi profil hitelesítő adatait vagy szolgáltatásnévét [GitHub-titkos](https://docs.github.com/en/actions/reference/encrypted-secrets) kódként. A titkos elérést a munkafolyamaton belül fogja elérni. 

# <a name="publish-profile"></a>[Profil közzététele](#tab/publish-profile)

A közzétételi profil egy alkalmazásszintű hitelesítő adat. Állítsa be a közzétételi profilt GitHub titkos kódként. 

1. Az app service-t a Azure Portal. 

1. Az Áttekintés **lapon** válassza a **Közzétételi profil lekérte lehetőséget.**

    > [!NOTE]
    > 2020 októberében a Linux-webalkalmazások esetében a fájl letöltése előtt a következőre kell beállítani az `WEBSITE_WEBDEPLOY_USE_SCM` `true` **alkalmazásbeállítást:**. Ez a követelmény a jövőben el lesz távolítva. A [gyakori webalkalmazás App Service beállítások](./configure-common.md)konfigurálásról Azure Portal alkalmazás konfigurálása a Azure Portal-ban.  

1. Mentse a letöltött fájlt. A github titkos kód létrehozásához a fájl tartalmát fogja használni.

# <a name="service-principal"></a>[Szolgáltatásnév](#tab/service-principal)

Szolgáltatásnév az [](../active-directory/develop/app-objects-and-service-principals.md#service-principal-object) [az ad sp create-for-rbac paranccsal](/cli/azure/ad/sp#az_ad_sp_create_for_rbac) hozható létre az [Azure CLI-n.](/cli/azure/) Futtassa ezt a [Azure Cloud Shell](https://shell.azure.com/) a Azure Portal vagy a Próbálja **ki gombot** választva.

```azurecli-interactive
az ad sp create-for-rbac --name "myApp" --role contributor \
                            --scopes /subscriptions/<subscription-id>/resourceGroups/<group-name>/providers/Microsoft.Web/sites/<app-name> \
                            --sdk-auth
```

A példában cserélje le a helyőrzőket az előfizetés azonosítójára, az erőforráscsoport nevére és az alkalmazás nevére. A kimenet egy JSON-objektum a szerepkör-hozzárendelési hitelesítő adatokkal, amelyek hozzáférést biztosítanak a App Service alkalmazáshoz. Másolja ezt a JSON-objektumot későbbire.

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
> Mindig jó gyakorlat a minimális hozzáférés megadása. Az előző példában a hatókör nem a teljes erőforráscsoportra, App Service alkalmazásra van korlátozva.

---
## <a name="configure-the-github-secret-for-authentication"></a>A GitHub titkos kód konfigurálása hitelesítéshez

# <a name="publish-profile"></a>[Profil közzététele](#tab/publish-profile)

A [GitHubon](https://github.com/)keresse meg az adattárat, és válassza a Settings > Secrets > Add a new secret (Új titkos kód **hozzáadása) lehetőséget.**

Alkalmazásszintű [hitelesítő adatok](#generate-deployment-credentials)használata érdekében illessze be a letöltött közzétételi profilfájl tartalmát a titkos adatokat tároló érték mezőbe. A titkos nak nevezze el `AZURE_WEBAPP_PUBLISH_PROFILE` a következőt: .

A GitHub-munkafolyamat konfigurálásakor a et kell `AZURE_WEBAPP_PUBLISH_PROFILE` használnia az Azure-webalkalmazás üzembe helyezése műveletben. Például:
    
```yaml
- uses: azure/webapps-deploy@v2
  with:
    publish-profile: ${{ secrets.AZURE_WEBAPP_PUBLISH_PROFILE }}
```

# <a name="service-principal"></a>[Szolgáltatásnév](#tab/service-principal)

A [GitHubon](https://github.com/)keresse meg az adattárat, és válassza a Settings > Secrets > Add a new secret (Új titkos kód **hozzáadása) lehetőséget.**

Felhasználói szintű hitelesítő adatok használata esetén illessze be az Azure [CLI-parancs](#generate-deployment-credentials)teljes JSON-kimenetét a titkos adat értékmezőjére. Adja meg a titkos nak a következő nevet: `AZURE_CREDENTIALS` .

Amikor később konfigurálja a munkafolyamat-fájlt, a titkos adatokat kell használnia az `creds` Azure Login művelet bemenetéhez. Például:

```yaml
- uses: azure/login@v1
  with:
    creds: ${{ secrets.AZURE_CREDENTIALS }}
```

---

## <a name="configure-github-secrets-for-your-registry"></a>GitHub-titkos kulcsok konfigurálása a beállításjegyzékhez

Definiálja a Docker Bejelentkezési művelethez használni szükséges titkos adatokat. A dokumentumban található példa az Azure Container Registry tárolójegyzékhez használja. 

1. A tárolóhoz a Azure Portal Dockerben, és másolja ki a felhasználónevet és a jelszót. A felhasználónevet és Azure Container Registry a beállításjegyzék beállítások **Azure Portal kulcsok** alatt  >   találja. 

2. Határozzon meg egy új titkos kulcsot a beállításjegyzékbeli felhasználónévhez `REGISTRY_USERNAME` névvel. 

3. Definiálja a nevű új titkos kulcsot a beállításjegyzékbeli `REGISTRY_PASSWORD` jelszóhoz. 

## <a name="build-the-container-image"></a>A tároló rendszerképének összeállítása

Az alábbi példa a munkafolyamat egy részét mutatja be, amely egy Node.JS Docker-rendszerképet. A [Docker Login használatával](https://github.com/azure/docker-login) jelentkezzen be egy privát tárolójegyzékbe. Ez a példa Azure Container Registry, de ugyanez a művelet más beállításregisztrálókhoz is működik. 


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

A [Docker Login használatával egyidejűleg](https://github.com/azure/docker-login) több tárolóregisztrálóba is bejelentkezhet. Ebben a példában két új GitHub-titkos kód található a docker.io. A példa feltételezi, hogy a regisztrációs adatbázis gyökérszintje egy Dockerfile. 

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

## <a name="deploy-to-an-app-service-container"></a>Üzembe helyezés egy App Service tárolóban

A rendszerkép egyéni tárolóban való üzembe helyezéséhez használja App Service `azure/webapps-deploy@v2` műveletet. Ennek a műveletnek hét paramétere van:

| **Paraméter**  | **Magyarázat**  |
|---------|---------|
| **alkalmazás neve** | (Kötelező) A App Service neve | 
| **közzétételi profil** | (Nem kötelező) A Web Apps (Windows és Linux) és a Web App Containers (linux) tárolókra vonatkozik. A többtárolós forgatókönyv nem támogatott. Publish profile ( .publishsettings) file contents with Web Deploy secrets (Profil közzététele ( \* .publishsettings) fájl tartalma a Web Deploy titkos kulcsok segítségével | 
| **slot-name (tárolóhely neve)** | (Nem kötelező) Adjon meg egy meglévő, az éles tárolóhelyen kívül található tárolóhelyet |
| **Csomag** | (Nem kötelező) Csak a webalkalmazásra vonatkozik: csomag vagy mappa elérési útja. \*.zip, \* .war, \* .jar vagy üzembe helyezni kívánt mappa |
| **Képek** | (Kötelező) Csak webalkalmazás-tárolókra vonatkozik: Adja meg a tároló rendszerkép(ök) teljes nevét. Például "myregistry.azurecr.io/nginx:latest" vagy "python:3.7.2-alpine/". Többtárolós alkalmazások esetén több tároló rendszerképnevét is meg lehet adni (többsoros elválasztva) |
| **konfigurációs fájl** | (Nem kötelező) Csak a webalkalmazás-tárolókra vonatkozik: a Docker-Compose elérési útja. Teljes elérési útnak kell lennie, vagy az alapértelmezett munkakönyvtárhoz képest relatívnak kell lennie. Többtárolós alkalmazásokhoz szükséges. |
| **indítási parancs** | (Nem kötelező) Adja meg az indítási parancsot. Például: dotnet run vagy dotnet filename.dll |

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

A GitHubon található különböző adattárakba csoportosított műveleteket találhat, amelyek dokumentációt és példákat tartalmaznak, amelyek segítenek a GitHub CI/CD-hez való használatában és az alkalmazások Azure-ban való üzembe helyezésében.

- [Az Azure-ban üzembe helyező műveletek munkafolyamatai](https://github.com/Azure/actions-workflow-samples)

- [Azure-bejelentkezés](https://github.com/Azure/login)

- [Azure WebApp](https://github.com/Azure/webapps-deploy)

- [Docker– bejelentkezés/kijelentkezés](https://github.com/Azure/docker-login)

- [Munkafolyamatokat kiváltó események](https://docs.github.com/en/actions/reference/events-that-trigger-workflows)

- [K8s üzembe helyezése](https://github.com/Azure/k8s-deploy)

- [Kezdő munkafolyamatok](https://github.com/actions/starter-workflows)