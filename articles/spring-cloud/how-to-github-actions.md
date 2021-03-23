---
title: Azure Spring Cloud CI/CD GitHub-műveletekkel
description: CI/CD-munkafolyamatok létrehozása az Azure Spring Cloud-hoz a GitHub-műveletekkel
author: MikeDodaro
ms.author: barbkess
ms.service: spring-cloud
ms.topic: how-to
ms.date: 09/08/2020
ms.custom: devx-track-java, devx-track-azurecli
zone_pivot_groups: programming-languages-spring-cloud
ms.openlocfilehash: 8400fcacbfa4c76aceb079b788255e3d3b83ce33
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/23/2021
ms.locfileid: "104878007"
---
# <a name="azure-spring-cloud-cicd-with-github-actions"></a>Azure Spring Cloud CI/CD GitHub-műveletekkel

A GitHub-műveletek egy automatizált szoftverfejlesztési életciklus-munkafolyamatot támogatnak. Az Azure Spring Cloud GitHub-műveleteivel munkafolyamatokat hozhat létre a tárházban az Azure-ban történő létrehozásához, teszteléséhez, csomagolásához, kiadásához és üzembe helyezéséhez. 

## <a name="prerequisites"></a>Előfeltételek
Ehhez a példához az [Azure CLI](/cli/azure/install-azure-cli)szükséges.

::: zone pivot="programming-language-csharp"
## <a name="set-up-github-repository-and-authenticate"></a>GitHub-adattár beállítása és hitelesítése
Szüksége lesz egy Azure-szolgáltatás egyszerű hitelesítő adataira az Azure bejelentkezési műveletének engedélyezéséhez. Azure-beli hitelesítő adatok beszerzéséhez hajtsa végre a következő parancsokat a helyi gépen:

```
az login
az ad sp create-for-rbac --role contributor --scopes /subscriptions/<SUBSCRIPTION_ID> --sdk-auth 
```

Egy adott erőforráscsoport eléréséhez csökkentheti a hatókört:

```
az ad sp create-for-rbac --role contributor --scopes /subscriptions/<SUBSCRIPTION_ID>/resourceGroups/<RESOURCE_GROUP> --sdk-auth
```

A parancsnak egy JSON-objektumot kell kiadnia:

```JSON
{
    "clientId": "<GUID>",
    "clientSecret": "<GUID>",
    "subscriptionId": "<GUID>",
    "tenantId": "<GUID>",
    ...
}
```

Ez a példa a [steeltoe mintát használja a githubon](https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples/tree/master/steeltoe-sample).  Válassza ki az adattárat, nyissa meg az elágazáshoz tartozó GitHub-adattár lapot, és kattintson a **Beállítások** fülre. Nyissa meg a **titkok** menüt, és válassza az **új titok** elemet:

 ![Új titok hozzáadása](./media/github-actions/actions1.png)

Állítsa be a titkos nevet `AZURE_CREDENTIALS` és annak értékét a *GitHub-tárház beállítása és hitelesítése* fejléc alatt található JSON-karakterláncra.

 ![Titkos adatértékek beállítása](./media/github-actions/actions2.png)

Azt is megteheti, hogy az Azure-beli bejelentkezési hitelesítő adatokat Key Vault a GitHub-műveletekben, ahogy az az [Azure Spring hitelesítése Key Vault a GitHub-műveletekben](./spring-cloud-github-actions-key-vault.md).

## <a name="provision-service-instance"></a>Szolgáltatási példány kiépítése
Az Azure Spring Cloud Service-példány kiépítéséhez futtassa az alábbi parancsokat az Azure CLI használatával.

```azurecli
az extension add --name spring-cloud
az group create --location eastus --name <resource group name>
az spring-cloud create -n <service instance name> -g <resource group name>
az spring-cloud config-server git set -n <service instance name> --uri https://github.com/xxx/Azure-Spring-Cloud-Samples --label main --search-paths steeltoe-sample/config
```

## <a name="build-the-workflow"></a>A munkafolyamat összeállítása
A munkafolyamat a következő beállításokkal van definiálva.

### <a name="prepare-for-deployment-with-azure-cli"></a>Felkészülés az Azure CLI-vel való üzembe helyezésre

A parancs `az spring-cloud app create` jelenleg nem idempotens. Miután egyszer futtatta, hibaüzenet jelenik meg, ha újra futtatja ugyanezt a parancsot. Ezt a munkafolyamatot a meglévő Azure Spring Cloud-alkalmazásokon és-példányokon ajánljuk.

Az előkészítéshez használja az alábbi Azure CLI-parancsokat:
```
az configure --defaults group=<service group name>
az configure --defaults spring-cloud=<service instance name>
az spring-cloud app create --name planet-weather-provider
az spring-cloud app create --name solar-system-weather
```

### <a name="deploy-with-azure-cli-directly"></a>Üzembe helyezés az Azure CLI-vel közvetlenül

Hozza létre a `.github/workflows/main.yml` fájlt a tárházban az alábbi tartalommal. Cserélje `<your resource group name>` le `<your service name>` a és a értéket a megfelelő értékekre.

```yaml
name: Steeltoe-CD

# Controls when the action will run. Triggers the workflow on push or pull request
# events but only for the main branch
on:
  push:
    branches: [ main]

# A workflow run is made up of one or more jobs that can run sequentially or in parallel
jobs:
  # This workflow contains a single job called "build"
  build:
    # The type of runner that the job will run on
    runs-on: ubuntu-latest
    env:
      working-directory: ./steeltoe-sample
      resource-group-name: <your resource group name>
      service-name: <your service name>
    
    # Supported .NET Core version matrix.
    strategy:
      matrix:
        dotnet: [ '3.1.x' ]

    # Steps represent a sequence of tasks that will be executed as part of the job
    steps:
      # Checks-out your repository under $GITHUB_WORKSPACE, so your job can access it
      - uses: actions/checkout@v2

      # Set up .NET Core 3.1 SDK
      - uses: actions/setup-dotnet@v1
        with:
          dotnet-version: ${{ matrix.dotnet }}
          
      # Set credential for az login
      - uses: azure/login@v1.1
        with:
          creds: ${{ secrets.AZURE_CREDENTIALS }}

      - name: install Azure CLI extension
        run: |
          az extension add --name spring-cloud --yes
      
      - name: Build and package planet-weather-provider app
        working-directory: ${{env.working-directory}}/src/planet-weather-provider
        run: |
          dotnet publish
          az spring-cloud app deploy -n planet-weather-provider --runtime-version NetCore_31 --main-entry Microsoft.Azure.SpringCloud.Sample.PlanetWeatherProvider.dll --artifact-path ./publish-deploy-planet.zip -s ${{ env.service-name }} -g ${{ env.resource-group-name }}
      - name: Build solar-system-weather app
        working-directory: ${{env.working-directory}}/src/solar-system-weather
        run: |
          dotnet publish
          az spring-cloud app deploy -n solar-system-weather --runtime-version NetCore_31 --main-entry Microsoft.Azure.SpringCloud.Sample.SolarSystemWeather.dll --artifact-path ./publish-deploy-solar.zip -s ${{ env.service-name }} -g ${{ env.resource-group-name }}
```
::: zone-end

::: zone pivot="programming-language-java"
## <a name="set-up-github-repository-and-authenticate"></a>GitHub-adattár beállítása és hitelesítése
Szüksége lesz egy Azure-szolgáltatás egyszerű hitelesítő adataira az Azure bejelentkezési műveletének engedélyezéséhez. Azure-beli hitelesítő adatok beszerzéséhez hajtsa végre a következő parancsokat a helyi gépen:
```
az login
az ad sp create-for-rbac --role contributor --scopes /subscriptions/<SUBSCRIPTION_ID> --sdk-auth 
```
Egy adott erőforráscsoport eléréséhez csökkentheti a hatókört:
```
az ad sp create-for-rbac --role contributor --scopes /subscriptions/<SUBSCRIPTION_ID>/resourceGroups/<RESOURCE_GROUP> --sdk-auth
```
A parancsnak egy JSON-objektumot kell kiadnia:
```JSON
{
    "clientId": "<GUID>",
    "clientSecret": "<GUID>",
    "subscriptionId": "<GUID>",
    "tenantId": "<GUID>",
    ...
}
```

Ez a példa a [PiggyMetrics](https://github.com/Azure-Samples/piggymetrics) mintát használja a githubon.  Elágazás a minta, nyissa meg a GitHub-adattár lapot, és kattintson a **Beállítások** fülre. Nyissa meg a **titkok** menüt, és kattintson **az új titkos kulcs hozzáadása** lehetőségre:

 ![Új titok hozzáadása](./media/github-actions/actions1.png)

Állítsa be a titkos nevet `AZURE_CREDENTIALS` és annak értékét a *GitHub-tárház beállítása és hitelesítése* fejléc alatt található JSON-karakterláncra.

 ![Titkos adatértékek beállítása](./media/github-actions/actions2.png)

Azt is megteheti, hogy az Azure-beli bejelentkezési hitelesítő adatokat Key Vault a GitHub-műveletekben, ahogy az az [Azure Spring hitelesítése Key Vault a GitHub-műveletekben](./spring-cloud-github-actions-key-vault.md).

## <a name="provision-service-instance"></a>Szolgáltatási példány kiépítése
Az Azure Spring Cloud Service-példány kiépítéséhez futtassa az alábbi parancsokat az Azure CLI használatával.
```
az extension add --name spring-cloud
az group create --location eastus --name <resource group name>
az spring-cloud create -n <service instance name> -g <resource group name>
az spring-cloud config-server git set -n <service instance name> --uri https://github.com/xxx/piggymetrics --label config
```
## <a name="build-the-workflow"></a>A munkafolyamat összeállítása
A munkafolyamat a következő beállításokkal van definiálva.

### <a name="prepare-for-deployment-with-azure-cli"></a>Felkészülés az Azure CLI-vel való üzembe helyezésre
A parancs `az spring-cloud app create` jelenleg nem idempotens.  Ezt a munkafolyamatot a meglévő Azure Spring Cloud-alkalmazásokon és-példányokon ajánljuk.

Az előkészítéshez használja az alábbi Azure CLI-parancsokat:
```
az configure --defaults group=<service group name>
az configure --defaults spring-cloud=<service instance name>
az spring-cloud app create --name gateway
az spring-cloud app create --name auth-service
az spring-cloud app create --name account-service
```

### <a name="deploy-with-azure-cli-directly"></a>Üzembe helyezés az Azure CLI-vel közvetlenül
Hozza létre a `.github/workflow/main.yml` fájlt a tárházban:

```
name: AzureSpringCloud
on: push

env:
  GROUP: <resource group name>
  SERVICE_NAME: <service instance name>

jobs:
  build-and-deploy:
    runs-on: ubuntu-latest
    steps:
    
    - uses: actions/checkout@main
    
    - name: Set up JDK 1.8
      uses: actions/setup-java@v1
      with:
        java-version: 1.8
    
    - name: maven build, clean
      run: |
        mvn clean package -DskipTests
    
    - name: Azure Login
      uses: azure/login@v1
      with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}
      
    - name: Install ASC AZ extension
      run: az extension add --name spring-cloud
   
    - name: Deploy with AZ CLI commands
      run: |
        az configure --defaults group=$GROUP
        az configure --defaults spring-cloud=$SERVICE_NAME
        az spring-cloud app deploy -n gateway --jar-path ${{ github.workspace }}/gateway/target/gateway.jar
        az spring-cloud app deploy -n account-service --jar-path ${{ github.workspace }}/account-service/target/account-service.jar
        az spring-cloud app deploy -n auth-service --jar-path ${{ github.workspace }}/auth-service/target/auth-service.jar
```
### <a name="deploy-with-azure-cli-action"></a>Üzembe helyezés az Azure CLI-vel művelettel
Az az `run` parancs az Azure CLI legújabb verzióját fogja használni. A változtatások megszakításakor az Azure CLI egy adott verzióját is használhatja az Azure/CLI-vel `action` . 

> [!Note] 
> Ez a parancs egy új tárolóban fog futni, ezért `env` nem fog működni, és a Többműveletes fájlhoz való hozzáférés további korlátozásokkal rendelkezhet.

Hozza létre a. GitHub/munkafolyamat/Main. YML fájlt az adattárban:
```
name: AzureSpringCloud
on: push

jobs:
  build-and-deploy:
    runs-on: ubuntu-latest
    steps:
    
    - uses: actions/checkout@main
    
    - name: Set up JDK 1.8
      uses: actions/setup-java@v1
      with:
        java-version: 1.8
    
    - name: maven build, clean
      run: |
        mvn clean package -DskipTests
        
    - name: Azure Login
      uses: azure/login@v1
      with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}
              
    - name: Azure CLI script
      uses: azure/CLI@v1
      with:
        azcliversion: 2.0.75
        inlineScript: |
          az extension add --name spring-cloud
          az configure --defaults group=<service group name>
          az configure --defaults spring-cloud=<service instance name>
          az spring-cloud app deploy -n gateway --jar-path $GITHUB_WORKSPACE/gateway/target/gateway.jar
          az spring-cloud app deploy -n account-service --jar-path $GITHUB_WORKSPACE/account-service/target/account-service.jar
          az spring-cloud app deploy -n auth-service --jar-path $GITHUB_WORKSPACE/auth-service/target/auth-service.jar
```

## <a name="deploy-with-maven-plugin"></a>Üzembe helyezés a Maven beépülő modullal
Egy másik lehetőség, hogy a [Maven beépülő modult](./spring-cloud-quickstart.md) használja a jar üzembe helyezéséhez és az alkalmazás beállításainak frissítéséhez. A parancs `mvn azure-spring-cloud:deploy` idempotens, és szükség esetén automatikusan hozza létre az alkalmazásokat. Nem kell előre létrehoznia a megfelelő alkalmazásokat.

```
name: AzureSpringCloud
on: push

jobs:
  build-and-deploy:
    runs-on: ubuntu-latest
    steps:
    
    - uses: actions/checkout@main
    
    - name: Set up JDK 1.8
      uses: actions/setup-java@v1
      with:
        java-version: 1.8
    
    - name: maven build, clean
      run: |
        mvn clean package -DskipTests
        
    # Maven plugin can cosume this authentication method automatically
    - name: Azure Login
      uses: azure/login@v1
      with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}
    
    # Maven deploy, make sure you have correct configurations in your pom.xml
    - name: deploy to Azure Spring Cloud using Maven
      run: |
        mvn azure-spring-cloud:deploy
```

::: zone-end

## <a name="run-the-workflow"></a>A munkafolyamat futtatása

A GitHub- **műveleteket** automatikusan engedélyezni kell `.github/workflow/main.yml` a githubra való leküldés után. A művelet akkor aktiválódik, amikor új véglegesítet küld. Ha ezt a fájlt a böngészőben hozza létre, a műveletnek már futnia kell.

Annak ellenőrzéséhez, hogy engedélyezve van-e a művelet, kattintson a **műveletek** fülre a GitHub-adattár lapon:

![A művelet engedélyezésének ellenőrzése](./media/github-actions/actions3.png)

Ha a művelet hibát jelez, például ha még nem állította be az Azure-beli hitelesítő adatokat, akkor a hiba kijavítása után újrafuttathatja az ellenőrzéseket. A GitHub-adattár lapon kattintson a **műveletek** elemre, jelölje ki az adott munkafolyamat-feladatot, majd kattintson az **ellenőrzések újrafuttatása** gombra az ellenőrzések újrafuttatásához:

![Ellenőrzések újrafuttatása](./media/github-actions/actions4.png)

## <a name="next-steps"></a>Következő lépések

* [Key Vault a Spring Cloud GitHub-műveletekhez](./spring-cloud-github-actions-key-vault.md)
* [Azure Active Directory egyszerű szolgáltatások](/cli/azure/ad/sp#az-ad-sp-create-for-rbac)
* [GitHub-műveletek az Azure-hoz](https://github.com/Azure/actions/)
