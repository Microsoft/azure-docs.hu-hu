---
title: Azure-irányítópultok programozott létrehozása
description: Az Azure-irányítópultok programozott létrehozásához használja a Azure Portalban található irányítópultot sablonként. JSON-hivatkozást tartalmaz.
ms.topic: how-to
ms.date: 12/4/2020
ms.openlocfilehash: bd56dc1c729c5aa7a77e79aa3af3366166fdcfea
ms.sourcegitcommit: 97c48e630ec22edc12a0f8e4e592d1676323d7b0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/18/2021
ms.locfileid: "101095172"
---
# <a name="programmatically-create-azure-dashboards"></a>Azure-irányítópultok programozott létrehozása

Ez a cikk végigvezeti az Azure-irányítópultok programozott létrehozásának és közzétételének folyamatán. Az alábbi irányítópult az egész dokumentumra hivatkozik.

![minta-irányítópult](./media/azure-portal-dashboards-create-programmatically/sample-dashboard.png)

## <a name="overview"></a>Áttekintés

A [Azure Portal](https://portal.azure.com) megosztott irányítópultok olyan [erőforrások](../azure-resource-manager/management/overview.md) , mint a Virtual Machines és a Storage-fiókok. Az erőforrásokat programozott módon kezelheti a [Azure Resource Manager REST API](/rest/api/)-k, az [Azure CLI](/cli/azure)és a [Azure PowerShell parancsok](/powershell/azure/get-started-azureps)használatával.

Ezen API-kra számos funkció támaszkodik az erőforrások kezelésének megkönnyítésére. Ezen API-k és eszközök mindegyike lehetőséget kínál az erőforrások létrehozására, listázására, lekérésére, módosítására és törlésére. Mivel az irányítópultok erőforrások, kiválaszthatja kedvenc API-ját vagy eszközét.

Bármelyik használt eszköz, amely programozott módon hozza létre az irányítópultot, létrehoz egy JSON-ábrázolást az irányítópult-objektumhoz. Ez az objektum az irányítópulton lévő csempék adatait tartalmazza. Ez magában foglalja a méreteket, a pozíciókat, a hozzájuk kötött erőforrásokat, valamint a felhasználói testreszabásokat.

A JSON-dokumentum felépítésének leghatékonyabb módja a Azure Portal használata. A csempéket interaktív módon is hozzáadhatja és elhelyezheti. Ezután exportálja a JSON-t, és hozzon létre egy sablont az eredményből a parancsfájlok, programok és központi telepítési eszközök későbbi használatához.

## <a name="create-a-dashboard"></a>Irányítópult létrehozása

Irányítópult létrehozásához válassza az **irányítópult** lehetőséget a [Azure Portal](https://portal.azure.com) menüben, majd válassza az **új irányítópult** lehetőséget.

![új irányítópult-parancs](./media/azure-portal-dashboards-create-programmatically/new-dashboard-command.png)

Csempék megkereséséhez és hozzáadásához használja a csempe gyűjteményét. A csempék a húzással lesznek hozzáadva. Néhány csempe támogatja az átméretezést húzással.

![a fogópont húzásával módosíthatja a méretet](./media/azure-portal-dashboards-create-programmatically/drag-handle.png)

Mások rögzített méretűek, hogy a helyi menüben legyenek kiválasztva.

![méretek a méretezési menüben a méret módosításához](./media/azure-portal-dashboards-create-programmatically/sizes-context-menu.png)

## <a name="share-the-dashboard"></a>Az irányítópult megosztása

Az irányítópult konfigurálása után a következő lépés az irányítópult közzététele a **Share** parancs használatával.

![irányítópult megosztása](./media/azure-portal-dashboards-create-programmatically/share-command.png)

A **megosztás** elem kiválasztásával kiválaszthatja, hogy melyik előfizetést és erőforráscsoportot szeretné közzétenni. Írási hozzáféréssel kell rendelkeznie a kiválasztott előfizetéshez és erőforráscsoporthoz. További információ: Azure- [szerepkörök kiosztása a Azure Portal használatával](../role-based-access-control/role-assignments-portal.md).

![megosztás és hozzáférés módosítása](./media/azure-portal-dashboards-create-programmatically/sharing-and-access.png)

## <a name="fetch-the-json-representation-of-the-dashboard"></a>Az irányítópult JSON-ábrázolásának beolvasása

A közzététel csak néhány másodpercet vesz igénybe. Ha elkészült, a következő lépés a JSON beolvasása a **letöltési** paranccsal.

![JSON-ábrázolás letöltése](./media/azure-portal-dashboards-create-programmatically/download-command.png)

## <a name="create-a-template-from-the-json"></a>Sablon létrehozása a JSON-ből

A következő lépés egy sablon létrehozása ebből a JSON-ból. A sablon programozott módon használható a megfelelő erőforrás-kezelési API-kkal, parancssori eszközökkel vagy a portálon.

Nem kell teljes mértékben megértenie az irányítópult JSON-struktúráját sablon létrehozásához. A legtöbb esetben szeretné megőrizni az egyes csempék szerkezetét és konfigurációját. Ezután parametrizálja azon Azure-erőforrások készletét, amelyeket a csempék mutatnak. Tekintse meg az exportált JSON-irányítópultot, és keresse meg az Azure-erőforrás-azonosítók összes előfordulását. A példában szereplő irányítópult több csempét tartalmaz, amelyek mindegyike egyetlen Azure-beli virtuális gépen található. Ennek az az oka, hogy az irányítópult csak ezt az egyetlen erőforrást vizsgálja. Ha a dokumentum végén található JSON-minta alapján keres, a "/Subscriptions" kifejezésnél az azonosító számos előfordulása látható.

`/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1`

Ha a későbbiekben szeretné közzétenni az irányítópultot bármelyik virtuális gépen, parametrizálja a karakterlánc minden előfordulását a JSON-n belül.

Az Azure-ban erőforrásokat létrehozó API-k esetében két megközelítés létezik:

* A kötelező API-k egyszerre egy erőforrást hoznak létre. További információ: [erőforrások](/rest/api/resources/resources).
* Sablon alapú központi telepítési rendszer, amely több, egymástól függő erőforrást hoz létre egyetlen API-hívással. További információ:  [erőforrások üzembe helyezése Resource Manager-sablonokkal és-Azure PowerShellokkal](../azure-resource-manager/templates/deploy-powershell.md).

A sablon alapú üzembe helyezés támogatja a paraméterezés és a Templates használatát. Ezt a megközelítést használjuk ebben a cikkben.

## <a name="programmatically-create-a-dashboard-from-your-template-using-a-template-deployment"></a>Irányítópult létrehozása sablon alapján a sablon központi telepítése alapján

Az Azure számos erőforrás üzembe helyezését teszi lehetővé. Létre kell hoznia egy központi telepítési sablont, amely a telepítendő erőforrások készletét és a közöttük fennálló kapcsolatokat fejezi ki.  Az egyes erőforrások JSON-formátuma ugyanaz, mintha egyenként létrehozta őket. A különbség az, hogy a sablon nyelve néhány olyan fogalmat is hozzáad, mint például a változók, paraméterek, alapszintű függvények és egyebek. Ez a kiterjesztett szintaxis csak egy sablon központi telepítésének kontextusában támogatott. Nem működik, ha a korábban tárgyalt, kötelező API-kkal van használatban. További információ: [Azure Resource Manager sablonok struktúrájának és szintaxisának megismerése](../azure-resource-manager/templates/template-syntax.md).

A paraméterezés a sablon paraméterének szintaxisa alapján kell elvégezni.  Az erőforrás-azonosító összes példányát lecseréli az itt látható módon.

Példa JSON-tulajdonságra a nehezen kódolt erőforrás-AZONOSÍTÓval:

```json
id: "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1"
```

Példa a JSON-tulajdonság paraméteres verzióra konvertálása sablon paraméterei alapján

```json
id: "[resourceId(parameters('virtualMachineResourceGroup'), 'Microsoft.Compute/virtualMachines', parameters('virtualMachineName'))]"
```

Deklarálja a szükséges sablon-metaadatokat és a JSON-sablon tetején található paramétereket:

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
A sablon konfigurálása után a következő módszerek bármelyikével telepítheti azt:

* [REST API-k](/rest/api/resources/deployments)
* [PowerShell](../azure-resource-manager/templates/deploy-powershell.md)
* [Azure CLI](/cli/azure/group/deployment#az-group-deployment-create)
* [A Azure Portal sablon üzembe helyezésének lapja](https://portal.azure.com/#create/Microsoft.Template)

Ezután két változatban láthatja a példában szereplő irányítópult JSON-t. Az első az a verzió, amelyet az erőforráshoz már kötött portálról exportáltunk. A második a sablon verziója, amely programozott módon köthető bármely virtuális géphez, és üzembe helyezhető a Azure Resource Manager használatával.

### <a name="json-representation-of-our-example-dashboard-before-templating"></a>Példa-irányítópult JSON-ábrázolása a sablon előtt

Ebből a példából megtudhatja, Mit várhat, ha követte ezt a cikket. Az utasítások a már üzembe helyezett irányítópult JSON-ábrázolását exportálták. A nehezen kódolt erőforrás-azonosítók azt mutatják, hogy ez az irányítópult egy adott Azure-beli virtuális gépen mutat.

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

### <a name="template-representation-of-our-example-dashboard"></a>A példában szereplő irányítópult sablon-ábrázolása

Az irányítópult sablonjának verziója három paramétert definiált: `virtualMachineName` , `virtualMachineResourceGroup` és `dashboardName` .  A paraméterek lehetővé teszik, hogy az irányítópultot egy másik Azure-beli virtuális gépen mutassa be minden egyes telepítésekor. Ez az irányítópult programozott módon konfigurálható és üzembe helyezhető úgy, hogy bármely Azure-beli virtuális gépre mutasson. A szolgáltatás teszteléséhez másolja a következő sablont, és illessze be a [Azure Portal sablon üzembe helyezése lapra](https://portal.azure.com/#create/Microsoft.Template).

Ez a példa egy irányítópultot helyez üzembe önmagával, de a sablon nyelve lehetővé teszi több erőforrás üzembe helyezését, valamint egy vagy több irányítópult egymás melletti elhelyezését.

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

Most, hogy megismerte az irányítópult üzembe helyezésére szolgáló paraméteres sablon használatát, megpróbálkozhat a sablon üzembe helyezésével a [Azure Resource Manager REST API](/rest/api/)-k, az [Azure CLI](/cli/azure)vagy a [Azure PowerShell parancsok](/powershell/azure/get-started-azureps)használatával.

## <a name="programmatically-create-a-dashboard-by-using-azure-cli"></a>Irányítópult programozott létrehozása az Azure CLI használatával

Készítse elő a környezetet az Azure CLI-hez.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

- Ezek a példák a következő irányítópultot használják: [portal-dashboard-template-testvm.json](https://raw.githubusercontent.com/Azure/azure-docs-powershell-samples/master/azure-portal/portal-dashboard-template-testvm.json). Cserélje le a tartalmat a szögletes zárójelben lévő értékekre.

Irányítópult létrehozásához futtassa az az [Portal irányítópult Create](/cli/azure/ext/portal/portal/dashboard#ext_portal_az_portal_dashboard_create) parancsot:

```azurecli
az portal dashboard create --resource-group myResourceGroup --name 'Simple VM Dashboard' \
   --input-path portal-dashboard-template-testvm.json --location centralus
```

Az irányítópultot az az [Portal Dashboard Update](/cli/azure/ext/portal/portal/dashboard#ext_portal_az_portal_dashboard_update) paranccsal frissítheti:

```azurecli
az portal dashboard update --resource-group myResourceGroup --name 'Simple VM Dashboard' \
--input-path portal-dashboard-template-testvm.json --location centralus
```

Tekintse meg az irányítópult részleteit az az [Portal irányítópult show](/cli/azure/ext/portal/portal/dashboard#ext_portal_az_portal_dashboard_show) parancs futtatásával:

```azurecli
az portal dashboard show --resource-group myResourceGroup --name 'Simple VM Dashboard'
```

Az aktuális előfizetés összes irányítópultjának megjelenítéséhez használja az [az Portal Dashboard List](/cli/azure/ext/portal/portal/dashboard#ext_portal_az_portal_dashboard_list):

```azurecli
az portal dashboard list
```

Az erőforráscsoport összes irányítópultját is megtekintheti:

```azurecli
az portal dashboard list --resource-group myResourceGroup
```

## <a name="next-steps"></a>Következő lépések

További információ az asztali számítógépekről: [Azure Portal beállítások és beállítások kezelése](set-preferences.md).

További információ az irányítópultok Azure CLI-támogatásáról: [az portál irányítópultja](/cli/azure/ext/portal/portal/dashboard).
