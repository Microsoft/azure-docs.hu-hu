---
title: Azure-irányítópultok programozott létrehozása
description: Az irányítópultot sablonként használhatja a Azure Portal Azure-irányítópultok programozott módon történő létrehozásához. Tartalmazza a JSON-referenciát.
ms.topic: how-to
ms.date: 12/4/2020
ms.openlocfilehash: 92848ac238ff11a90afc82713639b8abebf076ec
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2021
ms.locfileid: "107878793"
---
# <a name="programmatically-create-azure-dashboards"></a>Azure-irányítópultok programozott létrehozása

Ez a cikk végigvezeti az Azure-irányítópultok programozott módon történő létrehozásának és közzétételének folyamatán. Az alább látható irányítópultra a teljes dokumentum hivatkozik.

![minta-irányítópult](./media/azure-portal-dashboards-create-programmatically/sample-dashboard.png)

## <a name="overview"></a>Áttekintés

A megosztott irányítópultok a [Azure Portal](https://portal.azure.com) [a virtuális gépekhez](../azure-resource-manager/management/overview.md) és a tárfiókokhoz hasonló erőforrások. Az erőforrásokat programozott módon kezelheti a Azure Resource Manager [REST API-k,](/rest/api/)az [Azure CLI](/cli/azure)és a [Azure PowerShell parancsokkal.](/powershell/azure/get-started-azureps)

Számos szolgáltatás épül ezekre az API-kra az erőforrás-kezelés egyszerűbbé váltása érdekében. Ezen API-k és eszközök mind lehetőséget kínálnak az erőforrások létrehozására, listál való létrehozására, lekérésre, módosítására és törlésére. Mivel az irányítópultok erőforrások, választhatja ki kedvenc API-ját vagy eszközét.

Bármelyik eszközt is használja az irányítópult programozott módon történő létrehozásához, az irányítópult-objektum JSON-ábrázolása hozható létre. Ez az objektum információkat tartalmaz az irányítópult csempéiről. Ez magában foglalja a méreteket, a pozíciókat, a hozzá kötött erőforrásokat és a felhasználói testreszabásokat.

A JSON-dokumentum felépítésének legpraktikusabb módja a Azure Portal. Csempéket interaktív módon adhat hozzá és helyezzen el. Ezután exportálja a JSON-t, és hozzon létre egy sablont az eredményből a szkriptek, programok és telepítési eszközök későbbi használatra.

## <a name="create-a-dashboard"></a>Irányítópult létrehozása

Irányítópult létrehozásához válassza az Irányítópult **lehetőséget** a Azure Portal [menüben,](https://portal.azure.com) majd válassza az Új irányítópult **lehetőséget.**

![új irányítópult parancs](./media/azure-portal-dashboards-create-programmatically/new-dashboard-command.png)

Csempék megkeresése és hozzáadása a csempetár használatával. A csempéket húzással lehet hozzáadni. Egyes csempék húzással támogatják az átméretezést.

![a fogópont húzása a méret módosításakor](./media/azure-portal-dashboards-create-programmatically/drag-handle.png)

Mások rögzített méretűek, amelyek közül választhatnak a helyi menüben.

![sizes (méretek) helyi menü a méret módosításakor](./media/azure-portal-dashboards-create-programmatically/sizes-context-menu.png)

## <a name="share-the-dashboard"></a>Az irányítópult megosztása

Az irányítópult konfigurálása után a következő lépés az irányítópult közzététele a **Megosztás paranccsal.**

![irányítópult megosztása](./media/azure-portal-dashboards-create-programmatically/share-command.png)

A **Megosztás lehetőség** kiválasztásával kiválaszthatja, hogy melyik előfizetésben és erőforráscsoportban tegye közzé a közzétételt. Írási hozzáféréssel kell lennie a választott előfizetéshez és erőforráscsoporthoz. További információ: [Azure-szerepkörök hozzárendelése a Azure Portal.](../role-based-access-control/role-assignments-portal.md)

![megosztás és hozzáférés módosításai](./media/azure-portal-dashboards-create-programmatically/sharing-and-access.png)

## <a name="fetch-the-json-representation-of-the-dashboard"></a>Az irányítópult JSON-reprezentációjának beolvasása

A közzététel csak néhány másodpercet vesz igénybe. Ha ezzel végzett, a következő lépés a JSON beolvasása a **Letöltés paranccsal.**

![JSON-reprezentáció letöltése](./media/azure-portal-dashboards-create-programmatically/download-command.png)

## <a name="create-a-template-from-the-json"></a>Sablon létrehozása a JSON-ból

A következő lépés egy sablon létrehozása ebből a JSON-ból. Ezt a sablont programozott módon használhatja a megfelelő erőforrás-kezelési API-okkal, parancssori eszközökkel vagy a portálon belül.

Sablon létrehozásához nem kell teljesen megértenie az irányítópult JSON-struktúráját. A legtöbb esetben meg szeretné őrizni az egyes csempék szerkezetét és konfigurációját. Ezután paraméterezi az Azure-erőforrások készletét, amelyekre a csempék mutatnak. Nézze meg az exportált JSON-irányítópultot, és keresse meg az Azure-erőforrás-idok összes előfordulását. A példa irányítópult több csempével rendelkezik, amelyek egyetlen Azure-beli virtuális gépre mutatnak. Ennek az az oka, hogy az irányítópult csak ezt az erőforrást nézi. Ha a dokumentum végén található JSON-mintában az "/subscriptions" kifejezésre keres rá, ennek az azonosítónak több előfordulását is megtalálja.

`/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1`

Ha ezt az irányítópultot a jövőben bármely virtuális géphez közzéteheti, paraméterezheti a sztring minden előfordulását a JSON-ban.

Az Azure-ban erőforrásokat létrehozására használt API-knak két megközelítése van:

* Az imperatív API-k egyszerre egy erőforrást hoznak létre. További információ: [Erőforrások.](/rest/api/resources/resources)
* Egy sablonalapú üzembe helyezési rendszer, amely több függő erőforrást hoz létre egyetlen API-hívással. További információ: Erőforrások üzembe helyezése Resource Manager [sablonokkal és Azure PowerShell.](../azure-resource-manager/templates/deploy-powershell.md)

A sablonalapú üzembe helyezés támogatja a paraméterezést és a sablonozást. Ebben a cikkben ezt a megközelítést használjuk.

## <a name="programmatically-create-a-dashboard-from-your-template-using-a-template-deployment"></a>Irányítópult létrehozása sablonból programozott módon sablontelepítés használatával

Az Azure lehetővé teszi több erőforrás üzembe helyezésének vezénylét. Létrehoz egy üzembe helyezési sablont, amely az üzembe helyezni kívánt erőforrások készletét és a közöttük álló kapcsolatokat fejezi ki.  Az egyes erőforrások JSON-formátuma megegyezik azzal, mintha egyesenként hozta volna létre őket. A különbség az, hogy a sablonnyelv további fogalmakat is tartalmaz, például változókat, paramétereket, alapszintű függvényeket stb. Ez a kiterjesztett szintaxis csak sablontelepítés esetén támogatott. Nem működik, ha a korábban tárgyalt imperatív API-okkal használják. További információ: A sablonok szerkezetének [és szintaxisának Azure Resource Manager.](../azure-resource-manager/templates/template-syntax.md)

A paraméterezést a sablon paraméterszintaxisával kell tenni.  Lecseréli a korábban megtalált erőforrás-azonosító összes példányát az itt látható módon.

Példa JSON-tulajdonságra nem kódolt erőforrás-azonosítóval:

```json
id: "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1"
```

Példa JSON-tulajdonság paraméteres verzióra sablonparaméterek alapján

```json
id: "[resourceId(parameters('virtualMachineResourceGroup'), 'Microsoft.Compute/virtualMachines', parameters('virtualMachineName'))]"
```

Deklarálja a szükséges sablonmetaadatokat és paramétereket a JSON-sablon tetején az így:

```json

{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "virtualMachineName": {
            "type": "string"
        },
        "virtualMachineResourceGroup": {
            "type": "string"
        },
        "dashboardName": {
            "type": "string"
        }
    },
    "variables": {},

    ... rest of template omitted ...
```
Miután konfigurálta a sablont, az alábbi módszerek bármelyikével üzembe helyezheti:

* [REST API-k](/rest/api/resources/deployments)
* [PowerShell](../azure-resource-manager/templates/deploy-powershell.md)
* [Azure CLI](/cli/azure/group/deployment#az_group_deployment_create)
* [A Azure Portal sablon üzembe helyezési oldala](https://portal.azure.com/#create/Microsoft.Template)

Ezután a példa irányítópult JSON-verziójának két verzióját fogja látni. Az első az a verzió, amit a portálról exportáltunk, és amely már hozzá volt kötve egy erőforráshoz. A második a sablon verziója, amely programozott módon kötődik bármely virtuális géphez, és üzembe helyezhető a Azure Resource Manager.

### <a name="json-representation-of-our-example-dashboard-before-templating"></a>A példa irányítópult JSON-ábrázolása a sablon használata előtt

Ez a példa bemutatja, mire számíthat, ha követte a cikkel együtt. Az utasítások egy már üzembe helyezett irányítópult JSON-ábrázolását exportálta. A nem rögzített erőforrás-azonosítók azt mutatják, hogy ez az irányítópult egy adott Azure-beli virtuális gépre mutat.

```json

{
    "properties": {
        "lenses": {
            "0": {
                "order": 0,
                "parts": {
                    "0": {
                        "position": {
                            "x": 0,
                            "y": 0,
                            "rowSpan": 2,
                            "colSpan": 3
                        },
                        "metadata": {
                            "inputs": [],
                            "type": "Extension[azure]/HubsExtension/PartType/MarkdownPart",
                            "settings": {
                                "content": {
                                    "settings": {
                                        "content": "## Azure Virtual Machines Overview\r\nNew team members should watch this video to get familiar with Azure Virtual Machines.",
                                        "title": "",
                                        "subtitle": ""
                                    }
                                }
                            }
                        }
                    },
                    "1": {
                        "position": {
                            "x": 3,
                            "y": 0,
                            "rowSpan": 4,
                            "colSpan": 8
                        },
                        "metadata": {
                            "inputs": [],
                            "type": "Extension[azure]/HubsExtension/PartType/MarkdownPart",
                            "settings": {
                                "content": {
                                    "settings": {
                                        "content": "This is the team dashboard for the test VM we use on our team. Here are some useful links:\r\n\r\n1. [Getting started](https://www.contoso.com/tsgs)\r\n1. [Troubleshooting guide](https://www.contoso.com/tsgs)\r\n1. [Architecture docs](https://www.contoso.com/tsgs)",
                                        "title": "Test VM Dashboard",
                                        "subtitle": "Contoso"
                                    }
                                }
                            }
                        }
                    },
                    "2": {
                        "position": {
                            "x": 0,
                            "y": 2,
                            "rowSpan": 2,
                            "colSpan": 3
                        },
                        "metadata": {
                            "inputs": [],
                            "type": "Extension[azure]/HubsExtension/PartType/VideoPart",
                            "settings": {
                                "content": {
                                    "settings": {
                                        "title": "",
                                        "subtitle": "",
                                        "src": "https://www.youtube.com/watch?v=YcylDIiKaSU&list=PLLasX02E8BPCsnETz0XAMfpLR1LIBqpgs&index=4",
                                        "autoplay": false
                                    }
                                }
                            }
                        }
                    },
                    "3": {
                        "position": {
                            "x": 0,
                            "y": 4,
                            "rowSpan": 3,
                            "colSpan": 11
                        },
                        "metadata": {
                            "inputs": [
                                {
                                    "name": "queryInputs",
                                    "value": {
                                        "timespan": {
                                            "duration": "PT1H",
                                            "start": null,
                                            "end": null
                                        },
                                        "id": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1",
                                        "chartType": 0,
                                        "metrics": [
                                            {
                                                "name": "Percentage CPU",
                                                "resourceId": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1"
                                            }
                                        ]
                                    }
                                }
                            ],
                            "type": "Extension/Microsoft_Azure_Monitoring/PartType/MetricsChartPart",
                            "settings": {}
                        }
                    },
                    "4": {
                        "position": {
                            "x": 0,
                            "y": 7,
                            "rowSpan": 2,
                            "colSpan": 3
                        },
                        "metadata": {
                            "inputs": [
                                {
                                    "name": "queryInputs",
                                    "value": {
                                        "timespan": {
                                            "duration": "PT1H",
                                            "start": null,
                                            "end": null
                                        },
                                        "id": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1",
                                        "chartType": 0,
                                        "metrics": [
                                            {
                                                "name": "Disk Read Operations/Sec",
                                                "resourceId": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1"
                                            },
                                            {
                                                "name": "Disk Write Operations/Sec",
                                                "resourceId": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1"
                                            }
                                        ]
                                    }
                                }
                            ],
                            "type": "Extension/Microsoft_Azure_Monitoring/PartType/MetricsChartPart",
                            "settings": {}
                        }
                    },
                    "5": {
                        "position": {
                            "x": 3,
                            "y": 7,
                            "rowSpan": 2,
                            "colSpan": 3
                        },
                        "metadata": {
                            "inputs": [
                                {
                                    "name": "queryInputs",
                                    "value": {
                                        "timespan": {
                                            "duration": "PT1H",
                                            "start": null,
                                            "end": null
                                        },
                                        "id": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1",
                                        "chartType": 0,
                                        "metrics": [
                                            {
                                                "name": "Disk Read Bytes",
                                                "resourceId": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1"
                                            },
                                            {
                                                "name": "Disk Write Bytes",
                                                "resourceId": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1"
                                            }
                                        ]
                                    }
                                }
                            ],
                            "type": "Extension/Microsoft_Azure_Monitoring/PartType/MetricsChartPart",
                            "settings": {}
                        }
                    },
                    "6": {
                        "position": {
                            "x": 6,
                            "y": 7,
                            "rowSpan": 2,
                            "colSpan": 3
                        },
                        "metadata": {
                            "inputs": [
                                {
                                    "name": "queryInputs",
                                    "value": {
                                        "timespan": {
                                            "duration": "PT1H",
                                            "start": null,
                                            "end": null
                                        },
                                        "id": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1",
                                        "chartType": 0,
                                        "metrics": [
                                            {
                                                "name": "Network In",
                                                "resourceId": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1"
                                            },
                                            {
                                                "name": "Network Out",
                                                "resourceId": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1"
                                            }
                                        ]
                                    }
                                }
                            ],
                            "type": "Extension/Microsoft_Azure_Monitoring/PartType/MetricsChartPart",
                            "settings": {}
                        }
                    },
                    "7": {
                        "position": {
                            "x": 9,
                            "y": 7,
                            "rowSpan": 2,
                            "colSpan": 2
                        },
                        "metadata": {
                            "inputs": [
                                {
                                    "name": "id",
                                    "value": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1"
                                }
                            ],
                            "type": "Extension/Microsoft_Azure_Compute/PartType/VirtualMachinePart",
                            "asset": {
                                "idInputName": "id",
                                "type": "VirtualMachine"
                            },
                            "defaultMenuItemId": "overview"
                        }
                    }
                }
            }
        },
        "metadata": { }
    },
    "id": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/dashboards/providers/Microsoft.Portal/dashboards/aa9786ae-e159-483f-b05f-1f7f767741a9",
    "name": "aa9786ae-e159-483f-b05f-1f7f767741a9",
    "type": "Microsoft.Portal/dashboards",
    "location": "eastasia",
    "tags": {
        "hidden-title": "Created via API"
    }
}

```

### <a name="template-representation-of-our-example-dashboard"></a>A példa-irányítópult sablonrekrezentációja

Az irányítópult sablonverziója három paramétert `virtualMachineName` definiált: , `virtualMachineResourceGroup` és `dashboardName` .  A paraméterekkel ezt az irányítópultot minden üzembe helyezéskor egy másik Azure-beli virtuális gépre mutat. Ez az irányítópult programozott módon konfigurálható és üzembe helyezhető úgy, hogy bármely Azure-beli virtuális gépre mutasson. A szolgáltatás teszteléséhez másolja ki a következő sablont, és illessze be a Azure Portal [üzembe helyezési oldalára.](https://portal.azure.com/#create/Microsoft.Template)

Ez a példa önmagában helyez üzembe egy irányítópultot, de a sablonnyelv lehetővé teszi több erőforrás üzembe helyezését, és egy vagy több irányítópult kötegbe helyezését.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "virtualMachineName": {
            "type": "string"
        },
        "virtualMachineResourceGroup": {
            "type": "string"
        },
        "dashboardName": {
            "type": "string"
        }
    },
    "variables": {},
    "resources": [
        {
            "properties": {
                "lenses": {
                    "0": {
                        "order": 0,
                        "parts": {
                            "0": {
                                "position": {
                                    "x": 0,
                                    "y": 0,
                                    "rowSpan": 2,
                                    "colSpan": 3
                                },
                                "metadata": {
                                    "inputs": [],
                                    "type": "Extension[azure]/HubsExtension/PartType/MarkdownPart",
                                    "settings": {
                                        "content": {
                                            "settings": {
                                                "content": "## Azure Virtual Machines Overview\r\nNew team members should watch this video to get familiar with Azure Virtual Machines.",
                                                "title": "",
                                                "subtitle": ""
                                            }
                                        }
                                    }
                                }
                            },
                            "1": {
                                "position": {
                                    "x": 3,
                                    "y": 0,
                                    "rowSpan": 4,
                                    "colSpan": 8
                                },
                                "metadata": {
                                    "inputs": [],
                                    "type": "Extension[azure]/HubsExtension/PartType/MarkdownPart",
                                    "settings": {
                                        "content": {
                                            "settings": {
                                                "content": "This is the team dashboard for the test VM we use on our team. Here are some useful links:\r\n\r\n1. [Getting started](https://www.contoso.com/tsgs)\r\n1. [Troubleshooting guide](https://www.contoso.com/tsgs)\r\n1. [Architecture docs](https://www.contoso.com/tsgs)",
                                                "title": "Test VM Dashboard",
                                                "subtitle": "Contoso"
                                            }
                                        }
                                    }
                                }
                            },
                            "2": {
                                "position": {
                                    "x": 0,
                                    "y": 2,
                                    "rowSpan": 2,
                                    "colSpan": 3
                                },
                                "metadata": {
                                    "inputs": [],
                                    "type": "Extension[azure]/HubsExtension/PartType/VideoPart",
                                    "settings": {
                                        "content": {
                                            "settings": {
                                                "title": "",
                                                "subtitle": "",
                                                "src": "https://www.youtube.com/watch?v=YcylDIiKaSU&list=PLLasX02E8BPCsnETz0XAMfpLR1LIBqpgs&index=4",
                                                "autoplay": false
                                            }
                                        }
                                    }
                                }
                            },
                            "3": {
                                "position": {
                                    "x": 0,
                                    "y": 4,
                                    "rowSpan": 3,
                                    "colSpan": 11
                                },
                                "metadata": {
                                    "inputs": [
                                        {
                                            "name": "queryInputs",
                                            "value": {
                                                "timespan": {
                                                    "duration": "PT1H",
                                                    "start": null,
                                                    "end": null
                                                },
                                                "id": "[resourceId(parameters('virtualMachineResourceGroup'), 'Microsoft.Compute/virtualMachines', parameters('virtualMachineName'))]",
                                                "chartType": 0,
                                                "metrics": [
                                                    {
                                                        "name": "Percentage CPU",
                                                        "resourceId": "[resourceId(parameters('virtualMachineResourceGroup'), 'Microsoft.Compute/virtualMachines', parameters('virtualMachineName'))]"
                                                    }
                                                ]
                                            }
                                        }
                                    ],
                                    "type": "Extension/Microsoft_Azure_Monitoring/PartType/MetricsChartPart",
                                    "settings": {}
                                }
                            },
                            "4": {
                                "position": {
                                    "x": 0,
                                    "y": 7,
                                    "rowSpan": 2,
                                    "colSpan": 3
                                },
                                "metadata": {
                                    "inputs": [
                                        {
                                            "name": "queryInputs",
                                            "value": {
                                                "timespan": {
                                                    "duration": "PT1H",
                                                    "start": null,
                                                    "end": null
                                                },
                                                "id": "[resourceId(parameters('virtualMachineResourceGroup'), 'Microsoft.Compute/virtualMachines', parameters('virtualMachineName'))]",
                                                "chartType": 0,
                                                "metrics": [
                                                    {
                                                        "name": "Disk Read Operations/Sec",
                                                        "resourceId": "[resourceId(parameters('virtualMachineResourceGroup'), 'Microsoft.Compute/virtualMachines', parameters('virtualMachineName'))]"
                                                    },
                                                    {
                                                        "name": "Disk Write Operations/Sec",
                                                        "resourceId": "[resourceId(parameters('virtualMachineResourceGroup'), 'Microsoft.Compute/virtualMachines', parameters('virtualMachineName'))]"
                                                    }
                                                ]
                                            }
                                        }
                                    ],
                                    "type": "Extension/Microsoft_Azure_Monitoring/PartType/MetricsChartPart",
                                    "settings": {}
                                }
                            },
                            "5": {
                                "position": {
                                    "x": 3,
                                    "y": 7,
                                    "rowSpan": 2,
                                    "colSpan": 3
                                },
                                "metadata": {
                                    "inputs": [
                                        {
                                            "name": "queryInputs",
                                            "value": {
                                                "timespan": {
                                                    "duration": "PT1H",
                                                    "start": null,
                                                    "end": null
                                                },
                                                "id": "[resourceId(parameters('virtualMachineResourceGroup'), 'Microsoft.Compute/virtualMachines', parameters('virtualMachineName'))]",
                                                "chartType": 0,
                                                "metrics": [
                                                    {
                                                        "name": "Disk Read Bytes",
                                                        "resourceId": "[resourceId(parameters('virtualMachineResourceGroup'), 'Microsoft.Compute/virtualMachines', parameters('virtualMachineName'))]"
                                                    },
                                                    {
                                                        "name": "Disk Write Bytes",
                                                        "resourceId": "[resourceId(parameters('virtualMachineResourceGroup'), 'Microsoft.Compute/virtualMachines', parameters('virtualMachineName'))]"
                                                    }
                                                ]
                                            }
                                        }
                                    ],
                                    "type": "Extension/Microsoft_Azure_Monitoring/PartType/MetricsChartPart",
                                    "settings": {}
                                }
                            },
                            "6": {
                                "position": {
                                    "x": 6,
                                    "y": 7,
                                    "rowSpan": 2,
                                    "colSpan": 3
                                },
                                "metadata": {
                                    "inputs": [
                                        {
                                            "name": "queryInputs",
                                            "value": {
                                                "timespan": {
                                                    "duration": "PT1H",
                                                    "start": null,
                                                    "end": null
                                                },
                                                "id": "[resourceId(parameters('virtualMachineResourceGroup'), 'Microsoft.Compute/virtualMachines', parameters('virtualMachineName'))]",
                                                "chartType": 0,
                                                "metrics": [
                                                    {
                                                        "name": "Network In",
                                                        "resourceId": "[resourceId(parameters('virtualMachineResourceGroup'), 'Microsoft.Compute/virtualMachines', parameters('virtualMachineName'))]"
                                                    },
                                                    {
                                                        "name": "Network Out",
                                                        "resourceId": "[resourceId(parameters('virtualMachineResourceGroup'), 'Microsoft.Compute/virtualMachines', parameters('virtualMachineName'))]"
                                                    }
                                                ]
                                            }
                                        }
                                    ],
                                    "type": "Extension/Microsoft_Azure_Monitoring/PartType/MetricsChartPart",
                                    "settings": {}
                                }
                            },
                            "7": {
                                "position": {
                                    "x": 9,
                                    "y": 7,
                                    "rowSpan": 2,
                                    "colSpan": 2
                                },
                                "metadata": {
                                    "inputs": [
                                        {
                                            "name": "id",
                                            "value": "[resourceId(parameters('virtualMachineResourceGroup'), 'Microsoft.Compute/virtualMachines', parameters('virtualMachineName'))]"
                                        }
                                    ],
                                    "type": "Extension/Microsoft_Azure_Compute/PartType/VirtualMachinePart",
                                    "asset": {
                                        "idInputName": "id",
                                        "type": "VirtualMachine"
                                    },
                                    "defaultMenuItemId": "overview"
                                }
                            }
                        }
                    }
                }
            },
            "metadata": { },
            "apiVersion": "2015-08-01-preview",
            "type": "Microsoft.Portal/dashboards",
            "name": "[parameters('dashboardName')]",
            "location": "westus",
            "tags": {
                "hidden-title": "[parameters('dashboardName')]"
            }
        }
    ]
}
```

Most, hogy látott egy példát arra, hogyan helyezhet üzembe egy irányítópultot paraméteres sablonnal, megpróbálhatja üzembe helyezni a sablont [a Azure Resource Manager REST API-k,](/rest/api/)az Azure [CLI](/cli/azure)vagy a Azure PowerShell [parancs használatával.](/powershell/azure/get-started-azureps)

## <a name="programmatically-create-a-dashboard-by-using-azure-cli"></a>Irányítópult programozott létrehozása az Azure CLI használatával

A környezet előkészítése az Azure CLI-hez.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

- Ezek a példák a következő irányítópultot használják: [portal-dashboard-template-testvm.jsa következőn:](https://raw.githubusercontent.com/Azure/azure-docs-powershell-samples/master/azure-portal/portal-dashboard-template-testvm.json). Cserélje le a szögletes zárójelben lévő tartalmat a saját értékeire.

Az [irányítópult létrehozásához](/cli/azure/portal/dashboard#az_portal_dashboard_create) futtassa az az portal dashboard create parancsot:

```azurecli
az portal dashboard create --resource-group myResourceGroup --name 'Simple VM Dashboard' \
   --input-path portal-dashboard-template-testvm.json --location centralus
```

Az irányítópultot az [az portal dashboard update](/cli/azure/portal/dashboard#az_portal_dashboard_update) paranccsal frissítheti:

```azurecli
az portal dashboard update --resource-group myResourceGroup --name 'Simple VM Dashboard' \
--input-path portal-dashboard-template-testvm.json --location centralus
```

Az irányítópult részleteinek megtekintése az [az portal dashboard show paranccsal:](/cli/azure/portal/dashboard#az_portal_dashboard_show)

```azurecli
az portal dashboard show --resource-group myResourceGroup --name 'Simple VM Dashboard'
```

Az aktuális előfizetéshez az az portal dashboard list használatával használhatja az [összes irányítópultot:](/cli/azure/portal/dashboard#az_portal_dashboard_list)

```azurecli
az portal dashboard list
```

Egy erőforráscsoport összes irányítópultját is láthatja:

```azurecli
az portal dashboard list --resource-group myResourceGroup
```

## <a name="next-steps"></a>Következő lépések

Az asztali környezetekkel kapcsolatos további információkért lásd: [Manage Azure Portal settings and preferences (Beállítások és](set-preferences.md)beállítások kezelése).

Az irányítópultok Azure CLI-támogatásával kapcsolatos további információkért lásd: [az portal dashboard](/cli/azure/portal/dashboard).
