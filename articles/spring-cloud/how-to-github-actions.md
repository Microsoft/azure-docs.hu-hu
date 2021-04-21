---
title: Azure Spring Cloud CI/CD with GitHub Actions
description: CI/CD-munkafolyamat létrehozása Azure Spring Cloud a GitHub Actions
author: MikeDodaro
ms.author: barbkess
ms.service: spring-cloud
ms.topic: how-to
ms.date: 09/08/2020
ms.custom: devx-track-java, devx-track-azurecli
zone_pivot_groups: programming-languages-spring-cloud
ms.openlocfilehash: c52279108a8fd8d5a7ac8bbd7c8eb215097b21b0
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107791354"
---
# <a name="azure-spring-cloud-cicd-with-github-actions"></a>Azure Spring Cloud CI/CD with GitHub Actions

GitHub Actions automatizált szoftverfejlesztési életciklus-munkafolyamatot támogat. A GitHub Actions a Azure Spring Cloud létrehozhat munkafolyamatokat az adattárban az Azure-ban való létrehozáshoz, teszteléshez, csomagoláshoz, kiadáshoz és üzembe helyezéshez. 

## <a name="prerequisites"></a>Előfeltételek
Ehhez a példához az [Azure CLI szükséges.](/cli/azure/install-azure-cli)

::: zone pivot="programming-language-csharp"
## <a name="set-up-github-repository-and-authenticate"></a>GitHub-adattár beállítása és hitelesítés
Az Azure bejelentkezési műveletének hitelesítése érdekében szüksége lesz egy Azure-szolgáltatásnév hitelesítő adataira. Az Azure-beli hitelesítő adatok lekért végrehajtásához hajtsa végre a következő parancsokat a helyi gépen:

```
az login
az ad sp create-for-rbac --role contributor --scopes /subscriptions/<SUBSCRIPTION_ID> --sdk-auth 
```

Egy adott erőforráscsoporthoz való hozzáféréshez csökkentheti a hatókört:

```
az ad sp create-for-rbac --role contributor --scopes /subscriptions/<SUBSCRIPTION_ID>/resourceGroups/<RESOURCE_GROUP> --sdk-auth
```

A parancs kimenete egy JSON-objektum:

```JSON
{
    "clientId": "<GUID>",
    "clientSecret": "<GUID>",
    "subscriptionId": "<GUID>",
    "tenantId": "<GUID>",
    ...
}
```

Ez a példa a [GitHubon találhatócélmintát használja.](https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples/tree/master/steeltoe-sample)  Tárház elágasztatva nyissa meg az adattárház GitHub-lapját, és válassza a **Beállítások** lapot. Nyissa meg **a Titkos kulcsok** menüt, és válassza az Új titkos **elemet:**

 ![Új titkos gombra való felvétel](./media/github-actions/actions1.png)

Állítsa a titkos kód nevét értékre, a értékét pedig arra a JSON-sztringre, amely a GitHub-adattár beállítása és hitelesítése cím alatt `AZURE_CREDENTIALS` *található.*

 ![Titkos adatok beállítása](./media/github-actions/actions2.png)

Az Azure bejelentkezési hitelesítő adatait a GitHub-műveletekben Key Vault az Azure Spring hitelesítése a Key Vault használatával a GitHub-műveletekben [GitHub Actions.](./spring-cloud-github-actions-key-vault.md)

## <a name="provision-service-instance"></a>Szolgáltatáspéldány kiépítése
A szolgáltatáspéldány Azure Spring Cloud az alábbi parancsokat az Azure CLI használatával.

```azurecli
az extension add --name spring-cloud
az group create --location eastus --name <resource group name>
az spring-cloud create -n <service instance name> -g <resource group name>
az spring-cloud config-server git set -n <service instance name> --uri https://github.com/xxx/Azure-Spring-Cloud-Samples --label main --search-paths steeltoe-sample/config
```

## <a name="build-the-workflow"></a>A munkafolyamat létrehozása
A munkafolyamatot az alábbi beállításokkal lehet definiálni.

### <a name="prepare-for-deployment-with-azure-cli"></a>Felkészülés az Üzembe helyezésre az Azure CLI használatával

A parancs `az spring-cloud app create` jelenleg nem idempotent. Ha egyszer futtatja, hibaüzenetet kap, ha újra futtatja ugyanezt a parancsot. Ezt a munkafolyamatot meglévő alkalmazásokkal és Azure Spring Cloud javasoljuk.

Az előkészítéshez használja az alábbi Azure CLI-parancsokat:
```
az configure --defaults group=<service group name>
az configure --defaults spring-cloud=<service instance name>
az spring-cloud app create --name planet-weather-provider
az spring-cloud app create --name solar-system-weather
```

### <a name="deploy-with-azure-cli-directly"></a>Üzembe helyezés közvetlenül az Azure CLI-val

Hozza létre `.github/workflows/main.yml` a fájlt az adattárban az alábbi tartalommal. Cserélje le `<your resource group name>` a és `<your service name>` a értékeket a megfelelő értékekre.

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
## <a name="set-up-github-repository-and-authenticate"></a>GitHub-adattár beállítása és hitelesítés
Az Azure-beli bejelentkezési művelet hitelesítése érdekében szüksége lesz egy Azure-szolgáltatásnév hitelesítő adataira. Az Azure-beli hitelesítő adatok lekért végrehajtásához hajtsa végre a következő parancsokat a helyi gépen:
```
az login
az ad sp create-for-rbac --role contributor --scopes /subscriptions/<SUBSCRIPTION_ID> --sdk-auth 
```
Egy adott erőforráscsoporthoz való hozzáféréshez csökkentheti a hatókört:
```
az ad sp create-for-rbac --role contributor --scopes /subscriptions/<SUBSCRIPTION_ID>/resourceGroups/<RESOURCE_GROUP> --sdk-auth
```
A parancs kimenete egy JSON-objektum:
```JSON
{
    "clientId": "<GUID>",
    "clientSecret": "<GUID>",
    "subscriptionId": "<GUID>",
    "tenantId": "<GUID>",
    ...
}
```

Ez a példa a [PiggyMetrics mintát](https://github.com/Azure-Samples/piggymetrics) használja a GitHubon.  Nyissa meg a minta elágaezését, nyissa meg a GitHub-adattár oldalát, majd kattintson a **Beállítások lapra.** Nyissa meg **a Titkos kulcsok** menüt, és kattintson az Új titkos **gombra:**

 ![Új titkos gombra](./media/github-actions/actions1.png)

Állítsa a titkos kód nevét értékre, a értékét pedig a GitHub-adattár beállítása és hitelesítése cím alatt található `AZURE_CREDENTIALS` *JSON-sztringre.*

 ![Titkos adatok beállítása](./media/github-actions/actions2.png)

Az Azure bejelentkezési hitelesítő adatait a GitHub-műveletekben Key Vault azure-beli bejelentkezési hitelesítő adatokat is lekérhetők az Azure Spring hitelesítése Key Vault a [GitHub Actions.](./spring-cloud-github-actions-key-vault.md)

## <a name="provision-service-instance"></a>Szolgáltatáspéldány kiépítése
A szolgáltatáspéldány Azure Spring Cloud az alábbi parancsokat az Azure CLI használatával.
```
az extension add --name spring-cloud
az group create --location eastus --name <resource group name>
az spring-cloud create -n <service instance name> -g <resource group name>
az spring-cloud config-server git set -n <service instance name> --uri https://github.com/xxx/piggymetrics --label config
```
## <a name="build-the-workflow"></a>A munkafolyamat létrehozása
A munkafolyamatot az alábbi beállításokkal lehet definiálni.

### <a name="prepare-for-deployment-with-azure-cli"></a>Felkészülés az üzembe helyezésre az Azure CLI használatával
A parancs `az spring-cloud app create` jelenleg nem idempotent.  Ezt a munkafolyamatot a meglévő alkalmazásokhoz Azure Spring Cloud példányokhoz javasoljuk.

Az előkészítéshez használja az alábbi Azure CLI-parancsokat:
```
az configure --defaults group=<service group name>
az configure --defaults spring-cloud=<service instance name>
az spring-cloud app create --name gateway
az spring-cloud app create --name auth-service
az spring-cloud app create --name account-service
```

### <a name="deploy-with-azure-cli-directly"></a>Üzembe helyezés közvetlenül az Azure CLI-val
Hozza létre `.github/workflow/main.yml` a fájlt az adattárban:

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
### <a name="deploy-with-azure-cli-action"></a>Üzembe helyezés az Azure CLI-művelet használatával
Az az `run` parancs az Azure CLI legújabb verzióját fogja használni. Ha a változások nem megfelelőek, az Azure CLI egy adott verzióját is használhatja az azure/CLI-hez. `action` 

> [!Note] 
> Ez a parancs egy új tárolóban fog futni, ezért nem fog működni, és a műveletközi fájlelérés `env` további korlátozásokkal is előfordulhat.

Hozza létre a .github/workflow/main.yml fájlt az adattárban:
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
Egy másik lehetőség a [Maven](./spring-cloud-quickstart.md) beépülő modul használata a Jar telepítéséhez és az alkalmazásbeállítások frissítéséhez. A parancs `mvn azure-spring-cloud:deploy` idempotent, és szükség esetén automatikusan létrehozza az alkalmazásokat. Nem kell előre létrehoznia a megfelelő alkalmazásokat.

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

A  GitHub Actions-műveleteket automatikusan engedélyezni kell a `.github/workflow/main.yml` GitHubra való leküldés után. A művelet egy új véglegesítés leküldésekor lesz aktiválva. Ha ezt a fájlt a böngészőben hozza létre, a műveletnek már futnia kell.

Annak ellenőrzéséhez, hogy a művelet engedélyezve van-e, kattintson az Actions (Műveletek) **lapra** a GitHub-adattár oldalán:

![Művelet engedélyezése](./media/github-actions/actions3.png)

Ha a művelet hibásan fut, például ha még nem beállította az Azure-beli hitelesítő adatokat, a hiba kijavítás után újrafuttathatja az ellenőrzéseket. A GitHub-adattár oldalán kattintson az **Actions**(Műveletek) elemre, válassza ki az adott munkafolyamat-feladatot, majd kattintson az **Rerun checks** (Ellenőrzések újrafutása) gombra az ellenőrzések újrafuttatása érdekében:

![Újrafuttatják az ellenőrzéseket](./media/github-actions/actions4.png)

## <a name="next-steps"></a>Következő lépések

* [Key Vault GitHub Spring Cloud műveletekhez](./spring-cloud-github-actions-key-vault.md)
* [Azure Active Directory szolgáltatásnév](/cli/azure/ad/sp#az_ad_sp_create_for_rbac)
* [GitHub-műveletek az Azure-hoz](https://github.com/Azure/actions/)
