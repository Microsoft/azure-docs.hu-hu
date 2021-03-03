---
title: Automatizált közzététel a folyamatos integráció és a kézbesítés terén
description: Megtudhatja, hogyan teheti közzé automatikusan a folyamatos integrációt és a kézbesítést.
ms.service: data-factory
author: nabhishek
ms.author: abnarain
ms.reviewer: maghan
ms.topic: conceptual
ms.date: 02/02/2021
ms.openlocfilehash: 49ec43e59989f3fdad8f5731867953cc7cbb5757
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/03/2021
ms.locfileid: "101699708"
---
# <a name="automated-publishing-for-continuous-integration-and-delivery"></a>Automatizált közzététel a folyamatos integráció és a kézbesítés terén

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

## <a name="overview"></a>Áttekintés

A folyamatos integráció az a gyakorlat, amellyel a rendszer automatikusan és a lehető leghamarabb végezheti el a rendszerbe állítást, és a folyamatos integráció során megjelenő tesztelést követi, és leküldi az átmeneti vagy éles rendszerek módosításait.

Az Azure Data Factoryben a folyamatos integráció és teljesítés (CI/CD) a Data Factory-folyamatoknak az egyik (fejlesztési, tesztelési, éles) környezetből egy másikba történő áthelyezését jelenti. A Azure Data Factory [Azure Resource Manager sablonokat](../azure-resource-manager/templates/overview.md) használ a különböző ADF-entitások (folyamatok, adatkészletek, adatforgalom stb.) konfigurációjának tárolásához. Két javasolt módszer áll rendelkezésre az adatelőállító más környezetbe való előléptetéséhez:

- Automatikus üzembe helyezés a Data Factory Azure- [folyamatokkal](/azure/devops/pipelines/get-started/what-is-azure-pipelines)való integrálásával.
- Manuálisan tölthet fel egy Resource Manager-sablont a Data Factory UX-integrációval Azure Resource Manager használatával.

További információ: [folyamatos integráció és kézbesítés Azure Data Factoryban](continuous-integration-deployment.md).

Ebben a cikkben a folyamatos üzembe helyezés fejlesztésére és a CI/CD-hez készült automatizált közzétételi funkcióra koncentrálunk.

## <a name="continuous-deployment-improvements"></a>Folyamatos üzembe helyezési fejlesztések

Az "automatikus közzététel" funkció az ADF UX-ből az *összes* és az export *Azure Resource Manager (ARM) sablon* funkcióit veszi igénybe, és a logikát nyilvánosan elérhető NPM-csomagon keresztül teszi lehetővé [@microsoft/azure-data-factory-utilities](https://www.npmjs.com/package/@microsoft/azure-data-factory-utilities) . Ez lehetővé teszi, hogy programozott módon aktiválja ezeket a műveleteket ahelyett, hogy az ADF felhasználói felületére ugorjon, majd kattintson a gombra. Így a CI/CD-folyamatok igazabb folyamatos integrációs élményt biztosítanak.

### <a name="current-cicd-flow"></a>Jelenlegi CI/CD-folyamat

1. Minden felhasználó a saját fiókjaiban végez módosításokat.
2. A leküldéses főkiszolgáló tiltott, a felhasználóknak létre kell hozniuk egy PR-t a főkiszolgálón a módosítások elvégzéséhez.
3. A felhasználóknak be kell töltenie az ADF felhasználói felületét, majd a közzététel elemre kattintva központilag kell telepíteni a Data Factory és az ARM-sablonokat a közzétételi ágban.
4. A DevOps kiadási folyamat úgy van beállítva, hogy új kiadást hozzon létre, és minden alkalommal telepítse az ARM-sablont, amikor egy új módosítást küldenek a közzétételi ágra.

![Jelenlegi CI/CD-folyamat](media/continuous-integration-deployment-improvements/current-ci-cd-flow.png)

### <a name="manual-step"></a>Manuális lépés

A jelenlegi CI/CD-folyamatokban az UX az ARM-sablon létrehozásához szükséges közvetítő, ezért a felhasználónak az ADF felhasználói felületét kell megadnia, és manuálisan kell kattintania a Közzététel gombra, hogy elindítsa az ARM-sablon létrehozását, és eldobja azt a közzétételi ágra, amely egy kis Hack.

### <a name="the-new-cicd-flow"></a>Az új CI/CD-folyamat

1. Minden felhasználó a saját fiókjaiban végez módosításokat.
2. A leküldéses főkiszolgáló tiltott, a felhasználóknak létre kell hozniuk egy PR-t a főkiszolgálón a módosítások elvégzéséhez.
3. **Az Azure DevOps pipeline-Build minden alkalommal aktiválódik, amikor új véglegesítik a főkiszolgálót, érvényesíti az erőforrásokat, és egy ARM-sablont hoz létre, ha az érvényesítés sikeres.**
4. A DevOps kiadási folyamat úgy van beállítva, hogy új kiadást hozzon létre, és minden alkalommal telepítse az ARM-sablont, amikor egy új Build elérhetővé válik. 

![Új CI/CD-folyamat](media/continuous-integration-deployment-improvements/new-ci-cd-flow.png)

### <a name="what-changed"></a>Mi változott?

- Most már van egy DevOps-létrehozási folyamatot használó Build folyamata.
- A build folyamat ADFUtilities NPM csomagot használ, amely ellenőrzi az összes erőforrást, és létrehozza az ARM-sablonokat (egyetlen és csatolt sablon).
- A létrehozási folyamat feladata az ADF-erőforrások ellenőrzése és az ARM-sablon létrehozása az ADF felhasználói felületének (közzététel gomb) helyett.
- A DevOps kiadás definíciója mostantól ezt az új Build folyamatot fogja használni a git-összetevő helyett.

> [!NOTE]
> Továbbra is használhatja a meglévő mechanizmust (adf_publish ág), vagy használhatja az új folyamatot. Mindkettő támogatott. 

## <a name="package-overview"></a>Csomag – áttekintés

Jelenleg két parancs érhető el a csomagban:
- ARM-sablon exportálása
- Érvényesítés

### <a name="export-arm-template"></a>ARM-sablon exportálása

Futtassa a NPM Run Start export <rootFolder> <factoryId> [outputFolder] parancsot az ARM-sablon exportálásához egy adott mappa erőforrásainak használatával. Ez a parancs az ARM-sablon létrehozása előtt is futtat egy érvényesítési ellenőrzést. Az alábbiakban egy példa látható:

```
npm run start export C:\DataFactories\DevDataFactory /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/testResourceGroup/providers/Microsoft.DataFactory/factories/DevDataFactory ArmTemplateOutput
```

- A gyökérmappa egy kötelező mező, amely a Data Factory erőforrásainak helyét jelöli.
- A FactoryId egy kötelező mező, amely a (z) "/Subscriptions/ <subId> /ResourceGroups//Providers/Microsoft.DataFactory/factories/" formátumban adja meg az adatgyár-erőforrás azonosítóját <rgName> <dfName> .
- A OutputFolder egy opcionális paraméter, amely a generált ARM-sablon mentéséhez használt relatív elérési utat határozza meg.
 
> [!NOTE]
> A generált ARM-sablon nincs közzétéve a `Live` gyár verziójában. Az üzembe helyezést CI/CD-folyamat használatával kell elvégezni. 
 

### <a name="validate"></a>Érvényesítés

Futtassa az NPM Run Start validate parancsot <rootFolder> <factoryId> egy adott mappa összes erőforrásának ellenőrzéséhez. Az alábbiakban egy példa látható:
    
```
npm run start validate C:\DataFactories\DevDataFactory /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/testResourceGroup/providers/Microsoft.DataFactory/factories/DevDataFactory
```

- A gyökérmappa egy kötelező mező, amely a Data Factory erőforrásainak helyét jelöli.
- A FactoryId egy kötelező mező, amely a (z) "/Subscriptions/ <subId> /ResourceGroups//Providers/Microsoft.DataFactory/factories/" formátumban adja meg az adatgyár-erőforrás azonosítóját <rgName> <dfName> .


## <a name="create-an-azure-pipeline"></a>Azure-folyamat létrehozása

Míg a NPM csomagok különböző módokon használhatók fel, az egyik elsődleges előnyt egy [Azure-folyamaton](https://nam06.safelinks.protection.outlook.com/?url=https:%2F%2Fdocs.microsoft.com%2F%2Fazure%2Fdevops%2Fpipelines%2Fget-started%2Fwhat-is-azure-pipelines%3Fview%3Dazure-devops%23:~:text%3DAzure%2520Pipelines%2520is%2520a%2520cloud%2Cit%2520available%2520to%2520other%2520users.%26text%3DAzure%2520Pipelines%2520combines%2520continuous%2520integration%2Cship%2520it%2520to%2520any%2520target.&data=04%7C01%7Cabnarain%40microsoft.com%7C5f064c3d5b7049db540708d89564b0bc%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C1%7C637423607000268277%7CUnknown%7CTWFpbGZsb3d8eyJWIjoiMC4wLjAwMDAiLCJQIjoiV2luMzIiLCJBTiI6Ik1haWwiLCJXVCI6Mn0%3D%7C1000&sdata=jo%2BkIvSBiz6f%2B7kmgqDN27TUWc6YoDanOxL9oraAbmA%3D&reserved=0)keresztül fogyasztjuk. Az együttműködési ág minden egyesítése után egy folyamat indítható el, amely először ellenőrzi az összes kódot, majd exportálja az ARM-sablont egy olyan [Build](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Fdocs.microsoft.com%2F%2Fazure%2Fdevops%2Fpipelines%2Fartifacts%2Fbuild-artifacts%3Fview%3Dazure-devops%26tabs%3Dyaml%23how-do-i-consume-artifacts&data=04%7C01%7Cabnarain%40microsoft.com%7C5f064c3d5b7049db540708d89564b0bc%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C1%7C637423607000278113%7CUnknown%7CTWFpbGZsb3d8eyJWIjoiMC4wLjAwMDAiLCJQIjoiV2luMzIiLCJBTiI6Ik1haWwiLCJXVCI6Mn0%3D%7C1000&sdata=dN3t%2BF%2Fzbec4F28hJqigGANvvedQoQ6npzegTAwTp1A%3D&reserved=0) -összetevőbe, amelyet egy kiadási folyamat felhasználhat. **Miben különbözik az aktuális CI/CD-folyamattól, hogy a kiadási folyamatot a meglévő ág helyett ezen az összetevőn fogja irányítani `adf_publish` .**

Az első lépésekhez kövesse az alábbi lépéseket:

1.  Nyisson meg egy Azure DevOps-projektet, és válassza a "folyamatok" lehetőséget. Válassza az "új folyamat" lehetőséget.

    ![Új folyamat](media/continuous-integration-deployment-improvements/new-pipeline.png)
    
2.  Válassza ki azt a tárházat, ahová menteni szeretné a folyamat YAML parancsfájlját. Azt javasoljuk, hogy az ADF-erőforrások ugyanazon tárházában lévő *Build* mappában mentse azt. Győződjön meg arról, hogy a tárházban található fájl **package.js** , valamint a csomag nevét tartalmazza (az alábbi példában látható módon).

    ```json
    {
        "scripts":{
            "build":"node node_modules/@microsoft/azure-data-factory-utilities/lib/index"
        },
        "dependencies":{
            "@microsoft/azure-data-factory-utilities":"^0.1.3"
        }
    } 
    ```
    
3.  Válassza ki a *kezdő folyamat* elemet. Ha feltöltötte vagy egyesítette a YAML-fájlt (ahogy az az alábbi példában is látható), azt is megteheti, hogy közvetlenül erre a lehetőségre kattint, és szerkeszti azt. 

    ![Kezdő folyamat](media/continuous-integration-deployment-improvements/starter-pipeline.png) 

    ```yaml
    # Sample YAML file to validate and export an ARM template into a Build Artifact
    # Requires a package.json file located in the target repository
    
    trigger:
    - main #collaboration branch
    
    pool:
      vmImage: 'ubuntu-latest'
    
    steps:
    
    # Installs Node and the npm packages saved in your package.json file in the build
    
    - task: NodeTool@0
      inputs:
        versionSpec: '10.x'
      displayName: 'Install Node.js'
    
    - task: Npm@1
      inputs:
        command: 'install'
        verbose: true
      displayName: 'Install npm package'
    
    # Validates all of the ADF resources in the repository. You will get the same validation errors as when "Validate All" is clicked
    # Enter the appropriate subscription and name for the source factory 
    
    - task: Npm@1
      inputs:
        command: 'custom'
        customCommand: 'run build validate $(Build.Repository.LocalPath) /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/testResourceGroup/providers/Microsoft.DataFactory/factories/yourFactoryName'
      displayName: 'Validate'
    
    # Validate and then generate the ARM template into the destination folder. Same as clicking "Publish" from UX
    # The ARM template generated is not published to the ‘Live’ version of the factory. Deployment should be done using a CI/CD pipeline. 
    
    - task: Npm@1
      inputs:
        command: 'custom'
        customCommand: 'run build export $(Build.Repository.LocalPath) /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/testResourceGroup/providers/Microsoft.DataFactory/factories/yourFactoryName "ArmTemplate"'
      displayName: 'Validate and Generate ARM template'
    
    # Publish the Artifact to be used as a source for a release pipeline
    
    - task: PublishPipelineArtifact@1
      inputs:
        targetPath: '$(Build.Repository.LocalPath)/ArmTemplate'
        artifact: 'ArmTemplates'
        publishLocation: 'pipeline'
    ```

4.  Adja meg a YAML kódját. Azt javasoljuk, hogy a YAML-fájlt kiindulási pontként használja.
5.  Mentés és Futtatás. A YAML használatakor a rendszer minden alkalommal elindítja az aktiválást, amikor a "fő" ág frissül.

## <a name="next-steps"></a>Következő lépések

További információk a folyamatos integrációról és a Data Factory történő kézbesítésről: 

- [Folyamatos integráció és kézbesítés Azure Data Factoryban](continuous-integration-deployment.md).
