---
title: CI/CD az Azure Spring Cloud-hoz
description: CI/CD az Azure Spring Cloud-hoz
author: bmitchell287
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 09/08/2020
ms.author: brendm
ms.custom: devx-track-java, devx-track-azurecli
zone_pivot_groups: programming-languages-spring-cloud
ms.openlocfilehash: ff54c1dc62189f2a00528c6a82abe26c667c5538
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/04/2021
ms.locfileid: "102038696"
---
# <a name="cicd-for-azure-spring-cloud"></a>CI/CD az Azure Spring Cloud-hoz

A folyamatos integráció és a folyamatos kézbesítési eszközök lehetővé teszik a meglévő alkalmazások frissítéseinek gyors üzembe helyezését minimális erőfeszítéssel és kockázattal. Az Azure DevOps segítségével rendszerezheti és kezelheti ezeket a kulcsfontosságú feladatokat. Az Azure Spring Cloud jelenleg nem biztosít konkrét Azure DevOps beépülő modult.  A Spring Cloud-alkalmazásokat azonban integrálhatja a DevOps egy [Azure CLI-feladat](/azure/devops/pipelines/tasks/deploy/azure-cli?preserve-view=true&view=azure-devops)használatával.

Ebből a cikkből megtudhatja, hogyan használható az Azure Spring Cloud Azure CLI-feladata az Azure DevOps való integráláshoz.

## <a name="create-an-azure-resource-manager-service-connection"></a>Azure Resource Manager szolgáltatási kapcsolatok létrehozása

[Ebből a cikkből](/azure/devops/pipelines/library/connect-to-azure?preserve-view=true&view=azure-devops) megtudhatja, hogyan hozhat létre Azure Resource Manager szolgáltatás-kapcsolódást az Azure DevOps-projekthez. Ügyeljen arra, hogy ugyanazt az előfizetést válassza, amelyet az Azure Spring Cloud Service-példányhoz használ.

## <a name="azure-cli-task-templates"></a>Azure CLI-feladatok sablonjai
::: zone pivot="programming-language-csharp"
### <a name="deploy-artifacts"></a>Összetevők üzembe helyezése

Projektjeit a sorozatának használatával hozhatja létre és helyezheti üzembe `tasks` . Ez a kódrészlet változókat, .NET Core-feladatot definiál az alkalmazás létrehozásához, valamint egy Azure CLI-feladatot a *. zip* fájl telepítéséhez.

```yaml
variables:
  workingDirectory: './steeltoe-sample'
  planetMainEntry: 'Microsoft.Azure.SpringCloud.Sample.PlanetWeatherProvider.dll'
  solarMainEntry: 'Microsoft.Azure.SpringCloud.Sample.SolarSystemWeather.dll'
  planetAppName: 'planet-weather-provider'
  solarAppName: 'solar-system-weather'
  serviceName: '<your service name>'
  resourceGroupName: '<your resource group name>'

steps:
# Restore, build, publish and package the zipped planet app
- task: DotNetCoreCLI@2
  inputs:
    command: 'publish'
    publishWebProjects: false
    arguments: '--configuration Release'
    zipAfterPublish: false
    modifyOutputPath: false
    workingDirectory: $(workingDirectory)

# Configure Azure CLI and install spring-cloud extension
- task: AzureCLI@1
  inputs:
    azureSubscription: '<your subscription>'
    scriptLocation: 'inlineScript'
    inlineScript: |
      az extension add --name spring-cloud --y
      az configure --defaults group=${{ variables.resourceGroupName }}
      az configure --defaults spring-cloud=${{ variables.serviceName }}
      az spring-cloud app deploy -n ${{ variables.planetAppName }} --runtime-version NetCore_31 --main-entry ${{ variables.planetMainEntry }} --artifact-path ./${{ variables.planetAppName }}/publish-deploy-planet.zip
      az spring-cloud app deploy -n ${{ variables.solarAppName }} --runtime-version NetCore_31 --main-entry ${{ variables.solarMainEntry }} --artifact-path ./${{ variables.solarAppName }}/publish-deploy-solar.zip
      az spring-cloud app update -n ${{ variables.solarAppName }} --assign-endpoint
      az spring-cloud app show -n ${{ variables.solarAppName }} -o table
    workingDirectory: '${{ variables.workingDirectory }}/src'
```

::: zone-end
::: zone pivot="programming-language-java"
### <a name="deploy-artifacts"></a>Összetevők üzembe helyezése

Projektjeit a sorozatának használatával hozhatja létre és helyezheti üzembe `tasks` . Ez a kódrészlet először definiál egy Maven-feladatot az alkalmazás létrehozásához, majd egy második feladatot, amely az Azure Spring Cloud Azure CLI-bővítmény használatával telepíti a JAR-fájlt.

```yaml
steps:
- task: Maven@3
  inputs:
    mavenPomFile: 'pom.xml'
- task: AzureCLI@1
  inputs:
    azureSubscription: <your service connection name>
    scriptLocation: inlineScript
    inlineScript: |
      az extension add -y --name spring-cloud
      az spring-cloud app deploy --resource-group <your-resource-group> --service <your-spring-cloud-service> --name <app-name> --jar-path ./target/your-result-jar.jar
      # deploy other app
```

### <a name="deploy-from-source"></a>Üzembe helyezés forrásból

A közvetlenül az Azure-ba történő üzembe helyezése külön fordítási lépés nélkül lehetséges.

```yaml
- task: AzureCLI@1
  inputs:
    azureSubscription: <your service connection name>
    scriptLocation: inlineScript
    inlineScript: |
      az extension add -y --name spring-cloud
      az spring-cloud app deploy --resource-group <your-resource-group> --service <your-spring-cloud-service> --name <app-name>

      # or if it is a multi-module project
      az spring-cloud app deploy --resource-group <your-resource-group> --service <your-spring-cloud-service> --name <app-name> --target-module relative/path/to/module
```
::: zone-end

## <a name="next-steps"></a>Következő lépések

* [Rövid útmutató: az első Azure Spring Cloud-alkalmazás üzembe helyezése](spring-cloud-quickstart.md)