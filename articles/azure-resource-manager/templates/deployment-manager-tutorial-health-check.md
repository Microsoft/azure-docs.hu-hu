---
title: Az Azure Deployment Manager állapot-ellenőrzési szolgáltatása
description: Az állapot-ellenőrzési lehetőséggel biztonságosan telepíthet Azure-erőforrásokat az Azure Deployment Manager használatával.
author: mumian
ms.date: 10/09/2019
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 12d246a493ff9ee9e20868da32d633d51939e66c
ms.sourcegitcommit: 59cfed657839f41c36ccdf7dc2bee4535c920dd4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/06/2021
ms.locfileid: "99626627"
---
# <a name="tutorial-use-health-check-in-azure-deployment-manager-public-preview"></a>Oktatóanyag: állapot-ellenőrzési funkció használata az Azure Deployment Manager (nyilvános előzetes verzió)

Ismerje meg, hogyan integrálhatja az állapot-ellenőrzését az [Azure Deployment Managerban](./deployment-manager-overview.md). Ez az oktatóanyag az [Azure Deployment Manager és a Resource Manager-sablonok oktatóanyagának használatával foglalkozik](./deployment-manager-tutorial.md) . Mielőtt folytatná, ezt az oktatóanyagot kell végrehajtania.

Az [Azure Deployment Manager Resource Manager-sablonok használatával](./deployment-manager-tutorial.md)használt bevezetési sablonban várakozási lépést használt. Ebben az oktatóanyagban a várakozási lépést egy állapot-ellenőrzési lépéssel cseréli le.

> [!IMPORTANT]
> Ha az előfizetése Kanári-re van megjelölve az új Azure-funkciók kipróbálásához, akkor csak az Azure Deployment Managert használhatja a Kanári-régiókban való üzembe helyezéshez.

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

További források:

* Az [Azure Deployment Manager REST API referenciája](/rest/api/deploymentmanager/).
* [Egy Azure Deployment Manager minta](https://github.com/Azure-Samples/adm-quickstart).

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez a következőkre lesz szüksége:

* Egy Azure-előfizetés. Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.
* Fejezze be [Az Azure Deployment Manager használatát Resource Manager-sablonokkal](./deployment-manager-tutorial.md).

## <a name="install-the-artifacts"></a>Az összetevők telepítése

Ha még nem töltötte le az előfeltételként szolgáló oktatóanyagban használt mintákat, letöltheti [a sablonokat és az](https://github.com/Azure/azure-docs-json-samples/raw/master/tutorial-adm/ADMTutorial.zip) összetevőket, és kicsomagolhatja helyileg. Ezután futtassa a PowerShell-szkriptet az előfeltételek oktatóanyag szakaszának [előkészítés](./deployment-manager-tutorial.md#prepare-the-artifacts)szakaszában. A parancsfájl létrehoz egy erőforráscsoportot, létrehoz egy tárolót, létrehoz egy BLOB-tárolót, feltölti a letöltött fájlokat, majd létrehoz egy SAS-jogkivonatot.

* Készítsen másolatot az URL-címről SAS-jogkivonattal. Ez az URL-cím szükséges ahhoz, hogy feltöltse a mezőket a két paraméter fájljában: topológiai paraméterek fájl és bevezetési paraméterek fájl.
* Nyissa meg _aCreateADMServiceTopology.Parameters.jst_ , és frissítse a és a értékét `projectName` `artifactSourceSASLocation` .
* Nyissa meg _aCreateADMRollout.Parameters.jst_ , és frissítse a és a értékét `projectName` `artifactSourceSASLocation` .

## <a name="create-a-health-check-service-simulator"></a>Állapot-ellenőrzési szolgáltatás-szimulátor létrehozása

Éles környezetben általában egy vagy több figyelő szolgáltatót használ. Ahhoz, hogy a lehető legkönnyebben elérhető legyen az állapot-integráció, a Microsoft a legfelső szintű Service Health monitoring vállalatokkal együttműködve egyszerű másolási/beillesztési megoldást biztosít az állapot-ellenőrzéseknek az üzembe helyezésekkel való integrálásához. A vállalatok listáját itt tekintheti meg: [állapotfigyelő szolgáltatók](./deployment-manager-health-check.md#health-monitoring-providers). Ebben az oktatóanyagban egy [Azure-függvényt](../../azure-functions/index.yml) hoz létre az állapotfigyelő szolgáltatás szimulálása céljából. Ez a függvény egy állapotkódot használ, és ugyanazt a kódot adja vissza. Az Azure Deployment Manager-sablon az állapotkód használatával határozza meg, hogyan folytathatja a telepítést.

Az Azure-függvény telepítéséhez a következő két fájl használható. Ezeket a fájlokat nem kell letöltenie, hogy átugorjon az oktatóanyagon.

* A következő helyen található Resource Manager-sablon: [https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/tutorial-adm/deploy_hc_azure_function.json](https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/tutorial-adm/deploy_hc_azure_function.json) . Ezt a sablont üzembe helyezheti egy Azure-függvény létrehozásához.
* Az Azure Function forráskódjának zip-fájlja [https://github.com/Azure/azure-docs-json-samples/raw/master/tutorial-adm/ADMHCFunction0417.zip](https://github.com/Azure/azure-docs-json-samples/raw/master/tutorial-adm/ADMHCFunction0417.zip) . Ezt a zip nevű fájlt a Resource Manager-sablon hívja meg.

Az Azure-függvény üzembe helyezéséhez válassza a **kipróbálás** lehetőséget a Azure Cloud Shell megnyitásához, majd illessze be a következő szkriptet a rendszerhéj ablakába. A kód beillesztéséhez kattintson a jobb gombbal a rendszerhéj-ablakra, majd válassza a **Beillesztés** lehetőséget.

```azurepowershell-interactive
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/tutorial-adm/deploy_hc_azure_function.json" -projectName $projectName
```

Az Azure-függvény ellenőrzése és tesztelése:

1. Nyissa meg az [Azure Portalt](https://portal.azure.com).
1. Nyissa meg az erőforráscsoportot. Az alapértelmezett név a projekt neve **RG** hozzáfűzéssel.
1. Válassza ki az App Service-t az erőforráscsoporthoz. Az App Service alapértelmezett neve a projekt neve **WebApp** hozzáfűzéssel.
1. Bontsa ki a **függvények** csomópontot, majd válassza a **HttpTrigger1** lehetőséget.

    ![Azure Deployment Manager állapot-ellenőrzési Azure-függvény](./media/deployment-manager-tutorial-health-check/azure-deployment-manager-hc-function.png)

1. Válassza ki **&lt; /> a függvény URL-címének beolvasása**
1. A **Másolás** gombra kattintva másolja az URL-címet a vágólapra. Az URL-cím a következőhöz hasonló:

    ```url
    https://myhc0417webapp.azurewebsites.net/api/healthStatus/{healthStatus}?code=hc4Y1wY4AqsskAkVw6WLAN1A4E6aB0h3MbQ3YJRF3XtXgHvooaG0aw==
    ```

    Cserélje le az `{healthStatus}` URL-címet egy állapotkód értékre. Ebben az oktatóanyagban a nem *megfelelő állapotot használja a* nem Kifogástalan állapot teszteléséhez, és az egészséges forgatókönyv teszteléséhez használja az *egészséges* vagy a *figyelmeztetést* . Hozzon létre két URL-címet, egyet a *sérült* állapottal, a másikat pedig *kifogástalan* állapottal. Például:

    ```url
    https://myhc0417webapp.azurewebsites.net/api/healthStatus/unhealthy?code=hc4Y1wY4AqsskAkVw6WLAN1A4E6aB0h3MbQ3YJRF3XtXgHvooaG0aw==
    https://myhc0417webapp.azurewebsites.net/api/healthStatus/healthy?code=hc4Y1wY4AqsskAkVw6WLAN1A4E6aB0h3MbQ3YJRF3XtXgHvooaG0aw==
    ```

    Az oktatóanyag elvégzéséhez mindkét URL-cím szükséges.

1. Az állapotfigyelő szimulátor teszteléséhez nyissa meg az előző lépésben létrehozott URL-címeket. A sérült állapothoz tartozó eredmények a következőhöz hasonlóak lesznek:

    ```Output
    Status: unhealthy
    ```

## <a name="revise-the-rollout-template"></a>A bevezetési sablon felülvizsgálata

Ennek a szakasznak a célja, hogy bemutassa a bevezetési sablon állapot-ellenőrzési lépéseit.

1. Nyissa meg aCreateADMRollout.js, amelyet az [Azure Deployment Manager használata Resource Manager-sablonokkal](./deployment-manager-tutorial.md)című _oldalon_ hozott létre. Ez a JSON-fájl a letöltés részét képezi.  Lásd: [Előfeltételek](#prerequisites).
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

1. Frissítse a `dependsOn` bevezetési definíciót, hogy tartalmazza az újonnan definiált állapot-ellenőrzési lépést:

    ```json
    "dependsOn": [
      "[resourceId('Microsoft.DeploymentManager/artifactSources', variables('rolloutArtifactSource').name)]",
      "[resourceId('Microsoft.DeploymentManager/steps/', 'healthCheckStep')]"
    ],
    ```

1. A frissítéssel `stepGroups` adja meg az állapot-ellenőrzési lépést. A `healthCheckStep` neve: `postDeploymentSteps` `stepGroup2` . `stepGroup3`és `stepGroup4` csak akkor települnek, ha az állapota Kifogástalan vagy  *Figyelmeztetés*.

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

    Ha összehasonlítja a `stepGroup3` szakaszt a felülvizsgálat előtt és után, ez a szakasz a következőtől függ: `stepGroup2` . Erre akkor van szükség, ha `stepGroup3` a következő lépésekre vonatkozó csoportok az állapot figyelésének eredményétől függenek.

    Az alábbi képernyőfelvételen a módosított területek láthatók, valamint az állapot-ellenőrzési lépés használata:

    ![Azure Deployment Manager Health-ellenőrzési sablon](./media/deployment-manager-tutorial-health-check/azure-deployment-manager-hc-rollout-template.png)

## <a name="deploy-the-topology"></a>A topológia üzembe helyezése

Futtassa a következő PowerShell-szkriptet a topológia telepítéséhez. Az [Azure Deployment Manager Resource Manager-sablonokkal való használatához](./deployment-manager-tutorial.md)használt _CreateADMServiceTopology.js_ és a _CreateADMServiceTopology.Parameters.js_ .

```azurepowershell
# Create the service topology
New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateFile "$filePath\ADMTemplates\CreateADMServiceTopology.json" `
    -TemplateParameterFile "$filePath\ADMTemplates\CreateADMServiceTopology.Parameters.json"
```

Ellenőrizze, hogy a szolgáltatástopológia és az alapjául szolgáló erőforrások sikeresen létrejöttek-e az Azure Portal használatával:

![Azure Deployment Manager-oktatóanyag – üzembe helyezett szolgáltatástopológia-erőforrások](./media/deployment-manager-tutorial/azure-deployment-manager-tutorial-deployed-topology-resources.png)

Az erőforrások megjelenítéséhez be kell jelölnie a **Rejtett típusok megjelenítése** jelölőnégyzetet.

## <a name="deploy-the-rollout-with-the-unhealthy-status"></a>A bevezetést a nem megfelelő állapotba helyezheti

Használja a nem megfelelő állapotú URL-címet, amelyet az [állapot-ellenőrzési szolgáltatás létrehozása szimulátorban](#create-a-health-check-service-simulator)hozott létre. Az [Azure Deployment Manager Resource Manager-sablonokkal való használatához](./deployment-manager-tutorial.md)használt _CreateADMServiceTopology.js_ és ugyanezen a _CreateADMServiceTopology.Parameters.js_ kell megadnia.

```azurepowershell-interactive
$healthCheckUrl = Read-Host -Prompt "Enter the health check Azure function URL"
$healthCheckAuthAPIKey = $healthCheckUrl.Substring($healthCheckUrl.IndexOf("?code=")+6, $healthCheckUrl.Length-$healthCheckUrl.IndexOf("?code=")-6)
$healthCheckUrl = $healthCheckUrl.Substring(0, $healthCheckUrl.IndexOf("?"))

# Create the rollout
New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateFile "$filePath\ADMTemplates\CreateADMRollout.json" `
    -TemplateParameterFile "$filePath\ADMTemplates\CreateADMRollout.Parameters.json" `
    -healthCheckUrl $healthCheckUrl `
    -healthCheckAuthAPIKey $healthCheckAuthAPIKey
```

> [!NOTE]
> `New-AzResourceGroupDeployment` aszinkron hívás. A sikeres üzenet csak azt jelenti, hogy a központi telepítés sikeresen elindult. Az üzembe helyezés ellenőrzéséhez használja a következőt: `Get-AZDeploymentManagerRollout` .  Tekintse meg a következő eljárást.

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

```Output
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

Ennek a szakasznak a megismétlésével újra üzembe helyezheti a bevezetést a kifogástalan állapot URL-címével. A bevezetés befejezése után még egy erőforráscsoport jön létre az USA keleti régiójában.

## <a name="verify-the-deployment"></a>Az üzemelő példány ellenőrzése

1. Nyissa meg az [Azure Portalt](https://portal.azure.com).
1. Tallózással keresse meg az új webes alkalmazásokat a bevezetési telepítés által létrehozott új erőforráscsoportok területen.
1. Nyissa meg a webalkalmazást egy webböngészőben. Ellenőrizze a helyet és a verziót a _index.html_ fájlban.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs szükség az Azure-erőforrásokra, törölje az üzembe helyezett erőforrásokat az erőforráscsoport törlésével.

1. A Azure Portal válassza ki a bal oldali menüből az **erőforráscsoportot** .
1. A **Szűrés név alapján** mezővel szűkítse a keresést az oktatóanyagban létrehozott erőforráscsoportokra.

    * **&lt; projektnév>RG**: a Deployment Manager erőforrásait tartalmazza.
    * **&lt; projektnév>ServiceWUSrg**: a ServiceWUS által definiált erőforrásokat tartalmazza.
    * **&lt; projektnév>ServiceEUSrg**: a ServiceEUS által definiált erőforrásokat tartalmazza.
    * A felhasználó által meghatározott felügyelt identitás erőforráscsoportja.
1. Válassza ki az erőforráscsoport nevét.
1. Válassza az **erőforráscsoport törlése** lehetőséget a felső menüben.
1. Ennek a két lépésnek az ismétlésével törölje az oktatóanyagban létrehozott több erőforráscsoportot is.

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban megtanulta, hogyan használhatja az Azure Deployment Manager állapot-ellenőrzési funkcióját. További információért tekintse meg [az Azure Resource Manager dokumentációját](../index.yml).
