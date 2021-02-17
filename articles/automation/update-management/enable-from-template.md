---
title: Az Update Management engedélyezése Azure Resource Manager-sablonnal
description: Ez a cikk azt ismerteti, hogyan használható Azure Resource Manager-sablon a Update Management engedélyezéséhez.
services: automation
ms.subservice: update-management
ms.topic: conceptual
ms.date: 09/18/2020
ms.openlocfilehash: 95ef52acedc9171ba86110a665d08ea97c59bfbb
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/17/2021
ms.locfileid: "100575827"
---
# <a name="enable-update-management-using-azure-resource-manager-template"></a>Az Update Management engedélyezése Azure Resource Manager-sablonnal

Az erőforráscsoport Azure Automation Update Management funkciójának engedélyezéséhez használhat egy [Azure Resource Manager sablont](../../azure-resource-manager/templates/template-syntax.md) . Ez a cikk egy minta sablont tartalmaz, amely automatizálja a következőket:

* Automatizálja Azure Monitor Log Analytics munkaterület létrehozását.
* Automatizálja Azure Automation fiók létrehozását.
* Az Automation-fiók csatolása a Log Analytics munkaterülethez.
* A fiókhoz hozzáadja a minta Automation-runbookok.
* Engedélyezi a Update Management funkciót.

A sablon nem automatizálja az Update Management egy vagy több Azure-beli vagy nem Azure-beli virtuális gépen.

Ha már rendelkezik egy Log Analytics munkaterülettel és Automation-fiókkal, amely az előfizetés támogatott régiójában van telepítve, akkor nincsenek összekapcsolva. A sablon használatával sikeresen létrehozta a hivatkozást, és üzembe helyezi Update Management.

>[!NOTE]
>ARM-sablon használata esetén nem támogatott az Automation futtató fiók létrehozása. Ha manuálisan szeretne létrehozni egy futtató fiókot a portálon vagy a PowerShell-lel, tekintse meg a [futtató fiók létrehozása](../create-run-as-account.md)című témakört.

A lépések elvégzése után be kell állítania az Automation-fiókhoz tartozó [diagnosztikai beállításokat](../automation-manage-send-joblogs-log-analytics.md) , hogy a runbook-feladatok állapotát és a feladatok adatfolyamait a csatolt log Analytics munkaterületre küldje.

## <a name="api-versions"></a>API-verziók

A következő táblázat felsorolja az ebben a példában használt erőforrások API-verzióját.

| Erőforrás | Erőforrás típusa | API-verzió |
|:---|:---|:---|
| [Munkaterület](/azure/templates/microsoft.operationalinsights/workspaces) | munkaterületek | 2020-03-01 – előzetes verzió |
| [Automation-fiók](/azure/templates/microsoft.automation/automationaccounts) | automatizálás | 2020-01-13 – előzetes verzió |
| [Munkaterület-társított szolgáltatások](/azure/templates/microsoft.operationalinsights/workspaces/linkedservices) | munkaterületek | 2020-03-01 – előzetes verzió |
| [Megoldások](/azure/templates/microsoft.operationsmanagement/solutions) | megoldások | 2015-11-01 – előzetes verzió |

## <a name="before-using-the-template"></a>A sablon használata előtt

A JSON-sablon úgy van konfigurálva, hogy a következőre Kérdezzen:

* A munkaterület neve.
* Az a régió, amelyben létre kívánja hozni a munkaterületet.
* Az Automation-fiók neve.
* Az a régió, amelybe az Automation-fiókot létre kell hozni.

A sablonban a következő paraméterek a Log Analytics munkaterület alapértelmezett értékével vannak beállítva:

* az *SKU* alapértelmezett értéke az április 2018 díjszabási modellben megjelent GB-os díjszabási szinten.
* a *dataRetention* alapértelmezett értéke 30 nap.

>[!WARNING]
>Ha egy Log Analytics munkaterületet szeretne létrehozni vagy konfigurálni egy olyan előfizetésben, amely az áprilisi 2018 díjszabási modellre van kiválasztva, akkor az egyetlen érvényes Log Analytics díjszabási csomag *PerGB2018*.
>

A JSON-sablon olyan alapértelmezett értéket határoz meg a többi paraméter számára, amely valószínűleg a környezetben megszokott konfigurációként lesz használva. A sablont egy Azure Storage-fiókban is tárolhatja a szervezet megosztott hozzáféréséhez. További információ a sablonok használatáról: [erőforrások üzembe helyezése ARM-sablonokkal és az Azure CLI-vel](../../azure-resource-manager/templates/deploy-cli.md).

Ha még nem Azure Automation és Azure Monitor, fontos, hogy megértse a következő konfigurációs adatokat. Az új Automation-fiókhoz kapcsolódó Log Analytics munkaterületek létrehozásakor, konfigurálásakor és használatakor elkerülheti a hibák elhárítását.

* [További részletekért](../../azure-monitor/logs/resource-manager-workspace.md#create-a-log-analytics-workspace) tekintse át a munkaterület-konfigurációs beállításokat, például a hozzáférés-vezérlési módot, a díjszabási szintet, a megőrzést és a kapacitás foglalási szintjét.

* Tekintse át a [munkaterület-leképezéseket](../how-to/region-mappings.md) a támogatott régiók beágyazott vagy egy paraméterérték megadásához. Egy Log Analytics munkaterület és egy Automation-fiók összekapcsolása csak bizonyos régiókban támogatott az előfizetésben.

* Ha még nem Azure Monitor naplókat, és már nem telepített munkaterületet, tekintse át a [munkaterület kialakítására vonatkozó útmutatást](../../azure-monitor/logs/design-logs-deployment.md). Segít megismerni a hozzáférés-vezérlést, és megismerheti a szervezete számára ajánlott kialakítási stratégiákat.

## <a name="deploy-template"></a>Sablon üzembe helyezése

1. Másolja és illessze be a következő JSON-szintaxist a létrehozott fájlba:

    ```json
    {
        "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "workspaceName": {
                "type": "string",
                "metadata": {
                    "description": "Workspace name"
                }
            },
            "sku": {
                "type": "string",
                "allowedValues": [
                    "pergb2018",
                    "Free",
                    "Standalone",
                    "PerNode",
                    "Standard",
                    "Premium"
                ],
                "defaultValue": "pergb2018",
                "metadata": {
                    "description": "Pricing tier: perGB2018 or legacy tiers (Free, Standalone, PerNode, Standard or Premium), which are not available to all customers."
                }
            },
            "dataRetention": {
                "type": "int",
                "defaultValue": 30,
                "minValue": 7,
                "maxValue": 730,
                "metadata": {
                    "description": "Number of days to retain data."
                }
            },
            "location": {
                "type": "string",
                "defaultValue": "[resourceGroup().location]",
                "metadata": {
                    "description": "Specifies the location in which to create the workspace."
                }
            },
            "automationAccountName": {
                "type": "string",
                "metadata": {
                    "description": "Automation account name"
                }
            },
            "automationAccountLocation": {
                "type": "string",
                "metadata": {
                    "description": "Specifies the location in which to create the Automation account."
                }
            },
            "sampleGraphicalRunbookName": {
                "type": "String",
                "defaultValue": "AzureAutomationTutorial"
            },
            "sampleGraphicalRunbookDescription": {
                "type": "String",
                "defaultValue": " An example runbook that gets all the Resource Manager resources by using the Run As account (service principal)."
            },
            "samplePowerShellRunbookName": {
                "type": "String",
                "defaultValue": "AzureAutomationTutorialScript"
            },
            "samplePowerShellRunbookDescription": {
                "type": "String",
                "defaultValue": " An example runbook that gets all the Resource Manager resources by using the Run As account (service principal)."
            },
            "samplePython2RunbookName": {
                "type": "String",
                "defaultValue": "AzureAutomationTutorialPython2"
            },
            "samplePython2RunbookDescription": {
                "type": "String",
                "defaultValue": " An example runbook that gets all the Resource Manager resources by using the Run As account (service principal)."
            },
            "_artifactsLocation": {
                "type": "string",
                "defaultValue": "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-automation/",
                "metadata": {
                    "description": "URI to artifacts location"
                }
            },
            "_artifactsLocationSasToken": {
                "type": "securestring",
                "defaultValue": "",
                "metadata": {
                    "description": "The sasToken required to access _artifactsLocation.  When the template is deployed using the accompanying scripts, a sasToken will be automatically generated"
                }
            }
        },
        "variables": {
        "Updates": {
            "name": "[concat('Updates', '(', parameters('workspaceName'), ')')]",
            "galleryName": "Updates"
          }
        },
        "resources": [
            {
                "type": "Microsoft.OperationalInsights/workspaces",
                "apiVersion": "2020-03-01-preview",
                "name": "[parameters('workspaceName')]",
                "location": "[parameters('location')]",
                "properties": {
                    "sku": {
                        "name": "[parameters('sku')]"
                    },
                    "retentionInDays": "[parameters('dataRetention')]",
                    "features": {
                        "searchVersion": 1,
                        "legacy": 0
                    }
                }
            },
            {
                "apiVersion": "2015-11-01-preview",
                "location": "[parameters('location')]",
                "name": "[variables('Updates').name]",
                "type": "Microsoft.OperationsManagement/solutions",
                "id": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', resourceGroup().name, '/providers/Microsoft.OperationsManagement/solutions/', variables('Updates').name)]",
                "dependsOn": [
                    "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'))]"
                ],
                "properties": {
                    "workspaceResourceId": "[resourceId('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'))]"
                },
                "plan": {
                    "name": "[variables('Updates').name]",
                    "publisher": "Microsoft",
                    "promotionCode": "",
                    "product": "[concat('OMSGallery/', variables('Updates').galleryName)]"
                }
            },
            {
                "type": "Microsoft.Automation/automationAccounts",
                "apiVersion": "2020-01-13-preview",
                "name": "[parameters('automationAccountName')]",
                "location": "[parameters('automationAccountLocation')]",
                "dependsOn": [
                    "[parameters('workspaceName')]"
                ],
                "properties": {
                    "sku": {
                        "name": "Basic"
                    }
                },
                "resources": [
                    {
                        "type": "runbooks",
                        "apiVersion": "2018-06-30",
                        "name": "[parameters('sampleGraphicalRunbookName')]",
                        "location": "[parameters('automationAccountLocation')]",
                        "dependsOn": [
                            "[parameters('automationAccountName')]"
                        ],
                        "properties": {
                            "runbookType": "GraphPowerShell",
                            "logProgress": "false",
                            "logVerbose": "false",
                            "description": "[parameters('sampleGraphicalRunbookDescription')]",
                            "publishContentLink": {
                                "uri": "[uri(parameters('_artifactsLocation'), concat('scripts/AzureAutomationTutorial.graphrunbook', parameters('_artifactsLocationSasToken')))]",
                                "version": "1.0.0.0"
                            }
                        }
                    },
                    {
                        "type": "runbooks",
                        "apiVersion": "2018-06-30",
                        "name": "[parameters('samplePowerShellRunbookName')]",
                        "location": "[parameters('automationAccountLocation')]",
                        "dependsOn": [
                            "[parameters('automationAccountName')]"
                        ],
                        "properties": {
                            "runbookType": "PowerShell",
                            "logProgress": "false",
                            "logVerbose": "false",
                            "description": "[parameters('samplePowerShellRunbookDescription')]",
                            "publishContentLink": {
                                "uri": "[uri(parameters('_artifactsLocation'), concat('scripts/AzureAutomationTutorial.ps1', parameters('_artifactsLocationSasToken')))]",
                                "version": "1.0.0.0"
                            }
                        }
                    },
                    {
                        "type": "runbooks",
                        "apiVersion": "2018-06-30",
                        "name": "[parameters('samplePython2RunbookName')]",
                        "location": "[parameters('automationAccountLocation')]",
                        "dependsOn": [
                            "[parameters('automationAccountName')]"
                        ],
                        "properties": {
                            "runbookType": "Python2",
                            "logProgress": "false",
                            "logVerbose": "false",
                            "description": "[parameters('samplePython2RunbookDescription')]",
                            "publishContentLink": {
                                "uri": "[uri(parameters('_artifactsLocation'), concat('scripts/AzureAutomationTutorialPython2.py', parameters('_artifactsLocationSasToken')))]",
                                "version": "1.0.0.0"
                            }
                        }
                    }
                ]
            },
            {
                "type": "Microsoft.OperationalInsights/workspaces/linkedServices",
                "apiVersion": "2020-03-01-preview",
                "name": "[concat(parameters('workspaceName'), '/' , 'Automation')]",
                "location": "[parameters('location')]",
                "dependsOn": [
                    "[parameters('workspaceName')]",
                    "[parameters('automationAccountName')]"
                ],
                "properties": {
                    "resourceId": "[resourceId('Microsoft.Automation/automationAccounts', parameters('automationAccountName'))]"
                }
            }
        ]
    }
    ```

2. Szerkessze a sablont, hogy megfeleljen a követelményeinek. Hozzon létre egy [Resource Manager-paramétereket tartalmazó fájlt](../../azure-resource-manager/templates/parameter-files.md) ahelyett, hogy a paramétereket beágyazott értékként adja át.

3. Mentse ezt a fájlt egy helyi mappába **deployUMSolutiontemplate.jsként**.

4. Készen áll a sablon üzembe helyezésére. Használhatja a PowerShellt vagy az Azure CLI-t is. Ha a rendszer a munkaterület és az Automation-fiók nevének megadását kéri, adjon meg egy olyan nevet, amely globálisan egyedi az összes Azure-előfizetésen belül.

    **PowerShell**

    ```powershell
    New-AzResourceGroupDeployment -Name <deployment-name> -ResourceGroupName <resource-group-name> -TemplateFile deployUMSolutiontemplate.json
    ```

    **Azure CLI**

    ```azurecli
    az deployment group create --resource-group <my-resource-group> --name <my-deployment-name> --template-file deployUMSolutiontemplate.json
    ```

    Az üzembe helyezés eltarthat néhány percig. Amikor befejeződik, a következőhöz hasonló üzenet jelenik meg, amely tartalmazza az eredményt:

    ![Példa az üzembe helyezés befejezésekor bekövetkezett eredményre](media/enable-from-template/template-output.png)

## <a name="review-deployed-resources"></a>Üzembe helyezett erőforrások áttekintése

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

2. A Azure Portal nyissa meg a létrehozott Automation-fiókot.

3. A bal oldali panelen válassza a **runbookok** lehetőséget. A **runbookok** lapon a lista három oktatóanyag-runbookok hozott létre az Automation-fiókkal.

    ![Az Automation-fiókkal létrehozott runbookok oktatóanyag](../media/quickstart-create-automation-account-template/automation-sample-runbooks.png)

4. A bal oldali panelen válassza a **csatolt munkaterület** lehetőséget. A **csatolt munkaterület** lapon megjelenik az Automation-fiókhoz korábban megadott log Analytics munkaterület.

    ![Az Log Analytics munkaterülethez csatolt Automation-fiók](../media/quickstart-create-automation-account-template/automation-account-linked-workspace.png)

5. A bal oldali panelen válassza a **frissítés kezelése** lehetőséget. Az **Update Management (frissítés felügyelete** ) lapon az értékelés oldalt az éppen engedélyezve állapotot eredményező információk nélkül jeleníti meg, és a gépek nincsenek felügyeletre konfigurálva.

    ![Update Management funkció-értékelési nézet](./media/enable-from-template/update-management-assessment-view.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs szüksége rájuk, törölje a **frissítési** megoldást a log Analytics munkaterületen, válassza le az Automation-fiókot a munkaterületről, majd törölje az Automation-fiókot és-munkaterületet.

## <a name="next-steps"></a>Következő lépések

* A Update Management virtuális gépekhez való használatához lásd: [a virtuális gépek frissítéseinek és javításának kezelése](manage-updates-for-vm.md).

* Ha már nem szeretné Update Management használni, és el szeretné távolítani, tekintse meg az [Update Management funkció eltávolítása](remove-feature.md)című témakör utasításait.

* A virtuális gépek Update Managementból való törléséről lásd: [virtuális gépek eltávolítása Update Managementról](remove-vms.md).