---
title: Az Azure-irányítópultok szerkezete | Microsoft Docs
description: Ez a cikk az Azure-irányítópult JSON-szerkezetét ismerteti
services: azure-portal
documentationcenter: ''
author: adamabmsft
manager: dougeby
editor: tysonn
ms.service: azure-portal
ms.devlang: NA
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 09/01/2017
ms.author: kfollis
ms.openlocfilehash: dccf1550052910269efe156b999882234669d7dc
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/08/2019
ms.locfileid: "73815710"
---
# <a name="the-structure-of-azure-dashboards"></a>Az Azure-irányítópultok szerkezete
Ez a dokumentum egy Azure-irányítópult szerkezetét mutatja be példaként a következő irányítópult használatával:

![minta-irányítópult](./media/azure-portal-dashboards-structure/sample-dashboard.png)

Mivel a közös [Azure-irányítópultok erőforrások](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview), ez az irányítópult JSON-ként is megjeleníthető.  A következő JSON a fent látható irányítópultot jelöli.

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
        "metadata": {
            "model": {
                "timeRange": {
                    "value": {
                        "relative": {
                            "duration": 24,
                            "timeUnit": 1
                        }
                    },
                    "type": "MsPortalFx.Composition.Configuration.ValueTypes.TimeRange"
                }
            }
        }
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

## <a name="common-resource-properties"></a>Általános erőforrás-tulajdonságok

Nézzük meg a JSON megfelelő részeit.  A legfelső szintű tulajdonságok, az __azonosító__, a __név__, a __típus__, a __hely__és a __címkék__ tulajdonság az összes Azure-erőforrástípus között meg van osztva. Tehát nem sok köze van az irányítópult tartalmához.

### <a name="the-id-property"></a>Az id tulajdonság

Az Azure-erőforrás azonosítója az Azure- [erőforrások elnevezési konvenciói alapján](/azure/cloud-adoption-framework/ready/azure-best-practices/naming-and-tagging). Amikor a portál létrehoz egy irányítópultot, általában egy GUID formátumú azonosítót választ, de a programozott módon történő létrehozáskor bármilyen érvényes nevet használhat. 

### <a name="the-name-property"></a>A Name tulajdonság
A név az erőforrás-azonosító azon szegmense, amely nem tartalmazza az előfizetést, az erőforrás típusát vagy az erőforráscsoport adatait. Lényegében az erőforrás-azonosító utolsó szegmense.

### <a name="the-type-property"></a>A Type tulajdonság
Minden irányítópult __Microsoft. Portal/irányítópult__típusú.

### <a name="the-location-property"></a>A Location tulajdonság
Más erőforrásokkal ellentétben az irányítópultok nem rendelkeznek futtatókörnyezet-összetevővel.  Az irányítópultok esetében a hely azt az elsődleges földrajzi helyet jelzi, amely az irányítópult JSON-ábrázolását tárolja. Az értéknek a helyek API használatával beolvasható Helykód egyikének kell lennie [az előfizetések erőforráson](https://docs.microsoft.com/rest/api/resources/subscriptions).

### <a name="the-tags-property"></a>A címkék tulajdonság
A címkék az Azure-erőforrások egyik gyakori funkciója, amely lehetővé teszi, hogy tetszőleges érték párokkal szervezze az erőforrást. Az irányítópultok esetében egy __rejtett cím__nevű speciális címke található. Ha az irányítópulton ez a tulajdonság fel van töltve, akkor a portálon az irányítópult megjelenítendő neve lesz. Az Azure-erőforrás-azonosítókat nem lehet átnevezni, de a címkéket is. Ez a címke lehetővé teszi, hogy a renamable megjelenítse az irányítópult nevét.

`"tags": { "hidden-title": "Created via API" }`

### <a name="the-properties-object"></a>A tulajdonságok objektum
A Properties objektum két tulajdonságot tartalmaz: a __lencséket__ és a __metaadatokat__. A __lencsék__ tulajdonság a csempék adatait tartalmazza (más néven részek) az irányítópulton.  A __metaadatok__ tulajdonság a lehetséges jövőbeli funkciókhoz tartozik.

### <a name="the-lenses-property"></a>A lencsék tulajdonság
Az __objektívek__ tulajdonság tartalmazza az irányítópultot. Vegye figyelembe, hogy a példában szereplő objektívek objektum egyetlen "0" nevű tulajdonságot tartalmaz. A lencsék olyan csoportosítási koncepciók, amelyek jelenleg nincsenek implementálva az irányítópultokon. Egyelőre az összes irányítópult ezt az egyetlen tulajdonságot az objektív objektumon, a "0" néven is elvégezte.

### <a name="the-lens-object"></a>Az objektív objektum
A "0" alatti objektum két tulajdonságot, __sorrendet__ és __részt__tartalmaz.  Az irányítópultok aktuális verziójában a __sorrend__ mindig 0. A __részek__ tulajdonság olyan objektumot tartalmaz, amely meghatározza az egyes részeket (más néven csempék) az irányítópulton.

A __részek__ objektum minden részhez tartalmaz egy tulajdonságot, ahol a tulajdonság neve szám. Ez a szám nem jelentős. 

### <a name="the-part-object"></a>A rész objektum
Minden egyes részben lévő objektum rendelkezik egy __pozícióval__és egy __metaadatokkal__.

### <a name="the-position-object"></a>A pozíció objektum
A __position__ tulajdonság tartalmazza az __x__, __y__, __rowSpan__és __colSpan__értékben kifejezett rész méretének és helyének adatait. Az értékek a Grid egységekben vannak kifejezve. Ezek a rácsvonalak akkor láthatók, ha az irányítópult a testreszabási módban van, ahogy az itt látható. Ha azt szeretné, hogy a csempén két rácsos egység szélessége legyen, egy rácsos egység magassága, valamint az irányítópult bal felső sarkában található hely, akkor a pozíció objektum a következőképpen néz ki:

`location: { x: 0, y: 0, rowSpan: 2, colSpan: 1 }`

![rács – egységek](./media/azure-portal-dashboards-structure/grid-units.png)

### <a name="the-metadata-object"></a>A metaadat-objektum
Az egyes részek metaadat-tulajdonsággal rendelkeznek, egy objektumhoz csak egy __típus__nevű kötelező tulajdonság tartozik. Ez a karakterlánc közli a portálon, hogy melyik csempe látható. A példában szereplő irányítópult az alábbi típusú csempéket használja:


1. `Extension/Microsoft_Azure_Monitoring/PartType/MetricsChartPart` – figyelési metrikák megjelenítésére használatos
1. `Extension[azure]/HubsExtension/PartType/MarkdownPart` – olyan szövegekkel vagy képekkel való megjelenítésre használható, amelyek alapszintű formázással rendelkeznek a listához, a hivatkozásokhoz stb.
1. `Extension[azure]/HubsExtension/PartType/VideoPart` – videók megjelenítésére szolgál a YouTube-ról, a Channel9 és bármely más, a HTML-videó címkéjén használható videóról.
1. `Extension/Microsoft_Azure_Compute/PartType/VirtualMachinePart` – egy Azure-beli virtuális gép nevének és állapotának megjelenítésére szolgál.

A különböző típusú részek saját konfigurációval rendelkeznek. A lehetséges konfigurációs tulajdonságokat a __bemenetek__, a __Beállítások__és az __eszközök__nevezzük. 

### <a name="the-inputs-object"></a>A bemeneti objektum
A bemeneti objektum általában olyan információt tartalmaz, amely egy csempe erőforrás-példányhoz kötését köti össze.  A minta irányítópultján található virtuálisgép-rész egyetlen olyan bemenetet tartalmaz, amely az Azure Resource id-t használja a kötés kifejezésére.  Ez az erőforrás-azonosító formátum konzisztens az összes Azure-erőforráson.

```json
"inputs":
[
    {
        "name": "id",
        "value": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1"
    }
]

```
A metrikák diagram része egyetlen bemenettel rendelkezik, amely az erőforráshoz való kötést és a megjelenített metrika (ek) adatait mutatja. Itt látható a bemenet a hálózat és a hálózati kimenő mérőszámok megjelenítéséhez.

```json
“inputs”:
[
    {
        "name": "queryInputs",
        "value": 
        {
            "timespan": 
            {
                "duration": "PT1H",
                "start": null,
                "end": null
           },
            "id": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1",
            "chartType": 0,
            "metrics": 
            [
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
]

```

### <a name="the-settings-object"></a>A beállítások objektum
A Settings objektum egy rész konfigurálható elemeit tartalmazza.  A minta irányítópulton a Markdown rész az egyéni Markdown-tartalmak, valamint egy konfigurálható cím és alcím tárolására szolgáló beállításokat használja.

```json
"settings": 
{
    "content": 
    {
        "settings": 
        {
            "content": "This is the team dashboard for the test VM we use on our team. Here are some useful links:\r\n\r\n1. [Getting started](https://www.contoso.com/tsgs)\r\n1. [Troubleshooting guide](https://www.contoso.com/tsgs)\r\n1. [Architecture docs](https://www.contoso.com/tsgs)",
            "title": "Test VM Dashboard",
            "subtitle": "Contoso"
        }
    }
}

```

Hasonlóképpen, a videó csempén a saját beállításai is megtalálhatók, amelyek egy mutatót tartalmaznak a videó lejátszására, az automatikus lejátszási beállításokra és az opcionális címmel kapcsolatos információkra.

```json
"settings": 
{
   "content": 
    {
        "settings": 
        {
            "title": "",
            "subtitle": "",
            "src": "https://www.youtube.com/watch?v=YcylDIiKaSU&list=PLLasX02E8BPCsnETz0XAMfpLR1LIBqpgs&index=4",
            "autoplay": false
        }
    }
}

```

### <a name="the-asset-object"></a>Az Asset objektum
Az első osztálynak felügyelhető portál-objektumokhoz (úgynevezett eszközökhöz) kötött csempék ezt a kapcsolatot az Asset objektumon keresztül fejezik ki.  A példában szereplő irányítópulton a virtuális gép csempe tartalmazza az eszköz leírását.  A __idInputName__ tulajdonság azt jelzi, hogy a portálon az azonosító bemenet tartalmazza az eszköz egyedi azonosítóját, ebben az esetben az erőforrás-azonosítót. A legtöbb Azure-erőforrástípus rendelkezik olyan eszközökkel, amelyek a portálon vannak meghatározva.

`"asset": {    "idInputName": "id",    "type": "VirtualMachine"    }`
