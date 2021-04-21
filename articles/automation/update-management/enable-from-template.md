---
title: Az Update Management engedélyezése Azure Resource Manager-sablonnal
description: Ez a cikk azt mutatja be, hogyan használható Azure Resource Manager sablon a Update Management.
services: automation
ms.subservice: update-management
ms.topic: conceptual
ms.date: 09/18/2020
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 687c3d49f98fe6832d23dc1529a9761d862e0666
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2021
ms.locfileid: "107830879"
---
# <a name="enable-update-management-using-azure-resource-manager-template"></a>Az Update Management engedélyezése Azure Resource Manager-sablonnal

A [szolgáltatássablonok Azure Resource Manager az](../../azure-resource-manager/templates/template-syntax.md) erőforráscsoportban Azure Automation Update Management funkció engedélyezéséhez. Ez a cikk egy mintasablont tartalmaz, amely a következőket automatizálja:

* Automatizálja egy Azure Monitor Log Analytics-munkaterületen.
* Automatizálja a Azure Automation létrehozását.
* Az Automation-fiókot a Log Analytics-munkaterülethez kapcsolódik.
* Automation-minta runbookokat ad hozzá a fiókhoz.
* Engedélyezi a Update Management funkciót.

A sablon nem automatizálja a Update Management azure-beli vagy nem Azure-beli virtuális gépeken való engedélyezését.

Ha már rendelkezik telepített Log Analytics-munkaterülettel és Automation-fiókkal az előfizetés egy támogatott régiójában, a rendszer nem csatolja őket. A sablon használata sikeresen létrehozza a hivatkozást, és üzembe Update Management.

>[!NOTE]
>ARM-sablon használata esetén az Automation-run As-fiók létrehozása nem támogatott. Ha manuálisan, a portálról vagy a PowerShell-héjból is létre kell hoznia egy futó fiókot, tekintse meg a következőt: Create Run As account (Futtatás [fiók létrehozása).](../create-run-as-account.md)

A lépések befejezése után konfigurálnia kell az Automation-fiók diagnosztikai beállításait, hogy runbook-feladat állapotát és feladatstreameit a csatolt Log Analytics-munkaterületre küldjék. [](../automation-manage-send-joblogs-log-analytics.md)

## <a name="api-versions"></a>API-verziók

Az alábbi táblázat a példában használt erőforrások API-verzióját sorolja fel.

| Erőforrás | Erőforrás típusa | API-verzió |
|:---|:---|:---|
| [Munkaterület](/azure/templates/microsoft.operationalinsights/workspaces) | munkaterületek | 2020. 03. 01. előzetes verzió |
| [Automation-fiók](/azure/templates/microsoft.automation/automationaccounts) | automatizálás | 2020. 01. 13. előzetes verzió |
| [Munkaterülethez kapcsolódó szolgáltatások](/azure/templates/microsoft.operationalinsights/workspaces/linkedservices) | munkaterületek | 2020. 03. 01. előzetes verzió |
| [Megoldások](/azure/templates/microsoft.operationsmanagement/solutions) | megoldások | 2015. 11. 01. előzetes verzió |

## <a name="before-using-the-template"></a>A sablon használata előtt

A JSON-sablon úgy van konfigurálva, hogy a következő kéréseket kéri:

* A munkaterület neve.
* A régió, ahol a munkaterületet létre kell hozni.
* Az Automation-fiók neve.
* Az a régió, ahol létre kell hoznia az Automation-fiókot.

A sablonban a következő paraméterek vannak beállítva a Log Analytics-munkaterület alapértelmezett értékével:

* *A termékváltozat* alapértelmezés szerint a 2018. áprilisi díjszabási modellben kiadott, GB-bankénti tarifacsomagot használja.
* *dataRetention (adatretention)* – alapértelmezés szerint 30 nap.

>[!WARNING]
>Ha olyan előfizetésben szeretne Log Analytics-munkaterületet létrehozni vagy konfigurálni, amely a 2018. áprilisi díjszabási modellt választotta, az egyetlen érvényes Log Analytics-tarifacsomag a *PerGB2018.*
>

A JSON-sablon megad egy alapértelmezett értéket a többi paraméterhez, amelyek valószínűleg szabványos konfigurációként lesznek használva a környezetben. A sablont egy Azure-tárfiókban tárolhatja a szervezeten belül megosztott hozzáféréshez. A sablonok használatával kapcsolatos további információkért lásd: Erőforrások üzembe helyezése [ARM-sablonokkal és az Azure CLI használatával.](../../azure-resource-manager/templates/deploy-cli.md)

Ha még csak most Azure Automation és Azure Monitor, fontos, hogy tisztában van az alábbi konfigurációs részletekkel. Segíthetnek elkerülni a hibákat, amikor az új Automation-fiókhoz kapcsolt Log Analytics-munkaterületet próbál létrehozni, konfigurálni és használni.

* További [részletek áttekintésével](../../azure-monitor/logs/resource-manager-workspace.md#create-a-log-analytics-workspace) teljes mértékben megértheti a munkaterület konfigurációs beállításait, például a hozzáférés-vezérlési módot, a tarifacsomagot, a megőrzést és a kapacitás foglalási szintjét.

* Tekintse [át a munkaterület-leképezéseket](../how-to/region-mappings.md) a támogatott régiók beágyazott vagy paraméterfájlban való megadásához. Log Analytics-munkaterület és Automation-fiók előfizetésben való összekapcsolása csak bizonyos régiók esetében támogatott.

* Ha még nem Azure Monitor, és még nem helyezett üzembe munkaterületet, tekintse át a munkaterület tervezési [útmutatóját.](../../azure-monitor/logs/design-logs-deployment.md) Segítségével megismerheti a hozzáférés-vezérlést, és megismerheti a szervezet számára ajánlott tervezési megvalósítási stratégiákat.

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

2. Szerkessze a sablont az igényeinek megfelelően. Fontolja meg egy [Resource Manager paraméterfájl létrehozását](../../azure-resource-manager/templates/parameter-files.md) ahelyett, hogy beágyazott értékként ad át paramétereket.

3. Mentse ezt a fájlt egy helyi mappába adeployUMSolutiontemplate.js **fájlként.**

4. Készen áll a sablon üzembe helyezésére. Használhatja a PowerShellt vagy az Azure CLI-t. Amikor a rendszer kéri a munkaterület és az Automation-fiók nevét, adjon meg egy globálisan egyedi nevet az összes Azure-előfizetésben.

    **PowerShell**

    ```powershell
    New-AzResourceGroupDeployment -Name <deployment-name> -ResourceGroupName <resource-group-name> -TemplateFile deployUMSolutiontemplate.json
    ```

    **Azure CLI**

    ```azurecli
    az deployment group create --resource-group <my-resource-group> --name <my-deployment-name> --template-file deployUMSolutiontemplate.json
    ```

    Az üzembe helyezés eltarthat néhány percig. Amikor befejeződik, az alábbihoz hasonló üzenet jelenik meg, amely tartalmazza az eredményt:

    ![Példa az üzembe helyezés befejezését eredményező eredményre](media/enable-from-template/template-output.png)

## <a name="review-deployed-resources"></a>Az üzembe helyezett erőforrások áttekintése

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

2. A Azure Portal nyissa meg a létrehozott Automation-fiókot.

3. A bal oldali panelen válassza a **Runbookok lehetőséget.** A **Runbookok lapon három,** az Automation-fiókkal létrehozott oktatóanyag-runbook látható.

    ![Automation-fiókkal létrehozott oktatóanyag-runbookok](../media/quickstart-create-automation-account-template/automation-sample-runbooks.png)

4. A bal oldali panelen válassza a Csatolt **munkaterület lehetőséget.** A Csatolt **munkaterület lapon** megjelenik a korábban megadott Log Analytics-munkaterület, amely az Automation-fiókhoz van csatolva.

    ![A Log Analytics-munkaterülethez csatolt Automation-fiók](../media/quickstart-create-automation-account-template/automation-account-linked-workspace.png)

5. A bal oldali panelen válassza a **Frissítéskezelés lehetőséget.** A **Frissítéskezelés oldalon** az értékelési oldal az éppen engedélyezett információk nélkül jelenik meg, és a gépek nincsenek konfigurálva a felügyeletre.

    ![Update Management funkcióértékelési nézet használata](./media/enable-from-template/update-management-assessment-view.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rájuk szüksége, törölje a Frissítések megoldást a Log Analytics-munkaterületről, válassza le az Automation-fiókot a munkaterületről, majd törölje az Automation-fiókot és -munkaterületet. 

## <a name="next-steps"></a>Következő lépések

* A virtuális Update Management frissítésekkel való kezeléséhez lásd: Virtuális gépek frissítésének [és javításának kezelése.](manage-updates-for-vm.md)

* Ha már nem szeretné használni a Update Management, és el szeretné távolítani, tekintse meg az Eltávolítás Update Management [funkció utasításait.](remove-feature.md)

* Ha törölni szeretné a virtuális gépeket a Update Management, tekintse meg a Virtuális gépek eltávolítása [a Update Management.](remove-vms.md)
