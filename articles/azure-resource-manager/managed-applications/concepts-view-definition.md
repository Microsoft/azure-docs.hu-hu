---
title: A View definition áttekintése
description: A Azure Managed Applications nézet definíciójának létrehozásának fogalmát ismerteti.
ms.topic: conceptual
ms.author: lazinnat
author: lazinnat
ms.date: 06/12/2019
ms.openlocfilehash: 7a3d2234a140d1fb2eede50e3fe2eef5575da648
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "81391696"
---
# <a name="view-definition-artifact-in-azure-managed-applications"></a>Definíciós összetevő megtekintése Azure Managed Applications

A View definition egy opcionális összetevő a Azure Managed Applicationsban. Lehetővé teszi az áttekintő lap testreszabását, és további nézetek, például mérőszámok és egyéni erőforrások hozzáadását.

Ez a cikk áttekintést nyújt a View definition összetevőről és annak képességeiről.

## <a name="view-definition-artifact"></a>Meghatározás megtekintése összetevő

A View definition összetevőnek **viewDefinition. JSON** nevűnek kell lennie, és a felügyelt alkalmazás definícióját létrehozó. zip csomagban található **createUiDefinition. JSON** és **mainTemplate. JSON** fájlnak megfelelő szintre kell helyeznie. A. zip csomag létrehozásával és a felügyelt alkalmazás definíciójának közzétételével kapcsolatos információkért lásd: [Azure felügyelt alkalmazás definíciójának közzététele](publish-service-catalog-app.md)

## <a name="view-definition-schema"></a>Definíciós séma megtekintése

A **viewDefinition. JSON** fájlnak csak egyetlen legfelső `views` szintű tulajdonsága van, amely a nézetek tömbje. A felügyelt alkalmazás felhasználói felületén minden nézet külön menüelemként jelenik meg a tartalomjegyzékben. Minden nézet rendelkezik egy `kind` tulajdonsággal, amely a nézet típusát állítja be. A következő értékek egyikére kell beállítani: [Áttekintés](#overview), [metrikák](#metrics), [CustomResources](#custom-resources), [társítások](#associations). További információ: [a viewDefinition. JSON jelenlegi JSON-sémája](https://schema.management.azure.com/schemas/viewdefinition/0.0.1-preview/ViewDefinition.json#).

Példa a JSON-ra a nézet definíciójában:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/viewdefinition/0.0.1-preview/ViewDefinition.json#",
    "contentVersion": "0.0.0.1",
    "views": [
        {
            "kind": "Overview",
            "properties": {
                "header": "Welcome to your Azure Managed Application",
                "description": "This managed application is for demo purposes only.",
                "commands": [
                    {
                        "displayName": "Test Action",
                        "path": "testAction"
                    }
                ]
            }
        },
        {
            "kind": "Metrics",
            "properties": {
                "displayName": "This is my metrics view",
                "version": "1.0.0",
                "charts": [
                    {
                        "displayName": "Sample chart",
                        "chartType": "Bar",
                        "metrics": [
                            {
                                "name": "Availability",
                                "aggregationType": "avg",
                                "resourceTagFilter": [ "tag1" ],
                                "resourceType": "Microsoft.Storage/storageAccounts",
                                "namespace": "Microsoft.Storage/storageAccounts"
                            }
                        ]
                    }
                ]
            }
        },
        {
            "kind": "CustomResources",
            "properties": {
                "displayName": "Test custom resource type",
                "version": "1.0.0",
                "resourceType": "testCustomResource",
                "createUIDefinition": { },
                "commands": [
                    {
                        "displayName": "Custom Context Action",
                        "path": "testCustomResource/testContextAction",
                        "icon": "Stop",
                        "createUIDefinition": { }
                    }
                ],
                "columns": [
                    {"key": "name", "displayName": "Name"},
                    {"key": "properties.myProperty1", "displayName": "Property 1"},
                    {"key": "properties.myProperty2", "displayName": "Property 2", "optional": true}
                ]
            }
        },
        {
            "kind": "Associations",
            "properties": {
                "displayName": "Test association resource type",
                "version": "1.0.0",
                "targetResourceType": "Microsoft.Compute/virtualMachines",
                "createUIDefinition": { }
            }
        }
    ]
}
```

## <a name="overview"></a>Áttekintés

`"kind": "Overview"`

Ha ezt a nézetet megadja a **viewDefinition. JSON**fájlban, a felülbírálja a felügyelt alkalmazás alapértelmezett áttekintés lapját.

```json
{
    "kind": "Overview",
    "properties": {
        "header": "Welcome to your Azure Managed Application",
        "description": "This managed application is for demo purposes only.",
        "commands": [
            {
                "displayName": "Test Action",
                "path": "testAction"
            }
        ]
    }
}
```

|Tulajdonság|Kötelező|Leírás|
|---------|---------|---------|
|header|Nem|Az Áttekintés lap fejléce|
|leírás|Nem|A felügyelt alkalmazás leírása.|
|parancsok|Nem|Az Áttekintés lap további eszköztár gombjainak tömbje: [parancsok](#commands).|

![Áttekintés](./media/view-definition/overview.png)

## <a name="metrics"></a>Mérőszámok

`"kind": "Metrics"`

A metrikák nézet lehetővé teszi, hogy [Azure monitor mérőszámokban](../../azure-monitor/platform/data-platform-metrics.md)lévő felügyelt alkalmazás-erőforrások adatait összegyűjtse és összesítse.

```json
{
    "kind": "Metrics",
    "properties": {
        "displayName": "This is my metrics view",
        "version": "1.0.0",
        "charts": [
            {
                "displayName": "Sample chart",
                "chartType": "Bar",
                "metrics": [
                    {
                        "name": "Availability",
                        "aggregationType": "avg",
                        "resourceTagFilter": [ "tag1" ],
                        "resourceType": "Microsoft.Storage/storageAccounts",
                        "namespace": "Microsoft.Storage/storageAccounts"
                    }
                ]
            }
        ]
    }
}
```

|Tulajdonság|Kötelező|Leírás|
|---------|---------|---------|
|displayName|Nem|A nézet megjelenített címe|
|version|Nem|A nézet megjelenítéséhez használt platform verziója.|
|diagramok|Igen|A metrikák oldal diagramok tömbje.|

### <a name="chart"></a>Diagram

|Tulajdonság|Kötelező|Leírás|
|---------|---------|---------|
|displayName|Igen|A diagram megjelenített címe|
|chartType|Nem|A diagramhoz használandó vizualizáció. Alapértelmezés szerint egy vonalas diagramot használ. Támogatott diagramok típusai: `Bar, Line, Area, Scatter`.|
|metrics|Igen|A diagramon nyomtatandó mérőszámok tömbje. Ha többet szeretne megtudni a Azure Portal által támogatott mérőszámokról, tekintse meg [a támogatott mérőszámokat Azure monitor](../../azure-monitor/platform/metrics-supported.md)|

### <a name="metric"></a>Metrika

|Tulajdonság|Kötelező|Leírás|
|---------|---------|---------|
|név|Igen|A metrika neve.|
|aggregationType|Igen|A metrikához használandó összesítési típus. Támogatott összesítési típusok:`none, sum, min, max, avg, unique, percentile, count`|
|névtér|Nem|A helyes metrikák szolgáltatójának meghatározásakor használandó további információk.|
|resourceTagFilter|Nem|Az erőforrás-címkék tömbje (a `or` szó szerint lesz elválasztva), amelynél megjelennek a metrikák. Az erőforrástípus-szűrőre vonatkozik.|
|resourceType|Igen|Az az erőforrástípus, amelynek mérőszámait meg szeretné jeleníteni.|

![Mérőszámok](./media/view-definition/metrics.png)

## <a name="custom-resources"></a>Egyéni erőforrások

`"kind": "CustomResources"`

Több ilyen típusú nézetet is meghatározhat. Az egyes nézetek a **mainTemplate. JSON**fájlban meghatározott egyéni szolgáltató **egyedi** egyéni erőforrástípust jelölik. Az egyéni szolgáltatók bevezetését az [Azure Custom Providers előzetes](../custom-providers/overview.md)verziójának áttekintése című témakörben tekintheti meg.

Ebben a nézetben elvégezheti az egyéni erőforrástípus GET, PUT, DELETE és POST műveleteit. A POST művelet lehet globális egyéni művelet vagy egyéni művelet az egyéni erőforrástípus környezetében.

```json
{
    "kind": "CustomResources",
    "properties": {
        "displayName": "Test custom resource type",
        "version": "1.0.0",
        "resourceType": "testCustomResource",
        "icon": "Polychromatic.ResourceList",
        "createUIDefinition": { },
        "commands": [
            {
                "displayName": "Custom Context Action",
                "path": "testCustomResource/testContextAction",
                "icon": "Stop",
                "createUIDefinition": { },
            }
        ],
        "columns": [
            {"key": "name", "displayName": "Name"},
            {"key": "properties.myProperty1", "displayName": "Property 1"},
            {"key": "properties.myProperty2", "displayName": "Property 2", "optional": true}
        ]
    }
}
```

|Tulajdonság|Kötelező|Leírás|
|---------|---------|---------|
|displayName|Igen|A nézet megjelenített címe A címnek **egyedinek** kell lennie a **viewDefinition. JSON**fájl minden egyes CustomResources-nézetében.|
|version|Nem|A nézet megjelenítéséhez használt platform verziója.|
|resourceType|Igen|Az egyéni erőforrástípus. **Egyedi** egyéni erőforrás-típusnak kell lennie az egyéni szolgáltatónál.|
|ikon|Nem|A nézet ikonja A [JSON-sémában](https://schema.management.azure.com/schemas/viewdefinition/0.0.1-preview/ViewDefinition.json#)definiált ikonok listája.|
|createUIDefinition|Nem|Felhasználói felületi definíciós séma létrehozása az egyéni erőforrás létrehozása parancshoz. A felhasználói felületi definíciók létrehozásával kapcsolatban lásd: Bevezetés [a CreateUiDefinition](create-uidefinition-overview.md) használatába|
|parancsok|Nem|A CustomResources nézet további eszköztár gombjainak tömbje: [parancsok](#commands).|
|oszlopok|Nem|Az egyéni erőforrás oszlopainak tömbje. Ha nincs megadva, `name` az oszlop alapértelmezés szerint megjelenik. Az oszlopnak a `"key"` és `"displayName"`a értékkel kell rendelkeznie. A kulcs mezőben adja meg a nézetben megjelenítendő tulajdonság kulcsát. Ha beágyazott, a pontot elválasztó karakterként kell `"key": "name"` használni `"key": "properties.property1"`, például vagy. A megjelenítendő név mezőben adja meg a nézetben megjelenítendő tulajdonság megjelenítendő nevét. Egy `"optional"` tulajdonságot is megadhat. Ha igaz értékre van állítva, a rendszer alapértelmezés szerint elrejti az oszlopot a nézetben.|

![CustomResources](./media/view-definition/customresources.png)

## <a name="commands"></a>Parancsok

A parancsok a lapon megjelenő további eszköztár-gombok tömbje. Minden parancs a **mainTemplate. JSON**fájlban meghatározott Azure-beli egyéni szolgáltató utáni műveletet jelöli. Az egyéni szolgáltatók bevezetését az [Azure egyéni szolgáltatók áttekintése](../custom-providers/overview.md)című témakörben tekintheti meg.

```json
{
    "commands": [
        {
            "displayName": "Start Test Action",
            "path": "testAction",
            "icon": "Start",
            "createUIDefinition": { }
        },
    ]
}
```

|Tulajdonság|Kötelező|Leírás|
|---------|---------|---------|
|displayName|Igen|A parancsgomb megjelenített neve.|
|path|Igen|Az egyéni szolgáltató műveleti neve. A műveletet a **mainTemplate. JSON**fájlban kell definiálni.|
|ikon|Nem|A parancsgomb ikonja A [JSON-sémában](https://schema.management.azure.com/schemas/viewdefinition/0.0.1-preview/ViewDefinition.json#)definiált ikonok listája.|
|createUIDefinition|Nem|Felhasználói felületi definíciós séma létrehozása a parancshoz. A felhasználói felületi definíciók létrehozásával kapcsolatban lásd: Bevezetés [a CreateUiDefinition](create-uidefinition-overview.md)használatába.|

## <a name="associations"></a>Szövetségek

`"kind": "Associations"`

Több ilyen típusú nézetet is meghatározhat. Ez a nézet lehetővé teszi a meglévő erőforrások összekapcsolását a felügyelt alkalmazáshoz az **mainTemplate. JSON**fájlban meghatározott egyéni szolgáltatón keresztül. Az egyéni szolgáltatók bevezetését az [Azure Custom Providers előzetes](../custom-providers/overview.md)verziójának áttekintése című témakörben tekintheti meg.

Ebben a nézetben a meglévő Azure-erőforrásokat is kiterjesztheti `targetResourceType`a alapján. Ha egy erőforrás van kiválasztva, a rendszer létrehoz egy bevezetési kérelmet a **nyilvános** egyéni szolgáltatóhoz, amely egy mellékhatást alkalmazhat az erőforrásra. 

```json
{
    "kind": "Associations",
    "properties": {
        "displayName": "Test association resource type",
        "version": "1.0.0",
        "targetResourceType": "Microsoft.Compute/virtualMachines",
        "createUIDefinition": { }
    }
}
```

|Tulajdonság|Kötelező|Leírás|
|---------|---------|---------|
|displayName|Igen|A nézet megjelenített címe A címnek **egyedinek** kell lennie az **viewDefinition. JSON**fájl minden egyes társítás nézetében.|
|version|Nem|A nézet megjelenítéséhez használt platform verziója.|
|targetResourceType|Igen|A cél erőforrástípus. Ez az erőforrástípus, amely megjelenik az erőforrás-előkészítés során.|
|createUIDefinition|Nem|Felhasználói felületi definíciós séma létrehozása a társítási erőforrás létrehozása parancshoz. A felhasználói felületi definíciók létrehozásával kapcsolatban lásd: Bevezetés [a CreateUiDefinition](create-uidefinition-overview.md) használatába|

## <a name="looking-for-help"></a>Segítség keresése

Ha kérdése van a Azure Managed Applicationsával kapcsolatban, próbálja meg megkérdezni a [stack overflow](https://stackoverflow.com/questions/tagged/azure-managedapps). Előfordulhat, hogy egy hasonló kérdést már megtettek és megválaszoltak, ezért először A feladás előtt érdemes megnézni. A címke `azure-managedapps` hozzáadásával gyors választ kaphat!

## <a name="next-steps"></a>További lépések

- A felügyelt alkalmazásokra vonatkozó részleteket az [Azure felügyelt alkalmazásokat áttekintő](overview.md) cikk ismerteti.
- Az egyéni szolgáltatók bevezetését az [Azure egyéni szolgáltatók áttekintése](../custom-providers/overview.md)című témakörben tekintheti meg.
- Azure-beli felügyelt alkalmazás Azure-beli egyéni szolgáltatókkal való létrehozásával kapcsolatban lásd [: oktatóanyag: felügyelt alkalmazás létrehozása egyéni szolgáltatói műveletekkel és erőforrás-típusokkal](tutorial-create-managed-app-with-custom-provider.md)
