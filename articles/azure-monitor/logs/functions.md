---
title: A naplólekérdezések Azure Monitor függvényei
description: Ez a cikk bemutatja, hogyan hívhat meg egy lekérdezést egy másik naplólekérdezésből a függvények Azure Monitor.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 04/19/2021
ms.openlocfilehash: fecede1d582232ebc75878a687a24818031f0d80
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107752852"
---
# <a name="functions-in-azure-monitor-log-queries"></a>A naplólekérdezések Azure Monitor függvényei
A függvények olyan naplólekérdezések a Azure Monitor amelyek más naplólekérdezésekben is használhatók, mintha egy parancs lenne. A függvényekkel a fejlesztők megoldásokat nyújthatnak a különböző ügyfeleknek, és a lekérdezési logikát a saját környezetében is felhasználhatják. Ez a cikk részletesen bemutatja, hogyan használhatók a függvények, és hogyan hozhat létre saját függvényeket.

## <a name="types-of-functions"></a>Függvénytípusok
Két függvénytípus létezik a Azure Monitor:

- **Megoldási függvény:** Az előre felépített függvények a Azure Monitor. Ezek az összes Log Analytics-munkaterületen elérhetők, és nem módosíthatók.
- **Munkaterületi függvények:** Egy adott Log Analytics-munkaterületre telepített függvények, amelyek a felhasználó által módosíthatók és vezérelhetőek.

## <a name="viewing-functions"></a>Függvények megtekintése
Az aktuális munkaterület megoldás-függvényét és munkaterületi függvényét a Log Analytics-munkaterület bal oldali panelének Függvények lapján tudja megtekinteni.  A Szűrő **gombbal** szűrheti a listában szereplő függvényeket, a **Csoportosítási** alapján pedig módosíthatja a csoportosításukat. Írjon be egy sztringet a **Keresőmezőbe** egy adott függvény megkereshez. Ha a kurzort egy függvény fölé húzza, megtekintheti annak részleteit, például a leírását és paramétereit.

:::image type="content" source="media/functions/view-functions.png" alt-text="View függvény" lightbox="media/functions/view-functions.png":::

## <a name="use-a-function"></a>Függvény használata
Függvényt úgy használhat egy lekérdezésben, hogy beírja a nevét bármilyen paraméter értékeivel, ahogyan egy parancs beírásával tenné. A függvény kimenete visszaadható eredményként, vagy átküldheti egy másik parancsnak.

Adjon hozzá egy függvényt az aktuális lekérdezéshez. Ehhez kattintson duplán a nevére, vagy vigye fölé az egérmutatót, és válassza a Use in editor (Használat a **szerkesztőben) gombra.** A lekérdezések begépelésekor a munkaterületen lévő függvények is bekerülnek az IntelliSense-be. 

Ha egy lekérdezéshez paraméterekre van szükség, adja meg őket a következő szintaxissal: `function_name(param1,param2,...)` .

:::image type="content" source="media/functions/function-use.png" alt-text="Függvény használata" lightbox="media/functions/function-use.png":::

## <a name="create-a-function"></a>Függvény létrehozása
Ha a szerkesztőben az aktuális lekérdezésből létre kell hoznia egy függvényt, válassza a **Mentés,** majd a **Mentés függvényként lehetőséget.** 

:::image type="content" source="media/functions/function-save.png" alt-text="Függvény létrehozása" lightbox="media/functions/function-save.png":::

Az alábbi táblázatban található információk meg Azure Portal  a Save (Mentés) gombra kattintva hozzon létre egy függvényt a Log Analytics használatával.

| Beállítás | Leírás |
|:---|:---|
| Függvény neve  | A függvény neve. Ez nem tartalmazhat szóközt vagy speciális karaktereket. Ez a karakter nem biztos, hogy aláhúzásjel (_) karakterrel kezdődik, mivel ez a karakter a megoldásfóggjelek számára van fenntartva. |
| Örökölt kategória | Felhasználó által definiált kategória a függvények szűréséhez és csoportosításához.   |
| Mentés számítógépcsoportként | Mentse a lekérdezést [számítógépcsoportként.](computer-groups.md)  |
| Paraméterek | Adjon hozzá egy paramétert a függvény minden olyan változója számára, amelyhez értéket kell használni. A [részletekért lásd:](#function-parameters) Függvényparaméterek. |

:::image type="content" source="media/functions/function-details.png" alt-text="Függvény részletei" lightbox="media/functions/function-details.png":::

## <a name="function-parameters"></a>Függvényparaméterek 
Paramétereket adhat a függvényhez, így a hívható változókhoz értékeket adhat meg. Ez lehetővé teszi, hogy ugyanazt a függvényt különböző lekérdezésekben használja, és mindegyik különböző értékeket biztosít a paraméterekhez. A paramétereket az alábbi tulajdonságok határozzák meg.

| Beállítás | Leírás |
|:---|:---|
| Típus  | Az érték adattípusa. |
| Name  | A paraméter neve. Ezt a nevet kell használni a lekérdezésben a paraméterértékre való lecserélni.  |
| Alapértelmezett érték | A paraméterhez használt érték, ha nincs megadva érték. |

A paraméterek úgy vannak megrendelve, ahogy létrejönnek, és minden olyan paraméter, amely nem rendelkezik alapértelmezett értékkel az alapértelmezett értékkel nem rendelkezik.

## <a name="working-with-function-code"></a>Függvénykód használata
Megtekintheti egy függvény kódját, hogy betekintést nyerjen annak működésébe, vagy módosítsa egy munkaterületi függvény kódját. Válassza **a Függvénykód betöltése lehetőséget,** hogy hozzáadja a függvénykódot az aktuális lekérdezéshez a szerkesztőben. Ha egy üres lekérdezéshez vagy egy meglévő lekérdezés első sorában adja hozzá, akkor a függvény nevét hozzáadja a laphoz. Ha ez egy munkaterületi függvény, akkor ez lehetővé teszi a függvény részleteinek szerkesztését.

:::image type="content" source="media/functions/function-code.png" alt-text="Függvénykód betöltése" lightbox="media/functions/function-code.png":::

## <a name="edit-a-function"></a>Függvény szerkesztése
Szerkessze egy függvény tulajdonságait vagy kódját egy új lekérdezés létrehozásával, majd vigye a kurzort a függvény nevére, és válassza a **függvénykód betöltését.** Ha módosítani szeretné a kódot, válassza a **Mentés,** majd a **Függvény részleteinek szerkesztése lehetőséget.** Mielőtt a Mentés gombra kattint, a függvény tulajdonságainak és paramétereinek **módosításait el kell eszközlni.**

:::image type="content" source="media/functions/function-edit.png" alt-text="Függvény szerkesztése" lightbox="media/functions/function-edit.png":::
## <a name="example"></a>Példa
A következő minta függvény az Azure-tevékenységnaplóban egy adott dátum óta minden olyan eseményt visszaad, amely megfelel egy adott kategóriának. 

Kezdje a következő lekérdezéssel, szoftveres értékekkel. Ez ellenőrzi, hogy a lekérdezés a várt módon működik-e.

```Kusto
AzureActivity
| where CategoryValue == "Administrative"
| where TimeGenerated > todatetime("2021/04/05 5:40:01.032 PM")
```

:::image type="content" source="media/functions/example-query.png" alt-text="Példa függvény – kezdeti lekérdezés" lightbox="media/functions/example-query.png":::

Ezután cserélje le a szoftveres kódban szereplő értékeket  paraméternevekre, majd mentse a függvényt a Mentés, majd a **Mentés másként függvény kiválasztásával.**

```Kusto
AzureActivity
| where CategoryValue == CategoryParam
| where TimeGenerated > DateParam
```

:::image type="content" source="media/functions/example-save-function.png" alt-text="Példa függvény – függvény mentése" lightbox="media/functions/example-save-function.png":::

 Adja meg a következő értékeket a függvény tulajdonságaihoz.

| Tulajdonság | Érték |
|:---|:---|
| Függvény neve | AzureActivityByCategory |
| Örökölt kategória | Bemutató függvények |

A függvény mentése előtt határozza meg a következő paramétereket.

| Típus | Name | Alapértelmezett érték |
|:---|:---|:---|
| sztring   | CategoryParam (Kategóriaelemzés) | "Rendszergazda" |
| dátum/idő | DateParam (Dátumparam)     | |

:::image type="content" source="media/functions/example-function-properties.png" alt-text="Példa függvény – függvénytulajdonságok" lightbox="media/functions/example-function-properties.png":::

Hozzon létre egy új lekérdezést, és az egérmutatóval tekintse meg az új függvényt. Figyelje meg a paraméterek sorrendjét, mivel ebben a sorrendben kell megadni őket a függvény használatakor.

:::image type="content" source="media/functions/example-view-details.png" alt-text="Példa függvény – részletek megtekintése" lightbox="media/functions/example-view-details.png":::

Válassza **a Szerkesztőben való használat** lehetőséget az új függvény lekérdezéshez való hozzáadásához, majd a paraméterek értékeinek hozzáadásához. Vegye figyelembe, hogy nem kell értéket megadnia a CategoryParam mezőben, mert az alapértelmezett értékkel rendelkezik.

:::image type="content" source="media/functions/example-use-function.png" alt-text="Példa függvény – függvény használata" lightbox="media/functions/example-use-function.png":::



## <a name="next-steps"></a>Következő lépések
További leckék a naplólekérdezések Azure Monitor íráshoz:

- [Sztringműveletek](/azure/data-explorer/kusto/query/samples?&pivots=azuremonitor#string-operations)

