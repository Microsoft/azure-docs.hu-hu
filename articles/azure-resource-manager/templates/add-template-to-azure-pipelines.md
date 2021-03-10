---
title: CI/CD Azure-folyamatokkal és-sablonokkal
description: Ismerteti, hogyan konfigurálhatja a folyamatos integrációt az Azure-folyamatokban Azure Resource Manager sablonok használatával. Bemutatja, hogyan használhat PowerShell-parancsfájlokat, illetve hogyan másolhat fájlokat egy átmeneti helyre, és onnan telepítheti azokat.
ms.topic: conceptual
ms.date: 03/09/2021
ms.openlocfilehash: 4a2f1f15de0abd802f3dce138b2cea33e52e3dfc
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/10/2021
ms.locfileid: "102561942"
---
# <a name="integrate-arm-templates-with-azure-pipelines"></a>ARM-sablonok integrálása az Azure Pipelines használatával

A folyamatos integráció és a folyamatos üzembe helyezés (CI/CD) érdekében integrálhatja Azure Resource Manager sablonokat (ARM-sablonokat) az Azure-folyamatokkal. Ebből a cikkből megtudhatja, hogyan helyezhet üzembe a sablonok Azure-folyamatokkal való üzembe helyezésének két fejlettebb módját.

## <a name="select-your-option"></a>Válassza ki a lehetőséget

A cikk folytatása előtt tekintsük át az ARM-sablonok folyamatokból való üzembe helyezésének különböző lehetőségeit.

* **Az ARM-sablon üzembe helyezési feladata**. Ez a legegyszerűbb lehetőség. Ez a módszer akkor működik, ha közvetlenül egy adattárból kíván üzembe helyezni egy sablont. Ez a beállítás nem szerepel ebben a cikkben, hanem az [ARM-sablonok Azure-folyamatokkal való folyamatos integrációjának](deployment-tutorial-pipeline.md)oktatóanyaga tárgyalja. Bemutatja, hogyan helyezhet üzembe egy sablont a GitHub-tárházból a [ARM-sablon üzembe helyezési feladatának](https://github.com/microsoft/azure-pipelines-tasks/blob/master/Tasks/AzureResourceManagerTemplateDeploymentV3/README.md) használatával.

* **Azure PowerShell parancsfájlt futtató feladat hozzáadása**. Ennek a beállításnak az az előnye, hogy a fejlesztési életciklus teljes életciklusa alatt biztosítja a konzisztenciát, mivel a helyi tesztek futtatásakor használt parancsfájlt használhatja. A szkript telepíti a sablont, de más műveleteket is végrehajthat, például paramétereket lehet használni. Ez a beállítás ebben a cikkben látható. Lásd: [Azure PowerShell feladat](#azure-powershell-task).

   A Visual Studio egy PowerShell-parancsfájlt tartalmazó [Azure erőforráscsoport-projektet](create-visual-studio-deployment-project.md) biztosít. A parancsfájl a projektből egy olyan Storage-fiókra mutat, amelyet a Resource Manager elérhet. Az összetevők olyan elemek a projektben, mint például a csatolt sablonok, a parancsfájlok és az alkalmazás bináris fájljai. Ha továbbra is szeretné használni a szkriptet a projektből, használja a cikkben látható PowerShell-parancsfájlt.

* Feladatok **hozzáadása a feladatok másolásához és üzembe helyezéséhez**. Ez a beállítás kényelmes alternatívát kínál a projekt parancsfájlhoz. A folyamat két feladatot konfigurál. Egy feladattal az összetevők egy elérhető helyre kerülnek. A másik feladat az adott helyről telepíti a sablont. Ez a beállítás ebben a cikkben látható. Lásd: [feladatok másolása és telepítése](#copy-and-deploy-tasks).

## <a name="prepare-your-project"></a>A projekt előkészítése

Ez a cikk feltételezi, hogy az ARM-sablon és az Azure DevOps-szervezet készen áll a folyamat létrehozására. A következő lépések bemutatják, hogyan ellenőrizheti, hogy készen áll-e:

* Rendelkezik egy Azure DevOps-szervezettel. Ha még nem rendelkezik ilyennel, [hozzon létre egyet ingyen](/azure/devops/pipelines/get-started/pipelines-sign-up). Ha a csapata már rendelkezik Azure DevOps-szervezettel, ellenőrizze, hogy a használni kívánt Azure DevOps-projekt rendszergazdája-e.

* Konfigurált egy [szolgáltatási kapcsolódást](/azure/devops/pipelines/library/connect-to-azure) az Azure-előfizetéséhez. A folyamat feladatai az egyszerű szolgáltatás identitása alatt futnak. A kapcsolatok létrehozásának lépéseiért tekintse meg a [DevOps-projekt létrehozása](deployment-tutorial-pipeline.md#create-a-devops-project)című témakört.

* Rendelkezik egy [ARM-sablonnal](quickstart-create-templates-use-visual-studio-code.md) , amely meghatározza a projekt infrastruktúráját.

## <a name="create-pipeline"></a>Folyamat létrehozása

1. Ha korábban még nem adott hozzá egy folyamatot, létre kell hoznia egy új folyamatot. Az Azure DevOps-szervezetből válassza a **folyamatok** és az **új folyamat** elemet.

   ![Új folyamat hozzáadása](./media/add-template-to-azure-pipelines/new-pipeline.png)

1. Adja meg a kód tárolási helyét. Az alábbi képen az **Azure Repos git** kiválasztása látható.

   ![Kód forrásának kiválasztása](./media/add-template-to-azure-pipelines/select-source.png)

1. Ebből a forrásból válassza ki azt a tárházat, amely rendelkezik a projekt kódjával.

   ![Adattár kiválasztása](./media/add-template-to-azure-pipelines/select-repo.png)

1. Válassza ki a létrehozandó folyamat típusát. Kiválaszthatja az **induló folyamat** elemet.

   ![Folyamat kiválasztása](./media/add-template-to-azure-pipelines/select-pipeline.png)

Készen áll Azure PowerShell feladat hozzáadására vagy a fájl másolására és a feladatok üzembe helyezésére.

## <a name="azure-powershell-task"></a>Azure PowerShell-feladat

Ez a szakasz bemutatja, hogyan konfigurálhatja a folyamatos üzembe helyezést egyetlen olyan feladattal, amely futtatja a PowerShell-parancsfájlt a projektben. Ha egy sablont telepítő PowerShell-parancsfájlra van szüksége, tekintse meg [Deploy-AzTemplate.ps1](https://github.com/Azure/azure-quickstart-templates/blob/master/Deploy-AzTemplate.ps1) vagy [Deploy-AzureResourceGroup.ps1](https://github.com/Azure/azure-quickstart-templates/blob/master/Deploy-AzureResourceGroup.ps1).

A következő YAML-fájl egy [Azure PowerShell feladatot](/azure/devops/pipelines/tasks/deploy/azure-powershell)hoz létre:

```yml
trigger:
- master

pool:
  vmImage: 'ubuntu-latest'

steps:
- task: AzurePowerShell@5
  inputs:
    azureSubscription: 'script-connection'
    ScriptType: 'FilePath'
    ScriptPath: './Deploy-AzTemplate.ps1'
    ScriptArguments: -Location 'centralus' -ResourceGroupName 'demogroup' -TemplateFile templates\mainTemplate.json
    azurePowerShellVersion: 'LatestVersion'
```

A feladat beállításakor `AzurePowerShell@5` a folyamat az az [modult](/powershell/azure/new-azureps-module-az)használja. Ha a AzureRM modult használja a parancsfájlban, állítsa a feladatot a következőre: `AzurePowerShell@3` .

```yaml
steps:
- task: AzurePowerShell@3
```

A (z) esetében `azureSubscription` adja meg a létrehozott szolgáltatási kapcsolatok nevét.

```yaml
inputs:
    azureSubscription: '<your-connection-name>'
```

A esetében `scriptPath` adja meg a folyamat fájljának relatív elérési útját a parancsfájlnak. A tárházban megtekintheti az elérési utat.

```yaml
ScriptPath: '<your-relative-path>/<script-file-name>.ps1'
```

A-ben `ScriptArguments` adja meg a parancsfájlhoz szükséges paramétereket. Az alábbi példa egy parancsfájlhoz tartozó paramétereket mutat be, de testre kell szabnia a parancsfájl paramétereit.

```yaml
ScriptArguments: -Location 'centralus' -ResourceGroupName 'demogroup' -TemplateFile templates\mainTemplate.json
```

Ha a **Mentés** lehetőséget választja, a folyamat automatikusan elindul. Térjen vissza a build-folyamat összegzéséhez, és tekintse meg az állapotot.

![Eredmények megtekintése](./media/add-template-to-azure-pipelines/view-results.png)

Az aktuálisan futó folyamat kiválasztásával megtekintheti a feladatok részleteit. Amikor befejeződik, megjelenik az egyes lépések eredményei.

## <a name="copy-and-deploy-tasks"></a>Feladatok másolása és üzembe helyezése

Ez a szakasz bemutatja, hogyan konfigurálhatja a folyamatos üzembe helyezést két feladat használatával. Az első feladat az összetevőket egy Storage-fiókba, a második pedig a sablont telepíti.

A fájlok Storage-fiókba való másolásához a szolgáltatás-kapcsolódáshoz tartozó szolgáltatásnevet hozzá kell rendelni a Storage blob-adatközreműködőhöz vagy a Storage blob-adat tulajdonosi szerepköréhez. További információ: Ismerkedés [a AzCopy](../../storage/common/storage-use-azcopy-v10.md)szolgáltatással.

Az alábbi YAML az [Azure file Copy feladatot](/azure/devops/pipelines/tasks/deploy/azure-file-copy)jeleníti meg.

```yml
trigger:
- master

pool:
  vmImage: 'windows-latest'

steps:
- task: AzureFileCopy@4
  inputs:
    SourcePath: 'templates'
    azureSubscription: 'copy-connection'
    Destination: 'AzureBlob'
    storage: 'demostorage'
    ContainerName: 'projecttemplates'
  name: AzureFileCopy
```

A feladat több részből áll, hogy átvizsgálja a környezetét. A az összetevők `SourcePath` helyét jelzi a folyamat fájljához viszonyítva.

```yaml
SourcePath: '<path-to-artifacts>'
```

A (z) esetében `azureSubscription` adja meg a létrehozott szolgáltatási kapcsolatok nevét.

```yaml
azureSubscription: '<your-connection-name>'
```

A tárolási és a tároló neve mezőben adja meg az összetevők tárolásához használni kívánt Storage-fiók és-tároló nevét. A Storage-fióknak léteznie kell.

```yaml
storage: '<your-storage-account-name>'
ContainerName: '<container-name>'
```

A fájl másolása feladat létrehozása után készen áll arra, hogy hozzáadja az előkészített sablon üzembe helyezéséhez szükséges feladatot.

A következő YAML a [Azure Resource Manager sablon telepítési feladatát](https://github.com/microsoft/azure-pipelines-tasks/blob/master/Tasks/AzureResourceManagerTemplateDeploymentV3/README.md)mutatja:

```yaml
- task: AzureResourceManagerTemplateDeployment@3
  inputs:
    deploymentScope: 'Resource Group'
    azureResourceManagerConnection: 'copy-connection'
    subscriptionId: '00000000-0000-0000-0000-000000000000'
    action: 'Create Or Update Resource Group'
    resourceGroupName: 'demogroup'
    location: 'West US'
    templateLocation: 'URL of the file'
    csmFileLink: '$(AzureFileCopy.StorageContainerUri)templates/mainTemplate.json$(AzureFileCopy.StorageContainerSasToken)'
    csmParametersFileLink: '$(AzureFileCopy.StorageContainerUri)templates/mainTemplate.parameters.json$(AzureFileCopy.StorageContainerSasToken)'
    deploymentMode: 'Incremental'
    deploymentName: 'deploy1'
```

Ennek a feladatnak több része is részletesebben áttekinthető.

- `deploymentScope`: Válassza ki az üzembe helyezés hatókörét a következő lehetőségek közül: `Management Group` , `Subscription` és `Resource Group` . További információ a hatókörökről: [telepítési hatókörök](deploy-rest.md#deployment-scope).

- `azureResourceManagerConnection`: Adja meg a létrehozott szolgáltatási kapcsolatok nevét.

- `subscriptionId`: Adja meg a cél előfizetés-AZONOSÍTÓját. Ez a tulajdonság csak az erőforráscsoport központi telepítési hatókörére és az előfizetés központi telepítési hatókörére vonatkozik.

- `resourceGroupName` és `location` : adja meg a telepíteni kívánt erőforráscsoport nevét és helyét. A feladat akkor hozza létre az erőforráscsoportot, ha az nem létezik.

   ```yml
   resourceGroupName: '<resource-group-name>'
   location: '<location>'
   ```

- `csmFileLink`: Adja meg az előkészített sablon hivatkozását. Az érték beállításakor használja a fájlmásolási feladat által visszaadott változókat. Az alábbi példa egy mainTemplate.jsnevű sablonra hivatkozik. A **sablonok** nevű mappát a rendszer belefoglalja, mert a fájlmásolási feladat a fájlra másolta a fájlt. A folyamatában adja meg a sablon elérési útját és a sablon nevét.

   ```yml
   csmFileLink: '$(AzureFileCopy.StorageContainerUri)templates/mainTemplate.json$(AzureFileCopy.StorageContainerSasToken)'
   ```

A folyamat így néz ki:

```yml
trigger:
- master

pool:
  vmImage: 'windows-latest'

steps:
- task: AzureFileCopy@4
  inputs:
    SourcePath: 'templates'
    azureSubscription: 'copy-connection'
    Destination: 'AzureBlob'
    storage: 'demostorage'
    ContainerName: 'projecttemplates'
  name: AzureFileCopy
- task: AzureResourceManagerTemplateDeployment@3
  inputs:
    deploymentScope: 'Resource Group'
    azureResourceManagerConnection: 'copy-connection'
    subscriptionId: '00000000-0000-0000-0000-000000000000'
    action: 'Create Or Update Resource Group'
    resourceGroupName: 'demogroup'
    location: 'West US'
    templateLocation: 'URL of the file'
    csmFileLink: '$(AzureFileCopy.StorageContainerUri)templates/mainTemplate.json$(AzureFileCopy.StorageContainerSasToken)'
    csmParametersFileLink: '$(AzureFileCopy.StorageContainerUri)templates/mainTemplate.parameters.json$(AzureFileCopy.StorageContainerSasToken)'
    deploymentMode: 'Incremental'
    deploymentName: 'deploy1'
```

Ha a **Mentés** lehetőséget választja, a folyamat automatikusan elindul. Térjen vissza a build-folyamat összegzéséhez, és tekintse meg az állapotot.

## <a name="next-steps"></a>Következő lépések

* Ha a mi-if műveletet egy folyamaton szeretné használni, olvassa el a következő témakört: [ARM-sablonok tesztelése What-If egy folyamaton belül](https://4bes.nl/2021/03/06/test-arm-templates-with-what-if/).
* További információ az ARM-sablonok GitHub-műveletekkel való használatáról: [Azure Resource Manager sablonok üzembe helyezése a GitHub-műveletek használatával](deploy-github-actions.md).
