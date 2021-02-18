---
title: Azure Monitor munkafüzet legördülő paraméterei
description: A legördülő paramétereket tartalmazó, előre összeépített és egyéni paraméterekkel rendelkező munkafüzetek összetett jelentéskészítésének egyszerűsítése
services: azure-monitor
manager: carmonm
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 10/23/2019
ms.openlocfilehash: c3b44c7b8761010deeecba1f8ed80727fe635f2b
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/17/2021
ms.locfileid: "100620685"
---
# <a name="workbook-drop-down-parameters"></a>Munkafüzet legördülő paraméterei

A legördülő lista lehetővé teszi, hogy a felhasználó egy ismert készletből egy vagy több bemeneti értéket gyűjtsön (például kiválaszthatja az alkalmazás kérelmeit). A legördülő lista lehetővé teszi, hogy felhasználóbarát módon gyűjtsön tetszőleges bemeneteket a felhasználóktól. A legördülő lista különösen hasznos az interaktív jelentések szűrésének engedélyezéséhez. 

A legördülő lista megadásának legegyszerűbb módja, ha statikus listát biztosít a paraméter-beállításban. Egy érdekes módszer a lista dinamikus lekérése egy KQL-lekérdezésen keresztül. A paraméter beállításai lehetővé teszik annak megadását is, hogy az adott vagy többszörös kijelölés legyen-e, és ha a többszörös kijelölés van megadva, az eredményhalmaz formátumát (elválasztót, idézőjelet stb.) is meg kell adni.

## <a name="creating-a-static-drop-down-parameter"></a>Statikus legördülő paraméter létrehozása

1. Kezdés egy üres munkafüzettel szerkesztési módban.
2. Válassza a _Paraméterek hozzáadása_ lehetőséget a munkafüzetben található hivatkozások közül.
3. Kattintson a kék _paraméter hozzáadása_ gombra.
4. A felugró új paraméter panelen írja be a következőket:
    1. Paraméter neve: `Environment`
    2. Paraméter típusa: `Drop down`
    3. Szükséges `checked`
    4. Engedélyezés `multiple selection` : `unchecked`
    5. Adatok lekérése: `JSON`
5. A JSON bemeneti szövegmezőbe írja be a következő JSON-kódrészletet:
    ```json
    [
        { "value":"dev", "label":"Development" },
        { "value":"ppe", "label":"Pre-production" },
        { "value":"prod", "label":"Production", "selected":true }
    ]
    ```
6. Nyomja meg a kék `Update` gombot.
7. A paraméter létrehozásához válassza az eszköztár mentés elemét.
8. A környezeti paraméter a három érték legördülő lista lesz.

    ![A statikus elfojtani létrehozását bemutató kép](./media/workbooks-dropdowns/dropdown-create.png)

## <a name="creating-a-static-dropdown-with-groups-of-items"></a>Statikus legördülő lista létrehozása elemek csoportjaival

Ha a lekérdezés eredménye/JSON tartalmaz egy "csoport" mezőt, a legördülő lista az értékek csoportjait jeleníti meg. Kövesse a fenti mintát, de használja inkább a következő JSON-t:

```json
[
    { "value":"dev", "label":"Development", "group":"Development" },
    { "value":"dev-cloud", "label":"Development (Cloud)", "group":"Development" },
    { "value":"ppe", "label":"Pre-production", "group":"Test" },
    { "value":"ppe-test", "label":"Pre-production (Test)", "group":"Test" },
    { "value":"prod1", "label":"Prod 1", "selected":true, "group":"Production" },
    { "value":"prod2", "label":"Prod 2", "group":"Production" }
]
```

![Egy csoportosított legördülő listát ábrázoló kép](./media/workbooks-dropdowns/grouped-dropDown.png)


## <a name="creating-a-dynamic-drop-down-parameter"></a>Dinamikus legördülő paraméter létrehozása
1. Kezdés egy üres munkafüzettel szerkesztési módban.
2. Válassza a _Paraméterek hozzáadása_ lehetőséget a munkafüzetben található hivatkozások közül.
3. Kattintson a kék _paraméter hozzáadása_ gombra.
4. A felugró új paraméter panelen írja be a következőket:
    1. Paraméter neve: `RequestName`
    2. Paraméter típusa: `Drop down`
    3. Szükséges `checked`
    4. Engedélyezés `multiple selection` : `unchecked`
    5. Adatok lekérése: `Query`
5. A JSON bemeneti szövegmezőbe írja be a következő JSON-kódrészletet:

    ```kusto
        requests
        | summarize by name
        | order by name asc
    ```
1. Nyomja meg a kék `Run Query` gombot.
2. A paraméter létrehozásához válassza az eszköztár mentés elemét.
3. A RequestName paraméter az alkalmazásban lévő összes kérelem nevének legördülő menüje lesz.

    ![A dinamikus legördülő lista létrehozását bemutató kép](./media/workbooks-dropdowns/dropdown-dynamic.png)

## <a name="referencing-drop-down-parameter"></a>Viszonyítási legördülő paraméter

### <a name="in-kql"></a>A KQL
1. Vegyen fel egy lekérdezés vezérlőelemet a munkafüzetbe, és válasszon ki egy Application Insights erőforrást.
2. A KQL-szerkesztőben adja meg a következő kódrészletet:

    ```kusto
        requests
        | where name == '{RequestName}'
        | summarize Requests = count() by bin(timestamp, 1h)

    ```
3. A lekérdezés kiértékelésének ideje a következőre bővül:

    ```kusto
        requests
        | where name == 'GET Home/Index'
        | summarize Requests = count() by bin(timestamp, 1h)
    ```

4. A lekérdezés futtatásával tekintheti meg az eredményeket. Igény szerint diagramként is megjeleníthető.

    ![A KQL-ben hivatkozott legördülő lista képe](./media/workbooks-dropdowns/dropdown-reference.png)


## <a name="parameter-value-label-selection-and-group"></a>Paraméter értéke, címke, kijelölés és csoport
A fenti dinamikus legördülő paraméterben használt lekérdezés csak a legördülő listában szereplő értékek listáját adja vissza. De mi a teendő, ha más megjelenítendő nevet szeretne kijelölni? A legördülő paraméterek lehetővé teszik ezt az értéket, a feliratot, a kiválasztási és a csoport oszlopokat.

Az alábbi minta azt mutatja be, hogyan kérhető le egy olyan Application Insights függőségek listája, amelyek megjelenítendő nevei egy Emoji stílusúak, az első kiválasztva, és a műveleti nevek szerint vannak csoportosítva.

```kusto
dependencies
| summarize by operation_Name, name
| where name !contains ('.')
| order by name asc
| serialize Rank = row_number()
| project value = name, label = strcat('🌐 ', name), selected = iff(Rank == 1, true, false), group = operation_Name
```

![Az érték, a címke, a kiválasztási és a csoportosítási beállítások használatával legördülő paramétereket ábrázoló kép](./media/workbooks-dropdowns/dropdown-more-options.png)


## <a name="drop-down-parameter-options"></a>Legördülő paraméterek beállításai
| Paraméter | Magyarázat | Példa |
| ------------- |:-------------|:-------------|
| `{DependencyName}` | A kijelölt érték | Fabrikamaccount beolvasása |
| `{DependencyName:label}` | A kiválasztott címke | 🌐 fabrikamaccount beolvasása |
| `{DependencyName:value}` | A kijelölt érték | Fabrikamaccount beolvasása |

## <a name="multiple-selection"></a>Többszörös kijelölés
A példák eddig explicit módon beállítják a paramétert úgy, hogy csak egy értéket válasszon ki a legördülő menüből. A legördülő paraméterek is támogatják `multiple selection` a használatát – ezzel a beállítással egyszerűen ellenőrizheti a `Allow multiple selection` beállítást. 

A felhasználó a és a beállítások használatával is megadhatja az eredményhalmaz formátumát `delimiter` `quote with` . Az alapértelmezett érték az értékeket gyűjteményként adja vissza ebben az űrlapban: "a", "b", "c". Lehetőség van a kiválasztások számának korlátozására is.

A paraméterre hivatkozó KQL módosítani kell az eredmény formátumával. A leggyakrabban az operátoron keresztül engedélyezhető `in` .

```kusto
dependencies
| where name in ({DependencyName})
| summarize Requests = count() by bin(timestamp, 1h), name
```

Íme egy példa a többszörös kijelölés legördülő menüjére a munkahelyen:

![Egy többszörös kijelölésű legördülő paramétert ábrázoló kép](./media/workbooks-dropdowns/dropdown-multiselect.png)

## <a name="next-steps"></a>Következő lépések

* [Ismerkedjen](../platform/workbooks-overview.md#visualizations) meg a munkafüzetek számos gazdag vizualizációs lehetőségével.
* A munkafüzet erőforrásaihoz való hozzáférés [szabályozása](../platform/workbooks-access-control.md) és megosztása.