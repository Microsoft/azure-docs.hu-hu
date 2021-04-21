---
title: CI/CD konfigurálása GitHub Actions
description: Megtudhatja, hogyan helyezheti üzembe a kódot Azure App Service CI-/CD-folyamatból a GitHub Actions. Testre szabhatja a buildfeladatokat, és összetett üzembe helyezéseket hajthat végre.
ms.devlang: na
ms.topic: article
ms.date: 09/14/2020
ms.author: jafreebe
ms.reviewer: ushan
ms.custom: devx-track-python, github-actions-azure, devx-track-azurecli
ms.openlocfilehash: 1ed2b007ae00516a030e67b7f6abacbd00a8d403
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107772882"
---
# <a name="deploy-to-app-service-using-github-actions"></a>Üzembe helyezés az App Service-ben a GitHub Actions segítségével

Első lépések [a](https://docs.github.com/en/actions/learn-github-actions) GitHub Actions a munkafolyamat automatizálásához és a GitHubról Azure App Service [üzembe](overview.md) helyezéséhez. 

## <a name="prerequisites"></a>Előfeltételek 

- Aktív előfizetéssel rendelkezik egy Azure-fiók. [Hozzon létre egy ingyenes fiókot.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- Egy GitHub-fiók. Ha még nincs ilyen szolgáltatása, regisztráljon [ingyenesen.](https://github.com/join)  
- Egy működő Azure App Service alkalmazás. 
    - .NET: [ASP.NET Core-webalkalmazás létrehozása az Azure-ban](quickstart-dotnetcore.md)
    - ASP.NET: [ASP.NET Framework-webalkalmazás létrehozása az Azure-ban](quickstart-dotnet-framework.md)
    - JavaScript: [Node.js webalkalmazás létrehozása a Azure App Service](quickstart-nodejs.md)  
    - Java: [Java-alkalmazás létrehozása a Azure App Service](quickstart-java.md)
    - Python: [Python-alkalmazás létrehozása a Azure App Service](quickstart-python.md)

## <a name="workflow-file-overview"></a>Munkafolyamat-fájl áttekintése

A munkafolyamatokat az adattár elérési útján található YAML- (.yml-) fájl `/.github/workflows/` határozza meg. Ez a definíció a munkafolyamatot felhozó különböző lépéseket és paramétereket tartalmazza.

A fájl három szakaszból áll:

|Section  |Feladatok  |
|---------|---------|
|**Hitelesítés** | 1. Szolgáltatásnév vagy közzétételi profil definiálása. <br /> 2. Hozzon létre egy titkos GitHub-et. |
|**Létrehozás** | 1. A környezet beállítása. <br /> 2. A webalkalmazás összeállítása. |
|**Telepítés** | 1. A webalkalmazás üzembe helyezése. |

## <a name="use-the-deployment-center"></a>Az Üzembe helyezési központ használata

Az üzembe helyezési központ használatával GitHub Actions gyorsan App Service használatba. Ez automatikusan létrehoz egy munkafolyamatfájlt az alkalmazáskészlet alapján, és véglegesít egy github-adattárban a megfelelő könyvtárban.

1. Lépjen a webalkalmazáshoz a Azure Portal
1. A bal oldalon kattintson az Üzembe **helyezési központ elemre.**
1. A **Folyamatos üzembe helyezés (CI/CD) alatt** válassza a **GitHub lehetőséget**
1. Ezután válassza a **GitHub Actions**
1. A legördülő menük használatával válassza ki a GitHub-adattárat, ágat és alkalmazáskészletet
    - Ha a kiválasztott ág védett, továbbra is hozzáadhatja a munkafolyamat-fájlt. A folytatás előtt mindenképpen tekintse át az ágvédelemeket.
1. Az utolsó képernyőn áttekintheti a kiválasztott beállításokat, és megtekintheti az adattárba véglegesítő munkafolyamatfájl előnézetét. Ha a beállítások helyesek, kattintson a Befejezés **gombra.**

Ez véglegeszi a munkafolyamat-fájlt az adattárban. Az alkalmazás építési és üzembe helyezési munkafolyamata azonnal elindul.

## <a name="set-up-a-workflow-manually"></a>Munkafolyamat manuális beállítása

A munkafolyamatokat az Üzembe helyezési központ használata nélkül is üzembe helyezheti. Ahhoz, hogy ezt meg tudja tenni, először létre kell hoznia az üzembe helyezési hitelesítő adatokat. 

## <a name="generate-deployment-credentials"></a>Üzembe helyezési hitelesítő adatok létrehozása

Az Azure-fiókkal való App Services ajánlott GitHub Actions közzétételi profillal. Szolgáltatásnévvel is hitelesíthet, de a folyamathoz további lépésekre van szükség. 

Az Azure-ral való hitelesítéshez mentse a közzétételi profil hitelesítő adatait vagy szolgáltatásnévét [GitHub-titkos](https://docs.github.com/en/actions/reference/encrypted-secrets) kódként. A titkos elérést a munkafolyamaton belül fogja elérni. 

# <a name="publish-profile"></a>[Profil közzététele](#tab/applevel)

A közzétételi profil egy alkalmazásszintű hitelesítő adat. Állítsa be a közzétételi profilt GitHub titkos kódként. 

1. Az app service-t a Azure Portal. 

1. Az Áttekintés **lapon** válassza a **Közzétételi profil lekérte lehetőséget.**

1. Mentse a letöltött fájlt. A github titkos kód létrehozásához a fájl tartalmát fogja használni.

> [!NOTE]
> 2020 októberében a Linux-webalkalmazások esetében a közzétételi profil letöltése előtt az alkalmazásbeállításra `WEBSITE_WEBDEPLOY_USE_SCM` `true` **lesz szükség.** Ez a követelmény a jövőben el lesz távolítva.

# <a name="service-principal"></a>[Szolgáltatásnév](#tab/userlevel)

Szolgáltatásnév az [](../active-directory/develop/app-objects-and-service-principals.md#service-principal-object) [az ad sp create-for-rbac paranccsal](/cli/azure/ad/sp#az_ad_sp_create_for_rbac) hozható létre az [Azure CLI-n.](/cli/azure/) Futtassa ezt a [Azure Cloud Shell](https://shell.azure.com/) a Azure Portal vagy a Próbálja **ki gombot** választva.

```azurecli-interactive
az ad sp create-for-rbac --name "myApp" --role contributor \
                            --scopes /subscriptions/<subscription-id>/resourceGroups/<group-name>/providers/Microsoft.Web/sites/<app-name> \
                            --sdk-auth
```

A fenti példában cserélje le a helyőrzőket az előfizetés azonosítójára, az erőforráscsoport nevére és az alkalmazás nevére. A kimenet egy JSON-objektum a szerepkör-hozzárendelési hitelesítő adatokkal, amelyek hozzáférést biztosítanak App Service alkalmazáshoz az alábbihoz hasonlóan. Másolja ezt a JSON-objektumot későbbire.

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
> Mindig jó eljárás a minimális hozzáférés megadása. Az előző példában a hatókör nem a teljes erőforráscsoportra, App Service alkalmazásra van korlátozva.

---

## <a name="configure-the-github-secret"></a>A GitHub titkos kód konfigurálása


# <a name="publish-profile"></a>[Profil közzététele](#tab/applevel)

A [GitHubon](https://github.com/)keresse meg az adattárat, és válassza a Settings > Secrets > Add a new secret (Új titkos kód **hozzáadása) lehetőséget.**

Alkalmazásszintű [hitelesítő adatok](#generate-deployment-credentials)használata érdekében illessze be a letöltött közzétételi profilfájl tartalmát a titkos adatokat tároló érték mezőbe. A titkos nak nevezze el `AZURE_WEBAPP_PUBLISH_PROFILE` a következőt: .

A GitHub-munkafolyamat konfigurálásakor a et kell `AZURE_WEBAPP_PUBLISH_PROFILE` használnia az Azure-webalkalmazás üzembe helyezése műveletben. Például:
    
```yaml
- uses: azure/webapps-deploy@v2
  with:
    publish-profile: ${{ secrets.AZURE_WEBAPP_PUBLISH_PROFILE }}
```

# <a name="service-principal"></a>[Szolgáltatásnév](#tab/userlevel)

A [GitHubon](https://github.com/)keresse meg az adattárat, és válassza a Settings > Secrets > Add a new secret (Új titkos kód **hozzáadása) lehetőséget.**

Felhasználói szintű hitelesítő adatok használata esetén illessze be az Azure [CLI-parancs](#generate-deployment-credentials)teljes JSON-kimenetét a titkos adat értékmezőjére. Adja a titkos nak a `AZURE_CREDENTIALS` nevet.

Amikor később konfigurálja a munkafolyamat-fájlt, a titkos adatokat kell használnia az `creds` Azure Login művelet bemenetéhez. Például:

```yaml
- uses: azure/login@v1
  with:
    creds: ${{ secrets.AZURE_CREDENTIALS }}
```

---

## <a name="set-up-the-environment"></a>A környezet beállítása

A környezet beállítása az egyik beállítási művelettel használhatja.

|**Nyelv**  |**Beállítási művelet**  |
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

A webalkalmazások építési és üzembe helyezési folyamata Azure App Service nyelvtől függően változik. 

Az alábbi példák a webalkalmazást felépítő munkafolyamat különböző támogatott nyelveken található részét mutatják be.

A webalkalmazás gyökérkönyvtárát minden nyelvhez beállíthatja a `working-directory` használatával. 

**.NET**

A környezeti változó `AZURE_WEBAPP_PACKAGE_PATH` beállítja a webalkalmazás-projekt elérési útját. 

```yaml
- name: dotnet build and publish
  run: |
    dotnet restore
    dotnet build --configuration Release
    dotnet publish -c Release -o '${{ env.AZURE_WEBAPP_PACKAGE_PATH }}/myapp' 
```
**ASP.NET**

Visszaállíthatja a NuGet-függőségeket, és futtathatja az msbuild-et a `run` sel. 

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

A Node.js az npm-könyvtárat a következőben állíthatja be `working-directory` vagy módosíthatja: `pushd` . 

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

A kód üzembe helyezéséhez egy App Service alkalmazásba, használja a `azure/webapps-deploy@v2` műveletet. Ennek a műveletnek négy paramétere van:

| **Paraméter**  | **Magyarázat**  |
|---------|---------|
| **alkalmazás neve** | (Kötelező) A App Service neve | 
| **közzétételi profil** | (Nem kötelező) Profilfájl tartalmának közzététele a Web Deploy titkos kulcsok segítségével |
| **Csomag** | (Nem kötelező) Csomag vagy mappa elérési útja. Az elérési út tartalmazhat *.zip, *.war, *.jar vagy egy üzembe helyezni kívánt mappát |
| **slot-name (tárolóhely neve)** | (Nem kötelező) Adjon meg egy meglévő, az éles tárolóhelyen kívül található [tárolóhelyet](deploy-staging-slots.md) |


# <a name="publish-profile"></a>[Profil közzététele](#tab/applevel)

### <a name="net-core"></a>.NET Core

.NET Core-alkalmazás létrehozása és üzembe helyezése az Azure-ban egy Azure közzétételi profillal. A `publish-profile` bemenet a korábban létrehozott titkos `AZURE_WEBAPP_PUBLISH_PROFILE` referenciára hivatkozik.

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

NuGetet és hitelesítést használó ASP.NET MVC-alkalmazás `publish-profile` összeállítása és üzembe helyezése. 


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

Java Spring-alkalmazás létrehozása és üzembe helyezése az Azure-ban egy Azure közzétételi profillal. A `publish-profile` bemenet a korábban létrehozott titkos `AZURE_WEBAPP_PUBLISH_PROFILE` referenciára hivatkozik.

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

A helyett `war` egy üzembe helyezéséhez módosítsa az `jar` `package` értéket. 


```yaml
    - name: Azure WebApp
      uses: Azure/webapps-deploy@v2
      with:
        app-name: my-app-name
        publish-profile: ${{ secrets.AZURE_WEBAPP_PUBLISH_PROFILE }}
        package: my/target/*.war
```

### <a name="javascript"></a>JavaScript 

Alkalmazás-Node.js és üzembe helyezése az Azure-ban az alkalmazás közzétételi profiljával. A `publish-profile` bemenet a korábban létrehozott titkos `AZURE_WEBAPP_PUBLISH_PROFILE` referenciára hivatkozik.

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

Python-alkalmazás létrehozása és üzembe helyezése az Azure-ban az alkalmazás közzétételi profiljával. Figyelje `publish-profile` meg, hogy a bemenet a `AZURE_WEBAPP_PUBLISH_PROFILE` korábban létrehozott titkos referenciára hivatkozik.

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

.NET Core-alkalmazás összeállítása és üzembe helyezése az Azure-ban egy Azure-szolgáltatásnév használatával. Figyelje `creds` meg, hogy a bemenet a `AZURE_CREDENTIALS` korábban létrehozott titkos referenciára hivatkozik.


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

Egy azure-beli ASP.NET MVC-alkalmazás összeállítása és üzembe helyezése az Azure-ban. Figyelje `creds` meg, hogy a bemenet a `AZURE_CREDENTIALS` korábban létrehozott titkos referenciára hivatkozik.

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

Java Spring-alkalmazás összeállítása és üzembe helyezése az Azure-ban egy Azure-szolgáltatásnév használatával. Figyelje `creds` meg, hogy a bemenet a `AZURE_CREDENTIALS` korábban létrehozott titkos referenciára hivatkozik.

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

Egy azure-beli szolgáltatásnévvel Node.js létre és helyezhet üzembe egy új alkalmazásokat az Azure-ban. Figyelje `creds` meg, hogy a bemenet a `AZURE_CREDENTIALS` korábban létrehozott titkos referenciára hivatkozik.

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

Python-alkalmazás összeállítása és üzembe helyezése az Azure-ban egy Azure-szolgáltatásnévvel. Figyelje `creds` meg, hogy a bemenet a `AZURE_CREDENTIALS` korábban létrehozott titkos referenciára hivatkozik.

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

A GitHubon található különböző adattárakba csoportosított műveleteket találhat, amelyek dokumentációt és példákat tartalmaznak, amelyek segítenek a GitHub CI/CD-hez való használatában és az alkalmazások Azure-ban való üzembe helyezésében.

- [Az Azure-ban üzembe helyező műveletek munkafolyamatai](https://github.com/Azure/actions-workflow-samples)

- [Azure-bejelentkezés](https://github.com/Azure/login)

- [Azure WebApp](https://github.com/Azure/webapps-deploy)

- [Azure WebApp tárolókhoz](https://github.com/Azure/webapps-container-deploy)

- [Docker– bejelentkezés/kijelentkezés](https://github.com/Azure/docker-login)

- [Munkafolyamatokat kiváltó események](https://docs.github.com/en/actions/reference/events-that-trigger-workflows)

- [K8s üzembe helyezése](https://github.com/Azure/k8s-deploy)

- [Kezdő munkafolyamatok](https://github.com/actions/starter-workflows)
