---
title: Paraméterek létrehozásához Azure Monitor munkafüzetek
description: Ebből a cikkből megtudhatja, hogyan teszi lehetővé a munkafüzet szerzője a felhasználóktól érkező adatok begyűjtését, és a munkafüzet más részein való hivatkozását.
services: azure-monitor
manager: carmonm
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 10/23/2019
ms.openlocfilehash: 95e810373ac5a591c530004e8d1175c3300656f5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "101700558"
---
# <a name="workbook-parameters"></a>Munkafüzet paramétereinek

A paraméterek lehetővé teszik a munkafüzet szerzője számára, hogy adatokat gyűjtsenek a felhasználóktól, és hivatkozhatnak a munkafüzet más részeire – általában az eredményhalmaz hatókörére vagy a megfelelő vizualizáció beállítására. Ez egy kulcsfontosságú képesség, amely lehetővé teszi, hogy a szerzők interaktív jelentéseket és tapasztalatokat hozzanak létre. 

A munkafüzetek segítségével szabályozhatja, hogyan jelennek meg a paraméterek vezérlői a felhasználók számára – szövegmező és legördülő lista, egyetlen-és többszörös kijelölés, szöveg, JSON, KQL vagy Azure Resource Graph stb.  

A támogatott paraméterek típusai a következők:
* [Idő](workbooks-time.md) – lehetővé teszi, hogy a felhasználó előre feltöltött időtartományokból válasszon, vagy válasszon ki egy egyéni tartományt
* [Legördülő lista](workbooks-dropdowns.md) – lehetővé teszi, hogy a felhasználó kiválasszon egy értéket vagy egy értékeket
* [Text (szöveg](workbooks-text.md) ) – lehetővé teszi, hogy a felhasználó tetszőleges szöveget adjon meg
* [Erőforrás](workbooks-resources.md) – lehetővé teszi, hogy a felhasználó válasszon ki egy vagy több Azure-erőforrást
* [Előfizetés](workbooks-resources.md) – lehetővé teszi, hogy a felhasználó válasszon ki egy vagy több Azure-előfizetési erőforrást
* Erőforrástípus – lehetővé teszi, hogy a felhasználó válasszon ki egy vagy több Azure-erőforrástípus-értéket
* Hely – lehetővé teszi, hogy a felhasználó válasszon ki egy vagy több Azure Location értéket

Ezek a paraméterérték a munkafüzetek más részeire is hivatkozhatók kötések vagy értékek bővítései útján.

## <a name="creating-a-parameter"></a>Paraméter létrehozása
1. Kezdés egy üres munkafüzettel szerkesztési módban.
2. Válassza a _Paraméterek hozzáadása_ lehetőséget a munkafüzetben található hivatkozások közül.
3. Kattintson a kék _paraméter hozzáadása_ gombra.
4. A felugró új paraméter panelen írja be a következőket:
    1. Paraméter neve: `TimeRange` *(vegye figyelembe, hogy a paraméterek __nevei__ **nem** tartalmazhatnak szóközöket vagy speciális karaktereket).*
    2. Megjelenítendő név: `Time Range` *(a __megjelenítendő nevek__ azonban tartalmazhatnak szóközöket, speciális karaktereket, Emoji stb.).*  
    2. Paraméter típusa: `Time range picker`
    3. Szükséges `checked`
    4. Rendelkezésre álló időtartományok: elmúlt óra, utolsó 12 óra, utolsó 24 óra, utolsó 48 óra, elmúlt 3 nap, utolsó 7 nap, és egyéni időtartomány-kijelölés engedélyezése
5. A paraméter létrehozásához válassza az eszköztár mentés elemét.

   ![A Time Range paraméter létrehozását bemutató kép](./media/workbooks-parameters/time-settings.png)

A munkafüzet a "tabletták" stílusban fog kinézni, ahogy olvasható módban.

   ![Az időtartomány-paramétert olvasási módban ábrázoló kép](./media/workbooks-parameters/parameters-time.png)

## <a name="referencing-a-parameter"></a>Egy paraméterre hivatkozik
### <a name="via-bindings"></a>Kötések útján
1. Vegyen fel egy lekérdezés vezérlőelemet a munkafüzetbe, és válasszon ki egy Application Insights erőforrást.
2. Nyissa meg az _időtartomány_ legördülő listát, és válassza a `Time Range` lenti paraméterek szakaszban található lehetőséget.
3. Ezzel a beállítással az időtartomány paraméter a diagram időtartományához köthető. A minta lekérdezés időbeli hatóköre mostantól az elmúlt 24 órában érhető el.
4. Lekérdezés futtatása az eredmények megtekintéséhez

    ![A kötéseken keresztül hivatkozott időtartomány-paramétert ábrázoló kép](./media/workbooks-parameters/time-binding.png)

### <a name="in-kql"></a>A KQL
1. Vegyen fel egy lekérdezés vezérlőelemet a munkafüzetbe, és válasszon ki egy Application Insights erőforrást.
2. A KQL adja meg az időtartomány szűrőt a (z) paraméter használatával: `| where timestamp {TimeRange}`
3. Ez kibővíti a lekérdezés értékelésének idejét a értékre `| where timestamp > ago(1d)` , amely a paraméter időtartományának értéke.
4. Lekérdezés futtatása az eredmények megtekintéséhez

    ![A KQL-ben hivatkozott időtartományt ábrázoló kép](./media/workbooks-parameters/time-in-code.png)

### <a name="in-text"></a>Szövegben 
1. Adjon hozzá egy szövegbeviteli vezérlőt a munkafüzethez.
2. A Markdown írja be a `The chosen time range is {TimeRange:label}`
3. Válassza a _Szerkesztés kész_ lehetőséget
4. A szöveg vezérlőelem szövege a következő lesz: _a kiválasztott időtartomány az elmúlt 24 óra_

## <a name="parameter-options"></a>Paraméterek beállításai
A _in Text (szöveg_ ) szakaszban az `label` érték helyett a paramétert használta. A paraméterek az adott típustól függően különböző lehetőségeket tesznek elérhetővé, például az időtartomány-választókat az érték, a címke, a lekérdezés, a kezdés, a Befejezés és a gabona alapján.

A paraméter `Previews` _szerkesztése_ ablaktábla szakaszának használatával megtekintheti a paraméter bővítési beállításait:

![Az időtartomány paraméterének beállításait ábrázoló kép](./media/workbooks-parameters/time-previews.png)

## <a name="next-steps"></a>Következő lépések

* [Ismerkedjen](./workbooks-overview.md#visualizations) meg a munkafüzetek számos gazdag vizualizációs lehetőségével.
* A munkafüzet erőforrásaihoz való hozzáférés [szabályozása](./workbooks-access-control.md) és megosztása.