---
title: Munkafüzetek erőforrás-paramétereinek Azure Monitor
description: Megtudhatja, hogyan használhatja az erőforrás-paramétereket az erőforrások munkafüzetekbe való kiválasztásának engedélyezéséhez. Az erőforrás-paraméterekkel állíthatja be azt a hatókört, amelyből az adatok beszerezhetők.
services: azure-monitor
manager: carmonm
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 10/23/2019
ms.openlocfilehash: d4a6d04f88544b3a4f9f9c0c0b290d17f5ef148d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "101725577"
---
# <a name="workbook-resource-parameters"></a>Munkafüzet erőforrás-paraméterei

Az erőforrás-paraméterek lehetővé teszik az erőforrások kiválasztását a munkafüzetekben. Ez akkor hasznos, ha azt a hatókört állítja be, amelyből az adatok beszerezhetők. Ilyen például a felhasználók számára, hogy kiválassza a virtuális gépek készletét, amelyet később a diagramok az adatmegjelenítéskor fognak használni.

Az erőforrás-választóból származó értékek a munkafüzet kontextusában, a statikus listán vagy az Azure Resource Graph-lekérdezésekből származhatnak.

## <a name="creating-a-resource-parameter-workbook-resources"></a>Erőforrás-paraméter létrehozása (munkafüzet-erőforrások)
1. Kezdés egy üres munkafüzettel szerkesztési módban.
2. Válassza a _Paraméterek hozzáadása_ lehetőséget a munkafüzetben található hivatkozások közül.
3. Kattintson a kék _paraméter hozzáadása_ gombra.
4. A felugró új paraméter panelen írja be a következőket:
    1. Paraméter neve: `Applications`
    2. Paraméter típusa: `Resource picker`
    3. Szükséges `checked`
    4. Több kijelölés engedélyezése: `checked`
5. Adatok lekérése: `Workbook Resources`
6. Csak az erőforrástípusok belefoglalása: `Application Insights`
7. A paraméter létrehozásához válassza az eszköztár mentés elemét.

![Az erőforrás-paraméternek a munkafüzet erőforrásaival való létrehozását bemutató kép](./media/workbooks-resources/resource-create.png)

## <a name="creating-a-resource-parameter-azure-resource-graph"></a>Erőforrás-paraméter létrehozása (Azure Resource Graph)
1. Kezdés egy üres munkafüzettel szerkesztési módban.
2. Válassza a _Paraméterek hozzáadása_ lehetőséget a munkafüzetben található hivatkozások közül.
3. Kattintson a kék _paraméter hozzáadása_ gombra.
4. A felugró új paraméter panelen írja be a következőket:
    1. Paraméter neve: `Applications`
    2. Paraméter típusa: `Resource picker`
    3. Szükséges `checked`
    4. Több kijelölés engedélyezése: `checked`
5. Adatok lekérése: `Query`
    1. Lekérdezés típusa: `Azure Resource Graph`
    2. Előfizetések `Use default subscriptions`
    3. A lekérdezés vezérlőelemben adja hozzá ezt a kódrészletet.
    ```kusto
    where type == 'microsoft.insights/components'
    | project value = id, label = name, selected = false, group = resourceGroup
    ```
7. A paraméter létrehozásához válassza az eszköztár mentés elemét.

![Az erőforrás-paraméter Azure Resource Graph használatával történő létrehozását bemutató kép](./media/workbooks-resources/resource-query.png)

> [!NOTE]
> Az Azure Resource Graph még nem érhető el minden felhőben. Ha ezt a módszert választja, győződjön meg arról, hogy az támogatott a cél felhőben.

[Az Azure Resource Graph dokumentációja](../../governance/resource-graph/overview.md)

## <a name="creating-a-resource-parameter--json-list"></a>Erőforrás-paraméter létrehozása (JSON-lista)
1. Kezdés egy üres munkafüzettel szerkesztési módban.
2. Válassza a _Paraméterek hozzáadása_ lehetőséget a munkafüzetben található hivatkozások közül.
3. Kattintson a kék _paraméter hozzáadása_ gombra.
4. A felugró új paraméter panelen írja be a következőket:
    1. Paraméter neve: `Applications`
    2. Paraméter típusa: `Resource picker`
    3. Szükséges `checked`
    4. Több kijelölés engedélyezése: `checked`
5. Adatok lekérése: `JSON`
    1. A tartalom vezérlőelemben adja hozzá ezt a JSON-kódrészletet
    ```json
    [
        { "value":"/subscriptions/<sub-id>/resourceGroups/<resource-group>/providers/<resource-type>/acmeauthentication", "label": "acmeauthentication", "selected":true, "group":"Acme Backend" },
        { "value":"/subscriptions/<sub-id>/resourceGroups/<resource-group>/providers/<resource-type>/acmeweb", "label": "acmeweb", "selected":false, "group":"Acme Frontend" }
    ]
    ```
    2. Nyomja meg a kék _frissítés_ gombot.
6. Igény szerint beállíthatja `Include only resource types` a _Application Insights_
7. A paraméter létrehozásához válassza az eszköztár mentés elemét.

## <a name="referencing-a-resource-parameter"></a>Erőforrás-paraméterre hivatkozik
1. Vegyen fel egy lekérdezés vezérlőelemet a munkafüzetbe, és válasszon ki egy Application Insights erőforrást.
2. A paraméter a vezérlőhöz való kötéséhez használja a _Application Insights_ legördülő listát. Ezzel beállítja a lekérdezés hatókörét a paraméter által visszaadott erőforrásokhoz a futtatáskor.
4. A KQL vezérlőben adja hozzá ezt a kódrészletet.
    ```kusto
    requests
    | summarize Requests = count() by appName, name
    | order by Requests desc
    ```
5. A lekérdezés futtatásával tekintheti meg az eredményeket. 

![Egy lekérdezés vezérlőelemben hivatkozott erőforrás-paramétert ábrázoló kép](./media/workbooks-resources/resource-reference.png)

> Ezzel a módszerrel más vezérlőkhöz, például mérőszámokhoz is köthető erőforrásokhoz.

## <a name="resource-parameter-options"></a>Erőforrás-paraméter beállításai
| Paraméter | Magyarázat | Példa |
| ------------- |:-------------|:-------------|
| `{Applications}` | A kiválasztott erőforrás-azonosító | _/Subscriptions/<alazonosító>/resourceGroups/<Resource-Group>/Providers/<erőforrás-típus>/acmeauthentication_ |
| `{Applications:label}` | A kiválasztott erőforrás címkéje | `acmefrontend` |
| `{Applications:value}` | A kiválasztott erőforrás értéke | _"/Subscriptions/<alazonosító>/resourceGroups/<erőforrás-csoport>/Providers/<erőforrás-típus>/acmeauthentication"_ |
| `{Applications:name}` | A kiválasztott erőforrás neve | `acmefrontend` |
| `{Applications:resourceGroup}` | A kiválasztott erőforrás erőforráscsoport | `acmegroup` |
| `{Applications:resourceType}` | A kiválasztott erőforrás típusa | _Microsoft. bepillantások/összetevők_ |
| `{Applications:subscription}` | A kiválasztott erőforrás előfizetése |  |
| `{Applications:grid}` | Az erőforrás-tulajdonságokat megjelenítő rács A hibakeresés során hasznos lehet a szöveges blokkban való megjelenítéshez  |  |

## <a name="next-steps"></a>Következő lépések

* [Ismerkedjen](./workbooks-overview.md#visualizations) meg a munkafüzetek számos gazdag vizualizációs lehetőségével.
* A munkafüzet erőforrásaihoz való hozzáférés [szabályozása](./workbooks-access-control.md) és megosztása.