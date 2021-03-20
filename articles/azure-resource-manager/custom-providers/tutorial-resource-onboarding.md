---
title: Oktatóanyag – erőforrás-előkészítés
description: Az egyéni szolgáltatókon keresztüli erőforrás-előkészítés lehetővé teszi a meglévő Azure-erőforrások kezelését és kiterjesztését.
ms.topic: tutorial
ms.author: jobreen
author: jjbfour
ms.date: 09/17/2019
ms.openlocfilehash: 22d1dcd997a4ddb94aba184c5dace4c00509054d
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "75649938"
---
# <a name="tutorial-resource-onboarding-with-azure-custom-providers"></a>Oktatóanyag: erőforrás-előkészítés az Azure egyéni szolgáltatókkal

Ebben az oktatóanyagban egy egyéni erőforrás-szolgáltatót helyez üzembe az Azure-ban, amely kibővíti a Azure Resource Manager API-t a Microsoft. CustomProviders/társítások erőforrás-típussal. Az oktatóanyag bemutatja, hogyan terjeszthető ki az erőforráscsoport azon erőforrásain kívüli meglévő erőforrások, amelyeken az egyéni szolgáltatói példány található. Ebben az oktatóanyagban az egyéni erőforrás-szolgáltatót egy Azure logikai alkalmazás működteti, de bármilyen nyilvános API-végpontot használhat.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez ismernie kell a következőket:

* Az [Egyéni Azure-szolgáltatók](overview.md)képességei.
* Alapszintű információk az erőforrás-előkészítés [Egyéni szolgáltatókkal való](concepts-resource-onboarding.md)bevezetéséről.

## <a name="get-started-with-resource-onboarding"></a>Ismerkedés az erőforrás-előkészítéssel

Ebben az oktatóanyagban két darabot kell telepíteni: az egyéni szolgáltatót és a társítást. A folyamat megkönnyítése érdekében igény szerint egyetlen sablont is használhat, amely mindkettőt üzembe helyezi.

A sablon ezeket az erőforrásokat fogja használni:

* Microsoft. CustomProviders/resourceProviders
* Microsoft. Logic/munkafolyamatok
* Microsoft. CustomProviders/társítások

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "location": {
            "type": "string",
            "allowedValues": [
                "australiaeast",
                "eastus",
                "westeurope"
            ],
            "metadata": {
                "description": "Location for the resources."
            }
        },
        "logicAppName": {
            "type": "string",
            "defaultValue": "[uniqueString(resourceGroup().id)]",
            "metadata": {
                "description": "Name of the logic app to be created."
            }
        },
        "customResourceProviderName": {
            "type": "string",
            "defaultValue": "[uniqueString(resourceGroup().id)]",
            "metadata": {
                "description": "Name of the custom provider to be created."
            }
        },
        "customResourceProviderId": {
            "type": "string",
            "defaultValue": "",
            "metadata": {
                "description": "The resource ID of an existing custom provider. Provide this to skip deployment of new logic app and custom provider."
            }
        },
        "associationName": {
            "type": "string",
            "defaultValue": "myAssociationResource",
            "metadata": {
                "description": "Name of the custom resource that is being created."
            }
        }
    },
    "resources": [
        {
            "type": "Microsoft.Resources/deployments",
            "apiVersion": "2017-05-10",
            "condition": "[empty(parameters('customResourceProviderId'))]",
            "name": "customProviderInfrastructureTemplate",
            "properties": {
                "mode": "Incremental",
                "template": {
                    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
                    "contentVersion": "1.0.0.0",
                    "parameters": {
                        "logicAppName": {
                            "type": "string",
                            "defaultValue": "[parameters('logicAppName')]"
                        }
                    },
                    "resources": [
                        {
                            "type": "Microsoft.Logic/workflows",
                            "apiVersion": "2017-07-01",
                            "name": "[parameters('logicAppName')]",
                            "location": "[parameters('location')]",
                            "properties": {
                                "state": "Enabled",
                                "definition": {
                                    "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
                                    "actions": {
                                        "Switch": {
                                            "cases": {
                                                "Case": {
                                                    "actions": {
                                                        "CreateCustomResource": {
                                                            "inputs": {
                                                                "body": {
                                                                    "properties": "@addProperty(triggerBody().Body['properties'], 'myDynamicProperty', 'myDynamicValue')"
                                                                },
                                                                "statusCode": 200
                                                            },
                                                            "kind": "Http",
                                                            "type": "Response"
                                                        }
                                                    },
                                                    "case": "CREATE"
                                                }
                                            },
                                            "default": {
                                                "actions": {
                                                    "DefaultHttpResponse": {
                                                        "inputs": {
                                                            "statusCode": 200
                                                        },
                                                        "kind": "Http",
                                                        "type": "Response"
                                                    }
                                                }
                                            },
                                            "expression": "@triggerBody().operationType",
                                            "type": "Switch"
                                        }
                                    },
                                    "contentVersion": "1.0.0.0",
                                    "outputs": {},
                                    "parameters": {},
                                    "triggers": {
                                        "CustomProviderWebhook": {
                                            "inputs": {
                                                "schema": {}
                                            },
                                            "kind": "Http",
                                            "type": "Request"
                                        }
                                    }
                                }
                            }
                        },
                        {
                            "type": "Microsoft.CustomProviders/resourceProviders",
                            "apiVersion": "2018-09-01-preview",
                            "name": "[parameters('customResourceProviderName')]",
                            "location": "[parameters('location')]",
                            "properties": {
                                "resourceTypes": [
                                    {
                                        "name": "associations",
                                        "mode": "Secure",
                                        "routingType": "Webhook,Cache,Extension",
                                        "endpoint": "[[listCallbackURL(concat(resourceId('Microsoft.Logic/workflows', parameters('logicAppName')), '/triggers/CustomProviderWebhook'), '2017-07-01').value]"
                                    }
                                ]
                            }
                        }
                    ],
                    "outputs": {
                        "customProviderResourceId": {
                            "type": "string",
                            "value": "[resourceId('Microsoft.CustomProviders/resourceProviders', parameters('customResourceProviderName'))]"
                        }
                    }
                }
            }
        },
        {
            "type": "Microsoft.CustomProviders/associations",
            "apiVersion": "2018-09-01-preview",
            "name": "[parameters('associationName')]",
            "location": "global",
            "properties": {
                "targetResourceId": "[if(empty(parameters('customResourceProviderId')), reference('customProviderInfrastructureTemplate').outputs.customProviderResourceId.value, parameters('customResourceProviderId'))]",
                "myCustomInputProperty": "myCustomInputValue",
                "myCustomInputObject": {
                    "Property1": "Value1"
                }
            }
        }
    ],
    "outputs": {
        "associationResource": {
            "type": "object",
            "value": "[reference(parameters('associationName'), '2018-09-01-preview', 'Full')]"
        }
    }
}
```

### <a name="deploy-the-custom-provider-infrastructure"></a>Az egyéni szolgáltatói infrastruktúra üzembe helyezése

A sablon első része az egyéni szolgáltatói infrastruktúrát telepíti. Ez az infrastruktúra határozza meg a társítások erőforrásának hatását. Ha még nem ismeri az egyéni szolgáltatókat, tekintse meg az [egyéni szolgáltató alapjai](overview.md)című témakört.

Üzembe helyezzük az egyéni szolgáltatói infrastruktúrát. Másolja, mentse és telepítse az előző sablont, vagy kövesse az infrastruktúra következő lépéseit, és telepítse az infrastruktúrát a Azure Portal használatával.

1. Nyissa meg az [Azure Portal](https://portal.azure.com).

2. A **sablonok** keresése az **összes szolgáltatásban** vagy a fő keresőmező használatával:

   ![Sablonok keresése](media/tutorial-resource-onboarding/templates.png)

3. Válassza a **Hozzáadás** lehetőséget a **sablonok** ablaktáblán:

   ![Válassza a Hozzáadás lehetőséget](media/tutorial-resource-onboarding/templatesadd.png)

4. Az **általános** területen adja meg az új sablon **nevét** és **leírását** :

   ![Sablon neve és leírása](media/tutorial-resource-onboarding/templatesdescription.png)

5. Hozzon létre egy Resource Manager-sablont a jelen cikk "az erőforrások bevezetésének első lépései" című szakaszának JSON-sablonban történő másolásával:

   ![Resource Manager-sablon létrehozása](media/tutorial-resource-onboarding/templatesarmtemplate.png)

6. A sablon létrehozásához válassza a **Hozzáadás** lehetőséget. Ha az új sablon nem jelenik meg, válassza a **frissítés** lehetőséget.

7. Válassza ki az újonnan létrehozott sablont, majd válassza a **telepítés** lehetőséget:

   ![Válassza ki az új sablont, majd válassza a telepítés lehetőséget.](media/tutorial-resource-onboarding/templateselectspecific.png)

8. Adja meg a kötelező mezők beállításait, majd válassza ki az előfizetést és az erőforráscsoportot. Az **egyéni erőforrás-szolgáltató azonosítója** mezőt üresen hagyhatja.

   | Beállítás neve | Kötelező? | Description |
   | ------------ | -------- | ----------- |
   | Hely | Igen | A sablon erőforrásainak helye. |
   | Logikai alkalmazás neve | No | A logikai alkalmazás neve. |
   | Egyéni erőforrás-szolgáltató neve | No | Az egyéni erőforrás-szolgáltató neve. |
   | Egyéni erőforrás-szolgáltató azonosítója | No | Egy meglévő egyéni erőforrás-szolgáltató, amely támogatja a társítási erőforrást. Ha itt értéket ad meg, a rendszer kihagyja a logikai alkalmazást és az egyéni szolgáltató üzembe helyezését. |
   | Társítás neve | No | A társítási erőforrás neve. |

   Minta paramétereinek:

   ![Adja meg a sablon paramétereit](media/tutorial-resource-onboarding/templatescustomprovider.png)

9. Nyissa meg a központi telepítést, és várjon, amíg befejeződik. A következő képernyőképhez hasonlóan kell megjelennie. Az új társítási erőforrásnak kimenetként kell megjelennie:

   ![Sikeres üzembe helyezés](media/tutorial-resource-onboarding/customproviderdeployment.png)

   Itt található az erőforráscsoport, a **rejtett típusok megjelenítése** beállítással:

   ![Egyéni szolgáltató üzembe helyezése](media/tutorial-resource-onboarding/showhidden.png)

10. Fedezze fel a logikai alkalmazás **futtatási előzményei** lapot, ahol megtekintheti a társítások létrehozásához szükséges hívásokat:

    ![A Logic app-futtatások előzményei](media/tutorial-resource-onboarding/logicapprun.png)

## <a name="deploy-additional-associations"></a>További társítások telepítése

Miután beállította az egyéni szolgáltatói infrastruktúrát, egyszerűen üzembe helyezhet több társítást. A további társítások erőforráscsoporthoz nem kell megegyeznie az egyéni szolgáltatói infrastruktúrát üzembe helyező erőforráscsoporthoz. Társítás létrehozásához Microsoft. CustomProviders/resourceproviders/Write engedélyekkel kell rendelkeznie a megadott egyéni erőforrás-szolgáltatói AZONOSÍTÓhoz.

1. Nyissa meg a **Microsoft. CustomProviders/resourceProviders** erőforrást az előző üzemelő példány erőforráscsoporthoz. Be kell jelölnie a **rejtett típusok megjelenítése** jelölőnégyzetet:

   ![Ugrás az erőforráshoz](media/tutorial-resource-onboarding/showhidden.png)

2. Másolja az egyéni szolgáltató erőforrás-azonosító tulajdonságát.

3. A **sablonok** keresése az **összes szolgáltatásban** vagy a fő keresőmező használatával:

   ![Sablonok keresése](media/tutorial-resource-onboarding/templates.png)

4. Válassza ki a korábban létrehozott sablont, majd válassza a **telepítés** lehetőséget:

   ![Válassza ki a korábban létrehozott sablont, majd válassza a telepítés lehetőséget.](media/tutorial-resource-onboarding/templateselectspecific.png)

5. Adja meg a kötelező mezők beállításait, majd válassza ki az előfizetést és egy másik erőforráscsoportot. Az **egyéni erőforrás-szolgáltató azonosítója** beállításnál adja meg azt az erőforrás-azonosítót, amelyet a korábban telepített egyéni szolgáltatótól másolt.

6. Nyissa meg a központi telepítést, és várjon, amíg befejeződik. Most csak az új társítási erőforrást kell telepítenie:

   ![Új társítások erőforrás](media/tutorial-resource-onboarding/createdassociationresource.png)

Ha szeretné, térjen vissza a logikai alkalmazás **futtatási előzményeihez** , és tekintse meg, hogy a logikai alkalmazásnak van-e másik hívása. A logikai alkalmazás frissítésével további funkciókat is kibővítheti az egyes létrehozott társításokhoz.

## <a name="getting-help"></a>Segítség kérése

Ha kérdése van az egyéni Azure-szolgáltatókkal kapcsolatban, próbálja meg megkérdezni őket [stack Overflowon](https://stackoverflow.com/questions/tagged/azure-custom-providers). Egy hasonló kérdés megválaszolása már megtörtént, ezért először A feladás előtt érdemes megnéznie. A címke hozzáadásával `azure-custom-providers` gyors választ kaphat!

