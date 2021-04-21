---
title: Automatikus közzététel a folyamatos integrációhoz és teljesítéshez
description: Ismerje meg, hogyan tehet közzé automatikus közzétételt a folyamatos integráció és teljesítés érdekében.
ms.service: data-factory
author: nabhishek
ms.author: abnarain
ms.reviewer: jburchel
ms.topic: conceptual
ms.date: 02/02/2021
ms.openlocfilehash: 5730b0c7e522f7496f578ffebf716957fcaa56b0
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107788929"
---
# <a name="automated-publishing-for-continuous-integration-and-delivery"></a>Automatikus közzététel a folyamatos integrációhoz és teljesítéshez

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

## <a name="overview"></a>Áttekintés

A folyamatos integráció a kódbázis módosításainak automatikus tesztelését jelenti. A folyamatos teljesítés a lehető leghamarabb követi a folyamatos integráció során végzett tesztelést, és a módosításokat egy átmeneti vagy éles rendszerbe továbbkullja.

A Azure Data Factory folyamatos integráció és folyamatos teljesítés (CI/CD) azt jelenti, hogy Data Factory folyamatok egyik környezetből , például a fejlesztésből, a tesztelésből és az éles környezetből egy másikba vannak átköltöztve. Data Factory [sablonokat Azure Resource Manager (ARM-sablonokat)](../azure-resource-manager/templates/overview.md) használ a különböző Data Factory-entitások, például folyamatok, adatkészletek és adatfolyamok konfigurációjának tárolására.

Két javasolt módszer létezik az adat-előállító másik környezetbe való meg előléptetheti:

- Automatizált üzembe helyezés az Azure [Pipelines](/azure/devops/pipelines/get-started/what-is-azure-pipelines)Data Factory használatával.
- ARM-sablon manuális feltöltése a felhasználói Data Factory integrációja és a Azure Resource Manager.

További információ: Folyamatos integráció és [teljesítés a Azure Data Factory.](continuous-integration-deployment.md)

Ez a cikk a folyamatos üzembe helyezés fejlesztésére és a CI/CD automatikus közzétételi funkcióra összpontosít.

## <a name="continuous-deployment-improvements"></a>Folyamatos üzembe helyezés fejlesztései

Az automatizált közzétételi  funkció az Összes ellenőrzése és az **ARM-sablon** exportálása funkciót Data Factory felhasználói felületről, és a logikát egy nyilvánosan elérhető npm-csomagon keresztül használhatóként teszi [@microsoft/azure-data-factory-utilities](https://www.npmjs.com/package/@microsoft/azure-data-factory-utilities) elérhetővé. Ezért ezeket a műveleteket programozott módon is aktiválhatja ahelyett, hogy a felhasználói felületre kellene Data Factory, és manuálisan kellene kiválasztania egy gombot. Ez a képesség valódibb folyamatos integrációs élményt biztosít a CI-/CD-folyamatok számára.

### <a name="current-cicd-flow"></a>Aktuális CI/CD-folyamat

1. Minden felhasználó módosítja a saját privát ágát.
1. A főkiszolgálóra való leküldés nem engedélyezett. A felhasználóknak lekéréses kérelmet kell létrehozniuk a módosítások létrehozására.
1. A felhasználóknak be kell tölteniük  a Data Factory felhasználói felületét, és a Közzététel lehetőséget választva üzembe kell helyezniük a módosításokat a Data Factory és létre kell hozniuk az ARM-sablonokat a közzétételi ágban.
1. A DevOps-kiadási folyamat úgy van konfigurálva, hogy új kiadást hozzon létre, és minden alkalommal üzembe helyez egy ARM-sablont, amikor új módosítás lesz lekért a közzétételi ágba.

![Az aktuális CI/CD-folyamatot bemutató diagram.](media/continuous-integration-deployment-improvements/current-ci-cd-flow.png)

### <a name="manual-step"></a>Manuális lépés

A jelenlegi CI/CD-folyamat során a felhasználói élmény az ARM-sablon közvetítői létrehozása. Ennek eredményeképpen a felhasználónak a Data Factory felhasználói felületén  manuálisan kell kiválasztania a Publish (Közzététel) lehetőséget az ARM-sablon generálása és a közzétételi ágba való eldobása érdekében.

### <a name="the-new-cicd-flow"></a>Az új CI/CD-folyamat

1. Minden felhasználó módosításokat tesz a privát ágakban.
1. A főkiszolgálóra való leküldés nem engedélyezett. A felhasználóknak lekéréses kérelmet kell létrehozniuk a módosítások létrehozására.
1. Az Azure DevOps-folyamat build minden alkalommal aktiválódik, amikor új véglegesítést hoznak létre a főkiszolgálón. Ellenőrzi az erőforrásokat, és ha az érvényesítés sikeres, létrehoz egy ARM-sablont összetevőként.
1. A DevOps-kiadási folyamat úgy van konfigurálva, hogy új kiadást hozzon létre, és minden alkalommal üzembe helyez egy ARM-sablont, amikor új build érhető el.

![Az új CI/CD-folyamatot bemutató diagram.](media/continuous-integration-deployment-improvements/new-ci-cd-flow.png)

### <a name="what-changed"></a>Mi változott?

- Most már van egy buildfolyamatunk, amely DevOps buildfolyamatot használ.
- A build folyamat az ADFUtilities NPM-csomagot használja, amely érvényesíti az összes erőforrást, és létrehozza az ARM-sablonokat. Ezek a sablonok egyszeresek és összekapcsolhatók.
- A build folyamat feladata a Data Factory és az ARM-sablon létrehozása az Data Factory **felhasználói** felület (Közzététel gomb) helyett.
- A DevOps kiadási definíciója mostantól ezt az új build folyamatot használja a Git-összetevő helyett.

> [!NOTE]
> Továbbra is használhatja a meglévő mechanizmust, azaz a ágat, vagy használhatja az `adf_publish` új folyamatot. Mindkettő támogatott.

## <a name="package-overview"></a>Csomag – áttekintés

A csomagban jelenleg két parancs érhető el:

- ARM-sablon exportálása
- Érvényesítés

### <a name="export-arm-template"></a>ARM-sablon exportálása

Futtassa `npm run start export <rootFolder> <factoryId> [outputFolder]` a parancsot az ARM-sablon exportáláshoz egy adott mappa erőforrásainak használatával. Ez a parancs egy érvényesítési ellenőrzést is futtat az ARM-sablon létrehozása előtt. Bemutatunk egy példát:

```
npm run start export C:\DataFactories\DevDataFactory /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/testResourceGroup/providers/Microsoft.DataFactory/factories/DevDataFactory ArmTemplateOutput
```

- `RootFolder` A egy kötelező mező, amely azt jelöli, Data Factory erőforrások hol találhatók.
- `FactoryId` A egy kötelező mező, amely a Data Factory erőforrás-azonosítóját jelöli a következő formátumban: `/subscriptions/<subId>/resourceGroups/<rgName>/providers/Microsoft.DataFactory/factories/<dfName>` .
- `OutputFolder` A nem kötelező paraméter, amely megadja a létrehozott ARM-sablon mentéséhez szükséges relatív elérési utat.
 
> [!NOTE]
> A létrehozott ARM-sablon nem lesz közzétéve a gyár élő verziójában. Az üzembe helyezést CI/CD-folyamat használatával kell eltennünk.
 
### <a name="validate"></a>Érvényesítés

Futtassa `npm run start validate <rootFolder> <factoryId>` a parancsot egy adott mappa összes erőforrásának ellenőrzéshez. Bemutatunk egy példát:

```
npm run start validate C:\DataFactories\DevDataFactory /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/testResourceGroup/providers/Microsoft.DataFactory/factories/DevDataFactory
```

- `RootFolder` A egy kötelező mező, amely azt jelöli, Data Factory erőforrások hol találhatók.
- `FactoryId` A egy kötelező mező, amely a Data Factory erőforrás-azonosítóját jelöli a következő formátumban: `/subscriptions/<subId>/resourceGroups/<rgName>/providers/Microsoft.DataFactory/factories/<dfName>` .

## <a name="create-an-azure-pipeline"></a>Azure-folyamat létrehozása

Bár az npm-csomagok többféleképpen is felhasználhatjak, az egyik elsődleges előnyt az [Azure Pipeline használatával lehet igénybeni.](https://nam06.safelinks.protection.outlook.com/?url=https:%2F%2Fdocs.microsoft.com%2F%2Fazure%2Fdevops%2Fpipelines%2Fget-started%2Fwhat-is-azure-pipelines%3Fview%3Dazure-devops%23:~:text%3DAzure%2520Pipelines%2520is%2520a%2520cloud%2Cit%2520available%2520to%2520other%2520users.%26text%3DAzure%2520Pipelines%2520combines%2520continuous%2520integration%2Cship%2520it%2520to%2520any%2520target.&data=04%7C01%7Cabnarain%40microsoft.com%7C5f064c3d5b7049db540708d89564b0bc%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C1%7C637423607000268277%7CUnknown%7CTWFpbGZsb3d8eyJWIjoiMC4wLjAwMDAiLCJQIjoiV2luMzIiLCJBTiI6Ik1haWwiLCJXVCI6Mn0%3D%7C1000&sdata=jo%2BkIvSBiz6f%2B7kmgqDN27TUWc6YoDanOxL9oraAbmA%3D&reserved=0) Az együttműködési ággal való egyes egyesítéskor aktiválható egy folyamat, amely először érvényesíti [](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Fdocs.microsoft.com%2F%2Fazure%2Fdevops%2Fpipelines%2Fartifacts%2Fbuild-artifacts%3Fview%3Dazure-devops%26tabs%3Dyaml%23how-do-i-consume-artifacts&data=04%7C01%7Cabnarain%40microsoft.com%7C5f064c3d5b7049db540708d89564b0bc%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C1%7C637423607000278113%7CUnknown%7CTWFpbGZsb3d8eyJWIjoiMC4wLjAwMDAiLCJQIjoiV2luMzIiLCJBTiI6Ik1haWwiLCJXVCI6Mn0%3D%7C1000&sdata=dN3t%2BF%2Fzbec4F28hJqigGANvvedQoQ6npzegTAwTp1A%3D&reserved=0) az összes kódot, majd exportálja az ARM-sablont egy kiadási folyamat által használható build-összetevőbe. Miben különbözik az aktuális CI/CD-folyamattól, hogy a kiadási folyamatot erre az összetevőre fogja használni a *meglévő `adf_publish` ág helyett.*

A kezdéshez kövesse az alábbi lépéseket:

1.  Nyisson meg egy Azure DevOps-projektet, és nyissa meg **a Pipelines (Folyamatok) gombra.** Válassza a **New pipeline** (Új folyamat) lehetőséget.

    ![Az Új folyamat gombot bemutató képernyőkép.](media/continuous-integration-deployment-improvements/new-pipeline.png)
    
1.  Válassza ki azt az adattárat, ahová a folyamat YAML-szkriptet menteni szeretné. Javasoljuk, hogy mentse egy buildmappába a saját erőforrásának ugyanabban a Data Factory adattárában. Győződjön meg arról, *package.js* fájl tartalmaz egy fájlban, amely tartalmazza a csomag nevét, az alábbi példában látható módon:

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
    
1.  Válassza a **Kezdő folyamat lehetőséget.** Ha feltöltötte vagy egyesítette a YAML-fájlt az alábbi példában látható módon, közvetlenül erre is mutathat, és szerkesztheti azt.

    ![Képernyőkép a Starter folyamatról.](media/continuous-integration-deployment-improvements/starter-pipeline.png)

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
        workingDir: '$(Build.Repository.LocalPath)/<folder-of-the-package.json-file>' #replace with the package.json folder
        verbose: true
      displayName: 'Install npm package'
    
    # Validates all of the Data Factory resources in the repository. You'll get the same validation errors as when "Validate All" is selected.
    # Enter the appropriate subscription and name for the source factory.
    
    - task: Npm@1
      inputs:
        command: 'custom'
        workingDir: '$(Build.Repository.LocalPath)/<folder-of-the-package.json-file>' #replace with the package.json folder
        customCommand: 'run build validate $(Build.Repository.LocalPath) /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/testResourceGroup/providers/Microsoft.DataFactory/factories/yourFactoryName'
      displayName: 'Validate'
    
    # Validate and then generate the ARM template into the destination folder, which is the same as selecting "Publish" from the UX.
    # The ARM template generated isn't published to the live version of the factory. Deployment should be done by using a CI/CD pipeline. 
    
    - task: Npm@1
      inputs:
        command: 'custom'
        workingDir: '$(Build.Repository.LocalPath)/<folder-of-the-package.json-file>' #replace with the package.json folder
        customCommand: 'run build export $(Build.Repository.LocalPath) /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/testResourceGroup/providers/Microsoft.DataFactory/factories/yourFactoryName "ArmTemplate"'
      displayName: 'Validate and Generate ARM template'
    
    # Publish the artifact to be used as a source for a release pipeline.
    
    - task: PublishPipelineArtifact@1
      inputs:
        targetPath: '$(Build.Repository.LocalPath)/<folder-of-the-package.json-file>/ArmTemplate' #replace with the package.json folder
        artifact: 'ArmTemplates'
        publishLocation: 'pipeline'
    ```

1.  Adja meg a YAML-kódot. Javasoljuk, hogy kiindulási pontként használja a YAML-fájlt.
1.  Mentse és futtassa a gombra. Ha a YAML-t használta, az a főág minden egyes frissítésekor aktiválódik.

## <a name="next-steps"></a>Következő lépések

További információ a folyamatos integrációról és teljesítésről a Data Factory:

- [Folyamatos integráció és teljesítés a Azure Data Factory.](continuous-integration-deployment.md)
