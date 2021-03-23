---
title: Automatizált közzététel a folyamatos integráció és a kézbesítés terén
description: Megtudhatja, hogyan teheti közzé automatikusan a folyamatos integrációt és a kézbesítést.
ms.service: data-factory
author: nabhishek
ms.author: abnarain
ms.reviewer: jburchel
ms.topic: conceptual
ms.date: 02/02/2021
ms.openlocfilehash: b2c48fcc11feaec3efc0acab283609181b92a3dc
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/22/2021
ms.locfileid: "104780463"
---
# <a name="automated-publishing-for-continuous-integration-and-delivery"></a>Automatizált közzététel a folyamatos integráció és a kézbesítés terén

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

## <a name="overview"></a>Áttekintés

A folyamatos integráció a kódbázis módosításainak automatikus tesztelését jelenti. A folyamatos teljesítés a lehető leghamarabb a folyamatos integráció során megjelenő tesztelést követi, és leküldi a módosításokat egy átmeneti vagy éles rendszerbe.

Azure Data Factory a folyamatos integráció és a folyamatos teljesítés (CI/CD) a Data Factory folyamatok egyik környezetből, például a fejlesztésből, a tesztelésből és a termelésből egy másikba való áthelyezését jelenti. A Data Factory [Azure Resource Manager sablonokat (ARM-sablonokat)](../azure-resource-manager/templates/overview.md) használ a különböző Data Factory entitások, például folyamatok, adatkészletek és adatfolyamatok konfigurációjának tárolásához.

Két javasolt módszer áll rendelkezésre az adatelőállító más környezetbe való előléptetéséhez:

- Automatikus üzembe helyezés a Data Factory és az [Azure-folyamatok](/azure/devops/pipelines/get-started/what-is-azure-pipelines)integrálásával.
- ARM-sablon manuális feltöltése Data Factory felhasználói élmény Azure Resource Manager használatával történő integrálásával.

További információ: [folyamatos integráció és kézbesítés Azure Data Factoryban](continuous-integration-deployment.md).

Ez a cikk a folyamatos üzembe helyezés fejlesztéseit és a CI/CD automatikus közzétételi funkcióját ismerteti.

## <a name="continuous-deployment-improvements"></a>Folyamatos üzembe helyezési fejlesztések

Az automatikus közzétételi funkció a Data Factory felhasználói élményben fogadja el az **összes** és az **export ARM-sablon** funkcióit, és egy nyilvánosan elérhető NPM-csomagon keresztül teszi a logikát [@microsoft/azure-data-factory-utilities](https://www.npmjs.com/package/@microsoft/azure-data-factory-utilities) . Ezért programozott módon elindíthatja ezeket a műveleteket ahelyett, hogy a Data Factory felhasználói felületére ugorjon, és egy gombot manuálisan kell kiválasztania. Ezzel a képességgel a CI/CD-folyamatok igazabb folyamatos integrációs élményt biztosítanak.

### <a name="current-cicd-flow"></a>Jelenlegi CI/CD-folyamat

1. Minden felhasználó a saját fiókjaiban végez módosításokat.
1. A leküldéses főkiszolgáló nem engedélyezett. A felhasználóknak egy lekéréses kérelmet kell létrehozniuk a módosítások elvégzéséhez.
1. A felhasználóknak be kell tölteniük a Data Factory felhasználói felületet, és a **Közzététel** elemre kattintva telepíteniük kell a módosításokat a Data Factory és az ARM-sablonokat a közzétételi ágban kell előállítani.
1. A DevOps kiadási folyamat úgy van beállítva, hogy új kiadást hozzon létre, és minden alkalommal telepítse az ARM-sablont, amikor egy új módosítást küldenek a közzétételi ágra.

![Az aktuális CI/CD folyamatot bemutató diagram.](media/continuous-integration-deployment-improvements/current-ci-cd-flow.png)

### <a name="manual-step"></a>Manuális lépés

Az aktuális CI/CD-folyamat során a felhasználói élmény az ARM-sablon létrehozása. Ennek eredményeképpen a felhasználónak meg kell nyitnia a Data Factory felhasználói felületet, és manuálisan kell kiválasztania a **Közzététel** elemet az ARM-sablon létrehozásának elindításához és a közzétételi ág eldobásához.

### <a name="the-new-cicd-flow"></a>Az új CI/CD-folyamat

1. Minden felhasználó a saját fiókjaiban végez módosításokat.
1. A leküldéses főkiszolgáló nem engedélyezett. A felhasználóknak egy lekéréses kérelmet kell létrehozniuk a módosítások elvégzéséhez.
1. Az Azure DevOps pipeline-Build minden alkalommal aktiválódik, amikor új véglegesítik a főkiszolgálót. Ellenőrzi az erőforrásokat, és egy ARM-sablont hoz létre, ha az érvényesítés sikeres.
1. A DevOps kiadási folyamat úgy van beállítva, hogy új kiadást hozzon létre, és minden alkalommal telepítse az ARM-sablont, amikor egy új Build elérhetővé válik.

![Az új CI/CD folyamatot bemutató diagram.](media/continuous-integration-deployment-improvements/new-ci-cd-flow.png)

### <a name="what-changed"></a>Mi változott?

- Most már van egy DevOps-létrehozási folyamatot használó Build folyamata.
- A build folyamat a ADFUtilities NPM csomagot használja, amely ellenőrzi az összes erőforrást, és létrehozza az ARM-sablonokat. Ezek a sablonok lehetnek egyetlenek és összekapcsolhatók.
- A létrehozási folyamat feladata a Data Factory erőforrások ellenőrzése és az ARM-sablon létrehozása a Data Factory felhasználói felület (**Közzététel** gomb) helyett.
- A DevOps kiadás definíciója mostantól ezt az új Build folyamatot fogja használni a git-összetevő helyett.

> [!NOTE]
> Továbbra is használhatja a meglévő mechanizmust, amely az `adf_publish` ág, vagy használhatja az új folyamatot. Mindkettő támogatott.

## <a name="package-overview"></a>Csomag – áttekintés

Jelenleg két parancs érhető el a csomagban:

- ARM-sablon exportálása
- Érvényesítés

### <a name="export-arm-template"></a>ARM-sablon exportálása

Futtassa `npm run start export <rootFolder> <factoryId> [outputFolder]` a parancsot az ARM-sablon exportálásához egy adott mappa erőforrásainak használatával. Ez a parancs az ARM-sablon létrehozása előtt is futtat egy érvényesítési ellenőrzést. Bemutatunk egy példát:

```
npm run start export C:\DataFactories\DevDataFactory /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/testResourceGroup/providers/Microsoft.DataFactory/factories/DevDataFactory ArmTemplateOutput
```

- `RootFolder` egy kötelező mező, amely a Data Factory erőforrásainak helyét jelöli.
- `FactoryId` egy kötelező mező, amely a Data Factory erőforrás-azonosítót jelöli a formátumban `/subscriptions/<subId>/resourceGroups/<rgName>/providers/Microsoft.DataFactory/factories/<dfName>` .
- `OutputFolder` egy nem kötelező paraméter, amely a generált ARM-sablon mentéséhez használt relatív elérési utat határozza meg.
 
> [!NOTE]
> A létrehozott ARM-sablon nincs közzétéve a gyár élő verziójában. Az üzembe helyezést CI/CD-folyamat használatával kell elvégezni.
 
### <a name="validate"></a>Érvényesítés

`npm run start validate <rootFolder> <factoryId>`Egy adott mappa összes erőforrásának érvényesítéséhez futtassa a parancsot. Bemutatunk egy példát:

```
npm run start validate C:\DataFactories\DevDataFactory /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/testResourceGroup/providers/Microsoft.DataFactory/factories/DevDataFactory
```

- `RootFolder` egy kötelező mező, amely a Data Factory erőforrásainak helyét jelöli.
- `FactoryId` egy kötelező mező, amely a Data Factory erőforrás-azonosítót jelöli a formátumban `/subscriptions/<subId>/resourceGroups/<rgName>/providers/Microsoft.DataFactory/factories/<dfName>` .

## <a name="create-an-azure-pipeline"></a>Azure-folyamat létrehozása

Habár a NPM-csomagok különböző módokon használhatók fel, az egyik elsődleges előnyt az [Azure-folyamaton](https://nam06.safelinks.protection.outlook.com/?url=https:%2F%2Fdocs.microsoft.com%2F%2Fazure%2Fdevops%2Fpipelines%2Fget-started%2Fwhat-is-azure-pipelines%3Fview%3Dazure-devops%23:~:text%3DAzure%2520Pipelines%2520is%2520a%2520cloud%2Cit%2520available%2520to%2520other%2520users.%26text%3DAzure%2520Pipelines%2520combines%2520continuous%2520integration%2Cship%2520it%2520to%2520any%2520target.&data=04%7C01%7Cabnarain%40microsoft.com%7C5f064c3d5b7049db540708d89564b0bc%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C1%7C637423607000268277%7CUnknown%7CTWFpbGZsb3d8eyJWIjoiMC4wLjAwMDAiLCJQIjoiV2luMzIiLCJBTiI6Ik1haWwiLCJXVCI6Mn0%3D%7C1000&sdata=jo%2BkIvSBiz6f%2B7kmgqDN27TUWc6YoDanOxL9oraAbmA%3D&reserved=0)keresztül lehet használni. Az együttműködési ág minden egyesítése után egy folyamat indítható el, amely először ellenőrzi az összes kódot, majd exportálja az ARM-sablont egy olyan [Build](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Fdocs.microsoft.com%2F%2Fazure%2Fdevops%2Fpipelines%2Fartifacts%2Fbuild-artifacts%3Fview%3Dazure-devops%26tabs%3Dyaml%23how-do-i-consume-artifacts&data=04%7C01%7Cabnarain%40microsoft.com%7C5f064c3d5b7049db540708d89564b0bc%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C1%7C637423607000278113%7CUnknown%7CTWFpbGZsb3d8eyJWIjoiMC4wLjAwMDAiLCJQIjoiV2luMzIiLCJBTiI6Ik1haWwiLCJXVCI6Mn0%3D%7C1000&sdata=dN3t%2BF%2Fzbec4F28hJqigGANvvedQoQ6npzegTAwTp1A%3D&reserved=0) -összetevőbe, amelyet egy kiadási folyamat felhasználhat. Miben különbözik az aktuális CI/CD-folyamattól, hogy *a kiadási folyamatot a meglévő `adf_publish` ág helyett ezen* az összetevőn fogja irányítani.

A kezdéshez kövesse az alábbi lépéseket:

1.  Nyisson meg egy Azure DevOps-projektet, és lépjen a **folyamatok** elemre. Válassza a **New pipeline** (Új folyamat) lehetőséget.

    ![Képernyőkép, amely az új folyamat gombot jeleníti meg.](media/continuous-integration-deployment-improvements/new-pipeline.png)
    
1.  Válassza ki azt a tárházat, amelybe menteni szeretné a folyamat YAML parancsfájlját. Azt javasoljuk, hogy a Data Factory erőforrásainak ugyanazon tárházában lévő Build mappában mentse azt. Győződjön meg arról, hogy a csomag nevét tartalmazó adattárban van egy *package.jsa* következő példában látható módon:

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
    
1.  Válassza ki a **kezdő folyamat** elemet. Ha feltöltötte vagy egyesítette a YAML-fájlt, ahogy az az alábbi példában is látható, akkor közvetlenül is rámutathat, és szerkesztheti azt.

    ![Az alapszintű folyamatot bemutató képernyőkép.](media/continuous-integration-deployment-improvements/starter-pipeline.png)

    ```yaml
    # Sample YAML file to validate and export an ARM template into a build artifact
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
    
    # Validates all of the Data Factory resources in the repository. You'll get the same validation errors as when "Validate All" is selected.
    # Enter the appropriate subscription and name for the source factory.
    
    - task: Npm@1
      inputs:
        command: 'custom'
        customCommand: 'run build validate $(Build.Repository.LocalPath) /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/testResourceGroup/providers/Microsoft.DataFactory/factories/yourFactoryName'
      displayName: 'Validate'
    
    # Validate and then generate the ARM template into the destination folder, which is the same as selecting "Publish" from the UX.
    # The ARM template generated isn't published to the live version of the factory. Deployment should be done by using a CI/CD pipeline. 
    
    - task: Npm@1
      inputs:
        command: 'custom'
        customCommand: 'run build export $(Build.Repository.LocalPath) /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/testResourceGroup/providers/Microsoft.DataFactory/factories/yourFactoryName "ArmTemplate"'
      displayName: 'Validate and Generate ARM template'
    
    # Publish the artifact to be used as a source for a release pipeline.
    
    - task: PublishPipelineArtifact@1
      inputs:
        targetPath: '$(Build.Repository.LocalPath)/ArmTemplate'
        artifact: 'ArmTemplates'
        publishLocation: 'pipeline'
    ```

1.  Adja meg a YAML kódját. Azt javasoljuk, hogy kiindulási pontként használja a YAML-fájlt.
1.  Mentés és Futtatás. Ha a YAML használta, a rendszer a fő ág frissítésekor minden alkalommal elindul.

## <a name="next-steps"></a>Következő lépések

További információk a folyamatos integrációról és a Data Factory történő kézbesítésről:

- [Folyamatos integráció és kézbesítés Azure Data Factoryban](continuous-integration-deployment.md).
