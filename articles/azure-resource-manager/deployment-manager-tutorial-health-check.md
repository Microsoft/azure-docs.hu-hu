---
title: Az Azure Deployment Manager Resource Manager-sablonokkal való használata | Microsoft Docs
description: Az Azure Deployment Managerben használhat Resource Manager-sablonokat az Azure-erőforrások üzembe helyezéséhez.
services: azure-resource-manager
documentationcenter: ''
author: mumian
ms.service: azure-resource-manager
ms.date: 05/31/2019
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 3946690b24ec4123df1bb19deb05143189fd5aa5
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/29/2019
ms.locfileid: "68596044"
---
# <a name="tutorial-use-health-check-in-azure-deployment-manager-public-preview"></a>Oktatóanyag: Állapot-ellenőrzési funkció használata az Azure telepítéskezelő (nyilvános előzetes verzió)

Ismerje meg, hogyan integrálhatja az állapot-ellenőrzését az [Azure telepítéskezelőban](./deployment-manager-overview.md). Ez az oktatóanyag az [Azure Telepítéskezelő és a Resource Manager-sablonok oktatóanyagának használatával foglalkozik](./deployment-manager-tutorial.md) . Mielőtt folytatná, ezt az oktatóanyagot kell végrehajtania.

Az [Azure Telepítéskezelő Resource Manager-sablonok használatával](./deployment-manager-tutorial.md)használt bevezetési sablonban várakozási lépést használt. Ebben az oktatóanyagban a várakozási lépést egy állapot-ellenőrzési lépéssel cseréli le.

> [!IMPORTANT]
> Ha az előfizetése Kanári-re van megjelölve az új Azure-funkciók kipróbálásához, akkor csak az Azure telepítéskezelőt használhatja a Kanári-régiókban való üzembe helyezéshez. 

Ez az oktatóanyag a következő feladatokat mutatja be:

> [!div class="checklist"]
> * Állapot-ellenőrzési szolgáltatás-szimulátor létrehozása
> * A bevezetési sablon felülvizsgálata
> * A topológia üzembe helyezése
> * A bevezetés telepítése nem kifogástalan állapottal
> * A bevezetés központi telepítésének ellenőrzése
> * A bevezetés kifogástalan állapotú üzembe helyezése
> * A bevezetés központi telepítésének ellenőrzése
> * Az erőforrások eltávolítása

További erőforrások:

- Az [Azure telepítéskezelő REST API referenciája](https://docs.microsoft.com/rest/api/deploymentmanager/).
- [Egy Azure Telepítéskezelő minta](https://github.com/Azure-Samples/adm-quickstart).

Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez az alábbiakra van szükség:

* Fejezze be [Az Azure Telepítéskezelő használatát Resource Manager](./deployment-manager-tutorial.md)-sablonokkal.
* Töltse le az oktatóanyag által használt [sablonokat és](https://armtutorials.blob.core.windows.net/admtutorial/ADMTutorial.zip) összetevőket.

## <a name="create-a-health-check-service-simulator"></a>Állapot-ellenőrzési szolgáltatás-szimulátor létrehozása

Éles környezetben általában egy vagy több figyelő szolgáltatót használ. Ahhoz, hogy a lehető legkönnyebben elérhető legyen az állapot-integráció, a Microsoft a legfelső szintű Service Health monitoring vállalatokkal együttműködve egyszerű másolási/beillesztési megoldást biztosít az állapot-ellenőrzéseknek az üzembe helyezésekkel való integrálásához. A vállalatok listáját itt tekintheti meg: [állapotfigyelő szolgáltatók](./deployment-manager-health-check.md#health-monitoring-providers). Ebben az oktatóanyagban egy [Azure](/azure/azure-functions/) -függvényt hoz létre az állapotfigyelő szolgáltatás szimulálása céljából. Ez a függvény egy állapotkódot használ, és ugyanazt a kódot adja vissza. Az Azure telepítéskezelő-sablon az állapotkód használatával határozza meg, hogyan folytathatja a telepítést.

Az Azure-függvény telepítéséhez a következő két fájl használható. Ezeket a fájlokat nem kell letöltenie, hogy átugorjon az oktatóanyagon.

* A következő helyen található Resource Manager [https://armtutorials.blob.core.windows.net/admtutorial/deploy_hc_azure_function.json](https://armtutorials.blob.core.windows.net/admtutorial/deploy_hc_azure_function.json)-sablon:. Ezt a sablont üzembe helyezheti egy Azure-függvény létrehozásához.
* Az Azure Function forráskódjának zip- [https://armtutorials.blob.core.windows.net/admtutorial/ADMHCFunction0417.zip](https://armtutorials.blob.core.windows.net/admtutorial/ADMHCFunction0417.zip)fájlja. Ezt a zip nevű fájlt a Resource Manager-sablon hívja meg.

Az Azure-függvény üzembe helyezéséhez válassza a **kipróbálás** lehetőséget az Azure Cloud Shell megnyitásához, majd illessze be a következő szkriptet a rendszerhéj ablakába.  A kód beillesztéséhez kattintson a jobb gombbal a rendszerhéj-ablakra, majd válassza a **Beillesztés**lehetőséget.

> [!IMPORTANT]
> a PowerShell-parancsfájlban található **projektnév** az ebben az oktatóanyagban üzembe helyezett Azure-szolgáltatások nevének előállítására szolgálnak. A különböző Azure-szolgáltatások eltérő követelményekkel rendelkeznek a neveknél. Az üzembe helyezés sikerességének biztosításához válasszon egy 12 karakternél rövidebb nevet, amely csak kisbetűket és számokat tartalmazhat.
> Mentse a projekt neve másolatát. Ugyanazt a projektnév használja az oktatóanyagon keresztül.

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter a project name that is used to generate Azure resource names"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"
$resourceGroupName = "${projectName}rg"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri "https://armtutorials.blob.core.windows.net/admtutorial/deploy_hc_azure_function.json" -projectName $projectName
```

Az Azure-függvény ellenőrzése és tesztelése:

1. Nyissa meg az [Azure Portalt](https://portal.azure.com).
1. Nyissa meg az erőforráscsoportot.  Az alapértelmezett név a projekt neve **RG** hozzáfűzéssel.
1. Válassza ki az App Service-t az erőforráscsoporthoz.  Az App Service alapértelmezett neve a projekt neve **WebApp** hozzáfűzéssel.
1. Bontsa ki a **függvények**csomópontot, majd válassza a **HttpTrigger1**lehetőséget.

    ![Azure telepítéskezelő állapot-ellenőrzési Azure-függvény](./media/deployment-manager-tutorial-health-check/azure-deployment-manager-hc-function.png)

1. Válassza ki  **&lt;/> a függvény URL-címének**beolvasása
1. A **Másolás** gombra kattintva másolja az URL-címet a vágólapra.  Az URL-cím a következőhöz hasonló:

    ```url
    https://myhc0417webapp.azurewebsites.net/api/healthStatus/{healthStatus}?code=hc4Y1wY4AqsskAkVw6WLAN1A4E6aB0h3MbQ3YJRF3XtXgHvooaG0aw==
    ```

    Cserélje `{healthStatus}` le az URL-címet egy állapotkód értékre. Ebben az oktatóanyagban a nem megfelelő állapotot **használja a** nem Kifogástalan állapot teszteléséhez, és az egészséges forgatókönyv teszteléséhez használja az **egészséges** vagy a **figyelmeztetést** . Hozzon létre két URL-címet, egyet a sérült állapottal, a másikat pedig kifogástalan állapottal. Példák:

    ```url
    https://myhc0417webapp.azurewebsites.net/api/healthStatus/unhealthy?code=hc4Y1wY4AqsskAkVw6WLAN1A4E6aB0h3MbQ3YJRF3XtXgHvooaG0aw==
    https://myhc0417webapp.azurewebsites.net/api/healthStatus/healthy?code=hc4Y1wY4AqsskAkVw6WLAN1A4E6aB0h3MbQ3YJRF3XtXgHvooaG0aw==
    ```

    Az oktatóanyag elvégzéséhez mindkét URL-cím szükséges.

1. Az állapotfigyelő szimulátor teszteléséhez nyissa meg az utolsó lépésben létrehozott URL-címeket.  A nem Kifogástalan állapot eredményének a következőhöz hasonlónak kell lennie:

    ```
    Status: unhealthy
    ```

## <a name="revise-the-rollout-template"></a>A bevezetési sablon felülvizsgálata

Ennek a szakasznak a célja, hogy bemutassa a bevezetési sablon állapot-ellenőrzési lépéseit. Az oktatóanyag elvégzéséhez nem kell létrehoznia saját CreateADMRollout. JSON fájlt. A felülvizsgált bevezetési sablon egy olyan Storage-fiókban van megosztva, amely az ezt követő szakaszban szerepel.

1. Nyissa meg a **CreateADMRollout. JSON**fájlt. Ez a JSON-fájl a letöltés részét képezi.  Lásd: [Előfeltételek](#prerequisites).
1. Adjon hozzá két további paramétert:

    ```json
    "healthCheckUrl": {
        "type": "string",
        "metadata": {
            "description": "Specifies the health check URL."
        }
    },
    "healthCheckAuthAPIKey": {
        "type": "string",
        "metadata": {
            "description": "Specifies the health check Azure Function function authorization key."
        }
    }
    ```

1. Cserélje le a várakozási lépés erőforrás-definícióját egy állapot-ellenőrzési lépés erőforrás-definíciója szerint:

    ```json
    {
      "type": "Microsoft.DeploymentManager/steps",
      "apiVersion": "2018-09-01-preview",
      "name": "healthCheckStep",
      "location": "[parameters('azureResourceLocation')]",
      "properties": {
        "stepType": "healthCheck",
        "attributes": {
          "waitDuration": "PT0M",
          "maxElasticDuration": "PT0M",
          "healthyStateDuration": "PT1M",
          "type": "REST",
          "properties": {
            "healthChecks": [
              {
                "name": "appHealth",
                "request": {
                  "method": "GET",
                  "uri": "[parameters('healthCheckUrl')]",
                  "authentication": {
                    "type": "ApiKey",
                    "name": "code",
                    "in": "Query",
                    "value": "[parameters('healthCheckAuthAPIKey')]"
                  }
                },
                "response": {
                  "successStatusCodes": [
                    "200"
                  ],
                  "regex": {
                    "matches": [
                      "Status: healthy",
                      "Status: warning"
                    ],
                    "matchQuantifier": "Any"
                  }
                }
              }
            ]
          }
        }
      }
    },
    ```

    A definíció alapján a bevezetést akkor kell folytatni, ha az állapot állapota *kifogástalan* vagy *Figyelmeztetés*.

1. Frissítse a bevezetési definíció **dependsON** , hogy tartalmazza az újonnan definiált állapot-ellenőrzési lépést:

    ```json
    "dependsOn": [
        "[resourceId('Microsoft.DeploymentManager/artifactSources', variables('rolloutArtifactSource').name)]",
        "[resourceId('Microsoft.DeploymentManager/steps/', 'healthCheckStep')]"
    ],
    ```

1. Frissítse a **stepGroups** , hogy tartalmazza az állapot-ellenőrzési lépést. A **healthCheckStep** hívása a **stepGroup2** **postDeploymentSteps** történik. a **stepGroup3** és a **stepGroup4** csak akkor települnek, ha a kifogástalan állapot állapota *kifogástalan* vagy *Figyelmeztetés*.

    ```json
    "stepGroups": [
        {
            "name": "stepGroup1",
            "preDeploymentSteps": [],
            "deploymentTargetId": "[resourceId('Microsoft.DeploymentManager/serviceTopologies/services/serviceUnits', variables('serviceTopology').name, variables('serviceTopology').serviceWUS.name,  variables('serviceTopology').serviceWUS.serviceUnit2.name)]",
            "postDeploymentSteps": []
        },
        {
            "name": "stepGroup2",
            "dependsOnStepGroups": ["stepGroup1"],
            "preDeploymentSteps": [],
            "deploymentTargetId": "[resourceId('Microsoft.DeploymentManager/serviceTopologies/services/serviceUnits', variables('serviceTopology').name, variables('serviceTopology').serviceWUS.name,  variables('serviceTopology').serviceWUS.serviceUnit1.name)]",
            "postDeploymentSteps": [
                {
                    "stepId": "[resourceId('Microsoft.DeploymentManager/steps/', 'healthCheckStep')]"
                }
            ]
        },
        {
            "name": "stepGroup3",
            "dependsOnStepGroups": ["stepGroup2"],
            "preDeploymentSteps": [],
            "deploymentTargetId": "[resourceId('Microsoft.DeploymentManager/serviceTopologies/services/serviceUnits', variables('serviceTopology').name, variables('serviceTopology').serviceEUS.name,  variables('serviceTopology').serviceEUS.serviceUnit2.name)]",
            "postDeploymentSteps": []
        },
        {
            "name": "stepGroup4",
            "dependsOnStepGroups": ["stepGroup3"],
            "preDeploymentSteps": [],
            "deploymentTargetId": "[resourceId('Microsoft.DeploymentManager/serviceTopologies/services/serviceUnits', variables('serviceTopology').name, variables('serviceTopology').serviceEUS.name,  variables('serviceTopology').serviceEUS.serviceUnit1.name)]",
            "postDeploymentSteps": []
        }
    ]
    ```

    Ha összehasonlítja a **stepGroup3** szakaszt a módosítás előtt és után, ez a szakasz a **stepGroup2**függ.  Erre akkor van szükség, ha a **stepGroup3** és az azt követő csoportok az állapot figyelésének eredményétől függenek.

    Az alábbi képernyőfelvételen a módosított területek, valamint az állapot-ellenőrzési lépés használata látható:

    ![Azure telepítéskezelő Health-ellenőrzési sablon](./media/deployment-manager-tutorial-health-check/azure-deployment-manager-hc-rollout-template.png)

## <a name="deploy-the-topology"></a>A topológia üzembe helyezése

Az oktatóanyag leegyszerűsítése érdekében a topológia sablonja és összetevői a következő helyeken vannak megosztva, így nem kell előkészítenie a saját példányát. Ha sajátját kívánja használni, kövesse az oktatóanyagban található [utasításokat: Az Azure telepítéskezelő használata Resource Manager-](./deployment-manager-tutorial.md)sablonokkal.

* Topológiai sablon: \ https://armtutorials.blob.core.windows.net/admtutorial/ADMTemplates/CreateADMServiceTopology.json
* Összetevők tárolója: \ https://armtutorials.blob.core.windows.net/admtutorial/ArtifactStore

A topológia üzembe helyezéséhez válassza a **kipróbálás** lehetőséget a Cloud Shell megnyitásához, majd illessze be a PowerShell-szkriptet.

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter the same project name used earlier in this tutorial"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"
$resourceGroupName = "${projectName}rg"
$artifactLocation = "https://armtutorials.blob.core.windows.net/admtutorial/ArtifactStore?st=2019-05-06T03%3A57%3A31Z&se=2020-05-07T03%3A57%3A00Z&sp=rl&sv=2018-03-28&sr=c&sig=gOh%2Bkhi693rmdxiZFQ9xbKZMU1kbLJDqXw7EP4TaGlI%3D" | ConvertTo-SecureString -AsPlainText -Force

# Create the service topology
New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateUri "https://armtutorials.blob.core.windows.net/admtutorial/ADMTemplatesHC/CreateADMServiceTopology.json" `
    -namePrefix $projectName `
    -azureResourceLocation $location `
    -artifactSourceSASLocation $artifactLocation
```

Ellenőrizze, hogy a szolgáltatástopológia és az alapjául szolgáló erőforrások sikeresen létrejöttek-e az Azure Portal használatával:

![Azure Deployment Manager-oktatóanyag – üzembe helyezett szolgáltatástopológia-erőforrások](./media/deployment-manager-tutorial/azure-deployment-manager-tutorial-deployed-topology-resources.png)

Az erőforrások megjelenítéséhez be kell jelölnie a **Rejtett típusok megjelenítése** jelölőnégyzetet.

## <a name="deploy-the-rollout-with-the-unhealthy-status"></a>A bevezetést a nem megfelelő állapotba helyezheti

Az oktatóanyag egyszerűsítése érdekében a felülvizsgált bevezetési sablon a következő helyeken van megosztva, így nem kell előkészítenie saját példányát. Ha sajátját kívánja használni, kövesse az oktatóanyagban található [utasításokat: Az Azure telepítéskezelő használata Resource Manager-](./deployment-manager-tutorial.md)sablonokkal.

* Topológiai sablon: \ https://armtutorials.blob.core.windows.net/admtutorial/ADMTemplatesHC/CreateADMRollout.json
* Összetevők tárolója: \ https://armtutorials.blob.core.windows.net/admtutorial/ArtifactStore

Használja a nem megfelelő állapotú URL-címet, amelyet az [állapot-ellenőrzési szolgáltatás létrehozása szimulátorban](#create-a-health-check-service-simulator)hozott létre. A **managedIdentityID**a [felhasználó által hozzárendelt felügyelt identitás létrehozása](./deployment-manager-tutorial.md#create-the-user-assigned-managed-identity)című témakörben talál további információt.

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter the same project name used earlier in this tutorial"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"
$managedIdentityID = Read-Host -Prompt "Enter a user-assigned managed identity"
$healthCheckUrl = Read-Host -Prompt "Enter the health check Azure function URL"
$healthCheckAuthAPIKey = $healthCheckUrl.Substring($healthCheckUrl.IndexOf("?code=")+6, $healthCheckUrl.Length-$healthCheckUrl.IndexOf("?code=")-6)
$healthCheckUrl = $healthCheckUrl.Substring(0, $healthCheckUrl.IndexOf("?"))

$resourceGroupName = "${projectName}rg"
$artifactLocation = "https://armtutorials.blob.core.windows.net/admtutorial/ArtifactStore?st=2019-05-06T03%3A57%3A31Z&se=2020-05-07T03%3A57%3A00Z&sp=rl&sv=2018-03-28&sr=c&sig=gOh%2Bkhi693rmdxiZFQ9xbKZMU1kbLJDqXw7EP4TaGlI%3D" | ConvertTo-SecureString -AsPlainText -Force

# Create the rollout
New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateUri "https://armtutorials.blob.core.windows.net/admtutorial/ADMTemplatesHC/CreateADMRollout.json" `
    -namePrefix $projectName `
    -azureResourceLocation $location `
    -artifactSourceSASLocation $artifactLocation `
    -managedIdentityID $managedIdentityID `
    -healthCheckUrl $healthCheckUrl `
    -healthCheckAuthAPIKey $healthCheckAuthAPIKey
```

> [!NOTE]
> `New-AzResourceGroupDeployment`aszinkron hívás. A sikeres üzenet csak azt jelenti, hogy a központi telepítés sikeresen elindult. Az üzembe helyezés ellenőrzéséhez használja `Get-AZDeploymentManagerRollout`a következőt:.  Tekintse meg a következő eljárást.

A bevezetési folyamat ellenőrzéséhez használja a következő PowerShell-parancsfájlt:

```azurepowershell
$projectName = Read-Host -Prompt "Enter the same project name used earlier in this tutorial"
$resourceGroupName = "${projectName}rg"
$rolloutName = "${projectName}Rollout"

# Get the rollout status
Get-AzDeploymentManagerRollout `
    -ResourceGroupName $resourceGroupName `
    -Name $rolloutName `
    -Verbose
```

A következő minta kimenet azt mutatja, hogy az üzemelő példány nem Kifogástalan állapot miatt sikertelen:

```output
Service: myhc0417ServiceWUSrg
    TargetLocation: WestUS
    TargetSubscriptionId: <Subscription ID>

    ServiceUnit: myhc0417ServiceWUSWeb
        TargetResourceGroup: myhc0417ServiceWUSrg

        Step: RestHealthCheck/healthCheckStep.PostDeploy
            Status: Failed
            StepGroup: stepGroup2
            Operation Info:
                Start Time: 05/06/2019 17:58:31
                End Time: 05/06/2019 17:58:32
                Total Duration: 00:00:01
                Error:
                    Code: ResourceReportedUnhealthy
                    Message: Health checks failed as the following resources were unhealthy: '05/06/2019 17:58:32 UTC: Health check 'appHealth' failed with the following errors: Response from endpoint 'https://myhc0417webapp.azurewebsites.net/api/healthStatus/unhealthy' does not match the regex pattern(s): 'Status: healthy, Status: warning.'. Response content: "Status: unhealthy"..'.
Get-AzDeploymentManagerRollout :
Service: myhc0417ServiceWUSrg
    ServiceUnit: myhc0417ServiceWUSWeb
        Step: RestHealthCheck/healthCheckStep.PostDeploy
            Status: Failed
            StepGroup: stepGroup2
            Operation Info:
                Start Time: 05/06/2019 17:58:31
                End Time: 05/06/2019 17:58:32
                Total Duration: 00:00:01
                Error:
                    Code: ResourceReportedUnhealthy
                    Message: Health checks failed as the following resources were unhealthy: '05/06/2019 17:58:32 UTC: Health check 'appHealth' failed with the following errors: Response from endpoint 'https://myhc0417webapp.azurewebsites.net/api/healthStatus/unhealthy' does not match the regex pattern(s): 'Status: healthy, Status: warning.'. Response content: "Status: unhealthy"..'.
At line:1 char:1
+ Get-AzDeploymentManagerRollout `
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
+ CategoryInfo          : NotSpecified: (:) [Get-AzDeploymentManagerRollout], Exception
+ FullyQualifiedErrorId : RolloutFailed,Microsoft.Azure.Commands.DeploymentManager.Commands.GetRollout


ResourceGroupName       : myhc0417rg
BuildVersion            : 1.0.0.0
ArtifactSourceId        : /subscriptions/<Subscription ID>/resourceGroups/myhc0417rg/providers/Mi
                          crosoft.DeploymentManager/artifactSources/myhc0417ArtifactSourceRollout
TargetServiceTopologyId : /subscriptions/<Subscription ID>/resourceGroups/myhc0417rg/providers/Mi
                          crosoft.DeploymentManager/serviceTopologies/myhc0417ServiceTopology
Status                  : Failed
TotalRetryAttempts      : 0
Identity                : Microsoft.Azure.Commands.DeploymentManager.Models.PSIdentity
OperationInfo           : Microsoft.Azure.Commands.DeploymentManager.Models.PSRolloutOperationInfo
Services                : {myhc0417ServiceWUS, myhc0417ServiceWUSrg}
Name                    : myhc0417Rollout
Type                    : Microsoft.DeploymentManager/rollouts
Location                : centralus
Id                      : /subscriptions/<Subscription ID>/resourcegroups/myhc0417rg/providers/Mi
                          crosoft.DeploymentManager/rollouts/myhc0417Rollout
Tags                    :
```

A bevezetés befejezése után egy további, az USA nyugati régiója számára létrehozott erőforráscsoport jelenik meg.

## <a name="deploy-the-rollout-with-the-healthy-status"></a>A bevezetést Kifogástalan állapotba helyezheti

Ennek a szakasznak a megismétlésével újra üzembe helyezheti a bevezetést a kifogástalan állapot URL-címével.  A bevezetést követően egy további, az USA keleti régiója számára létrehozott erőforráscsoport fog megjelenni.

## <a name="verify-the-deployment"></a>Az üzemelő példány ellenőrzése

1. Nyissa meg az [Azure Portalt](https://portal.azure.com).
2. Tallózással keresse meg az újonnan létrehozott webalkalmazásokat a bevezetés üzembe helyezése során létrehozott új erőforráscsoportok alatt.
3. Nyissa meg a webalkalmazást egy webböngészőben. Ellenőrizze a helyet és a verziót az index.html fájlban.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs szükség az Azure-erőforrásokra, törölje az üzembe helyezett erőforrásokat az erőforráscsoport törlésével.

1. Az Azure Portalon válassza az **Erőforráscsoport** lehetőséget a bal oldali menüben.
2. A **Szűrés név alapján** mezővel szűkítse a keresést az oktatóanyagban létrehozott erőforráscsoportokra. 3–4 erőforrásnak kell lennie:

    * **&lt;namePrefix>rg**: A Deployment Manager-erőforrásokat tartalmazza.
    * **&lt;namePrefix>ServiceWUSrg**: A ServiceWUS által definiált erőforrásokat tartalmazza.
    * **&lt;namePrefix>ServiceEUSrg**: A ServiceEUS által definiált erőforrásokat tartalmazza.
    * A felhasználó által meghatározott felügyelt identitás erőforráscsoportja.
3. Válassza ki az erőforráscsoport nevét.
4. A felső menüben válassza az **Erőforráscsoport törlése** lehetőséget.
5. Ennek a két lépésnek az ismétlésével törölje az oktatóanyagban létrehozott több erőforráscsoportot is.

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban megtanulta, hogyan használhatja az Azure telepítéskezelő állapot-ellenőrzési funkcióját. További információért tekintse meg [az Azure Resource Manager dokumentációját](/azure/azure-resource-manager/).
