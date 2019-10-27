---
title: Szűrők Azure Monitor nézetekben | Microsoft Docs
description: A Azure Monitor nézetben lévő szűrők lehetővé teszik a felhasználók számára, hogy egy adott tulajdonság értékét a nézet módosítása nélkül szűrhetik.  Ez a cikk azt ismerteti, hogyan használható egy szűrő, és hogyan adhat hozzá egyet egyéni nézethez.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 06/22/2018
ms.openlocfilehash: 03950c7c87f659c5d1c032b5d3c1f74d136697c7
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/25/2019
ms.locfileid: "72931982"
---
# <a name="filters-in-azure-monitor-views"></a>Szűrők Azure Monitor nézetekben
A [Azure monitor nézetben](view-designer.md) lévő **szűrők** lehetővé teszik a felhasználók számára, hogy egy adott tulajdonság értékét a nézet módosítása nélkül szűrhetik.  Például engedélyezheti a nézet felhasználói számára, hogy csak egy adott számítógépről vagy számítógépekről származó adatokra szűrje a nézetet.  Egyetlen nézetben több szűrőt is létrehozhat, így a felhasználók több tulajdonság alapján szűrhetők.  Ez a cikk azt ismerteti, hogyan használható egy szűrő, és hogyan adhat hozzá egyet egyéni nézethez.

## <a name="using-a-filter"></a>Szűrő használata
Kattintson a nézet tetején látható dátum időtartományra a legördülő lista megnyitásához, ahol módosíthatja a nézet dátum-időtartományát.

![Példa szűrésre](media/view-designer-filters/filters-example-time.png)

A **+ra** kattintva hozzáadhat egy szűrőt a nézethez definiált egyéni szűrők használatával. Válasszon egy értéket a szűrőhöz a legördülő listából, vagy írjon be egy értéket. A **+ra** kattintva folytassa a szűrők hozzáadásával. 


![Példa szűrésre](media/view-designer-filters/filters-example-custom.png)

Ha eltávolít egy szűrő összes értékét, akkor a szűrő többé nem lesz alkalmazva.


## <a name="creating-a-filter"></a>Szűrő létrehozása

Hozzon létre egy szűrőt a **szűrők** lapról a [nézet szerkesztésekor](view-designer.md).  A szűrő globális a nézet számára, és a nézet összes részére érvényes.  

![Szűrési beállítások](media/view-designer-filters/filters-settings.png)

A következő táblázat a szűrők beállításait ismerteti.

| Beállítás | Leírás |
|:---|:---|
| Mezőnév | A szűréshez használt mező neve.  Ennek a mezőnek egyeznie kell az összesítő mezővel az **értékek lekérdezéséhez**. |
| Értékek lekérdezése | A felhasználó számára a szűrő legördülő listájának feltöltéséhez futtatandó lekérdezés.  A lekérdezésnek [összegzést](/azure/kusto/query/summarizeoperator) vagy [megkülönböztetést](/azure/kusto/query/distinctoperator) kell használnia egy adott mező egyedi értékeinek megadásához, és meg kell egyeznie a **mező nevével**.  A [Rendezés](/azure/kusto/query/sortoperator) lehetőséggel rendezheti a felhasználó számára megjelenített értékeket. |
| Címke | A szűrőt támogató lekérdezésekben használt mező neve, amely a felhasználó számára is megjelenik. |

### <a name="examples"></a>Példák

Az alábbi táblázat néhány példát tartalmaz a gyakori szűrőkre.  

| Mezőnév | Értékek lekérdezése | Címke |
|:--|:--|:--|
| Computer   | Szívverés &#124; különböző számítógépének &#124; rendezése számítógép ASC szerint | Számítógépek |
| EventLevelName | Esemény &#124; eltérő EventLevelName | Súlyosság |
| SeverityLevel | Syslog &#124; – különböző SeverityLevel | Súlyosság |
| SvcChangeType | Konfigurációváltozás &#124; – eltérő svcChangeType | ChangeType |


## <a name="modify-view-queries"></a>View lekérdezések módosítása

Ahhoz, hogy egy szűrő bármilyen hatással legyen, módosítania kell a nézet összes lekérdezését a kiválasztott értékek szűréséhez.  Ha nem módosítja a nézet egyik lekérdezését sem, akkor a felhasználó által választott értékek nincsenek hatással.

Egy lekérdezésben a szűrő értékének szintaxisa a következő: 

    where ${filter name}  

Ha például a nézet olyan lekérdezést tartalmaz, amely eseményeket ad vissza, és a _számítógépek_nevű szűrőt használja, a következő lekérdezést használhatja.

    Event | where ${Computers} | summarize count() by EventLevelName

Ha egy súlyosság nevű másik szűrőt adott hozzá, akkor a következő lekérdezéssel mindkét szűrőt használhatja.

    Event | where ${Computers} | where ${Severity} | summarize count() by EventLevelName

## <a name="next-steps"></a>Következő lépések
* További információ az egyéni nézetbe felvehető [vizualizációs részekről](view-designer-parts.md) .
