---
title: CI/CD konfigurálása GitHub-műveletekkel
description: Megtudhatja, hogyan helyezheti üzembe a kódot Azure App Service egy CI/CD-folyamatból a GitHub-műveletekkel. Testreszabhatja a felépítési feladatokat, és összetett központi telepítéseket hajthat végre.
ms.devlang: na
ms.topic: article
ms.date: 09/14/2020
ms.author: jafreebe
ms.reviewer: ushan
ms.custom: devx-track-python, github-actions-azure, devx-track-azurecli
ms.openlocfilehash: 59eb56dd188edf258c3631cde957c0864454ad76
ms.sourcegitcommit: d63f15674f74d908f4017176f8eddf0283f3fac8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/07/2021
ms.locfileid: "106582238"
---
# <a name="deploy-to-app-service-using-github-actions"></a>Üzembe helyezés az App Service-ben a GitHub Actions segítségével

Ismerkedjen meg a [GitHub-műveletekkel](https://docs.github.com/en/actions/learn-github-actions) a munkafolyamatok automatizálásához és a githubról [Azure app Service](overview.md) üzembe helyezéséhez. 

## <a name="prerequisites"></a>Előfeltételek 

- Aktív előfizetéssel rendelkező Azure-fiók. [Hozzon létre egy fiókot ingyenesen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Egy GitHub-fiók. Ha még nem rendelkezik ilyennel, regisztráljon [ingyenesen](https://github.com/join).  
- Egy működő Azure App Service alkalmazás. 
    - .NET: [ASP.net Core Webalkalmazás létrehozása az Azure-ban](quickstart-dotnetcore.md)
    - ASP.NET: [ASP.NET-keretrendszer Webalkalmazás létrehozása az Azure-ban](quickstart-dotnet-framework.md)
    - JavaScript: [Node.js Webalkalmazás létrehozása Azure app Service](quickstart-nodejs.md)  
    - Java: [Java-alkalmazás létrehozása Azure app Service](quickstart-java.md)
    - Python: [Python-alkalmazás létrehozása Azure app Service](quickstart-python.md)

## <a name="workflow-file-overview"></a>A munkafolyamat-fájl áttekintése

A munkafolyamatot egy YAML-(. YML) fájl határozza meg a `/.github/workflows/` tárház elérési útjában. Ez a definíció a munkafolyamatot alkotó különböző lépéseket és paramétereket tartalmazza.

A fájl három szakaszt tartalmaz:

|Section  |Feladatok  |
|---------|---------|
|**Hitelesítés** | 1. Adjon meg egy szolgáltatásnevet vagy egy közzétételi profilt. <br /> 2. hozzon létre egy GitHub-titkot. |
|**Létrehozás** | 1. Állítsa be a környezetet. <br /> 2. hozza létre a webalkalmazást. |
|**Telepítés** | 1. Telepítse a webalkalmazást. |

## <a name="use-the-deployment-center"></a>A központi telepítési központ használata

Gyorsan megkezdheti a GitHub-műveleteket a App Service Deployment Center használatával. Ez automatikusan létrehozza a munkafolyamat-fájlt az alkalmazás verem alapján, és véglegesíti azt a GitHub-tárházban a megfelelő könyvtárban.

1. Navigáljon a webapphoz a Azure Portal
1. A bal oldalon kattintson a **központi telepítési központ** elemre.
1. A **folyamatos üzembe helyezés (CI/CD)** alatt válassza a **GitHub** elemet.
1. Ezután válassza a **GitHub-műveletek** elemet.
1. A legördülő lista használatával kiválaszthatja a GitHub-tárházat, az ág és az alkalmazás veremét
    - Ha a kiválasztott ág védett, továbbra is hozzáadhatja a munkafolyamat-fájlt. A folytatás előtt tekintse át a fiókirodák védelmét.
1. Az utolsó képernyőn áttekintheti a beállításokat, és megtekintheti a tárházban véglegesíteni kívánt munkafolyamat-fájlt. Ha a beállítások helyesek, kattintson a **Befejezés** gombra.

Ezzel véglegesíti a munkafolyamat-fájlt a tárházban. Az alkalmazás létrehozásához és üzembe helyezéséhez szükséges munkafolyamat azonnal elindul.

## <a name="set-up-a-workflow-manually"></a>Munkafolyamat manuális beállítása

A munkafolyamatokat a központi telepítési központ használata nélkül is üzembe helyezheti. Ehhez először a központi telepítési hitelesítő adatokat kell létrehoznia. 

## <a name="generate-deployment-credentials"></a>Központi telepítési hitelesítő adatok előállítása

Az Azure App Services a GitHub-műveletekhez való hitelesítésének ajánlott módja a közzétételi profil. A hitelesítést egy egyszerű szolgáltatással is elvégezheti, de a folyamat további lépéseket igényel. 

Mentse a közzétételi profil hitelesítő adatait vagy egyszerű szolgáltatásnevet [GitHub-titokként](https://docs.github.com/en/actions/reference/encrypted-secrets) az Azure-beli hitelesítéshez. A titkos kulcsot a munkafolyamaton belül érheti el. 

# <a name="publish-profile"></a>[Profil közzététele](#tab/applevel)

A közzétételi profil egy alkalmazás szintű hitelesítő adat. A közzétételi profil beállítása GitHub-titokként. 

1. Nyissa meg az App Service-t a Azure Portal. 

1. Az **Áttekintés** lapon válassza a **közzétételi profil beolvasása** elemet.

1. Mentse a letöltött fájlt. A fájl tartalmát a GitHub-titok létrehozásához fogja használni.

> [!NOTE]
> Október 2020 a linuxos webalkalmazások `WEBSITE_WEBDEPLOY_USE_SCM` számára a `true` **közzétételi profil letöltése előtt** be kell állítani az Alkalmazásbeállítások beállítást. Ez a követelmény a jövőben el lesz távolítva.

# <a name="service-principal"></a>[Szolgáltatásnév](#tab/userlevel)

Az [Azure CLI](/cli/azure/)-ben létrehozhat egy [egyszerű szolgáltatást](../active-directory/develop/app-objects-and-service-principals.md#service-principal-object) az az [ad SP Create-for-RBAC](/cli/azure/ad/sp#az-ad-sp-create-for-rbac) parancs használatával. Futtassa ezt a parancsot [Azure Cloud Shell](https://shell.azure.com/) a Azure Portalban, vagy kattintson a **TRY IT (kipróbálás** ) gombra.

```azurecli-interactive
az ad sp create-for-rbac --name "myApp" --role contributor \
                            --scopes /subscriptions/<subscription-id>/resourceGroups/<group-name>/providers/Microsoft.Web/sites/<app-name> \
                            --sdk-auth
```

A fenti példában cserélje le a helyőrzőket az előfizetés-AZONOSÍTÓra, az erőforráscsoport nevére és az alkalmazás nevére. A kimenet egy JSON-objektum, amely a szerepkör-hozzárendelés hitelesítő adatait tartalmazza, amelyek hozzáférést biztosítanak a App Service alkalmazáshoz az alábbihoz hasonló módon. A JSON-objektum másolása később.

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

## <a name="configure-the-github-secret"></a>A GitHub-titok konfigurálása


# <a name="publish-profile"></a>[Profil közzététele](#tab/applevel)

A [githubon](https://github.com/)tallózzon a tárházban, válassza a **beállítások > titkok > új titok hozzáadása** lehetőséget.

Az [alkalmazás szintű hitelesítő adatok](#generate-deployment-credentials)használatához illessze be a letöltött közzétételi profil tartalmát a titkos kulcs érték mezőjébe. Nevezze el a titkot `AZURE_WEBAPP_PUBLISH_PROFILE` .

A GitHub-munkafolyamatok konfigurálásakor használja az `AZURE_WEBAPP_PUBLISH_PROFILE` Azure-webalkalmazás üzembe helyezése műveletet. Például:
    
```yaml
- uses: azure/webapps-deploy@v2
  with:
    publish-profile: ${{ secrets.AZURE_WEBAPP_PUBLISH_PROFILE }}
```

# <a name="service-principal"></a>[Szolgáltatásnév](#tab/userlevel)

A [githubon](https://github.com/)tallózzon a tárházban, válassza a **beállítások > titkok > új titok hozzáadása** lehetőséget.

[Felhasználói szintű hitelesítő adatok](#generate-deployment-credentials)használatához illessze be a teljes JSON-kimenetet az Azure CLI-parancsból a titkos kulcs érték mezőjébe. Adja meg a titkot a nevet `AZURE_CREDENTIALS` .

Amikor később konfigurálja a munkafolyamat-fájlt, az `creds` Azure bejelentkezési művelethez tartozó titkos kulcsot használja. Például:

```yaml
- uses: azure/login@v1
  with:
    creds: ${{ secrets.AZURE_CREDENTIALS }}
```

---

## <a name="set-up-the-environment"></a>A környezet beállítása

A környezet beállítása a telepítési műveletek egyikével végezhető el.

|**Nyelv**  |**Telepítési művelet**  |
|---------|---------|
|**.NET**     | `actions/setup-dotnet` |
|**ASP.NET**     | `actions/setup-dotnet` |
|**Java**     | `actions/setup-java` |
|**JavaScript** | `actions/setup-node` |
|**Python**     | `actions/setup-python` |

Az alábbi példák bemutatják, hogyan állíthatja be a környezetet a különböző támogatott nyelvekhez:

**.NET**

```yaml
    - name: Setup Dotnet 3.3.x
      uses: actions/setup-dotnet@v1
      with:
        dotnet-version: '3.3.x'
```

**ASP.NET**

```yaml
    - name: Install Nuget
      uses: nuget/setup-nuget@v1
      with:
        nuget-version: ${{ env.NUGET_VERSION}}
```

**Java**

```yaml
    - name: Setup Java 1.8.x
      uses: actions/setup-java@v1
      with:
        # If your pom.xml <maven.compiler.source> version is not in 1.8.x,
        # change the Java version to match the version in pom.xml <maven.compiler.source>
        java-version: '1.8.x'
```

**JavaScript**

```yaml
env:
  NODE_VERSION: '14.x'                # set this to the node version to use

jobs:
  build-and-deploy:
    name: Build and Deploy
    runs-on: ubuntu-latest
    steps:
    - uses: actions/checkout@main
    - name: Use Node.js ${{ env.NODE_VERSION }}
      uses: actions/setup-node@v1
      with:
        node-version: ${{ env.NODE_VERSION }}
```
**Python**

```yaml
    - name: Setup Python 3.x 
      uses: actions/setup-python@v1
      with:
        python-version: 3.x
```

## <a name="build-the-web-app"></a>A webalkalmazás összeállítása

A webalkalmazások létrehozásának folyamata, és a nyelvtől függően Azure App Service módosításokat hajthat végre. 

Az alábbi példák a webalkalmazást a különböző támogatott nyelveken felépítő munkafolyamat részét mutatják be.

Minden nyelvnél beállíthatja a webalkalmazás gyökérkönyvtárát a következővel: `working-directory` . 

**.NET**

A környezeti változó `AZURE_WEBAPP_PACKAGE_PATH` Beállítja a webalkalmazás-projekt elérési útját. 

```yaml
- name: dotnet build and publish
  run: |
    dotnet restore
    dotnet build --configuration Release
    dotnet publish -c Release -o '${{ env.AZURE_WEBAPP_PACKAGE_PATH }}/myapp' 
```
**ASP.NET**

Visszaállíthatja a NuGet-függőségeket, és futtathatja az MSBuild-t `run` . 

```yaml
- name: NuGet to restore dependencies as well as project-specific tools that are specified in the project file
  run: nuget restore

- name: Add msbuild to PATH
  uses: microsoft/setup-msbuild@v1.0.0

- name: Run msbuild
  run: msbuild .\SampleWebApplication.sln
```

**Java**

```yaml
- name: Build with Maven
  run: mvn package --file pom.xml
```

**JavaScript**

Node.js esetében beállíthatja `working-directory` vagy megváltoztathatja a NPM könyvtárát a alkalmazásban `pushd` . 

```yaml
- name: npm install, build, and test
  run: |
    npm install
    npm run build --if-present
    npm run test --if-present
  working-directory: my-app-folder # set to the folder with your app if it is not the root directory
```

**Python**

```yaml
- name: Install dependencies
  run: |
    python -m pip install --upgrade pip
    pip install -r requirements.txt
```


## <a name="deploy-to-app-service"></a>Üzembe helyezés az App Service-ben

A kód App Service alkalmazásba való üzembe helyezéséhez használja a `azure/webapps-deploy@v2` műveletet. A műveletnek négy paramétere van:

| **Paraméter**  | **Magyarázat**  |
|---------|---------|
| **alkalmazás neve** | Szükséges A App Service alkalmazás neve | 
| **közzétételi profil** | Választható Profil fájl tartalmának közzététele a web Deploy Secrets szolgáltatásban |
| **csomag** | Választható Csomag vagy mappa elérési útja. Az elérési út tartalmazhatja a *. zip, *. War, *. jar vagy a telepítendő mappát |
| **tárolóhely neve** | Választható Adja meg az üzemi [tárolóhelytől](deploy-staging-slots.md) eltérő meglévő tárolóhelyet |


# <a name="publish-profile"></a>[Profil közzététele](#tab/applevel)

### <a name="net-core"></a>.NET Core

.NET Core-alkalmazás létrehozása és üzembe helyezése az Azure-ban Azure közzétételi profil használatával. A `publish-profile` bemenet a `AZURE_WEBAPP_PUBLISH_PROFILE` korábban létrehozott titokra hivatkozik.

```yaml
name: .NET Core CI

on: [push]

env:
  AZURE_WEBAPP_NAME: my-app-name    # set this to your application's name
  AZURE_WEBAPP_PACKAGE_PATH: '.'      # set this to the path to your web app project, defaults to the repository root
  DOTNET_VERSION: '3.1.x'           # set this to the dot net version to use

jobs:
  build:
    runs-on: ubuntu-latest

    steps:
      # Checkout the repo
      - uses: actions/checkout@main
      
      # Setup .NET Core SDK
      - name: Setup .NET Core
        uses: actions/setup-dotnet@v1
        with:
          dotnet-version: ${{ env.DOTNET_VERSION }} 
      
      # Run dotnet build and publish
      - name: dotnet build and publish
        run: |
          dotnet restore
          dotnet build --configuration Release
          dotnet publish -c Release -o '${{ env.AZURE_WEBAPP_PACKAGE_PATH }}/myapp' 
          
      # Deploy to Azure Web apps
      - name: 'Run Azure webapp deploy action using publish profile credentials'
        uses: azure/webapps-deploy@v2
        with: 
          app-name: ${{ env.AZURE_WEBAPP_NAME }} # Replace with your app name
          publish-profile: ${{ secrets.AZURE_WEBAPP_PUBLISH_PROFILE  }} # Define secret variable in repository settings as per action documentation
          package: '${{ env.AZURE_WEBAPP_PACKAGE_PATH }}/myapp'
```

### <a name="aspnet"></a>ASP.NET

Hozzon létre és helyezzen üzembe egy ASP.NET MVC-alkalmazást, amely NuGet és `publish-profile` hitelesítést használ. 


```yaml
name: Deploy ASP.NET MVC App deploy to Azure Web App

on: [push]

env:
  AZURE_WEBAPP_NAME: my-app    # set this to your application's name
  AZURE_WEBAPP_PACKAGE_PATH: '.'      # set this to the path to your web app project, defaults to the repository root
  NUGET_VERSION: '5.3.x'           # set this to the dot net version to use

jobs:
  build-and-deploy:
    runs-on: windows-latest
    steps:

    - uses: actions/checkout@main  
    
    - name: Install Nuget
      uses: nuget/setup-nuget@v1
      with:
        nuget-version: ${{ env.NUGET_VERSION}}
    - name: NuGet to restore dependencies as well as project-specific tools that are specified in the project file
      run: nuget restore
  
    - name: Add msbuild to PATH
      uses: microsoft/setup-msbuild@v1.0.0

    - name: Run MSBuild
      run: msbuild .\SampleWebApplication.sln
       
    - name: 'Run Azure webapp deploy action using publish profile credentials'
      uses: azure/webapps-deploy@v2
      with: 
        app-name: ${{ env.AZURE_WEBAPP_NAME }} # Replace with your app name
        publish-profile: ${{ secrets.AZURE_WEBAPP_PUBLISH_PROFILE  }} # Define secret variable in repository settings as per action documentation
        package: '${{ env.AZURE_WEBAPP_PACKAGE_PATH }}/SampleWebApplication/'
```

### <a name="java"></a>Java

Hozzon létre és helyezzen üzembe egy Java Spring-alkalmazást az Azure-ban egy Azure közzétételi profil használatával. A `publish-profile` bemenet a `AZURE_WEBAPP_PUBLISH_PROFILE` korábban létrehozott titokra hivatkozik.

```yaml
name: Java CI with Maven

on: [push]

jobs:
  build:

    runs-on: ubuntu-latest

    steps:
    - uses: actions/checkout@v2
    - name: Set up JDK 1.8
      uses: actions/setup-java@v1
      with:
        java-version: 1.8
    - name: Build with Maven
      run: mvn -B package --file pom.xml
      working-directory: my-app-path
    - name: Azure WebApp
      uses: Azure/webapps-deploy@v2
      with:
        app-name: my-app-name
        publish-profile: ${{ secrets.AZURE_WEBAPP_PUBLISH_PROFILE }}
        package: my/target/*.jar
```

Ha a helyett egy helyet szeretne telepíteni `war` `jar` , módosítsa az `package` értéket. 


```yaml
    - name: Azure WebApp
      uses: Azure/webapps-deploy@v2
      with:
        app-name: my-app-name
        publish-profile: ${{ secrets.AZURE_WEBAPP_PUBLISH_PROFILE }}
        package: my/target/*.war
```

### <a name="javascript"></a>JavaScript 

Node.js-alkalmazás létrehozása és üzembe helyezése az Azure-ban az alkalmazás közzétételi profiljának használatával. A `publish-profile` bemenet a `AZURE_WEBAPP_PUBLISH_PROFILE` korábban létrehozott titokra hivatkozik.

```yaml
# File: .github/workflows/workflow.yml
name: JavaScript CI

on: [push]

env:
  AZURE_WEBAPP_NAME: my-app-name   # set this to your application's name
  AZURE_WEBAPP_PACKAGE_PATH: 'my-app-path'      # set this to the path to your web app project, defaults to the repository root
  NODE_VERSION: '14.x'                # set this to the node version to use

jobs:
  build-and-deploy:
    name: Build and Deploy
    runs-on: ubuntu-latest
    steps:
    - uses: actions/checkout@main
    - name: Use Node.js ${{ env.NODE_VERSION }}
      uses: actions/setup-node@v1
      with:
        node-version: ${{ env.NODE_VERSION }}
    - name: npm install, build, and test
      run: |
        # Build and test the project, then
        # deploy to Azure Web App.
        npm install
        npm run build --if-present
        npm run test --if-present
      working-directory: my-app-path
    - name: 'Deploy to Azure WebApp'
      uses: azure/webapps-deploy@v2
      with: 
        app-name: ${{ env.AZURE_WEBAPP_NAME }}
        publish-profile: ${{ secrets.AZURE_WEBAPP_PUBLISH_PROFILE }}
        package: ${{ env.AZURE_WEBAPP_PACKAGE_PATH }}
```

### <a name="python"></a>Python 

Python-alkalmazás létrehozása és üzembe helyezése az Azure-ban az alkalmazás közzétételi profiljának használatával. Figyelje meg, hogy a `publish-profile` bemenet a `AZURE_WEBAPP_PUBLISH_PROFILE` korábban létrehozott titokra hivatkozik.

```yaml
name: Python CI

on:
  [push]

env:
  AZURE_WEBAPP_NAME: my-web-app # set this to your application's name
  AZURE_WEBAPP_PACKAGE_PATH: '.' # set this to the path to your web app project, defaults to the repository root

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
    - uses: actions/checkout@v2
    - name: Set up Python 3.x
      uses: actions/setup-python@v2
      with:
        python-version: 3.x
    - name: Install dependencies
      run: |
        python -m pip install --upgrade pip
        pip install -r requirements.txt
    - name: Building web app
      uses: azure/appservice-build@v2
    - name: Deploy web App using GH Action azure/webapps-deploy
      uses: azure/webapps-deploy@v2
      with:
        app-name: ${{ env.AZURE_WEBAPP_NAME }}
        publish-profile: ${{ secrets.AZURE_WEBAPP_PUBLISH_PROFILE }}
        package: ${{ env.AZURE_WEBAPP_PACKAGE_PATH }}
```

# <a name="service-principal"></a>[Szolgáltatásnév](#tab/userlevel)

### <a name="net-core"></a>.NET Core 

.NET Core-alkalmazás létrehozása és üzembe helyezése az Azure-ban egy Azure-szolgáltatásnév használatával. Figyelje meg, hogy a `creds` bemenet a `AZURE_CREDENTIALS` korábban létrehozott titokra hivatkozik.


```yaml
name: .NET Core

on: [push]

env:
  AZURE_WEBAPP_NAME: my-app    # set this to your application's name
  AZURE_WEBAPP_PACKAGE_PATH: '.'      # set this to the path to your web app project, defaults to the repository root
  DOTNET_VERSION: '3.1.x'           # set this to the dot net version to use

jobs:
  build:
    runs-on: ubuntu-latest

    steps:
      # Checkout the repo
      - uses: actions/checkout@main
      - uses: azure/login@v1
        with:
          creds: ${{ secrets.AZURE_CREDENTIALS }}

      
      # Setup .NET Core SDK
      - name: Setup .NET Core
        uses: actions/setup-dotnet@v1
        with:
          dotnet-version: ${{ env.DOTNET_VERSION }} 
      
      # Run dotnet build and publish
      - name: dotnet build and publish
        run: |
          dotnet restore
          dotnet build --configuration Release
          dotnet publish -c Release -o '${{ env.AZURE_WEBAPP_PACKAGE_PATH }}/myapp' 
          
      # Deploy to Azure Web apps
      - name: 'Run Azure webapp deploy action using publish profile credentials'
        uses: azure/webapps-deploy@v2
        with: 
          app-name: ${{ env.AZURE_WEBAPP_NAME }} # Replace with your app name
          package: '${{ env.AZURE_WEBAPP_PACKAGE_PATH }}/myapp'
      
      - name: logout
        run: |
          az logout
```

### <a name="aspnet"></a>ASP.NET

ASP.NET MVC-alkalmazás létrehozása és üzembe helyezése az Azure-ban egy Azure-szolgáltatásnév használatával. Figyelje meg, hogy a `creds` bemenet a `AZURE_CREDENTIALS` korábban létrehozott titokra hivatkozik.

```yaml
name: Deploy ASP.NET MVC App deploy to Azure Web App

on: [push]

env:
  AZURE_WEBAPP_NAME: my-app    # set this to your application's name
  AZURE_WEBAPP_PACKAGE_PATH: '.'      # set this to the path to your web app project, defaults to the repository root
  NUGET_VERSION: '5.3.x'           # set this to the dot net version to use

jobs:
  build-and-deploy:
    runs-on: windows-latest
    steps:

    # checkout the repo
    - uses: actions/checkout@main
    
    - uses: azure/login@v1
      with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}

    - name: Install Nuget
      uses: nuget/setup-nuget@v1
      with:
        nuget-version: ${{ env.NUGET_VERSION}}
    - name: NuGet to restore dependencies as well as project-specific tools that are specified in the project file
      run: nuget restore
  
    - name: Add msbuild to PATH
      uses: microsoft/setup-msbuild@v1.0.0

    - name: Run MSBuild
      run: msbuild .\SampleWebApplication.sln
       
    - name: 'Run Azure webapp deploy action using publish profile credentials'
      uses: azure/webapps-deploy@v2
      with: 
        app-name: ${{ env.AZURE_WEBAPP_NAME }} # Replace with your app name
        package: '${{ env.AZURE_WEBAPP_PACKAGE_PATH }}/SampleWebApplication/'
  
    # Azure logout 
    - name: logout
      run: |
        az logout
```

### <a name="java"></a>Java 

Egy Java Spring-alkalmazás létrehozása és üzembe helyezése az Azure-ban egy Azure-szolgáltatásnév használatával. Figyelje meg, hogy a `creds` bemenet a `AZURE_CREDENTIALS` korábban létrehozott titokra hivatkozik.

```yaml
name: Java CI with Maven

on: [push]

jobs:
  build:

    runs-on: ubuntu-latest

    steps:
    - uses: actions/checkout@v2
    - uses: azure/login@v1
      with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}
    - name: Set up JDK 1.8
      uses: actions/setup-java@v1
      with:
        java-version: 1.8
    - name: Build with Maven
      run: mvn -B package --file pom.xml
      working-directory: complete
    - name: Azure WebApp
      uses: Azure/webapps-deploy@v2
      with:
        app-name: my-app-name
        package: my/target/*.jar

    # Azure logout 
    - name: logout
      run: |
        az logout
```

### <a name="javascript"></a>JavaScript 

Azure-szolgáltatásnév használatával Node.js alkalmazást hozhat létre és helyezhet üzembe az Azure-ban. Figyelje meg, hogy a `creds` bemenet a `AZURE_CREDENTIALS` korábban létrehozott titokra hivatkozik.

```yaml
name: JavaScript CI

on: [push]

name: Node.js

env:
  AZURE_WEBAPP_NAME: my-app   # set this to your application's name
  NODE_VERSION: '14.x'                # set this to the node version to use

jobs:
  build-and-deploy:
    runs-on: ubuntu-latest
    steps:
    # checkout the repo
    - name: 'Checkout GitHub Action' 
      uses: actions/checkout@main
   
    - uses: azure/login@v1
      with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}
        
    - name: Setup Node ${{ env.NODE_VERSION }}
      uses: actions/setup-node@v1
      with:
        node-version: ${{ env.NODE_VERSION }}
    
    - name: 'npm install, build, and test'
      run: |
        npm install
        npm run build --if-present
        npm run test --if-present
      working-directory:  my-app-path
               
    # deploy web app using Azure credentials
    - uses: azure/webapps-deploy@v2
      with:
        app-name: ${{ env.AZURE_WEBAPP_NAME }}
        package: ${{ env.AZURE_WEBAPP_PACKAGE_PATH }}

    # Azure logout 
    - name: logout
      run: |
        az logout
```

### <a name="python"></a>Python 

Egy Python-alkalmazás létrehozása és üzembe helyezése az Azure-ban egy Azure-szolgáltatásnév használatával. Figyelje meg, hogy a `creds` bemenet a `AZURE_CREDENTIALS` korábban létrehozott titokra hivatkozik.

```yaml
name: Python application

on:
  [push]

env:
  AZURE_WEBAPP_NAME: my-app # set this to your application's name
  AZURE_WEBAPP_PACKAGE_PATH: '.' # set this to the path to your web app project, defaults to the repository root

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
    - uses: actions/checkout@v2
    
    - uses: azure/login@v1
      with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}

    - name: Set up Python 3.x
      uses: actions/setup-python@v2
      with:
        python-version: 3.x
    - name: Install dependencies
      run: |
        python -m pip install --upgrade pip
        pip install -r requirements.txt
    - name: Deploy web App using GH Action azure/webapps-deploy
      uses: azure/webapps-deploy@v2
      with:
        app-name: ${{ env.AZURE_WEBAPP_NAME }}
        package: ${{ env.AZURE_WEBAPP_PACKAGE_PATH }}
    - name: logout
      run: |
        az logout
```


---

## <a name="next-steps"></a>Következő lépések

Megtalálhatja a GitHubon különböző adattárakba csoportosított műveleteit, amelyek mindegyike dokumentációt és példákat tartalmaz, amelyek segítséget nyújtanak a GitHub használatához a CI/CD-hez, és az alkalmazások üzembe helyezését az Azure-ban.

- [Az Azure-ba telepítendő műveletek munkafolyamatai](https://github.com/Azure/actions-workflow-samples)

- [Azure-bejelentkezés](https://github.com/Azure/login)

- [Azure WebApp](https://github.com/Azure/webapps-deploy)

- [Azure WebApp tárolók számára](https://github.com/Azure/webapps-container-deploy)

- [Docker-bejelentkezés/kijelentkezés](https://github.com/Azure/docker-login)

- [Munkafolyamatokat kiváltó események](https://docs.github.com/en/actions/reference/events-that-trigger-workflows)

- [K8s üzembe helyezése](https://github.com/Azure/k8s-deploy)

- [Kezdő munkafolyamatok](https://github.com/actions/starter-workflows)
