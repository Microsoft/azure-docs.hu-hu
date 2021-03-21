---
title: A GitHub-műveletek használata a Azure Functions a kód frissítéseinek elvégzéséhez
description: Megtudhatja, hogyan hozhat létre és helyezhet üzembe Azure Functions-projekteket a GitHubon a GitHub-műveletek használatával.
author: craigshoemaker
ms.topic: conceptual
ms.date: 10/07/2020
ms.author: cshoe
ms.custom: devx-track-csharp, devx-track-python, github-actions-azure
ms.openlocfilehash: cc356b307a752b10ba6f1c1a7151381c5644ca1e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98762730"
---
# <a name="continuous-delivery-by-using-github-action"></a>Folyamatos teljesítés a GitHub-művelet használatával

A [GitHub-műveletek](https://github.com/features/actions) használatával definiálhat egy munkafolyamatot, amely automatikusan felépíti és telepíti a Function alkalmazás kódját a Azure Functionsban. 

A GitHub-műveletekben a [munkafolyamat](https://docs.github.com/en/actions/learn-github-actions/introduction-to-github-actions#the-components-of-github-actions) egy automatizált folyamat, amelyet a GitHub-tárházban határozhat meg. Ez a folyamat azt mutatja be, hogy a GitHub hogyan hozhat létre és helyezhet üzembe Function app-projektet a GitHubon. 

A munkafolyamatot egy YAML-(. YML) fájl határozza meg a `/.github/workflows/` tárház elérési útjában. Ez a definíció a munkafolyamatot alkotó különböző lépéseket és paramétereket tartalmazza. 

Azure Functions munkafolyamathoz a fájl három szakaszt tartalmaz: 

| Section | Feladatok |
| ------- | ----- |
| **Hitelesítés** | Töltsön le egy közzétételi profilt.<br/>Hozzon létre egy GitHub-titkot.|
| **Létrehozás** | Állítsa be a környezetet.<br/>Hozza létre a Function alkalmazást.|
| **Telepítés** | Telepítse a Function alkalmazást.|

## <a name="prerequisites"></a>Előfeltételek

- Aktív előfizetéssel rendelkező Azure-fiók. [Hozzon létre egy fiókot ingyenesen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Egy GitHub-fiók. Ha még nem rendelkezik ilyennel, regisztráljon [ingyenesen](https://github.com/join).  
- Az Azure-ban egy GitHub-adattárral üzemeltetett Working Function-alkalmazás.   
    - [Rövid útmutató: Függvény létrehozása az Azure-ban a Visual Studio Code használatával](./create-first-function-vs-code-csharp.md)

## <a name="generate-deployment-credentials"></a>Központi telepítési hitelesítő adatok előállítása

A GitHub-műveletek Azure Functions való hitelesítésének ajánlott módja a közzétételi profil használata. A hitelesítést egy egyszerű szolgáltatással is elvégezheti. További információért lásd [a GitHub-műveletek tárházát](https://github.com/Azure/functions-action). 

Miután mentette a közzétételi profil hitelesítő adatait a [GitHub-titokként](https://docs.github.com/en/actions/reference/encrypted-secrets), ezt a titkot fogja használni a munkafolyamaton belül a hitelesítéshez az Azure-ban. 

#### <a name="download-your-publish-profile"></a>Közzétételi profil letöltése

A Function alkalmazás közzétételi profiljának letöltése:

1. Válassza ki a Function alkalmazás **Áttekintés** lapját, majd válassza a **közzétételi profil beolvasása** elemet.

   :::image type="content" source="media/functions-how-to-github-actions/get-publish-profile.png" alt-text="Közzétételi profil letöltése":::

1. Mentse és másolja a fájl tartalmát.


### <a name="add-the-github-secret"></a>A GitHub-titok hozzáadása

1. A [githubon](https://github.com)keresse meg a tárházat, és válassza a **Beállítások**  >  **titkok**  >  **új titok hozzáadása** elemet.

   :::image type="content" source="media/functions-how-to-github-actions/add-secret.png" alt-text="Titkos kód hozzáadása":::

1. Adjon hozzá egy új titkos kulcsot a `AZURE_FUNCTIONAPP_PUBLISH_PROFILE` **név** mezőben, a közzétételi profil fájljának tartalmát az **értékhez**, majd válassza a **titkos kód hozzáadása** lehetőséget.

A GitHub mostantól képes hitelesíteni az Azure-beli Function-alkalmazást.

## <a name="create-the-environment"></a>A környezet létrehozása 

A környezet beállítása a nyelvspecifikus közzétételi beállítás használatával történik.

# <a name="net"></a>[.NET](#tab/dotnet)

A .NET (a ASP.NET-t is beleértve) a `actions/setup-dotnet` műveletet használja.  
Az alábbi példa a munkafolyamat azon részét mutatja be, amely a környezetet állítja be:

```yaml
    - name: Setup DotNet 2.2.402 Environment
      uses: actions/setup-dotnet@v1
      with:
        dotnet-version: 2.2.402
```

# <a name="java"></a>[Java](#tab/java)

A Java a  `actions/setup-java` műveletet használja.  
Az alábbi példa a munkafolyamat azon részét mutatja be, amely a környezetet állítja be:

```yaml
    - name: Setup Java 1.8.x
      uses: actions/setup-java@v1
      with:
        # If your pom.xml <maven.compiler.source> version is not in 1.8.x
        # Please change the Java version to match the version in pom.xml <maven.compiler.source>
        java-version: '1.8.x'
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

A JavaScript (Node.js) a `actions/setup-node` műveletet használja.  
Az alábbi példa a munkafolyamat azon részét mutatja be, amely a környezetet állítja be:

```yaml

    - name: Setup Node 12.x Environment
      uses: actions/setup-node@v1
      with:
        node-version: 12.x
```

# <a name="python"></a>[Python](#tab/python)

A Python a `actions/setup-python` műveletet használja.  
Az alábbi példa a munkafolyamat azon részét mutatja be, amely a környezetet állítja be:

```yaml
    - name: Setup Python 3.7 Environment
      uses: actions/setup-python@v1
      with:
        python-version: 3.7
```
---

## <a name="build-the-function-app"></a>A Function alkalmazás összeállítása

Ez a Azure Functions által támogatott nyelvtől és nyelvtől függ. a szakasznak az egyes nyelvek standard Build lépéseinek kell lennie.

Az alábbi példa bemutatja a munkafolyamat azon részét, amely létrehozza a Function alkalmazást, amely a nyelvspecifikus:

# <a name="net"></a>[.NET](#tab/dotnet)

```yaml
    env:
      AZURE_FUNCTIONAPP_PACKAGE_PATH: '.' # set this to the path to your web app project, defaults to the repository root

    - name: 'Resolve Project Dependencies Using Dotnet'
      shell: bash
      run: |
        pushd './${{ env.AZURE_FUNCTIONAPP_PACKAGE_PATH }}'
        dotnet build --configuration Release --output ./output
        popd
```

# <a name="java"></a>[Java](#tab/java)

```yaml
    env:
      POM_XML_DIRECTORY: '.'  # set this to the directory which contains pom.xml file

    - name: 'Restore Project Dependencies Using Mvn'
      shell: bash
      run: |
        pushd './${{ env.POM_XML_DIRECTORY }}'
        mvn clean package
        mvn azure-functions:package
        popd
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```yaml
    env:
      AZURE_FUNCTIONAPP_PACKAGE_PATH: '.'  # set this to the path to your web app project, defaults to the repository root

    - name: 'Resolve Project Dependencies Using Npm'
      shell: bash
      run: |
        pushd './${{ env.AZURE_FUNCTIONAPP_PACKAGE_PATH }}'
        npm install
        npm run build --if-present
        npm run test --if-present
        popd
```

# <a name="python"></a>[Python](#tab/python)

```yaml
    env:
      AZURE_FUNCTIONAPP_PACKAGE_PATH: '.' # set this to the path to your web app project, defaults to the repository root

    - name: 'Resolve Project Dependencies Using Pip'
      shell: bash
      run: |
        pushd './${{ env.AZURE_FUNCTIONAPP_PACKAGE_PATH }}'
        python -m pip install --upgrade pip
        pip install -r requirements.txt --target=".python_packages/lib/site-packages"
        popd
```
---

## <a name="deploy-the-function-app"></a>A függvényalkalmazás üzembe helyezése

Használja a `Azure/functions-action` műveletet a kód egy Function alkalmazásban való üzembe helyezéséhez. Ehhez a művelethez három paraméter tartozik:

|Paraméter |Magyarázat  |
|---------|---------|
|_**alkalmazás neve**_ | Kötelező A függvény alkalmazásának neve. |
|_**tárolóhely neve**_ | Választható Annak a [telepítési tárolóhelynek](functions-deployment-slots.md) a neve, amelyre telepíteni kívánja a-t. A tárolóhelynek már definiálva kell lennie a Function alkalmazásban. |
|_**közzétételi profil**_ | Választható A közzétételi profilhoz tartozó GitHub-titok neve. |

Az alábbi példa a és a rendszer 1. verzióját használja a `functions-action` `publish profile` hitelesítéshez 

# <a name="net"></a>[.NET](#tab/dotnet)

Hozzon létre egy olyan .NET Linux-munkafolyamatot, amely egy közzétételi profilt használ.

```yaml
name: Deploy DotNet project to function app with a Linux environment

on:
  [push]

env:
  AZURE_FUNCTIONAPP_NAME: your-app-name  # set this to your application's name
  AZURE_FUNCTIONAPP_PACKAGE_PATH: '.'    # set this to the path to your web app project, defaults to the repository root
  DOTNET_VERSION: '2.2.402'              # set this to the dotnet version to use

jobs:
  build-and-deploy:
    runs-on: ubuntu-latest
    steps:
    - name: 'Checkout GitHub Action'
      uses: actions/checkout@main

    - name: Setup DotNet ${{ env.DOTNET_VERSION }} Environment
      uses: actions/setup-dotnet@v1
      with:
        dotnet-version: ${{ env.DOTNET_VERSION }}

    - name: 'Resolve Project Dependencies Using Dotnet'
      shell: bash
      run: |
        pushd './${{ env.AZURE_FUNCTIONAPP_PACKAGE_PATH }}'
        dotnet build --configuration Release --output ./output
        popd
    - name: 'Run Azure Functions Action'
      uses: Azure/functions-action@v1
      id: fa
      with:
        app-name: ${{ env.AZURE_FUNCTIONAPP_NAME }}
        package: '${{ env.AZURE_FUNCTIONAPP_PACKAGE_PATH }}/output'
        publish-profile: ${{ secrets.AZURE_FUNCTIONAPP_PUBLISH_PROFILE }}
```
Hozzon létre egy olyan .NET Windows-munkafolyamatot, amely egy közzétételi profilt használ.

```yaml
name: Deploy DotNet project to function app with a Windows environment

on:
  [push]

env:
  AZURE_FUNCTIONAPP_NAME: your-app-name  # set this to your application's name
  AZURE_FUNCTIONAPP_PACKAGE_PATH: '.'    # set this to the path to your web app project, defaults to the repository root
  DOTNET_VERSION: '2.2.402'              # set this to the dotnet version to use

jobs:
  build-and-deploy:
    runs-on: windows-latest
    steps:
    - name: 'Checkout GitHub Action'
      uses: actions/checkout@main

    - name: Setup DotNet ${{ env.DOTNET_VERSION }} Environment
      uses: actions/setup-dotnet@v1
      with:
        dotnet-version: ${{ env.DOTNET_VERSION }}

    - name: 'Resolve Project Dependencies Using Dotnet'
      shell: pwsh
      run: |
        pushd './${{ env.AZURE_FUNCTIONAPP_PACKAGE_PATH }}'
        dotnet build --configuration Release --output ./output
        popd
    - name: 'Run Azure Functions Action'
      uses: Azure/functions-action@v1
      id: fa
      with:
        app-name: ${{ env.AZURE_FUNCTIONAPP_NAME }}
        package: '${{ env.AZURE_FUNCTIONAPP_PACKAGE_PATH }}/output'
        publish-profile: ${{ secrets.AZURE_FUNCTIONAPP_PUBLISH_PROFILE }}
```

# <a name="java"></a>[Java](#tab/java)

Hozzon létre egy Java Linux-munkafolyamatot, amely egy közzétételi profilt használ.

```yaml
name: Deploy Java project to function app

on:
  [push]

env:
  AZURE_FUNCTIONAPP_NAME: your-app-name      # set this to your function app name on Azure
  POM_XML_DIRECTORY: '.'                     # set this to the directory which contains pom.xml file
  POM_FUNCTIONAPP_NAME: your-app-name        # set this to the function app name in your local development environment
  JAVA_VERSION: '1.8.x'                      # set this to the dotnet version to use

jobs:
  build-and-deploy:
    runs-on: ubuntu-latest
    steps:
    - name: 'Checkout GitHub Action'
      uses: actions/checkout@main

    - name: Setup Java Sdk ${{ env.JAVA_VERSION }}
      uses: actions/setup-java@v1
      with:
        java-version: ${{ env.JAVA_VERSION }}

    - name: 'Restore Project Dependencies Using Mvn'
      shell: bash
      run: |
        pushd './${{ env.POM_XML_DIRECTORY }}'
        mvn clean package
        mvn azure-functions:package
        popd
    - name: 'Run Azure Functions Action'
      uses: Azure/functions-action@v1
      id: fa
      with:
        app-name: ${{ env.AZURE_FUNCTIONAPP_NAME }}
        package: './${{ env.POM_XML_DIRECTORY }}/target/azure-functions/${{ env.POM_FUNCTIONAPP_NAME }}'
        publish-profile: ${{ secrets.AZURE_FUNCTIONAPP_PUBLISH_PROFILE }}
```

Hozzon létre egy Java Windows-munkafolyamatot, amely egy közzétételi profilt használ.

```yaml
name: Deploy Java project to function app

on:
  [push]

env:
  AZURE_FUNCTIONAPP_NAME: your-app-name      # set this to your function app name on Azure
  POM_XML_DIRECTORY: '.'                     # set this to the directory which contains pom.xml file
  POM_FUNCTIONAPP_NAME: your-app-name        # set this to the function app name in your local development environment
  JAVA_VERSION: '1.8.x'                      # set this to the java version to use

jobs:
  build-and-deploy:
    runs-on: windows-latest
    steps:
    - name: 'Checkout GitHub Action'
      uses: actions/checkout@main

    - name: Setup Java Sdk ${{ env.JAVA_VERSION }}
      uses: actions/setup-java@v1
      with:
        java-version: ${{ env.JAVA_VERSION }}

    - name: 'Restore Project Dependencies Using Mvn'
      shell: pwsh
      run: |
        pushd './${{ env.POM_XML_DIRECTORY }}'
        mvn clean package
        mvn azure-functions:package
        popd
    - name: 'Run Azure Functions Action'
      uses: Azure/functions-action@v1
      id: fa
      with:
        app-name: ${{ env.AZURE_FUNCTIONAPP_NAME }}
        package: './${{ env.POM_XML_DIRECTORY }}/target/azure-functions/${{ env.POM_FUNCTIONAPP_NAME }}'
        publish-profile: ${{ secrets.AZURE_FUNCTIONAPP_PUBLISH_PROFILE }}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Hozzon létre egy Node.JS Linux-munkafolyamatot, amely egy közzétételi profilt használ.

```yaml
name: Deploy Node.js project to function app

on:
  [push]

env:
  AZURE_FUNCTIONAPP_NAME: your-app-name    # set this to your application's name
  AZURE_FUNCTIONAPP_PACKAGE_PATH: '.'      # set this to the path to your web app project, defaults to the repository root
  NODE_VERSION: '12.x'                     # set this to the node version to use (supports 8.x, 10.x, 12.x)

jobs:
  build-and-deploy:
    runs-on: ubuntu-latest
    steps:
    - name: 'Checkout GitHub Action'
      uses: actions/checkout@main

    - name: Setup Node ${{ env.NODE_VERSION }} Environment
      uses: actions/setup-node@v1
      with:
        node-version: ${{ env.NODE_VERSION }}

    - name: 'Resolve Project Dependencies Using Npm'
      shell: bash
      run: |
        pushd './${{ env.AZURE_FUNCTIONAPP_PACKAGE_PATH }}'
        npm install
        npm run build --if-present
        npm run test --if-present
        popd
    - name: 'Run Azure Functions Action'
      uses: Azure/functions-action@v1
      id: fa
      with:
        app-name: ${{ env.AZURE_FUNCTIONAPP_NAME }}
        package: ${{ env.AZURE_FUNCTIONAPP_PACKAGE_PATH }}
        publish-profile: ${{ secrets.AZURE_FUNCTIONAPP_PUBLISH_PROFILE }}
```

Hozzon létre egy Node.JS Windows-munkafolyamatot, amely egy közzétételi profilt használ.

```yaml
name: Deploy Node.js project to function app

on:
  [push]

env:
  AZURE_FUNCTIONAPP_NAME: your-app-name    # set this to your application's name
  AZURE_FUNCTIONAPP_PACKAGE_PATH: '.'      # set this to the path to your web app project, defaults to the repository root
  NODE_VERSION: '10.x'                     # set this to the node version to use (supports 8.x, 10.x, 12.x)

jobs:
  build-and-deploy:
    runs-on: windows-latest
    steps:
    - name: 'Checkout GitHub Action'
      uses: actions/checkout@main

    - name: Setup Node ${{ env.NODE_VERSION }} Environment
      uses: actions/setup-node@v1
      with:
        node-version: ${{ env.NODE_VERSION }}

    - name: 'Resolve Project Dependencies Using Npm'
      shell: pwsh
      run: |
        pushd './${{ env.AZURE_FUNCTIONAPP_PACKAGE_PATH }}'
        npm install
        npm run build --if-present
        npm run test --if-present
        popd
    - name: 'Run Azure Functions Action'
      uses: Azure/functions-action@v1
      id: fa
      with:
        app-name: ${{ env.AZURE_FUNCTIONAPP_NAME }}
        package: ${{ env.AZURE_FUNCTIONAPP_PACKAGE_PATH }}
        publish-profile: ${{ secrets.AZURE_FUNCTIONAPP_PUBLISH_PROFILE }}

```
# <a name="python"></a>[Python](#tab/python)

Hozzon létre egy olyan Python Linux-munkafolyamatot, amely egy közzétételi profilt használ.

```yaml
name: Deploy Python project to function app

on:
  [push]

env:
  AZURE_FUNCTIONAPP_NAME: your-app-name # set this to your application's name
  AZURE_FUNCTIONAPP_PACKAGE_PATH: '.'   # set this to the path to your web app project, defaults to the repository root
  PYTHON_VERSION: '3.7'                 # set this to the python version to use (supports 3.6, 3.7, 3.8)

jobs:
  build-and-deploy:
    runs-on: ubuntu-latest
    steps:
    - name: 'Checkout GitHub Action'
      uses: actions/checkout@main

    - name: Setup Python ${{ env.PYTHON_VERSION }} Environment
      uses: actions/setup-python@v1
      with:
        python-version: ${{ env.PYTHON_VERSION }}

    - name: 'Resolve Project Dependencies Using Pip'
      shell: bash
      run: |
        pushd './${{ env.AZURE_FUNCTIONAPP_PACKAGE_PATH }}'
        python -m pip install --upgrade pip
        pip install -r requirements.txt --target=".python_packages/lib/site-packages"
        popd
    - name: 'Run Azure Functions Action'
      uses: Azure/functions-action@v1
      id: fa
      with:
        app-name: ${{ env.AZURE_FUNCTIONAPP_NAME }}
        package: ${{ env.AZURE_FUNCTIONAPP_PACKAGE_PATH }}
        publish-profile: ${{ secrets.AZURE_FUNCTIONAPP_PUBLISH_PROFILE }}
```

---

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [További információ az Azure-ról és a GitHub-integrációról](/azure/developer/github/)
